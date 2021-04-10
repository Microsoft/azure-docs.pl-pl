---
title: Dodawanie, usuwanie i wyświetlanie listy użytkowników w jednostce administracyjnej — Azure Active Directory | Microsoft Docs
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
ms.openlocfilehash: 7a9d80344a31023d174935e7f785e36102e99eba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103011561"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>Dodawanie użytkowników i zarządzanie nimi w jednostce administracyjnej w Azure Active Directory

W Azure Active Directory (Azure AD) można dodać użytkowników do jednostki administracyjnej, aby uzyskać bardziej szczegółowy zakres administracyjny kontroli.

Aby przygotować się do korzystania z programu PowerShell i Microsoft Graph do zarządzania jednostką administracyjną, zobacz [wprowadzenie](admin-units-manage.md#get-started).

## <a name="add-users-to-an-administrative-unit"></a>Dodawanie użytkowników do jednostki administracyjnej

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Użytkowników można przypisywać do jednostek administracyjnych pojedynczo lub jako operacje zbiorcze.

- Przypisywanie poszczególnych użytkowników z profilu użytkownika:

   1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://portal.azure.com) przy użyciu uprawnień administratora ról uprzywilejowanych.

   1. Wybierz pozycję **Użytkownicy** , a następnie, aby otworzyć profil użytkownika, wybierz użytkownika, który ma zostać przypisany do jednostki administracyjnej.
   
   1. Wybierz pozycję **jednostki administracyjne**. 
   
   1. Aby przypisać użytkownika do co najmniej jednej jednostki administracyjnej, wybierz pozycję **Przypisz do jednostki administracyjnej** , a następnie w okienku po prawej stronie Wybierz jednostki administracyjne, do których chcesz przypisać użytkownika.

       ![Zrzut ekranu przedstawiający okienko "jednostki administracyjne" służące do przypisywania użytkownika do jednostki administracyjnej.](./media/admin-units-add-manage-users/assign-users-individually.png)

- Przypisz poszczególnych użytkowników z jednostki administracyjnej:

   1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://portal.azure.com) przy użyciu uprawnień administratora ról uprzywilejowanych.
   1. Wybierz pozycję **jednostki administracyjne**, a następnie wybierz jednostkę administracyjną, do której użytkownik ma zostać przypisany.
   1. Wybierz pozycję **Wszyscy użytkownicy**, wybierz pozycję **Dodaj członka** , a następnie w okienku **Dodaj członka** wybierz co najmniej jednego użytkownika, który ma zostać przypisany do jednostki administracyjnej.

        ![Zrzut ekranu przedstawiający okienko "Użytkownicy" jednostki administracyjnej umożliwiające przypisanie użytkownika do jednostki administracyjnej.](./media/admin-units-add-manage-users/assign-to-admin-unit.png)

- Przypisz użytkowników jako operację zbiorczą:

   1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://portal.azure.com) przy użyciu uprawnień administratora ról uprzywilejowanych.

   1. Wybierz pozycję **jednostki administracyjne**.

   1. Wybierz jednostkę administracyjną, do której chcesz dodać użytkowników.

   1. Wybierz pozycję **Użytkownicy** masowa  >  **działania**  >  **zbiorcze Dodaj członków**. Następnie można pobrać szablon wartości rozdzielonych przecinkami (CSV) i edytować plik. Format jest prosty i wymaga dodania pojedynczej nazwy głównej użytkownika w każdym wierszu. Gdy plik jest gotowy, Zapisz go w odpowiedniej lokalizacji, a następnie Przekaż go w ramach tego kroku.

      ![Zrzut ekranu przedstawiający okienko "Użytkownicy" służący do przypisywania użytkowników do jednostki administracyjnej jako operacji zbiorczej.](./media/admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="use-powershell"></a>Korzystanie z programu PowerShell

W programie PowerShell Użyj `Add-AzureADAdministrativeUnitMember` polecenia cmdlet w poniższym przykładzie, aby dodać użytkownika do jednostki administracyjnej. Identyfikator obiektu jednostki administracyjnej, do której chcesz dodać użytkownika i identyfikator obiektu użytkownika, który chcesz dodać, jest traktowany jako argumenty. Zmień podświetloną sekcję jako wymaganą dla określonego środowiska.

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$userObj = Get-AzureADUser -Filter "UserPrincipalName eq 'bill@example.onmicrosoft.com'"
Add-AzureADMSAdministrativeUnitMember -Id $adminUnitObj.ObjectId -RefObjectId $userObj.ObjectId
```


### <a name="use-microsoft-graph"></a>Użyj Microsoft Graph

Zastąp symbol zastępczy informacjami o teście i uruchom następujące polecenie:

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

1. Przejdź do **usługi Azure AD**, a następnie wybierz pozycję **Użytkownicy**.

1. Wybierz użytkownika, którego profil chcesz wyświetlić.

1. Wybierz pozycję **jednostki administracyjne** , aby wyświetlić listę jednostek administracyjnych, do których użytkownik został przypisany.

   ![Zrzut ekranu przedstawiający jednostki administracyjne, do których przypisano użytkownika.](./media/admin-units-add-manage-users/list-user-admin-units.png)

### <a name="use-powershell"></a>Korzystanie z programu PowerShell

Uruchom następujące polecenie:

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -Id $_.ObjectId | where {$_.RefObjectId -eq $userObjId} }
```

> [!NOTE]
> Domyślnie `Get-AzureADAdministrativeUnitMember` zwraca tylko 100 członków jednostki administracyjnej. Aby uzyskać więcej elementów członkowskich, można dodać `"-All $true"` .

### <a name="use-microsoft-graph"></a>Użyj Microsoft Graph

Zastąp symbol zastępczy informacjami o teście i uruchom następujące polecenie:

```http
https://graph.microsoft.com/v1.0/users/{user-id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-single-user-from-an-administrative-unit"></a>Usuń jednego użytkownika z jednostki administracyjnej

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Użytkownika można usunąć z jednostki administracyjnej na jeden z dwóch sposobów: 

* W Azure Portal przejdź do **usługi Azure AD**, a następnie wybierz pozycję **Użytkownicy**. 
  1. Wybierz użytkownika, aby otworzyć profil użytkownika. 
  1. Wybierz jednostkę administracyjną, z której chcesz usunąć użytkownika, a następnie wybierz pozycję **Usuń z jednostki administracyjnej**.

     ![Zrzut ekranu przedstawiający sposób usuwania użytkownika z jednostki administracyjnej z okienka profil użytkownika.](./media/admin-units-add-manage-users/user-remove-admin-units.png)

* W Azure Portal przejdź do **usługi Azure AD**, a następnie wybierz pozycję **jednostki administracyjne**.
  1. Wybierz jednostkę administracyjną, z której chcesz usunąć użytkownika. 
  1. Wybierz użytkownika, a następnie wybierz pozycję **Usuń członka**.
  
     ![Zrzut ekranu przedstawiający sposób usuwania użytkownika na poziomie jednostki administracyjnej.](./media/admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="use-powershell"></a>Korzystanie z programu PowerShell

Uruchom następujące polecenie:

```powershell
Remove-AzureADMSAdministrativeUnitMember -Id $adminUnitId -MemberId $memberUserObjId
```

### <a name="use-microsoft-graph"></a>Użyj Microsoft Graph

Zastąp symbole zastępcze informacjami o teście i uruchom następujące polecenie:

```http
https://graph.microsoft.com/v1.0/directory/administrativeUnits/{admin-unit-id}/members/{user-id}/$ref
```

## <a name="remove-multiple-users-as-a-bulk-operation"></a>Usuwanie wielu użytkowników jako operacji zbiorczej

Aby usunąć wielu użytkowników z jednostki administracyjnej, wykonaj następujące czynności:

1. W Azure Portal przejdź do **usługi Azure AD**.

1. Wybierz pozycję **jednostki administracyjne**, a następnie wybierz jednostkę administracyjną, z której chcesz usunąć użytkowników. 

1. Wybierz pozycję **zbiorczy Usuń członków**, a następnie Pobierz szablon CSV, którego będziesz używać do wyświetlania listy użytkowników, których chcesz usunąć.

   ![Zrzut ekranu przedstawiający link "zbiorcze usuwanie elementów członkowskich" w okienku "Użytkownicy".](./media/admin-units-add-manage-users/bulk-user-remove.png)

1. Edytuj pobrany szablon CSV z odpowiednimi wpisami użytkownika. Nie usuwaj pierwszych dwóch wierszy szablonu. W każdym wierszu Dodaj jedną główną nazwę użytkownika (UPN).

   ![Zrzut ekranu przedstawiający edytowany plik CSV, który umożliwia zbiorcze usuwanie użytkowników z jednostki administracyjnej.](./media/admin-units-add-manage-users/bulk-user-entries.png)

1. Zapisz zmiany, Przekaż plik, a następnie wybierz pozycję **Prześlij**.

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie roli do jednostki administracyjnej](admin-units-assign-roles.md)
- [Dodawanie grup do jednostki administracyjnej](admin-units-add-manage-groups.md)
