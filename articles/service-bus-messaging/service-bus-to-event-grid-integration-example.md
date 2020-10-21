---
title: 'Samouczek: przykłady dotyczące Event Grid integracji Azure Service Bus'
description: 'Samouczek: w tym artykule przedstawiono przykłady Service Bus obsługi komunikatów i Event Grid integracji.'
documentationcenter: .net
author: spelluru
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-csharp
ms.openlocfilehash: c7f177b8738fdaf8d0975877d9af094b6f0a49a9
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92328151"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-functions-and-azure-logic-apps"></a>Samouczek: reagowanie na zdarzenia Azure Service Bus odbierane za pośrednictwem Azure Event Grid przy użyciu Azure Functions i Azure Logic Apps
W tym samouczku dowiesz się, jak odpowiedzieć na zdarzenia Azure Service Bus, które są odbierane za pośrednictwem Azure Event Grid przy użyciu Azure Functions i Azure Logic Apps. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie przestrzeni nazw usługi Service Bus
> * Przygotowanie przykładowej aplikacji do wysyłania wiadomości
> * Wysyłanie komunikatów do tematu Service Bus
> * Odbieranie komunikatów za pomocą usługi Logic Apps
> * Skonfiguruj funkcję testową na platformie Azure
> * Łączenie funkcji i przestrzeni nazw za pomocą usługi Event Grid
> * Odbieranie komunikatów przy użyciu usługi Azure Functions

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć kroki tego samouczka, upewnij się, że zainstalowano następujące elementy:

- [Program Visual Studio 2017 Update 3 (wersja 15.3, 26730.01)](https://www.visualstudio.com/vs) lub nowszy.
- [Zestaw NET Core SDK](https://www.microsoft.com/net/download/windows), wersja 2.0 lub nowsza.

## <a name="create-a-service-bus-namespace"></a>Tworzenie przestrzeni nazw usługi Service Bus
Postępuj zgodnie z instrukcjami w tym samouczku: [Szybki Start: użyj Azure Portal, aby utworzyć temat Service Bus i subskrypcje w temacie](service-bus-quickstart-topics-subscriptions-portal.md) , aby wykonać następujące zadania:

- Utwórz przestrzeń nazw Service Bus w **warstwie Premium** . 
- Pobierz parametry połączenia. 
- Utwórz temat Service Bus.
- Utwórz dwie subskrypcje w temacie. 

## <a name="prepare-a-sample-application-to-send-messages"></a>Przygotowanie przykładowej aplikacji do wysyłania wiadomości
Komunikat można wysłać do tematu usługi Service Bus za pomocą dowolnej metody. Przykładowy kod na końcu tej procedury zakłada, że używasz programu Visual Studio 2017.

1. Sklonuj [repozytorium azure-service-bus z usługi GitHub](https://github.com/Azure/azure-service-bus/).
2. W programie Visual Studio przejdź do folderu *\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration* i otwórz plik *SBEventGridIntegration.sln*.
3. Przejdź do projektu **MessageSender** i wybierz pozycję **Program.cs**.
4. Podaj nazwę tematu Service Bus i parametry połączenia, które zostały uzyskane z poprzedniego kroku:

    ```csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    const string TopicName = "YOUR TOPIC NAME";
    ```
5. Zaktualizuj `numberOfMessages` wartość do **5**. 
5. Skompiluj i uruchom program, aby wysyłać wiadomości testowe do tematu Service Bus. 

## <a name="receive-messages-by-using-logic-apps"></a>Odbieranie komunikatów za pomocą usługi Logic Apps
Połącz aplikację logiki z Azure Service Bus i Azure Event Grid, wykonując następujące czynności:

1. Utwórz aplikację logiki w Azure Portal.
    1. Wybierz pozycję **+ Utwórz zasób**, wybierz pozycję **integracja**, a następnie wybierz pozycję **aplikacja logiki**. 
    2. Na stronie **aplikacja logiki — tworzenie** wprowadź **nazwę** aplikacji logiki.
    3. Wybierz swoją **subskrypcję**platformy Azure. 
    4. Wybierz pozycję **Użyj istniejącej** dla **grupy zasobów**, a następnie wybierz grupę zasobów, która została użyta do innych zasobów (takich jak funkcja platformy Azure, Service Bus przestrzeń nazw), która została utworzona wcześniej. 
    5. Wybierz **lokalizację** aplikacji logiki. 
    6. Wybierz pozycję **Utwórz** , aby utworzyć aplikację logiki. 
2. Na stronie **projektant Logic Apps** wybierz pozycję **pusta aplikacja logiki** w obszarze **Szablony**. 
3. W projektancie wykonaj następujące czynności:
    1. Wyszukaj **Event Grid**. 
    2. Wybierz **, kiedy wystąpi zdarzenie zasobu — Azure Event Grid**. 

        ![Projektant Logic Apps — wybierz wyzwalacz Event Grid](./media/service-bus-to-event-grid-integration-example/logic-apps-event-grid-trigger.png)
4. Wybierz pozycję **Zaloguj**i wprowadź swoje poświadczenia platformy Azure, a następnie wybierz pozycję **Zezwalaj na dostęp**. 
5. Na stronie **gdy wystąpi zdarzenie zasobu** wykonaj następujące czynności:
    1. Wybierz swoją subskrypcję platformy Azure. 
    2. W obszarze **Typ zasobu**wybierz pozycję **Microsoft. ServiceBus. Namespaces**. 
    3. W obszarze **nazwa zasobu**wybierz przestrzeń nazw Service Bus. 
    4. Wybierz pozycję **Dodaj nowy parametr**, a następnie wybierz pozycję **Filtr sufiksów**. 
    5. W polu **Filtr sufiksu**wprowadź nazwę drugiej subskrypcji tematu Service Bus. 
        ![Projektant Logic Apps — Konfigurowanie zdarzenia](./media/service-bus-to-event-grid-integration-example/logic-app-configure-event.png)
6. Wybierz pozycję **+ nowy krok** w projektancie, a następnie wykonaj następujące czynności:
    1. Wyszukaj **Service Bus**.
    2. Na liście wybierz pozycję **Service Bus** . 
    3. Wybierz pozycję dla opcji **Pobierz komunikaty** na liście **Akcje** . 
    4. Wybierz pozycję **Pobierz komunikaty z subskrypcji tematu (Zablokuj)**. 

        ![Logic Apps Designer — pobieranie komunikatów](./media/service-bus-to-event-grid-integration-example/service-bus-get-messages-step.png)
    5. Wprowadź **nazwę połączenia**. Na przykład: **Pobierz komunikaty z subskrypcji tematu**i wybierz Service Bus przestrzeni nazw. 

        ![Projektant Logic Apps — wybierz Service Bus przestrzeń nazw](./media/service-bus-to-event-grid-integration-example/logic-apps-select-namespace.png) 
    6. Wybierz pozycję **RootManageSharedAccessKey**, a następnie wybierz pozycję **Utwórz**.

        ![Projektant Logic Apps — wybierz klucz dostępu współdzielonego](./media/service-bus-to-event-grid-integration-example/logic-app-shared-access-key.png) 
    8. Wybierz swój **temat** i **subskrypcję**. 
    
        ![Zrzut ekranu pokazujący, gdzie wybierasz swój temat i subskrypcję.](./media/service-bus-to-event-grid-integration-example/logic-app-select-topic-subscription.png)
7. Wybierz pozycję **+ nowy krok**i wykonaj następujące czynności: 
    1. Wybierz pozycję **Service Bus**.
    2. Wybierz pozycję **Zakończ wiadomość w subskrypcji tematu** z listy akcji. 
    3. Wybierz **temat**Service Bus.
    4. Wybierz drugą **subskrypcję** w temacie.
    5. W **polu token blokady wiadomości**wybierz opcję **Zablokuj token** z **zawartości dynamicznej**. 

        ![Projektant Logic Apps — wybierz Service Bus temat i subskrypcję](./media/service-bus-to-event-grid-integration-example/logic-app-complete-message.png)
8. Wybierz pozycję **Zapisz** na pasku narzędzi w projektancie Logic Apps, aby zapisać aplikację logiki. 
9. Postępuj zgodnie z instrukcjami w sekcji [wysyłanie komunikatów do Service Bus tematu,](#send-messages-to-the-service-bus-topic) aby wysyłać komunikaty do tematu. 
10. Przejdź do strony **Przegląd** aplikacji logiki. Zostanie wyświetlona aplikacja logiki uruchamiana w **historii uruchamiania** dla wysłanych komunikatów.

    ![Logic Apps Designer — uruchomienia aplikacji logiki](./media/service-bus-to-event-grid-integration-example/logic-app-runs.png)

## <a name="set-up-a-test-function-on-azure"></a>Skonfiguruj funkcję testową na platformie Azure 
Przed rozpoczęciem pracy w całym scenariuszu należy skonfigurować co najmniej małą funkcję testową, której można użyć do debugowania i obserwować zdarzenia, które są przepływami. Postępuj zgodnie z instrukcjami w artykule [Tworzenie pierwszej funkcji w Azure Portal artykułu,](../azure-functions/functions-create-first-azure-function.md) aby wykonać następujące zadania: 

1. Utwórz aplikację funkcji.
2. Utwórz funkcję wyzwalaną przez protokół HTTP. 

Następnie wykonaj następujące czynności: 


# <a name="azure-functions-v2"></a>[Azure Functions v2](#tab/v2)

1. Rozwiń pozycję **funkcje** w widoku drzewa, a następnie wybierz funkcję. Zastąp kod dla funkcji następującym kodem: 

    ```csharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");
        var content = req.Body;
        string jsonContent = await new StreamReader(content).ReadToEndAsync();
        log.LogInformation($"Received Event with payload: {jsonContent}");
    
        IEnumerable<string> headerValues;
        headerValues = req.Headers.GetCommaSeparatedValues("Aeg-Event-Type");
    
        if (headerValues.Count() != 0)
        {
            var validationHeaderValue = headerValues.FirstOrDefault();
            if(validationHeaderValue == "SubscriptionValidation")
            {
                log.LogInformation("Validating the subscription");            
                var events = JsonConvert.DeserializeObject<GridEvent[]>(jsonContent);
                var code = events[0].Data["validationCode"];
                log.LogInformation($"Validation code: {code}");
                return (ActionResult) new OkObjectResult(new { validationResponse = code });
            }
        }
    
        return jsonContent == null
            ? new BadRequestObjectResult("Please pass a name on the query string or in the request body")
            : (ActionResult)new OkObjectResult($"Hello, {jsonContent}");
    }
    
    public class GridEvent
    {
        public string Id { get; set; }
        public string EventType { get; set; }
        public string Subject { get; set; }
        public DateTime EventTime { get; set; }
        public Dictionary<string, string> Data { get; set; }
        public string Topic { get; set; }
    }    
    ```
2. Wybierz pozycję **Zapisz** na pasku narzędzi, aby zapisać kod dla funkcji.

    ![Zapisz kod funkcji](./media/service-bus-to-event-grid-integration-example/save-function-code.png)
3. Na pasku narzędzi wybierz pozycję **test/Uruchom** , a następnie wykonaj następujące czynności: 
    1. Wprowadź Poniższy kod JSON w **treści**.

        ```json
        [{
          "id": "64ba80ae-9f8e-425f-8bd7-d88d2c0ba3e3",
          "topic": "/subscriptions/0000000000-0000-0000-0000-0000000000000/resourceGroups/spegridsbusrg/providers/Microsoft.ServiceBus/namespaces/spegridsbusns",
          "subject": "",
          "data": {
            "validationCode": "D7D825D4-BD04-4F73-BDE3-70666B149857",
            "validationUrl": "https://rp-eastus.eventgrid.azure.net:553/eventsubscriptions/spsbusegridsubscription/validate?id=D7D825D4-BD04-4F73-BDE3-70666B149857&t=2020-06-09T18:28:51.5724615Z&apiVersion=2020-04-01-preview&[Hidden Credential]"
          },
          "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
          "eventTime": "2020-06-09T18:28:51.5724615Z",
          "metadataVersion": "1",
          "dataVersion": "2"
        }]
        ```    
    2. Kliknij pozycję **Dodaj nagłówek**i Dodaj nagłówek o nazwie `aeg-event-type` i wartości `SubscriptionValidation` . 
    3. Wybierz pozycję **Uruchom**. 

        ![Przebieg testowy](./media/service-bus-to-event-grid-integration-example/test-run-function.png)
    4. Upewnij się, że w treści odpowiedzi jest wyświetlany kod stanu powrotu **OK** i kod sprawdzania poprawności. Zobacz również informacje rejestrowane przez funkcję. 

        ![Przebieg testowy — odpowiedź](./media/service-bus-to-event-grid-integration-example/test-function-response.png)        
3. Wybierz pozycję **Pobierz adres URL funkcji** i ZANOTUJ adres URL. 

    ![Pobierz adres URL funkcji](./media/service-bus-to-event-grid-integration-example/get-function-url.png)
5. Wybierz przycisk **Kopiuj** obok tekstu adresu URL.    
    ![Kopiuj adres URL funkcji](./media/service-bus-to-event-grid-integration-example/get-function-url-copy.png)

# <a name="azure-functions-v1"></a>[Azure Functions v1](#tab/v1)

1. Skonfiguruj funkcję do korzystania z wersji **V1** : 
    1. Wybierz aplikację funkcji w widoku drzewa, a następnie wybierz pozycję **Ustawienia aplikacji funkcji**. 
    2. Wybierz pozycję **~ 1** dla **wersji środowiska uruchomieniowego**. 
2. Rozwiń pozycję **funkcje** w widoku drzewa, a następnie wybierz funkcję. Zastąp kod dla funkcji następującym kodem: 

    ```csharp
    #r "Newtonsoft.Json"
    using System.Net;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        log.Info("C# HTTP trigger function processed a request.");
        // parse query parameter
        var content = req.Content;
    
        string jsonContent = await content.ReadAsStringAsync(); 
        log.Info($"Received Event with payload: {jsonContent}");
    
        IEnumerable<string> headerValues;
        if (req.Headers.TryGetValues("Aeg-Event-Type", out headerValues))
        {
            var validationHeaderValue = headerValues.FirstOrDefault();
            if(validationHeaderValue == "SubscriptionValidation")
            {
            var events = JsonConvert.DeserializeObject<GridEvent[]>(jsonContent);
                 var code = events[0].Data["validationCode"];
                 return req.CreateResponse(HttpStatusCode.OK,
                 new { validationResponse = code });
            }
        }
    
        return jsonContent == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + jsonContent);
    }
    
    public class GridEvent
    {
        public string Id { get; set; }
        public string EventType { get; set; }
        public string Subject { get; set; }
        public DateTime EventTime { get; set; }
        public Dictionary<string, string> Data { get; set; }
        public string Topic { get; set; }
    }
    ```
4. Wybierz polecenie **Zapisz i uruchom**.

    ![Dane wyjściowe aplikacji funkcji](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
4. Na pasku narzędzi wybierz pozycję **Pobierz adres URL funkcji** . 

    ![Pobierz adres URL funkcji](./media/service-bus-to-event-grid-integration-example/get-function-url.png)
5. Wybierz przycisk **Kopiuj** obok tekstu adresu URL.    
    ![Kopiuj adres URL funkcji](./media/service-bus-to-event-grid-integration-example/get-function-url-copy.png)

---

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Łączenie funkcji i przestrzeni nazw za pomocą usługi Event Grid
W tej sekcji należy powiązać funkcję z przestrzenią nazw Service Bus przy użyciu Azure Portal. 

Aby utworzyć subskrypcję Azure Event Grid, wykonaj następujące kroki:

1. W Azure Portal przejdź do przestrzeni nazw, a następnie w okienku po lewej stronie wybierz pozycję **zdarzenia**. Zostanie otwarte okno przestrzeni nazw z dwiema subskrypcjami usługi Event Grid wyświetlonymi w okienku po prawej stronie. 
    
    ![Strona Service Bus — zdarzenia](./media/service-bus-to-event-grid-integration-example/service-bus-events-page.png)
2. Wybierz pozycję **+ subskrypcja zdarzeń** na pasku narzędzi. 
3. Na stronie **Tworzenie subskrypcji zdarzeń** wykonaj następujące czynności:
    1. Wprowadź **nazwę** subskrypcji. 
    2. Wprowadź **nazwę** **tematu systemowego**. Tematy systemowe są tematami utworzonymi dla zasobów platformy Azure, takich jak konto usługi Azure Storage i Azure Service Bus. Aby dowiedzieć się więcej na temat tematów systemowych, zobacz [Tematy systemowe — Omówienie](../event-grid/system-topics.md).
    2. Wybierz **element Hook sieci Web** dla **typu punktu końcowego**. 

        ![Subskrypcja Event Grid Service Bus](./media/service-bus-to-event-grid-integration-example/event-grid-subscription-page.png)
    3. Wybierz **pozycję Wybierz punkt końcowy**, wklej adres URL funkcji, a następnie wybierz pozycję **Potwierdź wybór**. 

        ![Funkcja — Wybieranie punktu końcowego](./media/service-bus-to-event-grid-integration-example/function-select-endpoint.png)
    4. Przejdź do karty **filtry** i wykonaj następujące zadania:
        1. Wybierz pozycję **Włącz filtrowanie podmiotu**
        2. Wprowadź nazwę **pierwszej subskrypcji** do utworzonego wcześniej tematu Service Bus.
        3. Wybierz przycisk **Utwórz**. 

            ![Filtr subskrypcji zdarzeń](./media/service-bus-to-event-grid-integration-example/event-subscription-filter.png)
4. Przejdź do karty **subskrypcje zdarzeń** na stronie **zdarzenia** i sprawdź, czy na liście znajduje się subskrypcja zdarzeń.

    ![Subskrypcja zdarzeń na liście](./media/service-bus-to-event-grid-integration-example/event-subscription-in-list.png)

## <a name="send-messages-to-the-service-bus-topic"></a>Wysyłanie komunikatów do tematu Service Bus
1. Uruchom aplikację .NET C#, która wysyła komunikaty do tematu Service Bus. 

    ![Dane wyjściowe aplikacji konsoli](./media/service-bus-to-event-grid-integration-example/console-app-output.png)
1. Na stronie aplikacji funkcji platformy Azure przejdź do karty **monitorowanie** na karcie **kod + testowanie** . Powinien zostać wyświetlony wpis dla każdej wiadomości ogłoszonej w temacie Service Bus. Jeśli ich nie widzisz, Odśwież stronę po odczekaniu przez kilka minut. 

    ![Funkcja monitorowania](./media/service-bus-to-event-grid-integration-example/function-monitor.png)

    Możesz również użyć karty **dzienniki** na stronie **Monitoruj** , aby wyświetlić informacje o rejestrowaniu w miarę wysyłania komunikatów. Może to opóźnić kilka minut, aby wyświetlić zarejestrowane komunikaty. 

## <a name="receive-messages-by-using-azure-functions"></a>Odbieranie komunikatów przy użyciu usługi Azure Functions
W poprzedniej sekcji przeprowadzono prosty scenariusz testowania i debugowania oraz sprawdzono, czy zdarzenia przepływają. 

W tej sekcji dowiesz się, jak odbierać i przetwarzać komunikaty po odebraniu zdarzenia.

### <a name="publish-a-function-from-visual-studio"></a>Publikowanie funkcji z programu Visual Studio
1. W tym samym otwartym rozwiązaniu programu Visual Studio (**SBEventGridIntegration**) wybierz pozycję **ReceiveMessagesOnEvent.cs** w projekcie **SBEventGridIntegration** . 
2. Wprowadź parametry połączenia Service Bus w następującym kodzie:

    ```Csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    ```
3. Pobierz **profil publikowania** dla funkcji:
    1. Wybierz aplikację funkcji. 
    2. Wybierz kartę **Przegląd** , jeśli nie została jeszcze wybrana. 
    3. Na pasku narzędzi wybierz pozycję **Pobierz profil publikowania** . 

        ![Pobierz profil publikowania dla funkcji](./media/service-bus-to-event-grid-integration-example/function-download-publish-profile.png)
    4. Zapisz plik w folderze projektu. 
4. W programie Visual Studio kliknij prawym przyciskiem myszy element **SBEventGridIntegration** i wybierz pozycję **Publikuj**. 
5. Na stronie **Publikowanie**wykonaj następujące czynności: 
    1. Wybierz pozycję **Rozpocznij** na stronie **Publikowanie** 
    2. Dla **elementu docelowego**wybierz pozycję **Importuj profil**. 
    3. Wybierz pozycję **Dalej**. 

        ![Visual Studio — przycisk importowania profilu](./media/service-bus-to-event-grid-integration-example/visual-studio-import-profile-button.png)
7. Wybierz pobrany wcześniej **plik profilu publikowania** i wybierz pozycję **Zakończ**.

    ![Wybierz profil publikowania](./media/service-bus-to-event-grid-integration-example/select-publish-profile.png)
8. Na stronie **Publikowanie** wybierz pozycję **Publikuj** . 

    ![Visual Studio — publikowanie](./media/service-bus-to-event-grid-integration-example/select-publish.png)
9. Potwierdź, że zobaczysz nową funkcję Azure Function **ReceiveMessagesOnEvent**. W razie konieczności Odśwież stronę. 

    ![Upewnij się, że nowa funkcja została utworzona](./media/service-bus-to-event-grid-integration-example/function-receive-messages.png)
10. Pobierz adres URL do nowej funkcji i zanotuj go. 

### <a name="event-grid-subscription"></a>Subskrypcja Event Grid

1. Usuń istniejącą subskrypcję Event Grid:
    1. Na stronie **obszar nazw Service Bus** wybierz pozycję **zdarzenia** w menu po lewej stronie. 
    2. Przejdź do karty **subskrypcje zdarzeń** . 
    2. Wybierz istniejącą subskrypcję zdarzeń. 

        ![Wybierz subskrypcję zdarzeń](./media/service-bus-to-event-grid-integration-example/select-event-subscription.png)
    3. Na stronie **subskrypcja zdarzeń** wybierz pozycję **Usuń**. Wybierz pozycję **tak** , aby potwierdzić usunięcie. 
        ![Przycisk usuwania subskrypcji zdarzeń](./media/service-bus-to-event-grid-integration-example/delete-subscription-button.png)
2. Postępuj zgodnie z instrukcjami w sekcji [łączenie funkcji i przestrzeni nazw za pośrednictwem Event Grid](#connect-the-function-and-namespace-via-event-grid) , aby utworzyć subskrypcję Event Grid przy użyciu nowego adresu URL funkcji.
3. Postępuj zgodnie z instrukcjami w sekcji [wysyłanie komunikatów do Service Bus tematu](#send-messages-to-the-service-bus-topic) , aby wysyłać komunikaty do tematu i monitorować funkcję. 


## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat usługi [Azure Event Grid](../event-grid/index.yml).
* Dowiedz się więcej na temat usługi [Azure Functions](../azure-functions/index.yml).
* Dowiedz się więcej o [funkcji Logic Apps usługi Azure App Service](../logic-apps/index.yml).
* Dowiedz się więcej na temat usługi [Azure Service Bus](/azure/service-bus/).


[2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid2.png
[3]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid3.png
[7]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid7.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[10]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid10.png
[11]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid11.png
[12]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12.png
[12-1]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-1.png
[12-2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-2.png
[13]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid13.png
[14]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid14.png
[15]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid15.png
[16]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid16.png
[17]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid17.png
[18]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid18.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png