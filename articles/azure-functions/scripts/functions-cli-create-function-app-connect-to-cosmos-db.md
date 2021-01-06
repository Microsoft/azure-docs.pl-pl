---
title: Tworzenie aplikacji funkcji przy użyciu interfejsu wiersza polecenia platformy Azure Azure Cosmos DB
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie funkcji platformy Azure łączącej się z usługą Azure Cosmos DB
ms.topic: sample
ms.date: 07/03/2018
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 9ec4d3cb9d47608aa98075ba98aacfde51f341cd
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934428"
---
# <a name="create-an-azure-function-that-connects-to-an-azure-cosmos-db"></a>Tworzenie funkcji platformy Azure łączącej się z usługą Azure Cosmos DB

Ten przykładowy skrypt usługi Azure Functions tworzy aplikację funkcji i łączy funkcję z bazą danych usługi Azure Cosmos DB. Ustawienie utworzonej aplikacji, które zawiera parametry połączenia, może być używane z [wyzwalaczem lub powiązaniem usługi Azure Cosmos DB](../functions-bindings-cosmosdb.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Ten samouczek wymaga wersji 2,0 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana. 

## <a name="sample-script"></a>Przykładowy skrypt

Ten przykładowy skrypt tworzy aplikację funkcji platformy Azure i dodaje do jej ustawień punkt końcowy i klucz dostępu usługi Azure Cosmos DB.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-cosmos-db/create-function-app-connect-to-cosmos-db.sh "Create an Azure Function that connects to an Azure Cosmos DB")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Tworzy grupę zasobów w lokalizacji. |
| [az storage accounts create](/cli/azure/storage/account#az-storage-account-create) | Tworzenie konta magazynu |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Tworzy aplikację funkcji w [planie zużycia](../consumption-plan.md)bezserwerowego. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Tworzy bazę danych usługi Azure Cosmos DB. |
| [AZ cosmosdb show](/cli/azure/cosmosdb#az-cosmosdb-show)| Pobiera połączenie z kontem bazy danych. |
| [az cosmosdb list-keys](/cli/azure/cosmosdb#az-cosmosdb-list-keys)| Pobiera klucze dla bazy danych. |
| [AZ functionapp config AppSettings Set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) | Ustawia parametry połączenia jako ustawienia aplikacji w aplikacji funkcji. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dla usługi Azure Functions można znaleźć w [dokumentacji usługi Azure Functions](../functions-cli-samples.md).




