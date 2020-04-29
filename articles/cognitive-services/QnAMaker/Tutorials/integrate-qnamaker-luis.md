---
title: LUIS i QnAMaker — integracja bot
titleSuffix: Azure Cognitive Services
description: W miarę zwiększania się QnA Maker baza wiedzy jest trudna do utrzymania jej jako jednego zestawu monolitycznego. Podziel bazę wiedzy na mniejsze, logiczne fragmenty.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 09/26/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b0d28c77966668f919cdf1265f8cc63b4931d5fd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81402720"
---
# <a name="use-a-bot-with-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>Rozpowszechnianie bazy wiedzy przy użyciu bot z QnA Maker i LUIS
W miarę zwiększania się QnA Maker baza wiedzy jest trudna do utrzymania jej jako jednego zestawu monolitycznego. Podziel bazę wiedzy na mniejsze, logiczne fragmenty.

Chociaż nie można utworzyć wielu baz wiedzy w QnA Maker, będzie potrzebna logika do kierowania pytania przychodzącego do odpowiedniej bazy wiedzy. Można to zrobić za pomocą LUIS.

W tym artykule jest stosowany zestaw bot Framework v3 SDK. Jeśli interesuje Cię wersję zestawu SDK bot Framework v4, zobacz [Korzystanie z wielu modeli Luis i QNA](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csharp).

## <a name="architecture"></a>Architektura

