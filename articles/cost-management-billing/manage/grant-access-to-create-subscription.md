---
title: Przyznawanie dostępu do tworzenia subskrypcji Enterprise platformy Azure
description: Dowiedz się, jak przyznać użytkownikowi lub jednostce usługi możliwość programowego tworzenia subskrypcji Enterprise platformy Azure.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.reviewer: amberb
ms.topic: conceptual
ms.date: 08/26/2020
ms.author: banders
ms.openlocfilehash: 661b088d024a6da631fa06fbd97131091b9f650b
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91371885"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Przyznawanie dostępu do tworzenia subskrypcji Enterprise platformy Azure (wersja zapoznawcza)

Jako klient platformy Azure posiadający umowę [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) możesz przyznać innemu użytkownikowi lub jednostce usługi uprawnienia do tworzenia subskrypcji rozliczanych w ramach Twojego konta. W tym artykule dowiesz się, jak korzystać z [kontroli dostępu na podstawie ról (role-based access control, RBAC) platformy Azure](../../role-based-access-control/role-assignments-portal.md) w celu udostępniania możliwości tworzenia subskrypcji oraz jak przeprowadzać inspekcję tworzenia subskrypcji. W tym celu musisz mieć rolę właściciela konta, które chcesz udostępnić.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="grant-access"></a>Udzielanie dostępu

