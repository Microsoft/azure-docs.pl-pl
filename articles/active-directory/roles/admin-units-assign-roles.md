---
title: Przypisywanie i wyświetlanie listy ról z zakresem jednostki administracyjnej — Azure Active Directory | Microsoft Docs
description: Użyj jednostek administracyjnych, aby ograniczyć zakres przypisań ról w Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecfa9186ef42d4822c9b3053d76b7c0160841621
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98740401"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>Przypisywanie ról objętych zakresem do jednostki administracyjnej

W Azure Active Directory (Azure AD), aby uzyskać bardziej szczegółową kontrolę administracyjną, można przypisać użytkowników do roli usługi Azure AD z zakresem ograniczonym do co najmniej jednej jednostki administracyjnej.

Aby przygotować się do korzystania z programu PowerShell i Microsoft Graph do zarządzania jednostką administracyjną, zobacz [wprowadzenie](admin-units-manage.md#get-started).

## <a name="available-roles"></a>Dostępne role

Rola  |  Opis
----- |  -----------
Administrator uwierzytelniania  |  Ma dostęp do wyświetlania, ustawiania i resetowania informacji o metodach uwierzytelniania dla dowolnego użytkownika niebędącego administratorem w przypisanej jednostce administracyjnej.
Administrator grup  |  Może zarządzać wszystkimi aspektami ustawień grup i grup, takimi jak zasady nazewnictwa i wygasania, tylko w przypisanej jednostce administracyjnej.
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

Rolę objętą zakresem można przypisać przy użyciu Azure Portal, programu PowerShell lub Microsoft Graph.

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

1. W Azure Portal przejdź do **usługi Azure AD**.

1. Wybierz pozycję **jednostki administracyjne**, a następnie wybierz jednostkę administracyjną, do której chcesz przypisać zakres roli użytkownika. 

1. W okienku po lewej stronie wybierz pozycję **role i Administratorzy** , aby wyświetlić listę wszystkich dostępnych ról.

   ![Zrzut ekranu okienka "rola i Administratorzy" służący do wybierania jednostki administracyjnej, której zakres roli chcesz przypisać.](./media/admin-units-assign-roles/select-role-to-scope.png)

1. Wybierz rolę, która ma zostać przypisana, a następnie wybierz pozycję **Dodaj przypisania**. 

1. W okienku **Dodaj przypisania** wybierz co najmniej jednego użytkownika, który ma zostać przypisany do roli.

   ![Wybierz rolę do zakresu, a następnie wybierz pozycję Dodaj przypisania](./media/admin-units-assign-roles/select-add-assignment.png)

> [!Note]
> Aby przypisać rolę w jednostce administracyjnej przy użyciu Azure AD Privileged Identity Management (PIM), zobacz [Przypisywanie ról usługi Azure AD w usłudze PIM](../privileged-identity-management/pim-how-to-add-role-to-user.md?tabs=new#assign-a-role-with-restricted-scope).

### <a name="use-powershell"></a>Korzystanie z programu PowerShell

```powershell
$AdminUser = Get-AzureADUser -ObjectId "Use the user's UPN, who would be an admin on this unit"
$Role = Get-AzureADDirectoryRole | Where-Object -Property DisplayName -EQ -Value "User Account Administrator"
$administrativeUnit = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
$RoleMember = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo
$RoleMember.ObjectId = $AdminUser.ObjectId
Add-AzureADMSScopedRoleMembership -ObjectId $administrativeUnit.ObjectId -RoleObjectId $Role.ObjectId -RoleMemberInfo $RoleMember
```

Wyróżnioną sekcję można zmienić zgodnie z wymaganiami określonego środowiska.

### <a name="use-microsoft-graph"></a>Użyj Microsoft Graph

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

## <a name="view-a-list-of-the-scoped-admins-in-an-administrative-unit"></a>Wyświetlanie listy administratorów o określonym zakresie w jednostce administracyjnej

Listę administratorów o określonym zakresie można wyświetlić za pomocą Azure Portal, programu PowerShell lub Microsoft Graph.

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Wszystkie przypisania ról utworzone przy użyciu zakresu jednostki administracyjnej można wyświetlić w [sekcji jednostki administracyjne w usłudze Azure AD](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit). 

1. W Azure Portal przejdź do **usługi Azure AD**.

1. W lewym okienku wybierz pozycję **jednostki administracyjne**, a następnie wybierz jednostkę administracyjną dla listy przypisań ról, które chcesz wyświetlić. 

1. Wybierz **role i Administratorzy**, a następnie otwórz rolę, aby wyświetlić przypisania w jednostce administracyjnej.

### <a name="use-powershell"></a>Korzystanie z programu PowerShell

```powershell
$administrativeUnit = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
Get-AzureADMSScopedRoleMembership -ObjectId $administrativeUnit.ObjectId | fl *
```

Wyróżnioną sekcję można zmienić zgodnie z wymaganiami określonego środowiska.

### <a name="use-microsoft-graph"></a>Użyj Microsoft Graph

```http
Http request
GET /directory/administrativeUnits/{id}/scopedRoleMembers
Request body
{}
```

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie przypisaniami ról za pomocą grup w chmurze](groups-concept.md)
- [Rozwiązywanie problemów z rolami przypisanymi do grup chmury](groups-faq-troubleshooting.md)
