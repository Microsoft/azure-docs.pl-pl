---
title: Konfigurowanie parametrów serwera — interfejs wiersza polecenia platformy Azure — Azure Database for MariaDB
description: W tym artykule opisano sposób konfigurowania parametrów usługi w Azure Database for MariaDB przy użyciu narzędzia wiersza polecenia platformy Azure.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: how-to
ms.date: 10/1/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4009d8047dae7bf8d9ba66566ff8797fa09a8878
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98662308"
---
# <a name="configure-server-parameters-in-azure-database-for-mariadb-using-the-azure-cli"></a>Konfigurowanie parametrów serwera w Azure Database for MariaDB przy użyciu interfejsu wiersza polecenia platformy Azure
Można wyświetlić, wyświetlić i zaktualizować parametry konfiguracji dla serwera Azure Database for MariaDB przy użyciu interfejsu wiersza polecenia platformy Azure, narzędzia wiersza poleceń platformy Azure. Podzestaw konfiguracji aparatu jest uwidoczniony na poziomie serwera i można go modyfikować.

>[!Note]
> Parametry serwera można zaktualizować globalnie na poziomie serwera, przy użyciu [interfejsu wiersza polecenia platformy Azure](./howto-configure-server-parameters-cli.md), programu [PowerShell](./howto-configure-server-parameters-using-powershell.md) lub witryny [Azure Portal](./howto-server-parameters.md).

## <a name="prerequisites"></a>Wymagania wstępne
Aby krokowo poprowadzić ten przewodnik, musisz:
- [Serwer Azure Database for MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md)
- Narzędzie wiersza polecenia [platformy Azure](/cli/azure/install-azure-cli) lub użyj Azure Cloud Shell w przeglądarce.

## <a name="list-server-configuration-parameters-for-azure-database-for-mariadb-server"></a>Wyświetlanie listy parametrów konfiguracji serwera dla Azure Database for MariaDB Server
Aby wyświetlić listę wszystkich modyfikowalnych parametrów na serwerze i ich wartości, uruchom polecenie [AZ MariaDB Server Configuration list](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list) .

Dla serwera **mydemoserver.MariaDB.Database.Azure.com** można wyświetlić listę parametrów konfiguracji serwera w **obszarze Grupa zasobów**.
```azurecli-interactive
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

Aby zapoznać się z definicją każdego z wymienionych parametrów, zobacz sekcję Referencja MariaDB na stronie [zmienne systemowe serwera](https://mariadb.com/kb/en/library/server-system-variables/).

## <a name="show-server-configuration-parameter-details"></a>Pokaż szczegóły parametru konfiguracji serwera
Aby wyświetlić szczegóły dotyczące określonego parametru konfiguracji dla serwera, uruchom polecenie [AZ MariaDB Server Configuration show](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-show) .

W tym przykładzie przedstawiono szczegółowe informacje o powolnych parametrach konfiguracji serwera **\_ \_ dziennika zapytania** dla serwera **mydemoserver.MariaDB.Database.Azure.com** w obszarze Grupa zasobów **.**
```azurecli-interactive
az mariadb server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Modyfikowanie wartości parametru konfiguracji serwera
Można również zmodyfikować wartość określonego parametru konfiguracji serwera, który aktualizuje podstawową wartość konfiguracyjną dla aparatu serwera MariaDB. Aby zaktualizować konfigurację, użyj polecenia [AZ MariaDB Server Configuration Set](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) . 

Aby zaktualizować parametr konfiguracji **powolnego serwera \_ \_ dziennika zapytań** serwera **mydemoserver.MariaDB.Database.Azure.com** w obszarze Grupa zasobów **.**
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```

Jeśli chcesz zresetować wartość parametru konfiguracji, Pomiń opcjonalny `--value` parametr, a usługa zastosuje wartość domyślną. W powyższym przykładzie będzie wyglądać następująco:
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```

Ten kod Resetuje konfigurację **\_ \_ dziennika wolnych zapytań** do wartości **domyślnej.** 

## <a name="setting-parameters-not-listed"></a>Nie wymieniono parametrów ustawień
Jeśli parametr serwera, który chcesz zaktualizować, nie znajduje się na liście Azure Portal, opcjonalnie możesz ustawić parametr na poziomie połączenia przy użyciu `init_connect` . Powoduje to ustawienie parametrów serwera dla każdego klienta łączącego się z serwerem. 

Zaktualizuj parametr Configuration programu **init \_ Connect** Server serwera **mydemoserver.MariaDB.Database.Azure.com** w obszarze Grupa zasobów **, aby** ustawić wartości takie jak zestaw znaków.
```azurecli-interactive
az mariadb server configuration set --name init_connect --resource-group myresourcegroup --server mydemoserver --value "SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;"
```

## <a name="working-with-the-time-zone-parameter"></a>Praca z parametrem strefy czasowej

### <a name="populating-the-time-zone-tables"></a>Wypełnianie tabel strefy czasowej

Tabele strefy czasowej na serwerze można wypełnić przez wywołanie `mysql.az_load_timezone` procedury składowanej z narzędzia, takiego jak MariaDB Command line lub MariaDB Workbench.

> [!NOTE]
> Jeśli uruchamiasz `mysql.az_load_timezone` polecenie z MariaDB Workbench, może być konieczne wyłączenie bezpiecznego trybu aktualizacji jako pierwszego przy użyciu `SET SQL_SAFE_UPDATES=0;` .

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Należy ponownie uruchomić serwer, aby upewnić się, że tabele strefy czasowej są prawidłowo wypełnione. Aby ponownie uruchomić serwer, użyj [Azure Portal](howto-restart-server-portal.md) lub [interfejsu wiersza polecenia](howto-restart-server-cli.md).

Aby wyświetlić dostępne wartości strefy czasowej, uruchom następujące polecenie:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Ustawianie strefy czasowej na poziomie globalnym

Strefę czasową na poziomie globalnym można ustawić za pomocą polecenia [AZ MariaDB Server Configuration Set](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) .

Następujące polecenie aktualizuje parametr konfiguracji **serwera \_ strefy czasowej** serwera **mydemoserver.MariaDB.Database.Azure.com** w **obszarze Grupa zasobów zasobu do** **Stanów Zjednoczonych/Pacyfiku**.

```azurecli-interactive
az mariadb server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Ustawianie strefy czasowej na poziomie sesji

Strefę czasową na poziomie sesji można ustawić, uruchamiając `SET time_zone` polecenie za pomocą narzędzia, takiego jak MariaDB Command line lub MariaDB Workbench. W poniższym przykładzie ustawiono strefę czasową dla strefy czasowej **USA/Pacyfiku** .  

```sql
SET time_zone = 'US/Pacific';
```

Zapoznaj się z dokumentacją MariaDB dla [funkcji daty i godziny](https://mariadb.com/kb/en/library/date-time-functions/).

## <a name="next-steps"></a>Następne kroki

- Jak skonfigurować [parametry serwera w Azure Portal](howto-server-parameters.md)