![Ilustracja przedstawiająca architekturę QnA Maker z Language Understanding](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

Poprzednia ilustracja pokazuje, że QnA Maker najpierw pobiera intencje przychodzącego pytania z modelu LUIS. Następnie QnA Maker używa tego celu do kierowania pytania do odpowiedniej QnA Maker bazie wiedzy.

## <a name="create-a-luis-app"></a>Tworzenie aplikacji usługi LUIS

1. Zaloguj się do portalu [Luis](https://www.luis.ai/) .
1. [Utwórz aplikację](https://docs.microsoft.com/azure/cognitive-services/luis/create-new-app).
1. [Dodaj zamierzenia](https://docs.microsoft.com/azure/cognitive-services/luis/add-intents) dla każdej QNA Maker bazy wiedzy. Przykład wyrażenia długości powinien odpowiadać na pytania w QnA Maker bazach wiedzy.
1. [Nauczenie aplikacji Luis](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train) i [opublikowanie aplikacji Luis](https://docs.microsoft.com/azure/cognitive-services/luis/publishapp).
1. W sekcji **Zarządzanie** Zanotuj identyfikator aplikacji Luis, klucz punktu końcowego Luis i [niestandardową nazwę domeny](../../cognitive-services-custom-subdomains.md). Te wartości będą potrzebne później.

## <a name="create-qna-maker-knowledge-bases"></a>Tworzenie QnA Maker baz wiedzy

1. Zaloguj się do [QNA Maker](https://qnamaker.ai).
1. [Utwórz](https://www.qnamaker.ai/Create) bazę wiedzy dla każdego zamiaru w aplikacji Luis.
1. Przetestuj i Opublikuj bazy wiedzy. Po opublikowaniu każdego z nich należy zanotować identyfikator, nazwę zasobu (niestandardową poddomenę przed _. azurewebsites.NET/qnamaker_) i klucz punktu końcowego autoryzacji. Te wartości będą potrzebne później.

    W tym artykule założono, że wszystkie bazy wiedzy zostały utworzone w tej samej subskrypcji platformy Azure QnA Maker.

    ![Zrzut ekranu przedstawiający QnA Maker żądanie HTTP](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

## <a name="web-app-bot"></a>Bot aplikacji internetowej

1. [Utwórz bot "Basic" z Azure bot Service](https://docs.microsoft.com/azure/bot-service/bot-service-quickstart?view=azure-bot-service-4.0), który automatycznie zawiera aplikację Luis. Wybierz język programowania w języku C#.

1. Po utworzeniu aplikacji sieci Web bot w Azure Portal wybierz aplikację sieci Web bot.
1. W obszarze Nawigacja usługi bot aplikacji sieci Web wybierz pozycję **Ustawienia aplikacji**. Następnie przewiń w dół do sekcji **Ustawienia aplikacji** dostępne ustawienia.
1. Zmień **LuisAppId** na wartość aplikacji Luis utworzonej w poprzedniej sekcji. Następnie wybierz pozycję **Zapisz**.


## <a name="change-the-code-in-the-basicluisdialogcs-file"></a>Zmień kod w pliku BasicLuisDialog.cs
1. W sekcji **bot Management** w obszarze Nawigacja aplikacji sieci Web bot w obszarze Azure Portal wybierz opcję **Kompiluj**.
2. Wybierz pozycję **Otwórz Edytor kodu online**. Zostanie otwarta nowa karta przeglądarki ze środowiskiem edycji online.
3. W sekcji **WWWROOT** wybierz katalog **okna dialogowe** , a następnie otwórz **BasicLuisDialog.cs**.
4. Dodaj zależności na początku pliku **BasicLuisDialog.cs** :

    ```csharp
    using System;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Bot.Builder.Luis;
    using Microsoft.Bot.Builder.Luis.Models;
    using Newtonsoft.Json;
    using System.Text;
    ```

5. Dodaj następujące klasy, aby deserializować QnA Maker odpowiedzi:

    ```csharp
    public class Metadata
    {
        public string name { get; set; }
        public string value { get; set; }
    }

    public class Answer
    {
        public IList<string> questions { get; set; }
        public string answer { get; set; }
        public double score { get; set; }
        public int id { get; set; }
        public string source { get; set; }
        public IList<object> keywords { get; set; }
        public IList<Metadata> metadata { get; set; }
    }

    public class QnAAnswer
    {
        public IList<Answer> answers { get; set; }
    }
    ```


6. Dodaj następującą klasę, aby wykonać żądanie HTTP do usługi QnA Maker. Zwróć uwagę, że wartość nagłówka **autoryzacji** obejmuje wyraz, `EndpointKey`, z odstępem po słowie. Wynik JSON jest deserializowany do poprzednich klas i zwracana jest pierwsza odpowiedź.

    ```csharp
    [Serializable]
    public class QnAMakerService
    {
        private string qnaServiceResourceName;
        private string knowledgeBaseId;
        private string endpointKey;

        public QnAMakerService(string resourceName, string kbId, string endpointkey)
        {
            qnaServiceResourceName = resourceName;
            knowledgeBaseId = kbId;
            endpointKey = endpointkey;

        }
        async Task<string> Post(string uri, string body)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(body, Encoding.UTF8, "application/json");
                request.Headers.Add("Authorization", "EndpointKey " + endpointKey);

                var response = await client.SendAsync(request);
                return  await response.Content.ReadAsStringAsync();
            }
        }
        public async Task<string> GetAnswer(string question)
        {
            string uri = qnaServiceResourceName + "/qnamaker/knowledgebases/" + knowledgeBaseId + "/generateAnswer";
            string questionJSON = "{\"question\": \"" + question.Replace("\"","'") +  "\"}";

            var response = await Post(uri, questionJSON);

            var answers = JsonConvert.DeserializeObject<QnAAnswer>(response);
            if (answers.answers.Count > 0)
            {
                return answers.answers[0].answer;
            }
            else
            {
                return "No good match found.";
            }
        }
    }
    ```


7. Zmodyfikuj `BasicLuisDialog` klasę. Każdy zamiar LUIS powinien mieć metodę z **LuisIntent**. Parametr do dekoracji jest rzeczywistą nazwą celu LUIS. Nazwa metody, która jest dekoracyjna, _powinna_ być nazwą Luis intencji dla czytelności i utrzymania, ale nie musi być taka sama w projekcie ani w środowisku uruchomieniowym.

    ```csharp
    [Serializable]
    public class BasicLuisDialog : LuisDialog<object>
    {
        // LUIS Settings
        static string LUIS_appId = "<LUIS APP ID>";
        static string LUIS_apiKey = "<LUIS API KEY>";
        static string LUIS_hostRegion = "westus.api.cognitive.microsoft.com";

        // QnA Maker global settings
        // assumes all knowledge bases are created with same Azure service
        static string qnamaker_endpointKey = "<QnA Maker endpoint KEY>";
        static string qnamaker_resourceName = "my-qnamaker-s0-s";

        // QnA Maker Human Resources Knowledge base
        static string HR_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // QnA Maker Finance Knowledge base
        static string Finance_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // Instantiate the knowledge bases
        public QnAMakerService hrQnAService = new QnAMakerService("https://" + qnamaker_resourceName + ".azurewebsites.net", HR_kbID, qnamaker_endpointKey);
        public QnAMakerService financeQnAService = new QnAMakerService("https://" + qnamaker_resourceName + ".azurewebsites.net", Finance_kbID, qnamaker_endpointKey);

        public BasicLuisDialog() : base(new LuisService(new LuisModelAttribute(
            LUIS_appId,
            LUIS_apiKey,
            domain: LUIS_hostRegion)))
        {
        }

        [LuisIntent("None")]
        public async Task NoneIntent(IDialogContext context, LuisResult result)
        {
            HttpClient client = new HttpClient();
            await this.ShowLuisResult(context, result);
        }

        // HR Intent
        [LuisIntent("HR")]
        public async Task HumanResourcesIntent(IDialogContext context, LuisResult result)
        {
            // Ask the HR knowledge base
            var qnaMakerAnswer = await hrQnAService.GetAnswer(result.Query);
            await context.PostAsync($"{qnaMakerAnswer}");
            context.Wait(MessageReceived);
        }

        // Finance intent
        [LuisIntent("Finance")]
        public async Task FinanceIntent(IDialogContext context, LuisResult result)
        {
            // Ask the finance knowledge base
            var qnaMakerAnswer = await financeQnAService.GetAnswer(result.Query);
            await context.PostAsync($"{qnaMakerAnswer}");
            context.Wait(MessageReceived);
        }
        private async Task ShowLuisResult(IDialogContext context, LuisResult result)
        {
            await context.PostAsync($"You have reached {result.Intents[0].Intent}. You said: {result.Query}");
            context.Wait(MessageReceived);
        }
    }
    ```


## <a name="build-the-bot"></a>Kompiluj bot
1. W edytorze kodu kliknij prawym przyciskiem myszy pozycję **Build. cmd**, a następnie wybierz polecenie **Uruchom z konsoli**.

    ![Zrzut ekranu przedstawiający opcję Uruchom z konsoli w edytorze kodu](../media/qnamaker-tutorials-qna-luis/run-from-console.png)

2. Widok kodu jest zastępowany oknem terminalu, który pokazuje postęp i wyniki kompilacji.

    ![Zrzut ekranu przedstawiający kompilację konsoli](../media/qnamaker-tutorials-qna-luis/console-build.png)

## <a name="test-the-bot"></a>Testowanie bot
W Azure Portal wybierz pozycję **Testuj w rozmowie w sieci Web** , aby przetestować bot. Wpisywanie komunikatów z różnych intencji w celu uzyskania odpowiedzi z odpowiedniej bazy wiedzy.

![Zrzut ekranu testu rozmowy w sieci Web](../media/qnamaker-tutorials-qna-luis/qnamaker-web-chat.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Integrowanie bazy wiedzy z agentem w usłudze Virtual Machine Agents](integrate-with-power-virtual-assistant-fallback-topic.md)
