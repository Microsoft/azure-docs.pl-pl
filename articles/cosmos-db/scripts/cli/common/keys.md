---
title: Praca z kluczami konta i parametry połączenia dla konta usługi Azure Cosmos
description: Praca z kluczami konta i parametry połączenia dla konta usługi Azure Cosmos
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: bef0501704a3c9c400d998b6e84cf1cabb839dd1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770871"
---
# <a name="work-with-account-keys-and-connection-strings-for-an-azure-cosmos-account-using-azure-cli"></a>Praca z kluczami konta i parametry połączenia dla konta usługi Azure Cosmos przy użyciu interfejsu wiersza polecenia platformy Azure
[!INCLUDE[appliesto-all-apis](../../../includes/appliesto-all-apis.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.9.1 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="sample-script"></a>Przykładowy skrypt

Ten skrypt przedstawia cztery operacje.

- Lista wszystkich kluczy kont
- Lista kluczy kont tylko do odczytu
- Lista parametry połączenia
- Ponowne generowanie kluczy konta

> [!NOTE]
> W tym przykładzie pokazano korzystanie z konta interfejsu API SQL (podstawowego), ale operacje na kluczu konta i parametrów połączenia są identyczne we wszystkich interfejsach API bazy danych w Cosmos DB.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/keys.sh "Keys and connection string operations for Cosmos DB.")]

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
| [az cosmosdb keys list](/cli/azure/cosmosdb/keys#az_cosmosdb_keys_list) | Lista kluczy dla konta Azure Cosmos DB magazynu. |
| [az cosmosdb list-read-only-keys](/cli/azure/cosmosdb#az_cosmosdb_list_read_only_keys) | Wyliczysz listę kluczy tylko do odczytu Azure Cosmos DB konta. |
| [az cosmosdb list-connection-strings](/cli/azure/cosmosdb#az_cosmosdb_list_connection_strings) | Lista parametry połączenia dla Azure Cosmos DB konta. |
| [az cosmosdb regenerate-key](/cli/azure/cosmosdb#az_cosmosdb_regenerate-key) | Ponowne generowanie kluczy dla Azure Cosmos DB magazynu. |
| [az group delete](/cli/azure/resource#az_resource_delete) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza Azure Cosmos DB, zobacz [dokumentację interfejsu wiersza Azure Cosmos DB wiersza polecenia](/cli/azure/cosmosdb).

Wszystkie Azure Cosmos DB skryptów interfejsu wiersza polecenia można znaleźć w repozytorium GitHub Azure Cosmos DB [cli.](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)
