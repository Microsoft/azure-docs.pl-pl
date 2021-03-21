---
title: Równoważenie obciążenia dla wielu konfiguracji adresów IP — interfejs wiersza polecenia platformy Azure
titleSuffix: Azure Load Balancer
description: W tym artykule dowiesz się, jak równoważyć obciążenie w podstawowych i dodatkowych konfiguracjach IP przy użyciu interfejsu wiersza polecenia platformy Azure.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18, devx-track-azurecli
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: a2916f28be0b45eec6e9c1a85c0b8db3fb611381
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103417865"
---
# <a name="load-balancing-on-multiple-ip-configurations-using-powershell"></a>Równoważenie obciążenia dla wielu konfiguracji adresów IP przy użyciu programu PowerShell

> [!div class="op_single_selector"]
> * [Portal](load-balancer-multiple-ip.md)
> * [Interfejs wiersza polecenia](load-balancer-multiple-ip-cli.md)
> * [Program PowerShell](load-balancer-multiple-ip-powershell.md)

W tym artykule opisano, jak używać Azure Load Balancer z wieloma adresami IP w dodatkowym interfejsie sieciowym. W tym scenariuszu istnieją dwie maszyny wirtualne z systemem Windows, z których każdy ma podstawową i pomocniczą kartę sieciową. Każda z pomocniczych kart sieciowych ma dwie konfiguracje IP. Każda maszyna wirtualna hostuje zarówno witryny sieci Web contoso.com, jak i fabrikam.com. Każda witryna sieci Web jest powiązana z jedną z konfiguracji protokołu IP na pomocniczej karcie sieciowej. Używamy Azure Load Balancer, aby uwidocznić dwa adresy IP frontonu, jeden dla każdej witryny sieci Web, aby dystrybuować ruch do odpowiedniej konfiguracji protokołu IP dla witryny sieci Web. W tym scenariuszu jest stosowany ten sam numer portu dla obu frontonów, a także adresy IP puli zaplecza.

