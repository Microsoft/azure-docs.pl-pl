---
title: 'Azure VPN Gateway: ustawienia konfiguracji'
description: Informacje dotyczące VPN Gateway zasobów i ustawień dla sieci wirtualnej utworzonej w ramach modelu wdrażania Menedżer zasobów.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/21/2020
ms.author: cherylmc
ms.openlocfilehash: 1aba87b2139fb8a7d395fb3180d2074e47310fa9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96010879"
---
# <a name="about-vpn-gateway-configuration-settings"></a>Informacje o ustawieniach konfiguracji VPN Gateway

Brama VPN Gateway to typ bramy sieci wirtualnej, który wysyła zaszyfrowany ruch sieciowy między siecią wirtualną a lokalizacją lokalną w ramach połączenia publicznego. Bramy sieci VPN można również używać do wysyłania ruchu między sieciami wirtualnymi przez sieć szkieletową platformy Azure.

Połączenie bramy sieci VPN bazuje na konfiguracji wielu zasobów, z których każdy zawiera konfigurowalne ustawienia. W sekcjach w tym artykule omówiono zasoby i ustawienia, które odnoszą się do bramy sieci VPN dla sieci wirtualnej utworzonej w Menedżer zasobów model wdrażania. Opisy i diagramy topologii poszczególnych rozwiązań połączeń można znaleźć w artykule [Informacje o VPN Gateway](vpn-gateway-about-vpngateways.md) .

Wartości w tym artykule stosują bramy sieci VPN (bramy sieci wirtualnej, które używają sieci VPN-Gatewaytype). Ten artykuł nie obejmuje wszystkich typów bram ani bram nadmiarowych.

* Aby uzyskać wartości mające zastosowanie do-Gatewaytype "ExpressRoute", zobacz [Virtual Network Gateways for ExpressRoute](../expressroute/expressroute-about-virtual-network-gateways.md).

* W przypadku bram nadmiarowych strefy należy zapoznać się z tematem [about Zone-nadmiarowe bramy](about-zone-redundant-vnet-gateways.md).

* W przypadku wirtualnej sieci WAN zobacz [Informacje o wirtualnej sieci WAN](../virtual-wan/virtual-wan-about.md).

## <a name="gateway-types"></a><a name="gwtype"></a>Typy bram

Każda sieć wirtualna może mieć tylko jedną bramę sieci wirtualnej dla każdego typu. Podczas tworzenia bramy sieci wirtualnej należy upewnić się, że typ bramy jest prawidłowy dla danej konfiguracji.

Dostępne wartości dla-Gatewaytype to:

* Vpn
* ExpressRoute

Brama sieci VPN wymaga `-GatewayType` *sieci VPN*.

