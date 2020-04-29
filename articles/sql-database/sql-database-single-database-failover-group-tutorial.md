---
title: 'Samouczek: Dodawanie pojedynczej bazy danych do grupy trybu failover'
description: Dodaj Azure SQL Database pojedynczą bazę danych do grupy trybu failover przy użyciu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 06/19/2019
ms.openlocfilehash: c5ce6a1c2f231d372a2a8113eb9043a236090388
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80061691"
---
# <a name="tutorial-add-an-azure-sql-database-single-database-to-a-failover-group"></a>Samouczek: Dodawanie Azure SQL Database pojedynczej bazy danych do grupy trybu failover

[Grupa trybu failover](sql-database-auto-failover-group.md) jest deklaratywną warstwą abstrakcji, która umożliwia grupowanie baz danych z replikacją geograficzną wiele. Informacje na temat konfigurowania grupy trybu failover dla Azure SQL Database pojedynczej bazy danych i testowania pracy w trybie failover przy użyciu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.  Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Utwórz Azure SQL Databaseą pojedynczą bazę danych.
> - Utwórz grupę trybu failover dla pojedynczej bazy danych między dwoma logicznymi serwerami SQL.
> - Testowanie pracy w trybie failover.

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="portal"></a>[Portal](#tab/azure-portal)
Aby ukończyć kroki tego samouczka, upewnij się, że dysponujesz następującymi elementami: 

- Subskrypcja platformy Azure. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) , jeśli jeszcze go nie masz.


# <a name="powershell"></a>[Narzędzia](#tab/azure-powershell)
Aby ukończyć ten samouczek, upewnij się, że masz następujące elementy:

- Subskrypcja platformy Azure. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) , jeśli jeszcze go nie masz.
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)


# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
Aby ukończyć ten samouczek, upewnij się, że masz następujące elementy:

- Subskrypcja platformy Azure. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) , jeśli jeszcze go nie masz.
- Najnowsza wersja [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). 

---

## <a name="1---create-a-single-database"></a>1 — Tworzenie pojedynczej bazy danych 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---create-the-failover-group"></a>2 — Tworzenie grupy trybu failover 
W tym kroku utworzysz [grupę trybu failover](sql-database-auto-failover-group.md) między istniejącym serwerem Azure SQL i nowym serwerem Azure SQL w innym regionie. Następnie Dodaj przykładową bazę danych do grupy trybu failover. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Utwórz grupę trybu failover i Dodaj do niej pojedynczą bazę danych przy użyciu Azure Portal. 

