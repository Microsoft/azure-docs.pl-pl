---
title: Łączność między sieciami platformy Azure
description: Na tej stronie opisano scenariusz aplikacji służący do łączności między sieciami i rozwiązań opartych na funkcjach sieciowych platformy Azure.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 04/03/2019
ms.author: rambala
ms.openlocfilehash: 48ec26cc98310dfeb61aa17018c940b431cfbcee
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75644260"
---
# <a name="cross-network-connectivity"></a>Łączność między sieciami

Firma Fabrikam Inc. ma dużą obecność fizyczną i wdrożenie platformy Azure w regionie Wschodnie stany USA. Firma Fabrikam ma łączność zaplecza między wdrożeniami lokalnymi i platformą Azure za pośrednictwem usługi ExpressRoute. Podobnie firma Contoso Ltd. ma w regionie zachodnie stany USA obecność i wdrożenie platformy Azure. Firma Contoso ma łączność zaplecza między wdrożeniami lokalnymi i platformą Azure za pośrednictwem usługi ExpressRoute.  

Fabrikam Inc. uzyskuje Contoso Ltd. Po połączeniu firma Fabrikam chce połączyć się z sieciami. Na poniższej ilustracji przedstawiono scenariusz:

 [![1]][1]

Strzałki kreskowane na środku powyższego rysunku wskazują żądanych połączeń sieciowych. W odniesieniu do programu istnieją trzy typy pożądanych połączeń: 1) Fabrikam i contoso sieci wirtualnych Cross connect, 2) międzyregionalne lokalne i sieci wirtualnych połączenie krzyżowe (czyli połączenie sieci firmy Fabrikam z siecią wirtualną Contoso i połączenie sieci lokalnej firmy Contoso z firmą Fabrikam VNet) oraz 3) Fabrikam i contoso lokalna sieć firmowa. 

W poniższej tabeli przedstawiono tabelę tras prywatnej komunikacji równorzędnej ExpressRoute firmy Contoso Ltd. przed połączeniem.

[![2]][2]

W poniższej tabeli przedstawiono efektywne trasy maszyny wirtualnej w ramach subskrypcji contoso przed połączeniem. Na tabelę maszyny wirtualne w sieci wirtualnej wiedzą, że przestrzeń adresowa wirtualnej i sieć firmowa contoso, niezależnie od domyślnych. 

[![4]][4]

W poniższej tabeli przedstawiono tabelę tras prywatnej komunikacji równorzędnej ExpressRoute firmy Fabrikam Inc. przed połączeniem.

[![3]][3]

W poniższej tabeli przedstawiono efektywne trasy maszyny wirtualnej w subskrypcji firmy Fabrikam przed połączeniem. Na tabelę maszyny wirtualne w sieci wirtualnej wiedzą, że przestrzeń adresowa wirtualnej i sieć firmowa firmy Fabrikam, niezależnie od domyślnych.

[![5]][5]

W tym artykule opisano krok po kroku i omówiono sposób osiągnięcia żądanych połączeń krzyżowych przy użyciu następujących funkcji sieci platformy Azure:

* [Wirtualne sieci równorzędne][Virtual network peering] 
* [Połączenie ExpressRoute sieci wirtualnej][connection]
* [Global Reach][Global Reach] 

## <a name="cross-connecting-vnets"></a>Połączenie krzyżowe sieci wirtualnych

Komunikacja równorzędna sieci wirtualnych (wirtualne sieci równorzędne) zapewnia optymalną i najlepszą wydajność sieci podczas łączenia dwóch sieci wirtualnych. Komunikacja równorzędna sieci wirtualnych obsługuje komunikację równorzędną dwóch sieci wirtualnych jednocześnie w tym samym regionie świadczenia usługi Azure (nazywanej zwykle równorzędnymi sieciami wirtualnymi) i w dwóch różnych regionach świadczenia usługi Azure (zazwyczaj nazywane globalnymi sieciami równorzędnymi). 

Skonfigurujmy globalną komunikację równorzędną sieci wirtualnej między sieci wirtualnych w ramach subskrypcji platformy Azure w firmie Contoso i fabrikam. Aby uzyskać informacje na temat tworzenia komunikacji równorzędnej sieci wirtualnej między dwiema sieciami wirtualnymi, zobacz artykuł [Tworzenie komunikacji równorzędnej sieci wirtualnej][Configure VNet peering] .

