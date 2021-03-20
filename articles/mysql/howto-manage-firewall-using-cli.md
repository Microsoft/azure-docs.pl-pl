---
title: Zarządzanie regułami zapory — interfejs wiersza polecenia platformy Azure — Azure Database for MySQL
description: W tym artykule opisano sposób tworzenia reguł zapory Azure Database for MySQL przy użyciu wiersza polecenia platformy Azure i zarządzania nimi.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: eaddd8b2979b30251301ad041ea4b872c23d680b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94541338"
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-cli"></a>Tworzenie reguł zapory Azure Database for MySQL i zarządzanie nimi za pomocą interfejsu wiersza polecenia platformy Azure
Reguły zapory na poziomie serwera mogą służyć do zarządzania dostępem do serwera Azure Database for MySQL z określonego adresu IP lub zakresu adresów IP. Korzystając z wygodnych poleceń interfejsu wiersza polecenia platformy Azure, można tworzyć, aktualizować, usuwać i wyświetlać reguły zapory w celu zarządzania serwerem. Aby zapoznać się z omówieniem zapór Azure Database for MySQL, zobacz [reguły zapory Azure Database for MySQL Server](./concepts-firewall-rules.md).

Reguły Virtual Network (VNet) mogą również służyć do zabezpieczania dostępu do serwera. Dowiedz się więcej o [tworzeniu i zarządzaniu Virtual Network punktów końcowych usługi i reguł przy użyciu interfejsu wiersza polecenia platformy Azure](howto-manage-vnet-using-cli.md).

