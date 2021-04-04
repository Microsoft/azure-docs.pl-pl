---
title: Odbieraj zdarzenia z Azure Event Grid do punktu końcowego HTTP
description: Opisuje, jak sprawdzać poprawność punktu końcowego HTTP, a następnie odbierać i deserializować zdarzenia z Azure Event Grid
ms.topic: conceptual
ms.date: 11/19/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 75c80fb85d39298f1130537971bc700897c039d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96023729"
---
# <a name="receive-events-to-an-http-endpoint"></a>Odbieranie zdarzeń w punkcie końcowym HTTP

W tym artykule opisano sposób [sprawdzania poprawności punktu końcowego http](webhook-event-delivery.md) w celu odbierania zdarzeń z subskrypcji zdarzeń, a następnie odbierania i deserializacji zdarzeń. W tym artykule jest wykorzystywana funkcja platformy Azure w celach demonstracyjnych, jednak te same koncepcje są stosowane niezależnie od lokalizacji, w której aplikacja jest hostowana.

> [!NOTE]
> **Zdecydowanie** zaleca się użycie [wyzwalacza Event Grid](../azure-functions/functions-bindings-event-grid.md) podczas wyzwalania funkcji platformy Azure z Event Grid. Korzystanie z ogólnego wyzwalacza elementu webhook znajduje się tutaj.

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebna jest aplikacja funkcji z funkcją wyzwalaną przez protokół HTTP.

## <a name="add-dependencies"></a>Dodaj zależności

