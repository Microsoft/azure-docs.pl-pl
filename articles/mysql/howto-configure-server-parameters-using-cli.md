---
title: Konfigurowanie parametrów serwera — interfejs wiersza polecenia platformy Azure — Azure Database for MySQL
description: W tym artykule opisano sposób konfigurowania parametrów usługi w programie Azure Database for MySQL użyciu narzędzia wiersza polecenia interfejsu wiersza polecenia platformy Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 10/1/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 94dd7f72f6411934587c76850a05d6885e9f6862
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763221"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-using-the-azure-cli"></a>Konfigurowanie parametrów serwera w usłudze Azure Database for MySQL użyciu interfejsu wiersza polecenia platformy Azure
Parametry konfiguracji dla serwera usługi Azure Database for MySQL można wyświetlać, wyświetlać i aktualizować za pomocą interfejsu wiersza polecenia platformy Azure — narzędzia wiersza polecenia platformy Azure. Podzestaw konfiguracji aparatu jest dostępny na poziomie serwera i można go modyfikować. 

>[!Note]
> Parametry serwera można aktualizować globalnie na poziomie serwera, przy użyciu interfejsu wiersza polecenia platformy [Azure,](./howto-configure-server-parameters-using-cli.md) [programu PowerShell](./howto-configure-server-parameters-using-powershell.md) [lub Azure Portal](./howto-server-parameters.md)

## <a name="prerequisites"></a>Wymagania wstępne
Aby przejść przez ten przewodnik, potrzebne są:
- [Serwer Azure Database for MySQL danych](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Narzędzie wiersza](/cli/azure/install-azure-cli) polecenia platformy Azure lub użyj Azure Cloud Shell w przeglądarce.

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Lista parametrów konfiguracji serwera dla Azure Database for MySQL serwera
Aby wyświetlić listę wszystkich modyfikowalnych parametrów na serwerze i ich wartości, uruchom [polecenie az mysql server configuration list.](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_list)

Parametry konfiguracji serwera dla serwera można wyświetlić w mydemoserver.mysql.database.azure.com **grupy** zasobów **myresourcegroup.**
```azurecli-interactive
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```
Aby uzyskać definicję każdego z wymienionych parametrów, zobacz sekcję z odwołaniem do programu MySQL w [tece Server System Variables](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-configuration-parameter-details"></a>Wyświetlanie szczegółów parametru konfiguracji serwera
Aby wyświetlić szczegółowe informacje o konkretnym parametrze konfiguracji dla serwera, uruchom [polecenie az mysql server configuration show.](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_show)

W tym przykładzie przedstawiono szczegóły **parametru konfiguracji serwera \_ \_** dziennika wolnych zapytań dla serwera **mydemoserver.mysql.database.azure.com** w grupie zasobów **myresourcegroup.**
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-a-server-configuration-parameter-value"></a>Modyfikowanie wartości parametru konfiguracji serwera
Można również zmodyfikować wartość określonego parametru konfiguracji serwera, który aktualizuje podstawową wartość konfiguracji aparatu serwera MySQL. Aby zaktualizować konfigurację, użyj [polecenia az mysql server configuration set.](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_set) 

Aby zaktualizować **parametr \_ konfiguracji serwera \_** dziennika wolnych zapytań serwera **mydemoserver.mysql.database.azure.com** w grupie zasobów **myresourcegroup.**
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```
Jeśli chcesz zresetować wartość parametru konfiguracji, pomiń opcjonalny parametr, a `--value` usługa stosuje wartość domyślną. W powyższym przykładzie wyglądałoby to tak:
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
Ten kod resetuje **konfigurację dziennika \_ \_ wolnych zapytań** do wartości domyślnej **WYŁ.**. 

## <a name="setting-parameters-not-listed"></a>Nie wymieniono parametrów ustawień
Jeśli parametr serwera, który chcesz zaktualizować, nie znajduje się na liście Azure Portal, możesz opcjonalnie ustawić parametr na poziomie połączenia przy użyciu . `init_connect` W ten sposób ustawiane są parametry serwera dla każdego klienta łączącego się z serwerem. 

Zaktualizuj parametr **konfiguracji serwera init \_ connect** serwera mydemoserver.mysql.database.azure.com **w** grupie zasobów **myresourcegroup,** aby ustawić wartości, takie jak zestaw znaków.
```azurecli-interactive
az mysql server configuration set --name init_connect --resource-group myresourcegroup --server mydemoserver --value "SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;"
```

## <a name="working-with-the-time-zone-parameter"></a>Praca z parametrem strefy czasowej

### <a name="populating-the-time-zone-tables"></a>Wypełnianie tabel strefy czasowej

Tabele stref czasowych na serwerze można wypełnić, wywołując procedurę składowaną z narzędzia, takiego jak wiersz polecenia `mysql.az_load_timezone` MySQL lub mySQL Workbench.

> [!NOTE]
> Jeśli uruchamiasz polecenie `mysql.az_load_timezone` z aplikacji MySQL Workbench, może być konieczne najpierw wyłączenie trybu bezpiecznej aktualizacji przy użyciu polecenia `SET SQL_SAFE_UPDATES=0;` .

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

Strefę czasową na poziomie globalnym można ustawić za pomocą [polecenia az mysql server configuration set.](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_set)

Następujące polecenie aktualizuje parametr konfiguracji serwera strefy **\_ czasowej** serwera **mydemoserver.mysql.database.azure.com** w grupie zasobów **myresourcegroup** na **US/Pacific.**

```azurecli-interactive
az mysql server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Ustawianie strefy czasowej na poziomie sesji

Strefę czasową na poziomie sesji można ustawić, uruchamiając polecenie z poziomu narzędzia, takiego jak wiersz polecenia `SET time_zone` mySQL lub program MySQL Workbench. W poniższym przykładzie strefa czasowa jest ustawiana na strefę **czasową USA/Pacyfik.**  

```sql
SET time_zone = 'US/Pacific';
```

Zapoznaj się z dokumentacją programu MySQL dotyczącą [funkcji daty i czasu.](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz)


## <a name="next-steps"></a>Następne kroki

- Jak skonfigurować [parametry serwera w programie Azure Portal](howto-server-parameters.md)
