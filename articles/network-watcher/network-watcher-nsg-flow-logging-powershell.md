---
title: Zarządzanie dziennikami przepływu sieciowej grupy zabezpieczeń — Azure PowerShell
titleSuffix: Azure Network Watcher
description: Na tej stronie wyjaśniono, jak zarządzać dziennikami przepływu sieciowych grup zabezpieczeń w usłudze Azure Network Watcher przy użyciu programu PowerShell
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
ms.openlocfilehash: 771b4ce2999357d729c3ffe557b778cf62a5c0f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98010986"
---
# <a name="configuring-network-security-group-flow-logs-with-powershell"></a>Konfigurowanie dzienników przepływu sieciowych grup zabezpieczeń przy użyciu programu PowerShell

> [!div class="op_single_selector"]
> - [Witryna Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [Program PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-nsg-flow-logging-cli.md)
> - [Interfejs API REST](network-watcher-nsg-flow-logging-rest.md)

Dzienniki przepływu sieciowych grup zabezpieczeń są funkcją Network Watcher, która umożliwia wyświetlanie informacji dotyczących ruchu przychodzącego i wychodzącego IP za pomocą sieciowej grupy zabezpieczeń. Te dzienniki przepływów są zapisywane w formacie JSON i pokazują przepływy wychodzące i przychodzące dla każdej reguły, karta sieciowa przepływu ma zastosowanie do, 5-informacje o spójnej kolekcji przepływu (źródłowy/docelowy adres IP, port źródłowy/docelowy, protokół) i jeśli ruch był dozwolony lub zabroniony.

## <a name="register-insights-provider"></a>Rejestrowanie dostawcy usługi Insights

Aby rejestrowanie przepływu działało prawidłowo, dostawca **Microsoft. Insights** musi być zarejestrowany. Jeśli nie masz pewności, czy dostawca **Microsoft. Insights** został zarejestrowany, uruchom następujący skrypt.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs-and-traffic-analytics"></a>Włącz dzienniki przepływu sieciowych grup zabezpieczeń i Analiza ruchu

W poniższym przykładzie przedstawiono polecenie umożliwiające włączenie dzienników przepływu:

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

Określone konto magazynu nie może mieć skonfigurowanych reguł sieciowych, które ograniczają dostęp do sieci tylko do usług firmy Microsoft lub określonych sieci wirtualnych. Konto magazynu może być w tej samej lub innej subskrypcji platformy Azure niż sieciowej grupy zabezpieczeń, dla którego włączono dziennik przepływów. Jeśli używasz różnych subskrypcji, muszą one być skojarzone z tą samą dzierżawą Azure Active Directory. Konto używane dla każdej subskrypcji musi mieć [wymagane uprawnienia](required-rbac-permissions.md).

## <a name="disable-traffic-analytics-and-network-security-group-flow-logs"></a>Wyłącz dzienniki przepływu sieciowych Analiza ruchu i sieciowej grupy zabezpieczeń

Aby wyłączyć analizy ruchu i dzienniki przepływów, użyj następującego przykładu:

```powershell
#Disable Traffic Analaytics by removing -EnableTrafficAnalytics property
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceGUID -WorkspaceLocation $workspaceLocation

#Disable Flow Logging
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $false
```

## <a name="download-a-flow-log"></a>Pobierz dziennik przepływu

Lokalizacja magazynu dziennika przepływu jest definiowana podczas tworzenia. Wygodnym narzędziem do uzyskiwania dostępu do tych dzienników przepływów zapisanych na koncie magazynu jest Eksplorator usługi Microsoft Azure Storage, które można pobrać tutaj:  https://storageexplorer.com/

Jeśli określono konto magazynu, pliki dziennika przepływu są zapisywane na koncie magazynu w następującej lokalizacji:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

Aby uzyskać informacje na temat struktury dziennika, zobacz [Dziennik przepływu sieciowych grup zabezpieczeń — omówienie](network-watcher-nsg-flow-logging-overview.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [wizualizować dzienniki przepływu sieciowej grupy zabezpieczeń za pomocą usługi PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Dowiedz się, jak [wizualizować dzienniki przepływu sieciowej grupy zabezpieczeń za pomocą narzędzi open source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
