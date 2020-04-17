---
title: Przykłady programu PowerShell maszyny wirtualnej platformy Azure
description: Przykłady programu PowerShell maszyny wirtualnej platformy Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.openlocfilehash: c726678578e3972ae57bdf99b75d0ddd52cd2d38
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460974"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Przykłady programu PowerShell usługi Azure Virtual Machine

Poniższa tabela zawiera łącza do przykładów skryptów programu PowerShell, które tworzą maszyny wirtualne systemu Linux i zarządzają nimi.

| | |
|---|---|
|**Tworzenie maszyn wirtualnych**||
| [Tworzenie w pełni skonfigurowanej maszyny wirtualnej](./../scripts/virtual-machines-linux-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.|
| [Tworzenie maszyny Wirtualnej z włączoną funkcją platformy Docker](./../scripts/virtual-machines-linux-powershell-sample-create-docker-host.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy maszynę wirtualną, konfiguruje tę maszynę wirtualną jako host platformy Docker i uruchamia kontener NGINX. |
| [Tworzenie maszyny Wirtualnej i uruchamianie skryptu konfiguracji](./../scripts/virtual-machines-linux-powershell-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy maszynę wirtualną i używa rozszerzenia skryptu niestandardowego platformy Azure do zainstalowania NGINX. |
| [Tworzenie maszyny wirtualnej z zainstalowanym wordpressem](./../scripts/virtual-machines-linux-powershell-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy maszynę wirtualną i używa rozszerzenia skryptu niestandardowego platformy Azure do zainstalowania wordpressa. |
| [Tworzenie maszyny wirtualnej na dysku zarządzanego systemu operacyjnego](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy maszynę wirtualną, dołączając istniejący dysk zarządzany jako dysk systemu operacyjnego. |
| [Tworzenie maszyny Wirtualnej na podstawie migawki](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy maszynę wirtualną z migawki, najpierw tworząc dysk zarządzany z migawki, a następnie dołączając nowy dysk zarządzany jako dysk systemu operacyjnego. |
|**Zarządzanie magazynem**||
| [Tworzenie dysku zarządzanego z dysku VHD w tej samej lub innej subskrypcji](../scripts/virtual-machines-linux-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy dysk zarządzany ze specjalistycznego dysku VHD jako dysk systemu operacyjnego lub z dysku wirtualnego danych jako dysk danych w tej samej lub innej subskrypcji.  |
| [Tworzenie dysku zarządzanego na podstawie migawki](../scripts/virtual-machines-linux-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy dysk zarządzany z migawki. |
| [Eksportowanie migawki jako dysku VHD na konto magazynu](../scripts/virtual-machines-linux-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Eksportuje migawkę zarządzaną jako dysk VHD do konta magazynu w innym regionie. |
| [Eksportowanie wirtualnego dysku twardego dysku zarządzanego na konto magazynu](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Eksportuje podstawowy dysk wirtualny dysku zarządzanego do konta magazynu w innym regionie. |
| [Tworzenie migawki z dysku VHD](../scripts/virtual-machines-linux-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy migawkę z dysku VHD, a następnie używa tej migawki, aby szybko utworzyć wiele identycznych dysków zarządzanych.  |
| [Kopiowanie migawki do tej samej lub innej subskrypcji](../scripts/virtual-machines-linux-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Kopiuje migawkę do tej samej lub innej subskrypcji, która znajduje się w tym samym regionie co migawka nadrzędna. |
|**Monitorowanie maszyn wirtualnych**||
| [Monitorowanie maszyny wirtualnej za pomocą dzienników usługi Azure Monitor](./../scripts/virtual-machines-linux-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy maszynę wirtualną, instaluje agenta usługi Log Analytics i rejestruje maszynę wirtualną w obszarze roboczym usługi Log Analytics.  |
| [Kopiowanie dysku zarządzanego do tej samej lub innej subskrypcji](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Kopiuje dysk zarządzany do tej samej lub innej subskrypcji, która znajduje się w tym samym regionie co nadrzędny dysk zarządzany.
| [Zbieranie szczegółowych informacji o wszystkich maszynach wirtualnych w ramach subskrypcji za pomocą programu PowerShell](../scripts/virtual-machines-powershell-sample-collect-vm-details.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy plik csv zawierający nazwę maszyny Wirtualnej, nazwę grupy zasobów, region, sieć wirtualną, podsieć, prywatny adres IP, typ systemu operacyjnego i publiczny adres IP maszyn wirtualnych w dostarczonej subskrypcji.
| | |