1. Wybierz pozycję **Azure SQL** w menu po lewej stronie [Azure Portal](https://portal.azure.com). Jeśli na liście nie ma **usługi Azure SQL** , wybierz pozycję **wszystkie usługi**, a następnie wpisz SQL Azure w polu wyszukiwania. Obowiązkowe Wybierz gwiazdkę obok pozycji **Azure SQL** , aby ją dodać do ulubionych, i Dodaj ją jako element w nawigacji po lewej stronie. 
1. Wybierz pojedynczą bazę danych utworzoną w sekcji 1, na `mySampleDatabase`przykład. 
1. Grupy trybu failover można skonfigurowane na poziomie serwera. Wybierz nazwę serwera w polu **Nazwa serwera** , aby otworzyć ustawienia serwera.

   ![Otwórz serwer dla pojedynczej bazy danych](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Wybierz pozycję **grupy trybu failover** w okienku **Ustawienia** , a następnie wybierz pozycję **Dodaj grupę** , aby utworzyć nową grupę trybu failover. 

    ![Dodaj nową grupę trybu failover](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. Na stronie **Grupa trybu failover** wprowadź lub wybierz następujące wartości, a następnie wybierz pozycję **Utwórz**:
    - **Nazwa grupy trybu failover**: wpisz unikatową nazwę grupy trybu failover, taką `failovergrouptutorial`jak. 
    - **Serwer pomocniczy**: wybierz opcję *konfigurowania wymaganych ustawień* , a następnie wybierz pozycję **Utwórz nowy serwer**. Alternatywnie można wybrać istniejący serwer jako serwer pomocniczy. Po wprowadzeniu następujących wartości wybierz pozycję **Wybierz**. 
        - **Nazwa serwera**: wpisz unikatową nazwę serwera pomocniczego, na przykład `mysqlsecondary`. 
        - **Identyfikator logowania administratora serwera**: typ`azureuser`
        - **Hasło**: wpisz złożone hasło spełniające wymagania dotyczące haseł.
        - **Lokalizacja**: Wybierz lokalizację z listy rozwijanej, na przykład `East US`. Ta lokalizacja nie może być taka sama jak lokalizacja serwera podstawowego.

    > [!NOTE]
    > Ustawienia logowania serwera i zapory muszą być zgodne z serwerem podstawowym. 
    
      ![Tworzenie serwera pomocniczego dla grupy trybu failover](media/sql-database-single-database-failover-group-tutorial/create-secondary-failover-server.png)

   - **Bazy danych w grupie**: po wybraniu serwera pomocniczego ta opcja zostanie odblokowana. Wybierz je, aby **wybrać bazy danych do dodania** , a następnie wybierz bazę danych utworzoną w sekcji 1. Dodanie bazy danych do grupy trybu failover spowoduje automatyczne uruchomienie procesu replikacji geograficznej. 
        
    ![Dodawanie bazy danych SQL do grupy trybu failover](media/sql-database-single-database-failover-group-tutorial/add-sqldb-to-failover-group.png)
        

# <a name="powershell"></a>[Narzędzia](#tab/azure-powershell)
Utwórz grupę trybu failover i Dodaj do niej pojedynczą bazę danych przy użyciu programu PowerShell. 

   > [!NOTE]
   > Ustawienia logowania serwera i zapory muszą być zgodne z serwerem podstawowym. 

   ```powershell-interactive
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "West US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $databaseName = "mySampleDatabase"
   $drLocation = "East US"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # The ip address range that you want to allow to access your server 
   # (leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "DR Server name is" $drServerName 
   Write-host "Failover group name is" $failoverGroupName
   
   # Create a secondary server in the failover region
   Write-host "Creating a secondary logical server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary logical server..."
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   $serverFirewallRule   
   
   # Create a failover group between the servers
   $failovergroup = Write-host "Creating a failover group between the primary and secondary server..."
   New-AzSqlDatabaseFailoverGroup `
      –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $failovergroup
   
   # Add the database to the failover group
   Write-host "Adding the database to the failover group..." 
   Get-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName | `
   Add-AzSqlDatabaseToFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Successfully added the database to the failover group..." 
   ```

W tej części samouczka są stosowane następujące polecenia cmdlet programu PowerShell:

| Polecenie | Uwagi |
|---|---|
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Tworzy serwer usługi SQL Database hostujący pojedyncze bazy danych i elastyczne pule. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Tworzy regułę zapory dla serwera logicznego. | 
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Tworzy nową Azure SQL Database pojedynczą bazę danych. | 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Tworzy nową grupę trybu failover. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Pobiera co najmniej jedną bazę danych SQL. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Dodaje co najmniej jedną bazę danych SQL platformy Azure do grupy trybu failover. |

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
Utwórz grupę trybu failover i Dodaj do niej pojedynczą bazę danych przy użyciu polecenia AZ CLI. 

   > [!NOTE]
   > Ustawienia logowania serwera i zapory muszą być zgodne z serwerem podstawowym. 

   ```azurecli-interactive
   #!/bin/bash
   # set variables
   $failoverLocation = "West US"
   $failoverServer = "failoverServer-$randomIdentifier"
   $failoverGroup = "failoverGroup-$randomIdentifier"

   echo "Creating a secondary logical server in the DR region..."
   az sql server create --name $failoverServer --resource-group $resourceGroup --location $failoverLocation --admin-user $login --admin-password $password
   
   echo "Creating a failover group between the two servers..."
   az sql failover-group create --name $failoverGroup --partner-server $failoverServer --resource-group $resourceGroup --server $server --add-db $database --failover-policy Automatic
   ```

W tej części samouczka są stosowane następujące polecenia:

| Polecenie | Uwagi |
|---|---|
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Tworzy serwer usługi SQL Database hostujący pojedyncze bazy danych i elastyczne pule. |
| [AZ SQL Server firewall-Rule Create](/cli/azure/sql/server/firewall-rule) | Tworzy reguły zapory serwera. | 
| [AZ SQL failover-Group Create](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Tworzy grupę trybu failover. | 

---

## <a name="3---test-failover"></a>3 — Testowanie pracy w trybie failover 
W tym kroku nastąpi niepowodzenie grupy trybu failover na serwerze pomocniczym, a następnie powrót po awarii przy użyciu Azure Portal. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Testowanie pracy w trybie failover przy użyciu Azure Portal. 

1. Wybierz pozycję **Azure SQL** w menu po lewej stronie [Azure Portal](https://portal.azure.com). Jeśli na liście nie ma **usługi Azure SQL** , wybierz pozycję **wszystkie usługi**, a następnie wpisz SQL Azure w polu wyszukiwania. Obowiązkowe Wybierz gwiazdkę obok pozycji **Azure SQL** , aby ją dodać do ulubionych, i Dodaj ją jako element w nawigacji po lewej stronie. 
1. Wybierz pojedynczą bazę danych utworzoną w sekcji 2, na przykład `mySampleDatbase`. 
1. Wybierz nazwę serwera w polu **Nazwa serwera** , aby otworzyć ustawienia serwera.

   ![Otwórz serwer dla pojedynczej bazy danych](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Wybierz pozycję **grupy trybu failover** w okienku **Ustawienia** , a następnie wybierz grupę trybu failover utworzoną w sekcji 2. 
  
   ![Wybieranie grupy trybu failover z portalu](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Sprawdź, który serwer jest podstawowy i który serwer jest serwerem pomocniczym. 
1. Wybierz pozycję **tryb failover** z okienka zadań, aby przełączyć grupę trybu failover do trybu failover zawierającej przykładową pojedynczą bazę danych. 
1. Na ostrzeżeniu wybierz pozycję **tak** , aby powiadomić, że sesje TDS zostaną rozłączone. 

   ![Praca awaryjna grupy trybu failover zawierającej bazę danych SQL](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Sprawdź, który serwer jest teraz podstawowy i który serwer jest serwerem pomocniczym. Jeśli przełączenie w tryb failover powiodło się, te dwa serwery powinny mieć zamienione role. 
1. Wybierz ponownie **tryb failover** , aby powrócić do awarii serwerów z powrotem do ich pierwotnych ról. 

# <a name="powershell"></a>[Narzędzia](#tab/azure-powershell)
Testowanie pracy w trybie failover przy użyciu programu PowerShell. 


Sprawdź rolę repliki pomocniczej: 

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```

Przejdź do trybu failover na serwerze pomocniczym: 

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully to" $drServerName 
   ```

Przywróć grupę trybu failover z powrotem do serwera podstawowego:

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully back to" $serverName
   ```

W tej części samouczka są stosowane następujące polecenia cmdlet programu PowerShell:

| Polecenie | Uwagi |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Pobiera lub wyświetla listę grup Azure SQL Database trybu failover. |
| [Przełącznik-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Wykonuje tryb failover grupy trybu failover Azure SQL Database. |



# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
Testowanie pracy w trybie failover przy użyciu polecenia AZ CLI. 

Sprawdź, który serwer jest serwerem pomocniczym:

   
   ```azurecli-interactive
   echo "Verifying which server is in the secondary role..."
   az sql failover-group list --server $server --resource-group $resourceGroup
   ```

Przejdź do trybu failover na serwerze pomocniczym: 

   ```azurecli-interactive
   echo "Failing over group to the secondary server..."
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $failoverServer
   echo "Successfully failed failover group over to" $failoverServer
   ```

Przywróć grupę trybu failover z powrotem do serwera podstawowego:

   ```azurecli-interactive
   echo "Failing over group back to the primary server..."
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $server
   echo "Successfully failed failover group back to" $server
   ```

W tej części samouczka są stosowane następujące polecenia:

| Polecenie | Uwagi |
|---|---|
| [AZ SQL failover-Group list](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Wyświetla listę grup trybu failover na serwerze. |
| [AZ SQL failover-Group Set-Primary](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Ustaw podstawową grupę trybu failover, przełączając się na wszystkie bazy danych z bieżącego serwera podstawowego. | 

---

## <a name="clean-up-resources"></a>Oczyszczanie zasobów 
Wyczyść zasoby, usuwając grupę zasobów. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Usuń grupę zasobów przy użyciu Azure Portal. 

1. Przejdź do grupy zasobów w [Azure Portal](https://portal.azure.com).
1. Wybierz pozycję **Usuń grupę zasobów** , aby usunąć wszystkie zasoby w grupie, a także samą grupę zasobów. 
1. Wpisz nazwę grupy zasobów, `myResourceGroup`w polu tekstowym, a następnie wybierz pozycję **Usuń** , aby usunąć grupę zasobów.  

# <a name="powershell"></a>[Narzędzia](#tab/azure-powershell)

Usuń grupę zasobów przy użyciu programu PowerShell. 

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"

   # Remove the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

W tej części samouczka są stosowane następujące polecenia cmdlet programu PowerShell:

| Polecenie | Uwagi |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów | 

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Usuń grupę zasobów za pomocą polecenia AZ CLI. 


   ```azurecli-interactive
   echo "Cleaning up resources by removing the resource group..."
   az group delete --name $resourceGroup
   echo "Successfully removed resource group" $resourceGroup
   ```

W tej części samouczka są stosowane następujące polecenia:

| Polecenie | Uwagi |
|---|---|
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

---


> [!IMPORTANT]
> Jeśli chcesz zachować grupę zasobów, ale usunąć pomocniczą bazę danych, usuń ją z grupy trybu failover przed jej usunięciem. Usunięcie pomocniczej bazy danych przed jej usunięciem z grupy trybu failover może spowodować nieprzewidywalne zachowanie. 


## <a name="full-scripts"></a>Pełne skrypty

# <a name="powershell"></a>[Narzędzia](#tab/azure-powershell)

[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add single database to a failover group")]

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Tworzy serwer usługi SQL Database hostujący pojedyncze bazy danych i elastyczne pule. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Tworzy regułę zapory dla serwera logicznego. | 
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Tworzy nową Azure SQL Database pojedynczą bazę danych. | 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Tworzy nową grupę trybu failover. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Pobiera co najmniej jedną bazę danych SQL. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Dodaje co najmniej jedną bazę danych SQL platformy Azure do grupy trybu failover. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Pobiera lub wyświetla listę grup Azure SQL Database trybu failover. |
| [Przełącznik-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Wykonuje tryb failover grupy trybu failover Azure SQL Database. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów | 

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

[!code-azurecli-interactive[main](../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add single database to a failover group")]

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [AZ Account Set](/cli/azure/account?view=azure-cli-latest#az-account-set) | Ustawia subskrypcję jako bieżącą aktywną subskrypcję. | 
| [az group create](/cli/azure/group#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Tworzy serwer usługi SQL Database hostujący pojedyncze bazy danych i elastyczne pule. |
| [AZ SQL Server firewall-Rule Create](/cli/azure/sql/server/firewall-rule) | Tworzy reguły zapory serwera. | 
| [az sql db create](/cli/azure/sql/db?view=azure-cli-latest) | Tworzy bazę danych. | 
| [AZ SQL failover-Group Create](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Tworzy grupę trybu failover. | 
| [AZ SQL failover-Group list](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Wyświetla listę grup trybu failover na serwerze. |
| [AZ SQL failover-Group Set-Primary](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Ustaw podstawową grupę trybu failover, przełączając się na wszystkie bazy danych z bieżącego serwera podstawowego. | 
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

# <a name="portal"></a>[Portal](#tab/azure-portal)
Brak skryptów dostępnych dla Azure Portal. 
 
---

Inne skrypty Azure SQL Database można znaleźć tutaj: [Azure PowerShell](sql-database-powershell-samples.md) i [interfejsu wiersza polecenia platformy Azure](sql-database-cli-samples.md). 

## <a name="next-steps"></a>Następne kroki

W tym samouczku dodano Azure SQL Database pojedynczą bazę danych do grupy trybu failover i przetestowano tryb failover. W tym samouczku omówiono: 

> [!div class="checklist"]
> - Utwórz Azure SQL Databaseą pojedynczą bazę danych. 
> - Utwórz [grupę trybu failover](sql-database-auto-failover-group.md) dla pojedynczej bazy danych między dwoma logicznymi serwerami SQL.
> - Testowanie pracy w trybie failover.

Przejdź do następnego samouczka dotyczącego dodawania puli elastycznej do grupy trybu failover. 

> [!div class="nextstepaction"]
> [Samouczek: Dodawanie Azure SQL Database elastycznej puli do grupy trybu failover](sql-database-elastic-pool-failover-group-tutorial.md)
