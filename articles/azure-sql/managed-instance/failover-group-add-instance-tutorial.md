---
title: 'Samouczek: dodawanie wystąpienia zarządzanego SQL do grupy trybu failover'
titleSuffix: Azure SQL Managed Instance
description: W tym samouczku dowiesz się, jak utworzyć grupę trybu failover między podstawowym i pomocniczym wystąpieniem zarządzanym usługi Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: high-availability
ms.custom: sqldbrb=1, devx-track-azurepowershell
ms.devlang: ''
ms.topic: tutorial
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sashan, sstein
ms.date: 08/27/2019
ms.openlocfilehash: df10e2b674a8e97766ee96a802e614e2bd797b7b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91617744"
---
# <a name="tutorial-add-sql-managed-instance-to-a-failover-group"></a>Samouczek: dodawanie wystąpienia zarządzanego SQL do grupy trybu failover
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Dodaj zarządzane wystąpienia wystąpienia zarządzanego usługi Azure SQL do grupy trybu failover. W tym artykule dowiesz się, jak:

> [!div class="checklist"]
> - Utwórz podstawowe wystąpienie zarządzane.
> - Utwórz dodatkowe wystąpienie zarządzane jako część [grupy trybu failover](../database/auto-failover-group-overview.md). 
> - Testowanie pracy w trybie failover.

  > [!NOTE]
  > - Korzystając z tego samouczka, należy się upewnić, że skonfigurowano zasoby z [wymaganiami wstępnymi dotyczącymi konfigurowania grup trybu failover dla wystąpienia zarządzanego SQL](../database/auto-failover-group-overview.md#enabling-geo-replication-between-managed-instances-and-their-vnets). 
  > - Tworzenie wystąpienia zarządzanego może zająć dużo czasu. W związku z tym ten samouczek może trwać kilka godzin. Aby uzyskać więcej informacji na temat czasów udostępniania, zobacz [operacje zarządzania wystąpieniami zarządzanymi przez program SQL Server](sql-managed-instance-paas-overview.md#management-operations). 
  > - Wystąpienia zarządzane uczestniczące w grupie trybu failover wymagają [platformy Azure ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) lub dwóch podłączonych bram sieci VPN. Globalna komunikacja równorzędna sieci wirtualnych nie jest obsługiwana. Ten samouczek zawiera instrukcje dotyczące tworzenia i łączenia bram sieci VPN. Pomiń te kroki, jeśli skonfigurowano już ExpressRoute. 


## <a name="prerequisites"></a>Wymagania wstępne

# <a name="portal"></a>[Portal](#tab/azure-portal)
Aby ukończyć kroki tego samouczka, upewnij się, że dysponujesz następującymi elementami: 

- Subskrypcja platformy Azure. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) , jeśli jeszcze go nie masz.


# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
Aby ukończyć ten samouczek, upewnij się, że masz następujące elementy:

- Subskrypcja platformy Azure. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) , jeśli jeszcze go nie masz.
- [Azure PowerShell](/powershell/azure/)

---


## <a name="create-a-resource-group-and-primary-managed-instance"></a>Tworzenie grupy zasobów i podstawowego wystąpienia zarządzanego

W tym kroku utworzysz grupę zasobów i podstawowe wystąpienie zarządzane dla grupy trybu failover przy użyciu Azure Portal lub programu PowerShell. 

Wdróż oba wystąpienia zarządzane do [sparowanych regionów](../../best-practices-availability-paired-regions.md) ze względu na wydajność. Wystąpienia zarządzane znajdujące się w regionach z parą geograficzną mają znacznie lepszą wydajność w porównaniu z niesparowanymi regionami. 


# <a name="portal"></a>[Portal](#tab/azure-portal) 

Utwórz grupę zasobów i główne wystąpienie zarządzane przy użyciu Azure Portal. 

1. Wybierz pozycję **Azure SQL** w menu po lewej stronie Azure Portal. Jeśli na liście nie ma **usługi Azure SQL** , wybierz pozycję **wszystkie usługi**, a następnie wpisz `Azure SQL` w polu wyszukiwania. Obowiązkowe Wybierz gwiazdkę obok pozycji **Azure SQL** , aby ją dodać do ulubionych, i Dodaj ją jako element w nawigacji po lewej stronie. 
1. Wybierz pozycję **+ Dodaj** , aby otworzyć stronę **Wybieranie opcji wdrożenia SQL** . Aby wyświetlić dodatkowe informacje o różnych bazach danych, wybierz pozycję **Pokaż szczegóły** na kafelku **bazy danych** .
1. Wybierz pozycję **Utwórz** na kafelku **wystąpienia zarządzane SQL** . 

    ![Wybierz wystąpienie zarządzane SQL](./media/failover-group-add-instance-tutorial/select-managed-instance.png)

1. Na stronie **Tworzenie wystąpienia zarządzanego usługi Azure SQL** na karcie **podstawowe** :
    1. W obszarze **szczegóły projektu**wybierz **subskrypcję** z listy rozwijanej, a następnie wybierz opcję **Utwórz nową** grupę zasobów. Wpisz nazwę grupy zasobów, na przykład `myResourceGroup` . 
    1. W obszarze **szczegóły wystąpienia zarządzanego SQL**Podaj nazwę wystąpienia zarządzanego oraz region, w którym chcesz wdrożyć wystąpienie zarządzane. Pozostaw wartości domyślne w obszarze **obliczenia i magazyn** . 
    1. W obszarze **konto administratora**Podaj nazwę logowania administratora, na przykład `azureuser` i złożone hasło administratora. 

    ![Utwórz podstawowe wystąpienie zarządzane](./media/failover-group-add-instance-tutorial/primary-sql-mi-values.png)

