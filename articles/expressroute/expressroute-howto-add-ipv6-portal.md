---
title: 'Azure ExpressRoute: Dodawanie obsługi protokołu IPv6 przy użyciu Azure Portal'
description: Dowiedz się, jak dodać obsługę protokołu IPv6 w celu nawiązania połączenia z wdrożeniami platformy Azure przy użyciu Azure Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/09/2021
ms.author: duau
ms.openlocfilehash: 7f5afc05a8d03d33366a2f76318bcf5e039d4d30
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105561665"
---
# <a name="add-ipv6-support-for-private-peering-using-the-azure-portal-preview"></a>Dodawanie obsługi protokołu IPv6 dla prywatnej komunikacji równorzędnej przy użyciu Azure Portal (wersja zapoznawcza)

W tym artykule opisano sposób dodawania obsługi protokołu IPv6 w celu nawiązania połączenia za pośrednictwem ExpressRoute z zasobami na platformie Azure przy użyciu Azure Portal. 

> [!Note]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej w [regionach platformy Azure z strefy dostępności](../availability-zones/az-region.md#azure-regions-with-availability-zones). Obwód usługi ExpressRoute może być w związku z tym tworzony przy użyciu dowolnej lokalizacji komunikacji równorzędnej, ale wdrożenia oparte na protokole IPv6, z którymi nawiązuje połączenie, muszą znajdować się w regionie z Strefy dostępności.

## <a name="register-for-public-preview"></a>Zarejestruj się w celu uzyskania publicznej wersji zapoznawczej
Przed dodaniem obsługi protokołu IPv6 należy najpierw zarejestrować swoją subskrypcję. Aby zarejestrować się, uruchom następujące polecenia za pośrednictwem Azure PowerShell:

1.  Zaloguj się do platformy Azure i wybierz subskrypcję. Uruchom te polecenia dla subskrypcji zawierającej obwód usługi ExpressRoute oraz subskrypcję zawierającą wdrożenia platformy Azure (jeśli są różne).

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

1. Zarejestruj swoją subskrypcję dla publicznej wersji zapoznawczej przy użyciu następującego polecenia:
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowIpv6PrivatePeering -ProviderNamespace Microsoft.Network
    ```

Twoje żądanie zostanie zatwierdzone przez zespół ExpressRoute w ciągu 2-3 dni roboczych.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

W przeglądarce przejdź do [Azure Portal](https://portal.azure.com), a następnie zaloguj się przy użyciu konta platformy Azure.

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>Dodawanie prywatnej komunikacji równorzędnej IPv6 do obwodu ExpressRoute

1. [Utwórz obwód ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) lub przejdź do istniejącego obwodu, który chcesz zmienić.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-circuit.png" alt-text="Zrzut ekranu przedstawiający listę obwodów ExpressRoute.":::

1. Wybierz konfigurację **prywatnej** komunikacji równorzędnej Azure.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-peering.png" alt-text="Zrzut ekranu przedstawiający stronę przegląd ExpressRoute.":::

1. Dodaj prywatną komunikację równorzędną IPv6 do istniejącej konfiguracji prywatnej komunikacji równorzędnej IPv4, wybierając opcję "oba" dla **podsieci** lub Włącz prywatną komunikację równorzędną IPv6 w nowym obwodzie, wybierając pozycję "IPv6". Podaj parę podsieci IPv6/126, które są używane dla linku podstawowego i linki pomocnicze. Z każdej z tych podsieci przypiszesz do routera pierwszy możliwy do użycia adres IP, ponieważ firma Microsoft używa drugiego adresu IP do użycia dla jego routera. **Zapisz** konfigurację komunikacji równorzędnej po określeniu wszystkich parametrów.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-peering.png" alt-text="Zrzut ekranu przedstawiający Dodawanie protokołu IPv6 na stronie prywatnej komunikacji równorzędnej.":::

1. Po pomyślnym zaakceptowaniu konfiguracji powinna zostać wyświetlona wartość podobna do poniższego przykładu.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/view-ipv6-peering.png" alt-text="Zrzut ekranu protokołu IPv6 skonfigurowany do prywatnej komunikacji równorzędnej.":::

## <a name="update-your-connection-to-an-existing-virtual-network"></a>Aktualizowanie połączenia z istniejącą siecią wirtualną

Wykonaj poniższe czynności, jeśli masz istniejące środowisko zasobów platformy Azure w regionie z Strefy dostępnościem, że chcesz użyć prywatnej komunikacji równorzędnej protokołu IPv6 z usługą.

1. Przejdź do sieci wirtualnej, z którą jest połączony obwód ExpressRoute.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-vnet.png" alt-text="Zrzut ekranu przedstawiający listę sieci wirtualnych.":::

1. Przejdź do karty **przestrzeń adresowa** i Dodaj przestrzeń adresową IPv6 do sieci wirtualnej. **Zapisz** przestrzeń adresową.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-space.png" alt-text="Zrzut ekranu przedstawiający Dodawanie przestrzeni adresów IPv6 do sieci wirtualnej.":::

1. Przejdź do karty **podsieci** i wybierz pozycję **GatewaySubnet**. Zaznacz pole wyboru **Dodaj przestrzeń adresową IPv6** i podaj przestrzeń adresową IPv6 dla podsieci. Podsieć IPv6 bramy powinna mieć wartość/64 lub większą. **Zapisz** konfigurację po określeniu wszystkich parametrów.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-gateway-space.png" alt-text="Zrzut ekranu przedstawiający Dodawanie przestrzeni adresów IPv6 do podsieci.":::

1. Jeśli masz istniejącą strefowo nadmiarową bramę, przejdź do bramy usługi ExpressRoute i Odśwież zasób w celu włączenia łączności IPv6. W przeciwnym razie [Utwórz bramę sieci wirtualnej](expressroute-howto-add-gateway-portal-resource-manager.md) za pomocą jednostki SKU nadmiarowej strefy (ErGw1AZ, ErGw2AZ, ErGw3AZ). Jeśli planujesz używać FastPath, użyj ErGw3AZ.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/refresh-gateway.png" alt-text="Zrzut ekranu przedstawiający odświeżanie bramy.":::

## <a name="create-a-connection-to-a-new-virtual-network"></a>Utwórz połączenie z nową siecią wirtualną

Wykonaj poniższe czynności, jeśli planujesz nawiązać połączenie z nowym zestawem zasobów platformy Azure w regionie z Strefy dostępności przy użyciu prywatnej komunikacji równorzędnej IPv6.

1. Utwórz sieć wirtualną o podwójnej stercie z przestrzenią adresową IPv4 i IPv6. Aby uzyskać więcej informacji, zobacz [Tworzenie sieci wirtualnej](../virtual-network/quick-create-portal.md#create-a-virtual-network).

1. [Utwórz podsieć bramy o podwójnym stosie](expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet).

1. [Utwórz bramę sieci wirtualnej](expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway) za pomocą jednostki SKU nadmiarowej strefy (ErGw1AZ, ErGw2AZ, ErGw3AZ). Jeśli planujesz używać FastPath, użyj ErGw3AZ (należy zauważyć, że ta opcja jest dostępna tylko dla obwodów korzystających z usługi ExpressRoute Direct).

1. [Połącz sieć wirtualną z obwodem ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md).

## <a name="limitations"></a>Ograniczenia
Gdy obsługa protokołu IPv6 jest dostępna dla połączeń z wdrożeniami w regionach z Strefy dostępności, nie obsługuje następujących przypadków użycia:

* Połączenia z wdrożeniami na platformie Azure za pośrednictwem jednostki SKU bramy ExpressRoute
* Połączenia z wdrożeniami w regionach innych niż AZ
* Global Reach połączenia między obwodymi usługi ExpressRoute
* Korzystanie z ExpressRoute z wirtualną siecią WAN
* FastPath z obwodami bezpośrednimi innymi niż ExpressRoute
* FastPath z obwodami w następujących lokalizacjach komunikacji równorzędnej: Dubaj
* Współistnienie z VPN Gateway

## <a name="next-steps"></a>Następne kroki

Aby rozwiązać problemy z ExpressRoute, zobacz następujące artykuły:

* [Weryfikowanie połączenia usługi ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Rozwiązywanie problemów z wydajnością sieci](expressroute-troubleshooting-network-performance.md)