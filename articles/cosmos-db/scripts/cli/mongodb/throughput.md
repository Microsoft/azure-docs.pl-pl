---
title: Skrypty interfejsu wiersza polecenia platformy Azure dla operacji przepływności (RU/s) dla Azure Cosmos DB API dla zasobów MongoDB
description: Skrypty interfejsu wiersza polecenia platformy Azure dla operacji przepływności (RU/s) dla Azure Cosmos DB API dla zasobów MongoDB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 10/07/2020
ms.openlocfilehash: 785f77df117b8072f6b8970287f4051795d07167
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91838633"
---
# <a name="throughput-rus-operations-with-azure-cli-for-a-database-or-graph-for-azure-cosmos-db-api-for-mongodb"></a>Operacje przepływności (RU/s) za pomocą interfejsu wiersza polecenia platformy Azure dla bazy danych lub Graf dla Azure Cosmos DB interfejsu API dla MongoDB

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten temat będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.12.1 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Przykładowy skrypt

Ten skrypt tworzy bazę danych MongoDB o wspólnej przepływności i kolekcji z dedykowaną przepływność, a następnie aktualizuje przepływność dla obu tych celów. Skrypt jest następnie migrowany z warstwy Standardowa do automatycznego skalowania, a następnie odczytuje wartość przepływności automatycznego skalowania po migracji.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/mongodb/throughput.sh "Throughput operations for Azure Cosmos DB API for MongoDB.")]

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
| [AZ cosmosdb MongoDB Database Create](/cli/azure/cosmosdb/mongodb/database#az-cosmosdb-mongodb-database-create) | Tworzy bazę danych interfejsu API usługi Azure Cosmos MongoDB. |
| [AZ cosmosdb MongoDB Collection Create](/cli/azure/cosmosdb/mongodb/collection#az-cosmosdb-mongodb-collection-create) | Tworzy kolekcję interfejsu API usługi Azure Cosmos MongoDB. |
| [AZ cosmosdb MongoDB Database — aktualizacja przepływności](/cli/azure/cosmosdb/mongodb/database/throughput#az-cosmosdb-mongodb-database-throughput-update) | Aktualizacja jednostek ru dla bazy danych interfejsu API usługi Azure Cosmos MongoDB. |
| [AZ cosmosdb MongoDB Collection — aktualizacja przepływności](/cli/azure/cosmosdb/mongodb/collection/throughput#az-cosmosdb-mongodb-collection-throughput-update) | Aktualizacja jednostek ru dla kolekcji interfejsu API usługi Azure Cosmos MongoDB. |
| [AZ cosmosdb MongoDB Database — Migrowanie przepływności](/cli/azure/cosmosdb/mongodb/database/throughput#az-cosmosdb-mongodb-database-throughput-migrate) | Migrowanie przepływności bazy danych. |
| [AZ cosmosdb MongoDB Collection przeprowadzenie migracji](/cli/azure/cosmosdb/mongodb/collection/throughput#az-cosmosdb-mongodb-collection-throughput-migrate) | Migrowanie przepływności dla kolekcji. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Więcej informacji o interfejsie wiersza polecenia Azure Cosmos DB można znaleźć w [dokumentacji interfejsu wiersza polecenia Azure Cosmos DB](/cli/azure/cosmosdb).

Wszystkie przykłady skryptów interfejsu wiersza polecenia Azure Cosmos DB można znaleźć w [repozytorium GitHub Azure Cosmos DB interfejsu wiersza polecenia](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
