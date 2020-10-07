---
title: Zaktualizuj RU/s dla Cassandra przestrzeni kluczy i tabeli dla Azure Cosmos DB
description: Aktualizowanie RU/s dla Cassandra przestrzeni kluczy i tabeli dla Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: c0026202e8f110123a7360a5fda521947ed8e9ee
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91803657"
---
# <a name="update-rus-for-a-cassandra-keyspace-and-table-using-azure-cli"></a>Aktualizowanie RU/s dla Cassandraej przestrzeni kluczy i tabeli przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten temat będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.9.1 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Przykładowy skrypt

Ten skrypt tworzy przestrzeń kluczy Cassandra z udostępnioną przepływność i tabelę Cassandra z dedykowaną przepływność, a następnie aktualizuje przepływność zarówno dla obszaru kluczy, jak i tabeli.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/cassandra/throughput.sh "Update RU/s for Cassandra keyspace and table.")]

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
| [AZ cosmosdb Cassandra Create Space](/cli/azure/cosmosdb/cassandra/keyspace#az-cosmosdb-cassandra-keyspace-create) | Tworzy przestrzeń kluczy Cassandra usługi Azure Cosmos. |
| [AZ cosmosdb Cassandra Table Create](/cli/azure/cosmosdb/cassandra/table#az-cosmosdb-cassandra-table-create) | Tworzy tabelę usługi Azure Cosmos Cassandra. |
| [AZ cosmosdb Cassandra — przepływność przestrzeni kluczy](/cli/azure/cosmosdb/cassandra/keyspace/throughput?view=azure-cli-latest&preserve-view=true#az-cosmosdb-cassandra-keyspace-throughput-update) | Zaktualizuj RU/s dla przestrzeni kluczy usługi Azure Cosmos Cassandra. |
| [AZ cosmosdb Cassandra — aktualizacja przepływności tabeli](/cli/azure/cosmosdb/cassandra/table/throughput#az-cosmosdb-cassandra-table-throughput-update) | Aktualizowanie RU/s dla tabeli usługi Azure Cosmos Cassandra. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Więcej informacji o interfejsie wiersza polecenia Azure Cosmos DB można znaleźć w [dokumentacji interfejsu wiersza polecenia Azure Cosmos DB](/cli/azure/cosmosdb).

Wszystkie przykłady skryptów interfejsu wiersza polecenia Azure Cosmos DB można znaleźć w [repozytorium GitHub Azure Cosmos DB interfejsu wiersza polecenia](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
