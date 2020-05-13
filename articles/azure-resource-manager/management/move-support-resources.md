---
title: Obsługa operacji przenoszenia według typu zasobu
description: Wyświetla listę typów zasobów platformy Azure, które można przenieść do nowej grupy zasobów lub subskrypcji.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 81d545066ea6bcc1d3e2eecd884671324155d796
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124690"
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
> | actionrules | Yes | Yes |
> | alerts | Nie | Nie |
> | alertssummary | Nie | Nie |
> | smartdetectoralertrules | Yes | Yes |

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | serwerem | Yes | Yes |

## <a name="microsoftapimanagement"></a>Microsoft. ApiManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | usługa | Yes | Yes |

> [!IMPORTANT]
> Nie można przenieść usługi API Management, która jest ustawiona na jednostkę SKU zużycia.

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | configurationstores | Yes | Yes |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Spring | Yes | Yes |

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
> | automationaccounts | Yes | Yes |
> | automationaccounts/konfiguracje | Yes | Yes |
> | automationaccounts/elementy Runbook | Yes | Yes |

> [!IMPORTANT]
> Elementy Runbook muszą istnieć w tej samej grupie zasobów co konto usługi Automation.

## <a name="microsoftazureactivedirectory"></a>Microsoft. usługi azureactivedirectory

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Yes | Yes |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | hybriddatamanagers | Nie | Nie |
> | postgresinstances | Nie | Nie |
> | wystąpienia sqlinstances | Nie | Nie |
> | sqlserverregistrations | Yes | Yes |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | rejestracje | Yes | Yes |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Yes | Yes |

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
> | botservices | Tak | Yes |

## <a name="microsoftcache"></a>Microsoft. cache

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Redis | Tak | Yes |

> [!IMPORTANT]
> Jeśli wystąpienie usługi Azure cache for Redis jest skonfigurowane przy użyciu sieci wirtualnej, nie można przenieść wystąpienia do innej subskrypcji. Zobacz [ograniczenia dotyczące przenoszenia sieci](./move-limitations/networking-move-limitations.md).

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallpolicies | Tak | Yes |
> | Profil | Tak | Yes |
> | Profile/punkty końcowe | Tak | Yes |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | certificateorders | Tak | Yes |

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
> | Konta | Yes | Yes |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | availabilitysets | Yes | Yes |
> | diskencryptionsets | Nie | Nie |
> | dysku | Yes | Yes |
> | znajduj | Nie | Nie |
> | Galerie/obrazy | Nie | Nie |
> | Galerie/obrazy/wersje | Nie | Nie |
> | hostgroups | Nie | Nie |
> | hostgroups/hosty | Nie | Nie |
> | images | Yes | Yes |
> | proximityplacementgroups | Yes | Yes |
> | restorepointcollections | Nie | Nie |
> | sharedvmextensions | Nie | Nie |
> | sharedvmimages | Nie | Nie |
> | sharedvmimages/wersje | Nie | Nie |
> | Migawki | Yes | Yes |
> | sshpublickeys | Nie | Nie |
> | virtualmachines | Yes | Yes |
> | virtualmachines/rozszerzenia | Yes | Yes |
> | virtualmachinescalesets | Yes | Yes |

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
> | wołuje | Yes | Yes |
> | rejestry/agentpools | Nie | Nie |
> | rejestry/BuildTasks | Yes | Yes |
> | rejestry/replikacje | Yes | Yes |
> | rejestry/taskruns | Yes | Yes |
> | rejestry/zadania | Yes | Yes |
> | rejestry/elementy webhook | Yes | Yes |

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
> | konta integracji | Yes | Yes |
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
> | resourceproviders | Yes | Yes |

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
> | wykazy | Yes | Yes |
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
> | fabryki datafactors | Yes | Yes |
> | fabryki | Yes | Yes |

## <a name="microsoftdatalake"></a>Microsoft. datalake

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | Nie | Nie |

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Konta | Yes | Yes |

## <a name="microsoftdatalakestore"></a>Microsoft. kontach datalakestore

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Konta | Yes | Yes |

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
> | Konta | Yes | Yes |

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | serwerem | Yes | Yes |

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | serwerem | Yes | Yes |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | servergroups | Nie | Nie |
> | serwerem | Yes | Yes |
> | serversv2 | Tak | Yes |
> | singleservers | Tak | Yes |

