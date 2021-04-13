---
title: Zasoby bez limitu 800
description: Wyświetla listę typów zasobów platformy Azure, które mogą mieć więcej niż 800 wystąpień w grupie zasobów.
ms.topic: conceptual
ms.date: 04/12/2021
ms.openlocfilehash: d132773ff35d53dc373c759326efc8179f4993d6
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366542"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>Zasoby nie są ograniczone do 800 wystąpień na grupę zasobów

Domyślnie można wdrożyć maksymalnie 800 wystąpień typu zasobu w każdej grupie zasobów. Jednak niektóre typy zasobów są wykluczone z limitu 800 wystąpień. W tym artykule wymieniono typy zasobów platformy Azure, które mogą mieć więcej niż 800 wystąpień w grupie zasobów. Wszystkie inne typy zasobów są ograniczone do 800 wystąpień.

W przypadku niektórych typów zasobów należy skontaktować się z pomocą techniczną, aby usunąć limit 800 wystąpień. Te typy zasobów zostały odnotowane w tym artykule.


## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

* resourceHealthAlertRules
* smartDetectorAlertRules

## <a name="microsoftautomation"></a>Microsoft.Automation

* automationAccounts

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

* edgeSubscriptions
* linkedSubscriptions
* Rejestracji
* rejestracje/subskrypcji klientów
* rejestracje/produkty

## <a name="microsoftbotservice"></a>Microsoft.BotService

* botServices — domyślnie ograniczona do 800 wystąpień. Ten limit można zwiększyć, kontaktując się z pomocą techniczną.

## <a name="microsoftcompute"></a>Microsoft.Compute

* Dysków
* Galerie
* galerie/obrazy
* galerie/obrazy/wersje
* images
* Migawki
* virtualMachineScaleSets — domyślnie ograniczona do 800 wystąpień. Ten limit można zwiększyć, kontaktując się z pomocą techniczną.
* virtualMachines

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

* containerGroups

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

* rejestry/buildTasks
* registries/buildTasks/listSourceRepositoryProperties
* rejestry/buildTasks/steps
* registries/buildTasks/steps/listBuildArguments
* registries/eventGridFilters
* rejestry/replikacje
* rejestry/zadania
* rejestry/webhook

## <a name="microsoftd365customerinsights"></a>Microsoft.D365CustomerInsights

* Wystąpień

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

* Serwerów

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

* flexibleServers
* Serwerów

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

* flexibleServers
* Servergroups
* serverGroupsv2
* Serwerów
* serversv2

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

* Harmonogramy

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

* services

## <a name="microsofteventhub"></a>Microsoft.EventHub

* Klastrów
* Obszary nazw

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

* experimentWorkspaces

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

* autoManagedVmConfigurationProfiles
* configurationProfileAssignments
* guestConfigurationAssignments
* Oprogramowania
* softwareUpdateProfile
* softwareUpdates

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

* maszyny — obsługuje do 5000 wystąpień
* maszyny/rozszerzenia — obsługuje nieograniczoną liczbę wystąpień rozszerzeń maszyn wirtualnych

## <a name="microsoftinsights"></a>microsoft.insights

* metricalerts

## <a name="microsoftlogic"></a>Microsoft.Logic

* integrationAccounts
* Przepływy pracy

## <a name="microsoftmedia"></a>Microsoft.Media

* mediaservices/liveEvents

## <a name="microsoftnetapp"></a>Microsoft.NetApp

* netAppAccounts
* netAppAccounts/capacityPools
* netAppAccounts/capacityPools/volumes
* netAppAccounts/capacityPools/volumes/mountTargets
* netAppAccounts/capacityPools/volumes/snapshots
* netAppAccounts/volumeGroups

## <a name="microsoftnetwork"></a>Microsoft.Network

* applicationGatewayWebApplicationFirewallPolicies
* applicationSecurityGroups
* bastionHosts
* ddosProtectionPlans
* strefy dns
* dnszones/A
* dnszones/AAAA
* dnszones/CAA
* dnszones/CNAME
* dnszones/MX
* dnszones/NS
* dnszones/PTR
* dnszones/SOA
* dnszones/SRV
* dnszones/TXT
* dnszones/all
* dnszones/recordsets
* networkIntentPolicies
* networkInterfaces
* privateDnsZones
* privateDnsZones/A
* privateDnsZones/AAAA
* privateDnsZones/CNAME
* privateDnsZones/MX
* privateDnsZones/PTR
* privateDnsZones/SOA
* privateDnsZones/SRV
* privateDnsZones/TXT
* privateDnsZones/wszystkie
* privateDnsZones/virtualNetworkLinks
* privateEndpoints
* privateLinkServices
* publicIPAddresses — domyślnie ograniczona do 800 wystąpień. Ten limit można zwiększyć, kontaktując się z pomocą techniczną.
* serviceEndpointPolicies
* trafficmanagerprofiles
* virtualNetworkTaps

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

* rootResources

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

* workspaceCollections — domyślnie ograniczone do 800 wystąpień. Ten limit można zwiększyć, kontaktując się z pomocą techniczną.

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

* autoScaleVCores — domyślnie ograniczona do 800 wystąpień. Ten limit można zwiększyć, kontaktując się z pomocą techniczną.
* pojemności — domyślnie ograniczone do 800 wystąpień. Ten limit można zwiększyć, kontaktując się z pomocą techniczną.

## <a name="microsoftrelay"></a>Microsoft.Relay

* Obszary nazw

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

* jobcollections

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

* Obszary nazw

## <a name="microsoftsingularity"></a>Microsoft.Singularity

* Konta
* accounts/accountQuotaPolicies
* accounts/groupPolicies
* konta/zadania
* accounts/storageContainers

## <a name="microsoftstorage"></a>Microsoft.Storage

* konta magazynu

## <a name="microsoftsql"></a>Microsoft.Sql

* serwery/bazy danych

## <a name="microsoftweb"></a>Microsoft.Web

* apiManagementAccounts/apis
* lokacje

## <a name="next-steps"></a>Następne kroki

Aby uzyskać pełną listę limitów przydziału i limitów, zobacz [Azure subscription and service limits, quotas, and constraints](azure-subscription-service-limits.md)(Limity, przydziały i ograniczenia usługi i subskrypcji platformy Azure).
