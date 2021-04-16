---
title: Co jest monitorowane przez Azure Monitor
description: Odwołanie do wszystkich usług i innych zasobów monitorowanych przez Azure Monitor.
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 08/15/2020
ms.openlocfilehash: 513f262f5d09cf56c4506a4f20c9aa41507c2abd
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515281"
---
# <a name="what-is-monitored-by-azure-monitor"></a>Co jest monitorowane przez Azure Monitor?
W tym artykule opisano różne aplikacje i usługi, które są monitorowane przez Azure Monitor. 

## <a name="insights-and-core-solutions"></a>Szczegółowe informacje i podstawowe rozwiązania
Podstawowe szczegółowe informacje i rozwiązania są traktowane jako część Azure Monitor i postępuj zgodnie z umowami pomocy technicznej i poziomu usług dla platformy Azure. Są one obsługiwane we wszystkich regionach świadczenia usługi Azure, Azure Monitor są dostępne.

### <a name="insights"></a>Insights

Szczegółowe informacje zapewniają dostosowane środowisko monitorowania dla określonych aplikacji i usług. Zbierają i analizują zarówno dzienniki, jak i metryki.

| Szczegółowe informacje | Opis |
|:---|:---|
| [Application Insights](app/app-insights-overview.md) | Rozszerzalna usługa zarządzania wydajnością aplikacji (APM, Extensible Application Performance Management) do monitorowania aplikacji internetowej na żywo na dowolnej platformie. |
| [Szczegółowe informacje o kontenerze](containers/container-insights-overview.md) | Monitoruje wydajność obciążeń kontenerów wdrożonych w Azure Container Instances lub zarządzanych klastrach Kubernetes hostowanych w Azure Kubernetes Service (AKS). |
| [Azure Monitor for Cosmos DB](insights/cosmosdb-insights-overview.md) | Zapewnia widok ogólnej wydajności, awarii, pojemności i kondycji operacyjnej wszystkich zasobów Azure Cosmos DB w ujednoliconym interaktywnym interfejsie. |
| [Azure Monitor dla sieci (wersja zapoznawcza)](insights/network-insights-overview.md) | Zapewnia kompleksowy widok kondycji i metryk dla wszystkich zasobów sieciowych. Zaawansowana funkcja wyszukiwania pomaga identyfikować zależności zasobów, umożliwiając scenariusze, takie jak identyfikowanie zasobów hostjących witrynę internetową, po prostu wyszukując nazwę witryny internetowej. |
[Azure Monitor dla grup zasobów (wersja zapoznawcza)](insights/resource-group-insights.md) |  Klasyzowanie i diagnozowanie problemów napotykanych przez poszczególne zasoby przy jednoczesnym oferowanie kontekstu kondycji i wydajności grupy zasobów jako całości. |
| [Azure Monitor dla magazynu](insights/storage-insights-overview.md) | Zapewnia kompleksowe monitorowanie kont usługi Azure Storage, zapewniając ujednolicony widok wydajności, pojemności i dostępności usług Azure Storage. |
| [Szczegółowe informacje o maszynach wirtualnych](vm/vminsights-overview.md) | Monitoruje maszyny wirtualne platformy Azure i zestawy skalowania maszyn wirtualnych na dużą skalę. Analizuje ona wydajność i kondycję Twoich maszyn wirtualnych z systemami Windows i Linux oraz monitoruje ich procesy i zależności od innych zasobów oraz procesów zewnętrznych. |
| [Azure Monitor for Key Vault (wersja zapoznawcza)](./insights/key-vault-insights-overview.md) | Zapewnia kompleksowe monitorowanie magazynów kluczy, zapewniając ujednolicony widok żądań Key Vault, wydajności, awarii i opóźnień. |
| [Azure Monitor for Azure Cache for Redis (wersja zapoznawcza)](insights/redis-cache-insights-overview.md) |  Zapewnia ujednolicony, interaktywny widok ogólnej wydajności, awarii, pojemności i kondycji operacyjnej. |


