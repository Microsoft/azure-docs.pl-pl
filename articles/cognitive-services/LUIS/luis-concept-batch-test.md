---
title: Testy wsadowe — LUIS
titleSuffix: Azure Cognitive Services
description: Korzystaj z testów wsadowych, aby w sposób ciągły pracować nad aplikacją, aby udoskonalić go i ulepszyć jego zrozumienie.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: f3a8f5ef8119d9895f67e07ea1b68c660be59f9b
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91541870"
---
# <a name="batch-testing-with-1000-utterances-in-luis-portal"></a>Testowanie wsadowe przy użyciu 1000 wyrażenia długości w portalu LUIS

Testy wsadowe sprawdzają poprawność aktywnej przeszkolonej wersji, aby zmierzyć jej dokładność przewidywania. Test wsadowy ułatwia przeglądanie dokładności poszczególnych zamierzeń i jednostek w aktywnej wersji, wyświetlając wyniki z wykresem. Zapoznaj się z wynikami testów wsadowych, aby podjąć odpowiednie działania w celu poprawienia dokładności, na przykład dodając więcej przykładowych wyrażenia długości do zamiaru, jeśli aplikacja często nie zidentyfikuje odpowiednich obiektów potrzebnych lub etykietowania w ramach wypowiedź.

## <a name="group-data-for-batch-test"></a>Grupuj dane dla testu wsadowego

Należy pamiętać, że wyrażenia długości używany do testowania wsadowego jest nowy do LUIS. Jeśli masz zestaw danych wyrażenia długości, Podziel wyrażenia długości na trzy zestawy: przykład wyrażenia długości dodany do intencji, wyrażenia długości otrzymany z opublikowanego punktu końcowego i wyrażenia długości używany do wsadowego testowania LUIS po jego przeszkoleniu.

## <a name="a-data-set-of-utterances"></a>Zestaw danych wyrażenia długości

Prześlij plik wsadowy wyrażenia długości, nazywany *zestawem danych*, na potrzeby testowania wsadowego. Zestaw danych jest plikiem w formacie JSON zawierającym maksymalnie 1 000 oznaczony **niezduplikowaną** wyrażenia długości. W aplikacji można testować do 10 zestawów danych. Jeśli chcesz przetestować więcej, Usuń zestaw danych, a następnie Dodaj nowy.

|**Przepisy**|
|--|
|* Brak zduplikowanych wyrażenia długości|
|1000 wyrażenia długości lub mniej|

* Duplikaty są uważane za dokładne dopasowania ciągu, a nie dopasowania, które są tokenami w pierwszej kolejności.

## <a name="entities-allowed-in-batch-tests"></a>Jednostki dozwolone w testach wsadowych

Wszystkie jednostki niestandardowe w modelu pojawiają się w filtr jednostek testów wsadowych, nawet jeśli w danych pliku wsadowego nie ma odpowiednich jednostek.

<a name="json-file-with-no-duplicates"></a>
<a name="example-batch-file"></a>

## <a name="batch-file-format"></a>Format pliku wsadowego

Plik wsadowy składa się z wyrażenia długości. Każdy wypowiedź musi mieć oczekiwaną prognozę założeń oraz wszystkie [jednostki uczenia maszynowego](luis-concept-entity-types.md#types-of-entities) , które powinny być wykrywane.

## <a name="batch-syntax-template-for-intents-with-entities"></a>Szablon składni usługi Batch dla intencji z jednostkami

Użyj następującego szablonu, aby uruchomić plik wsadowy:

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities":
    [
        {
            "entity": "entity name 1 goes here",
            "startPos": 14,
            "endPos": 23
        },
        {
            "entity": "entity name 2 goes here",
            "startPos": 14,
            "endPos": 23
        }
    ]
  }
]
```

Plik wsadowy używa właściwości **startPos** i **endPos** do zanotowania początku i końca jednostki. Wartości są zależne od zera i nie powinny zaczynać ani kończyć się spacją. Różni się to od dzienników zapytań, które używają właściwości startIndex i endIndex.

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="batch-syntax-template-for-intents-without-entities"></a>Szablon składni usługi Batch dla intencji bez jednostek

Użyj następującego szablonu, aby uruchomić plik wsadowy bez jednostek:

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities": []
  }
]
```

Jeśli nie chcesz testować jednostek, Dołącz `entities` Właściwość i ustaw wartość jako pustą tablicę `[]` .


## <a name="common-errors-importing-a-batch"></a>Typowe błędy podczas importowania partii

Typowe błędy obejmują:

> * Ponad 1 000 wyrażenia długości
> * Obiekt wypowiedź JSON, który nie ma właściwości Entities. Właściwość może być pustą tablicą.
> * Wyrazy oznaczone w wielu jednostkach
> * Etykieta jednostki rozpoczyna się lub kończy na miejscu.

## <a name="batch-test-state"></a>Test wsadowy — stan

LUIS śledzi stan ostatniego testu zestawu danych. Obejmuje to rozmiar (liczbę wyrażenia długości w partii), datę ostatniego uruchomienia i ostatni wynik (liczba pomyślnie przewidzianych wyrażenia długości).

<a name="sections-of-the-results-chart"></a>

## <a name="batch-test-results"></a>Wyniki testów wsadowych

Wynik testu wsadowego jest wykresem punktowym, znanym jako macierz błędów. Ten wykres to 4-kierunkowe porównanie wyrażenia długości w pliku wsadowym oraz przewidywanych zamiar i elementów bieżącego modelu.

Punkty danych w sekcjach **fałszywe pozytywne** i **fałszywe wartości ujemne** wskazują błędy, które należy zbadać. Jeśli wszystkie punkty danych znajdują się w sekcjach **prawdziwe pozytywne** i **prawdziwe** , dokładność Twojej aplikacji jest idealna dla tego zestawu danych.

![Cztery sekcje wykresu](./media/luis-concept-batch-test/chart-sections.png)

Ten wykres ułatwia znalezienie wyrażenia długości, że LUIS przewidywane nieprawidłowo na podstawie bieżącego szkolenia. Wyniki są wyświetlane na poszczególnych regionach wykresu. Wybierz poszczególne punkty na wykresie, aby przejrzeć informacje wypowiedź lub wybierz nazwę regionu, aby przejrzeć wyniki wypowiedź w tym regionie.

![Testowanie wsadowe](./media/luis-concept-batch-test/batch-testing.png)

## <a name="errors-in-the-results"></a>Błędy w wynikach

Błędy w teście wsadowym wskazują intencje, które nie są przewidywane w pliku wsadowym. Błędy są wskazane w dwóch czerwonych sekcjach wykresu.

Sekcja fałszywych wartości dodatnich wskazuje, że element wypowiedź jest zgodny z intencją lub jednostką, gdy nie powinien. Fałszywa wartość ujemna wskazuje, że element wypowiedź nie był zgodny z zamiarem lub jednostką, gdy powinien.

## <a name="fixing-batch-errors"></a>Naprawianie błędów partii

Jeśli wystąpią błędy w testach wsadowych, można dodać więcej wyrażenia długości do zamiaru i/lub podawać więcej wyrażenia długości z jednostką, aby pomóc LUIS dyskryminacji między intencjami. Po dodaniu wyrażenia długości i oznaczeniu ich, a mimo to w testowaniu wsadowym nadal pojawiają się błędy przewidywania, rozważ dodanie funkcji [listy fraz](luis-concept-feature.md) z słownictwem specyficznym dla domeny, aby ułatwić Luis szybsze uczenie się.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [przetestować partię](luis-how-to-batch-test.md)
