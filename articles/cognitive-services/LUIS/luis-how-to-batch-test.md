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
ms.date: 12/29/2020
ms.openlocfilehash: b297330f3562babf9e83d36934827f7b92d5ea35
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98787016"
---
# <a name="batch-testing-with-a-set-of-example-utterances"></a>Testowanie wsadowe z zestawem przykład wyrażenia długości

Testy wsadowe sprawdzają poprawność aktywnej przeszkolonej wersji, aby zmierzyć jej dokładność przewidywania. Test wsadowy ułatwia przeglądanie dokładności poszczególnych zamierzeń i jednostek w aktywnej wersji. Zapoznaj się z wynikami testów wsadowych, aby podjąć odpowiednie działania w celu poprawienia dokładności, na przykład dodając więcej przykładowych wyrażenia długości do zamiaru, jeśli aplikacja często nie zidentyfikuje odpowiednich obiektów potrzebnych lub etykietowania w ramach wypowiedź.

## <a name="group-data-for-batch-test"></a>Grupuj dane dla testu wsadowego

Należy pamiętać, że wyrażenia długości używany do testowania wsadowego jest nowy do LUIS. Jeśli masz zestaw danych wyrażenia długości, Podziel wyrażenia długości na trzy zestawy: przykład wyrażenia długości dodany do intencji, wyrażenia długości otrzymany z opublikowanego punktu końcowego i wyrażenia długości używany do wsadowego testowania LUIS po jego przeszkoleniu.

Używany plik JSON w usłudze Batch powinien zawierać wyrażenia długości z jednostkami uczenia maszynowego najwyższego poziomu oznaczonymi z uwzględnieniem pozycji początkowych i końcowych. Wyrażenia długości nie powinna być częścią przykładów już w aplikacji. Powinny one być wyrażenia długości, aby uzyskać pozytywne przewidywania dotyczące zamiar i jednostek.

Możesz oddzielić testy na podstawie zamiaru i/lub jednostki lub uzyskać wszystkie testy (do 1000 wyrażenia długości) w tym samym pliku. 

### <a name="common-errors-importing-a-batch"></a>Typowe błędy podczas importowania partii

Jeśli wystąpią błędy podczas przekazywania pliku wsadowego do LUIS, sprawdź następujące typowe problemy:

* Więcej niż 1 000 wyrażenia długości w pliku wsadowym
* Obiekt wypowiedź JSON, który nie ma właściwości Entities. Właściwość może być pustą tablicą.
* Wyrazy oznaczone w wielu jednostkach
* Etykiety jednostek rozpoczynające się lub kończące się spacją.

## <a name="fixing-batch-errors"></a>Naprawianie błędów partii

Jeśli wystąpią błędy w testach wsadowych, można dodać więcej wyrażenia długości do zamiaru i/lub podawać więcej wyrażenia długości z jednostką, aby pomóc LUIS dyskryminacji między intencjami. Po dodaniu wyrażenia długości i oznaczeniu ich, a mimo to w testowaniu wsadowym nadal pojawiają się błędy przewidywania, rozważ dodanie funkcji [listy fraz](luis-concept-feature.md) z słownictwem specyficznym dla domeny, aby ułatwić Luis szybsze uczenie się.


<a name="batch-testing"></a>

## <a name="batch-testing-using-the-luis-portal"></a>Testowanie wsadowe przy użyciu portalu LUIS 

### <a name="import-and-train-an-example-app"></a>Importowanie i uczenie przykładowej aplikacji

Zaimportuj aplikację, która przyjmuje zamówienie Pizza na przykład `1 pepperoni pizza on thin crust` .

1.  Pobierz i zapisz [plik JSON aplikacji](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json?raw=true).

