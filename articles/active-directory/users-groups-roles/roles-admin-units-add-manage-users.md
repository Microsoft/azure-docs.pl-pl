---
title: Dodawanie, usuwanie i wyświetlanie listy użytkowników w jednostce administracyjnej — Azure Active Directory | Microsoft Docs
description: Zarządzanie użytkownikami i ich uprawnieniami roli w jednostce administracyjnej w Azure Active Directory
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
ms.openlocfilehash: 0d29f4ef5806eb8ed9385696dea78f4ae0992b93
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91818175"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>Dodawanie użytkowników i zarządzanie nimi w jednostce administracyjnej w Azure Active Directory

W Azure Active Directory (Azure AD) można dodać użytkowników do jednostki administracyjnej (AU), aby uzyskać bardziej szczegółowy zakres administracyjny kontroli.

Aby dowiedzieć się, jak przygotować się do korzystania z programu PowerShell i Microsoft Graph do zarządzania jednostką administracyjną, zobacz [wprowadzenie](roles-admin-units-manage.md#get-started).

## <a name="add-users-to-an-au"></a>Dodawanie użytkowników do funkcji AU

### <a name="azure-portal"></a>Azure Portal

Użytkowników można przypisywać do jednostek administracyjnych pojedynczo lub w ramach operacji zbiorczej.

- Przypisanie indywidualne z profilu użytkownika

   1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://portal.azure.com) przy użyciu uprawnień administratora ról uprzywilejowanych.
   1. Wybierz pozycję **Użytkownicy** i wybierz użytkownika, który ma zostać przypisany do jednostki administracyjnej, aby otworzyć profil użytkownika.
   1. Wybierz pozycję **jednostki administracyjne**. Użytkownika można przypisać do co najmniej jednej jednostki administracyjnej, wybierając pozycję **Przypisz do jednostki administracyjnej** i wybierając jednostki administracyjne, do których użytkownik ma zostać przypisany.

       ![Wybierz pozycję Dodaj, a następnie wprowadź nazwę jednostki administracyjnej](./media/roles-admin-units-add-manage-users/assign-users-individually.png)

- Przypisanie indywidualne z jednostki administracyjnej

   1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://portal.azure.com) przy użyciu uprawnień administratora ról uprzywilejowanych.
   1. Wybierz pozycję **jednostki administracyjne** , a następnie wybierz jednostkę administracyjną, do której użytkownicy mają być przypisani.
   1. Wybierz pozycję **Wszyscy użytkownicy** , a następnie wybierz pozycję **Dodaj członka** , aby wybrać co najmniej jednego użytkownika, który ma zostać przypisany do jednostki administracyjnej z okienka **Dodawanie elementu członkowskiego** .

        ![Wybierz jednostkę administracyjną, a następnie wybierz pozycję Dodaj członka](./media/roles-admin-units-add-manage-users/assign-to-admin-unit.png)

- Przydział zbiorczy

   1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://portal.azure.com) przy użyciu uprawnień administratora ról uprzywilejowanych.
   1. Wybierz pozycję **jednostki administracyjne**.
   1. Wybierz jednostkę administracyjną, w której użytkownicy mają zostać dodani.
   1. Otwórz przystawkę **Wszyscy użytkownicy**  >  **Dodaj członków z pliku CSV**. Następnie można pobrać szablon wartości rozdzielonych przecinkami (CSV) i edytować plik. Format jest prosty i wymaga dodania pojedynczej nazwy głównej użytkownika w każdym wierszu. Gdy plik jest gotowy, Zapisz go w odpowiedniej lokalizacji, a następnie Przekaż go w ramach tego kroku.

    ![zbiorcze przypisywanie użytkowników do jednostki administracyjnej](./media/roles-admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

```powershell
$administrativeunitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$UserObj = Get-AzureADUser -Filter "UserPrincipalName eq 'billjohn@fabidentity.onmicrosoft.com'"
Add-AzureADMSAdministrativeUnitMember -Id $administrativeunitObj.ObjectId -RefObjectId $UserObj.ObjectId
```

W powyższym przykładzie polecenie cmdlet Add-AzureADAdministrativeUnitMember służy do dodawania użytkownika do jednostki administracyjnej. Identyfikator obiektu jednostki administracyjnej, w której użytkownik ma zostać dodany i identyfikator obiektu użytkownika, który ma zostać dodany, jest traktowany jako argument. Wyróżnioną sekcję można zmienić zgodnie z wymaganiami określonego środowiska.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
POST /administrativeUnits/{Admin Unit id}/members/$ref
Request body
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/{id}"
}
```

Przykład:

```http
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/johndoe@fabidentity.com"
}
```

## <a name="list-administrative-units-for-a-user"></a>Wyświetl listę jednostek administracyjnych dla użytkownika

### <a name="azure-portal"></a>Azure Portal

W Azure Portal można otworzyć profil użytkownika według:

1. Otwieranie użytkowników usługi **Azure AD**  >  **Users**.

1. Wybierz użytkownika, aby otworzyć profil użytkownika.

1. Wybierz pozycję **jednostki administracyjne** , aby wyświetlić listę jednostek administracyjnych, do których użytkownik został przypisany.

   ![Wyświetl listę jednostek administracyjnych dla użytkownika](./media/roles-admin-units-add-manage-users/list-user-admin-units.png)

### <a name="powershell"></a>PowerShell

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -Id $_.ObjectId | where {$_.RefObjectId -eq $userObjId} }
```
Uwaga: Domyślnie polecenie Get-AzureADAdministrativeUnitMember zwraca elementy członkowskie 100, a aby uzyskać więcej elementów członkowskich, można dodać "-All $true".

