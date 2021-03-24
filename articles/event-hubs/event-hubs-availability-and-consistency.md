---
title: Dostępność i spójność — Event Hubs platformy Azure | Microsoft Docs
description: Jak zapewnić maksymalną ilość dostępności i spójność za pomocą usługi Azure Event Hubs przy użyciu partycji.
ms.topic: article
ms.date: 03/15/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: e014a33e94fe7f90569dd2ef1e9b620eef274842
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952868"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Availability and consistency in Event Hubs (Dostępność i spójność w usłudze Event Hubs)
Ten artykuł zawiera informacje o dostępności i spójności obsługiwanej przez usługę Azure Event Hubs. 

## <a name="availability"></a>Dostępność
Platforma Azure Event Hubs rozprzestrzeniać się ryzyka katastrofalnych awarii poszczególnych maszyn, a nawet kompletnych stojaków w klastrach obejmujących wiele domen awarii w centrum danych. Implementuje ona przejrzyste mechanizmy wykrywania awarii i trybu failover, dzięki czemu usługa będzie kontynuowała pracę w ramach gwarantowanych poziomów usług i zwykle bez zauważalnych przerw w przypadku wystąpienia błędów. 

Jeśli Event Hubs przestrzeń nazw została włączona z włączonymi [strefami dostępności](../availability-zones/az-overview.md) , ryzyko przestoju jest dodatkowo rozłożone na trzy fizycznie oddzielone urządzenia, a usługa ma wystarczającą ilość rezerw, aby szybko uzyskać dostęp do kompletnej, krytycznej utraty całego obiektu. Aby uzyskać więcej informacji, zobacz [Azure Event Hubs — odzyskiwanie geograficznego odzyskiwania po awarii](event-hubs-geo-dr.md).

Gdy aplikacja kliencka wysyła zdarzenia do centrum zdarzeń bez określania partycji, zdarzenia są automatycznie dystrybuowane między partycjami w centrum zdarzeń. Jeśli z jakiegoś powodu partycja nie jest dostępna, zdarzenia są dystrybuowane między pozostałe partycje. To zachowanie umożliwia największą ilość czasu. W przypadku przypadków użycia, które wymagają maksymalnego czasu, ten model jest preferowany zamiast wysyłania zdarzeń do określonej partycji. 

## <a name="consistency"></a>Spójność
W niektórych scenariuszach kolejność zdarzeń może być ważna. Na przykład możesz chcieć, aby system zaplecza przetworzył polecenie aktualizacji przed poleceniem usuwania. W tym scenariuszu aplikacja kliencka wysyła zdarzenia do określonej partycji, dzięki czemu kolejność jest zachowywana. Gdy aplikacja konsumenta zużywa te zdarzenia z partycji, są one odczytywane w kolejności. 

W przypadku tej konfiguracji należy pamiętać, że jeśli określona partycja, do której jest wysyłana, jest niedostępna, zostanie wyświetlona odpowiedź na błąd. W ramach porównania, jeśli nie masz koligacji z jedną partycją, usługa Event Hubs wysyła zdarzenie do następnej dostępnej partycji.

