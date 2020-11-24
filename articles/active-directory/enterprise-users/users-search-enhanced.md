---
title: Ulepszenia zarządzania użytkownikami (wersja zapoznawcza) — Azure Active Directory | Microsoft Docs
description: Opisuje sposób, w jaki Azure Active Directory umożliwia wyszukiwanie użytkowników, filtrowanie i więcej informacji o użytkownikach.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8684b2af99eda00fbba47db444fcf6ee19117a1e
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95488525"
---
# <a name="user-management-enhancements-preview-in-azure-active-directory"></a>Ulepszenia zarządzania użytkownikami (wersja zapoznawcza) w Azure Active Directory

W tym artykule opisano sposób używania ulepszeń zarządzania użytkownikami w wersji zapoznawczej w portalu Azure Active Directory (Azure AD). Strony **Wszyscy użytkownicy** i **usunięci użytkownicy** zostali zaktualizowani, aby uzyskać więcej informacji i ułatwić znajdowanie użytkowników. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)zapoznawczych Microsoft Azure.

Zmiany w wersji zapoznawczej obejmują:

- Bardziej widoczne właściwości użytkownika, w tym identyfikator obiektu, stan synchronizacji katalogu, typ tworzenia i wystawca tożsamości
- Search umożliwia teraz wyszukiwanie podciągów i połączone wyszukiwanie nazw, wiadomości e-mail i identyfikatorów obiektów
- Ulepszone filtrowanie według typu użytkownika (elementu członkowskiego, gościa, brak), stanu synchronizacji katalogu, typu tworzenia, nazwy firmy i nazwy domeny
- Nowe możliwości sortowania dotyczące właściwości, takich jak nazwa i główna nazwa użytkownika
- Nowa łączna liczba użytkowników, którzy aktualizujeją wyszukiwania lub filtry

> [!NOTE]
> Ta wersja zapoznawcza jest obecnie niedostępna dla dzierżawców Azure AD B2C.

## <a name="find-the-preview"></a>Znajdź Podgląd

Wersja zapoznawcza jest domyślnie włączona, więc można z niej korzystać od razu. Najnowsze funkcje i ulepszenia można sprawdzić, wybierając pozycję funkcje w **wersji zapoznawczej** na stronie **Wszyscy użytkownicy** . Wszystkie strony, które zostały zaktualizowane w ramach tej wersji zapoznawczej, będą wyświetlać tag podglądu. Jeśli masz jakieś problemy, możesz przełączyć się z powrotem do starszego środowiska:

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) i wybierz pozycję **Użytkownicy**.
1. Na stronie **Użytkownicy — wszyscy użytkownicy** wybierz transparent w górnej części strony.
1. W okienku **Podgląd funkcji** Włącz opcję **ulepszone zarządzanie użytkownikami** .

   ![Jak i gdzie włączać i wyłączać ulepszone zarządzanie użytkownikami](./media/users-search-enhanced/enable-preview.png)

Dziękujemy za Twoją opinię, dzięki czemu możemy ulepszyć nasze środowisko.

## <a name="more-user-properties"></a>Więcej właściwości użytkownika

Wprowadziliśmy pewne zmiany w kolumnach dostępnych na stronach **Wszyscy użytkownicy** i **usunięci użytkownicy** . Oprócz istniejących kolumn, które udostępniamy do zarządzania listą użytkowników, dodaliśmy jeszcze kilka kolumn.

### <a name="all-users-page"></a>Strona wszyscy użytkownicy

Na stronie **Wszyscy użytkownicy** są wyświetlane następujące właściwości użytkownika:

- Nazwa: Nazwa wyświetlana użytkownika.
- Główna nazwa użytkownika: główna nazwa użytkownika (UPN) użytkownika.
- Typ użytkownika: członek, gość, brak.
- Katalog synchronizowany: wskazuje, czy użytkownik jest synchronizowany z katalogu lokalnego.
- Wystawca tożsamości: wystawcy tożsamości użyta do zalogowania się do konta użytkownika.
- Identyfikator obiektu: identyfikator obiektu użytkownika.
- Typ tworzenia: wskazuje sposób tworzenia konta użytkownika.
- Nazwa firmy: Nazwa firmy, która jest skojarzona z użytkownikiem.
- Stan zaproszenia: stan zaproszenia dla użytkownika-gościa.
- Poczta: wiadomość e-mail użytkownika.
- Ostatnie Logowanie: Data ostatniego logowania użytkownika. Ta właściwość jest widoczna tylko dla użytkowników z uprawnieniami do odczytu dzienników inspekcji (Reporting_ApplicationAuditLogs_Read)

