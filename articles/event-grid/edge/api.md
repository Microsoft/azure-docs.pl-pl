---
title: Interfejs API REST — Azure Event Grid IoT Edge | Microsoft Docs
description: Interfejs API REST na Event Grid na IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 414487d460d897eff787b11915db560706b29eb4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171758"
---
# <a name="rest-api"></a>Interfejs API REST
W tym artykule opisano interfejsy API REST Azure Event Grid na IoT Edge

## <a name="common-api-behavior"></a>Wspólne zachowanie interfejsu API

### <a name="base-url"></a>Podstawowy adres URL
Event Grid on IoT Edge ma następujące interfejsy API udostępniane za pośrednictwem protokołu HTTP (port 5888) i HTTPS (port 4438).

* Podstawowy adres URL dla HTTP: http://eventgridmodule:5888
* Podstawowy adres URL dla protokołu HTTPS: https://eventgridmodule:4438

### <a name="request-query-string"></a>Ciąg zapytania żądania
Wszystkie żądania interfejsu API wymagają następującego parametru ciągu zapytania:

```?api-version=2019-01-01-preview```

### <a name="request-content-type"></a>Typ zawartości żądania
Wszystkie żądania interfejsu API muszą mieć **Typ zawartości**.

W przypadku **EventGridSchema** lub **CustomSchema** wartość typu zawartości może być jedną z następujących wartości:

```Content-Type: application/json```

```Content-Type: application/json; charset=utf-8```

W przypadku **CloudEventSchemaV1_0** w trybie strukturalnym wartość typu zawartości może być jedną z następujących wartości:

```Content-Type: application/cloudevents+json```
    
```Content-Type: application/cloudevents+json; charset=utf-8```
    
```Content-Type: application/cloudevents-batch+json```
    
```Content-Type: application/cloudevents-batch+json; charset=utf-8```

