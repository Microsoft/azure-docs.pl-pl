---
title: Podnoszenie poziomu dostępu w celu zarządzania wszystkimi subskrypcjami platformy Azure i grupami zarządzania
description: Opisuje sposób podniesienia dostępu dla administratora globalnego w celu zarządzania wszystkimi subskrypcjami i grupami zarządzania w programie Azure Active Directory użyciu interfejsu Azure Portal API REST.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 06/09/2020
ms.author: rolyon
ms.openlocfilehash: 37d50c030a2b426cb3e9af57afb899b7fab68388
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778477"
---
# <a name="elevate-access-to-manage-all-azure-subscriptions-and-management-groups"></a>Podnoszenie poziomu dostępu w celu zarządzania wszystkimi subskrypcjami platformy Azure i grupami zarządzania

Jako administrator globalny w Azure Active Directory (Azure AD), możesz nie mieć dostępu do wszystkich subskrypcji i grup zarządzania w katalogu. W tym artykule opisano sposoby podniesienia dostępu do wszystkich subskrypcji i grup zarządzania.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="why-would-you-need-to-elevate-your-access"></a>Dlaczego należy podnieść swój dostęp?

Jeśli jesteś administratorem globalnym, być może chcesz wykonać następujące czynności:

- Odzyskaj dostęp do subskrypcji platformy Azure lub grupy zarządzania, gdy użytkownik utraci dostęp
- Przyznać innemu użytkownikowi lub sobie samemu dostęp do subskrypcji platformy Azure lub grupy zarządzania
- Wyświetlanie wszystkich subskrypcji platformy Azure lub grup zarządzania w organizacji
- Zezwalanie aplikacji automatyzacji (takiej jak aplikacja do fakturowania lub inspekcji) na dostęp do wszystkich subskrypcji platformy Azure lub grup zarządzania

## <a name="how-does-elevated-access-work"></a>Jak działa podwyższony poziom dostępu?

