---
title: PowerShell — tworzenie roli niestandardowej w laboratorium w Azure DevTest Labs
description: Ten artykuł zawiera Azure PowerShell skrypt, który tworzy rolę niestandardową w środowisku laboratoryjnym Azure DevTest Labs.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 3757d6c16a762a0f76f4718f117da93322af074c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021201"
---
# <a name="use-powershell-to-create-a-custom-role-in-a-lab-in-azure-devtest-labs"></a>Używanie programu PowerShell do tworzenia roli niestandardowej w laboratorium w Azure DevTest Labs

Ten przykładowy skrypt programu PowerShell tworzy rolę niestandardową do użycia w laboratorium w Azure DevTest Labs. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne
* **Laboratorium**. Skrypt wymaga posiadania istniejącego laboratorium. 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-role-in-lab/create-custom-role-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń: 

| Polecenie | Uwagi |
|---|---|
| [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) | Pobiera operacje dla dostawcy zasobów platformy Azure, które są zabezpieczane za pomocą funkcji RBAC platformy Azure. |
| [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) | Wyświetla listę wszystkich ról platformy Azure, które są dostępne do przypisania. |
| [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) | Tworzy rolę niestandardową. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/).

Dodatkowe przykłady skryptów programu Azure Lab Services PowerShell można znaleźć w [przykładach Azure Lab Services programu PowerShell](../samples-powershell.md).
