---
title: Uruchamianie kwerend Apache Base w usłudze Azure HDInsight przy użyciu Apache Phoenix
description: Dowiedz się, jak używać oprogramowania Apache Zeppelin do uruchamiania zapytań platformy Apache Base z Phoenix.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 10/14/2019
ms.openlocfilehash: 50a72d0400b23162e05b17b37bdad48783261072
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944776"
---
# <a name="use-apache-zeppelin-to-run-apache-phoenix-queries-over-apache-hbase-in-azure-hdinsight"></a>Korzystanie z programu Apache Zeppelin do uruchamiania zapytań Apache Phoenix w usłudze Apache HBase w usłudze Azure HDInsight

Apache Phoenix to "open source", wysoce równoległa warstwa relacyjnej bazy danych oparta na HBase. Phoenix umożliwia korzystanie z zapytań SQL, takich jak zapytania w HBase. Phoenix korzysta ze sterowników JDBC, aby umożliwić tworzenie, usuwanie, modyfikowanie tabel SQL, indeksów, widoków i sekwencji.  Możesz również użyć Phoenix, aby aktualizować wiersze osobno i zbiorczo. Phoenix używa kompilacji natywnej NOSQL zamiast używania MapReduce do kompilowania zapytań, co umożliwia tworzenie aplikacji o niskich opóźnieniach na HBase.

Apache Zeppelin to oparty na sieci Web Notes typu "open source", który umożliwia tworzenie opartych na danych, wspólnych dokumentów przy użyciu interakcyjnej analizy danych i języków, takich jak SQL i Scala. Pomaga ona deweloperom danych & naukowcom danych opracowywać, organizować, wykonywać i udostępniać kod na potrzeby manipulowania danymi. Pozwala to na wizualizację wyników bez odwoływania się do wiersza polecenia lub wymaganie szczegółowych informacji o klastrze.

Użytkownicy usługi HDInsight mogą używać oprogramowania Apache Zeppelin do wykonywania zapytań dotyczących tabel w Phoenix. Usługa Apache Zeppelin jest zintegrowana z klastrem usługi HDInsight i nie ma dodatkowych kroków, które należy wykonać. Po prostu Utwórz Notes Zeppelin z interpreterem JDBC i zacznij pisać zapytania SQL w Phoenix

## <a name="prerequisites"></a>Wymagania wstępne

Klaster Apache HBase w usłudze HDInsight. Zobacz Rozpoczynanie [pracy z Apache HBase](./apache-hbase-tutorial-get-started-linux.md).

## <a name="create-an-apache-zeppelin-note"></a>Tworzenie notatki Apache Zeppelin

1. Zamień na `CLUSTERNAME` nazwę klastra w następującym adresie URL `https://CLUSTERNAME.azurehdinsight.net/zeppelin` . Następnie wprowadź adres URL w przeglądarce sieci Web. Wprowadź nazwę użytkownika i hasło logowania do klastra.

1. Na stronie Zeppelin wybierz pozycję **Utwórz nową notatkę**.

    ![Zapytanie interaktywne Zeppelin usługi HDInsight](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-create-note.png)

1. W oknie dialogowym **Tworzenie nowej notatki** wpisz lub wybierz następujące wartości:

    - Nazwa komentarza: Wprowadź nazwę notatki.
    - Interpreter domyślny: wybierz pozycję **JDBC** z listy rozwijanej.

    Następnie wybierz pozycję **Utwórz notatkę**.

1. Upewnij się, że nagłówek notesu pokazuje połączony stan. Jest ona oznaczona zieloną kropką w prawym górnym rogu.

    ![Stan notesu Zeppelin](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-connected.png "Stan notesu Zeppelin")

1. Utwórz tabelę HBase. Wprowadź następujące polecenie, a następnie naciśnij klawisze **SHIFT + ENTER**:

    ```sql
    %jdbc(phoenix)
    CREATE TABLE Company (
        company_id INTEGER PRIMARY KEY,
        name VARCHAR(225)
    );
    ```

    Instrukcja **% JDBC (Phoenix)** w pierwszym wierszu instruuje Notes, aby korzystał z interpretera JDBC w Phoenix.

1. Wyświetl utworzone tabele.

    ```sql
    %jdbc(phoenix)
    SELECT DISTINCT table_name
    FROM SYSTEM.CATALOG
    WHERE table_schem is null or table_schem <> 'SYSTEM';
    ```

1. Wstaw wartości w tabeli.

    ```sql
    %jdbc(phoenix)
    UPSERT INTO dbo.Company VALUES(1, 'Microsoft');
    UPSERT INTO dbo.Company (name, company_id) VALUES('Apache', 2);
    ```

1. Zbadaj tabelę.

    ```sql
    %jdbc(phoenix)
    SELECT * FROM dbo.Company;
    ```

1. Usuń rekord.

    ```sql
    %jdbc(phoenix)
    DELETE FROM dbo.Company WHERE COMPANY_ID=1;
    ```

1. Usuń tabelę.

    ```sql
    %jdbc(phoenix)
    DROP TABLE dbo.Company;
    ```

## <a name="next-steps"></a>Następne kroki

- [Apache Phoenix teraz obsługuje Zeppelin w usłudze Azure HDInsight](/archive/blogs/ashish/apache-phoenix-now-supports-zeppelin-in-azure-hdinsight)
- [Apache Phoenix gramatyki](https://phoenix.apache.org/language/index.html)