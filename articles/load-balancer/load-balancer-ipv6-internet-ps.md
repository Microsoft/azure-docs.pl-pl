---
title: Tworzenie modułu równoważenia obciążenia dostępnego z Internetu przy użyciu protokołu IPv6 Azure PowerShell
titleSuffix: Azure Load Balancer
description: Dowiedz się, jak utworzyć moduł równoważenia obciążenia połączony z Internetem przy użyciu protokołu IPv6 dla Menedżer zasobów
services: load-balancer
documentationcenter: na
author: asudbring
keywords: IPv6, moduł równoważenia obciążenia platformy Azure, podwójny stos, publiczny adres IP, natywny protokół IPv6, urządzenia przenośne, IoT
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 43203a756bcb42c7d00de9c11e9223f1d8b9e2a8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87001591"
---
# <a name="get-started-creating-an-internet-facing-load-balancer-with-ipv6-using-powershell-for-resource-manager"></a>Wprowadzenie do tworzenia modułu równoważenia obciążenia połączonego z Internetem przy użyciu protokołu IPv6 dla Menedżer zasobów

> [!div class="op_single_selector"]
> * [Program PowerShell](load-balancer-ipv6-internet-ps.md)
> * [Interfejs wiersza polecenia platformy Azure](load-balancer-ipv6-internet-cli.md)
> * [Szablon](load-balancer-ipv6-internet-template.md)

>[!NOTE] 
>W tym artykule opisano wstępną funkcję IPv6 umożliwiającą podstawowym usługom równoważenia obciążenia zapewnienie łączności protokołów IPv4 i IPv6. Kompleksowa łączność z protokołem IPv6 jest teraz dostępna przy użyciu [protokołu IPv6 dla usługi Azure sieci wirtualnych](../virtual-network/ipv6-overview.md) , która integruje łączność IPv6 z sieciami wirtualnymi i zawiera najważniejsze funkcje, takie jak reguły sieciowej grupy zabezpieczeń IPv6, routing zdefiniowany przez użytkownika IPv6, podstawowe i standardowe Równoważenie obciążenia.  Protokół IPv6 dla usługi Azure sieci wirtualnych jest zalecanym standardem dla aplikacji IPv6 na platformie Azure. Zobacz [IPv6 dla wdrożenia programu PowerShell sieci wirtualnej platformy Azure](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) 

Usługa Azure Load Balancer to moduł równoważenia obciążenia w warstwie 4 (TCP, UDP). Moduł równoważenia obciążenia zapewnia wysoką dostępność, dystrybuując ruch przychodzący w wystąpieniach usług o dobrej kondycji w usługach w chmurze lub na maszynach wirtualnych w zestawie modułu równoważenia obciążenia. Usługa Azure Load Balancer może także prezentować te usługi na wielu portach i/lub wielu adresach IP.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="example-deployment-scenario"></a>Przykładowy scenariusz wdrażania

Na poniższym diagramie przedstawiono rozwiązanie równoważenia obciążenia, które jest wdrażane w tym artykule.

![Scenariusz modułu równoważenia obciążenia](./media/load-balancer-ipv6-internet-ps/lb-ipv6-scenario.png)

W tym scenariuszu utworzysz następujące zasoby platformy Azure:

* Load Balancer dostępne z Internetu przy użyciu adresu IPv4 i publicznego protokołu IPv6
* dwie reguły równoważenia obciążenia służące do mapowania publicznych adresów VIP na prywatne punkty końcowe
* Zestaw dostępności, który zawiera dwie maszyny wirtualne
* dwie maszyny wirtualne
* Interfejs sieci wirtualnej dla każdej maszyny wirtualnej z przypisanymi adresami IPv4 i IPv6

## <a name="deploying-the-solution-using-the-azure-powershell"></a>Wdrażanie rozwiązania przy użyciu Azure PowerShell

Poniższe kroki przedstawiają sposób tworzenia modułu równoważenia obciążenia połączonego z Internetem przy użyciu Azure Resource Manager z programem PowerShell. W przypadku Azure Resource Manager każdy zasób jest tworzony i konfigurowany indywidualnie, a następnie umieszcza się w celu utworzenia zasobu.

Aby wdrożyć moduł równoważenia obciążenia, należy utworzyć i skonfigurować następujące obiekty:

