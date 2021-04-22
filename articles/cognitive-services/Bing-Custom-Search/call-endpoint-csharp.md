---
title: 'Szybki start: wywołanie punktu końcowego wyszukiwanie niestandardowe Bing przy użyciu języka C# | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Użyj tego przewodnika Szybki start, aby rozpocząć żądanie wyników z wystąpienia wyszukiwania niestandardowego Bing w języku C#.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 1d3accdb20073bd1e9b29988b78d7eacd49976b8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862313"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-c"></a>Szybki start: wywołanie punktu końcowego wyszukiwanie niestandardowe Bing przy użyciu języka C # 

> [!WARNING]
> Wyszukiwanie Bing API są przechodnie z Cognitive Services do Wyszukiwanie Bing Services. Od **30 października 2020** r. wszystkie nowe wystąpienia usługi Wyszukiwanie Bing należy aprowizować zgodnie z procesem [udokumentowanym tutaj.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)
> Wyszukiwanie Bing aprowizowane przy użyciu usługi Cognitive Services będą obsługiwane przez następne trzy lata lub do Enterprise Agreement, w zależności od tego, co nastąpi najpierw.
> Aby uzyskać instrukcje dotyczące migracji, [zobacz Wyszukiwanie Bing Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Skorzystaj z tego przewodnika Szybki start, aby dowiedzieć się, jak żądać wyników wyszukiwania z wyszukiwanie niestandardowe Bing wystąpienia. Mimo że ta aplikacja jest napisana w języku C#, interfejs API wyszukiwanie niestandardowe Bing jest usługą internetową RESTful zgodną z większością języków programowania. Kod źródłowy tego przykładu jest dostępny w witrynie [GitHub.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingCustomSearchv7.cs)

## <a name="prerequisites"></a>Wymagania wstępne

- Wystąpienie wyszukiwania niestandardowego Bing. Aby uzyskać więcej informacji, zobacz [Szybki start: tworzenie pierwszego wyszukiwanie niestandardowe Bing wystąpienia .](quick-start.md)
- [Microsoft .NET Core](https://dotnet.microsoft.com/download).
- Dowolna wersja [programu Visual Studio 2019 lub nowszy.](https://www.visualstudio.com/downloads/)
- Jeśli używasz systemu Linux/MacOS, możesz uruchomić tę aplikację przy użyciu środowiska [Mono](https://www.mono-project.com/).
- Pakiet [Wyszukiwanie niestandardowe Bing](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/2.0.0) NuGet. 

   Aby zainstalować ten pakiet w programie Visual Studio: 
     1. Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań**, a następnie wybierz pozycję **Zarządzaj pakietami NuGet.** 
     2. Wyszukaj i wybierz *pozycję Microsoft.Azure.CognitiveServices.Search.CustomSearch,* a następnie zainstaluj pakiet.

   Podczas instalowania wyszukiwanie niestandardowe Bing NuGet program Visual Studio także następujące pakiety:
     - **Microsoft.Rest.ClientRuntime**
     - **Microsoft.Rest.ClientRuntime.Azure**
     - **Newtonsoft.Json**


[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz nową aplikację konsoli języka C# w programie Visual Studio. Następnie dodaj następujące pakiety do projektu:

    ```csharp
    using System;
    using System.Net.Http;
    using System.Web;
    using Newtonsoft.Json;
    ```

2. Utwórz następujące klasy do przechowywania wyników wyszukiwania zwracanych przez interfejs API wyszukiwanie niestandardowe Bing API:

    ```csharp
    public class BingCustomSearchResponse {        
        public string _type{ get; set; }            
        public WebPages webPages { get; set; }
    }

    public class WebPages {
        public string webSearchUrl { get; set; }
        public int totalEstimatedMatches { get; set; }
        public WebPage[] value { get; set; }        
    }

    public class WebPage {
        public string name { get; set; }
        public string url { get; set; }
        public string displayUrl { get; set; }
        public string snippet { get; set; }
        public DateTime dateLastCrawled { get; set; }
        public string cachedPageUrl { get; set; }
    }
    ```

3. W metodzie głównej projektu utwórz następujące zmienne dla klucza subskrypcji interfejsu API wyszukiwanie niestandardowe Bing, niestandardowego identyfikatora konfiguracji wystąpienia wyszukiwania i terminu wyszukiwania:

    ```csharp
    var subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    var customConfigId = "YOUR-CUSTOM-CONFIG-ID";
    var searchTerm = args.Length > 0 ? args[0]:"microsoft";
    ```

4. Skonstruuj adres URL żądania, dołączając termin wyszukiwania do parametru zapytania oraz niestandardowy identyfikator konfiguracji wystąpienia wyszukiwania `q=` do `customconfig=` parametru . Oddziel parametry za pomocą ampersand ( `&` ). Dla wartości zmiennej możesz użyć globalnego punktu końcowego w poniższym kodzie lub użyć niestandardowego punktu końcowego poddomeny `url` wyświetlanego w Azure Portal zasobu. [](../../cognitive-services/cognitive-services-custom-subdomains.md)

    ```csharp
    var url = "https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?" +
                "q=" + searchTerm + "&" +
                "customconfig=" + customConfigId;
    ```

## <a name="send-and-receive-a-search-request"></a>Wysyłanie i odbieranie żądania wyszukiwania 

1. Utwórz żądanie klienta i dodaj klucz subskrypcji do nagłówka `Ocp-Apim-Subscription-Key`.

    ```csharp
    var client = new HttpClient();
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
    ```

2. Wykonaj żądanie wyszukiwania i pobierz odpowiedź jako obiekt JSON.

    ```csharp
    var httpResponseMessage = client.GetAsync(url).Result;
    var responseContent = httpResponseMessage.Content.ReadAsStringAsync().Result;
    BingCustomSearchResponse response = JsonConvert.DeserializeObject<BingCustomSearchResponse>(responseContent);
    ```
## <a name="process-and-view-the-results"></a>Przetwarzanie i wyświetlanie wyników

- Przejrzyj obiekt odpowiedzi, aby wyświetlić informacje na temat każdego wyniku wyszukiwania, w tym jego nazwę, adres URL i datę ostatniego przeszukiwania strony internetowej.

    ```csharp
    for(int i = 0; i < response.webPages.value.Length; i++) {                
        var webPage = response.webPages.value[i];
        
        Console.WriteLine("name: " + webPage.name);
        Console.WriteLine("url: " + webPage.url);                
        Console.WriteLine("displayUrl: " + webPage.displayUrl);
        Console.WriteLine("snippet: " + webPage.snippet);
        Console.WriteLine("dateLastCrawled: " + webPage.dateLastCrawled);
        Console.WriteLine();
    }
    Console.WriteLine("Press any key to exit...");
    Console.ReadKey();
    ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie aplikacji internetowej z funkcją wyszukiwania niestandardowego](./tutorials/custom-search-web-page.md)