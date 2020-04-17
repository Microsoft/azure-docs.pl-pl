---
title: Kopiowanie migawki dysku zarządzanego do subskrypcji — przykład programu PowerShell
description: Przykład skryptu programu Azure PowerShell — kopiowanie (lub przenoszenie) migawki dysku zarządzanego do tej samej lub innej subskrypcji
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
ms.openlocfilehash: a86f3e443abc86075fa0f5ff4cc129f871e5e6a3
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460872"
---
# <a name="copy-snapshot-of-a-managed-disk-in-same-subscription-or-different-subscription-with-powershell"></a>Kopiowanie migawki dysku zarządzanego do tej samej lub innej subskrypcji przy użyciu programu PowerShell

Ten skrypt kopiuje migawkę dysku zarządzanego do tej samej lub innej subskrypcji. Użyj tego skryptu dla następujących scenariuszy:

1. Migrowanie migawki w magazynie w wersji Premium (Premium_LRS) do magazynu standardowego (Standard_LRS lub Standard_ZRS), aby zmniejszyć koszty.
1. Migrowanie migawki z magazynu lokalnie nadmiarowego (Premium_LRS, Standard_LRS) do magazynu nadmiarowego strefy (Standard_ZRS), aby korzystać z większej niezawodności magazynu ZRS.
1. Przenieś migawkę do innej subskrypcji w tym samym regionie, aby uzyskać dłuższe przechowywanie.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.ps1 "Copy snapshot")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następujących poleceń w celu utworzenia migawki w subskrypcji docelowej przy użyciu identyfikatora migawki źródła. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzSnapshotConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzSnapshotConfig) | Tworzy konfigurację migawki, która służy do tworzenia migawki. Zawiera identyfikator zasobu migawki nadrzędnej oraz lokalizację, która jest taka sama jak lokalizacja migawki nadrzędnej.  |
| [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Tworzy migawkę przy użyciu konfiguracji migawki, nazwy migawki i nazwy grupy zasobów przekazywanych jako parametry. |

## <a name="next-steps"></a>Następne kroki

[Tworzenie maszyny wirtualnej na podstawie migawki](./virtual-machines-linux-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładowych skryptów programu PowerShell na potrzeby maszyny wirtualnej można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Linux](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).