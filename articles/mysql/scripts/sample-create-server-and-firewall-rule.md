---
title: Skrypt interfejsu wiersza polecenia — Tworzenie serwera Azure Database for MySQL
description: Ten przykładowy skrypt interfejsu wiersza polecenia platformy Azure tworzy serwer usługi Azure Database for MySQL i konfiguruje regułę zapory na poziomie serwera.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.custom: mvc
ms.topic: sample
ms.date: 12/02/2019
ms.openlocfilehash: a210d2d11c9d9fccd000a6dffbf1f8730bda0c8b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "74765801"
---
# <a name="create-a-mysql-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>Tworzenie serwera MySQL i konfigurowanie reguły zapory przy użyciu polecenia interfejsu wiersza polecenia platformy Azure
Ten przykładowy skrypt interfejsu wiersza polecenia platformy Azure tworzy serwer usługi Azure Database for MySQL i konfiguruje regułę zapory na poziomie serwera. Po pomyślnym uruchomieniu skryptu serwer MySQL jest dostępny dla wszystkich usług platformy Azure oraz skonfigurowanego adresu IP.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się uruchomić interfejs wiersza polecenia lokalnie, na potrzeby tego artykułu wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Sprawdź wersję, uruchamiając polecenie `az --version`. Aby zainstalować lub uaktualnić interfejs wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Przykładowy skrypt
W tym przykładowym skrypcie dokonaj edycji wyróżnionych wierszy w celu zmiany nazwy użytkownika i hasła administratora na swoje własne.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/create-mysql-server-and-firewall-rule/create-mysql-server-and-firewall-rule.sh?highlight=15-16 "Create an Azure Database for MySQL, and server-level firewall rule.")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia
Po uruchomieniu skryptu użyj następującego polecenia, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby. 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/create-mysql-server-and-firewall-rule/delete-mysql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu
Ten skrypt używa poleceń opisanych w poniższej tabeli:

| **Polecenie** | **Uwagi** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az mysql server create](/cli/azure/mysql/server#az-mysql-server-create) | Tworzy serwer MySQL hostujący bazy danych. |
| [az mysql server firewall create](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-create) | Tworzy regułę zapory zezwalającą na dostęp do serwera i baz danych na serwerze z adresów IP należących do podanego zakresu. |
| [az group delete](/cli/azure/group#az-group-delete) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki
- Przeczytaj więcej informacji na temat interfejsu wiersza polecenia platformy Azure: [Dokumentacja interfejsu wiersza polecenia platformy Azure](/cli/azure).
- Wypróbuj dodatkowe skrypty: [Przykłady interfejsu wiersza polecenia platformy Azure dla usługi Azure Database for MySQL](../sample-scripts-azure-cli.md)
