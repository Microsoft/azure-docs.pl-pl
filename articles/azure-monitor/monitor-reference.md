---
title: Co jest monitorowane przez Azure Monitor
description: Informacje o wszystkich usługach i innych zasobach monitorowanych przez Azure Monitor.
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 08/15/2020
ms.openlocfilehash: 4bf792dd02e7cddcc40ef868e4a602fdb03ab3c6
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102052283"
---
# <a name="what-is-monitored-by-azure-monitor"></a>Co jest monitorowane przez Azure Monitor?
W tym artykule opisano różne aplikacje i usługi monitorowane przez program Azure Monitor. 

## <a name="insights-and-core-solutions"></a>Szczegółowe informacje i rozwiązania podstawowe
Podstawowe informacje i rozwiązania są uważane za część Azure Monitor i przestrzegają umów dotyczących poziomu usług dla platformy Azure. Są one obsługiwane we wszystkich regionach świadczenia usługi Azure, w których Azure Monitor jest dostępna.

### <a name="insights"></a>Insights

Szczegółowe informacje zapewniają dostosowane środowisko monitorowania dla określonych aplikacji i usług. Zbiera i analizuje zarówno dzienniki, jak i metryki.

| Szczegółowe informacje | Opis |
|:---|:---|
| [Application Insights](app/app-insights-overview.md) | Rozszerzalna usługa zarządzania wydajnością aplikacji (APM) do monitorowania działającej aplikacji sieci Web na dowolnej platformie. |
| [Szczegółowe informacje o kontenerze](containers/container-insights-overview.md) | Monitoruje wydajność obciążeń kontenera wdrożonych do Azure Container Instances lub zarządzanych klastrów Kubernetes hostowanych w usłudze Azure Kubernetes Service (AKS). |
| [Azure Monitor Cosmos DB](insights/cosmosdb-insights-overview.md) | Zapewnia widok ogólnej wydajności, niepowodzeń, pojemności i kondycji operacyjnej wszystkich zasobów Azure Cosmos DB w ujednoliconym środowisku interaktywnym. |
| [Azure Monitor dla sieci (wersja zapoznawcza)](insights/network-insights-overview.md) | Zapewnia kompleksowy wgląd w kondycję i metryki dla wszystkich zasobów sieciowych. Funkcja wyszukiwania zaawansowanego pomaga identyfikować zależności zasobów i umożliwiać scenariusze, takie jak identyfikowanie zasobów, które obsługują witrynę sieci Web, przez zwykłe wyszukiwanie nazwy witryny sieci Web. |
[Azure Monitor dla grup zasobów (wersja zapoznawcza)](insights/resource-group-insights.md) |  Klasyfikacja i Diagnozuj wszystkie problemy napotykane przez poszczególne zasoby, a także zaoferując kontekście kondycji i wydajności grupy zasobów jako całości. |
| [Azure Monitor magazynu](insights/storage-insights-overview.md) | Zapewnia kompleksowe monitorowanie kont usługi Azure Storage, zapewniając ujednolicony wgląd w wydajność, wydajność i dostępność usług Azure Storage. |
| [Szczegółowe informacje o maszynie wirtualnej](vm/vminsights-overview.md) | Monitoruje maszyny wirtualne platformy Azure i zestawy skalowania maszyn wirtualnych na dużą skalę. Analizuje ona wydajność i kondycję Twoich maszyn wirtualnych z systemami Windows i Linux oraz monitoruje ich procesy i zależności od innych zasobów oraz procesów zewnętrznych. |
| [Azure Monitor dla Key Vault (wersja zapoznawcza)](./insights/key-vault-insights-overview.md) | Zapewnia kompleksowe monitorowanie magazynów kluczy, udostępniając ujednolicony wgląd w Key Vault żądania, wydajność, błędy i opóźnienia. |
| [Azure Monitor dla usługi Azure cache for Redis (wersja zapoznawcza)](insights/redis-cache-insights-overview.md) |  Zapewnia ujednolicony, interaktywny widok ogólnej wydajności, niepowodzeń, pojemności i kondycji operacyjnej. |


### <a name="core-solutions"></a>Podstawowe rozwiązania

Rozwiązania są oparte na zapytaniach i widokach dzienników dostosowanych do określonej aplikacji lub usługi. Gromadzą i analizują tylko dzienniki i są one przestarzałe w czasie w celu wglądu w szczegółowe dane.

