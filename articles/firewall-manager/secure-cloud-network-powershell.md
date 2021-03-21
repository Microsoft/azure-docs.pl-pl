---
title: 'Samouczek: Zabezpieczanie koncentratora wirtualnego przy użyciu Azure PowerShell'
description: W tym artykule opisano sposób tworzenia wirtualnej sieci WAN platformy Azure w jednym regionie, w której włączono Zaporę platformy Azure w centrum.
services: firewall-manager
author: jomore
ms.topic: tutorial
ms.service: firewall-manager
ms.date: 10/22/2020
ms.author: victorh
ms.openlocfilehash: 093b962bb9f42b660fb8cc5c0584ec67ebc87e48
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98789165"
---
# <a name="tutorial-secure-your-virtual-hub-using-azure-powershell"></a>Samouczek: Zabezpieczanie koncentratora wirtualnego przy użyciu Azure PowerShell

W tym samouczku utworzysz wirtualne wystąpienie sieci WAN z koncentratorem wirtualnym w jednym regionie i wdrożono zaporę platformy Azure w koncentratorze wirtualnym w celu zabezpieczenia łączności. W tym przykładzie przedstawiono bezpieczną łączność między sieciami wirtualnymi. Ruch między sieciami wirtualnymi i gałęziami lokacja-lokacja, punkt-lokacja lub ExpressRoute są obsługiwane przez wirtualne Centrum zabezpieczeń.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wdróż wirtualną sieć WAN
> * Wdrażanie zapory platformy Azure i Konfigurowanie routingu niestandardowego
> * Testowanie łączności

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Program PowerShell 7

   Ten samouczek wymaga uruchomienia Azure PowerShell lokalnie w programie PowerShell 7. Aby zainstalować program PowerShell 7, zobacz [Migrowanie z programu Windows PowerShell 5,1 do programu PowerShell 7](/powershell/scripting/install/migrating-from-windows-powershell-51-to-powershell-7?view=powershell-7).
- AZ. Network Version 3.2.0

    Jeśli masz polecenie AZ. Network w wersji 3.4.0 lub nowszej, musisz obniżyć wersję, aby użyć niektórych poleceń z tego samouczka. Możesz sprawdzić wersję modułu AZ. Network przy użyciu polecenia `Get-InstalledModule -Name Az.Network` . Aby odinstalować moduł AZ. Network, uruchom polecenie `Uninstall-Module -name az.network` . Aby zainstalować moduł AZ. Network 3.2.0, uruchom polecenie `Install-Module az.network -RequiredVersion 3.2.0 -force` .

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="initial-virtual-wan-deployment"></a>Początkowe wdrożenie wirtualnej sieci WAN

Najpierw należy ustawić pewne zmienne i utworzyć grupę zasobów, wirtualne wystąpienie sieci WAN i koncentrator wirtualny:

```azurepowershell
# Variable definition
$RG = "vwan-rg"
$Location = "westeurope"
$VwanName = "vwan"
$HubName =  "hub1"
# Create Resource Group, Virtual WAN and Virtual Hub
New-AzResourceGroup -Name $RG -Location $Location
$Vwan = New-AzVirtualWan -Name $VwanName -ResourceGroupName $RG -Location $Location -AllowVnetToVnetTraffic -AllowBranchToBranchTraffic -VirtualWANType "Standard"
$Hub = New-AzVirtualHub -Name $HubName -ResourceGroupName $RG -VirtualWan $Vwan -Location $Location -AddressPrefix "192.168.1.0/24" -Sku "Standard"
```

Utwórz dwie sieci wirtualne i połącz je z centrum jako szprychy:

```azurepowershell
# Create Virtual Network
$Spoke1 = New-AzVirtualNetwork -Name "spoke1" -ResourceGroupName $RG -Location $Location -AddressPrefix "10.1.1.0/24"
$Spoke2 = New-AzVirtualNetwork -Name "spoke2" -ResourceGroupName $RG -Location $Location -AddressPrefix "10.1.2.0/24"
# Connect Virtual Network to Virtual WAN
$Spoke1Connection = New-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke1" -RemoteVirtualNetwork $Spoke1
$Spoke2Connection = New-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke2" -RemoteVirtualNetwork $Spoke2
```

