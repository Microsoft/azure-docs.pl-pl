---
title: Monitorowanie wirtualnej sieci WAN przy użyciu usługi Azure Monitor Insights
description: Ten artykuł zawiera informacje na temat monitorowania wirtualnej sieci WAN platformy Azure przy użyciu usługi Azure Monitor Insights.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 052fd0ea7619d566e78806580ee7b39e49cc85d2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91448617"
---
# <a name="azure-monitor-insights-for-virtual-wan-preview"></a>Azure Monitor szczegółowych informacji dotyczących wirtualnej sieci WAN (wersja zapoznawcza)

Usługa [Azure monitor Insights](../azure-monitor/insights/network-insights-overview.md) w usłudze Azure Virtual WAN zapewnia użytkownikom i operatorom możliwość wyświetlania stanu i stanu wirtualnej sieci WAN prezentowanej za pomocą autoodnajdywanej mapy topologiczny. Nakładki stanów i Stanów zasobów na mapie zapewniają widok migawki ogólnej kondycji wirtualnej sieci WAN. Na stronie Konfiguracja zasobów portalu sieci WAN można nawigować na mapie za pomocą jednego kliknięcia.

Metryki na poziomie zasobów wirtualnej sieci WAN są zbierane i prezentowane za pośrednictwem wstępnie spakowanego skoroszytu metryk wirtualnych sieci WAN. W skoroszycie są wyświetlane metryki na poziomie wirtualnej sieci WAN, koncentratora, bramy i połączenia. W tym artykule przedstawiono kroki umożliwiające używanie Azure Monitor szczegółowych informacji dotyczących wirtualnej sieci WAN do wyświetlania topologii wirtualnej sieci WAN i metryk wszystkich w jednym miejscu.

> [!NOTE]
> Opcja menu **Insights** w portalu wirtualnej sieci WAN jest obecnie wdrażana. Podczas wycofywania tego menu możesz uzyskać dostęp do skoroszytu topologii i metryk wirtualnych sieci WAN przy użyciu Azure Monitor sieci. Aby uzyskać więcej informacji, zobacz [Azure monitor dla sieci](../azure-monitor/insights/network-insights-overview.md). 
>

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Aby wykonać kroki opisane w tym artykule, musisz mieć wirtualną sieć WAN z co najmniej jednym koncentratorem. Aby utworzyć wirtualną sieć WAN i centrum, wykonaj kroki opisane w następujących artykułach:

