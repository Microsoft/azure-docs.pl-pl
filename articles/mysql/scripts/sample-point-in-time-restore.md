---
title: Skrypt interfejsu wiersza polecenia — przywracanie serwera-Azure Database for MySQL
description: Ten przykładowy skrypt interfejsu wiersza polecenia platformy Azure pokazuje sposób przywracania serwera usługi Azure Database for PostgreSQL i jego baz danych do wcześniejszego punktu w czasie.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 12/02/2019
ms.openlocfilehash: 5a84a44a4ea941bf3b77f0762fc764ec069919c7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "74771332"
---
# <a name="restore-an-azure-database-for-mysql-server-using-azure-cli"></a>Przywracanie serwera usługi Azure Database for MySQL za pomocą interfejsu wiersza polecenia platformy Azure
Ten przykładowy skrypt interfejsu wiersza polecenia przywraca pojedynczy serwer usługi Azure Database for MySQL do wcześniejszego punktu w czasie.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się uruchomić interfejs wiersza polecenia lokalnie, na potrzeby tego artykułu wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Sprawdź wersję, uruchamiając polecenie `az --version`. Aby zainstalować lub uaktualnić interfejs wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Przykładowy skrypt
W tym przykładowym skrypcie dokonaj edycji wyróżnionych wierszy w celu zmiany nazwy użytkownika i hasła administratora na swoje własne. Zastąp identyfikator subskrypcji używany w poleceniach `az monitor` własnym identyfikatorem subskrypcji.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/backup-restore-pitr/backup-restore.sh?highlight=15-16 "Restore Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia
Po uruchomieniu skryptu użyj następującego polecenia, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby. 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/backup-restore-pitr/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu
Ten skrypt używa poleceń opisanych w poniższej tabeli:

| **Polecenie** | **Uwagi** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az mysql server create](/cli/azure/mysql/server#az-mysql-server-create) | Tworzy serwer MySQL hostujący bazy danych. |
| [az mysql server restore](/cli/azure/mysql/server#az-mysql-server-restore) | Przywraca serwer z kopii zapasowej. |
| [az group delete](/cli/azure/group#az-group-delete) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki
- Przeczytaj więcej informacji na temat interfejsu wiersza polecenia platformy Azure: [Dokumentacja interfejsu wiersza polecenia platformy Azure](/cli/azure).
- Wypróbuj dodatkowe skrypty: [Przykłady interfejsu wiersza polecenia platformy Azure dla usługi Azure Database for MySQL](../sample-scripts-azure-cli.md)
