---
title: Dostęp do dzienników wolnych zapytań — interfejs wiersza polecenia platformy Azure — Azure Database for MySQL
description: W tym artykule opisano, jak uzyskać dostęp do dzienników wolnych zapytań w Azure Database for MySQL przy użyciu interfejsu wiersza polecenia platformy Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 4/13/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 945a67f81010a61adf814f6f6f422eba5001b48d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95998553"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Konfigurowanie i uzyskiwanie dostępu do dzienników wolnych zapytań za pomocą interfejsu wiersza polecenia platformy Azure
Możesz pobrać Azure Database for MySQL dzienników wolnych zapytań za pomocą interfejsu wiersza polecenia platformy Azure, narzędzia wiersza poleceń platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne
Aby krokowo poprowadzić ten przewodnik, musisz:
- [Serwer Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub Azure Cloud Shell w przeglądarce

## <a name="configure-logging"></a>Konfigurowanie rejestrowania
Serwer można skonfigurować tak, aby mógł uzyskać dostęp do dziennika wolnych zapytań programu MySQL, wykonując następujące czynności:
1. Włącz rejestrowanie wolnych zapytań, ustawiając parametr **\_ \_ dziennika wolnych zapytań** na wartość włączone.
2. Wybierz lokalizację, do której mają być wyprowadzane dzienniki, aby użyć **\_ danych wyjściowych dziennika**. Aby wysłać dzienniki do magazynu lokalnego i Azure Monitor dzienników diagnostycznych, wybierz pozycję **plik**. Aby wysyłać dzienniki tylko do dzienników Azure Monitor, zaznacz opcję **Brak**
3. Dostosuj inne parametry, takie jak **długi \_ \_ czas zapytania** i **Rejestruj \_ wolne \_ \_ instrukcje administratora**.

Aby dowiedzieć się, jak ustawić wartości tych parametrów za pomocą interfejsu wiersza polecenia platformy Azure, zobacz [How to configure Server Parameters](howto-configure-server-parameters-using-cli.md).

Na przykład następujące polecenie interfejsu wiersza polecenia włącza dziennik wolnych zapytań, ustawia długi czas zapytania na 10 sekund, a następnie wyłącza rejestrowanie powolnej instrukcji administratora. Na koniec wyświetla listę opcji konfiguracji dla przeglądu.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name log_output --resource-group myresourcegroup --server mydemoserver --value FILE
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Wyświetlanie listy dzienników dla Azure Database for MySQL Server
Jeśli **log_output** jest skonfigurowany do "plik", można uzyskać dostęp do dzienników bezpośrednio z magazynu lokalnego na serwerze. Aby wyświetlić listę dostępnych wolnych plików dziennika zapytań dla serwera, uruchom polecenie [AZ MySQL Server-Logs list](/cli/azure/mysql/server-logs#az-mysql-server-logs-list) .

Możesz wyświetlić listę plików dziennika dla serwera **mydemoserver.MySQL.Database.Azure.com** w **obszarze Grupa zasobów**. Następnie należy skierować listę plików dziennika do pliku tekstowego o nazwie **pliki dzienników \_ \_list.txt**.
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Pobierz dzienniki z serwera
Jeśli **log_output** jest skonfigurowany do "plik", można pobrać pojedyncze pliki dziennika z serwera za pomocą polecenia [AZ MySQL Server-Logs Download](/cli/azure/mysql/server-logs#az-mysql-server-logs-download) .

Użyj poniższego przykładu, aby pobrać określony plik dziennika dla serwera **mydemoserver.MySQL.Database.Azure.com** w **obszarze Grupa zasobów zasobu do** środowiska lokalnego.
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [wolnych dziennikach zapytań w Azure Database for MySQL](concepts-server-logs.md).
