---
title: Logowanie w Azure Monitor | Microsoft Docs
description: Opisuje dzienniki w Azure Monitor, które są używane do zaawansowanej analizy danych monitorowania.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: dae72454cd9c0b3cb7370873619595840b770ed3
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/22/2020
ms.locfileid: "83799973"
---
# <a name="logs-in-azure-monitor"></a>Dzienniki w usłudze Azure Monitor

> [!NOTE]
> Wszystkie dane zbierane przez Azure Monitor pasują do jednego z dwóch podstawowych typów, metryk i dzienników. W tym artykule opisano dzienniki. Zapoznaj się z [metrykami w Azure monitor](data-platform-metrics.md) , aby uzyskać szczegółowy opis metryk oraz [monitorować dane zbierane przez Azure monitor](data-platform.md) w celu porównania dwóch.

Dzienniki w Azure Monitor są szczególnie przydatne do wykonywania złożonej analizy danych z różnych źródeł. W tym artykule opisano sposób, w jaki dzienniki są uporządkowane w Azure Monitor, co można zrobić z danymi i identyfikuje różne źródła danych, które przechowują dane w dziennikach.

> [!NOTE]
> Ważne jest rozróżnienie między dziennikami Azure Monitor i źródłami danych dzienników na platformie Azure. Na przykład zdarzenia na poziomie subskrypcji na platformie Azure są zapisywane w [dzienniku aktywności](platform-logs-overview.md) , który można wyświetlić z menu Azure monitor. Większość zasobów będzie zapisywać informacje operacyjne w [dzienniku zasobów](platform-logs-overview.md) , które można przesłać dalej do różnych lokalizacji. Dzienniki Azure Monitor to platforma danych dziennika, która zbiera dzienniki aktywności i dzienniki zasobów wraz z innymi danymi monitorowania w celu zapewnienia głębokiej analizy w całym zestawie zasobów.

## <a name="what-are-azure-monitor-logs"></a>Co to są dzienniki Azure Monitor?

Dzienniki w Azure Monitor zawierają różne rodzaje danych zorganizowanych w rekordy z różnymi zestawami właściwości dla każdego typu. Dzienniki mogą zawierać wartości liczbowe, takie jak metryki Azure Monitor, ale zazwyczaj zawierają dane tekstowe z szczegółowymi opisami. Różnią się one od danych metryk w zależności od ich struktury i często nie są zbierane w regularnych odstępach czasu. Dane telemetryczne, takie jak zdarzenia i ślady, są przechowywane Azure Monitor dzienników oprócz danych wydajności, dzięki czemu można je ze sobą łączyć na potrzeby analizy.

Typowym typem wpisu dziennika jest zdarzenie, które jest zbierane sporadycznie. Zdarzenia są tworzone przez aplikację lub usługę i zwykle zawierają wystarczająco dużo informacji, aby zapewnić ich własny kontekst. Na przykład zdarzenie może wskazywać, że określony zasób został utworzony lub zmodyfikowany, Nowy host został uruchomiony w odpowiedzi na zwiększony ruch lub wykryto błąd w aplikacji.

 Ponieważ format danych może się różnić, aplikacje mogą tworzyć dzienniki niestandardowe przy użyciu wymaganej struktury. Dane metryk mogą nawet być przechowywane w dziennikach, aby połączyć je z innymi danymi monitorowania na potrzeby trendów i innych analiz danych.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>Co możesz zrobić z dziennikami Azure Monitor?
W poniższej tabeli wymieniono różne sposoby używania dzienników w Azure Monitor.


