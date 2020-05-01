---
title: Skrypt interfejsu wiersza polecenia platformy Azure — zmiana konfiguracji serwera (PostgreSQL)
description: Ten przykładowy skrypt interfejsu wiersza polecenia zwraca listę wszystkich dostępnych opcji konfiguracji serwera i aktualizuje wartość jednej z opcji.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 02/28/2018
ms.openlocfilehash: d2b54d1173b9591de2482f4b3368d3dde8b8c766
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "71947795"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-postgresql-server-using-azure-cli"></a>Uzyskiwanie listy i aktualizowanie konfiguracji serwera usługi Azure Database for PostgreSQL przy użyciu interfejsu wiersza polecenia platformy Azure
Ten przykładowy skrypt interfejsu wiersza polecenia zwraca listę wszystkich dostępnych parametrów konfiguracji oraz ich dopuszczalnych wartości dla serwera usługi Azure Database for PostgreSQL, a także ustawia parametr *log_retention_days* na wartość inną niż domyślna.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się uruchomić interfejs wiersza polecenia lokalnie, na potrzeby tego artykułu wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Sprawdź wersję, uruchamiając polecenie `az --version`. Aby zainstalować lub uaktualnić interfejs wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Przykładowy skrypt
W tym przykładowym skrypcie dokonaj edycji wyróżnionych wierszy w celu zmiany nazwy użytkownika i hasła administratora na swoje własne.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia
Po uruchomieniu skryptu użyj następującego polecenia, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/change-server-configurations/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu
Ten skrypt używa poleceń opisanych w poniższej tabeli:

| **Polecenie** | **Uwagi** |
|---|---|
| [az group create](/cli/azure/group) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az postgres server create](/cli/azure/postgres/server) | Tworzy serwer PostgreSQL hostujący bazy danych. |
| [az postgres server configuration list](/cli/azure/postgres/server/configuration) | Zwraca listę konfiguracji serwera usługi Azure Database for PostgreSQL. |
| [az postgres server configuration set](/cli/azure/postgres/server/configuration) | Aktualizuje konfigurację serwera usługi Azure Database for PostgreSQL. |
| [az postgres server configuration show](/cli/azure/postgres/server/configuration) | Pokazuje konfigurację serwera usługi Azure Database for PostgreSQL. |
| [az group delete](/cli/azure/group) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki
- Przeczytaj więcej informacji na temat interfejsu wiersza polecenia platformy Azure: [Dokumentacja interfejsu wiersza polecenia platformy Azure](/cli/azure).
- Wypróbuj dodatkowe skrypty: [Przykłady interfejsu wiersza polecenia platformy Azure dla usługi Azure Database for PostgreSQL](../sample-scripts-azure-cli.md)
- Aby uzyskać więcej informacji na temat parametrów serwera, zobacz [Jak konfigurować parametry serwera w witrynie Azure Portal](../howto-configure-server-parameters-using-portal.md).
