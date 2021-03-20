---
title: Apache Sqoop z usługą Apache Hadoop — Azure HDInsight
description: Dowiedz się, jak używać oprogramowania Apache Sqoop do importowania i eksportowania między Apache Hadoop w usłudze HDInsight i Azure SQL Database.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/28/2019
ms.openlocfilehash: 2d0d38dee15817e56c2784981365ea331b6a8459
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943148"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-azure-sql-database"></a>Za pomocą oprogramowania Apache Sqoop można importować i eksportować dane między Apache Hadoop w usłudze HDInsight i Azure SQL Database

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Dowiedz się, jak używać platformy Apache Sqoop do importowania i eksportowania między klastrem Apache Hadoop w usłudze Azure HDInsight i Azure SQL Database lub Microsoft SQL Server. Kroki opisane w tym dokumencie używają `sqoop` polecenia bezpośrednio z węzła głównego klastra Hadoop. Używasz protokołu SSH do nawiązywania połączenia z węzłem głównym i uruchamiania poleceń z tego dokumentu. Ten artykuł jest kontynuacją [używania platformy Apache Sqoop z usługą Hadoop w usłudze HDInsight](./hdinsight-use-sqoop.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Zakończenie [konfigurowania środowiska testowego](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) z [używania platformy Apache Sqoop z usługą Hadoop w usłudze HDInsight](./hdinsight-use-sqoop.md).

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Znajomość Sqoop. Aby uzyskać więcej informacji, zobacz [Podręcznik użytkownika Sqoop](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="set-up"></a>Konfiguruj

1. Użyj [polecenia SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) do nawiązania połączenia z klastrem. Edytuj poniższe polecenie, zastępując wartość CLUSTERname nazwą klastra, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Aby ułatwić użycie, Ustaw zmienne. Zastąp `PASSWORD` `MYSQLSERVER` wartości, i `MYDATABASE` z odpowiednimi wartościami, a następnie wprowadź poniższe polecenia:

    ```bash
    export password='PASSWORD'
    export sqlserver="MYSQLSERVER"
    export database="MYDATABASE"


    export serverConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password"
    export serverDbConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password;database=$database"
    ```

## <a name="sqoop-export"></a>Eksport Sqoop

Z usługi Hive do bazy danych SQL.

1. Aby sprawdzić, czy Sqoop może zobaczyć swoją bazę danych, wprowadź poniższe polecenie w otwartym połączeniu SSH. To polecenie zwraca listę baz danych.

    ```bash
    sqoop list-databases --connect $serverConnect
    ```

1. Wprowadź następujące polecenie, aby wyświetlić listę tabel dla określonej bazy danych:

    ```bash
    sqoop list-tables --connect $serverDbConnect
    ```

1. Aby wyeksportować dane z tabeli programu Hive `hivesampletable` do `mobiledata` tabeli w bazie danych, wprowadź poniższe polecenie w otwartym połączeniu SSH:

    ```bash
    sqoop export --connect $serverDbConnect \
    -table mobiledata \
    --hcatalog-table hivesampletable
    ```

1. Aby sprawdzić, czy dane zostały wyeksportowane, użyj następujących zapytań z połączenia SSH, aby wyświetlić wyeksportowane dane:

    ```bash
    sqoop eval --connect $serverDbConnect \
    --query "SELECT COUNT(*) from dbo.mobiledata WITH (NOLOCK)"


    sqoop eval --connect $serverDbConnect \
    --query "SELECT TOP(10) * from dbo.mobiledata WITH (NOLOCK)"
    ```

## <a name="sqoop-import"></a>Sqoop import

Z usługi SQL do magazynu Azure Storage.

1. Wprowadź poniższe polecenie w otwartym połączeniu SSH, aby zaimportować dane z `mobiledata` tabeli w programie SQL do katalogu w usłudze `wasbs:///tutorials/usesqoop/importeddata` HDInsight. Pola w danych są oddzielane znakami tabulacji, a wiersze kończą się znakiem nowego wiersza.

    ```bash
    sqoop import --connect $serverDbConnect \
    --table mobiledata \
    --target-dir 'wasb:///tutorials/usesqoop/importeddata' \
    --fields-terminated-by '\t' \
    --lines-terminated-by '\n' -m 1
    ```

1. Alternatywnie można również określić tabelę programu Hive:

    ```bash
    sqoop import --connect $serverDbConnect \
    --table mobiledata \
    --target-dir 'wasb:///tutorials/usesqoop/importeddata2' \
    --fields-terminated-by '\t' \
    --lines-terminated-by '\n' \
    --create-hive-table \
    --hive-table mobiledata_imported2 \
    --hive-import -m 1
    ```

1. Po zakończeniu importowania wprowadź następujące polecenie w otwartym połączeniu SSH, aby wyświetlić listę danych w nowym katalogu:

    ```bash
    hadoop fs -tail /tutorials/usesqoop/importeddata/part-m-00000
    ```

1. Użyj [z usługi Beeline](./apache-hadoop-use-hive-beeline.md) , aby sprawdzić, czy tabela została utworzona w usłudze Hive.

    1. Nawiązywanie połączenia

        ```bash
        beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
        ```

    1. Wykonaj wszystkie zapytania poniżej jednej i Przejrzyj dane wyjściowe:

        ```hql
        show tables;
        describe mobiledata_imported2;
        SELECT COUNT(*) FROM mobiledata_imported2;
        SELECT * FROM mobiledata_imported2 LIMIT 10;
        ```

    1. Zakończ z usługi Beeline z `!exit` .

## <a name="limitations"></a>Ograniczenia

* Eksport zbiorczy — za pomocą usługi HDInsight opartej na systemie Linux łącznik Sqoop używany do eksportowania danych do programu SQL nie obsługuje operacji wstawiania zbiorczego.

* Przetwarzanie wsadowe — za pomocą usługi HDInsight opartej na systemie Linux, gdy podczas wykonywania operacji wstawiania jest używany `-batch` przełącznik, Sqoop tworzy wiele wstawek zamiast wsadowych operacje wstawiania.

## <a name="important-considerations"></a>Istotne zagadnienia

* Zarówno Usługa HDInsight, jak i SQL Server muszą znajdować się na tym samym Virtual Network platformy Azure.

    Aby zapoznać się z przykładem, zobacz temat [łączenie usługi HDInsight z dokumentem w sieci lokalnej](./../connect-on-premises-network.md) .

    Aby uzyskać więcej informacji na temat używania usługi HDInsight z usługą Azure Virtual Network, zobacz dokument o [rozszerzonej usłudze HDInsight z platformą azure Virtual Network](../hdinsight-plan-virtual-network-deployment.md) . Aby uzyskać więcej informacji na temat usługi Azure Virtual Network, zobacz dokument [omówienie Virtual Network](../../virtual-network/virtual-networks-overview.md) .

* SQL Server musi być skonfigurowany tak, aby zezwalał na uwierzytelnianie SQL. Aby uzyskać więcej informacji, zapoznaj się z dokumentem [Wybieranie trybu uwierzytelniania](/sql/relational-databases/security/choose-an-authentication-mode) .

* Może być konieczne skonfigurowanie SQL Server w celu akceptowania połączeń zdalnych. Aby uzyskać więcej informacji, zobacz artykuł [Jak rozwiązywać problemy z nawiązywaniem połączenia z programem SQL Server Database Engine](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) .

## <a name="next-steps"></a>Następne kroki

Teraz wiesz już, jak używać programu Sqoop. Aby dowiedzieć się więcej, zobacz:

* [Korzystanie z platformy Apache Oozie z usługą HDInsight](../hdinsight-use-oozie-linux-mac.md): Użyj akcji Sqoop w przepływie pracy Oozie.
* [Analizowanie danych dotyczących opóźnień lotów przy użyciu usługi HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md): Użyj interakcyjnego zapytania, aby analizować dane opóźnień lotów, a następnie używać Sqoop do eksportowania danych do bazy danych na platformie Azure.
* [Przekazywanie danych do usługi HDInsight](../hdinsight-upload-data.md): Znajdź inne metody przekazywania danych do magazynu HDInsight/Azure Blob Storage.