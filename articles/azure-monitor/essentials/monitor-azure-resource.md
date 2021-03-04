---
title: Monitoruj zasoby platformy Azure za pomocą Azure Monitor | Microsoft Docs
description: Opisuje sposób zbierania i analizowania danych monitorowania z zasobów na platformie Azure przy użyciu Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: af2803a945821a23ff59f196967d792a6593009f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033388"
---
# <a name="monitoring-azure-resources-with-azure-monitor"></a>Monitorowanie zasobów platformy Azure za pomocą usługi Azure Monitor
Jeśli masz krytyczne aplikacje i procesy biznesowe polegające na zasobach platformy Azure, chcesz monitorować te zasoby pod kątem ich dostępności, wydajności i operacji. W tym artykule opisano dane monitorowania wygenerowane przez zasoby platformy Azure oraz sposób używania funkcji Azure Monitor do analizowania i generowania alertów dotyczących tych danych.

> [!IMPORTANT]
> Ten artykuł dotyczy wszystkich usług platformy Azure korzystających z Azure Monitor. Zasoby obliczeniowe, w tym maszyny wirtualne i App Service, generują te same dane monitorowania opisane tutaj, ale mają również system operacyjny gościa, który może również generować dzienniki i metryki. Zapoznaj się z dokumentacją dotyczącą monitorowania tych usług, aby uzyskać szczegółowe informacje na temat zbierania i analizowania tych danych.

## <a name="what-is-azure-monitor"></a>Co to jest Azure Monitor?
Azure Monitor to pełna usługa monitorowania stosu na platformie Azure, która oferuje pełny zestaw funkcji do monitorowania zasobów platformy Azure, a także zasobów w innych chmurach i lokalnych. [Platforma danych Azure monitor](../data-platform.md) zbiera dane do [dzienników](../logs/data-platform-logs.md) i [metryk](../essentials/data-platform-metrics.md) , gdzie można je analizować przy użyciu pełnego zestawu narzędzi do monitorowania. Zapoznaj się z pełną listą aplikacji i usług, które mogą być monitorowane przez Azure Monitor, co [jest monitorowane przez Azure monitor?](../monitor-reference.md).

