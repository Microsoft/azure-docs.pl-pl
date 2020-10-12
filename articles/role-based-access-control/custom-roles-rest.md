---
title: Tworzenie lub aktualizowanie ról niestandardowych platformy Azure przy użyciu interfejsu API REST — Azure RBAC
description: Dowiedz się, jak wyświetlać, tworzyć, aktualizować i usuwać role niestandardowe platformy Azure przy użyciu interfejsu API REST i kontroli dostępu opartej na rolach (Azure RBAC).
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: how-to
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b459f44308827308c28687db3c3fc33df470ea8d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84790193"
---
# <a name="create-or-update-azure-custom-roles-using-the-rest-api"></a>Tworzenie lub aktualizowanie ról niestandardowych platformy Azure przy użyciu interfejsu API REST

> [!IMPORTANT]
> Dodawanie grupy zarządzania do programu `AssignableScopes` jest obecnie w wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Jeśli [wbudowane role platformy Azure](built-in-roles.md) nie są zgodne z konkretnymi potrzebami organizacji, możesz utworzyć własne role niestandardowe. W tym artykule opisano sposób wyświetlania, tworzenia, aktualizowania lub usuwania ról niestandardowych przy użyciu interfejsu API REST.

## <a name="list-custom-roles"></a>Wyświetlanie ról niestandardowych

Aby wyświetlić listę wszystkich ról niestandardowych w katalogu, należy użyć interfejsu API REST [— lista definicji ról](/rest/api/authorization/roledefinitions/list) .

1. Rozpocznij od następującego żądania:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Zastąp ciąg *{Filter}* typem roli.

    > [!div class="mx-tableFixed"]
    > | Filtrowanie | Opis |
    > | --- | --- |
    > | `$filter=type+eq+'CustomRole'` | Filtr oparty na typie CustomRole |

## <a name="list-custom-roles-at-a-scope"></a>Wyświetlanie listy ról niestandardowych w zakresie

Aby wyświetlić listę ról niestandardowych w zakresie, użyj interfejsu API REST z [listą ról](/rest/api/authorization/roledefinitions/list) .

1. Rozpocznij od następującego żądania:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. W identyfikatorze URI Zastąp wartość *{SCOPE}* zakresem, dla którego chcesz wyświetlić listę ról.

    > [!div class="mx-tableFixed"]
    > | Zakres | Type |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subskrypcja |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Grupa zasobów |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Zasób |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupa zarządzania |

1. Zastąp ciąg *{Filter}* typem roli.

    > [!div class="mx-tableFixed"]
    > | Filtrowanie | Opis |
    > | --- | --- |
    > | `$filter=type+eq+'CustomRole'` | Filtr oparty na typie CustomRole |

## <a name="list-a-custom-role-definition-by-name"></a>Utwórz listę niestandardowych definicji ról według nazwy

Aby uzyskać informacje o roli niestandardowej według nazwy wyświetlanej, użyj [definicji ról — Pobierz](/rest/api/authorization/roledefinitions/get) interfejs API REST.

1. Rozpocznij od następującego żądania:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. W identyfikatorze URI Zastąp wartość *{SCOPE}* zakresem, dla którego chcesz wyświetlić listę ról.

    > [!div class="mx-tableFixed"]
    > | Zakres | Type |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subskrypcja |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Grupa zasobów |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Zasób |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupa zarządzania |

1. Zastąp ciąg *{Filter}* nazwą wyświetlaną roli.

    > [!div class="mx-tableFixed"]
    > | Filtrowanie | Opis |
    > | --- | --- |
    > | `$filter=roleName+eq+'{roleDisplayName}'` | Użyj zakodowanego w adresie URL postaci dokładnej nazwy wyświetlanej roli. Na przykład, `$filter=roleName+eq+'Virtual%20Machine%20Contributor'` |

## <a name="list-a-custom-role-definition-by-id"></a>Utwórz listę niestandardowych definicji ról według identyfikatora

