---
title: Wyszukaj w sieci Web trendy dotyczące trendów wideo przy użyciu interfejs API wyszukiwania wideo Bing
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak za pomocą interfejs API wyszukiwania wideo Bing przeszukać w Internecie trendy dotyczące trendów wideo.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: 8232af6cb409628a1066a044a196777ddc46348f
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93098971"
---
# <a name="get-trending-videos-with-the-bing-video-search-api"></a>Zyskaj trendy wideo przy użyciu interfejs API wyszukiwania wideo Bing 

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](https://aka.ms/cogsvcs/bingmove).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Umowa Enterprise, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](https://aka.ms/cogsvcs/bingmigration).

Interfejs API wyszukiwania wideo Bing umożliwia znalezienie dzisiejszych trendów wideo z poziomu sieci Web, a w różnych kategoriach. 

## <a name="get-request"></a>Żądanie GET

Aby uzyskać aktualne trendy wideo z interfejs API wyszukiwania wideo Bing, wyślij następujące żądanie GET:  
  
```cURL
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="market-support"></a>Wsparcie dla rynku

Poniższe rynki obsługują trendy dotyczące trendów wideo.  
 
-   en-AU (angielski, Australia)  
-   EN-CA (angielski, Kanada)  
-   pl-GB (angielski, Wielka Brytania)  
-   EN-ID (angielski, Indonezja)  
-   EN-IE (angielski, Irlandia)  
-   pl (angielski, Indie)  
-   EN-NZ (angielski, Nowa Zelandia)  
-   EN-PH (angielski, Filipiny)  
-   EN-SG (angielski, Singapur)  
-   pl-US (angielski, Stany Zjednoczone)  
-   EN-WW (angielski, ogólnoświatowy kod agregacji)  
-   pl-za (angielski, RPA)  
-   zh-CN (chiński, Chiny)

## <a name="example-json-response"></a>Przykładowa odpowiedź JSON  

Poniższy przykład przedstawia odpowiedź interfejsu API, która zawiera trendy wideo, które są wyświetlane według kategorii i podkategorii. Odpowiedź zawiera również wideo transparentów, które są najpopularniejszymi trendami wideo i mogą pochodzić z jednej lub kilku kategorii.  

```json
{  
    "_type" : "TrendingVideos",  
    "bannerTiles" : [
        {  
            "query" : {  
                "text" : "Hello - Smith",  
                "displayText" : "Hello - Smith",  
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3E8F5..."
            },  
            "image" : {  
                "description" : "Image from: contosowallpapers.com",  
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=RsA%2fdPlTmx4zS...",  
                "headLine" : "\"Hello\" is a song by...",  
                "contentUrl" : "http:\/\/www.contosowallpapers.com\/wp-content..."  
            }  
        },  
        . . .  
    ],  
    "categories" : [
        {  
            "title" : "Music videos",  
            "subcategories" : [
                {  
                    "tiles" : [
                        {  
                            "query" : {  
                                "text" : "Song Title - Artist Name",  
                                "displayText" : "Song Title - Artist Name",  
                                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3E8F5..."
                            },  
                            "image" : {  
                                "description" : "Image from: contoso.com",  
                                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=...",  
                                "contentUrl" : "http:\/\/images6.contoso.com\/image..."  
                            }  
                        },  
                        . . .  
                    ],
                    "title" : "Top "  
                },
                {
                    "tiles" : [...],
                    "title" : "Trending"
                },
                . . .
            ],  
        },
        {
            "title" : "Viral videos",
            "subcategories" : [
                {
                    "tiles" : [...],
                    "title" : "Trending"
                },
                . . .
            ],  
        },
        . . .  
    ]  
}  
  
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Pobieranie szczegółowych informacji o wideo](video-insights.md)