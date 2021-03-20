---
title: Pulpit nawigacyjny — Language Understanding — LUIS
titleSuffix: Azure Cognitive Services
description: Popraw intencje i jednostki za pomocą pulpitu nawigacyjnego swojej aplikacji. Na pulpicie nawigacyjnym są wyświetlane ogólne informacje o aplikacji z wyróżnionymi intencjami, które powinny zostać naprawione.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 11/08/2019
ms.openlocfilehash: 4867a065a85fab1e4abc7f19401239e5b76e1da4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91541411"
---
# <a name="how-to-use-the-dashboard-to-improve-your-app"></a>Jak ulepszyć aplikację przy użyciu pulpitu nawigacyjnego

Znajdowanie i rozwiązywanie problemów z intencjami przeszkolonych aplikacji w przypadku korzystania z przykładowej wyrażenia długości. Na pulpicie nawigacyjnym są wyświetlane ogólne informacje o aplikacji z wyróżnionymi intencjami, które powinny zostać naprawione.

Sprawdzanie, czy analiza pulpitu nawigacyjnego jest procesem iteracyjnym, powtarzaj się, gdy zmienisz i poprawisz model.

Ta strona nie będzie miała odpowiedniej analizy dla aplikacji, które nie mają żadnego przykładu wyrażenia długości, nazywanego _tylko wzorcem_ .

## <a name="what-issues-can-be-fixed-from-dashboard"></a>Jakie problemy można naprawić z poziomu pulpitu nawigacyjnego?

Trzy problemy rozkierowane na pulpicie nawigacyjnym są następujące:

|Problem|Kolor wykresu|Wyjaśnienie|
|--|--|--|
|Nierównoważność danych|-|Dzieje się tak, gdy liczba przykładowych wyrażenia długości znacznie się różni. Wszystkie intencje muszą mieć w _przybliżeniu_ tę samą liczbę przykładowych wyrażenia długości — z wyjątkiem zamiaru none. Powinna ona mieć 10%-15% łącznej liczby wyrażenia długości w aplikacji.<br><br> Jeśli dane są niezrównoważone, ale dokładność przeznaczenie jest wyższa od pewnej wartości progowej, to nierównowaga nie jest raportowana jako problem.<br><br>**Rozpocznij od tego problemu — może to być główna przyczyna innych problemów.**|
|Niejasne przewidywania|Orange|Dzieje się tak, gdy najlepsze zamierzenie i wyniki następnego celu są wystarczająco zbliżone, że mogą przerzucać kolejne szkolenia, z powodu [próbkowania negatywnego](luis-how-to-train.md#train-with-all-data) lub więcej przykładowych wyrażenia długości dodanych do intencji. |
|Nieprawidłowe przewidywania|Red (Czerwony)|Dzieje się tak, gdy przykład wypowiedź nie jest przewidziany dla zamiaru oznaczonego etykietą (zamiar, w której znajduje się).|

Poprawne przewidywania są reprezentowane kolorem niebieskim.

Na pulpicie nawigacyjnym znajdują się te problemy i zawarto informacje na temat tego, co należy zrobić, i co należy zrobić, aby ulepszyć aplikację.

## <a name="before-app-is-trained"></a>Przed przeszkoleniem aplikacji

Przed przeprowadzeniem uczenia aplikacji Pulpit nawigacyjny nie zawiera żadnych sugestii dotyczących poprawek. Nauczenie aplikacji, aby zobaczyć te sugestie.

## <a name="check-your-publishing-status"></a>Sprawdź stan publikowania

Karta **stan publikowania** zawiera informacje o ostatniej publikacji aktywnej wersji.

Sprawdź, czy wersja aktywna jest wersja, którą chcesz naprawić.

![Pulpit nawigacyjny pokazuje usługi zewnętrzne, opublikowane regiony i zagregowane trafienia punktów końcowych aplikacji.](./media/luis-how-to-use-dashboard/analytics-card-1-shows-app-summary-and-endpoint-hits.png)

Przedstawiono w nim także wszystkie usługi zewnętrzne, opublikowane regiony i zagregowane trafienia punktów końcowych.

## <a name="review-training-evaluation"></a>Przegląd oceny szkolenia

Karta **oceny szkolenia** zawiera zagregowane podsumowanie ogólnej dokładności aplikacji według obszaru. Wynik wskazuje jakość konwersji.

![Karta oceny szkolenia zawiera pierwszy obszar informacji o ogólnej dokładności Twojej aplikacji.](./media/luis-how-to-use-dashboard/analytics-card-2-shows-app-overall-accuracy.png)

Wykres wskazuje prawidłowo przewidywane intencje i obszary problemów z różnymi kolorami. Gdy poprawisz aplikację za pomocą sugestii, ten wynik rośnie.

Sugerowane poprawki są rozdzielane przez typ problemu i są najbardziej znaczące dla aplikacji. Jeśli wolisz przeglądać i rozwiązywać problemy dla zamiaru, Użyj karty **[intencje z błędami](#intents-with-errors)** w dolnej części strony.

Każdy obszar problemu ma intencje, które należy naprawić. Po wybraniu nazwy zamierzenia zostanie otwarta strona **cel** z filtrem zastosowanym do wyrażenia długości. Ten filtr umożliwia skoncentrowanie się na wyrażenia długości, który powoduje problem.

### <a name="compare-changes-across-versions"></a>Porównaj zmiany w różnych wersjach

Utwórz nową wersję przed wprowadzeniem zmian w aplikacji. W nowej wersji wprowadź sugerowane zmiany w przykładowej wyrażenia długości zamiaru, a następnie ponownie nauczenie. Na karcie **oceny szkoleń** na stronie pulpitu nawigacyjnego Użyj okna **Pokaż zmianę z przeszkolonej wersji** , aby porównać zmiany.

![Porównaj zmiany w różnych wersjach](./media/luis-how-to-use-dashboard/compare-improvement-across-versions.png)

### <a name="fix-version-by-adding-or-editing-example-utterances-and-retraining"></a>Popraw wersję poprzez dodanie lub edycję przykładu wyrażenia długości i przeszkolenie

Podstawową metodą naprawienia aplikacji będzie Dodawanie lub edytowanie przykładu wyrażenia długości i ponowne uczenie. Nowe lub zmienione wyrażenia długości muszą przestrzegać wytycznych dotyczących [różnych wyrażenia długości](luis-concept-utterance.md).

Dodawanie przykładowej wyrażenia długości powinno odbywać się przez kogoś, kto:

* ma wysoki stopień wiedzy na temat tego, co wyrażenia długości się w różnych intencjach.
* wie, jak wyrażenia długości w jednym zamiarze można mylić z innym zamiarem.
* jest w stanie zdecydować, czy dwa intencje, które są często mylone ze sobą, powinny być zwinięte do jednego celu. W takim przypadku należy ściągnąć różne dane przy użyciu jednostek.

### <a name="patterns-and-phrase-lists"></a>Wzorce i listy fraz

Strona analiza nie wskazuje, kiedy używać [wzorców](luis-concept-patterns.md) lub [list fraz](luis-concept-feature.md). Jeśli je dodasz, może to pomóc w niepoprawnym lub niejasnym prognozie, ale nie będzie można obsłużyć nierównowagi danych.

### <a name="review-data-imbalance"></a>Sprawdzanie nierównowagi danych

Rozpocznij od tego problemu — może to być główna przyczyna innych problemów.

Lista założeń dotyczących **nierównoważenia danych** przedstawia intencje, które wymagają więcej wyrażenia długościów w celu poprawienia nierównowagi danych.

**Aby rozwiązać ten problem**:

* Dodaj więcej wyrażenia długości do intencji, a następnie ponownie nauczenie.

Nie należy dodawać wyrażenia długości do zamiaru brak, chyba że jest to sugerowane na pulpicie nawigacyjnym.

> [!Tip]
> Skorzystaj z trzeciej sekcji na stronie, **wyrażenia długości z zamiarem** z ustawieniem **wyrażenia długości (Number)** jako krótkiego przewodnika, którego intencje potrzebują więcej wyrażenia długości.
    ![Użyj opcji "wyrażenia długości (Number)", aby znaleźć intencje z niezrównoważoną ilością danych.](./media/luis-how-to-use-dashboard/predictions-per-intent-number-of-utterances.png)

### <a name="review-incorrect-predictions"></a>Przejrzyj nieprawidłowe przewidywania

Nieprawidłowa lista założeń **prognozowania** zawiera intencje, które mają wyrażenia długości, które są używane jako przykłady dla konkretnego celu, ale są przewidywane dla różnych intencji.

**Aby rozwiązać ten problem**:

* Edytuj wyrażenia długości, aby był bardziej szczegółowy dla zamiaru i ponownie nauczenie.
* Połącz intencje, jeśli wyrażenia długości są zbyt ściśle wyrównane i ponownie nauczenie.

### <a name="review-unclear-predictions"></a>Przejrzyj niejasne przewidywania

Lista założeń **przewidywania niejasnego** przedstawia intencje w wyrażenia długości z wynikami przewidywania, które nie są daleko w stosunku do najbliższej Rival, że najwyższe zamiaru dla wypowiedź mogą ulec zmianie w następnym szkoleniu z powodu [próbkowania negatywnego](luis-how-to-train.md#train-with-all-data).

**Aby rozwiązać ten problem**;

* Edytuj wyrażenia długości, aby był bardziej szczegółowy dla zamiaru i ponownie nauczenie.
* Połącz intencje, jeśli wyrażenia długości są zbyt ściśle wyrównane i ponownie nauczenie.

## <a name="utterances-per-intent"></a>Wyrażenia długości na intencję

Ta karta przedstawia ogólną kondycję aplikacji w ramach intencji. W miarę naprawiania zamiarów i pouczenia się Kontynuuj błyskawiczne Rozwiązywanie problemów na tej karcie.

Na poniższym wykresie przedstawiono dobrze zrównoważoną aplikację, która nie rozwiązuje problemów.

![Na poniższym wykresie przedstawiono dobrze zrównoważoną aplikację, która nie rozwiązuje problemów.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-balance.png)

Na poniższym wykresie przedstawiono niewłaściwie zrównoważoną aplikację z wieloma problemami, które należy naprawić.

![Zrzut ekranu przedstawia prognozy na cel z kilkoma niejasnymi lub nieprawidłowo przewidywanymi wynikami.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-imbalance.png)

Umieść kursor nad każdym paskiem intencji, aby uzyskać informacje na temat zamiaru.

![Zrzut ekranu przedstawia prognozy na intencje ze szczegółami niejasnych lub nieprawidłowo przewidywanych wyników.](./media/luis-how-to-use-dashboard/utterances-per-intent-with-details-of-errors.png)

Użyj funkcji **Sortuj według** , aby rozmieścić intencje według typu problemu, aby skoncentrować się na najbardziej problematycznych intencjach tego problemu.

## <a name="intents-with-errors"></a>Intencje z błędami

Ta karta umożliwia przeglądanie problemów związanych z konkretnym zamiarem. Domyślnym widokiem tej karty jest najbardziej problematyczne intencje, dzięki czemu wiadomo, gdzie należy skoncentrować się na pracy.

![Zamiary z kartą błędy umożliwiają przeglądanie problemów dotyczących określonego celu. Karta jest domyślnie filtrowana pod kątem najbardziej problematycznych intencji, aby wiedzieć, gdzie należy skoncentrować się na działaniach.](./media/luis-how-to-use-dashboard/most-problematic-intents-with-errors.png)

Górny wykres pierścieniowy przedstawia problemy z intencją dla trzech typów problemu. W przypadku wystąpienia problemów z trzema typami problemów każdy typ ma swój własny wykres, a także wszelkie Rival.

### <a name="filter-intents-by-issue-and-percentage"></a>Filtruj intencje według problemu i wartości procentowej

Ta sekcja karty umożliwia znalezienie przykładowej wyrażenia długości, która wykracza poza próg błędu. Najlepiej, aby poprawne przewidywania były znaczące. Ta wartość procentowa jest zależna od biznesu i klienta.

Określ procent wartości progowych, z którymi masz doświadczenie dla Twojej firmy.

Filtr umożliwia znalezienie intencji z określonym problemem:

|Filtr|Sugerowana wartość procentowa|Przeznaczenie|
|--|--|--|
|Najbardziej problematyczne intencje|-|**Zacznij tutaj** — naprawianie wyrażenia długości w tym celu poprawi aplikację więcej niż inne poprawki.|
|Poprawne przewidywania poniżej|60%|Jest to wartość procentowa wyrażenia długości w wybranym zamierzeniu, która jest poprawna, ale ma wynik pewności poniżej wartości progowej. |
|Niejasne przewidywania powyżej|15%|Jest to wartość procentowa wyrażenia długości w wybranym zamierzeniu, która jest pomylona z najbliższym zamiarem Rival.|
|Nieprawidłowe przewidywania powyżej|15%|Jest to wartość procentowa wyrażenia długości w wybranym zamierzeniu, która jest nieprawidłowo przewidywalna. |

### <a name="correct-prediction-threshold"></a>Poprawna wartość progowa przewidywania

Co to jest wiarygodny wynik do przewidywania? Na początku opracowywania aplikacji 60% może być obiektem docelowym. Użyj **poprawnych prognoz poniżej** z wartością procentową 60%, aby znaleźć dowolne wyrażenia długości w wybranym zamierzeniu, które należy naprawić.

### <a name="unclear-or-incorrect-prediction-threshold"></a>Niejasny lub nieprawidłowy próg przewidywania

Te dwa filtry umożliwiają znalezienie wyrażenia długości w wybranym zasobie poza progiem. Te dwie wartości procentowe można traktować jako procent błędów. Jeśli masz doświadczenie ze stawką błędów 10-15% dla prognoz, ustaw próg filtru na 15%, aby znaleźć wszystkie wyrażenia długości powyżej tej wartości.

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie zasobami platformy Azure](luis-how-to-azure-subscription.md)
