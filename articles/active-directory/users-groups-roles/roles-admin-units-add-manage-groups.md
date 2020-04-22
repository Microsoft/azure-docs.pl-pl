---
title: Dodawanie, usuwanie i listy grup w jednostce administracyjnej (wersja zapoznawcza) — Usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Zarządzanie grupami i ich uprawnieniami do roli w jednostce administracyjnej usługi Azure Active Directory
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
ms.openlocfilehash: 1f0b0c0a7b666aed56ad24625c80c0a57683b998
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683270"
---
# <a name="add-and-manage-groups-in-administrative-units-in-azure-active-directory"></a>Dodawanie grup w jednostkach administracyjnych w usłudze Azure Active Directory i zarządzanie nimi

W usłudze Azure Active Directory (Azure AD) można dodać grupy do jednostki administracyjnej (UA), aby uzyskać bardziej szczegółowy zakres administracyjny kontroli.

Aby uzyskać instrukcje przygotowania do używania programów PowerShell i Microsoft Graph do zarządzania jednostkami administracyjnymi, zobacz [Wprowadzenie](roles-admin-units-manage.md#get-started).

## <a name="add-groups-to-an-au"></a>Dodawanie grup do ua

### <a name="azure-portal"></a>Azure Portal

W wersji zapoznawczej grupy można przypisywać tylko indywidualnie do jednostki administracyjnej. Nie ma możliwości zbiorczego przypisywania grup do jednostki administracyjnej. Grupę można przypisać do jednostki administracyjnej w jeden z dwóch sposobów w portalu:

1. Ze strony **Grupy > usługi Azure AD**

    Otwórz stronę Przegląd grup w usłudze Azure AD i wybierz grupę, która musi zostać przypisana do jednostki administracyjnej. Po lewej stronie wybierz **pozycję Jednostki administracyjne,** aby wyświetlić listę jednostek administracyjnych, do które grupa jest przypisana. Na górze znajdziesz opcję Przypisz do jednostki administracyjnej, a kliknięcie na nią da panel po prawej stronie, aby wybrać jednostkę administracyjną.

    ![przypisywanie grupy indywidualnie do jednostki administracyjnej](./media/roles-admin-units-add-manage-groups/assign-to-group-1.png)

1. Na stronie **Jednostki administracyjne usługi Azure AD > > wszystkie grupy**

    Otwórz wszystkie grupy bloku w usłudze Azure AD > jednostek administracyjnych. Jeśli istnieją grupy już przypisane do jednostki administracyjnej, zostaną one wyświetlone po prawej stronie. Wybierz **pozycję Dodaj** u góry, a prawy panel zostanie przesunie się z listą grup dostępnych w organizacji usługi Azure AD. Wybierz jedną lub więcej grup, które mają być przypisane do jednostek administracyjnych.

    ![wybierz jednostkę administracyjną, a następnie wybierz pozycję Dodaj element członkowski](./media/roles-admin-units-add-manage-groups/assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
    Add-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId -RefObjectId $GroupObj.ObjectId

W tym przykładzie dodawania polecenia cmdlet Add-AzureADAdministrativeUnitMember jest używany do dodawania grupy do jednostki administracyjnej. Jako argument przyjmowany jest identyfikator obiektu jednostki administracyjnej i identyfikator obiektu grupy, która ma zostać dodana. Wyróżniona sekcja może zostać zmieniona zgodnie z wymaganiami dla określonego środowiska.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    POST /administrativeUnits/{Admin Unit id}/members/$ref

    Request body
    {
      "@odata.id":"https://graph.microsoft.com/beta/groups/{id}"
    }

Przykład:

    {
      "@odata.id":"https://graph.microsoft.com/beta/users/ 871d21ab-6b4e-4d56-b257-ba27827628f3"
    }

## <a name="list-groups-in-an-au"></a>Lista grup w UA

### <a name="azure-portal"></a>Azure Portal

Przejdź do **jednostki administracyjne usługi Azure AD >** w portalu. Wybierz jednostkę administracyjną, dla której chcesz wyświetlić listę użytkowników. Domyślnie **wszyscy użytkownicy** są już wybierani na lewym panelu. Wybierz **wszystkie grupy,** a po prawej stronie znajdziesz listę grup, które są członkami wybranej jednostki administracyjnej.

![Wybierz jednostkę administracyjną do usunięcia](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    Get-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId

Pomoże ci to uzyskać wszystkich członków jednostki administracyjnej. Aby wyświetlić wszystkie grupy, które są członkami jednostki administracyjnej, możesz użyć poniższego fragmentu kodu:

    foreach ($member in (Get-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId)) 
    {
    if($member.ObjectType -eq "Group")
    {
    Get-AzureADGroup -ObjectId $member.ObjectId
    }
    }

### <a name="microsoft-graph"></a>Microsoft Graph

    HTTP request
    GET /administrativeUnits/{Admin id}/members/$/microsoft.graph.group
    Request body
    {}

## <a name="list-aus-for-a-group"></a>Lista aus dla grupy

### <a name="azure-portal"></a>Azure Portal

W portalu usługi Azure AD można otworzyć szczegóły grupy, otwierając **grupę**. Wybierz grupę, aby otworzyć profil grupy. Wybierz **jednostki administracyjne,** aby wyświetlić listę wszystkich jednostek administracyjnych, których grupa jest członkiem.

![Wyświetlanie listy jednostek administracyjnych dla grupy](./media/roles-admin-units-add-manage-groups/list-group-au.png)

### <a name="powershell"></a>PowerShell

    Get-AzureADAdministrativeUnit | where { Get-AzureADAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/groups/<group-id>/memberOf/$/Microsoft.Graph.AdministrativeUnit

## <a name="remove-a-group-from-an-au"></a>Usuwanie grupy z UA

### <a name="azure-portal"></a>Azure Portal

Istnieją dwa sposoby usuwania grupy z jednostki administracyjnej w witrynie Azure portal.

Otwórz**grupy** **usługi Azure AD** > i otwórz profil grupy, którą chcesz usunąć z jednostki administracyjnej. Wybierz **pozycję Jednostki administracyjne** w lewym panelu, aby wyświetlić listę wszystkich jednostek administracyjnych, których członkiem jest grupa. Wybierz jednostkę administracyjną, z której chcesz usunąć grupę, a następnie wybierz pozycję **Usuń z jednostki administracyjnej**.

![Usuwanie grupy z jednostki administracyjnej](./media/roles-admin-units-add-manage-groups/group-au-remove.png)

Alternatywnie można przejść do**jednostek administracyjnych** **usługi Azure AD** > i wybrać jednostkę administracyjną, której członkiem jest grupa. Wybierz **pozycję Grupy** w lewym panelu, aby wyświetlić listę grup członkowskich. Wybierz grupę, która ma zostać usunięta z jednostki administracyjnej, a następnie wybierz pozycję **Usuń grupy**.

![Lista grup w jednostce administracyjnej](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

    Remove-AzureADAdministrativeUnitMember -ObjectId $auId -MemberId $memberGroupObjId

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/administrativeUnits/<adminunit-id>/members/<group-id>/$ref

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie roli do jednostki administracyjnej](roles-admin-units-assign-roles.md)
- [Zarządzanie użytkownikami w jednostce administracyjnej](roles-admin-units-add-manage-users.md)
