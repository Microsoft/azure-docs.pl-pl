---
title: Przegląd Azure Monitor | Microsoft Docs
description: Omówienie usług i funkcji Microsoft składających się na pełną strategię monitorowania usług i aplikacji platformy Azure.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 11/17/2019
ms.openlocfilehash: 7c48311612d48ef616e5b4c0eefaaa0ae7bb2e84
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96451278"
---
# <a name="azure-monitor-overview"></a>Omówienie usługi Azure Monitor

Azure Monitor pomaga zmaksymalizować dostępność i wydajność aplikacji i usług. Oferuje kompleksowe rozwiązanie do zbierania, analizowania i działania w ramach telemetrii w środowiskach w chmurze i lokalnych. Te informacje ułatwiają zrozumienie, w jaki sposób aplikacje są wykonywane i aktywnie identyfikują problemy wpływające na nie i zasoby, od których zależą.

Poniżej przedstawiono kilka przykładów tego, co można zrobić z Azure Monitor:

- Wykrywaj i Diagnozuj problemy między aplikacjami i zależnościami, korzystając z [Application Insights](app/app-insights-overview.md).
- Skorelowanie problemów z infrastrukturą przy użyciu [Azure monitor dla maszyn wirtualnych](insights/vminsights-overview.md) i [Azure monitor dla kontenerów](insights/container-insights-overview.md).
- Przechodzenie do szczegółów danych monitorowania za pomocą [log Analytics](log-query/log-query-overview.md) na potrzeby rozwiązywania problemów i głębokiej diagnostyki.
- Obsługa operacji na dużą skalę przy użyciu [inteligentnych alertów](platform/alerts-smartgroups-overview.md) i [zautomatyzowanych akcji](platform/alerts-action-rules.md).
- Twórz wizualizacje przy użyciu [pulpitów nawigacyjnych](learn/tutorial-logs-dashboards.md) i [skoroszytów](platform/workbooks-overview.md)platformy Azure.
- Zbieraj dane ze [monitorowanych zasobów](./monitor-reference.md) przy użyciu [metryk Azure monitor](./platform/data-platform-metrics.md).

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4qXeL]


