---
title: Zarządzanie dziennikami przepływu sieciowej grupy zabezpieczeń — interfejs API REST platformy Azure
titleSuffix: Azure Network Watcher
description: Na tej stronie wyjaśniono, jak zarządzać dziennikami przepływu sieciowych grup zabezpieczeń w usłudze Azure Network Watcher przy użyciu interfejsu API REST
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 936501674fcf1d428de936174575440edad71de2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84738519"
---
# <a name="configuring-network-security-group-flow-logs-using-rest-api"></a>Konfigurowanie dzienników przepływu sieciowych grup zabezpieczeń przy użyciu interfejsu API REST

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [Program PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-nsg-flow-logging-cli.md)
> - [Interfejs API REST](network-watcher-nsg-flow-logging-rest.md)

Dzienniki przepływu sieciowych grup zabezpieczeń są funkcją Network Watcher, która umożliwia wyświetlanie informacji dotyczących ruchu przychodzącego i wychodzącego IP za pomocą sieciowej grupy zabezpieczeń. Te dzienniki przepływów są zapisywane w formacie JSON i pokazują przepływy wychodzące i przychodzące dla każdej reguły, karta sieciowa przepływu ma zastosowanie do, 5-informacje o spójnej kolekcji przepływu (źródłowy/docelowy adres IP, port źródłowy/docelowy, protokół) i jeśli ruch był dozwolony lub zabroniony.

## <a name="before-you-begin"></a>Przed rozpoczęciem

ARMclient jest używany do wywoływania interfejsu API REST przy użyciu programu PowerShell. ARMClient można znaleźć na czekolady w [ARMClient na czekoladie](https://chocolatey.org/packages/ARMClient)

W tym scenariuszu założono, że wykonano już kroki opisane w temacie [tworzenie Network Watcher](network-watcher-create.md) w celu utworzenia Network Watcher.

> [!Important]
> W przypadku wywołania interfejsu API REST Network Watcher nazwa grupy zasobów w identyfikatorze URI żądania jest grupą zasobów, która zawiera Network Watcher, a nie zasobów, na których wykonywane są akcje diagnostyczne.

## <a name="scenario"></a>Scenariusz

W scenariuszu opisanym w tym artykule przedstawiono sposób włączania, wyłączania i wysyłania dzienników przepływów przy użyciu interfejsu API REST. Aby dowiedzieć się więcej na temat rejestrowania przepływu sieciowych grup zabezpieczeń, odwiedź stronę [Rejestrowanie przepływu sieciowych grup zabezpieczeń — omówienie](network-watcher-nsg-flow-logging-overview.md).

W tym scenariuszu będziesz:

* Włącz dzienniki przepływów (wersja 2)
* Wyłącz dzienniki przepływów
* Stan dzienników przepływu zapytań

## <a name="log-in-with-armclient"></a>Logowanie za pomocą ARMClient

Zaloguj się do armclient przy użyciu poświadczeń platformy Azure.

```powershell
armclient login
```

## <a name="register-insights-provider"></a>Rejestrowanie dostawcy usługi Insights

Aby rejestrowanie przepływu działało prawidłowo, dostawca **Microsoft. Insights** musi być zarejestrowany. Jeśli nie masz pewności, czy dostawca **Microsoft. Insights** został zarejestrowany, uruchom następujący skrypt.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
armclient post "https://management.azure.com//subscriptions/${subscriptionId}/providers/Microsoft.Insights/register?api-version=2016-09-01"
```

## <a name="enable-network-security-group-flow-logs"></a>Włączanie dzienników przepływu sieciowych grup zabezpieczeń

W poniższym przykładzie przedstawiono polecenie umożliwiające włączenie dzienników przepływów w wersji 2. W przypadku wersji 1 Zastąp pole "Version" "1":

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

## <a name="disable-network-security-group-flow-logs"></a>Wyłącz dzienniki przepływu sieciowych grup zabezpieczeń

Użyj poniższego przykładu, aby wyłączyć dzienniki przepływów. Wywołanie jest takie samo jak Włączanie dzienników przepływów, z wyjątkiem, że dla właściwości Enabled ustawiono **wartość false** .

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

## <a name="query-flow-logs"></a>Dzienniki przepływu zapytań

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

Poniżej przedstawiono przykład zwracanej odpowiedzi:

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

## <a name="download-a-flow-log"></a>Pobierz dziennik przepływu

Lokalizacja magazynu dziennika przepływu jest definiowana podczas tworzenia. Wygodnym narzędziem do uzyskiwania dostępu do tych dzienników przepływów zapisanych na koncie magazynu jest Eksplorator usługi Microsoft Azure Storage, które można pobrać tutaj:  https://storageexplorer.com/

Jeśli określono konto magazynu, pliki przechwytywania pakietów są zapisywane na koncie magazynu w następującej lokalizacji:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [wizualizować dzienniki przepływu sieciowej grupy zabezpieczeń za pomocą usługi PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Dowiedz się, jak [wizualizować dzienniki przepływu sieciowej grupy zabezpieczeń za pomocą narzędzi open source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
