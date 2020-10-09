---
title: 'Samouczek: Konfigurowanie komunikacji równorzędnej dla obwodu usługi ExpressRoute — Azure PowerShell'
description: W tym samouczku pokazano, jak utworzyć konfigurację routingu dla obwodu usługi ExpressRoute w Menedżer zasobów modelu wdrażania przy użyciu programu PowerShell i zarządzać nią.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: duau
ms.openlocfilehash: 641d7eeef96af84f0f058aebd19d795083e3567f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91855365"
---
# <a name="tutorial-create-and-modify-peering-for-an-expressroute-circuit-using-powershell"></a>Samouczek: Tworzenie i modyfikowanie komunikacji równorzędnej dla obwodu usługi ExpressRoute przy użyciu programu PowerShell

Ten samouczek ułatwia tworzenie i zarządzanie konfiguracją routingu dla obwodu usługi ExpressRoute w modelu wdrażania Menedżer zasobów przy użyciu programu PowerShell. Możesz również sprawdzić stan, aktualizowanie i usuwanie komunikacji równorzędnej dla obwodu usługi ExpressRoute. Jeśli chcesz użyć innej metody do pracy z obwodem, wybierz artykuł z następującej listy:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [Program PowerShell](expressroute-howto-routing-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-routing-cli.md)
> * [Publiczna Komunikacja równorzędna](about-public-peering.md)
> * [Wideo — prywatna Komunikacja równorzędna](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Wideo — Komunikacja równorzędna firmy Microsoft](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-routing-classic.md)
> 

Te instrukcje dotyczą tylko obwodów utworzonych przy pomocy dostawców oferujących usługi łączności warstwy 2. Jeśli używasz dostawcy usług oferującego zarządzane usługi warstwy 3 (zwykle IPVPN, takie jak MPLS), dostawca łączności skonfiguruje dla Ciebie Routing i zarządza nim.

> [!IMPORTANT]
> Obecnie nie anonsujemy komunikacji równorzędnej skonfigurowanej przez dostawców usług w portalu zarządzania usługami. Pracujemy nad tym, by wkrótce włączyć tę funkcję. Przed skonfigurowaniem komunikacji równorzędnej BGP skontaktuj się z dostawcą usług.
> 

Można skonfigurować prywatną komunikację równorzędną i komunikację równorzędną firmy Microsoft dla obwodu usługi ExpressRoute (publiczna Komunikacja równorzędna Azure jest przestarzała dla nowych obwodów). Komunikację równorzędną można skonfigurować w dowolnej wybranej kolejności. Musisz jednak pamiętać, aby kończyć konfiguracje poszczególnych komunikacji równorzędnych pojedynczo. Aby uzyskać więcej informacji o domenach routingu i komunikacji równorzędnej, zobacz [ExpressRoute Routing domen](expressroute-circuit-peerings.md). Aby uzyskać informacje na temat publicznej komunikacji równorzędnej, zobacz [ExpressRoute publicznej komunikacji równorzędnej](about-public-peering.md).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> - Konfigurowanie, aktualizowanie i usuwanie komunikacji równorzędnej firmy Microsoft dla obwodu
> - Konfigurowanie, aktualizowanie i usuwanie prywatnej komunikacji równorzędnej Azure dla obwodu

## <a name="prerequisites"></a>Wymagania wstępne

* Przed rozpoczęciem konfiguracji upewnij się, że zostały sprawdzone następujące strony:
    * [Wymagania wstępne](expressroute-prerequisites.md) 
    * [Wymagania routingu](expressroute-routing.md)
    * [Przepływy](expressroute-workflows.md)
* Musisz mieć aktywny obwód usługi ExpressRoute. Postępuj zgodnie z instrukcjami, aby [utworzyć obwód usługi ExpressRoute](expressroute-howto-circuit-arm.md) i mieć obwód włączony przez dostawcę łączności przed kontynuowaniem. Obwód usługi ExpressRoute musi być w stanie aprowizacji i włączony, aby można było uruchamiać polecenia cmdlet w tym artykule.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="microsoft-peering"></a><a name="msft"></a>Komunikacja równorzędna firmy Microsoft

