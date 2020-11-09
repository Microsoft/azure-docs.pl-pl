---
title: Wyszukiwanie wizualne Bing przewodniku szybki start dotyczącej biblioteki klienta C#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/26/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 8c50d13f35f3d4f04cacba736e7e9fd5f3fba6f5
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2020
ms.locfileid: "94371851"
---
Skorzystaj z tego przewodnika Szybki Start, aby rozpocząć pobieranie szczegółowych informacji o obrazach z usługi wyszukiwanie wizualne Bing przy użyciu biblioteki klienckiej języka C#. Chociaż wyszukiwanie wizualne Bing ma interfejs API REST zgodny z większością języków programowania, Biblioteka klienta zapewnia łatwy sposób integracji usługi z aplikacjami. Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVisualSearch).

[Dokumentacja](/dotnet/api/overview/azure/cognitiveservices/client/bingvisualsearch?view=azure-dotnet)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingVisualSearch)  |  źródłowy biblioteki [Pakiet (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.VisualSearch/)  |  [Przykłady](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/)

## <a name="prerequisites"></a>Wymagania wstępne

* [Program Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Jeśli używasz systemu Linux/MacOS, możesz uruchomić tę aplikację przy użyciu środowiska [Mono](https://www.mono-project.com/).
* Pakiet NuGet wyszukiwania wizualnego. 
    - W Eksploratorze rozwiązań w programie Visual Studio kliknij prawym przyciskiem myszy swój projekt i wybierz polecenie `Manage NuGet Packages` z menu. Zainstaluj pakiet `Microsoft.Azure.CognitiveServices.Search.VisualSearch`. Zainstalowanie pakietu NuGet powoduje także zainstalowanie następujących elementów:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](~/includes/cognitive-services-bing-visual-search-signup-requirements.md)]

<a name="client"></a>

## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz nowy projekt w programie Visual Studio. Następnie dodaj poniższe dyrektywy.
    
    ```csharp
    using Microsoft.Azure.CognitiveServices.Search.VisualSearch;
    using Microsoft.Azure.CognitiveServices.Search.VisualSearch.Models;
    ```

2. Utwórz wystąpienie klienta przy użyciu klucza subskrypcji.
    
    ```csharp
    var client = new VisualSearchClient(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```
    
## <a name="send-a-search-request"></a>Wysyłanie żądania wyszukiwania 

1. Utwórz element `FileStream` dla obrazów (w tym przypadku `TestImages/image.jpg`). Następnie przy użyciu klienta wyślij żądanie wyszukiwania za pomocą funkcji `client.Images.VisualSearchMethodAsync()`. 
    
    ```csharp
     System.IO.FileStream stream = new FileStream(Path.Combine("TestImages", "image.jpg"), FileMode.Open);
     // The knowledgeRequest parameter is not required if an image binary is passed in the request body
     var visualSearchResults = client.Images.VisualSearchMethodAsync(image: stream, knowledgeRequest: (string)null).Result;
    ```
    
2. Przeanalizuj wyniki poprzedniego zapytania:

    ```csharp
    // Visual Search results
    if (visualSearchResults.Image?.ImageInsightsToken != null)
    {
        Console.WriteLine($"Uploaded image insights token: {visualSearchResults.Image.ImageInsightsToken}");
    }
    else
    {
        Console.WriteLine("Couldn't find image insights token!");
    }
    
    // List of tags
    if (visualSearchResults.Tags.Count > 0)
    {
        var firstTagResult = visualSearchResults.Tags[0];
        Console.WriteLine($"Visual search tag count: {visualSearchResults.Tags.Count}");
    
        // List of actions in first tag
        if (firstTagResult.Actions.Count > 0)
        {
            var firstActionResult = firstTagResult.Actions[0];
            Console.WriteLine($"First tag action count: {firstTagResult.Actions.Count}");
            Console.WriteLine($"First tag action type: {firstActionResult.ActionType}");
        }
        else
        {
            Console.WriteLine("Couldn't find tag actions!");
        }
    }
    ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji internetowej](../../tutorial-bing-visual-search-single-page-app.md)