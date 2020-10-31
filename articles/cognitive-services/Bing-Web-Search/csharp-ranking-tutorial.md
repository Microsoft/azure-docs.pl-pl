---
title: Używanie rangi do wyświetlania wyników wyszukiwania
titleSuffix: Azure Cognitive Services
description: Pokazuje, jak używać odpowiedzi Bing RankingResponse do wyświetlania wyników wyszukiwania w porządku rangi.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 2575A80C-FC74-4631-AE5D-8101CF2591D3
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: tutorial
ms.date: 06/24/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 9131dbff9b732ecfc7f6edb62b42959abcc17da8
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93078683"
---
# <a name="build-a-console-app-search-client-in-c"></a>Tworzenie klienta wyszukiwania aplikacji konsoli w języku C #

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](https://aka.ms/cogsvcs/bingmove).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Umowa Enterprise, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](https://aka.ms/cogsvcs/bingmigration).

W tym samouczku przedstawiono sposób tworzenia prostej aplikacji konsolowej platformy .NET Core, która umożliwia użytkownikom wykonywanie zapytań dotyczących interfejs API wyszukiwania w sieci Web Bing i wyświetlanie rankingowych wyników.

W tym samouczku pokazano, jak:

- Utwórz proste zapytanie do interfejs API wyszukiwania w sieci Web Bing
- Wyświetl wyniki zapytania w kolejności uporządkowanej

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać czynności opisane w samouczku, musisz:

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* Gdy masz subskrypcję platformy Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title=" Utwórz zasób wyszukiwanie Bing "  target="_blank"> utwórz zasób Wyszukiwanie Bing <span class="docon docon-navigate-external x-hidden-focus"></span> </a> w Azure Portal, aby uzyskać klucz i punkt końcowy. Po wdrożeniu programu kliknij pozycję **Przejdź do zasobu** .
* [Środowisko IDE programu Visual Studio](https://www.visualstudio.com/downloads/).

## <a name="create-a-new-console-app-project"></a>Utwórz nowy projekt aplikacji konsoli

W programie Visual Studio utwórz projekt za pomocą kombinacji `Ctrl`+`Shift`+`N`.

W oknie dialogowym **Nowy projekt** kliknij pozycję **Visual C# > Windows Classic Desktop > aplikacji konsolowej (.NET Framework)** .

Nadaj aplikacji nazwę **MyConsoleSearchApp** , a następnie kliknij przycisk **OK** .

## <a name="add-the-jsonnet-nuget-package-to-the-project"></a>Dodawanie pakietu NuGet JSON.net do projektu

JSON.net umożliwia działanie z odpowiedziami JSON zwracanymi przez interfejs API. Dodaj swój pakiet NuGet do projektu:

- W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Zarządzaj pakietami NuGet..** ..
- Na karcie  **Przeglądaj** Wyszukaj pozycję `Newtonsoft.Json` . Wybierz najnowszą wersję, a następnie kliknij przycisk **Instaluj** .
- Kliknij przycisk **OK** w oknie **Przegląd zmian** .
- Zamknij kartę programu Visual Studio zatytułowaną **NuGet: MyConsoleSearchApp** .

## <a name="add-a-reference-to-systemweb"></a>Dodawanie odwołania do elementu System. Web

Ten samouczek opiera się na `System.Web` zestawie. Dodaj odwołanie do tego zestawu do projektu:

- W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy pozycję **odwołania** i wybierz polecenie **Dodaj odwołanie...**
- Wybierz pozycję **zestawy > Framework** , a następnie przewiń w dół i sprawdź **System. Web**
- Wybierz **przycisk OK**

## <a name="add-some-necessary-using-statements"></a>Dodaj niektóre niezbędne instrukcje using

Kod w tym samouczku wymaga trzech dodatkowych instrukcji using. Dodaj te instrukcje poniżej istniejących `using` instrukcji w górnej części **program.cs** :

```csharp
using System.Web;
using System.Net.Http;
```

## <a name="ask-the-user-for-a-query"></a>Podawanie użytkownikowi zapytania

W **Eksplorator rozwiązań** Otwórz **program.cs** . Aktualizowanie `Main()` metody:

```csharp
static void Main()
{
    // Get the user's query
    Console.Write("Enter Bing query: ");
    string userQuery = Console.ReadLine();
    Console.WriteLine();

    // Run the query and display the results
    RunQueryAndDisplayResults(userQuery);

    // Prevent the console window from closing immediately
    Console.WriteLine("\nHit ENTER to exit...");
    Console.ReadLine();
}
```

Ta metoda:

- Prosi użytkownika o zapytanie
- Wywołuje `RunQueryAndDisplayResults(userQuery)` wykonywanie zapytania i wyświetla wyniki
- Czeka na dane wejściowe użytkownika w celu uniemożliwienia natychmiastowego zamknięcia okna konsoli.

## <a name="search-for-query-results-using-the-bing-web-search-api"></a>Wyszukaj wyniki zapytania przy użyciu interfejs API wyszukiwania w sieci Web Bing

Następnie Dodaj metodę, która wysyła zapytanie do interfejsu API i wyświetla wyniki:

```csharp
static void RunQueryAndDisplayResults(string userQuery)
{
    try
    {
        // Create a query
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "<YOUR_SUBSCRIPTION_KEY_GOES_HERE>");
        var queryString = HttpUtility.ParseQueryString(string.Empty);
        queryString["q"] = userQuery;
        var query = "https://api.cognitive.microsoft.com/bing/v7.0/search?" + queryString;

        // Run the query
        HttpResponseMessage httpResponseMessage = client.GetAsync(query).Result;

        // Deserialize the response content
        var responseContentString = httpResponseMessage.Content.ReadAsStringAsync().Result;
        Newtonsoft.Json.Linq.JObject responseObjects = Newtonsoft.Json.Linq.JObject.Parse(responseContentString);

        // Handle success and error codes
        if (httpResponseMessage.IsSuccessStatusCode)
        {
            DisplayAllRankedResults(responseObjects);
        }
        else
        {
            Console.WriteLine($"HTTP error status code: {httpResponseMessage.StatusCode.ToString()}");
        }
    }
    catch (Exception e)
    {
        Console.WriteLine(e.Message);
    }
}
```

Ta metoda:

- Tworzy `HttpClient` zapytanie do interfejsu API wyszukiwanie w sieci Web
- Ustawia `Ocp-Apim-Subscription-Key` nagłówek HTTP, który będzie używany przez usługę Bing do uwierzytelniania żądania
- Wykonuje żądanie i używa JSON.net do deserializacji wyników
- Wywołania `DisplayAllRankedResults(responseObjects)` , aby wyświetlić wszystkie wyniki w kolejności uporządkowanej

Upewnij się, że ustawiono wartość `Ocp-Apim-Subscription-Key` klucza subskrypcji.

## <a name="display-ranked-results"></a>Wyświetl rankingowe wyniki

Przed wyświetleniem sposobu wyświetlania wyników w kolejności uporządkowanej zapoznaj się z przykładową odpowiedzią na wyszukiwanie w sieci Web:

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346...",
        "totalEstimatedMatches" : 982000,
        "value" : [{
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
            "name" : "Contoso Sailing Club - Seattle",
            "url" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FE...",
            "displayUrl" : "https:\/\/contososailingsea...",
            "snippet" : "Come sail with Contoso in Seattle...",
            "dateLastCrawled" : "2017-04-07T02:25:00"
        },
        {
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/7\/#WebPages.6",
            "name" : "Contoso Sailing Lessons - Official Site",
            "url" : "http:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FE...",
            "displayUrl" : "https:\/\/www.constososailinglessonsseat...",
            "snippet" : "Contoso sailing lessons in Seattle...",
            "dateLastCrawled" : "2017-04-09T14:30:00"
        },

        ...

        ],
        "someResultsRemoved" : true
    },
    "relatedSearches" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/7\/#RelatedSearches",
        "value" : [{
            "text" : "sailing lessons",
            "displayText" : "sailing lessons",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346E..."
        }

        ...

        ]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "WebPages",
                "resultIndex" : 0,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 1,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.1"
                }
            }

            ...

            ]
        },
        "sidebar" : {
            "items" : [{
                "answerType" : "RelatedSearches",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches"
                }
            }]
        }
    }
}
```

`rankingResponse`Obiekt JSON ([Dokumentacja](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse)) zawiera opis odpowiedniej kolejności wyświetlania wyników wyszukiwania. Zawiera co najmniej jedną z następujących grup z priorytetem:

- `pole`: Wyniki wyszukiwania, aby uzyskać najbardziej widoczne traktowanie (na przykład wyświetlane nad linii głównej i paskiem bocznym).
- `mainline`: Wyniki wyszukiwania do wyświetlenia w linii głównej.
- `sidebar`: Wyniki wyszukiwania do wyświetlenia na pasku bocznym. Jeśli nie ma paska bocznego, Wyświetl wyniki poniżej linii głównej.

KOD JSON odpowiedzi rankingu może zawierać co najmniej jedną grupę.

W **program.cs** , Dodaj następującą metodę, aby wyświetlić wyniki w prawidłowym porządku uporządkowanym:

```csharp
static void DisplayAllRankedResults(Newtonsoft.Json.Linq.JObject responseObjects)
{
    string[] rankingGroups = new string[] { "pole", "mainline", "sidebar" };

    // Loop through the ranking groups in priority order
    foreach (string rankingName in rankingGroups)
    {
        Newtonsoft.Json.Linq.JToken rankingResponseItems = responseObjects.SelectToken($"rankingResponse.{rankingName}.items");
        if (rankingResponseItems != null)
        {
            foreach (Newtonsoft.Json.Linq.JObject rankingResponseItem in rankingResponseItems)
            {
                Newtonsoft.Json.Linq.JToken resultIndex;
                rankingResponseItem.TryGetValue("resultIndex", out resultIndex);
                var answerType = rankingResponseItem.Value<string>("answerType");
                switch (answerType)
                {
                    case "WebPages":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("webPages.value"), "WebPage", "name", "url", "displayUrl", "snippet");
                        break;
                    case "News":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("news.value"), "News", "name", "url", "description");
                        break;
                    case "Images":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("images.value"), "Image", "thumbnailUrl");
                        break;
                    case "Videos":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("videos.value"), "Video", "embedHtml");
                        break;
                    case "RelatedSearches":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("relatedSearches.value"), "RelatedSearch", "displayText", "webSearchUrl");
                        break;
                }
            }
        }
    }
}
```

Ta metoda:

- Pętle względem `rankingResponse` grup, które zawiera odpowiedź
- Wyświetla elementy w każdej grupie przez wywołanie `DisplaySpecificResults(...)`

W **program.cs** Dodaj następujące dwie metody:

```csharp
static void DisplaySpecificResults(Newtonsoft.Json.Linq.JToken resultIndex, Newtonsoft.Json.Linq.JToken items, string title, params string[] fields)
{
    if (resultIndex == null)
    {
        foreach (Newtonsoft.Json.Linq.JToken item in items)
        {
            DisplayItem(item, title, fields);
        }
    }
    else
    {
        DisplayItem(items.ElementAt((int)resultIndex), title, fields);
    }
}

static void DisplayItem(Newtonsoft.Json.Linq.JToken item, string title, string[] fields)
{
    Console.WriteLine($"{title}: ");
    foreach( string field in fields )
    {
        Console.WriteLine($"- {field}: {item[field]}");
    }
    Console.WriteLine();
}
```

Te metody współpracują ze sobą w celu wyprowadzania wyników wyszukiwania do konsoli programu.

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację. Dane wyjściowe powinny wyglądać mniej więcej tak:

```
Enter Bing query: sailing lessons seattle

WebPage:
- name: Contoso Sailing Club - Seattle
- url: https://www.bing.com/cr?IG=70BE289346ED4594874FE...
- displayUrl: https://contososailingsea....
- snippet: Come sail with Contoso in Seattle...

WebPage:
- name: Contoso Sailing Lessons Seattle - Official Site
- url: http://www.bing.com/cr?IG=70BE289346ED4594874FE...
- displayUrl: https://www.constososailinglessonsseat...
- snippet: Contoso sailing lessons in Seattle...

...
```

## <a name="next-steps"></a>Następne kroki

Przeczytaj więcej [na temat korzystania z funkcji klasyfikacji, aby wyświetlić wyniki](rank-results.md).
