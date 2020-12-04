---
title: Przywracanie zbiorcze usuniętych użytkowników w portalu Azure Active Directory | Microsoft Docs
description: Przywróć zbiorczo usuniętych użytkowników w centrum administracyjnym usługi Azure AD w Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 12/02/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: be3947e3de18f8ccaf47382c4035e187521ac710
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96571508"
---
# <a name="bulk-restore-deleted-users-in-azure-active-directory"></a>Przywracanie zbiorcze usuniętych użytkowników w Azure Active Directory

Azure Active Directory (Azure AD) obsługuje operacje przywracania użytkowników zbiorczych i obsługuje pobieranie list użytkowników, grup i członków grupy.

## <a name="understand-the-csv-template"></a>Zrozumienie szablonu CSV

Pobierz i wypełnij szablon CSV, aby ułatwić pomyślne przywrócenie użytkowników usługi Azure AD. Pobrany szablon CSV może wyglądać podobnie do tego przykładu:

![Arkusz kalkulacyjny do przekazywania i wywoływać wywołajeń objaśniających przeznaczenie i wartości dla każdego wiersza i kolumny](./media/users-bulk-restore/understand-template.png)

### <a name="csv-template-structure"></a>Struktura szablonu CSV

Wiersze pobranego szablonu CSV są następujące:

- **Numer wersji**: pierwszy wiersz zawierający numer wersji musi być uwzględniony w pliku CSV przekazywania.
- **Nagłówki kolumn**: format nagłówków kolumn jest &lt; *Item name* &gt; &lt; *wymagany lub pusty* &gt; . Na przykład `Object ID [objectId] Required`. Niektóre starsze wersje szablonu mogą mieć niewielkie wahania.
- **Przykład wiersza**: w szablonie zamieszczono wiersz przykładów dopuszczalnych wartości dla każdej kolumny. Musisz usunąć wiersz przykładów i zastąpić go własnymi wpisami.

### <a name="additional-guidance"></a>Dodatkowe wskazówki

- Pierwsze dwa wiersze szablonu przekazywania nie mogą być usuwane ani modyfikowane lub nie można przetworzyć przekazywania.
- Wymagane kolumny są wyświetlane jako pierwsze.
- Nie zalecamy dodawania nowych kolumn do szablonu. Wszelkie dodatkowe dodawane kolumny są ignorowane i nie są przetwarzane.
- Zalecamy pobranie najnowszej wersji szablonu CSV tak często, jak to możliwe.

## <a name="to-bulk-restore-users"></a>Do przywracania zbiorczego użytkowników

1. [Zaloguj się do swojej organizacji usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta, które jest administratorem użytkownika w organizacji usługi Azure AD.
1. W usłudze Azure AD wybierz pozycję **Użytkownicy**  >  **usunięci**.
1. Na stronie **usunięci użytkownicy** wybierz pozycję **przywracanie zbiorcze** , aby przekazać prawidłowy plik CSV właściwości użytkowników do przywrócenia.

    ![Wybierz polecenie Zbiorcza przywracanie na stronie usunięci użytkownicy](./media/users-bulk-restore/bulk-restore.png)

1. Otwórz szablon CSV i Dodaj wiersz dla każdego użytkownika, który ma zostać przywrócony. Jedyną wymaganą wartością jest **objectid**. Następnie zapisz plik.

    :::image type="content" source="./media/users-bulk-restore/upload-button.png" alt-text="Wybierz lokalny plik CSV, w którym chcesz wyświetlić listę użytkowników, których chcesz dodać":::

1. Na stronie **przywracanie zbiorcze** w obszarze **Przekaż plik CSV** przejdź do pliku. Po wybraniu pliku i kliknięciu przycisku **Prześlij** zostanie uruchomiony Walidacja pliku CSV.
1. Gdy zawartość pliku zostanie sprawdzona, zostanie wyświetlony **plik przekazany pomyślnie**. Jeśli występują błędy, należy je usunąć przed przesłaniem zadania.
1. Gdy plik zostanie pomyślnie zweryfikowany, wybierz pozycję **Prześlij** , aby rozpocząć operację zbiorczą platformy Azure, która przywraca użytkowników.
1. Po zakończeniu operacji przywracania zobaczysz powiadomienie, że operacja zbiorcza zakończyła się pomyślnie.

W przypadku wystąpienia błędów można pobrać i wyświetlić plik wyników na stronie **wyników operacji zbiorczej** . Plik zawiera przyczynę każdego błędu.

## <a name="check-status"></a>Sprawdzanie stanu

Na stronie **wyniki operacji zbiorczej** można zobaczyć stan wszystkich oczekujących żądań zbiorczych.

[![Sprawdź stan na stronie wyniki operacji zbiorczych.](./media/users-bulk-restore/bulk-center.png)](./media/users-bulk-restore/bulk-center.png#lightbox)

Następnie możesz sprawdzić, czy przywróconi użytkownicy znajdują się w organizacji usługi Azure AD w Azure Portal lub przy użyciu programu PowerShell.

## <a name="view-restored-users-in-the-azure-portal"></a>Wyświetl przywróconych użytkowników w Azure Portal

1. [Zaloguj się do centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta, które jest administratorem użytkownika w organizacji.
1. W okienku nawigacji wybierz pozycję **Azure Active Directory**.
1. W obszarze **Zarządzanie** wybierz pozycję **Użytkownicy**.
1. W obszarze **Pokaż** wybierz pozycję **Wszyscy użytkownicy** i sprawdź, czy na liście znajdują się użytkownicy, których przywrócono.

### <a name="view-users-with-powershell"></a>Wyświetlanie użytkowników przy użyciu programu PowerShell

Uruchom następujące polecenie:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Powinna zostać wyświetlona lista użytkowników, których przywrócono.

## <a name="next-steps"></a>Następne kroki

- [Importowanie zbiorczych użytkowników](users-bulk-add.md)
- [Zbiorcze usuwanie użytkowników](users-bulk-delete.md)
- [Pobierz listę użytkowników](users-bulk-download.md)
