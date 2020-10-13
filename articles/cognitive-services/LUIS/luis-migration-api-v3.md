---
title: Zmiany przewidywanych punktów końcowych w interfejsie API v3
description: Interfejsy API funkcji przewidywania zapytań w wersji 3 zostały zmienione. Skorzystaj z tego przewodnika, aby zrozumieć, jak przeprowadzić migrację do interfejsów API punktu końcowego w wersji 3.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: a5760db2d6e453d631680d6154e6d9a03ce55cd6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541343"
---
# <a name="prediction-endpoint-changes-for-v3"></a>Zmiany przewidywanych punktów końcowych dla wersji 3

Interfejsy API funkcji przewidywania zapytań w wersji 3 zostały zmienione. Skorzystaj z tego przewodnika, aby zrozumieć, jak przeprowadzić migrację do interfejsów API punktu końcowego w wersji 3.

**Ogólnie dostępny stan** — ten interfejs API v3 obejmuje znaczące zmiany żądania JSON i odpowiedzi z interfejsu API v2.

Interfejs API v3 udostępnia następujące nowe funkcje:

* [Jednostki zewnętrzne](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)
* [Listy dynamiczne](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Wstępnie skompilowane zmiany JSON jednostki](#prebuilt-entity-changes)

[Żądanie](#request-changes) i [odpowiedź](#response-changes) punktu końcowego przewidywania mają znaczące zmiany, aby obsługiwać nowe funkcje wymienione powyżej, w tym następujące:

* [Zmiany obiektu odpowiedzi](#top-level-json-changes)
* [Odwołania do nazwy roli jednostki zamiast nazwy jednostki](#entity-role-name-instead-of-entity-name)
* [Właściwości do oznaczania jednostek w wyrażenia długości](#marking-placement-of-entities-in-utterances)

[Dokumentacja referencyjna](https://aka.ms/luis-api-v3) jest dostępna w wersji 3.

## <a name="v3-changes-from-preview-to-ga"></a>Wersja 3 zmian z wersji zapoznawczej na GA

Wersja 3 wprowadziła następujące zmiany w ramach przechodzenia do wersji GA:

* Następujące wbudowane jednostki mają różne odpowiedzi JSON:
    * [OrdinalV1](luis-reference-prebuilt-ordinal.md)
    * [GeographyV2](luis-reference-prebuilt-geographyv2.md)
    * [DatetimeV2](luis-reference-prebuilt-datetimev2.md)
    * Wymierna nazwa klucza jednostki z `units` do `unit`

* Zmiana JSON treści żądania:
    * od `preferExternalEntities` do `preferExternalEntities`
    * opcjonalny `score` parametr dla jednostek zewnętrznych

* Zmiany JSON treści odpowiedzi:
    * `normalizedQuery` usunięte

## <a name="suggested-adoption-strategy"></a>Sugerowana strategia wdrażania

Jeśli używasz platformy bot Framework, sprawdzanie pisowni Bing wersji 7 lub chcesz migrować tylko tworzenie aplikacji LUIS, Kontynuuj korzystanie z punktu końcowego v2.

Jeśli nie znasz żadnej aplikacji klienckiej ani integracji (bot Framework i sprawdzanie pisowni Bing wersji 7), to na pewno chcesz migrować Tworzenie aplikacji LUIS i punkt końcowy przewidywania w tym samym czasie, zacznij korzystać z punktu końcowego przewidywania v3. Punkt końcowy przewidywania w wersji 2 nadal będzie dostępny i jest to dobra strategia obniżenia poziomu.


## <a name="not-supported"></a>Nieobsługiwane

### <a name="bing-spell-check"></a>Sprawdzanie pisowni Bing

Ten interfejs API nie jest obsługiwany w punkcie końcowym przewidywania v3 — Kontynuuj korzystanie z punktu końcowego przewidywania interfejsu API V2 na potrzeby poprawek pisowni. Jeśli wymagana jest Korekcja pisowni podczas korzystania z interfejsu API v3, aplikacja kliencka może wywołać interfejs API [Sprawdzanie pisowni Bing](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/overview) i zmienić tekst na poprawną pisownię przed wysłaniem tekstu do interfejsu API Luis.

## <a name="bot-framework-and-azure-bot-service-client-applications"></a>Bot Framework i Azure Bot Service aplikacje klienckie

Kontynuuj korzystanie z punktu końcowego przewidywania interfejsu API v2 do momentu wydania wersji bot Framework.

## <a name="v2-api-deprecation"></a>Przestarzałe interfejsy API v2

Interfejs API przewidywania w wersji 2 nie będzie przestarzały przez co najmniej 9 miesięcy od wersji zapoznawczej v3, 8 czerwca 2020.

## <a name="endpoint-url-changes"></a>Zmiany adresu URL punktu końcowego

### <a name="changes-by-slot-name-and-version-name"></a>Zmiany według nazwy miejsca i nazwy wersji

[Format wywołania http punktu końcowego v3](developer-reference-resource.md#rest-endpoints) został zmieniony.

Jeśli chcesz wykonać zapytanie według wersji, musisz najpierw [opublikować za pośrednictwem interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b) w usłudze `"directVersionPublish":true` . Zbadaj punkt końcowy odwołujący się do identyfikatora wersji zamiast nazwy gniazda.

|Prawidłowe wartości dla `SLOT-NAME`|
|--|
|`production`|
|`staging`|

## <a name="request-changes"></a>Żądanie zmian

### <a name="query-string-changes"></a>Zmiany ciągu zapytania

[!INCLUDE [V3 query params](./includes/v3-prediction-query-params.md)]

### <a name="v3-post-body"></a>Treść wpisu v3

```JSON
{
    "query":"your utterance here",
    "options":{
        "datetimeReference": "2019-05-05T12:00:00",
        "preferExternalEntities": true
    },
    "externalEntities":[],
    "dynamicLists":[]
}
```

|Właściwość|Type|Wersja|Domyślne|Przeznaczenie|
|--|--|--|--|--|
|`dynamicLists`|array|Tylko wersja 3|Niewymagane.|[Listy dynamiczne](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time) umożliwiają rozbudowa istniejącej, przeszkolonej i opublikowanej jednostki listy, już w aplikacji Luis.|
|`externalEntities`|array|Tylko wersja 3|Niewymagane.|[Jednostki zewnętrzne](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time) zapewniają aplikacji Luis możliwość identyfikowania i etykietowania jednostek podczas środowiska uruchomieniowego, które mogą być używane jako funkcje istniejących jednostek. |
|`options.datetimeReference`|ciąg|Tylko wersja 3|Brak domyślnego|Służy do określania [przesunięcia datetimeV2](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity). Format datetimeReference to [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).|
|`options.preferExternalEntities`|boolean|Tylko wersja 3|fałsz|Określa, czy jest używana [Jednostka zewnętrzna użytkownika (o takiej samej nazwie jak istniejąca jednostka)](schema-change-prediction-runtime.md#override-existing-model-predictions) lub istniejąca jednostka w modelu jest używana do przewidywania. |
|`query`|ciąg|Tylko wersja 3|Wymagane.|**W wersji 2**wypowiedź do przewidywania jest `q` parametrem. <br><br>**W wersji 3**funkcja jest przenoszona do `query` parametru.|

## <a name="response-changes"></a>Zmiany odpowiedzi

Plik JSON odpowiedzi zapytania został zmieniony, aby umożliwić większy dostęp programistyczny do danych najczęściej używanych.

### <a name="top-level-json-changes"></a>Zmiany w formacie JSON najwyższego poziomu



Najważniejsze właściwości JSON dla V2 to, gdy `verbose` ma wartość true, co zwraca wszystkie intencje i ich wyniki we `intents` Właściwości:

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

Najważniejsze właściwości JSON dla wersji v3 to:

```JSON
{
    "query": "this is your utterance you want predicted",
    "prediction":{
        "topIntent": "intent-name-1",
        "intents": {},
        "entities":{}
    }
}
```

`intents`Obiekt jest listą nieuporządkowaną. Nie należy zakładać pierwszego elementu podrzędnego w `intents` odnosi się do `topIntent` . Zamiast tego należy użyć `topIntent` wartości, aby znaleźć wynik:

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

Zmiany schematu JSON odpowiedzi są dozwolone dla:

* Wyczyść rozróżnienie między oryginalnymi wypowiedź `query` i zwróciło prognozowanie `prediction` .
* Łatwiejszy dostęp programistyczny do przewidywanych danych. Zamiast wyliczania za pomocą tablicy w wersji 2, można uzyskać dostęp do wartości według **nazwy** dla intencji i jednostek. W przypadku przewidywanych ról jednostek nazwa roli jest zwracana, ponieważ jest unikatowa w całej aplikacji.
* Typy danych, jeśli zostały określone, są przestrzegane. Wartości numeryczne nie są już zwracane jako ciągi.
* Rozróżnienie między informacjami prognozowania pierwszego priorytetu a dodatkowymi metadanymi zwracanymi w `$instance` obiekcie.

### <a name="entity-response-changes"></a>Zmiany odpowiedzi jednostki

#### <a name="marking-placement-of-entities-in-utterances"></a>Oznaczanie rozmieszczenia jednostek w wyrażenia długości

**W wersji 2**jednostka została oznaczona w wypowiedź z `startIndex` i `endIndex` .

**W wersji 3**jednostka jest oznaczona za pomocą `startIndex` i `entityLength` .

#### <a name="access-instance-for-entity-metadata"></a>Dostęp `$instance` do metadanych jednostki

Jeśli potrzebujesz metadanych jednostki, ciąg zapytania musi używać `verbose=true` flagi, a odpowiedź zawiera metadane w `$instance` obiekcie. Przykłady przedstawiono w odpowiedziach JSON w poniższych sekcjach.

#### <a name="each-predicted-entity-is-represented-as-an-array"></a>Każda jednostka predykcyjna jest reprezentowana jako tablica

`prediction.entities.<entity-name>`Obiekt zawiera tablicę, ponieważ każda jednostka może być przewidywalna więcej niż jeden raz w wypowiedź.

<a name="prebuilt-entities-with-new-json"></a>

#### <a name="prebuilt-entity-changes"></a>Wstępnie utworzone zmiany jednostek

Obiekt odpowiedzi v3 zawiera zmiany w wstępnie skompilowanych jednostkach. Przejrzyj [określone wstępnie skompilowane jednostki](luis-reference-prebuilt-entities.md) , aby dowiedzieć się więcej.

#### <a name="list-entity-prediction-changes"></a>Wyświetl zmiany przewidywania jednostek

Plik JSON dla przewidywania jednostek listy zmienił się na tablicę tablic:

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
Każda tablica wewnętrzna odnosi się do tekstu wewnątrz wypowiedź. Obiekt wnętrza jest tablicą, ponieważ ten sam tekst może znajdować się na więcej niż jednej podliście jednostki listy.

Podczas mapowania między `entities` obiektem a `$instance` obiektem kolejność obiektów jest zachowywana dla prognoz jednostek listy.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

#### <a name="entity-role-name-instead-of-entity-name"></a>Nazwa roli jednostki zamiast nazwy jednostki

W wersji 2 `entities` Tablica zwróciła wszystkie przewidywane jednostki o nazwie jednostki, która jest unikatowym identyfikatorem. W wersji 3, jeśli jednostka używa ról, a Prognoza dotyczy roli jednostki, identyfikator podstawowy to nazwa roli. Jest to możliwe, ponieważ nazwy ról jednostek muszą być unikatowe w całej aplikacji, w tym w przypadku innych nazw modelu (intencje, jednostki).

W poniższym przykładzie: Rozważmy wypowiedź, który zawiera tekst, `Yellow Bird Lane` . Ten tekst jest przewidywany jako `Location` rola jednostki niestandardowej `Destination` .

|Wypowiedź tekst|Nazwa jednostki|Nazwa roli|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

W wersji 2 jednostka jest identyfikowana przez _nazwę jednostki_ z rolą jako właściwość obiektu:

```JSON
"entities":[
    {
        "entity": "Yellow Bird Lane",
        "type": "Location",
        "startIndex": 13,
        "endIndex": 20,
        "score": 0.786378264,
        "role": "Destination"
    }
]
```

W wersji 3 jednostka jest przywoływana przez _rolę jednostki_, jeśli przewidywana jest rola:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

W wersji 3, ten sam wynik z `verbose` flagą do zwrócenia metadanych jednostki:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ],
    "$instance":{
        "Destination": [
            {
                "role": "Destination",
                "type": "Location",
                "text": "Yellow Bird Lane",
                "startIndex": 25,
                "length":16,
                "score": 0.9837309,
                "modelTypeId": 1,
                "modelType": "Entity Extractor"
            }
        ]
    }
}
```

<a name="external-entities-passed-in-at-prediction-time"></a>
<a name="override-existing-model-predictions"></a>

## <a name="extend-the-app-at-prediction-time"></a>Zwiększ aplikację w czasie przewidywania

Poznaj [koncepcje](schema-change-prediction-runtime.md) dotyczące sposobu rozbudowywania aplikacji w środowisku uruchomieniowym przewidywania.

## <a name="deprecation"></a>Przestarzałe

Interfejs API v2 nie będzie przestarzały przez co najmniej 9 miesięcy od wersji zapoznawczej v3.

## <a name="next-steps"></a>Następne kroki

Użyj dokumentacji interfejsu API v3, aby zaktualizować istniejące wywołania REST do interfejsów API LUIS [punktów końcowych](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/operations/5cb0a9459a1fe8fa44c28dd8) .