1. Zaloguj się do [portalu Luis](https://www.luis.ai)i wybierz swoją **subskrypcję** i **zasób tworzenia** , aby wyświetlić aplikacje przypisane do tego zasobu.
1. Wybierz strzałkę obok pozycji **Nowa aplikacja** , a następnie kliknij pozycję **Importuj jako plik JSON** , aby zaimportować plik JSON do nowej aplikacji. Nadaj nazwę aplikacji `Pizza app` .


1. Wybierz pozycję **szkolenie** w prawym górnym rogu nawigacji, aby szkolić aplikację.


[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

### <a name="batch-test-file"></a>Plik testu wsadowego

Przykładowy kod JSON zawiera jeden wypowiedź z etykietą Entity, aby zilustrować, jak wygląda plik testowy. We własnych testach należy mieć wiele wyrażenia długości z poprawnym przeznaczeniem i jednostką uczenia maszynowego.

1. Utwórz `pizza-with-machine-learned-entity-test.json` w edytorze tekstu lub [Pobierz](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/batch-tests/pizza-with-machine-learned-entity-test.json?raw=true) .

2. W pliku wsadowym w formacie JSON Dodaj wypowiedź z **zamiarem** , który ma być przewidywany w teście.

   [!code-json[Add the intents to the batch test file](~/samples-cognitive-services-data-files/luis/batch-tests/pizza-with-machine-learned-entity-test.json "Add the intent to the batch test file")]

## <a name="run-the-batch"></a>Uruchom zadanie wsadowe

1. Na górnym pasku nawigacyjnym wybierz pozycję **test** .

2. Wybierz pozycję **panel testowania partii** w panelu po prawej stronie.

    ![Link testowania partii](./media/luis-how-to-batch-test/batch-testing-link.png)

3. Wybierz pozycję **Importuj**. W wyświetlonym oknie dialogowym wybierz pozycję **Wybierz plik** i Znajdź plik JSON z prawidłowym formatem JSON, który nie zawiera *więcej niż 1 000* wyrażenia długości do przetestowania.

    Błędy importu są raportowane na czerwono pasku powiadomień u góry przeglądarki. Gdy import zawiera błędy, nie jest tworzony żaden zestaw danych. Aby uzyskać więcej informacji, zobacz [typowe błędy](#common-errors-importing-a-batch).

4. Wybierz lokalizację pliku `pizza-with-machine-learned-entity-test.json` .

5. Nazwij zestaw danych `pizza test` i wybierz pozycję **gotowe**.

6. Wybierz przycisk **Run** (Uruchom). Po uruchomieniu testu wsadowego wybierz pozycję **Zobacz wyniki**. 

    > [!TIP]
    > * Wybranie pozycji **Pobierz** spowoduje pobranie tego samego pliku, który został przekazany.
    > * Jeśli test wsadowy nie powiódł się, co najmniej jeden cel wypowiedź nie jest zgodny z przewidywaniam.

<a name="access-batch-test-result-details-in-a-visualized-view"></a>

### <a name="review-batch-results-for-intents"></a>Przejrzyj wyniki partii dla intencji

Aby przejrzeć wyniki testów wsadowych, wybierz pozycję **Zobacz wyniki**. Wyniki testu pokazują graficznie, w jaki sposób przewidywalno wyrażenia długości testów względem aktywnej wersji.

Na wykresie wsadowym są wyświetlane cztery ćwiartki wyników. Na prawo od wykresu jest filtrem. Filtr zawiera intencje i jednostki. Po wybraniu [sekcji wykresu](#review-batch-results-for-intents) lub punktu na wykresie, skojarzone wypowiedź są wyświetlane poniżej wykresu.

Po umieszczeniu wskaźnika na wykresie kółkiem myszy może powiększyć lub zmniejszyć poziom wyświetlania na wykresie. Jest to przydatne w przypadku, gdy wiele punktów na wykresie jest ściśle klastrowane.

Wykres znajduje się w czterech ćwiartkach z dwiema sekcjami wyświetlanymi w kolorze czerwonym.

1. Wybierz zamiar **ModifyOrder** na liście filtrów. Wypowiedź jest przewidywane jako **prawdziwe pozytywne** znaczenie, że wypowiedź pomyślnie pasowała do swojej pozytywnej przewidywania wymienionej w pliku wsadowym.

    > [!div class="mx-imgBorder"]
    > ![Wypowiedź pomyślnie pasowały do swojej pozytywnej prognozowania](./media/luis-tutorial-batch-testing/intent-predicted-true-positive.png)

    Zielony znacznik wyboru na liście filtry wskazuje również sukces testu dla każdego zamiaru. Wszystkie inne intencje są wymienione z 1/1 pozytywnego wyniku, ponieważ wypowiedź został przetestowany z każdym zamiarem, jako negatywny test dla wszelkich intencji niewymienionych w teście wsadowym.

1. Wybierz zamiar **potwierdzenia** . Ten cel nie jest wymieniony w teście wsadowym, więc jest to negatywny test wypowiedź, który jest wymieniony w teście wsadowym.

    > [!div class="mx-imgBorder"]
    > ![Wypowiedź pomyślnie przewidywalna wartość ujemna dla zamiaru nieznajdującego się na liście w pliku wsadowym](./media/luis-tutorial-batch-testing/true-negative-intent.png)

    Test negatywny zakończył się pomyślnie, jak zanotowano zielony tekst w filtrze i siatka.

### <a name="review-batch-test-results-for-entities"></a>Przegląd wyników testów partii dla jednostek

Jednostka ModifyOrder, jako jednostka maszyny z podjednostkami, wyświetla, czy jednostka najwyższego poziomu jest dopasowana i jak są przewidywane podjednostki.

1. Wybierz jednostkę **ModifyOrder** na liście filtrów, a następnie wybierz okrąg w siatce.

1. Prognoza jednostki jest wyświetlana poniżej wykresu. Wyświetlacz zawiera pełne linie dla prognoz, które pasują do oczekiwanych i kropkowanych wierszy w przypadku prognoz, które nie pasują do oczekiwanej wartości.

    > [!div class="mx-imgBorder"]
    > ![Element nadrzędny jednostki został pomyślnie przewidywalny w pliku wsadowym](./media/luis-tutorial-batch-testing/labeled-entity-prediction.png)

<a name="filter-chart-results-by-intent-or-entity"></a>

#### <a name="filter-chart-results"></a>Filtruj wyniki wykresu

Aby odfiltrować wykres według określonego zamiaru lub jednostki, wybierz cel lub jednostkę w panelu filtrowania po prawej stronie. Punkty danych i ich aktualizacje dystrybucji na wykresie zgodnie z wyborem.

![Wizualny wynik testu wsadowego](./media/luis-how-to-batch-test/filter-by-entity.png)

### <a name="chart-result-examples"></a>Przykłady wyników wykresu

Wykres w portalu LUIS można wykonać następujące czynności:
 
#### <a name="view-single-point-utterance-data"></a>Wyświetlanie danych wypowiedź pojedynczych punktów

Na wykresie Umieść kursor nad punktem danych, aby zobaczyć wynik pewności jego przewidywania. Wybierz punkt danych, aby pobrać odpowiadający mu wypowiedź na liście wyrażenia długości w dolnej części strony.

![Wybrany wypowiedź](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

#### <a name="view-section-data"></a>Wyświetl dane sekcji

Na wykresie z czterema sekcjami wybierz nazwę sekcji, na przykład **false pozytywną** w prawym górnym rogu wykresu. Pod wykresem wszystkie wyrażenia długości w tej sekcji są wyświetlane poniżej wykresu na liście.

![Wybrane wyrażenia długości według sekcji](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

W tym powyższym obrazie wypowiedź `switch on` jest oznaczona zamiarem TurnAllOn, ale otrzymał przeznaczenie wartości none. Wskazuje to, że TurnAllOn wymaga więcej przykładowych wyrażenia długości w celu zapewnienia oczekiwanego przewidywania.

Dwie sekcje wykresu na czerwono wskazują wyrażenia długości, które nie pasują do oczekiwanego przewidywania. Wskazują one wyrażenia długości, które LUIS wymagają więcej szkoleń.

Dwie sekcje wykresu w kolorze zielonym pasują do oczekiwanego przewidywania.

## <a name="batch-testing-using-the-rest-api"></a>Testowanie wsadowe przy użyciu interfejsu API REST 

LUIS umożliwia wsadowe testowanie przy użyciu portalu LUIS i interfejsu API REST. Punkty końcowe interfejsu API REST są wymienione poniżej. Aby uzyskać informacje o testowaniu wsadowym przy użyciu portalu LUIS, zobacz [Samouczek: zestawy danych testów wsadowych](). Użyj pełnych adresów URL poniżej, zastępując wartości symboli zastępczych własnym kluczem i punktem końcowym przewidywania LUIS. 

Pamiętaj, aby dodać klucz LUIS do `Ocp-Apim-Subscription-Key` nagłówka i ustawić wartość `Content-Type` `application/json` .

### <a name="start-a-batch-test"></a>Rozpocznij Test wsadowy

Rozpocznij Test wsadowy, korzystając z identyfikatora wersji aplikacji lub miejsca publikacji. Wyślij żądanie **post** do jednego z następujących formatów punktów końcowych. Dołącz plik wsadowy do treści żądania.

Miejsce publikowania
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-NAME>/evaluations`

Identyfikator wersji aplikacji
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations`

Te punkty końcowe zwróci identyfikator operacji, który zostanie użyty do sprawdzenia stanu i uzyskania wyników. 


### <a name="get-the-status-of-an-ongoing-batch-test"></a>Pobierz stan trwającego testu wsadowego

Użyj identyfikatora operacji z testu wsadowego rozpoczętego w celu pobrania jego stanu z następujących formatów punktów końcowych: 

Miejsce publikowania
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-ID>/evaluations/<YOUR-OPERATION-ID>/status`

Identyfikator wersji aplikacji
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations/<YOUR-OPERATION-ID>/status`

### <a name="get-the-results-from-a-batch-test"></a>Pobierz wyniki testu wsadowego

Użyj identyfikatora operacji z testu wsadowego rozpoczętego, aby uzyskać wyniki z następujących formatów punktów końcowych: 

Miejsce publikowania
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-ID>/evaluations/<YOUR-OPERATION-ID>/result`

Identyfikator wersji aplikacji
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations/<YOUR-OPERATION-ID>/result`


### <a name="batch-file-of-utterances"></a>Plik wsadowy elementu wyrażenia długości

Prześlij plik wsadowy wyrażenia długości, nazywany *zestawem danych*, na potrzeby testowania wsadowego. Zestaw danych jest plikiem w formacie JSON zawierającym maksymalnie 1 000 oznaczony wyrażenia długości. W aplikacji można testować do 10 zestawów danych. Jeśli chcesz przetestować więcej, Usuń zestaw danych, a następnie Dodaj nowy. Wszystkie jednostki niestandardowe w modelu pojawiają się w filtr jednostek testów wsadowych, nawet jeśli w danych pliku wsadowego nie ma odpowiednich jednostek.

Plik wsadowy składa się z wyrażenia długości. Każdy wypowiedź musi mieć oczekiwaną prognozę założeń oraz wszystkie [jednostki uczenia maszynowego](luis-concept-entity-types.md#types-of-entities) , które powinny być wykrywane.

### <a name="batch-syntax-template-for-intents-with-entities"></a>Szablon składni usługi Batch dla intencji z jednostkami

Użyj następującego szablonu, aby uruchomić plik wsadowy:

```JSON
{
    "LabeledTestSetUtterances": [
        {
            "text": "play a song",
            "intent": "play_music",
            "entities": [
                {
                    "entity": "song_parent",
                    "startPos": 0,
                    "endPos": 15,
                    "children": [
                        {
                            "entity": "pre_song",
                            "startPos": 0,
                            "endPos": 3
                        },
                        {
                            "entity": "song_info",
                            "startPos": 5,
                            "endPos": 15
                        }
                    ]
                }
            ]
        }
    ]
}

```

Plik wsadowy używa właściwości **startPos** i **endPos** do zanotowania początku i końca jednostki. Wartości są zależne od zera i nie powinny zaczynać ani kończyć się spacją. Różni się to od dzienników zapytań, które używają właściwości startIndex i endIndex.

Jeśli nie chcesz testować jednostek, Dołącz `entities` Właściwość i ustaw wartość jako pustą tablicę `[]` .

### <a name="rest-api-batch-test-results"></a>Wyniki testów wsadowych interfejsu API REST

Interfejs API zwrócił kilka obiektów:

* Informacje o modelach intencji i jednostek, takich jak precyzja, odwołania i F-Score.
* Informacje o modelach jednostek, takich jak precyzja, odwołania i F-Score) dla każdej jednostki 
  * Przy użyciu `verbose` flagi można uzyskać więcej informacji na temat jednostki, takiej jak `entityTextFScore` i `entityTypeFScore` .
* Podano wyrażenia długości z nazwami zamierzone i oznaczonymi intencjami
* Lista fałszywych jednostek pozytywnych i Lista fałszywych nieujemnych jednostek.

## <a name="next-steps"></a>Następne kroki

Jeśli testowanie wskazuje, że aplikacja LUIS nie rozpoznaje poprawnych intencji i jednostek, możesz obejść, aby zwiększyć wydajność aplikacji LUIS, dodając etykiety więcej wyrażenia długości lub dodając funkcje.

* [Etykieta sugerowanych wyrażenia długości z LUIS](luis-how-to-review-endpoint-utterances.md)
* [Korzystaj z funkcji, aby zwiększyć wydajność aplikacji LUIS](luis-how-to-add-features.md)
