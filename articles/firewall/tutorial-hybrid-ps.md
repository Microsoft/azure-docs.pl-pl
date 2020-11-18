---
title: Wdrażanie & konfigurowania zapory platformy Azure w sieci hybrydowej przy użyciu programu PowerShell
description: W tym artykule dowiesz się, jak wdrożyć i skonfigurować zaporę platformy Azure przy użyciu Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 08/28/2020
ms.author: victorh
customer intent: As an administrator, I want to control network access from an on-premises network to an Azure virtual network.
ms.openlocfilehash: e60c829831bde3b454ab180d1a39ec46cb346963
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658659"
---
# <a name="deploy-and-configure-azure-firewall-in-a-hybrid-network-using-azure-powershell"></a>Wdrażanie i konfigurowanie usługi Azure Firewall w sieci hybrydowej za pomocą programu Azure PowerShell

W przypadku łączenia sieci lokalnej z siecią wirtualną platformy Azure w celu utworzenia sieci hybrydowej ważną częścią ogólnego planu zabezpieczeń jest możliwość kontrolowania dostępu do zasobów sieciowych platformy Azure.

Aby kontrolować dostęp do sieci hybrydowej korzystającej z reguł, które definiują dozwolony i zabroniony ruch sieciowy, możesz użyć usługi Azure Firewall.

W tym artykule opisano tworzenie trzech sieci wirtualnych:

- **VNet-Hub** — w tej sieci wirtualnej znajduje się zapora.
- **VNet-Spoke** — sieć wirtualna będąca szprychą reprezentuje pakiet roboczy na platformie Azure.
- **VNet-Onprem** — lokalna sieć wirtualna reprezentuje sieć lokalną. W rzeczywistym wdrożeniu może ono być połączone przez połączenie sieci VPN lub ExpressRoute. Dla uproszczenia w tym artykule jest używane połączenie z bramą sieci VPN, a sieć wirtualna z systemem Azure jest używana do reprezentowania sieci lokalnej.

![Zapora w sieci hybrydowej](media/tutorial-hybrid-ps/hybrid-network-firewall.png)

W tym artykule omówiono sposób wykonywania następujących zadań:

* Deklarowanie zmiennych
* Tworzenie sieci wirtualnej koncentratora zapory
* Tworzenie sieci wirtualnej będącej szprychą
* Tworzenie lokalnej sieci wirtualnej
* Konfigurowanie i wdrażanie zapory
* Tworzenie i łączenie bram sieci VPN
* Komunikacja równorzędna pomiędzy sieciami wirtualnymi koncentratora i szprychy
* Tworzenie tras
* Tworzenie maszyn wirtualnych
* Testowanie zapory

Jeśli chcesz użyć Azure Portal zamiast tego samouczka, zobacz [Samouczek: wdrażanie i Konfigurowanie zapory platformy Azure w sieci hybrydowej przy użyciu Azure Portal](tutorial-hybrid-portal.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Ten artykuł wymaga lokalnego uruchomienia programu PowerShell. Musisz mieć zainstalowany moduł Azure PowerShell. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps). Po zweryfikowaniu wersji programu PowerShell uruchom polecenie `Login-AzAccount`, aby utworzyć połączenie z platformą Azure.

Aby ten scenariusz przebiegał prawidłowo, muszą zostać spełnione trzy podstawowe wymagania:

- Trasa zdefiniowana przez użytkownika w podsieci będącej szprychą, która wskazuje adres IP usługi Azure Firewall jako bramę domyślną. Propagacja trasy bramy sieci wirtualnej musi być **wyłączona** w tej tabeli tras.
- Trasa zdefiniowana przez użytkownika w podsieci bramy koncentratora musi wskazywać adres IP zapory jako następny przeskok do sieci będącej szprychą.

   W podsieci usługi Azure Firewall nie jest wymagana trasa zdefiniowana przez użytkownika, ponieważ uzyskuje ona informacje o trasach na podstawie protokołu BGP.