* [Tworzenie wirtualnej sieci WAN](virtual-wan-site-to-site-portal.md#openvwan)
* [Tworzenie koncentratora](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-vwan-topology"></a><a name="topology"></a>Wyświetl topologię VWAN

Przejdź do **Azure Portal**  >  **wirtualnej sieci WAN**. W menu **monitor** w lewym okienku wybierz pozycję **Insights (wersja zapoznawcza)**. Zostanie wyświetlony widok **szczegółowe informacje** . Pokazuje on mapę zależności wirtualnej sieci WAN i minimalny skoroszyt z **metrykami** wysokiego poziomu.

**Rysunek 1. menu monitorowanie > Insights**

:::image type="content" source="./media/azure-monitor-insights/monitor-menu.png" alt-text="Zrzut ekranu pokazujący widok wglądu w szczegółowe dane (wersja zapoznawcza)." lightbox="./media/azure-monitor-insights/monitor-menu.png":::

W widoku **wglądu w szczegółowe** dane można wyświetlić wykryte zasoby wirtualnej sieci WAN. Te zasoby obejmują centra, bramy, zapory, połączenia i sieci wirtualne szprych, urządzeń WUS innych firm i gałęzie w kompleksowej wirtualnej sieci WAN. Aby zapoznać się z przykładem, zobacz **rysunek 2**.

Stan i stan zasobu są kodowane kolorami i nałożone na ikony zasobów na mapie. Metryki wirtualnych sieci WAN wysokiego poziomu, takie jak pojemność centrów i użycie bramy, są wyświetlane po prawej stronie okna w postaci minipaska.

**Rysunek 2: widok szczegółowych informacji**

:::image type="content" source="./media/azure-monitor-insights/insights-view.png" alt-text="Zrzut ekranu pokazujący widok wglądu w szczegółowe dane (wersja zapoznawcza)." lightbox="./media/azure-monitor-insights/insights-view.png":::

## <a name="dependency-view"></a><a name="dependency"></a>Widok zależności

Widok **zależności** wirtualnej sieci WAN pomaga wizualizować połączony widok wszystkich wirtualnych zasobów sieci WAN w szerokim stopniu zorganizowanym w architekturę typu Hub i szprych.

**Rysunek 3: widok zależności VWAN**

:::image type="content" source="./media/azure-monitor-insights/dependency-map.png" alt-text="Zrzut ekranu pokazujący widok wglądu w szczegółowe dane (wersja zapoznawcza)." lightbox="./media/azure-monitor-insights/dependency-map.png":::

Mapa widoku **zależności** przedstawia następujące zasoby jako połączone wykresy:

* Wirtualne centra sieci WAN w różnych regionach świadczenia usługi Azure.
* Sieci wirtualne szprych połączone bezpośrednio z centrum.
* Lokacje oddziałów sieci VPN i usługi Azure ExpressRoute oraz użytkownicy P2S połączeni z każdym koncentratorem za pośrednictwem odpowiednich połączeń ExpressRoute, S2S i P2S oraz bramy sieci wirtualnej.
* Zapory platformy Azure (w tym serwery proxy zapory innych firm) wdrożone w centrum (zabezpieczonym koncentratorze).
* Urządzeń WUS innych firm (wirtualne urządzenia sieciowe) wdrożone w sieciach wirtualnych szprych.

Mapa zależności zawiera również powiązane sieci wirtualne (sieci wirtualne, które są połączone za pomocą komunikacji równorzędnej z wirtualnymi sieciami WAN szprych).

Mapa zależności umożliwia łatwe nawigowanie do ustawień konfiguracji poszczególnych zasobów. Na przykład możesz umieścić wskaźnik myszy nad zasobem centrum, aby wyświetlić podstawową konfigurację zasobów, np. region centrum i prefiks centrum. Kliknij prawym przyciskiem myszy, aby uzyskać dostęp do strony Azure Portal zasobu centrum.

**Ilustracja 4. przechodzenie do informacji dotyczących zasobów**

:::image type="content" source="./media/azure-monitor-insights/resource-information.png" alt-text="Zrzut ekranu pokazujący widok wglądu w szczegółowe dane (wersja zapoznawcza).":::

Wyszukiwanie i pasek filtru w widoku **zależności** zapewnia łatwy sposób wyszukiwania w grafie. Różne filtry zapewniają pomoc w zawężaniu wyszukiwania do określonej ścieżki i stanu.

**Rysunek 5. wyszukiwanie i filtrowanie**

:::image type="content" source="./media/azure-monitor-insights/search-filter.png" alt-text="Zrzut ekranu pokazujący widok wglądu w szczegółowe dane (wersja zapoznawcza)." lightbox="./media/azure-monitor-insights/search-filter.png":::

## <a name="detailed-metrics"></a><a name="detailed"></a>Szczegółowe metryki

Możesz wybrać opcję **Wyświetl szczegółowe metryki** , aby uzyskać dostęp do strony **metryki** szczegółowych. Strona **metryki** to pulpit nawigacyjny, który jest wstępnie skonfigurowany przy użyciu oddzielnych kart. Te karty zapewniają wgląd w wydajność wirtualnej sieci WAN, wydajność i wykorzystanie zasobów na poziomie sieci wirtualnej i na poziomie centrum oraz na poziomie poszczególnych połączeń.

**Ilustracja 6. szczegółowy pulpit nawigacyjny metryk**

:::image type="content" source="./media/azure-monitor-insights/detailed-metrics.png" alt-text="Zrzut ekranu pokazujący widok wglądu w szczegółowe dane (wersja zapoznawcza)." lightbox="./media/azure-monitor-insights/detailed-metrics.png":::

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej, zobacz [metryki w Azure monitor](../azure-monitor/platform/data-platform-metrics.md).
* Pełny opis wszystkich metryk wirtualnych sieci WAN zawiera temat [wirtualne dzienniki i metryki sieci WAN](logs-metrics.md).
