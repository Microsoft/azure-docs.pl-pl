---
title: Łączność między sieciami platformy Azure
description: Na tej stronie opisano scenariusz aplikacji służący do łączności między sieciami i rozwiązań opartych na funkcjach sieciowych platformy Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 04/03/2019
ms.author: duau
ms.openlocfilehash: f13e3c03c0cfd747c7819d95a5fb98560db861c4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89398073"
---
# <a name="cross-network-connectivity"></a>Łączność między sieciami

Firma Fabrikam Inc. ma dużą obecność fizyczną i wdrożenie platformy Azure w regionie Wschodnie stany USA. Firma Fabrikam ma łączność zaplecza między wdrożeniami lokalnymi i platformą Azure za pośrednictwem usługi ExpressRoute. Podobnie firma Contoso Ltd. ma w regionie zachodnie stany USA obecność i wdrożenie platformy Azure. Firma Contoso ma łączność zaplecza między wdrożeniami lokalnymi i platformą Azure za pośrednictwem usługi ExpressRoute.  

Fabrikam Inc. uzyskuje Contoso Ltd. Po połączeniu firma Fabrikam chce połączyć się z sieciami. Na poniższej ilustracji przedstawiono scenariusz:

![Scenariusz aplikacji](./media/cross-network-connectivity/premergerscenario.png)

Strzałki kreskowane na środku powyższego rysunku wskazują żądanych połączeń sieciowych. W odniesieniu do programu istnieją trzy typy pożądanych połączeń: 1) Fabrikam i contoso sieci wirtualnych Cross connect, 2) międzyregionalne lokalne i sieci wirtualnych połączenie krzyżowe (czyli połączenie sieci firmy Fabrikam z siecią wirtualną Contoso i połączenie sieci lokalnej firmy Contoso z firmą Fabrikam VNet) oraz 3) Fabrikam i contoso lokalna sieć firmowa. 

W poniższej tabeli przedstawiono tabelę tras prywatnej komunikacji równorzędnej ExpressRoute firmy Contoso Ltd. przed połączeniem.

![Tabela tras contoso ExpressRoute przed połączeniem](./media/cross-network-connectivity/contosoexr-rt-premerger.png)

W poniższej tabeli przedstawiono efektywne trasy maszyny wirtualnej w ramach subskrypcji contoso przed połączeniem. Na tabelę maszyny wirtualne w sieci wirtualnej wiedzą, że przestrzeń adresowa wirtualnej i sieć firmowa contoso, niezależnie od domyślnych.

![Trasy maszyn wirtualnych firmy Contoso przed połączeniem](./media/cross-network-connectivity/contosovm-routes-premerger.png)

W poniższej tabeli przedstawiono tabelę tras prywatnej komunikacji równorzędnej ExpressRoute firmy Fabrikam Inc. przed połączeniem.

![Tabela tras Fabrikam ExpressRoute przed połączeniem](./media/cross-network-connectivity/fabrikamexr-rt-premerger.png)

W poniższej tabeli przedstawiono efektywne trasy maszyny wirtualnej w subskrypcji firmy Fabrikam przed połączeniem. Na tabelę maszyny wirtualne w sieci wirtualnej wiedzą, że przestrzeń adresowa wirtualnej i sieć firmowa firmy Fabrikam, niezależnie od domyślnych.

![Trasy maszyn wirtualnych firmy Fabrikam przed połączeniem](./media/cross-network-connectivity/fabrikamvm-routes-premerger.png)

W tym artykule opisano krok po kroku i omówiono sposób osiągnięcia żądanych połączeń krzyżowych przy użyciu następujących funkcji sieci platformy Azure:

* [Komunikacja równorzędna sieci wirtualnych][Virtual network peering] 
* [Połączenie ExpressRoute sieci wirtualnej][connection]
* [Global Reach][Global Reach] 

## <a name="cross-connecting-vnets"></a>Połączenie krzyżowe sieci wirtualnych

