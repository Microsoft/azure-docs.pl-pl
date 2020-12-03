---
title: Zbiorcze tworzenie użytkowników w portalu Azure Active Directory | Microsoft Docs
description: Dodaj użytkowników zbiorczo w centrum administracyjnym usługi Azure AD w Azure Active Directory
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
ms.openlocfilehash: c653f3e8583ef3aadff26cb2b7a3266555d313a2
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96547818"
---
# <a name="bulk-create-users-in-azure-active-directory"></a>Zbiorcze tworzenie użytkowników w Azure Active Directory

Azure Active Directory (Azure AD) obsługuje operacje tworzenia i usuwania użytkowników zbiorczych oraz obsługują pobieranie list użytkowników. Po prostu wypełnij szablon wartości rozdzielanych przecinkami (CSV), które można pobrać z portalu usługi Azure AD.

## <a name="required-permissions"></a>Wymagane uprawnienia

Aby można było tworzyć zbiorczo użytkowników w portalu administracyjnym, użytkownik musi być zalogowany jako Administrator globalny lub administrator użytkowników.

## <a name="understand-the-csv-template"></a>Zrozumienie szablonu CSV

Pobierz i wypełnij szablon CSV przekazywanie zbiorcze, aby ułatwić pomyślne utworzenie użytkowników usługi Azure AD. Pobrany szablon CSV może wyglądać podobnie do tego przykładu:

![Arkusz kalkulacyjny do przekazywania i wywoływać wywołajeń objaśniających przeznaczenie i wartości dla każdego wiersza i kolumny](./media/users-bulk-add/create-template-example.png)

> [!WARNING]
> W przypadku dodawania tylko jednego wpisu przy użyciu szablonu CSV należy zachować wiersz 3 i dodać nowy wpis do wiersza 4.

### <a name="csv-template-structure"></a>Struktura szablonu CSV

Wiersze pobranego szablonu CSV są następujące:

- **Numer wersji**: pierwszy wiersz zawierający numer wersji musi być uwzględniony w pliku CSV przekazywania.
- **Nagłówki kolumn**: format nagłówków kolumn jest &lt; *Item name* &gt; &lt; *wymagany lub pusty* &gt; . Na przykład `Name [displayName] Required`. Niektóre starsze wersje szablonu mogą mieć niewielkie wahania.
- **Przykład wiersza**: w szablonie zamieszczono wiersz przykładów dopuszczalnych wartości dla każdej kolumny. Musisz usunąć wiersz przykładów i zastąpić go własnymi wpisami.

### <a name="additional-guidance"></a>Dodatkowe wskazówki

- Pierwsze dwa wiersze szablonu przekazywania nie mogą być usuwane ani modyfikowane lub nie można przetworzyć przekazywania.
- Wymagane kolumny są wyświetlane jako pierwsze.
- Nie zalecamy dodawania nowych kolumn do szablonu. Wszelkie dodatkowe dodawane kolumny są ignorowane i nie są przetwarzane.
- Zalecamy pobranie najnowszej wersji szablonu CSV tak często, jak to możliwe.
- Upewnij się, że nie ma żadnych niezamierzonych spacji przed żadnym polem lub po nim. W przypadku **nazwy głównej użytkownika**, jeśli takie odstępy spowodują Niepowodzenie importu.

## <a name="to-create-users-in-bulk"></a>Aby tworzyć użytkowników zbiorczo

1. [Zaloguj się do swojej organizacji usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta, które jest administratorem użytkownika w organizacji.
1. W usłudze Azure AD wybierz pozycję **Użytkownicy**  >  **Tworzenie zbiorcze**.
1. Na stronie **Tworzenie zbiorczego użytkownika** wybierz pozycję **Pobierz** , aby otrzymać prawidłowy plik wartości rozdzielanych przecinkami (CSV) właściwości użytkownika, a następnie Dodaj Dodaj użytkowników, których chcesz utworzyć.

   ![Wybierz lokalny plik CSV, w którym chcesz wyświetlić listę użytkowników, których chcesz dodać](./media/users-bulk-add/upload-button.png)

1. Otwórz plik CSV i Dodaj wiersz dla każdego użytkownika, który chcesz utworzyć. Jedyne wymagane wartości to **Nazwa**, **główna nazwa użytkownika**, **początkowe hasło** i **blokowanie logowania (tak/nie)**. Następnie zapisz plik.

   [![Plik CSV zawiera nazwy i identyfikatory użytkowników do utworzenia](./media/users-bulk-add/add-csv-file.png)](./media/users-bulk-add/add-csv-file.png#lightbox)

1. Na stronie **Tworzenie zbiorcze użytkownika** w obszarze Przekaż plik CSV przejdź do pliku. Po wybraniu pliku i kliknięciu przycisku **Prześlij** zostanie uruchomiony Walidacja pliku CSV.
1. Po zweryfikowaniu zawartości pliku zostanie wyświetlony **plik pomyślnie przekazane**. Jeśli występują błędy, należy je usunąć przed przesłaniem zadania.
1. Gdy plik zostanie pomyślnie zweryfikowany, wybierz pozycję **Prześlij** , aby rozpocząć operację zbiorczą platformy Azure, która importuje nowych użytkowników.
1. Po zakończeniu operacji importowania zobaczysz powiadomienie o stanie zadania operacji zbiorczej.

W przypadku wystąpienia błędów można pobrać i wyświetlić plik wyników na stronie **wyników operacji zbiorczej** . Plik zawiera przyczynę każdego błędu. Przesyłanie pliku musi być zgodne z podanym szablonem i zawierać dokładne nazwy kolumn.

## <a name="check-status"></a>Sprawdzanie stanu

Na stronie **wyniki operacji zbiorczej** można zobaczyć stan wszystkich oczekujących żądań zbiorczych.

   [![Sprawdzanie stanu tworzenia na stronie wyników operacji zbiorczych](./media/users-bulk-add/bulk-center.png)](./media/users-bulk-add/bulk-center.png#lightbox)

Następnie możesz sprawdzić, czy utworzone przez Ciebie użytkownicy znajdują się w organizacji usługi Azure AD w Azure Portal lub przy użyciu programu PowerShell.

## <a name="verify-users-in-the-azure-portal"></a>Weryfikowanie użytkowników w Azure Portal

1. [Zaloguj się do centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta, które jest administratorem użytkownika w organizacji.
1. W okienku nawigacji wybierz pozycję **Azure Active Directory**.
1. W obszarze **Zarządzanie** wybierz pozycję **Użytkownicy**.
1. W obszarze **Pokaż** wybierz opcję **Wszyscy użytkownicy** i sprawdź, czy utworzone przez Ciebie użytkownicy znajdują się na liście.

### <a name="verify-users-with-powershell"></a>Weryfikowanie użytkowników przy użyciu programu PowerShell

Uruchom następujące polecenie:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Powinny zostać wyświetlone wszystkie utworzone przez Ciebie użytkownicy.

## <a name="bulk-import-service-limits"></a>Limity usług importowania zbiorczego

Każde działanie zbiorcze służące do tworzenia użytkowników może być uruchamiane przez maksymalnie jedną godzinę. Dzięki temu można utworzyć zbiorczo co najmniej 50 000 użytkowników.

## <a name="next-steps"></a>Następne kroki

- [Zbiorcze usuwanie użytkowników](users-bulk-delete.md)
- [Pobierz listę użytkowników](users-bulk-download.md)
- [Zbiorcze przywracanie użytkowników](users-bulk-restore.md)
