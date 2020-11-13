---
title: Pobierz obrazy z interfejs API wyszukiwania obrazów Bing sieci Web
titleSuffix: Azure Cognitive Services
description: Użyj interfejs API wyszukiwania obrazów Bing, aby wyszukać i pobrać odpowiednie obrazy z sieci Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: AB1B9898-C94A-4B59-91A1-8623C94BA3D4
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 3e5041855cc036c278979ba1c87634a61ec68ec7
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94593488"
---
# <a name="get-images-from-the-web-with-the-bing-image-search-api"></a>Pobierz obrazy z sieci Web za pomocą interfejs API wyszukiwania obrazów Bing

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](https://aka.ms/cogsvcs/bingmove).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Umowa Enterprise, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](https://aka.ms/cogsvcs/bingmigration).

W przypadku korzystania z interfejsu API REST wyszukiwanie obrazów Bing można uzyskać obrazy z sieci Web, które są powiązane z terminem wyszukiwania, wysyłając następujące żądanie GET:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Użyj parametru [q](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query) zapytania dla terminu wyszukiwania zakodowanego w adresie URL. Na przykład, jeśli wprowadzisz *dinghies* , ustaw `q` na `sailing+dinghies` lub `sailing%20dinghies` .

> [!IMPORTANT]
> * Wszystkie żądania muszą zostać wykonane z serwera, a nie z klienta programu.
> * Jeśli podczas pierwszego wywołania interfejsów API wyszukiwania Bing jest używany program, nie dołączaj nagłówka identyfikatora klienta. Dołącz identyfikator klienta tylko w przypadku, gdy wcześniej wywołano interfejs API Bing, który zwrócił identyfikator klienta dla kombinacji użytkownika i urządzenia.

## <a name="get-images-from-a-specific-web-domain"></a>Pobierz obrazy z określonej domeny sieci Web

Aby uzyskać obrazy z określonej domeny, należy użyć operatora [site:](/previous-versions/bing/search/ff795613(v=msdn.10)) zapytania.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

> [!NOTE]
> Odpowiedzi na zapytania przy użyciu `site:` operatora mogą obejmować zawartość dla dorosłych niezależnie od ustawienia [bezpieczne wyszukiwanie](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#safesearch) . Należy używać tylko `site:` w przypadku, gdy jest świadome zawartości w domenie.

## <a name="filter-images"></a>Filtruj obrazy

 Domyślnie interfejs API wyszukiwanie obrazów zwraca wszystkie obrazy, które są istotne dla zapytania. Jeśli chcesz filtrować obrazy zwracane przez usługę Bing (na przykład w celu zwrócenia tylko obrazów z przezroczystym tłem lub określonym rozmiarem), użyj następujących parametrów zapytania:

* [aspekt](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#aspect)— filtruje obrazy według współczynnika proporcji (na przykład obrazów standardowych lub panoramicznych).
* [Color](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#color)— filtruje obrazy według koloru lub czerni i bieli.
* [aktualność](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#freshness)— filtruje obrazy według wieku (na przykład obrazy odnalezione przez usługę Bing w zeszłym tygodniu).
* [Height](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#height), [Width](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#width)— filtruje obrazy według szerokości i wysokości.
* [imageContent](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagecontent)— filtruje obrazy według zawartości (na przykład obrazy, które wyświetlają tylko powierzchnie osoby).
* [ImageType](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype)— umożliwia filtrowanie obrazów według typu (np. clipart, animowanych plików GIF lub przezroczystego tła).
* [licencja](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#license)— filtruje obrazy według typu licencji skojarzonej z witryną.
* [rozmiar](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#size)— umożliwia filtrowanie obrazów według rozmiaru, takich jak małe obrazy, do 200x200 pikseli.

Aby uzyskać obrazy z określonej domeny, należy użyć operatora [site:](/previous-versions/bing/search/ff795613(v=msdn.10)) zapytania.

Poniższy przykład pokazuje, jak pobrać małe obrazy z ContosoSailing.com, które zostały odnalezione w ciągu ostatniego tygodnia.  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="bing-image-search-response-format"></a>Format odpowiedzi wyszukiwanie obrazów Bing

Komunikat odpowiedzi z usługi Bing zawiera [obraz](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) zawierający listę obrazów, które Cognitive Services określone jako istotne dla zapytania. Każdy obiekt [obrazu](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) na liście zawiera następujące informacje o obrazie: adres URL, jego rozmiar, wymiary, format kodowania, adres URL do miniatury obrazu i wymiary miniatury.

> [!NOTE]
> * Obrazy muszą być wyświetlane w kolejności podanej w odpowiedzi.
> * Ponieważ formaty i parametry adresów URL mogą ulec zmianie bez powiadomienia, użyj wszystkich adresów URL jako-is. Nie należy uwzględniać zależności w odniesieniu do formatu adresu URL ani parametrów, chyba że zaznaczono inaczej.

```json
{
    "name": "Rich Passage Sailing Dinghy",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=73118C8B4E3...",
    "thumbnailUrl": "https:\/\/tse1.mm.bing.net\/th?id=OIP.GNarK7m...",
    "datePublished": "2011-10-29T11:26:00",
    "contentUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3...",
    "hostPageUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3687...",
    "contentSize": "79239 B",
    "encodingFormat": "jpeg",
    "hostPageDisplayUrl": "en.contoso.org\/wiki\/File:Rich_Passage...",
    "width": 526,
    "height": 688,
    "thumbnail": {
        "width": 229,
        "height": 300
    },
    "imageInsightsToken": "ccid_GNarK7ma*mid_CCF85447ADA6...",
    "insightsSourcesSummary": {
        "shoppingSourcesCount": 0,
        "recipeSourcesCount": 0
    },
    "imageId": "CCF85447ADA6FFF9E96E7DF0B796F7A86E34593",
    "accentColor": "376094"
},
```

Po wywołaniu interfejsu API wyszukiwania obrazów Bing usługa Bing zwraca listę wyników. Lista jest podzestawem całkowitej liczby wyników odpowiednich dla zapytania. Pole `totalEstimatedMatches` odpowiedzi zawiera szacunkową liczbę obrazów, które są dostępne do wyświetlenia. Aby uzyskać szczegółowe informacje na temat pozostałej części obrazów, zobacz [obrazy stronicowania](../../bing-web-search/paging-search-results.md).

## <a name="next-steps"></a>Następne kroki

Jeśli jeszcze nie podjęto próby interfejs API wyszukiwania obrazów Bing, wypróbuj [Przewodnik Szybki Start](../quickstarts/csharp.md). Jeśli szukasz czegoś bardziej złożonego, Wypróbuj ten samouczek, aby utworzyć [jednostronicową aplikację sieci Web](../tutorial-bing-image-search-single-page-app.md).