---
title: Jak wykonać test wsadowy — usługa LUIS
titleSuffix: Azure Cognitive Services
description: Użyj Language Understanding testów wsadowych (LUIS), aby znaleźć wypowiedzi z nieprawidłowymi intencjami i jednostkami.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 04/13/2021
ms.openlocfilehash: 9fe4f21a5c9e9e26a2f94b8a60cba47916842fe3
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107501794"
---
# <a name="batch-testing-with-a-set-of-example-utterances"></a>Testowanie wsadowe za pomocą zestawu przykładowych wypowiedzi

Testowanie wsadowe weryfikuje aktywną wytrenowane wersje, aby zmierzyć jej dokładność przewidywania. Test wsadowy ułatwia wyświetlenie dokładności poszczególnych intencji i jednostek w aktywnej wersji. Przejrzyj wyniki testu wsadowego, aby podjąć odpowiednie działania w celu zwiększenia dokładności, na przykład dodać więcej przykładowych wypowiedzi do intencji, jeśli aplikacja często nie może zidentyfikować poprawnej intencji lub etykietować jednostki w wypowiedzi.

## <a name="group-data-for-batch-test"></a>Grupowanie danych dla testu wsadowego

Ważne jest, aby wypowiedzi używane do testowania wsadowego zostały nowe dla usługi LUIS. Jeśli masz zestaw danych wypowiedzi, podziel je na trzy zestawy: przykładowe wypowiedzi dodane do intencji, wypowiedzi odebrane z opublikowanego punktu końcowego i wypowiedzi używane do testowania wsadowego usługi LUIS po zakończeniu trenowania.

Wsadowy plik JSON powinien zawierać wypowiedzi z jednostkami uczenia maszynowego najwyższego poziomu oznaczonymi etykietą, w tym pozycją rozpoczęcia i zakończenia. Wypowiedzi nie powinny być częścią przykładów już w aplikacji. Powinny to być wypowiedzi, które chcesz pozytywnie przewidzieć dla intencji i jednostek.

Testy można oddzielić według intencji i/lub jednostki lub mieć wszystkie testy (do 1000 wypowiedzi) w tym samym pliku. 

### <a name="common-errors-importing-a-batch"></a>Typowe błędy podczas importowania partii

Jeśli podczas przekazywania pliku wsadowego do usługi LUIS występują błędy, sprawdź, czy występują następujące typowe problemy:

* Więcej niż 1000 wypowiedzi w pliku wsadowym
* Obiekt JSON wypowiedzi, który nie ma właściwości entities. Właściwość może być pustą tablicą.
* Wyrazy oznaczone etykietami w wielu jednostkach
* Etykiety jednostek rozpoczynające się lub kończące w przestrzeni.

## <a name="fixing-batch-errors"></a>Naprawianie błędów wsadowych

Jeśli podczas testowania wsadowego występują błędy, możesz dodać więcej wypowiedzi do intencji i/lub dodać etykiety do większej liczby wypowiedzi za pomocą jednostki, aby ułatwić ujmowanie między intencjami przez usługę LUIS. Jeśli dodano wypowiedzi i oznaczyliśmy je etykietami i nadal występują błędy [](luis-concept-feature.md) przewidywania podczas testowania wsadowego, rozważ dodanie funkcji listy fraz ze słownictwem specyficznym dla domeny, aby pomóc uciekinierzom usługi LUIS szybciej się uczyć.


<a name="batch-testing"></a>

## <a name="batch-testing-using-the-luis-portal"></a>Testowanie wsadowe przy użyciu portalu usługi LUIS 

### <a name="import-and-train-an-example-app"></a>Importowanie i szkolenie przykładowej aplikacji

Zaimportuj aplikację, która zamówi pizzę, taką jak `1 pepperoni pizza on thin crust` .

1.  Pobierz i zapisz [plik JSON aplikacji](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json?raw=true).

