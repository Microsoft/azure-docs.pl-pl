---
title: Dodawanie, usuwanie i wyświetlanie grup w jednostce administracyjnej (wersja zapoznawcza) — Azure Active Directory | Microsoft Docs
description: Zarządzanie grupami i ich uprawnieniami roli w jednostce administracyjnej w Azure Active Directory
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
ms.openlocfilehash: d549d64dc2729b5d6280089e42d456043fb18393
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/12/2020
ms.locfileid: "84732501"
---
# <a name="add-and-manage-groups-in-administrative-units-in-azure-active-directory"></a>Dodawanie grup i zarządzanie nimi w jednostkach administracyjnych w Azure Active Directory

W Azure Active Directory (Azure AD) można dodać grupy do jednostki administracyjnej (AU), aby uzyskać bardziej szczegółowy zakres administracyjny kontroli.

Aby dowiedzieć się, jak przygotować się do korzystania z programu PowerShell i Microsoft Graph do zarządzania jednostką administracyjną, zobacz [wprowadzenie](roles-admin-units-manage.md#get-started).

## <a name="add-groups-to-an-au"></a>Dodawanie grup do funkcji AU

### <a name="azure-portal"></a>Azure Portal

W wersji zapoznawczej można przypisywać grupy tylko pojedynczo do jednostki administracyjnej. Nie ma możliwości zbiorczego przypisywania grup do jednostki administracyjnej. Grupę można przypisać do jednostki administracyjnej w jeden z dwóch sposobów w portalu:

1. Na stronie **grupy > usługi Azure AD**

    Otwórz stronę przegląd grup w usłudze Azure AD i wybierz grupę, która ma zostać przypisana do jednostki administracyjnej. Po lewej stronie wybierz pozycję **jednostki administracyjne** , aby wyświetlić jednostki administracyjne, do których grupa jest przypisana. W górnej części znajdziesz opcję Przypisz do jednostki administracyjnej i kliknięcie jej spowoduje udostępnienie panelu po prawej stronie, aby wybrać jednostkę administracyjną.

    ![Przypisz grupę pojedynczo do jednostki administracyjnej](./media/roles-admin-units-add-manage-groups/assign-to-group-1.png)

1. Na stronie **jednostki administracyjne > usługi Azure AD > wszystkie grupy**

    Otwórz blok wszystkie grupy w usłudze Azure AD > jednostki administracyjne. Jeśli istnieją już przypisane grupy do jednostki administracyjnej, zostaną one wyświetlone po prawej stronie. Wybierz pozycję **Dodaj** u góry, a prawy panel wyświetli listę grup dostępnych w organizacji usługi Azure AD. Wybierz co najmniej jedną grupę, która ma zostać przypisana do jednostek administracyjnych.

    ![Wybierz jednostkę administracyjną, a następnie wybierz pozycję Dodaj członka](./media/roles-admin-units-add-manage-groups/assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
    Add-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId -RefObjectId $GroupObj.ObjectId

W tym przykładzie polecenie cmdlet Add-AzureADAdministrativeUnitMember służy do dodawania grupy do jednostki administracyjnej. Identyfikator obiektu jednostki administracyjnej i identyfikator obiektu grupy, która ma zostać dodana, są brane jako argument. Wyróżnioną sekcję można zmienić zgodnie z wymaganiami określonego środowiska.

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

## <a name="list-groups-in-an-au"></a>Wyświetl listę grup w usłudze AU

### <a name="azure-portal"></a>Azure Portal

Przejdź do pozycji **jednostki administracyjne usługi Azure AD >** w portalu. Wybierz jednostkę administracyjną, dla której chcesz wyświetlić listę użytkowników. Domyślnie **Wszyscy użytkownicy** są wybierani już w lewym panelu. Zaznacz **wszystkie grupy** i po prawej stronie znajdziesz listę grup, które są członkami wybranej jednostki administracyjnej.

![Wyświetl listę grup w jednostce administracyjnej](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    Get-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId

Pomoże to uzyskać wszystkich członków jednostki administracyjnej. Aby wyświetlić wszystkie grupy, które są członkami jednostki administracyjnej, można użyć poniższego fragmentu kodu:

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

## <a name="list-aus-for-a-group"></a>Wystaw jako grupę

### <a name="azure-portal"></a>Azure Portal

W portalu usługi Azure AD można otworzyć Szczegóły grupy, otwierając **grupy**. Wybierz grupę, aby otworzyć profil grupy. Wybierz pozycję **jednostki administracyjne** , aby wyświetlić listę wszystkich jednostek administracyjnych, w których grupa jest członkiem.

![Wyświetl listę jednostek administracyjnych dla grupy](./media/roles-admin-units-add-manage-groups/list-group-au.png)

### <a name="powershell"></a>PowerShell

    Get-AzureADAdministrativeUnit | where { Get-AzureADAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/groups/<group-id>/memberOf/$/Microsoft.Graph.AdministrativeUnit

## <a name="remove-a-group-from-an-au"></a>Usuwanie grupy z funkcji AU

### <a name="azure-portal"></a>Azure Portal

Istnieją dwa sposoby usuwania grupy z jednostki administracyjnej w Azure Portal.

Otwórz **grupy usługi Azure AD**  >  **Groups** i Otwórz profil dla grupy, którą chcesz usunąć z jednostki administracyjnej. Wybierz pozycję **jednostki administracyjne** w lewym panelu, aby wyświetlić listę wszystkich jednostek administracyjnych, w których grupa jest członkiem. Wybierz jednostkę administracyjną, z której chcesz usunąć grupę, a następnie wybierz pozycję **Usuń z jednostki administracyjnej**.

![Usuwanie grupy z jednostki administracyjnej](./media/roles-admin-units-add-manage-groups/group-au-remove.png)

Alternatywnie możesz przejść do pozycji jednostki **administracyjne usługi Azure AD**  >  **Administrative units** i wybrać jednostkę administracyjną, do której należy Grupa. Wybierz **grupy** w lewym panelu, aby wyświetlić listę grup elementów członkowskich. Wybierz grupę, która ma zostać usunięta z jednostki administracyjnej, a następnie wybierz pozycję **Usuń grupy**.

![Wyświetl listę grup w jednostce administracyjnej](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

    Remove-AzureADAdministrativeUnitMember -ObjectId $auId -MemberId $memberGroupObjId

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/administrativeUnits/<adminunit-id>/members/<group-id>/$ref

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie roli do jednostki administracyjnej](roles-admin-units-assign-roles.md)
- [Zarządzanie użytkownikami w jednostce administracyjnej](roles-admin-units-add-manage-users.md)
