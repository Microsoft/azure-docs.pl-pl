---
title: 'Współdziałanie na platformie Azure: Analiza płaszczyzny kontroli'
description: Ten artykuł zawiera analizę płaszczyzny kontroli konfiguracji testowej, której można użyć do analizowania współdziałania między ExpressRoute, sieci VPN typu lokacja-lokacja i komunikacji równorzędnej sieci wirtualnej na platformie Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 5e41bc86533815c394077bf5276d930fe958cd19
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "80518270"
---
# <a name="interoperability-in-azure--control-plane-analysis"></a>Współdziałanie na platformie Azure: Analiza płaszczyzny kontroli

W tym artykule opisano analizę płaszczyzny kontroli dla [konfiguracji testu][Setup]. Można także sprawdzić konfigurację konfiguracji [testu][Configuration] i [analizę płaszczyzny danych][Data-Analysis] konfiguracji testu.

Analiza płaszczyzny kontroli zasadniczo bada trasy wymieniane między sieciami w ramach topologii. Analiza płaszczyzny kontroli może pomóc zrozumieć sposób wyświetlania topologii przez różne sieci.

## <a name="hub-and-spoke-vnet-perspective"></a>Perspektywa sieci wirtualnej gwiazdy i gwiazdy

Na poniższej ilustracji przedstawiono sieć z perspektywy sieci wirtualnej (VNet) z koncentratorem (Sieć wirtualna) i sieć wirtualną szprychy (wyróżnioną kolorem niebieskim). Na rysunku przedstawiono również numer systemu autonomicznego (ASN) różnych sieci i trasy, które są wymieniane między różnymi sieciami: 

![1][1]

Numer ASN bramy Azure ExpressRoute Gateway jest inny niż numer ASN routerów usługi Microsoft Enterprise Edge (MSEE). Brama ExpressRoute korzysta z prywatnego numeru ASN (wartość **65515**) i MSEE używać publicznego numeru ASN (wartość **12076**) globalnie. Po skonfigurowaniu komunikacji równorzędnej ExpressRoute, ponieważ MSEE jest elementem równorzędnym, należy użyć **12076** jako numeru ASN elementu równorzędnego. Po stronie platformy Azure MSEE ustanawia komunikację równorzędną eBGP z bramą ExpressRoute. Podwójna Komunikacja równorzędna eBGP, którą MSEE ustanawia dla każdej komunikacji równorzędnej ExpressRoute, jest przejrzysta na poziomie płaszczyzny kontroli. W związku z tym, gdy przeglądasz tabelę tras ExpressRoute, zobaczysz numer ASN bramy ExpressRoute Gateway dla prefiksów sieci wirtualnej. 

Na poniższej ilustracji przedstawiono przykładową tabelę tras ExpressRoute: 

![5][5]

Na platformie Azure numer ASN jest znaczący tylko z perspektywy komunikacji równorzędnej. Domyślnie ASN zarówno bramy ExpressRoute, jak i bramy sieci VPN na platformie Azure VPN Gateway to **65515**.

## <a name="on-premises-location-1-and-the-remote-vnet-perspective-via-expressroute-1"></a>Lokalizacja lokalna 1 i perspektywa zdalnej sieci wirtualnej za pośrednictwem ExpressRoute 1

Lokalizacja lokalna 1 i zdalna Sieć wirtualna są połączone z siecią wirtualną koncentratora za pośrednictwem ExpressRoute 1. Korzystają one z tej samej perspektywy topologii, jak pokazano na poniższym diagramie:

![2][2]

## <a name="on-premises-location-1-and-the-branch-vnet-perspective-via-a-site-to-site-vpn"></a>Lokalizacja lokalna 1 i perspektywa sieci wirtualnej gałęzi przez sieć VPN typu lokacja-lokacja

Lokalizacja lokalna 1 i Sieć wirtualna gałęzi są połączone z bramą sieci VPN piasty Hub za pośrednictwem połączenia sieci VPN typu lokacja-lokacja. Korzystają one z tej samej perspektywy topologii, jak pokazano na poniższym diagramie:

![3][3]

## <a name="on-premises-location-2-perspective"></a>Lokalizacja lokalna 2 — perspektywa

Lokalizacja lokalna 2 jest połączona z siecią wirtualną Hub za pośrednictwem prywatnej komunikacji równorzędnej ExpressRoute 2: 

![4][4]

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>ExpressRoute i połączenie sieci VPN typu lokacja-lokacja wspólnie

###  <a name="site-to-site-vpn-over-expressroute"></a>Sieć VPN typu lokacja-lokacja za pośrednictwem ExpressRoute

Sieć VPN typu lokacja-lokacja można skonfigurować za pomocą komunikacji równorzędnej firmy Microsoft, aby prywatnie wymieniać dane między siecią lokalną i usługą Azure sieci wirtualnych. Za pomocą tej konfiguracji można wymieniać dane z poufnością, autentycznością i integralnością. Wymiana danych to również ochrona przed odtwarzaniem. Aby uzyskać więcej informacji o sposobie konfigurowania sieci VPN typu lokacja-lokacja w trybie tunelowania przy użyciu ExpressRoute komunikacji równorzędnej firmy Microsoft, zobacz [sieci VPN typu lokacja-lokacja za pośrednictwem usługi ExpressRoute Microsoft peering][S2S-Over-ExR]. 

