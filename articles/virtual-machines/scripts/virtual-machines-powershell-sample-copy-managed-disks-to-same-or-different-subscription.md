---
title: Kopiuj dyski zarządzane do subskrypcji — przykład programu PowerShell
description: Przykładowy skrypt Azure PowerShell — kopiowanie lub przenoszenie dysków zarządzanych do tej samej lub innej subskrypcji
documentationcenter: storage
author: ramankumarlive
manager: kavithag
ms.service: virtual-machines
ms.subservice: disks
ms.topic: sample
ms.workload: infrastructure
ms.date: 06/06/2017
ms.author: ramankum
ms.openlocfilehash: e682a1546297e7715a00c7c174ad9a17021e6029
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89320483"
---
# <a name="copy-managed-disks-in-the-same-subscription-or-different-subscription-with-powershell"></a>Kopiowanie dysków zarządzanych do tej samej lub innej subskrypcji przy użyciu programu PowerShell

Ten skrypt tworzy kopię istniejącego dysku zarządzanego w tej samej lub innej subskrypcji. Nowy dysk jest tworzony w tym samym regionie, co nadrzędny dysk zarządzany.   

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.ps1 "Copy managed disk")]


## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następujących poleceń w celu utworzenia nowego dysku zarządzanego w subskrypcji docelowej przy użyciu identyfikatora źródłowego dysku zarządzanego. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) | Tworzy konfigurację dysku, która jest używana do tworzenia dysku. Zawiera identyfikator zasobu dysku zarządzanego oraz lokalizację, która jest taka sama jak lokalizacja dysku nadrzędnego.  |
| [New-AzDisk](/powershell/module/az.compute/new-azdisk) | Tworzy dysk przy użyciu konfiguracji dysku, nazwy dysku i nazwy grupy zasobów, przekazanych jako parametry. |


## <a name="next-steps"></a>Następne kroki

[Tworzenie maszyny wirtualnej na podstawie dysku zarządzanego](./virtual-machines-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/).

Więcej przykładowych skryptów programu PowerShell na potrzeby maszyny wirtualnej można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Windows](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
