---
title: Przywracanie usuniętej grupy Microsoft 365 — usługa Azure AD | Microsoft Docs
description: Jak przywrócić usuniętą grupę, wyświetlić grupy możliwe do przywrócenia i trwale usunąć grupę w usłudze Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 092288d320874488cee4d2f097d7406c0757e8a0
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92379148"
---
# <a name="restore-a-deleted-microsoft-365-group-in-azure-active-directory"></a>Przywracanie usuniętej grupy Microsoft 365 w Azure Active Directory

Po usunięciu grupy Microsoft 365 w Azure Active Directory (Azure AD) usunięta grupa zostanie zachowana, ale nie będzie widoczna przez 30 dni od daty usunięcia. Przyczyną takiego zachowania jest to, że grupy i ich zawartość można przywrócić w razie potrzeby. Ta funkcja jest ograniczona wyłącznie do grup Microsoft 365 w usłudze Azure AD. Nie jest dostępna dla grup zabezpieczeń i grup dystrybucji. Należy pamiętać, że 30-dniowy okres przywracania grupy nie jest dostosowywany.

> [!NOTE]
> Nie używaj opcji `Remove-MsolGroup`, ponieważ powoduje trwale wyczyszczenie grup. Zawsze używaj `Remove-AzureADMSGroup` , aby usunąć grupę Microsoft 365.

Uprawnienia wymagane do przywrócenia grupy mogą być dowolnymi z następujących:

Rola | Uprawnienia
--------- | ---------
Administrator globalny, administrator grupy, obsługa SVR partnera i administrator usługi Intune | Może przywrócić wszystkie usunięte grupy Microsoft 365
Pomoc techniczna dla administratorów i pomoc partnerów | Może przywrócić wszystkie usunięte grupy Microsoft 365 z wyjątkiem tych, które są przypisane do roli administratora firmy
Użytkownik | Może przywrócić wszystkie usunięte grupy Microsoft 365 należące do nich

## <a name="view-and-manage-the-deleted-microsoft-365-groups-that-are-available-to-restore"></a>Wyświetl usunięte grupy Microsoft 365, które są dostępne do przywrócenia, i zarządzaj nimi

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta administratora użytkownika.

2. Wybierz opcję **Grupy**, a następnie **Usunięte grupy**, aby wyświetlić usunięte grupy, które są dostępne do przywrócenia.

    ![wyświetlanie grup, które są dostępne do przywrócenia](./media/groups-restore-deleted/deleted-groups3.png)

3. W bloku **Usunięte grupy** możesz:

   - Przywrócić usuniętą grupę i jej zawartość, wybierając opcję **Przywróć grupę**.
   - Trwale usunąć usuniętą grupę, wybierając opcję **Usuń trwale**. Aby trwale usunąć grupę, musisz być administratorem.

## <a name="view-the-deleted-microsoft-365-groups-that-are-available-to-restore-using-powershell"></a>Wyświetlanie usuniętych grup Microsoft 365, które są dostępne do przywrócenia przy użyciu programu PowerShell

Prz użyciu poniższych poleceń cmdlet możesz wyświetlić usunięte grupy, aby sprawdzić, czy te, które Cię interesują, nie zostały jeszcze trwale usunięte. Te polecenia cmdlet są częścią [modułu usługi Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/). Więcej informacji na temat tego modułu można znaleźć w artykule [Azure Active Directory PowerShell w wersji 2](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0).

1.  Uruchom następujące polecenie cmdlet, aby wyświetlić wszystkie usunięte grupy Microsoft 365 w organizacji usługi Azure AD, które nadal są dostępne do przywrócenia.
   

    ```powershell
    Get-AzureADMSDeletedGroup
    ```

2.  Ewentualnie, jeśli znasz identyfikator obiektu określonej grupy (i możesz go pobrać, korzystając z polecenia cmdlet w kroku 1), uruchom następujące polecenie cmdlet, aby sprawdzić, czy określona usunięta grupa nie została jeszcze trwale usunięta.

    ```
    Get-AzureADMSDeletedGroup –Id <objectId>
    ```

## <a name="how-to-restore-your-deleted-microsoft-365-group-using-powershell"></a>Jak przywrócić usuniętą grupę Microsoft 365 przy użyciu programu PowerShell

Po upewnieniu się, że grupa jest nadal dostępna do przywrócenia, możesz przywrócić usuniętą grupę za pomocą jednego z następujących kroków. Jeśli grupa zawiera dokumenty, witryny usługi SharePoint lub inne trwałe obiekty, całkowite przywrócenie grupy i jej zawartości może zająć do 24 godzin.

1. Uruchom następujące polecenie cmdlet, aby przywrócić grupę i jej zawartość.
 

   ```
    Restore-AzureADMSDeletedDirectoryObject –Id <objectId>
    ``` 

2. Możesz również uruchomić następujące polecenie cmdlet, aby trwale usunąć usuniętą grupę.
    

    ```
    Remove-AzureADMSDeletedDirectoryObject –Id <objectId>
    ```

## <a name="how-do-you-know-this-worked"></a>Skąd wiadomo, że to zadziałało?

Aby sprawdzić, czy grupa Microsoft 365 została pomyślnie przywrócona, uruchom `Get-AzureADGroup –ObjectId <objectId>` polecenie cmdlet w celu wyświetlenia informacji o grupie. Po ukończeniu żądania przywrócenia:

- Grupa zostanie wyświetlona na lewym pasku nawigacyjnym w programie Exchange
- Plan dla grupy zostanie wyświetlony w programie Planner
- Wszystkie witryny programu SharePoint i cała ich zawartość będą dostępne
- Do grupy można uzyskać dostęp z dowolnego punktu końcowego programu Exchange i innych obciążeń Microsoft365, które obsługują grupy Microsoft 365

## <a name="next-steps"></a>Następne kroki

Te artykuły zawierają dodatkowe informacje o grupach usługi Azure Active Directory.

* [Wyświetlanie istniejących grup](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Zarządzanie ustawieniami grupy](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Zarządzanie członkami grupy](../fundamentals/active-directory-groups-members-azure-portal.md)
* [Zarządzanie członkostwem w grupie](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Zarządzanie regułami dynamicznymi dla użytkowników w grupie](groups-dynamic-membership.md)
