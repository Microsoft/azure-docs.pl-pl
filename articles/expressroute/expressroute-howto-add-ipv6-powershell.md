---
title: 'Azure ExpressRoute: Dodawanie obsługi protokołu IPv6 przy użyciu Azure PowerShell'
description: Dowiedz się, jak dodać obsługę protokołu IPv6 w celu nawiązania połączenia z wdrożeniami platformy Azure przy użyciu Azure PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 2/9/2021
ms.author: duau
ms.openlocfilehash: 402714b55d7513e41458503b12c68768d0c6ad5e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101747017"
---
# <a name="add-ipv6-support-for-private-peering-using-azure-powershell-preview"></a>Dodawanie obsługi protokołu IPv6 do prywatnej komunikacji równorzędnej przy użyciu Azure PowerShell (wersja zapoznawcza)

W tym artykule opisano sposób dodawania obsługi protokołu IPv6 w celu nawiązania połączenia za pośrednictwem ExpressRoute z zasobami na platformie Azure przy użyciu Azure PowerShell.

> [!Note]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej w [regionach platformy Azure z strefy dostępności](https://docs.microsoft.com/azure/availability-zones/az-region#azure-regions-with-availability-zones). Obwód usługi ExpressRoute może być w związku z tym tworzony przy użyciu dowolnej lokalizacji komunikacji równorzędnej, ale wdrożenia oparte na protokole IPv6, z którymi nawiązuje połączenie, muszą znajdować się w regionie z Strefy dostępności.

## <a name="working-with-azure-powershell"></a>Praca z Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="register-for-public-preview"></a>Zarejestruj się w celu uzyskania publicznej wersji zapoznawczej
Przed dodaniem obsługi protokołu IPv6 należy najpierw zarejestrować swoją subskrypcję. Aby się zarejestrować, wykonaj następujące czynności za pośrednictwem Azure PowerShell:
1.  Zaloguj się do platformy Azure i wybierz subskrypcję. Musisz to zrobić dla subskrypcji zawierającej obwód usługi ExpressRoute, a także subskrypcji zawierającej wdrożenia platformy Azure (jeśli są różne).

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

2. Żądaj zarejestrowania subskrypcji w publicznej wersji zapoznawczej przy użyciu następującego polecenia:
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowIpv6PrivatePeering -ProviderNamespace Microsoft.Network
    ```

Twoje żądanie zostanie zatwierdzone przez zespół ExpressRoute w ciągu 2-3 dni roboczych.

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>Dodawanie prywatnej komunikacji równorzędnej IPv6 do obwodu ExpressRoute

1. [Utwórz obwód ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-arm) lub Użyj istniejącego obwodu. Pobierz obwód, uruchamiając polecenie **Get-AzExpressRouteCircuit** :

    ```azurepowershell-interactive
    $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
    ```

2. Pobierz prywatną konfigurację komunikacji równorzędnej dla obwodu, uruchamiając **Get-AzExpressRouteCircuitPeeringConfig**:

    ```azurepowershell-interactive
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
    ```

3. Dodaj prywatną komunikację równorzędną IPv6 do istniejącej konfiguracji prywatnej komunikacji równorzędnej IPv4. Podaj parę podsieci IPv6/126, które są używane dla linku podstawowego i linki pomocnicze. Z każdej z tych podsieci zostanie przypisany pierwszy, przydatny adres IP do routera, ponieważ firma Microsoft używa drugiego adresu IP do użycia dla jego routera.

> [!Note]
> Elementy równorzędne ASN i VlanId powinny być zgodne z ustawieniami w konfiguracji prywatnej komunikacji równorzędnej w protokole IPv4.

    ```azurepowershell-interactive
    Set-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 200 -PeerAddressType IPv6

    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
    ```

4. Po pomyślnym zapisaniu konfiguracji Pobierz obwód ponownie, uruchamiając polecenie **Get-AzExpressRouteCircuit** . Odpowiedź powinna wyglądać podobnie do poniższego przykładu:

    ```azurepowershell
    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : eastus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Washington DC",
                                         "BandwidthInMbps": 50
                                       }
    ExpressRoutePort                 : null
    BandwidthInGbps                  :
    Stag                             : 29
    ServiceKey                       : **************************************
    Peerings                         : [
                                         {
                                           "Name": "AzurePrivatePeering",
                                           "Etag": "W/\"facc8972-995c-4861-a18d-9a82aaa7167e\"",
                                           "Id": "/subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/AzurePrivatePeering",
                                           "PeeringType": "AzurePrivatePeering",
                                           "State": "Enabled",
                                           "AzureASN": 12076,
                                           "PeerASN": 100,
                                           "PrimaryPeerAddressPrefix": "192.168.15.16/30",
                                           "SecondaryPeerAddressPrefix": "192.168.15.20/30",
                                           "PrimaryAzurePort": "",
                                           "SecondaryAzurePort": "",
                                           "VlanId": 200,
                                           "ProvisioningState": "Succeeded",
                                           "GatewayManagerEtag": "",
                                           "LastModifiedBy": "Customer",
                                           "Ipv6PeeringConfig": {
                                             "State": "Enabled",
                                             "PrimaryPeerAddressPrefix": "3FFE:FFFF:0:CD30::/126",
                                             "SecondaryPeerAddressPrefix": "3FFE:FFFF:0:CD30::4/126"
                                           },
                                           "Connections": [],
                                           "PeeredConnections": []
                                         },
                                       ]
    Authorizations                   : []
    AllowClassicOperations           : False
    GatewayManagerEtag               :
    ```

## <a name="update-your-connection-to-an-existing-virtual-network"></a>Aktualizowanie połączenia z istniejącą siecią wirtualną

Wykonaj poniższe czynności, jeśli masz istniejące środowisko zasobów platformy Azure w regionie zawierającym Strefy dostępności, do których chcesz użyć prywatnej komunikacji równorzędnej IPv6 z.

1. Pobierz sieć wirtualną, z którą jest połączony obwód ExpressRoute.

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork -Name "VirtualNetwork" -ResourceGroupName "ExpressRouteResourceGroup"
    ```

