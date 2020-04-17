---
title: Przewidywanie zmian punktu końcowego w interfejsie API systemu V3
description: Zmieniono interfejsy API punktu końcowego prognozowania kwerendy V3. Ten przewodnik służy do zrozumienia, jak przeprowadzić migrację do interfejsów API punktu końcowego w wersji 3.
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: diberry
ms.openlocfilehash: 4b6d28b24ffc6c0a848d1c7a34e863da0606d936
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81530389"
---
# <a name="prediction-endpoint-changes-for-v3"></a>Przewidywanie zmian punktu końcowego dla wersji 3

Zmieniono interfejsy API punktu końcowego prognozowania kwerendy V3. Ten przewodnik służy do zrozumienia, jak przeprowadzić migrację do interfejsów API punktu końcowego w wersji 3.

**Ogólnie dostępny stan** — ten interfejs API systemu V3 zawiera znaczące zmiany żądań json i odpowiedzi z interfejsu API systemu V2.

Interfejs API systemu V3 udostępnia następujące nowe funkcje:

* [Podmioty zewnętrzne](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)
* [Listy dynamiczne](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Wstępnie utworzone zmiany JSON jednostki](#prebuilt-entity-changes)

[Żądanie](#request-changes) punktu końcowego przewidywania i [odpowiedzi](#response-changes) mają istotne zmiany do obsługi nowych funkcji wymienionych powyżej, w tym następujące:

* [Zmiany obiektu odpowiedzi](#top-level-json-changes)
* [Odwołania do nazwy roli jednostki zamiast nazwy jednostki](#entity-role-name-instead-of-entity-name)
* [Właściwości do oznaczania jednostek w wypowiedziach](#marking-placement-of-entities-in-utterances)

[Dokumentacja referencyjna](https://aka.ms/luis-api-v3) jest dostępna dla wersji V3.

## <a name="v3-changes-from-preview-to-ga"></a>V3 zmienia się z podglądu na GA

W wersji 3 wprowadzono następujące zmiany w ramach przejścia do ga:

* Następujące wstępnie utworzone jednostki mają różne odpowiedzi JSON:
    * [Okręg wyborczy OrdinalV1](luis-reference-prebuilt-ordinal.md)
    * [GeographyV2](luis-reference-prebuilt-geographyv2.md)
    * [DatetimeV2](luis-reference-prebuilt-datetimev2.md)
    * Mierzalne nazwy `units` klucza jednostki od do`unit`

* Zmiana w treści żądania JSON:
    * od `preferExternalEntities` do`preferExternalEntities`
    * parametr `score` opcjonalny dla elementów zewnętrznych

* Zmiana w treści JSON w organie odpowiedzi:
    * `normalizedQuery`Usunięte

## <a name="suggested-adoption-strategy"></a>Sugerowana strategia adopcyjna

Jeśli używasz Bot Framework, Sprawdzanie pisowni Bing V7 lub chcesz przeprowadzić migrację aplikacji usługi LUIS tylko tworzenia, nadal używać punktu końcowego V2.

Jeśli wiesz, że żadna z aplikacji klienckich lub integracji (Bot Framework i Sprawdzanie pisowni Bing V7) mają wpływ i są wygodne migracji tworzenia aplikacji usługi LUIS i punktu końcowego przewidywania w tym samym czasie, rozpocząć korzystanie z punktu końcowego przewidywania V3. Punkt końcowy przewidywania V2 będzie nadal dostępny i jest dobrą strategią rezerwową.


## <a name="not-supported"></a>Nieobsługiwane

### <a name="bing-spell-check"></a>Sprawdzanie pisowni Bing

Ten interfejs API nie jest obsługiwany w punkcie końcowym przewidywania wersji 3 — nadal używać punktu końcowego przewidywania interfejsu API w wersji 2 dla poprawek pisowni. Jeśli potrzebujesz korekty pisowni podczas korzystania z interfejsu API V3, należy wywołać aplikację kliencką interfejsu API [sprawdzania pisowni Bing](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/overview) i zmienić tekst na poprawną pisownię przed wysłaniem tekstu do interfejsu API usługi LUIS.

## <a name="bot-framework-and-azure-bot-service-client-applications"></a>Aplikacje klienckie Bot Framework i Azure Bot Service

Kontynuuj używanie punktu końcowego przewidywania interfejsu API w wersji 2, dopóki nie zostanie wydany v4.7 programu Bot Framework.

## <a name="v2-api-deprecation"></a>Deprecation interfejsu API w wersji 2

Interfejs API przewidywania wersji 2 nie zostanie przestarzały przez co najmniej 9 miesięcy po wersji zapoznawczej wersji 3, 8 czerwca 2020 r.

## <a name="endpoint-url-changes"></a>Zmiany adresu URL punktu końcowego

### <a name="changes-by-slot-name-and-version-name"></a>Zmiany według nazwy gniazda i nazwy wersji

Format wywołania HTTP punktu końcowego V3 uległ zmianie.

Jeśli chcesz zapytać według wersji, najpierw musisz [opublikować za pośrednictwem interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b) z programem `"directVersionPublish":true`. Kwerenda punktu końcowego odwołując się do identyfikatora wersji zamiast nazwy gniazda.

|WERSJA INTERFEJSU API PRZEWIDYWANIA|Metoda|Adres URL|
|--|--|--|
|Wersja V3|GET|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>przewidywanie</b>/<b>v3.0</b>/apps/<b>{APP-ID}</b>/slots/<b>{SLOT-NAME}</b>/predict?query=<b>{QUERY}</b>|
|Wersja V3|POST|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>przewidywanie</b>/<b>v3.0</b>/apps/<b>{APP-ID}</b>/slots/<b>{SLOT-NAME}</b>/predict|
|Wersja 2|GET|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>przewidywanie</b>/<b>v3.0</b>/apps/<b>{APP-ID}</b>/versions/<b>{VERSION-ID}</b>/predict?query=<b>{QUERY}</b>|
|Wersja 2|POST|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>przewidywanie</b>/<b>v3.0</b>/apps/<b>{APP-ID}</b>/versions/<b>{VERSION-ID}</b>/predict|

|Prawidłowe wartości dla`SLOT-NAME`|
|--|
|`production`|
|`staging`|

## <a name="request-changes"></a>Żądanie zmian

### <a name="query-string-changes"></a>Zmiany ciągu kwerendy

Interfejs API wersji 3 ma różne parametry ciągu kwerendy.

|Nazwa Param|Typ|Wersja|Domyślne|Przeznaczenie|
|--|--|--|--|--|
|`log`|wartość logiczna|V2 & V3|false|Przechowywanie kwerendy w pliku dziennika. Wartość domyślna jest false.|
|`query`|ciąg|Tylko V3|Brak wartości domyślnej — jest wymagana w żądaniu GET|**W wersji 2**wypowiedź do przewidzenia znajduje się w parametrze. `q` <br><br>**W wersji V3**funkcja jest `query` przekazywana w parametrze.|
|`show-all-intents`|wartość logiczna|Tylko V3|false|Zwraca wszystkie intencje z odpowiednim wynikiem w **prediction.intents** obiektu. Intencje są zwracane jako `intents` obiekty w obiekcie nadrzędnym. Umożliwia to dostęp programowy bez konieczności znajdowania `prediction.intents.give`intencji w tablicy: . W wersji 2 zostały one zwrócone w tablicy. |
|`verbose`|wartość logiczna|V2 & V3|false|**W wersji V2**, gdy ustawiono wartość true, wszystkie przewidywane intencje zostały zwrócone. Jeśli potrzebujesz wszystkich przewidywanych intencji, użyj param V3 . `show-all-intents`<br><br>**W wersji 3**ten parametr zawiera tylko szczegóły metadanych jednostki przewidywania jednostki.  |
|`timezoneOffset`|ciąg|Wersja 2|-|Strefa czasowa zastosowana do jednostek datetimeV2.|
|`datetimeReference`|ciąg|Wersja V3|-|[Strefa czasowa](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) zastosowana do jednostek datetimeV2. Zastępuje `timezoneOffset` z V2.|


### <a name="v3-post-body"></a>Korpus V3 POST

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

|Właściwość|Typ|Wersja|Domyślne|Przeznaczenie|
|--|--|--|--|--|
|`dynamicLists`|tablica|Tylko V3|Niewymagane.|[Listy dynamiczne](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time) umożliwiają rozszerzenie istniejącej jednostki listy przeszkolonych i opublikowanych, już w aplikacji usługi LUIS.|
|`externalEntities`|tablica|Tylko V3|Niewymagane.|[Jednostki zewnętrzne](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time) dają aplikacji usługi LUIS możliwość identyfikowania i etykietowania jednostek w czasie wykonywania, które mogą być używane jako funkcje do istniejących jednostek. |
|`options.datetimeReference`|ciąg|Tylko V3|Brak wartości domyślnej|Służy do określania [przesunięcia datetimeV2](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity). Format datetimeReference to [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).|
|`options.preferExternalEntities`|wartość logiczna|Tylko V3|false|Określa, czy używana jest jednostka zewnętrzna użytkownika [(o takiej samej nazwie jak istniejąca encja)](schema-change-prediction-runtime.md#override-existing-model-predictions) lub istniejąca jednostka w modelu jest używana do przewidywania. |
|`query`|ciąg|Tylko V3|Wymagany.|**W wersji 2**wypowiedź do przewidzenia znajduje się w parametrze. `q` <br><br>**W wersji V3**funkcja jest `query` przekazywana w parametrze.|

## <a name="response-changes"></a>Zmiany odpowiedzi

Odpowiedź na kwerendę JSON zmieniona, aby umożliwić większy programowy dostęp do najczęściej używanych danych.

### <a name="top-level-json-changes"></a>Zmiany JSON najwyższego poziomu



Najlepsze właściwości JSON dla V2 `verbose` są, gdy jest ustawiona na true, `intents` który zwraca wszystkie intencje i ich wyniki w właściwości:

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

Najlepsze właściwości JSON dla wersji 3 to:

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

Obiekt `intents` jest nieuiarszną listą. Nie zakładaj pierwszego dziecka `intents` w odpowiada `topIntent`. Zamiast tego użyj `topIntent` wartości, aby znaleźć wynik:

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

Zmiany schematu JSON odpowiedzi pozwalają na:

* Wyraźne rozróżnienie między `query`oryginalną wypowiedź, i zwrócone przewidywanie, `prediction`.
* Łatwiejszy programowy dostęp do przewidywanych danych. Zamiast wyliczania za pośrednictwem tablicy w wersji 2, można uzyskać dostęp do wartości według **nazwy** dla intencji i jednostek. W przypadku przewidywanych ról encji nazwa roli jest zwracana, ponieważ jest unikatowa w całej aplikacji.
* Typy danych, jeśli są określone, są przestrzegane. Numeryki nie są już zwracane jako ciągi.
* Rozróżnienie między informacje przewidywanie pierwszego priorytetu `$instance` i dodatkowe metadane, zwrócone w obiekcie.

### <a name="entity-response-changes"></a>Zmiany odpowiedzi na encję

#### <a name="marking-placement-of-entities-in-utterances"></a>Oznaczanie umieszczania jednostek w wypowiedziach

**W wersji 2**jednostka została oznaczona `startIndex` w `endIndex`wypowiedź z i .

**W wersji V3**jednostka `startIndex` jest `entityLength`oznaczona i .

#### <a name="access-instance-for-entity-metadata"></a>Dostęp `$instance` do metadanych encji

Jeśli potrzebujesz metadanych jednostki, ciąg `verbose=true` zapytania musi używać flagi, a `$instance` odpowiedź zawiera metadane w obiekcie. Przykłady są wyświetlane w odpowiedziach JSON w poniższych sekcjach.

#### <a name="each-predicted-entity-is-represented-as-an-array"></a>Każda przewidywana encja jest reprezentowana jako tablica

Obiekt `prediction.entities.<entity-name>` zawiera tablicę, ponieważ każda jednostka można przewidzieć więcej niż jeden raz w wypowiedź.

<a name="prebuilt-entities-with-new-json"></a>

#### <a name="prebuilt-entity-changes"></a>Wstępnie utworzone zmiany encji

Obiekt odpowiedzi V3 zawiera zmiany wstępnie utworzone jednostki. Przejrzyj [określone wstępnie utworzone jednostki,](luis-reference-prebuilt-entities.md) aby dowiedzieć się więcej.

#### <a name="list-entity-prediction-changes"></a>Lista zmian prognozowania encji

JSON dla przewidywania jednostki listy zmienił się tablicy tablic:

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
Każda tablica wewnętrzna odpowiada tekstowi wewnątrz wypowiedź. Obiekt wewnętrzny jest tablicą, ponieważ ten sam tekst może pojawić się na więcej niż jednej podlisty encji listy.

Podczas mapowania `entities` między `$instance` obiektem do obiektu, kolejność obiektów jest zachowywana dla prognoz jednostki listy.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

#### <a name="entity-role-name-instead-of-entity-name"></a>Nazwa roli jednostki zamiast nazwy jednostki

W wersji 2 tablica `entities` zwróciła wszystkie przewidywane jednostki, przy czym nazwa jednostki jest unikatowym identyfikatorem. W wersji 3, jeśli jednostka używa ról i przewidywanie jest dla roli jednostki, identyfikator podstawowy jest nazwą roli. Jest to możliwe, ponieważ nazwy ról jednostki muszą być unikatowe w całej aplikacji, w tym innych nazw modelu (intencji, encji).

W poniższym przykładzie: należy wziąć pod `Yellow Bird Lane`uwagę wypowiedź, która zawiera tekst, . Ten tekst jest przewidywany `Location` jako rola `Destination`encji niestandardowej .

|Tekst wypowiedź|Nazwa jednostki|Nazwa roli|
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

W wersji 3 jednostka odwołuje się do _roli jednostki,_ jeśli przewidywanie jest dla roli:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

W wersji 3 ten `verbose` sam wynik z flagą do zwrócenia metadanych encji:

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

## <a name="extend-the-app-at-prediction-time"></a>Rozszerzanie aplikacji w czasie przewidywania

Dowiedz się [pojęcia](schema-change-prediction-runtime.md) dotyczące rozszerzania aplikacji w czasie wykonywania prognozowania.

## <a name="deprecation"></a>Przestarzałe

Interfejs API wersji 2 nie zostanie przestarzały przez co najmniej 9 miesięcy po wersji zapoznawczej wersji 3.

## <a name="next-steps"></a>Następne kroki

Za pomocą dokumentacji interfejsu API systemu V3 można zaktualizować istniejące wywołania REST do interfejsów API [punktu końcowego usługi](https://aka.ms/luis-api-v3) LUIS.
