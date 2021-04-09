---
title: Zarządzanie przechwytywaniem pakietów przy użyciu usługi Azure Network Watcher — interfejs wiersza polecenia platformy Azure | Microsoft Docs
description: Na tej stronie wyjaśniono, jak zarządzać funkcją przechwytywania pakietów Network Watcher przy użyciu interfejsu wiersza polecenia platformy Azure
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: cb0c1d10-f7f2-4c34-b08c-f73452430be8
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: efb8fba655570a661b6b001eb93942475eb2170d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101698641"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-azure-cli"></a>Zarządzanie przechwytywaniem pakietów za pomocą usługi Azure Network Watcher przy użyciu interfejsu wiersza polecenia platformy Azure

> [!div class="op_single_selector"]
> - [Witryna Azure Portal](network-watcher-packet-capture-manage-portal.md)
> - [Program PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-packet-capture-manage-cli.md)
> - [Interfejs API REST platformy Azure](network-watcher-packet-capture-manage-rest.md)

Przechwytywanie pakietów Network Watcher umożliwia tworzenie sesji przechwytywania w celu śledzenia ruchu do i z maszyny wirtualnej. Filtry są udostępniane dla sesji przechwytywania, aby upewnić się, że przechwytywany jest tylko żądany ruch. Przechwytywanie pakietów ułatwia diagnozowanie anomalii w sieci zarówno ponownie, jak i aktywnie. Inne zastosowania obejmują gromadzenie statystyk sieci, uzyskiwanie informacji o atakach sieci, debugowanie komunikacji klient-serwer i wiele więcej. Dzięki możliwości zdalnego wyzwalania przechwycenia pakietów ta funkcja ułatwia nawiązanie ręcznego uruchamiania przechwytywania pakietów i na odpowiedniej maszynie, co pozwala zaoszczędzić cenny czas.

Aby wykonać kroki opisane w tym artykule, należy [zainstalować interfejs Command-Line platformy Azure dla systemów Mac, Linux i Windows (interfejsu wiersza polecenia platformy Azure)](/cli/azure/install-azure-cli).

Ten artykuł przeprowadzi Cię przez różne zadania zarządzania, które są obecnie dostępne do przechwycenia pakietu.

