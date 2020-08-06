---
title: Monitorowanie wirtualnej sieci WAN platformy Azure przy użyciu usługi Azure Monitor Insights
description: Informacje o monitorowaniu wirtualnej sieci WAN przy użyciu usługi Azure Monitor Insights
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: cherylmc
ms.openlocfilehash: e3316b4a2255652972a0b9deef813f894f993589
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87836075"
---
# <a name="azure-monitor-insights-for-virtual-wan-preview"></a>Azure Monitor szczegółowych informacji dotyczących wirtualnej sieci WAN (wersja zapoznawcza)

[Azure monitor szczegółowe informacje](../azure-monitor/insights/network-insights-overview.md) dotyczące wirtualnej sieci WAN umożliwiają użytkownikom i operatorom Wyświetlanie stanu i stanu wirtualnej sieci WAN, prezentowanej za pomocą autoodnajdywanej mapy topologiczny. Stan i stan zasobu są nadmiarowe na mapie, aby zapewnić widok migawki ogólnej kondycji wirtualnej sieci WAN. Nawigowanie po zasobach jest włączane na mapie za pomocą dostępu jednego kliknięcia do stron konfiguracji zasobów wirtualnego portalu sieci WAN.

Metryki wirtualnego poziomu zasobów sieci WAN są zbierane i prezentowane za pośrednictwem wstępnie spakowanego skoroszytu metryk wirtualnych sieci WAN, który pokazuje metryki na poziomie wirtualnej sieci WAN, centrum, bramy i połączenia. W tym artykule przedstawiono kroki umożliwiające używanie Azure Monitor szczegółowych informacji dotyczących wirtualnej sieci WAN do wyświetlania topologii wirtualnej sieci WAN i metryk wszystkich w jednym miejscu.

> [!NOTE]
> Opcja menu Insights w portalu wirtualnej sieci WAN jest w trakcie wdrażania. Podczas wycofywania menu Insights dla wirtualnej sieci WAN można bezpośrednio uzyskać dostęp do dostępnej topologii i skoroszytu sieci WAN przy użyciu platformy Azure dla sieci. Zobacz [Azure monitor Insights](../azure-monitor/insights/network-insights-overview.md) , aby uzyskać więcej informacji. 
>

## <a name="before-you-begin"></a>Przed rozpoczęciem

W krokach w tym artykule przyjęto założenie, że wirtualna sieć WAN została już wdrożona z co najmniej jednym koncentratorem. Aby utworzyć nową wirtualną sieć WAN i nowe centrum, wykonaj czynności opisane w następujących artykułach:

