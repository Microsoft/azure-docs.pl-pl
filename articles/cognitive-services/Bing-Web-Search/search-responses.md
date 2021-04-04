---
title: Struktura odpowiedzi interfejs API wyszukiwania w sieci Web Bing i typy odpowiedzi
titleSuffix: Azure Cognitive Services
description: Po wysłaniu wyszukiwanie w sieci Web Bing żądanie wyszukiwania zwraca `SearchResponse` obiekt w treści odpowiedzi.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 3dda95312a0b9191ddc11de62959f308ee19fff4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "94380984"
---
# <a name="bing-web-search-api-response-structure-and-answer-types"></a>Struktura odpowiedzi interfejs API wyszukiwania w sieci Web Bing i typy odpowiedzi  

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Enterprise Agreement, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Po wysłaniu wyszukiwanie w sieci Web Bing żądanie wyszukiwania zwraca [`SearchResponse`](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse) obiekt w treści odpowiedzi. Obiekt zawiera pole dla każdej odpowiedzi wyznaczonej przez usługę Bing do zapytania. Ten przykład ilustruje obiekt odpowiedzi, jeśli Bing zwróci wszystkie odpowiedzi:

```json
{
    "_type": "SearchResponse",
    "queryContext": {...},
    "webPages": {...},
    "images": {...},
    "relatedSearches": {...},
    "videos": {...},
    "news": {...},
    "spellSuggestion": {...},
    "computation": {...},
    "timeZone": {...},
    "rankingResponse": {...}
}, ...
```

Zwykle wyszukiwanie w sieci Web Bing zwraca podzestaw odpowiedzi. Na przykład jeśli termin kwerendy miał wartość *dinghies*, odpowiedź może obejmować `webPages` , `images` , i `rankingResponse` . O ile nie użyto [responseFilter](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter) do odfiltrowania stron sieci Web, odpowiedź zawsze zawiera `webpages` odpowiedzi i `rankingResponse` .

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

## <a name="webpages-answer"></a>Odpowiedź stron sieci Web

Odpowiedź na [stronach sieci Web](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer) zawiera listę linków do stron sieci Web, które wyszukiwanie w sieci Web Bing określone w odniesieniu do zapytania. Każda [Strona sieci Web](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage) na liście będzie zawierać: nazwę strony, adres URL, adres URL wyświetlania, Krótki opis zawartości i datę znalezienia zawartości przez usługę Bing.

```json
{
    "id": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
    "name": "Dinghy sailing",
    "url": "https:\/\/www.bing.com\/cr?IG=3A43CA5...",
    "displayUrl": "https:\/\/en.contoso.com\/wiki\/Dinghy_sailing",
    "snippet": "Dinghy sailing is the activity of sailing small boats...",
    "dateLastCrawled": "2017-04-05T16:25:00"
}, ...
```

Użyj `name` i, `url` Aby utworzyć hiperłącze, które pobiera użytkownika do strony sieci Web.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display the webpage in a search results page.

![Rendered webpage example](./media/cognitive-services-bing-web-api/bing-rendered-webpage-example.PNG)
-->

## <a name="images-answer"></a>Odpowiedź na obrazy

Odpowiedź na [obrazy](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) zawiera listę obrazów, które są ważne dla zapytania w przypadku usługi Bing. Każdy [obraz](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) na liście zawiera adres URL obrazu, jego rozmiar, wymiary i format kodowania. Obiekt obrazu zawiera także adres URL miniatury obrazu i wymiary tej miniatury.

```json
{
    "name": "Rich Passage Sailing Dinghy",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=3A43CA5CA64...",
    "thumbnailUrl": "https:\/\/tse1.mm.bing.net\/th?id=OIP....",
    "datePublished": "2011-10-29T11:26:00",
    "contentUrl": "http:\/\/upload.contoso.com\/sailing\/...",
    "hostPageUrl": "http:\/\/www.bing.com\/cr?IG=3A43CA5CA6464....",
    "contentSize": "79239 B",
    "encodingFormat": "jpeg",
    "hostPageDisplayUrl": "http:\/\/en.contoso.com\/wiki\/File...",
    "width": 526,
    "height": 688,
    "thumbnail": {
        "width": 229,
        "height": 300
    },
    "insightsSourcesSummary": {
        "shoppingSourcesCount": 0,
        "recipeSourcesCount": 0
    }
}, ...
```