Aby [tworzyć subskrypcje na koncie rejestracji](programmatically-create-subscription.md), użytkownicy muszą mieć [rolę właściciela](../../role-based-access-control/built-in-roles.md#owner) RBAC platformy Azure na tym koncie. Można przyznać użytkownikowi lub grupie użytkowników rolę właściciela RBAC platformy Azure na koncie rejestracji, wykonując następujące czynności:

1. Pobierz identyfikator obiektu konta rejestracji, do którego chcesz przyznać dostęp

    Aby przyznać innym osobom rolę właściciela RBAC platformy Azure konta rejestracji, musisz być właścicielem konta lub właścicielem RBAC platformy Azure tego konta.

    # <a name="rest"></a>[REST](#tab/rest)

    Wykonaj żądanie wyświetlenia listy wszystkich kont rejestracji, do których masz dostęp:

    ```json
    GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
    ```

    Platforma Azure w odpowiedzi zwróci listę wszystkich kont rejestracji, do których masz dostęp:

    ```json
    {
      "value": [
        {
          "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "Microsoft.Billing/enrollmentAccounts",
          "properties": {
            "principalName": "SignUpEngineering@contoso.com"
          }
        },
        {
          "id": "/providers/Microsoft.Billing/enrollmentAccounts/4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "Microsoft.Billing/enrollmentAccounts",
          "properties": {
            "principalName": "BillingPlatformTeam@contoso.com"
          }
        }
      ]
    }
    ```

    Użyj właściwości `principalName`, aby określić konto, na którym chcesz przyznać użytkownikowi dostęp właściciela RBAC platformy Azure. Skopiuj właściwość `name` tego konta. Jeśli na przykład chcesz przyznać użytkownikowi dostęp właściciela RBAC platformy Azure na koncie rejestracji SignUpEngineering@contoso.com, skopiuj wartość ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Jest to identyfikator obiektu konta rejestracji. Wklej tę wartość w dogodnym miejscu, aby można było użyć jej w następnym kroku jako właściwości `enrollmentAccountObjectId`.

    # <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

    Użyj polecenia cmdlet [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount), aby wyświetlić wszystkie konta rejestracji, do których masz dostęp. Wybierz pozycję **Wypróbuj teraz**, aby otworzyć usługę [Azure Cloud Shell](https://shell.azure.com/). Aby wkleić kod, kliknij prawym przyciskiem myszy okna usługi Shell i wybierz pozycję **Wklej**.

    ```azurepowershell-interactive
    Get-AzEnrollmentAccount
    ```

    Platforma Azure w odpowiedzi zwróci listę kont rejestracji, do których masz dostęp:

    ```azurepowershell
    ObjectId                               | PrincipalName
    747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
    4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
    ```

    Użyj właściwości `principalName`, aby określić konto, na którym chcesz przyznać użytkownikowi dostęp właściciela RBAC platformy Azure. Skopiuj właściwość `ObjectId` tego konta. Jeśli na przykład chcesz przyznać użytkownikowi dostęp właściciela RBAC platformy Azure na koncie rejestracji SignUpEngineering@contoso.com, skopiuj wartość ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Wklej ten identyfikator obiektu w dogodnym miejscu, aby można było użyć go w następnym kroku jako właściwości `enrollmentAccountObjectId`.

    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

    Użyj polecenia [az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI), aby wyświetlić wszystkie konta rejestracji, do których masz dostęp. Wybierz pozycję **Wypróbuj teraz**, aby otworzyć usługę [Azure Cloud Shell](https://shell.azure.com/). Aby wkleić kod, kliknij prawym przyciskiem myszy okna usługi Shell i wybierz pozycję **Wklej**.

    ```azurecli-interactive
    az billing enrollment-account list
    ```

    Platforma Azure w odpowiedzi zwróci listę kont rejestracji, do których masz dostęp:

    ```json
    [
      {
        "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "principalName": "SignUpEngineering@contoso.com",
        "type": "Microsoft.Billing/enrollmentAccounts",
      },
      {
        "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "principalName": "BillingPlatformTeam@contoso.com",
        "type": "Microsoft.Billing/enrollmentAccounts",
      }
    ]
    ```

    ---

    Użyj właściwości `principalName`, aby określić konto, na którym chcesz przyznać użytkownikowi dostęp właściciela RBAC platformy Azure. Skopiuj właściwość `name` tego konta. Jeśli na przykład chcesz przyznać użytkownikowi dostęp właściciela RBAC platformy Azure na koncie rejestracji SignUpEngineering@contoso.com, skopiuj wartość ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Jest to identyfikator obiektu konta rejestracji. Wklej tę wartość w dogodnym miejscu, aby można było użyć jej w następnym kroku jako właściwości `enrollmentAccountObjectId`.

1. <a id="userObjectId"></a>Pobierz identyfikator obiektu użytkownika lub grupy, którym chcesz przyznać rolę właściciela RBAC platformy Azure

    1. W witrynie Azure Portal wyszukaj pozycję **Azure Active Directory**.
    1. Jeśli chcesz przyznać dostęp użytkownikowi, wybierz pozycję **Użytkownicy** w menu po lewej stronie. Jeśli chcesz przyznać dostęp grupie, wybierz pozycję **Grupy**.
    1. Wybierz użytkownika lub grupę, którym chcesz przyznać rolę właściciela RBAC platformy Azure.
    1. Jeśli wybrano użytkownika, identyfikator obiektu znajdziesz na stronie Profil. Jeśli wybrano grupę, identyfikator obiektu znajdziesz na stronie Przegląd. Skopiuj wartość **ObjectID**, wybierając ikonę po prawej stronie pola tekstowego. Wklej tę wartość w dogodnym miejscu, aby można było użyć jej w następnym kroku jako właściwości `userObjectId`.

1. Przyznaj użytkownikowi lub grupie rolę właściciela RBAC platformy Azure na koncie rejestracji

    Za pomocą wartości zebranych w pierwszych dwóch krokach przyznaj użytkownikowi lub grupie rolę właściciela RBAC platformy Azure na koncie rejestracji.

    # <a name="rest"></a>[REST](#tab/rest-2)

    Uruchom poniższe polecenie, zastępując wartość ```<enrollmentAccountObjectId>``` wartością `name` skopiowaną w pierwszym kroku (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Zastąp wartość ```<userObjectId>``` identyfikatorem obiektu skopiowanym w drugim kroku.

    ```json
    PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

    {
      "properties": {
        "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
        "principalId": "<userObjectId>"
      }
    }
    ```

    Gdy rola właściciela zostanie pomyślnie przypisana do zakresu konta rejestracji, platforma Azure w odpowiedzi wyświetli informacje na temat przypisania roli:

    ```json
    {
      "properties": {
        "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
        "principalId": "<userObjectId>",
        "scope": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "createdOn": "2018-03-05T08:36:26.4014813Z",
        "updatedOn": "2018-03-05T08:36:26.4014813Z",
        "createdBy": "<assignerObjectId>",
        "updatedBy": "<assignerObjectId>"
      },
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
      "type": "Microsoft.Authorization/roleAssignments",
      "name": "<roleAssignmentGuid>"
    }
    ```

    # <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell-2)

    Uruchom następujące polecenie [New-AzRoleAssignment](../../role-based-access-control/role-assignments-powershell.md), zastępując wartość ```<enrollmentAccountObjectId>``` wartością `ObjectId` uzyskaną w pierwszym kroku (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Zastąp wartość ```<userObjectId>``` identyfikatorem obiektu uzyskanym w drugim kroku.

    ```azurepowershell-interactive
    New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli-2)

    Uruchom następujące polecenie [az role assignment create](../../role-based-access-control/role-assignments-cli.md), zastępując wartość ```<enrollmentAccountObjectId>``` wartością `name` skopiowaną w pierwszym kroku (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Zastąp wartość ```<userObjectId>``` identyfikatorem obiektu uzyskanym w drugim kroku.

    ```azurecli-interactive
    az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    Gdy użytkownik zostanie właścicielem RBAC platformy Azure na koncie rejestracji, będzie mógł [programowo tworzyć subskrypcje](programmatically-create-subscription.md) w ramach tego konta. Subskrypcja utworzona przez delegowanych użytkowników nadal ma oryginalnego właściciela konta lub administratora usługi, ale domyślnie jej właścicielem RBAC platformy Azure jest również delegowany użytkownik.

    ---

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Przeprowadzanie inspekcji osób, które utworzyły subskrypcje za pomocą dzienników aktywności

Aby śledzić subskrypcje utworzone za pośrednictwem tego interfejsu API, skorzystaj z [interfejsu API dziennika aktywności w dzierżawie](/rest/api/monitor/tenantactivitylogs). Obecnie nie można śledzić tworzenia subskrypcji za pomocą programu PowerShell, interfejsu wiersza polecenia, ani witryny Azure Portal.

1. Jako administrator dzierżawy usługi Azure Active Directory [podnieś poziom dostępu](../../role-based-access-control/elevate-access-global-admin.md), a następnie przypisz rolę Czytelnika dla zakresu `/providers/microsoft.insights/eventtypes/management` do użytkownika wykonującego inspekcję. Ten dostęp przysługuje roli [czytelnika](../../role-based-access-control/built-in-roles.md#reader), [współautora monitorowania](../../role-based-access-control/built-in-roles.md#monitoring-contributor) lub [roli niestandardowej](../../role-based-access-control/custom-roles.md).
1. Jako użytkownik wykonujący inspekcję wywołaj [interfejs API dziennika aktywności w dzierżawie](/rest/api/monitor/tenantactivitylogs), aby wyświetlić działania tworzenia subskrypcji. Przykład:

    ```
    GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'"
    ```

Aby wygodnie wywołać ten interfejs API z poziomu wiersza polecenia, wypróbuj narzędzie [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="next-steps"></a>Następne kroki

* Teraz, gdy użytkownik lub jednostka usługi ma uprawnienie do tworzenia subskrypcji, możesz użyć tej tożsamości do [programowego tworzenia subskrypcji Enterprise platformy Azure](programmatically-create-subscription.md).
* Aby uzyskać informacje dotyczące tworzenia subskrypcji przy użyciu platformy .NET, zobacz [kod przykładowy w usłudze GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Aby dowiedzieć się więcej na temat usługi Azure Resource Manager i jej interfejsów API, zobacz [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/management/overview.md).
* Aby dowiedzieć się więcej na temat zarządzania dużą liczbą subskrypcji za pomocą grup zarządzania, zobacz [Organizowanie zasobów przy użyciu grup zarządzania platformy Azure](../../governance/management-groups/overview.md).
* Aby wyświetlić kompleksowe najlepsze rozwiązania dotyczące nadzoru nad subskrypcjami dla dużych organizacji, zobacz [Szkielet platformy Azure dla przedsiębiorstwa — narzucony nadzór subskrypcji](/azure/architecture/cloud-adoption-guide/subscription-governance).
