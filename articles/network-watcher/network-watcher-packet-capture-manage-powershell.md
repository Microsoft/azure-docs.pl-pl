---
title: Zarządzanie przechwytywaniem pakietów — Azure PowerShell
titleSuffix: Azure Network Watcher
description: Na tej stronie wyjaśniono, jak zarządzać funkcją przechwytywania pakietów Network Watcher przy użyciu programu PowerShell
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
ms.openlocfilehash: c9a7af7575fcdd19d83e5a4f38ef03ef8948b8c0
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94963474"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-powershell"></a>Zarządzanie przechwytywaniem pakietów za pomocą usługi Azure Network Watcher przy użyciu programu PowerShell

> [!div class="op_single_selector"]
> - [Witryna Azure Portal](network-watcher-packet-capture-manage-portal.md)
> - [Program PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-packet-capture-manage-cli.md)
> - [Interfejs API REST platformy Azure](network-watcher-packet-capture-manage-rest.md)

Przechwytywanie pakietów Network Watcher umożliwia tworzenie sesji przechwytywania w celu śledzenia ruchu do i z maszyny wirtualnej. Filtry są udostępniane dla sesji przechwytywania, aby upewnić się, że przechwytywany jest tylko żądany ruch. Przechwytywanie pakietów ułatwia diagnozowanie anomalii w sieci zarówno ponownie, jak i aktywnie. Inne zastosowania obejmują gromadzenie statystyk sieci, uzyskiwanie informacji o atakach sieci, debugowanie komunikacji klient-serwer i wiele więcej. Dzięki możliwości zdalnego wyzwalania przechwycenia pakietów ta funkcja ułatwia nawiązanie ręcznego uruchamiania przechwytywania pakietów i na odpowiedniej maszynie, co pozwala zaoszczędzić cenny czas.

Ten artykuł przeprowadzi Cię przez różne zadania zarządzania, które są obecnie dostępne do przechwycenia pakietu.

