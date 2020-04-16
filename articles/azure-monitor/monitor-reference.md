---
title: Co jest monitorowane przez usługę Azure Monitor
description: Odwołanie do wszystkich usług i innych zasobów monitorowanych przez usługę Azure Monitor.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/17/2020
ms.openlocfilehash: 27503defa23b2a35e767156139228aa2e177652f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405176"
---
# <a name="what-is-monitored-by-azure-monitor"></a>Co jest monitorowane przez usługę Azure Monitor?
W tym artykule opisano różne aplikacje i usługi, które są monitorowane przez usługę Azure Monitor. 

## <a name="insights-and-core-solutions"></a>Spostrzeżenia i podstawowe rozwiązania
Podstawowe informacje i rozwiązania są uważane za część usługi Azure Monitor i postępuj zgodnie z umowami pomocy technicznej i poziomu usług dla platformy Azure. Są one obsługiwane we wszystkich regionach platformy Azure, w których usługa Azure Monitor jest dostępna.

### <a name="insights"></a>Insights

Szczegółowe informacje zapewniają dostosowane środowisko monitorowania dla określonych aplikacji i usług. Zbierają i analizują zarówno dzienniki, jak i metryki.

| Szczegółowe informacje | Opis |
|:---|:---|
| [Application Insights](app/app-insights-overview.md) | Usługa zarządzania wydajnością aplikacji (APM) rozszerzalna w celu monitorowania aplikacji sieci web na żywo na dowolnej platformie. |
| [Usługa Azure Monitor dla kontenerów](insights/container-insights-overview.md) | Monitoruje wydajność obciążeń kontenerów wdrożonych w wystąpieniach kontenerów platformy Azure lub zarządzanych klastrach kubernetes hostowanych w usłudze Azure Kubernetes Service (AKS). |
| [Usługa Azure Monitor for Cosmos DB (wersja zapoznawcza)](insights/cosmosdb-insights-overview.md) | Zapewnia widok ogólnej wydajności, awarii, pojemności i kondycji operacyjnej wszystkich zasobów usługi Azure Cosmos DB w ujednoliconym interaktywnym środowiskach. |
| [Usługa Azure Monitor dla sieci (wersja zapoznawcza)](insights/network-insights-overview.md) | Zapewnia kompleksowy widok kondycji i metryki dla wszystkich zasobów sieciowych. Zaawansowane funkcje wyszukiwania ułatwiają identyfikowanie zależności zasobów, umożliwiając scenariusze, takie jak identyfikowanie zasobów hostujących witrynę, po prostu wyszukując nazwę witryny. |
[Usługa Azure Monitor dla grup zasobów (wersja zapoznawcza)](insights/resource-group-insights.md) |  Klasyfikowanie i diagnozowanie wszelkich problemów napotkanych przez poszczególne zasoby, jednocześnie oferując kontekst kondycji i wydajności grupy zasobów jako całości. |
| [Usługa Azure Monitor dla magazynu (wersja zapoznawcza)](insights/storage-insights-overview.md) | Zapewnia kompleksowe monitorowanie kont usługi Azure Storage, zapewniając ujednolicony widok wydajności, pojemności i dostępności usług Azure Storage. |
| [Usługa Azure Monitor dla maszyn wirtualnych](insights/container-insights-overview.md) | Monitoruje maszyny wirtualne platformy Azure (VM) i zestawy skalowania maszyny wirtualnej na dużą skalę. Analizuje ona wydajność i kondycję Twoich maszyn wirtualnych z systemami Windows i Linux oraz monitoruje ich procesy i zależności od innych zasobów oraz procesów zewnętrznych. |

### <a name="core-solutions"></a>Podstawowe rozwiązania

Rozwiązania są oparte na zapytaniach dziennika i widokach dostosowanych do określonej aplikacji lub usługi. Zbierają i analizują tylko dzienniki i są przestarzałe w czasie na rzecz wglądu.

| Rozwiązanie | Opis |
|:---|:---|
| [Kondycja agenta](insights/solution-agenthealth.md) | Analizowanie kondycji i konfiguracji agentów usługi Log Analytics. |
| [Zarządzanie alertami](platform/alert-management-solution.md) | Analizuj alerty zebrane z Programu Operacyjnego System Center, Nagios lub Zabbix. |
| [Mapa usługi](insights/service-map.md) | Automatycznie odnajduje składniki aplikacji w systemach Windows i Linux oraz mapuje komunikację między usługami. |