Gdy tylko utworzysz zasób platformy Azure, Azure Monitor jest włączona i rozpocznie zbieranie metryk i dzienników aktywności, które można [wyświetlać i analizować w Azure Portal](#monitoring-in-the-azure-portal). W przypadku niektórych konfiguracji można zebrać dodatkowe dane monitorowania i włączyć dodatkowe funkcje. Aby uzyskać szczegółowe informacje o wymaganiach dotyczących konfiguracji, zobacz poniższe [dane monitorowania](#monitoring-data) .


## <a name="costs-associated-with-monitoring"></a>Koszty związane z monitorowaniem
Nie ma kosztu na analizowanie danych monitorowania, które są zbierane domyślnie. Uwzględnione są następujące elementy:

- Zbieranie metryk platformy i analizowanie ich przy użyciu Eksploratora metryk.
- Zbieranie dziennika aktywności i analizowanie go w Azure Portal.
- Tworzenie reguły alertu dziennika aktywności.

Nie ma Azure Monitor kosztów zbierania i eksportowania dzienników i metryk, ale mogą istnieć powiązane koszty związane z miejscem docelowym:

- Koszty związane z pozyskiwaniem i przechowywaniem danych podczas zbierania dzienników i metryk w obszarze roboczym Log Analytics. Zobacz [Azure monitor cennika log Analytics](https://azure.microsoft.com/pricing/details/monitor/).
- Koszty związane z magazynem danych podczas zbierania dzienników i metryk na konto usługi Azure Storage. Zobacz [Cennik usługi Azure Storage dla magazynu obiektów BLOB](https://azure.microsoft.com/pricing/details/storage/blobs/).
- Koszty związane z przesyłaniem strumieniowym centrum zdarzeń podczas przekazywania dzienników i metryk do usługi Azure Event Hubs. Zobacz [Cennik usługi Azure Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

Możliwe jest Azure Monitor kosztów skojarzonych z następującymi usługami. Zobacz [cennik Azure monitor](https://azure.microsoft.com/pricing/details/monitor/):

- Uruchamianie zapytania dziennika.
- Tworzenie reguły alertu dotyczącego metryki lub zapytania dziennika.
- Wysyłanie powiadomienia z dowolnej reguły alertu.
- Uzyskiwanie dostępu do metryk przy użyciu interfejsu API.

## <a name="monitoring-data"></a>Dane monitorowania
Zasoby na platformie Azure generują [dzienniki](../logs/data-platform-logs.md) i [metryki](../essentials/data-platform-metrics.md) pokazane na poniższym diagramie. Zapoznaj się z dokumentacją dla każdej usługi platformy Azure dla określonych danych, które generują, oraz wszelkich dodatkowych rozwiązań lub szczegółowych informacji, które zapewnia.

![Omówienie](media/monitor-azure-resource/logs-metrics.png)



- [Metryki platformy](../essentials/data-platform-metrics.md) — wartości liczbowe, które są automatycznie zbierane w regularnych odstępach czasu i opisują niektóre aspekty zasobu w określonym czasie. 
- [Dzienniki zasobów](./platform-logs-overview.md) — umożliwiają uzyskanie wglądu w operacje wykonywane w ramach zasobu platformy Azure (płaszczyzny danych), na przykład w celu uzyskania wpisu tajnego z Key Vault lub żądania do bazy danych. Zawartość i struktura dzienników zasobów różnią się w zależności od usługi i typu zasobów platformy Azure.
- [Dziennik aktywności](./platform-logs-overview.md) — zawiera szczegółowe informacje o operacjach dotyczących poszczególnych zasobów platformy Azure w ramach subskrypcji z zewnątrz (płaszczyzny zarządzania), na przykład tworzenia nowego zasobu lub uruchamiania maszyny wirtualnej. Są to informacje o tym, kto i kiedy w przypadku operacji zapisu (PUT, POST, DELETE) wykonanych na zasobach w ramach subskrypcji.


## <a name="configuration-requirements"></a>Wymagania dotyczące konfiguracji

### <a name="configure-monitoring"></a>Konfigurowanie monitorowania
Niektóre dane monitorowania są zbierane automatycznie, ale może być konieczne wykonanie pewnej konfiguracji w zależności od wymagań. Zobacz poniższe informacje, aby uzyskać szczegółowe informacje dotyczące poszczególnych typów danych monitorowania.

- [Metryki platformy](../essentials/data-platform-metrics.md) — metryki platformy są zbierane automatycznie do [metryk Azure monitor](../essentials/data-platform-metrics.md) , bez konieczności konfigurowania. Utwórz ustawienie diagnostyczne, aby wysyłać wpisy do dzienników Azure Monitor lub do ich przesyłania poza platformę Azure.
- [Dzienniki zasobów](./platform-logs-overview.md) — dzienniki zasobów są automatycznie generowane przez zasoby platformy Azure, ale nie zbierane bez ustawień diagnostycznych.  Utwórz ustawienie diagnostyczne, aby wysyłać wpisy do dzienników Azure Monitor lub do ich przesyłania poza platformę Azure.
- [Dziennik aktywności](./platform-logs-overview.md) — dziennik aktywności jest zbierany automatycznie bez wymaganej konfiguracji i może być wyświetlany w Azure Portal. Utwórz ustawienie diagnostyczne, aby skopiować je do dzienników Azure Monitor lub do ich przesyłania poza platformę Azure.

### <a name="log-analytics-workspace"></a>Obszar roboczy usługi Log Analytics
Zbieranie danych do dzienników Azure Monitor wymaga Log Analytics obszaru roboczego. Możesz szybko rozpocząć monitorowanie usługi, tworząc nowy obszar roboczy, ale może być dostępna wartość przy użyciu obszaru roboczego, który zbiera dane z innych usług. Zobacz [Tworzenie obszaru roboczego log Analytics w Azure Portal,](../logs/quick-create-workspace.md) Aby uzyskać szczegółowe informacje na temat tworzenia obszaru roboczego i [projektowania wdrożenia dzienników Azure monitor](../logs/design-logs-deployment.md) , aby pomóc w ustaleniu najlepszego projektu obszaru roboczego dla wymagań. W przypadku korzystania z istniejącego obszaru roboczego w organizacji wymagane są odpowiednie uprawnienia zgodnie z opisem w temacie [Zarządzanie dostępem do danych dziennika i obszarów roboczych w Azure monitor](../logs/manage-access.md). 





## <a name="diagnostic-settings"></a>Ustawienia diagnostyczne
Ustawienia diagnostyczne definiują, gdzie należy wysyłać dzienniki zasobów i metryki dla określonego zasobu. Możliwe miejsca docelowe to:

- [Log Analytics obszar roboczy](./resource-logs.md#send-to-log-analytics-workspace) , dzięki któremu można analizować dane przy użyciu innych danych monitorowania zbieranych przez Azure monitor przy użyciu zaawansowanych zapytań dzienników, a także korzystać z innych funkcji Azure monitor, takich jak alerty dzienników i wizualizacje. 
- [Centra zdarzeń](./resource-logs.md#send-to-azure-event-hubs) do przesyłania strumieniowego danych do systemów zewnętrznych, takich jak rozwiązań Siem innych firm, oraz inne rozwiązania do analizy dzienników. 
- [Konto usługi Azure Storage](./resource-logs.md#send-to-azure-storage) , które jest przydatne do inspekcji, analizy statycznej lub tworzenia kopii zapasowych.

Postępuj zgodnie z procedurą w temacie [Tworzenie ustawień diagnostycznych, aby zbierać dzienniki platformy i metryki na platformie Azure w](../essentials/diagnostic-settings.md) celu tworzenia ustawień diagnostycznych i zarządzania nimi za pomocą Azure Portal. Zobacz [Tworzenie ustawień diagnostycznych na platformie Azure przy użyciu szablonu Menedżer zasobów](./resource-manager-diagnostic-settings.md) , aby zdefiniować je w szablonie i włączyć pełne monitorowanie dla zasobu po jego utworzeniu.


## <a name="monitoring-in-the-azure-portal"></a>Monitorowanie w Azure Portal
 Możesz uzyskać dostęp do danych monitorowania dla większości zasobów platformy Azure z menu zasobów w Azure Portal. Umożliwi to dostęp do danych pojedynczego zasobu przy użyciu standardowych narzędzi Azure Monitor. Niektóre usługi platformy Azure udostępniają różne opcje, dlatego należy odwołać się do dokumentacji tej usługi w celu uzyskania dodatkowych informacji. Użyj menu **Azure monitor** , aby przeanalizować dane ze wszystkich monitorowanych zasobów. 

### <a name="overview"></a>Omówienie
Wiele usług będzie obejmować dane monitorowania na stronie **przeglądowej** w celu szybkiego wglądu w ich działania. Zwykle jest to oparte na podzestawie metryk platformy przechowywanych w metrykach Azure Monitor. Inne opcje monitorowania są zwykle dostępne w sekcji **monitorowanie** w menu usługi.

![Strona omówienia](media/monitor-azure-resource/overview-page.png)


### <a name="insights-and-solutions"></a>Szczegółowe informacje i rozwiązania 
Niektóre usługi udostępniają narzędzia wykraczające poza standardowe funkcje programu Azure Monitor. [Szczegółowe informacje](../monitor-reference.md) zapewniają dostosowane środowisko monitorowania, oparte na platformie danych Azure monitor i standardowych funkcjach. [Rozwiązania](../insights/solutions.md) zapewniają wstępnie zdefiniowaną logikę monitorowania utworzoną na Azure monitor dziennikach. 

Jeśli usługa ma Azure Monitor wgląd, możesz uzyskać do niej dostęp z poziomu **monitorowania** w menu każdego zasobu. Uzyskaj dostęp do wszystkich szczegółowych informacji i rozwiązań z menu **Azure monitor** .

![Szczegółowe informacje w Azure Portal](media/monitor-azure-resource/insights.png)

### <a name="metrics"></a>Metryki
Analizuj metryki w Azure Portal przy użyciu [Eksploratora metryk](./metrics-getting-started.md) , który jest dostępny z poziomu elementu menu **metryk** dla większości usług. To narzędzie umożliwia korzystanie z poszczególnych metryk lub łączenie wielu w celu identyfikowania korelacji i trendów. 

- Zapoznaj się z tematem Rozpoczynanie [pracy z usługą Azure Eksplorator metryk](./metrics-getting-started.md) , aby poznać podstawy korzystania z Eksploratora metryk.
- Zobacz [Zaawansowane funkcje usługi Azure Eksplorator metryk](../essentials/metrics-charts.md) , aby uzyskać zaawansowane funkcje Eksploratora metryk, takie jak używanie wielu metryk i stosowanie filtrów i dzielenia.

![Eksplorator metryk w Azure Portal](media/monitor-azure-resource/metrics.png)


### <a name="activity-log"></a>Dziennik aktywności 
Wyświetl wpisy w dzienniku aktywności w Azure Portal z filtrem początkowym ustawionym na bieżący zasób. Skopiuj dziennik aktywności do obszaru roboczego Log Analytics, aby uzyskać do niego dostęp, aby używać go w zapytaniach i skoroszytach dzienników. 

- Zobacz [Wyświetlanie i pobieranie zdarzeń dziennika aktywności platformy Azure](../essentials/activity-log.md#view-the-activity-log) , aby uzyskać szczegółowe informacje na temat wyświetlania dziennika aktywności i pobierania wpisów przy użyciu różnych metod.
- Zapoznaj się z dokumentacją usługi platformy Azure, aby poznać określone zdarzenia, które są rejestrowane.

![Dziennik aktywności](media/monitor-azure-resource/activity-log.png)

### <a name="azure-monitor-logs"></a>Dzienniki usługi Azure Monitor
Dzienniki Azure Monitor konsolidują dzienniki i metryki z wielu usług i innych źródeł danych do analizy za pomocą zaawansowanego narzędzia do wykonywania zapytań. Zgodnie z powyższym opisem należy utworzyć ustawienie diagnostyczne w celu zbierania metryk platformy, dziennika aktywności i dzienników zasobów w obszarze roboczym Log Analytics w Azure Monitor.

[Log Analytics](../logs/log-analytics-tutorial.md) umożliwia korzystanie z [zapytań dzienników](../logs/log-query-overview.md), które są zaawansowaną funkcją Azure monitor, która pozwala na przeprowadzanie zaawansowanej analizy danych dzienników przy użyciu w pełni wyróżnionego języka zapytań. Otwórz Log Analytics z **dzienników** w menu **monitorowanie** dla zasobu platformy Azure, aby współpracować z kwerendami dzienników przy użyciu zasobu jako [zakresu zapytania](../logs/scope.md#query-scope). Dzięki temu można analizować dane w wielu tabelach tylko dla tego zasobu. Użyj **dzienników** z menu Azure monitor, aby uzyskać dostęp do dzienników dla wszystkich zasobów. 

- Zobacz Rozpoczynanie [pracy z dziennikami w Azure monitor](../logs/get-started-queries.md) , aby zapoznać się z samouczkiem dotyczącym korzystania z języka zapytań używanego do zapisywania zapytań dzienników.
- Zobacz [zbieranie dzienników zasobów platformy Azure w obszarze roboczym log Analytics w Azure monitor](./resource-logs.md#send-to-log-analytics-workspace) , aby uzyskać informacje na temat sposobu zbierania dzienników zasobów w dziennikach Azure monitor i szczegółowe informacje dotyczące sposobu uzyskiwania dostępu do nich w zapytaniu.
- Zobacz [tryb kolekcji](./resource-logs.md#send-to-log-analytics-workspace) , aby dowiedzieć się, jak dane dzienników zasobów są uporządkowane w dziennikach Azure monitor.
- Zapoznaj się z dokumentacją dla każdej usługi platformy Azure, aby uzyskać szczegółowe informacje na temat tabeli w Azure Monitor dziennikach.

![Log Analytics w Azure Portal](media/monitor-azure-resource/logs.png)

## <a name="monitoring-from-command-line"></a>Monitorowanie z wiersza polecenia
Możesz uzyskać dostęp do danych monitorowania zebranych z zasobu z wiersza polecenia lub dołączać je do skryptu za pomocą [Azure PowerShell](/powershell/azure/) lub [interfejsu wiersza polecenia platformy Azure](/cli/azure/). 

- Informacje o [metrykach interfejsu wiersza polecenia](/cli/azure/monitor/metrics) dotyczące uzyskiwania dostępu do danych metryk z interfejsu wiersza polecenia.
- Zobacz [informacje log Analytics interfejsu wiersza polecenia](/cli/azure/ext/log-analytics/monitor/log-analytics) , aby uzyskać dostęp do Azure monitor dzienników danych przy użyciu zapytania dziennika z interfejsu wiersza polecenia.
- Zobacz [informacje dotyczące metryk Azure PowerShell](/powershell/module/azurerm.insights/get-azurermmetric) , aby uzyskać dostęp do danych metryk z Azure PowerShell.
- Aby uzyskać dostęp do Azure Monitor dzienników danych przy użyciu zapytania dziennika z Azure PowerShell, zobacz [Informacje o zapytaniach w dzienniku Azure PowerShell](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery) .

## <a name="monitoring-from-rest-api"></a>Monitorowanie z interfejsu API REST
Uwzględnij dane monitorowania zebrane z zasobu w niestandardowej aplikacji przy użyciu interfejsu API REST.

- Zobacz [Przewodnik po interfejsie API REST usługi Azure Monitoring](./rest-api-walkthrough.md) , aby uzyskać szczegółowe informacje na temat uzyskiwania dostępu do metryk z interfejsu api REST Azure monitor.
- Zobacz [interfejs API REST usługi Azure log Analytics](https://dev.loganalytics.io/) , aby uzyskać informacje na temat uzyskiwania dostępu do Azure monitor dzienników przy użyciu zapytania dziennika z Azure PowerShell.

## <a name="alerts"></a>Alerty
[Alerty](../alerts/alerts-overview.md) z wyprzedzeniem powiadamiają użytkownika i mogą podejmować działania w przypadku znalezienia ważnych warunków w danych monitorowania. Tworzysz regułę alertu, która definiuje cel dla alertu, warunki dotyczące tego, czy ma zostać utworzony alert, oraz wszelkie akcje, które należy podjąć w odpowiedzi.

Różne rodzaje danych monitorowania są używane dla różnych rodzajów reguł alertów.

- [Alert dziennika aktywności](../alerts/alerts-activity-log.md) — umożliwia utworzenie alertu, gdy w dzienniku aktywności zostanie utworzony wpis zgodny z określonymi kryteriami. Pozwala to na wyświetlenie powiadomienia na przykład w przypadku utworzenia określonego typu zasobu lub zmiany konfiguracji.
- [Alert dotyczący metryki](../alerts/alerts-metric.md) — tworzenie alertu, gdy wartość metryki przekroczy określony próg. Alerty metryk są wydajniejsze niż inne alerty i mogą być automatycznie rozwiązywane po rozwiązaniu problemu.
- [Alert zapytania dziennika](../alerts/alerts-log.md) — uruchamianie zapytania dziennika w regularnych odstępach czasu i tworzenie alertu w przypadku znalezienia określonego warunku. Pozwala to na wykonywanie złożonej analizy w wielu zestawach danych i.

Użyj **alertów** z menu zasobów, aby wyświetlić alerty i zarządzać regułami alertów dla tego zasobu. Tylko alerty dziennika aktywności i alerty metryk używają poszczególnych zasobów platformy Azure jako obiektów docelowych. Alerty zapytań dzienników używają obszaru roboczego Log Analytics jako elementu docelowego i są oparte na zapytaniu, które może uzyskać dostęp do wszystkich dzienników przechowywanych w tym obszarze roboczym. Użyj menu Azure Monitor, aby wyświetlić alerty dla wszystkich zasobów i zarządzać nimi, a następnie Zarządzaj alertami dotyczącymi alertów dotyczących zapytań dzienników.

- Zapoznaj się z artykułami dotyczącymi różnych rodzajów alertów powyżej, aby uzyskać szczegółowe informacje na temat tworzenia reguł alertów.
- Zobacz [Tworzenie grup akcji i zarządzanie nimi w Azure Portal,](../alerts/action-groups.md) Aby uzyskać szczegółowe informacje na temat tworzenia grupy akcji, która umożliwia zarządzanie odpowiedziami na alerty.



## <a name="next-steps"></a>Następne kroki

* Szczegóły dzienników zasobów dla różnych usług platformy Azure można znaleźć w temacie [obsługiwane usługi, schematy i kategorie dla dzienników zasobów platformy Azure](./resource-logs-schema.md) .