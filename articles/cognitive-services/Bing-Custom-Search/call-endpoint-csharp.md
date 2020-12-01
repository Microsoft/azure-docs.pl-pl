---
title: 'Szybki Start: wywoływanie punktu końcowego wyszukiwanie niestandardowe Bing przy użyciu języka C# | Microsoft Docs'
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
ms.openlocfilehash: b892194a0e716aa3de218bc6edb6c38cdc898935
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96338660"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-c"></a>Szybki Start: wywoływanie punktu końcowego wyszukiwanie niestandardowe Bing przy użyciu języka C # 

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Umowa Enterprise, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Skorzystaj z tego przewodnika Szybki Start, aby dowiedzieć się, jak żądać wyników wyszukiwania z wystąpienia wyszukiwanie niestandardowe Bing. Mimo że aplikacja jest zapisywana w języku C#, interfejs API wyszukiwania niestandardowego Bing jest usługą sieci Web RESTful zgodną z większością języków programowania. Kod źródłowy dla tego przykładu jest dostępny w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingCustomSearchv7.cs).

## <a name="prerequisites"></a>Wymagania wstępne

- Wystąpienie wyszukiwania niestandardowego Bing. Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie pierwszego wystąpienia wyszukiwanie niestandardowe Bing](quick-start.md).
- [Microsoft .NET rdzeń](https://www.microsoft.com/net/download/core).
- Dowolna wersja programu [Visual Studio 2019 lub nowszego](https://www.visualstudio.com/downloads/).
- Jeśli używasz systemu Linux/MacOS, możesz uruchomić tę aplikację przy użyciu środowiska [Mono](https://www.mono-project.com/).
- Pakiet NuGet [wyszukiwanie niestandardowe Bing](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/2.0.0) . 

   Aby zainstalować ten pakiet w programie Visual Studio: 
     1. Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań**, a następnie wybierz pozycję **Zarządzaj pakietami NuGet**. 
     2. Wyszukaj i wybierz pozycję *Microsoft. Azure. CognitiveServices. Search. CustomSearch*, a następnie zainstaluj pakiet.

   Po zainstalowaniu pakietu NuGet wyszukiwanie niestandardowe Bing program Visual Studio instaluje również następujące pakiety:
     - **Microsoft.Rest.ClientRuntime**
     - **Microsoft. Rest. ClientRuntime. Azure**
     - **Newtonsoft.Json**


[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz nową aplikację konsoli języka C# w programie Visual Studio. Następnie Dodaj następujące pakiety do projektu:

    ```csharp
    using System;
    using System.Net.Http;
    using System.Web;
    using Newtonsoft.Json;
    ```

2. Utwórz następujące klasy do przechowywania wyników wyszukiwania zwróconych przez interfejs API wyszukiwania niestandardowego Bing:

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

3. W głównej metodzie projektu Utwórz następujące zmienne dla klucza subskrypcji interfejs API wyszukiwania niestandardowego Bing, identyfikator niestandardowej konfiguracji wystąpienia wyszukiwania i termin wyszukiwania:

    ```csharp
    var subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    var customConfigId = "YOUR-CUSTOM-CONFIG-ID";
    var searchTerm = args.Length > 0 ? args[0]:"microsoft";
    ```

4. Utwórz adres URL żądania przez dołączenie terminu wyszukiwania do `q=` parametru zapytania i NIESTANDARDOWEGO identyfikatora konfiguracji wystąpienia wyszukiwania do `customconfig=` parametru. Oddziel parametry znakiem handlowego "i" ( `&` ). Dla `url` wartości zmiennej można użyć globalnego punktu końcowego w poniższym kodzie lub użyć niestandardowego punktu końcowego [poddomeny](../../cognitive-services/cognitive-services-custom-subdomains.md) wyświetlanego w Azure Portal dla zasobu.

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