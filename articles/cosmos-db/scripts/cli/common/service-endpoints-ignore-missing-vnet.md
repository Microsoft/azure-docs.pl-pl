---
title: Łączenie istniejącego konta usługi Azure Cosmos z punktami końcowymi usługi dla sieci wirtualnej
description: Łączenie istniejącego konta usługi Azure Cosmos z punktami końcowymi usługi dla sieci wirtualnej
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: eae343b0ac85f3fdf6d0f6d52c7afbb91f401df4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770774"
---
# <a name="connect-an-existing-azure-cosmos-account-with-virtual-network-service-endpoints-using-azure-cli"></a>Łączenie istniejącego konta usługi Azure Cosmos z punktami końcowymi usługi dla sieci wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure
[!INCLUDE[appliesto-all-apis](../../../includes/appliesto-all-apis.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.9.1 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="sample-script"></a>Przykładowy skrypt

Ten przykład ma na celu pokazanie, jak połączyć istniejące konto usługi Azure Cosmos z istniejącą nową siecią wirtualną, w której podsieć nie została jeszcze skonfigurowana dla punktów końcowych usługi przy użyciu `ignore-missing-vnet-service-endpoint` parametru . Umożliwia to bezbłędne ukończenie konfiguracji konta usługi Cosmos przed ukończeniem konfiguracji podsieci sieci wirtualnej. Po zakończeniu konfiguracji podsieci konto usługi Cosmos będzie dostępne za pośrednictwem skonfigurowanej podsieci.

> [!NOTE]
> W tym przykładzie pokazano, jak używać konta interfejsu API SQL (Core). Aby użyć tego przykładu dla innych interfejsów API, zastosuj parametry i w poniższym `enable-virtual-network` `virtual-network-rules` skrypcie do skryptu specyficznego dla interfejsu API.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/service-endpoints-ignore-missing-vnet.sh "Create an Azure Cosmos account with service endpoints.")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Po wykonaniu przykładowego skryptu możesz uruchomić następujące polecenie, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) | Tworzy sieć wirtualną platformy Azure. |
| [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Tworzy podsieć dla sieci wirtualnej platformy Azure. |
| [az network vnet subnet show](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_show) | Zwraca podsieć dla sieci wirtualnej platformy Azure. |
| [az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create) | Tworzy konto usługi Azure Cosmos DB. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) | Aktualizuje podsieć dla sieci wirtualnej platformy Azure. |
| [az group delete](/cli/azure/resource#az_resource_delete) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza Azure Cosmos DB, zobacz [dokumentację interfejsu wiersza Azure Cosmos DB wiersza polecenia](/cli/azure/cosmosdb).

Wszystkie Azure Cosmos DB skryptów interfejsu wiersza polecenia można znaleźć w repozytorium GitHub interfejsu wiersza Azure Cosmos DB wiersza [polecenia.](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)
