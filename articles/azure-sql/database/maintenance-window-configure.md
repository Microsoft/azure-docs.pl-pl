---
title: Konfigurowanie okna obsługi (wersja zapoznawcza)
description: Dowiedz się, jak ustawić czas, w którym planowana konserwacja ma być wykonywana w bazach danych Azure SQL, elastycznych pulach i bazach danych wystąpień zarządzanych.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/23/2021
ms.openlocfilehash: 9771c68dda6f457586f27ea45fbc52aa118e8006
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874787"
---
# <a name="configure-maintenance-window-preview"></a>Konfigurowanie okna obsługi (wersja zapoznawcza)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]


Skonfiguruj okno [obsługi (wersja zapoznawcza)](maintenance-window.md) dla bazy Azure SQL, elastycznej puli lub bazy danych Azure SQL Managed Instance podczas tworzenia zasobu lub w dowolnym momencie po utworzeniu zasobu. 

Domyślne *okno obsługi* systemu jest codziennie od 17:00 do 20:00 (lokalny czas regionu świadczenia usługi Azure, w ramach których znajduje się zasób), aby uniknąć przerw w godzinach szczytu. Jeśli domyślne *okno obsługi* System nie jest najlepszym czasem, wybierz jedno z innych dostępnych okien obsługi.

