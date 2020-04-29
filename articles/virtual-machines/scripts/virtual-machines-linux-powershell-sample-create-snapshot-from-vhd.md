---
title: Tworzenie migawki z wirtualnego dysku twardego w celu utworzenia wielu identycznych dysków zarządzanych — przykład programu PowerShell
description: Przykładowy skrypt programu Azure PowerShell — tworzenie migawki z wirtualnego dysku twardego w celu utworzenia wielu identycznych dysków zarządzanych w krótkim czasie
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
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: b163b5a1aad343382b9b2d22a91c1b07078c14df
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81459716"
---
# <a name="create-a-snapshot-from-a-vhd-to-create-multiple-identical-managed-disks-in-small-amount-of-time-with-powershell"></a>Tworzenie migawki z wirtualnego dysku twardego w celu utworzenia wielu takich samych dysków zarządzanych w krótkim czasie przy użyciu programu PowerShell

Ten skrypt tworzy migawkę z pliku VHD na koncie magazynu w ramach tej samej lub innej subskrypcji. Ten skrypt umożliwia zaimportowanie wyspecjalizowanego (nieuogólnionego/nieprzetworzonego przez program Sysprep) wirtualnego dysku twardego do migawki, a następnie utworzenie wielu identycznych dysków zarządzanych w krótkim czasie za pomocą migawki. Ponadto za jego pomocą można zaimportować dane wirtualnego dysku twardego do migawki, a następnie przy użyciu migawki utworzyć wiele identycznych dysków zarządzanych w krótkim czasie.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-snapshots-from-vhd-in-different-subscription/create-snapshots-from-vhd-in-different-subscription.ps1 "Create snapshot from VHD")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa poniższych poleceń w celu utworzenia dysku zarządzanego na podstawie wirtualnego dysku twardego w innej subskrypcji. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzDiskConfig) | Tworzy konfigurację dysku, która jest używana do tworzenia dysku. Zawiera ona typ magazynu, lokalizację, identyfikator zasobu konta magazynu, w którym przechowywany jest nadrzędny wirtualny dysk twardy, oraz identyfikator URI nadrzędnego wirtualnego dysku twardego. |
| [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Tworzy dysk przy użyciu konfiguracji dysku, nazwy dysku i nazwy grupy zasobów, przekazanych jako parametry. |

## <a name="next-steps"></a>Następne kroki

[Tworzenie dysku zarządzanego na podstawie migawki](virtual-machines-linux-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładowych skryptów programu PowerShell na potrzeby maszyny wirtualnej można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Linux](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
