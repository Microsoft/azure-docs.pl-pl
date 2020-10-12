---
title: Zasoby bez limitu liczby 800
description: Wyświetla listę typów zasobów platformy Azure, które mogą mieć więcej niż 800 wystąpień w grupie zasobów.
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: e7a15607b144f1d7916f310948f15dc28d76a205
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91330761"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>Zasoby nieograniczone do 800 wystąpień na grupę zasobów

Domyślnie można wdrożyć do 800 wystąpień typu zasobu w każdej grupie zasobów. Niektóre typy zasobów są jednak wykluczone z limitu wystąpienia 800. W tym artykule wymieniono typy zasobów platformy Azure, które mogą mieć więcej niż 800 wystąpień w grupie zasobów. Wszystkie inne typy zasobów są ograniczone do 800 wystąpień.

W przypadku niektórych typów zasobów należy skontaktować się z pomocą techniczną, aby ograniczyć liczbę wystąpień 800. Te typy zasobów zostały wymienione w tym artykule.


## <a name="microsoftautomation"></a>Microsoft. Automation

* automationAccounts

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

* edgeSubscriptions
* linkedSubscriptions
* rejestracje
* rejestracje/customerSubscriptions
* rejestracje/produkty

## <a name="microsoftbotservice"></a>Microsoft. BotService

* botServices — domyślnie ograniczone do 800 wystąpień. Ten limit można zwiększyć, kontaktując się z pomocą techniczną.

## <a name="microsoftcompute"></a>Microsoft.Compute

* dysku
* znajduj
* Galerie/obrazy
* Galerie/obrazy/wersje
* images
* Migawki
* virtualMachineScaleSets — domyślnie ograniczone do 800 wystąpień. Ten limit można zwiększyć, kontaktując się z pomocą techniczną.
* virtualMachines

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

* containerGroups

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

* rejestry/buildTasks
* rejestry/buildTasks/listSourceRepositoryProperties
* rejestry/buildTasks/kroki
* rejestry/buildTasks/etapy/listBuildArguments
* rejestry/eventGridFilters
* rejestry/replikacje
* rejestry/zadania
* rejestry/elementy webhook

## <a name="microsoftd365customerinsights"></a>Microsoft. D365CustomerInsights

* Liczba

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

* serwerem

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

* flexibleServers
* serwerem

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

* flexibleServers
* serverGroups
* serwerem
* serversv2

## <a name="microsoftdevtestlab"></a>Microsoft. wspólny

* Uruchamianie

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

* services

## <a name="microsofteventhub"></a>Microsoft. EventHub

* oparty
* przestrzeni

## <a name="microsoftexperimentation"></a>Microsoft. eksperymentowanie

* experimentWorkspaces

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

* autoManagedVmConfigurationProfiles
* configurationProfileAssignments
* guestConfigurationAssignments
* programy
* softwareUpdateProfile
* softwareUpdates

## <a name="microsoftinsights"></a>Microsoft. Insights

* metricalerts

## <a name="microsoftlogic"></a>Microsoft. Logic

* integrationAccounts
* przebieg

## <a name="microsoftmedia"></a>Microsoft. Media

* MediaServices/liveEvents

## <a name="microsoftnetapp"></a>Microsoft. NetApp

* netAppAccounts
* netAppAccounts/capacityPools
* netAppAccounts/capacityPools/woluminy
* netAppAccounts/capacityPools/woluminy/mountTargets
* netAppAccounts/capacityPools/woluminy/migawki

## <a name="microsoftnetwork"></a>Microsoft.Network

* applicationGatewayWebApplicationFirewallPolicies
* applicationSecurityGroups
* bastionHosts
* ddosProtectionPlans
* dnszones
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
* dnszones/wszystkie
* dnszones/zestawy rekordów
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
* Adresów publicipaddress — domyślnie ograniczone do 800 wystąpień. Ten limit można zwiększyć, kontaktując się z pomocą techniczną.
* serviceEndpointPolicies
* trafficmanagerprofiles
* virtualNetworkTaps

## <a name="microsoftportalsdk"></a>Microsoft. PortalSdk

* rootResources

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

* workspaceCollections — domyślnie ograniczone do 800 wystąpień. Ten limit można zwiększyć, kontaktując się z pomocą techniczną.

## <a name="microsoftrelay"></a>Microsoft. Relay

* przestrzeni

## <a name="microsoftscheduler"></a>Microsoft. Scheduler

* jobcollections

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

* przestrzeni

## <a name="microsoftservicefabricmesh"></a>Microsoft. ServiceFabricMesh

* aplikacje
* containerGroups
* bram
* sieci
* wpisy tajne
* volumes

## <a name="microsoftstorage"></a>Microsoft.Storage

* storageAccounts

## <a name="microsoftweb"></a>Microsoft. Web

* apiManagementAccounts/interfejsy API
* lokacje

## <a name="next-steps"></a>Następne kroki

Aby uzyskać pełną listę przydziałów i limitów, zobacz [limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](azure-subscription-service-limits.md).
