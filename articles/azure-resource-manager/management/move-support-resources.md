---
title: Obsługa operacji przenoszenia według typu zasobu
description: Wyświetla listę typów zasobów platformy Azure, które można przenieść do nowej grupy zasobów, subskrypcji lub regionu.
ms.topic: conceptual
ms.date: 04/16/2021
ms.openlocfilehash: c159b6e5f64f3052a6584034aa58b058b1426b16
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725570"
---
# <a name="move-operation-support-for-resources"></a>Obsługa operacji przenoszenia dla zasobów

W tym artykule przedstawiono, czy typ zasobu platformy Azure obsługuje operację przenoszenia. Zawiera również informacje o specjalnych warunkach, które należy wziąć pod uwagę podczas przenoszenia zasobu.

> [!IMPORTANT]
> W większości przypadków zasobu podrzędnego nie można przenieść niezależnie od jego zasobu nadrzędnego. Zasoby podrzędne mają typ zasobu w formacie `<resource-provider-namespace>/<parent-resource>/<child-resource>` . Na przykład `Microsoft.ServiceBus/namespaces/queues` to zasób podrzędny . `Microsoft.ServiceBus/namespaces` Po przenoszeniu zasobu nadrzędnego zasób podrzędny jest automatycznie przenoszony wraz z nim. Jeśli w tym artykule nie widzisz zasobu podrzędnego, możesz założyć, że został on przeniesiony z zasobem nadrzędnym. Jeśli zasób nadrzędny nie obsługuje przenoszenia, nie można przenieść zasobu podrzędnego.