### <a name="core-solutions"></a>Podstawowe rozwiązania

Rozwiązania są oparte na zapytaniach dziennika i widokach dostosowanych do określonej aplikacji lub usługi. Zbierają i analizują tylko dzienniki i z czasem są przestarzałe na rzecz szczegółowych informacji.

| Rozwiązanie | Opis |
|:---|:---|
| [Kondycja agenta](insights/solution-agenthealth.md) | Analizowanie kondycji i konfiguracji agentów usługi Log Analytics. |
| [Zarządzanie alertami](insights/alert-management-solution.md) | Przeanalizuj alerty System Center Operations Manager, Nagios lub Zabbix. |
| [Service Map](vm/service-map.md) | Automatycznie odnajduje składniki aplikacji w systemach Windows i Linux oraz mapuje komunikację między usługami. |



## <a name="azure-services"></a>Usługi platformy Azure
W poniższej tabeli wymieniono usługi platformy Azure i dane, które zbierają w Azure Monitor. 

- Metryki — usługa automatycznie zbiera metryki do Azure Monitor metryk. 
- Dzienniki — usługa obsługuje ustawienia diagnostyczne, które mogą zbierać dzienniki platformy i metryki, aby Azure Monitor dzienniki.
- Szczegółowe informacje — usługa udostępnia szczegółowe informacje, które zapewniają dostosowane środowisko monitorowania dla usługi.

