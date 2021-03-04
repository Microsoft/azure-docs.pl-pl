---
title: Co nowego w dokumentacji Azure Monitor
description: Ważne aktualizacje dokumentacji Azure Monitor aktualizowane co miesiąc.
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 02/10/2021
ms.openlocfilehash: dd6c44587ce3f4e2b5de940ef831a20a4079c4ef
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102051926"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>Co nowego w dokumentacji Azure Monitor?

Ten artykuł zawiera listę artykułów Azure Monitor, które są nowe lub zostały znacząco zaktualizowane. Zostanie odświeżony pierwszy tydzień każdego miesiąca, aby uwzględnić aktualizacje artykułów z poprzedniego miesiąca.

## <a name="january-2021"></a>Styczeń 2021 r. 

### <a name="general"></a>Ogólne 
- [Azure monitor często zadawane pytania](faq.md) — dodano wpis na informacje o urządzeniu dla Application Insights.
### <a name="agents"></a>Agenci  
- [Zbieranie zdarzeń śledzenia zdarzeń systemu Windows (ETW) dla dzienników Azure monitor analizy](./agents/data-sources-event-tracing-windows.md) — nowy artykuł.
- [Reguły zbierania danych w Azure monitor (wersja zapoznawcza)](./agents/data-collection-rule-overview.md) — dodano linki do przykładów programu PowerShell i interfejsu wiersza polecenia.

### <a name="alerts"></a>Alerty  
- [Skonfiguruj platformę Azure do łączenia narzędzi Narzędzia ITSM przy użyciu opcji Bezpieczny eksport](./alerts/itsm-connector-secure-webhook-connections-azure-configuration.md) — nowy artykuł.
- [Błędy stanu łącznika na pulpicie nawigacyjnym ITSMC](./alerts/itsmc-dashboard-errors.md) — nowy artykuł.
- [Zbadaj błędy za pomocą pulpitu nawigacyjnego ITSMC](./alerts/itsmc-dashboard.md) — nowy artykuł.
- [Rozwiązywanie problemów z alertami metryki platformy Azure](./alerts/alerts-troubleshoot-metric.md) — dodano sekcje dla progów dynamicznych.
- [Rozwiązywanie problemów z łącznik zarządzania usługami IT](./alerts/itsmc-troubleshoot-overview.md) — nowy artykuł.

### <a name="application-insights"></a>Application Insights
- [Korelacja telemetrii Application Insights platformy Azure](app/correlation.md) — dodano korelację śledzenia, gdy jeden moduł wywołuje inne środowisko w języku Python OpenCensus.
- [Application Insights dla stron sieci Web](app/javascript.md) — nowy artykuł.
- [Kliknij opcję wtyczka Autocollection Analytics dla Application Insights JavaScript SDK](app/javascript-click-analytics-plugin.md) — nowy artykuł.
- [Monitoruj aplikacje bez zmian w kodzie — funkcja autoinstrumentation dla Azure Monitor Application Insights](app/codeless-overview.md) dodanej kolumny języka Python.
- [Zareaguj na wtyczkę dla Application Insights JavaScript SDK](app/javascript-react-plugin.md) — nowy artykuł.
- [Procesory telemetrii (wersja zapoznawcza) — Azure Monitor Application Insights dla języka Java](app/java-standalone-telemetry-processors.md) — Zapisano ponownie.
- [Analiza użycia za pomocą usługi Azure Application Insights](app/usage-overview.md) — nowy artykuł.
- [Użyj analizy zmian aplikacji w Azure monitor, aby znaleźć problemy z aplikacją sieci Web](app/change-analysis.md) — dodano błąd messges.


### <a name="insights"></a>Insights    
- [Azure monitor dla usługi Azure Eksplorator danych (wersja zapoznawcza)](insights/data-explorer.md) — nowy artykuł.

### <a name="logs"></a>Dzienniki    
- [Azure monitor klucz zarządzany przez klienta](./logs/customer-managed-keys.md) — wprowadź tożsamość zarządzaną przypisaną przez użytkownika.
- [Azure monitor rejestruje dedykowane klastry](./logs/logs-dedicated-clusters.md) — zaktualizowany kod odpowiedzi.
- [Zapytanie dotyczące wielu usług — Azure monitor i Azure Eksplorator danych (wersja zapoznawcza)](/azure/azure-monitor/platform/azure-data-explorer-monitor-cross-service-query) — nowy artykuł.

### <a name="metrics"></a>Metryki
- [Azure monitor agregacji metryk i wyświetlaj wyjaśnienie](./essentials/metrics-aggregation-explained.md) — nowy artykuł.

### <a name="platform-logs"></a>Dzienniki platformy
- [Azure monitor dzienniki zasobów obsługiwane usługi i kategorie](./essentials/resource-logs-categories.md) — nowy artykuł.

### <a name="visualizations"></a>Wizualizacje
- [Azure monitor skoroszyty ze źródłami danych](./visualize/workbooks-data-sources.md) — dodano analizę scalania i zmian.


## <a name="december-2020"></a>Grudzień 2020 r.

### <a name="general"></a>Ogólne
- [Azure monitor](logs/customer-managed-keys.md) komunikaty o błędach, które zostały dodane przez klienta.
- [Partnerzy, którzy integrują się z](partners.md) sekcją dodaną Azure monitor w ramach integracji centrum zdarzeń.

### <a name="agents"></a>Agenci
- [Zapytanie dotyczące wielu zasobów Eksplorator danych platformy Azure przy użyciu Azure monitor](logs/azure-monitor-data-explorer-proxy.md) — nowy artykuł.
- [Omówienia agentów monitorowania platformy Azure](agents/agents-overview.md) — dodano obsługę systemu Oracle 8.

### <a name="alerts"></a>Alerty
- [Rozwiązywanie problemów z alertami metryki platformy Azure](alerts/alerts-troubleshoot-metric.md) — dodano Rozwiązywanie problemów z progami dynamicznymi.
- [Łącznik zarządzania usługami IT w log Analytics](alerts/itsmc-definition.md) — nowy artykuł.
- [Przegląd łącznik zarządzania usługami IT](alerts/itsmc-overview.md) — informacje dotyczące rozwiązywania problemów z restrukturyzacją.
- [Połącz Cherwell łącznik zarządzania usługami IT z](alerts/itsmc-connections-cherwell.md) nowym artykułem.
- [Połącz Provance łącznik zarządzania usługami IT z](alerts/itsmc-connections-provance.md) nowym artykułem.
- [Połącz SCSM łącznik zarządzania usługami IT z](alerts/itsmc-connections-scsm.md) nowym artykułem.
- [Połącz usługi servicenow łącznik zarządzania usługami IT z](alerts/itsmc-connections-servicenow.md) nowym artykułem.
- [Ręczne rozwiązywanie problemów z synchronizacją usługi ServiceNow](alerts/itsmc-resync-servicenow.md) — informacje dotyczące rozwiązywania problemów ze strukturą.




### <a name="application-insights"></a>Application Insights
- [Application Insights platformy Azure dla aplikacji sieci Web JavaScript](app/javascript.md) — dodano konfigurację parametrów połączenia.
- [Azure Application Insights Standard Metrics](app/standard-metrics.md) — nowy artykuł.
- [Azure Monitor Application Insights Java](app/java-in-process-agent.md) — dodatkowe informacje na temat wysyłania niestandardowych danych telemetrycznych z aplikacji.
- [Ciągły eksport danych telemetrycznych z](app/export-telemetry.md) wyeksportowanych ustawień diagnostycznych opartych na Application Insights.
- [Włącz Snapshot debugger dla aplikacji .NET i .NET Core w Azure Functions](app/snapshot-debugger-function-app.md) — nowy artykuł.
- [Adresy IP używane przez Application Insights i log Analytics](app/ip-addresses.md) dodanych adresów ip do Azure Government.
- [Rozwiązywanie problemów z usługą Azure Application Insights Profiler](app/profiler-troubleshooting.md) — dodano informacje na stronie stanu rozszerzenia witryny usług diagnostycznych.
- [Rozwiązywanie problemów z testami dostępności Application Insights platformy Azure](app/troubleshoot-availability.md) — aktualizacje dotyczące rozwiązywania problemów z testami ping.
- [Rozwiązywanie problemów z Azure Monitor Application Insights dla języka Java](app/java-standalone-troubleshoot.md) — nowy artykuł.

### <a name="containers"></a>Kontenery
- [Raporty w usłudze Container Insights](insights/container-insights-reports.md) — nowy artykuł.

### <a name="logs"></a>Dzienniki
- [Azure monitor rejestruje dedykowane klastry](logs/logs-dedicated-clusters.md) — dodano automatyczne polecenia, metody do rozłączenia i usunięcia oraz rozwiązywania problemów.
- [Kwerenda między usługami Azure monitor i Azure Eksplorator danych (wersja zapoznawcza)](logs/azure-data-explorer-monitor-cross-service-query.md) — nowy artykuł.
- [Log Analytics eksportu danych obszaru roboczego w Azure monitor (wersja zapoznawcza)](logs/logs-data-export.md) — Dodano szablony ARM.

### <a name="metrics"></a>Metryki
- [Zaawansowane funkcje Eksplorator metryk platformy Azure](essentials/metrics-charts.md) — dodano informacje o selektorze zakresu zasobów.
- [Wyświetlanie wielu zasobów w Eksplorator metryk](essentials/metrics-dynamic-scope.md) — nowy artykuł.

### <a name="networks"></a>Sieci
- [Rozwiązanie do analizy sieci platformy Azure w Azure monitor](insights/azure-networking-analytics.md) — dodano informacje o skoroszycie usługi Network Insights.

