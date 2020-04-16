---
title: Używanie usługi Azure Event Grid ze zdarzeniami w schemacie CloudEvents
description: W tym artykule opisano sposób używania schematu CloudEvents dla zdarzeń w usłudze Azure Event Grid. Usługa obsługuje zdarzenia w implementacji JSON cloud events.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: babanisa
ms.openlocfilehash: 404052984cb99e37f7404a47f3ac374088d32d6c
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393476"
---
# <a name="use-cloudevents-v10-schema-with-event-grid"></a>Używanie schematu CloudEvents w wersji 1.0 z siatką zdarzeń
Oprócz [domyślnego schematu zdarzeń usługa](event-schema.md)Azure Event Grid obsługuje zdarzenia w [implementacji JSON powiązania protokołu CloudEvents w wersji 1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) i [http.](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md) [CloudEvents](https://cloudevents.io/) to [otwarta specyfikacja](https://github.com/cloudevents/spec/blob/v1.0/spec.md) opisująca dane zdarzeń.

CloudEvents upraszcza współdziałanie, udostępniając wspólny schemat zdarzeń do publikowania i likwale zdarzeń opartych na chmurze. Ten schemat umożliwia jednolite narzędzia, standardowe sposoby routingu & obsługi zdarzeń i uniwersalne sposoby deserializacji schematu zdarzeń zewnętrznych. Za pomocą wspólnego schematu można łatwiej zintegrować pracę na różnych platformach.

CloudEvents jest budowany przez kilku [współpracowników,](https://github.com/cloudevents/spec/blob/master/community/contributors.md)w tym Microsoft, za pośrednictwem [Cloud Native Computing Foundation.](https://www.cncf.io/) Jest obecnie dostępny w wersji 1.0.

W tym artykule opisano sposób korzystania ze schematu CloudEvents z siatką zdarzeń.

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Instalowanie funkcji w wersji zapoznawczej

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="cloudevent-schema"></a>Schemat CloudEvent

Oto przykład zdarzenia usługi Azure Blob Storage w formacie CloudEvents:

``` JSON
{
    "specversion": "1.0",
    "type": "Microsoft.Storage.BlobCreated",  
    "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}",
    "id": "9aeb0fdf-c01e-0131-0922-9eb54906e209",
    "time": "2019-11-18T15:13:39.4589254Z",
    "subject": "blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "dataschema": "#",
    "data": {
        "api": "PutBlockList",
        "clientRequestId": "4c5dd7fb-2c48-4a27-bb30-5361b5de920a",
        "requestId": "9aeb0fdf-c01e-0131-0922-9eb549000000",
        "eTag": "0x8D76C39E4407333",
        "contentType": "image/png",
        "contentLength": 30699,
        "blobType": "BlockBlob",
        "url": "https://gridtesting.blob.core.windows.net/testcontainer/{new-file}",
        "sequencer": "000000000000000000000000000099240000000000c41c18",
        "storageDiagnostics": {
            "batchId": "681fe319-3006-00a8-0022-9e7cde000000"
        }
    }
}
```

Szczegółowy opis dostępnych pól, ich typów i definicji w cloudevents v1.0 jest [dostępny tutaj](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes).

Wartości nagłówków dla zdarzeń dostarczanych w schemacie CloudEvents i event `content-type`grid są takie same, z wyjątkiem . W przypadku schematu CloudEvents `"content-type":"application/cloudevents+json; charset=utf-8"`ta wartość nagłówka jest . W przypadku schematu siatki zdarzeń `"content-type":"application/json; charset=utf-8"`ta wartość nagłówka to .

## <a name="configure-event-grid-for-cloudevents"></a>Konfigurowanie siatki zdarzeń dla cloudevents

Można użyć usługi Event Grid dla danych wejściowych i wyjściowych zdarzeń w schemacie CloudEvents. CloudEvents można używać dla zdarzeń systemowych, takich jak zdarzenia magazynu obiektów Blob i zdarzenia Usługi IoT Hub i zdarzenia niestandardowe. Może również przekształcić te zdarzenia na drucie tam iz powrotem.


| Schemat wprowadzania       | Schemat danych wyjściowych
|--------------------|---------------------
| Format CloudEvents | Format CloudEvents
| Format siatki zdarzeń  | Format CloudEvents
| Format siatki zdarzeń  | Format siatki zdarzeń

Dla wszystkich schematów zdarzeń usługa Event Grid wymaga weryfikacji podczas publikowania w temacie siatki zdarzeń i podczas tworzenia subskrypcji zdarzeń. Aby uzyskać więcej informacji, zobacz [Zabezpieczenia i uwierzytelnianie w siatce zdarzeń](security-authentication.md).

### <a name="input-schema"></a>Schemat wprowadzania

Schemat wprowadzania dla tematu niestandardowego można ustawić podczas tworzenia tematu niestandardowego.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
# If you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventschemav1_0
```

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridTopic `
  -ResourceGroupName gridResourceGroup `
  -Location westcentralus `
  -Name <topic_name> `
  -InputSchema CloudEventSchemaV1_0
```

### <a name="output-schema"></a>Schemat danych wyjściowych

Schemat danych wyjściowych można ustawić podczas tworzenia subskrypcji zdarzenia.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
topicID=$(az eventgrid topic show --name <topic-name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --name <event_subscription_name> \
  --source-resource-id $topicID \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventschemav1_0
```

W przypadku programu PowerShell użyj polecenia:
```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeliverySchema CloudEventSchemaV1_0
```

 Obecnie nie można użyć wyzwalacza siatki zdarzeń dla aplikacji usługi Azure Functions, gdy zdarzenie jest dostarczane w schemacie CloudEvents. Użyj wyzwalacza HTTP. Przykłady implementowania wyzwalacza HTTP, który odbiera zdarzenia w schemacie CloudEvents, zobacz [Korzystanie z funkcji CloudEvents z usługą Azure Functions](#azure-functions).

 ## <a name="endpoint-validation-with-cloudevents-v10"></a>Sprawdzanie poprawności punktu końcowego za pomocą cloudevents w wersji 1.0

Jeśli jesteś już zaznajomiony z event grid, może być świadomy uzgadniania sprawdzania poprawności punktu końcowego zdarzenia w celu zapobiegania nadużyciom. CloudEvents w wersji 1.0 implementuje własną [semantytykę ochrony przed nadużyciami](security-authentication.md#webhook-event-delivery) przy użyciu metody OPCJE HTTP. Więcej informacji na ten temat można znaleźć [tutaj](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). Podczas korzystania ze schematu CloudEvents dla danych wyjściowych, Usługa Event Grid używa z cloudEvents v1.0 ochrony przed nadużyciami zamiast mechanizmu sprawdzania poprawności siatki zdarzeń.

<a name="azure-functions"></a>

## <a name="use-with-azure-functions"></a>Używanie z usługą Azure Functions

[Powiązanie usługi Azure Functions Event Grid](../azure-functions/functions-bindings-event-grid.md) nie obsługuje natywnie cloudevents, więc funkcje wyzwalane przez HTTP są używane do odczytywania komunikatów CloudEvents. Podczas korzystania z wyzwalacza HTTP do odczytu CloudEvents, należy napisać kod, co wyzwalacz siatki zdarzeń robi automatycznie:

* Wysyła odpowiedź sprawdzania poprawności do [żądania sprawdzania poprawności subskrypcji](../event-grid/security-authentication.md#webhook-event-delivery).
* Wywołuje funkcję raz na element tablicy zdarzeń zawartej w treści żądania.

Aby uzyskać informacje na temat adresu URL do wywoływania funkcji lokalnie lub podczas uruchamiania na platformie Azure, zobacz [dokumentację odwołania do powiązania wyzwalacza HTTP](../azure-functions/functions-bindings-http-webhook.md)

Poniższy przykładowy kod języka C# dla wyzwalacza HTTP symuluje zachowanie wyzwalacza usługi Event Grid.  Użyj tego przykładu dla zdarzeń dostarczanych w schemacie CloudEvents.

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run([HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", "options", Route = null)]HttpRequestMessage req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");
    if (req.Method == "OPTIONS")
    {
        // If the request is for subscription validation, send back the validation code
        
        var response = req.CreateResponse(HttpStatusCode.OK);
        response.Add("Webhook-Allowed-Origin", "eventgrid.azure.net");

        return response;
    }

    var requestmessage = await req.Content.ReadAsStringAsync();
    var message = JToken.Parse(requestmessage);

    // The request is not for subscription validation, so it's for an event.
    // CloudEvents schema delivers one event at a time.
    log.LogInformation($"Source: {message["source"]}");
    log.LogInformation($"Time: {message["eventTime"]}");
    log.LogInformation($"Event data: {message["data"].ToString()}");

    return req.CreateResponse(HttpStatusCode.OK);
}
```

Poniższy przykładowy kod JavaScript dla wyzwalacza HTTP symuluje zachowanie wyzwalacza usługi Event Grid. Użyj tego przykładu dla zdarzeń dostarczanych w schemacie CloudEvents.

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    
    if (req.method == "OPTIONS) {
        // If the request is for subscription validation, send back the validation code
        
        context.log('Validate request received');
        context.res = { status: 200, body: { "ValidationResponse": code } };
        context.res.headers.append('Webhook-Allowed-Origin', 'eventgrid.azure.net');
    }
    else
    {
        var message = req.body;
        
        // The request is not for subscription validation, so it's for an event.
        // CloudEvents schema delivers one event at a time.
        var event = JSON.parse(message);
        context.log('Source: ' + event.source);
        context.log('Time: ' + event.eventTime);
        context.log('Data: ' + JSON.stringify(event.data));
    }
 
    context.done();
};
```

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje dotyczące monitorowania dostaw zdarzeń, zobacz [Monitorowanie dostarczania komunikatów w sieci zdarzeń](monitor-event-delivery.md).
* Zachęcamy do testowania, komentowania i [przyczyniania się](https://github.com/cloudevents/spec/blob/master/CONTRIBUTING.md) do CloudEvents.
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji usługi Azure Event Grid, zobacz [schemat subskrypcji usługi Event Grid](subscription-creation-schema.md).