Przejdź do przestrzeni nazw dostawcy zasobów:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.a pomów](#microsoftaadiam)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.analysisservices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AVS](#microsoftavs)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.ClassicSubscription](#microsoftclassicsubscription)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataConnect](#microsoftdataconnect)
> - [Microsoft.DataExchange](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLake](#microsoftdatalake)
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
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Experimentation](#microsoftexperimentation)
> - [Microsoft.Zygmej](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.HybridNetwork](#microsofthybridnetwork)
> - [Microsoft.Zygmej](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft.MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
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
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
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
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - [Microsoft.Quantum](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [microsoft.support](#microsoftsupport)
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMware](#microsoftvmware)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | domainservices | Nie | Nie |  Nie |

## <a name="microsoftaadiam"></a>microsoft.a pomiń

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | diagnosticsettings | Nie | Nie | Nie |
> | diagnosticsettingscategories | Nie | Nie | Nie |
> | privatelinkforazuread | Tak | Tak | Nie |
> | Najemców | Tak | Tak | Nie |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | pomoc techniczna | Nie | Nie | Nie |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | aadsupportcases | Nie | Nie | Nie |
> | addsservices | Nie | Nie | Nie |
> | Agentów | Nie | Nie | Nie |
> | anonymousapiusers | Nie | Nie | Nie |
> | konfiguracja | Nie | Nie | Nie |
> | dzienniki | Nie | Nie | Nie |
> | raporty | Nie | Nie | Nie |
> | servicehealthmetrics | Nie | Nie | Nie |
> | services | Nie | Nie | Nie |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Konfiguracji | Nie | Nie | Nie |
> | generaterecommendations | Nie | Nie | Nie |
> | metadane | Nie | Nie | Nie |
> | zalecenia | Nie | Nie | Nie |
> | Suppressions | Nie | Nie | Nie |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | actionrules (akcje) | Tak | Tak | Nie |
> | alerts | Nie | Nie | Nie |
> | lista alertów | Nie | Nie | Nie |
> | alertsmetadata | Nie | Nie | Nie |
> | alertssummary | Nie | Nie | Nie |
> | alertssummarylist | Nie | Nie | Nie |
> | smartdetectoralertrules | Tak | Tak | Nie |
> | grupy inteligentne | Nie | Nie | Nie |

## <a name="microsoftanalysisservices"></a>Microsoft.analysisservices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Serwerów | Tak | Tak | Nie |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!IMPORTANT]
> Nie API Management usługi, która jest ustawiona na wartość SKU Zużycie.

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | reportfeedback | Nie | Nie | Nie |
> | usługa | Tak | Tak | Tak (przy użyciu szablonu) <br/><br/> [Przenieś API Management między regionami.](../../api-management/api-management-howto-migrate.md) |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | magazyny konfiguracji | Tak | Tak | Nie |
> | configurationstores/eventgridfilters | Nie | Nie | Nie |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Wiosna | Tak | Tak | Nie |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!IMPORTANT]
> Zobacz [App Service wskazówki dotyczące przenoszenia.](./move-limitations/app-service-move-limitations.md)

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | apiapps | Nie | Nie | Tak (przy użyciu szablonu)<br/><br/> [Przenoszenie aplikacji App Service do innego regionu](../../app-service/manage-move-across-regions.md) |
> | identyfikatory aplikacji | Nie | Nie | Nie |
> | Bramy | Nie | Nie | Nie |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | attestationproviders | Tak | Tak | Nie |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | klasycyadministratorzy | Nie | Nie | Nie |
> | dataaliases | Nie | Nie | Nie |
> | denyassignments | Nie | Nie | Nie |
> | elevateaccess | Nie | Nie | Nie |
> | findorphanroleassignments | Nie | Nie | Nie |
> | Blokad | Nie | Nie | Nie |
> | uprawnienia | Nie | Nie | Nie |
> | policyassignments (przypisania zasad) | Nie | Nie | Nie |
> | Policydefinitions | Nie | Nie | Nie |
> | policysetdefinitions | Nie | Nie | Nie |
> | privatelinkassociations | Nie | Nie | Nie |
> | resourcemanagementprivatelinks | Nie | Nie | Nie |
> | roleassignments | Nie | Nie | Nie |
> | roleassignmentsusagemetrics | Nie | Nie | Nie |
> | roledefinitions | Nie | Nie | Nie |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!IMPORTANT]
> Zasoby Runbook muszą istnieć w tej samej grupie zasobów co konto usługi Automation.
>
> Aby uzyskać więcej informacji, [zobacz Move your Azure Automation account to another subscription (Przenoszenie](../../automation/how-to/move-account.md?toc=/azure/azure-resource-manager/toc.json)konta usługi Azure Automation do innej subskrypcji).

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | automationaccounts | Tak | Tak | Tak (przy użyciu szablonu) <br/><br/> [Korzystanie z replikacji geograficznej](../../automation/automation-managing-data.md#geo-replication-in-azure-automation) |
> | automationaccounts/configurations | Tak | Tak | Nie |
> | automationaccounts/runbook | Tak | Tak | Nie |

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | chmury prywatne | Tak | Tak | Nie |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | b2cdirectories | Tak | Tak | Nie |
> | b2ctenants | Nie | Nie | Nie |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | kontrolery danych | Nie | Nie | Nie |
> | hybriddatamanagers | Nie | Nie | Nie |
> | postgresinstances | Nie | Nie | Nie |
> | sqlinstances | Nie | Nie | Nie |
> | sqlmanagedinstances | Nie | Nie | Nie |
> | sqlserverinstances | Nie | Nie | Nie |
> | Sqlserverregistrations | Tak | Tak | Nie |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | cloudmanifestfiles | Nie | Nie | Nie |
> | Rejestracji | Tak | Tak | Nie |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Klastrów | Nie | Nie | Nie |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | batchaccounts | Tak | Tak | Kont usługi Batch nie można przenosić bezpośrednio z jednego regionu do innego, ale można użyć szablonu do wyeksportowania szablonu, zmodyfikowania go i wdrożenia w nowym regionie. <br/><br/> Dowiedz się więcej [o przenoszeniu konta usługi Batch między regionami](../../batch/best-practices.md#moving-batch-accounts-across-regions) |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | konta rozliczeniowe | Nie | Nie | Nie |
> | billingperiods | Nie | Nie | Nie |
> | billingpermissions | Nie | Nie | Nie |
> | billingproperty | Nie | Nie | Nie |
> | billingroleassignments | Nie | Nie | Nie |
> | billingroledefinitions | Nie | Nie | Nie |
> | Działów | Nie | Nie | Nie |
> | konta rejestracji | Nie | Nie | Nie |
> | Faktury | Nie | Nie | Nie |
> | Transfery | Nie | Nie | Nie |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | mapapis | Nie | Nie | Nie |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | biztalk | Nie | Nie | Nie |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | blockchainmembers | Nie | Nie | Nie <br/><br/> Sieć łańcucha bloków nie może mieć węzłów w różnych regionach. |
> | cordamembers | Nie | Nie | Nie |
> | Obserwatorów | Nie | Nie | Nie |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | tokenservices | Nie | Nie | Nie |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | blueprintassignments | Nie | Nie | Nie |
> | Plany | Nie | Nie | Nie |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | botservices | Tak | Tak | Nie |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!IMPORTANT]
> Jeśli Azure Cache for Redis jest skonfigurowane przy użyciu sieci wirtualnej, nie można przenieść wystąpienia do innej subskrypcji. Zobacz [Ograniczenia dotyczące przenoszenia sieci.](./move-limitations/networking-move-limitations.md)

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Redis | Tak | Tak | Nie |
> | redisenterprise | Nie | Nie | Nie |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | appliedreservations | Nie | Nie | Nie |
> | calculateexchange | Nie | Nie | Nie |
> | calculateprice | Nie | Nie | Nie |
> | calculatepurchaseprice | Nie | Nie | Nie |
> | Katalogi | Nie | Nie | Nie |
> | commercialreservationorders | Nie | Nie | Nie |
> | Exchange | Nie | Nie | Nie |
> | reservationorders | Nie | Nie | Nie |
> | Rezerwacje | Nie | Nie | Nie |
> | zasoby | Nie | Nie | Nie |
> | validatereservationorder | Nie | Nie | Nie |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | cdnwebapplicationfirewallmanagedrulesets | Nie | Nie | Nie |
> | cdnwebapplicationfirewallpolicies | Tak | Tak | Nie |
> | edgenodes (węzły brzegowe) | Nie | Nie | Nie |
> | Profile | Tak | Tak | Nie |
> | profile/punkty końcowe | Tak | Tak | Nie |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!IMPORTANT]
> Zobacz [App Service wskazówki dotyczące przenoszenia.](./move-limitations/app-service-move-limitations.md)

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | certificateorders | Tak | Tak | Nie |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!IMPORTANT]
> Zobacz [Wskazówki dotyczące przenoszenia wdrożenia klasycznego.](./move-limitations/classic-model-move-limitations.md) Zasoby wdrożenia klasycznego można przenosić między subskrypcjami przy użyciu operacji specyficznej dla tego scenariusza.

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | możliwości | Nie | Nie | Nie |
> | nazwy domen | Tak | Nie | Nie |
> | quotas | Nie | Nie | Nie |
> | resourcetypes | Nie | Nie | Nie |
> | validatesubscriptionmoveavailability | Nie | Nie | Nie |
> | virtualmachines | Tak | Tak | Nie |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!IMPORTANT]
> Zobacz [Wskazówki dotyczące przenoszenia wdrożenia klasycznego.](./move-limitations/classic-model-move-limitations.md) Zasoby wdrożenia klasycznego można przenosić między subskrypcjami przy użyciu operacji specyficznej dla tego scenariusza.

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | classicinfrastructureresources | Nie | Nie | Nie |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!IMPORTANT]
> Zobacz [Wskazówki dotyczące przenoszenia wdrożenia klasycznego.](./move-limitations/classic-model-move-limitations.md) Zasoby wdrożenia klasycznego można przenosić między subskrypcjami przy użyciu operacji specyficznej dla tego scenariusza.

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | możliwości | Nie | Nie | Nie |
> | expressroutecrossconnections | Nie | Nie | Nie |
> | expressroutecrossconnections/peerings | Nie | Nie | Nie |
> | gatewaysupporteddevices | Nie | Nie | Nie |
> | networksecuritygroups | Nie | Nie | Nie |
> | quotas | Nie | Nie | Nie |
> | reservedips | Nie | Nie | Nie |
> | virtualnetworks | Nie | Nie | Nie |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!IMPORTANT]
> Zobacz [Wskazówki dotyczące przenoszenia wdrożenia klasycznego.](./move-limitations/classic-model-move-limitations.md) Zasoby wdrożenia klasycznego można przenosić między subskrypcjami przy użyciu operacji specyficznej dla tego scenariusza.

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Dysków | Nie | Nie | Nie |
> | images | Nie | Nie | Nie |
> | osimages | Nie | Nie | Nie |
> | osplatformimages | Nie | Nie | Nie |
> | publicimages | Nie | Nie | Nie |
> | quotas | Nie | Nie | Nie |
> | konta magazynu | Tak | Nie | Tak |
> | vmimages | Nie | Nie | Nie |

## <a name="microsoftclassicsubscription"></a>Microsoft.ClassicSubscription

