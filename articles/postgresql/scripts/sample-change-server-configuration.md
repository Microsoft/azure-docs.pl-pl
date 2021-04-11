---
title: Skrypt interfejsu wiersza polecenia platformy Azure — zmiana konfiguracji serwera (PostgreSQL)
description: Ten przykładowy skrypt interfejsu wiersza polecenia zwraca listę wszystkich dostępnych opcji konfiguracji serwera i aktualizuje wartość jednej z opcji.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 02/28/2018
ms.openlocfilehash: 1c3433c1f96dc32d91f1c07882f231202383e1c3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105607358"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-postgresql-server-using-azure-cli"></a>Uzyskiwanie listy i aktualizowanie konfiguracji serwera usługi Azure Database for PostgreSQL przy użyciu interfejsu wiersza polecenia platformy Azure
Ten przykładowy skrypt interfejsu wiersza polecenia zwraca listę wszystkich dostępnych parametrów konfiguracji oraz ich dopuszczalnych wartości dla serwera usługi Azure Database for PostgreSQL, a także ustawia parametr *log_retention_days* na wartość inną niż domyślna.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga wersji 2,0 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

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
