---
title: wyszukiwanie wideo Bing przewodniku szybki start dla biblioteki klienta JavaScript
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: fa1ecb24649d9355e6104bff8add5430fdd64c3c
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91376669"
---
Skorzystaj z tego przewodnika Szybki Start, aby rozpocząć wyszukiwanie wiadomości za pomocą biblioteki klienta wyszukiwanie wideo Bing dla języka JavaScript. Chociaż wyszukiwanie wideo Bing ma interfejs API REST zgodny z większością języków programowania, Biblioteka klienta zapewnia łatwy sposób integracji usługi z aplikacjami. Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js). Zawiera on więcej adnotacji i funkcji.

## <a name="prerequisites"></a>Wymagania wstępne

* Najnowsza wersja środowiska [Node.js](https://nodejs.org/en/download/).
* [Zestaw wyszukiwanie wideo Bing SDK dla języka JavaScript](https://www.npmjs.com/package/@azure/cognitiveservices-videosearch)
     *  Aby go zainstalować, uruchom polecenie `npm install @azure/cognitiveservices-videosearch`
* `CognitiveServicesCredentials`Klasa z `@azure/ms-rest-azure-js` pakietu do uwierzytelniania klienta.
     * Aby go zainstalować, uruchom polecenie `npm install @azure/ms-rest-azure-js`

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz nowy plik JavaScript w ulubionym środowisku IDE lub edytorze i Dodaj `require()` instrukcję do wyszukiwanie wideo Bingej biblioteki klienta oraz `CognitiveServicesCredentials` modułu. Utwórz zmienną dla klucza subskrypcji. 
    
    ```javascript
    const CognitiveServicesCredentials = require('@azure/ms-rest-azure-js').CognitiveServicesCredentials;
    const VideoSearchAPIClient = require('@azure/cognitiveservices-videosearch');
    ```

2. Utwórz wystąpienie obiektu `CognitiveServicesCredentials`, korzystając z klucza. Następnie użyj go do utworzenia wystąpienia klienta wyszukiwania wideo.

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let client = new VideoSearchAPIClient(credentials);
    ```

## <a name="send-the-search-request"></a>Wysyłanie żądania wyszukiwania

1. Użyj funkcji `client.videosOperations.search()`, aby wysłać żądanie wyszukiwania do interfejsu API wyszukiwania wideo Bing. Jeśli zostaną zwrócone wyniki wyszukiwania, użyj funkcji `.then()`, aby je zarejestrować.
    
    ```javascript
    client.videosOperations.search('Interstellar Trailer').then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie internetowej aplikacji jednostronicowej](../../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Zobacz też 

* [Co to jest interfejs API wyszukiwania wideo Bing?](../../overview.md)
* [Przykłady zestawu SDK programu .NET dla usług poznawczych](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)