|  |  |
|:---|:---|
| Analiza | Użyj [log Analytics](../log-query/get-started-portal.md) w Azure Portal, aby napisać [zapytania dzienników](../log-query/log-query-overview.md) i interaktywnie przeanalizować dane dzienników przy użyciu zaawansowanego aparatu analizy Eksplorator danych.<br>Użyj [konsoli Application Insights Analytics](../app/analytics.md) w Azure Portal, aby napisać zapytania dziennika i interaktywnie przeanalizować dane dziennika z Application Insights. |
| Wizualizacja | Przypnij wyniki zapytania jako tabele lub wykresy do [pulpitu nawigacyjnego platformy Azure](../../azure-portal/azure-portal-dashboards.md).<br>Utwórz [skoroszyt](../platform/workbooks-overview.md) , aby połączyć się z wieloma zestawami danych w raporcie interaktywnym. <br>Eksportuj wyniki zapytania, aby [Power BI](powerbi.md) do używania różnych wizualizacji i udostępniania użytkownikom spoza platformy Azure.<br>Eksportuj wyniki zapytania do [Grafana](grafana-plugin.md) , aby wykorzystać jego pulpit nawigacyjny i połączyć się z innymi źródłami danych.|
| Alerty | Skonfiguruj [regułę alertu dziennika](alerts-log.md) , która wysyła powiadomienie lub wykonuje [automatyczne działanie](action-groups.md) , gdy wyniki zapytania pasują do określonego wyniku.<br>Skonfiguruj [regułę alertu metryki](alerts-metric-logs.md) dla niektórych dzienników danych dziennika wyodrębnionych jako metryki. |
| Odczytać | Uzyskiwanie dostępu do wyników zapytania dziennika z wiersza polecenia przy użyciu [interfejsu CLI platformy Azure](/cli/azure/ext/log-analytics/monitor/log-analytics).<br>Uzyskuj dostęp do wyników zapytania dziennika z wiersza polecenia przy użyciu [poleceń cmdlet programu PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights).<br>Uzyskiwanie dostępu do wyników zapytania dziennika z aplikacji niestandardowej przy użyciu [interfejsu API REST](https://dev.loganalytics.io/). |
| Eksportowanie | Utwórz przepływ pracy w celu pobrania danych dziennika i skopiuj go do lokalizacji zewnętrznej przy użyciu [Logic Apps](~/articles/logic-apps/index.yml). |


## <a name="how-is-data-in-azure-monitor-logs-structured"></a>Jak są uporządkowane dane w dziennikach Azure Monitor?
Dane zbierane przez dzienniki Azure Monitor są przechowywane w [log Analytics obszarze roboczym](../platform/design-logs-deployment.md). Każdy obszar roboczy zawiera wiele tabel, które przechowują dane z konkretnego źródła. Chociaż wszystkie tabele korzystają z [niektórych typowych właściwości](log-standard-properties.md), każdy z nich ma unikatowy zestaw właściwości w zależności od rodzaju przechowywanych danych. Nowy obszar roboczy będzie miał standardowy zestaw tabel i więcej tabel zostanie dodany przez różne rozwiązania monitorowania i inne usługi, które zapisują w obszarze roboczym.

Dane dziennika z Application Insights korzystają z tego samego aparatu Log Analytics jako obszary robocze, ale są przechowywane oddzielnie dla każdej monitorowanej aplikacji. Każda aplikacja ma standardowy zestaw tabel służący do przechowywania danych, takich jak żądania aplikacji, wyjątki i wyświetlenia stron.

Zapytania dziennika będą używać danych z obszaru roboczego Log Analytics lub aplikacji Application Insights. [Zapytania obejmującego wiele zasobów](../log-query/cross-workspace-query.md) można użyć do analizowania danych aplikacji wraz z innymi danymi dziennika lub do tworzenia zapytań, w tym wielu obszarów roboczych lub aplikacji.

![Obszary robocze](media/data-platform-logs/workspaces.png)

## <a name="log-queries"></a>Rejestrowanie zapytań
Dane w dziennikach Azure Monitor są pobierane za pomocą [zapytania dziennika](../log-query/log-query-overview.md) zapisanego w [języku zapytania Kusto](../log-query/get-started-queries.md), który umożliwia szybkie pobieranie, konsolidowanie i analizowanie zebranych danych. Użyj [log Analytics](../log-query/portals.md) do zapisywania i testowania zapytań dzienników w Azure Portal. Umożliwia interakcyjne korzystanie z wyników lub Przypinanie ich do pulpitu nawigacyjnego w celu wyświetlenia ich z innymi wizualizacjami.

![Log Analytics](media/data-platform-logs/log-analytics.png)

Otwórz [log Analytics z Application Insights](../app/analytics.md) , aby analizować Application Insights dane.

![Analiza Application Insights](media/data-platform-logs/app-insights-analytics.png)

Możesz również pobrać dane dziennika za pomocą [interfejsu api log Analytics](https://dev.loganalytics.io/documentation/overview) i [interfejsu API REST Application Insights](https://dev.applicationinsights.io/documentation/overview).


## <a name="sources-of-azure-monitor-logs"></a>Źródła dzienników Azure Monitor
Azure Monitor może zbierać dane dzienników z różnych źródeł zarówno w ramach platformy Azure, jak i z zasobów lokalnych. W poniższej tabeli wymieniono różne źródła danych dostępne z różnych zasobów, które zapisują dane do Azure Monitor dzienników. Każdy z nich ma link do szczegółowych informacji na temat każdej wymaganej konfiguracji.

### <a name="azure-tenant-and-subscription"></a>Dzierżawa i subskrypcja platformy Azure

| Dane | Opis |
|:---|:---|
| Azure Active Directory dzienników inspekcji | Skonfigurowane przy użyciu ustawień diagnostycznych dla każdego katalogu. Zobacz [integrowanie dzienników usługi Azure AD z dziennikami Azure monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).  |
| Dzienniki aktywności | Domyślnie przechowywane osobno i mogą być używane dla alertów niemal w czasie rzeczywistym. Zainstaluj rozwiązanie do analizy dzienników aktywności, aby zapisać w obszarze roboczym Log Analytics. Zobacz [zbieranie i analizowanie dzienników aktywności platformy Azure w log Analytics](activity-log-collect.md). |

### <a name="azure-resources"></a>Zasoby platformy Azure

| Dane | Opis |
|:---|:---|
| Diagnostyka zasobów | Skonfiguruj ustawienia diagnostyczne do zapisu w danych diagnostycznych, w tym metryki do obszaru roboczego Log Analytics. [Aby log Analytics, zobacz Przesyłanie strumieniowe dzienników zasobów platformy Azure](resource-logs-collect-workspace.md). |
| Rozwiązania do monitorowania | Rozwiązania do monitorowania umożliwiają zapisywanie danych zbieranych do ich obszaru roboczego Log Analytics. Lista rozwiązań zawiera [szczegółowe informacje na temat zbierania danych dotyczących rozwiązań do zarządzania na platformie Azure](../insights/solutions-inventory.md) . Zobacz sekcję [monitorowanie rozwiązań w Azure monitor](../insights/solutions.md) , aby uzyskać szczegółowe informacje na temat instalowania i używania rozwiązań. |
| Metryki | Wysyłaj metryki platformy dla Azure Monitor zasobów do Log Analytics obszaru roboczego, aby zachować dane dziennika przez dłuższy czas i wykonać nieskomplikowaną analizę z innymi typami danych przy użyciu [języka zapytań Kusto](/azure/kusto/query/). [Aby log Analytics, zobacz Przesyłanie strumieniowe dzienników zasobów platformy Azure](resource-logs-collect-storage.md). |
| Azure Table Storage | Zbieranie danych z usługi Azure Storage, w których niektóre zasoby platformy Azure zapisują dane monitorowania. Zapoznaj [się z tematem Używanie usługi Azure Blob Storage dla usług IIS i Azure Table Storage w przypadku zdarzeń z log Analytics](diagnostics-extension-logs.md). |

### <a name="virtual-machines"></a>Maszyny wirtualne

| Dane | Opis |
|:---|:---|
|  Źródła danych agentów | Źródła danych zbierane z agentów [systemu Windows](agent-windows.md) i [Linux](../learn/quick-collect-linux-computer.md) obejmują zdarzenia, dane dotyczące wydajności i dzienniki niestandardowe. Zobacz [źródła danych agentów w Azure monitor](data-sources.md) , aby uzyskać listę źródeł danych i szczegółów dotyczących konfiguracji. |
| Rozwiązania do monitorowania | Rozwiązania do monitorowania umożliwiają zapisywanie danych zbieranych od agentów do ich obszaru roboczego Log Analytics. Lista rozwiązań zawiera [szczegółowe informacje na temat zbierania danych dotyczących rozwiązań do zarządzania na platformie Azure](../insights/solutions-inventory.md) . Zobacz sekcję [monitorowanie rozwiązań w Azure monitor](../insights/solutions.md) , aby uzyskać szczegółowe informacje na temat instalowania i używania rozwiązań. |
| System Center Operations Manager | Połącz Operations Manager grupę zarządzania, aby Azure Monitor w celu zbierania danych dotyczących zdarzeń i wydajności z agentów lokalnych do dzienników. Aby uzyskać szczegółowe informacje na temat tej konfiguracji [, zobacz Connect Operations Manager, aby log Analytics](om-agents.md) . |


### <a name="applications"></a>Aplikacje

| Dane | Opis |
|:---|:---|
| Żądania i wyjątki | Szczegółowe dane dotyczące żądań i wyjątków aplikacji znajdują się w tabelach _Requests_, _pageViews_i _Exceptions_ . Wywołania [zewnętrznych składników](../app/asp-net-dependencies.md) znajdują się w tabeli _zależności_ . |
| Użycie i wydajność | Wydajność aplikacji jest dostępna w tabelach _Requests_, _browserTimings_ i _liczniki wydajności_ . Dane [metryk niestandardowych](../app/api-custom-events-metrics.md#trackevent) są w tabeli _customMetrics_ .|
| Dane śledzenia | Wyniki [śledzenia rozproszonego](../app/distributed-tracing.md) są przechowywane w tabeli _TRACES_ . |
| Testy dostępności | Dane podsumowujące z [testów dostępności](../app/monitor-web-app-availability.md) są przechowywane w tabeli _availabilityResults_ . Szczegółowe dane z tych testów znajdują się w osobnych magazynach i uzyskuje do nich dostęp z Application Insights w Azure Portal. |

### <a name="insights"></a>Insights

| Dane | Opis |
|:---|:---|
| Usługa Azure Monitor dla kontenerów | Dane spisu i wydajności zbierane przez [Azure monitor dla kontenerów](../insights/container-insights-overview.md). Aby uzyskać listę tabel, zobacz [szczegóły zbierania danych o kontenerach](../insights/container-insights-log-search.md#container-records) . |
| Usługa Azure Monitor dla maszyn wirtualnych | Dane mapy i wydajności zbierane przez [Azure monitor dla maszyn wirtualnych](../insights/vminsights-overview.md). Aby uzyskać szczegółowe informacje na temat wykonywania zapytań dotyczących tych danych [, zobacz temat jak wykonywać zapytania dotyczące dzienników z Azure monitor dla maszyn wirtualnych](../insights/vminsights-log-search.md) . |

### <a name="custom"></a>Niestandardowy 

| Dane | Opis |
|:---|:---|
| Interfejs API REST | Zapisuj dane w obszarze roboczym Log Analytics z dowolnego klienta REST. Aby uzyskać szczegółowe informacje [, zobacz Wysyłanie danych dzienników do Azure monitor z interfejsem API modułu zbierającego dane http](data-collector-api.md) .
| Aplikacja logiki | Zapisuj dowolne dane w obszarze roboczym Log Analytics z przepływu pracy aplikacji logiki za pomocą akcji **modułu zbierającego dane log Analytics Azure** . |

### <a name="security"></a>Zabezpieczenia

| Dane | Opis |
|:---|:---|
| Azure Security Center | [Azure Security Center](/azure/security-center/) przechowuje dane zbierane w log Analytics obszarze roboczym, gdzie można je analizować przy użyciu innych danych dziennika. Zobacz [zbieranie danych w Azure Security Center](../../security-center/security-center-enable-data-collection.md) , aby uzyskać szczegółowe informacje na temat konfiguracji obszaru roboczego. |
| Usługa Azure Sentinel | [Wskaźnik platformy Azure](/azure/sentinel/) umożliwia przechowywanie danych ze źródeł danych w obszarze roboczym log Analytics. Zobacz [łączenie ze źródłami danych](/azure/sentinel/connect-data-sources).  |


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [platformie danych Azure monitor](data-platform.md).
- Dowiedz się więcej o [metrykach w Azure monitor](data-platform-metrics.md).
- Poznaj [dane monitorowania dostępne](data-sources.md) dla różnych zasobów na platformie Azure.
