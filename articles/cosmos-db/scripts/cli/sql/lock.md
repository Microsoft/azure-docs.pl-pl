---
title: Tworzenie blokady zasobów dla bazy danych i kontenera interfejsu API Azure Cosmos DB Core (SQL)
description: Tworzenie blokady zasobów dla bazy danych i kontenera interfejsu API Azure Cosmos DB Core (SQL)
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 5a7c59cf579e87f9f772ea1ba27e5991b951adba
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772329"
---
# <a name="create-resource-lock-for-a-azure-cosmos-db-core-sql-api-database-and-container-using-azure-cli"></a>Tworzenie blokady zasobów dla bazy danych i kontenera interfejsu API Azure Cosmos DB Core (SQL) przy użyciu interfejsu wiersza polecenia platformy Azure
[!INCLUDE[appliesto-sql-api](../../../includes/appliesto-sql-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.9.1 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

> [!IMPORTANT]
> Blokady zasobów nie działają w przypadku zmian wprowadzonych przez użytkowników łączących się przy użyciu dowolnego zestawu SDK usługi Cosmos DB, narzędzi łączących się za pośrednictwem kluczy kont ani witryny Azure Portal, chyba że konto usługi Cosmos DB zostanie najpierw zablokowane z włączoną `disableKeyBasedMetadataWriteAccess` właściwością. Aby dowiedzieć się więcej na temat włączania tej właściwości, zobacz [Zapobieganie zmianom z zestawów SDK.](../../../role-based-access-control.md#prevent-sdk-changes)

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/sql/lock.sh "Create a resource lock for an Azure Cosmos DB Core (SQL) API database and container.")]

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
