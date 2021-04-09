---
title: Zarządzanie przechwytywaniem pakietów przy użyciu usługi Azure Network Watcher — interfejs API REST | Microsoft Docs
description: Na tej stronie wyjaśniono, jak zarządzać funkcją przechwytywania pakietów Network Watcher przy użyciu interfejsu API REST platformy Azure
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
ms.openlocfilehash: bcdb91d7516acb8f987a3f302c04fbd2d8594fc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019775"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-azure-rest-api"></a>Zarządzanie przechwytywaniem pakietów za pomocą usługi Azure Network Watcher przy użyciu interfejsu API REST platformy Azure

> [!div class="op_single_selector"]
> - [Witryna Azure Portal](network-watcher-packet-capture-manage-portal.md)
> - [Program PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-packet-capture-manage-cli.md)
> - [Interfejs API REST platformy Azure](network-watcher-packet-capture-manage-rest.md)

Przechwytywanie pakietów Network Watcher umożliwia tworzenie sesji przechwytywania w celu śledzenia ruchu do i z maszyny wirtualnej. Filtry są udostępniane dla sesji przechwytywania, aby upewnić się, że przechwytywany jest tylko żądany ruch. Przechwytywanie pakietów ułatwia diagnozowanie anomalii w sieci zarówno ponownie, jak i aktywnie. Inne zastosowania obejmują gromadzenie statystyk sieci, uzyskiwanie informacji o atakach sieci, debugowanie komunikacji klient-serwer i wiele więcej. Dzięki możliwości zdalnego wyzwalania przechwycenia pakietów ta funkcja ułatwia nawiązanie ręcznego uruchamiania przechwytywania pakietów i na odpowiedniej maszynie, co pozwala zaoszczędzić cenny czas.

Ten artykuł przeprowadzi Cię przez różne zadania zarządzania, które są obecnie dostępne do przechwycenia pakietu.

