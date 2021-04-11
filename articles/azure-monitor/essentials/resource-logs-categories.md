---
title: Azure Monitor obsługiwane usługi i kategorie dzienników zasobów
description: Informacje dotyczące Azure Monitor zrozumieć obsługiwane usługi i schemat zdarzeń dla dzienników zasobów platformy Azure.
ms.topic: reference
ms.date: 03/30/2021
ms.openlocfilehash: a4ab4a2e425b752198223da5efd1b07466ab83d1
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166942"
---
# <a name="supported-categories-for-azure-resource-logs"></a>Kategorie obsługiwane w przypadku dzienników zasobów platformy Azure

> [!NOTE]
> Dzienniki zasobów były wcześniej znane jako dzienniki diagnostyczne. Nazwa została zmieniona w październiku 2019, ponieważ typy dzienników zebrane przez Azure Monitor przesunięte w celu uwzględnienia więcej niż tylko zasobów platformy Azure.

[Dzienniki zasobów Azure monitor](../essentials/platform-logs-overview.md) to dzienniki wysyłane przez usługi platformy Azure opisujące operacje tych usług lub zasobów. Wszystkie dzienniki zasobów dostępne za pomocą Azure Monitor współdzielą wspólny schemat najwyższego poziomu, z elastycznością dla każdej usługi do emitowania unikatowych właściwości dla własnych zdarzeń.

Kombinacja typu zasobu (dostępnego we `resourceId` Właściwości) i `category` unikatowo identyfikujący schemat. Istnieje wspólny schemat dla wszystkich dzienników zasobów mających pola specyficzne dla usługi, a następnie dodane do różnych kategorii dzienników. Aby uzyskać więcej informacji, zobacz [typowe i specyficzne dla usługi schematy dla dzienników zasobów platformy Azure]()


## <a name="costs"></a>Koszty

Istnieją koszty związane z wysyłaniem i przechowywaniem danych w programie do Log Analytics, usługi Azure Storage i/lub centrum zdarzeń. Możesz płacić za koszt, aby uzyskać dostęp do tych lokalizacji i w ten sposób przechowywać dane.  Dzienniki zasobów to jeden typ danych, które można wysłać do tych lokalizacji. 

