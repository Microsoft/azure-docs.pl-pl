---
title: Konfigurowanie grupy trybu failover
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Dowiedz się, jak skonfigurować grupę autotrybu failover dla Azure SQL Database (pojedyncza i w puli) i wystąpienia zarządzanego SQL przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure i programu PowerShell.
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein
ms.date: 08/14/2019
ms.openlocfilehash: a154f9a75a70ed46155424d676d2b2cd8c6df3bf
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91277975"
---
# <a name="configure-a-failover-group-for-azure-sql-database"></a>Konfigurowanie grupy trybu failover dla Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

W tym temacie przedstawiono sposób konfigurowania [grupy autotrybu failover](auto-failover-group-overview.md) dla Azure SQL Database i wystąpienia zarządzanego Azure SQL.

## <a name="single-database-in-azure-sql-database"></a>Pojedyncza baza danych w Azure SQL Database

Utwórz grupę trybu failover i Dodaj do niej pojedynczą bazę danych przy użyciu Azure Portal lub programu PowerShell.

### <a name="prerequisites"></a>Wymagania wstępne

Należy wziąć pod uwagę następujące wymagania wstępne:

- Ustawienia logowania serwera i zapory dla serwera pomocniczego muszą być zgodne z serwerem podstawowym.

### <a name="create-failover-group"></a>Utwórz grupę trybu failover

# <a name="portal"></a>[Portal](#tab/azure-portal)

Utwórz grupę trybu failover i Dodaj do niej pojedynczą bazę danych przy użyciu Azure Portal.

