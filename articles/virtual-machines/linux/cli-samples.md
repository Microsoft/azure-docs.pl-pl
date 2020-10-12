---
title: Przykłady interfejsu wiersza polecenia platformy Azure
description: Przykłady interfejsu wiersza polecenia dla usługi Azure Virtual Machines
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 03/01/2019
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 63c420a7ae082cbf38b529e71d8324550a84b7bd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89319172"
---
# <a name="azure-cli-samples-for-linux-virtual-machines"></a>Przykłady interfejsu wiersza polecenia platformy Azure dla maszyn wirtualnych z systemem Linux

Poniższa tabela zawiera linki do skryptów bash utworzonych za pomocą interfejsu wiersza polecenia platformy Azure.

| Skrypt | Opis |
|---|---|
|**Tworzenie maszyn wirtualnych**||
| [Tworzenie maszyny wirtualnej](./../scripts/virtual-machines-linux-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy maszynę wirtualną z systemem Linux z minimalną konfiguracją. |
| [Tworzenie w pełni skonfigurowanej maszyny wirtualnej](./../scripts/virtual-machines-linux-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.|
| [Tworzenie maszyn wirtualnych o wysokiej dostępności](./../scripts/virtual-machines-linux-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy kilka maszyn wirtualnych w konfiguracji o wysokiej dostępności i o zrównoważonym obciążeniu. |
| [Tworzenie maszyny wirtualnej i uruchamianie skryptu konfiguracji](./../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy maszynę wirtualną i używa rozszerzenia niestandardowego skryptu platformy Azure do zainstalowania NGINX. |
| [Tworzenie maszyny wirtualnej z zainstalowaną platformą WordPress](./../scripts/virtual-machines-linux-cli-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy maszynę wirtualną i używa rozszerzenia niestandardowego skryptu platformy Azure do zainstalowania oprogramowania WordPress. |
| [Tworzenie maszyny wirtualnej na podstawie zarządzanego dysku systemu operacyjnego](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy maszynę wirtualną przez dołączenie istniejącego dysku zarządzanego jako dysku systemu operacyjnego. |
| [Tworzenie maszyny wirtualnej na podstawie migawki](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy maszynę wirtualną na podstawie migawki przez utworzenie dysku zarządzanego na podstawie migawki, a następnie dołączenie nowego dysku zarządzanego jako dysku systemu operacyjnego. |
|**Zarządzanie magazynem**||
| [Tworzenie dysku zarządzanego na podstawie dysku VHD](../scripts/virtual-machines-cli-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy dysk zarządzany na podstawie wyspecjalizowanego wirtualnego dysku twardego jako dysku systemu operacyjnego lub z wirtualnego dysku twardego jako dysku danych.  |
| [Tworzenie dysku zarządzanego na podstawie migawki](../scripts/virtual-machines-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy dysk zarządzany na podstawie migawki. |
| [Kopiowanie dysku zarządzanego do tej samej lub innej subskrypcji](../scripts/virtual-machines-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Kopiuje dysk zarządzany do tej samej lub innej subskrypcji, ale w tym samym regionie co nadrzędny dysk zarządzany. 
| [Eksportowanie migawki jako dysku VHD na konto magazynu](../scripts/virtual-machines-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Eksportuje zarządzaną migawkę jako dysk VHD do konta magazynu w innym regionie. |
| [Eksportowanie wirtualnego dysku twardego dysku zarządzanego na konto magazynu](../scripts/virtual-machines-cli-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Eksportuje podstawowy dysk VHD dysku zarządzanego do konta magazynu w innym regionie. |
| [Kopiowanie migawki do tej samej lub innej subskrypcji](../scripts/virtual-machines-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Kopiuje migawkę do tej samej lub innej subskrypcji, ale w tym samym regionie co migawka nadrzędna. |
|**Sieciowe maszyny wirtualne**||
| [Bezpieczny ruch sieciowy między maszynami wirtualnymi](./../scripts/virtual-machines-linux-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy dwie maszyny wirtualne, wszystkie powiązane zasoby i wewnętrzną i zewnętrzną grupę zabezpieczeń sieci (sieciowej grupy zabezpieczeń). |
|**Bezpieczne maszyny wirtualne**||
| [Szyfrowanie maszyny wirtualnej i dysków danych](./../scripts/virtual-machines-linux-cli-sample-encrypt-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy Azure Key Vault, klucz szyfrowania i nazwę główną usługi, a następnie szyfruje maszynę wirtualną. |
|**Monitorowanie maszyn wirtualnych**||
| [Monitorowanie maszyny wirtualnej za pomocą dzienników usługi Azure Monitor](./../scripts/virtual-machines-linux-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Tworzy maszynę wirtualną, instaluje agenta Log Analytics i rejestruje maszynę wirtualną w obszarze roboczym Log Analytics.  |
|**Rozwiązywanie problemów z maszynami wirtualnymi**||
| [Rozwiązywanie problemów z dyskiem systemu operacyjnego maszyn wirtualnych](./../scripts/virtual-machines-linux-cli-sample-mount-os-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Instaluje dysk systemu operacyjnego z jednej maszyny wirtualnej jako dysk danych na drugiej maszynie wirtualnej. |
| | |
