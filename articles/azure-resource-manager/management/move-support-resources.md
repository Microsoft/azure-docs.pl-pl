---
title: Obsługa operacji przenoszenia według typu zasobu
description: Wyświetla listę typów zasobów platformy Azure, które można przenieść do nowej grupy zasobów lub subskrypcji.
ms.topic: conceptual
ms.date: 07/13/2020
ms.openlocfilehash: 7b7f6f05c9cae80cffb64245a3fc8a6b0890d577
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86539231"
---
# <a name="move-operation-support-for-resources"></a>Obsługa operacji przenoszenia dla zasobów

W tym artykule przedstawiono, czy typ zasobu platformy Azure obsługuje operację przenoszenia. Zawiera również informacje dotyczące specjalnych warunków, które należy wziąć pod uwagę podczas przeniesienia zasobu.

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
> - [Microsoft. ChangeAnalysis](#microsoftchangeanalysis)
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
> - [Microsoft. CostManagementExports](#microsoftcostmanagementexports)
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
> - [Microsoft. PowerPlatform](#microsoftpowerplatform)
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
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | domainservices | Nie | Nie |
> | domainservices / oucontainer | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/operationresults | Nie | Nie |
> | operacje | Nie | Nie |

## <a name="microsoftaadiam"></a>Microsoft. aadiam

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | diagnosticsettings | Nie | Nie |
> | diagnosticsettingscategories | Nie | Nie |
> | operacje | Nie | Nie |
> | privatelinkforazuread | Tak | Tak |
> | dzierżaw | Tak | Tak |

## <a name="microsoftaddons"></a>Microsoft. Dodatki

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | operationresults | Nie | Nie |
> | operacje | Nie | Nie |
> | supportproviders | Nie | Nie |

## <a name="microsoftadhybridhealthservice"></a>Microsoft. ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | aadsupportcases | Nie | Nie |
> | addsservices | Nie | Nie |
> | pracownicy | Nie | Nie |
> | anonymousapiusers | Nie | Nie |
> | konfiguracja | Nie | Nie |
> | dzienniki | Nie | Nie |
> | operacje | Nie | Nie |
> | raporty | Nie | Nie |
> | servicehealthmetrics | Nie | Nie |
> | services | Nie | Nie |

## <a name="microsoftadvisor"></a>Microsoft. Advisor

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | komputerów | Nie | Nie |
> | generaterecommendations | Nie | Nie |
> | metadane | Nie | Nie |
> | operacje | Nie | Nie |
> | zalecenia | Nie | Nie |
> | pominięć | Nie | Nie |

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | actionrules | Tak | Tak |
> | alerts | Nie | Nie |
> | alertslist | Nie | Nie |
> | alertsmetadata | Nie | Nie |
> | alertssummary | Nie | Nie |
> | alertssummarylist | Nie | Nie |
> | operacje | Nie | Nie |
> | smartdetectoralertrules | Tak | Tak |
> | smartgroups | Nie | Nie |

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | locations | Nie | Nie |
> | Lokalizacje/checknameavailability | Nie | Nie |
> | Lokalizacje/operationresults | Nie | Nie |
> | Lokalizacje/operationstatuses | Nie | Nie |
> | operacje | Nie | Nie |
> | serwerem | Tak | Tak |

## <a name="microsoftapimanagement"></a>Microsoft. ApiManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | checkfeedbackrequired | Nie | Nie |
> | checknameavailability | Nie | Nie |
> | checkservicenameavailability | Nie | Nie |
> | operacje | Nie | Nie |
> | reportfeedback | Nie | Nie |
> | usługa | Tak | Tak |
> | validateservicename | Nie | Nie |

> [!IMPORTANT]
> Nie można przenieść usługi API Management, która jest ustawiona na jednostkę SKU zużycia.

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nie | Nie |
> | configurationstores | Tak | Tak |
> | configurationstores / eventgridfilters | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/operationsstatus | Nie | Nie |
> | operacje | Nie | Nie |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | locations | Nie | Nie |
> | Lokalizacje/checknameavailability | Nie | Nie |
> | Lokalizacje/operationresults | Nie | Nie |
> | Lokalizacje/operationstatus | Nie | Nie |
> | operacje | Nie | Nie |
> | Spring | Tak | Tak |
> | sprężynowe/aplikacje | Nie | Nie |
> | sprężynowe/aplikacje/wdrożenia | Nie | Nie |

## <a name="microsoftappservice"></a>Microsoft. AppService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | apiapps | Nie | Nie |
> | appidentities | Nie | Nie |
> | bram | Nie | Nie |

> [!IMPORTANT]
> Zobacz [App Service wskazówki dotyczące przenoszenia](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftattestation"></a>Microsoft. zaświadczanie

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | attestationproviders | Tak | Tak |
> | operacje | Nie | Nie |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | CheckAccess | Nie | Nie |
> | classicadministrators | Nie | Nie |
> | aliasy | Nie | Nie |
> | denyassignments | Nie | Nie |
> | elevateaccess | Nie | Nie |
> | findorphanroleassignments | Nie | Nie |
> | Zamki | Nie | Nie |
> | operacje | Nie | Nie |
> | operationstatus | Nie | Nie |
> | uprawnienia | Nie | Nie |
> | policyassignments | Nie | Nie |
> | policydefinitions | Nie | Nie |
> | policysetdefinitions | Nie | Nie |
> | privatelinkassociations | Nie | Nie |
> | provideroperations | Nie | Nie |
> | resourcemanagementprivatelinks | Nie | Nie |
> | RoleAssignments | Nie | Nie |
> | roleassignmentsusagemetrics | Nie | Nie |
> | roledefinitions | Nie | Nie |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | automationaccounts | Tak | Tak |
> | automationaccounts/konfiguracje | Tak | Tak |
> | automationaccounts/zadania | Nie | Nie |
> | automationaccounts / privateendpointconnectionproxies | Nie | Nie |
> | automationaccounts / privateendpointconnections | Nie | Nie |
> | automationaccounts / privatelinkresources | Nie | Nie |
> | automationaccounts/elementy Runbook | Tak | Tak |
> | automationaccounts / softwareupdateconfigurations | Nie | Nie |
> | automationaccounts/elementy webhook | Nie | Nie |
> | operacje | Nie | Nie |

> [!IMPORTANT]
> Elementy Runbook muszą istnieć w tej samej grupie zasobów co konto usługi Automation.
>
> Aby uzyskać więcej informacji, zobacz [Przenoszenie konta Azure Automation do innej subskrypcji](../../automation/how-to/move-account.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftavs"></a>Microsoft. Automatyczna synchronizacja

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | locations | Nie | Nie |
> | Lokalizacje/checkquotaavailability | Nie | Nie |
> | Lokalizacje/checktrialavailability | Nie | Nie |
> | operacje | Nie | Nie |
> | privateclouds | Tak | Tak |
> | privateclouds/klastry | Nie | Nie |

## <a name="microsoftazureactivedirectory"></a>Microsoft. usługi azureactivedirectory

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Tak | Tak |
> | b2ctenants | Nie | Nie |
> | checknameavailability | Nie | Nie |
> | operacje | Nie | Nie |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Kontrolery datacontrollers | Nie | Nie |
> | hybriddatamanagers | Nie | Nie |
> | operacje | Nie | Nie |
> | postgresinstances | Nie | Nie |
> | wystąpienia sqlinstances | Nie | Nie |
> | sqlmanagedinstances | Nie | Nie |
> | sqlserverinstances | Nie | Nie |
> | sqlserverregistrations | Tak | Tak |
> | SqlServerRegistrations/SQLServers | Nie | Nie |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | cloudmanifestfiles | Nie | Nie |
> | operacje | Nie | Nie |
> | rejestracje | Tak | Tak |
> | rejestracje/customersubscriptions | Nie | Nie |
> | rejestracje/produkty | Nie | Nie |

## <a name="microsoftazurestackhci"></a>Microsoft. AzureStackHCI

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | oparty | Nie | Nie |
> | operacje | Nie | Nie |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Tak | Tak |
> | locations | Nie | Nie |
> | Lokalizacje/accountoperationresults | Nie | Nie |
> | Lokalizacje/checknameavailability | Nie | Nie |
> | Lokalizacje/przydziały | Nie | Nie |
> | operacje | Nie | Nie |

## <a name="microsoftbilling"></a>Microsoft. rozliczenia

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | billingaccounts | Nie | Nie |
> | billingaccounts/umowy | Nie | Nie |
> | billingaccounts / billingpermissions | Nie | Nie |
> | billingaccounts / billingprofiles | Nie | Nie |
> | billingaccounts / billingprofiles / availablebalance | Nie | Nie |
> | billingaccounts / billingprofiles / billingpermissions | Nie | Nie |
> | billingaccounts / billingprofiles / billingroleassignments | Nie | Nie |
> | billingaccounts / billingprofiles / billingroledefinitions | Nie | Nie |
> | billingaccounts / billingprofiles / billingsubscriptions | Nie | Nie |
> | billingaccounts / billingprofiles / createbillingroleassignment | Nie | Nie |
> | billingaccounts/billingprofiles/klienci | Nie | Nie |
> | billingaccounts/billingprofiles/instrukcje | Nie | Nie |
> | billingaccounts/billingprofiles/faktury | Nie | Nie |
> | billingaccounts/billingprofiles/faktury/arkusza cen | Nie | Nie |
> | billingaccounts/billingprofiles/faktury/transakcje | Nie | Nie |
> | billingaccounts / billingprofiles / invoicesections | Nie | Nie |
> | billingaccounts / billingprofiles / invoicesections / billingpermissions | Nie | Nie |
> | billingaccounts / billingprofiles / invoicesections / billingroleassignments | Nie | Nie |
> | billingaccounts / billingprofiles / invoicesections / billingroledefinitions | Nie | Nie |
> | billingaccounts / billingprofiles / invoicesections / billingsubscriptions | Nie | Nie |
> | billingaccounts / billingprofiles / invoicesections / createbillingroleassignment | Nie | Nie |
> | billingaccounts / billingprofiles / invoicesections / initiatetransfer | Nie | Nie |
> | billingaccounts/billingprofiles/invoicesections/produkty | Nie | Nie |
> | billingaccounts/billingprofiles/invoicesections/produkty/transfer | Nie | Nie |
> | billingaccounts/billingprofiles/invoicesections/produkty/updateautorenew | Nie | Nie |
> | billingaccounts/billingprofiles/invoicesections/Transactions | Nie | Nie |
> | billingaccounts/billingprofiles/invoicesections/transfery | Nie | Nie |
> | billingaccounts / billingprofiles / patchoperations | Nie | Nie |
> | billingaccounts / billingprofiles / paymentmethods | Nie | Nie |
> | billingaccounts/billingprofiles/zasady | Nie | Nie |
> | billingaccounts/billingprofiles/arkusza cen | Nie | Nie |
> | billingaccounts / billingprofiles / pricesheetdownloadoperations | Nie | Nie |
> | billingaccounts/billingprofiles/produkty | Nie | Nie |
> | billingaccounts/billingprofiles/Transactions | Nie | Nie |
> | billingaccounts / billingroleassignments | Nie | Nie |
> | billingaccounts / billingroledefinitions | Nie | Nie |
> | billingaccounts / billingsubscriptions | Nie | Nie |
> | billingaccounts/billingsubscriptions/faktury | Nie | Nie |
> | billingaccounts / createbillingroleassignment | Nie | Nie |
> | billingaccounts / createinvoicesectionoperations | Nie | Nie |
> | billingaccounts/klienci | Nie | Nie |
> | billingaccounts/klienci/billingpermissions | Nie | Nie |
> | billingaccounts/klienci/billingsubscriptions | Nie | Nie |
> | billingaccounts/klienci/initiatetransfer | Nie | Nie |
> | billingaccounts/klienci/zasady | Nie | Nie |
> | billingaccounts/klienci/produkty | Nie | Nie |
> | billingaccounts/klienci/transakcje | Nie | Nie |
> | billingaccounts/klienci/transfery | Nie | Nie |
> | billingaccounts/działy | Nie | Nie |
> | billingaccounts / enrollmentaccounts | Nie | Nie |
> | billingaccounts/faktury | Nie | Nie |
> | billingaccounts / invoicesections | Nie | Nie |
> | billingaccounts / invoicesections / billingsubscriptionmoveoperations | Nie | Nie |
> | billingaccounts / invoicesections / billingsubscriptions | Nie | Nie |
> | billingaccounts/invoicesections/billingsubscriptions/transfer | Nie | Nie |
> | billingaccounts/invoicesections/Podnieś poziom | Nie | Nie |
> | billingaccounts / invoicesections / initiatetransfer | Nie | Nie |
> | billingaccounts / invoicesections / patchoperations | Nie | Nie |
> | billingaccounts / invoicesections / productmoveoperations | Nie | Nie |
> | billingaccounts/invoicesections/produkty | Nie | Nie |
> | billingaccounts/invoicesections/produkty/transfer | Nie | Nie |
> | billingaccounts/invoicesections/produkty/updateautorenew | Nie | Nie |
> | billingaccounts / invoicesections / producttransfersresults | Nie | Nie |
> | billingaccounts/invoicesections/Transactions | Nie | Nie |
> | billingaccounts/invoicesections/transfery | Nie | Nie |
> | billingaccounts / lineofcredit | Nie | Nie |
> | billingaccounts / listinvoicesectionswithcreatesubscriptionpermission | Nie | Nie |
> | billingaccounts / operationresults | Nie | Nie |
> | billingaccounts / patchoperations | Nie | Nie |
> | billingaccounts / paymentmethods | Nie | Nie |
> | billingaccounts/produkty | Nie | Nie |
> | billingaccounts/transakcje | Nie | Nie |
> | billingperiods | Nie | Nie |
> | billingpermissions | Nie | Nie |
> | billingproperty | Nie | Nie |
> | billingroleassignments | Nie | Nie |
> | billingroledefinitions | Nie | Nie |
> | createbillingroleassignment | Nie | Nie |
> | działów | Nie | Nie |
> | enrollmentaccounts | Nie | Nie |
> | faktur | Nie | Nie |
> | operationresults | Nie | Nie |
> | operacje | Nie | Nie |
> | operationstatus | Nie | Nie |
> | sunięcia | Nie | Nie |
> | transfery/accepttransfer | Nie | Nie |
> | transfery/declinetransfer | Nie | Nie |
> | transfery/operationstatus | Nie | Nie |
> | transfery/validatetransfer | Nie | Nie |
> | validateaddress | Nie | Nie |

## <a name="microsoftbingmaps"></a>Microsoft. BingMaps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | listcommunicationpreference | Nie | Nie |
> | mapapis | Nie | Nie |
> | operacje | Nie | Nie |
> | updatecommunicationpreference | Nie | Nie |

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
> | locations | Nie | Nie |
> | Lokalizacje/blockchainmemberoperationresults | Nie | Nie |
> | Lokalizacje/checknameavailability | Nie | Nie |
> | Lokalizacje/listconsortiums | Nie | Nie |
> | Lokalizacje/watcheroperationresults | Nie | Nie |
> | operacje | Nie | Nie |
> | obserwatorów | Nie | Nie |

## <a name="microsoftblockchaintokens"></a>Microsoft. BlockchainTokens

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | operacje | Nie | Nie |
> | tokenservices | Nie | Nie |

## <a name="microsoftblueprint"></a>Microsoft. plan

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | Nie | Nie |
> | blueprintassignments / assignmentoperations | Nie | Nie |
> | blueprintassignments/operacje | Nie | Nie |
> | plany | Nie | Nie |
> | plany/artefakty | Nie | Nie |
> | plany/wersje | Nie | Nie |
> | plany/wersje/artefakty | Nie | Nie |
> | operacje | Nie | Nie |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | botservices | Tak | Tak |
> | botservices/kanały | Nie | Nie |
> | botservices/połączenia | Nie | Nie |
> | checknameavailability | Nie | Nie |
> | listauthserviceproviders | Nie | Nie |
> | operacje | Nie | Nie |

## <a name="microsoftcache"></a>Microsoft. cache

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/operationresults | Nie | Nie |
> | Lokalizacje/operationsstatus | Nie | Nie |
> | operacje | Nie | Nie |
> | Redis | Tak | Tak |
> | Redis/eventgridfilters | Nie | Nie |
> | Redis/privatelinkresources | Nie | Nie |
> | redisenterprise | Nie | Nie |

> [!IMPORTANT]
> Jeśli wystąpienie usługi Azure cache for Redis jest skonfigurowane przy użyciu sieci wirtualnej, nie można przenieść wystąpienia do innej subskrypcji. Zobacz [ograniczenia dotyczące przenoszenia sieci](./move-limitations/networking-move-limitations.md).

## <a name="microsoftcapacity"></a>Microsoft. Pojemność

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | appliedreservations | Nie | Nie |
> | calculateexchange | Nie | Nie |
> | calculateprice | Nie | Nie |
> | calculatepurchaseprice | Nie | Nie |
> | wykazy | Nie | Nie |
> | checkoffers | Nie | Nie |
> | checkpurchasestatus | Nie | Nie |
> | checkscopes | Nie | Nie |
> | commercialreservationorders | Nie | Nie |
> | zamian | Nie | Nie |
> | listbenefits | Nie | Nie |
> | operacje | Nie | Nie |
> | placepurchaseorder | Nie | Nie |
> | reservationorders | Nie | Nie |
> | reservationorders / availablescopes | Nie | Nie |
> | reservationorders / calculaterefund | Nie | Nie |
> | reservationorders/Scal | Nie | Nie |
> | reservationorders/rezerwacje | Nie | Nie |
> | reservationorders/rezerwacje/availablescopes | Nie | Nie |
> | reservationorders/rezerwacje/poprawki | Nie | Nie |
> | reservationorders/Return | Nie | Nie |
> | reservationorders/Split | Nie | Nie |
> | reservationorders/swap | Nie | Nie |
> | dokonując | Nie | Nie |
> | zasoby | Nie | Nie |
> | validatereservationorder | Nie | Nie |

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallmanagedrulesets | Nie | Nie |
> | cdnwebapplicationfirewallpolicies | Tak | Tak |
> | checknameavailability | Nie | Nie |
> | checkresourceusage | Nie | Nie |
> | edgenodes | Nie | Nie |
> | operationresults | Nie | Nie |
> | operationresults / profileresults | Nie | Nie |
> | operationresults / profileresults / endpointresults | Nie | Nie |
> | operationresults / profileresults / endpointresults / customdomainresults | Nie | Nie |
> | operationresults / profileresults / endpointresults / origingroupresults | Nie | Nie |
> | operationresults / profileresults / endpointresults / originresults | Nie | Nie |
> | operacje | Nie | Nie |
> | Profil | Tak | Tak |
> | Profile/punkty końcowe | Tak | Tak |
> | Profile/punkty końcowe/customdomains | Nie | Nie |
> | Profile/punkty końcowe/origingroups | Nie | Nie |
> | Profile/punkty końcowe/źródła | Nie | Nie |
> | validateprobe | Nie | Nie |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | certificateorders | Tak | Tak |
> | certificateorders/certyfikaty | Nie | Nie |
> | operacje | Nie | Nie |
> | validatecertificateregistrationinformation | Nie | Nie |

> [!IMPORTANT]
> Zobacz [App Service wskazówki dotyczące przenoszenia](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftchangeanalysis"></a>Microsoft. ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | operacje | Nie | Nie |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | możliwości | Nie | Nie |
> | checkdomainnameavailability | Nie | Nie |
> | domainnames | Yes | Nie |
> | DomainNames/możliwości | Nie | Nie |
> | domainnames / internalloadbalancers | Nie | Nie |
> | DomainNames/servicecertificates | Nie | Nie |
> | DomainNames/miejsca | Nie | Nie |
> | DomainNames/gniazda/role | Nie | Nie |
> | DomainNames/szczeliny/role/metricdefinitions | Nie | Nie |
> | DomainNames/szczeliny/role/metryki | Nie | Nie |
> | movesubscriptionresources | Nie | Nie |
> | operatingsystemfamilies | Nie | Nie |
> | operatingsystems | Nie | Nie |
> | operacje | Nie | Nie |
> | operationstatuses | Nie | Nie |
> | quotas | Nie | Nie |
> | resourcetypes | Nie | Nie |
> | validatesubscriptionmoveavailability | Nie | Nie |
> | virtualmachines | Tak | Nie |
> | virtualmachines/diagnosticsettings | Nie | Nie |
> | virtualmachines/metricdefinitions | Nie | Nie |
> | virtualmachines/metryki | Nie | Nie |

> [!IMPORTANT]
> Zobacz [klasyczne wskazówki dotyczące przenoszenia wdrożenia](./move-limitations/classic-model-move-limitations.md). Klasyczne zasoby wdrażania można przenosić między subskrypcjami przy użyciu operacji specyficznych dla tego scenariusza.

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft. ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | classicinfrastructureresources | Nie | Nie |

> [!IMPORTANT]
> Zobacz [klasyczne wskazówki dotyczące przenoszenia wdrożenia](./move-limitations/classic-model-move-limitations.md). Klasyczne zasoby wdrażania można przenosić między subskrypcjami przy użyciu operacji specyficznych dla tego scenariusza.

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | możliwości | Nie | Nie |
> | expressroutecrossconnections | Nie | Nie |
> | expressroutecrossconnections/Komunikacja równorzędna | Nie | Nie |
> | gatewaysupporteddevices | Nie | Nie |
> | networksecuritygroups | Nie | Nie |
> | operacje | Nie | Nie |
> | quotas | Nie | Nie |
> | reservedips | Nie | Nie |
> | virtualnetworks | Nie | Nie |
> | virtualnetworks/remotevirtualnetworkpeeringproxies | Nie | Nie |
> | virtualnetworks/virtualnetworkpeerings | Nie | Nie |

> [!IMPORTANT]
> Zobacz [klasyczne wskazówki dotyczące przenoszenia wdrożenia](./move-limitations/classic-model-move-limitations.md). Klasyczne zasoby wdrażania można przenosić między subskrypcjami przy użyciu operacji specyficznych dla tego scenariusza.

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | możliwości | Nie | Nie |
> | checkstorageaccountavailability | Nie | Nie |
> | dysku | Nie | Nie |
> | images | Nie | Nie |
> | operacje | Nie | Nie |
> | osimages | Nie | Nie |
> | osplatformimages | Nie | Nie |
> | publicimages | Nie | Nie |
> | quotas | Nie | Nie |
> | storageaccounts | Yes | Nie |
> | storageaccounts/blobservices | Nie | Nie |
> | storageaccounts/FileServices | Nie | Nie |
> | storageaccounts/metricdefinitions | Nie | Nie |
> | storageaccounts/metryki | Nie | Nie |
> | storageaccounts/queueservices | Nie | Nie |
> | storageaccounts/usługi | Nie | Nie |
> | storageaccounts/usługi/diagnosticsettings | Nie | Nie |
> | storageaccounts/usługi/metricdefinitions | Nie | Nie |
> | storageaccounts/usługi/metryki | Nie | Nie |
> | storageaccounts/tableservices | Nie | Nie |
> | storageaccounts/vmimages | Nie | Nie |
> | vmimages | Nie | Nie |

> [!IMPORTANT]
> Zobacz [klasyczne wskazówki dotyczące przenoszenia wdrożenia](./move-limitations/classic-model-move-limitations.md). Klasyczne zasoby wdrażania można przenosić między subskrypcjami przy użyciu operacji specyficznych dla tego scenariusza.

## <a name="microsoftclassicsubscription"></a>Microsoft. ClassicSubscription

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | operacje | Nie | Nie |

> [!IMPORTANT]
> Zobacz [klasyczne wskazówki dotyczące przenoszenia wdrożenia](./move-limitations/classic-model-move-limitations.md). Klasyczne zasoby wdrażania można przenosić między subskrypcjami przy użyciu operacji specyficznych dla tego scenariusza.

## <a name="microsoftcognitiveservices"></a>Microsoft. CognitiveServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Konta | Tak | Tak |
> | checkdomainavailability | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/checkskuavailability | Nie | Nie |
> | Lokalizacje/deletevirtualnetworkorsubnets | Nie | Nie |
> | Lokalizacje/operationresults | Nie | Nie |
> | operacje | Nie | Nie |

## <a name="microsoftcommerce"></a>Microsoft. Commerce

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | operacje | Nie | Nie |
> | ratecard | Nie | Nie |
> | usageaggregates | Nie | Nie |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | availabilitysets | Tak | Tak |
> | diskaccesses | Nie | Nie |
> | diskencryptionsets | Nie | Nie |
> | dysku | Tak | Tak |
> | znajduj | Nie | Nie |
> | Galerie/obrazy | Nie | Nie |
> | Galerie/obrazy/wersje | Nie | Nie |
> | hostgroups | Nie | Nie |
> | hostgroups/hosty | Nie | Nie |
> | images | Tak | Tak |
> | locations | Nie | Nie |
> | Lokalizacje/artifactpublishers | Nie | Nie |
> | Lokalizacje/capsoperations | Nie | Nie |
> | Lokalizacje/diskoperations | Nie | Nie |
> | Lokalizacje/loganalytics | Nie | Nie |
> | Lokalizacje/operacje | Nie | Nie |
> | Lokalizacje/wydawców | Nie | Nie |
> | Lokalizacje/runcommands | Nie | Nie |
> | Lokalizacje/użycia | Nie | Nie |
> | Lokalizacje/virtualmachines | Nie | Nie |
> | Lokalizacje/vmsizes | Nie | Nie |
> | Lokalizacje/vsmoperations | Nie | Nie |
> | operacje | Nie | Nie |
> | proximityplacementgroups | Tak | Tak |
> | restorepointcollections | Nie | Nie |
> | restorepointcollections / restorepoints | Nie | Nie |
> | sharedvmextensions | Nie | Nie |
> | sharedvmimages | Nie | Nie |
> | sharedvmimages/wersje | Nie | Nie |
> | Migawki | Tak | Tak |
> | sshpublickeys | Nie | Nie |
> | virtualmachines | Tak | Tak |
> | virtualmachines/rozszerzenia | Tak | Tak |
> | virtualmachines/metricdefinitions | Nie | Nie |
> | virtualmachines/runcommands | Nie | Nie |
> | virtualmachinescalesets | Tak | Tak |
> | virtualmachinescalesets/rozszerzenia | Nie | Nie |
> | virtualmachinescalesets/NetworkInterfaces | Nie | Nie |
> | virtualmachinescalesets/adresów publicipaddress | Nie | Nie |
> | virtualmachinescalesets/virtualmachines | Nie | Nie |
> | virtualmachinescalesets/virtualmachines/NetworkInterfaces | Nie | Nie |

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
> | operacje | Nie | Nie |
> | operationstatus | Nie | Nie |
> | pricesheets | Nie | Nie |
> | wyrobów | Nie | Nie |
> | reservationdetails | Nie | Nie |
> | reservationrecommendationdetails | Nie | Nie |
> | reservationrecommendations | Nie | Nie |
> | reservationsummaries | Nie | Nie |
> | reservationtransactions | Nie | Nie |
> | tags | Nie | Nie |
> | dzierżaw | Nie | Nie |
> | odsetk | Nie | Nie |
> | usagedetails | Nie | Nie |

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | containergroups | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/cachedimages | Nie | Nie |
> | Lokalizacje/możliwości | Nie | Nie |
> | Lokalizacje/deletevirtualnetworkorsubnets | Nie | Nie |
> | Lokalizacje/operacje | Nie | Nie |
> | Lokalizacje/użycia | Nie | Nie |
> | operacje | Nie | Nie |
> | serviceassociationlinks | Nie | Nie |

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/autoryzacja | Nie | Nie |
> | Lokalizacje/deletevirtualnetworkorsubnets | Nie | Nie |
> | Lokalizacje/operationresults | Nie | Nie |
> | Lokalizacje/setupauth | Nie | Nie |
> | operacje | Nie | Nie |
> | wołuje | Tak | Tak |
> | rejestry/agentpools | Tak | Tak |
> | rejestry/agentpools/listqueuestatus | Nie | Nie |
> | rejestry/kompilacje | Nie | Nie |
> | rejestry/kompilacje/anulowanie | Nie | Nie |
> | rejestry/kompilacje/getloglink | Nie | Nie |
> | rejestry/BuildTasks | Tak | Tak |
> | rejestry/BuildTasks/listsourcerepositoryproperties | Nie | Nie |
> | rejestry/BuildTasks/kroki | Nie | Nie |
> | rejestry/BuildTasks/etapy/listbuildarguments | Nie | Nie |
> | rejestry/eventgridfilters | Nie | Nie |
> | rejestry/exportpipelines | Nie | Nie |
> | rejestry/generatecredentials | Nie | Nie |
> | rejestry/getbuildsourceuploadurl | Nie | Nie |
> | rejestry/getpoświadczenia | Nie | Nie |
> | rejestry/importimage | Nie | Nie |
> | rejestry/importpipelines | Nie | Nie |
> | rejestry/listbuildsourceuploadurl | Nie | Nie |
> | rejestry/listcredentials | Nie | Nie |
> | rejestry/ListPolicies | Nie | Nie |
> | rejestry/listusages | Nie | Nie |
> | rejestry/pipelineruns | Nie | Nie |
> | rejestry/privateendpointconnectionproxies | Nie | Nie |
> | rejestry/privateendpointconnectionproxies/weryfikacja | Nie | Nie |
> | rejestry/privateendpointconnections | Nie | Nie |
> | rejestry/privatelinkresources | Nie | Nie |
> | rejestry/queuebuild | Nie | Nie |
> | rejestry/regeneratecredential | Nie | Nie |
> | rejestry/regeneratecredentials | Nie | Nie |
> | rejestry/replikacje | Tak | Tak |
> | rejestry/uruchomienia | Nie | Nie |
> | rejestry/uruchomienia/Anuluj | Nie | Nie |
> | rejestry/uruchomienia/listlogsasurl | Nie | Nie |
> | rejestry/schedulerun | Nie | Nie |
> | rejestry/scopemaps | Nie | Nie |
> | rejestry/taskruns | Nie | Nie |
> | rejestry/taskruns/ListDetails | Nie | Nie |
> | rejestry/zadania | Tak | Tak |
> | rejestry/zadania/ListDetails | Nie | Nie |
> | rejestry/tokeny | Nie | Nie |
> | rejestry/updatepolicies | Nie | Nie |
> | rejestry/elementy webhook | Tak | Tak |
> | rejestry/elementy webhook/getcallbackconfig | Nie | Nie |
> | rejestry/elementy webhook/ListEvents | Nie | Nie |
> | rejestry/elementy webhook/polecenie ping | Nie | Nie |

## <a name="microsoftcontainerservice"></a>Microsoft. ContainerService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | containerservices | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/openshiftclusters | Nie | Nie |
> | Lokalizacje/operationresults | Nie | Nie |
> | Lokalizacje/operacje | Nie | Nie |
> | Lokalizacje/koordynatorzy | Nie | Nie |
> | managedclusters | Nie | Nie |
> | openshiftmanagedclusters | Nie | Nie |
> | operacje | Nie | Nie |

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
> | billingaccounts | Nie | Nie |
> | budżetów | Nie | Nie |
> | cloudconnectors | Nie | Nie |
> | konta integracji | Tak | Tak |
> | działów | Nie | Nie |
> | Wymiary | Nie | Nie |
> | enrollmentaccounts | Nie | Nie |
> | Eksportowanie | Nie | Nie |
> | externalbillingaccounts | Nie | Nie |
> | externalbillingaccounts/alerty | Nie | Nie |
> | externalbillingaccounts/wymiary | Nie | Nie |
> | externalbillingaccounts/Prognoza | Nie | Nie |
> | externalbillingaccounts/zapytanie | Nie | Nie |
> | externalsubscriptions | Nie | Nie |
> | externalsubscriptions/alerty | Nie | Nie |
> | externalsubscriptions/wymiary | Nie | Nie |
> | externalsubscriptions/Prognoza | Nie | Nie |
> | externalsubscriptions/zapytanie | Nie | Nie |
> | forecast | Nie | Nie |
> | operacje | Nie | Nie |
> | query | Nie | Nie |
> | zarejestruj | Nie | Nie |
> | reportconfigs | Nie | Nie |
> | raporty | Nie | Nie |
> | ustawienia | Nie | Nie |
> | showbackrules | Nie | Nie |
> | widoki | Nie | Nie |

## <a name="microsoftcostmanagementexports"></a>Microsoft. CostManagementExports

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | operacje | Nie | Nie |

## <a name="microsoftcustomerinsights"></a>Microsoft. CustomerInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | koncentratory, | Nie | Nie |

## <a name="microsoftcustomerlockbox"></a>Microsoft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | operacje | Nie | Nie |
> | żądań | Nie | Nie |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | związku | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/operationstatuses | Nie | Nie |
> | operacje | Nie | Nie |
> | resourceproviders | Tak | Tak |

## <a name="microsoftdatabox"></a>Microsoft. DataBox

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | zadania | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/availableskus | Nie | Nie |
> | Lokalizacje/checknameavailability | Nie | Nie |
> | Lokalizacje/operationresults | Nie | Nie |
> | Lokalizacje/regionconfiguration | Nie | Nie |
> | Lokalizacje/ValidateAddress | Nie | Nie |
> | Lokalizacje/validateinputs | Nie | Nie |
> | operacje | Nie | Nie |

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | availableskus | Nie | Nie |
> | databoxedgedevices | Tak | Tak |
> | databoxedgedevices / checknameavailability | Nie | Nie |
> | operacje | Nie | Nie |

## <a name="microsoftdatabricks"></a>Microsoft. datacegły

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | locations | Nie | Nie |
> | Lokalizacje/getnetworkpolicies | Nie | Nie |
> | Lokalizacje/operationstatuses | Nie | Nie |
> | operacje | Nie | Nie |
> | obszary robocze | Nie | Nie |
> | obszary robocze/dbworkspaces | Nie | Nie |
> | obszary robocze/virtualnetworkpeerings | Nie | Nie |

## <a name="microsoftdatacatalog"></a>Microsoft. datacatalog

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | wykazy | Tak | Tak |
> | checknameavailability | Nie | Nie |
> | wykazy | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/zadania | Nie | Nie |
> | Lokalizacje/operationresults | Nie | Nie |
> | operacje | Nie | Nie |

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
> | checkazuredatafactorynameavailability | Nie | Nie |
> | checkdatafactorynameavailability | Nie | Nie |
> | fabryki datafactors | Tak | Tak |
> | datafactors/diagnosticsettings | Nie | Nie |
> | datafactors/metricdefinitions | Nie | Nie |
> | datafactoryschema | Nie | Nie |
> | fabryki | Tak | Tak |
> | fabryki/integrationruntimes | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/configurefactoryrepo | Nie | Nie |
> | Lokalizacje/getfeaturevalue | Nie | Nie |
> | operacje | Nie | Nie |

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
> | konta/datalakestoreaccounts | Nie | Nie |
> | konta/storageaccounts | Nie | Nie |
> | konta/storageaccounts/kontenery | Nie | Nie |
> | konta/storageaccounts/kontenery/listsastokens | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/możliwości | Nie | Nie |
> | Lokalizacje/checknameavailability | Nie | Nie |
> | Lokalizacje/operationresults | Nie | Nie |
> | Lokalizacje/użycia | Nie | Nie |
> | operacje | Nie | Nie |

## <a name="microsoftdatalakestore"></a>Microsoft. kontach datalakestore

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Konta | Tak | Tak |
> | konta/eventgridfilters | Nie | Nie |
> | konta/firewallrules | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/możliwości | Nie | Nie |
> | Lokalizacje/checknameavailability | Nie | Nie |
> | Lokalizacje/deletevirtualnetworkorsubnets | Nie | Nie |
> | Lokalizacje/operationresults | Nie | Nie |
> | Lokalizacje/użycia | Nie | Nie |
> | operacje | Nie | Nie |

## <a name="microsoftdatamigration"></a>Migracja Microsoft.

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | locations | Nie | Nie |
> | Lokalizacje/checknameavailability | Nie | Nie |
> | Lokalizacje/operationresults | Nie | Nie |
> | Lokalizacje/operationstatuses | Nie | Nie |
> | operacje | Nie | Nie |
> | services | Nie | Nie |
> | usługi/projekty | Nie | Nie |
> | czasów | Nie | Nie |

## <a name="microsoftdataprotection"></a>Microsoft. dataprotection

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | backupvaults | Nie | Nie |
> | locations | Nie | Nie |
> | operacje | Nie | Nie |

## <a name="microsoftdatashare"></a>Microsoft. dataudział

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Konta | Tak | Tak |
> | konta/udziały | Nie | Nie |
> | konta/udziały/zestawy danych | Nie | Nie |
> | konta/udziały/zaproszenia | Nie | Nie |
> | konta/udziały/providersharesubscriptions | Nie | Nie |
> | konta/udziały/synchronizationsettings | Nie | Nie |
> | konta/sharesubscriptions | Nie | Nie |
> | konta/sharesubscriptions/consumersourcedatasets | Nie | Nie |
> | konta/sharesubscriptions/datasetmappings | Nie | Nie |
> | konta/sharesubscriptions/wyzwalacze | Nie | Nie |
> | listinvitations | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/consumerinvitations | Nie | Nie |
> | Lokalizacje/operationresults | Nie | Nie |
> | Lokalizacje/rejectinvitation | Nie | Nie |
> | operacje | Nie | Nie |

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/azureasyncoperation | Nie | Nie |
> | Lokalizacje/operationresults | Nie | Nie |
> | Lokalizacje/performancetiers | Nie | Nie |
> | Lokalizacje/privateendpointconnectionazureasyncoperation | Nie | Nie |
> | Lokalizacje/privateendpointconnectionoperationresults | Nie | Nie |
> | Lokalizacje/privateendpointconnectionproxyazureasyncoperation | Nie | Nie |
> | Lokalizacje/privateendpointconnectionproxyoperationresults | Nie | Nie |
> | Lokalizacje/recommendedactionsessionsazureasyncoperation | Nie | Nie |
> | Lokalizacje/recommendedactionsessionsoperationresults | Nie | Nie |
> | Lokalizacje/securityalertpoliciesazureasyncoperation | Nie | Nie |
> | Lokalizacje/securityalertpoliciesoperationresults | Nie | Nie |
> | Lokalizacje/serverkeyazureasyncoperation | Nie | Nie |
> | Lokalizacje/serverkeyoperationresults | Nie | Nie |
> | operacje | Nie | Nie |
> | serwerem | Tak | Tak |
> | serwery/doradcy | Nie | Nie |
> | serwery/privateendpointconnectionproxies | Nie | Nie |
> | serwery/privateendpointconnections | Nie | Nie |
> | serwery/privatelinkresources | Nie | Nie |
> | serwery/querytexts | Nie | Nie |
> | serwery/recoverableservers | Nie | Nie |
> | serwery/topquerystatistics | Nie | Nie |
> | serwery/virtualnetworkrules | Nie | Nie |
> | serwery/waitstatistics | Nie | Nie |

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/administratorazureasyncoperation | Nie | Nie |
> | Lokalizacje/administratoroperationresults | Nie | Nie |
> | Lokalizacje/azureasyncoperation | Nie | Nie |
> | Lokalizacje/operationresults | Nie | Nie |
> | Lokalizacje/performancetiers | Nie | Nie |
> | Lokalizacje/privateendpointconnectionazureasyncoperation | Nie | Nie |
> | Lokalizacje/privateendpointconnectionoperationresults | Nie | Nie |
> | Lokalizacje/privateendpointconnectionproxyazureasyncoperation | Nie | Nie |
> | Lokalizacje/privateendpointconnectionproxyoperationresults | Nie | Nie |
> | Lokalizacje/recommendedactionsessionsazureasyncoperation | Nie | Nie |
> | Lokalizacje/recommendedactionsessionsoperationresults | Nie | Nie |
> | Lokalizacje/securityalertpoliciesazureasyncoperation | Nie | Nie |
> | Lokalizacje/securityalertpoliciesoperationresults | Nie | Nie |
> | Lokalizacje/serverkeyazureasyncoperation | Nie | Nie |
> | Lokalizacje/serverkeyoperationresults | Nie | Nie |
> | operacje | Nie | Nie |
> | serwerem | Tak | Tak |
> | serwery/doradcy | Nie | Nie |
> | serwery/klucze | Nie | Nie |
> | serwery/privateendpointconnectionproxies | Nie | Nie |
> | serwery/privateendpointconnections | Nie | Nie |
> | serwery/privatelinkresources | Nie | Nie |
> | serwery/querytexts | Nie | Nie |
> | serwery/recoverableservers | Nie | Nie |
> | serwery/topquerystatistics | Nie | Nie |
> | serwery/virtualnetworkrules | Nie | Nie |
> | serwery/waitstatistics | Nie | Nie |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/administratorazureasyncoperation | Nie | Nie |
> | Lokalizacje/administratoroperationresults | Nie | Nie |
> | Lokalizacje/azureasyncoperation | Nie | Nie |
> | Lokalizacje/operationresults | Nie | Nie |
> | Lokalizacje/performancetiers | Nie | Nie |
> | Lokalizacje/privateendpointconnectionazureasyncoperation | Nie | Nie |
> | Lokalizacje/privateendpointconnectionoperationresults | Nie | Nie |
> | Lokalizacje/privateendpointconnectionproxyazureasyncoperation | Nie | Nie |
> | Lokalizacje/privateendpointconnectionproxyoperationresults | Nie | Nie |
> | Lokalizacje/recommendedactionsessionsazureasyncoperation | Nie | Nie |
> | Lokalizacje/recommendedactionsessionsoperationresults | Nie | Nie |
> | Lokalizacje/securityalertpoliciesazureasyncoperation | Nie | Nie |
> | Lokalizacje/securityalertpoliciesoperationresults | Nie | Nie |
> | Lokalizacje/serverkeyazureasyncoperation | Nie | Nie |
> | Lokalizacje/serverkeyoperationresults | Nie | Nie |
> | operacje | Nie | Nie |
> | servergroups | Nie | Nie |
> | serwerem | Tak | Tak |
> | serwery/doradcy | Nie | Nie |
> | serwery/klucze | Nie | Nie |
> | serwery/privateendpointconnectionproxies | Nie | Nie |
> | serwery/privateendpointconnections | Nie | Nie |
> | serwery/privatelinkresources | Nie | Nie |
> | serwery/querytexts | Nie | Nie |
> | serwery/recoverableservers | Nie | Nie |
> | serwery/topquerystatistics | Nie | Nie |
> | serwery/virtualnetworkrules | Nie | Nie |
> | serwery/waitstatistics | Nie | Nie |
> | serversv2 | Tak | Tak |
> | singleservers | Tak | Tak |

## <a name="microsoftdeploymentmanager"></a>Microsoft. Deploymentmanager

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | artifactsources | Tak | Tak |
> | operationresults | Nie | Nie |
> | operacje | Nie | Nie |
> | wprowadzanie | Tak | Tak |
> | servicetopologie | Tak | Tak |
> | servicetopologie/usługi | Tak | Tak |
> | servicetopologie/usługi/serviceunits | Tak | Tak |
> | kroki | Tak | Tak |

## <a name="microsoftdesktopvirtualization"></a>Microsoft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | applicationgroups | Tak | Tak |
> | applicationgroups/aplikacje | Nie | Nie |
> | applicationgroups/komputery stacjonarne | Nie | Nie |
> | applicationgroups / startmenuitems | Nie | Nie |
> | hostpools | Tak | Tak |
> | hostpools / sessionhosts | Nie | Nie |
> | hostpools / sessionhosts / usersessions | Nie | Nie |
> | hostpools / usersessions | Nie | Nie |
> | operacje | Nie | Nie |
> | obszary robocze | Tak | Tak |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nie | Nie |
> | checkprovisioningservicenameavailability | Nie | Nie |
> | elasticpools | Nie | Nie |
> | elasticpools / iothubtenants | Nie | Nie |
> | iothubs | Tak | Tak |
> | iothubs/eventgridfilters | Nie | Nie |
> | iothubs/SECURITYSETTINGS | Nie | Nie |
> | operationresults | Nie | Nie |
> | operacje | Nie | Nie |
> | provisioningservices | Tak | Tak |
> | użycia | Nie | Nie |

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
> | Kontrolery/listconnectiondetails | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/checkcontainerhostmapping | Nie | Nie |
> | Lokalizacje/operationresults | Nie | Nie |
> | operacje | Nie | Nie |

## <a name="microsoftdevtestlab"></a>Microsoft. wspólny

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | labcenters | Nie | Nie |
> | Labs | Yes | Nie |
> | laboratoria/środowiska | Tak | Tak |
> | laboratoria/servicerunner | Tak | Tak |
> | Labs/virtualmachines | Yes | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/operacje | Nie | Nie |
> | operacje | Nie | Nie |
> | Uruchamianie | Tak | Tak |

## <a name="microsoftdigitaltwins"></a>Microsoft. DigitalTwins

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | Nie | Nie |
> | digitaltwinsinstances / operationresults | Nie | Nie |
> | locations | Nie | Nie |
> | operacje | Nie | Nie |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | databaseaccountnames | Nie | Nie |
> | databaseaccounts | Tak | Tak |
> | locations | Nie | Nie |
> | Lokalizacje/deletevirtualnetworkorsubnets | Nie | Nie |
> | Lokalizacje/operationresults | Nie | Nie |
> | Lokalizacje/operationsstatus | Nie | Nie |
> | operationresults | Nie | Nie |
> | operacje | Nie | Nie |

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | checkdomainavailability | Nie | Nie |
> | 3.x | Tak | Tak |
> | domeny/domainownershipidentifiers | Nie | Nie |
> | generatessorequest | Nie | Nie |
> | listdomainrecommendations | Nie | Nie |
> | operacje | Nie | Nie |
> | topleveldomains | Nie | Nie |
> | validatedomainregistrationinformation | Nie | Nie |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | locations | Nie | Nie |
> | Lokalizacje/operationresults | Nie | Nie |
> | operacje | Nie | Nie |
> | services | Tak | Tak |

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | 3.x | Tak | Tak |
> | domeny/tematy | Nie | Nie |
> | eventsubscriptions | Nie — nie można przenieść niezależnie, ale automatycznie przeniesiono z subskrybowanym zasobem. | Nie — nie można przenieść niezależnie, ale automatycznie przeniesiono z subskrybowanym zasobem. |
> | extensiontopics | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/eventsubscriptions | Nie | Nie |
> | Lokalizacje/operationresults | Nie | Nie |
> | Lokalizacje/operationsstatus | Nie | Nie |
> | Lokalizacje/topictypes | Nie | Nie |
> | operationresults | Nie | Nie |
> | operacje | Nie | Nie |
> | operationsstatus | Nie | Nie |
> | partnernamespaces | Tak | Tak |
> | partnernamespaces/eventchannels | Nie | Nie |
> | partnerregistrations | Nie | Nie |
> | partnertopics | Tak | Tak |
> | partnertopics / eventsubscriptions | Nie | Nie |
> | systemtopics | Tak | Tak |
> | systemtopics / eventsubscriptions | Nie | Nie |
> | opisano | Tak | Tak |
> | topictypes | Nie | Nie |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | availableclusterregions | Nie | Nie |
> | checknameavailability | Nie | Nie |
> | checknamespaceavailability | Nie | Nie |
> | oparty | Tak | Tak |
> | locations | Nie | Nie |
> | Lokalizacje/deletevirtualnetworkorsubnets | Nie | Nie |
> | przestrzeni | Tak | Tak |
> | przestrzenie nazw/reguł autoryzacji | Nie | Nie |
> | przestrzenie nazw/disasterrecoveryconfigs | Nie | Nie |
> | przestrzenie nazw/disasterrecoveryconfigs/checknameavailability | Nie | Nie |
> | przestrzenie nazw/eventhubs | Nie | Nie |
> | przestrzenie nazw/eventhubs/reguł autoryzacji | Nie | Nie |
> | przestrzenie nazw/eventhubs/consumergroups | Nie | Nie |
> | przestrzenie nazw/networkrulesets | Nie | Nie |
> | operacje | Nie | Nie |
> | sku | Nie | Nie |

## <a name="microsoftexperimentation"></a>Microsoft. eksperymentowanie

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | experimentworkspaces | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/operacje | Nie | Nie |

## <a name="microsoftfalcon"></a>Microsoft. Falcon

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | przestrzeni | Tak | Tak |

## <a name="microsoftfeatures"></a>Microsoft. Features

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | featureproviders | Nie | Nie |
> | funkcje | Nie | Nie |
> | operacje | Nie | Nie |
> | dostawców | Nie | Nie |
> | subscriptionfeatureregistrations | Nie | Nie |

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
> | operacje | Nie | Nie |
> | programy | Nie | Nie |
> | softwareupdateprofile | Nie | Nie |
> | softwareupdates | Nie | Nie |

## <a name="microsofthanaonazure"></a>Microsoft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | hanainstances | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/operacje | Nie | Nie |
> | Lokalizacje/operationsstatus | Nie | Nie |
> | operacje | Nie | Nie |
> | sapmonitors | Tak | Tak |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft. HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | modułów dedicatedhsms | Nie | Nie |
> | locations | Nie | Nie |
> | operacje | Nie | Nie |

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | oparty | Tak | Tak |
> | klastry/aplikacje | Nie | Nie |
> | klastry/operationresults | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/azureasyncoperations | Nie | Nie |
> | Lokalizacje/billingspecs | Nie | Nie |
> | Lokalizacje/możliwości | Nie | Nie |
> | Lokalizacje/operationresults | Nie | Nie |
> | Lokalizacje/użycia | Nie | Nie |
> | Lokalizacje/validatecreaterequest | Nie | Nie |
> | operacje | Nie | Nie |

> [!IMPORTANT]
> Klastry usługi HDInsight można przenosić do nowej subskrypcji lub grupy zasobów. Nie jest jednak możliwe przechodzenie między subskrypcjami zasobów sieciowych połączonych z klastrem usługi HDInsight (takich jak sieć wirtualna, karta sieciowa lub moduł równoważenia obciążenia). Ponadto nie można przenieść do nowej grupy zasobów karty sieciowej podłączonej do maszyny wirtualnej w klastrze.
>
> Podczas przenoszenia klastra usługi HDInsight do nowej subskrypcji najpierw Przenieś inne zasoby (takie jak konto magazynu). Następnie należy przenieść klaster usługi HDInsight do samego siebie.

## <a name="microsofthealthcareapis"></a>Microsoft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/operationresults | Nie | Nie |
> | operacje | Nie | Nie |
> | services | Tak | Tak |
> | usługi/privateendpointconnections | Nie | Nie |
> | usługi/privatelinkresources | Nie | Nie |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | locations | Nie | Nie |
> | Lokalizacje/operationresults | Nie | Nie |
> | Lokalizacje/operationstatus | Nie | Nie |
> | Win64 | Tak | Tak |
> | Maszyny/rozszerzenia | Tak | Tak |
> | operacje | Nie | Nie |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | datamanagers | Tak | Tak |
> | operacje | Nie | Nie |

## <a name="microsofthybridnetwork"></a>Microsoft. HybridNetwork

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | devices | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/operationstatuses | Nie | Nie |
> | operacje | Nie | Nie |
> | vnfs | Nie | Nie |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | komponentów | Nie | Nie |
> | locations | Nie | Nie |
> | networkscopes | Nie | Nie |
> | operacje | Nie | Nie |

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | zadania | Tak | Tak |
> | locations | Nie | Nie |
> | Lokalizacje/operationresults | Nie | Nie |
> | operacje | Nie | Nie |

## <a name="microsoftinsights"></a>Microsoft. Insights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | actiongroups | Tak | Tak |
> | activitylogalerts | Nie | Nie |
> | alertrules | Tak | Tak |
> | autoscalesettings | Tak | Tak |
> | punkt odniesienia | Nie | Nie |
> | calculatebaseline | Nie | Nie |
> | komponentów | Tak | Tak |
> | składniki/zdarzenia | Nie | Nie |
> | składniki/linkedstorageaccounts | Nie | Nie |
> | składniki/metadane | Nie | Nie |
> | składniki/metryki | Nie | Nie |
> | składniki/pricingplans | Nie | Nie |
> | składniki/zapytanie | Nie | Nie |
> | datacollectionrules | Nie | Nie |
> | diagnosticsettings | Nie | Nie |
> | diagnosticsettingscategories | Nie | Nie |
> | eventcategories | Nie | Nie |
> | Typ zdarzenia | Nie | Nie |
> | extendeddiagnosticsettings | Nie | Nie |
> | guestdiagnosticsettings | Nie | Nie |
> | listmigrationdate | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/operationresults | Nie | Nie |
> | logdefinitions | Nie | Nie |
> | logprofiles | Nie | Nie |
> | dzienniki | Nie | Nie |
> | metricalerts | Nie | Nie |
> | metricbaselines | Nie | Nie |
> | metricbatch | Nie | Nie |
> | metricdefinitions | Nie | Nie |
> | metricnamespaces | Nie | Nie |
> | metrics | Nie | Nie |
> | migratealertrules | Nie | Nie |
> | migratetonewpricingmodel | Nie | Nie |
> | Moje skoroszyty | Nie | Nie |
> | notificationgroups | Nie | Nie |
> | operacje | Nie | Nie |
> | privatelinkscopeoperationstatuses | Nie | Nie |
> | privatelinkscopes | Nie | Nie |
> | privatelinkscopes / privateendpointconnectionproxies | Nie | Nie |
> | privatelinkscopes / privateendpointconnections | Nie | Nie |
> | privatelinkscopes / scopedresources | Nie | Nie |
> | rollbacktolegacypricingmodel | Nie | Nie |
> | scheduledqueryrules | Tak | Tak |
> | topology | Nie | Nie |
> | transakcje | Nie | Nie |
> | vminsightsonboardingstatuses | Nie | Nie |
> | testów internetowych | Tak | Tak |
> | webtests/gettestresultfile | Nie | Nie |
> | skoroszyty | Tak | Tak |
> | workbooktemplates | Tak | Tak |

> [!IMPORTANT]
> Upewnij się, że przejście do nowej subskrypcji nie przekracza [przydziałów subskrypcji](azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | apptemplates | Nie | Nie |
> | checknameavailability | Nie | Nie |
> | checksubdomainavailability | Nie | Nie |
> | iotapps | Tak | Tak |
> | operacje | Nie | Nie |

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Tak | Tak |
> | ziół | Tak | Tak |
> | operacje | Nie | Nie |

## <a name="microsoftkeyvault"></a>Microsoft. kluczy — magazyn

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nie | Nie |
> | deletedvaults | Nie | Nie |
> | hsmpools | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/deletedvaults | Nie | Nie |
> | Lokalizacje/deletevirtualnetworkorsubnets | Nie | Nie |
> | Lokalizacje/operationresults | Nie | Nie |
> | managedhsms | Nie | Nie |
> | operacje | Nie | Nie |
> | magazynów | Tak | Tak |
> | magazyny/accesspolicies | Nie | Nie |
> | magazyny/eventgridfilters | Nie | Nie |
> | magazyny/wpisy tajne | Nie | Nie |

> [!IMPORTANT]
> Magazyny kluczy używane do szyfrowania dysków nie mogą zostać przeniesione do grupy zasobów w tej samej subskrypcji lub w różnych subskrypcjach.

## <a name="microsoftkubernetes"></a>Microsoft. Kubernetes

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | connectedclusters | Tak | Tak |
> | locations | Nie | Nie |
> | Lokalizacje/operationstatuses | Nie | Nie |
> | operacje | Nie | Nie |
> | registeredsubscriptions | Nie | Nie |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft. KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | sourcecontrolconfigurations | Nie | Nie |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | oparty | Tak | Tak |
> | klastry/attacheddatabaseconfigurations | Nie | Nie |
> | klastry/bazy danych | Nie | Nie |
> | klastry/bazy danych/połączenia Databases | Nie | Nie |
> | klastry/bazy danych/eventhubconnections | Nie | Nie |
> | klastry/bazy danych/principalassignments | Nie | Nie |
> | klastry/principalassignments | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/checknameavailability | Nie | Nie |
> | Lokalizacje/operationresults | Nie | Nie |
> | operacje | Nie | Nie |

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | labaccounts | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/operacje | Nie | Nie |
> | operacje | Nie | Nie |
> | users | Nie | Nie |

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
> | locations | Nie | Nie |
> | Lokalizacje/przepływy pracy | Nie | Nie |
> | operacje | Nie | Nie |
> | przebieg | Tak | Tak |

## <a name="microsoftmachinelearning"></a>Microsoft. MachineLearning

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | commitmentplans | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/operacje | Nie | Nie |
> | Lokalizacje/operationsstatus | Nie | Nie |
> | operacje | Nie | Nie |
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

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | locations | Nie | Nie |
> | Lokalizacje/computeoperationsstatus | Nie | Nie |
> | Lokalizacje/przydziały | Nie | Nie |
> | Lokalizacje/updatequotas | Nie | Nie |
> | Lokalizacje/użycia | Nie | Nie |
> | Lokalizacje/vmsizes | Nie | Nie |
> | Lokalizacje/workspaceoperationsstatus | Nie | Nie |
> | operacje | Nie | Nie |
> | obszary robocze | Nie | Nie |
> | obszary robocze/obliczenia | Nie | Nie |
> | obszary robocze/eventgridfilters | Nie | Nie |

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
> | operacje | Nie | Nie |
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
> | marketplaceregistrationdefinitions | Nie | Nie |
> | operacje | Nie | Nie |
> | operationstatuses | Nie | Nie |
> | registrationassignments | Nie | Nie |
> | registrationdefinitions | Nie | Nie |

## <a name="microsoftmanagement"></a>Microsoft. Management

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nie | Nie |
> | getentities | Nie | Nie |
> | managementgroups | Nie | Nie |
> | managementgroups/ustawienia | Nie | Nie |
> | operationresults | Nie | Nie |
> | operationresults / asyncoperation | Nie | Nie |
> | operacje | Nie | Nie |
> | zasoby | Nie | Nie |
> | starttenantbackfill | Nie | Nie |
> | tenantbackfillstatus | Nie | Nie |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Konta | Tak | Tak |
> | konta/eventgridfilters | Nie | Nie |
> | konta/privateatlases | Tak | Tak |
> | operacje | Nie | Nie |

## <a name="microsoftmarketplace"></a>Microsoft. Marketplace

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | listavailableoffers | Nie | Nie |
> | otrzymane | Nie | Nie |
> | offertypes | Nie | Nie |
> | offertypes/wydawcy | Nie | Nie |
> | offertypes/wydawcy/oferty | Nie | Nie |
> | offertypes/wydawcy/oferty/plany | Nie | Nie |
> | offertypes/wydawcy/oferty/plany/umowy | Nie | Nie |
> | offertypes/wydawcy/oferty/plany/konfiguracje | Nie | Nie |
> | offertypes/wydawcy/oferty/plany/konfiguracje/importimage | Nie | Nie |
> | operacje | Nie | Nie |
> | privategalleryitems | Nie | Nie |
> | privatestoreclient | Nie | Nie |
> | privatestores | Nie | Nie |
> | privatestores/oferty | Nie | Nie |
> | wyrobów | Nie | Nie |
> | wydawcy | Nie | Nie |
> | wydawcy/oferty | Nie | Nie |
> | wydawcy/oferty/zmiany | Nie | Nie |
> | zarejestruj | Nie | Nie |

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Nie | Nie |
> | listcommunicationpreference | Nie | Nie |
> | operacje | Nie | Nie |
> | updatecommunicationpreference | Nie | Nie |

## <a name="microsoftmarketplaceordering"></a>Microsoft. MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | kontrakt | Nie | Nie |
> | offertypes | Nie | Nie |
> | operacje | Nie | Nie |

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/checknameavailability | Nie | Nie |
> | MediaServices | Tak | Tak |
> | MediaServices/accountfilters | Nie | Nie |
> | MediaServices/zasoby | Nie | Nie |
> | MediaServices/assetfilters | Nie | Nie |
> | MediaServices/contentkeypolicies | Nie | Nie |
> | MediaServices/eventgridfilters | Nie | Nie |
> | MediaServices/liveeventoperations | Nie | Nie |
> | MediaServices/liveevents | Tak | Tak |
> | MediaServices/liveevents/liveoutputs | Nie | Nie |
> | MediaServices/liveoutputoperations | Nie | Nie |
> | MediaServices/streamingendpointoperations | Nie | Nie |
> | MediaServices/streamingendpoints | Tak | Tak |
> | MediaServices/streaminglocators | Nie | Nie |
> | MediaServices/streamingpolicies | Nie | Nie |
> | MediaServices/transformacje | Nie | Nie |
> | MediaServices/transformacje/zadania | Nie | Nie |
> | operacje | Nie | Nie |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | appclusters | Nie | Nie |

## <a name="microsoftmigrate"></a>Microsoft. Migrowanie

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/assessmentoptions | Nie | Nie |
> | Lokalizacje/checknameavailability | Nie | Nie |
> | migrateprojects | Nie | Nie |
> | movecollections | Nie | Nie |
> | operacje | Nie | Nie |
> | projektami | Nie | Nie |

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | holographicsbroadcastaccounts | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/checknameavailability | Nie | Nie |
> | objectunderstandingaccounts | Nie | Nie |
> | operacje | Nie | Nie |
> | remoterenderingaccounts | Tak | Tak |
> | spatialanchorsaccounts | Tak | Tak |

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
> | operacje | Nie | Nie |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | applicationgatewayavailablerequestheaders | Nie | Nie |
> | applicationgatewayavailableresponseheaders | Nie | Nie |
> | applicationgatewayavailableservervariables | Nie | Nie |
> | applicationgatewayavailablessloptions | Nie | Nie |
> | applicationgatewayavailablewafrulesets | Nie | Nie |
> | applicationgateways | Nie | Nie |
> | applicationgatewaywebapplicationfirewallpolicies | Nie | Nie |
> | applicationsecuritygroups | Tak | Tak |
> | azurefirewallfqdntags | Nie | Nie |
> | azurefirewalls | Nie | Nie |
> | bastionhosts | Nie | Nie |
> | bgpservicecommunities | Nie | Nie |
> | checkfrontdoornameavailability | Nie | Nie |
> | checktrafficmanagernameavailability | Nie | Nie |
> | Licznik | Tak | Tak |
> | ddoscustompolicies | Tak | Tak |
> | ddosprotectionplans | Nie | Nie |
> | dnsoperationresults | Nie | Nie |
> | dnsoperationstatuses | Nie | Nie |
> | dnszones | Tak | Tak |
> | dnszones/a | Nie | Nie |
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
> | dnszones/txt | Nie | Nie |
> | expressroutecircuits | Nie | Nie |
> | expressroutegateways | Nie | Nie |
> | expressrouteserviceproviders | Nie | Nie |
> | firewallpolicies | Tak | Tak |
> | frontdooroperationresults | Nie | Nie |
> | usługi frontdoor | Nie | Nie |
> | usługi frontdoor/frontendendpoints | Nie | Nie |
> | frontdoorwebapplicationfirewallmanagedrulesets | Nie | Nie |
> | frontdoorwebapplicationfirewallpolicies | Nie | Nie |
> | getdnsresourcereference | Nie | Nie |
> | internalnotify | Nie | Nie |
> | ipallocations | Tak | Tak |
> | ipgroups | Tak | Tak |
> | loadbalancers | Tak — podstawowa jednostka SKU<br>Niestandardowa jednostka SKU | Tak — podstawowa jednostka SKU<br>Niestandardowa jednostka SKU |
> | localnetworkgateways | Tak | Tak |
> | locations | Nie | Nie |
> | Lokalizacje/autoapprovedprivatelinkservices | Nie | Nie |
> | Lokalizacje/availabledelegations | Nie | Nie |
> | Lokalizacje/availableprivateendpointtypes | Nie | Nie |
> | Lokalizacje/availableservicealiases | Nie | Nie |
> | Lokalizacje/baremetaltenants | Nie | Nie |
> | Lokalizacje/batchnotifyprivateendpointsforresourcemove | Nie | Nie |
> | Lokalizacje/batchvalidateprivateendpointsforresourcemove | Nie | Nie |
> | Lokalizacje/checkacceleratednetworkingsupport | Nie | Nie |
> | Lokalizacje/checkdnsnameavailability | Nie | Nie |
> | Lokalizacje/checkprivatelinkservicevisibility | Nie | Nie |
> | Lokalizacje/commitinternalazurenetworkmanagerconfiguration | Nie | Nie |
> | Lokalizacje/effectiveresourceownership | Nie | Nie |
> | Lokalizacje/nfvoperationresults | Nie | Nie |
> | Lokalizacje/nfvoperations | Nie | Nie |
> | Lokalizacje/operationresults | Nie | Nie |
> | Lokalizacje/operacje | Nie | Nie |
> | Lokalizacje/servicetagi | Nie | Nie |
> | Lokalizacje/setresourceownership | Nie | Nie |
> | Lokalizacje/supportedvirtualmachinesizes | Nie | Nie |
> | Lokalizacje/użycia | Nie | Nie |
> | Lokalizacje/validateresourceownership | Nie | Nie |
> | Lokalizacje/virtualnetworkavailableendpointservices | Nie | Nie |
> | natgateways | Nie | Nie |
> | networkexperimentprofiles | Nie | Nie |
> | networkintentpolicies | Tak | Tak |
> | networkinterfaces | Tak | Tak |
> | networkprofiles | Nie | Nie |
> | networksecuritygroups | Tak | Tak |
> | networkwatchers | Yes | Nie |
> | networkwatchers / connectionmonitors | Yes | Nie |
> | networkwatchers / flowlogs | Yes | Nie |
> | networkwatchers / pingmeshes | Yes | Nie |
> | operacje | Nie | Nie |
> | p2svpngateways | Nie | Nie |
> | privatednsoperationresults | Nie | Nie |
> | privatednsoperationstatuses | Nie | Nie |
> | privatednszones | Tak | Tak |
> | privatednszones/a | Nie | Nie |
> | privatednszones/AAAA | Nie | Nie |
> | privatednszones/wszystkie | Nie | Nie |
> | privatednszones/CNAME | Nie | Nie |
> | privatednszones/MX | Nie | Nie |
> | privatednszones/PTR | Nie | Nie |
> | privatednszones/SOA | Nie | Nie |
> | privatednszones/SRV | Nie | Nie |
> | privatednszones/txt | Nie | Nie |
> | privatednszones / virtualnetworklinks | Tak | Tak |
> | privatednszonesinternal | Nie | Nie |
> | privateendpointredirectmaps | Nie | Nie |
> | privateendpoints | Tak | Tak |
> | privatelinkservices | Nie | Nie |
> | adresów publicipaddress | Tak — podstawowa jednostka SKU<br>Niestandardowa jednostka SKU | Tak — podstawowa jednostka SKU<br>Niestandardowa jednostka SKU |
> | publicipprefixes | Tak | Tak |
> | routefilters | Nie | Nie |
> | routetables | Tak | Tak |
> | securitypartnerproviders | Tak | Tak |
> | serviceendpointpolicies | Tak | Tak |
> | trafficmanagergeographichierarchies | Nie | Nie |
> | trafficmanagerprofiles | Tak | Tak |
> | trafficmanagerprofiles/map cieplnych | Nie | Nie |
> | trafficmanagerusermetricskeys | Nie | Nie |
> | virtualhubs | Nie | Nie |
> | virtualnetworkgateways | Tak | Tak |
> | virtualnetworks | Tak | Tak |
> | virtualnetworktaps | Nie | Nie |
> | virtualrouters | Tak | Tak |
> | virtualwans | Nie | Nie |
> | vpngateways (wirtualna sieć WAN) | Nie | Nie |
> | vpnserverconfigurations | Nie | Nie |
> | vpnsites (wirtualna sieć WAN) | Nie | Nie |

> [!IMPORTANT]
> Zobacz [wskazówki dotyczące przenoszenia sieci](./move-limitations/networking-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nie | Nie |
> | checknamespaceavailability | Nie | Nie |
> | przestrzeni | Tak | Tak |
> | przestrzenie nazw/notificationhubs | Tak | Tak |
> | operationresults | Nie | Nie |
> | operacje | Nie | Nie |

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | osnamespaces | Tak | Tak |

## <a name="microsoftoffazure"></a>Microsoft. OffAzure

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | hypervsites | Nie | Nie |
> | importsites | Nie | Nie |
> | operacje | Nie | Nie |
> | serversites | Nie | Nie |
> | vmwaresites | Nie | Nie |

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | oparty | Nie | Nie |
> | deletedworkspaces | Nie | Nie |
> | linktargets | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/operationstatuses | Nie | Nie |
> | operacje | Nie | Nie |
> | storageinsightconfigs | Nie | Nie |
> | obszary robocze | Tak | Tak |
> | obszary robocze/źródła danych | Nie | Nie |
> | obszary robocze/linkedservices | Nie | Nie |
> | obszary robocze/linkedstorageaccounts | Nie | Nie |
> | obszary robocze/metadane | Nie | Nie |
> | obszary robocze/zapytanie | Nie | Nie |
> | obszary robocze/scopedprivatelinkproxies | Nie | Nie |

> [!IMPORTANT]
> Upewnij się, że przejście do nowej subskrypcji nie przekracza [przydziałów subskrypcji](azure-subscription-service-limits.md#azure-monitor-limits).
>
> Nie można przenieść obszarów roboczych, które mają połączone konto usługi Automation. Przed rozpoczęciem operacji przenoszenia należy pamiętać o odłączeniu wszystkich kont usługi Automation.

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | managementassociations | Nie | Nie |
> | managementconfigurations | Tak | Tak |
> | operacje | Nie | Nie |
> | rozwiązania | Tak | Tak |
> | widoki | Tak | Tak |

## <a name="microsoftpeering"></a>Microsoft. Komunikacja równorzędna

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | checkserviceprovideravailability | Nie | Nie |
> | legacypeerings | Nie | Nie |
> | operacje | Nie | Nie |
> | peerasns | Nie | Nie |
> | peeringlocations | Nie | Nie |
> | komunikacje równorzędne | Nie | Nie |
> | peeringservicecountries | Nie | Nie |
> | peeringservicelocations | Nie | Nie |
> | peeringserviceproviders | Nie | Nie |
> | peeringservices | Nie | Nie |

## <a name="microsoftpolicyinsights"></a>Microsoft. PolicyInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | asyncoperationresults | Nie | Nie |
> | operacje | Nie | Nie |
> | policyevents | Nie | Nie |
> | policystates | Nie | Nie |
> | policytrackedresources | Nie | Nie |
> | korygowania | Nie | Nie |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Konsole programu | Nie | Nie |
> | pulpity nawigacyjne | Tak | Tak |
> | locations | Nie | Nie |
> | Lokalizacje/konsole | Nie | Nie |
> | Lokalizacje/UserSettings | Nie | Nie |
> | operacje | Nie | Nie |
> | usersettings | Nie | Nie |

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | locations | Nie | Nie |
> | Lokalizacje/checknameavailability | Nie | Nie |
> | workspacecollections | Tak | Tak |

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | produkcyjnych | Tak | Tak |
> | locations | Nie | Nie |
> | Lokalizacje/checknameavailability | Nie | Nie |
> | Lokalizacje/operationresults | Nie | Nie |
> | Lokalizacje/operationstatuses | Nie | Nie |
> | operacje | Nie | Nie |

## <a name="microsoftpowerplatform"></a>Microsoft. PowerPlatform

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | operacje | Nie | Nie |

## <a name="microsoftprojectbabylon"></a>Microsoft. ProjectBabylon

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Konta | Nie | Nie |
> | checknameavailability | Nie | Nie |
> | operacje | Nie | Nie |

## <a name="microsoftproviderhub"></a>Microsoft. ProviderHub

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | availableaccounts | Nie | Nie |
> | providerregistrations | Nie | Nie |
> | providerregistrations / resourcetyperegistrations | Nie | Nie |
> | wprowadzanie | Nie | Nie |

## <a name="microsoftquantum"></a>Microsoft. Quantum

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | locations | Nie | Nie |
> | Lokalizacje/operationstatuses | Nie | Nie |
> | operacje | Nie | Nie |
> | obszary robocze | Nie | Nie |

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | backupprotecteditems | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/allocatedstamp | Nie | Nie |
> | Lokalizacje/allocatestamp | Nie | Nie |
> | Lokalizacje/backupaadproperties | Nie | Nie |
> | Lokalizacje/backupcrossregionrestore | Nie | Nie |
> | Lokalizacje/backupcrrjob | Nie | Nie |
> | Lokalizacje/backupcrrjobs | Nie | Nie |
> | Lokalizacje/backupcrroperationresults | Nie | Nie |
> | Lokalizacje/backupcrroperationsstatus | Nie | Nie |
> | Lokalizacje/backupprevalidateprotection | Nie | Nie |
> | Lokalizacje/backupstatus | Nie | Nie |
> | Lokalizacje/backupvalidatefeatures | Nie | Nie |
> | Lokalizacje/checknameavailability | Nie | Nie |
> | operacje | Nie | Nie |
> | replicationeligibilityresults | Nie | Nie |
> | magazynów | Tak | Tak |

> [!IMPORTANT]
> Zobacz [Recovery Services wskazówki dotyczące przenoszenia](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftredhatopenshift"></a>Microsoft. RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | locations | Nie | Nie |
> | Lokalizacje/operationresults | Nie | Nie |
> | Lokalizacje/operationsstatus | Nie | Nie |
> | openshiftclusters | Nie | Nie |
> | operacje | Nie | Nie |

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nie | Nie |
> | przestrzeni | Tak | Tak |
> | przestrzenie nazw/reguł autoryzacji | Nie | Nie |
> | przestrzenie nazw/hybridconnections | Nie | Nie |
> | przestrzenie nazw/hybridconnections/reguł autoryzacji | Nie | Nie |
> | przestrzenie nazw/privateendpointconnections | Nie | Nie |
> | przestrzenie nazw/wcfrelays | Nie | Nie |
> | przestrzenie nazw/wcfrelays/reguł autoryzacji | Nie | Nie |
> | operacje | Nie | Nie |

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | operacje | Nie | Nie |
> | wybiera | Tak | Tak |
> | resourcechangedetails | Nie | Nie |
> | resourcechanges | Nie | Nie |
> | zasoby | Nie | Nie |
> | resourceshistory | Nie | Nie |
> | subscriptionsstatus | Nie | Nie |

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | availabilitystatuses | Nie | Nie |
> | childavailabilitystatuses | Nie | Nie |
> | childresources | Nie | Nie |
> | emergingissues | Nie | Nie |
> | zdarzenia | Nie | Nie |
> | metadane | Nie | Nie |
> | powiadomienia | Nie | Nie |
> | operacje | Nie | Nie |

## <a name="microsoftresources"></a>Microsoft. resources

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | calculatetemplatehash | Nie | Nie |
> | checkpolicycompliance | Nie | Nie |
> | checkresourcename | Nie | Nie |
> | komputerów | Nie | Nie |
> | wdrożenia/operacje | Nie | Nie |
> | deploymentscripts | Nie | Nie |
> | deploymentscripts/dzienniki | Nie | Nie |
> | linki | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/deploymentscriptoperationresults | Nie | Nie |
> | notifyresourcejobs | Nie | Nie |
> | operationresults | Nie | Nie |
> | operacje | Nie | Nie |
> | dostawców | Nie | Nie |
> | ResourceGroups | Nie | Nie |
> | zasoby | Nie | Nie |
> | opłaty | Nie | Nie |
> | subskrypcje/lokalizacje | Nie | Nie |
> | subskrypcje/operationresults | Nie | Nie |
> | subskrypcje/dostawcy | Nie | Nie |
> | subskrypcje/ResourceGroups | Nie | Nie |
> | subskrypcje/ResourceGroups/zasoby | Nie | Nie |
> | subskrypcje/zasoby | Nie | Nie |
> | subskrypcje/TagName | Nie | Nie |
> | subskrypcje/TagNames/tagvalues | Nie | Nie |
> | tags | Nie | Nie |
> | templatespecs | Nie | Nie |
> | templatespecs/wersje | Nie | Nie |
> | dzierżaw | Nie | Nie |

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | aplikacje | Tak | Nie |
> | checkmoderneligibility | Nie | Nie |
> | checknameavailability | Nie | Nie |
> | operationresults | Nie | Nie |
> | operacje | Nie | Nie |
> | saasresources | Nie | Nie |

## <a name="microsoftsearch"></a>Microsoft. Search

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nie | Nie |
> | checkservicenameavailability | Nie | Nie |
> | operacje | Nie | Nie |
> | resourcehealthmetadata | Nie | Nie |
> | searchservices | Tak | Tak |

> [!IMPORTANT]
> Nie można przenieść kilku zasobów wyszukiwania w różnych regionach w jednej operacji. Zamiast tego należy przenieść je w osobnych operacjach.

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | adaptivenetworkhardenings | Nie | Nie |
> | advancedthreatprotectionsettings | Nie | Nie |
> | alerts | Nie | Nie |
> | allowedconnections | Nie | Nie |
> | applicationwhitelistings | Nie | Nie |
> | assessmentmetadata | Nie | Nie |
> | oceny | Nie | Nie |
> | autodismissalertsrules | Nie | Nie |
> | automatyzacji | Tak | Tak |
> | autoprovisioningsettings | Nie | Nie |
> | complianceresults | Nie | Nie |
> | zachowania zgodności prawnymi | Nie | Nie |
> | datacollectionagents | Nie | Nie |
> | devicesecuritygroups | Nie | Nie |
> | discoveredsecuritysolutions | Nie | Nie |
> | externalsecuritysolutions | Nie | Nie |
> | informationprotectionpolicies | Nie | Nie |
> | iotsecuritysolutions | Tak | Tak |
> | iotsecuritysolutions / analyticsmodels | Nie | Nie |
> | iotsecuritysolutions / analyticsmodels / aggregatedalerts | Nie | Nie |
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | Nie | Nie |
> | jitnetworkaccesspolicies | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/alerty | Nie | Nie |
> | Lokalizacje/allowedconnections | Nie | Nie |
> | Lokalizacje/applicationwhitelistings | Nie | Nie |
> | Lokalizacje/discoveredsecuritysolutions | Nie | Nie |
> | Lokalizacje/externalsecuritysolutions | Nie | Nie |
> | Lokalizacje/jitnetworkaccesspolicies | Nie | Nie |
> | Lokalizacje/securitysolutions | Nie | Nie |
> | Lokalizacje/securitysolutionsreferencedata | Nie | Nie |
> | Lokalizacje/zadania | Nie | Nie |
> | Lokalizacje/topologie | Nie | Nie |
> | operacje | Nie | Nie |
> | policies | Nie | Nie |
> | cen | Nie | Nie |
> | regulatorycompliancestandards | Nie | Nie |
> | regulatorycompliancestandards / regulatorycompliancecontrols | Nie | Nie |
> | regulatorycompliancestandards / regulatorycompliancecontrols / regulatorycomplianceassessments | Nie | Nie |
> | securitycontacts | Nie | Nie |
> | securitysolutions | Nie | Nie |
> | securitysolutionsreferencedata | Nie | Nie |
> | securitystatuses | Nie | Nie |
> | securitystatusessummaries | Nie | Nie |
> | servervulnerabilityassessments | Nie | Nie |
> | ustawienia | Nie | Nie |
> | podoceny | Nie | Nie |
> | zadania | Nie | Nie |
> | replikacji | Nie | Nie |
> | workspacesettings | Nie | Nie |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | agregacji | Nie | Nie |
> | alertrules | Nie | Nie |
> | alertruletemplates | Nie | Nie |
> | automationrules | Nie | Nie |
> | zakładki | Nie | Nie |
> | padkach | Nie | Nie |
> | połączenia dataconnecters | Nie | Nie |
> | dataconnectorscheckrequirements | Nie | Nie |
> | obiekty | Nie | Nie |
> | entityqueries | Nie | Nie |
> | zdarzenia | Nie | Nie |
> | officeconsents | Nie | Nie |
> | operacje | Nie | Nie |
> | ustawienia | Nie | Nie |
> | threatintelligence | Nie | Nie |

## <a name="microsoftserialconsole"></a>Microsoft. SerialConsole

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | consoleservices | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/consoleservices | Nie | Nie |
> | operacje | Nie | Nie |

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
> | checknameavailability | Nie | Nie |
> | checknamespaceavailability | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/deletevirtualnetworkorsubnets | Nie | Nie |
> | przestrzeni | Tak | Tak |
> | przestrzenie nazw/reguł autoryzacji | Nie | Nie |
> | przestrzenie nazw/disasterrecoveryconfigs | Nie | Nie |
> | przestrzenie nazw/disasterrecoveryconfigs/checknameavailability | Nie | Nie |
> | przestrzenie nazw/eventgridfilters | Nie | Nie |
> | przestrzenie nazw/networkrulesets | Nie | Nie |
> | przestrzenie nazw/kolejki | Nie | Nie |
> | przestrzenie nazw/kolejki/reguł autoryzacji | Nie | Nie |
> | obszary nazw/tematy | Nie | Nie |
> | przestrzenie nazw/tematy/reguł autoryzacji | Nie | Nie |
> | obszary nazw/tematy/subskrypcje | Nie | Nie |
> | obszary nazw/tematy/subskrypcje/reguły | Nie | Nie |
> | operacje | Nie | Nie |
> | premiummessagingregions | Nie | Nie |
> | sku | Nie | Nie |

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
> | locations | Nie | Nie |
> | Lokalizacje/clusterversions | Nie | Nie |
> | Lokalizacje/środowiska | Nie | Nie |
> | Lokalizacje/operationresults | Nie | Nie |
> | Lokalizacje/operacje | Nie | Nie |
> | managedclusters | Nie | Nie |
> | sieci | Nie | Nie |
> | operacje | Nie | Nie |
> | secretstores | Nie | Nie |
> | volumes | Nie | Nie |

## <a name="microsoftservicefabricmesh"></a>Microsoft. ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | aplikacje | Tak | Tak |
> | containergroups | Nie | Nie |
> | bram | Tak | Tak |
> | locations | Nie | Nie |
> | Lokalizacje/applicationoperations | Nie | Nie |
> | Lokalizacje/gatewayoperations | Nie | Nie |
> | Lokalizacje/networkoperations | Nie | Nie |
> | Lokalizacje/secretoperations | Nie | Nie |
> | Lokalizacje/volumeoperations | Nie | Nie |
> | sieci | Tak | Tak |
> | operacje | Nie | Nie |
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
> | locations | Nie | Nie |
> | Lokalizacje/checknameavailability | Nie | Nie |
> | Lokalizacje/operationresults | Nie | Nie |
> | Lokalizacje/operationstatuses | Nie | Nie |
> | Lokalizacje/użycia | Nie | Nie |
> | operacje | Nie | Nie |
> | SignalR | Tak | Tak |
> | sygnalizujący/eventgridfilters | Nie | Nie |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | hybridusebenefits | Nie | Nie |
> | operacje | Nie | Nie |

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | applicationdefinitions | Nie | Nie |
> | aplikacje | Nie | Nie |
> | jitrequests | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/operationstatuses | Nie | Nie |
> | operacje | Nie | Nie |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nie | Nie |
> | instancepools | Nie | Nie |
> | locations | Tak | Tak |
> | Lokalizacje/administratorazureasyncoperation | Nie | Nie |
> | Lokalizacje/administratoroperationresults | Nie | Nie |
> | Lokalizacje/auditingsettingsazureasyncoperation | Nie | Nie |
> | Lokalizacje/auditingsettingsoperationresults | Nie | Nie |
> | Lokalizacje/możliwości | Nie | Nie |
> | Lokalizacje/databaseazureasyncoperation | Nie | Nie |
> | Lokalizacje/databaseoperationresults | Nie | Nie |
> | Lokalizacje/databaserestoreazureasyncoperation | Nie | Nie |
> | Lokalizacje/deletevirtualnetworkorsubnets | Nie | Nie |
> | Lokalizacje/deletevirtualnetworkorsubnetsazureasyncoperation | Nie | Nie |
> | Lokalizacje/deletevirtualnetworkorsubnetsoperationresults | Nie | Nie |
> | Lokalizacje/dnsaliasasyncoperation | Nie | Nie |
> | Lokalizacje/dnsaliasoperationresults | Nie | Nie |
> | Lokalizacje/elasticpoolazureasyncoperation | Nie | Nie |
> | Lokalizacje/elasticpooloperationresults | Nie | Nie |
> | Lokalizacje/encryptionprotectorazureasyncoperation | Nie | Nie |
> | Lokalizacje/encryptionprotectoroperationresults | Nie | Nie |
> | Lokalizacje/extendedauditingsettingsazureasyncoperation | Nie | Nie |
> | Lokalizacje/extendedauditingsettingsoperationresults | Nie | Nie |
> | Lokalizacje/failovergroupazureasyncoperation | Nie | Nie |
> | Lokalizacje/failovergroupoperationresults | Nie | Nie |
> | Lokalizacje/firewallrulesazureasyncoperation | Nie | Nie |
> | Lokalizacje/firewallrulesoperationresults | Nie | Nie |
> | Lokalizacje/instancefailovergroupazureasyncoperation | Nie | Nie |
> | Lokalizacje/instancefailovergroupoperationresults | Nie | Nie |
> | Lokalizacje/instancefailovergroups | Nie | Nie |
> | Lokalizacje/instancepoolazureasyncoperation | Nie | Nie |
> | Lokalizacje/instancepooloperationresults | Nie | Nie |
> | Lokalizacje/jobagentazureasyncoperation | Nie | Nie |
> | Lokalizacje/jobagentoperationresults | Nie | Nie |
> | Lokalizacje/longtermretentionbackupazureasyncoperation | Nie | Nie |
> | Lokalizacje/longtermretentionbackupoperationresults | Nie | Nie |
> | Lokalizacje/longtermretentionbackups | Nie | Nie |
> | Lokalizacje/longtermretentionmanagedinstancebackupazureasyncoperation | Nie | Nie |
> | Lokalizacje/longtermretentionmanagedinstancebackupoperationresults | Nie | Nie |
> | Lokalizacje/longtermretentionmanagedinstancebackups | Nie | Nie |
> | Lokalizacje/longtermretentionmanagedinstances | Nie | Nie |
> | Lokalizacje/longtermretentionpolicyazureasyncoperation | Nie | Nie |
> | Lokalizacje/longtermretentionpolicyoperationresults | Nie | Nie |
> | Lokalizacje/longtermretentionservers | Nie | Nie |
> | Lokalizacje/manageddatabaseazureasyncoperation | Nie | Nie |
> | Lokalizacje/manageddatabasecompleterestoreazureasyncoperation | Nie | Nie |
> | Lokalizacje/manageddatabasecompleterestoreoperationresults | Nie | Nie |
> | Lokalizacje/manageddatabaseoperationresults | Nie | Nie |
> | Lokalizacje/manageddatabaserestoreazureasyncoperation | Nie | Nie |
> | Lokalizacje/manageddatabaserestoreoperationresults | Nie | Nie |
> | Lokalizacje/managedinstanceazureasyncoperation | Nie | Nie |
> | Lokalizacje/managedinstanceencryptionprotectorazureasyncoperation | Nie | Nie |
> | Lokalizacje/managedinstanceencryptionprotectoroperationresults | Nie | Nie |
> | Lokalizacje/managedinstancekeyazureasyncoperation | Nie | Nie |
> | Lokalizacje/managedinstancekeyoperationresults | Nie | Nie |
> | Lokalizacje/managedinstancelongtermretentionpolicyazureasyncoperation | Nie | Nie |
> | Lokalizacje/managedinstancelongtermretentionpolicyoperationresults | Nie | Nie |
> | Lokalizacje/managedinstanceoperationresults | Nie | Nie |
> | Lokalizacje/managedinstancetdecertazureasyncoperation | Nie | Nie |
> | Lokalizacje/managedinstancetdecertoperationresults | Nie | Nie |
> | Lokalizacje/managedserversecurityalertpoliciesazureasyncoperation | Nie | Nie |
> | Lokalizacje/managedserversecurityalertpoliciesoperationresults | Nie | Nie |
> | Lokalizacje/managedshorttermretentionpolicyazureasyncoperation | Nie | Nie |
> | Lokalizacje/managedshorttermretentionpolicyoperationresults | Nie | Nie |
> | Lokalizacje/notifyazureasyncoperation | Nie | Nie |
> | Lokalizacje/privateendpointconnectionazureasyncoperation | Nie | Nie |
> | Lokalizacje/privateendpointconnectionoperationresults | Nie | Nie |
> | Lokalizacje/privateendpointconnectionproxyazureasyncoperation | Nie | Nie |
> | Lokalizacje/privateendpointconnectionproxyoperationresults | Nie | Nie |
> | Lokalizacje/securityalertpoliciesazureasyncoperation | Nie | Nie |
> | Lokalizacje/securityalertpoliciesoperationresults | Nie | Nie |
> | Lokalizacje/serveradministratorazureasyncoperation | Nie | Nie |
> | Lokalizacje/serveradministratoroperationresults | Nie | Nie |
> | Lokalizacje/serverazureasyncoperation | Nie | Nie |
> | Lokalizacje/serverkeyazureasyncoperation | Nie | Nie |
> | Lokalizacje/serverkeyoperationresults | Nie | Nie |
> | Lokalizacje/serveroperationresults | Nie | Nie |
> | Lokalizacje/shorttermretentionpolicyazureasyncoperation | Nie | Nie |
> | Lokalizacje/shorttermretentionpolicyoperationresults | Nie | Nie |
> | Lokalizacje/syncagentoperationresults | Nie | Nie |
> | Lokalizacje/syncdatabaseids | Nie | Nie |
> | Lokalizacje/syncgroupoperationresults | Nie | Nie |
> | Lokalizacje/syncmemberoperationresults | Nie | Nie |
> | Lokalizacje/tdecertazureasyncoperation | Nie | Nie |
> | Lokalizacje/tdecertoperationresults | Nie | Nie |
> | Lokalizacje/użycia | Nie | Nie |
> | Lokalizacje/virtualclusterazureasyncoperation | Nie | Nie |
> | Lokalizacje/virtualclusteroperationresults | Nie | Nie |
> | Lokalizacje/virtualnetworkrulesazureasyncoperation | Nie | Nie |
> | Lokalizacje/virtualnetworkrulesoperationresults | Nie | Nie |
> | Lokalizacje/vulnerabilityassessmentscanazureasyncoperation | Nie | Nie |
> | Lokalizacje/vulnerabilityassessmentscanoperationresults | Nie | Nie |
> | managedinstances | Nie | Nie |
> | ManagedInstances/Administratorzy | Nie | Nie |
> | ManagedInstances/bazy danych | Nie | Nie |
> | ManagedInstances/Databases/backuplongtermretentionpolicies | Nie | Nie |
> | ManagedInstances/Databases/vulnerabilityassessments | Nie | Nie |
> | managedinstances / metricdefinitions | Nie | Nie |
> | ManagedInstances/metryki | Nie | Nie |
> | managedinstances / recoverabledatabases | Nie | Nie |
> | managedinstances / tdecertificates | Nie | Nie |
> | managedinstances / vulnerabilityassessments | Nie | Nie |
> | operacje | Nie | Nie |
> | serwerem | Tak | Tak |
> | serwery/administratoroperationresults | Nie | Nie |
> | serwery/Administratorzy | Nie | Nie |
> | serwery/doradcy | Nie | Nie |
> | serwery/aggregateddatabasemetrics | Nie | Nie |
> | serwery/auditingpolicies | Nie | Nie |
> | serwery/auditingsettings | Nie | Nie |
> | serwery/automatictuning | Nie | Nie |
> | serwery/communicationlinks | Nie | Nie |
> | serwery/connectionpolicies | Nie | Nie |
> | Serwery/bazy danych | Tak | Tak |
> | Serwery/bazy danych/doradcy | Nie | Nie |
> | Serwery/bazy danych/auditingpolicies | Nie | Nie |
> | Serwery/bazy danych/auditingsettings | Nie | Nie |
> | Serwery/bazy danych/AuditRecords | Nie | Nie |
> | Serwery/bazy danych/automatictuning | Nie | Nie |
> | Serwery/bazy danych/backuplongtermretentionpolicies | Nie | Nie |
> | Serwery/bazy danych/backupshorttermretentionpolicies | Nie | Nie |
> | Serwery/bazy danych/connectionpolicies | Nie | Nie |
> | Serwery/bazy danych/datamaskingpolicies | Nie | Nie |
> | Serwery/bazy danych/datamaskingpolicies/reguły | Nie | Nie |
> | Serwery/bazy danych/rozszerzenia | Nie | Nie |
> | Serwery/bazy danych/geobackuppolicies | Nie | Nie |
> | Serwery/bazy danych/metricdefinitions | Nie | Nie |
> | Serwery/bazy danych/metryki | Nie | Nie |
> | Serwery/bazy danych/recommendedsensitivitylabels | Nie | Nie |
> | Serwery/bazy danych/securityalertpolicies | Nie | Nie |
> | Serwery/bazy danych/syncgroups | Nie | Nie |
> | Serwery/bazy danych/syncgroups/syncmembers | Nie | Nie |
> | Serwery/bazy danych/topqueries | Nie | Nie |
> | Serwery/bazy danych/topqueries/querytext | Nie | Nie |
> | Serwery/bazy danych/transparentdataencryption | Nie | Nie |
> | Serwery/bazy danych/vulnerabilityassessment | Nie | Nie |
> | Serwery/bazy danych/vulnerabilityassessments | Nie | Nie |
> | Serwery/bazy danych/vulnerabilityassessmentscans | Nie | Nie |
> | Serwery/bazy danych/vulnerabilityassessmentsettings | Nie | Nie |
> | Serwery/bazy danych/WorkloadGroups | Nie | Nie |
> | serwery/databasesecuritypolicies | Nie | Nie |
> | serwery/disasterrecoveryconfiguration | Nie | Nie |
> | serwery/dnsaliases | Nie | Nie |
> | serwery/elasticpoolestimates | Nie | Nie |
> | serwery/elasticpools | Tak | Tak |
> | serwery/elasticpools/doradcy | Nie | Nie |
> | serwery/elasticpools/metricdefinitions | Nie | Nie |
> | serwery/elasticpools/metryki | Nie | Nie |
> | serwery/encryptionprotector | Nie | Nie |
> | serwery/extendedauditingsettings | Nie | Nie |
> | serwery/failovergroups | Nie | Nie |
> | serwery/Importowanie | Nie | Nie |
> | serwery/importexportoperationresults | Nie | Nie |
> | serwery/jobaccounts | Tak | Tak |
> | serwery/jobagents | Tak | Tak |
> | serwery/jobagents/zadania | Nie | Nie |
> | serwery/jobagents/zadania/wykonania | Nie | Nie |
> | serwery/jobagents/zadania/kroki | Nie | Nie |
> | serwery/klucze | Nie | Nie |
> | serwery/operationresults | Nie | Nie |
> | serwery/recommendedelasticpools | Nie | Nie |
> | serwery/recoverabledatabases | Nie | Nie |
> | serwery/restorabledroppeddatabases | Nie | Nie |
> | serwery/securityalertpolicies | Nie | Nie |
> | serwery/cele | Nie | Nie |
> | serwery/syncagents | Nie | Nie |
> | serwery/tdecertificates | Nie | Nie |
> | serwery/użycia | Nie | Nie |
> | serwery/virtualnetworkrules | Nie | Nie |
> | serwery/vulnerabilityassessments | Nie | Nie |
> | virtualclusters | Tak | Tak |

> [!IMPORTANT]
> Baza danych i serwer muszą znajdować się w tej samej grupie zasobów. Podczas przenoszenia programu SQL Server wszystkie jego bazy danych również są przenoszone. To zachowanie ma zastosowanie do baz danych Azure SQL Database i usługi Azure Synapse Analytics.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | locations | Nie | Nie |
> | Lokalizacje/availabilitygrouplisteneroperationresults | Nie | Nie |
> | Lokalizacje/operationtypes | Nie | Nie |
> | Lokalizacje/sqlvirtualmachinegroupoperationresults | Nie | Nie |
> | Lokalizacje/sqlvirtualmachineoperationresults | Nie | Nie |
> | operacje | Nie | Nie |
> | sqlvirtualmachinegroups | Tak | Tak |
> | sqlvirtualmachinegroups / availabilitygrouplisteners | Nie | Nie |
> | sqlvirtualmachines | Tak | Tak |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/asyncoperations | Nie | Nie |
> | Lokalizacje/checknameavailability | Nie | Nie |
> | Lokalizacje/deletevirtualnetworkorsubnets | Nie | Nie |
> | Lokalizacje/użycia | Nie | Nie |
> | operacje | Nie | Nie |
> | storageaccounts | Tak | Tak |
> | storageaccounts/blobservices | Nie | Nie |
> | storageaccounts/FileServices | Nie | Nie |
> | storageaccounts/listaccountsas | Nie | Nie |
> | storageaccounts/listservicesas | Nie | Nie |
> | storageaccounts/queueservices | Nie | Nie |
> | storageaccounts/usługi | Nie | Nie |
> | storageaccounts/usługi/metricdefinitions | Nie | Nie |
> | storageaccounts/tableservices | Nie | Nie |
> | użycia | Nie | Nie |

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | pamięci podręcznych | Nie | Nie |

## <a name="microsoftstoragesync"></a>Microsoft. StorageSync

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | locations | Nie | Nie |
> | Lokalizacje/checknameavailability | Nie | Nie |
> | Lokalizacje/operationresults | Nie | Nie |
> | Lokalizacje/operacje | Nie | Nie |
> | Lokalizacje/przepływy pracy | Nie | Nie |
> | operacje | Nie | Nie |
> | storagesyncservices | Tak | Tak |
> | storagesyncservices/RegisteredServers | Nie | Nie |
> | storagesyncservices / syncgroups | Nie | Nie |
> | storagesyncservices / syncgroups / cloudendpoints | Nie | Nie |
> | storagesyncservices / syncgroups / serverendpoints | Nie | Nie |
> | storagesyncservices/przepływy pracy | Nie | Nie |

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
> | operacje | Nie | Nie |

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | oparty | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/przydziały | Nie | Nie |
> | operacje | Nie | Nie |
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
> | Anuluj | Nie | Nie |
> | Anulowanie subskrypcji | Nie | Nie |
> | Włącz | Nie | Nie |
> | operationresults | Nie | Nie |
> | operacje | Nie | Nie |
> | zmień nazwę | Nie | Nie |
> | subscriptiondefinitions | Nie | Nie |
> | subscriptionoperations | Nie | Nie |
> | opłaty | Nie | Nie |

## <a name="microsoftsupport"></a>Microsoft. Support

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nie | Nie |
> | operationresults | Nie | Nie |
> | operacje | Nie | Nie |
> | operationsstatus | Nie | Nie |
> | services | Nie | Nie |
> | usługi/problemclassifications | Nie | Nie |
> | supporttickets | Nie | Nie |

## <a name="microsoftsynapse"></a>Microsoft. Synapse

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nie | Nie |
> | operacje | Nie | Nie |
> | obszary robocze | Tak | Tak |
> | obszary robocze/bigdatapools | Tak | Tak |
> | obszary robocze/operationresults | Nie | Nie |
> | obszary robocze/operationstatuses | Nie | Nie |
> | obszary robocze/pule | Tak | Tak |

## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | wiejski | Tak | Tak |
> | środowiska/accesspolicies | Nie | Nie |
> | środowiska/źródła zdarzeń | Tak | Tak |
> | środowiska/referencedatasets | Tak | Tak |
> | operacje | Nie | Nie |

## <a name="microsofttoken"></a>Microsoft. token

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | przechowywać | Tak | Tak |
> | Sklepy/accesspolicies | Nie | Nie |
> | Sklepy/usługi | Nie | Nie |
> | Sklepy/usługi/tokeny | Nie | Nie |

## <a name="microsoftvirtualmachineimages"></a>Microsoft. VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | imagetemplates | Nie | Nie |
> | imagetemplates / runoutputs | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/operacje | Nie | Nie |
> | operacje | Nie | Nie |

## <a name="microsoftvisualstudio"></a>Microsoft. VisualStudio

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | account | Nie | Nie |
> | konto/rozszerzenie | Nie | Nie |
> | konto/projekt | Nie | Nie |
> | checknameavailability | Nie | Nie |
> | operacje | Nie | Nie |

> [!IMPORTANT]
> Aby zmienić subskrypcję usługi Azure DevOps, zobacz [zmiana subskrypcji platformy Azure używanej do rozliczania](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftvmware"></a>Microsoft. VMware

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | arczones | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/operationstatuses | Nie | Nie |
> | operacje | Nie | Nie |
> | resourcepools | Nie | Nie |
> | serwery vCenter | Nie | Nie |
> | virtualmachines | Nie | Nie |
> | virtualmachinetemplates | Nie | Nie |
> | virtualnetworks | Nie | Nie |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | Nie | Nie |
> | dedicatedcloudservices | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/dostępność | Nie | Nie |
> | Lokalizacje/operationresults | Nie | Nie |
> | Lokalizacje/privateclouds | Nie | Nie |
> | Lokalizacje/privateclouds/ResourcePools | Nie | Nie |
> | Lokalizacje/privateclouds/virtualmachinetemplates | Nie | Nie |
> | Lokalizacje/privateclouds/virtualnetworks | Nie | Nie |
> | Lokalizacje/użycia | Nie | Nie |
> | operacje | Nie | Nie |
> | virtualmachines | Nie | Nie |

## <a name="microsoftvnfmanager"></a>Microsoft. VnfManager

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | devices | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/operationstatuses | Nie | Nie |
> | operacje | Nie | Nie |
> | vnfs | Nie | Nie |

## <a name="microsoftvsonline"></a>Microsoft. VSOnline

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | Konta | Nie | Nie |
> | operacje | Nie | Nie |
> | pakiety | Nie | Nie |
> | registeredsubscriptions | Nie | Nie |

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | availablestacks | Nie | Nie |
> | billingmeters | Nie | Nie |
> | certyfikaty | Nie | Yes |
> | checknameavailability | Nie | Nie |
> | connectiongateways | Tak | Tak |
> | Licznik | Tak | Tak |
> | customapis | Tak | Tak |
> | deletedsites | Nie | Nie |
> | deploymentlocations | Nie | Nie |
> | Georegiony | Nie | Nie |
> | hostingenvironments | Nie | Nie |
> | hostingenvironments / eventgridfilters | Nie | Nie |
> | hostingenvironments / multirolepools | Nie | Nie |
> | hostingenvironments / workerpools | Nie | Nie |
> | ishostingenvironmentnameavailable | Nie | Nie |
> | ishostnameavailable | Nie | Nie |
> | isusernameavailable | Nie | Nie |
> | kubeenvironments | Tak | Tak |
> | listsitesassignedtohostname | Nie | Nie |
> | locations | Nie | Nie |
> | Lokalizacje/apioperations | Nie | Nie |
> | Lokalizacje/connectiongatewayinstallations | Nie | Nie |
> | Lokalizacje/deletedsites | Nie | Nie |
> | Lokalizacje/deletevirtualnetworkorsubnets | Nie | Nie |
> | Lokalizacje/extractapidefinitionfromwsdl | Nie | Nie |
> | Lokalizacje/getnetworkpolicies | Nie | Nie |
> | Lokalizacje/listwsdlinterfaces | Nie | Nie |
> | Lokalizacje/managedapis | Nie | Nie |
> | Lokalizacje/operationresults | Nie | Nie |
> | Lokalizacje/operacje | Nie | Nie |
> | Lokalizacje/środowiska uruchomieniowe | Nie | Nie |
> | operacje | Nie | Nie |
> | publishingusers | Nie | Nie |
> | zalecenia | Nie | Nie |
> | resourcehealthmetadata | Nie | Nie |
> | Runtime | Nie | Nie |
> | dopuszczalna | Tak | Tak |
> | dopuszczalna/eventgridfilters | Nie | Nie |
> | lokacje | Tak | Tak |
> | Lokacje/eventgridfilters | Nie | Nie |
> | Lokacje/hostnamebindings | Nie | Nie |
> | Lokacje/NetworkConfig | Nie | Nie |
> | Lokacje/premieraddons | Tak | Tak |
> | Lokacje/miejsca | Tak | Tak |
> | Lokacje/miejsca/eventgridfilters | Nie | Nie |
> | Lokacje/miejsca/hostnamebindings | Nie | Nie |
> | Lokacje/miejsca/NetworkConfig | Nie | Nie |
> | sourcecontrols | Nie | Nie |
> | staticsites | Nie | Nie |
> | legalizacj | Nie | Nie |
> | verifyhostingenvironmentvnet | Nie | Nie |

> [!IMPORTANT]
> Zobacz [App Service wskazówki dotyczące przenoszenia](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsesu"></a>Microsoft. WindowsESU

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | locations | Nie | Nie |
> | Lokalizacje/operationstatuses | Nie | Nie |
> | multipleactivationkeys | Nie | Nie |
> | operacje | Nie | Nie |

## <a name="microsoftwindowsiot"></a>Microsoft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | deviceservices | Nie | Nie |
> | operacje | Nie | Nie |

## <a name="microsoftworkloadbuilder"></a>Microsoft. WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | locations | Nie | Nie |
> | Lokalizacje/operationstatuses | Nie | Nie |
> | operacje | Nie | Nie |
> | obciążeń | Nie | Nie |

## <a name="microsoftworkloadmonitor"></a>Microsoft. Monitor obciążenia został

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja |
> | ------------- | ----------- | ---------- |
> | komponentów | Nie | Nie |
> | componentssummary | Nie | Nie |
> | monitorinstances | Nie | Nie |
> | monitorinstancessummary | Nie | Nie |
> | monitora | Nie | Nie |
> | notificationsettings | Nie | Nie |
> | operacje | Nie | Nie |

## <a name="third-party-services"></a>Usługi innych firm

Usługi innych firm obecnie nie obsługują operacji przenoszenia.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać polecenia przenoszenia zasobów, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](move-resource-group-and-subscription.md).

Aby uzyskać te same dane w postaci pliku wartości rozdzielanych przecinkami, Pobierz [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
