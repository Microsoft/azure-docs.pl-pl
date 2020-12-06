---
title: Co nowego w dokumentacji Azure Monitor
description: Ważne aktualizacje dokumentacji Azure Monitor aktualizowane co miesiąc.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 12/04/2020
ms.openlocfilehash: 91aa76fd3d9dbb54316f32fdb319e92ca2283b1d
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745778"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>Co nowego w dokumentacji Azure Monitor?

Ten artykuł zawiera listę artykułów Azure Monitor, które są nowe lub zostały znacząco zaktualizowane. Zostanie odświeżony pierwszy tydzień każdego miesiąca, aby uwzględnić aktualizacje artykułów z poprzedniego miesiąca.

## <a name="november-2020"></a>Listopad 2020 r.

### <a name="general"></a>Ogólne
- [Limity usługi Azure monitor](service-limits.md) — zaktualizowane dla pomocy technicznej usługi Azure Arc.

### <a name="agents"></a>Agenci
- [Omówienie agentów monitorowania platformy Azure](platform/agents-overview.md) — Zaktualizowano obsługę usługi Azure Arc.
- [Zainstaluj agenta Azure monitor](platform/azure-monitor-agent-install.md) — nowy artykuł.
- [Omówienie agenta Azure monitor](platform/azure-monitor-agent-overview.md) — Zaktualizowano obsługę usługi Azure Arc.
- [Przykłady szablonów Menedżer zasobów dla agentów](samples/resource-manager-agent.md) — zaktualizowane dla pomocy technicznej usługi Azure Arc.

### <a name="alerts"></a>Alerty
- [Utwórz grupy akcji i zarządzaj nimi w Azure Portal](platform/action-groups.md) źródłowych adresów IP dla elementów webhook.

### <a name="application-insights"></a>Application Insights
- [Monitorowanie aplikacji bezkodowej języka Java Azure Monitor Application Insights](app/java-in-process-agent.md) dodano przykład konfiguracji.
- [Zareaguj na wtyczkę dla Application Insights zestawu SDK języka JavaScript](app/javascript-react-plugin.md) — dodana sekcja przy użyciu punktów zaczepienia.
- [Uaktualnianie z Application Insights Java 2. x SDK](app/java-standalone-upgrade-from-2x.md) — nowy artykuł.
- [Informacje o wersji dla Microsoft. ApplicationInsights. SnapshotCollector](app/snapshot-collector-release-notes.md) — nowy artykuł.

### <a name="autoscale"></a>Automatyczne skalowanie
- [Rozpocznij pracę z funkcją automatycznego skalowania na platformie Azure](platform/autoscale-get-started.md) — Dodano sekcję na temat przesuwania skalowania automatycznego do innego regionu.

### <a name="data-collection"></a>Zbieranie danych
- [Skonfiguruj zbieranie danych dla agenta Azure monitor (wersja zapoznawcza)](platform/data-collection-rule-azure-monitor-agent.md) — Zaktualizowano pod kątem obsługi usługi Azure Arc.
- [Reguły zbierania danych w Azure monitor (wersja zapoznawcza)](platform/data-collection-rule-overview.md) — Zaktualizowano dla pomocy technicznej usługi Azure Arc.
- [Przykłady szablonów Menedżer zasobów dla reguł zbierania danych](samples/resource-manager-data-collection-rules.md) — nowy artykuł.

### <a name="insights-and-solutions"></a>Szczegółowe informacje i rozwiązania
- [Połącz platformę Azure z narzędziami narzędzia ITSM przy użyciu opcji bezpiecznego eksportu,](platform/it-service-management-connector-secure-webhook-connections.md) Aby dołączać do usługi ServiceNow.

### <a name="logs"></a>Dzienniki
- [Integruj log Analytics i Excel](log-query/log-excel.md) — nowy artykuł.
- Sekcja [log Analytics dodano zabezpieczenia danych](platform/data-security.md) w dodatkowych funkcjach zabezpieczeń.
- [Integracja log Analytics z](log-query/log-powerbi.md) nowym artykułem Power BI.
- [Kolumny standardowe w Azure monitor rekordy dziennika](platform/log-standard-columns.md) — dodano _SubscriptionId kolumnę.

Nowe i zaktualizowane artykuły z restrukturyzacji zawartości zapytania dziennika.

- [Samouczek usługi Log Analytics](log-query/log-analytics-tutorial.md)
- [Rejestruj zapytania w Azure Monitor](log-query/log-query-overview.md)
- [Omówienie Log Analytics w Azure Monitor](log-query/log-analytics-overview.md)
- [Przykłady zapytań dotyczących usługi Azure Eksplorator danych i Azure Monitor](/azure/data-explorer/kusto/query/samples?pivots=azuremonitor)
- [Samouczek: używanie zapytań Kusto w usłudze Azure Eksplorator danych i Azure Monitor](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor)



### <a name="virtual-machines"></a>Maszyny wirtualne

- [Włączenie Azure monitor dla maszyn wirtualnych przegląd](insights/vminsights-enable-overview.md) — dodano Obsługiwane regiony.

Nowe artykuły dotyczące Azure Monitor dla maszyn wirtualnych kondycji gościa (wersja zapoznawcza)

- [Azure Monitor dla maszyn wirtualnych kondycja gościa (wersja zapoznawcza)](insights/vminsights-health-overview.md)
- [Azure Monitor dla maszyn wirtualnych alertów dotyczących kondycji gościa (wersja zapoznawcza)](insights/vminsights-health-alerts.md)
- [Konfigurowanie monitorowania w Azure Monitor dla maszyn wirtualnych kondycji gościa (wersja zapoznawcza)](insights/vminsights-health-configure.md)
- [Konfigurowanie monitorowania w Azure Monitor dla maszyn wirtualnych kondycji gościa przy użyciu reguł zbierania danych (wersja zapoznawcza)](insights/vminsights-health-configure-dcr.md)
- [Włącz Azure Monitor dla maszyn wirtualnych kondycję gościa (wersja zapoznawcza)](insights/vminsights-health-enable.md)
- [Rozwiązywanie problemów Azure Monitor dla maszyn wirtualnych kondycji gościa (wersja zapoznawcza)](insights/vminsights-health-troubleshoot.md)





## <a name="october-2020"></a>Październik 2020 r.

### <a name="general"></a>Ogólne
- [Wycofanie interfejsu API Azure monitor](platform/operationalinsights-api-retirement.md) — nowy artykuł.

### <a name="agents"></a>Agenci
- [Co jest monitorowane według sekcji Azure monitor](monitor-reference.md) dodanej do agentów.

### <a name="alerts"></a>Alerty
- [Tworzenie grup akcji i zarządzanie nimi w sekcji Azure Portal](platform/action-groups.md) dodanej przez tag usługi.
- [Przykłady szablonów Menedżer zasobów dla alertów metryk](samples/resource-manager-alerts-metric.md) — dodano parametr dopasowania zawartości i lokalizacje testów.
- [Rozwiązywanie problemów z alertami metryk platformy Azure](platform/alerts-troubleshoot-metric.md) — dodano najlepsze rozwiązanie dotyczące konfiguracji reguł.

### <a name="application-insights"></a>Application Insights
- [Wtyczka kątowa dla Application Insights JavaScript SDK](app/javascript-angular-plugin.md) — nowy artykuł.
- [Application Insights platformy Azure dla aplikacji ASP.NET Core](app/asp-net-core.md) — dodano często zadawane pytania dotyczące dzienników ILogger.
- [Skonfiguruj monitorowanie dla ASP.NET za pomocą platformy Azure Application Insights](app/asp-net.md) — artykuł ponownie zapisany.
- [Metryki oparte na dzienniku i wstępnie agregowane w usłudze Azure Application Insights](app/pre-aggregated-metrics-log-metrics.md) — dodane tabele z metrykami wstępnie zagregowanymi.
- [Monitoruj dostępność i czas odpowiedzi dowolnej](app/monitor-web-app-availability.md) sekcji dodanej witryny sieci Web w tagach populacji lokalizacji.
- [Monitoruj aplikacje Java w dowolnym miejscu —](app/java-standalone-config.md) przykład konfiguracji dodanej Application Insights Azure monitor.
- [Monitoruj aplikacje Java w dowolnym miejscu — Azure Monitor Application Insights](app/java-standalone-telemetry-processors.md) — nowy artykuł.
- [Użyj analizy zmian aplikacji w Azure monitor, aby znaleźć zagadnienia dotyczące aplikacji sieci Web](app/change-analysis.md) — dodano sekcje na maszynach wirtualnych i dzienniku aktywności.
  
### <a name="autoscale"></a>Automatyczne skalowanie
- [Rozpocznij pracę z funkcją automatycznego skalowania na platformie Azure](platform/autoscale-get-started.md) — Dodano sekcję na temat przesuwania skalowania automatycznego do innego regionu.

### <a name="containers"></a>Containers
- [Skonfiguruj monitorowanie PV przy użyciu Azure monitor dla kontenerów](insights/container-insights-persistent-volumes.md) — nowy artykuł.
- [Jak zarządzać Azure monitor do obsługi kontenerów](insights/container-insights-manage-agent.md) dodanych przez agenta dla klastra Kubernetes z obsługą usługi Azure Arc.
- [Alerty metryk z Azure monitor dla kontenerów](insights/container-insights-metric-alerts.md) — dodano obsługę klastra Kubernetes z obsługą usługi Azure Arc.

### <a name="insights-and-solutions"></a>Szczegółowe informacje i rozwiązania
- [Łącznik zarządzania usługami IT — Bezpieczny eksport w sekcji Azure monitor](platform/it-service-management-connector-secure-webhook-connections.md) dodanej do usługi ServiceNow.

