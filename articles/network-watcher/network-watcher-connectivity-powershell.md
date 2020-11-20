---
title: Rozwiązywanie problemów z połączeniami — Azure PowerShell
titleSuffix: Azure Network Watcher
description: Dowiedz się, jak korzystać z funkcji rozwiązywania problemów z usługą Azure Network Watcher przy użyciu programu PowerShell.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: damendo
ms.openlocfilehash: be919d55c69245d9f924dfa55fe4f93ad4a35955
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965497"
---
# <a name="troubleshoot-connections-with-azure-network-watcher-using-powershell"></a>Rozwiązywanie problemów z usługą Azure Network Watcher przy użyciu programu PowerShell

> [!div class="op_single_selector"]
> - [Portal](network-watcher-connectivity-portal.md)
> - [Program PowerShell](network-watcher-connectivity-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-connectivity-cli.md)
> - [Interfejs API REST platformy Azure](network-watcher-connectivity-rest.md)

Dowiedz się, jak używać rozwiązywania problemów z połączeniami, aby sprawdzić, czy można nawiązać bezpośrednie połączenie TCP z maszyny wirtualnej do danego punktu końcowego.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Wystąpienie Network Watcher w regionie, w którym chcesz rozwiązać problem z połączeniem.
* Maszyny wirtualne do rozwiązywania problemów z usługą.

> [!IMPORTANT]
> Rozwiązywanie problemów z połączeniem wymaga, aby maszyna wirtualna z maszyną wirtualną była `AzureNetworkWatcherExtension` zainstalowana jako rozszerzenie maszyny wirtualnej. Aby zainstalować rozszerzenie na maszynie wirtualnej z systemem Windows, odwiedź [rozszerzenie maszyny wirtualnej usługi azure Network Watcher Agent dla systemu Windows](../virtual-machines/extensions/network-watcher-windows.md?toc=%252fazure%252fnetwork-watcher%252ftoc.json) i dla maszyny wirtualnej z systemem Linux odwiedź [rozszerzenie maszyny wirtualnej agenta usługi Azure Network Watcher](../virtual-machines/extensions/network-watcher-linux.md?toc=%252fazure%252fnetwork-watcher%252ftoc.json). Rozszerzenie nie jest wymagane w docelowym punkcie końcowym.

## <a name="check-connectivity-to-a-virtual-machine"></a>Sprawdź łączność z maszyną wirtualną

Ten przykład sprawdza połączenie z docelową maszyną wirtualną przez port 80. Ten przykład wymaga, aby Network Watcher włączony w regionie zawierającym źródłową maszynę wirtualną.  

### <a name="example"></a>Przykład

```powershell
$rgName = "ContosoRG"
$sourceVMName = "MultiTierApp0"
$destVMName = "Database0"

$RG = Get-AzResourceGroup -Name $rgName

$VM1 = Get-AzVM -ResourceGroupName $rgName | Where-Object -Property Name -EQ $sourceVMName
$VM2 = Get-AzVM -ResourceGroupName $rgName | Where-Object -Property Name -EQ $destVMName

$networkWatcher = Get-AzNetworkWatcher | Where-Object -Property Location -EQ -Value $VM1.Location 

Test-AzNetworkWatcherConnectivity -NetworkWatcher $networkWatcher -SourceId $VM1.Id -DestinationId $VM2.Id -DestinationPort 80
```

### <a name="response"></a>Reakcja

Poniższa odpowiedź pochodzi z poprzedniego przykładu.  W tej odpowiedzi `ConnectionStatus` jest **nieosiągalny**. Można zobaczyć, że wszystkie sondy zostały wysłane. Łączność z urządzeniem wirtualnym nie powiodła się ze względu na skonfigurowaną przez użytkownika `NetworkSecurityRule` nazwę **UserRule_Port80** skonfigurowaną do blokowania ruchu przychodzącego na porcie 80. Te informacje mogą służyć do badania problemów z połączeniami.

