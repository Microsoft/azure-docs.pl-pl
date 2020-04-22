---
title: Dodawanie, usuwanie i wyświetlanie listy użytkowników w jednostce administracyjnej (wersja zapoznawcza) — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Zarządzanie użytkownikami i ich uprawnieniami do roli w jednostce administracyjnej usługi Azure Active Directory
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
ms.openlocfilehash: 9c2c5c083115440e1e4da203f39f2b32734458c3
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684974"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>Dodawanie użytkowników w jednostce administracyjnej usługi Azure Active Directory i zarządzanie nimi

W usłudze Azure Active Directory (Azure AD) można dodawać użytkowników do jednostki administracyjnej (UA), aby uzyskać bardziej szczegółowy zakres administracyjny kontroli.

Aby uzyskać instrukcje przygotowania do używania programów PowerShell i Microsoft Graph do zarządzania jednostkami administracyjnymi, zobacz [Wprowadzenie](roles-admin-units-manage.md#get-started).

## <a name="add-users-to-an-au"></a>Dodawanie użytkowników do au

### <a name="azure-portal"></a>Azure Portal

Użytkownicy można przypisać do jednostek administracyjnych na dwa sposoby.

1. Indywidualne przypisanie

    1. Możesz przejść do usługi Azure AD w portalu i wybrać pozycję Użytkownicy i wybrać użytkownika, który ma zostać przypisany do jednostki administracyjnej. Następnie można wybrać jednostki administracyjne w lewym panelu. Użytkownik może być przypisany do jednej lub więcej jednostek administracyjnych, klikając przycisk Przypisz do jednostki administracyjnej i wybierając jednostki administracyjne, w których użytkownik ma zostać przypisany.

       ![wybierz pozycję Dodaj, a następnie wprowadź nazwę jednostki administracyjnej](./media/roles-admin-units-add-manage-users/assign-users-individually.png)

    1. Możesz przejść do usługi Azure AD w portalu i wybrać jednostki administracyjne w lewym okienku, a następnie wybrać jednostkę administracyjną, do której mają być przypisani użytkownicy. Wybierz pozycję Wszyscy użytkownicy w lewym okienku, a następnie wybierz pozycję Dodaj członka. Następnie można przejść dalej i wybrać jednego lub więcej użytkowników, którzy mają być przypisani do jednostki administracyjnej z prawego okienka.

        ![wybierz jednostkę administracyjną, a następnie wybierz pozycję Dodaj element członkowski](./media/roles-admin-units-add-manage-users/assign-to-admin-unit.png)

1. Przypisanie zbiorcze

    Przejdź do usługi Azure AD w portalu i wybierz jednostki administracyjne. Wybierz jednostkę administracyjną, w której mają być dodawane użytkownicy. Kontynuuj, klikając na Wszystkich użytkowników -> Dodaj członków z pliku csv. Następnie można pobrać szablon CSV i edytować plik. Format jest prosty i wymaga jednej łzowej edn do dodania w każdym wierszu. Gdy plik jest gotowy, zapisz go w odpowiedniej lokalizacji, a następnie przekaż go w kroku 3, jak wyróżniono w migawce.

    ![zbiorcze przypisywanie użytkowników do jednostki administracyjnej](./media/roles-admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

    $administrativeunitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $UserObj = Get-AzureADUser -Filter "UserPrincipalName eq 'billjohn@fabidentity.onmicrosoft.com'"
    Add-AzureADAdministrativeUnitMember -ObjectId $administrativeunitObj.ObjectId -RefObjectId $UserObj.ObjectId

W powyższym przykładzie dodawania polecenia cmdlet Add-AzureADAdministrativeUnitMember jest używany do dodawania użytkownika do jednostki administracyjnej. Jako argument przyjmuje się identyfikator obiektu jednostki administracyjnej, w której użytkownik ma zostać dodany oraz identyfikator obiektu użytkownika, który ma zostać dodany. Wyróżniona sekcja może zostać zmieniona zgodnie z wymaganiami dla określonego środowiska.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    POST /administrativeUnits/{Admin Unit id}/members/$ref
    Request body
    {
      "@odata.id":"https://graph.microsoft.com/beta/users/{id}"
    }

Przykład:

    {
      "@odata.id":"https://graph.microsoft.com/beta/users/johndoe@fabidentity.com"
    }

## <a name="list-administrative-units-for-a-user"></a>Wyświetlanie listy jednostek administracyjnych dla użytkownika

### <a name="azure-portal"></a>Azure Portal

W witrynie Azure portal można otworzyć profil użytkownika, przechodząc do usługi Azure AD > użytkowników. Kliknij użytkownika, aby otworzyć profil użytkownika.

![Otwieranie profilu użytkownika w usłudze Azure Active Directory](./media/roles-admin-units-add-manage-users/user-profile-admin-units.png)

Wybierz **jednostki administracyjne** w lewym panelu, aby wyświetlić listę jednostek administracyjnych, do których użytkownik został przypisany.

![Wyświetlanie listy jednostek administracyjnych dla użytkownika](./media/roles-admin-units-add-manage-users/list-user-admin-units.png)

### <a name="powershell"></a>PowerShell

    Get-AzureADAdministrativeUnit | where { Get-AzureADAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $userObjId} }

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/users//memberOf/$/Microsoft.Graph.AdministrativeUnit

## <a name="remove-a-single-user-from-an-au"></a>Usuwanie pojedynczego użytkownika z AU

### <a name="azure-portal"></a>Azure Portal

Istnieją dwa sposoby usuwania użytkownika z jednostki administracyjnej. W witrynie Azure portal można otworzyć profil użytkownika, przechodząc do usługi**Użytkownicy** **usługi Azure AD** > . Wybierz użytkownika, aby otworzyć profil użytkownika. Wybierz jednostkę administracyjną, z której ma zostać usunięty użytkownik, i wybierz pozycję **Usuń z jednostki administracyjnej**.

![Usuwanie użytkownika z jednostki administracyjnej z profilu użytkownika](./media/roles-admin-units-add-manage-users/user-remove-admin-units.png)

Można również usunąć użytkownika w**jednostkach administracyjnych** **usługi Azure AD,** > wybierając jednostkę administracyjną, z której chcesz usunąć użytkowników. Wybierz użytkownika i wybierz pozycję **Usuń element członkowski**.
  
![Usuwanie użytkownika na poziomie jednostki administracyjnej](./media/roles-admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="powershell"></a>PowerShell

    Remove-AzureADAdministrativeUnitMember -ObjectId $auId -MemberId $memberUserObjId

### <a name="microsoft-graph"></a>Microsoft Graph

   https://graph.microsoft.com/beta/administrativeUnits/<adminunit-id>/members/<user-id>/$ref

## <a name="bulk-remove-more-than-one-user"></a>Zbiorcze usuwanie więcej niż jednego użytkownika

Możesz przejść do jednostki administracyjne usługi Azure AD > i wybrać jednostkę administracyjną, z której chcesz usunąć użytkowników. Kliknij opcję Usuń zbiorczo. Pobierz szablon CSV, aby udostępnić listę użytkowników do usunięcia.

Edytuj pobrany szablon CSV z odpowiednimi wpisami użytkownika. Nie należy usuwać pierwszych dwóch wierszy szablonu. Dodaj jedną łan nazwy UPN użytkownika w każdym wierszu.

![Edytowanie pliku CSV w celu zbiorczego usuwania użytkowników z jednostek administracyjnych](./media/roles-admin-units-add-manage-users/bulk-user-entries.png)

Po zapisaniu wpisów w pliku prześlij plik, wybierz opcję **Prześlij**.

![Prześlij plik przesyłania zbiorczego](./media/roles-admin-units-add-manage-users/bulk-user-remove.png)

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie roli do jednostki administracyjnej](roles-admin-units-assign-roles.md)
- [Dodawanie grup do jednostki administracyjnej](roles-admin-units-add-manage-groups.md)