### <a name="logs"></a>Dzienniki
- [Archiwizowanie danych z obszaru roboczego log Analytics w usłudze Azure Storage przy użyciu aplikacji logiki](platform/logs-export-logic-app.md) — nowy artykuł.
- [Log Analytics eksportu danych obszaru roboczego w Azure monitor (wersja zapoznawcza)](platform/logs-data-export.md) — dodaliśmy Przykładowa treść dla żądania REST dla centrum zdarzeń.
- [Zarządzanie użyciem i kosztami Azure monitor dzienników](platform/manage-cost-storage.md) — dodano informacje dotyczące relacji między dziennikami Azure monitor i rozliczeń Azure Security Center. Dodano zapytanie dla liczb węzłów w przypadku korzystania z warstwy cenowej na węzeł. 
- [Monitoruj kondycję obszaru roboczego log Analytics w Azure monitor](platform/monitor-workspace.md) — nowy artykuł.
- [Wykonywanie zapytań dotyczących danych w Azure monitor przy użyciu usługi Azure Eksplorator danych (wersja zapoznawcza)](platform/azure-data-explorer-monitor-proxy.md) — nowy artykuł.
- [Wyeksportuj dane z Azure monitor przy użyciu usługi Azure Eksplorator danych (wersja zapoznawcza)](platform/azure-data-explorer-query-storage.md) — nowy artykuł.

### <a name="networks"></a>Sieci
- [Azure monitor dla sieci](insights/network-insights-overview.md) — Dodano sekcję rozwiązywania problemów. Dodano sekcję na temat łączności.

### <a name="platform-logs"></a>Dzienniki platformy
- [Schemat zdarzeń dziennika aktywności platformy Azure](platform/activity-log-schema.md) — Dodano opis poziomów ważności.

### <a name="virtual-machines"></a>Maszyny wirtualne
- [Analiza zmian w Azure monitor dla maszyn wirtualnych](insights/vminsights-change-analysis.md) — nowy artykuł.
- [Włączenie Azure monitor dla maszyn wirtualnych przegląd](insights/vminsights-enable-overview.md) — dodano Obsługiwane regiony.
- [Jak aktualizować Azure monitor dla kontenerów w](insights/container-insights-update-metrics.md) celu dodania obsługi klastra Kubernetes z obsługą usługi Azure Arc.



## <a name="september-2020"></a>Wrzesień 2020

### <a name="general"></a>Ogólne
- [Azure monitor często zadawane pytania](faq.md) — Dodano sekcję w witrynie OpenTelemetry.

### <a name="agents"></a>Agenci
- [Omówienie agenta Azure monitor](platform/azure-monitor-agent-overview.md) — dodano czynniki decyzyjne w celu przełączenia do nowego agenta.
- [Informacje o obsłudze agentów monitorowania platformy Azure](platform/agents-overview.md) — dodano obsługę systemu Windows 10.

### <a name="alerts"></a>Alerty
- [Utwórz alert dotyczący dziennika z szablonem Azure Resource Manager](platform/alerts-log-create-templates.md) — nowy artykuł.
- [Rozwiązywanie problemów z alertami metryki platformy Azure](platform/alerts-troubleshoot-metric.md) — Dodano sekcję dotyczącą EKSPORTOWANIA szablonu ARM dla reguły alertu dotyczącego metryki.

### <a name="application-insights"></a>Application Insights
- [Utwórz nowy Azure Monitor Application Insights zasobów opartych na obszarze roboczym](app/create-workspace-resource.md) — usunięto oznaczenie podglądu.
- Przechowywanie [i przechowywanie danych w usłudze Azure Application Insights](app/data-retention-privacy.md) — dodano szczegóły dotyczące nowej obsługi ochrony przed utratą danych dla systemów Mac i Linux.
- [Liczniki zdarzeń w Application Insights](app/eventcounters.md) — dodano uwagę na licznikach zbieranych domyślnie.
- [Metryki oparte na dzienniku i wstępnie zagregowane na platformie Azure Application Insights](app/pre-aggregated-metrics-log-metrics.md) — oznaczenie wersji zapoznawczej usunięte.
- [Migrowanie Azure Monitor Application Insights zasobów klasycznych do nowego artykułu opartego na obszarze roboczym](app/convert-classic-resource.md) .
- [Monitoruj aplikacje Java w dowolnym środowisku — Azure Monitor Application Insights](app/java-in-process-agent.md) — Zaktualizowano pod kątem nowej wersji zapoznawczej agenta.
- [Skonfiguruj program Web App Analytics for ASP.NET za pomocą usługi Azure Application Insights](app/asp-net.md) — artykuł ponownie zapisany.
- [Kanały telemetrii na platformie Azure Application Insights](app/telemetry-channels.md) — dodano szczegóły dotyczące nowej obsługi ochrony przed utratą danych dla systemów Mac i Linux.
- [Rozwiązywanie problemów z usługą Azure Application Insights Snapshot Debugger](app/snapshot-debugger-troubleshoot.md) — Dodano sekcję SSL w celu Snapshot Debugger rozwiązywania problemów.
- [Użyj analizy zmian aplikacji w Azure monitor, aby znaleźć problemy z aplikacją sieci Web](app/change-analysis.md) — dodano maszynę wirtualną i dziennik aktywności.


### <a name="containers"></a>Containers
- [Skonfiguruj klaster Kubernetes z obsługą usługi Azure ARC przy użyciu Azure monitor dla kontenerów](insights/container-insights-enable-arc-enabled-clusters.md) — dodano wskazówki dotyczące włączania monitorowania za pomocą nazwy głównej usługi.
- [Wdrożenie & hPa metryk z Azure monitor dla kontenerów](insights/container-insights-deployment-hpa-metrics.md) — nowy artykuł.

### <a name="insights-and-solutions"></a>Szczegółowe informacje i rozwiązania
- [Azure monitor dla usługi Azure cache for Redis](insights/redis-cache-insights-overview.md) — oznaczenie podglądu usuniętego elementu.
- [Azure monitor dla sieci (wersja zapoznawcza)](insights/network-insights-overview.md) — dodano sekcje połączeń i ruchu.
- [Łącznik zarządzania usługami IT — Bezpieczny eksport w Azure monitor](platform/it-service-management-connector-secure-webhook-connections.md) — nowy artykuł.
- [Łącznik zarządzania usługami IT w Azure monitor](platform/itsmc-connections.md) — uwagi dotyczące integracji z Cherwell i Provance narzędzia ITSM.
- [Key Vault monitorowania z Azure monitor dla Key Vault](insights/key-vault-insights-overview.md) — usunięto oznaczenie podglądu.

### <a name="logs"></a>Dzienniki
- [Zapytania inspekcji w dziennikach Azure monitor](log-query/query-audit.md) — nowy artykuł.
- Azure Monitor skrytką klienta dodano [klucz zarządzany przez klienta](platform/customer-managed-keys.md) .
- [Azure monitor rejestruje dedykowane klastry](log-query/logs-dedicated-clusters.md) — nowy artykuł.
- [Projektowanie Azure monitor wdrożenia dzienników](platform/design-logs-deployment.md) — Zaktualizowano sekcję limit współczynnika rozmiaru i ilości pozyskanych danych.
- [Zakres zapytania dziennika w Azure Monitor Log Analytics](log-query/scope.md) — aktualizacje mające na celu uwzględnienie aplikacji opartych na obszarze roboczym.
- [Rejestruje Azure monitor](platform/data-platform-logs.md) — aktualizacje mające na celu uwzględnienie aplikacji opartych na obszarze roboczym.
- [Standardowe kolumny w Azure monitor rekordy dziennika](platform/log-standard-columns.md) — aktualizacje do uwzględnienia aplikacji opartych na obszarze roboczym.
- [Azure monitor limity usługi](service-limits.md) — Zaktualizowano limity ograniczania zapytania użytkownika.
- [Korzystanie z kont magazynu zarządzanych przez klienta w Azure Monitor Log Analytics](platform/private-storage.md) — artykuł ponownie zapisany.
- [Wyświetlanie i analizowanie danych w usłudze Azure log Analytics](./platform/data-platform-logs.md) — aktualizacje mające na celu uwzględnienie aplikacji opartych na obszarze roboczym.


### <a name="platform-logs"></a>Dzienniki platformy
- [Schemat zdarzeń dziennika aktywności platformy Azure —](platform/activity-log-schema.md) dodano poziomy ważności Azure monitor.
- [Przykłady szablonów Menedżer zasobów dla ustawień diagnostycznych](samples/resource-manager-diagnostic-settings.md) — dodano przykład dla konta usługi Azure Storage.

### <a name="visualizations"></a>Wizualizacje
- [Azure monitor wizualizacje wykresów skoroszytu](platform/workbooks-chart-visualizations.md) — nowy artykuł.
- [Moduł renderowania złożonego paska Azure monitor skoroszytu](platform/workbooks-composite-bar.md) — nowy artykuł.
- [Azure monitor wizualizacje wykresów skoroszytu](platform/workbooks-graph-visualizations.md) — nowy artykuł.
- [Azure monitor wizualizacje siatki skoroszytu](platform/workbooks-grid-visualizations.md) — nowy artykuł.
- [Wizualizacje plastra miodu Azure monitor skoroszyty](platform/workbooks-honey-comb.md) — nowy artykuł.
- [Azure monitor wizualizacje tekstu skoroszytu](platform/workbooks-text-visualizations.md) — nowy artykuł.
- [Azure monitor wizualizacje kafelków skoroszytu](platform/workbooks-tile-visualizations.md) — nowy artykuł.
- [Azure monitor wizualizacje drzewa skoroszytów](platform/workbooks-tree-visualizations.md) — nowy artykuł.




## <a name="august-2020"></a>Sierpień 2020 r.

### <a name="general"></a>Ogólne

- [Co jest monitorowane przez Azure monitor](monitor-reference.md) — zaktualizowano w celu uwzględnienia Azure monitor agenta.