![nowe właściwości użytkownika wyświetlane na stronach wszyscy użytkownicy i usunięci użytkownicy](./media/users-search-enhanced/user-properties.png)

### <a name="deleted-users-page"></a>Strona usuniętych użytkowników

Strona **usunięci użytkownicy** zawiera wszystkie kolumny, które są dostępne na stronie **Wszyscy użytkownicy** , oraz kilka dodatkowych kolumn:

- Data usunięcia: Data pierwszego usunięcia użytkownika z organizacji (użytkownik jest dostępnych).
- Data trwałego usunięcia: Data, kiedy proces trwałego usuwania użytkownika z organizacji rozpoczyna się automatycznie. 

> [!NOTE]
> Daty usunięcia są wyświetlane w uniwersalnym czasie koordynowanym (UTC).

Niektóre kolumny są domyślnie wyświetlane. Aby dodać inne kolumny, wybierz **kolumny** na stronie, wybierz nazwy kolumn, które chcesz dodać, a następnie wybierz **przycisk OK** , aby zapisać swoje preferencje.

### <a name="identity-issuers"></a>Wystawcy tożsamości

Wybierz wpis w kolumnie **wystawca tożsamości** dla dowolnego użytkownika, aby wyświetlić dodatkowe szczegóły dotyczące wystawcy, w tym typ logowania i Identyfikator przypisany do wystawcy. Wpisy w kolumnie **wystawca tożsamości** mogą być wielowartościowe. Jeśli istnieje wielu wystawców tożsamości użytkownika, zobaczysz wielokrotność wyrazu w kolumnie **wystawca tożsamości** na stronach **Wszyscy użytkownicy** i **usunięci użytkownicy** , a w okienku szczegółów zostanie wyświetlona lista wszystkich wystawców.

> [!NOTE]
> Kolumna **źródłowa** jest zastępowana wieloma kolumnami, takimi jak **Typ utworzenia**, **zsynchronizowany katalog** i **wystawca tożsamości** na potrzeby bardziej szczegółowego filtrowania.

## <a name="user-list-search"></a>Wyszukiwanie listy użytkowników

Gdy wprowadzisz ciąg wyszukiwania, funkcja wyszukiwania używa teraz "rozpoczyna się od" i wyszukiwania podciągu w celu dopasowania nazw, wiadomości e-mail lub identyfikatorów obiektów w ramach jednego wyszukiwania. Możesz wprowadzić dowolny z tych atrybutów do pola wyszukiwania, a wyszukiwanie automatycznie przeszukuje wszystkie te właściwości, aby zwracały wszelkie pasujące wyniki. Wyszukiwanie podciągów jest wykonywane tylko dla całych wyrazów. To samo wyszukiwanie można wykonać na stronach **Wszyscy użytkownicy** i **usunięci użytkownicy** .

## <a name="user-list-filtering"></a>Filtrowanie listy użytkowników

Możliwości filtrowania zostały ulepszone, aby zapewnić więcej opcji filtrowania dla stron **Wszyscy użytkownicy** i **usunięci użytkownicy** . Teraz można filtrować według wielu właściwości jednocześnie i można filtrować według większej liczby właściwości.

### <a name="filtering-all-users-list"></a>Filtrowanie listy wszystkich użytkowników

Poniżej znajdują się właściwości z możliwością filtrowania na stronie **Wszyscy użytkownicy** :

- Typ użytkownika: członek, gość, brak
- Stan synchronizacji katalogu: tak, nie
- Typ tworzenia: zaproszenie, zweryfikowane wiadomości E-mail, konto lokalne
- Stan zaproszenia — oczekiwanie na akceptację, zaakceptowane
- Nazwa domeny: Wprowadź nazwę domeny
- Nazwa firmy: Wprowadź nazwę firmy
- Jednostka administracyjna: Wybierz tę opcję, aby ograniczyć zakres wyświetlania użytkowników do pojedynczej jednostki administracyjnej. Aby uzyskać więcej informacji, zobacz [Zarządzanie jednostkami administracyjnymi w wersji zapoznawczej](../roles/administrative-units.md).

