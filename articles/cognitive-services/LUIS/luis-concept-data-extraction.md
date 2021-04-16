---
title: Wyodrębnianie danych — USŁUGA LUIS
description: Wyodrębnianie danych z tekstu wypowiedzi za pomocą intencji i jednostek. Dowiedz się, jakiego rodzaju dane można wyodrębnić z Language Understanding (LUIS).
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/13/2021
ms.openlocfilehash: dd7d113b1c23a0afec82a346e0f7baa1254ebbed
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107500145"
---
# <a name="extract-data-from-utterance-text-with-intents-and-entities"></a>Wyodrębnianie danych z tekstu wypowiedzi za pomocą intencji i jednostek
Usługa LUIS umożliwia uzyskiwanie informacji z wypowiedzi użytkownika w języku naturalnym. Informacje są wyodrębnione w taki sposób, że mogą być używane przez program, aplikację lub czatbota do podjęcia działania. W poniższych sekcjach dowiesz się, jakie dane są zwracane z intencji i jednostek, z przykładami kodu JSON.

Najtrudniejszymi danymi do wyodrębnienia są dane uczenia maszynowego, ponieważ nie są dokładnie zgodne z tekstem. Wyodrębnianie danych jednostek [](luis-concept-entity-types.md) uczenia maszynowego musi [](luis-concept-app-iteration.md) być częścią cyklu tworzenia, dopóki nie masz pewności, że otrzymujesz dane, których oczekujesz.

