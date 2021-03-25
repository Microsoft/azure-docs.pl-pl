---
title: Azure ExpressRoute Insights za pomocą usługi Network Insights
description: Dowiedz się więcej o usłudze Azure ExpressRoute Insights za pomocą usługi Network Insights.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/23/2021
ms.author: duau
ms.openlocfilehash: 7033ea6a1ba6d85f9aa15e14bb9577b2439c59a8
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105050567"
---
# <a name="azure-expressroute-insights-using-network-insights"></a>Azure ExpressRoute Insights za pomocą usługi Network Insights

W tym artykule wyjaśniono, jak usługi Network Insights mogą pomóc w przeglądaniu metryk ExpressRoute i konfiguracji w jednym miejscu. Za poorednictwem usługi Network Insights można wyświetlać mapy topologiczny i pulpity nawigacyjne kondycji zawierające ważne informacje ExpressRoute bez konieczności wykonywania dodatkowych czynności konfiguracyjnych.

:::image type="content" source="./media/expressroute-network-insights/monitor-landing-page.png" alt-text="Zrzut ekranu przedstawiający stronę docelową monitora ExpressRoute." lightbox="./media/expressroute-network-insights/monitor-landing-page-expanded.png":::

## <a name="visualize-functional-dependencies"></a>Wizualizowanie zależności funkcjonalnych

Aby wyświetlić to rozwiązanie, przejdź do strony *Azure monitor* , wybierz pozycję *sieci*, a następnie wybierz kartę *obwody usługi ExpressRoute* . Następnie wybierz przycisk topologia dla obwodu, który chcesz wyświetlić.

Widok zależności funkcjonalnych zawiera jasne zdjęcie Instalatora ExpressRoute, które tworzy relację między różnymi składnikami ExpressRoute (komunikacji równorzędnej, połączeń, bram).

:::image type="content" source="./media/expressroute-network-insights/topology-view.png" alt-text="Zrzut ekranu przedstawiający widok topologii dla usługi Network Insights." lightbox="./media/expressroute-network-insights/topology-view-expanded.png":::

Umieść kursor na dowolnym składniku mapy topologii, aby wyświetlić informacje o konfiguracji. Na przykład Umieść wskaźnik myszy na składniku komunikacji równorzędnej ExpressRoute, aby wyświetlić szczegóły, takie jak przepustowość obwodu i włączanie Global Reach.

:::image type="content" source="./media/expressroute-network-insights/topology-hovered.png" alt-text="Zrzut ekranu przedstawiający aktywowanie zasobów widoku topologii." lightbox="./media/expressroute-network-insights/topology-hovered-expanded.png":::

## <a name="view-a-detailed-and-pre-loaded-metrics-dashboard"></a>Wyświetlanie szczegółowych i wstępnie załadowanych pulpitów nawigacyjnych metryk

Po przejrzeniu topologii konfiguracji ExpressRoute za pomocą widoku zależności funkcjonalnej wybierz opcję **Wyświetl szczegółowe metryki** , aby przejść do widoku szczegółowych metryk, aby poznać wydajność obwodu. Ten widok oferuje uporządkowaną listę połączonych zasobów i bogaty pulpit nawigacyjny ważnych metryk ExpressRoute.

Sekcja **połączone zasoby** zawiera listę podłączonych bram ExpressRoute oraz skonfigurowanych komunikacji równorzędnych, które można wybrać, aby przejść do odpowiedniej strony zasobów.

:::image type="content" source="./media/expressroute-network-insights/linked-resources.png" alt-text="Zrzut ekranu przedstawiający połączony zasób na stronie monitora.":::


Sekcja **metryki ExpressRoute** zawiera wykresy ważnych metryk obwodów dla kategorii **dostępności**, **przepływności**, **porzucania pakietów** i **metryki bramy**.

### <a name="availability"></a>Dostępność

Karta *dostępność* śledzi dostępność protokołu ARP i protokół BGP, wykreślanie danych dla obwodu jako całego i pojedynczego połączenia (podstawowego i pomocniczego). 

:::image type="content" source="./media/expressroute-network-insights/arp-bgp-availability.png" alt-text="Zrzut ekranu przedstawiający wykresy metryk dostępności." lightbox="./media/expressroute-network-insights/arp-bgp-availability-expanded.png":::

### <a name="throughput"></a>Przepływność

Podobnie karta *przepływność* przedstawia łączną przepływność ruchu przychodzącego i wychodzącego obwodu w bitach na sekundę. Możesz również wyświetlić przepływność dla poszczególnych połączeń i każdego typu skonfigurowanej komunikacji równorzędnej.

:::image type="content" source="./media/expressroute-network-insights/throughput.png" alt-text="Zrzut ekranu przedstawiający wykresy metryk przepływności." lightbox="./media/expressroute-network-insights/throughput-expanded.png":::

### <a name="packet-drops"></a>Porzucenia pakietów

Na karcie *opuszcza* zostanie wykreślone porzucone bity/sekundę dla ruchu przychodzącego i wychodzącego za pomocą obwodu. Ta karta zapewnia łatwy sposób monitorowania problemów z wydajnością, które mogą wystąpić w przypadku regularnego lub mniejszej przepustowości obwodu.

:::image type="content" source="./media/expressroute-network-insights/dropped-packets.png" alt-text="Zrzut ekranu przedstawiający wykresy porzuconych pakietów." lightbox="./media/expressroute-network-insights/dropped-packets-expanded.png":::

### <a name="gateway-metrics"></a>Metryki bramy

Na koniec na karcie metryki bramy są wypełniane kluczowe wykresy metryk dla wybranej bramy ExpressRoute (z sekcji połączone zasoby). Ta karta jest używana, gdy trzeba monitorować łączność z określonymi sieciami wirtualnymi.

:::image type="content" source="./media/expressroute-network-insights/gateway-metrics.png" alt-text="Zrzut ekranu przedstawiający metryki dotyczące przepływności bramy i procesora CPU." lightbox="./media/expressroute-network-insights/gateway-metrics-expanded.png":::

## <a name="next-steps"></a>Następne kroki

Skonfiguruj połączenie usługi ExpressRoute.
  
* Dowiedz się więcej o [usłudze Azure ExpressRoute, usłudze](expressroute-introduction.md) [Network insights](../azure-monitor/insights/network-insights-overview.md)i [Network Watcher](../network-watcher/network-watcher-monitoring-overview.md)
* [Tworzenie i modyfikowanie obwodu](expressroute-howto-circuit-arm.md)
* [Tworzenie i modyfikowanie konfiguracji komunikacji równorzędnej](expressroute-howto-routing-arm.md)
* [Link a VNet to an ExpressRoute circuit (Łączenie sieci wirtualnej z obwodem usługi ExpressRoute)](expressroute-howto-linkvnet-arm.md)
* [Dostosowywanie metryk](expressroute-monitoring-metrics-alerts.md) i tworzenie [monitora połączeń](../network-watcher/connection-monitor-overview.md)
