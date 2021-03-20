---
title: Połączenia sieci VPN typu lokacja-lokacja za pośrednictwem prywatnej komunikacji równorzędnej ExpressRoute
description: Ten artykuł ułatwia włączenie sieci VPN typu lokacja-lokacja za pośrednictwem prywatnej komunikacji równorzędnej ExpressRoute w celu szyfrowania ruchu sieciowego.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/16/2020
ms.author: cherylmc
ms.openlocfilehash: 01d87bcb5697326fa87b25b20354897049900d9d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98880529"
---
# <a name="configure-a-site-to-site-vpn-connection-over-expressroute-private-peering"></a>Konfigurowanie połączenia sieci VPN typu lokacja-lokacja za pośrednictwem prywatnej komunikacji równorzędnej ExpressRoute

Sieć VPN typu lokacja-lokacja można skonfigurować dla bramy sieci wirtualnej za pośrednictwem prywatnej komunikacji równorzędnej ExpressRoute przy użyciu adresu IP RFC 1918. Ta konfiguracja zapewnia następujące korzyści:

* Ruch za pośrednictwem prywatnej komunikacji równorzędnej jest szyfrowany.

* Użytkownicy połączeń punkt-lokacja z bramą sieci wirtualnej mogą używać ExpressRoute (za pośrednictwem tunelu lokacja-lokacja), aby uzyskiwać dostęp do zasobów lokalnych.

>[!NOTE]
>Ta funkcja jest obsługiwana tylko w przypadku bram strefowo nadmiarowych. Na przykład VpnGw1AZ, VpnGw2AZ itd.
>

Aby ukończyć tę konfigurację, sprawdź, czy zostały spełnione następujące wymagania wstępne:

* Masz działający obwód ExpressRoute połączony z siecią wirtualną, w której Brama sieci VPN (lub zostanie utworzona).

* Możesz uzyskać dostęp do zasobów za pośrednictwem adresu IP RFC1918 (Private) w sieci wirtualnej za pośrednictwem obwodu ExpressRoute.

## <a name="routing"></a><a name="routing"></a>Routing

**Rysunek 1** przedstawia przykład łączności sieci VPN za pośrednictwem prywatnej komunikacji równorzędnej ExpressRoute. W tym przykładzie zostanie wyświetlona sieć w sieci lokalnej, która jest połączona z bramą sieci VPN usługi Azure Hub przez prywatną komunikację równorzędną ExpressRoute. Ważnym aspektem tej konfiguracji jest Routing między sieciami lokalnymi i platformą Azure przez ścieżki ExpressRoute i sieci VPN.

Rysunek 1.

:::image type="content" source="media/site-to-site-vpn-private-peering/routing.png" alt-text="Rysunek 1":::

Ustanawianie łączności jest proste:

1. Ustanów łączność ExpressRoute z obwodem usługi ExpressRoute i prywatną komunikację równorzędną.

1. Ustanów połączenie sieci VPN, wykonując kroki opisane w tym artykule.

### <a name="traffic-from-on-premises-networks-to-azure"></a>Ruch z sieci lokalnych do platformy Azure

W przypadku ruchu z sieci lokalnych na platformę Azure, prefiksy platformy Azure są anonsowane za pośrednictwem protokołu BGP prywatnej komunikacji równorzędnej ExpressRoute oraz protokołu BGP sieci VPN. Wynikiem są dwie trasy sieciowe (ścieżki) do platformy Azure z sieci lokalnych:

• Jedna trasa sieciowa za pośrednictwem ścieżki chronionej przez protokół IPsec.

• Jedna trasa sieciowa bezpośrednio za pośrednictwem usługi ExpressRoute bez ochrony IPsec.

Aby zastosować szyfrowanie do komunikacji, należy się upewnić, że dla sieci połączonej z siecią VPN na **rysunku 1** trasy platformy Azure za pośrednictwem lokalnej bramy sieci VPN są preferowane dla ścieżki Direct ExpressRoute.

### <a name="traffic-from-azure-to-on-premises-networks"></a>Ruch z platformy Azure do sieci lokalnych

To samo wymaganie dotyczy ruchu z platformy Azure do sieci lokalnych. Aby upewnić się, że ścieżka IPsec jest preferowana w ścieżce Direct ExpressRoute (bez protokołu IPsec), dostępne są dwie opcje:

• **Anonsowanie bardziej szczegółowych prefiksów w sesji protokołu BGP sieci VPN dla sieci połączonej z siecią VPN**. Można anonsować większy zakres obejmujący sieć podłączoną przez sieć VPN za pośrednictwem prywatnej komunikacji równorzędnej ExpressRoute, a następnie bardziej konkretne zakresy w sesji protokołu BGP sieci VPN. Na przykład Anonsuj 10.0.0.0/16 w ExpressRoute i 10.0.1.0/24 za pośrednictwem sieci VPN.

• **Anonsowanie rozłączonych prefiksów dla sieci VPN i ExpressRoute**. Jeśli zakresy sieci połączone z siecią VPN są rozłączone z innymi połączonymi sieciami ExpressRoute, można anonsować prefiksy odpowiednio w sesjach protokołu BGP sieci VPN i ExpressRoute. Na przykład Anonsuj 10.0.0.0/24 przez ExpressRoute i 10.0.1.0/24 za pośrednictwem sieci VPN.