1. Pozostaw pozostałe ustawienia w wartości domyślne, a następnie wybierz pozycję **Przegląd + Utwórz** , aby przejrzeć ustawienia wystąpienia zarządzanego SQL. 
1. Wybierz pozycję **Utwórz** , aby utworzyć podstawowe wystąpienie zarządzane. 

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Utwórz grupę zasobów i podstawowe wystąpienie zarządzane przy użyciu programu PowerShell. 

   ```powershell-interactive
   # Connect-AzAccount
   # The SubscriptionId in which to create these objects
   $SubscriptionId = '<Subscription-ID>'
   # Create a random identifier to use as subscript for the different resource names
   $randomIdentifier = $(Get-Random)
   # Set the resource group name and location for SQL Managed Instance
   $resourceGroupName = "myResourceGroup-$randomIdentifier"
   $location = "eastus"
   $drLocation = "eastus2"
   
   # Set the networking values for your primary managed instance
   $primaryVNet = "primaryVNet-$randomIdentifier"
   $primaryAddressPrefix = "10.0.0.0/16"
   $primaryDefaultSubnet = "primaryDefaultSubnet-$randomIdentifier"
   $primaryDefaultSubnetAddress = "10.0.0.0/24"
   $primaryMiSubnetName = "primaryMISubnet-$randomIdentifier"
   $primaryMiSubnetAddress = "10.0.0.0/24"
   $primaryMiGwSubnetAddress = "10.0.255.0/27"
   $primaryGWName = "primaryGateway-$randomIdentifier"
   $primaryGWPublicIPAddress = $primaryGWName + "-ip"
   $primaryGWIPConfig = $primaryGWName + "-ipc"
   $primaryGWAsn = 61000
   $primaryGWConnection = $primaryGWName + "-connection"
   
   
   # Set the networking values for your secondary managed instance
   $secondaryVNet = "secondaryVNet-$randomIdentifier"
   $secondaryAddressPrefix = "10.128.0.0/16"
   $secondaryDefaultSubnet = "secondaryDefaultSubnet-$randomIdentifier"
   $secondaryDefaultSubnetAddress = "10.128.0.0/24"
   $secondaryMiSubnetName = "secondaryMISubnet-$randomIdentifier"
   $secondaryMiSubnetAddress = "10.128.0.0/24"
   $secondaryMiGwSubnetAddress = "10.128.255.0/27"
   $secondaryGWName = "secondaryGateway-$randomIdentifier"
   $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
   $secondaryGWIPConfig = $secondaryGWName + "-ipc"
   $secondaryGWAsn = 62000
   $secondaryGWConnection = $secondaryGWName + "-connection"
   
   
   
   # Set the SQL Managed Instance name for the new managed instances
   $primaryInstance = "primary-mi-$randomIdentifier"
   $secondaryInstance = "secondary-mi-$randomIdentifier"
   
   # Set the admin login and password for SQL Managed Instance
   $secpasswd = "PWD27!"+(New-Guid).Guid | ConvertTo-SecureString -AsPlainText -Force
   $mycreds = New-Object System.Management.Automation.PSCredential ("azureuser", $secpasswd)
   
   
   # Set the SQL Managed Instance service tier, compute level, and license mode
   $edition = "General Purpose"
   $vCores = 8
   $maxStorage = 256
   $computeGeneration = "Gen5"
   $license = "LicenseIncluded" #"BasePrice" or LicenseIncluded if you have don't have SQL Server license that can be used for AHB discount
   
   # Set failover group details
   $vpnSharedKey = "mi1mi2psk"
   $failoverGroupName = "failovergroup-$randomIdentifier"
   
   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName
   Write-host "Password is" $secpasswd
   Write-host "Primary Virtual Network name is" $primaryVNet
   Write-host "Primary default subnet name is" $primaryDefaultSubnet
   Write-host "Primary SQL Managed Instance subnet name is" $primaryMiSubnetName
   Write-host "Secondary Virtual Network name is" $secondaryVNet
   Write-host "Secondary default subnet name is" $secondaryDefaultSubnet
   Write-host "Secondary SQL Managed Instance subnet name is" $secondaryMiSubnetName
   Write-host "Primary SQL Managed Instance name is" $primaryInstance
   Write-host "Secondary SQL Managed Instance name is" $secondaryInstance
   Write-host "Failover group name is" $failoverGroupName
   
   # Suppress networking breaking changes warning (https://aka.ms/azps-changewarnings
   Set-Item Env:\SuppressAzurePowerShellBreakingChangeWarnings "true"
   
   # Set the subscription context
   Set-AzContext -SubscriptionId $subscriptionId 
   
   # Create the resource group
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup
   
   # Configure the primary virtual network
   Write-host "Creating primary virtual network..."
   $primaryVirtualNetwork = New-AzVirtualNetwork `
                         -ResourceGroupName $resourceGroupName `
                         -Location $location `
                         -Name $primaryVNet `
                         -AddressPrefix $primaryAddressPrefix
   
                     Add-AzVirtualNetworkSubnetConfig `
                         -Name $primaryMiSubnetName `
                         -VirtualNetwork $primaryVirtualNetwork `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                     | Set-AzVirtualNetwork
   $primaryVirtualNetwork
   
   
   # Configure the primary managed instance subnet
   Write-host "Configuring primary MI subnet..."
   $primaryVirtualNetwork = Get-AzVirtualNetwork -Name $primaryVNet -ResourceGroupName $resourceGroupName
   
   
   $primaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $primaryMiSubnetName `
                           -VirtualNetwork $primaryVirtualNetwork
   $primaryMiSubnetConfig
   
   # Configure the network security group management service
   Write-host "Configuring primary MI subnet..."
   
   $primaryMiSubnetConfigId = $primaryMiSubnetConfig.Id
   
   $primaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'primaryNSGMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryNSGMiManagementService
   
   # Configure the route table management service
   Write-host "Configuring primary MI route table management service..."
   
   $primaryRouteTableMiManagementService = New-AzRouteTable `
                         -Name 'primaryRouteTableMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryRouteTableMiManagementService
   
   # Configure the primary network security group
   Write-host "Configuring primary network security group..."
   Set-AzVirtualNetworkSubnetConfig `
                         -VirtualNetwork $primaryVirtualNetwork `
                         -Name $primaryMiSubnetName `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                         -NetworkSecurityGroup $primaryNSGMiManagementService `
                         -RouteTable $primaryRouteTableMiManagementService | `
                       Set-AzVirtualNetwork
   
   Get-AzNetworkSecurityGroup `
                         -ResourceGroupName $resourceGroupName `
                         -Name "primaryNSGMiManagementService" `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 9000,9003,1438,1440,1452 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix $PrimaryMiSubnetAddress `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 300 `
                         -Name "allow_health_probe_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix AzureLoadBalancer `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1000 `
                         -Name "allow_tds_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 1433 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_inbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 80,443,12000 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_outbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix $PrimaryMiSubnetAddress `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_outbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Set-AzNetworkSecurityGroup
   Write-host "Primary network security group configured successfully."
   
   
   Get-AzRouteTable `
                         -ResourceGroupName $resourceGroupName `
                         -Name "primaryRouteTableMiManagementService" `
                       | Add-AzRouteConfig `
                         -Name "primaryToMIManagementService" `
                         -AddressPrefix 0.0.0.0/0 `
                         -NextHopType Internet `
                       | Add-AzRouteConfig `
                         -Name "ToLocalClusterNode" `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                         -NextHopType VnetLocal `
                       | Set-AzRouteTable
   Write-host "Primary network route table configured successfully."
   
   
   # Create the primary managed instance
   
   Write-host "Creating primary SQL Managed Instance..."
   Write-host "This will take some time, see https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations or more information."
   New-AzSqlInstance -Name $primaryInstance `
                         -ResourceGroupName $resourceGroupName `
                         -Location $location `
                         -SubnetId $primaryMiSubnetConfigId `
                         -AdministratorCredential $mycreds `
                         -StorageSizeInGB $maxStorage `
                         -VCore $vCores `
                         -Edition $edition `
                         -ComputeGeneration $computeGeneration `
                         -LicenseType $license
   Write-host "Primary SQL Managed Instance created successfully."
   ```

