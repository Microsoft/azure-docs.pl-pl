---
title: Aktualizowanie RU/s dla bazy danych Gremlin i grafu dla Azure Cosmos DB
description: Aktualizowanie RU/s dla bazy danych Gremlin i grafu dla Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: 0ef01a50e4827ec2ba96ce5d370bc2386e0dbfd6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "71275521"
---
# <a name="update-rus-for-a-gremlin-database-and-graph-for-azure-cosmos-db-using-azure-cli"></a>Aktualizowanie RU/s dla bazy danych Gremlin i grafu dla Azure Cosmos DB przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten temat będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.73 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Przykładowy skrypt

Ten skrypt tworzy bazę danych Gremlin z udostępnioną przepływność i wykresem Gremlin z dedykowaną przepływność, a następnie aktualizuje przepływność zarówno dla bazy danych, jak i grafu.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/gremlin/throughput.sh "Update RU/s for a Gremlin database and graph.")]

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
| [az group delete](/cli/azure/resource#az-resource-delete) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Więcej informacji o interfejsie wiersza polecenia Azure Cosmos DB można znaleźć w [dokumentacji interfejsu wiersza polecenia Azure Cosmos DB](/cli/azure/cosmosdb).

Wszystkie przykłady skryptów interfejsu wiersza polecenia Azure Cosmos DB można znaleźć w [repozytorium GitHub Azure Cosmos DB interfejsu wiersza polecenia](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