### <a name="virtual-machines"></a>Virtual Machines
- [Włącz Azure monitor dla środowiska hybrydowego](vm/vminsights-enable-hybrid.md) — Nowa wersja agenta zależności.


### <a name="visualizations"></a>Wizualizacje
- [Azure monitor wizualizacji mapy skoroszytu](visualize/workbooks-map-visualizations.md) — nowy artykuł.
- [Azure monitor skoroszyty](visualize/workbooks-bring-your-own-storage.md) przedstawiają swój własny magazyn — nowy artykuł.


## <a name="november-2020"></a>Listopad 2020 r.

### <a name="general"></a>Ogólne
- [Limity usługi Azure monitor](service-limits.md) — zaktualizowane dla pomocy technicznej usługi Azure Arc.

### <a name="agents"></a>Agenci
- [Omówienie agentów monitorowania platformy Azure](agents/agents-overview.md) — Zaktualizowano obsługę usługi Azure Arc.
- [Zainstaluj agenta Azure monitor](agents/azure-monitor-agent-install.md) — nowy artykuł.
- [Omówienie agenta Azure monitor](agents/azure-monitor-agent-overview.md) — Zaktualizowano obsługę usługi Azure Arc.
- [Przykłady szablonów Menedżer zasobów dla agentów](agents/resource-manager-agent.md) — zaktualizowane dla pomocy technicznej usługi Azure Arc.

### <a name="alerts"></a>Alerty
- [Utwórz grupy akcji i zarządzaj nimi w Azure Portal](alerts/action-groups.md) źródłowych adresów IP dla elementów webhook.

### <a name="application-insights"></a>Application Insights
- [Monitorowanie aplikacji bezkodowej języka Java Azure Monitor Application Insights](app/java-in-process-agent.md) dodano przykład konfiguracji.
- [Zareaguj na wtyczkę dla Application Insights zestawu SDK języka JavaScript](app/javascript-react-plugin.md) — dodana sekcja przy użyciu punktów zaczepienia.
- [Uaktualnianie z Application Insights Java 2. x SDK](app/java-standalone-upgrade-from-2x.md) — nowy artykuł.
- [Informacje o wersji dla Microsoft. ApplicationInsights. SnapshotCollector](app/snapshot-collector-release-notes.md) — nowy artykuł.

### <a name="autoscale"></a>Automatyczne skalowanie
- [Rozpocznij pracę z funkcją automatycznego skalowania na platformie Azure](autoscale/autoscale-get-started.md) — Dodano sekcję na temat przesuwania skalowania automatycznego do innego regionu.

### <a name="data-collection"></a>Zbieranie danych
- [Skonfiguruj zbieranie danych dla agenta Azure monitor (wersja zapoznawcza)](agents/data-collection-rule-azure-monitor-agent.md) — Zaktualizowano pod kątem obsługi usługi Azure Arc.
- [Reguły zbierania danych w Azure monitor (wersja zapoznawcza)](agents/data-collection-rule-overview.md) — Zaktualizowano dla pomocy technicznej usługi Azure Arc.
- [Przykłady szablonów Menedżer zasobów dla reguł zbierania danych](agents/resource-manager-data-collection-rules.md) — nowy artykuł.

### <a name="insights-and-solutions"></a>Szczegółowe informacje i rozwiązania
- [Połącz platformę Azure z narzędziami narzędzia ITSM przy użyciu opcji bezpiecznego eksportu,](alerts/it-service-management-connector-secure-webhook-connections.md) Aby dołączać do usługi ServiceNow.

### <a name="logs"></a>Dzienniki
- [Integruj log Analytics i Excel](logs/log-excel.md) — nowy artykuł.
- Sekcja [log Analytics dodano zabezpieczenia danych](logs/data-security.md) w dodatkowych funkcjach zabezpieczeń.
- [Integracja log Analytics z](logs/log-powerbi.md) nowym artykułem Power BI.
- [Kolumny standardowe w Azure monitor rekordy dziennika](logs/log-standard-columns.md) — dodano _SubscriptionId kolumnę.

Nowe i zaktualizowane artykuły z restrukturyzacji zawartości zapytania dziennika.

- [Samouczek usługi Log Analytics](logs/log-analytics-tutorial.md)
- [Rejestruj zapytania w Azure Monitor](logs/log-query-overview.md)
- [Omówienie Log Analytics w Azure Monitor](logs/log-analytics-overview.md)
- [Przykłady zapytań dotyczących usługi Azure Eksplorator danych i Azure Monitor](/azure/data-explorer/kusto/query/samples?pivots=azuremonitor)
- [Samouczek: używanie zapytań Kusto w usłudze Azure Eksplorator danych i Azure Monitor](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor)



### <a name="virtual-machines"></a>Maszyny wirtualne

- [Omówienie włączania usługi VM Insights](vm/vminsights-enable-overview.md) — dodano Obsługiwane regiony.

Nowe artykuły dotyczące kondycji gościa usługi VM Insights (wersja zapoznawcza)

- [Kondycja gościa usługi VM Insights (wersja zapoznawcza)](vm/vminsights-health-overview.md)
- [Alerty dotyczące kondycji gościa usługi VM Insights (wersja zapoznawcza)](vm/vminsights-health-alerts.md)
- [Konfigurowanie monitorowania w usłudze VM Insights (wersja zapoznawcza)](vm/vminsights-health-configure.md)
- [Konfigurowanie monitorowania w usłudze VM Insights gość przy użyciu reguł zbierania danych (wersja zapoznawcza)](vm/vminsights-health-configure-dcr.md)
- [Włącz kondycję gościa usługi VM Insights (wersja zapoznawcza)](vm/vminsights-health-enable.md)
- [Rozwiązywanie problemów z kondycją gościa usługi VM Insights (wersja zapoznawcza)](vm/vminsights-health-troubleshoot.md)





## <a name="october-2020"></a>Październik 2020 r.

### <a name="general"></a>Ogólne
- [Wycofanie interfejsu API Azure monitor](logs/operationalinsights-api-retirement.md) — nowy artykuł.

### <a name="agents"></a>Agenci
- [Co jest monitorowane według sekcji Azure monitor](monitor-reference.md) dodanej do agentów.

### <a name="alerts"></a>Alerty
- [Tworzenie grup akcji i zarządzanie nimi w sekcji Azure Portal](alerts/action-groups.md) dodanej przez tag usługi.
- [Przykłady szablonów Menedżer zasobów dla alertów metryk](alerts/resource-manager-alerts-metric.md) — dodano parametr dopasowania zawartości i lokalizacje testów.
- [Rozwiązywanie problemów z alertami metryk platformy Azure](alerts/alerts-troubleshoot-metric.md) — dodano najlepsze rozwiązanie dotyczące konfiguracji reguł.

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
- [Rozpocznij pracę z funkcją automatycznego skalowania na platformie Azure](autoscale/autoscale-get-started.md) — Dodano sekcję na temat przesuwania skalowania automatycznego do innego regionu.

### <a name="containers"></a>Kontenery
- [Skonfiguruj monitorowanie PV za pomocą usługi Container Insights](containers/container-insights-persistent-volumes.md) — nowy artykuł.
- [Jak zarządzać obsługą agenta usługi Container Insights](containers/container-insights-manage-agent.md) — dodano obsługę klastra Kubernetes z włączoną funkcją Azure Arc.
- [Alerty metryk z usługi Container Insights](containers/container-insights-metric-alerts.md) — dodano obsługę klastra Kubernetes z włączoną funkcją Azure Arc.

### <a name="insights-and-solutions"></a>Szczegółowe informacje i rozwiązania
- [Łącznik zarządzania usługami IT — Bezpieczny eksport w sekcji Azure monitor](alerts/it-service-management-connector-secure-webhook-connections.md) dodanej do usługi ServiceNow.

### <a name="logs"></a>Dzienniki
- [Archiwizowanie danych z obszaru roboczego log Analytics w usłudze Azure Storage przy użyciu aplikacji logiki](logs/logs-export-logic-app.md) — nowy artykuł.
- [Log Analytics eksportu danych obszaru roboczego w Azure monitor (wersja zapoznawcza)](logs/logs-data-export.md) — dodaliśmy Przykładowa treść dla żądania REST dla centrum zdarzeń.
- [Zarządzanie użyciem i kosztami Azure monitor dzienników](logs/manage-cost-storage.md) — dodano informacje dotyczące relacji między dziennikami Azure monitor i rozliczeń Azure Security Center. Dodano zapytanie dla liczb węzłów w przypadku korzystania z warstwy cenowej na węzeł. 
- [Monitoruj kondycję obszaru roboczego log Analytics w Azure monitor](logs/monitor-workspace.md) — nowy artykuł.
- [Wykonywanie zapytań dotyczących danych w Azure monitor przy użyciu usługi Azure Eksplorator danych (wersja zapoznawcza)](logs/azure-data-explorer-monitor-proxy.md) — nowy artykuł.
- [Wyeksportuj dane z Azure monitor przy użyciu usługi Azure Eksplorator danych (wersja zapoznawcza)](logs/azure-data-explorer-query-storage.md) — nowy artykuł.

### <a name="networks"></a>Sieci
- [Azure monitor dla sieci](insights/network-insights-overview.md) — Dodano sekcję rozwiązywania problemów. Dodano sekcję na temat łączności.

### <a name="platform-logs"></a>Dzienniki platformy
- [Schemat zdarzeń dziennika aktywności platformy Azure](essentials/activity-log-schema.md) — Dodano opis poziomów ważności.

### <a name="virtual-machines"></a>Maszyny wirtualne
- [Analiza zmian w Azure monitor dla maszyn wirtualnych](vm/vminsights-change-analysis.md) — nowy artykuł.
- [Włączenie Azure monitor dla maszyn wirtualnych przegląd](vm/vminsights-enable-overview.md) — dodano Obsługiwane regiony.
- [Jak zaktualizować usługę Container Insights w celu](containers/container-insights-update-metrics.md) uzyskania pomocy technicznej dla klastra Kubernetes z włączoną funkcją Azure Arc.



