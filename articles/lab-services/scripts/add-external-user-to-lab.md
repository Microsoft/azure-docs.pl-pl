---
title: PowerShell — Dodawanie użytkownika zewnętrznego do laboratorium w Azure DevTest Labs
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
ms.openlocfilehash: 40103294e2f610e3ff5879c650f835d4c3e4c207
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82101726"
---
# <a name="use-powershell-to-add-an-external-user-to-a-lab-in-azure-devtest-labs"></a>Dodawanie użytkownika zewnętrznego do laboratorium w Azure DevTest Labs przy użyciu programu PowerShell

Ten przykładowy skrypt programu PowerShell dodaje użytkownika zewnętrznego do laboratorium w Azure DevTest Labs. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Wymagania wstępne
* **Laboratorium**. Skrypt wymaga posiadania istniejącego laboratorium. 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-external-user-to-lab/add-external-user-to-custom-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń: 

| Polecenie | Uwagi |
|---|---|
| [Get-AzADUser](/powershell/module/az.resources/get-azaduser) | Ponawia próbę obiektu użytkownika z usługi Azure Active Directory. |
| [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) | Przypisuje określoną rolę do określonego podmiotu zabezpieczeń w określonym zakresie. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Dodatkowe przykłady skryptów programu Azure Lab Services PowerShell można znaleźć w [przykładach Azure Lab Services programu PowerShell](../samples-powershell.md).