W tej części samouczka są stosowane następujące polecenia cmdlet programu PowerShell:

| Polecenie | Uwagi |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tworzy grupę zasobów platformy Azure.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Tworzy sieć wirtualną.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Dodaje konfigurację podsieci do sieci wirtualnej. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Pobiera sieć wirtualną w grupie zasobów. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Pobiera podsieć w sieci wirtualnej. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Tworzy sieciową grupę zabezpieczeń. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Tworzy tabelę tras. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Aktualizuje konfigurację podsieci dla sieci wirtualnej.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Aktualizuje sieć wirtualną.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Pobiera sieciową grupę zabezpieczeń. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Dodaje konfigurację reguły zabezpieczeń sieci do sieciowej grupy zabezpieczeń. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Aktualizuje sieciową grupę zabezpieczeń.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Dodaje trasę do tabeli tras. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Aktualizuje tabelę tras.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Tworzy wystąpienie zarządzane.  |

---

## <a name="create-secondary-virtual-network"></a>Tworzenie dodatkowej sieci wirtualnej

Jeśli używasz Azure Portal, aby utworzyć wystąpienie zarządzane, musisz oddzielnie utworzyć sieć wirtualną, ponieważ istnieje wymóg, aby podsieć podstawowego i pomocniczego wystąpienia zarządzanego nie miała nakładających się zakresów. Jeśli używasz programu PowerShell do konfigurowania wystąpienia zarządzanego, przejdź do kroku 3. 

# <a name="portal"></a>[Portal](#tab/azure-portal) 

Aby sprawdzić zakres podsieci podstawowej sieci wirtualnej, wykonaj następujące kroki:

