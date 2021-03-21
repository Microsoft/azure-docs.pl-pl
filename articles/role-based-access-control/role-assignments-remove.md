---
title: Usuwanie przypisań ról platformy Azure — Azure RBAC
description: Dowiedz się, jak usunąć dostęp do zasobów platformy Azure dla użytkowników, grup, nazw głównych usług lub tożsamości zarządzanych przy użyciu Azure Portal, Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub API REST.
services: active-directory
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 02/15/2021
ms.author: rolyon
ms.openlocfilehash: 7a3e4853d6dffa7eb2c5cf80846f6f1bd6beba03
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100561508"
---
# <a name="remove-azure-role-assignments"></a>Usuwanie przypisań ról platformy Azure

[Kontrola dostępu oparta na rolach (Azure RBAC)](../../articles/role-based-access-control/overview.md) to system autoryzacji używany do zarządzania dostępem do zasobów platformy Azure. Aby usunąć dostęp z zasobu platformy Azure, należy usunąć przypisanie roli. W tym artykule opisano sposób usuwania przypisań ról przy użyciu interfejsu API Azure Portal, Azure PowerShell, wiersza polecenia platformy Azure i protokołu REST.

## <a name="prerequisites"></a>Wymagania wstępne

Aby usunąć przypisania ról, musisz mieć:

- `Microsoft.Authorization/roleAssignments/delete` uprawnienia, takie jak [administrator dostępu użytkowników](../../articles/role-based-access-control/built-in-roles.md#user-access-administrator) lub [właściciel](../../articles/role-based-access-control/built-in-roles.md#owner)

## <a name="azure-portal"></a>Azure Portal

1. Otwórz **kontrolę dostępu (IAM)** w zakresie, na przykład grupy zarządzania, subskrypcji, grupy zasobów lub zasobu, w której chcesz usunąć dostęp.

1. Kliknij kartę **przypisania roli** , aby wyświetlić wszystkie przypisania ról w tym zakresie.

1. Na liście przypisań ról dodaj znacznik wyboru obok podmiotu zabezpieczeń z przypisaniem roli, które chcesz usunąć.

   ![Przypisanie roli wybrane do usunięcia](./media/role-assignments-remove/rg-role-assignments-select.png)

1. Kliknij przycisk **Usuń**.

   ![Komunikat dotyczący usuwania przypisania roli](./media/role-assignments-remove/remove-role-assignment.png)

1. W wyświetlonym komunikacie dotyczącym usuwania przypisania roli wybierz pozycję **Tak**.

    Jeśli zobaczysz komunikat, że nie można usunąć przypisań ról dziedziczonych, próbujesz usunąć przypisanie roli w zakresie podrzędnym. Należy otworzyć kontrolę dostępu (IAM) w zakresie, w którym rola została przypisana, i ponowić próbę. Szybkim sposobem otwarcia kontroli dostępu (IAM) w prawidłowym zakresie jest Przyjrzyj się kolumnie **zakres** i kliknij łącze obok **(Odziedziczone)**.

   ![Usuń komunikat przypisania roli dla dziedziczonych przypisań ról](./media/role-assignments-remove/remove-role-assignment-inherited.png)

## <a name="azure-powershell"></a>Azure PowerShell

W Azure PowerShell można usunąć przypisanie roli za pomocą polecenia [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment).

Poniższy przykład usuwa przypisanie roli [współautor maszyny wirtualnej](built-in-roles.md#virtual-machine-contributor) z użytkownika *patlong \@ contoso.com* w grupie zasobów *Pharma-Sales* :

```azurepowershell
PS C:\> Remove-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales
```

Usuwa rolę [czytnika](built-in-roles.md#reader) z grupy *zespołu Ann Mack* o identyfikatorze 22222222-2222-2222-2222-222222222222 w zakresie subskrypcji.

```azurepowershell
PS C:\> Remove-AzRoleAssignment -ObjectId 22222222-2222-2222-2222-222222222222 `
-RoleDefinitionName "Reader" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000"
```

Usuwa rolę [czytnika rozliczeń](built-in-roles.md#billing-reader) z użytkownika *Alain \@ example.com* w zakresie grupy zarządzania.

```azurepowershell
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com `
-RoleDefinitionName "Billing Reader" `
-Scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

Jeśli zostanie wyświetlony komunikat o błędzie: "podane informacje nie są mapowane do przypisania roli", upewnij się, że określono również `-Scope` `-ResourceGroupName` parametry lub. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z usługą Azure RBAC](troubleshooting.md#role-assignments-with-identity-not-found).

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

W interfejsie wiersza polecenia platformy Azure można usunąć przypisanie roli za pomocą polecenia [AZ role przypisanie Delete](/cli/azure/role/assignment#az_role_assignment_delete).

Poniższy przykład usuwa przypisanie roli [współautor maszyny wirtualnej](built-in-roles.md#virtual-machine-contributor) z użytkownika *patlong \@ contoso.com* w grupie zasobów *Pharma-Sales* :

```azurecli
az role assignment delete --assignee "patlong@contoso.com" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

Usuwa rolę [czytnika](built-in-roles.md#reader) z grupy *zespołu Ann Mack* o identyfikatorze 22222222-2222-2222-2222-222222222222 w zakresie subskrypcji.

```azurecli
az role assignment delete --assignee "22222222-2222-2222-2222-222222222222" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

Usuwa rolę [czytnika rozliczeń](built-in-roles.md#billing-reader) z użytkownika *Alain \@ example.com* w zakresie grupy zarządzania.

```azurecli
az role assignment delete --assignee "alain@example.com" \
--role "Billing Reader" \
--scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

## <a name="rest-api"></a>Interfejs API REST

W interfejsie API REST można usunąć przypisanie roli, używając [przypisań ról-Delete](/rest/api/authorization/roleassignments/delete).

1. Pobierz identyfikator przypisywania ról (GUID). Ten identyfikator jest zwracany podczas pierwszego tworzenia przypisania roli lub można go uzyskać, wyświetlając listę przypisań ról.

1. Rozpocznij od następującego żądania:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2015-07-01
    ```

1. W identyfikatorze URI Zastąp wartość *{SCOPE}* zakresem, aby usunąć przypisanie roli.

    > [!div class="mx-tableFixed"]
    > | Zakres | Typ |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupa zarządzania |
    > | `subscriptions/{subscriptionId1}` | Subskrypcja |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Zasób |

1. Zastąp ciąg *{roleAssignmentId}* identyfikatorem GUID przypisania roli.

Poniższe żądanie usuwa określone przypisanie roli w zakresie subskrypcji:

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId1}/providers/microsoft.authorization/roleassignments/{roleAssignmentId1}?api-version=2015-07-01
```

Poniżej przedstawiono przykład danych wyjściowych:

```json
{
    "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
        "principalId": "{objectId1}",
        "scope": "/subscriptions/{subscriptionId1}",
        "createdOn": "2020-05-06T23:55:24.5379478Z",
        "updatedOn": "2020-05-06T23:55:24.5379478Z",
        "createdBy": "{createdByObjectId1}",
        "updatedBy": "{updatedByObjectId1}"
    },
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId1}",
    "type": "Microsoft.Authorization/roleAssignments",
    "name": "{roleAssignmentId1}"
}
```

## <a name="arm-template"></a>Szablon ARM

Nie można usunąć przypisania roli przy użyciu szablonu Azure Resource Manager (szablon ARM). Aby usunąć przypisanie roli, należy użyć innych narzędzi, takich jak Azure Portal, Azure PowerShell, interfejs wiersza polecenia platformy Azure lub interfejsu API REST.

## <a name="next-steps"></a>Następne kroki

- [Wyświetlanie listy przypisań ról platformy Azure przy użyciu Azure Portal](role-assignments-list-portal.md)
- [Wyświetlanie listy przypisań ról platformy Azure przy użyciu Azure PowerShell](role-assignments-list-powershell.md)
- [Rozwiązywanie problemów z usługą Azure RBAC](troubleshooting.md)
