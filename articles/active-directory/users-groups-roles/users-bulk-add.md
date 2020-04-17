---
title: Zbiorcze tworzenie użytkowników w portalu usługi Azure Active Directory | Dokumenty firmy Microsoft
description: Zbiorcze dodawanie użytkowników w centrum administracyjnym usługi Azure AD w usłudze Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/16/2020
ms.topic: article
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3a8b9cb9701288d24534ab08940f6dbd4a698ad
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532945"
---
# <a name="bulk-create-users-in-azure-active-directory"></a>Zbiorcze tworzenie użytkowników w usłudze Azure Active Directory

Usługa Azure Active Directory (Azure AD) obsługuje operacje tworzenia i usuwania użytkowników zbiorczych, zbiorcze zapraszanie gości oraz obsługuje pobieranie list użytkowników, grup i członków grupy.

## <a name="required-permissions"></a>Wymagane uprawnienia

Aby zbiorczo tworzyć użytkowników w portalu administracyjnym, użytkownik musi być zalogowany jako administrator globalny lub administrator użytkownika.

## <a name="to-create-users-in-bulk"></a>Aby zbiorczo tworzyć użytkowników

1. [Zaloguj się do organizacji usługi Azure AD](https://aad.portal.azure.com) przy za pomocą konta, które jest administratorem użytkownika w organizacji.
1. W usłudze Azure AD wybierz pozycję **Użytkownicy** > **zbiorczo utwórz**.
1. Na stronie **Zbiorcze tworzenie użytkownika** wybierz pozycję **Pobierz,** aby otrzymać prawidłowy plik właściwości użytkownika (CSV) z osobnymi przecinkami, a następnie dodaj użytkowników, których chcesz utworzyć.

   ![Wybierz lokalny plik CSV, w którym wyświetlisz listę użytkowników, których chcesz dodać](./media/users-bulk-add/upload-button.png)

1. Otwórz plik CSV i dodaj wiersz dla każdego użytkownika, którego chcesz utworzyć. Jedynymi wymaganymi wartościami są **Nazwa,** **Nazwa główna użytkownika,** **Hasło początkowe** i **Zaloguj się bloku (Tak/Nie)**. Następnie zapisz plik.

   ![Plik CSV zawiera nazwy i identyfikatory użytkowników do utworzenia](./media/users-bulk-add/add-csv-file.png)

1. Na stronie **Zbiorcze tworzenie użytkownika** w obszarze Przekazywanie pliku CSV przejdź do pliku. Po wybraniu pliku i kliknięciu **przycisku Prześlij**rozpocznie się sprawdzanie poprawności pliku CSV.
1. Po sprawdzeniu poprawności zawartości pliku zostanie **wyświetlony folder Plik przekazany pomyślnie**. Jeśli występują błędy, należy je naprawić, zanim będzie można przesłać zadanie.
1. Gdy plik przejdzie sprawdzanie poprawności, wybierz **przycisk Prześlij,** aby rozpocząć operację zbiorczą platformy Azure, która importuje nowych użytkowników.
1. Po zakończeniu operacji importowania zostanie wyświetlone powiadomienie o stanie zadania operacji zbiorczej.

Jeśli występują błędy, można pobrać i wyświetlić plik wyników na stronie **Wyniki operacji zbiorczej.** Plik zawiera przyczynę każdego błędu.

## <a name="check-status"></a>Sprawdzanie stanu

Stan wszystkich oczekujących żądań zbiorczych można zobaczyć na stronie **Wyniki operacji zbiorczej.**

   [![](media/users-bulk-add/bulk-center.png "Check create status in the Bulk Operations Results page")](media/users-bulk-add/bulk-center.png#lightbox)

Następnie można sprawdzić, czy użytkownicy, których utworzono istnieją w organizacji usługi Azure AD albo w witrynie Azure portal lub przy użyciu programu PowerShell.

## <a name="verify-users-in-the-azure-portal"></a>Weryfikowanie użytkowników w witrynie Azure portal

1. [Zaloguj się do centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy za pomocą konta, które jest administratorem użytkownika w organizacji.
1. W okienku nawigacji wybierz pozycję **Azure Active Directory**.
1. W obszarze **Zarządzaj** wybierz pozycję **Użytkownicy**.
1. W obszarze **Pokaż**wybierz pozycję **Wszyscy użytkownicy** i sprawdź, czy utworzyli użytkownicy są na liście.

### <a name="verify-users-with-powershell"></a>Weryfikowanie użytkowników za pomocą programu PowerShell

Uruchom następujące polecenie:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Powinieneś zobaczyć, że utworzyli użytkownicy są na liście.

## <a name="bulk-import-service-limits"></a>Limity usług importu zbiorczego

Każde działanie zbiorcze do tworzenia użytkowników może działać przez maksymalnie jedną godzinę. Umożliwia to masowe tworzenie co najmniej 50 000 użytkowników.

## <a name="next-steps"></a>Następne kroki

- [Zbiorcze usuwanie użytkowników](users-bulk-delete.md)
- [Lista użytkowników do pobrania](users-bulk-download.md)
- [Użytkownicy przywracania zbiorczego](users-bulk-restore.md)
