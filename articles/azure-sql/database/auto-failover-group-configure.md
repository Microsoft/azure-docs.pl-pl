---
title: Konfigurowanie grupy trybu failover
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Dowiedz się, jak skonfigurować grupę automatycznego trybu failover dla usługi Azure SQL Database (zarówno pojedynczej, jak i w puli) i SQL Managed Instance przy użyciu interfejsu Azure Portal, interfejsu wiersza polecenia platformy Azure i programu PowerShell.
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/14/2019
ms.openlocfilehash: a2f0cb683669aa092493c8080d5e4646cf9706c3
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107477939"
---
# <a name="configure-a-failover-group-for-azure-sql-database"></a>Konfigurowanie grupy trybu failover dla Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

W tym temacie opisano sposób konfigurowania grupy [automatycznego trybu failover](auto-failover-group-overview.md) dla Azure SQL Database i Azure SQL Managed Instance.

## <a name="single-database"></a>Pojedyncza baza danych

Utwórz grupę trybu failover i dodaj do tej grupy pojedynczą bazę danych przy użyciu Azure Portal programu PowerShell.

### <a name="prerequisites"></a>Wymagania wstępne

Należy wziąć pod uwagę następujące wymagania wstępne:

- Ustawienia logowania i zapory serwera pomocniczego muszą być zgodne z ustawieniami serwera podstawowego.

### <a name="create-failover-group"></a>Tworzenie grupy trybu failover

# <a name="portal"></a>[Portal](#tab/azure-portal)

Utwórz grupę trybu failover i dodaj do niego pojedynczą bazę danych przy użyciu Azure Portal.

