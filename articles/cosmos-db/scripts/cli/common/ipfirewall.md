---
title: Tworzenie konta usługi Azure Cosmos z zaporą IP
description: Tworzenie konta usługi Azure Cosmos z zaporą IP
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: cb00a3cf2e31a2bbd0fe9f0d143bfabb15ccc0be
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770961"
---
# <a name="create-an-azure-cosmos-account-with-ip-firewall-using-azure-cli"></a>Tworzenie konta usługi Azure Cosmos z zaporą IP przy użyciu interfejsu wiersza polecenia platformy Azure
[!INCLUDE[appliesto-all-apis](../../../includes/appliesto-all-apis.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.9.1 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="sample-script"></a>Przykładowy skrypt

> [!NOTE]
> W tym przykładzie pokazano, jak używać konta podstawowego interfejsu API SQL. Aby użyć tego przykładu dla innych interfejsów API, zastosuj parametr w poniższym `ip-range-filter` skrypcie `az cosmosdb account create` do polecenia dla skryptu określonego interfejsu API.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/ipfirewall.sh "Create an Azure Cosmos account with ip firewall.")]

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
| [az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create) | Tworzy konto usługi Azure Cosmos DB. |
| [az group delete](/cli/azure/resource#az_resource_delete) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza Azure Cosmos DB, zobacz [dokumentację interfejsu wiersza Azure Cosmos DB wiersza polecenia](/cli/azure/cosmosdb).

Wszystkie Azure Cosmos DB skryptów interfejsu wiersza polecenia można znaleźć w repozytorium GitHub Azure Cosmos DB [cli.](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)
