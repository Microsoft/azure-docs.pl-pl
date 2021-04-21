---
title: 'Skrypt interfejsu wiersza polecenia platformy Azure: przywracanie serwera usługi Azure Database for PostgreSQL'
description: W ramach tego przykładowego skryptu interfejsu wiersza polecenia platformy Azure przedstawiono sposób przywracania serwera usługi Azure Database for PostgreSQL i jego baz danych do wcześniejszego punktu w czasie.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 02/28/2018
ms.openlocfilehash: 6d95c06efa347618a7c571b65f01f147aeb5ef1f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778434"
---
# <a name="restore-an-azure-database-for-postgresql-server-using-azure-cli"></a>Przywracanie serwera usługi Azure Database for PostgreSQL przy użyciu interfejsu wiersza polecenia platformy Azure
Ten przykładowy skrypt interfejsu wiersza polecenia przywraca jeden serwer usługi Azure Database for PostgreSQL do wcześniejszego punktu w czasie.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="sample-script"></a>Przykładowy skrypt
W tym przykładowym skrypcie dokonaj edycji wyróżnionych wierszy w celu zmiany nazwy użytkownika i hasła administratora na swoje własne. Zastąp identyfikator subskrypcji używany w poleceniach `az monitor` własnym identyfikatorem subskrypcji.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/backup-restore/backup-restore.sh?highlight=15-16 "Restore Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia
Po uruchomieniu skryptu użyj następującego polecenia, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/backup-restore/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu
Ten skrypt używa poleceń opisanych w poniższej tabeli:

| **Polecenia** | **Uwagi** |
|---|---|
| [az group create](/cli/azure/group) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az postgresql server create](/cli/azure/postgres/server#az_postgres_server_create) | Tworzy serwer PostgreSQL hostujący bazy danych. |
| [az postgresql server restore](/cli/azure/postgres/server#az_postgres_server_restore) | Przywraca serwer z kopii zapasowej. |
| [az group delete](/cli/azure/group) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki
- Przeczytaj więcej informacji na temat interfejsu wiersza polecenia platformy Azure: [dokumentacja interfejsu wiersza polecenia platformy Azure.](/cli/azure)
- Wypróbuj dodatkowe skrypty: [Przykłady interfejsu wiersza polecenia platformy Azure dla usługi Azure Database for PostgreSQL](../sample-scripts-azure-cli.md)
- [Sposób wykonywania kopii zapasowej i przywracania serwera w usłudze Azure Database for PostgreSQL przy użyciu witryny Azure Portal](../howto-restore-server-portal.md)
