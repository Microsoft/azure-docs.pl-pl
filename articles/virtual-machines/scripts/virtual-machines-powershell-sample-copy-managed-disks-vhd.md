---
title: Eksportuj/Kopiuj dysk VHD z dysku zarządzanego do konta innego regionu (Windows) — PowerShell
description: Przykładowy skrypt programu Azure PowerShell — eksportowanie/kopiowanie wirtualnego dysku twardego dysku zarządzanego na konto magazynu w tym samym lub innym regionie
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
ms.date: 09/17/2018
ms.author: ramankum
ms.openlocfilehash: a02b55adf4ac1838e9fcb98b9dffcfbd2b4b52d4
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91969905"
---
# <a name="exportcopy-the-vhd-of-a-managed-disk-to-a-storage-account-in-different-region-with-powershell-windows"></a>Eksportuj/Kopiuj dysk VHD dysku zarządzanego do konta magazynu w innym regionie przy użyciu programu PowerShell (Windows)

Ten skrypt umożliwia wyeksportowanie wirtualnego dysku twardego dysku zarządzanego na konto magazynu w innym regionie. Najpierw generuje on identyfikator URI sygnatury dostępu współdzielonego dysku zarządzanego, a następnie używa go do skopiowania odpowiedniego wirtualnego dysku twardego na konto magazynu w innym regionie. Ten skrypt umożliwia kopiowanie dysków zarządzanych do innego regionu w celu zwiększenia liczby regionów.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-managed-disks-vhd-to-storage-account/copy-managed-disks-vhd-to-storage-account.ps1 "Copy the VHD of a managed disk")]


## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt generuje identyfikator URI sygnatury dostępu współdzielonego dysku zarządzanego przy użyciu poniższych poleceń oraz kopiuje odpowiedni wirtualny dysk twardy na konto magazynu przy użyciu identyfikatora URI sygnatury dostępu współdzielonego. Z każdym poleceniem w tabeli jest skojarzony link prowadzący do odpowiedniej dokumentacji.

| Polecenie | Uwagi |
|---|---|
| [Grant-AzDiskAccess](/powershell/module/az.compute/grant-azdiskaccess) | Generuje identyfikator URI sygnatury dostępu współdzielonego dla dysku zarządzanego, który umożliwia skopiowanie odpowiedniego wirtualnego dysku twardego na konto magazynu. |
| [New-AzureStorageContext](/powershell/module/azure.storage/new-azurestoragecontext) | Tworzy kontekst konta magazynu przy użyciu nazwy konta i klucza. Ten kontekst umożliwia wykonywanie operacji odczytu/zapisu na koncie magazynu. |
| [Start-AzureStorageBlobCopy](/powershell/module/azure.storage/start-azurestorageblobcopy) | Kopiuje odpowiedni wirtualny dysk twardy migawki na konto magazynu |

## <a name="next-steps"></a>Następne kroki

[Tworzenie dysku zarządzanego na podstawie dysku VHD](./virtual-machines-powershell-sample-create-managed-disk-from-vhd.md?toc=%252fpowershell%252fmodule%252ftoc.json)

[Tworzenie maszyny wirtualnej na podstawie dysku zarządzanego](./virtual-machines-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/).

Więcej przykładowych skryptów programu PowerShell na potrzeby maszyny wirtualnej można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Windows](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).