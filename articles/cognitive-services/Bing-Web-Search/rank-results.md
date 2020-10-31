---
title: Jak używać klasyfikacji do wyświetlania wyników wyszukiwania — interfejs API wyszukiwania w sieci Web Bing
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać klasyfikacji do wyświetlania wyników wyszukiwania z interfejs API wyszukiwania w sieci Web Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: BBF87972-B6C3-4910-BB52-DE90893F6C71
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/17/2019
ms.author: scottwhi
ms.openlocfilehash: 6c328c681874ba171eab1341a16cf059e359feea
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93076282"
---
# <a name="how-to-use-ranking-to-display-bing-web-search-api-results"></a>Jak używać klasyfikacji do wyświetlania wyników interfejs API wyszukiwania w sieci Web Bing  

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](https://aka.ms/cogsvcs/bingmove).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Umowa Enterprise, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](https://aka.ms/cogsvcs/bingmigration).

Każda odpowiedź wyszukiwania zawiera odpowiedź [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse) , która określa, jak należy wyświetlić wyniki wyszukiwania. Grupy odpowiedzi rankingu są wynikiem według zawartości linii głównej i zawartości paska bocznego dla tradycyjnej strony wyników wyszukiwania. Jeśli wyniki nie są wyświetlane w tradycyjnym formacie linii głównej i paska bocznego, należy udostępnić zawartość linii głównej wyższą widoczność niż zawartość paska bocznego.  

W każdej grupie (linii głównej lub Sidebar) tablica [Items](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankinggroup-items) identyfikuje kolejność, w której zawartość musi być wyświetlana. Każdy element zawiera następujące dwa sposoby identyfikacji wyniku w odpowiedzi.  

-   `answerType` i `resultIndex` — `answerType` pole Identyfikuje odpowiedź (na przykład stronę sieci Web lub wiadomości) i `resultIndex` identyfikuje wynik w odpowiedzi (na przykład artykuł wiadomości). Indeks jest oparty na zero.  

-   `value` — `value` Pole zawiera identyfikator, który odpowiada identyfikatorowi odpowiedzi lub wynikowi w odpowiedzi. Odpowiedź lub wyniki zawierają identyfikator, ale nie oba jednocześnie.  

Korzystanie z tego identyfikatora jest prostsze, ponieważ wystarczy dopasować identyfikator klasyfikacji z IDENTYFIKATORem odpowiedzi lub jednym z jej wyników. Jeśli obiekt odpowiedzi zawiera `id` pole, Wyświetl wszystkie wyniki odpowiedzi ze sobą. Na przykład jeśli `News` obiekt zawiera `id` pole, Wyświetl wszystkie artykuły z wiadomościami ze sobą. Jeśli `News` obiekt nie zawiera `id` pola, każdy artykuł z wiadomości zawiera `id` pole, a odpowiedź dotycząca rankingu łączy artykuły z wiadomościami z wynikami z innych odpowiedzi.  

Korzystanie z `answerType` i `resultIndex` jest nieco bardziej skomplikowane. Służy `answerType` do identyfikowania odpowiedzi, która zawiera wyniki do wyświetlenia. Następnie użyj `resultIndex` do indeksowania wyników odpowiedzi, aby uzyskać wynik do wyświetlenia. ( `answerType` Wartość jest nazwą pola w obiekcie [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse) ). Jeśli chcesz wyświetlić wszystkie wyniki odpowiedzi razem, element odpowiedzi rankingu nie zawiera `resultIndex` pola.  

## <a name="ranking-response-example"></a>Przykład klasyfikacji odpowiedzi

Poniżej przedstawiono przykład [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse). Ponieważ odpowiedź sieci Web nie zawiera `id` pola, wszystkie strony internetowe są wyświetlane indywidualnie na podstawie klasyfikacji (każda Strona sieci Web zawiera `id` pole). Ponieważ obrazy, klipy wideo i powiązane wyszukiwania odpowiedzi zawierają `id` pole, wyniki każdej z tych odpowiedzi są wyświetlane razem na podstawie klasyfikacji.

```json
{  
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214...",
        "totalEstimatedMatches" : 835000,
        "value" : [
            {
                "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
                "name" : "Motor Sports - Live at the race track ...",
                "url" : "http:\/\/www.bing.com\/cr?IG=96C4CF214A0A43...",
                "displayUrl" : "www.contoso.com\/usa\/eventsandracing\/motorsport",
                "snippet" : "Here you will find detailed information about racing...",
                "deepLinks" : [{
                    "name" : "Customer Racing",
                    "url" : "http:\/\/www.bing.com\/cr?IG=96C4CF214A0A43...",
                    "snippet" : "Customer racing news; General news..."
            },
            . . .  
        ]  
    }],  
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214A...",
        "isFamilyFriendly" : true,
        "value" : [
            {
                "name" : "2016 Supercar Wallpapers",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP...",
                "datePublished" : "2017-03-25T11:14:00",
                "contentUrl" : "http:\/\/www.contoso.com\/wall...",
                "hostPageUrl" : "http:\/\/www.bing.com\/cr?IG=96C4CF214...",
                "contentSize" : "373283 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "http:\/\/www.contoso.com\/lmp-...",
                "width" : 1920,
                "height" : 1080,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 168
                },
                "insightsSourcesSummary" : {
                    "shoppingSourcesCount" : 0,
                    "recipeSourcesCount" : 0
                }
            },
            . . .  
        ]  
    },  
    "relatedSearches" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches",
        "value" : [
            {
                "text" : "vintage racing teams",
                "displayText" : "vintage racing teams",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF2..."
            },
            . . .  
        ]  
    },  
    "videos" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Videos",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/videos...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214A...",
        "isFamilyFriendly" : true,
        "value" : [
            {
                "name" : "Why We Race",
                "description" : "A new era begins in motorsports this weekend...",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF2...",
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.Vo1...",
                "datePublished" : "2014-01-25T16:31:48",
                "publisher" : [
                    {
                        "name" : "Fabrikam"
                    }
                ],
                "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=oL...",
                "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214...",
                "encodingFormat" : "mp4",
                "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=oLAZgD...",
                "width" : 480,
                "height" : 360,
                "duration" : "PT2M42S",
                "motionThumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OM...",
                "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"http:\/\/www.you...<\/iframe>",
                "allowHttpsEmbed" : true,
                "viewCount" : 47325,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 168
                },
                "allowMobileEmbed" : true,
                "isSuperfresh" : false
            },
            . . .  
        ]  
    },  
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "WebPages",
                "resultIndex" : 0,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0"
                }
            },
            {
                "answerType" : "Images",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 1,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.1"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 2,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.2"
                }
            },
            {
                "answerType" : "Videos",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Videos"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 3,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.3"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 4,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.4"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 5,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.5"
                }
            }]
        },
        "sidebar" : {
            "items" : [{
                "answerType" : "RelatedSearches",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches"
                }
            }]
        }
    }
}  
```  

W oparciu o tę odpowiedź klasyfikacji linii głównej będzie wyświetlał następujące wyniki wyszukiwania:  

-   Pierwszy wynik strony sieci Web
-   Wszystkie obrazy  
-   Druga i trzecia wyniki strony sieci Web  
-   Wszystkie filmy wideo  
-   Czwarty, piąty i szósty wynik strony sieci Web  

Na pasku bocznym będą wyświetlane następujące wyniki wyszukiwania:  

-   Wszystkie powiązane wyszukiwania  


## <a name="next-steps"></a>Następne kroki

Informacje dotyczące promowania niesklasyfikowanych wyników można znaleźć w temacie [promowanie odpowiedzi, które nie są klasyfikowane](./filter-answers.md#promoting-answers-that-are-not-ranked).

Aby uzyskać informacje na temat ograniczania liczby odpowiedzi z rankingu do odpowiedzi, zobacz [ograniczanie liczby odpowiedzi w odpowiedzi](./filter-answers.md#limiting-the-number-of-answers-in-the-response).

Aby zapoznać się z przykładem w języku C#, który używa klasyfikacji do wyświetlania wyników, zobacz [Samouczek dotyczący klasyfikacji w języku c#](./csharp-ranking-tutorial.md).
