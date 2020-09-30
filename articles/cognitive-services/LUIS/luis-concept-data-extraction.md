---
title: Wyodrębnianie danych — LUIS
description: Wyodrębnij dane z wypowiedź tekstu z intencjami i jednostkami. Dowiedz się, jakiego rodzaju dane mogą zostać wyodrębnione z Language Understanding (LUIS).
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 9bcc3d08fa29109cf4178f8eb0c3efe661323ef0
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91541785"
---
# <a name="extract-data-from-utterance-text-with-intents-and-entities"></a>Wyodrębnij dane z wypowiedź tekstu z intencjami i jednostkami
LUIS daje możliwość uzyskiwania informacji z wyrażenia długości języka naturalnego użytkownika. Informacje są wyodrębniane w taki sposób, aby mogły być używane przez program, aplikację lub czat bot. W poniższych sekcjach dowiesz się, jakie dane są zwracane z intencji i jednostek z przykładami JSON.

Najtrudniejsze dane do wyodrębnienia to dane uczenia maszynowego, ponieważ nie jest to dokładne dopasowanie tekstu. Wyodrębnianie danych [jednostek](luis-concept-entity-types.md) uczenia maszynowego musi być częścią [cyklu tworzenia](luis-concept-app-iteration.md) , dopóki nie uzyskasz pewności, że otrzymasz oczekiwane dane.

## <a name="data-location-and-key-usage"></a>Lokalizacja danych i użycie klucza
LUIS wyodrębnia dane z wypowiedź użytkownika w opublikowanym [punkcie końcowym](luis-glossary.md#endpoint). **Żądanie https** (post lub Get) zawiera wypowiedź, a także niektóre opcjonalne konfiguracje, takie jak środowiska przejściowe lub produkcyjne.

**Żądanie punktu końcowego przewidywania wersji 2**

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

**Żądanie punktu końcowego przewidywania v3**

`https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/<appID>/slots/<slot-type>/predict?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&query=book 2 tickets to paris`

`appID`Jest ona dostępna na stronie **Ustawienia** aplikacji Luis, a także jako część adresu URL (po `/apps/` ) podczas edytowania tej aplikacji Luis. `subscription-key`Jest to klucz punktu końcowego używany do wykonywania zapytań dotyczących aplikacji. Chociaż możesz użyć bezpłatnego klucza autorstwa/początkowego podczas uczenia się LUIS, ważne jest, aby zmienić klucz punktu końcowego na klucz, który obsługuje [oczekiwane użycie Luis](luis-limits.md#key-limits). `timezoneOffset`Jednostka jest minut.

**Odpowiedź https** zawiera wszystkie zamierzenia i informacje o jednostce, które Luis mogą ustalić w oparciu o aktualnie opublikowany model punktu końcowego lub produkcyjnego. Adres URL punktu końcowego znajduje się w witrynie sieci Web [Luis](luis-reference-regions.md) w sekcji **Zarządzanie** na stronie **klucze i punkty końcowe** .

## <a name="data-from-intents"></a>Dane z intencji
Dane podstawowe są największą **nazwą przeznaczenie**oceniania. Odpowiedź punktu końcowego to:

#### <a name="v2-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania wersji 2](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania v3](#tab/V3)

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

Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).

* * *

|Obiekt danych|Typ danych|Lokalizacja danych|Wartość|
|--|--|--|--|
|Zamiar|Ciąg|topScoringIntent. intencja|"GetStoreInfo"|

Jeśli aplikacja chatbot lub LUIS-wywołująca podejmuje decyzję na podstawie więcej niż jednego wyniku zamiaru, zwracają wszystkie wyniki założeń.


#### <a name="v2-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania wersji 2](#tab/V2)

Ustaw parametr QueryString, `verbose=true` . Odpowiedź punktu końcowego to:

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

#### <a name="v3-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania v3](#tab/V3)

Ustaw parametr QueryString, `show-all-intents=true` . Odpowiedź punktu końcowego to:

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

Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).

