---
title: Skrypty interfejsu wiersza polecenia platformy Azure dla operacji przepływności (RU/s) dla zasobów interfejsu API Azure Cosmos DB Core (SQL)
description: Skrypty interfejsu wiersza polecenia platformy Azure dla operacji przepływności (RU/s) dla zasobów interfejsu API Azure Cosmos DB Core (SQL)
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/07/2020
ms.openlocfilehash: 8310de5ce8fd3f90e422555a5111569fadcca982
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566405"
---
# <a name="throughput-rus-operations-with-azure-cli-for-a-database-or-container-for-azure-cosmos-db-core-sql-api"></a>Operacje przepływności (RU/s) za pomocą interfejsu wiersza polecenia platformy Azure dla bazy danych lub kontenera dla interfejsu API Azure Cosmos DB Core (SQL)
[!INCLUDE[appliesto-sql-api](../../../includes/appliesto-sql-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga wersji 2.12.1 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="sample-script"></a>Przykładowy skrypt

Ten skrypt tworzy podstawową bazę danych interfejsu API (SQL) z udostępnioną przepływność i kontenerem interfejsu API Core (SQL) z dedykowaną przepływność, a następnie aktualizuje przepływność zarówno dla bazy danych, jak i kontenera. Skrypt jest następnie migrowany z warstwy Standardowa do automatycznego skalowania, a następnie odczytuje wartość przepływności automatycznego skalowania po migracji.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/sql/throughput.sh "Throughput operations for a SQL database and container.")]

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
| [AZ cosmosdb SQL Database Create](/cli/azure/cosmosdb/sql/database#az-cosmosdb-sql-database-create) | Tworzy bazę danych platformy Azure Cosmos Core (SQL). |
| [AZ cosmosdb SQL Container Create](/cli/azure/cosmosdb/sql/container#az-cosmosdb-sql-container-create) | Tworzy kontener platformy Azure Cosmos Core (SQL). |
| [AZ cosmosdb SQL Database — aktualizacja przepływności](/cli/azure/cosmosdb/sql/database/throughput#az-cosmosdb-sql-database-throughput-update) | Aktualizacja przepływności dla bazy danych Azure Cosmos Core (SQL). |
| [AZ cosmosdb SQL Container przepływność Update](/cli/azure/cosmosdb/sql/container/throughput#az-cosmosdb-sql-container-throughput-update) | Aktualizacja przepływności dla kontenera Azure Cosmos Core (SQL). |
| [AZ cosmosdb Database przepływność migracji](/cli/azure/cosmosdb/sql/database/throughput#az-cosmosdb-sql-database-throughput-migrate) | Migrowanie przepływności dla bazy danych Azure Cosmos Core (SQL). |
| [AZ cosmosdb SQL Container przepływność migracji](/cli/azure/cosmosdb/sql/container/throughput#az-cosmosdb-sql-container-throughput-migrate) | Migrowanie przepływności dla kontenera Azure Cosmos Core (SQL). |
| [az group delete](/cli/azure/resource#az-resource-delete) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Więcej informacji o interfejsie wiersza polecenia Azure Cosmos DB można znaleźć w [dokumentacji interfejsu wiersza polecenia Azure Cosmos DB](/cli/azure/cosmosdb).

Wszystkie przykłady skryptów interfejsu wiersza polecenia Azure Cosmos DB można znaleźć w [repozytorium GitHub Azure Cosmos DB interfejsu wiersza polecenia](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
