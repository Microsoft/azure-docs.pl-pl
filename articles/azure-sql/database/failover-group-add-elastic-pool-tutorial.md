---
title: 'Samouczek: Dodawanie elastycznej puli do grupy trybu failover'
description: Dodaj Azure SQL Database elastyczną pulę do grupy trybu failover przy użyciu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: seo-lt-2019 sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/27/2019
ms.openlocfilehash: cdbc44158de2f24d7d33d68311979c3b8bdda85d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94593981"
---
# <a name="tutorial-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>Samouczek: Dodawanie Azure SQL Database elastycznej puli do grupy trybu failover
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Skonfiguruj grupę trybu failover dla Azure SQL Database elastycznej puli i przetestuj tryb failover przy użyciu Azure Portal.  Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> - Utwórz pojedynczą bazę danych.
> - Dodaj bazę danych do puli elastycznej.
> - Utwórz [grupę trybu failover](auto-failover-group-overview.md) dla dwóch pul elastycznych między dwoma serwerami.
> - Testowanie pracy w trybie failover.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć kroki tego samouczka, upewnij się, że dysponujesz następującymi elementami:

- Subskrypcja platformy Azure. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) , jeśli jeszcze go nie masz.

## <a name="1---create-a-single-database"></a>1 — Tworzenie pojedynczej bazy danych

[!INCLUDE [sql-database-create-single-database](../includes/sql-database-create-single-database.md)]

## <a name="2---add-the-database-to-an-elastic-pool"></a>2 — Dodawanie bazy danych do puli elastycznej

W tym kroku utworzysz pulę elastyczną i dodasz do niej bazę danych.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Utwórz pulę elastyczną przy użyciu Azure Portal.

1. Wybierz pozycję **Azure SQL** w menu po lewej stronie Azure Portal. Jeśli na liście nie ma **usługi Azure SQL** , wybierz pozycję **wszystkie usługi**, a następnie wpisz ciąg "Azure SQL" w polu wyszukiwania. Obowiązkowe Wybierz gwiazdkę obok pozycji **Azure SQL** , aby ją dodać do ulubionych, i Dodaj ją jako element w nawigacji po lewej stronie.
1. Wybierz pozycję **+ Dodaj** , aby otworzyć stronę **Wybieranie opcji wdrożenia SQL** . Aby wyświetlić dodatkowe informacje o różnych bazach danych, wybierz pozycję Pokaż szczegóły na kafelku bazy danych.
1. Wybierz pozycję **Pula elastyczna** z listy rozwijanej **Typ zasobu** na kafelku **bazy danych SQL** . Wybierz pozycję **Utwórz** , aby utworzyć pulę elastyczną.

    ![Wybierz pulę elastyczną](./media/failover-group-add-elastic-pool-tutorial/select-azure-sql-elastic-pool.png)

1. Skonfiguruj pulę elastyczną przy użyciu następujących wartości:
   - **Nazwa**: Podaj unikatową nazwę puli elastycznej, na przykład `myElasticPool` .
   - **Subskrypcja**: wybierz subskrypcję z listy rozwijanej.
   - Grupa zasobów **: Wybierz** pozycję `myResourceGroup` z listy rozwijanej, a następnie kliknij pozycję utworzona w sekcji 1.
   - **Serwer**: Wybierz serwer utworzony w sekcji 1 z listy rozwijanej.  

       ![Utwórz nowy serwer dla puli elastycznej](./media/failover-group-add-elastic-pool-tutorial/use-existing-server-for-elastic-pool.png)

   - **Obliczenia + magazyn**: wybierz pozycję **Konfiguruj pulę elastyczną** , aby skonfigurować obliczenia, magazyn i dodać pojedynczą bazę danych do puli elastycznej. Na karcie **Ustawienia puli** pozostaw wartość domyślną 5 rdzeń z 2 rdzeni wirtualnych i 32 GB.

1. Na stronie **Konfigurowanie** wybierz kartę **bazy danych** , a następnie wybierz pozycję **Dodaj bazę danych**. Wybierz bazę danych utworzoną w sekcji 1, a następnie wybierz pozycję **Zastosuj** , aby dodać ją do puli elastycznej. Wybierz pozycję **Zastosuj** ponownie, aby zastosować ustawienia puli elastycznej i zamknąć stronę **Konfiguracja** .

    ![Dodaj bazę danych do puli elastycznej](./media/failover-group-add-elastic-pool-tutorial/add-database-to-elastic-pool.png)

