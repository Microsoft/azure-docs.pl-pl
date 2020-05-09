---
title: Tworzenie lub aktualizowanie ról niestandardowych platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure — RBAC
description: Dowiedz się, jak wyświetlać, tworzyć, aktualizować i usuwać role niestandardowe platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure i kontroli dostępu opartej na rolach (RBAC) platformy Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: cac0116cf7a068e63cb54698f7273b8c063ff854
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2020
ms.locfileid: "82734845"
---
# <a name="create-or-update-azure-custom-roles-using-azure-cli"></a>Tworzenie lub aktualizowanie ról niestandardowych platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

> [!IMPORTANT]
> Dodawanie grupy zarządzania do `AssignableScopes` programu jest obecnie w wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Jeśli [wbudowane role platformy Azure](built-in-roles.md) nie są zgodne z konkretnymi potrzebami organizacji, możesz utworzyć własne role niestandardowe. W tym artykule opisano sposób wyświetlania, tworzenia, aktualizowania lub usuwania ról niestandardowych przy użyciu interfejsu wiersza polecenia platformy Azure.

Aby zapoznać się z samouczkiem krok po kroku na temat tworzenia roli niestandardowej, zobacz [Samouczek: tworzenie roli niestandardowej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](tutorial-custom-role-cli.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do tworzenia ról niestandardowych wymagane są:

- Uprawnienia do tworzenia ról niestandardowych, takie jak [Właściciel](built-in-roles.md#owner) lub [Administrator dostępu użytkowników](built-in-roles.md#user-access-administrator)
- [Azure Cloud Shell](../cloud-shell/overview.md) lub [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)

## <a name="list-custom-roles"></a>Wyświetlanie ról niestandardowych

Aby wyświetlić listę ról niestandardowych, które są dostępne do przypisania, użyj [AZ role Definition list](/cli/azure/role/definition#az-role-definition-list). W poniższych przykładach wymieniono wszystkie role niestandardowe w bieżącej subskrypcji.

```azurecli
az role definition list --custom-role-only true --output json | jq '.[] | {"roleName":.roleName, "roleType":.roleType}'
```

```azurecli
az role definition list --output json | jq '.[] | if .roleType == "CustomRole" then {"roleName":.roleName, "roleType":.roleType} else empty end'
```

```Output
{
  "roleName": "My Management Contributor",
  "type": "CustomRole"
}
{
  "roleName": "My Service Reader Role",
  "type": "CustomRole"
}
{
  "roleName": "Virtual Machine Operator",
  "type": "CustomRole"
}

...
```

## <a name="list-a-custom-role-definition"></a>Utwórz listę niestandardowych definicji roli

Aby wyświetlić listę niestandardowych definicji roli, użyj [AZ role Definition list](/cli/azure/role/definition#az-role-definition-list). Jest to samo polecenie, którego można użyć w przypadku roli wbudowanej.

```azurecli
az role definition list --name <role_name>
```

Poniższy przykład zawiera listę definicji roli *operatora maszyny wirtualnej* :

```azurecli
az role definition list --name "Virtual Machine Operator"
```

```Output
[
  {
    "assignableScopes": [
      "/subscriptions/11111111-1111-1111-1111-111111111111"
    ],
    "description": "Can monitor and restart virtual machines.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/00000000-0000-0000-0000-000000000000",
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

Poniższy przykład zawiera listę tylko działań roli *operatora maszyny wirtualnej* :

```azurecli
az role definition list --name "Virtual Machine Operator" --output json | jq '.[] | .permissions[0].actions'
```

```Output
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
```

## <a name="create-a-custom-role"></a>Tworzenie roli niestandardowej

Aby utworzyć rolę niestandardową, użyj [AZ role Definition Create](/cli/azure/role/definition#az-role-definition-create). Definicja roli może być opisem JSON lub ścieżką do pliku zawierającego opis JSON.

```azurecli
az role definition create --role-definition <role_definition>
```

Poniższy przykład tworzy rolę niestandardową o nazwie *operator maszyny wirtualnej*. Ta rola niestandardowa przypisuje dostęp do wszystkich operacji odczytu dostawcy zasobów *Microsoft. COMPUTE*, *Microsoft. Storage*i *Microsoft. Network* oraz przypisuje do uruchamiania, ponownego uruchamiania i monitorowania maszyn wirtualnych. Ta rola niestandardowa może być używana w dwóch subskrypcjach. W tym przykładzie plik JSON jest wykorzystywany jako dane wejściowe.

vmoperator. JSON

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
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition create --role-definition ~/roles/vmoperator.json
```

## <a name="update-a-custom-role"></a>Aktualizacja roli niestandardowej

Aby zaktualizować rolę niestandardową, należy najpierw użyć [AZ role Definition list](/cli/azure/role/definition#az-role-definition-list) do pobrania definicji roli. Następnie wprowadź żądane zmiany w definicji roli. Na koniec Użyj [AZ role Definition Update](/cli/azure/role/definition#az-role-definition-update) w celu zapisania zaktualizowanej definicji roli.

```azurecli
az role definition update --role-definition <role_definition>
```

Poniższy przykład dodaje do *programu Microsoft. Insights/diagnosticSettings/* Operations to `Actions` i dodaje grupę zarządzania do `AssignableScopes` roli niestandardowej *operatora maszyny wirtualnej* . Dodawanie grupy zarządzania do `AssignableScopes` programu jest obecnie w wersji zapoznawczej.

vmoperator. JSON

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
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333",
    "/providers/Microsoft.Management/managementGroups/marketing-group"
  ]
}
```

```azurecli
az role definition update --role-definition ~/roles/vmoperator.json
```

## <a name="delete-a-custom-role"></a>Usuwanie roli niestandardowej

Aby usunąć rolę niestandardową, użyj [AZ role Definition Delete](/cli/azure/role/definition#az-role-definition-delete). Aby określić rolę do usunięcia, użyj nazwy roli lub identyfikatora roli. Aby określić identyfikator roli, użyj [AZ role Definition list](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition delete --name <role_name or role_id>
```

Poniższy przykład usuwa rolę niestandardową *operatora maszyny wirtualnej* .

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>Następne kroki

- [Samouczek: tworzenie roli niestandardowej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](tutorial-custom-role-cli.md)
- [Role niestandardowe platformy Azure](custom-roles.md)
- [Operacje dostawcy zasobów Azure Resource Manager](resource-provider-operations.md)