- [**Rozpocznij przechwytywanie pakietu**](#start-a-packet-capture)
- [**Zatrzymywanie przechwytywania pakietu**](#stop-a-packet-capture)
- [**Usuwanie przechwytywania pakietu**](#delete-a-packet-capture)
- [**Pobieranie przechwytywania pakietów**](#download-a-packet-capture)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule założono, że masz następujące zasoby:

* Wystąpienie Network Watcher w regionie, w którym chcesz utworzyć przechwycenie pakietu

* Maszyna wirtualna z włączonym rozszerzeniem przechwytywania pakietów.

> [!IMPORTANT]
> Przechwytywanie pakietów wymaga rozszerzenia maszyny wirtualnej `AzureNetworkWatcherExtension` . Aby zainstalować rozszerzenie na maszynie wirtualnej z systemem Windows, odwiedź [rozszerzenie maszyny wirtualnej usługi azure Network Watcher Agent dla systemu Windows](../virtual-machines/extensions/network-watcher-windows.md) i dla maszyny wirtualnej z systemem Linux odwiedź [rozszerzenie maszyny wirtualnej agenta usługi Azure Network Watcher](../virtual-machines/extensions/network-watcher-linux.md).

## <a name="install-vm-extension"></a>Instalowanie rozszerzenia maszyny wirtualnej

### <a name="step-1"></a>Krok 1

```powershell
$VM = Get-AzVM -ResourceGroupName testrg -Name VM1
```

### <a name="step-2"></a>Krok 2

Poniższy przykład pobiera informacje o rozszerzeniu, które są konieczne do uruchomienia `Set-AzVMExtension` polecenia cmdlet. To polecenie cmdlet powoduje zainstalowanie agenta przechwytywania pakietów na maszynie wirtualnej gościa.

> [!NOTE]
> `Set-AzVMExtension`Wykonanie polecenia cmdlet może potrwać kilka minut.

W przypadku maszyn wirtualnych z systemem Windows:

```powershell
$AzureNetworkWatcherExtension = Get-AzVMExtensionImage -Location WestCentralUS -PublisherName Microsoft.Azure.NetworkWatcher -Type NetworkWatcherAgentWindows -Version 1.4.585.2
$ExtensionName = "AzureNetworkWatcherExtension"
Set-AzVMExtension -ResourceGroupName $VM.ResourceGroupName  -Location $VM.Location -VMName $VM.Name -Name $ExtensionName -Publisher $AzureNetworkWatcherExtension.PublisherName -ExtensionType $AzureNetworkWatcherExtension.Type -TypeHandlerVersion $AzureNetworkWatcherExtension.Version.Substring(0,3)
```

Dla maszyn wirtualnych z systemem Linux:

```powershell
$AzureNetworkWatcherExtension = Get-AzVMExtensionImage -Location WestCentralUS -PublisherName Microsoft.Azure.NetworkWatcher -Type NetworkWatcherAgentLinux -Version 1.4.13.0
$ExtensionName = "AzureNetworkWatcherExtension"
Set-AzVMExtension -ResourceGroupName $VM.ResourceGroupName  -Location $VM.Location -VMName $VM.Name -Name $ExtensionName -Publisher $AzureNetworkWatcherExtension.PublisherName -ExtensionType $AzureNetworkWatcherExtension.Type -TypeHandlerVersion $AzureNetworkWatcherExtension.Version.Substring(0,3)
```

Poniższy przykład jest pomyślną odpowiedzią po uruchomieniu `Set-AzVMExtension` polecenia cmdlet.

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   
```

### <a name="step-3"></a>Krok 3

Aby upewnić się, że Agent jest zainstalowany, uruchom `Get-AzVMExtension` polecenie cmdlet i przekaż go do nazwy maszyny wirtualnej i nazwy rozszerzenia.

```powershell
Get-AzVMExtension -ResourceGroupName $VM.ResourceGroupName  -VMName $VM.Name -Name $ExtensionName
```

Poniższy przykład jest przykładem odpowiedzi z uruchamiania `Get-AzVMExtension`

```
ResourceGroupName       : testrg
VMName                  : testvm1
Name                    : AzureNetworkWatcherExtension
Location                : westcentralus
Etag                    : null
Publisher               : Microsoft.Azure.NetworkWatcher
ExtensionType           : NetworkWatcherAgentWindows
TypeHandlerVersion      : 1.4
Id                      : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm1/
                          extensions/AzureNetworkWatcherExtension
PublicSettings          : 
ProtectedSettings       : 
ProvisioningState       : Succeeded
Statuses                : 
SubStatuses             : 
AutoUpgradeMinorVersion : True
ForceUpdateTag          : 
```

## <a name="start-a-packet-capture"></a>Rozpocznij przechwytywanie pakietu

Po wykonaniu powyższych kroków Agent przechwytywania pakietów zostanie zainstalowany na maszynie wirtualnej.

### <a name="step-1"></a>Krok 1

Następnym krokiem jest pobranie Network Watcher wystąpienia. Ta zmienna jest przenoszona do `New-AzNetworkWatcherPacketCapture` polecenia cmdlet w kroku 4.

```powershell
$networkWatcher = Get-AzResource -ResourceType "Microsoft.Network/networkWatchers" | Where {$_.Location -eq "WestCentralUS" }
```

### <a name="step-2"></a>Krok 2

Pobierz konto magazynu. To konto magazynu jest używane do przechowywania pliku przechwytywania pakietów.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName testrg -Name testrgsa123
```

### <a name="step-3"></a>Krok 3

Filtry mogą służyć do ograniczania danych przechowywanych w ramach przechwytywania pakietów. Poniższy przykład konfiguruje dwa filtry.  Jeden filtr zbiera ruch wychodzący TCP tylko z lokalnego adresu IP 10.0.0.3 do portów docelowych 20, 80 i 443.  Drugi filtr zbiera tylko ruch UDP.

```powershell
$filter1 = New-AzPacketCaptureFilterConfig -Protocol TCP -RemoteIPAddress "1.1.1.1-255.255.255.255" -LocalIPAddress "10.0.0.3" -LocalPort "1-65535" -RemotePort "20;80;443"
$filter2 = New-AzPacketCaptureFilterConfig -Protocol UDP
```

> [!NOTE]
> Dla przechwycenia pakietu można zdefiniować wiele filtrów.

### <a name="step-4"></a>Krok 4

Uruchom `New-AzNetworkWatcherPacketCapture` polecenie cmdlet, aby rozpocząć proces przechwytywania pakietów, przekazując wymagane wartości pobrane w poprzednich krokach.
```powershell

New-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $vm.Id -PacketCaptureName "PacketCaptureTest" -StorageAccountId $storageAccount.id -TimeLimitInSeconds 60 -Filter $filter1, $filter2
```

Poniższy przykład jest oczekiwanym wyjściem z uruchamiania `New-AzNetworkWatcherPacketCapture` polecenia cmdlet.

```
Name                    : PacketCaptureTest
Id                      : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatcher
                          s/NetworkWatcher_westcentralus/packetCaptures/PacketCaptureTest
Etag                    : W/"3bf27278-8251-4651-9546-c7f369855e4e"
ProvisioningState       : Succeeded
Target                  : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm1
BytesToCapturePerPacket : 0
TotalBytesPerSession    : 1073741824
TimeLimitInSeconds      : 60
StorageLocation         : {
                            "StorageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Storage/storageA
                          ccounts/examplestorage",
                            "StoragePath": "https://examplestorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-00000
                          0000000/resourcegroups/testrg/providers/microsoft.compute/virtualmachines/testvm1/2017/02/01/packetcapture_22_42_48_238.cap"
                          }
Filters                 : [
                            {
                              "Protocol": "TCP",
                              "RemoteIPAddress": "1.1.1.1-255.255.255",
                              "LocalIPAddress": "10.0.0.3",
                              "LocalPort": "1-65535",
                              "RemotePort": "20;80;443"
                            },
                            {
                              "Protocol": "UDP",
                              "RemoteIPAddress": "",
                              "LocalIPAddress": "",
                              "LocalPort": "",
                              "RemotePort": ""
                            }
                          ]


```

## <a name="get-a-packet-capture"></a>Pobierz przechwycenie pakietu

Uruchomienie `Get-AzNetworkWatcherPacketCapture` polecenia cmdlet powoduje pobranie stanu aktualnie uruchomionego lub ukończonego przechwytywania pakietów.

```powershell
Get-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

Poniższy przykład jest wyjściem z `Get-AzNetworkWatcherPacketCapture` polecenia cmdlet. Poniższy przykład jest po zakończeniu przechwytywania. Wartość PacketCaptureStatus jest zatrzymana z parametru przyczynazatrzymaniaem TimeExceeded. Ta wartość pokazuje, że przechwycenie pakietu zakończyło się pomyślnie.
```
Name                    : PacketCaptureTest
Id                      : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatcher
                          s/NetworkWatcher_westcentralus/packetCaptures/PacketCaptureTest
Etag                    : W/"4b9a81ed-dc63-472e-869e-96d7166ccb9b"
ProvisioningState       : Succeeded
Target                  : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm1
BytesToCapturePerPacket : 0
TotalBytesPerSession    : 1073741824
TimeLimitInSeconds      : 60
StorageLocation         : {
                            "StorageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Storage/storageA
                          ccounts/examplestorage",
                            "StoragePath": "https://examplestorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-00000
                          0000000/resourcegroups/testrg/providers/microsoft.compute/virtualmachines/testvm1/2017/02/01/packetcapture_22_42_48_238.cap"
                          }
Filters                 : [
                            {
                              "Protocol": "TCP",
                              "RemoteIPAddress": "1.1.1.1-255.255.255",
                              "LocalIPAddress": "10.0.0.3",
                              "LocalPort": "1-65535",
                              "RemotePort": "20;80;443"
                            },
                            {
                              "Protocol": "UDP",
                              "RemoteIPAddress": "",
                              "LocalIPAddress": "",
                              "LocalPort": "",
                              "RemotePort": ""
                            }
                          ]
CaptureStartTime        : 2/1/2017 10:43:01 PM
PacketCaptureStatus     : Stopped
StopReason              : TimeExceeded
PacketCaptureError      : []
```

## <a name="stop-a-packet-capture"></a>Zatrzymywanie przechwytywania pakietu

Uruchomienie `Stop-AzNetworkWatcherPacketCapture` polecenia cmdlet, jeśli sesja przechwytywania jest w toku, zostaje zatrzymana.

```powershell
Stop-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

> [!NOTE]
> Polecenie cmdlet nie zwraca odpowiedzi w przypadku uruchomienia aktualnie uruchomionej sesji przechwytywania lub istniejącej sesji, która została już zatrzymana.

## <a name="delete-a-packet-capture"></a>Usuwanie przechwytywania pakietu

```powershell
Remove-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
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