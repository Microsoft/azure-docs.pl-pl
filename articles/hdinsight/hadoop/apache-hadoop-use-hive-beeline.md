---
title: Korzystanie z platformy Apache Z usługi Beeline z usługą Apache Hive — Azure HDInsight
description: Dowiedz się, jak używać klienta Z usługi beeline do uruchamiania zapytań Hive z usługą Hadoop w usłudze HDInsight. Z usługi Beeline to narzędzie do pracy z serwera hiveserver2em przez JDBC.
ms.service: hdinsight
ms.topic: how-to
ms.date: 10/28/2020
ms.custom: contperf-fy21q1, contperf-fy21q2
ms.openlocfilehash: e8b7478ba64da0f99a9b7a710222ff2953795adf
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943194"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Korzystanie z klienta Apache Beeline z usługą Apache Hive

W tym artykule opisano, jak używać wiersza polecenia [Apache z usługi Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) Client do tworzenia i wykonywania zapytań Apache Hive za pośrednictwem połączenia SSH.

## <a name="background"></a>Tło

Z usługi Beeline jest klientem programu Hive, który znajduje się w węzłach głównych klastra usługi HDInsight. Aby nawiązać połączenie z klientem Z usługi Beeline zainstalowanym w klastrze usługi HDInsight lub zainstalować lokalnie Z usługi Beeline, zobacz [Connect to lub install Apache z usługi Beeline](connect-install-beeline.md). Z usługi Beeline używa JDBC do nawiązywania połączenia z serwera hiveserver2, usługą hostowaną w klastrze usługi HDInsight. Możesz również użyć Z usługi Beeline, aby zdalnie uzyskiwać dostęp do usługi Hive w usłudze HDInsight za pośrednictwem Internetu. W poniższych przykładach przedstawiono najbardziej typowe parametry połączenia używane do nawiązywania połączenia z usługą HDInsight z Z usługi Beeline.

## <a name="prerequisites-for-examples"></a>Wymagania wstępne dotyczące przykładów

* Klaster usługi Hadoop w usłudze HDInsight. Zobacz Rozpoczynanie [pracy z usługą HDInsight w systemie Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Zwróć uwagę na schemat identyfikatora URI magazynu podstawowego klastra. Na przykład  `wasb://` w przypadku usługi Azure Storage w `abfs://` przypadku Azure Data Lake Storage Gen2 lub `adl://` Azure Data Lake Storage Gen1. Jeśli w usłudze Azure Storage włączono opcję bezpiecznego transferu, identyfikator URI to `wasbs://` . Aby uzyskać więcej informacji, zobacz [bezpieczny transfer](../../storage/common/storage-require-secure-transfer.md).

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). W większości kroków przedstawionych w tym dokumencie przyjęto założenie, że używasz Z usługi Beeline z sesji SSH z klastrem. Można również użyć lokalnego klienta Z usługi Beeline, ale te kroki nie zostały omówione w tym artykule.

## <a name="run-a-hive-query"></a>Uruchomienie zapytania programu Hive

Ten przykład jest oparty na użyciu klienta Z usługi Beeline z połączenia SSH.

1. Otwórz połączenie SSH z klastrem przy użyciu poniższego kodu. Zamień ciąg `sshuser` na nazwę użytkownika SSH klastra i zamień ciąg `CLUSTERNAME` na nazwę klastra. Po wyświetleniu monitu wprowadź hasło dla konta użytkownika SSH.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Aby nawiązać połączenie z usługą serwera hiveserver2 z Twoim klientem Z usługi Beeline, wprowadź następujące polecenie:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. Polecenia z usługi Beeline rozpoczynają się od `!` znaku, na przykład `!help` wyświetla pomoc. Można jednak `!` pominąć niektóre polecenia. Na przykład `help` również działa.

    Istnieje `!sql` , który jest używany do wykonywania instrukcji HiveQL. Jednak HiveQL jest tak często używany, aby można było pominąć poprzednią `!sql` . Następujące dwie instrukcje są równoważne:

    ```hiveql
    !sql show tables;
    show tables;
    ```

    W nowym klastrze jest wyświetlana tylko jedna tabela: **hivesampletable**.

4. Użyj następującego polecenia, aby wyświetlić schemat dla hivesampletable:

    ```hiveql
    describe hivesampletable;
    ```

    To polecenie zwraca następujące informacje:

    ```output
    +-----------------------+------------+----------+--+
    |       col_name        | data_type  | comment  |
    +-----------------------+------------+----------+--+
    | clientid              | string     |          |
    | querytime             | string     |          |
    | market                | string     |          |
    | deviceplatform        | string     |          |
    | devicemake            | string     |          |
    | devicemodel           | string     |          |
    | state                 | string     |          |
    | country               | string     |          |
    | querydwelltime        | double     |          |
    | sessionid             | bigint     |          |
    | sessionpagevieworder  | bigint     |          |
    +-----------------------+------------+----------+--+
    ```

    Te informacje opisują kolumny w tabeli.

