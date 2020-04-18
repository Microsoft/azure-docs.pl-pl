---
title: Korzystanie z usługi Apache Beeline z gałęzią Apache — Usługa Azure HDInsight
description: Dowiedz się, jak używać klienta Beeline do uruchamiania zapytań hive z Hadoop na HDInsight. Beeline to narzędzie do pracy z HiveServer2 nad JDBC.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/17/2020
ms.openlocfilehash: 10e53b6b7b79e7d4581a1843b70b3d02778e8df5
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617798"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Korzystanie z klienta Apache Beeline z usługą Apache Hive

Dowiedz się, jak używać [apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) do uruchamiania zapytań apache hive w programie HDInsight.

Beeline jest klientem hive, który znajduje się w węzłach główny klastra HDInsight. Aby zainstalować Beeline lokalnie, zobacz [Instalowanie klienta beeline](#install-beeline-client), poniżej. Beeline używa JDBC do łączenia się z HiveServer2, usługą hostową w klastrze HDInsight. Za pomocą funkcji Beeline można również uzyskać dostęp do usługi Hive w umiaście zdalnie przez Internet. Poniższe przykłady zawierają najbardziej typowe parametry połączenia używane do łączenia się z hdinsight z Beeline.

## <a name="types-of-connections"></a>Rodzaje połączeń

### <a name="from-an-ssh-session"></a>Z sesji SSH

Podczas łączenia się z sesji SSH z węzłem główny `headnodehost` klastra `10001`można następnie połączyć się z adresem na porcie:

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

---

### <a name="over-an-azure-virtual-network"></a>Za pośrednictwem sieci wirtualnej platformy Azure

Podczas łączenia się z klienta do usługi HDInsight za pośrednictwem sieci wirtualnej platformy Azure należy podać w pełni kwalifikowaną nazwę domeny (FQDN) węzła głównego klastra. Ponieważ to połączenie jest nawiązywać bezpośrednio do węzłów klastra, połączenie używa portu: `10001`

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Zamień `<headnode-FQDN>` na w pełni kwalifikowaną nazwę domeny węzła główny klastra. Aby znaleźć w pełni kwalifikowaną nazwę domeny headnode, użyj informacji w [zarządzania hdinsight przy użyciu dokumentu APACHE Ambari REST API.](../hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes)

---

### <a name="to-hdinsight-enterprise-security-package-esp-cluster-using-kerberos"></a>Do klastra pakietu zabezpieczeń organizacji HDInsight (ESP) przy użyciu protokołu Kerberos

Podczas łączenia się z klientem z klastrem pakietu zabezpieczeń przedsiębiorstwa (ESP) przyłączonego do usługi Azure Active Directory (AAD)-DS na komputerze w tym samym obszarze klastra, należy również określić nazwę `<AAD-Domain>` domeny i nazwę konta użytkownika domeny z uprawnieniami dostępu do klastra: `<username>`

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

Zastąp `<username>` nazwą konta w domenie uprawnieniami dostępu do klastra. Zamień `<AAD-DOMAIN>` na nazwę usługi Azure Active Directory (AAD), do których jest przyłączony klaster. Użyj ciągu wielkich liter `<AAD-DOMAIN>` dla wartości, w przeciwnym razie poświadczenie nie zostaną znalezione. Sprawdź `/etc/krb5.conf` nazwy domen, jeśli to konieczne.

Aby znaleźć adres URL JDBC z Ambari:

1. W przeglądarce internetowej `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary`przejdź `CLUSTERNAME` do miejsca , gdzie jest nazwa klastra. Upewnij się, że hiveServer2 jest uruchomiony.

1. Użyj schowka, aby skopiować adres URL JDBC HiveServer2.

---

### <a name="over-public-or-private-endpoints"></a>W publicznych lub prywatnych punktach końcowych

Podczas łączenia się z klastrem przy użyciu publicznych lub prywatnych punktów `admin`końcowych należy podać nazwę konta logowania klastra (domyślnie) i hasło. Na przykład za pomocą Beeline z systemu `clustername.azurehdinsight.net` klienckiego, aby połączyć się z adresem. To połączenie jest `443`nawiązywanie za pośrednictwem portu i jest szyfrowane przy użyciu protokołu TLS/SSL.

Element `clustername` należy zastąpić nazwą klastra usługi HDInsight. Zamień `admin` konto logowania klastra dla klastra. W przypadku klastrów ESP należy użyć pełnej sieci UPN (na przykład user@domain.com). Zamień `password` na hasło do konta logowania klastra.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

lub dla prywatnego punktu końcowego:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

Prywatne punkty końcowe wskazują podstawowy moduł równoważenia obciążenia, do którego można uzyskać dostęp tylko z sieci wirtualnych równorzędnych w tym samym regionie. Aby uzyskać więcej informacji, zobacz [ograniczenia dotyczące komunikacji równorzędnej i równoważenia obciążenia sieci wirtualnej.](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) Przed użyciem `curl` linii `-v` pszczelej można użyć polecenia z opcją rozwiązywania problemów z łącznością z publicznymi lub prywatnymi punktami końcowymi.

---

### <a name="use-beeline-with-apache-spark"></a>Użyj Beeline z Apache Spark

Apache Spark zapewnia własną implementację HiveServer2, który jest czasami określany jako serwer Spark Thrift. Ta usługa używa programu Spark SQL do rozpoznawania zapytań zamiast gałęzi. I może zapewnić lepszą wydajność w zależności od zapytania.

#### <a name="through-public-or-private-endpoints"></a>Za pośrednictwem publicznych lub prywatnych punktów końcowych

Używany ciąg połączenia jest nieco inny. Zamiast zawierać `httpPath=/hive2` używa . `httpPath/sparkhive2` Element `clustername` należy zastąpić nazwą klastra usługi HDInsight. Zamień `admin` konto logowania klastra dla klastra. W przypadku klastrów ESP należy użyć pełnej sieci UPN (na przykład user@domain.com). Zamień `password` na hasło do konta logowania klastra.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

lub dla prywatnego punktu końcowego:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

Prywatne punkty końcowe wskazują podstawowy moduł równoważenia obciążenia, do którego można uzyskać dostęp tylko z sieci wirtualnych równorzędnych w tym samym regionie. Aby uzyskać więcej informacji, zobacz [ograniczenia dotyczące komunikacji równorzędnej i równoważenia obciążenia sieci wirtualnej.](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) Przed użyciem `curl` linii `-v` pszczelej można użyć polecenia z opcją rozwiązywania problemów z łącznością z publicznymi lub prywatnymi punktami końcowymi.

---

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>Z głowicy klastra lub wewnątrz sieci wirtualnej platformy Azure z apache spark

Podczas łączenia się bezpośrednio z węzła głównego klastra lub z zasobu wewnątrz `10002` tej samej sieci wirtualnej platformy `10001`Azure co klaster HDInsight, port powinien być używany dla serwera Spark Thrift zamiast . W poniższym przykładzie pokazano, jak połączyć się bezpośrednio z węzłem głównym:

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

---

## <a name="prerequisites-for-examples"></a>Wymagania wstępne dla przykładów

* Klaster Hadoop na HDInsight. Zobacz [Wprowadzenie do HDInsight w systemie Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Zwróć uwagę na schemat identyfikatora URI dla magazynu podstawowego klastra. Na przykład `wasb://` dla usługi `abfs://` Azure Storage, usługi Azure `adl://` Data Lake Storage Gen2 lub usługi Azure Data Lake Storage Gen1. Jeśli bezpieczny transfer jest włączony dla usługi `wasbs://`Azure Storage, identyfikator URI jest . Aby uzyskać więcej informacji, zobacz [bezpieczny transfer](../../storage/common/storage-require-secure-transfer.md).

* Opcja 1: Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). Większość kroków w tym dokumencie zakłada, że używasz Beeline z sesji SSH do klastra.

* Opcja 2: Lokalny klient Beeline.

## <a name="run-a-hive-query"></a>Uruchomienie zapytania programu Hive

W tym przykładzie jest oparty na użyciu klienta Beeline z połączenia SSH.

1. Otwórz połączenie SSH z klastrem za pomocą poniższego kodu. Zamień ciąg `sshuser` na nazwę użytkownika SSH klastra i zamień ciąg `CLUSTERNAME` na nazwę klastra. Po wyświetleniu monitu wprowadź hasło do konta użytkownika SSH.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Połącz się z HiveServer2 z klientem Beeline z otwartej sesji SSH, wprowadzając następujące polecenie:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. Polecenia beeline zaczynają `!` się od `!help` znaku, na przykład wyświetla pomoc. Jednak `!` można pominąć dla niektórych poleceń. Na przykład, `help` również działa.

    Istnieje `!sql`, który jest używany do wykonywania instrukcji HiveQL. Jednak HiveQL jest tak powszechnie używany, że można `!sql`pominąć poprzednie . Następujące dwie instrukcje są równoważne:

    ```hiveql
    !sql show tables;
    show tables;
    ```

    W nowym klastrze wyświetlana jest tylko jedna tabela: **ulesampletable**.

4. Użyj następującego polecenia, aby wyświetlić schemat dla pouci:

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

5. Wprowadź następujące instrukcje, aby utworzyć tabelę o nazwie **log4jLogs** przy użyciu przykładowych danych dostarczonych z klastrem HDInsight: (Poprawiaj w razie potrzeby na podstawie schematu identyfikatora URI).

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

    Te instrukcje wykonują następujące akcje:

    |Instrukcja |Opis |
    |---|---|
    |TABELA UPUSZCZANIA|Jeśli tabela istnieje, zostanie usunięta.|
    |TWORZENIE TABELI ZEWNĘTRZNEJ|Tworzy tabelę **zewnętrzną** w obszarze gałęzi. Tabele zewnętrzne przechowują tylko definicję tabeli w obszarze Gałęzi. Dane są pozostawione w oryginalnej lokalizacji.|
    |FORMAT WIERSZA|Sposób formatowania danych. W takim przypadku pola w każdym dzienniku są oddzielone spacją.|
    |PRZECHOWYWANE JAKO LOKALIZACJA PLIKU TEKSTOWEGO|Gdzie dane są przechowywane i w jakim formacie pliku.|
    |SELECT|Wybiera liczbę wszystkich wierszy, w których kolumna **t4** zawiera wartość **[BŁĄD]**. Ta kwerenda zwraca wartość **3,** ponieważ istnieją trzy wiersze, które zawierają tę wartość.|
    |INPUT__FILE__NAME JAK '%.log'|Hive próbuje zastosować schemat do wszystkich plików w katalogu. W takim przypadku katalog zawiera pliki, które nie pasują do schematu. Aby zapobiec danych śmieci w wynikach, ta instrukcja informuje Hive, że należy zwracać tylko dane z plików kończących się w .log.|

   > [!NOTE]  
   > Tabele zewnętrzne powinny być używane, gdy oczekujesz, że dane źródłowe mają być aktualizowane przez źródło zewnętrzne. Na przykład zautomatyzowany proces przekazywania danych lub operacja MapReduce.
   >
   > Upuszczenie tabeli zewnętrznej **nie** powoduje usunięcia danych, a jedynie definicji tabeli.

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

6. Wyjdź z beeline:

    ```bash
    !exit
    ```

## <a name="run-a-hiveql-file"></a>Uruchamianie pliku HiveQL

W tym przykładzie jest kontynuacja z poprzedniego przykładu. Aby utworzyć plik, należy wykonać następujące czynności, a następnie uruchomić go za pomocą beeline.

1. Użyj następującego polecenia, aby utworzyć plik o nazwie **query.hql**:

    ```bash
    nano query.hql
    ```

1. Użyj następującego tekstu jako zawartości pliku. Ta kwerenda tworzy nową "wewnętrzną" tabelę o nazwie **errorLogs:**

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Te instrukcje wykonują następujące akcje:

    |Instrukcja |Opis |
    |---|---|
    |UTWÓRZ TABELĘ, JEŚLI NIE ISTNIEJE|Jeśli tabela jeszcze nie istnieje, zostanie utworzona. Ponieważ **słowo** kluczowe EXTERNAL nie jest używane, ta instrukcja tworzy tabelę wewnętrzną. Wewnętrzne tabele są przechowywane w magazynie danych hive i są zarządzane całkowicie przez hive.|
    |Przechowywane jako ORC|Przechowuje dane w formacie Zoptymalizowany wiersz kolumnowy (ORC). Format ORC jest wysoce zoptymalizowanym i wydajnym formatem do przechowywania danych Hive.|
    |WSTAW ZASTĄPIENIE ... Wybierz|Wybiera wiersze z **log4jLogs** tabeli, które zawierają **[BŁĄD]**, a następnie wstawia dane do tabeli **errorLogs.**|

    > [!NOTE]  
    > W przeciwieństwie do tabel zewnętrznych upuszczenie tabeli wewnętrznej powoduje usunięcie również danych źródłowych.

1. Aby zapisać plik, użyj **klawisza Ctrl**+**X**, a następnie wprowadź **Y**, a na koniec **Wprowadź**.

1. Użyj następujących czynności, aby uruchomić plik przy użyciu beeline:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > Parametr `-i` uruchamia Beeline i uruchamia instrukcje w `query.hql` pliku. Po zakończeniu kwerendy, można `jdbc:hive2://headnodehost:10001/>` dotrzeć do monitu. Można również uruchomić plik `-f` przy użyciu parametru, który kończy beeline po zakończeniu kwerendy.

1. Aby sprawdzić, czy została utworzona tabela **errorLogs,** użyj następującej instrukcji, aby zwrócić wszystkie wiersze z **errorLogs:**

    ```hiveql
    SELECT * from errorLogs;
    ```

    Należy zwrócić trzy wiersze danych, wszystkie zawierające **[BŁĄD]** w kolumnie t4:

        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (0.813 seconds)

## <a name="install-beeline-client"></a>Zainstaluj klienta beeline

Mimo że Beeline znajduje się w węzłach głównego, można zainstalować ją lokalnie.  Kroki instalacji komputera lokalnego są oparte na [podsystemie Windows dla systemu Linux](https://docs.microsoft.com/windows/wsl/install-win10).

1. Aktualizowanie list pakietów. Wprowadź następujące polecenie w powłoce basha:

    ```bash
    sudo apt-get update
    ```

1. Zainstaluj javę, jeśli nie jest zainstalowana. Można sprawdzić za `which java` pomocą polecenia.

    1. Jeśli pakiet java nie jest zainstalowany, wprowadź następujące polecenie:

        ```bash
        sudo apt install openjdk-11-jre-headless
        ```

    1. Otwórz plik bashrc (często spotykany w `nano ~/.bashrc`~/.bashrc): .

    1. Zmień plik bashrc. Dodaj następujący wiersz na końcu pliku:

        ```bash
        export JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64
        ```

        Następnie naciśnij **klawisze Ctrl+X**, a następnie **klawisz Y**, a następnie wprowadź.

1. Pobierz archiwa Hadoop i Beeline, wprowadź następujące polecenia:

    ```bash
    wget https://archive.apache.org/dist/hadoop/core/hadoop-2.7.3/hadoop-2.7.3.tar.gz
    wget https://archive.apache.org/dist/hive/hive-1.2.1/apache-hive-1.2.1-bin.tar.gz
    ```

1. Rozpakuj archiwa, wprowadź następujące polecenia:

    ```bash
    tar -xvzf hadoop-2.7.3.tar.gz
    tar -xvzf apache-hive-1.2.1-bin.tar.gz
    ```

1. Dalsza zmiana pliku bashrc. Musisz zidentyfikować ścieżkę do miejsca, w którym zostały rozpakowane archiwa. Jeśli używasz [podsystemu Windows dla Systemu Linux](https://docs.microsoft.com/windows/wsl/install-win10), a `/mnt/c/Users/user/`następnie `user` dokładnie kroki, ścieżka będzie , gdzie jest nazwa użytkownika.

    1. Otwórz plik:`nano ~/.bashrc`

    1. Zmodyfikuj poniższe polecenia za pomocą odpowiedniej ścieżki, a następnie wprowadź je na końcu pliku bashrc:

        ```bash
        export HADOOP_HOME=/path_where_the_archives_were_unpacked/hadoop-2.7.3
        export HIVE_HOME=/path_where_the_archives_were_unpacked/apache-hive-1.2.1-bin
        PATH=$PATH:$HIVE_HOME/bin
        ```

    1. Następnie naciśnij **klawisze Ctrl+X**, a następnie **klawisz Y**, a następnie wprowadź.

1. Zamknij, a następnie ponownie otwórz sesję bash.

1. Przetestuj połączenie. Użyj formatu połączenia z [ponad publicznych lub prywatnych punktów końcowych](#over-public-or-private-endpoints)powyżej.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać bardziej ogólne informacje na temat hive w hdinsight, zobacz [Używanie ula Apache z Apache Hadoop na HDInsight](hdinsight-use-hive.md)

* Aby uzyskać więcej informacji na temat innych sposobów pracy z Hadoop na HDInsight, zobacz [Korzystanie MapReduce z Apache Hadoop na HDInsight](hdinsight-use-mapreduce.md)
