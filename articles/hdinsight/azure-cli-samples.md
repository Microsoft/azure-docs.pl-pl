---
title: 'Azure HDInsight: przykłady interfejsu wiersza polecenia platformy Azure'
description: Przykłady interfejsu wiersza polecenia platformy Azure dla typowych zadań w Azure HDInsight.
ms.service: hdinsight
ms.topic: sample
ms.date: 09/23/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4502cb26b4aeedff4223aaf5a592581aee36a0e6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775105"
---
# <a name="azure-hdinsight-azure-cli-samples"></a>Azure HDInsight: przykłady interfejsu wiersza polecenia platformy Azure

> [!div class="op_single_selector"]
> [Przykłady dla .NET](hdinsight-sdk-dotnet-samples.md) 
>  [Przykłady języka](hdinsight-sdk-python-samples.md) 
>  Python [Przykłady języka Java](hdinsight-sdk-java-samples.md)

Ten artykuł zawiera przykładowe skrypty dla typowych zadań. Dla każdego przykładu zaktualizuj zmienne przy użyciu odpowiednich wartości, a następnie wykonaj polecenie .

## <a name="prerequisites"></a>Wymagania wstępne

* Interfejs wiersza polecenia platformy Azure. Aby uzyskać [instrukcje, zobacz Instalowanie interfejsu wiersza](/cli/azure/install-azure-cli) polecenia platformy Azure.

* Opcjonalnie: Bash. W przykładach w tym artykule powłoka Bash jest Windows 10. Zobacz [Podsystem Windows dla systemu Linux przewodnik instalacji, Windows 10,](/windows/wsl/install-win10) aby uzyskać informacje o krokach instalacji.  Przykłady będą działać z wiersza polecenia systemu Windows z niewielkimi zmianami.

## <a name="az-login"></a>az login

