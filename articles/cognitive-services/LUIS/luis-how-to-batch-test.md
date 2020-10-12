---
title: Jak wykonać test wsadowy — LUIS
titleSuffix: Azure Cognitive Services
description: Użyj zestawów testów wsadowych Language Understanding (LUIS), aby znaleźć wyrażenia długości z nieprawidłowymi intencjami i jednostkami.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.openlocfilehash: 8b34005f2796403e32b41a93e4163c7da16d40bb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91540952"
---
# <a name="batch-testing-with-a-set-of-example-utterances"></a>Testowanie wsadowe z zestawem przykład wyrażenia długości

 Testowanie wsadowe to kompleksowy test w bieżącym przeszkolonym modelu, który umożliwia mierzenie jego wydajności w LUIS. Zestawy danych używane do testowania wsadowego nie powinny zawierać przykładu wyrażenia długości w intencjach lub wyrażenia długości odebranych z punktu końcowego środowiska uruchomieniowego przewidywania.

<a name="batch-testing"></a>

## <a name="import-a-dataset-file-for-batch-testing"></a>Importowanie pliku zestawu danych na potrzeby testów wsadowych

1. Wybierz pozycję **Testuj** na górnym pasku, a następnie wybierz pozycję **panel testowania wsadowego**.

    ![Link testowania partii](./media/luis-how-to-batch-test/batch-testing-link.png)

2. Wybierz pozycję **Importuj zestaw danych**. Zostanie wyświetlone okno dialogowe **Importuj nowy zestaw danych** . Wybierz pozycję **Wybierz plik** i Znajdź plik JSON z prawidłowym [formatem JSON](luis-concept-batch-test.md#batch-file-format) , który *nie zawiera więcej niż 1 000* wyrażenia długości do przetestowania.

    Błędy importu są raportowane na czerwono pasku powiadomień u góry przeglądarki. Gdy import zawiera błędy, nie jest tworzony żaden zestaw danych. Aby uzyskać więcej informacji, zobacz [typowe błędy](luis-concept-batch-test.md#common-errors-importing-a-batch).

3. W polu **Nazwa zestawu danych** wprowadź nazwę pliku zestawu danych. Plik zestawu danych zawiera **tablicę wyrażenia długości** , włącznie z *zamiarem* i *obiektami*z etykietą. Przejrzyj [przykładowy plik wsadowy](luis-concept-batch-test.md#batch-file-format) pod kątem składni.

4. Wybierz pozycję **Gotowe**. Plik zestawu danych zostanie dodany.

## <a name="run-rename-export-or-delete-dataset"></a>Uruchamianie, zmiana nazwy, eksport lub usuwanie zestawu danych

Aby uruchomić, zmienić nazwę, wyeksportować lub usunąć zestaw danych, użyj przycisku wielokropka (***...***) na końcu wiersza zestawu danych.

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający listę testów wsadowych z opcjami](./media/luis-how-to-batch-test/batch-testing-options.png)

## <a name="run-a-batch-test-on-your-trained-app"></a>Uruchom test wsadowy dla przeszkolonej aplikacji

Aby uruchomić test, wybierz nazwę zestawu danych, a następnie wybierz pozycję **Uruchom** z kontekstowego paska narzędzi. Po zakończeniu testu w tym wierszu jest wyświetlany wynik testu zestawu danych.

Zestaw danych do pobrania to ten sam plik, który został przekazany do testowania wsadowego.

|State|Znaczenie|
|--|--|
|![Ikona pomyślnego testu zielony okrąg](./media/luis-how-to-batch-test/batch-test-result-green.png)|Wszystkie wyrażenia długości powiodło się.|
|![Niepowodzenie testu czerwona litera x](./media/luis-how-to-batch-test/batch-test-result-red.png)|Co najmniej jeden cel wypowiedź nie jest zgodny z przewidywaniam.|
|![Ikona gotowy do testowania](./media/luis-how-to-batch-test/batch-test-result-blue.png)|Test jest gotowy do uruchomienia.|

<a name="access-batch-test-result-details-in-a-visualized-view"></a>

## <a name="view-batch-test-results"></a>Wyświetl wyniki testów wsadowych

Aby przejrzeć wyniki testów wsadowych, wybierz pozycję **Zobacz wyniki**.

<a name="filter-chart-results-by-intent-or-entity"></a>

## <a name="filter-chart-results"></a>Filtruj wyniki wykresu

Aby odfiltrować wykres według określonego zamiaru lub jednostki, wybierz cel lub jednostkę w panelu filtrowania po prawej stronie. Punkty danych i ich aktualizacje dystrybucji na wykresie zgodnie z wyborem.

![Wizualny wynik testu wsadowego](./media/luis-how-to-batch-test/filter-by-entity.png)

## <a name="view-single-point-utterance-data"></a>Wyświetlanie danych wypowiedź pojedynczych punktów

Na wykresie Umieść kursor nad punktem danych, aby zobaczyć wynik pewności jego przewidywania. Wybierz punkt danych, aby pobrać odpowiadający mu wypowiedź na liście wyrażenia długości w dolnej części strony.

![Wybrany wypowiedź](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

## <a name="view-section-data"></a>Wyświetl dane sekcji

Na wykresie z czterema sekcjami wybierz nazwę sekcji, na przykład **false pozytywną** w prawym górnym rogu wykresu. Pod wykresem wszystkie wyrażenia długości w tej sekcji są wyświetlane poniżej wykresu na liście.

![Wybrane wyrażenia długości według sekcji](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

W tym powyższym obrazie wypowiedź `switch on` jest oznaczona zamiarem TurnAllOn, ale otrzymał przeznaczenie wartości none. Wskazuje to, że TurnAllOn wymaga więcej przykładowych wyrażenia długości w celu zapewnienia oczekiwanego przewidywania.

Dwie sekcje wykresu na czerwono wskazują wyrażenia długości, które nie pasują do oczekiwanego przewidywania. Wskazują one wyrażenia długości, które LUIS wymagają więcej szkoleń.

Dwie sekcje wykresu w kolorze zielonym pasują do oczekiwanego przewidywania.

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="next-steps"></a>Następne kroki

Jeśli testowanie wskazuje, że aplikacja LUIS nie rozpoznaje poprawnych intencji i jednostek, możesz obejść, aby zwiększyć wydajność aplikacji LUIS, dodając etykiety więcej wyrażenia długości lub dodając funkcje.

* [Etykieta sugerowanych wyrażenia długości z LUIS](luis-how-to-review-endpoint-utterances.md)
* [Korzystaj z funkcji, aby zwiększyć wydajność aplikacji LUIS](luis-how-to-add-features.md)
* [Opis testów wsadowych w tym samouczku](luis-tutorial-batch-testing.md)
* [Poznaj koncepcje testowania partii](luis-concept-batch-test.md).