1. Wybierz pozycję **Azure SQL** w menu po lewej stronie [Azure Portal](https://portal.azure.com). Jeśli na liście nie ma **usługi Azure SQL** , wybierz pozycję **wszystkie usługi**, a następnie wpisz SQL Azure w polu wyszukiwania. Obowiązkowe Wybierz gwiazdkę obok pozycji **Azure SQL** , aby ją dodać do ulubionych, i Dodaj ją jako element w nawigacji po lewej stronie.
1. Wybierz bazę danych, którą chcesz dodać do grupy trybu failover.
1. Wybierz nazwę serwera w polu **Nazwa serwera** , aby otworzyć ustawienia serwera.

   ![Otwórz serwer dla pojedynczej bazy danych](./media/auto-failover-group-configure/open-sql-db-server.png)

1. Wybierz pozycję **grupy trybu failover** w okienku **Ustawienia** , a następnie wybierz pozycję **Dodaj grupę** , aby utworzyć nową grupę trybu failover.

   ![Dodaj nową grupę trybu failover](./media/auto-failover-group-configure/sqldb-add-new-failover-group.png)

1. Na stronie **Grupa trybu failover** wprowadź lub wybierz wymagane wartości, a następnie wybierz pozycję **Utwórz**.

   - **Bazy danych w grupie**: Wybierz bazę danych, którą chcesz dodać do grupy trybu failover. Dodanie bazy danych do grupy trybu failover spowoduje automatyczne uruchomienie procesu replikacji geograficznej.

   ![Dodawanie SQL Database do grupy trybu failover](./media/auto-failover-group-configure/add-sqldb-to-failover-group.png)

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Utwórz grupę trybu failover i Dodaj do niej swoją bazę danych przy użyciu programu PowerShell.

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Create a secondary server in the failover region
   Write-host "Creating a secondary server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer

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

---

### <a name="test-failover"></a>Testowanie pracy w trybie failover

Testowanie pracy w trybie failover grupy trybu failover przy użyciu Azure Portal lub programu PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Testowanie pracy w trybie failover grupy trybu failover przy użyciu Azure Portal.

1. Wybierz pozycję **Azure SQL** w menu po lewej stronie [Azure Portal](https://portal.azure.com). Jeśli na liście nie ma **usługi Azure SQL** , wybierz pozycję **wszystkie usługi**, a następnie wpisz ciąg "Azure SQL" w polu wyszukiwania. Obowiązkowe Wybierz gwiazdkę obok pozycji **Azure SQL** , aby ją dodać do ulubionych, i Dodaj ją jako element w nawigacji po lewej stronie.
1. Wybierz bazę danych, którą chcesz dodać do grupy trybu failover.

   ![Otwórz serwer dla pojedynczej bazy danych](./media/auto-failover-group-configure/open-sql-db-server.png)

1. Wybierz pozycję **grupy trybu failover** w okienku **Ustawienia** , a następnie wybierz utworzoną grupę trybu failover.
  
   ![Wybieranie grupy trybu failover z portalu](./media/auto-failover-group-configure/select-failover-group.png)

1. Sprawdź, który serwer jest podstawowy i który serwer jest serwerem pomocniczym.
1. Wybierz pozycję **tryb failover** z okienka zadań, aby przełączyć grupę trybu failover w tryb pracy awaryjnej zawierającej bazę danych.
1. Na ostrzeżeniu wybierz pozycję **tak** , aby powiadomić, że sesje TDS zostaną rozłączone.

   ![Praca awaryjna grupy trybu failover zawierającej bazę danych](./media/auto-failover-group-configure/failover-sql-db.png)

1. Sprawdź, który serwer jest teraz podstawowy i który serwer jest serwerem pomocniczym. Jeśli praca awaryjna zakończyła się pomyślnie, te dwa serwery powinny mieć zamienione role.
1. Wybierz ponownie **tryb failover** , aby powrócić do błędów serwerów do ich oryginalnych ról.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Testowanie pracy w trybie failover grupy trybu failover przy użyciu programu PowerShell.  

Sprawdź rolę repliki pomocniczej:

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

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
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName
   ```

Przywróć grupę trybu failover z powrotem do serwera podstawowego:

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully to back to" $serverName
   ```

---

> [!IMPORTANT]
> Jeśli musisz usunąć pomocniczą bazę danych, usuń ją z grupy trybu failover przed jej usunięciem. Usunięcie pomocniczej bazy danych przed jej usunięciem z grupy trybu failover może spowodować nieprzewidywalne zachowanie.

## <a name="elastic-pools-in-azure-sql-database"></a>Pule elastyczne w Azure SQL Database

Utwórz grupę trybu failover i Dodaj do niej pulę elastyczną przy użyciu Azure Portal lub programu PowerShell.  

### <a name="prerequisites"></a>Wymagania wstępne

Należy wziąć pod uwagę następujące wymagania wstępne:

- Ustawienia logowania serwera i zapory dla serwera pomocniczego muszą być zgodne z serwerem podstawowym.

### <a name="create-the-failover-group"></a>Tworzenie grupy trybu failover

Utwórz grupę trybu failover dla puli elastycznej przy użyciu Azure Portal lub programu PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Utwórz grupę trybu failover i Dodaj do niej pulę elastyczną przy użyciu Azure Portal.

1. Wybierz pozycję **Azure SQL** w menu po lewej stronie [Azure Portal](https://portal.azure.com). Jeśli na liście nie ma **usługi Azure SQL** , wybierz pozycję **wszystkie usługi**, a następnie wpisz ciąg "Azure SQL" w polu wyszukiwania. Obowiązkowe Wybierz gwiazdkę obok pozycji **Azure SQL** , aby ją dodać do ulubionych, i Dodaj ją jako element w nawigacji po lewej stronie.
1. Wybierz pulę elastyczną, którą chcesz dodać do grupy trybu failover.
1. W okienku **Przegląd** wybierz nazwę serwera w polu **Nazwa serwera** , aby otworzyć ustawienia serwera.
  
   ![Otwórz serwer dla puli elastycznej](./media/auto-failover-group-configure/server-for-elastic-pool.png)

1. Wybierz pozycję **grupy trybu failover** w okienku **Ustawienia** , a następnie wybierz pozycję **Dodaj grupę** , aby utworzyć nową grupę trybu failover.

   ![Dodaj nową grupę trybu failover](./media/auto-failover-group-configure/sqldb-add-new-failover-group.png)

1. Na stronie **Grupa trybu failover** wprowadź lub wybierz wymagane wartości, a następnie wybierz pozycję **Utwórz**. Utwórz nowy serwer pomocniczy lub wybierz istniejący serwer pomocniczy.

1. Wybierz **bazy danych w grupie,** a następnie wybierz pulę elastyczną, którą chcesz dodać do grupy trybu failover. Jeśli pula elastyczna nie istnieje jeszcze na serwerze pomocniczym, zostanie wyświetlone ostrzeżenie z monitem o utworzenie puli elastycznej na serwerze pomocniczym. Wybierz ostrzeżenie, a następnie wybierz przycisk **OK** , aby utworzyć pulę elastyczną na serwerze pomocniczym.

   ![Dodawanie puli elastycznej do grupy trybu failover](./media/auto-failover-group-configure/add-elastic-pool-to-failover-group.png)

1. Wybierz pozycję **Wybierz** , aby zastosować ustawienia puli elastycznej do grupy trybu failover, a następnie wybierz pozycję **Utwórz** , aby utworzyć grupę trybu failover. Dodanie puli elastycznej do grupy trybu failover spowoduje automatyczne uruchomienie procesu replikacji geograficznej.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Utwórz grupę trybu failover i Dodaj do niej pulę elastyczną przy użyciu programu PowerShell.

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $poolName = "myElasticPool"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

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
   Write-host "Databases added to failover group successfully."
  ```

---

### <a name="test-failover"></a>Testowanie pracy w trybie failover

Testowanie pracy w trybie failover puli elastycznej przy użyciu Azure Portal lub programu PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Niepowodzenie grupy trybu failover na serwerze pomocniczym, a następnie powrót po awarii przy użyciu Azure Portal.

1. Wybierz pozycję **Azure SQL** w menu po lewej stronie [Azure Portal](https://portal.azure.com). Jeśli na liście nie ma **usługi Azure SQL** , wybierz pozycję **wszystkie usługi**, a następnie wpisz ciąg "Azure SQL" w polu wyszukiwania. Obowiązkowe Wybierz gwiazdkę obok pozycji **Azure SQL** , aby ją dodać do ulubionych, i Dodaj ją jako element w nawigacji po lewej stronie.
1. Wybierz pulę elastyczną, którą chcesz dodać do grupy trybu failover.
1. W okienku **Przegląd** wybierz nazwę serwera w polu **Nazwa serwera** , aby otworzyć ustawienia serwera.

   ![Otwórz serwer dla puli elastycznej](./media/auto-failover-group-configure/server-for-elastic-pool.png)
1. Wybierz pozycję **grupy trybu failover** w okienku **Ustawienia** , a następnie wybierz grupę trybu failover utworzoną w sekcji 2.
  
   ![Wybieranie grupy trybu failover z portalu](./media/auto-failover-group-configure/select-failover-group.png)

1. Sprawdź, który serwer jest podstawowy i który serwer jest serwerem pomocniczym.
1. Wybierz pozycję **tryb failover** z okienka zadań, aby przełączyć grupę trybu failover w tryb pracy awaryjnej zawierającej pulę elastyczną.
1. Na ostrzeżeniu wybierz pozycję **tak** , aby powiadomić, że sesje TDS zostaną rozłączone.

   ![Praca awaryjna grupy trybu failover zawierającej bazę danych](./media/auto-failover-group-configure/failover-sql-db.png)

1. Sprawdź, który serwer jest serwerem podstawowym, który serwer jest serwerem pomocniczym. Jeśli praca awaryjna zakończyła się pomyślnie, te dwa serwery powinny mieć zamienione role.
1. Wybierz ponownie **tryb failover** , aby zakończyć pracę grupy trybu failover z powrotem do oryginalnych ustawień.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Testowanie pracy w trybie failover grupy trybu failover przy użyciu programu PowerShell.

Sprawdź rolę repliki pomocniczej:

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

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
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName
   ```

---

> [!IMPORTANT]
> Jeśli musisz usunąć pomocniczą bazę danych, usuń ją z grupy trybu failover przed jej usunięciem. Usunięcie pomocniczej bazy danych przed jej usunięciem z grupy trybu failover może spowodować nieprzewidywalne zachowanie.

## <a name="sql-managed-instance"></a>Wystąpienie zarządzane SQL

Utwórz grupę trybu failover między dwoma wystąpieniami zarządzanymi w wystąpieniu zarządzanym usługi Azure SQL przy użyciu Azure Portal lub programu PowerShell.

Należy skonfigurować [ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) lub utworzyć bramę dla sieci wirtualnej dla każdego wystąpienia zarządzanego SQL, połączyć dwie bramy, a następnie utworzyć grupę trybu failover.

### <a name="prerequisites"></a>Wymagania wstępne

Należy wziąć pod uwagę następujące wymagania wstępne:

- Pomocnicze wystąpienie zarządzane musi być puste.
- Zakres podsieci dla pomocniczej sieci wirtualnej nie może nakładać się na zakres podsieci podstawowej sieci wirtualnej.
- Sortowanie i strefa czasowa pomocniczego wystąpienia zarządzanego muszą być zgodne z podstawowym wystąpieniem zarządzanym.
- Podczas łączenia dwóch bram, **klucz współużytkowany** powinien być taki sam dla obu połączeń.

### <a name="create-primary-virtual-network-gateway"></a>Utwórz bramę podstawowej sieci wirtualnej

Jeśli nie skonfigurowano [ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md), można utworzyć podstawową bramę sieci wirtualnej za pomocą Azure Portal lub PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Utwórz bramę podstawowej sieci wirtualnej przy użyciu Azure Portal.

1. W [Azure Portal](https://portal.azure.com)przejdź do grupy zasobów, a następnie wybierz zasób **sieci wirtualnej** dla podstawowego wystąpienia zarządzanego.
1. W obszarze **Ustawienia** wybierz pozycję **podsieci** , a następnie wybierz opcję, aby dodać nową **podsieć bramy**. Pozostaw wartości domyślne.

   ![Dodawanie bramy dla podstawowego wystąpienia zarządzanego](./media/auto-failover-group-configure/add-subnet-gateway-primary-vnet.png)

1. Po utworzeniu bramy podsieci wybierz pozycję **Utwórz zasób** w lewym okienku nawigacji, a następnie wpisz `Virtual network gateway` w polu wyszukiwania. Wybierz zasób **bramy sieci wirtualnej** Opublikowany przez **firmę Microsoft**.

   ![Utwórz nową bramę sieci wirtualnej](./media/auto-failover-group-configure/create-virtual-network-gateway.png)

1. Wypełnij pola wymagane, aby skonfigurować bramę jako podstawowe wystąpienie zarządzane.

   W poniższej tabeli przedstawiono wartości niezbędne dla bramy dla podstawowego wystąpienia zarządzanego:

    | **Pole** | Wartość |
    | --- | --- |
    | **Subskrypcja** |  Subskrypcja, w której znajduje się główne wystąpienie zarządzane. |
    | **Nazwa** | Nazwa bramy sieci wirtualnej. |
    | **Region** | Region, w którym znajduje się główne wystąpienie zarządzane. |
    | **Typ bramy** | wybierz pozycję **VPN**. |
    | **Typ sieci VPN** | Wybierz pozycję **oparta na trasach** |
    | **SKU**| Pozostaw wartość domyślną `VpnGw1` . |
    | **Lokalizacja**| Lokalizacja, w której znajduje się pomocnicze wystąpienie zarządzane i pomocnicza Sieć wirtualna.   |
    | **Sieć wirtualna**| Wybierz sieć wirtualną dla pomocniczego wystąpienia zarządzanego. |
    | **Publiczny adres IP**| Wybierz pozycję**Utwórz nowy**. |
    | **Nazwa publicznego adresu IP**| Wprowadź nazwę dla swojego adresu IP. |
    | &nbsp; | &nbsp; |

1. Pozostaw pozostałe wartości jako domyślne, a następnie wybierz pozycję **Przegląd + Utwórz** , aby przejrzeć ustawienia bramy sieci wirtualnej.

   ![Ustawienia bramy podstawowej](./media/auto-failover-group-configure/settings-for-primary-gateway.png)

1. Wybierz pozycję **Utwórz** , aby utworzyć nową bramę sieci wirtualnej.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Utwórz bramę podstawowej sieci wirtualnej przy użyciu programu PowerShell.

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryVnetName = "<Primary-Virtual-Network-Name>"
   $primaryGWName = "<Primary-Gateway-Name>"
   $primaryGWPublicIPAddress = $primaryGWName + "-ip"
   $primaryGWIPConfig = $primaryGWName + "-ipc"
   $primaryGWAsn = 61000

   # Get the primary virtual network
   $vnet1 = Get-AzVirtualNetwork -Name $primaryVnetName -ResourceGroupName $primaryResourceGroupName
   $primaryLocation = $vnet1.Location

   # Create primary gateway
   Write-host "Creating primary gateway..."
   $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet1
   $gwpip1= New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $primaryResourceGroupName `
            -Location $primaryLocation -AllocationMethod Dynamic
   $gwipconfig1 = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
            -SubnetId $subnet1.Id -PublicIpAddressId $gwpip1.Id

   $gw1 = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -IpConfigurations $gwipconfig1 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
   $gw1
   ```

---

### <a name="create-secondary-virtual-network-gateway"></a>Utwórz bramę dodatkowej sieci wirtualnej

Utwórz bramę dodatkowej sieci wirtualnej przy użyciu Azure Portal lub programu PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Powtórz kroki opisane w poprzedniej sekcji, aby utworzyć podsieć i bramę sieci wirtualnej dla pomocniczego wystąpienia zarządzanego. Wypełnij pola wymagane, aby skonfigurować bramę dla pomocniczego wystąpienia zarządzanego.

W poniższej tabeli przedstawiono wartości niezbędne dla bramy dla pomocniczego wystąpienia zarządzanego:

   | **Pole** | Wartość |
   | --- | --- |
   | **Subskrypcja** |  Subskrypcja, w której znajduje się pomocnicze wystąpienie zarządzane. |
   | **Nazwa** | Nazwa bramy sieci wirtualnej, na przykład `secondary-mi-gateway` . |
   | **Region** | Region, w którym znajduje się pomocnicze wystąpienie zarządzane. |
   | **Typ bramy** | wybierz pozycję **VPN**. |
   | **Typ sieci VPN** | Wybierz pozycję **oparta na trasach** |
   | **SKU**| Pozostaw wartość domyślną `VpnGw1` . |
   | **Lokalizacja**| Lokalizacja, w której znajduje się pomocnicze wystąpienie zarządzane i pomocnicza Sieć wirtualna.   |
   | **Sieć wirtualna**| Wybierz sieć wirtualną, która została utworzona w sekcji 2, na przykład `vnet-sql-mi-secondary` . |
   | **Publiczny adres IP**| Wybierz pozycję**Utwórz nowy**. |
   | **Nazwa publicznego adresu IP**| Wprowadź nazwę adresu IP, na przykład `secondary-gateway-IP` . |
   | &nbsp; | &nbsp; |

   ![Ustawienia bramy dodatkowej](./media/auto-failover-group-configure/settings-for-secondary-gateway.png)

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Utwórz bramę dodatkowej sieci wirtualnej przy użyciu programu PowerShell.

   ```powershell-interactive
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryVnetName = "<Secondary-Virtual-Network-Name>"
   $secondaryGWName = "<Secondary-Gateway-Name>"
   $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
   $secondaryGWIPConfig = $secondaryGWName + "-ipc"
   $secondaryGWAsn = 62000

   # Get the secondary virtual network
   $vnet2 = Get-AzVirtualNetwork -Name $secondaryVnetName -ResourceGroupName $secondaryResourceGroupName
   $secondaryLocation = $vnet2.Location

   # Create the secondary gateway
   Write-host "Creating secondary gateway..."
   $subnet2 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet2
   $gwpip2= New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $secondaryResourceGroupName `
            -Location $secondaryLocation -AllocationMethod Dynamic
   $gwipconfig2 = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
            -SubnetId $subnet2.Id -PublicIpAddressId $gwpip2.Id

   $gw2 = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -IpConfigurations $gwipconfig2 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn

   $gw2
   ```

---

### <a name="connect-the-gateways"></a>Połącz bramy

Utwórz połączenia między dwiema bramami przy użyciu Azure Portal lub programu PowerShell.

Należy utworzyć dwa połączenia — połączenie z bramy podstawowej do bramy pomocniczej, a następnie połączenie z bramy dodatkowej do bramy podstawowej.

Klucz współużytkowany używany do obu połączeń powinien być taki sam dla każdego połączenia.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Utwórz połączenia między dwiema bramami przy użyciu Azure Portal.

1. Wybierz pozycję **Utwórz zasób** na podstawie [Azure Portal](https://portal.azure.com).
1. Wpisz `connection` w polu wyszukiwania, a następnie naciśnij klawisz ENTER, aby przeszukać, co spowoduje przejście do zasobu **połączenia** opublikowanego przez firmę Microsoft.
1. Wybierz pozycję **Utwórz** , aby utworzyć połączenie.
1. Na karcie **podstawowe** wybierz poniższe wartości, a następnie wybierz przycisk **OK**.
    1. Wybierz pozycję `VNet-to-VNet` dla **typu połączenia**.
    1. Wybierz subskrypcję z listy rozwijanej.
    1. Z listy rozwijanej wybierz grupę zasobów dla wystąpienia zarządzanego.
    1. Z listy rozwijanej wybierz lokalizację podstawowego wystąpienia zarządzanego.
1. Na karcie **Ustawienia** wybierz lub wprowadź następujące wartości, a następnie wybierz przycisk **OK**:
    1. Wybierz bramę sieci podstawowej dla **pierwszej bramy sieci wirtualnej**, na przykład `Primary-Gateway` .  
    1. Wybierz bramę sieci pomocniczej dla **drugiej bramy sieci wirtualnej**, na przykład `Secondary-Gateway` .
    1. Zaznacz pole wyboru obok pozycji **Ustanów łączność dwukierunkową**.
    1. Pozostaw domyślną nazwę połączenia podstawowego lub Zmień nazwę na wybraną wartość.
    1. Podaj **klucz współużytkowany (PSK)** dla połączenia, na przykład `mi1m2psk` .

   ![Utwórz połączenie bramy](./media/auto-failover-group-configure/create-gateway-connection.png)

1. Na karcie **Podsumowanie** Sprawdź ustawienia połączenia dwukierunkowego, a następnie wybierz przycisk **OK** , aby utworzyć połączenie.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Utwórz połączenia między dwiema bramami przy użyciu programu PowerShell.

   ```powershell-interactive
   $vpnSharedKey = "mi1mi2psk"
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryGWConnection = "<Primary-connection-name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryGWConnection = "<Secondary-connection-name>"
   $secondaryLocation = "<Secondary-Region>"
  
   # Connect the primary to secondary gateway
   Write-host "Connecting the primary gateway"
   New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $primaryResourceGroupName `
       -VirtualNetworkGateway1 $gw1 -VirtualNetworkGateway2 $gw2 -Location $primaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $primaryGWConnection

   # Connect the secondary to primary gateway
   Write-host "Connecting the secondary gateway"

   New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $secondaryResourceGroupName `
       -VirtualNetworkGateway1 $gw2 -VirtualNetworkGateway2 $gw1 -Location $secondaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $secondaryGWConnection
   ```

---

### <a name="create-the-failover-group"></a>Tworzenie grupy trybu failover

Utwórz grupę trybu failover dla wystąpień zarządzanych przy użyciu Azure Portal lub programu PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Utwórz grupę trybu failover dla wystąpień zarządzanych SQL przy użyciu Azure Portal.

1. Wybierz pozycję **Azure SQL** w menu po lewej stronie [Azure Portal](https://portal.azure.com). Jeśli na liście nie ma **usługi Azure SQL** , wybierz pozycję **wszystkie usługi**, a następnie wpisz SQL Azure w polu wyszukiwania. Obowiązkowe Wybierz gwiazdkę obok pozycji **Azure SQL** , aby ją dodać do ulubionych, i Dodaj ją jako element w nawigacji po lewej stronie.
1. Wybierz główne wystąpienie zarządzane, które chcesz dodać do grupy trybu failover.  
1. W obszarze **Ustawienia**przejdź do pozycji **grupy trybu failover wystąpienia** , a następnie wybierz pozycję **Dodaj grupę** , aby otworzyć stronę **Grupa trybu failover wystąpienia** .

   ![Dodawanie grupy trybu failover](./media/auto-failover-group-configure/add-failover-group.png)

1. Na stronie **Grupa trybu failover wystąpienia** wpisz nazwę grupy trybu failover, a następnie wybierz z listy rozwijanej pomocnicze wystąpienie zarządzane. Wybierz pozycję **Utwórz** , aby utworzyć grupę trybu failover.

   ![Utwórz grupę trybu failover](./media/auto-failover-group-configure/create-failover-group.png)

1. Po zakończeniu wdrażania grupy trybu failover nastąpi powrót do strony **grupy trybu failover** .

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Utwórz grupę trybu failover dla wystąpień zarządzanych przy użyciu programu PowerShell.

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"

   # Create failover group
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $primaryLocation -ResourceGroupName $primaryResourceGroupName -PrimaryManagedInstanceName $primaryManagedInstance `
        -PartnerRegion $secondaryLocation -PartnerManagedInstanceName $secondaryManagedInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```

---

### <a name="test-failover"></a>Testowanie pracy w trybie failover

Testowanie pracy w trybie failover grupy trybu failover przy użyciu Azure Portal lub programu PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Testowanie pracy w trybie failover grupy trybu failover przy użyciu Azure Portal.

1. Przejdź do _pomocniczego_ wystąpienia zarządzanego w [Azure Portal](https://portal.azure.com) a następnie wybierz pozycję **grupy trybu failover dla wystąpienia** w obszarze Ustawienia.
1. Sprawdź, które wystąpienie zarządzane jest podstawowym, a którym wystąpieniem zarządzanym jest pomocniczy.
1. Wybierz pozycję **tryb failover** , a następnie wybierz pozycję **tak** na ostrzeżenie o rozłączeniu sesji tds.

   ![Praca awaryjna grupy trybu failover](./media/auto-failover-group-configure/failover-mi-failover-group.png)

1. Sprawdź, które zarządzane wystąpienie jest podstawowym i jakie wystąpienie jest serwerem pomocniczym. Jeśli przechodzenie do trybu failover zakończyło się pomyślnie, dwa wystąpienia powinny mieć przypisane role.

   ![Wystąpienia zarządzane mają przełączane role po zakończeniu pracy w trybie failover](./media/auto-failover-group-configure/mi-switched-after-failover.png)

1. Przejdź do nowego _pomocniczego_ wystąpienia zarządzanego i ponownie wybierz **tryb failover** w celu niepowodzenia wystąpienia podstawowego z powrotem do roli podstawowej.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Testowanie pracy w trybie failover grupy trybu failover przy użyciu programu PowerShell.

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"

   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName

   # Failover the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"

   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName

   # Fail primary managed instance back to primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"

   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   ```

---

## <a name="use-private-link"></a>Używanie linku prywatnego

Użycie linku prywatnego umożliwia skojarzenie serwera logicznego z określonym prywatnym adresem IP w sieci wirtualnej i podsieci. 

Aby użyć prywatnego linku z grupą trybu failover, wykonaj następujące czynności:

1. Upewnij się, że serwery główne i pomocnicze znajdują się w [sparowanym regionie](/azure/best-practices-availability-paired-regions). 
1. Utwórz sieć wirtualną i podsieć w każdym regionie, aby hostować prywatne punkty końcowe dla serwerów podstawowych i pomocniczych, takich jak nienakładające się przestrzenie adresów IP. Na przykład główny zakres adresów sieci wirtualnej 10.0.0.0/16 i zakres adresów pomocniczej sieci wirtualnej wynoszący 10.0.0.1/16 nakładają się na siebie. Więcej informacji o zakresach adresów sieci wirtualnej znajduje się w blogu [projektowanie sieci wirtualnych platformy Azure](https://devblogs.microsoft.com/premier-developer/understanding-cidr-notation-when-designing-azure-virtual-networks-and-subnets/).
1. Utwórz [prywatny punkt końcowy i strefę prywatna strefa DNS platformy Azure dla serwera podstawowego](../../private-link/create-private-endpoint-portal.md#create-a-private-endpoint). 
1. Utwórz również prywatny punkt końcowy serwera pomocniczego, ale tym razem wybierz opcję ponownego użycia tej samej strefy Prywatna strefa DNS, która została utworzona dla serwera podstawowego. 
1. Po nawiązaniu połączenia prywatnego można utworzyć grupę trybu failover, wykonując czynności opisane wcześniej w tym artykule. 


## <a name="locate-listener-endpoint"></a>Lokalizowanie punktu końcowego odbiornika

Po skonfigurowaniu grupy trybu failover zaktualizuj parametry połączenia dla aplikacji do punktu końcowego odbiornika. Dzięki temu aplikacja będzie połączona z odbiornikiem grupy trybu failover, a nie podstawową bazą danych, elastyczną pulą lub bazą danych wystąpienia. Dzięki temu nie trzeba ręcznie aktualizować parametrów połączenia za każdym razem, gdy jednostka bazy danych zostanie przełączona w tryb failover, a ruch jest kierowany do jednostki, która jest obecnie podstawowa.

Punkt końcowy odbiornika ma postać `fog-name.database.windows.net` i jest widoczny w Azure Portal podczas wyświetlania grupy trybu failover:

![Parametry połączenia grupy trybu failover](./media/auto-failover-group-configure/find-failover-group-connection-string.png)

## <a name="remarks"></a>Uwagi

- Usunięcie grupy trybu failover dla pojedynczej bazy danych lub puli nie powoduje zatrzymania replikacji i nie powoduje usunięcia zreplikowanej bazy danych. Należy ręcznie zatrzymać replikację geograficzną i usunąć bazę danych z serwera pomocniczego, jeśli chcesz dodać bazę danych z jednej lub puli z powrotem do grupy trybu failover po jej usunięciu. W przeciwnym razie może wystąpić błąd podobny do `The operation cannot be performed due to multiple errors` tego podczas próby dodania bazy danych do grupy trybu failover.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać szczegółowe instrukcje dotyczące konfigurowania grupy trybu failover, zobacz następujące samouczki:

- [Dodawanie pojedynczej bazy danych do grupy trybu failover](failover-group-add-single-database-tutorial.md)
- [Dodawanie elastycznej puli do grupy trybu failover](failover-group-add-elastic-pool-tutorial.md)
- [Dodawanie wystąpienia zarządzanego do grupy trybu failover](../managed-instance/failover-group-add-instance-tutorial.md)

Omówienie opcji wysokiej dostępności Azure SQL Database można znaleźć w temacie [replikacja geograficzna](active-geo-replication-overview.md) i [Grupa autopraca awaryjna](auto-failover-group-overview.md).