> [!IMPORTANT]
> Zobacz [Wskazówki dotyczące przenoszenia wdrożenia klasycznego.](./move-limitations/classic-model-move-limitations.md) Zasoby wdrożenia klasycznego można przenosić między subskrypcjami przy użyciu operacji specyficznej dla tego scenariusza.

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | operacje | Nie | Nie | Nie |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Konta | Tak | Tak | Nie |
> | Cognitive Search | **Oczekujące** | **Oczekujące** | Obsługiwane w krokach ręcznych.<br/><br/> Dowiedz się [więcej o przenoszeniu usługi Azure Cognitive Search do innego regionu](../../search/search-howto-move-across-regions.md) |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | ratecard | Nie | Nie | Nie |
> | usageaggregates | Nie | Nie | Nie |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!IMPORTANT]
> Zobacz [Virtual Machines wskazówki dotyczące przenoszenia.](./move-limitations/virtual-machines-move-limitations.md)

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | zestawy dostępności | Tak | Tak |  Tak <br/><br/> Użyj [Azure Resource Mover,](../../resource-mover/tutorial-move-region-virtual-machines.md) aby przenieść zestawy dostępności. |
> | diskaccesses | Nie | Nie | Nie |
> | diskencryptionsets | Nie | Nie | Nie |
> | Dysków | Tak | Tak | Tak <br/><br/> Użyj [Azure Resource Mover,](../../resource-mover/tutorial-move-region-virtual-machines.md) aby przenieść maszyny wirtualne platformy Azure i powiązane dyski. |
> | Galerie | Nie | Nie | Nie |
> | galerie/obrazy | Nie | Nie | Nie |
> | galerie/obrazy/wersje | Nie | Nie | Nie |
> | grupy hostów | Nie | Nie | Nie |
> | hosty/grupy hostów | Nie | Nie | Nie |
> | images | Tak | Tak | Nie |
> | proximityplacementgroups | Tak | Tak | Nie |
> | restorepointcollections | Nie | Nie | Nie |
> | restorepointcollections / restorepoints | Nie | Nie | Nie |
> | sharedvmextensions | Nie | Nie | Nie |
> | sharedvmimages | Nie | Nie | Nie |
> | sharedvmimages / wersje | Nie | Nie | Nie |
> | Migawki | Tak | Tak | Nie |
> | sshpublickeys | Nie | Nie | Nie |
> | virtualmachines | Tak | Tak | Tak <br/><br/> Użyj [Azure Resource Mover,](../../resource-mover/tutorial-move-region-virtual-machines.md) aby przenieść maszyny wirtualne platformy Azure. |
> | virtualmachines/extensions | Tak | Tak | Nie |
> | zestawy virtualmachinescaleset | Tak | Tak | Nie |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | aggregatedcost | Nie | Nie | Nie |
> | Salda | Nie | Nie | Nie |
> | Budżetów | Nie | Nie | Nie |
> | Opłaty | Nie | Nie | Nie |
> | costtags | Nie | Nie | Nie |
> | Kredyty | Nie | Nie | Nie |
> | zdarzenia | Nie | Nie | Nie |
> | Prognozy | Nie | Nie | Nie |
> | Wiele | Nie | Nie | Nie |
> | Rynkach | Nie | Nie | Nie |
> | arkusze cen | Nie | Nie | Nie |
> | Produktów | Nie | Nie | Nie |
> | reservationdetails | Nie | Nie | Nie |
> | reservationrecommendationdetails | Nie | Nie | Nie |
> | reservationrecommendations | Nie | Nie | Nie |
> | reservationsummaries | Nie | Nie | Nie |
> | reservationtransactions | Nie | Nie | Nie |
> | tags | Nie | Nie | Nie |
> | Najemców | Nie | Nie | Nie |
> | Warunki | Nie | Nie | Nie |
> | usagedetails | Nie | Nie | Nie |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | containergroups | Nie | Nie | Nie |
> | serviceassociationlinks | Nie | Nie | Nie |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Rejestrów | Tak | Tak | Nie |
> | rejestry/puli agentów | Tak | Tak | Nie |
> | rejestry /buildtasks | Tak | Tak | Nie |
> | rejestry/replikacje | Tak | Tak | Nie |
> | rejestry/zadania | Tak | Tak | Nie |
> | rejestry/webhook | Tak | Tak | Nie |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | containerservices | Nie | Nie | Nie |
> | managedclusters | Nie | Nie | Nie |
> | openshiftmanagedclusters | Nie | Nie | Nie |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | aplikacje | Nie | Nie | Nie |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Konta | Nie | Nie | Nie |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | alerts | Nie | Nie | Nie |
> | konta rozliczeniowe | Nie | Nie | Nie |
> | Budżetów | Nie | Nie | Nie |
> | cloudconnectors (połączenia w chmurze) | Nie | Nie | Nie |
> | konta integracji | Tak | Tak | Nie |
> | Działów | Nie | Nie | Nie |
> | Wymiary | Nie | Nie | Nie |
> | konta rejestracji | Nie | Nie | Nie |
> | Wywozu | Nie | Nie | Nie |
> | externalbillingaccounts | Nie | Nie | Nie |
> | forecast | Nie | Nie | Nie |
> | query | Nie | Nie | Nie |
> | zarejestruj | Nie | Nie | Nie |
> | konfiguracje raportów | Nie | Nie | Nie |
> | raporty | Nie | Nie | Nie |
> | ustawienia | Nie | Nie | Nie |
> | showbackrules | Nie | Nie | Nie |
> | widoki | Nie | Nie | Nie |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | koncentratory, | Nie | Nie | Nie |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Żądania | Nie | Nie | Nie |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Stowarzyszenia | Nie | Nie | Nie |
> | resourceproviders | Tak | Tak | Nie |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Zadania | Nie | Nie | Nie |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | availableskus | Nie | Nie | Nie |
> | databoxedgedevices | Nie | Nie | Nie |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | obszary robocze | Nie | Nie | Nie |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Katalogi | Tak | Tak | Nie |
> | datacatalogs | Nie | Nie | Nie |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | connectionmanagers (menedżerowie połączeń) | Nie | Nie | Nie |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Pakiety | Nie | Nie | Nie |
> | Plany | Nie | Nie | Nie |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | datafactories | Tak | Tak | Nie |
> | Fabryk | Tak | Tak | Nie |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | datalakeaccounts | Nie | Nie | Nie |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Konta | Tak | Tak | Nie |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Konta | Tak | Tak | Nie |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | services | Nie | Nie | Nie |
> | usługi/projekty | Nie | Nie | Nie |
> | Slotów | Nie | Nie | Nie |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ---------- |
> | backupvaults | Nie | Nie | Nie |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Konta | Tak | Tak | Nie |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Serwerów | Tak | Tak | Repliki odczytu między regionami można użyć do przeniesienia istniejącego serwera. [Dowiedz się więcej](../../postgresql/howto-move-regions-portal.md).<br/><br/> Jeśli usługa jest aprowizowana za pomocą geograficznie nadmiarowego magazynu kopii zapasowych, przywracanie w innych regionach można wykonać przy użyciu funkcji przywracania geograficznego. [Dowiedz się więcej](../../mariadb/concepts-business-continuity.md#recover-from-an-azure-regional-data-center-outage).

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | flexibleServers | Nie | Nie | Nie |
> | Serwerów | Tak | Tak | Repliki odczytu między regionami można użyć do przeniesienia istniejącego serwera. [Dowiedz się więcej](../../mysql/howto-move-regions-portal.md).

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | flexibleServers | Nie | Nie | Nie |
> | Servergroups | Nie | Nie | Nie |
> | Serwerów | Tak | Tak | Repliki odczytu między regionami można użyć do przeniesienia istniejącego serwera. [Dowiedz się więcej.](../../postgresql/howto-move-regions-portal.md)
> | serversv2 | Tak | Tak | Nie |
> | singleservers | Tak | Tak | Nie |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | artifactsources | Tak | Tak | Nie |
> | Wdrożenia | Tak | Tak | Nie |
> | servicetopologies | Tak | Tak | Nie |
> | servicetopologies /services | Tak | Tak | Nie |
> | servicetopologies / services / serviceunits | Tak | Tak | Nie |
> | kroki | Tak | Tak | Nie |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | grupy aplikacji | Tak | Tak | Nie |
> | hostpools (bufory hostów) | Tak | Tak | Nie |
> | obszary robocze | Tak | Tak | Nie |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | elasticpools | Nie | Nie | Nie. Zasób nie jest ujawniony. |
> | elasticpools /iothubtenants | Nie | Nie | Nie. Zasób nie jest ujawniony. |
> | iothubs | Tak | Tak | Tak. [Dowiedz się więcej](../../iot-hub/iot-hub-how-to-clone.md) |
> | provisioningservices | Tak | Tak | Nie |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Rurociągów | Tak | Tak | Nie |
> | kontrolery | **Oczekujące** | **Oczekujące** | Nie |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | kontrolery | Tak | Tak | Nie |
> | Klaster usługi AKS | **Oczekujące** | **Oczekujące** | Nie<br/><br/> [Dowiedz się więcej](../../dev-spaces/faq.md#can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region) o przenoszeniu do innego regionu.

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | centra laboratoryjne | Nie | Nie | Nie |
> | Labs | Tak | Nie | Nie |
> | laboratoria/środowiska | Tak | Tak | Nie |
> | laboratoria/urządzenia servicerunners | Tak | Tak | Nie |
> | laboratoria/maszyny wirtualne | Tak | Nie | Nie |
> | Harmonogramy | Tak | Tak | Nie |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | digitaltwinsinstances | Nie | Nie | Tak, przez ponowne stworzenie zasobów w nowym regionie. [Dowiedz się więcej](../../digital-twins/how-to-move-regions.md) |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | databaseaccountnames | Nie | Nie | Nie |
> | databaseaccounts | Tak | Tak | Nie |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Domen | Tak | Tak | Nie |
> | generatessorequest | Nie | Nie | Nie |
> | topleveldomains | Nie | Nie | Nie |
> | validatedomainregistrationinformation | Nie | Nie | Nie |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | services | Tak | Tak | Nie |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Domen | Tak | Tak | Nie |
> | eventsubscriptions (opisy zdarzeń) | Nie — nie można go przenosić niezależnie, ale jest automatycznie przenoszone z subskrybowany zasób. | Nie — nie można go przenosić niezależnie, ale jest automatycznie przenoszone z subskrybowany zasób. | Nie |
> | extensiontopics | Nie | Nie | Nie |
> | partnernamespaces | Tak | Tak | Nie |
> | partnerregistrations | Nie | Nie | Nie |
> | partnertopics | Tak | Tak | Nie |
> | systemtopics | Tak | Tak | Nie |
> | Tematy | Tak | Tak | Nie |
> | typy tematów | Nie | Nie | Nie |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Klastrów | Tak | Tak | Nie |
> | Obszary nazw | Tak | Tak | Tak (z szablonem)<br/><br/> [Przenoszenie przestrzeni nazw centrum zdarzeń do innego regionu](../../event-hubs/move-across-regions.md) |
> | sku | Nie | Nie | Nie |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | experimentworkspaces | Nie | Nie | Nie |

## <a name="microsoftfalcon"></a>Microsoft.Zygmej

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Obszary nazw | Tak | Tak | Nie |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | featureproviders | Nie | Nie | Nie |
> | funkcje | Nie | Nie | Nie |
> | dostawców | Nie | Nie | Nie |
> | subscriptionfeatureregistrations | Nie | Nie | Nie |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Konta | Nie | Nie | Nie |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | automanagedaccounts | Nie | Nie | Nie |
> | automanagedvmconfigurationprofiles | Nie | Nie | Nie |
> | guestconfigurationassignments | Nie | Nie | Nie |
> | Oprogramowania | Nie | Nie | Nie |
> | softwareupdateprofile | Nie | Nie | Nie |
> | softwareupdates | Nie | Nie | Nie |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | hanainstances | Nie | Nie | Nie |
> | sapmonitors | Nie | Nie | Nie |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | dedicatedhsms | Nie | Nie | Nie |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!IMPORTANT]
> Klastry usługi HDInsight można przenieść do nowej subskrypcji lub grupy zasobów. Nie można jednak przechodzić między subskrypcjami zasobów sieciowych połączonych z klastrem usługi HDInsight (na przykład sieci wirtualnej, karty sieciowej lub usługi równoważenia obciążenia). Ponadto nie można przenieść do nowej grupy zasobów karty sieciowej dołączonej do maszyny wirtualnej klastra.
>
> Podczas przenoszenia klastra usługi HDInsight do nowej subskrypcji należy najpierw przenieść inne zasoby (takie jak konto magazynu). Następnie należy samodzielnie przenieść klaster usługi HDInsight.

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Klastrów | Tak | Tak | Nie |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | services | Tak | Tak | Nie |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Maszyny | Tak | Tak | Nie |
> | maszyny/rozszerzenia | Tak | Tak | Nie |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | menedżerowie danych | Tak | Tak | Nie |

## <a name="microsofthybridnetwork"></a>Microsoft.HybridNetwork

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | devices | Nie | Nie | Nie |
> | vnfs | Nie | Nie | Nie |

## <a name="microsofthydra"></a>Microsoft.Przejmij

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Składniki | Nie | Nie | Nie |
> | networkscopes | Nie | Nie | Nie |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Zadania | Tak | Tak | Nie |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!IMPORTANT]
> Upewnij się, że przejście do nowej subskrypcji nie przekracza [limitów przydziału subskrypcji.](azure-subscription-service-limits.md#azure-monitor-limits)

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Konta | **Oczekujące** | **Oczekujące** | Nie. [Dowiedz się więcej](../../azure-monitor/faq.md#how-do-i-move-an-application-insights-resource-to-a-new-region). |
> | grupy akcji | Tak | Tak | Nie |
> | activitylogalerts | Nie | Nie | Nie |
> | alertrules (alerty) | Tak | Tak | Nie |
> | ustawienia autoskalowania | Tak | Tak | Nie |
> | punkt odniesienia | Nie | Nie | Nie |
> | Składniki | Tak | Tak | Nie |
> | datacollectionrules | Nie | Nie | Nie |
> | diagnosticsettings | Nie | Nie | Nie |
> | diagnosticsettingscategories | Nie | Nie | Nie |
> | eventcategories | Nie | Nie | Nie |
> | typy zdarzeń | Nie | Nie | Nie |
> | extendeddiagnosticsettings | Nie | Nie | Nie |
> | guestdiagnosticsettings | Nie | Nie | Nie |
> | listmigrationdate | Nie | Nie | Nie |
> | logdefinitions | Nie | Nie | Nie |
> | logprofiles | Nie | Nie | Nie |
> | dzienniki | Nie | Nie | Nie |
> | metryki | Nie | Nie | Nie |
> | linie bazowe metryk | Nie | Nie | Nie |
> | metricbatch | Nie | Nie | Nie |
> | metricdefinitions | Nie | Nie | Nie |
> | metricnamespaces | Nie | Nie | Nie |
> | metrics | Nie | Nie | Nie |
> | migratealertrules | Nie | Nie | Nie |
> | migratetonewpricingmodel | Nie | Nie | Nie |
> | myworkbooks | Nie | Nie | Nie |
> | grupy powiadomień | Nie | Nie | Nie |
> | privatelinkscopes | Nie | Nie | Nie |
> | rollbacktolegacypricingmodel | Nie | Nie | Nie |
> | scheduledqueryrules | Tak | Tak | Nie |
> | topology | Nie | Nie | Nie |
> | transakcje | Nie | Nie | Nie |
> | vminsightsonboardingstatuses | Nie | Nie | Nie |
> | webtests | Tak | Tak | Nie |
> | webtests / gettestresultfile | Nie | Nie | Nie |
> | skoroszyty | Tak | Tak | Nie |
> | workbooktemplates | Tak | Tak | Nie |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | apptemplates | Nie | Nie | Nie |
> | iotapps | Tak | Tak | Nie |

## <a name="microsoftiothub"></a>Microsoft.IoTHub

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | iothub | **Oczekujące** | **Oczekujące** | Tak (klonowanie centrum) <br/><br/> [Klonowanie centrum IoT do innego regionu](../../iot-hub/iot-hub-how-to-clone.md) |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Wykres | Tak | Tak | Nie |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!IMPORTANT]
> Magazynów kluczy używanych do szyfrowania dysków nie można przenosić do grupy zasobów w tej samej subskrypcji ani między subskrypcjami.

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | deletedvaults | Nie | Nie | Nie |
> | hsmpools | Nie | Nie | Nie |
> | managedhsms | Nie | Nie | Nie |
> | Sklepienia | Tak | Tak | Nie |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | connectedclusters | Tak | Tak | Nie |
> | zarejestrowanesubskrypcje | Nie | Nie | Nie |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | sourcecontrolconfigurations | Nie | Nie | Nie |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Klastrów | Tak | Tak | Nie |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | konta laboratorium | Nie | Nie | Nie |
> | users | Nie | Nie | Nie |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Konta | Nie | Nie | Nie |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Konta | Nie | Nie | Nie, jest to usługa globalna. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | hostingenvironments | Nie | Nie | Nie |
> | integrationaccounts | Tak | Tak | Nie |
> | integrationserviceenvironments | Tak | Nie | Nie |
> | integrationserviceenvironments/managedapis | Tak | Nie | Nie |
> | isolatedenvironments | Nie | Nie | Nie |
> | Przepływy pracy | Tak | Tak | Nie |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | commitmentplans | Nie | Nie | Nie |
> | Webservices | Tak | Nie | Nie |
> | obszary robocze | Tak | Tak | Nie |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | operationalizationclusters | Nie | Nie | Nie |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Konta | Nie | Nie | Nie |
> | konta zespołu | Nie | Nie | Nie |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Konta | Nie | Nie | Nie |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | obszary robocze | Nie | Nie | Nie |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | configurationassignments | Nie | Nie | Tak. [Dowiedz się więcej](../../virtual-machines/move-region-maintenance-configuration.md) |
> | maintenanceconfigurations | Tak | Tak | Tak. [Dowiedz się więcej](../../virtual-machines/move-region-maintenance-configuration-resources.md) |
> | aktualizacje | Nie | Nie | Nie |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Tożsamości | Nie | Nie | Nie |
> | userassignedidentities | Nie | Nie | Nie |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | managednetworks | Nie | Nie | Nie |
> | managednetworks / managednetworkgroups | Nie | Nie | Nie |
> | managednetworks / managednetworkpeeringpolicies | Nie | Nie | Nie |
> | powiadomienie | Nie | Nie | Nie |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | marketplaceregistrationdefinitions | Nie | Nie | Nie |
> | registrationassignments | Nie | Nie | Nie |
> | registrationdefinitions | Nie | Nie | Nie |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | getentities | Nie | Nie | Nie |
> | managementgroups | Nie | Nie | Nie |
> | grupy zarządzania/ustawienia | Nie | Nie | Nie |
> | zasoby | Nie | Nie | Nie |
> | starttenantbackfill | Nie | Nie | Nie |
> | tenantbackfillstatus | Nie | Nie | Nie |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Konta | Tak | Tak | Nie, Azure Maps jest usługą geoprzestrzenną. |
> | accounts/privateatlases | Tak | Tak | Nie |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Oferuje | Nie | Nie | Nie |
> | offertypes (typy ofert) | Nie | Nie | Nie |
> | privategalleryitems | Nie | Nie | Nie |
> | privatestoreclient | Nie | Nie | Nie |
> | privatestores | Nie | Nie | Nie |
> | Produktów | Nie | Nie | Nie |
> | Wydawców | Nie | Nie | Nie |
> | zarejestruj | Nie | Nie | Nie |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | classicdevservices | Nie | Nie | Nie |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Umów | Nie | Nie | Nie |
> | typy ofert | Nie | Nie | Nie |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | mediaservices | Tak | Tak | Nie |
> | mediaservices/liveevents | Tak | Tak | Nie |
> | mediaservices/streamingendpoints | Tak | Tak | Nie |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | appclusters | Nie | Nie | Nie |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | assessmentprojects | Nie | Nie | Nie |
> | migrateprojects | Nie | Nie | Nie |
> | movecollections | Nie | Nie | Nie |
> | Projektów | Nie | Nie | Nie |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ---------- |
> | holographicsbroadcastaccounts | Nie | Nie | Nie |
> | objectunderstandingaccounts | Nie | Nie | Nie |
> | remoterenderingaccounts | Tak | Tak | Nie |
> | spatialanchorsaccounts | Tak | Tak | Nie |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | netappaccounts | Nie | Nie | Nie |
> | netappaccounts /capacitypools | Nie | Nie | Nie |
> | netappaccounts / capacitypools / volumes | Nie | Nie | Nie |
> | netappaccounts / capacitypools / woluminy / mounttargets | Nie | Nie | Nie |
> | netappaccounts / capacitypools / woluminy / migawki | Nie | Nie | Nie |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!IMPORTANT]
> Zobacz [Wskazówki dotyczące przenoszenia sieci.](./move-limitations/networking-move-limitations.md)

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | applicationgateways | Nie | Nie | Nie |
> | applicationgatewaywebapplicationfirewallpolicies | Nie | Nie | Nie |
> | applicationsecuritygroups | Tak | Tak | Nie |
> | azurefirewalls | Nie | Nie | Nie |
> | bastionhosts | Nie | Nie | Nie |
> | bgpservicecommunities | Nie | Nie | Nie |
> | Połączenia | Tak | Tak | Nie |
> | ddoscustompolicies | Tak | Tak | Nie |
> | ddosprotectionplans | Nie | Nie | Nie |
> | dnszones | Tak | Tak | Nie |
> | expressroutecircuits | Nie | Nie | Nie |
> | expressroutegateways | Nie | Nie | Nie |
> | expressrouteserviceproviders | Nie | Nie | Nie |
> | firewallpolicies | Tak | Tak | Nie |
> | frontdoory | Nie | Nie | Nie |
> | ipallocations | Tak | Tak | Nie |
> | ipgroups | Tak | Tak | Nie |
> | równoważenie obciążenia | Tak — podstawowa sku<br> Tak — standardowa sku | Tak — podstawowa sku<br>Nie — standardowa sku | Tak <br/><br/> Użyj [Azure Resource Mover,](../../resource-mover/tutorial-move-region-virtual-machines.md) aby przenieść wewnętrzne i zewnętrzne usługi równoważenia obciążenia. |
> | localnetworkgateways | Tak | Tak | Nie |
> | natgateways | Nie | Nie | Nie |
> | networkexperimentprofiles | Nie | Nie | Nie |
> | networkintentpolicies | Tak | Tak | Nie |
> | networkinterfaces | Tak | Tak | Tak <br/><br/> Użyj [Azure Resource Mover,](../../resource-mover/tutorial-move-region-virtual-machines.md) aby przenieść karty sieciowe. |
> | networkprofiles | Nie | Nie | Nie |
> | networksecuritygroups | Tak | Tak | Tak <br/><br/> Użyj [Azure Resource Mover,](../../resource-mover/tutorial-move-region-virtual-machines.md) aby przenieść sieciowe grupy zabezpieczeń (NNGS). |
> | networkwatchers | Nie | Nie | Nie |
> | networkwatchers/connectionmonitors | Tak | Nie | Nie |
> | networkwatchers/flowlogs | Tak | Nie | Nie |
> | networkwatchers/pingmeshes | Tak | Nie | Nie |
> | p2svpngateways | Nie | Nie | Nie |
> | privatednszones | Tak | Tak | Nie |
> | privatednszones / virtualnetworklinks | Tak | Tak | Nie |
> | privatednszonesinternal | Nie | Nie | Nie |
> | privateendpointredirectmaps | Nie | Nie | Nie |
> | privateendpoints | Nie | Nie | Nie |
> | privatelinkservices | Nie | Nie | Nie |
> | publicipaddresses | Tak — podstawowa sku<br>Tak — standardowa sku | Tak — podstawowa sku<br>Nie — standardowa sku | Tak<br/><br/> Użyj [Azure Resource Mover,](../../resource-mover/tutorial-move-region-virtual-machines.md) aby przenieść publiczne adresy IP. |
> | publicipprefixes | Tak | Tak | Nie |
> | routefilters | Nie | Nie | Nie |
> | routetables | Tak | Tak | Nie |
> | securitypartnerproviders | Tak | Tak | Nie |
> | serviceendpointpolicies | Tak | Tak | Nie |
> | trafficmanagergeographichierarchies | Nie | Nie | Nie |
> | trafficmanagerprofiles | Tak | Tak | Nie |
> | trafficmanagerprofiles /heatmaps | Nie | Nie | Nie |
> | trafficmanagerusermetricskeys | Nie | Nie | Nie |
> | virtualhubs | Nie | Nie | Nie |
> | virtualnetworkgateways | Tak | Tak | Nie |
> | virtualnetworks | Tak | Tak | Nie |
> | virtualnetworktaps | Nie | Nie | Nie |
> | virtualrouters (trasy wirtualne) | Tak | Tak | Nie |
> | łabędzie wirtualne | Nie | Nie |
> | vpngateways (Virtual WAN) | Nie | Nie | Nie |
> | vpnserverconfigurations | Nie | Nie | Nie |
> | vpnsites (Virtual WAN) | Nie | Nie | Nie |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Obszary nazw | Tak | Tak | Nie |
> | namespaces/notificationhubs | Tak | Tak | Nie |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | osnamespaces | Tak | Tak | Nie |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | hypervsites | Nie | Nie | Nie |
> | importites | Nie | Nie | Nie |
> | serversites | Nie | Nie | Nie |
> | vmwaresites | Nie | Nie | Nie |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!IMPORTANT]
> Upewnij się, że przejście do nowej subskrypcji nie przekracza [limitów przydziału subskrypcji.](azure-subscription-service-limits.md#azure-monitor-limits)
>
> Nie można przenosić obszarów roboczych, które mają połączone konto automatyzacji. Przed rozpoczęciem operacji przenoszenia należy odłączyć wszystkie konta automatyzacji.

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Klastrów | Nie | Nie | Nie |
> | deletedworkspaces | Nie | Nie | Nie |
> | linktargets | Nie | Nie | Nie |
> | storageinsightconfigs | Nie | Nie | Nie |
> | obszary robocze | Tak | Tak | Nie |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | skojarzenia zarządzania | Nie | Nie | Nie |
> | managementconfigurations (konfiguracje zarządzania) | Tak | Tak | Nie |
> | rozwiązania | Tak | Tak | Nie |
> | widoki | Tak | Tak | Nie |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | starsze wersje | Nie | Nie | Nie |
> | peerasns | Nie | Nie | Nie |
> | lokalizacja komunikacji równorzędnej | Nie | Nie | Nie |
> | komunikacja równorzędna | Nie | Nie | Nie |
> | peeringservicecountries | Nie | Nie | Nie |
> | lokalizacja usługi komunikacji równorzędnej | Nie | Nie | Nie |
> | peeringserviceproviders | Nie | Nie | Nie |
> | usługi komunikacji równorzędnej | Nie | Nie | Nie |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | policyevents | Nie | Nie | Nie |
> | policystates | Nie | Nie | Nie |
> | policytrackedresources | Nie | Nie | Nie |
> | korygowanie | Nie | Nie | Nie |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Konsole programu | Nie | Nie | Nie |
> | pulpity nawigacyjne | Tak | Tak | Nie |
> | usersettings | Nie | Nie | Nie |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | workspacecollections | Tak | Tak | Nie |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Pojemności | Tak | Tak | Nie |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ---------- |
> | Konta | Nie | Nie | Nie |

## <a name="microsoftpurview"></a>Microsoft.Purview

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ---------- |
> | Konta | **Oczekujące** | **Oczekujące** | Nie |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | dostępne konta | Nie | Nie | Nie |
> | dostawcaregistrations | Nie | Nie | Nie |
> | Wdrożenia | Nie | Nie | Nie |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | obszary robocze | Nie | Nie | Nie |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!IMPORTANT]
> Zobacz [Recovery Services move guidance (Wskazówki dotyczące przenoszenia usług Recovery Services).](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | uprawnienia replikacjiresults | Nie | Nie | Nie |
> | Sklepienia | Tak | Tak | Nie.<br/><br/> Przenoszenie magazynów usługi Recovery Services dla Azure Backup między regionami świadczenia usługi Azure nie jest obsługiwane.<br/><br/> W magazynach usługi Recovery Services dla Azure Site Recovery można wyłączyć i ponownie utworzyć [magazyn](../../site-recovery/move-vaults-across-regions.md) w regionie docelowym. |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | openshiftclusters | Nie | Nie | Nie |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Obszary nazw | Tak | Tak | Nie |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Kwerendy | Tak | Tak | Nie |
> | resourcechangedetails | Nie | Nie | Nie |
> | resourcechanges | Nie | Nie | Nie |
> | zasoby | Nie | Nie | Nie |
> | resourceshistory | Nie | Nie | Nie |
> | subscriptionsstatus | Nie | Nie | Nie |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | childresources | Nie | Nie | Nie |
> | emergingissues | Nie | Nie | Nie |
> | zdarzenia | Nie | Nie | Nie |
> | metadane | Nie | Nie | Nie |
> | powiadomienia | Nie | Nie | Nie |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Wdrożeń | Nie | Nie | Nie |
> | deploymentscripts | Nie | Nie | Tak<br/><br/>[Przenoszenie zasobów Microsoft.Resources do nowego regionu](microsoft-resources-move-regions.md) |
> | deploymentscripts/logs | Nie | Nie | Nie |
> | Linki | Nie | Nie | Nie |
> | dostawców | Nie | Nie | Nie |
> | resourcegroups | Nie | Nie | Nie |
> | zasoby | Nie | Nie | Nie |
> | Subskrypcji | Nie | Nie | Nie |
> | tags | Nie | Nie | Nie |
> | templatespecs | Nie | Nie | Tak<br/><br/>[Przenoszenie zasobów Microsoft.Resources do nowego regionu](microsoft-resources-move-regions.md) |
> | templatespecs/versions | Nie | Nie | Nie |
> | Najemców | Nie | Nie | Nie |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | aplikacje | Tak | Nie | Nie |
> | saasresources | Nie | Nie | Nie |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!IMPORTANT]
> W jednej operacji nie można przenieść kilku zasobów wyszukiwania w różnych regionach. Zamiast tego przenieś je w oddzielnych operacjach.

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | resourcehealthmetadata | Nie | Nie | Nie |
> | searchservices | Tak | Tak | Nie |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | adaptivenetworkhardenings | Nie | Nie | Nie |
> | advancedthreatprotectionsettings | Nie | Nie | Nie |
> | alerts | Nie | Nie | Nie |
> | dozwolone połączenia | Nie | Nie | Nie |
> | applicationwhitelistings | Nie | Nie | Nie |
> | assessmentmetadata | Nie | Nie | Nie |
> | oceny | Nie | Nie | Nie |
> | autodismissalertsrules | Nie | Nie | Nie |
> | Automatyki | Tak | Tak | Nie |
> | ustawienia automatycznego aprowizowania | Nie | Nie | Nie |
> | wyniki zgodności | Nie | Nie | Nie |
> | zgodność | Nie | Nie | Nie |
> | datacollectionagents | Nie | Nie | Nie |
> | devicesecuritygroups | Nie | Nie | Nie |
> | discoveredsecuritysolutions | Nie | Nie | Nie |
> | externalsecuritysolutions (rozwiązania związane z zabezpieczeniami zewnętrznymi) | Nie | Nie | Nie |
> | informationprotectionpolicies | Nie | Nie | Nie |
> | iotsecuritysolutions | Tak | Tak | Nie |
> | iotsecuritysolutions/analyticsmodels | Nie | Nie | Nie |
> | iotsecuritysolutions / analyticsmodels / aggregatedalerts | Nie | Nie | Nie |
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | Nie | Nie | Nie |
> | jitnetworkaccesspolicies | Nie | Nie | Nie |
> | policies | Nie | Nie | Nie |
> | cennik | Nie | Nie | Nie |
> | zgodność z przepisami | Nie | Nie | Nie |
> | regulatorycompliancestandards/regulatorycompliancecontrols | Nie | Nie | Nie |
> | regulatorycompliancestandards / regulatorycompliancecontrols / regulatorycomplianceassessments | Nie | Nie | Nie |
> | securitycontacts | Nie | Nie | Nie |
> | securitysolutions (rozwiązania zabezpieczeń) | Nie | Nie | Nie |
> | securitysolutionsreferencedata | Nie | Nie | Nie |
> | securitystatuses (statystyki zabezpieczeń) | Nie | Nie | Nie |
> | securitystatusessummaries | Nie | Nie | Nie |
> | servervulnerabilityassessments | Nie | Nie | Nie |
> | ustawienia | Nie | Nie | Nie |
> | podoceny | Nie | Nie | Nie |
> | zadania | Nie | Nie | Nie |
> | Topologii | Nie | Nie | Nie |
> | workspacesettings | Nie | Nie | Nie |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Agregacji | Nie | Nie | Nie |
> | alerty | Nie | Nie | Nie |
> | alertruletemplates | Nie | Nie | Nie |
> | automationrules (reguły automatyzacji) | Nie | Nie | Nie |
> | zakładki | Nie | Nie | Nie |
> | Przypadkach | Nie | Nie | Nie |
> | połączenia danych | Nie | Nie | Nie |
> | Podmioty | Nie | Nie | Nie |
> | entityqueries | Nie | Nie | Nie |
> | zdarzenia | Nie | Nie | Nie |
> | officeconsents | Nie | Nie | Nie |
> | ustawienia | Nie | Nie | Nie |
> | threatintelligence | Nie | Nie | Nie |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | consoleservices (usługi konsoli) | Nie | Nie | Nie |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Bramy | Nie | Nie | Nie |
> | Węzłów | Nie | Nie | Nie |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Obszary nazw | Tak | Tak | Nie |
> | premiummessagingregions | Nie | Nie | Nie |
> | sku | Nie | Nie | Nie |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | aplikacje | Nie | Nie | Nie |
> | Klastrów | Tak | Tak | Nie |
> | containergroups | Nie | Nie | Nie |
> | containergroupsets | Nie | Nie | Nie |
> | edgeclusters | Nie | Nie | Nie |
> | managedclusters | Nie | Nie | Nie |
> | Sieci | Nie | Nie | Nie |
> | secretstores | Nie | Nie | Nie |
> | volumes | Nie | Nie | Nie |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | aplikacje | Tak | Tak | Nie |
> | containergroups | Nie | Nie | Nie |
> | Bramy | Tak | Tak | Nie |
> | Sieci | Tak | Tak | Nie |
> | wpisy tajne | Tak | Tak | Nie |
> | volumes | Tak | Tak | Nie |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Wdrożenia | Nie | Nie | Nie |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Signalr | Tak | Tak | Nie |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | hybridusebenefits | Nie | Nie | Nie |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | applicationdefinitions | Nie | Nie | Nie |
> | aplikacje | Nie | Nie | Nie |
> | jitrequests | Nie | Nie | Nie |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!IMPORTANT]
> Baza danych i serwer muszą znajdować się w tej samej grupie zasobów. Podczas przenoszenia serwera SQL wszystkie jego bazy danych również są przenoszone. To zachowanie dotyczy Azure SQL Database i Azure Synapse Analytics baz danych.

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | puli wystąpień | Nie | Nie | Nie |
> | locations | Tak | Tak | Nie |
> | managedinstances | Nie | Nie | Tak <br/><br/> [Dowiedz się więcej](../../azure-sql/database/move-resources-across-regions.md) o przenoszeniu wystąpień zarządzanych między regionami. |
> | managedinstances/databases | Nie | Nie | Tak |
> | Serwerów | Tak | Tak |Tak |
> | serwery/bazy danych | Tak | Tak | Tak <br/><br/> [Dowiedz się więcej](../../azure-sql/database/move-resources-across-regions.md) o przenoszeniu baz danych między regionami.<br/><br/> [Dowiedz się więcej](../../resource-mover/tutorial-move-region-sql.md) o używaniu Azure Resource Mover do przenoszenia Azure SQL baz danych.  |
> | serwery/bazy danych/backuplongtermretentionpolicies | Tak | Tak | Nie |
> | serwery/pule elastyczne | Tak | Tak | Tak <br/><br/> [Dowiedz się więcej o](../../azure-sql/database/move-resources-across-regions.md) przenoszeniu pul elastycznych między regionami.<br/><br/> [Dowiedz się więcej](../../resource-mover/tutorial-move-region-sql.md) na temat używania Azure Resource Mover do przenoszenia Azure SQL pul elastycznych.  |
> | serwery/konta zadań | Tak | Tak | Nie |
> | serwery/serwery jobagents | Tak | Tak | Nie |
> | virtualclusters | Tak | Tak | Tak |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | sqlvirtualmachinegroups | Tak | Tak | Nie |
> | sqlvirtualmachines | Tak | Tak | Nie |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | konta magazynu | Tak | Tak | Tak<br/><br/> [Przenoszenie konta usługi Azure Storage do innego regionu](../../storage/common/storage-account-move.md) |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Buforuje | Nie | Nie | Nie |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | storagesyncservices | Tak | Tak | Nie |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | storagesyncservices | Nie | Nie | Nie |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | storagesyncservices | Nie | Nie | Nie |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Menedżerów | Nie | Nie | Nie |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!IMPORTANT]
> Stream Analytics nie można przenosić zadań w stanie uruchomienia.

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Klastrów | Nie | Nie | Nie |
> | streamingjobs | Tak | Tak | Nie |

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Środowiskach | Nie | Nie | Nie |
> | Wystąpień | Nie | Nie | Nie |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Subskrypcji | Nie | Nie | Nie |

