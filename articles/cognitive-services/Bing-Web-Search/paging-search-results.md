---
title: Jak na stronie Wyniki wyszukiwania — interfejsy API wyszukiwania Bing
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak na stronie Wyniki wyszukiwania interfejsy API wyszukiwania Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: aahi
ms.openlocfilehash: e7613f4b6bb301c603ae5ded98f271f3cb98b340
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93074101"
---
# <a name="how-to-page-through-results-from-the-bing-search-apis"></a>Jak uzyskać stronę za pośrednictwem wyników z interfejsy API wyszukiwania Bing

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](https://aka.ms/cogsvcs/bingmove).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Umowa Enterprise, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](https://aka.ms/cogsvcs/bingmigration).

W przypadku wysyłania wywołania do interfejsów API sieci Web, niestandardowych, obrazów, wiadomości lub wyszukiwanie wideo, Bing zwraca podzestaw łącznej liczby wyników, które mogą być odpowiednie dla zapytania. Aby uzyskać szacowaną łączną liczbę dostępnych wyników, uzyskaj dostęp do pola obiektu odpowiedzi `totalEstimatedMatches` . 

Przykład: 

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    }
}  
```

## <a name="paging-through-search-results"></a>Stronicowanie za poorednictwem wyników wyszukiwania

Aby uzyskać dostęp do strony za pomocą dostępnych wyników, użyj `count` `offset` parametrów zapytania i podczas wysyłania żądania.  

> [!NOTE]
>
> * Stronicowanie za pomocą interfejsów API wideo, obrazów i grup dyskusyjnych Bing dotyczy tylko funkcji wyszukiwania ogólnego wideo ( `/video/search` ), wiadomości ( `/news/search` ) i obrazu ( `/image/search` ). Stronicowanie za poorednictwem tematów i kategorii trendów nie jest obsługiwane.  
> * `TotalEstimatedMatches`To pole jest oszacowaniem łącznej liczby wyników wyszukiwania dla bieżącego zapytania. Gdy ustawiasz `count` Parametry i `offset` , to oszacowanie może się zmienić.

| Parametr | Opis                                                                                                                                                                |
|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `count`   | Określa liczbę wyników do zwrócenia w odpowiedzi. Należy zauważyć, że wartość domyślna `count` i Maksymalna liczba wyników, które mogą być potrzebne, różnią się w zależności od interfejsu API. Te wartości można znaleźć w dokumentacji referencyjnej w sekcji [następne kroki](#next-steps). |
| `offset`  | Określa liczbę wyników do pominięcia. `offset`Jest równa zero i powinna być mniejsza niż ( `totalEstimatedMatches`  -  `count` ).                                           |

Jeśli na przykład chcesz wyświetlić 15 wyników na stronie, ustaw wartość `count` 15 i `offset` na 0, aby uzyskać pierwszą stronę wyników. Dla każdego kolejnego wywołania interfejsu API można zwiększyć wartość `offset` o 15. Poniższy przykład żąda 15 stron sieci Web zaczynających się od przesunięcia 45.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Jeśli używasz `count` wartości domyślnej, wystarczy określić `offset` parametr zapytania w WYWOŁANIACH interfejsu API.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Korzystając z obrazów Bing i interfejsów API wideo, można użyć wartości, `nextOffset` Aby uniknąć zduplikowanych wyników wyszukiwania. Pobierz wartość z `Images` `Videos` obiektów Response lub, a następnie użyj jej w żądaniach z `offset` parametrem.  

> [!NOTE]
> Interfejs API wyszukiwania w sieci Web Bing zwraca wyniki wyszukiwania, które mogą obejmować strony sieci Web, obrazy, filmy wideo i wiadomości. Podczas przeglądania wyników wyszukiwania w interfejs API wyszukiwania w sieci Web Bing są stronicowane tylko [strony sieci Web](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage), a nie inne typy odpowiedzi, takie jak obrazy lub wiadomości. Wyniki wyszukiwania w `WebPage` obiektach mogą zawierać również wyniki, które są wyświetlane w innych typach odpowiedzi.
>
> Jeśli używasz `responseFilter` parametru zapytania bez określenia żadnych wartości filtru, nie używaj `count` `offset` parametrów i. 

## <a name="next-steps"></a>Następne kroki

* [Jakie są wyszukiwanie w sieci Web Bing interfejsy API?](bing-api-comparison.md)
* [Dokumentacja interfejsu API wyszukiwania w sieci Web Bing w wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
* [interfejs API wyszukiwania niestandardowego Bing odwołanie wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
* [interfejs API wyszukiwania wiadomości Bing odwołanie wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)
* [interfejs API wyszukiwania wideo Bing odwołanie wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference)
* [interfejs API wyszukiwania obrazów Bing odwołanie wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