1. Zaloguj się do portalu [usługi LUIS](https://www.luis.ai)  i wybierz zasób **Subskrypcja** i Tworzenie, aby wyświetlić aplikacje przypisane do tego zasobu tworzenia.
1. Wybierz strzałkę obok pozycji **Nowa aplikacja i** kliknij pozycję Importuj jako dane **JSON,** aby zaimportować dane JSON do nowej aplikacji. Nadaj aplikacji nazwę `Pizza app` .


1. Wybierz **pozycję Train** (Trenuj) w prawym górnym rogu nawigacji, aby wytszkolić aplikację.


[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

### <a name="batch-test-file"></a>Plik testowy usługi Batch

Przykładowy plik JSON zawiera jedną wypowiedź z jednostką oznaczoną etykietą, aby zilustrować, jak wygląda plik testowy. We własnych testach powinno być wiele wypowiedzi z poprawną intencją i jednostką uczenia maszynowego z etykietą.

1. Utwórz `pizza-with-machine-learned-entity-test.json` plik w edytorze tekstów lub [pobierz](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/batch-tests/pizza-with-machine-learned-entity-test.json?raw=true) go.

2. W pliku wsadowym w formacie JSON  dodaj wypowiedź z intencją, którą chcesz przewidzieć w teście.

   [!code-json[Add the intents to the batch test file](~/samples-cognitive-services-data-files/luis/batch-tests/pizza-with-machine-learned-entity-test.json "Add the intent to the batch test file")]

## <a name="run-the-batch"></a>Uruchamianie partii

1. Wybierz **pozycję Testuj** na górnym pasku nawigacyjnym.

2. Wybierz **pozycję Panel testowania usługi Batch** w panelu po prawej stronie.

    ![Link do testowania wsadowego](./media/luis-how-to-batch-test/batch-testing-link.png)

3. Wybierz pozycję **Importuj**. W wyświetlonym oknie  dialogowym wybierz pozycję Wybierz plik i znajdź plik JSON o poprawnym formacie JSON, który zawiera nie więcej niż *1000* wypowiedzi do przetestowania.

    Błędy importowania są zgłaszane na czerwonym pasku powiadomień w górnej części przeglądarki. Jeśli import zawiera błędy, zestaw danych nie jest tworzony. Aby uzyskać więcej informacji, zobacz [Typowe błędy](#common-errors-importing-a-batch).

4. Wybierz lokalizację `pizza-with-machine-learned-entity-test.json` pliku.

5. Nadaj zestawowi `pizza test` danych nazwę i wybierz pozycję **Gotowe.**

6. Wybierz przycisk **Run** (Uruchom). Po zakończeniu testu wsadowego wybierz pozycję **Zobacz wyniki**. 

    > [!TIP]
    > * Wybranie **opcji** Pobierz spowoduje pobranie przekazanego pliku.
    > * Jeśli test wsadowy zakończył się niepowodzeniem, co najmniej jedna intencja wypowiedzi nie pasuje do przewidywania.

<a name="access-batch-test-result-details-in-a-visualized-view"></a>

### <a name="review-batch-results-for-intents"></a>Przeglądanie wyników wsadowych dla intencji

Aby przejrzeć wyniki testu wsadowego, wybierz **pozycję Zobacz wyniki**. Wyniki testu pokazują graficznie, jak wypowiedzi testowe zostały przewidywane względem aktywnej wersji.

Wykres wsadowy wyświetla cztery ćwiartki wyników. Po prawej stronie wykresu znajduje się filtr. Filtr zawiera intencje i jednostki. Po wybraniu [sekcji wykresu](#review-batch-results-for-intents) lub punktu na wykresie skojarzone wypowiedzi są wyświetlane poniżej wykresu.

Po umieszczeniu wskaźnika myszy na wykresie można powiększyć lub zmniejszyć wyświetlanie na wykresie za pomocą kółka myszy. Jest to przydatne, gdy istnieje wiele punktów wykresu ściśle ze sobą zgrupowanych.

Wykres znajduje się w czterech ćwiartce, a dwie sekcje są wyświetlane na czerwono.

1. Wybierz **intencję ModifyOrder** na liście filtrów. Wypowiedź jest przewidywana  jako prawdziwie dodatnia, co oznacza, że wypowiedź pomyślnie dopasowała swoje pozytywne przewidywanie wymienione w pliku wsadowym.

    > [!div class="mx-imgBorder"]
    > ![Wypowiedź pomyślnie dopasowała swoje dodatnie przewidywanie](./media/luis-tutorial-batch-testing/intent-predicted-true-positive.png)

    Zielone znaczniki wyboru na liście filtrów wskazują również powodzenie testu dla każdej intencji. Wszystkie pozostałe intencje są wyświetlane z wynikiem dodatnim 1/1, ponieważ wypowiedź została przetestowana względem każdej intencji jako test negatywny dla intencji, które nie zostały wymienione w teście wsadowym.

1. Wybierz **intencję Potwierdzenie.** Ta intencja nie jest wymieniona w teście wsadowym, więc jest to negatywny test wypowiedzi, który jest wymieniony w teście wsadowym.

    > [!div class="mx-imgBorder"]
    > ![Wypowiedź pomyślnie przewidziała negatywną dla intencji nieznajdącej się na liście w pliku wsadowym](./media/luis-tutorial-batch-testing/true-negative-intent.png)

    Test ujemny został pomyślnie zanotowany z zielonym tekstem w filtrze i siatką.

### <a name="review-batch-test-results-for-entities"></a>Przeglądanie wyników testów wsadowych dla jednostek

Jednostka ModifyOrder jako jednostka maszyny z podjęciami wyświetla, czy jednostka najwyższego poziomu jest do siebie dopasowana i jak są przewidywane podjednostwa.

1. Wybierz **jednostkę ModifyOrder** na liście filtrów, a następnie wybierz okrąg w siatce.

1. Przewidywanie jednostek jest wyświetlane poniżej wykresu. Ekran zawiera pełne linie dla przewidywań, które pasują do oczekiwań, i kropkowane linie dla przewidywań, które nie są zgodne z oczekiwaniami.

    > [!div class="mx-imgBorder"]
    > ![Element nadrzędny jednostki pomyślnie przewidywany w pliku wsadowym](./media/luis-tutorial-batch-testing/labeled-entity-prediction.png)

<a name="filter-chart-results-by-intent-or-entity"></a>

#### <a name="filter-chart-results"></a>Filtrowanie wyników wykresu

Aby filtrować wykres według określonej intencji lub jednostki, wybierz intencję lub jednostkę w panelu filtrowania po prawej stronie. Punkty danych i ich rozkład są aktualizowane na wykresie zgodnie z wyborem.

![Wynik wizualizowanego testu wsadowego](./media/luis-how-to-batch-test/filter-by-entity.png)

### <a name="chart-result-examples"></a>Przykłady wyników wykresu

Wykres w portalu usługi LUIS umożliwia wykonywanie następujących akcji:
 
#### <a name="view-single-point-utterance-data"></a>Wyświetlanie danych wypowiedzi jednopunktowej

Na wykresie umieść kursor nad punktem danych, aby zobaczyć wynik pewności przewidywania. Wybierz punkt danych, aby pobrać odpowiednią wypowiedź na liście wypowiedzi w dolnej części strony.

![Wybrana wypowiedź](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

#### <a name="view-section-data"></a>Wyświetlanie danych sekcji

Na wykresie z czterema sekcjami wybierz nazwę sekcji, na przykład **Wynik** fałszywie dodatni w prawym górnym rogu wykresu. Poniżej wykresu wszystkie wypowiedzi w tej sekcji są wyświetlane poniżej wykresu na liście.

![Wybrane wypowiedzi według sekcji](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

Na poprzedniej ilustracji wypowiedź jest oznaczona etykietą z intencją `switch on` TurnAllOn, ale otrzymała przewidywanie intencji None. Oznacza to, że intencja TurnAllOn potrzebuje więcej przykładowych wypowiedzi w celu przewidywania.

Dwie sekcje wykresu na czerwono wskazują wypowiedzi, które nie pasują do oczekiwanego przewidywania. Wskazują one wypowiedzi, które usługa LUIS wymaga więcej szkolenia.

Dwie sekcje wykresu w kolorze zielonym pasują do oczekiwanego przewidywania.

## <a name="batch-testing-using-the-rest-api"></a>Testowanie wsadowe przy użyciu interfejsu API REST 

Usługa LUIS umożliwia testowanie wsadowe przy użyciu portalu usługi LUIS i interfejsu API REST. Poniżej wymieniono punkty końcowe interfejsu API REST. Aby uzyskać informacje na temat testowania wsadowego przy użyciu portalu usługi LUIS, zobacz [Samouczek: zestawy danych testów wsadowych.]() Użyj poniższych pełnych adresów URL, zastępując wartości symboli zastępczych własnym kluczem przewidywania usługi LUIS i punktem końcowym. 

Pamiętaj, aby dodać klucz usługi LUIS `Ocp-Apim-Subscription-Key` do pliku w nagłówku i ustawić wartość `Content-Type` `application/json` .

### <a name="start-a-batch-test"></a>Uruchamianie testu wsadowego

Rozpocznij test wsadowy przy użyciu identyfikatora wersji aplikacji lub miejsca publikowania. Wyślij żądanie **POST** do jednego z następujących formatów punktu końcowego. Dołącz plik wsadowy do treści żądania.

Miejsce publikowania
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-NAME>/evaluations`

Identyfikator wersji aplikacji
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations`

Te punkty końcowe zwracają identyfikator operacji, który będzie używać do sprawdzania stanu i uzyskania wyników. 


### <a name="get-the-status-of-an-ongoing-batch-test"></a>Uzyskiwanie stanu trwającego testu wsadowego

Użyj identyfikatora operacji z testu wsadowego, który został rozpoczęty, aby uzyskać jego stan z następujących formatów punktu końcowego: 

Miejsce publikowania
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-ID>/evaluations/<YOUR-OPERATION-ID>/status`

Identyfikator wersji aplikacji
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations/<YOUR-OPERATION-ID>/status`

### <a name="get-the-results-from-a-batch-test"></a>Uzyskiwanie wyników z testu wsadowego

Użyj identyfikatora operacji z testu wsadowego, który rozpoczęto, aby uzyskać wyniki z następujących formatów punktów końcowych: 

Miejsce publikowania
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-ID>/evaluations/<YOUR-OPERATION-ID>/result`

Identyfikator wersji aplikacji
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations/<YOUR-OPERATION-ID>/result`


### <a name="batch-file-of-utterances"></a>Plik wsadowy wypowiedzi

Prześlij plik wsadowy wypowiedzi, znany jako *zestaw danych,* do testowania wsadowego. Zestaw danych to plik w formacie JSON zawierający maksymalnie 1000 wypowiedzi oznaczonych etykietami. W aplikacji można przetestować maksymalnie 10 zestawów danych. Jeśli chcesz przetestować więcej, usuń zestaw danych, a następnie dodaj nowy. Wszystkie jednostki niestandardowe w modelu są wyświetlane w filtrze jednostek testów wsadowych, nawet jeśli w danych pliku wsadowego nie ma odpowiednich jednostek.

Plik wsadowy składa się z wypowiedzi. Każda wypowiedź musi mieć oczekiwane przewidywanie intencji wraz [z](luis-concept-entity-types.md#machine-learned-ml-entity) dowolnymi jednostkami uczenia maszynowego, które powinny zostać wykryte.

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

Plik wsadowy używa **właściwości startPos** **i endPos,** aby zanotować początek i koniec jednostki. Wartości są od zera i nie powinny zaczynać się ani kończyć spacją. Różni się to od dzienników zapytań, które używają właściwości startIndex i endIndex.

Jeśli nie chcesz testować jednostek, uwzględnij właściwość i `entities` ustaw wartość jako pustą tablicę `[]` .

### <a name="rest-api-batch-test-results"></a>Wyniki testów wsadowych interfejsu API REST

Interfejs API zwraca kilka obiektów:

* Informacje o modelach intencji i jednostek, takie jak precyzja, odwoływanie i ocena F.
* Informacje o modelach jednostek, takie jak precyzja, odwoływanie i ocena F) dla każdej jednostki 
  * Za pomocą `verbose` flagi można uzyskać więcej informacji na temat jednostki, takich jak `entityTextFScore` i `entityTypeFScore` .
* Podane wypowiedzi z przewidywanymi i oznaczonymi nazwami intencji
* Lista jednostek fałszywie dodatnich i lista jednostek fałszywie ujemnych.

## <a name="next-steps"></a>Następne kroki

Jeśli testowanie wskazuje, że aplikacja usługi LUIS nie rozpoznaje poprawnych intencji i jednostek, możesz pracować nad zwiększeniem wydajności aplikacji LUIS, oznaczając etykietami więcej wypowiedzi lub dodając funkcje.

* [Oznaczanie etykietą sugerowanych wypowiedzi za pomocą usługi LUIS](luis-how-to-review-endpoint-utterances.md)
* [Korzystanie z funkcji w celu poprawy wydajności aplikacji LUIS](luis-how-to-add-features.md)