### <a name="agents"></a>Agenci
- [Omówienie agenta Azure monitor](platform/azure-monitor-agent-overview.md) — nowy artykuł.
- [Włącz Azure monitor dla środowiska hybrydowego](insights/vminsights-enable-hybrid.md) — zaktualizowana wersja agenta zależności.
- [Omówienie agentów monitorowania platformy Azure](platform/agents-overview.md) — dodano agenta Azure monitor i tabelę pomocy technicznej systemu operacyjnego.


#### <a name="new-and-updated-articles-from-restructure-of-agent-content"></a>Nowe i zaktualizowane artykuły z restrukturyzacji zawartości agenta
- [Włącz przegląd Azure Monitor dla maszyn wirtualnych](insights/vminsights-enable-overview.md)
- [Instalowanie agenta usługi Log Analytics na komputerach z systemem Linux](platform/agent-linux.md)
- [Instalowanie agenta usługi Log Analytics na komputerach z systemem Windows](platform/agent-windows.md)
- [Omówienie agenta Log Analytics](platform/log-analytics-agent.md)

### <a name="application-insights"></a>Application Insights
- [Application Insights platformy Azure dla aplikacji sieci Web JavaScript](app/javascript.md) — Dodano sekcję z wyjaśnieniem korelacji i konfiguracją serwera klienta na potrzeby korelacji CORS.
- [Utwórz nowe Azure Monitor Application Insights](app/create-workspace-resource.md) możliwości dodanych przez obszary robocze udostępniane przez aplikacje oparte na obszarze roboczym.
- [Adresy IP używane przez Application Insights i log Analytics](app/ip-addresses.md) — zaktualizowane adresy IP dla strumienia metryk na żywo.
- [Monitoruj aplikacje Java w dowolnym środowisku — Azure Monitor Application Insights](app/java-in-process-agent.md) dodanej tabeli pod kątem obsługiwanej niestandardową telemetrię.
- [Wtyczka z natywną obsługą reakcji dla Application Insights JavaScript SDK](app/javascript-react-native-plugin.md) — nowy artykuł.
- [Zareaguj na wtyczkę dla Application Insights JavaScript SDK](app/javascript-react-plugin.md) — nowy artykuł.
- [Przykład szablonu Menedżer zasobów na potrzeby tworzenia aplikacji funkcji platformy Azure przy użyciu monitorowania Application Insights](samples/resource-manager-function-app.md) — nowy artykuł.
- [Przykłady szablonów Menedżer zasobów do tworzenia aplikacji sieci Web platformy Azure App Services za pomocą monitorowania Application Insights](samples/resource-manager-web-app.md) — nowy artykuł.
- [Analiza użycia za pomocą dodanego wideo na platformie Azure Application Insights](app/usage-overview.md) .

### <a name="autoscale"></a>Automatyczne skalowanie
- [Rozpocznij pracę z funkcją automatycznego skalowania na platformie Azure](platform/autoscale-get-started.md) — Dodano sekcję dotyczącą routingu do wystąpień w dobrej kondycji dla App Service.

### <a name="data-collection"></a>Zbieranie danych
- [Skonfiguruj zbieranie danych dla agenta Azure monitor (wersja zapoznawcza)](platform/data-collection-rule-azure-monitor-agent.md) — nowy artykuł.
- [Reguły zbierania danych w Azure monitor (wersja zapoznawcza)](platform/data-collection-rule-overview.md) — nowy artykuł.


### <a name="containers"></a>Containers
- [Wdrożenie & hPa metryk z Azure monitor dla kontenerów](insights/container-insights-deployment-hpa-metrics.md) — nowy artykuł.

### <a name="insights"></a>Insights
- [Monitorowanie rozwiązań w Azure monitor](insights/solutions.md) — zaktualizowane dla nowego interfejsu użytkownika.
- [Network Performance Monitor rozwiązanie na platformie Azure](insights/network-performance-monitor.md) — dodano Obsługiwane regiony obszaru roboczego.


### <a name="logs"></a>Dzienniki
- [Azure monitor często zadawane pytania](faq.md) — dodano wpis służący do usuwania danych z obszaru roboczego. Dodano wpis na 502 i 503 odpowiedzi.
  - [Projektowanie wdrożenia dzienników Azure monitor](platform/design-logs-deployment.md) — liczba aktualizacji w sekcji limit ilości woluminu pozyskiwania.
- [Zarządzanie użyciem i kosztami Azure monitor dzienników](platform/manage-cost-storage.md) — zaktualizowane zapytania dotyczące użycia do bardziej wydajnego formatu zapytania.
- [Optymalizowanie zapytań dzienników w Azure monitor](log-query/query-optimization.md) dodanych określonych wartości do wskaźników wydajności.
- [Przykłady szablonów Menedżer zasobów dla ustawień diagnostycznych](samples/resource-manager-diagnostic-settings.md) — dodano przykład dla dzienników inspekcji zapytań dzienników.


### <a name="platform-logs"></a>Dzienniki platformy
- [Tworzenie ustawień diagnostycznych w celu wysyłania dzienników platformy i metryk do różnych miejsc docelowych](platform/diagnostic-settings.md) — dodanych wymagań regionalnych dla ustawień diagnostycznych.

### <a name="visualizations"></a>Wizualizacje
- [Przegląd Azure monitor skoroszytów](platform/workbooks-overview.md) — dodano film wideo.
- [Przenieś szablon skoroszytu platformy Azure do innego regionu](platform/workbook-templates-move-region.md) — nowy artykuł.
- [Przenieś skoroszyt platformy Azure do innego regionu](platform/workbooks-move-region.md) — nowy artykuł.



## <a name="july-2020"></a>Lipiec 2020 r.

### <a name="general"></a>Ogólne
- [Wdróż Azure monitor](deploy-scale.md) -rekonstrukcję Azure monitor dla maszyn wirtualnych zawartości dołączania.
- [Skorzystaj z prywatnego linku platformy Azure, aby bezpiecznie połączyć sieci z](platform/private-link-security.md) sekcją dodaną do Azure monitor.

### <a name="alerts"></a>Alerty
- [Reguły akcji dla alertów Azure monitor](platform/alerts-action-rules.md) — dodano procesy interfejsu wiersza polecenia.
- [Tworzenie grup akcji i zarządzanie nimi w Azure Portal](platform/action-groups.md) — zaktualizowano w celu odzwierciedlenia zmian w interfejsie użytkownika.
- [Przykładowe zapytania w Azure Monitor Log Analytics](log-query/example-queries.md) — nowy artykuł.
- [Rozwiązywanie problemów z alertami dziennika w sekcji Azure monitor](platform/alerts-troubleshoot-log.md) dodanej do przydziału reguły alertu.
- [Rozwiązywanie problemów z alertami metryki platformy Azure](platform/alerts-troubleshoot-metric.md) — Dodano sekcję dotyczącą reguły alertu w niestandardowej metryce, która nie jest jeszcze emitowana.
- [Informacje o tym, jak alerty metryk działają w Azure Monitor.](platform/alerts-metric-overview.md) -Dodano rekomendację dotyczącą wyboru stopnia szczegółowości agregacji.

### <a name="application-insights"></a>Application Insights
- [Informacje o wersji rozszerzenia aplikacji sieci Web platformy Azure — Application Insights](app/web-app-extension-release-notes.md) — nowy artykuł.
- [Przykłady szablonów Menedżer zasobów dla zasobów Application Insights](samples/resource-manager-app-resource.md) — nowy artykuł.
- [Rozwiązywanie problemów z usługą Azure Application Insights Profiler](app/profiler-troubleshooting.md) — dodano uwagę na temat usterki dotyczącej uruchamiania programu Profiler dla ASP.NET Core aplikacji w Azure App Service. 

### <a name="containers"></a>Containers
- [Alerty dzienników z Azure monitor dla kontenerów](insights/container-insights-log-alerts.md) — nowy artykuł.
- [Alerty metryk z Azure monitor dla kontenerów](insights/container-insights-metric-alerts.md) — nowy artykuł.

### <a name="logs"></a>Dzienniki
- [Azure monitor](platform/customer-managed-keys.md) komunikat o błędzie dodany przez klienta i sekcja CMK konfigurację dla zapytań.
- [Azure monitor interfejs API modułu zbierającego dane http](platform/data-collector-api.md) — dodano przykład języka Python 3.
- [Optymalizuj zapytania dzienników w sekcji Azure monitor](log-query/query-optimization.md) dodanej, unikając wielu skanów danych podczas korzystania z podzapytań.
- [Samouczek: Rozpoczynanie pracy z zapytaniami log Analytics](./log-query/log-analytics-tutorial.md) — dodano film wideo.

### <a name="platform-logs"></a>Dzienniki platformy
- [Tworzenie ustawień diagnostycznych w celu wysyłania dzienników platformy i metryk do różnych](platform/diagnostic-settings.md) dodanych filmów wideo.
- [Przykłady szablonów Menedżer zasobów dla](samples/resource-manager-samples.md) przykładowej Azure monitor dodanej ARM przy użyciu typu docelowego dzienników. 

### <a name="solutions"></a>Rozwiązania
- [Monitorowanie rozwiązań w](insights/solutions.md) procesach interfejsu wiersza polecenia dodanych Azure monitor.
- [Rozwiązanie do zarządzania pakietem Office 365 na platformie Azure](insights/solution-office-365.md) — Data wycofania zmian.

### <a name="virtual-machines"></a>Maszyny wirtualne

Nowe i zaktualizowane artykuły z restrukturyzacji zawartości Azure Monitor dla maszyn wirtualnych

