---
title: Podnoszenie poziomu dostępu w celu zarządzania wszystkimi subskrypcjami platformy Azure i grupami zarządzania
description: W tym artykule opisano sposób podnoszenia poziomu dostępu administratora globalnego do zarządzania wszystkimi subskrypcjami i grupami zarządzania w usłudze Azure Active Directory przy użyciu witryny Azure portal lub interfejsu API REST.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: bagovind
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 18490ec7c3cfde33cef186b753e2adc809f854c6
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641370"
---
# <a name="elevate-access-to-manage-all-azure-subscriptions-and-management-groups"></a>Podnoszenie poziomu dostępu w celu zarządzania wszystkimi subskrypcjami platformy Azure i grupami zarządzania

Jako administrator globalny w usłudze Azure Active Directory (Azure AD) możesz nie mieć dostępu do wszystkich subskrypcji i grup zarządzania w katalogu. W tym artykule opisano sposoby podnoszenia poziomu dostępu do wszystkich subskrypcji i grup zarządzania.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="why-would-you-need-to-elevate-your-access"></a>Dlaczego trzeba podnieść swój dostęp?

Jeśli jesteś administratorem globalnym, mogą wystąpić chwile, w których chcesz wykonać następujące czynności:

- Odzyskaj dostęp do subskrypcji platformy Azure lub grupy zarządzania, gdy użytkownik utraci dostęp
- Przyznać innemu użytkownikowi lub sobie samemu dostęp do subskrypcji platformy Azure lub grupy zarządzania
- Zobacz wszystkie subskrypcje platformy Azure lub grupy zarządzania w organizacji
- Zezwalaj aplikacji automatyzacji (takiej jak aplikacja do fakturowania lub inspekcji) na dostęp do wszystkich subskrypcji platformy Azure lub grup zarządzania

## <a name="how-does-elevated-access-work"></a>Jak działa podwyższony dostęp?

Zasoby usługi Azure AD i platformy Azure są zabezpieczone niezależnie od siebie. Oznacza to, że przypisania ról usługi Azure AD nie udzielają dostępu do zasobów platformy Azure, a przypisania ról platformy Azure nie udzielają dostępu do usługi Azure AD. Jeśli jednak jesteś [administratorem globalnym](../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator-permissions) w usłudze Azure AD, możesz przypisać sobie dostęp do wszystkich subskrypcji platformy Azure i grup zarządzania w katalogu. Tej możliwości należy użyć, jeśli nie masz dostępu do zasobów subskrypcji platformy Azure, takich jak maszyny wirtualne lub konta magazynu, i chcesz użyć uprawnienia administratora globalnego, aby uzyskać dostęp do tych zasobów.

