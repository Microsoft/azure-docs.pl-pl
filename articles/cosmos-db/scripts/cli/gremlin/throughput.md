---
title: Skrypty interfejsu wiersza polecenia platformy Azure dla operacji przepływności (RU/s) dla Azure Cosmos DB interfejsu API gremlin
description: Skrypty interfejsu wiersza polecenia platformy Azure dla operacji przepływności (RU/s) dla Azure Cosmos DB interfejsu API gremlin
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 10/07/2020
ms.openlocfilehash: ac8f915a1e9cc73c076879ea8e5ee487c3fe0c3f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770637"
---
# <a name="throughput-rus-operations-with-azure-cli-for-a-database-or-graph-for-azure-cosmos-db---gremlin-api"></a>Operacje przepływności (RU/s) za pomocą interfejsu wiersza polecenia platformy Azure dla bazy danych lub grafu na Azure Cosmos DB — interfejs API gremlin
[!INCLUDE[appliesto-gremlin-api](../../../includes/appliesto-gremlin-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.12.1 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="sample-script"></a>Przykładowy skrypt

Ten skrypt tworzy bazę danych Gremlin z udostępnioną przepływnością i graf Gremlin z dedykowaną przepływnością, a następnie aktualizuje przepływność zarówno dla bazy danych, jak i grafu. Skrypt jest następnie migrowany ze standardowej do automatycznego skalowania przepływności, a następnie odczytuje wartość przepływności autoskalowania po migracji.

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
| [az group create](/cli/azure/group#az_group_create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create) | Tworzy konto usługi Azure Cosmos DB. |
| [az cosmosdb gremlin database create](/cli/azure/cosmosdb/gremlin/database#az_cosmosdb_gremlin_database_create) | Tworzy bazę danych Azure Cosmos Gremlin. |
| [az cosmosdb gremlin graph create](/cli/azure/cosmosdb/gremlin/graph#az_cosmosdb_gremlin_graph_create) | Tworzy graf gremlin usługi Azure Cosmos. |
| [az cosmosdb gremlin database throughput update](/cli/azure/cosmosdb/gremlin/database/throughput#az_cosmosdb_gremlin_database_throughput_update) | Aktualizowanie ru/s dla bazy danych Gremlin usługi Azure Cosmos. |
| [az cosmosdb gremlin graph throughput update](/cli/azure/cosmosdb/gremlin/graph/throughput#az_cosmosdb_gremlin_graph_throughput_update) | Aktualizowanie ru/s dla grafu Gremlin usługi Azure Cosmos. |
| [az cosmosdb gremlin database throughput migrate](/cli/azure/cosmosdb/gremlin/database/throughput#az_cosmosdb_gremlin_database_throughput_migrate) | Migrowanie przepływności dla bazy danych Gremlin usługi Azure Cosmos. |
| [az cosmosdb gremlin graph throughput migrate](/cli/azure/cosmosdb/gremlin/graph/throughput#az_cosmosdb_gremlin_graph_throughput_migrate) | Migrowanie przepływności dla grafu gremlin usługi Azure Cosmos. |
| [az group delete](/cli/azure/resource#az_resource_delete) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza Azure Cosmos DB, zobacz [dokumentację interfejsu wiersza Azure Cosmos DB wiersza polecenia](/cli/azure/cosmosdb).

Wszystkie Azure Cosmos DB skryptów interfejsu wiersza polecenia można znaleźć w repozytorium GitHub Azure Cosmos DB [cli.](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)