W tym momencie masz w pełni funkcjonalne wirtualne sieci WAN zapewniające dowolną łączność. Aby zwiększyć bezpieczeństwo, należy wdrożyć zaporę platformy Azure w każdym koncentratorze wirtualnym. Zasady zapory mogą służyć do wydajnego zarządzania wystąpieniem zapory platformy Azure dla wirtualnego sieci WAN. Zasady zapory są również tworzone w tym przykładzie:

```azurepowershell
# New Firewall Policy
$FWPolicy = New-AzFirewallPolicy -Name "VwanFwPolicy" -ResourceGroupName $RG -Location $Location
# New Firewall Public IP
$AzFWPIPs = New-AzFirewallHubPublicIpAddress -Count 1
$AzFWHubIPs = New-AzFirewallHubIpAddress -PublicIP $AzFWPIPs
# New Firewall
$AzFW = New-AzFirewall -Name "azfw1" -ResourceGroupName $RG -Location $Location `
            -VirtualHubId $Hub.Id -FirewallPolicyId $FWPolicy.Id `
            -Sku AZFW_Hub -HubIPAddress $AzFWHubIPs
```

Włączenie rejestrowania z zapory platformy Azure do Azure Monitor jest opcjonalne, ale w tym przykładzie używasz dzienników zapory, aby potwierdzić, że ruch przechodzi przez zaporę:

```azurepowershell
# Optionally, enable looging of Azure Firewall to Azure Monitor
$LogWSName = "vwan-" + (Get-Random -Maximum 99999) + "-" + $RG
$LogWS = New-AzOperationalInsightsWorkspace -Location $Location -Name $LogWSName -Sku Standard -ResourceGroupName $RG
Set-AzDiagnosticSetting -ResourceId $AzFW.Id -Enabled $True -Category AzureFirewallApplicationRule, AzureFirewallNetworkRule -WorkspaceId $LogWS.ResourceId
```

## <a name="deploy-azure-firewall-and-configure-custom-routing"></a>Wdrażanie zapory platformy Azure i Konfigurowanie routingu niestandardowego

Teraz masz zaporę platformy Azure w centrum, ale nadal musisz zmodyfikować Routing, aby wirtualne sieci WAN wysyłali ruch z sieci wirtualnych i z gałęzi za pośrednictwem zapory. Wykonaj tę czynność w dwóch krokach:

1. Skonfiguruj wszystkie połączenia sieci wirtualnej (i połączenia gałęzi, jeśli istnieją) do propagowania do `None` tabeli tras. Ta konfiguracja polega na tym, że inne sieci wirtualne i gałęzie nie będą uczyć się swoich prefiksów, a więc nie będą miały dostępu do nich.
1. Teraz można wstawiać trasy statyczne w `Default` tabeli tras (w przypadku których wszystkie sieci wirtualne i gałęzie są skojarzone domyślnie), dzięki czemu cały ruch jest wysyłany do zapory platformy Azure.

> [!NOTE]
> Jest to konfiguracja wdrożona w przypadku zabezpieczania łączności z poziomu witryny Azure Portal za pomocą Menedżera zapory platformy Azure

Rozpocznij od pierwszego kroku, aby skonfigurować połączenia sieci wirtualnej do propagowania do `None` tabeli tras:

```azurepowershell
# Configure Virtual Network connections in hub to propagate to None
$VnetRoutingConfig = $Spoke1Connection.RoutingConfiguration    # We take $Spoke1Connection as baseline for the future vnet config, all vnets will have an identical config
$NoneRT = Get-AzVhubRouteTable -ResourceGroupName $RG -HubName $HubName -Name "noneRouteTable"
$NewPropRT = @{}
$NewPropRT.Add('Id', $NoneRT.id)
$PropRTList = @()
$PropRTList += $NewPropRT
$VnetRoutingConfig.PropagatedRouteTables.Ids = $PropRTList
$VnetRoutingConfig.PropagatedRouteTables.Labels = @()
$Spoke1Connection = Update-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke1" -RoutingConfiguration $VnetRoutingConfig
$Spoke2Connection = Update-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke2" -RoutingConfiguration $VnetRoutingConfig
```

Teraz możesz przejść do drugiego kroku, aby dodać trasy statyczne do `Default` tabeli tras. W tym przykładzie stosowana jest konfiguracja domyślna, którą usługa Azure firewall Manager wygenerowała w przypadku zabezpieczania łączności w wirtualnej sieci WAN, ale można zmienić listę prefiksów w statycznej trasie zgodnie z określonymi wymaganiami:

