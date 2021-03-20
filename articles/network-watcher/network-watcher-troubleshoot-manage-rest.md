---
title: Rozwiązywanie problemów z bramą sieci wirtualnej i połączeniami — interfejs API REST Azure
titleSuffix: Azure Network Watcher
description: Na tej stronie wyjaśniono, jak rozwiązywać problemy Virtual Network bram i połączeń z usługą Azure Network Watcher przy użyciu usługi REST
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 72f279d90d071b0e5fe7f552a05474bc3b37a82e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98011037"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher"></a>Rozwiązywanie problemów z bramą Virtual Network i połączeniami przy użyciu usługi Azure Network Watcher

> [!div class="op_single_selector"]
> - [Portal](diagnose-communication-problem-between-networks.md)
> - [Program PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-troubleshoot-manage-cli.md)
> - [Interfejs API REST](network-watcher-troubleshoot-manage-rest.md)

Network Watcher udostępnia wiele funkcji, które odnoszą się do poznania zasobów sieciowych na platformie Azure. Jedną z tych możliwości jest rozwiązywanie problemów z zasobami. Rozwiązywanie problemów z zasobami można wywołać za pomocą portalu, programu PowerShell, interfejsu wiersza polecenia lub API REST. Gdy jest wywoływana, Network Watcher sprawdza kondycję Virtual Network bramy lub połączenia i zwraca swoje wyniki.

W tym artykule przedstawiono różne zadania zarządzania, które są obecnie dostępne do rozwiązywania problemów z zasobami.

