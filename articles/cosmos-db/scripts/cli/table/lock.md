---
title: Tworzenie blokady zasobów dla Azure Cosmos DB tabeli interfejs API tabel
description: Tworzenie blokady zasobów dla Azure Cosmos DB tabeli interfejs API tabel
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 06/03/2020
ms.openlocfilehash: bb66f215241755ae0fbcef3c95f5ea67a686a4fd
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87092579"
---
# <a name="create-resource-lock-for-a-azure-cosmos-db-table-api-table-using-azure-cli"></a>Tworzenie blokady zasobów dla Azure Cosmos DB interfejs API tabel tabeli przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten temat będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.6.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

> [!IMPORTANT]
> Blokady zasobów nie działają w przypadku zmian wprowadzonych przez użytkowników łączących Cosmos DB zestaw SDK tabeli, zestaw SDK tabeli usługi Azure Storage, wszystkie narzędzia, które łączą się za pośrednictwem kluczy konta lub witryny Azure Portal, chyba że konto Cosmos DB jest najpierw zablokowane przy `disableKeyBasedMetadataWriteAccess` włączonej właściwości. Aby dowiedzieć się więcej o tym, jak włączyć tę właściwość, zobacz, [uniemożliwiając zmiany z zestawów SDK](../../../role-based-access-control.md#prevent-sdk-changes).

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/table/lock.sh "Create a resource lock for an Azure Cosmos DB Table API table.")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [AZ Lock Create](/cli/azure/lock#az-lock-create) | Tworzy blokadę. |
| [AZ Lock list](/cli/azure/lock#az-lock-list) | Wyświetl informacje o blokadzie. |
| [AZ Lock show](/cli/azure/lock#az-lock-show) | Pokaż właściwości blokady. |
| [AZ Lock Delete](/cli/azure/lock#az-lock-delete) | Usuwa blokadę. |

## <a name="next-steps"></a>Następne kroki

-[Zablokuj zasoby, aby zapobiec nieoczekiwanym zmianom](../../../../azure-resource-manager/management/lock-resources.md)

-[Dokumentacja interfejsu wiersza polecenia Azure Cosmos DB](/cli/azure/cosmosdb).

-[Azure Cosmos DB repozytorium GitHub interfejsu wiersza polecenia](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
