---
title: Azure Monitor dla sieci (wersja zapoznawcza)
description: Krótki przegląd dla Azure Monitor sieci, który zapewnia kompleksowy wgląd w kondycję i metryki dla wszystkich wdrożonych zasobów sieciowych bez żadnej konfiguracji.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/24/2020
ms.openlocfilehash: 0d0e8c4806784cf9b00712ad1bf45bca958a7eb1
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91995364"
---
# <a name="azure-monitor-for-networks-preview"></a>Azure Monitor dla sieci (wersja zapoznawcza)
Azure Monitor dla sieci zapewnia kompleksowy wgląd w [kondycję](../../service-health/resource-health-checks-resource-types.md) i [metryki](../platform/metrics-supported.md) dla wszystkich wdrożonych zasobów sieciowych bez żadnej konfiguracji.  Zapewnia również dostęp do wszystkich możliwości monitorowania sieci, takich jak [monitor połączeń](../../network-watcher/connection-monitor-preview.md), [Rejestrowanie przepływów dla sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń)](../../network-watcher/network-watcher-nsg-flow-logging-overview.md), [Analiza ruchu](../../network-watcher/traffic-analytics.md)i inne funkcje [diagnostyki](../../network-watcher/network-watcher-monitoring-overview.md#diagnostics) sieci.

Azure Monitor dla sieci ma strukturę następujących najważniejszych składników monitorowania:
- [Kondycja sieci i metryki](#networkhealth)
- [Łączność](#connectivity)
- [Ruch](#traffic)
- [Zestaw narzędzi diagnostycznych](#diagnostictoolkit)

## <a name="network-health-and-metrics"></a><a name="networkhealth"></a>Kondycja sieci i metryki

Strona **przegląd** Azure monitor dla sieci zapewnia bezproblemowy sposób wizualizacji spisu zasobów sieciowych wraz z kondycją zasobów i alertami. Jest podzielony na cztery kluczowe obszary funkcjonalne — wyszukiwanie i filtrowanie, Resource Health i metryki, alerty. i widok zależności

![Strona przeglądu](media/network-insights-overview/overview.png)

### <a name="search-and-filtering"></a>Wyszukiwanie i filtrowanie
Widok Kondycja zasobów i alerty można dostosować za pomocą filtrów, takich jak **subskrypcja**, **Grupa zasobów** i **Typ zasobu**. Pole wyszukiwania umożliwia wyszukiwanie w ramach właściwości zasobów.

Pole wyszukiwania może służyć do wyszukiwania zasobów i skojarzonych zasobów. Na przykład publiczny adres IP jest skojarzony z Application Gateway. Wyszukiwanie publicznych adresów IP nazwa DNS będzie identyfikować zarówno publiczny, jak i skojarzony Application Gateway.

![Azure Monitor dla sieci — wyszukiwanie](media/network-insights-overview/search.png)


### <a name="resource-health-and-metric"></a>Resource Health i metryka
Każdy kafelek reprezentuje typ zasobu, z liczbą wystąpień wdrożonych w ramach wszystkich wybranych subskrypcji wraz ze stanem kondycji zasobu. W poniższym przykładzie zostały wdrożone 45 połączenia z lokacją i sieci VPN, 44 są w dobrej kondycji i 1 niedostępne.

![Azure Monitor sieci — Kondycja zasobów](media/network-insights-overview/resource-health.png)

Kliknięcie opcji niedostępne połączenia z systemem i sieci VPN powoduje uruchomienie widoku metryki. 

![Azure Monitor sieci — widok metryki](media/network-insights-overview/metric-view.png)

Możesz kliknąć każdy element w widoku siatki. Kliknij ikonę kondycji, aby przekierować do kondycji zasobów dla tego połączenia. Kliknij pozycję alerty, aby przekierować na stronę alerty i metryki odpowiednio dla tego połączenia. 

### <a name="alerts"></a>Alerty
Siatka **alertów** po prawej stronie zawiera widok wszystkich alertów wygenerowanych dla wybranych zasobów we wszystkich subskrypcjach. Kliknij liczbę alertów, aby przejść do strony szczegółów alertów.

### <a name="dependency-view"></a>Widok zależności
Widok **zależności** pomaga wizualizować sposób konfiguracji zasobu. Obecnie jest obsługiwany widok zależności dla Application Gateway, wirtualnej sieci WAN i Load Balancer. Na przykład w przypadku Application Gateway można uzyskać dostęp do widoku zależności, klikając nazwę zasobu Application Gateway w widoku siatki metryk. Dotyczy to również wirtualnych sieci WAN i Load Balancer.

![Azure Monitor dla sieci — widok Application Gateway](media/network-insights-overview/application-gateway.png)

Widok **zależności** dla Application Gateway zawiera uproszczony widok sposobu, w jaki adresy IP frontonu są połączone z odbiornikami, regułami i pulą zaplecza. Krawędzie łączące są kodowane kolorami i zawierają dodatkowe informacje na podstawie kondycji puli zaplecza. Widok zawiera również szczegółowy widok Application Gateway metryk i metryki dla wszystkich powiązanych pul zaplecza, takich jak zestaw skalowania maszyn wirtualnych i wystąpienia maszyn wirtualnych.

![Azure Monitor dla sieci — widok zależności](media/network-insights-overview/dependency-view.png)

Wykres zależności umożliwia łatwe nawigowanie do ustawień konfiguracji. Kliknij prawym przyciskiem myszy pulę zaplecza, aby uzyskać dostęp do innych funkcji. Na przykład, jeśli Pula zaplecza jest maszyną wirtualną, można bezpośrednio uzyskać dostęp do usługi VM Insights i Network Watcher rozwiązywać problemy z połączeniami.

![Azure Monitor dla sieci — menu Widok zależności](media/network-insights-overview/dependency-view-menu.png)

Wyszukiwanie i pasek filtru w widoku zależności umożliwiają łatwe przeszukiwanie grafu. Na przykład wyszukiwanie *AppGWTestRule* w poniższym przykładzie spowoduje zawężenie widoku graficznego do wszystkich węzłów połączonych za pośrednictwem *AppGWTestRule*.

![Azure Monitor dla sieci — przykład wyszukiwania](media/network-insights-overview/search-example.png)

Różne filtry zapewniają pomoc w zawężaniu do określonej ścieżki i stanu. Na przykład wybierz pozycję tylko *zła kondycja* z listy rozwijanej **stan kondycji** , aby wyświetlić wszystkie krawędzie, w których stan jest *nieprawidłowy*.

Kliknij pozycję **szczegółowy widok metryki** , aby uruchomić wstępnie skonfigurowany skoroszyt ze szczegółowymi metrykami dla Application Gateway, wszystkie zasoby puli zaplecza i frontony adresy IP. 

## <a name="connectivity"></a><a name="connectivity"></a>Łączność

Karta **łączność** zapewnia bezproblemowa sposób wizualizacji wszystkich testów skonfigurowanych przy użyciu monitora połączeń i [monitora połączeń (wersja zapoznawcza)](../../network-watcher/connection-monitor-preview.md) dla wybranego zestawu subskrypcji.

![Karta łączność w Azure Monitor dla sieci](media/network-insights-overview/azure-monitor-for-networks-connectivity-tab.png)

Testy są pogrupowane według kafelków źródła i miejsca docelowe i wyświetlają stan osiągalności dla każdego testu. Dostępne ustawienia zapewniają łatwy dostęp do konfigurowania kryteriów osiągalności na podstawie testów zakończonych niepowodzeniem (%) i RTT (MS). Po ustawieniu wartości stan każdego testu zostanie zaktualizowany na podstawie kryteriów wyboru.

![Testy łączności w Azure Monitor dla sieci](media/network-insights-overview/azure-monitor-for-networks-connectivity-tests.png)

Kliknięcie dowolnego kafelka źródłowego lub docelowego powoduje uruchomienie widoku metryki.

![Metryki łączności w Azure Monitor dla sieci](media/network-insights-overview/azure-monitor-for-networks-connectivity-metrics.png)


Możesz kliknąć każdy element w widoku siatki. Kliknij ikonę **osiągalności** , aby przekierować do strony portalu **monitora połączeń** , aby wyświetlić przeskoki według topologii przeskoku i wykryte problemy z łącznością. Kliknij pozycję **alerty** , aby przekierować do alertów i **sprawdzać, czy czas trwania przekroczenia procentu lub czasu rundy** zostanie przekierowany na stronę metryki wybranego monitora połączeń.

Siatka **alertów**   po prawej stronie zawiera widok wszystkich alertów wygenerowanych dla testów łączności skonfigurowanych dla wszystkich subskrypcji. Kliknij liczbę alertów, aby przejść do strony szczegółów alertów.

## <a name="traffic"></a><a name="traffic"></a>Ruch
Karta ruch zapewnia dostęp do wszystkich sieciowych grup zabezpieczeń skonfigurowanych dla [dzienników przepływów sieciowej grupy zabezpieczeń](../../network-watcher/network-watcher-nsg-flow-logging-overview.md) i [Analiza ruchu](../../network-watcher/traffic-analytics.md) dla wybranego zestawu subskrypcji i pogrupowanych według lokalizacji. Funkcja wyszukiwania dostępna na tej karcie umożliwia zidentyfikowanie sieciowych grup zabezpieczeń skonfigurowanego dla przeszukanego adresu IP. Możesz wyszukać dowolny adres IP w swoim środowisku, a w widoku regionalnym z rozdzielonymi fragmentami zostaną wyświetlone wszystkie sieciowych grup zabezpieczeń wraz z dziennikami przepływów sieciowej grupy zabezpieczeń i stanem konfiguracji analizy ruchu.

![Widok ruchu w Azure Monitor dla sieci](media/network-insights-overview/azure-monitor-for-networks-traffic-view.png)

Kliknięcie dowolnego kafelka regionu powoduje uruchomienie widoku siatki, który umożliwia łatwe wyświetlanie i Konfigurowanie dzienników przepływów sieciowej grupy zabezpieczeń oraz Analiza ruchu.  

![Widok obszaru ruchu w Azure Monitor dla sieci](media/network-insights-overview/azure-monitor-for-networks-traffic-region-view.png)

Możesz kliknąć każdy element w widoku siatki. Kliknij pozycję stan konfiguracji, aby edytować dziennik przepływu sieciowej grupy zabezpieczeń i konfigurację Analiza ruchu. Kliknij pozycję alerty, aby przekierować do alertów ruchu skonfigurowanych dla wybranej sieciowej grupy zabezpieczeń. Analogicznie, możesz przejść do widoku Analiza ruchu, klikając obszar roboczy.  

Siatka **alertów**   po prawej stronie zawiera widok wszystkich alertów opartych na obszarze roboczym Analiza ruchu w ramach wszystkich subskrypcji. Kliknij liczbę alertów, aby przejść do strony szczegółów alertów.

## <a name="diagnostic-toolkit"></a><a name="diagnostictoolkit"></a> Zestaw narzędzi diagnostycznych
Zestaw narzędzi diagnostycznych zapewnia dostęp do wszystkich funkcji diagnostycznych dostępnych do rozwiązywania problemów z siecią. Z tej listy rozwijanej uzyskasz dostęp do funkcji, takich [jak przechwytywanie pakietów](../../network-watcher/network-watcher-packet-capture-overview.md), [Rozwiązywanie problemów z siecią VPN](../../network-watcher/network-watcher-troubleshoot-overview.md), [Rozwiązywanie problemów z połączeniami](../../network-watcher/network-watcher-connectivity-overview.md), [Następny przeskok](../../network-watcher/network-watcher-next-hop-overview.md) i [weryfikacja przepływu IP](../../network-watcher/network-watcher-ip-flow-verify-overview.md).

![Karta zestawu narzędzi diagnostycznych](media/network-insights-overview/azure-monitor-for-networks-diagnostic-toolkit.png)

## <a name="troubleshooting"></a>Rozwiązywanie problemów 

Ogólne wskazówki dotyczące rozwiązywania problemów można znaleźć w artykule dotyczącym szczegółowych informacji o [rozwiązywaniu problemów](troubleshoot-workbooks.md)opartych na skoroszycie.

Ta sekcja pomoże w diagnozowaniu i rozwiązywaniu problemów z typowymi problemami, które mogą wystąpić podczas korzystania z Azure Monitor dla sieci. Skorzystaj z poniższej listy, aby znaleźć informacje istotne dla danego problemu.

### <a name="resolving-performance-issues-or-failures"></a>Rozwiązywanie problemów z wydajnością lub niepowodzeń

Aby pomóc w rozwiązywaniu problemów związanych z siecią, które są identyfikowane za pomocą Azure Monitor dla sieci, zapoznaj się z dokumentacją rozwiązywania problemów dotyczących nieprawidłowego zasobu. Poniżej znajdują się linki do rozwiązywania problemów z dużymi używanymi usługami.
* Virtual Network (Sieć wirtualna)
* Application Gateway
* VPN Gateway
* ExpressRoute 
* Moduł równoważenia obciążenia 

### <a name="why-dont-i-see-the-resources-from-all-the-subscriptions-i-have-selected"></a>Dlaczego nie widzę zasobów ze wszystkich wybranych subskrypcji

W usłudze Network Insights można wyświetlać tylko zasoby z 5 subskrypcji jednocześnie. 

### <a name="i-want-to-make-changes-or-add-additional-visualizations-to-network-insights-how-do-i-do-so"></a>Chcę wprowadzić zmiany lub dodać dodatkowe wizualizacje do usługi Network Insights, jak to zrobić

Aby wprowadzić zmiany, wybierz pozycję "Edytuj tryb", aby zmodyfikować skoroszyt, a następnie Zapisz swoją służbę jako nowy skoroszyt powiązany z określoną subskrypcją i grupą zasobów.

### <a name="what-is-the-time-grain-once-we-pin-any-part-of-the-workbooks"></a>Co to jest ziarno czasu, gdy przypinam wszystkie części skoroszytów

Wykorzystujemy ziarno czasu "Auto", dlatego zależy od tego, jaki zakres czasu jest wybrany.

### <a name="what-is-the-time-range-when-any-part-of-the-workbook-is-pinned"></a>Jaki jest zakres czasu, gdy dowolna część skoroszytu jest przypięta

Zakres czasu zależy od ustawień pulpitu nawigacyjnego.

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-the-network-insights"></a>Co zrobić, jeśli chcę zobaczyć inne dane lub utworzyć własne wizualizacje? Jak mogę wprowadzić zmiany w usłudze Network Insights

Możesz edytować skoroszyt widoczny w dowolnym panelu bocznym i szczegółowy widok metryki, korzystając z trybu edycji, a następnie zapisać swoją służbę jako nowy skoroszyt, który będzie zawierał wszystkie nowe zmiany.


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o monitorowaniu sieci na [platformie Azure Network Watcher?](../../network-watcher/network-watcher-monitoring-overview.md).
- Dowiedz się, jakie scenariusze skoroszyty są przeznaczone do obsługi, jak tworzyć nowe i dostosowywać istniejące raporty, a inne dzięki przeglądowi [Tworzenie interaktywnych raportów przy użyciu skoroszytów Azure monitor](../platform/workbooks-overview.md).