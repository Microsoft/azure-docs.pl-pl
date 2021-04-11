---
title: Skrypt interfejsu wiersza polecenia platformy Azure — pobieranie dzienników serwera w usłudze Azure Database for PostgreSQL
description: Ten przykładowy skrypt interfejsu wiersza polecenia platformy Azure pokazuje, jak włączyć i pobrać dzienniki serwera usługi Azure Database for PostgreSQL.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 02/28/2018
ms.openlocfilehash: 3011182794df1c6d60fe286e48c3173dfcfcbe24
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605250"
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-postgresql-server-using-azure-cli"></a>Włączanie i pobieranie dzienników wolnych zapytań serwera usługi Azure Database for PostgreSQL przy użyciu interfejsu wiersza polecenia platformy Azure
Ten przykładowy skrypt interfejsu wiersza polecenia włącza i pobiera dzienniki wolnych zapytań jednego serwera usługi Azure Database for PostgreSQL.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga wersji 2,0 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="sample-script"></a>Przykładowy skrypt
W tym przykładowym skrypcie dokonaj edycji wyróżnionych wierszy w celu zmiany nazwy użytkownika i hasła administratora na swoje własne. Zastąp element &lt;log_file_name&gt; w poleceniach `az monitor` własną nazwą pliku dziennika serwera.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/server-logs/server-logs.sh?highlight=15-16 "Manipulate with server logs.")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia
Po uruchomieniu skryptu użyj następującego polecenia, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/server-logs/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu
Ten skrypt używa poleceń opisanych w poniższej tabeli:

| **Polecenie** | **Uwagi** |
|---|---|
| [az group create](/cli/azure/group) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az postgres server create](/cli/azure/postgres/server) | Tworzy serwer PostgreSQL hostujący bazy danych. |
| [az postgres server configuration list](/cli/azure/postgres/server/configuration) | Zwraca listę wartości konfiguracji dla serwera. |
| [az postgres server configuration set](/cli/azure/postgres/server/configuration) | Aktualizuje konfigurację serwera. |
| [az postgres server-logs list](/cli/azure/postgres/server-logs) | Zwraca listę plików dziennika dla serwera. |
| [az postgres server-logs download](/cli/azure/postgres/server-logs) | Pobiera pliki dziennika. |
| [az group delete](/cli/azure/group) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki
- Przeczytaj więcej informacji na temat interfejsu wiersza polecenia platformy Azure: [Dokumentacja interfejsu wiersza polecenia platformy Azure](/cli/azure).
- Wypróbuj dodatkowe skrypty: [Przykłady interfejsu wiersza polecenia platformy Azure dla usługi Azure Database for PostgreSQL](../sample-scripts-azure-cli.md)
- [Konfigurowanie dzienników serwera i uzyskiwanie do nich dostępu w witrynie Azure Portal](../howto-configure-server-logs-in-portal.md)
