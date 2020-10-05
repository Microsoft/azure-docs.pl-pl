---
title: Przegląd Azure Monitor | Microsoft Docs
description: Omówienie usług i funkcji Microsoft składających się na pełną strategię monitorowania usług i aplikacji platformy Azure.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 10/07/2019
ms.openlocfilehash: 005068c8e81adb9a79a4e6dc7e86a9bfb39902a1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "90088633"
---
# <a name="azure-monitor-overview"></a>Omówienie usługi Azure Monitor

Azure Monitor maksymalizuje dostępność i wydajność aplikacji i usług, udostępniając kompleksowe rozwiązanie do zbierania, analizowania i działania na telemetrii z chmur i środowisk lokalnych. Usługa ta pomaga interpretować działanie aplikacji i proaktywnie identyfikuje problemy dotyczące aplikacji i zasobów, od których zależą.

Poniżej przedstawiono kilka przykładów tego, co można zrobić z Azure Monitor:

- Wykrywaj i Diagnozuj problemy między aplikacjami i zależnościami, korzystając z [Application Insights](app/app-insights-overview.md).
- Skorelowanie problemów z infrastrukturą przy użyciu [Azure monitor dla maszyn wirtualnych](insights/vminsights-overview.md) i [Azure monitor dla kontenerów](insights/container-insights-overview.md).
- Przechodzenie do szczegółów danych monitorowania za pomocą [log Analytics](log-query/log-query-overview.md) na potrzeby rozwiązywania problemów i głębokiej diagnostyki.
- Obsługa operacji na dużą skalę przy użyciu [inteligentnych alertów](platform/alerts-smartgroups-overview.md) i [zautomatyzowanych akcji](platform/alerts-action-rules.md).
- Twórz wizualizacje przy użyciu [pulpitów nawigacyjnych](learn/tutorial-logs-dashboards.md) i [skoroszytów](platform/workbooks-overview.md)platformy Azure.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4qXeL]


