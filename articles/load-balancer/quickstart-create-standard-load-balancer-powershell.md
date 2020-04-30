---
title: 'Szybki Start: Tworzenie Load Balancer-Azure PowerShell'
titleSuffix: Azure Load Balancer
description: Ten przewodnik Szybki Start przedstawia sposób tworzenia Load Balancer przy użyciu Azure PowerShell
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a Load balancer so that I can load balance internet traffic to VMs.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: allensu
ms:custom: seodec18
ms.openlocfilehash: f169d7694199e496e472a6c32312cf6782270378
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80247218"
---
# <a name="quickstart-create-a-load-balancer-using-azure-powershell"></a>Szybki Start: Tworzenie Load Balancer przy użyciu Azure PowerShell

W tym przewodniku Szybki start przedstawiono sposób tworzenia usługi Load Balancer w warstwie Standardowa przy użyciu programu Azure PowerShell. Aby przetestować moduł równoważenia obciążenia, należy wdrożyć trzy maszyny wirtualne z systemem Windows Server i zrównoważyć obciążenie aplikacji sieci Web między maszynami wirtualnymi. Aby dowiedzieć się więcej na temat usługi Load Balancer w warstwie Standardowa, zobacz [Co to jest usługa Load Balancer w warstwie Standardowa](load-balancer-standard-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten artykuł wymaga modułu Azure PowerShell w wersji 5.4.1 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Zanim będzie można utworzyć moduł równoważenia obciążenia, musisz utworzyć grupę zasobów za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroupSLB* w lokalizacji *Wschodnie* :

```azurepowershell
$rgName='MyResourceGroupSLB'
$location='eastus'
New-AzResourceGroup -Name $rgName -Location $location
```

## <a name="create-a-public-ip-address"></a>Tworzenie publicznego adresu IP

Aby uzyskać dostęp do aplikacji za pośrednictwem Internetu, potrzebujesz publicznego adresu IP modułu równoważenia obciążenia. Utwórz publiczny adres IP przy użyciu polecenia [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Poniższy przykład tworzy strefowo nadmiarowy publiczny adres IP o nazwie *myPublicIP* w grupie zasobów *myResourceGroupSLB* :

```azurepowershell
$publicIp = New-AzPublicIpAddress `
 -ResourceGroupName $rgName `
 -Name 'myPublicIP' `
 -Location $location `
 -AllocationMethod static `
 -SKU Standard
```

Aby utworzyć strefowy publiczny adres IP w strefie 1, użyj następujących:

```azurepowershell
$publicIp = New-AzPublicIpAddress `
 -ResourceGroupName $rgName `
 -Name 'myPublicIP' `
 -Location $location `
 -AllocationMethod static `
 -SKU Standard `
 -zone 1
```

Użyj ```-SKU Basic``` , aby utworzyć podstawowy publiczny adres IP. Podstawowe publiczne adresy IP nie są zgodne z usługą równoważenia obciążenia w **warstwie Standardowa** . Firma Microsoft zaleca używanie **standardu** dla obciążeń produkcyjnych.

> [!IMPORTANT]
> W pozostałej części tego przewodnika Szybki Start przyjęto założenie, że w ramach procesu wyboru jednostki SKU zostanie wybrana **standardowa** jednostka SKU.

## <a name="create-load-balancer"></a>Utwórz Load Balancer

W tej sekcji skonfigurujesz adres IP frontonu i pulę adresów zaplecza na potrzeby modułu równoważenia, a następnie utworzysz usługę Load Balancer w warstwie Standardowa.

### <a name="create-frontend-ip"></a>Tworzenie adresu IP frontonu

Utwórz adres IP frontonu przy użyciu polecenia [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig). Poniższy przykład umożliwia utworzenie konfiguracji adresu IP frontonu o nazwie Moja *fronton* i dołączenie adresu *myPublicIP* :

```azurepowershell
$feip = New-AzLoadBalancerFrontendIpConfig -Name 'myFrontEndPool' -PublicIpAddress $publicIp
```

### <a name="configure-back-end-address-pool"></a>Konfigurowanie puli adresów zaplecza

Utwórz pulę adresów zaplecza za pomocą elementu [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig). W kolejnych krokach maszyny wirtualne zostaną dołączone do tej puli zaplecza. Poniższy przykład tworzy pulę adresów zaplecza o nazwie *myBackEndPool*:

```azurepowershell-interactive
$bepool = New-AzLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'
```

### <a name="create-a-health-probe"></a>Tworzenie sondy kondycji
Sonda kondycji umożliwia modułowi równoważenia obciążenia monitorowanie stanu aplikacji. Dynamicznie dodaje lub usuwa maszyny wirtualne w rotacji modułu równoważenia obciążenia na podstawie ich odpowiedzi na kontrole kondycji. Domyślnie maszyna wirtualna jest wykluczana z dystrybucji ruchu przez moduł równoważenia obciążenia, jeśli dwie kolejne próby podejmowane w 15-sekundowych odstępach zakończą się niepowodzeniem. Sonda kondycji jest tworzona z użyciem protokołu lub konkretnej strony kontroli kondycji aplikacji.

W poniższym przykładzie zostanie utworzona sonda TCP. Możesz także tworzyć niestandardowe sondy HTTP na potrzeby bardziej szczegółowych kontroli kondycji. W przypadku użycia niestandardowej sondy HTTP należy utworzyć stronę kontroli kondycji, na przykład *healthcheck.aspx*. Aby dany host pozostał w rotacji, sonda musi zwrócić do modułu równoważenia obciążenia kod odpowiedzi **HTTP 200 OK**.

Aby utworzyć sondę kondycji TCP, użyj polecenia [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig). W poniższym przykładzie zostanie utworzona sonda kondycji o nazwie *myHealthProbe* monitorująca poszczególne maszyny wirtualne przy użyciu portu *HTTP**80*:

```azurepowershell
$probe = New-AzLoadBalancerProbeConfig `
 -Name 'myHealthProbe' `
 -Protocol Http -Port 80 `
 -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
```

### <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia
Reguła modułu równoważenia obciążenia służy do definiowania sposobu dystrybucji ruchu do maszyn wirtualnych. Zdefiniuj konfigurację adresu IP frontonu na potrzeby ruchu przychodzącego oraz pulę adresów IP zaplecza do odbierania ruchu, wraz z wymaganym portem źródłowym i docelowym. Aby mieć pewność, że ruch będzie kierowany tylko do maszyn wirtualnych w dobrej kondycji, zdefiniuj również sondę kondycji do użycia.

Utwórz regułę modułu równoważenia obciążenia przy użyciu polecenia [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig). W poniższym przykładzie zostanie utworzona reguła modułu równoważenia obciążenia o nazwie *myLoadBalancerRule* w celu równoważenia obciążenia na porcie *TCP**80*:

```azurepowershell
$rule = New-AzLoadBalancerRuleConfig `
  -Name 'myLoadBalancerRuleWeb' -Protocol Tcp `
  -Probe $probe -FrontendPort 80 -BackendPort 80 `
  -FrontendIpConfiguration $feip `
  -BackendAddressPool $bePool
```

### <a name="create-the-nat-rules"></a>Tworzenie reguł NAT

Utwórz reguły NAT za pomocą [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig). Poniższy przykład tworzy reguły NAT o nazwie *myLoadBalancerRDP1* i *myLoadBalancerRDP2* , aby zezwolić na połączenia RDP z serwerami zaplecza z portami 4221 i 4222:

```azurepowershell
$natrule1 = New-AzLoadBalancerInboundNatRuleConfig `
  -Name 'myLoadBalancerRDP1' `
  -FrontendIpConfiguration $feip `
  -Protocol tcp -FrontendPort 4221 `
  -BackendPort 3389

$natrule2 = New-AzLoadBalancerInboundNatRuleConfig `
  -Name 'myLoadBalancerRDP2' `
  -FrontendIpConfiguration $feip `
  -Protocol tcp `
  -FrontendPort 4222 `
  -BackendPort 3389

$natrule3 = New-AzLoadBalancerInboundNatRuleConfig `
  -Name 'myLoadBalancerRDP3' `
  -FrontendIpConfiguration $feip `
  -Protocol tcp `
  -FrontendPort 4223 `
  -BackendPort 3389
```

### <a name="create-load-balancer"></a>Tworzenie modułu równoważenia obciążenia

Utwórz usługę Load Balancer w warstwie Standardowa przy użyciu polecenia [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). Poniższy przykład tworzy publiczną usługa Load Balancer w warstwie Standardowa o nazwie myLoadBalancer przy użyciu konfiguracji adresu IP frontonu, puli zaplecza, sondy kondycji, reguły równoważenia obciążenia i reguł translatora adresów sieciowych utworzonych w poprzednich krokach:

```azurepowershell
$lb = New-AzLoadBalancer `
  -ResourceGroupName $rgName `
  -Name 'MyLoadBalancer' `
  -SKU Standard `
  -Location $location `
  -FrontendIpConfiguration $feip `
  -BackendAddressPool $bepool `
  -Probe $probe `
  -LoadBalancingRule $rule `
  -InboundNatRule $natrule1,$natrule2,$natrule3
```

Użyj ```-SKU Basic``` , aby utworzyć podstawową Load Balancer. Firma Microsoft zaleca używanie standardu dla obciążeń produkcyjnych.

> [!IMPORTANT]
> W pozostałej części tego przewodnika Szybki Start przyjęto założenie, że w ramach procesu wyboru jednostki SKU zostanie wybrana **standardowa** jednostka SKU.

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych
Zanim będzie możliwe wdrożenie maszyn wirtualnych i przetestowanie modułu równoważenia obciążenia, musisz utworzyć pomocnicze zasoby sieci wirtualnej — sieć wirtualną i wirtualnej karty sieciowe. 

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej
Utwórz sieć wirtualną przy użyciu polecenia [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Poniższy przykład obejmuje tworzenie sieci wirtualnej o nazwie *myVnet* z podsiecią *mySubnet*:

```azurepowershell
# Create subnet config
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.2.0/24

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroupSLB" `
  -Location $location `
  -Name "myVnet" `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnetConfig
```
### <a name="create-public-ip-addresses-for-the-vms"></a>Tworzenie publicznych adresów IP dla maszyn wirtualnych

Aby uzyskać dostęp do maszyn wirtualnych przy użyciu połączenia RDP, potrzebny jest publiczny adres IP dla maszyn wirtualnych. Ponieważ w tym scenariuszu jest używana usługa Load Balancer w warstwie Standardowa, należy utworzyć standardowe publiczne adresy IP dla maszyn wirtualnych za pomocą polecenie [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress).

```azurepowershell
$RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $RgName `
  -Location $location  `
  -SKU Standard `
  -AllocationMethod static
 

$RdpPublicIP_2 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_2" `
  -ResourceGroupName $RgName `
  -Location $location  `
  -SKU Standard `
  -AllocationMethod static


$RdpPublicIP_3 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_3" `
  -ResourceGroupName $RgName `
  -Location $location  `
  -SKU Standard `
  -AllocationMethod static

```

Użyj ```-SKU Basic``` , aby utworzyć podstawowe publiczne adresy IP. Firma Microsoft zaleca używanie standardu dla obciążeń produkcyjnych.

### <a name="create-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń
Utwórz sieciową grupę zabezpieczeń w celu zdefiniowania połączeń przychodzących do sieci wirtualnej.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Tworzenie reguły sieciowej grupy zabezpieczeń dla portu 3389
Utwórz regułę sieciowej grupy zabezpieczeń, aby zezwolić na połączenia protokołu RDP za pośrednictwem portu 3389 przy użyciu polecenia [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell

$rule1 = New-AzNetworkSecurityRuleConfig -Name 'myNetworkSecurityGroupRuleRDP' -Description 'Allow RDP' `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 1000 `
  -SourceAddressPrefix Internet -SourcePortRange * `
  -DestinationAddressPrefix * -DestinationPortRange 3389
```

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Tworzenie reguły sieciowej grupy zabezpieczeń dla portu 80
Utwórz regułę sieciowej grupy zabezpieczeń, aby zezwolić na połączenia przychodzące za pośrednictwem portu 80 przy użyciu polecenia [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell
$rule2 = New-AzNetworkSecurityRuleConfig -Name 'myNetworkSecurityGroupRuleHTTP' -Description 'Allow HTTP' `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 2000 `
  -SourceAddressPrefix Internet -SourcePortRange * `
  -DestinationAddressPrefix * -DestinationPortRange 80
```

#### <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Utwórz sieciową grupę zabezpieczeń przy użyciu polecenia [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

```azurepowershell
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $RgName -Location $location `
-Name 'myNetworkSecurityGroup' -SecurityRules $rule1,$rule2
```

### <a name="create-nics"></a>Tworzenie kart sieciowych
Utwórz wirtualne karty sieciowe i skojarz je z publicznymi adresami IP i sieciowymi grupami zabezpieczeń utworzonymi w poprzednich krokach za pomocą instrukcji [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). W poniższym przykładzie zostaną utworzone trzy wirtualne karty sieciowe. (Po jednej karcie na każdą maszynę wirtualną, która zostanie utworzona na potrzeby aplikacji w kolejnych krokach). Możesz w dowolnym momencie utworzyć i dodać do modułu równoważenia obciążenia dodatkowe wirtualne karty sieciowe i maszyny wirtualne:

```azurepowershell
# Create NIC for VM1
$nicVM1 = New-AzNetworkInterface -ResourceGroupName $rgName -Location $location `
  -Name 'MyNic1' -PublicIpAddress $RdpPublicIP_1 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg `
  -LoadBalancerInboundNatRule $natrule1 -Subnet $vnet.Subnets[0]

$nicVM2 = New-AzNetworkInterface -ResourceGroupName $rgName -Location $location `
  -Name 'MyNic2' -PublicIpAddress $RdpPublicIP_2 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg `
  -LoadBalancerInboundNatRule $natrule2 -Subnet $vnet.Subnets[0]

$nicVM3 = New-AzNetworkInterface -ResourceGroupName $rgName -Location $location `
  -Name 'MyNic3' -PublicIpAddress $RdpPublicIP_3 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg `
  -LoadBalancerInboundNatRule $natrule3 -Subnet $vnet.Subnets[0]
```

### <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Ustaw nazwę użytkownika i hasło administratora maszyn wirtualnych przy użyciu polecenia [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell
$cred = Get-Credential
```

Następnie utwórz maszyny wirtualne za pomocą polecenia [New-AzVM](/powershell/module/az.compute/new-azvm). Poniższy przykład obejmuje tworzenie dwóch maszyn wirtualnych oraz wymaganych składników sieci wirtualnej, jeśli jeszcze nie istnieją. W tym przykładzie karty sieciowe (*MyNic1*, *MyNic2*i *MyNic3*) utworzone w poprzednim kroku są przypisane do maszyn wirtualnych *myVM1*, *myVM2*i *VM3*. Ponadto, ponieważ karty sieciowe są skojarzone z pulą zaplecza modułu równoważenia obciążenia, maszyny wirtualne są automatycznie dodawane do puli zaplecza.

```azurepowershell

# ############## VM1 ###############

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName 'myVM1' -VMSize Standard_DS1_v2 `
 | Set-AzVMOperatingSystem -Windows -ComputerName 'myVM1' -Credential $cred `
 | Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2019-Datacenter -Version latest `
 | Add-AzVMNetworkInterface -Id $nicVM1.Id

# Create a virtual machine
$vm1 = New-AzVM -ResourceGroupName $rgName -Zone 1 -Location $location -VM $vmConfig

# ############## VM2 ###############

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName 'myVM2' -VMSize Standard_DS1_v2 `
 | Set-AzVMOperatingSystem -Windows -ComputerName 'myVM2' -Credential $cred `
 | Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2019-Datacenter -Version latest `
 | Add-AzVMNetworkInterface -Id $nicVM2.Id

# Create a virtual machine
$vm2 = New-AzVM -ResourceGroupName $rgName -Zone 2 -Location $location -VM $vmConfig

# ############## VM3 ###############

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName 'myVM3' -VMSize Standard_DS1_v2 `
 | Set-AzVMOperatingSystem -Windows -ComputerName 'myVM3' -Credential $cred `
 | Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2019-Datacenter -Version latest `
| Add-AzVMNetworkInterface -Id $nicVM3.Id

# Create a virtual machine
$vm3 = New-AzVM -ResourceGroupName $rgName -Zone 3 -Location $location -VM $vmConfig
```

Utworzenie i skonfigurowanie trzech maszyn wirtualnych może potrwać kilka minut.

### <a name="install-iis-with-a-custom-web-page"></a>Instalowanie usług IIS za pomocą niestandardowej strony sieci Web

Zainstaluj usługi IIS przy użyciu niestandardowej strony internetowej na obu maszynach wirtualnych zaplecza w następujący sposób:

1. Pobierz publiczne adresy IP z trzech maszyn wirtualnych przy użyciu `Get-AzPublicIPAddress`programu.

   ```azurepowershell
     $vm1_rdp_ip = (Get-AzPublicIPAddress -ResourceGroupName $rgName -Name "RdpPublicIP_1").IpAddress
     $vm2_rdp_ip = (Get-AzPublicIPAddress -ResourceGroupName $rgName -Name "RdpPublicIP_2").IpAddress
     $vm3_rdp_ip = (Get-AzPublicIPAddress -ResourceGroupName $rgName -Name "RdpPublicIP_3").IpAddress
    ```
2. Utwórz połączenia pulpitu zdalnego z *myVM1*, *myVM2*i *MYVM3* przy użyciu publicznych adresów IP maszyn wirtualnych w następujący sposób: 

   ```azurepowershell    
     mstsc /v:$vm1_rdp_ip
     mstsc /v:$vm2_rdp_ip
     mstsc /v:$vm3_rdp_ip
   
    ```

3. Wprowadź poświadczenia dla każdej maszyny wirtualnej, aby uruchomić sesję RDP.
4. Uruchom program Windows PowerShell na każdej maszynie wirtualnej i użyj następujących poleceń, aby zainstalować serwer IIS i zaktualizować domyślny plik htm.

    ```azurepowershell
    # Install IIS
     Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from host " + $env:computername)
    ```

5. Zamknij połączenia RDP z *myVM1*, *myVM2*i *myVM3*.


## <a name="test-load-balancer"></a>Testowanie modułu równoważenia obciążenia
Uzyskaj publiczny adres IP modułu równoważenia obciążenia za pomocą polecenia [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress). W poniższym przykładzie uzyskano utworzony wcześniej adres IP *myPublicIP*:

```azurepowershell
Get-AzPublicIPAddress `
  -ResourceGroupName "myResourceGroupSLB" `
  -Name "myPublicIP" | select IpAddress
```

Następnie możesz wprowadzić publiczny adres IP w przeglądarce internetowej. Zostanie wyświetlona witryna internetowa z nazwą hosta maszyny wirtualnej, do której moduł równoważenia obciążenia kieruje ruch, jak pokazano na poniższym przykładzie:

![Testowanie modułu równoważenia obciążenia](media/quickstart-create-basic-load-balancer-powershell/load-balancer-test.png)

Aby zobaczyć, jak moduł równoważenia obciążenia rozdziela ruch między trzy maszyny wirtualne używane przez aplikację, możesz wymusić odświeżenie w przeglądarce internetowej. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell
Remove-AzResourceGroup -Name myResourceGroupSLB
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono usługa Load Balancer w warstwie Standardowa, dołączono do niego maszyny wirtualne, skonfigurowano regułę ruchu Load Balancer, sondę kondycji, a następnie przetestowano Load Balancer. Aby dowiedzieć się więcej na temat Azure Load Balancer, przejdź do [Azure Load Balancer samouczków](tutorial-load-balancer-standard-public-zone-redundant-portal.md).

Dowiedz się więcej na temat [stref Load Balancer i dostępności](load-balancer-standard-availability-zones.md).