W przypadku **CloudEventSchemaV1_0** w trybie binarnym zapoznaj się z [dokumentacją](https://github.com/cloudevents/spec/blob/master/http-protocol-binding.md) , aby uzyskać szczegółowe informacje.

### <a name="error-response"></a>Odpowiedź na błąd
Wszystkie interfejsy API zwracają błąd z następującym ładunkiem:

```json
{
    "error":
    {
        "code": "<HTTP STATUS CODE>",
        "details": 
        {
            "code": "<Detailed Error Code>",
            "message": "..."
        }
    }
}
```

## <a name="manage-topics"></a>Zarządzanie tematami

### <a name="put-topic-create--update"></a>Umieść temat (Utwórz/zaktualizuj)

**Żądanie**: ``` PUT /topics/<topic_name>?api-version=2019-01-01-preview ```

**Ładunek**:

```json
    {
        "name": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "properties":
        {
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // optional
        }
    }
```

**Odpowiedź**: http 200

**Ładunek**:

```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
    "name": "<topic_name>",
    "type": "Microsoft.EventGrid/topics",
    "properties":
    {
        "endpoint": "<get_request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
        "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // populated with EventGridSchema if not explicitly specified in PUT request
    }
}
```

### <a name="get-topic"></a>Pobierz temat

**Żądanie**: ``` GET /topics/<topic_name>?api-version=2019-01-01-preview ```

**Odpowiedź**: http 200

**Ładunek**:
```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
    "name": "<topic_name>",
    "type": "Microsoft.EventGrid/topics",
    "properties":
    {
        "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
        "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
    }
}
```

### <a name="get-all-topics"></a>Pobierz wszystkie tematy

**Żądanie**: ``` GET /topics?api-version=2019-01-01-preview ```

**Odpowiedź**: http 200

**Ładunek**:
```json
[
    {
        "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
        "name": "<topic_name>",
        "type": "Microsoft.EventGrid/topics",
        "properties":
        {
            "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
        }
    },
    {
        "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
        "name": "<topic_name>",
        "type": "Microsoft.EventGrid/topics",
        "properties":
        {
            "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
        }
    }
]
```

### <a name="delete-topic"></a>Usuń temat

**Żądanie**: ``` DELETE /topics/<topic_name>?api-version=2019-01-01-preview ```

**Odpowiedź**: http 200, pusty ładunek

## <a name="manage-event-subscriptions"></a>Zarządzanie subskrypcjami zdarzeń
Przykłady w tej sekcji są używane `EndpointType=Webhook;` . Przykłady JSON dla programu `EndpointType=EdgeHub / EndpointType=EventGrid` znajdują się w następnej sekcji. 

### <a name="put-event-subscription-create--update"></a>Umieść subskrypcję zdarzeń (Utwórz/zaktualizuj)

**Żądanie**: ``` PUT /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Ładunek**:
```json
{
    "name": "<subscription_name>", // optional, inferred from URL. If specified must match URL subscription_name
    "properties":
    {
        "topicName": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0", // optional
        "retryPolicy":  //optional
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "persistencePolicy": "true",
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // optional
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```

**Odpowiedź**: http 200

**Ładunek**:

```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>/eventSubscriptions/<subscription_name>",
    "name": "<subscription_name>",
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "properties":
    {
        "topicName": "<topic_name>",
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema  | CloudEventSchemaV1_0", // populated with EventGridSchema if not explicitly specified in PUT request
        "retryPolicy":  // only populated if specified in the PUT request
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // only populated if specified in the PUT request 
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```


### <a name="get-event-subscription"></a>Pobierz subskrypcję zdarzeń

**Żądanie**: ``` GET /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Odpowiedź**: http 200

**Ładunek**:
```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>/eventSubscriptions/<subscription_name>",
    "name": "<subscription_name>",
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "properties":
    {
        "topicName": "<topic_name>",
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema  | CloudEventSchemaV1_0", // populated with EventGridSchema if not explicitly specified in PUT request
        "retryPolicy":  // only populated if specified in the PUT request
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // only populated if specified in the PUT request 
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```

### <a name="get-event-subscriptions"></a>Pobierz subskrypcje zdarzeń

**Żądanie**: ``` GET /topics/<topic_name>/eventSubscriptions?api-version=2019-01-01-preview ```

**Odpowiedź**: http 200

**Ładunek**:
```json
[
    {
        // same event-subscription json as that returned from Get-EventSubscription above
    },
    {
    },
    ...
]
```

### <a name="delete-event-subscription"></a>Usuń subskrypcję zdarzeń

**Żądanie**: ``` DELETE /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Odpowiedź**: http 200, brak ładunku


## <a name="publish-events-api"></a>Interfejs API zdarzeń publikowania

### <a name="send-batch-of-events-in-event-grid-schema"></a>Wyślij partię zdarzeń (w Event Grid schemacie)

**Żądanie**: ``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        "id": "<user-defined-event-id>",
        "topic": "<topic_name>",
        "subject": "",
        "eventType": "",
        "eventTime": ""
        "dataVersion": "",
        "metadataVersion": "1",
        "data": 
            ...
    }
]
```

**Odpowiedź**: http 200, pusty ładunek


**Opisy pól ładunku**
- ```Id``` jest obowiązkowy. Może to być dowolna wartość ciągu, która jest wypełniana przez obiekt wywołujący. Event Grid nie wykonuje żadnych zduplikowanych wykryć ani nie wymusza żadnej semantyki tego pola.
- ```Topic``` jest opcjonalne, ale jeśli określony musi pasować do topic_name z adresu URL żądania
- ```Subject``` jest obowiązkowy, może być dowolną wartością ciągu
- ```EventType``` jest obowiązkowy, może być dowolną wartością ciągu
- ```EventTime``` jest obowiązkowy, nie jest sprawdzany, ale powinien być prawidłowym obiektem DateTime.
- ```DataVersion``` jest obowiązkowy
- ```MetadataVersion``` jest opcjonalne, jeśli określony, musi być ciągiem z wartością ```"1"```
- ```Data``` jest opcjonalne i może być dowolnym tokenem JSON (Number, String, Boolean, Array, Object)

### <a name="send-batch-of-events-in-custom-schema"></a>Wyślij partię zdarzeń (w schemacie niestandardowym)

**Żądanie**: ``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        ...
    }
]
```

**Odpowiedź**: http 200, pusty ładunek


**Ograniczenia ładunku**
- MUSI być tablicą zdarzeń.
- Każdy wpis tablicy musi być obiektem JSON.
- Brak innych ograniczeń (innych niż rozmiar ładunku).

## <a name="examples"></a>Przykłady

### <a name="set-up-topic-with-eventgrid-schema"></a>Konfigurowanie tematu za pomocą schematu EventGrid
Konfiguruje temat, aby wymagać opublikowania zdarzeń w programie **eventgridschema**.

```json
    {
        "name": "myeventgridtopic",
        "properties":
        {
            "inputSchema": "EventGridSchema"
        }
    }
```

### <a name="set-up-topic-with-custom-schema"></a>Konfigurowanie tematu ze schematem niestandardowym
Konfiguruje temat, aby wymagać opublikowania zdarzeń w programie `customschema` .

```json
    {
        "name": "mycustomschematopic",
        "properties":
        {
            "inputSchema": "CustomSchema"
        }
    }
```

### <a name="set-up-topic-with-cloud-event-schema"></a>Konfigurowanie tematu przy użyciu schematu zdarzeń w chmurze
Konfiguruje temat, aby wymagać opublikowania zdarzeń w programie `cloudeventschema` .

```json
    {
        "name": "mycloudeventschematopic",
        "properties":
        {
            "inputSchema": "CloudEventSchemaV1_0"
        }
    }
```

### <a name="set-up-webhook-as-destination-events-to-be-delivered-in-eventgridschema"></a>Skonfiguruj element webhook jako miejsce docelowe, zdarzenia, które mają być dostarczone w eventgridschema
Ten typ docelowy służy do wysyłania zdarzeń do dowolnego innego modułu (który obsługuje punkt końcowy HTTP) lub do dowolnego punktu końcowego z adresem HTTP w sieci/Internecie.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "eventDeliverySchema": "eventgridschema",
            }
        }
    }
}
```

Ograniczenia dotyczące `endpointUrl` atrybutu:
- Nie może mieć wartości null.
- Musi to być bezwzględny adres URL.
- Jeśli outbound__webhook__httpsOnly ma wartość true w ustawieniach EventGridModule, musi być tylko HTTPS.
- Jeśli outbound__webhook__httpsOnly ma wartość false, może to być HTTP lub HTTPS.

Ograniczenia dotyczące `eventDeliverySchema` Właściwości:
- Musi być zgodna ze schematem wejściowym tematu subskrybowania.
- Może mieć wartość null. Domyślnie jest to schemat wejściowy tematu.

### <a name="set-up-iot-edge-as-destination"></a>Skonfiguruj IoT Edge jako miejsce docelowe

Ta lokalizacja docelowa służy do wysyłania zdarzeń do centrum IoT Edge i podlegająca przekierowaniu do podsystemu Routing/filtrowanie/przekazywanie.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "EdgeHub",
            "properties":
            {
                "outputName": "<eventgridmodule_output_port_name>"
            }
        }
    }
}
```

### <a name="set-up-event-grid-cloud-as-destination"></a>Skonfiguruj Event Grid chmurę jako lokalizację docelową

Ta lokalizacja docelowa służy do wysyłania zdarzeń do Event Grid w chmurze (Azure). Najpierw należy skonfigurować temat użytkownika w chmurze, do którego mają być wysyłane zdarzenia, przed utworzeniem subskrypcji zdarzeń na krawędzi.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "EventGrid",
            "properties":
            {
                "endpointUrl": "<eventgrid_user_topic_url>",
                "sasKey": "<user_topic_sas_key>",
                "topicName": "<new value to populate in forwarded EventGridEvent.Topic>" // if not specified, the Topic field on every event gets nulled out before being sent to Azure Event Grid
            }
        }
    }
}
```

