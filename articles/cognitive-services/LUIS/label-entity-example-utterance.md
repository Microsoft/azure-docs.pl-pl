---
title: Przykład wypowiedź jednostki etykiety
description: Dowiedz się, jak dodać etykietę do jednostki uczenia maszynowego za pomocą podjednostek w przykładowej wypowiedź na stronie szczegółów obiektu w portalu LUIS.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: ffbaa2e40d5924ba61e548398e63295cf7dba2b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91303730"
---
# <a name="label-machine-learning-entity-in-an-example-utterance"></a>Etykieta jednostki uczenia maszynowego w przykładzie wypowiedź

Etykietowanie jednostki w przykładzie wypowiedź daje LUIS przykład obiektu i miejsce, w którym jednostka może być wyświetlana w wypowiedź.

Można oznaczyć jednostki i podjednostki, które są poznanie maszynowe.

Ponieważ nie można oznaczyć wyrażenia regularnego, listy lub wstępnie skompilowanych jednostek, utworzyć jednostkę lub podjednostkę, a następnie dodać te jednostki jako funkcje, jeśli ma to zastosowanie, do jednostki lub podjednostki.

## <a name="label-example-utterances-from-the-intent-detail-page"></a>Etykieta przykład wyrażenia długości na stronie szczegółów zamiaru

Aby oznaczyć przykłady jednostek w wypowiedź, wybierz intencję wypowiedź.

1. Zaloguj się do [portalu Luis](https://www.luis.ai)i wybierz swoją **subskrypcję** i **zasób tworzenia** , aby wyświetlić aplikacje przypisane do tego zasobu.
1. Otwórz aplikację, wybierając jej nazwę na stronie **Moje aplikacje** .
1. Wybierz zamiar, która ma przykład wyrażenia długości, którą chcesz oznaczyć do wyodrębnienia z jednostką.
1. Zaznacz tekst, który chcesz oznaczyć etykietą, a następnie wybierz jednostkę.

## <a name="two-techniques-to-label-entities"></a>Dwie techniki do etykietowania jednostek

Na stronie szczegółów intencji są obsługiwane dwie techniki etykietowania.
* Wybierz pozycję jednostka lub podjednostka z [palety Entity](#label-with-the-entity-palette-visible) , a następnie wybierz wewnątrz przykładowego tekstu wypowiedź. Jest to zalecana technika, ponieważ możesz wizualnie sprawdzić, czy pracujesz z poprawną jednostką lub podobiektem, zgodnie ze schematem.
* Najpierw wybierz w przykładzie wypowiedź tekst. Po wykonaniu tej czynności zostanie wyświetlone menu rozwijane z [opcjami etykietowania](#how-to-label-entity-from-in-place-menu) .

## <a name="label-with-the-entity-palette-visible"></a>Etykieta z widoczną paletą jednostek

Po [zaplanowaniu schematu z jednostkami](luis-how-plan-your-app.md)pozostaw **paletę jednostki** widoczną podczas etykietowania. **Paleta jednostek** to przypomnienie dotyczące obiektów zaplanowanych do wyodrębnienia.

Aby uzyskać dostęp do **palety Entity**, zaznacz **@** symbol na pasku narzędzi kontekstowych powyżej przykładowej listy wypowiedź.

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający paletę jednostek na stronie Szczegóły intencji.](media/label-utterances/entity-palette-from-tool-bar.png)

## <a name="how-to-label-entity-from-entity-palette"></a>Jak dodać etykietę do jednostki z palety jednostek

Paleta jednostek oferuje alternatywę dla poprzedniego środowiska etykietowania. Umożliwia malowanie tekstu, aby natychmiast oznaczyć go jednostką.

1. Otwórz paletę jednostki, wybierając **@** symbol w prawym górnym rogu tabeli wypowiedź.

2. Wybierz jednostkę z palety, która ma być oznaczona etykietą. Ta akcja jest wskazywana wizualnie z nowym kursorem. Kursor jest podążany podczas przenoszenia w portalu LUIS.

3. W przykładzie wypowiedź, _maluj_ jednostki za pomocą kursora.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawia jednostkę pomalowaną kursorem.](media/label-utterances/example-1-label-machine-learned-entity-palette-label-action.png)

## <a name="adding-entity-as-a-feature-from-the-entity-palette"></a>Dodawanie jednostki jako funkcji z palety Entity

Dolna sekcja palety jednostek umożliwia dodanie funkcji do aktualnie wybranej jednostki. Możesz wybrać wszystkie istniejące listy jednostek i fraz lub utworzyć nową listę fraz.

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający paletę jednostek z jednostką jako funkcją](media/label-utterances/entity-palette-entity-as-a-feature.png)

## <a name="labeling-entity-roles"></a>Etykietowanie ról jednostek

Role jednostek są oznaczone etykietami przy użyciu **palety Entity**.

1. Na stronie Szczegóły zamiaru wybierz **paletę jednostki** z poziomu paska narzędzi kontekstu.
1. Po otwarciu palety jednostki wybierz jednostkę z listy jednostek.
1. Wybierz istniejącą rolę poniżej listy jednostek.
1. W przykładowym tekście wypowiedź etykieta tekst z rolą jednostki.