| Rozwiązanie | Opis |
|:---|:---|
| [Kondycja agenta](insights/solution-agenthealth.md) | Analizuj kondycję i konfigurację agentów Log Analytics. |
| [Zarządzanie alertami](insights/alert-management-solution.md) | Analizuj alerty zebrane z System Center Operations Manager, Nagios lub Zabbix. |
| [Service Map](vm/service-map.md) | Automatycznie odnajduje składniki aplikacji w systemach Windows i Linux oraz mapuje komunikację między usługami. |



## <a name="azure-services"></a>Usługi platformy Azure
W poniższej tabeli wymieniono usługi platformy Azure i zebrane przez nie dane do Azure Monitor. 

- Metryki — usługa automatycznie zbiera metryki na Azure Monitor metryki. 
- Dzienniki — usługa obsługuje ustawienia diagnostyczne, które mogą zbierać dzienniki platformy i metryki do dzienników Azure Monitor.
- Wgląd w szczegółowe dane dostępne dla usługi, która zapewnia dostosowane środowisko monitorowania dla usługi.

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
|Usługa zaświadczania | Nie | Nie | Nie |  |
|Automation | Tak | Tak | Nie |  |
|Azure Service Manager (frontonu reddog) | Nie | Nie | Nie |  |
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
|Data Factory v2 | Nie | Tak | Nie |  |
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
|DOKUMENTY | Nie | Nie | Nie |  |
|Dynamics 365 — zaangażowanie klienta | Nie | Nie | Nie |  |
|Dynamics 365 — Finanse i operacje | Nie | Nie | Nie |  |
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
|Multi-Factor Authentication | Nie | Tak | Nie |  |
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
|Wyszukiwanie detaliczne — według Bing | Nie | Nie | Nie |  |
|Wyszukaj | Tak | Tak | Nie |  |
|Service Bus | Tak | Tak | Nie |  |
|Service Fabric | Nie | Tak | Nie | Agent wymagany do monitorowania systemu operacyjnego gościa i przepływów pracy.  |
|Portal rejestracji | Nie | Nie | Nie |  |
|Site Recovery | Nie | Tak | Nie |  |
|Usługa w chmurze wiosennej | Nie | Nie | Nie |  |
|Azure Synapse Analytics | Tak | Tak | Nie |  |
|Baza danych SQL | Tak | Tak | Nie |  |
|SQL Server Stretch Database | Tak | Tak | Nie |  |
|Stos | Nie | Nie | Nie |  |
|Storage | Tak | Nie | [Tak](insights/storage-insights-overview.md) |  |
|Pamięć podręczna magazynu | Nie | Nie | Nie |  |
|Usługi synchronizacji magazynu | Nie | Nie | Nie |  |
|Stream Analytics | Tak | Tak | Nie |  |
|Time Series Insights | Tak | Tak | Nie |  |
|TINA | Nie | Nie | Nie |  |
|Traffic Manager | Tak | Tak | Nie |  |
|Wydruk uniwersalny | Nie | Nie | Nie |  |
|Virtual Machine Scale Sets | Nie | Tak | [Tak](vm/vminsights-overview.md) | Agent wymagany do monitorowania systemu operacyjnego gościa i przepływów pracy. |
|Virtual Machines | Tak | Tak | [Tak](vm/vminsights-overview.md) | Agent wymagany do monitorowania systemu operacyjnego gościa i przepływów pracy. |
|Virtual Network | Tak | Tak | [Tak](insights/network-insights-overview.md) |  |
|Dzienniki przepływów Virtual Network sieciowej grupy zabezpieczeń | Nie | Tak | Nie |  |
|VPN Gateway | Tak | Tak | Nie |  |
|Windows Virtual Desktop | Nie | Nie | Nie |  |

## <a name="virtual-machine-agents"></a>Agenci maszyny wirtualnej
W poniższej tabeli wymieniono agentów, którzy mogą zbierać dane z systemu operacyjnego gościa maszyn wirtualnych i wysyłać dane do monitorowania. Każdy agent może zbierać różne dane i wysyłać je do metryk lub dzienników w Azure Monitor. 

Aby uzyskać szczegółowe informacje na temat danych, które mogą zbierać każdy agent, zobacz [Omówienie agentów Azure monitor](agents/agents-overview.md) .

| Agent |  Metryki | Dzienniki |
|:---|:---|:---|:---|
| [Agent Azure Monitor (wersja zapoznawcza)](agents/azure-monitor-agent-overview.md) | Tak | Tak |
| [Agent usługi Log Analytics](agents/log-analytics-agent.md) | Nie | Tak|
| [Rozszerzenie diagnostyczne](agents/diagnostics-extension-overview.md) | Tak | Nie |
| [Agent telegraf](essentials/collect-custom-metrics-linux-telegraf.md) | Tak | Nie |
| [Agent zależności](vm/vminsights-enable-overview.md) | Nie | Tak |


