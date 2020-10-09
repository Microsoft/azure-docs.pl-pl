---
title: Tworzenie maszyny wirtualnej przez dołączenie dysku zarządzanego jako dysku systemu operacyjnego — PowerShell
description: Przykładowy skrypt programu Azure PowerShell — tworzenie maszyny wirtualnej przez dołączenie dysku zarządzanego jako dysk systemu operacyjnego
documentationcenter: virtual-machines
author: ramankumarlive
manager: kavithag
ms.service: virtual-machines-windows
ms.subservice: disks
ms.topic: sample
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 038ec433a4f27eec5ace537ff9b08e5ff6c2f6a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89323182"
---
# <a name="create-a-virtual-machine-using-an-existing-managed-os-disk-with-powershell"></a>Tworzenie maszyny wirtualnej przy użyciu istniejącego zarządzanego dysku systemu operacyjnego za pomocą programu Azure PowerShell 

Ten skrypt tworzy maszynę wirtualną przez dołączenie istniejącego dysku zarządzanego jako dysku systemu operacyjnego. Użyj tego skryptu w poprzednich scenariuszach:
* Tworzenie maszyny wirtualnej na podstawie istniejącego zarządzanego dysku systemu operacyjnego, który został skopiowany z dysku zarządzanego z innej subskrypcji
* Tworzenie maszyny wirtualnej na podstawie istniejącego dysku zarządzanego utworzonego z wyspecjalizowanego pliku VHD 
* Tworzenie maszyny wirtualnej na podstawie istniejącego zarządzanego dysku systemu operacyjnego utworzonego z migawki 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.ps1 "Create VM from snapshot")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następujących poleceń w celu uzyskania właściwości dysku zarządzanego, dołączenia dysku zarządzanego do nowej maszyny wirtualnej i utworzenia maszyny wirtualnej. Każda pozycja w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [Get-AzDisk](/powershell/module/az.compute/get-azdisk) | Pobiera obiekt dysku na podstawie nazwy i grupy zasobów dysku. Właściwość identyfikatora zwróconego obiektu dysku jest używana do dołączania dysku do nowej maszyny wirtualnej |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Tworzy konfigurację maszyny wirtualnej. Ta konfiguracja zawiera informacje, takie jak nazwa maszyny wirtualnej, system operacyjny i poświadczenia administracyjne. Jest ona używana podczas tworzenia maszyny wirtualnej. |
| [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) | Dołącza dysk zarządzany do nowej maszyny wirtualnej przy użyciu właściwości identyfikatora dysku jako dysk systemu operacyjnego |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Tworzy publiczny adres IP. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Tworzy interfejs sieciowy. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Tworzy maszynę wirtualną. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów i wszystkie zasoby w niej zawarte. |

W przypadku obrazów w portalu Marketplace użyj polecenia [Set-AzVMPlan](/powershell/module/az.compute/set-azvmplan), aby ustawić informacje o planie.

```powershell
Set-AzVMPlan -VM $VirtualMachine -Publisher $Publisher -Product $Product -Name $Bame
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/).

Więcej przykładowych skryptów programu PowerShell na potrzeby maszyny wirtualnej można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Windows](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
