---
title: Szczegółowe informacje na temat Azure Load Balancer
description: Skorzystaj z usługi równoważenia obciążenia, aby uzyskać szybką lokalizację błędów i świadome decyzje projektowe
services: load-balancer
documentationcenter: na
author: erichrt
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2020
ms.author: errobin
ms.openlocfilehash: 88c287c4aa87b4df41bfe6bbc1992262b21d07aa
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2021
ms.locfileid: "98232764"
---
# <a name="using-insights-to-monitor-and-configure-your-azure-load-balancer"></a>Korzystanie z usługi Insights do monitorowania i konfigurowania Azure Load Balancer

Za poorednictwem Azure Monitor dla sieci są udostępniane wizualizacje zależności funkcjonalnych i wstępnie skonfigurowany pulpit nawigacyjny metryk dla modułów równoważenia obciążenia. Te wizualizacje ułatwiają podejmowanie świadomych decyzji projektowych i szybkie lokalizowanie, diagnozowanie i rozwiązywanie wszelkich błędów.

>[!NOTE] 
>Należy pamiętać, że ta funkcja jest dostępna w wersji zapoznawczej, a wstępnie skonfigurowany pulpit nawigacyjny może ulec zmianie, aby ulepszyć środowisko pracy.

>[!IMPORTANT]
>Usługa Load Balancer w warstwie Standardowa jest wymagane, aby wyświetlić metryki z przestrzeni nazw Load Balancer na wstępnie skonfigurowanym pulpicie nawigacyjnym metryk. Nadal będzie można zobaczyć metryki z maszyn wirtualnych, zestawu skalowania maszyn wirtualnych i przestrzeni nazw monitora połączeń, ale zalecamy [uaktualnienie do warstwy Standardowa](./upgrade-basic-standard.md) dla wszystkich obciążeń produkcyjnych, aby korzystać z niezawodnego zestawu metryk Load Balancer.

## <a name="functional-dependency-view"></a>Widok zależności funkcjonalnej

Widok zależności funkcjonalnej umożliwi obrazowanie nawet najbardziej skomplikowanych ustawień modułu równoważenia obciążenia. Dzięki wizualnym informacjom dotyczącym najnowszej konfiguracji Load Balancer można wprowadzać aktualizacje przy zachowaniu konfiguracji.

Aby uzyskać dostęp do tego widoku, przejdź do bloku szczegółowe informacje o zasobie Load Balancer na platformie Azure.

:::image type="content" source="./media/load-balancer-insights/load-balancer-functional-dependency-visual.png" alt-text="Depecition widoku zależności funkcjonalnej. Fronton usługi równoważenia obciążenia może być widoczny w celu nawiązania połączenia z członkami puli zaplecza za pomocą skonfigurowanych reguł. W przypadku usługa Load Balancer w warstwie Standardowa wiersze z reguł równoważenia obciążenia do wystąpień puli zaplecza są kodowane kolorami na podstawie stanu sondy kondycji." border="true":::

W przypadku standardowych modułów równoważenia obciążenia zasoby puli zaplecza są kodowane kolorami ze stanem sondy kondycji wskazującą bieżącą dostępność puli zaplecza do obsługi ruchu. Wraz z powyższą topologią prezentowany jest wykres o stanie kondycji, co zapewnia widok migawki kondycji aplikacji.

## <a name="metrics-dashboard"></a>Pulpit nawigacyjny aplikacji Metryki

W bloku wglądu w dane Load Balancer możesz wybrać bardziej szczegółowe metryki, aby wyświetlić wstępnie skonfigurowany [skoroszyt Azure monitor](../azure-monitor/platform/workbooks-overview.md) zawierający wizualizacje metryk odpowiednie dla określonych aspektów Load Balancer. Ten pulpit nawigacyjny wyświetli stan Load Balancer i linki do odpowiedniej dokumentacji w górnej części strony.

W pierwszej kolejności zostanie wyświetlona karta przegląd. Możesz przechodzić przez dostępne karty, z których każdy zawiera wizualizacje istotne dla określonego aspektu Load Balancer. Jawne wskazówki dla każdej z nich są dostępne na pulpicie nawigacyjnym u dołu każdej karty.

Dostępne są obecnie karty pulpitu nawigacyjnego:
* Omówienie
* Dostępność frontonu i zaplecza
* Przepływność danych
* Dystrybucja przepływu
* Monitory połączeń
* Definicje metryk 

