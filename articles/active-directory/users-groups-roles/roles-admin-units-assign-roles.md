---
title: Przypisywanie i wyświetlanie listy ról z zakresem jednostki administracyjnej (wersja zapoznawcza) — Azure Active Directory | Microsoft Docs
description: Ograniczanie zakresu przypisań ról w Azure Active Directory przy użyciu jednostek administracyjnych
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 578fb481ec858e65ede49bdce2d8bc26470aa2ca
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85850763"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>Przypisywanie ról objętych zakresem do jednostki administracyjnej

W Azure Active Directory (Azure AD) można przypisać użytkowników do roli usługi Azure AD z zakresem ograniczonym do co najmniej jednej jednostki administracyjnej (np.) w celu uzyskania bardziej szczegółowej kontroli administracyjnej.

Aby dowiedzieć się, jak przygotować się do korzystania z programu PowerShell i Microsoft Graph do zarządzania jednostką administracyjną, zobacz [wprowadzenie](roles-admin-units-manage.md#get-started).

## <a name="roles-available"></a>Dostępne role

Rola  |  Opis
----- |  -----------
Administrator uwierzytelniania  |  Ma dostęp do wyświetlania, ustawiania i resetowania informacji o metodach uwierzytelniania dla dowolnego użytkownika niebędącego administratorem w przypisanej jednostce administracyjnej.
Administrator grup  |  Może zarządzać wszystkimi aspektami ustawień grup i grup, takimi jak zasady nazewnictwa i wygasania tylko w przypisanej jednostce administracyjnej.
Administrator pomocy technicznej  |  Można resetować hasła dla administratorów systemów innych niż administratorzy i pomocy technicznej tylko w przypisanej jednostce administracyjnej.
Administrator licencji  |  Można przypisywać, usuwać i aktualizować przypisania licencji tylko w ramach jednostki administracyjnej.
Administrator haseł  |  Można resetować hasła dla administratorów nie będących administratorami i haseł tylko w ramach przypisanej jednostki administracyjnej.
Administrator użytkowników  |  Może zarządzać wszystkimi aspektami użytkowników i grup, włącznie z resetowaniem haseł dla ograniczonych administratorów tylko w przypisanej jednostce administracyjnej.

## <a name="assign-a-scoped-role"></a>Przypisywanie roli w zakresie

### <a name="azure-portal"></a>Azure Portal

Przejdź do pozycji **jednostki administracyjne usługi Azure AD >** w portalu. Wybierz jednostkę administracyjną, w której chcesz przypisać rolę do użytkownika. W okienku po lewej stronie wybierz pozycję Role i Administratorzy, aby wyświetlić listę wszystkich dostępnych ról.

![Wybierz jednostkę administracyjną, aby zmienić zakres roli](./media/roles-admin-units-assign-roles/select-role-to-scope.png)

Wybierz rolę, która ma zostać przypisana, a następnie wybierz pozycję **Dodaj przypisania**. Spowoduje to otwarcie panelu po prawej stronie, w którym można wybrać co najmniej jednego użytkownika, który ma zostać przypisany do roli.

![Wybierz rolę do zakresu, a następnie wybierz pozycję Dodaj przypisania](./media/roles-admin-units-assign-roles/select-add-assignment.png)

### <a name="powershell"></a>PowerShell

```powershell
$administrative = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$AdminUser = Get-AzureADUser -ObjectId 'janedoe@fabidentity.onmicrosoft.com'
$uaRoleMemberInfo = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo -Property @{ObjectId = $AdminUser.ObjectId}
Add-AzureADScopedRoleMembership -RoleObjectId $UserAdminRole.ObjectId -ObjectId $administrative unitObj.ObjectId -RoleMemberInfo  $uaRoleMemberInfo
```

Wyróżnioną sekcję można zmienić zgodnie z wymaganiami określonego środowiska.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
POST /administrativeUnits/{id}/scopedRoleMembers
    
Request body
{
  "roleId": "roleId-value",
  "roleMemberInfo": {
    "id": "id-value"
  }
}
```

## <a name="list-the-scoped-admins-on-an-au"></a>Wyświetl listę administratorów o określonym zakresie w usłudze AU

### <a name="azure-portal"></a>Azure Portal

Wszystkie przypisania ról wykonane z zakresem jednostki administracyjnej można wyświetlić w [sekcji jednostki administracyjne w usłudze Azure AD](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit). Przejdź do pozycji **jednostki administracyjne usługi Azure AD >** w portalu. Wybierz jednostkę administracyjną dla przypisań ról, które chcesz wyświetlić. Wybierz **role i Administratorzy** i Otwórz rolę, aby wyświetlić przypisania w jednostce administracyjnej.

### <a name="powershell"></a>PowerShell

```powershell
$administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
Get-AzureADScopedRoleMembership -ObjectId $administrative unitObj.ObjectId | fl *
```

Wyróżnioną sekcję można zmienić zgodnie z wymaganiami określonego środowiska.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
GET /administrativeUnits/{id}/scopedRoleMembers
Request body
{}
```

## <a name="next-steps"></a>Następne kroki

- [Jednostki administracyjne Rozwiązywanie problemów i często zadawane pytania](roles-admin-units-faq-troubleshoot.md)