1. Wybierz pozycję **Przegląd + Utwórz** , aby przejrzeć ustawienia puli elastycznej, a następnie wybierz pozycję **Utwórz** , aby utworzyć pulę elastyczną.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Tworzenie pul elastycznych i serwera pomocniczego przy użyciu programu PowerShell.

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   $poolName = "myElasticPool"
   $databaseName = "mySampleDatabase"
   $drLocation = "West US"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # The ip address range that you want to allow to access your server
   # Leaving at 0.0.0.0 will prevent outside-of-azure connections
   # $startIp = "0.0.0.0"
   # $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "DR Server name is" $drServerName
   Write-host "Failover group name is" $failoverGroupName

   # Create primary Gen5 elastic 2 vCore pool
   Write-host "Creating elastic pool..."
   $elasticPool = New-AzSqlElasticPool -ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -ElasticPoolName $poolName `
       -Edition "GeneralPurpose" `
       -vCore 2 `
       -ComputeGeneration Gen5
   $elasticPool

   # Add single db into elastic pool
   Write-host "Creating elastic pool..."
   $addDatabase = Set-AzSqlDatabase -ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -DatabaseName $databaseName `
       -ElasticPoolName $poolName
   $addDatabase
   ```

W tej części samouczka są stosowane następujące polecenia cmdlet programu PowerShell:

| Polecenie | Uwagi |
|---|---|
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Tworzy elastyczną pulę baz danych dla Azure SQL Database.|
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Ustawia właściwości bazy danych lub przenosi istniejącą bazę danych do puli elastycznej. |

---

## <a name="3---create-the-failover-group"></a>3 — Tworzenie grupy trybu failover

W tym kroku utworzysz [grupę trybu failover](auto-failover-group-overview.md) między istniejącym serwerem a nowym serwerem w innym regionie. Następnie Dodaj pulę elastyczną do grupy trybu failover.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Utwórz grupę trybu failover przy użyciu Azure Portal.

1. Wybierz pozycję **Azure SQL** w menu po lewej stronie [Azure Portal](https://portal.azure.com). Jeśli na liście nie ma **usługi Azure SQL** , wybierz pozycję **wszystkie usługi**, a następnie wpisz SQL Azure w polu wyszukiwania. Obowiązkowe Wybierz gwiazdkę obok pozycji **Azure SQL** , aby ją dodać do ulubionych, i Dodaj ją jako element w nawigacji po lewej stronie.
1. Wybierz pulę elastyczną utworzoną w poprzedniej sekcji, na przykład `myElasticPool` .
1. W okienku **Przegląd** wybierz nazwę serwera w polu **Nazwa serwera** , aby otworzyć ustawienia serwera.
  
    ![Otwórz serwer dla puli elastycznej](./media/failover-group-add-elastic-pool-tutorial/server-for-elastic-pool.png)

1. Wybierz pozycję **grupy trybu failover** w okienku **Ustawienia** , a następnie wybierz pozycję **Dodaj grupę** , aby utworzyć nową grupę trybu failover.

    ![Dodaj nową grupę trybu failover](./media/failover-group-add-elastic-pool-tutorial/elastic-pool-failover-group.png)

1. Na stronie **Grupa trybu failover** wprowadź lub wybierz następujące wartości, a następnie wybierz pozycję **Utwórz**:
    - **Nazwa grupy trybu failover**: wpisz unikatową nazwę grupy trybu failover, taką jak `failovergrouptutorial` .
    - **Serwer pomocniczy**: wybierz opcję *konfigurowania wymaganych ustawień* , a następnie wybierz pozycję **Utwórz nowy serwer**. Alternatywnie można wybrać istniejący serwer jako serwer pomocniczy. Po wprowadzeniu następujących wartości dla nowego serwera pomocniczego wybierz pozycję **Wybierz**.
        - **Nazwa serwera**: wpisz unikatową nazwę serwera pomocniczego, na przykład `mysqlsecondary` .
        - **Identyfikator logowania administratora serwera**: typ `azureuser`
        - **Hasło**: wpisz złożone hasło spełniające wymagania dotyczące haseł.
        - **Lokalizacja**: Wybierz lokalizację z listy rozwijanej, na przykład `East US` . Ta lokalizacja nie może być taka sama jak lokalizacja serwera podstawowego.

       > [!NOTE]
       > Ustawienia logowania serwera i zapory muszą być zgodne z serwerem podstawowym.

       ![Tworzenie serwera pomocniczego dla grupy trybu failover](./media/failover-group-add-elastic-pool-tutorial/create-secondary-failover-server.png)

1. Wybierz **bazy danych w grupie,** a następnie wybierz pulę elastyczną utworzoną w sekcji 2. Powinien pojawić się ostrzeżenie z monitem o utworzenie elastycznej puli na serwerze pomocniczym. Wybierz ostrzeżenie, a następnie wybierz przycisk **OK** , aby utworzyć pulę elastyczną na serwerze pomocniczym. 

   ![Dodawanie puli elastycznej do grupy trybu failover](./media/failover-group-add-elastic-pool-tutorial/add-elastic-pool-to-failover-group.png)

1. Wybierz pozycję **Wybierz** , aby zastosować ustawienia puli elastycznej do grupy trybu failover, a następnie wybierz pozycję **Utwórz** , aby utworzyć grupę trybu failover. Dodanie puli elastycznej do grupy trybu failover spowoduje automatyczne uruchomienie procesu replikacji geograficznej.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Utwórz grupę trybu failover przy użyciu programu PowerShell.

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Create a secondary server in the failover region
   Write-host "Creating a secondary server in the failover region..."
   New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   Write-host "Secondary server =" $drServerName

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary server..."
   New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   Write-host "Firewall configured"

   # Create secondary Gen5 elastic 2 vCore pool
   Write-host "Creating secondary elastic pool..."
   $elasticPool = New-AzSqlElasticPool -ResourceGroupName $resourceGroupName `
       -ServerName $drServerName `
       -ElasticPoolName $poolName `
       -Edition "GeneralPurpose" `
       -vCore 2 `
       -ComputeGeneration Gen5
   $elasticPool

   # Create a failover group between the servers
   Write-host "Creating failover group..."
   New-AzSqlDatabaseFailoverGroup `
     –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   Write-host "Failover group created successfully."

   # Add elastic pool to the failover group
   Write-host "Enumerating databases in elastic pool...."
   $FailoverGroup = Get-AzSqlDatabaseFailoverGroup `
                    -ResourceGroupName $resourceGroupName `
                    -ServerName $serverName `
                    -FailoverGroupName $failoverGroupName
   $databases = Get-AzSqlElasticPoolDatabase `
                  -ResourceGroupName $resourceGroupName `
                  -ServerName $serverName `
                  -ElasticPoolName $poolName
   Write-host "Adding databases to failover group..."
   $failoverGroup = $failoverGroup | Add-AzSqlDatabaseToFailoverGroup `
                                     -Database $databases
   $failoverGroup
   ```

W tej części samouczka są stosowane następujące polecenia cmdlet programu PowerShell:

| Polecenie | Uwagi |
|---|---|
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Tworzy serwer, który hostuje bazy danych i pule elastyczne. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Tworzy regułę zapory dla serwera. |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Tworzy pulę elastyczną dla Azure SQL Database.|
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Tworzy nową grupę trybu failover. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Dodaje co najmniej jedną bazę danych SQL platformy Azure do grupy trybu failover. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Pobiera lub wyświetla listę grup Azure SQL Database trybu failover. |

---

## <a name="4---test-failover"></a>4 — Testowanie pracy w trybie failover

W tym kroku nastąpi niepowodzenie grupy trybu failover na serwerze pomocniczym, a następnie powrót po awarii przy użyciu Azure Portal.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Testowanie pracy w trybie failover grupy trybu failover przy użyciu Azure Portal.

1. Wybierz pozycję **Azure SQL** w menu po lewej stronie [Azure Portal](https://portal.azure.com). Jeśli na liście nie ma **usługi Azure SQL** , wybierz pozycję **wszystkie usługi**, a następnie wpisz SQL Azure w polu wyszukiwania. Obowiązkowe Wybierz gwiazdkę obok pozycji **Azure SQL** , aby ją dodać do ulubionych, i Dodaj ją jako element w nawigacji po lewej stronie.
1. Wybierz pulę elastyczną utworzoną w poprzedniej sekcji, na przykład `myElasticPool` .
1. Wybierz nazwę serwera w polu **Nazwa serwera** , aby otworzyć ustawienia serwera.

    ![Otwórz serwer dla puli elastycznej](./media/failover-group-add-elastic-pool-tutorial/server-for-elastic-pool.png)

1. Wybierz pozycję **grupy trybu failover** w okienku **Ustawienia** , a następnie wybierz grupę trybu failover utworzoną w sekcji 2.
  
   ![Wybieranie grupy trybu failover z portalu](./media/failover-group-add-elastic-pool-tutorial/select-failover-group.png)

1. Sprawdź, który serwer jest podstawowy i który serwer jest serwerem pomocniczym.
1. Wybierz pozycję **tryb failover** z okienka zadań, aby przełączyć grupę trybu failover w tryb pracy awaryjnej zawierającej pulę elastyczną.
1. Na ostrzeżeniu wybierz pozycję **tak** , aby powiadomić, że sesje TDS zostaną rozłączone.

   ![Praca awaryjna grupy trybu failover zawierającej bazę danych](./media/failover-group-add-elastic-pool-tutorial/failover-sql-db.png)

1. Sprawdź, który serwer jest serwerem podstawowym, który serwer jest serwerem pomocniczym. Jeśli praca awaryjna zakończyła się pomyślnie, te dwa serwery powinny mieć zamienione role.
1. Wybierz ponownie **tryb failover** , aby zakończyć pracę grupy trybu failover z powrotem do oryginalnych ustawień.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Testowanie pracy w trybie failover grupy trybu failover przy użyciu programu PowerShell.

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Check role of secondary replica
   Write-host "Confirming the secondary server is secondary...."
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole

   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failover group failed over to" $drServerName
   ```

