---
title: Wyszukiwanie filmów wideo przy użyciu interfejs API wyszukiwania wideo Bing
titleSuffix: Azure Cognitive Services
description: Wyszukiwanie wideo Bing APIfinds i zwraca odpowiednie wideo z Internetu, udostępnia kilka funkcji inteligentnych i ukierunkowanych na pobieranie wideo w sieci Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 10277efe1f06de3633b2d614e2ee5ec0cc351c76
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96351933"
---
# <a name="search-for-videos-with-the-bing-video-search-api"></a>Wyszukiwanie wideo za pomocą interfejsu API wyszukiwania wideo Bing

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Enterprise Agreement, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Interfejs API wyszukiwania wideo Bing umożliwia łatwą integrację możliwości poznawczego wyszukiwania wiadomości w usłudze Bing z aplikacjami. O ile interfejs API umożliwia przede wszystkim wyszukiwanie odpowiednich filmów wideo w Internecie i zwracanie ich, to udostępnia także kilka funkcji inteligentnego i ukierunkowanego pobierania wideo w Internecie.

## <a name="getting-videos"></a>Uzyskiwanie filmów wideo

Aby uzyskać z Internetu filmy wideo powiązane z wyszukiwanym terminem użytkownika, wyślij następujące żądanie GET:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Wszystkie żądania muszą pochodzić z serwera.

Jeśli jest to Twoje pierwsze wywoływanie dowolnego z interfejsów API Bing, nie dołączaj nagłówka identyfikatora klienta. Identyfikator klienta należy uwzględnić tylko wtedy, gdy interfejs API Bing został już wywołany i usługa Bing zwróciła identyfikator klienta dla kombinacji użytkownika i urządzenia.

Aby uzyskać filmy wideo z określonej domeny, należy użyć operatora [site:](/previous-versions/bing/search/ff795613(v=msdn.10)) zapytania.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

