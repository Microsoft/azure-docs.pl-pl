---
title: 'Szybki Start: wysyłanie żądania wyszukiwania do interfejsu API REST przy użyciu języka C# wyszukiwanie jednostek Bing'
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika Szybki Start, aby wysyłać żądania do interfejsu API REST wyszukiwania wiadomości Bing przy użyciu języka C# i otrzymywać odpowiedzi w formacie JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 10/19/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: a0a77faba971f328c6ae1c5a03f8faf7ccfcf60d
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351490"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-c"></a>Szybki Start: wysyłanie żądania wyszukiwania do wyszukiwanie jednostek Bing interfejsu API REST przy użyciu języka C #

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Umowa Enterprise, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Ten przewodnik Szybki start umożliwi Ci utworzenie Twojego pierwszego wywołania interfejsu API wyszukiwania jednostek Bing i wyświetlenie odpowiedzi JSON. Ta prosta aplikacja w języku C# wysyła zapytanie wyszukiwania wiadomości do interfejsu API i wyświetla odpowiedź. Kod źródłowy tej aplikacji jest dostępny w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingEntitySearchv7.cs).

Mimo że aplikacja jest zapisywana w języku C#, interfejs API jest usługą sieci Web RESTful zgodną z większością języków programowania.


## <a name="prerequisites"></a>Wymagania wstępne

- Dowolna wersja programu [Visual Studio 2017 lub nowszego](https://www.visualstudio.com/downloads/).
- Lub jeśli korzystasz z systemu Linux lub MacOS, możesz skorzystać z tego przewodnika Szybki Start przy użyciu [Visual Studio Code](https://code.visualstudio.com/) i [.NET Core](/dotnet/core/install/macos)
- [Bezpłatne konto platformy Azure](https://azure.microsoft.com/free/dotnet)


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Tworzenie i inicjowanie projektu

1. Utwórz nowe rozwiązanie konsolowe dla języka C# w programie Visual Studio. 
1. Dodaj [Newtonsoft.Js](https://www.nuget.org/packages/Newtonsoft.Json/) do pakietu NuGet.
    1. Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań**.
    2. Wybierz pozycję **Zarządzaj pakietami NuGet**.
    3. Wyszukaj i wybierz pozycję *Newtonsoft.Json*, a następnie zainstaluj pakiet.
1. Następnie Dodaj następujące przestrzenie nazw do głównego pliku kodu:
    
    ```csharp
    using Newtonsoft.Json;
    using System;
    using System.Net.Http;
    using System.Text;
    ```

2. Utwórz nową klasę i Dodaj zmienne dla punktu końcowego interfejsu API, klucza subskrypcji i zapytania, które chcesz przeszukać. Możesz użyć globalnego punktu końcowego w poniższym kodzie lub użyć punktu końcowego [niestandardowej domeny](../../../cognitive-services/cognitive-services-custom-subdomains.md) podrzędnej wyświetlanego w Azure Portal dla zasobu.

    ```csharp
    namespace EntitySearchSample
    {
        class Program
        {
            static string host = "https://api.cognitive.microsoft.com";
            static string path = "/bing/v7.0/entities";
    
            static string market = "en-US";
    
            // NOTE: Replace this example key with a valid subscription key.
            static string key = "ENTER YOUR KEY HERE";
    
            static string query = "italian restaurant near me";
        //...
        }
    }
    ```

## <a name="send-a-request-and-get-the-api-response"></a>Wysyłanie żądania i odbieranie odpowiedzi interfejsu API

1. W klasie utwórz funkcję o nazwie `Search()`. W ramach tej funkcji Utwórz nowy `HttpClient` obiekt i Dodaj swój klucz subskrypcji do `Ocp-Apim-Subscription-Key` nagłówka.

2. Łącząc hosta i ścieżkę, utwórz identyfikator URI Twojego żądania. Następnie Dodaj swój rynek i adres URL — Koduj zapytanie.

3. Oczekiwanie `client.GetAsync()` na uzyskanie odpowiedzi HTTP, a następnie przechowanie odpowiedzi JSON przez oczekiwanie `ReadAsStringAsync()` .

4. Sformatuj ciąg JSON `JsonConvert.DeserializeObject()` i wydrukuj go w konsoli.

      ```csharp
      async static void Search()
      {
       //...
       HttpClient client = new HttpClient();
       client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

       string uri = host + path + "?mkt=" + market + "&q=" + System.Net.WebUtility.UrlEncode(query);

       HttpResponseMessage response = await client.GetAsync(uri);

       string contentString = await response.Content.ReadAsStringAsync();
       dynamic parsedJson = JsonConvert.DeserializeObject(contentString);
       Console.WriteLine(parsedJson);
      }
      ```

5. W `Main()` metodzie aplikacji Wywołaj `Search()` funkcję.
    
    ```csharp
    static void Main(string[] args)
    {
        Search();
        Console.ReadLine();
    }
    ```


## <a name="example-json-response"></a>Przykładowa odpowiedź JSON

Po pomyślnym przetworzeniu żądania zostanie zwrócona odpowiedź w formacie JSON, jak pokazano w następującym przykładzie: 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "italian restaurant near me",
    "askUserForLocation": true
  },
  "places": {
    "value": [
      {
        "_type": "LocalBusiness",
        "webSearchUrl": "https://www.bing.com/search?q=sinful+bakery&filters=local...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "https://www.contoso.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98112",
          "addressCountry": "US",
          "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
      },

      . . .
      {
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Pickles+and+Preserves...",
        "name": "Munson's Pickles and Preserves Farm",
        "url": "https://www.princi.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness",
            "Restaurant"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98101",
          "addressCountry": "US",
          "neighborhood": "Capitol Hill"
        },
        "telephone": "(800) 555-1212"
      },
      
      . . .
    ]
  }
}
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji internetowej](../tutorial-bing-entities-search-single-page-app.md)

* [Czym jest interfejs API wyszukiwania jednostek Bing?](../overview.md )
* [Odwołanie interfejs API wyszukiwania jednostek Bing](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference).