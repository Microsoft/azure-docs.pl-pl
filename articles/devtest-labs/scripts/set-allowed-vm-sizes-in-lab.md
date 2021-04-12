---
title: 'Skrypt programu PowerShell: Ustaw dozwolone rozmiary maszyn wirtualnych w Azure Lab Services | Microsoft Docs'
description: Ten artykuł zawiera przykładowy skrypt programu PowerShell, który ustawia dozwolone rozmiary maszyn wirtualnych (VM) w Azure Lab Services.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 476b86b7c577db17efc39dbac64a527432c916b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95998140"
---
# <a name="use-powershell-to-set-allowed-vm-sizes-in-azure-lab-services"></a>Użyj programu PowerShell, aby ustawić dozwolone rozmiary maszyn wirtualnych w Azure Lab Services

Ten przykładowy skrypt programu PowerShell ustawia dozwolone rozmiary maszyn wirtualnych w Azure Lab Services.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne
* **Laboratorium**. Skrypt wymaga posiadania istniejącego laboratorium. 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/devtest-lab/set-allowed-vm-sizes-in-lab/set-allowed-vm-sizes-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń: 

| Polecenie | Uwagi |
|---|---|
| Find-AzResource | Wyszukuje zasoby na podstawie określonych parametrów. |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Pobiera zasoby. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Modyfikuje zasób. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | Utworzyć zasób |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/).

Dodatkowe przykłady skryptów programu Azure Lab Services PowerShell można znaleźć w [przykładach Azure Lab Services programu PowerShell](../samples-powershell.md).
