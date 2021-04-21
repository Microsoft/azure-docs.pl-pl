---
title: Zarządzanie regułami zapory — interfejs wiersza polecenia platformy Azure — Azure Database for MySQL
description: W tym artykule opisano sposób tworzenia reguł zapory Azure Database for MySQL i zarządzania nimi przy użyciu wiersza polecenia interfejsu wiersza polecenia platformy Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 41c5c856953c4c45b38a69ba4695df489aaf5270
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774705"
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-cli"></a>Tworzenie reguł zapory Azure Database for MySQL i zarządzanie nimi przy użyciu interfejsu wiersza polecenia platformy Azure
Reguły zapory na poziomie serwera mogą służyć do zarządzania dostępem do serwera Azure Database for MySQL z określonego adresu IP lub zakresu adresów IP. Korzystając z wygodnych poleceń interfejsu wiersza polecenia platformy Azure, możesz tworzyć, aktualizować, usuwać, wyświetlać i wyświetlać reguły zapory do zarządzania serwerem. Aby uzyskać omówienie zapory Azure Database for MySQL, zobacz [Azure Database for MySQL reguły zapory serwera](./concepts-firewall-rules.md).

Virtual Network (VNet) mogą być również używane do zabezpieczania dostępu do serwera. Dowiedz się więcej na temat tworzenia punktów końcowych Virtual Network i reguł usługi przy użyciu [interfejsu wiersza polecenia platformy Azure i zarządzania nimi.](howto-manage-vnet-using-cli.md)

## <a name="prerequisites"></a>Wymagania wstępne
* [Zainstaluj interfejs wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)
* Serwer [Azure Database for MySQL bazy danych](quickstart-create-mysql-server-database-using-azure-cli.md).

## <a name="firewall-rule-commands"></a>Polecenia reguły zapory:
Polecenie **az mysql server firewall-rule** służy z interfejsu wiersza polecenia platformy Azure do tworzenia, usuwania, tworzenia listy, pokazywania i aktualizowania reguł zapory.

Polecenia:
- **create:** utwórz regułę zapory serwera Azure MySQL.
- **usuń:** usuń regułę zapory serwera Azure MySQL.
- **list:** lista reguł zapory serwera Azure MySQL.
- **pokaż:** pokaż szczegóły reguły zapory serwera Usługi Azure MySQL.
- **update:** zaktualizuj regułę zapory serwera Usługi Azure MySQL.

## <a name="sign-in-to-azure-and-list-your-azure-database-for-mysql-servers"></a>Zaloguj się do platformy Azure i wyloguj się z Azure Database for MySQL Serwerów
Bezpiecznie połącz interfejs wiersza polecenia platformy Azure z kontem platformy Azure przy użyciu **polecenia az login.**

1. W wierszu polecenia uruchom następujące polecenie:
    ```azurecli
    az login
    ```
   To polecenie wyprowadza kod do użycia w następnym kroku.

