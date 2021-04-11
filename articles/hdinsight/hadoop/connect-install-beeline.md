---
title: Nawiązywanie połączenia z usługą Apache Z usługi Beeline — Azure HDInsight lub instalowanie jej
description: Dowiedz się, jak nawiązać połączenie z klientem Apache Z usługi Beeline w celu uruchamiania zapytań Hive z usługą Hadoop w usłudze HDInsight. Z usługi Beeline to narzędzie do pracy z serwera hiveserver2em przez JDBC.
ms.service: hdinsight
ms.topic: how-to
ms.custom: contperf-fy21q1
ms.date: 04/07/2021
ms.openlocfilehash: 5dcb6168a263be11410126ff08bd8b015da5af46
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103440"
---
# <a name="connect-to-apache-beeline-on-hdinsight-or-install-it-locally"></a>Nawiązywanie połączenia z usługą Apache Z usługi Beeline w usłudze HDInsight lub instalowanie jej lokalnie

[Apache z usługi Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) to klient programu Hive uwzględniony w węzłach głównych klastra usługi HDInsight. W tym artykule opisano sposób nawiązywania połączenia z klientem Z usługi Beeline zainstalowanym w klastrze usługi HDInsight w różnych typach połączeń. Omówiono również sposób [lokalnego instalowania klienta programu z usługi Beeline](#install-beeline-client). 

## <a name="types-of-connections"></a>Typy połączeń

### <a name="from-an-ssh-session"></a>Z sesji SSH

Podczas nawiązywania połączenia z sesji SSH z klastrem węzła głównego można połączyć się z `headnodehost` adresem na porcie `10001` :

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

### <a name="over-an-azure-virtual-network"></a>Za pośrednictwem Virtual Network platformy Azure

Podczas nawiązywania połączenia z klientem z usługą HDInsight za pośrednictwem usługi Azure Virtual Network należy podać w pełni kwalifikowaną nazwę domeny (FQDN) węzła głównego klastra. Ponieważ to połączenie jest nawiązywane bezpośrednio z węzłami klastra, połączenie używa portu `10001` :

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Zamień na w `<headnode-FQDN>` pełni kwalifikowaną nazwę domeny węzła głównego klastra. Aby znaleźć w pełni kwalifikowaną nazwę domeny węzła głównego, Skorzystaj z informacji w temacie [Zarządzanie usługą HDInsight przy użyciu dokumentu interfejsu API REST usługi Apache Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes) .

### <a name="to-hdinsight-enterprise-security-package-esp-cluster-using-kerberos"></a>Do klastra usługi HDInsight pakiet Enterprise Security (ESP) przy użyciu protokołu Kerberos

Podczas nawiązywania połączenia z klientem z klastrem pakiet Enterprise Security (ESP) przyłączonym do Azure Active Directory (AAD) — DS na komputerze w tym samym obszarze klastra należy również określić nazwę domeny `<AAD-Domain>` i nazwę konta użytkownika domeny z uprawnieniami dostępu do klastra `<username>` :

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

Zamień na `<username>` nazwę konta w domenie z uprawnieniami dostępu do klastra. Zamień na `<AAD-DOMAIN>` nazwę Azure Active Directory (AAD), do której jest dołączony klaster. Użyj ciągu z wielką literą dla `<AAD-DOMAIN>` wartości, w przeciwnym razie nie zostanie znalezione poświadczenie. `/etc/krb5.conf`W razie potrzeby Sprawdź nazwy obszarów.

Aby znaleźć adres URL JDBC z Ambari:

1. W przeglądarce sieci Web przejdź do `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary` lokalizacji, gdzie `CLUSTERNAME` jest nazwą klastra. Upewnij się, że serwera hiveserver2 jest uruchomiony.

1. Użyj schowka, aby skopiować adres URL serwera hiveserver2 JDBC.

### <a name="over-public-or-private-endpoints"></a>Za pośrednictwem publicznych lub prywatnych punktów końcowych

Podczas nawiązywania połączenia z klastrem przy użyciu publicznych lub prywatnych punktów końcowych należy podać nazwę konta logowania klastra (ustawienie domyślne `admin` ) i hasło. Na przykład przy użyciu Z usługi Beeline z systemu klienckiego do nawiązywania połączenia z `clustername.azurehdinsight.net` adresem. To połączenie jest nawiązywane za pośrednictwem portu `443` i szyfrowane przy użyciu protokołu TLS/SSL.

Element `clustername` należy zastąpić nazwą klastra usługi HDInsight. Zamień na `admin` konto logowania klastra dla klastra. W przypadku klastrów ESP Użyj pełnej nazwy UPN (na przykład user@domain.com ). Zamień na `password` hasło dla konta logowania klastra.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

lub dla prywatnego punktu końcowego:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

Prywatne punkty końcowe wskazują podstawowy moduł równoważenia obciążenia, do którego można uzyskać dostęp tylko z sieci wirtualnych komunikacji równorzędnej w tym samym regionie. Aby uzyskać więcej informacji [, zobacz ograniczenia dotyczące globalnej komunikacji równorzędnej sieci wirtualnej i modułów równoważenia obciążenia](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) . Możesz użyć `curl` polecenia z opcją, `-v` Aby rozwiązać problemy z łącznością z publicznymi lub prywatnymi punktami końcowymi przed użyciem z usługi Beeline.

### <a name="use-beeline-with-apache-spark"></a>Użyj Z usługi Beeline z Apache Spark

Apache Spark udostępnia własną implementację serwera hiveserver2, która jest czasami określana jako serwer Spark Thrift. Ta usługa używa platformy Spark SQL do rozpoznawania zapytań zamiast Hive. I może zapewnić lepszą wydajność w zależności od zapytania.

#### <a name="through-public-or-private-endpoints"></a>Za poorednictwem publicznych lub prywatnych punktów końcowych

Użyte parametry połączenia są nieco inne. Zamiast z `httpPath=/hive2` niego korzystają `httpPath/sparkhive2` . Element `clustername` należy zastąpić nazwą klastra usługi HDInsight. Zamień na `admin` konto logowania klastra dla klastra. Zamień na `password` hasło dla konta logowania klastra.
> [!NOTE]
> W przypadku klastrów ESP Zamień `admin` na pełną nazwę UPN (na przykład user@domain.com ). 

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

lub dla prywatnego punktu końcowego:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

Prywatne punkty końcowe wskazują podstawowy moduł równoważenia obciążenia, do którego można uzyskać dostęp tylko z sieci wirtualnych komunikacji równorzędnej w tym samym regionie. Aby uzyskać więcej informacji [, zobacz ograniczenia dotyczące globalnej komunikacji równorzędnej sieci wirtualnej i modułów równoważenia obciążenia](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) . Możesz użyć `curl` polecenia z opcją, `-v` Aby rozwiązać problemy z łącznością z publicznymi lub prywatnymi punktami końcowymi przed użyciem z usługi Beeline.

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>Z poziomu węzła klastra lub wewnątrz Virtual Network platformy Azure z Apache Spark

W przypadku łączenia się bezpośrednio z węzłem głównym klastra lub z zasobu w ramach tego samego Virtual Network platformy Azure jako klastra usługi HDInsight port `10002` powinien być używany dla serwera Spark Thrift zamiast systemu `10001` . Poniższy przykład pokazuje, jak połączyć się bezpośrednio z węzłem głównym:

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

## <a name="install-beeline-client"></a>Zainstaluj klienta Z usługi Beeline

Mimo że usługa Z usługi Beeline jest dołączana do węzłów głównych, warto zainstalować ją lokalnie.  Kroki instalacji dla komputera lokalnego są oparte na [podsystemie Windows dla systemu Linux](/windows/wsl/install-win10).

1. Aktualizowanie list pakietów. Wprowadź następujące polecenie w powłoce bash:

    ```bash
    sudo apt-get update
    ```

1. Zainstaluj język Java, jeśli nie jest zainstalowany. Możesz sprawdzić za pomocą `which java` polecenia.

    1. Jeśli nie zainstalowano żadnego pakietu Java, wprowadź następujące polecenie:

        ```bash
        sudo apt install openjdk-11-jre-headless
        ```

    1. Otwórz plik BASHRC (często znaleziono w ~/.bashrc.): `nano ~/.bashrc` .

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

1. Dalsze zmiany pliku bashrc. Należy określić ścieżkę, w której archiwa zostały rozpakowane. W przypadku korzystania z [podsystemu Windows dla systemu Linux](/windows/wsl/install-win10), jeśli wykonano kroki dokładnie, ścieżką będzie `/mnt/c/Users/user/` , gdzie `user` to nazwa użytkownika.

    1. Otwórz plik: `nano ~/.bashrc`

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

* Aby zapoznać się z przykładami dotyczącymi używania klienta Z usługi Beeline z Apache Hive, zobacz [Korzystanie z platformy Apache z usługi Beeline z usługą Apache Hive](apache-hadoop-use-hive-beeline.md)
* Aby uzyskać więcej ogólnych informacji na temat platformy Hive w usłudze HDInsight, zobacz [używanie Apache Hive z usługą Apache Hadoop w usłudze HDInsight](hdinsight-use-hive.md)