5. Wprowadź następujące instrukcje, aby utworzyć tabelę o nazwie **log4jLogs** przy użyciu przykładowych danych dostarczanych z klastrem usługi HDInsight: (Popraw je w zależności od schematu identyfikatora URI).

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
    STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs
        WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log'
        GROUP BY t4;
    ```

    Te instrukcje wykonują następujące czynności:

    |Instrukcja |Opis |
    |---|---|
    |DROP TABLE|Jeśli tabela istnieje, zostanie usunięta.|
    |TWORZENIE TABELI ZEWNĘTRZNEJ|Tworzy tabelę **zewnętrzną** w usłudze Hive. Tabele zewnętrzne przechowują wyłącznie definicję tabeli w programie Hive. Dane pozostaną w oryginalnej lokalizacji.|
    |FORMAT WIERSZA|Sposób formatowania danych. W takim przypadku pola w każdym dzienniku są oddzielone spacją.|
    |PRZECHOWYWANE JAKO LOKALIZACJA TEXTFILE|Miejsce przechowywania danych i w jakim formacie pliku.|
    |SELECT|Wybiera liczbę wszystkich wierszy, w których kolumna **T4** zawiera wartość **[Error]**. To zapytanie zwraca wartość **3** , ponieważ istnieją trzy wiersze, które zawierają tę wartość.|
    |INPUT__FILE__NAME jak "%. log"|Gałąź próbuje zastosować schemat do wszystkich plików w katalogu. W takim przypadku katalog zawiera pliki, które nie są zgodne ze schematem. Aby zapobiec utracie danych bezużytecznych w wynikach, ta instrukcja informuje gałąź, że powinna zwracać tylko dane z plików kończących się na. log.|

   > [!NOTE]  
   > Tabele zewnętrzne powinny być używane, gdy oczekuje się, że dane podstawowe mają być aktualizowane przez zewnętrzne źródło. Na przykład proces automatycznego przekazywania danych lub operacja MapReduce.
   >
   > Porzucenie tabeli **zewnętrznej nie powoduje usunięcia danych** , tylko definicji tabeli.

    Dane wyjściowe tego polecenia są podobne do następującego tekstu:

    ```output
    INFO  : Tez session hasn't been created yet. Opening session
    INFO  :

    INFO  : Status: Running (Executing on YARN cluster with App id application_1443698635933_0001)

    INFO  : Map 1: -/-      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
    INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
    INFO  : Map 1: 1/1      Reducer 2: 0/1
    INFO  : Map 1: 1/1      Reducer 2: 0(+1)/1
    INFO  : Map 1: 1/1      Reducer 2: 1/1
    +----------+--------+--+
    |   sev    | count  |
    +----------+--------+--+
    | [ERROR]  | 3      |
    +----------+--------+--+
    1 row selected (47.351 seconds)
    ```

6. Zakończ Z usługi Beeline:

    ```bash
    !exit
    ```

## <a name="run-a-hiveql-file"></a>Uruchamianie pliku HiveQL

Ten przykład jest kontynuacją z poprzedniego przykładu. Wykonaj następujące kroki, aby utworzyć plik, a następnie uruchom go za pomocą Z usługi Beeline.

1. Użyj następującego polecenia, aby utworzyć plik o nazwie **Query. HQL**:

    ```bash
    nano query.hql
    ```

1. Użyj następującego tekstu jako zawartości pliku. To zapytanie tworzy nową tabelę "wewnętrzną" o nazwie **errorLogs**:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Te instrukcje wykonują następujące czynności:

    |Instrukcja |Opis |
    |---|---|
    |CREATE TABLE, JEŚLI NIE ISTNIEJE|Jeśli tabela jeszcze nie istnieje, zostanie utworzona. Ponieważ **zewnętrzne** słowo kluczowe nie jest używane, ta instrukcja tworzy tabelę wewnętrzną. Tabele wewnętrzne są przechowywane w magazynie danych programu Hive i są zarządzane całkowicie przez program Hive.|
    |PRZECHOWYWANE JAKO ORC|Dane są przechowywane w formacie zoptymalizowanego wiersza kolumnowy (ORC). Format ORC to wysoce zoptymalizowany i wydajny format służący do przechowywania danych programu Hive.|
    |WSTAW ZASTĄPIENIE... ZAZNACZENIA|Wybiera wiersze z tabeli **log4jLogs** zawierającej wartość **[Error]**, a następnie wstawia dane do tabeli **errorLogs** .|

    > [!NOTE]  
    > W przeciwieństwie do tabel zewnętrznych, porzucanie tabeli wewnętrznej również usuwa dane źródłowe.

1. Aby zapisać plik, użyj **klawiszy CTRL** + **X**, a następnie wprowadź **Y**, a na koniec **wprowadź**.

1. Użyj następującego polecenia, aby uruchomić plik za pomocą Z usługi Beeline:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > `-i`Parametr uruchamia z usługi Beeline i uruchamia instrukcje w `query.hql` pliku. Po zakończeniu zapytania zostanie `jdbc:hive2://headnodehost:10001/>` wyświetlony monit. Można również uruchomić plik za pomocą `-f` parametru, który kończy z usługi Beeline po zakończeniu zapytania.

1. Aby sprawdzić, czy tabela **errorLogs** została utworzona, użyj następującej instrukcji, aby zwrócić wszystkie wiersze z **errorLogs**:

    ```hiveql
    SELECT * from errorLogs;
    ```

    Należy zwrócić trzy wiersze danych, a wszystkie zawierające **[Error]** w kolumnie T4:

    ```output
    +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
    | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
    +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
    | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
    | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
    | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
    +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
    3 rows selected (0.813 seconds)
    ```

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej ogólnych informacji na temat platformy Hive w usłudze HDInsight, zobacz [używanie Apache Hive z usługą Apache Hadoop w usłudze HDInsight](hdinsight-use-hive.md)

* Aby uzyskać więcej informacji na temat innych sposobów pracy z usługą Hadoop w usłudze HDInsight, zobacz [Używanie MapReduce z usługą Apache Hadoop w usłudze HDInsight](hdinsight-use-mapreduce.md)
