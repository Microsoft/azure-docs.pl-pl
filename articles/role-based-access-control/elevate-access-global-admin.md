---
title: Podnoszenie poziomu dostępu w celu zarządzania wszystkimi subskrypcjami platformy Azure i grupami zarządzania
description: Opisuje sposób podniesienia uprawnień dostępu administratora globalnego do zarządzania wszystkimi subskrypcjami i grupami zarządzania w Azure Active Directory przy użyciu Azure Portal lub interfejsu API REST.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 06/09/2020
ms.author: rolyon
ms.openlocfilehash: efc3bfef7c182ea005ac17a59793ac8f6484a424
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102042015"
---
# <a name="elevate-access-to-manage-all-azure-subscriptions-and-management-groups"></a>Podnoszenie poziomu dostępu w celu zarządzania wszystkimi subskrypcjami platformy Azure i grupami zarządzania

Administrator globalny w Azure Active Directory (Azure AD) może nie mieć dostępu do wszystkich subskrypcji i grup zarządzania w Twoim katalogu. W tym artykule opisano sposób podniesienia poziomu dostępu do wszystkich subskrypcji i grup zarządzania.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="why-would-you-need-to-elevate-your-access"></a>Dlaczego należy podnieść poziom dostępu?

Jeśli jesteś administratorem globalnym, może się zdarzyć, że chcesz wykonać następujące czynności:

- Odzyskaj dostęp do subskrypcji platformy Azure lub grupy zarządzania, gdy użytkownik utraci dostęp
- Przyznać innemu użytkownikowi lub sobie samemu dostęp do subskrypcji platformy Azure lub grupy zarządzania
- Wyświetlanie wszystkich subskrypcji platformy Azure lub grup zarządzania w organizacji
- Zezwalaj aplikacji usługi Automation (na przykład w przypadku aplikacji do fakturowania lub inspekcji) na dostęp do wszystkich subskrypcji platformy Azure lub grup zarządzania

## <a name="how-does-elevated-access-work"></a>Jak działa podwyższony poziom dostępu?

