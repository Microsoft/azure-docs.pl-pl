---
title: Dodawanie i usuwanie jednostek administracyjnych (wersja zapoznawcza) — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Użyj jednostek administracyjnych, aby ograniczyć zakres uprawnień roli w usłudze Azure Active Directory.
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
ms.openlocfilehash: 7a4a0dfaeda18b3f68ddc3c7cc7333b8c994d174
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684913"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Zarządzanie jednostkami administracyjnymi w usłudze Azure Active Directory

Aby uzyskać bardziej szczegółową kontrolę administracyjną w usłudze Azure Active Directory (Azure AD), można przypisać użytkowników do roli usługi Azure AD o zakresie ograniczonym do co najmniej jednej jednostki administracyjnej (AU).

## <a name="get-started"></a>Rozpoczęcie pracy

1. Aby uruchomić zapytania z następujących instrukcji za pośrednictwem [Eksploratora wykresu,](https://aka.ms/ge)wykonaj następujące czynności:

    a. W witrynie Azure portal przejdź do usługi Azure AD. Na liście aplikacji wybierz pozycję **Graph Explorer**, a następnie wybierz pozycję **Udzielaj zgody administratora eksploratorowi wykresu**.

    ![Zrzut ekranu przedstawiający link do "Udziel zgody administratora"](./media/roles-admin-units-manage/select-graph-explorer.png)

    b. W Eksploratorze wykresów wybierz wersję **beta.**

    ![Zrzut ekranu przedstawiający wybraną wersję beta](./media/roles-admin-units-manage/select-beta-version.png)

1. Użyj wersji zapoznawczej programu Azure AD PowerShell.

## <a name="add-an-administrative-unit"></a>Dodawanie jednostki administracyjnej

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

1. W witrynie Azure portal przejdź do usługi Azure AD, a następnie w lewym okienku wybierz pozycję **Jednostki administracyjne**.

    ![Zrzut ekranu przedstawiający łącze Jednostki administracyjne (Wersja zapoznawcza) w usłudze Azure AD](./media/roles-admin-units-manage/nav-to-admin-units.png)

1. Wybierz **pozycję Dodaj,** a następnie wprowadź nazwę jednostki administracyjnej. Opcjonalnie należy dodać opis jednostki administracyjnej.

    ![Zrzut ekranu przedstawiający przycisk Dodaj i pole tekstowe służące do wprowadzania nazwy jednostki administracyjnej](./media/roles-admin-units-manage/add-new-admin-unit.png)

1. Wybierz **pozycję Dodaj,** aby sfinalizować jednostkę administracyjną.

### <a name="use-powershell"></a>Korzystanie z programu PowerShell

Zainstaluj program Azure AD PowerShell (wersja zapoznawcza) przed podjęciem próby uruchomienia następujących poleceń:

    Connect-AzureAD
    New-AzureADAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"

W razie potrzeby można zmodyfikować wartości ujęte w cudzysłów.

### <a name="use-microsoft-graph"></a>Korzystanie z programu Microsoft Graph

    Http Request
    POST /administrativeUnits
    Request body
    {
        "displayName": "North America Operations",
        "description": "North America Operations administration"
    }

## <a name="remove-an-administrative-unit"></a>Usuwanie jednostki administracyjnej

W usłudze Azure AD można usunąć jednostkę administracyjną, która nie jest już potrzebna jako jednostka zakresu ról administracyjnych.

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

1. W witrynie Azure portal przejdź do**jednostek administracyjnych** **usługi Azure AD** > . 
1. Wybierz jednostkę administracyjną do usunięcia, a następnie wybierz pozycję **Usuń**. 
1. Aby potwierdzić, że chcesz usunąć jednostkę administracyjną, **wybierz**tak . Jednostka administracyjna zostanie usunięta.

![Zrzut ekranu przedstawiający przycisk usuwania i okno potwierdzenia jednostki administracyjnej](./media/roles-admin-units-manage/select-admin-unit-to-delete.png)

### <a name="use-powershell"></a>Korzystanie z programu PowerShell

    $delau = Get-AzureADAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
    Remove-AzureADAdministrativeUnit -ObjectId $delau.ObjectId

Można zmodyfikować wartości, które są ujęte w cudzysłów, zgodnie z wymaganiami dla określonego środowiska.

### <a name="use-the-graph-api"></a>Korzystanie z interfejsu API wykresu

    HTTP request
    DELETE /administrativeUnits/{Admin id}
    Request body
    {}

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie użytkownikami w jednostce administracyjnej](roles-admin-units-add-manage-users.md)
* [Zarządzanie grupami w jednostce administracyjnej](roles-admin-units-add-manage-groups.md)
