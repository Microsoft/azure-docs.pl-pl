---
title: 'Szybki Start: Apache HBase & Apache Phoenix — Azure HDInsight'
description: W tym przewodniku szybki start dowiesz się, jak używać Apache Phoenix w usłudze HDInsight. Ponadto Dowiedz się, jak zainstalować i skonfigurować element SqlLine na komputerze, aby połączyć się z klastrem HBase w usłudze HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 06/12/2019
ms.openlocfilehash: 6b9196d9ad5cf07cd210726bc1af7121c08094a8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98935995"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-apache-phoenix"></a>Szybki Start: Kwerenda Apache HBase w usłudze Azure HDInsight z Apache Phoenix

W tym przewodniku szybki start dowiesz się, jak używać Apache Phoenix do uruchamiania zapytań HBase w usłudze Azure HDInsight. Apache Phoenix jest aparatem zapytań SQL dla platformy Apache HBase. Jest on dostępny jako sterownik JDBC i umożliwia wykonywanie zapytań i zarządzanie tabelami HBase przy użyciu języka SQL. [SqlLine](http://sqlline.sourceforge.net/) to narzędzie wiersza polecenia do wykonywania instrukcji SQL.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache HBase. Zobacz [Tworzenie klastra](../hadoop/apache-hadoop-linux-tutorial-get-started.md) , aby utworzyć klaster usługi HDInsight.  Upewnij się, że wybrano typ klastra **HBase** .

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="identify-a-zookeeper-node"></a>Zidentyfikuj węzeł dozorcy

Po nawiązaniu połączenia z klastrem HBase należy nawiązać połączenie z jednym z węzłów Apache ZooKeeper. Każdy klaster usługi HDInsight ma trzy węzły dozorcy. Zwinięcie może służyć do szybkiej identyfikacji węzła dozorcy. Edytuj poniższe polecenie `PASSWORD` , zastępując i `CLUSTERNAME` przy użyciu odpowiednich wartości, a następnie wprowadź polecenie w wierszu polecenia:

```cmd
curl -u admin:PASSWORD -sS -G https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER
```

Część danych wyjściowych będzie wyglądać podobnie do:

```output
    {
      "href" : "http://hn1-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net:8080/api/v1/clusters/myCluster/hosts/zk0-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net/host_components/ZOOKEEPER_SERVER",
      "HostRoles" : {
        "cluster_name" : "myCluster",
        "component_name" : "ZOOKEEPER_SERVER",
        "host_name" : "zk0-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net"
      }
```

Zwróć uwagę na wartość do `host_name` użycia w przyszłości.

## <a name="create-a-table-and-manipulate-data"></a>Tworzenie tabeli i manipulowanie danymi

Do łączenia się z klastrami HBase można użyć protokołu SSH, a następnie użyć Apache Phoenix do tworzenia tabel HBase, wstawiania danych i wykonywania zapytań dotyczących danych.

1. Użyj `ssh` polecenia, aby nawiązać połączenie z klastrem HBase. Edytuj poniższe polecenie, zastępując je `CLUSTERNAME` nazwą klastra, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Zmień katalog na klienta w Phoenix. Wprowadź następujące polecenie:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

3. Uruchom [SqlLine](http://sqlline.sourceforge.net/). Edytuj poniższe polecenie `ZOOKEEPER` , zastępując węzeł dozorcy zidentyfikowany wcześniej, a następnie wprowadź polecenie:

    ```bash
    ./sqlline.py ZOOKEEPER:2181:/hbase-unsecure
    ```

4. Utwórz tabelę HBase. Wprowadź następujące polecenie:

    ```sql
    CREATE TABLE Company (company_id INTEGER PRIMARY KEY, name VARCHAR(225));
    ```

5. Użyj polecenia SqlLine, `!tables` Aby wyświetlić listę wszystkich tabel w HBase. Wprowadź następujące polecenie:

    ```sqlline
    !tables
    ```

6. Wstaw wartości w tabeli. Wprowadź następujące polecenie:

    ```sql
    UPSERT INTO Company VALUES(1, 'Microsoft');
    UPSERT INTO Company VALUES(2, 'Apache');
    ```

7. Zbadaj tabelę. Wprowadź następujące polecenie:

    ```sql
    SELECT * FROM Company;
    ```

8. Usuń rekord. Wprowadź następujące polecenie:

    ```sql
    DELETE FROM Company WHERE COMPANY_ID=1;
    ```

9. Usuń tabelę. Wprowadź następujące polecenie:

    ```hbase
    DROP TABLE Company;
    ```

10. Użyj polecenia SqlLine, `!quit` Aby zakończyć działanie SqlLine. Wprowadź następujące polecenie:

    ```sqlline
    !quit
    ```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu przewodnika Szybki Start możesz chcieć usunąć klaster. Dzięki usłudze HDInsight dane są przechowywane w usłudze Azure Storage, więc można bezpiecznie usunąć klaster, gdy nie jest używany. Opłaty za klaster usługi HDInsight są naliczane nawet wtedy, gdy nie jest używany. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ze względów ekonomicznych warto usuwać klastry, gdy nie są używane.

Aby usunąć klaster, zobacz [usuwanie klastra usługi HDInsight przy użyciu przeglądarki, programu PowerShell lub interfejsu wiersza polecenia platformy Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób używania Apache Phoenix do uruchamiania zapytań HBase w usłudze Azure HDInsight. Aby dowiedzieć się więcej na temat Apache Phoenix, Następny artykuł zapewni dokładniejszą analizę.

> [!div class="nextstepaction"]
> [Oprogramowanie Apache Phoenix w usłudze HDInsight](../hdinsight-phoenix-in-hdinsight.md)