[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="overview"></a>Omówienie

Poniższy diagram przedstawia ogólny widok usługi Azure Monitor. Na środku diagramu są magazyny danych dla metryk i dzienników, które są dwoma podstawowymi typami danych używanymi przez Azure Monitor. Po lewej stronie znajdują się [źródła danych monitorowania](platform/data-sources.md) , które wypełniają te [magazyny danych](platform/data-platform.md). Po prawej stronie znajdują się różne funkcje, które Azure Monitor wykonuje przy użyciu zebranych danych. Obejmuje to takie działania jak analiza, alerty i przesyłanie strumieniowe do systemów zewnętrznych.

![Omówienie usługi Azure Monitor](media/overview/overview.png)

## <a name="monitoring-data-platform"></a>Platforma danych monitorowania

Wszystkie dane zbierane przez Azure Monitor pasują do jednego z dwóch podstawowych typów, [metryk i dzienników](platform/data-platform.md). [Metryki](platform/data-platform-metrics.md) to wartości liczbowe, które opisują część systemu w konkretnym momencie. Są one lekkie i mogą obsługiwać niemal scenariusze w czasie rzeczywistym. [Dzienniki](platform/data-platform-logs.md) zawierają różne rodzaje danych zorganizowanych w rekordy z różnymi zestawami właściwości dla każdego typu. Dane telemetryczne, takie jak zdarzenia i ślady, są przechowywane jako dzienniki oprócz danych wydajności, dzięki czemu można je ze sobą łączyć na potrzeby analizy.

W przypadku wielu zasobów platformy Azure zobaczysz dane zebrane przez Azure Monitor bezpośrednio na stronie Przegląd w Azure Portal. Zapoznaj się z dowolną maszyną wirtualną na przykład, gdy zobaczysz kilka wykresów, które wyświetlają metryki wydajności. Kliknij dowolny wykres, aby otworzyć dane w [Eksploratorze metryk](platform/metrics-charts.md) w Azure Portal, co pozwala na wykres wartości wielu metryk w czasie.  Możesz wyświetlić wykresy interaktywnie lub przypiąć je do pulpitu nawigacyjnego, aby wyświetlić inne wizualizacje.

![Diagram przedstawia dane metryk przepływające do Eksplorator metryk, które mają być używane w wizualizacjach.](media/overview/metrics.png)

Dane dziennika zbierane przez Azure Monitor mogą być analizowane za pomocą [zapytań](log-query/log-query-overview.md) , aby szybko pobierać, konsolidować i analizować zebrane dane.  Można tworzyć i testować zapytania przy użyciu [log Analytics](./log-query/log-query-overview.md) w Azure Portal. Następnie można bezpośrednio analizować dane przy użyciu różnych narzędzi lub zapisywać zapytania do użycia z [wizualizacjami](visualizations.md) lub [regułami alertów](platform/alerts-overview.md).

Azure Monitor używa wersji [języka zapytań Kusto](/azure/kusto/query/) , która jest odpowiednia dla prostych zapytań dziennika, ale również zawiera zaawansowane funkcje, takie jak agregacje, sprzężenia i Inteligentna analiza. Możesz szybko poznać język zapytań przy użyciu [wielu lekcji](log-query/get-started-queries.md).  Specjalne instrukcje udostępniono dla użytkowników, którzy znają już [SQL](log-query/sql-cheatsheet.md) i [Splunk](log-query/splunk-cheatsheet.md).

![Diagram przedstawia dane dzienników przepływające do Log Analytics na potrzeby analizy.](media/overview/logs.png)

## <a name="what-data-does-azure-monitor-collect"></a>Jakie dane zbiera usługa Azure Monitor?

Azure Monitor może zbierać dane z [różnych źródeł](monitor-reference.md). Te zakresy są używane przez aplikację, każdy system operacyjny i usługi, na których bazują, w dół do samej platformy. Usługa Azure Monitor zbiera dane z każdej z następujących warstw:

- **Dane monitorowania aplikacji**: dane dotyczące wydajności i funkcjonalności kodu, który zapisano, niezależnie od jego platformy.
- **Dane monitorowania systemu operacyjnego gościa**: dane dotyczące systemu operacyjnego, w którym działa aplikacja. Może się to odbywać na platformie Azure, w innej chmurze lub lokalnie. 
- **Dane monitorowania zasobów platformy Azure**: Dane dotyczące działania zasobów platformy Azure.
- **Dane monitorowania subskrypcji platformy Azure**: dane dotyczące operacji i zarządzania subskrypcją platformy Azure, a także dane dotyczące kondycji i działania samej platformy Azure. 
- **Dane monitorowania dzierżawy platformy Azure**: dane dotyczące działania usług platformy Azure na poziomie dzierżawy, takie jak Azure Active Directory.

Zaraz po utworzeniu subskrypcji platformy Azure i rozpoczęciu dodawania zasobów, takich jak maszyny wirtualne i aplikacje internetowe, usługa Azure Monitor rozpoczyna zbieranie danych.  [Rejestruje dzienniki aktywności](platform/platform-logs-overview.md) podczas tworzenia lub modyfikowania zasobów. [Metryki](platform/data-platform.md) informują o sposobie wykonywania zasobów i zasobach, których zużywa. 

[Włącz diagnostykę](platform/platform-logs-overview.md) , aby rozszerzać dane zbierane do wewnętrznej operacji zasobów.  [Dodaj agenta](platform/agents-overview.md) do zasobów obliczeniowych, aby zbierać dane telemetryczne z systemów operacyjnych gościa. 

Włącz monitorowanie aplikacji za pomocą [Application Insights](app/app-insights-overview.md) , aby zbierać szczegółowe informacje, w tym wyświetlenia stron, żądania aplikacji i wyjątki. Sprawdź dostępność aplikacji, konfigurując [Test dostępności](app/monitor-web-app-availability.md) w celu zasymulowania ruchu użytkownika.

### <a name="custom-sources"></a>Źródła niestandardowe

Azure Monitor może zbierać dane dziennika z dowolnego klienta REST za pomocą [interfejsu API modułu zbierającego dane](platform/data-collector-api.md). Dzięki temu można tworzyć niestandardowe scenariusze monitorowania i zwiększać monitorowanie do zasobów, które nie ujawniają danych telemetrycznych w innych źródłach.

## <a name="insights"></a>Insights
Dane monitorowania są przydatne tylko wtedy, gdy mogą zwiększyć widoczność do działania środowiska obliczeniowego. Szczegółowe [informacje](monitor-reference.md#insights-and-core-solutions) zapewniają dostosowane środowisko monitorowania dla określonych usług platformy Azure. Wymagają one minimalnej konfiguracji i zwiększają wgląd w działanie najważniejszych zasobów.

### <a name="application-insights"></a>Application Insights
[Application Insights](app/app-insights-overview.md) monitoruje dostępność, wydajność i użycie aplikacji sieci Web, niezależnie od tego, czy są one hostowane w chmurze, czy lokalnie. Korzysta ona z zaawansowanej platformy do analizy danych w Azure Monitor, aby zapewnić szczegółowe informacje o operacjach aplikacji. Umożliwia Diagnozowanie błędów bez czekania na zgłoszenie ich przez użytkownika. Application Insights obejmuje punkty połączenia z różnymi narzędziami programistycznymi i integruje się z programem Visual Studio, aby obsługiwać procesy DevOps.

![App Insights](media/overview/app-insights.png)

### <a name="azure-monitor-for-containers"></a>Usługa Azure Monitor dla kontenerów
[Azure monitor kontenerów](insights/container-insights-overview.md) służy do monitorowania wydajności obciążeń kontenera wdrożonych w zarządzanych klastrach Kubernetes hostowanych w usłudze Azure Kubernetes Service (AKS). Zapewnia wgląd w wydajność przez zbieranie metryk z kontrolerów, węzłów i kontenerów, które są dostępne w Kubernetes za pośrednictwem interfejsu API metryk. Gromadzone są też dzienniki kontenerów.  Po włączeniu monitorowania z klastrów Kubernetes te metryki i dzienniki są automatycznie zbierane za pośrednictwem załączonej wersji agenta Log Analytics dla systemu Linux.

![Kondycja kontenera](media/overview/container-insights.png)

### <a name="azure-monitor-for-vms"></a>Usługa Azure Monitor dla maszyn wirtualnych
[Azure monitor dla maszyn wirtualnych](insights/vminsights-overview.md) monitoruje maszyny wirtualne platformy Azure na dużą skalę. Analizuje ona wydajność i kondycję maszyn wirtualnych z systemami Windows i Linux oraz identyfikuje różne procesy i wzajemnie połączone zależności w procesach zewnętrznych. Rozwiązanie obejmuje obsługę monitorowania wydajności i zależności aplikacji dla maszyn wirtualnych hostowanych lokalnie lub w innym dostawcy chmury.  


![Szczegółowe informacje o maszynie wirtualnej](media/overview/vm-insights.png)


## <a name="responding-to-critical-situations"></a>Reagowanie na sytuacje krytyczne
Oprócz umożliwienia interaktywnego analizowania danych monitorowania, efektywne rozwiązanie do monitorowania musi być w stanie aktywnie reagować na warunki krytyczne określone w zbieranych danych. Może to spowodować wysłanie tekstu lub wiadomości e-mail do administratora odpowiedzialnego za badanie problemu. Lub można uruchomić zautomatyzowany proces, który próbuje poprawić warunek błędu.


### <a name="alerts"></a>Alerty
[Alerty w usłudze Azure monitor](platform/alerts-overview.md) aktywnie powiadamiać użytkownika o warunkach krytycznych i potencjalnie podejmować działania naprawcze. Reguły alertów w oparciu o metryki zapewniają alerty niemal w czasie rzeczywistym na podstawie wartości liczbowych. Reguły oparte na dziennikach umożliwiają użycie złożonej logiki w danych z wielu źródeł.

Reguły alertów w Azure Monitor używają [grup akcji](platform/action-groups.md), które zawierają unikatowe zestawy odbiorców i akcje, które mogą być współużytkowane przez wiele reguł. W zależności od wymagań grupy akcji mogą wykonywać takie działania jak używanie elementów webhook do uruchamiania alertów zewnętrznych lub do integracji z narzędziami narzędzia ITSM.

![Zrzut ekranu przedstawia alerty w Azure Monitor z ważnością, łącznymi alertami i innymi informacjami.](media/overview/alerts.png)

### <a name="autoscale"></a>Automatyczne skalowanie
Funkcja automatycznego skalowania umożliwia korzystanie z odpowiedniej ilości zasobów, aby obsłużyć obciążenie aplikacji. Utwórz reguły korzystające z metryk zbieranych przez Azure Monitor, aby określić, kiedy automatycznie dodawać zasoby po zwiększeniu obciążenia. Oszczędność pieniędzy dzięki usunięciu zasobów znajdujących się w stanie bezczynności. Należy określić minimalną i maksymalną liczbę wystąpień oraz logikę, dla której należy zwiększyć lub zmniejszyć zasoby.

![Diagram przedstawia automatyczne skalowanie, z kilkoma serwerami w wierszu o etykiecie czas procesora > 80% i dwa serwery oznaczone jako minimum, trzy serwery jako bieżąca pojemność i pięć jako maksymalne.](media/overview/autoscale.png)

## <a name="visualizing-monitoring-data"></a>Wizualizacja danych monitorowania
[Wizualizacje](visualizations.md) , takie jak wykresy i tabele, to efektywne narzędzia służące do podsumowywania danych monitorowania i przedstawiania ich w różnych odbiorcach. Azure Monitor ma własne funkcje wizualizacji danych monitorowania i wykorzystuje inne usługi platformy Azure do publikowania ich na różnych odbiorcach.

### <a name="dashboards"></a>Pulpity nawigacyjne
[Pulpity nawigacyjne platformy Azure](../azure-portal/azure-portal-dashboards.md) umożliwiają łączenie różnych rodzajów danych w jedno okienko w [Azure Portal](https://portal.azure.com). Opcjonalnie możesz udostępnić pulpit nawigacyjny innym użytkownikom platformy Azure. Dodaj dane wyjściowe zapytania dziennika lub wykresu metryk do pulpitu nawigacyjnego platformy Azure. Można na przykład utworzyć pulpit nawigacyjny, który łączy kafelki przedstawiające wykres metryk, tabelę dzienników aktywności, wykres użycia Application Insights i dane wyjściowe zapytania dziennika.

![Zrzut ekranu przedstawia pulpit nawigacyjny platformy Azure, który obejmuje kafelki aplikacji i zabezpieczeń oraz inne dostosowywalne informacje.](media/overview/dashboard.png)

### <a name="workbooks"></a>Skoroszyty
[Skoroszyty](platform/workbooks-overview.md) zapewniają elastyczną kanwę do analizy danych i tworzenia bogatych raportów wizualnych w Azure Portal. Umożliwiają one naciskanie na wiele źródeł danych z platformy Azure i łączenie ich w ujednolicone interaktywne środowiska. Używaj skoroszytów udostępnianych z usługą Insights lub Utwórz własne ze wstępnie zdefiniowanych szablonów.


![Przykład skoroszytów](media/overview/workbooks.png)

### <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com) to usługa analizy biznesowej, która zapewnia interaktywne wizualizacje w różnych źródłach danych. Jest skutecznym sposobem udostępniania danych innym osobom w organizacji i poza nią. Power BI można skonfigurować do [automatycznego importowania danych dziennika z Azure monitor](./platform/powerbi.md) , aby skorzystać z tych dodatkowych wizualizacji.


![Power BI](media/overview/power-bi.png)


## <a name="integrate-and-export-data"></a>Integrowanie i eksportowanie danych
Często wymaga się, aby zintegrować Azure Monitor z innymi systemami i tworzyć niestandardowe rozwiązania korzystające z danych monitorowania. Inne usługi platformy Azure współpracują z Azure Monitor w celu zapewnienia tej integracji.

### <a name="event-hub"></a>Centrum zdarzeń
[Azure Event Hubs](../event-hubs/index.yml) to usługa przesyłania strumieniowego i usługi pozyskiwania zdarzeń. Dane można przekształcać i przechowywać za pomocą dowolnego dostawcy analiz w czasie rzeczywistym lub kart sieciowych/magazynowych. Użyj Event Hubs, aby [przesyłać strumieniowo dane Azure monitor](platform/stream-monitoring-data-event-hubs.md) do Siem i narzędzi do monitorowania partnerów.


### <a name="logic-apps"></a>Logic Apps
[Logic Apps](https://azure.microsoft.com/services/logic-apps) to usługa, która umożliwia Automatyzowanie zadań i procesów firmy przy użyciu przepływów pracy, które integrują się z różnymi systemami i usługami. W Azure Monitor są dostępne działania odczytu i zapisu metryk i dzienników. Dzięki temu można tworzyć przepływy pracy integrujące się z różnymi innymi systemami.


### <a name="api"></a>Interfejs API
Wiele interfejsów API jest dostępnych do odczytu i zapisu metryk i dzienników do i z Azure Monitor oprócz uzyskiwania dostępu do wygenerowanych alertów. Możesz również skonfigurować i pobrać alerty. Zapewnia to bardzo nieograniczone możliwości tworzenia niestandardowych rozwiązań, które integrują się z Azure Monitor.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej:

* [Metryki i dzienniki](platform/data-platform.md) dla danych zbieranych przez Azure monitor.
* [Źródła danych](platform/data-sources.md) , dla których różne składniki aplikacji wysyłają dane telemetryczne.
* [Zapytania dzienników](log-query/log-query-overview.md) do analizowania zebranych danych.
* [Najlepsze rozwiązania](/azure/architecture/best-practices/monitoring) dotyczące monitorowania aplikacji i usług w chmurze.