Aby uzyskać informacje o roli niestandardowej przy użyciu unikatowego identyfikatora, użyj [definicji ról — Pobierz](/rest/api/authorization/roledefinitions/get) interfejs API REST.

1. Użyj interfejsu API REST z [listą ról](/rest/api/authorization/roledefinitions/list) , aby uzyskać identyfikator GUID roli.

1. Rozpocznij od następującego żądania:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. W identyfikatorze URI Zastąp wartość *{SCOPE}* zakresem, dla którego chcesz wyświetlić listę ról.

    > [!div class="mx-tableFixed"]
    > | Zakres | Type |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subskrypcja |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Grupa zasobów |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Zasób |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupa zarządzania |

1. Zastąp ciąg *{zduplikowanych}* identyfikatorem GUID definicji roli.

## <a name="create-a-custom-role"></a>Tworzenie roli niestandardowej

Aby utworzyć rolę niestandardową, użyj [definicji roli — interfejsu API REST — Tworzenie lub aktualizowanie](/rest/api/authorization/roledefinitions/createorupdate) . Aby wywołać ten interfejs API, należy zalogować się przy użyciu użytkownika, do którego przypisano rolę mającą `Microsoft.Authorization/roleDefinitions/write` uprawnienia do wszystkich `assignableScopes` . W przypadku ról wbudowanych tylko [właściciel](built-in-roles.md#owner) i [administrator dostępu użytkowników](built-in-roles.md#user-access-administrator) zawierają to uprawnienie.

1. Przejrzyj listę [operacji dostawcy zasobów](resource-provider-operations.md) dostępnych do utworzenia uprawnień dla roli niestandardowej.

1. Użyj narzędzia GUID, aby wygenerować unikatowy identyfikator, który będzie używany dla niestandardowego identyfikatora roli. Identyfikator ma format: `00000000-0000-0000-0000-000000000000`

1. Rozpocznij od następującego żądania i treści:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId1}",
          "/subscriptions/{subscriptionId2}",
          "/subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}",
          "/subscriptions/{subscriptionId2}/resourceGroups/{resourceGroup2}",
          "/providers/Microsoft.Management/managementGroups/{groupId1}"
        ]
      }
    }
    ```

1. W identyfikatorze URI Zastąp element *{SCOPE}* pierwszą `assignableScopes` rolą niestandardową.

    > [!div class="mx-tableFixed"]
    > | Zakres | Type |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subskrypcja |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Grupa zasobów |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupa zarządzania |

1. Zastąp element *{zduplikowanych}* identyfikatorem GUID roli niestandardowej.

1. W treści żądania Zastąp ciąg *{zduplikowanych}* identyfikatorem GUID.

1. Jeśli `assignableScopes` jest subskrypcją lub grupą zasobów, Zastąp wystąpienia *{Subscription}* lub *{resourceName}* identyfikatorami.

1. Jeśli `assignableScopes` jest grupą zarządzania, Zastąp wystąpienie *{GroupID}* identyfikatorem grupy zarządzania. Dodawanie grupy zarządzania do programu `assignableScopes` jest obecnie w wersji zapoznawczej.

1. We `actions` Właściwości Dodaj operacje, które mogą być wykonywane przez rolę.

1. We `notActions` Właściwości Dodaj operacje, które są wykluczone z dozwolonej `actions` .

1. We `roleName` `description` właściwościach i Określ unikatową nazwę roli i opis. Aby uzyskać więcej informacji na temat właściwości, zobacz [role niestandardowe platformy Azure](custom-roles.md).

    Poniżej przedstawiono przykład treści żądania:

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
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
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000",
          "/providers/Microsoft.Management/managementGroups/marketing-group"
        ]
      }
    }
    ```

## <a name="update-a-custom-role"></a>Aktualizacja roli niestandardowej

