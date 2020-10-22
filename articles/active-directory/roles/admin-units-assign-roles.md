---
title: Przypisywanie i wyświetlanie listy ról z zakresem jednostki administracyjnej — Azure Active Directory | Microsoft Docs
description: Ograniczanie zakresu przypisań ról w Azure Active Directory przy użyciu jednostek administracyjnych
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 09/22/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66a4810b3a84cac55a49744025b6ac71c3f1c0a7
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92377951"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>Przypisywanie ról objętych zakresem do jednostki administracyjnej

W Azure Active Directory (Azure AD) można przypisać użytkowników do roli usługi Azure AD z zakresem ograniczonym do co najmniej jednej jednostki administracyjnej (np.) w celu uzyskania bardziej szczegółowej kontroli administracyjnej.

Aby dowiedzieć się, jak przygotować się do korzystania z programu PowerShell i Microsoft Graph do zarządzania jednostką administracyjną, zobacz [wprowadzenie](admin-units-manage.md#get-started).

## <a name="roles-available"></a>Dostępne role

Rola  |  Opis
----- |  -----------
Administrator uwierzytelniania  |  Ma dostęp do wyświetlania, ustawiania i resetowania informacji o metodach uwierzytelniania dla dowolnego użytkownika niebędącego administratorem w przypisanej jednostce administracyjnej.
Administrator grup  |  Może zarządzać wszystkimi aspektami ustawień grup i grup, takimi jak zasady nazewnictwa i wygasania tylko w przypisanej jednostce administracyjnej.
Administrator pomocy technicznej  |  Można resetować hasła dla administratorów systemów innych niż administratorzy i pomocy technicznej tylko w przypisanej jednostce administracyjnej.
Administrator licencji  |  Można przypisywać, usuwać i aktualizować przypisania licencji tylko w ramach jednostki administracyjnej.
Administrator haseł  |  Można resetować hasła dla administratorów nie będących administratorami i haseł tylko w ramach przypisanej jednostki administracyjnej.
Administrator użytkowników  |  Może zarządzać wszystkimi aspektami użytkowników i grup, włącznie z resetowaniem haseł dla ograniczonych administratorów tylko w przypisanej jednostce administracyjnej.

## <a name="security-principals-that-can-be-assigned-to-a-scoped-role"></a>Podmioty zabezpieczeń, które mogą być przypisane do roli o określonym zakresie

Następujące podmioty zabezpieczeń można przypisać do roli z zakresem jednostki administracyjnej:

* Użytkownicy
* Grupy w chmurze, które można przypisać do ról (wersja zapoznawcza)
* Główna nazwa usługi

## <a name="assign-a-scoped-role"></a>Przypisywanie roli w zakresie

### <a name="azure-portal"></a>Azure Portal

Przejdź do pozycji **jednostki administracyjne usługi Azure AD >** w portalu. Wybierz jednostkę administracyjną, w której chcesz przypisać rolę do użytkownika. W okienku po lewej stronie wybierz pozycję Role i Administratorzy, aby wyświetlić listę wszystkich dostępnych ról.

![Wybierz jednostkę administracyjną, aby zmienić zakres roli](./media/admin-units-assign-roles/select-role-to-scope.png)

Wybierz rolę, która ma zostać przypisana, a następnie wybierz pozycję **Dodaj przypisania**. Zostanie otwarty panel po prawej stronie, w którym można wybrać co najmniej jednego użytkownika, który ma zostać przypisany do roli.

![Wybierz rolę do zakresu, a następnie wybierz pozycję Dodaj przypisania](./media/admin-units-assign-roles/select-add-assignment.png)

> [!Note]
>
> Aby przypisać rolę w jednostce administracyjnej przy użyciu programu PIM, wykonaj kroki opisane [tutaj](../privileged-identity-management/pim-how-to-add-role-to-user.md?tabs=new#assign-a-role-with-restricted-scope).

### <a name="powershell"></a>PowerShell

```powershell
$AdminUser = Get-AzureADUser -ObjectId "Use the user's UPN, who would be an admin on this unit"
$Role = Get-AzureADDirectoryRole | Where-Object -Property DisplayName -EQ -Value "User Account Administrator"
$administrativeUnit = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
$RoleMember = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo
$RoleMember.ObjectId = $AdminUser.ObjectId
Add-AzureADMSScopedRoleMembership -ObjectId $administrativeUnit.ObjectId -RoleObjectId $Role.ObjectId -RoleMemberInfo $RoleMember
```

Wyróżnioną sekcję można zmienić zgodnie z wymaganiami określonego środowiska.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
POST /directory/administrativeUnits/{id}/scopedRoleMembers
    
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
$administrativeUnit = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
Get-AzureADMSScopedRoleMembership -ObjectId $administrativeUnit.ObjectId | fl *
```

Wyróżnioną sekcję można zmienić zgodnie z wymaganiami określonego środowiska.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
GET /directory/administrativeUnits/{id}/scopedRoleMembers
Request body
{}
```

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie przypisaniami ról przy użyciu grup chmur](groups-concept.md)
- [Rozwiązywanie problemów z rolami przypisanymi do grup w chmurze](groups-faq-troubleshooting.md)