2. Dodaj przestrzeń adresową IPv6 do sieci wirtualnej.

    ```azurepowershell-interactive
    $vnet.AddressSpace.AddressPrefixes.add("ace:daa:daaa:deaa::/64")
    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```

3. Dodaj przestrzeń adresową IPv6 do podsieci bramy. Podsieć IPv6 bramy powinna mieć wartość/64 lub większą.

    ```azurepowershell-interactive
    Set-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix "10.0.0.0/26", "ace:daa:daaa:deaa::/64"
    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```

4. Jeśli masz istniejącą strefowo nadmiarową bramę, uruchom następujące polecenie, aby włączyć łączność IPv6. W przeciwnym razie [Utwórz bramę sieci wirtualnej](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-resource-manager) za pomocą jednostki SKU nadmiarowej strefy (ErGw1AZ, ErGw2AZ, ErGw3AZ).

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name "GatewayName" -ResourceGroupName "ExpressRouteResourceGroup"
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
    ```

## <a name="create-a-connection-to-a-new-virtual-network"></a>Utwórz połączenie z nową siecią wirtualną

Wykonaj poniższe czynności, jeśli planujesz nawiązać połączenie z nowym zestawem zasobów platformy Azure w regionie z Strefy dostępności przy użyciu prywatnej komunikacji równorzędnej IPv6.

1. Utwórz sieć wirtualną o podwójnej stercie z przestrzenią adresową IPv4 i IPv6. Aby uzyskać więcej informacji, zobacz [Tworzenie sieci wirtualnej](https://docs.microsoft.com/azure/virtual-network/quick-create-portal#create-a-virtual-network).

2. [Utwórz podsieć bramy o podwójnym stosie](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-resource-manager#add-a-gateway).

3. [Utwórz bramę sieci wirtualnej](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-resource-manager#add-a-gateway) za pomocą jednostki SKU nadmiarowej strefy (ErGw1AZ, ErGw2AZ, ErGw3AZ). Jeśli planujesz używać FastPath, użyj ErGw3AZ.

4. [Połącz sieć wirtualną z obwodem ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-arm).

## <a name="limitations"></a>Ograniczenia
Gdy obsługa protokołu IPv6 jest dostępna dla połączeń z wdrożeniami w regionach z Strefy dostępności, nie obsługuje następujących przypadków użycia:

* Połączenia z wdrożeniami na platformie Azure za pośrednictwem jednostki SKU bramy ExpressRoute
* Połączenia z wdrożeniami w regionach innych niż AZ
* Global Reach połączenia między obwodymi usługi ExpressRoute

## <a name="next-steps"></a>Następne kroki

Aby rozwiązać problemy z ExpressRoute, zobacz następujące artykuły:

* [Weryfikowanie połączenia usługi ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Rozwiązywanie problemów z wydajnością sieci](expressroute-troubleshooting-network-performance.md)
