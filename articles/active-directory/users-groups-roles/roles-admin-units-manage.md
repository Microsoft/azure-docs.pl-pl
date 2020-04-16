---
title: Dodawanie i usuwanie jednostek administracyjnych (wersja zapoznawcza) — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Ograniczanie uprawnień roli w usłudze Azure Active Directory za pomocą jednostek administracyjnych
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
ms.openlocfilehash: 351cbee9cbc7cc02c7f20553b6fc8c3f861541df
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428162"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Zarządzanie jednostkami administracyjnymi w usłudze Azure Active Directory

Aby uzyskać bardziej szczegółową kontrolę administracyjną w usłudze Azure Active Directory (Azure AD), można przypisać użytkowników do roli usługi Azure AD o zakresie ograniczonym do co najmniej jednej jednostki administracyjnej (AU).

## <a name="getting-started"></a>Wprowadzenie

1. Aby uruchomić zapytania z następujących instrukcji za pośrednictwem [Programu Graph Explorer,](https://aka.ms/ge)upewnij się, że:

    1. Przejdź do usługi Azure AD w portalu, a następnie w aplikacjach wybierz Graph Explorer i udzielić zgody administratora Graph Explorer.

        ![wybierz Graph Explorer i przekaż zgodę administratora na tej stronie](./media/roles-admin-units-manage/select-graph-explorer.png)

    1. W Eksploratorze wykresów upewnij się, że wybierzesz wersję beta.

        ![wybierz wersję beta przed operacją POST](./media/roles-admin-units-manage/select-beta-version.png)

1. Użyj wersji zapoznawczej programu Azure AD PowerShell. Szczegółowe instrukcje są tutaj.

## <a name="add-an-administrative-unit"></a>Dodawanie jednostki administracyjnej

### <a name="azure-portal"></a>Azure Portal

1. Przejdź do usługi Active Directory w portalu i wybierz pozycję Jednostki administracyjne w lewym panelu.

    ![przechodzenie do jednostek administracyjnych w usłudze Azure Active Directory](./media/roles-admin-units-manage/nav-to-admin-units.png)

1. Wybierz **dodaj*** i podaj nazwę jednostki administracyjnej i opcjonalnie można dodać opis jednostki administracyjnej.

    ![wybierz pozycję Dodaj, a następnie wprowadź nazwę jednostki administracyjnej](./media/roles-admin-units-manage/add-new-admin-unit.png)

1. Wybierz **pozycję Dodaj,** aby sfinalizować jednostkę administracyjną.

### <a name="powershell"></a>PowerShell

Zainstaluj program Azure AD PowerShell (wersja zapoznawcza) przed podjęciem poniższych czynności:

    Connect-AzureAD
    New-AzureADAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"

Wartości wyróżnione powyżej mogą być modyfikowane zgodnie z wymaganiami.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http Request
    POST /administrativeUnits
    Request body
    {
        "displayName": "North America Operations",
        "description": "North America Operations administration"
    }

## <a name="remove-an-administrative-unit"></a>Usuwanie jednostki administracyjnej

W usłudze Azure Active Directory (Azure AD) można usunąć jednostkę administracyjną, która nie jest już potrzebna jako jednostka zakresu ról administracyjnych.

### <a name="azure-portal"></a>Azure Portal

Przejdź do **jednostki administracyjne usługi Azure AD >** w portalu. Wybierz jednostkę administracyjną do usunięcia, a następnie wybierz pozycję **Usuń**. Po potwierdzeniu **tak**jednostka administracyjna zostanie usunięta.

![Wybierz jednostkę administracyjną do usunięcia](./media/roles-admin-units-manage/select-admin-unit-to-delete.png)

### <a name="powershell"></a>PowerShell

    $delau = Get-AzureADAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
    Remove-AzureADAdministrativeUnit -ObjectId $delau.ObjectId

Wyróżniona sekcja może zostać zmieniona zgodnie z wymaganiami dla określonego środowiska.

### <a name="graph-api"></a>Interfejs API programu Graph

    HTTP request
    DELETE /administrativeUnits/{Admin id}
    Request body
    {}

## <a name="next-steps"></a>Następne kroki

[Zarządzanie użytkownikami w jednostce administracyjnej](roles-admin-units-add-manage-users.md)
[Zarządzanie grupami w jednostce administracyjnej](roles-admin-units-add-manage-groups.md)
