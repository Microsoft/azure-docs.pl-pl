---
title: Tworzenie klastrów Apache Hadoop przy użyciu interfejsu wiersza polecenia platformy Azure — Azure HDInsight
description: Dowiedz się, jak tworzyć Azure HDInsight klastrów przy użyciu międzyplatformowego interfejsu wiersza polecenia platformy Azure.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 02/03/2020
ms.openlocfilehash: 9c19eb58e32fec66e5fe698c82133c8583f67b8b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775137"
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>Tworzenie klastrów usługi HDInsight przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Kroki opisane w tym dokumencie dotyczące tworzenia klastra usługi HDInsight 3.6 przy użyciu interfejsu wiersza polecenia platformy Azure.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-a-cluster"></a>Tworzenie klastra

1. Zaloguj się do subskrypcji platformy Azure. Jeśli planujesz używać Azure Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. W innym przypadku wprowadź poniższe polecenie:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Ustaw zmienne środowiskowe. Użycie zmiennych w tym artykule jest oparte na powłoce Bash. W innych środowiskach będą potrzebne niewielkie zmiany. Aby uzyskać pełną listę możliwych parametrów tworzenia klastra, zobacz [az-hdinsight-create.](/cli/azure/hdinsight#az_hdinsight_create)

    |Parametr | Opis |
    |---|---|
    |`--workernode-count`| Liczba węzłów procesu roboczego w klastrze. W tym artykule użyto zmiennej `clusterSizeInNodes` jako wartości przekazanej do `--workernode-count` . |
    |`--version`| Wersja klastra usługi HDInsight. W tym artykule użyto zmiennej `clusterVersion` jako wartości przekazanej do `--version` . Zobacz też: [Obsługiwane wersje hdInsight.](./hdinsight-component-versioning.md#supported-hdinsight-versions)|
    |`--type`| Typ klastra usługi HDInsight, taki jak: hadoop, interactivehive, hbase, kafka, storm, spark, rserver, mlservices.  W tym artykule użyto zmiennej `clusterType` jako wartości przekazanej do `--type` . Zobacz też: [Typy klastrów i konfiguracja](./hdinsight-hadoop-provision-linux-clusters.md#cluster-type).|
    |`--component-version`|Wersje różnych składników usługi Hadoop w wersjach rozdzielonych spacjami w formacie "component=version". W tym artykule użyto zmiennej `componentVersion` jako wartości przekazanej do zmiennej `--component-version` . Zobacz też: [Składniki hadoop.](./hdinsight-component-versioning.md)|

    Zastąp `RESOURCEGROUPNAME` wartości , , , i `LOCATION` `CLUSTERNAME` `STORAGEACCOUNTNAME` `PASSWORD` żądanymi wartościami. Zmień wartości innych zmiennych zgodnie z potrzebami. Następnie wprowadź polecenia interfejsu wiersza polecenia.

    ```azurecli-interactive
    export resourceGroupName=RESOURCEGROUPNAME
    export location=LOCATION
    export clusterName=CLUSTERNAME
    export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME
    export httpCredential='PASSWORD'
    export sshCredentials='PASSWORD'

    export AZURE_STORAGE_CONTAINER=$clusterName
    export clusterSizeInNodes=1
    export clusterVersion=3.6
    export clusterType=hadoop
    export componentVersion=Hadoop=2.7
    ```

3. [Utwórz grupę zasobów,](/cli/azure/group#az_group_create) wprowadzając poniższe polecenie:

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

    Aby uzyskać listę prawidłowych lokalizacji, użyj polecenia , a następnie użyj jednej `az account list-locations` z lokalizacji z wartości `name` .

4. [Utwórz konto usługi Azure Storage,](/cli/azure/storage/account#az_storage_account_create) wprowadzając poniższe polecenie:

    ```azurecli-interactive
    # Note: kind BlobStorage is not available as the default storage account.
    az storage account create \
        --name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

5. [Wyodrębnij klucz podstawowy z konta usługi Azure Storage](/cli/azure/storage/account/keys#az_storage_account_keys_list) i przechowuj go w zmiennej, wprowadzając poniższe polecenie:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. [Utwórz kontener usługi Azure Storage,](/cli/azure/storage/container#az_storage_container_create) wprowadzając poniższe polecenie:

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. [Utwórz klaster usługi HDInsight,](/cli/azure/hdinsight#az_hdinsight_create) wprowadzając następujące polecenie:

    ```azurecli-interactive
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpCredential \
        --http-user admin \
        --location $location \
        --workernode-count $clusterSizeInNodes \
        --ssh-password $sshCredentials \
        --ssh-user sshuser \
        --storage-account $AZURE_STORAGE_ACCOUNT \
        --storage-account-key $AZURE_STORAGE_KEY \
        --storage-container $AZURE_STORAGE_CONTAINER \
        --version $clusterVersion
    ```

    > [!IMPORTANT]  
    > Klastry usługi HDInsight są dostępne w różnych typach, które odpowiadają obciążeniu lub technologii, pod kątem których jest dostosowany klaster. Nie ma obsługiwanej metody tworzenia klastra, który łączy wiele typów, takich jak Storm i HBase w jednym klastrze.

    Ukończenie procesu tworzenia klastra może potrwać kilka minut. Zwykle około 15.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po ukończeniu korzystania z artykułu warto usunąć klaster. W usłudze HDInsight dane są przechowywane w usłudze Azure Storage, dzięki czemu można bezpiecznie usunąć klaster, gdy nie jest on w użyciu. Opłaty są również naliczane za klaster usługi HDInsight, nawet jeśli nie jest on w użyciu. Ponieważ opłaty za klaster są wielokrotnie wyższe niż opłaty za magazyn, ekonomicznie warto usunąć klastry, gdy nie są one w użyciu.

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

Teraz, po pomyślnym utworzeniu klastra usługi HDInsight przy użyciu interfejsu wiersza polecenia platformy Azure, użyj następującego polecenia, aby dowiedzieć się, jak pracować z klastrem:

### <a name="apache-hadoop-clusters"></a>Klastry Apache Hadoop

* [Korzystanie z programu Apache Hive z usługą HDInsight](hadoop/hdinsight-use-hive.md)
* [Używanie technologii MapReduce z hdinsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Klastry Apache HBase

* [Rozpoczynanie pracy z bazą danych Apache HBase w umacie HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Develop Java applications for Apache HBase on HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm klastrów

* [Opracowywanie topologii Języka Java dla Apache Storm u usługi HDInsight](storm/apache-storm-develop-java-topology.md)
* [Używanie składników języka Python w Apache Storm w umacie HDInsight](storm/apache-storm-develop-python-topology.md)
* [Wdrażanie i monitorowanie topologii za pomocą Apache Storm u usługi HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)