Na poniższej ilustracji przedstawiono architekturę sieci po skonfigurowaniu globalnej sieci równorzędnej.

[![ust]][6]

W poniższej tabeli przedstawiono trasy znane dla maszyny wirtualnej subskrypcji contoso. Zwróć uwagę na ostatni wpis w tabeli. Ten wpis jest wynikiem połączenia między sieciami wirtualnymi.

[![7]][7]

W poniższej tabeli przedstawiono trasy znane dla maszyny wirtualnej subskrypcji fabrikam. Zwróć uwagę na ostatni wpis w tabeli. Ten wpis jest wynikiem połączenia między sieciami wirtualnymi.

[![0,8]][8]

Wirtualne sieci równorzędne łączą się bezpośrednio w dwóch sieciach wirtualnych (Zobacz, że nie ma żadnego następnego skoku dla wpisu *VNetGlobalPeering* w powyższych dwóch tabelach)

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>Łączenie sieci wirtualnych z sieciami lokalnymi

Można połączyć obwód ExpressRoute z wieloma sieciami wirtualnymi. Zapoznaj się z [limitami subskrypcji i usług][Subscription limits] , aby uzyskać maksymalną liczbę sieci wirtualnych, które mogą być połączone z obwodem usługi ExpressRoute. 

Połączmy obwód usługi Fabrikam ExpressRoute z siecią wirtualną subskrypcji firmy Contoso i podobnym obwodem usługi contoso ExpressRoute do sieci wirtualnej Fabrikam Subscription, aby umożliwić komunikację między sieciami wirtualnymi i lokalnymi. Aby połączyć sieć wirtualną z obwodem usługi ExpressRoute w innej subskrypcji, musimy utworzyć i używać autoryzacji.  Zobacz artykuł: [łączenie sieci wirtualnej z obwodem ExpressRoute][Connect-ER-VNet].

Na poniższej ilustracji przedstawiono architekturę sieci po skonfigurowaniu połączenia krzyżowego ExpressRoute z sieciami wirtualnymi.

[![9]][9]

W poniższej tabeli przedstawiono tabelę tras prywatnej komunikacji równorzędnej ExpressRoute firmy Contoso Ltd. po nawiązaniu połączenia między sieciami wirtualnymi i sieciami lokalnymi za pośrednictwem ExpressRoute. Sprawdź, czy tabela tras ma trasy należące do sieci wirtualnych.

[![dziesięć]][10]

W poniższej tabeli przedstawiono tabelę tras prywatnej komunikacji równorzędnej ExpressRoute firmy Fabrikam Inc. po nawiązaniu połączenia między sieciami wirtualnymi i sieciami lokalnymi za pośrednictwem ExpressRoute. Sprawdź, czy tabela tras ma trasy należące do sieci wirtualnych.

[![11]][11]

W poniższej tabeli przedstawiono trasy znane dla maszyny wirtualnej subskrypcji contoso. Zwróć uwagę na wpisy *bramy sieci wirtualnej* w tabeli. Maszyna wirtualna widzi trasy dla sieci lokalnych.

[![dwunastomiesięcznych]][12]

W poniższej tabeli przedstawiono trasy znane dla maszyny wirtualnej subskrypcji fabrikam. Zwróć uwagę na wpisy *bramy sieci wirtualnej* w tabeli. Maszyna wirtualna widzi trasy dla sieci lokalnych.

[![13]][13]

>[!NOTE]
>W subskrypcjach Fabrikam i/lub contoso można także mieć sieci wirtualnych z odpowiednią siecią wirtualną koncentratora (projekt gwiazdy nie jest zilustrowany na diagramach architektury w tym artykule). Połączenia krzyżowe między bramami sieci wirtualnej centrum a usługą ExpressRoute umożliwiają również komunikację między centrami wschodnie i zachodnie i szprych.
>

## <a name="cross-connecting-on-premises-networks"></a>Łączenie między sieciami lokalnymi

