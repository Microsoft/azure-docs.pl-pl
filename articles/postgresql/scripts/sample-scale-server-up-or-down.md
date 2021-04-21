---
title: Skrypt interfejsu wiersza polecenia platformy Azure — skalowanie i monitorowanie Azure Database for PostgreSQL
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — skalowanie serwera usługi Azure Database for PostgreSQL na inny poziom wydajności po wykonaniu zapytania względem metryk.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.custom: mvc, devx-track-azurecli
ms.topic: sample
ms.date: 08/07/2019
ms.openlocfilehash: 56fd88ab658e59cccb14a35559d1793bc3ad1aa0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778447"
---
# <a name="monitor-and-scale-a-single-postgresql-server-using-azure-cli"></a>Monitorowanie i skalowanie pojedynczego serwera PostgreSQL za pomocą interfejsu wiersza polecenia platformy Azure
Ten przykładowy skrypt interfejsu wiersza polecenia skaluje zasoby obliczeniowe i magazyn dla jednego Azure Database for PostgreSQL serwera po odpytaniu metryk. Obliczenia można skalować w górę lub w dół. Magazyn można skalować tylko w górę. 

> [!IMPORTANT] 
> Magazyn można skalować tylko w górę, a nie w dół.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="sample-script"></a>Przykładowy skrypt
Zaktualizuj skrypt przy użyciu identyfikatora subskrypcji.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/scale-postgresql-server.sh "Create and scale Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia
Po uruchomieniu skryptu użyj następującego polecenia, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/delete-postgresql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu
Ten skrypt używa poleceń opisanych w poniższej tabeli:

| **Polecenia** | **Uwagi** |
|---|---|
| [az group create](/cli/azure/group) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az postgres server create](/cli/azure/postgres/server#az_postgres_server_create) | Tworzy serwer PostgreSQL hostujący bazy danych. |
| [az postgres server update](/cli/azure/postgres/server#az_postgres_server_update) | Aktualizuje właściwości serwera PostgreSQL. |
| [az monitor metrics list](/cli/azure/monitor/metrics) | Zwraca wartość metryki dla zasobów. |
| [az group delete](/cli/azure/group) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [Azure Database for PostgreSQL obliczeniowych i magazynu](../concepts-pricing-tiers.md)
- Wypróbuj dodatkowe skrypty: [Przykłady interfejsu wiersza polecenia platformy Azure dla usługi Azure Database for PostgreSQL](../sample-scripts-azure-cli.md)
- Dowiedz się więcej o interfejsie [wiersza polecenia platformy Azure](/cli/azure)
