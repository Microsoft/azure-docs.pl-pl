---
title: Dodawanie regionów, zmienianie priorytetu trybu failover, wyzwalanie trybu failover dla konta usługi Azure Cosmos
description: Dodawanie regionów, zmienianie priorytetu trybu failover, wyzwalanie trybu failover dla konta usługi Azure Cosmos
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 4a1a061945fe1c6c6a95eb62d286d40a158281ca
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770802"
---
# <a name="add-regions-change-failover-priority-trigger-failover-for-an-azure-cosmos-account-using-azure-cli"></a>Dodawanie regionów, zmienianie priorytetu trybu failover, wyzwalanie trybu failover dla konta usługi Azure Cosmos przy użyciu interfejsu wiersza polecenia platformy Azure
[!INCLUDE[appliesto-all-apis](../../../includes/appliesto-all-apis.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.9.1 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="sample-script"></a>Przykładowy skrypt

Ten skrypt demonstruje trzy operacje.

- Dodaj region do istniejącego konta usługi Azure Cosmos.
- Zmiana priorytetu regionalnego trybu failover (dotyczy kont korzystających z automatycznego trybu failover)
- Wyzwalanie ręcznego trybu failover z regionu podstawowego do pomocniczego (dotyczy kont z ręcznym trybem failover)

> [!NOTE]
> Operacji dodawania i usuwania regionów na koncie usługi Cosmos nie można wykonać podczas zmiany innych właściwości.

> [!NOTE]
> W tym przykładzie pokazano korzystanie z konta interfejsu API SQL (podstawowego), ale te operacje są identyczne we wszystkich interfejsach API bazy danych w Cosmos DB.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/regions.sh "Regional operations for Cosmos DB.")]

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
| [az cosmosdb update](/cli/azure/cosmosdb#az_cosmosdb_update) | Aktualizuje konto Azure Cosmos DB (dodawanie lub usuwanie regionu). |
| [az cosmosdb failover-priority-change](/cli/azure/cosmosdb#az_cosmosdb_failover_priority_change) | Aktualizowanie priorytetu trybu failover lub wyzwalanie trybu failover na Azure Cosmos DB trybu failover. |
| [az group delete](/cli/azure/resource#az_resource_delete) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza Azure Cosmos DB, zobacz [dokumentację interfejsu wiersza Azure Cosmos DB wiersza polecenia](/cli/azure/cosmosdb).

Wszystkie Azure Cosmos DB skryptów interfejsu wiersza polecenia można znaleźć w repozytorium GitHub Azure Cosmos DB [cli.](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)