W zależności od urządzenia użytkownika zwykle wyświetlany jest podzbiór miniatur z opcją dla użytkownika [za pomocą](./paging-search-results.md) pozostałych obrazów.

<!-- Remove until this can be replaced with a sanitized version.
![List of thumbnail images](./media/cognitive-services-bing-web-api/bing-web-image-thumbnails.PNG)
-->

Możesz również powiększać miniatury, gdy użytkownik zatrzyma nad nimi wskaźnik myszy. Pamiętaj, aby po powiększeniu obrazu podać informacje o jego źródle. Na przykład poprzez wyodrębnienie hosta z `hostPageDisplayUrl` i wyświetlenie go poniżej obrazu. Aby uzyskać informacje na temat zmieniania rozmiaru miniatur, zobacz [Zmiana rozmiaru i przycinanie miniatur](./resize-and-crop-thumbnails.md).

<!-- Remove until this can be replaced with a sanitized version.
![Expanded view of thumbnail image](./media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

Jeśli użytkownik kliknie miniaturę, użyj `webSearchUrl` do przełączenia użytkownika do strony wyników wyszukiwania Bing dla obrazów, które zawierają kolaż obrazu.

Aby uzyskać szczegółowe informacje na temat odpowiedzi i obrazów obrazu, zobacz [Wyszukiwanie obrazów API](../bing-image-search/overview.md).

## <a name="related-searches-answer"></a>Odpowiedź związana z wyszukiwaniem

Odpowiedź [relatedSearches](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse-relatedsearches) zawiera listę najpopularniejszych zapytań tworzonych przez innych użytkowników. Każde [zapytanie](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query_obj) na liście zawiera ciąg zapytania ( `text` ), ciąg zapytania z znakami wyróżniania trafień ( `displayText` ) i adres URL ( `webSearchUrl` ) do strony wyników wyszukiwania w usłudze Bing dla tego zapytania.

```json
{
    "text": "dinghy racing teams",
    "displayText": "dinghy racing teams",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=96C4CF214A0..."
}, ...
```

Użyj `displayText` ciągu zapytania i `webSearchUrl` adresu URL, aby utworzyć hiperłącze, które pobiera użytkownika do strony wyników wyszukiwania Bing dla powiązanego zapytania. Możesz również użyć `text` ciągu zapytania we własnej kwerendzie interfejsu API wyszukiwanie w sieci Web i wyświetlić wyniki samodzielnie.

Aby uzyskać informacje o sposobie obsługi znaczników wyróżniania w programie `displayText` , zobacz [podświetlanie trafień](../bing-web-search/hit-highlighting.md).

Poniżej przedstawiono przykład użycia powiązanych zapytań w Bing.com.

![Przykład wyszukiwania związanego z wyszukiwaniem w usłudze Bing](./media/cognitive-services-bing-web-api/bing-web-rendered-relatedsearches.GIF)

## <a name="videos-answer"></a>Odpowiedź wideo

Odpowiedź [wideo](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) zawiera listę filmów wideo, które są ważne dla zapytania. Każde [wideo](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video) na liście zawiera adres URL filmu wideo, jego czas trwania, wymiary i format kodowania. Obiekt wideo zawiera także adres URL miniatury wideo i wymiary tej miniatury.

```json
{
    "name": "Sailing dinghy",
    "description": "Northwind Traders is a 12 foot gunter rigged...",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=1CAE739681D84...",
    "thumbnailUrl": "https:\/\/tse2.mm.bing.net\/th?id=OVP.wsKiL...",
    "datePublished": "2013-11-06T01:56:28",
    "publisher": [{
        "name": "Fabrikam"
    }],
    "contentUrl": "https:\/\/www.fabrikam.com\/watch?v=MrVBWZpJjX",
    "hostPageUrl": "https:\/\/www.bing.com\/cr?IG=1CAE739681D8400DB...",
    "encodingFormat": "mp4",
    "hostPageDisplayUrl": "https:\/\/www.fabrikam.com\/watch?v=MrBWZpJjXo",
    "width": 1280,
    "height": 720,
    "duration": "PT3M47S",
    "motionThumbnailUrl": "https:\/\/tse2.mm.bing.net\/th?id=OM.oa...",
    "embedHtml": "<iframe width=\"1280\" height=\"720\" src=\"http:\/\/www....><\/iframe>",
    "allowHttpsEmbed": true,
    "viewCount": 19089,
    "thumbnail": {
        "width": 300,
        "height": 168
    },
    "allowMobileEmbed": true,
    "isSuperfresh": false
}, ...
```

W zależności od urządzenia użytkownika zwykle jest wyświetlany podzestaw filmów wideo z opcją wyświetlania pozostałych filmów wideo. Zostanie wyświetlona miniatura filmu wideo o długości, opisie (nazwie) i przypisywaniu (wydawcy).

<!-- Remove until this can be replaced with a sanitized version.
![List of video thumbnails](./media/cognitive-services-bing-web-api/bing-web-video-thumbnails.PNG)
-->

Gdy użytkownik umieści wskaźnik myszy nad miniaturą, której można użyć `motionThumbnailUrl` do odtwarzania miniaturowej wersji wideo. Pamiętaj o nadaniu atrybutu wyświetlonej miniaturze ruchu.

<!-- Remove until this can be replaced with a sanitized version.
![Motion thumbnail of a video](./media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

Po kliknięciu miniatury będą dostępne następujące opcje wyświetlania filmu wideo:

- Użyj, `hostPageUrl` Aby wyświetlić wideo w witrynie sieci Web hosta (na przykład YouTube)
- Używanie `webSearchUrl` do wyświetlania wideo w przeglądarce wideo Bing
- Używanie `embedHtml` do osadzania wideo we własnym środowisku

Aby uzyskać szczegółowe informacje na temat odpowiedzi wideo i wideo, zobacz [Wyszukiwanie wideo API](../bing-video-search/overview.md).

## <a name="news-answer"></a>Odpowiedź na wiadomości

Odpowiedź z [wiadomościami](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news) zawiera listę artykułów z wiadomościami, które są ważne dla zapytania w usłudze Bing. Każdy [artykułu z wiadomościami](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) na liście zawiera nazwę, opis i adres URL do artykułu w witrynie internetowej hosta. Jeśli artykuł zawiera obraz, obiekt zawiera miniaturę obrazu.

```json
{
    "name": "WC Sailing Qualifies for America Trophy with...",
    "url": "http:\/\/www.bing.com\/cr?IG=3445EEF15DAF4FFFBF7...",
    "image": {
        "contentUrl": "http:\/\/www.contoso.com\/sports\/sail...",
        "thumbnail": {
            "contentUrl": "https:\/\/www.bing.com\/th?id=ON.1...",
            "width": 400,
            "height": 272
        }
    },
    "description": "The WC sailing team qualified for a...",
    "provider": [{
        "_type": "Organization",
        "name": "contoso.com"
    }],
    "datePublished": "2017-04-16T21:56:00"
}, ...
```

W zależności od urządzenia użytkownika zostanie wyświetlony podzestaw artykułów z wiadomościami z opcją dla użytkownika, aby wyświetlić pozostałe artykuły. Użyj właściwości `name` i `url`, aby utworzyć hiperlink prowadzący użytkownika do artykułu w witrynie hosta. Jeśli artykuł zawiera obraz, należy kliknąć obraz przy użyciu polecenia `url` . Pamiętaj, aby podać źródło artykułu przy użyciu właściwości `provider`.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display articles in a search results page.

![List of news articles](./media/cognitive-services-bing-web-api/bing-web-news-list.PNG)
-->

Aby uzyskać szczegółowe informacje o nowościach i artykułach nowości, zobacz [Wyszukiwanie wiadomości API](../bing-news-search/search-the-web.md).

## <a name="computation-answer"></a>Odpowiedź obliczeniowa

Jeśli użytkownik wprowadzi wyrażenie matematyczne lub kwerendę konwersji jednostkowej, odpowiedź może zawierać odpowiedź [obliczeniową](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#computation) . `computation`Odpowiedź zawiera wyrażenie znormalizowane i jego wynik.

Zapytanie konwersji jednostkowej to zapytanie, które konwertuje jedną jednostkę na inną. Na przykład, *ile stóp w 10 metrów?* lub *ile tablespoons w filiżanki 1/4?*

Poniżej przedstawiono `computation` odpowiedzi na liczbę metrów *w 10 pomiarach?*

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "10 meters",
    "value": "32.808399 feet"
}, ...
```

Poniżej przedstawiono przykłady zapytań matematycznych i odpowiadające im `computation` odpowiedzi.

```
Query: (5+3)(10/2)+8
Encoded query: %285%2B3%29%2810%2F2%29%2B8
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "((5+3)*(10\/2))+8",
    "value": "48"
}
```

```
Query: sqrt(4^2+8^2)
Encoded query: sqrt%284^2%2B8^2%29
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "sqrt((4^2)+(8^2))",
    "value": "8.94427191"
}
```

```
Query: 30 6/8 - 18 8/16
Encoded query: 30%206%2F8%20-%2018%208%2F16
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#WolframAlpha",
    "expression": "30 6\/8-18 8\/16",
    "value": "12.25"
}
```

```
Query: 8^2+11^2-2*8*11*cos(37)
Encoded query: 8^2%2B11^2-2*8*11*cos%2837%29
```

```json
"computation": {
        "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
        "expression": "(8^2)+(11^2)-(2*8*11*cos(37))",
        "value": "44.4401502"
}
```

Wyrażenie matematyczne może zawierać następujące symbole:

|Symbol|Opis|
|------------|-----------------|
|+|Dodawanie|
|-|Odejmowanie|
|/|Dział|
|*|Znak mnożenia|
|^|Zasilanie|
|!|Silnia|
|.|Liczba dziesiętna|
|()|Grupowanie pierwszeństwa|
|[]|Funkcja|

Wyrażenie matematyczne może zawierać następujące stałe:

|Symbol|Opis|
|------------|-----------------|
|Pi|3,14159...|
|Stopień|Stopień|
|mogę|Liczba urojona|
|e|e, 2,71828...|
|GoldenRatio|Stosunek złota, 1,61803...|

Wyrażenie matematyczne może zawierać następujące funkcje:

|Symbol|Opis|
|------------|-----------------|
|Sortowanie|Pierwiastek kwadratowy|
|Sin [x], cos [x], Tan [x]<br />CSC [x], s [x], COT [x]|Funkcje trygonometryczne (z argumentami w radianach)|
|Łuki [x], ArcCos [x], ArcTan [x]<br />ArcCsc [x], ArcSec [x], ArcCot [x]|Odwrotne funkcje trygonometryczne (dające wyniki w radianach)|
|EXP [x], E ^ x|Funkcja wykładnicza|
|Dziennik [x]|Logarytm naturalny|
|SINH [x], cosh — [x], tanh [x]<br />Csch [x], sech [x], coth [x]|Funkcje hiperboliczne|
|ArcSinh [x], ArcCosh [x], ArcTanh [x]<br />ArcCsch [x], ArcSech [x], ArcCoth [x]|Odwrotne Funkcje hiperboliczne|

Wyrażenia matematyczne zawierające zmienne (na przykład 4x + 6 = 18, gdzie x jest zmienną) nie są obsługiwane.

## <a name="timezone-answer"></a>Odpowiedź strefy czasowej

Jeśli użytkownik wprowadzi zapytanie o godzinę lub datę, odpowiedź może zawierać odpowiedź [strefy](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#timezone) czasowej. Ta odpowiedź obsługuje zapytania niejawne lub jawne. Niejawne zapytanie, na przykład *jego czas trwania?*, zwraca czas lokalny na podstawie lokalizacji użytkownika. Jawne zapytanie, na przykład *jaki jest czas w Seattle?*, zwraca czas lokalny dla Seattle, WA.

`timeZone`Odpowiedź zawiera nazwę lokalizacji, bieżącą datę i godzinę UTC w określonej lokalizacji oraz przesunięcie czasu UTC. Jeśli granica lokalizacji znajduje się w wielu strefach czasowych, odpowiedź zawiera bieżącą datę i godzinę UTC wszystkich stref czasowych w ramach granicy. Na przykład, ponieważ stan Florida jest w dwóch strefach czasowych, odpowiedź zawiera lokalną datę i godzinę obu stref czasowych.  

Jeśli zapytanie żąda czasu stanu lub kraju/regionu, Bing określa główne miasto w granicach geograficznych lokalizacji i zwraca je w `primaryCityTime` polu. Jeśli granica zawiera wiele stref czasowych, pozostałe strefy czasowe są zwracane w `otherCityTimes` polu.

Poniżej przedstawiono przykładowe zapytania, które zwracają `timeZone` odpowiedź.

```
Query: What time is it?

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Redmond, Washington, United States",
        "time": "2015-10-27T08:38:12.1189231Z",
        "utcOffset": "UTC-7"
    }
}