* * *

Intencje są uporządkowane od najwyższego do najniższego wyniku.

|Obiekt danych|Typ danych|Lokalizacja danych|Wartość|Wynik|
|--|--|--|--|:--|
|Zamiar|Ciąg|intencje [0]. cel|"GetStoreInfo"|0,984749258|
|Zamiar|Ciąg|intencje [1]. cel|Dawaj|0,0168218873|

W przypadku dodania wstępnie skompilowanych domen nazwa zamierzenia wskazuje domenę, taką jak `Utilties` lub, `Communication` a także zamiar:

#### <a name="v2-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania wersji 2](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania v3](#tab/V3)

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

Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).

* * *

|Obszar|Obiekt danych|Typ danych|Lokalizacja danych|Wartość|
|--|--|--|--|--|
|Narzędzia|Zamiar|Ciąg|intencje [0]. cel|"<b>Narzędzia</b>. ShowNext"|
|Komunikacja|Zamiar|Ciąg|intencje [1]. cel|<b>Komunikacja</b>. StartOver"|
||Zamiar|Ciąg|intencje [2]. cel|Dawaj|


## <a name="data-from-entities"></a>Dane z jednostek
Większość botów rozmowy i aplikacji musi być większa niż nazwa celu. Te dodatkowe dane opcjonalne pochodzą z jednostek odnalezionych w wypowiedź. Każdy typ jednostki zwraca różne informacje o dopasowaniu.

Pojedyncze słowo lub fraza w wypowiedź może być zgodna z więcej niż jedną jednostką. W takim przypadku każda pasująca jednostka jest zwracana z wynikiem.

Wszystkie jednostki są zwracane w tablicy **jednostek** odpowiedzi z punktu końcowego

## <a name="tokenized-entity-returned"></a>Zwrócono token jednostki

