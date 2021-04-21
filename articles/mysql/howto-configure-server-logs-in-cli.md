---
title: Uzyskiwanie dostępu do dzienników wolnych zapytań — interfejs wiersza polecenia platformy Azure — Azure Database for MySQL
description: W tym artykule opisano sposób uzyskiwania dostępu do dzienników wolnych zapytań w usłudze Azure Database for MySQL użyciu interfejsu wiersza polecenia platformy Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 4/13/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1d5fc2b14a655251e59a9209e078b0534f08baf9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763239"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Konfigurowanie dzienników wolnych zapytań i uzyskiwanie do nich dostępu przy użyciu interfejsu wiersza polecenia platformy Azure
Dzienniki wolnych zapytań Azure Database for MySQL pobrać przy użyciu interfejsu wiersza polecenia platformy Azure — narzędzia wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne
Aby przejść przez ten przewodnik, potrzebne są:
- [Azure Database for MySQL serwera](quickstart-create-mysql-server-database-using-azure-cli.md)
- Interfejs [wiersza polecenia](/cli/azure/install-azure-cli) platformy Azure Azure Cloud Shell w przeglądarce

## <a name="configure-logging"></a>Konfigurowanie rejestrowania
Aby skonfigurować serwer do uzyskiwania dostępu do dziennika wolnych zapytań MySQL, należy wykonać następujące czynności:
1. Włącz rejestrowanie wolnych zapytań, ustawiając dla parametru **dziennika \_ \_ wolnych zapytań** wartość WŁĄCZONE.
2. Wybierz miejsce, do którego mają być wyprowadzane dzienniki, korzystając z **danych \_ wyjściowych dziennika**. Aby wysłać dzienniki zarówno do magazynu lokalnego, jak i do Azure Monitor diagnostycznych, wybierz **pozycję Plik**. Aby wysyłać dzienniki tylko do Azure Monitor, wybierz pozycję **Brak**
3. Dostosuj inne parametry, takie jak **długi \_ czas wykonywania \_ zapytania** i **rejestrowanie \_ \_ wolnych instrukcji \_ administratora.**

Aby dowiedzieć się, jak ustawić wartość tych parametrów za pomocą interfejsu wiersza polecenia platformy Azure, zobacz [Jak skonfigurować parametry serwera](howto-configure-server-parameters-using-cli.md).

Na przykład następujące polecenie interfejsu wiersza polecenia włącza dziennik wolnych zapytań, ustawia długi czas zapytania na 10 sekund, a następnie wyłącza rejestrowanie powolnej instrukcji administratora. Na koniec wyświetla listę opcji konfiguracji do przeglądu.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name log_output --resource-group myresourcegroup --server mydemoserver --value FILE
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Lista dzienników dla Azure Database for MySQL serwera
Jeśli **log_output** skonfigurowano "Plik", możesz uzyskać dostęp do dzienników bezpośrednio z lokalnego magazynu serwera. Aby wyświetlić listę dostępnych plików dziennika wolnych zapytań dla serwera, uruchom [polecenie az mysql server-logs list.](/cli/azure/mysql/server-logs#az_mysql_server_logs_list)

Możesz wyświetlić listę plików dziennika dla serwera **mydemoserver.mysql.database.azure.com** w grupie zasobów **myresourcegroup**. Następnie przekieruj listę plików dziennika do pliku tekstowego o nazwie **pliki \_list.txt\_**.
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Pobieranie dzienników z serwera
Jeśli **log_output** na "Plik", możesz pobrać poszczególne pliki dziennika z serwera za pomocą polecenia [az mysql server-logs download.](/cli/azure/mysql/server-logs#az_mysql_server_logs_download)

Użyj poniższego przykładu, aby pobrać określony plik dziennika dla serwera **mydemoserver.mysql.database.azure.com** w grupie zasobów **myresourcegroup** do środowiska lokalnego.
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej [o dziennikach wolnych zapytań w Azure Database for MySQL](concepts-server-logs.md).
