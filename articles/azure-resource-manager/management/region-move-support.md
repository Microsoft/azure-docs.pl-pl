---
title: Obsługa przemieszczania zasobów platformy Azure między regionami
description: Wyświetla listę typów zasobów platformy Azure, które mogą być przenoszone w regionach platformy Azure
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 08/25/2020
ms.author: raynew
ms.openlocfilehash: dc931b910981578a3257c9131bea93cd836d1def
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2020
ms.locfileid: "91945167"
---
# <a name="support-for-moving-azure-resources-across-regions"></a>Obsługa przemieszczania zasobów platformy Azure między regionami

Ten artykuł potwierdza, czy typ zasobu platformy Azure jest obsługiwany w przypadku przechodzenia do innego regionu platformy Azure. 

Przejdź do przestrzeni nazw dostawcy zasobów:
> [!div class="op_single_selector"]
> - [Microsoft. AAD](#microsoftaad)
> - [Microsoft. aadiam](#microsoftaadiam)
> - [Microsoft. AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft. AnalysisServices](#microsoftanalysisservices)
> - [Microsoft. ApiManagement](#microsoftapimanagement)
> - [Microsoft. AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft. Automation](#microsoftautomation)
> - [Microsoft. usługi azureactivedirectory](#microsoftazureactivedirectory)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft. AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft. BingMaps](#microsoftbingmaps)
> - [Microsoft. BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft. łańcucha bloków](#microsoftblockchain)
> - [Microsoft. plan](#microsoftblueprint)
> - [Microsoft. BotService](#microsoftbotservice)
> - [Microsoft. cache](#microsoftcache)
> - [Microsoft. CDN](#microsoftcdn)
> - [Microsoft. CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft. ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft. ClassicStorage](#microsoftclassicstorage)
> - [Microsoft. CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft. Container](#microsoftcontainer)
> - [Microsoft. ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft. ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft. ContainerService](#microsoftcontainerservice)
> - [Microsoft. ContentModerator](#microsoftcontentmoderator)
> - [Microsoft. CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft. CostManagement](#microsoftcostmanagement)
> - [Microsoft. CustomerInsights](#microsoftcustomerinsights)
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
> - [Microsoft. dataudział](#microsoftdatashare)
> - [Microsoft. DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft. DBforMySQL](#microsoftdbformysql)
> - [Microsoft. DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft. Deploymentmanager](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft. DevSpaces](#microsoftdevspaces)
> - [Microsoft. wspólny](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft. DomainRegistration](#microsoftdomainregistration)
> - [Microsoft. EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft. EventGrid](#microsofteventgrid)
> - [Microsoft. EventHub](#microsofteventhub)
> - [Microsoft. genomika](#microsoftgenomics)
> - [Microsoft. HanaOnAzure](#microsofthanaonazure)
> - [Microsoft. HDInsight](#microsofthdinsight)
> - [Microsoft. HealthcareApis](#microsofthealthcareapis)
> - [Microsoft. HybridCompute](#microsofthybridcompute)
> - [Microsoft. HybridData](#microsofthybriddata)
> - [Microsoft. ImportExport](#microsoftimportexport)
> - [Microsoft. Insights](#microsoftinsights)
> - [Microsoft. IoTCentral](#microsoftiotcentral)
> - [Microsoft. IoTSpaces](#microsoftiotspaces)
> - [Microsoft. kluczy — magazyn](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft. LabServices](#microsoftlabservices)
> - [Microsoft. LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft. LocationServices](#microsoftlocationservices)
> - [Microsoft. Logic](#microsoftlogic)
> - [Microsoft. MachineLearning](#microsoftmachinelearning)
> - [Microsoft. MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft. MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft. MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft. MachineLearningOperationalization](#microsoftmachinelearningoperationalization)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft. ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft. Maps](#microsoftmaps)
> - [Microsoft. MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft. Media](#microsoftmedia)
> - [Microsoft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft. Migrowanie](#microsoftmigrate)
> - [Microsoft. NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft. NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft. OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft. OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft. Komunikacja równorzędna](#microsoftpeering)
> - [Microsoft. Portal](#microsoftportal)
> - [Microsoft. PortalSdk](#microsoftportalsdk)
> - [Microsoft. PowerBI](#microsoftpowerbi)
> - [Microsoft. PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft. ProjectOxford](#microsoftprojectoxford)
> - [Microsoft. RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft. Relay](#microsoftrelay)
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
> - [Microsoft. resources](#microsoftresources)
> - [Microsoft. SaaS](#microsoftsaas)
> - [Microsoft. Scheduler](#microsoftscheduler)
> - [Microsoft. Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft. ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft. servicefabric](#microsoftservicefabric)
> - [Microsoft. ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft. SignalRService](#microsoftsignalrservice)
> - [Microsoft. Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft. SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft. SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft. StorageCache](#microsoftstoragecache)
> - [Microsoft. StorageSync](#microsoftstoragesync)
> - [Microsoft. StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft. StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft. StorSimple](#microsoftstorsimple)
> - [Microsoft. StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft. StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft. TerraformOSS](#microsoftterraformoss)
> - [Microsoft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft. token](#microsofttoken)
> - [Microsoft. VirtualMachineImages](#microsoftvirtualmachineimages)
> - [Microsoft. VisualStudio](#microsoftvisualstudio)
> - [Microsoft. VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft. Web](#microsoftweb)
> - [Microsoft. WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. WindowsVirtualDesktop](#microsoftwindowsvirtualdesktop)

## <a name="microsoftaad"></a>Microsoft. AAD

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- | 
> | domainservices | Nie | 
> | domainservices / replicasets | Nie | 

## <a name="microsoftaadiam"></a>Microsoft. aadiam

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | dzierżaw | Nie |

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | actionrules | Nie | 

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | serwerem | Nie |

## <a name="microsoftapimanagement"></a>Microsoft. ApiManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | usługa |  Tak (przy użyciu szablonu) <br/><br/> [Przenieś API Management między regionami](../../api-management/api-management-howto-migrate.md). | 

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | configurationstores | Nie | 

## <a name="microsoftappservice"></a>Microsoft. AppService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | apiapps | Tak (przy użyciu szablonu)<br/><br/> [Przenoszenie aplikacji App Service do innego regionu](../../app-service/manage-move-across-regions.md) | 
> | appidentities | Nie | 
> | bram | Nie | 


## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | policyassignments | Nie |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | automationaccounts | Tak (przy użyciu szablonu) <br/><br/> [Korzystanie z replikacji geograficznej](../../automation/automation-managing-data.md#geo-replication-in-azure-automation) |  
> | automationaccounts/konfiguracje | Nie | 
> | automationaccounts/elementy Runbook | Nie | 



## <a name="microsoftazureactivedirectory"></a>Microsoft. usługi azureactivedirectory

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | b2cdirectories | Nie | 

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | sqlserverregistrations | Nie |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | rejestracje | Nie | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | batchaccounts |  Kont usługi Batch nie można przenieść bezpośrednio z jednego regionu do innego, ale można użyć szablonu do wyeksportowania szablonu, zmodyfikowania go i wdrożenia szablonu w nowym regionie. <br/><br/> Dowiedz się więcej o [przenoszeniu konta w usłudze Batch w różnych regionach](../../batch/best-practices.md#moving-batch-accounts-across-regions) |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | oparty | Nie <br/><br/> Usługa Azure Batch AI zostanie [wycofana](/previous-versions/azure/batch-ai/overview-what-happened-batch-ai).
> | fileservers | Nie | 
> | zadania | Nie | 
> | obszary robocze | Nie | 

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
> | obserwatorów | Nie | 

## <a name="microsoftblueprint"></a>Microsoft. plan

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | blueprintassignments | Nie | 

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


## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpolicies | Nie |
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
> | domainnames | Nie zaplanowano żadnych zadań dla usług klasycznych.
> | virtualmachines | Nie | 



## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | networksecuritygroups | Nie zaplanowano żadnych zadań dla usług klasycznych.
> | reservedips | Nie | 
> | virtualnetworks | Nie | 

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | storageaccounts | Tak |  


## <a name="microsoftcognitiveservices"></a>Microsoft. CognitiveServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | Konta | Nie | 
> | Cognitive Search | Obsługiwane z ręcznymi krokami.<br/><br/> Dowiedz się więcej o [przenoszeniu usługi Wyszukiwanie poznawcze platformy Azure do innego regionu](../../search/search-howto-move-across-regions.md)

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | availabilitysets | Tak <br/><br/> Przenoszenie zestawów dostępności przy użyciu [usługi Azure Resource przeprowadzki](../../resource-mover/tutorial-move-region-virtual-machines.md) . | 
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
> | virtualmachines | Tak <br/><br/> Przenoszenie maszyn wirtualnych platformy Azure przy użyciu [usługi Azure Resource Recovery](../../resource-mover/tutorial-move-region-virtual-machines.md) . | 
> | virtualmachines/rozszerzenia | Nie | 
> | virtualmachinescalesets | Nie | 

## <a name="microsoftcontainer"></a>Microsoft. Container

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | containergroups | Nie | 

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | containergroups | Nie | 

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | wołuje | Nie |  
> | rejestry/BuildTasks | Nie |  
> | rejestry/replikacje | Nie | 
> | rejestry/zadania | Nie |  
> | rejestry/elementy webhook | Nie | 

## <a name="microsoftcontainerservice"></a>Microsoft. ContainerService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | containerservices | Nie.<br/><br/> Usługa została [wycofana](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/).
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
> | konta integracji | Nie |  

## <a name="microsoftcustomerinsights"></a>Microsoft. CustomerInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | koncentratory, | Nie |  

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
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

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | elasticpools | Nie. Zasób nie jest ujawniony.
> | elasticpools / iothubtenants | Nie. Zasób nie jest ujawniony.
> | iothubs | Tak. [Dowiedz się więcej](../../iot-hub/iot-hub-how-to-clone.md)
> | provisioningservices | Nie | 

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

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | databaseaccounts | Nie | 

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | 3.x | Nie | 

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
> | opisano | Nie | 

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | oparty | Nie |  
> | przestrzeni | Tak (z szablonem)<br/><br/> [Przenoszenie przestrzeni nazw centrum zdarzeń do innego regionu](../../event-hubs/move-across-regions.md) | 

## <a name="microsoftgenomics"></a>Microsoft. genomika

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | Konta | Nie | 

## <a name="microsofthanaonazure"></a>Microsoft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | hanainstances | Nie | 
> | sapmonitors | Nie |  

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

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | datamanagers |  Nie | 

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
> | komponentów |  Nie |  
> | guestdiagnosticsettings | Nie | 
> | metricalerts | Nie | 
> | notificationgroups | Nie | 
> | notificationrules | Nie | 
> | scheduledqueryrules |  Nie | 
> | testów internetowych |  Nie | 
> | skoroszyty |  Nie |  


## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | checknameavailability |  Nie.<br/><br/> IoT Central współpracuje z lokalizacje geograficzneą, a nie regionami.
> | ziół | Nie

## <a name="microsoftiothub"></a>Microsoft.IoTHub

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> |  iothub |  Tak (Klonuj Hub) <br/><br/> [Klonowanie Centrum IoT Hub do innego regionu](../../iot-hub/iot-hub-how-to-clone.md)

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | checknameavailability |  Nie |  
> | ziół |  Nie | 

## <a name="microsoftkeyvault"></a>Microsoft. kluczy — magazyn

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | hsmpools | Nie | 
> | magazynów |  Nie | 


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
> | konta/obszary robocze | Nie | 
> | konta/obszary robocze/projekty | Nie | 
> | teamaccounts | Nie | 
> | teamaccounts/obszary robocze | Nie | 
> | teamaccounts/obszary robocze/projekty | Nie | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft. MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | Konta | Nie | 

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft. MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | hostingaccounts | Nie | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | obszary robocze | Nie | 

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | resourceidentity | Nie | 

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | Konta |  Nie, Azure Maps jest usługą geograficznym. 

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | classicdevservices | Nie zaplanowano żadnych zadań dla usług klasycznych 

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
> | projektami | Nie | 

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
> | Licznik |  Nie | 
> | ddoscustompolicies |  Nie | 
> | ddosprotectionplans | Nie | 
> | dnszones |  Nie | 
> | expressroutecircuits | Nie | 
> | expressroutecrossconnections | Nie | 
> | expressroutegateways | Nie | 
> | expressrouteports | Nie | 
> | usługi frontdoor | Nie | 
> | frontdoorwebapplicationfirewallpolicies | Nie | 
> | loadbalancers | Tak <br/><br/> Przenoszenie wewnętrznych i zewnętrznych modułów równoważenia obciążenia przy użyciu [usługi Azure Resource Recovery](../../resource-mover/tutorial-move-region-virtual-machines.md) . |
> | localnetworkgateways |  Nie | 
> | natgateways |  Nie | 
> | networkintentpolicies |  Nie | 
> | networkinterfaces | Tak <br/><br/> Przenoszenie kart sieciowych za pomocą [usługi Azure Resource](../../resource-mover/tutorial-move-region-virtual-machines.md) przenoszącej. | 
> | networkprofiles | Nie | 
> | networksecuritygroups | Tak <br/><br/> Przenoszenie sieciowych grup zabezpieczeń (NGSs) przy użyciu [usługi Azure Resource](../../resource-mover/tutorial-move-region-virtual-machines.md) . | 
> | networkwatchers |  Nie |  
> | networkwatchers / connectionmonitors |  Nie | 
> | networkwatchers/soczewki |  Nie | 
> | networkwatchers / pingmeshes |  Nie | 
> | p2svpngateways | Nie | 
> | privatednszones |  Nie |  
> | privatednszones / virtualnetworklinks |  Nie |  
> | privateendpoints | Nie | 
> | privatelinkservices | Nie | 
> | adresów publicipaddress | Tak<br/><br/> Przenoszenie publicznych adresów IP za pomocą [usługi Azure Resource Recovery](../../resource-mover/tutorial-move-region-virtual-machines.md) . |
> | publicipprefixes | Nie | 
> | routefilters | Nie | 
> | routetables |  Nie | 
> | serviceendpointpolicies |  Nie | 
> | trafficmanagerprofiles |  Nie | 
> | virtualhubs | Nie | 
> | virtualnetworkgateways |  Nie |  
> | virtualnetworks |  Nie | 
> | virtualnetworktaps | Nie | 
> | virtualwans | Nie | 
> | vpngateways (wirtualna sieć WAN) | Nie | 
> | vpnsites (wirtualna sieć WAN) | Nie | 
> | webapplicationfirewallpolicies |  Nie | 


## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | przestrzeni |  Nie | 
> | przestrzenie nazw/notificationhubs |  Nie |  

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | obszary robocze |  Nie | 



## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | managementconfigurations |  Nie | 
> | widoki |  Nie | 

## <a name="microsoftpeering"></a>Microsoft. Komunikacja równorzędna

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | komunikacje równorzędne | Nie | 

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | pulpity nawigacyjne | Nie | 

## <a name="microsoftportalsdk"></a>Microsoft. PortalSdk

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | rootresources | Nie | 

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

## <a name="microsoftprojectoxford"></a>Microsoft. ProjectOxford

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | Konta | Nie | 

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | magazynów | Nie.<br/><br/> Przeniesienie Recovery Services magazynów dla Azure Backup w regionach platformy Azure nie jest obsługiwane.<br/><br/> W Recovery Services magazynach Azure Site Recovery można [wyłączyć i utworzyć ponownie magazyn](../../site-recovery/move-vaults-across-regions.md) w regionie docelowym. | 


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

## <a name="microsoftscheduler"></a>Microsoft. Scheduler

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | flows (przepływy) |  Nie |  
> | jobcollections |  Nie | 

## <a name="microsoftsearch"></a>Microsoft. Search

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | searchservices |  Nie | 


## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | iotsecuritysolutions |  Nie | 
> | playbookconfigurations | Nie | 

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

## <a name="microsoftservicefabric"></a>Microsoft. servicefabric

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | aplikacje | Nie | 
> | oparty |  Nie | 
> | klastry/aplikacje | Nie | 
> | containergroups | Nie | 
> | containergroupsets | Nie | 
> | edgeclusters | Nie | 
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

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | SignalR |  Nie |  

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | appliancedefinitions | Nie | 
> | gotowania | Nie | 
> | applicationdefinitions | Nie | 
> | aplikacje | Nie | 
> | jitrequests | Nie | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | instancepools | Nie | 
> | managedinstances | Tak <br/><br/> [Dowiedz się więcej](/azure/azure-sql/database/move-resources-across-regions) o przenoszeniu wystąpień zarządzanych między regionami. | 
> | ManagedInstances/bazy danych | Tak | 
> | serwerem | Tak | 
> | Serwery/bazy danych | Tak <br/><br/> [Dowiedz się więcej](/azure/azure-sql/database/move-resources-across-regions) o przenoszeniu baz danych między regionami.<br/><br/> [Dowiedz się więcej](../../resource-mover/tutorial-move-region-sql.md) o używaniu funkcji przenoszenia zasobów platformy Azure do przenoszenia baz danych SQL Azure.  | 
> | serwery/elasticpools | Tak <br/><br/> [Dowiedz się więcej](/azure/azure-sql/database/move-resources-across-regions) o przenoszeniu pul elastycznych między regionami.<br/><br/> [Dowiedz się więcej](../../resource-mover/tutorial-move-region-sql.md) o korzystaniu z funkcji przenoszenia zasobów platformy Azure do przenoszenia pul elastycznych usługi Azure SQL.  | 
> | virtualclusters | Tak | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | sqlvirtualmachinegroups |  Nie |  
> | sqlvirtualmachines |  Nie |  

## <a name="microsoftsqlvm"></a>Microsoft. SqlVM

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | dwvm | Nie | 

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
> | streamingjobs |  Nie |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft. StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | wiejski | Nie | 
> | środowiska/źródła zdarzeń | Nie | 
> | Liczba | Nie | 
> | wystąpienia/środowiska | Nie | 
> | wystąpienia/środowiska/obiekty EventSource | Nie | 

## <a name="microsoftterraformoss"></a>Microsoft. TerraformOSS

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | providerregistrations | Nie | 
> | zasoby | Nie | 

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



## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | dedicatedcloudnodes | Nie | 
> | dedicatedcloudservices | Nie | 
> | virtualmachines | Nie | 

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | certyfikaty | Nie | 
> | connectiongateways |  Nie |  
> | Licznik |  Nie |  
> | customapis |  Nie | 
> | hostingenvironments | Nie | 
> | dopuszczalna |  Nie |  
> | lokacje |  Nie | 
> | Lokacje/premieraddons |  Nie |  
> | Lokacje/miejsca |  Nie |  


## <a name="microsoftwindowsiot"></a>Microsoft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | deviceservices | Nie | 

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft. WindowsVirtualDesktop

> [!div class="mx-tableFixed"]
> | Typ zasobu | Przeniesienie regionu | 
> | ------------- | ----------- |
> | applicationgroups | Nie | 
> | hostpools | Nie | 
> | obszary robocze | Nie | 

## <a name="third-party-services"></a>Usługi innych firm

Usługi innych firm obecnie nie obsługują operacji przenoszenia.
