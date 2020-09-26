---
title: wyszukiwanie jednostek Bing przewodniku szybki start dla biblioteki klienta JavaScript
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: b145cc1689ad2c1a39591df0e39bb8d0445333c7
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376879"
---
Skorzystaj z tego przewodnika Szybki Start, aby rozpocząć wyszukiwanie jednostek przy użyciu biblioteki klienta wyszukiwanie jednostek Bing dla języka JavaScript. Chociaż wyszukiwanie jednostek Bing ma interfejs API REST zgodny z większością języków programowania, Biblioteka klienta zapewnia łatwy sposób integracji usługi z aplikacjami. Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js).

## <a name="prerequisites"></a>Wymagania wstępne

* Najnowsza wersja środowiska [Node.js](https://nodejs.org/en/download/).
* [Zestaw wyszukiwanie jednostek Bing SDK dla języka JavaScript](https://www.npmjs.com/package/@azure/cognitiveservices-entitysearch)
     *  Aby go zainstalować, uruchom polecenie `npm install @azure/cognitiveservices-entitysearch`
* `CognitiveServicesCredentials`Klasa z `@azure/ms-rest-azure-js` pakietu do uwierzytelniania klienta.
     * Aby go zainstalować, uruchom polecenie `npm install @azure/ms-rest-azure-js`

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-entity-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz nowy plik w języku JavaScript w ulubionym środowisku IDE lub edytorze i dodaj następujące wymagania.

    ```javascript
    const CognitiveServicesCredentials = require('@azure/ms-rest-azure-js').CognitiveServicesCredentials;
    const EntitySearchAPIClient = require('@azure/cognitiveservices-entitysearch');
    ```

2. Utwórz wystąpienie obiektu `CognitiveServicesCredentials` przy użyciu klucza subskrypcji. Następnie za pomocą tego obiektu utwórz wystąpienie klienta wyszukiwania.

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let entitySearchApiClient = new EntitySearchAPIClient(credentials);
    ```

## <a name="send-a-request-and-receive-a-response"></a>Wysyłanie żądania i odbieranie odpowiedzi

1. Wyślij żądanie wyszukiwania jednostek za pomocą funkcji `entitiesOperations.search()`. Po otrzymaniu odpowiedzi wyświetl obiekt `queryContext`, liczbę zwróconych wyników i opis pierwszego wyniku.

    ```javascript
    entitySearchApiClient.entitiesOperations.search('seahawks').then((result) => {
        console.log(result.queryContext);
        console.log(result.entities.value);
        console.log(result.entities.value[0].description);
    }).catch((err) => {
        throw err;
    });
    ```

<!-- Removing until we can replace with a sanitized version.
![Entity results](media/entity-search-sdk-node-quickstart-results.png)
-->

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji internetowej](../../tutorial-bing-entities-search-single-page-app.md)

* [Czym jest interfejs API wyszukiwania jednostek Bing?](../../overview.md)