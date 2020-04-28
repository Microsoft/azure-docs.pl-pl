---
title: Korzystanie z platformy Apache Z usługi Beeline z usługą Apache Hive — Azure HDInsight
description: Dowiedz się, jak używać klienta Z usługi beeline do uruchamiania zapytań Hive z usługą Hadoop w usłudze HDInsight. Z usługi Beeline to narzędzie do pracy z serwera hiveserver2em przez JDBC.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/17/2020
ms.openlocfilehash: 2396207c88716420d299382006a270eb747ddc03
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192667"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Korzystanie z klienta Apache Beeline z usługą Apache Hive

Dowiedz się, jak używać oprogramowania [Apache z usługi Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) do uruchamiania zapytań Apache Hive w usłudze HDInsight.

Z usługi Beeline jest klientem programu Hive, który znajduje się w węzłach głównych klastra usługi HDInsight. Aby zainstalować Z usługi Beeline lokalnie, zobacz [install z usługi Beeline Client](#install-beeline-client). Z usługi Beeline używa JDBC do nawiązywania połączenia z serwera hiveserver2, usługą hostowaną w klastrze usługi HDInsight. Możesz również użyć Z usługi Beeline, aby zdalnie uzyskiwać dostęp do usługi Hive w usłudze HDInsight za pośrednictwem Internetu. W poniższych przykładach przedstawiono najbardziej typowe parametry połączenia używane do nawiązywania połączenia z usługą HDInsight z Z usługi Beeline.

## <a name="types-of-connections"></a>Typy połączeń

### <a name="from-an-ssh-session"></a>Z sesji SSH

Podczas nawiązywania połączenia z sesji SSH z klastrem węzła głównego można połączyć się z `headnodehost` adresem na porcie: `10001`

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

---

### <a name="over-an-azure-virtual-network"></a>Za pośrednictwem Virtual Network platformy Azure

Podczas nawiązywania połączenia z klientem z usługą HDInsight za pośrednictwem usługi Azure Virtual Network należy podać w pełni kwalifikowaną nazwę domeny (FQDN) węzła głównego klastra. Ponieważ to połączenie jest nawiązywane bezpośrednio z węzłami klastra, połączenie używa portu `10001`:

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Zamień `<headnode-FQDN>` na w pełni kwalifikowaną nazwę domeny węzła głównego klastra. Aby znaleźć w pełni kwalifikowaną nazwę domeny węzła głównego, Skorzystaj z informacji w temacie [Zarządzanie usługą HDInsight przy użyciu dokumentu interfejsu API REST usługi Apache Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes) .

---

### <a name="to-hdinsight-enterprise-security-package-esp-cluster-using-kerberos"></a>Do klastra usługi HDInsight pakiet Enterprise Security (ESP) przy użyciu protokołu Kerberos

Podczas nawiązywania połączenia z klientem z klastrem pakiet Enterprise Security (ESP) przyłączonym do Azure Active Directory (AAD) — DS na komputerze w tym samym obszarze klastra należy również określić nazwę `<AAD-Domain>` domeny i nazwę konta użytkownika domeny z uprawnieniami dostępu do klastra: `<username>`

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

Zamień `<username>` na nazwę konta w domenie z uprawnieniami dostępu do klastra. Zamień `<AAD-DOMAIN>` na nazwę Azure Active Directory (AAD), do której jest dołączony klaster. Użyj ciągu z wielką literą `<AAD-DOMAIN>` dla wartości, w przeciwnym razie nie zostanie znalezione poświadczenie. W `/etc/krb5.conf` razie potrzeby Sprawdź nazwy obszarów.

Aby znaleźć adres URL JDBC z Ambari:

1. W przeglądarce sieci Web przejdź do `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary`lokalizacji, gdzie `CLUSTERNAME` jest nazwą klastra. Upewnij się, że serwera hiveserver2 jest uruchomiony.

1. Użyj schowka, aby skopiować adres URL serwera hiveserver2 JDBC.

---

### <a name="over-public-or-private-endpoints"></a>Za pośrednictwem publicznych lub prywatnych punktów końcowych

Podczas nawiązywania połączenia z klastrem przy użyciu publicznych lub prywatnych punktów końcowych należy podać nazwę konta logowania klastra ( `admin`ustawienie domyślne) i hasło. Na przykład przy użyciu Z usługi Beeline z systemu klienckiego do nawiązywania połączenia `clustername.azurehdinsight.net` z adresem. To połączenie jest nawiązywane za `443`pośrednictwem portu i szyfrowane przy użyciu protokołu TLS/SSL.

Element `clustername` należy zastąpić nazwą klastra usługi HDInsight. Zamień `admin` na konto logowania klastra dla klastra. W przypadku klastrów ESP Użyj pełnej nazwy UPN (na przykład user@domain.com). Zamień `password` na hasło dla konta logowania klastra.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

lub dla prywatnego punktu końcowego:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

Prywatne punkty końcowe wskazują podstawowy moduł równoważenia obciążenia, do którego można uzyskać dostęp tylko z sieci wirtualnych komunikacji równorzędnej w tym samym regionie. Aby uzyskać więcej informacji [, zobacz ograniczenia dotyczące globalnej komunikacji równorzędnej sieci wirtualnej i modułów równoważenia obciążenia](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) . Możesz użyć `curl` polecenia z `-v` opcją, aby rozwiązać problemy z łącznością z publicznymi lub prywatnymi punktami końcowymi przed użyciem z usługi Beeline.

---

### <a name="use-beeline-with-apache-spark"></a>Użyj Z usługi Beeline z Apache Spark

Apache Spark udostępnia własną implementację serwera hiveserver2, która jest czasami określana jako serwer Spark Thrift. Ta usługa używa platformy Spark SQL do rozpoznawania zapytań zamiast Hive. I może zapewnić lepszą wydajność w zależności od zapytania.

#### <a name="through-public-or-private-endpoints"></a>Za poorednictwem publicznych lub prywatnych punktów końcowych

Użyte parametry połączenia są nieco inne. Zamiast z `httpPath=/hive2` niego korzystają `httpPath/sparkhive2`. Element `clustername` należy zastąpić nazwą klastra usługi HDInsight. Zamień `admin` na konto logowania klastra dla klastra. W przypadku klastrów ESP Użyj pełnej nazwy UPN (na przykład user@domain.com). Zamień `password` na hasło dla konta logowania klastra.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

lub dla prywatnego punktu końcowego:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

Prywatne punkty końcowe wskazują podstawowy moduł równoważenia obciążenia, do którego można uzyskać dostęp tylko z sieci wirtualnych komunikacji równorzędnej w tym samym regionie. Aby uzyskać więcej informacji [, zobacz ograniczenia dotyczące globalnej komunikacji równorzędnej sieci wirtualnej i modułów równoważenia obciążenia](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) . Możesz użyć `curl` polecenia z `-v` opcją, aby rozwiązać problemy z łącznością z publicznymi lub prywatnymi punktami końcowymi przed użyciem z usługi Beeline.

---

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>Z poziomu węzła klastra lub wewnątrz Virtual Network platformy Azure z Apache Spark

W przypadku łączenia się bezpośrednio z węzłem głównym klastra lub z zasobu w ramach tego samego Virtual Network platformy Azure jako klastra usługi HDInsight `10002` port powinien być używany dla serwera Spark Thrift zamiast `10001`systemu. Poniższy przykład pokazuje, jak połączyć się bezpośrednio z węzłem głównym:

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

---

## <a name="prerequisites-for-examples"></a>Wymagania wstępne dotyczące przykładów

* Klaster usługi Hadoop w usłudze HDInsight. Zobacz Rozpoczynanie [pracy z usługą HDInsight w systemie Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Zwróć uwagę na schemat identyfikatora URI magazynu podstawowego klastra. Na przykład `wasb://` w przypadku usługi Azure Storage `abfs://` w przypadku Azure Data Lake Storage Gen2 lub `adl://` Azure Data Lake Storage Gen1. Jeśli w usłudze Azure Storage włączono opcję bezpiecznego transferu, identyfikator URI `wasbs://`to. Aby uzyskać więcej informacji, zobacz [bezpieczny transfer](../../storage/common/storage-require-secure-transfer.md).

* Opcja 1: klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). W większości kroków przedstawionych w tym dokumencie przyjęto założenie, że używasz Z usługi Beeline z sesji SSH z klastrem.

* Opcja 2: lokalny klient Z usługi Beeline.

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

3. Polecenia z usługi Beeline rozpoczynają się `!` od znaku, na `!help` przykład wyświetla pomoc. `!` Można jednak pominąć niektóre polecenia. Na przykład `help` również działa.

    Istnieje `!sql`, który jest używany do wykonywania instrukcji HiveQL. Jednak HiveQL jest tak często używany, aby można było pominąć poprzednią `!sql`. Następujące dwie instrukcje są równoważne:

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
    |USUŃ TABELĘ|Jeśli tabela istnieje, zostanie usunięta.|
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

1. Aby zapisać plik, użyj **klawiszy CTRL**+**X**, a następnie wprowadź **Y**, a na koniec **wprowadź**.

1. Użyj następującego polecenia, aby uruchomić plik za pomocą Z usługi Beeline:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > `-i` Parametr uruchamia z usługi Beeline i uruchamia instrukcje w `query.hql` pliku. Po zakończeniu zapytania zostanie `jdbc:hive2://headnodehost:10001/>` wyświetlony monit. Można również uruchomić plik za pomocą `-f` parametru, który kończy z usługi Beeline po zakończeniu zapytania.

1. Aby sprawdzić, czy tabela **errorLogs** została utworzona, użyj następującej instrukcji, aby zwrócić wszystkie wiersze z **errorLogs**:

    ```hiveql
    SELECT * from errorLogs;
    ```

    Należy zwrócić trzy wiersze danych, a wszystkie zawierające **[Error]** w kolumnie T4:

        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (0.813 seconds)

## <a name="install-beeline-client"></a>Zainstaluj klienta z usługi Beeline

Mimo że usługa Z usługi Beeline jest dołączana do węzłów głównych, warto zainstalować ją lokalnie.  Kroki instalacji dla komputera lokalnego są oparte na [podsystemie Windows dla systemu Linux](https://docs.microsoft.com/windows/wsl/install-win10).

1. Aktualizowanie list pakietów. Wprowadź następujące polecenie w powłoce bash:

    ```bash
    sudo apt-get update
    ```

1. Zainstaluj język Java, jeśli nie jest zainstalowany. Możesz sprawdzić za pomocą `which java` polecenia.

    1. Jeśli nie zainstalowano żadnego pakietu Java, wprowadź następujące polecenie:

        ```bash
        sudo apt install openjdk-11-jre-headless
        ```

    1. Otwórz plik BASHRC (często znaleziono w ~/.bashrc.): `nano ~/.bashrc`.

    1. Popraw plik BASHRC. Dodaj następujący wiersz na końcu pliku:

        ```bash
        export JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64
        ```

        Naciśnij **klawisze Ctrl + X**, a następnie **Y**, a następnie ENTER.

1. Pobierz archiwa Hadoop i Z usługi Beeline, wprowadź następujące polecenia:

    ```bash
    wget https://archive.apache.org/dist/hadoop/core/hadoop-2.7.3/hadoop-2.7.3.tar.gz
    wget https://archive.apache.org/dist/hive/hive-1.2.1/apache-hive-1.2.1-bin.tar.gz
    ```

1. Rozpakuj archiwa, wprowadź następujące polecenia:

    ```bash
    tar -xvzf hadoop-2.7.3.tar.gz
    tar -xvzf apache-hive-1.2.1-bin.tar.gz
    ```

1. Dalsze zmiany pliku bashrc. Należy określić ścieżkę, w której archiwa zostały rozpakowane. W przypadku korzystania z [podsystemu Windows dla systemu Linux](https://docs.microsoft.com/windows/wsl/install-win10), jeśli wykonano kroki dokładnie, ścieżką `/mnt/c/Users/user/`będzie `user` , gdzie to nazwa użytkownika.

    1. Otwórz plik:`nano ~/.bashrc`

    1. Zmodyfikuj poniższe polecenia z odpowiednią ścieżką, a następnie wprowadź je na końcu pliku bashrc:

        ```bash
        export HADOOP_HOME=/path_where_the_archives_were_unpacked/hadoop-2.7.3
        export HIVE_HOME=/path_where_the_archives_were_unpacked/apache-hive-1.2.1-bin
        PATH=$PATH:$HIVE_HOME/bin
        ```

    1. Naciśnij **klawisze Ctrl + X**, a następnie **Y**, a następnie ENTER.

1. Zamknij i ponownie otwórz sesję bash.

1. Przetestuj połączenie. Użyj formatu połączenia z [ponad publiczne lub prywatne punkty końcowe](#over-public-or-private-endpoints), powyżej.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej ogólnych informacji na temat platformy Hive w usłudze HDInsight, zobacz [używanie Apache Hive z usługą Apache Hadoop w usłudze HDInsight](hdinsight-use-hive.md)

* Aby uzyskać więcej informacji na temat innych sposobów pracy z usługą Hadoop w usłudze HDInsight, zobacz [Używanie MapReduce z usługą Apache Hadoop w usłudze HDInsight](hdinsight-use-mapreduce.md)