[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="overview"></a>Omówienie

Poniższy diagram przedstawia ogólny widok usługi Azure Monitor. Na środku diagramu są magazyny danych dla metryk i dzienników, które są dwoma podstawowymi typami danych używanymi przez Azure Monitor. Po lewej stronie znajdują się [źródła danych monitorowania](platform/data-sources.md) , które wypełniają te [magazyny danych](platform/data-platform.md). Po prawej stronie znajdują się różne funkcje, które Azure Monitor wykonuje przy użyciu tych zebranych danych, takich jak analiza, alerty i przesyłanie strumieniowe do systemów zewnętrznych.

![Omówienie usługi Azure Monitor](media/overview/overview.png)

## <a name="monitoring-data-platform"></a>Platforma danych monitorowania

Wszystkie dane zbierane przez Azure Monitor pasują do jednego z dwóch podstawowych typów, [metryk i dzienników](platform/data-platform.md). [Metryki](platform/data-platform-metrics.md) to wartości liczbowe, które opisują część systemu w konkretnym momencie. Są one lekkie i mogą obsługiwać niemal scenariusze w czasie rzeczywistym. [Dzienniki](platform/data-platform-logs.md) zawierają różne rodzaje danych zorganizowanych w rekordy z różnymi zestawami właściwości dla każdego typu. Dane telemetryczne, takie jak zdarzenia i ślady, są przechowywane jako dzienniki oprócz danych wydajności, dzięki czemu można je ze sobą łączyć na potrzeby analizy.

W przypadku wielu zasobów platformy Azure zobaczysz dane zebrane przez Azure Monitor bezpośrednio na stronie Przegląd w Azure Portal. Zapoznaj się z dowolną maszyną wirtualną na przykład, gdy zobaczysz kilka wykresów, które wyświetlają metryki wydajności. Kliknij dowolny wykres, aby otworzyć dane w [Eksploratorze metryk](platform/metrics-charts.md) w Azure Portal, co pozwala na wykres wartości wielu metryk w czasie.  Możesz wyświetlić wykresy interaktywnie lub przypiąć je do pulpitu nawigacyjnego, aby wyświetlić inne wizualizacje.

![Diagram przedstawia dane metryk przepływające do Eksplorator metryk, które mają być używane w wizualizacjach.](media/overview/metrics.png)

Dane dziennika zbierane przez Azure Monitor mogą być analizowane za pomocą [zapytań](log-query/log-query-overview.md) , aby szybko pobierać, konsolidować i analizować zebrane dane.  Można tworzyć i testować zapytania przy użyciu [log Analytics](./log-query/log-query-overview.md) w Azure Portal, a następnie bezpośrednio analizować dane przy użyciu różnych narzędzi lub zapisywać zapytania do użycia z [wizualizacjami](visualizations.md) lub [regułami alertów](platform/alerts-overview.md).

Azure Monitor używa wersji [języka zapytań Kusto](/azure/kusto/query/) używanej przez platformę Azure Eksplorator danych, która jest odpowiednia dla prostych zapytań dzienników, ale również zawiera zaawansowane funkcje, takie jak agregacje, sprzężenia i Inteligentna analiza. Możesz szybko poznać język zapytań przy użyciu [wielu lekcji](log-query/get-started-queries.md).  Specjalne instrukcje udostępniono dla użytkowników, którzy znają już [SQL](log-query/sql-cheatsheet.md) i [Splunk](log-query/splunk-cheatsheet.md).

![Diagram przedstawia dane dzienników przepływające do Log Analytics na potrzeby analizy.](media/overview/logs.png)

## <a name="what-data-does-azure-monitor-collect"></a>Jakie dane zbiera usługa Azure Monitor?

Usługa Azure Monitor może zbierać dane z różnych źródeł. Monitorowanie danych aplikacji zachodzi w różnych warstwach — od aplikacji przez system operacyjny i usługi aż do samej platformy. Usługa Azure Monitor zbiera dane z każdej z następujących warstw:

- **Dane monitorowania aplikacji**: dane dotyczące wydajności i funkcjonalności kodu, który zapisano, niezależnie od jego platformy.
- **Dane monitorowania systemu operacyjnego gościa**: dane dotyczące systemu operacyjnego, w którym działa aplikacja. Może się to odbywać na platformie Azure, w innej chmurze lub lokalnie. 
- **Dane monitorowania zasobów platformy Azure**: Dane dotyczące działania zasobów platformy Azure.
- **Dane monitorowania subskrypcji platformy Azure**: dane dotyczące operacji i zarządzania subskrypcją platformy Azure, a także dane dotyczące kondycji i działania samej platformy Azure. 
- **Dane monitorowania dzierżawy platformy Azure**: dane dotyczące działania usług platformy Azure na poziomie dzierżawy, takie jak Azure Active Directory.

Zaraz po utworzeniu subskrypcji platformy Azure i rozpoczęciu dodawania zasobów, takich jak maszyny wirtualne i aplikacje internetowe, usługa Azure Monitor rozpoczyna zbieranie danych.  [Rejestruje dzienniki aktywności](platform/platform-logs-overview.md) podczas tworzenia lub modyfikowania zasobów. [Metryki](platform/data-platform.md) informują o sposobie wykonywania zasobów i zasobach, których zużywa. 

Rozszerzanie zbieranych danych do rzeczywistej operacji zasobów przez [włączenie diagnostyki](platform/platform-logs-overview.md) i [dodanie agenta](platform/agent-windows.md) do zasobów obliczeniowych. Spowoduje to zebranie danych telemetrycznych dotyczących wewnętrznej operacji zasobu i umożliwi skonfigurowanie różnych [źródeł danych](platform/agent-data-sources.md) w celu zbierania dzienników i metryk z systemu operacyjnego gościa z systemami Windows i Linux. 

Włącz monitorowanie [aplikacji App Services](app/azure-web-apps.md) lub maszyny wirtualnej [oraz aplikacji zestawu skalowania maszyn wirtualnych](app/azure-vm-vmss-apps.md), aby umożliwić Application Insights Zbieranie szczegółowych informacji o aplikacji, takich jak wyświetlanie stron, żądania aplikacji i wyjątki. Sprawdź dostępność aplikacji, konfigurując [Test dostępności](app/monitor-web-app-availability.md) w celu zasymulowania ruchu użytkownika.

### <a name="custom-sources"></a>Źródła niestandardowe

Azure Monitor może zbierać dane dziennika z dowolnego klienta REST za pomocą [interfejsu API modułu zbierającego dane](platform/data-collector-api.md). Dzięki temu można tworzyć niestandardowe scenariusze monitorowania i zwiększać monitorowanie do zasobów, które nie ujawniają danych telemetrycznych w innych źródłach.

## <a name="insights"></a>Insights
Dane monitorowania są przydatne tylko wtedy, gdy mogą zwiększyć widoczność do działania środowiska obliczeniowego. Azure Monitor obejmuje kilka funkcji i narzędzi, które zapewniają cenne informacje dotyczące aplikacji i innych zasobów, od których zależą. [Monitorowanie rozwiązań](insights/solutions.md) i funkcji, takich jak [Application Insights](app/app-insights-overview.md) i [Azure monitor dla kontenerów](insights/container-insights-overview.md) , zapewnia szczegółowe informacje o różnych aspektach aplikacji i określonych usługach platformy Azure. 

### <a name="application-insights"></a>Application Insights
[Application Insights](app/app-insights-overview.md) monitoruje dostępność, wydajność i użycie aplikacji sieci Web, niezależnie od tego, czy są one hostowane w chmurze, czy lokalnie. Korzysta ona z zaawansowanej platformy do analizy danych w Azure Monitor, aby zapewnić szczegółowe informacje o operacjach aplikacji i zdiagnozować błędy bez oczekiwania na zgłoszenie ich przez użytkownika. Application Insights obejmuje punkty połączenia z różnymi narzędziami programistycznymi i integruje się z programem Visual Studio, aby obsługiwać procesy DevOps.

![App Insights](media/overview/app-insights.png)

### <a name="azure-monitor-for-containers"></a>Usługa Azure Monitor dla kontenerów
[Azure monitor for Containers](insights/container-insights-overview.md) to funkcja przeznaczona do monitorowania wydajności obciążeń kontenera wdrożonych do zarządzanych klastrów Kubernetes hostowanych w usłudze Azure Kubernetes Service (AKS). Zapewnia wgląd w wydajność przez zbieranie metryk pamięci i procesora z kontrolerów, węzłów i kontenerów, które są dostępne w Kubernetes za pośrednictwem interfejsu API metryk. Gromadzone są też dzienniki kontenerów.  Po włączeniu monitorowania z klastrów Kubernetes te metryki i dzienniki są automatycznie zbierane za pośrednictwem załączonej wersji agenta Log Analytics dla systemu Linux.

![Kondycja kontenera](media/overview/container-insights.png)

### <a name="azure-monitor-for-vms"></a>Usługa Azure Monitor dla maszyn wirtualnych
[Azure monitor dla maszyn wirtualnych](insights/vminsights-overview.md) monitoruje maszyny wirtualne platformy Azure na dużą skalę, analizując wydajność i kondycję maszyn wirtualnych z systemami Windows i Linux, w tym różne procesy i połączone zależności od innych zasobów i procesów zewnętrznych. Rozwiązanie obejmuje obsługę monitorowania wydajności i zależności aplikacji dla maszyn wirtualnych hostowanych lokalnie lub w innym dostawcy chmury.  


![Szczegółowe informacje o maszynie wirtualnej](media/overview/vm-insights.png)

### <a name="monitoring-solutions"></a>Rozwiązania do monitorowania
[Rozwiązania do monitorowania](insights/solutions.md) w Azure monitor są spakowanymi zestawami logiki, które zapewniają wgląd w konkretną aplikację lub usługę. Obejmują one logikę zbierania danych monitorowania dla aplikacji lub usługi, [zapytania](log-query/log-query-overview.md) do analizy tych danych i [widoki](./platform/view-designer.md) do wizualizacji. Rozwiązania do monitorowania są [dostępne od firmy Microsoft](./monitor-reference.md) i partnerów w celu zapewnienia monitorowania różnych usług platformy Azure i innych aplikacji.

![Rozwiązania do monitorowania](media/overview/solutions-overview.png)

## <a name="responding-to-critical-situations"></a>Reagowanie na sytuacje krytyczne
Oprócz umożliwienia interaktywnego analizowania danych monitorowania, efektywne rozwiązanie do monitorowania musi być w stanie aktywnie reagować na warunki krytyczne określone w zbieranych danych. Może to spowodować wysłanie tekstu lub wiadomości e-mail do administratora odpowiedzialnego za badanie problemu. Lub można uruchomić zautomatyzowany proces, który próbuje poprawić warunek błędu.


### <a name="alerts"></a>Alerty
[Alerty w usłudze Azure monitor](platform/alerts-overview.md) aktywnie powiadamiać użytkownika o warunkach krytycznych i potencjalnie podejmować działania naprawcze. Reguły alertów w oparciu o metryki zapewniają generowanie alertów niemal w czasie rzeczywistym na podstawie wartości liczbowych, natomiast reguły oparte na dziennikach umożliwiają użycie złożonej logiki w danych z wielu źródeł.

Reguły alertów w Azure Monitor używają [grup akcji](platform/action-groups.md), które zawierają unikatowe zestawy odbiorców i akcje, które mogą być współużytkowane przez wiele reguł. W zależności od wymagań grupy akcji mogą wykonywać takie działania jak używanie elementów webhook do uruchamiania alertów zewnętrznych lub do integracji z narzędziami narzędzia ITSM.

![Zrzut ekranu przedstawia alerty w Azure Monitor z ważnością, łącznymi alertami i innymi informacjami.](media/overview/alerts.png)

### <a name="autoscale"></a>Automatyczne skalowanie
Funkcja automatycznego skalowania umożliwia korzystanie z odpowiedniej ilości zasobów, aby obsłużyć obciążenie aplikacji. Umożliwia tworzenie reguł, które używają metryk zbieranych przez Azure Monitor, aby określić, kiedy automatycznie dodawać zasoby do obsłużyć wzrost obciążenia, a także zaoszczędzić pieniądze przez usunięcie zasobów, które są w stanie bezczynnym. Należy określić minimalną i maksymalną liczbę wystąpień oraz logikę, dla której należy zwiększyć lub zmniejszyć zasoby.

![Diagram przedstawia automatyczne skalowanie, z kilkoma serwerami w wierszu o etykiecie czas procesora > 80% i dwa serwery oznaczone jako minimum, trzy serwery jako bieżąca pojemność i pięć jako maksymalne.](media/overview/autoscale.png)

## <a name="visualizing-monitoring-data"></a>Wizualizacja danych monitorowania
[Wizualizacje](visualizations.md) , takie jak wykresy i tabele, to efektywne narzędzia służące do podsumowywania danych monitorowania i przedstawiania ich w różnych odbiorcach. Azure Monitor ma własne funkcje wizualizacji danych monitorowania i wykorzystuje inne usługi platformy Azure do publikowania ich na różnych odbiorcach.

### <a name="dashboards"></a>Pulpity nawigacyjne
[Pulpity nawigacyjne platformy Azure](../azure-portal/azure-portal-dashboards.md) umożliwiają łączenie różnych rodzajów danych, w tym metryk i dzienników, w jednym okienku w [Azure Portal](https://portal.azure.com). Opcjonalnie możesz udostępnić pulpit nawigacyjny innym użytkownikom platformy Azure. Elementy w Azure Monitor można dodać do pulpitu nawigacyjnego platformy Azure oprócz danych wyjściowych zapytania dziennika lub wykresu metryk. Można na przykład utworzyć pulpit nawigacyjny, który łączy kafelki przedstawiające wykres metryk, tabelę dzienników aktywności, wykres użycia Application Insights i dane wyjściowe zapytania dziennika.

![Zrzut ekranu przedstawia pulpit nawigacyjny platformy Azure, który obejmuje kafelki aplikacji i zabezpieczeń oraz inne dostosowywalne informacje.](media/overview/dashboard.png)

### <a name="views"></a>Widoki
[Wyświetla](./platform/view-designer.md) wizualne wizualizacje danych dzienników w Azure monitor.  Każdy widok zawiera pojedynczy kafelek, który przechodzi w dół do kombinacji wizualizacji, takich jak wykresy słupkowe i liniowe, oprócz list podsumowujących krytyczne dane.  Rozwiązania do monitorowania obejmują widoki, które podsumowują dane dla konkretnej aplikacji, i można tworzyć własne widoki do prezentowania danych z dowolnego zapytania dziennika. Podobnie jak w przypadku innych elementów w Azure Monitor, widoki mogą być dodawane do pulpitów nawigacyjnych platformy Azure.

![Zrzut ekranu przedstawia kafelek rozwiązania do monitorowania kontenerów i widok szczegółowy, który zostanie otwarty w przypadku wybrania kafelka.](media/overview/view.png)

### <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com) to usługa analizy biznesowej, która zapewnia interaktywne wizualizacje w różnych źródłach danych i jest skutecznym sposobem udostępniania danych innym osobom w organizacji i poza nią. Power BI można skonfigurować do [automatycznego importowania danych dziennika z Azure monitor](./platform/powerbi.md) , aby skorzystać z tych dodatkowych wizualizacji.


![Power BI](media/overview/power-bi.png)


## <a name="integrate-and-export-data"></a>Integrowanie i eksportowanie danych
Często wymaga się, aby zintegrować Azure Monitor z innymi systemami i tworzyć niestandardowe rozwiązania korzystające z danych monitorowania. Inne usługi platformy Azure współpracują z Azure Monitor w celu zapewnienia tej integracji.

### <a name="event-hub"></a>Centrum zdarzeń
[Azure Event Hubs](../event-hubs/index.yml) to usługa przesyłania strumieniowego i usług pozyskiwania zdarzeń, która umożliwia przekształcanie i przechowywanie danych przy użyciu dowolnego dostawcy analiz w czasie rzeczywistym lub adapterów wsadowych/magazynowych. Użyj Event Hubs, aby [przesyłać strumieniowo dane Azure monitor](platform/stream-monitoring-data-event-hubs.md) do Siem i narzędzi do monitorowania partnerów.


### <a name="logic-apps"></a>Logic Apps
[Logic Apps](https://azure.microsoft.com/services/logic-apps) to usługa, która umożliwia Automatyzowanie zadań i procesów firmy przy użyciu przepływów pracy, które integrują się z różnymi systemami i usługami. Dostępne są działania dotyczące odczytu i zapisu metryk i dzienników w Azure Monitor, co umożliwia tworzenie przepływów pracy integracji z różnymi innymi systemami.


### <a name="api"></a>Interfejs API
Wiele interfejsów API jest dostępnych do odczytu i zapisu metryk i dzienników do i z Azure Monitor oprócz uzyskiwania dostępu do wygenerowanych alertów. Możesz również skonfigurować i pobrać alerty. Zapewnia to bardzo nieograniczone możliwości tworzenia niestandardowych rozwiązań, które integrują się z Azure Monitor.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na następujące tematy:

* [Metryki i dzienniki](platform/data-platform.md) dla danych zbieranych przez Azure monitor.
* [Źródła danych](platform/data-sources.md) , dla których różne składniki aplikacji wysyłają dane telemetryczne.
* [Zapytania dzienników](log-query/log-query-overview.md) do analizowania zebranych danych.
* [Najlepsze rozwiązania](/azure/architecture/best-practices/monitoring) dotyczące monitorowania aplikacji i usług w chmurze.