### <a name="overview-tab"></a>Karta przegląd
Karta przegląd zawiera siatkę z możliwością wyszukiwania zawierającą ogólne informacje o dostępności i kondycji ścieżki danych dla każdego z adresów IP frontonu dołączonych do Load Balancer. Te metryki wskazują, czy adres IP frontonu odpowiada, a wystąpienia obliczeniowe w puli zaplecza są indywidualnie odpowiadać na połączenia przychodzące.

Możesz również wyświetlić ogólną przepływność danych dla każdego adresu IP frontonu na tej stronie, aby zrozumieć, czy są wytwarzane i odbierane poziomy oczekiwanego ruchu. Wskazówki w dolnej części strony przekierują do odpowiedniej karty, aby wyświetlić wszystkie nieregularne wartości.

### <a name="frontend-and-backend-availability-tab"></a>Karta dostępność frontonu i zaplecza
Karty dostępność frontonu i zaplecza przedstawiają metryki przepływności ścieżki danych i stanu sondy kondycji prezentowane w kilku użytecznych widokach. Pierwszy wykres pokazuje wartość zagregowaną, dzięki czemu można określić, czy wystąpił problem. Pozostałe wykresy przedstawiają te metryki podzielone według różnych wymiarów, dzięki czemu można rozwiązywać problemy i identyfikować źródła wszelkich problemów dotyczących dostępności przychodzącej.

Przepływ pracy służący do wyświetlania tych wykresów jest dostępny w dolnej części strony z typowymi przyczynami różnych objawów. 

### <a name="data-throughput-tab"></a>Karta przepływność danych
Karta przepływność danych umożliwia sprawdzenie przepływności ruchu przychodzącego i wychodzącego w celu ustalenia, czy wzorce ruchu są zgodnie z oczekiwaniami. Zostanie wyświetlona przepływność danych przychodzących i wychodzących podzielona przez adres IP frontonu i port frontonu, dzięki czemu można określić, czy w jaki sposób uruchomione usługi są wykonywane pojedynczo.

### <a name="flow-distribution"></a>Dystrybucja przepływu
Karta dystrybucja przepływu ułatwia wizualizowanie i zarządzanie liczbą przepływów, które są wysyłane i wytwarzające wystąpienia zaplecza. Pokazuje współczynnik tworzenia przepływu oraz liczbę przepływów dla ruchu przychodzącego i wychodzącego, a także ruch sieciowy dla każdej maszyny wirtualnej i wystąpienia zestawu skalowania maszyn wirtualnych. 

Te widoki mogą dać opinię na temat tego, czy Load Balancer konfiguracja lub wzorce ruchu prowadzą do niezrównoważonego ruchu. Na przykład jeśli skonfigurowano koligację sesji i jeden klient ma nieproporcjonalną liczbę żądań. Poinformuje również o tym, czy zbliża się [Limit przepływu na maszynę wirtualną](../virtual-network/virtual-machine-network-throughput.md#flow-limits-and-active-connections-recommendations) dla rozmiaru maszyny.

### <a name="connection-monitors"></a>Monitory połączeń
Na karcie monitory połączeń zostanie wyświetlone opóźnienie rundy na mapie globalnej dla wszystkich skonfigurowanych [monitorów połączeń](../network-watcher/connection-monitor.md)  . Te wizualizacje zawierają przydatne informacje dotyczące usług o rygorystycznych wymaganiach w zakresie opóźnień. Aby spełnić wymagania, może być konieczne dodanie dodatkowych wdrożeń regionalnych lub przejście do modelu [międzyregionalnego równoważenia obciążenia](./cross-region-overview.md)

### <a name="metric-definitions"></a>Definicje metryk
Karta definicje metryk zawiera wszystkie informacje przedstawione w [artykule metryki wielowymiarowej](./load-balancer-standard-diagnostics.md#multi-dimensional-metrics).

## <a name="next-steps"></a>Następne kroki
* Zapoznaj się z pulpitem nawigacyjnym i podaj opinię, korzystając z poniższego linku, jeśli wszystko, co można ulepszyć
* [Zapoznaj się z dokumentacją metryk, aby dowiedzieć się, jak jest obliczana każda Metryka](./load-balancer-standard-diagnostics.md#multi-dimensional-metrics)
* [Utwórz monitory połączeń dla Load Balancer](../network-watcher/connection-monitor.md)
* [Utwórz własne skoroszyty](../azure-monitor/platform/workbooks-overview.md), a następnie kliknij przycisk Edytuj w szczegółowym pulpicie nawigacyjnym metryk
