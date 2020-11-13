---
title: Połącz istniejące konto usługi Azure Cosmos z punktami końcowymi usługi sieci wirtualnej
description: Połącz istniejące konto usługi Azure Cosmos z punktami końcowymi usługi sieci wirtualnej
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: f66d219eff1919e62088e5c4f7aa72aab97ea4f9
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566235"
---
# <a name="connect-an-existing-azure-cosmos-account-with-virtual-network-service-endpoints-using-azure-cli"></a>Łączenie istniejącego konta Azure Cosmos z punktami końcowymi usługi sieci wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure
[!INCLUDE[appliesto-all-apis](../../../includes/appliesto-all-apis.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga wersji 2.9.1 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="sample-script"></a>Przykładowy skrypt

Ten przykład ma na celu pokazanie sposobu łączenia istniejącego konta usługi Azure Cosmos z istniejącą nową siecią wirtualną, w której podsieć nie została jeszcze skonfigurowana dla punktów końcowych usługi przy użyciu `ignore-missing-vnet-service-endpoint` parametru. Pozwala to na ukończenie konfiguracji konta Cosmos bez błędu przed ukończeniem konfiguracji podsieci w sieci wirtualnej. Po zakończeniu konfiguracji podsieci konto Cosmos będzie dostępne za pomocą skonfigurowanej podsieci.

> [!NOTE]
> Ten przykład pokazuje użycie konta interfejsu API SQL (rdzeń). Aby użyć tego przykładu dla innych interfejsów API, należy zastosować `enable-virtual-network` `virtual-network-rules` Parametry i w poniższym skrypcie do skryptu określonego przez interfejs API.

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
| [az group create](/cli/azure/group#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) | Tworzy sieć wirtualną platformy Azure. |
| [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) | Tworzy podsieć dla sieci wirtualnej platformy Azure. |
| [AZ Network VNET Subnet show](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-show) | Zwraca podsieć dla sieci wirtualnej platformy Azure. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Tworzy konto usługi Azure Cosmos DB. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) | Aktualizuje podsieć dla sieci wirtualnej platformy Azure. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Więcej informacji o interfejsie wiersza polecenia Azure Cosmos DB można znaleźć w [dokumentacji interfejsu wiersza polecenia Azure Cosmos DB](/cli/azure/cosmosdb).

Wszystkie przykłady skryptów interfejsu wiersza polecenia Azure Cosmos DB można znaleźć w [repozytorium GitHub Azure Cosmos DB interfejsu wiersza polecenia](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
