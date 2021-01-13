---
title: Popraw błędne słowa — LUIS
titleSuffix: Azure Cognitive Services
description: Popraw błędne słowa w wyrażenia długości przez dodanie sprawdzanie pisowni Bing API wersji 7 do zapytań LUIS Endpoint.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 01/12/2021
ms.openlocfilehash: f416fe8ef4f6e89d07e6065d4c9435642d9bacb9
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/13/2021
ms.locfileid: "98179643"
---
# <a name="correct-misspelled-words-with-bing-search-resource"></a>Poprawianie błędnie napisanych wyrazów za pomocą zasobu Wyszukiwanie Bing

Możesz zintegrować swoją aplikację LUIS z [wyszukiwanie Bing](https://ms.portal.azure.com/#create/Microsoft.BingSearch) , aby poprawić błędne słowa w wyrażenia długości, zanim Luis przeanalizuje wynik i jednostki wypowiedź.

## <a name="create-endpoint-key"></a>Utwórz klucz punktu końcowego

Aby utworzyć zasób Wyszukiwanie Bing w Azure Portal, wykonaj następujące instrukcje:

1. Zaloguj się do [Azure Portal](https://portal.azure.com).

2. Wybierz pozycję **Utwórz zasób** w lewym górnym rogu.

3. W polu wyszukiwania wprowadź `Bing Search V7` i wybierz usługę.

4. Panel Informacje pojawia się z prawej strony zawierającej informacje, takie jak Uwaga prawna. Wybierz pozycję **Utwórz** , aby rozpocząć proces tworzenia subskrypcji.

    :::image type="content" source="./media/luis-tutorial-bing-spellcheck/bing-search-resource-portal.png" alt-text="Zasób sprawdzanie pisowni Bing API wersji 7":::

5. W następnym panelu wprowadź ustawienia usługi. Poczekaj na zakończenie procesu tworzenia usługi.

6. Po utworzeniu zasobu przejdź do bloku **klucze i punkt końcowy** po lewej stronie. 

7. Skopiuj jeden z kluczy do dodania do nagłówka żądania przewidywania. Potrzebny będzie tylko jeden z tych dwóch kluczy.

8. Dodaj klucz do `mkt-bing-spell-check-key` w nagłówku żądania predykcyjnego.

<!--
## Using the key in LUIS test panel
There are two places in LUIS to use the key. The first is in the [test panel](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key isn't saved into LUIS but instead is a session variable. You need to set the key every time you want the test panel to apply the Bing Spell Check API v7 service to the utterance. See [instructions](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) in the test panel for setting the key.
-->
## <a name="adding-the-key-to-the-endpoint-url"></a>Dodawanie klucza do adresu URL punktu końcowego
Dla każdego zapytania, dla którego ma zostać zastosowana korekta pisowni, zapytanie punktu końcowego wymaga klucza zasobu sprawdzania pisowni usługi Bing przesłanego w parametrze nagłówka zapytania. Może być chatbot, który wywołuje LUIS, lub może bezpośrednio wywołać interfejs API punktu końcowego LUIS. Niezależnie od tego, jak jest wywoływany punkt końcowy, każdy i każde wywołanie musi zawierać wymagane informacje w żądaniu w nagłówku, aby poprawki pisowni działały prawidłowo. Należy ustawić wartość klucza **MKT-Bing-pisownia-Check-Key** .


## <a name="send-misspelled-utterance-to-luis"></a>Wyślij błędnie wpisane wypowiedź do LUIS
1. Dodaj błędnie wpisane wypowiedź do zapytania predykcyjnego, które będzie wysyłane na przykład "jak daleko jest mountainn?". W języku angielskim, `mountain` , z jednym `n` , jest poprawną pisownią.

2. LUIS reaguje na wynik JSON dla `How far is the mountain?` . Jeśli sprawdzanie pisowni Bing API wersji 7 wykrywa błąd pisowni, `query` pole w odpowiedzi JSON aplikacji Luis zawiera oryginalne zapytanie, a `alteredQuery` pole zawiera poprawione zapytanie wysyłane do Luis.

```json
{
  "query": "How far is the mountainn?",
  "alteredQuery": "How far is the mountain?",
  "topScoringIntent": {
    "intent": "Concierge",
    "score": 0.183866
  },
  "entities": []
}
```

## <a name="ignore-spelling-mistakes"></a>Ignoruj błędy pisowni

Jeśli nie chcesz używać Wyszukiwanie Bing API wersji 7, musisz dodać poprawną i niepoprawną pisownię.

Dwa rozwiązania:

* Etykieta przykład wyrażenia długości, która ma wszystkie inne pisownie, dzięki czemu LUIS może poznać poprawną pisownię oraz literówki. Ta opcja wymaga więcej nakładów na etykietowanie niż przy użyciu modułu sprawdzania pisowni.
* Utwórz listę fraz ze wszystkimi odmianami wyrazu. W tym rozwiązaniu nie trzeba etykietować odmian wyrazów w przykładzie wyrażenia długości.


> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat przykładu wyrażenia długości](./luis-how-to-add-entities.md)
