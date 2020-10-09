---
title: Przykłady programu PowerShell dla maszyny wirtualnej platformy Azure
description: Przykłady programu PowerShell dla maszyny wirtualnej platformy Azure
author: cynthn
ms.service: virtual-machines-windows
ms.topic: sample
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: e8489ecb9343cf70bd8523aa45f04a6bde171b98
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89321756"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Przykłady programu PowerShell dla maszyny wirtualnej platformy Azure

Poniższa tabela zawiera linki do przykładów skryptów programu PowerShell, które umożliwiają tworzenie maszyn wirtualnych z systemem Windows i zarządzanie nimi.

| Skrypt | Opis |
|---|---|
|**Tworzenie maszyn wirtualnych**||
| [Szybkie tworzenie maszyny wirtualnej](./../scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby z minimalnymi komunikatami.|
| [Tworzenie w pełni skonfigurowanej maszyny wirtualnej](./../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.|
| [Tworzenie maszyn wirtualnych o wysokiej dostępności](./../scripts/virtual-machines-windows-powershell-sample-create-nlb-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy kilka maszyn wirtualnych w konfiguracji o wysokiej dostępności i o zrównoważonym obciążeniu.|
| [Tworzenie maszyny wirtualnej i uruchamianie skryptu konfiguracji](./../scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy maszynę wirtualną i używa rozszerzenia niestandardowego skryptu platformy Azure do zainstalowania usług IIS. |
| [Tworzenie maszyny wirtualnej i uruchamianie konfiguracji DSC](./../scripts/virtual-machines-windows-powershell-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy maszynę wirtualną i używa rozszerzenia konfiguracji żądanego stanu (DSC) platformy Azure w celu zainstalowania usług IIS. |
| [Przekazywanie wirtualnego dysku twardego i tworzenie maszyn wirtualnych](./../scripts/virtual-machines-windows-powershell-upload-generalized-script.md) | Przekazuje lokalny plik VHD na platformę Azure, tworzy obraz z dysku VHD, a następnie tworzy maszynę wirtualną na podstawie tego obrazu. |
| [Tworzenie maszyny wirtualnej na podstawie zarządzanego dysku systemu operacyjnego](./../scripts/virtual-machines-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy maszynę wirtualną przez dołączenie istniejącego dysku zarządzanego jako dysku systemu operacyjnego. |
| [Tworzenie maszyny wirtualnej na podstawie migawki](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy maszynę wirtualną na podstawie migawki, tworząc najpierw dysk zarządzany na podstawie migawki, a następnie dołączając nowy dysk zarządzany jako dysk systemu operacyjnego. |
|**Zarządzanie magazynem**||
| [Tworzenie dysku zarządzanego na podstawie dysku VHD w tej samej lub innej subskrypcji](../scripts/virtual-machines-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy dysk zarządzany na podstawie wyspecjalizowanego wirtualnego dysku twardego jako dysku systemu operacyjnego lub z wirtualnego dysku twardego danych jako dysku danych w tej samej lub innej subskrypcji.  |
| [Tworzenie dysku zarządzanego na podstawie migawki](../scripts/virtual-machines-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy dysk zarządzany na podstawie migawki. |
| [Kopiowanie dysku zarządzanego do tej samej lub innej subskrypcji](../scripts/virtual-machines-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | Kopiuje dysk zarządzany do tej samej lub innej subskrypcji, która znajduje się w tym samym regionie co nadrzędny dysk zarządzany.
| [Eksportowanie migawki jako dysku VHD na konto magazynu](../scripts/virtual-machines-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Eksportuje zarządzaną migawkę jako dysk VHD do konta magazynu w innym regionie. |
| [Eksportowanie wirtualnego dysku twardego dysku zarządzanego na konto magazynu](../scripts/virtual-machines-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Eksportuje podstawowy dysk VHD dysku zarządzanego do konta magazynu w innym regionie. |
| [Tworzenie migawki z dysku VHD](../scripts/virtual-machines-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy migawkę z dysku VHD, a następnie używa tej migawki do szybkiego tworzenia wielu identycznych dysków zarządzanych.  |
| [Kopiowanie migawki do tej samej lub innej subskrypcji](../scripts/virtual-machines-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Kopiuje migawkę do tej samej lub innej subskrypcji, która znajduje się w tym samym regionie co migawka nadrzędna. |
|**Bezpieczne maszyny wirtualne**||
| [Szyfrowanie maszyny wirtualnej i jej dysków danych](./../scripts/virtual-machines-windows-powershell-sample-encrypt-vm.md?toc=%2fpowershell%2fazure%2ftoc.json) | Tworzy magazyn kluczy platformy Azure, klucz szyfrowania i nazwę główną usługi, a następnie szyfruje maszynę wirtualną. |
|**Monitorowanie maszyn wirtualnych**||
| [Monitorowanie maszyny wirtualnej za pomocą Azure Monitor](./../scripts/virtual-machines-windows-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy maszynę wirtualną, instaluje agenta usługi Azure Log Analytics i rejestruje maszynę wirtualną w obszarze roboczym Log Analytics.  |
| [Zbieranie szczegółów dotyczących wszystkich maszyn wirtualnych w ramach subskrypcji przy użyciu programu PowerShell](../scripts/virtual-machines-powershell-sample-collect-vm-details.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy wolumin CSV, który zawiera nazwę maszyny wirtualnej, nazwę grupy zasobów, region, Virtual Network, podsieć, prywatny adres IP, typ systemu operacyjnego i publiczny adres IP maszyn wirtualnych w podanej subskrypcji.
| | |
