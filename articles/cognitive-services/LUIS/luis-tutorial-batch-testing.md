---
title: 'Samouczek: testowanie wsadowe w celu znalezienia problemów — LUIS'
description: W tym samouczku pokazano, jak za pomocą testów wsadowych sprawdzić poprawność jakości aplikacji Language Understanding (LUIS).
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 05/07/2020
ms.openlocfilehash: 7dd181f8cd398dd683296b446028b398a9800b53
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91298322"
---
# <a name="tutorial-batch-test-data-sets"></a>Samouczek: zestawy danych testów wsadowych

W tym samouczku pokazano, jak za pomocą testów wsadowych sprawdzić poprawność jakości aplikacji Language Understanding (LUIS).

Testowanie wsadowe umożliwia zweryfikowanie aktywnego, przeszkolonego stanu modelu z znanym zestawem oznaczonym przez wyrażenia długości i jednostek. W pliku wsadowym w formacie JSON Dodaj wyrażenia długości i ustaw etykiety jednostek, które są wymagane w ramach wypowiedź.

Wymagania dotyczące testowania wsadowego:

* Maksymalnie 1000 wyrażenia długości na test.
* Brak duplikatów.
* Dozwolone typy jednostek: tylko jednostki rozpoznajce maszynowo.

W przypadku korzystania z aplikacji innej niż w tym samouczku *nie należy używać* przykładowej wyrażenia długości już dodanej do aplikacji.

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importowanie aplikacji przykładowej
> * Utwórz plik testu wsadowego
> * Uruchom test wsadowy
> * Przejrzyj wyniki testu

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importowanie aplikacji przykładowej

Zaimportuj aplikację, która przyjmuje zamówienie Pizza na przykład `1 pepperoni pizza on thin crust` .

1.  Pobierz i zapisz [plik JSON aplikacji](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json?raw=true).

1. Zaloguj się do [portalu Luis](https://www.luis.ai)i wybierz swoją **subskrypcję** i **zasób tworzenia** , aby wyświetlić aplikacje przypisane do tego zasobu.
1. Zaimportuj kod JSON do nowej aplikacji, nazwij aplikację `Pizza app` .


1. Wybierz pozycję **szkolenie** w prawym górnym rogu nawigacji, aby szkolić aplikację.

## <a name="what-should-the-batch-file-utterances-include"></a>Co powinien zawierać plik wsadowy wyrażenia długości

Plik wsadowy powinien zawierać wyrażenia długości z jednostkami uczenia maszynowego najwyższego poziomu z etykietą, w tym początkową i końcową pozycję. Wyrażenia długości nie powinna być częścią przykładów już w aplikacji. Powinny one być wyrażenia długości, aby uzyskać pozytywne przewidywania dotyczące zamiar i jednostek.

Możesz oddzielić testy na podstawie zamiaru i/lub jednostki lub uzyskać wszystkie testy (do 1000 wyrażenia długości) w tym samym pliku.

## <a name="batch-file"></a>Plik wsadowy

Przykładowy kod JSON zawiera jeden wypowiedź z etykietą Entity, aby zilustrować, jak wygląda plik testowy. We własnych testach należy mieć wiele wyrażenia długości z poprawnym przeznaczeniem i jednostką uczenia maszynowego.

1. Utwórz `pizza-with-machine-learned-entity-test.json` w edytorze tekstu lub [Pobierz](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/batch-tests/pizza-with-machine-learned-entity-test.json?raw=true) .

2. W pliku wsadowym w formacie JSON Dodaj wypowiedź z **zamiarem** , który ma być przewidywany w teście.

   [!code-json[Add the intents to the batch test file](~/samples-cognitive-services-data-files/luis/batch-tests/pizza-with-machine-learned-entity-test.json "Add the intent to the batch test file")]

## <a name="run-the-batch"></a>Uruchom zadanie wsadowe

1. Na górnym pasku nawigacyjnym wybierz pozycję **test** .

2. Wybierz pozycję **panel testowania partii** w panelu po prawej stronie.

3. Wybierz pozycję **Importuj zestaw danych**.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu aplikacji LUIS z wyróżnioną pozycją Importuj zestaw danych](./media/luis-tutorial-batch-testing/import-dataset-button.png)

4. Wybierz lokalizację pliku `pizza-with-machine-learned-entity-test.json` .

5. Nazwij zestaw danych `pizza test` i wybierz pozycję **gotowe**.

    > [!div class="mx-imgBorder"]
    > ![Wybierz plik](./media/luis-tutorial-batch-testing/import-dataset-modal.png)

6. Wybierz przycisk **Run** (Uruchom).

7. Wybierz pozycję **Zobacz wyniki**.

8. Przejrzyj wyniki w grafie i legendzie.

## <a name="review-batch-results-for-intents"></a>Przejrzyj wyniki partii dla intencji