Możliwość zmiany na inne okno obsługi nie jest dostępna dla każdego poziomu usługi ani w każdym regionie. Aby uzyskać szczegółowe informacje na temat dostępności, zobacz [Dostępność okna obsługi](maintenance-window.md#availability).

> [!Important]
> Konfigurowanie okna obsługi jest długotrwałą operacją asynchroniczną podobną do zmiany warstwy usługi Azure SQL zasobów. Zasób jest dostępny podczas operacji, z wyjątkiem krótkiej ponownej konfiguracji, która ma miejsce na końcu operacji i zwykle trwa do 8 sekund, nawet w przypadku przerwanych długotrwałych transakcji. Aby zminimalizować wpływ ponownej konfiguracji, należy wykonać operację poza godzinami szczytu.

## <a name="configure-maintenance-window-during-database-creation"></a>Konfigurowanie okna obsługi podczas tworzenia bazy danych 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby skonfigurować okno obsługi podczas tworzenia bazy danych, elastycznej puli lub wystąpienia zarządzanego, ustaw odpowiednie okno **Konserwacja** na **stronie Ustawienia** dodatkowe. 

## <a name="set-the-maintenance-window-while-creating-a-single-database-or-elastic-pool"></a>Ustawianie okna obsługi podczas tworzenia pojedynczej bazy danych lub elastycznej puli

Aby uzyskać szczegółowe informacje na temat tworzenia nowej bazy danych lub puli, zobacz Tworzenie bazy [Azure SQL Database pojedynczej bazy danych.](single-database-create-quickstart.md)

   :::image type="content" source="media/maintenance-window-configure/additional-settings.png" alt-text="Karta Dodatkowych ustawień tworzenia bazy danych":::


## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>Ustawianie okna obsługi podczas tworzenia wystąpienia zarządzanego

Aby uzyskać szczegółowe informacje na temat tworzenia nowego wystąpienia zarządzanego, zobacz [Tworzenie Azure SQL Managed Instance](../managed-instance/instance-create-quickstart.md).

   :::image type="content" source="media/maintenance-window-configure/additional-settings-mi.png" alt-text="Karta Tworzenie dodatkowych ustawień wystąpienia zarządzanego":::




# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Poniższe przykłady pokazują, jak skonfigurować okno obsługi przy użyciu Azure PowerShell. Możesz zainstalować [program Azure PowerShell](/powershell/azure/install-az-ps)lub użyć Azure Cloud Shell.

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. 

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Możesz również uruchomić Cloud Shell na osobnej karcie przeglądarki, przechodząc do strony [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.


## <a name="discover-available-maintenance-windows"></a>Odnajdywanie dostępnych okien obsługi

Podczas ustawiania okna obsługi każdy region ma własne opcje okna obsługi, które odpowiadają strefie czasowej dla regionu, w którym znajduje się baza danych lub pula. 

### <a name="discover-sql-database-and-elastic-pool-maintenance-windows"></a>Odnajdywanie SQL Database i okien obsługi puli elastycznej 

Poniższy przykład zwraca dostępne okna obsługi dla *regionu eastus2* za pomocą polecenia cmdlet [Get-AzMaintenancePublicConfiguration.](/powershell/module/az.maintenance/get-azmaintenancepublicconfiguration) W przypadku baz danych i pul elastycznych ustaw `MaintenanceScope` wartość `SQLDB` .

   ```powershell-interactive
   $location = "eastus2"

   Write-Host "Available maintenance schedules in ${location}:"
   $configurations = Get-AzMaintenancePublicConfiguration
   $configurations | ?{ $_.Location -eq $location -and $_.MaintenanceScope -eq "SQLDB"}
   ```

### <a name="discover-sql-managed-instance-maintenance-windows"></a>Odnajdywanie SQL Managed Instance okien obsługi 

Poniższy przykład zwraca dostępne okna obsługi dla *regionu eastus2* za pomocą polecenia cmdlet [Get-AzMaintenancePublicConfiguration.](/powershell/module/az.maintenance/get-azmaintenancepublicconfiguration) W przypadku wystąpień zarządzanych ustaw `MaintenanceScope` wartość `SQLManagedInstance` .

   ```powershell-interactive
   $location = "eastus2"

   Write-Host "Available maintenance schedules in ${location}:"
   $configurations = Get-AzMaintenancePublicConfiguration
   $configurations | ?{ $_.Location -eq $location -and $_.MaintenanceScope -eq "SQLManagedInstance"}
   ```


## <a name="set-the-maintenance-window-while-creating-a-single-database"></a>Ustawianie okna obsługi podczas tworzenia pojedynczej bazy danych

Poniższy przykład tworzy nową bazę danych i ustawia okno obsługi przy użyciu polecenia cmdlet [New-AzSqlDatabase.](/powershell/module/az.sql/new-azsqldatabase) Musi `-MaintenanceConfigurationId` być ustawiona na prawidłową wartość dla regionu bazy danych. Aby uzyskać prawidłowe wartości dla swojego regionu, zobacz [Odnajdywanie dostępnych okien obsługi](#discover-available-maintenance-windows).


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

Poniższy przykład tworzy nową pulę elastyczną i ustawia okno obsługi przy użyciu polecenia cmdlet [New-AzSqlElasticPool.](/powershell/module/az.sql/new-azsqlelasticpool) Okno obsługi jest ustawiane w elastycznej puli, więc wszystkie bazy danych w puli mają harmonogram okna obsługi puli. Wartość `-MaintenanceConfigurationId` musi być ustawiona na prawidłową wartość dla regionu puli. Aby uzyskać prawidłowe wartości dla swojego regionu, zobacz [Discover available maintenance windows (Odnajdywanie dostępnych okien obsługi).](#discover-available-maintenance-windows)


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

## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>Ustawianie okna obsługi podczas tworzenia wystąpienia zarządzanego

Poniższy przykład tworzy nowe wystąpienie zarządzane i ustawia okno obsługi przy użyciu polecenia cmdlet [New-AzSqlInstance.](/powershell/module/az.sql/new-azsqlinstance) Okno obsługi jest ustawiane w wystąpieniu, więc wszystkie bazy danych w wystąpieniu mają harmonogram okna obsługi wystąpienia. W `-MaintenanceConfigurationId` przypadku *parametru nazwa_konfiguracji_konserwacji* musi być prawidłową wartością dla regionu wystąpienia. Aby uzyskać prawidłowe wartości dla swojego regionu, zobacz [Discover available maintenance windows (Odnajdywanie dostępnych okien obsługi).](#discover-available-maintenance-windows)


   ```powershell
   New-AzSqlInstance -Name "your_mi_name" `
     -ResourceGroupName "your_resource_group_name" `
     -Location "your_mi_location" `
     -SubnetId /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNETName}/subnets/{SubnetName} `
     -MaintenanceConfigurationId "/subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}" `
     -AsJob
   ```

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/azure-cli)

Poniższe przykłady pokazują, jak skonfigurować okno obsługi przy użyciu interfejsu wiersza polecenia platformy Azure. Możesz zainstalować [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)lub użyć Azure Cloud Shell. 

Konfigurowanie okna obsługi za pomocą interfejsu wiersza polecenia platformy Azure jest dostępne tylko dla SQL Managed Instance.

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. 

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Możesz również uruchomić aplikację Cloud Shell osobnej karcie przeglądarki, przechodząc do strony [https://shell.azure.com/cli](https://shell.azure.com/cli) . Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.

## <a name="discover-available-maintenance-windows"></a>Odnajdywanie dostępnych okien obsługi

Podczas ustawiania okna obsługi każdy region ma własne opcje okna obsługi, które odpowiadają strefie czasowej dla regionu, w którym znajduje się baza danych lub pula.

### <a name="discover-sql-database-and-elastic-pool-maintenance-windows"></a>Odnajdywanie SQL Database i okien obsługi puli elastycznej

Poniższy przykład zwraca dostępne okna obsługi dla *regionu eastus2* za pomocą [polecenia az maintenance public-configuration list.](/cli/azure/maintenance/public-configuration#az_maintenance_public_configuration_list) W przypadku baz danych i pul elastycznych ustaw `maintenanceScope` wartość `SQLDB` .

   ```azurecli
   location="eastus2"

   az maintenance public-configuration list --query "[?location=='$location'&&contains(maintenanceScope,'SQLDB')]"
   ```

### <a name="discover-sql-managed-instance-maintenance-windows"></a>Odnajdywanie SQL Managed Instance okien obsługi

Poniższy przykład zwraca dostępne okna obsługi dla *regionu eastus2* za pomocą [polecenia az maintenance public-configuration list.](/cli/azure/maintenance/public-configuration#az_maintenance_public_configuration_list) W przypadku wystąpień zarządzanych ustaw `maintenanceScope` wartość `SQLManagedInstance` .

   ```azurecli
   az maintenance public-configuration list --query "[?location=='eastus2'&&contains(maintenanceScope,'SQLManagedInstance')]"
   ```

## <a name="set-the-maintenance-window-while-creating-a-single-database"></a>Ustawianie okna obsługi podczas tworzenia pojedynczej bazy danych

Poniższy przykład tworzy nową bazę danych i ustawia okno obsługi za pomocą [polecenia az sql db create.](/cli/azure/sql/db#az_sql_db_create) Wartość `--maint-config-id` (lub `-m` ) musi być ustawiona na prawidłową wartość dla regionu bazy danych. Aby uzyskać prawidłowe wartości dla swojego regionu, zobacz [Discover available maintenance windows (Odnajdywanie dostępnych okien obsługi).](#discover-available-maintenance-windows)


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

## <a name="set-the-maintenance-window-while-creating-an-elastic-pool"></a>Ustawianie okna obsługi podczas tworzenia elastycznej puli

Poniższy przykład tworzy nową pulę elastyczną i ustawia okno obsługi za pomocą polecenia cmdlet [az sql elastic-pool create.](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_create) Okno obsługi jest ustawione w elastycznej puli, więc wszystkie bazy danych w puli mają harmonogram okna obsługi puli. Wartość `--maint-config-id` (lub `-m` ) musi być ustawiona na prawidłową wartość dla regionu puli. Aby uzyskać prawidłowe wartości dla swojego regionu, zobacz [Discover available maintenance windows (Odnajdywanie dostępnych okien obsługi).](#discover-available-maintenance-windows)


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

## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>Ustawianie okna obsługi podczas tworzenia wystąpienia zarządzanego

Poniższy przykład tworzy nowe wystąpienie zarządzane i ustawia okno obsługi za pomocą [polecenia az sql mi create](/cli/azure/sql/mi#az_sql_mi_create). Okno obsługi jest ustawiane w wystąpieniu, więc wszystkie bazy danych w wystąpieniu mają harmonogram okna obsługi wystąpienia. *Parametr MaintenanceConfigName* musi być prawidłową wartością regionu wystąpienia. Aby uzyskać prawidłowe wartości dla swojego regionu, zobacz [Discover available maintenance windows (Odnajdywanie dostępnych okien obsługi).](#discover-available-maintenance-windows)

   ```azurecli
   az sql mi create -g mygroup -n myinstance -l mylocation -i -u myusername -p mypassword --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNETName}/subnets/{SubnetName} -m /subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}
   ```

-----

## <a name="configure-maintenance-window-for-existing-databases"></a>Konfigurowanie okna obsługi dla istniejących baz danych


Podczas stosowania okna obsługi do bazy danych może w niektórych przypadkach wystąpić krótka ponowna konfiguracja (kilka sekund), ponieważ platforma Azure stosuje wymagane zmiany.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Poniższe kroki ustawiają okno obsługi w istniejącej bazie danych, elastycznej puli lub wystąpieniu zarządzanym przy użyciu Azure Portal:


## <a name="set-the-maintenance-window-for-an-existing-database-or-elastic-pool"></a>Ustawianie okna obsługi dla istniejącej bazy danych lub elastycznej puli

1. Przejdź do bazy danych SQL lub elastycznej puli, dla której chcesz ustawić okno obsługi.
1. W menu **Ustawienia** wybierz pozycję **Konserwacja,** a następnie wybierz odpowiednie okno obsługi.

   :::image type="content" source="media/maintenance-window-configure/maintenance.png" alt-text="Strona konserwacji bazy danych SQL":::


## <a name="set-the-maintenance-window-for-an-existing-managed-instance"></a>Ustawianie okna obsługi dla istniejącego wystąpienia zarządzanego

1. Przejdź do wystąpienia zarządzanego, dla którego chcesz ustawić okno obsługi.
1. W menu **Ustawienia** wybierz pozycję **Konserwacja,** a następnie wybierz odpowiednie okno obsługi.

   :::image type="content" source="media/maintenance-window-configure/maintenance-mi.png" alt-text="Strona konserwacji wystąpienia zarządzanego SQL":::



# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

## <a name="set-the-maintenance-window-for-an-existing-database"></a>Ustawianie okna obsługi dla istniejącej bazy danych

Poniższy przykład ustawia okno obsługi w istniejącej bazie danych przy użyciu polecenia cmdlet [Set-AzSqlDatabase.](/powershell/module/az.sql/set-azsqldatabase) Musi `-MaintenanceConfigurationId` być ustawiona na prawidłową wartość dla regionu bazy danych. Aby uzyskać prawidłowe wartości dla swojego regionu, zobacz [Discover available maintenance windows (Odnajdywanie dostępnych okien obsługi).](#discover-available-maintenance-windows)

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

Poniższy przykład ustawia okno obsługi w istniejącej puli elastycznej przy użyciu polecenia cmdlet [Set-AzSqlElasticPool.](/powershell/module/az.sql/set-azsqlelasticpool) Ważne jest, aby upewnić się, że wartość jest prawidłową wartością dla `$maintenanceConfig` regionu puli.  Aby uzyskać prawidłowe wartości dla regionu, zobacz [Odnajdywanie dostępnych okien obsługi](#discover-available-maintenance-windows).

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



## <a name="set-the-maintenance-window-on-an-existing-managed-instance"></a>Ustawianie okna obsługi w istniejącym wystąpieniu zarządzanym

Poniższy przykład ustawia okno obsługi w istniejącym wystąpieniu zarządzanym przy użyciu polecenia cmdlet [Set-AzSqlInstance.](/powershell/module/az.sql/set-azsqlinstance) Ważne jest, aby upewnić się, że wartość musi być prawidłową wartością dla `$maintenanceConfig` regionu wystąpienia.  Aby uzyskać prawidłowe wartości dla regionu, zobacz [Odnajdywanie dostępnych okien obsługi](#discover-available-maintenance-windows).


   ```powershell-interactive
   Set-AzSqlInstance -Name "your_mi_name" `
     -ResourceGroupName "your_resource_group_name" `
     -MaintenanceConfigurationId "/subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}" `
     -AsJob
   ```

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/azure-cli)

Poniższe przykłady pokazują, jak skonfigurować okno obsługi przy użyciu interfejsu wiersza polecenia platformy Azure. Możesz zainstalować [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)lub użyć Azure Cloud Shell.

## <a name="set-the-maintenance-window-for-an-existing-database"></a>Ustawianie okna obsługi dla istniejącej bazy danych

Poniższy przykład ustawia okno obsługi w istniejącej bazie danych za pomocą [polecenia az sql db update.](/cli/azure/sql/db#az_sql_db_update) Wartość `--maint-config-id` (lub `-m` ) musi być ustawiona na prawidłową wartość dla regionu bazy danych. Aby uzyskać prawidłowe wartości dla swojego regionu, zobacz [Odnajdywanie dostępnych okien obsługi](#discover-available-maintenance-windows).

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

Poniższy przykład ustawia okno obsługi w istniejącej puli elastycznej przy użyciu [polecenia az sql elastic-pool update.](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) Ważne jest, aby upewnić się, że wartość jest prawidłową wartością dla `maintenanceConfig` regionu puli.  Aby uzyskać prawidłowe wartości dla regionu, zobacz [Odnajdywanie dostępnych okien obsługi](#discover-available-maintenance-windows).

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

## <a name="set-the-maintenance-window-on-an-existing-managed-instance"></a>Ustawianie okna obsługi w istniejącym wystąpieniu zarządzanym

Poniższy przykład ustawia okno obsługi przy użyciu [polecenia az sql mi update](/cli/azure/sql/mi#az_sql_mi_update). Okno obsługi jest ustawione na wystąpieniu, więc wszystkie bazy danych w wystąpieniu mają harmonogram okna obsługi wystąpienia. W `-MaintenanceConfigurationId` przypadku *parametru MaintenanceConfigName* musi być prawidłową wartością regionu wystąpienia. Aby uzyskać prawidłowe wartości dla swojego regionu, zobacz [Odnajdywanie dostępnych okien obsługi](#discover-available-maintenance-windows).

   ```azurecli
   az sql mi update -g mygroup  -n myinstance -m /subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MainteanceConfigName}
   ```

-----

## <a name="cleanup-resources"></a>Oczyszczanie zasobów

Pamiętaj o usunięciu niepotrzebnych zasobów po zakończeniu pracy z nimi, aby uniknąć niepotrzebnych opłat.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Przejdź do bazy danych SQL lub elastycznej puli, która nie jest już potrzebna.
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

- Aby dowiedzieć się więcej na temat okna obsługi, zobacz [Okno obsługi (wersja zapoznawcza).](maintenance-window.md)
- Aby uzyskać więcej informacji, zobacz [Okno obsługi — często zadawane pytania.](maintenance-window-faq.yml)
- Aby dowiedzieć się więcej na temat optymalizacji wydajności, zobacz Monitorowanie i dostrajanie [wydajności w Azure SQL Database i Azure SQL Managed Instance](monitor-tune-overview.md).
