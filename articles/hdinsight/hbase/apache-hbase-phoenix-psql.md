---
title: Ładowanie zbiorcze do Apache Phoenix przy użyciu PSQL — Azure HDInsight
description: Ładowanie zbiorczych danych ładowania do tabel Apache Phoenix w usłudze Azure HDInsight za pomocą narzędzia PSQL
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: d1ddf69acda442212500200eb6dc326dcbcb3c1b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104867801"
---
# <a name="bulk-load-data-into-apache-phoenix-using-psql"></a>Zbiorcze ładowanie danych do oprogramowania Apache Phoenix za pomocą programu psql

[Apache Phoenix](https://phoenix.apache.org/) to "open source", wysoce równoległa relacyjna baza danych oparta na platformie [Apache HBase](../hbase/apache-hbase-overview.md). Phoenix udostępnia zapytania podobne do języka SQL w programie HBase. Phoenix korzysta ze sterowników JDBC, aby umożliwić użytkownikom tworzenie, usuwanie i modyfikowanie tabel SQL, indeksów, widoków i sekwencji oraz wierszy upsert pojedynczo i zbiorczo. Phoenix używa kompilacji natywnej noSQL zamiast używania MapReduce do kompilowania zapytań, aby utworzyć aplikacje o niskim opóźnieniu na HBase. Phoenix dodaje współprocesory obsługujące uruchamianie kodu dostarczonego przez klienta w przestrzeni adresowej serwera, wykonując z danymi. To minimalizuje transfer danych klienta/serwera.  Aby korzystać z danych przy użyciu platformy Phoenix w usłudze HDInsight, najpierw Utwórz tabele, a następnie Załaduj do nich dane.

## <a name="bulk-loading-with-apache-phoenix"></a>Ładowanie zbiorcze z Apache Phoenix

Istnieje wiele sposobów na uzyskanie danych do HBase, w tym Używanie interfejsów API klienta, zadania MapReduce z TableOutputFormat lub wprowadzanie danych ręcznie przy użyciu powłoki HBase. Phoenix oferuje dwie metody ładowania danych CSV do tabel w Phoenix: narzędzia ładowania klienta o nazwie `psql` i narzędzia ładowania zbiorczego opartego na MapReduce.

`psql`Narzędzie jest jednowątkowe i najlepiej nadaje się do ładowania megabajtów lub gigabajtów danych. Wszystkie pliki CSV do załadowania muszą mieć rozszerzenie pliku CSV.  Możesz również określić pliki skryptów SQL w `psql` wierszu polecenia z rozszerzeniem pliku ". SQL".

Ładowanie zbiorcze z MapReduce jest używane dla znacznie większych woluminów danych, zwykle w scenariuszach produkcyjnych, ponieważ MapReduce używa wielu wątków.

Przed rozpoczęciem ładowania danych upewnij się, że Phoenix jest włączona i że ustawienia limitu czasu zapytania są zgodnie z oczekiwaniami.  Uzyskaj dostęp do pulpitu nawigacyjnego [Apache Ambari](https://ambari.apache.org/) klastra usługi HDInsight, wybierz pozycję HBase, a następnie kartę Konfiguracja.  Przewiń w dół, aby sprawdzić, czy Apache Phoenix jest ustawiona na `enabled` tak, jak pokazano:

:::image type="content" source="./media/apache-hbase-phoenix-psql/apache-ambari-phoenix.png" alt-text="Apache Phoenix ustawień klastra usługi HDInsight" border="true":::

### <a name="use-psql-to-bulk-load-tables"></a>Użyj `psql` do ładowania zbiorczego tabel

1. Utwórz plik o nazwie `createCustomersTable.sql` i Skopiuj poniższy kod do pliku. Następnie zapisz i zamknij plik.

    ```sql
    CREATE TABLE Customers (
        ID varchar NOT NULL PRIMARY KEY,
        Name varchar,
        Income decimal,
        Age INTEGER,
        Country varchar);
    ```

1. Utwórz plik o nazwie `listCustomers.sql` i Skopiuj poniższy kod do pliku. Następnie zapisz i zamknij plik.

    ```sql
    SELECT * from Customers;
    ```

1. Utwórz plik o nazwie `customers.csv` i Skopiuj poniższy kod do pliku. Następnie zapisz i zamknij plik.

    ```txt
    1,Samantha,260000.0,18,US
    2,Sam,10000.5,56,US
    3,Anton,550150.0,42,Norway
    ```

1. Utwórz plik o nazwie `customers2.csv` i Skopiuj poniższy kod do pliku. Następnie zapisz i zamknij plik.

    ```txt
    4,Nicolle,180000.0,22,US
    5,Kate,210000.5,24,Canada
    6,Ben,45000.0,32,Poland
    ```

1. Otwórz wiersz polecenia i zmień katalog na lokalizację nowo utworzonych plików. Zastąp wartość CLUSTERname, poniżej z rzeczywistą nazwą klastra HBase. Następnie wykonaj kod w celu przekazania plików do węzła głównego klastra:

    ```cmd
    scp customers.csv customers2.csv createCustomersTable.sql listCustomers.sql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/tmp
    ```

1. Użyj [polecenia SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) do nawiązania połączenia z klastrem. Edytuj poniższe polecenie, zastępując wartość CLUSTERname nazwą klastra, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. W sesji SSH Zmień katalog na lokalizację narzędzia **PSQL** . Wykonaj poniższe polecenie:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

1. Ładowanie zbiorcze danych. Poniższy kod utworzy tabelę **Customers** , a następnie przekaże dane.

    ```bash
    python psql.py /tmp/createCustomersTable.sql /tmp/customers.csv
    ```

    Po `psql` zakończeniu operacji powinien zostać wyświetlony komunikat podobny do poniższego:

    ```output
    csv columns from database.
    CSV Upsert complete. 3 rows upserted
    Time: 0.081 sec(s)
    ```

1. Można nadal używać `psql` do wyświetlania zawartości tabeli Customers. Wykonaj Poniższy kod:

    ```bash
    python psql.py /tmp/listCustomers.sql
    ```

    Alternatywnie można użyć [powłoki HBase](./query-hbase-with-hbase-shell.md)lub [Apache Zeppelin](./apache-hbase-phoenix-zeppelin.md) do wykonywania zapytań dotyczących danych.

1. Przekaż dodatkowe dane. Teraz, gdy tabela już istnieje, polecenie określa tabelę. Wykonaj poniższe polecenie:

    ```bash
    python psql.py -t CUSTOMERS /tmp/customers2.csv
    ```

## <a name="use-mapreduce-to-bulk-load-tables"></a>Ładowanie tabel przy użyciu MapReduce

Aby obciążać obciążenie wyższego poziomu przepływności za pośrednictwem klastra, użyj narzędzia ładowania MapReduce. Ten moduł ładujący najpierw konwertuje wszystkie dane do HFiles, a następnie tworzy HFiles do HBase.

1. Ta sekcja kontynuuje pracę z sesją SSH i utworzone wcześniej obiekty. Utwórz tabelę **Customers** i plik **customers.csv** w razie konieczności, wykonując kroki opisane powyżej. W razie potrzeby ponownie Ustanów połączenie SSH.

1. Obetnij zawartość tabeli **Customers** . Z otwartej sesji SSH wykonaj następujące polecenia:

    ```bash
    hbase shell
    truncate 'CUSTOMERS'
    exit
    ```

1. Skopiuj `customers.csv` plik z usługi węzła głównego do usługi Azure Storage.

    ```bash
    hdfs dfs -put /tmp/customers.csv wasbs:///tmp/customers.csv
    ```

1. Przejdź do katalogu wykonywania polecenia MapReduce zbiorczego ładowania:

    ```bash
    cd /usr/hdp/current/phoenix-client
    ```

1. Uruchom moduł ładujący MapReduce woluminów CSV przy użyciu `hadoop` polecenia z JAR klienta w Phoenix:

    ```bash
    HADOOP_CLASSPATH=/usr/hdp/current/hbase-client/lib/hbase-protocol.jar:/etc/hbase/conf hadoop jar phoenix-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input /tmp/customers.csv
    ```

    Po zakończeniu przekazywania powinien zostać wyświetlony komunikat podobny do poniższego:

    ```output
    19/12/18 18:30:57 INFO client.ConnectionManager$HConnectionImplementation: Closing master protocol: MasterService
    19/12/18 18:30:57 INFO client.ConnectionManager$HConnectionImplementation: Closing zookeeper sessionid=0x26f15dcceff02c3
    19/12/18 18:30:57 INFO zookeeper.ZooKeeper: Session: 0x26f15dcceff02c3 closed
    19/12/18 18:30:57 INFO zookeeper.ClientCnxn: EventThread shut down
    19/12/18 18:30:57 INFO mapreduce.AbstractBulkLoadTool: Incremental load complete for table=CUSTOMERS
    19/12/18 18:30:57 INFO mapreduce.AbstractBulkLoadTool: Removing output directory /tmp/50254426-aba6-400e-88eb-8086d3dddb6
    ```

1. Aby użyć MapReduce z Azure Data Lake Storage, Zlokalizuj Data Lake Storage katalog główny, który jest `hbase.rootdir` wartością w `hbase-site.xml` . W poniższym poleceniu Data Lake Storage katalog główny `adl://hdinsightconf1.azuredatalakestore.net:443/hbase1` . W tym poleceniu Określ Data Lake Storage wejściowe i wyjściowe folderów jako parametry:

    ```bash
    cd /usr/hdp/current/phoenix-client

    $HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/temp/input/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure --output  adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/output1
    ```

1. Aby zbadać i wyświetlić dane, możesz użyć **PSQL** zgodnie z wcześniejszym opisem. Możesz również użyć [powłoki HBase](./query-hbase-with-hbase-shell.md)lub [Apache Zeppelin](./apache-hbase-phoenix-zeppelin.md).

## <a name="recommendations"></a>Zalecenia

* Użyj tego samego nośnika magazynu dla folderów wejściowych i wyjściowych, usługi Azure Storage (WASB) lub Azure Data Lake Storage (ADL). Aby przenieść dane z usługi Azure Storage do Data Lake Storage, można użyć `distcp` polecenia:

    ```bash
    hadoop distcp wasb://@.blob.core.windows.net/example/data/gutenberg adl://.azuredatalakestore.net:443/myfolder
    ```

* Używaj węzłów procesu roboczego o większym rozmiarze. Procesy mapy kopii zbiorczej MapReduce tworzą duże ilości danych wyjściowych, które wypełniają dostępne miejsce inne niż systemu plików DFS. W przypadku dużej ilości ładowania zbiorczego należy użyć więcej i większych węzłów procesów roboczych. Liczba węzłów procesu roboczego przydzielenia do klastra wpływa bezpośrednio na szybkość przetwarzania.

* Podziel pliki wejściowe na segmenty ~ 10 GB. Ładowanie zbiorcze jest operacją intensywnie korzystającą z magazynu, więc dzielenie plików wejściowych na wiele fragmentów skutkuje lepszą wydajnością.

* Unikaj hotspotów serwera regionów. Jeśli klucz wiersza jest monotonicznie rosnący, HBase sekwencyjne zapisy mogą wywołać serwer regionu hotspotting. *Solenie* klucza wiersza zmniejsza sekwencyjne zapisywanie. Phoenix zapewnia sposób nieprzezroczystego posolenia klucza wiersza z bajtem soli dla określonej tabeli, jak opisano poniżej.

## <a name="next-steps"></a>Następne kroki

* [Ładowanie danych zbiorczych za pomocą Apache Phoenix](https://phoenix.apache.org/bulk_dataload.html)
* [Korzystanie z Apache Phoenix w przypadku klastrów Apache HBase opartych na systemie Linux w usłudze HDInsight](../hbase/apache-hbase-query-with-phoenix.md)
* [Tabele solone](https://phoenix.apache.org/salted.html)
* [Apache Phoenix gramatyki](https://phoenix.apache.org/language/index.html)
