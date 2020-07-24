---
title: Migawka dysku zarządzanego do subskrypcji (Linux) — PowerShell
description: Przykładowy skrypt Azure PowerShell — kopiowanie (lub przenoszenie) migawek dysku zarządzanego do tej samej lub innej subskrypcji
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
ms.openlocfilehash: 54fbdc86ecd035593960eaa57187fbf9e35393fc
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87069310"
---
# <a name="copy-snapshot-of-a-managed-disk-in-same-subscription-or-different-subscription-with-powershell-linux"></a>Kopiowanie migawki dysku zarządzanego w ramach tej samej subskrypcji lub innej subskrypcji przy użyciu programu PowerShell (Linux)

Ten skrypt kopiuje migawkę dysku zarządzanego do tej samej lub innej subskrypcji. Użyj tego skryptu w następujących scenariuszach:

1. Przeprowadź migrację migawki w usłudze Premium Storage (Premium_LRS) do magazynu w warstwie Standardowa (Standard_LRS lub Standard_ZRS), aby zmniejszyć koszty.
1. Przeprowadź migrację migawki z magazynu lokalnie nadmiarowego (Premium_LRS, Standard_LRS) do magazynu Strefowo nadmiarowego (Standard_ZRS), aby korzystać z wyższej niezawodności magazynu ZRS.
1. Przenieś migawkę do innej subskrypcji w tym samym regionie w celu dłuższego przechowywania.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.ps1 "Copy snapshot")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następujących poleceń w celu utworzenia migawki w subskrypcji docelowej przy użyciu identyfikatora migawki źródła. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzSnapshotConfig](/powershell/module/az.compute/new-azsnapshotconfig) | Tworzy konfigurację migawki, która służy do tworzenia migawki. Zawiera identyfikator zasobu migawki nadrzędnej oraz lokalizację, która jest taka sama jak lokalizacja migawki nadrzędnej.  |
| [New-AzSnapshot](/powershell/module/az.compute/new-azdisk) | Tworzy migawkę przy użyciu konfiguracji migawki, nazwy migawki i nazwy grupy zasobów przekazywanych jako parametry. |

## <a name="next-steps"></a>Następne kroki

[Tworzenie maszyny wirtualnej na podstawie migawki](./virtual-machines-linux-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/).

Więcej przykładowych skryptów programu PowerShell na potrzeby maszyny wirtualnej można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Linux](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
