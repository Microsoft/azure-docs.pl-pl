---
title: Tworzenie bazy danych i kontenera interfejsu API programu Core (SQL) dla Azure Cosmos DB
description: Tworzenie bazy danych i kontenera interfejsu API programu Core (SQL) dla Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 06/03/2020
ms.openlocfilehash: 416da39df9bfb49d6323ee789d5e67b1743a1cd7
ms.sourcegitcommit: 5504d5a88896c692303b9c676a7d2860f36394c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2020
ms.locfileid: "84509393"
---
# <a name="create-an-azure-cosmos-core-sql-api-account-database-and-container-using-azure-cli"></a>Tworzenie konta, bazy danych i kontenera interfejsu API platformy Azure Cosmos Core przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten temat będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.73 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/sql/create.sh "Create an Azure Cosmos DB SQL (Core) API account, database, and container.")]

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
| [AZ cosmosdb SQL Database Create](/cli/azure/cosmosdb/sql/database#az-cosmosdb-sql-database-create) | Tworzy bazę danych SQL (rdzeń) usługi Azure Cosmos. |
| [AZ cosmosdb SQL Container Create](/cli/azure/cosmosdb/sql/container#az-cosmosdb-sql-container-create) | Tworzy kontener usługi Azure Cosmos SQL (rdzeń). |
| [az group delete](/cli/azure/resource#az-resource-delete) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Więcej informacji o interfejsie wiersza polecenia Azure Cosmos DB można znaleźć w [dokumentacji interfejsu wiersza polecenia Azure Cosmos DB](/cli/azure/cosmosdb).

Wszystkie przykłady skryptów interfejsu wiersza polecenia Azure Cosmos DB można znaleźć w [repozytorium GitHub Azure Cosmos DB interfejsu wiersza polecenia](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
