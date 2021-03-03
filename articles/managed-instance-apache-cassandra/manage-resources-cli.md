---
title: Zarządzanie wystąpieniem zarządzanym platformy Azure dla zasobów Apache Cassandra przy użyciu interfejsu wiersza polecenia platformy Azure
description: Poznaj typowe polecenia służące do automatyzowania zarządzania wystąpieniem zarządzanym platformy Azure dla usługi Apache Cassandra przy użyciu interfejsu wiersza polecenia platformy Azure.
author: TheovanKraay
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 03/02/2021
ms.author: thvankra
ms.openlocfilehash: 3cd5fdbf6cdc504a1290c8fbd80cf89cf85ce714
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746737"
---
# <a name="manage-azure-managed-instance-for-apache-cassandra-resources-using-azure-cli-preview"></a>Zarządzanie wystąpieniem zarządzanym Azure dla zasobów Apache Cassandra przy użyciu interfejsu wiersza polecenia platformy Azure (wersja zapoznawcza)

W tym artykule opisano typowe polecenia służące do automatyzowania zarządzania wystąpieniem zarządzanym platformy Azure na potrzeby klastrów Apache Cassandra przy użyciu interfejsu wiersza polecenia platformy Azure.

> [!IMPORTANT]
> Wystąpienie zarządzane platformy Azure dla systemu Apache Cassandra jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.12.1 lub nowszej. Jeśli używasz Azure Cloud Shell, Najnowsza wersja jest już zainstalowana.

> [!IMPORTANT]
> Nie można zmienić nazwy zarządzania wystąpieniem zarządzanym platformy Azure dla zasobów Apache Cassandra, ponieważ to narusza sposób, w jaki Azure Resource Manager współpracuje z identyfikatorami URI zasobów.

## <a name="azure-managed-instance-for-apache-cassandra-clusters"></a>Wystąpienie zarządzane platformy Azure dla klastrów Apache Cassandra

W poniższych sekcjach pokazano, jak zarządzać wystąpieniem zarządzanym platformy Azure dla klastrów Apache Cassandra, w tym:

* [Tworzenie klastra wystąpienia zarządzanego](#create-cluster)
* [Usuń klaster wystąpienia zarządzanego](#delete-cluster)
* [Pobierz szczegóły klastra](#get-cluster-details)
* [Pobierz stan węzła klastra](#get-cluster-status)
* [Wyświetl listę klastrów według grupy zasobów](#list-clusters-resource-group)
* [Wyświetl listę klastrów według identyfikatora subskrypcji](#list-clusters-subscription)

### <a name="create-a-managed-instance-cluster"></a><a id="create-cluster"></a>Tworzenie klastra wystąpienia zarządzanego

Utwórz wystąpienie zarządzane platformy Azure dla klastra Apache Cassandra:

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
location='West US'
delegatedManagementSubnetId='/subscriptions/<subscription id>/resourceGroups/customer-vnet-rg/providers/Microsoft.Network/virtualNetworks/customer-vnet/subnets/management'
initialCassandraAdminPassword='myPassword'

# You can override the cluster name if the original name is not legal for an Azure resource.
# overrideClusterName='ClusterNameIllegalForAzureResource'
# the default Cassandra version is v3.11

az managed-cassandra cluster create \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName \
    --location $location \
    --delegated-management-subnet-id $delegatedManagementSubnetId \
    --initial-cassandra-admin-password $initialCassandraAdminPassword \
```

### <a name="delete-a-managed-instance-cluster"></a><a id="delete-cluster"></a>Usuń klaster wystąpienia zarządzanego

Usuwanie klastra:

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra cluster delete \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="get-the-cluster-details"></a><a id="get-cluster-details"></a>Pobierz szczegóły klastra

Pobierz szczegóły klastra:

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra cluster show \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="get-the-cluster-node-status"></a><a id="get-cluster-status"></a>Pobierz stan węzła klastra

Pobierz szczegóły klastra:

```azurecli-interactive
clusterName='cassandra-hybrid-cluster'
resourceGroupName='MyResourceGroup'

az managed-cassandra cluster node-status \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="list-the-clusters-by-resource-group"></a><a id="list-clusters-resource-group"></a>Wyświetl listę klastrów według grupy zasobów

Wyświetl listę klastrów według grupy zasobów:

```azurecli-interactive
subscriptionId='MySubscriptionId'
resourceGroupName='MyResourceGroup'

az managed-cassandra cluster list\
    --resource-group $resourceGroupName
```

### <a name="list-clusters-by-subscription-id"></a><a id="list-clusters-subscription"></a>Wyświetl listę klastrów według identyfikatora subskrypcji

Wyświetl listę klastrów według identyfikatora subskrypcji:

```azurecli-interactive
# set your subscription id
az account set -s <subscription id>

az managed-cassandra cluster list
```

## <a name="the-managed-instance-datacenters"></a><a id="managed-instance-datacenter"></a>Centra danych wystąpienia zarządzanego

W poniższych sekcjach pokazano, jak zarządzać wystąpieniem zarządzanym platformy Azure dla centrów danych Apache Cassandra, w tym:

* [Tworzenie centrum danych](#create-datacenter)
* [Usuwanie centrum danych](#delete-datacenter)
* [Pobierz szczegóły centrum danych](#get-datacenter-details)
* [Aktualizowanie lub skalowanie centrum danych](#update-datacenter)
* [Pobieranie centrów danych w klastrze](#get-datacenters-cluster)

### <a name="create-a-datacenter"></a><a id="create-datacenter"></a>Tworzenie centrum danych

Utwórz centrum danych:

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'
dataCenterLocation='eastus2'
delegatedSubnetId='/subscriptions/<Subscription_ID>/resourceGroups/customer-vnet-rg/providers/Microsoft.Network/virtualNetworks/customer-vnet/subnets/dc1-subnet'

az managed-cassandra datacenter create \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName \
    --data-center-location $dataCenterLocation \
    --delegated-subnet-id $delegatedSubnetId \
    --node-count 3 
```

### <a name="delete-a-datacenter"></a><a id="delete-datacenter"></a>Usuwanie centrum danych

Usuwanie centrum danych:

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'

az managed-cassandra datacenter delete \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName 
```

### <a name="get-datacenter-details"></a><a id="get-datacenter-details"></a>Pobierz szczegóły centrum danych

Pobierz szczegóły centrum danych:

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'

az managed-cassandra datacenter show \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName 
```

### <a name="update-or-scale-a-datacenter"></a><a id="update-datacenter"></a>Aktualizowanie lub skalowanie centrum danych

Aktualizowanie lub skalowanie centrum danych (w celu skalowania wartości zmiany nodeCount):

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'
dataCenterLocation='eastus'
delegatedSubnetId= '/subscriptions/<Subscription_ID>/resourceGroups/customer-vnet-rg/providers/Microsoft.Network/virtualNetworks/customer-vnet/subnets/dc1-subnet'

az managed-cassandra datacenter update \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName \
    --node-count 13 
```

### <a name="get-the-datacenters-in-a-cluster"></a><a id="get-datacenters-cluster"></a>Pobieranie centrów danych w klastrze

Pobierz centra danych w klastrze:

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra datacenter list \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName
```

## <a name="next-steps"></a>Następne kroki

* [Utwórz klaster wystąpienia zarządzanego na podstawie Azure Portal](create-cluster-portal.md)
* [Wdróż zarządzany Klaster Apache Spark z Azure Databricks](deploy-cluster-databricks.md)