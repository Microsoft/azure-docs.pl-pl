---
title: Tworzenie blokady zasobów dla bazy danych Gremlin i grafu dla Azure Cosmos DB
description: Tworzenie blokady zasobów dla bazy danych Gremlin i grafu dla Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 19809d7150c8a461f97282d1583d0d870d6af8bb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770675"
---
# <a name="create-a-resource-lock-for-azure-cosmos-gremlin-api-database-and-graph-using-azure-cli"></a>Tworzenie blokady zasobów dla bazy danych i grafu interfejsu API gremlin usługi Azure Cosmos przy użyciu interfejsu wiersza polecenia platformy Azure
[!INCLUDE[appliesto-gremlin-api](../../../includes/appliesto-gremlin-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.9.1 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

> [!IMPORTANT]
> Blokady zasobów nie działają w przypadku zmian wprowadzonych przez użytkowników łączących się przy użyciu dowolnego zestawu SDK języka Gremlin lub witryny Azure Portal, chyba że konto Cosmos DB zostanie najpierw zablokowane z włączoną `disableKeyBasedMetadataWriteAccess` właściwością . Aby dowiedzieć się więcej na temat włączania tej właściwości, zobacz [Zapobieganie zmianom z zestawów SDK.](../../../role-based-access-control.md#prevent-sdk-changes)

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/gremlin/lock.sh "Create a resource lock for an Azure Cosmos DB Gremlin API database and graph.")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az lock create](/cli/azure/lock#az_lock_create) | Tworzy blokadę. |
| [az lock list](/cli/azure/lock#az_lock_list) | Lista informacji o blokadach. |
| [az lock show](/cli/azure/lock#az_lock_show) | Pokazywanie właściwości blokady. |
| [az lock delete](/cli/azure/lock#az_lock_delete) | Usuwa blokadę. |

## <a name="next-steps"></a>Następne kroki

- [Blokowanie zasobów w celu uniemożliwienia nieoczekiwanych zmian](../../../../azure-resource-manager/management/lock-resources.md)

- [Azure Cosmos DB dokumentacji interfejsu wiersza polecenia.](/cli/azure/cosmosdb)

- [Azure Cosmos DB repozytorium GitHub interfejsu wiersza polecenia.](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)