Komunikacja równorzędna sieci wirtualnych (wirtualne sieci równorzędne) zapewnia optymalną i najlepszą wydajność sieci podczas łączenia dwóch sieci wirtualnych. Komunikacja równorzędna sieci wirtualnych obsługuje komunikację równorzędną dwóch sieci wirtualnych jednocześnie w tym samym regionie świadczenia usługi Azure (nazywanej zwykle równorzędnymi sieciami wirtualnymi) i w dwóch różnych regionach świadczenia usługi Azure (zazwyczaj nazywane globalnymi sieciami równorzędnymi). 

Skonfigurujmy globalną komunikację równorzędną sieci wirtualnej między sieci wirtualnych w ramach subskrypcji platformy Azure w firmie Contoso i fabrikam. Aby uzyskać informacje na temat tworzenia komunikacji równorzędnej sieci wirtualnej między dwiema sieciami wirtualnymi, zobacz artykuł [Tworzenie komunikacji równorzędnej sieci wirtualnej][Configure VNet peering] .

Na poniższej ilustracji przedstawiono architekturę sieci po skonfigurowaniu globalnej sieci równorzędnej.

![Architektura po komunikacji równorzędnej sieci wirtualnej](./media/cross-network-connectivity/vnet-peering.png )

W poniższej tabeli przedstawiono trasy znane dla maszyny wirtualnej subskrypcji contoso. Zwróć uwagę na ostatni wpis w tabeli. Ten wpis jest wynikiem połączenia między sieciami wirtualnymi.

![Trasy maszyn wirtualnych firmy Contoso po komunikacji równorzędnej sieci wirtualnej](./media/cross-network-connectivity/contosovm-routes-peering.png)

W poniższej tabeli przedstawiono trasy znane dla maszyny wirtualnej subskrypcji fabrikam. Zwróć uwagę na ostatni wpis w tabeli. Ten wpis jest wynikiem połączenia między sieciami wirtualnymi.

![Trasy maszyn wirtualnych Fabrikam po komunikacji równorzędnej sieci wirtualnej](./media/cross-network-connectivity/fabrikamvm-routes-peering.png)

Wirtualne sieci równorzędne łączą się bezpośrednio w dwóch sieciach wirtualnych (Zobacz, że nie ma żadnego następnego skoku dla wpisu *VNetGlobalPeering* w powyższych dwóch tabelach)

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>Łączenie sieci wirtualnych z sieciami lokalnymi

Można połączyć obwód ExpressRoute z wieloma sieciami wirtualnymi. Zapoznaj się z [limitami subskrypcji i usług][Subscription limits] , aby uzyskać maksymalną liczbę sieci wirtualnych, które mogą być połączone z obwodem usługi ExpressRoute. 

Połączmy obwód usługi Fabrikam ExpressRoute z siecią wirtualną subskrypcji firmy Contoso i podobnym obwodem usługi contoso ExpressRoute do sieci wirtualnej Fabrikam Subscription, aby umożliwić komunikację między sieciami wirtualnymi i lokalnymi. Aby połączyć sieć wirtualną z obwodem usługi ExpressRoute w innej subskrypcji, musimy utworzyć i używać autoryzacji.  Zobacz artykuł: [łączenie sieci wirtualnej z obwodem ExpressRoute][Connect-ER-VNet].

Na poniższej ilustracji przedstawiono architekturę sieci po skonfigurowaniu połączenia krzyżowego ExpressRoute z sieciami wirtualnymi.

![Architektura po usługa expressroutes połączenia krzyżowego](./media/cross-network-connectivity/exr-x-connect.png)

W poniższej tabeli przedstawiono tabelę tras prywatnej komunikacji równorzędnej ExpressRoute firmy Contoso Ltd. po nawiązaniu połączenia między sieciami wirtualnymi i sieciami lokalnymi za pośrednictwem ExpressRoute. Sprawdź, czy tabela tras ma trasy należące do sieci wirtualnych.

![Tabela tras contoso ExpressRoute po połączeniu między ExR i sieci wirtualnych](./media/cross-network-connectivity/contosoexr-rt-xconnect.png)