## <a name="september-2020"></a>Wrzesień 2020

### <a name="general"></a>Ogólne
- [Azure monitor często zadawane pytania](faq.md) — Dodano sekcję w witrynie OpenTelemetry.

### <a name="agents"></a>Agenci
- [Omówienie agenta Azure monitor](agents/azure-monitor-agent-overview.md) — dodano czynniki decyzyjne w celu przełączenia do nowego agenta.
- [Informacje o obsłudze agentów monitorowania platformy Azure](agents/agents-overview.md) — dodano obsługę systemu Windows 10.

### <a name="alerts"></a>Alerty
- [Utwórz alert dotyczący dziennika z szablonem Azure Resource Manager](alerts/alerts-log-create-templates.md) — nowy artykuł.
- [Rozwiązywanie problemów z alertami metryki platformy Azure](alerts/alerts-troubleshoot-metric.md) — Dodano sekcję dotyczącą EKSPORTOWANIA szablonu ARM dla reguły alertu dotyczącego metryki.

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


### <a name="containers"></a>Kontenery
- [Skonfiguruj klaster Kubernetes z obsługą usługi Azure ARC przy użyciu usługi Container Insights](containers/container-insights-enable-arc-enabled-clusters.md) — dodano wskazówki dotyczące włączania monitorowania za pomocą nazwy głównej.
- [Wdrożenie & hPa metryk z usługą Container Insights](containers/container-insights-deployment-hpa-metrics.md) — nowy artykuł.

### <a name="insights-and-solutions"></a>Szczegółowe informacje i rozwiązania
- [Azure monitor dla usługi Azure cache for Redis](insights/redis-cache-insights-overview.md) — oznaczenie podglądu usuniętego elementu.
- [Azure monitor dla sieci (wersja zapoznawcza)](insights/network-insights-overview.md) — dodano sekcje połączeń i ruchu.
- [Łącznik zarządzania usługami IT — Bezpieczny eksport w Azure monitor](alerts/it-service-management-connector-secure-webhook-connections.md) — nowy artykuł.
- [Łącznik zarządzania usługami IT w Azure monitor](alerts/itsmc-connections.md) — uwagi dotyczące integracji z Cherwell i Provance narzędzia ITSM.
- [Key Vault monitorowania z Azure monitor dla Key Vault](insights/key-vault-insights-overview.md) — usunięto oznaczenie podglądu.

### <a name="logs"></a>Dzienniki
- [Zapytania inspekcji w dziennikach Azure monitor](logs/query-audit.md) — nowy artykuł.
- Azure Monitor skrytką klienta dodano [klucz zarządzany przez klienta](logs/customer-managed-keys.md) .
- [Azure monitor rejestruje dedykowane klastry](logs/logs-dedicated-clusters.md) — nowy artykuł.
- [Projektowanie Azure monitor wdrożenia dzienników](logs/design-logs-deployment.md) — Zaktualizowano sekcję limit współczynnika rozmiaru i ilości pozyskanych danych.
- [Zakres zapytania dziennika w Azure Monitor Log Analytics](logs/scope.md) — aktualizacje mające na celu uwzględnienie aplikacji opartych na obszarze roboczym.
- [Rejestruje Azure monitor](logs/data-platform-logs.md) — aktualizacje mające na celu uwzględnienie aplikacji opartych na obszarze roboczym.
- [Standardowe kolumny w Azure monitor rekordy dziennika](logs/log-standard-columns.md) — aktualizacje do uwzględnienia aplikacji opartych na obszarze roboczym.
- [Azure monitor limity usługi](service-limits.md) — Zaktualizowano limity ograniczania zapytania użytkownika.
- [Korzystanie z kont magazynu zarządzanych przez klienta w Azure Monitor Log Analytics](logs/private-storage.md) — artykuł ponownie zapisany.
- [Wyświetlanie i analizowanie danych w usłudze Azure log Analytics](./logs/data-platform-logs.md) — aktualizacje mające na celu uwzględnienie aplikacji opartych na obszarze roboczym.


### <a name="platform-logs"></a>Dzienniki platformy
- [Schemat zdarzeń dziennika aktywności platformy Azure —](essentials/activity-log-schema.md) dodano poziomy ważności Azure monitor.
- [Przykłady szablonów Menedżer zasobów dla ustawień diagnostycznych](essentials/resource-manager-diagnostic-settings.md) — dodano przykład dla konta usługi Azure Storage.

### <a name="visualizations"></a>Wizualizacje
- [Azure monitor wizualizacje wykresów skoroszytu](visualize/workbooks-chart-visualizations.md) — nowy artykuł.
- [Moduł renderowania złożonego paska Azure monitor skoroszytu](visualize/workbooks-composite-bar.md) — nowy artykuł.
- [Azure monitor wizualizacje wykresów skoroszytu](visualize/workbooks-graph-visualizations.md) — nowy artykuł.
- [Azure monitor wizualizacje siatki skoroszytu](visualize/workbooks-grid-visualizations.md) — nowy artykuł.
- [Wizualizacje plastra miodu Azure monitor skoroszyty](visualize/workbooks-honey-comb.md) — nowy artykuł.
- [Azure monitor wizualizacje tekstu skoroszytu](visualize/workbooks-text-visualizations.md) — nowy artykuł.
- [Azure monitor wizualizacje kafelków skoroszytu](visualize/workbooks-tile-visualizations.md) — nowy artykuł.
- [Azure monitor wizualizacje drzewa skoroszytów](visualize/workbooks-tree-visualizations.md) — nowy artykuł.




## <a name="august-2020"></a>Sierpień 2020 r.

### <a name="general"></a>Ogólne

- [Co jest monitorowane przez Azure monitor](monitor-reference.md) — zaktualizowano w celu uwzględnienia Azure monitor agenta.


### <a name="agents"></a>Agenci
- [Omówienie agenta Azure monitor](agents/azure-monitor-agent-overview.md) — nowy artykuł.
- [Włącz Azure monitor dla środowiska hybrydowego](vm/vminsights-enable-hybrid.md) — zaktualizowana wersja agenta zależności.
- [Omówienie agentów monitorowania platformy Azure](agents/agents-overview.md) — dodano agenta Azure monitor i tabelę pomocy technicznej systemu operacyjnego.


#### <a name="new-and-updated-articles-from-restructure-of-agent-content"></a>Nowe i zaktualizowane artykuły z restrukturyzacji zawartości agenta
- [Omówienie włączania usługi VM Insights](vm/vminsights-enable-overview.md)
- [Instalowanie agenta usługi Log Analytics na komputerach z systemem Linux](agents/agent-linux.md)
- [Instalowanie agenta usługi Log Analytics na komputerach z systemem Windows](agents/agent-windows.md)
- [Omówienie agenta Log Analytics](agents/log-analytics-agent.md)

### <a name="application-insights"></a>Application Insights
- [Application Insights platformy Azure dla aplikacji sieci Web JavaScript](app/javascript.md) — Dodano sekcję z wyjaśnieniem korelacji i konfiguracją serwera klienta na potrzeby korelacji CORS.
- [Utwórz nowe Azure Monitor Application Insights](app/create-workspace-resource.md) możliwości dodanych przez obszary robocze udostępniane przez aplikacje oparte na obszarze roboczym.
- [Adresy IP używane przez Application Insights i log Analytics](app/ip-addresses.md) — zaktualizowane adresy IP dla strumienia metryk na żywo.
- [Monitoruj aplikacje Java w dowolnym środowisku — Azure Monitor Application Insights](app/java-in-process-agent.md) dodanej tabeli pod kątem obsługiwanej niestandardową telemetrię.
- [Wtyczka z natywną obsługą reakcji dla Application Insights JavaScript SDK](app/javascript-react-native-plugin.md) — nowy artykuł.
- [Zareaguj na wtyczkę dla Application Insights JavaScript SDK](app/javascript-react-plugin.md) — nowy artykuł.
- [Przykład szablonu Menedżer zasobów na potrzeby tworzenia aplikacji funkcji platformy Azure przy użyciu monitorowania Application Insights](app/resource-manager-function-app.md) — nowy artykuł.
- [Przykłady szablonów Menedżer zasobów do tworzenia aplikacji sieci Web platformy Azure App Services za pomocą monitorowania Application Insights](app/resource-manager-web-app.md) — nowy artykuł.
- [Analiza użycia za pomocą dodanego wideo na platformie Azure Application Insights](app/usage-overview.md) .

### <a name="autoscale"></a>Automatyczne skalowanie
- [Rozpocznij pracę z funkcją automatycznego skalowania na platformie Azure](autoscale/autoscale-get-started.md) — Dodano sekcję dotyczącą routingu do wystąpień w dobrej kondycji dla App Service.

### <a name="data-collection"></a>Zbieranie danych
- [Skonfiguruj zbieranie danych dla agenta Azure monitor (wersja zapoznawcza)](agents/data-collection-rule-azure-monitor-agent.md) — nowy artykuł.
- [Reguły zbierania danych w Azure monitor (wersja zapoznawcza)](agents/data-collection-rule-overview.md) — nowy artykuł.


### <a name="containers"></a>Kontenery
- [Wdrożenie & hPa metryk z usługą Container Insights](containers/container-insights-deployment-hpa-metrics.md) — nowy artykuł.

### <a name="insights"></a>Insights
- [Monitorowanie rozwiązań w Azure monitor](insights/solutions.md) — zaktualizowane dla nowego interfejsu użytkownika.
- [Network Performance Monitor rozwiązanie na platformie Azure](insights/network-performance-monitor.md) — dodano Obsługiwane regiony obszaru roboczego.