Przykład:

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased
```

## <a name="gateway-skus"></a><a name="gwsku"></a>Jednostki SKU bramy

[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

### <a name="configure-a-gateway-sku"></a>Konfigurowanie jednostki SKU bramy

**Witryna Azure Portal**

Jeśli używasz Azure Portal do utworzenia bramy sieci wirtualnej Menedżer zasobów, możesz wybrać jednostkę SKU bramy przy użyciu listy rozwijanej. Wyświetlane opcje odpowiadają wybranemu typowi bramy i typowi sieci VPN.

**Program PowerShell**

Poniższy przykład programu PowerShell określa `-GatewaySku` jako VpnGw1. W przypadku tworzenia bramy przy użyciu programu PowerShell należy najpierw utworzyć konfigurację adresu IP, a następnie użyć zmiennej, aby odwołać się do niej. W tym przykładzie zmienna konfiguracyjna jest $gwipconfig.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'US East' -IpConfigurations $gwipconfig -GatewaySku VpnGw1 `
-GatewayType Vpn -VpnType RouteBased
```

**Interfejs wiersza polecenia platformy Azure**

```azurecli
az network vnet-gateway create --name VNet1GW --public-ip-address VNet1GWPIP --resource-group TestRG1 --vnet VNet1 --gateway-type Vpn --vpn-type RouteBased --sku VpnGw1 --no-wait
```

###  <a name="resizing-or-changing-a-sku"></a><a name="resizechange"></a>Zmienianie rozmiarów lub zmiana w jednostce SKU

Jeśli masz bramę sieci VPN i chcesz korzystać z innej jednostki SKU bramy, opcje umożliwiają zmianę rozmiaru jednostki SKU bramy lub zmianę na inną jednostkę SKU. Po zmianie na inną jednostkę SKU bramy należy usunąć istniejącą bramę całkowicie i utworzyć nową. Kompilowanie bramy może potrwać do 45 minut. W porównaniu z rozmiarem jednostki SKU bramy nie ma dużo przestoju, ponieważ nie trzeba usuwać i ponownie kompilować bramy. Jeśli istnieje możliwość zmiany rozmiaru jednostki SKU bramy, zamiast jej zmiany należy to zrobić. Istnieją jednak reguły dotyczące zmiany rozmiarów:

1. Z wyjątkiem podstawowej jednostki SKU można zmienić rozmiar jednostki SKU bramy sieci VPN na inną jednostkę SKU bramy sieci VPN w ramach tej samej generacji (Generation1 lub Generation2). Na przykład VpnGw1 Generation1 można zmienić na VpnGw2 Generation1, ale nie do VpnGw2.
2. Podczas pracy ze starymi jednostkami SKU bramy można zmienić rozmiar, wybierając z opcji Basic, Standard i HighPerformance.
3. **Nie można** zmienić rozmiaru jednostek SKU Basic/Standard/HighPerformance na jednostki SKU VpnGw. Zamiast tego należy [zmienić](#change) na nowe jednostki SKU.

#### <a name="to-resize-a-gateway"></a><a name="resizegwsku"></a>Aby zmienić rozmiar bramy

**Witryna Azure Portal**

[!INCLUDE [Resize a SKU - portal](../../includes/vpn-gateway-resize-gw-portal-include.md)]

**Program PowerShell**

[!INCLUDE [Resize a SKU](../../includes/vpn-gateway-gwsku-resize-include.md)]

####  <a name="to-change-from-an-old-legacy-sku-to-a-new-sku"></a><a name="change"></a>Aby zmienić poprzednią (starszą) jednostkę SKU na nową jednostkę SKU

[!INCLUDE [Change a SKU](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="connection-types"></a><a name="connectiontype"></a>Typy połączeń

W modelu wdrażania Menedżer zasobów Każda konfiguracja wymaga określonego typu połączenia bramy sieci wirtualnej. Dostępne wartości opcji `-ConnectionType` w programie PowerShell w usłudze Resource Manager to:

* IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

W poniższym przykładzie programu PowerShell tworzymy połączenie S2S, które wymaga typu połączenia *IPSec*.

```azurepowershell-interactive
New-AzVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

## <a name="vpn-types"></a><a name="vpntype"></a>Typy sieci VPN

Podczas tworzenia bramy sieci wirtualnej dla konfiguracji bramy sieci VPN należy określić typ sieci VPN. Wybrany typ sieci VPN zależy od topologii połączenia, która ma zostać utworzona. Na przykład połączenie P2S wymaga typu sieci VPN RouteBased. Typ sieci VPN może także zależeć od używanego sprzętu. Konfiguracje S2S wymagają urządzenia sieci VPN. Niektóre urządzenia sieci VPN obsługują tylko określony typ sieci VPN.

Wybrany typ sieci VPN musi spełniać wszystkie wymagania dotyczące połączenia dla rozwiązania, które chcesz utworzyć. Jeśli na przykład chcesz utworzyć połączenie bramy sieci VPN S2S i połączenie bramy sieci VPN P2S dla tej samej sieci wirtualnej, użyj typu sieci VPN *RouteBased* , ponieważ P2S wymaga typu sieci VPN RouteBased. Należy również sprawdzić, czy urządzenie sieci VPN obsługuje połączenie sieci VPN RouteBased. 

Po utworzeniu bramy sieci wirtualnej nie można zmienić typu sieci VPN. Musisz usunąć bramę sieci wirtualnej i utworzyć nową. Istnieją dwa typy sieci VPN:

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

Poniższy przykład programu PowerShell określa `-VpnType` jako *RouteBased*. Podczas tworzenia bramy musisz upewnić się, że typ -VpnType jest prawidłowy dla danej konfiguracji.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig `
-GatewayType Vpn -VpnType RouteBased
```

## <a name="gateway-requirements"></a><a name="requirements"></a>Wymagania dotyczące bramy

[!INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]

## <a name="gateway-subnet"></a><a name="gwsub"></a>Podsieć bramy

Przed utworzeniem bramy sieci VPN należy utworzyć podsieć bramy. Podsieć bramy zawiera adresy IP używane przez maszyny wirtualne i usługi bramy sieci wirtualnej. Podczas tworzenia bramy sieci wirtualnej maszyny wirtualne bramy są wdrażane w podsieci bramy i konfigurowane przy użyciu wymaganych ustawień bramy sieci VPN. Nigdy nie należy wdrażać żadnych innych (na przykład dodatkowych maszyn wirtualnych) do podsieci bramy. Podsieć bramy musi mieć nazwę "GatewaySubnet", aby działała prawidłowo. Nazwa podsieci bramy "GatewaySubnet" pozwala platformie Azure wiedzieć, że jest to podsieć służąca do wdrażania maszyn wirtualnych i usług bramy sieci wirtualnej.

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

