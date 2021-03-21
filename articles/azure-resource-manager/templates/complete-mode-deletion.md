---
title: Usuwanie w trybie pełnym
description: Pokazuje, jak typy zasobów obsługują usuwanie w trybie pełnym w szablonach Azure Resource Manager.
ms.topic: conceptual
ms.date: 10/21/2020
ms.openlocfilehash: e0c67bfcda81ad128e0018c4ab37c4b0cbe680f0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96184029"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Usuwanie zasobów platformy Azure na potrzeby wdrożeń w trybie pełnym

W tym artykule opisano sposób, w jaki typy zasobów obsługują usuwanie, gdy nie znajduje się w szablonie wdrożonym w trybie kompletnym.

Typy zasobów oznaczone **wartością tak** są usuwane, gdy typ nie jest w szablonie wdrożonym z trybem kompletnym.

Typy zasobów oznaczone atrybutem **no** nie są automatycznie usuwane, gdy nie znajdują się w szablonie; Jednak są usuwane, jeśli zasób nadrzędny zostanie usunięty. Pełny opis zachowania można znaleźć w temacie [Azure Resource Manager Modes Deployments](deployment-modes.md).

W przypadku wdrożenia w [więcej niż jednej grupie zasobów w szablonie](./deploy-to-resource-group.md)zasoby w grupie zasobów określonej w operacji wdrażania mogą zostać usunięte. Zasoby w dodatkowych grupach zasobów nie są usuwane.

Zasoby są wyświetlane według przestrzeni nazw dostawcy zasobów. Aby dopasować przestrzeń nazw dostawcy zasobów przy użyciu nazwy usługi platformy Azure, zobacz [dostawcy zasobów dla usług platformy Azure](../management/azure-services-resource-providers.md).

