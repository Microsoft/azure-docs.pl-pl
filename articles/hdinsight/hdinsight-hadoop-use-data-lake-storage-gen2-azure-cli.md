---
title: Tworzenie usługi Azure HDInsight — Azure Data Lake Storage Gen2 — interfejs wiersza polecenia platformy Azure
description: Dowiedz się, jak używać Azure Data Lake Storage Gen2 z klastrami usługi Azure HDInsight przy użyciu interfejsu wiersza polecenia platformy Azure.
author: guyhay
ms.author: guyhay
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020, devx-track-azurecli
ms.date: 09/17/2020
ms.openlocfilehash: bbc1cd27d5c16eddd3aaad748c34445e5017e209
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98945503"
---
# <a name="create-a-cluster-with-data-lake-storage-gen2-using-azure-cli"></a>Tworzenie klastra z Data Lake Storage Gen2 przy użyciu interfejsu wiersza polecenia platformy Azure

Aby utworzyć klaster usługi HDInsight, który używa Data Lake Storage Gen2 dla magazynu, wykonaj następujące kroki.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz Azure Data Lake Storage Gen2, zapoznaj się z [sekcją przegląd](hdinsight-hadoop-use-data-lake-storage-gen2.md). 
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Aby uruchomić przykłady skryptów interfejsu wiersza polecenia, można korzystać z trzech opcji:
    - Użyj [Azure Cloud Shell](../cloud-shell/overview.md) z Azure Portal (zobacz następną sekcję).
    - Użyj osadzonego Azure Cloud Shell za pomocą przycisku "Wypróbuj go" znajdującego się w prawym górnym rogu każdego bloku kodu.
    - [Zainstaluj najnowszą wersję interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) (2.0.13 lub nowsza), jeśli wolisz korzystać z lokalnej konsoli interfejsu wiersza polecenia. Zaloguj się do platformy Azure przy użyciu `az login` konta skojarzonego z subskrypcją platformy Azure, w ramach której chcesz wdrożyć tożsamość zarządzaną przypisaną przez użytkownika. Interfejs wiersza polecenia platformy Azure. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Możesz [pobrać przykładowy plik szablonu](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) i [pobrać przykładowy plik parametrów](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json). Przed użyciem szablonu i poniższego fragmentu kodu interfejsu wiersza polecenia platformy Azure Zastąp następujące symbole zastępcze odpowiednimi wartościami:

| Symbol zastępczy | Opis |
|---|---|
| `<SUBSCRIPTION_ID>` | Identyfikator subskrypcji platformy Azure |
| `<RESOURCEGROUPNAME>` | Grupa zasobów, w której ma zostać utworzony nowy klaster i konto magazynu. |
| `<MANAGEDIDENTITYNAME>` | Nazwa tożsamości zarządzanej, która będzie mieć uprawnienia na koncie magazynu z Azure Data Lake Storage Gen2. |
| `<STORAGEACCOUNTNAME>` | Nowe konto magazynu z Azure Data Lake Storage Gen2, które zostanie utworzone. |
| `<FILESYSTEMNAME>`  | Nazwa systemu plików, który ma być używany przez ten klaster na koncie magazynu. |
| `<CLUSTERNAME>` | Nazwa klastra usługi HDInsight. |
| `<PASSWORD>` | Wybrane hasło do logowania się do klastra przy użyciu protokołu SSH i pulpitu nawigacyjnego Ambari. |

Poniższy fragment kodu wykonuje następujące czynności wstępne:

1. Loguje się do konta platformy Azure.
1. Ustawia aktywną subskrypcję, w której będą wykonywane operacje tworzenia.
1. Tworzy nową grupę zasobów dla nowych działań wdrożenia.
1. Tworzy tożsamość zarządzaną przypisaną przez użytkownika.
1. Dodaje rozszerzenie interfejsu wiersza polecenia platformy Azure w celu używania funkcji dla Data Lake Storage Gen2.
1. Tworzy nowe konto magazynu z Data Lake Storage Gen2 przy użyciu `--hierarchical-namespace true` flagi.

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus

# Create managed identity
az identity create -g <RESOURCEGROUPNAME> -n <MANAGEDIDENTITYNAME>

az extension add --name storage-preview

az storage account create --name <STORAGEACCOUNTNAME> \
    --resource-group <RESOURCEGROUPNAME> \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

Następnie zaloguj się do portalu. Dodaj nową tożsamość zarządzaną przez użytkownika do roli **współautor danych obiektów blob magazynu** na koncie magazynu. Ten krok jest opisany w kroku 3 w sekcji [Korzystanie z Azure Portal](hdinsight-hadoop-use-data-lake-storage-gen2.md).

 > [!IMPORTANT]
 > Upewnij się, że konto magazynu ma tożsamość przypisaną przez użytkownika z uprawnieniami roli **współautor danych obiektów BLOB** , w przeciwnym razie Tworzenie klastra zakończy się niepowodzeniem.

```azurecli
az deployment group create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po ukończeniu korzystania z artykułu warto usunąć klaster. Dzięki usłudze HDInsight dane są przechowywane w usłudze Azure Storage, dzięki czemu można bezpiecznie usunąć klaster, gdy nie jest używany. Opłaty są naliczone również za klaster usługi HDInsight, nawet wtedy, gdy nie jest używany. Ze względu na to, że opłaty za klaster są dużo razy większe niż opłaty za magazyn, sprawia to, że należy usunąć klastry, gdy nie są używane.

Wprowadź wszystkie lub niektóre z następujących poleceń, aby usunąć zasoby:

```azurecli-interactive
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $AZURE_STORAGE_ACCOUNT \
    --name $AZURE_STORAGE_CONTAINER

# Remove storage account
az storage account delete \
    --name $AZURE_STORAGE_ACCOUNT \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="troubleshoot"></a>Rozwiązywanie problemów

W razie problemów podczas tworzenia klastrów usługi HDInsight zapoznaj się z [wymaganiami dotyczącymi kontroli dostępu](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Następne kroki

Klaster usługi HDInsight został pomyślnie utworzony. Teraz Dowiedz się, jak korzystać z klastra.

### <a name="apache-spark-clusters"></a>Klastry Apache Spark

* [Dostosowywanie klastrów usługi HDInsight opartych na systemie Linux za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md)
* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](spark/apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu programu Apache Livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark z usługą BI: wykonywanie interaktywnej analizy danych przy użyciu platformy Spark w usłudze HDInsight przy użyciu narzędzi analizy biznesowej](spark/apache-spark-use-bi-tools.md)
* [Apache Spark z Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do przewidywania wyników inspekcji żywności](spark/apache-spark-machine-learning-mllib-ipython.md)

### <a name="apache-hadoop-clusters"></a>Klastry Apache Hadoop

* [Korzystanie z programu Apache Hive z usługą HDInsight](hadoop/hdinsight-use-hive.md)
* [Korzystanie z MapReduce z usługą HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Klastry Apache HBase

* [Wprowadzenie do platformy Apache HBase w usłudze HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Tworzenie aplikacji Java dla platformy Apache HBase w usłudze HDInsight](hbase/apache-hbase-build-java-maven-linux.md)