## <a name="prerequisites"></a>Wymagania wstępne
* [Zainstaluj interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
* [Serwer Azure Database for MySQL i baza danych](quickstart-create-mysql-server-database-using-azure-cli.md).

## <a name="firewall-rule-commands"></a>Polecenia reguły zapory:
Polecenie **AZ MySQL Server firewall-Rule** jest używane w interfejsie wiersza polecenia platformy Azure do tworzenia, usuwania, wyświetlania i aktualizowania reguł zapory.

Polecenia:
- **Tworzenie**: Tworzenie reguły zapory serwera Azure MySQL.
- **usuwanie**: Usuwanie reguły zapory serwera Azure MySQL.
- **Lista**: Lista reguł zapory serwera Azure MySQL.
- **Pokaż**: pokazuje szczegóły reguły zapory serwera Azure MySQL.
- **Aktualizacja**: aktualizowanie reguły zapory serwera Azure MySQL.

## <a name="sign-in-to-azure-and-list-your-azure-database-for-mysql-servers"></a>Zaloguj się do platformy Azure i wystaw serwery Azure Database for MySQL
Bezpiecznie łącz interfejs wiersza polecenia platformy Azure z kontem platformy Azure za pomocą polecenia **AZ login** .

1. W wierszu polecenia Uruchom następujące polecenie:
    ```azurecli
    az login
    ```
   To polecenie wyprowadza kod do użycia w następnym kroku.

2. Użyj przeglądarki sieci Web, aby otworzyć stronę [https://aka.ms/devicelogin](https://aka.ms/devicelogin) , a następnie wprowadź kod.

3. W wierszu polecenia Zaloguj się przy użyciu poświadczeń platformy Azure.

4. Gdy logowanie zostanie autoryzowane, Lista subskrypcji zostanie wydrukowany w konsoli programu. Skopiuj identyfikator żądanej subskrypcji, aby ustawić bieżącą subskrypcję. Użyj polecenia [AZ Account Set](/cli/azure/account#az-account-set) .
    ```azurecli-interactive
    az account set --subscription <your subscription id>
    ```

5. Jeśli nie masz pewności co do nazw, Utwórz listę baz danych usługi Azure Database for MySQL dla Twojej subskrypcji i grupy zasobów. Użyj polecenia [AZ MySQL Server list](/cli/azure/mysql/server#az-mysql-server-list) .

    ```azurecli-interactive
    az mysql server list --resource-group myresourcegroup
    ```

   Zanotuj atrybut name na liście, w której chcesz określić serwer MySQL, na którym ma być wykonywane działanie. W razie potrzeby Potwierdź szczegóły tego serwera i używając atrybutu Name, aby upewnić się, że jest on poprawny. Użyj polecenia [AZ MySQL Server show](/cli/azure/mysql/server#az-mysql-server-show) .

    ```azurecli-interactive
    az mysql server show --resource-group myresourcegroup --name mydemoserver
    ```

## <a name="list-firewall-rules-on-azure-database-for-mysql-server"></a>Wyświetl listę reguł zapory na serwerze Azure Database for MySQL 
Korzystając z nazwy serwera i nazwy grupy zasobów, należy wyświetlić listę istniejących reguł zapory serwera na serwerze. Użyj polecenia [AZ MySQL Server firewall list](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-list) .  Należy zauważyć, że atrybut nazwy serwera jest określony w przełączniku **--Server** , a nie w przełączniku **--name** . 
```azurecli-interactive
az mysql server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
W danych wyjściowych są wyświetlane reguły, jeśli istnieją, w formacie JSON (domyślnie). Możesz użyć przełącznika **--Output tabeli** , aby uzyskać wyniki w bardziej czytelnym formacie tabeli.
```azurecli-interactive
az mysql server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mysql-server"></a>Tworzenie reguły zapory na serwerze Azure Database for MySQL
Przy użyciu nazwy serwera Azure MySQL i nazwy grupy zasobów Utwórz nową regułę zapory na serwerze. Użyj polecenia [AZ MySQL Server firewall Create](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-create) . Podaj nazwę reguły, a także początkowy adres IP i końcowy adres IP (aby zapewnić dostęp do zakresu adresów IP) dla reguły.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Aby zezwolić na dostęp dla pojedynczego adresu IP, podaj ten sam adres IP, który jest początkowym adresem IP i końcowym adresem IP, jak w tym przykładzie.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```

Aby umożliwić aplikacjom z adresów IP platformy Azure Łączenie się z serwerem Azure Database for MySQL, podaj adres IP 0.0.0.0 jako początkowy adres IP i końcowy adres IP, jak w tym przykładzie.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mysql --name "AllowAllWindowsAzureIps" --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Ta opcja konfiguruje zaporę w celu zezwalania na wszystkie połączenia z platformy Azure, w tym połączenia z subskrypcji innych klientów. W przypadku wybrania tej opcji upewnij się, że uprawnienia logowania i użytkownika zezwalają na dostęp tylko uprawnionym użytkownikom.
> 

Po pomyślnym wykonaniu poszczególnych poleceń tworzenia danych wyjściowych polecenia są wyświetlane szczegóły utworzonej reguły zapory w formacie JSON (domyślnie). Jeśli wystąpi błąd, w danych wyjściowych zostanie wyświetlony tekst komunikatu o błędzie.

## <a name="update-a-firewall-rule-on-azure-database-for-mysql-server"></a>Aktualizowanie reguły zapory na serwerze Azure Database for MySQL 
Korzystając z nazwy serwera Azure MySQL i nazwy grupy zasobów, zaktualizuj istniejącą regułę zapory na serwerze. Użyj polecenia [AZ MySQL Server firewall Update](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-update) . Podaj nazwę istniejącej reguły zapory jako dane wejściowe, a także atrybuty początkowy adres IP i końcowy adres IP do zaktualizowania.
```azurecli-interactive
az mysql server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Po pomyślnym wykonaniu polecenia wyświetlane są szczegóły zaktualizowanej reguły zapory w formacie JSON (domyślnie). Jeśli wystąpi błąd, w danych wyjściowych zostanie wyświetlony tekst komunikatu o błędzie.

> [!NOTE]
> Jeśli reguła zapory nie istnieje, reguła zostanie utworzona za pomocą polecenia Update.

## <a name="show-firewall-rule-details-on-azure-database-for-mysql-server"></a>Pokaż szczegóły reguły zapory na serwerze Azure Database for MySQL
Korzystając z nazwy serwera Azure MySQL i nazwy grupy zasobów, Pokaż szczegóły istniejącej reguły zapory z serwera. Użyj polecenia [AZ MySQL Server firewall show](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-show) . Podaj nazwę istniejącej reguły zapory jako dane wejściowe.
```azurecli-interactive
az mysql server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Po pomyślnym wykonaniu polecenia wyświetlane są szczegóły zdefiniowanej reguły zapory w formacie JSON (domyślnie). Jeśli wystąpi błąd, w danych wyjściowych zostanie wyświetlony tekst komunikatu o błędzie.

## <a name="delete-a-firewall-rule-on-azure-database-for-mysql-server"></a>Usuwanie reguły zapory na serwerze Azure Database for MySQL
Korzystając z nazwy serwera Azure MySQL i nazwy grupy zasobów, Usuń istniejącą regułę zapory z serwera. Użyj polecenia [AZ MySQL Server firewall Delete](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-delete) . Podaj nazwę istniejącej reguły zapory.
```azurecli-interactive
az mysql server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Po pomyślnym zakończeniu nie ma żadnych danych wyjściowych. W przypadku niepowodzenia zostanie wyświetlony tekst komunikatu o błędzie.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [regułach zapory serwera Azure Database for MySQL](./concepts-firewall-rules.md).
- [Utwórz reguły zapory Azure Database for MySQL przy użyciu Azure Portal i zarządzaj nimi](./howto-manage-firewall-using-portal.md).
- Aby zapewnić bardziej bezpieczny dostęp do serwera, można [utworzyć i zarządzać Virtual Network punktami końcowymi usługi i regułami przy użyciu interfejsu wiersza polecenia platformy Azure](howto-manage-vnet-using-cli.md).