| Usługa | Metryki | Dzienniki | Szczegółowe informacje | Uwagi |
|:---|:---|:---|:---|:---|
|Active Directory | Nie | Tak | [Tak](../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md) |  |
|Active Directory B2C | Nie | Nie | Nie |  |
|Active Directory Domain Services | Nie | Tak | Nie |  |
|Dziennik aktywności | Nie | Tak | Nie | |
|Zaawansowana ochrona przed zagrożeniami | Nie | Nie | Nie |  |
|Advisor | Nie | Nie | Nie |  |
|AI Builder | Nie | Nie | Nie |  |
|Analysis Services | Tak | Tak | Nie |  |
|Interfejs API dla standardu FHIR | Nie | Nie | Nie |  |
|API Management | Tak | Tak | Nie |  |
|App Service | Tak | Tak | Nie |  |
|AppConfig | Nie | Nie | Nie |  |
|Application Gateway | Tak | Tak | Nie |  |
|Usługa zaświadczenia | Nie | Nie | Nie |  |
|Automation | Tak | Tak | Nie |  |
|Azure Service Manager (RDFE) | Nie | Nie | Nie |  |
|Backup | Nie | Tak | Nie |  |
|Bastion | Nie | Nie | Nie |  |
|Batch | Tak | Tak | Nie |  |
|Usługa Batch — sztuczna inteligencja | Nie | Nie | Nie |  |
|Usługa Blockchain | Nie | Tak | Nie |  |
|Blueprints | Nie | Nie | Nie |  |
|Usługa bota | Nie | Nie | Nie |  |
|Cloud Services | Tak | Tak | Nie | Agent wymagany do monitorowania systemu operacyjnego gościa i przepływów pracy.  |
|Cloud Shell | Nie | Nie | Nie |  |
|Cognitive Services | Tak | Tak | Nie |  |
|Container Instances | Tak | Nie | Nie |  |
|Container Registry | Tak | Tak | Nie |  |
|Content Delivery Network (CDN) | Nie | Tak | Nie |  |
|Cosmos DB | Tak | Tak | [Tak](insights/cosmosdb-insights-overview.md) |  |
|Cost Management | Nie | Nie | Nie |  |
|Data Box | Nie | Nie | Nie |  |
|Data Catalog Gen2 | Nie | Nie | Nie |  |
|Eksplorator danych | Tak | Tak | Nie |  |
|Data Factory | Tak | Tak | Nie |  |
|Data Factory wersja 2 | Nie | Tak | Nie |  |
|Data Share | Nie | Nie | Nie |  |
|Database for MariaDB | Tak | Tak | Nie |  |
|Database for MySQL | Tak | Tak | Nie |  |
|Database for PostgreSQL | Tak | Tak | Nie |  |
|Database Migration Service | Nie | Nie | Nie |  |
|Databricks | Nie | Tak | Nie |  |
|Ochrona przed atakami DDoS | Tak | Tak | Nie |  |
|DevOps | Nie | Nie | Nie |  |
|DNS | Tak | Nie | Nie |  |
|Nazwy domen | Nie | Nie | Nie |  |
|Dps | Nie | Nie | Nie |  |
|Dynamics 365 Customer Engagement | Nie | Nie | Nie |  |
|Dynamics 365 Finance and Operations | Nie | Nie | Nie |  |
|Event Grid | Tak | Nie | Nie |  |
|Event Hubs | Tak | Tak | Nie |  |
|ExpressRoute | Tak | Tak | Nie |  |
|Firewall | Tak | Tak | Nie |  |
|Front Door | Tak | Tak | Nie |  |
|Funkcje | Tak | Tak | Nie |  |
|HDInsight | Nie | Tak | Nie |  |
|HPC Cache | Nie | Nie | Nie |  |
|Information Protection | Nie | Tak | Nie |  |
|Intune | Nie | Tak | Nie |  |
|IoT Central | Nie | Nie | Nie |  |
|Usługa IoT Hub | Tak | Tak | Nie |  |
|Key Vault | Tak | Tak | [Tak](./insights/key-vault-insights-overview.md) |  |
|Kubernetes Service (AKS) | Nie | Nie | [Tak](containers/container-insights-overview.md)  |  |
|Load Balancer | Tak | Nie | Nie |  |
|Logic Apps | Tak | Tak | Nie |  |
|Usługa Machine Learning | Nie | Nie | Nie |  |
|Aplikacje zarządzane  | Nie | Nie | Nie |  |
|Maps  | Nie | Nie | Nie |  |
|Media Services | Tak | Tak | Nie |  |
|Microsoft Managed Desktop | Nie | Nie | Nie |  |
|Microsoft PowerApps | Nie | Nie | Nie |  |
|Microsoft Social Engagement | Nie | Nie | Nie |  |
|Usługa Microsoft Stream | Tak | Tak | Nie |  |
|Migrate | Nie | Nie | Nie |  |
|Uwierzytelnianie wieloskładnikowe | Nie | Tak | Nie |  |
|Network Watcher | Tak | Tak | Nie |  |
|Notification Hubs | Tak | Nie | Nie |  |
|Open Datasets | Nie | Nie | Nie |  |
|Zasady | Nie | Nie | Nie |  |
|Power Automate | Nie | Nie | Nie |  |
|Power BI Embedded | Tak | Tak | Nie |  |
|Private Link | Nie | Nie | Nie |  |
|Platforma komunikacji buforu projektu | Nie | Nie | Nie |  |
|Red Hat OpenShift | Nie | Nie | Nie |  |
|Pamięć podręczna Redis | Tak | Tak | [Tak](insights/redis-cache-insights-overview.md) | |
|Resource Graph | Nie | Nie | Nie |  |
|Resource Manager | Nie | Nie | Nie |  |
|Wyszukiwanie detaliczne — przez Bing | Nie | Nie | Nie |  |
|Wyszukaj | Tak | Tak | Nie |  |
|Service Bus | Tak | Tak | Nie |  |
|Service Fabric | Nie | Tak | Nie | Agent wymagany do monitorowania systemu operacyjnego gościa i przepływów pracy.  |
|Portal rejestracji | Nie | Nie | Nie |  |
|Site Recovery | Nie | Tak | Nie |  |
|Spring Cloud Service | Nie | Nie | Nie |  |
|Azure Synapse Analytics | Tak | Tak | Nie |  |
|SQL Database | Tak | Tak | Nie |  |
|SQL Server Stretch Database | Tak | Tak | Nie |  |
|Stos | Nie | Nie | Nie |  |
|Storage | Tak | Nie | [Tak](insights/storage-insights-overview.md) |  |
|Pamięć podręczna magazynu | Nie | Nie | Nie |  |
|Usługi synchronizacji magazynu | Nie | Nie | Nie |  |
|Stream Analytics | Tak | Tak | Nie |  |
|Time Series Insights | Tak | Tak | Nie |  |
|Tina | Nie | Nie | Nie |  |
|Traffic Manager | Tak | Tak | Nie |  |
|Drukowanie uniwersalne | Nie | Nie | Nie |  |
|Virtual Machine Scale Sets | Nie | Tak | [Tak](vm/vminsights-overview.md) | Agent wymagany do monitorowania systemu operacyjnego gościa i przepływów pracy. |
|Virtual Machines | Tak | Tak | [Tak](vm/vminsights-overview.md) | Agent wymagany do monitorowania systemu operacyjnego gościa i przepływów pracy. |
|Virtual Network | Tak | Tak | [Tak](insights/network-insights-overview.md) |  |
|Virtual Network — dzienniki przepływu sieciowej organizacji zabezpieczeń | Nie | Tak | Nie |  |
|VPN Gateway | Tak | Tak | Nie |  |
|Windows Virtual Desktop | Nie | Tak | Nie |  |