> [!NOTE]
> Zawsze używaj [operacji działania warunkowego](template-deploy-what-if.md) przed wdrożeniem szablonu w trybie kompletnym. Co oznacza, które zasoby zostaną utworzone, usunięte lub zmodyfikowane. Użyj co do tego, aby uniknąć przypadkowego usunięcia zasobów.
Przejdź do przestrzeni nazw dostawcy zasobów:
> [!div class="op_single_selector"]
> - [Microsoft. AAD](#microsoftaad)
> - [Microsoft. Dodatki](#microsoftaddons)
> - [Microsoft. ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft. Advisor](#microsoftadvisor)
> - [Microsoft. AgFoodPlatform](#microsoftagfoodplatform)
> - [Microsoft. AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft. AnalysisServices](#microsoftanalysisservices)
> - [Microsoft. ApiManagement](#microsoftapimanagement)
> - [Microsoft. AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. AppPlatform](#microsoftappplatform)
> - [Microsoft. zaświadczanie](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft. Autozarządzanie](#microsoftautomanage)
> - [Microsoft. Automation](#microsoftautomation)
> - [Microsoft. Automatyczna synchronizacja](#microsoftavs)
> - [Microsoft. Azure. Genewa](#microsoftazuregeneva)
> - [Microsoft. usługi azureactivedirectory](#microsoftazureactivedirectory)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft. AzureStack](#microsoftazurestack)
> - [Microsoft. AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft. BareMetalInfrastructure](#microsoftbaremetalinfrastructure)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft. rozliczenia](#microsoftbilling)
> - [Microsoft. BingMaps](#microsoftbingmaps)
> - [Microsoft. łańcucha bloków](#microsoftblockchain)
> - [Microsoft. BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft. plan](#microsoftblueprint)
> - [Microsoft. BotService](#microsoftbotservice)
> - [Microsoft. cache](#microsoftcache)
> - [Microsoft. Pojemność](#microsoftcapacity)
> - [Microsoft. CDN](#microsoftcdn)
> - [Microsoft. CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft. ChangeAnalysis](#microsoftchangeanalysis)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft. ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft. ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft. ClassicStorage](#microsoftclassicstorage)
> - [Microsoft. Codespaces](#microsoftcodespaces)
> - [Microsoft. CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft. Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft. ConnectedCache](#microsoftconnectedcache)
> - [Microsoft. zużycie](#microsoftconsumption)
> - [Microsoft. ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft. ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft. ContainerService](#microsoftcontainerservice)
> - [Microsoft. CostManagement](#microsoftcostmanagement)
> - [Microsoft. CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft. CustomProviders](#microsoftcustomproviders)
> - [Microsoft. D365CustomerInsights](#microsoftd365customerinsights)
> - [Microsoft. DataBox](#microsoftdatabox)
> - [Microsoft. DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft. datacegły](#microsoftdatabricks)
> - [Microsoft. datacatalog](#microsoftdatacatalog)
> - [Microsoft. DataFactory](#microsoftdatafactory)
> - [Microsoft. DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft. kontach datalakestore](#microsoftdatalakestore)
> - [Migracja Microsoft.](#microsoftdatamigration)
> - [Microsoft. dataprotection](#microsoftdataprotection)
> - [Microsoft. dataudział](#microsoftdatashare)
> - [Microsoft. DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft. DBforMySQL](#microsoftdbformysql)
> - [Microsoft. DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft. Deploymentmanager](#microsoftdeploymentmanager)
> - [Microsoft. DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft. DeviceUpdate](#microsoftdeviceupdate)
> - [Microsoft. DevOps](#microsoftdevops)
> - [Microsoft. DevSpaces](#microsoftdevspaces)
> - [Microsoft. wspólny](#microsoftdevtestlab)
> - [Microsoft. DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft. DomainRegistration](#microsoftdomainregistration)
> - [Microsoft. DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft. EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft. EventGrid](#microsofteventgrid)
> - [Microsoft. EventHub](#microsofteventhub)
> - [Microsoft. eksperymentowanie](#microsoftexperimentation)
> - [Microsoft. Falcon](#microsoftfalcon)
> - [Microsoft. Features](#microsoftfeatures)
> - [Microsoft. Gallery](#microsoftgallery)
> - [Microsoft. genomika](#microsoftgenomics)
> - [Microsoft. GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft. HanaOnAzure](#microsofthanaonazure)
> - [Microsoft. HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft. HDInsight](#microsofthdinsight)
> - [Microsoft. HealthcareApis](#microsofthealthcareapis)
> - [Microsoft. HybridCompute](#microsofthybridcompute)
> - [Microsoft. HybridData](#microsofthybriddata)
> - [Microsoft. HybridNetwork](#microsofthybridnetwork)
> - [Microsoft. Hydra](#microsofthydra)
> - [Microsoft. ImportExport](#microsoftimportexport)
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft. IoTCentral](#microsoftiotcentral)
> - [Microsoft. IoTSpaces](#microsoftiotspaces)
> - [Microsoft. kluczy — magazyn](#microsoftkeyvault)
> - [Microsoft. Kubernetes](#microsoftkubernetes)
> - [Microsoft. KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft. LabServices](#microsoftlabservices)
> - [Microsoft. Logic](#microsoftlogic)
> - [Microsoft. MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft. Maintenance](#microsoftmaintenance)
> - [Microsoft. ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft. ManagedNetwork](#microsoftmanagednetwork)
> - [Microsoft. ManagedServices](#microsoftmanagedservices)
> - [Microsoft. Management](#microsoftmanagement)
> - [Microsoft. Maps](#microsoftmaps)
> - [Microsoft. Marketplace](#microsoftmarketplace)
> - [Microsoft. MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft. MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft. Media](#microsoftmedia)
> - [Microsoft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft. Migrowanie](#microsoftmigrate)
> - [Microsoft. MixedReality](#microsoftmixedreality)
> - [Microsoft. NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft. notesy](#microsoftnotebooks)
> - [Microsoft. NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft. ObjectStore](#microsoftobjectstore)
> - [Microsoft. OffAzure](#microsoftoffazure)
> - [Microsoft. OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft. OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft. Komunikacja równorzędna](#microsoftpeering)
> - [Microsoft. PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft. Portal](#microsoftportal)
> - [Microsoft. PowerBI](#microsoftpowerbi)
> - [Microsoft. PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft. ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft. ProviderHub](#microsoftproviderhub)
> - [Microsoft. Quantum](#microsoftquantum)
> - [Microsoft. RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft. RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft. Relay](#microsoftrelay)
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
> - [Microsoft. ResourceHealth](#microsoftresourcehealth)
> - [Microsoft. resources](#microsoftresources)
> - [Microsoft. SaaS](#microsoftsaas)
> - [Microsoft. ScVmm](#microsoftscvmm)
> - [Microsoft. Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft. SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft. SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft. SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft. servicefabric](#microsoftservicefabric)
> - [Microsoft. ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft. Services](#microsoftservices)
> - [Microsoft. SignalRService](#microsoftsignalrservice)
> - [Microsoft. Singularity](#microsoftsingularity)
> - [Microsoft. SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft. Solutions](#microsoftsolutions)
> - [Microsoft. SQL](#microsoftsql)
> - [Microsoft. SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft. StorageCache](#microsoftstoragecache)
> - [Microsoft. StorageReplication](#microsoftstoragereplication)
> - [Microsoft. StorageSync](#microsoftstoragesync)
> - [Microsoft. StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft. StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft. StorSimple](#microsoftstorsimple)
> - [Microsoft. StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft. Subscription](#microsoftsubscription)
> - [Microsoft. Synapse](#microsoftsynapse)
> - [Microsoft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft. token](#microsofttoken)
> - [Microsoft. VirtualMachineImages](#microsoftvirtualmachineimages)
> - [Microsoft. VMware](#microsoftvmware)
> - [Microsoft. VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft. VnfManager](#microsoftvnfmanager)
> - [Microsoft. VSOnline](#microsoftvsonline)
> - [Microsoft. Web](#microsoftweb)
> - [Microsoft. WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft. WindowsESU](#microsoftwindowsesu)
> - [Microsoft. WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft. Monitor obciążenia został](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft. AAD

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | DomainServices | Tak |
> | DomainServices / oucontainer | Nie |

## <a name="microsoftaddons"></a>Microsoft. Dodatki

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | supportProviders | Nie |

## <a name="microsoftadhybridhealthservice"></a>Microsoft. ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | aadsupportcases | Nie |
> | addsservices | Nie |
> | pracownicy | Nie |
> | anonymousapiusers | Nie |
> | konfiguracja | Nie |
> | dzienniki | Nie |
> | raporty | Nie |
> | servicehealthmetrics | Nie |
> | services | Nie |

## <a name="microsoftadvisor"></a>Microsoft. Advisor

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | advisorScore | Nie |
> | komputerów | Nie |
> | generateRecommendations | Nie |
> | metadane | Nie |
> | zalecenia | Nie |
> | pominięć | Nie |

## <a name="microsoftagfoodplatform"></a>Microsoft. AgFoodPlatform

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | farmBeats | Tak |

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | actionRules | Tak |
> | alerts | Nie |
> | alertsList | Nie |
> | alertsMetaData | Nie |
> | alertsSummary | Nie |
> | alertsSummaryList | Nie |
> | smartDetectorAlertRules | Tak |
> | smartGroups | Nie |

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | serwerem | Tak |

## <a name="microsoftapimanagement"></a>Microsoft. ApiManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | reportFeedback | Nie |
> | usługa | Tak |
> | validateServiceName | Nie |

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | configurationStores | Tak |
> | configurationStores / eventGridFilters | Nie |
> | configurationStores/wartości | Nie |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Spring | Tak |
> | Sprężynowe/aplikacje | Nie |
> | Sprężynowe/aplikacje/wdrożenia | Nie |

## <a name="microsoftattestation"></a>Microsoft. zaświadczanie

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | attestationProviders | Tak |
> | defaultProviders | Nie |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | accessReviewScheduleDefinitions | Nie |
> | accessReviewScheduleSettings | Nie |
> | classicAdministrators | Nie |
> | aliasy | Nie |
> | denyAssignments | Nie |
> | elevateAccess | Nie |
> | findOrphanRoleAssignments | Nie |
> | Zamki | Nie |
> | uprawnienia | Nie |
> | policyAssignments | Nie |
> | policyDefinitions | Nie |
> | policyExemptions | Nie |
> | policySetDefinitions | Nie |
> | privateLinkAssociations | Nie |
> | providerOperations | Nie |
> | resourceManagementPrivateLinks | Tak |
> | roleAssignments | Nie |
> | roleAssignmentsUsageMetrics | Nie |
> | roleDefinitions | Nie |

## <a name="microsoftautomanage"></a>Microsoft. Autozarządzanie

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Konta | Tak |
> | configurationProfileAssignments | Nie |
> | configurationProfilePreferences | Tak |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | automationAccounts | Tak |
> | automationAccounts/konfiguracje | Tak |
> | automationAccounts/zadania | Nie |
> | automationAccounts / privateEndpointConnectionProxies | Nie |
> | automationAccounts / privateEndpointConnections | Nie |
> | automationAccounts / privateLinkResources | Nie |
> | automationAccounts/elementy Runbook | Tak |
> | automationAccounts / softwareUpdateConfigurations | Nie |
> | automationAccounts/elementy webhook | Nie |

## <a name="microsoftavs"></a>Microsoft. Automatyczna synchronizacja

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | privateClouds | Tak |
> | privateClouds/Dodatki | Nie |
> | privateClouds/autoryzacje | Nie |
> | privateClouds/klastry | Nie |
> | privateClouds / globalReachConnections | Nie |
> | privateClouds / hcxEnterpriseSites | Nie |
> | privateClouds / workloadNetworks | Nie |
> | privateClouds / workloadNetworks / dhcpConfigurations | Nie |
> | privateClouds/workloadNetworks/bramy | Nie |
> | privateClouds / workloadNetworks / portMirroringProfiles | Nie |
> | privateClouds/workloadNetworks/segmenty | Nie |
> | privateClouds/workloadNetworks/virtualMachines | Nie |
> | privateClouds / workloadNetworks / vmGroups | Nie |

## <a name="microsoftazuregeneva"></a>Microsoft. Azure. Genewa

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | wiejski | Nie |
> | środowiska/konta | Nie |
> | środowiska/konta/przestrzenie nazw | Nie |
> | środowiska/konta/przestrzenie nazw/konfiguracje | Nie |

## <a name="microsoftazureactivedirectory"></a>Microsoft. usługi azureactivedirectory

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | b2cDirectories | Tak |
> | b2ctenants | Nie |
> | guestUsages | Tak |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Kontrolery datacontrollers | Tak |
> | postgresInstances | Tak |
> | sqlManagedInstances | Tak |
> | sqlServerInstances | Tak |
> | sqlServerRegistrations | Tak |
> | sqlServerRegistrations/SQLServers | Nie |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | cloudManifestFiles | Nie |
> | edgeSubscriptions | Tak |
> | linkedSubscriptions | Tak |
> | rejestracje | Tak |
> | rejestracje/customerSubscriptions | Nie |
> | rejestracje/produkty | Nie |

## <a name="microsoftazurestackhci"></a>Microsoft. AzureStackHCI

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | oparty | Tak |

## <a name="microsoftbaremetalinfrastructure"></a>Microsoft. BareMetalInfrastructure

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | bareMetalInstances | Tak |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | batchAccounts | Tak |
> | batchAccounts/certyfikaty | Nie |
> | batchAccounts/pule | Nie |

## <a name="microsoftbilling"></a>Microsoft. rozliczenia

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | billingAccounts | Nie |
> | billingAccounts/umowy | Nie |
> | billingAccounts / billingPermissions | Nie |
> | billingAccounts / billingProfiles | Nie |
> | billingAccounts / billingProfiles / billingPermissions | Nie |
> | billingAccounts / billingProfiles / billingRoleAssignments | Nie |
> | billingAccounts / billingProfiles / billingRoleDefinitions | Nie |
> | billingAccounts / billingProfiles / billingSubscriptions | Nie |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | Nie |
> | billingAccounts/billingProfiles/klienci | Nie |
> | billingAccounts/billingProfiles/instrukcje | Nie |
> | billingAccounts/billingProfiles/faktury | Nie |
> | billingAccounts/billingProfiles/faktury/arkusza cen | Nie |
> | billingAccounts/billingProfiles/faktury/transakcje | Nie |
> | billingAccounts / billingProfiles / invoiceSections | Nie |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | Nie |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | Nie |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | Nie |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | Nie |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | Nie |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | Nie |
> | billingAccounts/billingProfiles/invoiceSections/produkty | Nie |
> | billingAccounts/billingProfiles/invoiceSections/produkty/transfer | Nie |
> | billingAccounts/billingProfiles/invoiceSections/produkty/updateAutoRenew | Nie |
> | billingAccounts/billingProfiles/invoiceSections/Transactions | Nie |
> | billingAccounts/billingProfiles/invoiceSections/transfery | Nie |
> | billingAccounts / BillingProfiles / patchOperations | Nie |
> | billingAccounts / billingProfiles / paymentMethods | Nie |
> | billingAccounts/billingProfiles/zasady | Nie |
> | billingAccounts/billingProfiles/arkusza cen | Nie |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | Nie |
> | billingAccounts/billingProfiles/produkty | Nie |
> | billingAccounts/billingProfiles/rezerwacje | Nie |
> | billingAccounts/billingProfiles/Transactions | Nie |
> | billingAccounts / billingProfiles / validateDetachPaymentMethodEligibility | Nie |
> | billingAccounts / billingRoleAssignments | Nie |
> | billingAccounts / billingRoleDefinitions | Nie |
> | billingAccounts / billingSubscriptions | Nie |
> | billingAccounts/billingSubscriptions/faktury | Nie |
> | billingAccounts / createBillingRoleAssignment | Nie |
> | billingAccounts / createInvoiceSectionOperations | Nie |
> | billingAccounts/klienci | Nie |
> | billingAccounts/klienci/billingPermissions | Nie |
> | billingAccounts/klienci/billingSubscriptions | Nie |
> | billingAccounts/klienci/initiateTransfer | Nie |
> | billingAccounts/klienci/zasady | Nie |
> | billingAccounts/klienci/produkty | Nie |
> | billingAccounts/klienci/transakcje | Nie |
> | billingAccounts/klienci/transfery | Nie |
> | billingAccounts/działy | Nie |
> | billingAccounts/działy/billingPermissions | Nie |
> | billingAccounts/działy/billingRoleAssignments | Nie |
> | billingAccounts/działy/billingRoleDefinitions | Nie |
> | billingAccounts / enrollmentAccounts | Nie |
> | billingAccounts / enrollmentAccounts / billingPermissions | Nie |
> | billingAccounts / enrollmentAccounts / billingRoleAssignments | Nie |
> | billingAccounts / enrollmentAccounts / billingRoleDefinitions | Nie |
> | billingAccounts/faktury | Nie |
> | billingAccounts/faktury/transakcje | Nie |
> | billingAccounts / invoiceSections | Nie |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | Nie |
> | billingAccounts / invoiceSections / billingSubscriptions | Nie |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | Nie |
> | billingAccounts/invoiceSections/Podnieś poziom | Nie |
> | billingAccounts / invoiceSections / initiateTransfer | Nie |
> | billingAccounts / invoiceSections / patchOperations | Nie |
> | billingAccounts / invoiceSections / productMoveOperations | Nie |
> | billingAccounts/invoiceSections/produkty | Nie |
> | billingAccounts/invoiceSections/produkty/transfer | Nie |
> | billingAccounts/invoiceSections/produkty/updateAutoRenew | Nie |
> | billingAccounts/invoiceSections/Transactions | Nie |
> | billingAccounts/invoiceSections/transfery | Nie |
> | billingAccounts / lineOfCredit | Nie |
> | billingAccounts / patchOperations | Nie |
> | billingAccounts / paymentMethods | Nie |
> | billingAccounts/produkty | Nie |
> | billingAccounts/rezerwacje | Nie |
> | billingAccounts/transakcje | Nie |
> | billingPeriods | Nie |
> | billingPermissions | Nie |
> | billingProperty | Nie |
> | billingRoleAssignments | Nie |
> | billingRoleDefinitions | Nie |
> | createBillingRoleAssignment | Nie |
> | działów | Nie |
> | enrollmentAccounts | Nie |
> | faktur | Nie |
> | sunięcia | Nie |
> | transfery/acceptTransfer | Nie |
> | transfery/declineTransfer | Nie |
> | transfery/operationStatus | Nie |
> | transfery/validateTransfer | Nie |
> | validateAddress | Nie |

## <a name="microsoftbingmaps"></a>Microsoft. BingMaps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | mapApis | Tak |
> | updateCommunicationPreference | Nie |

## <a name="microsoftblockchain"></a>Microsoft. łańcucha bloków

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | blockchainMembers | Tak |
> | cordaMembers | Tak |
> | obserwatorów | Tak |

## <a name="microsoftblockchaintokens"></a>Microsoft. BlockchainTokens

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | TokenServices | Tak |
> | TokenServices / BlockchainNetworks | Nie |
> | TokenServices/grupy | Nie |
> | TokenServices/grupy/konta | Nie |
> | TokenServices / TokenTemplates | Nie |

## <a name="microsoftblueprint"></a>Microsoft. plan

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | blueprintAssignments | Nie |
> | blueprintAssignments / assignmentOperations | Nie |
> | blueprintAssignments/operacje | Nie |
> | plany | Nie |
> | plany/artefakty | Nie |
> | plany/wersje | Nie |
> | plany/wersje/artefakty | Nie |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | botServices | Tak |
> | botServices/kanały | Nie |
> | botServices/połączenia | Nie |
> | języki | Nie |
> | szablonów | Nie |

## <a name="microsoftcache"></a>Microsoft. cache

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Redis | Tak |
> | Redis/EventGridFilters | Nie |
> | Redis/privateEndpointConnectionProxies | Nie |
> | Redis/privateEndpointConnectionProxies/Weryfikuj | Nie |
> | Redis/privateEndpointConnections | Nie |
> | Redis/privateLinkResources | Nie |
> | redisEnterprise | Tak |
> | RedisEnterprise / privateEndpointConnectionProxies | Nie |
> | RedisEnterprise/privateEndpointConnectionProxies/Weryfikuj | Nie |
> | RedisEnterprise / privateEndpointConnections | Nie |
> | RedisEnterprise / privateLinkResources | Nie |

## <a name="microsoftcapacity"></a>Microsoft. Pojemność

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | appliedReservations | Nie |
> | autoQuotaIncrease | Nie |
> | calculateExchange | Nie |
> | calculatePrice | Nie |
> | calculatePurchasePrice | Nie |
> | wykazy | Nie |
> | commercialReservationOrders | Nie |
> | zamian | Nie |
> | ownReservations | Nie |
> | placePurchaseOrder | Nie |
> | reservationOrders | Nie |
> | reservationOrders / calculateRefund | Nie |
> | reservationOrders/Scal | Nie |
> | reservationOrders/rezerwacje | Nie |
> | reservationOrders/rezerwacje/poprawki | Nie |
> | reservationOrders/Return | Nie |
> | reservationOrders/Split | Nie |
> | reservationOrders/swap | Nie |
> | dokonując | Nie |
> | resourceProviders | Nie |
> | zasoby | Nie |
> | validateReservationOrder | Nie |

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Nie |
> | CdnWebApplicationFirewallPolicies | Tak |
> | edgenodes | Nie |
> | Profil | Tak |
> | Profile/punkty końcowe | Tak |
> | Profile/punkty końcowe/customdomains | Nie |
> | Profile/punkty końcowe/origingroups | Nie |
> | Profile/punkty końcowe/źródła | Nie |
> | validateProbe | Nie |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | certificateOrders | Tak |
> | certificateOrders/certyfikaty | Nie |
> | validateCertificateRegistrationInformation | Nie |

## <a name="microsoftchangeanalysis"></a>Microsoft. ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | profil | Nie |
> | resourceChanges | Nie |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | możliwości | Nie |
> | domainNames | Tak |
> | domainNames/możliwości | Nie |
> | domainNames / internalLoadBalancers | Nie |
> | domainNames/servicecertificates | Nie |
> | domainNames/miejsca | Nie |
> | domainNames/gniazda/role | Nie |
> | domainNames/szczeliny/role/metricDefinitions | Nie |
> | domainNames/szczeliny/role/metryki | Nie |
> | moveSubscriptionResources | Nie |
> | operatingSystemFamilies | Nie |
> | operatingSystems | Nie |
> | quotas | Nie |
> | resourceTypes | Nie |
> | validateSubscriptionMoveAvailability | Nie |
> | virtualMachines | Tak |
> | virtualMachines/diagnosticSettings | Nie |
> | virtualMachines/metricDefinitions | Nie |
> | virtualMachines/metryki | Nie |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft. ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | classicInfrastructureResources | Nie |

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | możliwości | Nie |
> | expressRouteCrossConnections | Nie |
> | expressRouteCrossConnections/Komunikacja równorzędna | Nie |
> | gatewaySupportedDevices | Nie |
> | networkSecurityGroups | Tak |
> | quotas | Nie |
> | reservedIps | Tak |
> | virtualNetworks | Tak |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | Nie |
> | virtualNetworks/virtualNetworkPeerings | Nie |

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | możliwości | Nie |
> | dysku | Nie |
> | images | Nie |
> | osImages | Nie |
> | osPlatformImages | Nie |
> | publicImages | Nie |
> | quotas | Nie |
> | storageAccounts | Tak |
> | storageAccounts/blobServices | Nie |
> | storageAccounts/fileServices | Nie |
> | storageAccounts/metricDefinitions | Nie |
> | storageAccounts/metryki | Nie |
> | storageAccounts/queueServices | Nie |
> | storageAccounts/usługi | Nie |
> | storageAccounts/usługi/diagnosticSettings | Nie |
> | storageAccounts/usługi/metricDefinitions | Nie |
> | storageAccounts/usługi/metryki | Nie |
> | storageAccounts/tableServices | Nie |
> | storageAccounts/vmImages | Nie |
> | vmImages | Nie |

## <a name="microsoftcodespaces"></a>Microsoft. Codespaces

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | pakiety | Tak |
> | registeredSubscriptions | Nie |

## <a name="microsoftcognitiveservices"></a>Microsoft. CognitiveServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Konta | Tak |
> | konta/privateEndpointConnectionProxies | Nie |
> | konta/privateEndpointConnections | Nie |
> | konta/privateLinkResources | Nie |

## <a name="microsoftcommerce"></a>Microsoft. Commerce

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | RateCard | Nie |
> | UsageAggregates | Nie |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | availabilitySets | Tak |
> | cloudServices | Tak |
> | cloudServices / networkInterfaces | Nie |
> | cloudServices/adresów publicipaddress | Nie |
> | cloudServices / roleInstances | Nie |
> | cloudServices / roleInstances / networkInterfaces | Nie |
> | cloudServices/role | Nie |
> | diskAccesses | Tak |
> | diskEncryptionSets | Tak |
> | dysku | Tak |
> | znajduj | Tak |
> | Galerie/aplikacje | Nie |
> | Galerie/aplikacje/wersje | Nie |
> | Galerie/obrazy | Nie |
> | Galerie/obrazy/wersje | Nie |
> | hostGroups | Tak |
> | hostGroups/hosty | Tak |
> | images | Tak |
> | proximityPlacementGroups | Tak |
> | restorePointCollections | Tak |
> | restorePointCollections / restorePoints | Nie |
> | sharedVMExtensions | Tak |
> | sharedVMExtensions/wersje | Nie |
> | sharedVMImages | Tak |
> | sharedVMImages/wersje | Nie |
> | Migawki | Tak |
> | sshPublicKeys | Tak |
> | virtualMachines | Tak |
> | virtualMachines/rozszerzenia | Tak |
> | virtualMachines/metricDefinitions | Nie |
> | virtualMachines/runCommands | Tak |
> | virtualMachineScaleSets | Tak |
> | virtualMachineScaleSets/rozszerzenia | Nie |
> | virtualMachineScaleSets/networkInterfaces | Nie |
> | virtualMachineScaleSets/adresów publicipaddress | Nie |
> | virtualMachineScaleSets/virtualMachines | Nie |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | Nie |

## <a name="microsoftconnectedcache"></a>Microsoft. ConnectedCache

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | CacheNodes | Tak |

## <a name="microsoftconsumption"></a>Microsoft. zużycie

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | AggregatedCost | Nie |
> | Salda | Nie |
> | Budżety | Nie |
> | Opłaty | Nie |
> | CostTags | Nie |
> | środki | Nie |
> | zdarzenia | Nie |
> | Prognozy | Nie |
> | ilości | Nie |
> | Platform handlowych | Nie |
> | Arkusze cen | Nie |
> | wyrobów | Nie |
> | ReservationDetails | Nie |
> | ReservationRecommendationDetails | Nie |
> | ReservationRecommendations | Nie |
> | ReservationSummaries | Nie |
> | ReservationTransactions | Nie |
> | Tagi | Nie |
> | dzierżaw | Nie |
> | Terminologia | Nie |
> | UsageDetails (Szczegóły użycia) | Nie |

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | containerGroups | Tak |
> | serviceAssociationLinks | Nie |

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | wołuje | Tak |
> | rejestry/agentPools | Tak |
> | rejestry/kompilacje | Nie |
> | rejestry/kompilacje/anulowanie | Nie |
> | rejestry/kompilacje/getLogLink | Nie |
> | rejestry/buildTasks | Tak |
> | rejestry/buildTasks/kroki | Nie |
> | rejestry/eventGridFilters | Nie |
> | rejestry/exportPipelines | Nie |
> | rejestry/generateCredentials | Nie |
> | rejestry/getBuildSourceUploadUrl | Nie |
> | rejestry/getpoświadczenia | Nie |
> | rejestry/importImage | Nie |
> | rejestry/importPipelines | Nie |
> | rejestry/pipelineRuns | Nie |
> | rejestry/privateEndpointConnectionProxies | Nie |
> | rejestry/privateEndpointConnectionProxies/weryfikacja | Nie |
> | rejestry/privateEndpointConnections | Nie |
> | rejestry/privateLinkResources | Nie |
> | rejestry/queueBuild | Nie |
> | rejestry/regenerateCredential | Nie |
> | rejestry/regenerateCredentials | Nie |
> | rejestry/replikacje | Tak |
> | rejestry/uruchomienia | Nie |
> | rejestry/uruchomienia/Anuluj | Nie |
> | rejestry/scheduleRun | Nie |
> | rejestry/scopeMaps | Nie |
> | rejestry/taskRuns | Nie |
> | rejestry/zadania | Tak |
> | rejestry/tokeny | Nie |
> | rejestry/updatePolicies | Nie |
> | rejestry/elementy webhook | Tak |
> | rejestry/elementy webhook/getCallbackConfig | Nie |
> | rejestry/elementy webhook/polecenie ping | Nie |

## <a name="microsoftcontainerservice"></a>Microsoft. ContainerService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | containerServices | Tak |
> | managedClusters | Tak |
> | openShiftManagedClusters | Tak |

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Alerty | Nie |
> | BillingAccounts | Nie |
> | Budżety | Nie |
> | CloudConnectors | Nie |
> | Łączniki | Tak |
> | costAllocationRules | Nie |
> | Działy | Nie |
> | Wymiary | Nie |
> | EnrollmentAccounts | Nie |
> | Eksporty | Nie |
> | ExternalBillingAccounts | Nie |
> | ExternalBillingAccounts/alerty | Nie |
> | ExternalBillingAccounts/wymiary | Nie |
> | ExternalBillingAccounts/Prognoza | Nie |
> | ExternalBillingAccounts/zapytanie | Nie |
> | ExternalSubscriptions | Nie |
> | ExternalSubscriptions/alerty | Nie |
> | ExternalSubscriptions/wymiary | Nie |
> | ExternalSubscriptions/Prognoza | Nie |
> | ExternalSubscriptions/zapytanie | Nie |
> | Prognoza | Nie |
> | Insights | Nie |
> | Zapytanie | Nie |
> | zarejestruj | Nie |
> | Reportconfigs | Nie |
> | Raporty | Nie |
> | Ustawienia | Nie |
> | showbackRules | Nie |
> | Widoki | Nie |

## <a name="microsoftcustomerlockbox"></a>Microsoft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | żądań | Nie |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | związku | Nie |
> | resourceProviders | Tak |

## <a name="microsoftd365customerinsights"></a>Microsoft. D365CustomerInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Liczba | Tak |

## <a name="microsoftdatabox"></a>Microsoft. DataBox

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | zadania | Tak |

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | Tak |

## <a name="microsoftdatabricks"></a>Microsoft. datacegły

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | obszary robocze | Tak |
> | obszary robocze/dbworkspaces | Nie |
> | obszary robocze/virtualNetworkPeerings | Nie |

## <a name="microsoftdatacatalog"></a>Microsoft. datacatalog

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | wykazy | Tak |

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Fabryki datafactors | Tak |
> | datafactors/diagnosticSettings | Nie |
> | datafactors/metricDefinitions | Nie |
> | dataFactorySchema | Nie |
> | fabryki | Tak |
> | fabryki/integrationRuntimes | Nie |

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Konta | Tak |
> | konta/dataLakeStoreAccounts | Nie |
> | konta/storageAccounts | Nie |
> | konta/storageAccounts/kontenery | Nie |
> | konta/transferAnalyticsUnits | Nie |

## <a name="microsoftdatalakestore"></a>Microsoft. kontach datalakestore

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Konta | Tak |
> | konta/eventGridFilters | Nie |
> | konta/firewallRules | Nie |

## <a name="microsoftdatamigration"></a>Migracja Microsoft.

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | services | Tak |
> | usługi/projekty | Tak |

## <a name="microsoftdataprotection"></a>Microsoft. dataprotection

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | BackupVaults | Tak |
> | ResourceOperationGateKeepers | Tak |

## <a name="microsoftdatashare"></a>Microsoft. dataudział

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Konta | Tak |
> | konta/udziały | Nie |
> | konta/udziały/zestawy danych | Nie |
> | konta/udziały/zaproszenia | Nie |
> | konta/udziały/providersharesubscriptions | Nie |
> | konta/udziały/synchronizationSettings | Nie |
> | konta/sharesubscriptions | Nie |
> | konta/sharesubscriptions/consumerSourceDataSets | Nie |
> | konta/sharesubscriptions/datasetmappings | Nie |
> | konta/sharesubscriptions/wyzwalacze | Nie |

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | serwerem | Tak |
> | serwery/doradcy | Nie |
> | serwery/klucze | Nie |
> | serwery/privateEndpointConnectionProxies | Nie |
> | serwery/privateEndpointConnections | Nie |
> | serwery/privateLinkResources | Nie |
> | serwery/queryTexts | Nie |
> | serwery/recoverableServers | Nie |
> | serwery/Start | Nie |
> | serwery/Zatrzymaj | Nie |
> | serwery/topQueryStatistics | Nie |
> | serwery/virtualNetworkRules | Nie |
> | serwery/waitStatistics | Nie |

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | flexibleServers | Tak |
> | serwerem | Tak |
> | serwery/doradcy | Nie |
> | serwery/klucze | Nie |
> | serwery/privateEndpointConnectionProxies | Nie |
> | serwery/privateEndpointConnections | Nie |
> | serwery/privateLinkResources | Nie |
> | serwery/queryTexts | Nie |
> | serwery/recoverableServers | Nie |
> | serwery/Start | Nie |
> | serwery/Zatrzymaj | Nie |
> | serwery/topQueryStatistics | Nie |
> | serwery/uaktualnienie | Nie |
> | serwery/virtualNetworkRules | Nie |
> | serwery/waitStatistics | Nie |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | flexibleServers | Tak |
> | serverGroups | Tak |
> | serwerem | Tak |
> | serwery/doradcy | Nie |
> | serwery/klucze | Nie |
> | serwery/privateEndpointConnectionProxies | Nie |
> | serwery/privateEndpointConnections | Nie |
> | serwery/privateLinkResources | Nie |
> | serwery/queryTexts | Nie |
> | serwery/recoverableServers | Nie |
> | serwery/topQueryStatistics | Nie |
> | serwery/virtualNetworkRules | Nie |
> | serwery/waitStatistics | Nie |
> | serversv2 | Tak |

## <a name="microsoftdeploymentmanager"></a>Microsoft. Deploymentmanager

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | artifactSources | Tak |
> | wprowadzanie | Tak |
> | servicetopologie | Tak |
> | servicetopologie/usługi | Tak |
> | servicetopologie/usługi/serviceunits | Tak |
> | kroki | Tak |

## <a name="microsoftdesktopvirtualization"></a>Microsoft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | applicationgroups | Tak |
> | applicationgroups/aplikacje | Nie |
> | applicationgroups/komputery stacjonarne | Nie |
> | applicationgroups / startmenuitems | Nie |
> | hostpools | Tak |
> | hostpools / msixpackages | Nie |
> | hostpools / sessionhosts | Nie |
> | hostpools / sessionhosts / usersessions | Nie |
> | hostpools / usersessions | Nie |
> | obszary robocze | Tak |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | ElasticPools | Tak |
> | ElasticPools / IotHubTenants | Tak |
> | ElasticPools/IotHubTenants/securitySettings | Nie |
> | IotHubs | Tak |
> | IotHubs/eventGridFilters | Nie |
> | IotHubs/securitySettings | Nie |
> | ProvisioningServices | Tak |
> | użycia | Nie |

## <a name="microsoftdeviceupdate"></a>Microsoft. DeviceUpdate

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Konta | Tak |
> | konta/wystąpienia | Tak |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | potoki | Tak |

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | kontrolery | Tak |

## <a name="microsoftdevtestlab"></a>Microsoft. wspólny

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | labcenters | Tak |
> | Labs | Tak |
> | laboratoria/środowiska | Tak |
> | laboratoria/servicerunner | Tak |
> | Labs/virtualMachines | Tak |
> | Uruchamianie | Tak |

## <a name="microsoftdigitaltwins"></a>Microsoft. DigitalTwins

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | digitalTwinsInstances | Tak |
> | digitalTwinsInstances/punkty końcowe | Nie |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | databaseAccountNames | Nie |
> | databaseAccounts | Tak |
> | restorableDatabaseAccounts | Nie |

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | 3.x | Tak |
> | domeny/domainOwnershipIdentifiers | Nie |
> | generateSsoRequest | Nie |
> | topLevelDomains | Nie |
> | validateDomainRegistrationInformation | Nie |

## <a name="microsoftdynamicslcs"></a>Microsoft. DynamicsLcs

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | lcsprojects | Nie |
> | lcsprojects / clouddeployments | Nie |
> | lcsprojects/łączniki | Nie |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | services | Tak |

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | 3.x | Tak |
> | domeny/tematy | Nie |
> | eventSubscriptions | Nie |
> | extensionTopics | Nie |
> | partnerNamespaces | Tak |
> | partnerNamespaces/eventChannels | Nie |
> | partnerRegistrations | Tak |
> | partnerTopics | Tak |
> | partnerTopics / eventSubscriptions | Nie |
> | systemTopics | Tak |
> | systemTopics / eventSubscriptions | Nie |
> | opisano | Tak |
> | topicTypes | Nie |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | oparty | Tak |
> | przestrzeni | Tak |
> | przestrzenie nazw/reguł autoryzacji | Nie |
> | przestrzenie nazw/disasterrecoveryconfigs | Nie |
> | przestrzenie nazw/eventhubs | Nie |
> | przestrzenie nazw/eventhubs/reguł autoryzacji | Nie |
> | przestrzenie nazw/eventhubs/consumergroups | Nie |
> | przestrzenie nazw/networkrulesets | Nie |
> | przestrzenie nazw/privateEndpointConnections | Nie |

## <a name="microsoftexperimentation"></a>Microsoft. eksperymentowanie

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | experimentWorkspaces | Tak |

## <a name="microsoftfalcon"></a>Microsoft. Falcon

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | przestrzeni | Tak |

## <a name="microsoftfeatures"></a>Microsoft. Features

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | featureProviders | Nie |
> | funkcje | Nie |
> | dostawców | Nie |
> | subscriptionFeatureRegistrations | Nie |

## <a name="microsoftgallery"></a>Microsoft. Gallery

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | pisaną | Nie |
> | galleryitems | Nie |
> | generateartifactaccessuri | Nie |
> | Moje obszary | Nie |
> | Moje obszary/obszary | Nie |
> | Moje obszary/obszary/obszary | Nie |
> | Moje obszary/obszary/obszary/galleryitems | Nie |
> | Moje obszary/obszary/galleryitems | Nie |
> | Moje obszary/galleryitems | Nie |
> | zarejestruj | Nie |
> | zasoby | Nie |
> | retrieveresourcesbyid | Nie |

## <a name="microsoftgenomics"></a>Microsoft. genomika

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Konta | Tak |

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | autoManagedAccounts | Tak |
> | autoManagedVmConfigurationProfiles | Tak |
> | configurationProfileAssignments | Nie |
> | guestConfigurationAssignments | Nie |
> | programy | Nie |
> | softwareUpdateProfile | Nie |
> | softwareUpdates | Nie |

## <a name="microsofthanaonazure"></a>Microsoft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | hanaInstances | Tak |
> | sapMonitors | Tak |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft. HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Modułów dedicatedhsms | Tak |

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | oparty | Tak |
> | klastry/aplikacje | Nie |

## <a name="microsofthealthcareapis"></a>Microsoft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | services | Tak |
> | usługi/iomtconnectors | Nie |
> | usługi/iomtconnectors/połączenia | Nie |
> | usługi/iomtconnectors/mapowania | Nie |
> | usługi/privateEndpointConnectionProxies | Nie |
> | usługi/privateEndpointConnections | Nie |
> | usługi/privateLinkResources | Nie |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Win64 | Tak |
> | Maszyny/assessPatches | Nie |
> | Maszyny/rozszerzenia | Tak |
> | Maszyny/installPatches | Nie |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | datamanagers | Tak |

## <a name="microsofthybridnetwork"></a>Microsoft. HybridNetwork

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | devices | Tak |
> | networkFunctions | Tak |
> | networkFunctionVendors | Nie |
> | registeredSubscriptions | Nie |
> | dostawców | Nie |
> | dostawcy/vendorSkus | Nie |
> | dostawcy/vendorSkus/previewSubscriptions | Nie |
> | virtualNetworkFunctions | Tak |
> | virtualNetworkFunctionVendors | Nie |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | komponentów | Tak |
> | networkScopes | Tak |

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | zadania | Tak |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | diagnosticSettings | Nie |
> | diagnosticSettingsCategories | Nie |

## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | appTemplates | Nie |
> | IoTApps | Tak |

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Graph | Tak |

## <a name="microsoftkeyvault"></a>Microsoft. kluczy — magazyn

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | deletedVaults | Nie |
> | hsmPools | Tak |
> | managedHSMs | Tak |
> | magazynów | Tak |
> | magazyny/accessPolicies | Nie |
> | magazyny/eventGridFilters | Nie |
> | magazyny/klucze | Nie |
> | magazyny/klucze/wersje | Nie |
> | magazyny/wpisy tajne | Nie |

## <a name="microsoftkubernetes"></a>Microsoft. Kubernetes

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | connectedClusters | Tak |
> | registeredSubscriptions | Nie |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft. KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | rozszerzenia | Nie |
> | sourceControlConfigurations | Nie |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | oparty | Tak |
> | klastry/attacheddatabaseconfigurations | Nie |
> | klastry/bazy danych | Nie |
> | klastry/bazy danych/połączenia Databases | Nie |
> | klastry/bazy danych/eventhubconnections | Nie |
> | klastry/bazy danych/principalassignments | Nie |
> | klastry/połączenia dataconnections | Nie |
> | klastry/principalassignments | Nie |
> | klastry/sharedidentities | Nie |

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | labaccounts | Tak |
> | users | Nie |

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | hostingEnvironments | Tak |
> | integrationAccounts | Tak |
> | integrationServiceEnvironments | Tak |
> | integrationServiceEnvironments/managedApis | Tak |
> | isolatedEnvironments | Tak |
> | przebieg | Tak |

## <a name="microsoftmachinelearning"></a>Microsoft. MachineLearning

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | commitmentPlans | Tak |
> | webServices | Tak |
> | Obszary robocze | Tak |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | obszary robocze | Tak |
> | obszary robocze/batchEndpoints | Tak |
> | obszary robocze/batchEndpoints/wdrożenia | Tak |
> | obszary robocze/kody | Nie |
> | obszary robocze/kody/wersje | Nie |
> | obszary robocze/obliczenia | Nie |
> | obszary robocze/magazyny danych | Nie |
> | obszary robocze/eventGridFilters | Nie |
> | obszary robocze/zadania | Nie |
> | obszary robocze/labelingJobs | Nie |
> | obszary robocze/linkedServices | Nie |
> | obszary robocze/modele | Nie |
> | obszary robocze/modele/wersje | Nie |
> | obszary robocze/onlineEndpoints | Tak |
> | obszary robocze/onlineEndpoints/wdrożenia | Tak |

## <a name="microsoftmaintenance"></a>Microsoft. Maintenance

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | applyUpdates | Nie |
> | configurationAssignments | Nie |
> | maintenanceConfigurations | Tak |
> | publicMaintenanceConfigurations | Nie |
> | aktualizacje | Nie |

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Tożsamości | Nie |
> | Resourceidentity | Tak |

## <a name="microsoftmanagednetwork"></a>Microsoft. ManagedNetwork

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | managedNetworks | Tak |
> | managedNetworks / managedNetworkGroups | Tak |
> | managedNetworks / managedNetworkPeeringPolicies | Tak |
> | powiadomienie | Tak |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions | Nie |
> | registrationAssignments | Nie |
> | registrationDefinitions | Nie |

## <a name="microsoftmanagement"></a>Microsoft. Management

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | getentities | Nie |
> | managementGroups | Nie |
> | managementGroups/ustawienia | Nie |
> | zasoby | Nie |
> | startTenantBackfill | Nie |
> | tenantBackfillStatus | Nie |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Konta | Tak |
> | konta/eventGridFilters | Nie |
> | konta/privateAtlases | Tak |

## <a name="microsoftmarketplace"></a>Microsoft. Marketplace

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | macc | Nie |
> | otrzymane | Nie |
> | offerTypes | Nie |
> | offerTypes/wydawcy | Nie |
> | offerTypes/wydawcy/oferty | Nie |
> | offerTypes/wydawcy/oferty/plany | Nie |
> | offerTypes/wydawcy/oferty/plany/umowy | Nie |
> | offerTypes/wydawcy/oferty/plany/konfiguracje | Nie |
> | offerTypes/wydawcy/oferty/plany/konfiguracje/importImage | Nie |
> | privategalleryitems | Nie |
> | privateStoreClient | Nie |
> | privateStores | Nie |
> | privateStores/oferty | Nie |
> | wyrobów | Nie |
> | wydawcy | Nie |
> | wydawcy/oferty | Nie |
> | wydawcy/oferty/zmiany | Nie |
> | zarejestruj | Nie |

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | classicDevServices | Tak |
> | updateCommunicationPreference | Nie |

## <a name="microsoftmarketplaceordering"></a>Microsoft. MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | kontrakt | Nie |
> | offertypes | Nie |

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | MediaServices | Tak |
> | MediaServices/accountFilters | Nie |
> | MediaServices/zasoby | Nie |
> | MediaServices/assetFilters | Nie |
> | MediaServices/contentKeyPolicies | Nie |
> | MediaServices/eventGridFilters | Nie |
> | MediaServices/liveEventOperations | Nie |
> | MediaServices/liveEvents | Tak |
> | MediaServices/liveEvents/liveOutputs | Nie |
> | MediaServices/liveOutputOperations | Nie |
> | MediaServices/mediaGraphs | Nie |
> | MediaServices/privateEndpointConnectionOperations | Nie |
> | MediaServices/privateEndpointConnectionProxies | Nie |
> | MediaServices/privateEndpointConnections | Nie |
> | MediaServices/streamingEndpointOperations | Nie |
> | MediaServices/streamingEndpoints | Tak |
> | MediaServices/streamingLocators | Nie |
> | MediaServices/streamingPolicies | Nie |
> | MediaServices/transformacje | Nie |
> | MediaServices/transformacje/zadania | Nie |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | appClusters | Tak |

## <a name="microsoftmigrate"></a>Microsoft. Migrowanie

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | assessmentProjects | Tak |
> | migrateprojects | Tak |
> | moveCollections | Tak |
> | projektami | Tak |

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | Tak |
> | objectUnderstandingAccounts | Tak |
> | remoteRenderingAccounts | Tak |
> | spatialAnchorsAccounts | Tak |

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | netAppAccounts | Tak |
> | netAppAccounts / accountBackups | Nie |
> | netAppAccounts / capacityPools | Tak |
> | netAppAccounts/capacityPools/woluminy | Tak |
> | netAppAccounts/capacityPools/woluminy/migawki | Nie |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | applicationGateways | Tak |
> | applicationGatewayWebApplicationFirewallPolicies | Tak |
> | applicationSecurityGroups | Tak |
> | azureFirewallFqdnTags | Nie |
> | azureFirewalls | Tak |
> | bastionHosts | Tak |
> | bgpServiceCommunities | Nie |
> | Licznik | Tak |
> | ddosCustomPolicies | Tak |
> | ddosProtectionPlans | Tak |
> | dnsOperationStatuses | Nie |
> | dnszones | Tak |
> | dnszones/A | Nie |
> | dnszones/AAAA | Nie |
> | dnszones/wszystkie | Nie |
> | dnszones/CAA | Nie |
> | dnszones/CNAME | Nie |
> | dnszones/MX | Nie |
> | dnszones/NS | Nie |
> | dnszones/PTR | Nie |
> | dnszones/zestawy rekordów | Nie |
> | dnszones/SOA | Nie |
> | dnszones/SRV | Nie |
> | dnszones/TXT | Nie |
> | expressRouteCircuits | Tak |
> | expressRouteCrossConnections | Tak |
> | expressRouteGateways | Tak |
> | expressRoutePorts | Tak |
> | expressRouteServiceProviders | Nie |
> | firewallPolicies | Tak |
> | usługi frontdoor | Tak |
> | frontdoorWebApplicationFirewallManagedRuleSets | Nie |
> | frontdoorWebApplicationFirewallPolicies | Tak |
> | getDnsResourceReference | Nie |
> | internalNotify | Nie |
> | ipGroups | Tak |
> | loadBalancers | Tak |
> | localNetworkGateways | Tak |
> | natGateways | Tak |
> | networkIntentPolicies | Tak |
> | networkInterfaces | Tak |
> | networkProfiles | Tak |
> | networkSecurityGroups | Tak |
> | networkWatchers | Tak |
> | networkWatchers / connectionMonitors | Tak |
> | networkWatchers / flowLogs | Tak |
> | networkWatchers/soczewki | Tak |
> | networkWatchers / pingMeshes | Tak |
> | p2sVpnGateways | Tak |
> | privateDnsOperationStatuses | Nie |
> | privateDnsZones | Tak |
> | privateDnsZones/A | Nie |
> | privateDnsZones/AAAA | Nie |
> | privateDnsZones/wszystkie | Nie |
> | privateDnsZones/CNAME | Nie |
> | privateDnsZones/MX | Nie |
> | privateDnsZones/PTR | Nie |
> | privateDnsZones/SOA | Nie |
> | privateDnsZones/SRV | Nie |
> | privateDnsZones/TXT | Nie |
> | privateDnsZones / virtualNetworkLinks | Tak |
> | privateEndpoints | Tak |
> | privateLinkServices | Tak |
> | Adresów publicipaddress | Tak |
> | publicIPPrefixes | Tak |
> | routeFilters | Tak |
> | routeTables | Tak |
> | serviceEndpointPolicies | Tak |
> | trafficManagerGeographicHierarchies | Nie |
> | trafficmanagerprofiles | Tak |
> | trafficmanagerprofiles/map cieplnych | Nie |
> | trafficManagerUserMetricsKeys | Nie |
> | virtualHubs | Tak |
> | virtualNetworkGateways | Tak |
> | virtualNetworks | Tak |
> | virtualNetworks/podsieci | Nie |
> | virtualNetworkTaps | Tak |
> | virtualWans | Tak |
> | vpnGateways | Tak |
> | vpnSites | Tak |
> | webApplicationFirewallPolicies | Tak |

## <a name="microsoftnotebooks"></a>Microsoft. notesy

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | NotebookProxies | Nie |

## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | przestrzeni | Tak |
> | przestrzenie nazw/notificationHubs | Tak |

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | osNamespaces | Tak |

## <a name="microsoftoffazure"></a>Microsoft. OffAzure

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | HyperVSites | Tak |
> | ImportSites | Tak |
> | MasterSites | Tak |
> | ServerSites | Tak |
> | VMwareSites | Tak |

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | oparty | Tak |
> | deletedWorkspaces | Nie |
> | linkTargets | Nie |
> | storageInsightConfigs | Nie |
> | obszary robocze | Tak |
> | obszary robocze/eksporty | Nie |
> | obszary robocze/źródła danych | Nie |
> | obszary robocze/linkedServices | Nie |
> | obszary robocze/linkedStorageAccounts | Nie |
> | obszary robocze/metadane | Nie |
> | obszary robocze/zapytanie | Nie |
> | obszary robocze/scopedPrivateLinkProxies | Nie |

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | managementassociations | Nie |
> | managementconfigurations | Tak |
> | rozwiązania | Tak |
> | widoki | Tak |

## <a name="microsoftpeering"></a>Microsoft. Komunikacja równorzędna

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | legacyPeerings | Nie |
> | peerAsns | Nie |
> | komunikacje równorzędne | Tak |
> | peeringServiceCountries | Nie |
> | peeringServiceProviders | Nie |
> | peeringServices | Tak |

## <a name="microsoftpolicyinsights"></a>Microsoft. PolicyInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | atestacji | Nie |
> | policyEvents | Nie |
> | policyMetadata | Nie |
> | policyStates | Nie |
> | policyTrackedResources | Nie |
> | korygowania | Nie |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Konsole programu | Nie |
> | pulpity nawigacyjne | Tak |
> | userSettings | Nie |

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | privateLinkServicesForPowerBI | Tak |
> | dzierżaw | Tak |
> | dzierżawy/obszary robocze | Nie |
> | workspaceCollections | Tak |

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | produkcyjnych | Tak |

## <a name="microsoftprojectbabylon"></a>Microsoft. ProjectBabylon

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Konta | Tak |
> | deletedAccounts | Nie |

## <a name="microsoftproviderhub"></a>Microsoft. ProviderHub

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | providerRegistrations | Nie |
> | providerRegistrations / defaultRollouts | Nie |
> | providerRegistrations / resourceTypeRegistrations | Nie |
> | wprowadzanie | Tak |

## <a name="microsoftquantum"></a>Microsoft. Quantum

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Obszary robocze | Tak |

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | backupProtectedItems | Nie |
> | magazynów | Tak |

## <a name="microsoftredhatopenshift"></a>Microsoft. RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | OpenShiftClusters | Tak |

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | przestrzeni | Tak |
> | przestrzenie nazw/reguł autoryzacji | Nie |
> | przestrzenie nazw/hybridconnections | Nie |
> | przestrzenie nazw/hybridconnections/reguł autoryzacji | Nie |
> | przestrzenie nazw/privateEndpointConnections | Nie |
> | przestrzenie nazw/wcfrelays | Nie |
> | przestrzenie nazw/wcfrelays/reguł autoryzacji | Nie |

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | wybiera | Tak |
> | resourceChangeDetails | Nie |
> | resourceChanges | Nie |
> | zasoby | Nie |
> | resourcesHistory | Nie |
> | subscriptionsStatus | Nie |

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

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
> | powiadomienia | Nie |

## <a name="microsoftresources"></a>Microsoft. resources

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | calculateTemplateHash | Nie |
> | komputerów | Nie |
> | wdrożenia/operacje | Nie |
> | deploymentScripts | Tak |
> | deploymentScripts/dzienniki | Nie |
> | linki | Nie |
> | notifyResourceJobs | Nie |
> | dostawców | Nie |
> | resourceGroups | Nie |
> | opłaty | Nie |
> | templateSpecs | Tak |
> | templateSpecs/wersje | Tak |
> | dzierżaw | Nie |

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | aplikacje | Tak |
> | saasresources | Nie |

## <a name="microsoftscvmm"></a>Microsoft. ScVmm

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | połączeń | Tak |
> | VirtualMachines | Tak |
> | VirtualMachineTemplates | Tak |
> | VirtualNetworks | Tak |
> | vmmservers | Tak |

## <a name="microsoftsearch"></a>Microsoft. Search

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | resourceHealthMetadata | Nie |
> | searchServices | Tak |

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
> | automatyzacji | Tak |
> | AutoProvisioningSettings | Nie |
> | Zachowania zgodności prawnymi | Nie |
> | konta integracji | Nie |
> | dataCollectionAgents | Nie |
> | deviceSecurityGroups | Nie |
> | discoveredSecuritySolutions | Nie |
> | externalSecuritySolutions | Nie |
> | InformationProtectionPolicies | Nie |
> | iotDefenderSettings | Nie |
> | iotSecuritySolutions | Tak |
> | iotSecuritySolutions / analyticsModels | Nie |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | Nie |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | Nie |
> | iotSecuritySolutions / iotAlerts | Nie |
> | iotSecuritySolutions / iotAlertTypes | Nie |
> | iotSecuritySolutions / iotRecommendations | Nie |
> | iotSecuritySolutions / iotRecommendationTypes | Nie |
> | iotSensors | Nie |
> | jitNetworkAccessPolicies | Nie |
> | jitPolicies | Nie |
> | policies | Nie |
> | cen | Nie |
> | regulatoryComplianceStandards | Nie |
> | regulatoryComplianceStandards / regulatoryComplianceControls | Nie |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | Nie |
> | secureScoreControlDefinitions | Nie |
> | secureScoreControls | Nie |
> | secureScores | Nie |
> | secureScores / secureScoreControls | Nie |
> | securityContacts | Nie |
> | securitySolutions | Nie |
> | securitySolutionsReferenceData | Nie |
> | securityStatuses | Nie |
> | securityStatusesSummaries | Nie |
> | serverVulnerabilityAssessments | Nie |
> | ustawienia | Nie |
> | sqlVulnerabilityAssessments | Nie |
> | podoceny | Nie |
> | zadania | Nie |
> | replikacji | Nie |
> | workspaceSettings | Nie |

## <a name="microsoftsecuritygraph"></a>Microsoft. SecurityGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | diagnosticSettings | Nie |
> | diagnosticSettingsCategories | Nie |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | agregacji | Nie |
> | alertRules | Nie |
> | alertRuleTemplates | Nie |
> | automationRules | Nie |
> | zakładki | Nie |
> | padkach | Nie |
> | Połączenia dataconnecters | Nie |
> | dataConnectorsCheckRequirements | Nie |
> | obiekty | Nie |
> | entityQueries | Nie |
> | zdarzenia | Nie |
> | officeConsents | Nie |
> | ustawienia | Nie |
> | threatIntelligence | Nie |
> | watchlists | Nie |

## <a name="microsoftserialconsole"></a>Microsoft. SerialConsole

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | consoleServices | Nie |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | przestrzeni | Tak |
> | przestrzenie nazw/reguł autoryzacji | Nie |
> | przestrzenie nazw/disasterrecoveryconfigs | Nie |
> | przestrzenie nazw/eventgridfilters | Nie |
> | przestrzenie nazw/networkrulesets | Nie |
> | przestrzenie nazw/privateEndpointConnections | Nie |
> | przestrzenie nazw/kolejki | Nie |
> | przestrzenie nazw/kolejki/reguł autoryzacji | Nie |
> | obszary nazw/tematy | Nie |
> | przestrzenie nazw/tematy/reguł autoryzacji | Nie |
> | obszary nazw/tematy/subskrypcje | Nie |
> | obszary nazw/tematy/subskrypcje/reguły | Nie |
> | premiumMessagingRegions | Nie |

## <a name="microsoftservicefabric"></a>Microsoft. servicefabric

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | aplikacje | Tak |
> | oparty | Tak |
> | klastry/aplikacje | Nie |
> | containerGroups | Tak |
> | containerGroupSets | Tak |
> | edgeclusters | Tak |
> | edgeclusters/aplikacje | Nie |
> | managedclusters | Tak |
> | managedclusters/elementów NodeType | Nie |
> | sieci | Tak |
> | secretstores | Tak |
> | secretstores/certyfikaty | Nie |
> | secretstores/wpisy tajne | Nie |
> | volumes | Tak |

## <a name="microsoftservicefabricmesh"></a>Microsoft. ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | aplikacje | Tak |
> | containerGroups | Tak |
> | bram | Tak |
> | sieci | Tak |
> | wpisy tajne | Tak |
> | volumes | Tak |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | providerRegistrations | Nie |
> | providerRegistrations / resourceTypeRegistrations | Nie |
> | wprowadzanie | Tak |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | SignalR | Tak |
> | Sygnalizujący/eventGridFilters | Nie |

## <a name="microsoftsingularity"></a>Microsoft. Singularity

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Konta | Tak |
> | konta/accountQuotaPolicies | Nie |
> | konta/groupPolicies | Nie |
> | konta/zadania | Nie |
> | konta/storageContainers | Nie |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | hybridUseBenefits | Nie |

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | applicationDefinitions | Tak |
> | aplikacje | Tak |
> | jitRequests | Tak |

## <a name="microsoftsql"></a>Microsoft. SQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | managedInstances | Tak |
> | managedInstances/bazy danych | Tak |
> | managedInstances/Databases/backupShortTermRetentionPolicies | Nie |
> | managedInstances/bazy danych/schematy/tabele/kolumny/sensitivityLabels | Nie |
> | managedInstances/Databases/vulnerabilityAssessments | Nie |
> | managedInstances/bazy danych/vulnerabilityAssessments/reguły/linie bazowe | Nie |
> | managedInstances / encryptionProtector | Nie |
> | managedInstances/klucze | Nie |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | Nie |
> | managedInstances / vulnerabilityAssessments | Nie |
> | serwerem | Tak |
> | serwery/Administratorzy | Nie |
> | serwery/communicationLinks | Nie |
> | Serwery/bazy danych | Tak |
> | serwery/encryptionProtector | Nie |
> | serwery/firewallRules | Nie |
> | serwery/klucze | Nie |
> | serwery/restorableDroppedDatabases | Nie |
> | serwery/cele | Nie |
> | serwery/tdeCertificates | Nie |
> | virtualClusters | Nie |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | Tak |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Nie |
> | SqlVirtualMachines | Tak |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | deletedAccounts | Nie |
> | storageAccounts | Tak |
> | storageAccounts/blobServices | Nie |
> | storageAccounts/fileServices | Nie |
> | storageAccounts/queueServices | Nie |
> | storageAccounts/usługi | Nie |
> | storageAccounts/usługi/metricDefinitions | Nie |
> | storageAccounts/tableServices | Nie |
> | użycia | Nie |

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | pamięci podręcznych | Tak |
> | pamięci podręczne/storageTargets | Nie |
> | usageModels | Nie |

## <a name="microsoftstoragereplication"></a>Microsoft. StorageReplication

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | replicationGroups | Nie |

## <a name="microsoftstoragesync"></a>Microsoft. StorageSync

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | storageSyncServices | Tak |
> | storageSyncServices/registeredServers | Nie |
> | storageSyncServices / syncGroups | Nie |
> | storageSyncServices / syncGroups / cloudEndpoints | Nie |
> | storageSyncServices / syncGroups / serverEndpoints | Nie |
> | storageSyncServices/przepływy pracy | Nie |

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | storageSyncServices | Tak |
> | storageSyncServices/registeredServers | Nie |
> | storageSyncServices / syncGroups | Nie |
> | storageSyncServices / syncGroups / cloudEndpoints | Nie |
> | storageSyncServices / syncGroups / serverEndpoints | Nie |
> | storageSyncServices/przepływy pracy | Nie |

## <a name="microsoftstoragesyncint"></a>Microsoft. StorageSyncInt

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | storageSyncServices | Tak |
> | storageSyncServices/registeredServers | Nie |
> | storageSyncServices / syncGroups | Nie |
> | storageSyncServices / syncGroups / cloudEndpoints | Nie |
> | storageSyncServices / syncGroups / serverEndpoints | Nie |
> | storageSyncServices/przepływy pracy | Nie |

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | skonfigurowany | Tak |

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | oparty | Tak |
> | klastry/privateEndpoints | Nie |
> | streamingjobs | Tak |

## <a name="microsoftsubscription"></a>Microsoft. Subscription

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | acceptChangeTenant | Nie |
> | aliasy | Nie |
> | Anuluj | Nie |
> | changeTenantRequest | Nie |
> | changeTenantStatus | Nie |
> | Anulowanie subskrypcji | Nie |
> | Włącz | Nie |
> | zmień nazwę | Nie |
> | SubscriptionDefinitions | Nie |
> | SubscriptionOperations | Nie |
> | opłaty | Nie |

## <a name="microsoftsynapse"></a>Microsoft. Synapse

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | privateLinkHubs | Tak |
> | obszary robocze | Tak |
> | obszary robocze/bigDataPools | Tak |
> | obszary robocze/operationStatuses | Nie |
> | obszary robocze/sqldatabases | Tak |
> | obszary robocze/pule | Tak |

## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | wiejski | Tak |
> | środowiska/accessPolicies | Nie |
> | środowiska/źródła zdarzeń | Tak |
> | środowiska/referenceDataSets | Tak |

## <a name="microsofttoken"></a>Microsoft. token

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | przechowywać | Tak |
> | Sklepy/accessPolicies | Nie |
> | Sklepy/usługi | Nie |
> | Sklepy/usługi/tokeny | Nie |

## <a name="microsoftvirtualmachineimages"></a>Microsoft. VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | imageTemplates | Tak |
> | imageTemplates / runOutputs | Nie |

## <a name="microsoftvmware"></a>Microsoft. VMware

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | ArcZones | Tak |
> | ResourcePools | Tak |
> | Serwery vCenter | Tak |
> | VirtualMachines | Tak |
> | VirtualMachineTemplates | Tak |
> | VirtualNetworks | Tak |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | dedicatedCloudNodes | Tak |
> | dedicatedCloudServices | Tak |
> | virtualMachines | Tak |

## <a name="microsoftvnfmanager"></a>Microsoft. VnfManager

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | devices | Tak |
> | registeredSubscriptions | Nie |
> | dostawców | Nie |
> | dostawcy/jednostki SKU | Nie |
> | dostawcy/vnfs | Nie |
> | virtualNetworkFunctionSkus | Nie |
> | vnfs | Tak |

## <a name="microsoftvsonline"></a>Microsoft. VSOnline

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | Konta | Tak |
> | pakiety | Tak |
> | registeredSubscriptions | Nie |

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | apiManagementAccounts | Nie |
> | apiManagementAccounts / apiAcls | Nie |
> | apiManagementAccounts/interfejsy API | Nie |
> | apiManagementAccounts/interfejsy API/apiAcls | Nie |
> | apiManagementAccounts/interfejsy API/connectionAcls | Nie |
> | apiManagementAccounts/interfejsy API/połączenia | Nie |
> | apiManagementAccounts/interfejsy API/połączenia/connectionAcls | Nie |
> | apiManagementAccounts/interfejsy API/localizedDefinitions | Nie |
> | apiManagementAccounts / connectionAcls | Nie |
> | apiManagementAccounts/połączenia | Nie |
> | billingMeters | Nie |
> | certyfikaty | Tak |
> | connectionGateways | Tak |
> | Licznik | Tak |
> | customApis | Tak |
> | deletedSites | Nie |
> | hostingEnvironments | Tak |
> | hostingEnvironments / eventGridFilters | Nie |
> | hostingEnvironments / multiRolePools | Nie |
> | hostingEnvironments / workerPools | Nie |
> | kubeEnvironments | Tak |
> | publishingUsers | Nie |
> | zalecenia | Nie |
> | resourceHealthMetadata | Nie |
> | Runtime | Nie |
> | Dopuszczalna | Tak |
> | Dopuszczalna/eventGridFilters | Nie |
> | Dopuszczalna/firstPartyApps | Nie |
> | Dopuszczalna/firstPartyApps/keyVaultSettings | Nie |
> | lokacje | Tak |
> | Lokacje/konfiguracja  | Nie |
> | Lokacje/eventGridFilters | Nie |
> | Lokacje/hostNameBindings | Nie |
> | Lokacje/networkConfig | Nie |
> | Lokacje/premieraddons | Tak |
> | Lokacje/miejsca | Tak |
> | Lokacje/miejsca/eventGridFilters | Nie |
> | Lokacje/miejsca/hostNameBindings | Nie |
> | Lokacje/miejsca/networkConfig | Nie |
> | sourceControls | Nie |
> | staticSites | Tak |
> | legalizacj | Nie |
> | verifyHostingEnvironmentVnet | Nie |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft. WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | diagnosticSettings | Nie |
> | diagnosticSettingsCategories | Nie |

## <a name="microsoftwindowsesu"></a>Microsoft. WindowsESU

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | multipleActivationKeys | Tak |

## <a name="microsoftwindowsiot"></a>Microsoft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | DeviceServices | Tak |

## <a name="microsoftworkloadbuilder"></a>Microsoft. WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | obciążeń | Tak |
> | obciążenia/wystąpienia | Nie |
> | obciążenia/wersje | Nie |
> | obciążenia/wersje/artefakty | Nie |

## <a name="microsoftworkloadmonitor"></a>Microsoft. Monitor obciążenia został

> [!div class="mx-tableFixed"]
> | Typ zasobu | Usuwanie w trybie pełnym |
> | ------------- | ----------- |
> | komponentów | Nie |
> | componentsSummary | Nie |
> | monitorInstances | Nie |
> | monitorInstancesSummary | Nie |
> | monitora | Nie |
> | notificationSettings | Nie |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać te same dane w postaci pliku wartości rozdzielanych przecinkami, Pobierz [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).