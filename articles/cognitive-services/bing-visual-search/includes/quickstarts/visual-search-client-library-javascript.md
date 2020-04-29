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
ms.openlocfilehash: 404529bf269f899603b92c6c23b0d95cd2749ee5
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80550023"
---
Skorzystaj z tego przewodnika Szybki Start, aby rozpocząć pobieranie szczegółowych informacji o obrazach z usługi wyszukiwanie wizualne Bing przy użyciu biblioteki klienckiej języka JavaScript. Chociaż wyszukiwanie wizualne Bing ma interfejs API REST zgodny z większością języków programowania, Biblioteka klienta zapewnia łatwy sposób integracji usługi z aplikacjami. Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/visualSearch.js). 

[Reference documentation](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-visualsearch/?view=azure-node-latest) |  | [Przykłady](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/) pakietu | [kodu źródłowego biblioteki](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-visualsearch)dokumentacji referencyjnej[(npm)](https://www.npmjs.com/package/@azure/cognitiveservices-visualsearch)

## <a name="prerequisites"></a>Wymagania wstępne
* [Node.js](https://www.nodejs.org/)
* Biblioteka klienta wyszukiwanie wizualne Bing dla języka JavaScript
    * Aby skonfigurować aplikację konsolową przy użyciu biblioteki klienta wyszukiwanie wizualne Bing, uruchom następujące polecenia:
        1. `npm install ms-rest-azure`
        2. `npm install azure-cognitiveservices-visualsearch`.


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](~/includes/cognitive-services-bing-visual-search-signup-requirements.md)]

<a name="client"></a>

## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz nowy plik w języku JavaScript w ulubionym środowisku IDE lub edytorze i dodaj następujące wymagania. Następnie utwórz zmienne dla klucza subskrypcji, identyfikatora konfiguracji niestandardowej i ścieżki do pliku obrazu, który chcesz przekazać. 

    ```javascript
    const os = require("os");
    const async = require('async');
    const fs = require('fs');
    const Search = require('azure-cognitiveservices-visualsearch');
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    
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