Query: What time is it in the Pacific time zone?

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Pacific Time Zone",
        "time": "2015-10-23T12:33:19.0728146Z",
        "utcOffset": "UTC-7"
    }
}

Query: Time in Florida?

"timeZone": {
        "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
        "primaryCityTime": {
            "location": "Tallahassee, Florida, United States",
            "time": "2015-10-23T13:04:56.6774389Z",
            "utcOffset": "UTC-4"
        },
        "otherCityTimes": [{
            "location": "Pensacola",
            "time": "2015-10-23T12:04:56.6664294Z",
            "utcOffset": "UTC-5"
        }]
}

Query: What time is it in the U.S.

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Washington, D.C., United States",
        "time": "2015-10-23T15:27:59.8892745Z",
        "utcOffset": "UTC-4"
    },
    "otherCityTimes": [{
        "location": "Honolulu",
        "time": "2015-10-23T09:27:59.8892745Z",
        "utcOffset": "UTC-10"
    },
    {
        "location": "Anchorage",
        "time": "2015-10-23T11:27:59.8892745Z",
        "utcOffset": "UTC-8"
    },
    {
        "location": "Phoenix",
        "time": "2015-10-23T12:27:59.8892745Z",
        "utcOffset": "UTC-7"
    },
    {
        "location": "Los Angeles",
        "time": "2015-10-23T12:27:59.8942788Z",
        "utcOffset": "UTC-7"
    },
    {
        "location": "Denver",
        "time": "2015-10-23T13:27:59.8812681Z",
        "utcOffset": "UTC-6"
    },
    {
        "location": "Chicago",
        "time": "2015-10-23T14:27:59.8892745Z",
        "utcOffset": "UTC-5"
    }]
}
```

## <a name="spellsuggestion-answer"></a>Odpowiedź SpellSuggestion

Jeśli Bing określi, że użytkownik może chcieć wyszukać coś innego, odpowiedź zawiera obiekt [SpellSuggestions](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#spellsuggestions) . Na przykład, jeśli użytkownik wyszukuje *pióro Carlos*, Bing może określić, że użytkownik prawdopodobnie przeszukał Carlos Pena zamiast tego (na podstawie przeszłych wyszukiwań innych osób z *Carlos piórem*). Poniżej przedstawiono przykładową odpowiedź na pisownię.

```json
"spellSuggestions": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#SpellSuggestions",
    "value": [{
        "text": "carlos pena",
        "displayText": "carlos pena"
    }]
}, ...
```

## <a name="response-headers"></a>Nagłówki odpowiedzi

Odpowiedzi z interfejs API wyszukiwania w sieci Web Bing mogą zawierać następujące nagłówki:

| Nagłówek | Opis |
|-|-|
|`X-MSEdge-ClientID`|Unikatowy identyfikator, który jest przypisany do użytkownika usługi Bing|
|`BingAPIs-Market`|Rynek używany do realizacji żądania|
|`BingAPIs-TraceId`|Wpis dziennika na serwerze interfejsu API Bing dla tego żądania (na potrzeby obsługi)|

Jest to szczególnie ważne, aby zachować identyfikator klienta i zwrócić go na kolejne żądania. Gdy to zrobisz, wyszukiwanie będzie używać przeszłego kontekstu w celu określania rankingu wyników wyszukiwania, a także zapewnienia spójnego środowiska użytkownika.

Jednak po wywołaniu interfejs API wyszukiwania w sieci Web Bing w języku JavaScript wbudowane funkcje zabezpieczeń (CORS) przeglądarki mogą uniemożliwiać dostęp do wartości tych nagłówków.

Aby uzyskać dostęp do nagłówków, można wykonać żądanie interfejs API wyszukiwania w sieci Web Bing za pomocą serwera proxy CORS. Odpowiedź z takiego serwera proxy ma `Access-Control-Expose-Headers` nagłówek, który filtruje nagłówki odpowiedzi i udostępnia je skryptom JavaScript.

Można łatwo zainstalować serwer proxy CORS, aby umożliwić naszej [aplikacji samouczka](tutorial-bing-web-search-single-page-app.md) dostęp do opcjonalnych nagłówków klienta. Najpierw [zainstaluj platformę Node.js](https://nodejs.org/en/download/), jeśli jeszcze jej nie masz. Potem wprowadź poniższe polecenie w wierszu polecenia.

```console
npm install -g cors-proxy-server
```

Następnie zmień interfejs API wyszukiwania w sieci Web Bing punkt końcowy w pliku HTML na: \
`http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search`

Na koniec uruchom serwer proxy CORS za pomocą następującego polecenia:

```console
cors-proxy-server
```

Podczas korzystania z aplikacji samouczka pozostaw okno polecenia otwarte, ponieważ jego zamknięcie spowoduje zatrzymanie serwera proxy. W rozwijanej sekcji nagłówków HTML poniżej wyników wyszukiwania można teraz zobaczyć nagłówek `X-MSEdge-ClientID` (pomiędzy innymi) i sprawdzić, czy jest on taki sam dla każdego żądania.

## <a name="response-headers-in-production"></a>Nagłówki odpowiedzi w środowisku produkcyjnym

Podejście proxy CORS opisane w poprzedniej odpowiedzi jest odpowiednie do programowania, testowania i uczenia się.

W środowisku produkcyjnym należy hostować skrypt po stronie serwera w tej samej domenie, w której znajduje się Strona sieci Web, która używa interfejs API wyszukiwania w sieci Web Bing. Ten skrypt powinien wykonywać wywołania interfejsu API na żądanie ze strony sieci Web JavaScript i przekazać wszystkie wyniki, włącznie z nagłówkami, z powrotem do klienta. Ponieważ dwa zasoby (strona i skrypt) współużytkują źródło, funkcja CORS nie jest używana, a specjalne nagłówki są dostępne dla języka JavaScript na stronie sieci Web.

Takie podejście umożliwia również ochronę klucza interfejsu API przed ekspozycją publiczną, ponieważ tylko skrypt po stronie serwera wymaga tego. Skrypt może użyć innej metody, aby upewnić się, że żądanie jest autoryzowane.

Poniżej przedstawiono sposób korzystania z sugestii pisowni w usłudze Bing.

![Przykład sugestii pisowni Bing](./media/cognitive-services-bing-web-api/bing-web-spellingsuggestion.GIF)  

## <a name="next-steps"></a>Następne kroki  

* Przejrzyj dokumentację dotyczącą [ograniczania żądań](throttling-requests.md) .  

## <a name="see-also"></a>Zobacz też  

* [Informacje interfejs API wyszukiwania w sieci Web Bing](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)