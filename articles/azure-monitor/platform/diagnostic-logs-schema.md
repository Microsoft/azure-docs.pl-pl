---
title: Obsługiwane usługi i schematy dzienników zasobów platformy Azure
description: Poznaj obsługiwane usługi i schemat zdarzeń dla dzienników zasobów platformy Azure.
ms.subservice: logs
ms.topic: reference
ms.date: 10/22/2019
ms.openlocfilehash: 352310a6e489a96c38e85b16e9504d8eb9be38b1
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457250"
---
# <a name="supported-services-schemas-and-categories-for-azure-resource-logs"></a>Obsługiwane usługi, schematy i kategorie dzienników zasobów platformy Azure

> [!NOTE]
> Dzienniki zasobów były wcześniej nazywane dziennikami diagnostycznymi.

[Dzienniki zasobów usługi Azure Monitor](../../azure-monitor/platform/platform-logs-overview.md) to dzienniki emitowane przez usługi platformy Azure, które opisują działanie tych usług lub zasobów. Wszystkie dzienniki zasobów dostępne za pośrednictwem usługi Azure Monitor współużytkują wspólny schemat najwyższego poziomu, z elastycznością dla każdej usługi do emitowania unikatowych właściwości dla własnych zdarzeń.

Kombinacja typu zasobu (dostępne `resourceId` w właściwości) i `category` jednoznacznie zidentyfikować schematu. W tym artykule opisano schemat najwyższego poziomu dla dzienników zasobów i łącza do schematu dla każdej usługi.

## <a name="top-level-resource-logs-schema"></a>Schemat dzienników zasobów najwyższego poziomu

| Nazwa | Wymagane/Opcjonalne | Opis |
|---|---|---|
| time | Wymagany | Sygnatura czasowa (UTC) zdarzenia. |
| resourceId | Wymagany | Identyfikator zasobu, który emitował zdarzenie. W przypadku usług dzierżawy jest to formularz /tenants/tenant-id/providers/provider-name. |
| identyfikator dzierżawy | Wymagane dla dzienników dzierżawców | Identyfikator dzierżawy dzierżawy usługi Active Directory, z którą jest powiązane to zdarzenie. Ta właściwość jest używana tylko dla dzienników na poziomie dzierżawy, nie jest wyświetlana w dziennikach na poziomie zasobów. |
| operationName | Wymagany | Nazwa operacji reprezentowanej przez to zdarzenie. Jeśli zdarzenie reprezentuje operację RBAC, jest to nazwa operacji RBAC (np. Microsoft.Storage/storageKonta/blobSługi/obiekty blobs/Odczyt). Zazwyczaj modelowane w formie operacji Menedżera zasobów, nawet jeśli nie są one`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`rzeczywiste udokumentowane operacje Menedżera zasobów ( ) |
| operationVersion | Optional (Opcjonalność) | Wersja api skojarzona z operacją, jeśli operationName została wykonana przy użyciu interfejsu API (np. `http://myservice.windowsazure.net/object?api-version=2016-06-01`). Jeśli nie ma interfejsu API, który odpowiada tej operacji, wersja reprezentuje wersję tej operacji w przypadku, gdy właściwości skojarzone z zmianą operacji w przyszłości. |
| category | Wymagany | Kategoria dziennika zdarzenia. Kategoria to szczegółowość, w której można włączyć lub wyłączyć dzienniki w określonym zasobie. Właściwości, które pojawiają się w obrębie obiektu blob właściwości zdarzenia są takie same w ramach określonej kategorii dziennika i typu zasobu. Typowe kategorie dziennika to "Inspekcja" "Operacyjne" "Wykonanie" i "Żądanie". |
| resultType | Optional (Opcjonalność) | Stan zdarzenia. Typowe wartości obejmują rozpoczęte, w toku, powiodło się, nie powiodło się, aktywne i rozwiązane. |
| resultSignature | Optional (Opcjonalność) | Stan podrzędny zdarzenia. Jeśli ta operacja odpowiada wywołaniu interfejsu API REST, jest to kod stanu HTTP odpowiedniego wywołania REST. |
| resultDescription | Optional (Opcjonalność) | Statyczny opis tekstowy tej operacji, np. "Pobierz plik magazynu." |
| durationMs | Optional (Opcjonalność) | Czas trwania operacji w milisekundach. |
| callerIpAddress | Optional (Opcjonalność) | Adres IP wywołującego, jeśli operacja odpowiada wywołaniu interfejsu API, które pochodziłoby od jednostki o publicznie dostępnym adresie IP. |
| correlationId | Optional (Opcjonalność) | Identyfikator GUID używany do grupowanie zestawu powiązanych zdarzeń. Zazwyczaj, jeśli dwa zdarzenia mają taką samą operacjęName, ale dwa różne stany (np. "Rozpoczęty" i "Udało się"), mają ten sam identyfikator korelacji. Może to również reprezentować inne relacje między zdarzeniami. |
| identity | Optional (Opcjonalność) | Obiekt blob JSON, który opisuje tożsamość użytkownika lub aplikacji, która wykonała operację. Zazwyczaj będzie to obejmować autoryzacji i oświadczeń / Token JWT z usługi Active Directory. |
| Poziom | Optional (Opcjonalność) | Poziom ważności zdarzenia. Musi być jednym z informacyjnych, ostrzeżenie, błąd lub krytyczne. |
| location | Optional (Opcjonalność) | Region zasobu emitującego zdarzenie, np. "Wschodnie stany USA" lub "Francja Południowa" |
| properties | Optional (Opcjonalność) | Wszelkie rozszerzone właściwości związane z tą konkretną kategorią zdarzeń. Wszystkie niestandardowe/unikatowe właściwości muszą być umieszczone wewnątrz tej "części B" schematu. |

