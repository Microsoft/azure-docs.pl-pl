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
ms.openlocfilehash: ef9cb083c9bbe6eae5c34cd3799debde771231b6
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100558201"
---
# <a name="correct-misspelled-words-with-bing-resource"></a>Poprawianie błędnie napisanych wyrazów za pomocą zasobu Bing

Interfejs API przewidywania v3 obsługuje teraz [interfejs API sprawdzania pisowni usługi Bing](https://docs.microsoft.com/bing/search-apis/bing-spell-check/overview). Dodaj sprawdzanie pisowni do swojej aplikacji, dołączając klucz do zasobu wyszukiwania Bing w nagłówku swoich żądań. Możesz użyć istniejącego zasobu Bing, jeśli jest już własny, lub [utworzyć nowy](https://portal.azure.com/#create/Microsoft.BingSearch) , aby użyć tej funkcji. 

Przykład danych wyjściowych przewidywania dla błędnego zapytania:

```json
{
  "query": "bouk me a fliht to kayro",
  "prediction": {
    "alteredQuery": "book me a flight to cairo",
    "topIntent": "book a flight",
    "intents": {
      "book a flight": {
        "score": 0.9480589
      }
      "None": {
        "score": 0.0332136229
      }
    },
    "entities": {}
  }
}
```

Poprawki pisowni są wprowadzane przed przewidywaniem LUIS użytkownika wypowiedź. W odpowiedzi można zobaczyć zmiany w oryginalnym wypowiedź, w tym pisownię.

## <a name="create-bing-search-resource"></a>Utwórz zasób Wyszukiwanie Bing

Aby utworzyć zasób Wyszukiwanie Bing w Azure Portal, wykonaj następujące instrukcje:

1. Zaloguj się do [Azure Portal](https://portal.azure.com).

2. Wybierz pozycję **Utwórz zasób** w lewym górnym rogu.

3. W polu wyszukiwania wprowadź `Bing Search V7` i wybierz usługę.

4. Panel Informacje pojawia się z prawej strony zawierającej informacje, takie jak Uwaga prawna. Wybierz pozycję **Utwórz** , aby rozpocząć proces tworzenia subskrypcji.

> [!div class="mx-imgBorder"]
> ![Zasób sprawdzanie pisowni Bing API wersji 7](./media/luis-tutorial-bing-spellcheck/bing-search-resource-portal.png)

5. W następnym panelu wprowadź ustawienia usługi. Poczekaj na zakończenie procesu tworzenia usługi.

6. Po utworzeniu zasobu przejdź do bloku **klucze i punkt końcowy** po lewej stronie. 

7. Skopiuj jeden z kluczy do dodania do nagłówka żądania przewidywania. Potrzebny będzie tylko jeden z tych dwóch kluczy.

<!--
## Using the key in LUIS test panel
There are two places in LUIS to use the key. The first is in the [test panel](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key isn't saved into LUIS but instead is a session variable. You need to set the key every time you want the test panel to apply the Bing Spell Check API v7 service to the utterance. See [instructions](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) in the test panel for setting the key.
-->
## <a name="enable-spell-check-from-ui"></a>Włącz sprawdzanie pisowni z poziomu interfejsu użytkownika 
Możesz włączyć sprawdzanie pisowni dla przykładowego zapytania przy użyciu [portalu Luis](https://www.luis.ai). Wybierz pozycję **Zarządzaj** w górnej części ekranu i **zasoby platformy Azure** w lewym obszarze nawigacji. Po skojarzeniu zasobu przewidywania z aplikacją można wybrać pozycję **Zmień parametry zapytania** w dolnej części strony i wkleić klucz zasobu w polu **Włącz sprawdzanie pisowni** .
    
   > [!div class="mx-imgBorder"]
   > ![Włącz sprawdzanie pisowni](./media/luis-tutorial-bing-spellcheck/spellcheck-query-params.png)


## <a name="adding-the-key-to-the-endpoint-url"></a>Dodawanie klucza do adresu URL punktu końcowego
Dla każdego zapytania, dla którego ma zostać zastosowana korekta pisowni, zapytanie punktu końcowego wymaga klucza zasobu sprawdzania pisowni usługi Bing przesłanego w parametrze nagłówka zapytania. Może być chatbot, który wywołuje LUIS, lub może bezpośrednio wywołać interfejs API punktu końcowego LUIS. Niezależnie od tego, jak jest wywoływany punkt końcowy, każdy i każde wywołanie musi zawierać wymagane informacje w żądaniu w nagłówku, aby poprawki pisowni działały prawidłowo. Należy ustawić wartość klucza **MKT-Bing-pisownia-Check-Key** .

|Klucz nagłówka|Wartość nagłówka|
|--|--|
|`mkt-bing-spell-check-key`|Klucze Znalezione w bloku **klucze i punkt końcowy** zasobu|

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