- [**Pobierz przechwycenie pakietu**](#get-a-packet-capture)
- [**Wyświetl listę wszystkich przechwyconych pakietów**](#list-all-packet-captures)
- [**Zbadaj stan przechwycenia pakietu**](#query-packet-capture-status)
- [**Rozpocznij przechwytywanie pakietu**](#start-packet-capture)
- [**Zatrzymywanie przechwytywania pakietu**](#stop-packet-capture)
- [**Usuwanie przechwytywania pakietu**](#delete-packet-capture)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym scenariuszu należy wywołać interfejs API REST Network Watcher, aby uruchomić weryfikację przepływu IP. ARMclient jest używany do wywoływania interfejsu API REST przy użyciu programu PowerShell. ARMClient można znaleźć na czekolady w [ARMClient na czekoladie](https://chocolatey.org/packages/ARMClient)

W tym scenariuszu założono, że wykonano już kroki opisane w temacie [tworzenie Network Watcher](network-watcher-create.md) w celu utworzenia Network Watcher.

> Przechwytywanie pakietów wymaga rozszerzenia maszyny wirtualnej `AzureNetworkWatcherExtension` . Aby zainstalować rozszerzenie na maszynie wirtualnej z systemem Windows, odwiedź [rozszerzenie maszyny wirtualnej usługi azure Network Watcher Agent dla systemu Windows](../virtual-machines/extensions/network-watcher-windows.md) i dla maszyny wirtualnej z systemem Linux odwiedź [rozszerzenie maszyny wirtualnej agenta usługi Azure Network Watcher](../virtual-machines/extensions/network-watcher-linux.md).

## <a name="log-in-with-armclient"></a>Logowanie za pomocą ARMClient

```powershell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Pobierz maszynę wirtualną

Uruchom następujący skrypt, aby zwrócić maszynę wirtualną. Te informacje są zbędne do rozpoczęcia przechwytywania pakietów.

Następujący kod wymaga zmiennych:

- **subskrypcji** — Identyfikator subskrypcji można także pobrać przy użyciu polecenia cmdlet **Get-AzSubscription** .
- **resourceGroupName** — nazwa grupy zasobów zawierającej maszyny wirtualne.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

Z następujących danych wyjściowych Identyfikator maszyny wirtualnej jest używany w następnym przykładzie.

```json
...
,
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute
/virtualMachines/ContosoVM",
      "name": "ContosoVM"
    }
  ]
}
```


## <a name="get-a-packet-capture"></a>Pobierz przechwycenie pakietu

Poniższy przykład pobiera stan pojedynczego przechwycenia pakietu

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}/querystatus?api-version=2016-12-01"
```

Poniżej przedstawiono przykłady typowej odpowiedzi zwracanej podczas wykonywania zapytania dotyczącego stanu przechwytywania pakietów.

```json
{
  "name": "TestPacketCapture5",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/packetCaptures/TestPacketCapture6",
  "captureStartTime": "2016-12-06T17:20:01.5671279Z",
  "packetCaptureStatus": "Running",
  "packetCaptureError": []
}
```

```json
{
  "name": "TestPacketCapture5",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/packetCaptures/TestPacketCapture6",
  "captureStartTime": "2016-12-06T17:20:01.5671279Z",
  "packetCaptureStatus": "Stopped",
  "stopReason": "TimeExceeded",
  "packetCaptureError": []
}
```

## <a name="list-all-packet-captures"></a>Wyświetl listę wszystkich przechwyconych pakietów

Poniższy przykład pobiera wszystkie sesje przechwytywania pakietów w regionie.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
armclient get "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures?api-version=2016-12-01"
```

Poniżej przedstawiono przykład typowej odpowiedzi zwracanej podczas pobierania wszystkich przechwycenia pakietu

```json
{
  "value": [
    {
      "name": "TestPacketCapture6",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/packetCaptures/TestPacketCapture6",
      "etag": "W/\"091762e1-c23f-448b-89d5-37cf56e4c045\"",
      "properties": {
        "provisioningState": "Succeeded",
        "target": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute/virtualMachines/ContosoVM",
        "bytesToCapturePerPacket": 0,
        "totalBytesPerSession": 1073741824,
        "timeLimitInSeconds": 60,
        "storageLocation": {
          "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Storage/storageAccounts/contosoexamplergdiag374",
          "storagePath": "https://contosoexamplergdiag374.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/contosoexamplerg/providers/microsoft.compute/virtualmachines/contosovm/2016/12/06/packetcap
ture_17_19_53_056.cap",
          "filePath": "c:\\temp\\packetcapture.cap"
        },
        "filters": [
          {
            "protocol": "Any",
            "localIPAddress": "",
            "localPort": "",
            "remoteIPAddress": "",
            "remotePort": ""
          }
        ]
      }
    },
    {
      "name": "TestPacketCapture7",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/packetCaptures/TestPacketCapture7",
      "etag": "W/\"091762e1-c23f-448b-89d5-37cf56e4c045\"",
      "properties": {
        "provisioningState": "Failed",
        "target": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute/virtualMachines/ContosoVM",
        "bytesToCapturePerPacket": 0,
        "totalBytesPerSession": 1073741824,
        "timeLimitInSeconds": 60,
        "storageLocation": {
          "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Storage/storageAccounts/contosoexamplergdiag374",
          "storagePath": "https://contosoexamplergdiag374.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/contosoexamplerg/providers/microsoft.compute/virtualmachines/contosovm/2016/12/06/packetcap
ture_17_23_15_364.cap",
          "filePath": "c:\\temp\\packetcapture.cap"
        },
        "filters": [
          {
            "protocol": "Any",
            "localIPAddress": "",
            "localPort": "",
            "remoteIPAddress": "",
            "remotePort": ""
          }
        ]
      }
    }
  ]
}
```

## <a name="query-packet-capture-status"></a>Badanie stanu przechwytywania pakietów

Poniższy przykład pobiera wszystkie sesje przechwytywania pakietów w regionie.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$packetCaptureName = "TestPacketCapture5"
armclient get "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}/querystatus?api-version=2016-12-01"
```

Poniżej przedstawiono przykład typowej odpowiedzi zwracanej podczas wykonywania zapytania o stan przechwytywania pakietów.

```json
{
    "name": "vm1PacketCapture",
    "id": "/subscriptions/{guid}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkWatchers/{networkWatcherName}/packetCaptures/{packetCaptureName}",
    "captureStartTime" : "9/7/2016 12:35:24PM",
    "packetCaptureStatus" : "Stopped",
    "stopReason" : "TimeExceeded",
    "packetCaptureError" : [ ]
}
```

## <a name="start-packet-capture"></a>Rozpocznij przechwytywanie pakietów

Poniższy przykład tworzy przechwytywanie pakietów na maszynie wirtualnej.  Przykład jest sparametryzowane, aby umożliwić elastyczność w tworzeniu przykładu.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$packetCaptureName = "TestPacketCapture5"
$storageaccountname = "contosoexamplergdiag374"
$vmName = "ContosoVM"
$bytestoCaptureperPacket = "0"
$bytesPerSession = "1073741824"
$captureTimeinSeconds = "60"
$localIP = ""
$localPort = "" # Examples are: 80, or 80-120
$remoteIP = ""
$remotePort = "" # Examples are: 80, or 80-120
$protocol = "" # Valid values are TCP, UDP and Any.
$targetUri = "" # Example: /subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.compute/virtualMachine/$vmName
$storageId = "" #Example "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Storage/storageAccounts/contosoexamplergdiag374"
$storagePath = "" # Example: "https://mytestaccountname.blob.core.windows.net/capture/vm1Capture.cap"
$localFilePath = "c:\\temp\\packetcapture.cap" # Example: "d:\capture\vm1Capture.cap"

$requestBody = @"
{
    'properties':  {
                       'target':  '/${targetUri}',
                       'bytesToCapturePerPacket':  '${bytestoCaptureperPacket}',
                       'totalBytesPerSession':  '${bytesPerSession}',
                       'timeLimitinSeconds':  '${captureTimeinSeconds}',
                       'storageLocation':  {
                                               'storageId':  '${storageId}',
                                               'storagePath':  '${storagePath}',
                                               'filePath':  '${localFilePath}'
                                           },
                       'filters':  [
                                       {
                                           'protocol':  '${protocol}',
                                           'localIPAddress':  '${localIP}',
                                           'localPort':  '${localPort}',
                                           'remoteIPAddress':  '${remoteIP}',
                                           'remotePort':  '${remotePort}'
                                       }
                                   ]
                   }
}
"@

armclient PUT "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}?api-version=2016-07-01" $requestbody
```

## <a name="stop-packet-capture"></a>Zatrzymywanie przechwytywania pakietu

Poniższy przykład powoduje zatrzymanie przechwytywania pakietów na maszynie wirtualnej.  Przykład jest sparametryzowane, aby umożliwić elastyczność w tworzeniu przykładu.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$packetCaptureName = "TestPacketCapture5"
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}/stop?api-version=2016-12-01"
```

## <a name="delete-packet-capture"></a>Usuń przechwycenie pakietu

Poniższy przykład usuwa przechwytywanie pakietów na maszynie wirtualnej.  Przykład jest sparametryzowane, aby umożliwić elastyczność w tworzeniu przykładu.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$packetCaptureName = "TestPacketCapture5"

armclient delete "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}?api-version=2016-12-01"
```

> [!NOTE]
> Usuwanie przechwycenia pakietu nie powoduje usunięcia pliku na koncie magazynu

## <a name="next-steps"></a>Następne kroki

Aby uzyskać instrukcje dotyczące pobierania plików z kont usługi Azure Storage, zobacz Rozpoczynanie [pracy z usługą Azure Blob Storage przy użyciu platformy .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md). Inne narzędzie, które może być używane, jest Eksplorator usługi Storage. Więcej informacji na temat Eksplorator usługi Storage można znaleźć tutaj przy użyciu następującego linku: [Eksplorator usługi Storage](https://storageexplorer.com/)

Informacje o automatyzowaniu przechwytywania pakietów przy użyciu alertów dotyczących maszyn wirtualnych poprzez wyświetlanie [funkcji tworzenia alertu wyzwolenie pakietu](network-watcher-alert-triggered-packet-capture.md)