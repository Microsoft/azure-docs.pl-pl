---
title: 'Azure ExpressRoute: modele łączności'
description: W tym artykule opisano różne tryby łączności między sieciami i Microsoft Azure klienta oraz usługi pakietu Office 365. Klienci mogą korzystać z dostawców MPLS, wymian w chmurze i dostawców sieci Ethernet.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: cherylmc
ms.openlocfilehash: 375d2f9d3b455c0495c69f2b23d62b1ab6522710
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84710460"
---
# <a name="expressroute-connectivity-models"></a>Modele połączeń usługi ExpressRoute
Połączenie między siecią lokalną i chmurą firmy Microsoft można utworzyć na trzy sposoby: [kolokacja CloudExchange](#CloudExchange), [połączenie Ethernet typu punkt-punkt](#Ethernet) i [łączność typu dowolna-dowolna (IPVPN)](#IPVPN). Dostawcy połączenia oferują po jednym modelu łączności lub większą ich liczbę. Można współpracować z dostawcą połączenia w celu wybrania najlepszego dla siebie modelu.
<br><br>

![Diagram modelu połączeń usługi ExpressRoute](./media/expressroute-connectivity-models/expressroute-connectivity-models-diagram.png)

## <a name="co-located-at-a-cloud-exchange"></a><a name="CloudExchange"></a>Współdziałanie z wymianą w chmurze
Jeśli użytkownik jest kolokowany w ramach infrastruktury z wymianą w chmurze, może zamówić łączność obejmującą wiele połączeń wirtualnych z chmurą Microsoft za pośrednictwem wymiany sieci Ethernet dostawy kolokacji. Dostawcy kolokacji mogą zaoferować łączność obejmującą wiele połączeń w warstwie 2 lub wiele zarządzanych połączeń w warstwie 3 między infrastrukturą w strukturze kolokacji i chmurą Microsoft.

## <a name="point-to-point-ethernet-connections"></a><a name="Ethernet"></a>Połączenia Ethernet typu punkt-punkt
Lokalne centra danych/biura można połączyć z chmurą Microsoft za pośrednictwem łączy Ethernet typu punkt-punkt. Dostawcy sieci Ethernet typu punkt-punkt oferują połączenia w warstwie 2 lub zarządzane połączenia w warstwie 3 między witryną użytkownika i chmurą Microsoft.

## <a name="any-to-any-ipvpn-networks"></a><a name="IPVPN"></a>Sieci dowolnych (IPVPN)
Sieć WAN można zintegrować z chmurą Microsoft. Dostawcy sieci IPVPN (zwykle MPLS VPN) oferują łączność typu dowolna-dowolna między biurami oddziałów i centrami danych. Chmurę Microsoft można połączyć z siecią WAN w taki sposób, aby wyglądało to tak jak każde inne biuro oddziału. Dostawcy sieci WAN oferują zazwyczaj łączność zarządzaną w warstwie 3. Wszystkie możliwości i funkcje usługi ExpressRoute są identyczne we wszystkich powyższych modelach łączności. 

## <a name="next-steps"></a>Następne kroki
* Więcej informacji na temat połączeń ExpressRoute i domen routingu. Zobacz artykuł [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (Obwody i domeny routingu usługi ExpressRoute).
* Dowiedz się więcej na temat funkcji usługi ExpressRoute. Zobacz [ExpressRoute — opis techniczny](expressroute-introduction.md)
* Znajdź dostawcę usługi. Zobacz artykuł [ExpressRoute partners and peering locations](expressroute-locations.md) (Partnerzy i lokalizacje komunikacji równorzędnej usługi ExpressRoute).
* Upewnij się, że zostały spełnione wszystkie wymagania wstępne. Zobacz artykuł [ExpressRoute prerequisites](expressroute-prerequisites.md) (Wymagania wstępne usługi ExpressRoute).
* Zapoznaj się z wymaganiami dotyczącymi [routingu](expressroute-routing.md), [NAT](expressroute-nat.md) i [QoS](expressroute-qos.md).
* Skonfiguruj połączenie usługi ExpressRoute.
  * [Create an ExpressRoute circuit (Tworzenie obwodu usługi ExpressRoute)](expressroute-howto-circuit-portal-resource-manager.md)
  * [Configure routing (Konfigurowanie routingu)](expressroute-howto-routing-portal-resource-manager.md)
  * [Link a VNet to an ExpressRoute circuit (Łączenie sieci wirtualnej z obwodem usługi ExpressRoute)](expressroute-howto-linkvnet-portal-resource-manager.md)