- [**Rozpocznij przechwytywanie pakietu**](#start-a-packet-capture)
- [**Zatrzymywanie przechwytywania pakietu**](#stop-a-packet-capture)
- [**Usuwanie przechwytywania pakietu**](#delete-a-packet-capture)
- [**Pobieranie przechwytywania pakietów**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym artykule założono, że masz następujące zasoby:

- Wystąpienie Network Watcher w regionie, w którym chcesz utworzyć przechwycenie pakietu
- Maszyna wirtualna z włączonym rozszerzeniem przechwytywania pakietów.

> [!IMPORTANT]
> Przechwytywanie pakietów wymaga, aby Agent był uruchomiony na maszynie wirtualnej. Agent jest instalowany jako rozszerzenie. Aby uzyskać instrukcje dotyczące rozszerzeń maszyn wirtualnych, odwiedź stronę [rozszerzenia i funkcje maszyny wirtualnej](../virtual-machines/extensions/features-windows.md).

## <a name="install-vm-extension"></a>Instalowanie rozszerzenia maszyny wirtualnej

### <a name="step-1"></a>Krok 1

Uruchom `az vm extension set` polecenie, aby zainstalować agenta przechwytywania pakietów na maszynie wirtualnej gościa.

W przypadku maszyn wirtualnych z systemem Windows:

```azurecli-interactive
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentWindows --version 1.4
```

Dla maszyn wirtualnych z systemem Linux:

```azurecli-interactive
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentLinux --version 1.4
```

### <a name="step-2"></a>Krok 2

Aby upewnić się, że Agent jest zainstalowany, uruchom `vm extension show` polecenie i przekaż go do grupy zasobów i nazwy maszyny wirtualnej. Sprawdź listę wyników, aby upewnić się, że Agent jest zainstalowany.

W przypadku maszyn wirtualnych z systemem Windows:

```azurecli-interactive
az vm extension show --resource-group resourceGroupName --vm-name virtualMachineName --name NetworkWatcherAgentWindows
```

Dla maszyn wirtualnych z systemem Linux:

```azurecli-interactive
az vm extension show --resource-group resourceGroupName --vm-name virtualMachineName --name AzureNetworkWatcherExtension
```

Poniższy przykład jest przykładem odpowiedzi z uruchamiania `az vm extension show`

```json
{
  "autoUpgradeMinorVersion": true,
  "forceUpdateTag": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/extensions/NetworkWatcherAgentWindows",
  "instanceView": null,
  "location": "westcentralus",
  "name": "NetworkWatcherAgentWindows",
  "protectedSettings": null,
  "provisioningState": "Succeeded",
  "publisher": "Microsoft.Azure.NetworkWatcher",
  "resourceGroup": "{resourceGroupName}",
  "settings": null,
  "tags": null,
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "typeHandlerVersion": "1.4",
  "virtualMachineExtensionType": "NetworkWatcherAgentWindows"
}
```

## <a name="start-a-packet-capture"></a>Rozpocznij przechwytywanie pakietu

Po wykonaniu powyższych kroków Agent przechwytywania pakietów zostanie zainstalowany na maszynie wirtualnej.

### <a name="step-1"></a>Krok 1

Pobierz konto magazynu. To konto magazynu jest używane do przechowywania pliku przechwytywania pakietów.

```azurecli-interactive
az storage account list
```

### <a name="step-2"></a>Krok 2

W tym momencie można przystąpić do tworzenia przechwytywania pakietów.  Najpierw zapoznaj się z parametrami, które można skonfigurować. Filtry są jednym z tych parametrów, których można użyć do ograniczenia danych przechowywanych przez funkcję przechwytywania pakietów. Poniższy przykład konfiguruje przechwytywanie pakietów przy użyciu kilku filtrów.  Pierwsze trzy filtry zbierają ruch wychodzący TCP tylko z lokalnego adresu IP 10.0.0.3 do portów docelowych 20, 80 i 443.  Ostatni filtr zbiera tylko ruch UDP.

```azurecli-interactive
az network watcher packet-capture create --resource-group {resourceGroupName} --vm {vmName} --name packetCaptureName --storage-account {storageAccountName} --filters "[{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"20\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"80\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"443\"},{\"protocol\":\"UDP\"}]"
```

Poniższy przykład jest oczekiwanym wyjściem z uruchomienia `az network watcher packet-capture create` polecenia.

```json
{
  "bytesToCapturePerPacket": 0,
  "etag": "W/\"b8cf3528-2e14-45cb-a7f3-5712ffb687ac\"",
  "filters": [
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "20"
    },
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "80"
    },
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "443"
    },
    {
      "localIpAddress": "",
      "localPort": "",
      "protocol": "UDP",
      "remoteIpAddress": "",
      "remotePort": ""
    }
  ],
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/pa
cketCaptures/packetCaptureName",
  "name": "packetCaptureName",
  "provisioningState": "Succeeded",
  "resourceGroup": "NetworkWatcherRG",
  "storageLocation": {
    "filePath": null,
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/gwteststorage123abc",
    "storagePath": "https://gwteststorage123abc.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/{resourceGroupName}/p
roviders/microsoft.compute/virtualmachines/{vmName}/2017/05/25/packetcapture_16_22_34_630.cap"
  },
  "target": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}",
  "timeLimitInSeconds": 18000,
  "totalBytesPerSession": 1073741824
}
```

## <a name="get-a-packet-capture"></a>Pobierz przechwycenie pakietu

Uruchomienie `az network watcher packet-capture show-status` polecenia powoduje pobranie stanu aktualnie uruchomionego lub ukończonego przechwytywania pakietów.

```azurecli-interactive
az network watcher packet-capture show-status --name packetCaptureName --location {networkWatcherLocation}
```

Poniższy przykład to dane wyjściowe `az network watcher packet-capture show-status` polecenia. Poniższy przykład dotyczy, gdy przechwytywanie zostanie zatrzymane z parametru przyczynaZatrzymania TimeExceeded.

```
{
  "additionalProperties": {
    "status": "Succeeded"
  },
  "captureStartTime": "2016-12-06T17:20:01.5671279Z",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/packetCaptures/packetCaptureName",
  "name": "packetCaptureName",
  "packetCaptureError": [],
  "packetCaptureStatus": "Stopped",
  "stopReason": "TimeExceeded"
}
```

## <a name="stop-a-packet-capture"></a>Zatrzymywanie przechwytywania pakietu

Uruchamiając `az network watcher packet-capture stop` polecenie, jeśli sesja przechwytywania jest w toku, zostanie zatrzymana.

```azurecli-interactive
az network watcher packet-capture stop --name packetCaptureName --location westcentralus
```

> [!NOTE]
> Polecenie nie zwraca odpowiedzi w przypadku uruchomienia aktualnie uruchomionej sesji przechwytywania lub istniejącej sesji, która została już zatrzymana.

## <a name="delete-a-packet-capture"></a>Usuwanie przechwytywania pakietu

```azurecli-interactive
az network watcher packet-capture delete --name packetCaptureName --location westcentralus
```

> [!NOTE]
> Usunięcie przechwycenia pakietu nie powoduje usunięcia pliku na koncie magazynu.

## <a name="download-a-packet-capture"></a>Pobieranie przechwytywania pakietów

Po zakończeniu sesji przechwytywania pakietów plik przechwytywania można przekazać do magazynu obiektów blob lub do pliku lokalnego na maszynie wirtualnej. Lokalizacja przechowywania przechwycenia pakietu jest definiowana podczas tworzenia sesji. Wygodnym narzędziem do uzyskiwania dostępu do tych plików przechwytywania zapisanych na koncie magazynu jest Eksplorator usługi Microsoft Azure Storage, które można pobrać tutaj:  https://storageexplorer.com/

Jeśli określono konto magazynu, pliki przechwytywania pakietów są zapisywane na koncie magazynu w następującej lokalizacji:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>Następne kroki

Informacje o automatyzowaniu przechwytywania pakietów przy użyciu alertów dotyczących maszyn wirtualnych poprzez wyświetlanie [funkcji tworzenia alertu wyzwolenie pakietu](network-watcher-alert-triggered-packet-capture.md)

Sprawdź, czy określony ruch jest dozwolony w lub z maszyny wirtualnej, odwiedzając [sprawdzenie przepływu IP](diagnose-vm-network-traffic-filtering-problem.md)

<!-- Image references -->