---
title: Konfiguruj okno obsługi (wersja zapoznawcza)
description: Dowiedz się, jak ustawić czas, w którym planowana konserwacja powinna być wykonywana w bazach danych Azure SQL Database, elastycznych pulach i zarządzanych wystąpieniach.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/04/2021
ms.openlocfilehash: f5692f2bcaaadfc56084ec8db745eae48112ecf3
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102101243"
---
# <a name="configure-maintenance-window-preview"></a>Konfiguruj okno obsługi (wersja zapoznawcza)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]


Skonfiguruj [okno obsługi (wersja zapoznawcza)](maintenance-window.md) dla bazy danych Azure SQL Database, puli elastycznej lub bazy danych wystąpienia zarządzanego Azure SQL podczas tworzenia zasobów lub w dowolnym momencie po utworzeniu zasobu. 

Domyślne okno obsługi *systemu* to 17:00 8:00 (czas lokalny w regionie platformy Azure, w którym znajduje się zasób), aby uniknąć szczytu przerw w pracy w godzinach pracy. Jeśli domyślny czas okna obsługi *systemu* nie jest najlepszy, wybierz jedno z innych dostępnych okien obsługi.

Możliwość zmiany w innym oknie obsługi nie jest dostępna dla każdego poziomu usług lub w każdym regionie. Aby uzyskać szczegółowe informacje na temat dostępności, zobacz [dostępność okna obsługi](maintenance-window.md#availability).

> [!Important]
> Konfigurowanie okna obsługi jest długotrwałą operacją asynchroniczną, podobną do zmiany warstwy usług zasobu SQL platformy Azure. Zasób jest dostępny podczas operacji, z wyjątkiem krótkiej pracy w trybie failover, która jest wykonywana na końcu operacji i zazwyczaj trwa do 8 sekund nawet w przypadku przerwanych długotrwałych transakcji. Aby zminimalizować wpływ trybu failover, należy wykonać operację poza godzinami szczytu.

## <a name="configure-maintenance-window-during-database-creation"></a>Konfiguruj okno obsługi podczas tworzenia bazy danych 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby skonfigurować okno obsługi podczas tworzenia bazy danych, puli elastycznej lub wystąpienia zarządzanego, należy ustawić odpowiednie **okno obsługi** na stronie **Ustawienia dodatkowe** . 

## <a name="set-the-maintenance-window-while-creating-a-single-database-or-elastic-pool"></a>Ustawianie okna obsługi podczas tworzenia pojedynczej bazy danych lub puli elastycznej

Aby uzyskać informacje krok po kroku dotyczące tworzenia nowej bazy danych lub puli, zobacz [tworzenie Azure SQL Database pojedynczej bazy danych](single-database-create-quickstart.md).

   :::image type="content" source="media/maintenance-window-configure/additional-settings.png" alt-text="Karta Tworzenie dodatkowych ustawień bazy danych":::


## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>Ustaw okno obsługi podczas tworzenia wystąpienia zarządzanego

Informacje krok po kroku dotyczące tworzenia nowego wystąpienia zarządzanego można znaleźć w temacie [Tworzenie wystąpienia zarządzanego usługi Azure SQL](../managed-instance/instance-create-quickstart.md).

   :::image type="content" source="media/maintenance-window-configure/additional-settings-mi.png" alt-text="Karta Tworzenie ustawień dodatkowych wystąpienia zarządzanego":::




# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

W poniższych przykładach pokazano, jak skonfigurować okno obsługi przy użyciu Azure PowerShell. Można [zainstalować Azure PowerShell](/powershell/azure/install-az-ps)lub użyć Azure Cloud Shell.

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. 

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Cloud Shell można również uruchomić na osobnej karcie przeglądarki, przechodząc do [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.


## <a name="discover-available-maintenance-windows"></a>Odkryj dostępne okna obsługi

Podczas ustawiania okna obsługi każdy region ma własne opcje okna obsługi odpowiadające strefie czasowej dla regionu, w którym znajduje się baza danych lub Pula. 

### <a name="discover-sql-database-and-elastic-pool-maintenance-windows"></a>Odnajdź okna obsługi SQL Database i elastycznych pul 

Poniższy przykład zwraca dostępne okna obsługi dla regionu *eastus2* przy użyciu polecenia cmdlet [Get-AzMaintenancePublicConfiguration](/powershell/module/az.maintenance/get-azmaintenancepublicconfiguration) . W przypadku baz danych i pul elastycznych ustaw wartość `MaintenanceScope` `SQLDB` .

   ```powershell-interactive
   $location = "eastus2"

   Write-Host "Available maintenance schedules in ${location}:"
   $configurations = Get-AzMaintenancePublicConfiguration
   $configurations | ?{ $_.Location -eq $location -and $_.MaintenanceScope -eq "SQLDB"}
   ```

### <a name="discover-sql-managed-instance-maintenance-windows"></a>Odnajdź okna obsługi wystąpienia zarządzanego SQL 

Poniższy przykład zwraca dostępne okna obsługi dla regionu *eastus2* przy użyciu polecenia cmdlet [Get-AzMaintenancePublicConfiguration](/powershell/module/az.maintenance/get-azmaintenancepublicconfiguration) . Dla wystąpień zarządzanych ustaw wartość `MaintenanceScope` `SQLManagedInstance` .

   ```powershell-interactive
   $location = "eastus2"

   Write-Host "Available maintenance schedules in ${location}:"
   $configurations = Get-AzMaintenancePublicConfiguration
   $configurations | ?{ $_.Location -eq $location -and $_.MaintenanceScope -eq "SQLManagedInstance"}
   ```


## <a name="set-the-maintenance-window-while-creating-a-single-database"></a>Ustaw okno obsługi podczas tworzenia pojedynczej bazy danych

Poniższy przykład tworzy nową bazę danych i ustawia okno obsługi przy użyciu polecenia cmdlet [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) . `-MaintenanceConfigurationId`Dla regionu bazy danych musi być ustawiona prawidłowa wartość. Aby uzyskać prawidłowe wartości dla regionu, zobacz [odnajdywanie dostępnych okien obsługi](#discover-available-maintenance-windows).


   ```powershell-interactive
    # Set variables for your database
    $resourceGroupName = "your_resource_group_name"
    $serverName = "your_server_name"
    $databaseName = "your_db_name"
    
    # Set selected maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_1"

    Write-host "Creating a gen5 2 vCore database with maintenance window ${maintenanceConfig} ..."
    $database = New-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -Edition GeneralPurpose `
      -ComputeGeneration Gen5 `
      -VCore 2 `
      -MaintenanceConfigurationId $maintenanceConfig
    $database
   ```



## <a name="set-the-maintenance-window-while-creating-an-elastic-pool"></a>Ustawianie okna obsługi podczas tworzenia puli elastycznej

Poniższy przykład tworzy nową pulę elastyczną i ustawia okno obsługi przy użyciu polecenia cmdlet [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) . Okno obsługi jest ustawiane w puli elastycznej, więc wszystkie bazy danych w puli mają harmonogram okna obsługi puli. `-MaintenanceConfigurationId`Dla regionu puli musi być ustawiona prawidłowa wartość. Aby uzyskać prawidłowe wartości dla regionu, zobacz [odnajdywanie dostępnych okien obsługi](#discover-available-maintenance-windows).


   ```powershell-interactive
    # Set variables for your pool
    $resourceGroupName = "your_resource_group_name"
    $serverName = "your_server_name"
    $poolName = "your_pool_name"
    
    # Set selected maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_2"

    Write-host "Creating a Standard 50 pool with maintenance window ${maintenanceConfig} ..."
    $pool = New-AzSqlElasticPool `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -ElasticPoolName $poolName `
      -Edition "Standard" `
      -Dtu 50 `
      -DatabaseDtuMin 10 `
      -DatabaseDtuMax 20 `
      -MaintenanceConfigurationId $maintenanceConfig
    $pool
   ```

## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>Ustaw okno obsługi podczas tworzenia wystąpienia zarządzanego

Poniższy przykład tworzy nowe wystąpienie zarządzane i ustawia okno obsługi przy użyciu polecenia cmdlet [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) . Okno obsługi jest ustawione na wystąpienie, więc wszystkie bazy danych w wystąpieniu mają harmonogram okna obsługi. W przypadku elementu `-MaintenanceConfigurationId` *MaintenanceConfigName* musi być prawidłową wartością dla regionu wystąpienia. Aby uzyskać prawidłowe wartości dla regionu, zobacz [odnajdywanie dostępnych okien obsługi](#discover-available-maintenance-windows).


   ```powershell
   New-AzSqlInstance -Name "your_mi_name" `
     -ResourceGroupName "your_resource_group_name" `
     -Location "your_mi_location" `
     -SubnetId /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNETName}/subnets/{SubnetName} `
     -MaintenanceConfigurationId "/subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}" `
     -AsJob
   ```

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/azure-cli)

W poniższych przykładach pokazano, jak skonfigurować okno obsługi przy użyciu interfejsu wiersza polecenia platformy Azure. Możesz [zainstalować interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)lub użyć Azure Cloud Shell. 

Konfigurowanie okna obsługi przy użyciu interfejsu wiersza polecenia platformy Azure jest dostępne tylko dla wystąpienia zarządzanego SQL.

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. 

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Cloud Shell można również uruchomić na osobnej karcie przeglądarki, przechodząc do [https://shell.azure.com/cli](https://shell.azure.com/cli) . Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.

## <a name="discover-available-maintenance-windows"></a>Odkryj dostępne okna obsługi

Podczas ustawiania okna obsługi każdy region ma własne opcje okna obsługi odpowiadające strefie czasowej dla regionu, w którym znajduje się baza danych lub Pula.

### <a name="discover-sql-database-and-elastic-pool-maintenance-windows"></a>Odnajdź okna obsługi SQL Database i elastycznych pul

Poniższy przykład zwraca dostępne okna obsługi dla regionu *eastus2* za pomocą polecenia [AZ Maintenance Public-Configuration list](/cli/azure/ext/maintenance/maintenance/public-configuration?view=azure-cli-latest&preserve-view=true#ext_maintenance_az_maintenance_public_configuration_list) . W przypadku baz danych i pul elastycznych ustaw wartość `maintenanceScope` `SQLDB` .

   ```azurecli
   location="eastus2"

   az maintenance public-configuration list --query "[?location=='$location'&&contains(maintenanceScope,'SQLDB')]"
   ```

### <a name="discover-sql-managed-instance-maintenance-windows"></a>Odnajdź okna obsługi wystąpienia zarządzanego SQL

Poniższy przykład zwraca dostępne okna obsługi dla regionu *eastus2* za pomocą polecenia [AZ Maintenance Public-Configuration list](/cli/azure/ext/maintenance/maintenance/public-configuration?view=azure-cli-latest&preserve-view=true#ext_maintenance_az_maintenance_public_configuration_list) . Dla wystąpień zarządzanych ustaw wartość `maintenanceScope` `SQLManagedInstance` .

   ```azurecli
   az maintenance public-configuration list --query "[?location=='eastus2'&&contains(maintenanceScope,'SQLManagedInstance')]"
   ```

## <a name="set-the-maintenance-window-while-creating-a-single-database"></a>Ustaw okno obsługi podczas tworzenia pojedynczej bazy danych

Poniższy przykład tworzy nową bazę danych i ustawia okno obsługi przy użyciu polecenia [AZ SQL DB Create](/cli/azure/sql/db#az_sql_db_create) . `--maint-config-id`Dla (lub `-m` ) musi być ustawiona prawidłowa wartość dla regionu bazy danych. Aby uzyskać prawidłowe wartości dla regionu, zobacz [odnajdywanie dostępnych okien obsługi](#discover-available-maintenance-windows).


   ```azurecli
    # Set variables for your database
    resourceGroupName="your_resource_group_name"
    serverName="your_server_name"
    databaseName="your_db_name"

    # Set selected maintenance window
    maintenanceConfig="SQL_EastUS2_DB_1"

    # Create database
    az sql db create \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName \
      --edition GeneralPurpose \
      --family Gen5 \
      --capacity 2 \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-while-creating-an-elastic-pool"></a>Ustawianie okna obsługi podczas tworzenia puli elastycznej

Poniższy przykład tworzy nową pulę elastyczną i ustawia okno obsługi, korzystając z polecenia [AZ SQL Elastic-Pool Create](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_create) . Okno obsługi jest ustawiane w puli elastycznej, więc wszystkie bazy danych w puli mają harmonogram okna obsługi puli. `--maint-config-id`Dla (lub `-m` ) musi być ustawiona prawidłowa wartość dla regionu puli. Aby uzyskać prawidłowe wartości dla regionu, zobacz [odnajdywanie dostępnych okien obsługi](#discover-available-maintenance-windows).


   ```azurecli
    # Set variables for your pool
    resourceGroupName="your_resource_group_name"
    serverName="your_server_name"
    poolName="your_pool_name"

    # Set selected maintenance window
    maintenanceConfig="SQL_EastUS2_DB_2"

    # Create elastic pool
    az sql elastic-pool create \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $poolName \
      --edition GeneralPurpose \
      --family Gen5 \
      --capacity 2 \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>Ustaw okno obsługi podczas tworzenia wystąpienia zarządzanego

Poniższy przykład tworzy nowe wystąpienie zarządzane i ustawia okno obsługi przy użyciu polecenia [AZ SQL mi Create](/cli/azure/sql/mi#az_sql_mi_create). Okno obsługi jest ustawione na wystąpienie, więc wszystkie bazy danych w wystąpieniu mają harmonogram okna obsługi. *MaintenanceConfigName* musi być prawidłową wartością dla regionu wystąpienia. Aby uzyskać prawidłowe wartości dla regionu, zobacz [odnajdywanie dostępnych okien obsługi](#discover-available-maintenance-windows).

   ```azurecli
   az sql mi create -g mygroup -n myinstance -l mylocation -i -u myusername -p mypassword --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNETName}/subnets/{SubnetName} -m /subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}
   ```

-----

## <a name="configure-maintenance-window-for-existing-databases"></a>Konfigurowanie okna obsługi dla istniejących baz danych


Podczas stosowania wyboru okna obsługi do bazy danych w niektórych przypadkach może wystąpić krótki tryb failover (kilka sekund), ponieważ platforma Azure stosuje wymagane zmiany.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Poniższe kroki ustawiają okno obsługi w istniejącej bazie danych, puli elastycznej lub wystąpieniu zarządzanym przy użyciu Azure Portal:


## <a name="set-the-maintenance-window-for-an-existing-database-or-elastic-pool"></a>Ustawianie okna obsługi dla istniejącej bazy danych lub puli elastycznej

1. Przejdź do bazy danych SQL lub puli elastycznej, dla której chcesz ustawić okno obsługi.
1. W menu **Ustawienia** wybierz pozycję **konserwacja**, a następnie wybierz odpowiednie okno obsługi.

   :::image type="content" source="media/maintenance-window-configure/maintenance.png" alt-text="Strona obsługi bazy danych SQL":::


## <a name="set-the-maintenance-window-for-an-existing-managed-instance"></a>Ustawianie okna obsługi dla istniejącego wystąpienia zarządzanego

1. Przejdź do wystąpienia zarządzanego, dla którego chcesz ustawić okno obsługi.
1. W menu **Ustawienia** wybierz pozycję **konserwacja**, a następnie wybierz odpowiednie okno obsługi.

   :::image type="content" source="media/maintenance-window-configure/maintenance-mi.png" alt-text="Strona konserwacji wystąpienia zarządzanego SQL":::



# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

## <a name="set-the-maintenance-window-for-an-existing-database"></a>Ustawianie okna obsługi dla istniejącej bazy danych

Poniższy przykład ustawia okno obsługi w istniejącej bazie danych przy użyciu polecenia cmdlet [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) . `-MaintenanceConfigurationId`Dla regionu bazy danych musi być ustawiona prawidłowa wartość. Aby uzyskać prawidłowe wartości dla regionu, zobacz [odnajdywanie dostępnych okien obsługi](#discover-available-maintenance-windows).

   ```powershell-interactive
    # Select different maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_2"

    Write-host "Changing database maintenance window to ${maintenanceConfig} ..."
    $database = Set-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -MaintenanceConfigurationId $maintenanceConfig
    $database
   ```

## <a name="set-the-maintenance-window-on-an-existing-elastic-pool"></a>Ustawianie okna obsługi w istniejącej puli elastycznej

Poniższy przykład ustawia okno obsługi w istniejącej puli elastycznej przy użyciu polecenia cmdlet [Set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool) . Ważne jest, aby upewnić się, że `$maintenanceConfig` wartość jest prawidłową wartością dla regionu puli.  Aby uzyskać prawidłowe wartości dla regionu, zobacz [odnajdywanie dostępnych okien obsługi](#discover-available-maintenance-windows).

   ```powershell-interactive
    # Select different maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_1"
    
    Write-host "Changing pool maintenance window to ${maintenanceConfig} ..."
    $pool = Set-AzSqlElasticPool `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -ElasticPoolName $poolName `
      -MaintenanceConfigurationId $maintenanceConfig
    $pool
   ```



## <a name="set-the-maintenance-window-on-an-existing-managed-instance"></a>Ustaw okno obsługi na istniejącym wystąpieniu zarządzanym

Poniższy przykład ustawia okno obsługi na istniejącym wystąpieniu zarządzanym za pomocą polecenia cmdlet [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance) . Ważne jest, aby upewnić się, że `$maintenanceConfig` wartość musi być prawidłową wartością dla regionu wystąpienia.  Aby uzyskać prawidłowe wartości dla regionu, zobacz [odnajdywanie dostępnych okien obsługi](#discover-available-maintenance-windows).


   ```powershell-interactive
   Set-AzSqlInstance -Name "your_mi_name" `
     -ResourceGroupName "your_resource_group_name" `
     -MaintenanceConfigurationId "/subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}" `
     -AsJob
   ```

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/azure-cli)

W poniższych przykładach pokazano, jak skonfigurować okno obsługi przy użyciu interfejsu wiersza polecenia platformy Azure. Możesz [zainstalować interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)lub użyć Azure Cloud Shell.

## <a name="set-the-maintenance-window-for-an-existing-database"></a>Ustawianie okna obsługi dla istniejącej bazy danych

Poniższy przykład ustawia okno obsługi w istniejącej bazie danych przy użyciu polecenia [AZ SQL DB Update](/cli/azure/sql/db#az_sql_db_update) . `--maint-config-id`Dla (lub `-m` ) musi być ustawiona prawidłowa wartość dla regionu bazy danych. Aby uzyskać prawidłowe wartości dla regionu, zobacz [odnajdywanie dostępnych okien obsługi](#discover-available-maintenance-windows).

   ```azurecli
    # Select different maintenance window
    maintenanceConfig="SQL_EastUS2_DB_2"

    # Update database
    az sql db update \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-on-an-existing-elastic-pool"></a>Ustawianie okna obsługi w istniejącej puli elastycznej

Poniższy przykład ustawia okno obsługi w istniejącej puli elastycznej przy użyciu polecenia [AZ SQL Elastic-Pool Update](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) . Ważne jest, aby upewnić się, że `maintenanceConfig` wartość jest prawidłową wartością dla regionu puli.  Aby uzyskać prawidłowe wartości dla regionu, zobacz [odnajdywanie dostępnych okien obsługi](#discover-available-maintenance-windows).

   ```azurecli
    # Select different maintenance window
    maintenanceConfig="SQL_EastUS2_DB_1"

    # Update pool
    az sql elastic-pool update \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $poolName \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-on-an-existing-managed-instance"></a>Ustaw okno obsługi na istniejącym wystąpieniu zarządzanym

Poniższy przykład ustawia okno obsługi przy użyciu polecenia [AZ SQL mi Update](/cli/azure/sql/mi#az_sql_mi_update). Okno obsługi jest ustawione na wystąpienie, więc wszystkie bazy danych w wystąpieniu mają harmonogram okna obsługi. W przypadku elementu `-MaintenanceConfigurationId` *MaintenanceConfigName* musi być prawidłową wartością dla regionu wystąpienia. Aby uzyskać prawidłowe wartości dla regionu, zobacz [odnajdywanie dostępnych okien obsługi](#discover-available-maintenance-windows).

   ```azurecli
   az sql mi update -g mygroup  -n myinstance -m /subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MainteanceConfigName}
   ```

-----

## <a name="cleanup-resources"></a>Oczyszczanie zasobów

Pamiętaj o usunięciu niepotrzebnych zasobów po zakończeniu ich pracy, aby uniknąć niepotrzebnych opłat.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Przejdź do bazy danych SQL lub puli elastycznej, która nie jest już potrzebna.
1. W menu **Przegląd** wybierz opcję usunięcia zasobu.


# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   # Delete database
   Remove-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName

   # Delete elastic pool
   Remove-AzSqlElasticPool `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -ElasticPoolName $poolName
   ```

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/azure-cli)

   ```azurecli
   az sql db delete \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName

   az sql elastic-pool delete \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $poolName
   ```

-----

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o oknie obsługi, zobacz [okno obsługi (wersja zapoznawcza)](maintenance-window.md).
- Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące okna obsługi](maintenance-window-faq.yml).
- Aby dowiedzieć się więcej o optymalizacji wydajności, zobacz [monitorowanie i dostrajanie wydajności w Azure SQL Database i wystąpieniu zarządzanym usługi Azure SQL](monitor-tune-overview.md).
