---
title: Obsługiwane usługi i schematy dzienników zasobów platformy Azure
description: Poznaj obsługiwane usługi i schemat zdarzeń dla dzienników zasobów platformy Azure.
ms.subservice: logs
ms.topic: reference
ms.date: 10/22/2019
ms.openlocfilehash: 9b7b51417814e74cc7e3559029c9af8c35cbf6f2
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84016359"
---
# <a name="supported-services-schemas-and-categories-for-azure-resource-logs"></a>Obsługiwane usługi, schematy i kategorie dla dzienników zasobów platformy Azure

> [!NOTE]
> Dzienniki zasobów były wcześniej znane jako dzienniki diagnostyczne.

[Dzienniki zasobów Azure monitor](../../azure-monitor/platform/platform-logs-overview.md) to dzienniki wysyłane przez usługi platformy Azure opisujące operacje tych usług lub zasobów. Wszystkie dzienniki zasobów dostępne za pomocą Azure Monitor współdzielą wspólny schemat najwyższego poziomu, z elastycznością dla każdej usługi do emitowania unikatowych właściwości dla własnych zdarzeń.

Kombinacja typu zasobu (dostępnego we `resourceId` Właściwości) i `category` unikatowo identyfikujący schemat. W tym artykule opisano schemat najwyższego poziomu dla dzienników zasobów i linki do schematu dla każdej usługi.

## <a name="top-level-resource-logs-schema"></a>Schemat dzienników zasobów najwyższego poziomu

| Nazwa | Wymagane/Opcjonalne | Opis |
|---|---|---|
| time | Wymagane | Sygnatura czasowa zdarzenia (UTC). |
| resourceId | Wymagane | Identyfikator zasobu, który emituje zdarzenie. W przypadku usług dzierżawców jest to forma/tenants/tenant-ID/Providers/Provider-Name. |
| tenantId | Wymagane w przypadku dzienników dzierżawy | Identyfikator dzierżawy dzierżawy Active Directory, z którym jest powiązane to zdarzenie. Ta właściwość jest używana tylko w przypadku dzienników na poziomie dzierżawy, ale nie jest wyświetlana w dziennikach na poziomie zasobów. |
| operationName | Wymagane | Nazwa operacji reprezentowanej przez to zdarzenie. Jeśli zdarzenie reprezentuje operację RBAC, jest to nazwa operacji RBAC (np. Microsoft. Storage/storageAccounts/blobServices/obiekty blob/odczyt). Zwykle modelowane w formie Menedżer zasobów operacji, nawet jeśli nie są rzeczywiste udokumentowane operacje Menedżer zasobów ( `Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>` ) |
| operationVersion | Opcjonalne | Wersja interfejsu API skojarzona z operacją, jeśli operacjaname została wykonana przy użyciu interfejsu API (np. `http://myservice.windowsazure.net/object?api-version=2016-06-01`). Jeśli nie ma interfejsu API odpowiadającego tej operacji, wersja reprezentuje wersję tej operacji w przypadku, gdy właściwości skojarzone z operacją zmieniają się w przyszłości. |
| category | Wymagane | Kategoria dziennika zdarzenia. Kategoria to stopień szczegółowości, w którym można włączyć lub wyłączyć dzienniki dla określonego zasobu. Właściwości, które pojawiają się w obiekcie blob właściwości zdarzenia są takie same w określonej kategorii dziennika i typie zasobu. Typowe kategorie dzienników to "inspekcja" "działania" "wykonywanie" i "żądanie". |
| resultType | Opcjonalne | Stan zdarzenia. Typowe wartości to: rozpoczęte, w toku, zakończone powodzeniem, zakończone niepowodzeniem, aktywne i rozwiązane. |
| resultSignature | Opcjonalne | Stan podrzędny zdarzenia. Jeśli ta operacja odnosi się do wywołania interfejsu API REST, jest to kod stanu HTTP odpowiedniego wywołania REST. |
| resultDescription | Opcjonalne | Opis tekstu statycznego tej operacji, np. "Pobierz plik magazynu". |
| durationMs | Opcjonalne | Czas trwania operacji w milisekundach. |
| callerIpAddress | Opcjonalne | Adres IP wywołującego, jeśli operacja odnosi się do wywołania interfejsu API, które mogłoby pochodzić z jednostki z publicznie dostępnym adresem IP. |
| correlationId | Opcjonalne | Identyfikator GUID służący do grupowania razem z zestawem powiązanych zdarzeń. Zazwyczaj Jeśli dwa zdarzenia mają tę samą wartość OperationName, ale dwa różne stany (np. "Uruchomiono" i "powodzenie") współużytkują ten sam identyfikator korelacji. Może to również reprezentować inne relacje między zdarzeniami. |
| identity | Opcjonalne | Obiekt BLOB JSON, który opisuje tożsamość użytkownika lub aplikacji, która wykonała operację. Zwykle będzie to obejmować Token autoryzacji i oświadczeń/tokenu JWT z usługi Active Directory. |
| Poziom | Opcjonalne | Poziom ważności zdarzenia. Musi to być jeden z informacji, ostrzegawczy, błąd lub krytyczny. |
| location | Opcjonalne | Region zasobu emitującego zdarzenie, np. "Wschodnie stany USA" lub "Francja Południowa" |
| properties | Opcjonalne | Wszystkie rozszerzone właściwości powiązane z tą określoną kategorią zdarzeń. Wszystkie właściwości niestandardowe/unikatowe należy umieścić w tym "części B" schematu. |