* Konfiguracja adresu IP frontonu — zawiera publiczne adresy IP dla przychodzącego ruchu sieciowego.
* Pula adresów zaplecza — zawiera interfejsy sieciowe dla maszyn wirtualnych, które mają odbierać ruch sieciowy z modułu równoważenia obciążenia.
* Reguły równoważenia obciążenia — reguły mapowania portu publicznego modułu równoważenia obciążenia na port w puli adresów zaplecza.
* Reguły NAT ruchu przychodzącego — reguły mapowania portu publicznego modułu równoważenia obciążenia na port określonej maszyny wirtualnej w puli adresów zaplecza.
* Sondy — sondy kondycji używane do sprawdzania dostępności wystąpień maszyn wirtualnych w puli adresów zaplecza.

Aby uzyskać więcej informacji, zobacz [Azure Load Balancer Components](./components.md).

## <a name="set-up-powershell-to-use-resource-manager"></a>Konfigurowanie programu PowerShell do korzystania z usługi Resource Manager

Upewnij się, że masz najnowszą wersję produkcyjną modułu Azure Resource Manager dla programu PowerShell.

1. Logowanie na platformie Azure

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

    Po wyświetleniu monitu wprowadź poświadczenia.

2. Sprawdź subskrypcje dostępne na koncie.

   ```azurepowershell-interactive
    Get-AzSubscription
    ```

3. Wybierz subskrypcję platformy Azure do użycia.

    ```azurepowershell-interactive
    Select-AzSubscription -SubscriptionId 'GUID of subscription'
    ```

4. Utwórz grupę zasobów (Pomiń ten krok, jeśli używasz istniejącej grupy zasobów)

    ```azurepowershell-interactive
    New-AzResourceGroup -Name NRP-RG -location "West US"
    ```

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>Tworzenie sieci wirtualnej oraz publicznego adresu IP dla puli adresów IP frontonu

1. Utwórz sieć wirtualną z podsiecią.

    ```azurepowershell-interactive
    $backendSubnet = New-AzVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
    $vnet = New-AzvirtualNetwork -Name VNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
    ```

2. Tworzenie zasobów publicznego adresu IP (PIP) platformy Azure dla puli adresów IP frontonu. `-DomainNameLabel`Przed uruchomieniem poniższych poleceń należy zmienić wartość parametru. Wartość musi być unikatowa w regionie świadczenia usługi Azure.

    ```azurepowershell-interactive
    $publicIPv4 = New-AzPublicIpAddress -Name 'pub-ipv4' -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Static -IpAddressVersion IPv4 -DomainNameLabel lbnrpipv4
    $publicIPv6 = New-AzPublicIpAddress -Name 'pub-ipv6' -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Dynamic -IpAddressVersion IPv6 -DomainNameLabel lbnrpipv6
    ```

    > [!IMPORTANT]
    > Moduł równoważenia obciążenia używa etykiety domeny publicznego adresu IP jako prefiksu dla jego nazwy FQDN. W tym przykładzie nazwy FQDN to *lbnrpipv4.westus.cloudapp.Azure.com* i *lbnrpipv6.westus.cloudapp.Azure.com*.

## <a name="create-a-front-end-ip-configurations-and-a-back-end-address-pool"></a>Tworzenie Front-End konfiguracji protokołu IP i puli adresów Back-End

1. Utwórz konfigurację adresu frontonu korzystającą z utworzonych publicznych adresów IP.

    ```azurepowershell-interactive
    $FEIPConfigv4 = New-AzLoadBalancerFrontendIpConfig -Name "LB-Frontendv4" -PublicIpAddress $publicIPv4
    $FEIPConfigv6 = New-AzLoadBalancerFrontendIpConfig -Name "LB-Frontendv6" -PublicIpAddress $publicIPv6
    ```

2. Utwórz pule adresów zaplecza.

    ```azurepowershell-interactive
    $backendpoolipv4 = New-AzLoadBalancerBackendAddressPoolConfig -Name "BackendPoolIPv4"
    $backendpoolipv6 = New-AzLoadBalancerBackendAddressPoolConfig -Name "BackendPoolIPv6"
    ```

## <a name="create-lb-rules-nat-rules-a-probe-and-a-load-balancer"></a>Tworzenie reguł równoważenia obciążenia, reguł NAT i sondy

W tym przykładzie opisano tworzenie następujących elementów:

* Reguła NAT do translacji całego ruchu przychodzącego na porcie 443 do portu 4443
* Reguła modułu równoważenia obciążenia do równoważenia całego ruchu przychodzącego do portu 80 na port 80 adresów w puli zaplecza.
* Reguła modułu równoważenia obciążenia zezwalająca na połączenie RDP z maszynami wirtualnymi na porcie 3389.
* Reguła sondy służąca do sprawdzania stanu kondycji na stronie o nazwie *HealthProbe. aspx* lub usługi na porcie 8080
* Moduł równoważenia obciążenia korzystający ze wszystkich tych obiektów

1. Utwórz reguły NAT.

    ```azurepowershell-interactive
    $inboundNATRule1v4 = New-AzLoadBalancerInboundNatRuleConfig -Name "NicNatRulev4" -FrontendIpConfiguration $FEIPConfigv4 -Protocol TCP -FrontendPort 443 -BackendPort 4443
    $inboundNATRule1v6 = New-AzLoadBalancerInboundNatRuleConfig -Name "NicNatRulev6" -FrontendIpConfiguration $FEIPConfigv6 -Protocol TCP -FrontendPort 443 -BackendPort 4443
    ```

2. Utwórz sondę kondycji. Istnieją dwie metody konfiguracji sondy:

    Sonda HTTP

    ```azurepowershell-interactive
    $healthProbe = New-AzLoadBalancerProbeConfig -Name 'HealthProbe-v4v6' -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2
    ```

    lub sondy TCP

    ```azurepowershell-interactive
    $healthProbe = New-AzLoadBalancerProbeConfig -Name 'HealthProbe-v4v6' -Protocol Tcp -Port 8080 -IntervalInSeconds 15 -ProbeCount 2
    $RDPprobe = New-AzLoadBalancerProbeConfig -Name 'RDPprobe' -Protocol Tcp -Port 3389 -IntervalInSeconds 15 -ProbeCount 2
    ```

    Na potrzeby tego przykładu będziemy używać sond protokołu TCP.

3. Utwórz regułę modułu równoważenia obciążenia.

    ```azurepowershell-interactive
    $lbrule1v4 = New-AzLoadBalancerRuleConfig -Name "HTTPv4" -FrontendIpConfiguration $FEIPConfigv4 -BackendAddressPool $backendpoolipv4 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 8080
    $lbrule1v6 = New-AzLoadBalancerRuleConfig -Name "HTTPv6" -FrontendIpConfiguration $FEIPConfigv6 -BackendAddressPool $backendpoolipv6 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 8080
    $RDPrule = New-AzLoadBalancerRuleConfig -Name "RDPrule" -FrontendIpConfiguration $FEIPConfigv4 -BackendAddressPool $backendpoolipv4 -Probe $RDPprobe -Protocol Tcp -FrontendPort 3389 -BackendPort 3389
    ```

4. Utwórz moduł równoważenia obciążenia przy użyciu wcześniej utworzonych obiektów.

    ```azurepowershell-interactive
    $NRPLB = New-AzLoadBalancer -ResourceGroupName NRP-RG -Name 'myNrpIPv6LB' -Location 'West US' -FrontendIpConfiguration $FEIPConfigv4,$FEIPConfigv6 -InboundNatRule $inboundNATRule1v6,$inboundNATRule1v4 -BackendAddressPool $backendpoolipv4,$backendpoolipv6 -Probe $healthProbe,$RDPprobe -LoadBalancingRule $lbrule1v4,$lbrule1v6,$RDPrule
    ```

## <a name="create-nics-for-the-back-end-vms"></a>Tworzenie kart sieciowych dla maszyn wirtualnych zaplecza

