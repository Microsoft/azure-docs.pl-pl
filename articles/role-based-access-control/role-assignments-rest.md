---
title: Przypisywanie ról platformy Azure przy użyciu interfejsu API REST — Azure RBAC
description: Dowiedz się, jak udzielić dostępu do zasobów platformy Azure dla użytkowników, grup, nazw głównych usług lub tożsamości zarządzanych przy użyciu interfejsu API REST i kontroli dostępu opartej na rolach (RBAC) platformy Azure.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: how-to
ms.date: 02/15/2021
ms.author: rolyon
ms.openlocfilehash: d012173adb5e238282e107b832ed9c6895237e48
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100556080"
---
# <a name="assign-azure-roles-using-the-rest-api"></a>Przypisywanie ról platformy Azure przy użyciu interfejsu API REST

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] W tym artykule opisano sposób przypisywania ról przy użyciu interfejsu API REST.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="assign-an-azure-role"></a>Przypisywanie roli platformy Azure

Aby przypisać rolę, użyj [przypisań ról — tworzenie](/rest/api/authorization/roleassignments/create) interfejsu API REST i określanie podmiotu zabezpieczeń, definicji roli i zakresu. Aby wywołać ten interfejs API, musisz mieć dostęp do tej `Microsoft.Authorization/roleAssignments/write` operacji. W przypadku ról wbudowanych dostęp do tej operacji mają tylko [Administratorzy dostępu](built-in-roles.md#user-access-administrator) [właściciela](built-in-roles.md#owner) i użytkownika.

1. Aby uzyskać identyfikator definicji roli, która ma zostać przypisana, użyj sekcji [definicje ról-list](/rest/api/authorization/roledefinitions/list) API REST lub zobacz [wbudowane role](built-in-roles.md) .

1. Użyj narzędzia GUID, aby wygenerować unikatowy identyfikator, który będzie używany dla identyfikatora przypisania roli. Identyfikator ma format: `00000000-0000-0000-0000-000000000000`

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

1. W identyfikatorze URI Zastąp wartość *{SCOPE}* zakresem przypisania roli.

    > [!div class="mx-tableFixed"]
    > | Zakres | Typ |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupa zarządzania |
    > | `subscriptions/{subscriptionId1}` | Subskrypcja |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Zasób |

    W poprzednim przykładzie firma Microsoft. Web jest dostawcą zasobów, który odwołuje się do wystąpienia App Service. Analogicznie, można użyć innych dostawców zasobów i określić zakres. Aby uzyskać więcej informacji, zobacz [dostawcy zasobów platformy Azure i typy](../azure-resource-manager/management/resource-providers-and-types.md) oraz obsługiwane operacje związane z [dostawcą zasobów platformy Azure](resource-provider-operations.md).  

1. Zastąp ciąg *{roleAssignmentId}* identyfikatorem GUID przypisania roli.

1. W treści żądania Zastąp wartość *{SCOPE}* zakresem przypisania roli.

    > [!div class="mx-tableFixed"]
    > | Zakres | Typ |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupa zarządzania |
    > | `subscriptions/{subscriptionId1}` | Subskrypcja |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Zasób |

1. Zastąp ciąg *{zduplikowanych}* identyfikatorem definicji roli.

1. Zastąp ciąg *{principalId}* identyfikatorem obiektu użytkownika, grupy lub jednostki usługi, do której zostanie przypisana rola.

Poniższe żądanie i treść przypisuje rolę [czytnika kopii zapasowych](built-in-roles.md#backup-reader) użytkownikowi w zakresie subskrypcji:

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

Poniżej przedstawiono przykład danych wyjściowych:

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

## <a name="next-steps"></a>Następne kroki

- [Wyświetlanie listy przypisań ról platformy Azure przy użyciu interfejsu API REST](role-assignments-list-rest.md)
- [Deploy resources with Resource Manager templates and Resource Manager REST API (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i interfejsu API REST usługi Resource Manager)](../azure-resource-manager/templates/deploy-rest.md)
- [Dokumentacja interfejsu API REST platformy Azure](/rest/api/azure/)
- [Tworzenie lub aktualizowanie ról niestandardowych platformy Azure przy użyciu interfejsu API REST](custom-roles-rest.md)