## <a name="data-location-and-key-usage"></a>Lokalizacja danych i użycie klucza
Usługa LUIS wyodrębnia dane z wypowiedzi użytkownika w opublikowanym punkcie [końcowym](luis-glossary.md#endpoint). Żądanie **HTTPS** (POST lub GET) zawiera wypowiedź oraz niektóre opcjonalne konfiguracje, takie jak środowiska przejściowe lub produkcyjne.

**Żądanie punktu końcowego przewidywania w wersji 2**

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

**Żądanie punktu końcowego przewidywania w wersji 3**

`https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/<appID>/slots/<slot-type>/predict?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&query=book 2 tickets to paris`

Element jest dostępny na stronie Ustawienia aplikacji USŁUGI LUIS, a także jako część adresu URL (po pliku ) podczas edytowania `appID` tej aplikacji usługi  `/apps/` LUIS. to `subscription-key` klucz punktu końcowego używany do wykonywania zapytań w aplikacji. Możesz użyć bezpłatnego klucza tworzenia/klucza początkowego podczas nauki usługi LUIS, ale ważne jest, aby zmienić klucz punktu końcowego na klucz, który obsługuje oczekiwane [użycie usługi LUIS.](luis-limits.md#key-limits) Jednostka `timezoneOffset` to minuty.

Odpowiedź **HTTPS zawiera** wszystkie informacje o intencji i jednostce, które usługa LUIS może określić na podstawie bieżącego opublikowanego modelu przejściowego lub produkcyjnego punktu końcowego. Adres URL punktu końcowego znajduje się w witrynie internetowej [usługi LUIS](luis-reference-regions.md) w **sekcji Zarządzanie** na stronie Klucze **i punkty** końcowe.

## <a name="data-from-intents"></a>Dane z intencji
Dane podstawowe to nazwa najważniejszej **intencji oceniania**. Odpowiedź punktu końcowego to:

#### <a name="v2-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania w wersji 2](#tab/V2)

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania w wersji 3](#tab/V3)

```JSON
{
  "query": "when do you open next?",
  "prediction": {
    "normalizedQuery": "when do you open next?",
    "topIntent": "GetStoreInfo",
    "intents": {
        "GetStoreInfo": {
            "score": 0.984749258
        }
    }
  },
  "entities": []
}
```

Dowiedz się więcej o punkcie [końcowym przewidywania w wersji 3.](luis-migration-api-v3.md)

* * *

|Obiekt danych|Typ danych|Lokalizacja danych|Wartość|
|--|--|--|--|
|Zamiar|Ciąg|topScoringIntent.intent|"GetStoreInfo"|

Jeśli twój czatbot lub aplikacja wywołująca usługę LUIS podejmuje decyzję na podstawie więcej niż jednego wyniku intencji, zwróć wyniki wszystkich intencji.


#### <a name="v2-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania w wersji 2](#tab/V2)

Ustaw parametr querystring , `verbose=true` . Odpowiedź punktu końcowego to:

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "intents": [
    {
      "intent": "GetStoreInfo",
      "score": 0.984749258
    },
    {
      "intent": "None",
      "score": 0.2040639
    }
  ],
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania w wersji 3](#tab/V3)

Ustaw parametr querystring , `show-all-intents=true` . Odpowiedź punktu końcowego to:

```JSON
{
    "query": "when do you open next?",
    "prediction": {
        "normalizedQuery": "when do you open next?",
        "topIntent": "GetStoreInfo",
        "intents": {
            "GetStoreInfo": {
                "score": 0.984749258
            },
            "None": {
                 "score": 0.2040639
            }
        },
        "entities": {
        }
    }
}
```

Dowiedz się więcej o punkcie [końcowym przewidywania w wersji 3.](luis-migration-api-v3.md)

* * *

Intencje są uporządkowane od najwyższego do najniższego wyniku.

|Obiekt danych|Typ danych|Lokalizacja danych|Wartość|Wynik|
|--|--|--|--|:--|
|Zamiar|Ciąg|intents[0].intent|"GetStoreInfo"|0.984749258|
|Zamiar|Ciąg|intents[1].intent|"Brak"|0.0168218873|

Jeśli dodasz wstępnie utworzone domeny, nazwa intencji wskazuje domenę, taką jak lub , `Utilties` `Communication` a także intencję:

#### <a name="v2-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania w wersji 2](#tab/V2)

```JSON
{
  "query": "Turn on the lights next monday at 9am",
  "topScoringIntent": {
    "intent": "Utilities.ShowNext",
    "score": 0.07842206
  },
  "intents": [
    {
      "intent": "Utilities.ShowNext",
      "score": 0.07842206
    },
    {
      "intent": "Communication.StartOver",
      "score": 0.0239675418
    },
    {
      "intent": "None",
      "score": 0.0168218873
    }],
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania w wersji 3](#tab/V3)

```JSON
{
    "query": "Turn on the lights next monday at 9am",
    "prediction": {
        "normalizedQuery": "Turn on the lights next monday at 9am",
        "topIntent": "Utilities.ShowNext",
        "intents": {
            "Utilities.ShowNext": {
                "score": 0.07842206
            },
            "Communication.StartOver": {
                "score": 0.0239675418
            },
            "None": {
                "score": 0.00085447653
            }
        },
        "entities": []
    }
}
```

Dowiedz się więcej o punkcie [końcowym przewidywania w wersji 3.](luis-migration-api-v3.md)

* * *

|Domena|Obiekt danych|Typ danych|Lokalizacja danych|Wartość|
|--|--|--|--|--|
|Narzędzia|Zamiar|Ciąg|intents[0].intent|"<b>Narzędzia</b>. ShowNext"|
|Komunikacja|Zamiar|Ciąg|intents[1].intent|<b>Komunikacja</b>. StartOver"|
||Zamiar|Ciąg|intents[2].intent|"Brak"|


## <a name="data-from-entities"></a>Dane z jednostek
Większość czatbotów i aplikacji potrzebuje więcej niż nazwa intencji. Te dodatkowe, opcjonalne dane pochodzą z jednostek odnalezionych w wypowiedzi. Każdy typ jednostki zwraca różne informacje na temat dopasowania.

Pojedyncze słowo lub fraza w wypowiedzi może odpowiadać więcej niż jednej jednostce. W takim przypadku każda pasująca jednostka jest zwracana wraz z jej wynikiem.

Wszystkie jednostki są zwracane w tablicy **jednostek** odpowiedzi z punktu końcowego

## <a name="tokenized-entity-returned"></a>Zwrócona jednostka tokenizowana

Zapoznaj się z [obsługą tokenów w](luis-language-support.md#tokenization) u usługi LUIS.


## <a name="prebuilt-entity-data"></a>Wstępnie utworzone dane jednostki
[Wstępnie utworzone jednostki są](luis-concept-entity-types.md) odnalezione na podstawie dopasowania wyrażenia regularnego przy użyciu projektu [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) typu open source. Wstępnie utworzone jednostki są zwracane w tablicy jednostek i używają nazwy typu poprzedzonej prefiksem `builtin::` .

## <a name="list-entity-data"></a>Lista danych jednostki

[Jednostki listy](reference-entity-list.md) reprezentują stały, zamknięty zestaw powiązanych wyrazów wraz z ich synonimami. Usługa LUIS nie odnajduje dodatkowych wartości dla jednostek listy. Funkcja **Rekomendacja** umożliwia wyświetlanie sugestii dotyczących nowych wyrazów na podstawie bieżącej listy. Jeśli istnieje więcej niż jedna jednostka listy o tej samej wartości, każda jednostka jest zwracana w zapytaniu punktu końcowego.

## <a name="regular-expression-entity-data"></a>Dane jednostki wyrażenia regularnego

Jednostka [wyrażenia regularnego](reference-entity-regular-expression.md) wyodrębnia jednostkę na podstawie pou zapewnianych wyrażeń regularnych.

## <a name="extracting-names"></a>Wyodrębnianie nazw
Pobieranie nazw z wypowiedzi jest trudne, ponieważ nazwa może być niemal dowolną kombinacją liter i słów. W zależności od typu wyodrębnianych nazw masz kilka opcji. Poniższe sugestie nie są regułami, ale większej liczby wytycznych.

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>Dodawanie wstępnie utworzonych jednostek PersonName i GeographyV2

[Jednostki PersonName](luis-reference-prebuilt-person.md) [i GeographyV2](luis-reference-prebuilt-geographyV2.md) są dostępne w [niektórych kulturach językowych.](luis-reference-prebuilt-entities.md)

### <a name="names-of-people"></a>Nazwiska osób

Nazwy osób mogą mieć niewielki format w zależności od języka i kultury. Użyj wstępnie utworzonej jednostki **[personName lub](luis-reference-prebuilt-person.md)** prostej **[jednostki](luis-concept-entity-types.md)** z rolami imienia i nazwiska.

Jeśli używasz prostej jednostki, pamiętaj, aby podać przykłady, które używają imienia i nazwiska w różnych częściach wypowiedzi, w wypowiedziach o różnych długościach i wypowiedziach we wszystkich intencjach, w tym intencji None. [Regularnie](./luis-how-to-review-endpoint-utterances.md) sprawdzaj wypowiedzi punktu końcowego, aby oznaczać etykietami nazwy, które nie zostały prawidłowo przewidywane.

### <a name="names-of-places"></a>Nazwy miejsc

Nazwy lokalizacji są ustawione i znane, na przykład miasta, hrabstwa, województwa i kraje/regiony. Wyodrębnij informacje o lokalizacji za pomocą wstępnie utworzonej **[jednostki geographyV2.](luis-reference-prebuilt-geographyv2.md)**

### <a name="new-and-emerging-names"></a>Nowe i nowe nazwy

Niektóre aplikacje muszą mieć możliwość znalezienia nowych i pojawiających się nazw, takich jak produkty lub firmy. Te typy nazw są najtrudniejszym typem wyodrębniania danych. Zacznij od prostej **[jednostki i](luis-concept-entity-types.md)** dodaj listę [fraz](luis-concept-feature.md). [Regularnie](./luis-how-to-review-endpoint-utterances.md) sprawdzaj wypowiedzi punktu końcowego, aby oznaczać etykietami nazwy, które nie zostały prawidłowo przewidywane.

## <a name="patternany-entity-data"></a>Dane jednostki Pattern.any

[Pattern.any](reference-entity-pattern-any.md) to symbol zastępczy o zmiennej długości używany tylko w wypowiedzi szablonu wzorca do oznaczania, gdzie rozpoczyna się i kończy jednostka. Aby można było zastosować wzorzec, należy znaleźć jednostkę używaną we wzorcu.

## <a name="sentiment-analysis"></a>Analiza tonacji
Jeśli podczas publikowania [](luis-how-to-publish-app.md#sentiment-analysis)skonfigurowano analizę tonacji, odpowiedź JSON usługi LUIS obejmuje analizę tonacji. Dowiedz się więcej na temat analizy tonacji [w analiza tekstu](../text-analytics/index.yml) dokumentacji.

## <a name="key-phrase-extraction-entity-data"></a>Dane jednostki wyodrębniania kluczowych fraz
Jednostka [wyodrębniania kluczowych fraz](luis-reference-prebuilt-keyphrase.md) zwraca kluczowe frazy w wypowiedzi dostarczonej przez [analiza tekstu](../text-analytics/index.yml).

## <a name="data-matching-multiple-entities"></a>Dane pasujące do wielu jednostek

Usługa LUIS zwraca wszystkie jednostki odnalezione w wypowiedzi. W związku z tym czatbot może wymagać podjęcia decyzji na podstawie wyników.

## <a name="data-matching-multiple-list-entities"></a>Dane pasujące do wielu jednostek listy

Jeśli wyraz lub fraza pasuje do więcej niż jednej jednostki listy, zapytanie punktu końcowego zwraca każdą jednostkę List.

W przypadku zapytania , a aplikacja zawiera wyraz na więcej niż jednej liście, usługa LUIS rozpoznaje wszystkie jednostki i zwraca tablicę jednostek w ramach odpowiedzi punktu końcowego `when is the best time to go to red rock?` `red` JSON.

## <a name="next-steps"></a>Następne kroki

Zobacz [Dodawanie jednostek,](luis-how-to-add-entities.md) aby dowiedzieć się więcej na temat sposobu dodawania jednostek do aplikacji usługi LUIS.
