---
title: Monitorowanie zasobów platformy Azure za pomocą Azure Monitor | Microsoft Docs
description: Opisuje sposób zbierania i analizowania danych monitorowania z zasobów na platformie Azure przy użyciu Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: cb778d826ef094d71fd27f3c10bc1f2c292baa47
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862403"
---
# <a name="monitoring-azure-resources-with-azure-monitor"></a>Monitorowanie zasobów platformy Azure za pomocą usługi Azure Monitor
Jeśli masz krytyczne aplikacje i procesy biznesowe korzystające z zasobów platformy Azure, chcesz monitorować ich dostępność, wydajność i działanie. W tym artykule opisano dane monitorowania generowane przez zasoby platformy Azure oraz sposób używania funkcji usługi Azure Monitor do analizowania tych danych i generowania na nich alertów.

> [!IMPORTANT]
> Ten artykuł dotyczy wszystkich usług na platformie Azure, które używają Azure Monitor. Zasoby obliczeniowe, w tym maszyny wirtualne i App Service, generują te same dane monitorowania, które opisano tutaj, ale mają również system operacyjny gościa, który może również generować dzienniki i metryki. Zobacz dokumentację monitorowania tych usług, aby uzyskać szczegółowe informacje na temat zbierania i analizowania tych danych.

## <a name="what-is-azure-monitor"></a>Co to jest Azure Monitor?
Azure Monitor to usługa monitorowania pełnego stosu na platformie Azure, która zapewnia kompletny zestaw funkcji do monitorowania zasobów platformy Azure, a także zasobów w innych chmurach i lokalnych. Platforma [Azure Monitor zbiera](../data-platform.md) dane do dzienników [](../essentials/data-platform-metrics.md) i metryk, gdzie można je analizować razem przy użyciu pełnego zestawu narzędzi do monitorowania. [](../logs/data-platform-logs.md) Zobacz pełną listę aplikacji i usług, które mogą być monitorowane przez usługę Azure Monitor w tece [Co jest monitorowane przez Azure Monitor?](../monitor-reference.md).