## <a name="product-integrations"></a>Integracji produktów
Usługi i rozwiązania w poniższej tabeli przechowują swoje dane w Log Analytics obszarze roboczym, dzięki czemu można je analizować przy użyciu innych danych dzienników zebranych przez Azure Monitor.

| Produkt/usługa | Opis |
|:---|:---|
| [Azure Automation](../automation/index.yml) | Zarządzaj aktualizacjami systemu operacyjnego i Śledź zmiany na komputerach z systemem Windows i Linux. Zobacz [Change Tracking](../automation/change-tracking/overview.md) i [Update Management](../automation/update-management/overview.md). |
| [Azure Information Protection ](/azure/information-protection/) | Klasyfikowanie i opcjonalne chronienie dokumentów i wiadomości e-mail. Zobacz [centralne raportowanie dla Azure Information Protection](/azure/information-protection/reports-aip#configure-a-log-analytics-workspace-for-the-reports). |
| [Azure Security Center](../security-center/index.yml) | Zbieraj i Analizuj zdarzenia zabezpieczeń oraz przeprowadzaj analizę zagrożeń. Zobacz [zbieranie danych w Azure Security Center](../security-center/security-center-enable-data-collection.md) |
| [Azure Sentinel](../sentinel/index.yml) | Łączy się z różnymi źródłami, w tym z pakietem Office 365 i Amazon Web Services w chmurze. Zobacz [łączenie ze źródłami danych](../sentinel/connect-data-sources.md). |
| [Microsoft Intune](/intune/) | Utwórz ustawienie diagnostyczne, aby wysłać dzienniki do Azure Monitor. Zobacz [wysyłanie danych dzienników do magazynu, centrów zdarzeń lub usługi log Analytics w usłudze Intune (wersja zapoznawcza)](/intune/fundamentals/review-logs-using-azure-monitor).  |
| Sieć  | [Network Performance Monitor](insights/network-performance-monitor.md) — Monitorowanie łączności sieciowej i wydajności do punktów końcowych usługi i aplikacji.<br>[Azure Application Gateway](insights/azure-networking-analytics.md#azure-application-gateway-analytics) — analizowanie dzienników i metryk z usługi Azure Application Gateway.<br>[Analiza ruchu](../network-watcher/traffic-analytics.md) — analizuje dzienniki przepływu sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń) Network Watcher, aby uzyskać wgląd w przepływ ruchu w chmurze platformy Azure. |
| [Office 365](insights/solution-office-365.md) | Monitoruj środowisko pakietu Office 365. Zaktualizowana wersja z ulepszonym dołączaniem dostępnym za pomocą platformy Azure — wskaźnikiem. |
| [Analiza SQL](insights/azure-sql.md) | Monitoruj wydajność baz danych SQL Azure i wystąpień zarządzanych SQL na dużą skalę i w wielu subskrypcjach. |
| [Surface Hub](insights/surface-hubs.md) | Śledź kondycję i użycie urządzeń Surface Hub. |
| [System Center Operations Manager](/system-center/scom) | Zbierz dane z agentów Operations Manager, łącząc ich grupę zarządzania z Azure Monitor. Zobacz [łączenie Operations Manager do Azure monitor](agents/om-agents.md)<br> Oceń ryzyko i kondycję System Center Operations Manager grupy zarządzania, korzystając z rozwiązania do [oceny Operations Manager](insights/scom-assessment.md) . |
| [Pokoje Microsoft Teams](/microsoftteams/room-systems/azure-monitor-deploy) | Zintegrowane, kompleksowe zarządzanie urządzeniami pokojów Microsoft Teams. |
| [Visual Studio App Center](/appcenter/) | Kompiluj, Testuj i Dystrybuuj aplikacje, a następnie Monitoruj ich status i użycie. Zobacz [Rozpoczynanie analizowania aplikacji mobilnej przy użyciu App Center i Application Insights](app/mobile-center-quickstart.md). |
| Windows | [Windows Update zgodność](/windows/deployment/update/update-compliance-get-started) — Oceń uaktualnienia pulpitu systemu Windows.<br>[Analiza pulpitu](/configmgr/desktop-analytics/overview) — integruje się z usługą Configuration Manager, aby zapewnić wgląd i inteligencję w celu podejmowania bardziej świadomych decyzji o gotowości aktualizacji dla klientów z systemem Windows. |



## <a name="other-solutions"></a>Inne rozwiązania
Inne rozwiązania są dostępne do monitorowania różnych aplikacji i usług, ale aktywne programowanie zostało zatrzymane i mogą nie być dostępne we wszystkich regionach. Są one objęte umową dotyczącą poziomu usług pozyskiwania danych Log Analytics platformy Azure.

| Rozwiązanie | Opis |
|:---|:---|
| [Sprawdzanie kondycji Active Directory](insights/ad-assessment.md) | Oceń ryzyko i kondycję środowisk Active Directoryych. |
| [Active Directory stanu replikacji](insights/ad-replication-status.md) | Regularne monitorowanie środowiska Active Directory pod kątem błędów replikacji. |
| [Analiza dzienników aktywności](essentials/activity-log.md#activity-log-analytics-monitoring-solution) | Wyświetlanie wpisów dziennika aktywności. |
| [DNS Analytics (wersja zapoznawcza)](insights/dns-analytics.md) | Gromadzi, analizuje i skorelowanie dzienników analitycznych i inspekcji systemu Windows DNS oraz innych powiązanych danych z serwerów DNS. |
| [Cloud Foundry](../cloudfoundry/cloudfoundry-oms-nozzle.md) | Zbieraj, Wyświetlaj i Analizuj metryki dotyczące kondycji systemu Cloud Foundry i wydajności w wielu wdrożeniach. |
| [Containers](containers/containers.md) | Wyświetlanie hostów platformy Docker i kontenerów systemu Windows oraz zarządzanie nimi. |
| [Oceny na żądanie](/services-hub/health/getting_started_with_on_demand_assessments) | Oceń i Optymalizuj dostępność, bezpieczeństwo i wydajność środowisk lokalnych, hybrydowych i chmurowych firmy Microsoft. |
| [Sprawdzenie kondycji SQL](insights/sql-assessment.md) | Oceń ryzyko i kondycję środowisk SQL Serverych.  |
| [Dane o komunikacji sieciowej](insights/wire-data.md) | Skonsolidowane dane dotyczące sieci i wydajności zebrane z komputerów połączonych z systemem Windows i Linux z agentem Log Analytics. |

## <a name="third-party-integration"></a>Integracja z firmą innej firmy

| Rozwiązanie | Opis |
|:---|:---|
| [ITSM](alerts/itsmc-overview.md) | Łącznik zarządzania usługami IT (ITSMC, IT Service Management Connector) umożliwia łączenie platformy Azure z obsługiwanym produktem/obsługiwaną usługą zarządzania usługami IT (ITSM).  |


## <a name="resources-outside-of-azure"></a>Zasoby poza platformą Azure
Azure Monitor może zbierać dane z zasobów poza platformą Azure przy użyciu metod wymienionych w poniższej tabeli.

| Zasób | Metoda |
|:---|:---|
| Aplikacje | Monitoruj aplikacje sieci Web poza platformą Azure przy użyciu Application Insights. Zobacz [co to jest Application Insights?](./app/app-insights-overview.md). |
| Maszyny wirtualne | Użyj agentów do zbierania danych z systemu operacyjnego gościa maszyn wirtualnych w innych środowiskach w chmurze lub lokalnie. Zobacz [Omówienie agentów Azure monitor](agents/agents-overview.md). |
| Klient interfejsu API REST | Dostępne są oddzielne interfejsy API do zapisywania danych do Azure Monitor dzienników i metryk z dowolnego klienta interfejsu API REST. Zobacz [wysyłanie danych dzienników do Azure monitor za pomocą interfejsu API modułu zbierającego dane protokołu HTTP](logs/data-collector-api.md) dla dzienników i [wysyłania niestandardowych metryk dla zasobu platformy Azure do magazynu metryk Azure monitor przy użyciu interfejsu API REST](essentials/metrics-store-custom-rest-api.md) dla metryk. |



## <a name="next-steps"></a>Następne kroki

- Przeczytaj więcej na temat [platformy danych Azure monitor, w której są przechowywane dzienniki i metryki zbierane przez usługi Insights i rozwiązania](data-platform.md).
- Ukończ [Samouczek dotyczący monitorowania zasobu platformy Azure](essentials/tutorial-resource-logs.md).
- Ukończ samouczek dotyczący tworzenia [zapytania dziennika w celu analizowania danych w dziennikach Azure monitor](essentials/tutorial-resource-logs.md).
- Ukończ samouczek dotyczący [tworzenia wykresu metryk do analizowania danych w metrykach Azure monitor](essentials/tutorial-metrics-explorer.md).

