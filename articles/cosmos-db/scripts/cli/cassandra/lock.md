---
title: Utwórz blokadę zasobów dla przestrzeni kluczy Cassandra i tabeli dla Azure Cosmos DB
description: Utwórz blokadę zasobów dla przestrzeni kluczy Cassandra i tabeli dla Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 94ff29d2c3272b61ff09fa0e564fcf2fcd281130
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566048"
---
# <a name="create-a-resource-lock-for-azure-cosmos-cassandra-api-keyspace-and-table-using-azure-cli"></a>Tworzenie blokady zasobów dla usługi Azure Cosmos interfejs API Cassandra przestrzeni kluczy i tabeli przy użyciu interfejsu wiersza polecenia platformy Azure
[!INCLUDE[appliesto-cassandra-api](../../../includes/appliesto-cassandra-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga wersji 2.9.1 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

> [!IMPORTANT]
> Blokady zasobów nie działają w przypadku zmian wprowadzonych przez użytkowników łączących się przy użyciu dowolnego zestawu Cassandra SDK, powłoki CQL lub witryny Azure Portal, chyba że konto Cosmos DB jest najpierw zablokowane przy `disableKeyBasedMetadataWriteAccess` włączonej właściwości. Aby dowiedzieć się więcej o tym, jak włączyć tę właściwość, zobacz, [uniemożliwiając zmiany z zestawów SDK](../../../role-based-access-control.md#prevent-sdk-changes).

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/cassandra/lock.sh "Create a resource lock for an Azure Cosmos DB Cassandra API keyspace, and table.")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [AZ Lock Create](/cli/azure/lock#az-lock-create) | Tworzy blokadę. |
| [AZ Lock list](/cli/azure/lock#az-lock-list) | Wyświetl informacje o blokadzie. |
| [AZ Lock show](/cli/azure/lock#az-lock-show) | Pokaż właściwości blokady. |
| [AZ Lock Delete](/cli/azure/lock#az-lock-delete) | Usuwa blokadę. |

## <a name="next-steps"></a>Następne kroki

- [Blokowanie zasobów w celu uniemożliwienia nieoczekiwanych zmian](../../../../azure-resource-manager/management/lock-resources.md)

- [Dokumentacja interfejsu wiersza polecenia Azure Cosmos DB](/cli/azure/cosmosdb).

- [Azure Cosmos DB repozytorium GitHub interfejsu wiersza polecenia](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