Odpowiedź zawiera obiekt [Videos](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) z listą filmów wideo, które usługa Bing uznała za odpowiednie dla zapytania. Każdy obiekt [Video](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video) na liście zawiera adres URL filmu wideo, a także jego czas trwania, wymiary i format kodowania oraz inne atrybuty. Obiekt wideo zawiera także adres URL miniatury wideo i wymiary tej miniatury.

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545...",
    "totalEstimatedMatches" : 1000,
    "value" : [
        {
            "name" : "How to sail - What to Wear for Dinghy Sailing",
            "description" : "An informative video on what to wear when...",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7...",
            "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.DYW...",
            "datePublished" : "2014-03-04T11:51:53",
            "publisher" : [
                {
                    "name" : "Fabrikam"
                }
            ],
            "creator" : 
            {
                "name" : "Marcus Appel"
            },
            "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjZ--g",
            "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D569...",
            "encodingFormat" : "h264",
            "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjZ--g",
            "width" : 1280,
            "height" : 720,
            "duration" : "PT2M47S",
            "motionThumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OM.Y62...",
            "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"https:...><\/iframe>",
            "allowHttpsEmbed" : true,
            "viewCount" : 8743,
            "thumbnail" : 
            {
                "width" : 300,
                "height" : 168
            },
            "videoId" : "6DB795E11A6E3CBAAD636DB795E113CBAAD63",
            "allowMobileEmbed" : true,
            "isSuperfresh" : false
        },
        ...
    ],
    "queryExpansions" : [...],
    "nextOffsetAddCount" : 0,
    "pivotSuggestions" : [...]
}
```

## <a name="video-thumbnails"></a>Miniatury wideo

Możesz wyświetlić wszystkie miniatury wideo zwracane przez interfejs API wyszukiwania wideo Bing lub ich podzbiór. Jeśli wyświetlisz podzestaw, udostępnij użytkownikowi opcję wyświetlenia pozostałych filmów wideo. W ramach [wymagań dotyczących użycia i wyświetlania](../../bing-web-search/use-display-requirements.md) dla interfejsu API wyszukiwania Bing filmy wideo należy wyświetlać w kolejności podanej w odpowiedzi. Aby uzyskać informacje na temat zmieniania rozmiaru miniatur, zobacz [Zmiana rozmiaru i przycinanie miniatur](../../bing-web-search/resize-and-crop-thumbnails.md). 

Gdy użytkownik umieści kursor na miniaturze, można użyć elementu [motionThumbnailUrl](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-motionthumbnailurl) do odtworzenia miniatury filmu wideo. Pamiętaj o nadaniu atrybutu wyświetlonej miniaturze ruchu.

<!-- Removing until the images can be sanitized.
![Motion thumbnail of a video](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

Po kliknięciu miniatury istnieją trzy opcje wyświetlenia wideo:

- Użyj elementu [hostPageUrl](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-hostpageurl), aby wyświetlić film wideo w witrynie internetowej hosta (na przykład YouTube)
- Użyj elementu [webSearchUrl](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-websearchurl), aby wyświetlić film wideo w przeglądarce wideo Bing
- Użyj elementu [embdedHtml](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-embedhtml), aby osadzić film wideo w swoim środowisku 

Pamiętaj, aby użyć wydawcy i autora do nadania atrybutu odtwarzanemu filmowi wideo.

Szczegóły użycia elementu [videoId](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-videoid) do uzyskiwania szczegółowych informacji na temat filmu wideo można znaleźć w temacie [Video Insights (Szczegółowe informacje o filmie wideo)](../video-insights.md).

## <a name="filtering-videos"></a>Filtrowanie filmów wideo

Domyślnie interfejs API wyszukiwania wideo zwraca wszystkie filmy wideo odpowiednie dla zapytania. Jeśli chcesz znaleźć tylko bezpłatne filmy wideo lub filmy wideo krótsze niż pięć minut, użyj następujących parametrów w zapytaniu filtru:

- [Cennik](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#pricing) &mdash; Filtrowanie filmów wideo według cen (na przykład bezpłatnych materiałów wideo lub opłat za korzystanie z nich)
- [rozwiązanie](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#resolution) &mdash; Filtrowanie filmów wideo według rozdzielczości (na przykład wideo z rozdzielczością 720 lub wyższą)
- [videoLength](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videolength) &mdash; Filtrowanie filmów wideo według długości wideo (na przykład wideo o długości mniejszej niż pięć minut)
- [świeżość](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#freshness) &mdash; Filtrowanie filmów wideo według wieku (na przykład wideo odnalezione przez usługę Bing w ciągu ostatniego tygodnia)

Aby uzyskać filmy wideo z określonej domeny, należy uwzględnić operatora [site:](/previous-versions/bing/search/ff795613(v=msdn.10)) zapytania w ciągu zapytania.

> [!NOTE]
> W zależności od zapytania, jeśli używasz operatora zapytania `site:`, istnieje ryzyko, że odpowiedź będzie zawierać zawartość dla dorosłych niezależnie od ustawienia [safeSearch](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#safesearch). Operatora `site:` używaj tylko wtedy, gdy znasz zawartość witryny i w swoim scenariuszu uwzględniasz możliwość pojawienia się zawartości dla dorosłych.

Poniższy przykład pokazuje, jak z witryny ContosoSailing.com uzyskać bezpłatne filmy wideo o rozdzielczości co najmniej 720p, które usługa Bing odnalazła w ostatnim miesiącu.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies+site:contososailing.com&pricing=free&freshness=month&resolution=720p&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

## <a name="expanding-the-query"></a>Rozwijanie zapytania

Jeśli usługa Bing może rozwinąć zapytanie w celu zawężenia kryteriów oryginalnego wyszukiwania, obiekt [Videos](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) zawiera pole `queryExpansions`. Jeśli na przykład zapytanie to *Cleaning Gutters* (Czyszczenie rynien), potencjalnymi rozwiniętymi zapytaniami mogą być: Gutter Cleaning **Tools** (Czyszczenie rynien — narzędzia), Cleaning Gutters **From the Ground** (Czyszczenie rynien — z poziomu gruntu), Gutter Cleaning **Machine** (Czyszczenie rynien — maszyna) oraz **Easy** Gutter Cleaning (Proste czyszczenie rynien).

W poniższym przykładzie pokazano rozwinięte zapytania dla frazy *Cleaning Gutters*.

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC5...",
    "totalEstimatedMatches" : 1000,
    "value" : [...],
    "nextOffsetAddCount" : 4,
    "queryExpansions" : [
        {
            "text" : "Gutter Cleaning Tools",
            "displayText" : "Tools",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FB....",
            "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5...",
            "thumbnail" : {
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Gutter..."
            }
        },
        ...
    ]
    "pivotSuggestions" : [...],
}
```

