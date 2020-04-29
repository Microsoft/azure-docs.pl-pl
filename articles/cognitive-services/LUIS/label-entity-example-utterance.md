---
title: Przykład wypowiedź jednostki etykiety
description: Dowiedz się, jak dodać etykietę do jednostki uczenia maszynowego za pomocą podskładników w przykładowej wypowiedź na stronie szczegółów obiektu w portalu LUIS.
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: ea5fbea902c9694d9a8a6a8a5bffcf5e7234bbbd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81382399"
---
# <a name="label-machine-learned-entity-in-an-example-utterance"></a>Etykieta jednostki doznanej przez maszynę w przykładzie wypowiedź

Etykietowanie jednostki w przykładzie wypowiedź daje LUIS przykład obiektu i miejsce, w którym jednostka może być wyświetlana w wypowiedź.

## <a name="labeling-machine-learned-entity"></a>Etykieta jednostki uczenia maszynowego

Rozważ wyrażenie `hi, please I want a cheese pizza in 20 minutes`.

1. Zaznacz tekst z lewej strony, a następnie wybierz prawym przyciskiem myszy tekst jednostki, a następnie zaznacz jednostkę, do której chcesz dodać etykietę, w tym przypadku kompletnej kolejności. _Pełna kolejność_ jest oznaczona na poniższej ilustracji.

    > [!div class="mx-imgBorder"]
    > ![Oznacz kompletną jednostkę podaną przez maszynę](media/label-utterances/example-1-label-machine-learned-entity-complete-order.png)

1. Wybierz jednostkę z okna podręcznego. Jednostka etykiety pełna kolejność Pizza obejmuje wszystkie słowa (od lewej do prawej w języku angielskim), które są oznaczone etykietami.

## <a name="review-labeled-text"></a>Przejrzyj tekst z etykietą

Po etykietowaniu Sprawdź przykład wypowiedź i upewnij się, że wybrany zakres tekstu został podkreślony z wybraną jednostką. Linia ciągła wskazuje, że tekst został oznaczony etykietą.