- [Co to jest Azure Monitor dla maszyn wirtualnych?](insights/vminsights-overview.md)
- [Skonfiguruj obszar roboczy Log Analytics dla Azure Monitor dla maszyn wirtualnych](insights/vminsights-configure-workspace.md)
- [Łączenie komputerów z systemem Linux z Azure Monitor](platform/agent-linux.md)
- [Włącz Azure Monitor środowiska hybrydowego](insights/vminsights-enable-hybrid.md)
- [Włącz Azure Monitor dla jednej maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych w Azure Portal](insights/vminsights-enable-portal.md)
- [Włączanie Azure Monitor dla maszyn wirtualnych przy użyciu Azure Policy](./insights/vminsights-enable-policy.md)
- [Włącz przegląd Azure Monitor dla maszyn wirtualnych](insights/vminsights-enable-overview.md)
- [Włączanie Azure Monitor dla maszyn wirtualnych przy użyciu programu PowerShell](insights/vminsights-enable-powershell.md)
- [Włączanie Azure Monitor dla maszyn wirtualnych przy użyciu szablonów Menedżer zasobów](insights/vminsights-enable-resource-manager.md)
- [Włączanie Azure Monitor dla maszyn wirtualnych przy użyciu programu PowerShell lub szablonów](./insights/vminsights-enable-powershell.md)


### <a name="visualizations"></a>Wizualizacje
- [Uaktualnianie log Analytics wizualizacji pulpitu nawigacyjnego](log-query/dashboard-upgrade.md) — Zaktualizowano częstotliwość odświeżania.
- [Wizualizowanie danych z](visualizations.md) wideo dodanego Azure monitor.


## <a name="june-2020"></a>Czerwiec 2020 r.

### <a name="general"></a>Ogólne
- [Wdróż Azure monitor](deploy-scale.md) — nowy artykuł.
- Azure Monitor Właściwość rozliczenia [klucza zarządzanego przez klienta](platform/customer-managed-keys.md) . Dodano polecenia programu PowerShell.

### <a name="agents"></a>Agenci
- [Omówienie agenta log Analytics](platform/log-analytics-agent.md) — dodano wymagania dotyczące języka Python 2.

### <a name="alerts"></a>Alerty
- [Jak zaktualizować reguły alertów lub reguły akcji, gdy ich zasób docelowy jest przenoszony do innego regionu platformy Azure](platform/alerts-resource-move.md) — nowy artykuł.
- [Rozwiązywanie problemów z alertami usługi Azure Metrics](platform/alerts-troubleshoot-metric.md) — nowy artykuł.
- [Rozwiązywanie problemów z alertami dziennika w Azure monitor](platform/alerts-troubleshoot-metric.md) — nowy artykuł.
  
### <a name="application-insights"></a>Application Insights
- [Azure Application Insights for JavaScript Web Apps](app/javascript.md) — aktualizacja do sekcji SDK języka JavaScript. Zaktualizowany fragment kodu dotyczący błędów ładowania raportu.
- [Konfigurowanie BYOS (przenoszenie własnego magazynu) dla profilera & Snapshot Debugger](app/profiler-bring-your-own-storage.md) — nowy artykuł.
- [Przychodzące śledzenie żądań na platformie Azure Application Insights z OpenCensus Python](app/opencensus-python-request.md) — Zaktualizowano rejestrowanie i konfigurację dla OpenCensus.
- [Monitoruj działającą aplikację sieci web ASP.NET za pomocą platformy Azure Application Insights](app/monitor-performance-live-website-now.md) — Zaktualizowano datę wycofania dla Monitor stanu v1.
- [Monitorowanie usług Node.js za pomocą usługi Azure Application Insights](app/nodejs.md) — wiele aktualizacji, w tym Migrowanie z wersji poprzedniej i konfiguracji zestawu SDK
- [Monitoruj aplikacje Python z sekcją Azure monitor (wersja zapoznawcza)](app/opencensus-python.md) dotyczącą konfigurowania Azure monitor eksportujących.
- [Monitoruj aplikacje bez zmian w kodzie — funkcja autoinstrumentation dla Azure Monitor Application Insights](app/codeless-overview.md) — nowy artykuł.
- [Rozwiązywanie problemów z błędem ładowania zestawu SDK dla aplikacji sieci Web w języku JavaScript](app/javascript-sdk-load-failure.md) — nowy artykuł.

### <a name="containers"></a>Containers
- [Jak zatrzymać monitorowanie dodanej sekcji hybrydowej klastra Kubernetes](insights/container-insights-optout-hybrid.md) dla Kubernetes z funkcją Arc.
- [Skonfiguruj klaster Kubernetes z obsługą usługi Azure ARC przy użyciu Azure monitor dla kontenerów](insights/container-insights-enable-arc-enabled-clusters.md) — nowy artykuł.
- [Skonfiguruj platformę Azure Red Hat OpenShift v4. x z Azure monitor for Containers](insights/container-insights-azure-redhat4-setup.md) — zaktualizowane wymagania wstępne.
- [Skonfiguruj Azure monitor dla kontenerów dane dynamiczne (wersja zapoznawcza)](insights/container-insights-livedata-setup.md) — usunięto uwagę na to, że funkcja nie jest dostępna w programie Azure USA dla instytucji rządowych.

### <a name="insights"></a>Insights
- [Często zadawane pytania — Network Performance Monitor rozwiązanie na platformie Azure](insights/network-performance-monitor-faq.md) — dodano obsługę często zadawanych pytań dotyczących monitora ExpressRoute.

### <a name="logs"></a>Dzienniki
- [Usuń i Odzyskaj polecenie programu PowerShell dla usługi Azure log Analytics Workspace](platform/delete-workspace.md) . Zaktualizowano Rozwiązywanie problemów.
- [Zarządzanie obszarami roboczymi Log Analytics Azure monitor w](platform/manage-access.md) przykładowym przykładzie dla niedozwolonych tabel w sekcji Azure RBAC.
- [Zarządzanie użyciem i kosztami dzienników Azure monitor](platform/manage-cost-storage.md) — dodatkowe szczegóły dotyczące obliczania rozmiaru danych. Zaktualizowano Konfigurowanie alertów ilości danych. Szczegółowe informacje o danych zabezpieczeń zebranych przez wskaźnik na platformie Azure. Wyjaśnienie dotyczące limitów danych.
- [Użyj dzienników Azure monitor z użyciem Azure Logic Apps i automatyzacji oszczędzania mocy](platform/logicapp-flow-connector.md) — dodano limity łączników.

### <a name="metrics"></a>Metryki
- [Azure monitor metryki obsługiwane przez typ zasobu](platform/metrics-supported.md) — Zaktualizowano metryki SQL Server.


### <a name="platform-logs"></a>Dzienniki platformy

- [Przykłady szablonów Menedżer zasobów dla ustawień diagnostycznych](samples/resource-manager-diagnostic-settings.md) — poprawka dla ustawienia diagnostyki dziennika aktywności.
- [Wyślij dziennik aktywności platformy Azure do log Analytics obszaru roboczego przy użyciu Azure Portal](learn/quick-collect-activity-log-portal.md) — nowy artykuł.
- [Wyślij dziennik aktywności platformy Azure do log Analytics obszaru roboczego przy użyciu szablonu Azure Resource Manager](learn/quick-collect-activity-log-arm.md) — nowy artykuł.

Nowe i zaktualizowane artykuły z przebudowy i konsolidacji zawartości dziennika platformy

- [Archiwizuj dzienniki zasobów platformy Azure na koncie magazynu](./platform/resource-logs.md#send-to-azure-storage)
- [Schemat zdarzeń dziennika aktywności platformy Azure](platform/activity-log-schema.md)
- [Dziennik aktywności platformy Azure](platform/activity-log.md)
- [Przykłady interfejsu wiersza polecenia Azure Monitor](samples/cli-samples.md)
- [Przykłady programu Azure Monitor PowerShell](samples/powershell-samples.md)
- [Przewodnik po interfejsie API REST usługi Azure Monitoring](platform/rest-api-walkthrough.md)
- [Obsługiwane usługi i schematy dzienników zasobów platformy Azure](./platform/resource-logs-schema.md)
- [Dzienniki zasobów platformy Azure](platform/resource-logs.md)
- [Zbieranie i analizowanie dzienników aktywności platformy Azure w Azure Monitor](./platform/activity-log.md)
- [Zbieranie dzienników zasobów platformy Azure w obszarze roboczym Log Analytics](./platform/resource-logs.md#send-to-log-analytics-workspace)
- [Tworzenie ustawień diagnostycznych w celu wysyłania metryk i dzienników platformy do różnych miejsc docelowych](platform/diagnostic-settings.md)
- [Eksportowanie dziennika aktywności platformy Azure](./platform/activity-log.md#legacy-collection-methods)
- [Omówienie dzienników platformy Azure](platform/platform-logs-overview.md)
- [Przesyłanie strumieniowe dzienników platformy Azure do centrum zdarzeń](./platform/resource-logs.md#send-to-azure-event-hubs)
- [Wyświetlanie zdarzeń dziennika aktywności platformy Azure w Azure Monitor](./platform/activity-log.md#view-the-activity-log)

### <a name="virtual-machines"></a>Maszyny wirtualne
- [Włącz Azure monitor dla maszyn wirtualnych w Azure Portal](./insights/vminsights-enable-portal.md) — zaktualizowano w celu uwzględnienia usługi Azure Arc.
- [Włączanie Azure monitor dla maszyn wirtualnych przegląd](insights/vminsights-enable-overview.md) — zaktualizowano w celu uwzględnienia usługi Azure Arc.
- [Co to jest Azure Monitor dla maszyn wirtualnych?](insights/vminsights-overview.md) — Zaktualizowano w celu uwzględnienia usługi Azure Arc.


### <a name="visualizations"></a>Wizualizacje
- [Azure monitor skoroszyty ze źródłami danych](platform/workbooks-data-sources.md) — dodano alerty i sekcję niestandardowych punktów końcowych.
- [Rozwiązywanie problemów Azure monitor szczegółowych informacji opartych na skoroszycie](insights/troubleshoot-workbooks.md) — nowy artykuł.
- [Uaktualnianie log Analytics wizualizacji pulpitu nawigacyjnego](log-query/dashboard-upgrade.md) — nowy artykuł.



## <a name="may-2020"></a>Maj 2020 r.

### <a name="general"></a>Ogólne

- [Azure monitor często zadawane pytania](faq.md) — Dodano sekcję dla metryk.
- [Azure monitor zarządzanie kluczami zarządzanymi przez klienta](platform/customer-managed-keys.md) — różne zmiany w zakresie ogólnej dostępności.
- [Wbudowane definicje zasad dla Azure monitor](./samples/policy-reference.md) — nowy artykuł.
- [Konta magazynu należące do klienta na potrzeby](platform/private-storage.md) pozyskiwania dziennika — nowy artykuł.
- [Zarządzanie użyciem i kosztami dla dzienników Azure monitor](platform/manage-cost-storage.md) — dodano rozliczenia proporcjonalnie do klastra.
- [Skorzystaj z prywatnego linku platformy Azure, aby bezpiecznie połączyć sieci z](platform/private-link-security.md) nowym artykułem Azure monitor.


#### <a name="new-resource-manager-template-samples"></a>Nowe przykłady szablonów Menedżer zasobów 
- [Przykłady szablonów Menedżer zasobów dla Azure Monitor](samples/resource-manager-samples.md)
- [Przykłady szablonów Menedżer zasobów dla grup akcji](samples/resource-manager-action-groups.md)
- [Przykłady szablonów Menedżer zasobów dla agentów](samples/resource-manager-agent.md)
- [Przykłady szablonów Menedżer zasobów dla Azure Monitor kontenerów](samples/resource-manager-container-insights.md)
- [Przykłady szablonów Menedżer zasobów dla Azure Monitor dla maszyn wirtualnych](samples/resource-manager-vminsights.md)
- [Przykłady szablonów Menedżer zasobów dla ustawień diagnostycznych](samples/resource-manager-diagnostic-settings.md)
- [Przykłady szablonów Menedżer zasobów dla obszarów roboczych Log Analytics](samples/resource-manager-workspace.md)
- [Przykłady szablonów Menedżer zasobów dla zapytań dziennika](samples/resource-manager-log-queries.md)
- [Przykłady szablonów Menedżer zasobów dla reguł alertów dotyczących zapytań dziennika](samples/resource-manager-alerts-log.md)
- [Przykłady szablonów Menedżer zasobów dla reguł alertów dotyczących metryk](samples/resource-manager-alerts-metric.md)
- [Przykłady szablonów Menedżer zasobów dla skoroszytów](samples/resource-manager-workbooks.md)

### <a name="agents"></a>Agenci
- [Zainstaluj i skonfiguruj rozszerzenie Windows Azure Diagnostics (funkcji wad)](platform/diagnostics-extension-windows-install.md) — dodano szczegóły dotyczące konfigurowania diagnostyki.
- [Omówienie agenta log Analytics](platform/log-analytics-agent.md) — dodano obsługiwane wersje systemu Linux.

### <a name="application-insights"></a>Application Insights

- [Monitoruj aplikacje działające na Azure Functions przy użyciu Application Insights-Azure monitor](app/monitor-functions.md) -nowego artykułu.
- [Monitoruj usługi Node.js za pomocą usługi Azure Application Insights](app/nodejs.md) — Ogólne aktualizacje, w tym nową sekcję na temat migracji z wcześniejszych wersji.
- [Adresy IP używane przez Application Insights i log Analytics](app/ip-addresses.md) — adresy IP dla elementów webhook i instytucji rządowych USA.
- [Monitoruj aplikacje w usłudze Azure Kubernetes Service (AKS) za pomocą Application Insights-Azure monitor](app/kubernetes-codeless.md) — nowy artykuł.
- [Rozwiązywanie problemów z sekcją Application Insights danych dla platformy .NET](app/asp-net-troubleshoot-no-data.md) — Dodawanie dzienników przy użyciu funkcji monitorowania dotnet.
- [Użyj analizy zmian aplikacji w Azure monitor, aby znaleźć problemy z aplikacją sieci Web](app/change-analysis.md) — wiele aktualizacji w funkcji analizy zmian.

#### <a name="new-and-updated-articles-for-preview-of-workspace-based-applications"></a>Nowe i zaktualizowane artykuły na potrzeby wersji zapoznawczej aplikacji opartych na obszarze roboczym
- [Azure Monitor Application Insights schemat zasobów oparty na obszarze roboczym](app/apm-tables.md)
- [Tworzenie nowego Azure Monitor Application Insights zasobów opartych na obszarze roboczym](app/create-workspace-resource.md)
- [wyrażenie App () w dziennikach Azure Monitor](log-query/app-expression.md)
- [Zakres zapytania dziennika w Azure Monitor Log Analytics](log-query/scope.md)
- [Wykonywanie zapytań między zasobami przy użyciu Azure Monitor](log-query/cross-workspace-query.md)
- [Standardowe właściwości w rekordach dziennika Azure Monitor](./platform/log-standard-columns.md)
- [Struktura dzienników Azure Monitor](./platform/data-platform-logs.md)





### <a name="containers"></a>Containers
- [Jak włączyć Azure monitor dla kontenerów](insights/container-insights-onboard.md) — zaktualizowana tabela konfiguracji zapory.
- [Jak zaktualizować Azure monitor kontenerów dla metryk](insights/container-insights-update-metrics.md) — Aktualizacja dotycząca używania tożsamości zarządzanych do zbierania metryk.
- [Koszt monitorowania Azure monitor kontenerów](insights/container-insights-cost.md) — nowy artykuł.
- [Skonfiguruj Azure monitor dla kontenerów dane dynamiczne (wersja zapoznawcza)](insights/container-insights-livedata-setup.md) — obsługa nowego powiązania roli klastra.

### <a name="insights"></a>Insights
- [Azure monitor dla usługi Azure cache for Redis (wersja zapoznawcza)](insights/redis-cache-insights-overview.md) — nowy artykuł.
- [Monitoruj Key Vault z Azure monitor do Key Vault (wersja zapoznawcza)](./insights/key-vault-insights-overview.md) — nowy artykuł.

### <a name="logs"></a>Dzienniki
- [Utwórz & skonfiguruj log Analytics przy użyciu](platform/powershell-workspace-configuration.md) sekcji Rozwiązywanie problemów dodanej do programu PowerShell.
- [Utwórz obszar roboczy log Analytics w](learn/quick-create-workspace.md) sekcji Rozwiązywanie problemów dodanej Azure Portal.
- [Utwórz obszar roboczy log Analytics przy użyciu interfejsu wiersza polecenia platformy Azure](learn/quick-create-workspace-cli.md) — Dodano sekcję Rozwiązywanie problemów.
- [Usuwanie i odzyskiwanie usługi Azure log Analytics Workspace](platform/delete-workspace.md) — zaktualizowane informacje dotyczące odzyskiwania usuniętego obszaru roboczego.
- [Funkcje w zapytaniach dziennika Azure monitor](log-query/functions.md) — usunięto uwagę na temat funkcji, które nie zawierają innych funkcji.
- [Struktura dzienników Azure monitor](./platform/data-platform-logs.md) — objaśniono opisy właściwości dla Application Insights tabeli.
- [Użyj dzienników Azure monitor z użyciem Azure Logic Apps i automatyzacji oszczędzania mocy](platform/logicapp-flow-connector.md) — Dodano sekcję limity.
- [Użyj programu PowerShell, aby utworzyć i skonfigurować](platform/powershell-workspace-configuration.md) sekcję rozwiązywania problemów dodaną przez log Analytics obszaru roboczego.


### <a name="metrics"></a>Metryki
- [Azure monitor obsługiwane metryki według typów zasobów](platform/metrics-supported.md) — wyjaśniono metryki Gości i Routing metryk. 

### <a name="solutions"></a>Rozwiązania
- [Zoptymalizuj środowisko Active Directory za pomocą Azure monitor](insights/ad-assessment.md) dodanego systemu Windows Server 2019 do obsługiwanych wersji.
- [Zoptymalizuj środowisko SQL Server przy użyciu Azure monitor](insights/sql-assessment.md) — dodano do obsługiwanych wersji programu SQL Server.


### <a name="virtual-machines"></a>Maszyny wirtualne
- [Włącz przegląd Azure monitor dla maszyn wirtualnych](insights/vminsights-enable-overview.md) — dodano do obsługiwanych wersji programu Ubuntu Server. Dodano Obsługiwane regiony dla Log Analytics obszaru roboczego.
- [Przedstawianie wykresu wydajności za pomocą](insights/vminsights-performance.md) sekcji ograniczeń Azure monitor dla maszyn wirtualnych dodanych dla niedostępnych metryk.

### <a name="visualizations"></a>Wizualizacje
- [Azure monitor skoroszyty i szablony Azure Resource Manager](platform/workbooks-automate.md) — dodano aktualizację Menedżer zasobów do wdrożenia szablonu skoroszytu.
- [Azure monitor grupy skoroszytów](platform/workbooks-groups.md) — nowy artykuł.
- [Azure monitor skoroszytów — Przekształć dane JSON przy użyciu wykryto](platform/workbooks-jsonpath.md) — nowy artykuł.


## <a name="april-2020"></a>Kwiecień 2020 r.

### <a name="general"></a>Ogólne

- [Azure monitor sekcji z kluczami zarządzanymi przez klienta](platform/customer-managed-keys.md) w operacjach asynchronicznych
- [Zarządzanie obszarami roboczymi log Analytics w](platform/manage-access.md) sekcjach Azure monitor zaktualizowanych dzienników niestandardowych.

### <a name="alerts"></a>Alerty

- [Reguły akcji dla alertów Azure monitor](platform/alerts-action-rules.md) — dodano film wideo.
- [Przegląd alertów i monitorowania powiadomień na platformie Azure](platform/alerts-overview.md) — wideo.

### <a name="application-insights"></a>Application Insights

- [Mapa aplikacji w usłudze Azure Application Insights](app/app-map.md) — dodano nazwę roli chmury dla agenta Java.
- [Dokumentacja interfejsu API agenta platformy Azure Application Insights platformy .NET](app/status-monitor-v2-api-reference.md) — Skonsolidowane odwołanie do interfejsu API.
- [Adresy IP używane przez Application Insights i log Analytics](app/ip-addresses.md) — zaktualizowane adresy IP dla usługi App Insights i interfejsów API log Analytics, elementów webhook grupy akcji, platformy Azure dla instytucji rządowych.
- [Monitoruj aplikacje Java w dowolnym miejscu](app/java-standalone-config.md) — nowy artykuł.
- [Monitoruj aplikacje Java w dowolnym środowisku](app/java-in-process-agent.md) — nowy artykuł.
- [Monitoruj aplikacje Java działające w dowolnym środowisku](app/java-standalone-arguments.md) — nowy artykuł.
- [Monitoruj aplikacje Java działające w środowisku lokalnym](app/java-on-premises.md) — nowy artykuł.
- [Usuń Application Insights w programie Visual Studio](app/remove-application-insights.md) — nowy artykuł.
- [Próbkowanie danych telemetrycznych w usłudze Azure Application Insights](app/sampling.md) — poprawka w przypadku stałej szybkości próbkowania w języku Python.

### <a name="containers"></a>Containers

- [Skonfiguruj platformę Azure Red Hat OpenShift v4. x z Azure monitor for Containers](insights/container-insights-azure-redhat4-setup.md) — nowy artykuł.
- [Ręczne rozwiązywanie problemów z synchronizacją usługi ServiceNow](platform/itsmc-resync-servicenow.md) — nowy artykuł.
- [Jak zatrzymać monitorowanie klastra platformy Azure i systemu Red Hat OpenShift v4](insights/container-insights-optout-openshift-v4.md) — nowy artykuł.
- [Jak zatrzymać monitorowanie klastra usługi Azure Red Hat OpenShift v3](insights/container-insights-optout-openshift-v3.md) — nowy artykuł.
- [Jak zatrzymać monitorowanie klastra hybrydowego Kubernetes](insights/container-insights-optout-hybrid.md) — nowy artykuł.

### <a name="insights"></a>Insights

- [Monitoruj magazyny kluczy Azure za pomocą Azure monitor dla magazynów kluczy (wersja zapoznawcza)](insights/key-vault-insights-overview.md) — nowy artykuł.

### <a name="logs"></a>Dzienniki

- [Azure monitor limity usług](service-limits.md) — dodano ograniczenie kwerendy użytkownika.
- [Zarządzanie użyciem i kosztami dla dzienników Azure monitor](platform/manage-cost-storage.md) — dodano rozliczanie dla klastrów dzienników. Dodano zapytanie Kusto, aby umożliwić klientom z starszą warstwą cenową na węzeł, aby określić, czy należy przenieść do warstwy rezerwacji na GB lub na pojemność.

### <a name="metrics"></a>Metryki

- [Zaawansowane funkcje usługi Azure Eksplorator metryk](platform/metrics-charts.md) — Dodano sekcję agregacji.

### <a name="workbooks"></a>Skoroszyty

- [Azure monitor skoroszyty i szablony Azure Resource Manager](platform/workbooks-automate.md) — dodano szablon Menedżer zasobów do wdrażania szablonu skoroszytu.

## <a name="march-2020"></a>Marzec 2020 r.

### <a name="general"></a>Ogólne

- [Przegląd Azure monitor](overview.md) — dodano Azure monitor wideo z omówieniem.
- [Azure monitor konfiguracja klucza zarządzanego przez klienta](platform/customer-managed-keys.md) — aktualizacje ogólne.
- [Azure monitor odwołanie do danych](/azure/azure-monitor/reference/) — Nowa witryna.

### <a name="alerts"></a>Alerty

- [Tworzenie i wyświetlanie alertów dziennika aktywności oraz zarządzanie nimi w Azure monitor](platform/alerts-activity-log.md) — dodatkowe wyjaśnienie Menedżer zasobów szablonu.
- [Informacje o tym, jak alerty metryk działają w Azure Monitor.](platform/alerts-metric-overview.md) — Zaktualizowano w celu wsparcia dla instytucji rządowych.
- [Rozwiązywanie problemów dotyczących alertów Azure monitor i powiadomień](platform/alerts-troubleshoot.md) — nowy artykuł.

### <a name="application-insights"></a>Application Insights

- [Automatyzacja Application Insights platformy Azure przy użyciu programu PowerShell](app/powershell.md) — dodano przykłady ARMClient.
- [Ciągły eksport danych telemetrycznych z Application Insights](app/export-telemetry.md) — Dodaj tabelę ze szczegółami struktury eksportu.
- [Włącz Snapshot debugger dla aplikacji .NET w Azure App Service](app/snapshot-debugger-appservice.md) dodanym przykładzie szablonu Menedżer zasobów.
- [Zarządzanie użyciem i kosztami dla Application Insights platformy Azure](app/pricing.md) — dodano informacje dotyczące alertu dotyczącego zakończenia danych.
- [Monitoruj aplikacje języka Python za pomocą Azure monitor (wersja zapoznawcza)](app/opencensus-python.md) — dodano metryki standardowe.
- [Obsługa mapy źródłowej dla aplikacji JavaScript — Azure Monitor Application Insights](app/source-map-support.md) — nowy artykuł.

### <a name="containers"></a>Containers

- [Azure monitor często zadawane pytania](faq.md) — aktualizacja Azure monitor dla kontenerów.
- [Konfigurowanie monitorowania procesora GPU przy użyciu Azure monitor dla kontenerów](insights/container-insights-gpu-monitoring.md) — nowy artykuł.

### <a name="insights"></a>Insights

- [Rozwiązanie do zarządzania pakietem Office 365 na platformie Azure](insights/solution-office-365.md) — Data wycofania aktualizacji.

### <a name="logs"></a>Dzienniki

- [Optymalizuj zapytania dzienników w Azure monitor](log-query/query-optimization.md) dodatkowym stanie procesora dla analizy XML i JSON.
- [Usuń i Odzyskaj rozwiązanie do rozwiązywania problemów z obszarem roboczym usługi Azure log Analytics](platform/delete-workspace.md) .
- [Korzystanie z dzienników Azure monitor z funkcją Azure Logic Apps i automatyzowania](platform/logicapp-flow-connector.md) — Zaktualizowano dla nowego łącznika Azure monitor.

### <a name="metrics"></a>Metryki

- [Wycofanie metryk dyskowych w Azure Portal](platform/portal-disk-metrics-deprecation.md) artykule.
- [Samouczek — Tworzenie wykresu metryk w](learn/tutorial-metrics-explorer.md) filmach wideo dodanych przez Azure monitor.

### <a name="platform-logs"></a>Dzienniki platformy

- [Zbieraj i Analizuj dziennik aktywności platformy Azure w Azure monitor](./platform/activity-log.md) — Zapisz ponownie, aby lepiej wyjaśnić zbieranie dziennika aktywności z ustawieniami diagnostycznymi.

### <a name="virtual-machines"></a>Maszyny wirtualne

- [Monitoruj usługę Azure Virtual Machines przy użyciu](insights/monitor-vm-azure.md) nowego artykułu Azure monitor.
- [Szybki Start: monitorowanie maszyn wirtualnych platformy Azure za pomocą Azure monitor](learn/quick-monitor-azure-vm.md) — zaktualizowano w celu dodania Azure monitor dla maszyn wirtualnych.
- [Alerty od Azure monitor dla maszyn wirtualnych](insights/vminsights-alerts.md) — nowy artykuł.
- [Włączenie Azure monitor dla maszyn wirtualnych przegląd](insights/vminsights-enable-overview.md) — zaktualizowane linki do pobrania agenta.

Ogólne aktualizacje dotyczące ogólnej dostępności Azure Monitor dla maszyn wirtualnych

- [Co to jest Azure Monitor dla maszyn wirtualnych?](insights/vminsights-overview.md)
- [Azure Monitor dla maszyn wirtualnych (GA) często zadawane pytania](insights/vminsights-ga-release-faq.md) 
- [Włączanie Azure Monitor dla maszyn wirtualnych przy użyciu Azure Policy](./insights/vminsights-enable-policy.md) 
- [Jak uzyskać Wykres wydajności przy użyciu Azure Monitor dla maszyn wirtualnych](insights/vminsights-performance.md)
- [Jak wykonywać zapytania dotyczące dzienników z Azure Monitor dla maszyn wirtualnych](insights/vminsights-log-search.md)
- [Wyświetlanie zależności aplikacji za pomocą Azure Monitor dla maszyn wirtualnych](insights/vminsights-maps.md) 

### <a name="visualizations"></a>Wizualizacje

- [Wizualizowanie danych z Azure monitor](visualizations.md) — zaktualizowano w celu zanotowania planowanego zaniechania projektanta widoków.

## <a name="february-2020"></a>Luty 2020 r.

### <a name="agents"></a>Agenci

Wiele aktualizacji w ramach ponownego zapisywania zawartości rozszerzenia diagnostyki.

- Omówienie tabel z restrukturyzacją [agentów monitorowania platformy Azure](platform/agents-overview.md) w celu lepszego wyjaśnienia unikatowych funkcji poszczególnych agentów.
- [Przegląd rozszerzenia Diagnostyka Azure](platform/diagnostics-extension-overview.md) — ukończono ponowne zapisywanie.
- [Używaj usługi BLOB Storage dla usług IIS i magazynu tabel dla zdarzeń w Azure monitor](platform/diagnostics-extension-logs.md) — ogólne ponowne zapisywanie dla aktualizacji i przejrzystości.
- [Zainstaluj i skonfiguruj rozszerzenie Microsoft Azure Diagnostics (funkcji wad)](platform/diagnostics-extension-windows-install.md) — nowy artykuł. 
- [Schemat rozszerzenia diagnostyki systemu Windows](platform/diagnostics-extension-schema-windows.md) — Reorganizuje.
- [Wyślij dane z rozszerzenia Diagnostyka systemu Windows Azure do platformy Azure Event Hubs](platform/diagnostics-extension-stream-event-hubs.md) — całkowicie ponownie napisano i Zaktualizowano.
- [Przechowywanie i przeglądanie danych diagnostycznych w usłudze Azure Storage](../cloud-services/diagnostics-extension-to-storage.md) — całkowicie ponownie zapisane i zaktualizowane.
- [Log Analytics rozszerzenie maszyny wirtualnej dla systemu Windows](../virtual-machines/extensions/oms-windows.md) — lepszym rozwiązaniem jest powiększanie relacji z agentem log Analytics.
- [Azure monitor rozszerzenie maszyny wirtualnej dla systemu Linux](../virtual-machines/extensions/oms-linux.md) — lepsza relacja z programem log Analytics Agent.

### <a name="application-insights"></a>Application Insights

- [Parametry połączenia w usłudze Azure Application Insights](app/sdk-connection-string.md) — nowy artykuł.

### <a name="insights-and-solutions"></a>Szczegółowe informacje i rozwiązania

#### <a name="azure-monitor-for-containers"></a>Usługa Azure Monitor dla kontenerów

- [Integruj Azure Active Directory z usługą Azure Kubernetes Service](../aks/azure-ad-integration-cli.md) — dodano uwagę na potrzeby tworzenia aplikacji klienckiej do obsługi klastra z obsługą RBAC Kubernetes w celu obsługi Azure monitor kontenerów.

#### <a name="azure-monitor-for-vms"></a>Usługa Azure Monitor dla maszyn wirtualnych

- [Azure monitor dla maszyn wirtualnych (ga) — często zadawane pytania](insights/vminsights-ga-release-faq.md) — Zmień sposób przechowywania danych wydajności.

#### <a name="office-365"></a>Office 365

- [Rozwiązanie do zarządzania pakietem Office 365 na platformie Azure](insights/solution-office-365.md) — Data wycofania aktualizacji.


### <a name="logs"></a>Dzienniki

- [Optymalizowanie zapytań dzienników w Azure monitor](log-query/query-optimization.md) — nowy artykuł.
- [Zarządzanie użyciem i kosztami dzienników Azure monitor](platform/manage-cost-storage.md) — ulepszone przykładowe zapytania ułatwiające zrozumienie użycia.

### <a name="metrics"></a>Metryki

- [Azure monitor metryki platformy eksportowane za pośrednictwem ustawień diagnostycznych](platform/metrics-supported-export-diagnostic-settings.md) — Dodano sekcję w temacie Zmiana na zachowanie dla wartości null i zero.

### <a name="visualizations"></a>Wizualizacje

Wiele nowych artykułów do przewodnika po konwersji projektanta widoków na skoroszyty.

- [Przewodnik po przejściu do programu Azure monitor View Designer do skoroszytów](platform/view-designer-conversion-overview.md) — nowy artykuł.
- [Azure monitor widoku projektanta na skoroszyty opcje konwersji](platform/view-designer-conversion-options.md) — nowy artykuł.
- [Azure monitor widoków Projektant do skoroszytów konwersje kafelków](platform/view-designer-conversion-tiles.md) — nowy artykuł.
- [Azure monitor widoku projektanta na skoroszyty podsumowanie konwersji i dostęp do](platform/view-designer-conversion-access.md) nowego artykułu.
- [Azure monitor widoków Projektant do skoroszytów typowych zadań konwersji](platform/view-designer-conversion-tasks.md) — nowy artykuł.
- [Przykłady konwersji Azure monitor View na skoroszyty](platform/view-designer-conversion-examples.md) — nowy artykuł.

## <a name="january-2020"></a>Styczeń 2020 r.

### <a name="general"></a>Ogólne

- [Co jest monitorowane przez Azure Monitor?](monitor-reference.md) — Nowy artykuł.

### <a name="agents"></a>Agenci

- [Zbierz dane dziennika za pomocą usługi Azure log Analytics Agent](platform/log-analytics-agent.md) — Zaktualizowano tabelę wymagań zapory sieciowej.

### <a name="alerts"></a>Alerty

- [Tworzenie grup akcji i zarządzanie nimi w ustawieniach Azure Portal](platform/action-groups.md) , które zostały usunięte dla funkcji w wersji 2, które nie są już wymagane.
- [Utwórz alert metryki z przykładem Menedżer zasobów szablonu](platform/alerts-metric-create-templates.md) dodanym jako parametr *ignoreDataBefore* .  Dodano ograniczenia dotyczące reguł wielokryteriów.
- [Za pomocą log Analytics alert API REST](platform/api-alerts.md) — Poprawiono przykład JSON.

### <a name="application-insights"></a>Application Insights

- [Adresy IP używane przez Application Insights i log Analytics](app/ip-addresses.md) — Zaktualizowano sekcję Test dostępności wraz z dodawaniem reguły portów przychodzących w celu zezwalania na ruch przy użyciu sieciowych grup zabezpieczeń platformy Azure.
- [Rozwiązywanie problemów z usługą Azure Application Insights Profiler](app/profiler-troubleshooting.md) — zaktualizowane ogólne Rozwiązywanie problemów.
- [Próbkowanie danych telemetrycznych na platformie Azure Application Insights](app/sampling.md) — zaktualizowane i rozbudowane w celu zwiększenia czytelności na podstawie opinii klientów.

### <a name="data-security"></a>Bezpieczeństwo danych

- [Azure monitor konfiguracja klucza zarządzanego przez klienta](platform/customer-managed-keys.md) — nowy artykuł.

### <a name="insights-and-solutions"></a>Szczegółowe informacje i rozwiązania

#### <a name="azure-monitor-for-containers"></a>Usługa Azure Monitor dla kontenerów

- [Konfigurowanie Azure monitor do zbierania danych przez agentów kontenerów](insights/container-insights-agent-config.md) — dodano szczegóły dotyczące uaktualniania agenta na platformie Azure Red Hat OpenShift oraz dodano dodatkowe informacje w celu odróżnienia metod uaktualnienia agenta.
- [Utwórz alerty wydajności dla Azure monitor](./insights/container-insights-log-alerts.md) informacje o zaktualizowanych kontenerach i zaktualizowanych krokach w celu utworzenia alertu dotyczącego danych wydajności przechowywanych w obszarze roboczym przy użyciu alertów kontekstu obszaru roboczego.
- [Kubernetes monitorowanie za pomocą Azure monitor dla kontenerów](insights/container-insights-analyze.md) — Zaktualizowano artykuł z omówieniem i artykuł dotyczący analizy w celu obsługi klastrów Windows Kubernetes.
- [Skonfiguruj klastry usługi Azure Red Hat OpenShift za pomocą Azure monitor dla kontenerów](insights/container-insights-azure-redhat-setup.md) — dodano szczegóły dotyczące uaktualniania agenta na platformie Azure Red Hat OpenShift i dodano dodatkowe informacje w celu odróżnienia metod uaktualnienia agenta.
- [Skonfiguruj hybrydowe klastry Kubernetes z Azure monitor dla kontenerów](insights/container-insights-hybrid-setup.md) — zaktualizowano w celu odzwierciedlenia dodanej obsługi bezpiecznego portu: 10250 z cAdvisor Kubelet.
- [Jak zarządzać Azure Monitorą dla agenta kontenerów](insights/container-insights-manage-agent.md) — Zaktualizowano szczegółowe informacje związane z zachowaniem i konfiguracją wycinków metryk z usługą Azure Red Hat OpenShift w porównaniu z innymi typami klastrów Kubernetes.
- [Konfigurowanie Azure monitor na potrzeby integracji z integracją Prometheus](insights/container-insights-prometheus-integration.md) — Zaktualizowano szczegółowe informacje związane z zachowaniem i konfiguracją wycinków metryk z usługą Azure Red Hat OpenShift w porównaniu z innymi typami klastrów Kubernetes.
- [Jak zaktualizować Azure monitor kontenerów dla metryk](insights/container-insights-update-metrics.md) — Zaktualizowano szczegóły dotyczące zachowania i konfiguracji odporności metryk z usługą Azure Red Hat OpenShift w porównaniu z innymi typami klastrów Kubernetes.

#### <a name="azure-monitor-for-vms"></a>Usługa Azure Monitor dla maszyn wirtualnych

- [Azure monitor dla maszyn wirtualnych (ga) — często zadawane pytania](insights/vminsights-ga-release-faq.md) — dodano informacje dotyczące uaktualniania obszaru roboczego i agentów do nowej wersji.

#### <a name="office-365"></a>Office 365

- [Rozwiązanie do zarządzania pakietem office 365 na platformie Azure](insights/solution-office-365.md) — dodano szczegóły i często zadawane pytania dotyczące migracji do rozwiązania pakietu Office 365 na platformie Azure. Usunięto sekcję dołączania.

### <a name="logs"></a>Dzienniki

- [Zarządzanie obszarami roboczymi log Analytics w programie Azure monitor](platform/manage-access.md) — aktualizacje nie są wykonywane.
- [Zarządzanie użyciem i kosztami Azure monitor dzienników](platform/manage-cost-storage.md) — dodano wyjaśnienie dotyczące obliczania ilości danych w sekcji model cen.
- [Użyj szablonów Azure Resource Manager, aby utworzyć i skonfigurować szablon log Analytics obszaru roboczego](./samples/resource-manager-workspace.md) zaktualizowany przy użyciu nowych warstw cenowych.

### <a name="platform-logs"></a>Dzienniki platformy

- [Zbierz dziennik aktywności platformy Azure z ustawieniami diagnostycznymi — Azure monitor](./platform/activity-log.md) — dodatkowe informacje na temat zmienionych właściwości.
- [Eksportowanie dziennika aktywności platformy Azure](./platform/activity-log.md#legacy-collection-methods) — zaktualizowano w celu wprowadzenia zmian w interfejsie użytkownika. 

## <a name="december-2019"></a>Grudzień 2019 r.

### <a name="agents"></a>Agenci

- [Połącz komputery z systemem Linux, aby Azure monitor](platform/agent-linux.md) — nowy artykuł.

### <a name="alerts"></a>Alerty

- [Utwórz alert metryki za pomocą szablonu Menedżer zasobów](platform/alerts-metric-create-templates.md) dodanego przykładu dla metryki niestandardowej.
- [Tworzenie alertów z progami dynamicznymi w sekcji Azure monitor](platform/alerts-dynamic-thresholds.md) dodano w celu interpretacji wykresów progów dynamicznych.
- [Przegląd alertów i monitorowania powiadomień na platformie Azure](platform/alerts-overview.md) — zaktualizowane zapytanie dotyczące grafu zasobów.
- [Obsługiwane zasoby dla alertów metryk w Azure monitor](platform/alerts-metric-near-real-time.md) -Update do obsługiwanych metryk i wymiarów.
- [Przełączenie się ze starszej wersji interfejsu API alertów usługi log Analytics na nową funkcję API alertów platformy Azure](platform/alerts-log-api-switch.md) — dodano uwagę na zmodyfikowaną nazwę
- [Informacje o tym, jak alerty metryk działają w Azure Monitor.](platform/alerts-metric-overview.md) -Dodano obsługiwane typy zasobów do monitorowania w odpowiedniej skali.

### <a name="application-insights"></a>Application Insights

- [Application Insights dla aplikacji usługi Worker (aplikacje inne niż http)](app/worker-service.md) — dodano domyślny poziom rejestrowania do kodu w języku C#. Zaktualizowana wersja odwołania do pakietu.
- [ OdwołanieApplicationInsights.config —](app/configuration-with-applicationinsights-config.md) kod przykładowy zaktualizowany przez platformę Azure.
- [Automatyzacja Application Insights platformy Azure przy użyciu programu PowerShell](app/powershell.md) — aktualizacja do Menedżer zasobów szablonu.
- [Utwórz nową usługę Azure Application Insights Resource](app/create-new-resource.md) — Uwaga dodana do globalnie unikatowej nazwy.
- [Diagnozuj przy użyciu Live Metrics Stream platformy Azure Application Insights](app/live-stream.md) — Zaktualizowano wymagania dotyczące wersji zestawu SDK ASP.NET Core.
- [Liczniki zdarzeń w Application Insights](app/eventcounters.md) — Zaktualizowano kategorię i tabelę do customMetrics.
- [Poznaj dzienniki śledzenia języka Java na platformie Azure Application Insights](app/java-trace-logs.md) — dodano konfigurację dla progu rejestrowania agenta Java.
- [Adresy IP używane przez Application Insights i log Analytics](app/ip-addresses.md) — zaktualizowane adresy ip dla Live Metrics Stream.
- [Monitorowanie wydajności usług Azure App Services](app/azure-web-apps.md) — dodano obsługę ASP.NET Core 3,0. 
- [Monitoruj aplikacje języka Python za pomocą Azure monitor (wersja zapoznawcza)](app/opencensus-python.md) — dodaliśmy wyjaśnienie dla mapowania schematu OpenCensus języka Python na platformę Azure. Monitoruj schemat
- [Informacje o wersji dla platformy Azure Application Insights](app/release-notes.md) — notatki dodane dla starszych wersji.
- [Kanały telemetrii na platformie Azure Application Insights](app/telemetry-channels.md) — zaktualizowany czas trwania odrzuconych danych podczas dłuższego okresu utraconych połączeń.
- [Próbkowanie danych telemetrycznych w Application Insights platformy Azure](app/sampling.md) — poprawione fragmenty kodu dla niestandardowej TelemetryInitializer.
- [Rozwiązywanie problemów Application Insights w projekcie sieci Web w języku Java](app/java-troubleshoot.md) — usunięto instrukcję dotyczącą braku obsługi kolekcji zależności w programie JDK 9.

### <a name="insights-and-solutions"></a>Szczegółowe informacje i rozwiązania

- [Azure monitor for Containers — często zadawane pytania](./faq.md) — pytanie dotyczące pól obrazu i nazwy.
- [Azure SQL Analytics rozwiązanie w Azure monitor](insights/azure-sql.md) — zaktualizowana baza danych czeka na obsługę wystąpienia zarządzanego.
- [Skonfiguruj Azure monitor dla ustawienia Dodawanie kolekcji danych agenta kontenerów](insights/container-insights-agent-config.md) dla enrich_container_logs.
- [Skonfiguruj hybrydowe klastry Kubernetes za pomocą Azure monitor dla kontenerów](insights/container-insights-hybrid-setup.md) — Dodano sekcję rozwiązywania problemów.
- [Monitoruj Active Directory stan replikacji z](insights/ad-replication-status.md) aktualizacją wymagań wstępnych Azure monitor .NET Framework.
- [Network Performance Monitor rozwiązanie na platformie Azure](insights/network-performance-monitor.md) — dodano Obsługiwane regiony.
- [Zoptymalizuj środowisko Active Directory z](insights/ad-assessment.md) aktualizacją wymagań wstępnych Azure monitor .NET Framework.
- [Zoptymalizuj środowisko SQL Server z](insights/sql-assessment.md) aktualizacją wymagań wstępnych Azure monitor .NET Framework.
- [Zoptymalizuj środowisko System Center Operations Manager za pomocą usługi Azure log Analytics](insights/scom-assessment.md) -.NET Framework aktualizacji wymagań wstępnych.
- [Obsługiwane połączenia z łącznik zarządzania usługami IT na platformie Azure log Analytics](platform/itsmc-connections.md) — dodano Nowy Jork do wstępnie wymaganego identyfikatora klienta i klucza tajnego klienta.

### <a name="logs"></a>Dzienniki

- [Usuń i Odzyskaj metodę programu PowerShell dla usługi Azure log Analytics Workspace](platform/delete-workspace.md) .
- [Projektowanie Azure Monitorych dzienników](platform/design-logs-deployment.md) — szybkość pozyskiwania dla obszaru roboczego zwiększyła się.

### <a name="metrics"></a>Metryki

- [Metryki platformy Azure monitor eksportowane za pośrednictwem ustawień diagnostycznych](platform/metrics-supported-export-diagnostic-settings.md) — nowy artykuł.

### <a name="platform-logs"></a>Dzienniki platformy

Zaktualizowano wiele artykułów w ramach restrukturyzacji zawartości dla dzienników platformy w oparciu o nową funkcję konfigurowania dziennika aktywności przy użyciu ustawień diagnostycznych.

- [Archiwizuj dzienniki zasobów platformy Azure na koncie magazynu](./platform/resource-logs.md#send-to-azure-storage)
- [Schemat zdarzeń dziennika aktywności platformy Azure](platform/activity-log-schema.md)
- [Limity usługi Azure Monitor](service-limits.md)
- [Zbieranie i analizowanie dzienników aktywności platformy Azure w obszarze roboczym Log Analytics](./platform/activity-log.md)
- [Zbierz dziennik aktywności platformy Azure przy użyciu ustawień diagnostycznych (wersja zapoznawcza) — Azure Monitor](./platform/activity-log.md)
- [Zbieranie dzienników aktywności platformy Azure w obszarze roboczym Log Analytics w ramach dzierżawców platformy Azure](./platform/activity-log.md)
- [Zbieranie dzienników zasobów platformy Azure w obszarze roboczym Log Analytics](./platform/resource-logs.md#send-to-log-analytics-workspace)
- [Tworzenie ustawień diagnostycznych na platformie Azure przy użyciu szablonu Menedżer zasobów](./samples/resource-manager-diagnostic-settings.md)
- [Tworzenie ustawień diagnostycznych w celu zbierania dzienników i metryk na platformie Azure](platform/diagnostic-settings.md)
- [Eksportowanie dziennika aktywności platformy Azure](./platform/activity-log.md#legacy-collection-methods)
- [Omówienie dzienników platformy Azure](platform/platform-logs-overview.md)
- [Przesyłanie strumieniowe danych monitorowania platformy Azure do centrum zdarzeń](platform/stream-monitoring-data-event-hubs.md)
- [Przesyłanie strumieniowe dzienników platformy Azure do centrum zdarzeń](./platform/resource-logs.md#send-to-azure-event-hubs)

### <a name="quickstarts-and-tutorials"></a>Przewodniki Szybki start i samouczki

- [Utwórz wykres metryk w Azure monitor](learn/tutorial-metrics-explorer.md) — nowy artykuł.
- [Zbierz dzienniki zasobów z zasobów platformy Azure i Analizuj je za pomocą Azure monitor](learn/tutorial-resource-logs.md) — nowy artykuł.
- [Monitoruj zasób platformy Azure za pomocą Azure monitor](learn/quick-monitor-azure-resource.md) — nowy artykuł.
   
## <a name="next-steps"></a>Następne kroki

- Jeśli chcesz współtworzyć dokumentację Azure Monitor, zobacz [Przewodnik współautora dokumentów](/contribute/).