## <a name="microsoftdeploymentmanager"></a>Microsoft. Deploymentmanager

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | artifactsources | Tak | Yes |
> | wprowadzanie | Tak | Yes |
> | servicetopologie | Tak | Yes |
> | servicetopologie/usługi | Tak | Yes |
> | servicetopologie/usługi/serviceunits | Tak | Yes |
> | kroki | Tak | Yes |

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
> | iothubs | Tak | Yes |
> | provisioningservices | Tak | Yes |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | potoki | Tak | Yes |

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | kontrolery | Tak | Yes |

## <a name="microsoftdevtestlab"></a>Microsoft. wspólny

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | labcenters | Nie | Nie |
> | Labs | Yes | Nie |
> | laboratoria/środowiska | Tak | Yes |
> | laboratoria/servicerunner | Tak | Yes |
> | Labs/virtualmachines | Yes | Nie |
> | Uruchamianie | Tak | Yes |

## <a name="microsoftdigitaltwins"></a>Microsoft. DigitalTwins

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | Nie | Nie |

## <a name="microsoftdocumentdb"></a>Microsoft. DocumentDB

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | databaseaccounts | Yes | Yes |

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | 3.x | Yes | Yes |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | services | Yes | Yes |

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | 3.x | Yes | Yes |
> | eventSubscriptions | Nie — nie można przenieść niezależnie, ale automatycznie przeniesiono z subskrybowanym zasobem. | Nie — nie można przenieść niezależnie, ale automatycznie przeniesiono z subskrybowanym zasobem. |
> | eventsubscriptions | Nie — nie można przenieść niezależnie, ale automatycznie przeniesiono z subskrybowanym zasobem. | Nie — nie można przenieść niezależnie, ale automatycznie przeniesiono z subskrybowanym zasobem. |
> | extensiontopics | Nie | Nie |
> | partnernamespaces | Yes | Yes |
> | partnerregistrations | Nie | Nie |
> | partnertopics | Yes | Yes |
> | systemtopics | Yes | Yes |
> | opisano | Yes | Yes |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | oparty | Yes | Yes |
> | przestrzeni | Yes | Yes |

## <a name="microsoftexperimentation"></a>Microsoft. eksperymentowanie

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | experimentworkspaces | Nie | Nie |

## <a name="microsoftfalcon"></a>Microsoft. Falcon

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | przestrzeni | Yes | Yes |

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
> | sapmonitors | Yes | Yes |

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | oparty | Yes | Yes |

> [!IMPORTANT]
> Klastry usługi HDInsight można przenosić do nowej subskrypcji lub grupy zasobów. Nie jest jednak możliwe przechodzenie między subskrypcjami zasobów sieciowych połączonych z klastrem usługi HDInsight (takich jak sieć wirtualna, karta sieciowa lub moduł równoważenia obciążenia). Ponadto nie można przenieść do nowej grupy zasobów karty sieciowej podłączonej do maszyny wirtualnej w klastrze.
>
> Podczas przenoszenia klastra usługi HDInsight do nowej subskrypcji najpierw Przenieś inne zasoby (takie jak konto magazynu). Następnie należy przenieść klaster usługi HDInsight do samego siebie.

## <a name="microsofthealthcareapis"></a>Microsoft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | services | Yes | Yes |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Win64 | Yes | Yes |
> | Maszyny/rozszerzenia | Nie | Nie |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | datamanagers | Yes | Yes |

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
> | zadania | Yes | Yes |

## <a name="microsoftinsights"></a>Microsoft. Insights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | actiongroups | Yes | Yes |
> | activitylogalerts | Nie | Nie |
> | alertrules | Yes | Yes |
> | autoscalesettings | Yes | Yes |
> | punkcie | Nie | Nie |
> | calculatebaseline | Nie | Nie |
> | komponentów | Yes | Yes |
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
> | privatelinkscopes | Yes | Yes |
> | scheduledqueryrules | Yes | Yes |
> | topology | Nie | Nie |
> | transakcje | Nie | Nie |
> | vminsightsonboardingstatuses | Nie | Nie |
> | testów internetowych | Yes | Yes |
> | skoroszyty | Yes | Yes |
> | workbooktemplates | Yes | Yes |