Po podniesieniu poziomu dostępu zostanie przypisana rola [administratora dostępu użytkownika](built-in-roles.md#user-access-administrator) `/`na platformie Azure w zakresie głównym ( ).Dzięki temu można wyświetlić wszystkie zasoby i przypisać dostęp w dowolnej subskrypcji lub grupy zarządzania w katalogu. Przypisania ról administratora dostępu użytkownika można usunąć za pomocą usługi Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST.

Należy usunąć ten podwyższony poziom dostępu po wprowadzeniu zmian, które należy wprowadzić w zakresie głównym.

![Podnoszenie poziomu dostępu](./media/elevate-access-global-admin/elevate-access.png)

## <a name="azure-portal"></a>Azure Portal

### <a name="elevate-access-for-a-global-administrator"></a>Zwiększ poziom dostępu administratora globalnego

Wykonaj następujące kroki, aby podnieść poziom dostępu dla administratora globalnego przy użyciu witryny Azure portal.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) lub [centrum administracyjnego usługi Azure Active Directory](https://aad.portal.azure.com) jako administrator globalny.

1. Otwórz **usługę Azure Active Directory**.

1. W obszarze **Zarządzanie**wybierz pozycję **Właściwości**.

   ![Wybierz właściwości dla właściwości usługi Azure Active Directory — zrzut ekranu](./media/elevate-access-global-admin/azure-active-directory-properties.png)

1. W obszarze **Zarządzanie programem Access dla zasobów platformy Azure**ustaw przełącznik na **Tak**.

   ![Zarządzanie dostępem dla zasobów platformy Azure — zrzut ekranu](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   Po ustawieniu przełącznika **tak,** zostanie przypisana rola administratora dostępu użytkownika w usłudze Azure RBAC w zakresie głównym (/). Daje to uprawnienia do przypisywania ról we wszystkich subskrypcjach platformy Azure i grupach zarządzania skojarzonych z tym katalogiem usługi Azure AD. Ten przełącznik jest dostępny tylko dla użytkowników, którym przypisano rolę administratora globalnego w usłudze Azure AD.

   Po ustawieniu przełącznika na **Nie,** rola administratora dostępu użytkownika w usłudze Azure RBAC zostanie usunięta z konta użytkownika. Nie można już przypisywać ról we wszystkich subskrypcjach platformy Azure i grupach zarządzania skojarzonych z tym katalogiem usługi Azure AD. Możesz wyświetlać i zarządzać tylko subskrypcjami platformy Azure i grupami zarządzania, do których udzielono dostępu.

    > [!NOTE]
    > Jeśli używasz [usługi Azure AD Privileged Identity Management (PIM),](../active-directory/privileged-identity-management/pim-configure.md)dezaktywacja przypisania roli nie powoduje zmiany tego przełącznika na **Nie**. Aby zachować najmniej uprzywilejowany dostęp, zaleca się ustawienie tego przełącznika na **Nie** przed dezaktywacją przypisania roli.
    
1. Kliknij **przycisk Zapisz,** aby zapisać ustawienie.

   To ustawienie nie jest właściwością globalną i dotyczy tylko aktualnie zalogowanego użytkownika. Nie można podnieść poziomu dostępu dla wszystkich członków roli administratora globalnego.

1. Wyloguj się i zaloguj się z powrotem, aby odświeżyć swój dostęp.

    Teraz powinieneś mieć dostęp do wszystkich subskrypcji i grup zarządzania w katalogu. Podczas wyświetlania okienka kontroli dostępu (IAM) można zauważyć, że przypisano rolę Administratora dostępu użytkownika w zakresie głównym.

   ![Przypisania ról subskrypcji z zakresem głównym — zrzut ekranu](./media/elevate-access-global-admin/iam-root.png)

1. Wykonuj zmiany, które należy wprowadzić przy podwyższonym poziomie dostępu.

    Aby uzyskać informacje dotyczące przypisywania ról, zobacz [Zarządzanie dostępem przy użyciu funkcji RBAC i witryny Azure portal](role-assignments-portal.md). Jeśli używasz usługi Azure AD Privileged Identity Management (PIM), zobacz [Odnajdowanie zasobów platformy Azure do zarządzania w pim](../active-directory/privileged-identity-management/pim-resource-roles-discover-resources.md) lub [przypisywanie ról zasobów platformy Azure w pim](../active-directory/privileged-identity-management/pim-resource-roles-assign-roles.md).

### <a name="remove-elevated-access"></a>Usuwanie podwyższonego poziomu dostępu

Aby usunąć przypisanie roli Administratora`/`dostępu użytkownika w zakresie głównym ( ), wykonaj następujące kroki.

1. Zaloguj się jako ten sam użytkownik, który został użyty do podniesienia poziomu dostępu.

1. Na liście nawigacji kliknij pozycję **Usługa Azure Active Directory,** a następnie kliknij pozycję **Właściwości**.

1. Ustaw **przełączenie zarządzania programem Access dla zasobów platformy Azure** na **Nie**. Ponieważ jest to ustawienie dla użytkownika, użytkownik musi być zalogowany jako ten sam użytkownik, który został użyty do podniesienia poziomu dostępu.

    Jeśli spróbujesz usunąć przypisanie roli Administratora dostępu użytkownika w okienku kontroli dostępu (IAM), zostanie wyświetlony następujący komunikat. Aby usunąć przypisanie roli, należy ustawić przełącznik z powrotem na **Nie** lub użyj interfejsu Api programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST.

    ![Usuwanie przypisań ról z zakresem głównym](./media/elevate-access-global-admin/iam-root-remove.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

### <a name="list-role-assignment-at-root-scope-"></a>Przypisywanie roli listy w zakresie głównym (/)

Aby wyświetlić listę przypisania roli Administratora`/`dostępu użytkownika dla użytkownika w zakresie głównym ( ), użyj polecenia [Get-AzRoleAssignment.](/powershell/module/az.resources/get-azroleassignment)

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

Aby usunąć przypisanie roli Administratora dostępu użytkownika dla`/`siebie lub innego użytkownika w zakresie głównym ( ), wykonaj następujące kroki.

1. Zaloguj się jako użytkownik, który może usunąć podwyższony poziom dostępu. Może to być ten sam użytkownik, który został użyty do podniesienia poziomu dostępu lub inny administrator globalny z podwyższonym poziomem dostępu w zakresie głównym.

1. Użyj polecenia [Usuń-AzRoleAssignment,](/powershell/module/az.resources/remove-azroleassignment) aby usunąć przypisanie roli Administratora dostępu użytkownika.

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName <username@example.com> `
      -RoleDefinitionName "User Access Administrator" -Scope "/"
    ```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

### <a name="list-role-assignment-at-root-scope-"></a>Przypisywanie roli listy w zakresie głównym (/)

Aby wyświetlić listę przypisania roli Administratora`/`dostępu użytkownika dla użytkownika w zakresie głównym ( ), użyj polecenia [listy przypisań ról az.](/cli/azure/role/assignment#az-role-assignment-list)

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

Aby usunąć przypisanie roli Administratora dostępu użytkownika dla`/`siebie lub innego użytkownika w zakresie głównym ( ), wykonaj następujące kroki.

1. Zaloguj się jako użytkownik, który może usunąć podwyższony poziom dostępu. Może to być ten sam użytkownik, który został użyty do podniesienia poziomu dostępu lub inny administrator globalny z podwyższonym poziomem dostępu w zakresie głównym.

1. Użyj polecenia [usuń przypisanie roli az,](/cli/azure/role/assignment#az-role-assignment-delete) aby usunąć przypisanie roli Administratora dostępu użytkownika.

    ```azurecli
    az role assignment delete --assignee username@example.com --role "User Access Administrator" --scope "/"
    ```

## <a name="rest-api"></a>Interfejs API REST

### <a name="elevate-access-for-a-global-administrator"></a>Zwiększ poziom dostępu administratora globalnego

Poniższe podstawowe kroki można wykonać, aby podnieść poziom dostępu administratora globalnego przy użyciu interfejsu API REST.

1. Korzystanie REST, `elevateAccess`call , który przyznaje ci rolę`/`administratora dostępu użytkownika w zakresie głównym ( ).

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. Utwórz [przypisanie roli,](/rest/api/authorization/roleassignments) aby przypisać dowolną rolę w dowolnym zakresie. W poniższym przykładzie przedstawiono właściwości przypisywania roli {roleDefinitionID} w zakresie głównym (`/`):

   ```json
   { 
     "properties": {
       "roleDefinitionId": "providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionID}",
       "principalId": "{objectID}",
       "scope": "/"
     },
     "id": "providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
     "type": "Microsoft.Authorization/roleAssignments",
     "name": "11111111-1111-1111-1111-111111111111"
   }
   ```

1. Podczas gdy administrator dostępu użytkownika, można również usunąć`/`przypisania ról w zakresie głównym ( ).

1. Usuń uprawnienia administratora dostępu użytkownika, dopóki nie będą one ponownie potrzebne.

### <a name="list-role-assignments-at-root-scope-"></a>Lista przypisań ról w zakresie głównym (/)

Można wyświetlić listę wszystkich przypisań ról dla`/`użytkownika w zakresie głównym ( ).

- Wywołaj [polecenie GETAssignments gdzie](/rest/api/authorization/roleassignments/listforscope) `{objectIdOfUser}` jest identyfikator obiektu użytkownika, którego przypisania ról, które chcesz pobrać.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectIdOfUser}'
   ```

### <a name="list-deny-assignments-at-root-scope-"></a>Lista przypisań odmowy w zakresie głównym (/)

Można wyświetlić listę wszystkich przypisań odmów`/`dla użytkownika w zakresie głównym ( ).

- Wywołaj get denyAssignments gdzie `{objectIdOfUser}` jest identyfikator obiektu użytkownika, którego przypisania odmowy, które chcesz pobrać.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter=gdprExportPrincipalId+eq+'{objectIdOfUser}'
   ```

### <a name="remove-elevated-access"></a>Usuwanie podwyższonego poziomu dostępu

Podczas wywoływania `elevateAccess`program umożliwia utworzenie przypisania roli dla siebie, więc aby odwołać te uprawnienia, należy`/`usunąć przypisanie roli Administratora dostępu użytkownika dla siebie w zakresie głównym ( ).

1. Wywołanie [funkcji GETDefinitions,](/rest/api/authorization/roledefinitions/get) gdzie `roleName` jest równa Administrator dostępu użytkownika, aby określić nazwę roli administratora dostępu użytkownika.

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

    Zapisz identyfikator z `name` parametru, w `18d7d88d-d35e-4fb5-a5c3-7773c20a72d9`tym przypadku .

1. Należy również wyświetlić listę przypisania roli dla administratora katalogu w zakresie katalogu. Wyświetl listę wszystkich przydziałów w `principalId` zakresie katalogów administratora katalogu, który wykonał wywołanie dostępu podwyższonego poziomem uprawnień. Spowoduje to wyświetlenie listy wszystkich przypisań w katalogu dla objectid.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
        
    >[!NOTE] 
    >Administrator katalogu nie powinien mieć wielu przypisań, jeśli poprzednia kwerenda zwraca zbyt wiele przypisań, można również wysyłać kwerendy dla wszystkich przydziałów tylko na poziomie zakresu katalogu, a następnie filtrować wyniki:`GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
            
1. Poprzednie wywołania zwracają listę przypisań ról. Znajdź przypisanie roli, `"/"` w `roleDefinitionId` którym znajduje się zakres, a kończy się `principalId` identyfikatorem nazwy roli znalezionym w kroku 1 i pasuje do identyfikatora objectId administratora katalogu. 
    
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
    
    Ponownie zapisz identyfikator z `name` parametru, w tym przypadku 11111111-1111-1111-1111-1111-111111111111111111.

1. Na koniec użyj identyfikatora przypisania roli, `elevateAccess`aby usunąć przypisanie dodane przez:

    ```http
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111?api-version=2015-07-01
    ```

## <a name="next-steps"></a>Następne kroki

- [Understand the different roles in Azure (Omówienie ról na platformie Azure)](rbac-and-directory-admin-roles.md)
- [Zarządzanie dostępem do zasobów platformy Azure przy użyciu interfejsu RBAC i interfejsu API REST](role-assignments-rest.md)
