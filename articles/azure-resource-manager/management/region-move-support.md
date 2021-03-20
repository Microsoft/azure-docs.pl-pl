---
title: Obsługa przemieszczania zasobów platformy Azure między regionami
description: Wyświetla listę typów zasobów platformy Azure, które mogą być przenoszone w regionach platformy Azure
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 08/25/2020
ms.author: raynew
ms.openlocfilehash: 18d4d84462d528b718d784ff6a16ecf990ed0d20
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100094019"
---
# <a name="support-for-moving-azure-resources-across-regions"></a>Obsługa przemieszczania zasobów platformy Azure między regionami

Ten artykuł potwierdza, czy typ zasobu platformy Azure jest obsługiwany w przypadku przechodzenia do innego regionu platformy Azure. 

Przejdź do przestrzeni nazw dostawcy zasobów:
> [!div class="op_single_selector"]
> - [Microsoft. AAD](#microsoftaad)
> - [Microsoft. aadiam](#microsoftaadiam)
> - [Microsoft. Dodatki](#microsoftaddons)
> - [Microsoft. ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft. Advisor](#microsoftadvisor)
> - [Microsoft. AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft. AnalysisServices](#microsoftanalysisservices)
> - [Microsoft. ApiManagement](#microsoftapimanagement)
> - [Microsoft. AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. AppPlatform](#microsoftappplatform)
> - [Microsoft. AppService](#microsoftappservice)
> - [Microsoft. zaświadczanie](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft. Automation](#microsoftautomation)
> - [Microsoft. Automatyczna synchronizacja](#microsoftavs)
> - [Microsoft. usługi azureactivedirectory](#microsoftazureactivedirectory)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft. AzureStack](#microsoftazurestack)
> - [Microsoft. AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft. rozliczenia](#microsoftbilling)
> - [Microsoft. BingMaps](#microsoftbingmaps)
> - [Microsoft. BizTalkServices](#microsoftbiztalkservices)
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
> - [Microsoft. ClassicSubscription](#microsoftclassicsubscription)
> - [Microsoft. CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft. Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft. zużycie](#microsoftconsumption)
> - [Microsoft. ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft. ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft. ContainerService](#microsoftcontainerservice)
> - [Microsoft. ContentModerator](#microsoftcontentmoderator)
> - [Microsoft. CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft. CostManagement](#microsoftcostmanagement)
> - [Microsoft. CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft. CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft. CustomProviders](#microsoftcustomproviders)
> - [Microsoft. DataBox](#microsoftdatabox)
> - [Microsoft. DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft. datacegły](#microsoftdatabricks)
> - [Microsoft. datacatalog](#microsoftdatacatalog)
> - [Microsoft. dataconnect](#microsoftdataconnect)
> - [Microsoft. dataexchange](#microsoftdataexchange)
> - [Microsoft. DataFactory](#microsoftdatafactory)
> - [Microsoft. datalake](#microsoftdatalake)
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
> - [Microsoft. DevOps](#microsoftdevops)
> - [Microsoft. DevSpaces](#microsoftdevspaces)
> - [Microsoft. wspólny](#microsoftdevtestlab)
> - [Microsoft. DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft. DomainRegistration](#microsoftdomainregistration)
> - [Microsoft. EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft. EventGrid](#microsofteventgrid)
> - [Microsoft. EventHub](#microsofteventhub)
> - [Microsoft. eksperymentowanie](#microsoftexperimentation)
> - [Microsoft. Falcon](#microsoftfalcon)
> - [Microsoft. Features](#microsoftfeatures)
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
> - [Microsoft. Insights](#microsoftinsights)
> - [Microsoft. IoTCentral](#microsoftiotcentral)
> - [Microsoft. IoTSpaces](#microsoftiotspaces)
> - [Microsoft. kluczy — magazyn](#microsoftkeyvault)
> - [Microsoft. Kubernetes](#microsoftkubernetes)
> - [Microsoft. KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft. LabServices](#microsoftlabservices)
> - [Microsoft. LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft. LocationServices](#microsoftlocationservices)
> - [Microsoft. Logic](#microsoftlogic)
> - [Microsoft. MachineLearning](#microsoftmachinelearning)
> - [Microsoft. MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft. MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft. MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
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
> - [Microsoft. kontrolą](#microsoftpurview)
> - [Microsoft. ProviderHub](#microsoftproviderhub)
> - [Microsoft. Quantum](#microsoftquantum)
> - [Microsoft. RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft. RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft. Relay](#microsoftrelay)
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
> - [Microsoft. ResourceHealth](#microsoftresourcehealth)
> - [Microsoft. resources](#microsoftresources)
> - [Microsoft. SaaS](#microsoftsaas)
> - [Microsoft. Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft. SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft. SerialConsole](#microsoftserialconsole)
> - [Microsoft. ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft. servicefabric](#microsoftservicefabric)
> - [Microsoft. ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft. Services](#microsoftservices)
> - [Microsoft. SignalRService](#microsoftsignalrservice)
> - [Microsoft. SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft. Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft. SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft. StorageCache](#microsoftstoragecache)
> - [Microsoft. StorageSync](#microsoftstoragesync)
> - [Microsoft. StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft. StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft. StorSimple](#microsoftstorsimple)
> - [Microsoft. StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft. StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft. Subscription](#microsoftsubscription)
> - [Microsoft. Support](#microsoftsupport)
> - [Microsoft. Synapse](#microsoftsynapse)
> - [Microsoft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft. token](#microsofttoken)
> - [Microsoft. VirtualMachineImages](#microsoftvirtualmachineimages)
> - [Microsoft. VisualStudio](#microsoftvisualstudio)
> - [Microsoft. VMware](#microsoftvmware)
> - [Microsoft. VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft. VnfManager](#microsoftvnfmanager)
> - [Microsoft. VSOnline](#microsoftvsonline)
> - [Microsoft. Web](#microsoftweb)
> - [Microsoft. WindowsESU](#microsoftwindowsesu)
> - [Microsoft. WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft. Monitor obciążenia został](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft. AAD

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | domainservices | Nie | 


## <a name="microsoftaadiam"></a>Microsoft. aadiam

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | diagnosticsettings | Nie |
> | diagnosticsettingscategories | Nie |
> | privatelinkforazuread | Nie |
> | dzierżaw |  Nie |

## <a name="microsoftaddons"></a>programu. Addons

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | supportproviders | Nie |

## <a name="microsoftadhybridhealthservice"></a>Microsoft. ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
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
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | komputerów | Nie | 
> | generaterecommendations | Nie |
> | metadane | Nie |
> | zalecenia | Nie |
> | pominięć | Nie | 

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | actionrules | Nie | 
> | alerts | Nie | 
> | alertslist | Nie | 
> | alertsmetadata | Nie | 
> | alertssummary | Nie | 
> | alertssummarylist | Nie | 
> | smartdetectoralertrules | Nie | 
> | smartgroups | Nie | 

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | serwerem | Nie |

## <a name="microsoftapimanagement"></a>Microsoft. ApiManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | reportfeedback | Nie |
> | usługa |  Tak (przy użyciu szablonu) <br/><br/> [Przenieś API Management między regionami](../../api-management/api-management-howto-migrate.md). | 

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | configurationstores | Nie | 
> | configurationstores / eventgridfilters | Nie |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | Spring | Nie | 

## <a name="microsoftappservice"></a>Microsoft. AppService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | apiapps | Tak (przy użyciu szablonu)<br/><br/> [Przenoszenie aplikacji App Service do innego regionu](../../app-service/manage-move-across-regions.md) | 
> | appidentities | Nie | 
> | bram | Nie | 

## <a name="microsoftattestation"></a>Microsoft. zaświadczanie

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | attestationproviders | Nie | 

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | classicadministrators | Nie | 
> | aliasy | Nie | 
> | denyassignments | Nie | 
> | elevateaccess | Nie | 
> | findorphanroleassignments | Nie | 
> | Zamki | Nie | 
> | uprawnienia | Nie | 
> | policyassignments | Nie | 
> | policydefinitions | Nie | 
> | policysetdefinitions | Nie | 
> | privatelinkassociations | Nie | 
> | resourcemanagementprivatelinks | Nie | 
> | RoleAssignments | Nie | 
> | roleassignmentsusagemetrics | Nie | 
> | roledefinitions | Nie | 

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | automationaccounts | Tak (przy użyciu szablonu) <br/><br/> [Korzystanie z replikacji geograficznej](../../automation/automation-managing-data.md#geo-replication-in-azure-automation) |  
> | automationaccounts/konfiguracje | Nie | 
> | automationaccounts/elementy Runbook | Nie | 

## <a name="microsoftavs"></a>Microsoft. Automatyczna synchronizacja

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | Subskrypcja |
> | ------------- | ----------- | 
> | privateclouds | Nie | 


## <a name="microsoftazureactivedirectory"></a>Microsoft. usługi azureactivedirectory

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | b2cdirectories | Nie | 
> | b2ctenants | Nie | 

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | Kontrolery datacontrollers | Nie | 
> | hybriddatamanagers | Nie | 
> | postgresinstances | Nie | 
> | wystąpienia sqlinstances | Nie | 
> | sqlmanagedinstances | Nie |
> | sqlserverinstances | Nie | 
> | sqlserverregistrations | Nie |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | cloudmanifestfiles | Nie |
> | rejestracje | Nie | 

## <a name="microsoftazurestackhci"></a>Microsoft. AzureStackHCI

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | oparty | Nie | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | batchaccounts |  Kont usługi Batch nie można przenieść bezpośrednio z jednego regionu do innego, ale można użyć szablonu do wyeksportowania szablonu, zmodyfikowania go i wdrożenia szablonu w nowym regionie. <br/><br/> Dowiedz się więcej o [przenoszeniu konta w usłudze Batch w różnych regionach](../../batch/best-practices.md#moving-batch-accounts-across-regions) |

## <a name="microsoftbilling"></a>Microsoft. rozliczenia

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | billingaccounts | Nie | 
> | billingperiods | Nie | 
> | billingpermissions | Nie | 
> | billingproperty | Nie | 
> | billingroleassignments | Nie | 
> | billingroledefinitions | Nie | 
> | działów | Nie | 
> | enrollmentaccounts | Nie | 
> | faktur | Nie | 
> | sunięcia | Nie | 

## <a name="microsoftbingmaps"></a>Microsoft. BingMaps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | mapapis | Nie | 

## <a name="microsoftbiztalkservices"></a>Microsoft. BizTalkServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | biztalk | Nie | 

## <a name="microsoftblockchain"></a>Microsoft. łańcucha bloków

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | blockchainmembers | Nie <br/><br/> Sieć łańcucha bloków nie może mieć węzłów w różnych regionach. 
> | cordamembers | Nie |
> | obserwatorów | Nie | 

## <a name="microsoftblockchaintokens"></a>Microsoft. BlockchainTokens

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | tokenservices | Nie |


## <a name="microsoftblueprint"></a>Microsoft. plan

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | blueprintassignments | Nie | 
> | plany | Nie |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | botservices | Nie | 

## <a name="microsoftcache"></a>Microsoft. cache

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | Redis | Nie | 
> | redisenterprise | Nie | 

## <a name="microsoftcapacity"></a>Microsoft. Pojemność

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | appliedreservations | Nie | 
> | calculateexchange | Nie | 
> | calculateprice | Nie | 
> | calculatepurchaseprice | Nie | 
> | wykazy | Nie | 
> | commercialreservationorders | Nie | 
> | zamian | Nie |
> | reservationorders | Nie | 
> | dokonując | Nie | 
> | zasoby | Nie | 
> | validatereservationorder | Nie | 

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpolicies | Nie |
> | edgenodes | Nie
> | Profil | Nie | 
> | Profile/punkty końcowe | Nie | 

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | certificateorders | Nie | 


## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | możliwości | Nie | 
> | domainnames | Tak | Nie |
> | quotas | Nie | 
> | resourcetypes | Nie |
> | validatesubscriptionmoveavailability | Nie | 
> | virtualmachines | Nie 

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft. ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | classicinfrastructureresources | Nie | 

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | możliwości | Nie | 
> | expressroutecrossconnections | Nie | 
> | expressroutecrossconnections/Komunikacja równorzędna | Nie | 
> | gatewaysupporteddevices | Nie | 
> | networksecuritygroups | Nie |
> | quotas | Nie |
> | reservedips | Nie | 
> | virtualnetworks | Nie | 

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | dysku | Nie | 
> | images | Nie | 
> | osimages | Nie | 
> | osplatformimages | Nie | 
> | publicimages | Nie | 
> | quotas | Nie | 
> | storageaccounts | Tak |  
> | vmimages | Nie |

## <a name="microsoftclassicsubscription"></a>Microsoft. ClassicSubscription

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | operacje | Nie | 

## <a name="microsoftcognitiveservices"></a>Microsoft. CognitiveServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | Konta | Nie | 
> | Cognitive Search | Obsługiwane z ręcznymi krokami.<br/><br/> Dowiedz się więcej o [przenoszeniu usługi Wyszukiwanie poznawcze platformy Azure do innego regionu](../../search/search-howto-move-across-regions.md)

## <a name="microsoftcommerce"></a>Microsoft. Commerce

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | ratecard | Nie | 
> | usageaggregates | Nie | 

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | availabilitysets | Tak <br/><br/> Przenoszenie zestawów dostępności przy użyciu [usługi Azure Resource przeprowadzki](../../resource-mover/tutorial-move-region-virtual-machines.md) . | 
> | diskaccesses | Nie |
> | diskencryptionsets | Nie | 
> | dysku | Tak <br/><br/> Przenoszenie maszyn wirtualnych platformy Azure i powiązanych dysków za pomocą [usługi Azure Resource Recovery](../../resource-mover/tutorial-move-region-virtual-machines.md) . | 
> | znajduj | Nie | 
> | Galerie/obrazy | Nie | 
> | Galerie/obrazy/wersje | Nie | 
> | hostgroups | Nie | 
> | hostgroups/hosty | Nie | 
> | images | Nie | 
> | proximityplacementgroups | Nie | 
> | restorepointcollections | Nie | 
> | sharedvmimages | Nie | 
> | sharedvmimages/wersje | Nie | 
> | Migawki | Nie | 
> | sshpublickeys | Nie |
> | virtualmachines | Tak <br/><br/> Przenoszenie maszyn wirtualnych platformy Azure przy użyciu [usługi Azure Resource Recovery](../../resource-mover/tutorial-move-region-virtual-machines.md) . | 
> | virtualmachines/rozszerzenia | Nie | 
> | virtualmachinescalesets | Nie | 

## <a name="microsoftconsumption"></a>Microsoft. zużycie

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | aggregatedcost | Nie | 
> | równoważy | Nie | 
> | budżetów | Nie | 
> | odsetk | Nie | 
> | costtags | Nie | 
> | środki | Nie | 
> | zdarzenia | Nie | 
> | prognozy | Nie | 
> | ilości | Nie | 
> | platform handlowych | Nie | 
> | pricesheets | Nie | 
> | wyrobów | Nie | 
> | reservationdetails | Nie | 
> | reservationrecommendationdetails | Nie | 
> | reservationrecommendations | Nie | 
> | reservationsummaries | Nie | 
> | reservationtransactions | Nie | 
> | tags | Nie | 
> | dzierżaw | Nie | 
> | odsetk | Nie | 
> | usagedetails | Nie | 


## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | containergroups | Nie | 
> | serviceassociationlinks | Nie |


## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | wołuje | Nie |  
> | rejestry/agentpools | Nie | 
> | rejestry/BuildTasks | Nie |  
> | rejestry/replikacje | Nie | 
> | rejestry/zadania | Nie |  
> | rejestry/elementy webhook | Nie | 

## <a name="microsoftcontainerservice"></a>Microsoft. ContainerService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | containerservices | Nie |
> | managedclusters | Nie | 
> | openshiftmanagedclusters | Nie | 

## <a name="microsoftcontentmoderator"></a>Microsoft. ContentModerator

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | aplikacje | Nie | 

## <a name="microsoftcortanaanalytics"></a>Microsoft. CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | Konta | Nie | 

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | alerts | Nie | 
> | billingaccounts | Nie | 
> | budżetów | Nie | 
> | cloudconnectors | Nie | 
> | konta integracji | Nie | 
> | działów | Nie | 
> | Wymiary | Nie | 
> | enrollmentaccounts | Nie | 
> | Eksportowanie | Nie | 
> | externalbillingaccounts | Nie | 
> | forecast | Nie | 
> | query | Nie | 
> | zarejestruj | Nie | 
> | reportconfigs | Nie | 
> | raporty | Nie | 
> | ustawienia | Nie | 
> | showbackrules | Nie | 
> | widoki | Nie | 

## <a name="microsoftcustomerinsights"></a>Microsoft. CustomerInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | koncentratory, | Nie |  

## <a name="microsoftcustomerlockbox"></a>Microsoft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | żądań | Nie | 

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | związku | Nie |
> | resourceproviders | Nie | 

## <a name="microsoftdatabox"></a>Microsoft. DataBox

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | zadania | Nie | 

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | availableskus | Nie |
> | databoxedgedevices | Nie | 

## <a name="microsoftdatabricks"></a>Microsoft. datacegły

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | obszary robocze | Nie | 

## <a name="microsoftdatacatalog"></a>Microsoft. datacatalog

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | wykazy | Nie | 
> | wykazy | Nie | 

## <a name="microsoftdataconnect"></a>Microsoft. dataconnect

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | connectionmanagers | Nie | 

## <a name="microsoftdataexchange"></a>Microsoft. dataexchange

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | pakiety | Nie | 
> | pakiety | Nie | 

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | fabryki datafactors | Nie | 
> | fabryki | Nie |  

## <a name="microsoftdatalake"></a>Microsoft. datalake

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | datalakeaccounts | Nie | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | Konta | Nie | 

## <a name="microsoftdatalakestore"></a>Microsoft. kontach datalakestore

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | Konta | Nie | 

## <a name="microsoftdatamigration"></a>Migracja Microsoft.

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | services | Nie | 
> | usługi/projekty | Nie | 
> | czasów | Nie | 

## <a name="microsoftdataprotection"></a>Microsoft. dataprotection

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | ---------- |
> | backupvaults | Nie | 

## <a name="microsoftdatashare"></a>Microsoft. dataudział

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | Konta | Nie | 

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | serwerem | Aby przenieść istniejący serwer, można użyć repliki odczytu między regionami. [Dowiedz się więcej](../../postgresql/howto-move-regions-portal.md).<br/><br/> Jeśli usługa jest obsługiwana z magazynem geograficznie nadmiarowym, można użyć przywracania geograficznego do przywrócenia w innych regionach. [Dowiedz się więcej](../../mariadb/concepts-business-continuity.md#recover-from-an-azure-regional-data-center-outage).

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | serwerem | Aby przenieść istniejący serwer, można użyć repliki odczytu między regionami. [Dowiedz się więcej](../../mysql/howto-move-regions-portal.md).

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | servergroups | Nie | 
> | serwerem | Aby przenieść istniejący serwer, można użyć repliki odczytu między regionami. [Dowiedz się więcej](../../postgresql/howto-move-regions-portal.md).
> | serversv2 | Nie | 

## <a name="microsoftdeploymentmanager"></a>Microsoft. Deploymentmanager

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | artifactsources | Nie | 
> | wprowadzanie | Nie |  
> | servicetopologie | Nie | 
> | servicetopologie/usługi | Nie |  
> | servicetopologie/usługi/serviceunits | Nie | 
> | kroki | Nie | 


## <a name="microsoftdesktopvirtualization"></a>Microsoft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | applicationgroups | Nie | 
> | obszary robocze | Nie | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | elasticpools | Nie. Zasób nie jest ujawniony.
> | elasticpools / iothubtenants | Nie. Zasób nie jest ujawniony.
> | iothubs | Tak. [Dowiedz się więcej](../../iot-hub/iot-hub-how-to-clone.md)
> | provisioningservices | Nie | 

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | kontrolery | Nie | 


## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | kontrolery | Nie | 
> | Klaster AKS | Nie<br/><br/> [Dowiedz się więcej](../../dev-spaces/faq.md#can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region) o przenoszeniu do innego regionu.

## <a name="microsoftdevtestlab"></a>Microsoft. wspólny

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | labcenters | Nie | 
> | Labs | Nie | 
> | laboratoria/środowiska | Nie |  
> | laboratoria/servicerunner | Nie | 
> | Labs/virtualmachines | Nie |  
> | Uruchamianie | Nie |  

## <a name="microsoftdigitaltwins"></a>Microsoft. DigitalTwins

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | digitaltwinsinstances | Tak, przez ponowne utworzenie zasobów w nowym regionie. [Dowiedz się więcej](../../digital-twins/how-to-move-regions.md) |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | databaseaccounts | Nie | 
> | databaseaccounts | Nie | 

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | 3.x | Nie | 
> | generatessorequest | Nie | 
> | topleveldomains | Nie | 
> | validatedomainregistrationinformation | Nie |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | services | Nie |  

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | 3.x | Nie | 
> | eventsubscriptions | Nie |
> | extensiontopics | Nie | 
> | partnernamespaces | Nie | 
> | partnerregistrations | Nie | 
> | partnertopics | Nie | 
> | systemtopics | Nie | 
> | opisano | Nie | 
> | topictypes | Nie | 

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | oparty | Nie |  
> | przestrzeni | Tak (z szablonem)<br/><br/> [Przenoszenie przestrzeni nazw centrum zdarzeń do innego regionu](../../event-hubs/move-across-regions.md) | 
> | sku | Nie |  

## <a name="microsoftexperimentation"></a>Microsoft. eksperymentowanie

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | experimentworkspaces | Nie | 

## <a name="microsoftfalcon"></a>Microsoft. Falcon

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | przestrzeni | Nie | 

## <a name="microsoftfeatures"></a>Microsoft. Features

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | featureproviders | Nie | 
> | funkcje | Nie | 
> | dostawców | Nie | 
> | subscriptionfeatureregistrations | Nie | 

## <a name="microsoftgenomics"></a>Microsoft. genomika

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | Konta | Nie | 

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | automanagedaccounts | Nie | 
> | automanagedvmconfigurationprofiles | Nie | 
> | guestconfigurationassignments | Nie | 
> | programy | Nie | 
> | softwareupdateprofile | Nie | 
> | softwareupdates | Nie | 

## <a name="microsofthanaonazure"></a>Microsoft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | hanainstances | Nie | 
> | sapmonitors | Nie |  

## <a name="microsofthardwaresecuritymodules"></a>Microsoft. HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | modułów dedicatedhsms | Nie | 


## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | oparty | Nie | 

## <a name="microsofthealthcareapis"></a>Microsoft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | services | Nie |  

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | Win64 | Nie | 
> | Maszyny/rozszerzenia | Nie |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | datamanagers |  Nie | 

## <a name="microsofthybridnetwork"></a>Microsoft. HybridNetwork

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | devices | Nie | 
> | vnfs | Nie | 

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | komponentów | Nie | 
> | networkscopes | Nie | 

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | zadania |  Nie | 

## <a name="microsoftinsights"></a>Microsoft. Insights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | Konta | Nie. [Dowiedz się więcej](../../azure-monitor/faq.md#how-do-i-move-an-application-insights-resource-to-a-new-region).
> | actiongroups |  Nie | 
> | activitylogalerts | Nie | 
> | alertrules |  Nie | 
> | autoscalesettings |  Nie | 
> | punkt odniesienia | Nie |
> | komponentów |  Nie |  
> | datacollectionrules | Nie | 
> | diagnosticsettings | Nie | 
> | diagnosticsettingscategories | Nie | 
> | eventcategories | Nie | 
> | Typ zdarzenia | Nie | 
> | extendeddiagnosticsettings | Nie | |
> | guestdiagnosticsettings | Nie | 
> | listmigrationdate | Nie | 
> | logdefinitions | Nie | 
> | logprofiles | Nie | 
> | dzienniki | Nie | Nie |
> | metricalerts | Nie | 
> | metricbaselines | Nie | 
> | metricbatch | Nie | 
> | metricdefinitions | Nie | 
> | metricnamespaces | Nie | 
> | metrics | Nie | 
> | migratealertrules | Nie |
> | migratetonewpricingmodel | Nie | 
> | Moje skoroszyty | Nie |
> | notificationgroups | Nie | 
> | privatelinkscopes | Nie |
> | rollbacktolegacypricingmodel | Nie |
> | scheduledqueryrules |  Nie | 
> | topology | Nie |
> | transakcje | Nie |
> | vminsightsonboardingstatuses | Nie |
> | testów internetowych |  Nie | 
> | webtests/gettestresultfile | Nie |
> | skoroszyty |  Nie |  
> | workbooktemplates | Nie |


## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | apptemplates | Nie | 
> | iotapps | Nie | 



## <a name="microsoftiothub"></a>Microsoft.IoTHub

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> |  iothub |  Tak (Klonuj Hub) <br/><br/> [Klonowanie Centrum IoT Hub do innego regionu](../../iot-hub/iot-hub-how-to-clone.md)

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu |
> | ------------- | ----------- | 
> | ziół | Nie | 

## <a name="microsoftkeyvault"></a>Microsoft. kluczy — magazyn

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | deletedvaults | Nie |
> | hsmpools | Nie | 
> | managedhsms | Nie |
> | magazynów |  Nie | 

## <a name="microsoftkubernetes"></a>Microsoft. Kubernetes

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | connectedclusters | Nie | 
> | registeredsubscriptions | Nie | 

## <a name="microsoftkubernetesconfiguration"></a>Microsoft. KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | sourcecontrolconfigurations | Nie | 

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | oparty |  Nie |  

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | labaccounts | Nie | 
> | users | Nie | 

## <a name="microsoftlocationbasedservices"></a>Microsoft. LocationBasedServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | Konta | Nie | 

## <a name="microsoftlocationservices"></a>Microsoft. LocationServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | Konta | Nie, jest to usługa globalna.

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | hostingenvironments | Nie | 
> | integrationaccounts |  Nie |  
> | integrationserviceenvironments | Nie | 
> | integrationserviceenvironments/managedapis | Nie |
> | isolatedenvironments | Nie | 
> | przebieg |  Nie |  

## <a name="microsoftmachinelearning"></a>Microsoft. MachineLearning

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | commitmentplans |  Nie | 
> | WebServices |  Nie | 
> | obszary robocze |  Nie | 

## <a name="microsoftmachinelearningcompute"></a>Microsoft. MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | operationalizationclusters |  Nie | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft. MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | Konta | Nie | 
> | teamaccounts | Nie | 


## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft. MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | Konta | Nie | 


## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | obszary robocze | Nie | 

## <a name="microsoftmaintenance"></a>Microsoft. Maintenance

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | configurationassignments | Tak. [Dowiedz się więcej](../../virtual-machines/move-region-maintenance-configuration.md) | 
> | maintenanceconfigurations | Tak. [Dowiedz się więcej](../../virtual-machines/move-region-maintenance-configuration-resources.md) |
> | aktualizacje | Nie | 

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | osob | Nie | 
> | resourceidentity | Nie | 

## <a name="microsoftmanagednetwork"></a>Microsoft. ManagedNetwork

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | managednetworks | Nie | 
> | managednetworks / managednetworkgroups | Nie |
> | managednetworks / managednetworkpeeringpolicies | Nie | 
> | powiadomienie | Nie | 

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | marketplaceregistrationdefinitions | Nie | 
> | registrationassignments | Nie |
> | registrationdefinitions | Nie | 

## <a name="microsoftmanagement"></a>Microsoft. Management

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | getentities | Nie | 
> | managementgroups | Nie | 
> | managementgroups/ustawienia | Nie | 
> | zasoby | Nie | 
> | starttenantbackfill | Nie | 
> | tenantbackfillstatus | Nie | 

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | Konta |  Nie, Azure Maps jest usługą geograficznym. 
> | konta/privateatlases | Nie

## <a name="microsoftmarketplace"></a>Microsoft. Marketplace

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | otrzymane | Nie | 
> | offertypes | Nie | 
> | privategalleryitems | Nie | 
> | privatestoreclient | Nie | 
> | privatestores | Nie | 
> | wyrobów | Nie | 
> | wydawcy | Nie | 
> | zarejestruj | Nie | 

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | classicdevservices | Nie | 

## <a name="microsoftmarketplaceordering"></a>Microsoft. MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | kontrakt | Nie | 
> | offertypes | Nie | 

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | MediaServices |  Nie | 
> | MediaServices/liveevents |  Nie | 
> | MediaServices/streamingendpoints |  Nie | 

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | appclusters | Nie | 

## <a name="microsoftmigrate"></a>Microsoft. Migrowanie

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | assessmentprojects | Nie | 
> | migrateprojects | Nie | 
> | movecollections | Nie
> | projektami | Nie | 

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | ---------- |
> | holographicsbroadcastaccounts | Nie | 
> | objectunderstandingaccounts | Nie | 
> | remoterenderingaccounts | Nie | 
> | spatialanchorsaccounts | Nie | 

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | netappaccounts | Nie | 
> | netappaccounts / capacitypools | Nie | 
> | netappaccounts/capacitypools/woluminy | Nie | 
> | netappaccounts/capacitypools/woluminy/mounttargets | Nie | 
> | netappaccounts/capacitypools/woluminy/migawki | Nie | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | applicationgateways | Nie |
> | applicationgatewaywebapplicationfirewallpolicies | Nie | 
> | applicationsecuritygroups |  Nie |  
> | azurefirewalls |  Nie |  
> | bastionhosts | Nie | 
> | bgpservicecommunities | Nie |
> | Licznik |  Nie | 
> | ddoscustompolicies |  Nie | 
> | ddosprotectionplans | Nie | 
> | dnszones |  Nie | 
> | expressroutecircuits | Nie | 
> | expressroutegateways | Nie | 
> | expressrouteserviceproviders | Nie | 
> | firewallpolicies | Nie |
> | usługi frontdoor | Nie | 
> | ipallocations | Nie |
> | ipgroups | Nie |
> | loadbalancers | Tak <br/><br/> Przenoszenie wewnętrznych i zewnętrznych modułów równoważenia obciążenia przy użyciu [usługi Azure Resource Recovery](../../resource-mover/tutorial-move-region-virtual-machines.md) . |
> | localnetworkgateways |  Nie | 
> | natgateways |  Nie | 
> | networkexperimentprofiles | Nie |
> | networkintentpolicies |  Nie | 
> | networkinterfaces | Tak <br/><br/> Przenoszenie kart sieciowych za pomocą [usługi Azure Resource](../../resource-mover/tutorial-move-region-virtual-machines.md) przenoszącej. | 
> | networkprofiles | Nie | 
> | networksecuritygroups | Tak <br/><br/> Przenoszenie sieciowych grup zabezpieczeń (NGSs) przy użyciu [usługi Azure Resource](../../resource-mover/tutorial-move-region-virtual-machines.md) . | 
> | networkwatchers |  Nie |  
> | networkwatchers / connectionmonitors |  Nie | 
> | networkwatchers / flowlogs |  Nie | 
> | networkwatchers / pingmeshes |  Nie | 
> | p2svpngateways | Nie | 
> | privatednszones |  Nie |  
> | privatednszones / virtualnetworklinks | Nie |> | privatednszonesinternal | Nie |
> | privateendpointredirectmaps | Nie |
> | privateendpoints | Nie | 
> | privatelinkservices | Nie | 
> | adresów publicipaddress | Tak<br/><br/> Przenoszenie publicznych adresów IP za pomocą [usługi Azure Resource Recovery](../../resource-mover/tutorial-move-region-virtual-machines.md) . |
> | publicipprefixes | Nie | 
> | routefilters | Nie | 
> | routetables |  Nie | 
> | securitypartnerproviders | Nie |
> | serviceendpointpolicies |  Nie | 
> | trafficmanagergeographichierarchies | Nie | 
> | trafficmanagerprofiles |  Nie | 
> | trafficmanagerusermetricskeys | Nie |
> | virtualhubs | Nie | 
> | virtualnetworkgateways |  Nie |  
> | virtualnetworks |  Nie | 
> | virtualnetworktaps | Nie | 
> | virtualwans | Nie | 
> | vpngateways (wirtualna sieć WAN) | Nie | 
> | vpnsites (wirtualna sieć WAN) | Nie | 
> | vpnsites (wirtualna sieć WAN) | Nie |


## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | przestrzeni |  Nie | 
> | przestrzenie nazw/notificationhubs |  Nie |  

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | osnamespaces | Nie | 

## <a name="microsoftoffazure"></a>Microsoft. OffAzure

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | hypervsites | Nie | 
> | importsites | Nie | 
> | serversites | Nie | 
> | vmwaresites | Nie | 

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | oparty | Nie | 
> | deletedworkspaces | Nie | 
> | linktargets | Nie | 
> | storageinsightconfigs | Nie |
> | obszary robocze | Nie |



## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | managementassociations | Nie |
> | managementconfigurations |  Nie | 
> | rozwiązania | Nie |
> | widoki |  Nie | 

## <a name="microsoftpeering"></a>Microsoft. Komunikacja równorzędna

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | legacypeerings | Nie | 
> | peerasns | Nie | 
> | peeringlocations | Nie | 
> | komunikacje równorzędne | Nie | 
> | peeringservicecountries | Nie | 
> | peeringservicelocations | Nie | 
> | peeringserviceproviders | Nie | 
> | peeringservices | Nie | 

## <a name="microsoftpolicyinsights"></a>Microsoft. PolicyInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | policyevents | Nie | 
> | policystates | Nie | 
> | policytrackedresources | Nie | 
> | korygowania | Nie | 

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | Konsole programu | Nie |
> | pulpity nawigacyjne | Nie | 
> | usersettings | Nie | 


## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | workspacecollections |  Nie | 

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | produkcyjnych |  Nie | 

## <a name="microsoftpurview"></a>Microsoft. kontrolą

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | Konta | Nie | 

## <a name="microsoftproviderhub"></a>Microsoft. ProviderHub

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | availableaccounts | Nie | 
> | providerregistrations | Nie | 
> | wprowadzanie | Nie | 

## <a name="microsoftquantum"></a>Microsoft. Quantum

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | obszary robocze | Nie | 

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | replicationeligibilityresults | Nie |
> | magazynów | Nie.<br/><br/> Przeniesienie Recovery Services magazynów dla Azure Backup w regionach platformy Azure nie jest obsługiwane.<br/><br/> W Recovery Services magazynach Azure Site Recovery można [wyłączyć i utworzyć ponownie magazyn](../../site-recovery/move-vaults-across-regions.md) w regionie docelowym. | 

## <a name="microsoftredhatopenshift"></a>Microsoft. RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | openshiftclusters | Nie | 

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | przestrzeni |  Nie | 

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | wybiera |  Nie |  
> | resourcechangedetails | Nie | 
> | resourcechanges | Nie | 
> | zasoby | Nie | 
> | resourceshistory | Nie | 
> | subscriptionsstatus | Nie | 

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | childresources | Nie | 
> | emergingissues | Nie | 
> | zdarzenia | Nie | 
> | metadane | Nie | 
> | powiadomienia | Nie | 

## <a name="microsoftresources"></a>Microsoft. resources

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu |
> | ------------- | ----------- |
> | deploymentScripts |  Tak<br/><br/>[Przenoszenie zasobów Microsoft. resources do nowego regionu](microsoft-resources-move-regions.md) |
> | templateSpecs |  Tak<br/><br/>[Przenoszenie zasobów Microsoft. resources do nowego regionu](microsoft-resources-move-regions.md) |  

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | aplikacje |  Nie | 
> | saasresources | Nie | 


## <a name="microsoftsearch"></a>Microsoft. Search

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | resourcehealthmetadata | Nie |
> | searchservices |  Nie | 


## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | adaptivenetworkhardenings | Nie | 
> | advancedthreatprotectionsettings | Nie | 
> | alerts | Nie | 
> | allowedconnections | Nie | 
> | applicationwhitelistings | Nie | 
> | assessmentmetadata | Nie | 
> | oceny | Nie | 
> | autodismissalertsrules | Nie | 
> | automatyzacji | Nie | 
> | autoprovisioningsettings | Nie |
> | complianceresults | Nie | 
> | zachowania zgodności prawnymi | Nie | 
> | datacollectionagents | Nie | 
> | devicesecuritygroups | Nie | 
> | discoveredsecuritysolutions | Nie | 
> | externalsecuritysolutions | Nie | 
> | informationprotectionpolicies | Nie | 
> | iotsecuritysolutions | Nie | 
> | iotsecuritysolutions / analyticsmodels | Nie | 
> | iotsecuritysolutions / analyticsmodels / aggregatedalerts | Nie | 
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | Nie | 
> | jitnetworkaccesspolicies | Nie | 
> | policies | Nie | 
> | cen | Nie | 
> | regulatorycompliancestandards | Nie | 
> | regulatorycompliancestandards / regulatorycompliancecontrols | Nie | 
> | regulatorycompliancestandards / regulatorycompliancecontrols / regulatorycomplianceassessments | Nie | 
> | securitycontacts | Nie | 
> | securitysolutions | Nie | 
> | securitysolutionsreferencedata | Nie | 
> | securitystatuses | Nie | 
> | securitystatusessummaries | Nie | 
> | servervulnerabilityassessments | Nie | 
> | ustawienia | Nie | 
> | podoceny | Nie |
> | zadania | Nie | 
> | replikacji | Nie | 
> | workspacesettings | Nie | 

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | agregacji | Nie | 
> | alertrules | Nie | 
> | alertruletemplates | Nie | 
> | automationrules | Nie |
> | padkach | Nie | 
> | połączenia dataconnecters | Nie | 
> | obiekty | Nie | 
> | entityqueries | Nie |
> | zdarzenia | Nie | 
> | officeconsents | Nie | 
> | ustawienia | Nie | 
> | threatintelligence | Nie | 

## <a name="microsoftserialconsole"></a>Microsoft. SerialConsole

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | consoleservices | Nie | 

## <a name="microsoftservermanagement"></a>Microsoft. ServerManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | bram | Nie | 
> | nich | Nie | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | przestrzeni |  Nie | 
> | premiummessagingregions | Nie | 
> | sku | Nie | 

## <a name="microsoftservicefabric"></a>Microsoft. servicefabric

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | aplikacje | Nie | 
> | oparty |  Nie |  
> | containergroups | Nie | 
> | containergroupsets | Nie | 
> | edgeclusters | Nie | 
> | managedclusters | Nie |
> | sieci | Nie | 
> | secretstores | Nie | 
> | volumes | Nie | 

## <a name="microsoftservicefabricmesh"></a>Microsoft. ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | aplikacje |  Nie | 
> | containergroups | Nie | 
> | bram |  Nie | 
> | sieci |  Nie | 
> | wpisy tajne |  Nie | 
> | volumes |  Nie |  

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | wprowadzanie | Nie | 

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | SignalR |  Nie |  

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | hybridusebenefits | Nie | 

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | appliancedefinitions | Nie | 
> | gotowania | Nie | 
> | jitrequests | Nie | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | instancepools | Nie | 
> | locations | Nie |
> | managedinstances | Tak <br/><br/> [Dowiedz się więcej](../../azure-sql/database/move-resources-across-regions.md) o przenoszeniu wystąpień zarządzanych między regionami. | 
> | ManagedInstances/bazy danych | Tak | 
> | serwerem | Tak | 
> | Serwery/bazy danych | Tak <br/><br/> [Dowiedz się więcej](../../azure-sql/database/move-resources-across-regions.md) o przenoszeniu baz danych między regionami.<br/><br/> [Dowiedz się więcej](../../resource-mover/tutorial-move-region-sql.md) o używaniu funkcji przenoszenia zasobów platformy Azure do przenoszenia baz danych SQL Azure.  | 
> | serwery/elasticpools | Tak <br/><br/> [Dowiedz się więcej](../../azure-sql/database/move-resources-across-regions.md) o przenoszeniu pul elastycznych między regionami.<br/><br/> [Dowiedz się więcej](../../resource-mover/tutorial-move-region-sql.md) o korzystaniu z funkcji przenoszenia zasobów platformy Azure do przenoszenia pul elastycznych usługi Azure SQL.  | 
> | virtualclusters | Tak | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | sqlvirtualmachinegroups |  Nie |  
> | sqlvirtualmachines |  Nie |  


## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | storageaccounts | Tak<br/><br/> [Przenoszenie konta usługi Azure Storage do innego regionu](../../storage/common/storage-account-move.md) | 

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | pamięci podręcznych | Nie | 

## <a name="microsoftstoragesync"></a>Microsoft. StorageSync

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | storagesyncservices |  Nie | 

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | storagesyncservices | Nie | 

## <a name="microsoftstoragesyncint"></a>Microsoft. StorageSyncInt

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | storagesyncservices | Nie | 

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | skonfigurowany | Nie | 

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | oparty | Nie |
> | streamingjobs |  Nie |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft. StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | wiejski | Nie | 
> | Liczba | Nie | 

## <a name="microsoftsubscription"></a>Microsoft. Subscription

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | opłaty | Nie | 

## <a name="microsoftsupport"></a>Microsoft. Support

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | services | Nie | 
> | supporttickets | Nie | 

## <a name="microsoftsynapse"></a>Microsoft. Synapse

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | obszary robocze | Nie | 
> | obszary robocze/bigdatapools | Nie | 
> | obszary robocze/pule | Nie | 


## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | wiejski |  Nie | 
> | środowiska/źródła zdarzeń |  Nie |  
> | środowiska/referencedatasets |  Nie | 

## <a name="microsofttoken"></a>Microsoft. token

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | przechowywać | Nie | 

## <a name="microsoftvirtualmachineimages"></a>Microsoft. VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | imagetemplates | Nie | 

## <a name="microsoftvisualstudio"></a>Microsoft. VisualStudio

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | account |  Nie | 
> | konto/rozszerzenie |  Nie | 
> | konto/projekt |  Nie | 

## <a name="microsoftvmware"></a>Microsoft. VMware

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | arczones | Nie | 
> | resourcepools | Nie | 
> | serwery vCenter | Nie | 
> | virtualmachines | Nie | 
> | virtualmachinetemplates | Nie | 
> | virtualnetworks | Nie | 

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | dedicatedcloudnodes | Nie | 
> | dedicatedcloudservices | Nie | 
> | virtualmachines | Nie | 

## <a name="microsoftvnfmanager"></a>Microsoft. VnfManager

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | devices | Nie | 
> | vnfs | Nie | 

## <a name="microsoftvsonline"></a>Microsoft. VSOnline

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | Konta | Nie | 
> | pakiety | Nie | 
> | registeredsubscriptions | Nie |


## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | availablestacks | Nie | 
> | billingmeters | Nie | 
> | certyfikaty | Nie | 
> | connectiongateways |  Nie |  
> | Licznik |  Nie |  
> | customapis |  Nie | 
> | deletedsites | Nie | 
> | deploymentlocations | Nie | 
> | Georegiony | Nie | 
> | hostingenvironments | Nie | 
> | kubeenvironments | Nie | 
> | publishingusers | Nie |
> | zalecenia | Nie | 
> | resourcehealthmetadata | Nie | 
> | Runtime | Nie | 
> | dopuszczalna | Nie |  
> | dopuszczalna/eventgridfilters | N
> | lokacje |  Nie | 
> | Lokacje/premieraddons |  Nie |  
> | Lokacje/miejsca |  Nie |  
> | sourcecontrols | Nie |
> | staticsites | Nie | 

## <a name="microsoftwindowsesu"></a>Microsoft. WindowsESU

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | multipleactivationkeys | Nie |

## <a name="microsoftwindowsiot"></a>Microsoft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | deviceservices | Nie | 

## <a name="microsoftworkloadbuilder"></a>Microsoft. WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | obciążeń | Nie | 

## <a name="microsoftworkloadmonitor"></a>Microsoft. Monitor obciążenia został

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | komponentów | Nie |
> | componentssummary | Nie | 
> | monitorinstances | Nie | 
> | monitorinstancessummary | Nie | 
> | monitora | Nie | 
## <a name="third-party-services"></a>Usługi innych firm

Usługi innych firm obecnie nie obsługują operacji przenoszenia.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej](../../resource-mover/overview.md) o usłudze przenoszenia zasobów.

