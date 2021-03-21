---
title: Wdrażanie aplikacji dwustosowej IPv6 przy użyciu standardowej Load Balancer wewnętrznej na platformie Azure — PowerShell
titlesuffix: Azure Virtual Network
description: W tym artykule pokazano, jak wdrożyć aplikację dwustosową IPv6 przy użyciu standardowego wewnętrznego Load Balancer w usłudze Azure Virtual Network przy użyciu programu Azure PowerShell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/14/2019
ms.author: kumud
ms.openlocfilehash: 3df89b84e748f041f13866c1eb3c0b8a3341209c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98220834"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-standard-internal-load-balancer-in-azure---powershell-preview"></a>Wdrażanie aplikacji podwójnego stosu IPv6 przy użyciu standardowego wewnętrznego Load Balancer na platformie Azure — PowerShell (wersja zapoznawcza)

W tym artykule opisano sposób wdrażania aplikacji podwójnego stosu (IPv4 + IPv6) na platformie Azure, która obejmuje sieć wirtualną o podwójnym stosie i podsieć, standardową Load Balancer z dwoma (IPv4 + IPv6) konfiguracjami frontonu, maszyn wirtualnych z kartami sieciowymi z konfiguracją podwójnego adresu IP, sieciowej grupy zabezpieczeń i publicznym adresy IP.

Procedura tworzenia Load Balancer wewnętrznych z obsługą protokołu IPv6 jest niemal identyczna z procesem tworzenia Load Balancer protokołu IPv6 dostępnego z [Internetu.](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) Jedyne różnice dotyczące tworzenia wewnętrznego modułu równoważenia obciążenia znajdują się w konfiguracji frontonu, jak pokazano w poniższym przykładzie programu PowerShell:

```azurepowershell
 $frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
 -Name "dsLbFrontEnd_v6" `
 -PrivateIpAddress "fd00:db8:deca:deed::100" `
 -PrivateIpAddressVersion "IPv6" `
 -Subnet $DsSubnet