Podczas tworzenia podsieci bramy należy określić liczbę zawartych w niej adresów IP. Adresy IP w podsieci bramy są przydzieleni do maszyn wirtualnych bramy i usług bramy. Niektóre konfiguracje wymagają większej liczby adresów IP niż inne. 

Podczas planowania rozmiaru podsieci bramy zapoznaj się z dokumentacją dotyczącą konfiguracji, która ma zostać utworzona. Na przykład Konfiguracja współdzielenia ExpressRoute/VPN Gateway wymaga większej podsieci bramy niż większość innych konfiguracji. Ponadto warto upewnić się, że podsieć bramy zawiera wystarczającą liczbę adresów IP, aby uwzględnić możliwe przyszłe konfiguracje dodatkowe. Chociaż można utworzyć małą podsieć bramy (/29), zalecamy utworzenie podsieci bramy/27 lub większej (/27,/26 itp.), jeśli masz dostępną przestrzeń adresową. Obejmuje to większość konfiguracji.

Poniższy przykład Menedżer zasobów PowerShell przedstawia podsieć bramy o nazwie GatewaySubnet. W notacji CIDR można zobaczyć wartość/27, która pozwala na wystarczającą liczbę adresów IP dla większości konfiguracji, które obecnie istnieją.

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="local-network-gateways"></a><a name="lng"></a>Bramy sieci lokalnej

Brama sieci lokalnej różni się od bramy sieci wirtualnej. Podczas tworzenia konfiguracji bramy VPN brama sieci lokalnej zwykle reprezentuje sieć lokalną i odpowiednie urządzenie sieci VPN. W klasycznym modelu wdrażania brama sieci była określana jako lokacja lokalna.

Należy podać nazwę bramy sieci lokalnej, publiczny adres IP lub w pełni kwalifikowaną nazwę domeny (FQDN) lokalnego urządzenia sieci VPN, a także określić prefiksy adresów, które znajdują się w lokalizacji lokalnej. Na platformie Azure są sprawdzane prefiksy adresów docelowych dla ruchu sieciowego, zapoznaj się z konfiguracją określoną dla bramy sieci lokalnej i odpowiednio kieruj pakiety. W przypadku korzystania z Border Gateway Protocol (BGP) na urządzeniu sieci VPN należy podać adres IP elementu równorzędnego protokołu BGP urządzenia sieci VPN i numer systemu autonomicznego (ASN) sieci lokalnej. Należy również określić bramy sieci lokalnej dla konfiguracji Sieć wirtualna-sieć wirtualna, która korzysta z połączenia bramy sieci VPN.

Poniższy przykład programu PowerShell tworzy nową bramę sieci lokalnej:

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

Czasami trzeba zmodyfikować ustawienia bramy sieci lokalnej. Na przykład po dodaniu lub zmodyfikowaniu zakresu adresów lub jeśli adres IP urządzenia sieci VPN ulegnie zmianie. Zobacz [Modyfikowanie ustawień bramy sieci lokalnej przy użyciu programu PowerShell](vpn-gateway-modify-local-network-gateway.md).

## <a name="rest-apis-powershell-cmdlets-and-cli"></a><a name="resources"></a>Interfejsy API REST, polecenia cmdlet programu PowerShell i interfejs wiersza polecenia

Aby uzyskać dodatkowe zasoby techniczne i wymagania dotyczące składni w przypadku używania interfejsów API REST, poleceń cmdlet programu PowerShell lub interfejsu wiersza polecenia platformy Azure dla konfiguracji VPN Gateway, zobacz następujące strony:

| **Motyw** | **Resource Manager** |
| --- | --- |
| [Program PowerShell](/powershell/module/az.network/#networking) |[Program PowerShell](/powershell/module/az.network#vpn) |
| [Interfejs API REST](/previous-versions/azure/reference/jj154113(v=azure.100)) |[Interfejs API REST](/rest/api/network/virtualnetworkgateways) |
| Nieobsługiwane | [Interfejs wiersza polecenia platformy Azure](/cli/azure/network/vnet-gateway)|

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o konfiguracjach dostępnych połączeń, zobacz [Informacje o VPN Gateway](vpn-gateway-about-vpngateways.md).