---
title: PowerShell — tworzenie roli niestandardowej w laboratorium w Azure DevTest Labs
description: Ten artykuł zawiera skrypt Azure PowerShell, który dodaje użytkownika zewnętrznego do laboratorium w Azure DevTest Labs.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/22/2020
ms.author: spelluru
ms.openlocfilehash: a7822d6ea46b7efc21f43e944d0a96f609df389a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82100188"
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
| [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) | Wyświetla wszystkie role RBAC platformy Azure, które są dostępne do przypisania. |
| [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) | Tworzy rolę niestandardową. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Dodatkowe przykłady skryptów programu Azure Lab Services PowerShell można znaleźć w [przykładach Azure Lab Services programu PowerShell](../samples-powershell.md).