W przypadku tworzenia aplikacji w programie .NET należy [dodać zależność](../azure-functions/functions-reference-csharp.md#referencing-custom-assemblies) do funkcji dla `Microsoft.Azure.EventGrid` [pakietu NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventGrid). Przykłady w tym artykule wymagają wersji 1.4.0 lub nowszej.

Zestawy SDK dla innych języków są dostępne za pośrednictwem referencyjnego [publikowania zestawów SDK](./sdk-overview.md#data-plane-sdks) . Te pakiety mają modele dla natywnych typów zdarzeń, takich jak `EventGridEvent` , `StorageBlobCreatedEventData` , i `EventHubCaptureFileCreatedEventData` .

Kliknij link "Wyświetl pliki" w funkcji platformy Azure (z prawej strony w portalu usługi Azure Functions) i Utwórz plik o nazwie project.json. Dodaj następującą zawartość do `project.json` pliku i Zapisz go:

 ```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.Azure.EventGrid": "2.0.0"
      }
    }
   }
}
```

![Dodano pakiet NuGet](./media/receive-events/add-dependencies.png)

## <a name="endpoint-validation"></a>Walidacja punktu końcowego

W pierwszej kolejności są obsługiwane `Microsoft.EventGrid.SubscriptionValidationEvent` zdarzenia. Za każdym razem, gdy ktoś subskrybuje zdarzenie, Event Grid wysyła zdarzenie weryfikacji do punktu końcowego z `validationCode` ładunkiem danych. Punkt końcowy jest wymagany do wyechania z powrotem w treści odpowiedzi, aby [potwierdzić, że punkt końcowy jest prawidłowy i należy do użytkownika](webhook-event-delivery.md). Jeśli używasz [wyzwalacza Event Grid](../azure-functions/functions-bindings-event-grid.md) zamiast funkcji wyzwalanej przez element webhook, sprawdzanie poprawności punktu końcowego jest obsługiwane. Jeśli używasz usługi API innej firmy (na przykład [zapier](https://zapier.com/home) lub [IFTTT](https://ifttt.com/)), możesz nie być w stanie programowo echo kodu weryfikacyjnego. W przypadku tych usług można ręcznie zweryfikować subskrypcję przy użyciu adresu URL walidacji, który jest wysyłany w ramach zdarzenia walidacji subskrypcji. Skopiuj ten adres URL we `validationUrl` właściwości i Wyślij żądanie Get za pomocą klienta REST lub przeglądarki sieci Web.

W języku C# `DeserializeEventGridEvents()` Funkcja deserializacji zdarzeń Event Grid. Deserializacji dane zdarzenia do odpowiedniego typu, takiego jak StorageBlobCreatedEventData. Użyj `Microsoft.Azure.EventGrid.EventTypes` klasy, aby pobrać obsługiwane typy i nazwy zdarzeń.

Aby programowo ECHA kod sprawdzania poprawności, użyj następującego kodu. Pokrewne przykłady można znaleźć na [przykład Event Grid konsumenta](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/tree/master/EventGridConsumer).

```csharp
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.EventGrid;
namespace Function1
{
    public static class Function1
    {
        [FunctionName("Function1")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            string response = string.Empty;
            string requestContent = await new StreamReader(req.Body).ReadToEndAsync();
            log.LogInformation($"Received events: {requestContent}");

            EventGridSubscriber eventGridSubscriber = new EventGridSubscriber();

            EventGridEvent[] eventGridEvents = eventGridSubscriber.DeserializeEventGridEvents(requestContent);

            foreach (EventGridEvent eventGridEvent in eventGridEvents)
            {
                if (eventGridEvent.Data is SubscriptionValidationEventData)
                {
                    var eventData = (SubscriptionValidationEventData)eventGridEvent.Data;
                    log.LogInformation($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
                    // Do any additional validation (as required) and then return back the below response

                    var responseData = new SubscriptionValidationResponse()
                    {
                        ValidationResponse = eventData.ValidationCode
                    };
                    return new OkObjectResult(responseData);
                }
            }
            return new OkObjectResult(response);
        }
   }
}
```

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }
    }
    context.done();
};
```

### <a name="test-validation-response"></a>Test odpowiedzi na weryfikację

Przetestuj funkcję odpowiedzi walidacji, wklejając przykładowe zdarzenie do pola test dla funkcji:

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2018-01-25T22:12:19.4556811Z",
  "metadataVersion": "1",
  "dataVersion": "1"
}]
```

Kliknięcie przycisku Uruchom powoduje, że dane wyjściowe powinny być 200 OK i `{"validationResponse":"512d38b6-c7b8-40c8-89fe-f46f9e9622b6"}` w treści:

:::image type="content" source="./media/receive-events/validation-request.png" alt-text="Żądanie weryfikacji":::

:::image type="content" source="./media/receive-events/validation-output.png" alt-text="Dane wyjściowe walidacji":::

## <a name="handle-blob-storage-events"></a>Obsługa zdarzeń magazynu obiektów BLOB

Teraz przyciągnijmy funkcję do obsługi `Microsoft.Storage.BlobCreated` :

```cs
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.EventGrid;
using Microsoft.Azure.EventGrid.Models;

namespace FunctionApp1
{
    public static class Function1
    {
        [FunctionName("Function1")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation($"C# HTTP trigger function begun");
            string response = string.Empty;

            string requestContent = await new StreamReader(req.Body).ReadToEndAsync();
            log.LogInformation($"Received events: {requestContent}");

            EventGridSubscriber eventGridSubscriber = new EventGridSubscriber();

            EventGridEvent[] eventGridEvents = eventGridSubscriber.DeserializeEventGridEvents(requestContent);

            foreach (EventGridEvent eventGridEvent in eventGridEvents)
            {
                if (eventGridEvent.Data is SubscriptionValidationEventData)
                {
                    var eventData = (SubscriptionValidationEventData)eventGridEvent.Data;
                    log.LogInformation($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
                    // Do any additional validation (as required) and then return back the below response

                    var responseData = new SubscriptionValidationResponse()
                    {
                        ValidationResponse = eventData.ValidationCode
                    };

                    return new OkObjectResult(responseData);
                }
            }

            return new OkObjectResult(response);
        }
    }
}
```

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type  
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }

        else if (body.data && body.eventType == storageBlobCreatedEvent) {
            var blobCreatedEventData = body.data;
            context.log("Relaying received blob created event payload:" + JSON.stringify(blobCreatedEventData));
        }
    }
    context.done();
};

```

### <a name="test-blob-created-event-handling"></a>Obsługa zdarzeń dla testowego obiektu BLOB

Przetestuj nowe funkcje funkcji przez umieszczenie [zdarzenia magazynu obiektów BLOB](./event-schema-blob-storage.md#example-event) w polu test i uruchomienie:

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "0x8D4BCC2E4835CD0",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://example.blob.core.windows.net/testcontainer/testfile.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

W dzienniku funkcji powinny zostać wyświetlone dane wyjściowe adresu URL obiektu BLOB:

![Dziennik wyjściowy](./media/receive-events/blob-event-response.png)

Możesz również testować przez utworzenie konta usługi BLOB Storage lub konta magazynu Ogólnego przeznaczenia v2 (GPv2), [dodanie i subskrypcję zdarzeń](../storage/blobs/storage-blob-event-quickstart.md)oraz ustawienie punktu końcowego na adres URL funkcji:

![Adres URL funkcji](./media/receive-events/function-url.png)

## <a name="handle-custom-events"></a>Obsługa zdarzeń niestandardowych

Na koniec program umożliwia rozbudowanie funkcji jeszcze raz, aby umożliwić obsługę niestandardowych zdarzeń. 

W języku C# zestaw SDK obsługuje mapowanie nazwy typu zdarzenia na typ danych zdarzenia. Użyj `AddOrUpdateCustomEventMapping()` funkcji, aby zmapować zdarzenie niestandardowe.

Dodaj sprawdzanie dla zdarzenia `Contoso.Items.ItemReceived` . Końcowy kod powinien wyglądać następująco:

```cs
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.EventGrid;
using Newtonsoft.Json;

namespace FunctionApp1
{
    class ContosoItemReceivedEventData
    {
        [JsonProperty("itemSku")]
        public string ItemSku { get; set; }
    }
    public static class Function1
    {
        [FunctionName("Function1")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation($"C# HTTP trigger function begun");
            string response = string.Empty;

            string requestContent = await new StreamReader(req.Body).ReadToEndAsync();
            log.LogInformation($"Received events: {requestContent}");

            EventGridSubscriber eventGridSubscriber = new EventGridSubscriber();
            eventGridSubscriber.AddOrUpdateCustomEventMapping("Contoso.Items.ItemReceived", typeof(ContosoItemReceivedEventData));
            EventGridEvent[] eventGridEvents = eventGridSubscriber.DeserializeEventGridEvents(requestContent);

            foreach (EventGridEvent eventGridEvent in eventGridEvents)
            {
                if (eventGridEvent.Data is SubscriptionValidationEventData)
                {
                    var eventData = (SubscriptionValidationEventData)eventGridEvent.Data;
                    log.LogInformation($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
                    // Do any additional validation (as required) and then return back the below response

                    var responseData = new SubscriptionValidationResponse()
                    {
                        ValidationResponse = eventData.ValidationCode
                    };

                    return new OkObjectResult(responseData);
                }
                else if (eventGridEvent.Data is StorageBlobCreatedEventData)
                {
                    var eventData = (StorageBlobCreatedEventData)eventGridEvent.Data;
                    log.LogInformation($"Got BlobCreated event data, blob URI {eventData.Url}");
                }
                else if (eventGridEvent.Data is ContosoItemReceivedEventData)
                {
                    var eventData = (ContosoItemReceivedEventData)eventGridEvent.Data;
                    log.LogInformation($"Got ContosoItemReceived event data, item SKU {eventData.ItemSku}");
                }
            }

            return new OkObjectResult(response);
        }
    }
}
```

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";
    var customEventType = "Contoso.Items.ItemReceived";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }

        else if (body.data && body.eventType == storageBlobCreatedEvent) {
            var blobCreatedEventData = body.data;
            context.log("Relaying received blob created event payload:" + JSON.stringify(blobCreatedEventData));
        }

        else if (body.data && body.eventType == customEventType) {
            var payload = body.data;
            context.log("Relaying received custom payload:" + JSON.stringify(payload));
        }
    }
    context.done();
};
```

### <a name="test-custom-event-handling"></a>Testowanie obsługi zdarzeń niestandardowych

Na koniec sprawdź, czy funkcja może teraz obsłużyć niestandardowy typ zdarzenia:

```json
[{
    "subject": "Contoso/foo/bar/items",
    "eventType": "Contoso.Items.ItemReceived",
    "eventTime": "2017-08-16T01:57:26.005121Z",
    "id": "602a88ef-0001-00e6-1233-1646070610ea",
    "data": { 
            "itemSku": "Standard"
            },
    "dataVersion": "",
    "metadataVersion": "1"
}]
```

Możesz również przetestować tę funkcję na żywo, [wysyłając niestandardowe zdarzenie z zwinięciem z portalu](./custom-event-quickstart-portal.md) lub [ogłaszając w temacie niestandardowym](./post-to-custom-topic.md)  przy użyciu dowolnej usługi lub aplikacji, która może publikować w punkcie końcowym [, takim jak program](https://www.getpostman.com/). Utwórz temat niestandardowy i subskrypcję zdarzeń z punktem końcowym ustawionym jako adres URL funkcji.

[!INCLUDE [event-grid-message-headers](../../includes/event-grid-message-headers.md)]

## <a name="next-steps"></a>Następne kroki

* Eksplorowanie [Azure Event Grid zarządzania i publikowania zestawów SDK](./sdk-overview.md)
* Dowiedz się, jak [publikować w temacie niestandardowym](./post-to-custom-topic.md)
* Wypróbuj jeden z szczegółowych Event Grid i samouczków funkcji, takich jak [zmiany rozmiarów obrazów przesłane do magazynu obiektów BLOB](resize-images-on-storage-blob-upload-event.md)
