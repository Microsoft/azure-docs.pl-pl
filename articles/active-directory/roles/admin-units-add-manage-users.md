---
title: Dodawanie, usuwanie i lista użytkowników w jednostce administracyjnej — Azure Active Directory | Microsoft Docs
description: Zarządzanie użytkownikami i ich uprawnieniami roli w jednostce administracyjnej w Azure Active Directory
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
ms.openlocfilehash: 101f1452f547f195288e2d22bc516880100c7735
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107496830"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>Dodawanie użytkowników i zarządzanie nimi w jednostce administracyjnej w Azure Active Directory

W Azure Active Directory (Azure AD) można dodawać użytkowników do jednostki administracyjnej w celu bardziej szczegółowego zakresu administracyjnego kontroli.

Aby przygotować się do korzystania z programu PowerShell Microsoft Graph zarządzania jednostkami administracyjnymi, [zobacz Wprowadzenie.](admin-units-manage.md#get-started)

## <a name="add-users-to-an-administrative-unit"></a>Dodawanie użytkowników do jednostki administracyjnej

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Użytkowników można przypisywać do jednostek administracyjnych pojedynczo lub zbiorczo.

- Przypisz poszczególnych użytkowników z profilu użytkownika:

   1. Zaloguj się do centrum [administracyjnego usługi Azure AD z](https://portal.azure.com) uprawnieniami administratora ról uprzywilejowanych.

   1. Wybierz **pozycję** Użytkownicy, a następnie, aby otworzyć profil użytkownika, wybierz użytkownika, który ma zostać przypisany do jednostki administracyjnej.
   
   1. Wybierz **pozycję Jednostki administracyjne.** 
   
   1. Aby przypisać użytkownika do jednej lub  większej liczby jednostek administracyjnych, wybierz pozycję Przypisz do jednostki administracyjnej, a następnie w okienku po prawej stronie wybierz jednostki administracyjne, do których chcesz przypisać użytkownika.

       ![Zrzut ekranu przedstawiający okienko "Jednostki administracyjne" służące do przypisywania użytkownika do jednostki administracyjnej.](./media/admin-units-add-manage-users/assign-users-individually.png)

- Przypisywanie poszczególnych użytkowników z jednostki administracyjnej:

   1. Zaloguj się do centrum [administracyjnego usługi Azure AD z](https://portal.azure.com) uprawnieniami administratora ról uprzywilejowanych.
   1. Wybierz **pozycję Jednostki** administracyjne, a następnie wybierz jednostkę administracyjną, do której ma zostać przypisany użytkownik.
   1. Wybierz **pozycję Wszyscy użytkownicy**, wybierz  pozycję **Dodaj** członka, a następnie w okienku Dodawanie członka wybierz co najmniej jednego użytkownika, którego chcesz przypisać do jednostki administracyjnej.

        ![Zrzut ekranu przedstawiający okienko "Użytkownicy" jednostki administracyjnej służące do przypisywania użytkownika do jednostki administracyjnej.](./media/admin-units-add-manage-users/assign-to-admin-unit.png)

- Przypisywanie użytkowników jako operacji zbiorczej:

   1. Zaloguj się do centrum [administracyjnego usługi Azure AD z](https://portal.azure.com) uprawnieniami administratora ról uprzywilejowanych.

   1. Wybierz **pozycję Jednostki administracyjne.**

   1. Wybierz jednostkę administracyjną, do której chcesz dodać użytkowników.

   1. Wybierz pozycję **Działania**  >  **zbiorcze użytkowników** Zbiorcze dodawanie  >  **członków.** Następnie możesz pobrać szablon wartości rozdzielanych przecinkami (CSV) i edytować plik. Format jest prosty i wymaga dodania pojedynczej głównej nazwy użytkownika w każdym wierszu. Gdy plik będzie gotowy, zapisz go w odpowiedniej lokalizacji, a następnie przekaż go w ramach tego kroku.

      ![Zrzut ekranu przedstawiający okienko "Użytkownicy" służące do przypisywania użytkowników do jednostki administracyjnej jako operacji zbiorczej.](./media/admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="use-powershell"></a>Korzystanie z programu PowerShell

W programie PowerShell użyj polecenia cmdlet w poniższym przykładzie, aby dodać użytkownika `Add-AzureADAdministrativeUnitMember` do jednostki administracyjnej. Identyfikator obiektu jednostki administracyjnej, do której chcesz dodać użytkownika, oraz identyfikator obiektu użytkownika, który chcesz dodać, są podejmowane jako argumenty. Zmień wyróżnione sekcje zgodnie z wymaganiami dla określonego środowiska.

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$userObj = Get-AzureADUser -Filter "UserPrincipalName eq 'bill@example.onmicrosoft.com'"
Add-AzureADMSAdministrativeUnitMember -Id $adminUnitObj.Id -RefObjectId $userObj.ObjectId
```


### <a name="use-microsoft-graph"></a>Korzystanie z Microsoft Graph

Zastąp symbol zastępczy informacjami testowym i uruchom następujące polecenie:

Żądanie

```http
POST /administrativeUnits/{admin-unit-id}/members/$ref
```

Treść

```http
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/{user-id}"
}
```

Przykład

```http
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/john@example.com"
}
```

## <a name="view-a-list-of-administrative-units-for-a-user"></a>Wyświetlanie listy jednostek administracyjnych dla użytkownika

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

W Azure Portal można otworzyć profil użytkownika, wykonując następujące czynności:

1. Przejdź do **usługi Azure AD,** a następnie wybierz pozycję **Użytkownicy.**

1. Wybierz użytkownika, którego profil chcesz wyświetlić.

1. Wybierz **pozycję Jednostki** administracyjne, aby wyświetlić listę jednostek administracyjnych, do których użytkownik został przypisany.

   ![Zrzut ekranu przedstawiający jednostki administracyjne, do których przypisano użytkownika.](./media/admin-units-add-manage-users/list-user-admin-units.png)

### <a name="use-powershell"></a>Korzystanie z programu PowerShell

Uruchom następujące polecenie:

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -Id $_.ObjectId | where {$_.RefObjectId -eq $userObjId} }
```

> [!NOTE]
> Domyślnie funkcja `Get-AzureADAdministrativeUnitMember` zwraca tylko 100 członków jednostki administracyjnej. Aby pobrać więcej elementów członkowskich, możesz `"-All $true"` dodać .

### <a name="use-microsoft-graph"></a>Korzystanie z Microsoft Graph

Zastąp symbol zastępczy informacjami testowym i uruchom następujące polecenie:

```http
https://graph.microsoft.com/v1.0/users/{user-id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-single-user-from-an-administrative-unit"></a>Usuwanie pojedynczego użytkownika z jednostki administracyjnej

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Użytkownika z jednostki administracyjnej można usunąć na jeden z dwóch sposobów: 

* W witrynie Azure Portal do usługi **Azure AD,** a następnie wybierz pozycję **Użytkownicy.** 
  1. Wybierz użytkownika, aby otworzyć jego profil. 
  1. Wybierz jednostkę administracyjną, z której chcesz usunąć użytkownika, a następnie wybierz **pozycję Usuń z jednostki administracyjnej.**

     ![Zrzut ekranu przedstawiający sposób usuwania użytkownika z jednostki administracyjnej z okienka profilu użytkownika.](./media/admin-units-add-manage-users/user-remove-admin-units.png)

* W witrynie Azure Portal do usługi **Azure AD,** a następnie wybierz pozycję **Jednostki administracyjne.**
  1. Wybierz jednostkę administracyjną, z której chcesz usunąć użytkownika. 
  1. Wybierz użytkownika, a następnie wybierz pozycję **Usuń członka**.
  
     ![Zrzut ekranu przedstawiający sposób usuwania użytkownika na poziomie jednostki administracyjnej.](./media/admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="use-powershell"></a>Korzystanie z programu PowerShell

Uruchom następujące polecenie:

```powershell
Remove-AzureADMSAdministrativeUnitMember -Id $adminUnitId -MemberId $memberUserObjId
```

### <a name="use-microsoft-graph"></a>Korzystanie z Microsoft Graph

Zastąp symbole zastępcze informacjami testowym i uruchom następujące polecenie:

```http
https://graph.microsoft.com/v1.0/directory/administrativeUnits/{admin-unit-id}/members/{user-id}/$ref
```

## <a name="remove-multiple-users-as-a-bulk-operation"></a>Usuwanie wielu użytkowników jako operacji zbiorczej

Aby usunąć wielu użytkowników z jednostki administracyjnej, wykonaj następujące czynności:

1. W witrynie Azure Portal przejdź do usługi **Azure AD.**

1. Wybierz **pozycję Jednostki** administracyjne, a następnie wybierz jednostkę administracyjną, z której chcesz usunąć użytkowników. 

1. Wybierz **pozycję Zbiorcze usuwanie członków,** a następnie pobierz szablon CSV, którego użyjesz, aby wyświetlić listę użytkowników, których chcesz usunąć.

   ![Zrzut ekranu przedstawiający link "Zbiorcze usuwanie członków" w okienku "Użytkownicy".](./media/admin-units-add-manage-users/bulk-user-remove.png)

1. Edytuj pobrany szablon CSV przy użyciu odpowiednich wpisów użytkownika. Nie usuwaj pierwszych dwóch wierszy szablonu. Dodaj jedną główną nazwę użytkownika (UPN) w każdym wierszu.

   ![Zrzut ekranu przedstawiający edytowany plik CSV w celu zbiorczego usuwania użytkowników z jednostki administracyjnej.](./media/admin-units-add-manage-users/bulk-user-entries.png)

1. Zapisz zmiany, przekaż plik, a następnie wybierz pozycję **Prześlij.**

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie roli do jednostki administracyjnej](admin-units-assign-roles.md)
- [Dodawanie grup do jednostki administracyjnej](admin-units-add-manage-groups.md)