### <a name="microsoft-graph"></a>Microsoft Graph

```http
https://graph.microsoft.com/v1.0/users/{id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-single-user-from-an-au"></a>Usuwanie pojedynczego użytkownika z funkcji AU

### <a name="azure-portal"></a>Azure Portal

Istnieją dwa sposoby usunięcia użytkownika z jednostki administracyjnej. W Azure Portal można otworzyć profil użytkownika, przechodząc do użytkowników **usługi Azure AD**  >  **Users**. Wybierz użytkownika, aby otworzyć profil użytkownika. Wybierz jednostkę administracyjną, z której chcesz usunąć użytkownika, a następnie wybierz pozycję **Usuń z jednostki administracyjnej**.

![Usuwanie użytkownika z jednostki administracyjnej z profilu użytkownika](./media/roles-admin-units-add-manage-users/user-remove-admin-units.png)

Możesz również usunąć użytkownika w **Azure AD**  >  **jednostkach administracyjnych** usługi Azure AD, wybierając jednostkę administracyjną, z której chcesz usunąć użytkowników. Wybierz użytkownika i wybierz pozycję **Usuń członka**.
  
![Usuń użytkownika na poziomie jednostki administracyjnej](./media/roles-admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="powershell"></a>PowerShell

```powershell
Remove-AzureADMSAdministrativeUnitMember -Id $auId -MemberId $memberUserObjId
```

### <a name="microsoft-graph"></a>Microsoft Graph

   https://graph.microsoft.com/v1.0/directory/administrativeUnits/{adminunit-id}/members/{user-id}/$ref

## <a name="bulk-remove-more-than-one-user"></a>Zbiorczo Usuń więcej niż jednego użytkownika

Możesz przejść do pozycji jednostki administracyjne usługi Azure AD > i wybrać jednostkę administracyjną, z której chcesz usunąć użytkowników. Kliknij pozycję zbiorcze usuwanie elementu członkowskiego. Pobierz szablon CSV w celu udostępnienia listy użytkowników do usunięcia.

Edytuj pobrany szablon CSV z odpowiednimi wpisami użytkownika. Nie usuwaj pierwszych dwóch wierszy szablonu. Dodaj jedną nazwę UPN użytkownika w każdym wierszu.

![Edytuj plik CSV do usuwania zbiorczego użytkowników z jednostek administracyjnych](./media/roles-admin-units-add-manage-users/bulk-user-entries.png)

Po zapisaniu wpisów w pliku Przekaż plik, a następnie wybierz pozycję **Prześlij**.

![Prześlij plik ładowania zbiorczego](./media/roles-admin-units-add-manage-users/bulk-user-remove.png)

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie roli do jednostki administracyjnej](roles-admin-units-assign-roles.md)
- [Dodawanie grup do jednostki administracyjnej](roles-admin-units-add-manage-groups.md)
