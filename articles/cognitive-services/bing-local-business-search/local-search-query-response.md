---
title: Wysyłanie i używanie zapytań API i odpowiedzi — wyszukiwanie lokalne w usłudze Bing
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego artykułu, aby dowiedzieć się, jak wysyłać zapytania wyszukiwania i korzystać z nich przy użyciu interfejsu API wyszukiwania lokalnego usługi Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: rosh
ms.openlocfilehash: 8e26d128fea7208206be8a7329522f8202051215
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095411"
---
# <a name="sending-and-using-bing-local-business-search-api-queries-and-responses"></a>Wysyłanie zapytań i odpowiedzi interfejsu API wyszukiwania lokalnego usługi Bing oraz korzystanie z nich

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](https://aka.ms/cogsvcs/bingmove).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Umowa Enterprise, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](https://aka.ms/cogsvcs/bingmigration).

Możesz uzyskać wyniki lokalne z interfejsu API wyszukiwania lokalnego usługi Bing, wysyłając zapytanie wyszukiwania do jego punktu końcowego i uwzględniając `Ocp-Apim-Subscription-Key` nagłówek, który jest wymagany. Oprócz dostępnych [nagłówków](local-search-reference.md#headers) i [parametrów](local-search-reference.md#query-parameters)wyszukiwania można dostosować, określając [granice geograficzne](specify-geographic-search.md) dla obszaru, który ma być przeszukiwany, oraz [Kategorie](local-search-query-response.md) zwracanych miejsc.

## <a name="creating-a-request"></a>Tworzenie żądania

Aby wysłać żądanie do interfejsu API wyszukiwania lokalnego usługi Bing, Dołącz termin wyszukiwania do `q=` parametru przed dodaniem go do punktu końcowego interfejsu API, a w tym `Ocp-Apim-Subscription-Key` nagłówek. Przykład:

`https://api.cognitive.microsoft.com/bing/localbusinesses/v7.0/search?q=restaurant+in+Bellevue`

Poniżej przedstawiono składnię adresu URL pełnej żądania. Aby uzyskać więcej informacji na temat wysyłania żądań, zobacz [Przewodnik Szybki Start](quickstarts/local-quickstart.md)dotyczący interfejsu API wyszukiwania w usłudze Bing — informacje szczegółowe i informacje o [nagłówkach](local-search-reference.md#headers) i [parametrach](local-search-reference.md#query-parameters) . 

Aby uzyskać informacje na temat lokalnych kategorii wyszukiwania, zobacz [kategorie wyszukiwania dla interfejsu API wyszukiwania lokalnego usługi Bing](local-categories.md).

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search[?q][&localCategories][&cc][&mkt][&safesearch][&setlang][&count][&first][&localCircularView][&localMapView]
```

## <a name="using-responses"></a>Korzystanie z odpowiedzi

Odpowiedzi JSON z interfejsu API wyszukiwania lokalnego usługi Bing zawierają `SearchResponse` obiekt. Interfejs API zwróci odpowiednie wyniki wyszukiwania w `places` polu. Jeśli nie zostaną znalezione żadne wyniki, `places` pole nie zostanie uwzględnione w odpowiedzi.

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

```
{
   "_type": "SearchResponse",
   "queryContext": {
      "originalQuery": "restaurant in Bellevue"
   },
   "places": {
      "totalEstimatedMatches": 10,
. . . 
```

### <a name="search-result-attributes"></a>Atrybuty wyników wyszukiwania

Wyniki JSON zwrócone przez interfejs API zawierają następujące atrybuty:

* _type
* adres
* entityPresentationInfo
* geograficznie
* identyfikator
* name
* routeablePoint
* telefon
* url

Aby uzyskać ogólne informacje o nagłówkach, parametrach, kodach rynku, obiektach odpowiedzi, błędach itp., zobacz Kompendium [wersji 7 interfejsu API wyszukiwania lokalnego Bing](local-search-reference.md) .

> [!NOTE]
> Użytkownik lub osoba trzecia w Twoim imieniu może nie używać, przechowywać, zapisywać, buforować, udostępniać lub rozpowszechniać dowolne dane z lokalnego interfejsu API wyszukiwania na potrzeby testowania, opracowywania, uczenia, rozpowszechniania lub udostępniania dowolnej usługi lub funkcji firmy innej niż Microsoft. 


## <a name="example-json-response"></a>Przykładowa odpowiedź JSON

Następująca odpowiedź JSON zawiera wyniki wyszukiwania określone przez zapytanie `?q=restaurant+in+Bellevue` .

```json
Vary: Accept-Encoding
BingAPIs-TraceId: 5376FFEB65294E24BB9F91AD70545826
BingAPIs-SessionId: 06ED7CEC80F746AA892EDAAC97CB0CB4
X-MSEdge-ClientID: 112C391E72C0624204153594738C63DE
X-MSAPI-UserState: aeab
BingAPIs-Market: en-US
X-Search-ResponseInfo: InternalResponseTime=659,MSDatacenter=CO4
X-MSEdge-Ref: Ref A: 5376FFEB65294E24BB9F91AD70545826 Ref B: BY3EDGE0306 Ref C: 2018-10-16T16:26:15Z
apim-request-id: fe54f585-7c54-4bf5-8b92-b9bede2b710a
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
x-content-type-options: nosniff
Cache-Control: max-age=0, private
Date: Tue, 16 Oct 2018 16:26:15 GMT
P3P: CP="NON UNI COM NAV STA LOC CURa DEVa PSAa PSDa OUR IND"
Content-Length: 978
Content-Type: application/json; charset=utf-8
Expires: Tue, 16 Oct 2018 16:25:15 GMT

{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "restaurant Bellevue"
  },
  "places": {
    "totalEstimatedMatches": 50,
    "value": [{
      "_type": "LocalBusiness",
      "id": "https:\/\/cognitivegblppe.azure-api.net\/api\/v7\/#Places.0",
      "name": "Facing East Taiwanese Restaurant",
      "url": "http:\/\/litadesign.wix.com\/facingeastrestaurant",
      "entityPresentationInfo": {
        "entityScenario": "ListItem",
        "entityTypeHints": ["Place", "LocalBusiness", "Restaurant"]
      },
      "geo": {
        "latitude": 47.6199188232422,
        "longitude": -122.202796936035
      },
      "routablePoint": {
        "latitude": 47.6199188232422,
        "longitude": -122.201713562012
      },
      "address": {
        "streetAddress": "1075 Bellevue Way NE Ste B2",
        "addressLocality": "Bellevue",
        "addressRegion": "WA",
        "postalCode": "98004",
        "addressCountry": "US",
        "neighborhood": "Bellevue",
        "text": "1075 Bellevue Way NE Ste B2, Bellevue, WA 98004"
      },
      "telephone": "(425) 688-2986"
    }],
    "searchAction": {
      "location": [{
        "name": "Bellevue, Washington"
      }],
      "query": "restaurant"
    }
  }
}
 
```

## <a name="throttling-requests"></a>Ograniczanie żądań

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]


## <a name="next-steps"></a>Następne kroki
- [Lokalne wyszukiwanie biznesowe — Szybki Start](quickstarts/local-quickstart.md)
- [Lokalne wyszukiwanie biznesowe w języku Java — Szybki Start](quickstarts/local-search-java-quickstart.md)
- [Lokalny węzeł wyszukiwania biznesowego — Szybki Start](quickstarts/local-search-node-quickstart.md)
- [Lokalne wyszukiwanie biznesowe w języku Python — Szybki Start](quickstarts/local-search-python-quickstart.md)
