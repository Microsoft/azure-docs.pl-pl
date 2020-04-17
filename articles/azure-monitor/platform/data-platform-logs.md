---
title: Dzienniki w usłudze Azure Monitor | Dokumenty firmy Microsoft
description: W tym artykule opisano dzienniki w usłudze Azure Monitor, które są używane do zaawansowanej analizy danych monitorowania.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 0b288bf6d987b9db682c8d1439879cf6b499f213
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457335"
---
# <a name="logs-in-azure-monitor"></a>Dzienniki w usłudze Azure Monitor

> [!NOTE]
> Wszystkie dane zebrane przez usługę Azure Monitor są jednym z dwóch podstawowych typów, metryk i dzienników. W tym artykule opisano dzienniki. Aby uzyskać szczegółowy opis metryk i [danych monitorowania zebranych przez usługę Azure Monitor,](data-platform.md) zapoznaj się z [metrykami w usłudze Azure Monitor.](data-platform-metrics.md)

Dzienniki w usłudze Azure Monitor są szczególnie przydatne do wykonywania złożonych analiz danych z różnych źródeł. W tym artykule opisano, jak dzienniki są skonstruowane w usłudze Azure Monitor, co można zrobić z danymi i identyfikuje różne źródła danych, które przechowują dane w dziennikach.

> [!NOTE]
> Należy odróżnić dzienniki usługi Azure Monitor i źródła danych dziennika na platformie Azure. Na przykład zdarzenia na poziomie subskrypcji na platformie Azure są zapisywane w [dzienniku aktywności,](platform-logs-overview.md) który można wyświetlić z menu Usługi Azure Monitor. Większość zasobów będzie zapisywać informacje operacyjne w [dzienniku zasobów,](platform-logs-overview.md) które można przesyłać dalej do różnych lokalizacji. Dzienniki usługi Azure Monitor to platforma danych dziennika, która zbiera dzienniki aktywności i dzienniki zasobów wraz z innymi danymi monitorowania, aby zapewnić dogłębną analizę całego zestawu zasobów.

## <a name="what-are-azure-monitor-logs"></a>Co to są dzienniki monitora platformy Azure?

Dzienniki w usłudze Azure Monitor zawierają różne rodzaje danych zorganizowanych w rekordy z różnymi zestawami właściwości dla każdego typu. Dzienniki mogą zawierać wartości liczbowe, takie jak metryki usługi Azure Monitor, ale zazwyczaj zawierają dane tekstowe ze szczegółowymi opisami. Ponadto różnią się one od danych metrycznych tym, że różnią się strukturą i często nie są zbierane w regularnych odstępach czasu. Dane telemetryczne, takie jak zdarzenia i ślady są przechowywane dzienniki usługi Azure Monitor oprócz danych wydajności, dzięki czemu wszystkie mogą być łączone do analizy.

Typowy typ wpisu dziennika jest zdarzeniem, które jest zbierane sporadycznie. Zdarzenia są tworzone przez aplikację lub usługę i zazwyczaj zawierają wystarczające informacje, aby zapewnić pełny kontekst na własną rękę. Na przykład zdarzenie może wskazywać, że określony zasób został utworzony lub zmodyfikowany, nowy host uruchomiony w odpowiedzi na zwiększony ruch lub wykryto błąd w aplikacji.

 Ponieważ format danych może się różnić, aplikacje mogą tworzyć dzienniki niestandardowe przy użyciu struktury, której potrzebują. Dane metryki mogą być nawet przechowywane w dziennikach, aby połączyć je z innymi danymi monitorowania dla trendów i innych analiz danych.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>Co można zrobić z dziennikami monitora platformy Azure?
W poniższej tabeli wymieniono różne sposoby używania dzienników w usłudze Azure Monitor.


