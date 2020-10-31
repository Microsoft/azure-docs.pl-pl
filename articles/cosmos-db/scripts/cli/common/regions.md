---
title: Dodawanie regionów, zmiana priorytetu trybu failover, wyzwalanie trybu failover dla konta usługi Azure Cosmos
description: Dodawanie regionów, zmiana priorytetu trybu failover, wyzwalanie trybu failover dla konta usługi Azure Cosmos
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: c8c8cbd81be07070ec5434ee49e42d81641bc969
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93083934"
---
# <a name="add-regions-change-failover-priority-trigger-failover-for-an-azure-cosmos-account-using-azure-cli"></a>Dodawanie regionów, zmiana priorytetu trybu failover, wyzwalanie trybu failover dla konta usługi Azure Cosmos przy użyciu interfejsu wiersza polecenia platformy Azure
[!INCLUDE[appliesto-all-apis](../../../includes/appliesto-all-apis.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten temat będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.9.1 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Przykładowy skrypt

Ten skrypt demonstruje trzy operacje.

- Dodaj region do istniejącego konta usługi Azure Cosmos.
- Zmień priorytet regionalnej pracy awaryjnej (dotyczy kont przy użyciu automatycznego trybu failover)
- Wyzwalanie ręcznego przełączania z regionu podstawowego do pomocniczego (dotyczy kont z ręcznym trybem failover)

> [!NOTE]
> Operacji dodawania i usuwania regionów na koncie Cosmos nie można wykonać podczas zmieniania innych właściwości.

> [!NOTE]
> Ten przykład demonstruje użycie konta interfejsu API SQL (Core), ale te operacje są identyczne we wszystkich interfejsach API bazy danych w Cosmos DB.

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
| [az group create](/cli/azure/group#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Tworzy konto usługi Azure Cosmos DB. |
| [az cosmosdb update](/cli/azure/cosmosdb#az-cosmosdb-update) | Aktualizuje konto Azure Cosmos DB (Dodaj lub Usuń region). |
| [AZ cosmosdb failover — Priority-Change](/cli/azure/cosmosdb#az-cosmosdb-failover-priority-change) | Zaktualizuj priorytet trybu failover lub Wyzwól tryb failover na koncie Azure Cosmos DB. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Więcej informacji o interfejsie wiersza polecenia Azure Cosmos DB można znaleźć w [dokumentacji interfejsu wiersza polecenia Azure Cosmos DB](/cli/azure/cosmosdb).

Wszystkie przykłady skryptów interfejsu wiersza polecenia Azure Cosmos DB można znaleźć w [repozytorium GitHub Azure Cosmos DB interfejsu wiersza polecenia](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
