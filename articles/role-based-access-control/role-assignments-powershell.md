---
title: Dodawanie i usuwanie przypisań ról platformy Azure przy użyciu Azure PowerShell — RBAC na platformie Azure
description: Dowiedz się, jak udzielić dostępu do zasobów platformy Azure dla użytkowników, grup, nazw głównych usług lub tożsamości zarządzanych przy użyciu Azure PowerShell i kontroli dostępu opartej na rolach (RBAC) platformy Azure.
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
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 9b0df4337a5e5faff3427222fb66caf8e02184a3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86146655"
---
# <a name="add-or-remove-azure-role-assignments-using-azure-powershell"></a>Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu programu Azure PowerShell

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] W tym artykule opisano sposób przypisywania ról przy użyciu Azure PowerShell.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby dodać lub usunąć przypisania ról, musisz mieć:

- `Microsoft.Authorization/roleAssignments/write` i `Microsoft.Authorization/roleAssignments/delete` uprawnienia, takie jak [administrator dostępu użytkowników](built-in-roles.md#user-access-administrator) lub [właściciel](built-in-roles.md#owner)
- Program [PowerShell w Azure Cloud Shell](/azure/cloud-shell/overview) lub [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="get-object-ids"></a>Pobierz identyfikatory obiektów

Aby dodać lub usunąć przypisania ról, może być konieczne określenie unikatowego identyfikatora obiektu. Identyfikator ma format: `11111111-1111-1111-1111-111111111111` . Identyfikator można uzyskać przy użyciu Azure Portal lub Azure PowerShell.

### <a name="user"></a>Użytkownik

Aby uzyskać identyfikator obiektu dla użytkownika usługi Azure AD, możesz użyć polecenie [Get-AzADUser](/powershell/module/az.resources/get-azaduser).

```azurepowershell
Get-AzADUser -StartsWith <string_in_quotes>
(Get-AzADUser -DisplayName <name_in_quotes>).id
```

### <a name="group"></a>Group (Grupa)

Aby uzyskać identyfikator obiektu dla grupy usługi Azure AD, możesz użyć polecenie [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup).

```azurepowershell
Get-AzADGroup -SearchString <group_name_in_quotes>
(Get-AzADGroup -DisplayName <group_name_in_quotes>).id
```

### <a name="application"></a>Aplikacja

Aby uzyskać identyfikator obiektu dla jednostki usługi Azure AD (tożsamość używana przez aplikację), możesz użyć polecenie [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal). Dla jednostki usługi Użyj identyfikatora obiektu, a **nie** identyfikatora aplikacji.

```azurepowershell
Get-AzADServicePrincipal -SearchString <service_name_in_quotes>
(Get-AzADServicePrincipal -DisplayName <service_name_in_quotes>).id
```

## <a name="add-a-role-assignment"></a>Dodaj przypisanie roli

W celu udzielenia dostępu w usłudze Azure RBAC należy dodać przypisanie roli.

### <a name="user-at-a-resource-group-scope"></a>Użytkownik w zakresie grupy zasobów

Aby dodać przypisanie roli dla użytkownika w zakresie grupy zasobów, użyj polecenie [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment).

```azurepowershell
New-AzRoleAssignment -SignInName <email_or_userprincipalname> -RoleDefinitionName <role_name> -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> New-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales


RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/pr
                     oviders/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="using-the-unique-role-id"></a>Używanie unikatowego identyfikatora roli

Istnieje kilka razy więcej czasu, gdy nazwa roli może ulec zmianie, na przykład:

- Używasz własnej roli niestandardowej i zdecydujesz się zmienić nazwę.
- W nazwie jest używana rola w wersji zapoznawczej mająca **(wersja zapoznawcza)** . Po wydaniu roli zostanie zmieniona nazwa roli.

> [!IMPORTANT]
> Wersja zapoznawcza jest świadczona bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Nawet w przypadku zmiany nazwy roli identyfikator roli nie zmienia się. Jeśli używasz skryptów lub automatyzacji do tworzenia przypisań ról, najlepszym rozwiązaniem jest użycie unikatowego identyfikatora roli zamiast nazwy roli. W związku z tym, jeśli zostanie zmieniona nazwa roli, skrypty mogą pracować.

Aby dodać przypisanie roli przy użyciu unikatowego identyfikatora roli zamiast nazwy roli, użyj polecenia [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment).

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionId <role_id> -Scope <resource_group_name/resource/management groups>
```

Poniższy przykład przypisuje rolę [współautor maszyny wirtualnej](built-in-roles.md#virtual-machine-contributor) do *Alain \@ example.com* użytkownika w zakresie grupy zasobów *Pharma-Sales* . Aby uzyskać unikatowy identyfikator roli, można użyć polecenie [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) lub zobaczyć [wbudowane role platformy Azure](built-in-roles.md).

```Example
PS C:\> New-AzRoleAssignment -ObjectId 44444444-4444-4444-4444-444444444444 -RoleDefinitionId 9980e02c-c2be-4d73-94e8-173b1dc7cf3c -Scope /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="group-at-a-resource-scope"></a>Grupuj w zakresie zasobów

Aby dodać przypisanie roli dla grupy w zakresie zasobów, użyj polecenie [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). Aby uzyskać informacje na temat pobierania identyfikatora obiektu grupy, zobacz [pobieranie identyfikatorów obiektów](#get-object-ids).

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -ResourceName <resource_name> -ResourceType <resource_type> -ParentResource <parent resource> -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzADGroup -SearchString "Pharma"

SecurityEnabled DisplayName         Id                                   Type
--------------- -----------         --                                   ----
           True Pharma Sales Admins aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa Group

PS C:\> New-AzRoleAssignment -ObjectId aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa -RoleDefinitionName "Virtual Machine Contributor" -ResourceName RobertVirtualNetwork -ResourceType Microsoft.Network/virtualNetworks -ResourceGroupName RobertVirtualNetworkResourceGroup

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork/providers/Microsoft.Authorizat
                     ion/roleAssignments/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork
DisplayName        : Pharma Sales Admins
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa
ObjectType         : Group
CanDelegate        : False
```

### <a name="application-at-a-subscription-scope"></a>Aplikacja w zakresie subskrypcji

Aby dodać przypisanie roli dla aplikacji w zakresie subskrypcji, użyj polecenie [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). Aby uzyskać informacje na temat pobierania identyfikatora obiektu aplikacji, zobacz [pobieranie identyfikatorów obiektów](#get-object-ids).

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> New-AzRoleAssignment -ObjectId 77777777-7777-7777-7777-777777777777 -RoleDefinitionName "Reader" -Scope /subscriptions/00000000-0000-0000-0000-000000000000

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : MyApp1
SignInName         :
RoleDefinitionName : Reader
RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

### <a name="user-at-a-management-group-scope"></a>Użytkownik w zakresie grupy zarządzania

Aby dodać przypisanie roli dla użytkownika w zakresie grupy zarządzania, użyj polecenie [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). Aby uzyskać identyfikator grupy zarządzania, można go znaleźć w bloku **grupy zarządzania** w Azure Portal lub użyć polecenie [Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup).

```azurepowershell
New-AzRoleAssignment -SignInName <email_or_userprincipalname> -RoleDefinitionName <role_name> -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> New-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Billing Reader" -Scope /providers/Microsoft.Management/managementGroups/marketing-group

RoleAssignmentId   : /providers/Microsoft.Management/managementGroups/marketing-group/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /providers/Microsoft.Management/managementGroups/marketing-group
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Billing Reader
RoleDefinitionId   : fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

## <a name="remove-a-role-assignment"></a>Usuwanie przypisania roli

W celu usunięcia dostępu w usłudze Azure RBAC należy usunąć przypisanie roli za pomocą polecenia [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment).

Poniższy przykład usuwa przypisanie roli *współautor maszyny wirtualnej* z użytkownika *Alain \@ example.com* w grupie zasobów *Pharma-Sales* :

```Example
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales
```

Poniższy przykład usuwa rolę <role_name> z <object_id> w zakresie subskrypcji.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

Poniższy przykład usuwa rolę <role_name> z <object_id> w zakresie grupy zarządzania.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

Jeśli zostanie wyświetlony komunikat o błędzie: "podane informacje nie są mapowane do przypisania roli", upewnij się, że określono również `-Scope` `-ResourceGroupName` parametry lub. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z usługą Azure RBAC](troubleshooting.md#role-assignments-with-identity-not-found).

## <a name="next-steps"></a>Następne kroki

- [Wyświetlanie listy przypisań ról platformy Azure przy użyciu Azure PowerShell](role-assignments-list-powershell.md)
- [Samouczek: udzielanie grupie dostępu do zasobów platformy Azure przy użyciu Azure PowerShell](tutorial-role-assignments-group-powershell.md)
- [Samouczek: tworzenie roli niestandardowej platformy Azure przy użyciu Azure PowerShell](tutorial-custom-role-powershell.md)
- [Zarządzanie zasobami za pomocą Azure PowerShell](../azure-resource-manager/management/manage-resources-powershell.md)
