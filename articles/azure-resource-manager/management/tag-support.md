---
title: Obsługa tagów dla zasobów
description: Pokazuje, które typy zasobów platformy Azure obsługują tagi. Zawiera szczegółowe informacje dotyczące wszystkich usług platformy Azure.
ms.topic: conceptual
ms.date: 04/20/2021
ms.openlocfilehash: b196cae267a8d7dc878f055f6b2d70a3ff6f9313
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773967"
---
# <a name="tag-support-for-azure-resources"></a>Obsługa tagów dla zasobów platformy Azure
W tym artykule opisano, czy typ zasobu obsługuje [tagi](tag-resources.md). Kolumna z **etykietą Obsługuje tagi** wskazuje, czy typ zasobu ma właściwość tagu. Kolumna z **etykietą Tag w raporcie kosztów** wskazuje, czy ten typ zasobu przekazuje tag do raportu kosztów. Koszty według tagów można wyświetlić w analizie [kosztów Cost Management](../../cost-management-billing/costs/group-filter.md) faktury rozliczeniowej platformy Azure i [danych dziennego użycia.](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md)

Aby uzyskać te same dane co plik wartości rozdzielanych przecinkami, pobierz [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

Przejdź do przestrzeni nazw dostawcy zasobów:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.Ag IchPlatforma](#microsoftagfoodplatform)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.analysisservices](#microsoftanalysisservices)
> - [Microsoft.AnyBuild](#microsoftanybuild)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppAssessment](#microsoftappassessment)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automanage](#microsoftautomanage)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AVS](#microsoftavs)
> - [Microsoft.Azure.Geneva](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureArcData](#microsoftazurearcdata)
> - [Microsoft.AzureCIS](#microsoftazurecis)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureSphere](#microsoftazuresphere)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.BareMetalInfrastructure](#microsoftbaremetalinfrastructure)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cascade](#microsoftcascade)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ChangeAnalysis](#microsoftchangeanalysis)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.ClusterStor](#microsoftclusterstor)
> - [Microsoft.Codespaces](#microsoftcodespaces)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.ConnectedCache](#microsoftconnectedcache)
> - [Microsoft.ConnectedVehicle](#microsoftconnectedvehicle)
> - [Microsoft.ConnectedVMwarevSphere](#microsoftconnectedvmwarevsphere)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.D365CustomerInsights](#microsoftd365customerinsights)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataProtection](#microsoftdataprotection)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DeviceUpdate](#microsoftdeviceupdate)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EdgeOrder](#microsoftedgeorder)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Experimentation](#microsoftexperimentation)
> - [Microsoft.Przechwyt](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Gallery](#microsoftgallery)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthBot](#microsofthealthbot)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.HybridNetwork](#microsofthybridnetwork)
> - [Microsoft.Zygmej](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft.Insights](#microsoftinsights)
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSecurity](#microsoftiotsecurity)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.Maintenance](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedNetwork](#microsoftmanagednetwork)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.MixedReality](#microsoftmixedreality)
> - [Microsoft.MobileNetwork](#microsoftmobilenetwork)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.Notebooks](#microsoftnotebooks)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.PowerPlatform](#microsoftpowerplatform)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - [Microsoft.Purview](#microsoftpurview)
> - [Microsoft.Quantum](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceConnector](#microsoftresourceconnector)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.ScVmm](#microsoftscvmm)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.ServiceLinker](#microsoftservicelinker)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.Singularity](#microsoftsingularity)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageReplication](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [Microsoft.VMware](#microsoftvmware)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | DomainServices | Tak | Tak |
> | DomainServices / oucontainer | Nie | Nie |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | supportProviders | Nie | Nie |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | Nie | Nie |
> | addsservices | Nie | Nie |
> | Agentów | Nie | Nie |
> | anonymousapiusers | Nie | Nie |
> | konfiguracja | Nie | Nie |
> | dzienniki | Nie | Nie |
> | raporty | Nie | Nie |
> | servicehealthmetrics | Nie | Nie |
> | services | Nie | Nie |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | advisorScore | Nie | Nie |
> | Konfiguracji | Nie | Nie |
> | generateRecommendations | Nie | Nie |
> | metadane | Nie | Nie |
> | zalecenia | Nie | Nie |
> | Suppressions | Nie | Nie |

## <a name="microsoftagfoodplatform"></a>Microsoft.Ag IchPlatforma

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | farmBeats | Tak | Tak |
> | farmBeats / eventGridFilters | Nie | Nie |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | ActionRules | Tak | Tak |
> | alerts | Nie | Nie |
> | alertsList (lista alertów) | Nie | Nie |
> | alertsMetaData | Nie | Nie |
> | alertsSummary | Nie | Nie |
> | alertsSummaryList | Nie | Nie |
> | migrateFromSmartDetection | Nie | Nie |
> | resourceHealthAlertRules | Tak | Tak |
> | smartDetectorAlertRules | Tak | Tak |
> | smartGroups | Nie | Nie |

## <a name="microsoftanalysisservices"></a>Microsoft.analysisservices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Serwerów | Tak | Tak |

## <a name="microsoftanybuild"></a>Microsoft.AnyBuild

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Klastrów | Tak | Tak |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | deletedServices | Nie | Nie |
> | getDomainOwnershipIdentifier | Nie | Nie |
> | reportFeedback | Nie | Nie |
> | usługa | Tak | Tak |
> | validateServiceName | Nie | Nie |

> [!NOTE]
> Usługa Azure API Management obsługuje tworzenie maksymalnie 15 par nazwa/wartość tagu dla każdej usługi.

## <a name="microsoftappassessment"></a>Microsoft.AppAssessment

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | migrateProjects | Tak | Tak |
> | migrateProjects/assessments | Nie | Nie |
> | migrateProjects/assessments/assessedApplications | Nie | Nie |
> | migrateProjects/assessments/assessedApplications/machines | Nie | Nie |
> | migrateProjects/assessments/assessedMachines | Nie | Nie |
> | migrateProjects / assessments / assessedMachines / applications | Nie | Nie |
> | migrateProjects / assessments / machinesToAssess | Nie | Nie |
> | migrateProjects/sites | Nie | Nie |
> | migrateProjects /sites/applianceConfigurations | Nie | Nie |
> | migrateProjects/sites/machines | Nie | Nie |
> | osVersions | Nie | Nie |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | configurationStores (magazyny konfiguracji) | Tak | Nie |
> | configurationStores / eventGridFilters | Nie | Nie |
> | configurationStores/keyValues | Nie | Nie |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Spring | Tak | Tak |
> | Spring/apps | Nie | Nie |
> | Spring/apps/deployments | Nie | Nie |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | attestationProviders | Tak | Tak |
> | defaultProviders | Nie | Nie |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | accessReviewScheduleDefinitions | Nie | Nie |
> | accessReviewScheduleSettings | Nie | Nie |
> | classicAdministrators | Nie | Nie |
> | dataAliases | Nie | Nie |
> | dataPolicyManifests | Nie | Nie |
> | denyAssignments | Nie | Nie |
> | elevateAccess | Nie | Nie |
> | findOrphanRoleAssignments | Nie | Nie |
> | Blokad | Nie | Nie |
> | uprawnienia | Nie | Nie |
> | policyAssignments (przypisania zasad) | Nie | Nie |
> | Policydefinitions | Nie | Nie |
> | policyExemptions | Nie | Nie |
> | policySetDefinitions | Nie | Nie |
> | privateLinkAssociations | Nie | Nie |
> | providerOperations | Nie | Nie |
> | resourceManagementPrivateLinks | Tak | Tak |
> | roleAssignmentApprovals | Nie | Nie |
> | roleAssignments | Nie | Nie |
> | roleAssignmentScheduleInstances | Nie | Nie |
> | roleAssignmentScheduleRequests | Nie | Nie |
> | roleAssignmentSchedules | Nie | Nie |
> | roleAssignmentsUsageMetrics | Nie | Nie |
> | roleDefinitions | Nie | Nie |
> | roleEligibilityScheduleInstances | Nie | Nie |
> | roleEligibilityScheduleRequests | Nie | Nie |
> | roleEligibilitySchedules | Nie | Nie |
> | roleManagementPolicies | Nie | Nie |
> | roleManagementPolicyAssignments | Nie | Nie |

## <a name="microsoftautomanage"></a>Microsoft.Automanage

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Konta | Tak | Tak |
> | configurationProfileAssignments | Nie | Nie |
> | configurationProfilePreferences | Tak | Tak |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | automationAccounts | Tak | Tak |
> | automationAccounts /configurations | Tak | Tak |
> | automationAccounts /jobs | Nie | Nie |
> | automationAccounts / privateEndpointConnectionProxies | Nie | Nie |
> | automationAccounts/privateEndpointConnections | Nie | Nie |
> | automationAccounts /privateLinkResources | Nie | Nie |
> | automationAccounts/runbook | Tak | Tak |
> | automationAccounts /softwareUpdateConfigurations | Nie | Nie |
> | automationAccounts /webhook | Nie | Nie |

> [!NOTE]
> Azure Automation obsługuje tworzenie maksymalnie 15 par nazwa/wartość tagu dla każdego zasobu usługi Automation.

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | privateClouds | Tak | Tak |
> | privateClouds/ dodatki | Nie | Nie |
> | privateClouds / autoryzacje | Nie | Nie |
> | privateClouds/cloudLinks | Nie | Nie |
> | privateClouds/clusters | Nie | Nie |
> | privateClouds / klastry / magazyny danych | Nie | Nie |
> | privateClouds/globalReachConnections | Nie | Nie |
> | privateClouds / hcxEnterpriseSites | Nie | Nie |
> | privateClouds/scriptExecutions | Nie | Nie |
> | privateClouds / scriptPackages | Nie | Nie |
> | privateClouds / scriptPackages / scriptCmdlets | Nie | Nie |
> | privateClouds / workloadNetworks | Nie | Nie |
> | privateClouds / workloadNetworks / dhcpConfigurations | Nie | Nie |
> | privateClouds / workloadNetworks / dnsServices | Nie | Nie |
> | privateClouds / workloadNetworks / dnsZones | Nie | Nie |
> | privateClouds / workloadNetworks / gateways | Nie | Nie |
> | privateClouds / workloadNetworks / portMirroringProfiles | Nie | Nie |
> | privateClouds / workloadNetworks / segmenty | Nie | Nie |
> | privateClouds / workloadNetworks / virtualMachines | Nie | Nie |
> | privateClouds / workloadNetworks / vmGroups | Nie | Nie |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Środowiskach | Nie | Nie |
> | środowiska/konta | Nie | Nie |
> | środowiska/ konta / przestrzenie nazw | Nie | Nie |
> | środowiska/ konta / przestrzenie nazw / konfiguracje | Nie | Nie |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | Tak | Nie |
> | b2ctenants | Nie | Nie |
> | GuestUsages | Tak | Tak |

## <a name="microsoftazurearcdata"></a>Microsoft.AzureArcData

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | dataControllers | Tak | Tak |
> | dataWarehouseInstances | Tak | Tak |
> | postgresInstances | Tak | Tak |
> | sqlManagedInstances | Tak | Tak |
> | sqlServerInstances | Tak | Tak |

## <a name="microsoftazurecis"></a>Microsoft.AzureCIS

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | autopilotŚrodowisko | Tak | Tak |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Sqlserverregistrations | Tak | Tak |
> | sqlServerRegistrations / sqlServers | Nie | Nie |

## <a name="microsoftazuresphere"></a>Microsoft.AzureSphere

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Katalogi | Tak | Tak |
> | katalogi/produkty | Tak | Tak |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | cloudManifestFiles | Nie | Nie |
> | edgeSubscriptions | Tak | Tak |
> | linkedSubscriptions | Tak | Tak |
> | Rejestracji | Tak | Tak |
> | rejestracje/subskrypcji klientów | Nie | Nie |
> | rejestracje/produkty | Nie | Nie |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Klastrów | Tak | Tak |
> | galleryImages | Tak | Tak |
> | networkInterfaces | Tak | Tak |
> | virtualHardDisks | Tak | Tak |
> | virtualMachines | Tak | Tak |
> | virtualNetworks | Tak | Tak |

## <a name="microsoftbaremetalinfrastructure"></a>Microsoft.BareMetalInfrastructure

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | bareMetalInstances | Tak | Tak |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | batchAccounts | Tak | Tak |
> | batchAccounts /certificates | Nie | Nie |
> | batchAccounts/pools | Nie | Nie |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | billingAccounts | Nie | Nie |
> | billingAccounts/agreements | Nie | Nie |
> | billingAccounts/billingPermissions | Nie | Nie |
> | billingAccounts/billingProfiles | Nie | Nie |
> | billingAccounts / billingProfiles / billingPermissions | Nie | Nie |
> | billingAccounts / billingProfiles / billingRoleAssignments | Nie | Nie |
> | billingAccounts / billingProfiles / billingRoleDefinitions | Nie | Nie |
> | billingAccounts / billingProfiles / billingSubscriptions | Nie | Nie |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | Nie | Nie |
> | billingAccounts / billingProfiles / customers | Nie | Nie |
> | billingAccounts / billingProfiles / instrukcje | Nie | Nie |
> | billingAccounts / billingProfiles / invoices | Nie | Nie |
> | billingAccounts / billingProfiles / invoices / pricesheet | Nie | Nie |
> | billingAccounts / billingProfiles / invoices / transactions | Nie | Nie |
> | billingAccounts / billingProfiles / invoiceSections | Nie | Nie |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | Nie | Nie |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | Nie | Nie |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | Nie | Nie |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | Nie | Nie |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | Nie | Nie |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | Nie | Nie |
> | billingAccounts / billingProfiles / invoiceSections / products | Nie | Nie |
> | billingAccounts / billingProfiles / invoiceSections / products / transfer | Nie | Nie |
> | billingAccounts / billingProfiles / invoiceSections / products / updateAutoRenew | Nie | Nie |
> | billingAccounts / billingProfiles / invoiceSections / transactions | Nie | Nie |
> | billingAccounts/billingProfiles/invoiceSections/transfers | Nie | Nie |
> | billingAccounts / billingProfiles / invoiceSections / validateDeleteInvoiceSectionEligibility | Nie | Nie |
> | billingAccounts / BillingProfiles / patchOperations | Nie | Nie |
> | billingAccounts / billingProfiles / paymentMethods | Nie | Nie |
> | billingAccounts/billingProfiles/policies | Nie | Nie |
> | billingAccounts/billingProfiles/pricesheet | Nie | Nie |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | Nie | Nie |
> | billingAccounts / billingProfiles / products | Nie | Nie |
> | billingAccounts/billingProfiles/reservations | Nie | Nie |
> | billingAccounts/billingProfiles/transactions | Nie | Nie |
> | billingAccounts / billingProfiles / validateDeleteBillingProfileEligibility | Nie | Nie |
> | billingAccounts / billingProfiles / validateDetachPaymentMethodEligibility (Uprawnienia do rozliczeń/rozliczeńProfile/ validateDetachPaymentMethod) | Nie | Nie |
> | billingAccounts/billingRoleAssignments | Nie | Nie |
> | billingAccounts/billingRoleDefinitions | Nie | Nie |
> | billingAccounts/billingSubscriptions | Nie | Nie |
> | billingAccounts / billingSubscriptions / elevateRole | Nie | Nie |
> | billingAccounts/billingSubscriptions/invoices | Nie | Nie |
> | billingAccounts / createBillingRoleAssignment | Nie | Nie |
> | billingAccounts / createInvoiceSectionOperations | Nie | Nie |
> | billingAccounts /customers | Nie | Nie |
> | billingAccounts / customers / billingPermissions | Nie | Nie |
> | billingAccounts / customers / billingSubscriptions | Nie | Nie |
> | billingAccounts / customers / initiateTransfer | Nie | Nie |
> | billingAccounts / customers / policies | Nie | Nie |
> | billingAccounts / customers / products | Nie | Nie |
> | billingAccounts / customers / transactions | Nie | Nie |
> | billingAccounts /customers/transfers | Nie | Nie |
> | billingAccounts /departments | Nie | Nie |
> | billingAccounts /departments/billingPermissions | Nie | Nie |
> | billingAccounts / działy / billingRoleAssignments | Nie | Nie |
> | billingAccounts / działy / billingRoleDefinitions | Nie | Nie |
> | billingAccounts / działy / rozliczeniaSubskrypcje | Nie | Nie |
> | billingAccounts / enrollmentAccounts | Nie | Nie |
> | billingAccounts / enrollmentAccounts / billingPermissions | Nie | Nie |
> | billingAccounts / enrollmentAccounts / billingRoleAssignments | Nie | Nie |
> | billingAccounts / enrollmentAccounts / billingRoleDefinitions | Nie | Nie |
> | billingAccounts / enrollmentAccounts / billingSubscriptions | Nie | Nie |
> | billingAccounts /invoices | Nie | Nie |
> | billingAccounts /invoices/transactions | Nie | Nie |
> | billingAccounts / invoices / transactionSummary | Nie | Nie |
> | billingAccounts/invoiceSections | Nie | Nie |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | Nie | Nie |
> | billingAccounts/invoiceSections/billingSubscriptions | Nie | Nie |
> | billingAccounts / invoiceSections / billingSubscriptions / transfer | Nie | Nie |
> | billingAccounts / invoiceSections / elevate | Nie | Nie |
> | billingAccounts / invoiceSections / initiateTransfer | Nie | Nie |
> | billingAccounts / invoiceSections / patchOperations | Nie | Nie |
> | billingAccounts / invoiceSections / productMoveOperations | Nie | Nie |
> | billingAccounts /invoiceSections / products | Nie | Nie |
> | billingAccounts / invoiceSections / products / transfer | Nie | Nie |
> | billingAccounts / invoiceSections / products / updateAutoRenew | Nie | Nie |
> | billingAccounts/invoiceSections/transactions | Nie | Nie |
> | billingAccounts/invoiceSections/transfers | Nie | Nie |
> | billingAccounts / lineOfCredit | Nie | Nie |
> | billingAccounts/patchOperations | Nie | Nie |
> | billingAccounts /payableOverage | Nie | Nie |
> | billingAccounts/paymentMethods | Nie | Nie |
> | billingAccounts /payNow | Nie | Nie |
> | billingAccounts/products | Nie | Nie |
> | billingAccounts /reservations | Nie | Nie |
> | billingAccounts /transactions | Nie | Nie |
> | billingPeriods | Nie | Nie |
> | billingPermissions (rozliczenia)Missions (Rozliczenia )Missions | Nie | Nie |
> | billingProperty | Nie | Nie |
> | billingRoleAssignments | Nie | Nie |
> | billingRoleDefinitions | Nie | Nie |
> | createBillingRoleAssignment | Nie | Nie |
> | Działów | Nie | Nie |
> | enrollmentAccounts (konta rejestracji) | Nie | Nie |
> | Faktury | Nie | Nie |
> | Promocje | Nie | Nie |
> | Transfery | Nie | Nie |
> | transfery /acceptTransfer | Nie | Nie |
> | transfery/odrzućPrzeniesienie | Nie | Nie |
> | transfers/operationStatus | Nie | Nie |
> | transfery /validateTransfer | Nie | Nie |
> | validateAddress | Nie | Nie |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | mapApis | Tak | Tak |
> | updateCommunicationPreference | Nie | Nie |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | Tak | Tak |
> | CordaMembers | Tak | Tak |
> | Obserwatorów | Tak | Tak |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | TokenServices | Tak | Tak |
> | TokenServices / BlockchainNetworks | Nie | Nie |
> | TokenServices/Groups | Nie | Nie |
> | TokenServices / Groups / Accounts | Nie | Nie |
> | TokenServices/TokenTemplates | Nie | Nie |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | Nie | Nie |
> | blueprintAssignments/assignmentOperations | Nie | Nie |
> | blueprintAssignments/operations | Nie | Nie |
> | Plany | Nie | Nie |
> | strategie/artefakty | Nie | Nie |
> | strategie/wersje | Nie | Nie |
> | strategie/wersje/artefakty | Nie | Nie |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | botServices | Tak | Tak |
> | botServices/channels | Nie | Nie |
> | botServices/connections | Nie | Nie |
> | hostSettings | Nie | Nie |
> | języki | Nie | Nie |
> | szablonów | Nie | Nie |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Redis | Tak | Tak |
> | Redis / EventGridFilters | Nie | Nie |
> | Redis/privateEndpointConnectionProxies | Nie | Nie |
> | Redis / privateEndpointConnectionProxies / validate | Nie | Nie |
> | Redis/privateEndpointConnections | Nie | Nie |
> | Redis/privateLinkResources | Nie | Nie |
> | redisEnterprise | Tak | Tak |
> | redisEnterprise/databases | Nie | Nie |
> | RedisEnterprise/privateEndpointConnectionProxies | Nie | Nie |
> | RedisEnterprise / privateEndpointConnectionProxies / validate | Nie | Nie |
> | RedisEnterprise/privateEndpointConnections | Nie | Nie |
> | RedisEnterprise/privateLinkResources | Nie | Nie |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | appliedReservations | Nie | Nie |
> | autoQuotaIncrease | Nie | Nie |
> | calculateExchange | Nie | Nie |
> | calculatePrice | Nie | Nie |
> | calculatePurchasePrice | Nie | Nie |
> | Katalogi | Nie | Nie |
> | commercialReservationOrders | Nie | Nie |
> | Exchange | Nie | Nie |
> | ownReservations | Nie | Nie |
> | placePurchaseOrder | Nie | Nie |
> | reservationOrders | Nie | Nie |
> | reservationOrders / calculateRekowany | Nie | Nie |
> | reservationOrders/merge | Nie | Nie |
> | reservationOrders/reservations | Nie | Nie |
> | reservationOrders /reservations/revisions | Nie | Nie |
> | reservationOrders/ return | Nie | Nie |
> | reservationOrders /split | Nie | Nie |
> | reservationOrders/swap | Nie | Nie |
> | Rezerwacje | Nie | Nie |
> | resourceProviders | Nie | Nie |
> | zasoby | Nie | Nie |
> | validateReservationOrder | Nie | Nie |

## <a name="microsoftcascade"></a>Microsoft.Cascade

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | lokacje | Tak | Tak |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Nie | Nie |
> | CdnWebApplicationFirewallPolicies | Tak | Tak |
> | edgenodes (węzły brzegowe) | Nie | Nie |
> | Profile | Tak | Tak |
> | profiles/afdendpoints | Tak | Tak |
> | profiles/afdendpoints/routes | Nie | Nie |
> | profile/domeny niestandardowe | Nie | Nie |
> | profile/punkty końcowe | Tak | Tak |
> | profile/punkty końcowe/domeny niestandardowe | Nie | Nie |
> | profile/punkty końcowe/grupy pochodzenia | Nie | Nie |
> | profile/punkty końcowe/źródła | Nie | Nie |
> | profiles/origingroups | Nie | Nie |
> | profile /origingroups/origins | Nie | Nie |
> | profile/rulesets | Nie | Nie |
> | profile/rulesets/rules | Nie | Nie |
> | profile/wpisy tajne | Nie | Nie |
> | profile/zasady zabezpieczeń | Nie | Nie |
> | validateProbe | Nie | Nie |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | certificateOrders | Tak | Tak |
> | certificateOrders/certificates | Nie | Nie |
> | validateCertificateRegistrationInformation | Nie | Nie |

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | zmiany | Nie | Nie |
> | profil | Nie | Nie |
> | resourceChanges | Nie | Nie |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | możliwości | Nie | Nie |
> | domainNames (Nazwy domen) | Nie | Nie |
> | domainNames / capabilities | Nie | Nie |
> | domainNames / internalLoadBalancers | Nie | Nie |
> | domainNames / serviceCertificates | Nie | Nie |
> | domainNames / slots | Nie | Nie |
> | domainNames / miejsca / role | Nie | Nie |
> | domainNames / miejsca / role / metricDefinitions | Nie | Nie |
> | domainNames / miejsca / role / metryki | Nie | Nie |
> | moveSubscriptionResources | Nie | Nie |
> | operatingSystemFamilies | Nie | Nie |
> | operatingSystems | Nie | Nie |
> | quotas | Nie | Nie |
> | resourceTypes | Nie | Nie |
> | validateSubscriptionMoveAvailability | Nie | Nie |
> | virtualMachines | Nie | Nie |
> | virtualMachines/diagnosticSettings | Nie | Nie |
> | virtualMachines/metricDefinitions | Nie | Nie |
> | virtualMachines/metrics | Nie | Nie |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | Nie | Nie |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | możliwości | Nie | Nie |
> | expressRouteCrossConnections | Nie | Nie |
> | expressRouteCrossConnections/ komunikacja równorzędna | Nie | Nie |
> | gatewaySupportedDevices | Nie | Nie |
> | networkSecurityGroups | Nie | Nie |
> | quotas | Nie | Nie |
> | reservedIps | Nie | Nie |
> | virtualNetworks | Nie | Nie |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | Nie | Nie |
> | virtualNetworks/virtualNetworkPeerings | Nie | Nie |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | możliwości | Nie | Nie |
> | Dysków | Nie | Nie |
> | images | Nie | Nie |
> | osImages | Nie | Nie |
> | osPlatformImages | Nie | Nie |
> | publicImages | Nie | Nie |
> | quotas | Nie | Nie |
> | konta magazynu | Nie | Nie |
> | storageAccounts /blobServices | Nie | Nie |
> | storageAccounts / fileServices | Nie | Nie |
> | storageAccounts / metricDefinitions | Nie | Nie |
> | storageAccounts /metrics | Nie | Nie |
> | storageAccounts /queueServices | Nie | Nie |
> | storageAccounts /services | Nie | Nie |
> | storageAccounts / services / diagnosticSettings | Nie | Nie |
> | storageAccounts / services / metricDefinitions | Nie | Nie |
> | storageAccounts / services / metrics | Nie | Nie |
> | storageAccounts / tableServices | Nie | Nie |
> | storageAccounts / vmImages | Nie | Nie |
> | vmImages | Nie | Nie |

## <a name="microsoftclusterstor"></a>Microsoft.ClusterStor

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Węzłów | Tak | Tak |

## <a name="microsoftcodespaces"></a>Microsoft.Codespaces

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Plany | Tak | Nie |
> | zarejestrowaneSubskrypcje | Nie | Nie |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Konta | Tak | Tak |
> | accounts/privateEndpointConnectionProxies | Nie | Nie |
> | accounts/privateEndpointConnections | Nie | Nie |
> | accounts/privateLinkResources | Nie | Nie |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | RateCard | Nie | Nie |
> | UsageAggregates | Nie | Nie |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | availabilitySets | Tak | Tak |
> | cloudServices | Tak | Tak |
> | cloudServices/networkInterfaces | Nie | Nie |
> | cloudServices /publicIPAddresses | Nie | Nie |
> | cloudServices/roleInstances | Nie | Nie |
> | cloudServices / roleInstances / networkInterfaces | Nie | Nie |
> | cloudServices/roles | Nie | Nie |
> | diskAccesses | Tak | Tak |
> | diskEncryptionSets | Tak | Tak |
> | Dysków | Tak | Tak |
> | Galerie | Tak | Tak |
> | galerie/aplikacje | Nie | Nie |
> | galerie/aplikacje/wersje | Nie | Nie |
> | galerie/obrazy | Nie | Nie |
> | galerie/obrazy/wersje | Nie | Nie |
> | hostGroups | Tak | Tak |
> | hostGroups/hosts | Tak | Tak |
> | images | Tak | Tak |
> | proximityPlacementGroups | Tak | Tak |
> | restorePointCollections | Tak | Tak |
> | restorePointCollections / restorePoints | Nie | Nie |
> | sharedVMExtensions | Tak | Tak |
> | sharedVMExtensions / versions | Nie | Nie |
> | sharedVMImages | Tak | Tak |
> | sharedVMImages / wersje | Nie | Nie |
> | Migawki | Tak | Tak |
> | sshPublicKeys | Tak | Tak |
> | virtualMachines (maszyny wirtualne) | Tak | Tak |
> | virtualMachines /extensions | Tak | Tak |
> | virtualMachines / metricDefinitions | Nie | Nie |
> | virtualMachines / runCommands | Tak | Tak |
> | virtualMachineScaleSets | Tak | Tak |
> | virtualMachineScaleSets/extensions | Nie | Nie |
> | virtualMachineScaleSets/ networkInterfaces | Nie | Nie |
> | virtualMachineScaleSets/ publicIPAddresses | Tak | Nie |
> | virtualMachineScaleSets/virtualMachines | Nie | Nie |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | Nie | Nie |

> [!NOTE]
> Nie można dodać tagu do maszyny wirtualnej, która została oznaczona jako uogólniona. Maszynę wirtualną należy oznaczyć jako uogólniony za pomocą [set-AzVm -Generalized](/powershell/module/Az.Compute/Set-AzVM) lub [az vm generalize](/cli/azure/vm#az_vm_generalize).

## <a name="microsoftconnectedcache"></a>Microsoft.ConnectedCache

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | CacheNodes | Tak | Tak |

## <a name="microsoftconnectedvehicle"></a>Microsoft.ConnectedVehicle

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | konta platformy | Tak | Tak |
> | registeredSubscriptions | Nie | Nie |

## <a name="microsoftconnectedvmwarevsphere"></a>Microsoft.ConnectedVMwarevSphere

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | ResourcePools | Tak | Tak |
> | Centra VCenter | Tak | Tak |
> | VCenters / InventoryItems | Nie | Nie |
> | VirtualMachines | Tak | Tak |
> | VirtualMachines / Extensions | Tak | Tak |
> | VirtualMachines / GuestAgents | Nie | Nie |
> | VirtualMachines / HybridIdentityMetadata | Nie | Nie |
> | VirtualMachineTemplates | Tak | Tak |
> | VirtualNetworks | Tak | Tak |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | AggregatedCost | Nie | Nie |
> | Salda | Nie | Nie |
> | Budżety | Nie | Nie |
> | Opłaty | Nie | Nie |
> | CostTags | Nie | Nie |
> | Kredyty | Nie | Nie |
> | zdarzenia | Nie | Nie |
> | Prognozy | Nie | Nie |
> | Wiele | Nie | Nie |
> | Rynkach | Nie | Nie |
> | Arkusze cen | Nie | Nie |
> | Produktów | Nie | Nie |
> | ReservationDetails | Nie | Nie |
> | ReservationRecommendationDetails | Nie | Nie |
> | ReservationRecommendations | Nie | Nie |
> | ReservationSummaries | Nie | Nie |
> | ReservationTransactions | Nie | Nie |
> | Tagi | Nie | Nie |
> | Najemców | Nie | Nie |
> | Terminologia | Nie | Nie |
> | UsageDetails (Szczegóły użycia) | Nie | Nie |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | containerGroups | Tak | Tak |
> | serviceAssociationLinks | Nie | Nie |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Rejestrów | Tak | Tak |
> | rejestry/puli agentów | Tak | Tak |
> | rejestry/kompilacje | Nie | Nie |
> | rejestry/kompilacje/anulowanie | Nie | Nie |
> | rejestry/kompilacje/getLogLink | Nie | Nie |
> | rejestry/buildTasks | Tak | Tak |
> | rejestry / buildTasks / kroki | Nie | Nie |
> | rejestry /connectedRegistries | Nie | Nie |
> | rejestry/connectedRegistries/dezaktywacja | Nie | Nie |
> | rejestry /eventGridFilters | Nie | Nie |
> | rejestry/exportPipelines | Nie | Nie |
> | rejestry /generateCredentials | Nie | Nie |
> | rejestry / getBuildSourceUploadUrl | Nie | Nie |
> | rejestry / GetCredentials | Nie | Nie |
> | rejestry / importImage | Nie | Nie |
> | rejestry/importPipelines | Nie | Nie |
> | rejestry/potokiRuns | Nie | Nie |
> | rejestry /privateEndpointConnectionProxies | Nie | Nie |
> | rejestry / privateEndpointConnectionProxies / weryfikacja | Nie | Nie |
> | rejestry/privateEndpointConnections | Nie | Nie |
> | rejestry/privateLinkResources | Nie | Nie |
> | rejestry/kolejkaBuild | Nie | Nie |
> | rejestry / regenerateCredential | Nie | Nie |
> | rejestry /regenerateCredentials | Nie | Nie |
> | rejestry/replikacje | Tak | Tak |
> | rejestry/przebiegi | Nie | Nie |
> | rejestry/przebiegi/anulowanie | Nie | Nie |
> | rejestry /scheduleRun | Nie | Nie |
> | rejestry/zakresMapy | Nie | Nie |
> | rejestry/zadaniaRuns | Nie | Nie |
> | rejestry/zadania | Tak | Tak |
> | rejestry/tokeny | Nie | Nie |
> | rejestry/updatePolicies | Nie | Nie |
> | rejestry/webhook | Tak | Tak |
> | rejestry/ webhook / getCallbackConfig | Nie | Nie |
> | rejestry/webhook/ping | Nie | Nie |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | containerServices | Tak | Tak |
> | managedClusters | Tak | Tak |
> | ManagedClusters / eventGridFilters | Nie | Nie |
> | openShiftManagedClusters | Tak | Tak |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Alerty | Nie | Nie |
> | Konta rozliczeniowe | Nie | Nie |
> | Budżety | Nie | Nie |
> | Połączenia w chmurze | Nie | Nie |
> | Łączniki | Tak | Tak |
> | costAllocationRules | Nie | Nie |
> | Działy | Nie | Nie |
> | Wymiary | Nie | Nie |
> | Konta rejestracji | Nie | Nie |
> | Eksporty | Nie | Nie |
> | ExternalBillingAccounts | Nie | Nie |
> | ExternalBillingAccounts / Alerts | Nie | Nie |
> | ExternalBillingAccounts /Dimensions | Nie | Nie |
> | ExternalBillingAccounts / Forecast | Nie | Nie |
> | ExternalBillingAccounts / Query | Nie | Nie |
> | ExternalSubscriptions | Nie | Nie |
> | ExternalSubscriptions / Alerts | Nie | Nie |
> | ExternalSubscriptions /Dimensions | Nie | Nie |
> | ExternalSubscriptions / Forecast | Nie | Nie |
> | ExternalSubscriptions / Query | Nie | Nie |
> | fetchPrices | Nie | Nie |
> | Prognoza | Nie | Nie |
> | GenerateDetailedCostReport | Nie | Nie |
> | GenerateReservationDetailsReport | Nie | Nie |
> | Insights | Nie | Nie |
> | Zapytanie | Nie | Nie |
> | zarejestruj | Nie | Nie |
> | Konfiguracje raportów | Nie | Nie |
> | Raporty | Nie | Nie |
> | ScheduledActions | Nie | Nie |
> | Ustawienia | Nie | Nie |
> | showbackRules | Nie | Nie |
> | Widoki | Nie | Nie |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | DisableLockbox | Nie | Nie |
> | EnableLockbox | Nie | Nie |
> | Żądania | Nie | Nie |
> | TenantOptedIn | Nie | Nie |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Stowarzyszenia | Nie | Nie |
> | resourceProviders | Tak | Tak |

## <a name="microsoftd365customerinsights"></a>Microsoft.D365CustomerInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Wystąpień | Tak | Tak |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Zadania | Tak | Tak |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | Tak | Tak |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | obszary robocze | Tak | Tak |
> | obszary robocze/dbWorkspaces | Nie | Nie |
> | obszary robocze/virtualNetworkPeerings | Nie | Nie |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Katalogi | Tak | Tak |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | dataFactories | Tak | Tak |
> | dataFactories/diagnosticSettings | Nie | Nie |
> | dataFactories/metricDefinitions | Nie | Nie |
> | dataFactorySchema | Nie | Nie |
> | Fabryk | Tak | Tak |
> | fabryki/integracjaRuntimes | Nie | Nie |

> [!NOTE]
> Jeśli masz środowiska Azure-SSIS Integration Runtime w fabryce danych, ich koszt działania zostanie oznaczony tagami fabryki danych. Uruchamianie środowisk Azure SSIS Integration Runtime należy zatrzymać i ponownie uruchomić, aby nowe tagi fabryki danych można było zastosować do kosztów działania.

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Konta | Tak | Tak |
> | accounts/dataLakeStoreAccounts | Nie | Nie |
> | accounts/storageAccounts | Nie | Nie |
> | accounts/storageAccounts/containers | Nie | Nie |
> | accounts/transferAnalyticsUnits | Nie | Nie |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Konta | Tak | Tak |
> | accounts/eventGridFilters | Nie | Nie |
> | accounts/firewallRules | Nie | Nie |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | DatabaseMigrations (Migracje bazy danych) | Nie | Nie |
> | services | Tak | Tak |
> | usługi/projekty | Tak | Tak |
> | SqlMigrationServices | Tak | Tak |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | BackupVaults | Tak | Tak |
> | ResourceGuards | Tak | Tak |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Konta | Tak | Tak |
> | konta/udziały | Nie | Nie |
> | konta/udziały/zestawy danych | Nie | Nie |
> | konta/udziały/zaproszenia | Nie | Nie |
> | accounts/shares/providersharesubscriptions | Nie | Nie |
> | konta/udziały/synchronizacjaUstawienia | Nie | Nie |
> | konta/udziałysubskrypcje | Nie | Nie |
> | accounts/sharesubscriptions/consumerSourceDataSets | Nie | Nie |
> | konta/ udziałysubskrypcje /datasetmappings | Nie | Nie |
> | konta/udziałysubskrypcje/wyzwalacze | Nie | Nie |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Serwerów | Tak | Tak |
> | serwery/doradcy | Nie | Nie |
> | serwery/klucze | Nie | Nie |
> | servers/privateEndpointConnectionProxies | Nie | Nie |
> | servers/privateEndpointConnections | Nie | Nie |
> | servers/privateLinkResources | Nie | Nie |
> | servers/queryTexts | Nie | Nie |
> | servers/recoverableServers | Nie | Nie |
> | servers/ resetQueryPerformanceInsightData | Nie | Nie |
> | serwery/uruchamianie | Nie | Nie |
> | serwery/zatrzymywanie | Nie | Nie |
> | servers/topQueryStatistics | Nie | Nie |
> | servers/virtualNetworkRules | Nie | Nie |
> | servers/waitStatistics | Nie | Nie |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | flexibleServers | Tak | Tak |
> | Serwerów | Tak | Tak |
> | serwery/doradcy | Nie | Nie |
> | serwery/klucze | Nie | Nie |
> | servers/privateEndpointConnectionProxies | Nie | Nie |
> | servers/privateEndpointConnections | Nie | Nie |
> | serwery /privateLinkResources | Nie | Nie |
> | serwery/queryTexts | Nie | Nie |
> | serwery /recoverableServers | Nie | Nie |
> | servers / resetQueryPerformanceInsightData | Nie | Nie |
> | serwery/uruchamianie | Nie | Nie |
> | serwery/zatrzymywanie | Nie | Nie |
> | servers/topQueryStatistics | Nie | Nie |
> | serwery/uaktualnianie | Nie | Nie |
> | servers/virtualNetworkRules | Nie | Nie |
> | servers/waitStatistics | Nie | Nie |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | flexibleServers | Tak | Tak |
> | Servergroups | Tak | Tak |
> | serverGroupsv2 | Tak | Tak |
> | Serwerów | Tak | Tak |
> | serwery/doradcy | Nie | Nie |
> | serwery/klucze | Nie | Nie |
> | servers/privateEndpointConnectionProxies | Nie | Nie |
> | servers/privateEndpointConnections | Nie | Nie |
> | servers/privateLinkResources | Nie | Nie |
> | servers/queryTexts | Nie | Nie |
> | servers/recoverableServers | Nie | Nie |
> | servers/ resetQueryPerformanceInsightData | Nie | Nie |
> | servers/topQueryStatistics | Nie | Nie |
> | servers/virtualNetworkRules | Nie | Nie |
> | servers/waitStatistics | Nie | Nie |
> | serversv2 | Tak | Tak |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | artifactSources | Tak | Tak |
> | Wdrożenia | Tak | Tak |
> | serviceTopologies | Tak | Tak |
> | serviceTopologies /services | Tak | Tak |
> | serviceTopologies / services / serviceUnits | Tak | Tak |
> | kroki | Tak | Tak |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | grupy aplikacji | Tak | Tak |
> | applicationgroups/applications | Nie | Nie |
> | grupy aplikacji/komputery stacjonarne | Nie | Nie |
> | applicationgroups / startmenuitems | Nie | Nie |
> | hostpools (bufory hostów) | Tak | Tak |
> | hostpools/msixpackages | Nie | Nie |
> | hostpools/sessionhosts | Nie | Nie |
> | hostpools/sessionhosts/usersessions | Nie | Nie |
> | hostpools/usersessions | Nie | Nie |
> | scalingPlans | Tak | Tak |
> | obszary robocze | Tak | Tak |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | ElasticPools | Tak | Tak |
> | ElasticPools / IotHubTenants | Tak | Tak |
> | ElasticPools / IotHubTenants / securitySettings | Nie | Nie |
> | IotHubs | Tak | Tak |
> | IotHubs / eventGridFilters | Nie | Nie |
> | IotHubs / securitySettings | Nie | Nie |
> | ProvisioningServices | Tak | Tak |
> | Zwyczaje | Nie | Nie |

## <a name="microsoftdeviceupdate"></a>Microsoft.DeviceUpdate

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Konta | Tak | Tak |
> | konta/wystąpienia | Tak | Tak |
> | registeredSubscriptions | Nie | Nie |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Rurociągów | Tak | Tak |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | kontrolery | Tak | Tak |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | centra laboratoryjne | Tak | Tak |
> | Labs | Tak | Tak |
> | laboratoria/środowiska | Tak | Tak |
> | labs/serviceRunners | Tak | Tak |
> | laboratoria/maszyny wirtualne | Tak | Tak |
> | Harmonogramy | Tak | Tak |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | digitalTwinsInstances | Tak | Tak |
> | digitalTwinsInstances / endpoints | Nie | Nie |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | cassandraClusters | Tak | Tak |
> | databaseAccountNames | Nie | Nie |
> | databaseAccounts (konta bazy danych) | Tak | Tak |
> | restorableDatabaseAccounts | Nie | Nie |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Domen | Tak | Tak |
> | domains/domainOwnershipIdentifiers | Nie | Nie |
> | generateSsoRequest | Nie | Nie |
> | topLevelDomains | Nie | Nie |
> | validateDomainRegistrationInformation | Nie | Nie |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | lcsprojects | Nie | Nie |
> | lcsprojects/clouddeployments | Nie | Nie |
> | lcsprojects /connectors | Nie | Nie |

## <a name="microsoftedgeorder"></a>Microsoft.EdgeOrder

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Adresy | Tak | Tak |
> | orderCollections | Tak | Tak |
> | orders | Tak | Tak |
> | productFamiliesMetadata | Nie | Nie |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | services | Tak | Tak |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Domen | Tak | Tak |
> | domeny/tematy | Nie | Nie |
> | eventSubscriptions | Nie | Nie |
> | extensionTopics | Nie | Nie |
> | partnerNamespaces | Tak | Tak |
> | partnerNamespaces / eventChannels | Nie | Nie |
> | partnerRegistrations | Tak | Tak |
> | partnerTopics | Tak | Tak |
> | partnerTopics/ eventSubscriptions | Nie | Nie |
> | systemTopics | Tak | Tak |
> | systemTopics/eventSubscriptions | Nie | Nie |
> | Tematy | Tak | Tak |
> | topicTypes | Nie | Nie |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Klastrów | Tak | Tak |
> | Obszary nazw | Tak | Tak |
> | przestrzenie nazw/reguły autoryzacji | Nie | Nie |
> | namespaces/disasterrecoveryconfigs | Nie | Nie |
> | namespaces/eventhubs | Nie | Nie |
> | namespaces /eventhubs/authorizationrules | Nie | Nie |
> | namespaces / eventhubs / consumergroups | Nie | Nie |
> | namespaces/networkrulesets | Nie | Nie |
> | namespaces/privateEndpointConnections | Nie | Nie |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | experimentWorkspaces | Tak | Tak |

## <a name="microsoftfalcon"></a>Microsoft.Zygmej

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Obszary nazw | Tak | Tak |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | featureConfigurations | Nie | Nie |
> | featureProviderNamespaces | Nie | Nie |
> | featureProviders | Nie | Nie |
> | funkcje | Nie | Nie |
> | dostawców | Nie | Nie |
> | subscriptionFeatureRegistrations | Nie | Nie |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Zarejestrować | Nie | Nie |
> | galleryitems | Nie | Nie |
> | generateartifactaccessuri | Nie | Nie |
> | myareas | Nie | Nie |
> | myareas / obszary | Nie | Nie |
> | myareas / obszary / obszary | Nie | Nie |
> | myareas / obszary / obszary / galleryitems | Nie | Nie |
> | myareas / obszary / galleryitems | Nie | Nie |
> | myareas /galleryitems | Nie | Nie |
> | zarejestruj | Nie | Nie |
> | zasoby | Nie | Nie |
> | retrieveresourcesbyid | Nie | Nie |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Konta | Tak | Tak |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | autoManagedAccounts | Tak | Tak |
> | autoManagedVmConfigurationProfiles | Tak | Tak |
> | configurationProfileAssignments | Nie | Nie |
> | guestConfigurationAssignments | Nie | Nie |
> | Oprogramowania | Nie | Nie |
> | softwareUpdateProfile | Nie | Nie |
> | softwareUpdates | Nie | Nie |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | hanaInstances | Tak | Tak |
> | sapMonitors | Tak | Tak |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | dedicatedHSMs | Tak | Tak |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | clusterPools | Tak | Tak |
> | clusterPools/clusters | Tak | Tak |
> | Klastrów | Tak | Tak |
> | klastry/aplikacje | Nie | Nie |

## <a name="microsofthealthbot"></a>Microsoft.HealthBot

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | healthBots | Tak | Tak |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | services | Tak | Tak |
> | usługi /iomtconnectors | Nie | Nie |
> | usługi / iomtconnectors / połączenia | Nie | Nie |
> | usługi / iomtconnectors / mapowania | Nie | Nie |
> | services/privateEndpointConnectionProxies | Nie | Nie |
> | services/privateEndpointConnections | Nie | Nie |
> | services/privateLinkResources | Nie | Nie |
> | obszary robocze | Tak | Tak |
> | obszary robocze/dicomservices | Tak | Tak |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Maszyny | Tak | Tak |
> | maszyny /assessPatches | Nie | Nie |
> | maszyny/rozszerzenia | Tak | Tak |
> | machines/installPatches | Nie | Nie |
> | machines/privateLinkScopes | Nie | Nie |
> | privateLinkScopes | Tak | Tak |
> | privateLinkScopes / privateEndpointConnectionProxies | Nie | Nie |
> | privateLinkScopes / privateEndpointConnections | Nie | Nie |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Menedżerowie danych | Tak | Tak |

## <a name="microsofthybridnetwork"></a>Microsoft.HybridNetwork

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | devices | Tak | Tak |
> | networkfunctions | Tak | Tak |
> | networkFunctionVendors | Nie | Nie |
> | registeredSubscriptions | Nie | Nie |
> | Dostawców | Nie | Nie |
> | Dostawcy/dostawcykus | Nie | Nie |
> | Dostawcy/dostawcy/wersje zapoznawczesubskrypcje | Nie | Nie |
> | virtualNetworkFunctions | Tak | Tak |
> | virtualNetworkFunctionVendors | Nie | Nie |

## <a name="microsofthydra"></a>Microsoft.Zygmej

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Składniki | Tak | Tak |
> | networkScopes | Tak | Tak |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Zadania | Tak | Tak |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | actionGroups | Tak | Tak |
> | activityLogAlerts | Tak | Tak |
> | alertrules (alerty) | Tak | Tak |
> | ustawienia autoskalowania | Tak | Tak |
> | Składniki | Tak | Tak |
> | components/linkedStorageAccounts | Nie | Nie |
> | components / ProactiveDetectionConfigs | Nie | Nie |
> | diagnosticSettings | Nie | Nie |
> | guestDiagnosticSettings | Tak | Tak |
> | guestDiagnosticSettingsAssociation | Tak | Tak |
> | logprofiles | Tak | Tak |
> | metricAlerts | Tak | Tak |
> | privateLinkScopes | Tak | Tak |
> | privateLinkScopes / privateEndpointConnections | Nie | Nie |
> | privateLinkScopes / scopedResources | Nie | Nie |
> | queryPacks | Tak | Tak |
> | queryPacks/queries | Nie | Nie |
> | scheduledQueryRules | Tak | Tak |
> | webtests | Tak | Tak |
> | skoroszyty | Tak | Tak |
> | workbooktemplates | Tak | Tak |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Nie | Nie |
> | diagnosticSettingsCategories | Nie | Nie |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | appTemplates | Nie | Nie |
> | IoTApps | Tak | Tak |

## <a name="microsoftiotsecurity"></a>Microsoft.IoTSecurity

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | defenderSettings | Nie | Nie |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Graph | Tak | Tak |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | deletedManagedHSMs | Nie | Nie |
> | deletedVaults | Nie | Nie |
> | hsmPools | Tak | Tak |
> | managedHSMs | Tak | Tak |
> | Sklepienia | Tak | Tak |
> | magazyny/accessPolicies | Nie | Nie |
> | vaults/eventGridFilters | Nie | Nie |
> | magazyny/klucze | Nie | Nie |
> | magazyny/klucze/wersje | Nie | Nie |
> | magazyny/wpisy tajne | Nie | Nie |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | connectedClusters | Tak | Tak |
> | zarejestrowaneSubskrypcje | Nie | Nie |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Rozszerzenia | Nie | Nie |
> | sourceControlConfigurations | Nie | Nie |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Klastrów | Tak | Tak |
> | klastry /attacheddatabaseconfigurations | Nie | Nie |
> | klastry/bazy danych | Nie | Nie |
> | klastry/bazy danych/połączenia danych | Nie | Nie |
> | klastry/bazy danych/eventhubconnections | Nie | Nie |
> | klastry/bazy danych/jednostkizapisy | Nie | Nie |
> | klastry/bazy danych/skrypty | Nie | Nie |
> | klastry/połączenia danych | Nie | Nie |
> | klastry /principalassignments | Nie | Nie |
> | klastry/współużytkowaniaidentyfikaty | Nie | Nie |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | konta laboratorium | Tak | Nie |
> | labplans | Tak | Tak |
> | Labs | Tak | Tak |
> | users | Nie | Nie |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | Tak | Tak |
> | integrationAccounts | Tak | Tak |
> | integrationServiceEnvironments | Tak | Tak |
> | integrationServiceEnvironments/managedApis | Nie | Nie |
> | isolatedEnvironments | Tak | Tak |
> | Przepływy pracy | Tak | Tak |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | Tak | Tak |
> | Webservices | Tak | Tak |
> | Obszary robocze | Tak | Tak |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | modelinventories | Tak | Tak |
> | virtualclusters | Tak | Tak |
> | obszary robocze | Tak | Tak |
> | obszary robocze/batchEndpoints | Tak | Tak |
> | obszary robocze/batchEndpoints/wdrożenia | Tak | Tak |
> | obszary robocze / batchEndpoints / wdrożenia / zadania | Nie | Nie |
> | obszary robocze/ batchEndpoints /jobs | Nie | Nie |
> | obszary robocze/kody | Nie | Nie |
> | obszary robocze/kody/wersje | Nie | Nie |
> | obszary robocze/obliczenia | Nie | Nie |
> | obszary robocze/dane | Nie | Nie |
> | obszary robocze/magazyny danych | Nie | Nie |
> | obszary robocze/środowiska | Nie | Nie |
> | workspaces/eventGridFilters | Nie | Nie |
> | obszary robocze/zadania | Nie | Nie |
> | obszary robocze/etykietowanieObs | Nie | Nie |
> | obszary robocze/połączoneusługi | Nie | Nie |
> | obszary robocze/modele | Nie | Nie |
> | obszary robocze/modele/wersje | Nie | Nie |
> | workspaces/onlineEndpoints | Tak | Tak |
> | obszary robocze/ onlinePunktyendów/wdrożenia | Tak | Tak |

> [!NOTE]
> Tagi obszaru roboczego nie są propagowane do klastrów obliczeniowych i wystąpień obliczeniowych.

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | applyUpdates | Nie | Nie |
> | configurationAssignments | Nie | Nie |
> | maintenanceConfigurations | Tak | Tak |
> | publicMaintenanceConfigurations | Nie | Nie |
> | aktualizacje | Nie | Nie |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Tożsamości | Nie | Nie |
> | userAssignedIdentities | Tak | Tak |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | managedNetworks | Tak | Tak |
> | managedNetworks/managedNetworkGroups | Tak | Tak |
> | managedNetworks / managedNetworkPeeringPolicies | Tak | Tak |
> | powiadomienie | Tak | Tak |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinitions | Nie | Nie |
> | registrationAssignments | Nie | Nie |
> | registrationDefinitions | Nie | Nie |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | getEntities | Nie | Nie |
> | managementGroups | Nie | Nie |
> | managementGroups /settings | Nie | Nie |
> | zasoby | Nie | Nie |
> | startTenantBackfill | Nie | Nie |
> | tenantBackfillStatus | Nie | Nie |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Konta | Tak | Tak |
> | konta/twórcy | Tak | Tak |
> | accounts/eventGridFilters | Nie | Nie |
> | accounts/privateAtlases | Tak | Tak |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | macc | Nie | Nie |
> | Oferuje | Nie | Nie |
> | offerTypes | Nie | Nie |
> | offerTypes/publishers | Nie | Nie |
> | offerTypes/publishers/offers | Nie | Nie |
> | offerTypes/publishers/offers/plans | Nie | Nie |
> | offerTypes/publishers/offers/plans/agreements | Nie | Nie |
> | offerTypes / publishers / offers / plans / configs | Nie | Nie |
> | offerTypes / publishers / offers / plans / configs / importImage | Nie | Nie |
> | privategalleryitems | Nie | Nie |
> | privateStoreClient | Nie | Nie |
> | privateStores | Nie | Nie |
> | privateStores / AdminRequestApprovals | Nie | Nie |
> | privateStores/offers | Nie | Nie |
> | privateStores/offers/acknowledgeNotification | Nie | Nie |
> | privateStores/queryNotificationsState | Nie | Nie |
> | privateStores / RequestApprovals | Nie | Nie |
> | privateStores / requestApprovals / query | Nie | Nie |
> | privateStores/requestApprovals/wyliczyPlan | Nie | Nie |
> | Produktów | Nie | Nie |
> | Wydawców | Nie | Nie |
> | wydawcy/oferty | Nie | Nie |
> | wydawcy/oferty/poprawki | Nie | Nie |
> | zarejestruj | Nie | Nie |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | classicDevServices | Tak | Tak |
> | updateCommunicationPreference | Nie | Nie |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Umów | Nie | Nie |
> | typy ofert | Nie | Nie |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | mediaservices | Tak | Tak |
> | mediaservices/accountFilters | Nie | Nie |
> | mediaservices/assets | Nie | Nie |
> | mediaservices / assets / assetFilters | Nie | Nie |
> | mediaservices / contentKeyPolicies | Nie | Nie |
> | mediaservices / eventGridFilters | Nie | Nie |
> | mediaservices/graphInstances | Nie | Nie |
> | mediaservices/graphTopologies | Nie | Nie |
> | mediaservices/ liveEventOperations | Nie | Nie |
> | mediaservices/liveEvents | Tak | Tak |
> | mediaservices / liveEvents / liveOutputs | Nie | Nie |
> | mediaservices / liveOutputOperations | Nie | Nie |
> | mediaservices/mediaGraphs | Nie | Nie |
> | mediaservices/privateEndpointConnectionOperations | Nie | Nie |
> | mediaservices/privateEndpointConnectionProxies | Nie | Nie |
> | mediaservices/privateEndpointConnections | Nie | Nie |
> | mediaservices/streamingEndpointOperations | Nie | Nie |
> | mediaservices/streamingEndpoints | Tak | Tak |
> | mediaservices/streamingLocators | Nie | Nie |
> | mediaservices/streamingPolicies | Nie | Nie |
> | mediaservices/transforms | Nie | Nie |
> | mediaservices/transforms/jobs | Nie | Nie |
> | videoAnalyzers | Tak | Tak |
> | videoAnalyzers /edgeModules | Nie | Nie |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | appClusters | Tak | Tak |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | assessmentProjects | Tak | Tak |
> | migrateprojects | Tak | Tak |
> | moveCollections | Tak | Tak |
> | Projektów | Tak | Tak |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastAccounts | Tak | Tak |
> | objectAnchorsAccounts | Tak | Tak |
> | objectUnderstandingAccounts | Tak | Tak |
> | remoteRenderingAccounts | Tak | Tak |
> | spatialAnchorsAccounts | Tak | Tak |

## <a name="microsoftmobilenetwork"></a>Microsoft.MobileNetwork

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Sieci | Tak | Tak |
> | sieci/lokacje | Tak | Tak |
> | packetCores | Tak | Tak |
> | The sims | Tak | Tak |
> | nie tylko/ simProfiles | Tak | Tak |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | Tak | Nie |
> | netAppAccounts / accountBackups | Nie | Nie |
> | netAppAccounts /capacityPools | Tak | Nie |
> | netAppAccounts / capacityPools / volumes | Tak | Nie |
> | netAppAccounts / capacityPools / woluminy / migawki | Nie | Nie |
> | netAppAccounts / volumeGroups | Nie | Nie |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | applicationGateways | Tak | Tak |
> | applicationGatewayWebApplicationFirewallPolicies | Tak | Tak |
> | applicationSecurityGroups | Tak | Tak |
> | azureFirewallFqdnTags | Nie | Nie |
> | azureFirewalls | Tak | Nie |
> | bastionHosts | Tak | Nie |
> | bgpServiceCommunities | Nie | Nie |
> | Połączenia | Tak | Tak |
> | ddosCustomPolicies | Tak | Tak |
> | ddosProtectionPlans | Tak | Tak |
> | dnsOperationStatuses | Nie | Nie |
> | dnszones | Tak | Tak |
> | dnszones / A | Nie | Nie |
> | dnszones / AAAA | Nie | Nie |
> | dnszones / wszystkie | Nie | Nie |
> | dnszones / CAA | Nie | Nie |
> | dnszones / CNAME | Nie | Nie |
> | dnszones / MX | Nie | Nie |
> | dnszones / NS | Nie | Nie |
> | dnszones / PTR | Nie | Nie |
> | dnszones/recordsets | Nie | Nie |
> | dnszones / SOA | Nie | Nie |
> | dnszones / SRV | Nie | Nie |
> | dnszones / TXT | Nie | Nie |
> | expressRouteCircuits | Tak | Tak |
> | expressRouteCrossConnections | Tak | Tak |
> | expressRouteGateways | Tak | Tak |
> | expressRoutePorts | Tak | Tak |
> | expressRouteServiceProviders | Nie | Nie |
> | firewallPolicies | Tak | Tak |
> | frontdoors (frontdoory) | Tak, ale ograniczone (patrz [uwaga poniżej](#frontdoor)) | Tak |
> | frontdoorWebApplicationFirewallManagedRuleSets | Tak, ale ograniczone (patrz [uwaga poniżej](#frontdoor)) | Nie |
> | frontdoorWebApplicationFirewallPolicies | Tak, ale ograniczone (patrz [uwaga poniżej](#frontdoor)) | Tak |
> | getDnsResourceReference | Nie | Nie |
> | internalNotify | Nie | Nie |
> | ipGroups | Tak | Tak |
> | loadBalancers | Tak | Tak |
> | localNetworkGateways | Tak | Tak |
> | natGateways | Tak | Tak |
> | networkIntentPolicies | Tak | Tak |
> | networkInterfaces | Tak | Tak |
> | networkProfiles | Tak | Tak |
> | networkSecurityGroups | Tak | Tak |
> | networkWatchers | Tak | Tak |
> | networkWatchers /connectionMonitors | Tak | Nie |
> | networkWatchers/flowLogs | Tak | Nie |
> | networkWatchers / lenses | Tak | Nie |
> | networkWatchers / pingMeshes | Tak | Nie |
> | p2sVpnGateways | Tak | Tak |
> | privateDnsOperationStatuses | Nie | Nie |
> | privateDnsZones | Tak | Tak |
> | privateDnsZones / A | Nie | Nie |
> | privateDnsZones / AAAA | Nie | Nie |
> | privateDnsZones / wszystkie | Nie | Nie |
> | privateDnsZones / CNAME | Nie | Nie |
> | privateDnsZones / MX | Nie | Nie |
> | privateDnsZones / PTR | Nie | Nie |
> | privateDnsZones / SOA | Nie | Nie |
> | privateDnsZones / SRV | Nie | Nie |
> | privateDnsZones / TXT | Nie | Nie |
> | privateDnsZones / virtualNetworkLinks | Tak | Tak |
> | privateEndpoints | Tak | Tak |
> | privateLinkServices | Tak | Tak |
> | publicIPAddresses | Tak | Tak |
> | publicIPPrefixes | Tak | Tak |
> | routeFilters | Tak | Tak |
> | routeTables | Tak | Tak |
> | serviceEndpointPolicies | Tak | Tak |
> | trafficManagerGeographicHierarchies | Nie | Nie |
> | trafficmanagerprofiles | Tak | Tak |
> | trafficmanagerprofiles/heatMaps | Nie | Nie |
> | trafficManagerUserMetricsKeys | Nie | Nie |
> | virtualHubs | Tak | Tak |
> | virtualNetworkGateways | Tak | Tak |
> | virtualNetworks | Tak | Tak |
> | virtualNetworks/podsieci | Nie | Nie |
> | virtualNetworkTaps | Tak | Tak |
> | virtualWans | Tak | Nie |
> | vpnGateways | Tak | Tak |
> | vpnSites | Tak | Tak |
> | webApplicationFirewallPolicies | Tak | Tak |

<a id="frontdoor"></a>

> [!NOTE]
> Na Azure Front Door Service tagów można stosować podczas tworzenia zasobu, ale aktualizowanie lub dodawanie tagów nie jest obecnie obsługiwane.


## <a name="microsoftnotebooks"></a>Microsoft.Notebooks

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | NotebookProxies | Nie | Nie |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Obszary nazw | Tak | Nie |
> | namespaces/notificationHubs | Tak | Nie |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | osNamespaces | Tak | Tak |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | HyperVSites | Tak | Tak |
> | ImportSites | Tak | Tak |
> | MasterSites | Tak | Tak |
> | SerwerWitryny | Tak | Tak |
> | VMwareSites | Tak | Tak |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Klastrów | Tak | Tak |
> | deletedWorkspaces | Nie | Nie |
> | linkTargets | Nie | Nie |
> | querypacks | Tak | Tak |
> | storageInsightConfigs | Nie | Nie |
> | obszary robocze | Tak | Tak |
> | obszary robocze/daneEkporty | Nie | Nie |
> | obszary robocze/źródła danych | Nie | Nie |
> | obszary robocze/połączoneusługi | Nie | Nie |
> | workspaces/linkedStorageAccounts | Nie | Nie |
> | obszary robocze/metadane | Nie | Nie |
> | obszary robocze/zapytanie | Nie | Nie |
> | workspaces/scopedPrivateLinkProxies | Nie | Nie |
> | workspaces/storageInsightConfigs | Nie | Nie |
> | obszary robocze/tabele | Nie | Nie |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | skojarzenia zarządzania | Nie | Nie |
> | managementconfigurations (konfiguracje zarządzania) | Tak | Tak |
> | rozwiązania | Tak | Tak |
> | widoki | Tak | Tak |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | cdnPeeringPrefixes | Nie | Nie |
> | Starsza wersja | Nie | Nie |
> | peerAsns | Nie | Nie |
> | komunikacja równorzędna | Tak | Tak |
> | peeringServiceCountries | Nie | Nie |
> | peeringServiceProviders | Nie | Nie |
> | peeringServices (usługi komunikacji równorzędnej) | Tak | Tak |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Atesty | Nie | Nie |
> | eventGridFilters | Nie | Nie |
> | policyEvents | Nie | Nie |
> | policyMetadata | Nie | Nie |
> | policyStates | Nie | Nie |
> | policyTrackedResources | Nie | Nie |
> | korygowanie | Nie | Nie |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Konsole programu | Nie | Nie |
> | pulpity nawigacyjne | Tak | Tak |
> | konfiguracje dzierżawy | Nie | Nie |
> | userSettings | Nie | Nie |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | privateLinkServicesForPowerBI | Tak | Tak |
> | Najemców | Tak | Tak |
> | dzierżawy/obszary robocze | Nie | Nie |
> | workspaceCollections | Tak | Tak |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | autoScaleVCores | Tak | Tak |
> | Pojemności | Tak | Tak |

## <a name="microsoftpowerplatform"></a>Microsoft.PowerPlatform

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | enterprisePolicies | Tak | Tak |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Konta | Tak | Tak |
> | deletedAccounts | Nie | Nie |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | Nie | Nie |
> | providerRegistrations/ customRollouts | Nie | Nie |
> | providerRegistrations / defaultRollouts | Nie | Nie |
> | providerRegistrations / resourceTypeRegistrations | Nie | Nie |

## <a name="microsoftpurview"></a>Microsoft.Purview

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Konta | Tak | Tak |
> | deletedAccounts | Nie | Nie |
> | getDefaultAccount | Nie | Nie |
> | removeDefaultAccount | Nie | Nie |
> | setDefaultAccount | Nie | Nie |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Obszary robocze | Tak | Tak |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | Nie | Nie |
> | Sklepienia | Tak | Tak |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | OpenShiftClusters | Tak | Tak |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Obszary nazw | Tak | Tak |
> | przestrzenie nazw/reguły autoryzacji | Nie | Nie |
> | przestrzenie nazw/połączenia hybrydowe | Nie | Nie |
> | namespaces/hybridconnections/authorizationrules | Nie | Nie |
> | namespaces/privateEndpointConnections | Nie | Nie |
> | namespaces / wcfrelays | Nie | Nie |
> | namespaces / wcfrelays / authorizationrules | Nie | Nie |

## <a name="microsoftresourceconnector"></a>Microsoft.ResourceConnector

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Urządzeń | Tak | Tak |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Kwerendy | Tak | Tak |
> | resourceChangeDetails | Nie | Nie |
> | resourceChanges | Nie | Nie |
> | zasoby | Nie | Nie |
> | resourcesHistory | Nie | Nie |
> | subscriptionsStatus | Nie | Nie |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | Nie | Nie |
> | childAvailabilityStatuses | Nie | Nie |
> | childResources | Nie | Nie |
> | emergingissues | Nie | Nie |
> | zdarzenia | Nie | Nie |
> | impactedResources | Nie | Nie |
> | metadane | Nie | Nie |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Wdrożeń | Tak | Nie |
> | wdrożenia/operacje | Nie | Nie |
> | deploymentScripts | Tak | Tak |
> | deploymentScripts/logs | Nie | Nie |
> | Linki | Nie | Nie |
> | dostawców | Nie | Nie |
> | resourceGroups | Tak | Nie |
> | Subskrypcji | Tak | Nie |
> | templateSpecs | Tak | Tak |
> | templateSpecs/versions | Tak | Tak |
> | Najemców | Nie | Nie |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | aplikacje | Tak | Tak |
> | zasoby | Tak | Tak |
> | saasresources | Nie | Nie |

## <a name="microsoftscvmm"></a>Microsoft.ScVmm

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Chmury | Tak | Tak |
> | VirtualMachines | Tak | Tak |
> | VirtualMachineTemplates | Tak | Tak |
> | VirtualNetworks | Tak | Tak |
> | vmmservers | Tak | Tak |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | Nie | Nie |
> | searchServices | Tak | Tak |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | Nie | Nie |
> | advancedThreatProtectionSettings | Nie | Nie |
> | alerts | Nie | Nie |
> | alertsSuppressionRules | Nie | Nie |
> | allowedConnections | Nie | Nie |
> | applicationWhitelistings | Nie | Nie |
> | assessmentMetadata | Nie | Nie |
> | oceny | Nie | Nie |
> | autoDismissAlertsRules | Nie | Nie |
> | Automatyki | Tak | Tak |
> | AutoProvisioningSettings | Nie | Nie |
> | Zgodność | Nie | Nie |
> | konta integracji | Nie | Nie |
> | dataCollectionAgents | Nie | Nie |
> | devices | Nie | Nie |
> | deviceSecurityGroups | Nie | Nie |
> | discoveredSecuritySolutions | Nie | Nie |
> | externalSecuritySolutions | Nie | Nie |
> | InformationProtectionPolicies | Nie | Nie |
> | ingestionSettings | Nie | Nie |
> | Spostrzeżenia | Nie | Nie |
> | iotAlerts | Nie | Nie |
> | iotAlertTypes | Nie | Nie |
> | iotDefenderSettings | Nie | Nie |
> | iotRecommendations | Nie | Nie |
> | iotRecommendationTypes | Nie | Nie |
> | iotSecuritySolutions | Tak | Tak |
> | iotSecuritySolutions/analyticsModels | Nie | Nie |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | Nie | Nie |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | Nie | Nie |
> | iotSecuritySolutions / iotAlerts | Nie | Nie |
> | iotSecuritySolutions / iotAlertTypes | Nie | Nie |
> | iotSecuritySolutions / iotRecommendations | Nie | Nie |
> | iotSecuritySolutions / iotRecommendationTypes | Nie | Nie |
> | iotSensors | Nie | Nie |
> | iotSites | Nie | Nie |
> | jitNetworkAccessPolicies | Nie | Nie |
> | jitPolicies | Nie | Nie |
> | onPremiseIotSensors | Nie | Nie |
> | policies | Nie | Nie |
> | cennik | Nie | Nie |
> | regulatoryComplianceStandards | Nie | Nie |
> | regulatoryComplianceStandards/regulatoryComplianceControls | Nie | Nie |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | Nie | Nie |
> | secureScoreControlDefinitions | Nie | Nie |
> | secureScoreControls | Nie | Nie |
> | secureScores | Nie | Nie |
> | secureScores/ secureScoreControls | Nie | Nie |
> | securityContacts | Nie | Nie |
> | securitySolutions (rozwiązania zabezpieczeń) | Nie | Nie |
> | securitySolutionsReferenceData | Nie | Nie |
> | securityStatuses | Nie | Nie |
> | securityStatusesSummaries | Nie | Nie |
> | serverVulnerabilityAssessments | Nie | Nie |
> | ustawienia | Nie | Nie |
> | sqlVulnerabilityAssessments | Nie | Nie |
> | podoceny | Nie | Nie |
> | zadania | Nie | Nie |
> | Topologii | Nie | Nie |
> | workspaceSettings | Nie | Nie |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Nie | Nie |
> | diagnosticSettingsCategories | Nie | Nie |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Agregacji | Nie | Nie |
> | alertRules | Nie | Nie |
> | alertRuleTemplates | Nie | Nie |
> | automationRules | Nie | Nie |
> | zakładki | Nie | Nie |
> | Przypadkach | Nie | Nie |
> | dataConnectors (połączenia danych) | Nie | Nie |
> | dataConnectorsCheckRequirements | Nie | Nie |
> | Wzbogacania | Nie | Nie |
> | Podmioty | Nie | Nie |
> | entityQueries | Nie | Nie |
> | entityQueryTemplates | Nie | Nie |
> | zdarzenia | Nie | Nie |
> | officeConsents | Nie | Nie |
> | ustawienia | Nie | Nie |
> | threatIntelligence | Nie | Nie |
> | listy do obejrzenia | Nie | Nie |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | consoleServices | Nie | Nie |
> | serialPorts | Nie | Nie |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Obszary nazw | Tak | Tak |
> | przestrzenie nazw/reguły autoryzacji | Nie | Nie |
> | namespaces/disasterrecoveryconfigs | Nie | Nie |
> | namespaces/eventgridfilters | Nie | Nie |
> | namespaces/networkrulesets | Nie | Nie |
> | namespaces/privateEndpointConnections | Nie | Nie |
> | przestrzenie nazw/kolejki | Nie | Nie |
> | przestrzenie nazw/kolejki/reguły autoryzacji | Nie | Nie |
> | przestrzenie nazw/tematy | Nie | Nie |
> | przestrzenie nazw / tematy / reguły autoryzacji | Nie | Nie |
> | przestrzenie nazw/tematy/subskrypcje | Nie | Nie |
> | przestrzenie nazw / tematy / subskrypcje / reguły | Nie | Nie |
> | premiumMessagingRegions | Nie | Nie |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | aplikacje | Tak | Tak |
> | Klastrów | Tak | Tak |
> | klastry/aplikacje | Nie | Nie |
> | containerGroups | Tak | Tak |
> | containerGroupSets | Tak | Tak |
> | edgeclusters | Tak | Tak |
> | edgeclusters / aplikacje | Nie | Nie |
> | managedclusters | Tak | Tak |
> | managedclusters /applications | Nie | Nie |
> | managedclusters / aplikacje / usługi | Nie | Nie |
> | managedclusters / applicationTypes | Nie | Nie |
> | managedclusters / applicationTypes / versions | Nie | Nie |
> | managedclusters /nodetypes | Nie | Nie |
> | Sieci | Tak | Tak |
> | secretstores | Tak | Tak |
> | secretstores/certificates | Nie | Nie |
> | secretstores/secrets | Nie | Nie |
> | volumes | Tak | Tak |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | aplikacje | Tak | Tak |
> | containerGroups | Tak | Tak |
> | Bramy | Tak | Tak |
> | Sieci | Tak | Tak |
> | wpisy tajne | Tak | Tak |
> | volumes | Tak | Tak |

## <a name="microsoftservicelinker"></a>Microsoft.ServiceLinker

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Linkers | Nie | Nie |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | Nie | Nie |
> | providerRegistrations / resourceTypeRegistrations | Nie | Nie |
> | Wdrożenia | Tak | Tak |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | SignalR | Tak | Tak |
> | SignalR/eventGridFilters | Nie | Nie |
> | WebPubSub | Tak | Tak |

## <a name="microsoftsingularity"></a>Microsoft.Singularity

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Konta | Tak | Tak |
> | accounts/accountQuotaPolicies | Nie | Nie |
> | accounts/groupPolicies | Nie | Nie |
> | konta/zadania | Nie | Nie |
> | accounts/storageContainers | Nie | Nie |
> | images | Nie | Nie |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | Nie | Nie |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | Tak | Tak |
> | aplikacje | Tak | Tak |
> | jitRequests | Tak | Tak |


## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | longtermRetentionManagedInstance / longtermRetentionDatabase / longtermRetentionBackup | Nie | Nie |
> | longtermRetentionServer / longtermRetentionDatabase / longtermRetentionBackup | Nie | Nie |
> | managedInstances | Tak | Tak |
> | managedInstances /databases | Nie | Nie |
> | managedInstances / bazy danych / backupShortTermRetentionPolicies | Nie | Nie |
> | managedInstances / bazy danych / schematy / tabele / kolumny / sensitivityLabels | Nie | Nie |
> | managedInstances /databases/vulnerabilityAssessments | Nie | Nie |
> | managedInstances / bazy danych / vulnerabilityAssessments / rules / baselines | Nie | Nie |
> | managedInstances /encryptionProtector | Nie | Nie |
> | managedInstances / keys | Nie | Nie |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | Nie | Nie |
> | managedInstances/vulnerabilityAssessments | Nie | Nie |
> | Serwerów | Tak | Tak |
> | serwery/administratorzy | Nie | Nie |
> | serwery/komunikacjaLinki | Nie | Nie |
> | serwery/bazy danych | Tak (patrz [uwaga poniżej](#sqlnote)) | Tak |
> | serwery/encryptionProtector | Nie | Nie |
> | servers/firewallRules | Nie | Nie |
> | serwery/klucze | Nie | Nie |
> | servers/restorableDroppedDatabases | Nie | Nie |
> | servers/serviceobjectives | Nie | Nie |
> | servers/tdeCertificates | Nie | Nie |
> | virtualClusters | Tak | Tak |

<a id="sqlnote"></a>

> [!NOTE]
> Baza danych master nie obsługuje tagów, ale inne bazy danych, w tym Azure Synapse Analytics baz danych, obsługują tagi. Azure Synapse Analytics bazy danych muszą być w stanie Aktywne (nie Wstrzymane).

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | Tak | Tak |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Nie | Nie |
> | SqlVirtualMachines | Tak | Tak |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | deletedAccounts | Nie | Nie |
> | konta magazynu | Tak | Tak |
> | storageAccounts /blobServices | Nie | Nie |
> | storageAccounts / fileServices | Nie | Nie |
> | storageAccounts /queueServices | Nie | Nie |
> | storageAccounts /services | Nie | Nie |
> | storageAccounts / services / metricDefinitions | Nie | Nie |
> | storageAccounts / tableServices | Nie | Nie |
> | Zwyczaje | Nie | Nie |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | amlFilesystems | Tak | Tak |
> | Buforuje | Tak | Tak |
> | pamięci podręczne/storageTargets | Nie | Nie |
> | usageModels | Nie | Nie |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | replicationGroups | Nie | Nie |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Tak | Tak |
> | storageSyncServices/registeredServers | Nie | Nie |
> | storageSyncServices/syncGroups | Nie | Nie |
> | storageSyncServices / syncGroups / cloudEndpoints | Nie | Nie |
> | storageSyncServices / syncGroups / serverEndpoints | Nie | Nie |
> | storageSyncServices/workflows | Nie | Nie |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Tak | Tak |
> | storageSyncServices/registeredServers | Nie | Nie |
> | storageSyncServices/syncGroups | Nie | Nie |
> | storageSyncServices / syncGroups / cloudEndpoints | Nie | Nie |
> | storageSyncServices / syncGroups / serverEndpoints | Nie | Nie |
> | storageSyncServices/workflows | Nie | Nie |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Tak | Tak |
> | storageSyncServices / registeredServers | Nie | Nie |
> | storageSyncServices/syncGroups | Nie | Nie |
> | storageSyncServices/syncGroups/cloudEndpoints | Nie | Nie |
> | storageSyncServices / syncGroups / serverEndpoints | Nie | Nie |
> | storageSyncServices/workflows | Nie | Nie |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Menedżerów | Tak | Tak |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Klastrów | Tak | Tak |
> | klastry/prywatnepunktyendowe | Nie | Nie |
> | streamingjobs | Tak (patrz uwaga poniżej) | Tak |

> [!NOTE]
> Nie można dodać tagu, gdy działa streamingjobs. Zatrzymaj zasób, aby dodać tag.

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | acceptChangeTenant | Nie | Nie |
> | acceptOwnership | Nie | Nie |
> | acceptOwnershipStatus | Nie | Nie |
> | Aliasy | Nie | Nie |
> | Anuluj | Nie | Nie |
> | changeTenantRequest | Nie | Nie |
> | changeTenantStatus | Nie | Nie |
> | Createsubscription | Nie | Nie |
> | Włącz | Nie | Nie |
> | policies | Nie | Nie |
> | zmień nazwę | Nie | Nie |
> | SubscriptionDefinitions | Nie | Nie |
> | SubscriptionOperations | Nie | Nie |
> | Subskrypcji | Nie | Nie |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | privateLinkHubs | Tak | Tak |
> | obszary robocze | Tak | Tak |
> | workspaces/bigDataPools | Tak | Tak |
> | workspaces/operationStatuses | Nie | Nie |
> | obszary robocze/sqlDatabases | Tak | Tak |
> | obszary robocze/sqlPools | Tak | Tak |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Środowiskach | Tak | Nie |
> | środowiska/accessPolicies | Nie | Nie |
> | środowiska/źródła zdarzeń | Tak | Nie |
> | środowiska/privateEndpointConnectionProxies | Nie | Nie |
> | environments/privateEndpointConnections | Nie | Nie |
> | środowiska/privateLinkResources | Nie | Nie |
> | środowiska/referenceDataSets | Tak | Nie |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Sklepy | Tak | Tak |
> | stores/accessPolicies | Nie | Nie |
> | magazyny/usługi | Nie | Nie |
> | magazyny/usługi/tokeny | Nie | Nie |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | imageTemplates | Tak | Tak |
> | imageTemplates/runOutputs | Nie | Nie |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Strefy arczone | Tak | Tak |
> | ResourcePools | Tak | Tak |
> | VCenters | Tak | Tak |
> | VCenters / InventoryItems | Nie | Nie |
> | virtualmachines | Tak | Tak |
> | VirtualMachineTemplates | Tak | Tak |
> | VirtualNetworks | Tak | Tak |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | dedicatedCloudNodes | Tak | Tak |
> | dedicatedCloudServices | Tak | Tak |
> | virtualMachines (maszyny wirtualne) | Tak | Tak |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | devices | Tak | Tak |
> | registeredSubscriptions | Nie | Nie |
> | Dostawców | Nie | Nie |
> | dostawcy/jednostki SKU | Nie | Nie |
> | dostawcy/vnfs | Nie | Nie |
> | virtualNetworkFunctionSkus | Nie | Nie |
> | vnfs | Tak | Tak |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Konta | Tak | Nie |
> | Plany | Tak | Nie |
> | registeredSubscriptions | Nie | Nie |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | Nie | Nie |
> | apiManagementAccounts /apiAcls | Nie | Nie |
> | apiManagementAccounts/apis | Nie | Nie |
> | apiManagementAccounts / apis / apiAcls | Nie | Nie |
> | apiManagementAccounts / apis / connectionAcls | Nie | Nie |
> | apiManagementAccounts /apis/connections | Nie | Nie |
> | apiManagementAccounts / apis / connections / connectionAcls | Nie | Nie |
> | apiManagementAccounts / apis / localizedDefinitions | Nie | Nie |
> | apiManagementAccounts/connectionAcls | Nie | Nie |
> | apiManagementAccounts/connections | Nie | Nie |
> | billingMeters | Nie | Nie |
> | certyfikaty | Tak | Tak |
> | connectionGateways | Tak | Tak |
> | Połączenia | Tak | Tak |
> | customApis | Tak | Tak |
> | deletedSites | Nie | Nie |
> | functionAppStacks | Nie | Nie |
> | generateGithubAccessTokenForAppserviceCLI | Nie | Nie |
> | hostingEnvironments | Tak | Tak |
> | hostingEnvironments /eventGridFilters | Nie | Nie |
> | hostingEnvironments/multiRolePools | Nie | Nie |
> | hostingEnvironments/workerPools | Nie | Nie |
> | kubeEnvironments | Tak | Tak |
> | publishingUsers | Nie | Nie |
> | zalecenia | Nie | Nie |
> | resourceHealthMetadata | Nie | Nie |
> | środowiska uruchomieniowe | Nie | Nie |
> | serverFarms | Tak | Tak |
> | serverFarms / eventGridFilters | Nie | Nie |
> | serverFarms / firstPartyApps | Nie | Nie |
> | serverFarms / firstPartyApps / keyVaultSettings | Nie | Nie |
> | lokacje | Tak | Tak |
> | witryny/konfiguracja  | Nie | Nie |
> | sites/eventGridFilters | Nie | Nie |
> | sites/hostNameBindings | Nie | Nie |
> | sites/networkConfig | Nie | Nie |
> | sites/premieraddons | Tak | Tak |
> | lokacje/miejsca | Tak | Tak |
> | sites/ slots / eventGridFilters | Nie | Nie |
> | sites / slots / hostNameBindings | Nie | Nie |
> | sites/slots/networkConfig | Nie | Nie |
> | sourceControls | Nie | Nie |
> | staticSites | Tak | Tak |
> | Sprawdzania poprawności | Nie | Nie |
> | verifyHostingEnvironmentVnet | Nie | Nie |
> | WebAppStacks | Nie | Nie |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Nie | Nie |
> | diagnosticSettingsCategories | Nie | Nie |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | multipleActivationKeys | Tak | Tak |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | DeviceServices | Tak | Tak |

## <a name="microsoftworkloadbuilder"></a>Microsoft.WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | migrationAgents | Tak | Tak |
> | Obciążeń | Tak | Tak |
> | obciążenia/wystąpienia | Nie | Nie |
> | obciążenia/wersje | Nie | Nie |
> | obciążenia/wersje/artefakty | Nie | Nie |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje tagi | Tagowanie w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Monitory | Nie | Nie |

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak stosować tagi do zasobów, zobacz Organizowanie zasobów [platformy Azure przy użyciu tagów.](tag-resources.md)