2. Za pomocą przeglądarki internetowej otwórz stronę [https://aka.ms/devicelogin](https://aka.ms/devicelogin) , a następnie wprowadź kod.

3. Po wyświetleniu monitu zaloguj się przy użyciu poświadczeń platformy Azure.

4. Po autoryzacji logowania w konsoli zostanie wyświetlona lista subskrypcji. Skopiuj identyfikator żądanej subskrypcji, aby ustawić bieżącą subskrypcję do użycia. Użyj [polecenia az account set.](/cli/azure/account#az_account_set)
    ```azurecli-interactive
    az account set --subscription <your subscription id>
    ```

5. Jeśli nie masz pewności co do nazw, wyliczysz listę serwerów usługi Azure Databases for MySQL dla subskrypcji i grupy zasobów. Użyj [polecenia az mysql server list.](/cli/azure/mysql/server#az_mysql_server_list)

    ```azurecli-interactive
    az mysql server list --resource-group myresourcegroup
    ```

   Zanotuj na liście atrybut name, nad którym należy określić serwer MySQL, nad którym chcesz pracować. W razie potrzeby potwierdź szczegóły dla tego serwera i upewnij się, że jest on poprawny, używając atrybutu name. Użyj [polecenia az mysql server show.](/cli/azure/mysql/server#az_mysql_server_show)

    ```azurecli-interactive
    az mysql server show --resource-group myresourcegroup --name mydemoserver
    ```

## <a name="list-firewall-rules-on-azure-database-for-mysql-server"></a>Lista reguł zapory na Azure Database for MySQL Server 
Korzystając z nazwy serwera i nazwy grupy zasobów, należy wyświetlić listę istniejących reguł zapory serwera na serwerze. Użyj polecenia [az mysql server firewall list.](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_list)  Zwróć uwagę, że atrybut nazwy serwera jest określony w **przełączniku --server,** a nie w **przełączniku --name.** 
```azurecli-interactive
az mysql server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
Dane wyjściowe wyświetla listę reguł(jeśli są) w formacie JSON (domyślnie). Możesz użyć przełącznika **tabeli --output,** aby uzyskać wyniki w bardziej czytelnym formacie tabeli.
```azurecli-interactive
az mysql server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mysql-server"></a>Tworzenie reguły zapory na Azure Database for MySQL Server
Używając nazwy serwera Azure MySQL i nazwy grupy zasobów, utwórz nową regułę zapory na serwerze. Użyj polecenia [az mysql server firewall create.](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_create) Podaj nazwę reguły, a także adres IP rozpoczęcia i końcowy adres IP (aby zapewnić dostęp do zakresu adresów IP) dla reguły.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Aby zezwolić na dostęp dla pojedynczego adresu IP, podaj ten sam adres IP co adres IP rozpoczęcia i końcowy adres IP, jak w tym przykładzie.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```

Aby umożliwić aplikacjom z adresów IP platformy Azure łączenie się z serwerem Azure Database for MySQL, podaj adres IP 0.0.0.0 jako adres IP rozpoczęcia i końcowy adres IP, jak w tym przykładzie.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mysql --name "AllowAllWindowsAzureIps" --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Ta opcja konfiguruje zaporę w celu zezwalania na wszystkie połączenia z platformy Azure, w tym połączenia z subskrypcji innych klientów. W przypadku wybrania tej opcji upewnij się, że uprawnienia logowania i użytkownika zezwalają na dostęp tylko uprawnionym użytkownikom.
> 

Po sukcesie każde polecenie tworzenia wyświetla szczegóły utworzonej reguły zapory w formacie JSON (domyślnie). Jeśli wystąpi awaria, w danych wyjściowych zostanie wyświetlony tekst komunikatu o błędzie.

## <a name="update-a-firewall-rule-on-azure-database-for-mysql-server"></a>Aktualizowanie reguły zapory na Azure Database for MySQL serwera 
Używając nazwy serwera Azure MySQL i nazwy grupy zasobów, zaktualizuj istniejącą regułę zapory na serwerze. Użyj polecenia [az mysql server firewall update.](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_update) Podaj nazwę istniejącej reguły zapory jako dane wejściowe, a także atrybuty adresu IP rozpoczęcia i końcowego adresu IP do zaktualizowania.
```azurecli-interactive
az mysql server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Po sukcesie dane wyjściowe polecenia będą zawierały szczegóły zaktualizowanej reguły zapory w formacie JSON (domyślnie). Jeśli wystąpi awaria, w danych wyjściowych zostanie wyświetlony tekst komunikatu o błędzie.

> [!NOTE]
> Jeśli reguła zapory nie istnieje, reguła jest tworzona za pomocą polecenia update.

## <a name="show-firewall-rule-details-on-azure-database-for-mysql-server"></a>Wyświetlanie szczegółów reguły zapory na Azure Database for MySQL Server
Korzystając z nazwy serwera Azure MySQL i nazwy grupy zasobów, wyświetl szczegóły istniejącej reguły zapory z serwera. Użyj polecenia [az mysql server firewall show.](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_show) Podaj nazwę istniejącej reguły zapory jako dane wejściowe.
```azurecli-interactive
az mysql server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
W przypadku powodzenia dane wyjściowe polecenia będą zawierały szczegóły określonej reguły zapory w formacie JSON (domyślnie). Jeśli wystąpi awaria, w danych wyjściowych zostanie wyświetlony tekst komunikatu o błędzie.

## <a name="delete-a-firewall-rule-on-azure-database-for-mysql-server"></a>Usuwanie reguły zapory na Azure Database for MySQL Server
Używając nazwy serwera Azure MySQL i nazwy grupy zasobów, usuń istniejącą regułę zapory z serwera. Użyj polecenia [az mysql server firewall delete.](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_delete) Podaj nazwę istniejącej reguły zapory.
```azurecli-interactive
az mysql server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Po sukcesie nie ma żadnych danych wyjściowych. Po niepowodzeniu zostanie wyświetlony tekst komunikatu o błędzie.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [Azure Database for MySQL zapory serwera .](./concepts-firewall-rules.md)
- [Tworzenie reguł zapory Azure Database for MySQL i](./howto-manage-firewall-using-portal.md)zarządzanie nimi przy użyciu Azure Portal .
- Dodatkowo zabezpiecz dostęp do serwera, tworząc punkty końcowe i reguły usługi Virtual Network za [pomocą interfejsu wiersza polecenia platformy Azure](howto-manage-vnet-using-cli.md)i zarządzając nimi.