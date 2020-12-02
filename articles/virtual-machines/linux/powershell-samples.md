---
title: Przykłady programu PowerShell
description: Lista przykładów programu PowerShell dla maszyn wirtualnych
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.openlocfilehash: cc11c21eda243df1298286c4745588bc492e955d
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498493"
---
# <a name="azure-vm-powershell-samples-for-creating-and-managing-linux-vms"></a>Przykłady programu PowerShell dla maszyn wirtualnych platformy Azure do tworzenia maszyn wirtualnych z systemem Linux i zarządzania nimi

Poniższa tabela zawiera linki do przykładów skryptów programu PowerShell, które umożliwiają tworzenie maszyn wirtualnych z systemem Linux i zarządzanie nimi.

| Skrypt | Opis |
|---|---|
|**Tworzenie maszyn wirtualnych**||
| [Tworzenie w pełni skonfigurowanej maszyny wirtualnej](./../scripts/virtual-machines-linux-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.|
| [Tworzenie maszyny wirtualnej z włączoną platformą Docker](./../scripts/virtual-machines-linux-powershell-sample-create-docker-host.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy maszynę wirtualną, konfiguruje tę MASZYNę wirtualną jako hosta platformy Docker i uruchamia kontener NGINX. |
| [Tworzenie maszyny wirtualnej i uruchamianie skryptu konfiguracji](./../scripts/virtual-machines-linux-powershell-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy maszynę wirtualną i używa rozszerzenia niestandardowego skryptu platformy Azure do zainstalowania NGINX. |
| [Tworzenie maszyny wirtualnej z zainstalowaną platformą WordPress](./../scripts/virtual-machines-linux-powershell-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy maszynę wirtualną i używa rozszerzenia niestandardowego skryptu platformy Azure do zainstalowania oprogramowania WordPress. |
| [Tworzenie maszyny wirtualnej na podstawie zarządzanego dysku systemu operacyjnego](./../scripts/virtual-machines-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy maszynę wirtualną przez dołączenie istniejącego dysku zarządzanego jako dysku systemu operacyjnego. |
| [Tworzenie maszyny wirtualnej na podstawie migawki](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy maszynę wirtualną na podstawie migawki, tworząc najpierw dysk zarządzany na podstawie migawki, a następnie dołączając nowy dysk zarządzany jako dysk systemu operacyjnego. |
|**Zarządzanie magazynem**||
| [Tworzenie dysku zarządzanego na podstawie dysku VHD w tej samej lub innej subskrypcji](../scripts/virtual-machines-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy dysk zarządzany na podstawie wyspecjalizowanego wirtualnego dysku twardego jako dysku systemu operacyjnego lub z wirtualnego dysku twardego danych jako dysku danych w tej samej lub innej subskrypcji.  |
| [Tworzenie dysku zarządzanego na podstawie migawki](../scripts/virtual-machines-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy dysk zarządzany na podstawie migawki. |
| [Eksportowanie migawki jako dysku VHD na konto magazynu](../scripts/virtual-machines-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Eksportuje zarządzaną migawkę jako dysk VHD do konta magazynu w innym regionie. |
| [Eksportowanie wirtualnego dysku twardego dysku zarządzanego na konto magazynu](../scripts/virtual-machines-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Eksportuje podstawowy dysk VHD dysku zarządzanego do konta magazynu w innym regionie. |
| [Tworzenie migawki z dysku VHD](../scripts/virtual-machines-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy migawkę z dysku VHD, a następnie używa tej migawki do szybkiego tworzenia wielu identycznych dysków zarządzanych.  |
| [Kopiowanie migawki do tej samej lub innej subskrypcji](../scripts/virtual-machines-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Kopiuje migawkę do tej samej lub innej subskrypcji, która znajduje się w tym samym regionie co migawka nadrzędna. |
|**Monitorowanie maszyn wirtualnych**||
| [Monitorowanie maszyny wirtualnej za pomocą dzienników usługi Azure Monitor](./../scripts/virtual-machines-linux-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy maszynę wirtualną, instaluje agenta Log Analytics i rejestruje maszynę wirtualną w obszarze roboczym Log Analytics.  |
| [Kopiowanie dysku zarządzanego do tej samej lub innej subskrypcji](../scripts/virtual-machines-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Kopiuje dysk zarządzany do tej samej lub innej subskrypcji, która znajduje się w tym samym regionie co nadrzędny dysk zarządzany.
| [Zbieranie szczegółów dotyczących wszystkich maszyn wirtualnych w ramach subskrypcji przy użyciu programu PowerShell](../scripts/virtual-machines-powershell-sample-collect-vm-details.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy wolumin CSV, który zawiera nazwę maszyny wirtualnej, nazwę grupy zasobów, region, Virtual Network, podsieć, prywatny adres IP, typ systemu operacyjnego i publiczny adres IP maszyn wirtualnych w podanej subskrypcji.
| | |