```
ConnectionStatus : Unreachable
AvgLatencyInMs   : 
MinLatencyInMs   : 
MaxLatencyInMs   : 
ProbesSent       : 100
ProbesFailed     : 100
Hops             : [
                     {
                       "Type": "Source",
                       "Id": "c5222ea0-3213-4f85-a642-cee63217c2f3",
                       "Address": "10.1.1.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGrou
                   ps/ContosoRG/providers/Microsoft.Network/networkInterfaces/appNic0/ipConfigurat
                   ions/ipconfig1",
                       "NextHopIds": [
                         "9283a9f0-cc5e-4239-8f5e-ae0f3c19fbaa"
                       ],
                       "Issues": []
                     },
                     {
                       "Type": "VirtualAppliance",
                       "Id": "9283a9f0-cc5e-4239-8f5e-ae0f3c19fbaa",
                       "Address": "10.1.2.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGrou
                   ps/ContosoRG/providers/Microsoft.Network/networkInterfaces/fwNic/ipConfiguratio
                   ns/ipconfig1",
                       "NextHopIds": [
                         "0f1500cd-c512-4d43-b431-7267e4e67017"
                       ],
                       "Issues": []
                     },
                     {
                       "Type": "VirtualAppliance",
                       "Id": "0f1500cd-c512-4d43-b431-7267e4e67017",
                       "Address": "10.1.3.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGrou
                   ps/ContosoRG/providers/Microsoft.Network/networkInterfaces/auNic/ipConfiguratio
                   ns/ipconfig1",
                       "NextHopIds": [
                         "a88940f8-5fbe-40da-8d99-1dee89240f64"
                       ],
                       "Issues": [
                         {
                           "Origin": "Outbound",
                           "Severity": "Error",
                           "Type": "NetworkSecurityRule",
                           "Context": [
                             {
                               "key": "RuleName",
                               "value": "UserRule_Port80"
                             }
                           ]
                         }
                       ]
                     },
                     {
                       "Type": "VnetLocal",
                       "Id": "a88940f8-5fbe-40da-8d99-1dee89240f64",
                       "Address": "10.1.4.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGrou
                   ps/ContosoRG/providers/Microsoft.Network/networkInterfaces/dbNic0/ipConfigurati
                   ons/ipconfig1",
                       "NextHopIds": [],
                       "Issues": []
                     }
                   ]
```

## <a name="validate-routing-issues"></a>Weryfikuj problemy z routingiem

Ten przykład sprawdza łączność między maszyną wirtualną a zdalnym punktem końcowym. Ten przykład wymaga, aby Network Watcher włączony w regionie zawierającym źródłową maszynę wirtualną.  

### <a name="example"></a>Przykład

```powershell
$rgName = "ContosoRG"
$sourceVMName = "MultiTierApp0"

$RG = Get-AzResourceGroup -Name $rgName
$VM1 = Get-AzVM -ResourceGroupName $rgName | Where-Object -Property Name -EQ $sourceVMName

$networkWatcher = Get-AzNetworkWatcher | Where-Object -Property Location -EQ -Value $VM1.Location 

Test-AzNetworkWatcherConnectivity -NetworkWatcher $networkWatcher -SourceId $VM1.Id -DestinationAddress 13.107.21.200 -DestinationPort 80
```

### <a name="response"></a>Reakcja

W poniższym przykładzie `ConnectionStatus` jest wyświetlany jako **nieosiągalny**. W `Hops` szczegółach można zobaczyć, `Issues` że ruch został zablokowany z powodu `UserDefinedRoute` . 

```
ConnectionStatus : Unreachable
AvgLatencyInMs   : 
MinLatencyInMs   : 
MaxLatencyInMs   : 
ProbesSent       : 100
ProbesFailed     : 100
Hops             : [
                     {
                       "Type": "Source",
                       "Id": "b4f7bceb-07a3-44ca-8bae-adec6628225f",
                       "Address": "10.1.1.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/appNic0/ipConfigurations/ipconfig1",
                       "NextHopIds": [
                         "3fee8adf-692f-4523-b742-f6fdf6da6584"
                       ],
                       "Issues": [
                         {
                           "Origin": "Outbound",
                           "Severity": "Error",
                           "Type": "UserDefinedRoute",
                           "Context": [
                             {
                               "key": "RouteType",
                               "value": "User"
                             }
                           ]
                         }
                       ]
                     },
                     {
                       "Type": "Destination",
                       "Id": "3fee8adf-692f-4523-b742-f6fdf6da6584",
                       "Address": "13.107.21.200",
                       "ResourceId": "Unknown",
                       "NextHopIds": [],
                       "Issues": []
                     }
                   ]
```

## <a name="check-website-latency"></a>Sprawdzanie opóźnienia witryny sieci Web

Poniższy przykład umożliwia sprawdzenie łączności z witryną sieci Web. Ten przykład wymaga, aby Network Watcher włączony w regionie zawierającym źródłową maszynę wirtualną.  

### <a name="example"></a>Przykład