* [Tworzenie wirtualnej sieci WAN](virtual-wan-site-to-site-portal.md#openvwan)
* [Tworzenie koncentratora](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-vwan-topology"></a><a name="topology"></a>Wyświetl topologię VWAN

W **Azure Portal->wirtualnej sieci WAN**z menu **monitor** po lewej stronie wybierz pozycję **Insights (wersja zapoznawcza)**. Spowoduje to **wyświetlenie widoku szczegółowych**informacji, w którym jest wyświetlana mapa zależności wirtualnej sieci WAN i minie Źródło metryk.

**Rysunek 1. menu monitorowanie szczegółowe**

:::image type="content" source="./media/azure-monitor-insights/monitor-menu.png" alt-text="poznać" lightbox="./media/azure-monitor-insights/monitor-menu.png":::

W widoku **wglądu w szczegółowe** dane można wyświetlić wykryte przez siebie wirtualne zasoby sieci WAN, takie jak centra, bramy, zapory, połączenia i szprychy sieci wirtualnych, inne urządzeń WUS i gałęzie w sieci wirtualnej typu end-to-end, jak pokazano na **rysunku 2**.

Stan i **stan** **zasobu** są kodowane kolorami i nałożone na ikony zasobów na mapie. Metryki wysokiego poziomu sieci WAN, takie jak pojemność koncentratora i użycie bramy, są wyświetlane po prawej stronie za pośrednictwem minipaska skoroszytów.

**Rysunek 2: widok szczegółowych informacji**

:::image type="content" source="./media/azure-monitor-insights/insights-view.png" alt-text="poznać" lightbox="./media/azure-monitor-insights/insights-view.png":::

## <a name="dependency-view"></a><a name="dependency"></a>Widok zależności

Widok **zależności** wirtualnej sieci WAN pomaga wizualizować połączony widok wszystkich wirtualnych zasobów sieci WAN w szerokim stopniu zorganizowanym w architekturę typu Hub i szprych.

**Rysunek 3: widok zależności VWAN**

:::image type="content" source="./media/azure-monitor-insights/dependency-map.png" alt-text="Mapa zależności" lightbox="./media/azure-monitor-insights/dependency-map.png":::

Mapa widoku zależności przedstawia następujące zasoby jako połączone wykresy:

* Wirtualne centra sieci WAN w różnych regionach świadczenia usługi Azure.
* Szprycha sieci wirtualnych, która jest bezpośrednio podłączona do centrum.
* Lokacje oddziałów sieci VPN i ExpressRoute oraz użytkownicy P2S połączeni z każdym koncentratorem za pośrednictwem odpowiednich połączeń ExpressRoute, S2S i P2S oraz bram sieci wirtualnej.
* Zapory platformy Azure (w tym serwery proxy zapory innej firmy) wdrożone w centrum (zabezpieczonym koncentratorze).
* URZĄDZENIE WUS inne firmy (wirtualne urządzenia sieciowe), które są wdrożone w szprychie sieci wirtualnych.

Mapa zależności zawiera również bezpośrednio połączone sieci wirtualnych (sieć wirtualną, która jest równorzędna z wirtualną szprychą sieci WAN sieci wirtualnych).

Mapa zależności umożliwia łatwe nawigowanie do ustawień konfiguracji poszczególnych zasobów. Na przykład możesz umieścić wskaźnik myszy nad zasobem centrum, aby wyświetlić podstawową konfigurację zasobów, taką jak region centrum i prefiks centrum. Kliknij prawym przyciskiem myszy, aby uzyskać dostęp do strony Azure Portal zasobu centrum.

**Ilustracja 4. przechodzenie do informacji dotyczących zasobów**

:::image type="content" source="./media/azure-monitor-insights/resource-information.png" alt-text="Informacje o zasobach":::

Wyszukiwanie i pasek filtru w widoku zależności umożliwiają łatwe przeszukiwanie grafu. Różne filtry zapewniają pomoc w zawężaniu wyszukiwania do określonej ścieżki i stanu.

**Rysunek 5. wyszukiwanie i filtrowanie**

:::image type="content" source="./media/azure-monitor-insights/search-filter.png" alt-text="Wyszukiwanie i pasek filtru" lightbox="./media/azure-monitor-insights/search-filter.png":::

## <a name="detailed-metrics"></a><a name="detailed"></a>Szczegółowe metryki

Możesz wybrać opcję **Wyświetl szczegółowe metryki** , aby uzyskać dostęp do strony **metryki** szczegółowych. Strona metryki jest pulpitem nawigacyjnym, który jest wstępnie skonfigurowany z osobnymi kartami zapewniającymi przydatny wgląd w pojemność zasobów wirtualnej sieci WAN, wydajność i wykorzystanie na poziomie wirtualnego poziomu sieci WAN, na poziomie centrum i poszczególnych połączeń.

**Ilustracja 6. szczegółowy pulpit nawigacyjny metryk**

:::image type="content" source="./media/azure-monitor-insights/detailed-metrics.png" alt-text="szczegółowe metryki" lightbox="./media/azure-monitor-insights/detailed-metrics.png":::

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o metrykach w Azure Monitor, zobacz [metryki w Azure monitor](../azure-monitor/platform/data-platform-metrics.md).
* Pełny opis wszystkich metryk wirtualnych sieci WAN zawiera temat [wirtualne dzienniki i metryki sieci WAN](logs-metrics.md).
