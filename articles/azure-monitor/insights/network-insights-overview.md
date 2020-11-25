---
title: Usługa Azure Monitor dla sieci
description: Przegląd Azure Monitor dla sieci, który zapewnia kompleksowy wgląd w kondycję i metryki dla wszystkich wdrożonych zasobów sieciowych bez żadnej konfiguracji.
ms.subservice: ''
ms.topic: conceptual
author: KumudD
ms.author: kumud
ms.date: 11/25/2020
ms.openlocfilehash: 52ca879d5a680d0e62cc469e768236eac11f3719
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96030222"
---
# <a name="azure-monitor-for-networks"></a>Usługa Azure Monitor dla sieci
Azure Monitor dla sieci zapewnia kompleksowy wgląd w [kondycję](../../service-health/resource-health-checks-resource-types.md) i [metryki](../platform/metrics-supported.md) dla wszystkich wdrożonych zasobów sieciowych, bez konieczności konfigurowania. Zapewnia również dostęp do funkcji monitorowania sieci, takich jak [monitor połączeń](../../network-watcher/connection-monitor-preview.md), [Rejestrowanie przepływów dla sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń)](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)i [Analiza ruchu](../../network-watcher/traffic-analytics.md). Zapewnia inne funkcje [diagnostyki](../../network-watcher/network-watcher-monitoring-overview.md#diagnostics) sieci.

Azure Monitor dla sieci ma strukturę następujących najważniejszych składników monitorowania:
- [Kondycja sieci i metryki](#networkhealth)
- [Połączenia](#connectivity)
- [Ruch](#traffic)
- [Zestaw narzędzi diagnostycznych](#diagnostictoolkit)

## <a name="network-health-and-metrics"></a><a name="networkhealth"></a>Kondycja sieci i metryki

Strona **przegląd** Azure monitor dla sieci zapewnia łatwy sposób wizualizacji spisu zasobów sieciowych wraz z kondycją zasobów i alertami. Jest on podzielony na cztery kluczowe obszary funkcjonalne: wyszukiwanie i filtrowanie, Kondycja zasobów i metryki, alerty i widok zależności.

[ ![ Zrzut ekranu przedstawiający stronę przeglądu](media/network-insights-overview/overview.png)](media/network-insights-overview/overview.png)#lightbox)

### <a name="search-and-filtering"></a>Wyszukiwanie i filtrowanie
Widok Kondycja zasobów i alerty można dostosować za pomocą filtrów, takich jak **subskrypcja**, **Grupa zasobów** i **Typ**.

Możesz użyć pola wyszukiwania do wyszukiwania zasobów i skojarzonych z nimi zasobów. Na przykład publiczny adres IP jest skojarzony z bramą aplikacji. Wyszukiwanie nazwy DNS publicznego adresu IP spowoduje zwrócenie zarówno publicznego adresu IP, jak i skojarzonej bramy aplikacji:

[![Zrzut ekranu przedstawiający Azure Monitor wyników wyszukiwania w sieciach.](media/network-insights-overview/search.png)](media/network-insights-overview/search.png#lightbox)


### <a name="resource-health-and-metrics"></a>Kondycja zasobów i metryki
W poniższym przykładzie każdy kafelek reprezentuje typ zasobu. Kafelek przedstawia liczbę wystąpień tego typu zasobu wdrożonych w ramach wszystkich wybranych subskrypcji. Wyświetla również stan kondycji zasobu. W tym przykładzie zostały wdrożone 105 połączenia z lokacjami i sieci VPN. 103 są w dobrej kondycji i 2 są niedostępne.

![Zrzut ekranu przedstawiający kondycję zasobów i metryki w Azure Monitor dla sieci.](media/network-insights-overview/resource-health.png)

W przypadku wybrania niedostępnych połączeń między lokacjami i sieci VPN zostanie wyświetlony widok metryki: 

![Zrzut ekranu pokazujący widok metryk w Azure Monitor dla sieci.](media/network-insights-overview/metric-view.png)

Możesz wybrać dowolny element w widoku siatki. Wybierz ikonę w kolumnie **kondycja** , aby uzyskać kondycję zasobów dla tego połączenia. Wybierz wartość w kolumnie **alert** , aby przejść do strony alerty i metryki dla połączenia. 

### <a name="alerts"></a>Alerty
Pole **alert** po prawej stronie zawiera widok wszystkich alertów wygenerowanych dla wybranych zasobów we wszystkich subskrypcjach. Wybierz liczbę alertów, aby przejść do strony szczegółów alertów.

### <a name="dependency-view"></a>Widok zależności
Widok zależności ułatwia wizualizowanie sposobu konfiguracji zasobu. Widok zależności jest obecnie dostępny dla usługi Azure Application Gateway, wirtualnej sieci WAN platformy Azure i Azure Load Balancer. Na przykład w przypadku Application Gateway można uzyskać dostęp do widoku zależności, wybierając nazwę zasobu Application Gateway w widoku siatki metryk. Można to zrobić w przypadku wirtualnych sieci WAN i Load Balancer.

![Sreenshot, który pokazuje widok Application Gateway w Azure Monitor dla sieci.](media/network-insights-overview/application-gateway.png)

Widok zależności dla Application Gateway zawiera uproszczony widok sposobu, w jaki adresy IP frontonu są połączone z odbiornikami, regułami i pulą zaplecza. Linie łączące są kodowane kolorami i zawierają dodatkowe informacje na podstawie kondycji puli zaplecza. Widok zawiera również szczegółowy widok Application Gateway metryk i metryki dla wszystkich powiązanych pul zaplecza, takich jak zestaw skalowania maszyn wirtualnych i wystąpienia maszyn wirtualnych.

[![Zrzut ekranu pokazujący widok zależności w Azure Monitor dla sieci.](media/network-insights-overview/dependency-view.png)](media/network-insights-overview/dependency-view.png#lightbox)

Wykres zależności zapewnia łatwą nawigację do ustawień konfiguracji. Kliknij prawym przyciskiem myszy pulę zaplecza, aby uzyskać dostęp do innych informacji. Na przykład, jeśli Pula zaplecza jest maszyną wirtualną, możesz bezpośrednio uzyskać dostęp do usługi VM Insights i rozwiązania Azure Network Watcher Connection, aby zidentyfikować problemy z łącznością:

![Zrzut ekranu pokazujący menu Widok zależności w Azure Monitor dla sieci.](media/network-insights-overview/dependency-view-menu.png)

Wyszukiwanie i pasek filtru w widoku zależności zapewnia łatwy sposób wyszukiwania w grafie. Na przykład jeśli szukasz **AppGWTestRule** w poprzednim przykładzie, widok zostanie przeskalowany w dół do wszystkich węzłów połączonych za pośrednictwem AppGWTestRule:

![Zrzut ekranu przedstawiający przykład wyszukiwania w Azure Monitor dla sieci.](media/network-insights-overview/search-example.png)

Różne filtry ułatwiają skalowanie w dół do określonej ścieżki i stanu. Na przykład wybierz pozycję tylko **zła kondycja** z listy **stan kondycji** , aby wyświetlić wszystkie krawędzie, dla których stan jest nieprawidłowy.

Wybierz pozycję **Wyświetl szczegółowe metryki** , aby otworzyć wstępnie skonfigurowany skoroszyt, który zawiera szczegółowe metryki dotyczące bramy aplikacji, wszystkich zasobów puli zaplecza i adresów IP frontonu. 

## <a name="connectivity"></a><a name="connectivity"></a>Łączność

Karta **łączność** zapewnia łatwy sposób wizualizacji wszystkich testów skonfigurowanych za pomocą [monitora połączeń](../../network-watcher/connection-monitor-overview.md) i monitora połączeń (klasycznego) dla wybranego zestawu subskrypcji.

![Zrzut ekranu przedstawiający kartę łączność w Azure Monitor dla sieci.](media/network-insights-overview/azure-monitor-for-networks-connectivity-tab.png)

Testy są pogrupowane według kafelków **źródła** i **miejsca docelowe** i wyświetlają stan osiągalności dla każdego testu. Dostępne ustawienia zapewniają łatwy dostęp do konfiguracji dla kryteriów osiągalności, na podstawie testów zakończonych niepowodzeniem (%) i RTT (MS). Po ustawieniu wartości stan każdej testowej aktualizacji na podstawie kryteriów wyboru.

[![Zrzut ekranu przedstawiający testy łączności w Azure Monitor dla sieci.](media/network-insights-overview/azure-monitor-for-networks-connectivity-tests.png)](media/network-insights-overview/azure-monitor-for-networks-connectivity-tests.png#lightbox)

Możesz wybrać dowolny kafelek źródłowy lub docelowy, aby otworzyć widok metryk:

[![Zrzut ekranu pokazujący metryki łączności w Azure Monitor dla sieci.](media/network-insights-overview/azure-monitor-for-networks-connectivity-metrics.png)](media/network-insights-overview/azure-monitor-for-networks-connectivity-metrics.png#lightbox)


Możesz wybrać dowolny element w widoku siatki. Wybierz ikonę w kolumnie **osiągalność** , aby przejść do strony portalu monitora połączeń i wyświetlić topologię przeskoków przez przeskok oraz łączność, która ma wpływ na zidentyfikowane problemy. Wybierz wartość w kolumnie **alert** , aby przejść do alertów. Wybierz wykresy w kolumnach **sprawdzaj pomyślność** i **czas błądzenia (MS)** , aby przejść do strony metryki wybranego monitora połączeń.

Pole **alert** po prawej stronie zawiera widok wszystkich alertów wygenerowanych dla testów łączności skonfigurowanych dla wszystkich subskrypcji. Wybierz liczbę alertów, aby przejść do strony szczegółów alertów.

## <a name="traffic"></a><a name="traffic"></a>Ruch
Karta **ruch** zapewnia dostęp do wszystkich sieciowych grup zabezpieczeń skonfigurowanych dla [dzienników przepływów sieciowej grupy zabezpieczeń](../../network-watcher/network-watcher-nsg-flow-logging-overview.md) i [Analiza ruchu](../../network-watcher/traffic-analytics.md) dla wybranego zestawu subskrypcji, pogrupowane według lokalizacji. Funkcja wyszukiwania dostępna na tej karcie umożliwia zidentyfikowanie sieciowych grup zabezpieczeń skonfigurowanych dla przeszukanego adresu IP. Możesz wyszukać dowolny adres IP w swoim środowisku. Widok regionalny z rozdziałami będzie wyświetlał wszystkie sieciowych grup zabezpieczeń wraz z dziennikami przepływów sieciowej grupy zabezpieczeń i stanem konfiguracji Analiza ruchu.

[![Zrzut ekranu przedstawiający kartę ruch w Azure Monitor dla sieci.](media/network-insights-overview/azure-monitor-for-networks-traffic-view.png)](media/network-insights-overview/azure-monitor-for-networks-traffic-view.png#lightbox)

W przypadku wybrania dowolnego kafelka obszaru zostanie wyświetlony widok siatki. Siatka zawiera dzienniki przepływu sieciowej grupy zabezpieczeń i Analiza ruchu w widoku, który można łatwo odczytać i skonfigurować:  

[![Zrzut ekranu pokazujący widok obszaru ruchu w Azure Monitor dla sieci.](media/network-insights-overview/azure-monitor-for-networks-traffic-region-view.png)](media/network-insights-overview/azure-monitor-for-networks-traffic-region-view.png#lightbox)

Możesz wybrać dowolny element w widoku siatki. Wybierz ikonę w kolumnie **stan konfiguracji Flowlog** , aby edytować dziennik przepływu sieciowej grupy zabezpieczeń i konfigurację Analiza ruchu. Wybierz wartość w kolumnie **alert** , aby przejść do alertów ruchu skonfigurowanych dla wybranych sieciowej grupy zabezpieczeń. Analogicznie, możesz przejść do widoku Analiza ruchu, wybierając **obszar roboczy Analiza ruchu**.  

Pole **alert** po prawej stronie zawiera widok wszystkich alertów opartych na obszarze roboczym Analiza ruchu we wszystkich subskrypcjach. Wybierz liczbę alertów, aby przejść do strony szczegółów alertów.

## <a name="diagnostic-toolkit"></a><a name="diagnostictoolkit"></a> Zestaw narzędzi diagnostycznych
Zestaw narzędzi diagnostycznych zapewnia dostęp do wszystkich funkcji diagnostycznych dostępnych do rozwiązywania problemów z siecią. Ta lista rozwijana służy do uzyskiwania dostępu do funkcji, takich jak [przechwytywanie pakietów](../../network-watcher/network-watcher-packet-capture-overview.md), [Rozwiązywanie problemów z siecią VPN](../../network-watcher/network-watcher-troubleshoot-overview.md), [Rozwiązywanie problemów z połączeniami](../../network-watcher/network-watcher-connectivity-overview.md), [Następny przeskok](../../network-watcher/network-watcher-next-hop-overview.md)i [weryfikacja przepływu IP](../../network-watcher/network-watcher-ip-flow-verify-overview.md):

![Zrzut ekranu przedstawiający kartę zestaw narzędzi diagnostycznych.](media/network-insights-overview/azure-monitor-for-networks-diagnostic-toolkit.png)

## <a name="troubleshooting"></a>Rozwiązywanie problemów 

Aby uzyskać ogólne wskazówki dotyczące rozwiązywania problemów, zobacz artykuł dotyczący [rozwiązywania problemów](troubleshoot-workbooks.md)ze szczegółowymi informacjami w skoroszycie.

Ta sekcja ułatwi diagnozowanie i rozwiązywanie typowych problemów, które mogą wystąpić podczas korzystania z Azure Monitor dla sieci. 

### <a name="how-do-i-resolve-performance-problems-or-failures"></a>Jak mogę rozwiązać problemy z wydajnością lub błędy?

Aby dowiedzieć się więcej o rozwiązywaniu problemów związanych z siecią, które są identyfikowane za pomocą Azure Monitor dla sieci, zapoznaj się z dokumentacją rozwiązywania problemów dotyczących nieprawidłowego zasobu. 

Poniżej znajdują się linki do rozwiązywania problemów dotyczących często używanych usług. Aby uzyskać więcej informacji dotyczących rozwiązywania problemów dotyczących tych usług, zobacz inne artykuły w sekcji Rozwiązywanie problemów w spisie treści usługi.
* [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-peering-issues)
* [Usługa Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/create-gateway-internal-load-balancer-app-service-environment)
* [Azure VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot)
* [Azure ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-expressroute-overview) 
* [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-troubleshoot) 

### <a name="why-dont-i-see-the-resources-for-all-the-subscriptions-ive-selected"></a>Dlaczego nie widzę zasobów dla wszystkich wybranych subskrypcji?

W usłudze Network Insights można wyświetlać zasoby tylko dla pięciu subskrypcji jednocześnie. 

### <a name="how-do-i-make-changes-or-add-visualizations-to-network-insights"></a>Jak mogę wprowadzić zmiany lub dodać wizualizacje do usługi Network Insights?

Aby wprowadzić zmiany, wybierz **tryb edycji** , aby zmodyfikować skoroszyt. Zmiany można następnie zapisać jako nowy skoroszyt powiązany z wydaną subskrypcją i grupą zasobów.

### <a name="whats-the-time-grain-after-i-pin-any-part-of-the-workbooks"></a>Co to jest ziarno czasu po przypięciu dowolnej części skoroszytów?

W usłudze Network Insights jest wykorzystana **Funkcja** Time ziarno, dlatego ziarno czasu zależy od wybranego zakresu czasu.

### <a name="whats-the-time-range-when-any-part-of-a-workbook-is-pinned"></a>Jaki jest zakres czasu, w którym jest przypięta jakakolwiek część skoroszytu?

Zakres czasu zależy od ustawień pulpitu nawigacyjnego.

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-network-insights"></a>Co zrobić, jeśli chcę zobaczyć inne dane lub utworzyć własne wizualizacje? Jak mogę wprowadzić zmiany w usłudze Network Insights?

Można edytować skoroszyt widoczny w dowolnym panelu bocznym lub szczegółowym widoku metryki przy użyciu trybu edycji. Zmiany można następnie zapisać jako nowy skoroszyt.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o monitorowaniu sieci: [co to jest platforma Azure Network Watcher?](../../network-watcher/network-watcher-monitoring-overview.md)
- Zapoznaj się ze scenariuszami skoroszytów, które są przeznaczone do obsługi, tworzenia raportów i dostosowywania istniejących raportów oraz nie tylko: [Tworzenie interaktywnych raportów za pomocą skoroszytów Azure monitor](../platform/workbooks-overview.md)