## <a name="service-specific-schemas-for-resource-logs"></a>Schematy specyficzne dla usługi dla dzienników zasobów
Schemat dzienników diagnostycznych zasobów różni się w zależności od kategorii zasobów i dziennika. Ta lista zawiera wszystkie usługi, które udostępniają dzienniki zasobów i łącza do usługi i schematu specyficzne dla kategorii, jeśli są dostępne.

| Usługa | Schemat & dokumenty |
| --- | --- |
| Usługa Azure Active Directory | [Omówienie](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md) [schematu dziennika inspekcji](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) i [schematu logowania](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| API Management | [Dzienniki zasobów zarządzania interfejsami API](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Bramy aplikacji |[Rejestrowanie bramy aplikacji](../../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Analiza dzienników dla usługi Azure Automation](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Rejestrowanie wsadowe platformy Azure](../../batch/batch-diagnostics.md) |
| Azure Database for MySQL | [Dzienniki diagnostyczne usługi Azure Database for MySQL](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database for PostgreSQL | [Usługa Azure Database dla dzienników PostgreSQL](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| Azure Data Explorer | [Dzienniki Eksploratora danych platformy Azure](/azure/data-explorer/using-diagnostic-logs) |
| Cognitive Services | [Rejestrowanie usług Azure Cognitive Services](../../cognitive-services/diagnostic-logging.md) |
| Container Registry | [Rejestrowanie dla rejestru kontenerów platformy Azure](../../container-registry/container-registry-diagnostics-audit-logs.md) |
| Content Delivery Network | [Dzienniki platformy Azure dla usługi CDN](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Rejestrowanie bazy danych usługi Azure Cosmos](../../cosmos-db/logging.md) |
| Fabryka danych | [Monitorowanie fabryk danych przy użyciu usługi Azure Monitor](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Uzyskiwanie dostępu do dzienników dla usługi Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Uzyskiwanie dostępu do dzienników dla usługi Azure Data Lake Store](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Event Hubs |[Dzienniki usługi Azure Event Hubs](../../event-hubs/event-hubs-diagnostic-logs.md) |
| ExpressRoute | Schemat jest niedostępny. |
| Azure Firewall | Schemat jest niedostępny. |
| Usługa IoT Hub | [Operacje w centrum IoT](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Usługa Key Vault |[Rejestrowanie magazynu kluczy platformy Azure](../../key-vault/general/logging.md) |
| Kubernetes Service |[Rejestrowanie kubernetes platformy Azure](../../aks/view-master-logs.md#log-event-schema) |
| Moduł równoważenia obciążenia |[Analiza dzienników dotyczących usługi Azure Load Balancer](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Logic Apps — niestandardowy schemat śledzenia B2B](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Grupy zabezpieczeń sieci |[Usługa Log Analytics dla sieciowych grup zabezpieczeń](../../virtual-network/virtual-network-nsg-manage-log.md) |
| Ochrona przed atakami DDOS | [Zarządzanie standardem ochrony przed atakami DDoS platformy Azure](../../virtual-network/manage-ddos-protection.md) |
| Power BI — warstwa Dedykowana | [Rejestrowanie dla usługi Power BI osadzone na platformie Azure](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Recovery Services | [Model danych dla usługi Azure Backup](../../backup/backup-azure-reports-data-model.md)|
| Wyszukiwanie |[Włączanie i korzystanie z analizy ruchu w sieci wyszukiwania](../../search/search-traffic-analytics.md) |
| Service Bus |[Dzienniki usługi Azure Service Bus](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| Baza danych SQL | [Rejestrowanie bazy danych SQL platformy Azure](../../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Dzienniki zadań](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | [Schemat dziennika menedżera ruchu](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Sieci wirtualne | Schemat jest niedostępny. |
| Bramy sieci wirtualnej | Schemat jest niedostępny. |

## <a name="supported-log-categories-per-resource-type"></a>Obsługiwane kategorie dzienników dla typu zasobu

Niektóre kategorie mogą być obsługiwane tylko dla określonych typów zasobów. Jest to lista wszystkich, które są dostępne w jakiejś formie.  Na przykład kategorie Microsoft.Sql/servers/databases nie są dostępne dla wszystkich typów baz danych. Aby uzyskać więcej informacji, zobacz [informacje dotyczące rejestrowania diagnostycznego bazy danych SQL](../../sql-database/sql-database-metrics-diag-logging.md). 

|Typ zasobu|Kategoria|Nazwa wyświetlana kategorii|
|---|---|---|
|Microsoft.AAD/domainSługa|Bezpieczeństwo systemu|Bezpieczeństwo systemu|
|Microsoft.AAD/domainSługa|AccountManagement (Odpowiedzialne konto)|AccountManagement (Odpowiedzialne konto)|
|Microsoft.AAD/domainSługa|LognLogoff|LognLogoff|
|Microsoft.AAD/domainSługa|ObiektAkces|ObiektAkces|
|Microsoft.AAD/domainSługa|Zmiany zasad|Zmiany zasad|
|Microsoft.AAD/domainSługa|Użycie uprawnień|Użycie uprawnień|
|Microsoft.AAD/domainSługa|Śledzenie szczegółów|Śledzenie szczegółów|
|Microsoft.AAD/domainSługa|Usługa katalogowąAkcesa|Usługa katalogowąAkcesa|
|Microsoft.AAD/domainSługa|AccountLogon (Blog konta)|AccountLogon (Blog konta)|
|microsoft.aadiam/dzierżawcy|Signin|Signin|
|Microsoft.AnalysisSługa/serwery|Aparat|Aparat|
|Microsoft.AnalysisSługa/serwery|Usługa|Usługa|
|Microsoft.ApiManagement/service|Dzienniki bram|Dzienniki związane z Bramą Manaagementa|
|Microsoft.AppPlatform/Wiosna|ApplicationConsole|Konsola aplikacji|
|Microsoft.Automation/automationKontacje|JobLogs (Dzienniki zadań)|Dzienniki zadań|
|Microsoft.Automation/automationKontacje|JobStreams (Strumienie zadań)|Strumienie zadań|
|Microsoft.Automation/automationKontacje|DscNodeStatus (DscNodeStatus)|Stan węzła dsc|
|Microsoft.Batch/batchKonkludy|Dziennik serwisu|Dzienniki usług|
|Microsoft.BatchAI/obszary robocze|BaiClusterEvent (Polski)|BaiClusterEvent (Polski)|
|Microsoft.BatchAI/obszary robocze|BaiClusterNodeEvent|BaiClusterNodeEvent|
|Microsoft.BatchAI/obszary robocze|BaiJobEwent|BaiJobEwent|
|Microsoft.Blockchain/blockchainCzłonek|BlockchainApplication (Zastosowanie w łańcuchu bloków)|Aplikacja Blockchain|
|Microsoft.Blockchain/blockchainCzłonek|Serwer proxy|Serwer proxy|
|Microsoft.Cdn/profile/punkty końcowe|Analizy Core|Pobiera metryki punktu końcowego, na przykład przepustowość, ruch wychodzący itp.|
|Microsoft.ClassicNetwork/networksecuritygroups|Zdarzenie przepływ reguły grupy zabezpieczeń sieci|Zdarzenie przepływ reguły grupy zabezpieczeń sieci|
|Microsoft.CognitiveServices/accounts|Inspekcja|Dzienniki inspekcji|
|Microsoft.CognitiveServices/accounts|RequestResponse (Prośba o odpowiedzi)|Dzienniki żądań i odpowiedzi|
|Microsoft.ContainerRegistry/rejestry|ContainerRegistryRepositoryWydacje|Repozytorium Dzienniki zdarzeń (wersja zapoznawcza)|
|Microsoft.ContainerRegistry/rejestry|ContainerRegistryLoginVents|Zdarzenia logowania (wersja zapoznawcza)|
|Microsoft.ContainerService/managedClusters|kube-apiserver|Serwer interfejsu API kubernetes|
|Microsoft.ContainerService/managedClusters|kube-controller-manager|Menedżer kontrolerów Kubernetes|
|Microsoft.ContainerService/managedClusters|kube-harmonogram|Harmonogram Kubernetes|
|Microsoft.ContainerService/managedClusters|kube-audyt|Audyt Kubernetes|
|Microsoft.ContainerService/managedClusters|skalowanie automatyczne klastra|Skalowanie automatyczne klastra kubernetes|
|Microsoft.Databricks/obszary robocze|dbfs ( dbfs )|System plików usługi Databricks|
|Microsoft.Databricks/obszary robocze|Klastrów|Klastry łupy danych|
|Microsoft.Databricks/obszary robocze|accounts|Konta Databricks|
|Microsoft.Databricks/obszary robocze|Zadania|Oferty pracy: Databricks|
|Microsoft.Databricks/obszary robocze|Notebook|Notes usługi Databricks|
|Microsoft.Databricks/obszary robocze|Ssh|Databricks SSH|
|Microsoft.Databricks/obszary robocze|obszar roboczy|Obszar roboczy Databricks|
|Microsoft.Databricks/obszary robocze|wpisy tajne|Tajemnice Databricks|
|Microsoft.Databricks/obszary robocze|sqlPermissions (Niesie ze)|Databricks SQLPermissions|
|Microsoft.Databricks/obszary robocze|wystąpieniaPools|Pule wystąpień|
|Microsoft.DataCatalog/datacatalogs|ScanStatusLogEvent (Badanie scanStatusLogEvent)|Stan skanowania|
|Microsoft.DataFactory/fabryki|ActivityRuns (AktywnośćRuns)|Dziennik uruchomienia działania potoku|
|Microsoft.DataFactory/fabryki|PipelineRuns (PipelineRuns)|Dziennik przebiegów potoku|
|Microsoft.DataFactory/fabryki|Wyzwalacze|Dziennik uruchomień wyzwalacza|
|Microsoft.DataLakeAnalytics/accounts|Inspekcja|Dzienniki inspekcji|
|Microsoft.DataLakeAnalytics/accounts|Żądania|Dzienniki żądań|
|Microsoft.DataLakeStore/konta|Inspekcja|Dzienniki inspekcji|
|Microsoft.DataLakeStore/konta|Żądania|Dzienniki żądań|
|Microsoft.DataShare/konta|Udziały|Udziały|
|Microsoft.DataShare/konta|ShareSubscriptions (Subskrypcje udziału)|Udostępnij subskrypcje|
|Microsoft.DataShare/konta|Zdjęcia SentShareSnapshots|Wysłane migawki udziału|
|Microsoft.DataShare/konta|OdebraneShareSnapshots|Odebrane migawki udziału|
|Microsoft.DBforMySQL/servers|MySqlSlowLogs|Dzienniki serwera MySQL|
|Microsoft.DBforMySQL/servers|MySqlAuditLogs (MySqlAuditLogs)|Dzienniki inspekcji MySQL|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLLogs (Dzienniki postgreSQLlogs)|Dzienniki serwera PostgreSQL|
|Microsoft.DBforPostgreSQL/servers|QueryStoreRuntimeStatistics ( QueryStoreRuntimeStatistics )|Statystyki środowiska wykonawczego magazynu kwerend PostgreSQL|
|Microsoft.DBforPostgreSQL/servers|QueryStoreWaitStatistics (Statystyki magazynu querystorewait)|Statystyki oczekiwania w magazynie zapytań PostgreSQL|
|Microsoft.DBforPostgreSQL/serversv2|PostgreSQLLogs (Dzienniki postgreSQLlogs)|Dzienniki serwera PostgreSQL|
|Microsoft.DBforPostgreSQL/serversv2|QueryStoreRuntimeStatistics ( QueryStoreRuntimeStatistics )|Statystyki środowiska wykonawczego magazynu kwerend PostgreSQL|
|Microsoft.DBforPostgreSQL/serversv2|QueryStoreWaitStatistics (Statystyki magazynu querystorewait)|Statystyki oczekiwania w magazynie zapytań PostgreSQL|
|Microsoft.DesktopWirtualizacja/obszary robocze|Punkt kontrolny|Punkt kontrolny|
|Microsoft.DesktopWirtualizacja/obszary robocze|Błąd|Błąd|
|Microsoft.DesktopWirtualizacja/obszary robocze|Zarządzanie|Zarządzanie|
|Microsoft.DesktopWirtualizacja/obszary robocze|Źródło danych|Źródło danych|
|Microsoft.DesktopWirtualization/applicationGroups Microsoft.DesktopVirtualization/applicationGroups Microsoft.DesktopVirtualization/applicationGroups Microsoft.|Punkt kontrolny|Punkt kontrolny|
|Microsoft.DesktopWirtualization/applicationGroups Microsoft.DesktopVirtualization/applicationGroups Microsoft.DesktopVirtualization/applicationGroups Microsoft.|Błąd|Błąd|
|Microsoft.DesktopWirtualization/applicationGroups Microsoft.DesktopVirtualization/applicationGroups Microsoft.DesktopVirtualization/applicationGroups Microsoft.|Zarządzanie|Zarządzanie|
|Microsoft.DesktopWirtualization/hostPools|Punkt kontrolny|Punkt kontrolny|
|Microsoft.DesktopWirtualization/hostPools|Błąd|Błąd|
|Microsoft.DesktopWirtualization/hostPools|Zarządzanie|Zarządzanie|
|Microsoft.DesktopWirtualization/hostPools|Połączenie|Połączenie|
|Microsoft.DesktopWirtualization/hostPools|Rejestracja hosta|Rejestracja hosta|
|Urządzenia firmy Microsoft.Devices/IotHubs|Połączenia|Połączenia|
|Urządzenia firmy Microsoft.Devices/IotHubs|DeviceTelemetry|Dane telemetryczne urządzenia|
|Urządzenia firmy Microsoft.Devices/IotHubs|C2DCommands ( C2DCommands )|Polecenia C2D|
|Urządzenia firmy Microsoft.Devices/IotHubs|DeviceIdentityOperations (Działanie deviceidentity)|Operacje tożsamości urządzenia|
|Urządzenia firmy Microsoft.Devices/IotHubs|Operator systemu Plików|Operacje przekazywania plików|
|Urządzenia firmy Microsoft.Devices/IotHubs|Trasy|Trasy|
|Urządzenia firmy Microsoft.Devices/IotHubs|D2CTwinOperacje|D2CTwinOperacje|
|Urządzenia firmy Microsoft.Devices/IotHubs|C2DTwinOperacje|Operacje bliźniacze C2D|
|Urządzenia firmy Microsoft.Devices/IotHubs|TwinQueries (Bliźnięta)|Zapytania bliźniacze|
|Urządzenia firmy Microsoft.Devices/IotHubs|PracaOperacje|Operacje pracy|
|Urządzenia firmy Microsoft.Devices/IotHubs|Metody directmeodów|Metody bezpośrednie|
|Urządzenia firmy Microsoft.Devices/IotHubs|Śledzenie rozproszone|Śledzenie rozproszone (wersja zapoznawcza)|
|Urządzenia firmy Microsoft.Devices/IotHubs|Konfiguracje|Konfiguracje|
|Urządzenia firmy Microsoft.Devices/IotHubs|Strumienie urządzeń|Strumienie urządzeń (wersja zapoznawcza)|
|Urządzenia firmy Microsoft.Devices/inicjowanie obsługi administracyjnejUsługi|Działanie urządzenia|Operacje na urządzeniach|
|Urządzenia firmy Microsoft.Devices/inicjowanie obsługi administracyjnejUsługi|Obsługa usługi|Operacje usługi|
|Microsoft.DocumentDB/databaseKonta kont|Prośby o plany danych|Prośby o plany danych|
|Microsoft.DocumentDB/databaseKonta kont|MongoRequests|MongoRequests|
|Microsoft.DocumentDB/databaseKonta kont|QueryRuntimeStatistics (Statystyki dotyczące queryruntime)|QueryRuntimeStatistics (Statystyki dotyczące queryruntime)|
|Microsoft.DocumentDB/databaseKonta kont|Statystyki klucza partycji|Statystyki klucza partycji|
|Microsoft.DocumentDB/databaseKonta kont|Forsowanie samolotów|Forsowanie samolotów|
|Microsoft.EnterpriseKnowledgeGraph/usługi|Badanie audytu|Dziennik zdarzeń inspekcji|
|Microsoft.EnterpriseKnowledgeGraph/usługi|DataIssue|Dziennik dataIssue|
|Microsoft.EnterpriseKnowledgeGraph/usługi|Żądania|Dziennik konfiguracji|
|Microsoft.EventHub/przestrzenie nazw|ArchiveLogs (Dzienniki archiwalne)|Dzienniki archiwum|
|Microsoft.EventHub/przestrzenie nazw|Dzienniki operacyjne|Dzienniki operacyjne|
|Microsoft.EventHub/przestrzenie nazw|AutoscaleLogs|Dzienniki automatycznej skali|
|Microsoft.EventHub/przestrzenie nazw|KafkaCoordinatorLogs|Dzienniki koordynatora platformy Kafka|
|Microsoft.EventHub/przestrzenie nazw|KafkaUserErrorLogs|Dzienniki błędów użytkowników platformy Kafka|
|Microsoft.EventHub/przestrzenie nazw|EventHubVNetConnectionEvent|Dzienniki połączeń filtrowania sieci wirtualnych/ip|
|Microsoft.EventHub/przestrzenie nazw|CustomerManagedKeyUserLogs|Dzienniki kluczy zarządzanych przez klienta|
|Microsoft.HealthcareApis/usługi|Dzienniki inspekcji|Dzienniki inspekcji|
|Microsoft.Insights/Skalowanie automatyczneStawienia|Skalowanie automatyczneWartości|Oceny skalowania automatycznego|
|Microsoft.Insights/Skalowanie automatyczneStawienia|Skalowanie automatyczneAkcja|Akcje skalowania automatycznego|
|Microsoft.IoTSpaces/Graph|Ślad|Ślad|
|Microsoft.IoTSpaces/Graph|Operacyjne|Operacyjne|
|Microsoft.IoTSpaces/Graph|Inspekcja|Inspekcja|
|Microsoft.IoTSpaces/Graph|Userdefinedfunction|Userdefinedfunction|
|Microsoft.IoTSpaces/Graph|Ruch przychodzący|Ruch przychodzący|
|Microsoft.IoTSpaces/Graph|Ruch wychodzący|Ruch wychodzący|
|Microsoft.KeyVault/przechowalnia|Badanie audytu|Dzienniki inspekcji|
|Microsoft.Kusto/klastry|Powiodło się Połknienie|Udane operacje pozyskiwania|
|Microsoft.Kusto/klastry|Nieudane połknienie|Operacje pozyskiwania nie powiodły się|
|Microsoft.Logic/przepływy pracy|Workflowruntime|Zdarzenia diagnostyczne środowiska wykonawczego przepływu pracy|
|Microsoft.Logic/integrationKontacje|IntegrationAccountTrackingAvents|Śledzenie zdarzeń konta integracji|
|Microsoft.MachineLearningUsługi/obszary robocze|AmlComputeClusterEvent|AmlComputeClusterEvent|
|Microsoft.MachineLearningUsługi/obszary robocze|AmlComputeClusterNodeevent|AmlComputeClusterNodeevent|
|Microsoft.MachineLearningUsługi/obszary robocze|AmlComputeJobevent|AmlComputeJobevent|
|Usługi Microsoft.Media/media|KeyDeliverySukacje|Żądania dostarczenia kluczy|
|Grupy zabezpieczeń w sieci/sieci firmy Microsoft|NetworkSecurityGroupEvent (NetworkSecurityGroupEvent)|Zdarzenie sieciowej grupy zabezpieczeń|
|Grupy zabezpieczeń w sieci/sieci firmy Microsoft|NetworkSecurityGroupRuleCounter|Licznik reguł sieciowej grupy zabezpieczeń|
|Grupy zabezpieczeń w sieci/sieci firmy Microsoft|NetworkSecurityGroupFlowEvent (NetworkSecurityGroupFlowEvent)|Zdarzenie przepływ reguły grupy zabezpieczeń sieci|
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|Powiadomienia o ochronie przed atakami DDoS|
|Microsoft.Network/publicIPAddresses|DDoSMitigationFlowLogs|Dzienniki przepływu decyzji o łagodzeniu DDoS|
|Microsoft.Network/publicIPAddresses|DDoSMitigationReports|Raporty o czynnikach ograniczających ddos|
|Sieć Microsoft.Network/virtual Sieci|Wirtualne certyfikaty|Alerty ochrony maszyn wirtualnych|
|Microsoft.Network/applicationGateways|AplikacjaGatewayAccessLog|Dziennik dostępu do bramy aplikacji|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Dziennik wydajności bramy aplikacji|
|Microsoft.Network/applicationGateways|AplikacjaGatewayFirewallLog|Dziennik zapory bramy aplikacji|
|Microsoft.Network/azurefirewalls|AzureFirewallApplicationRule|Reguła aplikacji zapory platformy Azure|
|Microsoft.Network/azurefirewalls|AzureFirewallNetworkRule|Reguła sieci zapory platformy Azure|
|Microsoft.Network/virtualNetworkGateways|Dziennik diagnostyki bramy|Dzienniki diagnostyczne bramy|
|Microsoft.Network/virtualNetworkGateways|TunnelDiagnosticLog|Dzienniki diagnostyczne tuneli|
|Microsoft.Network/virtualNetworkGateways|Dziennik diagnostyki trasy|Dzienniki diagnostyczne trasy|
|Microsoft.Network/virtualNetworkGateways|IKEDiagnosticLog|Dzienniki diagnostyczne IKE|
|Microsoft.Network/virtualNetworkGateways|P2SDiagnosticLog|Dzienniki diagnostyczne P2S|
|Microsoft.Network/trafficManagerProfiles|ProbeHealthStatusWydarzenia|Zdarzenie wyników kondycji sondy menedżera ruchu|
|Microsoft.Network/expressRouteCircuits|PeeringRouteLog|Dzienniki tabel tras komunikacji równorzędnej|
|Microsoft.Network/vpnGateways|Dziennik diagnostyki bramy|Dzienniki diagnostyczne bramy|
|Microsoft.Network/vpnGateways|TunnelDiagnosticLog|Dzienniki diagnostyczne tuneli|
|Microsoft.Network/vpnGateways|Dziennik diagnostyki trasy|Dzienniki diagnostyczne trasy|
|Microsoft.Network/vpnGateways|IKEDiagnosticLog|Dzienniki diagnostyczne IKE|
|Microsoft.Network/frontdoory|FrontdoorAkcessLog|Dziennik dostępu frontdoor|
|Microsoft.Network/frontdoory|FrontdoorWebApplicationFirewallLog|Dziennik zapory aplikacji sieci Web Frontdoor|
|Microsoft.Network/p2sVpnGateways|Dziennik diagnostyki bramy|Dzienniki diagnostyczne bramy|
|Microsoft.Network/p2sVpnGateways|IKEDiagnosticLog|Dzienniki diagnostyczne IKE|
|Microsoft.Network/p2sVpnGateways|P2SDiagnosticLog|Dzienniki diagnostyczne P2S|
|Microsoft.Network/bastionHosts|BastionAuditLogs|Dzienniki inspekcji bastionu|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent (Równourza obciążenia)|Zdarzenia alertu modułu równoważenia obciążenia|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Stan kondycji sondy modułu równoważenia obciążenia|
|Microsoft.PowerBIDedicated/capacitys|Aparat|Aparat|
|Microsoft.RecoverySługs/Vaults|Raport usługi AzureBackup|Dane raportowania kopii zapasowych platformy Azure|
|Microsoft.RecoverySługs/Vaults|CoreAzureBackup (właśc.|Podstawowe dane kopii zapasowej platformy Azure|
|Microsoft.RecoverySługs/Vaults|AddonAzureBackupJobs|Dane zadania tworzenia kopii zapasowych usługi Addon Azure|
|Microsoft.RecoverySługs/Vaults|AddonAzureBackupAlerts|Dane alertu o kopii zapasowej usługi Addon Azure|
|Microsoft.RecoverySługs/Vaults|AddonAzureBackupPolityka|Dane zasad tworzenia kopii zapasowych usługi Addon Azure|
|Microsoft.RecoverySługs/Vaults|AddonAzureBackupStorage|Dodawanie danych magazynu kopii zapasowych platformy Azure|
|Microsoft.RecoverySługs/Vaults|AddonAzureBackupProtectedInstance|Dane wystąpienia chronionego kopii zapasowej dodatku Azure|
|Microsoft.RecoverySługs/Vaults|AzureSiteRecoveryJobs|Zadania odzyskiwania witryny platformy Azure|
|Microsoft.RecoverySługs/Vaults|Usługa AzureSiteRecoveryEvents|Zdarzenia odzyskiwania witryny platformy Azure|
|Microsoft.RecoverySługs/Vaults|Usługa AzureSiteRecoveryReplicatedItems|Elementy replikowane usługi Azure Site Recovery|
|Microsoft.RecoverySługs/Vaults|Usługa AzureSiteRecoveryReplicationStats|Statystyki replikacji usługi Azure Site Recovery|
|Microsoft.RecoverySługs/Vaults|Punkty programu AzureSiteRecoveryRecoveryPoints|Punkty odzyskiwania usługi Azure site recovery|
|Microsoft.RecoverySługs/Vaults|Usługa AzureSiteRecoveryReplicationDataUploadRate|Szybkość przekazywania danych replikacji usługi Azure Recovery|
|Microsoft.RecoverySługs/Vaults|Usługa AzureSiteRecoveryProtectedDiskDataChurn|Zmiany danych dysku chronionego w usłudze Azure Site Recovery|
|Microsoft.Search/searchSługs|Dzienniki operacji|Dzienniki operacji|
|Microsoft.ServiceBus/przestrzenie nazw|Dzienniki operacyjne|Dzienniki operacyjne|
|Microsoft.Sql/serwery/bazy danych|SqlInsights (WYSK ZDJ.|SQL Insights|
|Microsoft.Sql/serwery/bazy danych|Automatyczne dostrajanie|Automatyczne dostrajanie|
|Microsoft.Sql/serwery/bazy danych|QueryStoreRuntimeStatistics ( QueryStoreRuntimeStatistics )|Statystyki środowiska wykonawczego magazynu kwerend|
|Microsoft.Sql/serwery/bazy danych|QueryStoreWaitStatistics (Statystyki magazynu querystorewait)|Statystyki oczekiwania w magazynie kwerend|
|Microsoft.Sql/serwery/bazy danych|Errors|Errors|
|Microsoft.Sql/serwery/bazy danych|DatabaseWaitStatistics (DatabaseWaitStatistics)|Statystyki oczekiwania w bazie danych|
|Microsoft.Sql/serwery/bazy danych|Limity czasu|Limity czasu|
|Microsoft.Sql/serwery/bazy danych|Bloki|Bloki|
|Microsoft.Sql/serwery/bazy danych|Zakleszczenia|Zakleszczenia|
|Microsoft.Sql/serwery/bazy danych|Inspekcja|Dzienniki inspekcji|
|Microsoft.Sql/serwery/bazy danych|SQLSecurityAuditWydarzenia|Zdarzenie inspekcji zabezpieczeń SQL|
|Microsoft.Sql/serwery/bazy danych|Pracownicy Dms|Pracownicy Dms|
|Microsoft.Sql/serwery/bazy danych|ExecRequests (Prośby o exec)|Prośby Exec|
|Microsoft.Sql/serwery/bazy danych|RequestSteps (Kroki żądań)|Kroki żądania|
|Microsoft.Sql/serwery/bazy danych|Prośby sqlrequests|Żądania sql|
|Microsoft.Sql/serwery/bazy danych|Czeka|Czeka|
|Microsoft.Sql/managedInstances|Stanowanie zasobów|Statystyki użycia zasobów|
|Microsoft.Sql/managedInstances|SQLSecurityAuditWydarzenia|Zdarzenie inspekcji zabezpieczeń SQL|
|Microsoft.Sql/managedInstances/databases|SqlInsights (WYSK ZDJ.|SQL Insights|
|Microsoft.Sql/managedInstances/databases|QueryStoreRuntimeStatistics ( QueryStoreRuntimeStatistics )|Statystyki środowiska wykonawczego magazynu kwerend|
|Microsoft.Sql/managedInstances/databases|QueryStoreWaitStatistics (Statystyki magazynu querystorewait)|Statystyki oczekiwania w magazynie kwerend|
|Microsoft.Sql/managedInstances/databases|Errors|Errors|
|Magazyn/magazyn firmy MicrosoftKonserwowanie/tabelaUsługi|PrzechowywaniePrzeczytaj|PrzechowywaniePrzeczytaj|
|Magazyn/magazyn firmy MicrosoftKonserwowanie/tabelaUsługi|StorageWrite (Dane magazynu)|StorageWrite (Dane magazynu)|
|Magazyn/magazyn firmy MicrosoftKonserwowanie/tabelaUsługi|StorageDelete|StorageDelete|
|Magazyn/magazyn firmy MicrosoftKonserwowanie/obiekt usługi|PrzechowywaniePrzeczytaj|PrzechowywaniePrzeczytaj|
|Magazyn/magazyn firmy MicrosoftKonserwowanie/obiekt usługi|StorageWrite (Dane magazynu)|StorageWrite (Dane magazynu)|
|Magazyn/magazyn firmy MicrosoftKonserwowanie/obiekt usługi|StorageDelete|StorageDelete|
|Magazyn microsoft.storage/storageKonta/fileSługi|PrzechowywaniePrzeczytaj|PrzechowywaniePrzeczytaj|
|Magazyn microsoft.storage/storageKonta/fileSługi|StorageWrite (Dane magazynu)|StorageWrite (Dane magazynu)|
|Magazyn microsoft.storage/storageKonta/fileSługi|StorageDelete|StorageDelete|
|Magazyn/magazyn firmy MicrosoftKonserwowanie/kolejkaUsługi|PrzechowywaniePrzeczytaj|PrzechowywaniePrzeczytaj|
|Magazyn/magazyn firmy MicrosoftKonserwowanie/kolejkaUsługi|StorageWrite (Dane magazynu)|StorageWrite (Dane magazynu)|
|Magazyn/magazyn firmy MicrosoftKonserwowanie/kolejkaUsługi|StorageDelete|StorageDelete|
|Microsoft.StreamAnalityka/szybkość przesyłania strumieniowego|Wykonanie|Wykonanie|
|Microsoft.StreamAnalityka/szybkość przesyłania strumieniowego|Tworzenie|Tworzenie|
|microsoft.web/hostingśrodowiska|AppServiceEnvironmentPlatformLogs|Dzienniki platformy środowiska usługi aplikacji|
|microsoft.web/witryny|FunkcjeAppLogs|Dzienniki aplikacji funkcji|
|microsoft.web/witryny|AppServiceHTTPLogs|Dzienniki HTTP|
|microsoft.web/witryny|AppServiceConsoleLogs|Dzienniki konsoli usługi app service|
|microsoft.web/witryny|AppServiceAppLogs (AppServiceAppLogs)|Dzienniki aplikacji usługi app service|
|microsoft.web/witryny|AppServiceFileAuditLogs|Dzienniki inspekcji zmian zawartości witryny|
|microsoft.web/witryny|AppServiceAuditLogs (Usługi w aplikacji)|Dzienniki inspekcji dostępu|
|microsoft.web/sites/slots|FunkcjeAppLogs|Dzienniki aplikacji funkcji|
|microsoft.web/sites/slots|AppServiceHTTPLogs|Dzienniki HTTP|
|microsoft.web/sites/slots|AppServiceConsoleLogs|Dzienniki konsoli|
|microsoft.web/sites/slots|AppServiceAppLogs (AppServiceAppLogs)|Dzienniki aplikacji|
|microsoft.web/sites/slots|AppServiceFileAuditLogs|Dzienniki inspekcji zmian zawartości witryny|
|microsoft.web/sites/slots|AppServiceAuditLogs (Usługi w aplikacji)|Dzienniki inspekcji dostępu|

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o dziennikach zasobów](../../azure-monitor/platform/platform-logs-overview.md)
* [Przesyłanie strumieniowe dzienników zasobów do **centrów zdarzeń**](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)
* [Zmienianie ustawień diagnostycznych dziennika zasobów przy użyciu interfejsu API REST monitora azure](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)
* [Analizowanie dzienników z magazynu platformy Azure za pomocą usługi Log Analytics](../../azure-monitor/platform/collect-azure-metrics-logs.md)