Zasoby usługi Azure AD i platformy Azure są zabezpieczone niezależnie od siebie. Oznacza to, że przypisania ról usługi Azure AD nie przyznają dostępu do zasobów platformy Azure, a przypisania ról platformy Azure nie przyznają dostępu do usługi Azure AD. Jeśli jednak jesteś administratorem globalnym [w](../active-directory/roles/permissions-reference.md#global-administrator) usłudze Azure AD, możesz przypisać sobie dostęp do wszystkich subskrypcji platformy Azure i grup zarządzania w katalogu. Użyj tej funkcji, jeśli nie masz dostępu do zasobów subskrypcji platformy Azure, takich jak maszyny wirtualne lub konta magazynu, i chcesz użyć uprawnień administratora globalnego, aby uzyskać dostęp do tych zasobów.

Po podnieśeniu uprawnień dostępu będziesz mieć przypisaną rolę [administratora](built-in-roles.md#user-access-administrator) dostępu użytkowników na platformie Azure w zakresie głównym ( `/` ).Dzięki temu można wyświetlić wszystkie zasoby i przypisać dostęp w dowolnej subskrypcji lub grupie zarządzania w katalogu. Przypisania ról administratora dostępu użytkowników można usunąć przy użyciu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST.

Ten podwyższony poziom dostępu należy usunąć po wymuszeniem zmian, które należy wprowadzić w zakresie głównym.

![Podnoszenie poziomu dostępu](./media/elevate-access-global-admin/elevate-access.png)

## <a name="azure-portal"></a>Azure Portal

### <a name="elevate-access-for-a-global-administrator"></a>Podnieś uprawnienia dostępu administratora globalnego

Wykonaj następujące kroki, aby podnieść uprawnienia dostępu administratora globalnego przy użyciu Azure Portal.

1. Zaloguj się do [centrum Azure Portal](https://portal.azure.com) lub Azure Active Directory [administratora jako](https://aad.portal.azure.com) administrator globalny.

    Jeśli używasz usługi Azure AD Privileged Identity Management, [aktywuj przypisanie roli administratora globalnego.](../active-directory/privileged-identity-management/pim-how-to-activate-role.md)

1. Otwórz **Azure Active Directory**.

1. W obszarze **Zarządzanie** wybierz pozycję **Właściwości**.

   ![Wybieranie właściwości dla właściwości Azure Active Directory — zrzut ekranu](./media/elevate-access-global-admin/azure-active-directory-properties.png)

1. W **obszarze Zarządzanie dostępem do zasobów platformy Azure** ustaw przełącznik tak. 

   ![Zarządzanie dostępem do zasobów platformy Azure — zrzut ekranu](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   Po skonfigurowaniu przełącznika na wartość **Tak** zostanie przypisana rola administratora dostępu użytkowników w kontroli dostępu na poziomie roli platformy Azure w zakresie głównym (/). Daje to uprawnienia do przypisywania ról we wszystkich subskrypcjach platformy Azure i grupach zarządzania skojarzonych z tym katalogiem usługi Azure AD. Ten przełącznik jest dostępny tylko dla użytkowników, którym przypisano rolę administratora globalnego w usłudze Azure AD.

   Po skonfigurowaniu przełącznika na wartość **Nie** rola administratora dostępu użytkowników w kontroli dostępu na platformie Azure zostanie usunięta z konta użytkownika. Nie można już przypisywać ról we wszystkich subskrypcjach platformy Azure i grupach zarządzania skojarzonych z tym katalogiem usługi Azure AD. Możesz wyświetlać tylko subskrypcje platformy Azure i grupy zarządzania, do których masz przyznany dostęp, i zarządzać nimi.

    > [!NOTE]
    > Jeśli używasz usługi [Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md), dezaktywowanie przypisania roli nie zmienia przełącznika Zarządzanie dostępem dla zasobów platformy **Azure** na **nie**. Aby zachować dostęp z najmniejszymi uprawnieniami, zalecamy ustawienie tego przełącznika na wartość **Nie** przed dezaktywowanie przypisania roli.
    
1. Kliknij **przycisk Zapisz,** aby zapisać ustawienie.

   To ustawienie nie jest właściwością globalną i ma zastosowanie tylko do aktualnie zalogowago użytkownika. Nie można podnieść uprawnień dostępu dla wszystkich członków roli administratora globalnego.

1. Wyloguj się i zaloguj się ponownie, aby odświeżyć dostęp.

    Teraz powinien mieć dostęp do wszystkich subskrypcji i grup zarządzania w katalogu. Podczas wyświetlania okienka Kontrola dostępu (IAM) zauważysz, że przypisano Ci rolę administratora dostępu użytkowników w zakresie głównym.

   ![Przypisania ról subskrypcji z zakresem głównym — zrzut ekranu](./media/elevate-access-global-admin/iam-root.png)

1. Wprowadzaj zmiany, które należy wprowadzić przy podwyższonym poziomie dostępu.

    Aby uzyskać informacje na temat przypisywania ról, zobacz [Przypisywanie ról platformy Azure przy użyciu Azure Portal](role-assignments-portal.md). Jeśli używasz usługi azure Privileged Identity Management, zobacz Discover Azure resources to manage or Assign Azure resource roles (Odnajdywanie zasobów platformy Azure w celu zarządzania [rolami](../active-directory/privileged-identity-management/pim-resource-roles-discover-resources.md) zasobów platformy Azure lub [Przypisywanie ról zasobów platformy Azure).](../active-directory/privileged-identity-management/pim-resource-roles-assign-roles.md)

1. Wykonaj kroki opisane w poniższej sekcji, aby usunąć dostęp z podwyższonym poziomem uprawnień.

### <a name="remove-elevated-access"></a>Usuwanie dostępu z podwyższonym poziomem uprawnień

Aby usunąć przypisanie roli Administrator dostępu użytkowników w zakresie głównym ( `/` ), wykonaj następujące kroki.

1. Zaloguj się jako ten sam użytkownik, który został użyty do podnieść uprawnienia dostępu.

1. Na liście nawigacji kliknij pozycję **Azure Active Directory** a następnie kliknij pozycję **Właściwości.**

1. Ustaw przełącznik **Zarządzanie dostępem dla zasobów platformy Azure** z powrotem na wartość **Nie.** Ponieważ jest to ustawienie dla każdego użytkownika, musisz zalogować się jako ten sam użytkownik, który został użyty do uzyskania dostępu.

    Jeśli spróbujesz usunąć przypisanie roli Administrator dostępu użytkowników w okienku Kontrola dostępu (IAM), zostanie wyświetlony następujący komunikat. Aby usunąć przypisanie roli, należy ustawić  przełącznik z powrotem na wartość Nie lub użyć Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST.

    ![Usuwanie przypisań ról z zakresem głównym](./media/elevate-access-global-admin/iam-root-remove.png)

1. Wyloguj się jako administrator globalny.

    Jeśli używasz konta usługi Privileged Identity Management, dezaktywuj przypisanie roli administratora globalnego.

    > [!NOTE]
    > Jeśli używasz usługi [Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md), dezaktywowanie przypisania roli nie powoduje zmiany przełącznika Zarządzanie dostępem do zasobów platformy **Azure** na **nie.** Aby zachować dostęp z najmniejszymi uprawnieniami, zalecamy ustawienie tego przełącznika na wartość **Nie** przed dezaktywowanie przypisania roli.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

### <a name="list-role-assignment-at-root-scope-"></a>Lista przypisań ról w zakresie głównym (/)

Aby wyświetlić listę przypisania roli administratora dostępu użytkowników dla użytkownika w zakresie głównym ( ), użyj polecenia `/` [Get-AzRoleAssignment.](/powershell/module/az.resources/get-azroleassignment)

```azurepowershell
Get-AzRoleAssignment | where {$_.RoleDefinitionName -eq "User Access Administrator" `
  -and $_.SignInName -eq "<username@example.com>" -and $_.Scope -eq "/"}
```

```Example
RoleAssignmentId   : /providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111
Scope              : /
DisplayName        : username
SignInName         : username@example.com
RoleDefinitionName : User Access Administrator
RoleDefinitionId   : 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9
ObjectId           : 22222222-2222-2222-2222-222222222222
ObjectType         : User
CanDelegate        : False
```

### <a name="remove-elevated-access"></a>Usuwanie podwyższonego poziomu dostępu

Aby usunąć przypisanie roli Administrator dostępu użytkowników dla siebie lub innego użytkownika w zakresie głównym ( `/` ), wykonaj następujące kroki.

1. Zaloguj się jako użytkownik, który może usunąć podwyższony poziom dostępu. Może to być ten sam użytkownik, który został użyty do podwyższenia poziomu dostępu, lub inny administrator globalny z podwyższonym poziomem uprawnień w zakresie głównym.

1. Użyj polecenia [Remove-AzRoleAssignment,](/powershell/module/az.resources/remove-azroleassignment) aby usunąć przypisanie roli Administrator dostępu użytkowników.

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName <username@example.com> `
      -RoleDefinitionName "User Access Administrator" -Scope "/"
    ```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

### <a name="elevate-access-for-a-global-administrator"></a>Podnieś uprawnienia dostępu administratora globalnego

Aby podnieść uprawnienia dostępu administratora globalnego przy użyciu interfejsu wiersza polecenia platformy Azure, należy wykonać następujące podstawowe kroki.

1. Użyj polecenia [az rest,](/cli/azure/reference-index#az_rest) aby wywołać punkt końcowy, co daje rolę administratora dostępu użytkowników `elevateAccess` w zakresie głównym ( `/` ).

    ```azurecli
    az rest --method post --url "/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01"
    ```

1. Wprowadzaj zmiany, które należy wprowadzić w przypadku podwyższonego poziomu dostępu.

    Aby uzyskać informacje na temat przypisywania ról, zobacz [Przypisywanie ról platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.](role-assignments-cli.md)

1. Wykonaj kroki opisane w dalszej sekcji, aby usunąć dostęp z podwyższonym poziomem uprawnień.

### <a name="list-role-assignment-at-root-scope-"></a>Lista przypisań ról w zakresie głównym (/)

Aby wyświetlić listę przypisania roli administratora dostępu użytkowników dla użytkownika w zakresie głównym ( `/` ), użyj polecenia az role assignment [list.](/cli/azure/role/assignment#az_role_assignment_list)

```azurecli
az role assignment list --role "User Access Administrator" --scope "/"
```

```Example
[
  {
    "canDelegate": null,
    "id": "/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
    "name": "11111111-1111-1111-1111-111111111111",
    "principalId": "22222222-2222-2222-2222-222222222222",
    "principalName": "username@example.com",
    "principalType": "User",
    "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "roleDefinitionName": "User Access Administrator",
    "scope": "/",
    "type": "Microsoft.Authorization/roleAssignments"
  }
]

```

### <a name="remove-elevated-access"></a>Usuwanie podwyższonego poziomu dostępu

Aby usunąć przypisanie roli Administrator dostępu użytkowników dla siebie lub innego użytkownika w zakresie głównym ( `/` ), wykonaj następujące kroki.

1. Zaloguj się jako użytkownik, który może usunąć podwyższony poziom dostępu. Może to być ten sam użytkownik, który został użyty do podwyższenia poziomu dostępu, lub inny administrator globalny z podwyższonym poziomem uprawnień w zakresie głównym.

1. Użyj polecenia [az role assignment delete,](/cli/azure/role/assignment#az_role_assignment_delete) aby usunąć przypisanie roli Administrator dostępu użytkowników.

    ```azurecli
    az role assignment delete --assignee username@example.com --role "User Access Administrator" --scope "/"
    ```

## <a name="rest-api"></a>Interfejs API REST

### <a name="elevate-access-for-a-global-administrator"></a>Podnieś uprawnienia dostępu administratora globalnego

Aby podnieść uprawnienia dostępu administratora globalnego przy użyciu interfejsu API REST, należy wykonać następujące podstawowe kroki.

1. Przy użyciu interfejsu REST wywołaj funkcję , która udziela roli administratora dostępu `elevateAccess` użytkowników w zakresie głównym ( `/` ).

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. Wprowadzaj zmiany, które należy wprowadzić przy podwyższonym poziomie dostępu.

    Aby uzyskać informacje na temat przypisywania ról, zobacz [Przypisywanie ról platformy Azure przy użyciu interfejsu API REST.](role-assignments-rest.md)

1. Wykonaj kroki opisane w dalszej sekcji, aby usunąć dostęp z podwyższonym poziomem uprawnień.

### <a name="list-role-assignments-at-root-scope-"></a>Lista przypisań ról w zakresie głównym (/)

Listę wszystkich przypisań ról dla użytkownika można wyświetlić w zakresie głównym ( `/` ).

- Wywołaj [funkcję GET roleAssignments,](/rest/api/authorization/roleassignments/listforscope) gdzie to identyfikator obiektu użytkownika, którego przypisania `{objectIdOfUser}` ról chcesz pobrać.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectIdOfUser}'
   ```

### <a name="list-deny-assignments-at-root-scope-"></a>Lista przypisań odmowy w zakresie głównym (/)

Listę wszystkich przypisań odmowy dla użytkownika można wyświetlić w zakresie głównym ( `/` ).

- Wywołaj get denyAssignments gdzie jest identyfikatorem obiektu użytkownika, którego przypisania `{objectIdOfUser}` odmowy, które chcesz pobrać.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter=gdprExportPrincipalId+eq+'{objectIdOfUser}'
   ```

### <a name="remove-elevated-access"></a>Usuwanie dostępu z podwyższonym poziomem uprawnień

Po wywołaniu funkcji tworzysz dla siebie przypisanie roli, więc aby odwołać te uprawnienia, musisz usunąć przypisanie roli Administrator dostępu użytkowników dla siebie w zakresie `elevateAccess` głównym ( `/` ).

1. Wywołaj [funkcję GET roleDefinitions,](/rest/api/authorization/roledefinitions/get) gdzie równa się administratorowi dostępu użytkowników, aby określić identyfikator `roleName` nazwy roli administrator dostępu użytkowników.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter=roleName+eq+'User Access Administrator'
    ```

    ```json
    {
      "value": [
        {
          "properties": {
      "roleName": "User Access Administrator",
      "type": "BuiltInRole",
      "description": "Lets you manage user access to Azure resources.",
      "assignableScopes": [
        "/"
      ],
      "permissions": [
        {
          "actions": [
            "*/read",
            "Microsoft.Authorization/*",
            "Microsoft.Support/*"
          ],
          "notActions": []
        }
      ],
      "createdOn": "0001-01-01T08:00:00.0000000Z",
      "updatedOn": "2016-05-31T23:14:04.6964687Z",
      "createdBy": null,
      "updatedBy": null
          },
          "id": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
          "type": "Microsoft.Authorization/roleDefinitions",
          "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9"
        }
      ],
      "nextLink": null
    }
    ```

    Zapisz identyfikator z `name` parametru , w tym przypadku `18d7d88d-d35e-4fb5-a5c3-7773c20a72d9` .

1. Należy również wyświetlić przypisanie roli dla administratora katalogu w zakresie katalogu. Lista wszystkich przypisań w zakresie katalogu dla administratora katalogu, który dokonał wywołania dostępu `principalId` podnieść. Spowoduje to listę wszystkich przypisań w katalogu dla objectid.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
        
    >[!NOTE] 
    >Administrator katalogu nie powinien mieć wielu przypisań. Jeśli poprzednie zapytanie zwraca zbyt wiele przypisań, możesz również utworzyć zapytanie dotyczące wszystkich przypisań tylko na poziomie zakresu katalogu, a następnie przefiltrować wyniki: `GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
            
1. Poprzednie wywołania zwracają listę przypisań ról. Znajdź przypisanie roli, w którym znajduje się zakres, a na końcu identyfikator nazwy roli znaleziony w kroku 1, i dopasowuje `"/"` `roleDefinitionId` identyfikator `principalId` objectId administratora katalogu. 
    
    Przykładowe przypisanie roli:
    
    ```json
    {
      "value": [
        {
          "properties": {
            "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
            "principalId": "{objectID}",
            "scope": "/",
            "createdOn": "2016-08-17T19:21:16.3422480Z",
            "updatedOn": "2016-08-17T19:21:16.3422480Z",
            "createdBy": "22222222-2222-2222-2222-222222222222",
            "updatedBy": "22222222-2222-2222-2222-222222222222"
          },
          "id": "/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
          "type": "Microsoft.Authorization/roleAssignments",
          "name": "11111111-1111-1111-1111-111111111111"
        }
      ],
      "nextLink": null
    }
    ```
    
    Ponownie zapisz identyfikator z parametru , w tym `name` przypadku 11111111-1111-1111-1111-111111111111111111.

1. Na koniec użyj identyfikatora przypisania roli, aby usunąć przypisanie dodane `elevateAccess` przez :

    ```http
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111?api-version=2015-07-01
    ```

## <a name="next-steps"></a>Następne kroki

- [Omówienie różnych ról](rbac-and-directory-admin-roles.md)
- [Przypisywanie ról platformy Azure przy użyciu interfejsu API REST](role-assignments-rest.md)
