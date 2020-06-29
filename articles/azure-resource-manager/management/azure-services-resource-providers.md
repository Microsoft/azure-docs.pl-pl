---
title: Dostawcy zasobów według usług platformy Azure
description: Wyświetla listę wszystkich przestrzeni nazw dostawcy zasobów dla Azure Resource Manager i pokazuje usługę platformy Azure dla tej przestrzeni nazw.
ms.topic: conceptual
ms.date: 06/05/2020
ms.openlocfilehash: d4dea1dc5ee46d9be7bbc92166535aa93db46275
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2020
ms.locfileid: "85506807"
---
# <a name="resource-providers-for-azure-services"></a>Dostawcy zasobów dla usług platformy Azure

W tym artykule pokazano, jak przestrzenie nazw dostawcy zasobów są mapowane na usługi platformy Azure.

## <a name="match-resource-provider-to-service"></a>Dopasuj dostawcę zasobów do usługi

| Przestrzeń nazw dostawcy zasobów | Usługa platformy Azure |
| --------------------------- | ------------- |
| Microsoft. AAD | [Azure Active Directory Domain Services](../../active-directory-domain-services/index.yml) |
| Microsoft. Dodatki | Procesor |
| Microsoft. ADHybridHealthService<sup>1</sup> | [Usługa Azure Active Directory](/azure/active-directory/) |
| Microsoft. Advisor | [Azure Advisor](../../advisor/index.yml) |
| Microsoft. AlertsManagement | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft. AnalysisServices | [Azure Analysis Services](/azure/analysis-services/) |
| Microsoft. ApiManagement | [API Management](../../api-management/index.yml) |
| Microsoft. AppConfiguration | [Azure App Configuration](../../azure-app-configuration/index.yml) |
| Microsoft. AppPlatform | [Azure Spring Cloud](../../spring-cloud/spring-cloud-overview.md) |
| Microsoft. zaświadczanie | Usługa zaświadczania platformy Azure |
| Microsoft. Authorization<sup>1</sup> | [Azure Resource Manager](../index.yml) |
| Microsoft. Automation | [Automatyzacja](../../automation/index.yml) |
| Microsoft. AutonomousSystems | [Systemy autonomiczne](https://www.microsoft.com/ai/autonomous-systems) |
| Microsoft. Automatyczna synchronizacja | [Rozwiązanie Azure VMware](../../azure-vmware/index.yml) |
| Microsoft. usługi azureactivedirectory | [Azure Active Directory B2C](../../active-directory-b2c/index.yml) |
| Microsoft. AzureData | Rejestr SQL Server |
| Microsoft. AzureStack | Procesor |
| Microsoft. AzureStackHCI | [Azure Stack HCI](/azure-stack/hci/overview) |
| Microsoft.Batch | [Batch](../../batch/index.yml) |
| Microsoft. rozliczenia<sup>1</sup> | [Zarządzanie kosztami i rozliczenia](/azure/billing/) |
| Microsoft. BingMaps | [Mapy Bing](https://docs.microsoft.com/BingMaps/#pivot=main&panel=BingMapsAPI) |
| Microsoft. łańcucha bloków | [Usługa Azure Blockchain](/azure/blockchain/workbench/) |
| Microsoft. BlockchainTokens | [Tokeny usługi Azure Blockchain](https://azure.microsoft.com/services/blockchain-tokens/) |
| Microsoft. plan | [Azure Blueprints](/azure/governance/blueprints/) |
| Microsoft. BotService | [Usługa Azure Bot](/azure/bot-service/) |
| Microsoft. cache | [Azure Cache for Redis](/azure/azure-cache-for-redis/) |
| Microsoft. Pojemność | Procesor |
| Microsoft. CDN | [Content Delivery Network](../../cdn/index.yml) |
| Microsoft. CertificateRegistration | [App Service certyfikaty](../../app-service/configure-ssl-certificate.md#import-an-app-service-certificate) |
| Microsoft. ChangeAnalysis | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.ClassicCompute | Maszyna wirtualna klasycznego modelu wdrażania |
| Microsoft. ClassicInfrastructureMigrate | Migracja klasycznego modelu wdrażania |
| Microsoft. ClassicNetwork | Sieć wirtualna klasycznego modelu wdrażania |
| Microsoft. ClassicStorage | Magazyn klasycznych modeli wdrażania |
| Microsoft. ClassicSubscription<sup>1</sup> | Klasyczny model wdrażania |
| Microsoft. CognitiveServices | [Cognitive Services](/azure/cognitive-services/) |
| Microsoft. Commerce<sup>1</sup> | Procesor |
| Microsoft.Compute | [Maszyny wirtualne](/azure/virtual-machines/)<br />[Zestawy skali maszyn wirtualnych](/azure/virtual-machine-scale-sets/) |
| Microsoft. zużycie<sup>1</sup> | [Cost Management](/azure/cost-management/) |
| Microsoft. ContainerInstance | [Container Instances](/azure/container-instances/) |
| Microsoft. ContainerRegistry | [Container Registry](/azure/container-registry/) |
| Microsoft. ContainerService | [Azure Kubernetes Service (AKS)](/azure/aks/) |
| Microsoft. CostManagement<sup>1</sup> | [Cost Management](/azure/cost-management/) |
| Microsoft. CostManagementExports | [Cost Management](/azure/cost-management/) |
| Microsoft. CustomerLockbox | [Skrytka klienta Microsoft Azure](../../security/fundamentals/customer-lockbox-overview.md) |
| Microsoft. CustomProviders | [Azure Custom Providers](../custom-providers/overview.md) |
| Microsoft. DataBox | [Azure Data Box](/azure/databox/) |
| Microsoft. DataBoxEdge | [Azure Stack Edge](../../databox-online/azure-stack-edge-overview.md) |
| Microsoft. datacegły | [Azure Databricks](/azure/azure-databricks/) |
| Microsoft. datacatalog | [Data Catalog](/azure/data-catalog/) |
| Microsoft. DataFactory | [Fabryka danych](/azure/data-factory/) |
| Microsoft. DataLakeAnalytics | [Data Lake Analytics](/azure/data-lake-analytics/) |
| Microsoft. kontach datalakestore | [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md) |
| Migracja Microsoft. | [Azure Database Migration Service](/azure/dms/) |
| Microsoft. dataprotection | Ochrona danych |
| Microsoft. dataudział | [Azure Data Share](/azure/data-share/) |
| Microsoft. DBforMariaDB | [Azure Database for MariaDB](/azure/mariadb/) |
| Microsoft. DBforMySQL | [Azure Database for MySQL](/azure/mysql/) |
| Microsoft. DBforPostgreSQL | [Azure Database for PostgreSQL](/azure/postgresql/) |
| Microsoft. Deploymentmanager | [Menedżer wdrażania platformy Azure](../templates/deployment-manager-overview.md) |
| Microsoft. DesktopVirtualization | [Windows Virtual Desktop](/azure/virtual-desktop/) |
| Microsoft. urządzenia | [Azure IoT Hub](/azure/iot-hub/)<br />[Usługa Azure IoT Hub Device Provisioning](/azure/iot-dps/) |
| Microsoft. DevOps | [Azure DevOps](/azure/devops/) |
| Microsoft. DevSpaces | [Azure Dev Spaces](/azure/dev-spaces/) |
| Microsoft. wspólny | [Azure Lab Services](../../lab-services/index.yml) |
| Microsoft. DigitalTwins | [Azure Digital Twins](../../digital-twins/about-digital-twins.md) |
| Microsoft.DocumentDB | [Azure Cosmos DB](../../cosmos-db/index.yml) |
| Microsoft. DomainRegistration | [App Service](/azure/app-service/) |
| Microsoft. EnterpriseKnowledgeGraph | Wykres merytoryczny dla przedsiębiorstw |
| Microsoft. EventGrid | [Event Grid](/azure/event-grid/) |
| Microsoft. EventHub | [Event Hubs](../../event-hubs/index.yml) |
| Microsoft. funkcje<sup>1</sup> | [Azure Resource Manager](../index.yml) |
| Microsoft. GuestConfiguration | [Azure Policy](../../governance/policy/index.yml) |
| Microsoft. HanaOnAzure | [Oprogramowanie SAP HANA na platformie Azure — duże wystąpienia](../../virtual-machines/workloads/sap/hana-overview-architecture.md) |
| Microsoft. HardwareSecurityModules | [Dedykowany moduł HSM platformy Azure](../../dedicated-hsm/index.yml) |
| Microsoft. HDInsight | [HDInsight](../../hdinsight/index.yml) |
| Microsoft. HealthcareApis | [Interfejs API platformy Azure dla standardu FHIR](../../healthcare-apis/index.yml) |
| Microsoft. HybridCompute | [Azure Arc](../../azure-arc/index.yml) |
| Microsoft. HybridData | [Magazyn StorSimple](/azure/storsimple/) |
| Microsoft. ImportExport | [Usługa Azure Import/Export](../../storage/common/storage-import-export-service.md) |
| Microsoft. Insights | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft. IoTCentral | [Azure IoT Central](/azure/iot-central/) |
| Microsoft. IoTSpaces | [Azure Digital Twins](../../digital-twins/index.yml) |
| Microsoft. kluczy — magazyn | [Usługa Key Vault](../../key-vault/index.yml) |
| Microsoft. Kubernetes | [Azure Kubernetes Service (AKS)](/azure/aks/) |
| Microsoft. KubernetesConfiguration | [Azure Kubernetes Service (AKS)](/azure/aks/) |
| Microsoft.Kusto | [Azure Data Explorer](/azure/data-explorer/) |
| Microsoft. LabServices | [Azure Lab Services](../../lab-services/index.yml) |
| Microsoft. Logic | [Logic Apps](../../logic-apps/index.yml) |
| Microsoft. MachineLearning | [Machine Learning Studio](../../machine-learning/studio/index.yml) |
| Microsoft.MachineLearningServices | [Azure Machine Learning](../../machine-learning/index.yml) |
| Microsoft. Maintenance | [Konserwacja platformy Azure](../../virtual-machines/maintenance-control-cli.md) |
| Microsoft. ManagedIdentity | [Tożsamości zarządzane dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/index.yml) |
| Microsoft. ManagedServices | [Azure Lighthouse](/azure/lighthouse/) |
| Microsoft. Management | [Grupy zarządzania](/azure/governance/management-groups/) |
| Microsoft. Maps | [Azure Maps](../../azure-maps/index.yml) |
| Microsoft. Marketplace | Procesor |
| Microsoft. MarketplaceApps | Procesor |
| Microsoft. MarketplaceOrdering<sup>1</sup> | Procesor |
| Microsoft. Media | [Media Services](../../media-services/index.yml) |
| Microsoft. Migrowanie | [Azure Migrate](../../migrate/migrate-overview.md) |
| Microsoft. MixedReality | [Azure Spatial Anchors](/azure/spatial-anchors/) |
| Microsoft. NetApp | [Azure NetApp Files](../../azure-netapp-files/index.yml) |
| Microsoft.Network | [Application Gateway](../../application-gateway/index.yml)<br />[Azure Bastion](/azure/bastion/)<br />[Azure DDoS Protection](../../virtual-network/ddos-protection-overview.md)<br />[System DNS platformy Azure](../../dns/index.yml)<br />[Azure ExpressRoute](../../expressroute/index.yml)<br />[Azure Firewall](../../firewall/index.yml)<br />[Azure Front Door Service](../../frontdoor/index.yml)<br />[Link prywatny platformy Azure](../../private-link/index.yml)<br />[Moduł równoważenia obciążenia](../../load-balancer/index.yml)<br />[Network Watcher](../../network-watcher/index.yml)<br />[Traffic Manager](../../traffic-manager/index.yml)<br />[Virtual Network](../../virtual-network/index.yml)<br />[Wirtualna sieć WAN](../../virtual-wan/index.yml)<br />[VPN Gateway](../../vpn-gateway/index.yml)<br /> |
| Microsoft. NotificationHubs | [Notification Hubs](../../notification-hubs/index.yml) |
| Microsoft. ObjectStore | Magazyn obiektów |
| Microsoft. OffAzure | [Azure Migrate](../../migrate/migrate-overview.md) |
| Microsoft. OperationalInsights | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft. OperationsManagement | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft. Komunikacja równorzędna | [Usługa Azure Peering Service](../../peering-service/index.yml) |
| Microsoft.PolicyInsights | [Azure Policy](../../governance/policy/index.yml) |
| Microsoft. Portal<sup>1</sup> | [Azure Portal](/azure/azure-portal/) |
| Microsoft. PowerBI | [Power BI](/power-bi/power-bi-overview) |
| Microsoft. PowerBIDedicated | [Power BI Embedded](/azure/power-bi-embedded/) |
| Microsoft. PowerPlatform | [Power Platform](/power-platform/) |
| Microsoft. Quantum | [Quantum platformy Azure](https://azure.microsoft.com/services/quantum/) |
| Microsoft. RecoveryServices | [Azure Site Recovery](../../site-recovery/index.yml) |
| Microsoft. RedHatOpenShift | [Azure Red Hat OpenShift](../../virtual-machines/linux/openshift-get-started.md) |
| Microsoft. Relay | [Azure Relay](../../service-bus-relay/relay-what-is-it.md) |
| Microsoft. ResourceGraph<sup>1</sup> | [Azure Resource Graph](/azure/governance/resource-graph/) |
| Microsoft. ResourceHealth | [Azure Service Health](../../service-health/index.yml) |
| Microsoft. resources<sup>1</sup> | [Azure Resource Manager](../index.yml) |
| Microsoft. SaaS | Procesor |
| Microsoft. Scheduler | [Scheduler](/azure/scheduler/) |
| Microsoft. Search | [Azure Cognitive Search](../../search/index.yml) |
| Microsoft.Security | [Security Center](../../security-center/index.yml) |
| Microsoft. SecurityInsights | [Azure Sentinel](/azure/sentinel/) |
| Microsoft. SerialConsole<sup>1</sup> | [Konsola szeregowa platformy Azure dla systemu Windows](../../virtual-machines/troubleshooting/serial-console-windows.md) |
| Microsoft.ServiceBus | [Service Bus](/azure/service-bus/) |
| Microsoft. servicefabric | [Service Fabric](../../service-fabric/index.yml) |
| Microsoft. ServiceFabricMesh | [Service Fabric Mesh](../../service-fabric-mesh/index.yml) |
| Microsoft. Services | Procesor |
| Microsoft. SignalRService | [Usługa Azure SignalR Service](../../azure-signalr/index.yml) |
| Microsoft. SoftwarePlan | Licencja |
| Microsoft. Solutions | [Azure Managed Applications](../managed-applications/index.yml) |
| Microsoft.Sql | [Azure SQL Database](../../azure-sql/database/index.yml)<br /> [Wystąpienie zarządzane Azure SQL](../../azure-sql/managed-instance/index.yml) <br />[Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Microsoft. SqlVirtualMachine | [Program SQL Server na maszynach wirtualnych platformy Azure](../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) |
| Microsoft.Storage | [Storage](../../storage/index.yml) |
| Microsoft. StorageSync | [Storage](../../storage/index.yml) |
| Microsoft. StorSimple | [Magazyn StorSimple](/azure/storsimple/) |
| Microsoft. StreamAnalytics | [Azure Stream Analytics](../../stream-analytics/index.yml) |
| Microsoft. Subscription | Procesor |
| Microsoft. Support<sup>1</sup> | Procesor |
| Microsoft. Synapse | [Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Microsoft. TimeSeriesInsights | [Azure Time Series Insights](../../time-series-insights/index.yml) |
| Microsoft. token | Token |
| Microsoft. VirtualMachineImages | [Konstruktor obrazów platformy Azure](../../virtual-machines/linux/image-builder-overview.md) |
| Microsoft. VisualStudio | [Azure DevOps](/azure/devops/?view=azure-devops) |
| Microsoft. VMware | [Rozwiązanie Azure VMware](../../azure-vmware/index.yml) |
| Microsoft. VMwareCloudSimple | [Azure VMware Solution by CloudSimple](/azure/vmware-cloudsimple/) |
| Microsoft. VSOnline | [Azure DevOps](/azure/devops/?view=azure-devops) |
| Microsoft. Web | [App Service](../../app-service/index.yml)<br />[Azure Functions](../../azure-functions/index.yml) |
| Microsoft. WindowsESU | Rozszerzone aktualizacje zabezpieczeń |
| Microsoft. WindowsIoT | [Windows 10 IoT Core Services](https://docs.microsoft.com/windows-hardware/manufacture/iot/iotcoreservicesoverview) |
| Microsoft. Monitor obciążenia został<sup>1</sup> | [Azure Monitor](../../azure-monitor/index.yml) |

<sup>1</sup> zarejestrowano domyślnie

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o dostawcach zasobów, w tym o sposobie rejestrowania dostawcy zasobów, zobacz [dostawcy zasobów platformy Azure i typy](resource-providers-and-types.md)
