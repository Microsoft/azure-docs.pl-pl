---
title: Dodawanie i usuwanie jednostek administracyjnych — Azure Active Directory | Microsoft Docs
description: Użyj jednostek administracyjnych, aby ograniczyć zakres uprawnień roli w Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: bbe76d21d948525421790f574830da7b3a163216
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93395397"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Zarządzanie jednostkami administracyjnymi w Azure Active Directory

Aby uzyskać bardziej szczegółową kontrolę administracyjną w Azure Active Directory (Azure AD), można przypisać użytkowników do roli usługi Azure AD z zakresem ograniczonym do co najmniej jednej jednostki administracyjnej.

## <a name="get-started"></a>Rozpoczęcie pracy

1. Aby uruchamiać zapytania z poniższych instrukcji za pośrednictwem [Eksploratora grafów](https://aka.ms/ge), wykonaj następujące czynności:

    a. W Azure Portal przejdź do usługi Azure AD. 
    
    b. Na liście Aplikacje wybierz pozycję **Eksplorator grafów**.
    
    c. W okienku **uprawnienia** wybierz pozycję **Udziel zgody administratora dla Eksploratora grafów**.

    ![Zrzut ekranu przedstawiający łącze "Udziel zgody administratora na Eksplorator grafów".](./media/admin-units-manage/select-graph-explorer.png)


1. Użyj wersji zapoznawczej programu Azure AD PowerShell.

## <a name="add-an-administrative-unit"></a>Dodaj jednostkę administracyjną

Jednostkę administracyjną można dodać przy użyciu Azure Portal lub programu PowerShell.

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

1. W Azure Portal przejdź do usługi Azure AD. Następnie w okienku po lewej stronie wybierz pozycję **jednostki administracyjne**.

    ![Zrzut ekranu przedstawiający link "jednostki administracyjne" w usłudze Azure AD.](./media/admin-units-manage/nav-to-admin-units.png)

1. Wybierz przycisk **Dodaj** w górnej części okienka, a następnie w polu **Nazwa** wprowadź nazwę jednostki administracyjnej. Opcjonalnie Dodaj opis jednostki administracyjnej.

    ![Zrzut ekranu przedstawiający przycisk Dodaj i pole Nazwa służące do wprowadzania nazwy jednostki administracyjnej.](./media/admin-units-manage/add-new-admin-unit.png)

1. Wybierz przycisk niebieski **Dodaj** , aby sfinalizować jednostkę administracyjną.

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

1. W Azure Portal przejdź do **usługi Azure AD** , a następnie wybierz pozycję **jednostki administracyjne**. 
1. Wybierz jednostkę administracyjną do usunięcia, a następnie wybierz pozycję **Usuń**. 
1. Aby potwierdzić, że chcesz usunąć jednostkę administracyjną, wybierz pozycję **tak**. Jednostka administracyjna została usunięta.

![Zrzut ekranu przedstawiający przycisk usuwania jednostki administracyjnej i okno potwierdzenia.](./media/admin-units-manage/select-admin-unit-to-delete.png)

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
