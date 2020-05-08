---
title: Obsługa tagów dla zasobów
description: Pokazuje, które typy zasobów platformy Azure obsługują Tagi. Zawiera szczegółowe informacje dotyczące wszystkich usług platformy Azure.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 6c035f4f9ce6b7c842caeab3b705394417d15ba0
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82609293"
---
# <a name="tag-support-for-azure-resources"></a>Obsługa tagów dla zasobów platformy Azure
W tym artykule opisano, czy typ zasobu obsługuje [Tagi](tag-resources.md). Kolumna z etykietą **obsługuje znaczniki** wskazuje, czy typ zasobu ma właściwość dla tagu. Kolumna **tag z etykietą w raporcie koszt** wskazuje, czy ten typ zasobu przekazuje tag do raportu kosztów. Możesz wyświetlić koszty według tagów w [Cost Management analiza kosztów](../../cost-management-billing/costs/quick-acm-cost-analysis.md#understanding-grouping-and-filtering-options) oraz [dane dotyczące rozliczeń i dziennego użycia na platformie Azure](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md).

Aby uzyskać te same dane jak plik z wartościami rozdzielanymi przecinkami, Pobierz [tag-support. csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

Przejdź do przestrzeni nazw dostawcy zasobów:
> [!div class="op_single_selector"]
> - [Microsoft. AAD](#microsoftaad)
> - [Microsoft. Dodatki](#microsoftaddons)
> - [Microsoft. ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft. Advisor](#microsoftadvisor)
> - [Microsoft. AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft. AnalysisServices](#microsoftanalysisservices)
> - [Microsoft. ApiManagement](#microsoftapimanagement)
> - [Microsoft. AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. AppPlatform](#microsoftappplatform)
> - [Microsoft. zaświadczanie](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft. Automation](#microsoftautomation)
> - [Microsoft. Azconfig](#microsoftazconfig)
> - [Microsoft. Azure. Genewa](#microsoftazuregeneva)
> - [Microsoft. usługi azureactivedirectory](#microsoftazureactivedirectory)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft. AzureStack](#microsoftazurestack)
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
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft. ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft. ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft. ClassicStorage](#microsoftclassicstorage)
> - [Microsoft. CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft. Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft. zużycie](#microsoftconsumption)
> - [Microsoft. ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft. ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft. ContainerService](#microsoftcontainerservice)
> - [Microsoft. CostManagement](#microsoftcostmanagement)
> - [Microsoft. CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft. CustomProviders](#microsoftcustomproviders)
> - [Microsoft. DataBox](#microsoftdatabox)
> - [Microsoft. DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft. datacegły](#microsoftdatabricks)
> - [Microsoft. datacatalog](#microsoftdatacatalog)
> - [Microsoft. DataFactory](#microsoftdatafactory)
> - [Microsoft. DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft. kontach datalakestore](#microsoftdatalakestore)
> - [Migracja Microsoft.](#microsoftdatamigration)
> - [Microsoft. dataudział](#microsoftdatashare)
> - [Microsoft. DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft. DBforMySQL](#microsoftdbformysql)
> - [Microsoft. DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft. Deploymentmanager](#microsoftdeploymentmanager)
> - [Microsoft. DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft. urządzenia](#microsoftdevices)
> - [Microsoft. DevOps](#microsoftdevops)
> - [Microsoft. DevSpaces](#microsoftdevspaces)
> - [Microsoft. wspólny](#microsoftdevtestlab)
> - [Microsoft. DocumentDB](#microsoftdocumentdb)
> - [Microsoft. DomainRegistration](#microsoftdomainregistration)
> - [Microsoft. DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft. EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft. EventGrid](#microsofteventgrid)
> - [Microsoft. EventHub](#microsofteventhub)
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
> - [Microsoft. Hydra](#microsofthydra)
> - [Microsoft. ImportExport](#microsoftimportexport)
> - [Microsoft. Insights](#microsoftinsights)
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft. IoTCentral](#microsoftiotcentral)
> - [Microsoft. IoTSpaces](#microsoftiotspaces)
> - [Microsoft. kluczy — magazyn](#microsoftkeyvault)
> - [Microsoft. Kubernetes](#microsoftkubernetes)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft. LabServices](#microsoftlabservices)
> - [Microsoft. Logic](#microsoftlogic)
> - [Microsoft. MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft. Maintenance](#microsoftmaintenance)
> - [Microsoft. ManagedIdentity](#microsoftmanagedidentity)
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
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft. Portal](#microsoftportal)
> - [Microsoft. PowerBI](#microsoftpowerbi)
> - [Microsoft. PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft. ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft. Quantum](#microsoftquantum)
> - [Microsoft. RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft. Relay](#microsoftrelay)
> - [Microsoft. RemoteApp](#microsoftremoteapp)
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
> - [Microsoft. ResourceHealth](#microsoftresourcehealth)
> - [Microsoft. resources](#microsoftresources)
> - [Microsoft. SaaS](#microsoftsaas)
> - [Microsoft. Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft. SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft. SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft. servicefabric](#microsoftservicefabric)
> - [Microsoft. ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft. Services](#microsoftservices)
> - [Microsoft. SignalRService](#microsoftsignalrservice)
> - [Microsoft. SiteRecovery](#microsoftsiterecovery)
> - [Microsoft. SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft. Solutions](#microsoftsolutions)
> - [Microsoft. SpoolService](#microsoftspoolservice)
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
> - [Microsoft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft. VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft. VnfManager](#microsoftvnfmanager)
> - [Microsoft. Web](#microsoftweb)
> - [Microsoft. WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft. WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. Monitor obciążenia został](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft. AAD

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | DomainServices | Tak | Tak |
> | DomainServices / oucontainer | Nie | Nie |

## <a name="microsoftaddons"></a>Microsoft. Dodatki

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | supportProviders | Nie | Nie |

## <a name="microsoftadhybridhealthservice"></a>Microsoft. ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | Nie | Nie |
> | addsservices | Nie | Nie |
> | pracownicy | Nie | Nie |
> | anonymousapiusers | Nie | Nie |
> | konfiguracja | Nie | Nie |
> | dzienniki | Nie | Nie |
> | reports | Nie | Nie |
> | servicehealthmetrics | Nie | Nie |
> | services | Nie | Nie |

## <a name="microsoftadvisor"></a>Microsoft. Advisor

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | komputerów | Nie | Nie |
> | generateRecommendations | Nie | Nie |
> | metadane | Nie | Nie |
> | zalecenia | Nie | Nie |
> | pominięć | Nie | Nie |

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | actionRules | Tak | Tak |
> | alerts | Nie | Nie |
> | alertsList | Nie | Nie |
> | alertsMetaData | Nie | Nie |
> | alertsSummary | Nie | Nie |
> | alertsSummaryList | Nie | Nie |
> | smartDetectorAlertRules | Tak | Tak |
> | smartGroups | Nie | Nie |

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | serwerem | Tak | Tak |

## <a name="microsoftapimanagement"></a>Microsoft. ApiManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | reportFeedback | Nie | Nie |
> | usługa | Tak | Tak |
> | validateServiceName | Nie | Nie |

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | configurationStores | Tak | Tak |
> | configurationStores / eventGridFilters | Nie | Nie |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Spring | Tak | Tak |

## <a name="microsoftattestation"></a>Microsoft. zaświadczanie

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | attestationProviders | Nie | Nie |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | classicAdministrators | Nie | Nie |
> | aliasy | Nie | Nie |
> | denyAssignments | Nie | Nie |
> | elevateAccess | Nie | Nie |
> | findOrphanRoleAssignments | Nie | Nie |
> | Zamki | Nie | Nie |
> | uprawnienia | Nie | Nie |
> | policyAssignments | Nie | Nie |
> | policyDefinitions | Nie | Nie |
> | policySetDefinitions | Nie | Nie |
> | providerOperations | Nie | Nie |
> | roleAssignments | Nie | Nie |
> | roleAssignmentsUsageMetrics | Nie | Nie |
> | roleDefinitions | Nie | Nie |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | automationAccounts | Tak | Tak |
> | automationAccounts/konfiguracje | Tak | Tak |
> | automationAccounts/zadania | Nie | Nie |
> | automationAccounts / privateEndpointConnectionProxies | Nie | Nie |
> | automationAccounts / privateEndpointConnections | Nie | Nie |
> | automationAccounts / privateLinkResources | Nie | Nie |
> | automationAccounts/elementy Runbook | Tak | Tak |
> | automationAccounts / softwareUpdateConfigurations | Nie | Nie |
> | automationAccounts/elementy webhook | Nie | Nie |

## <a name="microsoftazconfig"></a>Microsoft. Azconfig

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | configurationStores | Tak | Tak |
> | configurationStores / eventGridFilters | Nie | Nie |

## <a name="microsoftazuregeneva"></a>Microsoft. Azure. Genewa

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | wiejski | Nie | Nie |
> | środowiska/konta | Nie | Nie |
> | środowiska/konta/przestrzenie nazw | Nie | Nie |
> | środowiska/konta/przestrzenie nazw/konfiguracje | Nie | Nie |

## <a name="microsoftazureactivedirectory"></a>Microsoft. usługi azureactivedirectory

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | Yes | Nie |
> | b2ctenants | Nie | Nie |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | hybridDataManagers | Tak | Tak |
> | postgresInstances | Tak | Tak |
> | Wystąpienia sqlinstances | Tak | Tak |
> | sqlServerRegistrations | Tak | Tak |
> | sqlServerRegistrations/SQLServers | Nie | Nie |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | cloudManifestFiles | Nie | Nie |
> | rejestracje | Tak | Tak |
> | rejestracje/customerSubscriptions | Nie | Nie |
> | rejestracje/produkty | Nie | Nie |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | batchAccounts | Tak | Tak |

## <a name="microsoftbilling"></a>Microsoft. rozliczenia

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | billingAccounts | Nie | Nie |
> | billingAccounts/umowy | Nie | Nie |
> | billingAccounts / billingPermissions | Nie | Nie |
> | billingAccounts / billingProfiles | Nie | Nie |
> | billingAccounts / billingProfiles / billingPermissions | Nie | Nie |
> | billingAccounts / billingProfiles / billingRoleAssignments | Nie | Nie |
> | billingAccounts / billingProfiles / billingRoleDefinitions | Nie | Nie |
> | billingAccounts / billingProfiles / billingSubscriptions | Nie | Nie |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | Nie | Nie |
> | billingAccounts/billingProfiles/klienci | Nie | Nie |
> | billingAccounts/billingProfiles/instrukcje | Nie | Nie |
> | billingAccounts/billingProfiles/faktury | Nie | Nie |
> | billingAccounts/billingProfiles/faktury/arkusza cen | Nie | Nie |
> | billingAccounts/billingProfiles/faktury/transakcje | Nie | Nie |
> | billingAccounts / billingProfiles / invoiceSections | Nie | Nie |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | Nie | Nie |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | Nie | Nie |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | Nie | Nie |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | Nie | Nie |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | Nie | Nie |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | Nie | Nie |
> | billingAccounts/billingProfiles/invoiceSections/produkty | Nie | Nie |
> | billingAccounts/billingProfiles/invoiceSections/produkty/transfer | Nie | Nie |
> | billingAccounts/billingProfiles/invoiceSections/produkty/updateAutoRenew | Nie | Nie |
> | billingAccounts/billingProfiles/invoiceSections/Transactions | Nie | Nie |
> | billingAccounts/billingProfiles/invoiceSections/transfery | Nie | Nie |
> | billingAccounts / BillingProfiles / patchOperations | Nie | Nie |
> | billingAccounts / billingProfiles / paymentMethods | Nie | Nie |
> | billingAccounts/billingProfiles/zasady | Nie | Nie |
> | billingAccounts/billingProfiles/arkusza cen | Nie | Nie |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | Nie | Nie |
> | billingAccounts/billingProfiles/produkty | Nie | Nie |
> | billingAccounts/billingProfiles/Transactions | Nie | Nie |
> | billingAccounts / billingRoleAssignments | Nie | Nie |
> | billingAccounts / billingRoleDefinitions | Nie | Nie |
> | billingAccounts / billingSubscriptions | Nie | Nie |
> | billingAccounts/billingSubscriptions/faktury | Nie | Nie |
> | billingAccounts / createBillingRoleAssignment | Nie | Nie |
> | billingAccounts / createInvoiceSectionOperations | Nie | Nie |
> | billingAccounts/klienci | Nie | Nie |
> | billingAccounts/klienci/billingPermissions | Nie | Nie |
> | billingAccounts/klienci/billingSubscriptions | Nie | Nie |
> | billingAccounts/klienci/initiateTransfer | Nie | Nie |
> | billingAccounts/klienci/zasady | Nie | Nie |
> | billingAccounts/klienci/produkty | Nie | Nie |
> | billingAccounts/klienci/transakcje | Nie | Nie |
> | billingAccounts/klienci/transfery | Nie | Nie |
> | billingAccounts/działy | Nie | Nie |
> | billingAccounts / enrollmentAccounts | Nie | Nie |
> | billingAccounts/faktury | Nie | Nie |
> | billingAccounts / invoiceSections | Nie | Nie |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | Nie | Nie |
> | billingAccounts / invoiceSections / billingSubscriptions | Nie | Nie |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | Nie | Nie |
> | billingAccounts/invoiceSections/Podnieś poziom | Nie | Nie |
> | billingAccounts / invoiceSections / initiateTransfer | Nie | Nie |
> | billingAccounts / invoiceSections / patchOperations | Nie | Nie |
> | billingAccounts / invoiceSections / productMoveOperations | Nie | Nie |
> | billingAccounts/invoiceSections/produkty | Nie | Nie |
> | billingAccounts/invoiceSections/produkty/transfer | Nie | Nie |
> | billingAccounts/invoiceSections/produkty/updateAutoRenew | Nie | Nie |
> | billingAccounts/invoiceSections/Transactions | Nie | Nie |
> | billingAccounts/invoiceSections/transfery | Nie | Nie |
> | billingAccounts / lineOfCredit | Nie | Nie |
> | billingAccounts / patchOperations | Nie | Nie |
> | billingAccounts / paymentMethods | Nie | Nie |
> | billingAccounts/produkty | Nie | Nie |
> | billingAccounts/transakcje | Nie | Nie |
> | billingPeriods | Nie | Nie |
> | billingPermissions | Nie | Nie |
> | billingProperty | Nie | Nie |
> | billingRoleAssignments | Nie | Nie |
> | billingRoleDefinitions | Nie | Nie |
> | createBillingRoleAssignment | Nie | Nie |
> | działów | Nie | Nie |
> | enrollmentAccounts | Nie | Nie |
> | faktur | Nie | Nie |
> | sunięcia | Nie | Nie |
> | transfery/acceptTransfer | Nie | Nie |
> | transfery/declineTransfer | Nie | Nie |
> | transfery/operationStatus | Nie | Nie |
> | transfery/validateTransfer | Nie | Nie |
> | validateAddress | Nie | Nie |

## <a name="microsoftbingmaps"></a>Microsoft. BingMaps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | mapApis | Tak | Tak |
> | updateCommunicationPreference | Nie | Nie |

## <a name="microsoftblockchain"></a>Microsoft. łańcucha bloków

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | Tak | Tak |
> | cordaMembers | Tak | Tak |
> | obserwatorów | Tak | Tak |

## <a name="microsoftblockchaintokens"></a>Microsoft. BlockchainTokens

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | TokenServices | Tak | Tak |
> | TokenServices / BlockchainNetworks | Nie | Nie |
> | TokenServices/grupy | Nie | Nie |
> | TokenServices/grupy/konta | Nie | Nie |
> | TokenServices / TokenTemplates | Nie | Nie |

## <a name="microsoftblueprint"></a>Microsoft. plan

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | Nie | Nie |
> | blueprintAssignments / assignmentOperations | Nie | Nie |
> | blueprintAssignments/operacje | Nie | Nie |
> | plany | Nie | Nie |
> | plany/artefakty | Nie | Nie |
> | plany/wersje | Nie | Nie |
> | plany/wersje/artefakty | Nie | Nie |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | botServices | Tak | Tak |
> | botServices/kanały | Nie | Nie |
> | botServices/połączenia | Nie | Nie |
> | języki | Nie | Nie |
> | szablonów | Nie | Nie |

## <a name="microsoftcache"></a>Microsoft. cache

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Redis | Tak | Tak |

## <a name="microsoftcapacity"></a>Microsoft. Pojemność

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | appliedReservations | Nie | Nie |
> | autoQuotaIncrease | Nie | Nie |
> | calculateExchange | Nie | Nie |
> | calculatePrice | Nie | Nie |
> | calculatePurchasePrice | Nie | Nie |
> | wykazy | Nie | Nie |
> | commercialReservationOrders | Nie | Nie |
> | zamian | Nie | Nie |
> | placePurchaseOrder | Nie | Nie |
> | reservationOrders | Nie | Nie |
> | reservationOrders / calculateRefund | Nie | Nie |
> | reservationOrders/Scal | Nie | Nie |
> | reservationOrders/rezerwacje | Nie | Nie |
> | reservationOrders/rezerwacje/poprawki | Nie | Nie |
> | reservationOrders/Return | Nie | Nie |
> | reservationOrders/Split | Nie | Nie |
> | reservationOrders/swap | Nie | Nie |
> | dokonując | Nie | Nie |
> | resourceProviders | Nie | Nie |
> | zasoby | Nie | Nie |
> | validateReservationOrder | Nie | Nie |

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Nie | Nie |
> | CdnWebApplicationFirewallPolicies | Tak | Tak |
> | edgenodes | Nie | Nie |
> | Profil | Tak | Tak |
> | Profile/punkty końcowe | Tak | Tak |
> | Profile/punkty końcowe/customdomains | Nie | Nie |
> | Profile/punkty końcowe/origingroups | Nie | Nie |
> | Profile/punkty końcowe/źródła | Nie | Nie |
> | validateProbe | Nie | Nie |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | certificateOrders | Tak | Tak |
> | certificateOrders/certyfikaty | Nie | Nie |
> | validateCertificateRegistrationInformation | Nie | Nie |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | możliwość | Nie | Nie |
> | domainNames | Nie | Nie |
> | domainNames/możliwości | Nie | Nie |
> | domainNames / internalLoadBalancers | Nie | Nie |
> | domainNames/servicecertificates | Nie | Nie |
> | domainNames/miejsca | Nie | Nie |
> | domainNames/gniazda/role | Nie | Nie |
> | domainNames/szczeliny/role/metricDefinitions | Nie | Nie |
> | domainNames/szczeliny/role/metryki | Nie | Nie |
> | moveSubscriptionResources | Nie | Nie |
> | operatingSystemFamilies | Nie | Nie |
> | operatingSystems | Nie | Nie |
> | quotas | Nie | Nie |
> | resourceTypes | Nie | Nie |
> | validateSubscriptionMoveAvailability | Nie | Nie |
> | virtualMachines | Nie | Nie |
> | virtualMachines/diagnosticSettings | Nie | Nie |
> | virtualMachines/metricDefinitions | Nie | Nie |
> | virtualMachines/metryki | Nie | Nie |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft. ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | Nie | Nie |

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | możliwość | Nie | Nie |
> | expressRouteCrossConnections | Nie | Nie |
> | expressRouteCrossConnections/Komunikacja równorzędna | Nie | Nie |
> | gatewaySupportedDevices | Nie | Nie |
> | networkSecurityGroups | Nie | Nie |
> | quotas | Nie | Nie |
> | reservedIps | Nie | Nie |
> | virtualNetworks | Nie | Nie |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | Nie | Nie |
> | virtualNetworks/virtualNetworkPeerings | Nie | Nie |

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | możliwość | Nie | Nie |
> | dysku | Nie | Nie |
> | images | Nie | Nie |
> | osImages | Nie | Nie |
> | osPlatformImages | Nie | Nie |
> | publicImages | Nie | Nie |
> | quotas | Nie | Nie |
> | storageAccounts | Nie | Nie |
> | storageAccounts/blobServices | Nie | Nie |
> | storageAccounts/fileServices | Nie | Nie |
> | storageAccounts/metricDefinitions | Nie | Nie |
> | storageAccounts/metryki | Nie | Nie |
> | storageAccounts/queueServices | Nie | Nie |
> | storageAccounts/usługi | Nie | Nie |
> | storageAccounts/usługi/diagnosticSettings | Nie | Nie |
> | storageAccounts/usługi/metricDefinitions | Nie | Nie |
> | storageAccounts/usługi/metryki | Nie | Nie |
> | storageAccounts/tableServices | Nie | Nie |
> | storageAccounts/vmImages | Nie | Nie |
> | vmImages | Nie | Nie |

## <a name="microsoftcognitiveservices"></a>Microsoft. CognitiveServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Konta | Tak | Tak |

## <a name="microsoftcommerce"></a>Microsoft. Commerce

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | RateCard | Nie | Nie |
> | UsageAggregates | Nie | Nie |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | availabilitySets | Tak | Tak |
> | diskEncryptionSets | Tak | Tak |
> | dysku | Tak | Tak |
> | znajduj | Tak | Tak |
> | Galerie/aplikacje | Nie | Nie |
> | Galerie/aplikacje/wersje | Nie | Nie |
> | Galerie/obrazy | Nie | Nie |
> | Galerie/obrazy/wersje | Nie | Nie |
> | hostGroups | Tak | Tak |
> | hostGroups/hosty | Tak | Tak |
> | images | Tak | Tak |
> | proximityPlacementGroups | Tak | Tak |
> | restorePointCollections | Tak | Tak |
> | restorePointCollections / restorePoints | Nie | Nie |
> | sharedVMExtensions | Tak | Tak |
> | sharedVMExtensions/wersje | Nie | Nie |
> | sharedVMImages | Tak | Tak |
> | sharedVMImages/wersje | Nie | Nie |
> | Migawki | Tak | Tak |
> | sshPublicKeys | Tak | Tak |
> | virtualMachines | Tak | Tak |
> | virtualMachines/rozszerzenia | Tak | Tak |
> | virtualMachines/metricDefinitions | Nie | Nie |
> | virtualMachineScaleSets | Tak | Tak |
> | virtualMachineScaleSets/rozszerzenia | Nie | Nie |
> | virtualMachineScaleSets/networkInterfaces | Nie | Nie |
> | virtualMachineScaleSets/adresów publicipaddress | Nie | Nie |
> | virtualMachineScaleSets/virtualMachines | Nie | Nie |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | Nie | Nie |

## <a name="microsoftconsumption"></a>Microsoft. zużycie

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | AggregatedCost | Nie | Nie |
> | Salda | Nie | Nie |
> | Budżety | Nie | Nie |
> | Opłaty | Nie | Nie |
> | CostTags | Nie | Nie |
> | środki | Nie | Nie |
> | zdarzenia | Nie | Nie |
> | Prognozy | Nie | Nie |
> | ilości | Nie | Nie |
> | Platform handlowych | Nie | Nie |
> | Arkusze cen | Nie | Nie |
> | wyrobów | Nie | Nie |
> | ReservationDetails | Nie | Nie |
> | ReservationRecommendations | Nie | Nie |
> | ReservationSummaries | Nie | Nie |
> | ReservationTransactions | Nie | Nie |
> | Tagi | Nie | Nie |
> | dzierżaw | Nie | Nie |
> | Terminologia | Nie | Nie |
> | UsageDetails | Nie | Nie |

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | containerGroups | Tak | Tak |
> | serviceAssociationLinks | Nie | Nie |

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | wołuje | Tak | Tak |
> | rejestry/agentPools | Tak | Tak |
> | rejestry/kompilacje | Nie | Nie |
> | rejestry/kompilacje/anulowanie | Nie | Nie |
> | rejestry/kompilacje/getLogLink | Nie | Nie |
> | rejestry/buildTasks | Tak | Tak |
> | rejestry/buildTasks/kroki | Nie | Nie |
> | rejestry/eventGridFilters | Nie | Nie |
> | rejestry/generateCredentials | Nie | Nie |
> | rejestry/getBuildSourceUploadUrl | Nie | Nie |
> | rejestry/getpoświadczenia | Nie | Nie |
> | rejestry/importImage | Nie | Nie |
> | rejestry/privateEndpointConnectionProxies | Nie | Nie |
> | rejestry/privateEndpointConnectionProxies/weryfikacja | Nie | Nie |
> | rejestry/privateEndpointConnections | Nie | Nie |
> | rejestry/privateLinkResources | Nie | Nie |
> | rejestry/queueBuild | Nie | Nie |
> | rejestry/regenerateCredential | Nie | Nie |
> | rejestry/regenerateCredentials | Nie | Nie |
> | rejestry/replikacje | Tak | Tak |
> | rejestry/uruchomienia | Nie | Nie |
> | rejestry/uruchomienia/Anuluj | Nie | Nie |
> | rejestry/scheduleRun | Nie | Nie |
> | rejestry/scopeMaps | Nie | Nie |
> | rejestry/taskRuns | Tak | Tak |
> | rejestry/zadania | Tak | Tak |
> | rejestry/tokeny | Nie | Nie |
> | rejestry/updatePolicies | Nie | Nie |
> | rejestry/elementy webhook | Tak | Tak |
> | rejestry/elementy webhook/getCallbackConfig | Nie | Nie |
> | rejestry/elementy webhook/polecenie ping | Nie | Nie |

## <a name="microsoftcontainerservice"></a>Microsoft. ContainerService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | containerServices | Tak | Tak |
> | managedClusters | Tak | Tak |
> | openShiftManagedClusters | Tak | Tak |

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Alerty | Nie | Nie |
> | BillingAccounts | Nie | Nie |
> | Budżety | Nie | Nie |
> | CloudConnectors | Nie | Nie |
> | Łączniki | Tak | Tak |
> | Działy | Nie | Nie |
> | Wymiary | Nie | Nie |
> | EnrollmentAccounts | Nie | Nie |
> | Eksportowanie | Nie | Nie |
> | ExternalBillingAccounts | Nie | Nie |
> | ExternalBillingAccounts/alerty | Nie | Nie |
> | ExternalBillingAccounts/wymiary | Nie | Nie |
> | ExternalBillingAccounts/Prognoza | Nie | Nie |
> | ExternalBillingAccounts/zapytanie | Nie | Nie |
> | ExternalSubscriptions | Nie | Nie |
> | ExternalSubscriptions/alerty | Nie | Nie |
> | ExternalSubscriptions/wymiary | Nie | Nie |
> | ExternalSubscriptions/Prognoza | Nie | Nie |
> | ExternalSubscriptions/zapytanie | Nie | Nie |
> | Prognoza | Nie | Nie |
> | Zapytanie | Nie | Nie |
> | zarejestruj | Nie | Nie |
> | Reportconfigs | Nie | Nie |
> | Raporty | Nie | Nie |
> | Ustawienia | Nie | Nie |
> | showbackRules | Nie | Nie |
> | Widoki | Nie | Nie |

## <a name="microsoftcustomerlockbox"></a>Microsoft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | żądań | Nie | Nie |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | związku | Nie | Nie |
> | resourceProviders | Tak | Tak |

## <a name="microsoftdatabox"></a>Microsoft. DataBox

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | zadania | Tak | Tak |

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | Tak | Tak |

## <a name="microsoftdatabricks"></a>Microsoft. datacegły

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | obszary robocze | Yes | Nie |
> | obszary robocze/dbworkspaces | Nie | Nie |
> | obszary robocze/storageEncryption | Nie | Nie |
> | obszary robocze/virtualNetworkPeerings | Nie | Nie |

## <a name="microsoftdatacatalog"></a>Microsoft. datacatalog

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | wykazy | Tak | Tak |
> | wykazy | Tak | Tak |
> | wykazy danych/źródła danych | Nie | Nie |
> | wykazy danych/źródła danych/skany | Nie | Nie |
> | wykazy danych/źródła danych/skany/zestawy danych | Nie | Nie |
> | wykazy danych/źródła danych/skanowania/wyzwalacze | Nie | Nie |

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Fabryki datafactors | Yes | Nie |
> | datafactors/diagnosticSettings | Nie | Nie |
> | datafactors/metricDefinitions | Nie | Nie |
> | dataFactorySchema | Nie | Nie |
> | fabryki | Yes | Nie |
> | fabryki/integrationRuntimes | Nie | Nie |

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Konta | Tak | Tak |
> | konta/dataLakeStoreAccounts | Nie | Nie |
> | konta/storageAccounts | Nie | Nie |
> | konta/storageAccounts/kontenery | Nie | Nie |
> | konta/transferAnalyticsUnits | Nie | Nie |

## <a name="microsoftdatalakestore"></a>Microsoft. kontach datalakestore

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Konta | Tak | Tak |
> | konta/eventGridFilters | Nie | Nie |
> | konta/firewallRules | Nie | Nie |

## <a name="microsoftdatamigration"></a>Migracja Microsoft.

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | services | Nie | Nie |
> | usługi/projekty | Nie | Nie |

## <a name="microsoftdatashare"></a>Microsoft. dataudział

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Konta | Tak | Tak |
> | konta/udziały | Nie | Nie |
> | konta/udziały/zestawy danych | Nie | Nie |
> | konta/udziały/zaproszenia | Nie | Nie |
> | konta/udziały/providersharesubscriptions | Nie | Nie |
> | konta/udziały/synchronizationSettings | Nie | Nie |
> | konta/sharesubscriptions | Nie | Nie |
> | konta/sharesubscriptions/consumerSourceDataSets | Nie | Nie |
> | konta/sharesubscriptions/datasetmappings | Nie | Nie |
> | konta/sharesubscriptions/wyzwalacze | Nie | Nie |

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | serwerem | Tak | Tak |
> | serwery/doradcy | Nie | Nie |
> | serwery/klucze | Nie | Nie |
> | serwery/privateEndpointConnectionProxies | Nie | Nie |
> | serwery/privateEndpointConnections | Nie | Nie |
> | serwery/privateLinkResources | Nie | Nie |
> | serwery/queryTexts | Nie | Nie |
> | serwery/recoverableServers | Nie | Nie |
> | serwery/topQueryStatistics | Nie | Nie |
> | serwery/virtualNetworkRules | Nie | Nie |
> | serwery/waitStatistics | Nie | Nie |

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | serwerem | Tak | Tak |
> | serwery/doradcy | Nie | Nie |
> | serwery/klucze | Nie | Nie |
> | serwery/privateEndpointConnectionProxies | Nie | Nie |
> | serwery/privateEndpointConnections | Nie | Nie |
> | serwery/privateLinkResources | Nie | Nie |
> | serwery/queryTexts | Nie | Nie |
> | serwery/recoverableServers | Nie | Nie |
> | serwery/topQueryStatistics | Nie | Nie |
> | serwery/virtualNetworkRules | Nie | Nie |
> | serwery/waitStatistics | Nie | Nie |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | serverGroups | Tak | Tak |
> | serwerem | Tak | Tak |
> | serwery/doradcy | Nie | Nie |
> | serwery/klucze | Nie | Nie |
> | serwery/privateEndpointConnectionProxies | Nie | Nie |
> | serwery/privateEndpointConnections | Nie | Nie |
> | serwery/privateLinkResources | Nie | Nie |
> | serwery/queryTexts | Nie | Nie |
> | serwery/recoverableServers | Nie | Nie |
> | serwery/topQueryStatistics | Nie | Nie |
> | serwery/virtualNetworkRules | Nie | Nie |
> | serwery/waitStatistics | Nie | Nie |
> | serversv2 | Tak | Tak |
> | singleServers | Tak | Tak |

## <a name="microsoftdeploymentmanager"></a>Microsoft. Deploymentmanager

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | artifactSources | Tak | Tak |
> | wprowadzanie | Tak | Tak |
> | servicetopologie | Tak | Tak |
> | servicetopologie/usługi | Tak | Tak |
> | servicetopologie/usługi/serviceunits | Tak | Tak |
> | kroki | Tak | Tak |

## <a name="microsoftdesktopvirtualization"></a>Microsoft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | applicationgroups | Tak | Tak |
> | applicationgroups/aplikacje | Nie | Nie |
> | applicationgroups/komputery stacjonarne | Nie | Nie |
> | applicationgroups / startmenuitems | Nie | Nie |
> | hostpools | Tak | Tak |
> | hostpools / sessionhosts | Nie | Nie |
> | hostpools / sessionhosts / usersessions | Nie | Nie |
> | hostpools / usersessions | Nie | Nie |
> | obszary robocze | Tak | Tak |

## <a name="microsoftdevices"></a>Microsoft. urządzenia

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | ElasticPools | Tak | Tak |
> | ElasticPools / IotHubTenants | Tak | Tak |
> | ElasticPools/IotHubTenants/securitySettings | Nie | Nie |
> | IotHubs | Tak | Tak |
> | IotHubs/eventGridFilters | Nie | Nie |
> | IotHubs/securitySettings | Nie | Nie |
> | ProvisioningServices | Tak | Tak |
> | użycia | Nie | Nie |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | potoki | Tak | Tak |

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | kontrolery | Tak | Tak |

## <a name="microsoftdevtestlab"></a>Microsoft. wspólny

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | labcenters | Tak | Tak |
> | Labs | Tak | Tak |
> | laboratoria/środowiska | Tak | Tak |
> | laboratoria/servicerunner | Tak | Tak |
> | Labs/virtualMachines | Tak | Tak |
> | Uruchamianie | Tak | Tak |

## <a name="microsoftdocumentdb"></a>Microsoft. DocumentDB

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | databaseAccountNames | Nie | Nie |
> | databaseAccounts | Tak | Tak |

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | 3.x | Tak | Tak |
> | domeny/domainOwnershipIdentifiers | Nie | Nie |
> | generateSsoRequest | Nie | Nie |
> | topLevelDomains | Nie | Nie |
> | validateDomainRegistrationInformation | Nie | Nie |

## <a name="microsoftdynamicslcs"></a>Microsoft. DynamicsLcs

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | lcsprojects | Nie | Nie |
> | lcsprojects / clouddeployments | Nie | Nie |
> | lcsprojects/łączniki | Nie | Nie |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | services | Tak | Tak |

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | 3.x | Tak | Tak |
> | domeny/tematy | Nie | Nie |
> | eventSubscriptions | Nie | Nie |
> | extensionTopics | Nie | Nie |
> | partnerNamespaces | Tak | Tak |
> | partnerNamespaces/eventChannels | Nie | Nie |
> | partnerRegistrations | Tak | Tak |
> | partnerTopics | Tak | Tak |
> | partnerTopics / eventSubscriptions | Nie | Nie |
> | systemTopics | Tak | Tak |
> | systemTopics / eventSubscriptions | Nie | Nie |
> | opisano | Tak | Tak |
> | topicTypes | Nie | Nie |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | oparty | Tak | Tak |
> | przestrzeni | Tak | Tak |
> | przestrzenie nazw/reguł autoryzacji | Nie | Nie |
> | przestrzenie nazw/disasterrecoveryconfigs | Nie | Nie |
> | przestrzenie nazw/eventhubs | Nie | Nie |
> | przestrzenie nazw/eventhubs/reguł autoryzacji | Nie | Nie |
> | przestrzenie nazw/eventhubs/consumergroups | Nie | Nie |
> | przestrzenie nazw/networkrulesets | Nie | Nie |

## <a name="microsoftfalcon"></a>Microsoft. Falcon

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | przestrzeni | Tak | Tak |

## <a name="microsoftfeatures"></a>Microsoft. Features

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | featureProviders | Nie | Nie |
> | funkcje | Nie | Nie |
> | dostawców | Nie | Nie |
> | subscriptionFeatureRegistrations | Nie | Nie |

## <a name="microsoftgallery"></a>Microsoft. Gallery

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | pisaną | Nie | Nie |
> | galleryitems | Nie | Nie |
> | generateartifactaccessuri | Nie | Nie |
> | Moje obszary | Nie | Nie |
> | Moje obszary/obszary | Nie | Nie |
> | Moje obszary/obszary/obszary | Nie | Nie |
> | Moje obszary/obszary/obszary/galleryitems | Nie | Nie |
> | Moje obszary/obszary/galleryitems | Nie | Nie |
> | Moje obszary/galleryitems | Nie | Nie |
> | zarejestruj | Nie | Nie |
> | zasoby | Nie | Nie |
> | retrieveresourcesbyid | Nie | Nie |

## <a name="microsoftgenomics"></a>Microsoft. genomika

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Konta | Tak | Tak |

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | autoManagedAccounts | Tak | Tak |
> | autoManagedVmConfigurationProfiles | Tak | Tak |
> | configurationProfileAssignments | Nie | Nie |
> | guestConfigurationAssignments | Nie | Nie |
> | programy | Nie | Nie |
> | softwareUpdateProfile | Nie | Nie |
> | softwareUpdates | Nie | Nie |

## <a name="microsofthanaonazure"></a>Microsoft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | hanaInstances | Tak | Tak |
> | sapMonitors | Tak | Tak |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft. HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Modułów dedicatedhsms | Tak | Tak |

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | oparty | Tak | Tak |
> | klastry/aplikacje | Nie | Nie |

## <a name="microsofthealthcareapis"></a>Microsoft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | services | Tak | Tak |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Win64 | Tak | Tak |
> | Maszyny/rozszerzenia | Tak | Tak |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | datamanagers | Tak | Tak |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | komponentów | Tak | Tak |
> | networkScopes | Tak | Tak |

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | zadania | Tak | Tak |

## <a name="microsoftinsights"></a>Microsoft. Insights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | actionGroups | Tak | Tak |
> | activityLogAlerts | Tak | Tak |
> | alertrules | Tak | Tak |
> | autoscalesettings | Tak | Tak |
> | komponentów | Tak | Tak |
> | składniki/linkedStorageAccounts | Nie | Nie |
> | składniki/ProactiveDetectionConfigs | Nie | Nie |
> | diagnosticSettings | Nie | Nie |
> | guestDiagnosticSettings | Tak | Tak |
> | guestDiagnosticSettingsAssociation | Tak | Tak |
> | logprofiles | Tak | Tak |
> | metricAlerts | Tak | Tak |
> | privateLinkScopes | Tak | Tak |
> | privateLinkScopes / privateEndpointConnections | Nie | Nie |
> | privateLinkScopes / scopedResources | Nie | Nie |
> | queryPacks | Tak | Tak |
> | queryPacks/zapytania | Nie | Nie |
> | scheduledQueryRules | Tak | Tak |
> | testów internetowych | Tak | Tak |
> | skoroszyty | Tak | Tak |
> | workbooktemplates | Tak | Tak |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Nie | Nie |
> | diagnosticSettingsCategories | Nie | Nie |

## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | appTemplates | Nie | Nie |
> | IoTApps | Tak | Tak |

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Graph | Tak | Tak |

## <a name="microsoftkeyvault"></a>Microsoft. kluczy — magazyn

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | deletedVaults | Nie | Nie |
> | hsmPools | Tak | Tak |
> | magazynów | Tak | Tak |
> | magazyny/accessPolicies | Nie | Nie |
> | magazyny/eventGridFilters | Nie | Nie |
> | magazyny/wpisy tajne | Nie | Nie |

## <a name="microsoftkubernetes"></a>Microsoft. Kubernetes

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | connectedClusters | Tak | Tak |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | oparty | Tak | Tak |
> | klastry/attacheddatabaseconfigurations | Nie | Nie |
> | klastry/bazy danych | Nie | Nie |
> | klastry/bazy danych/połączenia Databases | Nie | Nie |
> | klastry/bazy danych/eventhubconnections | Nie | Nie |
> | klastry/bazy danych/principalassignments | Nie | Nie |
> | klastry/połączenia dataconnections | Nie | Nie |
> | klastry/principalassignments | Nie | Nie |
> | klastry/sharedidentities | Nie | Nie |

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | labaccounts | Tak | Tak |
> | users | Nie | Nie |

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | Tak | Tak |
> | integrationAccounts | Tak | Tak |
> | integrationServiceEnvironments | Tak | Tak |
> | integrationServiceEnvironments/managedApis | Tak | Tak |
> | isolatedEnvironments | Tak | Tak |
> | przebieg | Tak | Tak |

## <a name="microsoftmachinelearning"></a>Microsoft. MachineLearning

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | Tak | Tak |
> | webServices | Tak | Tak |
> | Obszary robocze | Tak | Tak |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | obszary robocze | Tak | Tak |
> | obszary robocze/obliczenia | Nie | Nie |
> | obszary robocze/eventGridFilters | Nie | Nie |

## <a name="microsoftmaintenance"></a>Microsoft. Maintenance

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | applyUpdates | Nie | Nie |
> | configurationAssignments | Nie | Nie |
> | maintenanceConfigurations | Tak | Tak |
> | aktualizacje | Nie | Nie |

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Tożsamości | Nie | Nie |
> | Resourceidentity | Tak | Tak |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinitions | Nie | Nie |
> | registrationAssignments | Nie | Nie |
> | registrationDefinitions | Nie | Nie |

## <a name="microsoftmanagement"></a>Microsoft. Management

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | getentities | Nie | Nie |
> | managementGroups | Nie | Nie |
> | managementGroups/ustawienia | Nie | Nie |
> | zasoby | Nie | Nie |
> | startTenantBackfill | Nie | Nie |
> | tenantBackfillStatus | Nie | Nie |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Konta | Tak | Tak |
> | konta/eventGridFilters | Nie | Nie |
> | konta/privateAtlases | Tak | Tak |

## <a name="microsoftmarketplace"></a>Microsoft. Marketplace

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | otrzymane | Nie | Nie |
> | offerTypes | Nie | Nie |
> | offerTypes/wydawcy | Nie | Nie |
> | offerTypes/wydawcy/oferty | Nie | Nie |
> | offerTypes/wydawcy/oferty/plany | Nie | Nie |
> | offerTypes/wydawcy/oferty/plany/umowy | Nie | Nie |
> | offerTypes/wydawcy/oferty/plany/konfiguracje | Nie | Nie |
> | offerTypes/wydawcy/oferty/plany/konfiguracje/importImage | Nie | Nie |
> | privategalleryitems | Nie | Nie |
> | privateStoreClient | Nie | Nie |
> | privateStores | Nie | Nie |
> | privateStores/oferty | Nie | Nie |
> | wyrobów | Nie | Nie |
> | wydawcy | Nie | Nie |
> | wydawcy/oferty | Nie | Nie |
> | wydawcy/oferty/zmiany | Nie | Nie |
> | zarejestruj | Nie | Nie |

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | classicDevServices | Tak | Tak |
> | updateCommunicationPreference | Nie | Nie |

## <a name="microsoftmarketplaceordering"></a>Microsoft. MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | kontrakt | Nie | Nie |
> | offertypes | Nie | Nie |

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | MediaServices | Tak | Tak |
> | MediaServices/accountFilters | Nie | Nie |
> | MediaServices/zasoby | Nie | Nie |
> | MediaServices/assetFilters | Nie | Nie |
> | MediaServices/contentKeyPolicies | Nie | Nie |
> | MediaServices/eventGridFilters | Nie | Nie |
> | MediaServices/liveEventOperations | Nie | Nie |
> | MediaServices/liveEvents | Tak | Tak |
> | MediaServices/liveEvents/liveOutputs | Nie | Nie |
> | MediaServices/liveOutputOperations | Nie | Nie |
> | MediaServices/mediaGraphs | Nie | Nie |
> | MediaServices/streamingEndpointOperations | Nie | Nie |
> | MediaServices/streamingEndpoints | Tak | Tak |
> | MediaServices/streamingLocators | Nie | Nie |
> | MediaServices/streamingPolicies | Nie | Nie |
> | MediaServices/transformacje | Nie | Nie |
> | MediaServices/transformacje/zadania | Nie | Nie |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | appClusters | Tak | Tak |

## <a name="microsoftmigrate"></a>Microsoft. Migrowanie

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | assessmentProjects | Tak | Tak |
> | migrateprojects | Tak | Tak |
> | moveCollections | Tak | Tak |
> | projektami | Tak | Tak |

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastAccounts | Tak | Tak |
> | objectUnderstandingAccounts | Tak | Tak |
> | remoteRenderingAccounts | Tak | Tak |
> | spatialAnchorsAccounts | Tak | Tak |

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | Yes | Nie |
> | netAppAccounts / accountBackups | Nie | Nie |
> | netAppAccounts / capacityPools | Yes | Nie |
> | netAppAccounts/capacityPools/woluminy | Yes | Nie |
> | netAppAccounts/capacityPools/woluminy/migawki | Nie | Nie |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | applicationGateways | Tak | Tak |
> | applicationGatewayWebApplicationFirewallPolicies | Tak | Tak |
> | applicationSecurityGroups | Tak | Tak |
> | azureFirewallFqdnTags | Nie | Nie |
> | azureFirewalls | Yes | Nie |
> | bastionHosts | Yes | Nie |
> | bgpServiceCommunities | Nie | Nie |
> | Licznik | Tak | Tak |
> | ddosCustomPolicies | Tak | Tak |
> | ddosProtectionPlans | Tak | Tak |
> | dnsOperationStatuses | Nie | Nie |
> | dnszones | Tak | Tak |
> | dnszones/A | Nie | Nie |
> | dnszones/AAAA | Nie | Nie |
> | dnszones/wszystkie | Nie | Nie |
> | dnszones/CAA | Nie | Nie |
> | dnszones/CNAME | Nie | Nie |
> | dnszones/MX | Nie | Nie |
> | dnszones/NS | Nie | Nie |
> | dnszones/PTR | Nie | Nie |
> | dnszones/zestawy rekordów | Nie | Nie |
> | dnszones/SOA | Nie | Nie |
> | dnszones/SRV | Nie | Nie |
> | dnszones/TXT | Nie | Nie |
> | expressRouteCircuits | Tak | Tak |
> | expressRouteCrossConnections | Tak | Tak |
> | expressRouteGateways | Tak | Tak |
> | expressRoutePorts | Tak | Tak |
> | expressRouteServiceProviders | Nie | Nie |
> | firewallPolicies | Tak | Tak |
> | usługi frontdoor | Tak, ale ograniczone (patrz [Uwaga poniżej](#frontdoor)) | Tak |
> | frontdoorWebApplicationFirewallManagedRuleSets | Tak, ale ograniczone (patrz [Uwaga poniżej](#frontdoor)) | Nie |
> | frontdoorWebApplicationFirewallPolicies | Tak, ale ograniczone (patrz [Uwaga poniżej](#frontdoor)) | Tak |
> | getDnsResourceReference | Nie | Nie |
> | internalNotify | Nie | Nie |
> | loadBalancers | Tak | Tak |
> | localNetworkGateways | Tak | Tak |
> | natGateways | Tak | Tak |
> | networkIntentPolicies | Tak | Tak |
> | networkInterfaces | Tak | Tak |
> | networkProfiles | Tak | Tak |
> | networkSecurityGroups | Tak | Tak |
> | networkWatchers | Tak | Tak |
> | networkWatchers / connectionMonitors | Yes | Nie |
> | networkWatchers / flowLogs | Nie | Nie |
> | networkWatchers/soczewki | Yes | Nie |
> | networkWatchers / pingMeshes | Yes | Nie |
> | p2sVpnGateways | Tak | Tak |
> | privateDnsOperationStatuses | Nie | Nie |
> | privateDnsZones | Tak | Tak |
> | privateDnsZones/A | Nie | Nie |
> | privateDnsZones/AAAA | Nie | Nie |
> | privateDnsZones/wszystkie | Nie | Nie |
> | privateDnsZones/CNAME | Nie | Nie |
> | privateDnsZones/MX | Nie | Nie |
> | privateDnsZones/PTR | Nie | Nie |
> | privateDnsZones/SOA | Nie | Nie |
> | privateDnsZones/SRV | Nie | Nie |
> | privateDnsZones/TXT | Nie | Nie |
> | privateDnsZones / virtualNetworkLinks | Tak | Tak |
> | privateEndpoints | Tak | Tak |
> | privateLinkServices | Tak | Tak |
> | Adresów publicipaddress | Tak | Tak |
> | publicIPPrefixes | Tak | Tak |
> | routeFilters | Tak | Tak |
> | routeTables | Tak | Tak |
> | serviceEndpointPolicies | Tak | Tak |
> | trafficManagerGeographicHierarchies | Nie | Nie |
> | trafficmanagerprofiles | Tak | Tak |
> | trafficmanagerprofiles/map cieplnych | Nie | Nie |
> | trafficManagerUserMetricsKeys | Nie | Nie |
> | virtualHubs | Tak | Tak |
> | virtualNetworkGateways | Tak | Tak |
> | virtualNetworks | Tak | Tak |
> | virtualNetworks/podsieci | Nie | Nie |
> | virtualNetworkTaps | Tak | Tak |
> | virtualWans | Yes | Nie |
> | vpnGateways | Tak | Tak |
> | vpnSites | Tak | Tak |
> | webApplicationFirewallPolicies | Tak | Tak |

<a id="frontdoor" />

> [!NOTE]
> W przypadku usługi Azure front-drzwi można zastosować Tagi podczas tworzenia zasobu, ale aktualizowanie lub Dodawanie tagów nie jest obecnie obsługiwane.


## <a name="microsoftnotebooks"></a>Microsoft. notesy

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | NotebookProxies | Nie | Nie |

## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | przestrzeni | Yes | Nie |
> | przestrzenie nazw/notificationHubs | Yes | Nie |

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | osNamespaces | Tak | Tak |

## <a name="microsoftoffazure"></a>Microsoft. OffAzure

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | HyperVSites | Tak | Tak |
> | ImportSites | Tak | Tak |
> | ServerSites | Tak | Tak |
> | VMwareSites | Tak | Tak |

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | oparty | Tak | Tak |
> | linkTargets | Nie | Nie |
> | storageInsightConfigs | Nie | Nie |
> | obszary robocze | Tak | Tak |
> | obszary robocze/eksporty | Nie | Nie |
> | obszary robocze/źródła danych | Nie | Nie |
> | obszary robocze/linkedServices | Nie | Nie |
> | obszary robocze/linkedStorageAccounts | Nie | Nie |
> | obszary robocze/zapytanie | Nie | Nie |
> | obszary robocze/scopedPrivateLinkProxies | Nie | Nie |

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | managementassociations | Nie | Nie |
> | managementconfigurations | Tak | Tak |
> | rozwiązania | Tak | Tak |
> | widoki | Tak | Tak |

## <a name="microsoftpeering"></a>Microsoft. Komunikacja równorzędna

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | legacyPeerings | Nie | Nie |
> | peerAsns | Nie | Nie |
> | komunikacje równorzędne | Tak | Tak |
> | peeringServiceCountries | Nie | Nie |
> | peeringServiceProviders | Nie | Nie |
> | peeringServices | Tak | Tak |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | policyEvents | Nie | Nie |
> | policyMetadata | Nie | Nie |
> | policyStates | Nie | Nie |
> | policyTrackedResources | Nie | Nie |
> | korygowania | Nie | Nie |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Konsole programu | Nie | Nie |
> | pulpity nawigacyjne | Tak | Tak |
> | userSettings | Nie | Nie |

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | workspaceCollections | Tak | Tak |

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | produkcyjnych | Tak | Tak |

## <a name="microsoftprojectbabylon"></a>Microsoft. ProjectBabylon

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Konta | Tak | Tak |

## <a name="microsoftquantum"></a>Microsoft. Quantum

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Obszary robocze | Tak | Tak |

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | Nie | Nie |
> | magazynów | Tak | Tak |

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | przestrzeni | Tak | Tak |
> | przestrzenie nazw/reguł autoryzacji | Nie | Nie |
> | przestrzenie nazw/hybridconnections | Nie | Nie |
> | przestrzenie nazw/hybridconnections/reguł autoryzacji | Nie | Nie |
> | przestrzenie nazw/wcfrelays | Nie | Nie |
> | przestrzenie nazw/wcfrelays/reguł autoryzacji | Nie | Nie |

## <a name="microsoftremoteapp"></a>Microsoft. RemoteApp

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Konta | Nie | Nie |
> | Kolekcje | Tak | Tak |
> | Kolekcje/aplikacje | Nie | Nie |
> | Kolekcje/SecurityPrincipals | Nie | Nie |
> | templateImages | Nie | Nie |

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | wybiera | Tak | Tak |
> | resourceChangeDetails | Nie | Nie |
> | resourceChanges | Nie | Nie |
> | zasoby | Nie | Nie |
> | resourcesHistory | Nie | Nie |
> | subscriptionsStatus | Nie | Nie |

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | Nie | Nie |
> | childAvailabilityStatuses | Nie | Nie |
> | childResources | Nie | Nie |
> | emergingissues | Nie | Nie |
> | zdarzenia | Nie | Nie |
> | impactedResources | Nie | Nie |
> | metadane | Nie | Nie |
> | powiadomienia | Nie | Nie |

## <a name="microsoftresources"></a>Microsoft. resources

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | komputerów | Yes | Nie |
> | wdrożenia/operacje | Nie | Nie |
> | deploymentScripts | Tak | Tak |
> | deploymentScripts/dzienniki | Nie | Nie |
> | linki | Nie | Nie |
> | notifyResourceJobs | Nie | Nie |
> | dostawców | Nie | Nie |
> | resourceGroups | Yes | Nie |
> | opłaty | Yes | Nie |
> | dzierżaw | Nie | Nie |

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | aplikacje | Tak | Tak |
> | saasresources | Nie | Nie |

## <a name="microsoftsearch"></a>Microsoft. Search

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | Nie | Nie |
> | searchServices | Tak | Tak |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | Nie | Nie |
> | advancedThreatProtectionSettings | Nie | Nie |
> | alerts | Nie | Nie |
> | allowedConnections | Nie | Nie |
> | applicationWhitelistings | Nie | Nie |
> | assessmentMetadata | Nie | Nie |
> | oceny | Nie | Nie |
> | autoDismissAlertsRules | Nie | Nie |
> | automatyzacji | Tak | Tak |
> | AutoProvisioningSettings | Nie | Nie |
> | Zachowania zgodności prawnymi | Nie | Nie |
> | dataCollectionAgents | Nie | Nie |
> | deviceSecurityGroups | Nie | Nie |
> | discoveredSecuritySolutions | Nie | Nie |
> | externalSecuritySolutions | Nie | Nie |
> | InformationProtectionPolicies | Nie | Nie |
> | iotSecuritySolutions | Tak | Tak |
> | iotSecuritySolutions / analyticsModels | Nie | Nie |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | Nie | Nie |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | Nie | Nie |
> | jitNetworkAccessPolicies | Nie | Nie |
> | networkData | Nie | Nie |
> | policies | Nie | Nie |
> | cen | Nie | Nie |
> | regulatoryComplianceStandards | Nie | Nie |
> | regulatoryComplianceStandards / regulatoryComplianceControls | Nie | Nie |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | Nie | Nie |
> | secureScoreControlDefinitions | Nie | Nie |
> | secureScoreControls | Nie | Nie |
> | secureScores | Nie | Nie |
> | secureScores / secureScoreControls | Nie | Nie |
> | securityContacts | Nie | Nie |
> | securitySolutions | Nie | Nie |
> | securitySolutionsReferenceData | Nie | Nie |
> | securityStatuses | Nie | Nie |
> | securityStatusesSummaries | Nie | Nie |
> | serverVulnerabilityAssessments | Nie | Nie |
> | settings | Nie | Nie |
> | podoceny | Nie | Nie |
> | zadania | Nie | Nie |
> | replikacji | Nie | Nie |
> | workspaceSettings | Nie | Nie |

## <a name="microsoftsecuritygraph"></a>Microsoft. SecurityGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Nie | Nie |
> | diagnosticSettingsCategories | Nie | Nie |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | agregacji | Nie | Nie |
> | alertRules | Nie | Nie |
> | alertRuleTemplates | Nie | Nie |
> | zakładki | Nie | Nie |
> | padkach | Nie | Nie |
> | Połączenia dataconnecters | Nie | Nie |
> | dataConnectorsCheckRequirements | Nie | Nie |
> | obiekty | Nie | Nie |
> | entityQueries | Nie | Nie |
> | zdarzenia | Nie | Nie |
> | officeConsents | Nie | Nie |
> | settings | Nie | Nie |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | przestrzeni | Tak | Tak |
> | przestrzenie nazw/reguł autoryzacji | Nie | Nie |
> | przestrzenie nazw/disasterrecoveryconfigs | Nie | Nie |
> | przestrzenie nazw/eventgridfilters | Nie | Nie |
> | przestrzenie nazw/networkrulesets | Nie | Nie |
> | przestrzenie nazw/kolejki | Nie | Nie |
> | przestrzenie nazw/kolejki/reguł autoryzacji | Nie | Nie |
> | obszary nazw/tematy | Nie | Nie |
> | przestrzenie nazw/tematy/reguł autoryzacji | Nie | Nie |
> | obszary nazw/tematy/subskrypcje | Nie | Nie |
> | obszary nazw/tematy/subskrypcje/reguły | Nie | Nie |
> | premiumMessagingRegions | Nie | Nie |

## <a name="microsoftservicefabric"></a>Microsoft. servicefabric

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | aplikacje | Tak | Tak |
> | oparty | Tak | Tak |
> | klastry/aplikacje | Nie | Nie |
> | containerGroups | Tak | Tak |
> | containerGroupSets | Tak | Tak |
> | edgeclusters | Tak | Tak |
> | edgeclusters/aplikacje | Nie | Nie |
> | managedclusters | Tak | Tak |
> | managedclusters/elementów NodeType | Nie | Nie |
> | sieci | Tak | Tak |
> | secretstores | Tak | Tak |
> | secretstores/certyfikaty | Nie | Nie |
> | secretstores/wpisy tajne | Nie | Nie |
> | volumes | Tak | Tak |

## <a name="microsoftservicefabricmesh"></a>Microsoft. ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | aplikacje | Tak | Tak |
> | containerGroups | Tak | Tak |
> | bram | Tak | Tak |
> | sieci | Tak | Tak |
> | wpisy tajne | Tak | Tak |
> | volumes | Tak | Tak |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | Nie | Nie |
> | providerRegistrations / resourceTypeRegistrations | Nie | Nie |
> | wprowadzanie | Tak | Tak |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | SignalR | Tak | Tak |
> | Sygnalizujący/eventGridFilters | Nie | Nie |

## <a name="microsoftsiterecovery"></a>Microsoft. SiteRecovery

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | SiteRecoveryVault | Tak | Tak |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | Nie | Nie |

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | Tak | Tak |
> | aplikacje | Tak | Tak |
> | jitRequests | Tak | Tak |

## <a name="microsoftspoolservice"></a>Microsoft. SpoolService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | registeredSubscriptions | Nie | Nie |
> | bufory | Tak | Tak |


## <a name="microsoftsql"></a>Microsoft. SQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | managedInstances | Tak | Tak |
> | managedInstances/bazy danych | Tak (patrz [Uwaga poniżej](#sqlnote)) | Tak |
> | managedInstances/Databases/backupShortTermRetentionPolicies | Nie | Nie |
> | managedInstances/bazy danych/schematy/tabele/kolumny/sensitivityLabels | Nie | Nie |
> | managedInstances/Databases/vulnerabilityAssessments | Nie | Nie |
> | managedInstances/bazy danych/vulnerabilityAssessments/reguły/linie bazowe | Nie | Nie |
> | managedInstances / encryptionProtector | Nie | Nie |
> | managedInstances/klucze | Nie | Nie |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | Nie | Nie |
> | managedInstances / vulnerabilityAssessments | Nie | Nie |
> | serwerem | Tak | Tak |
> | serwery/Administratorzy | Nie | Nie |
> | serwery/communicationLinks | Nie | Nie |
> | Serwery/bazy danych | Tak (patrz [Uwaga poniżej](#sqlnote)) | Tak |
> | serwery/encryptionProtector | Nie | Nie |
> | serwery/firewallRules | Nie | Nie |
> | serwery/klucze | Nie | Nie |
> | serwery/restorableDroppedDatabases | Nie | Nie |
> | serwery/cele | Nie | Nie |
> | serwery/tdeCertificates | Nie | Nie |
> | virtualClusters | Nie | Nie |

<a id="sqlnote" />

> [!NOTE]
> Baza danych Master nie obsługuje tagów, ale inne bazy danych, w tym bazy danych Azure SQL Data Warehouse, obsługują Tagi. Azure SQL Data Warehouse bazy danych muszą znajdować się w stanie aktywnym (niewstrzymanym).

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | Tak | Tak |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Nie | Nie |
> | SqlVirtualMachines | Tak | Tak |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | storageAccounts | Tak | Tak |
> | storageAccounts/blobServices | Nie | Nie |
> | storageAccounts/fileServices | Nie | Nie |
> | storageAccounts/queueServices | Nie | Nie |
> | storageAccounts/usługi | Nie | Nie |
> | storageAccounts/usługi/metricDefinitions | Nie | Nie |
> | storageAccounts/tableServices | Nie | Nie |
> | użycia | Nie | Nie |

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | pamięci podręcznych | Tak | Tak |
> | pamięci podręczne/storageTargets | Nie | Nie |
> | usageModels | Nie | Nie |

## <a name="microsoftstoragereplication"></a>Microsoft. StorageReplication

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | replicationGroups | Nie | Nie |

## <a name="microsoftstoragesync"></a>Microsoft. StorageSync

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Tak | Tak |
> | storageSyncServices/registeredServers | Nie | Nie |
> | storageSyncServices / syncGroups | Nie | Nie |
> | storageSyncServices / syncGroups / cloudEndpoints | Nie | Nie |
> | storageSyncServices / syncGroups / serverEndpoints | Nie | Nie |
> | storageSyncServices/przepływy pracy | Nie | Nie |

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Tak | Tak |
> | storageSyncServices/registeredServers | Nie | Nie |
> | storageSyncServices / syncGroups | Nie | Nie |
> | storageSyncServices / syncGroups / cloudEndpoints | Nie | Nie |
> | storageSyncServices / syncGroups / serverEndpoints | Nie | Nie |
> | storageSyncServices/przepływy pracy | Nie | Nie |

## <a name="microsoftstoragesyncint"></a>Microsoft. StorageSyncInt

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Tak | Tak |
> | storageSyncServices/registeredServers | Nie | Nie |
> | storageSyncServices / syncGroups | Nie | Nie |
> | storageSyncServices / syncGroups / cloudEndpoints | Nie | Nie |
> | storageSyncServices / syncGroups / serverEndpoints | Nie | Nie |
> | storageSyncServices/przepływy pracy | Nie | Nie |

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | skonfigurowany | Tak | Tak |

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | streamingjobs | Tak (patrz Uwaga poniżej) | Tak |

> [!NOTE]
> Nie można dodać znacznika, gdy streamingjobs jest uruchomiony. Zatrzymaj zasób, aby dodać tag.

## <a name="microsoftsubscription"></a>Microsoft. Subscription

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | Anuluj | Nie | Nie |
> | Anulowanie subskrypcji | Nie | Nie |
> | Włącz | Nie | Nie |
> | zmień nazwę | Nie | Nie |
> | SubscriptionDefinitions | Nie | Nie |
> | SubscriptionOperations | Nie | Nie |
> | opłaty | Nie | Nie |

## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | wiejski | Yes | Nie |
> | środowiska/accessPolicies | Nie | Nie |
> | środowiska/źródła zdarzeń | Yes | Nie |
> | środowiska/referenceDataSets | Yes | Nie |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | dedicatedCloudNodes | Tak | Tak |
> | dedicatedCloudServices | Tak | Tak |
> | virtualMachines | Tak | Tak |

## <a name="microsoftvnfmanager"></a>Microsoft. VnfManager

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | devices | Tak | Tak |
> | registeredSubscriptions | Nie | Nie |
> | dostawców | Nie | Nie |
> | dostawcy/jednostki SKU | Nie | Nie |
> | dostawcy/vnfs | Nie | Nie |
> | virtualNetworkFunctionSkus | Nie | Nie |
> | vnfs | Tak | Tak |

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | Nie | Nie |
> | apiManagementAccounts / apiAcls | Nie | Nie |
> | apiManagementAccounts/interfejsy API | Nie | Nie |
> | apiManagementAccounts/interfejsy API/apiAcls | Nie | Nie |
> | apiManagementAccounts/interfejsy API/connectionAcls | Nie | Nie |
> | apiManagementAccounts/interfejsy API/połączenia | Nie | Nie |
> | apiManagementAccounts/interfejsy API/połączenia/connectionAcls | Nie | Nie |
> | apiManagementAccounts/interfejsy API/localizedDefinitions | Nie | Nie |
> | apiManagementAccounts / connectionAcls | Nie | Nie |
> | apiManagementAccounts/połączenia | Nie | Nie |
> | billingMeters | Nie | Nie |
> | certyfikaty | Tak | Tak |
> | connectionGateways | Tak | Tak |
> | Licznik | Tak | Tak |
> | customApis | Tak | Tak |
> | deletedSites | Nie | Nie |
> | hostingEnvironments | Tak | Tak |
> | hostingEnvironments / eventGridFilters | Nie | Nie |
> | hostingEnvironments / multiRolePools | Nie | Nie |
> | hostingEnvironments / workerPools | Nie | Nie |
> | kubeEnvironments | Tak | Tak |
> | publishingUsers | Nie | Nie |
> | zalecenia | Nie | Nie |
> | resourceHealthMetadata | Nie | Nie |
> | Runtime | Nie | Nie |
> | Dopuszczalna | Tak | Tak |
> | Dopuszczalna/eventGridFilters | Nie | Nie |
> | lokacje | Tak | Tak |
> | Lokacje/konfiguracja  | Nie | Nie |
> | Lokacje/eventGridFilters | Nie | Nie |
> | Lokacje/hostNameBindings | Nie | Nie |
> | Lokacje/networkConfig | Nie | Nie |
> | Lokacje/premieraddons | Tak | Tak |
> | Lokacje/miejsca | Tak | Tak |
> | Lokacje/miejsca/eventGridFilters | Nie | Nie |
> | Lokacje/miejsca/hostNameBindings | Nie | Nie |
> | Lokacje/miejsca/networkConfig | Nie | Nie |
> | sourceControls | Nie | Nie |
> | staticSites | Tak | Tak |
> | legalizacj | Nie | Nie |
> | verifyHostingEnvironmentVnet | Nie | Nie |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft. WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Nie | Nie |
> | diagnosticSettingsCategories | Nie | Nie |

## <a name="microsoftwindowsiot"></a>Microsoft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | DeviceServices | Tak | Tak |

## <a name="microsoftworkloadmonitor"></a>Microsoft. Monitor obciążenia został

> [!div class="mx-tableFixed"]
> | Typ zasobu | Obsługuje Tagi | Tag w raporcie kosztów |
> | ------------- | ----------- | ----------- |
> | komponentów | Nie | Nie |
> | componentsSummary | Nie | Nie |
> | monitorInstances | Nie | Nie |
> | monitorInstancesSummary | Nie | Nie |
> | monitora | Nie | Nie |
> | notificationSettings | Nie | Nie |

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zastosować Tagi do zasobów, zobacz [Używanie tagów do organizowania zasobów platformy Azure](tag-resources.md).