- Ustaw wartość **AllowGatewayTransit** na potrzeby komunikacji równorzędnej między sieciami VNet-Hub i VNet-Spoke oraz **UseRemoteGateways** na potrzeby komunikacji równorzędnej VNet-Spoke i VNet-Hub.

Zapoznaj się z sekcją [Tworzenie tras](#create-the-routes) w tym artykule, aby zobaczyć, jak te trasy zostały utworzone.

>[!NOTE]
>Usługa Azure Firewall musi mieć bezpośrednie połączenie z Internetem. Jeśli AzureFirewallSubnet nauczy trasy domyślnej do sieci lokalnej za pośrednictwem protokołu BGP, należy przesłonić ten element przy użyciu wartości 0.0.0.0/0 UDR z wartością **NextHopType** ustawioną jako **Internet** w celu utrzymania bezpośredniej łączności z Internetem.
>
>Zaporę platformy Azure można skonfigurować do obsługi wymuszonego tunelowania. Aby uzyskać więcej informacji, zobacz [tunelowanie wymuszone przez zaporę platformy Azure](forced-tunneling.md).

>[!NOTE]
>Ruch między wirtualnymi sieciami równorzędnymi połączonymi bezpośrednio jest kierowany bezpośrednio nawet wtedy, gdy trasa zdefiniowana przez użytkownika wskazuje usługę Azure Firewall jako bramę domyślną. Aby w tym scenariuszu wysyłać ruch między podsieciami do zapory, trasa zdefiniowana przez użytkownika musi jawnie zawierać prefiks podsieci docelowej w obu podsieciach.

Aby zapoznać się z powiązaną dokumentacją programu Azure PowerShell, zobacz [dokumentacja programu Azure PowerShell](/powershell/module/az.network/new-azfirewall).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="declare-the-variables"></a>Deklarowanie zmiennych

Poniższy przykład deklaruje zmienne przy użyciu wartości w tym artykule. W pewnych przypadkach może być konieczne zastąpienie niektórych wartości Twoimi własnymi wartościami umożliwiającymi pracę w ramach subskrypcji. Jeśli jest taka konieczność, zmodyfikuj zmienne, a następnie skopiuj je i wklej do konsoli programu PowerShell.

```azurepowershell
$RG1 = "FW-Hybrid-Test"
$Location1 = "East US"

# Variables for the firewall hub VNet

$VNetnameHub = "VNet-hub"
$SNnameHub = "AzureFirewallSubnet"
$VNetHubPrefix = "10.5.0.0/16"
$SNHubPrefix = "10.5.0.0/24"
$SNGWHubPrefix = "10.5.1.0/24"
$GWHubName = "GW-hub"
$GWHubpipName = "VNet-hub-GW-pip"
$GWIPconfNameHub = "GW-ipconf-hub"
$ConnectionNameHub = "hub-to-Onprem"

# Variables for the spoke virtual network

$VnetNameSpoke = "VNet-Spoke"
$SNnameSpoke = "SN-Workload"
$VNetSpokePrefix = "10.6.0.0/16"
$SNSpokePrefix = "10.6.0.0/24"
$SNSpokeGWPrefix = "10.6.1.0/24"

# Variables for the on-premises virtual network

$VNetnameOnprem = "Vnet-Onprem"
$SNNameOnprem = "SN-Corp"
$VNetOnpremPrefix = "192.168.0.0/16"
$SNOnpremPrefix = "192.168.1.0/24"
$SNGWOnpremPrefix = "192.168.2.0/24"
$GWOnpremName = "GW-Onprem"
$GWIPconfNameOnprem = "GW-ipconf-Onprem"
$ConnectionNameOnprem = "Onprem-to-hub"
$GWOnprempipName = "VNet-Onprem-GW-pip"

$SNnameGW = "GatewaySubnet"
```


## <a name="create-the-firewall-hub-virtual-network"></a>Tworzenie sieci wirtualnej koncentratora zapory

Najpierw utwórz grupę zasobów zawierającą zasoby z tego artykułu:

```azurepowershell
  New-AzResourceGroup -Name $RG1 -Location $Location1
  ```

Zdefiniuj podsieci, które mają być dołączone do sieci wirtualnej:

```azurepowershell
$FWsub = New-AzVirtualNetworkSubnetConfig -Name $SNnameHub -AddressPrefix $SNHubPrefix
$GWsub = New-AzVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWHubPrefix
```

Teraz utwórz sieć wirtualną koncentratora zapory:

```azurepowershell
$VNetHub = New-AzVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetHubPrefix -Subnet $FWsub,$GWsub
```

Zażądaj przydzielenia publicznego adresu IP do bramy sieci VPN, którą utworzysz dla sieci wirtualnej. Należy zauważyć, że metoda *AllocationMethod* jest **dynamiczna**. Nie można określić adresu IP, którego chcesz użyć. Jest on przydzielany dynamicznie do bramy sieci VPN.

  ```azurepowershell
  $gwpip1 = New-AzPublicIpAddress -Name $GWHubpipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="create-the-spoke-virtual-network"></a>Tworzenie sieci wirtualnej będącej szprychą

Zdefiniuj podsieci, które mają być dołączone do sieci wirtualnej będącej szprychą:

```azurepowershell
$Spokesub = New-AzVirtualNetworkSubnetConfig -Name $SNnameSpoke -AddressPrefix $SNSpokePrefix
$GWsubSpoke = New-AzVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNSpokeGWPrefix
```

Utwórz sieć wirtualną będącą szprychą:

```azurepowershell
$VNetSpoke = New-AzVirtualNetwork -Name $VnetNameSpoke -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetSpokePrefix -Subnet $Spokesub,$GWsubSpoke
```

## <a name="create-the-on-premises-virtual-network"></a>Tworzenie lokalnej sieci wirtualnej

Zdefiniuj podsieci, które mają być dołączone do sieci wirtualnej:

```azurepowershell
$Onpremsub = New-AzVirtualNetworkSubnetConfig -Name $SNNameOnprem -AddressPrefix $SNOnpremPrefix
$GWOnpremsub = New-AzVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWOnpremPrefix
```

Teraz utwórz lokalną sieć wirtualną:

```azurepowershell
$VNetOnprem = New-AzVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetOnpremPrefix -Subnet $Onpremsub,$GWOnpremsub
```

Zażądaj przydzielenia publicznego adresu IP do bramy, która zostanie utworzona dla sieci wirtualnej. Należy zauważyć, że metoda *AllocationMethod* jest **dynamiczna**. Nie można określić adresu IP, którego chcesz użyć. Jest on przydzielany dynamicznie do bramy.

  ```azurepowershell
  $gwOnprempip = New-AzPublicIpAddress -Name $GWOnprempipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="configure-and-deploy-the-firewall"></a>Konfigurowanie i wdrażanie zapory

Teraz należy wdrożyć zaporę w sieci wirtualnej koncentratora.

```azurepowershell
# Get a Public IP for the firewall
$FWpip = New-AzPublicIpAddress -Name "fw-pip" -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Static -Sku Standard
# Create the firewall
$Azfw = New-AzFirewall -Name AzFW01 -ResourceGroupName $RG1 -Location $Location1 -VirtualNetworkName $VNetnameHub -PublicIpName fw-pip

#Save the firewall private IP address for future use

$AzfwPrivateIP = $Azfw.IpConfigurations.privateipaddress
$AzfwPrivateIP

```

### <a name="configure-network-rules"></a>Konfigurowanie reguł sieci

<!--- $Rule3 = New-AzFirewallNetworkRule -Name "AllowPing" -Protocol ICMP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort *--->

```azurepowershell
$Rule1 = New-AzFirewallNetworkRule -Name "AllowWeb" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 80

$Rule2 = New-AzFirewallNetworkRule -Name "AllowRDP" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 3389

$NetRuleCollection = New-AzFirewallNetworkRuleCollection -Name RCNet01 -Priority 100 `
   -Rule $Rule1,$Rule2 -ActionType "Allow"
$Azfw.NetworkRuleCollections = $NetRuleCollection
Set-AzFirewall -AzureFirewall $Azfw
```

## <a name="create-and-connect-the-vpn-gateways"></a>Tworzenie i łączenie bram sieci VPN

Sieć wirtualna koncentratora i lokalna sieć wirtualna są połączone za pośrednictwem bram sieci VPN.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>Tworzenie bramy sieci VPN dla sieci wirtualnej koncentratora

Utwórz konfigurację bramy sieci VPN. W ramach konfiguracji bramy sieci VPN zostaje zdefiniowana podsieć i publiczny adres IP do użycia.

  ```azurepowershell
  $vnet1 = Get-AzVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1
  $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
  $gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfNameHub `
  -Subnet $subnet1 -PublicIpAddress $gwpip1
  ```

Teraz utwórz bramę sieci VPN dla sieci wirtualnej koncentratora. Konfiguracje połączeń między sieciami wymagają zastosowania wartości RouteBased obiektu VpnType. Tworzenie bramy sieci VPN może potrwać 45 minut lub dłużej, w zależności od wybranej jednostki SKU bramy sieci VPN.

```azurepowershell
New-AzVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>Tworzenie bramy sieci VPN dla lokalnej sieci wirtualnej

Utwórz konfigurację bramy sieci VPN. W ramach konfiguracji bramy sieci VPN zostaje zdefiniowana podsieć i publiczny adres IP do użycia.

  ```azurepowershell
$vnet2 = Get-AzVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1
$subnet2 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfNameOnprem `
  -Subnet $subnet2 -PublicIpAddress $gwOnprempip
  ```

Teraz utwórz bramę sieci VPN dla lokalnej sieci wirtualnej. Konfiguracje połączeń między sieciami wymagają zastosowania wartości RouteBased obiektu VpnType. Tworzenie bramy sieci VPN może potrwać 45 minut lub dłużej, w zależności od wybranej jednostki SKU bramy sieci VPN.

```azurepowershell
New-AzVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf2 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-the-vpn-connections"></a>Tworzenie połączeń sieci VPN

Teraz możesz utworzyć połączenia sieci VPN pomiędzy bramami sieci koncentratora i sieci lokalnej

#### <a name="get-the-vpn-gateways"></a>Uzyskiwanie bram sieci VPN

```azurepowershell
$vnetHubgw = Get-AzVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1
$vnetOnpremgw = Get-AzVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1
```

#### <a name="create-the-connections"></a>Tworzenie połączeń

W tym kroku utworzysz połączenie z sieci wirtualnej koncentratora do lokalnej sieci wirtualnej. W przykładach zastosowano odwołania do klucza współużytkowanego. Możesz wybrać własne wartości dla klucza współużytkowanego. Ważne jest, aby klucz współużytkowany był zgodny z obydwoma połączeniami. Tworzenie połączenia może nieco potrwać.

```azurepowershell
New-AzVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1 `
-VirtualNetworkGateway1 $vnetHubgw -VirtualNetworkGateway2 $vnetOnpremgw -Location $Location1 `
-ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
```
Utwórz połączenie z lokalnej sieci wirtualnej do sieci wirtualnej koncentratora. Ten krok jest podobny do poprzedniego, jednak w tym przypadku tworzysz połączenie z sieci VNet-Onprem do sieci VNet-hub. Upewnij się, że klucze współużytkowane są zgodne. Po kilku minutach połączenie zostanie ustanowione.

  ```azurepowershell
  New-AzVirtualNetworkGatewayConnection -Name $ConnectionNameOnprem -ResourceGroupName $RG1 `
  -VirtualNetworkGateway1 $vnetOnpremgw -VirtualNetworkGateway2 $vnetHubgw -Location $Location1 `
  -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```

#### <a name="verify-the-connection"></a>Weryfikowanie połączenia

Pomyślne połączenie można sprawdzić za pomocą polecenia cmdlet *Get-AzVirtualNetworkGatewayConnection* z opcją *Debug* lub bez niego. Można skorzystać z następującego przykładu użycia polecenia cmdlet, dopasowując wartości do własnych potrzeb. Jeśli pojawi się monit, wybierz **A**, aby uruchomić **Wszystko**. W podanym przykładzie *-name* odnosi się do nazwy połączenia, które ma zostać przetestowane.

```azurepowershell
Get-AzVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1
```

Po zakończeniu działania polecenia cmdlet sprawdź wartości. W następującym przykładzie stan połączenia jest wyświetlany jako *Połączone* i można zobaczyć bajty przychodzące i wychodzące.

```
"connectionStatus": "Connected",
"ingressBytesTransferred": 33509044,
"egressBytesTransferred": 4142431
```

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>Komunikacja równorzędna pomiędzy sieciami wirtualnymi koncentratora i szprychy

Teraz nawiąż komunikację równorzędną pomiędzy siecią wirtualną koncentratora i siecią wirtualną będącą szprychą.

```azurepowershell
# Peer hub to spoke
Add-AzVirtualNetworkPeering -Name HubtoSpoke -VirtualNetwork $VNetHub -RemoteVirtualNetworkId $VNetSpoke.Id -AllowGatewayTransit

# Peer spoke to hub
Add-AzVirtualNetworkPeering -Name SpoketoHub -VirtualNetwork $VNetSpoke -RemoteVirtualNetworkId $VNetHub.Id -AllowForwardedTraffic -UseRemoteGateways
```

## <a name="create-the-routes"></a>Tworzenie tras

Następnie należy utworzyć kilka tras:

- Trasa z podsieci bramy koncentratora do podsieci będącej szprychą za pośrednictwem adresu IP zapory
- Trasa domyślna z podsieci będącej szprychą za pośrednictwem adresu IP zapory

```azurepowershell
#Create a route table
$routeTableHubSpoke = New-AzRouteTable `
  -Name 'UDR-Hub-Spoke' `
  -ResourceGroupName $RG1 `
  -location $Location1

#Create a route
Get-AzRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-Hub-Spoke `
  | Add-AzRouteConfig `
  -Name "ToSpoke" `
  -AddressPrefix $VNetSpokePrefix `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzRouteTable

#Associate the route table to the subnet

Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetHub `
  -Name $SNnameGW `
  -AddressPrefix $SNGWHubPrefix `
  -RouteTable $routeTableHubSpoke | `
Set-AzVirtualNetwork

#Now create the default route

#Create a table, with BGP route propagation disabled. The property is now called "Virtual network gateway route propagation," but the API still refers to the parameter as "DisableBgpRoutePropagation."
$routeTableSpokeDG = New-AzRouteTable `
  -Name 'UDR-DG' `
  -ResourceGroupName $RG1 `
  -location $Location1 `
  -DisableBgpRoutePropagation

#Create a route
Get-AzRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-DG `
  | Add-AzRouteConfig `
  -Name "ToFirewall" `
  -AddressPrefix 0.0.0.0/0 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzRouteTable

#Associate the route table to the subnet

Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetSpoke `
  -Name $SNnameSpoke `
  -AddressPrefix $SNSpokePrefix `
  -RouteTable $routeTableSpokeDG | `
Set-AzVirtualNetwork
```

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Teraz utwórz maszyny wirtualne pakietu roboczego szprychy i sieci lokalnej, a następnie umieść je w odpowiednich podsieciach.

### <a name="create-the-workload-virtual-machine"></a>Tworzenie maszyny wirtualnej pakietu roboczego

Utwórz maszynę wirtualną w sieci wirtualnej będącej szprychą z uruchomionymi usługami IIS, bez publicznego adresu IP i zezwól na wysyłanie poleceń ping.
Po wyświetleniu monitu wpisz nazwę użytkownika i hasło maszyny wirtualnej.

```azurepowershell
# Create an inbound network security group rule for ports 3389 and 80
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name Allow-RDP  -Protocol Tcp `
  -Direction Inbound -Priority 200 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 3389 -Access Allow
$nsgRuleWeb = New-AzNetworkSecurityRuleConfig -Name Allow-web  -Protocol Tcp `
  -Direction Inbound -Priority 202 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $RG1 -Location $Location1 -Name NSG-Spoke02 -SecurityRules $nsgRuleRDP,$nsgRuleWeb

#Create the NIC
$NIC = New-AzNetworkInterface -Name spoke-01 -ResourceGroupName $RG1 -Location $Location1 -SubnetId $VnetSpoke.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id

#Define the virtual machine
$VirtualMachine = New-AzVMConfig -VMName VM-Spoke-01 -VMSize "Standard_DS2"
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName Spoke-01 -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2016-Datacenter' -Version latest

#Create the virtual machine
New-AzVM -ResourceGroupName $RG1 -Location $Location1 -VM $VirtualMachine -Verbose

#Install IIS on the VM
Set-AzVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server"}' `
    -Location $Location1
```

<!---#Create a host firewall rule to allow ping in
Set-AzVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4"}' `
    -Location $Location1--->

### <a name="create-the-on-premises-virtual-machine"></a>Tworzenie maszyny wirtualnej w środowisku lokalnym

Jest to prosta maszyna wirtualna, za pomocą której można połączyć się z publicznym adresem IP przy użyciu pulpitu zdalnego. Z tego miejsca nawiążesz następnie połączenie z serwerem lokalnym za pośrednictwem zapory. Po wyświetleniu monitu wpisz nazwę użytkownika i hasło maszyny wirtualnej.

```azurepowershell
New-AzVm `
    -ResourceGroupName $RG1 `
    -Name "VM-Onprem" `
    -Location $Location1 `
    -VirtualNetworkName $VNetnameOnprem `
    -SubnetName $SNNameOnprem `
    -OpenPorts 3389 `
    -Size "Standard_DS2"
```

## <a name="test-the-firewall"></a>Testowanie zapory

Najpierw pobierz, a następnie zanotuj prywatny adres IP dla maszyny wirtualnej **VM-spoke-01**.

```azurepowershell
$NIC.IpConfigurations.privateipaddress
```

W witrynie Azure Portal połącz się z maszyną wirtualną **VM-Onprem**.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
Otwórz przeglądarkę internetową w obszarze **VM-lokalnego**, a następnie przejdź do http:// \<VM-spoke-01 private IP\> .

Powinna zostać wyświetlona domyślna strona usług Internet Information Services.

Z poziomu maszyny **VM-Onprem**, otwórz pulpit zdalny na maszynie **VM-spoke-01** przy użyciu prywatnego adresu IP.

Połączenie powinno zostać nawiązane pomyślnie, a użytkownik powinien móc zalogować się za pomocą wybranej nazwy użytkownika i hasła.

Teraz sprawdzono, że reguły zapory działają:

<!---- You can ping the server on the spoke VNet.--->
- Możesz przeglądać serwer internetowy w sieci wirtualnej będącej szprychą.
- Możesz nawiązać połączenie z serwerem w sieci wirtualnej będącej szprychą, korzystając z protokołu RDP.

Następnie zmień ustawienie akcji kolekcji reguł sieci zapory na **Odmów**, aby sprawdzić, czy reguły zapory działają zgodnie z oczekiwaniami. Uruchom następujący skrypt, aby zmienić ustawienie akcji kolekcji reguł na **Odmów**.

```azurepowershell
$rcNet = $azfw.GetNetworkRuleCollectionByName("RCNet01")
$rcNet.action.type = "Deny"

Set-AzFirewall -AzureFirewall $azfw
```

Teraz ponownie uruchom testy. Tym razem wszystkie powinny zakończyć się niepowodzeniem. Zamknij wszystkie zdalne pulpity, zanim zaczniesz testować zmienione zasady.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Możesz zachować zasoby zapory na potrzeby kolejnego samouczka, a jeśli nie będą już potrzebne, możesz usunąć grupę zasobów **FW-Hybrid-Test**, aby usunąć wszystkie zasoby związane z zaporą.

## <a name="next-steps"></a>Następne kroki

Następnie możesz monitorować dzienniki usługi Azure Firewall.

[Samouczek: monitorowanie dzienników usługi Azure Firewall](./firewall-diagnostics.md)