```azurepowershell
# Create static routes in default Route table
$AzFWId = $(Get-AzVirtualHub -ResourceGroupName $RG -name  $HubName).AzureFirewall.Id
$AzFWRoute = New-AzVHubRoute -Name "private-traffic" -Destination @("0.0.0.0/0", "10.0.0.0/8", "172.16.0.0/12", "192.168.0.0/16") -DestinationType "CIDR" -NextHop $AzFWId -NextHopType "ResourceId"
$DefaultRT = Update-AzVHubRouteTable -Name "defaultRouteTable" -ResourceGroupName $RG -VirtualHubName  $HubName -Route @($AzFWRoute)
```

## <a name="test-connectivity"></a>Testowanie łączności

Teraz masz w pełni funkcjonalne centrum zabezpieczeń. Aby przetestować łączność, potrzebna jest jedna maszyna wirtualna w każdej sieci wirtualnej szprych podłączonej do centrum:

```azurepowershell
# Create VMs in spokes for testing
$VMLocalAdminUser = "lab-user"
$VMLocalAdminSecurePassword = ConvertTo-SecureString -AsPlainText -Force
$VMCredential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);
$VMSize = "Standard_B2ms"
# Spoke1
$Spoke1 = Get-AzVirtualNetwork -ResourceGroupName $RG -Name "spoke1"
Add-AzVirtualNetworkSubnetConfig -Name "vm" -VirtualNetwork $Spoke1 -AddressPrefix "10.1.1.0/26"
$Spoke1 | Set-AzVirtualNetwork
$VM1 = New-AzVM -Name "spoke1-vm" -ResourceGroupName $RG -Location $Location `
            -Image "UbuntuLTS" -credential $VMCredential `
            -VirtualNetworkName "spoke1" -SubnetName "vm" -PublicIpAddressName "spoke1-pip"
$NIC1 = Get-AzNetworkInterface -ResourceId $($VM1.NetworkProfile.NetworkInterfaces[0].Id)
$Spoke1VMPrivateIP = $NIC1.IpConfigurations[0].PrivateIpAddress
$Spoke1VMPIP = $(Get-AzPublicIpAddress -ResourceGroupName $RG -Name "spoke1-pip")
# Spoke2
$Spoke2 = Get-AzVirtualNetwork -ResourceGroupName $RG -Name "spoke2"
Add-AzVirtualNetworkSubnetConfig -Name "vm" -VirtualNetwork $Spoke2 -AddressPrefix "10.1.2.0/26"
$Spoke2 | Set-AzVirtualNetwork
$VM2 = New-AzVM -Name "spoke2-vm" -ResourceGroupName $RG -Location $Location `
            -Image "UbuntuLTS" -credential $VMCredential `
            -VirtualNetworkName "spoke2" -SubnetName "vm" -PublicIpAddressName "spoke2-pip"
$NIC2 = Get-AzNetworkInterface -ResourceId $($VM2.NetworkProfile.NetworkInterfaces[0].Id)
$Spoke2VMPrivateIP = $NIC2.IpConfigurations[0].PrivateIpAddress
$Spoke2VMPIP = $(Get-AzPublicIpAddress -ResourceGroupName $RG -Name "spoke2-pip")
```

Domyślną konfiguracją w zasadach zapory jest porzucenie wszystkiego. W związku z tym należy skonfigurować niektóre reguły. Rozpocznij od reguł DNAT, aby testowe maszyny wirtualne były dostępne za pośrednictwem publicznego adresu IP zapory:

```azurepowershell
# Adding DNAT rules for virtual machines in the spokes
$AzFWPublicAddress = $AzFW.HubIPAddresses.PublicIPs.Addresses[0].Address
$NATRuleSpoke1 = New-AzFirewallPolicyNatRule -Name "Spoke1SSH" -Protocol "TCP" `
        -SourceAddress "*" -DestinationAddress $AzFWPublicAddress -DestinationPort 10001 `
        -TranslatedAddress $Spoke1VMPrivateIP -TranslatedPort 22
$NATRuleSpoke2 = New-AzFirewallPolicyNatRule -Name "Spoke2SSH" -Protocol "TCP" `
        -SourceAddress "*" -DestinationAddress $AzFWPublicAddress -DestinationPort 10002 `
        -TranslatedAddress $Spoke2VMPrivateIP -TranslatedPort 22
$NATCollection = New-AzFirewallPolicyNatRuleCollection -Name "SSH" -Priority 100 `
        -Rule @($NATRuleSpoke1, $NATRuleSpoke2) -ActionType "Dnat"
