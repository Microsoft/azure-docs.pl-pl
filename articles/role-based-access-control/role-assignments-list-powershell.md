---
title: Wyświetlanie listy przypisań ról platformy Azure przy użyciu Azure PowerShell — RBAC na platformie Azure
description: Dowiedz się, jak określić zasoby, których użytkownicy, grupy, nazwy główne usług lub tożsamości zarządzane mają dostęp do korzystania z usług Azure PowerShell i kontroli dostępu opartej na rolach (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/28/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 980e9f1ffd453a070dde72f8c7ca34c7ef08f948
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964358"
---
# <a name="list-azure-role-assignments-using-azure-powershell"></a>Wyświetlanie listy przypisań ról platformy Azure przy użyciu Azure PowerShell

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control/definition-list.md)] W tym artykule opisano sposób wyświetlania listy przypisań ról przy użyciu Azure PowerShell.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

> [!NOTE]
> Jeśli organizacja ma funkcje zarządzania, które są używane przez usługę zarządzania [zasobami delegowanymi przez platformę Azure](../lighthouse/concepts/azure-delegated-resource-management.md), w tym miejscu nie będą wyświetlane przypisania ról autoryzowane przez tego dostawcę usług.

## <a name="prerequisites"></a>Wymagania wstępne

- Program [PowerShell w Azure Cloud Shell](../cloud-shell/overview.md) lub [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-role-assignments-for-the-current-subscription"></a>Wyświetl listę przypisań ról dla bieżącej subskrypcji

Najprostszym sposobem uzyskania listy wszystkich przypisań ról w bieżącej subskrypcji (łącznie z dziedziczonymi przypisaniami ról z grup głównych i zarządzania) jest użycie metody [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) bez żadnych parametrów.

```azurepowershell
Get-AzRoleAssignment
```

```Example
PS C:\> Get-AzRoleAssignment

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : Alain
SignInName         : alain@example.com
RoleDefinitionName : Storage Blob Data Reader
RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Marketing
SignInName         :
RoleDefinitionName : Contributor
RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
ObjectId           : 22222222-2222-2222-2222-222222222222
ObjectType         : Group
CanDelegate        : False

...
```

## <a name="list-role-assignments-for-a-subscription"></a>Tworzenie listy przypisań ról dla subskrypcji

Aby wyświetlić listę wszystkich przypisań ról w zakresie subskrypcji, użyj polecenie [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment). Aby uzyskać identyfikator subskrypcji, możesz go znaleźć w bloku **subskrypcje** w Azure Portal lub użyć polecenie [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

```azurepowershell
Get-AzRoleAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /subscriptions/00000000-0000-0000-0000-000000000000
```

## <a name="list-role-assignments-for-a-user"></a>Tworzenie listy przypisań ról dla użytkownika

Aby wyświetlić listę wszystkich ról, które są przypisane do określonego użytkownika, użyj polecenie [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname>
```

```Example
PS C:\> Get-AzRoleAssignment -SignInName isabella@example.com | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

Aby wyświetlić listę wszystkich ról, które są przypisane do określonego użytkownika i role, które są przypisane do grup, do których należy użytkownik, użyj polecenie [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname> -ExpandPrincipalGroups
```

```Example
Get-AzRoleAssignment -SignInName isabella@example.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

## <a name="list-role-assignments-for-a-resource-group"></a>Wyświetlanie listy przypisań ról dla grupy zasobów

Aby wyświetlić listę wszystkich przypisań ról w zakresie grupy zasobów, użyj polecenie [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzRoleAssignment -ResourceGroupName pharma-sales | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Alain Charon
RoleDefinitionName : Backup Operator
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Alain Charon
RoleDefinitionName : Virtual Machine Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

## <a name="list-role-assignments-for-a-management-group"></a>Wyświetlanie listy przypisań ról dla grupy zarządzania

Aby wyświetlić listę wszystkich przypisań ról w zakresie grupy zarządzania, użyj polecenie [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment). Aby uzyskać identyfikator grupy zarządzania, można go znaleźć w bloku **grupy zarządzania** w Azure Portal lub użyć polecenie [Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup).

```azurepowershell
Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="list-role-assignments-for-a-resource"></a>Wyświetlanie listy przypisań ról dla zasobu

Aby wyświetlić listę przypisań ról dla określonego zasobu, użyj polecenie [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) i `-Scope` parametru. Zakres będzie różny w zależności od zasobu. Aby uzyskać zakres, można uruchomić `Get-AzRoleAssignment` bez żadnych parametrów, aby wyświetlić listę wszystkich przypisań ról, a następnie znaleźć zakres, który chcesz wyświetlić.

```azurepowershell
Get-AzRoleAssignment -Scope "/subscriptions/<subscription_id>/resourcegroups/<resource_group_name>/providers/<provider_name>/<resource_type>/<resource>
```

Poniższy przykład pokazuje, jak wyświetlić listę przypisań ról dla konta magazynu. Należy zauważyć, że to polecenie wyświetla również przypisania ról w wyższych zakresach, takich jak grupy zasobów i subskrypcje, które mają zastosowanie do tego konta magazynu.

```Example
PS C:\> Get-AzRoleAssignment -Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/storage-test-rg/providers/Microsoft.Storage/storageAccounts/storagetest0122"
```

Jeśli chcesz tylko wyświetlić przypisania ról, które są przypisane bezpośrednio do zasobu, możesz użyć polecenia [WHERE-Object](/powershell/module/microsoft.powershell.core/where-object) , aby odfiltrować listę.

```Example
PS C:\> Get-AzRoleAssignment | Where-Object {$_.Scope -eq "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/storage-test-rg/providers/Microsoft.Storage/storageAccounts/storagetest0122"}
```

## <a name="list-role-assignments-for-classic-service-administrator-and-co-administrators"></a>Wyświetl listę przypisań ról dla administratora klasycznej usługi i współadministratorów

Aby wyświetlić listę przypisań ról dla klasycznego administratora subskrypcji i współadministratorów, użyj polecenie [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -IncludeClassicAdministrators
```

## <a name="list-role-assignments-for-a-managed-identity"></a>Wyświetlanie listy przypisań ról dla tożsamości zarządzanej

1. Pobierz identyfikator obiektu przypisanej do systemu lub tożsamości zarządzanej przypisanej przez użytkownika. 

    Aby uzyskać identyfikator obiektu tożsamości zarządzanej przypisanej przez użytkownika, można użyć polecenie [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal).

    ```azurepowershell
    Get-AzADServicePrincipal -DisplayNameBeginsWith "<name> or <vmname>"
    ```

1. Aby wyświetlić listę przypisań ról, użyj polecenie [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

    ```azurepowershell
    Get-AzRoleAssignment -ObjectId <objectid>
    ```

## <a name="next-steps"></a>Następne kroki

- [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu programu Azure PowerShell](role-assignments-powershell.md)