---
title: Tworzenie klastrów Apache Hadoop przy użyciu interfejsu wiersza polecenia platformy Azure — Azure HDInsight
description: Dowiedz się, jak tworzyć klastry usługi Azure HDInsight przy użyciu międzyplatformowego interfejsu wiersza polecenia platformy Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 02/03/2020
ms.openlocfilehash: 04def98108bf996a8f8cabe0ad36c022011aa533
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86080708"
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>Tworzenie klastrów usługi HDInsight przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Kroki opisane w tym dokumencie przedstawiają Tworzenie klastra HDInsight 3,6 przy użyciu interfejsu wiersza polecenia platformy Azure.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Interfejs wiersza polecenia platformy Azure. Jeśli nie zainstalowano interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) w celu wykonania kroków.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-cluster"></a>Tworzenie klastra

1. Zaloguj się do subskrypcji platformy Azure. Jeśli planujesz używać Azure Cloud Shell, wybierz opcję **Wypróbuj** w prawym górnym rogu bloku kodu. W przeciwnym razie wprowadź poniższe polecenie:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Ustaw zmienne środowiskowe. Użycie zmiennych w tym artykule jest oparte na bash. Inne środowiska będą wymagały niewielkich zmian. Zobacz [AZ-HDInsight-Create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) , aby uzyskać pełną listę możliwych parametrów do tworzenia klastra.

    |Parametr | Opis |
    |---|---|
    |`--workernode-count`| Liczba węzłów procesu roboczego w klastrze. W tym artykule jest stosowana zmienna `clusterSizeInNodes` jako wartość przekazaną do `--workernode-count` . |
    |`--version`| Wersja klastra usługi HDInsight. W tym artykule jest stosowana zmienna `clusterVersion` jako wartość przekazaną do `--version` . Zobacz również: [obsługiwane wersje usługi HDInsight](./hdinsight-component-versioning.md#supported-hdinsight-versions).|
    |`--type`| Typ klastra usługi HDInsight, taki jak: Hadoop, interactivehive, HBase, Kafka, burza, Spark, Rserver, mlservices.  W tym artykule jest stosowana zmienna `clusterType` jako wartość przekazaną do `--type` . Zobacz również: [typy i konfiguracja klastra](./hdinsight-hadoop-provision-linux-clusters.md#cluster-type).|
    |`--component-version`|Wersje różnych składników usługi Hadoop, w wersjach rozdzielonych spacjami w formacie "Component = Version". W tym artykule jest stosowana zmienna `componentVersion` jako wartość przekazaną do `--component-version` . Zobacz również: [składniki usługi Hadoop](./hdinsight-component-versioning.md#apache-components-available-with-different-hdinsight-versions).|

    Zamień `RESOURCEGROUPNAME` , `LOCATION` , `CLUSTERNAME` , `STORAGEACCOUNTNAME` i `PASSWORD` z wymaganymi wartościami. Zmień wartości innych zmiennych zgodnie z potrzebami. Następnie wprowadź polecenie interfejsu wiersza polecenia.

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

3. [Utwórz grupę zasobów](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) , wprowadzając następujące polecenie:

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

    Aby uzyskać listę prawidłowych lokalizacji, użyj `az account list-locations` polecenia, a następnie użyj jednej z tych lokalizacji z `name` wartości.

4. [Utwórz konto usługi Azure Storage](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) , wprowadzając następujące polecenie:

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

5. [Wyodrębnij klucz podstawowy z konta usługi Azure Storage](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest#az-storage-account-keys-list) i Zapisz go w zmiennej, wprowadzając następujące polecenie:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. [Utwórz kontener usługi Azure Storage](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) , wprowadzając następujące polecenie:

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. [Utwórz klaster usługi HDInsight](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) , wprowadzając następujące polecenie:

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
    > Klastry usługi HDInsight są dostępne w różnych typach, które odpowiadają obciążom lub technologiom, dla których jest dostrojony klaster. Nie ma obsługiwanej metody tworzenia klastra, który łączy wiele typów, takich jak burza i HBase w jednym klastrze.

    Ukończenie procesu tworzenia klastra może potrwać kilka minut. Zwykle około 15.

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

Teraz, gdy klaster usługi HDInsight został pomyślnie utworzony przy użyciu interfejsu wiersza polecenia platformy Azure, Skorzystaj z następujących informacji, aby dowiedzieć się, jak współpracować z klastrem:

### <a name="apache-hadoop-clusters"></a>Klastry Apache Hadoop

* [Korzystanie z programu Apache Hive z usługą HDInsight](hadoop/hdinsight-use-hive.md)
* [Korzystanie z MapReduce z usługą HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Klastry Apache HBase

* [Wprowadzenie do platformy Apache HBase w usłudze HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Tworzenie aplikacji Java dla platformy Apache HBase w usłudze HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Klastry Apache Storm

* [Tworzenie topologii języka Java dla Apache Storm w usłudze HDInsight](storm/apache-storm-develop-java-topology.md)
* [Używanie składników języka Python w Apache Storm w usłudze HDInsight](storm/apache-storm-develop-python-topology.md)
* [Wdrażanie i monitorowanie topologii za pomocą Apache Storm w usłudze HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)