Pole `queryExpansions` zawiera listę obiektów [Query](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query_obj). Pole `text` zawiera rozwinięte zapytanie, a pole `displayText` zawiera termin będący rozwinięciem. Pól tekstu i miniatury można użyć do wyświetlenia rozwiniętych ciągów zapytania użytkownikowi w przypadku, gdy rozwinięte ciągi zapytania naprawdę są tym, czego użytkownik szuka. Zmień miniatury i tekst w elementy klikalne za pomocą adresu URL `webSearchUrl` lub adresu URL `searchLink`. Użyj parametru `webSearchUrl`, aby odesłać użytkownika do wyników wyszukiwania w usłudze Bing, albo parametru `searchLink`, jeśli udostępniasz własną stronę z wynikami.

## <a name="pivoting-the-query"></a>Stosowanie elementów bazowych zapytania

Jeśli usługa Bing może podzielić oryginalne zapytanie wyszukiwania na segmenty, obiekt [Videos](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) zawiera pole `pivotSuggestions`. Jeśli na przykład oryginalnym zapytaniem była fraza *Cleaning Gutters* (Czyszczenie rynien), usługa Bing może podzielić zapytanie na segmenty *Cleaning* i *Gutters*.

W poniższym przykładzie przedstawiono propozycje elementów bazowych dla zapytania *Cleaning Gutters*.

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC...",
    "totalEstimatedMatches" : 1000,
    "value" : [...],
    "nextOffsetAddCount" : 0,
    "queryExpansions" : [...],
    "pivotSuggestions" : [
        {
            "pivot" : "cleaning",
            "suggestions" : [
                {
                    "text" : "Gutter Repair",
                    "displayText" : "Repair",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5\/videos...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=Gutter..."
                    }
                },
                ...
            ]
        },
        {
            "pivot" : "gutters",
            "suggestions" : [
                {
                    "text" : "Window Cleaning",
                    "displayText" : "Window",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC59...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?q=Window..."
                    }
                },
                ...
            ]
        }
    ]
}
```

Dla każdego elementu bazowego odpowiedź zawiera listę obiektów [Query](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query_obj) zawierających sugerowane zapytania. Pole `text` zawiera sugerowane zapytanie, a pole `displayText` zawiera termin, który zastępuje element bazowy w oryginalnym zapytaniu. Na przykład „Window Cleaning” (Czyszczenie okien).

Pól `text` i `thumbnail` można użyć do wyświetlenia rozwiniętych ciągów zapytania użytkownikowi w przypadku, gdy rozwinięte ciągi zapytania naprawdę są tym, czego użytkownik szuka. Zmień miniatury i tekst w elementy klikalne za pomocą adresu URL `webSearchUrl` lub adresu URL `searchLink`. Użyj parametru `webSearchUrl`, aby odesłać użytkownika do wyników wyszukiwania w usłudze Bing, albo parametru `searchLink`, jeśli udostępniasz własną stronę z wynikami.

## <a name="throttling-requests"></a>Ograniczanie żądań

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]