Niepowodzenie grupy trybu failover na serwerze pomocniczym, a następnie powrót po awarii przy użyciu programu PowerShell.

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Check role of secondary replica
   Write-host "Confirming the secondary server is now primary"
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole

   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failover group failed over to" $serverName
   ```

W tej części samouczka są stosowane następujące polecenia cmdlet programu PowerShell:

| Polecenie | Uwagi |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Pobiera lub wyświetla listę grup Azure SQL Database trybu failover. |
| [Przełącznik-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Wykonuje tryb failover grupy trybu failover Azure SQL Database. |

---

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Wyczyść zasoby, usuwając grupę zasobów.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Przejdź do grupy zasobów w [Azure Portal](https://portal.azure.com).
1. Wybierz pozycję  **Usuń grupę zasobów** , aby usunąć wszystkie zasoby w grupie, a także samą grupę zasobów.
1. Wpisz nazwę grupy zasobów, `myResourceGroup` w polu tekstowym, a następnie wybierz pozycję **Usuń** , aby usunąć grupę zasobów.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Wyczyść zasoby przy użyciu programu PowerShell.

   ```powershell-interactive
   # Set variables for your server and database
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"

   # Clean up resources by removing the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

W tej części samouczka jest stosowane następujące polecenie cmdlet programu PowerShell:

