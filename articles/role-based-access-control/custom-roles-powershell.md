---
title: Tworzenie lub aktualizowanie ról niestandardowych platformy Azure przy użyciu Azure PowerShell — RBAC
description: Dowiedz się, jak wyświetlać, tworzyć, aktualizować i usuwać role niestandardowe przy użyciu Azure PowerShell i kontroli dostępu opartej na rolach (RBAC) na platformie Azure.
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
ms.date: 03/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 799475db567c88f067192d027589e9185ee1782b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97369178"
---
# <a name="create-or-update-azure-custom-roles-using-azure-powershell"></a>Tworzenie lub aktualizowanie ról niestandardowych platformy Azure przy użyciu Azure PowerShell

> [!IMPORTANT]
> Dodawanie grupy zarządzania do programu `AssignableScopes` jest obecnie w wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Jeśli [wbudowane role platformy Azure](built-in-roles.md) nie są zgodne z konkretnymi potrzebami organizacji, możesz utworzyć własne role niestandardowe. W tym artykule opisano sposób wyświetlania, tworzenia, aktualizowania lub usuwania ról niestandardowych przy użyciu Azure PowerShell.

Aby zapoznać się z samouczkiem krok po kroku na temat tworzenia roli niestandardowej, zobacz [Samouczek: tworzenie roli niestandardowej platformy Azure przy użyciu Azure PowerShell](tutorial-custom-role-powershell.md).

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Do tworzenia ról niestandardowych wymagane są:

- Uprawnienia do tworzenia ról niestandardowych, takie jak [Właściciel](built-in-roles.md#owner) lub [Administrator dostępu użytkowników](built-in-roles.md#user-access-administrator)
- Usługa [Azure Cloud Shell](../cloud-shell/overview.md) lub program [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-custom-roles"></a>Wyświetlanie ról niestandardowych

Aby wyświetlić listę ról, które są dostępne do przypisania w zakresie, użyj polecenia [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) . Poniższy przykład wyświetla listę wszystkich ról, które są dostępne do przypisania w ramach wybranej subskrypcji.

```azurepowershell
Get-AzRoleDefinition | FT Name, IsCustom
```

```Example
Name                                              IsCustom
----                                              --------
Virtual Machine Operator                              True
AcrImageSigner                                       False
AcrQuarantineReader                                  False
AcrQuarantineWriter                                  False
API Management Service Contributor                   False
...
```

Poniższy przykład zawiera listę tylko ról niestandardowych, które są dostępne do przypisania w ramach wybranej subskrypcji.

```azurepowershell
Get-AzRoleDefinition -Custom | FT Name, IsCustom
```

```Example
Name                     IsCustom
----                     --------
Virtual Machine Operator     True
```

Jeśli wybrana subskrypcja nie znajduje się w `AssignableScopes` roli, rola niestandardowa nie zostanie wyświetlona.

## <a name="list-a-custom-role-definition"></a>Utwórz listę niestandardowych definicji roli

Aby wyświetlić listę niestandardowych definicji roli, użyj polecenie [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition). Jest to samo polecenie, które jest używane w przypadku roli wbudowanej.

```azurepowershell
Get-AzRoleDefinition <role_name> | ConvertTo-Json
```

```Example
PS C:\> Get-AzRoleDefinition "Virtual Machine Operator" | ConvertTo-Json

{
  "Name": "Virtual Machine Operator",
  "Id": "00000000-0000-0000-0000-000000000000",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

Poniższy przykład zawiera listę tylko działań roli:

```azurepowershell
(Get-AzRoleDefinition <role_name>).Actions
```

```Example
PS C:\> (Get-AzRoleDefinition "Virtual Machine Operator").Actions

"Microsoft.Storage/*/read",
"Microsoft.Network/*/read",
"Microsoft.Compute/*/read",
"Microsoft.Compute/virtualMachines/start/action",
"Microsoft.Compute/virtualMachines/restart/action",
"Microsoft.Authorization/*/read",
"Microsoft.ResourceHealth/availabilityStatuses/read",
"Microsoft.Resources/subscriptions/resourceGroups/read",
"Microsoft.Insights/alertRules/*",
"Microsoft.Insights/diagnosticSettings/*",
"Microsoft.Support/*"
```

## <a name="create-a-custom-role"></a>Tworzenie roli niestandardowej

Aby utworzyć rolę niestandardową, użyj polecenia [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) . Istnieją dwie metody tworzenia struktury roli przy użyciu `PSRoleDefinition` obiektu lub szablonu JSON. 

### <a name="get-operations-for-a-resource-provider"></a>Pobierz operacje dla dostawcy zasobów

Podczas tworzenia ról niestandardowych ważne jest, aby znać wszystkie możliwe operacje od dostawców zasobów.
Możesz wyświetlić listę [operacji dostawcy zasobów](resource-provider-operations.md) lub użyć polecenia [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) , aby pobrać te informacje.
Jeśli na przykład chcesz sprawdzić wszystkie dostępne operacje dotyczące maszyn wirtualnych, użyj tego polecenia:

```azurepowershell
Get-AzProviderOperation <operation> | FT OperationName, Operation, Description -AutoSize
```

```Example
PS C:\> Get-AzProviderOperation "Microsoft.Compute/virtualMachines/*" | FT OperationName, Operation, Description -AutoSize

OperationName                                  Operation                                                      Description
-------------                                  ---------                                                      -----------
Get Virtual Machine                            Microsoft.Compute/virtualMachines/read                         Get the propertie...
Create or Update Virtual Machine               Microsoft.Compute/virtualMachines/write                        Creates a new vir...
Delete Virtual Machine                         Microsoft.Compute/virtualMachines/delete                       Deletes the virtu...
Start Virtual Machine                          Microsoft.Compute/virtualMachines/start/action                 Starts the virtua...
...
```

### <a name="create-a-custom-role-with-the-psroledefinition-object"></a>Tworzenie roli niestandardowej przy użyciu obiektu PSRoleDefinition

Jeśli tworzysz rolę niestandardową przy użyciu programu PowerShell, możesz użyć jednej z [wbudowanych ról](built-in-roles.md) jako punktu początkowego lub można zacząć od podstaw. Pierwszy przykład w tej sekcji rozpoczyna się od wbudowanej roli, a następnie dostosowuje ją z większą liczbą uprawnień. Edytuj atrybuty, aby dodać `Actions` , `NotActions` lub, `AssignableScopes` a następnie Zapisz zmiany jako nową rolę.

Poniższy przykład jest uruchamiany z wbudowaną rolą [współautor maszyny wirtualnej](built-in-roles.md#virtual-machine-contributor) , aby utworzyć rolę niestandardową o nazwie *operator maszyny wirtualnej*. Nowa rola przyznaje dostęp do wszystkich operacji odczytu z dostawców zasobów *Microsoft. COMPUTE*, *Microsoft. Storage* i *Microsoft. Network* oraz udziela dostępu do uruchamiania, ponownego uruchamiania i monitorowania maszyn wirtualnych. Roli niestandardowej można używać w dwóch subskrypcjach.

```azurepowershell
$role = Get-AzRoleDefinition "Virtual Machine Contributor"
$role.Id = $null
$role.Name = "Virtual Machine Operator"
$role.Description = "Can monitor and restart virtual machines."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/*/read")
$role.Actions.Add("Microsoft.Network/*/read")
$role.Actions.Add("Microsoft.Compute/*/read")
$role.Actions.Add("Microsoft.Compute/virtualMachines/start/action")
$role.Actions.Add("Microsoft.Compute/virtualMachines/restart/action")
$role.Actions.Add("Microsoft.Authorization/*/read")
$role.Actions.Add("Microsoft.ResourceHealth/availabilityStatuses/read")
$role.Actions.Add("Microsoft.Resources/subscriptions/resourceGroups/read")
$role.Actions.Add("Microsoft.Insights/alertRules/*")
$role.Actions.Add("Microsoft.Support/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/00000000-0000-0000-0000-000000000000")
$role.AssignableScopes.Add("/subscriptions/11111111-1111-1111-1111-111111111111")
New-AzRoleDefinition -Role $role
```

Poniższy przykład przedstawia inny sposób tworzenia roli niestandardowej *operatora maszyny wirtualnej* . Zaczyna od tworzenia nowego `PSRoleDefinition` obiektu. Operacje akcji są określone w `perms` zmiennej i są ustawione na `Actions` Właściwość. `NotActions`Właściwość jest ustawiana poprzez odczytywanie `NotActions` z roli wbudowane [współautor maszyny wirtualnej](built-in-roles.md#virtual-machine-contributor) . Ponieważ [współautor maszyny wirtualnej](built-in-roles.md#virtual-machine-contributor) nie ma żadnego `NotActions` , ten wiersz nie jest wymagany, ale pokazuje, jak można pobrać informacje z innej roli.

```azurepowershell
$role = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
$role.Name = 'Virtual Machine Operator 2'
$role.Description = 'Can monitor and restart virtual machines.'
$role.IsCustom = $true
$perms = 'Microsoft.Storage/*/read','Microsoft.Network/*/read','Microsoft.Compute/*/read'
$perms += 'Microsoft.Compute/virtualMachines/start/action','Microsoft.Compute/virtualMachines/restart/action'
$perms += 'Microsoft.Authorization/*/read'
$perms += 'Microsoft.ResourceHealth/availabilityStatuses/read'
$perms += 'Microsoft.Resources/subscriptions/resourceGroups/read'
$perms += 'Microsoft.Insights/alertRules/*','Microsoft.Support/*'
$role.Actions = $perms
$role.NotActions = (Get-AzRoleDefinition -Name 'Virtual Machine Contributor').NotActions
$subs = '/subscriptions/00000000-0000-0000-0000-000000000000','/subscriptions/11111111-1111-1111-1111-111111111111'
$role.AssignableScopes = $subs
New-AzRoleDefinition -Role $role
```

### <a name="create-a-custom-role-with-json-template"></a>Utwórz rolę niestandardową z szablonem JSON

Szablon JSON może służyć jako definicja źródła dla roli niestandardowej. Poniższy przykład tworzy rolę niestandardową, która umożliwia dostęp do odczytu do magazynu i zasobów obliczeniowych, dostęp do pomocy technicznej i dodaje tę rolę do dwóch subskrypcji. Utwórz nowy plik `C:\CustomRoles\customrole1.json` z poniższym przykładem. Identyfikator należy ustawić na `null` podczas tworzenia początkowej roli, ponieważ nowy identyfikator jest generowany automatycznie. 

```json
{
  "Name": "Custom Role 1",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

Aby dodać rolę do subskrypcji, uruchom następujące polecenie programu PowerShell:

```azurepowershell
New-AzRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="update-a-custom-role"></a>Aktualizacja roli niestandardowej

Podobnie jak w przypadku tworzenia roli niestandardowej, można zmodyfikować istniejącą rolę niestandardową przy użyciu `PSRoleDefinition` szablonu Object lub JSON.

### <a name="update-a-custom-role-with-the-psroledefinition-object"></a>Aktualizowanie roli niestandardowej przy użyciu obiektu PSRoleDefinition

Aby zmodyfikować rolę niestandardową, należy najpierw użyć polecenia [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) w celu pobrania definicji roli. Następnie wprowadź żądane zmiany w definicji roli. Na koniec użyj polecenia [Set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition) , aby zapisać zmodyfikowaną definicję roli.

Poniższy przykład dodaje `Microsoft.Insights/diagnosticSettings/*` operację do roli niestandardowej *operatora maszyny wirtualnej* .

```azurepowershell
$role = Get-AzRoleDefinition "Virtual Machine Operator"
$role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
Set-AzRoleDefinition -Role $role
```

```Example
PS C:\> $role = Get-AzRoleDefinition "Virtual Machine Operator"
PS C:\> $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
PS C:\> Set-AzRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}
```

Poniższy przykład dodaje subskrypcję platformy Azure do przypisanych zakresów roli niestandardowej *operatora maszyny wirtualnej* .

```azurepowershell
Get-AzSubscription -SubscriptionName Production3

$role = Get-AzRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
Set-AzRoleDefinition -Role $role
```

```Example
PS C:\> Get-AzSubscription -SubscriptionName Production3

Name     : Production3
Id       : 22222222-2222-2222-2222-222222222222
TenantId : 99999999-9999-9999-9999-999999999999
State    : Enabled

PS C:\> $role = Get-AzRoleDefinition "Virtual Machine Operator"
PS C:\> $role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
PS C:\> Set-AzRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111,
                   /subscriptions/22222222-2222-2222-2222-222222222222}
```

Poniższy przykład dodaje grupę zarządzania do `AssignableScopes` roli niestandardowej *operatora maszyny wirtualnej* . Dodawanie grupy zarządzania do programu `AssignableScopes` jest obecnie w wersji zapoznawczej.

```azurepowershell
Get-AzManagementGroup

$role = Get-AzRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/providers/Microsoft.Management/managementGroups/{groupId1}")
Set-AzRoleDefinition -Role $role
```

```Example
PS C:\> Get-AzManagementGroup

Id          : /providers/Microsoft.Management/managementGroups/marketing-group
Type        : /providers/Microsoft.Management/managementGroups
Name        : marketing-group
TenantId    : 99999999-9999-9999-9999-999999999999
DisplayName : Marketing group

PS C:\> $role = Get-AzRoleDefinition "Virtual Machine Operator"
PS C:\> $role.AssignableScopes.Add("/providers/Microsoft.Management/managementGroups/marketing-group")
PS C:\> Set-AzRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111,
                   /subscriptions/22222222-2222-2222-2222-222222222222,
                   /providers/Microsoft.Management/managementGroups/marketing-group}
```

### <a name="update-a-custom-role-with-a-json-template"></a>Aktualizowanie roli niestandardowej przy użyciu szablonu JSON

Przy użyciu poprzedniego szablonu JSON można łatwo modyfikować istniejącą rolę niestandardową w celu dodawania lub usuwania akcji. Zaktualizuj szablon JSON i Dodaj akcję odczytu dla sieci, jak pokazano w poniższym przykładzie. Definicje wymienione w szablonie nie są stosowane łącznie do istniejącej definicji, co oznacza, że rola jest wyświetlana dokładnie w przypadku określenia w szablonie. Należy również zaktualizować pole ID identyfikatorem roli. Jeśli nie masz pewności, jaka jest ta wartość, możesz uzyskać te informacje za pomocą polecenia cmdlet [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) .

```json
{
  "Name": "Custom Role 1",
  "Id": "acce7ded-2559-449d-bcd5-e9604e50bad1",
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

Aby zaktualizować istniejącą rolę, uruchom następujące polecenie programu PowerShell:

```azurepowershell
Set-AzRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="delete-a-custom-role"></a>Usuwanie roli niestandardowej

Aby usunąć rolę niestandardową, użyj polecenia [Remove-AzRoleDefinition](/powershell/module/az.resources/remove-azroledefinition) .

Poniższy przykład usuwa rolę niestandardową *operatora maszyny wirtualnej* .

```azurepowershell
Get-AzRoleDefinition "Virtual Machine Operator"
Get-AzRoleDefinition "Virtual Machine Operator" | Remove-AzRoleDefinition
```

```Example
PS C:\> Get-AzRoleDefinition "Virtual Machine Operator"

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}

PS C:\> Get-AzRoleDefinition "Virtual Machine Operator" | Remove-AzRoleDefinition

Confirm
Are you sure you want to remove role definition with name 'Virtual Machine Operator'.
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
```

## <a name="next-steps"></a>Następne kroki

- [Samouczek: tworzenie roli niestandardowej platformy Azure przy użyciu Azure PowerShell](tutorial-custom-role-powershell.md)
- [Role niestandardowe platformy Azure](custom-roles.md)
- [Operacje dostawcy zasobów platformy Azure](resource-provider-operations.md)
