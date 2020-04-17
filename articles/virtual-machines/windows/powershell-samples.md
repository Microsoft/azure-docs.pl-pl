---
title: Przykłady programu PowerShell maszyny wirtualnej platformy Azure
description: Przykłady programu PowerShell maszyny wirtualnej platformy Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 434cf3f9342ad10bab39030e65cdd4bc4d0edc27
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458866"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Przykłady programu PowerShell usługi Azure Virtual Machine

Poniższa tabela zawiera łącza do przykładów skryptów programu PowerShell, które tworzą maszyny wirtualne systemu Windows i zarządzają nimi.

| | |
|---|---|
|**Tworzenie maszyn wirtualnych**||
| [Szybkie tworzenie maszyny wirtualnej](./../scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby, z co najmniej monitami.|
| [Tworzenie w pełni skonfigurowanej maszyny wirtualnej](./../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.|
| [Tworzenie maszyn wirtualnych o wysokiej dostępności](./../scripts/virtual-machines-windows-powershell-sample-create-nlb-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy kilka maszyn wirtualnych w konfiguracji o wysokiej dostępności i z równoważenia obciążenia.|
| [Tworzenie maszyny Wirtualnej i uruchamianie skryptu konfiguracji](./../scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy maszynę wirtualną i używa rozszerzenia skryptu niestandardowego platformy Azure do zainstalowania usług IIS. |
| [Tworzenie maszyny wirtualnej i uruchamianie konfiguracji DSC](./../scripts/virtual-machines-windows-powershell-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy maszynę wirtualną i używa rozszerzenia konfiguracji żądanego stanu platformy Azure (DSC) do zainstalowania usług IIS. |
| [Przekazywanie dysku wirtualnego i tworzenie maszyn wirtualnych](./../scripts/virtual-machines-windows-powershell-upload-generalized-script.md) | Przekazuje lokalny plik VHD na platformę Azure, tworzy obraz z dysku VHD, a następnie tworzy maszynę wirtualną z tego obrazu. |
| [Tworzenie maszyny wirtualnej na dysku zarządzanego systemu operacyjnego](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy maszynę wirtualną, dołączając istniejący dysk zarządzany jako dysk systemu operacyjnego. |
| [Tworzenie maszyny Wirtualnej na podstawie migawki](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy maszynę wirtualną z migawki, najpierw tworząc dysk zarządzany z migawki, a następnie dołączając nowy dysk zarządzany jako dysk systemu operacyjnego. |
|**Zarządzanie magazynem**||
| [Tworzenie dysku zarządzanego z dysku VHD w tej samej lub innej subskrypcji](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy dysk zarządzany ze specjalistycznego dysku VHD jako dysk systemu operacyjnego lub z dysku wirtualnego danych jako dysk danych w tej samej lub innej subskrypcji.  |
| [Tworzenie dysku zarządzanego na podstawie migawki](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy dysk zarządzany z migawki. |
| [Kopiowanie dysku zarządzanego do tej samej lub innej subskrypcji](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | Kopiuje dysk zarządzany do tej samej lub innej subskrypcji, która znajduje się w tym samym regionie co nadrzędny dysk zarządzany.
| [Eksportowanie migawki jako dysku VHD na konto magazynu](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Eksportuje migawkę zarządzaną jako dysk VHD do konta magazynu w innym regionie. |
| [Eksportowanie wirtualnego dysku twardego dysku zarządzanego na konto magazynu](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Eksportuje podstawowy dysk wirtualny dysku zarządzanego do konta magazynu w innym regionie. |
| [Tworzenie migawki z dysku VHD](../scripts/virtual-machines-windows-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy migawkę z dysku VHD, a następnie używa tej migawki, aby szybko utworzyć wiele identycznych dysków zarządzanych.  |
| [Kopiowanie migawki do tej samej lub innej subskrypcji](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Kopiuje migawkę do tej samej lub innej subskrypcji, która znajduje się w tym samym regionie co migawka nadrzędna. |
|**Bezpieczne maszyn wirtualnych**||
| [Szyfrowanie maszyny wirtualnej i jej dysków danych](./../scripts/virtual-machines-windows-powershell-sample-encrypt-vm.md?toc=%2fpowershell%2fazure%2ftoc.json) | Tworzy magazyn kluczy platformy Azure, klucz szyfrowania i jednostkę usługi, a następnie szyfruje maszynę wirtualną. |
|**Monitorowanie maszyn wirtualnych**||
| [Monitorowanie maszyny wirtualnej za pomocą usługi Azure Monitor](./../scripts/virtual-machines-windows-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy maszynę wirtualną, instaluje agenta usługi Azure Log Analytics i rejestruje maszynę wirtualną w obszarze roboczym usługi Log Analytics.  |
| [Zbieranie szczegółowych informacji o wszystkich maszynach wirtualnych w ramach subskrypcji za pomocą programu PowerShell](../scripts/virtual-machines-powershell-sample-collect-vm-details.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy plik csv zawierający nazwę maszyny Wirtualnej, nazwę grupy zasobów, region, sieć wirtualną, podsieć, prywatny adres IP, typ systemu operacyjnego i publiczny adres IP maszyn wirtualnych w dostarczonej subskrypcji.
| | |
