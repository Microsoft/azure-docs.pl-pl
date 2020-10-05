---
title: Wyszukiwanie niestandardowe Bing przewodniku szybki start dotyczącej biblioteki klienta C#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.author: aahi
ms.openlocfilehash: ba80d1396b30b61bdfe4c121220429f5a7d994b0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "79486051"
---
Wprowadzenie do biblioteki klienta wyszukiwanie niestandardowe Bing dla języka C#. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań. Interfejs API wyszukiwania niestandardowego Bing umożliwia tworzenie dostosowanych, bezpłatnych funkcji wyszukiwania w usłudze AD dla interesujących Cię tematów. Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingCustomWebSearch).

Użyj biblioteki klienta wyszukiwanie niestandardowe Bing dla języka C#, aby:
* Znajdź wyniki wyszukiwania w sieci Web w wystąpieniu wyszukiwanie niestandardowe Bing.

[Dokumentacja](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingcustomsearch?view=azure-dotnet)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingCustomSearch)  |  źródłowy biblioteki [Pakiet (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0)  |  [Przykłady](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)


## <a name="prerequisites"></a>Wymagania wstępne

- Wystąpienie wyszukiwania niestandardowego Bing. Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie pierwszego wystąpienia wyszukiwanie niestandardowe Bing](../../quick-start.md) .
- Microsoft [.NET Core](https://www.microsoft.com/net/download/core)
- Dowolna wersja programu [Visual Studio 2017 lub nowszego](https://www.visualstudio.com/downloads/)
- Jeśli używasz systemu Linux/MacOS, możesz uruchomić tę aplikację przy użyciu środowiska [Mono](https://www.mono-project.com/).
- Pakiet NuGet [wyszukiwanie niestandardowe Bing](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0)  . 
    - W **Eksplorator rozwiązań** w programie Visual Studio kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Zarządzaj pakietami NuGet** z menu. Zainstaluj pakiet `Microsoft.Azure.CognitiveServices.Search.CustomSearch`. Zainstalowanie pakietu NuGet Custom Search powoduje również zainstalowanie następujących zestawów:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz nową aplikację konsoli języka C# w programie Visual Studio. Następnie dodaj do projektu poniższe pakiety.

    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.CustomSearch;
    ```

2. W metodzie głównej aplikacji utwórz wystąpienie klienta wyszukiwania przy użyciu klucza interfejsu API.

    ```csharp
    var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-SUBSCRIPTION-KEY"));
    ```

## <a name="send-the-search-request-and-receive-a-response"></a>Wysyłanie żądania wyszukiwania i odbieranie odpowiedzi
    
1. Wyślij zapytanie wyszukiwania przy użyciu metody `SearchAsync()` klienta i zapisz odpowiedź. Zastąp wartość `YOUR-CUSTOM-CONFIG-ID` identyfikatorem konfiguracji wystąpienia (identyfikator można znaleźć w [portalu usługi wyszukiwania niestandardowego Bing](https://www.customsearch.ai/)). W tym przykładzie wyszukiwany jest termin „Xbox”.

    ```csharp
    // This will look up a single query (Xbox).
    var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
    ```

2. Metoda `SearchAsync()` zwraca obiekt `WebData`. Użyj obiektu, aby iterować po wszystkich znalezionych obiektach klienta `WebPages`. Ten kod znajduje pierwszy wynik w postaci strony internetowej i wyświetla wartości elementów `Name` oraz `URL` strony internetowej.

    ```csharp
    if (webData?.WebPages?.Value?.Count > 0)
    {
        // find the first web page
        var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

        if (firstWebPagesResult != null)
        {
            Console.WriteLine("Number of webpage results {0}", webData.WebPages.Value.Count);
            Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
            Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
        }
        else
        {
            Console.WriteLine("Couldn't find web results!");
        }
    }
    else
    {
        Console.WriteLine("Didn't see any Web data..");
    }
    ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie aplikacji internetowej z funkcją wyszukiwania niestandardowego](../../tutorials/custom-search-web-page.md)
