---
title: Azure Monitor dla sieci (wersja zapoznawcza)
description: Krótki przegląd dla Azure Monitor sieci, który zapewnia kompleksowy wgląd w kondycję i metryki dla wszystkich wdrożonych zasobów sieciowych bez żadnej konfiguracji.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/07/2019
ms.openlocfilehash: 5a85efc5de0d99904da568c97672adf40ce8a414
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2020
ms.locfileid: "90088922"
---
# <a name="azure-monitor-for-networks-preview"></a>Azure Monitor dla sieci (wersja zapoznawcza)
Azure Monitor dla sieci zapewnia kompleksowy wgląd w kondycję i metryki dla wszystkich wdrożonych zasobów sieciowych bez żadnej konfiguracji. Funkcja wyszukiwania zaawansowanego pomaga identyfikować zależności zasobów, które umożliwiają scenariuszom, takim jak identyfikowanie zasobów, które obsługują witrynę sieci Web, wyszukując nazwę hostowanej witryny sieci Web.

Strona **przegląd** Azure monitor dla sieci zapewnia bezproblemowy sposób wizualizacji spisu zasobów sieciowych wraz z kondycją zasobów i alertami. Jest podzielony na cztery kluczowe obszary funkcjonalne:

- Wyszukiwanie i filtrowanie
- Resource Health i metryki
- Alerty 
- Widok zależności

![Strona przeglądu](media/network-insights-overview/overview.png)

## <a name="search-and-filtering"></a>Wyszukiwanie i filtrowanie
Widok Kondycja zasobów i alerty można dostosować za pomocą filtrów, takich jak **subskrypcja**, **Grupa zasobów** i **Typ zasobu**. Pole wyszukiwania umożliwia wyszukiwanie w ramach właściwości zasobów.

Pole wyszukiwania może służyć do wyszukiwania zasobów i skojarzonych zasobów. Na przykład publiczny adres IP jest skojarzony z Application Gateway. Wyszukiwanie publicznych adresów IP nazwa DNS będzie identyfikować zarówno publiczny, jak i skojarzony Application Gateway.

![Zrzut ekranu przedstawia wyniki wyszukiwania nazwy DNS.](media/network-insights-overview/search.png)


## <a name="resource-health-and-metric"></a>Resource Health i metryka
Każdy kafelek reprezentuje typ zasobu, z liczbą wystąpień wdrożonych w ramach wszystkich wybranych subskrypcji wraz ze stanem kondycji zasobu. W poniższym przykładzie zostały wdrożone 105 połączenia z lokacją i sieci VPN, 103 są w dobrej kondycji i 2 niedostępne.

![Kondycja zasobów](media/network-insights-overview/resource-health.png)

Kliknięcie dwóch niedostępnych połączeń ER i VPN spowoduje uruchomienie widoku metryki. 

![Widok metryki](media/network-insights-overview/metric-view.png)

Możesz kliknąć każdy element w widoku siatki. Kliknij ikonę kondycji, aby przekierować do kondycji zasobów dla tego połączenia. Kliknij pozycję alerty, aby przekierować na stronę alerty i metryki odpowiednio dla tego połączenia. 

## <a name="alerts"></a>Alerty
Siatka **alertów** po prawej stronie zawiera widok wszystkich alertów wygenerowanych dla wybranych zasobów we wszystkich subskrypcjach. Kliknij liczbę alertów, aby przejść do strony szczegółów alertów.

## <a name="dependency-view"></a>Widok zależności
Widok **zależności** pomaga wizualizować sposób konfiguracji zasobu. Obecnie jest obsługiwany widok zależności dla Application Gateway, wirtualnej sieci WAN i Load Balancer. Na przykład w przypadku Application Gateway można uzyskać dostęp do widoku zależności, klikając nazwę zasobu Application Gateway w widoku siatki metryk. Dotyczy to również wirtualnych sieci WAN i Load Balancer. 

![Widok Application Gateway](media/network-insights-overview/application-gateway.png)

Widok **zależności** dla Application Gateway zawiera uproszczony widok sposobu, w jaki adresy IP frontonu są połączone z odbiornikami, regułami i pulą zaplecza. Krawędzie łączące są kodowane kolorami i zawierają dodatkowe informacje na podstawie kondycji puli zaplecza. Widok zawiera również szczegółowy widok Application Gateway metryk i metryki dla wszystkich powiązanych pul zaplecza, takich jak zestaw skalowania maszyn wirtualnych i wystąpienia maszyn wirtualnych.

![Widok zależności](media/network-insights-overview/dependency-view.png)

Wykres zależności umożliwia łatwe nawigowanie do ustawień konfiguracji. Kliknij prawym przyciskiem myszy pulę zaplecza, aby uzyskać dostęp do innych funkcji. Na przykład, jeśli Pula zaplecza jest maszyną wirtualną, możesz bezpośrednio uzyskać dostęp do usługi Virtual Machine Insights oraz połączenia Network Watcher, aby rozwiązać problemy z łącznością i zidentyfikować je.

![Menu widoku zależności](media/network-insights-overview/dependency-view-menu.png)

Wyszukiwanie i pasek filtru w widoku zależności umożliwiają łatwe przeszukiwanie grafu. Na przykład wyszukiwanie *AppGWTestRule* w poniższym przykładzie spowoduje zawężenie widoku graficznego do wszystkich węzłów połączonych za pośrednictwem *AppGWTestRule*. 

![Przykład wyszukiwania](media/network-insights-overview/search-example.png)

Różne filtry zapewniają pomoc w zawężaniu do określonej ścieżki i stanu. Na przykład wybierz pozycję tylko *zła kondycja* z listy rozwijanej **stan kondycji** , aby wyświetlić wszystkie krawędzie, w których stan jest *nieprawidłowy*.

Kliknij pozycję **szczegółowy widok metryki** , aby uruchomić wstępnie skonfigurowany skoroszyt ze szczegółowymi metrykami dla bramy aplikacji, wszystkich zasobów puli zaplecza i adresów IP frontonu. 

## <a name="next-steps"></a>Następne kroki 

- Dowiedz się więcej o monitorowaniu sieci na [platformie Azure Network Watcher?](../../network-watcher/network-watcher-monitoring-overview.md).
