---
title: Dodawanie i usuwanie jednostek administracyjnych — Azure Active Directory | Microsoft Docs
description: Użyj jednostek administracyjnych, aby ograniczyć zakres uprawnień roli w Azure Active Directory.
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
ms.openlocfilehash: 0706fad1e5340625c32eab691ac3e4d58eeafc9f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103012125"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Zarządzanie jednostkami administracyjnymi w Azure Active Directory

Aby uzyskać bardziej szczegółową kontrolę administracyjną w Azure Active Directory (Azure AD), można przypisać użytkowników do roli usługi Azure AD z zakresem ograniczonym do co najmniej jednej jednostki administracyjnej.

## <a name="get-started"></a>Rozpoczęcie pracy

1. Aby uruchamiać zapytania z poniższych instrukcji za pośrednictwem [Eksploratora grafów](https://aka.ms/ge), wykonaj następujące czynności:

    a. W Azure Portal przejdź do usługi Azure AD. 
    
    b. Na liście Aplikacje wybierz pozycję **Eksplorator grafów**.
    
    c. W okienku **uprawnienia** wybierz pozycję **Udziel zgody administratora dla Eksploratora grafów**.

    ![Zrzut ekranu przedstawiający łącze "Udziel zgody administratora na Eksplorator grafów".](./media/admin-units-manage/select-graph-explorer.png)


1. Użyj [programu Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="add-an-administrative-unit"></a>Dodaj jednostkę administracyjną

Jednostkę administracyjną można dodać przy użyciu Azure Portal lub programu PowerShell.

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

1. W Azure Portal przejdź do usługi Azure AD. Następnie w okienku po lewej stronie wybierz pozycję **jednostki administracyjne**.

    ![Zrzut ekranu przedstawiający link "jednostki administracyjne" w usłudze Azure AD.](./media/admin-units-manage/nav-to-admin-units.png)

1. Wybierz przycisk **Dodaj** w górnej części okienka, a następnie w polu **Nazwa** wprowadź nazwę jednostki administracyjnej. Opcjonalnie Dodaj opis jednostki administracyjnej.

    ![Zrzut ekranu przedstawiający przycisk Dodaj i pole Nazwa służące do wprowadzania nazwy jednostki administracyjnej.](./media/admin-units-manage/add-new-admin-unit.png)

1. Wybierz przycisk niebieski **Dodaj** , aby sfinalizować jednostkę administracyjną.

### <a name="use-powershell"></a>Korzystanie z programu PowerShell

Zainstaluj program [Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/) przed podjęciem próby uruchomienia następujących poleceń:

```powershell
Connect-AzureAD
New-AzureADMSAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"
```

W razie potrzeby można zmodyfikować wartości ujęte w znaki cudzysłowu.

### <a name="use-microsoft-graph"></a>Użyj Microsoft Graph

Żądanie

```http
POST /administrativeUnits
```

Treść

```http
{
  "displayName": "North America Operations",
  "description": "North America Operations administration"
}
```

## <a name="remove-an-administrative-unit"></a>Usuń jednostkę administracyjną

W usłudze Azure AD można usunąć jednostkę administracyjną, która nie jest już potrzebna jako jednostka zakresu dla ról administracyjnych.

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

1. W Azure Portal przejdź do **usługi Azure AD**, a następnie wybierz pozycję **jednostki administracyjne**. 
1. Wybierz jednostkę administracyjną do usunięcia, a następnie wybierz pozycję **Usuń**. 
1. Aby potwierdzić, że chcesz usunąć jednostkę administracyjną, wybierz pozycję **tak**. Jednostka administracyjna została usunięta.

![Zrzut ekranu przedstawiający przycisk usuwania jednostki administracyjnej i okno potwierdzenia.](./media/admin-units-manage/select-admin-unit-to-delete.png)

### <a name="use-powershell"></a>Korzystanie z programu PowerShell

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
Remove-AzureADMSAdministrativeUnit -ObjectId $adminUnitObj.ObjectId
```

Wartości, które są ujęte w znaki cudzysłowu, można modyfikować zgodnie z wymaganiami określonego środowiska.

### <a name="use-the-graph-api"></a>Użycie interfejsu Graph API

Żądanie

```http
DELETE /administrativeUnits/{admin-unit-id}
```

Treść

```http
{}
```

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie użytkownikami w jednostce administracyjnej](admin-units-add-manage-users.md)
* [Zarządzanie grupami w jednostce administracyjnej](admin-units-add-manage-groups.md)
