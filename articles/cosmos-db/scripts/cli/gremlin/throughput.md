---
title: Skrypty interfejsu wiersza polecenia platformy Azure dla operacji przepływności (RU/s) dla zasobów interfejsu API Azure Cosmos DB Gremlin
description: Skrypty interfejsu wiersza polecenia platformy Azure dla operacji przepływności (RU/s) dla zasobów interfejsu API Azure Cosmos DB Gremlin
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 10/07/2020
ms.openlocfilehash: 3e4b912d086065f28c56fd4af309d373b811a8ec
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91838636"
---
# <a name="throughput-rus-operations-with-azure-cli-for-a-database-or-graph-for-azure-cosmos-db---gremlin-api"></a>Operacje przepływności (RU/s) za pomocą interfejsu wiersza polecenia platformy Azure dla bazy danych lub grafu dla interfejsu API Azure Cosmos DB Gremlin

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten temat będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.12.1 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Przykładowy skrypt

Ten skrypt tworzy bazę danych Gremlin z udostępnioną przepływność i wykresem Gremlin z dedykowaną przepływność, a następnie aktualizuje przepływność zarówno dla bazy danych, jak i grafu. Skrypt jest następnie migrowany z warstwy Standardowa do automatycznego skalowania, a następnie odczytuje wartość przepływności automatycznego skalowania po migracji.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/gremlin/throughput.sh "Throughput operations for a Gremlin database and graph.")]

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
| [AZ cosmosdb Gremlin Database Create](/cli/azure/cosmosdb/gremlin/database#az-cosmosdb-gremlin-database-create) | Tworzy bazę danych usługi Azure Cosmos Gremlin. |
| [AZ cosmosdb Gremlin Graph Create](/cli/azure/cosmosdb/gremlin/graph#az-cosmosdb-gremlin-graph-create) | Tworzy wykres usługi Azure Cosmos Gremlin. |
| [AZ cosmosdb Gremlin Database — aktualizacja przepływności](/cli/azure/cosmosdb/gremlin/database/throughput#az-cosmosdb-gremlin-database-throughput-update) | Aktualizowanie RU/s dla bazy danych usługi Azure Cosmos Gremlin. |
| [AZ cosmosdb Gremlin Graph — aktualizacja przepływności](/cli/azure/cosmosdb/gremlin/graph/throughput#az-cosmosdb-gremlin-graph-throughput-update) | Aktualizowanie RU/s grafu usługi Azure Cosmos Gremlin. |
| [AZ cosmosdb Gremlin Database — Migrowanie przepływności](/cli/azure/cosmosdb/gremlin/database/throughput#az_cosmosdb_gremlin_database_throughput_migrate) | Migrowanie przepływności dla bazy danych usługi Azure Cosmos Gremlin. |
| [AZ cosmosdb Gremlin Graph — Migrowanie przepływności](/cli/azure/cosmosdb/gremlin/graph/throughput#az_cosmosdb_gremlin_graph_throughput_migrate) | Migruj przepływność dla wykresu usługi Azure Cosmos Gremlin. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Więcej informacji o interfejsie wiersza polecenia Azure Cosmos DB można znaleźć w [dokumentacji interfejsu wiersza polecenia Azure Cosmos DB](/cli/azure/cosmosdb).

Wszystkie przykłady skryptów interfejsu wiersza polecenia Azure Cosmos DB można znaleźć w [repozytorium GitHub Azure Cosmos DB interfejsu wiersza polecenia](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
