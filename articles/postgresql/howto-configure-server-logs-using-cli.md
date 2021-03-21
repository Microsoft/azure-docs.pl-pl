---
title: Zarządzanie dziennikami — interfejs wiersza polecenia platformy Azure — Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano sposób konfigurowania i uzyskiwania dostępu do dzienników serwera (plików dziennika) w Azure Database for PostgreSQL-pojedynczym serwerze przy użyciu interfejsu wiersza polecenia platformy Azure.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 71dac0b6ea2202e712280607a73e860ae68bdb73
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96005592"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Konfigurowanie i uzyskiwanie dostępu do dzienników serwera przy użyciu interfejsu wiersza polecenia platformy Azure
Dzienniki błędów serwera PostgreSQL można pobrać przy użyciu interfejsu wiersza polecenia (Azure CLI). Jednak dostęp do dzienników transakcji nie jest obsługiwany. 

## <a name="prerequisites"></a>Wymagania wstępne
Aby krokowo poprowadzić ten przewodnik, musisz:
- [Serwer Azure Database for PostgreSQL](quickstart-create-server-database-azure-cli.md)
- Narzędzie wiersza polecenia [platformy Azure](/cli/azure/install-azure-cli) lub Azure Cloud Shell w przeglądarce

## <a name="configure-logging"></a>Konfigurowanie rejestrowania
Serwer można skonfigurować tak, aby mógł uzyskiwać dostęp do dzienników zapytań i dzienników błędów. Dzienniki błędów mogą mieć informacje dotyczące autopróżni, połączeń i punktów kontrolnych.
1. Włącz rejestrowanie.
2. Aby włączyć rejestrowanie zapytań, zaktualizuj **\_ instrukcję log** i **log \_ min \_ duration \_**.
3. Aktualizowanie okresu przechowywania.

Aby uzyskać więcej informacji, zobacz [Dostosowywanie parametrów konfiguracji serwera](howto-configure-server-parameters-using-cli.md).

## <a name="list-logs"></a>Wyświetl listę dzienników
Aby wyświetlić listę dostępnych plików dziennika dla serwera, uruchom polecenie [AZ Postgres Server-Logs list](/cli/azure/postgres/server-logs) .

Możesz wyświetlić listę plików dziennika dla serwera **mydemoserver.Postgres.Database.Azure.com** w **obszarze Grupa zasobów**. Następnie należy skierować listę plików dziennika do pliku tekstowego o nazwie **pliki dzienników \_ \_list.txt**.
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Pobierz dzienniki lokalnie z serwera
Za pomocą polecenia [AZ Postgres Server-Logs Download](/cli/azure/postgres/server-logs) można pobrać pojedyncze pliki dziennika dla serwera. 

Użyj poniższego przykładu, aby pobrać określony plik dziennika dla serwera **mydemoserver.Postgres.Database.Azure.com** w **obszarze Grupa zasobów zasobu do** środowiska lokalnego.
```azurecli-interactive
az postgres server-logs download --name 20170414-mydemoserver-postgresql.log --resource-group myresourcegroup --server mydemoserver
```
## <a name="next-steps"></a>Następne kroki
- Aby dowiedzieć się więcej o dziennikach serwera, zobacz [Dzienniki serwera w Azure Database for PostgreSQL](concepts-server-logs.md).
- Aby uzyskać więcej informacji na temat parametrów serwera, zobacz [Dostosowywanie parametrów konfiguracji serwera przy użyciu interfejsu wiersza polecenia platformy Azure](howto-configure-server-parameters-using-cli.md).
