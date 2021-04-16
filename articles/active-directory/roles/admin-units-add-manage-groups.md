---
title: Dodawanie, usuwanie i lista grup w jednostce administracyjnej — Azure Active Directory | Microsoft Docs
description: Zarządzanie grupami i ich uprawnieniami roli w jednostce administracyjnej w Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 03/10/2021
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ad8cce8375ecd670a481541a091e36aacb41240
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505296"
---
# <a name="add-and-manage-groups-in-an-administrative-unit-in-azure-active-directory"></a>Dodawanie grup i zarządzanie nimi w jednostce administracyjnej w Azure Active Directory

W Azure Active Directory (Azure AD) można dodawać grupy do jednostki administracyjnej w celu bardziej szczegółowego zakresu administracyjnego kontroli.

Aby przygotować się do korzystania z programu PowerShell i Microsoft Graph do zarządzania jednostkami administracyjnymi, zobacz [Wprowadzenie.](admin-units-manage.md#get-started)

## <a name="add-groups-to-an-administrative-unit"></a>Dodawanie grup do jednostki administracyjnej

Grupy można dodawać do jednostki administracyjnej przy użyciu Azure Portal, programu PowerShell lub Microsoft Graph.

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Do jednostki administracyjnej można przypisać tylko poszczególne grupy. Nie ma możliwości przypisania grup jako operacji zbiorczej. W Azure Portal można przypisać grupę do jednostki administracyjnej na jeden z dwóch sposobów:

* W **okienku** Grupy:

  1. W witrynie Azure Portal przejdź do usługi **Azure AD.**
  1. Wybierz **pozycję** Grupy, a następnie wybierz grupę, którą chcesz przypisać do jednostki administracyjnej. 
  1. W okienku po lewej stronie wybierz pozycję **Jednostki** administracyjne, aby wyświetlić listę jednostek administracyjnych, do których przypisano grupę. 

     ![Zrzut ekranu przedstawiający link "Przypisz do jednostki administracyjnej" w okienku "Jednostki administracyjne".](./media/admin-units-add-manage-groups/assign-to-group-1.png)

  1. Wybierz **pozycję Przypisz do jednostki administracyjnej.**
  1. W okienku po prawej stronie wybierz jednostkę administracyjną.

* W **okienku Wszystkie**  >  **grupy jednostek administracyjnych:**

  1. W witrynie Azure Portal przejdź do usługi **Azure AD.**
  
  1. W okienku po lewej stronie wybierz **pozycję Jednostki administracyjne,** a następnie wybierz **pozycję Wszystkie grupy.** 
     Wszystkie grupy, które są już przypisane do jednostki administracyjnej są wyświetlane w okienku po prawej stronie. 

  1. W **okienku Grupy** wybierz pozycję **Dodaj**.
    W okienku po prawej stronie jest dostępna lista wszystkich dostępnych grup w organizacji usługi Azure AD. 

     ![Zrzut ekranu przedstawiający przycisk "Dodaj" dodawania grupy do jednostki administracyjnej.](./media/admin-units-add-manage-groups/assign-to-admin-unit.png)

  1. Wybierz co najmniej jedną grupy do przypisania do jednostki administracyjnej, a następnie wybierz **przycisk** Wybierz.

### <a name="use-powershell"></a>Korzystanie z programu PowerShell

W poniższym przykładzie użyj `Add-AzureADMSAdministrativeUnitMember` polecenia cmdlet , aby dodać grupę do jednostki administracyjnej. Identyfikator obiektu jednostki administracyjnej i identyfikator obiektu grupy do dodania są podejmowane jako argumenty. Zmień wyróżnione sekcje zgodnie z wymaganiami dla określonego środowiska.


```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
Add-AzureADMSAdministrativeUnitMember -Id $adminUnitObj.Id -RefObjectId $GroupObj.ObjectId
```

### <a name="use-microsoft-graph"></a>Korzystanie z Microsoft Graph

Uruchom następujące polecenia:

Żądanie

```http
POST /administrativeUnits/{admin-unit-id}/members/$ref
```

Treść

```http
{
"@odata.id":"https://graph.microsoft.com/v1.0/groups/{group-id}"
}
```

Przykład

```http
{
"@odata.id":"https://graph.microsoft.com/v1.0/groups/871d21ab-6b4e-4d56-b257-ba27827628f3"
}
```

## <a name="view-a-list-of-groups-in-an-administrative-unit"></a>Wyświetlanie listy grup w jednostce administracyjnej

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

1. W witrynie Azure Portal przejdź do usługi **Azure AD.**

1. W okienku po lewej stronie wybierz **pozycję Jednostki administracyjne,** a następnie wybierz jednostkę administracyjną, której grupy chcesz wyświetlić. Domyślnie w **okienku po** lewej stronie jest zaznaczona opcja Wszyscy użytkownicy. 

1. W okienku po lewej stronie wybierz pozycję **Grupy**. W okienku po prawej stronie zostanie wyświetlona lista grup, które są członkami wybranej jednostki administracyjnej.

   ![Zrzut ekranu przedstawiający okienko "Grupy" z listą grup w jednostce administracyjnej.](./media/admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="use-powershell"></a>Korzystanie z programu PowerShell

Aby wyświetlić listę wszystkich członków jednostki administracyjnej, uruchom następujące polecenie: 

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
Get-AzureADMSAdministrativeUnitMember -Id $adminUnitObj.Id
```

Aby wyświetlić wszystkie grupy, które są członkami jednostki administracyjnej, użyj następującego fragmentu kodu:

```powershell
foreach ($member in (Get-AzureADMSAdministrativeUnitMember -Id $adminUnitObj.Id)) 
{
if($member.ObjectType -eq "Group")
{
Get-AzureADGroup -ObjectId $member.ObjectId
}
}
```

### <a name="use-microsoft-graph"></a>Korzystanie z Microsoft Graph

Uruchom następujące polecenie:

Żądanie

```http
GET /directory/administrativeUnits/{admin-unit-id}/members/$/microsoft.graph.group
```

Treść

```http
{}
```

## <a name="view-a-list-of-administrative-units-for-a-group"></a>Wyświetlanie listy jednostek administracyjnych dla grupy

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

1. W witrynie Azure Portal przejdź do usługi **Azure AD.**

1. W okienku po lewej stronie wybierz **pozycję Grupy,** aby wyświetlić listę grup.

1. Wybierz grupę, aby otworzyć profil grupy. 

1. W okienku po lewej stronie wybierz **pozycję Jednostki administracyjne,** aby wyświetlić listę wszystkich jednostek administracyjnych, których członkiem jest grupa.

   ![Zrzut ekranu przedstawiający okienko "Jednostki administracyjne" z wyświetloną listą jednostek administracyjnych, do których przypisano grupę.](./media/admin-units-add-manage-groups/list-group-au.png)

### <a name="use-powershell"></a>Korzystanie z programu PowerShell

Uruchom następujące polecenie:

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }
```

### <a name="use-microsoft-graph"></a>Korzystanie z Microsoft Graph

Uruchom następujące polecenie:

```http
https://graph.microsoft.com/v1.0/groups/{group-id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-group-from-an-administrative-unit"></a>Usuwanie grupy z jednostki administracyjnej

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Grupę można usunąć z jednostki administracyjnej w Azure Portal na jeden z dwóch sposobów:

- Usuń go z przeglądu grupy:

  1. W witrynie Azure Portal przejdź do usługi **Azure AD.**
  1. W okienku po lewej stronie wybierz **pozycję Grupy,** a następnie otwórz profil grupy, którą chcesz usunąć z jednostki administracyjnej.
  1. W okienku po lewej stronie wybierz pozycję **Jednostki administracyjne,** aby wyświetlić listę wszystkich jednostek administracyjnych, do których przypisano grupę. 
  1. Wybierz jednostkę administracyjną, z której chcesz usunąć grupę, a następnie wybierz **pozycję Usuń z jednostki administracyjnej.**

     ![Zrzut ekranu przedstawiający okienko "Jednostki administracyjne" z listą grup przypisanych do wybranej jednostki administracyjnej.](./media/admin-units-add-manage-groups/group-au-remove.png)

- Usuń go z jednostki administracyjnej:

  1. W witrynie Azure Portal przejdź do usługi **Azure AD.**
  1. W okienku po lewej stronie wybierz **pozycję Jednostki administracyjne,** a następnie wybierz jednostkę administracyjną, do których przypisano grupę.
  1. W okienku po lewej stronie wybierz **pozycję Grupy,** aby wyświetlić listę wszystkich grup przypisanych do jednostki administracyjnej.
  1. Wybierz grupę, którą chcesz usunąć, a następnie wybierz pozycję **Usuń grupy.**

    ![Zrzut ekranu przedstawiający okienko "Grupy" z listą grup w jednostce administracyjnej.](./media/admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="use-powershell"></a>Korzystanie z programu PowerShell

Uruchom następujące polecenie:

```powershell
Remove-AzureADMSAdministrativeUnitMember -ObjectId $adminUnitId -MemberId $memberGroupObjId
```

### <a name="use-microsoft-graph"></a>Korzystanie z Microsoft Graph

Uruchom następujące polecenie:

```http
https://graph.microsoft.com/v1.0/directory/administrativeUnits/{admin-unit-id}/members/{group-id}/$ref
```

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie roli do jednostki administracyjnej](admin-units-assign-roles.md)
- [Zarządzanie użytkownikami w jednostce administracyjnej](admin-units-add-manage-users.md)
