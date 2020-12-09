---
title: Azure Monitor obsługiwane usługi i kategorie dzienników zasobów
description: Informacje dotyczące Azure Monitor zrozumieć obsługiwane usługi i schemat zdarzeń dla dzienników zasobów platformy Azure.
ms.subservice: logs
ms.topic: reference
ms.date: 12/09/2020
ms.openlocfilehash: c7b2d48b40843930bba78f54d2294769d952daf6
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96931234"
---
# <a name="supported-categories-for-azure-resource-logs"></a>Kategorie obsługiwane w przypadku dzienników zasobów platformy Azure

> [!NOTE]
> Dzienniki zasobów były wcześniej znane jako dzienniki diagnostyczne. Nazwa została zmieniona w październiku 2019, ponieważ typy dzienników zebrane przez Azure Monitor przesunięte w celu uwzględnienia więcej niż tylko zasobów platformy Azure.

[Dzienniki zasobów Azure monitor](./platform-logs-overview.md) to dzienniki wysyłane przez usługi platformy Azure opisujące operacje tych usług lub zasobów. Wszystkie dzienniki zasobów dostępne za pomocą Azure Monitor współdzielą wspólny schemat najwyższego poziomu, z elastycznością dla każdej usługi do emitowania unikatowych właściwości dla własnych zdarzeń.

Kombinacja typu zasobu (dostępnego we `resourceId` Właściwości) i `category` unikatowo identyfikujący schemat. Istnieje wspólny schemat dla wszystkich dzienników zasobów z polami, które następnie zostały dodane do różnych kategorii dzienników. Aby uzyskać więcej informacji, zobacz [schemat wspólnych i specyficzny dla usługi dla dzienników zasobów platformy Azure]()


## <a name="costs"></a>Koszty

