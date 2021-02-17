---
title: 'Samouczek: udzielanie grupie dostępu do zasobów platformy Azure przy użyciu Azure PowerShell — RBAC'
description: Dowiedz się, jak przyznać grupie dostęp do zasobów platformy Azure przy użyciu usług Azure PowerShell i kontroli dostępu opartej na rolach (RBAC) na platformie Azure w tym samouczku.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/02/2019
ms.author: rolyon
ms.openlocfilehash: e25db6213c99afaa46732831534956b01adba0b4
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100555220"
---
# <a name="tutorial-grant-a-group-access-to-azure-resources-using-azure-powershell"></a>Samouczek: udzielanie grupie dostępu do zasobów platformy Azure przy użyciu Azure PowerShell

[Kontrola dostępu oparta na rolach (Azure RBAC)](overview.md) umożliwia zarządzanie dostępem do zasobów platformy Azure. W tym samouczku przyznasz grupie dostęp z możliwością wyświetlania wszystkiego w ramach subskrypcji i zarządzania wszystkim w grupie zasobów przy użyciu programu Azure PowerShell.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Udzielanie dostępu grupie w innych zakresach
> * Tworzenie listy dostępu
> * Usuwanie dostępu

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

- Uprawnienia do tworzenia grup w usłudze Azure Active Directory (lub istniejąca grupa)
- [Azure Cloud Shell](../cloud-shell/quickstart-powershell.md)

## <a name="role-assignments"></a>Przypisania ról

W celu udzielenia dostępu w usłudze Azure RBAC należy utworzyć przypisanie roli. Przypisanie roli składa się z trzech elementów: podmiotu zabezpieczeń, definicji roli i zakresu. W tym samouczku utworzysz dwa przypisania ról:

