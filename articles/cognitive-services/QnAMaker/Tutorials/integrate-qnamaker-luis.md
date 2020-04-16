---
title: LUIS i QnAMaker - integracja botów
titleSuffix: Azure Cognitive Services
description: W miarę powiększania się bazy wiedzy QnA Maker trudno jest utrzymać ją jako pojedynczy zestaw monolityczny. Podziel bazę wiedzy na mniejsze, logiczne fragmenty.
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402720"
---
# <a name="use-a-bot-with-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>Dystrybucja bazy wiedzy za pomocą bota z urządzeniami QnA Maker i LUIS
W miarę powiększania się bazy wiedzy QnA Maker trudno jest utrzymać ją jako pojedynczy zestaw monolityczny. Podziel bazę wiedzy na mniejsze, logiczne fragmenty.

Chociaż tworzenie wielu baz wiedzy w aplikacji QnA Maker jest proste, aby przekierować pytanie przychodzące do odpowiedniej bazy wiedzy. Można to zrobić za pomocą usługi LUIS.

W tym artykule użyto sdk bot framework w wersji 3. Jeśli jesteś zainteresowany wersją tych informacji na zestaw SDK w wersji Bot Framework v4, zobacz [Używanie wielu modeli usługi LUIS i QnA.](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csharp)

## <a name="architecture"></a>Architektura

![Grafika przedstawiająca architekturę programu QnA Maker ze zrozumieniem języka](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

Poprzednia grafika pokazuje, że QnA Maker najpierw pobiera intencji przychodzącego pytania z modelu usługi LUIS. Następnie program QnA Maker używa tej intencji, aby przekierować pytanie do poprawnej bazy wiedzy programu QnA Maker.

## <a name="create-a-luis-app"></a>Tworzenie aplikacji usługi LUIS

1. Zaloguj się do portalu [usługi LUIS.](https://www.luis.ai/)
1. [Tworzenie aplikacji](https://docs.microsoft.com/azure/cognitive-services/luis/create-new-app).
1. [Dodaj intencję](https://docs.microsoft.com/azure/cognitive-services/luis/add-intents) dla każdej bazy wiedzy programu QnA Maker. Wypowiedzi przykład powinny odpowiadać pytania w bazach wiedzy QnA Maker.
1. [Trenuj aplikację usługi LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train) i [publikuj aplikację usługi LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/publishapp).
1. W sekcji **Zarządzanie** zanotuj identyfikator aplikacji usługi LUIS, klucz punktu końcowego usługi LUIS i [niestandardową nazwę domeny](../../cognitive-services-custom-subdomains.md). Te wartości będą potrzebne później.

## <a name="create-qna-maker-knowledge-bases"></a>Tworzenie baz wiedzy programu QnA Maker

1. Zaloguj się do [aplikacji QnA Maker](https://qnamaker.ai).
1. [Utwórz](https://www.qnamaker.ai/Create) bazę wiedzy dla każdej intencji w aplikacji usługi LUIS.
1. Przetestuj i opublikuj bazy wiedzy. Podczas publikowania każdego z nich należy zanotować identyfikator, nazwę zasobu (niestandardową poddomenę przed _azurewebsites.net/qnamaker_) i klucz końcowy autoryzacji. Te wartości będą potrzebne później.

    W tym artykule przyjęto założenie, że bazy wiedzy są tworzone w tej samej subskrypcji programu Azure QnA Maker.

    ![Zrzut ekranu przedstawiający żądanie HTTP programu QnA Maker](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

## <a name="web-app-bot"></a>Bot aplikacji internetowej

1. [Utwórz "Podstawowy" bot z usługą Azure Bot Service](https://docs.microsoft.com/azure/bot-service/bot-service-quickstart?view=azure-bot-service-4.0), która automatycznie zawiera aplikację usługi LUIS. Wybierz język programowania Języka C#.

1. Po utworzeniu bota aplikacji sieci web w witrynie Azure portal wybierz bota aplikacji sieci web.
1. W nawigacji usługi bota aplikacji sieci web wybierz pozycję **Ustawienia aplikacji**. Następnie przewiń w dół do sekcji **Ustawienia aplikacji** dostępnych ustawień.
1. Zmień **LuisAppId** na wartość aplikacji usługi LUIS utworzonej w poprzedniej sekcji. Następnie wybierz pozycję **Zapisz**.


## <a name="change-the-code-in-the-basicluisdialogcs-file"></a>Zmienianie kodu w pliku BasicLuisDialog.cs
1. W sekcji **Zarządzanie botami** nawigacji botów aplikacji sieci web w witrynie Azure portal wybierz pozycję **Buduj**.
2. Wybierz **otwórz edytor kodu online**. Zostanie otwarta nowa karta przeglądarki ze środowiskiem edycji online.
3. W sekcji **WWWROOT** wybierz katalog **Dialogi,** a następnie otwórz **BasicLuisDialog.cs**.
4. Dodaj zależności do górnej części pliku **BasicLuisDialog.cs:**

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

5. Dodaj następujące klasy, aby zdesializować odpowiedź programu QnA Maker:

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


6. Dodaj następującą klasę, aby złożyć żądanie HTTP do usługi QnA Maker. Należy zauważyć, że wartość nagłówka `EndpointKey` **autoryzacji** zawiera wyraz, z spacją po słowie. Wynik JSON jest deserialized do poprzednich klas i zwracana jest pierwsza odpowiedź.

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


7. Zmodyfikuj `BasicLuisDialog` klasę. Każda intencja usługi LUIS powinna mieć metodę ozdobioną **luisintent**. Parametrem do dekoracji jest rzeczywista nazwa intencji usługi LUIS. Nazwa metody, która jest dekorowana _powinna_ być nazwą intencji usługi LUIS dla czytelności i łatwości konserwacji, ale nie musi być taka sama w projekcie lub czasie wykonywania.

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


## <a name="build-the-bot"></a>Zbuduj bota
1. W edytorze kodu kliknij prawym przyciskiem myszy **build.cmd**i wybierz polecenie **Uruchom z konsoli**.

    ![Zrzut ekranu przedstawiający opcję Uruchom z konsoli w edytorze kodu](../media/qnamaker-tutorials-qna-luis/run-from-console.png)

2. Widok kodu jest zastępowany oknem terminala, które pokazuje postęp i wyniki kompilacji.

    ![Zrzut ekranu przedstawiający kompilację konsoli](../media/qnamaker-tutorials-qna-luis/console-build.png)

## <a name="test-the-bot"></a>Przetestuj bota
W witrynie Azure portal wybierz pozycję **Testuj w czacie sieci Web,** aby przetestować bota. Wpisz wiadomości z różnych intencji, aby uzyskać odpowiedź z odpowiedniej bazy wiedzy.

![Zrzut ekranu przedstawiający test czatu internetowego](../media/qnamaker-tutorials-qna-luis/qnamaker-web-chat.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zintegruj swoją bazę wiedzy z agentem w Power Virtual Agents](integrate-with-power-virtual-assistant-fallback-topic.md)
