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
ms.openlocfilehash: 6fad9356d3379e76aa259d67711d18f14a4e266f
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505279"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Zarządzanie jednostkami administracyjnymi w Azure Active Directory

Aby uzyskać bardziej szczegółową kontrolę administracyjną w usłudze Azure Active Directory (Azure AD), możesz przypisać użytkowników do roli usługi Azure AD z zakresem ograniczonym do co najmniej jednej jednostki administracyjnej.

## <a name="get-started"></a>Rozpoczęcie pracy

1. Aby uruchamiać zapytania z następujących instrukcji za pośrednictwem [Eksploratora programu Graph,](https://aka.ms/ge)wykonaj następujące czynności:

    a. W witrynie Azure Portal przejdź do usługi Azure AD. 
    
    b. Na liście aplikacji wybierz pozycję **eksplorator programu Graph**.
    
    c. W **okienku Uprawnienia** wybierz pozycję **U dostępną eksplorator programu Graph.**

    ![Zrzut ekranu przedstawiający link "Udzielanie zgody eksplorator programu Graph administratora".](./media/admin-units-manage/select-graph-explorer.png)


1. Użyj [programu Azure AD PowerShell.](https://www.powershellgallery.com/packages/AzureAD/)

## <a name="add-an-administrative-unit"></a>Dodawanie jednostki administracyjnej

Jednostkę administracyjną można dodać przy użyciu interfejsu Azure Portal programu PowerShell.

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

1. W witrynie Azure Portal przejdź do usługi Azure AD. Następnie w okienku po lewej stronie wybierz pozycję **Jednostki administracyjne**.

    ![Zrzut ekranu przedstawiający link "Jednostki administracyjne" w usłudze Azure AD.](./media/admin-units-manage/nav-to-admin-units.png)

1. Wybierz przycisk **Dodaj** w górnej części okienka, a następnie w polu **Nazwa** wprowadź nazwę jednostki administracyjnej. Opcjonalnie dodaj opis jednostki administracyjnej.

    ![Zrzut ekranu przedstawiający przycisk Dodaj i pole Nazwa służące do wprowadzania nazwy jednostki administracyjnej.](./media/admin-units-manage/add-new-admin-unit.png)

1. Wybierz niebieski przycisk **Dodaj,** aby zakończyć jednostkę administracyjną.

### <a name="use-powershell"></a>Korzystanie z programu PowerShell

Zainstaluj [program Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/) przed próbą uruchomienia następujących poleceń:

```powershell
Connect-AzureAD
New-AzureADMSAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"
```

W razie potrzeby można zmodyfikować wartości ujęte w cudzysłów.

### <a name="use-microsoft-graph"></a>Korzystanie z Microsoft Graph

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

## <a name="remove-an-administrative-unit"></a>Usuwanie jednostki administracyjnej

W usłudze Azure AD można usunąć jednostkę administracyjną, która nie jest już potrzebna jako jednostka zakresu dla ról administracyjnych.

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

1. W witrynie Azure Portal do usługi **Azure AD,** a następnie wybierz pozycję **Jednostki administracyjne.** 
1. Wybierz jednostkę administracyjną do usunięcia, a następnie wybierz pozycję **Usuń**. 
1. Aby potwierdzić, że chcesz usunąć jednostkę administracyjną, wybierz pozycję **Tak.** Jednostka administracyjna zostanie usunięta.

![Zrzut ekranu przedstawiający przycisk Usuń jednostki administracyjnej i okno potwierdzenia.](./media/admin-units-manage/select-admin-unit-to-delete.png)

### <a name="use-powershell"></a>Korzystanie z programu PowerShell

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
Remove-AzureADMSAdministrativeUnit -Id $adminUnitObj.Id
```

Można zmodyfikować wartości, które są ujęte w cudzysłów, zgodnie z wymaganiami dla określonego środowiska.

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
