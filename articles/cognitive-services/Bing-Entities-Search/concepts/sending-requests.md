---
title: Wysyłanie żądań wyszukiwania do interfejsu API wyszukiwania jednostek Bing
titleSuffix: Azure cognitive Services
description: Interfejs API wyszukiwania jednostek Bing wysyła zapytanie wyszukiwania do usługi Bing i pobiera wyniki, które zawierają jednostki i miejsca.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: aahi
ms.openlocfilehash: b290539bf6424895a9685c1e4cdb53d8fb8cfb41
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2020
ms.locfileid: "94365911"
---
# <a name="sending-search-requests-to-the-bing-entity-search-api"></a>Wysyłanie żądań wyszukiwania do interfejsu API wyszukiwania jednostek Bing

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](https://aka.ms/cogsvcs/bingmove).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Umowa Enterprise, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](https://aka.ms/cogsvcs/bingmigration).

Interfejs API wyszukiwania jednostek Bing wysyła zapytanie wyszukiwania do usługi Bing i pobiera wyniki, które zawierają jednostki i miejsca. Wyniki dotyczące miejsc to między innymi restauracje, hotele i inne lokalne firmy. W zapytaniu można na przykład określić nazwę lokalnej firmy lub zadać pytanie o listę miejsc (na przykład „restaurants near me” — restauracje w pobliżu). Wyniki dotyczące jednostek to między innymi osoby, miejsca i rzeczy. Miejsce w tym kontekście to attractions, Stany, kraje/regiony itd.

## <a name="the-endpoint"></a>Punkt końcowy

Aby uzyskać wyniki wyszukiwania jednostek i miejsc, należy wysłać żądanie GET do następującego punktu końcowego:  

```
https://api.cognitive.microsoft.com/bing/v7.0/entities
```

Żądania muszą używać protokołu HTTPS.

Zalecamy, aby wszystkie żądania pochodziły z serwera. Dystrybuowanie klucza w ramach aplikacji klienckiej dostarcza więcej okazji do przejęcia go przez złośliwy kod innych firm. Ponadto wykonywanie wywołań z serwera zapewnia jeden punkt uaktualniania dla przyszłych wersji interfejsu API.

## <a name="specifying-query-parameters-and-headers"></a>Określanie parametrów zapytania i nagłówków

Żądanie musi określać parametr zapytania [q](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query), który zawiera wyszukiwany termin użytkownika. Żądanie musi również określać parametr zapytania [mkt](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#mkt) identyfikujący rynek, z którego mają pochodzić wyniki. Aby uzyskać listę opcjonalnych parametrów zapytania, zobacz [Query Parameters (Parametry zapytania)](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters). Wszystkie parametry zapytania muszą być kodowane w formacie URL.  
  
Żądanie musi określać nagłówek [Ocp-Apim-Subscription-Key](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#subscriptionkey). Mimo że jest to opcjonalne, zachęcamy, aby określić również następujące nagłówki:  
  
-   [User-Agent](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#clientid)  
-   [X-MSEdge-ClientIP](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#clientip)  
-   [X-Search-Location](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#location)  

Nagłówki adresu IP klienta i lokalizacji są ważne z punktu widzenia zwracania zawartości odpowiedniej dla danej lokalizacji.  

Aby uzyskać listę wszystkich nagłówków żądań i odpowiedzi, zobacz [Nagłówki](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers).

## <a name="the-request"></a>Żądanie

Poniżej przedstawiono żądanie wyszukiwania jednostek, które zawiera wszystkie sugerowane parametry zapytania i nagłówki. 

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/entities?q=mount+rainier&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Jeśli jest to Twoje pierwsze wywoływanie dowolnego z interfejsów API Bing, nie dołączaj nagłówka identyfikatora klienta. Identyfikator klienta należy uwzględnić tylko wtedy, gdy interfejs API Bing został już wywołany i usługa Bing zwróciła identyfikator klienta dla kombinacji użytkownika i urządzenia.

## <a name="the-response"></a>Odpowiedź

Poniżej przedstawiono odpowiedź na poprzednie żądanie. W przykładzie pokazano również nagłówki odpowiedzi specyficzne dla usługi Bing. Aby uzyskać informacji o obiekcie odpowiedzi, zobacz [SearchResponse](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#searchresponse).

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "mount rainier"
    },
    "entities" : {
        "queryScenario" : "DominantEntity",
        "value" : [{
            "contractualRules" : [{
                "_type" : "ContractualRules\/LicenseAttribution",
                "targetPropertyName" : "description",
                "mustBeCloseToContent" : true,
                "license" : {
                    "name" : "CC-BY-SA",
                    "url" : "http:\/\/creativecommons.org\/licenses\/by-sa\/3.0\/"
                },
                "licenseNotice" : "Text under CC-BY-SA license"
            },
            {
                "_type" : "ContractualRules\/LinkAttribution",
                "targetPropertyName" : "description",
                "mustBeCloseToContent" : true,
                "text" : "en.wikipedia.org",
                "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
            },
            {
                "_type" : "ContractualRules\/MediaAttribution",
                "targetPropertyName" : "image",
                "mustBeCloseToContent" : true,
                "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
            }],
            "webSearchUrl" : "https:\/\/www.bing.com\/search?q=Mount%20Rainier...",
            "name" : "Mount Rainier",
            "image" : {
                "name" : "Mount Rainier",
                "thumbnailUrl" : "https:\/\/www.bing.com\/th?id=A21890c0e1f...",
                "provider" : [{
                    "_type" : "Organization",
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
                }],
                "hostPageUrl" : "http:\/\/upload.wikimedia.org\/wikipedia...",
                "width" : 110,
                "height" : 110
            },
            "description" : "Mount Rainier, Mount Tacoma, or Mount Tahoma is the highest...",
            "entityPresentationInfo" : {
                "entityScenario" : "DominantEntity",
                "entityTypeHints" : ["Attraction"],
                "entityTypeDisplayHint" : "Mountain"
            },
            "bingId" : "9ae3e6ca-81ea-6fa1-ffa0-42e1d78906"
        }]
    }
}
```


## <a name="next-steps"></a>Następne kroki

* [Wyszukiwanie jednostek za pomocą interfejsu API jednostek Bing](search-for-entities.md)
* [Wymagania dotyczące użycia i wyświetlania interfejsu API Bing](../../bing-web-search/use-display-requirements.md)