## <a name="how-to-label-entity-from-in-place-menu"></a>Jak oznaczyć obiekt etykietą z menu w miejscu

Etykietowanie miejscowe umożliwia szybkie Zaznaczanie tekstu w wypowiedź i etykietowanie go. Można również utworzyć jednostkę usługi Machine Learning lub jednostkę listy na podstawie tekstu z etykietą.

Rozważmy przykład wypowiedź, `hi, please I want a cheese pizza in 20 minutes` .

Wybierz tekst z lewej strony, a następnie wybierz prawym przyciskiem myszy tekst jednostki, a następnie z menu w miejscu wybierz jednostkę, z którą chcesz dodać etykietę.

> [!div class="mx-imgBorder"]
> ![Oznacz kompletną jednostkę uczenia maszynowego](media/label-utterances/label-steps-in-place-menu.png)

## <a name="review-labeled-text"></a>Przejrzyj tekst z etykietą

Po etykietowaniu Sprawdź przykład wypowiedź i upewnij się, że wybrany zakres tekstu został podkreślony z wybraną jednostką. Linia ciągła wskazuje, że tekst został oznaczony etykietą.

> [!div class="mx-imgBorder"]
> ![Etykieta kompletnej jednostki uczenia maszynowego](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="confirm-predicted-entity"></a>Potwierdź jednostkę predykcyjną

Jeśli pole ma Kropkowane obramowanie wokół zakresu tekstu, oznacza to, że tekst jest przewidywany, ale _nie jest jeszcze oznaczony etykietą_. Aby włączyć prognozowanie do etykiety, zaznacz wiersz wypowiedź, a następnie wybierz pozycję **Potwierdź jednostki** na podstawie kontekstowego paska narzędzi.

## <a name="relabeling-over-existing-entities"></a>Etykietowanie istniejących jednostek

W przypadku przetworzenia etykiety tekstu, który jest już oznaczony etykietą, LUIS może podzielić lub scalić istniejące etykiety.

## <a name="labeling-for-punctuation"></a>Etykietowanie interpunkcji

Nie ma potrzeby etykietowania interpunkcji. Użyj [ustawień aplikacji](luis-reference-application-settings.md) , aby kontrolować sposób, w jaki interpunkcja wpływa na przewidywania wypowiedź.

## <a name="unlabel-entities"></a>Nie etykieta jednostek

> [!NOTE]
> Tylko jednostki, na które nauczyne maszynowe, mogą być bez etykiet. Nie można etykietować ani deetykietować jednostek wyrażeń regularnych, jednostek listy ani wstępnie skompilowanych jednostek.

Aby usunąć etykietę jednostki, wybierz jednostkę i wybierz pozycję Usuń **etykietę** z menu w miejscu.

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający jednostkę bez etykietowania](media/label-utterances/unlabel-entity-using-in-place-menu.png)

## <a name="automatic-labeling-for-parent-and-child-entities"></a>Automatyczne etykietowanie dla jednostek nadrzędnych i podrzędnych

W przypadku etykietowania jednostki nadrzędnej każda podjednostka, która może być przewidywalna na podstawie aktualnie przeszkolonej wersji, będzie oznaczona jako etykieta.

Jeśli jest oznaczona etykietami dla podjednostki, obiekt nadrzędny zostanie oznaczony automatycznie.

## <a name="automatic-labeling-for-non-machine-learned-entities"></a>Automatyczne etykietowanie jednostek nieprzeznaczonych dla maszyn

Jednostki, które nie są poznanie maszynowe, obejmują wstępnie utworzone jednostki, jednostki wyrażeń regularnych, jednostki listy i wzorzec. wszystkie jednostki. Są one automatycznie oznaczone jako LUIS, więc użytkownicy nie muszą mieć do nich ręcznego etykiet.

## <a name="intent-prediction-errors"></a>Błędy przewidywania intencji

Błąd prognozowania intencji wskazuje, że przykład wypowiedź, że dana przeszkolonej aplikacji nie zostanie przewidywalna dla zamiaru.

Dowiedz się, jak [wyświetlić te błędy](luis-how-to-add-intents.md#intent-prediction-errors) na stronie szczegółów dotyczących intencji.

## <a name="entity-prediction-errors"></a>Błędy przewidywania jednostek

Błędy przewidywania jednostek wskazują, że przewidywana jednostka nie jest zgodna z oznaczona jednostką. Jest to Wizualizacja ze wskaźnikiem ostrożności obok wypowiedź.

> [!div class="mx-imgBorder"]
> ![Paleta jednostek dla jednostki uczenia maszynowego](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>Następne kroki

Użyj [pulpitu nawigacyjnego](luis-how-to-use-dashboard.md) i [Przejrzyj wyrażenia długości punktu końcowego](luis-how-to-review-endpoint-utterances.md) , aby zwiększyć jakość przewidywania aplikacji.