Ta sekcja ułatwia tworzenie, pobieranie, aktualizowanie i usuwanie konfiguracji komunikacji równorzędnej firmy Microsoft dla obwodu usługi ExpressRoute.

> [!IMPORTANT]
> Komunikacja równorzędna firmy Microsoft ze obwodów usługi ExpressRoute, które zostały skonfigurowane przed 1 sierpnia 2017, będzie miała wszystkie prefiksy usług anonsowane za pomocą komunikacji równorzędnej firmy Microsoft, nawet jeśli filtry tras nie są zdefiniowane. Komunikacja równorzędna firmy Microsoft obwodów usługi ExpressRoute, które są skonfigurowane w dniu lub po 1 sierpnia 2017, nie będzie miała żadnych prefiksów anonsowanych do momentu dołączenia do obwodu filtru tras. Aby uzyskać więcej informacji, zobacz [Konfigurowanie filtru tras dla komunikacji równorzędnej firmy Microsoft](how-to-routefilter-powershell.md).
> 

### <a name="to-create-microsoft-peering"></a>Aby utworzyć komunikację równorzędną Microsoft

1. Zaloguj się i wybierz swoją subskrypcję.

   Jeśli program PowerShell został zainstalowany lokalnie, zaloguj się. Jeśli używasz Azure Cloud Shell, możesz pominąć ten krok.

   ```azurepowershell
   Connect-AzAccount
   ```

   Wybierz subskrypcję, którą chcesz utworzyć obwód usługi ExpressRoute.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. Utwórz obwód usługi ExpressRoute.

   Wypełnij instrukcje, aby utworzyć [obwód usługi ExpressRoute](expressroute-howto-circuit-arm.md), który zostanie zainicjowany przez dostawcę połączenia. Jeśli dostawca połączenia oferuje zarządzane usługi warstwy 3, możesz polecić dostawcę połączenia, aby włączyć komunikację równorzędną firmy Microsoft. Nie trzeba wykonywać instrukcji wymienionych w następnych sekcjach. Jeśli jednak dostawca łączności nie zarządza usługą dla Ciebie, po utworzeniu obwodu kontynuuj konfigurację przy użyciu następnych kroków. 

3. Sprawdź obwód ExpressRoute, aby upewnić się, że jest on zainicjowany i włączony. Skorzystaj z następującego przykładu:

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   Odpowiedź jest podobna do poniższego przykładu:

   ```
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
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
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
   ServiceKey                       : **************************************
   Peerings                         : []
   ```
4. Skonfiguruj komunikację równorzędną Microsoft dla obwodu. Przed kontynuowaniem upewnij się, że masz poniższe informacje.

   * Podsieć/30 lub/126 dla linku podstawowego. Blok adresu musi być prawidłowym publicznym prefiksem IPv4 lub IPv6 należącym do użytkownika i zarejestrowanym w RIR/IRR.
   * Podsieć/30 lub/126 dla łącza pomocniczego. Blok adresu musi być prawidłowym publicznym prefiksem IPv4 lub IPv6 należącym do użytkownika i zarejestrowanym w RIR/IRR.
   * Prawidłowy identyfikator sieci VLAN do ustanowienia tej komunikacji równorzędnej jest włączony. Upewnij się, że żadna inna komunikacja równorzędna w obwodzie nie używa tego samego identyfikatora VLAN.
   * Numer AS do komunikacji równorzędnej. Możesz używać 2-bajtowych i 4-bajtowych numerów AS.
   * Anonsowane prefiksy: zawiera listę wszystkich prefiksów, które planujesz anonsować za pośrednictwem sesji BGP. Akceptowane są tylko prefiksy publicznych adresów IP. Jeśli planujesz wysłanie zestawu prefiksów, możesz wysłać listę rozdzieloną przecinkami. Prefiksy te muszą być zarejestrowane na Ciebie w RIR/IRR. W przypadku sesji protokołu IPv4 BGP wymagane są anonsowane prefiksy protokołu IPv4 i sesje protokołu IPv6 BGP wymagają wyanonsowanych prefiksów IPv6. 
   * Nazwa rejestru routingu: możesz określić RIR/IRR, względem którego rejestrowany jest numer AS i prefiksy.
   * Opcjonalnie:
     * Numer ASN klienta: w przypadku anonsowania prefiksów, które nie są zarejestrowane w komunikacji równorzędnej jako liczba, można określić liczbę AS, do której są zarejestrowani.
     * Skrót MD5, jeśli zdecydujesz się go użyć.