### <a name="logs"></a>Dzienniki
- [Azure monitor często zadawane pytania](faq.md) — dodano wpis służący do usuwania danych z obszaru roboczego. Dodano wpis na 502 i 503 odpowiedzi.
  - [Projektowanie wdrożenia dzienników Azure monitor](logs/design-logs-deployment.md) — liczba aktualizacji w sekcji limit ilości woluminu pozyskiwania.
- [Zarządzanie użyciem i kosztami Azure monitor dzienników](logs/manage-cost-storage.md) — zaktualizowane zapytania dotyczące użycia do bardziej wydajnego formatu zapytania.
- [Optymalizowanie zapytań dzienników w Azure monitor](logs/query-optimization.md) dodanych określonych wartości do wskaźników wydajności.
- [Przykłady szablonów Menedżer zasobów dla ustawień diagnostycznych](essentials/resource-manager-diagnostic-settings.md) — dodano przykład dla dzienników inspekcji zapytań dzienników.


### <a name="platform-logs"></a>Dzienniki platformy
- [Tworzenie ustawień diagnostycznych w celu wysyłania dzienników platformy i metryk do różnych miejsc docelowych](essentials/diagnostic-settings.md) — dodanych wymagań regionalnych dla ustawień diagnostycznych.

### <a name="visualizations"></a>Wizualizacje
- [Przegląd Azure monitor skoroszytów](visualize/workbooks-overview.md) — dodano film wideo.
- [Przenieś szablon skoroszytu platformy Azure do innego regionu](visualize/workbook-templates-move-region.md) — nowy artykuł.
- [Przenieś skoroszyt platformy Azure do innego regionu](visualize/workbooks-move-region.md) — nowy artykuł.



## <a name="july-2020"></a>Lipiec 2020 r.

### <a name="general"></a>Ogólne
- [Wdróż Azure monitor](deploy-scale.md) -rekonstrukcję zawartości dołączania do usługi VM Insights.
- [Skorzystaj z prywatnego linku platformy Azure, aby bezpiecznie połączyć sieci z](logs/private-link-security.md) sekcją dodaną do Azure monitor.

### <a name="alerts"></a>Alerty
- [Reguły akcji dla alertów Azure monitor](alerts/alerts-action-rules.md) — dodano procesy interfejsu wiersza polecenia.
- [Tworzenie grup akcji i zarządzanie nimi w Azure Portal](alerts/action-groups.md) — zaktualizowano w celu odzwierciedlenia zmian w interfejsie użytkownika.
- [Przykładowe zapytania w Azure Monitor Log Analytics](logs/example-queries.md) — nowy artykuł.
- [Rozwiązywanie problemów z alertami dziennika w sekcji Azure monitor](alerts/alerts-troubleshoot-log.md) dodanej do przydziału reguły alertu.
- [Rozwiązywanie problemów z alertami metryki platformy Azure](alerts/alerts-troubleshoot-metric.md) — Dodano sekcję dotyczącą reguły alertu w niestandardowej metryce, która nie jest jeszcze emitowana.
- [Informacje o tym, jak alerty metryk działają w Azure Monitor.](alerts/alerts-metric-overview.md) -Dodano rekomendację dotyczącą wyboru stopnia szczegółowości agregacji.

### <a name="application-insights"></a>Application Insights
- [Informacje o wersji rozszerzenia aplikacji sieci Web platformy Azure — Application Insights](app/web-app-extension-release-notes.md) — nowy artykuł.
- [Przykłady szablonów Menedżer zasobów dla zasobów Application Insights](app/resource-manager-app-resource.md) — nowy artykuł.
- [Rozwiązywanie problemów z usługą Azure Application Insights Profiler](app/profiler-troubleshooting.md) — dodano uwagę na temat usterki dotyczącej uruchamiania programu Profiler dla ASP.NET Core aplikacji w Azure App Service. 

### <a name="containers"></a>Kontenery
- [Rejestruj alerty z usługi Container Insights](containers/container-insights-log-alerts.md) — nowy artykuł.
- [Alerty metryk z usługi Container Insights](containers/container-insights-metric-alerts.md) — nowy artykuł.

### <a name="logs"></a>Dzienniki
- [Azure monitor](logs/customer-managed-keys.md) komunikat o błędzie dodany przez klienta i sekcja CMK konfigurację dla zapytań.
- [Azure monitor interfejs API modułu zbierającego dane http](logs/data-collector-api.md) — dodano przykład języka Python 3.
- [Optymalizuj zapytania dzienników w sekcji Azure monitor](logs/query-optimization.md) dodanej, unikając wielu skanów danych podczas korzystania z podzapytań.
- [Samouczek: Rozpoczynanie pracy z zapytaniami log Analytics](./logs/log-analytics-tutorial.md) — dodano film wideo.

### <a name="platform-logs"></a>Dzienniki platformy
- [Tworzenie ustawień diagnostycznych w celu wysyłania dzienników platformy i metryk do różnych](essentials/diagnostic-settings.md) dodanych filmów wideo.
- [Przykłady szablonów Menedżer zasobów dla](/resource-manager-samples.md) przykładowej Azure monitor dodanej ARM przy użyciu typu docelowego dzienników. 

### <a name="solutions"></a>Rozwiązania
- [Monitorowanie rozwiązań w](insights/solutions.md) procesach interfejsu wiersza polecenia dodanych Azure monitor.
- [Rozwiązanie do zarządzania pakietem Office 365 na platformie Azure](insights/solution-office-365.md) — Data wycofania zmian.

### <a name="virtual-machines"></a>Maszyny wirtualne

Nowe i zaktualizowane artykuły z restrukturyzacji zawartości usługi VM Insights

- [Co to jest maszyna wirtualna Insights?](vm/vminsights-overview.md)
- [Skonfiguruj obszar roboczy Log Analytics dla szczegółowych informacji o maszynie wirtualnej](vm/vminsights-configure-workspace.md)
- [Łączenie komputerów z systemem Linux z Azure Monitor](agents/agent-linux.md)
- [Włącz Azure Monitor środowiska hybrydowego](vm/vminsights-enable-hybrid.md)
- [Włącz Azure Monitor dla jednej maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych w Azure Portal](vm/vminsights-enable-portal.md)
- [Włącz szczegółowe informacje o maszynie wirtualnej za pomocą Azure Policy](./vm/vminsights-enable-policy.md)
- [Omówienie włączania usługi VM Insights](vm/vminsights-enable-overview.md)
- [Włączanie usługi VM Insights przy użyciu programu PowerShell](vm/vminsights-enable-powershell.md)
- [Włączanie usługi VM Insights za pomocą szablonów Menedżer zasobów](vm/vminsights-enable-resource-manager.md)
- [Włączanie usługi VM Insights przy użyciu programu PowerShell lub szablonów](./vm/vminsights-enable-powershell.md)


### <a name="visualizations"></a>Wizualizacje
- [Uaktualnianie log Analytics wizualizacji pulpitu nawigacyjnego](logs/dashboard-upgrade.md) — Zaktualizowano częstotliwość odświeżania.
- [Wizualizowanie danych z](visualizations.md) wideo dodanego Azure monitor.


## <a name="june-2020"></a>Czerwiec 2020 r.

### <a name="general"></a>Ogólne
- [Wdróż Azure monitor](deploy-scale.md) — nowy artykuł.
- Azure Monitor Właściwość rozliczenia [klucza zarządzanego przez klienta](logs/customer-managed-keys.md) . Dodano polecenia programu PowerShell.

### <a name="agents"></a>Agenci
- [Omówienie agenta log Analytics](agents/log-analytics-agent.md) — dodano wymagania dotyczące języka Python 2.

### <a name="alerts"></a>Alerty
- [Jak zaktualizować reguły alertów lub reguły akcji, gdy ich zasób docelowy jest przenoszony do innego regionu platformy Azure](alerts/alerts-resource-move.md) — nowy artykuł.
- [Rozwiązywanie problemów z alertami usługi Azure Metrics](alerts/alerts-troubleshoot-metric.md) — nowy artykuł.
- [Rozwiązywanie problemów z alertami dziennika w Azure monitor](alerts/alerts-troubleshoot-metric.md) — nowy artykuł.
  
### <a name="application-insights"></a>Application Insights
- [Azure Application Insights for JavaScript Web Apps](app/javascript.md) — aktualizacja do sekcji SDK języka JavaScript. Zaktualizowany fragment kodu dotyczący błędów ładowania raportu.
- [Konfigurowanie BYOS (przenoszenie własnego magazynu) dla profilera & Snapshot Debugger](app/profiler-bring-your-own-storage.md) — nowy artykuł.
- [Przychodzące śledzenie żądań na platformie Azure Application Insights z OpenCensus Python](app/opencensus-python-request.md) — Zaktualizowano rejestrowanie i konfigurację dla OpenCensus.
- [Monitoruj działającą aplikację sieci web ASP.NET za pomocą platformy Azure Application Insights](app/monitor-performance-live-website-now.md) — Zaktualizowano datę wycofania dla Monitor stanu v1.
- [Monitorowanie usług Node.js za pomocą usługi Azure Application Insights](app/nodejs.md) — wiele aktualizacji, w tym Migrowanie z wersji poprzedniej i konfiguracji zestawu SDK
- [Monitoruj aplikacje Python z sekcją Azure monitor (wersja zapoznawcza)](app/opencensus-python.md) dotyczącą konfigurowania Azure monitor eksportujących.
- [Monitoruj aplikacje bez zmian w kodzie — funkcja autoinstrumentation dla Azure Monitor Application Insights](app/codeless-overview.md) — nowy artykuł.
- [Rozwiązywanie problemów z błędem ładowania zestawu SDK dla aplikacji sieci Web w języku JavaScript](app/javascript-sdk-load-failure.md) — nowy artykuł.

