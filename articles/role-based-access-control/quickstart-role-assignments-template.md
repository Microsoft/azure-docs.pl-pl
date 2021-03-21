---
title: 'Szybki Start: Przypisywanie roli platformy Azure przy użyciu szablonu Azure Resource Manager — RBAC na platformie Azure'
description: Dowiedz się, jak udzielić dostępu do zasobów platformy Azure dla użytkownika w zakresie grupy zasobów przy użyciu szablonów Azure Resource Manager i kontroli dostępu opartej na rolach (Azure RBAC).
services: role-based-access-control,azure-resource-manager
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: quickstart
ms.custom: subject-armqs
ms.workload: identity
ms.date: 02/15/2021
ms.author: rolyon
ms.openlocfilehash: 6e8f194cd85a3c381bdabf206777a99dce3c29b6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100559275"
---
# <a name="quickstart-assign-an-azure-role-using-an-arm-template"></a>Szybki Start: Przypisywanie roli platformy Azure przy użyciu szablonu ARM

[Kontrola dostępu oparta na rolach (Azure RBAC)](overview.md) umożliwia zarządzanie dostępem do zasobów platformy Azure. W tym przewodniku szybki start utworzysz grupę zasobów i udzielą użytkownikowi dostępu do tworzenia maszyn wirtualnych w grupie zasobów i zarządzania nimi. Ten przewodnik Szybki Start używa szablonu Azure Resource Manager (szablon ARM) do udzielenia dostępu.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-rbac-builtinrole-resourcegroup%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Aby przypisać role platformy Azure i usunąć przypisania ról, musisz dysponować:

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- `Microsoft.Authorization/roleAssignments/write` i `Microsoft.Authorization/roleAssignments/delete` uprawnienia, takie jak [administrator dostępu użytkowników](built-in-roles.md#user-access-administrator) lub [właściciel](built-in-roles.md#owner)
- Aby przypisać rolę, należy określić trzy elementy: podmiot zabezpieczeń, definicja roli i zakres. W tym przewodniku szybki start podmiot zabezpieczeń jest używany przez Ciebie lub innego użytkownika w katalogu, definicja roli to [współautor maszyny wirtualnej](built-in-roles.md#virtual-machine-contributor), a zakres to grupa zasobów, którą określisz.

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-rbac-builtinrole-resourcegroup/). Szablon zawiera trzy parametry i sekcję Resources. W sekcji Resources należy zauważyć, że ma ona trzy elementy przypisania roli: podmiot zabezpieczeń, definicja roli i zakres.

:::code language="json" source="~/quickstart-templates/101-rbac-builtinrole-resourcegroup/azuredeploy.json":::

Zasób zdefiniowany w szablonie to:

- [Microsoft. Authorization/roleAssignments](/azure/templates/Microsoft.Authorization/roleAssignments)

## <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Określ adres e-mail skojarzony z subskrypcją platformy Azure. Lub określ adres e-mail innego użytkownika w katalogu.

1. Otwórz Azure Cloud Shell dla programu PowerShell.

1. Skopiuj i wklej następujący skrypt do Cloud Shell.

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter a resource group name (i.e. ExampleGrouprg)"
    $emailAddress = Read-Host -Prompt "Enter an email address for a user in your directory"
    $location = Read-Host -Prompt "Enter a location (i.e. centralus)"
    
    $roleAssignmentName = New-Guid
    $principalId = (Get-AzAdUser -Mail $emailAddress).id
    $roleDefinitionId = (Get-AzRoleDefinition -name "Virtual Machine Contributor").id
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-rbac-builtinrole-resourcegroup/azuredeploy.json"
    
    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -roleAssignmentName $roleAssignmentName -roleDefinitionID $roleDefinitionId -principalId $principalId
    ```

1. Wprowadź nazwę grupy zasobów, na przykład ExampleGrouprg.

1. Wprowadź adres e-mail dla siebie lub innego użytkownika w katalogu.

1. Wprowadź lokalizację dla grupy zasobów, na przykład centralną.

1. W razie potrzeby naciśnij klawisz ENTER, aby uruchomić polecenie New-AzResourceGroupDeployment.

    Polecenie [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) tworzy nową grupę zasobów, a polecenie [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) wdraża szablon w celu dodania przypisania roli.

    Wyświetlone dane wyjściowe powinny przypominać następujące dane:

    ```azurepowershell
    PS> New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -roleAssignmentName $roleAssignmentName -roleDefinitionID $roleDefinitionId -principalId $principalId
    
    DeploymentName          : azuredeploy
    ResourceGroupName       : ExampleGrouprg
    ProvisioningState       : Succeeded
    Timestamp               : 5/22/2020 9:01:30 PM
    Mode                    : Incremental
    TemplateLink            :
                              Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-rbac-builtinrole-resourcegroup/azuredeploy.json
                              ContentVersion : 1.0.0.0
    
    Parameters              :
                              Name                  Type                       Value
                              ====================  =========================  ==========
                              roleAssignmentName    String                     {roleAssignmentName}
                              roleDefinitionID      String                     9980e02c-c2be-4d73-94e8-173b1dc7cf3c
                              principalId           String                     {principalId}
    
    Outputs                 :
    DeploymentDebugLogLevel :
    ```

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

1. W Azure Portal Otwórz utworzoną grupę zasobów.

1. W menu po lewej stronie kliknij pozycję **Kontrola dostępu (IAM)**.

1. Kliknij kartę **Przypisania ról**.

1. Sprawdź, czy rola **współautor maszyny wirtualnej** jest przypisana do określonego użytkownika.

   ![Przypisanie nowej roli](./media/quickstart-role-assignments-template/role-assignment-portal.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby usunąć przypisanie roli i utworzoną grupę zasobów, wykonaj następujące kroki.

1. Skopiuj i wklej następujący skrypt do Cloud Shell.

    ```azurepowershell
    $emailAddress = Read-Host -Prompt "Enter the email address of the user with the role assignment to remove"
    $resourceGroupName = Read-Host -Prompt "Enter the resource group name to remove (i.e. ExampleGrouprg)"
    
    $principalId = (Get-AzAdUser -Mail $emailAddress).id
    
    Remove-AzRoleAssignment -ObjectId $principalId -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName $resourceGroupName
    Remove-AzResourceGroup -Name $resourceGroupName
    ```
    
1. Wprowadź adres e-mail użytkownika z przypisaniem roli do usunięcia.

1. Wprowadź nazwę grupy zasobów, która ma zostać usunięta, np. ExampleGrouprg.

1. W razie potrzeby naciśnij klawisz ENTER, aby uruchomić polecenie Remove-AzResourceGroup.

1. Wprowadź wartość **Y** , aby potwierdzić, że chcesz usunąć grupę zasobów.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: udzielanie użytkownikom dostępu do zasobów platformy Azure przy użyciu Azure PowerShell](tutorial-role-assignments-user-powershell.md)