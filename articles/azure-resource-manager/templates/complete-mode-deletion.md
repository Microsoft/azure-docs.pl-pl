---
title: Usuwanie w trybie pełnym
description: Pokazuje, jak typy zasobów obsługują usuwanie w trybie Azure Resource Manager szablonów.
ms.topic: conceptual
ms.date: 04/16/2021
ms.openlocfilehash: 6986f600274beaaa67f2f6ce64cbc3d0ceaf322e
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576070"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Usuwanie zasobów platformy Azure dla wdrożeń w trybie kompletnym

W tym artykule opisano, jak typy zasobów obsługują usuwanie, gdy nie są w szablonie wdrożonym w trybie kompletnym.

Typy zasobów oznaczone znakiem **Tak** są usuwane, gdy typ nie znajduje się w szablonie wdrożonym w trybie kompletnym.

Typy zasobów oznaczone za **pomocą nie** są automatycznie usuwane, gdy nie są w szablonie; zostaną jednak usunięte, jeśli zasób nadrzędny zostanie usunięty. Aby uzyskać pełny opis zachowania, zobacz [Azure Resource Manager wdrożenia.](deployment-modes.md)

W przypadku wdrożenia [w więcej niż jednej grupie zasobów w szablonie](./deploy-to-resource-group.md)zasoby w grupie zasobów określonej w operacji wdrażania kwalifikują się do usunięcia. Zasoby w dodatkowych grupach zasobów nie są usuwane.

Zasoby są wyświetlane według przestrzeni nazw dostawcy zasobów. Aby dopasować przestrzeń nazw dostawcy zasobów do jej nazwy usługi platformy Azure, zobacz [Dostawcy zasobów dla usług platformy Azure.](../management/azure-services-resource-providers.md)