W poniższej tabeli przedstawiono tabelę tras prywatnej komunikacji równorzędnej ExpressRoute firmy Fabrikam Inc. po nawiązaniu połączenia między sieciami wirtualnymi i sieciami lokalnymi za pośrednictwem ExpressRoute. Sprawdź, czy tabela tras ma trasy należące do sieci wirtualnych.

![Tabela tras Fabrikam ExpressRoute po nawiązaniu połączenia między ExR i sieci wirtualnych](./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png)

W poniższej tabeli przedstawiono trasy znane dla maszyny wirtualnej subskrypcji contoso. Zwróć uwagę na wpisy *bramy sieci wirtualnej* w tabeli. Maszyna wirtualna widzi trasy dla sieci lokalnych.

![Trasy maszyn wirtualnych firmy Contoso po nawiązaniu połączenia z ExR i sieci wirtualnych](./media/cross-network-connectivity/contosovm-routes-xconnect.png)

W poniższej tabeli przedstawiono trasy znane dla maszyny wirtualnej subskrypcji fabrikam. Zwróć uwagę na wpisy *bramy sieci wirtualnej* w tabeli. Maszyna wirtualna widzi trasy dla sieci lokalnych.

![Trasy maszyn wirtualnych firmy Fabrikam po nawiązaniu połączenia z ExR i sieci wirtualnych](./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png)

>[!NOTE]
>W subskrypcjach Fabrikam i/lub contoso można także mieć sieci wirtualnych z odpowiednią siecią wirtualną koncentratora (projekt gwiazdy nie jest zilustrowany na diagramach architektury w tym artykule). Połączenia krzyżowe między bramami sieci wirtualnej centrum a usługą ExpressRoute umożliwiają również komunikację między centrami wschodnie i zachodnie i szprych.
>

## <a name="cross-connecting-on-premises-networks"></a>Łączenie między sieciami lokalnymi

ExpressRoute Global Reach zapewnia łączność między sieciami lokalnymi podłączonymi do różnych obwodów usługi ExpressRoute. Skonfigurujmy Global Reach między obwodami firmy Contoso i fabrikam ExpressRoute. Ze względu na to, że obwody usługi ExpressRoute znajdują się w różnych subskrypcjach, musimy utworzyć i używać autoryzacji. Zobacz [Konfigurowanie ExpressRoute Global REACH][Configure Global Reach] artykułu, aby uzyskać wskazówki krok po kroku.

Na poniższej ilustracji przedstawiono architekturę sieci po skonfigurowaniu Global Reach.

![Architektura po skonfigurowaniu Global Reach](./media/cross-network-connectivity/globalreach.png)

W poniższej tabeli przedstawiono tabelę tras prywatnej komunikacji równorzędnej ExpressRoute firmy Contoso Ltd., po skonfigurowaniu Global Reach. Sprawdź, czy tabela tras ma trasy należące do sieci lokalnych. 

![Tabela tras contoso ExpressRoute po Global Reach](./media/cross-network-connectivity/contosoexr-rt-gr.png)

W poniższej tabeli przedstawiono tabelę tras prywatnej komunikacji równorzędnej ExpressRoute firmy Fabrikam Inc. po skonfigurowaniu Global Reach. Sprawdź, czy tabela tras ma trasy należące do sieci lokalnych.

![Tabela tras Fabrikam ExpressRoute po Global Reach]( ./media/cross-network-connectivity/fabrikamexr-rt-gr.png )

## <a name="next-steps"></a>Następne kroki

Zobacz [często zadawane pytania dotyczące usługi Virtual Network][VNet-FAQ], aby uzyskać dalsze pytania dotyczące sieci wirtualnej i komunikacji równorzędnej. Zapoznaj się z [ExpressRoute często zadawanych][ER-FAQ] pytań, aby uzyskać odpowiedzi na pytania dotyczące ExpressRoute i sieci wirtualnej.

Global Reach jest wdrażana w kraju/regionie według kraju/regionu. Aby sprawdzić, czy Global Reach jest dostępny w krajach/regionach, które chcesz, zobacz [ExpressRoute Global REACH][Global Reach].

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