## <a name="virtual-machine-agents"></a>Agenci maszyny wirtualnej
W poniższej tabeli wymieniono agentów, które mogą zbierać dane z systemu operacyjnego gościa maszyn wirtualnych i wysyłać dane do monitora. Każdy agent może zbierać różne dane i wysyłać je do metryk lub dzienników w Azure Monitor. 

Szczegółowe informacje na temat Azure Monitor [zbieranych](agents/agents-overview.md) przez poszczególnych agentów zawiera temat Overview of Azure Monitor agents (Omówienie agentów sieciowych).

| Agent |  Metryki | Dzienniki |
|:---|:---|:---|:---|
| [Azure Monitor agenta (wersja zapoznawcza)](agents/azure-monitor-agent-overview.md) | Tak | Tak |
| [Agent usługi Log Analytics](agents/log-analytics-agent.md) | Nie | Tak|
| [Rozszerzenie diagnostyczne](agents/diagnostics-extension-overview.md) | Tak | Nie |
| [Agent telegrafu](essentials/collect-custom-metrics-linux-telegraf.md) | Tak | Nie |
| [Agent zależności](vm/vminsights-enable-overview.md) | Nie | Tak |


## <a name="product-integrations"></a>Integracje produktów
Usługi i rozwiązania w poniższej tabeli przechowują swoje dane w obszarze roboczym usługi Log Analytics, dzięki czemu mogą być analizowane przy użyciu innych danych dzienników zebranych przez Azure Monitor.

