---
title: Konfigurowanie parametrów serwera — interfejs wiersza polecenia platformy Azure-Azure Database for MySQL elastyczny serwer
description: W tym artykule opisano sposób konfigurowania parametrów usługi w Azure Database for MySQL elastycznym serwerze przy użyciu narzędzia wiersza polecenia platformy Azure.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 11/10/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 58e7c024d6494aee745884997e42b527c51ab237
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94489543"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-flexible-server-using-the-azure-cli"></a>Konfigurowanie parametrów serwera w Azure Database for MySQL elastycznym serwerze przy użyciu interfejsu wiersza polecenia platformy Azure

> [!IMPORTANT] 
> Serwer elastyczny Azure Database for MySQL jest obecnie w publicznej wersji zapoznawczej.

Można wyświetlić, pokazać i zaktualizować parametry dla Azure Database for MySQL elastycznego serwera za pomocą interfejsu wiersza polecenia platformy Azure, narzędzia wiersza poleceń platformy Azure. Parametry serwera są skonfigurowane z domyślną i zalecaną wartością podczas tworzenia serwera.  

W tym artykule opisano sposób wyświetlania, wyświetlania i aktualizowania parametrów serwera przy użyciu interfejsu wiersza polecenia platformy Azure.

>[!Note]
> Parametry serwera można aktualizować globalnie na poziomie serwera, korzystać z [interfejsu wiersza polecenia platformy Azure](./how-to-configure-server-parameters-cli.md) lub [Azure Portal](./how-to-configure-server-parameters-portal.md)

## <a name="prerequisites"></a>Wymagania wstępne
Aby krokowo poprowadzić ten przewodnik, musisz:
- [Azure Database for MySQL elastyczny serwer](quickstart-create-server-cli.md)
- Narzędzie wiersza polecenia [platformy Azure](/cli/azure/install-azure-cli) lub użyj Azure Cloud Shell w przeglądarce.

## <a name="list-server-parameters-for-azure-database-for-mysql-flexible-server"></a>Wyświetlanie listy parametrów serwera dla Azure Database for MySQL elastyczny serwer
Aby wyświetlić listę wszystkich parametrów na serwerze i ich wartości, uruchom polecenie [AZ MySQL elastyczny-Server Parameter List](/cli/azure/mysql/flexible-server/parameter) .

