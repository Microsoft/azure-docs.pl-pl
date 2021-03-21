---
title: Wyświetlanie listy przypisań ról platformy Azure przy użyciu interfejsu API REST — Azure RBAC
description: Dowiedz się, jak określić zasoby, których użytkownicy, grupy, nazwy główne usług lub tożsamości zarządzane mają dostęp do korzystania z interfejsu API REST i kontroli dostępu opartej na rolach platformy Azure (RBAC).
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.topic: how-to
ms.date: 02/27/2021
ms.author: rolyon
ms.openlocfilehash: 9780902a1c5f4a711e1abffa6b508c28efe269ac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101735884"
---
# <a name="list-azure-role-assignments-using-the-rest-api"></a>Wyświetlanie listy przypisań ról platformy Azure przy użyciu interfejsu API REST

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control/definition-list.md)] W tym artykule opisano sposób wyświetlania listy przypisań ról przy użyciu interfejsu API REST.

> [!NOTE]
> Jeśli organizacja ma funkcje zarządzania, które są używane przez usługę zarządzania [zasobami delegowanymi przez platformę Azure](../lighthouse/concepts/azure-delegated-resource-management.md), w tym miejscu nie będą wyświetlane przypisania ról autoryzowane przez tego dostawcę usług.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="list-role-assignments"></a>Lista przypisań ról

W celu wyświetlenia listy uprawnień w usłudze Azure RBAC należy wyświetlić listę przypisań ról. Aby wyświetlić listę przypisań ról, użyj jednego z [przypisań ról —](/rest/api/authorization/roleassignments/list) interfejsów API REST. Aby uściślić wyniki, należy określić zakres i opcjonalny filtr.

1. Rozpocznij od następującego żądania:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. W identyfikatorze URI Zastąp wartość *{SCOPE}* zakresem, dla którego chcesz wyświetlić listę przypisań ról.

    > [!div class="mx-tableFixed"]
    > | Zakres | Typ |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupa zarządzania |
    > | `subscriptions/{subscriptionId1}` | Subskrypcja |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Zasób |

    W poprzednim przykładzie firma Microsoft. Web jest dostawcą zasobów, który odwołuje się do wystąpienia App Service. Analogicznie, można użyć innych dostawców zasobów i określić zakres. Aby uzyskać więcej informacji, zobacz [dostawcy zasobów platformy Azure i typy](../azure-resource-manager/management/resource-providers-and-types.md) oraz obsługiwane operacje związane z [dostawcą zasobów platformy Azure](resource-provider-operations.md).  
     
1. Zamień *filtr {Filter}* na warunek, który ma zostać zastosowany do filtrowania listy przypisywania ról.

    > [!div class="mx-tableFixed"]
    > | Filtr | Opis |
    > | --- | --- |
    > | `$filter=atScope()` | Wyświetla listę przypisań ról tylko dla określonego zakresu, bez uwzględniania przypisań ról w podzakresach. |
    > | `$filter=assignedTo('{objectId}')` | Wyświetla listę przypisań ról dla określonego użytkownika lub nazwy głównej usługi.<br/>Jeśli użytkownik jest członkiem grupy z przypisaniem roli, zostanie ona również wyświetlona. Ten filtr jest przechodni dla grup, co oznacza, że jeśli użytkownik jest członkiem grupy, a grupa jest członkiem innej grupy z przypisaniem roli, to przypisanie roli jest również wymienione.<br/>Ten filtr akceptuje tylko identyfikator obiektu dla użytkownika lub nazwy głównej usługi. Nie można przekazać identyfikatora obiektu dla grupy. |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | Wyświetla listę przypisań ról dla określonego użytkownika lub nazwy głównej usługi i w określonym zakresie. |
    > | `$filter=principalId+eq+'{objectId}'` | Wyświetla listę przypisań ról dla określonego użytkownika, grupy lub nazwy głównej usługi. |

Poniższe żądanie wyświetla listę wszystkich przypisań ról dla określonego użytkownika w zakresie subskrypcji:

```http
GET https://management.azure.com/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()+and+assignedTo('{objectId1}')
```

Poniżej przedstawiono przykład danych wyjściowych:

```json
{
    "value": [
        {
            "properties": {
                "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
                "principalId": "{objectId1}",
                "scope": "/subscriptions/{subscriptionId1}",
                "createdOn": "2019-01-15T21:08:45.4904312Z",
                "updatedOn": "2019-01-15T21:08:45.4904312Z",
                "createdBy": "{createdByObjectId1}",
                "updatedBy": "{updatedByObjectId1}"
            },
            "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId1}",
            "type": "Microsoft.Authorization/roleAssignments",
            "name": "{roleAssignmentId1}"
        }
    ]
}
```

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie ról platformy Azure przy użyciu interfejsu API REST](role-assignments-rest.md)
- [Dokumentacja interfejsu API REST platformy Azure](/rest/api/azure/)