- [**Rozwiązywanie problemów z bramą Virtual Network**](#troubleshoot-a-virtual-network-gateway)
- [**Rozwiązywanie problemów z połączeniem**](#troubleshoot-connections)

## <a name="before-you-begin"></a>Zanim rozpoczniesz

ARMclient jest używany do wywoływania interfejsu API REST przy użyciu programu PowerShell. ARMClient można znaleźć na czekolady w [ARMClient na czekoladie](https://chocolatey.org/packages/ARMClient)

W tym scenariuszu założono, że wykonano już kroki opisane w temacie [tworzenie Network Watcher](network-watcher-create.md) w celu utworzenia Network Watcher.

Aby zapoznać się z listą obsługiwanych typów bram, należy odwiedzić [obsługiwane typy bram](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Omówienie

Network Watcher Rozwiązywanie problemów zapewnia możliwość rozwiązywania problemów związanych z bramami Virtual Network i połączeniami. Gdy do rozwiązywania problemów zostanie wysłane żądanie, dzienniki są zapytania i sprawdzane. Po zakończeniu inspekcji są zwracane wyniki. Żądania interfejsu API rozwiązywania problemów są długotrwałymi żądaniami, co może potrwać kilka minut. Dzienniki są przechowywane w kontenerze na koncie magazynu.

## <a name="log-in-with-armclient"></a>Logowanie za pomocą ARMClient

```powershell
armclient login
```

## <a name="troubleshoot-a-virtual-network-gateway"></a>Rozwiązywanie problemów z bramą Virtual Network


### <a name="post-the-troubleshoot-request"></a>Opublikuj żądanie rozwiązywania problemów

Poniższy przykład wysyła zapytanie do stanu bramy Virtual Network.

```powershell

$subscriptionId = "00000000-0000-0000-0000-000000000000"
$resourceGroupName = "ContosoRG"
$NWresourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$vnetGatewayName = "ContosoVNETGateway"
$storageAccountName = "contososa"
$containerName = "gwlogs"
$requestBody = @"
{
'TargetResourceId': '/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/virtualNetworkGateways/${vnetGatewayName}',
'Properties': {
'StorageId': '/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Storage/storageAccounts/${storageAccountName}',
'StoragePath': 'https://${storageAccountName}.blob.core.windows.net/${containerName}'
}
}
"@


armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${NWresourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/troubleshoot?api-version=2016-03-30" $requestBody -verbose
```

Ponieważ ta operacja jest długotrwała, identyfikator URI zapytania o operację i identyfikator URI dla wyniku jest zwracany w nagłówku odpowiedzi, jak pokazano w następującej odpowiedzi:

**Ważne wartości**

* **Azure-AsyncOperation** — ta właściwość zawiera identyfikator URI służący do wykonywania zapytań dotyczących asynchronicznej operacji rozwiązywania problemów
* **Location** — ta właściwość zawiera identyfikator URI, do którego wyniki są po zakończeniu operacji

```
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 10
x-ms-request-id: 8a1167b7-6768-4ac1-85dc-703c9c9b9247
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Strict-Transport-Security: max-age=31536000; includeSubDomains
Cache-Control: no-cache
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Server: Microsoft-HTTPAPI/2.0; Microsoft-HTTPAPI/2.0
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 4364d88a-bd08-422c-a716-dbb0cdc99f7b
x-ms-routing-request-id: NORTHCENTRALUS:20170112T183202Z:4364d88a-bd08-422c-a716-dbb0cdc99f7b
Date: Thu, 12 Jan 2017 18:32:01 GMT

null
```

### <a name="query-the-async-operation-for-completion"></a>Wykonywanie zapytania dotyczącego asynchronicznej operacji ukończenia

Użyj identyfikatora URI operacji, aby wykonać zapytanie o postęp operacji, jak pokazano w następującym przykładzie:

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30" -verbose
```

Gdy operacja jest w toku, **odpowiedź pokazuje, jak widać w** poniższym przykładzie:

```json
{
  "status": "InProgress"
}
```

Po zakończeniu operacji stan zmieni się na **powodzenie**.

```json
{
  "status": "Succeeded"
}
```

### <a name="retrieve-the-results"></a>Pobieranie wyników

Po **pomyślnym** uzyskaniu stanu Wywołaj metodę get na identyfikatorze URI klasy OperationResult, aby pobrać wyniki.

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30" -verbose
```

Poniżej przedstawiono przykłady typowej reakcji o obniżonej wydajności zwracany podczas wykonywania zapytania o wyniki rozwiązywania problemów z bramą. Zobacz [Opis wyników](#understanding-the-results) , aby uzyskać wyjaśnienie, co oznacza właściwości w odpowiedzi.

```json
{
  "startTime": "2017-01-12T10:31:41.562646-08:00",
  "endTime": "2017-01-12T18:31:48.677Z",
  "code": "Degraded",
  "results": [
    {
      "id": "PlatformInActive",
      "summary": "We are sorry, your VPN gateway is in standby mode",
      "detail": "During this time the gateway will not initiate or accept VPN connections with on premises VPN devices or other Azure VPN Gateways. This is a transient state while the Azure platform is being updated.",
      "recommendedActions": [
        {
          "actionText": "If the condition persists, please try resetting your Azure VPN gateway",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting the VPN Gateway"
        },
        {
          "actionText": "If your VPN gateway isn't up and running by the expected resolution time, contact support",
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    },
    {
      "id": "NoFault",
      "summary": "This VPN gateway is running normally",
      "detail": "There aren't any known Azure platform problems affecting this VPN Connection",
      "recommendedActions": [
        {
          "actionText": "If you are still experience problems with the VPN gateway, please try resetting the VPN gateway.",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting VPN gateway"
        },
        {
          "actionText": "If you are experiencing problems you believe are caused by Azure, contact support",
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    }
  ]
}
```


## <a name="troubleshoot-connections"></a>Rozwiązywanie problemów z połączeniami

Poniższy przykład wykonuje zapytanie o stan połączenia.

```powershell

$subscriptionId = "00000000-0000-0000-0000-000000000000"
$resourceGroupName = "ContosoRG"
$NWresourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$connectionName = "VNET2toVNET1Connection"
$storageAccountName = "contososa"
$containerName = "gwlogs"
$requestBody = @{
"TargetResourceId": "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/connections/${connectionName}",
"Properties": {
"StorageId": "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Storage/storageAccounts/${storageAccountName}",
"StoragePath": "https://${storageAccountName}.blob.core.windows.net/${containerName}"
}

}
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${NWresourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/troubleshoot?api-version=2016-03-30 $requestBody"
```

> [!NOTE]
> Operacji rozwiązywania problemów nie można uruchomić równolegle dla połączenia i odpowiednich bram. Operacja musi zostać zakończona przed uruchomieniem jej w poprzednim zasobie.

Ponieważ jest to długotrwała transakcja, w nagłówku odpowiedzi zostaje zwrócony identyfikator URI służący do wykonywania zapytań dotyczących operacji oraz identyfikator URI dla wyniku, jak pokazano w następującej odpowiedzi:

**Ważne wartości**

* **Azure-AsyncOperation** — ta właściwość zawiera identyfikator URI służący do wykonywania zapytań dotyczących asynchronicznej operacji rozwiązywania problemów
* **Location** — ta właściwość zawiera identyfikator URI, do którego wyniki są po zakończeniu operacji

```
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 10
x-ms-request-id: 8a1167b7-6768-4ac1-85dc-703c9c9b9247
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Strict-Transport-Security: max-age=31536000; includeSubDomains
Cache-Control: no-cache
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Server: Microsoft-HTTPAPI/2.0; Microsoft-HTTPAPI/2.0
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 4364d88a-bd08-422c-a716-dbb0cdc99f7b
x-ms-routing-request-id: NORTHCENTRALUS:20170112T183202Z:4364d88a-bd08-422c-a716-dbb0cdc99f7b
Date: Thu, 12 Jan 2017 18:32:01 GMT

null
```

### <a name="query-the-async-operation-for-completion"></a>Wykonywanie zapytania dotyczącego asynchronicznej operacji ukończenia

Użyj identyfikatora URI operacji, aby wykonać zapytanie o postęp operacji, jak pokazano w następującym przykładzie:

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/843b1c31-4717-4fdd-b7a6-4c786ca9c501?api-version=2016-03-30"
```

Gdy operacja jest w toku, **odpowiedź pokazuje, jak widać w** poniższym przykładzie:

```json
{
  "status": "InProgress"
}
```

Po zakończeniu operacji stan zmieni się na **powodzenie**.

```json
{
  "status": "Succeeded"
}
```

Poniżej przedstawiono przykłady typowej odpowiedzi zwracanej podczas wykonywania zapytania o wyniki rozwiązywania problemów z połączeniem.

### <a name="retrieve-the-results"></a>Pobieranie wyników

Po **pomyślnym** uzyskaniu stanu Wywołaj metodę get na identyfikatorze URI klasy OperationResult, aby pobrać wyniki.

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/843b1c31-4717-4fdd-b7a6-4c786ca9c501?api-version=2016-03-30"
```

Poniżej przedstawiono przykłady typowej odpowiedzi zwracanej podczas wykonywania zapytania o wyniki rozwiązywania problemów z połączeniem.

```json
{
  "startTime": "2017-01-12T14:09:19.1215346-08:00",
  "endTime": "2017-01-12T22:09:23.747Z",
  "code": "UnHealthy",
  "results": [
    {
      "id": "PlatformInActive",
      "summary": "We are sorry, your VPN gateway is in standby mode",
      "detail": "During this time the gateway will not initiate or accept VPN connections with on premises VPN devices or other Azure VPN Gateways. This 
is a transient state while the Azure platform is being updated.",
      "recommendedActions": [
        {
          "actionText": "If the condition persists, please try resetting your Azure VPN gateway",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting the VPN gateway"
        },
        {
          "actionText": "If your VPN Connection isn't up and running by the expected resolution time, contact support",
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    },
    {
      "id": "NoFault",
      "summary": "This VPN Connection is running normally",
      "detail": "There aren't any known Azure platform problems affecting this VPN Connection",
      "recommendedActions": [
        {
          "actionText": "If you are still experience problems with the VPN gateway, please try resetting the VPN gateway.",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting VPN gateway"
        },
        {
          "actionText": "If you are experiencing problems you believe are caused by Azure, contact support",
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    }
  ]
}
```

## <a name="understanding-the-results"></a>Zrozumienie wyników

Tekst akcji zawiera ogólne wskazówki dotyczące sposobu rozwiązania problemu. Jeśli można wykonać akcję dotyczącą problemu, zostanie podane łącze z dodatkowymi wskazówkami. W przypadku braku dodatkowych wskazówek odpowiedź zawiera adres URL służący do otwierania zgłoszenia do pomocy technicznej.  Aby uzyskać więcej informacji o właściwościach odpowiedzi i uwzględnionych na niej tematach, odwiedź stronę [Network Watcher Rozwiązywanie problemów](network-watcher-troubleshoot-overview.md)

Aby uzyskać instrukcje dotyczące pobierania plików z kont usługi Azure Storage, zobacz Rozpoczynanie [pracy z usługą Azure Blob Storage przy użyciu platformy .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md). Inne narzędzie, które może być używane, jest Eksplorator usługi Storage. Więcej informacji na temat Eksplorator usługi Storage można znaleźć tutaj przy użyciu następującego linku: [Eksplorator usługi Storage](https://storageexplorer.com/)

## <a name="next-steps"></a>Następne kroki

Jeśli zmieniono ustawienia, które zatrzymują łączność z siecią VPN, zobacz [Manage Network Security Groups](../virtual-network/manage-network-security-group.md) to Track The Network Security Groups and Security rules.