Istnieją koszty związane z wysyłaniem i przechowywaniem danych w programie do Log Analytics, usługi Azure Storage i/lub centrum zdarzeń. Możesz płacić za koszt, aby uzyskać dostęp do tych lokalizacji i w ten sposób przechowywać dane.  Dzienniki zasobów to jeden typ danych, które można wysłać do tych lokalizacji. Istnieje dodatkowe [koszty eksportowania niektórych kategorii dzienników zasobów](https://azure.microsoft.com/pricing/details/monitor/) do tych lokalizacji, podczas gdy inne nie mają kosztów eksportu. Szczegóły dotyczące kosztów eksportu są wymienione w poniższej tabeli.

## <a name="supported-log-categories-per-resource-type"></a>Obsługiwane kategorie dzienników według typu zasobu

Poniżej znajduje się lista typów dzienników dostępnych dla każdego typu zasobu. 

Niektóre kategorie mogą być obsługiwane tylko dla określonych typów zasobów. Zapoznaj się z dokumentacją specyficzną dla zasobów, jeśli uważasz, że brakuje zasobu. Na przykład kategorie Microsoft. SQL/serwery/bazy danych nie są dostępne dla wszystkich typów baz danych. Aby uzyskać więcej informacji, zobacz [Informacje o rejestrowaniu diagnostyki SQL Database](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md). 

Jeśli nadal brakuje czegoś, możesz otworzyć komentarz w witrynie GitHub w dolnej części tego artykułu.
## <a name="microsoftanalysisservicesservers"></a>Microsoft. AnalysisServices/serwery

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|Aparat|Aparat|
|Usługa|Usługa|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|GatewayLogs|Dzienniki powiązane z bramą ApiManagement|


## <a name="microsoftappplatformspring"></a>Microsoft. AppPlatform/Sprężyna

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|ApplicationConsole|Konsola aplikacji|
|SystemLogs|Dzienniki systemu|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft. Automation/automationAccounts

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|DscNodeStatus|Stan węzła DSC|
|JobLogs|Dzienniki zadań|
|JobStreams|Strumienie zadań|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|ServiceLog|Dzienniki usług|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/obszary robocze

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|BaiClusterEvent|BaiClusterEvent|
|BaiClusterNodeEvent|BaiClusterNodeEvent|
|BaiJobEvent|BaiJobEvent|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft. łańcucha bloków/blockchainMembers

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|BlockchainApplication|Aplikacja łańcucha bloków|
|FabricOrderer|Zamówienie sieci szkieletowej|
|FabricPeer|Element równorzędny sieci szkieletowej|
|Serwer proxy|Serwer proxy|


## <a name="microsoftblockchaincordamembers"></a>Microsoft. łańcucha bloków/cordaMembers

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|BlockchainApplication|Aplikacja łańcucha bloków|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/cdnwebapplicationfirewallpolicies

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|WebApplicationFirewallLogs|Dzienniki zapory aplikacja w sieci Web|


## <a name="microsoftcdnprofiles"></a>Microsoft. CDN/profile

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|AzureCdnAccessLog|Dziennik dostępu usługi Azure CDN|


## <a name="microsoftcdnprofilesendpoints"></a>Microsoft. CDN/profile/punkty końcowe

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|CoreAnalytics|Pobiera metryki punktu końcowego, np. przepustowość, ruch wychodzący itp.|


## <a name="microsoftclassicnetworknetworksecuritygroups"></a>Microsoft. ClassicNetwork/networksecuritygroups

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|Zdarzenie przepływu reguły sieciowej grupy zabezpieczeń|Zdarzenie przepływu reguły sieciowej grupy zabezpieczeń|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft. CognitiveServices/konta

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|Inspekcja|Dzienniki inspekcji|
|RequestResponse|Dzienniki żądań i odpowiedzi|
|Ślad|Dzienniki śledzenia|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft. ContainerRegistry/rejestry

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|ContainerRegistryLoginEvents|Zdarzenia logowania|
|ContainerRegistryRepositoryEvents|Dzienniki RepositoryEvent|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft. ContainerService/managedClusters

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|klaster — Autoskalowanie|Automatyczne skalowanie klastra Kubernetes|
|polecenia — apiserver|Serwer interfejsu API Kubernetes|
|polecenia — Inspekcja|Inspekcja Kubernetes|
|polecenia-Controller-Manager|Menedżer kontrolera Kubernetes|
|polecenia — Scheduler|Harmonogram Kubernetes|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|AuditLogs|Dzienniki inspekcji dla wywołań MiniRP|


## <a name="microsoftdatabricksworkspaces"></a>Microsoft. datacegły/obszary robocze

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|Konta|Konta datakostek|
|oparty|Klastry datacegły|
|dBfs|System plików usługi Databricks|
|instancePools|Pule wystąpień|
|zadania|Zadania datakostki|
|notebook|Notes usługi Databricks|
|wpisy tajne|Wpisy tajne datakostek|
|Uprawnienia sqlpermissions|Datakosteks — uprawnienia sqlpermissions|
|SSH|Połączenia SSH|
|obszar roboczy|Obszar roboczy datakosteks|


## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/fabryki

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|ActivityRuns|Dziennik uruchomienia działania potoku|
|PipelineRuns|Dziennik przebiegów potoku|
|TriggerRuns|Dziennik uruchomienia wyzwalacza|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft. kontach datalakestore/konta

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|Inspekcja|Dzienniki inspekcji|
|Żądania|Dzienniki żądań|


## <a name="microsoftdatashareaccounts"></a>Microsoft. dataudział/konta

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|ReceivedShareSnapshots|Odebrane migawki udziału|
|SentShareSnapshots|Wysłane migawki udziałów|
|Udziały|Udziały|
|ShareSubscriptions|Udostępnianie subskrypcji|


## <a name="microsoftdbformariadbservers"></a>Microsoft. DBforMariaDB/serwery

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|MySqlAuditLogs|Dzienniki inspekcji MariaDB|
|MySqlSlowLogs|Dzienniki serwera MariaDB|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft. DBforMySQL/flexibleServers

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|MySqlAuditLogs|Dzienniki inspekcji MySQL|
|MySqlSlowLogs|Wolne dzienniki MySQL|


## <a name="microsoftdbformysqlservers"></a>Microsoft. DBforMySQL/serwery

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|MySqlAuditLogs|Dzienniki inspekcji MySQL|
|MySqlSlowLogs|Dzienniki serwera MySQL|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft. DBforPostgreSQL/flexibleServers

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|PostgreSQLLogs|Dzienniki serwera PostgreSQL|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft. DBforPostgreSQL/serwery

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|PostgreSQLLogs|Dzienniki serwera PostgreSQL|
|QueryStoreRuntimeStatistics|Statystyka środowiska uruchomieniowego magazynu zapytań PostgreSQL|
|QueryStoreWaitStatistics|Statystyka oczekiwania magazynu zapytań PostgreSQL|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft. DBforPostgreSQL/serversv2

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|PostgreSQLLogs|Dzienniki serwera PostgreSQL|


## <a name="microsoftdesktopvirtualizationapplicationgroups"></a>Microsoft. DesktopVirtualization/applicationgroups

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|Punkt kontrolny|Punkt kontrolny|
|Błąd|Błąd|
|Zarządzanie|Zarządzanie|


## <a name="microsoftdesktopvirtualizationhostpools"></a>Microsoft. DesktopVirtualization/hostpools

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|Punkt kontrolny|Punkt kontrolny|
|Połączenie|Połączenie|
|Błąd|Błąd|
|Rejestracja hosta|Rejestracja hosta|
|Zarządzanie|Zarządzanie|


## <a name="microsoftdesktopvirtualizationworkspaces"></a>Microsoft. DesktopVirtualization/obszary robocze

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|Punkt kontrolny|Punkt kontrolny|
|Błąd|Błąd|
|Źródło danych|Źródło danych|
|Zarządzanie|Zarządzanie|


## <a name="microsoftdevicesiothubs"></a>Microsoft. Devices/IotHubs

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|C2DCommands|Polecenia C2D|
|C2DTwinOperations|C2D — operacje na Przędzce|
|Konfiguracje|Konfiguracje|
|Połączenia|Połączenia|
|D2CTwinOperations|D2CTwinOperations|
|DeviceIdentityOperations|Operacje tożsamości urządzeń|
|DeviceStreams|Strumienie urządzeń (wersja zapoznawcza)|
|DeviceTelemetry|Dane telemetryczne urządzenia|
|DirectMethods|Metody bezpośrednie|
|DistributedTracing|Śledzenie rozproszone (wersja zapoznawcza)|
|FileUploadOperations|Operacje przekazywania plików|
|JobsOperations|Operacje zadań|
|Trasy|Trasy|
|TwinQueries|Zapytania bliźniaczye|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft. Devices/provisioningServices

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|DeviceOperations|Operacje na urządzeniach|
|Operacje serviceoperations|Operacje usługi|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|CassandraRequests|CassandraRequests|
|ControlPlaneRequests|ControlPlaneRequests|
|DataPlaneRequests|DataPlaneRequests|
|GremlinRequests|GremlinRequests|
|MongoRequests|MongoRequests|
|PartitionKeyRUConsumption|PartitionKeyRUConsumption|
|PartitionKeyStatistics|PartitionKeyStatistics|
|QueryRuntimeStatistics|QueryRuntimeStatistics|


## <a name="microsofteventgriddomains"></a>Microsoft. EventGrid/domeny

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|DeliveryFailures|Dzienniki błędów dostarczania|
|PublishFailures|Publikowanie dzienników błędów|


## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|DeliveryFailures|Dzienniki błędów dostarczania|


## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/tematy

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|DeliveryFailures|Dzienniki błędów dostarczania|
|PublishFailures|Publikowanie dzienników błędów|


## <a name="microsofteventhubnamespaces"></a>Microsoft. EventHub/przestrzenie nazw

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|ArchiveLogs|Archiwizuj dzienniki|
|AutoScaleLogs|Automatyczne skalowanie dzienników|
|CustomerManagedKeyUserLogs|Dzienniki kluczy zarządzanych przez klienta|
|EventHubVNetConnectionEvent|Dzienniki połączeń sieci wirtualnej/IP filtrowania|
|KafkaCoordinatorLogs|Dzienniki koordynatora Kafka|
|KafkaUserErrorLogs|Kafka dzienniki błędów użytkownika|
|OperationalLogs|Dzienniki operacyjne|


## <a name="microsofthealthcareapisservices"></a>Microsoft. HealthcareApis/usługi

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|AuditLogs|Dzienniki inspekcji|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft. Insights/AutoscaleSettings

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|AutoscaleEvaluations|Obliczenia automatycznego skalowania|
|AutoscaleScaleActions|Akcje skalowania automatycznego skalowania|


## <a name="microsoftinsightscomponents"></a>Microsoft. Insights/składniki

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|AppAvailabilityResults|Wyniki dostępności|
|AppBrowserTimings|Chronometraż przeglądarki|
|AppDependencies|Zależności|
|AppEvents|Zdarzenia|
|AppExceptions|Wyjątki|
|AppMetrics|Metryki|
|AppPageViews|Wyświetlenia stron|
|AppPerformanceCounters|Liczniki wydajności|
|AppRequests|Żądania|
|AppSystemEvents|Zdarzenia systemowe|
|AppTraces|Ślady|


## <a name="microsoftkeyvaultvaults"></a>Microsoft./magazyny kluczy

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|AuditEvent|Dzienniki inspekcji|


## <a name="microsoftkustoclusters"></a>Microsoft. Kusto/klastry

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|Polecenie|Polecenie|
|FailedIngestion|Nieudane operacje pozyskiwania|
|IngestionBatching|Dzielenie na partie pozyskiwania|
|Zapytanie|Zapytanie|
|SucceededIngestion|Pomyślne operacje pozyskiwania|
|TableDetails|Szczegóły tabeli|
|TableUsageStatistics|Statystyka użycia tabeli|


## <a name="microsoftlogicintegrationaccounts"></a>Microsoft. Logic/integrationAccounts

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|IntegrationAccountTrackingEvents|Zdarzenia śledzenia konta integracji|


## <a name="microsoftlogicworkflows"></a>Microsoft. Logic/przepływy pracy

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|Element|Zdarzenia diagnostyczne środowiska uruchomieniowego przepływu pracy|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft. MachineLearningServices/obszary robocze

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|AmlComputeClusterEvent|AmlComputeClusterEvent|
|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|
|AmlComputeCpuGpuUtilization|AmlComputeCpuGpuUtilization|
|AmlComputeJobEvent|AmlComputeJobEvent|
|AmlRunStatusChangedEvent|AmlRunStatusChangedEvent|


## <a name="microsoftmediamediaservices"></a>Microsoft. Media/MediaServices

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|KeyDeliveryRequests|Żądania dostarczania kluczy|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft. Network/applicationGateways

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|ApplicationGatewayAccessLog|Application Gateway dziennika dostępu|
|ApplicationGatewayFirewallLog|Application Gateway dziennika zapory|
|ApplicationGatewayPerformanceLog|Application Gateway dziennik wydajności|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft. Network/azurefirewalls

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|AzureFirewallApplicationRule|Reguła aplikacji zapory platformy Azure|
|AzureFirewallNetworkRule|Reguła sieci zapory platformy Azure|


## <a name="microsoftnetworkbastionhosts"></a>Microsoft. Network/bastionHosts

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|BastionAuditLogs|Dzienniki inspekcji bastionu|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft. Network/expressRouteCircuits

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|PeeringRouteLog|Dzienniki tabel tras komunikacji równorzędnej|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/usługi frontdoor

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|FrontdoorAccessLog|Dziennik dostępu usługa frontdoor|
|FrontdoorWebApplicationFirewallLog|Dziennik zapory aplikacji sieci Web usługa frontdoor|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft. Network/loadBalancers

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|LoadBalancerAlertEvent|Zdarzenia alertu Load Balancer|
|LoadBalancerProbeHealthStatus|Load Balancer stan kondycji sondy|


## <a name="microsoftnetworknetworksecuritygroups"></a>Microsoft. Network/networksecuritygroups

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|NetworkSecurityGroupEvent|Zdarzenie sieciowej grupy zabezpieczeń|
|NetworkSecurityGroupFlowEvent|Zdarzenie przepływu reguły sieciowej grupy zabezpieczeń|
|NetworkSecurityGroupRuleCounter|Licznik reguły sieciowej grupy zabezpieczeń|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft. Network/adresów publicipaddress

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|DDoSMitigationFlowLogs|Dzienniki przepływów decyzji o łagodzeniu DDoS|
|DDoSMitigationReports|Raporty dotyczące środków zaradczych DDoS|
|DDoSProtectionNotifications|Powiadomienia o ochronie DDoS|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft. Network/trafficManagerProfiles

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|ProbeHealthStatusEvents|Zdarzenie Traffic Manager wyników kondycji sondy|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft. Network/virtualNetworkGateways

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|GatewayDiagnosticLog|Dzienniki diagnostyczne bramy|
|IKEDiagnosticLog|Dzienniki diagnostyczne IKE|
|P2SDiagnosticLog|P2S dzienników diagnostycznych|
|RouteDiagnosticLog|Kierowanie dzienników diagnostycznych|
|TunnelDiagnosticLog|Dzienniki diagnostyczne tunelu|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft. Network/virtualNetworks

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|VMProtectionAlerts|Alerty ochrony maszyny wirtualnej|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft. PowerBIDedicated/pojemności

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|Aparat|Aparat|


## <a name="microsoftrecoveryservicesvaults"></a>Microsoft. RecoveryServices/magazyny

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|AddonAzureBackupAlerts|Dodatek Azure Backup danych alertów|
|AddonAzureBackupJobs|Dodatek Azure Backup danych zadania|
|AddonAzureBackupPolicy|Dodatek Azure Backup danych zasad|
|AddonAzureBackupProtectedInstance|Dodatek Azure Backup danych chronionego wystąpienia|
|AddonAzureBackupStorage|Dodatek Azure Backup danych magazynu|
|AzureBackupReport|Azure Backup dane raportowania|
|AzureSiteRecoveryEvents|Zdarzenia Azure Site Recovery|
|AzureSiteRecoveryJobs|Zadania Azure Site Recovery|
|AzureSiteRecoveryProtectedDiskDataChurn|Azure Site Recovery zmienionych danych dysku chronionego|
|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery punktów odzyskiwania|
|AzureSiteRecoveryReplicatedItems|Azure Site Recovery zreplikowane elementy|
|AzureSiteRecoveryReplicationDataUploadRate|Szybkość przekazywania danych replikacji Azure Site Recovery|
|AzureSiteRecoveryReplicationStats|Statystyka replikacji Azure Site Recovery|
|CoreAzureBackup|Podstawowe Azure Backup dane|


## <a name="microsoftrelaynamespaces"></a>Microsoft. Relay/przestrzenie nazw

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|HybridConnectionsEvent|Zdarzenia HybridConnections|


## <a name="microsoftsearchsearchservices"></a>Microsoft. Search/searchServices

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|OperationLogs|Dzienniki operacji|


## <a name="microsoftservicebusnamespaces"></a>Microsoft. ServiceBus/przestrzenie nazw

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|OperationalLogs|Dzienniki operacyjne|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft. SignalRService/sygnalizujący

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|AllLogs|Dzienniki usługi Azure sygnalizujące.|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft. SQL/managedInstances

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|DevOpsOperationsAudit|Dzienniki inspekcji operacji DevOps|
|ResourceUsageStats|Statystyka użycia zasobów|
|SQLSecurityAuditEvents|Zdarzenie inspekcji zabezpieczeń SQL|


## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft. SQL/managedInstances/bazy danych

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|błędy|błędy|
|QueryStoreRuntimeStatistics|Statystyka środowiska uruchomieniowego magazynu zapytań|
|QueryStoreWaitStatistics|Statystyka oczekiwania magazynu zapytań|
|SQLInsights|Szczegółowe informacje SQL|


## <a name="microsoftsqlserversdatabases"></a>Microsoft. SQL/serwery/bazy danych

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|AutomaticTuning|Automatyczne dostrajanie|
|Bloki|Bloki|
|DatabaseWaitStatistics|Statystyka oczekiwania bazy danych|
|Zakleszczenia|Zakleszczenia|
|DevOpsOperationsAudit|Dzienniki inspekcji operacji DevOps|
|DmsWorkers|Pracownicy DMS|
|błędy|błędy|
|ExecRequests|Żądania exec|
|QueryStoreRuntimeStatistics|Statystyka środowiska uruchomieniowego magazynu zapytań|
|QueryStoreWaitStatistics|Statystyka oczekiwania magazynu zapytań|
|RequestSteps|Kroki żądania|
|SQLInsights|Szczegółowe informacje SQL|
|Sqlrequests|Żądania SQL|
|SQLSecurityAuditEvents|Zdarzenie inspekcji zabezpieczeń SQL|
|Limity czasu|Limity czasu|
|Czeka|Czeka|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft. Storage/storageAccounts/blobServices

Koszt: płatne zgodnie z opisem w sekcji Dzienniki platformy na [stronie cennika Azure monitor.](https://azure.microsoft.com/pricing/details/monitor/) 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft. Storage/storageAccounts/fileServices

Koszt: płatne zgodnie z opisem w sekcji Dzienniki platformy na [stronie cennika Azure monitor.](https://azure.microsoft.com/pricing/details/monitor/) 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft. Storage/storageAccounts/queueServices

Koszt: płatne zgodnie z opisem w sekcji Dzienniki platformy na [stronie cennika Azure monitor.](https://azure.microsoft.com/pricing/details/monitor/) 
 
|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft. Storage/storageAccounts/tableServices

Koszt: płatne zgodnie z opisem w sekcji Dzienniki platformy na [stronie cennika Azure monitor.](https://azure.microsoft.com/pricing/details/monitor/) 
 
|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft. StreamAnalytics/streamingjobs

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|Tworzenie|Tworzenie|
|Wykonanie|Wykonanie|


## <a name="microsoftsynapseworkspaces"></a>Microsoft. Synapse/obszary robocze

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|BuiltinSqlReqsEnded|Zakończono wbudowane żądania puli SQL|
|GatewayApiRequests|Żądania interfejsu API bramy Synapse|
|SQLSecurityAuditEvents|Zdarzenie inspekcji zabezpieczeń SQL|
|SynapseRbacOperations|Synapse operacje RBAC|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft. Synapse/Workspaces/bigDataPools

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|BigDataPoolAppsEnded|Zakończono aplikacje dla puli danych Big Data|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft. Synapse/obszary robocze/xmlpools

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|DmsWorkers|Pracownicy DMS|
|ExecRequests|Żądania exec|
|RequestSteps|Kroki żądania|
|Sqlrequests|Żądania SQL|
|SQLSecurityAuditEvents|Zdarzenie inspekcji zabezpieczeń SQL|
|Czeka|Czeka|


## <a name="microsoftwebhostingenvironments"></a>Microsoft. Web/hostingenvironments

Koszt: bezpłatnie 

|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|AppServiceEnvironmentPlatformLogs|Dzienniki platformy App Service Environment|


## <a name="microsoftwebsites"></a>Microsoft. Web/witryny

Koszt: bezpłatnie 


|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|AppServiceAppLogs|App Service dzienników aplikacji|
|AppServiceAuditLogs|Dzienniki inspekcji dostępu|
|AppServiceConsoleLogs|App Service dzienniki konsoli|
|AppServiceFileAuditLogs|Dzienniki inspekcji zmian zawartości witryny|
|AppServiceHTTPLogs|Dzienniki HTTP|
|FunctionAppLogs|Dzienniki aplikacji funkcji|


## <a name="microsoftwebsitesslots"></a>Microsoft. Web/Sites/miejsca

Koszt: bezpłatnie 


|Kategoria |Nazwa wyświetlana kategorii|
|---|---|
|AppServiceAppLogs|App Service dzienników aplikacji|
|AppServiceAuditLogs|Dzienniki inspekcji dostępu|
|AppServiceConsoleLogs|App Service dzienniki konsoli|
|AppServiceFileAuditLogs|Dzienniki inspekcji zmian zawartości witryny|
|AppServiceHTTPLogs|Dzienniki HTTP|
|FunctionAppLogs|Dzienniki aplikacji funkcji|


## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o dziennikach zasobów](./platform-logs-overview.md)
* [Przesyłanie strumieniowe dzienników zasobów zasobów do **Event Hubs**](./resource-logs.md#send-to-azure-event-hubs)
* [Zmienianie ustawień diagnostycznych dziennika zasobów przy użyciu interfejsu API REST Azure Monitor](/rest/api/monitor/diagnosticsettings)
* [Analizowanie dzienników z usługi Azure Storage za pomocą Log Analytics](./resource-logs.md#send-to-log-analytics-workspace)