## <a name="microsoftsupport"></a>Microsoft.Support

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | services | Nie | Nie | Nie |
> | supporttickets | Nie | Nie | Nie |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | obszary robocze | Nie | Nie | Nie |
> | obszary robocze/bigdatapools | Nie | Nie | Nie |
> | obszary robocze/sqlpools | Nie | Nie | Nie |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Środowiskach | Tak | Tak | Nie |
> | środowiska/źródła zdarzeń | Tak | Tak | Nie |
> | środowiska/zestawy danych referencyjnych | Tak | Tak | Nie |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Sklepy | Tak | Tak | Nie |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | imagetemplates | Nie | Nie | Nie |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

> [!IMPORTANT]
> Aby zmienić subskrypcję usługi Azure DevOps, zobacz Zmienianie subskrypcji [platformy Azure używanej do rozliczeń](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | account | Nie | Nie | Nie |
> | konto/rozszerzenie | Nie | Nie | Nie |
> | konto/projekt | Nie | Nie | Nie |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | arczones | Nie | Nie | Nie |
> | resourcepools | Nie | Nie | Nie |
> | centra wirtualne | Nie | Nie | Nie |
> | virtualmachines | Nie | Nie | Nie |
> | virtualmachinetemplates | Nie | Nie | Nie |
> | virtualnetworks | Nie | Nie | Nie |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | dedykowane węzły chmurowe | Nie | Nie | Nie |
> | dedicatedcloudservices | Nie | Nie | Nie |
> | virtualmachines | Nie | Nie | Nie |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | devices | Nie | Nie | Nie |
> | vnfs | Nie | Nie | Nie |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Konta | Nie | Nie | Nie |
> | Plany | Nie | Nie | Nie |
> | zarejestrowanesubskrypcje | Nie | Nie | Nie |

## <a name="microsoftweb"></a>Microsoft.Web

> [!IMPORTANT]
> Zobacz [App Service wskazówki dotyczące przenoszenia.](./move-limitations/app-service-move-limitations.md)

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | availablestacks | Nie | Nie | Nie |
> | billingmeters | Nie | Nie | Nie |
> | certyfikaty | Nie | Tak | Nie |
> | bramy połączeń | Tak | Tak | Nie |
> | Połączenia | Tak | Tak | Nie |
> | customapis | Tak | Tak | Nie |
> | usuniętewitryny | Nie | Nie | Nie |
> | deploymentlocations | Nie | Nie | Nie |
> | georegiony | Nie | Nie | Nie |
> | hostingenvironments | Nie | Nie | Nie |
> | kubeenvironments | Tak | Tak | Nie |
> | publishingusers | Nie | Nie | Nie |
> | zalecenia | Nie | Nie | Nie |
> | resourcehealthmetadata | Nie | Nie | Nie |
> | środowiska uruchomieniowe | Nie | Nie | Nie |
> | farmy serwerów | Tak | Tak | Nie |
> | serverfarms / eventgridfilters | Nie | Nie | Nie |
> | lokacje | Tak | Tak | Nie |
> | sites/premieraddons | Tak | Tak | Nie |
> | lokacje/miejsca | Tak | Tak | Nie |
> | sourcecontrols (kontrolery źródła) | Nie | Nie | Nie |
> | staticsites | Nie | Nie | Nie |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | multipleactivationkeys | Nie | Nie | Nie |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | deviceservices | Nie | Nie | Nie |

## <a name="microsoftworkloadbuilder"></a>Microsoft.WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Obciążeń | Nie | Nie | Nie |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Typ zasobu | Grupa zasobów | Subskrypcja | Przenoszenie regionów |
> | ------------- | ----------- | ---------- | ----------- |
> | Składniki | Nie | Nie | Nie |
> | componentssummary | Nie | Nie | Nie |
> | monitorinstances | Nie | Nie | Nie |
> | monitorinstancessummary | Nie | Nie | Nie |
> | Monitory | Nie | Nie | Nie |

## <a name="third-party-services"></a>Usługi innych firm

Usługi innych firm obecnie nie obsługują operacji przenoszenia.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać polecenia służące do przenoszenia zasobów, zobacz [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](move-resource-group-and-subscription.md).
- [Dowiedz się więcej](../../resource-mover/overview.md) o usłudze Resource Mover.
- Aby uzyskać te same dane co plik wartości rozdzielanych przecinkami, pobierz [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
