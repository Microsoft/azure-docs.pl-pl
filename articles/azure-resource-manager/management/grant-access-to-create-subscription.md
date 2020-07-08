---
title: Udzielanie dostępu do tworzenia subskrypcji platformy Azure Enterprise
description: Dowiedz się, jak przyznać jednostce użytkownika lub usługi możliwość programowego tworzenia subskrypcji platformy Azure.
author: jureid
manager: jureid
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: jureid
ms.openlocfilehash: 6a03d5e67e859a29cb18e29223fe74134aef75fb
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86057623"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Udzielanie dostępu do tworzenia subskrypcji platformy Azure Enterprise (wersja zapoznawcza)

Jako klient platformy Azure w [Umowa Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)można przyznać innemu użytkownikowi lub uprawnienia głównej usługi do tworzenia subskrypcji rozliczanych z Twoim kontem. W tym artykule dowiesz się, jak używać [Access Control opartej na rolach (RBAC)](../../role-based-access-control/role-assignments-portal.md) do udostępniania możliwości tworzenia subskrypcji i przeprowadzania inspekcji tworzenia subskrypcji. Musisz mieć rolę właściciela na koncie, które chcesz udostępnić.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="grant-access"></a>Udzielanie dostępu

Aby można było [tworzyć subskrypcje](programmatically-create-subscription.md)na koncie rejestracji, użytkownicy muszą mieć [rolę właściciela RBAC](../../role-based-access-control/built-in-roles.md#owner) na tym koncie. Można udzielić użytkownikowi lub grupie użytkowników roli właściciela RBAC na koncie rejestracji, wykonując następujące czynności:

1. Pobierz identyfikator obiektu konta rejestracji, do którego chcesz udzielić dostępu

    Aby przyznać innym osobom rolę właściciela RBAC na koncie rejestracji, musisz być właścicielem konta lub właścicielem RBAC konta.

    # <a name="rest"></a>[REST](#tab/rest)

    Żądaj, aby wyświetlić listę wszystkich kont rejestracji, do których masz dostęp:

    ```json
    GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
    ```

    Platforma Azure reaguje na listę wszystkich kont rejestracji, do których masz dostęp:

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

    Użyj `principalName` właściwości, aby zidentyfikować konto, do którego chcesz udzielić dostępu właściciela RBAC. Skopiuj to `name` konto. Jeśli na przykład chcesz udzielić dostępu właściciela RBAC do SignUpEngineering@contoso.com konta rejestracji, skopiujesz ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` . To jest identyfikator obiektu konta rejestracji. Wklej tę wartość w miejscu, aby można było użyć jej w następnym kroku jako `enrollmentAccountObjectId` .

    # <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

    Użyj polecenia cmdlet [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) , aby wyświetlić listę wszystkich kont rejestracji, do których masz dostęp. Wybierz pozycję **Wypróbuj,** aby otworzyć [Azure Cloud Shell](https://shell.azure.com/). Aby wkleić kod, kliknij prawym przyciskiem myszy okna powłoki i wybierz polecenie **Wklej**.

    ```azurepowershell-interactive
    Get-AzEnrollmentAccount
    ```

    Platforma Azure reaguje na listę kont rejestracji, do których masz dostęp:

    ```azurepowershell
    ObjectId                               | PrincipalName
    747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
    4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
    ```

    Użyj `principalName` właściwości, aby zidentyfikować konto, do którego chcesz udzielić dostępu właściciela RBAC. Skopiuj to `ObjectId` konto. Jeśli na przykład chcesz udzielić dostępu właściciela RBAC do SignUpEngineering@contoso.com konta rejestracji, skopiujesz ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` . Wklej ten identyfikator obiektu w miejscu, aby można było go użyć w następnym kroku jako `enrollmentAccountObjectId` .

    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

    Aby wyświetlić listę wszystkich kont rejestracji, do których masz dostęp, użyj polecenia [AZ Rozlicz rejestrację konta](https://aka.ms/EASubCreationPublicPreviewCLI) . Wybierz pozycję **Wypróbuj,** aby otworzyć [Azure Cloud Shell](https://shell.azure.com/). Aby wkleić kod, kliknij prawym przyciskiem myszy okna powłoki i wybierz polecenie **Wklej**.

    ```azurecli-interactive
    az billing enrollment-account list
    ```

    Platforma Azure reaguje na listę kont rejestracji, do których masz dostęp:

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

    Użyj `principalName` właściwości, aby zidentyfikować konto, do którego chcesz udzielić dostępu właściciela RBAC. Skopiuj to `name` konto. Jeśli na przykład chcesz udzielić dostępu właściciela RBAC do SignUpEngineering@contoso.com konta rejestracji, skopiujesz ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` . To jest identyfikator obiektu konta rejestracji. Wklej tę wartość w miejscu, aby można było użyć jej w następnym kroku jako `enrollmentAccountObjectId` .

1. <a id="userObjectId"></a>Pobierz identyfikator obiektu użytkownika lub grupy, do której chcesz przypisać rolę właściciela RBAC

    1. W Azure Portal Wyszukaj **Azure Active Directory**.
    1. Jeśli chcesz udzielić użytkownikowi dostępu, kliknij opcję **Użytkownicy** w menu po lewej stronie. Jeśli chcesz udzielić dostępu do grupy, kliknij przycisk **grupy**.
    1. Wybierz użytkownika lub grupę, do której chcesz przypisać rolę właściciela RBAC.
    1. W przypadku wybrania użytkownika na stronie profil znajdziesz identyfikator obiektu. Jeśli wybrano grupę, identyfikator obiektu będzie się znajdować na stronie Przegląd. Skopiuj identyfikator **objectid** , klikając ikonę po prawej stronie pola tekstowego. Wklej ten element w miejscu, aby można było go używać w następnym kroku jako `userObjectId` .

1. Przyznaj użytkownikowi lub grupie rolę właściciela RBAC na koncie rejestracji

    Korzystając z wartości zebranych w dwóch pierwszych krokach, Udziel użytkownikowi lub grupie roli właściciela RBAC na koncie rejestracji.

    # <a name="rest"></a>[REST](#tab/rest-2)

    Uruchom następujące polecenie, zastępując ```<enrollmentAccountObjectId>``` je `name` skopiowanym w pierwszym kroku ( ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` ). Zamień na ```<userObjectId>``` Identyfikator obiektu skopiowanego z drugiego kroku.

    ```json
    PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

    {
      "properties": {
        "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
        "principalId": "<userObjectId>"
      }
    }
    ```

    Po pomyślnym przypisaniu roli właściciela w zakresie konta rejestracji platforma Azure reaguje na informacje o przypisaniu roli:

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

    Uruchom następujące polecenie [New-AzRoleAssignment](../../role-based-access-control/role-assignments-powershell.md) , zastępując ```<enrollmentAccountObjectId>``` je `ObjectId` zebranym w pierwszym kroku ( ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` ). Zamień na ```<userObjectId>``` Identyfikator obiektu zebrany w drugim kroku.

    ```azurepowershell-interactive
    New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli-2)

    Uruchom następujące polecenie [AZ role przypisanie Create](../../role-based-access-control/role-assignments-cli.md) , zastępując ```<enrollmentAccountObjectId>``` je `name` skopiowanym w pierwszym kroku ( ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` ). Zamień na ```<userObjectId>``` Identyfikator obiektu zebrany w drugim kroku.

    ```azurecli-interactive
    az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    Gdy użytkownik stanie się właścicielem RBAC dla konta rejestracji, może [programistycznie tworzyć](programmatically-create-subscription.md) w nim subskrypcje. Subskrypcja utworzona przez delegowanego użytkownika nadal ma oryginalnego właściciela konta jako administratora usługi, ale domyślnie ma delegowanego użytkownika jako właściciela RBAC.

    ---

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Inspekcja osób, które utworzyły subskrypcje przy użyciu dzienników aktywności

Aby śledzić subskrypcje utworzone za pośrednictwem tego interfejsu API, użyj [interfejsu API dziennika aktywności dzierżawcy](/rest/api/monitor/tenantactivitylogs). Nie jest obecnie możliwe używanie programu PowerShell, interfejsu wiersza polecenia lub Azure Portal do śledzenia tworzenia subskrypcji.

1. Jako administrator dzierżawy usługi Azure Active Directory [podnieś poziom dostępu](../../role-based-access-control/elevate-access-global-admin.md), a następnie przypisz rolę Czytelnika dla zakresu `/providers/microsoft.insights/eventtypes/management` do użytkownika wykonującego inspekcję.
1. Jako użytkownik inspekcji Wywołaj [interfejs API dziennika aktywności dzierżawców](/rest/api/monitor/tenantactivitylogs) , aby zobaczyć działania związane z tworzeniem subskrypcji. Przykład:

    ```
    GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'"
    ```

Aby wygodnie wywołać ten interfejs API z poziomu wiersza polecenia, wypróbuj narzędzie [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="next-steps"></a>Następne kroki

* Teraz, gdy użytkownik lub jednostka usługi ma uprawnienia do tworzenia subskrypcji, możesz użyć tej tożsamości do [programistycznego tworzenia subskrypcji platformy Azure](programmatically-create-subscription.md).
* Aby zapoznać się z przykładem tworzenia subskrypcji przy użyciu platformy .NET, zobacz [przykładowy kod w witrynie GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Aby dowiedzieć się więcej na temat Azure Resource Manager i jego interfejsów API, zobacz [Omówienie usługi Azure Resource Manager](overview.md).
* Aby dowiedzieć się więcej na temat zarządzania dużą liczbą subskrypcji przy użyciu grup zarządzania, zobacz [organizowanie zasobów za pomocą grup zarządzania platformy Azure](../../governance/management-groups/overview.md) .
* Aby wyświetlić kompleksowe wskazówki dotyczące najlepszych rozwiązań dla dużych organizacji w zakresie zarządzania subskrypcjami, zobacz [Azure Enterprise szkielet — zalecenia](/azure/architecture/cloud-adoption-guide/subscription-governance) dotyczące nadzoru subskrypcji