ExpressRoute Global Reach zapewnia łączność między sieciami lokalnymi podłączonymi do różnych obwodów usługi ExpressRoute. Skonfigurujmy Global Reach między obwodami firmy Contoso i fabrikam ExpressRoute. Ze względu na to, że obwody usługi ExpressRoute znajdują się w różnych subskrypcjach, musimy utworzyć i używać autoryzacji. Zobacz [Konfigurowanie ExpressRoute Global REACH][Configure Global Reach] artykułu, aby uzyskać wskazówki krok po kroku.

Na poniższej ilustracji przedstawiono architekturę sieci po skonfigurowaniu Global Reach.

[![14]][14]

W poniższej tabeli przedstawiono tabelę tras prywatnej komunikacji równorzędnej ExpressRoute firmy Contoso Ltd., po skonfigurowaniu Global Reach. Sprawdź, czy tabela tras ma trasy należące do sieci lokalnych. 

[![15000]][15]

W poniższej tabeli przedstawiono tabelę tras prywatnej komunikacji równorzędnej ExpressRoute firmy Fabrikam Inc. po skonfigurowaniu Global Reach. Sprawdź, czy tabela tras ma trasy należące do sieci lokalnych.

[![16]][16]

## <a name="next-steps"></a>Następne kroki

Zobacz [często zadawane pytania dotyczące usługi Virtual Network][VNet-FAQ], aby uzyskać dalsze pytania dotyczące sieci wirtualnej i komunikacji równorzędnej. Zapoznaj się z [ExpressRoute często zadawanych][ER-FAQ] pytań, aby uzyskać odpowiedzi na pytania dotyczące ExpressRoute i sieci wirtualnej.

Global Reach jest wdrażana w kraju/regionie według kraju/regionu. Aby sprawdzić, czy Global Reach jest dostępny w krajach/regionach, które chcesz, zobacz [ExpressRoute Global REACH][Global Reach].

<!--Image References-->
[1]: ./media/cross-network-connectivity/premergerscenario.png "scenariusz aplikacji"
[2]: ./media/cross-network-connectivity/contosoexr-rt-premerger.png "contoso ExpressRoute — tabela tras przed połączeniem"
[3]: ./media/cross-network-connectivity/fabrikamexr-rt-premerger.png "Fabrikam ExpressRoute tabelę tras przed fuzją"
[4]: ./media/cross-network-connectivity/contosovm-routes-premerger.png "trasy maszyn wirtualnych firmy Contoso przed połączeniem"
[5]: ./media/cross-network-connectivity/fabrikamvm-routes-premerger.png. "trasy maszyn wirtualnych firmy Fabrikam przed połączeniem"
[6]: ./media/cross-network-connectivity/vnet-peering.png "architektura po komunikacji równorzędnej sieci wirtualnej"
[7]: ./media/cross-network-connectivity/contosovm-routes-peering.png "tras maszyn wirtualnych contoso po komunikacji równorzędnej sieci wirtualnej"
[8]: ./media/cross-network-connectivity/fabrikamvm-routes-peering.png "tras maszyn wirtualnych Fabrikam po komunikacji równorzędnej sieci" wirtualnej
[9]: ./media/cross-network-connectivity/exr-x-connect.png "architektura po połączeniu krzyżowym usługa expressroutes"
[10]: ./media/cross-network-connectivity/contosoexr-rt-xconnect.png "contoso ExpressRoute Route tabeli po nawiązaniu połączenia między ExR i sieci wirtualnych"
[11]: ./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png "tabela "Fabrikam ExpressRoute Route" po nawiązaniu połączenia między ExR i sieci wirtualnych"
[12]: ./media/cross-network-connectivity/contosovm-routes-xconnect.png "tras maszyn wirtualnych firmy Contoso po połączeniu między ExR i sieci wirtualnych"
[13]: ./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png "tras maszyn wirtualnych Fabrikam po połączeniu z ExR i sieci wirtualnych"
[14]: ./media/cross-network-connectivity/globalreach.png "architektura po skonfigurowaniu Global REACH"
[15]: ./media/cross-network-connectivity/contosoexr-rt-gr.png "contoso ExpressRoute route tabeli po Global REACH"
[16]: ./media/cross-network-connectivity/fabrikamexr-rt-gr.png "Fabrikam ExpressRoute tabeli tras po Global REACH"

<!--Link References-->
[Virtual network peering]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[connection]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-global-reach
[Configure VNet peering]: https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[Subscription limits]: https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits
[Connect-ER-VNet]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[VNet-FAQ]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq