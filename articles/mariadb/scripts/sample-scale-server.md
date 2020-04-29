---
title: Serwer skalowania skryptów interfejsu wiersza polecenia — Azure Database for MariaDB
description: Ten przykładowy skrypt interfejsu wiersza polecenia służy do skalowania serwera usługi Azure Database for MariaDB na inny poziom wydajności po wykonaniu zapytania względem metryk.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 12/02/2019
ms.openlocfilehash: 562f265cccf444740c177a41e516f9066188613e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "74771639"
---
# <a name="monitor-and-scale-an-azure-database-for-mariadb-server-using-azure-cli"></a>Monitorowanie i skalowanie serwera usługi Azure Database for MariaDB za pomocą interfejsu wiersza polecenia platformy Azure
Ten przykładowy skrypt interfejsu wiersza polecenia skaluje zasoby obliczeniowe i magazyn dla jednego serwera Azure Database for MariaDB po wykonaniu zapytania o metryki. Obliczenia można skalować w górę lub w dół. Magazyn można skalować w górę.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się uruchomić interfejs wiersza polecenia lokalnie, na potrzeby tego artykułu wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Sprawdź wersję, uruchamiając polecenie `az --version`. Aby zainstalować lub uaktualnić interfejs wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Przykładowy skrypt
Zaktualizuj skrypt przy użyciu identyfikatora subskrypcji.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/scale-mariadb-server/scale-mariadb-server.sh "Create and scale Azure Database for MariaDB.")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia
Po uruchomieniu skryptu użyj następującego polecenia, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby. 
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/scale-mariadb-server/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu
Ten skrypt używa poleceń opisanych w poniższej tabeli:

| **Polecenie** | **Uwagi** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create) | Tworzy serwer MariaDB hostujący bazy danych. |
| [AZ MariaDB Server Update](/cli/azure/mariadb/server#az-mariadb-server-update) | Aktualizuje właściwości serwera MariaDB. |
| [az monitor metrics list](/cli/azure/monitor/metrics#az-monitor-metrics-list) | Zwraca wartość metryki dla zasobów. |
| [az group delete](/cli/azure/group#az-group-delete) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej na temat [Azure Database for MariaDB zasobów obliczeniowych i magazynu](../concepts-pricing-tiers.md)
- Wypróbuj dodatkowe skrypty: [Przykłady interfejsu wiersza polecenia platformy Azure dla usługi Azure Database for MariaDB](../sample-scripts-azure-cli.md)
- Dowiedz się więcej o [interfejsie wiersza polecenia platformy Azure](/cli/azure)
