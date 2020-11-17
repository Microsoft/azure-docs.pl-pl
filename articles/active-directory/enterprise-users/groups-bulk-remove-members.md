---
title: Zbiorcze usuwanie członków grupy przez przekazanie pliku CSV — Azure Active Directory | Microsoft Docs
description: Usuń członków grupy w operacjach zbiorczych w centrum administracyjnym platformy Azure.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 11/15/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83b905faa892cc338b5bdf4f998ad95f7b8a484e
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94650858"
---
# <a name="bulk-remove-group-members-in-azure-active-directory"></a>Zbiorcze usuwanie członków grupy w Azure Active Directory

Korzystając z portalu Azure Active Directory (Azure AD), można usunąć dużą liczbę członków z grupy, używając pliku wartości rozdzielanych przecinkami (CSV) do zbiorczego usuwania elementów członkowskich grupy.

## <a name="understand-the-csv-template"></a>Zrozumienie szablonu CSV

Pobierz i wypełnij szablon CSV przekazywania zbiorczego, aby pomyślnie dodać członków grupy usługi Azure AD. Szablon CSV może wyglądać podobnie do tego przykładu:

![Arkusz kalkulacyjny do przekazywania i wywoływać wywołajeń objaśniających przeznaczenie i wartości dla każdego wiersza i kolumny](./media/groups-bulk-remove-members/template-example.png)

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

## <a name="to-bulk-remove-group-members"></a>Aby zbiorczo usuwać członków grupy

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta administratora użytkowników w organizacji. Właściciele grupy mogą również zbiorczo usuwać członków należących do nich grup.
1. W usłudze Azure AD wybierz kolejno pozycje **grupy**  >  **wszystkie grupy**.
1. Otwórz grupę, z której zostaną usunięte elementy członkowskie, a następnie wybierz pozycję **Członkowie**.
1. Na stronie **Członkowie** wybierz pozycję **Usuń członków**.
1. Na stronie **usuwanie zbiorczych członków grupy** wybierz pozycję **Pobierz** , aby pobrać szablon pliku CSV z właściwościami wymaganych elementów członkowskich grupy.

   ![Polecenie Usuń członków znajduje się na stronie profilu grupy](./media/groups-bulk-remove-members/remove-panel.png)

1. Otwórz plik CSV i Dodaj wiersz dla każdego członka grupy, który chcesz usunąć z grupy (wymagane wartości to identyfikator obiektu elementu członkowskiego lub główna nazwa użytkownika). Następnie zapisz plik.

    :::image type="content" source="./media/groups-bulk-import-members/csv-file.png" alt-text="Plik CSV zawiera nazwy i identyfikatory członków grupy do usunięcia":::

1. Na stronie **usuwanie zbiorczych członków grupy** , w obszarze **Przekaż plik CSV**, przejdź do pliku. Po wybraniu pliku zostanie uruchomiony Walidacja pliku CSV.
1. Po sprawdzeniu poprawności zawartości pliku, Strona importowania zbiorczego wyświetla **pomyślnie przekazany plik**. Jeśli występują błędy, należy je usunąć przed przesłaniem zadania.
1. Gdy plik zostanie pomyślnie zweryfikowany, wybierz pozycję **Prześlij** , aby rozpocząć operację zbiorczą platformy Azure, która usuwa członków grupy z grupy.
1. Po zakończeniu operacji usuwania zobaczysz powiadomienie, że operacja zbiorcza zakończyła się pomyślnie.

## <a name="check-removal-status"></a>Sprawdź stan usuwania

Na stronie **wyniki operacji zbiorczej** można zobaczyć stan wszystkich oczekujących żądań zbiorczych.

[![Sprawdź stan na stronie wyników operacji zbiorczych](./media/groups-bulk-remove-members/bulk-center.png)](./media/groups-bulk-remove-members/bulk-center.png#lightbox)

Aby uzyskać szczegółowe informacje na temat każdego elementu wiersza w ramach operacji zbiorczej, wybierz wartości z kolumny **# Success**, **# Failure** lub **Total Requests** . Jeśli wystąpią błędy, zostaną wyświetlone przyczyny niepowodzenia.

## <a name="bulk-removal-service-limits"></a>Limity usługi usuwania zbiorczego

Każde działanie zbiorcze służące do usuwania listy członków grupy można uruchomić przez maksymalnie jedną godzinę. Umożliwia to usunięcie listy co najmniej 40 000 członków.

## <a name="next-steps"></a>Następne kroki

- [Członkowie grupy importu zbiorczego](groups-bulk-import-members.md)
- [Pobierz członków grupy](groups-bulk-download-members.md)
- [Pobierz listę wszystkich grup](groups-bulk-download.md)