Istnieje dodatkowe koszty eksportowania niektórych kategorii dzienników zasobów do tych lokalizacji. Te dzienniki z kosztami eksportu są wymienione w poniższej tabeli. Aby uzyskać więcej informacji na temat tych cen, zobacz sekcję dzienniki platformy na [stronie cennika Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="supported-log-categories-per-resource-type"></a>Obsługiwane kategorie dzienników według typu zasobu

Poniżej znajduje się lista typów dzienników dostępnych dla każdego typu zasobu. 

Niektóre kategorie mogą być obsługiwane tylko dla określonych typów zasobów. Zapoznaj się z dokumentacją specyficzną dla zasobów, jeśli uważasz, że brakuje zasobu. Na przykład kategorie Microsoft. SQL/serwery/bazy danych nie są dostępne dla wszystkich typów baz danych. Aby uzyskać więcej informacji, zobacz [Informacje o rejestrowaniu diagnostyki SQL Database](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md). 

Jeśli uważasz, że coś nie ma, możesz otworzyć komentarz w witrynie GitHub w dolnej części tego artykułu.

## <a name="microsoftaaddomainservices"></a>Microsoft. AAD/DomainServices

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|AccountLogon|AccountLogon|Nie|
|Zarządzania kontem|Zarządzania kontem|Nie|
|DetailTracking|DetailTracking|Nie|
|DirectoryServiceAccess|DirectoryServiceAccess|Nie|
|LogonLogoff|LogonLogoff|Nie|
|ObjectAccess|ObjectAccess|Nie|
|PolicyChange|PolicyChange|Nie|
|PrivilegeUse|PrivilegeUse|Nie|
|SystemSecurity|SystemSecurity|Nie|


## <a name="microsoftaadiamtenants"></a>Microsoft. aadiam/dzierżawcy

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|Logowania|Logowania|Tak|


## <a name="microsoftanalysisservicesservers"></a>Microsoft. AnalysisServices/serwery

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|Aparat|Aparat|Nie|
|Usługa|Usługa|Nie|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|GatewayLogs|Dzienniki powiązane z bramą ApiManagement|Nie|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft. AppConfiguration/configurationStores

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|HttpRequest|Żądania HTTP|Tak|


## <a name="microsoftappplatformspring"></a>Microsoft. AppPlatform/Sprężyna

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|ApplicationConsole|Konsola aplikacji|Nie|
|SystemLogs|Dzienniki systemu|Nie|


## <a name="microsoftattestationattestationproviders"></a>Microsoft. zaświadczanie/attestationProviders

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|AuditEvent|Kategoria dziennika komunikatów AuditEvent.|Nie|
|BŁĘD|Kategoria dziennika komunikatów o błędach.|Nie|
|INF|Kategoria dziennika komunikatów informacyjnych.|Nie|
|Ostrzeżenie|Kategoria dziennika komunikatów ostrzegawczych.|Nie|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft. Automation/automationAccounts

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|DscNodeStatus|Stan węzła DSC|Nie|
|JobLogs|Dzienniki zadań|Nie|
|JobStreams|Strumienie zadań|Nie|


## <a name="microsoftautonomousdevelopmentplatformaccounts"></a>Microsoft. AutonomousDevelopmentPlatform/konta

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|Inspekcja|Inspekcja|Tak|
|Operacyjne|Operacyjne|Tak|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|ServiceLog|Dzienniki usług|Nie|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/obszary robocze

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|BaiClusterEvent|BaiClusterEvent|Nie|
|BaiClusterNodeEvent|BaiClusterNodeEvent|Nie|
|BaiJobEvent|BaiJobEvent|Nie|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft. łańcucha bloków/blockchainMembers

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|BlockchainApplication|Aplikacja łańcucha bloków|Nie|
|FabricOrderer|Zamówienie sieci szkieletowej|Nie|
|FabricPeer|Element równorzędny sieci szkieletowej|Nie|
|Serwer proxy|Serwer proxy|Nie|


## <a name="microsoftblockchaincordamembers"></a>Microsoft. łańcucha bloków/cordaMembers

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|BlockchainApplication|Aplikacja łańcucha bloków|Nie|


## <a name="microsoftbotservicebotservices"></a>Microsoft. botservice/botservices

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|BotRequest|Żądania z kanałów do bot|Nie|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/cdnwebapplicationfirewallpolicies

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|WebApplicationFirewallLogs|Dzienniki zapory aplikacja w sieci Web|Nie|


## <a name="microsoftcdnprofiles"></a>Microsoft. CDN/profile

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|AzureCdnAccessLog|Dziennik dostępu usługi Azure CDN|Nie|
|FrontDoorAccessLog|Dziennik dostępu usługa frontdoor|Tak|
|FrontDoorHealthProbeLog|Dziennik sondy kondycji usługa frontdoor|Tak|
|FrontDoorWebApplicationFirewallLog|Dziennik usługa frontdoor WebApplicationFirewall|Tak|


## <a name="microsoftcdnprofilesendpoints"></a>Microsoft. CDN/profile/punkty końcowe

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|CoreAnalytics|Pobiera metryki punktu końcowego, np. przepustowość, ruch wychodzący itp.|Nie|


## <a name="microsoftclassicnetworknetworksecuritygroups"></a>Microsoft. ClassicNetwork/networksecuritygroups

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|Zdarzenie przepływu reguły sieciowej grupy zabezpieczeń|Zdarzenie przepływu reguły sieciowej grupy zabezpieczeń|Nie|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft. CognitiveServices/konta

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|Inspekcja|Dzienniki inspekcji|Nie|
|RequestResponse|Dzienniki żądań i odpowiedzi|Nie|
|Ślad|Dzienniki śledzenia|Nie|


## <a name="microsoftcommunicationcommunicationservices"></a>Microsoft. Communication/CommunicationServices

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|AuthOperational|Dzienniki uwierzytelniania operacyjnego|Tak|
|ChatOperational|Dzienniki rozmów operacyjnych|Nie|
|SMSOperational|Operacyjne dzienniki programu SMS|Nie|
|Użycie|Rekordy użycia|Nie|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft. ContainerRegistry/rejestry

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|ContainerRegistryLoginEvents|Zdarzenia logowania|Nie|
|ContainerRegistryRepositoryEvents|Dzienniki RepositoryEvent|Nie|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft. ContainerService/managedClusters

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|klaster — Autoskalowanie|Automatyczne skalowanie klastra Kubernetes|Nie|
|chroni|chroni|Nie|
|polecenia — apiserver|Serwer interfejsu API Kubernetes|Nie|
|polecenia — Inspekcja|Inspekcja Kubernetes|Nie|
|polecenia-Audit-administrator|Kubernetes inspekcje dzienników administratorów|Nie|
|polecenia-Controller-Manager|Menedżer kontrolera Kubernetes|Nie|
|polecenia — Scheduler|Harmonogram Kubernetes|Nie|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|AuditLogs|Dzienniki inspekcji dla wywołań MiniRP|Nie|


## <a name="microsoftd365customerinsightsinstances"></a>Microsoft. D365CustomerInsights/Instances

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|Inspekcja|Inspekcja zdarzeń|Nie|
|Operacyjne|Zdarzenia operacyjne|Nie|


## <a name="microsoftdatabricksworkspaces"></a>Microsoft. datacegły/obszary robocze

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|Konta|Konta datakostek|Nie|
|oparty|Klastry datacegły|Nie|
|dBfs|System plików usługi Databricks|Nie|
|instancePools|Pule wystąpień|Nie|
|zadania|Zadania datakostki|Nie|
|notes|Notes usługi Databricks|Nie|
|wpisy tajne|Wpisy tajne datakostek|Nie|
|Uprawnienia sqlpermissions|Datakosteks — uprawnienia sqlpermissions|Nie|
|SSH|Połączenia SSH|Nie|
|obszar roboczy|Obszar roboczy datakosteks|Nie|


## <a name="microsoftdatacollaborationworkspaces"></a>Microsoft. datacollaboration/obszary robocze

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|CollaborationAudit|Inspekcja współpracy|Tak|
|Elementy zawartości|Zasoby danych|Nie|
|Pipelines|Pipelines|Nie|
|Konkurs|Konkurs|Nie|
|Skrypty|Skrypty|Nie|


## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/fabryki

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|ActivityRuns|Dziennik uruchomienia działania potoku|Nie|
|PipelineRuns|Dziennik przebiegów potoku|Nie|
|SandboxActivityRuns|Dziennik uruchamiania działania piaskownicy|Tak|
|SandboxPipelineRuns|Dziennik uruchamiania potoków piaskownicy|Tak|
|SSISIntegrationRuntimeLogs|Dzienniki usług SSIS Integration Runtime|Nie|
|SSISPackageEventMessageContext|Kontekst komunikatu zdarzenia pakietu SSIS|Nie|
|SSISPackageEventMessages|Komunikaty zdarzeń pakietu SSIS|Nie|
|SSISPackageExecutableStatistics|Statystyka pliku wykonywalnego pakietu SSIS|Nie|
|SSISPackageExecutionComponentPhases|Fazy składnika wykonywania pakietu SSIS|Nie|
|SSISPackageExecutionDataStatistics|Statystyka danych exeution pakietu usług SSIS|Nie|
|TriggerRuns|Dziennik uruchomienia wyzwalacza|Nie|


## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft. DataLakeAnalytics/konta

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|Inspekcja|Dzienniki inspekcji|Nie|
|Żądania|Dzienniki żądań|Nie|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft. kontach datalakestore/konta

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|Inspekcja|Dzienniki inspekcji|Nie|
|Żądania|Dzienniki żądań|Nie|


## <a name="microsoftdatashareaccounts"></a>Microsoft. dataudział/konta

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|ReceivedShareSnapshots|Odebrane migawki udziału|Nie|
|SentShareSnapshots|Wysłane migawki udziałów|Nie|
|Udziały|Udziały|Nie|
|ShareSubscriptions|Udostępnianie subskrypcji|Nie|


## <a name="microsoftdbformariadbservers"></a>Microsoft. DBforMariaDB/serwery

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|MySqlAuditLogs|Dzienniki inspekcji MariaDB|Nie|
|MySqlSlowLogs|Dzienniki serwera MariaDB|Nie|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft. DBforMySQL/flexibleServers

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|MySqlAuditLogs|Dzienniki inspekcji MySQL|Nie|
|MySqlSlowLogs|Wolne dzienniki MySQL|Nie|


## <a name="microsoftdbformysqlservers"></a>Microsoft. DBforMySQL/serwery

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|MySqlAuditLogs|Dzienniki inspekcji MySQL|Nie|
|MySqlSlowLogs|Dzienniki serwera MySQL|Nie|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft. DBforPostgreSQL/flexibleServers

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|PostgreSQLLogs|Dzienniki serwera PostgreSQL|Nie|


## <a name="microsoftdbforpostgresqlservergroupsv2"></a>Microsoft. DBForPostgreSQL/serverGroupsv2

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|PostgreSQLLogs|Dzienniki serwera PostgreSQL|Tak|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft. DBforPostgreSQL/serwery

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|PostgreSQLLogs|Dzienniki serwera PostgreSQL|Nie|
|QueryStoreRuntimeStatistics|Statystyka środowiska uruchomieniowego magazynu zapytań PostgreSQL|Nie|
|QueryStoreWaitStatistics|Statystyka oczekiwania magazynu zapytań PostgreSQL|Nie|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft. DBforPostgreSQL/serversv2

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|PostgreSQLLogs|Dzienniki serwera PostgreSQL|Nie|


## <a name="microsoftdesktopvirtualizationapplicationgroups"></a>Microsoft. DesktopVirtualization/applicationgroups

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|Punkt kontrolny|Punkt kontrolny|Nie|
|Błąd|Błąd|Nie|
|Zarządzanie|Zarządzanie|Nie|


## <a name="microsoftdesktopvirtualizationhostpools"></a>Microsoft. DesktopVirtualization/hostpools

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|AgentHealthStatus|AgentHealthStatus|Nie|
|Punkt kontrolny|Punkt kontrolny|Nie|
|Połączenie|Połączenie|Nie|
|Błąd|Błąd|Nie|
|Rejestracja hosta|Rejestracja hosta|Nie|
|Zarządzanie|Zarządzanie|Nie|


## <a name="microsoftdesktopvirtualizationworkspaces"></a>Microsoft. DesktopVirtualization/obszary robocze

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|Punkt kontrolny|Punkt kontrolny|Nie|
|Błąd|Błąd|Nie|
|Źródło danych|Źródło danych|Nie|
|Zarządzanie|Zarządzanie|Nie|


## <a name="microsoftdeviceselasticpoolsiothubtenants"></a>Microsoft. Devices/ElasticPools/IotHubTenants

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|C2DCommands|Polecenia C2D|Nie|
|C2DTwinOperations|C2D — operacje na Przędzce|Nie|
|Konfiguracje|Konfiguracje|Nie|
|Połączenia|Połączenia|Nie|
|D2CTwinOperations|D2CTwinOperations|Nie|
|DeviceIdentityOperations|Operacje tożsamości urządzeń|Nie|
|DeviceStreams|Strumienie urządzeń (wersja zapoznawcza)|Nie|
|DeviceTelemetry|Dane telemetryczne urządzenia|Nie|
|DirectMethods|Metody bezpośrednie|Nie|
|DistributedTracing|Śledzenie rozproszone (wersja zapoznawcza)|Nie|
|FileUploadOperations|Operacje przekazywania plików|Nie|
|JobsOperations|Operacje zadań|Nie|
|Trasy|Trasy|Nie|
|TwinQueries|Zapytania bliźniaczye|Nie|


## <a name="microsoftdevicesiothubs"></a>Microsoft. Devices/IotHubs

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|C2DCommands|Polecenia C2D|Nie|
|C2DTwinOperations|C2D — operacje na Przędzce|Nie|
|Konfiguracje|Konfiguracje|Nie|
|Połączenia|Połączenia|Nie|
|D2CTwinOperations|D2CTwinOperations|Nie|
|DeviceIdentityOperations|Operacje tożsamości urządzeń|Nie|
|DeviceStreams|Strumienie urządzeń (wersja zapoznawcza)|Nie|
|DeviceTelemetry|Dane telemetryczne urządzenia|Nie|
|DirectMethods|Metody bezpośrednie|Nie|
|DistributedTracing|Śledzenie rozproszone (wersja zapoznawcza)|Nie|
|FileUploadOperations|Operacje przekazywania plików|Nie|
|JobsOperations|Operacje zadań|Nie|
|Trasy|Trasy|Nie|
|TwinQueries|Zapytania bliźniaczye|Nie|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft. Devices/provisioningServices

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|DeviceOperations|Operacje na urządzeniach|Nie|
|Operacje serviceoperations|Operacje usługi|Nie|


## <a name="microsoftdigitaltwinsdigitaltwinsinstances"></a>Microsoft. DigitalTwins/digitalTwinsInstances

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|DigitalTwinsOperation|DigitalTwinsOperation|Nie|
|EventRoutesOperation|EventRoutesOperation|Nie|
|ModelsOperation|ModelsOperation|Nie|
|Queryoperation obiektu DataService|Queryoperation obiektu DataService|Nie|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|CassandraRequests|CassandraRequests|Nie|
|ControlPlaneRequests|ControlPlaneRequests|Nie|
|DataPlaneRequests|DataPlaneRequests|Nie|
|GremlinRequests|GremlinRequests|Nie|
|MongoRequests|MongoRequests|Nie|
|PartitionKeyRUConsumption|PartitionKeyRUConsumption|Nie|
|PartitionKeyStatistics|PartitionKeyStatistics|Nie|
|QueryRuntimeStatistics|QueryRuntimeStatistics|Nie|


## <a name="microsofteventgriddomains"></a>Microsoft. EventGrid/domeny

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|DeliveryFailures|Dzienniki błędów dostarczania|Nie|
|PublishFailures|Publikowanie dzienników błędów|Nie|


## <a name="microsofteventgridpartnernamespaces"></a>Microsoft. EventGrid/partnerNamespaces

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|DeliveryFailures|Dzienniki błędów dostarczania|Nie|
|PublishFailures|Publikowanie dzienników błędów|Nie|


## <a name="microsofteventgridpartnertopics"></a>Microsoft. EventGrid/partnerTopics

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|DeliveryFailures|Dzienniki błędów dostarczania|Nie|


## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|DeliveryFailures|Dzienniki błędów dostarczania|Nie|


## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/tematy

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|DeliveryFailures|Dzienniki błędów dostarczania|Nie|
|PublishFailures|Publikowanie dzienników błędów|Nie|


## <a name="microsofteventhubnamespaces"></a>Microsoft. EventHub/przestrzenie nazw

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|ArchiveLogs|Archiwizuj dzienniki|Nie|
|AutoScaleLogs|Automatyczne skalowanie dzienników|Nie|
|CustomerManagedKeyUserLogs|Dzienniki kluczy zarządzanych przez klienta|Nie|
|EventHubVNetConnectionEvent|Dzienniki połączeń sieci wirtualnej/IP filtrowania|Nie|
|KafkaCoordinatorLogs|Dzienniki koordynatora Kafka|Nie|
|KafkaUserErrorLogs|Kafka dzienniki błędów użytkownika|Nie|
|OperationalLogs|Dzienniki operacyjne|Nie|


## <a name="microsoftexperimentationexperimentworkspaces"></a>Microsoft. eksperymentowanie/experimentWorkspaces

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|Żądanie|Żądanie|Nie|


## <a name="microsofthealthcareapisservices"></a>Microsoft. HealthcareApis/usługi

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|AuditLogs|Dzienniki inspekcji|Nie|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft. Insights/autoscalesettings

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|AutoscaleEvaluations|Obliczenia automatycznego skalowania|Nie|
|AutoscaleScaleActions|Akcje skalowania automatycznego skalowania|Nie|


## <a name="microsoftinsightscomponents"></a>Microsoft. Insights/składniki

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|AppAvailabilityResults|Wyniki dostępności|Nie|
|AppBrowserTimings|Chronometraż przeglądarki|Nie|
|AppDependencies|Zależności|Nie|
|AppEvents|Zdarzenia|Nie|
|AppExceptions|Wyjątki|Nie|
|AppMetrics|Metryki|Nie|
|AppPageViews|Wyświetlenia stron|Nie|
|AppPerformanceCounters|Liczniki wydajności|Nie|
|AppRequests|Żądania|Nie|
|AppSystemEvents|Zdarzenia systemowe|Nie|
|AppTraces|Ślady|Nie|


## <a name="microsoftkeyvaultmanagedhsms"></a>Microsoft./Magazyn kluczy/managedhsms

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|AuditEvent|Zdarzenie inspekcji|Nie|


## <a name="microsoftkeyvaultvaults"></a>Microsoft./magazyny kluczy

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|AuditEvent|Dzienniki inspekcji|Nie|


## <a name="microsoftkustoclusters"></a>Microsoft. Kusto/klastry

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|Polecenie|Polecenie|Nie|
|FailedIngestion|Nieudane operacje pozyskiwania|Nie|
|IngestionBatching|Dzielenie na partie pozyskiwania|Nie|
|Zapytanie|Zapytanie|Nie|
|SucceededIngestion|Pomyślne operacje pozyskiwania|Nie|
|TableDetails|Szczegóły tabeli|Nie|
|TableUsageStatistics|Statystyka użycia tabeli|Nie|


## <a name="microsoftlogicintegrationaccounts"></a>Microsoft. Logic/integrationAccounts

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|IntegrationAccountTrackingEvents|Zdarzenia śledzenia konta integracji|Nie|


## <a name="microsoftlogicworkflows"></a>Microsoft. Logic/przepływy pracy

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|Element|Zdarzenia diagnostyczne środowiska uruchomieniowego przepływu pracy|Nie|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft. MachineLearningServices/obszary robocze

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|AmlComputeClusterEvent|AmlComputeClusterEvent|Nie|
|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|Nie|
|AmlComputeCpuGpuUtilization|AmlComputeCpuGpuUtilization|Nie|
|AmlComputeJobEvent|AmlComputeJobEvent|Nie|
|AmlRunStatusChangedEvent|AmlRunStatusChangedEvent|Nie|


## <a name="microsoftmediamediaservices"></a>Microsoft. Media/MediaServices

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|KeyDeliveryRequests|Żądania dostarczania kluczy|Nie|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft. Network/applicationGateways

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|ApplicationGatewayAccessLog|Application Gateway dziennika dostępu|Nie|
|ApplicationGatewayFirewallLog|Application Gateway dziennika zapory|Nie|
|ApplicationGatewayPerformanceLog|Application Gateway dziennik wydajności|Nie|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft. Network/azurefirewalls

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|AzureFirewallApplicationRule|Reguła aplikacji zapory platformy Azure|Nie|
|AzureFirewallDnsProxy|Serwer proxy usługi DNS zapory platformy Azure|Nie|
|AzureFirewallNetworkRule|Reguła sieci zapory platformy Azure|Nie|


## <a name="microsoftnetworkbastionhosts"></a>Microsoft. Network/bastionHosts

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|BastionAuditLogs|Dzienniki inspekcji bastionu|Nie|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft. Network/expressRouteCircuits

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|PeeringRouteLog|Dzienniki tabel tras komunikacji równorzędnej|Nie|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/usługi frontdoor

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|FrontdoorAccessLog|Dziennik dostępu usługa frontdoor|Nie|
|FrontdoorWebApplicationFirewallLog|Dziennik zapory aplikacji sieci Web usługa frontdoor|Nie|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft. Network/loadBalancers

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|LoadBalancerAlertEvent|Zdarzenia alertu Load Balancer|Nie|
|LoadBalancerProbeHealthStatus|Load Balancer stan kondycji sondy|Nie|


## <a name="microsoftnetworknetworksecuritygroups"></a>Microsoft. Network/networksecuritygroups

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|NetworkSecurityGroupEvent|Zdarzenie sieciowej grupy zabezpieczeń|Nie|
|NetworkSecurityGroupFlowEvent|Zdarzenie przepływu reguły sieciowej grupy zabezpieczeń|Nie|
|NetworkSecurityGroupRuleCounter|Licznik reguły sieciowej grupy zabezpieczeń|Nie|


## <a name="microsoftnetworkp2svpngateways"></a>Microsoft. Network/p2sVpnGateways

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|GatewayDiagnosticLog|Dzienniki diagnostyczne bramy|Nie|
|IKEDiagnosticLog|Dzienniki diagnostyczne IKE|Nie|
|P2SDiagnosticLog|P2S dzienników diagnostycznych|Nie|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft. Network/adresów publicipaddress

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|DDoSMitigationFlowLogs|Dzienniki przepływów decyzji o łagodzeniu DDoS|Nie|
|DDoSMitigationReports|Raporty dotyczące środków zaradczych DDoS|Nie|
|DDoSProtectionNotifications|Powiadomienia o ochronie DDoS|Nie|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft. Network/trafficManagerProfiles

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|ProbeHealthStatusEvents|Zdarzenie Traffic Manager wyników kondycji sondy|Nie|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft. Network/virtualNetworkGateways

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|GatewayDiagnosticLog|Dzienniki diagnostyczne bramy|Nie|
|IKEDiagnosticLog|Dzienniki diagnostyczne IKE|Nie|
|P2SDiagnosticLog|P2S dzienników diagnostycznych|Nie|
|RouteDiagnosticLog|Kierowanie dzienników diagnostycznych|Nie|
|TunnelDiagnosticLog|Dzienniki diagnostyczne tunelu|Nie|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft. Network/virtualNetworks

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|VMProtectionAlerts|Alerty ochrony maszyny wirtualnej|Nie|


## <a name="microsoftnetworkvpngateways"></a>Microsoft. Network/vpnGateways

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|GatewayDiagnosticLog|Dzienniki diagnostyczne bramy|Nie|
|IKEDiagnosticLog|Dzienniki diagnostyczne IKE|Nie|
|RouteDiagnosticLog|Kierowanie dzienników diagnostycznych|Nie|
|TunnelDiagnosticLog|Dzienniki diagnostyczne tunelu|Nie|


## <a name="microsoftnotificationhubsnamespaces"></a>Microsoft. NotificationHubs/przestrzenie nazw

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|OperationalLogs|Dzienniki operacyjne|Nie|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft. OperationalInsights/obszary robocze

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|Inspekcja|Dzienniki inspekcji|Nie|


## <a name="microsoftpowerbitenants"></a>Microsoft. PowerBI/dzierżawcy

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|Aparat|Aparat|Nie|


## <a name="microsoftpowerbitenantsworkspaces"></a>Microsoft. PowerBI/dzierżawcy/obszary robocze

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|Aparat|Aparat|Nie|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft. PowerBIDedicated/pojemności

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|Aparat|Aparat|Nie|


## <a name="microsoftpurviewaccounts"></a>Microsoft. kontrolą/konta

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|ScanStatusLogEvent|ScanStatus|Nie|


## <a name="microsoftrecoveryservicesvaults"></a>Microsoft. RecoveryServices/magazyny

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|AddonAzureBackupAlerts|Dodatek Azure Backup danych alertów|Nie|
|AddonAzureBackupJobs|Dodatek Azure Backup danych zadania|Nie|
|AddonAzureBackupPolicy|Dodatek Azure Backup danych zasad|Nie|
|AddonAzureBackupProtectedInstance|Dodatek Azure Backup danych chronionego wystąpienia|Nie|
|AddonAzureBackupStorage|Dodatek Azure Backup danych magazynu|Nie|
|AzureBackupReport|Azure Backup dane raportowania|Nie|
|AzureSiteRecoveryEvents|Zdarzenia Azure Site Recovery|Nie|
|AzureSiteRecoveryJobs|Zadania Azure Site Recovery|Nie|
|AzureSiteRecoveryProtectedDiskDataChurn|Azure Site Recovery zmienionych danych dysku chronionego|Nie|
|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery punktów odzyskiwania|Nie|
|AzureSiteRecoveryReplicatedItems|Azure Site Recovery zreplikowane elementy|Nie|
|AzureSiteRecoveryReplicationDataUploadRate|Szybkość przekazywania danych replikacji Azure Site Recovery|Nie|
|AzureSiteRecoveryReplicationStats|Statystyka replikacji Azure Site Recovery|Nie|
|CoreAzureBackup|Podstawowe Azure Backup dane|Nie|


## <a name="microsoftrelaynamespaces"></a>Microsoft. Relay/przestrzenie nazw

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|HybridConnectionsEvent|Zdarzenia HybridConnections|Nie|
|HybridConnectionsLogs|HybridConnectionsLogs|Nie|


## <a name="microsoftsearchsearchservices"></a>Microsoft. Search/searchServices

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|OperationLogs|Dzienniki operacji|Nie|


## <a name="microsoftservicebusnamespaces"></a>Microsoft. ServiceBus/przestrzenie nazw

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|OperationalLogs|Dzienniki operacyjne|Nie|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft. SignalRService/sygnalizujący

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|AllLogs|Dzienniki usługi Azure sygnalizujące.|Nie|


## <a name="microsoftsignalrservicewebpubsub"></a>Microsoft. SignalRService/WebPubSub

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|AllLogs|Dzienniki usługi Azure Web PubSub.|Tak|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft. SQL/managedInstances

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|DevOpsOperationsAudit|Dzienniki inspekcji operacji DevOps|Nie|
|ResourceUsageStats|Statystyka użycia zasobów|Nie|
|SQLSecurityAuditEvents|Zdarzenie inspekcji zabezpieczeń SQL|Nie|


## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft. SQL/managedInstances/bazy danych

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|błędy|błędy|Nie|
|QueryStoreRuntimeStatistics|Statystyka środowiska uruchomieniowego magazynu zapytań|Nie|
|QueryStoreWaitStatistics|Statystyka oczekiwania magazynu zapytań|Nie|
|SQLInsights|Szczegółowe informacje SQL|Nie|


## <a name="microsoftsqlserversdatabases"></a>Microsoft. SQL/serwery/bazy danych

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|AutomaticTuning|Automatyczne dostrajanie|Nie|
|Bloki|Bloki|Nie|
|DatabaseWaitStatistics|Statystyka oczekiwania bazy danych|Nie|
|Zakleszczenia|Zakleszczenia|Nie|
|DevOpsOperationsAudit|Dzienniki inspekcji operacji DevOps|Nie|
|DmsWorkers|Pracownicy DMS|Nie|
|błędy|błędy|Nie|
|ExecRequests|Żądania exec|Nie|
|QueryStoreRuntimeStatistics|Statystyka środowiska uruchomieniowego magazynu zapytań|Nie|
|QueryStoreWaitStatistics|Statystyka oczekiwania magazynu zapytań|Nie|
|RequestSteps|Kroki żądania|Nie|
|SQLInsights|Szczegółowe informacje SQL|Nie|
|Sqlrequests|Żądania SQL|Nie|
|SQLSecurityAuditEvents|Zdarzenie inspekcji zabezpieczeń SQL|Nie|
|Limity czasu|Limity czasu|Nie|
|Czeka|Czeka|Nie|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft. Storage/storageAccounts/blobServices

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|StorageDelete|StorageDelete|Tak|
|StorageRead|StorageRead|Tak|
|StorageWrite|StorageWrite|Tak|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft. Storage/storageAccounts/fileServices

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|StorageDelete|StorageDelete|Tak|
|StorageRead|StorageRead|Tak|
|StorageWrite|StorageWrite|Tak|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft. Storage/storageAccounts/queueServices

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|StorageDelete|StorageDelete|Tak|
|StorageRead|StorageRead|Tak|
|StorageWrite|StorageWrite|Tak|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft. Storage/storageAccounts/tableServices

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|StorageDelete|StorageDelete|Tak|
|StorageRead|StorageRead|Tak|
|StorageWrite|StorageWrite|Tak|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft. StreamAnalytics/streamingjobs

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|Tworzenie|Tworzenie|Nie|
|Wykonanie|Wykonanie|Nie|


## <a name="microsoftsynapseworkspaces"></a>Microsoft. Synapse/obszary robocze

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|BuiltinSqlReqsEnded|Zakończono wbudowane żądania puli SQL|Nie|
|GatewayApiRequests|Żądania interfejsu API bramy Synapse|Nie|
|IntegrationActivityRuns|Uruchomienia działania integracji|Tak|
|IntegrationPipelineRuns|Uruchomienia potoku integracji|Tak|
|IntegrationTriggerRuns|Uruchomienia wyzwalacza integracji|Tak|
|SQLSecurityAuditEvents|Zdarzenie inspekcji zabezpieczeń SQL|Nie|
|SynapseRbacOperations|Synapse operacje RBAC|Nie|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft. Synapse/Workspaces/bigDataPools

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|BigDataPoolAppsEnded|Zakończono aplikacje dla puli danych Big Data|Nie|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft. Synapse/obszary robocze/xmlpools

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|DmsWorkers|Pracownicy DMS|Nie|
|ExecRequests|Żądania exec|Nie|
|RequestSteps|Kroki żądania|Nie|
|Sqlrequests|Żądania SQL|Nie|
|SQLSecurityAuditEvents|Zdarzenie inspekcji zabezpieczeń SQL|Nie|
|Czeka|Czeka|Nie|


## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft. TimeSeriesInsights/środowiska

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|Ruch przychodzący|Ruch przychodzący|Nie|
|Zarządzanie|Zarządzanie|Nie|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft. TimeSeriesInsights/Environments/eventsources

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|Ruch przychodzący|Ruch przychodzący|Nie|
|Zarządzanie|Zarządzanie|Nie|


## <a name="microsoftwebhostingenvironments"></a>Microsoft. Web/hostingenvironments

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|AppServiceEnvironmentPlatformLogs|Dzienniki platformy App Service Environment|Nie|


## <a name="microsoftwebsites"></a>Microsoft. Web/witryny

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|AppServiceAntivirusScanAuditLogs|Zgłoś dzienniki inspekcji programu antywirusowego|Nie|
|AppServiceAppLogs|App Service dzienników aplikacji|Nie|
|AppServiceAuditLogs|Dzienniki inspekcji dostępu|Nie|
|AppServiceConsoleLogs|App Service dzienniki konsoli|Nie|
|AppServiceFileAuditLogs|Dzienniki inspekcji zmian zawartości witryny|Nie|
|AppServiceHTTPLogs|Dzienniki HTTP|Nie|
|AppServiceIPSecAuditLogs|IPSecurity dzienników inspekcji|Nie|
|AppServicePlatformLogs|Dzienniki platformy App Service|Nie|
|FunctionAppLogs|Dzienniki aplikacji funkcji|Nie|


## <a name="microsoftwebsitesslots"></a>Microsoft. Web/Sites/miejsca

|Kategoria|Nazwa wyświetlana kategorii|Koszty eksportowania|
|---|---|---|
|AppServiceAntivirusScanAuditLogs|Zgłoś dzienniki inspekcji programu antywirusowego|Nie|
|AppServiceAppLogs|App Service dzienników aplikacji|Nie|
|AppServiceAuditLogs|Dzienniki inspekcji dostępu|Nie|
|AppServiceConsoleLogs|App Service dzienniki konsoli|Nie|
|AppServiceFileAuditLogs|Dzienniki inspekcji zmian zawartości witryny|Nie|
|AppServiceHTTPLogs|Dzienniki HTTP|Nie|
|AppServiceIPSecAuditLogs|IPSecurity dzienników inspekcji|Nie|
|AppServicePlatformLogs|Dzienniki platformy App Service|Nie|
|FunctionAppLogs|Dzienniki aplikacji funkcji|Nie|


## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o dziennikach zasobów](../essentials/platform-logs-overview.md)
* [Przesyłanie strumieniowe dzienników zasobów zasobów do **Event Hubs**](./resource-logs.md#send-to-azure-event-hubs)
* [Zmienianie ustawień diagnostycznych dziennika zasobów przy użyciu interfejsu API REST Azure Monitor](/rest/api/monitor/diagnosticsettings)
* [Analizowanie dzienników z usługi Azure Storage za pomocą Log Analytics](./resource-logs.md#send-to-log-analytics-workspace)

