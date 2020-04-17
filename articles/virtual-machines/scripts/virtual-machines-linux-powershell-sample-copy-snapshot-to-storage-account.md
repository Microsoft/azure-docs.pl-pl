---
title: Przykład programu PowerShell — eksportowanie/kopiowanie migawki jako dysku VHD na konto magazynu w innym regionie
description: Przykładowy skrypt programu Azure PowerShell — eksportowanie/kopiowanie migawki jako dysku VHD na konto magazynu w tym samym lub innym regionie
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 540ad6e820a909f86b43d1957d8abb407a0606d8
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460342"
---
# <a name="exportcopy-managed-snapshots-as-vhd-to-a-storage-account-in-different-region-with-powershell"></a>Eksportowanie/kopiowanie zarządzanych migawek jako dysku VHD do konta magazynu w innym regionie przy użyciu programu PowerShell

Ten skrypt eksportuje zarządzaną migawkę do konta magazynu w innym regionie. Najpierw generuje on identyfikator URI sygnatury dostępu współdzielonego migawki, a następnie używa go w celu skopiowania go do konta magazynu w innym regionie. Ten skrypt umożliwia obsługę kopii zapasowej dysków zarządzanych w innym regionie na potrzeby odzyskiwania po awarii.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-storage-account/copy-snapshot-to-storage-account.ps1 "Copy snapshot")]


## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa poniższych poleceń do generowania identyfikatora URI sygnatury dostępu współdzielonego dla migawki zarządzanej i kopiuje migawkę do konta magazynu przy użyciu identyfikatora URI sygnatury dostępu współdzielonego. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [Grant-AzSnapshotAccess](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Generuje identyfikator URI sygnatury dostępu współdzielonego dla migawki używanej w celu skopiowania jej do konta magazynu. |
| [New-AzureStorageContext](https://docs.microsoft.com/powershell/module/azure.storage/New-AzureStorageContext) | Tworzy kontekst konta magazynu przy użyciu nazwy konta i klucza. Ten kontekst umożliwia wykonywanie operacji odczytu/zapisu na koncie magazynu. |
| [Start-AzureStorageBlobCopy](https://docs.microsoft.com/powershell/module/azure.storage/Start-AzureStorageBlobCopy) | Kopiuje odpowiedni wirtualny dysk twardy migawki na konto magazynu |

## <a name="next-steps"></a>Następne kroki

[Tworzenie dysku zarządzanego na podstawie dysku VHD](virtual-machines-linux-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Tworzenie maszyny wirtualnej na podstawie dysku zarządzanego](./virtual-machines-linux-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładowych skryptów programu PowerShell na potrzeby maszyny wirtualnej można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Linux](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).