---
title: Pobieranie obrazów trendów z interfejs API wyszukiwania obrazów Bing
titleSuffix: Azure Cognitive Services
description: Wyszukaj dzisiejsze obrazy trendów z witryny sieci Web za pomocą interfejs API wyszukiwania obrazów Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: EAB92D35-5C0B-4A0A-8F49-02DF7FAD44B4
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: cf7d1baf895d44730eb913b658ee4c7fe7eb7b11
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96341618"
---
# <a name="get-trending-images-from-the-web"></a>Pobieranie obrazów trendów z sieci Web

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Enterprise Agreement, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Aby uzyskać dzisiejsze obrazy trendów, wyślij następujące żądanie GET:  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending?mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Interfejs API obrazów trendów obsługuje obecnie tylko następujące rynki:  

- pl-US (angielski, Stany Zjednoczone)  
- EN-CA (angielski, Kanada)  
- en-AU (angielski, Australia)  
- zh-CN (chiński, Chiny)

Odpowiedź zawiera obiekt [TrendingImages](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#trendingimages) , który wyświetla listę obrazów według kategorii. Użyj kategorii, `title` Aby grupować obrazy w środowisku użytkownika. Kategorie mogą zmieniać się codziennie.  

```json
{
    "_type" : "TrendingImages",  
    "categories" : [{  
        "title" : "Popular people searches",  
        "tiles" : [{  
            "query" : {  
                "text" : "Smith",  
                "displayText" : "Mr. Smith",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=smith&FORM=..."
            },  
            "image" : {  
                "id" : "C3C60AE779A054D5CD80D3CACF0F3",  
                "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OIP.M2532...",  
                "contentUrl" : "http:\/\/www.contoso.com.au\/assets\/Uploads\/smith-SH01.jpg",  
                "thumbnail" : {  
                    "width" : 288,  
                    "height" : 300  
                }  
            }  
        },  
        . . .  
        ]  
    },  
    . . .  
    {  
        "title" : "Popular Halloween searches",  
        "tiles" : [{  
            "query" : {  
                "text" : "Halloween costumes for adults",  
                "displayText" : "Halloween costumes for adults",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Halloween+costumes..."
            },  
            "image" : {  
                "id" : "0F3395F2983003F89DCEE711B55D7FA53E4",  
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OIP.Me429c...",  
                "contentUrl" : "http:\/\/images.domain.com\/products\/8179\/1-1\/adult-squirrel...",  
                "thumbnail" : {  
                    "width" : 336,  
                    "height" : 480  
                }  
            }  
        }]  
    }]  
}  
```  

Każdy kafelek zawiera obraz i opcje pobierania powiązanych obrazów. Aby uzyskać powiązane obrazy, można użyć zapytania `text` do wywołania [interfejsu API wyszukiwanie obrazów](./overview.md) i samodzielnie wyświetlić powiązane obrazy. Można też użyć adresu URL w programie, `webSearchUrl` Aby uzyskać dostęp do strony wyników wyszukiwania obrazów w usłudze Bing, która zawiera powiązane obrazy.

Jeśli wywołasz interfejs API wyszukiwanie obrazów, aby uzyskać powiązane obrazy, ustaw parametr [ID](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#id) zapytania na identyfikator w `id` polu. Określenie identyfikatora zapewnia, że odpowiedź zawiera obraz (jest to pierwszy obraz w odpowiedzi) i powiązane z nim obrazy. Ponadto należy ustawić parametr zapytania [q](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) na tekst w `query` `text` polu obiektu.

Poniższy przykład pokazuje, jak używać identyfikatora obrazu do uzyskiwania pokrewnych obrazów Mr. Smith w poprzedniej odpowiedzi interfejsu API obrazów trendów.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=Smith&id=77FDE4A1C6529A23C7CF0EC073FAA64843E828F2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```