W obu tych przykładach usługa Azure wyśle ruch do 10.0.1.0/24 przez połączenie sieci VPN, a nie bezpośrednio za pośrednictwem ExpressRoute bez ochrony sieci VPN.

>[!Warning]
>Jeśli anonsuje te same prefiksy dla połączeń ExpressRoute i VPN, >Azure będzie używać ścieżki ExpressRoute bezpośrednio bez ochrony sieci VPN.
>

## <a name="portal-steps"></a><a name="portal"></a>Kroki portalu

1. Skonfiguruj połączenie lokacja-lokacja. Kroki można znaleźć w artykule dotyczącym [konfiguracji lokacja-lokacja](./tutorial-site-to-site-portal.md) . Upewnij się, że wybrano strefowo nadmiarową jednostkę SKU bramy dla bramy. 

   Jednostki SKU nadmiarowe strefy mają "AZ" na końcu jednostki SKU. Na przykład **VpnGw1AZ**. Bramy strefowo nadmiarowych są dostępne tylko w regionach, w których dostępna jest usługa strefy dostępności. Aby uzyskać informacje o regionach, w których obsługiwane są strefy dostępności, zobacz [regiony obsługujące strefy dostępności](../availability-zones/az-region.md).

   :::image type="content" source="media/site-to-site-vpn-private-peering/gateway.png" alt-text="Prywatne adresy IP bramy":::
1. Włącz prywatne adresy IP na bramie. Wybierz pozycję **Konfiguracja**, a następnie ustaw opcję **prywatne adresy IP bramy** na **włączone**. Wybierz przycisk **Zapisz**, aby zapisać zmiany.
1. Na stronie **Przegląd** wybierz pozycję **Zobacz więcej** , aby wyświetlić prywatny adres IP. Zapisz te informacje do użycia w dalszej części kroków konfiguracyjnych.

   :::image type="content" source="media/site-to-site-vpn-private-peering/gateway-overview.png" alt-text="Strona omówienia" lightbox="media/site-to-site-vpn-private-peering/gateway-overview.png":::
1. Aby włączyć **Korzystanie z prywatnego adresu IP platformy Azure** w połączeniu, wybierz pozycję  **Konfiguracja**. Ustaw opcję **Użyj prywatnego adresu IP platformy Azure** , aby **włączyć**, a następnie wybierz pozycję **Zapisz**.

   :::image type="content" source="media/site-to-site-vpn-private-peering/connection.png" alt-text="Prywatne adresy IP bramy — włączone":::
1. Z poziomu zapory Wyślij polecenie ping do prywatnego adresu IP, który zapisano w kroku 3. Prywatny adres IP powinien być dostępny za pośrednictwem prywatnej komunikacji równorzędnej ExpressRoute.
1. Użyj tego prywatnego adresu IP jako zdalnego adresu IP w zaporze lokalnej, aby nawiązać tunel między lokacjami za pośrednictwem prywatnej komunikacji równorzędnej ExpressRoute.

## <a name="powershell-steps"></a><a name="powershell"></a>Kroki programu PowerShell

1. Skonfiguruj połączenie lokacja-lokacja. Kroki można znaleźć w artykule [Konfigurowanie sieci VPN typu lokacja-lokacja](./tutorial-site-to-site-portal.md) . Upewnij się, że wybrano strefowo nadmiarową jednostkę SKU bramy dla bramy. Jednostki SKU nadmiarowe strefy mają "AZ" na końcu jednostki SKU. Na przykład VpnGw1AZ.
1. Ustaw flagę w celu korzystania z prywatnego adresu IP w bramie przy użyciu następujących poleceń programu PowerShell:

   ```azurepowershell-interactive
   $Gateway = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroup <name of resource group>

   Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -EnablePrivateIpAddress $true
   ```

   Powinien zostać wyświetlony publiczny i prywatny adres IP. Zapisz adres IP w sekcji "TunnelIpAddresses" w danych wyjściowych. Te informacje będą używane w późniejszym kroku.
1. Ustaw połączenie do korzystania z prywatnego adresu IP za pomocą następującego polecenia programu PowerShell:

   ```azurepowershell-interactive
   $Connection = get-AzVirtualNetworkGatewayConnection -Name <name of the connection> -ResourceGroupName <name of resource group>

   Set-AzVirtualNetworkGatewayConnection --VirtualNetworkGatewayConnection $Connection -UseLocalAzureIpAddress $true
   ```
1. Z poziomu zapory Wyślij polecenie ping do prywatnego adresu IP, który zapisano w kroku 2. Powinien być dostępny za pośrednictwem prywatnej komunikacji równorzędnej ExpressRoute.
1. Użyj tego prywatnego adresu IP jako zdalnego adresu IP w zaporze lokalnej, aby nawiązać tunel między lokacjami za pośrednictwem prywatnej komunikacji równorzędnej ExpressRoute.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat VPN Gateway, zobacz [co to jest VPN Gateway?](vpn-gateway-about-vpngateways.md)