1. Wybierz **Azure SQL** w menu po lewej stronie Azure Portal [.](https://portal.azure.com) Jeśli **Azure SQL** nie ma na liście, wybierz **pozycję** Wszystkie usługi , a następnie wpisz Azure SQL w polu wyszukiwania. (Opcjonalnie) Wybierz gwiazdkę obok pozycji **Azure SQL,** aby dodać ją do ulubionych, i dodaj ją jako element w nawigacji po lewej stronie.
1. Wybierz bazę danych, którą chcesz dodać do grupy trybu failover.
1. Wybierz nazwę serwera w obszarze **Nazwa serwera,** aby otworzyć ustawienia serwera.

   ![Otwieranie serwera dla pojedynczej bazy danych](./media/auto-failover-group-configure/open-sql-db-server.png)

1. Wybierz **pozycję Grupy trybu failover** w **okienku** Ustawienia, a następnie wybierz **pozycję Dodaj** grupę, aby utworzyć nową grupę trybu failover.

   ![Dodawanie nowej grupy trybu failover](./media/auto-failover-group-configure/sqldb-add-new-failover-group.png)

1. Na stronie **Grupa trybu failover** wprowadź lub wybierz wymagane wartości, a następnie wybierz pozycję **Utwórz.**

   - **Bazy danych w grupie:** wybierz bazę danych, którą chcesz dodać do grupy trybu failover. Dodanie bazy danych do grupy trybu failover spowoduje automatyczne rozpoczęcie procesu replikacji geograficznej.

   ![Dodawanie SQL Database do grupy trybu failover](./media/auto-failover-group-configure/add-sqldb-to-failover-group.png)

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Utwórz grupę trybu failover i dodaj do niego bazę danych przy użyciu programu PowerShell.

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
      â€“ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      â€“FailoverGroupName $failoverGroupName `
      â€“FailoverPolicy Automatic `
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

Przetestuj trybu failover grupy trybu failover przy użyciu Azure Portal lub programu PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Przetestuj trybu failover grupy trybu failover przy użyciu Azure Portal.

1. Wybierz **Azure SQL** w menu po lewej stronie [Azure Portal](https://portal.azure.com). Jeśli **Azure SQL** nie ma na liście, wybierz **pozycję** Wszystkie usługi, a następnie wpisz "Azure SQL" w polu wyszukiwania. (Opcjonalnie) Wybierz gwiazdkę obok pozycji **Azure SQL,** aby dodać ją do ulubionych, i dodaj ją jako element w nawigacji po lewej stronie.
1. Wybierz bazę danych, którą chcesz dodać do grupy trybu failover.

   ![Otwieranie serwera dla pojedynczej bazy danych](./media/auto-failover-group-configure/open-sql-db-server.png)

1. Wybierz **pozycję Grupy trybu failover** w **okienku** Ustawienia, a następnie wybierz właśnie utworzoną grupę trybu failover.
  
   ![Wybieranie grupy trybu failover z portalu](./media/auto-failover-group-configure/select-failover-group.png)

1. Sprawdź, który serwer jest serwerem podstawowym, a który pomocniczym.
1. Wybierz **pozycję Failover (Trybu failover)** w okienku zadań, aby przesłonić do trybu failover grupę trybu failover zawierającą bazę danych.
1. Wybierz **pozycję Tak** w ostrzeżeniu z powiadomieniem, że sesje TDS zostaną rozłączone.

   ![Failover your failover group containing your database (Przejmij w failover grupę trybu failover zawierającą bazę danych)](./media/auto-failover-group-configure/failover-sql-db.png)

1. Sprawdź, który serwer jest teraz podstawowy, a który pomocniczy. W przypadku powodzenia trybu failover oba serwery powinny mieć zamienione role.
1. Wybierz ponownie pozycję Failover (Trybu **failover),** aby ponownie wrócić do oryginalnych ról serwerów.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Przetestuj trybu failover grupy trybu failover przy użyciu programu PowerShell.  

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

W przypadku pracy awaryjnej na serwerze pomocniczym:

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

Przywróć grupę trybu failover z powrotem na serwer podstawowy:

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
> Jeśli musisz usunąć pomocniczą bazę danych, usuń ją z grupy trybu failover przed jej usunięciem. Usunięcie pomocniczej bazy danych przed usunięciem jej z grupy trybu failover może spowodować nieprzewidywalne zachowanie.

## <a name="elastic-pool"></a>Pula elastyczna

Utwórz grupę trybu failover i dodaj do tej grupy elastyczną pulę przy użyciu Azure Portal lub programu PowerShell.  

### <a name="prerequisites"></a>Wymagania wstępne

Należy wziąć pod uwagę następujące wymagania wstępne:

- Ustawienia logowania i zapory serwera pomocniczego muszą być zgodne z ustawieniami serwera podstawowego.

### <a name="create-the-failover-group"></a>Tworzenie grupy trybu failover

Utwórz grupę trybu failover dla elastycznej puli przy użyciu Azure Portal lub programu PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Utwórz grupę trybu failover i dodaj do niego pulę elastyczną przy użyciu Azure Portal.

1. Wybierz **Azure SQL** w menu po lewej stronie Azure Portal [.](https://portal.azure.com) Jeśli **Azure SQL** nie ma na liście, wybierz **pozycję** Wszystkie usługi, a następnie wpisz "Azure SQL" w polu wyszukiwania. (Opcjonalnie) Wybierz gwiazdkę obok pozycji **Azure SQL,** aby dodać ją do ulubionych, i dodaj ją jako element w nawigacji po lewej stronie.
1. Wybierz pulę elastyczną, którą chcesz dodać do grupy trybu failover.
1. W **okienku** Przegląd wybierz nazwę serwera w obszarze **Nazwa serwera,** aby otworzyć ustawienia serwera.
  
   ![Otwieranie serwera dla puli elastycznej](./media/auto-failover-group-configure/server-for-elastic-pool.png)

1. Wybierz **pozycję Grupy trybu failover** w **okienku** Ustawienia, a następnie wybierz **pozycję Dodaj** grupę, aby utworzyć nową grupę trybu failover.

   ![Dodawanie nowej grupy trybu failover](./media/auto-failover-group-configure/sqldb-add-new-failover-group.png)

1. Na stronie **Grupa trybu failover** wprowadź lub wybierz wymagane wartości, a następnie wybierz pozycję **Utwórz.** Utwórz nowy serwer pomocniczy lub wybierz istniejący serwer pomocniczy.

1. Wybierz **pozycję Bazy danych w grupie,** a następnie wybierz pulę elastyczną, którą chcesz dodać do grupy trybu failover. Jeśli elastyczna pula jeszcze nie istnieje na serwerze pomocniczym, zostanie wyświetlone ostrzeżenie z monitem o utworzenie elastycznej puli na serwerze pomocniczym. Wybierz ostrzeżenie, a następnie wybierz przycisk **OK,** aby utworzyć pulę elastyczną na serwerze pomocniczym.

   ![Dodawanie elastycznej puli do grupy trybu failover](./media/auto-failover-group-configure/add-elastic-pool-to-failover-group.png)

1. Wybierz **pozycję** Wybierz, aby zastosować ustawienia puli elastycznej do grupy trybu failover, a następnie wybierz pozycję **Utwórz,** aby utworzyć grupę trybu failover. Dodanie elastycznej puli do grupy trybu failover spowoduje automatyczne rozpoczęcie procesu replikacji geograficznej.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Utwórz grupę trybu failover i dodaj do niego elastyczną pulę przy użyciu programu PowerShell.

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
       â€“ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -PartnerServerName $drServerName  `
       â€“FailoverGroupName $failoverGroupName `
       â€“FailoverPolicy Automatic `
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

Przetestuj trybu failover elastycznej puli przy użyciu Azure Portal lub programu PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Przejmij grupę trybu failover na serwer pomocniczy, a następnie wróć po awarii przy użyciu Azure Portal.

1. Wybierz **Azure SQL** w menu po lewej stronie [Azure Portal](https://portal.azure.com). Jeśli **Azure SQL** nie ma na liście, wybierz **pozycję** Wszystkie usługi, a następnie wpisz "Azure SQL" w polu wyszukiwania. (Opcjonalnie) Wybierz gwiazdkę obok pozycji **Azure SQL,** aby dodać ją do ulubionych, i dodaj ją jako element w nawigacji po lewej stronie.
1. Wybierz pulę elastyczną, którą chcesz dodać do grupy trybu failover.
1. W **okienku** Przegląd wybierz nazwę serwera w obszarze **Nazwa serwera,** aby otworzyć ustawienia serwera.

   ![Otwieranie serwera dla puli elastycznej](./media/auto-failover-group-configure/server-for-elastic-pool.png)
1. Wybierz **pozycję Grupy trybu failover** w **okienku Ustawienia,** a następnie wybierz grupę trybu failover utworzoną w sekcji 2.
  
   ![Wybieranie grupy trybu failover z portalu](./media/auto-failover-group-configure/select-failover-group.png)

1. Sprawdź, który serwer jest serwerem podstawowym, a który pomocniczym.
1. Wybierz **pozycję Failover (Trybu failover)** w okienku zadań, aby przesłonić w failover grupę trybu failover zawierającą elastyczną pulę.
1. Wybierz **pozycję Tak** w ostrzeżeniu z powiadomieniem, że sesje TDS zostaną rozłączone.

   ![Failover your failover group containing your database (Przeniesienie grupy trybu failover zawierającej bazę danych do trybu failover)](./media/auto-failover-group-configure/failover-sql-db.png)

1. Sprawdź, który serwer jest serwerem podstawowym, a który pomocniczym. W przypadku powodzenia trybu failover te dwa serwery powinny mieć zamienione role.
1. Wybierz **ponownie pozycję Failover (Trybu failover),** aby wrócić do oryginalnych ustawień grupy trybu failover.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Przetestuj trybu failover grupy trybu failover przy użyciu programu PowerShell.

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

W przypadku pracy awaryjnej na serwerze pomocniczym:

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
> Jeśli musisz usunąć pomocniczą bazę danych, usuń ją z grupy trybu failover przed jej usunięciem. Usunięcie pomocniczej bazy danych przed usunięciem jej z grupy trybu failover może spowodować nieprzewidywalne zachowanie.

## <a name="sql-managed-instance"></a>Wystąpienie zarządzane SQL

Utwórz grupę trybu failover między dwoma wystąpieniami zarządzanymi w programie Azure SQL Managed Instance przy użyciu Azure Portal programu PowerShell.

Musisz skonfigurować usługę [ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) lub utworzyć bramę dla sieci wirtualnej każdego SQL Managed Instance, połączyć dwie bramy, a następnie utworzyć grupę trybu failover. 

Wdobyj oba wystąpienia zarządzane w [sparowanych regionach](../../best-practices-availability-paired-regions.md) ze względu na wydajność. Wystąpienia zarządzane, które są w regionach sparowanych geograficznie, mają znacznie lepszą wydajność niż niezapłacone regiony. 

### <a name="prerequisites"></a>Wymagania wstępne

Należy wziąć pod uwagę następujące wymagania wstępne:

- Pomocnicze wystąpienie zarządzane musi być puste.
- Zakres podsieci dodatkowej sieci wirtualnej nie może nakładać się na zakres podsieci podstawowej sieci wirtualnej.
- Sortowanie i strefa czasowa pomocniczego wystąpienia zarządzanego muszą być zgodne z sortowaniem podstawowego wystąpienia zarządzanego.
- Podczas łączenia dwóch bram klucz **wspólny** powinien być taki sam dla obu połączeń.

### <a name="create-primary-virtual-network-gateway"></a>Tworzenie podstawowej bramy sieci wirtualnej

Jeśli nie skonfigurowano usługi [ExpressRoute,](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md)możesz utworzyć podstawową bramę sieci wirtualnej przy użyciu Azure Portal programu PowerShell.

> [!NOTE]
> Jednostki SKU bramy wpływają na wydajność przepływności. W tym artykule wdrożono bramę z najbardziej podstawową sku `HwGw1` (). Wdrożenie wyższej jednostki SKU (na przykład : `VpnGw3` ), aby osiągnąć wyższą przepływność. Aby uzyskać wszystkie dostępne opcje, zobacz [Gateway SKUs (Jednostki SKU bramy)](../../vpn-gateway/vpn-gateway-about-vpngateways.md#benchmark) 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Utwórz podstawową bramę sieci wirtualnej przy użyciu Azure Portal.

1. W [Azure Portal](https://portal.azure.com)przejdź do grupy zasobów i wybierz zasób **Sieć wirtualna** dla podstawowego wystąpienia zarządzanego.
1. Wybierz **pozycję Podsieci w** obszarze **Ustawienia,** a następnie wybierz pozycję , aby dodać **nową podsieć bramy.** Pozostaw wartości domyślne.

   ![Dodawanie bramy dla podstawowego wystąpienia zarządzanego](./media/auto-failover-group-configure/add-subnet-gateway-primary-vnet.png)

1. Po utworzeniu bramy podsieci wybierz pozycję Utwórz **zasób** w okienku nawigacji po lewej stronie, a następnie wpisz `Virtual network gateway` w polu wyszukiwania. Wybierz **zasób bramy sieci wirtualnej** opublikowany przez **firmę Microsoft.**

   ![Tworzenie nowej bramy sieci wirtualnej](./media/auto-failover-group-configure/create-virtual-network-gateway.png)

1. Wypełnij pola wymagane do skonfigurowania bramy jako podstawowego wystąpienia zarządzanego.

   W poniższej tabeli przedstawiono wartości niezbędne dla bramy dla podstawowego wystąpienia zarządzanego:

    | **Pole** | Wartość |
    | --- | --- |
    | **Subskrypcja** |  Subskrypcja, w której znajduje się główne wystąpienie zarządzane. |
    | **Nazwa** | Nazwa bramy sieci wirtualnej. |
    | **Region** | Region, w którym znajduje się główne wystąpienie zarządzane. |
    | **Typ bramy** | wybierz pozycję **VPN**. |
    | **Typ sieci VPN** | Wybierz **pozycję Oparte na trasach** |
    | **SKU**| Pozostaw wartość domyślną `VpnGw1` . |
    | **Lokalizacja**| Lokalizacja, w której znajduje się pomocnicze wystąpienie zarządzane i dodatkowa sieć wirtualna.   |
    | **Sieć wirtualna**| Wybierz sieć wirtualną dla pomocniczego wystąpienia zarządzanego. |
    | **Publiczny adres IP**| Wybierz pozycję **Utwórz nowy**. |
    | **Nazwa publicznego adresu IP**| Wprowadź nazwę adresu IP. |
    | &nbsp; | &nbsp; |

1. Pozostaw inne wartości domyślne, a następnie wybierz **pozycję Przejrzyj i utwórz,** aby przejrzeć ustawienia bramy sieci wirtualnej.

   ![Ustawienia bramy podstawowej](./media/auto-failover-group-configure/settings-for-primary-gateway.png)

1. Wybierz **pozycję Utwórz,** aby utworzyć nową bramę sieci wirtualnej.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Utwórz podstawową bramę sieci wirtualnej przy użyciu programu PowerShell.

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

### <a name="create-secondary-virtual-network-gateway"></a>Tworzenie dodatkowej bramy sieci wirtualnej

Utwórz pomocniczą bramę sieci wirtualnej przy użyciu Azure Portal programu PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Powtórz kroki opisane w poprzedniej sekcji, aby utworzyć podsieć i bramę sieci wirtualnej dla pomocniczego wystąpienia zarządzanego. Wypełnij pola wymagane do skonfigurowania bramy dla pomocniczego wystąpienia zarządzanego.

W poniższej tabeli przedstawiono wartości niezbędne dla bramy dla pomocniczego wystąpienia zarządzanego:

   | **Pole** | Wartość |
   | --- | --- |
   | **Subskrypcja** |  Subskrypcja, w której znajduje się pomocnicze wystąpienie zarządzane. |
   | **Nazwa** | Nazwa bramy sieci wirtualnej, taka jak `secondary-mi-gateway` . |
   | **Region** | Region, w którym znajduje się pomocnicze wystąpienie zarządzane. |
   | **Typ bramy** | wybierz pozycję **VPN**. |
   | **Typ sieci VPN** | Wybierz **pozycję Oparta na trasach** |
   | **SKU**| Pozostaw wartość domyślną `VpnGw1` . |
   | **Lokalizacja**| Lokalizacja, w której znajduje się pomocnicze wystąpienie zarządzane i dodatkowa sieć wirtualna.   |
   | **Sieć wirtualna**| Wybierz sieć wirtualną utworzoną w sekcji 2, na przykład `vnet-sql-mi-secondary` . |
   | **Publiczny adres IP**| Wybierz pozycję **Utwórz nowy**. |
   | **Nazwa publicznego adresu IP**| Wprowadź nazwę adresu IP, na przykład `secondary-gateway-IP` . |
   | &nbsp; | &nbsp; |

   ![Ustawienia bramy pomocniczej](./media/auto-failover-group-configure/settings-for-secondary-gateway.png)

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Utwórz pomocniczą bramę sieci wirtualnej przy użyciu programu PowerShell.

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

### <a name="connect-the-gateways"></a>Łączenie bram

Tworzenie połączeń między dwiema bramami przy użyciu Azure Portal lub Programu PowerShell.

Należy utworzyć dwa połączenia — połączenie z bramy podstawowej do bramy pomocniczej, a następnie połączenie z bramy pomocniczej do bramy podstawowej.

Klucz wspólny używany dla obu połączeń powinien być taki sam dla każdego połączenia.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Utwórz połączenia między dwiema bramami przy użyciu Azure Portal.

1. Wybierz **pozycję Utwórz zasób** z [Azure Portal](https://portal.azure.com).
1. Wpisz w polu wyszukiwania, a następnie naciśnij klawisz Enter, aby wyszukać, co umożliwia dostęp do `connection` zasobu **Połączenie** opublikowanego przez firmę Microsoft.
1. Wybierz **pozycję Utwórz,** aby utworzyć połączenie.
1. Na karcie **Podstawowe** wybierz następujące wartości, a następnie wybierz przycisk **OK.**
    1. Wybierz `VNet-to-VNet` dla opcji Typ **połączenia**.
    1. Wybierz subskrypcję z listy rozwijanej.
    1. Wybierz grupę zasobów dla wystąpienia zarządzanego z listy rozwijanej.
    1. Wybierz lokalizację podstawowego wystąpienia zarządzanego z listy rozwijanej.
1. Na karcie **Ustawienia** wybierz lub wprowadź następujące wartości, a następnie wybierz przycisk **OK:**
    1. Wybierz bramę sieci podstawowej dla bramy **pierwszej sieci wirtualnej,** taką jak `Primary-Gateway` .  
    1. Wybierz bramę sieci dodatkowej dla drugiej bramy **sieci wirtualnej,** taką jak `Secondary-Gateway` .
    1. Zaznacz pole wyboru obok opcji **Ustanów łączność dwukierunkową**.
    1. Pozostaw domyślną podstawową nazwę połączenia lub zmień jej nazwę na wartość.
    1. Podaj klucz **wspólny (PSK)** dla połączenia, taki jak `mi1m2psk` .

   ![Tworzenie połączenia bramy](./media/auto-failover-group-configure/create-gateway-connection.png)

1. Na **karcie Podsumowanie** przejrzyj ustawienia połączenia dwukierunkowego, a następnie wybierz przycisk **OK,** aby utworzyć połączenie.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Tworzenie połączeń między dwiema bramami przy użyciu programu PowerShell.

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

Utwórz grupę trybu failover dla wystąpień zarządzanych przy użyciu programu Azure Portal lub PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Utwórz grupę trybu failover dla usługi SQL Managed Instances przy użyciu Azure Portal.

1. Wybierz **Azure SQL** w menu po lewej stronie Azure Portal [.](https://portal.azure.com) Jeśli **Azure SQL** nie ma na liście, wybierz **pozycję** Wszystkie usługi , a następnie wpisz Azure SQL w polu wyszukiwania. (Opcjonalnie) Wybierz gwiazdkę obok pozycji **Azure SQL,** aby dodać ją do ulubionych, i dodaj ją jako element w nawigacji po lewej stronie.
1. Wybierz podstawowe wystąpienie zarządzane, które chcesz dodać do grupy trybu failover.  
1. W **obszarze Ustawienia** przejdź do grupy trybu **failover wystąpienia,** a następnie wybierz pozycję **Dodaj** grupę, aby otworzyć stronę Grupa **trybu failover wystąpienia.**

   ![Dodawanie grupy trybu failover](./media/auto-failover-group-configure/add-failover-group.png)

1. Na stronie **Grupa trybu failover wystąpienia** wpisz nazwę grupy trybu failover, a następnie wybierz z listy rozwijanej pomocnicze wystąpienie zarządzane. Wybierz **pozycję Utwórz,** aby utworzyć grupę trybu failover.

   ![Tworzenie grupy trybu failover](./media/auto-failover-group-configure/create-failover-group.png)

1. Po zakończeniu wdrażania grupy trybu failover wrócisz do strony **Grupy trybu failover.**

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

Przetestuj trybu failover grupy trybu failover przy użyciu Azure Portal lub programu PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Przetestuj trybu failover grupy trybu failover przy użyciu Azure Portal.

1. Przejdź do _pomocniczego wystąpienia_ zarządzanego w obszarze [Azure Portal](https://portal.azure.com) a następnie wybierz pozycję Grupy **trybu failover wystąpienia** w obszarze ustawień.
1. Sprawdź, które wystąpienie zarządzane jest wystąpieniem podstawowym, a które wystąpieniem pomocniczym.
1. Wybierz **pozycję Failover (Trybu failover),** a następnie wybierz pozycję **Yes (Tak)** w ostrzeżeniu o odłączonych sesjach TDS.

   ![Failover the failover group (Przejmij grupę trybu failover w celu trybu fail](./media/auto-failover-group-configure/failover-mi-failover-group.png)

1. Sprawdź, które wystąpienie jest wystąpieniem podstawowym, a które wystąpieniem pomocniczym. Jeśli przejście w tryb failover zakończyło się pomyślnie, oba wystąpienia powinny mieć przełączone role.

   ![Wystąpienia zarządzane mają przełączone role po przełączeniu w tryb failover](./media/auto-failover-group-configure/mi-switched-after-failover.png)

1. Przejdź do nowego _pomocniczego wystąpienia_ zarządzanego i ponownie wybierz pozycję **Failover (Przejdź do trybu failover),** aby powieść wystąpienie podstawowe z powrotem do roli podstawowej.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Przetestuj trybu failover grupy trybu failover przy użyciu programu PowerShell.

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

Użycie łącza prywatnego umożliwia skojarzenie serwera logicznego z określonym prywatnym adresem IP w sieci wirtualnej i podsieci. 

Aby użyć linku prywatnego z grupą trybu failover, wykonaj następujące czynności:

1. Upewnij się, że serwer podstawowy i pomocniczy znajdują się w [sparowanych regionach.](../../best-practices-availability-paired-regions.md) 
1. Utwórz sieć wirtualną i podsieć w każdym regionie, aby hostować prywatne punkty końcowe dla serwerów podstawowych i pomocniczych w taki sposób, aby nie nakładały się przestrzenie adresów IP. Na przykład zakres adresów podstawowej sieci wirtualnej 10.0.0.0/16 i zakres adresów dodatkowej sieci wirtualnej 10.0.0.1/16 nakładają się na siebie. Aby uzyskać więcej informacji na temat zakresów adresów sieci wirtualnej, zobacz blog [projektowanie sieci wirtualnych platformy Azure.](https://devblogs.microsoft.com/premier-developer/understanding-cidr-notation-when-designing-azure-virtual-networks-and-subnets/)
1. Utwórz prywatny [punkt końcowy i strefę Prywatna strefa DNS Azure dla serwera podstawowego.](../../private-link/create-private-endpoint-portal.md#create-a-private-endpoint) 
1. Utwórz również prywatny punkt końcowy dla serwera pomocniczego, ale tym razem wybierz ponowne użycie tej samej strefy Prywatna strefa DNS, która została utworzona dla serwera podstawowego. 
1. Po utworzeniu linku prywatnego możesz utworzyć grupę trybu failover, zgodnie z instrukcjami opisanymi wcześniej w tym artykule. 


## <a name="locate-listener-endpoint"></a>Lokalizowanie punktu końcowego odbiornika

Po skonfigurowaniu grupy trybu failover zaktualizuj ciąg połączenia aplikacji do punktu końcowego odbiornika. Dzięki temu aplikacja będzie połączona z odbiornikiem grupy trybu failover, a nie podstawową bazą danych, pulą elastyczną lub bazą danych wystąpienia. Dzięki temu nie trzeba ręcznie aktualizować parametrów połączenia za każdym razem, gdy jednostka bazy danych ulegnie awarii, a ruch jest przekierowyny do tej jednostki, która jest obecnie podstawowa.

Punkt końcowy odbiornika ma postać i jest widoczny w Azure Portal `fog-name.database.windows.net` podczas wyświetlania grupy trybu failover:

![Parametrów połączenia grupy trybu failover](./media/auto-failover-group-configure/find-failover-group-connection-string.png)

## <a name="remarks"></a>Uwagi

- Usunięcie grupy trybu failover dla pojedynczej bazy danych lub bazy danych w puli nie powoduje zatrzymania replikacji i nie powoduje usunięcia replikowanej bazy danych. Musisz ręcznie zatrzymać replikację geograficzną i usunąć bazę danych z serwera pomocniczego, jeśli chcesz dodać pojedynczą bazę danych lub bazę danych w puli z powrotem do grupy trybu failover po jej usunięciu. Niepowodzenie albo może spowodować błąd podobny do podczas próby dodania bazy danych `The operation cannot be performed due to multiple errors` do grupy trybu failover.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać szczegółowe instrukcje konfigurowania grupy trybu failover, zobacz następujące samouczki:

- [Dodawanie pojedynczej bazy danych do grupy trybu failover](failover-group-add-single-database-tutorial.md)
- [Dodawanie elastycznej puli do grupy trybu failover](failover-group-add-elastic-pool-tutorial.md)
- [Dodawanie wystąpienia zarządzanego do grupy trybu failover](../managed-instance/failover-group-add-instance-tutorial.md)

Omówienie opcji wysokiej Azure SQL Database, zobacz [Replikacja geograficzna](active-geo-replication-overview.md) i [grupy automatycznego trybu failover.](auto-failover-group-overview.md)
