---
title: Skrypt interfejsu wiersza polecenia — tworzenie serwera — Azure Database for MySQL
description: Ten przykładowy skrypt interfejsu wiersza polecenia platformy Azure tworzy serwer usługi Azure Database for MySQL i konfiguruje regułę zapory na poziomie serwera.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.custom: mvc, devx-track-azurecli
ms.topic: sample
ms.date: 12/02/2019
ms.openlocfilehash: 5859af08a8343c0640cdae293d001a1f143f4629
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763267"
---
# <a name="create-a-mysql-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>Tworzenie serwera MySQL i konfigurowanie reguły zapory przy użyciu polecenia interfejsu wiersza polecenia platformy Azure
Ten przykładowy skrypt interfejsu wiersza polecenia platformy Azure tworzy serwer usługi Azure Database for MySQL i konfiguruje regułę zapory na poziomie serwera. Po pomyślnym uruchomieniu skryptu serwer MySQL jest dostępny dla wszystkich usług platformy Azure oraz skonfigurowanego adresu IP.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana. 

## <a name="sample-script"></a>Przykładowy skrypt
W tym przykładowym skrypcie dokonaj edycji wyróżnionych wierszy w celu zmiany nazwy użytkownika i hasła administratora na swoje własne.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/create-mysql-server-and-firewall-rule/create-mysql-server-and-firewall-rule.sh?highlight=15-16 "Create an Azure Database for MySQL, and server-level firewall rule.")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia
Po uruchomieniu skryptu użyj następującego polecenia, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby. 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/create-mysql-server-and-firewall-rule/delete-mysql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu
Ten skrypt używa poleceń opisanych w poniższej tabeli:

| **Polecenia** | **Uwagi** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az mysql server create](/cli/azure/mysql/server#az_mysql_server_create) | Tworzy serwer MySQL hostujący bazy danych. |
| [az mysql server firewall create](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_create) | Tworzy regułę zapory zezwalającą na dostęp do serwera i baz danych na serwerze z adresów IP należących do podanego zakresu. |
| [az group delete](/cli/azure/group#az_group_delete) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki
- Przeczytaj więcej informacji na temat interfejsu wiersza polecenia platformy Azure: [dokumentacja interfejsu wiersza polecenia platformy Azure.](/cli/azure)
- Wypróbuj dodatkowe skrypty: [Przykłady interfejsu wiersza polecenia platformy Azure dla usługi Azure Database for MySQL](../sample-scripts-azure-cli.md)
