---
title: Lista definicji ról platformy Azure — Azure RBAC
description: Dowiedz się, jak wyświetlić listę ról wbudowanych i niestandardowych platformy Azure przy użyciu Azure Portal, Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 03/26/2021
ms.author: rolyon
ms.openlocfilehash: b285755d24cdbf1f8ef06eb850fc218a00734f16
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771735"
---
# <a name="list-azure-role-definitions"></a>Lista definicji ról platformy Azure

Definicja roli to kolekcja uprawnień, które można wykonać, takich jak odczyt, zapis i usuwanie. Zazwyczaj jest to po prostu nazywana rolą. Kontrola dostępu oparta na rolach [(RBAC)](overview.md) platformy [](built-in-roles.md) Azure ma ponad 120 wbudowanych ról lub można tworzyć własne role niestandardowe. W tym artykule opisano sposób listy ról wbudowanych i niestandardowych, których można użyć do udzielenia dostępu do zasobów platformy Azure.

Aby wyświetlić listę ról administratorów dla Azure Active Directory, zobacz [Uprawnienia roli](../active-directory/roles/permissions-reference.md)administratorów w Azure Active Directory .

## <a name="azure-portal"></a>Azure Portal

### <a name="list-all-roles"></a>Lista wszystkich ról

Wykonaj następujące kroki, aby wyświetlić listę wszystkich ról w Azure Portal.

Jeśli chcesz wyświetlić zaktualizowane środowisko ról, zapoznaj się z kartą **Role (wersja zapoznawcza),** która jest obecnie dostępna w publicznej wersji zapoznawczej. Karta **Role (wersja zapoznawcza)** zawiera tę samą listę ról co karta **Role z** dodatkowymi funkcjami. Do pracy z rolami można użyć jednej z kart ról, jednak w przypadku tworzenia lub usuwania ról niestandardowych może być konieczne ręczne odświeżenie strony w celu zobaczenia najnowszych zmian.

#### <a name="roles"></a>[Role](#tab/roles/)

1. W Azure Portal kliknij pozycję **Wszystkie usługi,** a następnie wybierz dowolny zakres. Możesz na przykład wybrać grupy **zarządzania,** **subskrypcje,** **grupy zasobów** lub zasób.

1. Kliknij określony zasób.

1. Kliknij pozycję **Kontrola dostępu (IAM)**.

1. Kliknij **kartę Role,** aby wyświetlić listę wszystkich ról wbudowanych i niestandardowych.

   Można wyświetlić liczbę użytkowników i grup przypisanych do poszczególnych ról w bieżącym zakresie.

   ![Lista ról](./media/role-definitions-list/roles-list-current.png)

#### <a name="roles-preview"></a>[Role (wersja zapoznawcza)](#tab/roles-preview/)

1. W Azure Portal kliknij pozycję **Wszystkie usługi,** a następnie wybierz dowolny zakres. Możesz na przykład wybrać grupy **zarządzania,** **subskrypcje,** **grupy zasobów** lub zasób.

1. Kliknij określony zasób.

1. Kliknij pozycję **Kontrola dostępu (IAM)**.

1. Kliknij **kartę Role (wersja** zapoznawcza), aby wyświetlić listę wszystkich ról wbudowanych i niestandardowych.

   ![Lista ról korzystająca ze obsługi wersji zapoznawczej](./media/role-definitions-list/roles-list.png)

1. Aby wyświetlić uprawnienia dla określonej roli, w kolumnie **Szczegóły** kliknij link **Wyświetl.**

    Zostanie wyświetlone okienko uprawnień.

1. Kliknij **kartę Uprawnienia,** aby wyświetlić i wyszukać uprawnienia dla wybranej roli.

   ![Uprawnienia roli przy użyciu wersji zapoznawczej](./media/role-definitions-list/role-permissions.png)

---

## <a name="azure-powershell"></a>Azure PowerShell

### <a name="list-all-roles"></a>Lista wszystkich ról