Po utworzeniu zasobu platformy Azure usługa Azure Monitor i rozpoczyna zbieranie metryk i dzienników aktywności, które można wyświetlać i [analizować](#monitoring-in-the-azure-portal)w Azure Portal . W przypadku niektórych konfiguracji można zbierać dodatkowe dane monitorowania i włączać dodatkowe funkcje. Aby [uzyskać szczegółowe informacje](#monitoring-data) na temat wszelkich wymagań dotyczących konfiguracji, zobacz Monitoring Data below (Dane monitorowania poniżej).


## <a name="costs-associated-with-monitoring"></a>Koszty związane z monitorowaniem
Analizowanie danych monitorowania, które są zbierane domyślnie, nie jest kosztem. Uwzględnione są następujące elementy:

- Zbieranie metryk platformy i analizowanie ich za pomocą eksploratora metryk.
- Zbieranie dziennika aktywności i analizowanie go w Azure Portal.
- Tworzenie reguły alertu dziennika aktywności.

Nie ma żadnych Azure Monitor dotyczących zbierania i eksportowania dzienników i metryk, ale z miejscem docelowym mogą być powiązane koszty:

- Koszty związane z pozyskiwaniem i przechowywaniem danych podczas zbierania dzienników i metryk w obszarze roboczym usługi Log Analytics. Zobacz [Azure Monitor cennik usługi Log Analytics.](https://azure.microsoft.com/pricing/details/monitor/)
- Koszty związane z magazynem danych podczas zbierania dzienników i metryk na koncie usługi Azure Storage. Zobacz [Cennik usługi Azure Storage dla magazynu obiektów blob.](https://azure.microsoft.com/pricing/details/storage/blobs/)
- Koszty związane z przesyłaniem strumieniowym centrum zdarzeń podczas przekazywania dzienników i metryk do Azure Event Hubs. Zobacz [Azure Event Hubs cennika.](https://azure.microsoft.com/pricing/details/event-hubs/)

Mogą wystąpić Azure Monitor powiązane z następującymi. Zobacz [Azure Monitor cennika:](https://azure.microsoft.com/pricing/details/monitor/)

- Uruchamianie zapytania dziennika.
- Tworzenie reguły alertu zapytania metryki lub dziennika.
- Wysyłanie powiadomienia z dowolnej reguły alertu.
- Uzyskiwanie dostępu do metryk za pośrednictwem interfejsu API.

## <a name="monitoring-data"></a>Dane monitorowania
Zasoby na platformie Azure [generują dzienniki](../logs/data-platform-logs.md) [i metryki pokazane](../essentials/data-platform-metrics.md) na poniższym diagramie. Zapoznaj się z dokumentacją dotyczącą poszczególnych usług platformy Azure, aby uzyskać konkretne generowane przez nie dane oraz wszelkie dodatkowe rozwiązania lub szczegółowe informacje, które zapewniają.

![Omówienie](media/monitor-azure-resource/logs-metrics.png)



- [Metryki platformy —](../essentials/data-platform-metrics.md) wartości liczbowe, które są automatycznie zbierane w regularnych odstępach czasu i opisują jakiś aspekt zasobu w określonym czasie. 
- [Dzienniki zasobów](./platform-logs-overview.md) — zapewniają wgląd w operacje, które zostały wykonane w obrębie zasobu platformy Azure (płaszczyzny danych), na przykład pobieranie tajnego Key Vault lub wykonywanie żądania do bazy danych. Zawartość i struktura dzienników zasobów zależy od usługi platformy Azure i typu zasobu.
- [Dziennik aktywności](./platform-logs-overview.md) — zapewnia wgląd w operacje na poszczególnych zasobach platformy Azure w subskrypcji z zewnątrz (płaszczyzna zarządzania), na przykład tworzenie nowego zasobu lub uruchamianie maszyny wirtualnej. Są to informacje o tym, co, kto i kiedy w przypadku jakichkolwiek operacji zapisu (PUT, POST, DELETE) wykonywanych na zasobach w ramach subskrypcji.


## <a name="configuration-requirements"></a>Wymagania dotyczące konfiguracji

### <a name="configure-monitoring"></a>Konfigurowanie monitorowania
Niektóre dane monitorowania są zbierane automatycznie, ale może być konieczne przeprowadzenie konfiguracji w zależności od wymagań. Zapoznaj się z poniższymi informacjami, aby uzyskać szczegółowe informacje dotyczące poszczególnych typów danych monitorowania.

- [Metryki platformy —](../essentials/data-platform-metrics.md) metryki platformy są zbierane automatycznie do metryk [Azure Monitor bez](../essentials/data-platform-metrics.md) wymaganej konfiguracji. Utwórz ustawienie diagnostyczne, aby wysyłać wpisy do dzienników Azure Monitor lub przesyłać je dalej poza platformę Azure.
- [Dzienniki zasobów](./platform-logs-overview.md) — dzienniki zasobów są automatycznie generowane przez zasoby platformy Azure, ale nie są zbierane bez ustawienia diagnostycznego.  Utwórz ustawienie diagnostyczne, aby wysyłać wpisy do dzienników Azure Monitor lub przesyłać je dalej poza platformę Azure.
- [Dziennik aktywności](./platform-logs-overview.md) — dziennik aktywności jest zbierany automatycznie bez wymaganej konfiguracji i można go wyświetlić w Azure Portal. Utwórz ustawienie diagnostyczne, aby skopiować je do dzienników Azure Monitor lub do przesyłania dalej poza platformę Azure.

### <a name="log-analytics-workspace"></a>Obszar roboczy usługi Log Analytics
Zbieranie danych do dzienników Azure Monitor wymaga obszaru roboczego usługi Log Analytics. Możesz szybko rozpocząć monitorowanie usługi, tworząc nowy obszar roboczy, ale korzystanie z obszaru roboczego, który zbiera dane z innych usług, może mieć wartość. Zobacz [Tworzenie obszaru roboczego usługi Log Analytics](../logs/quick-create-workspace.md) w Azure Portal, aby uzyskać szczegółowe informacje na temat tworzenia obszaru roboczego i projektowania wdrożenia dzienników usługi [Azure Monitor,](../logs/design-logs-deployment.md) aby ułatwić określenie najlepszego projektu obszaru roboczego zgodnie z wymaganiami. Jeśli używasz istniejącego obszaru roboczego w organizacji, wymagane będą odpowiednie uprawnienia, zgodnie z opisem w tece Zarządzanie dostępem do danych dziennika i obszarów roboczych w Azure Monitor [.](../logs/manage-access.md) 





## <a name="diagnostic-settings"></a>Ustawienia diagnostyczne
Ustawienia diagnostyczne określają, dokąd mają być wysyłane dzienniki zasobów i metryki dla określonego zasobu. Możliwe miejsca docelowe to:

- Obszar roboczy [usługi Log Analytics,](./resource-logs.md#send-to-log-analytics-workspace) który umożliwia analizowanie danych za pomocą innych danych monitorowania zbieranych przez usługę Azure Monitor przy użyciu zaawansowanych zapytań dotyczących dzienników, a także korzystanie z innych funkcji Azure Monitor, takich jak alerty dzienników i wizualizacje. 
- [Usługa Event Hubs](./resource-logs.md#send-to-azure-event-hubs) do strumieniowego przesyłania danych do systemów zewnętrznych, takich jak rozwiązania SIEM innych firm i inne rozwiązania analizy dzienników. 
- [Konto usługi Azure Storage,](./resource-logs.md#send-to-azure-storage) które jest przydatne do inspekcji, analizy statycznej lub tworzenia kopii zapasowych.

Postępuj zgodnie z procedurą w tece Tworzenie ustawienia [diagnostycznego,](../essentials/diagnostic-settings.md) aby zbierać dzienniki platformy i metryki na platformie Azure, aby tworzyć ustawienia diagnostyczne i zarządzać nimi za pośrednictwem Azure Portal. Zobacz Tworzenie ustawienia diagnostycznego na platformie Azure przy użyciu [szablonu Resource Manager szablonu,](./resource-manager-diagnostic-settings.md) aby zdefiniować je w szablonie i włączyć pełne monitorowanie zasobu po jego utworzeniu.


## <a name="monitoring-in-the-azure-portal"></a>Monitorowanie w Azure Portal
 Dostęp do danych monitorowania dla większości zasobów platformy Azure można uzyskać z menu zasobu w Azure Portal. Zapewni to dostęp do danych pojedynczego zasobu przy użyciu standardowych narzędzi Azure Monitor zasobów. Niektóre usługi platformy Azure będą zapewniać różne opcje, dlatego należy odwołać się do dokumentacji tej usługi, aby uzyskać dodatkowe informacje. Użyj menu **Azure Monitor,** aby analizować dane ze wszystkich monitorowanych zasobów. 

### <a name="overview"></a>Omówienie
Wiele usług będzie zawierać dane monitorowania na **stronie** Przegląd, aby szybko uzyskać dostęp do ich działania. Zwykle jest to oparte na podzestawie metryk platformy przechowywanych w Azure Monitor Metryki. Inne opcje monitorowania są zwykle dostępne w sekcji **Monitorowanie** w menu usługi.

![Strona omówienia](media/monitor-azure-resource/overview-page.png)


### <a name="insights-and-solutions"></a>Szczegółowe informacje i rozwiązania 
Niektóre usługi będą zapewniać narzędzia wykraczające poza standardowe funkcje Azure Monitor. [Szczegółowe informacje](../monitor-reference.md) zapewniają dostosowane środowisko monitorowania, które jest Azure Monitor platformie danych i standardowych funkcjach. [Rozwiązania](../insights/solutions.md) zapewniają wstępnie zdefiniowaną logikę monitorowania zbudowaną na Azure Monitor dzienników. 

Jeśli usługa ma szczegółowe Azure Monitor, możesz uzyskać do niego dostęp z pozycji **Monitorowanie** w menu każdego zasobu. Uzyskaj dostęp do wszystkich szczegółowych informacji i rozwiązań z **Azure Monitor** menu.

![Szczegółowe informacje w Azure Portal](media/monitor-azure-resource/insights.png)

### <a name="metrics"></a>Metryki
Analizuj metryki w Azure Portal [eksploratora](./metrics-getting-started.md) metryk, który jest dostępny w pozycji menu **Metryki** dla większości usług. To narzędzie umożliwia pracę z poszczególnymi metrykami lub łączenie wielu w celu identyfikowania korelacji i trendów. 

- Aby uzyskać podstawowe informacje na temat korzystania z eksploratora metryk, zobacz Wprowadzenie do usługi [Azure Eksplorator metryk.](./metrics-getting-started.md)
- Zobacz [Zaawansowane funkcje usługi Azure Eksplorator metryk,](../essentials/metrics-charts.md) aby uzyskać zaawansowane funkcje eksploratora metryk, takie jak używanie wielu metryk oraz stosowanie filtrów i dzielenie.

![Eksplorator metryk w Azure Portal](media/monitor-azure-resource/metrics.png)


### <a name="activity-log"></a>Dziennik aktywności 
Wyświetl wpisy w dzienniku aktywności w Azure Portal z filtrem początkowym ustawionym na bieżący zasób. Skopiuj dziennik aktywności do obszaru roboczego usługi Log Analytics, aby uzyskać do niego dostęp, aby używać go w zapytaniach dziennika i skoroszytach. 

- Zobacz [Wyświetlanie i pobieranie zdarzeń dziennika aktywności platformy Azure,](../essentials/activity-log.md#view-the-activity-log) aby uzyskać szczegółowe informacje na temat wyświetlania dziennika aktywności i pobierania wpisów przy użyciu różnych metod.
- Zapoznaj się z dokumentacją usługi platformy Azure, aby uzyskać informacje o konkretnych rejestrowanych zdarzeniach.

![Dziennik aktywności](media/monitor-azure-resource/activity-log.png)

### <a name="azure-monitor-logs"></a>Dzienniki usługi Azure Monitor
Azure Monitor dzienniki konsolidują dzienniki i metryki z wielu usług i innych źródeł danych do analizy za pomocą zaawansowanego narzędzia do wykonywania zapytań. Jak opisano powyżej, utwórz ustawienie diagnostyczne w celu zbierania metryk platformy, dziennika aktywności i dzienników zasobów w obszarze roboczym usługi Log Analytics w Azure Monitor.

[Usługa Log Analytics](../logs/log-analytics-tutorial.md) umożliwia pracę z zapytaniami dzienników [,](../logs/log-query-overview.md)co jest zaawansowaną funkcją usługi Azure Monitor umożliwiającą zaawansowaną analizę danych dziennika przy użyciu w pełni funkcjonalnego języka zapytań. Otwórz usługę Log Analytics **z** dzienników **w** menu Monitorowanie dla zasobu platformy Azure, aby pracować z zapytaniami dzienników korzystającymi z zasobu jako zakresu [zapytań.](../logs/scope.md#query-scope) Umożliwia to analizowanie danych w wielu tabelach tylko dla tego zasobu. Użyj **pozycji** Dzienniki z menu Azure Monitor, aby uzyskać dostęp do dzienników dla wszystkich zasobów. 

- Zobacz Get started with log queries in Azure Monitor (Rozpoczynanie pracy z zapytaniami dziennika w programie [Azure Monitor),](../logs/get-started-queries.md) aby uzyskać samouczek dotyczący używania języka zapytań używanego do pisania zapytań dziennika.
- Zobacz [Zbieranie dzienników zasobów platformy Azure](./resource-logs.md#send-to-log-analytics-workspace) w obszarze roboczym usługi Log Analytics w usłudze Azure Monitor, aby uzyskać informacje na temat sposobu zbierania dzienników zasobów w dziennikach usługi Azure Monitor oraz szczegółowe informacje na temat sposobu uzyskiwania do nich dostępu w zapytaniu.
- Zobacz [Tryb zbierania,](./resource-logs.md#send-to-log-analytics-workspace) aby uzyskać wyjaśnienie struktury danych dziennika zasobów w dziennikach Azure Monitor danych.
- Zapoznaj się z dokumentacją każdej usługi platformy Azure, aby uzyskać szczegółowe informacje na temat jej tabeli w Azure Monitor Dzienników.

![Usługa Log Analytics w Azure Portal](media/monitor-azure-resource/logs.png)

## <a name="monitoring-from-command-line"></a>Monitorowanie z wiersza polecenia
Dostęp do danych monitorowania zebranych z zasobu można uzyskać z wiersza polecenia lub dołączyć do skryptu przy [użyciu](/powershell/azure/) Azure PowerShell lub interfejsu wiersza polecenia [platformy Azure.](/cli/azure/) 

- Zobacz informacje [na temat metryk interfejsu wiersza polecenia,](/cli/azure/monitor/metrics) aby uzyskać dostęp do danych metryk z interfejsu wiersza polecenia.
- Zobacz cli [Log Analytics reference](/cli/azure/monitor/log-analytics) for accessing Azure Monitor Logs data using a log query from CLI (Informacje dotyczące usługi Log Analytics interfejsu wiersza polecenia w celu uzyskiwania Azure Monitor danych dzienników przy użyciu zapytania dziennika z interfejsu wiersza polecenia.
- Zobacz [Azure PowerShell metryk, aby](/powershell/module/azurerm.insights/get-azurermmetric) uzyskać dostęp do danych metryk z Azure PowerShell.
- Zobacz [Azure PowerShell zapytania dziennika,](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery) aby uzyskać dostęp do Azure Monitor danych dzienników przy użyciu zapytania dziennika z Azure PowerShell.

## <a name="monitoring-from-rest-api"></a>Monitorowanie z interfejsu API REST
Uwzględnianie danych monitorowania zebranych z zasobu w aplikacji niestandardowej przy użyciu interfejsu API REST.

- Zobacz [Przewodnik po interfejsie API REST monitorowania platformy Azure,](./rest-api-walkthrough.md) aby uzyskać szczegółowe informacje na temat uzyskiwania dostępu do metryk z Azure Monitor API REST.
- Zobacz [Interfejs API REST usługi Azure Log Analytics,](https://dev.loganalytics.io/) aby uzyskać informacje na temat uzyskiwania dostępu do Azure Monitor danych dzienników przy użyciu zapytania dziennika z Azure PowerShell.

## <a name="alerts"></a>Alerty
[Alerty](../alerts/alerts-overview.md) proaktywnie powiadamiają użytkownika i potencjalnie mogą podjąć działania w przypadku, gdy w danych monitorowania zostaną znalezione ważne warunki. Utworzysz regułę alertu, która definiuje element docelowy alertu, warunki tworzenia alertu oraz wszelkie akcje do podjęcia w odpowiedzi.

Różne rodzaje danych monitorowania są używane dla różnych rodzajów reguł alertów.

- [Alert dziennika aktywności](../alerts/alerts-activity-log.md) — tworzy alert po utworzeniu wpisu w dzienniku aktywności, który spełnia określone kryteria. Dzięki temu możesz być powiadamiany na przykład o utworzeniu określonego typu zasobu lub w przypadku niepowodzenia zmiany konfiguracji.
- [Alert metryki](../alerts/alerts-metric.md) — utwórz alert, gdy wartość metryki przekroczy określony próg. Alerty dotyczące metryk są bardziej dynamiczne niż inne alerty i można je automatycznie rozwiązać po skorygowania problemu.
- [Alert zapytania dziennika](../alerts/alerts-log.md) — uruchamianie zapytania dziennika w regularnych odstępach czasu i tworzenie alertu w przypadku wyszukiwania określonego warunku. Umożliwia to wykonywanie złożonej analizy wielu zestawów danych i .

Użyj **alertów** z menu zasobu, aby wyświetlić alerty i zarządzać regułami alertów dla tego zasobu. Tylko alerty dziennika aktywności i alerty metryk używają pojedynczych zasobów platformy Azure jako celu. Alerty zapytań dzienników używają obszaru roboczego usługi Log Analytics jako miejsca docelowego i są oparte na zapytaniu, które może uzyskać dostęp do dowolnych dzienników przechowywanych w tym obszarze roboczym. Użyj menu Azure Monitor, aby wyświetlać alerty dla wszystkich zasobów i zarządzać nimi, a także zarządzać regułami alertów zapytania dziennika.

- Zobacz artykuły dotyczące różnych rodzajów alertów powyżej, aby uzyskać szczegółowe informacje na temat tworzenia reguł alertów.
- Zobacz [Tworzenie grup akcji i](../alerts/action-groups.md) zarządzanie nimi w Azure Portal, aby uzyskać szczegółowe informacje na temat tworzenia grupy akcji, która umożliwia zarządzanie odpowiedziami na alerty.



## <a name="next-steps"></a>Następne kroki

* Zobacz [Obsługiwane usługi, schematy i kategorie dla dzienników](./resource-logs-schema.md) zasobów platformy Azure, aby uzyskać szczegółowe informacje o dziennikach zasobów dla różnych usług platformy Azure.
