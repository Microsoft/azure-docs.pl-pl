---
title: Zarządzanie dziennikami przepływu sieciowej organizacji zabezpieczeń — interfejs API REST platformy Azure
titleSuffix: Azure Network Watcher
description: Na tej stronie wyjaśniono, jak zarządzać dziennikami przepływu sieciowej grupy zabezpieczeń w usłudze Azure Network Watcher przy użyciu interfejsu API REST
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
ms.openlocfilehash: b45d066d0996aaba2a25500f8134085f5e9b6ffb
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535189"
---
# <a name="configuring-network-security-group-flow-logs-using-rest-api"></a>Konfigurowanie dzienników przepływu sieciowej grupy zabezpieczeń przy użyciu interfejsu API REST

> [!div class="op_single_selector"]
> - [Witryna Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [Program PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-nsg-flow-logging-cli.md)
> - [Interfejs API REST](network-watcher-nsg-flow-logging-rest.md)

Dzienniki przepływu sieciowej grupy zabezpieczeń są funkcją usługi Network Watcher która umożliwia wyświetlanie informacji o przychodzącym i wychodzącym ruchu IP za pośrednictwem sieciowej grupy zabezpieczeń. Te dzienniki przepływu są zapisywane w formacie JSON i pokazują przepływy wychodzące i przychodzące dla procesów, kartę sieciową, do których ma zastosowanie przepływ, 5-krotną informację o przepływie (źródłowy/docelowy adres IP, port źródłowy/docelowy, protokół) oraz informację o tym, czy ruch został dozwolony lub odrzucony.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

ArMclient służy do wywołania interfejsu API REST przy użyciu programu PowerShell. ArmClient znajduje się na chocolatey at [ARMClient na Chocolatey](https://chocolatey.org/packages/ARMClient). Szczegółowe specyfikacje interfejsu API REST dzienników przepływu sieciowych sieciowych sieci można znaleźć [tutaj](https://docs.microsoft.com/rest/api/network-watcher/flowlogs) 

W tym scenariuszu założono, że zostały już podjęte kroki opisane w Network Watcher [tworzenia](network-watcher-create.md) Network Watcher.

> [!Important]
> Na Network Watcher API REST wywołuje nazwę grupy zasobów w żądaniu URI grupy zasobów, która zawiera Network Watcher, a nie zasoby, na których są podejmowane akcje diagnostyczne.

## <a name="scenario"></a>Scenariusz

W scenariuszu, w którym opisano ten artykuł, pokazano, jak włączać i wyłączać dzienniki przepływu zapytań przy użyciu interfejsu API REST. Aby dowiedzieć się więcej na temat rejestrowania przepływu sieciowej grupy zabezpieczeń, odwiedź stronę Rejestrowanie przepływu sieciowej grupy zabezpieczeń [— omówienie.](network-watcher-nsg-flow-logging-overview.md)

W tym scenariuszu:

* Włączanie dzienników przepływu (wersja 2)
* Wyłączanie dzienników przepływu
* Stan dzienników przepływu zapytań

## <a name="log-in-with-armclient"></a>Logowanie przy użyciu usługi ARMClient

Zaloguj się do aplikacji armclient przy użyciu poświadczeń platformy Azure.

```powershell
armclient login
```

## <a name="register-insights-provider"></a>Rejestrowanie dostawcy usługi Insights

Aby rejestrowanie przepływu działało pomyślnie, należy zarejestrować dostawcę **Microsoft.Insights.** Jeśli nie masz pewności, czy **dostawca Microsoft.Insights** jest zarejestrowany, uruchom następujący skrypt.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
armclient post "https://management.azure.com//subscriptions/${subscriptionId}/providers/Microsoft.Insights/register?api-version=2016-09-01"
```

## <a name="enable-network-security-group-flow-logs"></a>Włączanie dzienników przepływu sieciowej grupy zabezpieczeń

Polecenie włączenia dzienników przepływu w wersji 2 jest pokazane w poniższym przykładzie. W wersji 1 zastąp pole "version" wartością "1":

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$targetUri = "" # example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
$storageId = "/subscriptions/00000000-0000-0000-0000-000000000000/{resourceGroupName/providers/Microsoft.Storage/storageAccounts/{saName}"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
    'properties': {
    'storageId': '${storageId}',
    'enabled': 'true',
    'retentionPolicy' : {
            days: 5,
            enabled: true
        },
    'format': {
        'type': 'JSON',
        'version': 2
    }
    }
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/configureFlowLog?api-version=2016-12-01" $requestBody
```

Odpowiedź zwrócona z poprzedniego przykładu jest następująca:

```json
{
  "targetResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}",
  "properties": {
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{saName}",
    "enabled": true,
    "retentionPolicy": {
      "days": 5,
      "enabled": true
    },
    "format": {
    "type": "JSON",
    "version": 2
    }
  }
}
```

## <a name="disable-network-security-group-flow-logs"></a>Wyłączanie dzienników przepływu sieciowej grupy zabezpieczeń

Użyj poniższego przykładu, aby wyłączyć dzienniki przepływów. Wywołanie jest takie samo jak włączenie dzienników przepływu, z tą **różnicą, że dla** włączonej właściwości ustawiono wartość false.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$targetUri = "" # example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
$storageId = "/subscriptions/00000000-0000-0000-0000-000000000000/{resourceGroupName/providers/Microsoft.Storage/storageAccounts/{saName}"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
    'properties': {
    'storageId': '${storageId}',
    'enabled': 'false',
    'retentionPolicy' : {
            days: 5,
            enabled: true
        },
    'format': {
        'type': 'JSON',
        'version': 2
    }
    }
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/configureFlowLog?api-version=2016-12-01" $requestBody
```

Odpowiedź zwrócona z poprzedniego przykładu jest następująca:

```json
{
  "targetResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}",
  "properties": {
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{saName}",
    "enabled": false,
    "retentionPolicy": {
      "days": 5,
      "enabled": true
    },
    "format": {
    "type": "JSON",
    "version": 2
    }
  }
}
```

## <a name="query-flow-logs"></a>Wykonywanie zapytań o dzienniki przepływu

Następujące wywołanie REST wysyła zapytanie o stan dzienników przepływu w sieciowej grupie zabezpieczeń.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$targetUri = "" # example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/queryFlowLogStatus?api-version=2016-12-01" $requestBody
```

Poniżej przedstawiono przykład zwróconej odpowiedzi:

```json
{
  "targetResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}",
  "properties": {
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{saName}",
    "enabled": true,
   "retentionPolicy": {
      "days": 5,
      "enabled": true
    },
    "format": {
    "type": "JSON",
    "version": 2
    }
  }
}
```

## <a name="download-a-flow-log"></a>Pobieranie dziennika przepływu

Lokalizacja magazynu dziennika przepływu jest definiowana podczas tworzenia. Wygodnym narzędziem do uzyskiwania dostępu do tych dzienników przepływu zapisanych na koncie magazynu jest Eksplorator usługi Microsoft Azure Storage, które można pobrać tutaj:  https://storageexplorer.com/

Jeśli określono konto magazynu, pliki przechwytywania pakietów są zapisywane na koncie magazynu w następującej lokalizacji:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [wizualizować dzienniki przepływu sieciowej organizacji zabezpieczeń przy użyciu usługi Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Dowiedz się, jak [wizualizować dzienniki przepływu sieciowej open source sieciowej](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
