---
title: dołączanie pliku
description: dołączanie pliku
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 02/21/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 922ac7eb6cb9676af65700a6a2fe7fbae35a0dc5
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "67183764"
---
Kroki dla tego zadania używają sieci wirtualnej na podstawie wartości z poniższej listy odniesienia konfiguracji. Dodatkowe ustawienia i nazwy są również opisane na tej liście. Nie korzystamy z tej listy bezpośrednio w żadnym z kroków, chociaż dodajemy zmienne na podstawie wartości z tej listy. Możesz skopiować listę do użycia jako odwołanie, zastępując wartości własnymi.

* Virtual Network Name = "TestVNet"
* Virtual Network przestrzeń adresowa = 192.168.0.0/16
* Grupa zasobów = "TestRG"
* Subnet1 Name = "fronton" 
* Subnet1 przestrzeń adresów = "192.168.1.0/24"
* Nazwa podsieci bramy: "GatewaySubnet" zawsze należy nazwać sieci bramy *GatewaySubnet*.
* Przestrzeń adresowa podsieci bramy = "192.168.200.0/26"
* Region = "Wschodnie stany USA"
* Nazwa bramy = "GW"
* Nazwa IP bramy = "GWIP"
* Nazwa konfiguracji adresu IP bramy = "gwipconf"
* Type = "ExpressRoute" Ten typ jest wymagany w przypadku konfiguracji ExpressRoute.
* Publiczna nazwa IP bramy = "gwpip"

## <a name="add-a-gateway"></a>Dodawanie bramy
1. Połącz się z subskrypcją platformy Azure.

   [!INCLUDE [Sign in](expressroute-cloud-shell-connect.md)]
2. Zadeklaruj zmienne dla tego ćwiczenia. Należy pamiętać, aby edytować przykład w celu odzwierciedlenia ustawień, które mają być używane.

   ```azurepowershell-interactive 
   $RG = "TestRG"
   $Location = "East US"
   $GWName = "GW"
   $GWIPName = "GWIP"
   $GWIPconfName = "gwipconf"
   $VNetName = "TestVNet"
   ```
3. Zapisz obiekt sieci wirtualnej jako zmienną.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   ```
4. Dodaj podsieć bramy do Virtual Network. Podsieć bramy musi mieć nazwę "GatewaySubnet". Należy utworzyć podsieć bramy o wielkości/27 lub większej (/26,/25 itp.).

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
   ```
5. Ustaw konfigurację.

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
6. Przechowaj podsieć bramy jako zmienną.

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
   ```
7. Prześlij żądanie dotyczące publicznego adresu IP. Wymagany jest adres IP przed utworzeniem bramy. Nie można określić adresu IP, który ma być używany; jest on przydzielany dynamicznie. Ten adres IP zostanie użyty w następnej sekcji konfiguracji. Metodę AllocationMethod jako musi być dynamiczny.

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   ```
8. Utwórz konfigurację bramy. W ramach konfiguracji bramy zostaje zdefiniowana podsieć i publiczny adres IP do użycia. W tym kroku określisz konfigurację, która będzie używana podczas tworzenia bramy. Ten krok nie powoduje utworzenia obiektu bramy. Poniższy przykład umożliwia utworzenie konfiguracji bramy.

   ```azurepowershell-interactive
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```
9. Utwórz bramę. W tym kroku wartość **-gatewaytype** jest szczególnie ważna. Musisz użyć wartości **ExpressRoute**. Po uruchomieniu tych poleceń cmdlet Brama może zająć do 45 minut lub dłużej.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard
   ```

## <a name="verify-the-gateway-was-created"></a>Sprawdź, czy brama została utworzona
Użyj następujących poleceń, aby sprawdzić, czy brama została utworzona:

```azurepowershell-interactive
Get-AzVirtualNetworkGateway -ResourceGroupName $RG
```

## <a name="resize-a-gateway"></a>Zmienianie rozmiaru bramy
Istnieje wiele [jednostek SKU bramy](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Aby zmienić jednostkę SKU bramy w dowolnym momencie, można użyć poniższego polecenia.

> [!IMPORTANT]
> To polecenie nie działa w przypadku bramy UltraPerformance. Aby zmienić bramę na bramę UltraPerformance, najpierw usuń istniejącą bramę ExpressRoute, a następnie utwórz nową bramę UltraPerformance. Aby obniżyć poziom bramy z bramy UltraPerformance, najpierw usuń bramę UltraPerformance, a następnie utwórz nową bramę.
> 
> 

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="remove-a-gateway"></a>Usuwanie bramy
Aby usunąć bramę, użyj następującego polecenia:

```azurepowershell-interactive
Remove-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
```
