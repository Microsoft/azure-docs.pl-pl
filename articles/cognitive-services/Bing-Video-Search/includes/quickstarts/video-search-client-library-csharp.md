---
title: Wyszukiwanie wideo Bing przewodniku szybki start dotyczącej biblioteki klienta C#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 6d50a8e2c9d0263616b25e25958be6a6f0fb7fe1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88929277"
---
Skorzystaj z tego przewodnika Szybki Start, aby rozpocząć wyszukiwanie wiadomości za pomocą biblioteki klienta wyszukiwanie wideo Bing dla języka C#. Chociaż wyszukiwanie wideo Bing ma interfejs API REST zgodny z większością języków programowania, Biblioteka klienta zapewnia łatwy sposób integracji usługi z aplikacjami. Kod źródłowy dla tego przykładu można znaleźć w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVideoSearch) z dodatkowymi adnotacjami i funkcjami.

## <a name="prerequisites"></a>Wymagania wstępne

* Dowolna wersja programu [Visual Studio 2017 lub nowszego](https://visualstudio.microsoft.com/downloads/).
* Json.NET Framework, dostępny [jako pakiet NuGet](https://www.nuget.org/packages/Newtonsoft.Json/).

Aby dodać wyszukiwanie wideo Bingą bibliotekę kliencką do projektu, wybierz pozycję **Zarządzaj pakietami NuGet** z **Eksplorator rozwiązań** w programie Visual Studio. Dodaj pakiet `Microsoft.Azure.CognitiveServices.Search.VideoSearch`.

Zainstalowanie [[zestawu NuGet Video Search SDK]](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.VideoSearch/1.2.0)spowoduje również zainstalowanie następujących zależności:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Tworzenie i inicjowanie projektu

1. Utwórz nowe rozwiązanie konsolowe dla języka C# w programie Visual Studio. Dodaj następujący kod do głównego pliku kodu.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch.Models;
    ```

2. Utwórz wystąpienie klienta, tworząc nowy obiekt `ApiKeyServiceClientCredentials` za pomocą klucza subskrypcji, a następnie wywołaj konstruktor.

    ```csharp
    var client = new VideoSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

## <a name="send-a-search-request-and-process-the-results"></a>Wyślij żądanie wyszukiwania i przetwórz wyniki

1. Użyj klienta do wysłania żądania wyszukiwania. Użyj elementu „SwiftKey” na potrzeby zapytania wyszukiwania.

    ```csharp
    var videoResults = client.Videos.SearchAsync(query: "SwiftKey").Result;
    ```

2. Jeśli zostały zwrócone jakiekolwiek wyniki, pobierz pierwszy z nich przy użyciu funkcji `videoResults.Value[0]`. Następnie wyświetl identyfikator, tytuł i adres URL wideo.

    ```csharp
    if (videoResults.Value.Count > 0)
    {
        var firstVideoResult = videoResults.Value[0];

        Console.WriteLine($"\r\nVideo result count: {videoResults.Value.Count}");
        Console.WriteLine($"First video id: {firstVideoResult.VideoId}");
        Console.WriteLine($"First video name: {firstVideoResult.Name}");
        Console.WriteLine($"First video url: {firstVideoResult.ContentUrl}");
    }
    else
    {
        Console.WriteLine("Couldn't find video results!");
    }
    ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie internetowej aplikacji jednostronicowej](../../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Zobacz też 

* [Co to jest interfejs API wyszukiwania wideo Bing?](../../overview.md)
* [Przykłady zestawu SDK programu .NET dla usług poznawczych](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
