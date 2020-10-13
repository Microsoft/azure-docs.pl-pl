---
title: Obsługa użytkowników zdalnych za pomocą usługi Azure ExpressRoute
description: Na tej stronie opisano, jak można korzystać z usługi Azure ExpressRoute, aby umożliwić pracę zdalną z powodu COVID-19 Pandemic.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/22/2020
ms.author: duau
ms.openlocfilehash: d51f47b73fe572ce81d3e7b54f902f94fcd11b5b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89567677"
---
# <a name="using-azure-expressroute-to-create-hybrid-connectivity-to-support-remote-users"></a>Tworzenie łączności hybrydowej do obsługi użytkowników zdalnych za pomocą usługi Azure ExpressRoute

W tym artykule opisano, jak korzystać z usługi ExpressRoute, Azure, Microsoft Network i ekosystemu partnerów platformy Azure do zdalnego działania.

## <a name="connecting-to-azure-services-with-expressroute"></a>Nawiązywanie połączenia z usługami platformy Azure za pomocą usługi ExpressRoute

>[!NOTE]
>W tym artykule opisano, jak korzystać z usługi ExpressRoute, Azure, Microsoft Network i ekosystemu partnera platformy Azure do zdalnego działania i rozwiązywania problemów z siecią, które są dostępne z powodu kryzysu COVID-19.
>

[ExpressRoute](expressroute-introduction.md) to usługa platformy Azure, która umożliwia połączenie prywatne między centrami danych firmy Microsoft i infrastrukturą lokalną lub międzylokalizacyjną. Połączenia ExpressRoute nie odbywają się za pośrednictwem publicznego Internetu. Oferują one bezpieczną łączność, niezawodność i szybkości oraz krótsze i spójne opóźnienia niż typowe połączenia przez Internet.

## <a name="useful-links"></a>Przydatne łącza

* [Jak zwiększyć przepustowość istniejącego obwodu usługi ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md#modify)
* [ExpressRoute monitorowanie, metryki i alerty](expressroute-monitoring-metrics-alerts.md#expressroute-gateway-connections-in-bitsseconds)
* [Optymalizacja tras nad ExpressRoute](expressroute-optimize-routing.md)
* [Usługa Azure ExpressRoute dla Microsoft 365](/microsoft-365/enterprise/azure-expressroute)
* [Zagadnienia dotyczące routingu asymetrycznego](expressroute-asymmetric-routing.md)
* [Jak otworzyć żądanie pomocy technicznej w Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

## <a name="troubleshoot"></a>Rozwiązywanie problemów

* [Verify ExpressRoute Connectivity (Weryfikowanie połączenia usługi ExpressRoute)](expressroute-troubleshooting-expressroute-overview.md)
* Pobieranie tabeli ARP w [Menedżer zasobów](expressroute-troubleshooting-arp-resource-manager.md) i [klasyczny](expressroute-troubleshooting-arp-classic.md)
* [Resetowanie obwodu zakończonego niepowodzeniem](reset-circuit.md)
* [Rozwiązywanie problemów z wydajnością sieci](expressroute-troubleshooting-network-performance.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej na temat modeli łączności ExpressRoute](expressroute-connectivity-models.md)
* Więcej informacji na temat połączeń ExpressRoute i domen routingu. Zobacz [obwody usługi ExpressRoute i domeny routingu](expressroute-circuit-peerings.md)
* Znajdź dostawcę usługi. Zobacz [partnerzy ExpressRoute i lokalizacje komunikacji równorzędnej](expressroute-locations.md)
* Upewnij się, że zostały spełnione wszystkie wymagania wstępne. Zobacz artykuł [ExpressRoute prerequisites](expressroute-prerequisites.md) (Wymagania wstępne usługi ExpressRoute).
* [Tworzenie i modyfikowanie obwodu usługi ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md)
* [Tworzenie i modyfikowanie komunikacji równorzędnej dla obwodu usługi ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Łączenie sieci wirtualnej z obwodem usługi ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
