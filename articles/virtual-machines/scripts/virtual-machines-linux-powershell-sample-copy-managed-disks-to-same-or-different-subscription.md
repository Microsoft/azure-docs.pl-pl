---
title: Kopiowanie dysków zarządzanych do subskrypcji — przykład programu PowerShell
description: Przykład skryptu programu Azure PowerShell — kopiowanie (lub przenoszenie) dysków zarządzanych do tej samej lub innej subskrypcji
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/06/2017
ms.author: ramankum
ms.openlocfilehash: 35890d7b19451aa8051618871c9ca485aabca2c9
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458526"
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
| [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzDiskConfig) | Tworzy konfigurację dysku, która jest używana do tworzenia dysku. Zawiera identyfikator zasobu dysku zarządzanego oraz lokalizację, która jest taka sama jak lokalizacja dysku nadrzędnego.  |
| [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Tworzy dysk przy użyciu konfiguracji dysku, nazwy dysku i nazwy grupy zasobów, przekazanych jako parametry. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładowych skryptów programu PowerShell na potrzeby maszyny wirtualnej można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Linux](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).