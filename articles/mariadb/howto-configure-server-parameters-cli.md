---
title: Konfigurowanie parametrów serwera — interfejs wiersza polecenia platformy Azure — Azure Database for MariaDB
description: W tym artykule opisano sposób konfigurowania parametrów usługi w usłudze Azure Database for MariaDB użyciu narzędzia wiersza polecenia interfejsu wiersza polecenia platformy Azure.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: how-to
ms.date: 10/1/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: a3ebcf5a381efceb5e7de503caf88f4ffd3b504e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774741"
---
# <a name="configure-server-parameters-in-azure-database-for-mariadb-using-the-azure-cli"></a>Konfigurowanie parametrów serwera w usłudze Azure Database for MariaDB użyciu interfejsu wiersza polecenia platformy Azure
Parametry konfiguracji dla serwera usługi Azure Database for MariaDB można wyświetlać, wyświetlać i aktualizować przy użyciu interfejsu wiersza polecenia platformy Azure — narzędzia wiersza polecenia platformy Azure. Podzbiór konfiguracji aparatu jest dostępny na poziomie serwera i można go modyfikować.

>[!Note]
> Parametry serwera można zaktualizować globalnie na poziomie serwera, przy użyciu [interfejsu wiersza polecenia platformy Azure](./howto-configure-server-parameters-cli.md), programu [PowerShell](./howto-configure-server-parameters-using-powershell.md) lub witryny [Azure Portal](./howto-server-parameters.md).

## <a name="prerequisites"></a>Wymagania wstępne
Aby przejść przez ten przewodnik, potrzebne są:
- [Serwer Azure Database for MariaDB danych](quickstart-create-mariadb-server-database-using-azure-cli.md)
- [Narzędzie wiersza](/cli/azure/install-azure-cli) polecenia platformy Azure lub użyj Azure Cloud Shell w przeglądarce.

## <a name="list-server-configuration-parameters-for-azure-database-for-mariadb-server"></a>Lista parametrów konfiguracji serwera dla Azure Database for MariaDB serwera
Aby wyświetlić listę wszystkich parametrów modyfikowalnych na serwerze i ich wartości, uruchom [polecenie az mariadb server configuration list.](/cli/azure/mariadb/server/configuration#az_mariadb_server_configuration_list)

Parametry konfiguracji serwera dla serwera można wyświetlić w mydemoserver.mariadb.database.azure.com **grupy** zasobów **myresourcegroup.**
```azurecli-interactive
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

Aby uzyskać definicję każdego z wymienionych parametrów, zobacz sekcję referencyjną bazy danych MariaDB w [tece Server System Variables](https://mariadb.com/kb/en/library/server-system-variables/)( Zmienne systemowe serwera).

## <a name="show-server-configuration-parameter-details"></a>Wyświetlanie szczegółów parametru konfiguracji serwera
Aby wyświetlić szczegółowe informacje o konkretnym parametrze konfiguracji dla serwera, uruchom [polecenie az mariadb server configuration show.](/cli/azure/mariadb/server/configuration#az_mariadb_server_configuration_show)

W tym przykładzie przedstawiono szczegóły **parametru konfiguracji serwera \_ \_** dziennika wolnych zapytań dla serwera **mydemoserver.mariadb.database.azure.com** w grupie zasobów **myresourcegroup.**
```azurecli-interactive
az mariadb server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Modyfikowanie wartości parametru konfiguracji serwera
Można również zmodyfikować wartość określonego parametru konfiguracji serwera, który aktualizuje podstawową wartość konfiguracji aparatu serwera MariaDB. Aby zaktualizować konfigurację, użyj [polecenia az mariadb server configuration set.](/cli/azure/mariadb/server/configuration#az_mariadb_server_configuration_set) 

Aby zaktualizować **parametr \_ konfiguracji serwera \_** dziennika wolnych zapytań serwera **mydemoserver.mariadb.database.azure.com** w grupie zasobów **myresourcegroup.**
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```

Jeśli chcesz zresetować wartość parametru konfiguracji, pomiń opcjonalny parametr, a `--value` usługa stosuje wartość domyślną. W powyższym przykładzie wyglądałoby to tak:
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```

Ten kod resetuje **konfigurację dziennika \_ \_ wolnych zapytań** do wartości domyślnej **WYŁ.**. 

## <a name="setting-parameters-not-listed"></a>Nie wymieniono parametrów ustawień
Jeśli parametr serwera, który chcesz zaktualizować, nie znajduje się na liście Azure Portal, możesz opcjonalnie ustawić parametr na poziomie połączenia przy użyciu . `init_connect` W ten sposób ustawiane są parametry serwera dla każdego klienta łączącego się z serwerem. 

Zaktualizuj parametr **konfiguracji serwera init \_ connect** serwera mydemoserver.mariadb.database.azure.com **w** grupie zasobów **myresourcegroup,** aby ustawić wartości, takie jak zestaw znaków.
```azurecli-interactive
az mariadb server configuration set --name init_connect --resource-group myresourcegroup --server mydemoserver --value "SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;"
```

## <a name="working-with-the-time-zone-parameter"></a>Praca z parametrem strefy czasowej

### <a name="populating-the-time-zone-tables"></a>Wypełnianie tabel strefy czasowej

Tabele stref czasowych na serwerze można wypełnić, wywołując procedurę składowaną z narzędzia, takiego jak wiersz polecenia mariaDB lub `mysql.az_load_timezone` program MariaDB Workbench.

> [!NOTE]
> Jeśli uruchamiasz polecenie `mysql.az_load_timezone` z aplikacji MariaDB Workbench, może być konieczne najpierw wyłączenie trybu bezpiecznej aktualizacji przy użyciu polecenia `SET SQL_SAFE_UPDATES=0;` .

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Należy ponownie uruchomić serwer, aby upewnić się, że tabele strefy czasowej są prawidłowo wypełnione. Aby ponownie uruchomić serwer, użyj [interfejsu Azure Portal](howto-restart-server-portal.md) interfejsu [wiersza polecenia](howto-restart-server-cli.md).

Aby wyświetlić dostępne wartości strefy czasowej, uruchom następujące polecenie:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Ustawianie strefy czasowej na poziomie globalnym

Strefę czasową na poziomie globalnym można ustawić za pomocą [polecenia az mariadb server configuration set.](/cli/azure/mariadb/server/configuration#az_mariadb_server_configuration_set)

Następujące polecenie aktualizuje parametr konfiguracji serwera strefy czasowej serwera mydemoserver.mariadb.database.azure.com **w** grupie zasobów **myresourcegroup** na **US/Pacific.** **\_**

```azurecli-interactive
az mariadb server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Ustawianie strefy czasowej na poziomie sesji

Strefę czasową na poziomie sesji można ustawić, uruchamiając polecenie z poziomu narzędzia, takiego jak wiersz polecenia `SET time_zone` mariaDB lub program MariaDB Workbench. W poniższym przykładzie ustawia  strefę czasową na strefę czasową USA/Pacyfik.  

```sql
SET time_zone = 'US/Pacific';
```

Zapoznaj się z dokumentacją bazy danych MariaDB dotyczącą [funkcji daty i czasu.](https://mariadb.com/kb/en/library/date-time-functions/)

## <a name="next-steps"></a>Następne kroki

- Jak skonfigurować [parametry serwera w Azure Portal](howto-server-parameters.md)