```powershell
$rgName = "ContosoRG"
$sourceVMName = "MultiTierApp0"

$RG = Get-AzResourceGroup -Name $rgName
$VM1 = Get-AzVM -ResourceGroupName $rgName | Where-Object -Property Name -EQ $sourceVMName

$networkWatcher = Get-AzNetworkWatcher | Where-Object -Property Location -EQ -Value $VM1.Location 


Test-AzNetworkWatcherConnectivity -NetworkWatcher $networkWatcher -SourceId $VM1.Id -DestinationAddress https://bing.com/
```

### <a name="response"></a>Reakcja

W poniższej odpowiedzi widać, że są `ConnectionStatus` wyświetlane jako **dostępne**. Po pomyślnym nawiązaniu połączenia są podawane wartości opóźnienia.

```
ConnectionStatus : Reachable
AvgLatencyInMs   : 1
MinLatencyInMs   : 0
MaxLatencyInMs   : 7
ProbesSent       : 100
ProbesFailed     : 0
Hops             : [
                     {
                       "Type": "Source",
                       "Id": "1f0e3415-27b0-4bf7-a59d-3e19fb854e3e",
                       "Address": "10.1.1.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/appNic0/ipConfigurations/ipconfig1",
                       "NextHopIds": [
                         "f99f2bd1-42e8-4bbf-85b6-5d21d00c84e0"
                       ],
                       "Issues": []
                     },
                     {
                       "Type": "Internet",
                       "Id": "f99f2bd1-42e8-4bbf-85b6-5d21d00c84e0",
                       "Address": "204.79.197.200",
                       "ResourceId": "Internet",
                       "NextHopIds": [],
                       "Issues": []
                     }
                   ]
```

## <a name="check-connectivity-to-a-storage-endpoint"></a>Sprawdź łączność z punktem końcowym magazynu

Poniższy przykład umożliwia sprawdzenie łączności z maszyny wirtualnej z kontem magazynu w blogu. Ten przykład wymaga, aby Network Watcher włączony w regionie zawierającym źródłową maszynę wirtualną.  

### <a name="example"></a>Przykład

```powershell
$rgName = "ContosoRG"
$sourceVMName = "MultiTierApp0"

$RG = Get-AzResourceGroup -Name $rgName

$VM1 = Get-AzVM -ResourceGroupName $rgName | Where-Object -Property Name -EQ $sourceVMName

$networkWatcher = Get-AzNetworkWatcher | Where-Object -Property Location -EQ -Value $VM1.Location

Test-AzNetworkWatcherConnectivity -NetworkWatcher $networkWatcher -SourceId $VM1.Id -DestinationAddress https://contosostorageexample.blob.core.windows.net/ 
```

### <a name="response"></a>Reakcja

Poniższy kod JSON to Przykładowa odpowiedź z uruchomienia poprzedniego polecenia cmdlet. Gdy lokalizacja docelowa jest osiągalna, `ConnectionStatus` Właściwość jest wyświetlana jako **osiągalna**.  Podano szczegółowe informacje dotyczące liczby przeskoków wymaganych do uzyskania dostępu do obiektu blob magazynu i opóźnienia.

```json
ConnectionStatus : Reachable
AvgLatencyInMs   : 1
MinLatencyInMs   : 0
MaxLatencyInMs   : 8
ProbesSent       : 100
ProbesFailed     : 0
Hops             : [
                     {
                       "Type": "Source",
                       "Id": "9e7f61d9-fb45-41db-83e2-c815a919b8ed",
                       "Address": "10.1.1.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/appNic0/ipConfigurations/ipconfig1",
                       "NextHopIds": [
                         "1e6d4b3c-7964-4afd-b959-aaa746ee0f15"
                       ],
                       "Issues": []
                     },
                     {
                       "Type": "Internet",
                       "Id": "1e6d4b3c-7964-4afd-b959-aaa746ee0f15",
                       "Address": "13.71.200.248",
                       "ResourceId": "Internet",
                       "NextHopIds": [],
                       "Issues": []
                     }
                   ]
```

## <a name="next-steps"></a>Następne kroki

Należy określić, czy ruch z maszyny wirtualnej jest dozwolony z lub z niej, odwiedzając [sprawdzenie przepływu IP](diagnose-vm-network-traffic-filtering-problem.md).

Jeśli ruch jest blokowany i nie powinien być, zobacz [Zarządzanie sieciowymi](../virtual-network/manage-network-security-group.md) grupami zabezpieczeń, aby śledzić zdefiniowane grupy zabezpieczeń sieci i reguły zabezpieczeń.