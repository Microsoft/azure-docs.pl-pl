---
title: Aktualizowanie rozszerzenia Network Watcher do najnowszej wersji
description: Dowiedz się, jak zaktualizować rozszerzenie Network Watcher platformy Azure do najnowszej wersji.
services: virtual-machines-windows
documentationcenter: ''
author: damendo
manager: balar
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.workload: infrastructure-services
ms.date: 09/23/2020
ms.author: damendo
ms.openlocfilehash: c427a206e0422e66cb526a29a462d8b6bdf6818e
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965939"
---
# <a name="update-the-network-watcher-extension-to-the-latest-version"></a>Aktualizowanie rozszerzenia Network Watcher do najnowszej wersji

## <a name="overview"></a>Omówienie

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) to usługa monitorowania wydajności sieci, diagnostyki i analizy, która monitoruje sieci platformy Azure. Rozszerzenie maszyny wirtualnej agenta Network Watcher jest wymaganiem do przechwytywania ruchu sieciowego na żądanie i korzystania z innych zaawansowanych funkcji na maszynach wirtualnych platformy Azure. Rozszerzenie Network Watcher jest używane przez funkcje, takie jak monitor połączeń, monitor połączeń (wersja zapoznawcza), rozwiązywanie problemów z połączeniami i przechwytywanie pakietów.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że na maszynie wirtualnej jest zainstalowane rozszerzenie Network Watcher.

## <a name="latest-version"></a>Najnowsza wersja

Najnowsza wersja rozszerzenia Network Watcher jest obecnie `1.4.1654.1` .

## <a name="update-your-extension"></a>Aktualizowanie rozszerzenia

Aby zaktualizować rozszerzenie, musisz znać swoją wersję rozszerzenia.

### <a name="check-your-extension-version"></a>Sprawdź wersję rozszerzenia

Możesz sprawdzić wersję rozszerzenia przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure lub programu PowerShell.

#### <a name="usetheazureportal"></a>Użyj Azure Portal

1. Przejdź do okienka **rozszerzenia** maszyny wirtualnej w Azure Portal.
1. Wybierz rozszerzenie **AzureNetworkWatcher** , aby wyświetlić okienko Szczegóły.
1. W polu **wersja** Znajdź numer wersji.  

#### <a name="use-the-azure-cli"></a>Używanie interfejsu wiersza polecenia platformy Azure

Uruchom następujące polecenie w wierszu polecenia interfejsu wiersza polecenia platformy Azure:

```azurecli
az vm get-instance-view --resource-group  "SampleRG" --name "Sample-VM"
```
Znajdź wartość **"AzureNetworkWatcherExtension"** w danych wyjściowych i zidentyfikuj numer wersji z pola *"TypeHandlerVersion"* w danych wyjściowych.  Uwaga: informacje o rozszerzeniu pojawiają się wiele razy w danych wyjściowych JSON. Sprawdź w bloku "rozszerzenia" i powinien zostać wyświetlony pełny numer wersji rozszerzenia. 

Powinien wyglądać mniej więcej tak: ![ zrzut ekranu interfejsu wiersza polecenia platformy Azure](./media/network-watcher/azure-cli-screenshot.png)

#### <a name="usepowershell"></a>Korzystanie z programu PowerShell

Uruchom następujące polecenia w wierszu polecenia programu PowerShell:

```powershell
Get-AzVM -ResourceGroupName "SampleRG" -Name "Sample-VM" -Status
```
Znajdź rozszerzenie Azure Network Watcher w danych wyjściowych i zidentyfikuj numer wersji z pola *"TypeHandlerVersion"* w danych wyjściowych.   

Powinien wyglądać mniej więcej tak: ![ zrzut ekranu programu PowerShell](./media/network-watcher/powershell-screenshot.png)

### <a name="update-your-extension"></a>Aktualizowanie rozszerzenia

Jeśli wersja jest wcześniejsza niż `1.4.1654.1` , która jest bieżącą najnowszą wersją, zaktualizuj rozszerzenie przy użyciu dowolnej z poniższych opcji.

#### <a name="option-1-use-powershell"></a>Opcja 1. Korzystanie z programu PowerShell

Uruchom następujące polecenia:

```powershell
#Linux command
Set-AzVMExtension -ResourceGroupName "myResourceGroup1" -Location "WestUS" -VMName "myVM1" -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux"

#Windows command
Set-AzVMExtension -ResourceGroupName "myResourceGroup1" -Location "WestUS" -VMName "myVM1" -Name "NetworkWatcherAgentWindows" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows" -ForceRerun "True"

```

Jeśli to nie zadziała. Usuń rozszerzenie i zainstaluj je ponownie, wykonując poniższe kroki. Spowoduje to automatyczne dodanie najnowszej wersji.

Usuwanie rozszerzenia 

```powershell
#Same command for Linux and Windows
Remove-AzVMExtension -ResourceGroupName "SampleRG" -VMName "Sample-VM" -Name "AzureNetworkWatcherExtension"
``` 

Ponowne instalowanie rozszerzenia

```powershell
#Linux command
Set-AzVMExtension -ResourceGroupName "SampleRG" -Location "centralus" -VMName "Sample-VM" -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux" -typeHandlerVersion "1.4"

#Windows command
Set-AzVMExtension -ResourceGroupName "SampleRG" -Location "centralus" -VMName "Sample-VM" -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows" -typeHandlerVersion "1.4"
```

#### <a name="option-2-use-the-azure-cli"></a>Opcja 2: korzystanie z interfejsu wiersza polecenia platformy Azure

Wymuś uaktualnienie.

```azurecli
#Linux command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher" --force-update

#Windows command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" --force-update
```

Jeśli to nie zadziała, Usuń i zainstaluj ponownie rozszerzenie i wykonaj następujące kroki, aby automatycznie dodać najnowszą wersję.

Usuń rozszerzenie.

```azurecli
#Same for Linux and Windows
az vm extension delete --resource-group "myResourceGroup1" --vm-name "myVM1" -n "AzureNetworkWatcherExtension"

```

Zainstaluj rozszerzenie ponownie.

```azurecli
#Linux command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher"

#Windows command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher"

```

#### <a name="option-3-reboot-your-vms"></a>Opcja 3: ponowne uruchamianie maszyn wirtualnych

Jeśli dla rozszerzenia Network Watcher jest ustawiona funkcja autouaktualnia, należy ponownie uruchomić instalację maszyny wirtualnej, aby uzyskać najnowsze rozszerzenie.

## <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym punkcie tego artykułu, zapoznaj się z dokumentacją rozszerzenia Network Watcher dla systemu [Linux](./network-watcher-linux.md) lub [Windows](./network-watcher-windows.md). Możesz również skontaktować się z ekspertami platformy Azure na [forach MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie możesz zaplikować zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/)i wybierz pozycję **Uzyskaj pomoc techniczną**. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj temat [Microsoft Azure support — często zadawane pytania](https://azure.microsoft.com/support/faq/).
