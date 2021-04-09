---
title: Co to jest Apache Hive i HiveQL — Azure HDInsight
description: Apache Hive to system magazynu danych dla Apache Hadoop. Możesz wykonywać zapytania dotyczące danych przechowywanych w usłudze Hive przy użyciu HiveQL, który przypomina język Transact-SQL. W tym dokumencie dowiesz się, jak używać usługi Hive i HiveQL w usłudze Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/28/2020
ms.openlocfilehash: 4e8c6b25055dfc38d56509e1744b8c7fcac40700
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944295"
---
# <a name="what-is-apache-hive-and-hiveql-on-azure-hdinsight"></a>Co to jest Apache Hive i HiveQL w usłudze Azure HDInsight?

[Apache Hive](https://hive.apache.org/) to system magazynu danych dla Apache Hadoop. Funkcja Hive umożliwia tworzenie podsumowań danych, wykonywanie zapytań oraz analizowanie danych. Zapytania Hive są zapisywane w HiveQL, który jest językiem zapytań podobnym do bazy danych SQL.

Program Hive umożliwia tworzenie struktury projektu na danych o dużej strukturze. Po zdefiniowaniu struktury można użyć HiveQL do wykonywania zapytań dotyczących danych bez znajomości języka Java lub MapReduce.

Usługa HDInsight udostępnia kilka typów klastrów, które są dostrajane pod kątem określonych obciążeń. Następujące typy klastrów są najczęściej używane w przypadku zapytań Hive:

|Typ klastra |Opis|
|---|---|
|Zapytanie interakcyjne|Klaster Hadoop, który zapewnia funkcjonalność [przetwarzania analitycznego o małym opóźnieniu (LLAP)](https://cwiki.apache.org/confluence/display/Hive/LLAP) w celu skrócenia czasów odpowiedzi dla interaktywnych zapytań. Aby uzyskać więcej informacji, zobacz temat [Rozpoczynanie pracy z interaktywną kwerendą w](../interactive-query/apache-interactive-query-get-started.md) dokumencie usługi HDInsight.|
|Hadoop|Klaster usługi Hadoop dostosowany do obciążeń przetwarzania wsadowego. Aby uzyskać więcej informacji, zobacz artykuł [Rozpoczynanie pracy z Apache Hadoop w usłudze HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md) .|
|platforma Spark|Apache Spark ma wbudowaną funkcję pracy z programem Hive. Aby uzyskać więcej informacji, zobacz artykuł [Rozpoczynanie pracy z Apache Spark w usłudze HDInsight](../spark/apache-spark-jupyter-spark-sql.md) .|
|HBase|HiveQL może służyć do wykonywania zapytań dotyczących danych przechowywanych w usłudze Apache HBase. Aby uzyskać więcej informacji, zobacz artykuł [Rozpoczynanie pracy z programem Apache HBase w usłudze HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md) .|

## <a name="how-to-use-hive"></a>Jak używać programu Hive

Skorzystaj z poniższej tabeli, aby poznać różne sposoby używania programu Hive z usługą HDInsight:

| **Użyj tej metody** , jeśli chcesz... | ... zapytania **interaktywne** | ... przetwarzanie **wsadowe** | ... z tego **systemu operacyjnego klienta** |
|:--- |:---:|:---:|:--- |:--- |
| [Narzędzia HDInsight Tools for Visual Studio Code](../hdinsight-for-vscode.md) |✔ |✔ | Linux, UNIX, Mac OS X lub Windows |
| [Narzędzia HDInsight Tools for Visual Studio](../hadoop/apache-hadoop-use-hive-visual-studio.md) |✔ |✔ |Windows |
| [widok programu Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md) |✔ |✔ |Dowolny (oparty na przeglądarce) |
| [Klient z usługi Beeline](../hadoop/apache-hadoop-use-hive-beeline.md) |✔ |✔ |Linux, UNIX, Mac OS X lub Windows |
| [Interfejs API REST](../hadoop/apache-hadoop-use-hive-curl.md) |&nbsp; |✔ |Linux, UNIX, Mac OS X lub Windows |
| [Windows PowerShell](../hadoop/apache-hadoop-use-hive-powershell.md) |&nbsp; |✔ |Windows |

## <a name="hiveql-language-reference"></a>Dokumentacja języka HiveQL

Dokumentacja języka HiveQL jest dostępna w [podręczniku języka](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).

## <a name="hive-and-data-structure"></a>Struktura Hive i dane

Program Hive zrozumie, jak korzystać z danych ze strukturą i z częściową strukturą. Na przykład pliki tekstowe, w których pola są rozdzielane przez określone znaki. Poniższa instrukcja HiveQL tworzy tabelę z danymi rozdzielanymi spacjami:

```hiveql
CREATE EXTERNAL TABLE log4jLogs (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE LOCATION '/example/data/';
```

Program Hive obsługuje również niestandardowe **serializatory/deserializatory (elementu SERDE)** na potrzeby złożonych lub nieregularnej struktury danych. Aby uzyskać więcej informacji, zobacz artykuł [jak korzystać z NIESTANDARDOWEGO JSON elementu SERDE with HDInsight](https://web.archive.org/web/20190217104719/https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/) .

Aby uzyskać więcej informacji na temat formatów plików obsługiwanych przez program Hive, zobacz [Podręcznik https://cwiki.apache.org/confluence/display/Hive/LanguageManual) języka (](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)

### <a name="hive-internal-tables-vs-external-tables"></a>Wewnętrzne tabele programu Hive a tabele zewnętrzne

Istnieją dwa typy tabel, które można utworzyć przy użyciu programu Hive:

* __Wewnętrzne__: dane są przechowywane w magazynie danych programu Hive. Magazyn danych znajduje się w `/hive/warehouse/` domyślnym magazynie klastra.

    Użyj tabel wewnętrznych, gdy są spełnione jeden z następujących warunków:

    * Dane są tymczasowe.
    * Program Hive ma zarządzać cyklem życia tabeli i danych.

* __Zewnętrzne__: dane są przechowywane poza magazynem danych. Dane mogą być przechowywane w dowolnym magazynie dostępnym w klastrze.

    Użyj tabel zewnętrznych, gdy są spełnione jeden z następujących warunków:

    * Dane są również używane poza programem Hive. Na przykład pliki danych są aktualizowane przez inny proces (który nie blokuje plików).
    * Dane muszą pozostać w źródłowej lokalizacji, nawet po porzucenie tabeli.
    * Potrzebujesz niestandardowej lokalizacji, takiej jak konto magazynu innego niż domyślne.
    * Program inny niż Hive zarządza formatem danych, lokalizacją i tak dalej.

Aby uzyskać więcej informacji, zobacz wpis w blogu dotyczący [wewnętrznej i zewnętrznej tabeli programu Hive](/archive/blogs/cindygross/hdinsight-hive-internal-and-external-tables-intro) .

## <a name="user-defined-functions-udf"></a>Funkcje zdefiniowane przez użytkownika (UDF)

Gałąź Hive można również rozszerzyć za poorednictwem **funkcji zdefiniowanych przez użytkownika (UDF)**. System UDF umożliwia implementację funkcji lub logiki, która nie jest łatwo modelowana w HiveQL. Przykład użycia UDF z programem Hive można znaleźć w następujących dokumentach:

* [Używanie funkcji zdefiniowanej przez użytkownika w języku Java z Apache Hive](../hadoop/apache-hadoop-hive-java-udf.md)

* [Używanie funkcji zdefiniowanej przez użytkownika w języku Python z Apache Hive](../hadoop/python-udf-hdinsight.md)

* [Używanie funkcji zdefiniowanej przez użytkownika w języku C# z Apache Hive](../hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Jak dodać niestandardową Apache Hive funkcję zdefiniowaną przez użytkownika do usługi HDInsight](/archive/blogs/bigdatasupport/how-to-add-custom-hive-udfs-to-hdinsight)

* [Przykład Apache Hive funkcji zdefiniowanej przez użytkownika w celu przekonwertowania formatów daty/godziny na sygnaturę czasową Hive](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## <a name="example-data"></a>Przykładowe dane

Gałąź Hive w usłudze HDInsight jest wstępnie załadowana z wewnętrzną tabelą o nazwie `hivesampletable` . Usługa HDInsight udostępnia również przykładowe zestawy danych, które mogą być używane z programem Hive. Te zestawy danych są przechowywane w `/example/data` `/HdiSamples` katalogach i. Te katalogi istnieją w domyślnym magazynie klastra.

## <a name="example-hive-query"></a>Przykładowe zapytanie Hive

Następujące kolumny HiveQL instrukcji projektu do `/example/data/sample.log` pliku:

```hiveql
DROP TABLE log4jLogs;
CREATE EXTERNAL TABLE log4jLogs (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE LOCATION '/example/data/';
SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs
    WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log'
    GROUP BY t4;
```

W poprzednim przykładzie instrukcje HiveQL wykonują następujące czynności:

|Instrukcja |Opis |
|---|---|
|DROP TABLE|Jeśli tabela już istnieje, usuń ją.|
|TWORZENIE TABELI ZEWNĘTRZNEJ|Tworzy nową tabelę **zewnętrzną** w usłudze Hive. Tabele zewnętrzne przechowują wyłącznie definicję tabeli w programie Hive. Dane pozostaną w oryginalnej lokalizacji i w oryginalnym formacie.|
|FORMAT WIERSZA|Informuje Hive, jak dane są sformatowane. W takim przypadku pola w każdym dzienniku są oddzielone spacją.|
|PRZECHOWYWANE JAKO LOKALIZACJA TEXTFILE|Informuje gałąź, w której dane są przechowywane ( `example/data` katalog) i które są przechowywane jako tekst. Dane mogą znajdować się w jednym pliku lub rozłożyć na wiele plików w katalogu.|
|SELECT|Wybiera liczbę wszystkich wierszy, w których kolumna **T4** zawiera wartość **[Error]**. Ta instrukcja zwraca wartość **3** , ponieważ istnieją trzy wiersze, które zawierają tę wartość.|
|INPUT__FILE__NAME jak "%. log"|Gałąź próbuje zastosować schemat do wszystkich plików w katalogu. W takim przypadku katalog zawiera pliki, które nie są zgodne ze schematem. Aby zapobiec utracie danych bezużytecznych w wyniku, ta instrukcja informuje gałąź, że będziemy zwracać tylko dane z plików kończących się na. log.|

> [!NOTE]  
> Tabele zewnętrzne powinny być używane, gdy oczekuje się, że dane podstawowe mają być aktualizowane przez zewnętrzne źródło. Na przykład proces automatycznego przekazywania danych lub operacja MapReduce.
>
> Porzucanie tabeli zewnętrznej **nie powoduje usunięcia danych** , tylko usuwa definicję tabeli.

Aby utworzyć **wewnętrzną** tabelę zamiast zewnętrznej, użyj następującego HiveQL:

```hiveql
CREATE TABLE IF NOT EXISTS errorLogs (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
STORED AS ORC;
INSERT OVERWRITE TABLE errorLogs
SELECT t1, t2, t3, t4, t5, t6, t7 
    FROM log4jLogs WHERE t4 = '[ERROR]';
```

Te instrukcje wykonują następujące czynności:

|Instrukcja |Opis |
|---|---|
|CREATE TABLE, JEŚLI NIE ISTNIEJE|Jeśli tabela nie istnieje, utwórz ją. Ponieważ **zewnętrzne** słowo kluczowe nie jest używane, ta instrukcja tworzy tabelę wewnętrzną. Tabela jest przechowywana w magazynie danych programu Hive i jest w pełni zarządzana przez program Hive.|
|PRZECHOWYWANE JAKO ORC|Dane są przechowywane w formacie zoptymalizowanego wiersza kolumnowy (ORC). ORC to wysoce zoptymalizowany i wydajny format służący do przechowywania danych programu Hive.|
|WSTAW ZASTĄPIENIE... ZAZNACZENIA|Wybiera wiersze z tabeli **log4jLogs** zawierającej wartość **[Error]**, a następnie wstawia dane do tabeli **errorLogs** .|

> [!NOTE]  
> W przeciwieństwie do tabel zewnętrznych, porzucanie tabeli wewnętrznej powoduje również usunięcie danych źródłowych.

## <a name="improve-hive-query-performance"></a>Zwiększanie wydajności zapytań programu Hive

### <a name="apache-tez"></a>Apache Tez

[Apache tez](https://tez.apache.org) to struktura, która umożliwia aplikacjom intensywnie korzystające z dużych ilości danych, takich jak Hive, wydajne działanie na dużą skalę. Tez jest domyślnie włączona.  [Apache Hive w dokumentach projektowania tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) zawiera szczegółowe informacje o wyborach implementacji i konfiguracjach dostrajania.

### <a name="low-latency-analytical-processing-llap"></a>Przetwarzanie analityczne o małym opóźnieniu (LLAP)

[LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) (czasami znany jako Live Long i Process) to nowa funkcja w programie Hive 2,0, która umożliwia buforowanie zapytań w pamięci. LLAP sprawia, że zapytania Hive znacznie szybsze, do [26x szybciej niż w przypadku programu Hive 1. x w niektórych przypadkach](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/).

Usługa HDInsight udostępnia LLAP w typie klastra zapytań interaktywnych. Aby uzyskać więcej informacji, zobacz dokument [Rozpoczynanie pracy z interakcyjnym zapytaniem](../interactive-query/apache-interactive-query-get-started.md) .

## <a name="scheduling-hive-queries"></a>Planowanie zapytań programu Hive

Istnieje kilka usług, za pomocą których można uruchamiać zapytania Hive w ramach przepływu pracy zaplanowanego lub na żądanie.

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory umożliwia korzystanie z usługi HDInsight w ramach potoku Data Factory. Aby uzyskać więcej informacji na temat korzystania z usługi Hive z potoku, zobacz dokument [Przekształcanie danych przy użyciu działania programu Hive w Azure Data Factory](../../data-factory/transform-data-using-hadoop-hive.md) .

### <a name="hive-jobs-and-sql-server-integration-services"></a>Zadania Hive i SQL Server Integration Services

Do uruchomienia zadania Hive można użyć SQL Server Integration Services (SSIS). Pakiet Azure Feature Pack dla usług SSIS zawiera następujące składniki, które działają z zadaniami Hive w usłudze HDInsight.

* [Zadanie Hive usługi Azure HDInsight](/sql/integration-services/control-flow/azure-hdinsight-hive-task)

* [Menedżer połączeń subskrypcji platformy Azure](/sql/integration-services/connection-manager/azure-subscription-connection-manager)

Aby uzyskać więcej informacji, zobacz dokumentację [pakietu Feature Pack platformy Azure](/sql/integration-services/azure-feature-pack-for-integration-services-ssis) .

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie to przepływ pracy i system koordynacji, który zarządza zadaniami usługi Hadoop. Aby uzyskać więcej informacji o korzystaniu z Oozie z usługą Hive, zobacz artykuł [Używanie platformy Apache Oozie do definiowania i uruchamiania dokumentu przepływu pracy](../hdinsight-use-oozie-linux-mac.md) .

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, co to jest gałąź i jak używać jej z usługą Hadoop w usłudze HDInsight, Skorzystaj z poniższych linków, aby poznać inne sposoby pracy z usługą Azure HDInsight.

* [Przekazywanie danych do usługi HDInsight](../hdinsight-upload-data.md)
* [Używanie funkcji języka Python zdefiniowanej przez użytkownika (UDF) z Apache Hive i Apache chlewnej w usłudze HDInsight](./python-udf-hdinsight.md)
* [Korzystanie z zadań MapReduce z usługą HDInsight](hdinsight-use-mapreduce.md)