$NATGroup = New-AzFirewallPolicyRuleCollectionGroup -Name "NAT" -Priority 100 -RuleCollection $NATCollection -FirewallPolicyObject $FWPolicy
```

Teraz można skonfigurować kilka przykładowych reguł. Zdefiniuj regułę sieci, która zezwala na ruch SSH oraz regułę aplikacji, która umożliwia dostęp do Internetu do w pełni kwalifikowanej nazwy domeny `ifconfig.co` . Ten adres URL zwraca źródłowy adres IP, który jest widoczny w żądaniu HTTP:

```azurepowershell
# Add Network Rule
$SSHRule = New-AzFirewallPolicyNetworkRule -Name PermitSSH -Protocol TCP `
        -SourceAddress "10.0.0.0/8" -DestinationAddress "10.0.0.0/8" -DestinationPort 22
$NetCollection = New-AzFirewallPolicyFilterRuleCollection -Name "Management" -Priority 100 -ActionType Allow -Rule $SSHRule
$NetGroup = New-AzFirewallPolicyRuleCollectionGroup -Name "Management" -Priority 200 -RuleCollection $NetCollection -FirewallPolicyObject $FWPolicy
# Add Application Rul
$ifconfigRule = New-AzFirewallPolicyApplicationRule -Name PermitIfconfig -SourceAddress "10.0.0.0/8" -TargetFqdn "ifconfig.co" -Protocol "http:80","https:443"
$AppCollection = New-AzFirewallPolicyFilterRuleCollection -Name "TargetURLs" -Priority 300 -ActionType Allow -Rule $ifconfigRule
$NetGroup = New-AzFirewallPolicyRuleCollectionGroup -Name "TargetURLs" -Priority 300 -RuleCollection $AppCollection -FirewallPolicyObject $FWPolicy
```

Przed faktycznym wysłaniem ruchu można sprawdzić efektywne trasy maszyn wirtualnych. Powinny one zawierać prefiksy uzyskane z wirtualnej sieci WAN ( `0.0.0.0/0` Plus RFC1918), ale nie prefiks innych szprych:

```azurepowershell
# Check effective routes in the VM NIC in spoke 1
# Note that 10.1.2.0/24 (the prefix for spoke2) should not appear
Get-AzEffectiveRouteTable -ResourceGroupName $RG -NetworkInterfaceName $NIC1.Name | ft
# Check effective routes in the VM NIC in spoke 2
# Note that 10.1.1.0/24 (the prefix for spoke1) should not appear
Get-AzEffectiveRouteTable -ResourceGroupName $RG -NetworkInterfaceName $NIC2.Name | ft
```

Teraz Generuj ruch między maszynami wirtualnymi i sprawdź, czy został on porzucony w zaporze platformy Azure. W poniższych poleceniach SSH musisz zaakceptować odciski palca maszyn wirtualnych, a następnie podać hasło zdefiniowane podczas tworzenia maszyn wirtualnych. W tym przykładzie zamierzasz wysłać pięć pakietów żądań echa protokołu ICMP z maszyny wirtualnej w spoke1 do spoke2, a następnie połączenie TCP na porcie 22 przy użyciu narzędzia systemu Linux `nc` (z `-vz` flagami po prostu wysyła żądanie połączenia i wyświetla wynik). Powinien zostać wyświetlony komunikat ping zakończony niepowodzeniem i próba nawiązania połączenia TCP w porcie 22 kończy się powodzeniem, ponieważ jest to dozwolone przez skonfigurowaną wcześniej regułę sieci:

```azurepowershell
# Connect to one VM and ping the other. It shouldnt work, because the firewall should drop the traffic, since no rule for ICMP is configured
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "ping $Spoke2VMPrivateIP -c 5"
# Connect to one VM and send a TCP request on port 22 to the other. It should work, because the firewall is configured to allow SSH traffic (port 22)
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "nc -vz $Spoke2VMPrivateIP 22"
```

Możesz również zweryfikować ruch internetowy. Żądania HTTP za pomocą narzędzia `curl` do nazwy FQDN dozwolonej w zasadach zapory ( `ifconfig.co` ) powinny działać, ale żądania HTTP do innych miejsc docelowych powinny kończyć się niepowodzeniem (w tym przykładzie można testować za pomocą `bing.com` ):

