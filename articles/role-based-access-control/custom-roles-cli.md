---
title: Tworzenie lub aktualizowanie ról niestandardowych platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure — RBAC platformy Azure
description: Dowiedz się, jak wyświetlić, utworzyć, zaktualizować lub usunąć role niestandardowe platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure i kontroli dostępu na podstawie ról (RBAC) platformy Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/17/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: d3d05ba65e0d3918f1651c36cd17700ebf74de76
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778341"
---
# <a name="create-or-update-azure-custom-roles-using-azure-cli"></a>Tworzenie lub aktualizowanie ról niestandardowych platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

> [!IMPORTANT]
> Dodawanie grupy zarządzania do programu jest `AssignableScopes` obecnie dostępne w wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Jeśli wbudowane [role platformy Azure](built-in-roles.md) nie spełniają określonych potrzeb organizacji, możesz utworzyć własne role niestandardowe. W tym artykule opisano sposób tworzenia, aktualizowania i usuwania ról niestandardowych przy użyciu interfejsu wiersza polecenia platformy Azure.

Aby uzyskać samouczek krok po kroku dotyczący tworzenia roli niestandardowej, zobacz [Samouczek: tworzenie](tutorial-custom-role-cli.md)roli niestandardowej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Do utworzenia ról niestandardowych potrzebne są:

- Uprawnienia do tworzenia ról niestandardowych, takie jak [Właściciel](built-in-roles.md#owner) lub [Administrator dostępu użytkowników](built-in-roles.md#user-access-administrator)
- [Azure Cloud Shell](../cloud-shell/overview.md) lub [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)

## <a name="list-custom-roles"></a>Wyświetlanie ról niestandardowych

Aby wyświetlić listę ról niestandardowych, które są dostępne do przypisania, użyj [az role definition list](/cli/azure/role/definition#az_role_definition_list). Poniższy przykład zawiera listę wszystkich ról niestandardowych w bieżącej subskrypcji.

```azurecli
az role definition list --custom-role-only true --output json --query '[].{roleName:roleName, roleType:roleType}'
```

```json
[
  {
    "roleName": "My Management Contributor",
    "type": "CustomRole"
  },
  {
    "roleName": "My Service Reader Role",
    "type": "CustomRole"
  },
  {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole"
  }
]
```

## <a name="list-a-custom-role-definition"></a>Lista definicji roli niestandardowej

Aby wyświetlić listę definicji roli niestandardowej, użyj [az role definition list](/cli/azure/role/definition#az_role_definition_list). Jest to to samo polecenie, które będzie miało zastosowanie w przypadku roli wbudowanej.

```azurecli
az role definition list --name {roleName}
```

W poniższym przykładzie przedstawiono *definicję roli Operator maszyny* wirtualnej:

```azurecli
az role definition list --name "Virtual Machine Operator"
```

```json
[
  {
    "assignableScopes": [
      "/subscriptions/{subscriptionId}"
    ],
    "description": "Can monitor and restart virtual machines.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00000000-0000-0000-0000-000000000000",
    "name": "00000000-0000-0000-0000-000000000000",
    "permissions": [
      {
        "actions": [
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
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Virtual Machine Operator",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

W poniższym przykładzie wymieniono tylko akcje roli *Operator maszyny wirtualnej:*

```azurecli
az role definition list --name "Virtual Machine Operator" --output json --query '[].permissions[0].actions'
```

```json
[
  [
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
  ]
]
```

## <a name="create-a-custom-role"></a>Tworzenie roli niestandardowej

Aby utworzyć rolę niestandardową, użyj [az role definition create](/cli/azure/role/definition#az_role_definition_create). Definicją roli może być opis JSON lub ścieżka do pliku zawierającego opis JSON.

```azurecli
az role definition create --role-definition {roleDefinition}
```

Poniższy przykład tworzy rolę niestandardową o nazwie *Operator maszyny wirtualnej*. Ta rola niestandardowa przypisuje dostęp do wszystkich operacji odczytu dostawców zasobów *Microsoft.Compute,* *Microsoft.Storage* i *Microsoft.Network* oraz przypisuje dostęp do uruchamiania, ponownego uruchamiania i monitorowania maszyn wirtualnych. Tej roli niestandardowej można używać w dwóch subskrypcjach. W tym przykładzie użyto pliku JSON jako danych wejściowych.

vmoperator.jswł.

```json
{
  "Name": "Virtual Machine Operator",
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
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}"
  ]
}
```

```azurecli
az role definition create --role-definition ~/roles/vmoperator.json
```

## <a name="update-a-custom-role"></a>Aktualizacja roli niestandardowej

Aby zaktualizować rolę niestandardową, najpierw użyj [az role definition list,](/cli/azure/role/definition#az_role_definition_list) aby pobrać definicję roli. Po drugie, należy wprowadzić żądane zmiany w definicji roli. Na koniec użyj [przycisku az role definition update,](/cli/azure/role/definition#az_role_definition_update) aby zapisać zaktualizowaną definicję roli.

```azurecli
az role definition update --role-definition {roleDefinition}
```

W poniższym przykładzie dodano operację *Microsoft.Insights/diagnosticSettings/* do funkcji i dodano grupę zarządzania dla roli niestandardowej `Actions` `AssignableScopes` *Operator* maszyny wirtualnej. Dodawanie grupy zarządzania do programu jest `AssignableScopes` obecnie dostępne w wersji zapoznawczej.

vmoperator.jswł.

```json
{
  "Name": "Virtual Machine Operator",
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
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}",
    "/providers/Microsoft.Management/managementGroups/marketing-group"
  ]
}
```

```azurecli
az role definition update --role-definition ~/roles/vmoperator.json
```

## <a name="delete-a-custom-role"></a>Usuwanie roli niestandardowej

Aby usunąć rolę niestandardową, użyj [az role definition delete](/cli/azure/role/definition#az_role_definition_delete). Aby określić rolę do usunięcia, użyj nazwy roli lub identyfikatora roli. Aby określić identyfikator roli, użyj [az role definition list](/cli/azure/role/definition#az_role_definition_list).

```azurecli
az role definition delete --name {roleNameOrId}
```

Poniższy przykład usuwa rolę niestandardową *Operator maszyny* wirtualnej.

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>Następne kroki

- [Samouczek: tworzenie roli niestandardowej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](tutorial-custom-role-cli.md)
- [Role niestandardowe platformy Azure](custom-roles.md)
- [Operacje dostawcy zasobów platformy Azure](resource-provider-operations.md)