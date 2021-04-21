---
title: Skrypt interfejsu wiersza polecenia — pobieranie dzienników wolnych zapytań — Azure Database for MariaDB
description: Ten przykładowy skrypt interfejsu wiersza polecenia platformy Azure pokazuje, jak włączyć i pobrać dzienniki wolnych zapytań Azure Database for MariaDB serwera.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: 42b1806d3aa1235dfd976dd5700204aa0b9e4700
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785563"
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-mariadb-server-using-azure-cli"></a>Włączanie i pobieranie dzienników wolnych zapytań serwera usługi Azure Database for MariaDB przy użyciu interfejsu wiersza polecenia platformy Azure
Ten przykładowy skrypt interfejsu wiersza polecenia włącza i pobiera dzienniki wolnych zapytań jednego serwera usługi Azure Database for MariaDB.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="sample-script"></a>Przykładowy skrypt
W tym przykładowym skrypcie dokonaj edycji wyróżnionych wierszy w celu zmiany nazwy użytkownika i hasła administratora na swoje własne. Zastąp element &lt;log_file_name&gt; w poleceniach `az monitor` własną nazwą pliku dziennika serwera.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/server-logs/server-logs.sh?highlight=15-16 "Manipulate with server logs.")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia
Po uruchomieniu skryptu użyj następującego polecenia, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby. 
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/server-logs/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu
Ten skrypt używa poleceń opisanych w poniższej tabeli:

| **Polecenia** | **Uwagi** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az mariadb server create](/cli/azure/mariadb/server#az_mariadb_server_create) | Tworzy serwer MariaDB hostujący bazy danych. |
| [az mariadb server configuration list](/cli/azure/mariadb/server/configuration#az_mariadb_server_configuration_list) | Zwraca listę wartości konfiguracji dla serwera. |
| [az mariadb server configuration set](/cli/azure/mariadb/server/configuration#az_mariadb_server_configuration_set) | Aktualizuje konfigurację serwera. |
| [az mariadb server-logs list](/cli/azure/mariadb/server-logs#az_mariadb_server_logs_list) | Zwraca listę plików dziennika dla serwera. |
| [az mariadb server-logs download](/cli/azure/mariadb/server-logs#az_mariadb_server_logs_download) | Pobiera pliki dziennika. |
| [az group delete](/cli/azure/group#az_group_delete) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki
- Przeczytaj więcej informacji na temat interfejsu wiersza polecenia platformy Azure: [dokumentacja interfejsu wiersza polecenia platformy Azure.](/cli/azure)
- Wypróbuj dodatkowe skrypty: [Przykłady interfejsu wiersza polecenia platformy Azure dla usługi Azure Database for MariaDB](../sample-scripts-azure-cli.md)