Usługa Azure AD i zasoby platformy Azure są zabezpieczone niezależnie od siebie. Oznacza to, że przypisania ról usługi Azure AD nie zapewniają dostępu do zasobów platformy Azure, a przypisania ról platformy Azure nie udzielają dostępu do usługi Azure AD. Jeśli jednak jesteś [administratorem globalnym](../active-directory/roles/permissions-reference.md#global-administrator) w usłudze Azure AD, możesz przypisać sobie dostęp do wszystkich subskrypcji platformy Azure i grup zarządzania w Twoim katalogu. Użyj tej funkcji, jeśli nie masz dostępu do zasobów subskrypcji platformy Azure, takich jak maszyny wirtualne lub konta magazynu, i chcesz użyć uprawnienia administratora globalnego, aby uzyskać dostęp do tych zasobów.

Podniesienie poziomu dostępu spowoduje przypisanie roli [administratora dostępu użytkownika](built-in-roles.md#user-access-administrator) na platformie Azure w zakresie głównym ( `/` ).Pozwala to na wyświetlenie wszystkich zasobów i przypisanie dostępu w ramach dowolnej subskrypcji lub grupy zarządzania w katalogu. Przypisania ról administratora dostępu użytkowników można usuwać za pomocą Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub API REST.

Ten podwyższony poziom dostępu należy usunąć po wprowadzeniu zmian, które należy wprowadzić w zakresie głównym.

![Podnoszenie poziomu dostępu](./media/elevate-access-global-admin/elevate-access.png)

## <a name="azure-portal"></a>Azure Portal

### <a name="elevate-access-for-a-global-administrator"></a>Podnieś poziom dostępu dla administratora globalnego

Wykonaj następujące kroki, aby podwyższyć poziom dostępu administratora globalnego przy użyciu Azure Portal.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) lub [Centrum administracyjnego Azure Active Directory](https://aad.portal.azure.com) jako Administrator globalny.

    Jeśli używasz Azure AD Privileged Identity Management, [Aktywuj przypisanie roli administratora globalnego](../active-directory/privileged-identity-management/pim-how-to-activate-role.md).

1. Otwórz **Azure Active Directory**.

1. W obszarze **Zarządzanie** wybierz pozycję **Właściwości**.

   ![Wybieranie właściwości właściwości Azure Active Directory — zrzut ekranu](./media/elevate-access-global-admin/azure-active-directory-properties.png)

1. W obszarze **Zarządzanie dostępem do zasobów platformy Azure** Ustaw przełącznik na **wartość tak**.

   ![Zarządzanie dostępem do zasobów platformy Azure — zrzut ekranu](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   Po ustawieniu przełącznika na **wartość tak** przypiszesz rolę administratora dostępu użytkownika w usłudze Azure RBAC w zakresie głównym (/). To przyznaje uprawnienia do przypisywania ról we wszystkich subskrypcjach platformy Azure i grupach zarządzania skojarzonych z tym katalogiem usługi Azure AD. Ten przełącznik jest dostępny tylko dla użytkowników, którym przypisano rolę administratora globalnego w usłudze Azure AD.

   Po ustawieniu przełącznika na wartość **nie** rola administratora dostępu użytkowników w usłudze Azure RBAC zostanie usunięta z konta użytkownika. Nie można już przypisywać ról we wszystkich subskrypcjach platformy Azure i grupach zarządzania skojarzonych z tym katalogiem usługi Azure AD. Można wyświetlać i zarządzać tylko subskrypcjami platformy Azure i grupami zarządzania, do których udzielono dostępu.

    > [!NOTE]
    > Jeśli używasz [Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md), dezaktywowanie przypisywania roli nie powoduje zmiany opcji **zarządzania dostępem dla zasobów platformy Azure** na wartość **nie**. Aby zachować najmniejszy dostęp uprzywilejowany, zalecamy ustawienie tego przełącznika na wartość **nie** przed dezaktywacją przypisania roli.
    
1. Kliknij przycisk **Zapisz** , aby zapisać ustawienia.

   To ustawienie nie jest właściwością globalną i ma zastosowanie tylko do aktualnie zalogowanego użytkownika. Nie można podwyższyć poziomu dostępu dla wszystkich członków roli administratora globalnego.

1. Wyloguj się i zaloguj się ponownie, aby odświeżyć dostęp.

    Teraz masz dostęp do wszystkich subskrypcji i grup zarządzania w Twoim katalogu. Po wyświetleniu okienka kontroli dostępu (IAM) można zauważyć, że masz przypisaną rolę administratora dostępu użytkownika w zakresie głównym.

   ![Przypisania ról subskrypcji z zakresem głównym — zrzut ekranu](./media/elevate-access-global-admin/iam-root.png)

1. Wprowadź zmiany, które należy wprowadzić w celu uzyskania dostępu z podwyższonym poziomem uprawnień.

    Aby uzyskać informacje na temat przypisywania ról, zobacz [Przypisywanie ról platformy Azure przy użyciu Azure Portal](role-assignments-portal.md). Jeśli używasz Privileged Identity Management, zobacz [odnajdywanie zasobów platformy Azure w celu zarządzania](../active-directory/privileged-identity-management/pim-resource-roles-discover-resources.md) lub [przypisywania ról zasobów platformy Azure](../active-directory/privileged-identity-management/pim-resource-roles-assign-roles.md).

1. Wykonaj kroki opisane w poniższej sekcji, aby usunąć podwyższony poziom dostępu.

### <a name="remove-elevated-access"></a>Usuń dostęp z podwyższonym poziomem uprawnień

Aby usunąć przypisanie roli administratora dostępu użytkownika w zakresie katalogu głównego ( `/` ), wykonaj następujące czynności.

1. Zaloguj się jako ten sam użytkownik, który został użyty do podniesienia poziomu dostępu.

1. Na liście Nawigacja kliknij **Azure Active Directory** a następnie kliknij pozycję **Właściwości**.

1. Dla opcji **Zarządzanie dostępem dla zasobów platformy Azure** Przełącz się z powrotem do **nie**. Ponieważ jest to ustawienie dla poszczególnych użytkowników, należy zalogować się jako ten sam użytkownik, który został użyty do podniesienia poziomu dostępu.

    Jeśli spróbujesz usunąć przypisanie roli administratora dostępu użytkownika w okienku kontroli dostępu (IAM), zobaczysz następujący komunikat. Aby usunąć przypisanie roli, należy ustawić przełącznik z powrotem do nie lub **nie** używać Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub API REST.

    ![Usuwanie przypisań ról z zakresem głównym](./media/elevate-access-global-admin/iam-root-remove.png)

1. Wyloguj się jako Administrator globalny.

    W przypadku korzystania z Privileged Identity Management Dezaktywuj przypisanie roli administratora globalnego.

    > [!NOTE]
    > Jeśli używasz [Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md), dezaktywowanie przypisywania roli nie powoduje zmiany opcji **zarządzania dostępem dla zasobów platformy Azure** na wartość **nie**. Aby zachować najmniejszy dostęp uprzywilejowany, zalecamy ustawienie tego przełącznika na wartość **nie** przed dezaktywacją przypisania roli.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

### <a name="list-role-assignment-at-root-scope-"></a>Wyświetl listę przypisań ról w zakresie głównym (/)

Aby wyświetlić listę przypisania roli administratora dostępu użytkownika dla użytkownika w zakresie katalogu głównego ( `/` ), użyj polecenia [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) .

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

### <a name="remove-elevated-access"></a>Usuń dostęp z podwyższonym poziomem uprawnień

Aby usunąć przypisanie roli administratora dostępu użytkownika dla siebie lub innego użytkownika w zakresie głównym ( `/` ), wykonaj następujące kroki.

1. Zaloguj się jako użytkownik, który może usunąć dostęp z podwyższonym poziomem uprawnień. Może to być ten sam użytkownik, który został użyty do podniesienia uprawnień dostępu lub innego administratora globalnego z podwyższonym poziomem uprawnień w zakresie głównym.

1. Użyj polecenia [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) , aby usunąć przypisanie roli administratora dostępu użytkownika.

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName <username@example.com> `
      -RoleDefinitionName "User Access Administrator" -Scope "/"
    ```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

### <a name="elevate-access-for-a-global-administrator"></a>Podnieś poziom dostępu dla administratora globalnego

Wykonaj następujące podstawowe kroki, aby podwyższyć poziom dostępu administratora globalnego przy użyciu interfejsu wiersza polecenia platformy Azure.

1. Użyj polecenia [AZ REST](/cli/azure/reference-index#az_rest) , aby wywołać `elevateAccess` punkt końcowy, który przyznaje rolę administratora dostępu użytkownika w zakresie głównym ( `/` ).

    ```azurecli
    az rest --method post --url "/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01"
    ```

1. Wprowadź zmiany, które należy wprowadzić w celu uzyskania dostępu z podwyższonym poziomem uprawnień.

    Aby uzyskać informacje na temat przypisywania ról, zobacz [Przypisywanie ról platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](role-assignments-cli.md).

1. Wykonaj kroki opisane w dalszej części sekcji, aby usunąć podwyższony poziom dostępu.

### <a name="list-role-assignment-at-root-scope-"></a>Wyświetl listę przypisań ról w zakresie głównym (/)

Aby wyświetlić listę przypisania roli administratora dostępu użytkownika dla użytkownika w zakresie głównym ( `/` ), użyj polecenia [AZ role przypisanie list](/cli/azure/role/assignment#az-role-assignment-list) .

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

### <a name="remove-elevated-access"></a>Usuń dostęp z podwyższonym poziomem uprawnień

Aby usunąć przypisanie roli administratora dostępu użytkownika dla siebie lub innego użytkownika w zakresie głównym ( `/` ), wykonaj następujące kroki.

1. Zaloguj się jako użytkownik, który może usunąć dostęp z podwyższonym poziomem uprawnień. Może to być ten sam użytkownik, który został użyty do podniesienia uprawnień dostępu lub innego administratora globalnego z podwyższonym poziomem uprawnień w zakresie głównym.

1. Użyj polecenia [AZ role przypisanie Delete](/cli/azure/role/assignment#az-role-assignment-delete) , aby usunąć przypisanie roli administratora dostępu użytkownika.

    ```azurecli
    az role assignment delete --assignee username@example.com --role "User Access Administrator" --scope "/"
    ```

## <a name="rest-api"></a>Interfejs API REST

### <a name="elevate-access-for-a-global-administrator"></a>Podnieś poziom dostępu dla administratora globalnego

Wykonaj następujące podstawowe kroki, aby podwyższyć poziom dostępu administratora globalnego przy użyciu interfejsu API REST.

1. Przy użyciu REST, Call `elevateAccess` , która przyznaje użytkownikowi rolę administratora dostępu użytkownika w zakresie głównym ( `/` ).

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. Wprowadź zmiany, które należy wprowadzić w celu uzyskania dostępu z podwyższonym poziomem uprawnień.

    Aby uzyskać informacje na temat przypisywania ról, zobacz [Przypisywanie ról platformy Azure przy użyciu interfejsu API REST](role-assignments-rest.md).

1. Wykonaj kroki opisane w dalszej części sekcji, aby usunąć podwyższony poziom dostępu.

### <a name="list-role-assignments-at-root-scope-"></a>Wyświetl listę przypisań ról w zakresie głównym (/)

Można wyświetlić listę wszystkich przypisań ról dla użytkownika w zakresie głównym ( `/` ).

- Wywołanie [Get roleAssignments](/rest/api/authorization/roleassignments/listforscope) gdzie `{objectIdOfUser}` jest identyfikatorem obiektu użytkownika, którego przypisania ról chcesz pobrać.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectIdOfUser}'
   ```

### <a name="list-deny-assignments-at-root-scope-"></a>Wyświetl listę przypisań Odmów w zakresie głównym (/)

Można wyświetlić listę wszystkich przypisań Odmów dla użytkownika w zakresie głównym ( `/` ).

- Wywołanie GET denyAssignments gdzie `{objectIdOfUser}` jest identyfikatorem obiektu użytkownika, którego przypisania odmowy chcesz pobrać.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter=gdprExportPrincipalId+eq+'{objectIdOfUser}'
   ```

### <a name="remove-elevated-access"></a>Usuń dostęp z podwyższonym poziomem uprawnień

Po wywołaniu `elevateAccess` Utwórz przypisanie roli dla siebie, więc aby odwołać te uprawnienia, musisz usunąć przypisanie roli administratora dostępu użytkownika do siebie w zakresie głównym ( `/` ).

1. Wywołaj metodę [Get roleDefinitions](/rest/api/authorization/roledefinitions/get) , gdzie `roleName` jest to administrator dostępu użytkowników, aby określić identyfikator nazwy roli administratora dostępu użytkownika.

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

    Zapisz identyfikator z `name` parametru, w tym przypadku `18d7d88d-d35e-4fb5-a5c3-7773c20a72d9` .

1. Należy również wyświetlić listę przypisania roli administratora katalogu w zakresie katalogu. Wyświetl listę wszystkich przydziałów w katalogu dla `principalId` administratora katalogu, który wykonał wywołanie podniesienia uprawnień dostępu. Spowoduje to wyświetlenie listy wszystkich przypisań w katalogu dla identyfikatora objectid.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
        
    >[!NOTE] 
    >Administrator katalogu nie powinien mieć wielu przypisań, jeśli poprzednia kwerenda zwróci zbyt wiele przypisań, można również wykonać zapytania dotyczące wszystkich przypisań tylko na poziomie zakresu katalogu, a następnie filtrować wyniki: `GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
            
1. Poprzednie wywołania zwracają listę przypisań ról. Znajdź przypisanie roli, w którym zakres jest `"/"` i `roleDefinitionId` KOŃCZĄ się identyfikatorem nazwy roli znalezionej w kroku 1 i `principalId` dopasowuje objectid administratora katalogu. 
    
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
    
    Ponownie Zapisz identyfikator z `name` parametru, w tym przypadku 11111111-1111-1111-1111-111111111111.

1. Na koniec użyj identyfikatora przypisania roli, aby usunąć przypisanie dodane przez `elevateAccess` :

    ```http
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111?api-version=2015-07-01
    ```

## <a name="next-steps"></a>Następne kroki

- [Omówienie różnych ról](rbac-and-directory-admin-roles.md)
- [Przypisywanie ról platformy Azure przy użyciu interfejsu API REST](role-assignments-rest.md)