## <a name="service-specific-schemas-for-resource-logs"></a>Schematy dotyczące usługi dla dzienników zasobów
Schemat dzienników diagnostycznych zasobów różni się w zależności od kategorii zasobów i dzienników. Ta lista zawiera wszystkie usługi, które udostępniają dostępne dzienniki zasobów i linki do usługi i schematu specyficznego dla kategorii, gdzie są dostępne.

| Usługa | Dokumentacja & schematu |
| --- | --- |
| Usługa Azure Active Directory | [Przegląd](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), [schemat dziennika inspekcji](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) i [schemat logowania](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| API Management | [Dzienniki zasobów API Management](../../api-management/api-management-howto-use-azure-monitor.md#resource-logs) |
| Bramy aplikacji |[Rejestrowanie Application Gateway](../../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Log Analytics dla Azure Automation](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Rejestrowanie Azure Batch](../../batch/batch-diagnostics.md) |
| Azure Database for MySQL | [Azure Database for MySQL dzienników diagnostycznych](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database for PostgreSQL | [Dzienniki Azure Database for PostgreSQL](../../postgresql/concepts-server-logs.md#resource-logs) |
| Azure Data Explorer | [Dzienniki usługi Azure Eksplorator danych](/azure/data-explorer/using-diagnostic-logs) |
| Cognitive Services | [Rejestrowanie w usłudze Azure Cognitive Services](../../cognitive-services/diagnostic-logging.md) |
| Container Registry | [Rejestrowanie Azure Container Registry](../../container-registry/container-registry-diagnostics-audit-logs.md) |
| Content Delivery Network | [Dzienniki platformy Azure dla usługi CDN](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Rejestrowanie Azure Cosmos DB](../../cosmos-db/logging.md) |
| Fabryka danych | [Monitorowanie fabryk danych przy użyciu Azure Monitor](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Uzyskiwanie dostępu do dzienników dla Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Uzyskiwanie dostępu do dzienników dla Azure Data Lake Store](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Event Hubs |[Dzienniki usługi Azure Event Hubs](../../event-hubs/event-hubs-diagnostic-logs.md) |
| Express Route | Schemat jest niedostępny. |
| Azure Firewall | Schemat jest niedostępny. |
| Usługa IoT Hub | [Operacje IoT Hub](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Usługa Key Vault |[Rejestrowanie Azure Key Vault](../../key-vault/general/logging.md) |
| Kubernetes Service |[Rejestrowanie usługi Azure Kubernetes](../../aks/view-master-logs.md#log-event-schema) |
| Moduł równoważenia obciążenia |[Analiza dzienników dotyczących usługi Azure Load Balancer](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Logic Apps — niestandardowy schemat śledzenia B2B](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Grupy zabezpieczeń sieci |[Usługa Log Analytics dla sieciowych grup zabezpieczeń](../../virtual-network/virtual-network-nsg-manage-log.md) |
| Ochrona przed atakami DDOS | [Zarządzanie Azure DDoS Protection Standard](../../virtual-network/manage-ddos-protection.md) |
| Power BI — warstwa Dedykowana | [Rejestrowanie Power BI Embedded na platformie Azure](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Recovery Services | [Model danych dla Azure Backup](../../backup/backup-azure-reports-data-model.md)|
| Wyszukiwanie |[Włączanie i używanie Analiza ruchu wyszukiwania](../../search/search-traffic-analytics.md) |
| Service Bus |[Dzienniki Azure Service Bus](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Rejestrowanie Azure SQL Database](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md) |
| Stream Analytics |[Dzienniki zadań](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | [Traffic Manager schematu dziennika](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Sieci wirtualne | Schemat jest niedostępny. |
| Bramy sieci wirtualnej | Schemat jest niedostępny. |

## <a name="supported-log-categories-per-resource-type"></a>Obsługiwane kategorie dzienników według typu zasobu

Niektóre kategorie mogą być obsługiwane tylko dla określonych typów zasobów. Jest to lista wszystkich, które są dostępne w pewnym formularzu.  Na przykład kategorie Microsoft. SQL/serwery/bazy danych nie są dostępne dla wszystkich typów baz danych. Aby uzyskać więcej informacji, zobacz [Informacje o rejestrowaniu diagnostyki SQL Database](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md). 

|Typ zasobu|Kategoria|Nazwa wyświetlana kategorii|
|---|---|---|
|Microsoft. AAD/domainServices|SystemSecurity|SystemSecurity|
|Microsoft. AAD/domainServices|Zarządzania kontem|Zarządzania kontem|
|Microsoft. AAD/domainServices|LogonLogoff|LogonLogoff|
|Microsoft. AAD/domainServices|ObjectAccess|ObjectAccess|
|Microsoft. AAD/domainServices|PolicyChange|PolicyChange|
|Microsoft. AAD/domainServices|PrivilegeUse|PrivilegeUse|
|Microsoft. AAD/domainServices|DetailTracking|DetailTracking|
|Microsoft. AAD/domainServices|DirectoryServiceAccess|DirectoryServiceAccess|
|Microsoft. AAD/domainServices|AccountLogon|AccountLogon|
|Microsoft. aadiam/dzierżawcy|Logowania|Logowania|
|Microsoft. AnalysisServices/serwery|Aparat|Aparat|
|Microsoft. AnalysisServices/serwery|Usługa|Usługa|
|Microsoft.ApiManagement/service|GatewayLogs|Dzienniki powiązane z bramą ApiManagement|
|Microsoft. AppPlatform/Sprężyna|ApplicationConsole|Konsola aplikacji|
|Microsoft. Automation/automationAccounts|JobLogs|Dzienniki zadań|
|Microsoft. Automation/automationAccounts|JobStreams|Strumienie zadań|
|Microsoft. Automation/automationAccounts|DscNodeStatus|Stan węzła DSC|
|Microsoft. Batch/batchAccounts|ServiceLog|Dzienniki usług|
|Microsoft. Batchai Job/obszary robocze|BaiClusterEvent|BaiClusterEvent|
|Microsoft. Batchai Job/obszary robocze|BaiClusterNodeEvent|BaiClusterNodeEvent|
|Microsoft. Batchai Job/obszary robocze|BaiJobEvent|BaiJobEvent|
|Microsoft. łańcucha bloków/blockchainMembers|BlockchainApplication|Aplikacja łańcucha bloków|
|Microsoft. łańcucha bloków/blockchainMembers|Serwer proxy|Serwer proxy|
|Microsoft. CDN/profile/punkty końcowe|CoreAnalytics|Pobiera metryki punktu końcowego, np. przepustowość, ruch wychodzący itp.|
|Microsoft. ClassicNetwork/networksecuritygroups|Zdarzenie przepływu reguły sieciowej grupy zabezpieczeń|Zdarzenie przepływu reguły sieciowej grupy zabezpieczeń|
|Microsoft. CognitiveServices/konta|Inspekcja|Dzienniki inspekcji|
|Microsoft. CognitiveServices/konta|RequestResponse|Dzienniki żądań i odpowiedzi|
|Microsoft. ContainerRegistry/rejestry|ContainerRegistryRepositoryEvents|Dzienniki RepositoryEvent (wersja zapoznawcza)|
|Microsoft. ContainerRegistry/rejestry|ContainerRegistryLoginEvents|Zdarzenia logowania (wersja zapoznawcza)|
|Microsoft. ContainerService/managedClusters|polecenia — apiserver|Serwer interfejsu API Kubernetes|
|Microsoft. ContainerService/managedClusters|polecenia-Controller-Manager|Menedżer kontrolera Kubernetes|
|Microsoft. ContainerService/managedClusters|polecenia — Scheduler|Harmonogram Kubernetes|
|Microsoft. ContainerService/managedClusters|polecenia — Inspekcja|Inspekcja Kubernetes|
|Microsoft. ContainerService/managedClusters|klaster — Autoskalowanie|Automatyczne skalowanie klastra Kubernetes|
|Microsoft. datacegły/obszary robocze|dBfs|System plików usługi Databricks|
|Microsoft. datacegły/obszary robocze|oparty|Klastry datacegły|
|Microsoft. datacegły/obszary robocze|Konta|Konta datakostek|
|Microsoft. datacegły/obszary robocze|zadania|Zadania datakostki|
|Microsoft. datacegły/obszary robocze|notesu|Notes usługi Databricks|
|Microsoft. datacegły/obszary robocze|SSH|Połączenia SSH|
|Microsoft. datacegły/obszary robocze|obszar roboczy|Obszar roboczy datakosteks|
|Microsoft. datacegły/obszary robocze|wpisy tajne|Wpisy tajne datakostek|
|Microsoft. datacegły/obszary robocze|Uprawnienia sqlpermissions|Datakosteks — uprawnienia sqlpermissions|
|Microsoft. datacegły/obszary robocze|instancePools|Pule wystąpień|
|Microsoft. datacatalog/wykazy|ScanStatusLogEvent|ScanStatus|
|Microsoft. DataFactory/fabryki|ActivityRuns|Dziennik uruchomienia działania potoku|
|Microsoft. DataFactory/fabryki|PipelineRuns|Dziennik przebiegów potoku|
|Microsoft. DataFactory/fabryki|TriggerRuns|Dziennik uruchomienia wyzwalacza|
|Microsoft. DataLakeAnalytics/konta|Inspekcja|Dzienniki inspekcji|
|Microsoft. DataLakeAnalytics/konta|Żądania|Dzienniki żądań|
|Microsoft. kontach datalakestore/konta|Inspekcja|Dzienniki inspekcji|
|Microsoft. kontach datalakestore/konta|Żądania|Dzienniki żądań|
|Microsoft. dataudział/konta|Udziały|Udziały|
|Microsoft. dataudział/konta|ShareSubscriptions|Udostępnianie subskrypcji|
|Microsoft. dataudział/konta|SentShareSnapshots|Wysłane migawki udziałów|
|Microsoft. dataudział/konta|ReceivedShareSnapshots|Odebrane migawki udziału|
|Microsoft. DBforMySQL/serwery|MySqlSlowLogs|Dzienniki serwera MySQL|
|Microsoft. DBforMySQL/serwery|MySqlAuditLogs|Dzienniki inspekcji MySQL|
|Microsoft. DBforPostgreSQL/serwery|PostgreSQLLogs|Dzienniki serwera PostgreSQL|
|Microsoft. DBforPostgreSQL/serwery|QueryStoreRuntimeStatistics|Statystyka środowiska uruchomieniowego magazynu zapytań PostgreSQL|
|Microsoft. DBforPostgreSQL/serwery|QueryStoreWaitStatistics|Statystyka oczekiwania magazynu zapytań PostgreSQL|
|Microsoft. DBforPostgreSQL/serversv2|PostgreSQLLogs|Dzienniki serwera PostgreSQL|
|Microsoft. DBforPostgreSQL/serversv2|QueryStoreRuntimeStatistics|Statystyka środowiska uruchomieniowego magazynu zapytań PostgreSQL|
|Microsoft. DBforPostgreSQL/serversv2|QueryStoreWaitStatistics|Statystyka oczekiwania magazynu zapytań PostgreSQL|
|Microsoft. DesktopVirtualization/obszary robocze|Punkt kontrolny|Punkt kontrolny|
|Microsoft. DesktopVirtualization/obszary robocze|Error|Error|
|Microsoft. DesktopVirtualization/obszary robocze|Zarządzanie|Zarządzanie|
|Microsoft. DesktopVirtualization/obszary robocze|Źródło danych|Źródło danych|
|Microsoft. DesktopVirtualization/applicationGroups|Punkt kontrolny|Punkt kontrolny|
|Microsoft. DesktopVirtualization/applicationGroups|Error|Error|
|Microsoft. DesktopVirtualization/applicationGroups|Zarządzanie|Zarządzanie|
|Microsoft. DesktopVirtualization/hostPools|Punkt kontrolny|Punkt kontrolny|
|Microsoft. DesktopVirtualization/hostPools|Error|Error|
|Microsoft. DesktopVirtualization/hostPools|Zarządzanie|Zarządzanie|
|Microsoft. DesktopVirtualization/hostPools|Połączenie|Połączenie|
|Microsoft. DesktopVirtualization/hostPools|HostRegistration|HostRegistration|
|Microsoft. Devices/IotHubs|Połączenia|Połączenia|
|Microsoft. Devices/IotHubs|DeviceTelemetry|Dane telemetryczne urządzenia|
|Microsoft. Devices/IotHubs|C2DCommands|Polecenia C2D|
|Microsoft. Devices/IotHubs|DeviceIdentityOperations|Operacje tożsamości urządzeń|
|Microsoft. Devices/IotHubs|FileUploadOperations|Operacje przekazywania plików|
|Microsoft. Devices/IotHubs|Trasy|Trasy|
|Microsoft. Devices/IotHubs|D2CTwinOperations|D2CTwinOperations|
|Microsoft. Devices/IotHubs|C2DTwinOperations|C2D — operacje na Przędzce|
|Microsoft. Devices/IotHubs|TwinQueries|Zapytania bliźniaczye|
|Microsoft. Devices/IotHubs|JobsOperations|Operacje zadań|
|Microsoft. Devices/IotHubs|DirectMethods|Metody bezpośrednie|
|Microsoft. Devices/IotHubs|DistributedTracing|Śledzenie rozproszone (wersja zapoznawcza)|
|Microsoft. Devices/IotHubs|Konfiguracje|Konfiguracje|
|Microsoft. Devices/IotHubs|DeviceStreams|Strumienie urządzeń (wersja zapoznawcza)|
|Microsoft. Devices/provisioningServices|DeviceOperations|Operacje na urządzeniach|
|Microsoft. Devices/provisioningServices|Operacje serviceoperations|Operacje usługi|
|Microsoft. DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft. DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft. DocumentDB/databaseAccounts|QueryRuntimeStatistics|QueryRuntimeStatistics|
|Microsoft. DocumentDB/databaseAccounts|PartitionKeyStatistics|PartitionKeyStatistics|
|Microsoft. DocumentDB/databaseAccounts|ControlPlaneRequests|ControlPlaneRequests|
|Microsoft. EnterpriseKnowledgeGraph/usługi|AuditEvent|Dziennik AuditEvent|
|Microsoft. EnterpriseKnowledgeGraph/usługi|Problem z|Dziennik wydawania dataissue|
|Microsoft. EnterpriseKnowledgeGraph/usługi|Żądania|Dziennik konfiguracji|
|Microsoft. EventHub/przestrzenie nazw|ArchiveLogs|Archiwizuj dzienniki|
|Microsoft. EventHub/przestrzenie nazw|OperationalLogs|Dzienniki operacyjne|
|Microsoft. EventHub/przestrzenie nazw|AutoScaleLogs|Automatyczne skalowanie dzienników|
|Microsoft. EventHub/przestrzenie nazw|KafkaCoordinatorLogs|Dzienniki koordynatora Kafka|
|Microsoft. EventHub/przestrzenie nazw|KafkaUserErrorLogs|Kafka dzienniki błędów użytkownika|
|Microsoft. EventHub/przestrzenie nazw|EventHubVNetConnectionEvent|Dzienniki połączeń sieci wirtualnej/IP filtrowania|
|Microsoft. EventHub/przestrzenie nazw|CustomerManagedKeyUserLogs|Dzienniki kluczy zarządzanych przez klienta|
|Microsoft. HealthcareApis/usługi|AuditLogs|Dzienniki inspekcji|
|Microsoft. Insights/AutoscaleSettings|AutoscaleEvaluations|Obliczenia automatycznego skalowania|
|Microsoft. Insights/AutoscaleSettings|AutoscaleScaleActions|Akcje skalowania automatycznego skalowania|
|Microsoft. IoTSpaces/Graph|Ślad|Ślad|
|Microsoft. IoTSpaces/Graph|Działał|Działał|
|Microsoft. IoTSpaces/Graph|Inspekcja|Inspekcja|
|Microsoft. IoTSpaces/Graph|UserDefinedFunction|UserDefinedFunction|
|Microsoft. IoTSpaces/Graph|Ruch przychodzący|Ruch przychodzący|
|Microsoft. IoTSpaces/Graph|Ruch wychodzący|Ruch wychodzący|
|Microsoft./magazyny kluczy|AuditEvent|Dzienniki inspekcji|
|Microsoft. Kusto/klastry|SucceededIngestion|Pomyślne operacje pozyskiwania|
|Microsoft. Kusto/klastry|FailedIngestion|Nieudane operacje pozyskiwania|
|Microsoft. Logic/przepływy pracy|Element|Zdarzenia diagnostyczne środowiska uruchomieniowego przepływu pracy|
|Microsoft. Logic/integrationAccounts|IntegrationAccountTrackingEvents|Zdarzenia śledzenia konta integracji|
|Microsoft. MachineLearningServices/obszary robocze|AmlComputeClusterEvent|AmlComputeClusterEvent|
|Microsoft. MachineLearningServices/obszary robocze|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|
|Microsoft. MachineLearningServices/obszary robocze|AmlComputeJobEvent|AmlComputeJobEvent|
|Microsoft. Media/MediaServices|KeyDeliveryRequests|Żądania dostarczania kluczy|
|Microsoft. Network/networksecuritygroups|NetworkSecurityGroupEvent|Zdarzenie sieciowej grupy zabezpieczeń|
|Microsoft. Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Licznik reguły sieciowej grupy zabezpieczeń|
|Microsoft. Network/networksecuritygroups|NetworkSecurityGroupFlowEvent|Zdarzenie przepływu reguły sieciowej grupy zabezpieczeń|
|Microsoft. Network/adresów publicipaddress|DDoSProtectionNotifications|Powiadomienia o ochronie DDoS|
|Microsoft. Network/adresów publicipaddress|DDoSMitigationFlowLogs|Dzienniki przepływów decyzji o łagodzeniu DDoS|
|Microsoft. Network/adresów publicipaddress|DDoSMitigationReports|Raporty dotyczące środków zaradczych DDoS|
|Microsoft. Network/virtualNetworks|VMProtectionAlerts|Alerty ochrony maszyny wirtualnej|
|Microsoft. Network/applicationGateways|ApplicationGatewayAccessLog|Application Gateway dziennika dostępu|
|Microsoft. Network/applicationGateways|ApplicationGatewayPerformanceLog|Application Gateway dziennik wydajności|
|Microsoft. Network/applicationGateways|ApplicationGatewayFirewallLog|Application Gateway dziennika zapory|
|Microsoft. Network/azurefirewalls|AzureFirewallApplicationRule|Reguła aplikacji zapory platformy Azure|
|Microsoft. Network/azurefirewalls|AzureFirewallNetworkRule|Reguła sieci zapory platformy Azure|
|Microsoft. Network/virtualNetworkGateways|GatewayDiagnosticLog|Dzienniki diagnostyczne bramy|
|Microsoft. Network/virtualNetworkGateways|TunnelDiagnosticLog|Dzienniki diagnostyczne tunelu|
|Microsoft. Network/virtualNetworkGateways|RouteDiagnosticLog|Kierowanie dzienników diagnostycznych|
|Microsoft. Network/virtualNetworkGateways|IKEDiagnosticLog|Dzienniki diagnostyczne IKE|
|Microsoft. Network/virtualNetworkGateways|P2SDiagnosticLog|P2S dzienników diagnostycznych|
|Microsoft. Network/trafficManagerProfiles|ProbeHealthStatusEvents|Zdarzenie Traffic Manager wyników kondycji sondy|
|Microsoft. Network/expressRouteCircuits|PeeringRouteLog|Dzienniki tabel tras komunikacji równorzędnej|
|Microsoft. Network/vpnGateways|GatewayDiagnosticLog|Dzienniki diagnostyczne bramy|
|Microsoft. Network/vpnGateways|TunnelDiagnosticLog|Dzienniki diagnostyczne tunelu|
|Microsoft. Network/vpnGateways|RouteDiagnosticLog|Kierowanie dzienników diagnostycznych|
|Microsoft. Network/vpnGateways|IKEDiagnosticLog|Dzienniki diagnostyczne IKE|
|Microsoft. Network/usługi frontdoor|FrontdoorAccessLog|Dziennik dostępu usługa frontdoor|
|Microsoft. Network/usługi frontdoor|FrontdoorWebApplicationFirewallLog|Dziennik zapory aplikacji sieci Web usługa frontdoor|
|Microsoft. Network/p2sVpnGateways|GatewayDiagnosticLog|Dzienniki diagnostyczne bramy|
|Microsoft. Network/p2sVpnGateways|IKEDiagnosticLog|Dzienniki diagnostyczne IKE|
|Microsoft. Network/p2sVpnGateways|P2SDiagnosticLog|P2S dzienników diagnostycznych|
|Microsoft. Network/bastionHosts|BastionAuditLogs|Dzienniki inspekcji bastionu|
|Microsoft. Network/loadBalancers|LoadBalancerAlertEvent|Zdarzenia alertu Load Balancer|
|Microsoft. Network/loadBalancers|LoadBalancerProbeHealthStatus|Load Balancer stan kondycji sondy|
|Microsoft. PowerBIDedicated/pojemności|Aparat|Aparat|
|Microsoft. RecoveryServices/magazyny|AzureBackupReport|Azure Backup dane raportowania|
|Microsoft. RecoveryServices/magazyny|CoreAzureBackup|Podstawowe Azure Backup dane|
|Microsoft. RecoveryServices/magazyny|AddonAzureBackupJobs|Dodatek Azure Backup danych zadania|
|Microsoft. RecoveryServices/magazyny|AddonAzureBackupAlerts|Dodatek Azure Backup danych alertów|
|Microsoft. RecoveryServices/magazyny|AddonAzureBackupPolicy|Dodatek Azure Backup danych zasad|
|Microsoft. RecoveryServices/magazyny|AddonAzureBackupStorage|Dodatek Azure Backup danych magazynu|
|Microsoft. RecoveryServices/magazyny|AddonAzureBackupProtectedInstance|Dodatek Azure Backup danych chronionego wystąpienia|
|Microsoft. RecoveryServices/magazyny|AzureSiteRecoveryJobs|Zadania Azure Site Recovery|
|Microsoft. RecoveryServices/magazyny|AzureSiteRecoveryEvents|Zdarzenia Azure Site Recovery|
|Microsoft. RecoveryServices/magazyny|AzureSiteRecoveryReplicatedItems|Azure Site Recovery zreplikowane elementy|
|Microsoft. RecoveryServices/magazyny|AzureSiteRecoveryReplicationStats|Statystyka replikacji Azure Site Recovery|
|Microsoft. RecoveryServices/magazyny|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery punktów odzyskiwania|
|Microsoft. RecoveryServices/magazyny|AzureSiteRecoveryReplicationDataUploadRate|Szybkość przekazywania danych replikacji Azure Site Recovery|
|Microsoft. RecoveryServices/magazyny|AzureSiteRecoveryProtectedDiskDataChurn|Azure Site Recovery zmienionych danych dysku chronionego|
|Microsoft. Search/searchServices|OperationLogs|Dzienniki operacji|
|Microsoft. ServiceBus/przestrzenie nazw|OperationalLogs|Dzienniki operacyjne|
|Microsoft. SQL/serwery/bazy danych|SQLInsights|Szczegółowe informacje SQL|
|Microsoft. SQL/serwery/bazy danych|AutomaticTuning|Automatyczne dostrajanie|
|Microsoft. SQL/serwery/bazy danych|QueryStoreRuntimeStatistics|Statystyka środowiska uruchomieniowego magazynu zapytań|
|Microsoft. SQL/serwery/bazy danych|QueryStoreWaitStatistics|Statystyka oczekiwania magazynu zapytań|
|Microsoft. SQL/serwery/bazy danych|Errors|Errors|
|Microsoft. SQL/serwery/bazy danych|DatabaseWaitStatistics|Statystyka oczekiwania bazy danych|
|Microsoft. SQL/serwery/bazy danych|Limity czasu|Limity czasu|
|Microsoft. SQL/serwery/bazy danych|Bloki|Bloki|
|Microsoft. SQL/serwery/bazy danych|Zakleszczenia|Zakleszczenia|
|Microsoft. SQL/serwery/bazy danych|Inspekcja|Dzienniki inspekcji|
|Microsoft. SQL/serwery/bazy danych|SQLSecurityAuditEvents|Zdarzenie inspekcji zabezpieczeń SQL|
|Microsoft. SQL/serwery/bazy danych|DmsWorkers|Pracownicy DMS|
|Microsoft. SQL/serwery/bazy danych|ExecRequests|Żądania exec|
|Microsoft. SQL/serwery/bazy danych|RequestSteps|Kroki żądania|
|Microsoft. SQL/serwery/bazy danych|Sqlrequests|Żądania SQL|
|Microsoft. SQL/serwery/bazy danych|Czeka|Czeka|
|Microsoft. SQL/managedInstances|ResourceUsageStats|Statystyka użycia zasobów|
|Microsoft. SQL/managedInstances|SQLSecurityAuditEvents|Zdarzenie inspekcji zabezpieczeń SQL|
|Microsoft. SQL/managedInstances/bazy danych|SQLInsights|Szczegółowe informacje SQL|
|Microsoft. SQL/managedInstances/bazy danych|QueryStoreRuntimeStatistics|Statystyka środowiska uruchomieniowego magazynu zapytań|
|Microsoft. SQL/managedInstances/bazy danych|QueryStoreWaitStatistics|Statystyka oczekiwania magazynu zapytań|
|Microsoft. SQL/managedInstances/bazy danych|Errors|Errors|
|Microsoft. Storage/storageAccounts/tableServices|StorageRead|StorageRead|
|Microsoft. Storage/storageAccounts/tableServices|StorageWrite|StorageWrite|
|Microsoft. Storage/storageAccounts/tableServices|StorageDelete|StorageDelete|
|Microsoft. Storage/storageAccounts/blobServices|StorageRead|StorageRead|
|Microsoft. Storage/storageAccounts/blobServices|StorageWrite|StorageWrite|
|Microsoft. Storage/storageAccounts/blobServices|StorageDelete|StorageDelete|
|Microsoft. Storage/storageAccounts/fileServices|StorageRead|StorageRead|
|Microsoft. Storage/storageAccounts/fileServices|StorageWrite|StorageWrite|
|Microsoft. Storage/storageAccounts/fileServices|StorageDelete|StorageDelete|
|Microsoft. Storage/storageAccounts/queueServices|StorageRead|StorageRead|
|Microsoft. Storage/storageAccounts/queueServices|StorageWrite|StorageWrite|
|Microsoft. Storage/storageAccounts/queueServices|StorageDelete|StorageDelete|
|Microsoft. StreamAnalytics/streamingjobs|Wykonanie|Wykonanie|
|Microsoft. StreamAnalytics/streamingjobs|Tworzenie|Tworzenie|
|Microsoft. Web/hostingenvironments|AppServiceEnvironmentPlatformLogs|Dzienniki platformy App Service Environment|
|Microsoft. Web/witryny|FunctionAppLogs|Dzienniki aplikacji funkcji|
|Microsoft. Web/witryny|AppServiceHTTPLogs|Dzienniki HTTP|
|Microsoft. Web/witryny|AppServiceConsoleLogs|App Service dzienniki konsoli|
|Microsoft. Web/witryny|AppServiceAppLogs|App Service dzienników aplikacji|
|Microsoft. Web/witryny|AppServiceFileAuditLogs|Dzienniki inspekcji zmian zawartości witryny|
|Microsoft. Web/witryny|AppServiceAuditLogs|Dzienniki inspekcji dostępu|
|Microsoft. Web/Sites/miejsca|FunctionAppLogs|Dzienniki aplikacji funkcji|
|Microsoft. Web/Sites/miejsca|AppServiceHTTPLogs|Dzienniki HTTP|
|Microsoft. Web/Sites/miejsca|AppServiceConsoleLogs|Dzienniki konsoli|
|Microsoft. Web/Sites/miejsca|AppServiceAppLogs|Dzienniki aplikacji|
|Microsoft. Web/Sites/miejsca|AppServiceFileAuditLogs|Dzienniki inspekcji zmian zawartości witryny|
|Microsoft. Web/Sites/miejsca|AppServiceAuditLogs|Dzienniki inspekcji dostępu|

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o dziennikach zasobów](../../azure-monitor/platform/platform-logs-overview.md)
* [Przesyłanie strumieniowe dzienników zasobów zasobów do **Event Hubs**](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)
* [Zmienianie ustawień diagnostycznych dziennika zasobów przy użyciu interfejsu API REST Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)
* [Analizowanie dzienników z usługi Azure Storage za pomocą Log Analytics](../../azure-monitor/platform/collect-azure-metrics-logs.md)