W związku z tym, jeśli wysoka dostępność jest najważniejsza, nie należy kierować konkretną partycją (przy użyciu identyfikatora partycji/klucza). Użycie identyfikatora partycji/klucza obniża dostępność centrum zdarzeń do poziomu partycji. W tym scenariuszu wprowadzasz jawny wybór między dostępnością (bez identyfikatora partycji/klucza) i spójności (Przypinanie zdarzeń do określonej partycji). Aby uzyskać szczegółowe informacje na temat partycji w Event Hubs, zobacz [Partitions](event-hubs-features.md#partitions).

## <a name="appendix"></a>Dodatek

### <a name="send-events-without-specifying-a-partition"></a>Wysyłanie zdarzeń bez określania partycji
Zalecamy wysyłanie zdarzeń do centrum zdarzeń bez ustawiania informacji o partycji, aby umożliwić usłudze Event Hubs zrównoważenie obciążenia między partycjami. Zobacz następujące Przewodniki Szybki Start, aby dowiedzieć się, jak to zrobić w różnych językach programowania. 

- [Wysyłanie zdarzeń przy użyciu platformy .NET](event-hubs-dotnet-standard-getstarted-send.md)
- [Wysyłanie zdarzeń przy użyciu języka Java](event-hubs-java-get-started-send.md)
- [Wysyłanie zdarzeń przy użyciu języka JavaScript](event-hubs-python-get-started-send.md)
- [Wysyłanie zdarzeń za pomocą języka Python](event-hubs-python-get-started-send.md)


### <a name="send-events-to-a-specific-partition"></a>Wysyłanie zdarzeń do określonej partycji
W tej sekcji dowiesz się, jak wysyłać zdarzenia do określonej partycji przy użyciu różnych języków programowania. 

### <a name="net"></a>[.NET](#tab/dotnet)
Aby wysłać zdarzenia do określonej partycji, należy utworzyć partię przy użyciu metody [EventHubProducerClient. CreateBatchAsync](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient.createbatchasync#Azure_Messaging_EventHubs_Producer_EventHubProducerClient_CreateBatchAsync_Azure_Messaging_EventHubs_Producer_CreateBatchOptions_System_Threading_CancellationToken_) , określając albo parametr `PartitionId` `PartitionKey` in [CreateBatchOptions](/dotnet/api/azure.messaging.eventhubs.producer.createbatchoptions). Poniższy kod wysyła partię zdarzeń do określonej partycji przez określenie klucza partycji. Event Hubs zapewnia, że wszystkie zdarzenia współużytkujące wartość klucza partycji są przechowywane razem i dostarczane w kolejności przybycia.

```csharp
var batchOptions = new CreateBatchOptions { PartitionKey = "cities" };
using var eventBatch = await producer.CreateBatchAsync(batchOptions);
```

Można również użyć metody [EventHubProducerClient. SendAsync](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient.sendasync#Azure_Messaging_EventHubs_Producer_EventHubProducerClient_SendAsync_System_Collections_Generic_IEnumerable_Azure_Messaging_EventHubs_EventData__Azure_Messaging_EventHubs_Producer_SendEventOptions_System_Threading_CancellationToken_) , określając wartość **PartitionID** lub **PartitionKey** w [SendEventOptions](/dotnet/api/azure.messaging.eventhubs.producer.sendeventoptions).

```csharp
var sendEventOptions  = new SendEventOptions { PartitionKey = "cities" };
// create the events array
producer.SendAsync(events, sendOptions)
```


### <a name="java"></a>[Java](#tab/java)
Aby wysłać zdarzenia do określonej partycji, należy utworzyć partię przy użyciu [metody](/java/api/com.azure.messaging.eventhubs.eventhubproducerclient.createbatch) CreatePartition przez określenie **identyfikatora partycji** lub **klucza partycji** w [createBatchOptions](/java/api/com.azure.messaging.eventhubs.models.createbatchoptions). Poniższy kod wysyła partię zdarzeń do określonej partycji przez określenie klucza partycji. 

```java
CreateBatchOptions batchOptions = new CreateBatchOptions();
batchOptions.setPartitionKey("cities");
```

Można również użyć metody [EventHubProducerClient. Send](/java/api/com.azure.messaging.eventhubs.eventhubproducerclient.send#com_azure_messaging_eventhubs_EventHubProducerClient_send_java_lang_Iterable_com_azure_messaging_eventhubs_EventData__com_azure_messaging_eventhubs_models_SendOptions_) , określając **identyfikator partycji** lub **klucz partycji** w [SendOptions](/java/api/com.azure.messaging.eventhubs.models.sendoptions).

```java
List<EventData> events = Arrays.asList(new EventData("Melbourne"), new EventData("London"), new EventData("New York"));
SendOptions sendOptions = new SendOptions();
sendOptions.setPartitionKey("cities");
producer.send(events, sendOptions);
```


### <a name="python"></a>[Python](#tab/python) 
Aby wysłać zdarzenia do określonej partycji, podczas tworzenia partii przy użyciu [`EventHubProducerClient.create_batch`](/python/api/azure-eventhub/azure.eventhub.eventhubproducerclient#create-batch---kwargs-) metody należy określić `partition_id` lub `partition_key` . Następnie użyj metody, [`EventHubProducerClient.send_batch`](/python/api/azure-eventhub/azure.eventhub.aio.eventhubproducerclient#send-batch-event-data-batch--typing-union-azure-eventhub--common-eventdatabatch--typing-list-azure-eventhub-) Aby wysłać partię do partycji centrum zdarzeń. 

```python
event_data_batch = await producer.create_batch(partition_key='cities')
```

Można również użyć metody [EventHubProducerClient.send_batch](/python/api/azure-eventhub/azure.eventhub.eventhubproducerclient#send-batch-event-data-batch----kwargs-) , określając wartości `partition_id` `partition_key` parametrów lub.

```python
producer.send_batch(event_data_batch, partition_key="cities")
```

### <a name="javascript"></a>[JavaScript](#tab/javascript)
Aby wysłać zdarzenia do określonej partycji, [Utwórz partię](/javascript/api/@azure/event-hubs/eventhubproducerclient#createBatch_CreateBatchOptions_) przy użyciu obiektu [EventHubProducerClient. CreateBatchOptions](/javascript/api/@azure/event-hubs/eventhubproducerclient#createBatch_CreateBatchOptions_) , określając `partitionId` lub `partitionKey` . Następnie Wyślij partię do centrum zdarzeń za pomocą metody [EventHubProducerClient. SendBatch](/javascript/api/@azure/event-hubs/eventhubproducerclient#sendBatch_EventDataBatch__OperationOptions_) . 

Zobacz poniższy przykład.

```javascript
const batchOptions = { partitionKey = "cities"; };
const batch = await producer.createBatch(batchOptions);
```

Można również użyć metody [EventHubProducerClient. sendBatch](/javascript/api/@azure/event-hubs/eventhubproducerclient#sendBatch_EventData____SendBatchOptions_) , określając **identyfikator partycji** lub **klucz partycji** w [SendBatchOptions](/javascript/api/@azure/event-hubs/sendbatchoptions).

```javascript
const sendBatchOptions = { partitionKey = "cities"; };
// prepare events
producer.sendBatch(events, sendBatchOptions);
```

---



## <a name="next-steps"></a>Następne kroki
Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

- [Przegląd usługi Event Hubs](./event-hubs-about.md)
- [Terminologia usługi Event Hubs](event-hubs-features.md)
