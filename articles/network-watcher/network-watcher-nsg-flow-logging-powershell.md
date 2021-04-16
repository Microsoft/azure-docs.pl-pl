---
title: Zarządzanie dziennikami przepływu sieciowych Azure PowerShell
titleSuffix: Azure Network Watcher
description: Na tej stronie wyjaśniono, jak zarządzać dziennikami przepływu sieciowych grup zabezpieczeń w usłudze Azure Network Watcher programie PowerShell
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 29340852cabcc77b7488f734a4677697b4a9b972
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535227"
---
# <a name="configuring-network-security-group-flow-logs-with-powershell"></a>Konfigurowanie dzienników przepływu sieciowej grupy zabezpieczeń przy użyciu programu PowerShell

> [!div class="op_single_selector"]
> - [Witryna Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [Program PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-nsg-flow-logging-cli.md)
> - [Interfejs API REST](network-watcher-nsg-flow-logging-rest.md)

Dzienniki przepływu sieciowej grupy zabezpieczeń są funkcją usługi Network Watcher która umożliwia wyświetlanie informacji o przychodzącym i wychodzącym ruchu IP za pośrednictwem sieciowej grupy zabezpieczeń. Te dzienniki przepływu są zapisywane w formacie JSON i pokazują przepływy wychodzące i przychodzące dla procesów, kartę sieciową, do których ma zastosowanie przepływ, 5-krotną informację o przepływie (źródłowy/docelowy adres IP, port źródłowy/docelowy, protokół) oraz informację o tym, czy ruch został dozwolony lub odrzucony.

Szczegółową specyfikację wszystkich poleceń dzienników przepływu sieciowych grup zabezpieczeń dla różnych wersji programu AzPowerShell można znaleźć [tutaj](https://docs.microsoft.com/powershell/module/az.network/#network-watcher)

## <a name="register-insights-provider"></a>Rejestrowanie dostawcy usługi Insights

Aby rejestrowanie przepływu działało pomyślnie, należy zarejestrować dostawcę **Microsoft.Insights.** Jeśli nie masz pewności, czy **dostawca Microsoft.Insights** jest zarejestrowany, uruchom następujący skrypt.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs-and-traffic-analytics"></a>Włączanie dzienników przepływu sieciowych grup zabezpieczeń i Analiza ruchu

Polecenie włączenia dzienników przepływu jest wyświetlane w poniższym przykładzie:

```powershell
$NW = Get-AzNetworkWatcher -ResourceGroupName NetworkWatcherRg -Name NetworkWatcher_westcentralus
$nsg = Get-AzNetworkSecurityGroup -ResourceGroupName nsgRG -Name nsgName
$storageAccount = Get-AzStorageAccount -ResourceGroupName StorageRG -Name contosostorage123
Get-AzNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $nsg.Id

#Traffic Analytics Parameters
$workspaceResourceId = "/subscriptions/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb/resourcegroups/trafficanalyticsrg/providers/microsoft.operationalinsights/workspaces/taworkspace"
$workspaceGUID = "cccccccc-cccc-cccc-cccc-cccccccccccc"
$workspaceLocation = "westeurope"

#Configure Version 1 Flow Logs
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 1

#Configure Version 2 Flow Logs, and configure Traffic Analytics
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2

#Configure Version 2 FLow Logs with Traffic Analytics Configured
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -EnableTrafficAnalytics -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceGUID -WorkspaceLocation $workspaceLocation

#Query Flow Log Status
Get-AzNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $nsg.Id
```

Określone konto magazynu nie może mieć skonfigurowanych reguł sieci, które ograniczają dostęp sieciowy tylko do usługi firmy Microsoft lub określonych sieci wirtualnych. Konto magazynu może być w tej samej lub innej subskrypcji platformy Azure niż sieciowa sieciowa żadna z tych sieci, dla których jest włączany dziennik przepływu. Jeśli używasz różnych subskrypcji, obie muszą być skojarzone z tą samą Azure Active Directory dzierżawą. Konto, które jest potrzebne dla każdej subskrypcji, musi mieć [niezbędne uprawnienia.](required-rbac-permissions.md)

## <a name="disable-traffic-analytics-and-network-security-group-flow-logs"></a>Wyłączanie Analiza ruchu i przepływu sieciowej grupy zabezpieczeń

Skorzystaj z poniższego przykładu, aby wyłączyć analizę ruchu i dzienniki przepływu:

```powershell
#Disable Traffic Analaytics by removing -EnableTrafficAnalytics property
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceGUID -WorkspaceLocation $workspaceLocation

#Disable Flow Logging
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $false
```

## <a name="download-a-flow-log"></a>Pobieranie dziennika przepływu

Lokalizacja przechowywania dziennika przepływu jest definiowana podczas tworzenia. Wygodne narzędzie do uzyskiwania dostępu do tych dzienników przepływu zapisanych na koncie magazynu Eksplorator usługi Microsoft Azure Storage, które można pobrać tutaj:  https://storageexplorer.com/

Jeśli określono konto magazynu, pliki dziennika przepływu są zapisywane na koncie magazynu w następującej lokalizacji:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

Aby uzyskać informacje o strukturze dziennika, odwiedź stronę Network Security Group Flow log Overview (Omówienie dziennika [przepływu sieciowej grupy zabezpieczeń)](network-watcher-nsg-flow-logging-overview.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [wizualizować dzienniki przepływu sieciowej organizacji zabezpieczeń przy użyciu usługi Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Dowiedz się, jak [wizualizować dzienniki przepływu sieciowej open source sieciowej](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
