---
title: Używanie tras niestandardowych platformy Azure w celu włączenia aktywacji usługi KMS z wymuszonym tunelowaniem | Microsoft Docs
description: Pokazuje, jak używać niestandardowych tras platformy Azure, aby włączyć aktywację usługi KMS podczas korzystania z wymuszonego tunelowania na platformie Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 12/20/2018
ms.author: genli
ms.openlocfilehash: 1c2050969e95b521554bba100b688add3a987a80
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86526745"
---
# <a name="windows-activation-fails-in-forced-tunneling-scenario"></a>Aktywacja systemu Windows kończy się niepowodzeniem w scenariuszu wymuszonego tunelowania

W tym artykule opisano sposób rozwiązywania problemu z aktywacją usługi KMS, który może wystąpić po włączeniu wymuszonego tunelowania w scenariuszach połączenia sieci VPN typu lokacja-lokacja lub ExpressRoute.

## <a name="symptom"></a>Objaw

[Wymuszone tunelowanie](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) można włączyć w podsieciach sieci wirtualnej platformy Azure, aby skierować cały ruch związany z Internetem z powrotem do sieci lokalnej. W tym scenariuszu nie można aktywować systemu Windows na maszynach wirtualnych platformy Azure z systemem Windows.

## <a name="cause"></a>Przyczyna

Maszyny wirtualne z systemem Windows Azure muszą nawiązać połączenie z serwerem usługi Azure KMS w celu aktywacji systemu Windows. Aktywacja wymaga, aby żądanie aktywacji pochodzi z publicznego adresu IP platformy Azure. W scenariuszu wymuszonego tunelowania aktywacja nie powiedzie się, ponieważ żądanie aktywacji pochodzi z sieci lokalnej zamiast z publicznego adresu IP platformy Azure.

## <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, Użyj niestandardowej trasy platformy Azure do kierowania ruchu aktywacji do serwera usługi Azure KMS.

Adres IP serwera usługi KMS dla chmury globalnej platformy Azure to 23.102.135.246. Nazwa DNS to kms.core.windows.net. Jeśli używasz innych platform platformy Azure, takich jak Azure (Niemcy), musisz użyć adresu IP odpowiedniego serwera usługi KMS. Aby uzyskać więcej informacji, zobacz następującą tabelę:

|Platforma| USŁUGA KMS DNS|ADRES IP USŁUGI KMS|
|------|-------|-------|
|Globalne platformy Azure|kms.core.windows.net|23.102.135.246|
|Azure (Niemcy)|kms.core.cloudapi.de|51.4.143.248|
|Wersja platformy Azure dla administracji USA|kms.core.usgovcloudapi.net|23.97.0.13|
|Azure w Chinach — 21Vianet|kms.core.chinacloudapi.cn|42.159.7.249|


Aby dodać trasę niestandardową, wykonaj następujące kroki:

### <a name="for-resource-manager-vms"></a>Dla maszyn wirtualnych Menedżer zasobów

 

> [!NOTE] 
> Aktywacja używa publicznych adresów IP i ma wpływ na konfigurację Load Balancer standardowej jednostki SKU. Uważnie Przejrzyj [połączenia wychodzące na platformie Azure](../../load-balancer/load-balancer-outbound-connections.md) , aby dowiedzieć się więcej o wymaganiach.

1. Otwórz Azure PowerShell, a następnie [Zaloguj się do swojej subskrypcji platformy Azure](/powershell/azure/authenticate-azureps).
2. Uruchom następujące polecenia:

    ```powershell
    # First, get the virtual network that hosts the VMs that have activation problems. In this case, we get virtual network ArmVNet-DM in Resource Group ArmVNet-DM:

    $vnet = Get-AzVirtualNetwork -ResourceGroupName "ArmVNet-DM" -Name "ArmVNet-DM"

    # Next, create a route table and specify that traffic bound to the KMS IP (23.102.135.246) will go directly out:

    $RouteTable = New-AzRouteTable -Name "ArmVNet-DM-KmsDirectRoute" -ResourceGroupName "ArmVNet-DM" -Location "centralus"

    Add-AzRouteConfig -Name "DirectRouteToKMS" -AddressPrefix 23.102.135.246/32 -NextHopType Internet -RouteTable $RouteTable

    Set-AzRouteTable -RouteTable $RouteTable

    # Next, attach the route table to the subnet that hosts the VMs

    Set-AzVirtualNetworkSubnetConfig -Name "Subnet01" -VirtualNetwork $vnet -AddressPrefix "10.0.0.0/24" -RouteTable $RouteTable

    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```
3. Przejdź do maszyny wirtualnej, która ma problemy z aktywacją. Użyj [PsPing](/sysinternals/downloads/psping) , aby sprawdzić, czy może nawiązać połączenie z serwerem KMS:

    ```console
    psping kms.core.windows.net:1688
    ```

4. Spróbuj aktywować system Windows i sprawdź, czy problem został rozwiązany.

### <a name="for-classic-vms"></a>Dla klasycznych maszyn wirtualnych

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

1. Otwórz Azure PowerShell, a następnie [Zaloguj się do swojej subskrypcji platformy Azure](/powershell/azure/authenticate-azureps).
2. Uruchom następujące polecenia:

    ```powershell
    # First, create a new route table:
    New-AzureRouteTable -Name "VNet-DM-KmsRouteGroup" -Label "Route table for KMS" -Location "Central US"

    # Next, get the route table that was created:
    $rt = Get-AzureRouteTable -Name "VNet-DM-KmsRouteTable"

    # Next, create a route:
    Set-AzureRoute -RouteTable $rt -RouteName "AzureKMS" -AddressPrefix "23.102.135.246/32" -NextHopType Internet

    # Apply the KMS route table to the subnet that hosts the problem VMs (in this case, we apply it to the subnet that's named Subnet-1):
    Set-AzureSubnetRouteTable -VirtualNetworkName "VNet-DM" -SubnetName "Subnet-1" 
    -RouteTableName "VNet-DM-KmsRouteTable"
    ```

3. Przejdź do maszyny wirtualnej, która ma problemy z aktywacją. Użyj [PsPing](/sysinternals/downloads/psping) , aby sprawdzić, czy może nawiązać połączenie z serwerem KMS:

    ```console
    psping kms.core.windows.net:1688
    ```

4. Spróbuj aktywować system Windows i sprawdź, czy problem został rozwiązany.

## <a name="next-steps"></a>Następne kroki

- [Klucze instalacji klienta usługi KMS](/windows-server/get-started/kmsclientkeys)
- [Przeglądanie i wybieranie metod aktywacji](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/jj134256(v=ws.11))
