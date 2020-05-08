---
title: Obsługa operacji przenoszenia według typu zasobu
description: Wyświetla listę typów zasobów platformy Azure, które można przenieść do nowej grupy zasobów lub subskrypcji.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 45450b21b1cd3236712043629f433c2c5fe20f80
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2020
ms.locfileid: "82900975"
---
# <a name="move-operation-support-for-resources"></a>Obsługa operacji przenoszenia dla zasobów
W tym artykule przedstawiono, czy typ zasobu platformy Azure obsługuje operację przenoszenia. Zawiera również informacje dotyczące specjalnych warunków, które należy wziąć pod uwagę podczas przeniesienia zasobu.

Przejdź do przestrzeni nazw dostawcy zasobów:
> [!div class="op_single_selector"]
> - [Microsoft. AAD](#microsoftaad)
> - [Microsoft. aadiam](#microsoftaadiam)
> - [Microsoft. Advisor](#microsoftadvisor)
> - [Microsoft. AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft. AnalysisServices](#microsoftanalysisservices)
> - [Microsoft. ApiManagement](#microsoftapimanagement)
> - [Microsoft. AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. AppPlatform](#microsoftappplatform)
> - [Microsoft. AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft. Automation](#microsoftautomation)
> - [Microsoft. usługi azureactivedirectory](#microsoftazureactivedirectory)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft. AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft. Batchai Job](#microsoftbatchai)
> - [Microsoft. rozliczenia](#microsoftbilling)
> - [Microsoft. BingMaps](#microsoftbingmaps)
> - [Microsoft. BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft. łańcucha bloków](#microsoftblockchain)
> - [Microsoft. BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft. plan](#microsoftblueprint)
> - [Microsoft. BotService](#microsoftbotservice)
> - [Microsoft. cache](#microsoftcache)
> - [Microsoft. CDN](#microsoftcdn)
> - [Microsoft. CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft. ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft. ClassicStorage](#microsoftclassicstorage)
> - [Microsoft. Cognition](#microsoftcognition)
> - [Microsoft. CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft. zużycie](#microsoftconsumption)
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
> - [Microsoft. dataprotection](#microsoftdataprotection)
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
> - [Microsoft. DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft. DocumentDB](#microsoftdocumentdb)
> - [Microsoft. DomainRegistration](#microsoftdomainregistration)
> - [Microsoft. EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft. EventGrid](#microsofteventgrid)
> - [Microsoft. EventHub](#microsofteventhub)
> - [Microsoft. eksperymentowanie](#microsoftexperimentation)
> - [Microsoft. Falcon](#microsoftfalcon)
> - [Microsoft. genomika](#microsoftgenomics)
> - [Microsoft. GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft. HanaOnAzure](#microsofthanaonazure)
> - [Microsoft. HDInsight](#microsofthdinsight)
> - [Microsoft. HealthcareApis](#microsofthealthcareapis)
> - [Microsoft. HybridCompute](#microsofthybridcompute)
> - [Microsoft. HybridData](#microsofthybriddata)
> - [Microsoft. Hydra](#microsofthydra)
> - [Microsoft. ImportExport](#microsoftimportexport)
> - [Microsoft. Insights](#microsoftinsights)
> - [Microsoft. IoTCentral](#microsoftiotcentral)
> - [Microsoft. IoTSpaces](#microsoftiotspaces)
> - [Microsoft. kluczy — magazyn](#microsoftkeyvault)
> - [Microsoft. Kubernetes](#microsoftkubernetes)
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
> - [Microsoft. Maintenance](#microsoftmaintenance)
> - [Microsoft. ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft. ManagedNetwork](#microsoftmanagednetwork)
> - [Microsoft. ManagedServices](#microsoftmanagedservices)
> - [Microsoft. Maps](#microsoftmaps)
> - [Microsoft. MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft. Media](#microsoftmedia)
> - [Microsoft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft. Migrowanie](#microsoftmigrate)
> - [Microsoft. NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft. NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft. ObjectStore](#microsoftobjectstore)
> - [Microsoft. OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft. OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft. Komunikacja równorzędna](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft. Portal](#microsoftportal)
> - [Microsoft. PortalSdk](#microsoftportalsdk)
> - [Microsoft. PowerBI](#microsoftpowerbi)
> - [Microsoft. PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft. ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft. ProjectOxford](#microsoftprojectoxford)
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
> - [Microsoft. SqlVM](#microsoftsqlvm)
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
> - [Microsoft. TerraformOSS](#microsoftterraformoss)
> - [Microsoft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft. token](#microsofttoken)
> - [Microsoft. VirtualMachineImages](#microsoftvirtualmachineimages)
> - [Microsoft. VisualStudio](#microsoftvisualstudio)
> - [Microsoft. VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft. VnfManager](#microsoftvnfmanager)
> - [Microsoft. VSOnline](#microsoftvsonline)
> - [Microsoft. Web](#microsoftweb)
> - [Microsoft. WindowsESU](#microsoftwindowsesu)
> - [Microsoft. WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. Monitor obciążenia został](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft. AAD

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | domainservices | Nie | Nie |

## <a name="microsoftaadiam"></a>Microsoft. aadiam

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | dzierżaw | Nie | Nie |

## <a name="microsoftadvisor"></a>Microsoft. Advisor

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | komputerów | Nie | Nie |
> | zalecenia | Nie | Nie |
> | pominięć | Nie | Nie |

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | actionrules | Tak | Tak |
> | alerts | Nie | Nie |
> | alertssummary | Nie | Nie |
> | smartdetectoralertrules | Tak | Tak |

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | serwerem | Tak | Tak |

## <a name="microsoftapimanagement"></a>Microsoft. ApiManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | usługa | Tak | Tak |

> [!IMPORTANT]
> Nie można przenieść usługi API Management, która jest ustawiona na jednostkę SKU zużycia.

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | configurationstores | Tak | Tak |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Spring | Tak | Tak |

## <a name="microsoftappservice"></a>Microsoft. AppService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | apiapps | Nie | Nie |
> | appidentities | Nie | Nie |
> | bram | Nie | Nie |

> [!IMPORTANT]
> Zobacz [App Service wskazówki dotyczące przenoszenia](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | CheckAccess | Nie | Nie |
> | denyassignments | Nie | Nie |
> | findorphanroleassignments | Nie | Nie |
> | Zamki | Nie | Nie |
> | uprawnienia | Nie | Nie |
> | policyassignments | Nie | Nie |
> | policydefinitions | Nie | Nie |
> | policysetdefinitions | Nie | Nie |
> | RoleAssignments | Nie | Nie |
> | roleassignmentsusagemetrics | Nie | Nie |
> | roledefinitions | Nie | Nie |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | automationaccounts | Tak | Tak |
> | automationaccounts/konfiguracje | Tak | Tak |
> | automationaccounts/elementy Runbook | Tak | Tak |

> [!IMPORTANT]
> Elementy Runbook muszą istnieć w tej samej grupie zasobów co konto usługi Automation.

## <a name="microsoftazureactivedirectory"></a>Microsoft. usługi azureactivedirectory

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Tak | Tak |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | hybriddatamanagers | Nie | Nie |
> | postgresinstances | Nie | Nie |
> | wystąpienia sqlinstances | Nie | Nie |
> | sqlserverregistrations | Tak | Tak |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | rejestracje | Tak | Tak |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Tak | Tak |

## <a name="microsoftbatchai"></a>Microsoft. Batchai Job

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | oparty | Nie | Nie |
> | fileservers | Nie | Nie |
> | zadania | Nie | Nie |
> | obszary robocze | Nie | Nie |

## <a name="microsoftbilling"></a>Microsoft. rozliczenia

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | billingperiods | Nie | Nie |
> | billingpermissions | Nie | Nie |
> | billingroleassignments | Nie | Nie |
> | billingroledefinitions | Nie | Nie |
> | createbillingroleassignment | Nie | Nie |

## <a name="microsoftbingmaps"></a>Microsoft. BingMaps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | mapapis | Nie | Nie |

## <a name="microsoftbiztalkservices"></a>Microsoft. BizTalkServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | biztalk | Nie | Nie |

## <a name="microsoftblockchain"></a>Microsoft. łańcucha bloków

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | Nie | Nie |
> | cordamembers | Nie | Nie |
> | obserwatorów | Nie | Nie |

## <a name="microsoftblockchaintokens"></a>Microsoft. BlockchainTokens

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | tokenservices | Nie | Nie |

## <a name="microsoftblueprint"></a>Microsoft. plan

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | Nie | Nie |
> | plany | Nie | Nie |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | botservices | Tak | Tak |

## <a name="microsoftcache"></a>Microsoft. cache

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Redis | Tak | Tak |

> [!IMPORTANT]
> Jeśli wystąpienie usługi Azure cache for Redis jest skonfigurowane przy użyciu sieci wirtualnej, nie można przenieść wystąpienia do innej subskrypcji. Zobacz [ograniczenia dotyczące przenoszenia sieci](./move-limitations/networking-move-limitations.md).

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallpolicies | Tak | Tak |
> | Profil | Tak | Tak |
> | Profile/punkty końcowe | Tak | Tak |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | certificateorders | Tak | Tak |

> [!IMPORTANT]
> Zobacz [App Service wskazówki dotyczące przenoszenia](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | domainnames | Yes | Nie |
> | virtualmachines | Yes | Nie |

> [!IMPORTANT]
> Zobacz [klasyczne wskazówki dotyczące przenoszenia wdrożenia](./move-limitations/classic-model-move-limitations.md). Klasyczne zasoby wdrażania można przenosić między subskrypcjami przy użyciu operacji specyficznych dla tego scenariusza.

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | networksecuritygroups | Nie | Nie |
> | reservedips | Nie | Nie |
> | virtualnetworks | Nie | Nie |

> [!IMPORTANT]
> Zobacz [klasyczne wskazówki dotyczące przenoszenia wdrożenia](./move-limitations/classic-model-move-limitations.md). Klasyczne zasoby wdrażania można przenosić między subskrypcjami przy użyciu operacji specyficznych dla tego scenariusza.

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Yes | Nie |

> [!IMPORTANT]
> Zobacz [klasyczne wskazówki dotyczące przenoszenia wdrożenia](./move-limitations/classic-model-move-limitations.md). Klasyczne zasoby wdrażania można przenosić między subskrypcjami przy użyciu operacji specyficznych dla tego scenariusza.

## <a name="microsoftcognition"></a>Microsoft. Cognition

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | syntheticsaccounts | Nie | Nie |

## <a name="microsoftcognitiveservices"></a>Microsoft. CognitiveServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Konta | Tak | Tak |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | availabilitysets | Tak | Tak |
> | diskencryptionsets | Nie | Nie |
> | dysku | Tak | Tak |
> | znajduj | Nie | Nie |
> | Galerie/obrazy | Nie | Nie |
> | Galerie/obrazy/wersje | Nie | Nie |
> | hostgroups | Nie | Nie |
> | hostgroups/hosty | Nie | Nie |
> | images | Tak | Tak |
> | proximityplacementgroups | Tak | Tak |
> | restorepointcollections | Nie | Nie |
> | sharedvmextensions | Nie | Nie |
> | sharedvmimages | Nie | Nie |
> | sharedvmimages/wersje | Nie | Nie |
> | Migawki | Tak | Tak |
> | sshpublickeys | Nie | Nie |
> | virtualmachines | Tak | Tak |
> | virtualmachines/rozszerzenia | Tak | Tak |
> | virtualmachinescalesets | Tak | Tak |

> [!IMPORTANT]
> Zobacz [Virtual Machines wskazówki dotyczące przenoszenia](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftconsumption"></a>Microsoft. zużycie

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | aggregatedcost | Nie | Nie |
> | równoważy | Nie | Nie |
> | budżetów | Nie | Nie |
> | odsetk | Nie | Nie |
> | costtags | Nie | Nie |
> | środki | Nie | Nie |
> | zdarzenia | Nie | Nie |
> | prognozy | Nie | Nie |
> | ilości | Nie | Nie |
> | platform handlowych | Nie | Nie |
> | operationresults | Nie | Nie |
> | operationstatus | Nie | Nie |
> | pricesheets | Nie | Nie |
> | wyrobów | Nie | Nie |
> | reservationdetails | Nie | Nie |
> | reservationrecommendations | Nie | Nie |
> | reservationsummaries | Nie | Nie |
> | reservationtransactions | Nie | Nie |
> | tags | Nie | Nie |
> | dzierżaw | Nie | Nie |
> | odsetk | Nie | Nie |
> | usagedetails | Nie | Nie |

## <a name="microsoftcontainer"></a>Microsoft. Container

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | containergroups | Nie | Nie |

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | containergroups | Nie | Nie |
> | serviceassociationlinks | Nie | Nie |

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | wołuje | Tak | Tak |
> | rejestry/agentpools | Nie | Nie |
> | rejestry/BuildTasks | Tak | Tak |
> | rejestry/replikacje | Tak | Tak |
> | rejestry/taskruns | Tak | Tak |
> | rejestry/zadania | Tak | Tak |
> | rejestry/elementy webhook | Tak | Tak |

## <a name="microsoftcontainerservice"></a>Microsoft. ContainerService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | containerservices | Nie | Nie |
> | managedclusters | Nie | Nie |
> | openshiftmanagedclusters | Nie | Nie |

## <a name="microsoftcontentmoderator"></a>Microsoft. ContentModerator

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | aplikacje | Nie | Nie |

## <a name="microsoftcortanaanalytics"></a>Microsoft. CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Konta | Nie | Nie |

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | alerts | Nie | Nie |
> | budżetów | Nie | Nie |
> | konta integracji | Tak | Tak |
> | Wymiary | Nie | Nie |
> | Eksportowanie | Nie | Nie |
> | externalsubscriptions | Nie | Nie |
> | forecast | Nie | Nie |
> | query | Nie | Nie |
> | reportconfigs | Nie | Nie |
> | reports | Nie | Nie |
> | showbackrules | Nie | Nie |
> | widoki | Nie | Nie |

## <a name="microsoftcustomerinsights"></a>Microsoft. CustomerInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | koncentratory, | Nie | Nie |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | związku | Nie | Nie |
> | resourceproviders | Tak | Tak |

## <a name="microsoftdatabox"></a>Microsoft. DataBox

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | zadania | Nie | Nie |

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | databoxedgedevices | Nie | Nie |

## <a name="microsoftdatabricks"></a>Microsoft. datacegły

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | obszary robocze | Nie | Nie |

## <a name="microsoftdatacatalog"></a>Microsoft. datacatalog

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | wykazy | Tak | Tak |
> | wykazy | Nie | Nie |

## <a name="microsoftdataconnect"></a>Microsoft. dataconnect

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | Nie | Nie |

## <a name="microsoftdataexchange"></a>Microsoft. dataexchange

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | pakiety | Nie | Nie |
> | pakiety | Nie | Nie |

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | fabryki datafactors | Tak | Tak |
> | fabryki | Tak | Tak |

## <a name="microsoftdatalake"></a>Microsoft. datalake

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | Nie | Nie |

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Konta | Tak | Tak |

## <a name="microsoftdatalakestore"></a>Microsoft. kontach datalakestore

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Konta | Tak | Tak |

## <a name="microsoftdatamigration"></a>Migracja Microsoft.

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | services | Nie | Nie |
> | usługi/projekty | Nie | Nie |
> | czasów | Nie | Nie |

## <a name="microsoftdataprotection"></a>Microsoft. dataprotection

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | backupvaults | Nie | Nie |

## <a name="microsoftdatashare"></a>Microsoft. dataudział

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Konta | Tak | Tak |

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | serwerem | Tak | Tak |

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | serwerem | Tak | Tak |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | servergroups | Nie | Nie |
> | serwerem | Tak | Tak |
> | serversv2 | Tak | Tak |
> | singleservers | Tak | Tak |

## <a name="microsoftdeploymentmanager"></a>Microsoft. Deploymentmanager

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | artifactsources | Tak | Tak |
> | wprowadzanie | Tak | Tak |
> | servicetopologie | Tak | Tak |
> | servicetopologie/usługi | Tak | Tak |
> | servicetopologie/usługi/serviceunits | Tak | Tak |
> | kroki | Tak | Tak |

## <a name="microsoftdesktopvirtualization"></a>Microsoft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | applicationgroups | Nie | Nie |
> | hostpools | Nie | Nie |
> | obszary robocze | Nie | Nie |

## <a name="microsoftdevices"></a>Microsoft. urządzenia

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | elasticpools | Nie | Nie |
> | elasticpools / iothubtenants | Nie | Nie |
> | iothubs | Tak | Tak |
> | provisioningservices | Tak | Tak |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | potoki | Tak | Tak |

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | kontrolery | Tak | Tak |

## <a name="microsoftdevtestlab"></a>Microsoft. wspólny

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | labcenters | Nie | Nie |
> | Labs | Yes | Nie |
> | laboratoria/środowiska | Tak | Tak |
> | laboratoria/servicerunner | Tak | Tak |
> | Labs/virtualmachines | Yes | Nie |
> | Uruchamianie | Tak | Tak |

## <a name="microsoftdigitaltwins"></a>Microsoft. DigitalTwins

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | Nie | Nie |

## <a name="microsoftdocumentdb"></a>Microsoft. DocumentDB

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | databaseaccounts | Tak | Tak |

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | 3.x | Tak | Tak |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | services | Tak | Tak |

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | 3.x | Tak | Tak |
> | eventSubscriptions | Nie — nie można przenieść niezależnie, ale automatycznie przeniesiono z subskrybowanym zasobem. | Nie — nie można przenieść niezależnie, ale automatycznie przeniesiono z subskrybowanym zasobem. |
> | eventsubscriptions | Nie — nie można przenieść niezależnie, ale automatycznie przeniesiono z subskrybowanym zasobem. | Nie — nie można przenieść niezależnie, ale automatycznie przeniesiono z subskrybowanym zasobem. |
> | extensiontopics | Nie | Nie |
> | partnernamespaces | Tak | Tak |
> | partnerregistrations | Nie | Nie |
> | partnertopics | Tak | Tak |
> | systemtopics | Tak | Tak |
> | opisano | Tak | Tak |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | oparty | Tak | Tak |
> | przestrzeni | Tak | Tak |

## <a name="microsoftexperimentation"></a>Microsoft. eksperymentowanie

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | experimentworkspaces | Nie | Nie |

## <a name="microsoftfalcon"></a>Microsoft. Falcon

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | przestrzeni | Tak | Tak |

## <a name="microsoftgenomics"></a>Microsoft. genomika

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Konta | Nie | Nie |

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | automanagedaccounts | Nie | Nie |
> | automanagedvmconfigurationprofiles | Nie | Nie |
> | guestconfigurationassignments | Nie | Nie |
> | programy | Nie | Nie |
> | softwareupdateprofile | Nie | Nie |
> | softwareupdates | Nie | Nie |

## <a name="microsofthanaonazure"></a>Microsoft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | hanainstances | Nie | Nie |
> | sapmonitors | Tak | Tak |

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | oparty | Tak | Tak |

> [!IMPORTANT]
> Klastry usługi HDInsight można przenosić do nowej subskrypcji lub grupy zasobów. Nie jest jednak możliwe przechodzenie między subskrypcjami zasobów sieciowych połączonych z klastrem usługi HDInsight (takich jak sieć wirtualna, karta sieciowa lub moduł równoważenia obciążenia). Ponadto nie można przenieść do nowej grupy zasobów karty sieciowej podłączonej do maszyny wirtualnej w klastrze.
>
> Podczas przenoszenia klastra usługi HDInsight do nowej subskrypcji najpierw Przenieś inne zasoby (takie jak konto magazynu). Następnie należy przenieść klaster usługi HDInsight do samego siebie.

## <a name="microsofthealthcareapis"></a>Microsoft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | services | Tak | Tak |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Win64 | Tak | Tak |
> | Maszyny/rozszerzenia | Nie | Nie |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | datamanagers | Tak | Tak |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | komponentów | Nie | Nie |
> | networkscopes | Nie | Nie |

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | zadania | Tak | Tak |

## <a name="microsoftinsights"></a>Microsoft. Insights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | actiongroups | Tak | Tak |
> | activitylogalerts | Nie | Nie |
> | alertrules | Tak | Tak |
> | autoscalesettings | Tak | Tak |
> | punkcie | Nie | Nie |
> | calculatebaseline | Nie | Nie |
> | komponentów | Tak | Tak |
> | datacollectionrules | Nie | Nie |
> | diagnosticsettings | Nie | Nie |
> | diagnosticsettingscategories | Nie | Nie |
> | Typ zdarzenia | Nie | Nie |
> | extendeddiagnosticsettings | Nie | Nie |
> | guestdiagnosticsettings | Nie | Nie |
> | logdefinitions | Nie | Nie |
> | dzienniki | Nie | Nie |
> | metricalerts | Nie | Nie |
> | metricbaselines | Nie | Nie |
> | metricdefinitions | Nie | Nie |
> | metricnamespaces | Nie | Nie |
> | metrics | Nie | Nie |
> | Moje skoroszyty | Nie | Nie |
> | notificationgroups | Nie | Nie |
> | privatelinkscopes | Tak | Tak |
> | scheduledqueryrules | Tak | Tak |
> | topology | Nie | Nie |
> | transakcje | Nie | Nie |
> | vminsightsonboardingstatuses | Nie | Nie |
> | testów internetowych | Tak | Tak |
> | skoroszyty | Tak | Tak |
> | workbooktemplates | Tak | Tak |

> [!IMPORTANT]
> Upewnij się, że przejście do nowej subskrypcji nie przekracza [przydziałów subskrypcji](azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | iotapps | Tak | Tak |

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Tak | Tak |
> | ziół | Tak | Tak |

## <a name="microsoftkeyvault"></a>Microsoft. kluczy — magazyn

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | hsmpools | Nie | Nie |
> | magazynów | Tak | Tak |

> [!IMPORTANT]
> Magazyny kluczy używane do szyfrowania dysków nie mogą zostać przeniesione do grupy zasobów w tej samej subskrypcji lub w różnych subskrypcjach.

## <a name="microsoftkubernetes"></a>Microsoft. Kubernetes

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | connectedclusters | Nie | Nie |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | oparty | Tak | Tak |

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | labaccounts | Nie | Nie |

## <a name="microsoftlocationbasedservices"></a>Microsoft. LocationBasedServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Konta | Nie | Nie |

## <a name="microsoftlocationservices"></a>Microsoft. LocationServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Konta | Nie | Nie |

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | Nie | Nie |
> | integrationaccounts | Tak | Tak |
> | integrationserviceenvironments | Yes | Nie |
> | integrationserviceenvironments/managedapis | Yes | Nie |
> | isolatedenvironments | Nie | Nie |
> | przebieg | Tak | Tak |

## <a name="microsoftmachinelearning"></a>Microsoft. MachineLearning

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | commitmentplans | Nie | Nie |
> | WebServices | Yes | Nie |
> | obszary robocze | Tak | Tak |

## <a name="microsoftmachinelearningcompute"></a>Microsoft. MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | Nie | Nie |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft. MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Konta | Nie | Nie |
> | konta/obszary robocze | Nie | Nie |
> | konta/obszary robocze/projekty | Nie | Nie |
> | teamaccounts | Nie | Nie |
> | teamaccounts/obszary robocze | Nie | Nie |
> | teamaccounts/obszary robocze/projekty | Nie | Nie |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft. MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Konta | Nie | Nie |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft. MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | hostingaccounts | Nie | Nie |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | obszary robocze | Nie | Nie |
> | obszary robocze/obliczenia | Nie | Nie |

## <a name="microsoftmaintenance"></a>Microsoft. Maintenance

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | applyupdates | Nie | Nie |
> | configurationassignments | Nie | Nie |
> | maintenanceconfigurations | Tak | Tak |
> | aktualizacje | Nie | Nie |

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | osob | Nie | Nie |
> | resourceidentity | Nie | Nie |

## <a name="microsoftmanagednetwork"></a>Microsoft. ManagedNetwork

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | managednetworks | Nie | Nie |
> | managednetworks / managednetworkgroups | Nie | Nie |
> | managednetworks / managednetworkpeeringpolicies | Nie | Nie |
> | powiadomienie | Nie | Nie |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | registrationassignments | Nie | Nie |
> | registrationdefinitions | Nie | Nie |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Konta | Tak | Tak |
> | konta/privateatlases | Tak | Tak |

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Nie | Nie |

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | MediaServices | Tak | Tak |
> | MediaServices/liveevents | Tak | Tak |
> | MediaServices/streamingendpoints | Tak | Tak |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | appclusters | Nie | Nie |

## <a name="microsoftmigrate"></a>Microsoft. Migrowanie

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | Tak | Tak |
> | migrateprojects | Tak | Tak |
> | movecollections | Nie | Nie |
> | projektami | Nie | Nie |

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | netappaccounts | Nie | Nie |
> | netappaccounts / backuppolicies | Nie | Nie |
> | netappaccounts / capacitypools | Nie | Nie |
> | netappaccounts/capacitypools/woluminy | Nie | Nie |
> | netappaccounts/capacitypools/woluminy/mounttargets | Nie | Nie |
> | netappaccounts/capacitypools/woluminy/migawki | Nie | Nie |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | applicationgateways | Nie | Nie |
> | applicationgatewaywebapplicationfirewallpolicies | Nie | Nie |
> | applicationsecuritygroups | Tak | Tak |
> | azurefirewalls | Tak | Tak |
> | bastionhosts | Nie | Nie |
> | Licznik | Tak | Tak |
> | ddoscustompolicies | Tak | Tak |
> | ddosprotectionplans | Nie | Nie |
> | dnszones | Tak | Tak |
> | expressroutecircuits | Nie | Nie |
> | expressroutegateways | Nie | Nie |
> | firewallpolicies | Tak | Tak |
> | usługi frontdoor | Nie | Nie |
> | frontdoorwebapplicationfirewallpolicies | Nie | Nie |
> | ipgroups | Tak | Tak |
> | loadbalancers | Tak — podstawowa jednostka SKU<br>Niestandardowa jednostka SKU | Tak — podstawowa jednostka SKU<br>Niestandardowa jednostka SKU |
> | localnetworkgateways | Tak | Tak |
> | natgateways | Tak | Tak |
> | networkexperimentprofiles | Tak | Tak |
> | networkintentpolicies | Tak | Tak |
> | networkinterfaces | Tak | Tak |
> | networkprofiles | Nie | Nie |
> | networksecuritygroups | Tak | Tak |
> | networkwatchers | Yes | Nie |
> | networkwatchers / connectionmonitors | Yes | Nie |
> | networkwatchers / flowlogs | Yes | Nie |
> | networkwatchers / pingmeshes | Yes | Nie |
> | p2svpngateways | Nie | Nie |
> | privatednszones | Tak | Tak |
> | privatednszones / virtualnetworklinks | Tak | Tak |
> | privateendpointredirectmaps | Nie | Nie |
> | privateendpoints | Tak | Tak |
> | privatelinkservices | Nie | Nie |
> | adresów publicipaddress | Tak — podstawowa jednostka SKU<br>Niestandardowa jednostka SKU | Tak — podstawowa jednostka SKU<br>Niestandardowa jednostka SKU |
> | publicipprefixes | Tak | Tak |
> | routefilters | Nie | Nie |
> | routetables | Tak | Tak |
> | serviceendpointpolicies | Tak | Tak |
> | trafficmanagerprofiles | Tak | Tak |
> | virtualhubs | Nie | Nie |
> | virtualnetworkgateways | Tak | Tak |
> | virtualnetworks | Tak | Tak |
> | virtualnetworktaps | Nie | Nie |
> | virtualrouters | Tak | Tak |
> | virtualwans | Nie | Nie |
> | vpngateways (wirtualna sieć WAN) | Nie | Nie |
> | vpnserverconfigurations | Nie | Nie |
> | vpnsites (wirtualna sieć WAN) | Nie | Nie |
> | webapplicationfirewallpolicies | Tak | Tak |

> [!IMPORTANT]
> Zobacz [wskazówki dotyczące przenoszenia sieci](./move-limitations/networking-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | przestrzeni | Tak | Tak |
> | przestrzenie nazw/notificationhubs | Tak | Tak |

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | osnamespaces | Tak | Tak |

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | oparty | Nie | Nie |
> | storageinsightconfigs | Nie | Nie |
> | obszary robocze | Tak | Tak |

> [!IMPORTANT]
> Upewnij się, że przejście do nowej subskrypcji nie przekracza [przydziałów subskrypcji](azure-subscription-service-limits.md#azure-monitor-limits).

> [!IMPORTANT]
> Nie można przenieść obszarów roboczych, które mają połączone konto usługi Automation. Przed rozpoczęciem operacji przenoszenia należy pamiętać o odłączeniu wszystkich kont usługi Automation.   

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | managementassociations | Nie | Nie |
> | managementconfigurations | Tak | Tak |
> | rozwiązania | Tak | Tak |
> | widoki | Tak | Tak |

## <a name="microsoftpeering"></a>Microsoft. Komunikacja równorzędna

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | komunikacje równorzędne | Tak | Tak |
> | peeringservices | Nie | Nie |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | policyevents | Nie | Nie |
> | policystates | Nie | Nie |
> | policytrackedresources | Nie | Nie |
> | korygowania | Nie | Nie |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | pulpity nawigacyjne | Tak | Tak |

## <a name="microsoftportalsdk"></a>Microsoft. PortalSdk

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | rootresources | Nie | Nie |

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | workspacecollections | Tak | Tak |

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | produkcyjnych | Tak | Tak |

## <a name="microsoftprojectbabylon"></a>Microsoft. ProjectBabylon

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Konta | Nie | Nie |

## <a name="microsoftprojectoxford"></a>Microsoft. ProjectOxford

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Konta | Nie | Nie |

## <a name="microsoftproviderhub"></a>Microsoft. ProviderHub

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | wprowadzanie | Nie | Nie |

## <a name="microsoftquantum"></a>Microsoft. Quantum

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | obszary robocze | Nie | Nie |

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | backupprotecteditems | Nie | Nie |
> | replicationeligibilityresults | Nie | Nie |
> | magazynów | Tak | Tak |

> [!IMPORTANT]
> Zobacz [Recovery Services wskazówki dotyczące przenoszenia](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftredhatopenshift"></a>Microsoft. RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | openshiftclusters | Nie | Nie |

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | przestrzeni | Tak | Tak |

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | wybiera | Tak | Tak |

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | availabilitystatuses | Nie | Nie |
> | childavailabilitystatuses | Nie | Nie |
> | childresources | Nie | Nie |
> | zdarzenia | Nie | Nie |
> | powiadomienia | Nie | Nie |

## <a name="microsoftresources"></a>Microsoft. resources

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | deploymentscripts | Nie | Nie |
> | linki | Nie | Nie |
> | tags | Nie | Nie |

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | aplikacje | Yes | Nie |

## <a name="microsoftsearch"></a>Microsoft. Search

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | searchservices | Tak | Tak |

> [!IMPORTANT]
> Nie można przenieść kilku zasobów wyszukiwania w różnych regionach w jednej operacji. Zamiast tego należy przenieść je w osobnych operacjach.

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | adaptivenetworkhardenings | Nie | Nie |
> | advancedthreatprotectionsettings | Nie | Nie |
> | assessmentmetadata | Nie | Nie |
> | oceny | Nie | Nie |
> | automatyzacji | Tak | Tak |
> | complianceresults | Nie | Nie |
> | zachowania zgodności prawnymi | Nie | Nie |
> | datacollectionagents | Nie | Nie |
> | devicesecuritygroups | Nie | Nie |
> | informationprotectionpolicies | Nie | Nie |
> | iotsecuritysolutions | Tak | Tak |
> | servervulnerabilityassessments | Nie | Nie |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | agregacji | Nie | Nie |
> | alertrules | Nie | Nie |
> | alertruletemplates | Nie | Nie |
> | zakładki | Nie | Nie |
> | padkach | Nie | Nie |
> | połączenia dataconnecters | Nie | Nie |
> | dataconnectorscheckrequirements | Nie | Nie |
> | obiekty | Nie | Nie |
> | entityqueries | Nie | Nie |
> | zdarzenia | Nie | Nie |
> | officeconsents | Nie | Nie |
> | settings | Nie | Nie |

## <a name="microsoftservermanagement"></a>Microsoft. ServerManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | bram | Nie | Nie |
> | nich | Nie | Nie |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | przestrzeni | Tak | Tak |

## <a name="microsoftservicefabric"></a>Microsoft. servicefabric

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | aplikacje | Nie | Nie |
> | oparty | Tak | Tak |
> | klastry/aplikacje | Nie | Nie |
> | containergroups | Nie | Nie |
> | containergroupsets | Nie | Nie |
> | edgeclusters | Nie | Nie |
> | managedclusters | Nie | Nie |
> | sieci | Nie | Nie |
> | secretstores | Nie | Nie |
> | volumes | Nie | Nie |

## <a name="microsoftservicefabricmesh"></a>Microsoft. ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | aplikacje | Tak | Tak |
> | containergroups | Nie | Nie |
> | bram | Tak | Tak |
> | sieci | Tak | Tak |
> | wpisy tajne | Tak | Tak |
> | volumes | Tak | Tak |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | wprowadzanie | Nie | Nie |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | signalr | Tak | Tak |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | hybridusebenefits | Nie | Nie |

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | applicationdefinitions | Nie | Nie |
> | aplikacje | Nie | Nie |
> | jitrequests | Nie | Nie |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | instancepools | Nie | Nie |
> | locations | Tak | Tak |
> | managedinstances | Nie | Nie |
> | ManagedInstances/bazy danych | Nie | Nie |
> | serwerem | Tak | Tak |
> | Serwery/bazy danych | Tak | Tak |
> | serwery/elasticpools | Tak | Tak |
> | serwery/jobaccounts | Tak | Tak |
> | serwery/jobagents | Tak | Tak |
> | virtualclusters | Tak | Tak |

> [!IMPORTANT]
> Baza danych i serwer muszą znajdować się w tej samej grupie zasobów. Podczas przenoszenia programu SQL Server wszystkie jego bazy danych również są przenoszone. To zachowanie ma zastosowanie do baz danych Azure SQL Database i Azure SQL Data Warehouse.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | Tak | Tak |
> | sqlvirtualmachines | Tak | Tak |

## <a name="microsoftsqlvm"></a>Microsoft. SqlVM

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | dwvm | Nie | Nie |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Tak | Tak |

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | pamięci podręcznych | Nie | Nie |

## <a name="microsoftstoragesync"></a>Microsoft. StorageSync

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Tak | Tak |

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Nie | Nie |

## <a name="microsoftstoragesyncint"></a>Microsoft. StorageSyncInt

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Nie | Nie |

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | skonfigurowany | Nie | Nie |

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | streamingjobs | Tak | Tak |

> [!IMPORTANT]
> Nie można przenieść zadań Stream Analytics, gdy stan jest uruchomiony.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft. StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | wiejski | Nie | Nie |
> | środowiska/źródła zdarzeń | Nie | Nie |
> | Liczba | Nie | Nie |
> | wystąpienia/środowiska | Nie | Nie |
> | wystąpienia/środowiska/obiekty EventSource | Nie | Nie |

## <a name="microsoftsubscription"></a>Microsoft. Subscription

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Anulowanie subskrypcji | Nie | Nie |

## <a name="microsoftsupport"></a>Microsoft. Support

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | supporttickets | Nie | Nie |

## <a name="microsoftsynapse"></a>Microsoft. Synapse

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | obszary robocze | Nie | Nie |
> | obszary robocze/bigdatapools | Nie | Nie |
> | obszary robocze/pule | Nie | Nie |

## <a name="microsoftterraformoss"></a>Microsoft. TerraformOSS

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | providerregistrations | Nie | Nie |
> | zasoby | Nie | Nie |

## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | wiejski | Tak | Tak |
> | środowiska/źródła zdarzeń | Tak | Tak |
> | środowiska/referencedatasets | Tak | Tak |

## <a name="microsofttoken"></a>Microsoft. token

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | przechowywać | Tak | Tak |

## <a name="microsoftvirtualmachineimages"></a>Microsoft. VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | imagetemplates | Nie | Nie |

## <a name="microsoftvisualstudio"></a>Microsoft. VisualStudio

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | account | Nie | Nie |
> | konto/rozszerzenie | Tak | Tak |
> | konto/projekt | Tak | Tak |

> [!IMPORTANT]
> Aby zmienić subskrypcję usługi Azure DevOps, zobacz [zmiana subskrypcji platformy Azure używanej do rozliczania](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | Nie | Nie |
> | dedicatedcloudservices | Nie | Nie |
> | virtualmachines | Nie | Nie |

## <a name="microsoftvnfmanager"></a>Microsoft. VnfManager

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | devices | Nie | Nie |
> | vnfs | Nie | Nie |

## <a name="microsoftvsonline"></a>Microsoft. VSOnline

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Konta | Tak | Tak |
> | pakiety | Tak | Tak |

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | certyfikaty | Nie | Yes |
> | connectiongateways | Tak | Tak |
> | Licznik | Tak | Tak |
> | customapis | Tak | Tak |
> | hostingenvironments | Nie | Nie |
> | kubeenvironments | Tak | Tak |
> | dopuszczalna | Tak | Tak |
> | lokacje | Tak | Tak |
> | Lokacje/premieraddons | Tak | Tak |
> | Lokacje/miejsca | Tak | Tak |
> | staticsites | Nie | Nie |

> [!IMPORTANT]
> Zobacz [App Service wskazówki dotyczące przenoszenia](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsesu"></a>Microsoft. WindowsESU

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | multipleactivationkeys | Nie | Nie |

## <a name="microsoftwindowsiot"></a>Microsoft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | deviceservices | Nie | Nie |

## <a name="microsoftworkloadmonitor"></a>Microsoft. Monitor obciążenia został

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | komponentów | Nie | Nie |
> | monitorinstances | Nie | Nie |
> | monitora | Nie | Nie |
> | notificationsettings | Nie | Nie |

## <a name="third-party-services"></a>Usługi innych firm

Usługi innych firm obecnie nie obsługują operacji przenoszenia.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać polecenia przenoszenia zasobów, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](move-resource-group-and-subscription.md).

Aby uzyskać te same dane jak plik z wartościami rozdzielanymi przecinkami, Pobierz [Move-support-Resources. csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