Aby wyświetlić listę wszystkich ról w Azure PowerShell, użyj [get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

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

Aby wyświetlić szczegóły określonej roli, użyj [get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

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

### <a name="list-a-role-definition-in-json-format"></a>Lista definicji roli w formacie JSON

Aby wyświetlić listę ról w formacie JSON, użyj [get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

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

### <a name="list-permissions-of-a-role-definition"></a>Lista uprawnień definicji roli

Aby wyświetlić listę uprawnień dla określonej roli, użyj [get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

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

### <a name="list-all-roles"></a>Lista wszystkich ról

Aby wyświetlić listę wszystkich ról w interfejsie wiersza polecenia platformy Azure, użyj [polecenia az role definition list](/cli/azure/role/definition#az_role_definition_list).

```azurecli
az role definition list
```

W poniższym przykładzie wymieniono nazwę i opis wszystkich dostępnych definicji ról:

```azurecli
az role definition list --output json --query '[].{roleName:roleName, description:description}'
```

```json
[
  {
    "description": "Can manage service and the APIs",
    "roleName": "API Management Service Contributor"
  },
  {
    "description": "Can manage service but not the APIs",
    "roleName": "API Management Service Operator Role"
  },
  {
    "description": "Read-only access to service and APIs",
    "roleName": "API Management Service Reader Role"
  },

  ...

]
```

Poniższy przykład zawiera listę wszystkich wbudowanych ról.

```azurecli
az role definition list --custom-role-only false --output json --query '[].{roleName:roleName, description:description, roleType:roleType}'
```

```json
[
  {
    "description": "Can manage service and the APIs",
    "roleName": "API Management Service Contributor",
    "roleType": "BuiltInRole"
  },
  {
    "description": "Can manage service but not the APIs",
    "roleName": "API Management Service Operator Role",
    "roleType": "BuiltInRole"
  },
  {
    "description": "Read-only access to service and APIs",
    "roleName": "API Management Service Reader Role",
    "roleType": "BuiltInRole"
  },
  
  ...

]
```

### <a name="list-a-role-definition"></a>Lista definicji roli

Aby wyświetlić szczegółowe informacje o roli, użyj [az role definition list](/cli/azure/role/definition#az_role_definition_list).

```azurecli
az role definition list --name {roleName}
```

Poniższy przykład zawiera listę definicji *roli* Współautor:

```azurecli
az role definition list --name "Contributor"
```

```json
[
  {
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "dataActions": [],
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action",
          "Microsoft.Blueprint/blueprintAssignments/write",
          "Microsoft.Blueprint/blueprintAssignments/delete"
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

### <a name="list-permissions-of-a-role-definition"></a>Lista uprawnień definicji roli

W poniższym przykładzie wymieniono *tylko akcje,* *a nie akcje* roli *Współautor.*

```azurecli
az role definition list --name "Contributor" --output json --query '[].{actions:permissions[0].actions, notActions:permissions[0].notActions}'
```

```json
[
  {
    "actions": [
      "*"
    ],
    "notActions": [
      "Microsoft.Authorization/*/Delete",
      "Microsoft.Authorization/*/Write",
      "Microsoft.Authorization/elevateAccess/Action",
      "Microsoft.Blueprint/blueprintAssignments/write",
      "Microsoft.Blueprint/blueprintAssignments/delete"
    ]
  }
]
```

W poniższym przykładzie wymieniono tylko akcje roli Współautor *maszyny wirtualnej.*

```azurecli
az role definition list --name "Virtual Machine Contributor" --output json --query '[].permissions[0].actions'
```

```json
[
  [
    "Microsoft.Authorization/*/read",
    "Microsoft.Compute/availabilitySets/*",
    "Microsoft.Compute/locations/*",
    "Microsoft.Compute/virtualMachines/*",
    "Microsoft.Compute/virtualMachineScaleSets/*",
    "Microsoft.Compute/disks/write",
    "Microsoft.Compute/disks/read",
    "Microsoft.Compute/disks/delete",
    "Microsoft.DevTestLab/schedules/*",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
    "Microsoft.Network/loadBalancers/backendAddressPools/join/action",

    ...

    "Microsoft.Storage/storageAccounts/listKeys/action",
    "Microsoft.Storage/storageAccounts/read",
    "Microsoft.Support/*"
  ]
]
```

## <a name="rest-api"></a>Interfejs API REST

### <a name="list-role-definitions"></a>Lista definicji ról

Aby wyświetlić listę definicji ról, użyj interfejsu API REST [Role Definitions - List.](/rest/api/authorization/roledefinitions/list) Aby uściślić wyniki, należy określić zakres i opcjonalny filtr.

1. Rozpocznij od następującego żądania:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?$filter={$filter}&api-version=2015-07-01
    ```

1. W identyfikatorze URI zastąp *element {scope}* zakresem, dla którego chcesz wyświetlić listę definicji ról.

    > [!div class="mx-tableFixed"]
    > | Zakres | Typ |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupa zarządzania |
    > | `subscriptions/{subscriptionId1}` | Subskrypcja |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Zasób |

    W poprzednim przykładzie microsoft.web to dostawca zasobów, który odwołuje się do App Service wystąpienia. Podobnie można użyć innych dostawców zasobów i określić zakres. Aby uzyskać więcej informacji, zobacz Azure Resource providers and types and supported Azure resource provider operations [(Dostawcy](../azure-resource-manager/management/resource-providers-and-types.md) zasobów i typy zasobów platformy Azure oraz obsługiwane [operacje dostawcy zasobów platformy Azure).](resource-provider-operations.md)  
     
1. Zastąp *element {filter}* warunkiem, który chcesz zastosować w celu filtrowania listy definicji roli.

    > [!div class="mx-tableFixed"]
    > | Filtr | Opis |
    > | --- | --- |
    > | `$filter=atScopeAndBelow()` | Wyświetla listę definicji ról dla określonego zakresu i wszystkich zakresów. |
    > | `$filter=type+eq+'{type}'` | Wyświetla listę definicji ról określonego typu. Typem roli może być `CustomRole` lub `BuiltInRole` . |

Następujące żądanie wyświetla listę niestandardowych definicji ról w zakresie subskrypcji:

```http
GET https://management.azure.com/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter=type+eq+'CustomRole'
```

Poniżej przedstawiono przykładowe dane wyjściowe:

```json
{
    "value": [
        {
            "properties": {
                "roleName": "Billing Reader Plus",
                "type": "CustomRole",
                "description": "Read billing data and download invoices",
                "assignableScopes": [
                    "/subscriptions/{subscriptionId1}"
                ],
                "permissions": [
                    {
                        "actions": [
                            "Microsoft.Authorization/*/read",
                            "Microsoft.Billing/*/read",
                            "Microsoft.Commerce/*/read",
                            "Microsoft.Consumption/*/read",
                            "Microsoft.Management/managementGroups/read",
                            "Microsoft.CostManagement/*/read",
                            "Microsoft.Billing/invoices/download/action",
                            "Microsoft.CostManagement/exports/*"
                        ],
                        "notActions": [
                            "Microsoft.CostManagement/exports/delete"
                        ]
                    }
                ],
                "createdOn": "2020-02-21T04:49:13.7679452Z",
                "updatedOn": "2020-02-21T04:49:13.7679452Z",
                "createdBy": "{createdByObjectId1}",
                "updatedBy": "{updatedByObjectId1}"
            },
            "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId1}",
            "type": "Microsoft.Authorization/roleDefinitions",
            "name": "{roleDefinitionId1}"
        }
    ]
}
```

### <a name="list-a-role-definition"></a>Lista definicji roli

Aby wyświetlić listę szczegółów określonej roli, użyj interfejsu API REST Definicje ról [—](/rest/api/authorization/roledefinitions/get) Pobierz lub Definicje ról [— Pobierz według identyfikatora.](/rest/api/authorization/roledefinitions/getbyid)

1. Rozpocznij od następującego żądania:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

    W przypadku definicji roli na poziomie katalogu możesz użyć tego żądania:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. W identyfikatorze URI zastąp *element {scope}* zakresem, dla którego chcesz wyświetlić definicję roli.

    > [!div class="mx-tableFixed"]
    > | Zakres | Typ |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupa zarządzania |
    > | `subscriptions/{subscriptionId1}` | Subskrypcja |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Zasób |
     
1. Zastąp *element {roleDefinitionId}* identyfikatorem definicji roli.

Następujące żądanie wyświetla [definicję roli](built-in-roles.md#reader) Czytelnik:

```http
GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7?api-version=2015-07-01
```

Poniżej przedstawiono przykładowe dane wyjściowe:

```json
{
    "properties": {
        "roleName": "Reader",
        "type": "BuiltInRole",
        "description": "Lets you view everything, but not make any changes.",
        "assignableScopes": [
            "/"
        ],
        "permissions": [
            {
                "actions": [
                    "*/read"
                ],
                "notActions": []
            }
        ],
        "createdOn": "2015-02-02T21:55:09.8806423Z",
        "updatedOn": "2019-02-05T21:24:35.7424745Z",
        "createdBy": null,
        "updatedBy": null
    },
    "id": "/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "type": "Microsoft.Authorization/roleDefinitions",
    "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
}
```

## <a name="next-steps"></a>Następne kroki

- [Wbudowane role platformy Azure](built-in-roles.md)
- [Role niestandardowe platformy Azure](custom-roles.md)
- [Lista przypisań ról platformy Azure przy użyciu Azure Portal](role-assignments-list-portal.md)
- [Przypisywanie ról platformy Azure przy użyciu Azure Portal](role-assignments-portal.md)