> [!IMPORTANT]
> Upewnij się, że przejście do nowej subskrypcji nie przekracza [przydziałów subskrypcji](azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | iotapps | Yes | Yes |

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Yes | Yes |
> | ziół | Yes | Yes |

## <a name="microsoftkeyvault"></a>Microsoft. kluczy — magazyn

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | hsmpools | Nie | Nie |
> | magazynów | Yes | Yes |

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
> | oparty | Tak | Yes |

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
> | integrationaccounts | Tak | Yes |
> | integrationserviceenvironments | Yes | Nie |
> | integrationserviceenvironments/managedapis | Yes | Nie |
> | isolatedenvironments | Nie | Nie |
> | przebieg | Tak | Yes |

## <a name="microsoftmachinelearning"></a>Microsoft. MachineLearning

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | commitmentplans | Nie | Nie |
> | WebServices | Yes | Nie |
> | obszary robocze | Tak | Yes |

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
> | maintenanceconfigurations | Yes | Yes |
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
> | Konta | Yes | Yes |
> | konta/privateatlases | Yes | Yes |

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Nie | Nie |

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | MediaServices | Yes | Yes |
> | MediaServices/liveevents | Yes | Yes |
> | MediaServices/streamingendpoints | Yes | Yes |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | appclusters | Nie | Nie |

## <a name="microsoftmigrate"></a>Microsoft. Migrowanie

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | Yes | Yes |
> | migrateprojects | Yes | Yes |
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
> | applicationsecuritygroups | Yes | Yes |
> | azurefirewalls | Yes | Yes |
> | bastionhosts | Nie | Nie |
> | Licznik | Yes | Yes |
> | ddoscustompolicies | Yes | Yes |
> | ddosprotectionplans | Nie | Nie |
> | dnszones | Yes | Yes |
> | expressroutecircuits | Nie | Nie |
> | expressroutegateways | Nie | Nie |
> | firewallpolicies | Yes | Yes |
> | usługi frontdoor | Nie | Nie |
> | frontdoorwebapplicationfirewallpolicies | Nie | Nie |
> | ipgroups | Yes | Yes |
> | loadbalancers | Tak — podstawowa jednostka SKU<br>Niestandardowa jednostka SKU | Tak — podstawowa jednostka SKU<br>Niestandardowa jednostka SKU |
> | localnetworkgateways | Yes | Yes |
> | natgateways | Yes | Yes |
> | networkexperimentprofiles | Tak | Yes |
> | networkintentpolicies | Tak | Yes |
> | networkinterfaces | Tak | Yes |
> | networkprofiles | Nie | Nie |
> | networksecuritygroups | Tak | Yes |
> | networkwatchers | Yes | Nie |
> | networkwatchers / connectionmonitors | Yes | Nie |
> | networkwatchers / flowlogs | Yes | Nie |
> | networkwatchers / pingmeshes | Yes | Nie |
> | p2svpngateways | Nie | Nie |
> | privatednszones | Tak | Yes |
> | privatednszones / virtualnetworklinks | Tak | Yes |
> | privateendpointredirectmaps | Nie | Nie |
> | privateendpoints | Tak | Yes |
> | privatelinkservices | Nie | Nie |
> | adresów publicipaddress | Tak — podstawowa jednostka SKU<br>Niestandardowa jednostka SKU | Tak — podstawowa jednostka SKU<br>Niestandardowa jednostka SKU |
> | publicipprefixes | Tak | Yes |
> | routefilters | Nie | Nie |
> | routetables | Tak | Yes |
> | serviceendpointpolicies | Tak | Yes |
> | trafficmanagerprofiles | Tak | Yes |
> | virtualhubs | Nie | Nie |
> | virtualnetworkgateways | Tak | Yes |
> | virtualnetworks | Tak | Yes |
> | virtualnetworktaps | Nie | Nie |
> | virtualrouters | Tak | Yes |
> | virtualwans | Nie | Nie |
> | vpngateways (wirtualna sieć WAN) | Nie | Nie |
> | vpnserverconfigurations | Nie | Nie |
> | vpnsites (wirtualna sieć WAN) | Nie | Nie |
> | webapplicationfirewallpolicies | Tak | Yes |

> [!IMPORTANT]
> Zobacz [wskazówki dotyczące przenoszenia sieci](./move-limitations/networking-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | przestrzeni | Yes | Yes |
> | przestrzenie nazw/notificationhubs | Yes | Yes |

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | osnamespaces | Yes | Yes |

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | oparty | Nie | Nie |
> | storageinsightconfigs | Nie | Nie |
> | obszary robocze | Yes | Yes |

> [!IMPORTANT]
> Upewnij się, że przejście do nowej subskrypcji nie przekracza [przydziałów subskrypcji](azure-subscription-service-limits.md#azure-monitor-limits).
> 
> Nie można przenieść obszarów roboczych, które mają połączone konto usługi Automation. Przed rozpoczęciem operacji przenoszenia należy pamiętać o odłączeniu wszystkich kont usługi Automation.   

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | managementassociations | Nie | Nie |
> | managementconfigurations | Yes | Yes |
> | rozwiązania | Yes | Yes |
> | widoki | Yes | Yes |

## <a name="microsoftpeering"></a>Microsoft. Komunikacja równorzędna

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | komunikacje równorzędne | Yes | Yes |
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
> | pulpity nawigacyjne | Yes | Yes |

## <a name="microsoftportalsdk"></a>Microsoft. PortalSdk

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | rootresources | Nie | Nie |

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | workspacecollections | Yes | Yes |

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | produkcyjnych | Yes | Yes |

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
> | magazynów | Yes | Yes |

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
> | przestrzeni | Yes | Yes |

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | wybiera | Yes | Yes |

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
> | searchservices | Tak | Yes |

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
> | automatyzacji | Tak | Yes |
> | complianceresults | Nie | Nie |
> | zachowania zgodności prawnymi | Nie | Nie |
> | datacollectionagents | Nie | Nie |
> | devicesecuritygroups | Nie | Nie |
> | informationprotectionpolicies | Nie | Nie |
> | iotsecuritysolutions | Tak | Yes |
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
> | przestrzeni | Yes | Yes |

## <a name="microsoftservicefabric"></a>Microsoft. servicefabric

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | aplikacje | Nie | Nie |
> | oparty | Yes | Yes |
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
> | aplikacje | Yes | Yes |
> | containergroups | Nie | Nie |
> | bram | Yes | Yes |
> | sieci | Yes | Yes |
> | wpisy tajne | Yes | Yes |
> | volumes | Yes | Yes |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | wprowadzanie | Nie | Nie |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | signalr | Yes | Yes |

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
> | locations | Yes | Yes |
> | managedinstances | Nie | Nie |
> | ManagedInstances/bazy danych | Nie | Nie |
> | serwerem | Yes | Yes |
> | Serwery/bazy danych | Yes | Yes |
> | serwery/elasticpools | Yes | Yes |
> | serwery/jobaccounts | Yes | Yes |
> | serwery/jobagents | Yes | Yes |
> | virtualclusters | Yes | Yes |

> [!IMPORTANT]
> Baza danych i serwer muszą znajdować się w tej samej grupie zasobów. Podczas przenoszenia programu SQL Server wszystkie jego bazy danych również są przenoszone. To zachowanie ma zastosowanie do baz danych Azure SQL Database i Azure SQL Data Warehouse.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | Yes | Yes |
> | sqlvirtualmachines | Yes | Yes |

## <a name="microsoftsqlvm"></a>Microsoft. SqlVM

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | dwvm | Nie | Nie |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Yes | Yes |

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | pamięci podręcznych | Nie | Nie |

## <a name="microsoftstoragesync"></a>Microsoft. StorageSync

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Yes | Yes |

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
> | streamingjobs | Yes | Yes |

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
> | wiejski | Yes | Yes |
> | środowiska/źródła zdarzeń | Yes | Yes |
> | środowiska/referencedatasets | Yes | Yes |

## <a name="microsofttoken"></a>Microsoft. token

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | przechowywać | Yes | Yes |

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
> | konto/rozszerzenie | Nie | Nie |
> | konto/projekt | Nie | Nie |

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
> | Konta | Nie | Nie |
> | pakiety | Nie | Nie |

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | certyfikaty | Nie | Yes |
> | connectiongateways | Yes | Tak |
> | Licznik | Yes | Tak |
> | customapis | Yes | Tak |
> | hostingenvironments | Nie | Nie |
> | kubeenvironments | Yes | Tak |
> | dopuszczalna | Yes | Tak |
> | lokacje | Yes | Tak |
> | Lokacje/premieraddons | Yes | Tak |
> | Lokacje/miejsca | Yes | Tak |
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
