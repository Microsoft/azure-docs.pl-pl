---
title: Przypisywanie ról platformy Azure przy użyciu Azure PowerShell — kontrola RBAC na platformie Azure
description: Dowiedz się, jak udzielić dostępu do zasobów platformy Azure dla użytkowników, grup, nazw głównych usług lub tożsamości zarządzanych przy użyciu Azure PowerShell i kontroli dostępu opartej na rolach (RBAC) platformy Azure.
services: active-directory
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 02/15/2021
ms.author: rolyon
ms.openlocfilehash: 00f663b90f34f3b557329692f844bbbc1bf3207d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100556804"
---
# <a name="assign-azure-roles-using-azure-powershell"></a>Przypisywanie ról platformy Azure przy użyciu Azure PowerShell

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] W tym artykule opisano sposób przypisywania ról przy użyciu Azure PowerShell.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby przypisać role, musisz mieć:

- `Microsoft.Authorization/roleAssignments/write` uprawnienia, takie jak [administrator dostępu użytkowników](built-in-roles.md#user-access-administrator) lub [właściciel](built-in-roles.md#owner)
- Program [PowerShell w Azure Cloud Shell](../cloud-shell/overview.md) lub [Azure PowerShell](/powershell/azure/install-az-ps)
- Konto używane do uruchamiania polecenia programu PowerShell musi mieć `Directory.Read.All` uprawnienie Microsoft Graph.

## <a name="steps-to-assign-an-azure-role"></a>Procedura przypisywania roli platformy Azure

Aby przypisać rolę składa się z trzech elementów: podmiot zabezpieczeń, definicja roli i zakres.

### <a name="step-1-determine-who-needs-access"></a>Krok 1. Określanie, kto potrzebuje dostępu

Rolę można przypisać do użytkownika, grupy, nazwy głównej usługi lub tożsamości zarządzanej. Aby przypisać rolę, może być konieczne określenie unikatowego identyfikatora obiektu. Identyfikator ma format: `11111111-1111-1111-1111-111111111111` . Identyfikator można uzyskać przy użyciu Azure Portal lub Azure PowerShell.

**Użytkownik**

W przypadku użytkownika usługi Azure AD Pobierz główną nazwę użytkownika, taką jak *patlong \@ contoso.com* lub identyfikator obiektu użytkownika. Aby uzyskać identyfikator obiektu, można użyć polecenie [Get-AzADUser](/powershell/module/az.resources/get-azaduser).

```azurepowershell
Get-AzADUser -StartsWith <userName>
(Get-AzADUser -DisplayName <userName>).id
```

**Grupa**

W przypadku grupy usługi Azure AD wymagany jest identyfikator obiektu grupy. Aby uzyskać identyfikator obiektu, można użyć polecenie [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup).

```azurepowershell
Get-AzADGroup -SearchString <groupName>
(Get-AzADGroup -DisplayName <groupName>).id
```

**Jednostka usługi**

W przypadku jednostki usługi Azure AD (tożsamości używanej przez aplikację) wymagany jest identyfikator obiektu jednostki usługi. Aby uzyskać identyfikator obiektu, można użyć polecenie [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal). Dla jednostki usługi Użyj identyfikatora obiektu, a **nie** identyfikatora aplikacji.

```azurepowershell
Get-AzADServicePrincipal -SearchString <principalName>
(Get-AzADServicePrincipal -DisplayName <principalName>).id
```

**Tożsamość zarządzana**

Dla przypisanej do systemu lub tożsamości zarządzanej przypisanej przez użytkownika wymagany jest identyfikator obiektu. Aby uzyskać identyfikator obiektu, można użyć polecenie [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal).

```azurepowershell
Get-AzADServicePrincipal -SearchString <principalName>
(Get-AzADServicePrincipal -DisplayName <principalName>).id
```
    
### <a name="step-2-select-the-appropriate-role"></a>Krok 2. Wybierz odpowiednią rolę

Uprawnienia są pogrupowane w role. Możesz wybrać jedną z listy [wbudowanych ról platformy Azure](built-in-roles.md) lub użyć własnych ról niestandardowych. Najlepszym rozwiązaniem jest przyznanie dostępu z najniższymi potrzebami, dlatego nie należy przypisywać szerszej roli.

Aby wyświetlić listę ról i uzyskać unikatowy identyfikator roli, można użyć polecenie [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition | FT Name, IsCustom, Id
```

Poniżej przedstawiono sposób wyświetlania szczegółów konkretnej roli.

```azurepowershell
Get-AzRoleDefinition <roleName>
```

Aby uzyskać więcej informacji, zobacz [lista definicji ról platformy Azure](role-definitions-list.md#azure-powershell).
 
### <a name="step-3-identify-the-needed-scope"></a>Krok 3. identyfikowanie wymaganego zakresu

Platforma Azure udostępnia cztery poziomy zakresu: zasobu, [grupy zasobów](../azure-resource-manager/management/overview.md#resource-groups), subskrypcji i [grupy zarządzania](../governance/management-groups/overview.md). Najlepszym rozwiązaniem jest przyznanie dostępu z najniższymi potrzebami, dlatego nie należy przypisywać roli w szerszym zakresie. Aby uzyskać więcej informacji na temat zakresu, zobacz [Opis zakresu](scope-overview.md).

**Zakres zasobu**

Dla zakresu zasobów wymagany jest identyfikator zasobu dla zasobu. Identyfikator zasobu można znaleźć, przeglądając właściwości zasobu w Azure Portal. Identyfikator zasobu ma następujący format.

```
/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/<providerName>/<resourceType>/<resourceSubType>/<resourceName>
```

**Zakres grupy zasobów**

Dla zakresu grupy zasobów wymagana jest nazwa grupy zasobów. Nazwę można znaleźć na stronie **grupy zasobów** w Azure Portal lub można użyć [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup).

```azurepowershell
Get-AzResourceGroup
```

**Zakres subskrypcji** 

W przypadku zakresu subskrypcji wymagany jest Identyfikator subskrypcji. Identyfikator można znaleźć na stronie **subskrypcje** w Azure Portal lub użyć [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription).

```azurepowershell
Get-AzSubscription
```

**Zakres grupy zarządzania** 

W przypadku zakresu grupy zarządzania potrzebna jest nazwa grupy zarządzania. Nazwę można znaleźć na stronie **grupy zarządzania** w Azure Portal lub można użyć [Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup).

```azurepowershell
Get-AzManagementGroup
```
    
### <a name="step-4-assign-role"></a>Krok 4. Przypisywanie roli

Aby przypisać rolę, użyj polecenia [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) . W zależności od zakresu polecenie ma zwykle jeden z następujących formatów.

**Zakres zasobu**

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/<providerName>/<resourceType>/<resourceSubType>/<resourceName>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionId <roleId> `
-ResourceName <resourceName> `
-ResourceType <resourceType> `
-ResourceGroupName <resourceGroupName>
```

**Zakres grupy zasobów**

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-ResourceGroupName <resourceGroupName>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-ResourceGroupName <resourceGroupName>
```

**Zakres subskrypcji** 

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>
```

**Zakres grupy zarządzania** 

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-Scope /providers/Microsoft.Management/managementGroups/<groupName>
``` 

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /providers/Microsoft.Management/managementGroups/<groupName>
``` 
    
## <a name="assign-role-examples"></a>Przypisywanie przykładów roli

#### <a name="assign-a-role-for-all-blob-containers-in-a-storage-account-resource-scope"></a>Przypisywanie roli dla wszystkich kontenerów obiektów BLOB w zakresie zasobów konta magazynu

Przypisuje rolę [współautor danych obiektów blob magazynu](built-in-roles.md#storage-blob-data-contributor) do jednostki usługi z identyfikatorem obiektu *55555555-5555-5555-5555-555555555555* w zakresie zasobów dla konta magazynu o nazwie *storage12345*.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 55555555-5555-5555-5555-555555555555 `
-RoleDefinitionName "Storage Blob Data Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/providers/Microsoft.Authorization/roleAssignments/cccccccc-cccc-cccc-cccc-cccccccccccc
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345
DisplayName        : example-identity
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 55555555-5555-5555-5555-555555555555
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="assign-a-role-for-a-specific-blob-container-resource-scope"></a>Przypisywanie roli dla określonego zakresu zasobów kontenera obiektów BLOB

Przypisuje rolę [współautor danych obiektów blob magazynu](built-in-roles.md#storage-blob-data-contributor) do jednostki usługi z identyfikatorem obiektu *55555555-5555-5555-5555-555555555555* w zakresie zasobów dla kontenera obiektów BLOB o nazwie *BLOB-Container-01*.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 55555555-5555-5555-5555-555555555555 `
-RoleDefinitionName "Storage Blob Data Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01/providers/Microsoft.Authorization/roleAssignm
                     ents/dddddddd-dddd-dddd-dddd-dddddddddddd
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01
DisplayName        : example-identity
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 55555555-5555-5555-5555-555555555555
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="assign-a-role-for-a-group-in-a-specific-virtual-network-resource-scope"></a>Przypisywanie roli dla grupy w określonym zakresie zasobów sieci wirtualnej

Przypisuje rolę [współautor maszyny wirtualnej](built-in-roles.md#virtual-machine-contributor) do grupy *administratorzy Sales Pharma* z identyfikatorem Aaaaaaaa-AAAA-AAAA-AAAA-aaaaaaaaaaaa w zakresie zasobów dla sieci wirtualnej o nazwie *Pharma-Sales-Project-Network*.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceName pharma-sales-project-network `
-ResourceType Microsoft.Network/virtualNetworks `
-ResourceGroupName MyVirtualNetworkResourceGroup

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network/providers/Microsoft.Authorizat
                     ion/roleAssignments/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
DisplayName        : Pharma Sales Admins
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa
ObjectType         : Group
CanDelegate        : False
```

#### <a name="assign-a-role-for-a-user-at-a-resource-group-scope"></a>Przypisywanie roli dla użytkownika w zakresie grupy zasobów

Przypisuje rolę [współautor maszyny wirtualnej](built-in-roles.md#virtual-machine-contributor) do użytkownika *patlong \@ contoso.com* w zakresie grupy zasobów *Pharma-Sales* .

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/pr
                     oviders/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

Alternatywnie można określić w pełni kwalifikowaną grupę zasobów z `-Scope` parametrem:

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

#### <a name="assign-a-role-for-a-user-using-the-unique-role-id-at-a-resource-group-scope"></a>Przypisywanie roli dla użytkownika przy użyciu unikatowego identyfikatora roli w zakresie grupy zasobów

Istnieje kilka razy więcej czasu, gdy nazwa roli może ulec zmianie, na przykład:

- Używasz własnej roli niestandardowej i zdecydujesz się zmienić nazwę.
- W nazwie jest używana rola w wersji zapoznawczej mająca **(wersja zapoznawcza)** . Po wydaniu roli zostanie zmieniona nazwa roli.

Nawet w przypadku zmiany nazwy roli identyfikator roli nie zmienia się. Jeśli używasz skryptów lub automatyzacji do tworzenia przypisań ról, najlepszym rozwiązaniem jest użycie unikatowego identyfikatora roli zamiast nazwy roli. W związku z tym, jeśli zostanie zmieniona nazwa roli, skrypty mogą pracować.

Poniższy przykład przypisuje rolę [współautor maszyny wirtualnej](built-in-roles.md#virtual-machine-contributor) do użytkownika *patlong \@ contoso.com* w zakresie grupy zasobów *Pharma-Sales* .

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 44444444-4444-4444-4444-444444444444 `
-RoleDefinitionId 9980e02c-c2be-4d73-94e8-173b1dc7cf3c `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

#### <a name="assign-a-role-for-an-application-at-a-resource-group-scope"></a>Przypisywanie roli dla aplikacji w zakresie grupy zasobów

Przypisuje rolę [współautor maszyny wirtualnej](built-in-roles.md#virtual-machine-contributor) do aplikacji z identyfikatorem obiektu jednostki usługi 77777777-7777-7777-7777-777777777777 w zakresie grupy zasobów *Pharma-Sales* .

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 77777777-7777-7777-7777-777777777777 `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : MyApp1
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="assign-a-role-for-a-user-at-a-subscription-scope"></a>Przypisywanie roli dla użytkownika w zakresie subskrypcji

Przypisuje rolę [czytnika](built-in-roles.md#reader) do użytkownika *annm \@ example.com* w zakresie subskrypcji.

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName annm@example.com `
-RoleDefinitionName "Reader" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : Ann M
SignInName         : annm@example.com
RoleDefinitionName : Reader
RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="assign-a-role-for-a-user-at-a-management-group-scope"></a>Przypisywanie roli dla użytkownika w zakresie grupy zarządzania

Przypisuje rolę [czytnika rozliczeń](built-in-roles.md#billing-reader) do użytkownika *Alain \@ example.com* w zakresie grupy zarządzania.

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName alain@example.com `
-RoleDefinitionName "Billing Reader" `
-Scope "/providers/Microsoft.Management/managementGroups/marketing-group"

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

## <a name="next-steps"></a>Następne kroki

- [Wyświetlanie listy przypisań ról platformy Azure przy użyciu Azure PowerShell](role-assignments-list-powershell.md)
- [Samouczek: udzielanie grupie dostępu do zasobów platformy Azure przy użyciu Azure PowerShell](tutorial-role-assignments-group-powershell.md)
- [Zarządzanie zasobami za pomocą Azure PowerShell](../azure-resource-manager/management/manage-resources-powershell.md)
