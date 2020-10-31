---
title: Wyszukiwanie jednostek za pomocą interfejsu API wyszukiwania jednostek Bing
titleSuffix: Azure Cognitive Services
description: Użyj interfejsu API wyszukiwanie jednostek Bing, aby wyodrębniać i wyszukiwać jednostki oraz miejsca z poziomu zapytań wyszukiwania.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 532bf806789476c1ec901c1e4ac8522451819625
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93085126"
---
# <a name="searching-for-entities-with-the-bing-entity-api"></a>Wyszukiwanie jednostek za pomocą interfejsu API jednostek Bing

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](https://aka.ms/cogsvcs/bingmove).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Umowa Enterprise, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](https://aka.ms/cogsvcs/bingmigration).

## <a name="suggest-search-terms-with-the-bing-autosuggest-api"></a>Sugerowanie terminów wyszukiwania za pomocą interfejsu API automatycznego sugerowania Bing

Jeśli udostępniasz pole wyszukiwania, w którym użytkownik wprowadza wyszukiwany termin, użyj [interfejsu API automatycznego sugerowania Bing](../../bing-autosuggest/get-suggested-search-terms.md) w celu ulepszenia jego działania. Interfejs API zwraca sugerowane ciągi zapytań na podstawie częściowych wyszukiwanych terminów w miarę wpisywania ich przez użytkownika.

Gdy użytkownik wprowadzi wyszukiwany termin, zakoduj go w formacie URL przed ustawieniem parametru zapytania [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query). Jeśli na przykład użytkownik wprowadzi termin *Marcus Appel* , należy ustawić parametr `q` na *Marcus+Appel* lub *Marcus%20Appel* .

Jeśli wyszukiwany termin zawiera błąd pisowni, odpowiedź wyszukiwania zawiera obiekt [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#querycontext). Obiekt ten zawiera pierwotną pisownię oraz poprawioną pisownię użytą przez usługę Bing podczas wyszukiwania.

```json
"queryContext": {
    "originalQuery": "hollo wrld",
    "alteredQuery": "hello world",
    "alterationOverrideQuery": "+hollo wrld",
    "adultIntent": false
}
```

## <a name="the-bing-entity-search-api-response"></a>Odpowiedź interfejsu API wyszukiwania jednostek Bing

Odpowiedź interfejsu API zawiera obiekt [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#searchresponse). Jeśli wyszukiwarka Bing znajdzie dopasowane jednostki lub miejsca, ten obiekt będzie zawierał pole `entities`, pole `places` lub oba te pola. W przeciwnym razie obiekt odpowiedzi nie będzie zawierał żadnego z tych pól.
> [!NOTE]
> Odpowiedzi dotyczące jednostek są obsługiwane na wielu rynkach, ale odpowiedzi dotyczące miejsc są obsługiwane tylko w przypadku lokalizacji firmowych w USA. 

Pole `entities` to obiekt [EntityAnswer](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) zawierający listę obiektów [Entity](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entity) (zobacz pole `value`). Lista może zawierać jedną jednostkę dominującą, wiele jednostek uściślających lub oba te elementy. 

Jednostka dominująca jest zwracana, gdy zostanie uznana przez usługę Bing za jedyną jednostkę spełniającą kryteria żądania (można jednoznacznie stwierdzić, jaka jednostka spełnia kryteria żądania). Jeśli kryteria żądania spełnia wiele jednostek, lista zawiera większą liczbę jednostek uściślających. Na przykład jeśli w żądaniu użyto ogólnego tytułu serii filmów, lista będzie najprawdopodobniej zawierała jednostki uściślające. Jeśli jednak w żądaniu użyto tytułu konkretnego filmu z serii, lista będzie najprawdopodobniej zawierała jedną jednostkę dominującą.

Jednostki mogą obejmować znane osobistości, na przykład piosenkarzy, aktorki, sportowców, modelki itp.; miejsca i obiekty, takie jak Mount Rainier czy Mauzoleum Abrahama Lincolna; a także inne obiekty, takie jak banan, goldendoodle albo tytuł książki lub filmu. Pole [entityPresentationInfo](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entitypresentationinfo) zawiera wskazówki określające typ jednostki. Mogą wskazywać na przykład, że jest to osoba, film, zwierzę lub atrakcja. Aby zapoznać się z listą możliwych typów, zobacz [Entity types (Typy jednostek)](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entity-types)

```json
"entityPresentationInfo": {
    "entityScenario": "DominantEntity",
    "entityTypeHints": ["Attraction"],
    "entityTypeDisplayHint": "Mountain"
}, ...
```

Poniżej przedstawiono odpowiedź zawierającą jednostkę dominującą i uściślającą.

```json
{
    "_type": "SearchResponse",
    "queryContext": {
        "originalQuery": "Mount Rainier"
    },
    "entities": {
        "value": [{
            "contractualRules": [{
                "_type": "ContractualRules/LicenseAttribution",
                "targetPropertyName": "description",
                "mustBeCloseToContent": true,
                "license": {
                    "name": "CC-BY-SA",
                    "url": "https://creativecommons.org/licenses/by-sa/3.0/"
                },
                "licenseNotice": "Text under CC-BY-SA license"
            },
            {
                "_type": "ContractualRules/LinkAttribution",
                "targetPropertyName": "description",
                "mustBeCloseToContent": true,
                "text": "contoso.com",
                "url": "http://contoso.com/mount_rainier"
            },
            {
                "_type": "ContractualRules/MediaAttribution",
                "targetPropertyName": "image",
                "mustBeCloseToContent": true,
                "url": "http://contoso.com/mount-rainier"
            }],
            "webSearchUrl": "https://www.bing.com/search?q=Mount%20Rainier...",
            "name": "Mount Rainier",
            "url": "http://www.northwindtraders.com/",
            "image": {
                "name": "Mount Rainier",
                "thumbnailUrl": "https://www.bing.com/th?id=A4ae343983daa4...",
                "provider": [{
                    "_type": "Organization",
                    "url": "http://contoso.com/mount_rainier"
                }],
                "hostPageUrl": "http://contoso.com/commons/7/72/mount_rain...",
                "width": 110,
                "height": 110
            },
            "description": "Mount Rainier is 14,411 ft tall and the highest mountain...",
            "entityPresentationInfo": {
                "entityScenario": "DominantEntity",
                "entityTypeHints": ["Attraction"]
            },
            "bingId": "38b9431e-cf91-93be-0584-c42a3ecbfdc7"
        },
        {
            "contractualRules": [{
                "_type": "ContractualRules/MediaAttribution",
                "targetPropertyName": "image",
                "mustBeCloseToContent": true,
                "url": "http://contoso.com/mount_rainier_national_park"
            }],
            "webSearchUrl": "https://www.bing.com/search?q=Mount%20Rainier%20National...",
            "name": "Mount Rainier National Park",
            "url": "http://worldwideimporters.com/",
            "image": {
                "name": "Mount Rainier National Park",
                "thumbnailUrl": "https://www.bing.com/th?id=A91bdc5a1b648a695a39...",
                "provider": [{
                    "_type": "Organization",
                    "url": "http://contoso.com/mount_rainier_national_park"
                }],
                "hostPageUrl": "http://contoso.com/en/7/7a...",
                "width": 50,
                "height": 50
            },
            "description": "Mount Rainier National Park is a United States National Park...",
            "entityPresentationInfo": {
                "entityScenario": "DisambiguationItem",
                "entityTypeHints": ["Organization"]
            },
            "bingId": "29d4b681-227a-3924-7bb1-8a54e8666b8c"
        }]
    }
}
```

Jednostka zawiera pola `name` (nazwa), `description` (opis) i `image` (obraz). W przypadku wyświetlania tych pól w środowisku użytkownika konieczne jest podanie ich autorstwa lub źródła. Pole `contractualRules` (reguły umowne) zawiera listę wymaganych przypisań autorstwa. Reguła umowna określa również pole, którego dotyczy dane przypisanie. Aby uzyskać informacje dotyczące przypisywania autorstwa, zobacz [Określanie autorstwa danych](#data-attribution).

```json
"contractualRules": [{
    "_type": "ContractualRules/LicenseAttribution",
    "targetPropertyName": "description",
    "mustBeCloseToContent": true,
    "license": {
        "name": "CC-BY-SA",
        "url": "https://creativecommons.org/licenses/by-sa/3.0/"
    },
    "licenseNotice": "Text under CC-BY-SA license"
},
{
    "_type": "ContractualRules/LinkAttribution",
    "targetPropertyName": "description",
    "mustBeCloseToContent": true,
    "text": "contoso.com",
    "url": "http://contoso.com/wiki/Mount_Rainier"
},
{
    "_type": "ContractualRules/MediaAttribution",
    "targetPropertyName": "image",
    "mustBeCloseToContent": true,
    "url": "http://contoso.com/wiki/Mount_Rainier"
}], ...
```

W przypadku wyświetlania informacji dotyczących jednostki (nazwy, opisu i obrazu) należy również użyć adresu URL w polu `webSearchUrl`, aby dodać link do zawierającej tę jednostkę strony wyników wyszukiwania w usłudze Bing.

## <a name="find-places"></a>Znajdowanie miejsc

Pole `places` to obiekt [LocalEntityAnswer](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) zawierający listę obiektów [Place](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#place). Zobacz [Entity Types](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entity-types) (Typy jednostek), aby uzyskać więcej informacji. Lista zawiera co najmniej jedną lokalną jednostkę spełniającą kryteria żądania.

Miejsca to między innymi restauracje, hotele, czy lokalne firmy. Pole [entityPresentationInfo](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entitypresentationinfo) zawiera wskazówki określające typ jednostki lokalnej. Lista zawiera listę wskazówek, takich jak Place (miejsce), LocalBusiness (lokalna firma), czy Restaurant (restauracja). Każda kolejne wskazówka w tablicy powoduje zawężenie typu jednostki. Aby zapoznać się z listą możliwych typów, zobacz [Entity types (Typy jednostek)](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entity-types)

```json
"entityPresentationInfo": {
    "entityScenario": "ListItem",
    "entityTypeHints": ["Place",
    "LocalBusiness",
    "Restaurant"]
}, ...
```
> [!NOTE]
> Odpowiedzi dotyczące jednostek są obsługiwane na wielu rynkach, ale odpowiedzi dotyczące miejsc są obsługiwane tylko w przypadku lokalizacji firmowych w USA. 

Zapytania dotyczące jednostek uwzględniające lokalizację, na przykład *restaurants near me* (restauracje w pobliżu), wymagają lokalizacji użytkownika do znalezienia dokładnych wyników. W zapytaniach należy zawsze używać nagłówków X-Search-Location i X-MSEdge-ClientIP w celu określenia lokalizacji użytkownika. Jeśli usługa Bing uzna, że użycie lokalizacji użytkownika będzie korzystne podczas wykonywania zapytania, ustawi w polu `askUserForLocation` obiektu [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#querycontext) wartość **true** . 

```json
{
    "_type": "SearchResponse",
    "queryContext": {
        "originalQuery": "Sinful Bakery and Cafe",
        "askUserForLocation": true
    },
    ...
}
```

Wynik dotyczący miejsca zawiera nazwę tego miejsca, jego adres, numer telefonu oraz adres URL jego witryny internetowej. W przypadku wyświetlania informacji dotyczących jednostki należy również użyć adresu URL w polu `webSearchUrl`, aby dodać link do zawierającej tę jednostkę strony wyników wyszukiwania w usłudze Bing.

```json
"places": {
    "value": [{
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Sinful%20Bakery...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "http://libertysdelightfulsinfulbakeryandcafe.com/",
        "entityPresentationInfo": {
            "entityScenario": "ListItem",
            "entityTypeHints": ["Place",
            "LocalBusiness",
            "Restaurant"]
        },
        "address": {
            "addressLocality": "Seattle",
            "addressRegion": "WA",
            "postalCode": "98112",
            "addressCountry": "US",
            "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
    }]
}
```

> [!NOTE]
> Użytkownik ani żaden inny podmiot w jego imieniu nie może używać, zachowywać, przechowywać, buforować, udostępniać ani rozpowszechniać żadnych danych z interfejsu API wyszukiwania jednostek do celów testowania, opracowywania, rozpowszechniania lub udostępniania jakiejkolwiek usługi lub funkcji firmy innej niż Microsoft.  

## <a name="data-attribution"></a>Określanie autorstwa danych

Odpowiedzi z interfejsu API wyszukiwania jednostek Bing zawierają informacje, których właścicielem są inne podmioty. Użytkownik odpowiada za korzystanie z usługi w sposób właściwy, na przykład poprzez przestrzeganie licencji Creative Commons, z których mogą korzystać elementy środowiska użytkownika.

Jeśli odpowiedź lub wynik zawiera pola `contractualRules`, `attributions` lub `provider`, należy podać autorstwo danych. Jeśli odpowiedź nie zawiera żadnego z tych pól, podanie autorstwa danych nie jest wymagane. Jeśli odpowiedź zawiera pole `contractualRules` oraz pola `attributions` i/lub `provider`, należy zastosować reguły umowne w celu określenia autorstwa danych.

W poniższym przykładzie pokazano jednostkę, która zawiera regułę umowną MediaAttribution i obraz, który zawiera pole `provider`. Reguła MediaAttribution identyfikuje obraz jako obiekt docelowy reguły, a więc możesz zignorować pole `provider` obrazu, i zastosować regułę MediaAttribution w celu określenia autorstwa.  

```json
"value": [{
    "contractualRules": [
        ...
        {
            "_type": "ContractualRules/MediaAttribution",
            "targetPropertyName": "image",
            "mustBeCloseToContent": true,
            "url": "http://contoso.com/mount_rainier"
        }
    ],
    ...
    "image": {
        "name": "Mount Rainier",
        "thumbnailUrl": "https://www.bing.com/th?id=A46378861201...",
        "provider": [{
            "_type": "Organization",
            "url": "http://contoso.com/mount_rainier"
        }],
        "hostPageUrl": "http://www.graphicdesigninstitute.com/Uploaded...",
        "width": 110,
        "height": 110
    },
    ...
}]
```

Jeśli reguła umowna zawiera pole `targetPropertyName`, reguła dotyczy tylko pola docelowego. W przeciwnym razie ta reguła dotyczy obiektu nadrzędnego, który zawiera pole `contractualRules`.

W poniższym przykładzie reguła `LinkAttribution` zawiera pole `targetPropertyName`, a więc reguła dotyczy pola `description`. W przypadku reguł, które dotyczą konkretnych pól, po danych docelowych musisz umieścić wiersz zawierający hiperlink do witryny internetowej dostawcy. Na przykład aby podać autorstwo opisu, po tekście opisu należy dołączyć wiersz zawierający hiperlink do danych w witrynie internetowej dostawcy — w tym przypadku link do witryny contoso.com.

```json
"entities": {
    "value": [{
            ...
            "description": "Marcus Appel is a former American....",
            ...
            "contractualRules": [{
                    "_type": "ContractualRules/LinkAttribution",
                    "targetPropertyName": "description",
                    "mustBeCloseToContent": true,
                    "text": "contoso.com",
                    "url": "http://contoso.com/cr?IG=B8AD73..."
                 },
            ...
  
```

### <a name="license-attribution"></a>Określanie licencji

Jeśli lista reguł umownych zawiera regułę [LicenseAttribution](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#licenseattribution), należy w wierszu następującym tuż po zawartości objętej licencją dodać odpowiednią informację. Reguła `LicenseAttribution` korzysta z pola `targetPropertyName`, aby określić właściwość, której dotyczy licencja.

Poniżej pokazano przykład, który zawiera regułę `LicenseAttribution`.

![Określanie licencji](../media/cognitive-services-bing-entities-api/licenseattribution.png)

Wyświetlana informacja dotycząca licencji musi zawierać hiperlink do witryny internetowej, która zawiera informacje dotyczące licencji. Najczęściej hiperlinkiem jest nazwa licencji. Na przykład jeśli komunikat brzmi **Text under CC-BY-SA license** (Tekst w ramach licencji CC-BY-SA), a CC-BY-SA to nazwa licencji, hiperlinkiem będzie ciąg „CC-BY-SA”.

### <a name="link-and-text-attribution"></a>Określanie autorstwa linków i tekstu

Reguły [LinkAttribution](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#linkattribution) i [TextAttribution](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#textattribution) są zwykle używane do identyfikowania dostawcy danych. Pole `targetPropertyName` określa pole, którego dotyczy reguła.

Aby określić dostawców, należy dodać odpowiedni wiersz tuż po zawartości, której dotyczy (na przykład po polu docelowym). Ten wiersz powinien być czytelnie oznaczony, aby wskazać, że określeni dostawcy są źródłem danych. Na przykład „Źródło danych: contoso.com”. W przypadku reguł `LinkAttribution`, należy utworzyć hiperlink do witryny internetowej dostawcy.

Poniżej pokazano przykład, który zawiera reguły `LinkAttribution` i `TextAttribution`.

![Określanie autorstwa tekstu linku](../media/cognitive-services-bing-entities-api/linktextattribution.png)

### <a name="media-attribution"></a>Określanie autorstwa multimediów

Jeśli jednostka zawiera obraz, który wyświetlasz, musisz dołączyć link do witryny dostawcy, który można kliknąć. Jeśli jednostka zawiera regułę [MediaAttribution](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#mediaattribution), należy do utworzenia tego linku użyć adresu URL określonego przez regułę. W przeciwnym razie do utworzenia tego linku użyj adresu URL zawartego w polu `provider` obrazu.

Poniżej przedstawiono przykład, który zawiera pole `provider` obrazu i reguły umowne. Ponieważ przykład zawiera reguły umowne, należy zignorować pole `provider` obrazu i zastosować regułę `MediaAttribution`.

![Określanie autorstwa multimediów](../media/cognitive-services-bing-entities-api/mediaattribution.png)

### <a name="search-or-search-like-experience"></a>Środowisko wyszukiwania lub czynności podobnych do wyszukiwania

Tak samo jak w przypadku interfejsu API wyszukiwania w sieci Web Bing, interfejs API wyszukiwania jednostek Bing może być używany tylko w wyniku wykonania zapytania lub wyszukiwania bezpośrednio przez użytkownika lub w wyniku wykonania w aplikacji lub środowisku czynności, którą można logicznie zinterpretować jako żądanie wyszukiwania użytkownika. W celach ilustracyjnych przedstawiono poniżej kilka przykładów akceptowalnych sytuacji wyszukiwania lub czynności podobnych do wyszukiwania.

- Użytkownik wprowadza zapytanie bezpośrednio w polu wyszukiwania w aplikacji.
- Użytkownik wybiera określony tekst lub obraz, a następnie wybiera opcję „więcej informacji” lub „dodatkowe informacje”.
- Użytkownik zadaje botowi wyszukiwania pytanie dotyczące określonego tematu.
- Użytkownik zatrzymuje się na obiekcie lub jednostce w scenariuszu wyszukiwania wizualnego.

Jeśli nie masz pewności, czy Twoje środowisko można uznać za środowisko czynności podobnych do wyszukiwania, zalecamy kontakt z firmą Microsoft.

## <a name="throttling-requests"></a>Ograniczanie żądań

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Następne kroki

* Użyj tego poradnika [Szybki start](../quickstarts/csharp.md), aby zacząć wyszukiwać jednostki za pomocą interfejsu API wyszukiwania jednostek Bing.
