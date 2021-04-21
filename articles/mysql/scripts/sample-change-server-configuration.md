---
title: Skrypt interfejsu wiersza polecenia — zmienianie parametrów serwera — Azure Database for MySQL
description: Ten przykładowy skrypt interfejsu wiersza polecenia zwraca listę wszystkich dostępnych konfiguracji serwera i aktualizuje wartość opcji innodb_lock_wait_timeout.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: eea11eb79cda47a11456178100941db05f0f2fa1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791682"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-mysql-server-using-azure-cli"></a>Uzyskiwanie listy i aktualizowanie konfiguracji serwera usługi Azure Database for MySQL za pomocą interfejsu wiersza polecenia platformy Azure
Ten przykładowy skrypt interfejsu wiersza polecenia zwraca listę wszystkich dostępnych parametrów konfiguracji oraz ich dopuszczalnych wartości dla serwera usługi Azure Database for MySQL, a także ustawia parametr *innodb_lock_wait_timeout* na wartość inną niż domyślna.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana. 

## <a name="sample-script"></a>Przykładowy skrypt
W tym przykładowym skrypcie dokonaj edycji wyróżnionych wierszy w celu zmiany nazwy użytkownika i hasła administratora na swoje własne.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia
Po uruchomieniu skryptu użyj następującego polecenia, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby. 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/change-server-configurations/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu
Ten skrypt używa poleceń opisanych w poniższej tabeli:

| **Polecenia** | **Uwagi** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az mysql server create](/cli/azure/mysql/server#az_mysql_server_create) | Tworzy serwer MySQL hostujący bazy danych. |
| [az mysql server configuration list](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_list) | Zwraca listę konfiguracji serwera usługi Azure Database for MySQL. |
| [az mysql server configuration set](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_set) | Aktualizuje konfigurację serwera usługi Azure Database for MySQL. |
| [az mysql server configuration show](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_show) | Wyświetla konfigurację serwera usługi Azure Database for MySQL. |
| [az group delete](/cli/azure/group#az_group_delete) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki
- Przeczytaj więcej informacji na temat interfejsu wiersza polecenia platformy Azure: [dokumentacja interfejsu wiersza polecenia platformy Azure.](/cli/azure)
- Wypróbuj dodatkowe skrypty: [Przykłady interfejsu wiersza polecenia platformy Azure dla usługi Azure Database for MySQL](../sample-scripts-azure-cli.md)
- Aby uzyskać więcej informacji na temat parametrów serwera, zobacz [Jak konfigurować parametry serwera w usłudze Azure Database for MySQL](../howto-server-parameters.md).