Zapoznaj się z [pomocą techniczną tokenu](luis-language-support.md#tokenization) w Luis.


## <a name="prebuilt-entity-data"></a>Wstępnie utworzone dane jednostki
Wstępnie [skompilowane](luis-concept-entity-types.md) jednostki są odnajdywane na podstawie wyrażenia regularnego zgodnego z rozpoznawaniem typu "Open Source [" — projektem tekstu](https://github.com/Microsoft/Recognizers-Text) . Wstępnie skompilowane jednostki są zwracane w tablicy jednostek i używają nazwy typu poprzedzonej prefiksem `builtin::` .

## <a name="list-entity-data"></a>Wyświetl dane jednostki

[Jednostki listy](reference-entity-list.md) reprezentują stały, zamknięty zestaw powiązanych słów wraz z ich synonimami. LUIS nie odnajduje dodatkowych wartości dla jednostek listy. Użyj opcji **zalecamy** , aby zobaczyć sugestie dotyczące nowych słów na podstawie bieżącej listy. Jeśli istnieje więcej niż jedna jednostka listy o tej samej wartości, każda jednostka zostanie zwrócona w zapytaniu punktu końcowego.

## <a name="regular-expression-entity-data"></a>Dane jednostki wyrażenia regularnego

[Jednostka wyrażenia regularnego](reference-entity-regular-expression.md) wyodrębnia jednostkę na podstawie podania wyrażenia regularnego.

## <a name="extracting-names"></a>Wyodrębnianie nazw
Pobieranie nazw z wypowiedź jest trudne, ponieważ nazwa może być niemal dowolną kombinacją liter i wyrazów. W zależności od typu wyodrębnianych nazw można korzystać z kilku opcji. Poniższe sugestie nie są regułami, ale są bardziej szczegółowe.

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>Dodawanie wstępnie utworzonych jednostek PersonName i GeographyV2

Jednostki [PersonName](luis-reference-prebuilt-person.md) i [GeographyV2](luis-reference-prebuilt-geographyV2.md) są dostępne w niektórych [kulturach językowych](luis-reference-prebuilt-entities.md).

### <a name="names-of-people"></a>Nazwy osób

Nazwa osoby może mieć nieco niewielki format w zależności od języka i kultury. Użyj wstępnie utworzonej jednostki **[PersonName](luis-reference-prebuilt-person.md)** lub **[prostej jednostki](luis-concept-entity-types.md#simple-entity)** z [rolami](luis-concept-roles.md) imię i nazwisko.

Jeśli używasz prostej jednostki, upewnij się, że są podane przykłady, które używają pierwszej i ostatniej nazwy w różnych częściach wypowiedź, w wyrażenia długości o różnej długości i wyrażenia długości we wszystkich intencjach, w tym dla opcji Brak. Regularnie [Przeglądaj](luis-how-to-review-endoint-utt.md) wyrażenia długości punktu końcowego, aby oznaczyć wszystkie nazwy, które nie zostały prawidłowo przewidywalne.

### <a name="names-of-places"></a>Nazwy miejsc

Nazwy lokalizacji są ustawiane i znane, takie jak miasta, powiaty, Stany, prowincje i kraje/regiony. Aby wyodrębnić informacje o lokalizacji, Użyj wstępnie skompilowanej jednostki **[geographyV2](luis-reference-prebuilt-geographyv2.md)** .

### <a name="new-and-emerging-names"></a>Nowe i pojawiające się nazwy

Niektóre aplikacje muszą być w stanie znaleźć nowe i pojawiające się nazwy, takie jak produkty lub firmy. Te typy nazw są najbardziej trudnym typem wyodrębniania danych. Zacznij od **[prostej jednostki](luis-concept-entity-types.md#simple-entity)** i Dodaj [listę fraz](luis-concept-feature.md). Regularnie [Przeglądaj](luis-how-to-review-endoint-utt.md) wyrażenia długości punktu końcowego, aby oznaczyć wszystkie nazwy, które nie zostały prawidłowo przewidywalne.

## <a name="patternany-entity-data"></a>Wzorzec. wszystkie dane jednostki

[Wzorzec. any](reference-entity-pattern-any.md) jest symbolem zastępczym o zmiennej długości używanym tylko w szablonie wzorca wypowiedź, aby oznaczyć, gdzie rozpoczyna się i kończą. Aby można było zastosować wzorzec, należy znaleźć jednostkę używaną we wzorcu.

## <a name="sentiment-analysis"></a>Analiza tonacji
Jeśli podczas [publikowania](luis-how-to-publish-app.md#sentiment-analysis)tonacji analiza jest skonfigurowana, odpowiedź JSON Luis zawiera analizę tonacji. Dowiedz się więcej o analizie tonacji w dokumentacji [Analiza tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) .

## <a name="key-phrase-extraction-entity-data"></a>Dane jednostki wyodrębniania klucza
[Jednostka wyodrębniania klucza](luis-reference-prebuilt-keyphrase.md) zwraca kluczowe frazy w wypowiedź, dostarczone przez [Analiza tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/).

## <a name="data-matching-multiple-entities"></a>Dane pasujące do wielu jednostek

LUIS zwraca wszystkie jednostki odnalezione w wypowiedź. W związku z tym czat bot może wymagać podjęcia decyzji na podstawie wyników.

## <a name="data-matching-multiple-list-entities"></a>Dane zgodne z wieloma jednostkami listy

Jeśli słowo lub fraza pasuje do więcej niż jednej jednostki listy, zapytanie punktu końcowego zwróci każdą jednostkę listy.

Dla zapytania `when is the best time to go to red rock?` , a aplikacja zawiera słowo `red` w więcej niż jednej liście, Luis rozpoznaje wszystkie jednostki i zwraca tablicę jednostek jako część odpowiedzi punktu końcowego JSON.

## <a name="next-steps"></a>Następne kroki

Zobacz [Dodawanie jednostek](luis-how-to-add-entities.md) , aby dowiedzieć się więcej na temat dodawania jednostek do aplikacji Luis.
