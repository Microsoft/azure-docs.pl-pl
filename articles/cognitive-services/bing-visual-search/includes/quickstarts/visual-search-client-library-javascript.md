---
title: wyszukiwanie wizualne Bing przewodniku szybki start dla biblioteki klienta JavaScript
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/26/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: a87a5f7208e4966b6afba0b939be42726ca20365
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2020
ms.locfileid: "94371708"
---
Skorzystaj z tego przewodnika Szybki Start, aby rozpocząć pobieranie szczegółowych informacji o obrazach z usługi wyszukiwanie wizualne Bing przy użyciu biblioteki klienckiej języka JavaScript. Chociaż wyszukiwanie wizualne Bing ma interfejs API REST zgodny z większością języków programowania, Biblioteka klienta zapewnia łatwy sposób integracji usługi z aplikacjami. Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/visualSearch.js). 

[Dokumentacja](/javascript/api/@azure/cognitiveservices-visualsearch/?preserve-view=true&view=azure-node-latest)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-visualsearch)  |  źródłowy biblioteki [Pakiet (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-visualsearch)  |  [Przykłady](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

## <a name="prerequisites"></a>Wymagania wstępne

* Najnowsza wersja środowiska [Node.js](https://nodejs.org/en/download/).
* [Zestaw Wyszukiwanie wizualne Bing SDK dla języka JavaScript](https://www.npmjs.com/package/@azure/cognitiveservices-visualsearch)
     *  Aby go zainstalować, uruchom polecenie `npm install @azure/cognitiveservices-visualsearch`
* `CognitiveServicesCredentials`Klasa z `@azure/ms-rest-azure-js` pakietu do uwierzytelniania klienta.
     * Aby go zainstalować, uruchom polecenie `npm install @azure/ms-rest-azure-js`

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](~/includes/cognitive-services-bing-visual-search-signup-requirements.md)]

<a name="client"></a>

## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz nowy plik w języku JavaScript w ulubionym środowisku IDE lub edytorze i dodaj następujące wymagania. Następnie utwórz zmienne dla klucza subskrypcji, identyfikatora konfiguracji niestandardowej i ścieżki do pliku obrazu, który chcesz przekazać. 

    ```javascript
    const os = require("os");
    const async = require('async');
    const fs = require('fs');
    const Search = require('@azure/cognitiveservices-visualsearch');
    const CognitiveServicesCredentials = require('@azure/ms-rest-azure-js').CognitiveServicesCredentials;
    
    let keyVar = 'YOUR-VISUAL-SEARCH-ACCESS-KEY';
    let credentials = new CognitiveServicesCredentials(keyVar);
    let filePath = "../Data/image.jpg";
    ```

2. Utwórz wystąpienie klienta.

    ```javascript
    let visualSearchClient = new Search.VisualSearchClient(credentials);
    ```

## <a name="search-for-images"></a>Wyszukiwanie obrazów

1. Użyj metody `fs.createReadStream()` do odczytu w pliku obrazu i utwórz zmienne dla żądania wyszukiwania i wyników. Następnie użyj klienta do wyszukiwania obrazów.

    ```javascript
    let fileStream = fs.createReadStream(filePath);
    let visualSearchRequest = JSON.stringify({});
    let visualSearchResults;
    try {
        visualSearchResults = await visualSearchClient.images.visualSearch({
            image: fileStream,
            knowledgeRequest: visualSearchRequest
        });
        console.log("Search visual search request with binary of image");
    } catch (err) {
        console.log("Encountered exception. " + err.message);
    }
    ```

2. Przeanalizuj wyniki poprzedniego zapytania:

    ```javascript
    // Visual Search results
    if (visualSearchResults.image.imageInsightsToken) {
        console.log(`Uploaded image insights token: ${visualSearchResults.image.imageInsightsToken}`);
    }
    else {
        console.log("Couldn't find image insights token!");
    }
    
    // List of tags
    if (visualSearchResults.tags.length > 0) {
        let firstTagResult = visualSearchResults.tags[0];
        console.log(`Visual search tag count: ${visualSearchResults.tags.length}`);
    
        // List of actions in first tag
        if (firstTagResult.actions.length > 0) {
            let firstActionResult = firstTagResult.actions[0];
            console.log(`First tag action count: ${firstTagResult.actions.length}`);
            console.log(`First tag action type: ${firstActionResult.actionType}`);
        }
        else {
            console.log("Couldn't find tag actions!");
        }
    
    }
    else {
        console.log("Couldn't find image tags!");
    }
    
    ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji internetowej](../../tutorial-bing-visual-search-single-page-app.md)