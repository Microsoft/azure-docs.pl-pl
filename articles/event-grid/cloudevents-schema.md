---
title: Używanie Azure Event Grid ze zdarzeniami w schemacie CloudEvents
description: Opisuje sposób użycia schematu CloudEvents dla zdarzeń w Azure Event Grid. Usługa obsługuje zdarzenia w implementacji JSON usługi CloudEvents.
ms.topic: conceptual
ms.date: 11/10/2020
ms.custom: devx-track-js, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 0ee816663a385601d4a31edbf87f8c787ea5aa91
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389506"
---
# <a name="use-cloudevents-v10-schema-with-event-grid"></a>Używanie schematu CloudEvents v1.0 z Event Grid
Oprócz [domyślnego](event-schema.md)schematu zdarzeń usługa Azure Event Grid natywnie obsługuje zdarzenia w implementacji JSON usługi [CloudEvents w wersji 1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) i powiązań [protokołu HTTP.](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md) [CloudEvents to](https://cloudevents.io/) otwarta [specyfikacja opisująca](https://github.com/cloudevents/spec/blob/v1.0/spec.md) dane zdarzenia.

Rozwiązanie CloudEvents upraszcza współdziałanie, zapewniając wspólny schemat zdarzeń do publikowania i obsługi zdarzeń w chmurze. Ten schemat umożliwia stosowanie jednolitych narzędzi, standardowych sposobów routingu i obsługi zdarzeń oraz uniwersalnych sposobów deserializacji zewnętrznego schematu zdarzeń. Dzięki wspólnej schematowi można łatwiej zintegrować pracę między platformami.

Usługa CloudEvents jest budowaną przez [kilku](https://github.com/cloudevents/spec/blob/master/community/contributors.md)współpracowników, w tym firmę Microsoft, za pośrednictwem platformy Cloud Native [Computing Foundation.](https://www.cncf.io/) Jest ona obecnie dostępna w wersji 1.0.

W tym artykule opisano sposób używania schematu CloudEvents z Event Grid.

## <a name="cloudevent-schema"></a>Schemat CloudEvent

Oto przykład zdarzenia zdarzenia Azure Blob Storage w formacie CloudEvents:

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

Aby uzyskać szczegółowy opis dostępnych pól, ich typów i definicji, zobacz [CloudEvents v1.0](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes).

Wartości nagłówków dla zdarzeń dostarczanych w schemacie CloudEvents i Event Grid są takie same z wyjątkiem `content-type` . W przypadku schematu CloudEvents ta wartość nagłówka to `"content-type":"application/cloudevents+json; charset=utf-8"` . W przypadku Event Grid wartość nagłówka to `"content-type":"application/json; charset=utf-8"` .

## <a name="configure-event-grid-for-cloudevents"></a>Konfigurowanie Event Grid dla usługi CloudEvents

Możesz użyć Event Grid zarówno dla danych wejściowych, jak i wyjściowych zdarzeń w schemacie CloudEvents. W poniższej tabeli opisano możliwe przekształcenia:

 Event Grid zasobów | Schemat wejściowy       | Schemat dostarczania
|---------------------|-------------------|---------------------
| Tematy systemowe       | Schemat usługi Event Grid | Event Grid schematu lub schematu CloudEvents
| Tematy/domeny niestandardowe | Schemat usługi Event Grid | Event Grid schematu lub schematu CloudEvents
| Tematy/domeny niestandardowe | Schemat CloudEvents | Schemat CloudEvents
| Tematy/domeny niestandardowe | Schemat niestandardowy     | Schemat niestandardowy, Event Grid lub schemat CloudEvents
| Tematy partnerów       | Schemat CloudEvents | Schemat CloudEvents

W przypadku wszystkich schematów zdarzeń Event Grid weryfikacji podczas publikowania w Event Grid tematu oraz podczas tworzenia subskrypcji zdarzeń.

Aby uzyskać więcej informacji, [zobacz Event Grid zabezpieczeń i uwierzytelniania.](security-authentication.md)

### <a name="input-schema"></a>Schemat wejściowy

Schemat wejściowy dla tematu niestandardowego ustawia się podczas tworzenia tematu niestandardowego.

W przypadku interfejsu wiersza polecenia platformy Azure użyj:

```azurecli-interactive
az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventschemav1_0
```

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
New-AzEventGridTopic `
  -ResourceGroupName gridResourceGroup `
  -Location westcentralus `
  -Name <topic_name> `
  -InputSchema CloudEventSchemaV1_0
```

### <a name="output-schema"></a>Schemat wyjściowy

Schemat wyjściowy jest ustawiany podczas tworzenia subskrypcji zdarzeń.

W przypadku interfejsu wiersza polecenia platformy Azure użyj:

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
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeliverySchema CloudEventSchemaV1_0
```

 Obecnie nie można używać wyzwalacza Event Grid dla aplikacji Azure Functions, gdy zdarzenie zostanie dostarczone w schemacie CloudEvents. Użyj wyzwalacza HTTP. Aby uzyskać przykłady implementacji wyzwalacza HTTP, który odbiera zdarzenia w schemacie CloudEvents, zobacz [Using CloudEvents with Azure Functions](#azure-functions)(Używanie obiektów CloudEvents z Azure Functions ).

## <a name="endpoint-validation-with-cloudevents-v10"></a>Walidacja punktu końcowego przy użyciu usługi CloudEvents w wersji 1.0

Jeśli znasz już te Event Grid, być może wiesz o uściśliniu weryfikacji punktu końcowego w celu zapobiegania nadużyciom. Usługa CloudEvents 1.0 implementuje własną semantykę ochrony przed [nadużyciami](webhook-event-delivery.md) przy użyciu metody HTTP OPTIONS. Aby dowiedzieć się więcej na ten temat, [zobacz HTTP 1.1 Web Hooks for event delivery - Version 1.0 (Web Hooks 1.1 Http 1.1 Web Hooks for event delivery — wersja 1.0).](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection) W przypadku użycia schematu CloudEvents dla danych wyjściowych usługa Event Grid używa ochrony przed nadużyciami w wersji 1.0 usługi CloudEvents w miejsce mechanizmu zdarzeń weryfikacji Event Grid wirtualnej.

<a name="azure-functions"></a>

## <a name="use-with-azure-functions"></a>Używanie z Azure Functions

Powiązanie [Azure Functions Event Grid nie](../azure-functions/functions-bindings-event-grid.md) obsługuje natywnie usługi CloudEvents, więc funkcje wyzwalane przez protokół HTTP są używane do odczytywania komunikatów CloudEvents. Gdy używasz wyzwalacza HTTP do odczytywania aplikacji CloudEvents, musisz napisać kod tego, co wyzwalacz Event Grid automatycznie:

* Wysyła odpowiedź weryfikacji na żądanie [weryfikacji subskrypcji](../event-grid/webhook-event-delivery.md)
* Wywołuje funkcję raz na element tablicy zdarzeń zawartej w treści żądania

Aby uzyskać informacje o adresie URL do wywoływania funkcji lokalnie lub po jej użyciu na platformie Azure, zobacz dokumentację referencyjną powiązania [wyzwalacza HTTP](../azure-functions/functions-bindings-http-webhook.md).

Poniższy przykładowy kod języka C# dla wyzwalacza HTTP symuluje Event Grid działania wyzwalacza. Użyj tego przykładu dla zdarzeń dostarczanych w schemacie CloudEvents.

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run([HttpTrigger(AuthorizationLevel.Anonymous, "post", "options", Route = null)]HttpRequestMessage req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");
    if (req.Method == HttpMethod.Options)
    {
        // If the request is for subscription validation, send back the validation code
        
        var response = req.CreateResponse(HttpStatusCode.OK);
        response.Headers.Add("Webhook-Allowed-Origin", "eventgrid.azure.net");

        return response;
    }

    var requestmessage = await req.Content.ReadAsStringAsync();
    var message = JToken.Parse(requestmessage);

    // The request isn't for subscription validation, so it's for an event.
    // CloudEvents schema delivers one event at a time.
    log.LogInformation($"Source: {message["source"]}");
    log.LogInformation($"Time: {message["eventTime"]}");
    log.LogInformation($"Event data: {message["data"].ToString()}");

    return req.CreateResponse(HttpStatusCode.OK);
}
```

Poniższy przykładowy kod JavaScript wyzwalacza HTTP symuluje Event Grid wyzwalacza. Użyj tego przykładu dla zdarzeń dostarczanych w schemacie CloudEvents.

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    
    if (req.method == "OPTIONS") {
        // If the request is for subscription validation, send back the validation code
        
        context.log('Validate request received');
        context.res = {
            status: 200,
            headers: {
                'Webhook-Allowed-Origin': 'eventgrid.azure.net',
            },
         };
    }
    else
    {
        var message = req.body;
        
        // The request isn't for subscription validation, so it's for an event.
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

* Aby uzyskać informacje na temat monitorowania dostaw zdarzeń, [zobacz Monitorowanie Event Grid dostarczania komunikatów.](monitor-event-delivery.md)
* Zachęcamy do testowania, komentowania i [współtwonia rozwiązań CloudEvents.](https://github.com/cloudevents/spec/blob/master/community/CONTRIBUTING.md)
* Aby uzyskać więcej informacji na temat tworzenia Azure Event Grid subskrypcji, zobacz [Event Grid schematu subskrypcji](subscription-creation-schema.md).