> [!IMPORTANT]
> Firma Microsoft sprawdza, czy określone "anonsowane publiczne prefiksy" i "peer ASN" (lub "ASN klienta") są przypisane do Ciebie w rejestrze routingu internetowego. Jeśli otrzymujesz publiczne prefiksy z innej jednostki i jeśli przypisanie nie zostanie zarejestrowane w rejestrze routingu, automatyczne sprawdzanie poprawności nie zostanie ukończone i będzie wymagać ręcznej walidacji. Jeśli automatyczne sprawdzanie poprawności nie powiedzie się, zobaczysz "AdvertisedPublicPrefixesState" jako "wymagana Walidacja" w danych wyjściowych polecenia "Get-AzExpressRouteCircuitPeeringConfig" (zobacz "Aby uzyskać szczegóły dotyczące komunikacji równorzędnej firmy Microsoft" w poniższej sekcji). 
> 
> Jeśli zobaczysz komunikat "wymagana Walidacja", Zbierz dokumenty, które pokazują, że publiczne prefiksy są przypisane do organizacji przez jednostkę wymienioną jako właściciel prefiksów w rejestrze routingu i prześlij te dokumenty w celu ręcznej weryfikacji przez otwarcie biletu pomocy technicznej. 
> 

   Skorzystaj z następującego przykładu, aby skonfigurować komunikację równorzędną firmy Microsoft dla obwodu:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

   Add-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

### <a name="to-get-microsoft-peering-details"></a><a name="getmsft"></a>Aby pobrać szczegóły dotyczące komunikacji równorzędnej firmy Microsoft

