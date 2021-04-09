---
title: Dostawcy zasobów według usług platformy Azure
description: Wyświetla listę wszystkich przestrzeni nazw dostawcy zasobów dla Azure Resource Manager i pokazuje usługę platformy Azure dla tej przestrzeni nazw.
ms.topic: conceptual
ms.date: 03/16/2021
ms.openlocfilehash: d8239a9305560eb77ac2c714b993373f0d5b16fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023014"
---
# <a name="resource-providers-for-azure-services"></a>Dostawcy zasobów dla usług platformy Azure

W tym artykule pokazano, jak przestrzenie nazw dostawcy zasobów są mapowane na usługi platformy Azure. Jeśli nie znasz dostawcy zasobów, zobacz [Znajdowanie dostawcy zasobów](#find-resource-provider).

## <a name="match-resource-provider-to-service"></a>Dopasuj dostawcę zasobów do usługi

Dostawcy zasobów, którzy są oznaczeni jako **zarejestrowany** , są domyślnie rejestrowani dla Twojej subskrypcji. Aby uzyskać więcej informacji, zobacz [rejestracja](#registration).

| Przestrzeń nazw dostawcy zasobów | Usługa platformy Azure |
| --------------------------- | ------------- |
| Microsoft. AAD | [Azure Active Directory Domain Services](../../active-directory-domain-services/index.yml) |
| Microsoft. Dodatki | Procesor |
| Microsoft. ADHybridHealthService — [zarejestrowany](#registration) | [Azure Active Directory](../../active-directory/index.yml) |
| Microsoft. Advisor | [Azure Advisor](../../advisor/index.yml) |
| Microsoft. AlertsManagement | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft. AnalysisServices | [Azure Analysis Services](../../analysis-services/index.yml) |
| Microsoft. ApiManagement | [API Management](../../api-management/index.yml) |
| Microsoft. AppConfiguration | [Azure App Configuration](../../azure-app-configuration/index.yml) |
| Microsoft. AppPlatform | [Azure Spring Cloud](../../spring-cloud/overview.md) |
| Microsoft. zaświadczanie | Usługa zaświadczania platformy Azure |
| Microsoft. Authorization — [zarejestrowany](#registration) | [Azure Resource Manager](../index.yml) |
| Microsoft. Automation | [Automatyzacja](../../automation/index.yml) |
| Microsoft. AutonomousSystems | [Systemy autonomiczne](https://www.microsoft.com/ai/autonomous-systems) |
| Microsoft. Automatyczna synchronizacja | [Rozwiązanie Azure VMware](../../azure-vmware/index.yml) |
| Microsoft. usługi azureactivedirectory | [Azure Active Directory B2C](../../active-directory-b2c/index.yml) |
| Microsoft. AzureArcData | Usługi danych z obsługą usługi Azure Arc |
| Microsoft. AzureData | Rejestr SQL Server |
| Microsoft. AzureStack | Procesor |
| Microsoft. AzureStackHCI | [Azure Stack HCI](/azure-stack/hci/overview) |
| Microsoft.Batch | [Batch](../../batch/index.yml) |
| Microsoft. rozliczenia — [zarejestrowane](#registration) | [Zarządzanie kosztami i rozliczenia](/azure/billing/) |
| Microsoft. BingMaps | [Mapy Bing](/BingMaps/#pivot=main&panel=BingMapsAPI) |
| Microsoft. łańcucha bloków | [Usługa Azure Blockchain](../../blockchain/workbench/index.yml) |
| Microsoft. BlockchainTokens | [Tokeny usługi Azure Blockchain](https://azure.microsoft.com/services/blockchain-tokens/) |
| Microsoft. plan | [Azure Blueprints](../../governance/blueprints/index.yml) |
| Microsoft. BotService | [Azure Bot Service](/azure/bot-service/) |
| Microsoft. cache | [Azure Cache for Redis](../../azure-cache-for-redis/index.yml) |
| Microsoft. Pojemność | Procesor |
| Microsoft. CDN | [Content Delivery Network](../../cdn/index.yml) |
| Microsoft. CertificateRegistration | [App Service certyfikaty](../../app-service/configure-ssl-certificate.md#import-an-app-service-certificate) |
| Microsoft. ChangeAnalysis | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.ClassicCompute | Maszyna wirtualna klasycznego modelu wdrażania |
| Microsoft. ClassicInfrastructureMigrate | Migracja klasycznego modelu wdrażania |
| Microsoft. ClassicNetwork | Sieć wirtualna klasycznego modelu wdrażania |
| Microsoft. ClassicStorage | Magazyn klasycznych modeli wdrażania |
| Microsoft. ClassicSubscription — [zarejestrowany](#registration) | Klasyczny model wdrażania |
| Microsoft. CognitiveServices | [Cognitive Services](../../cognitive-services/index.yml) |
| Microsoft. Commerce — [zarejestrowany](#registration) | Procesor |
| Microsoft.Compute | [Virtual Machines](../../virtual-machines/index.yml)<br />[Virtual Machine Scale Sets](../../virtual-machine-scale-sets/index.yml) |
| Microsoft. zużycie — [zarejestrowane](#registration) | [Cost Management](/azure/cost-management/) |
| Microsoft. ContainerInstance | [Container Instances](../../container-instances/index.yml) |
| Microsoft. ContainerRegistry | [Container Registry](../../container-registry/index.yml) |
| Microsoft. ContainerService | [Azure Kubernetes Service (AKS)](../../aks/index.yml) |
| Microsoft. CostManagement — [zarejestrowany](#registration) | [Cost Management](/azure/cost-management/) |
| Microsoft. CostManagementExports | [Cost Management](/azure/cost-management/) |
| Microsoft. CustomerLockbox | [Funkcja Skrytka klienta dla platformy Microsoft Azure](../../security/fundamentals/customer-lockbox-overview.md) |
| Microsoft. CustomProviders | [Azure Custom Providers](../custom-providers/overview.md) |
| Microsoft. DataBox | [Azure Data Box](../../databox/index.yml) |
| Microsoft. DataBoxEdge | [Azure Stack Edge](../../databox-online/azure-stack-edge-overview.md) |
| Microsoft. datacegły | [Azure Databricks](/azure/azure-databricks/) |
| Microsoft. datacatalog | [Data Catalog](../../data-catalog/index.yml) |
| Microsoft. DataFactory | [Data Factory](../../data-factory/index.yml) |
| Microsoft. DataLakeAnalytics | [Data Lake Analytics](../../data-lake-analytics/index.yml) |
| Microsoft. kontach datalakestore | [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md) |
| Migracja Microsoft. | [Azure Database Migration Service](../../dms/index.yml) |
| Microsoft. dataprotection | Ochrona danych |
| Microsoft. dataudział | [Azure Data Share](../../data-share/index.yml) |
| Microsoft. DBforMariaDB | [Azure Database for MariaDB](../../mariadb/index.yml) |
| Microsoft. DBforMySQL | [Azure Database for MySQL](../../mysql/index.yml) |
| Microsoft. DBforPostgreSQL | [Azure Database for PostgreSQL](../../postgresql/index.yml) |
| Microsoft. Deploymentmanager | [Deployment Manager platformy Azure](../templates/deployment-manager-overview.md) |
| Microsoft. DesktopVirtualization | [Windows Virtual Desktop](../../virtual-desktop/index.yml) |
| Microsoft.Devices | [Azure IoT Hub](../../iot-hub/index.yml)<br />[Usługa Azure IoT Hub Device Provisioning](../../iot-dps/index.yml) |
| Microsoft. DevOps | [Azure DevOps](/azure/devops/) |
| Microsoft. DevSpaces | [Azure Dev Spaces](../../dev-spaces/index.yml) |
| Microsoft. wspólny | [Azure Lab Services](../../lab-services/index.yml) |
| Microsoft. DigitalTwins | [Azure Digital Twins](../../digital-twins/overview.md) |
| Microsoft.DocumentDB | [Azure Cosmos DB](../../cosmos-db/index.yml) |
| Microsoft. DomainRegistration | [App Service](../../app-service/index.yml) |
| Microsoft. DynamicsLcs | [Usługi cyklu życia](https://lcs.dynamics.com/Logon/Index ) |
| Microsoft. EnterpriseKnowledgeGraph | Wykres merytoryczny dla przedsiębiorstw |
| Microsoft. EventGrid | [Event Grid](../../event-grid/index.yml) |
| Microsoft. EventHub | [Event Hubs](../../event-hubs/index.yml) |
| Microsoft. Features — [zarejestrowane](#registration) | [Azure Resource Manager](../index.yml) |
| Microsoft. GuestConfiguration | [Azure Policy](../../governance/policy/index.yml) |
| Microsoft. HanaOnAzure | [Oprogramowanie SAP HANA na platformie Azure — duże wystąpienia](../../virtual-machines/workloads/sap/hana-overview-architecture.md) |
| Microsoft. HardwareSecurityModules | [Dedykowany moduł HSM platformy Azure](../../dedicated-hsm/index.yml) |
| Microsoft. HDInsight | [HDInsight](../../hdinsight/index.yml) |
| Microsoft. HealthcareApis | [Interfejs API platformy Azure dla standardu FHIR](../../healthcare-apis/fhir/index.yml) |
| Microsoft. HybridCompute | [Azure Arc](../../azure-arc/index.yml) |
| Microsoft. HybridData | [StorSimple](../../storsimple/index.yml) |
| Microsoft. HybridNetwork  | [Prywatne strefy brzegowe](../../networking/edge-zones-overview.md) |
| Microsoft. ImportExport | [Usługa Azure Import/Export](../../import-export/storage-import-export-service.md) |
| Microsoft. Insights | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft. IoTCentral | [Azure IoT Central](../../iot-central/index.yml) |
| Microsoft. IoTSpaces | [Azure Digital Twins](../../digital-twins/index.yml) |
| Microsoft.Intune | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft. kluczy — magazyn | [Usługa Key Vault](../../key-vault/index.yml) |
| Microsoft. Kubernetes | [Azure Kubernetes Service (AKS)](../../aks/index.yml) |
| Microsoft. KubernetesConfiguration | [Azure Kubernetes Service (AKS)](../../aks/index.yml) |
| Microsoft.Kusto | [Azure Data Explorer](/azure/data-explorer/) |
| Microsoft. LabServices | [Azure Lab Services](../../lab-services/index.yml) |
| Microsoft. Logic | [Logic Apps](../../logic-apps/index.yml) |
| Microsoft. MachineLearning | [Machine Learning Studio](../../machine-learning/classic/index.yml) |
| Microsoft.MachineLearningServices | [Azure Machine Learning](../../machine-learning/index.yml) |
| Microsoft. Maintenance | [Konserwacja platformy Azure](../../virtual-machines/maintenance-control-cli.md) |
| Microsoft. ManagedIdentity | [Tożsamości zarządzane dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/index.yml) |
| Microsoft. ManagedNetwork | Sieci wirtualne zarządzane przez usługi PaaS Services |
| Microsoft. ManagedServices | [Azure Lighthouse](../../lighthouse/index.yml) |
| Microsoft. Management | [Grupy zarządzania](../../governance/management-groups/index.yml) |
| Microsoft. Maps | [Azure Maps](../../azure-maps/index.yml) |
| Microsoft. Marketplace | Procesor |
| Microsoft. MarketplaceApps | Procesor |
| Microsoft. MarketplaceOrdering — [zarejestrowany](#registration) | Procesor |
| Microsoft. Media | [Media Services](../../media-services/index.yml) |
| Microsoft. Microservices4Spring | [Azure Spring Cloud](../../spring-cloud/overview.md) |
| Microsoft. Migrowanie | [Azure Migrate](../../migrate/migrate-services-overview.md) |
| Microsoft. MixedReality | [Azure Spatial Anchors](../../spatial-anchors/index.yml) |
| Microsoft. NetApp | [Azure NetApp Files](../../azure-netapp-files/index.yml) |
| Microsoft.Network | [Application Gateway](../../application-gateway/index.yml)<br />[Azure Bastion](../../bastion/index.yml)<br />[Azure DDoS Protection](../../ddos-protection/ddos-protection-overview.md)<br />[Azure DNS](../../dns/index.yml)<br />[Azure ExpressRoute](../../expressroute/index.yml)<br />[Azure Firewall](../../firewall/index.yml)<br />[Azure Front Door Service](../../frontdoor/index.yml)<br />[Link prywatny platformy Azure](../../private-link/index.yml)<br />[Load Balancer](../../load-balancer/index.yml)<br />[Network Watcher](../../network-watcher/index.yml)<br />[Traffic Manager](../../traffic-manager/index.yml)<br />[Virtual Network](../../virtual-network/index.yml)<br />[Wirtualna sieć WAN](../../virtual-wan/index.yml)<br />[VPN Gateway](../../vpn-gateway/index.yml)<br /> |
| Microsoft. notesy | [Azure Notebooks](https://notebooks.azure.com/help/introduction) |
| Microsoft. NotificationHubs | [Notification Hubs](../../notification-hubs/index.yml) |
| Microsoft. ObjectStore | Magazyn obiektów |
| Microsoft. OffAzure | [Azure Migrate](../../migrate/migrate-services-overview.md) |
| Microsoft. OperationalInsights | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft. OperationsManagement | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft. Komunikacja równorzędna | [Usługa Azure Peering Service](../../peering-service/index.yml) |
| Microsoft. PolicyInsights | [Azure Policy](../../governance/policy/index.yml) |
| Microsoft. Portal — [zarejestrowany](#registration) | [Witryna Azure Portal](../../azure-portal/index.yml) |
| Microsoft. PowerBI | [Power BI](/power-bi/power-bi-overview) |
| Microsoft. PowerBIDedicated | [Power BI Embedded](/azure/power-bi-embedded/) |
| Microsoft. PowerPlatform | [Power Platform](/power-platform/) |
| Microsoft. ProjectBabylon | [Azure Data Catalog](../../data-catalog/overview.md) |
| Microsoft. Quantum | [Quantum platformy Azure](https://azure.microsoft.com/services/quantum/) |
| Microsoft. RecoveryServices | [Azure Site Recovery](../../site-recovery/index.yml) |
| Microsoft. RedHatOpenShift | [Azure Red Hat OpenShift](../../virtual-machines/linux/openshift-get-started.md) |
| Microsoft. Relay | [Azure Relay](../../azure-relay/relay-what-is-it.md) |
| Microsoft. ResourceGraph — [zarejestrowany](#registration) | [Azure Resource Graph](../../governance/resource-graph/index.yml) |
| Microsoft. ResourceHealth | [Azure Service Health](../../service-health/index.yml) |
| Microsoft. resources- [Registered](#registration) | [Azure Resource Manager](../index.yml) |
| Microsoft. SaaS | Procesor |
| Microsoft. Scheduler | [Scheduler](../../scheduler/index.yml) |
| Microsoft. Search | [Azure Cognitive Search](../../search/index.yml) |
| Microsoft.Security | [Centrum zabezpieczeń](../../security-center/index.yml) |
| Microsoft. SecurityInsights | [Azure Sentinel](../../sentinel/index.yml) |
| Microsoft. SerialConsole — [zarejestrowany](#registration) | [Konsola szeregowa platformy Azure dla systemu Windows](/troubleshoot/azure/virtual-machines/serial-console-windows) |
| Microsoft.ServiceBus | [Service Bus](/azure/service-bus/) |
| Microsoft. servicefabric | [Service Fabric](../../service-fabric/index.yml) |
| Microsoft. ServiceFabricMesh | [Service Fabric Mesh](../../service-fabric-mesh/index.yml) |
| Microsoft. Services | Procesor |
| Microsoft. SignalRService | [Azure SignalR Service](../../azure-signalr/index.yml) |
| Microsoft. SoftwarePlan | Licencja |
| Microsoft. Solutions | [Azure Managed Applications](../managed-applications/index.yml) |
| Microsoft.Sql | [Azure SQL Database](../../azure-sql/database/index.yml)<br /> [Wystąpienie zarządzane Azure SQL](../../azure-sql/managed-instance/index.yml) <br />[Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Microsoft. SqlVirtualMachine | [Program SQL Server na maszynach wirtualnych platformy Azure](../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) |
| Microsoft.Storage | [Storage](../../storage/index.yml) |
| Microsoft. StorageCache | [Azure HPC Cache](../../hpc-cache/index.yml) |
| Microsoft. StorageSync | [Storage](../../storage/index.yml) |
| Microsoft. StorSimple | [StorSimple](../../storsimple/index.yml) |
| Microsoft. StreamAnalytics | [Azure Stream Analytics](../../stream-analytics/index.yml) |
| Microsoft. Subscription | Procesor |
| Microsoft. Support — [zarejestrowany](#registration) | Procesor |
| Microsoft. Synapse | [Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Microsoft. TimeSeriesInsights | [Azure Time Series Insights](../../time-series-insights/index.yml) |
| Microsoft. token | Token |
| Microsoft. VirtualMachineImages | [Konstruktor obrazów platformy Azure](../../virtual-machines/image-builder-overview.md) |
| Microsoft. VisualStudio | [Azure DevOps](/azure/devops/) |
| Microsoft. VMware | [Rozwiązanie Azure VMware](../../azure-vmware/index.yml) |
| Microsoft. VMwareCloudSimple | [Azure VMware Solution by CloudSimple](../../vmware-cloudsimple/index.md) |
| Microsoft. VSOnline | [Azure DevOps](/azure/devops/) |
| Microsoft. Web | [App Service](../../app-service/index.yml)<br />[Azure Functions](../../azure-functions/index.yml) |
| Microsoft. WindowsDefenderATP | [Zaawansowana ochrona przed zagrożeniami w usłudze Microsoft Defender](../../security-center/security-center-wdatp.md) |
| Microsoft. WindowsESU | Rozszerzone aktualizacje zabezpieczeń |
| Microsoft. WindowsIoT | [Windows 10 IoT Core Services](/windows-hardware/manufacture/iot/iotcoreservicesoverview) |
| Microsoft. Monitor obciążenia został | [Azure Monitor](../../azure-monitor/index.yml) |

## <a name="registration"></a>Rejestracja

Dostawcy zasobów powyżej oznaczona jako **zarejestrowani** są zarejestrowani domyślnie dla Twojej subskrypcji. Aby korzystać z innych dostawców zasobów, należy [je zarejestrować](resource-providers-and-types.md). Jednak wielu dostawców zasobów jest zarejestrowana podczas wykonywania pewnych akcji. Jeśli na przykład utworzysz zasób za pomocą portalu, Portal automatycznie zarejestruje wszystkich niezarejestrowanych dostawców zasobów, które są potrzebne. W przypadku wdrażania zasobów przy użyciu [szablonu Azure Resource Manager](../templates/overview.md)są również rejestrowane wszystkie wymagane dostawcy zasobów.

> [!IMPORTANT]
> Zarejestruj dostawcę zasobów tylko wtedy, gdy wszystko jest gotowe do użycia. Krok rejestracji umożliwia zachowanie najniższych uprawnień w ramach subskrypcji. Złośliwy użytkownik nie może używać dostawców zasobów, którzy nie są zarejestrowani.

## <a name="find-resource-provider"></a>Znajdowanie dostawcy zasobów

Jeśli masz istniejącą infrastrukturę na platformie Azure, ale nie masz pewności, który dostawca zasobów jest używany, możesz znaleźć dostawcę zasobów przy użyciu interfejsu wiersza polecenia platformy Azure lub programu PowerShell. Określ nazwę grupy zasobów, która zawiera zasoby, które mają zostać odnalezione.

W poniższym przykładzie pokazano użycie interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az resource list -g examplegroup
```

Wyniki obejmują typ zasobu. Przestrzeń nazw dostawcy zasobów jest pierwszą częścią typu zasobu. Poniższy przykład pokazuje dostawcę zasobów **Microsoft.**

```json
[
  {
    ...
    "type": "Microsoft.KeyVault/vaults"
  }
]
```

Poniższy przykład używa programu PowerShell:

```azurepowershell-interactive
Get-AzResource -ResourceGroupName examplegroup
```

Wyniki obejmują typ zasobu. Przestrzeń nazw dostawcy zasobów jest pierwszą częścią typu zasobu. Poniższy przykład pokazuje dostawcę zasobów **Microsoft.**

```azurepowershell
Name              : examplekey
ResourceGroupName : examplegroup
ResourceType      : Microsoft.KeyVault/vaults
...
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o dostawcach zasobów, w tym o sposobie rejestrowania dostawcy zasobów, zobacz [dostawcy zasobów platformy Azure i ich typy](resource-providers-and-types.md).