| Polecenie | Uwagi |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów |

---

> [!IMPORTANT]
> Jeśli chcesz zachować grupę zasobów, ale usunąć pomocniczą bazę danych, usuń ją z grupy trybu failover przed jej usunięciem. Usunięcie pomocniczej bazy danych przed jej usunięciem z grupy trybu failover może spowodować nieprzewidywalne zachowanie.

## <a name="full-script"></a>Pełny skrypt

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-ps.ps1 "Add elastic pool to a failover group")]

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Tworzy serwer, który hostuje bazy danych i pule elastyczne. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Tworzy regułę zapory dla serwera. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Tworzy bazę danych. |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Tworzy elastyczną pulę baz danych dla Azure SQL Database.|
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Ustawia właściwości bazy danych lub przenosi istniejącą bazę danych do puli elastycznej. |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Tworzy nową grupę trybu failover. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Pobiera co najmniej jedną bazę danych w SQL Database. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Dodaje co najmniej jedną bazę danych SQL platformy Azure do grupy trybu failover. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Pobiera lub wyświetla listę grup Azure SQL Database trybu failover. |
| [Przełącznik-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Wykonuje tryb failover grupy trybu failover Azure SQL Database. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów |

# <a name="portal"></a>[Portal](#tab/azure-portal)

Brak skryptów dostępnych dla Azure Portal.

---

## <a name="next-steps"></a>Następne kroki

W tym samouczku dodano pulę elastyczną Azure SQL Database do grupy trybu failover i przetestowano tryb failover. W tym samouczku omówiono:

> [!div class="checklist"]
>
> - Utwórz pojedynczą bazę danych.
> - Dodaj bazę danych do puli elastycznej.
> - Utwórz [grupę trybu failover](auto-failover-group-overview.md) dla dwóch pul elastycznych między dwoma serwerami.
> - Testowanie pracy w trybie failover.

Przejdź do następnego samouczka dotyczącego migracji za pomocą usługi DMS.

> [!div class="nextstepaction"]
> [Samouczek: Migrowanie SQL Server do bazy danych w puli za pomocą usługi DMS](../../dms/tutorial-sql-server-to-azure-sql.md?toc=/azure/sql-database/toc.json)