## <a name="azure-services"></a>Usługi platformy Azure
W poniższej tabeli wymieniono usługi platformy Azure i dane, które zbierają w usłudze Azure Monitor. 

- Metryki — usługa automatycznie zbiera metryki do metryk usługi Azure Monitor. 
- Dzienniki — usługa obsługuje ustawienia diagnostyczne, które mogą zbierać dzienniki platformy i metryki do dzienników usługi Azure Monitor.
- Wgląd — jest dostępny wgląd dla usługi, która zapewnia dostosowane środowisko monitorowania dla usługi.

| Usługa | Metryki | Dzienniki | Szczegółowe informacje | Uwagi |
|:---|:---|:---|:---|:---|
|Usługa Active Directory | Nie | Tak | [Tak](../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md) |  |
|Active Directory B2C | Nie | Nie | Nie |  |
|Active Directory Domain Services | Nie | Tak | Nie |  |
|Dziennik aktywności | Nie | Tak | Nie | |
|Zaawansowana ochrona przed zagrożeniami | Nie | Nie | Nie |  |
|Advisor | Nie | Nie | Nie |  |
|Konstruktor sztucznej inteligencji | Nie | Nie | Nie |  |
|Analysis Services | Tak | Tak | Nie |  |
|Interfejs API dla standardu FHIR | Nie | Nie | Nie |  |
|API Management | Tak | Tak | Nie |  |
|App Service | Tak | Tak | Nie |  |
|AppConfig | Nie | Nie | Nie |  |
|Application Gateway | Tak | Tak | Nie |  |
|Usługa zaświadczania | Nie | Nie | Nie |  |
|Automatyzacja | Tak | Tak | Nie |  |
|Usługa Azure Service Manager (RDFE) | Nie | Nie | Nie |  |
|Backup | Nie | Tak | Nie |  |
|Bastion | Nie | Nie | Nie |  |
|Batch | Tak | Tak | Nie |  |
|Usługa Batch — sztuczna inteligencja | Nie | Nie | Nie |  |
|Blockchain Service | Nie | Tak | Nie |  |
|Strategie | Nie | Nie | Nie |  |
|Bot Service | Nie | Nie | Nie |  |
|Cloud Services | Tak | Tak | Nie | Agent wymagany do monitorowania systemu operacyjnego gościa i przepływów pracy.  |
|Cloud Shell | Nie | Nie | Nie |  |
|Cognitive Services | Tak | Tak | Nie |  |
|Container Instances | Tak | Nie | Nie |  |
|Container Registry | Tak | Tak | Nie |  |
|Sieć dostarczania zawartości (CDN) | Nie | Tak | Nie |  |
|Cosmos DB | Tak | Tak | [Tak](insights/cosmosdb-insights-overview.md) |  |
|Cost Management | Nie | Nie | Nie |  |
|Data Box | Nie | Nie | Nie |  |
|Katalog danych Gen2 | Nie | Nie | Nie |  |
|Data Explorer | Tak | Tak | Nie |  |
|Fabryka danych | Tak | Tak | Nie |  |
|Fabryka danych v2 | Nie | Tak | Nie |  |
|Data Share | Nie | Nie | Nie |  |
|Database for MariaDB | Tak | Tak | Nie |  |
|Database for MySQL | Tak | Tak | Nie |  |
|Database for PostgreSQL | Tak | Tak | Nie |  |
|Usługa migracji bazy danych | Nie | Nie | Nie |  |
|Databricks | Nie | Tak | Nie |  |
|DDoS Protection | Tak | Tak | Nie |  |
|DevOps | Nie | Nie | Nie |  |
|DNS | Tak | Nie | Nie |  |
|Nazwy domen | Nie | Nie | Nie |  |
|Dps | Nie | Nie | Nie |  |
|Dynamics 365 Zaangażowanie klientów | Nie | Nie | Nie |  |
|Dynamics 365 Finanse i operacje | Nie | Nie | Nie |  |
|Event Grid | Tak | Nie | Nie |  |
|Event Hubs | Tak | Tak | Nie |  |
|ExpressRoute | Tak | Tak | Nie |  |
|Zapora | Tak | Tak | Nie |  |
|Front Door | Tak | Tak | Nie |  |
|Funkcje | Tak | Tak | Nie |  |
|HDInsight | Nie | Tak | Nie |  |
|HPC Cache | Nie | Nie | Nie |  |
|Ochrona informacji | Nie | Tak | Nie |  |
|Usługa Intune | Nie | Tak | Nie |  |
|IoT Central | Nie | Nie | Nie |  |
|Usługa IoT Hub | Tak | Tak | Nie |  |
|Usługa Key Vault | Tak | Tak | Nie |  |
|Kubernetes Service (AKS) | Nie | Nie | [Tak](insights/container-insights-overview.md)  |  |
|Moduł równoważenia obciążenia | Tak | Tak | Nie |  |
|Logic Apps | Tak | Tak | Nie |  |
|Machine Learning Service | Nie | Nie | Nie |  |
|Aplikacje zarządzane  | Nie | Nie | Nie |  |
|Maps  | Nie | Nie | Nie |  |
|Media Services | Tak | Tak | Nie |  |
|Microsoft Flow | Nie | Nie | Nie |  |
|Microsoft Managed Desktop | Nie | Nie | Nie |  |
|Microsoft PowerApps | Nie | Nie | Nie |  |
|Microsoft Social Engagement | Nie | Nie | Nie |  |
|Usługa Microsoft Stream | Tak | Tak | Nie |  |
|Migrate (Migracja) | Nie | Nie | Nie |  |
|Multi-Factor Authentication | Nie | Tak | Nie |  |
|Network Watcher | Tak | Tak | Nie |  |
|Notification Hubs | Tak | Nie | Nie |  |
|Zestawy danych licencji Open | Nie | Nie | Nie |  |
|Zasady | Nie | Nie | Nie |  |
|Power BI | Tak | Tak | Nie |  |
|Power BI Embedded | Nie | Nie | Nie |  |
|Link prywatny | Nie | Nie | Nie |  |
|Platforma komunikacji z szpulą projektu | Nie | Nie | Nie |  |
|Red Hat OpenShift | Nie | Nie | Nie |  |
|Pamięć podręczna Redis | Tak | Tak | Nie |  |
|Wykres zasobów | Nie | Nie | Nie |  |
|Resource Manager | Nie | Nie | Nie |  |
|Wyszukiwanie w handlu detalicznym - przez Bing | Nie | Nie | Nie |  |
|Wyszukiwanie | Tak | Tak | Nie |  |
|Service Bus | Tak | Tak | Nie |  |
|Service Fabric | Nie | Tak | Nie | Agent wymagany do monitorowania systemu operacyjnego gościa i przepływów pracy.  |
|Portal rejestracji | Nie | Nie | Nie |  |
|Site Recovery | Nie | Tak | Nie |  |
|Wiosenna usługa w chmurze | Nie | Nie | Nie |  |
|SQL Data Warehouse | Tak | Tak | Nie |  |
|Baza danych SQL | Tak | Tak | Nie |  |
|SQL Server Stretch Database | Tak | Tak | Nie |  |
|Stos | Nie | Nie | Nie |  |
|Magazyn | Tak | Nie | [Tak](insights/storage-insights-overview.md) |  |
|Pamięć podręczna magazynu | Nie | Nie | Nie |  |
|Usługi synchronizacji magazynu | Nie | Nie | Nie |  |
|Stream Analytics | Tak | Tak | Nie |  |
|Time Series Insights | Tak | Tak | Nie |  |
|Tina | Nie | Nie | Nie |  |
|Traffic Manager | Tak | Tak | Nie |  |
|Uniwersalny nadruk | Nie | Nie | Nie |  |
|Zestawy skali maszyn wirtualnych | Nie | Tak | [Tak](insights/vminsights-overview.md) | Agent wymagany do monitorowania systemu operacyjnego gościa i przepływów pracy. |
|Maszyny wirtualne | Tak | Tak | [Tak](insights/vminsights-overview.md) | Agent wymagany do monitorowania systemu operacyjnego gościa i przepływów pracy. |
|Virtual Network | Tak | Tak | [Tak](insights/network-insights-overview.md) |  |
|Sieć wirtualna — dzienniki przepływu sieciowej sieciowej | Nie | Tak | Nie |  |
|VPN Gateway | Tak | Tak | Nie |  |
|Windows Virtual Desktop | Nie | Nie | Nie |  |


