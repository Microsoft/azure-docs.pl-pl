---
title: Skrypty interfejsu wiersza polecenia platformy Azure dla operacji przepływności (RU/s) dla Azure Cosmos DB interfejs API Cassandra zasobów
description: Skrypty interfejsu wiersza polecenia platformy Azure dla operacji przepływności (RU/s) dla Azure Cosmos DB interfejs API Cassandra zasobów
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 10/07/2020
ms.openlocfilehash: 765ad939c8de9fb1b8c6b9c41cb2f7f685657d54
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770979"
---
# <a name="throughput-rus-operations-with-azure-cli-for-a-keyspace-or-table-for-azure-cosmos-db---cassandra-api"></a>Operacje przepływności (RU/s) za pomocą interfejsu wiersza polecenia platformy Azure dla przestrzeni kluczy lub tabeli na Azure Cosmos DB — interfejs API Cassandra
[!INCLUDE[appliesto-cassandra-api](../../../includes/appliesto-cassandra-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.12.1 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="sample-script"></a>Przykładowy skrypt

Ten skrypt tworzy przestrzeń kluczy Cassandra z udostępnioną przepływnością i tabelę Cassandra z dedykowaną przepływnością, a następnie aktualizuje przepływność dla przestrzeni kluczy i tabeli. Skrypt jest następnie migrowany ze standardowej do przepływności autoskalowania, a następnie odczytuje wartość przepływności autoskalowania po migracji.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/cassandra/throughput.sh "Throughput operations for Cassandra keyspace and table.")]

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
| [az cosmosdb cassandra keyspace create](/cli/azure/cosmosdb/cassandra/keyspace#az_cosmosdb_cassandra_keyspace_create) | Tworzy przestrzeń kluczy usługi Azure Cosmos Cassandra. |
| [az cosmosdb cassandra table create](/cli/azure/cosmosdb/cassandra/table#az_cosmosdb_cassandra_table_create) | Tworzy tabelę usługi Azure Cosmos Cassandra. |
| [az cosmosdb cassandra keyspace keyspace throughput update](/cli/azure/cosmosdb/cassandra/keyspace/throughput#az_cosmosdb_cassandra_keyspace_throughput_update) | Zaktualizuj ru/s dla przestrzeni kluczy cassandra usługi Azure Cosmos. |
| [az cosmosdb cassandra table throughput update](/cli/azure/cosmosdb/cassandra/table/throughput#az_cosmosdb_cassandra_table_throughput_update) | Aktualizowanie ru/s dla tabeli cassandra usługi Azure Cosmos. |
| [az cosmosdb cassandra keyspace throughput migrate](/cli/azure/cosmosdb/cassandra/keyspace/throughput#az_cosmosdb_cassandra_keyspace_throughput_migrate) | Migrowanie przepływności dla przestrzeni kluczy usługi Azure Cosmos Cassandra. |
| [az cosmosdb cassandra table throughput migrate](/cli/azure/cosmosdb/cassandra/table/throughput#az_cosmosdb_cassandra_table_throughput_migrate) | Migrowanie przepływności dla tabeli cassandra usługi Azure Cosmos. |
| [az group delete](/cli/azure/resource#az_resource_delete) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza Azure Cosmos DB, zobacz [dokumentację interfejsu wiersza Azure Cosmos DB wiersza polecenia](/cli/azure/cosmosdb).

Wszystkie Azure Cosmos DB skryptów interfejsu wiersza polecenia można znaleźć w repozytorium GitHub interfejsu wiersza Azure Cosmos DB wiersza [polecenia.](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)
