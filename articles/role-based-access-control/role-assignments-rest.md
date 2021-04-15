---
title: Przypisywanie ról platformy Azure przy użyciu interfejsu API REST — Azure RBAC
description: Dowiedz się, jak udzielić dostępu do zasobów platformy Azure użytkownikom, grupom, jednostkom usługi lub tożsamościom zarządzanym przy użyciu interfejsu API REST i kontroli dostępu na podstawie ról (RBAC) platformy Azure.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: how-to
ms.date: 04/06/2021
ms.author: rolyon
ms.openlocfilehash: 3baf44a4240b23b41ce2e80dc22dbda4c7d0672a
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363720"
---
# <a name="assign-azure-roles-using-the-rest-api"></a>Przypisywanie ról platformy Azure przy użyciu interfejsu API REST

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] W tym artykule opisano sposób przypisywania ról przy użyciu interfejsu API REST.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="assign-an-azure-role"></a>Przypisywanie roli platformy Azure

Aby przypisać rolę, użyj przypisania [ról](/rest/api/authorization/roleassignments/create) — utwórz interfejs API REST i określ podmiot zabezpieczeń, definicję roli i zakres. Aby wywołać ten interfejs API, musisz mieć dostęp do `Microsoft.Authorization/roleAssignments/write` operacji. Z wbudowanych ról tylko [](built-in-roles.md#owner) właściciel i administrator dostępu użytkowników [mają](built-in-roles.md#user-access-administrator) dostęp do tej operacji.

1. Użyj definicji [ról — wyświetl listę interfejsu](/rest/api/authorization/roledefinitions/list) API REST lub zobacz [Role](built-in-roles.md) wbudowane, aby uzyskać identyfikator definicji roli, którą chcesz przypisać.

1. Użyj narzędzia identyfikatora GUID, aby wygenerować unikatowy identyfikator, który będzie używany dla identyfikatora przypisania roli. Identyfikator ma format: `00000000-0000-0000-0000-000000000000`

1. Rozpocznij od następującego żądania i treści:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2015-07-01
    ```

    ```json
    {
      "properties": {
        "roleDefinitionId": "/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}"
      }
    }
    ```

1. W ramach identyfikatorów URI zastąp *element {scope}* zakresem przypisania roli.

    > [!div class="mx-tableFixed"]
    > | Zakres | Typ |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupa zarządzania |
    > | `subscriptions/{subscriptionId1}` | Subskrypcja |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Zasób |

    W poprzednim przykładzie microsoft.web to dostawca zasobów, który odwołuje się do App Service wystąpienia. Podobnie można użyć innych dostawców zasobów i określić zakres. Aby uzyskać więcej informacji, zobacz Azure Resource providers and types and supported Azure resource provider operations [(Dostawcy zasobów](../azure-resource-manager/management/resource-providers-and-types.md) i typy zasobów platformy Azure oraz obsługiwane [operacje dostawcy zasobów platformy Azure).](resource-provider-operations.md)  

1. Zastąp *element {roleAssignmentId}* identyfikatorem GUID przypisania roli.

1. W treści żądania zastąp *element {scope}* zakresem przypisania roli.

    > [!div class="mx-tableFixed"]
    > | Zakres | Typ |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupa zarządzania |
    > | `subscriptions/{subscriptionId1}` | Subskrypcja |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Zasób |

1. Zastąp *element {roleDefinitionId}* identyfikatorem definicji roli.

1. Zastąp *element {principalId}* identyfikatorem obiektu użytkownika, grupy lub jednostki usługi, do których zostanie przypisana rola.

Następujące żądanie i treść przypisze użytkownikowi [rolę](built-in-roles.md#backup-reader) Czytelnik kopii zapasowej w zakresie subskrypcji:

```http
PUT https://management.azure.com/subscriptions/{subscriptionId1}/providers/microsoft.authorization/roleassignments/{roleAssignmentId1}?api-version=2015-07-01
```

```json
{
  "properties": {
    "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
    "principalId": "{objectId1}"
  }
}
```

Poniżej przedstawiono przykładowe dane wyjściowe:

```json
{
    "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
        "principalId": "{objectId1}",
        "scope": "/subscriptions/{subscriptionId1}",
        "createdOn": "2020-05-06T23:55:23.7679147Z",
        "updatedOn": "2020-05-06T23:55:23.7679147Z",
        "createdBy": null,
        "updatedBy": "{updatedByObjectId1}"
    },
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId1}",
    "type": "Microsoft.Authorization/roleAssignments",
    "name": "{roleAssignmentId1}"
}
```

### <a name="new-service-principal"></a>Nowa jednostka usługi

Jeśli utworzysz nową jednostkę usługi i natychmiast spróbujesz przypisać rolę do tej jednostki usługi, w niektórych przypadkach przypisanie roli może się nie powieść. Jeśli na przykład utworzysz nową tożsamość zarządzaną, a następnie spróbujesz przypisać rolę do tej jednostki usługi, przypisanie roli może się nie powieść. Przyczyną tego niepowodzenia jest prawdopodobnie opóźnienie replikacji. Jednostkę usługi tworzy się w jednym regionie. Jednak przypisanie roli może wystąpić w innym regionie, który nie zreplikował jeszcze jednostki usługi.

Aby rozwiązać ten scenariusz, użyj przypisania [ról — utwórz](/rest/api/authorization/roleassignments/create) interfejs API REST i ustaw właściwość na wartość `principalType` `ServicePrincipal` . Należy również ustawić wartość `apiVersion` `2018-09-01-preview` lub później.

```http
PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2018-09-01-preview
```

```json
{
  "properties": {
    "roleDefinitionId": "/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
    "principalId": "{principalId}",
    "principalType": "ServicePrincipal"
  }
}
```

## <a name="next-steps"></a>Następne kroki

- [Lista przypisań ról platformy Azure przy użyciu interfejsu API REST](role-assignments-list-rest.md)
- [Deploy resources with Resource Manager templates and Resource Manager REST API (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i interfejsu API REST usługi Resource Manager)](../azure-resource-manager/templates/deploy-rest.md)
- [Dokumentacja interfejsu API REST platformy Azure](/rest/api/azure/)
- [Tworzenie lub aktualizowanie ról niestandardowych platformy Azure przy użyciu interfejsu API REST](custom-roles-rest.md)
