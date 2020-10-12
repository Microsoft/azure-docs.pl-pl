---
title: 'Współdziałanie na platformie Azure: Konfiguracja testu | Microsoft Docs'
description: W tym artykule opisano konfigurację testu, za pomocą której można analizować współdziałanie między usługą ExpressRoute, siecią VPN typu lokacja-lokacja i wirtualną siecią równorzędną na platformie Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 3aec41a145d2c94a45a453393831902069b9c41b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "80518191"
---
# <a name="interoperability-in-azure--test-setup"></a>Współdziałanie na platformie Azure: Konfiguracja testu

W tym artykule opisano konfigurację testową, której można użyć do analizowania działania usług sieciowych platformy Azure na poziomie płaszczyzny kontroli i poziomie płaszczyzny danych. Przyjrzyjmy się krótko w składnikach sieciowych platformy Azure:

-   **Azure ExpressRoute**: używanie prywatnej komunikacji równorzędnej w usłudze Azure ExpressRoute do bezpośredniego łączenia prywatnych miejsc IP w sieci lokalnej z wdrożeniami Virtual Network platformy Azure. ExpressRoute może pomóc w osiągnięciu większej przepustowości i połączenia prywatnego. Wielu ExpressRouteów ekonomicznych oferuje łączność ExpressRoute z umowy SLA. Aby dowiedzieć się więcej na temat ExpressRoute i dowiedzieć się, jak skonfigurować ExpressRoute, zobacz [wprowadzenie do ExpressRoute][ExpressRoute].
-   **Sieć VPN typu lokacja-lokacja**: możesz użyć usługi Azure VPN Gateway jako sieci VPN typu lokacja-lokacja, aby bezpiecznie połączyć sieć lokalną z platformą Azure za pośrednictwem Internetu lub przy użyciu usługi ExpressRoute. Aby dowiedzieć się, jak skonfigurować sieć VPN typu lokacja-lokacja do łączenia się z platformą Azure, zobacz [konfigurowanie VPN Gateway][VPN].
-   **Komunikacja równorzędna**sieci wirtualnych: Użyj komunikacji równorzędnej (VNET) w celu nawiązania połączenia między sieci wirtualnych na platformie Virtual Network Azure. Aby dowiedzieć się więcej o wirtualnych sieciach równorzędnych, zobacz Samouczek dotyczący [komunikacji równorzędnej w sieci wirtualnej][VNet].

## <a name="test-setup"></a>Konfiguracja testu

Na poniższej ilustracji przedstawiono konfigurację testu:

![1][1]

Filarem konfiguracji testowej to sieć wirtualna centrum w regionie 1 platformy Azure. Sieć wirtualna koncentratora jest połączona z różnymi sieciami w następujący sposób:

-   Sieć wirtualna piasty jest połączona z siecią wirtualną szprych przy użyciu komunikacji równorzędnej sieci wirtualnej. Sieć wirtualna szprych ma zdalny dostęp do obu bram w sieci wirtualnej centrum.
-   Sieć wirtualna piasty jest połączona z siecią wirtualną gałęzi przy użyciu sieci VPN typu lokacja-lokacja. Połączenie używa usługi eBGP do wymiany tras.
-   Sieć wirtualna centrum jest połączona z siecią lokalizacji lokalnej 1, przy użyciu prywatnej komunikacji równorzędnej ExpressRoute jako ścieżki podstawowej. Używa połączenia sieci VPN typu lokacja-lokacja jako ścieżki kopii zapasowej. W pozostałej części tego artykułu odwołujemy się do tego obwodu ExpressRoute jako ExpressRoute 1. Domyślnie obwody usługi ExpressRoute udostępniają nadmiarowe połączenie w celu zapewnienia wysokiej dostępności. W systemie ExpressRoute 1 pomocniczy router graniczny klienta (CE), który jest podłączany do pomocniczego routera programu Microsoft Enterprise Edge (MSEE) jest wyłączony. Czerwona linia nad strzałką podwójnej linii na powyższej ilustracji reprezentuje wyłączony podinterfejs routera CE.
-   Sieć wirtualna piasty jest połączona z siecią lokalizacji lokalnej 2 przy użyciu innej prywatnej komunikacji równorzędnej usługi ExpressRoute. W pozostałej części tego artykułu odwołujemy się do tego drugiego obwodu ExpressRoute jako ExpressRoute 2.
-   ExpressRoute 1 również łączy sieć wirtualną z koncentratorem i lokację lokalną 1 do zdalnej sieci wirtualnej w regionie 2 platformy Azure.

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

Informacje o [konfiguracji][Configuration] topologii testowej.

Dowiedz się więcej na temat [analizy płaszczyzny kontroli][Control-Analysis] konfiguracji testów oraz widoków różnych sieci wirtualnych lub sieci VLAN w topologii.

Dowiedz się więcej na temat [analizy płaszczyzny danych][Data-Analysis] w widokach testów i funkcji monitorowania sieci platformy Azure.

Zobacz [często zadawane pytania dotyczące ExpressRoute][ExR-FAQ] :
-   Dowiedz się, ile obwodów usługi ExpressRoute można połączyć z bramą ExpressRoute.
-   Dowiedz się, ile bram ExpressRoute można połączyć z obwodem ExpressRoute.
-   Dowiedz się więcej na temat innych limitów skalowania ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/TestSetup.png "Diagram topologii testowej"

<!--Link References-->
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: connectivty-interoperability-configuration.md
[Control-Analysis]: connectivty-interoperability-control-plane.md
[Data-Analysis]: connectivty-interoperability-data-plane.md
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha


