---
title: 'Samouczek: udzielanie użytkownikom dostępu do zasobów platformy Azure przy użyciu szablonu Azure Resource Manager na platformie Azure RBAC'
description: Dowiedz się, jak przyznać użytkownikom dostęp do zasobów platformy Azure przy użyciu szablonu Azure Resource Manager i kontroli dostępu opartej na rolach (RBAC) na platformie Azure w tym samouczku.
services: role-based-access-control,azure-resource-manager
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 05/15/2019
ms.author: rolyon
ms.openlocfilehash: f98b1e9c57cf9f624c5af3e59c8afd56d073dd28
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2020
ms.locfileid: "82735474"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-an-azure-resource-manager-template"></a>Samouczek: udzielanie użytkownikom dostępu do zasobów platformy Azure przy użyciu szablonu Azure Resource Manager

[Kontrola dostępu oparta na rolach (Azure RBAC)](overview.md) umożliwia zarządzanie dostępem do zasobów platformy Azure. W tym samouczku utworzysz grupę zasobów i udzielą użytkownikowi dostępu do tworzenia maszyn wirtualnych w grupie zasobów i zarządzania nimi. Ten samouczek koncentruje się na procesie wdrażania szablonu Menedżer zasobów w celu udzielenia dostępu. Aby uzyskać więcej informacji na temat opracowywania szablonów Menedżer zasobów, zobacz [dokumentację Menedżer zasobów i dokumentacja](/azure/azure-resource-manager/) [szablonu](/azure/templates/microsoft.authorization/allversions
).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Udzielanie dostępu użytkownikowi w zakresie grupy zasobów
> * Weryfikowanie wdrożenia
> * Czyszczenie

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Wymagania wstępne

Aby dodać i usunąć przypisania ról, musisz mieć:

* `Microsoft.Authorization/roleAssignments/write`i `Microsoft.Authorization/roleAssignments/delete` uprawnienia, takie jak [administrator dostępu użytkowników](built-in-roles.md#user-access-administrator) lub [właściciel](built-in-roles.md#owner)

## <a name="grant-access"></a>Udzielanie dostępu

Szablon używany w tym przewodniku szybki start pochodzi z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-rbac-builtinrole-resourcegroup/). Więcej szablonów związanych z autoryzacją platformy Azure można znaleźć [tutaj](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization).

Aby wdrożyć szablon, wybierz pozycję **Wypróbuj** , aby otworzyć usługę Azure Cloud Shell, a następnie wklej następujący skrypt programu PowerShell do okna powłoki. Aby wkleić kod, kliknij prawym przyciskiem myszy okno powłoki, a następnie wybierz polecenie **Wklej**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$emailAddress = Read-Host -Prompt "Enter your email address that is associated with your Azure subscription (used to find the principal ID)"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

$resourceGroupName = "${projectName}rg"
$roleAssignmentName = New-Guid
$principalId = (Get-AzAdUser -Mail $emailAddress).id
$roleDefinitionId = (Get-AzRoleDefinition -name "Virtual Machine Contributor").id
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-rbac-builtinrole-resourcegroup/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -roleAssignmentName $roleAssignmentName -roleDefinitionID $roleDefinitionId -principalId $principalId
```

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Otwórz grupę zasobów utworzoną w ostatniej procedurze. Nazwa domyślna to nazwa projektu z dołączoną **RG** .
1. Z menu po lewej stronie wybierz opcję **Kontrola dostępu (IAM)**.
1. Wybierz pozycję **Przypisania ról**. 
1. W polu **Nazwa**wprowadź adres e-mail, który wpisano w ostatniej procedurze. Użytkownik o adresie e-mail zobaczy rolę **współautor maszyny wirtualnej** .

## <a name="clean-up"></a>Czyszczenie

Aby usunąć grupę zasobów utworzoną w ostatniej procedurze, wybierz opcję **Wypróbuj** , aby otworzyć usługę Azure Cloud Shell, a następnie wklej następujący skrypt programu PowerShell do okna powłoki.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a same project name you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: udzielanie użytkownikom dostępu do zasobów platformy Azure przy użyciu Azure PowerShell](tutorial-role-assignments-user-powershell.md)