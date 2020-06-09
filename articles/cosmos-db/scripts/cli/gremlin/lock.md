---
title: Utwórz blokadę zasobów dla bazy danych Gremlin i Graf dla Azure Cosmos DB
description: Utwórz blokadę zasobów dla bazy danych Gremlin i Graf dla Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 06/03/2020
ms.openlocfilehash: e4aba05df23ce4048ba711680086407cb764a24a
ms.sourcegitcommit: 5504d5a88896c692303b9c676a7d2860f36394c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2020
ms.locfileid: "84514724"
---
# <a name="create-a-resource-lock-for-azure-cosmos-gremlin-api-database-and-graph-using-azure-cli"></a>Tworzenie blokady zasobów dla bazy danych i grafu interfejsu API usługi Azure Cosmos Gremlin przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten temat będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.6.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

> [!IMPORTANT]
> Blokady zasobów nie działają w przypadku zmian wprowadzonych przez użytkowników łączących się przy użyciu dowolnego zestawu Gremlin SDK lub witryny Azure Portal, chyba że konto Cosmos DB jest najpierw zablokowane przy `disableKeyBasedMetadataWriteAccess` włączonej właściwości. Aby dowiedzieć się więcej o tym, jak włączyć tę właściwość, zobacz, [uniemożliwiając zmiany z zestawów SDK](../../../role-based-access-control.md#preventing-changes-from-cosmos-sdk).

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/gremlin/lock.sh "Create a resource lock for an Azure Cosmos DB Gremlin API database and graph.")]

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