## <a name="product-integrations"></a>Integracja produktów
Usługi i rozwiązania w poniższej tabeli przechowują swoje dane w obszarze roboczym usługi Log Analytics, dzięki czemu można je analizować z innymi danymi dziennika zebranymi przez usługę Azure Monitor.

| Produkt/usługa | Opis |
|:---|:---|
| [Azure Automation](/azure/automation/) | Zarządzanie aktualizacjami systemu operacyjnego i śledzenie zmian na komputerach z systemem Windows i Linux. Zobacz [Śledzenie zmian](../automation/change-tracking.md) i zarządzanie [aktualizacjami](../automation/automation-update-management.md). |
| [Azure Information Protection](https://docs.microsoft.com/azure/information-protection/) | Klasyfikowanie i opcjonalnie chronić dokumenty i wiadomości e-mail. Zobacz [Centralne raportowanie dla usługi Azure Information Protection](https://docs.microsoft.com/azure/information-protection/reports-aip#configure-a-log-analytics-workspace-for-the-reports). |
| [Azure Security Center](/azure/security-center/) | Zbieraj i analizuj zdarzenia zabezpieczeń i przeprowadzaj analizę zagrożeń. Zobacz [Zbieranie danych w usłudze Azure Security Center](/azure/security-center/security-center-enable-data-collection) |
| [Usługa Azure Sentinel](/azure/sentinel/) | Łączy się z różnymi źródłami, w tym z usługami Office 365 i usługami Amazon Web Cloud Trail. Zobacz [Łączenie źródeł danych](/azure/sentinel/connect-data-sources). |
| [Key Vault Analytics](insights/azure-key-vault.md) | Analizowanie dzienników zdarzeń inspekcji usługi Azure Key Vault. |
| [Microsoft Intune](https://docs.microsoft.com/intune/) | Utwórz ustawienie diagnostyczne do wysyłania dzienników do usługi Azure Monitor. Zobacz [Wysyłanie danych dziennika do magazynu, centrów zdarzeń lub analizy dzienników w usłudze Intune (wersja zapoznawcza)](https://docs.microsoft.com/intune/fundamentals/review-logs-using-azure-monitor).  |
| Sieć  | [Monitor wydajności sieci](insights/network-performance-monitor.md) — monitorowanie łączności sieciowej i wydajności do punktów końcowych usług i aplikacji.<br>[Brama aplikacji platformy Azure](insights/azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-azure-monitor) — analizowanie dzienników i metryk z usługi Azure Application Gateway.<br>[Analiza ruchu](/azure/network-watcher/traffic-analytics) — analizuje dzienniki przepływu sieciowej grupy zabezpieczeń obserwatora sieci (Network Watcher), aby zapewnić wgląd w przepływ ruchu w chmurze platformy Azure. |
| [Office 365](insights/solution-office-365.md) | Monitorowanie środowiska usługi Office 365. Zaktualizowana wersja z ulepszonym dołączaniem dostępnym za pośrednictwem usługi Azure Sentinel. |
| [Analiza SQL](insights/azure-sql.md) | Monitoruj wydajność baz danych SQL platformy Azure, pul elastycznych i wystąpień zarządzanych na dużą skalę i w wielu subskrypcjach. |
| [Surface Hub](insights/surface-hubs.md) | Śledzenie kondycji i użycia urządzeń Surface Hub. |
| [System Center Operations Manager](https://docs.microsoft.com/system-center/scom) | Zbieraj dane od agentów programu Operations Manager, łącząc ich grupę zarządzania z usługą Azure Monitor. Zobacz [Łączenie programu Operations Manager z monitorem platformy Azure](platform/om-agents.md)<br> Oceń ryzyko i kondycję grupy zarządzania programu System Center Operations Manager za pomocą rozwiązania [Oceny programu Operations Manager.](insights/scom-assessment.md) |
| [Pokoje w usłudze Microsoft Teams](https://docs.microsoft.com/microsoftteams/room-systems/azure-monitor-deploy) | Zintegrowane kompleksowe zarządzanie urządzeniami usługi Microsoft Teams Rooms. |
| [Visual Studio App Center](https://docs.microsoft.com/appcenter/) | Tworzenie, testowanie i rozpowszechnianie aplikacji, a następnie monitorowanie ich stanu i użycia. Zobacz [Rozpoczynanie analizowania aplikacji mobilnej za pomocą Usługi App Center i usługi Application Insights](learn/mobile-center-quickstart.md). |
| Windows | [Zgodność z usługami Windows Update](https://docs.microsoft.com/windows/deployment/update/update-compliance-get-started) — ocena uaktualnień pulpitu systemu Windows.<br>[Analiza pulpitu](https://docs.microsoft.com/configmgr/desktop-analytics/overview) — integruje się z programem Configuration Manager w celu zapewnienia wglądu i analizy w celu podejmowania bardziej świadomych decyzji dotyczących gotowości klientów systemu Windows do aktualizacji. |



## <a name="other-solutions"></a>Inne rozwiązania
Inne rozwiązania są dostępne do monitorowania różnych aplikacji i usług, ale aktywny rozwój został zatrzymany i mogą nie być dostępne we wszystkich regionach. They are covered by the Azure Log Analytics data ingestion service level agreement.

| Rozwiązanie | Opis |
|:---|:---|
| [Sprawdzanie kondycji usługi Active Directory](insights/ad-assessment.md) | Oceń ryzyko i kondycję środowisk usługi Active Directory. |
| [Stan replikacji usługi Active Directory](insights/ad-replication-status.md) | Regularnie monitoruje środowisko usługi Active Directory pod kątem wszelkich awarii replikacji. |
| [Analiza dziennika aktywności](platform/activity-log-view.md#azure-portal) | Służy do wyświetlania wpisów dziennika aktywności. |
| [Analiza DNS (wersja zapoznawcza)](insights/dns-analytics.md) | Zbiera, analizuje i koreluje dzienniki analityczne i inspekcji DNS systemu Windows oraz inne powiązane dane z serwerów DNS. |
| [Cloud Foundry](../cloudfoundry/cloudfoundry-oms-nozzle.md) | Zbieraj, wyświetlaj i analizuj metryki kondycji i wydajności systemu Cloud Foundry w wielu wdrożeniach. |
| [Containers](insights/containers.md) | Wyświetlanie hostów kontenerów platformy Docker i Windows oraz zarządzanie nimi. |
| [Oceny na żądanie](https://docs.microsoft.com/services-hub/health/getting_started_with_on_demand_assessments) | Oceń i zoptymalizuj dostępność, zabezpieczenia i wydajność lokalnych, hybrydowych i chmurowych środowisk technologicznych firmy Microsoft. |
| [Sprawdzanie kondycji SQL](insights/sql-assessment.md) | Oceń ryzyko i kondycję środowisk programu SQL Server.  |
| [Dane o komunikacji sieciowej](insights/wire-data.md) | Skonsolidowane dane dotyczące sieci i wydajności zebrane z komputerów podłączonych do systemu Windows i z systemem Linux za pomocą agenta usługi Log Analytics. |

## <a name="third-party-integration"></a>Integracja z osobami trzecimi

| Rozwiązanie | Opis |
|:---|:---|
| [ITSM](platform/itsmc-overview.md) | Łącznik zarządzania usługami IT (ITSMC) umożliwia połączenie platformy Azure z obsługiwanym produktem/usługą zarządzania usługami IT (ITSM).  |


## <a name="resources-outside-of-azure"></a>Zasoby poza platformą Azure
Usługa Azure Monitor może zbierać dane z zasobów spoza platformy Azure przy użyciu metod wymienionych w poniższej tabeli.

| Zasób | Metoda |
|:---|:---|
| Aplikacje | Monitoruj aplikacje sieci web poza platformą Azure przy użyciu usługi Application Insights. Zobacz [Co to jest usługa Application Insights?](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). |
| Maszyny wirtualne | Agent usługi Log Analytics służy do zbierania danych z systemu operacyjnego gościa maszyn wirtualnych w innych środowiskach w chmurze lub lokalnie. Zobacz [Zbieranie danych dziennika za pomocą agenta usługi Log Analytics](platform/log-analytics-agent.md). |
| Klient interfejsu API REST | Oddzielne interfejsy API są dostępne do zapisywania danych do dzienników i metryk usługi Azure Monitor z dowolnego klienta interfejsu API REST. Zobacz [Wysyłanie danych dziennika do usługi Azure Monitor za pomocą interfejsu API modułu zbierającego dane HTTP](platform/data-collector-api.md) dla dzienników i [wysyłania niestandardowych metryk dla zasobu platformy Azure do magazynu metryk Usługi Azure Monitor przy użyciu interfejsu API REST](platform/metrics-store-custom-rest-api.md) dla metryk. |



## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [platformie danych Usługi Azure Monitor, która przechowuje dzienniki i metryki zebrane przez szczegółowe informacje i rozwiązania.](platform/data-platform.md)
- Ukończ [samouczek dotyczący monitorowania zasobu platformy Azure](learn/tutorial-resource-logs.md).
- Ukończ [samouczek dotyczący pisania kwerendy dziennika w celu analizowania danych w dziennikach monitora platformy Azure](learn/tutorial-resource-logs.md).
- Ukończ [samouczek dotyczący tworzenia wykresu metryk do analizowania danych w metrykach usługi Azure Monitor](learn/tutorial-metrics-explorer.md).

 