### <a name="containers"></a>Kontenery
- [Jak zatrzymać monitorowanie dodanej sekcji hybrydowej klastra Kubernetes](containers/container-insights-optout-hybrid.md) dla Kubernetes z funkcją Arc.
- [Skonfiguruj klaster Kubernetes z obsługą usługi Azure ARC z usługą Container Insights](containers/container-insights-enable-arc-enabled-clusters.md) — nowy artykuł.
- [Skonfiguruj usługę Azure Red Hat OpenShift v4. x za pomocą usługi Container Insights](containers/container-insights-azure-redhat4-setup.md) — zaktualizowane wymagania wstępne.
- [Konfigurowanie danych na żywo usługi Container Insights (wersja zapoznawcza)](containers/container-insights-livedata-setup.md) — usunięto uwagę na to, że funkcja nie jest dostępna w programie Azure USA dla instytucji rządowych.

### <a name="insights"></a>Insights
- [Często zadawane pytania — Network Performance Monitor rozwiązanie na platformie Azure](insights/network-performance-monitor-faq.md) — dodano obsługę często zadawanych pytań dotyczących monitora ExpressRoute.

### <a name="logs"></a>Dzienniki
- [Usuń i Odzyskaj polecenie programu PowerShell dla usługi Azure log Analytics Workspace](logs/delete-workspace.md) . Zaktualizowano Rozwiązywanie problemów.
- [Zarządzanie obszarami roboczymi Log Analytics Azure monitor w](logs/manage-access.md) przykładowym przykładzie dla niedozwolonych tabel w sekcji Azure RBAC.
- [Zarządzanie użyciem i kosztami dzienników Azure monitor](logs/manage-cost-storage.md) — dodatkowe szczegóły dotyczące obliczania rozmiaru danych. Zaktualizowano Konfigurowanie alertów ilości danych. Szczegółowe informacje o danych zabezpieczeń zebranych przez wskaźnik na platformie Azure. Wyjaśnienie dotyczące limitów danych.
- [Użyj dzienników Azure monitor z użyciem Azure Logic Apps i automatyzacji oszczędzania mocy](logs/logicapp-flow-connector.md) — dodano limity łączników.

### <a name="metrics"></a>Metryki
- [Azure monitor metryki obsługiwane przez typ zasobu](essentials/metrics-supported.md) — Zaktualizowano metryki SQL Server.


### <a name="platform-logs"></a>Dzienniki platformy

- [Przykłady szablonów Menedżer zasobów dla ustawień diagnostycznych](essentials/resource-manager-diagnostic-settings.md) — poprawka dla ustawienia diagnostyki dziennika aktywności.
- [Wyślij dziennik aktywności platformy Azure do log Analytics obszaru roboczego przy użyciu Azure Portal](essentials/quick-collect-activity-log-portal.md) — nowy artykuł.
- [Wyślij dziennik aktywności platformy Azure do log Analytics obszaru roboczego przy użyciu szablonu Azure Resource Manager](essentials/quick-collect-activity-log-arm.md) — nowy artykuł.

Nowe i zaktualizowane artykuły z przebudowy i konsolidacji zawartości dziennika platformy

