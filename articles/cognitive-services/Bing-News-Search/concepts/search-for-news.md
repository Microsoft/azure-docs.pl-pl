---
title: Wyszukiwanie wiadomości za pomocą interfejsu API wyszukiwania wiadomości Bing
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak wysyłać zapytania wyszukiwania dla wiadomości ogólnych, popularnych tematów i wiadomości z nagłówków.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: 1e14f13d0e9fba407a983fb182d25c77593e4d3c
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93098403"
---
# <a name="search-for-news-with-the-bing-news-search-api"></a>Wyszukiwanie wiadomości za pomocą interfejsu API wyszukiwania wiadomości Bing

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](https://aka.ms/cogsvcs/bingmove).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Umowa Enterprise, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](https://aka.ms/cogsvcs/bingmigration).

Interfejs API wyszukiwania obrazów Bing umożliwia łatwą integrację możliwości poznawczego wyszukiwania wiadomości w usłudze Bing z aplikacjami.

O ile interfejs API wyszukiwania wiadomości Bing umożliwia przede wszystkim wyszukiwanie artykułów z wiadomościami i zwraca je, to udostępnia także kilka funkcji inteligentnego i ukierunkowanego pobierania wiadomości w Internecie.

## <a name="suggest-and-use-search-terms"></a>Sugerowanie i używanie terminów wyszukiwania

Jeśli udostępniasz pole wyszukiwania, w którym użytkownik wprowadza wyszukiwany termin, użyj [interfejsu API automatycznego sugerowania Bing](../../bing-autosuggest/get-suggested-search-terms.md) w celu ulepszenia jego działania. Interfejs API zwraca sugerowane ciągi zapytań na podstawie częściowych wyszukiwanych terminów w miarę wpisywania ich przez użytkownika.

Gdy użytkownik wprowadzi wyszukiwany termin, zakoduj go w formacie URL przed ustawieniem parametru zapytania [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query). Na przykład jeśli użytkownik wprowadzi frazę *sailing dinghies* , ustaw parametr `q` na wartość `sailing+dinghies` lub `sailing%20dinghies`.

## <a name="get-general-news"></a>Uzyskiwanie wiadomości ogólnych

Aby pobrać z Internetu artykuły z wiadomościami powiązane z wyszukiwanym terminem użytkownika, wyślij następujące żądanie GET:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Jeśli jest to Twoje pierwsze wywoływanie dowolnego z interfejsów API Bing, nie dołączaj nagłówka identyfikatora klienta. Identyfikator klienta należy uwzględnić tylko wtedy, gdy interfejs API Bing został już wywołany i usługa Bing zwróciła identyfikator klienta dla kombinacji użytkownika i urządzenia.

Aby pobrać wiadomości z określonej domeny, należy użyć operatora zapytania [site:](https://msdn.microsoft.com/library/ff795613.aspx).

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

Poniższy przykład JSON przedstawia odpowiedź na poprzednie zapytanie. W ramach [wymagań dotyczących użycia i wyświetlania](../useanddisplayrequirements.md) dla interfejsów API wyszukiwania Bing każdy artykuł z wiadomościami należy wyświetlać w kolejności podanej w odpowiedzi. Jeśli artykuł zawiera klastrowane artykuły, należy wskazać, że pokrewne artykuły istnieją i wyświetlić je na żądanie.

```json
{
    "_type" : "News",
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v5\/news\/search?q=sailing+dinghies",
    "totalEstimatedMatches" : 88400,
    "value" : [{
        "name" : "Sailing Vies for Four Trophies",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891FE99A72...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.9C23AA5...",
                "width" : 650,
                "height" : 341
            }
        },
        "description" : "College Rankings, presented by Zim...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "contoso.com"
        }],
        "datePublished" : "2017-04-14T15:28:00"
    },

    ...

    {
        "name" : "Fabrikam Sailing Club to host Mirror Dinghy...",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891F...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.36...",
                "width" : 448,
                "height" : 300
            }
        },
        "description" : "The sailing club that trained Olympian Ben...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "Contoso"
        }],
        "datePublished" : "2017-04-04T11:02:00",
        "category" : "Sports"
    }]
}
```

Odpowiedź z [wiadomościami](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v5-reference#news) zawiera artykuły z wiadomościami, które usługa Bing uznała za odpowiednie dla zapytania. Pole `totalEstimatedMatches` zawiera szacunkową liczbę artykułów, które są dostępne do wyświetlenia. Aby uzyskać informacje na temat stronicowania artykułów, zobacz [Stronicowanie wiadomości](../paging-news.md).

Każdy [artykułu z wiadomościami](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v5-reference#newsarticle) na liście zawiera nazwę, opis i adres URL do artykułu w witrynie internetowej hosta. Jeśli artykuł zawiera obraz, obiekt zawiera miniaturę obrazu. Użyj właściwości `name` i `url`, aby utworzyć hiperlink prowadzący użytkownika do artykułu w witrynie hosta. Jeśli artykuł zawiera obraz, umożliw jego kliknięcie przy użyciu właściwości `url`. Pamiętaj, aby podać źródło artykułu przy użyciu właściwości `provider`.

Jeśli usłudze Bing uda się określić kategorię artykułu z wiadomościami, artykuł będzie zawierał pole `category`.

## <a name="get-todays-top-news"></a>Pobieranie dzisiejszych najważniejszych wiadomości

Aby uzyskać dzisiejsze najpopularniejsze artykuły z wiadomościami, możesz wysłać te same ogólne żądanie, tak jak wcześniej, pozostawiając `q` parametr bez ustawienia.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Odpowiedź na korzystanie z najważniejszych wiadomości jest niemal taka sama jak w przypadku uzyskiwania ogólnych wiadomości. Jednak odpowiedź `news` nie zawiera pola `totalEstimatedMatches`, ponieważ obowiązuje określona liczba wyników. Liczba najważniejszych artykułów może być inna w zależności od cyklu życia informacji. Upewnij się, że pole jest używane `provider` do poatrybutowania artykułu.

## <a name="get-news-by-category"></a>Pobieranie wiadomości według kategorii

Aby pobrać artykuły z wiadomościami według kategorii, na przykład najważniejsze artykuły sportowe lub rozrywkowe, wyślij następujące żądanie GET do usługi Bing:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news?category=sports&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Użyj parametru zapytania [category](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#category), aby określić kategorię artykułów do pobrania. Aby uzyskać listę kategorii wiadomości, które można określić, zobacz [Kategorie wiadomości według rynku](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news-categories-by-market).

Odpowiedź na żądanie pobrania wiadomości według kategorii jest prawie taka sama jak w przypadku żądania pobrania wiadomości ogólnych. Jednak wszystkie artykuły należą do określonej kategorii.

## <a name="get-headline-news"></a>Pobieranie wiadomości z nagłówków

Aby zażądać artykułów z wiadomościami z nagłówków i pobrać artykuły ze wszystkich kategorii wiadomości, wyślij następujące żądanie GET do usługi Bing:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Nie używaj parametru zapytania [category](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#category).

Odpowiedź na żądanie pobrania wiadomości z nagłówków jest taka sama jak w przypadku żądania pobrania najważniejszych wiadomości z dnia dzisiejszego. Jeśli artykuł jest artykułem z nagłówka, jego pole `headline` ma wartość **true** .

Domyślnie odpowiedź zawiera maksymalnie 12 artykułów z nagłówków. Aby zmienić liczbę artykułów z nagłówków do zwrócenia, określ parametr zapytania [headlineCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#headlinecount). Odpowiedź zawiera również do czterech artykułów spoza nagłówków w każdej z kategorii wiadomości.

W odpowiedzi klaster artykułów jest liczony jako jeden artykuł. Ponieważ klaster może zawierać kilka artykułów, odpowiedź może obejmować więcej niż 12 artykułów z nagłówków i więcej niż 4 artykuły spoza nagłówków na kategorię.

## <a name="get-trending-news"></a>Pobieranie popularnych wiadomości

Aby pobrać tematy wiadomości, które są popularnie w sieciach społecznościowych, wyślij następujące żądanie GET do usługi Bing:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
X-MSAPI-UserState: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

> [!NOTE]
> Popularne tematy są dostępne tylko na rynkach en-US i zh-CN.

Poniższy kod JSON jest odpowiedzią na poprzednie żądanie. Każdy popularny artykułu z wiadomościami zawiera powiązany obraz, flagę ważnej wiadomości oraz adres URL do wyników wyszukiwania w usłudze Bing dla artykułu. Użyj adresu URL w polu `webSearchUrl`, aby zabrać użytkownika do strony z wynikami wyszukiwania usługi Bing. Lub użyj tekstu zapytania, aby wywołać [interfejs API wyszukiwania w Internecie](../../bing-web-search/search-the-web.md) i wyświetlić wyniki samodzielnie.

```json
{
    "_type" : "TrendingTopics",
    "value" : [{
        "name" : "Canada pot measure",
        "image" : {
            "url" : "https:\/\/www.bing.com\/th?id=OPN.RTNews_hHD...",
            "provider" : [{
                "_type" : "Organization",
                "name" : "Contoso Images"
            }]
        },
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=070292D8CEDD...",
        "isBreakingNews" : false,
        "query" : {
            "text" : "Canada marijuana"
        }
    },
    {
        "name" : "Down on Vegas move",
        "image" : {
            "url" : "https:\/\/www.bing.com\/th?id=OPN.RTNews_Bfbmg8h...",
            "provider" : [{
                "_type" : "Organization",
                "name" : "Contoso"
            }]
        },
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=070292D8CEDD...",
        "isBreakingNews" : false,
        "query" : {
            "text" : "Marcus Appel Las Vegas"
        }
    },

    ...

    ]
}
```

## <a name="getting-related-news"></a>Pobieranie powiązanych wiadomości

Jeśli istnieją inne artykuły, które są powiązane z artykułem z wiadomościami, artykuł z wiadomościami może zawierać pole [clusteredArticles](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle-clusteredarticles). Poniżej przedstawiono artykuł z klastrowanymi artykułami.

```json
    {
        "name" : "Playoffs 2017: Betting lines, point spreads...",
        "url" : "http:\/\/www.bing.com\/cr?IG=4B7056CEC271408997D115...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.D7B1...",
                "width" : 700,
                "height" : 393
            }
        },
        "description" : "April 14, 2017 3:37pm EDT April 14, 2017 3:34pm...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "Contoso"
        }],
        "datePublished" : "2017-04-14T19:43:00",
        "category" : "Sports",
        "clusteredArticles" : [{
            "name" : "Playoffs 2017: Betting odds, favorites to win...",
            "url" : "http:\/\/www.bing.com\/cr?IG=4B7056CEC271408997D1159E...",
            "description" : "April 14, 2017 3:30pm EDT April 14, 2017 3:27pm...",
            "provider" : [{
                "_type" : "Organization",
                "name" : "Contoso"
            }],
            "datePublished" : "2017-04-14T19:37:00",
            "category" : "Sports"
        }]
    },
```

## <a name="throttling-requests"></a>Ograniczanie żądań

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Jak przechodzić do kolejnych stron wyników wyszukiwania wiadomości Bing](../paging-news.md)
