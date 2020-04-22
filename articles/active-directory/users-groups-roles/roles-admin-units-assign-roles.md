---
title: Przypisywanie i wyświetlanie ról z zakresem jednostki administracyjnej (wersja zapoznawcza) — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Ograniczanie zakresu przypisań ról w usłudze Azure Active Directory za pomocą jednostek administracyjnych
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: c900b2a363a4ff271e7a436b358ecf170daca163
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687017"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>Przypisywanie ról o określonym zakresie do jednostki administracyjnej

W usłudze Azure Active Directory (Azure AD) można przypisać użytkowników do roli usługi Azure AD o zakresie ograniczonym do co najmniej jednej jednostki administracyjnej (AU) w celu uzyskania bardziej szczegółowej kontroli administracyjnej.

Aby uzyskać instrukcje przygotowania do używania programów PowerShell i Microsoft Graph do zarządzania jednostkami administracyjnymi, zobacz [Wprowadzenie](roles-admin-units-manage.md#get-started).

## <a name="roles-available"></a>Dostępne role

Rola  |  Opis
----- |  -----------
Administrator uwierzytelniania  |  Ma dostęp do wyświetlania, ustawiania i resetowania informacji o metodzie uwierzytelniania dla każdego użytkownika niebędącego administratorem tylko w przypisanej jednostce administracyjnej.
Administrator grup  |  Może zarządzać wszystkimi aspektami ustawień grup i grup, takimi jak zasady nazewnictwa i wygasania tylko w przypisanej jednostce administracyjnej.
Administrator działu pomocy technicznej  |  Można resetować hasła tylko dla administratorów niebędących administratorami i administratorami pomocy technicznej w przypisanej jednostce administracyjnej.
Administrator licencji  |  Można przypisywać, usuwać i aktualizować przypisania licencji tylko w jednostce administracyjnej.
Administrator haseł  |  Można resetować hasła tylko dla administratorów niebędących administratorami i administratorami haseł tylko w ramach przypisanej jednostki administracyjnej.
Administrator użytkowników  |  Może zarządzać wszystkimi aspektami użytkowników i grup, w tym resetowaniem haseł dla ograniczonych administratorów tylko w ramach przypisanej jednostki administracyjnej.

## <a name="assign-a-scoped-role"></a>Przypisywanie roli o określonym zakresie

### <a name="azure-portal"></a>Azure Portal

Przejdź do **jednostki administracyjne usługi Azure AD >** w portalu. Wybierz jednostkę administracyjną, nad którą chcesz przypisać rolę do użytkownika. W lewym okienku wybierz pozycję Role i administratorzy, aby wyświetlić listę wszystkich dostępnych ról.

![Wybierz jednostkę administracyjną, aby zmienić zakres roli](./media/roles-admin-units-assign-roles/select-role-to-scope.png)

Wybierz rolę, która ma zostać przypisana, a następnie wybierz pozycję **Dodaj przydziały**. Spowoduje to przesunięcie panelu po prawej stronie, w którym można wybrać jednego lub więcej użytkowników, którzy mają zostać przypisani do roli.

![Wybierz rolę, która ma być objęte zakresem, a następnie wybierz pozycję Dodaj przydziały](./media/roles-admin-units-assign-roles/select-add-assignment.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $AdminUser = Get-AzureADUser -ObjectId 'janedoe@fabidentity.onmicrosoft.com'
    $uaRoleMemberInfo = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo -Property @{ObjectId = $AdminUser.ObjectId}
    Add-AzureADScopedRoleMembership -RoleObjectId $UserAdminRole.ObjectId -ObjectId $administrative unitObj.ObjectId -RoleMemberInfo  $uaRoleMemberInfo

Wyróżniona sekcja może zostać zmieniona zgodnie z wymaganiami dla określonego środowiska.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    POST /administrativeUnits/{id}/scopedRoleMembers
    
    Request body
    {
      "roleId": "roleId-value",
      "roleMemberInfo": {
        "id": "id-value"
      }
    }

## <a name="list-the-scoped-admins-on-an-au"></a>Wyświetlanie listy administratorów o określonym zakresie w uisznej uiszczeń

### <a name="azure-portal"></a>Azure Portal

Wszystkie przypisania ról wykonane za pomocą zakresu jednostki administracyjnej można wyświetlić w [sekcji Jednostki administracyjne usługi Azure AD](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit). Przejdź do **jednostki administracyjne usługi Azure AD >** w portalu. Wybierz jednostkę administracyjną dla przypisań ról, które chcesz wyświetlić. Wybierz **pozycję Role i administratorzy** oraz otwórz rolę, aby wyświetlić przydziały w jednostce administracyjnej.

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    Get-AzureADScopedRoleMembership -ObjectId $administrative unitObj.ObjectId | fl *

Wyróżniona sekcja może zostać zmieniona zgodnie z wymaganiami dla określonego środowiska.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    GET /administrativeUnits/{id}/scopedRoleMembers
    Request body
    {}

## <a name="next-steps"></a>Następne kroki

- [Rozwiązywanie problemów z jednostkami administracyjnymi i często zadawane pytania](roles-admin-units-faq-troubleshoot.md)
