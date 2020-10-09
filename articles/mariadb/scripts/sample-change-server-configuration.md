---
title: Skrypt interfejsu wiersza polecenia — zmiana parametrów serwera — Azure Database for MariaDB
description: Ten przykładowy skrypt interfejsu wiersza polecenia zawiera listę wszystkich dostępnych konfiguracji serwera i aktualizacji Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: ce27dc3008803faf548c10f4fc2ab3b579af2ba0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90525035"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-mariadb-server-using-azure-cli"></a>Uzyskiwanie listy i aktualizowanie konfiguracji serwera usługi Azure Database for MariaDB za pomocą interfejsu wiersza polecenia platformy Azure
Ten przykładowy skrypt interfejsu wiersza polecenia zwraca listę wszystkich dostępnych parametrów konfiguracji oraz ich dopuszczalnych wartości dla serwera usługi Azure Database for MariaDB, a także ustawia parametr *innodb_lock_wait_timeout* na wartość inną niż domyślna.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się uruchomić interfejs wiersza polecenia lokalnie, na potrzeby tego artykułu wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Sprawdź wersję, uruchamiając polecenie `az --version`. Aby zainstalować lub uaktualnić interfejs wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Przykładowy skrypt
W tym przykładowym skrypcie dokonaj edycji wyróżnionych wierszy w celu zmiany nazwy użytkownika i hasła administratora na swoje własne.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for MariaDB.")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia
Po uruchomieniu skryptu użyj następującego polecenia, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/change-server-configurations/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu
Ten skrypt używa poleceń opisanych w poniższej tabeli:

| **Polecenie** | **Uwagi** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create) | Tworzy serwer MariaDB hostujący bazy danych. |
| [az mariadb server configuration list](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list) | Zwraca listę konfiguracji serwera usługi Azure Database for MariaDB. |
| [az mariadb server configuration set](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) | Aktualizuje konfigurację serwera usługi Azure Database for MariaDB. |
| [az mariadb server configuration show](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-show) | Wyświetla konfigurację serwera usługi Azure Database for MariaDB. |
| [az group delete](/cli/azure/group#az-group-delete) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki
- Przeczytaj więcej informacji na temat interfejsu wiersza polecenia platformy Azure: [Dokumentacja interfejsu wiersza polecenia platformy Azure](/cli/azure).
- Wypróbuj dodatkowe skrypty: [Przykłady interfejsu wiersza polecenia platformy Azure dla usługi Azure Database for MariaDB](../sample-scripts-azure-cli.md)
- Aby uzyskać więcej informacji na temat parametrów serwera, zobacz [Jak konfigurować parametry serwera w usłudze Azure Database for MariaDB](../howto-server-parameters.md).