[Zaloguj się do platformy Azure.](/cli/azure/reference-index#az_login)

```azurecli
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="az-hdinsight-create"></a>az hdinsight create

[Tworzy nowy klaster](/cli/azure/hdinsight#az_hdinsight_create).

### <a name="create-a-cluster-with-an-existing-storage-account"></a>Tworzenie klastra z istniejącym kontem magazynu

```azurecli
# set variables
export clusterName=CLUSTERNAME
export resourceGroupName=RESOURCEGROUPNAME
export clusterType=hadoop
export httpCredential='PASSWORD'
export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME

az hdinsight create \
    --name $clusterName \
    --resource-group $resourceGroupName \
    --type $clusterType \
    --http-password $httpCredential \
    --storage-account $AZURE_STORAGE_ACCOUNT
```

### <a name="create-a-cluster-with-the-enterprise-security-package-esp"></a>Tworzenie klastra przy użyciu pakiet Enterprise Security (ESP)

```azurecli
export clusterName=CLUSTERNAME
export resourceGroupName=RESOURCEGROUPNAME
export clusterType=spark
export httpCredential='PASSWORD'
export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME
export subnet="/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyRG/providers/Microsoft.Network/virtualNetworks/MyVnet/subnets/subnet1"
export domain="/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyRG/providers/Microsoft.AAD/domainServices/MyDomain.onmicrosoft.com"
export userAssignedIdentity="/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/MyMsiRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/MyMSI"
export domainAccount=MyAdminAccount@MyDomain.onmicrosoft.com
export groupDNS=MyGroup

az hdinsight create \
    --esp \
    --name $clusterName \
    --resource-group $resourceGroupName \
    --type $clusterType \
    --http-password $httpCredential \
    --storage-account $AZURE_STORAGE_ACCOUNT \
    --subnet $subnet \
    --domain $domain \
    --assign-identity $userAssignedIdentity \
    --cluster-admin-account $domainAccount \
    --cluster-users-group-dns $groupDNS
```

### <a name="create-a-kafka-cluster-with-disk-encryption"></a>Tworzenie klastra platformy Kafka z [szyfrowaniem dysków](./disk-encryption.md)

```azurecli
export clusterName=CLUSTERNAME
export resourceGroupName=RESOURCEGROUPNAME
export clusterType=kafka
export httpCredential='PASSWORD'
export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME
export encryptionKeyName=kafkaClusterKey
export encryptionKeyVersion=00000000000000000000000000000000
export encryptionVaultUri=https://MyKeyVault.vault.azure.net
export userAssignedIdentity="/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/MyMsiRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/MyMSI"

az hdinsight create \
    --name $clusterName \
    --resource-group $resourceGroupName \
    --type $clusterType \
    --http-password $httpCredential \
    --storage-account $AZURE_STORAGE_ACCOUNT \
    --workernode-data-disks-per-node 2 \
    --encryption-key-name $encryptionKeyName \
    --encryption-key-version $encryptionKeyVersion \
    --encryption-vault-uri $encryptionVaultUri \
    --assign-identity $userAssignedIdentity
```

### <a name="create-a-cluster-with-azure-data-lake-storage-gen2"></a>Tworzenie klastra z Azure Data Lake Storage Gen2

```azurecli
export clusterName=CLUSTERNAME
export resourceGroupName=RESOURCEGROUPNAME
export clusterType=spark
export httpCredential='PASSWORD'
export adlgen2=MyStorageAccount
export sami=MyMSI

az hdinsight create \
    --name $clusterName \
    --resource-group $resourceGroupName \
    --type $clusterType \
    --http-password $httpCredential \
    --storage-account $adlgen2 \
    --storage-account-managed-identity $sami
```

### <a name="create-a-cluster-with-configuration-from-json-string"></a>Tworzenie klastra z konfiguracją na pomocą ciągu JSON

```azurecli
export clusterName=CLUSTERNAME
export resourceGroupName=RESOURCEGROUPNAME
export clusterType=spark
export httpCredential='PASSWORD'
export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME
export clusterConfiguration="{'gateway':{'restAuthCredential.username':'admin'}}"

az hdinsight create \
    --name $clusterName \
    --resource-group $resourceGroupName \
    --type $clusterType \
    --http-password $httpCredential \
    --storage-account $AZURE_STORAGE_ACCOUNT \
    --cluster-configuration $clusterConfiguration
```

### <a name="create-a-cluster-with-configuration-from-a-local-file"></a>Tworzenie klastra z konfiguracją z pliku lokalnego

```azurecli
export clusterName=CLUSTERNAME
export resourceGroupName=RESOURCEGROUPNAME
export clusterType=spark
export httpCredential='PASSWORD'
export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME
export clusterConfiguration=@/mnt/c/HDI/config.json

az hdinsight create \
    --name $clusterName \
    --resource-group $resourceGroupName \
    --type $clusterType \
    --http-password $httpCredential \
    --storage-account $AZURE_STORAGE_ACCOUNT \
    --cluster-configuration $clusterConfiguration
```

## <a name="az-hdinsight-application-create"></a>az hdinsight application create

[Utwórz aplikację dla klastra usługi HDInsight.](/cli/azure/hdinsight/application#az_hdinsight_application_create)

### <a name="create-an-application-with-a-script-uri"></a>Tworzenie aplikacji przy użyciu skryptu URI

```azurecli
export resourceGroupName=RESOURCEGROUPNAME
export applicationName=MyApplication
export clusterName=CLUSTERNAME
export scriptURI=https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
export scriptActionName=MyScriptAction
export scriptParameters='"-version latest -port 20000"'

az hdinsight application create \
    --resource-group $resourceGroupName \
    --name $applicationName \
    --cluster-name $clusterName \
    --script-uri $scriptURI  \
    --script-action-name $scriptActionName \
    --script-parameters "$scriptParameters"
```

### <a name="create-an-application-with-a-script-uri-and-specified-edge-node-size"></a>Tworzenie aplikacji z określonym rozmiarem węzła krawędzi i uri skryptu

```azurecli
export resourceGroupName=RESOURCEGROUPNAME
export applicationName=MyApplication
export clusterName=CLUSTERNAME
export scriptURI=https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
export scriptActionName=MyScriptAction
export scriptParameters='"-version latest -port 20000"'
export edgenodeSize=Standard_D4_v2

az hdinsight application create \
    --resource-group $resourceGroupName \
    --name $applicationName \
    --cluster-name $clusterName \
    --script-uri $scriptURI  \
    --script-action-name $scriptActionName \
    --script-parameters "$scriptParameters" \
    --edgenode-size $edgenodeSize
```

### <a name="create-an-application-with-https-endpoint"></a>Tworzenie aplikacji przy użyciu punktu końcowego HTTPS

```azurecli
export resourceGroupName=RESOURCEGROUPNAME
export applicationName=MyApplication
export clusterName=CLUSTERNAME
export scriptURI=https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
export scriptActionName=MyScriptAction
export scriptParameters='"-version latest -port 20000"'
export destinationPort=8888
export subDomainSuffix=was

az hdinsight application create \
    --resource-group $resourceGroupName \
    --name $applicationName \
    --cluster-name $clusterName \
    --script-uri $scriptURI  \
    --script-action-name $scriptActionName \
    --script-parameters "$scriptParameters" \
    --destination-port $destinationPort \
    --sub-domain-suffix $subDomainSuffix
```

## <a name="az-hdinsight-script-action-execute"></a>az hdinsight script-action execute

[Wykonaj akcje skryptu w określonym klastrze usługi HDInsight.](/cli/azure/hdinsight/script-action#az_hdinsight_script_action_execute)

### <a name="execute-a-script-action-and-persist-on-success"></a>Wykonywanie akcji skryptu i utrwalanie w przypadku powodzenia

```azurecli
export resourceGroupName=RESOURCEGROUPNAME
export clusterName=CLUSTERNAME
export scriptActionName=MyScriptAction
export scriptURI=https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
export roles="headnode workernode"

az hdinsight script-action execute \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --name $scriptActionName \
    --script-uri $scriptURI  \
    --roles $roles \
    --persist-on-success
```