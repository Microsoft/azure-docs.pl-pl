---
title: Zaktualizuj rozszerzenie Network Watcher do najnowszej wersji
description: Dowiedz się, jak aktualizować rozszerzenie Network Watcher do najnowszej wersji
services: virtual-machines-windows
documentationcenter: ''
author: damendo
manager: balar
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure-services
ms.date: 09/23/2020
ms.author: damendo
ms.openlocfilehash: c386685d63894472623ffc4392a529541a91391c
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/28/2020
ms.locfileid: "91410344"
---
# <a name="how-to-update-the-network-watcher-extension-to-the-latest-the-version"></a>Jak zaktualizować rozszerzenie Network Watcher do najnowszej wersji 

## <a name="overview"></a>Przegląd

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) to usługa monitorowania wydajności sieci, diagnostyki i analizy, która umożliwia monitorowanie sieci platformy Azure. Rozszerzenie maszyny wirtualnej agenta Network Watcher jest wymaganiem do przechwytywania ruchu sieciowego na żądanie oraz innych zaawansowanych funkcji na maszynach wirtualnych platformy Azure. Rozszerzenie Network Watcher jest używane przez funkcje, takie jak monitor połączeń, monitor połączeń (wersja zapoznawcza), rozwiązywanie problemów z połączeniami i przechwytywanie pakietów.   

## <a name="prerequisites"></a>Wymagania wstępne
W tym dokumencie przyjęto założenie, że na maszynie wirtualnej zainstalowano rozszerzenie Network Watcher i zawarto instrukcje dotyczące aktualizowania go do najnowszej wersji. 

## <a name="latest-version"></a>Najnowsza wersja
Najnowsza wersja rozszerzenia Network Watcher jest obecnie `1.4.1654.1` .

## <a name="updating-your-extension"></a>Aktualizowanie rozszerzenia 

### <a name="check-your-extension-version"></a>Sprawdź wersję rozszerzenia  

**Korzystanie z Azure Portal**

1. Przejdź do bloku "rozszerzenia" maszyny wirtualnej w Azure Portal.   
2. Kliknij rozszerzenie "AzureNetworkWatcher", aby wyświetlić okienko szczegółów.  
3. Zlokalizuj numer wersji w polu "wersja".  

**Korzystanie z interfejsu wiersza polecenia platformy Azure** Uruchom poniższe polecenie w wierszu polecenia interfejsu wiersza polecenia platformy Azure.   

```azurecli
az vm extension list --resource-group  <ResourceGroupName> --vm-name <VMName>
```

Znajdź rozszerzenie AzureNetworkWatcher w danych wyjściowych i zidentyfikuj numer wersji z pola "TypeHandlerVersion" w danych wyjściowych.  


**Przy użyciu programu PowerShell** Uruchom następujące polecenia w wierszu polecenia programu PowerShell:   

```powershell
Get-AzVMExtension -ResourceGroupName <ResourceGroupName> -VMName <VMName>  
```

Znajdź rozszerzenie AzureNetworkWatcher w danych wyjściowych i zidentyfikuj numer wersji z pola "TypeHandlerVersion" w danych wyjściowych.   


### <a name="update-your-extension"></a>Aktualizowanie rozszerzenia

W przypadku, gdy wersja jest starsza niż `1.4.1654.1` (bieżąca Najnowsza wersja), zaktualizuj rozszerzenie przy użyciu dowolnej z poniższych opcji. 

**Opcja 1. Korzystanie z programu PowerShell**

```powershell
#Linux command
Set-AzVMExtension `  -ResourceGroupName "myResourceGroup1" `  -Location "WestUS" `  -VMName "myVM1" `  -Name "AzureNetworkWatcherExtension" `  -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux"   

#Windows command
Set-AzVMExtension `  -ResourceGroupName "myResourceGroup1" `  -Location "WestUS" `  -VMName "myVM1" `  -Name "AzureNetworkWatcherExtension" `  -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows"   
```


**Opcja 2: korzystanie z interfejsu wiersza polecenia platformy Azure**  

Wymuś uaktualnienie 

```azurecli
#Linux command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher" --force-update

#Windows command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" --force-update
```

Jeśli to nie zadziała. Usuń rozszerzenie i zainstaluj je ponownie, wykonując poniższe kroki. Spowoduje to automatyczne dodanie najnowszej wersji. 

Usuwanie rozszerzenia 

```azurecli
#Same for Linux and Windows
az vm extension delete --resource-group "myResourceGroup1" --vm-name "myVM1" -n "AzureNetworkWatcherExtension"

```

Ponowne instalowanie rozszerzenia

```azurecli
#Linux command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher"  

#Windows command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" 

```

**Opcja 3: ponowne uruchamianie maszyn wirtualnych**

Jeśli na potrzeby rozszerzenia NetworkWatcher jest ustawiona funkcja autoupgrade o wartości true. Ponowne uruchamianie maszyny wirtualnej Zainstaluj najnowsze rozszerzenie.


## <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym punkcie tego artykułu, możesz zapoznać się z dokumentacją rozszerzenia Network Watcher ([Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux)lub [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows)) lub skontaktować się z ekspertami platformy Azure na [forach MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie możesz zaplikować zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj temat [Microsoft Azure support — często zadawane pytania](https://azure.microsoft.com/support/faq/).
