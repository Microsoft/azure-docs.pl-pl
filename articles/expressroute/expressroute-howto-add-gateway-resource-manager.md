---
title: 'Samouczek — Azure ExpressRoute: Dodawanie bramy do sieci wirtualnej — Azure PowerShell'
description: Ten samouczek ułatwia dodanie bramy sieci wirtualnej do już utworzonej Menedżer zasobów sieci wirtualnej dla usługi ExpressRoute przy użyciu Azure PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/05/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 9f01961ec7c7f8e0a4e2d72e28e6def50e93ad5d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91854311"
---
# <a name="tutorial-configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Samouczek: Konfigurowanie bramy sieci wirtualnej dla usługi ExpressRoute przy użyciu programu PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager — witryna Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager — program PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klasyczny — PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Wideo — Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 

Ten samouczek ułatwia dodawanie, zmienianie rozmiaru i usuwanie bramy sieci wirtualnej (VNet) dla istniejącej sieci wirtualnej. Kroki tej konfiguracji dotyczą sieci wirtualnych, które zostały utworzone przy użyciu modelu wdrażania Menedżer zasobów na potrzeby konfiguracji ExpressRoute. Aby uzyskać więcej informacji, zobacz [Informacje o bramach sieci wirtualnej dla usługi ExpressRoute](expressroute-about-virtual-network-gateways.md).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> - Utwórz podsieć bramy.
> - Utwórz bramę Virtual Network.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="configuration-reference-list"></a>Lista odwołań do konfiguracji

Kroki dla tego zadania używają sieci wirtualnej na podstawie wartości z poniższej listy odniesienia konfiguracji. Dodatkowe ustawienia i nazwy są również opisane na tej liście. Nie korzystamy z tej listy bezpośrednio w żadnym z kroków, chociaż dodajemy zmienne na podstawie wartości z tej listy. Możesz skopiować listę do użycia jako odwołanie, zastępując wartości własnymi.

| Ustawienie                   | Wartość                                              |
| ---                       | ---                                                |
| Nazwa Virtual Network | *TestVNet* |    
| Przestrzeń adresów Virtual Network | *192.168.0.0/16* |
| Grupa zasobów | *TestRG* |
| Nazwa Subnet1 | *Frontonu* |   
| Przestrzeń adresowa Subnet1 | *192.168.1.0/24* |
| Nazwa Subnet1 | *Frontonu* |
| Nazwa podsieci bramy | *GatewaySubnet* |    
| Przestrzeń adresowa podsieci bramy | *192.168.200.0/26* |
| Region | *East US* |
| Nazwa bramy | *GW* |   
| Nazwa IP bramy | *GWIP* |
| Nazwa konfiguracji adresu IP bramy | *gwipconf* |
| Typ | *ExpressRoute* |
| Publiczna nazwa IP bramy  | *gwpip* |

## <a name="add-a-gateway"></a>Dodawanie bramy

1. Aby nawiązać połączenie z platformą Azure, uruchom polecenie `Connect-AzAccount` .

1. Zadeklaruj zmienne dla tego ćwiczenia. Należy pamiętać, aby edytować przykład w celu odzwierciedlenia ustawień, które mają być używane.

   ```azurepowershell-interactive 
   $RG = "TestRG"
   $Location = "East US"
   $GWName = "GW"
   $GWIPName = "GWIP"
   $GWIPconfName = "gwipconf"
   $VNetName = "TestVNet"
   ```
1. Zapisz obiekt sieci wirtualnej jako zmienną.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   ```
1. Dodaj podsieć bramy do Virtual Network. Podsieć bramy musi mieć nazwę "GatewaySubnet". Podsieć bramy musi mieć wartość/27 lub większą (/26,/25 itd.).

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
   ```
1. Ustaw konfigurację.

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
1. Przechowaj podsieć bramy jako zmienną.

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
   ```
1. Prześlij żądanie dotyczące publicznego adresu IP. Wymagany jest adres IP przed utworzeniem bramy. Nie można określić adresu IP, który ma być używany; jest on dynamicznie przypisywany. Ten adres IP zostanie użyty w następnej sekcji konfiguracji. Metodę AllocationMethod jako musi być dynamiczny.

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   ```
1. Utwórz konfigurację bramy. W ramach konfiguracji bramy zostaje zdefiniowana podsieć i publiczny adres IP do użycia. W tym kroku określisz konfigurację, która będzie używana podczas tworzenia bramy. Poniższy przykład umożliwia utworzenie konfiguracji bramy.

   ```azurepowershell-interactive
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```
1. Utwórz bramę. W tym kroku wartość **-gatewaytype** jest szczególnie ważna. Musisz użyć wartości **ExpressRoute**. Po uruchomieniu tych poleceń cmdlet Brama może zająć do 45 minut lub dłużej.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard
   ```

## <a name="verify-the-gateway-was-created"></a>Sprawdź, czy brama została utworzona
Użyj następujących poleceń, aby sprawdzić, czy brama została utworzona:

```azurepowershell-interactive
Get-AzVirtualNetworkGateway -ResourceGroupName $RG
```

## <a name="resize-a-gateway"></a>Zmienianie rozmiaru bramy
Istnieje wiele [jednostek SKU bramy](expressroute-about-virtual-network-gateways.md). Aby zmienić jednostkę SKU bramy w dowolnym momencie, można użyć poniższego polecenia.

> [!IMPORTANT]
> To polecenie nie działa w przypadku bramy UltraPerformance. Aby zmienić bramę na bramę UltraPerformance, najpierw usuń istniejącą bramę ExpressRoute, a następnie utwórz nową bramę UltraPerformance. Aby obniżyć poziom bramy z bramy UltraPerformance, najpierw usuń bramę UltraPerformance, a następnie utwórz nową bramę.
> 

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów
Aby usunąć bramę, użyj następującego polecenia:

```azurepowershell-interactive
Remove-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
```

## <a name="next-steps"></a>Następne kroki
Po utworzeniu bramy sieci wirtualnej można połączyć sieć wirtualną z obwodem ExpressRoute. 

> [!div class="nextstepaction"]
> [Łączenie Virtual Network z obwodem ExpressRoute](expressroute-howto-linkvnet-arm.md)
