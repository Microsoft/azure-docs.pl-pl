---
title: Konfigurowanie słownika biznesowego w Azure Data Catalog
description: Artykuł jak wyróżniać słownik biznesowy w Azure Data Catalog do definiowania i używania wspólnego słownika biznesowego do oznaczania zarejestrowanych zasobów danych.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: f91728435b885f3b4d9415bc81e18fdaaea0148f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021949"
---
# <a name="set-up-the-business-glossary-for-governed-tagging"></a>Konfigurowanie słownika biznesowego dla tagowania regulowanego

## <a name="introduction"></a>Wprowadzenie

Azure Data Catalog umożliwia odnajdywanie źródeł danych, dzięki czemu można łatwo odnajdywać i zrozumieć źródła danych potrzebne do przeprowadzenia analizy i podejmowania decyzji. Te funkcje zapewniają największy wpływ, gdy można znaleźć i zrozumieć największy zakres dostępnych źródeł danych.

Jedną Data Catalog funkcji, która promuje lepsze zrozumienie danych elementów zawartości, to znakowanie. Za pomocą tagowania można kojarzyć słowa kluczowe z elementem zawartości lub kolumną, co z kolei ułatwia odnajdywanie zasobów za pomocą wyszukiwania lub przeglądania. Tagowanie ułatwia również zrozumienie kontekstu i intencji elementu zawartości.

Jednak znakowanie może czasami spowodować problemy. Niektóre przykłady problemów, które mogą wprowadzać znakowanie, to:

* Użycie skrótów dla niektórych elementów zawartości i tekstu rozwiniętego w innych. Ta niespójność utrudnia odnajdywanie zasobów, nawet jeśli zamiarem było znaczniki zasobów z tym samym tagiem.
* Potencjalne wahania w znaczeniu, w zależności od kontekstu. Na przykład tag o nazwie *przychód* w zestawie danych klienta może oznaczać przychód według klienta, ale ten sam tag w zestawie kwartalnej sprzedaży może oznaczać kwartalne przychody dla firmy.  

Aby pomóc rozwiązać te i inne podobne wyzwania, Data Catalog obejmuje słownik biznesowy.

Korzystając z Data Catalog słownik biznesowy, organizacja może udokumentować kluczowe warunki biznesowe i ich definicje, aby utworzyć wspólny słownik biznesowy. To zarządzanie umożliwia spójność użycia danych w całej organizacji. Po zdefiniowaniu terminu w słowniku biznesowym można go przypisać do zasobów danych w wykazie. Takie podejście, które *reguluje znakowanie*, jest takie samo jak znakowanie.

## <a name="glossary-availability-and-privileges"></a>Dostępność i uprawnienia słownika

Słownik biznesowy jest dostępny tylko w wersji Standard programu Azure Data Catalog. Bezpłatna wersja Data Catalog nie zawiera słownika i nie zapewnia możliwości sterowania znakiem.

Możesz uzyskać dostęp do słownika biznesowego za pośrednictwem opcji **słownik** w menu nawigacji portalu Data Catalog.  

![Data Catalog — dostęp do słownika biznesowego](./media/data-catalog-how-to-business-glossary/01-portal-menu.png)

Data Catalog Administratorzy i członkowie roli Administratorzy słownika mogą tworzyć, edytować i usuwać warunki słownika w słowniku biznesowym. Wszyscy użytkownicy Data Catalog mogą przeglądać definicje terminów i Tagi zawartości za pomocą terminów słownika.

![Data Catalog — Dodaj nowy termin słownika](./media/data-catalog-how-to-business-glossary/02-new-term.png)

## <a name="creating-glossary-terms"></a>Tworzenie terminów słownika

Data Catalog Administratorzy i Administratorzy słownika mogą tworzyć terminy słownika, klikając przycisk **nowy termin** . Każdy termin słownika zawiera następujące pola:

* Definicja firmy dla terminu
* Opis, który przechwytuje zamierzone użycie lub reguły biznesowe dla elementu zawartości lub kolumny
* Lista udziałowców, którzy wiedzą, kto wie, że jest to termin
* Termin nadrzędny, który definiuje hierarchię, w której jest zorganizowany termin

## <a name="glossary-term-hierarchies"></a>Hierarchie terminów słownika

Korzystając z Data Catalog słownik biznesowy, organizacja może opisać swój słownik biznesowy jako hierarchię warunków i może utworzyć klasyfikację warunków, które lepiej przedstawiają swoją taksonomię biznesową.

Termin musi być unikatowy na danym poziomie hierarchii. Zduplikowane nazwy nie są dozwolone. Nie ma żadnego limitu liczby poziomów w hierarchii, ale hierarchia jest często łatwiej zrozumiała, gdy istnieją trzy poziomy lub mniej.

Korzystanie z hierarchii w słowniku biznesowym jest opcjonalne. Pozostawienie pustego pola termin dla słownika spowoduje utworzenie płaskiej (niehierarchicznej) listy warunków w słowniku.  

## <a name="tagging-assets-with-glossary-terms"></a>Tagowanie elementów zawartości za pomocą terminów słownika

Po zdefiniowaniu warunków słownika w wykazie środowisko do tagowania elementów zawartości jest zoptymalizowane pod kątem przeszukiwania słownika jako znaku przez użytkownika. W portalu Data Catalog zostanie wyświetlona lista zgodnych terminów słownika do wyboru. Jeśli użytkownik wybierze termin słownika z listy, termin jest dodawany do elementu zawartości jako tag (nazywany także tagiem słownika). Użytkownik może również utworzyć nowy tag, wpisując termin, który nie znajduje się w słowniku (nazywanym także tagiem użytkownika).

![Zasób danych otagowany za pomocą jednego tagu użytkownika i dwóch tagów słownika](./media/data-catalog-how-to-business-glossary/03-tagged-asset.png)

> [!NOTE]
> Tagi użytkownika są jedynym typem tagu obsługiwanym w bezpłatnej wersji Data Catalog.

### <a name="hover-behavior-on-tags"></a>Zachowanie podczas aktywowania tagów

W portalu Data Catalog dwa typy tagów są wizualnie różne i stanowią inne zachowania dotyczące aktywowania. Po umieszczeniu wskaźnika myszy nad tagiem użytkownika zobaczysz tekst znacznika oraz użytkownika lub użytkowników, którzy dodali tag. Po umieszczeniu wskaźnika myszy na znaczniku słownika zobaczysz również definicję terminu słownika oraz link umożliwiający otwarcie słownika biznesowego, aby wyświetlić jego pełną definicję.

### <a name="search-filters-for-tags"></a>Filtry wyszukiwania dla tagów

Tagi słownikowe i tagi użytkownika są zarówno do przeszukiwania, jak i w wyszukiwaniu.

## <a name="summary"></a>Podsumowanie

Korzystając z słownika biznesowego w Azure Data Catalog i regulowanego tagowania, można w spójny sposób identyfikować i odnajdywać zasoby danych oraz zarządzać nimi. Słownik biznesowy może wspierać naukę słownika biznesowego przez członków organizacji. Słownik obsługuje również przechwytywanie znaczących metadanych, co upraszcza odnajdywanie i zrozumienie zasobów.

## <a name="next-steps"></a>Następne kroki

* [Dokumentacja interfejsu API REST dla operacji słownika biznesowego](/rest/api/datacatalog/data-catalog-glossary)
