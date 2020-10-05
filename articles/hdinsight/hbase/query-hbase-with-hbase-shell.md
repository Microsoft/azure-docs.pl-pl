---
title: 'Szybki Start: Kwerenda Apache HBase w usłudze Azure HDInsight — powłoka HBase'
description: W tym przewodniku szybki start dowiesz się, jak używać powłoki Apache HBase do uruchamiania zapytań Apache HBase.
keywords: HDInsight, Hadoop, HBase
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: 572262cbece26171f9a67bf073906fa2dfd4d8e1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "79371073"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-hbase-shell"></a>Szybki Start: Kwerenda Apache HBase w usłudze Azure HDInsight przy użyciu powłoki HBase

W tym przewodniku szybki start dowiesz się, jak za pomocą powłoki Apache HBase utworzyć tabelę HBase, wstawić dane, a następnie zbadać tabelę.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache HBase. Zobacz [Tworzenie klastra](../hadoop/apache-hadoop-linux-tutorial-get-started.md) , aby utworzyć klaster usługi HDInsight.  Upewnij się, że wybrano typ klastra **HBase** .

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-a-table-and-manipulate-data"></a>Tworzenie tabeli i manipulowanie danymi

Dla większości użytkowników dane są wyświetlane w formacie tabelarycznym:

![Dane tabelaryczne usługi HDInsight Apache HBase](./media/query-hbase-with-hbase-shell/hdinsight-hbase-contacts-tabular.png)

W HBase (implementacja BigTable w [chmurze](https://cloud.google.com/bigtable/)) te same dane wyglądają następująco:

![Dane usługi HDInsight Apache HBase BigTable](./media/query-hbase-with-hbase-shell/hdinsight-hbase-contacts-bigtable.png)

Za pomocą protokołu SSH można łączyć się z klastrami HBase, a następnie używać powłoki Apache HBase do tworzenia tabel HBase, wstawiania danych i wykonywania zapytań dotyczących danych.

1. Użyj `ssh` polecenia, aby nawiązać połączenie z klastrem HBase. Edytuj poniższe polecenie, zastępując je `CLUSTERNAME` nazwą klastra, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Użyj `hbase shell` polecenia, aby uruchomić powłokę interaktywną HBase. Wprowadź następujące polecenie w połączeniu SSH:

    ```bash
    hbase shell
    ```

3. Użyj `create` polecenia, aby utworzyć tabelę HBase z rodziną dwóch kolumn. Wprowadź następujące polecenie:

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. Użyj `list` polecenia, aby wyświetlić listę wszystkich tabel w HBase. Wprowadź następujące polecenie:

    ```hbase
    list
    ```

5. Użyj `put` polecenia, aby wstawić wartości w określonej kolumnie w określonym wierszu w określonej tabeli. Wprowadź następujące polecenie:

    ```hbase
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

6. Użyj `scan` polecenia, aby przeskanować i zwrócić `Contacts` dane tabeli. Wprowadź następujące polecenie:

    ```hbase
    scan 'Contacts'
    ```

7. Użyj `get` polecenia, aby pobrać zawartość wiersza. Wprowadź następujące polecenie:

    ```hbase
    get 'Contacts', '1000'
    ```

    Wyniki są wyświetlane podobnie jak przy użyciu `scan` polecenia, ponieważ istnieje tylko jeden wiersz.

8. Użyj `delete` polecenia, aby usunąć wartość komórki z tabeli. Wprowadź następujące polecenie:

    ```hbase
    delete 'Contacts', '1000', 'Office:Address'
    ```

9. Użyj `disable` polecenia, aby wyłączyć tabelę. Wprowadź następujące polecenie:

    ```hbase
    disable 'Contacts'
    ```

10. Użyj `drop` polecenia, aby usunąć tabelę z HBase. Wprowadź następujące polecenie:

    ```hbase
    drop 'Contacts'
    ```

11. Użyj `exit` polecenia, aby zatrzymać powłokę interaktywną HBase. Wprowadź następujące polecenie:

    ```hbase
    exit
    ```

Aby uzyskać więcej informacji na temat schematu tabeli HBase, zobacz [wprowadzenie do projektu schematu Apache HBase](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf). Więcej poleceń bazy danych HBase można znaleźć w [Podręczniku bazy danych Apache HBase](https://hbase.apache.org/book.html#quickstart).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu przewodnika Szybki Start możesz chcieć usunąć klaster. Dzięki usłudze HDInsight dane są przechowywane w usłudze Azure Storage, więc można bezpiecznie usunąć klaster, gdy nie jest używany. Opłaty za klaster usługi HDInsight są naliczane nawet wtedy, gdy nie jest używany. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ze względów ekonomicznych warto usuwać klastry, gdy nie są używane.

Aby usunąć klaster, zobacz [usuwanie klastra usługi HDInsight przy użyciu przeglądarki, programu PowerShell lub interfejsu wiersza polecenia platformy Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start pokazano, jak za pomocą powłoki Apache HBase utworzyć tabelę HBase, wstawić dane, a następnie zbadać tabelę. Aby dowiedzieć się więcej na temat danych przechowywanych w HBase, w następnym artykule przedstawiono sposób wykonywania zapytań przy użyciu Apache Spark.

> [!div class="nextstepaction"]
> [Odczytywanie i zapisywanie danych w bazie danych Apache HBase za pomocą platformy Apache Spark](../hdinsight-using-spark-query-hbase.md)