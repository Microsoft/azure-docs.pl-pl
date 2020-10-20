---
title: Wyszukiwanie w sieci Web Bing przewodniku szybki start dotyczącej biblioteki klienta C#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/19/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 9dbed0de3cf6cc911111825a75430d0c616f4dc4
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92217591"
---
Wyszukiwanie w sieci Web Bing Biblioteka kliencka ułatwia integrację wyszukiwanie w sieci Web Bing z aplikacją w języku C#. Z tego przewodnika Szybki start dowiesz się, jak utworzyć wystąpienie klienta, wysłać żądanie i wyświetlić odpowiedź.

Chcesz zobaczyć kod teraz? Przykłady dla [bibliotek klienckich wyszukiwanie Bing dla platformy .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7) są dostępne w witrynie GitHub.

## <a name="prerequisites"></a>Wymagania wstępne
Oto kilka rzeczy, które są potrzebne przed rozpoczęciem tego przewodnika Szybki start:

* [Program Visual Studio](https://visualstudio.microsoft.com/downloads/) lub
* [Program Visual Studio Code 2017](https://code.visualstudio.com/download)
  * [Rozszerzenie C# for Visual Studio Code](https://visualstudio.microsoft.com/downloads/)
  * [Menedżer pakietów NuGet](https://github.com/jmrog/vscode-nuget-package-manager)
* [Zestaw .NET Core SDK](https://www.microsoft.com/net/download)

[!INCLUDE [bing-web-search-quickstart-signup](~/includes/bing-web-search-quickstart-signup.md)]

## <a name="create-a-project-and-install-dependencies"></a>Tworzenie projektu i instalowanie zależności

> [!TIP]
> Pobierz najnowszy kod jako rozwiązanie programu Visual Studio z usługi [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/).

Pierwszym krokiem jest utworzenie nowego projektu konsoli. Jeśli potrzebujesz pomocy przy konfigurowaniu projektu konsoli, zobacz [Hello World — pierwszy program (Przewodnik programowania w języku C#)](https://docs.microsoft.com/dotnet/csharp/programming-guide/inside-a-program/hello-world-your-first-program). Aby korzystać z zestawu SDK wyszukiwania w Internecie Bing w aplikacji, musisz zainstalować element `Microsoft.Azure.CognitiveServices.Search.WebSearch` przy użyciu Menedżera pakietów NuGet.

[Pakiet zestawu SDK wyszukiwania w Internecie](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.WebSearch/1.2.0) instaluje także następujące elementy:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="declare-dependencies"></a>Deklarowanie zależności

Otwórz projekt w programie Visual Studio lub Visual Studio Code i zaimportuj te zależności:

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.CognitiveServices.Search.WebSearch;
using Microsoft.Azure.CognitiveServices.Search.WebSearch.Models;
using System.Linq;
using System.Threading.Tasks;
```

## <a name="create-project-scaffolding"></a>Tworzenie szkieletu projektu

Podczas tworzenia nowego projektu konsoli powinna zostać utworzona przestrzeń nazw i klasa dla aplikacji. Program powinien wyglądać podobnie do tego przykładu:

```csharp
namespace WebSearchSDK
{
    class YOUR_PROGRAM
    {

        // The code in the following sections goes here.

    }
}
```

W poniższych sekcjach utworzymy naszą przykładową aplikację w ramach tej klasy.

## <a name="construct-a-request"></a>Konstruowanie żądania

Ten kod tworzy zapytanie wyszukiwania.

```csharp
public static async Task WebResults(WebSearchClient client)
{
    try
    {
        var webData = await client.Web.SearchAsync(query: "Yosemite National Park");
        Console.WriteLine("Searching for \"Yosemite National Park\"");

        // Code for handling responses is provided in the next section...

    }
    catch (Exception ex)
    {
        Console.WriteLine("Encountered exception. " + ex.Message);
    }
}
```

## <a name="handle-the-response"></a>Obsługa odpowiedzi

Następnie dodajmy kod do analizy odpowiedzi i wyświetlania wyników. Wartości `Name` i `Url` dla pierwszej strony internetowej, pierwszego obrazu, artykułu i wideo są wyświetlane, jeśli są obecne w obiekcie odpowiedzi.

```csharp
if (webData?.WebPages?.Value?.Count > 0)
{
    // find the first web page
    var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

    if (firstWebPagesResult != null)
    {
        Console.WriteLine("Webpage Results # {0}", webData.WebPages.Value.Count);
        Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
        Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
    }
    else
    {
        Console.WriteLine("Didn't find any web pages...");
    }
}
else
{
    Console.WriteLine("Didn't find any web pages...");
}

/*
 * Images
 * If the search response contains images, the first result's name
 * and url are printed.
 */
if (webData?.Images?.Value?.Count > 0)
{
    // find the first image result
    var firstImageResult = webData.Images.Value.FirstOrDefault();

    if (firstImageResult != null)
    {
        Console.WriteLine("Image Results # {0}", webData.Images.Value.Count);
        Console.WriteLine("First Image result name: {0} ", firstImageResult.Name);
        Console.WriteLine("First Image result URL: {0} ", firstImageResult.ContentUrl);
    }
    else
    {
        Console.WriteLine("Didn't find any images...");
    }
}
else
{
    Console.WriteLine("Didn't find any images...");
}

/*
 * News
 * If the search response contains news articles, the first result's name
 * and url are printed.
 */
if (webData?.News?.Value?.Count > 0)
{
    // find the first news result
    var firstNewsResult = webData.News.Value.FirstOrDefault();

    if (firstNewsResult != null)
    {
        Console.WriteLine("\r\nNews Results # {0}", webData.News.Value.Count);
        Console.WriteLine("First news result name: {0} ", firstNewsResult.Name);
        Console.WriteLine("First news result URL: {0} ", firstNewsResult.Url);
    }
    else
    {
        Console.WriteLine("Didn't find any news articles...");
    }
}
else
{
    Console.WriteLine("Didn't find any news articles...");
}

/*
 * Videos
 * If the search response contains videos, the first result's name
 * and url are printed.
 */
if (webData?.Videos?.Value?.Count > 0)
{
    // find the first video result
    var firstVideoResult = webData.Videos.Value.FirstOrDefault();

    if (firstVideoResult != null)
    {
        Console.WriteLine("\r\nVideo Results # {0}", webData.Videos.Value.Count);
        Console.WriteLine("First Video result name: {0} ", firstVideoResult.Name);
        Console.WriteLine("First Video result URL: {0} ", firstVideoResult.ContentUrl);
    }
    else
    {
        Console.WriteLine("Didn't find any videos...");
    }
}
else
{
    Console.WriteLine("Didn't find any videos...");
}
```

## <a name="declare-the-main-method"></a>Deklarowanie metody main

W tej aplikacji metoda main zawiera kod, który tworzy wystąpienie klienta, weryfikuje klucz `subscriptionKey` i wywołuje metodę `WebResults`. Przed kontynuowaniem upewnij się, że został wprowadzony prawidłowy klucz subskrypcji dla konta platformy Azure.

```csharp
static async Task Main(string[] args)
{
    var client = new WebSearchClient(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

    await WebResults(client);

    Console.WriteLine("Press any key to exit...");
    Console.ReadKey();
}
```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchommy aplikację!

```console
dotnet run
```

## <a name="define-functions-and-filter-results"></a>Definiowanie funkcji i filtrowanie wyników

Teraz, gdy wykonaliśmy pierwsze wywołanie interfejsu API wyszukiwania w Internecie Bing, przyjrzyjmy się kilku funkcjom przedstawiającym funkcjonalności zestawu SDK w zakresie uściślania zapytań i filtrowania wyników. Każdą funkcję można dodać do aplikacji C# utworzonej w poprzedniej sekcji.

### <a name="limit-the-number-of-results-returned-by-bing"></a>Ograniczanie liczby wyników zwracanych przez usługę Bing

W tym przykładzie użyto parametrów `count` i `offset`, aby ograniczyć liczbę wyników zwracanych dla zapytania „Best restaurants in Seattle”. Wartości `Name` i `Url` są wyświetlane dla pierwszego wyniku.

1. Dodaj następujący kod do projektu konsoli:

    ```csharp
    public static async Task WebResultsWithCountAndOffset(WebSearchClient client)
    {
        try
        {
            var webData = await client.Web.SearchAsync(query: "Best restaurants in Seattle", offset: 10, count: 20);
            Console.WriteLine("\r\nSearching for \" Best restaurants in Seattle \"");

            if (webData?.WebPages?.Value?.Count > 0)
            {
                var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

                if (firstWebPagesResult != null)
                {
                    Console.WriteLine("Web Results #{0}", webData.WebPages.Value.Count);
                    Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
                    Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
                }
                else
                {
                    Console.WriteLine("Couldn't find first web result!");
                }
            }
            else
            {
                Console.WriteLine("Didn't see any Web data..");
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine("Encountered exception. " + ex.Message);
        }
    }
    ```

2. Dodaj metodę `WebResultsWithCountAndOffset` do klasy `main`:

    ```csharp
    static async Task Main(string[] args)
    {
        var client = new WebSearchClient(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

        await WebResults(client);
        // Search with count and offset...
        await WebResultsWithCountAndOffset(client);  

        Console.WriteLine("Press any key to exit...");
        Console.ReadKey();
    }
    ```

3. Uruchom aplikację.

### <a name="filter-for-news"></a>Filtrowanie wiadomości

W tym przykładzie użyto parametru `response_filter`, aby przefiltrować wyniki wyszukiwania. Zwrócone wyniki wyszukiwania zostały ograniczone do artykułów z wiadomościami dotyczących hasła „Microsoft”. Wartości `Name` i `Url` są wyświetlane dla pierwszego wyniku.

1. Dodaj następujący kod do projektu konsoli:

    ```csharp
    public static async Task WebSearchWithResponseFilter(WebSearchClient client)
    {
        try
        {
            IList<string> responseFilterstrings = new List<string>() { "news" };
            var webData = await client.Web.SearchAsync(query: "Microsoft", responseFilter: responseFilterstrings);
            Console.WriteLine("\r\nSearching for \" Microsoft \" with response filter \"news\"");

            if (webData?.News?.Value?.Count > 0)
            {
                var firstNewsResult = webData.News.Value.FirstOrDefault();

                if (firstNewsResult != null)
                {
                    Console.WriteLine("News Results #{0}", webData.News.Value.Count);
                    Console.WriteLine("First news result name: {0} ", firstNewsResult.Name);
                    Console.WriteLine("First news result URL: {0} ", firstNewsResult.Url);
                }
                else
                {
                    Console.WriteLine("Couldn't find first News results!");
                }
            }
            else
            {
                Console.WriteLine("Didn't see any News data..");
            }

        }
        catch (Exception ex)
        {
            Console.WriteLine("Encountered exception. " + ex.Message);
        }
    }
    ```

2. Dodaj metodę `WebResultsWithCountAndOffset` do klasy `main`:

    ```csharp
    static Task Main(string[] args)
    {
        var client = new WebSearchClient(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

        await WebResults(client);
        // Search with count and offset...
        await WebResultsWithCountAndOffset(client);  
        // Search with news filter...
        await WebSearchWithResponseFilter(client);

        Console.WriteLine("Press any key to exit...");
        Console.ReadKey();
    }
    ```

3. Uruchom aplikację.

### <a name="use-safe-search-answer-count-and-the-promote-filter"></a>Używanie filtrów safe search, answer count i promote

W tym przykładzie użyto parametrów `answer_count`, `promote` i `safe_search`, aby przefiltrować wyniki wyszukiwania dla zapytania „Music Videos”. Wartości `Name` i `ContentUrl` są wyświetlane dla pierwszego wyniku.

1. Dodaj następujący kod do projektu konsoli:

    ```csharp
    public static async Task WebSearchWithAnswerCountPromoteAndSafeSearch(WebSearchClient client)
    {
        try
        {
            IList<string> promoteAnswertypeStrings = new List<string>() { "videos" };
            var webData = await client.Web.SearchAsync(query: "Music Videos", answerCount: 2, promote: promoteAnswertypeStrings, safeSearch: SafeSearch.Strict);
            Console.WriteLine("\r\nSearching for \"Music Videos\"");

            if (webData?.Videos?.Value?.Count > 0)
            {
                var firstVideosResult = webData.Videos.Value.FirstOrDefault();

                if (firstVideosResult != null)
                {
                    Console.WriteLine("Video Results # {0}", webData.Videos.Value.Count);
                    Console.WriteLine("First Video result name: {0} ", firstVideosResult.Name);
                    Console.WriteLine("First Video result URL: {0} ", firstVideosResult.ContentUrl);
                }
                else
                {
                    Console.WriteLine("Couldn't find videos results!");
                }
            }
            else
            {
                Console.WriteLine("Didn't see any data..");
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine("Encountered exception. " + ex.Message);
        }
    }
    ```

2. Dodaj metodę `WebResultsWithCountAndOffset` do klasy `main`:

    ```csharp
    static async Task Main(string[] args)
    {
        var client = new WebSearchClient(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

        await WebResults(client);
        // Search with count and offset...
        await WebResultsWithCountAndOffset(client);  
        // Search with news filter...
        await WebSearchWithResponseFilter(client);
        // Search with answer count, promote, and safe search
        await WebSearchWithAnswerCountPromoteAndSafeSearch(client);

        Console.WriteLine("Press any key to exit...");
        Console.ReadKey();
    }
    ```

3. Uruchom aplikację.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Pamiętaj, aby po zakończeniu pracy z tym projektem usunąć klucz subskrypcji z kodu aplikacji.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przykłady zastosowania zestawu SDK dla platformy Node.js usług Cognitive Services](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/)
                                    