```azurepowershell
# This HTTP request should succeed, since it is allowed in an app rule in the AzFW, and return the public IP of the FW
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "curl -s4 ifconfig.co"
# This HTTP request should fail, since the FQDN bing.com is not in any app rule in the firewall policy
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "curl -s4 bing.com"
```

Najprostszym sposobem sprawdzenia, czy pakiety są porzucane przez zaporę, jest sprawdzenie dzienników. Ponieważ skonfigurowano zaporę platformy Azure w celu wysyłania dzienników do Azure Monitor, można użyć języka zapytań Kusto do pobrania odpowiednich dzienników z Azure Monitor:

> [!NOTE]
> Aby dzienniki były wysyłane do Azure Monitor, może upłynąć około 1 minuty.

```azurepowershell
# Getting Azure Firewall network rule Logs
$LogWS = Get-AzOperationalInsightsWorkspace -ResourceGroupName $RG
$LogQuery = 'AzureDiagnostics 
| where Category == "AzureFirewallNetworkRule" 
| where TimeGenerated >= ago(5m) 
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " to " TargetIP ":" TargetPortInt:int * 
| parse msg_s with * ". Action: " Action1a 
| parse msg_s with * " was " Action1b " to " NatDestination 
| parse msg_s with Protocol2 " request from " SourceIP2 " to " TargetIP2 ". Action: " Action2 
| extend SourcePort = tostring(SourcePortInt),TargetPort = tostring(TargetPortInt) 
| extend Action = case(Action1a == "", case(Action1b == "",Action2,Action1b), Action1a),Protocol = case(Protocol == "", Protocol2, Protocol),SourceIP = case(SourceIP == "", SourceIP2, SourceIP),TargetIP = case(TargetIP == "", TargetIP2, TargetIP),SourcePort = case(SourcePort == "", "N/A", SourcePort),TargetPort = case(TargetPort == "", "N/A", TargetPort),NatDestination = case(NatDestination == "", "N/A", NatDestination) 
| project TimeGenerated, Protocol, SourceIP,SourcePort,TargetIP,TargetPort,Action, NatDestination, Resource 
| take 25 '
$(Invoke-AzOperationalInsightsQuery -Workspace $LogWS -Query $LogQuery).Results | ft
```

W poprzednim poleceniu powinny zostać wyświetlone różne wpisy:

* Twoje połączenie SSH DNAT'ed
* Pakiety ICMP porzucone między maszynami wirtualnymi w szprychach (10.1.1.4 i 10.1.2.4)
* Dozwolone połączenia SSH między maszynami wirtualnymi w szprychach

Oto przykładowe dane wyjściowe generowane przez polecenie powyżej:

```
TimeGenerated            Protocol    SourceIP       SourcePort TargetIP      TargetPort Action  NatDestination Resource
-------------            --------    --------       ---------- --------      ---------- ------  -------------- --------
2020-10-04T20:53:02.41Z  TCP         109.125.122.99 62281      51.105.224.44 10001      DNAT'ed 10.1.1.4:22    AZFW1
2020-10-04T20:53:07.045Z TCP         10.1.1.4       35932      10.1.2.4      22         Allow   N/A            AZFW1
2020-10-04T20:53:50.119Z TCP         109.125.122.99 62293      51.105.224.44 10001      DNAT'ed 10.1.2.4:22    AZFW1
2020-10-04T20:52:47.475Z TCP         109.125.122.99 62273      51.105.224.44 10001      DNAT'ed 10.1.2.4:22    AZFW1
2020-10-04T20:51:04.682Z TCP         109.125.122.99 62200      51.105.224.44 10001      DNAT'ed 10.1.2.4:22    AZFW1
2020-10-04T20:51:17.031Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:18.049Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:19.075Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:20.097Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:21.121Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:52:52.356Z TCP         10.1.1.4       53748      10.1.2.4      22         Allow   N/A            AZFW1
```

Jeśli chcesz zobaczyć dzienniki dla reguł aplikacji (opis dozwolonych i zabronionych połączeń HTTP) lub zmienić sposób wyświetlania dzienników, możesz spróbować wykonać inne zapytania KQL. Przykłady można znaleźć w [dziennikach Azure monitor dla zapory platformy Azure](../firewall/firewall-workbook.md).


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby usunąć środowisko testowe, można usunąć grupę zasobów ze wszystkimi zawartymi obiektami:

```azurepowershell
# Delete resource group and all contained resources
Remove-AzResourceGroup -Name $RG
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Informacje o zaufanych partnerach zabezpieczeń](trusted-security-partners.md)