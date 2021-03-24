---
title: Dodawanie niestandardowych danych do zdarzeń w usłudze Azure Event Hubs
description: W tym artykule opisano sposób dodawania niestandardowych danych do zdarzeń w usłudze Azure Event Hubs.
ms.topic: how-to
ms.date: 03/19/2021
ms.openlocfilehash: 3362b6ac4b0d624969aa3ba36d2ebc83b8777cf5
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104893479"
---
# <a name="add-custom-data-to-events-in-azure-event-hubs"></a>Dodawanie niestandardowych danych do zdarzeń w usłudze Azure Event Hubs
Ze względu na to, że zdarzenie składa się głównie z nieprzezroczystego zestawu bajtów, może być trudne dla użytkowników tych zdarzeń, aby podejmować świadome decyzje dotyczące sposobu ich przetwarzania. Aby zezwolić wydawcom zdarzeń na oferowanie lepszego kontekstu dla odbiorców, zdarzenia mogą również zawierać niestandardowe metadane w postaci zestawu par klucz-wartość. Typowym scenariuszem dołączenia metadanych jest zapewnienie wskazówki dotyczącej typu danych zawartych w zdarzeniu, dzięki czemu konsumenci rozumieją swój format i mogą odpowiednio deserializować.

> [!NOTE]
> Te metadane nie są używane przez program lub w jakikolwiek sposób zrozumiały dla usługi Event Hubs. Istnieje tylko w przypadku koordynacji między wydawcami zdarzeń i użytkownikami. 

W poniższych sekcjach pokazano, jak dodać niestandardowe dane do zdarzeń w różnych językach programowania. 

## <a name="net"></a>.NET 

```csharp
var eventBody = new BinaryData("Hello, Event Hubs!");
var eventData = new EventData(eventBody);
eventData.Properties.Add("EventType", "com.microsoft.samples.hello-event");
eventData.Properties.Add("priority", 1);
eventData.Properties.Add("score", 9.0);
```

Aby uzyskać pełny przykład kodu, zobacz [Publikowanie zdarzeń za pomocą metadanych niestandardowych](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample04_PublishingEvents.md#publishing-events-with-custom-metadata).

## <a name="java"></a>Java

```java
EventData firstEvent = new EventData("EventData Sample 1".getBytes(UTF_8));
firstEvent.getProperties().put("EventType", "com.microsoft.samples.hello-event");
firstEvent.getProperties().put("priority", 1);
firstEvent.getProperties().put("score", 9.0);
```

Aby uzyskać pełny przykład kodu, zobacz [Publikowanie zdarzeń za pomocą metadanych niestandardowych](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs/PublishEventsWithCustomMetadata.java).


## <a name="python"></a>Python

```python
event_data = EventData('Message with properties')
event_data.properties = {'event-type': 'com.microsoft.samples.hello-event', 'priority': 1, "score": 9.0}
```

Aby uzyskać pełny przykład kodu, zobacz [wysyłanie wsadowe danych zdarzeń z właściwościami](https://github.com/Azure/azure-sdk-for-python/blob/azure-eventhub_5.3.1/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py).

## <a name="javascript"></a>JavaScript

```javascript
let eventData = { body: "First event", properties: { "event-type": "com.microsoft.samples.hello-event", "priority": 1, "score": 9.0  } };
```


## <a name="next-steps"></a>Następne kroki
Zobacz następujące Przewodniki Szybki Start i przykłady. 

- Przewodniki Szybki Start: [.NET](event-hubs-dotnet-standard-getstarted-send.md), [Java](event-hubs-java-get-started-send.md), [Python](event-hubs-python-get-started-send.md), [JavaScript](event-hubs-node-get-started-send.md)
- Przykłady w witrynie GitHub: [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples), [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples), [Python](https://github.com/Azure/azure-sdk-for-python/blob/azure-eventhub_5.3.1/sdk/eventhub/azure-eventhub/samples), [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript), [TypeScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