|  |  |
|:---|:---|
| Analiza | Użyj [usługi Log Analytics](../log-query/get-started-portal.md) w witrynie Azure portal do pisania [zapytań dziennika](../log-query/log-query-overview.md) i interakcyjnie analizować dane dziennika przy użyciu zaawansowanego aparatu analizy Eksploratora danych.<br>Użyj [konsoli analizy usługi Application Insights](../app/analytics.md) w witrynie Azure portal, aby pisać zapytania dziennika i interaktywnie analizować dane dziennika z usługi Application Insights. |
| Wizualizacja | Przypinanie wyników kwerend renderowanych jako tabele lub wykresy do [pulpitu nawigacyjnego platformy Azure](../../azure-portal/azure-portal-dashboards.md).<br>Utwórz [skoroszyt](../app/usage-workbooks.md) do łączenia z wieloma zestawami danych w raporcie interaktywnym. <br>Eksportuj wyniki kwerendy do [usługi Power BI,](powerbi.md) aby używać różnych wizualizacji i udostępniać je użytkownikom spoza platformy Azure.<br>Eksportuj wyniki kwerendy do [grafany,](grafana-plugin.md) aby wykorzystać jej pulpit nawigacyjny i połączyć z innymi źródłami danych.|
| Alerty | Skonfiguruj [regułę alertu dziennika,](alerts-log.md) która wysyła powiadomienie lub wykonuje [zautomatyzowane działania,](action-groups.md) gdy wyniki kwerendy odpowiadają określonemu wynikowi.<br>Skonfiguruj [regułę alertu metryki](alerts-metric-logs.md) w niektórych dziennikach danych dziennika wyodrębnionych jako metryki. |
| Pobrać | Wyniki kwerendy dziennika programu Access z wiersza polecenia przy użyciu interfejsu [wiersza polecenia platformy Azure](/cli/azure/ext/log-analytics/monitor/log-analytics).<br>Wyniki kwerendy dziennika programu Access z wiersza polecenia przy użyciu [poleceń cmdlet programu PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights).<br>Wyniki kwerendy dziennika dostępu z niestandardowej aplikacji przy użyciu [interfejsu API REST](https://dev.loganalytics.io/). |
| Eksportowanie | Tworzenie przepływu pracy w celu pobrania danych dziennika i skopiowanie ich do lokalizacji zewnętrznej przy użyciu [aplikacji logiki](~/articles/logic-apps/index.yml). |


## <a name="how-is-data-in-azure-monitor-logs-structured"></a>Jak mają strukturę danych w dziennikach usługi Azure Monitor?
Dane zbierane przez dzienniki monitora platformy Azure są przechowywane w [obszarze roboczym usługi Log Analytics.](../platform/design-logs-deployment.md) Każdy obszar roboczy zawiera wiele tabel, które przechowują dane z określonego źródła. Chociaż wszystkie tabele mają [kilka wspólnych właściwości,](log-standard-properties.md)każdy ma unikatowy zestaw właściwości w zależności od rodzaju danych, które przechowuje. Nowy obszar roboczy będzie miał standardowy zestaw tabel, a więcej tabel zostanie dodanych przez różne rozwiązania monitorowania i inne usługi, które zapisują w obszarze roboczym.

Rejestrowanie danych z usługi Application Insights używa tego samego aparatu usługi Log Analytics jako obszarów roboczych, ale jest przechowywany oddzielnie dla każdej monitorowana aplikacji. Każda aplikacja ma standardowy zestaw tabel do przechowywania danych, takich jak żądania aplikacji, wyjątki i widoki strony.

Kwerendy dziennika będą używać danych z obszaru roboczego usługi Log Analytics lub aplikacji usługi Application Insights. [Kwerendy międzysóbowej](../log-query/cross-workspace-query.md) można używać do analizowania danych aplikacji wraz z innymi danymi dziennika lub do tworzenia zapytań, w tym wielu obszarów roboczych lub aplikacji.

![Obszary robocze](media/data-platform-logs/workspaces.png)

## <a name="log-queries"></a>Rejestrowanie zapytań
Dane w dziennikach usługi Azure Monitor są pobierane przy użyciu [kwerendy dziennika](../log-query/log-query-overview.md) napisanej za pomocą [języka zapytań Kusto,](../log-query/get-started-queries.md)który umożliwia szybkie pobieranie, konsolidowanie i analizowanie zebranych danych. Użyj [usługi Log Analytics](../log-query/portals.md) do pisania i testowania zapytań dziennika w witrynie Azure portal. Umożliwia interaktywną pracę z wynikami lub przypięcie ich do pulpitu nawigacyjnego w celu wyświetlenia ich z innymi wizualizacjami.

![Log Analytics](media/data-platform-logs/log-analytics.png)

Otwórz [usługa Log Analytics z usługi Application Insights,](../app/analytics.md) aby analizować dane usługi Application Insights.

![Analiza wglądu w aplikacje](media/data-platform-logs/app-insights-analytics.png)

Można również pobrać dane dziennika przy użyciu [interfejsu API usługi Log Analytics](https://dev.loganalytics.io/documentation/overview) i interfejsu API REST usługi Application [Insights](https://dev.applicationinsights.io/documentation/overview).


## <a name="sources-of-azure-monitor-logs"></a>Źródła dzienników monitora platformy Azure
Usługa Azure Monitor może zbierać dane dziennika z różnych źródeł zarówno na platformie Azure, jak i z zasobów lokalnych. W poniższych tabelach przedstawiono różne źródła danych dostępne z różnych zasobów, które zapisują dane w dziennikach usługi Azure Monitor. Każdy z nich ma łącze do szczegółów dotyczących dowolnej wymaganej konfiguracji.

### <a name="azure-tenant-and-subscription"></a>Dzierżawa i subskrypcja platformy Azure

| Dane | Opis |
|:---|:---|
| Dzienniki inspekcji usługi Azure Active Directory | Skonfigurowane za pomocą ustawień diagnostycznych dla każdego katalogu. Zobacz [integrowanie dzienników usługi Azure AD z dziennikami usługi Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).  |
| Dzienniki aktywności | Przechowywane oddzielnie domyślnie i mogą być używane do alertów w czasie zbliżonym do rzeczywistego. Zainstaluj rozwiązanie analizy dziennika aktywności, aby zapisać w obszarze roboczym usługi Log Analytics. Zobacz [Zbieranie i analizowanie dzienników aktywności platformy Azure w usłudze Log Analytics](activity-log-collect.md). |

### <a name="azure-resources"></a>Zasoby platformy Azure

| Dane | Opis |
|:---|:---|
| Diagnostyka zasobów | Skonfiguruj ustawienia diagnostyczne, aby zapisywać dane diagnostyczne, w tym metryki w obszarze roboczym usługi Log Analytics. Zobacz [Strumieniowanie dzienników zasobów platformy Azure do usługi Log Analytics](resource-logs-collect-workspace.md). |
| Rozwiązania do monitorowania | Rozwiązania do monitorowania zapisują dane, które zbierają w swoim obszarze roboczym usługi Log Analytics. Zobacz [szczegóły zbierania danych dla rozwiązań do zarządzania na platformie Azure,](../insights/solutions-inventory.md) aby uzyskać listę rozwiązań. Zobacz [Monitorowanie rozwiązań w usłudze Azure Monitor, aby](../insights/solutions.md) uzyskać szczegółowe informacje na temat instalowania i używania rozwiązań. |
| Metryki | Wysyłaj metryki platformy dla zasobów usługi Azure Monitor do obszaru roboczego usługi Log Analytics, aby zachować dane dziennika przez dłuższy czas i przeprowadzić złożoną analizę z innymi typami danych przy użyciu [języka zapytań Kusto](/azure/kusto/query/). Zobacz [Strumieniowanie dzienników zasobów platformy Azure do usługi Log Analytics](resource-logs-collect-storage.md). |
| Azure Table Storage | Zbieraj dane z magazynu platformy Azure, gdzie niektóre zasoby platformy Azure zapisują dane monitorowania. Zobacz [Używanie magazynu obiektów blob platformy Azure dla usług IIS i magazynu tabel platformy Azure dla zdarzeń z usługą Log Analytics.](diagnostics-extension-logs.md) |

### <a name="virtual-machines"></a>Maszyny wirtualne

| Dane | Opis |
|:---|:---|
|  Źródła danych agentów | Źródła danych zbierane od agentów [systemu Windows](agent-windows.md) i [Linux](../learn/quick-collect-linux-computer.md) obejmują zdarzenia, dane dotyczące wydajności i dzienniki niestandardowe. Zobacz [źródła danych agenta w usłudze Azure Monitor, aby](data-sources.md) uzyskać listę źródeł danych i szczegóły dotyczące konfiguracji. |
| Rozwiązania do monitorowania | Rozwiązania do monitorowania zapisują dane, które zbierają od agentów do ich obszaru roboczego usługi Log Analytics. Zobacz [szczegóły zbierania danych dla rozwiązań do zarządzania na platformie Azure,](../insights/solutions-inventory.md) aby uzyskać listę rozwiązań. Zobacz [Monitorowanie rozwiązań w usłudze Azure Monitor, aby](../insights/solutions.md) uzyskać szczegółowe informacje na temat instalowania i używania rozwiązań. |
| System Center Operations Manager | Połącz grupę zarządzania programu Operations Manager z usługą Azure Monitor, aby zbierać dane o zdarzeniach i wydajności od agentów lokalnych w dzienniki. Aby uzyskać szczegółowe informacje na temat tej konfiguracji, zobacz Łączenie programu Operations Manager z [usługą Log Analytics.](om-agents.md) |


### <a name="applications"></a>Aplikacje

| Dane | Opis |
|:---|:---|
| Wnioski i wyjątki | Szczegółowe dane dotyczące żądań aplikacji i wyjątków znajdują się w _żądaniach_, _pageViews_i _tabelach wyjątków._ Wywołania [składników zewnętrznych](../app/asp-net-dependencies.md) znajdują się w tabeli _zależności._ |
| Użytkowanie i wydajność | Wydajność aplikacji jest dostępna w _żądaniach_, _browserTimings_ i _performanceCounters_ tabel. Dane dla [metryk niestandardowych](../app/api-custom-events-metrics.md#trackevent) znajduje się w tabeli _metryki niestandardowe._|
| Śledzenie danych | Wyniki śledzenia [rozproszonego](../app/distributed-tracing.md) są przechowywane w tabeli _śladów._ |
| Testy dostępności | Dane zbiorcze z [testów dostępności](../app/monitor-web-app-availability.md) są przechowywane w _tabeli availabilityResults._ Szczegółowe dane z tych testów są w oddzielnym magazynie i dostępne z usługi Application Insights w witrynie Azure portal. |

### <a name="insights"></a>Insights

| Dane | Opis |
|:---|:---|
| Usługa Azure Monitor dla kontenerów | Dane dotyczące zapasów i wydajności zebrane przez [usługę Azure Monitor dla kontenerów](../insights/container-insights-overview.md). Zobacz [szczegóły zbierania danych kontenera](../insights/container-insights-log-search.md#container-records) dla listy tabel. |
| Usługa Azure Monitor dla maszyn wirtualnych | Mapa i dane dotyczące wydajności zbierane przez [usługę Azure Monitor dla maszyn wirtualnych.](../insights/vminsights-overview.md) Zobacz [Jak kwerendy dzienników z usługi Azure Monitor dla maszyn wirtualnych, aby](../insights/vminsights-log-search.md) uzyskać szczegółowe informacje na temat kwerendy tych danych. |

### <a name="custom"></a>Niestandardowy 

| Dane | Opis |
|:---|:---|
| Interfejs API REST | Zapisuj dane w obszarze roboczym usługi Log Analytics z dowolnego klienta REST. Szczegółowe informacje [można znaleźć w witrynie Wyślij dane dziennika do usługi Azure Monitor za pomocą interfejsu API modułu zbierającego dane HTTP.](data-collector-api.md)
| Aplikacja logiki | Zapisz dowolne dane w obszarze roboczym usługi Log Analytics z przepływu pracy aplikacji logiki za pomocą akcji **Azure Log Analytics Data Collector.** |

### <a name="security"></a>Zabezpieczenia

| Dane | Opis |
|:---|:---|
| Azure Security Center | [Usługa Azure Security Center](/azure/security-center/) przechowuje dane, które zbiera w obszarze roboczym usługi Log Analytics, gdzie mogą być analizowane z innymi danymi dziennika. Zobacz [Zbieranie danych w usłudze Azure Security Center, aby](../../security-center/security-center-enable-data-collection.md) uzyskać szczegółowe informacje na temat konfiguracji obszaru roboczego. |
| Usługa Azure Sentinel | [Usługa Azure Sentinel](/azure/sentinel/) przechowuje dane ze źródeł danych w obszarze roboczym usługi Log Analytics. Zobacz [Łączenie źródeł danych](/azure/sentinel/connect-data-sources).  |


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [platformie danych Usługi Azure Monitor](data-platform.md).
- Dowiedz się więcej o [metrykach w usłudze Azure Monitor](data-platform-metrics.md).
- Dowiedz się więcej o [danych monitorowania dostępnych](data-sources.md) dla różnych zasobów na platformie Azure.
