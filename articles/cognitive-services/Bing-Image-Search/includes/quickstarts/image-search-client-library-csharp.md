---
title: Wyszukiwanie obrazów Bing przewodniku szybki start dotyczącej biblioteki klienta C#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/21/2020
ms.author: aahi
ms.openlocfilehash: e051eca990ae0aa0b5a79c208a594e1b2332bcb2
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97612688"
---
Ten przewodnik Szybki Start umożliwia przeszukiwanie pierwszego obrazu przy użyciu biblioteki klienta wyszukiwanie obrazów Bing. 

Biblioteka wyszukiwania klienta jest otoką interfejsu API REST i zawiera te same funkcje. 

Utworzysz aplikację w języku C#, która wyśle zapytanie wyszukiwania obrazów, przeanalizuje odpowiedź JSON i wyświetla adres URL zwróconego pierwszego obrazu.

Kod źródłowy dla tego przykładu jest dostępny [w witrynie GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingImageSearch) z dodatkową obsługą błędów i adnotacjami.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli używasz systemu Windows, dowolna wersja programu [Visual Studio 2017 lub nowszego](https://visualstudio.microsoft.com/vs/whatsnew/)
* Jeśli używasz macOS lub Linux, [vs Code](https://code.visualstudio.com) z [zainstalowanym programem .NET Core](https://dotnet.microsoft.com/learn/dotnet/hello-world-tutorial/install)
* [Bezpłatna subskrypcja platformy Azure](https://azure.microsoft.com/free/dotnet)

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](~/includes/cognitive-services-bing-image-search-signup-requirements.md)]

Zobacz też [Cennik usług Cognitive Services — interfejs API wyszukiwania Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="create-a-console-project"></a>Tworzenie projektu konsoli

Najpierw utwórz nową aplikację konsolową w języku C#.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visualstudio)

1. Utwórz nowe rozwiązanie konsoli o nazwie *BingImageSearch* w programie Visual Studio.
    
1. Dodaj [pakiet NuGet wyszukiwanie obrazów poznawczego](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.ImageSearch)
    1. Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań**.
    1. Wybierz pozycję **Zarządzaj pakietami NuGet**.
    1. Wyszukaj i wybierz pozycję *Microsoft. Azure. CognitiveServices. Search. ImageSearch*, a następnie zainstaluj pakiet.
    
# <a name="vs-code"></a>[VS Code](#tab/vscode)

1. Otwórz okno terminalu w VS Code.
1. Utwórz nowy projekt konsoli o nazwie *BingImageSearch* , wprowadzając następujący kod w oknie terminalu:
    
    ```bash
    dotnet new console -n BingImageSearch
    ```
1. Otwórz folder *BingImageSearch* w vs Code.
1. Dodaj NuGetPackage [pakietu NuGet wyszukiwanie obrazów](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.ImageSearch) , wprowadzając następujący kod w oknie terminalu:

    ```bash
    dotnet add package Microsoft.Azure.CognitiveServices.Search.ImageSearch
    ```

---

## <a name="initialize-the-application"></a>Inicjowanie aplikacji


1. Zastąp wszystkie `using` instrukcje w *program.cs* następującym kodem:

    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.ImageSearch;
    using Microsoft.Azure.CognitiveServices.Search.ImageSearch.Models;
    ```

1. W `Main` metodzie projektu Utwórz zmienne dla prawidłowego klucza subskrypcji, wyniki z obrazu do zwrócenia przez usługę Bing i termin wyszukiwania. Następnie utwórz wystąpienie klienta wyszukiwania obrazów za pomocą klucza.

    ```csharp
    static async Task Main(string[] args)
    {
        //IMPORTANT: replace this variable with your Cognitive Services subscription key
        string subscriptionKey = "ENTER YOUR KEY HERE";
        //stores the image results returned by Bing
        Images imageResults = null;
        // the image search term to be used in the query
        string searchTerm = "canadian rockies";
        
        //initialize the client
        //NOTE: If you're using version 1.2.0 or below for the Bing Image Search client library, 
        // use ImageSearchAPI() instead of ImageSearchClient() to initialize your search client.
        
        var client = new ImageSearchClient(new ApiKeyServiceClientCredentials(subscriptionKey));
    }
    ```
    
## <a name="send-a-search-query-using-the-client"></a>Wysyłanie zapytania wyszukiwania przy użyciu klienta
    
Nadal w `Main` metodzie Użyj klienta do wyszukiwania tekstu zapytania:
    
```csharp
// make the search request to the Bing Image API, and get the results"
imageResults = await client.Images.SearchAsync(query: searchTerm).Result; //search query
```

## <a name="parse-and-view-the-first-image-result"></a>Analizowanie i wyświetlanie wyniku pierwszego obrazu

Analizuj wyniki obrazów zwróconych w odpowiedzi. 

Jeśli odpowiedź zawiera wyniki wyszukiwania, należy zapisać pierwszy wynik i wydrukować część jego szczegółów.

```csharp
if (imageResults != null)
{
    //display the details for the first image result.
    var firstImageResult = imageResults.Value.First();
    Console.WriteLine($"\nTotal number of returned images: {imageResults.Value.Count}\n");
    Console.WriteLine($"Copy the following URLs to view these images on your browser.\n");
    Console.WriteLine($"URL to the first image:\n\n {firstImageResult.ContentUrl}\n");
    Console.WriteLine($"Thumbnail URL for the first image:\n\n {firstImageResult.ThumbnailUrl}");
    Console.WriteLine("Press any key to exit ...");
    Console.ReadKey();
}
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek dotyczący jednostronicowej aplikacji wyszukiwania obrazów Bing](../../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Zobacz także

* [Czym jest funkcja wyszukiwania obrazów Bing?](../../overview.md)  
* [Wypróbuj interaktywny pokaz online](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Przykłady dla zestawu Azure Cognitive Services SDK na platformie .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
* [Dokumentacja usługi Azure Cognitive Services](../../../index.yml)
* [Dokumentacja interfejsu API wyszukiwania obrazów Bing](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)