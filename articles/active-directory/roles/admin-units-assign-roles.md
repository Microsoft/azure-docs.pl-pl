---
title: Przypisywanie ról i ich lista z zakresem jednostki administracyjnej — Azure Active Directory | Microsoft Docs
description: Użyj jednostek administracyjnych, aby ograniczyć zakres przypisań ról w Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 04/14/2021
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: e24bf5df84015ada6b62c35fdd29571c66e06ebd
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505262"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>Przypisywanie ról o zakresie do jednostki administracyjnej

W Azure Active Directory (Azure AD) w celu bardziej szczegółowej kontroli administracyjnej można przypisać użytkowników do roli usługi Azure AD z zakresem ograniczonym do co najmniej jednej jednostki administracyjnej.

Aby przygotować się do korzystania z programu PowerShell i Microsoft Graph do zarządzania jednostkami administracyjnymi, zobacz [Wprowadzenie.](admin-units-manage.md#get-started)

## <a name="available-roles"></a>Dostępne role

Rola  |  Opis
----- |  -----------
Administrator uwierzytelniania  |  Ma dostęp do wyświetlania, ustawianie i resetowanie informacji o metodzie uwierzytelniania dla każdego użytkownika bez uprawnień administratora tylko w przypisanej jednostce administracyjnej.
Administrator grup  |  Może zarządzać wszystkimi aspektami ustawień grup i grup, takimi jak zasady nazewnictwa i wygasania, tylko w przypisanej jednostce administracyjnej.
Administrator pomocy technicznej  |  Może resetować hasła tylko dla osób niebędących administratorami i administratorów pomocy technicznej w przypisanej jednostce administracyjnej.
Administrator licencji  |  Może przypisywać, usuwać i aktualizować przypisania licencji tylko w ramach jednostki administracyjnej.
Administrator haseł  |  Może resetować hasła tylko dla osób niebędących administratorami i administratorów haseł w ramach przypisanej jednostki administracyjnej.
Administrator użytkowników  |  Może zarządzać wszystkimi aspektami użytkowników i grup, w tym resetowaniem haseł tylko dla ograniczonych administratorów w ramach przypisanej jednostki administracyjnej.

## <a name="security-principals-that-can-be-assigned-to-a-scoped-role"></a>Podmioty zabezpieczeń, które można przypisać do roli o zakresie

Do roli z zakresem jednostki administracyjnej można przypisać następujące podmioty zabezpieczeń:

* Użytkownicy
* Grupy chmury przypisane do ról (wersja zapoznawcza)
* Główna nazwa usługi

## <a name="assign-a-scoped-role"></a>Przypisywanie roli o zakresie

Rolę o zakresie można przypisać przy użyciu Azure Portal programu PowerShell lub Microsoft Graph.

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

1. W witrynie Azure Portal przejdź do usługi **Azure AD.**

1. Wybierz **pozycję Jednostki** administracyjne, a następnie wybierz jednostkę administracyjną, do której chcesz przypisać zakres roli użytkownika. 

1. W okienku po lewej stronie wybierz **pozycję Role i administratorzy,** aby wyświetlić listę wszystkich dostępnych ról.

   ![Zrzut ekranu przedstawiający okienko "Rola i administratorzy" służące do wybierania jednostki administracyjnej, której zakres roli chcesz przypisać.](./media/admin-units-assign-roles/select-role-to-scope.png)

1. Wybierz rolę, która ma zostać przypisana, a następnie wybierz **pozycję Dodaj przypisania.** 

1. W **okienku Dodawanie przypisań** wybierz co najmniej jednego użytkownika, który ma zostać przypisany do roli.

   ![Wybierz rolę do zakresu, a następnie wybierz pozycję Dodaj przypisania](./media/admin-units-assign-roles/select-add-assignment.png)

> [!Note]
> Aby przypisać rolę w jednostce administracyjnej przy użyciu usługi Azure AD Privileged Identity Management (PIM), zobacz [Przypisywanie ról usługi Azure AD w usłudze PIM.](../privileged-identity-management/pim-how-to-add-role-to-user.md?tabs=new#assign-a-role-with-restricted-scope)

### <a name="use-powershell"></a>Korzystanie z programu PowerShell

```powershell
$adminUser = Get-AzureADUser -ObjectId "Use the user's UPN, who would be an admin on this unit"
$role = Get-AzureADDirectoryRole | Where-Object -Property DisplayName -EQ -Value "User Administrator"
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
$roleMember = New-Object -TypeName Microsoft.Open.MSGraph.Model.MsRoleMemberInfo
$roleMember.Id = $adminUser.ObjectId
Add-AzureADMSScopedRoleMembership -Id $adminUnitObj.Id -RoleId $role.ObjectId -RoleMemberInfo $roleMember
```

Wyróżnione sekcje można zmienić zgodnie z wymaganiami dla określonego środowiska.

### <a name="use-microsoft-graph"></a>Korzystanie z Microsoft Graph

Żądanie

```http
POST /directory/administrativeUnits/{admin-unit-id}/scopedRoleMembers
```
    
Treść

```http
{
  "roleId": "roleId-value",
  "roleMemberInfo": {
    "id": "id-value"
  }
}
```

## <a name="view-a-list-of-the-scoped-admins-in-an-administrative-unit"></a>Wyświetlanie listy administratorów z zakresem w jednostce administracyjnej

Listę administratorów z zakresami można wyświetlić przy użyciu Azure Portal, Programu PowerShell lub Microsoft Graph.

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Wszystkie przypisania ról utworzone z zakresem jednostki administracyjnej można wyświetlić w sekcji [Jednostki administracyjne usługi Azure AD.](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit) 

1. W witrynie Azure Portal przejdź do usługi **Azure AD.**

1. W okienku po lewej stronie wybierz pozycję **Jednostki** administracyjne, a następnie wybierz jednostkę administracyjną z listy przypisań ról, które chcesz wyświetlić. 

1. Wybierz **pozycję Role i administratorzy,** a następnie otwórz rolę, aby wyświetlić przypisania w jednostce administracyjnej.

### <a name="use-powershell"></a>Korzystanie z programu PowerShell

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
Get-AzureADMSScopedRoleMembership -Id $adminUnitObj.Id | fl *
```

Wyróżnione sekcje można zmienić zgodnie z wymaganiami dla określonego środowiska.

### <a name="use-microsoft-graph"></a>Korzystanie z Microsoft Graph

Żądanie

```http
GET /directory/administrativeUnits/{admin-unit-id}/scopedRoleMembers
```

Treść

```http
{}
```

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie przypisaniami ról za pomocą grup w chmurze](groups-concept.md)
- [Rozwiązywanie problemów z rolami przypisanymi do grup w chmurze](groups-faq-troubleshooting.md)