Wyniki testu pokazują graficznie, w jaki sposób przewidywalno wyrażenia długości testów względem aktywnej wersji.

Na wykresie wsadowym są wyświetlane cztery ćwiartki wyników. Na prawo od wykresu jest filtrem. Filtr zawiera intencje i jednostki. Po wybraniu [sekcji wykresu](luis-concept-batch-test.md#batch-test-results) lub punktu na wykresie, skojarzone wypowiedź są wyświetlane poniżej wykresu.

Po umieszczeniu wskaźnika na wykresie kółkiem myszy może powiększyć lub zmniejszyć poziom wyświetlania na wykresie. Jest to przydatne w przypadku, gdy wiele punktów na wykresie jest ściśle klastrowane.

Wykres znajduje się w czterech ćwiartkach z dwiema sekcjami wyświetlanymi w kolorze czerwonym.

1. Wybierz zamiar **ModifyOrder** na liście filtrów.

    > [!div class="mx-imgBorder"]
    > ![Wybierz ModifyOrder intencję z listy filtrów](./media/luis-tutorial-batch-testing/select-intent-from-filter-list.png)

    Wypowiedź jest przewidywane jako **prawdziwe pozytywne** znaczenie, że wypowiedź pomyślnie pasowała do swojej pozytywnej przewidywania wymienionej w pliku wsadowym.

    > [!div class="mx-imgBorder"]
    > ![Wypowiedź pomyślnie pasowały do swojej pozytywnej prognozowania](./media/luis-tutorial-batch-testing/intent-predicted-true-positive.png)

    Zielony znacznik wyboru na liście filtry wskazuje również sukces testu dla każdego zamiaru. Wszystkie inne intencje są wymienione z 1/1 pozytywnego wyniku, ponieważ wypowiedź został przetestowany z każdym zamiarem, jako negatywny test dla wszelkich intencji niewymienionych w teście wsadowym.

1. Wybierz zamiar **potwierdzenia** . Ten cel nie jest wymieniony w teście wsadowym, więc jest to negatywny test wypowiedź, który jest wymieniony w teście wsadowym.

    > [!div class="mx-imgBorder"]
    > ![Wypowiedź pomyślnie przewidywalna wartość ujemna dla zamiaru nieznajdującego się na liście w pliku wsadowym](./media/luis-tutorial-batch-testing/true-negative-intent.png)

    Test negatywny zakończył się pomyślnie, jak zanotowano zielony tekst w filtrze i siatka.

## <a name="review-batch-test-results-for-entities"></a>Przegląd wyników testów partii dla jednostek

Jednostka ModifyOrder jako jednostka maszyny z podjednostkami wyświetla, czy jednostka najwyższego poziomu jest dopasowana i wyświetla sposób przewidywania podjednostek.

1. Wybierz jednostkę **ModifyOrder** na liście filtrów, a następnie wybierz okrąg w siatce.

1. Prognoza jednostki jest wyświetlana poniżej wykresu. Wyświetlacz zawiera pełne linie dla prognoz, które pasują do oczekiwanych i kropkowanych wierszy w przypadku prognoz, które nie pasują do oczekiwanej wartości.

    > [!div class="mx-imgBorder"]
    > ![Element nadrzędny jednostki został pomyślnie przewidywalny w pliku wsadowym](./media/luis-tutorial-batch-testing/labeled-entity-prediction.png)

## <a name="finding-errors-with-a-batch-test"></a>Znajdowanie błędów z testem wsadowym

W tym samouczku pokazano, jak uruchomić test i interpretować wyniki. Nie obejmuje to zasady testowania ani reagowania na testy zakończone niepowodzeniem.

* Upewnij się, że w teście są pokryte pozytywne i ujemne wyrażenia długości, w tym wyrażenia długości, które mogą być przewidywane dla różnych, ale powiązanych zamierzeń.
* W przypadku niepowodzenia wyrażenia długości wykonaj następujące zadania, a następnie ponownie uruchom testy:
    * Zapoznaj się z bieżącymi przykładami założeń i jednostek, sprawdzając, czy przykład wyrażenia długości aktywnej wersji jest poprawny zarówno w przypadku zamiaru, jak i etykiety jednostek.
    * Dodawanie funkcji, które ułatwiają prognozowanie intencji i jednostek
    * Dodaj bardziej pozytywne przykładowe wyrażenia długości
    * Przeglądanie bilansu przykładowej wyrażenia długości w ramach intencji

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [LUIS How to clean up resources](./includes/cleanup-resources-preview-portal.md)]

## <a name="next-step"></a>Następny krok

Samouczek użył testu wsadowego do walidacji bieżącego modelu.

> [!div class="nextstepaction"]
> [Informacje o wzorcach](luis-tutorial-pattern.md)

