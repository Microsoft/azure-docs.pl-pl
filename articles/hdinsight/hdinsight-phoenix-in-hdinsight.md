---
title: Apache Phoenix w usłudze HDInsight — Azure HDInsight
description: Omówienie Apache Phoenix
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: cb5230ae42703d19726fb8ea0d6c88aa70e589a8
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864146"
---
# <a name="apache-phoenix-in-azure-hdinsight"></a>Apache Phoenix w usłudze Azure HDInsight

[Apache Phoenix](https://phoenix.apache.org/) to "open source", wysoce równoległa warstwa relacyjnej bazy danych oparta na platformie [Apache HBase](hbase/apache-hbase-overview.md). Phoenix umożliwia korzystanie z zapytań przypominających SQL w HBase. Phoenix korzysta ze sterowników JDBC, aby umożliwić użytkownikom tworzenie, usuwanie, modyfikowanie tabel SQL, indeksów, widoków i sekwencji oraz wierszy upsert indywidualnie i zbiorczo. Phoenix używa kompilacji natywnej noSQL zamiast używania MapReduce do kompilowania zapytań, co umożliwia tworzenie aplikacji o małym opóźnieniu na HBase. Phoenix dodaje współprocesory obsługujące uruchamianie kodu dostarczonego przez klienta w przestrzeni adresowej serwera, wykonując z danymi. Takie podejście minimalizuje transfer danych klienta/serwera.

Apache Phoenix otwiera zapytania o dane big data dla innych niż deweloperzy, którzy mogą używać składni podobnej do języka SQL, a nie programowania. Phoenix jest wysoce zoptymalizowany pod kątem HBase, w przeciwieństwie do innych narzędzi, takich jak [Apache Hive](hadoop/hdinsight-use-hive.md) i Apache Spark SQL. Korzyści dla deweloperów polegają na pisaniu wysoce wydajnych zapytań z znacznie mniejszym kodem.

Podczas przesyłania zapytania SQL, Phoenix kompiluje zapytanie w celu HBase natywnych wywołań i uruchamia skanowania (lub planu) równolegle w celu optymalizacji. Ta warstwa abstrakcji zwalnia dewelopera z pisania zadań MapReduce, aby skoncentrować się na logice biznesowej i przepływie pracy aplikacji otaczającej magazyn danych Big Data.

## <a name="query-performance-optimization-and-other-features"></a>Optymalizacja wydajności zapytań i inne funkcje

Apache Phoenix dodaje kilka ulepszeń wydajności i funkcji HBase zapytań.

### <a name="secondary-indexes"></a>Indeksy pomocnicze

HBase ma jeden indeks, który jest lexicographically posortowany w podstawowym kluczu wiersza. Dostęp do tych rekordów można uzyskać tylko za pomocą klucza wiersza. Uzyskiwanie dostępu do rekordów za pomocą dowolnej kolumny poza kluczem wiersza wymaga skanowania wszystkich danych przy zastosowaniu wymaganego filtru. W indeksie pomocniczym kolumny lub wyrażenia, które są indeksowane, tworzą alternatywny klucz wiersza, co umożliwia wyszukiwanie i przeszukiwanie zakresu w tym indeksie.

Utwórz indeks pomocniczy za pomocą `CREATE INDEX` polecenia:

```sql
CREATE INDEX ix_purchasetype on SALTEDWEBLOGS (purchasetype, transactiondate) INCLUDE (bookname, quantity);
```

Takie podejście może przynieść znaczący wzrost wydajności nad wykonywaniem zapytań z pojedynczym indeksem. Ten typ pomocniczego indeksu jest **indeksem pokrywającym** zawierającym wszystkie kolumny zawarte w zapytaniu. W związku z tym wyszukiwanie w tabeli nie jest wymagane, a indeks spełnia całe zapytanie.

### <a name="views"></a>Widoki

Widoki w Phoenix umożliwiają przezwyciężenie ograniczenia HBase, w którym wydajność zaczyna obniżać się podczas tworzenia więcej niż około 100 tabel fizycznych. Widoki Phoenix umożliwiają udostępnienie jednej źródłowej tabeli HBase w wielu *tabelach wirtualnych* .

Tworzenie widoku Phoenix jest podobne do standardowej składni widoku języka SQL. Jedną z różnic polega na tym, że można zdefiniować kolumny dla widoku, oprócz kolumn dziedziczonych ze swojej tabeli podstawowej. Możesz również dodać nowe `KeyValue` kolumny.

Na przykład Oto tabela fizyczna o nazwie `product_metrics` z następującą definicją:

```sql
CREATE  TABLE product_metrics (
    metric_type CHAR(1),
    created_by VARCHAR,
    created_date DATE,
    metric_id INTEGER
    CONSTRAINT pk PRIMARY KEY (metric_type, created_by, created_date, metric_id));
```

Zdefiniuj widok na tej tabeli z dodatkowymi kolumnami:

```sql
CREATE VIEW mobile_product_metrics (carrier VARCHAR, dropped_calls BIGINT) AS
SELECT * FROM product_metrics
WHERE metric_type = 'm';
```

Aby później dodać więcej kolumn, użyj `ALTER VIEW` instrukcji.

### <a name="skip-scan"></a>Pomiń skanowanie

Funkcja pomijania skanowania używa co najmniej jednej kolumny indeksu złożonego, aby znaleźć różne wartości. W przeciwieństwie do skanowania zakresu, pomijanie skanowania implementuje skanowanie wewnątrz wierszy, co [zwiększa wydajność](https://phoenix.apache.org/performance.html#Skip-Scan). Podczas skanowania pierwsza dopasowana wartość jest pomijana wraz z indeksem do momentu znalezienia następnej wartości.

Pomijanie skanowania używa `SEEK_NEXT_USING_HINT` wyliczenia filtru HBase. Przy użyciu `SEEK_NEXT_USING_HINT` , pomijanie skanowania śledzi zbiór kluczy lub zakresów kluczy, które są wyszukiwane w każdej kolumnie. Funkcja pomijania skanowania Pobiera klucz, który został przesłany do niego podczas obliczania filtru, i określa, czy jest to jedna z kombinacji. W przeciwnym razie pomijanie skanowania szacuje następny najwyższy klucz, aby przejść do.

### <a name="transactions"></a>Transakcje

Mimo że usługa HBase zapewnia transakcje na poziomie wierszy, usługa Phoenix integruje się z [Tephra](https://tephra.io/) w celu dodania obsługi transakcji między wierszami i międzytabelową z użyciem pełnej semantyki [kwasowej](https://en.wikipedia.org/wiki/ACID) .

Podobnie jak w przypadku tradycyjnych transakcji SQL, transakcje udostępniane za pomocą Menedżera transakcji w Phoenix umożliwiają zagwarantowanie, że niepodzielna jednostka danych została pomyślnie upserted, wycofywanie transakcji, jeśli operacja upsert nie powiedzie się w żadnej tabeli z obsługą transakcji.

Aby włączyć transakcje w Phoenix, zapoznaj się z [dokumentacją Apache Phoenix transakcji](https://phoenix.apache.org/transactions.html).

Aby utworzyć nową tabelę z włączonymi transakcjami, ustaw `TRANSACTIONAL` Właściwość na `true` w `CREATE` instrukcji:

```sql
CREATE TABLE my_table (k BIGINT PRIMARY KEY, v VARCHAR) TRANSACTIONAL=true;
```

Aby zmienić istniejącą tabelę jako transakcyjną, Użyj tej samej właściwości w `ALTER` instrukcji:

```sql
ALTER TABLE my_other_table SET TRANSACTIONAL=true;
```

> [!NOTE]  
> Nie można przełączyć tabeli transakcyjnej z powrotem do nie transakcyjnej.

### <a name="salted-tables"></a>Tabele solone

*Serwer regionu hotspotting* może wystąpić podczas pisania rekordów z sekwencyjnymi klawiszami do HBase. Mimo że w klastrze może znajdować się wiele serwerów regionów, wszystkie operacje zapisu są wykonywane tylko na jednym z nich. To stężenie tworzy problem hotspotting, w którym zamiast obciążeń zapisu, które są dystrybuowane na wszystkich dostępnych serwerach regionów, tylko jeden obsługuje obciążenie. Ponieważ każdy region ma wstępnie zdefiniowany maksymalny rozmiar, gdy region osiągnie ten limit rozmiaru, jest podzielony na dwa małe regiony. W takim przypadku jeden z tych nowych regionów przyjmuje wszystkie nowe rekordy, stając się nowym punktem aktywnym.

Aby wyeliminować ten problem i uzyskać lepszą wydajność, należy wstępnie podzielić tabele, aby wszystkie serwery regionów były równie używane. Phoenix udostępnia *tabele solone*, które w sposób przezroczysty umożliwiają dodawanie bajtów soli do klucza wiersza dla konkretnej tabeli. Tabela jest wstępnie podzielona na granice bajtów soli, aby zapewnić równoważną dystrybucję obciążenia między serwerami regionów podczas początkowej fazy tabeli. Takie podejście dystrybuuje obciążenie pracą zapisu na wszystkich dostępnych serwerach regionów, poprawiając wydajność zapisu i odczytu. Aby przeprowadzić solenie tabeli, określ `SALT_BUCKETS` Właściwość tabeli podczas tworzenia tabeli:

```sql
CREATE TABLE Saltedweblogs (
    transactionid varchar(500) Primary Key,
    transactiondate Date NULL,
    customerid varchar(50) NULL,
    bookid varchar(50) NULL,
    purchasetype varchar(50) NULL,
    orderid varchar(50) NULL,
    bookname varchar(50) NULL,
    categoryname varchar(50) NULL,
    invoicenumber varchar(50) NULL,
    invoicestatus varchar(50) NULL,
    city varchar(50) NULL,
    state varchar(50) NULL,
    paymentamount DOUBLE NULL,
    quantity INTEGER NULL,
    shippingamount DOUBLE NULL) SALT_BUCKETS=4;
```

## <a name="enable-and-tune-phoenix-with-apache-ambari"></a>Włączanie i dostrajanie Phoenix przy użyciu platformy Apache Ambari

HDInsight An klaster HBase zawiera [interfejs użytkownika Ambari](hdinsight-hadoop-manage-ambari.md) do wprowadzania zmian w konfiguracji.

1. Aby włączyć lub wyłączyć Phoenix oraz kontrolować ustawienia limitu czasu zapytania w Phoenix, zaloguj się do interfejsu użytkownika sieci Web Ambari ( `https://YOUR_CLUSTER_NAME.azurehdinsight.net` ) przy użyciu poświadczeń użytkownika usługi Hadoop.

2. Wybierz pozycję **HBase** z listy usług w menu po lewej stronie, a następnie **Wybierz kartę konfiguracje** .

    :::image type="content" source="./media/hdinsight-phoenix-in-hdinsight/ambari-hbase-config1.png" alt-text="Konfiguracje HBase Apache Ambari":::

3. Aby włączyć lub wyłączyć Phoenix i ustawić limit czasu zapytania, Znajdź sekcję konfiguracyjną **języka SQL w Phoenix** .

    :::image type="content" source="./media/hdinsight-phoenix-in-hdinsight/apache-ambari-phoenix.png" alt-text="Sekcja konfiguracji SQL w Ambari Phoenix":::

## <a name="see-also"></a>Zobacz też

* [Używanie Apache Phoenix z klastrami HBase opartymi na systemie Linux w usłudze HDInsight](hbase/apache-hbase-query-with-phoenix.md)

* [Korzystanie z programu Apache Zeppelin do uruchamiania zapytań Apache Phoenix w usłudze Apache HBase w usłudze Azure HDInsight](./hbase/apache-hbase-phoenix-zeppelin.md)
