---
title: Przekazywanie zbiorcze w celu dodania lub utworzenia elementów członkowskich grupy — Azure Active Directory | Microsoft Docs
description: Dodaj zbiorczo członków grupy w centrum administracyjnym Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/27/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: add8a533f2b4fba9a9e881442e43030adb7e2b53
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762012"
---
# <a name="bulk-add-group-members-in-azure-active-directory"></a>Zbiorcze dodawanie elementów członkowskich grupy w Azure Active Directory

Za pomocą portalu usługi Azure Active Directory (Azure AD) można dodać dużą liczbę elementów członkowskich do grupy przy użyciu pliku wartości rozdzielanych przecinkami (CSV) w celu zbiorczego importowania elementów członkowskich grupy.

## <a name="understand-the-csv-template"></a>Zrozumienie szablonu CSV

Pobierz i wypełnij szablon CSV przekazywania zbiorczego, aby pomyślnie dodać członków grupy usługi Azure AD. Szablon CSV może wyglądać podobnie do tego przykładu:

![Arkusz kalkulacyjny do przekazywania i wywoływać wywołajeń objaśniających przeznaczenie i wartości dla każdego wiersza i kolumny](./media/groups-bulk-import-members/template-with-callouts.png)

### <a name="csv-template-structure"></a>Struktura szablonu CSV

Wiersze pobranego szablonu CSV są następujące:

- **Numer wersji**: pierwszy wiersz zawierający numer wersji musi być uwzględniony w pliku CSV przekazywania.
- **Nagłówki kolumn**: format nagłówków kolumn jest &lt; *Item name* &gt; &lt; *wymagany lub pusty* &gt; . Na przykład `Member object ID or user principal name [memberObjectIdOrUpn] Required`. Niektóre starsze wersje szablonu mogą mieć niewielkie wahania. Aby zmienić członkostwo w grupie, można użyć opcji identyfikatora obiektu Członkowskiego lub nazwy głównej użytkownika.
- **Przykład wiersza**: w szablonie zamieszczono wiersz przykładów dopuszczalnych wartości dla każdej kolumny. Musisz usunąć wiersz przykładów i zastąpić go własnymi wpisami.

### <a name="additional-guidance"></a>Dodatkowe wskazówki

- Pierwsze dwa wiersze szablonu przekazywania nie mogą być usuwane ani modyfikowane lub nie można przetworzyć przekazywania.
- Wymagane kolumny są wyświetlane jako pierwsze.
- Nie zalecamy dodawania nowych kolumn do szablonu. Wszelkie dodatkowe dodawane kolumny są ignorowane i nie są przetwarzane.
- Zalecamy pobranie najnowszej wersji szablonu CSV tak często, jak to możliwe.
- Dodaj co najmniej dwóch głównych nazw użytkowników lub identyfikatorów obiektów, aby pomyślnie przekazać plik.

## <a name="to-bulk-import-group-members"></a>Aby zaimportować zbiorczo członków grupy

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta administratora użytkowników w organizacji. Właściciele grupy mogą również zbiorczo importować członków należących do nich grup.
1. W usłudze Azure AD wybierz kolejno pozycje **grupy**  >  **wszystkie grupy**.
1. Otwórz grupę, do której dodawane są elementy członkowskie, a następnie wybierz pozycję **Członkowie**.
1. Na stronie **Członkowie** wybierz pozycję **Importuj członków**.
1. Na stronie **grupy importu zbiorczego** kliknij pozycję **Pobierz** , aby pobrać szablon pliku CSV z wymaganymi właściwościami elementów członkowskich grupy.

    ![Polecenie Importuj elementy członkowskie znajduje się na stronie profilu grupy](./media/groups-bulk-import-members/import-panel.png)

1. Otwórz plik CSV i Dodaj wiersz dla każdego członka grupy, który ma zostać zaimportowany do grupy (wymagane wartości to **Identyfikator obiektu elementu członkowskiego** lub **główna nazwa użytkownika**). Następnie zapisz plik.

    :::image type="content" source="./media/groups-bulk-import-members/csv-file.png" alt-text="Plik CSV zawiera nazwy i identyfikatory członków do zaimportowania":::

1. Na stronie **grupy importu zbiorczego** , w obszarze **Przekaż plik CSV**, przejdź do pliku. Po wybraniu pliku zostanie uruchomiony Walidacja pliku CSV.
1. Po sprawdzeniu poprawności zawartości pliku, Strona importowania zbiorczego wyświetla **pomyślnie przekazany plik**. Jeśli występują błędy, należy je usunąć przed przesłaniem zadania.
1. Gdy plik zostanie pomyślnie zweryfikowany, wybierz pozycję **Prześlij** , aby rozpocząć operację zbiorczą platformy Azure, która importuje członków grupy do grupy.
1. Po zakończeniu operacji importowania zobaczysz powiadomienie, że operacja zbiorcza zakończyła się pomyślnie.

## <a name="check-import-status"></a>Sprawdź stan importowania

Na stronie **wyniki operacji zbiorczej** można zobaczyć stan wszystkich oczekujących żądań zbiorczych.

[![Sprawdź stan na stronie wyniki operacji zbiorczych.](media/groups-bulk-import-members/bulk-center.png)](media/groups-bulk-import-members/bulk-center.png#lightbox)

Aby uzyskać szczegółowe informacje na temat każdego elementu wiersza w ramach operacji zbiorczej, wybierz wartości z kolumny **# Success**, **# Failure**lub **Total Requests** . Jeśli wystąpią błędy, zostaną wyświetlone przyczyny niepowodzenia.

## <a name="bulk-import-service-limits"></a>Limity usług importowania zbiorczego

Każde działanie zbiorcze służące do importowania listy członków grupy może być uruchamiane przez maksymalnie jedną godzinę. Umożliwia to zaimportowanie listy maksymalnie 40 000 elementów członkowskich.

## <a name="next-steps"></a>Następne kroki

- [Zbiorcze usuwanie członków grupy](groups-bulk-remove-members.md)
- [Pobierz członków grupy](groups-bulk-download-members.md)
- [Pobierz listę wszystkich grup](groups-bulk-download.md)