Szczegóły konfiguracji można uzyskać, korzystając z następującego przykładu:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt
```

### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Aby zaktualizować konfigurację komunikacji równorzędnej firmy Microsoft

Możesz zaktualizować dowolną część konfiguracji, korzystając z następującego przykładu:

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="azure-private-peering"></a><a name="private"></a>Prywatna komunikacja równorzędna platformy Azure

Ta sekcja ułatwia tworzenie, pobieranie, aktualizowanie i usuwanie konfiguracji prywatnej komunikacji równorzędnej Azure dla obwodu usługi ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Aby utworzyć prywatną komunikację równorzędną

1. Zaimportuj moduł programu PowerShell dla usługi ExpressRoute.

   Zainstaluj najnowszy Instalator programu PowerShell z programu [Galeria programu PowerShell](https://www.powershellgallery.com/). Następnie zaimportuj moduły Azure Resource Manager do sesji programu PowerShell, aby rozpocząć korzystanie z poleceń cmdlet ExpressRoute. Musisz uruchomić program PowerShell jako administrator.

   ```azurepowershell-interactive
   Install-Module Az
   ```

   Zaimportuj wszystkie AZ. \* Moduły w znanym zakresie wersji semantycznej.

   ```azurepowershell-interactive
   Import-Module Az
   ```

   Możesz również zaimportować moduł SELECT w ramach znanego zakresu wersji semantycznych.

   ```azurepowershell-interactive
   Import-Module Az.Network 
   ```

   Zaloguj się do swojego konta.

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

   Wybierz subskrypcję, którą chcesz utworzyć obwód usługi ExpressRoute.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. Utwórz obwód usługi ExpressRoute.

   Wypełnij instrukcje, aby utworzyć [obwód usługi ExpressRoute](expressroute-howto-circuit-arm.md), który zostanie zainicjowany przez dostawcę połączenia. Jeśli dostawca połączenia oferuje zarządzane usługi warstwy 3, możesz polecić dostawcę połączenia, aby włączyć prywatną komunikację równorzędną Azure. Nie trzeba wykonywać instrukcji wymienionych w następnych sekcjach. Jeśli jednak dostawca łączności nie zarządza usługą dla Ciebie, po utworzeniu obwodu kontynuuj konfigurację przy użyciu następnych kroków.

3. Sprawdź obwód ExpressRoute, aby upewnić się, że jest on zainicjowany i włączony. Skorzystaj z następującego przykładu:

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   Odpowiedź jest podobna do poniższego przykładu:

   ```
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
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
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
   ServiceKey                       : **************************************
   Peerings                         : []
   ```
4. Skonfiguruj prywatną komunikację równorzędną Azure dla obwodu. Przed przejściem do następnego kroku upewnij się, że masz następujące elementy:

   * Podsieć /30 dla połączenia podstawowego. Podsieć nie może być częścią żadnej przestrzeni adresowej zarezerwowanej dla sieci wirtualnych.
   * Podsieć /30 dla połączenia dodatkowego. Podsieć nie może być częścią żadnej przestrzeni adresowej zarezerwowanej dla sieci wirtualnych.
   * Prawidłowy identyfikator sieci VLAN do ustanowienia tej komunikacji równorzędnej jest włączony. Upewnij się, że żadna inna komunikacja równorzędna w obwodzie nie używa tego samego identyfikatora VLAN.
   * Numer AS do komunikacji równorzędnej. Możesz używać 2-bajtowych i 4-bajtowych numerów AS. Możesz użyć prywatnego numeru AS dla tej komunikacji równorzędnej. Upewnij się, że nie używasz 65515.
   * Opcjonalnie:
     * Skrót MD5, jeśli zdecydujesz się go użyć.

   Skorzystaj z następującego przykładu, aby skonfigurować prywatną komunikację równorzędną Azure dla obwodu:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   Jeśli zdecydujesz się używać skrótu MD5, użyj poniższego przykładu:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Pamiętaj, aby określić numer AS jako ASN komunikacji równorzędnej, a nie ASN klienta.
   > 
   >

### <a name="to-get-azure-private-peering-details"></a><a name="getprivate"></a>Aby uzyskać szczegóły dotyczące prywatnego komunikacji równorzędnej Azure

Szczegóły konfiguracji można uzyskać, korzystając z następującego przykładu:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
```

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>Aby zaktualizować konfigurację prywatnej komunikacji równorzędnej Azure

Możesz zaktualizować dowolną część konfiguracji, korzystając z poniższego przykładu. W tym przykładzie identyfikator sieci VLAN obwodu jest aktualizowany z 100 do 500.

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Aby usunąć komunikację równorzędną firmy Microsoft

Możesz usunąć konfigurację komunikacji równorzędnej, uruchamiając następujące polecenie cmdlet:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Aby usunąć prywatną komunikację równorzędną Azure

Konfigurację komunikacji równorzędnej można usunąć, uruchamiając Poniższy przykład:

> [!WARNING]
> Przed uruchomieniem tego przykładu należy upewnić się, że wszystkie sieci wirtualne i ExpressRoute Global Reach są usuwane. 
> 

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu prywatnej komunikacji równorzędnej Azure Możesz utworzyć bramę ExpressRoute, aby połączyć sieć wirtualną z obwodem. 

> [!div class="nextstepaction"]
> [Konfigurowanie bramy sieci wirtualnej dla usługi ExpressRoute](expressroute-howto-add-gateway-resource-manager.md)
