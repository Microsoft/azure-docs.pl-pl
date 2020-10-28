---
title: Używanie Azure Event Grid ze zdarzeniami w schemacie CloudEvents
description: Opisuje sposób używania schematu CloudEvents dla zdarzeń w Azure Event Grid. Usługa obsługuje zdarzenia w implementacji JSON zdarzeń w chmurze.
ms.topic: conceptual
ms.date: 07/07/2020
ms.custom: devx-track-js, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 51e3f5477cad39b48b441122cf17599f7d25ccf8
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92747289"
---
# <a name="use-cloudevents-v10-schema-with-event-grid"></a>Używanie schematu CloudEvents v 1.0 z Event Grid
Poza [domyślnym schematem zdarzeń](event-schema.md), Azure Event Grid natywnie obsługuje zdarzenia w [implementacji JSON CloudEvents v 1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) i [powiązania protokołu HTTP](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md). [CloudEvents](https://cloudevents.io/) to [otwarta Specyfikacja](https://github.com/cloudevents/spec/blob/v1.0/spec.md) dla opisywania danych zdarzenia.

CloudEvents upraszcza współdziałanie, dostarczając Typowy schemat zdarzeń do publikowania i zużywania zdarzeń opartych na chmurze. Ten schemat umożliwia korzystanie z jednolitych narzędzi, standardowych metod routingu & obsługi zdarzeń oraz uniwersalne sposoby deserializacji schematu zdarzenia zewnętrznego. Ze wspólnym schematem można łatwiej zintegrować prace między platformami.

CloudEvents jest tworzona przez kilku [współpracowników](https://github.com/cloudevents/spec/blob/master/community/contributors.md), w tym firmę Microsoft, za pośrednictwem [natywnej podstawy przetwarzania w chmurze](https://www.cncf.io/). Jest ona obecnie dostępna jako wersja 1,0.

W tym artykule opisano sposób używania schematu CloudEvents z Event Grid.

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

Szczegółowy opis dostępnych pól, ich typów i definicji w CloudEvents v 1.0 jest [dostępny tutaj](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes).

Wartości nagłówków dla zdarzeń dostarczonych w schemacie CloudEvents i schemacie Event Grid są takie same, z wyjątkiem `content-type` . W przypadku schematu CloudEvents wartość tego nagłówka to `"content-type":"application/cloudevents+json; charset=utf-8"` . W przypadku schematu Event Grid wartość tego nagłówka to `"content-type":"application/json; charset=utf-8"` .

## <a name="configure-event-grid-for-cloudevents"></a>Konfigurowanie Event Grid CloudEvents

Można użyć Event Grid zarówno dla danych wejściowych, jak i wyjściowych zdarzeń w schemacie CloudEvents. CloudEvents można używać dla zdarzeń systemowych, takich jak zdarzenia Blob Storage i zdarzenia IoT Hub i zdarzenia niestandardowe. Może również przekształcić te zdarzenia do tyłu i do przodu.


| Schemat wejściowy       | Schemat danych wyjściowych
|--------------------|---------------------
| Format CloudEvents | Format CloudEvents
| Event Grid format  | Format CloudEvents
| Event Grid format  | Event Grid format

W przypadku wszystkich schematów zdarzeń Event Grid wymaga weryfikacji podczas publikowania w temacie Event Grid i podczas tworzenia subskrypcji zdarzeń. Aby uzyskać więcej informacji, zobacz [Event Grid zabezpieczenia i uwierzytelnianie](security-authentication.md).

### <a name="input-schema"></a>Schemat wejściowy

Schemat wejściowy dla tematu niestandardowego ustawia się podczas tworzenia tematu niestandardowego.

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

Schemat wyjściowy jest ustawiany podczas tworzenia subskrypcji zdarzeń.

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

 Obecnie nie można używać wyzwalacza Event Grid dla aplikacji Azure Functions, gdy zdarzenie jest dostarczane w schemacie CloudEvents. Użyj wyzwalacza HTTP. Przykłady implementacji wyzwalacza HTTP, który odbiera zdarzenia w schemacie CloudEvents, można znaleźć w temacie [using CloudEvents with Azure Functions](#azure-functions).

 ## <a name="endpoint-validation-with-cloudevents-v10"></a>Walidacja punktu końcowego z CloudEvents v 1.0

Jeśli znasz już Event Grid, możesz mieć świadomość, Event Grid uzgadniania sprawdzania poprawności punktu końcowego w celu zapobiegania nadużyciom. CloudEvents v 1.0 implementuje własną [semantykę ochrony przed nadużyciami](webhook-event-delivery.md) przy użyciu metody http Options. Więcej informacji na ten temat można znaleźć [tutaj](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). W przypadku korzystania ze schematu CloudEvents dla danych wyjściowych Event Grid jest używany z ochroną nadużycia CloudEvents v 1.0 zamiast mechanizmu zdarzenia walidacji Event Grid.

<a name="azure-functions"></a>

## <a name="use-with-azure-functions"></a>Używanie z Azure Functions

[Powiązanie Event Grid Azure Functions](../azure-functions/functions-bindings-event-grid.md) nie obsługuje natywnie CloudEvents, więc funkcje wyzwalane przez protokół HTTP są używane do odczytywania komunikatów CloudEvents. W przypadku korzystania z wyzwalacza HTTP w celu odczytu CloudEvents należy napisać kod, który automatycznie wyzwalacz Event Grid:

* Wysyła odpowiedź weryfikacji do [żądania weryfikacji subskrypcji](../event-grid/webhook-event-delivery.md).
* Wywołuje funkcję jeden raz dla elementu tablicy zdarzeń zawartej w treści żądania.

Aby uzyskać informacje o adresie URL używanym do wywoływania funkcji lokalnie lub uruchamianej na platformie Azure, zobacz [dokumentację referencyjną powiązania wyzwalacza http](../azure-functions/functions-bindings-http-webhook.md)

Poniższy przykładowy kod w języku C# dla wyzwalacza HTTP symuluje działanie wyzwalacza Event Grid.  Użyj tego przykładu dla zdarzeń dostarczonych w schemacie CloudEvents.

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

Następujący przykładowy kod JavaScript dla wyzwalacza HTTP symuluje działanie wyzwalacza Event Grid. Użyj tego przykładu dla zdarzeń dostarczonych w schemacie CloudEvents.

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    
    if (req.method == "OPTIONS") {
        // If the request is for subscription validation, send back the validation code
        
        context.log('Validate request received');
        context.res = { status: 200 };
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

* Aby uzyskać informacje na temat monitorowania dostarczania zdarzeń, zobacz [monitorowanie Event Grid dostarczania komunikatów](monitor-event-delivery.md).
* Zachęcamy do testowania, komentowania i [współtworzenia](https://github.com/cloudevents/spec/blob/master/community/CONTRIBUTING.md) CloudEvents.
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji Azure Event Grid, zobacz [Event Grid schematu subskrypcji](subscription-creation-schema.md).