```

Zmiany, które przewyższają powyższą konfigurację frontonu modułu równoważenia obciążenia:
- `PrivateIpAddressVersion`Jest określony jako "IPv6"
- `-PublicIpAddress`Argument został pominięty lub zastąpiony przez `-PrivateIpAddress` . Należy pamiętać, że adres prywatny musi znajdować się w zakresie przestrzeni adresów IP podsieci, w której zostanie wdrożony wewnętrzny moduł równoważenia obciążenia. W przypadku `-PrivateIpAddress` pominięcia statycznego zostanie wybrany następny bezpłatny adres IPv6 z podsieci, w której jest wdrażany wewnętrzny moduł równoważenia obciążenia.
- Podsieć o podwójnej stercie, w której zostanie wdrożony wewnętrzny moduł równoważenia obciążenia, jest określana za pomocą `-Subnet` `-SubnetId` argumentu lub.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować program PowerShell i używać go lokalnie, ten artykuł będzie wymagał modułu Azure PowerShell w wersji 6.9.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="prerequisites"></a>Wymagania wstępne
Przed wdrożeniem aplikacji o podwójnej stercie na platformie Azure należy skonfigurować subskrypcję tej funkcji w wersji zapoznawczej, korzystając z następującej Azure PowerShell:

Zarejestruj się w następujący sposób:
```azurepowershell
Register-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Register-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
Ukończenie rejestracji funkcji może potrwać do 30 minut. Stan rejestracji można sprawdzić, uruchamiając następujące polecenie Azure PowerShell: Sprawdź rejestrację w następujący sposób:
```azurepowershell
Get-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Get-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
Po zakończeniu rejestracji Uruchom następujące polecenie:

```azurepowershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Przed utworzeniem sieci wirtualnej o podwójnym stosie należy utworzyć grupę zasobów przy użyciu polecenie [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Poniższy przykład tworzy grupę zasobów o nazwie *dsStd_ILB_RG* w lokalizacji *Wschodnie stany USA* :

```azurepowershell
$rg = New-AzResourceGroup `
  -ResourceGroupName "dsStd_ILB_RG"  `
  -Location "east us"
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses"></a>Tworzenie publicznych adresów IP adresów IPv4 i IPv6
Aby można było uzyskać dostęp do maszyn wirtualnych z Internetu, potrzebne są publiczne adresy IP IPv4 i IPv6 dla maszyn wirtualnych. Utwórz publiczne adresy IP przy użyciu programu [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Poniższy przykład tworzy publiczny adres IP IPv4 i IPv6 o nazwie *RdpPublicIP_1* i *RdpPublicIP_2* w grupie zasobów *dsStd_ILB_RG* :

```azurepowershell
$RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv4  `
  -sku Standard
  
$RdpPublicIP_2 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_2" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv4  `
  -sku Standard
```
## <a name="create-the-virtual-network-and-the-subnet"></a>Tworzenie sieci wirtualnej i podsieci

Utwórz sieć wirtualną przy użyciu polecenia [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) z podwójnym stosem konfiguracji podsieci przy użyciu polecenia [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Poniższy przykład tworzy sieć wirtualną o nazwie *dsVnet* z *dsSubnet*.

```azurepowershell
# Create dual stack subnet config
$DsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name "dsSubnet" `
  -AddressPrefix "10.0.0.0/24","fd00:db8:deca:deed::/64"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsVnet" `
  -AddressPrefix "10.0.0.0/16","fd00:db8:deca::/48"  `
  -Subnet $DsSubnet

#Refresh the fully populated subnet for use in load balancer frontend configuration
$DsSubnet = get-AzVirtualNetworkSubnetconfig -name dsSubnet -VirtualNetwork $vnet
```
## <a name="create-standard-load-balancer"></a>Tworzenie usługi Load Balancer w warstwie Standardowa

W tej sekcji należy skonfigurować podwójny adres IP frontonu (IPv4 i IPv6) oraz pulę adresów zaplecza dla modułu równoważenia obciążenia, a następnie utworzyć usługa Load Balancer w warstwie Standardowa.

### <a name="create-front-end-ip"></a>Tworzenie adresu IP frontonu

Utwórz adres IP frontonu przy użyciu polecenia [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig). Poniższy przykład tworzy konfiguracje adresów IP frontonu IPv4 i IPv6 o nazwie *dsLbFrontEnd_v4* i *dsLbFrontEnd_v6*:

```azurepowershell
$frontendIPv4 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v4" `
  -PrivateIpAddress "10.0.0.100"  `
  -PrivateIpAddressVersion "IPv4"   `
  -Subnet $DsSubnet

$frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PrivateIpAddress "fd00:db8:deca:deed::100"  `
  -PrivateIpAddressVersion "IPv6"   `
  -Subnet $DsSubnet

```

### <a name="configure-back-end-address-pool"></a>Konfigurowanie puli adresów zaplecza

Utwórz pulę adresów zaplecza za pomocą elementu [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig). W kolejnych krokach maszyny wirtualne zostaną dołączone do tej puli zaplecza. Poniższy przykład tworzy pule adresów zaplecza o nazwie *dsLbBackEndPool_v4* i *dsLbBackEndPool_v6* w celu uwzględnienia maszyn wirtualnych z konfiguracją kart sieciowych IPv4 i IPv6:

```azurepowershell
$backendPoolv4 = New-AzLoadBalancerBackendAddressPoolConfig -Name "dsLbBackEndPool_v4"

$backendPoolv6 = New-AzLoadBalancerBackendAddressPoolConfig -Name "dsLbBackEndPool_v6"
```

### <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła modułu równoważenia obciążenia służy do definiowania sposobu dystrybucji ruchu do maszyn wirtualnych. Zdefiniuj konfigurację adresu IP frontonu na potrzeby ruchu przychodzącego oraz pulę adresów IP zaplecza do odbierania ruchu, wraz z wymaganym portem źródłowym i docelowym. Aby upewnić się, że tylko zdrowe maszyny wirtualne odbierają ruch, można opcjonalnie zdefiniować sondę kondycji. Podstawowa usługa równoważenia obciążenia używa sondy IPv4 do oceny kondycji dla punktów końcowych IPv4 i IPv6 na maszynach wirtualnych. Usługa równoważenia obciążenia w warstwie Standardowa obejmuje obsługę jawnych sond kondycji protokołu IPv6.

Utwórz regułę modułu równoważenia obciążenia przy użyciu polecenia [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig). Poniższy przykład tworzy reguły modułu równoważenia obciążenia o nazwie *dsLBrule_v4* i *dsLBrule_v6* i równoważy ruch na  porcie TCP *80* do konfiguracji adresu IP frontonu IPv4 i IPv6:

```azurepowershell
$lbrule_v4 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v4" `
  -FrontendIpConfiguration $frontendIPv4 `
  -BackendAddressPool $backendPoolv4 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80

$lbrule_v6 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
```

### <a name="create-load-balancer"></a>Tworzenie modułu równoważenia obciążenia

Utwórz usługa Load Balancer w warstwie Standardowa za pomocą elementu [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). Poniższy przykład tworzy publiczną usługa Load Balancer w warstwie Standardowa o nazwie *myInternalLoadBalancer* przy użyciu konfiguracji IP frontonu IPv4 i IPv6, pul zaplecza i reguł równoważenia obciążenia, które zostały utworzone w poprzednich krokach:

```azurepowershell
$lb = New-AzLoadBalancer  `
  -ResourceGroupName $rg.ResourceGroupName  `
  -Location $rg.Location  `
  -Name  "MyInternalLoadBalancer"  `
  -Sku "Standard"  `
  -FrontendIpConfiguration  $frontendIPv4,$frontendIPv6  `
  -BackendAddressPool  $backendPoolv4,$backendPoolv6  `
  -LoadBalancingRule  $lbrule_v4,$lbrule_v6

```

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych
Przed wdrożeniem niektórych maszyn wirtualnych i przetestowanie modułu równoważenia obciążenia, należy utworzyć pomocnicze zasoby sieciowe — zestaw dostępności, sieciową grupę zabezpieczeń i wirtualne karty sieciowe. 

### <a name="create-an-availability-set"></a>Tworzenie zestawu dostępności
Aby zwiększyć wysoką dostępność aplikacji, umieść maszyny wirtualne w zestawie dostępności.

Aby utworzyć zestaw dostępności, użyj polecenia [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset). Poniższy przykład tworzy zestaw dostępności o nazwie *dsAVset*:

```azurepowershell
$avset = New-AzAvailabilitySet `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsAVset" `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2 `
  -Sku aligned
```

### <a name="create-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Utwórz sieciową grupę zabezpieczeń dla reguł, które będą zarządzać komunikacją przychodzącą i wychodzącą w sieci wirtualnej.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Tworzenie reguły sieciowej grupy zabezpieczeń dla portu 3389

Utwórz regułę sieciowej grupy zabezpieczeń, aby zezwolić na połączenia protokołu RDP za pośrednictwem portu 3389 przy użyciu polecenia [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell
$rule1 = New-AzNetworkSecurityRuleConfig `
  -Name 'myNetworkSecurityGroupRuleRDP' `
  -Description 'Allow RDP' `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 3389
```
#### <a name="create-a-network-security-group-rule-for-port-80"></a>Tworzenie reguły sieciowej grupy zabezpieczeń dla portu 80

Utwórz regułę sieciowej grupy zabezpieczeń, aby zezwolić na połączenia internetowe przez port 80 za pomocą narzędzia [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name 'myNetworkSecurityGroupRuleHTTP' `
  -Description 'Allow HTTP' `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange 80 `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80
```
#### <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Utwórz sieciową grupę zabezpieczeń przy użyciu polecenia [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

```azurepowershell
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsNSG1"  `
  -SecurityRules $rule1,$rule2
```
### <a name="create-nics"></a>Tworzenie kart sieciowych

Twórz wirtualne karty sieciowe za pomocą interfejsu [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). Poniższy przykład tworzy dwie wirtualne karty sieciowe zarówno z konfiguracją IPv4, jak i IPv6. (Po jednej karcie na każdą maszynę wirtualną, która zostanie utworzona na potrzeby aplikacji w kolejnych krokach).

```azurepowershell

# Create the IPv4 configuration for NIC 1
$Ip4Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp4Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv4 `
  -LoadBalancerBackendAddressPool $backendPoolv4 `
  -PublicIpAddress  $RdpPublicIP_1

# Create the IPv6 configuration
$Ip6Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp6Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv6 `
  -LoadBalancerBackendAddressPool $backendPoolv6

# Create NIC 1
$NIC_1 = New-AzNetworkInterface `
  -Name "dsNIC1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -NetworkSecurityGroupId $nsg.Id `
  -IpConfiguration $Ip4Config,$Ip6Config

# Create the IPv4 configuration for NIC 2
$Ip4Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp4Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv4 `
  -LoadBalancerBackendAddressPool $backendPoolv4 `
  -PublicIpAddress  $RdpPublicIP_2

# Create NIC 2 reusing the IPv6 configuration from NIC 1
$NIC_2 = New-AzNetworkInterface `
  -Name "dsNIC2" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -NetworkSecurityGroupId $nsg.Id `
  -IpConfiguration $Ip4Config,$Ip6Config

```

### <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Ustaw nazwę użytkownika i hasło administratora maszyn wirtualnych przy użyciu polecenia [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell
$cred = get-credential -Message "DUAL STACK VNET SAMPLE:  Please enter the Administrator credential to log into the VM's"
```

Następnie utwórz maszyny wirtualne za pomocą polecenia [New-AzVM](/powershell/module/az.compute/new-azvm). Poniższy przykład obejmuje tworzenie dwóch maszyn wirtualnych oraz wymaganych składników sieci wirtualnej, jeśli jeszcze nie istnieją.

```azurepowershell
$vmsize = "Standard_A2"
$ImagePublisher = "MicrosoftWindowsServer"
$imageOffer = "WindowsServer"
$imageSKU = "2019-Datacenter"

$vmName= "dsVM1"
$VMconfig1 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_1.Id  3> $null
$VM1 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig1


$vmName= "dsVM2"
$VMconfig2 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_2.Id  3> $null
$VM2 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig2
```
## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Wyświetlanie sieci wirtualnej podwójnego stosu IPv6 w Azure Portal
Sieć wirtualną o podwójnym stosie IPv6 można wyświetlić w Azure Portal w następujący sposób:
1. Na pasku wyszukiwania portalu wprowadź *dsVnet*.
2. Gdy **dsVnet** pojawia się w wynikach wyszukiwania, wybierz ją. Spowoduje to uruchomienie strony **Przegląd** sieci wirtualnej o podwójnym stosie o nazwie *dsVnet*. Sieć wirtualna o podwójnym stosie pokazuje dwie karty sieciowe z konfiguracją protokołów IPv4 i IPv6 znajdującą się w podwójnej podsieci o nazwie *dsSubnet*.

![Virtual Network podwójnego stosu IPv6 ze standardowym Load Balancer wewnętrznym](./media/ipv6-dual-stack-standard-internal-load-balancer-powershell/ipv6-dual-stack-virtual-network.png)


> [!NOTE]
> Protokół IPv6 dla usługi Azure Virtual Network jest dostępny w Azure Portal w ramach tej wersji zapoznawczej tylko do odczytu.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell
Remove-AzResourceGroup -Name dsStd_ILB_RG
```

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano tworzenie usługa Load Balancer w warstwie Standardowa z konfiguracją dwóch adresów IP frontonu (IPv4 i IPv6). Utworzono również dwie maszyny wirtualne, które zawierają karty sieciowe z dwoma konfiguracjami protokołu IP (IPV4 + IPv6), które zostały dodane do puli zaplecza modułu równoważenia obciążenia. Aby dowiedzieć się więcej o obsłudze protokołu IPv6 w sieciach wirtualnych platformy Azure, zobacz [co to jest protokół IPv6 dla systemu azure Virtual Network?](ipv6-overview.md)