EndpointUrl
- Nie może mieć wartości null.
- Musi to być bezwzględny adres URL.
- Ścieżka `/api/events` musi być zdefiniowana w ścieżce URL żądania.
- Musi zawierać `api-version=2018-01-01` ciąg zapytania.
- Jeśli outbound__eventgrid__httpsOnly ma wartość true w ustawieniach EventGridModule (domyślnie true), musi być tylko HTTPS.
- Jeśli outbound__eventgrid__httpsOnly jest ustawiona na false, może to być HTTP lub HTTPS.
- Jeśli outbound__eventgrid__allowInvalidHostnames jest ustawiona na false (domyślnie false), musi ona wskazywać jeden z następujących punktów końcowych:
   - `eventgrid.azure.net`
   - `eventgrid.azure.us`
   - `eventgrid.azure.cn`

SasKey:
- Nie może mieć wartości null.

Temat:
- Jeśli subskrypcja. EventDeliverySchema ma wartość EventGridSchema, wartość z tego pola jest umieszczana w polu tematu każdego zdarzenia przed przekazaniem do Event Grid w chmurze.
- Jeśli wartość Subscription. EventDeliverySchema jest ustawiona na CustomEventSchema, ta właściwość jest ignorowana, a ładunek zdarzenia niestandardowego jest przekazywany dokładnie tak, jak został odebrany.