| Podmiot zabezpieczeń | Definicja roli | Zakres |
| --- | --- | --- |
| Group (Grupa)<br>(Grupa samouczka kontroli dostępu opartej na rolach) | [Czytelnik](built-in-roles.md#reader) | Subskrypcja |
| Group (Grupa)<br>(Grupa samouczka kontroli dostępu opartej na rolach)| [Współautor](built-in-roles.md#contributor) | Grupa zasobów<br>(rbac-tutorial-resource-group) |

   ![Przypisania ról dla grupy](./media/tutorial-role-assignments-group-powershell/rbac-role-assignments.png)

## <a name="create-a-group"></a>Tworzenie grupy

Do przypisania roli potrzebny jest użytkownik, grupa lub jednostka usługi. Jeśli nie masz jeszcze grupy, możesz ją utworzyć.

- W usłudze Azure Cloud Shell utwórz nową grupę za pomocą polecenia [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup).

   ```azurepowershell
   New-AzureADGroup -DisplayName "RBAC Tutorial Group" `
     -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
   ```

   ```Example
   ObjectId                             DisplayName         Description
   --------                             -----------         -----------
   11111111-1111-1111-1111-111111111111 RBAC Tutorial Group
   ```

Jeśli nie masz uprawnień do tworzenia grup, możesz skorzystać z [samouczka: Udziel użytkownikowi dostępu do zasobów platformy Azure przy użyciu Azure PowerShell](tutorial-role-assignments-user-powershell.md) zamiast tego.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Za pomocą grupy zasobów można zademonstrować przypisywanie roli w zakresie grupy zasobów.

1. Pobierz listę lokalizacji regionów za pomocą polecenia [Get-AzLocation](/powershell/module/az.resources/get-azlocation).

   ```azurepowershell
   Get-AzLocation | select Location
   ```

1. Wybierz lokalizację w pobliżu i przypisz ją do zmiennej.

   ```azurepowershell
   $location = "westus"
   ```

1. Utwórz nową grupę zasobów za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

   ```azurepowershell
   New-AzResourceGroup -Name "rbac-tutorial-resource-group" -Location $location
   ```

   ```Example
   ResourceGroupName : rbac-tutorial-resource-group
   Location          : westus
   ProvisioningState : Succeeded
   Tags              :
   ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
   ```

## <a name="grant-access"></a>Udzielanie dostępu

Aby udzielić dostępu grupie, przypisz rolę za pomocą polecenia [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). Należy określić podmiot zabezpieczeń, definicję roli i zakres.

1. Pobierz identyfikator obiektu grupy za pomocą polecenia [Get-AzureADGroup](/powershell/module/azuread/new-azureadgroup).

    ```azurepowershell
    Get-AzureADGroup -SearchString "RBAC Tutorial Group"
    ```

    ```Example
    ObjectId                             DisplayName         Description
    --------                             -----------         -----------
    11111111-1111-1111-1111-111111111111 RBAC Tutorial Group
    ```

1. Zapisz identyfikator obiektu grupy w zmiennej.

    ```azurepowershell
    $groupId = "11111111-1111-1111-1111-111111111111"
    ```

1. Pobierz identyfikator subskrypcji za pomocą polecenia [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

    ```azurepowershell
    Get-AzSubscription
    ```

    ```Example
    Name     : Pay-As-You-Go
    Id       : 00000000-0000-0000-0000-000000000000
    TenantId : 22222222-2222-2222-2222-222222222222
    State    : Enabled
    ```

1. Zapisz zakres subskrypcji w zmiennej.

    ```azurepowershell
    $subScope = "/subscriptions/00000000-0000-0000-0000-000000000000"
    ```

1. Przypisz grupie rolę [Czytelnik](built-in-roles.md#reader) w zakresie subskrypcji.

    ```azurepowershell
    New-AzRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/44444444-4444-4444-4444-444444444444
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

1. Przypisz grupie rolę [Współautor](built-in-roles.md#contributor) w zakresie grupy zasobów.

    ```azurepowershell
    New-AzRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

## <a name="list-access"></a>Tworzenie listy dostępu

1. Aby zweryfikować dostęp do subskrypcji, wyświetl listę przypisań ról za pomocą polecenia [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

    ```azurepowershell
    Get-AzRoleAssignment -ObjectId $groupId -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

    W danych wyjściowych widać, że do grupy samouczka kontroli dostępu opartej na rolach przypisano rolę Czytelnik w zakresie subskrypcji.

1. Aby zweryfikować dostęp do grupy zasobów, wyświetl listę przypisań ról za pomocą polecenia [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

    ```azurepowershell
    Get-AzRoleAssignment -ObjectId $groupId -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

    W danych wyjściowych widać, że do grupy samouczka kontroli dostępu opartej na rolach przypisano zarówno rolę Współautor, jak i rolę Czytelnik. Rola Współautor jest przypisana w zakresie rbac-tutorial-resource-group, a rola Czytelnik jest dziedziczona w zakresie subskrypcji.

## <a name="optional-list-access-using-the-azure-portal"></a>Opcjonalnie: tworzenie listy dostępu przy użyciu witryny Azure Portal

1. Aby sprawdzić, jak wyglądają przypisania ról w witrynie Azure Portal, należy wyświetlić blok **Kontrola dostępu (IAM)** dla subskrypcji.

    ![Przypisania ról dla grupy w zakresie subskrypcji](./media/tutorial-role-assignments-group-powershell/role-assignments-subscription.png)

1. Wyświetl blok **Kontrola dostępu (IAM)** dla grupy zasobów.

    ![Przypisania ról dla grupy w zakresie grupy zasobów](./media/tutorial-role-assignments-group-powershell/role-assignments-resource-group.png)

## <a name="remove-access"></a>Usuwanie dostępu

Aby usunąć dostęp dla użytkowników, grup i aplikacji, usuń przypisanie roli za pomocą polecenia [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment).

1. Za pomocą następującego polecenia usuń przypisanie roli Współautor dla grupy w zakresie grupy zasobów.

    ```azurepowershell
    Remove-AzRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

1. Za pomocą następującego polecenia usuń przypisanie roli Czytelnik dla grupy w zakresie subskrypcji.

    ```azurepowershell
    Remove-AzRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby wyczyścić zasoby utworzone w tym samouczku, usuń grupę zasobów i grupę.

1. Usuń grupę zasobów za pomocą polecenia [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

    ```azurepowershell
    Remove-AzResourceGroup -Name "rbac-tutorial-resource-group"
    ```

    ```Example
    Confirm
    Are you sure you want to remove resource group 'rbac-tutorial-resource-group'
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```
    
1. Gdy zostanie wyświetlony monit o potwierdzenie, wpisz **Y**. Usunięcie może potrwać kilka sekund.

1. Usuń grupę za pomocą polecenia [Remove-AzureADGroup](/powershell/module/azuread/remove-azureadgroup).

    ```azurepowershell
    Remove-AzureADGroup -ObjectId $groupId
    ```
    
    Jeśli podczas próby usunięcia grupy wystąpi błąd, możesz także usunąć grupę w portalu.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przypisywanie ról platformy Azure przy użyciu Azure PowerShell](role-assignments-powershell.md)