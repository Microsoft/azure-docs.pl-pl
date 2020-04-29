---
title: 'Pobierz model z wywołaniem REST w języku C #'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: 96129b9141b4759fd61b539fa08354f02af3af7b
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80151359"
---
## <a name="prerequisites"></a>Wymagania wstępne

* Azure Language Understanding — tworzenie zasobu 32 klucz znaku i adres URL punktu końcowego tworzenia. Utwórz za pomocą [Azure Portal](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) lub [interfejsu wiersza polecenia platformy Azure](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli).
* Zaimportuj aplikację [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) z repozytorium GitHub-Services-Language-zrozumieć.
* Identyfikator aplikacji usługi LUIS dla zaimportowanej aplikacji TravelAgent. Identyfikator aplikacji jest wyświetlany na pulpicie nawigacyjnym aplikacji.
* Identyfikator wersji w aplikacji, w której odbierane są wypowiedzi. Domyślny identyfikator to „0.1”.
* [.NET Core 3,1](https://dotnet.microsoft.com/download)
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>Plik JSON z przykładowymi wypowiedziami

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="change-model-programmatically"></a>Programowo Zmień model

1. Utwórz nową aplikację konsolową przeznaczoną dla języka C#, z nazwą projektu i folderu `model-with-rest`.

    ```console
    dotnet new console -lang C# -n model-with-rest
    ```

1. Zainstaluj wymagane zależności z następującymi poleceniami interfejsu wiersza polecenia dotnet.

    ```console
    dotnet add package System.Net.Http
    dotnet add package JsonFormatterPlus
    ```
1. Zastąp plik Program.cs przy użyciu następującego kodu:

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    using System.Linq;

    // 3rd party NuGet packages
    using JsonFormatterPlus;

    namespace AddUtterances
    {
        class Program
        {
            // NOTE: use your LUIS authoring key - 32 character value
            static string authoringKey = "YOUR-KEY";

            // NOTE: Replace this endpoint with your authoring key endpoint
            // for example, your-resource-name.api.cognitive.microsoft.com
            static string endpoint = "YOUR-ENDPOINT";

            // NOTE: Replace this with the ID of your LUIS application
            static string appID = "YOUR-APP-ID";

            // NOTE: Replace this your version number
            static string appVersion = "0.1";

            static string host = String.Format("https://{0}/luis/authoring/v3.0-preview/apps/{1}/versions/{2}/", endpoint, appID, appVersion);

            // GET request with authentication
            async static Task<HttpResponseMessage> SendGet(string uri)
            {
                using (var client = new HttpClient())
                using (var request = new HttpRequestMessage())
                {
                    request.Method = HttpMethod.Get;
                    request.RequestUri = new Uri(uri);
                    request.Headers.Add("Ocp-Apim-Subscription-Key", authoringKey);
                    return await client.SendAsync(request);
                }
            }
            // POST request with authentication
            async static Task<HttpResponseMessage> SendPost(string uri, string requestBody)
            {
                using (var client = new HttpClient())
                using (var request = new HttpRequestMessage())
                {
                    request.Method = HttpMethod.Post;
                    request.RequestUri = new Uri(uri);

                    if (!String.IsNullOrEmpty(requestBody))
                    {
                        request.Content = new StringContent(requestBody, Encoding.UTF8, "text/json");
                    }

                    request.Headers.Add("Ocp-Apim-Subscription-Key", authoringKey);
                    return await client.SendAsync(request);
                }
            }
            // Add utterances as string with POST request
            async static Task AddUtterances(string utterances)
            {
                string uri = host + "examples";

                var response = await SendPost(uri, utterances);
                var result = await response.Content.ReadAsStringAsync();
                Console.WriteLine("Added utterances.");
                Console.WriteLine(JsonFormatter.Format(result));
            }
            // Train app after adding utterances
            async static Task Train()
            {
                string uri = host  + "train";

                var response = await SendPost(uri, null);
                var result = await response.Content.ReadAsStringAsync();
                Console.WriteLine("Sent training request.");
                Console.WriteLine(JsonFormatter.Format(result));
            }
            // Check status of training
            async static Task Status()
            {
                var response = await SendGet(host  + "train");
                var result = await response.Content.ReadAsStringAsync();
                Console.WriteLine("Requested training status.");
                Console.WriteLine(JsonFormatter.Format(result));
            }
            // Add utterances, train, check status
            static void Main(string[] args)
            {
                string utterances = @"
                [
                    {
                    'text': 'go to Seattle today',
                    'intentName': 'BookFlight',
                    'entityLabels': [
                        {
                        'entityName': 'Location::LocationTo',
                        'startCharIndex': 6,
                        'endCharIndex': 12
                        }
                    ]
                    },
                    {
                        'text': 'a barking dog is annoying',
                        'intentName': 'None',
                        'entityLabels': []
                    }
                ]
                ";
                AddUtterances(utterances).Wait();
                Train().Wait();
                Status().Wait();
            }
        }
    }
    ```

1. Zastąp wartości zaczynające `YOUR-` się od własnymi wartościami.

    |Informacje|Przeznaczenie|
    |--|--|
    |`YOUR-KEY`|Klucz tworzenia znaków 32.|
    |`YOUR-ENDPOINT`| Twój punkt końcowy adresu URL tworzenia. Na przykład `replace-with-your-resource-name.api.cognitive.microsoft.com`. Nazwa zasobu jest ustawiana podczas tworzenia zasobu.|
    |`YOUR-APP-ID`| Identyfikator aplikacji LUIS. |

    Przypisane klucze i zasoby są widoczne w portalu LUIS w sekcji Zarządzanie na stronie **zasoby platformy Azure** . Identyfikator aplikacji jest dostępny w tej samej sekcji zarządzania na stronie **Ustawienia aplikacji** .

1. Skompiluj aplikację konsolową.

    ```console
    dotnet build
    ```

1. Uruchom aplikację konsolową. Dane wyjściowe konsoli wyświetlają ten sam kod JSON, który został wyświetlony wcześniej w oknie przeglądarki.

    ```console
    dotnet run
    ```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu pracy z tym przewodnikiem Szybki Start Usuń plik z systemu plików.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Najlepsze rozwiązania dla aplikacji](../luis-concept-best-practices.md)