1. W [Azure Portal](https://portal.azure.com)przejdź do grupy zasobów i wybierz sieć wirtualną dla wystąpienia podstawowego.  
2. W obszarze **Ustawienia** wybierz pozycję **podsieci** i zanotuj **zakres adresów**. Zakres adresów podsieci sieci wirtualnej dla pomocniczego wystąpienia zarządzanego nie może się nakładać. 


   ![Podsieć podstawowa](./media/failover-group-add-instance-tutorial/verify-primary-subnet-range.png)

Aby utworzyć sieć wirtualną, wykonaj następujące kroki:

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **Utwórz zasób** i Wyszukaj *sieć wirtualną*. 
1. Wybierz opcję **Virtual Network** publikowaną przez firmę Microsoft, a następnie wybierz pozycję **Utwórz** na następnej stronie. 
1. Wypełnij pola wymagane, aby skonfigurować sieć wirtualną dla pomocniczego wystąpienia zarządzanego, a następnie wybierz pozycję **Utwórz**. 

   W poniższej tabeli przedstawiono wartości niezbędne do zapewnienia dodatkowej sieci wirtualnej:

    | **Pole** | Wartość |
    | --- | --- |
    | **Nazwa** |  Nazwa sieci wirtualnej, która ma być używana przez pomocnicze wystąpienie zarządzane, na przykład `vnet-sql-mi-secondary` . |
    | **Przestrzeń adresowa** | Przestrzeń adresowa sieci wirtualnej, na przykład `10.128.0.0/16` . | 
    | **Subskrypcja** | Subskrypcja, w której znajduje się główne wystąpienie zarządzane i Grupa zasobów. |
    | **Region** | Lokalizacja, w której zostanie wdrożone pomocnicze wystąpienie zarządzane. |
    | **Podsieć** | Nazwa podsieci. `default` jest dostarczany domyślnie. |
    | **Zakres adresów**| Zakres adresów dla podsieci. Ta wartość musi być inna niż zakres adresów podsieci używany przez sieć wirtualną głównego wystąpienia zarządzanego, na przykład `10.128.0.0/24` .  |
    | &nbsp; | &nbsp; |

    ![Dodatkowe wartości sieci wirtualnej](./media/failover-group-add-instance-tutorial/secondary-virtual-network.png)

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Ten krok jest wymagany tylko wtedy, gdy używasz Azure Portal do wdrożenia wystąpienia zarządzanego SQL. Przejdź do kroku 3, jeśli używasz programu PowerShell. 

---

## <a name="create-a-secondary-managed-instance"></a>Tworzenie pomocniczego wystąpienia zarządzanego
W tym kroku utworzysz dodatkowe wystąpienie zarządzane w Azure Portal, które również skonfiguruje sieć między dwoma wystąpieniami zarządzanymi. 

Drugie wystąpienie zarządzane musi:
- Być pusty. 
- Ma inną podsieć i zakres adresów IP niż podstawowe wystąpienie zarządzane. 

# <a name="portal"></a>[Portal](#tab/azure-portal) 

Utwórz pomocnicze wystąpienie zarządzane przy użyciu Azure Portal. 

1. Wybierz pozycję **Azure SQL** w menu po lewej stronie Azure Portal. Jeśli na liście nie ma **usługi Azure SQL** , wybierz pozycję **wszystkie usługi**, a następnie wpisz `Azure SQL` w polu wyszukiwania. Obowiązkowe Wybierz gwiazdkę obok pozycji **Azure SQL** , aby ją dodać do ulubionych, i Dodaj ją jako element w nawigacji po lewej stronie. 
1. Wybierz pozycję **+ Dodaj** , aby otworzyć stronę **Wybieranie opcji wdrożenia SQL** . Aby wyświetlić dodatkowe informacje o różnych bazach danych, wybierz pozycję **Pokaż szczegóły** na kafelku **bazy danych** .
1. Wybierz pozycję **Utwórz** na kafelku **wystąpienia zarządzane SQL** . 

    ![Wybierz wystąpienie zarządzane SQL](./media/failover-group-add-instance-tutorial/select-managed-instance.png)

1. Na karcie **podstawy** na stronie **Tworzenie wystąpienia zarządzanego usługi Azure SQL** wprowadź wymagane pola, aby skonfigurować dodatkowe wystąpienie zarządzane. 

   W poniższej tabeli przedstawiono wartości niezbędne do pomocniczego wystąpienia zarządzanego:
 
    | **Pole** | Wartość |
    | --- | --- |
    | **Subskrypcja** |  Subskrypcja, w której znajduje się główne wystąpienie zarządzane. |
    | **Grupa zasobów**| Grupa zasobów, w której znajduje się podstawowe wystąpienie zarządzane. |
    | **Nazwa wystąpienia zarządzanego SQL** | Nazwa nowego pomocniczego wystąpienia zarządzanego, na przykład `sql-mi-secondary` .  | 
    | **Region**| Lokalizacja pomocniczego wystąpienia zarządzanego.  |
    | **Logowanie administratora wystąpienia zarządzanego SQL** | Nazwa logowania, która ma być używana dla nowego pomocniczego wystąpienia zarządzanego, na przykład `azureuser` . |
    | **Hasło** | Złożone hasło, które będzie używane przez nazwę logowania administratora dla nowego wystąpienia zarządzanego.  |
    | &nbsp; | &nbsp; |

1. Na karcie **Sieć** dla **Virtual Network**wybierz sieć wirtualną utworzoną dla pomocniczego wystąpienia zarządzanego z listy rozwijanej.

   ![Pomocnicza sieć MI](./media/failover-group-add-instance-tutorial/networking-settings-for-secondary-mi.png)

1. Na karcie **Ustawienia dodatkowe** na potrzeby **replikacji geograficznej**wybierz opcję **tak** , aby _użyć jej jako pomocniczej pracy w trybie failover_. Wybierz z listy rozwijanej podstawowe wystąpienie zarządzane. 
    
   Upewnij się, że sortowanie i strefa czasowa są zgodne z podstawowym wystąpieniem zarządzanym. Główne wystąpienie zarządzane utworzone w tym samouczku użyło domyślnego `SQL_Latin1_General_CP1_CI_AS` sortowania i `(UTC) Coordinated Universal Time` strefy czasowej. 

   ![Obsługa dodatkowych sieci wystąpienia zarządzanego](./media/failover-group-add-instance-tutorial/secondary-mi-failover.png)

1. Wybierz pozycję **Przegląd + Utwórz** , aby przejrzeć ustawienia dla pomocniczego wystąpienia zarządzanego. 
1. Wybierz pozycję **Utwórz** , aby utworzyć pomocnicze wystąpienie zarządzane. 

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Utwórz dodatkowe wystąpienie zarządzane przy użyciu programu PowerShell. 

   ```powershell-interactive
   # Configure the secondary virtual network
   Write-host "Configuring secondary virtual network..."
   
   $SecondaryVirtualNetwork = New-AzVirtualNetwork `
                         -ResourceGroupName $resourceGroupName `
                         -Location $drlocation `
                         -Name $secondaryVNet `
                         -AddressPrefix $secondaryAddressPrefix
   
   Add-AzVirtualNetworkSubnetConfig `
                         -Name $secondaryMiSubnetName `
                         -VirtualNetwork $SecondaryVirtualNetwork `
                         -AddressPrefix $secondaryMiSubnetAddress `
                       | Set-AzVirtualNetwork
   $SecondaryVirtualNetwork
   
   # Configure the secondary managed instance subnet
   Write-host "Configuring secondary MI subnet..."
   
   $SecondaryVirtualNetwork = Get-AzVirtualNetwork -Name $secondaryVNet `
                                   -ResourceGroupName $resourceGroupName
   
   $secondaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $secondaryMiSubnetName `
                           -VirtualNetwork $SecondaryVirtualNetwork
   $secondaryMiSubnetConfig
   
   # Configure the secondary network security group management service
   Write-host "Configuring secondary network security group management service..."
   
   $secondaryMiSubnetConfigId = $secondaryMiSubnetConfig.Id
   
   $secondaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'secondaryToMIManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryNSGMiManagementService
   
   # Configure the secondary route table MI management service
   Write-host "Configuring secondary route table MI management service..."
   
   $secondaryRouteTableMiManagementService = New-AzRouteTable `
                         -Name 'secondaryRouteTableMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryRouteTableMiManagementService
   
   # Configure the secondary network security group
   Write-host "Configuring secondary network security group..."
   
   Set-AzVirtualNetworkSubnetConfig `
                         -VirtualNetwork $SecondaryVirtualNetwork `
                         -Name $secondaryMiSubnetName `
                         -AddressPrefix $secondaryMiSubnetAddress `
                         -NetworkSecurityGroup $secondaryNSGMiManagementService `
                         -RouteTable $secondaryRouteTableMiManagementService `
                       | Set-AzVirtualNetwork
   
   Get-AzNetworkSecurityGroup `
                         -ResourceGroupName $resourceGroupName `
                         -Name "secondaryToMIManagementService" `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 9000,9003,1438,1440,1452 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix $secondaryMiSubnetAddress `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 300 `
                         -Name "allow_health_probe_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix AzureLoadBalancer `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1000 `
                         -Name "allow_tds_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 1433 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_inbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 80,443,12000 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_outbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix $secondaryMiSubnetAddress `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_outbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Set-AzNetworkSecurityGroup
   
   
   Get-AzRouteTable `
                         -ResourceGroupName $resourceGroupName `
                         -Name "secondaryRouteTableMiManagementService" `
                       | Add-AzRouteConfig `
                         -Name "secondaryToMIManagementService" `
                         -AddressPrefix 0.0.0.0/0 `
                         -NextHopType Internet `
                       | Add-AzRouteConfig `
                         -Name "ToLocalClusterNode" `
                         -AddressPrefix $secondaryMiSubnetAddress `
                         -NextHopType VnetLocal `
                       | Set-AzRouteTable
   Write-host "Secondary network security group configured successfully."
   
   # Create the secondary managed instance
   
   $primaryManagedInstanceId = Get-AzSqlInstance -Name $primaryInstance -ResourceGroupName $resourceGroupName | Select-Object Id
   
   
   Write-host "Creating secondary SQL Managed Instance..."
   Write-host "This will take some time, see https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations or more information."
   New-AzSqlInstance -Name $secondaryInstance `
                     -ResourceGroupName $resourceGroupName `
                     -Location $drLocation `
                     -SubnetId $secondaryMiSubnetConfigId `
                     -AdministratorCredential $mycreds `
                     -StorageSizeInGB $maxStorage `
                     -VCore $vCores `
                     -Edition $edition `
                     -ComputeGeneration $computeGeneration `
                     -LicenseType $license `
                     -DnsZonePartner $primaryManagedInstanceId.Id
   Write-host "Secondary SQL Managed Instance created successfully."
   ```

W tej części samouczka są stosowane następujące polecenia cmdlet programu PowerShell:

| Polecenie | Uwagi |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tworzy grupę zasobów platformy Azure.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Tworzy sieć wirtualną.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Dodaje konfigurację podsieci do sieci wirtualnej. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Pobiera sieć wirtualną w grupie zasobów. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Pobiera podsieć w sieci wirtualnej. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Tworzy sieciową grupę zabezpieczeń. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Tworzy tabelę tras. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Aktualizuje konfigurację podsieci dla sieci wirtualnej.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Aktualizuje sieć wirtualną.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Pobiera sieciową grupę zabezpieczeń. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Dodaje konfigurację reguły zabezpieczeń sieci do sieciowej grupy zabezpieczeń. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Aktualizuje sieciową grupę zabezpieczeń.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Dodaje trasę do tabeli tras. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Aktualizuje tabelę tras.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Tworzy wystąpienie zarządzane.  |

---

## <a name="create-a-primary-gateway"></a>Tworzenie bramy podstawowej 

W przypadku dwóch wystąpień zarządzanych, które mają być uwzględnione w grupie trybu failover, musi istnieć albo ExpressRoute, albo Brama skonfigurowana między sieciami wirtualnymi dwóch wystąpień zarządzanych, aby umożliwić komunikację sieciową. Jeśli zdecydujesz się skonfigurować [ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) zamiast łączyć dwie bramy sieci VPN, przejdź do [kroku 7](#create-a-failover-group).  

Ten artykuł zawiera instrukcje tworzenia dwóch bram sieci VPN i łączenia ich, ale można przejść do tworzenia grupy trybu failover, jeśli zamiast tego została skonfigurowana ExpressRoute. 

> [!NOTE]
> Jednostka SKU bramy wpływa na wydajność przepływności. Ten samouczek służy do wdrażania bramy z największą podstawową jednostką SKU ( `HwGw1` ). Wdróż wyższą jednostkę SKU (przykład: `VpnGw3` ), aby uzyskać większą przepływność. Aby uzyskać wszystkie dostępne opcje, zobacz [jednostki SKU bramy](../../vpn-gateway/vpn-gateway-about-vpngateways.md#benchmark)

# <a name="portal"></a>[Portal](#tab/azure-portal)

Utwórz bramę dla sieci wirtualnej głównego wystąpienia zarządzanego przy użyciu Azure Portal. 


1. W [Azure Portal](https://portal.azure.com)przejdź do grupy zasobów, a następnie wybierz zasób **sieci wirtualnej** dla podstawowego wystąpienia zarządzanego. 
1. W obszarze **Ustawienia** wybierz pozycję **podsieci** , a następnie wybierz opcję, aby dodać nową **podsieć bramy**. Pozostaw wartości domyślne. 

   ![Dodawanie bramy dla podstawowego wystąpienia zarządzanego](./media/failover-group-add-instance-tutorial/add-subnet-gateway-primary-vnet.png)

1. Po utworzeniu bramy podsieci wybierz pozycję **Utwórz zasób** w lewym okienku nawigacji, a następnie wpisz `Virtual network gateway` w polu wyszukiwania. Wybierz zasób **bramy sieci wirtualnej** Opublikowany przez **firmę Microsoft**. 

   ![Utwórz nową bramę sieci wirtualnej](./media/failover-group-add-instance-tutorial/create-virtual-network-gateway.png)

1. Wypełnij pola wymagane, aby skonfigurować bramę dla podstawowego wystąpienia zarządzanego. 

   W poniższej tabeli przedstawiono wartości niezbędne dla bramy dla podstawowego wystąpienia zarządzanego:
 
    | **Pole** | Wartość |
    | --- | --- |
    | **Subskrypcja** |  Subskrypcja, w której znajduje się główne wystąpienie zarządzane. |
    | **Nazwa** | Nazwa bramy sieci wirtualnej, na przykład `primary-mi-gateway` . | 
    | **Region** | Region, w którym znajduje się główne wystąpienie zarządzane. |
    | **Typ bramy** | wybierz pozycję **VPN**. |
    | **Typ sieci VPN** | wybierz pozycję **Oparte na trasach**. |
    | **SKU**| Pozostaw wartość domyślną `VpnGw1` . |
    | **Sieć wirtualna**| Wybierz sieć wirtualną, która została utworzona w sekcji 2, na przykład `vnet-sql-mi-primary` . |
    | **Publiczny adres IP**| Wybierz pozycję**Utwórz nowy**. |
    | **Nazwa publicznego adresu IP**| Wprowadź nazwę adresu IP, na przykład `primary-gateway-IP` . |
    | &nbsp; | &nbsp; |

1. Pozostaw pozostałe wartości jako domyślne, a następnie wybierz pozycję **Przegląd + Utwórz** , aby przejrzeć ustawienia bramy sieci wirtualnej.

   ![Ustawienia bramy podstawowej](./media/failover-group-add-instance-tutorial/settings-for-primary-gateway.png)

1. Wybierz pozycję **Utwórz** , aby utworzyć nową bramę sieci wirtualnej. 


# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Utwórz bramę dla sieci wirtualnej głównego wystąpienia zarządzanego przy użyciu programu PowerShell. 

   ```powershell-interactive
   # Create the primary gateway
   Write-host "Adding GatewaySubnet to primary VNet..."
   Get-AzVirtualNetwork `
                     -Name $primaryVNet `
                     -ResourceGroupName $resourceGroupName `
                   | Add-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -AddressPrefix $primaryMiGwSubnetAddress `
                   | Set-AzVirtualNetwork
   
   $primaryVirtualNetwork  = Get-AzVirtualNetwork `
                     -Name $primaryVNet `
                     -ResourceGroupName $resourceGroupName
   $primaryGatewaySubnet = Get-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -VirtualNetwork $primaryVirtualNetwork
   
   Write-host "Creating primary gateway..."
   Write-host "This will take some time."
   $primaryGWPublicIP = New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $resourceGroupName `
            -Location $location -AllocationMethod Dynamic
   $primaryGatewayIPConfig = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
            -Subnet $primaryGatewaySubnet -PublicIpAddress $primaryGWPublicIP
   
   $primaryGateway = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $resourceGroupName `
       -Location $location -IpConfigurations $primaryGatewayIPConfig -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
   $primaryGateway
   ```

W tej części samouczka są stosowane następujące polecenia cmdlet programu PowerShell:

| Polecenie | Uwagi |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Pobiera sieć wirtualną w grupie zasobów. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Dodaje konfigurację podsieci do sieci wirtualnej. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Aktualizuje sieć wirtualną.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Pobiera podsieć w sieci wirtualnej. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Tworzy publiczny adres IP.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Tworzy konfigurację adresu IP dla bramy sieci wirtualnej. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Tworzy bramę sieci wirtualnej. |


---


## <a name="create-secondary-gateway"></a>Utwórz bramę pomocniczą 
W tym kroku należy utworzyć bramę dla sieci wirtualnej w ramach pomocniczego wystąpienia zarządzanego przy użyciu Azure Portal. 


# <a name="portal"></a>[Portal](#tab/azure-portal)

Korzystając z Azure Portal, powtórz kroki opisane w poprzedniej sekcji, aby utworzyć podsieć i bramę sieci wirtualnej dla pomocniczego wystąpienia zarządzanego. Wypełnij pola wymagane, aby skonfigurować bramę dla pomocniczego wystąpienia zarządzanego. 

   W poniższej tabeli przedstawiono wartości niezbędne dla bramy dla pomocniczego wystąpienia zarządzanego:

   | **Pole** | Wartość |
   | --- | --- |
   | **Subskrypcja** |  Subskrypcja, w której znajduje się pomocnicze wystąpienie zarządzane. |
   | **Nazwa** | Nazwa bramy sieci wirtualnej, na przykład `secondary-mi-gateway` . | 
   | **Region** | Region, w którym znajduje się pomocnicze wystąpienie zarządzane. |
   | **Typ bramy** | wybierz pozycję **VPN**. |
   | **Typ sieci VPN** | wybierz pozycję **Oparte na trasach**. |
   | **SKU**| Pozostaw wartość domyślną `VpnGw1` . |
   | **Sieć wirtualna**| Wybierz sieć wirtualną dla pomocniczego wystąpienia zarządzanego, na przykład `vnet-sql-mi-secondary` . |
   | **Publiczny adres IP**| Wybierz pozycję**Utwórz nowy**. |
   | **Nazwa publicznego adresu IP**| Wprowadź nazwę adresu IP, na przykład `secondary-gateway-IP` . |
   | &nbsp; | &nbsp; |

   ![Ustawienia bramy dodatkowej](./media/failover-group-add-instance-tutorial/settings-for-secondary-gateway.png)


# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Utwórz bramę dla sieci wirtualnej w pomocniczym wystąpieniu zarządzanym przy użyciu programu PowerShell. 

   ```powershell-interactive
   # Create the secondary gateway
   Write-host "Creating secondary gateway..."
   
   Write-host "Adding GatewaySubnet to secondary VNet..."
   Get-AzVirtualNetwork `
                     -Name $secondaryVNet `
                     -ResourceGroupName $resourceGroupName `
                   | Add-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -AddressPrefix $secondaryMiGwSubnetAddress `
                   | Set-AzVirtualNetwork
   
   $secondaryVirtualNetwork  = Get-AzVirtualNetwork `
                     -Name $secondaryVNet `
                     -ResourceGroupName $resourceGroupName
   $secondaryGatewaySubnet = Get-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -VirtualNetwork $secondaryVirtualNetwork
   $drLocation = $secondaryVirtualNetwork.Location
   
   Write-host "Creating secondary gateway..."
   Write-host "This will take some time."
   $secondaryGWPublicIP = New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $resourceGroupName `
            -Location $drLocation -AllocationMethod Dynamic
   $secondaryGatewayIPConfig = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
            -Subnet $secondaryGatewaySubnet -PublicIpAddress $secondaryGWPublicIP
   
   $secondaryGateway = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $resourceGroupName `
       -Location $drLocation -IpConfigurations $secondaryGatewayIPConfig -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn
   $secondaryGateway
   ```

W tej części samouczka są stosowane następujące polecenia cmdlet programu PowerShell:

| Polecenie | Uwagi |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Pobiera sieć wirtualną w grupie zasobów. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Dodaje konfigurację podsieci do sieci wirtualnej. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Aktualizuje sieć wirtualną.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Pobiera podsieć w sieci wirtualnej. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Tworzy publiczny adres IP.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Tworzy konfigurację adresu IP dla bramy sieci wirtualnej. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Tworzy bramę sieci wirtualnej. |

---


## <a name="connect-the-gateways"></a>Połącz bramy
W tym kroku utworzysz dwukierunkową opcję połączenia między dwiema bramami dwóch sieci wirtualnych. 


# <a name="portal"></a>[Portal](#tab/azure-portal)

Połącz dwie bramy przy użyciu Azure Portal. 


1. Wybierz pozycję **Utwórz zasób** na podstawie [Azure Portal](https://portal.azure.com).
1. Wpisz `connection` w polu wyszukiwania, a następnie naciśnij klawisz ENTER, aby przeszukać, co spowoduje przejście do zasobu **połączenia** opublikowanego przez firmę Microsoft.
1. Wybierz pozycję **Utwórz** , aby utworzyć połączenie. 
1. Na stronie **podstawowe** wybierz następujące wartości, a następnie wybierz przycisk **OK**. 
    1. Wybierz pozycję `VNet-to-VNet` dla **typu połączenia**. 
    1. Wybierz subskrypcję z listy rozwijanej. 
    1. Z listy rozwijanej wybierz grupę zasobów dla wystąpienia zarządzanego SQL. 
    1. Z listy rozwijanej wybierz lokalizację podstawowego wystąpienia zarządzanego. 
1. Na stronie **Ustawienia** wybierz lub wprowadź następujące wartości, a następnie wybierz przycisk **OK**:
    1. Wybierz bramę sieci podstawowej dla **pierwszej bramy sieci wirtualnej**, na przykład `primaryGateway` .  
    1. Wybierz bramę sieci pomocniczej dla **drugiej bramy sieci wirtualnej**, na przykład `secondaryGateway` . 
    1. Zaznacz pole wyboru obok pozycji **Ustanów łączność dwukierunkową**. 
    1. Pozostaw domyślną nazwę połączenia podstawowego lub Zmień nazwę na wybraną wartość. 
    1. Podaj **klucz współużytkowany (PSK)** dla połączenia, na przykład `mi1m2psk` . 
    1. Wybierz przycisk **OK**, aby zapisać ustawienia. 

    ![Utwórz połączenie bramy](./media/failover-group-add-instance-tutorial/create-gateway-connection.png)

    

1. Na stronie **Recenzja i tworzenie** Sprawdź ustawienia połączenia dwukierunkowego, a następnie wybierz przycisk **OK** , aby utworzyć połączenie. 


# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Połącz dwie bramy przy użyciu programu PowerShell. 

   ```powershell-interactive
   # Connect the primary to secondary gateway
   Write-host "Connecting the primary gateway to secondary gateway..."
   New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $resourceGroupName `
       -VirtualNetworkGateway1 $primaryGateway -VirtualNetworkGateway2 $secondaryGateway -Location $location `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $primaryGWConnection
   
   # Connect the secondary to primary gateway
   Write-host "Connecting the secondary gateway to primary gateway..."
   
   New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $resourceGroupName `
       -VirtualNetworkGateway1 $secondaryGateway -VirtualNetworkGateway2 $primaryGateway -Location $drLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $secondaryGWConnection
   ```

W tej części samouczka jest stosowane następujące polecenie cmdlet programu PowerShell:

| Polecenie | Uwagi |
|---|---|
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Tworzy połączenie między dwoma bramami sieci wirtualnej.   |

---


## <a name="create-a-failover-group"></a>Tworzenie grupy trybu failover
W tym kroku utworzysz grupę trybu failover i dodasz do niej oba wystąpienia zarządzane. 


# <a name="portal"></a>[Portal](#tab/azure-portal)
Utwórz grupę trybu failover przy użyciu Azure Portal. 


1. Wybierz pozycję **Azure SQL** w menu po lewej stronie [Azure Portal](https://portal.azure.com). Jeśli na liście nie ma **usługi Azure SQL** , wybierz pozycję **wszystkie usługi**, a następnie wpisz `Azure SQL` w polu wyszukiwania. Obowiązkowe Wybierz gwiazdkę obok pozycji **Azure SQL** , aby ją dodać do ulubionych, i Dodaj ją jako element w nawigacji po lewej stronie. 
1. Wybierz główne wystąpienie zarządzane utworzone w pierwszej sekcji, na przykład `sql-mi-primary` . 
1. W obszarze **Ustawienia**przejdź do pozycji **grupy trybu failover wystąpienia** , a następnie wybierz pozycję **Dodaj grupę** , aby otworzyć stronę **Grupa trybu failover wystąpienia** . 

   ![Dodawanie grupy trybu failover](./media/failover-group-add-instance-tutorial/add-failover-group.png)

1. Na stronie **Grupa trybu failover wystąpienia** wpisz nazwę grupy trybu failover, na przykład `failovergrouptutorial` . Następnie wybierz pomocnicze wystąpienie zarządzane, takie jak `sql-mi-secondary` , z listy rozwijanej. Wybierz pozycję **Utwórz** , aby utworzyć grupę trybu failover. 

   ![Utwórz grupę trybu failover](./media/failover-group-add-instance-tutorial/create-failover-group.png)

1. Po zakończeniu wdrażania grupy trybu failover nastąpi powrót do strony **grupy trybu failover** . 


# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
Utwórz grupę trybu failover przy użyciu programu PowerShell. 

   ```powershell-interactive
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $location -ResourceGroupName $resourceGroupName -PrimaryManagedInstanceName $primaryInstance `
        -PartnerRegion $drLocation -PartnerManagedInstanceName $secondaryInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```

W tej części samouczka jest stosowane następujące polecenie cmdlet programu PowerShell:

| Polecenie | Uwagi |
|---|---|
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Tworzy nową grupę trybu failover wystąpienia zarządzanego Azure SQL.  |


---


## <a name="test-failover"></a>Testowanie pracy w trybie failover
W tym kroku nastąpi niepowodzenie grupy trybu failover na serwerze pomocniczym, a następnie powrót po awarii przy użyciu Azure Portal. 


# <a name="portal"></a>[Portal](#tab/azure-portal)
Testowanie pracy w trybie failover przy użyciu Azure Portal. 


1. Przejdź do _pomocniczego_ wystąpienia zarządzanego w [Azure Portal](https://portal.azure.com) a następnie wybierz pozycję **grupy trybu failover dla wystąpienia** w obszarze Ustawienia. 
1. Sprawdź, które wystąpienie zarządzane jest podstawowym, a którym wystąpieniem zarządzanym jest pomocniczy. 
1. Wybierz pozycję **tryb failover** , a następnie wybierz pozycję **tak** na ostrzeżenie o rozłączeniu sesji tds. 

   ![Praca awaryjna grupy trybu failover](./media/failover-group-add-instance-tutorial/failover-mi-failover-group.png)

1. Sprawdź, które wystąpienie zarządzane jest podstawowym i jakie wystąpienie zarządzane jest pomocnicze. Jeśli przechodzenie do trybu failover zakończyło się pomyślnie, dwa wystąpienia powinny mieć przypisane role. 

   ![Wystąpienia zarządzane mają przełączane role po zakończeniu pracy w trybie failover](./media/failover-group-add-instance-tutorial/mi-switched-after-failover.png)

1. Przejdź do nowego _pomocniczego_ wystąpienia zarządzanego i ponownie wybierz **tryb failover** w celu niepowodzenia wystąpienia podstawowego z powrotem do roli podstawowej. 


# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
Testowanie pracy w trybie failover przy użyciu programu PowerShell. 

   ```powershell-interactive
    
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   
   # Fail over the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"
   ```


Przywróć grupę trybu failover z powrotem do serwera podstawowego:

   ```powershell-interactive
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName
   
   # Fail the primary managed instance back to the primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   ```

W tej części samouczka są stosowane następujące polecenia cmdlet programu PowerShell:

| Polecenie | Uwagi |
|---|---|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Pobiera lub wyświetla grupy trybu failover wystąpienia zarządzanego SQL.| 
| [Przełącznik-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Wykonuje tryb failover grupy trybu failover wystąpienia zarządzanego SQL. | 

---



## <a name="clean-up-resources"></a>Czyszczenie zasobów
Wyczyść zasoby, usuwając najpierw wystąpienia zarządzane, a następnie klaster wirtualny, wszystkie pozostałe zasoby i wreszcie grupę zasobów. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Przejdź do grupy zasobów w [Azure Portal](https://portal.azure.com). 
1. Wybierz wystąpienia zarządzane, a następnie wybierz pozycję **Usuń**. Wpisz `yes` w polu tekstowym, aby potwierdzić, że chcesz usunąć zasób, a następnie wybierz pozycję **Usuń**. Ten proces może potrwać trochę czasu w tle, a do momentu ukończenia nie będzie można usunąć *klastra wirtualnego* ani żadnych innych zasobów zależnych. Monitoruj usuwanie na karcie **działanie** , aby potwierdzić, że zarządzane wystąpienie zostało usunięte. 
1. Po usunięciu wystąpienia zarządzanego Usuń *klaster wirtualny* , wybierając go w grupie zasobów, a następnie wybierając pozycję **Usuń**. Wpisz `yes` w polu tekstowym, aby potwierdzić, że chcesz usunąć zasób, a następnie wybierz pozycję **Usuń**. 
1. Usuń wszystkie pozostałe zasoby. Wpisz `yes` w polu tekstowym, aby potwierdzić, że chcesz usunąć zasób, a następnie wybierz pozycję **Usuń**. 
1. Aby usunąć grupę zasobów, wybierz pozycję **Usuń grupę zasobów**, wpisz nazwę grupy zasobów, `myResourceGroup` a następnie wybierz pozycję **Usuń**. 

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Należy dwukrotnie usunąć grupę zasobów. Usunięcie grupy zasobów po raz pierwszy spowoduje usunięcie wystąpień zarządzanych i klastrów wirtualnych, a następnie zakończy się niepowodzeniem z komunikatem o błędzie `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'` . Uruchom polecenie Remove-AzResourceGroup po raz drugi, aby usunąć wszystkie zasoby pozostałe oraz grupę zasobów.

```powershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing SQL Managed Instance and virtual cluster..."
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing residual resources and resource group..."
```

W tej części samouczka jest stosowane następujące polecenie cmdlet programu PowerShell:

| Polecenie | Uwagi |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów. |

---

## <a name="full-script"></a>Pełny skrypt

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add SQL Managed Instance to a failover group")]

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tworzy grupę zasobów platformy Azure.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Tworzy sieć wirtualną.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Dodaje konfigurację podsieci do sieci wirtualnej. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Pobiera sieć wirtualną w grupie zasobów. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Pobiera podsieć w sieci wirtualnej. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Tworzy sieciową grupę zabezpieczeń. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Tworzy tabelę tras. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Aktualizuje konfigurację podsieci dla sieci wirtualnej.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Aktualizuje sieć wirtualną.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Pobiera sieciową grupę zabezpieczeń. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Dodaje konfigurację reguły zabezpieczeń sieci do sieciowej grupy zabezpieczeń. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Aktualizuje sieciową grupę zabezpieczeń.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Dodaje trasę do tabeli tras. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Aktualizuje tabelę tras.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Tworzy wystąpienie zarządzane.  |
| [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| Zwraca informacje na temat wystąpienia zarządzanego Azure SQL. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Tworzy publiczny adres IP.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Tworzy konfigurację adresu IP dla bramy sieci wirtualnej. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Tworzy bramę sieci wirtualnej. |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Tworzy połączenie między dwoma bramami sieci wirtualnej.   |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Tworzy nową grupę trybu failover wystąpienia zarządzanego SQL.  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Pobiera lub wyświetla grupy trybu failover wystąpienia zarządzanego SQL.| 
| [Przełącznik-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Wykonuje tryb failover grupy trybu failover wystąpienia zarządzanego SQL. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów. | 

# <a name="portal"></a>[Portal](#tab/azure-portal) 

Brak skryptów dostępnych dla Azure Portal.

---

## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurowano grupę trybu failover między dwoma wystąpieniami zarządzanymi. W tym samouczku omówiono:

> [!div class="checklist"]
> - Utwórz podstawowe wystąpienie zarządzane.
> - Utwórz dodatkowe wystąpienie zarządzane jako część [grupy trybu failover](../database/auto-failover-group-overview.md). 
> - Testowanie pracy w trybie failover.

Przejdź do następnego przewodnika Szybki Start dotyczącego sposobu nawiązywania połączenia z wystąpieniem zarządzanym SQL oraz sposobu przywracania bazy danych do wystąpienia zarządzanego SQL: 

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z wystąpieniem](connect-vm-instance-configure.md) 
>  zarządzanym SQL [Przywracanie bazy danych do wystąpienia zarządzanego SQL](restore-sample-database-quickstart.md)


