---
title: 'Szybki Start: Wyszukiwanie obrazów przy użyciu interfejsu API REST wyszukiwanie obrazów Bing i Node.js'
titleSuffix: Azure Cognitive Services
description: Ten przewodnik Szybki Start umożliwia wysyłanie żądań wyszukiwania obrazów do interfejsu API REST wyszukiwanie obrazów Bing przy użyciu języka JavaScript i odpowiedzi JSON.
services: cognitive-services
documentationcenter: ''
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: seodec2018, devx-track-js
ms.openlocfilehash: b9f677c9596974129b38d56e8ef9aeb304c5f690
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91324913"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-nodejs"></a>Szybki Start: Wyszukiwanie obrazów przy użyciu interfejsu API REST wyszukiwanie obrazów Bing i Node.js

Skorzystaj z tego przewodnika Szybki Start, aby dowiedzieć się, jak wysyłać żądania wyszukiwania do interfejs API wyszukiwania obrazów Bing. Ta aplikacja języka JavaScript wysyła zapytanie dotyczące wyszukiwania do interfejsu API i wyświetla adres URL pierwszego obrazu w wynikach. Mimo że aplikacja jest zapisywana w języku JavaScript, interfejs API jest usługą sieci Web RESTful zgodną z większością języków programowania.

Kod źródłowy dla tego przykładu jest dostępny [w witrynie GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingImageSearchv7Quickstart.js) z dodatkową obsługą błędów i adnotacjami.

## <a name="prerequisites"></a>Wymagania wstępne

* Najnowsza wersja środowiska [Node.js](https://nodejs.org/en/download/).

* [Biblioteka żądań JavaScript](https://github.com/request/request).

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

Aby uzyskać więcej informacji, zobacz [Cognitive Services Cennik — wyszukiwanie Bing API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz nowy plik JavaScript w ulubionym środowisku IDE lub edytorze, a następnie ustaw wymagania dotyczące ścisłości i protokołu HTTPS.

    ```javascript
    'use strict';
    let https = require('https');
    ```

2. Utwórz zmienne dla punktu końcowego interfejsu API, ścieżki wyszukiwania interfejsu API obrazów, klucza subskrypcji i wyszukiwanego terminu. W przypadku programu `host` można użyć globalnego punktu końcowego w poniższym kodzie lub użyć niestandardowego punktu końcowego [poddomeny](../../../cognitive-services/cognitive-services-custom-subdomains.md) wyświetlanego w Azure Portal dla zasobu.

    ```javascript
    let subscriptionKey = 'enter key here';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/images/search';
    let term = 'tropical ocean';
    ```

## <a name="construct-the-search-request-and-query"></a>Konstruowanie żądania wyszukiwania i zapytania

1. Użyj zmiennych utworzonych w ostatnim kroku, aby sformatować adres URL wyszukiwania dla żądania interfejsu API. URL — Koduj termin wyszukiwania przed wysłaniem go do interfejsu API.

    ```javascript
    let request_params = {
        method : 'GET',
        hostname : host,
        path : path + '?q=' + encodeURIComponent(search),
        headers : {
        'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };
    ```

2. Użyj biblioteki żądań, aby wysłać zapytanie do interfejsu API. 
    ```javascript
    let req = https.request(request_params, response_handler);
    req.end();
    ```

## <a name="handle-and-parse-the-response"></a>Obsługa i analizowanie odpowiedzi

1. Zdefiniuj funkcję o nazwie `response_handler`, która przyjmuje wywołanie HTTP, `response`, jako parametr. 

2. W ramach tej funkcji Zdefiniuj zmienną, aby zawierała treść odpowiedzi JSON. 

    ```javascript
    let response_handler = function (response) {
        let body = '';
    };
    ```

3. Zapisz treść odpowiedzi po `data` wywołaniu flagi.

    ```javascript
    response.on('data', function (d) {
        body += d;
    });
    ```

4. Po `end` zasygnalizowaniu flagi Pobierz pierwszy wynik z odpowiedzi JSON. Zostanie wyświetlony adres URL pierwszego obrazu i łączna liczba zwróconych obrazów.

    ```javascript
    response.on('end', function () {
        let firstImageResult = imageResults.value[0];
        console.log(`Image result count: ${imageResults.value.length}`);
        console.log(`First image thumbnail url: ${firstImageResult.thumbnailUrl}`);
        console.log(`First image web search url: ${firstImageResult.webSearchUrl}`);
     });
    ```

## <a name="example-json-response"></a>Przykładowa odpowiedź JSON

Odpowiedzi z interfejsu API wyszukiwania obrazów Bing są zwracane w formacie JSON. Ta przykładowa odpowiedź została obcięta w celu pokazania pojedynczego wyniku.

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }]
}
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Utwórz aplikację jednostronicową](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Zobacz też

* [Co to jest interfejs API wyszukiwania obrazów Bing?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Wypróbuj interaktywny pokaz online](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)
* [Szczegóły cennika interfejsy API wyszukiwania Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) 
* [Dokumentacja usług Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Dokumentacja interfejsu API wyszukiwania obrazów Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