> [!div class="mx-imgBorder"]
> ![Etykieta kompletnej jednostki uczenia maszynowego](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="confirm-predicted-entity"></a>Potwierdź jednostkę predykcyjną

Jeśli pole ma Kropkowane obramowanie wokół zakresu tekstu, a nazwa jednostki znajduje się powyżej wypowiedź, oznacza to, że tekst jest przewidywany, ale _nie jest jeszcze oznaczony etykietą_. Aby włączyć prognozowanie do etykiety, zaznacz wiersz wypowiedź, a następnie wybierz pozycję **Potwierdź przewidywania jednostek**.

> [!div class="mx-imgBorder"]
> ![Przewidywanie kompletnej jednostki uczenia maszynowego](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted.png)

Alternatywnie można wybrać nazwę jednostki powyżej tekstu, a następnie wybrać pozycję **Potwierdź prognozę** z wyświetlonego menu.

> [!div class="mx-imgBorder"]
> ![Przewidywanie kompletnej jednostki zdobytej na maszynie z menu](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted-menu.png)

## <a name="label-entity-by-painting-with-entity-palette-cursor"></a>Etykieta jednostki przez malowanie przy użyciu kursora palety jednostek

Paleta jednostek oferuje alternatywę dla poprzedniego środowiska etykietowania. Umożliwia malowanie tekstu, aby natychmiast oznaczyć go jednostką.

1. Otwórz paletę Entity, wybierając ikonę wyróżnienia w prawym górnym rogu tabeli wypowiedź.

    > [!div class="mx-imgBorder"]
    > ![Paleta jednostek dla jednostki o Poznaniu maszynowym](media/label-utterances/example-1-label-machine-learned-entity-palette.png)

2. Wybierz składnik jednostki. Ta akcja jest wskazywana wizualnie z nowym kursorem. Kursor jest podążany podczas przenoszenia w portalu.

    > [!div class="mx-imgBorder"]
    > ![Paleta jednostek dla jednostki o Poznaniu maszynowym](media/label-utterances/example-1-label-machine-learned-entity-palette-menu.png)

3. W przykładzie wypowiedź, _maluj_ jednostki za pomocą kursora.

    > [!div class="mx-imgBorder"]
    > ![Paleta jednostek dla jednostki o Poznaniu maszynowym](media/label-utterances/example-1-label-machine-learned-entity-palette-label-action.png)

## <a name="labeling-subcomponents-of-a-machine-learned-entity"></a>Etykietowanie podskładników maszyny, którą pouczył się

Podskładniki w jednostkach są oznaczone etykietą dokładnie tak samo jak jednostki najwyższego poziomu. Gdy zaznaczasz tekst, jednostki dostępne w oknie podręcznym są względem kontekstu, w którym pojawia się tekst. Na przykład jeśli masz zainstalowaną maszynę 5-poziomą jednostkę i wybierasz tekst, który ma etykietę 1 i 2 (wskazywane przez nazwę jednostki oznaczonej etykietą w przykładzie wypowiedź), jednostki dostępne w oknie podręcznym są ograniczone do kontekstu składników poziomu trzeciego. Aby oznaczyć tekst innymi jednostkami, wybierz pozycję **etykieta jako inną opcję jednostki** .

> [!div class="mx-imgBorder"]
> ![Paleta jednostek dla jednostki o Poznaniu maszynowym](media/label-utterances/example-1-label-machine-learned-entity-subcomponent.png)

Podskładniki można etykietować tylko wtedy, gdy element nadrzędny jest również oznaczony etykietą.

## <a name="labeling-entity-roles"></a>Etykietowanie ról jednostek

Role jednostek są oznaczone etykietami przy użyciu palety Entity.

1. Na stronie Szczegóły zamiaru wybierz **paletę jednostki** z poziomu paska narzędzi kontekstu.
1. Po otwarciu palety jednostki wybierz jednostkę z listy jednostek.
1. Przejdź do **inspektora jednostek**i wybierz istniejącą rolę lub Utwórz nową rolę.
1. W przykładowym tekście wypowiedź etykieta tekst z rolą jednostki.

## <a name="labeling-for-punctuation"></a>Etykietowanie interpunkcji

Nie ma potrzeby etykietowania interpunkcji. Użyj [ustawień aplikacji](luis-reference-application-settings.md) , aby określić, czy interpunkcja ma wpływ na przewidywania wypowiedź.

## <a name="unlabel-entities"></a>Nie etykieta jednostek

Aby usunąć etykietę jednostki, wybierz nazwę jednostki pod tekstem i wybierz pozycję Usuń **etykietę**. Jeśli jednostka, którą próbujesz wyrównać, ma etykietę podskładniki, wówczas podskładniki nie muszą mieć najpierw etykiety.

## <a name="editing-labels-using-the-entity-palette"></a>Edytowanie etykiet przy użyciu palety Entity

Jeśli wystąpi błąd podczas etykietowania, paleta jednostek jest łatwym narzędziem umożliwiającym szybkie edycję. Na przykład, jeśli etykieta jednostki obejmuje dodatkowe słowo przez pomyłkę i ma już etykietę podskładniki, można użyć palety Entity do pędzla nad wymaganym krótszym zakresem wyrazów.

Przykład:

1. Podskładnik typu Pizza składa się z "sera Pizza with", co obejmuje bardzo niepoprawną literę wyrazu "z"

    > [!div class="mx-imgBorder"]
    > ![Paleta jednostek dla jednostki o Poznaniu maszynowym](media/label-utterances/edit-label-with-palette-1.png)

2. Użyj palety Entity, aby wybrać typ Pizza i Pędzel na "ser Pizza". Wynikiem jest to, że tylko Pizza sera jest oznaczona jako Pizza.

    > [!div class="mx-imgBorder"]
    > ![Paleta jednostek dla jednostki o Poznaniu maszynowym](media/label-utterances/edit-label-with-palette-2.png)

## <a name="labels-for-matching-text-entities"></a>Etykiety dla jednostek dopasowywania tekstu

Jednostki dopasowywania tekstu obejmują wstępnie utworzone jednostki, jednostki wyrażeń regularnych, jednostki listy oraz wzorzec. wszystkie jednostki. Są one automatycznie oznaczone jako LUIS, więc użytkownicy nie muszą mieć do nich ręcznego etykiet.

## <a name="entity-prediction-errors"></a>Błędy przewidywania jednostek

Błędy przewidywania jednostek wskazują, że przewidywana jednostka nie jest zgodna z oznaczona jednostką. Jest to Wizualizacja ze wskaźnikiem ostrożności obok wypowiedź.

> [!div class="mx-imgBorder"]
> ![Paleta jednostek dla jednostki o Poznaniu maszynowym](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>Następne kroki

Użyj [pulpitu nawigacyjnego](luis-how-to-use-dashboard.md) i [Przejrzyj wyrażenia długości punktu końcowego](luis-how-to-review-endpoint-utterances.md) , aby zwiększyć jakość przewidywania aplikacji.