| Produkt/usługa | Opis |
|:---|:---|
| [Azure Automation](../automation/index.yml) | Zarządzanie aktualizacjami systemu operacyjnego i śledzenie zmian na komputerach z systemami Windows i Linux. Zobacz [Change Tracking](../automation/change-tracking/overview.md) i [Update Management](../automation/update-management/overview.md). |
| [Azure Information Protection ](/azure/information-protection/) | Klasyfikowanie i opcjonalnie ochrona dokumentów i wiadomości e-mail. Zobacz [Centralne raportowanie dla Azure Information Protection](/azure/information-protection/reports-aip#configure-a-log-analytics-workspace-for-the-reports). |
| [Azure Security Center](../security-center/index.yml) | Zbieranie i analizowanie zdarzeń zabezpieczeń oraz wykonywanie analizy zagrożeń. Zobacz [Zbieranie danych w Azure Security Center](../security-center/security-center-enable-data-collection.md) |
| [Azure Sentinel](../sentinel/index.yml) | Nawiązuje połączenie z różnymi źródłami, w tym z usługami Office 365 i Amazon Web Services Cloud Trail. Zobacz [Connect data sources (Łączenie źródeł danych).](../sentinel/connect-data-sources.md) |
| [Microsoft Intune](/intune/) | Utwórz ustawienie diagnostyczne, aby wysyłać dzienniki do Azure Monitor. Zobacz [Wysyłanie danych dziennika do magazynu, centrów zdarzeń lub analizy dzienników w usłudze Intune (wersja zapoznawcza).](/intune/fundamentals/review-logs-using-azure-monitor)  |
| Sieć  | [Network Performance Monitor](insights/network-performance-monitor.md) — monitoruj łączność sieciową i wydajność z punktami końcowymi usług i aplikacji.<br>[Azure Application Gateway](insights/azure-networking-analytics.md#azure-application-gateway-analytics) — analizowanie dzienników i metryk z Azure Application Gateway.<br>[Analiza ruchu](../network-watcher/traffic-analytics.md) — analizuje Network Watcher przepływu sieciowej grupy zabezpieczeń w celu zapewnienia wglądu w przepływ ruchu w chmurze platformy Azure. |
| [Office 365](insights/solution-office-365.md) | Monitorowanie środowiska usługi Office 365. Zaktualizowano wersję z ulepszonym dołączaniem dostępnym za pośrednictwem Azure Sentinel. |
| [SQL Analytics](insights/azure-sql.md) | Monitorowanie wydajności baz danych Azure SQL i usługi SQL Managed Instance na dużą skalę i w wielu subskrypcjach. |
| [Surface Hub](insights/surface-hubs.md) | Śledź kondycję i użycie Surface Hub urządzeń. |
| [System Center Operations Manager](/system-center/scom) | Zbieraj dane z Operations Manager, łącząc ich grupę zarządzania z Azure Monitor. Zobacz [Łączenie Operations Manager z Azure Monitor](agents/om-agents.md)<br> Oceń ryzyko i kondycję grupy zarządzania System Center Operations Manager za pomocą [Operations Manager Assessment.](insights/scom-assessment.md) |
| [Microsoft Teams Rooms](/microsoftteams/room-systems/azure-monitor-deploy) | Zintegrowane, end-to-end zarządzanie urządzeniami Microsoft Teams Rooms. |
| [Visual Studio App Center](/appcenter/) | Kompilowanie, testowanie i rozpowszechnianie aplikacji, a następnie monitorowanie ich stanu i użycia. Zobacz [Rozpoczynanie analizowania aplikacji mobilnej za pomocą App Center i Application Insights](app/mobile-center-quickstart.md). |
| Windows | [Windows Update zgodności —](/windows/deployment/update/update-compliance-get-started) ocena uaktualnień pulpitu systemu Windows.<br>[Desktop Analytics](/configmgr/desktop-analytics/overview) — integruje się z usługą Menedżer konfiguracji w celu zapewnienia wglądu i analizy w celu podejmowania bardziej świadomych decyzji dotyczących gotowości klientów systemu Windows do aktualizacji. |



## <a name="other-solutions"></a>Inne rozwiązania
Inne rozwiązania są dostępne do monitorowania różnych aplikacji i usług, ale aktywne tworzenie zostało zatrzymane i mogą nie być dostępne we wszystkich regionach. Są one objęte umową o poziomie usług pozyskiwania danych usługi Azure Log Analytics.

| Rozwiązanie | Opis |
|:---|:---|
| [Sprawdzanie kondycji usługi Active Directory](insights/ad-assessment.md) | Oceń ryzyko i kondycję środowisk usługi Active Directory. |
| [Stan replikacji usługi Active Directory](insights/ad-replication-status.md) | Regularnie monitoruje środowisko usługi Active Directory pod celu nieudanych replikacji. |
| [Analiza dzienników aktywności](essentials/activity-log.md#activity-log-analytics-monitoring-solution) | Wyświetlanie wpisów dziennika aktywności. |
| [DNS Analytics (wersja zapoznawcza)](insights/dns-analytics.md) | Zbiera, analizuje i koreluje dzienniki analityczne i inspekcji dns systemu Windows oraz inne powiązane dane z serwerów DNS. |
| [Cloud Foundry](../cloudfoundry/cloudfoundry-oms-nozzle.md) | Zbieraj, wyświetlaj i analizuj Cloud Foundry metryki kondycji i wydajności systemu w wielu wdrożeniach. |
| [Containers](containers/containers.md) | Wyświetlanie hostów kontenerów platformy Docker i systemu Windows oraz zarządzanie nimi. |
| [Oceny na żądanie](/services-hub/health/getting_started_with_on_demand_assessments) | Ocenianie i optymalizowanie dostępności, zabezpieczeń i wydajności lokalnych, hybrydowych i chmurowych środowisk technologicznych firmy Microsoft. |
| [Sprawdzanie kondycji bazy danych SQL](insights/sql-assessment.md) | Oceń ryzyko i kondycję SQL Server środowiskach.  |
| [Dane o komunikacji sieciowej](insights/wire-data.md) | Skonsolidowane dane dotyczące sieci i wydajności zebrane z komputerów połączonych z systemem Windows i połączonych z systemem Linux za pomocą agenta usługi Log Analytics. |

## <a name="third-party-integration"></a>Integracja z firmami trzecimi

| Rozwiązanie | Opis |
|:---|:---|
| [ITSM](alerts/itsmc-overview.md) | Łącznik zarządzania usługami IT (ITSMC, IT Service Management Connector) umożliwia łączenie platformy Azure z obsługiwanym produktem/obsługiwaną usługą zarządzania usługami IT (ITSM).  |


## <a name="resources-outside-of-azure"></a>Zasoby spoza platformy Azure
Azure Monitor zbierać dane z zasobów spoza platformy Azure przy użyciu metod wymienionych w poniższej tabeli.

| Zasób | Metoda |
|:---|:---|
| Aplikacje | Monitorowanie aplikacji internetowych poza platformą Azure przy użyciu Application Insights. Zobacz [Co to jest Application Insights?](./app/app-insights-overview.md). |
| Maszyny wirtualne | Użyj agentów, aby zbierać dane z systemu operacyjnego gościa maszyn wirtualnych w innych środowiskach w chmurze lub lokalnie. Zobacz [Overview of Azure Monitor agents (Omówienie Azure Monitor agentów).](agents/agents-overview.md) |
| Klient interfejsu API REST | Dostępne są oddzielne interfejsy API do zapisu danych w Azure Monitor i metryk z dowolnego klienta interfejsu API REST. Zobacz Wysyłanie danych dziennika do usługi Azure Monitor za pomocą interfejsu API modułu zbierającego dane [HTTP](logs/data-collector-api.md) dla dzienników i Wysyłanie metryk niestandardowych dla zasobu platformy Azure do magazynu metryk platformy Azure Monitor przy użyciu interfejsu [API REST](essentials/metrics-store-custom-rest-api.md) dla metryk. |



## <a name="next-steps"></a>Następne kroki

- Przeczytaj więcej na temat platformy Azure Monitor danych, która przechowuje dzienniki i [metryki zbierane przez szczegółowe informacje i rozwiązania.](data-platform.md)
- Ukończ [samouczek dotyczący monitorowania zasobu platformy Azure.](essentials/tutorial-resource-logs.md)
- Ukończ [samouczek dotyczący pisania zapytania dziennika w celu analizowania danych w Azure Monitor Dzienniki.](essentials/tutorial-resource-logs.md)
- Ukończ [samouczek dotyczący tworzenia wykresu metryk w celu](essentials/tutorial-metrics-explorer.md)analizowania danych w Azure Monitor Metryki.