- [Archiwizuj dzienniki zasobów platformy Azure na koncie magazynu](./essentials/resource-logs.md#send-to-azure-storage)
- [Schemat zdarzeń dziennika aktywności platformy Azure](essentials/activity-log-schema.md)
- [Dziennik aktywności platformy Azure](essentials/activity-log.md)
- [Przykłady interfejsu wiersza polecenia Azure Monitor](/cli-samples.md)
- [Przykłady programu Azure Monitor PowerShell](/powershell-samples.md)
- [Przewodnik po interfejsie API REST usługi Azure Monitoring](essentials/rest-api-walkthrough.md)
- [Obsługiwane usługi i schematy dzienników zasobów platformy Azure](./essentials/resource-logs-schema.md)
- [Dzienniki zasobów platformy Azure](essentials/resource-logs.md)
- [Zbieranie i analizowanie dzienników aktywności platformy Azure w Azure Monitor](./essentials/activity-log.md)
- [Zbieranie dzienników zasobów platformy Azure w obszarze roboczym Log Analytics](./essentials/resource-logs.md#send-to-log-analytics-workspace)
- [Tworzenie ustawień diagnostycznych w celu wysyłania metryk i dzienników platformy do różnych miejsc docelowych](essentials/diagnostic-settings.md)
- [Eksportowanie dziennika aktywności platformy Azure](./essentials/activity-log.md#legacy-collection-methods)
- [Omówienie dzienników platformy Azure](essentials/platform-logs-overview.md)
- [Przesyłanie strumieniowe dzienników platformy Azure do centrum zdarzeń](./essentials/resource-logs.md#send-to-azure-event-hubs)
- [Wyświetlanie zdarzeń dziennika aktywności platformy Azure w Azure Monitor](./essentials/activity-log.md#view-the-activity-log)

### <a name="virtual-machines"></a>Maszyny wirtualne
- [Włącz szczegółowe informacje o maszynie wirtualnej w Azure Portal](./vm/vminsights-enable-portal.md) — zaktualizowano w celu uwzględnienia usługi Azure Arc.
- [Omówienie włączania usługi VM Insights](vm/vminsights-enable-overview.md) — zaktualizowano w celu uwzględnienia Azure Arc.
- [Co to jest maszyna wirtualna Insights?](vm/vminsights-overview.md) — Zaktualizowano w celu uwzględnienia usługi Azure Arc.


### <a name="visualizations"></a>Wizualizacje
- [Azure monitor skoroszyty ze źródłami danych](visualize/workbooks-data-sources.md) — dodano alerty i sekcję niestandardowych punktów końcowych.
- [Rozwiązywanie problemów Azure monitor szczegółowych informacji opartych na skoroszycie](insights/troubleshoot-workbooks.md) — nowy artykuł.
- [Uaktualnianie log Analytics wizualizacji pulpitu nawigacyjnego](logs/dashboard-upgrade.md) — nowy artykuł.



## <a name="may-2020"></a>Maj 2020 r.

### <a name="general"></a>Ogólne

- [Azure monitor często zadawane pytania](faq.md) — Dodano sekcję dla metryk.
- [Azure monitor zarządzanie kluczami zarządzanymi przez klienta](logs/customer-managed-keys.md) — różne zmiany w zakresie ogólnej dostępności.
- [Wbudowane definicje zasad dla Azure monitor](./policy-reference.md) — nowy artykuł.
- [Konta magazynu należące do klienta na potrzeby](logs/private-storage.md) pozyskiwania dziennika — nowy artykuł.
- [Zarządzanie użyciem i kosztami dla dzienników Azure monitor](logs/manage-cost-storage.md) — dodano rozliczenia proporcjonalnie do klastra.
- [Skorzystaj z prywatnego linku platformy Azure, aby bezpiecznie połączyć sieci z](logs/private-link-security.md) nowym artykułem Azure monitor.


#### <a name="new-resource-manager-template-samples"></a>Nowe przykłady szablonów Menedżer zasobów 
- [Przykłady szablonów Menedżer zasobów dla Azure Monitor](/resource-manager-samples.md)
- [Przykłady szablonów Menedżer zasobów dla grup akcji](alerts/resource-manager-action-groups.md)
- [Przykłady szablonów Menedżer zasobów dla agentów](agents/resource-manager-agent.md)
- [Przykłady szablonów Menedżer zasobów dla usługi Container Insights](containers/resource-manager-container-insights.md)
- [Przykłady szablonów Menedżer zasobów dla usługi VM Insights](vm/resource-manager-vminsights.md)
- [Przykłady szablonów Menedżer zasobów dla ustawień diagnostycznych](essentials/resource-manager-diagnostic-settings.md)
- [Przykłady szablonów Menedżer zasobów dla obszarów roboczych Log Analytics](logs/resource-manager-workspace.md)
- [Przykłady szablonów Menedżer zasobów dla zapytań dziennika](logs/resource-manager-log-queries.md)
- [Przykłady szablonów Menedżer zasobów dla reguł alertów dotyczących zapytań dziennika](alerts/resource-manager-alerts-log.md)
- [Przykłady szablonów Menedżer zasobów dla reguł alertów dotyczących metryk](alerts/resource-manager-alerts-metric.md)
- [Przykłady szablonów Menedżer zasobów dla skoroszytów](visualize/resource-manager-workbooks.md)

### <a name="agents"></a>Agenci
- [Zainstaluj i skonfiguruj rozszerzenie Windows Azure Diagnostics (funkcji wad)](agents/diagnostics-extension-windows-install.md) — dodano szczegóły dotyczące konfigurowania diagnostyki.
- [Omówienie agenta log Analytics](agents/log-analytics-agent.md) — dodano obsługiwane wersje systemu Linux.

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
- [wyrażenie App () w dziennikach Azure Monitor](logs/app-expression.md)
- [Zakres zapytania dziennika w Azure Monitor Log Analytics](logs/scope.md)
- [Wykonywanie zapytań między zasobami przy użyciu Azure Monitor](logs/cross-workspace-query.md)
- [Standardowe właściwości w rekordach dziennika Azure Monitor](./logs/log-standard-columns.md)
- [Struktura dzienników Azure Monitor](./logs/data-platform-logs.md)





### <a name="containers"></a>Kontenery
- [Jak włączyć informacje o kontenerze](containers/container-insights-onboard.md) — zaktualizowana tabela konfiguracji zapory.
- [Jak zaktualizować usługi Container Insights for Metrics](containers/container-insights-update-metrics.md) — Aktualizacja dotycząca używania tożsamości zarządzanych do zbierania metryk.
- [Koszt monitorowania usługi Container Insights](containers/container-insights-cost.md) — nowy artykuł.
- [Skonfiguruj dane na żywo usługi Container Insights (wersja zapoznawcza)](containers/container-insights-livedata-setup.md) — obsługa nowego powiązania roli klastra.

### <a name="insights"></a>Insights
- [Azure monitor dla usługi Azure cache for Redis (wersja zapoznawcza)](insights/redis-cache-insights-overview.md) — nowy artykuł.
- [Monitoruj Key Vault z Azure monitor do Key Vault (wersja zapoznawcza)](./insights/key-vault-insights-overview.md) — nowy artykuł.

### <a name="logs"></a>Dzienniki
- [Utwórz & skonfiguruj log Analytics przy użyciu](logs/powershell-workspace-configuration.md) sekcji Rozwiązywanie problemów dodanej do programu PowerShell.
- [Utwórz obszar roboczy log Analytics w](logs/quick-create-workspace.md) sekcji Rozwiązywanie problemów dodanej Azure Portal.
- [Utwórz obszar roboczy log Analytics przy użyciu interfejsu wiersza polecenia platformy Azure](logs/quick-create-workspace-cli.md) — Dodano sekcję Rozwiązywanie problemów.
- [Usuwanie i odzyskiwanie usługi Azure log Analytics Workspace](logs/delete-workspace.md) — zaktualizowane informacje dotyczące odzyskiwania usuniętego obszaru roboczego.
- [Funkcje w zapytaniach dziennika Azure monitor](logs/functions.md) — usunięto uwagę na temat funkcji, które nie zawierają innych funkcji.
- [Struktura dzienników Azure monitor](./logs/data-platform-logs.md) — objaśniono opisy właściwości dla Application Insights tabeli.
- [Użyj dzienników Azure monitor z użyciem Azure Logic Apps i automatyzacji oszczędzania mocy](logs/logicapp-flow-connector.md) — Dodano sekcję limity.
- [Użyj programu PowerShell, aby utworzyć i skonfigurować](logs/powershell-workspace-configuration.md) sekcję rozwiązywania problemów dodaną przez log Analytics obszaru roboczego.


### <a name="metrics"></a>Metryki
- [Azure monitor obsługiwane metryki według typów zasobów](essentials/metrics-supported.md) — wyjaśniono metryki Gości i Routing metryk. 

### <a name="solutions"></a>Rozwiązania
- [Zoptymalizuj środowisko Active Directory za pomocą Azure monitor](insights/ad-assessment.md) dodanego systemu Windows Server 2019 do obsługiwanych wersji.
- [Zoptymalizuj środowisko SQL Server przy użyciu Azure monitor](insights/sql-assessment.md) — dodano do obsługiwanych wersji programu SQL Server.


### <a name="virtual-machines"></a>Maszyny wirtualne
- [Omówienie włączania usługi VM Insights](vm/vminsights-enable-overview.md) — dodano do obsługiwanych wersji programu Ubuntu Server. Dodano Obsługiwane regiony dla Log Analytics obszaru roboczego.
- [Jak uzyskać Wykres wydajności za pomocą usługi VM Insights](vm/vminsights-performance.md) — Dodano sekcję ograniczenia dotyczące niedostępnych metryk.

### <a name="visualizations"></a>Wizualizacje
- [Azure monitor skoroszyty i szablony Azure Resource Manager](visualize/workbooks-automate.md) — dodano aktualizację Menedżer zasobów do wdrożenia szablonu skoroszytu.
- [Azure monitor grupy skoroszytów](./visualize/workbooks-groups.md) — nowy artykuł.
- [Azure monitor skoroszytów — Przekształć dane JSON przy użyciu wykryto](visualize/workbooks-jsonpath.md) — nowy artykuł.


## <a name="april-2020"></a>Kwiecień 2020 r.

### <a name="general"></a>Ogólne

- [Azure monitor sekcji z kluczami zarządzanymi przez klienta](logs/customer-managed-keys.md) w operacjach asynchronicznych
- [Zarządzanie obszarami roboczymi log Analytics w](logs/manage-access.md) sekcjach Azure monitor zaktualizowanych dzienników niestandardowych.

### <a name="alerts"></a>Alerty

- [Reguły akcji dla alertów Azure monitor](alerts/alerts-action-rules.md) — dodano film wideo.
- [Przegląd alertów i monitorowania powiadomień na platformie Azure](alerts/alerts-overview.md) — wideo.

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

### <a name="containers"></a>Kontenery

- [Skonfiguruj usługę Azure Red Hat OpenShift v4. x za pomocą usługi Container Insights](containers/container-insights-azure-redhat4-setup.md) — nowy artykuł.
- [Ręczne rozwiązywanie problemów z synchronizacją usługi ServiceNow](alerts/itsmc-resync-servicenow.md) — nowy artykuł.
- [Jak zatrzymać monitorowanie klastra platformy Azure i systemu Red Hat OpenShift v4](containers/container-insights-optout-openshift-v4.md) — nowy artykuł.
- [Jak zatrzymać monitorowanie klastra usługi Azure Red Hat OpenShift v3](containers/container-insights-optout-openshift-v3.md) — nowy artykuł.
- [Jak zatrzymać monitorowanie klastra hybrydowego Kubernetes](containers/container-insights-optout-hybrid.md) — nowy artykuł.

### <a name="insights"></a>Insights

- [Monitoruj magazyny kluczy Azure za pomocą Azure monitor dla magazynów kluczy (wersja zapoznawcza)](insights/key-vault-insights-overview.md) — nowy artykuł.

### <a name="logs"></a>Dzienniki

- [Azure monitor limity usług](service-limits.md) — dodano ograniczenie kwerendy użytkownika.
- [Zarządzanie użyciem i kosztami dla dzienników Azure monitor](logs/manage-cost-storage.md) — dodano rozliczanie dla klastrów dzienników. Dodano zapytanie Kusto, aby umożliwić klientom z starszą warstwą cenową na węzeł, aby określić, czy należy przenieść do warstwy rezerwacji na GB lub na pojemność.

### <a name="metrics"></a>Metryki

- [Zaawansowane funkcje usługi Azure Eksplorator metryk](essentials/metrics-charts.md) — Dodano sekcję agregacji.

### <a name="workbooks"></a>Skoroszyty

- [Azure monitor skoroszyty i szablony Azure Resource Manager](visualize/workbooks-automate.md) — dodano szablon Menedżer zasobów do wdrażania szablonu skoroszytu.

## <a name="march-2020"></a>Marzec 2020 r.

### <a name="general"></a>Ogólne

- [Przegląd Azure monitor](overview.md) — dodano Azure monitor wideo z omówieniem.
- [Azure monitor konfiguracja klucza zarządzanego przez klienta](logs/customer-managed-keys.md) — aktualizacje ogólne.
- [Azure monitor odwołanie do danych](/azure/azure-monitor/reference/) — Nowa witryna.

### <a name="alerts"></a>Alerty

- [Tworzenie i wyświetlanie alertów dziennika aktywności oraz zarządzanie nimi w Azure monitor](alerts/alerts-activity-log.md) — dodatkowe wyjaśnienie Menedżer zasobów szablonu.
- [Informacje o tym, jak alerty metryk działają w Azure Monitor.](alerts/alerts-metric-overview.md) — Zaktualizowano w celu wsparcia dla instytucji rządowych.
- [Rozwiązywanie problemów dotyczących alertów Azure monitor i powiadomień](alerts/alerts-troubleshoot.md) — nowy artykuł.

### <a name="application-insights"></a>Application Insights

- [Automatyzacja Application Insights platformy Azure przy użyciu programu PowerShell](app/powershell.md) — dodano przykłady ARMClient.
- [Ciągły eksport danych telemetrycznych z Application Insights](app/export-telemetry.md) — Dodaj tabelę ze szczegółami struktury eksportu.
- [Włącz Snapshot debugger dla aplikacji .NET w Azure App Service](app/snapshot-debugger-appservice.md) dodanym przykładzie szablonu Menedżer zasobów.
- [Zarządzanie użyciem i kosztami dla Application Insights platformy Azure](app/pricing.md) — dodano informacje dotyczące alertu dotyczącego zakończenia danych.
- [Monitoruj aplikacje języka Python za pomocą Azure monitor (wersja zapoznawcza)](app/opencensus-python.md) — dodano metryki standardowe.
- [Obsługa mapy źródłowej dla aplikacji JavaScript — Azure Monitor Application Insights](app/source-map-support.md) — nowy artykuł.

### <a name="containers"></a>Kontenery

- [Azure monitor często zadawane pytania](faq.md) — Aktualizacja dotycząca usługi Container Insights.
- [Skonfiguruj monitorowanie procesora GPU za pomocą usługi Container Insights](containers/container-insights-gpu-monitoring.md) — nowy artykuł.

### <a name="insights"></a>Insights

- [Rozwiązanie do zarządzania pakietem Office 365 na platformie Azure](insights/solution-office-365.md) — Data wycofania aktualizacji.

### <a name="logs"></a>Dzienniki

- [Optymalizuj zapytania dzienników w Azure monitor](logs/query-optimization.md) dodatkowym stanie procesora dla analizy XML i JSON.
- [Usuń i Odzyskaj rozwiązanie do rozwiązywania problemów z obszarem roboczym usługi Azure log Analytics](logs/delete-workspace.md) .
- [Korzystanie z dzienników Azure monitor z funkcją Azure Logic Apps i automatyzowania](logs/logicapp-flow-connector.md) — Zaktualizowano dla nowego łącznika Azure monitor.

### <a name="metrics"></a>Metryki

- [Wycofanie metryk dyskowych w Azure Portal](essentials/portal-disk-metrics-deprecation.md) artykule.
- [Samouczek — Tworzenie wykresu metryk w](essentials/tutorial-metrics-explorer.md) filmach wideo dodanych przez Azure monitor.

### <a name="platform-logs"></a>Dzienniki platformy

- [Zbieraj i Analizuj dziennik aktywności platformy Azure w Azure monitor](./essentials/activity-log.md) — Zapisz ponownie, aby lepiej wyjaśnić zbieranie dziennika aktywności z ustawieniami diagnostycznymi.

### <a name="virtual-machines"></a>Maszyny wirtualne

- [Monitoruj usługę Azure Virtual Machines przy użyciu](vm/monitor-vm-azure.md) nowego artykułu Azure monitor.
- [Szybki Start: monitorowanie maszyn wirtualnych platformy Azure za pomocą Azure monitor](vm/quick-monitor-azure-vm.md) — zaktualizowane w celu dodania szczegółowych informacji o maszynie wirtualnej.
- [Alerty z usługi VM Insights](vm/vminsights-alerts.md) — nowy artykuł.
- [Omówienie włączania usługi VM Insights](vm/vminsights-enable-overview.md) — zaktualizowane linki do pobrania agenta.

Ogólne aktualizacje dotyczące ogólnej dostępności usługi VM Insights

- [Co to jest maszyna wirtualna Insights?](vm/vminsights-overview.md)
- [Analiza maszyn wirtualnych — często zadawane pytania](vm/vminsights-ga-release-faq.md) 
- [Włącz szczegółowe informacje o maszynie wirtualnej za pomocą Azure Policy](./vm/vminsights-enable-policy.md) 
- [Jak uzyskać Wykres wydajności za pomocą usługi VM Insights](vm/vminsights-performance.md)
- [Jak wykonywać zapytania dotyczące dzienników z usługi VM Insights](vm/vminsights-log-search.md)
- [Wyświetlanie zależności aplikacji za pomocą usługi VM Insights](vm/vminsights-maps.md) 

### <a name="visualizations"></a>Wizualizacje

- [Wizualizowanie danych z Azure monitor](visualizations.md) — zaktualizowano w celu zanotowania planowanego zaniechania projektanta widoków.

## <a name="february-2020"></a>Luty 2020 r.

### <a name="agents"></a>Agenci

Wiele aktualizacji w ramach ponownego zapisywania zawartości rozszerzenia diagnostyki.

- Omówienie tabel z restrukturyzacją [agentów monitorowania platformy Azure](agents/agents-overview.md) w celu lepszego wyjaśnienia unikatowych funkcji poszczególnych agentów.
- [Przegląd rozszerzenia Diagnostyka Azure](agents/diagnostics-extension-overview.md) — ukończono ponowne zapisywanie.
- [Używaj usługi BLOB Storage dla usług IIS i magazynu tabel dla zdarzeń w Azure monitor](agents/diagnostics-extension-logs.md) — ogólne ponowne zapisywanie dla aktualizacji i przejrzystości.
- [Zainstaluj i skonfiguruj rozszerzenie Microsoft Azure Diagnostics (funkcji wad)](agents/diagnostics-extension-windows-install.md) — nowy artykuł. 
- [Schemat rozszerzenia diagnostyki systemu Windows](agents/diagnostics-extension-schema-windows.md) — Reorganizuje.
- [Wyślij dane z rozszerzenia Diagnostyka systemu Windows Azure do platformy Azure Event Hubs](agents/diagnostics-extension-stream-event-hubs.md) — całkowicie ponownie napisano i Zaktualizowano.
- [Przechowywanie i przeglądanie danych diagnostycznych w usłudze Azure Storage](../cloud-services/diagnostics-extension-to-storage.md) — całkowicie ponownie zapisane i zaktualizowane.
- [Log Analytics rozszerzenie maszyny wirtualnej dla systemu Windows](../virtual-machines/extensions/oms-windows.md) — lepszym rozwiązaniem jest powiększanie relacji z agentem log Analytics.
- [Azure monitor rozszerzenie maszyny wirtualnej dla systemu Linux](../virtual-machines/extensions/oms-linux.md) — lepsza relacja z programem log Analytics Agent.

### <a name="application-insights"></a>Application Insights

- [Parametry połączenia w usłudze Azure Application Insights](app/sdk-connection-string.md) — nowy artykuł.

### <a name="insights-and-solutions"></a>Szczegółowe informacje i rozwiązania

#### <a name="container-insights"></a>Szczegółowe informacje o kontenerze

- [Integruj Azure Active Directory z usługą Azure Kubernetes Service](../aks/azure-ad-integration-cli.md) — dodano uwagę na potrzeby tworzenia aplikacji klienckiej do obsługi klastra z obsługą RBAC Kubernetes w celu obsługi usługi Container Insights.

#### <a name="vm-insights"></a>Szczegółowe informacje o maszynie wirtualnej

- [Informacje o usłudze VM Insights (ga) — często zadawane pytania](vm/vminsights-ga-release-faq.md) — zmiana sposobu przechowywania danych wydajności.

#### <a name="office-365"></a>Office 365

- [Rozwiązanie do zarządzania pakietem Office 365 na platformie Azure](insights/solution-office-365.md) — Data wycofania aktualizacji.


### <a name="logs"></a>Dzienniki

- [Optymalizowanie zapytań dzienników w Azure monitor](logs/query-optimization.md) — nowy artykuł.
- [Zarządzanie użyciem i kosztami dzienników Azure monitor](logs/manage-cost-storage.md) — ulepszone przykładowe zapytania ułatwiające zrozumienie użycia.

### <a name="metrics"></a>Metryki

- [Azure monitor metryki platformy eksportowane za pośrednictwem ustawień diagnostycznych](essentials/metrics-supported-export-diagnostic-settings.md) — Dodano sekcję w temacie Zmiana na zachowanie dla wartości null i zero.

### <a name="visualizations"></a>Wizualizacje

Wiele nowych artykułów do przewodnika po konwersji projektanta widoków na skoroszyty.

- [Przewodnik po przejściu do programu Azure monitor View Designer do skoroszytów](visualize/view-designer-conversion-overview.md) — nowy artykuł.
- [Azure monitor widoku projektanta na skoroszyty opcje konwersji](visualize/view-designer-conversion-options.md) — nowy artykuł.
- [Azure monitor widoków Projektant do skoroszytów konwersje kafelków](visualize/view-designer-conversion-tiles.md) — nowy artykuł.
- [Azure monitor widoku projektanta na skoroszyty podsumowanie konwersji i dostęp do](visualize/view-designer-conversion-access.md) nowego artykułu.
- [Azure monitor widoków Projektant do skoroszytów typowych zadań konwersji](visualize/view-designer-conversion-tasks.md) — nowy artykuł.
- [Przykłady konwersji Azure monitor View na skoroszyty](visualize/view-designer-conversion-examples.md) — nowy artykuł.

## <a name="january-2020"></a>Styczeń 2020 r.

### <a name="general"></a>Ogólne

- [Co jest monitorowane przez Azure Monitor?](monitor-reference.md) — Nowy artykuł.

### <a name="agents"></a>Agenci

- [Zbierz dane dziennika za pomocą usługi Azure log Analytics Agent](agents/log-analytics-agent.md) — Zaktualizowano tabelę wymagań zapory sieciowej.

### <a name="alerts"></a>Alerty

- [Tworzenie grup akcji i zarządzanie nimi w ustawieniach Azure Portal](alerts/action-groups.md) , które zostały usunięte dla funkcji w wersji 2, które nie są już wymagane.
- [Utwórz alert metryki z przykładem Menedżer zasobów szablonu](alerts/alerts-metric-create-templates.md) dodanym jako parametr *ignoreDataBefore* .  Dodano ograniczenia dotyczące reguł wielokryteriów.
- [Za pomocą log Analytics alert API REST](alerts/api-alerts.md) — Poprawiono przykład JSON.

### <a name="application-insights"></a>Application Insights

- [Adresy IP używane przez Application Insights i log Analytics](app/ip-addresses.md) — Zaktualizowano sekcję Test dostępności wraz z dodawaniem reguły portów przychodzących w celu zezwalania na ruch przy użyciu sieciowych grup zabezpieczeń platformy Azure.
- [Rozwiązywanie problemów z usługą Azure Application Insights Profiler](app/profiler-troubleshooting.md) — zaktualizowane ogólne Rozwiązywanie problemów.
- [Próbkowanie danych telemetrycznych na platformie Azure Application Insights](app/sampling.md) — zaktualizowane i rozbudowane w celu zwiększenia czytelności na podstawie opinii klientów.

### <a name="data-security"></a>Bezpieczeństwo danych

- [Azure monitor konfiguracja klucza zarządzanego przez klienta](logs/customer-managed-keys.md) — nowy artykuł.

### <a name="insights-and-solutions"></a>Szczegółowe informacje i rozwiązania

#### <a name="container-insights"></a>Szczegółowe informacje o kontenerze

- [Konfigurowanie zbierania danych agenta usługi Container Insights](containers/container-insights-agent-config.md) — dodano szczegóły dotyczące uaktualniania agenta na platformie Azure Red Hat OpenShift oraz dodano dodatkowe informacje w celu odróżnienia metod uaktualnienia agenta.
- [Utwórz alerty wydajności dla usługi Container Insights](./containers/container-insights-log-alerts.md) — poprawione informacje i zaktualizowane kroki w celu utworzenia alertu dotyczącego danych wydajności przechowywanych w obszarze roboczym przy użyciu alertów kontekstu obszaru roboczego.
- [Kubernetes monitorowanie za pomocą usługi Container Insights](containers/container-insights-analyze.md) — Zaktualizowano artykuł z omówieniem oraz artykuł Analizuj dotyczący obsługi klastrów Windows Kubernetes.
- [Skonfiguruj klastry usługi Azure Red Hat OpenShift z usługą Container Insights](containers/container-insights-azure-redhat-setup.md) — dodano szczegóły dotyczące uaktualniania agenta na platformie Azure Red Hat OpenShift i dodano dodatkowe informacje w celu odróżnienia metod uaktualnienia agenta.
- [Skonfiguruj hybrydowe klastry Kubernetes za pomocą usługi Container Insights](containers/container-insights-hybrid-setup.md) — zaktualizowano w celu odzwierciedlenia dodanej obsługi bezpiecznego portu: 10250 przy użyciu cAdvisor Kubelet.
- [Jak zarządzać agentem usługi Container Insights](containers/container-insights-manage-agent.md) — Zaktualizowano szczegóły dotyczące zachowania i konfiguracji odporności metryk przy użyciu rozwiązania Azure Red Hat OpenShift w porównaniu z innymi typami klastrów Kubernetes.
- [Konfigurowanie integracji usługi Container Insights Prometheus](containers/container-insights-prometheus-integration.md) — zaktualizowane informacje związane z zachowaniem i konfiguracją odporności metryk dzięki usłudze Azure Red Hat OpenShift w porównaniu z innymi typami klastrów Kubernetes.
- [Jak zaktualizować usługę Container Insights dla metryk](containers/container-insights-update-metrics.md) — Zaktualizowano szczegółowe informacje związane z zachowaniem i konfiguracją wycinków metryk za pomocą Red Hat OpenShift w porównaniu z innymi typami klastrów Kubernetes.

#### <a name="vm-insights"></a>Szczegółowe informacje o maszynie wirtualnej

- Analiza [maszyn wirtualnych — często zadawane pytania](vm/vminsights-ga-release-faq.md) — dodano informacje dotyczące uaktualniania obszaru roboczego i agentów do nowej wersji.

#### <a name="office-365"></a>Office 365

- [Rozwiązanie do zarządzania pakietem office 365 na platformie Azure](insights/solution-office-365.md) — dodano szczegóły i często zadawane pytania dotyczące migracji do rozwiązania pakietu Office 365 na platformie Azure. Usunięto sekcję dołączania.

### <a name="logs"></a>Dzienniki

- [Zarządzanie obszarami roboczymi log Analytics w programie Azure monitor](logs/manage-access.md) — aktualizacje nie są wykonywane.
- [Zarządzanie użyciem i kosztami Azure monitor dzienników](logs/manage-cost-storage.md) — dodano wyjaśnienie dotyczące obliczania ilości danych w sekcji model cen.
- [Użyj szablonów Azure Resource Manager, aby utworzyć i skonfigurować szablon log Analytics obszaru roboczego](./logs/resource-manager-workspace.md) zaktualizowany przy użyciu nowych warstw cenowych.

### <a name="platform-logs"></a>Dzienniki platformy

- [Zbierz dziennik aktywności platformy Azure z ustawieniami diagnostycznymi — Azure monitor](./essentials/activity-log.md) — dodatkowe informacje na temat zmienionych właściwości.
- [Eksportowanie dziennika aktywności platformy Azure](./essentials/activity-log.md#legacy-collection-methods) — zaktualizowano w celu wprowadzenia zmian w interfejsie użytkownika. 

## <a name="december-2019"></a>Grudzień 2019 r.

### <a name="agents"></a>Agenci

- [Połącz komputery z systemem Linux, aby Azure monitor](agents/agent-linux.md) — nowy artykuł.

### <a name="alerts"></a>Alerty

- [Utwórz alert metryki za pomocą szablonu Menedżer zasobów](alerts/alerts-metric-create-templates.md) dodanego przykładu dla metryki niestandardowej.
- [Tworzenie alertów z progami dynamicznymi w sekcji Azure monitor](alerts/alerts-dynamic-thresholds.md) dodano w celu interpretacji wykresów progów dynamicznych.
- [Przegląd alertów i monitorowania powiadomień na platformie Azure](alerts/alerts-overview.md) — zaktualizowane zapytanie dotyczące grafu zasobów.
- [Obsługiwane zasoby dla alertów metryk w Azure monitor](alerts/alerts-metric-near-real-time.md) -Update do obsługiwanych metryk i wymiarów.
- [Przełączenie się ze starszej wersji interfejsu API alertów usługi log Analytics na nową funkcję API alertów platformy Azure](alerts/alerts-log-api-switch.md) — dodano uwagę na zmodyfikowaną nazwę
- [Informacje o tym, jak alerty metryk działają w Azure Monitor.](alerts/alerts-metric-overview.md) -Dodano obsługiwane typy zasobów do monitorowania w odpowiedniej skali.

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

- [Informacje o kontenerze często zadawane pytania](./faq.md) — dodano pytanie dotyczące pól obrazu i nazwy.
- [Azure SQL Analytics rozwiązanie w Azure monitor](insights/azure-sql.md) — zaktualizowana baza danych czeka na obsługę wystąpienia zarządzanego.
- [Skonfiguruj ustawienia kolekcji danych agenta usługi Container Insights](containers/container-insights-agent-config.md) dla enrich_container_logs.
- [Skonfiguruj hybrydowe klastry Kubernetes za pomocą usługi Container Insights](containers/container-insights-hybrid-setup.md) — Dodano sekcję rozwiązywania problemów.
- [Monitoruj Active Directory stan replikacji z](insights/ad-replication-status.md) aktualizacją wymagań wstępnych Azure monitor .NET Framework.
- [Network Performance Monitor rozwiązanie na platformie Azure](insights/network-performance-monitor.md) — dodano Obsługiwane regiony.
- [Zoptymalizuj środowisko Active Directory z](insights/ad-assessment.md) aktualizacją wymagań wstępnych Azure monitor .NET Framework.
- [Zoptymalizuj środowisko SQL Server z](insights/sql-assessment.md) aktualizacją wymagań wstępnych Azure monitor .NET Framework.
- [Zoptymalizuj środowisko System Center Operations Manager za pomocą usługi Azure log Analytics](insights/scom-assessment.md) -.NET Framework aktualizacji wymagań wstępnych.
- [Obsługiwane połączenia z łącznik zarządzania usługami IT na platformie Azure log Analytics](alerts/itsmc-connections.md) — dodano Nowy Jork do wstępnie wymaganego identyfikatora klienta i klucza tajnego klienta.

### <a name="logs"></a>Dzienniki

- [Usuń i Odzyskaj metodę programu PowerShell dla usługi Azure log Analytics Workspace](logs/delete-workspace.md) .
- [Projektowanie Azure Monitorych dzienników](logs/design-logs-deployment.md) — szybkość pozyskiwania dla obszaru roboczego zwiększyła się.

### <a name="metrics"></a>Metryki

- [Metryki platformy Azure monitor eksportowane za pośrednictwem ustawień diagnostycznych](essentials/metrics-supported-export-diagnostic-settings.md) — nowy artykuł.

### <a name="platform-logs"></a>Dzienniki platformy

Zaktualizowano wiele artykułów w ramach restrukturyzacji zawartości dla dzienników platformy w oparciu o nową funkcję konfigurowania dziennika aktywności przy użyciu ustawień diagnostycznych.

- [Archiwizuj dzienniki zasobów platformy Azure na koncie magazynu](./essentials/resource-logs.md#send-to-azure-storage)
- [Schemat zdarzeń dziennika aktywności platformy Azure](essentials/activity-log-schema.md)
- [Limity usługi Azure Monitor](service-limits.md)
- [Zbieranie i analizowanie dzienników aktywności platformy Azure w obszarze roboczym Log Analytics](./essentials/activity-log.md)
- [Zbierz dziennik aktywności platformy Azure przy użyciu ustawień diagnostycznych (wersja zapoznawcza) — Azure Monitor](./essentials/activity-log.md)
- [Zbieranie dzienników aktywności platformy Azure w obszarze roboczym Log Analytics w ramach dzierżawców platformy Azure](./essentials/activity-log.md)
- [Zbieranie dzienników zasobów platformy Azure w obszarze roboczym Log Analytics](./essentials/resource-logs.md#send-to-log-analytics-workspace)
- [Tworzenie ustawień diagnostycznych na platformie Azure przy użyciu szablonu Menedżer zasobów](./essentials/resource-manager-diagnostic-settings.md)
- [Tworzenie ustawień diagnostycznych w celu zbierania dzienników i metryk na platformie Azure](essentials/diagnostic-settings.md)
- [Eksportowanie dziennika aktywności platformy Azure](./essentials/activity-log.md#legacy-collection-methods)
- [Omówienie dzienników platformy Azure](essentials/platform-logs-overview.md)
- [Przesyłanie strumieniowe danych monitorowania platformy Azure do centrum zdarzeń](essentials/stream-monitoring-data-event-hubs.md)
- [Przesyłanie strumieniowe dzienników platformy Azure do centrum zdarzeń](./essentials/resource-logs.md#send-to-azure-event-hubs)

### <a name="quickstarts-and-tutorials"></a>Przewodniki Szybki start i samouczki

- [Utwórz wykres metryk w Azure monitor](essentials/tutorial-metrics-explorer.md) — nowy artykuł.
- [Zbierz dzienniki zasobów z zasobów platformy Azure i Analizuj je za pomocą Azure monitor](essentials/tutorial-resource-logs.md) — nowy artykuł.
- [Monitoruj zasób platformy Azure za pomocą Azure monitor](essentials/quick-monitor-azure-resource.md) — nowy artykuł.
   
## <a name="next-steps"></a>Następne kroki

- Jeśli chcesz współtworzyć dokumentację Azure Monitor, zobacz [Przewodnik współautora dokumentów](/contribute/).