![Obraz scenariusza LB](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Procedura równoważenia obciążenia dla wielu konfiguracji adresów IP

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Postępuj zgodnie z poniższymi instrukcjami, aby osiągnąć scenariusz opisany w tym artykule:

1. Zainstaluj program Azure PowerShell. Zobacz [How to install and configure Azure PowerShell](/powershell/azure/) (Jak zainstalować i skonfigurować program Azure PowerShell), aby uzyskać informacje na temat instalowania najnowszej wersji programu Azure PowerShell, wybierania subskrypcji i logowania się do konta.
2. Utwórz grupę zasobów przy użyciu następujących ustawień:

    ```powershell
    $location = "westcentralus".
    $myResourceGroup = "contosofabrikam"
    ```

    Aby uzyskać więcej informacji, zobacz krok 2 [tworzenia grupy zasobów](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm?toc=%2fazure%2fload-balancer%2ftoc.json).

3. [Utwórz zestaw dostępności](../virtual-machines/windows/tutorial-availability-sets.md?toc=%2fazure%2fload-balancer%2ftoc.json) zawierający maszyny wirtualne. W tym scenariuszu Użyj następującego polecenia:

    ```powershell
    New-AzAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset" -Location "West Central US"
    ```

4. Wykonaj instrukcje od 3 do 5 w artykule [Tworzenie maszyny wirtualnej z systemem Windows](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm?toc=%2fazure%2fload-balancer%2ftoc.json) , aby przygotować Tworzenie maszyny wirtualnej z jedną kartą sieciową. Wykonaj krok 6,1 i użyj poniższego zamiast kroku 6,2:

    ```powershell
    $availset = Get-AzAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset"
    New-AzVMConfig -VMName "VM1" -VMSize "Standard_DS1_v2" -AvailabilitySetId $availset.Id
    ```

    Następnie ukończ [Tworzenie kroków maszyn wirtualnych z systemem Windows od](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm?toc=%2fazure%2fload-balancer%2ftoc.json) 6,3 do 6,8.

5. Dodaj drugą konfigurację adresu IP do każdej z maszyn wirtualnych. Postępuj zgodnie z instrukcjami w artykule [przypisywanie wielu adresów IP do maszyn wirtualnych](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md#add) . Użyj następujących ustawień konfiguracji:

    ```powershell
    $NicName = "VM1-NIC2"
    $RgName = "contosofabrikam"
    $NicLocation = "West Central US"
    $IPConfigName4 = "VM1-ipconfig2"
    $Subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $myVnet
    ```

    Na potrzeby tego samouczka nie ma potrzeby kojarzenia pomocniczych konfiguracji adresów IP z publicznymi adresami IPv4. Edytuj polecenie, aby usunąć część skojarzenia publicznego adresu IP.

6. Ponownie wykonaj kroki od 4 do 6 tego artykułu dla VM2. Pamiętaj o zamianie nazwy maszyny wirtualnej na VM2 podczas wykonywania tej operacji. Należy pamiętać, że nie trzeba tworzyć sieci wirtualnej dla drugiej maszyny wirtualnej. Nie można utworzyć nowej podsieci na podstawie przypadku użycia.

7. Utwórz dwa publiczne adresy IP i Zapisz je w odpowiednich zmiennych, jak pokazano poniżej:

    ```powershell
    $publicIP1 = New-AzPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel contoso
    $publicIP2 = New-AzPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel fabrikam

    $publicIP1 = Get-AzPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam
    $publicIP2 = Get-AzPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam
    ```

8. Utwórz dwie konfiguracje adresów IP frontonu:

    ```powershell
    $frontendIP1 = New-AzLoadBalancerFrontendIpConfig -Name contosofe -PublicIpAddress $publicIP1
    $frontendIP2 = New-AzLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2
    ```

9. Utwórz pule adresów zaplecza, sondę i reguły równoważenia obciążenia:

    ```powershell
    $beaddresspool1 = New-AzLoadBalancerBackendAddressPoolConfig -Name contosopool
    $beaddresspool2 = New-AzLoadBalancerBackendAddressPoolConfig -Name fabrikampool

    $healthProbe = New-AzLoadBalancerProbeConfig -Name HTTP -RequestPath 'index.html' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    $lbrule1 = New-AzLoadBalancerRuleConfig -Name HTTPc -FrontendIpConfiguration $frontendIP1 -BackendAddressPool $beaddresspool1 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    $lbrule2 = New-AzLoadBalancerRuleConfig -Name HTTPf -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    ```

10. Po utworzeniu tych zasobów Utwórz moduł równoważenia obciążenia:

    ```powershell
    $mylb = New-AzLoadBalancer -ResourceGroupName contosofabrikam -Name mylb -Location 'West Central US' -FrontendIpConfiguration $frontendIP1 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
    ```

11. Dodaj drugą pulę adresów zaplecza i konfigurację adresu IP frontonu do nowo utworzonego modułu równoważenia obciążenia:

    ```powershell
    $mylb = Get-AzLoadBalancer -Name "mylb" -ResourceGroupName $myResourceGroup | Add-AzLoadBalancerBackendAddressPoolConfig -Name fabrikampool | Set-AzLoadBalancer

    $mylb | Add-AzLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2 | Set-AzLoadBalancer
    
    Add-AzLoadBalancerRuleConfig -Name HTTP -LoadBalancer $mylb -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80 | Set-AzLoadBalancer
    ```

12. Poniższe polecenia umożliwiają uzyskanie kart sieciowych, a następnie dodanie obu konfiguracji IP do puli adresów zaplecza modułu równoważenia obciążenia:

    ```powershell
    $nic1 = Get-AzNetworkInterface -Name "VM1-NIC2" -ResourceGroupName "MyResourcegroup";
    $nic2 = Get-AzNetworkInterface -Name "VM2-NIC2" -ResourceGroupName "MyResourcegroup";

    $nic1.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic1.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);
    $nic2.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic2.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);

    $mylb = $mylb | Set-AzLoadBalancer

    $nic1 | Set-AzNetworkInterface
    $nic2 | Set-AzNetworkInterface
    ```

13. Na koniec należy skonfigurować rekordy zasobów DNS w taki sposób, aby wskazywały odpowiedni adres IP frontonu Load Balancer. Domeny mogą być hostowane w Azure DNS. Aby uzyskać więcej informacji o korzystaniu z Azure DNS z Load Balancer, zobacz [używanie Azure DNS z innymi usługami platformy Azure](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej na temat sposobu łączenia usług równoważenia obciążenia na platformie Azure [przy użyciu usług równoważenia obciążenia na platformie Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Dowiedz się, jak używać różnych typów dzienników na platformie Azure do zarządzania modułem równoważenia obciążenia i rozwiązywania problemów z nim w [Azure monitor dziennikach Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md).