Dla serwera **mydemoserver.MySQL.Database.Azure.com** można wyświetlić listę parametrów serwera w **obszarze Grupa zasobów**.
```azurecli-interactive
az mysql flexible-server parameter list --resource-group myresourcegroup --server-name mydemoserver
```
Aby zapoznać się z definicją każdego z wymienionych parametrów, zobacz sekcję informacje dotyczące programu MySQL w artykule [zmienne systemowe serwera](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-parameter-details"></a>Pokaż szczegóły parametru serwera
Aby wyświetlić szczegóły dotyczące określonego parametru dla serwera, uruchom polecenie [AZ MySQL elastyczny-Server Parameter show](/cli/azure/mysql/flexible-server/parameter) .

W tym przykładzie przedstawiono szczegółowe informacje o **powolnych parametrach serwera \_ \_ dziennika zapytania** dla serwera **mydemoserver.MySQL.Database.Azure.com** w obszarze Grupa zasobów **.**
```azurecli-interactive
az mysql flexible-server parameter show --name slow_query_log --resource-group myresourcegroup --server-name mydemoserver
```
## <a name="modify-a-server-parameter-value"></a>Modyfikowanie wartości parametru serwera
Można również zmodyfikować wartość określonego parametru serwera, który aktualizuje podstawową wartość konfiguracyjną dla aparatu serwera MySQL. Aby zaktualizować parametr serwera, użyj polecenia [AZ MySQL elastyczny-Server Parameter Set](/cli/azure/mysql/flexible-server/parameter) . 

Aby zaktualizować parametr **serwera \_ \_ dziennika wolnych zapytań** serwera **mydemoserver.MySQL.Database.Azure.com** w obszarze Grupa zasobów **.**
```azurecli-interactive
az mysql flexible-server parameter set --name slow_query_log --resource-group myresourcegroup --server-name mydemoserver --value ON
```
Jeśli chcesz zresetować wartość parametru, Pomiń opcjonalny `--value` parametr, a usługa zastosuje wartość domyślną. W powyższym przykładzie będzie wyglądać następująco:
```azurecli-interactive
az mysql flexible-server parameter set --name slow_query_log --resource-group myresourcegroup --server-name mydemoserver
```
Ten kod resetuje **\_ \_ Dziennik wolnych zapytań** **do wartości domyślnej**. 

## <a name="setting-non-modifiable-server-parameters"></a>Ustawianie niemodyfikowalnych parametrów serwera

Jeśli parametr serwera, który chcesz zaktualizować, nie jest modyfikowalny, możesz opcjonalnie ustawić parametr na poziomie połączenia przy użyciu `init_connect` . Powoduje to ustawienie parametrów serwera dla każdego klienta łączącego się z serwerem. 

Zaktualizuj parametr **init \_ Connect** Server serwera **mydemoserver.MySQL.Database.Azure.com** w obszarze Grupa **zasobów, aby** ustawić wartości takie jak zestaw znaków.
```azurecli-interactive
az mysql flexible-server parameter set --name init_connect --resource-group myresourcegroup --server-name mydemoserver --value "SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;"
```
>[!Note]
> Polecenie `init_connect` może służyć do zmieniania parametrów, które nie wymagają uprawnień administratora na poziomie sesji. Aby sprawdzić, czy można ustawić parametr przy użyciu polecenia `init_connect`, wykonaj polecenie `set session parameter_name=YOUR_DESIRED_VALUE;`, a jeśli zwraca ono błąd **Odmowa dostępu; wymagane są uprawnienia administratora** , to nie można ustawić parametru przy użyciu polecenia „init_connect”.

## <a name="working-with-the-time-zone-parameter"></a>Praca z parametrem strefy czasowej

### <a name="populating-the-time-zone-tables"></a>Wypełnianie tabel strefy czasowej

Tabele strefy czasowej na serwerze można wypełnić przez wywołanie `mysql.az_load_timezone` procedury składowanej z narzędzia, takiego jak wiersz polecenia MySQL lub MySQL Workbench.

> [!NOTE]
> Jeśli uruchamiasz `mysql.az_load_timezone` polecenie z programu MySQL Workbench, może być konieczne wyłączenie bezpiecznego trybu aktualizacji jako pierwszego przy użyciu programu `SET SQL_SAFE_UPDATES=0;` .

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Należy ponownie uruchomić serwer, aby upewnić się, że tabele strefy czasowej są prawidłowo wypełnione.<!-- fIX me To restart the server, use the [Azure portal](howto-restart-server-portal.md) or [CLI](howto-restart-server-cli.md). -->

Aby wyświetlić dostępne wartości strefy czasowej, uruchom następujące polecenie:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Ustawianie strefy czasowej na poziomie globalnym

Strefę czasową na poziomie globalnym można ustawić za pomocą polecenia [AZ MySQL elastyczny-Server Parameter Set](/cli/azure/mysql/flexible-server/parameter) .

Następujące polecenie aktualizuje parametr serwera **\_ strefy czasowej** serwera **mydemoserver.MySQL.Database.Azure.com** w **obszarze Grupa zasobów zasobu do** **USA/Pacyfik**.

```azurecli-interactive
az mysql flexible-server parameter set --name time_zone --resource-group myresourcegroup --server-name mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Ustawianie strefy czasowej na poziomie sesji

Strefę czasową na poziomie sesji można ustawić, uruchamiając `SET time_zone` polecenie za pomocą narzędzia, takiego jak wiersz polecenia MySQL lub MySQL Workbench. W poniższym przykładzie ustawiono strefę czasową dla strefy czasowej **USA/Pacyfiku** .  

```sql
SET time_zone = 'US/Pacific';
```

Zapoznaj się z dokumentacją programu MySQL dla [funkcji daty i godziny](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).


## <a name="next-steps"></a>Następne kroki

- Jak skonfigurować [parametry serwera w Azure Portal](./how-to-configure-server-parameters-portal.md)