## <a name="set-up-event-hubs-as-a-destination"></a>Skonfiguruj Event Hubs jako lokalizację docelową

Aby opublikować w centrum zdarzeń, ustaw polecenie `endpointType` na `eventHub` i podaj:

* connectionString: parametry połączenia dla określonego centrum zdarzeń, które są przeznaczone do wygenerowania za pośrednictwem zasad dostępu współdzielonego.

    >[!NOTE]
    > Parametry połączenia muszą być specyficzne dla jednostki. Używanie parametrów połączenia z przestrzenią nazw nie będzie działało. Parametry połączenia specyficzne dla jednostki można wygenerować, przechodząc do określonego centrum zdarzeń, które chcesz opublikować w witrynie Azure Portal, a następnie klikając pozycję **zasady dostępu współdzielonego** , aby wygenerować nowy ciąg connecection specyficzny dla jednostki.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventHub",
              "properties": {
                "connectionString": "<your-event-hub-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-service-bus-queues-as-a-destination"></a>Skonfiguruj kolejki Service Bus jako miejsce docelowe

Aby opublikować w kolejce Service Bus, ustaw dla opcji `endpointType` wartość `serviceBusQueue` i podaj:

* connectionString: parametry połączenia dla konkretnej kolejki Service Bus są generowane przez zasady dostępu współdzielonego.

    >[!NOTE]
    > Parametry połączenia muszą być specyficzne dla jednostki. Używanie parametrów połączenia z przestrzenią nazw nie będzie działało. Wygeneruj parametry połączenia specyficzne dla jednostki, przechodząc do konkretnej kolejki Service Bus, w której chcesz opublikować w witrynie Azure Portal, a następnie klikając pozycję **zasady dostępu współdzielonego** , aby wygenerować nowy ciąg connecection specyficzny dla jednostki.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusQueue",
              "properties": {
                "connectionString": "<your-service-bus-queue-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-service-bus-topics-as-a-destination"></a>Skonfiguruj Service Bus tematy jako miejsce docelowe

Aby opublikować w temacie Service Bus, ustaw polecenie `endpointType` na `serviceBusTopic` i podaj:

* connectionString: parametry połączenia dla określonego tematu Service Bus, które są przeznaczone do wygenerowania w ramach zasad dostępu współdzielonego.

    >[!NOTE]
    > Parametry połączenia muszą być specyficzne dla jednostki. Używanie parametrów połączenia z przestrzenią nazw nie będzie działało. Wygeneruj parametry połączenia specyficzne dla jednostki, przechodząc do określonego tematu Service Bus, w którym chcesz opublikować w witrynie Azure Portal, a następnie klikając pozycję **zasady dostępu współdzielonego** , aby wygenerować nowy ciąg connecection specyficzny dla jednostki.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusTopic",
              "properties": {
                "connectionString": "<your-service-bus-topic-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-storage-queues-as-a-destination"></a>Skonfiguruj kolejki magazynu jako lokalizację docelową

Aby opublikować w kolejce magazynu, ustaw polecenie  `endpointType` na `storageQueue` i podaj:

* QueueName: Nazwa kolejki magazynu, w której jest publikowany.
* connectionString: parametry połączenia dla konta magazynu, w którym znajduje się kolejka magazynu.

    >[!NOTE]
    > Nieliniowe Event Hubs, kolejki Service Bus i tematy Service Bus, parametry połączenia używane dla kolejek magazynu nie są specyficzne dla jednostki. Zamiast tego należy użyć parametrów połączenia dla konta magazynu.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "storageQueue",
              "properties": {
                "queueName": "<your-storage-queue-name>",
                "connectionString": "<your-storage-account-connection-string>"
              }
            }
          }
        }
    ```