> [!NOTE]
> Zawsze używaj operacji [what-if przed](template-deploy-what-if.md) wdrożeniem szablonu w trybie kompletnym. What-if pokazuje, które zasoby zostaną utworzone, usunięte lub zmodyfikowane. Użyj funkcji what-if, aby uniknąć niezamierzonego usunięcia zasobów.

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
> - [Microsoft.Zygmej](#microsoftfalcon)
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
> - [Microsoft.Przejmij](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
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
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | DomainServices | Yes |
> | DomainServices /oucontainer | Nie |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | supportProviders | Nie |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | aadsupportcases | Nie |
> | addsservices | Nie |
> | Agentów | Nie |
> | anonymousapiusers | Nie |
> | konfiguracja | Nie |
> | dzienniki | Nie |
> | raporty | Nie |
> | servicehealthmetrics | Nie |
> | services | Nie |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | advisorScore | Nie |
> | Konfiguracji | Nie |
> | generateRecommendations | Nie |
> | metadane | Nie |
> | zalecenia | Nie |
> | Suppressions | Nie |

## <a name="microsoftagfoodplatform"></a>Microsoft.Ag IchPlatforma

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | farmBeats | Yes |
> | farmBeats / eventGridFilters | Nie |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | actionRules (akcje) | Yes |
> | alerts | Nie |
> | alertsList (lista alertów) | Nie |
> | alertsMetaData | Nie |
> | alertsSummary | Nie |
> | alertsSummaryList | Nie |
> | migrateFromSmartDetection | Nie |
> | resourceHealthAlertRules | Yes |
> | smartDetectorAlertRules | Yes |
> | smartGroups | Nie |

## <a name="microsoftanalysisservices"></a>Microsoft.analysisservices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Serwerów | Yes |

## <a name="microsoftanybuild"></a>Microsoft.AnyBuild

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Klastrów | Yes |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | deletedServices | Nie |
> | getDomainOwnershipIdentifier | Nie |
> | reportFeedback | Nie |
> | usługa | Yes |
> | validateServiceName | Nie |

## <a name="microsoftappassessment"></a>Microsoft.AppAssessment

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | migrateProjects | Yes |
> | migrateProjects/assessments | Nie |
> | migrateProjects/assessments/assessedApplications | Nie |
> | migrateProjects/assessments/assessedApplications/machines | Nie |
> | migrateProjects /assessments/assessedMachines | Nie |
> | migrateProjects / assessments / assessedMachines / applications | Nie |
> | migrateProjects / assessments / machinesToAssess | Nie |
> | migrateProjects/sites | Nie |
> | migrateProjects /sites/applianceConfigurations | Nie |
> | migrateProjects/sites/machines | Nie |
> | osVersions | Nie |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | configurationStores (magazyny konfiguracji) | Yes |
> | configurationStores / eventGridFilters | Nie |
> | configurationStores/keyValues | Nie |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Spring | Yes |
> | Spring/apps | Nie |
> | Spring/apps/deployments | Nie |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | attestationProviders | Yes |
> | defaultProviders | Nie |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | accessReviewScheduleDefinitions | Nie |
> | accessReviewScheduleSettings | Nie |
> | classicAdministratorzy | Nie |
> | dataAliases | Nie |
> | dataPolicyManifests | Nie |
> | denyAssignments | Nie |
> | elevateAccess | Nie |
> | findOrphanRoleAssignments | Nie |
> | Blokad | Nie |
> | uprawnienia | Nie |
> | policyAssignments (przypisania zasad) | Nie |
> | Policydefinitions | Nie |
> | policyExemptions | Nie |
> | policySetDefinitions | Nie |
> | privateLinkAssociations | Nie |
> | providerOperations | Nie |
> | resourceManagementPrivateLinks | Yes |
> | roleAssignmentApprovals | Nie |
> | roleAssignments | Nie |
> | roleAssignmentScheduleInstances | Nie |
> | roleAssignmentScheduleRequests | Nie |
> | roleAssignmentSchedules | Nie |
> | roleAssignmentsUsageMetrics | Nie |
> | roleDefinitions | Nie |
> | roleEligibilityScheduleInstances | Nie |
> | roleEligibilityScheduleRequests | Nie |
> | roleEligibilitySchedules | Nie |
> | roleManagementPolicies | Nie |
> | roleManagementPolicyAssignments | Nie |

## <a name="microsoftautomanage"></a>Microsoft.Automanage

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Konta | Yes |
> | configurationProfileAssignments | Nie |
> | configurationProfilePreferences | Yes |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | automationAccounts | Yes |
> | automationAccounts /configurations | Yes |
> | automationAccounts / jobs | Nie |
> | automationAccounts / privateEndpointConnectionProxies | Nie |
> | automationAccounts / privateEndpointConnections | Nie |
> | automationAccounts / privateLinkResources | Nie |
> | automationAccounts / runbook | Yes |
> | automationAccounts / softwareUpdateConfigurations | Nie |
> | automationAccounts /webhook | Nie |

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | privateClouds | Yes |
> | privateClouds/ dodatki | Nie |
> | privateClouds / autoryzacje | Nie |
> | privateClouds/cloudLinks | Nie |
> | privateClouds/clusters | Nie |
> | privateClouds / klastry / magazyny danych | Nie |
> | privateClouds/globalReachConnections | Nie |
> | privateClouds / hcxEnterpriseSites | Nie |
> | privateClouds/scriptExecutions | Nie |
> | privateClouds/scriptPackages | Nie |
> | privateClouds / scriptPackages / scriptCmdlets | Nie |
> | privateClouds/workloadNetworks | Nie |
> | privateClouds / workloadNetworks / dhcpConfigurations | Nie |
> | privateClouds / workloadNetworks / dnsServices | Nie |
> | privateClouds / workloadNetworks / dnsZones | Nie |
> | privateClouds / workloadNetworks / gateways | Nie |
> | privateClouds / workloadNetworks / portMirroringProfiles | Nie |
> | privateClouds / workloadNetworks / segmenty | Nie |
> | privateClouds / workloadNetworks / virtualMachines | Nie |
> | privateClouds / workloadNetworks / vmGroups | Nie |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Środowiskach | Nie |
> | środowiska/konta | Nie |
> | środowiska/ konta / przestrzenie nazw | Nie |
> | środowiska/ konta / przestrzenie nazw / konfiguracje | Nie |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | b2cDirectories | Yes |
> | b2ctenants (usługi b2ctenants) | Nie |
> | GuestUsages | Yes |

## <a name="microsoftazurearcdata"></a>Microsoft.AzureArcData

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | dataControllers | Yes |
> | dataWarehouseInstances | Yes |
> | postgresInstances | Yes |
> | sqlManagedInstances | Yes |
> | sqlServerInstances | Yes |

## <a name="microsoftazurecis"></a>Microsoft.AzureCIS

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | autopilotŚrodowisko | Yes |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Sqlserverregistrations | Yes |
> | sqlServerRegistrations / sqlServers | Nie |

## <a name="microsoftazuresphere"></a>Microsoft.AzureSphere

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Katalogi | Yes |
> | katalogi/produkty | Yes |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | cloudManifestFiles | Nie |
> | edgeSubscriptions | Yes |
> | linkedSubscriptions | Yes |
> | Rejestracji | Yes |
> | rejestracje/klienciSubskrypcje | Nie |
> | rejestracje/produkty | Nie |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Klastrów | Yes |
> | galleryImages | Yes |
> | networkInterfaces | Yes |
> | virtualHardDisks | Yes |
> | virtualMachines | Yes |
> | virtualNetworks | Yes |

## <a name="microsoftbaremetalinfrastructure"></a>Microsoft.BareMetalInfrastructure

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | bareMetalInstances | Yes |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | batchAccounts | Yes |
> | batchAccounts /certificates | Nie |
> | batchAccounts /pools | Nie |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | billingAccounts (konta rozliczeniowe) | Nie |
> | billingAccounts /agreements | Nie |
> | billingAccounts /billingPermissions | Nie |
> | billingAccounts /billingProfiles | Nie |
> | billingAccounts / billingProfiles / billingPermissions | Nie |
> | billingAccounts / billingProfiles / billingRoleAssignments | Nie |
> | billingAccounts / billingProfiles / billingRoleDefinitions | Nie |
> | billingAccounts / billingProfiles / billingSubscriptions | Nie |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | Nie |
> | billingAccounts / billingProfiles / customers | Nie |
> | billingAccounts / billingProfiles / instrukcje | Nie |
> | billingAccounts / billingProfiles / invoices | Nie |
> | billingAccounts / billingProfiles / invoices / pricesheet | Nie |
> | billingAccounts / billingProfiles / invoices / transactions | Nie |
> | billingAccounts / billingProfiles / invoiceSections | Nie |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | Nie |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | Nie |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | Nie |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | Nie |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | Nie |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | Nie |
> | billingAccounts / billingProfiles / invoiceSections / products | Nie |
> | billingAccounts / billingProfiles / invoiceSections / products / transfer | Nie |
> | billingAccounts / billingProfiles / invoiceSections / products / updateAutoRenew | Nie |
> | billingAccounts / billingProfiles / invoiceSections / transactions | Nie |
> | billingAccounts/billingProfiles/invoiceSections/transfers | Nie |
> | billingAccounts / billingProfiles / invoiceSections / validateDeleteInvoiceSectionEligibility | Nie |
> | billingAccounts / BillingProfiles / patchOperations | Nie |
> | billingAccounts / billingProfiles / paymentMethods | Nie |
> | billingAccounts/billingProfiles/policies | Nie |
> | billingAccounts/billingProfiles/pricesheet | Nie |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | Nie |
> | billingAccounts / billingProfiles / products | Nie |
> | billingAccounts/billingProfiles/reservations | Nie |
> | billingAccounts/billingProfiles/transactions | Nie |
> | billingAccounts / billingProfiles / validateDeleteBillingProfileEligibility | Nie |
> | billingAccounts / billingProfiles / validateDetachPaymentMethodEligibility (Uprawnienia do rozliczeń/rozliczeńProfile/ validateDetachPaymentMethod) | Nie |
> | billingAccounts/billingRoleAssignments | Nie |
> | billingAccounts / billingRoleDefinitions | Nie |
> | billingAccounts/billingSubscriptions | Nie |
> | billingAccounts / billingSubscriptions / elevateRole | Nie |
> | billingAccounts/billingSubscriptions/invoices | Nie |
> | billingAccounts / createBillingRoleAssignment | Nie |
> | billingAccounts / createInvoiceSectionOperations | Nie |
> | billingAccounts /customers | Nie |
> | billingAccounts / customers / billingPermissions | Nie |
> | billingAccounts / customers / billingSubscriptions | Nie |
> | billingAccounts / customers / initiateTransfer | Nie |
> | billingAccounts / customers / policies | Nie |
> | billingAccounts / customers / products | Nie |
> | billingAccounts / customers / transactions | Nie |
> | billingAccounts /customers/transfers | Nie |
> | billingAccounts /departments | Nie |
> | billingAccounts /departments/billingPermissions | Nie |
> | billingAccounts / działy / billingRoleAssignments | Nie |
> | billingAccounts / działy / billingRoleDefinitions | Nie |
> | billingAccounts / działy / rozliczeniaSubskrypcje | Nie |
> | billingAccounts / enrollmentAccounts | Nie |
> | billingAccounts / enrollmentAccounts / billingPermissions | Nie |
> | billingAccounts / enrollmentAccounts / billingRoleAssignments | Nie |
> | billingAccounts / enrollmentAccounts / billingRoleDefinitions | Nie |
> | billingAccounts / enrollmentAccounts / billingSubscriptions | Nie |
> | billingAccounts/invoices | Nie |
> | billingAccounts /invoices/transactions | Nie |
> | billingAccounts / invoices / transactionSummary | Nie |
> | billingAccounts/invoiceSections | Nie |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | Nie |
> | billingAccounts/invoiceSections/billingSubscriptions | Nie |
> | billingAccounts / invoiceSections / billingSubscriptions / transfer | Nie |
> | billingAccounts / invoiceSections / elevate | Nie |
> | billingAccounts / invoiceSections / initiateTransfer | Nie |
> | billingAccounts / invoiceSections / patchOperations | Nie |
> | billingAccounts / invoiceSections / productMoveOperations | Nie |
> | billingAccounts /invoiceSections / products | Nie |
> | billingAccounts / invoiceSections / products / transfer | Nie |
> | billingAccounts / invoiceSections / products / updateAutoRenew | Nie |
> | billingAccounts/invoiceSections/transactions | Nie |
> | billingAccounts/invoiceSections/transfers | Nie |
> | billingAccounts / lineOfCredit | Nie |
> | billingAccounts/patchOperations | Nie |
> | billingAccounts / payableOverage | Nie |
> | billingAccounts / paymentMethods | Nie |
> | billingAccounts / payNow | Nie |
> | billingAccounts /products | Nie |
> | billingAccounts /reservations | Nie |
> | billingAccounts /transactions | Nie |
> | billingPeriods | Nie |
> | billingPermissions | Nie |
> | billingProperty | Nie |
> | billingRoleAssignments | Nie |
> | billingRoleDefinitions | Nie |
> | createBillingRoleAssignment | Nie |
> | Działów | Nie |
> | enrollmentAccounts (konta rejestracji) | Nie |
> | Faktury | Nie |
> | Promocje | Nie |
> | Transfery | Nie |
> | transfery /acceptTransfer | Nie |
> | transfery/odrzućPrzenie | Nie |
> | transfers/operationStatus | Nie |
> | transfery /validateTransfer | Nie |
> | validateAddress | Nie |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | mapApis | Yes |
> | updateCommunicationPreference | Nie |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | blockchainMembers | Yes |
> | CordaMembers | Yes |
> | Obserwatorów | Yes |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | TokenServices | Yes |
> | TokenServices / BlockchainNetworks | Nie |
> | TokenServices/Groups | Nie |
> | TokenServices / Groups / Accounts | Nie |
> | TokenServices/TokenTemplates | Nie |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | blueprintAssignments | Nie |
> | blueprintAssignments/assignmentOperations | Nie |
> | blueprintAssignments/operations | Nie |
> | Plany | Nie |
> | strategie/artefakty | Nie |
> | strategie/wersje | Nie |
> | strategie/wersje/artefakty | Nie |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | botServices | Yes |
> | botServices/channels | Nie |
> | botServices/connections | Nie |
> | hostSettings | Nie |
> | języki | Nie |
> | szablonów | Nie |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Redis | Yes |
> | Redis / EventGridFilters | Nie |
> | Redis/privateEndpointConnectionProxies | Nie |
> | Redis/ privateEndpointConnectionProxies / validate | Nie |
> | Redis/privateEndpointConnections | Nie |
> | Redis/privateLinkResources | Nie |
> | redisEnterprise | Yes |
> | redisEnterprise / bazy danych | Nie |
> | RedisEnterprise/ privateEndpointConnectionProxies | Nie |
> | RedisEnterprise / privateEndpointConnectionProxies / validate | Nie |
> | RedisEnterprise/privateEndpointConnections | Nie |
> | RedisEnterprise/privateLinkResources | Nie |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | appliedReservations | Nie |
> | autoQuotaIncrease | Nie |
> | calculateExchange | Nie |
> | calculatePrice | Nie |
> | calculatePurchasePrice | Nie |
> | Katalogi | Nie |
> | commercialReservationOrders | Nie |
> | Exchange | Nie |
> | ownReservations | Nie |
> | placePurchaseOrder | Nie |
> | reservationOrders | Nie |
> | reservationOrders/ calculateRe między | Nie |
> | reservationOrders/merge | Nie |
> | reservationOrders/reservations | Nie |
> | reservationOrders/reservations/revisions | Nie |
> | reservationOrders/return | Nie |
> | reservationOrders/split | Nie |
> | reservationOrders/swap | Nie |
> | Rezerwacje | Nie |
> | resourceProviders | Nie |
> | zasoby | Nie |
> | validateReservationOrder | Nie |

## <a name="microsoftcascade"></a>Microsoft.Cascade

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | lokacje | Yes |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Nie |
> | CdnWebApplicationFirewallPolicies | Yes |
> | brzegowe węzły | Nie |
> | Profile | Yes |
> | profile/afdendpoints | Yes |
> | profile/afdendpoints/routes | Nie |
> | profile/domeny niestandardowe | Nie |
> | profile/punkty końcowe | Yes |
> | profile/punkty końcowe/domeny niestandardowe | Nie |
> | profile/punkty końcowe/grupy pochodzenia | Nie |
> | profile/punkty końcowe/źródła | Nie |
> | profile/grupy pochodzenia | Nie |
> | profile/grupy pochodzenia/źródła | Nie |
> | profile/zasady | Nie |
> | profile/rulesets/rules | Nie |
> | profile/wpisy tajne | Nie |
> | profile/zasady zabezpieczeń | Nie |
> | validateProbe | Nie |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | certificateOrders | Yes |
> | certificateOrders/certificates | Nie |
> | validateCertificateRegistrationInformation | Nie |

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | zmiany | Nie |
> | profil | Nie |
> | resourceChanges | Nie |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | możliwości | Nie |
> | domainNames | Yes |
> | domainNames/capabilities | Nie |
> | domainNames / internalLoadBalancers | Nie |
> | domainNames/serviceCertificates | Nie |
> | domainNames/ slots | Nie |
> | domainNames / miejsca / role | Nie |
> | domainNames / slots / roles / metricDefinitions | Nie |
> | domainNames / slots / roles / metrics | Nie |
> | moveSubscriptionResources | Nie |
> | operatingSystemFamilies | Nie |
> | operatingSystems | Nie |
> | quotas | Nie |
> | resourceTypes | Nie |
> | validateSubscriptionMoveAvailability | Nie |
> | virtualMachines (maszyny wirtualne) | Yes |
> | virtualMachines / diagnosticSettings | Nie |
> | virtualMachines / metricDefinitions | Nie |
> | virtualMachines /metrics | Nie |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | classicInfrastructureResources | Nie |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | możliwości | Nie |
> | expressRouteCrossConnections | Nie |
> | expressRouteCrossConnections / komunikacja równorzędna | Nie |
> | gatewaySupportedDevices | Nie |
> | networkSecurityGroups | Yes |
> | quotas | Nie |
> | reservedIps | Yes |
> | virtualNetworks | Yes |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | Nie |
> | virtualNetworks/virtualNetworkPeerings | Nie |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | możliwości | Nie |
> | Dysków | Nie |
> | images | Nie |
> | osImages | Nie |
> | osPlatformImages | Nie |
> | publicImages | Nie |
> | quotas | Nie |
> | konta magazynu | Yes |
> | storageAccounts/blobServices | Nie |
> | storageAccounts /fileServices | Nie |
> | storageAccounts/metricDefinitions | Nie |
> | storageAccounts/metrics | Nie |
> | storageAccounts/queueServices | Nie |
> | storageAccounts /services | Nie |
> | storageAccounts / services / diagnosticSettings | Nie |
> | storageAccounts / services / metricDefinitions | Nie |
> | storageAccounts / services / metrics | Nie |
> | storageAccounts /tableServices | Nie |
> | storageAccounts / vmImages | Nie |
> | vmImages | Nie |

## <a name="microsoftclusterstor"></a>Microsoft.ClusterStor

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Węzłów | Yes |

## <a name="microsoftcodespaces"></a>Microsoft.Codespaces

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Plany | Yes |
> | registeredSubscriptions | Nie |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Konta | Yes |
> | accounts/privateEndpointConnectionProxies | Nie |
> | accounts/privateEndpointConnections | Nie |
> | accounts/privateLinkResources | Nie |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | RateCard | Nie |
> | UsageAggregates (Agregacje użycia) | Nie |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | availabilitySets | Yes |
> | cloudServices | Yes |
> | cloudServices / networkInterfaces | Nie |
> | cloudServices / publicIPAddresses | Nie |
> | cloudServices/roleInstances | Nie |
> | cloudServices / roleInstances / networkInterfaces | Nie |
> | cloudServices /roles | Nie |
> | diskAccesses | Yes |
> | diskEncryptionSets | Yes |
> | Dysków | Yes |
> | Galerie | Yes |
> | galerie/aplikacje | Nie |
> | galerie/aplikacje/wersje | Nie |
> | galerie/obrazy | Yes |
> | galerie/obrazy/wersje | Yes |
> | hostGroups | Yes |
> | hostGroups/hosts | Yes |
> | images | Yes |
> | proximityPlacementGroups | Yes |
> | restorePointCollections | Yes |
> | restorePointCollections / restorePoints | Nie |
> | sharedVMExtensions | Yes |
> | sharedVMExtensions / versions | Nie |
> | sharedVMImages | Yes |
> | sharedVMImages / wersje | Nie |
> | Migawki | Yes |
> | sshPublicKeys | Yes |
> | virtualMachines | Yes |
> | virtualMachines/extensions | Yes |
> | virtualMachines/metricDefinitions | Nie |
> | virtualMachines/ runCommands | Yes |
> | virtualMachineScaleSets | Yes |
> | virtualMachineScaleSets/extensions | Nie |
> | virtualMachineScaleSets/networkInterfaces | Nie |
> | virtualMachineScaleSets / publicIPAddresses | Nie |
> | virtualMachineScaleSets/virtualMachines | Nie |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | Nie |

## <a name="microsoftconnectedcache"></a>Microsoft.ConnectedCache

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | CacheNodes (Wnoki pamięci podręcznej) | Yes |

## <a name="microsoftconnectedvehicle"></a>Microsoft.ConnectedVehicle

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | konta platformy | Yes |
> | registeredSubscriptions | Nie |

## <a name="microsoftconnectedvmwarevsphere"></a>Microsoft.ConnectedVMwarevSphere

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | ResourcePools | Yes |
> | Centra wirtualne | Yes |
> | VCenters / InventoryItems | Nie |
> | VirtualMachines | Yes |
> | VirtualMachines / Extensions | Yes |
> | VirtualMachines / GuestAgents | Nie |
> | VirtualMachines / HybridIdentityMetadata | Nie |
> | VirtualMachineTemplates | Yes |
> | VirtualNetworks | Yes |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | AggregatedCost | Nie |
> | Salda | Nie |
> | Budżety | Nie |
> | Opłaty | Nie |
> | CostTags | Nie |
> | Kredyty | Nie |
> | zdarzenia | Nie |
> | Prognozy | Nie |
> | Wiele | Nie |
> | Rynkach | Nie |
> | Arkusze cen | Nie |
> | Produktów | Nie |
> | ReservationDetails | Nie |
> | ReservationRecommendationDetails | Nie |
> | ReservationRecommendations | Nie |
> | ReservationSummaries | Nie |
> | ReservationTransactions | Nie |
> | Tagi | Nie |
> | Najemców | Nie |
> | Terminologia | Nie |
> | UsageDetails (Szczegóły użycia) | Nie |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | containerGroups | Yes |
> | serviceAssociationLinks | Nie |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Rejestrów | Yes |
> | rejestry/agentPools | Yes |
> | rejestry/kompilacje | Nie |
> | rejestry/kompilacje/anulowanie | Nie |
> | rejestry/kompilacje/getLogLink | Nie |
> | rejestry/buildTasks | Yes |
> | rejestry / buildTasks / kroki | Nie |
> | rejestry /connectedRegistries | Nie |
> | rejestry/connectedRegistries/dezaktywacja | Nie |
> | rejestry /eventGridFilters | Nie |
> | rejestry/exportPipelines | Nie |
> | rejestry/generateCredentials | Nie |
> | rejestry / getBuildSourceUploadUrl | Nie |
> | rejestry / GetCredentials | Nie |
> | rejestry / importImage | Nie |
> | rejestry/importPipelines | Nie |
> | rejestry /pipelineRuns | Nie |
> | rejestry /privateEndpointConnectionProxies | Nie |
> | rejestry / privateEndpointConnectionProxies / weryfikacja | Nie |
> | rejestry/privateEndpointConnections | Nie |
> | rejestry/privateLinkResources | Nie |
> | rejestry/kolejkaBuild | Nie |
> | rejestry / regenerateCredential | Nie |
> | rejestry /regenerateCredentials | Nie |
> | rejestry/replikacje | Yes |
> | rejestry/przebiegi | Nie |
> | rejestry/przebiegi/anulowanie | Nie |
> | rejestry /scheduleRun | Nie |
> | rejestry/zakresMapy | Nie |
> | rejestry/zadaniaRuns | Nie |
> | rejestry/zadania | Yes |
> | rejestry/tokeny | Nie |
> | rejestry/updatePolicies | Nie |
> | rejestry/webhook | Yes |
> | rejestry/ webhook / getCallbackConfig | Nie |
> | rejestry/webhook/ping | Nie |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | containerServices | Yes |
> | managedClusters | Yes |
> | ManagedClusters / eventGridFilters | Nie |
> | openShiftManagedClusters | Yes |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Alerty | Nie |
> | Konta rozliczeniowe | Nie |
> | Budżety | Nie |
> | Połączenia w chmurze | Nie |
> | Łączniki | Yes |
> | costAllocationRules | Nie |
> | Działy | Nie |
> | Wymiary | Nie |
> | Konta rejestracji | Nie |
> | Eksporty | Nie |
> | ExternalBillingAccounts | Nie |
> | ExternalBillingAccounts / Alerts | Nie |
> | ExternalBillingAccounts / Dimensions | Nie |
> | ExternalBillingAccounts / Forecast | Nie |
> | ExternalBillingAccounts / Zapytanie | Nie |
> | ExternalSubscriptions (Opisy zewnętrzne) | Nie |
> | ExternalSubscriptions / Alerts | Nie |
> | ExternalSubscriptions / Wymiary | Nie |
> | ExternalSubscriptions / Forecast | Nie |
> | ExternalSubscriptions / Query | Nie |
> | fetchPrices | Nie |
> | Prognoza | Nie |
> | GenerateDetailedCostReport | Nie |
> | GenerateReservationDetailsReport | Nie |
> | Insights | Nie |
> | Zapytanie | Nie |
> | zarejestruj | Nie |
> | Konfiguracje raportów | Nie |
> | Raporty | Nie |
> | ScheduledActions | Nie |
> | Ustawienia | Nie |
> | showbackRules | Nie |
> | Widoki | Nie |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | DisableLockbox | Nie |
> | EnableLockbox | Nie |
> | Żądania | Nie |
> | TenantOptedIn | Nie |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Stowarzyszenia | Nie |
> | resourceProviders | Yes |

## <a name="microsoftd365customerinsights"></a>Microsoft.D365CustomerInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Wystąpień | Yes |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Zadania | Yes |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | Yes |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | obszary robocze | Yes |
> | obszary robocze/dbWorkspaces | Nie |
> | obszary robocze/virtualNetworkPeerings | Nie |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Katalogi | Yes |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | dataFactories | Yes |
> | dataFactories/diagnosticSettings | Nie |
> | dataFactories/metricDefinitions | Nie |
> | dataFactorySchema | Nie |
> | Fabryk | Yes |
> | fabryki/integracjaRuntimes | Nie |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Konta | Yes |
> | accounts/dataLakeStoreAccounts | Nie |
> | accounts/storageAccounts | Nie |
> | accounts/ storageAccounts / containers | Nie |
> | accounts/ transferAnalyticsUnits | Nie |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Konta | Yes |
> | accounts/eventGridFilters | Nie |
> | accounts/firewallRules | Nie |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | DatabaseMigrations (Migracje bazy danych) | Nie |
> | services | Yes |
> | usługi/projekty | Yes |
> | SqlMigrationServices | Yes |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | BackupVaults | Yes |
> | ResourceGuards | Yes |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Konta | Yes |
> | konta/udziały | Nie |
> | konta/udziały/zestawy danych | Nie |
> | konta/udziały/zaproszenia | Nie |
> | accounts/shares/providersharesubscriptions | Nie |
> | konta/udziały/synchronizacjaUstawienia | Nie |
> | konta/udziałysubskrypcje | Nie |
> | accounts/sharesubscriptions/consumerSourceDataSets | Nie |
> | accounts/sharesubscriptions/datasetmappings | Nie |
> | konta/udziałysubskrypcje/wyzwalacze | Nie |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Serwerów | Yes |
> | serwery/doradcy | Nie |
> | serwery/klucze | Nie |
> | servers/privateEndpointConnectionProxies | Nie |
> | servers/privateEndpointConnections | Nie |
> | serwery /privateLinkResources | Nie |
> | serwery/queryTexts | Nie |
> | serwery /recoverableServers | Nie |
> | servers / resetQueryPerformanceInsightData | Nie |
> | serwery/uruchamianie | Nie |
> | serwery/zatrzymywanie | Nie |
> | servers/topQueryStatistics | Nie |
> | servers/virtualNetworkRules | Nie |
> | servers/waitStatistics | Nie |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | flexibleServers | Yes |
> | Serwerów | Yes |
> | serwery/doradcy | Nie |
> | serwery/klucze | Nie |
> | servers/privateEndpointConnectionProxies | Nie |
> | servers/privateEndpointConnections | Nie |
> | servers/privateLinkResources | Nie |
> | servers/queryTexts | Nie |
> | servers/recoverableServers | Nie |
> | servers/ resetQueryPerformanceInsightData | Nie |
> | serwery/uruchamianie | Nie |
> | serwery/zatrzymywanie | Nie |
> | servers/topQueryStatistics | Nie |
> | serwery/uaktualnianie | Nie |
> | servers/virtualNetworkRules | Nie |
> | servers/waitStatistics | Nie |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | flexibleServers | Yes |
> | Servergroups | Yes |
> | serverGroupsv2 | Yes |
> | Serwerów | Yes |
> | serwery/doradcy | Nie |
> | serwery/klucze | Nie |
> | servers/privateEndpointConnectionProxies | Nie |
> | servers/privateEndpointConnections | Nie |
> | serwery /privateLinkResources | Nie |
> | serwery/queryTexts | Nie |
> | serwery /recoverableServers | Nie |
> | servers / resetQueryPerformanceInsightData | Nie |
> | servers/topQueryStatistics | Nie |
> | servers/virtualNetworkRules | Nie |
> | servers/waitStatistics | Nie |
> | serversv2 | Yes |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | artifactSources | Yes |
> | Wdrożenia | Yes |
> | serviceTopologies | Yes |
> | serviceTopologies /services | Yes |
> | serviceTopologies / services / serviceUnits | Yes |
> | kroki | Yes |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | grupy aplikacji | Yes |
> | grupy aplikacji/aplikacje | Nie |
> | grupy aplikacji/komputery stacjonarne | Nie |
> | applicationgroups / startmenuitems | Nie |
> | puli hostów | Yes |
> | hostpools/msixpackages | Nie |
> | hostpools/sessionhosts | Nie |
> | hostpools /sessionhosts/usersessions | Nie |
> | hostpools/usersessions | Nie |
> | scalingPlans | Yes |
> | obszary robocze | Yes |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | ElasticPools | Yes |
> | ElasticPools / IotHubTenants | Yes |
> | ElasticPools / IotHubTenants / securitySettings | Nie |
> | IotHubs | Yes |
> | IotHubs / eventGridFilters | Nie |
> | IotHubs / securitySettings | Nie |
> | ProvisioningServices | Yes |
> | Zwyczaje | Nie |

## <a name="microsoftdeviceupdate"></a>Microsoft.DeviceUpdate

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Konta | Yes |
> | konta/wystąpienia | Yes |
> | zarejestrowaneSubskrypcje | Nie |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Rurociągów | Yes |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | kontrolery | Yes |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | centra laboratoryjne | Yes |
> | Labs | Yes |
> | laboratoria/środowiska | Yes |
> | labs/serviceRunners | Yes |
> | labs/virtualMachines | Yes |
> | Harmonogramy | Yes |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | digitalTwinsInstances | Yes |
> | digitalTwinsInstances /endpoints | Nie |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | cassandraClusters | Yes |
> | databaseAccountNames | Nie |
> | databaseAccounts | Yes |
> | restorableDatabaseAccounts | Nie |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Domen | Yes |
> | domains/domainOwnershipIdentifiers | Nie |
> | generateSsoRequest | Nie |
> | topLevelDomains | Nie |
> | validateDomainRegistrationInformation | Nie |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | lcsprojects | Nie |
> | lcsprojects/ clouddeployments | Nie |
> | lcsprojects /connectors | Nie |

## <a name="microsoftedgeorder"></a>Microsoft.EdgeOrder

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Adresy | Yes |
> | orderCollections | Yes |
> | orders | Yes |
> | productFamiliesMetadata | Nie |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | services | Yes |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Domen | Yes |
> | domeny/tematy | Nie |
> | eventSubscriptions | Nie |
> | extensionTopics | Nie |
> | partnerNamespaces | Yes |
> | partnerNamespaces/eventChannels | Nie |
> | partnerRegistrations | Yes |
> | partnerTopics | Yes |
> | partnerTopics/eventSubscriptions | Nie |
> | systemTopics | Yes |
> | systemTopics/eventSubscriptions | Nie |
> | Tematy | Yes |
> | topicTypes | Nie |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Klastrów | Yes |
> | Obszary nazw | Yes |
> | przestrzenie nazw/reguły autoryzacji | Nie |
> | namespaces/disasterrecoveryconfigs | Nie |
> | namespaces/eventhubs | Nie |
> | namespaces /eventhubs/authorizationrules | Nie |
> | namespaces / eventhubs / consumergroups | Nie |
> | namespaces/networkrulesets | Nie |
> | namespaces/privateEndpointConnections | Nie |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | experimentWorkspaces | Yes |

## <a name="microsoftfalcon"></a>Microsoft.Zygmej

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Obszary nazw | Yes |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | featureConfigurations | Nie |
> | featureProviderNamespaces | Nie |
> | featureProviders | Nie |
> | funkcje | Nie |
> | dostawców | Nie |
> | subscriptionFeatureRegistrations | Nie |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Zarejestrować | Nie |
> | galleryitems | Nie |
> | generateartifactaccessuri | Nie |
> | myareas | Nie |
> | myareas / obszary | Nie |
> | myareas / obszary / obszary | Nie |
> | myareas / obszary / obszary / galleryitems | Nie |
> | myareas / obszary / galleryitems | Nie |
> | myareas / galleryitems | Nie |
> | zarejestruj | Nie |
> | zasoby | Nie |
> | retrieveresourcesbyid | Nie |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Konta | Yes |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | autoManagedAccounts | Yes |
> | autoManagedVmConfigurationProfiles | Yes |
> | configurationProfileAssignments | Nie |
> | guestConfigurationAssignments | Nie |
> | Oprogramowania | Nie |
> | softwareUpdateProfile | Nie |
> | softwareUpdates | Nie |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | hanaInstances | Yes |
> | sapMonitors | Yes |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | dedicatedHSMs | Yes |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | clusterPools | Yes |
> | clusterPools/clusters | Yes |
> | Klastrów | Yes |
> | klastry/aplikacje | Nie |

## <a name="microsofthealthbot"></a>Microsoft.HealthBot

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | healthBots | Yes |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | services | Yes |
> | usługi /iomtconnectors | Nie |
> | usługi / iomtconnectors / połączenia | Nie |
> | usługi / iomtconnectors / mapowania | Nie |
> | services/privateEndpointConnectionProxies | Nie |
> | services/privateEndpointConnections | Nie |
> | services/privateLinkResources | Nie |
> | obszary robocze | Yes |
> | obszary robocze/dicomservices | Yes |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Maszyny | Yes |
> | maszyny/assessPatches | Nie |
> | maszyny/rozszerzenia | Yes |
> | machines/installPatches | Nie |
> | machines/privateLinkScopes | Nie |
> | privateLinkScopes | Yes |
> | privateLinkScopes / privateEndpointConnectionProxies | Nie |
> | privateLinkScopes / privateEndpointConnections | Nie |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Menedżerowie danych | Yes |

## <a name="microsofthybridnetwork"></a>Microsoft.HybridNetwork

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | devices | Yes |
> | networkfunctions | Yes |
> | networkFunctionVendors | Nie |
> | zarejestrowaneSubskrypcje | Nie |
> | Dostawców | Nie |
> | Dostawcy/dostawcykus | Nie |
> | Dostawcy/dostawcykus/wersje zapoznawczesubskrypcje | Nie |
> | virtualNetworkFunctions | Yes |
> | virtualNetworkFunctionVendors | Nie |

## <a name="microsofthydra"></a>Microsoft.Przejmij

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Składniki | Yes |
> | networkScopes | Yes |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Zadania | Yes |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | diagnosticSettings | Nie |
> | diagnosticSettingsCategories | Nie |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | appTemplates | Nie |
> | IoTApps | Yes |

## <a name="microsoftiotsecurity"></a>Microsoft.IoTSecurity

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | defenderSettings | Nie |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Graph | Yes |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | deletedManagedHSMs | Nie |
> | deletedVaults | Nie |
> | hsmPools | Yes |
> | managedHSMs | Yes |
> | Sklepienia | Yes |
> | magazyny/accessPolicies | Nie |
> | vaults/eventGridFilters | Nie |
> | magazyny/klucze | Nie |
> | magazyny/klucze/wersje | Nie |
> | magazyny/wpisy tajne | Nie |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | connectedClusters | Yes |
> | registeredSubscriptions | Nie |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Rozszerzenia | Nie |
> | sourceControlConfigurations | Nie |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Klastrów | Yes |
> | klastry/attacheddatabaseconfigurations | Nie |
> | klastry/bazy danych | Nie |
> | klastry/bazy danych/połączenia danych | Nie |
> | klastry/bazy danych/eventhubconnections | Nie |
> | klastry/bazy danych/jednostkizapisy | Nie |
> | klastry/bazy danych/skrypty | Nie |
> | klastry/połączenia danych | Nie |
> | klastry /principalassignments | Nie |
> | klastry/współdzielone identyfikatory | Nie |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | labaccounts | Yes |
> | labplans | Yes |
> | Labs | Yes |
> | users | Nie |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | hostingEnvironments | Yes |
> | integrationAccounts | Yes |
> | integrationServiceEnvironments | Yes |
> | integrationServiceEnvironments/managedApis | Yes |
> | isolatedEnvironments | Yes |
> | Przepływy pracy | Yes |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | commitmentPlans | Yes |
> | Webservices | Yes |
> | Obszary robocze | Yes |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | modelinventories | Yes |
> | virtualclusters | Yes |
> | obszary robocze | Yes |
> | obszary robocze/batchEndpoints | Yes |
> | obszary robocze/batchEndpoints/wdrożenia | Yes |
> | obszary robocze / batchEndpoints / wdrożenia / zadania | Nie |
> | obszary robocze/ batchEndpoints /jobs | Nie |
> | obszary robocze/kody | Nie |
> | obszary robocze/kody/wersje | Nie |
> | obszary robocze/obliczenia | Nie |
> | obszary robocze/dane | Nie |
> | obszary robocze/magazyny danych | Nie |
> | obszary robocze/środowiska | Nie |
> | workspaces/eventGridFilters | Nie |
> | obszary robocze/zadania | Nie |
> | obszary robocze/etykietowanieObs | Nie |
> | obszary robocze/połączoneusługi | Nie |
> | obszary robocze/modele | Nie |
> | obszary robocze/modele/wersje | Nie |
> | obszary robocze/onlinePunktyendów | Yes |
> | obszary robocze/ onlinePunktyendów/wdrożenia | Yes |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | applyUpdates | Nie |
> | configurationAssignments | Nie |
> | maintenanceConfigurations | Yes |
> | publicMaintenanceConfigurations | Nie |
> | aktualizacje | Nie |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Tożsamości | Nie |
> | userAssignedIdentities | Yes |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | managedNetworks | Yes |
> | managedNetworks/managedNetworkGroups | Yes |
> | managedNetworks / managedNetworkPeeringPolicies | Yes |
> | powiadomienie | Yes |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions | Nie |
> | registrationAssignments | Nie |
> | registrationDefinitions | Nie |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | getEntities | Nie |
> | managementGroups (grupy zarządzania) | Nie |
> | managementGroups /settings | Nie |
> | zasoby | Nie |
> | startTenantBackfill | Nie |
> | tenantBackfillStatus | Nie |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Konta | Yes |
> | konta/twórcy | Yes |
> | accounts/eventGridFilters | Nie |
> | accounts/privateAtlases | Yes |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | macc | Nie |
> | Oferuje | Nie |
> | offerTypes | Nie |
> | offerTypes/publishers | Nie |
> | offerTypes /publishers/offers | Nie |
> | offerTypes /publishers/offers/plans | Nie |
> | offerTypes / publishers / offers / plans / agreements | Nie |
> | offerTypes / publishers / offers / plans / configs | Nie |
> | offerTypes / publishers / offers / plans / configs / importImage | Nie |
> | privategalleryitems | Nie |
> | privateStoreClient | Nie |
> | privateStores | Nie |
> | privateStores / AdminRequestApprovals | Nie |
> | privateStores/offers | Nie |
> | privateStores/offers/acknowledgeNotification | Nie |
> | privateStores/queryNotificationsState | Nie |
> | privateStores / RequestApprovals | Nie |
> | privateStores / requestApprovals / query | Nie |
> | privateStores/requestApprovals/wyliczyPlan | Nie |
> | Produktów | Nie |
> | Wydawców | Nie |
> | wydawcy/oferty | Nie |
> | wydawcy/oferty/poprawki | Nie |
> | zarejestruj | Nie |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | classicDevServices | Yes |
> | updateCommunicationPreference | Nie |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Umów | Nie |
> | offertypes (typy ofert) | Nie |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | mediaservices | Yes |
> | mediaservices/accountFilters | Nie |
> | mediaservices/assets | Nie |
> | mediaservices / assets / assetFilters | Nie |
> | mediaservices / contentKeyPolicies | Nie |
> | mediaservices / eventGridFilters | Nie |
> | mediaservices/graphInstances | Nie |
> | mediaservices/graphTopologies | Nie |
> | mediaservices/liveEventOperations | Nie |
> | mediaservices/liveEvents | Yes |
> | mediaservices/liveEvents/liveOutputs | Nie |
> | mediaservices/ liveOutputOperations | Nie |
> | mediaservices/mediaGraphs | Nie |
> | mediaservices/ privateEndpointConnectionOperations | Nie |
> | mediaservices/ privateEndpointConnectionProxies | Nie |
> | mediaservices/privateEndpointConnections | Nie |
> | mediaservices/ streamingEndpointOperations | Nie |
> | mediaservices/streamingEndpoints | Yes |
> | mediaservices/streamingLocators | Nie |
> | mediaservices/ streamingPolicies | Nie |
> | mediaservices/transforms | Nie |
> | mediaservices/transforms/jobs | Nie |
> | videoAnalyzers | Yes |
> | videoAnalyzers /edgeModules | Nie |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | appClusters | Yes |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | assessmentProjects | Yes |
> | migrateprojects | Yes |
> | moveCollections | Yes |
> | Projektów | Yes |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | Yes |
> | objectAnchorsAccounts | Yes |
> | objectUnderstandingAccounts | Yes |
> | remoteRenderingAccounts | Yes |
> | spatialAnchorsAccounts | Yes |

## <a name="microsoftmobilenetwork"></a>Microsoft.MobileNetwork

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Sieci | Yes |
> | sieci/lokacje | Yes |
> | packetCores | Yes |
> | The sims | Yes |
> | shape /simProfiles | Yes |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | netAppAccounts | Yes |
> | netAppAccounts / accountBackups | Nie |
> | netAppAccounts / capacityPools | Yes |
> | netAppAccounts / capacityPools / volumes | Yes |
> | netAppAccounts / capacityPools / woluminy / migawki | Nie |
> | netAppAccounts / volumeGroups | Nie |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | applicationGateways | Yes |
> | applicationGatewayWebApplicationFirewallPolicies | Yes |
> | applicationSecurityGroups | Yes |
> | azureFirewallFqdnTags | Nie |
> | azureFirewalls | Yes |
> | bastionHosts | Yes |
> | bgpServiceCommunities | Nie |
> | Połączenia | Yes |
> | ddosCustomPolicies | Yes |
> | ddosProtectionPlans | Yes |
> | dnsOperationStatuses | Nie |
> | dnszones | Yes |
> | dnszones /A | Nie |
> | dnszones / AAAA | Nie |
> | dnszones / wszystkie | Nie |
> | dnszones / CAA | Nie |
> | dnszones / CNAME | Nie |
> | dnszones / MX | Nie |
> | dnszones / NS | Nie |
> | dnszones / PTR | Nie |
> | dnszones /recordsets | Nie |
> | dnszones / SOA | Nie |
> | dnszones / SRV | Nie |
> | dnszones / TXT | Nie |
> | expressRouteCircuits | Yes |
> | expressRouteCrossConnections | Yes |
> | expressRouteGateways | Yes |
> | expressRoutePorts | Yes |
> | expressRouteServiceProviders | Nie |
> | firewallPolicies | Yes |
> | frontdoors (frontdoory) | Yes |
> | frontdoorWebApplicationFirewallManagedRuleSets | Nie |
> | frontdoorWebApplicationFirewallPolicies | Yes |
> | getDnsResourceReference | Nie |
> | internalNotify | Nie |
> | IpGroups | Yes |
> | loadBalancers | Yes |
> | localNetworkGateways | Yes |
> | natGateways | Yes |
> | networkIntentPolicies | Yes |
> | networkInterfaces | Yes |
> | networkProfiles | Yes |
> | networkSecurityGroups | Yes |
> | networkWatchers | Yes |
> | networkWatchers /connectionMonitors | Yes |
> | networkWatchers/flowLogs | Yes |
> | networkWatchers / Lenses | Yes |
> | networkWatchers / pingMeshes | Yes |
> | p2sVpnGateways | Yes |
> | privateDnsOperationStatuses | Nie |
> | privateDnsZones | Yes |
> | privateDnsZones /A | Nie |
> | privateDnsZones / AAAA | Nie |
> | privateDnsZones / wszystkie | Nie |
> | privateDnsZones / CNAME | Nie |
> | privateDnsZones /MX | Nie |
> | privateDnsZones / PTR | Nie |
> | privateDnsZones / SOA | Nie |
> | privateDnsZones / SRV | Nie |
> | privateDnsZones / TXT | Nie |
> | privateDnsZones / virtualNetworkLinks | Yes |
> | privateEndpoints | Yes |
> | privateLinkServices | Yes |
> | publicIPAddresses | Yes |
> | publicIPPrefixes | Yes |
> | routeFilters | Yes |
> | routeTables | Yes |
> | serviceEndpointPolicies | Yes |
> | trafficManagerGeographicHierarchies | Nie |
> | trafficmanagerprofiles | Yes |
> | trafficmanagerprofiles/ heatMaps | Nie |
> | trafficManagerUserMetricsKeys | Nie |
> | virtualHubs | Yes |
> | virtualNetworkGateways | Yes |
> | virtualNetworks | Yes |
> | virtualNetworks/podsieci | Nie |
> | virtualNetworkTaps | Yes |
> | virtualWans | Yes |
> | vpnGateways | Yes |
> | vpnSites | Yes |
> | webApplicationFirewallPolicies | Yes |

## <a name="microsoftnotebooks"></a>Microsoft.Notebooks

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | NotebookProxies | Nie |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Obszary nazw | Yes |
> | namespaces/notificationHubs | Yes |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | osNamespaces | Yes |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | HyperVSites | Yes |
> | ImportSites | Yes |
> | Strony główne | Yes |
> | SerwerWitryny | Yes |
> | VMwareSites | Yes |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Klastrów | Yes |
> | deletedWorkspaces | Nie |
> | linkTargets | Nie |
> | querypacks | Yes |
> | storageInsightConfigs | Nie |
> | obszary robocze | Yes |
> | obszary robocze/daneEkporty | Nie |
> | obszary robocze/źródła danych | Nie |
> | obszary robocze/połączoneusługi | Nie |
> | workspaces/linkedStorageAccounts | Nie |
> | obszary robocze/metadane | Nie |
> | obszary robocze/zapytanie | Nie |
> | workspaces/scopedPrivateLinkProxies | Nie |
> | workspaces/storageInsightConfigs | Nie |
> | obszary robocze/tabele | Nie |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | skojarzenia zarządzania | Nie |
> | managementconfigurations (konfiguracje zarządzania) | Yes |
> | rozwiązania | Yes |
> | widoki | Yes |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | cdnPeeringPrefixes | Nie |
> | starsza wersja | Nie |
> | peerAsns | Nie |
> | komunikacja równorzędna | Yes |
> | peeringServiceCountries | Nie |
> | peeringServiceProviders | Nie |
> | peeringServices (usługi komunikacji równorzędnej) | Yes |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Atesty | Nie |
> | eventGridFilters | Nie |
> | policyEvents | Nie |
> | policyMetadata | Nie |
> | policyStates (stan zasad) | Nie |
> | policyTrackedResources | Nie |
> | korygowanie | Nie |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Konsole programu | Nie |
> | pulpity nawigacyjne | Yes |
> | tenantconfigurations | Nie |
> | userSettings | Nie |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | privateLinkServicesForPowerBI | Yes |
> | Najemców | Yes |
> | dzierżawy/obszary robocze | Nie |
> | workspaceCollections | Yes |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | autoScaleVCores | Yes |
> | Pojemności | Yes |

## <a name="microsoftpowerplatform"></a>Microsoft.PowerPlatform

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | enterprisePolicies | Yes |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Konta | Yes |
> | deletedAccounts | Nie |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | providerRegistrations | Nie |
> | providerRegistrations / customRollouts | Nie |
> | providerRegistrations / defaultRollouts | Nie |
> | providerRegistrations / resourceTypeRegistrations | Nie |

## <a name="microsoftpurview"></a>Microsoft.Purview

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Konta | Yes |
> | deletedAccounts | Nie |
> | getDefaultAccount | Nie |
> | removeDefaultAccount | Nie |
> | setDefaultAccount | Nie |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Obszary robocze | Yes |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | backupProtectedItems | Nie |
> | Sklepienia | Yes |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | OpenShiftClusters | Yes |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Obszary nazw | Yes |
> | namespaces/authorizationrules | Nie |
> | przestrzenie nazw/połączenia hybrydowe | Nie |
> | namespaces/hybridconnections/authorizationrules | Nie |
> | namespaces/privateEndpointConnections | Nie |
> | namespaces / wcfrelays | Nie |
> | namespaces / wcfrelays / authorizationrules | Nie |

## <a name="microsoftresourceconnector"></a>Microsoft.ResourceConnector

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Urządzeń | Yes |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Kwerendy | Yes |
> | resourceChangeDetails | Nie |
> | resourceChanges | Nie |
> | zasoby | Nie |
> | resourcesHistory | Nie |
> | subscriptionsStatus | Nie |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | availabilityStatuses | Nie |
> | childAvailabilityStatuses | Nie |
> | childResources | Nie |
> | emergingissues | Nie |
> | zdarzenia | Nie |
> | impactedResources | Nie |
> | metadane | Nie |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Wdrożeń | Nie |
> | wdrożenia/operacje | Nie |
> | deploymentScripts | Yes |
> | deploymentScripts/logs | Nie |
> | Linki | Nie |
> | dostawców | Nie |
> | resourceGroups | Nie |
> | Subskrypcji | Nie |
> | templateSpecs | Yes |
> | templateSpecs/versions | Yes |
> | Najemców | Nie |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | aplikacje | Yes |
> | zasoby | Yes |
> | saasresources | Nie |

## <a name="microsoftscvmm"></a>Microsoft.ScVmm

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Chmury | Yes |
> | VirtualMachines | Yes |
> | VirtualMachineTemplates | Yes |
> | VirtualNetworks | Yes |
> | vmmservers | Yes |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | resourceHealthMetadata | Nie |
> | searchServices | Yes |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | adaptiveNetworkHardenings | Nie |
> | advancedThreatProtectionSettings | Nie |
> | alerts | Nie |
> | alertsSuppressionRules | Nie |
> | allowedConnections | Nie |
> | applicationWhitelistings | Nie |
> | assessmentMetadata | Nie |
> | oceny | Nie |
> | autoDismissAlertsRules | Nie |
> | Automatyki | Yes |
> | AutoProvisioningSettings | Nie |
> | Zgodność | Nie |
> | konta integracji | Nie |
> | dataCollectionAgents | Nie |
> | devices | Nie |
> | deviceSecurityGroups | Nie |
> | discoveredSecuritySolutions | Nie |
> | externalSecuritySolutions | Nie |
> | InformationProtectionPolicies | Nie |
> | ingestionSettings | Nie |
> | Spostrzeżenia | Nie |
> | iotAlerts | Nie |
> | iotAlertTypes | Nie |
> | iotDefenderSettings | Nie |
> | iotRecommendations | Nie |
> | iotRecommendationTypes | Nie |
> | iotSecuritySolutions | Yes |
> | iotSecuritySolutions/analyticsModels | Nie |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | Nie |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | Nie |
> | iotSecuritySolutions / iotAlerts | Nie |
> | iotSecuritySolutions / iotAlertTypes | Nie |
> | iotSecuritySolutions / iotRecommendations | Nie |
> | iotSecuritySolutions / iotRecommendationTypes | Nie |
> | iotSensors | Nie |
> | iotSites | Nie |
> | jitNetworkAccessPolicies | Nie |
> | jitPolicies | Nie |
> | onPremiseIotSensors | Nie |
> | policies | Nie |
> | cennik | Nie |
> | regulatoryComplianceStandards | Nie |
> | regulatoryComplianceStandards/regulatoryComplianceControls | Nie |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | Nie |
> | secureScoreControlDefinitions | Nie |
> | secureScoreControls | Nie |
> | secureScores | Nie |
> | secureScores/ secureScoreControls | Nie |
> | securityContacts | Nie |
> | securitySolutions (rozwiązania zabezpieczeń) | Nie |
> | securitySolutionsReferenceData | Nie |
> | securityStatuses (statystyki zabezpieczeń) | Nie |
> | securityStatusesSummaries | Nie |
> | serverVulnerabilityAssessments | Nie |
> | ustawienia | Nie |
> | sqlVulnerabilityAssessments | Nie |
> | podoceny | Nie |
> | zadania | Nie |
> | Topologii | Nie |
> | workspaceSettings | Nie |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | diagnosticSettings | Nie |
> | diagnosticSettingsCategories | Nie |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Agregacji | Nie |
> | alertRules | Nie |
> | alertRuleTemplates | Nie |
> | automationRules | Nie |
> | zakładki | Nie |
> | Przypadkach | Nie |
> | dataConnectors (połączenia danych) | Nie |
> | dataConnectorsCheckRequirements | Nie |
> | Wzbogacania | Nie |
> | Podmioty | Nie |
> | entityQueries | Nie |
> | entityQueryTemplates | Nie |
> | zdarzenia | Nie |
> | officeConsents | Nie |
> | ustawienia | Nie |
> | threatIntelligence | Nie |
> | listy do obejrzenia | Nie |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | consoleServices | Nie |
> | serialPorts | Nie |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Obszary nazw | Yes |
> | przestrzenie nazw/reguły autoryzacji | Nie |
> | namespaces/disasterrecoveryconfigs | Nie |
> | namespaces/eventgridfilters | Nie |
> | namespaces/networkrulesets | Nie |
> | namespaces/privateEndpointConnections | Nie |
> | przestrzenie nazw/kolejki | Nie |
> | namespaces/queues/authorizationrules | Nie |
> | przestrzenie nazw/tematy | Nie |
> | namespaces / topics / authorizationrules | Nie |
> | przestrzenie nazw / tematy / subskrypcje | Nie |
> | przestrzenie nazw / tematy / subskrypcje / reguły | Nie |
> | premiumMessagingRegions | Nie |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | aplikacje | Yes |
> | Klastrów | Yes |
> | klastry/aplikacje | Nie |
> | containerGroups | Yes |
> | containerGroupSets | Yes |
> | edgeclusters | Yes |
> | edgeclusters / aplikacje | Nie |
> | managedclusters | Yes |
> | managedclusters /applications | Nie |
> | managedclusters / aplikacje / usługi | Nie |
> | managedclusters /applicationTypes | Nie |
> | managedclusters / applicationTypes / versions | Nie |
> | managedclusters /nodetypes | Nie |
> | Sieci | Yes |
> | secretstores | Yes |
> | secretstores/certificates | Nie |
> | magazyny wpisów tajnych/wpisy tajne | Nie |
> | volumes | Yes |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | aplikacje | Yes |
> | containerGroups | Yes |
> | Bramy | Yes |
> | Sieci | Yes |
> | wpisy tajne | Yes |
> | volumes | Yes |

## <a name="microsoftservicelinker"></a>Microsoft.ServiceLinker

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Linkers | Nie |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | providerRegistrations | Nie |
> | providerRegistrations / resourceTypeRegistrations | Nie |
> | Wdrożenia | Yes |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | SignalR | Yes |
> | SignalR /eventGridFilters | Nie |
> | WebPubSub | Yes |

## <a name="microsoftsingularity"></a>Microsoft.Singularity

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Konta | Yes |
> | accounts/accountQuotaPolicies | Nie |
> | accounts/groupPolicies | Nie |
> | konta/zadania | Nie |
> | accounts/storageContainers | Nie |
> | images | Nie |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | hybridUseBenefits | Nie |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | applicationDefinitions | Yes |
> | aplikacje | Yes |
> | jitRequests | Yes |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | managedInstances | Yes |
> | managedInstances /databases | Yes |
> | managedInstances / databases / backupShortTermRetentionPolicies | Nie |
> | managedInstances / bazy danych / schematy / tabele / kolumny / sensitivityLabels | Nie |
> | managedInstances /databases/vulnerabilityAssessments | Nie |
> | managedInstances / databases / vulnerabilityAssessments / rules / baselines | Nie |
> | managedInstances /encryptionProtector | Nie |
> | managedInstances /keys | Nie |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | Nie |
> | managedInstances/vulnerabilityAssessments | Nie |
> | Serwerów | Yes |
> | serwery/administratorzy | Nie |
> | serwery/komunikacjaLinki | Nie |
> | serwery/bazy danych | Yes |
> | serwery/encryptionProtector | Nie |
> | servers/firewallRules | Nie |
> | serwery/klucze | Nie |
> | servers/restorableDroppedDatabases | Nie |
> | servers/serviceobjectives | Nie |
> | servers/tdeCertificates | Nie |
> | virtualClusters | Nie |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | Yes |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Nie |
> | SqlVirtualMachines | Yes |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | deletedAccounts | Nie |
> | konta magazynu | Yes |
> | storageAccounts / blobServices | Nie |
> | storageAccounts / fileServices | Nie |
> | storageAccounts / queueServices | Nie |
> | storageAccounts /services | Nie |
> | storageAccounts / services / metricDefinitions | Nie |
> | storageAccounts / tableServices | Nie |
> | Zwyczaje | Nie |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | amlFilesystems | Yes |
> | Buforuje | Yes |
> | pamięci podręczne/storageTargets | Nie |
> | usageModels | Nie |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | replicationGroups | Nie |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | storageSyncServices | Yes |
> | storageSyncServices/registeredServers | Nie |
> | storageSyncServices/syncGroups | Nie |
> | storageSyncServices / syncGroups / cloudEndpoints | Nie |
> | storageSyncServices / syncGroups / serverEndpoints | Nie |
> | storageSyncServices/workflows | Nie |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | storageSyncServices | Yes |
> | storageSyncServices/registeredServers | Nie |
> | storageSyncServices/syncGroups | Nie |
> | storageSyncServices / syncGroups / cloudEndpoints | Nie |
> | storageSyncServices / syncGroups / serverEndpoints | Nie |
> | storageSyncServices/workflows | Nie |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | storageSyncServices | Yes |
> | storageSyncServices/registeredServers | Nie |
> | storageSyncServices/syncGroups | Nie |
> | storageSyncServices / syncGroups / cloudEndpoints | Nie |
> | storageSyncServices / syncGroups / serverEndpoints | Nie |
> | storageSyncServices/workflows | Nie |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Menedżerów | Yes |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Klastrów | Yes |
> | klastry/prywatnePunktyendowe | Nie |
> | streamingjobs | Yes |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | acceptChangeTenant | Nie |
> | acceptOwnership | Nie |
> | acceptOwnershipStatus | Nie |
> | Aliasy | Nie |
> | Anuluj | Nie |
> | changeTenantRequest | Nie |
> | changeTenantStatus | Nie |
> | Createsubscription | Nie |
> | Włącz | Nie |
> | policies | Nie |
> | zmień nazwę | Nie |
> | SubscriptionDefinitions | Nie |
> | SubscriptionOperations | Nie |
> | Subskrypcji | Nie |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | privateLinkHubs | Yes |
> | obszary robocze | Yes |
> | workspaces/bigDataPools | Yes |
> | workspaces/operationStatuses | Nie |
> | obszary robocze /sqlDatabases | Yes |
> | obszary robocze/sqlPools | Yes |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Środowiskach | Yes |
> | środowiska /accessPolicies | Nie |
> | środowiska/źródła zdarzeń | Yes |
> | environments/privateEndpointConnectionProxies | Nie |
> | środowiska/privateEndpointConnections | Nie |
> | środowiska/privateLinkResources | Nie |
> | środowiska/referenceDataSets | Yes |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Sklepy | Yes |
> | stores/accessPolicies | Nie |
> | sklepy/usługi | Nie |
> | magazyny/usługi/tokeny | Nie |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | imageTemplates | Yes |
> | imageTemplates/runOutputs | Nie |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | ArcZones | Yes |
> | ResourcePools | Yes |
> | Centra VCenter | Yes |
> | VCenters / InventoryItems | Nie |
> | virtualmachines | Yes |
> | VirtualMachineTemplates | Yes |
> | VirtualNetworks | Yes |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | dedicatedCloudNodes | Yes |
> | dedicatedCloudServices | Yes |
> | virtualMachines (maszyny wirtualne) | Yes |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | devices | Yes |
> | registeredSubscriptions | Nie |
> | Dostawców | Nie |
> | dostawcy/jednostki SKU | Nie |
> | dostawcy/vnfs | Nie |
> | virtualNetworkFunctionSkus | Nie |
> | vnfs | Yes |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Konta | Yes |
> | Plany | Yes |
> | zarejestrowaneSubskrypcje | Nie |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | apiManagementAccounts | Nie |
> | apiManagementAccounts /apiAcls | Nie |
> | apiManagementAccounts/apis | Nie |
> | apiManagementAccounts / apis / apiAcls | Nie |
> | apiManagementAccounts / apis / connectionAcls | Nie |
> | apiManagementAccounts /apis/connections | Nie |
> | apiManagementAccounts / apis / connections / connectionAcls | Nie |
> | apiManagementAccounts / apis / localizedDefinitions | Nie |
> | apiManagementAccounts/connectionAcls | Nie |
> | apiManagementAccounts/connections | Nie |
> | billingMeters | Nie |
> | certyfikaty | Yes |
> | connectionGateways | Yes |
> | Połączenia | Yes |
> | customApis | Yes |
> | deletedSites | Nie |
> | functionAppStacks | Nie |
> | generateGithubAccessTokenForAppserviceCLI | Nie |
> | hostingEnvironments | Yes |
> | hostingEnvironments / eventGridFilters | Nie |
> | hostingEnvironments/ multiRolePools | Nie |
> | hostingEnvironments/workerPools | Nie |
> | kubeEnvironments | Yes |
> | publishingUsers | Nie |
> | zalecenia | Nie |
> | resourceHealthMetadata | Nie |
> | środowiska uruchomieniowe | Nie |
> | serverFarms | Yes |
> | serverFarms / eventGridFilters | Nie |
> | serverFarms / firstPartyApps | Nie |
> | serverFarms / firstPartyApps / keyVaultSettings | Nie |
> | lokacje | Yes |
> | witryny/konfiguracja  | Nie |
> | sites/eventGridFilters | Nie |
> | sites/hostNameBindings | Nie |
> | sites/networkConfig | Nie |
> | sites/premieraddons | Yes |
> | lokacje/miejsca | Yes |
> | sites/ slots / eventGridFilters | Nie |
> | sites / slots / hostNameBindings | Nie |
> | sites/slots/networkConfig | Nie |
> | sourceControls | Nie |
> | staticSites | Yes |
> | Sprawdzania poprawności | Nie |
> | verifyHostingEnvironmentVnet | Nie |
> | WebAppStacks | Nie |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | diagnosticSettings | Nie |
> | diagnosticSettingsCategories | Nie |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | multipleActivationKeys | Yes |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | DeviceServices | Yes |

## <a name="microsoftworkloadbuilder"></a>Microsoft.WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | migrationAgents | Yes |
> | Obciążeń | Yes |
> | obciążenia/wystąpienia | Nie |
> | obciążenia/wersje | Nie |
> | obciążenia/wersje/artefakty | Nie |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Monitory | Nie |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać te same dane co plik wartości rozdzielanych przecinkami, pobierz [ plikcomplete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).
