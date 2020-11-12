---
title: Dostęp do dzienników wolnych zapytań — interfejs wiersza polecenia platformy Azure — Azure Database for MariaDB
description: W tym artykule opisano, jak uzyskać dostęp do wolnych dzienników w Azure Database for MariaDB przy użyciu narzędzia wiersza polecenia platformy Azure.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: how-to
ms.date: 4/13/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: cea1571a48afe00350dde247c3c10e222a446247
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94539876"
---
# <a name="configure-and-access-azure-database-for-maria-db-slow-query-logs-by-using-azure-cli"></a>Konfigurowanie i uzyskiwanie dostępu do dzienników wolnych zapytań usługi Azure Database for Maria DB za pomocą interfejsu wiersza polecenia platformy Azure

Możesz pobrać Azure Database for MariaDB dzienników wolnych zapytań za pomocą interfejsu wiersza polecenia platformy Azure, narzędzia wiersza poleceń platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne
Aby krokowo poprowadzić ten przewodnik, musisz:
- [Serwer Azure Database for MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md)
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub Azure Cloud Shell w przeglądarce

## <a name="configure-logging"></a>Konfigurowanie rejestrowania
Serwer można skonfigurować tak, aby mógł uzyskać dostęp do dziennika wolnych zapytań programu MySQL, wykonując następujące czynności:
1. Włącz rejestrowanie wolnych zapytań, ustawiając parametr **\_ \_ dziennika wolnych zapytań** na wartość włączone.
2. Wybierz lokalizację, do której mają być wyprowadzane dzienniki, aby użyć **\_ danych wyjściowych dziennika**. Aby wysłać dzienniki do magazynu lokalnego i Azure Monitor dzienników diagnostycznych, wybierz pozycję **plik**. Aby wysyłać dzienniki tylko do dzienników Azure Monitor, zaznacz opcję **Brak**
3. Dostosuj inne parametry, takie jak **długi \_ \_ czas zapytania** i **Rejestruj \_ wolne \_ \_ instrukcje administratora**.

Aby dowiedzieć się, jak ustawić wartości tych parametrów za pomocą interfejsu wiersza polecenia platformy Azure, zobacz [How to configure Server Parameters](howto-configure-server-parameters-cli.md).

Na przykład następujące polecenie interfejsu wiersza polecenia włącza dziennik wolnych zapytań, ustawia długi czas zapytania na 10 sekund, a następnie wyłącza rejestrowanie powolnej instrukcji administratora. Na koniec wyświetla listę opcji konfiguracji dla przeglądu.
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mariadb server configuration set --name log_output --resource-group myresourcegroup --server mydemoserver --value FILE
az mariadb server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mariadb server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mariadb-server"></a>Wyświetlanie listy dzienników dla Azure Database for MariaDB Server
Jeśli **log_output** jest skonfigurowany do "plik", można uzyskać dostęp do dzienników bezpośrednio z magazynu lokalnego na serwerze. Aby wyświetlić listę dostępnych wolnych plików dziennika zapytań dla serwera, uruchom polecenie [AZ MariaDB Server-Logs list](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list) .

Możesz wyświetlić listę plików dziennika dla serwera **mydemoserver.MariaDB.Database.Azure.com** w **obszarze Grupa zasobów**. Następnie należy skierować listę plików dziennika do pliku tekstowego o nazwie **pliki dzienników \_ \_list.txt**.
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Pobierz dzienniki z serwera
Jeśli **log_output** jest skonfigurowany do "plik", można pobrać pojedyncze pliki dziennika z serwera za pomocą polecenia [AZ MariaDB Server-Logs Download](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download) .

Użyj poniższego przykładu, aby pobrać określony plik dziennika dla serwera **mydemoserver.MariaDB.Database.Azure.com** w **obszarze Grupa zasobów zasobu do** środowiska lokalnego.
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [wolnych dziennikach zapytań w Azure Database for MariaDB](concepts-server-logs.md).
