---
title: Usuwanie zbiorcze użytkowników w portalu Azure Active Directory | Microsoft Docs
description: Usuń użytkowników zbiorczo w centrum administracyjnym platformy Azure w Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 12/02/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: b57d675003c1ebeb29927b86338f95cf7dd68090
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96574467"
---
# <a name="bulk-delete-users-in-azure-active-directory"></a>Usuwanie zbiorcze użytkowników w Azure Active Directory

Za pomocą portalu usługi Azure Active Directory (Azure AD) można usunąć dużą liczbę elementów członkowskich do grupy przy użyciu pliku wartości rozdzielanych przecinkami (CSV) do zbiorczego usuwania użytkowników.

## <a name="understand-the-csv-template"></a>Zrozumienie szablonu CSV

Pobierz i wypełnij szablon CSV, aby pomóc w zbiorczym pomyślnym usunięciu użytkowników usługi Azure AD. Pobrany szablon CSV może wyglądać podobnie do tego przykładu:

![Arkusz kalkulacyjny do przekazywania i wywoływać wywołajeń objaśniających przeznaczenie i wartości dla każdego wiersza i kolumny](./media/users-bulk-delete/understand-template.png)

### <a name="csv-template-structure"></a>Struktura szablonu CSV

Wiersze pobranego szablonu CSV są następujące:

- **Numer wersji**: pierwszy wiersz zawierający numer wersji musi być uwzględniony w pliku CSV przekazywania.
- **Nagłówki kolumn**: format nagłówków kolumn jest &lt;  &gt; &lt; *wymagany lub pusty* &gt; . Na przykład `User name [userPrincipalName] Required`. Niektóre starsze wersje szablonu mogą mieć niewielkie wahania.
- **Przykład wiersza**: w szablonie zamieszczono wiersz przykładów dopuszczalnych wartości dla każdej kolumny. Musisz usunąć wiersz przykładów i zastąpić go własnymi wpisami.

### <a name="additional-guidance"></a>Dodatkowe wskazówki

- Pierwsze dwa wiersze szablonu przekazywania nie mogą być usuwane ani modyfikowane lub nie można przetworzyć przekazywania.
- Wymagane kolumny są wyświetlane jako pierwsze.
- Nie zalecamy dodawania nowych kolumn do szablonu. Wszelkie dodatkowe dodawane kolumny są ignorowane i nie są przetwarzane.
- Zalecamy pobranie najnowszej wersji szablonu CSV tak często, jak to możliwe.

## <a name="to-bulk-delete-users"></a>Aby usunąć zbiorczo użytkowników

1. [Zaloguj się do swojej organizacji usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta, które jest administratorem użytkownika w organizacji.
1. W usłudze Azure AD wybierz pozycję **Użytkownicy**  >  **zbiorczo usuwać**.
1. Na stronie **usuwania zbiorczego użytkownika** wybierz pozycję **Pobierz** , aby otrzymać prawidłowy plik CSV właściwości użytkownika.

   ![Wybierz lokalny plik CSV, w którym chcesz wyświetlić listę użytkowników do usunięcia](./media/users-bulk-delete/bulk-delete.png)

1. Otwórz plik CSV i Dodaj wiersz dla każdego użytkownika, który chcesz usunąć. Jedyną wymaganą wartością jest **główna nazwa użytkownika**. Następnie zapisz plik.

   ![Plik CSV zawiera nazwy i identyfikatory użytkowników do usunięcia](./media/users-bulk-delete/delete-csv-file.png)

1. Na stronie **usuwania zbiorczego użytkownika** w obszarze **Przekaż plik CSV** przejdź do pliku. Po wybraniu pliku i kliknięciu przycisku Prześlij zostanie uruchomiony Walidacja pliku CSV.
1. Gdy zawartość pliku zostanie sprawdzona, zostanie wyświetlony **plik przekazany pomyślnie**. Jeśli występują błędy, należy je usunąć przed przesłaniem zadania.
1. Gdy plik zostanie pomyślnie zweryfikowany, wybierz pozycję **Prześlij** , aby rozpocząć operację zbiorczą platformy Azure, która spowoduje usunięcie użytkowników.
1. Po zakończeniu operacji usuwania zobaczysz powiadomienie, że operacja zbiorcza zakończyła się pomyślnie.

W przypadku wystąpienia błędów można pobrać i wyświetlić plik wyników na stronie **wyników operacji zbiorczej** . Plik zawiera przyczynę każdego błędu.

## <a name="check-status"></a>Sprawdzanie stanu

Na stronie **wyniki operacji zbiorczej** można zobaczyć stan wszystkich oczekujących żądań zbiorczych.

   [![Sprawdź stan usuwania na stronie wyniki operacji zbiorczych.](./media/users-bulk-delete/bulk-center.png)](./media/users-bulk-delete/bulk-center.png#lightbox)

Następnie możesz sprawdzić, czy usunięte użytkownicy znajdują się w organizacji usługi Azure AD w Azure Portal lub przy użyciu programu PowerShell.

## <a name="verify-deleted-users-in-the-azure-portal"></a>Weryfikowanie usuniętych użytkowników w Azure Portal

1. Zaloguj się do Azure Portal przy użyciu konta, które jest administratorem użytkownika w organizacji.
1. W okienku nawigacji wybierz pozycję **Azure Active Directory**.
1. W obszarze **Zarządzanie** wybierz pozycję **Użytkownicy**.
1. W obszarze **Pokaż** zaznacz opcję tylko **Wszyscy użytkownicy** i sprawdź, czy usunięte użytkownicy nie są już wyświetlani.

### <a name="verify-deleted-users-with-powershell"></a>Weryfikowanie usuniętych użytkowników przy użyciu programu PowerShell

Uruchom następujące polecenie:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Upewnij się, że usunięci użytkownicy nie są już wyświetlani.

## <a name="next-steps"></a>Następne kroki

- [Zbiorcze dodawanie użytkowników](users-bulk-add.md)
- [Pobierz listę użytkowników](users-bulk-download.md)
- [Zbiorcze przywracanie użytkowników](users-bulk-restore.md)