Aby zaktualizować rolę niestandardową, użyj [definicji ról — interfejsu API REST — Tworzenie lub aktualizowanie](/rest/api/authorization/roledefinitions/createorupdate) . Aby wywołać ten interfejs API, należy zalogować się przy użyciu użytkownika, do którego przypisano rolę mającą `Microsoft.Authorization/roleDefinitions/write` uprawnienia do wszystkich `assignableScopes` . W przypadku ról wbudowanych tylko [właściciel](built-in-roles.md#owner) i [administrator dostępu użytkowników](built-in-roles.md#user-access-administrator) zawierają to uprawnienie.

1. Aby uzyskać informacje o roli niestandardowej, użyj funkcji [definicje ról — lista](/rest/api/authorization/roledefinitions/list) lub [definicje ról — Pobierz](/rest/api/authorization/roledefinitions/get) interfejs API REST. Aby uzyskać więcej informacji, zobacz sekcję wcześniejsze [listy ról niestandardowych](#list-custom-roles) .

1. Rozpocznij od następującego żądania:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. W identyfikatorze URI Zastąp element *{SCOPE}* pierwszą `assignableScopes` rolą niestandardową.

    > [!div class="mx-tableFixed"]
    > | Zakres | Type |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subskrypcja |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Grupa zasobów |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupa zarządzania |

1. Zastąp element *{zduplikowanych}* identyfikatorem GUID roli niestandardowej.

1. Na podstawie informacji o roli niestandardowej Utwórz treść żądania o następującym formacie:

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId1}",
          "/subscriptions/{subscriptionId2}",
          "/subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}",
          "/subscriptions/{subscriptionId2}/resourceGroups/{resourceGroup2}",
          "/providers/Microsoft.Management/managementGroups/{groupId1}"
        ]
      }
    }
    ```

1. Zaktualizuj treść żądania, podając zmiany, które chcesz wprowadzić do roli niestandardowej.

    Poniżej przedstawiono przykład treści żądania z dodaną nową akcją ustawień diagnostycznych:

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
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
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000",
          "/providers/Microsoft.Management/managementGroups/marketing-group"
        ]
      }
    }
    ```

## <a name="delete-a-custom-role"></a>Usuwanie roli niestandardowej

Aby usunąć rolę niestandardową, użyj [definicji roli — Usuń](/rest/api/authorization/roledefinitions/delete) interfejs API REST. Aby wywołać ten interfejs API, należy zalogować się przy użyciu użytkownika, do którego przypisano rolę mającą `Microsoft.Authorization/roleDefinitions/delete` uprawnienia do wszystkich `assignableScopes` . W przypadku ról wbudowanych tylko [właściciel](built-in-roles.md#owner) i [administrator dostępu użytkowników](built-in-roles.md#user-access-administrator) zawierają to uprawnienie.

1. Aby uzyskać identyfikator GUID roli niestandardowej, należy użyć definicji [ról — listy](/rest/api/authorization/roledefinitions/list) lub [definicji ról — Uzyskaj](/rest/api/authorization/roledefinitions/get) interfejs API REST. Aby uzyskać więcej informacji, zobacz sekcję wcześniejsze [listy ról niestandardowych](#list-custom-roles) .

1. Rozpocznij od następującego żądania:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. W identyfikatorze URI Zastąp element *{SCOPE}* zakresem, dla którego chcesz usunąć rolę niestandardową.

    > [!div class="mx-tableFixed"]
    > | Zakres | Type |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subskrypcja |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Grupa zasobów |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupa zarządzania |

1. Zastąp element *{zduplikowanych}* identyfikatorem GUID roli niestandardowej.

## <a name="next-steps"></a>Następne kroki

- [Role niestandardowe platformy Azure](custom-roles.md)
- [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu interfejsu API REST](role-assignments-rest.md)
- [Dokumentacja interfejsu API REST platformy Azure](/rest/api/azure/)
