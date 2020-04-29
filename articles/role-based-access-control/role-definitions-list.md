---
title: Wyświetlanie listy definicji ról w usłudze Azure RBAC przy użyciu usług Azure Portal, Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST | Microsoft Docs
description: Dowiedz się, jak wyświetlić listę ról wbudowanych i niestandardowych w usłudze Azure RBAC przy użyciu Azure Portal, Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub API REST.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: aa888eedc81ceb3188f801e273c70722207bf512
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80062986"
---
# <a name="list-role-definitions-in-azure-rbac"></a>Wyświetlanie listy definicji ról w usłudze Azure RBAC

Definicja roli to zbiór uprawnień, które mogą być wykonywane, takie jak Odczyt, zapis i usuwanie. Zwykle jest nazywana rolą. [Kontrola dostępu oparta na rolach (RBAC) na platformie Azure](overview.md) ma ponad 120 [ról wbudowanych](built-in-roles.md) lub można utworzyć własne role niestandardowe. W tym artykule opisano sposób tworzenia listy ról wbudowanych i niestandardowych, których można użyć w celu udzielenia dostępu do zasobów platformy Azure.

Aby wyświetlić listę ról administratorów dla Azure Active Directory, zobacz [uprawnienia roli administrator w programie Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

## <a name="azure-portal"></a>Azure Portal

### <a name="list-all-roles"></a>Wyświetl wszystkie role

Wykonaj następujące kroki, aby wyświetlić listę wszystkich ról w Azure Portal.

1. W Azure Portal kliknij pozycję **wszystkie usługi** , a następnie wybierz dowolny zakres. Można na przykład wybrać **grupy zarządzania**, **subskrypcje**, **grupy zasobów**lub zasób.

1. Kliknij konkretny zasób.

1. Kliknij pozycję **Kontrola dostępu (IAM)**.

1. Kliknij kartę **role** , aby wyświetlić listę wszystkich ról wbudowanych i niestandardowych.

   Możesz zobaczyć liczbę użytkowników i grup przypisanych do każdej roli w bieżącym zakresie.

   ![Lista ról](./media/role-definitions-list/roles-list.png)

## <a name="azure-powershell"></a>Azure PowerShell

### <a name="list-all-roles"></a>Wyświetl wszystkie role

Aby wyświetlić listę wszystkich ról w Azure PowerShell, użyj polecenie [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition | FT Name, Description
```

```Example
AcrImageSigner                                    acr image signer
AcrQuarantineReader                               acr quarantine data reader
AcrQuarantineWriter                               acr quarantine data writer
API Management Service Contributor                Can manage service and the APIs
API Management Service Operator Role              Can manage service but not the APIs
API Management Service Reader Role                Read-only access to service and APIs
Application Insights Component Contributor        Can manage Application Insights components
Application Insights Snapshot Debugger            Gives user permission to use Application Insights Snapshot Debugge...
Automation Job Operator                           Create and Manage Jobs using Automation Runbooks.
Automation Operator                               Automation Operators are able to start, stop, suspend, and resume ...
...
```

### <a name="list-a-role-definition"></a>Lista definicji roli

Aby wyświetlić szczegóły określonej roli, użyj polecenie [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition <role_name>
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor"

Name             : Contributor
Id               : b24988ac-6180-42a0-ab88-20f7382dd24c
IsCustom         : False
Description      : Lets you manage everything except access to resources.
Actions          : {*}
NotActions       : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
                   Microsoft.Authorization/elevateAccess/Action}
DataActions      : {}
NotDataActions   : {}
AssignableScopes : {/}
```

### <a name="list-a-role-definition-in-json-format"></a>Wyświetlanie listy definicji ról w formacie JSON

Aby wyświetlić listę ról w formacie JSON, użyj polecenie [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition <role_name> | ConvertTo-Json
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | ConvertTo-Json

{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action",
    "Microsoft.Blueprint/blueprintAssignments/write",
    "Microsoft.Blueprint/blueprintAssignments/delete"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

### <a name="list-permissions-of-a-role-definition"></a>Wyświetlanie listy uprawnień definicji roli

Aby wyświetlić listę uprawnień dla określonej roli, użyj polecenie [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition <role_name> | FL Actions, NotActions
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | FL Actions, NotActions

Actions    : {*}
NotActions : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
             Microsoft.Authorization/elevateAccess/Action,
             Microsoft.Blueprint/blueprintAssignments/write...}
```

```azurepowershell
(Get-AzRoleDefinition <role_name>).Actions
```

```Example
PS C:\> (Get-AzRoleDefinition "Virtual Machine Contributor").Actions

Microsoft.Authorization/*/read
Microsoft.Compute/availabilitySets/*
Microsoft.Compute/locations/*
Microsoft.Compute/virtualMachines/*
Microsoft.Compute/virtualMachineScaleSets/*
Microsoft.DevTestLab/schedules/*
Microsoft.Insights/alertRules/*
Microsoft.Network/applicationGateways/backendAddressPools/join/action
Microsoft.Network/loadBalancers/backendAddressPools/join/action
...
```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

### <a name="list-all-roles"></a>Wyświetl wszystkie role

Aby wyświetlić listę wszystkich ról w interfejsie wiersza polecenia platformy Azure, użyj polecenia [AZ role Definition list](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition list
```

Poniższy przykład zawiera listę nazw i opis wszystkich dostępnych definicji ról:

```azurecli
az role definition list --output json | jq '.[] | {"roleName":.roleName, "description":.description}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs"
}

...
```

W poniższym przykładzie wymieniono wszystkie wbudowane role.

```azurecli
az role definition list --custom-role-only false --output json | jq '.[] | {"roleName":.roleName, "description":.description, "roleType":.roleType}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs",
  "roleType": "BuiltInRole"
}

...
```

### <a name="list-a-role-definition"></a>Lista definicji roli

Aby wyświetlić szczegóły roli, użyj [AZ role Definition list](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition list --name <role_name>
```

Poniższy przykład zawiera listę definicji roli *współautor* :

```azurecli
az role definition list --name "Contributor"
```

```Output
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "additionalProperties": {},
        "dataActions": [],
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
        "notDataActions": []
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

### <a name="list-permissions-of-a-role-definition"></a>Wyświetlanie listy uprawnień definicji roli

W poniższym przykładzie wymieniono tylko *Akcje* i *nienaruszone* roli *współautor* .

```azurecli
az role definition list --name "Contributor" --output json | jq '.[] | {"actions":.permissions[0].actions, "notActions":.permissions[0].notActions}'
```

```Output
{
  "actions": [
    "*"
  ],
  "notActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action"
  ]
}
```

W poniższym przykładzie wymieniono tylko akcje roli *współautor maszyny wirtualnej* .

```azurecli
az role definition list --name "Virtual Machine Contributor" --output json | jq '.[] | .permissions[0].actions'
```

```Output
[
  "Microsoft.Authorization/*/read",
  "Microsoft.Compute/availabilitySets/*",
  "Microsoft.Compute/locations/*",
  "Microsoft.Compute/virtualMachines/*",
  "Microsoft.Compute/virtualMachineScaleSets/*",
  "Microsoft.Insights/alertRules/*",
  "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
  "Microsoft.Network/loadBalancers/backendAddressPools/join/action",

  ...

  "Microsoft.Storage/storageAccounts/listKeys/action",
  "Microsoft.Storage/storageAccounts/read"
]
```

## <a name="rest-api"></a>Interfejs API REST

### <a name="list-role-definitions"></a>Lista definicji ról

Aby wyświetlić listę definicji ról, użyj interfejsu API REST z [listą ról](/rest/api/authorization/roledefinitions/list) . Aby uściślić wyniki, należy określić zakres i opcjonalny filtr.

1. Rozpocznij od następującego żądania:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?$filter={$filter}&api-version=2015-07-01
    ```

1. W identyfikatorze URI Zastąp wartość *{SCOPE}* zakresem, dla którego chcesz wyświetlić listę definicji ról.

    > [!div class="mx-tableFixed"]
    > | Zakres | Typ |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupa zarządzania |
    > | `subscriptions/{subscriptionId1}` | Subskrypcja |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Zasób |

    W poprzednim przykładzie firma Microsoft. Web jest dostawcą zasobów, który odwołuje się do wystąpienia App Service. Analogicznie, można użyć innych dostawców zasobów i określić zakres. Aby uzyskać więcej informacji, zobacz [dostawcy zasobów platformy Azure i typy](../azure-resource-manager/management/resource-providers-and-types.md) oraz obsługiwane [Azure Resource Manager operacje dostawcy](resource-provider-operations.md)zasobów.  
     
1. Zamień *filtr {Filter}* na warunek, który ma zostać zastosowany, aby przefiltrować listę definicji ról.

    > [!div class="mx-tableFixed"]
    > | Filtr | Opis |
    > | --- | --- |
    > | `$filter=atScopeAndBelow()` | Wyświetla definicje ról dla określonego zakresu i wszelkich podzakresów. |
    > | `$filter=type+eq+'{type}'` | Wyświetla definicje ról określonego typu. Typem roli może być `CustomRole` lub. `BuiltInRole` |

### <a name="list-a-role-definition"></a>Lista definicji roli

Aby wyświetlić szczegółowe informacje o określonej roli, należy użyć definicji [ról — Get](/rest/api/authorization/roledefinitions/get) lub definicji ról — interfejsu API REST usługi [Get przez ID](/rest/api/authorization/roledefinitions/getbyid) .

1. Rozpocznij od następującego żądania:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

    W przypadku definicji roli na poziomie katalogu można użyć tego żądania:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. W identyfikatorze URI Zastąp wartość *{SCOPE}* zakresem, dla którego chcesz utworzyć listę definicji roli.

    > [!div class="mx-tableFixed"]
    > | Zakres | Typ |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupa zarządzania |
    > | `subscriptions/{subscriptionId1}` | Subskrypcja |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Zasób |
     
1. Zastąp ciąg *{zduplikowanych}* identyfikatorem definicji roli.

## <a name="next-steps"></a>Następne kroki

- [Wbudowane role dla zasobów platformy Azure](built-in-roles.md)
- [Niestandardowe role dla zasobów platformy Azure](custom-roles.md)
- [Wyświetlanie listy przypisań ról przy użyciu funkcji RBAC platformy Azure i Azure Portal](role-assignments-list-portal.md)
- [Dodawanie lub usuwanie przypisań ról przy użyciu funkcji RBAC platformy Azure i Azure Portal](role-assignments-portal.md)
