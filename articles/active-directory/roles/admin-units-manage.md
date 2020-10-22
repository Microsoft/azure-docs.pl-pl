---
title: Dodawanie i usuwanie jednostek administracyjnych — Azure Active Directory | Microsoft Docs
description: Użyj jednostek administracyjnych, aby ograniczyć zakres uprawnień roli w Azure Active Directory.
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
ms.openlocfilehash: 7b47ea3cc55be26521dfa6e2b3230b477f82f442
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92377909"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Zarządzanie jednostkami administracyjnymi w Azure Active Directory

Aby uzyskać bardziej szczegółową kontrolę administracyjną w usłudze Azure Active Directory (Azure AD), można przypisać użytkowników do roli usługi Azure AD z zakresem ograniczonym do co najmniej jednej jednostki administracyjnej (np.).

## <a name="get-started"></a>Rozpoczęcie pracy

1. Aby uruchamiać zapytania z poniższych instrukcji za pośrednictwem [Eksploratora grafów](https://aka.ms/ge), wykonaj następujące czynności:

    a. W Azure Portal przejdź do usługi Azure AD. Na liście Aplikacje wybierz pozycję **Eksplorator wykresu**, a następnie wybierz pozycję **Udziel zgody administratora na Eksplorator grafów**.

    ![Zrzut ekranu przedstawiający link do "Udziel zgody administratora"](./media/admin-units-manage/select-graph-explorer.png)


1. Użyj wersji zapoznawczej programu Azure AD PowerShell.

## <a name="add-an-administrative-unit"></a>Dodaj jednostkę administracyjną

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

1. W Azure Portal przejdź do usługi Azure AD, a następnie w okienku po lewej stronie wybierz pozycję **jednostki administracyjne**.

    ![Zrzut ekranu przedstawiający unitslink administracyjne w usłudze Azure AD](./media/admin-units-manage/nav-to-admin-units.png)

1. Wybierz pozycję **Dodaj** , a następnie wprowadź nazwę jednostki administracyjnej. Opcjonalnie Dodaj opis jednostki administracyjnej.

    ![Zrzut ekranu przedstawiający przycisk Dodaj i pole tekstowe służące do wprowadzania nazwy jednostki administracyjnej](./media/admin-units-manage/add-new-admin-unit.png)

1. Wybierz pozycję **Dodaj** , aby sfinalizować jednostkę administracyjną.

### <a name="use-powershell"></a>Korzystanie z programu PowerShell

Zainstaluj program Azure AD PowerShell (wersja zapoznawcza) przed podjęciem próby uruchomienia następujących poleceń:

```powershell
Connect-AzureAD
New-AzureADMSAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"
```

W razie potrzeby można zmodyfikować wartości ujęte w znaki cudzysłowu.

### <a name="use-microsoft-graph"></a>Użyj Microsoft Graph

```http
Http Request
POST /administrativeUnits
Request body
{
  "displayName": "North America Operations",
  "description": "North America Operations administration"
}
```

## <a name="remove-an-administrative-unit"></a>Usuń jednostkę administracyjną

W usłudze Azure AD można usunąć jednostkę administracyjną, która nie jest już potrzebna jako jednostka zakresu dla ról administracyjnych.

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

1. W Azure Portal przejdź do pozycji jednostki **administracyjne usługi Azure AD**  >  **Administrative units**. 
1. Wybierz jednostkę administracyjną do usunięcia, a następnie wybierz pozycję **Usuń**. 
1. Aby potwierdzić, że chcesz usunąć jednostkę administracyjną, wybierz pozycję **tak**. Jednostka administracyjna została usunięta.

![Zrzut ekranu przedstawiający przycisk usuwania jednostki administracyjnej i okno potwierdzenia](./media/admin-units-manage/select-admin-unit-to-delete.png)

### <a name="use-powershell"></a>Korzystanie z programu PowerShell

```powershell
$delau = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
Remove-AzureADMSAdministrativeUnit -ObjectId $delau.ObjectId
```

Wartości, które są ujęte w znaki cudzysłowu, można modyfikować zgodnie z wymaganiami określonego środowiska.

### <a name="use-the-graph-api"></a>Użyj interfejs API programu Graph

```http
HTTP request
DELETE /administrativeUnits/{Admin id}
Request body
{}
```

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie użytkownikami w jednostce administracyjnej](admin-units-add-manage-users.md)
* [Zarządzanie grupami w jednostce administracyjnej](admin-units-add-manage-groups.md)
