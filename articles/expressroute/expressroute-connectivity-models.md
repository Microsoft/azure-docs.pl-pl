---
title: 'Azure ExpressRoute: modele łączności'
description: Zapoznaj się z łącznością między siecią klienta, Microsoft Azure i usługami Microsoft 365. Klienci mogą korzystać z dostawców MPLS, wymian chmur i sieci Ethernet.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: duau
ms.openlocfilehash: fb35a03b5dd8780445eb27f485966e3c3452feea
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91978762"
---
# <a name="expressroute-connectivity-models"></a>Modele połączeń usługi ExpressRoute
Połączenie między siecią lokalną i chmurą firmy Microsoft można utworzyć na cztery różne sposoby: [CloudExchange wspólne lokalizacje](#CloudExchange), [połączenia Ethernet typu punkt-punkt](#Ethernet), [dowolne-dowolne (IPVPN)](#IPVPN)i [ExpressRoute Direct](#Direct). Dostawcy łączności mogą oferować jeden lub więcej modeli łączności. Można współpracować z dostawcą połączenia w celu wybrania najlepszego dla siebie modelu.
<br><br>

:::image type="content" source="./media/expressroute-connectivity-models/expressroute-connectivity-models-diagram.png" alt-text="Diagram modelu połączeń usługi ExpressRoute":::

## <a name="co-located-at-a-cloud-exchange"></a><a name="CloudExchange"></a>Współdziałanie z wymianą w chmurze
Jeśli znajdujesz się na tym samym miejscu w ramach funkcji z wymianą w chmurze, możesz zamówić wirtualne połączenia krzyżowe w chmurze firmy Microsoft za pomocą wymiany sieci Ethernet dostawcy wspólnej lokalizacji. Dostawcy kolokacji mogą zaoferować łączność obejmującą wiele połączeń w warstwie 2 lub wiele zarządzanych połączeń w warstwie 3 między infrastrukturą w strukturze kolokacji i chmurą Microsoft.

## <a name="point-to-point-ethernet-connections"></a><a name="Ethernet"></a>Połączenia Ethernet typu punkt-punkt
Lokalne centra danych/biura można połączyć z chmurą Microsoft za pośrednictwem łączy Ethernet typu punkt-punkt. Dostawcy sieci Ethernet typu punkt-punkt oferują połączenia w warstwie 2 lub zarządzane połączenia w warstwie 3 między witryną użytkownika i chmurą Microsoft.

## <a name="any-to-any-ipvpn-networks"></a><a name="IPVPN"></a>Sieci typu dowolna-dowolna (IPVPN)
Sieć WAN można zintegrować z chmurą Microsoft. Dostawcy sieci IPVPN (zwykle MPLS VPN) oferują łączność typu dowolna-dowolna między biurami oddziałów i centrami danych. Chmurę Microsoft można połączyć z siecią WAN w taki sposób, aby wyglądało to tak jak każde inne biuro oddziału. Dostawcy sieci WAN oferują zazwyczaj łączność zarządzaną w warstwie 3. Wszystkie możliwości i funkcje usługi ExpressRoute są identyczne we wszystkich powyższych modelach łączności.

## <a name="direct-from-expressroute-sites"></a><a name="Direct"></a>Bezpośrednio z witryn ExpressRoute
Możesz połączyć się bezpośrednio z siecią globalną firmy Microsoft w lokalizacji komunikacji równorzędnej strategicznie dystrybuowanej na całym świecie. Funkcja [ExpressRoute Direct](expressroute-erdirect-about.md) oferuje dwie połączenia o szybkości 100 GB/s lub 10 GB/s, które obsługują łączność aktywną/aktywną w dużej skali.

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