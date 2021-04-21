---
title: Uzyskiwanie dostępu do dzienników wolnych zapytań — interfejs wiersza polecenia platformy Azure — Azure Database for MariaDB
description: W tym artykule opisano sposób uzyskiwania dostępu do powolnych dzienników w usłudze Azure Database for MariaDB użyciu narzędzia wiersza polecenia interfejsu wiersza polecenia platformy Azure.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: how-to
ms.date: 4/13/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 42382076b6c14989eb153725e991c8ef047ad15b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774795"
---
# <a name="configure-and-access-azure-database-for-maria-db-slow-query-logs-by-using-azure-cli"></a>Konfigurowanie dzienników wolnych zapytań usługi Azure Database for Maria DB i uzyskiwanie do nich dostępu przy użyciu interfejsu wiersza polecenia platformy Azure

Dzienniki wolnych zapytań Azure Database for MariaDB pobrać przy użyciu interfejsu wiersza polecenia platformy Azure , narzędzia wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne
Aby przejść przez ten przewodnik, potrzebne są:
- [Azure Database for MariaDB serwera](quickstart-create-mariadb-server-database-using-azure-cli.md)
- Interfejs [wiersza polecenia](/cli/azure/install-azure-cli) platformy Azure Azure Cloud Shell w przeglądarce

## <a name="configure-logging"></a>Konfigurowanie rejestrowania
Aby skonfigurować serwer do uzyskiwania dostępu do dziennika wolnych zapytań MySQL, należy wykonać następujące czynności:
1. Włącz rejestrowanie wolnych zapytań, ustawiając dla parametru **dziennika \_ \_ wolnych zapytań** wartość WŁĄCZONE.
2. Wybierz miejsce, do którego mają być wyprowadzane dzienniki, korzystając z **danych \_ wyjściowych dziennika**. Aby wysłać dzienniki zarówno do magazynu lokalnego, jak i do Azure Monitor diagnostycznych, wybierz **pozycję Plik**. Aby wysyłać dzienniki tylko do Azure Monitor, wybierz pozycję **Brak**
3. Dostosuj inne parametry, takie jak **długi \_ czas wykonywania \_ zapytania** i **rejestrowanie \_ \_ wolnych instrukcji \_ administratora.**

Aby dowiedzieć się, jak ustawić wartość tych parametrów za pomocą interfejsu wiersza polecenia platformy Azure, zobacz [Jak skonfigurować parametry serwera](howto-configure-server-parameters-cli.md).

Na przykład następujące polecenie interfejsu wiersza polecenia włącza dziennik wolnych zapytań, ustawia długi czas zapytania na 10 sekund, a następnie wyłącza rejestrowanie powolnej instrukcji administratora. Na koniec wyświetla listę opcji konfiguracji do przeglądu.
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mariadb server configuration set --name log_output --resource-group myresourcegroup --server mydemoserver --value FILE
az mariadb server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mariadb server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mariadb-server"></a>Lista dzienników dla Azure Database for MariaDB serwera
Jeśli **log_output** skonfigurowano "Plik", możesz uzyskać dostęp do dzienników bezpośrednio z lokalnego magazynu serwera. Aby wyświetlić listę dostępnych plików dziennika wolnych zapytań dla serwera, uruchom [polecenie az mariadb server-logs list.](/cli/azure/mariadb/server-logs#az_mariadb_server_logs_list)

Listę plików dziennika dla serwerów można wyświetlić **mydemoserver.mariadb.database.azure.com** w grupie zasobów **myresourcegroup**. Następnie przekieruj listę plików dziennika do pliku tekstowego o nazwie **pliki \_ dziennika \_list.txt**.
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Pobieranie dzienników z serwera
Jeśli **log_output** na "Plik", możesz pobrać poszczególne pliki dziennika z serwera za pomocą polecenia [az mariadb server-logs download.](/cli/azure/mariadb/server-logs#az_mariadb_server_logs_download)

Skorzystaj z poniższego przykładu, aby pobrać określony plik dziennika dla serwera **mydemoserver.mariadb.database.azure.com** grupie zasobów **myresourcegroup** do środowiska lokalnego.
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej [na temat dzienników wolnych zapytań w Azure Database for MariaDB](concepts-server-logs.md).