### <a name="filtering-deleted-users-list"></a>Filtrowanie listy usuniętych użytkowników

Strona **usunięci użytkownicy** ma dodatkowe filtry, które nie znajdują się na stronie **Wszyscy użytkownicy** . Poniżej znajdują się właściwości z możliwością filtrowania na stronie **usunięci użytkownicy** :

- Typ użytkownika: członek, gość, brak
- Stan synchronizacji katalogu: tak, nie
- Typ tworzenia: zaproszenie, zweryfikowane wiadomości E-mail, konto lokalne
- Stan zaproszenia: oczekiwanie na akceptację, zaakceptowane
- Data usunięcia: ostatnie 7, 14 lub 30 dni
- Nazwa domeny: Wprowadź nazwę domeny
- Nazwa firmy: Wprowadź nazwę firmy
- Data trwałego usunięcia: ostatnie 7, 14 lub 30 dni

## <a name="user-list-sorting"></a>Sortowanie listy użytkowników

Teraz można sortować według nazwy i nazwy głównej użytkownika na stronach **Wszyscy użytkownicy** i **usunięci użytkownicy** . Możesz również sortować według daty usunięcia na liście **usuniętych użytkowników** .

## <a name="user-list-counts"></a>Liczba list użytkowników

Możesz wyświetlić łączną liczbę użytkowników na stronach **Wszyscy użytkownicy** i **usunięci użytkownicy** . Podczas wyszukiwania lub filtrowania list liczba jest aktualizowana w celu odzwierciedlenia łącznej liczby znalezionych użytkowników.

![Ilustracja przedstawiająca liczbę list użytkowników na stronie wszyscy użytkownicy](./media/users-search-enhanced/user-list-sorting.png)

## <a name="frequently-asked-questions-faq"></a>Często zadawane pytania

Pytanie | Odpowiedź
-------- | ------
Dlaczego usunięty użytkownik jest nadal wyświetlany po upływie daty trwałego usunięcia? | Data trwałego usunięcia jest wyświetlana w strefie czasowej UTC, więc może być niezgodna z bieżącą strefą czasową. Ponadto ta data to Najwcześniejsza data, po upływie którego użytkownik zostanie trwale usunięty z organizacji, więc może nadal być przetwarzany. Trwale usunięci użytkownicy zostaną automatycznie usunięci z listy.
Co dzieje się z możliwościami zbiorczymi dla użytkowników i Gości? | Operacje zbiorcze są nadal dostępne dla użytkowników i Gości, w tym zbiorcze tworzenie, zapraszanie zbiorcze, zbiorcze usuwanie i pobieranie użytkowników. Właśnie zostały one scalone w menu o nazwie **operacje zbiorcze**. Możesz znaleźć opcje **operacji zbiorczych** w górnej części strony **Wszyscy użytkownicy** .
Co się stało z kolumną źródłową? | Kolumna **źródłowa** została zastąpiona innymi kolumnami, które zawierają podobne informacje, a jednocześnie umożliwiają filtrowanie tych wartości niezależnie. Przykłady obejmują **Typ tworzenia**, **zsynchronizowany katalog** i **wystawcę tożsamości**.
Co się stało z kolumną Nazwa użytkownika? | Kolumna **Nazwa użytkownika** nadal istnieje, ale została zmieniona na **nazwę główną użytkownika**. Lepiej odzwierciedla informacje zawarte w tej kolumnie. Zauważ również, że pełna główna nazwa użytkownika jest teraz wyświetlana dla Gości B2B. Jest to zgodne z tym, co uzyskasz w programie MS Graph.  

## <a name="next-steps"></a>Następne kroki

Operacje użytkownika

- [Dodawanie lub zmiana informacji o profilu](../fundamentals/active-directory-users-profile-azure-portal.md)
- [Dodawanie lub usuwanie użytkowników](../fundamentals/add-users-azure-active-directory.md)

Operacje zbiorcze

- [Pobierz listę użytkowników](users-bulk-download.md)
- [Zbiorcze dodawanie użytkowników](users-bulk-add.md)
- [Zbiorcze usuwanie użytkowników](users-bulk-delete.md)
- [Zbiorcze przywracanie użytkowników](users-bulk-restore.md)