1. Pobierz Virtual Network i Virtual Network podsieć, w której należy utworzyć karty sieciowe.

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork -Name VNet -ResourceGroupName NRP-RG
    $backendSubnet = Get-AzVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
    ```

2. Utwórz konfiguracje IP i karty sieciowe dla maszyn wirtualnych.

    ```azurepowershell-interactive
    $nic1IPv4 = New-AzNetworkInterfaceIpConfig -Name "IPv4IPConfig" -PrivateIpAddressVersion "IPv4" -Subnet $backendSubnet -LoadBalancerBackendAddressPool $backendpoolipv4 -LoadBalancerInboundNatRule $inboundNATRule1v4
    $nic1IPv6 = New-AzNetworkInterfaceIpConfig -Name "IPv6IPConfig" -PrivateIpAddressVersion "IPv6" -LoadBalancerBackendAddressPool $backendpoolipv6 -LoadBalancerInboundNatRule $inboundNATRule1v6
    $nic1 = New-AzNetworkInterface -Name 'myNrpIPv6Nic0' -IpConfiguration $nic1IPv4,$nic1IPv6 -ResourceGroupName NRP-RG -Location 'West US'

    $nic2IPv4 = New-AzNetworkInterfaceIpConfig -Name "IPv4IPConfig" -PrivateIpAddressVersion "IPv4" -Subnet $backendSubnet -LoadBalancerBackendAddressPool $backendpoolipv4
    $nic2IPv6 = New-AzNetworkInterfaceIpConfig -Name "IPv6IPConfig" -PrivateIpAddressVersion "IPv6" -LoadBalancerBackendAddressPool $backendpoolipv6
    $nic2 = New-AzNetworkInterface -Name 'myNrpIPv6Nic1' -IpConfiguration $nic2IPv4,$nic2IPv6 -ResourceGroupName NRP-RG -Location 'West US'
    ```

## <a name="create-virtual-machines-and-assign-the-newly-created-nics"></a>Tworzenie maszyn wirtualnych i przypisywanie nowo utworzonych kart sieciowych

Aby uzyskać więcej informacji o tworzeniu maszyny wirtualnej, zobacz [Tworzenie i prekonfigurowanie maszyny wirtualnej z systemem Windows przy użyciu Menedżer zasobów i Azure PowerShell](../virtual-machines/windows/quick-create-powershell.md?toc=%2fazure%2fload-balancer%2ftoc.json)

1. Tworzenie zestawu dostępności i konta magazynu

    ```azurepowershell-interactive
    New-AzAvailabilitySet -Name 'myNrpIPv6AvSet' -ResourceGroupName NRP-RG -location 'West US'
    $availabilitySet = Get-AzAvailabilitySet -Name 'myNrpIPv6AvSet' -ResourceGroupName NRP-RG
    New-AzStorageAccount -ResourceGroupName NRP-RG -Name 'mynrpipv6stacct' -Location 'West US' -SkuName "Standard_LRS"
    $CreatedStorageAccount = Get-AzStorageAccount -ResourceGroupName NRP-RG -Name 'mynrpipv6stacct'
    ```

2. Tworzenie każdej maszyny wirtualnej i przypisywanie wcześniej utworzonych kart sieciowych

    ```azurepowershell-interactive
    $mySecureCredentials= Get-Credential -Message "Type the username and password of the local administrator account."

    $vm1 = New-AzVMConfig -VMName 'myNrpIPv6VM0' -VMSize 'Standard_G1' -AvailabilitySetId $availabilitySet.Id
    $vm1 = Set-AzVMOperatingSystem -VM $vm1 -Windows -ComputerName 'myNrpIPv6VM0' -Credential $mySecureCredentials -ProvisionVMAgent -EnableAutoUpdate
    $vm1 = Set-AzVMSourceImage -VM $vm1 -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm1 = Add-AzVMNetworkInterface -VM $vm1 -Id $nic1.Id -Primary
    $osDisk1Uri = $CreatedStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/myNrpIPv6VM0osdisk.vhd"
    $vm1 = Set-AzVMOSDisk -VM $vm1 -Name 'myNrpIPv6VM0osdisk' -VhdUri $osDisk1Uri -CreateOption FromImage
    New-AzVM -ResourceGroupName NRP-RG -Location 'West US' -VM $vm1

    $vm2 = New-AzVMConfig -VMName 'myNrpIPv6VM1' -VMSize 'Standard_G1' -AvailabilitySetId $availabilitySet.Id
    $vm2 = Set-AzVMOperatingSystem -VM $vm2 -Windows -ComputerName 'myNrpIPv6VM1' -Credential $mySecureCredentials -ProvisionVMAgent -EnableAutoUpdate
    $vm2 = Set-AzVMSourceImage -VM $vm2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm2 = Add-AzVMNetworkInterface -VM $vm2 -Id $nic2.Id -Primary
    $osDisk2Uri = $CreatedStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/myNrpIPv6VM1osdisk.vhd"
    $vm2 = Set-AzVMOSDisk -VM $vm2 -Name 'myNrpIPv6VM1osdisk' -VhdUri $osDisk2Uri -CreateOption FromImage
    New-AzVM -ResourceGroupName NRP-RG -Location 'West US' -VM $vm2
    ```