Podstawowe ograniczenie konfiguracji sieci VPN typu lokacja-lokacja, która używa komunikacji równorzędnej firmy Microsoft, to przepływność. Przepływność przez tunel IPsec jest ograniczona przez pojemność bramy sieci VPN. Przepływność bramy sieci VPN jest mniejsza niż ExpressRoute przepływność. W tym scenariuszu użycie tunelu IPsec w przypadku wysoce bezpiecznego ruchu i użycie prywatnej komunikacji równorzędnej dla całego ruchu pomaga zoptymalizować wykorzystanie przepustowości ExpressRoute.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>Sieć VPN typu lokacja-lokacja jako bezpieczna ścieżka trybu failover dla usługi ExpressRoute

ExpressRoute służy jako para nadmiarowego obwodu w celu zapewnienia wysokiej dostępności. W różnych regionach świadczenia usługi Azure można skonfigurować ExpressRoute Geograficznie nadmiarowy. Ponadto, jak pokazano w naszej konfiguracji testowej w regionie świadczenia usługi Azure, można użyć sieci VPN typu lokacja-lokacja, aby utworzyć ścieżkę trybu failover dla łączności ExpressRoute. Gdy te same prefiksy są anonsowane zarówno w ExpressRoute, jak i w sieci VPN typu lokacja-lokacja, platforma Azure ma priorytet ExpressRoute. Aby uniknąć asymetrycznego routingu między ExpressRoute i siecią VPN typu lokacja-lokacja, konfiguracja sieci lokalnej powinna również natłokować przy użyciu połączenia ExpressRoute przed użyciem połączenia sieci VPN typu lokacja-lokacja.

Aby uzyskać więcej informacji o konfigurowaniu współistniejących połączeń dla ExpressRoute i sieci VPN typu lokacja-lokacja, zobacz [współistnienie ExpressRoute i lokacja-][ExR-S2S-CoEx]lokacja.

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Zwiększanie łączności zaplecza z sieci wirtualnych i lokalizacjami gałęzi

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Łączność z siecią wirtualną przy użyciu komunikacji równorzędnej sieci wirtualnej

Architektura sieci wirtualnej typu Hub i szprych jest szeroko używana. Centrum to sieć wirtualna na platformie Azure, która działa jako centralny punkt łączności między satelitami sieci wirtualnych i sieci lokalnej. Szprychy są sieci wirtualnych tego elementu równorzędnego z koncentratorem i którego można użyć do izolowania obciążeń. Przepływy ruchu między lokalnym centrum danych a centrum za pośrednictwem połączenia ExpressRoute lub sieci VPN. Aby uzyskać więcej informacji o architekturze, zobacz [implementowanie topologii sieci Hub i gwiazdy na platformie Azure][Hub-n-Spoke].

W przypadku komunikacji równorzędnej sieci wirtualnych w obrębie regionu szprycha sieci wirtualnych może używać bram sieci wirtualnej Hub (zarówno bramy VPN, jak i ExpressRoute) do komunikowania się z sieciami zdalnymi.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Łączność między sieciami wirtualnymi przy użyciu połączenia VPN typu lokacja-lokacja

Możesz potrzebować oddziału sieci wirtualnych, które znajdują się w różnych regionach, i sieci lokalnych, aby komunikować się ze sobą za pośrednictwem sieci wirtualnej centrum. Natywne rozwiązanie platformy Azure dla tej konfiguracji to połączenie sieci VPN typu lokacja-lokacja przy użyciu sieci VPN. Alternatywą jest użycie wirtualnego urządzenia sieciowego (urządzenie WUS) do routingu w centrum.

Aby uzyskać więcej informacji, zobacz [co to jest VPN Gateway?][VPN] i [Wdróż urządzenie WUS o wysokiej][Deploy-NVA]dostępności.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [analizy płaszczyzny danych][Data-Analysis] w widokach testów i funkcji monitorowania sieci platformy Azure.

Zobacz [często zadawane pytania dotyczące ExpressRoute][ExR-FAQ] :
-   Dowiedz się, ile obwodów usługi ExpressRoute można połączyć z bramą ExpressRoute.
-   Dowiedz się, ile bram ExpressRoute można połączyć z obwodem ExpressRoute.
-   Dowiedz się więcej na temat innych limitów skalowania ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/HubView.png "Perspektywa sieci wirtualnej gwiazdy i gwiazdy topologii"
[2]: ./media/backend-interoperability/Loc1ExRView.png "Lokalizacja 1 i perspektywa zdalnej sieci wirtualnej topologii za pośrednictwem ExpressRoute 1"
[3]: ./media/backend-interoperability/Loc1VPNView.png "Lokalizacja 1 i perspektywa sieci wirtualnej gałęzi topologii przez sieć VPN typu lokacja-lokacja"
[4]: ./media/backend-interoperability/Loc2View.png "Lokalizacja 2 — perspektywa topologii"
[5]: ./media/backend-interoperability/ExR1-RouteTable.png "Tabela tras ExpressRoute 1"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
[Control-Analysis]:https://docs.microsoft.com/azure/networking/connectivty-interoperability-control-plane
[Data-Analysis]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-data-plane
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering


