---
title: Dostępność i spójność — Event Hubs platformy Azure | Microsoft Docs
description: Jak zapewnić maksymalną ilość dostępności i spójność za pomocą usługi Azure Event Hubs przy użyciu partycji.
ms.topic: article
ms.date: 01/25/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 325cc80daba2a44dedbd5e09ac4858ae2815c1cd
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102425927"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Availability and consistency in Event Hubs (Dostępność i spójność w usłudze Event Hubs)
Ten artykuł zawiera informacje o dostępności i spójności obsługiwanej przez usługę Azure Event Hubs. 

## <a name="availability"></a>Dostępność
Platforma Azure Event Hubs rozprzestrzeniać się ryzyka katastrofalnych awarii poszczególnych maszyn, a nawet kompletnych stojaków w klastrach obejmujących wiele domen awarii w centrum danych. Implementuje ona przejrzyste mechanizmy wykrywania awarii i trybu failover, dzięki czemu usługa będzie kontynuowała pracę w ramach gwarantowanych poziomów usług i zwykle bez zauważalnych przerw w przypadku wystąpienia błędów. 

Jeśli Event Hubs przestrzeń nazw została włączona z włączonymi [strefami dostępności](../availability-zones/az-overview.md) , ryzyko przestoju jest dodatkowo rozłożone na trzy fizycznie oddzielone urządzenia, a usługa ma wystarczającą ilość rezerw, aby szybko uzyskać dostęp do kompletnej, krytycznej utraty całego obiektu. Aby uzyskać więcej informacji, zobacz [Azure Event Hubs — odzyskiwanie geograficznego odzyskiwania po awarii](event-hubs-geo-dr.md).

Gdy aplikacja kliencka wysyła zdarzenia do centrum zdarzeń bez określania partycji, zdarzenia są automatycznie dystrybuowane między partycjami w centrum zdarzeń. Jeśli z jakiegoś powodu partycja nie jest dostępna, zdarzenia są dystrybuowane między pozostałe partycje. To zachowanie umożliwia największą ilość czasu. W przypadku przypadków użycia, które wymagają maksymalnego czasu, ten model jest preferowany zamiast wysyłania zdarzeń do określonej partycji. 

### <a name="availability-considerations-when-using-a-partition-id-or-key"></a>Zagadnienia dotyczące dostępności w przypadku używania identyfikatora lub klucza partycji
Użycie identyfikatora partycji lub klucza partycji jest opcjonalne. Należy uważnie rozważyć, czy należy używać jednego z nich. Jeśli nie określisz identyfikatora/klucza partycji podczas publikowania zdarzenia, Event Hubs zrównoważy obciążenie między partycjami. W przypadku używania identyfikatora partycji/klucza partycje te wymagają dostępności w jednym węźle, a przerwy mogą wystąpić w czasie. Na przykład może być konieczne ponowne uruchomienie lub zainstalowanie węzłów obliczeniowych. Dlatego jeśli ustawisz identyfikator partycji/klucz i ta partycja będzie niedostępna z jakiegoś powodu, próba uzyskania dostępu do danych w tej partycji zakończy się niepowodzeniem. Jeśli wysoka dostępność jest najważniejsza, nie określaj identyfikatora ani klucza partycji. W takim przypadku zdarzenia są wysyłane do partycji przy użyciu wewnętrznego algorytmu równoważenia obciążenia. W tym scenariuszu wprowadzasz jawny wybór między dostępnością (bez identyfikatora partycji/klucza) i spójności (Przypinanie zdarzeń do określonej partycji). Użycie identyfikatora partycji/klucza obniża dostępność centrum zdarzeń do poziomu partycji. 

### <a name="availability-considerations-when-handling-delays-in-processing-events"></a>Zagadnienia dotyczące dostępności w przypadku opóźnień w przetwarzaniu zdarzeń
Innym zagadnieniem jest założenie, że klient będzie obsługiwał opóźnienia w przetwarzaniu zdarzeń. W niektórych przypadkach lepszym rozwiązaniem może być porzucanie danych przez aplikację konsumenta i ponawianie prób zamiast konieczności przeprowadzenia przetwarzania, co może potencjalnie spowodować opóźnienia przetwarzania podrzędnego. Na przykład w przypadku grafu giełdowego lepszym rozwiązaniem jest zaczekanie na pełne dane, ale w przypadku rozmowy na żywo lub w scenariuszu korzystającym z technologii VOIP dane są szybko dostępne nawet wtedy, gdy nie są kompletne.

Biorąc pod uwagę te zagadnienia dotyczące dostępności, w tych scenariuszach aplikacja konsumencka może wybrać jedną z następujących strategii obsługi błędów:

- Zatrzymaj (Zatrzymywanie odczytywania z centrum zdarzeń do momentu rozwiązania problemów)
- Drop (komunikaty nie są ważne, upuść je)
- Ponów próbę (ponów próbę wykonania komunikatów)


## <a name="consistency"></a>Spójność
W niektórych scenariuszach kolejność zdarzeń może być ważna. Na przykład możesz chcieć, aby system zaplecza przetworzył polecenie aktualizacji przed poleceniem usuwania. W tym scenariuszu aplikacja kliencka wysyła zdarzenia do określonej partycji, dzięki czemu kolejność jest zachowywana. Gdy aplikacja konsumenta zużywa te zdarzenia z partycji, są one odczytywane w kolejności. 

W przypadku tej konfiguracji należy pamiętać, że jeśli określona partycja, do której jest wysyłana, jest niedostępna, zostanie wyświetlona odpowiedź na błąd. W ramach porównania, jeśli nie masz koligacji z jedną partycją, usługa Event Hubs wysyła zdarzenie do następnej dostępnej partycji.


## <a name="appendix"></a>Dodatek

### <a name="send-events-to-a-specific-partition"></a>Wysyłanie zdarzeń do określonej partycji
W tej sekcji pokazano, jak wysyłać zdarzenia do określonej partycji za pomocą języków C#, Java, Python i JavaScript. 

### <a name="net"></a>[.NET](#tab/dotnet)
Pełny przykładowy kod, który pokazuje, jak wysłać partię zdarzeń do centrum zdarzeń (bez ustawiania identyfikatora partycji/klucza), zobacz [wysyłanie zdarzeń do i odbieranie zdarzeń z platformy Azure Event Hubs — .NET (Azure. Messaging. EventHubs)](event-hubs-dotnet-standard-getstarted-send.md).

Aby wysłać zdarzenia do określonej partycji, należy utworzyć partię przy użyciu metody [EventHubProducerClient. CreateBatchAsync](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient.createbatchasync#Azure_Messaging_EventHubs_Producer_EventHubProducerClient_CreateBatchAsync_Azure_Messaging_EventHubs_Producer_CreateBatchOptions_System_Threading_CancellationToken_) , określając albo parametr `PartitionId` `PartitionKey` in [CreateBatchOptions](//dotnet/api/azure.messaging.eventhubs.producer.createbatchoptions). Poniższy kod wysyła partię zdarzeń do określonej partycji przez określenie klucza partycji. 

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
Pełny przykładowy kod, który pokazuje, jak wysłać partię zdarzeń do centrum zdarzeń (bez ustawiania identyfikatora partycji/klucza), zobacz [Używanie języka Java do wysyłania zdarzeń do usługi azure Event Hubs (Azure-Messaging-eventhubs)](event-hubs-java-get-started-send.md).

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
Pełny przykładowy kod, który pokazuje, jak wysłać partię zdarzeń do centrum zdarzeń (bez ustawiania identyfikatora partycji/klucza), zobacz [wysyłanie zdarzeń do lub odbieranie zdarzeń z centrów zdarzeń przy użyciu języka Python (Azure-eventhub)](event-hubs-python-get-started-send.md).

Aby wysłać zdarzenia do określonej partycji, podczas tworzenia partii przy użyciu [`EventHubProducerClient.create_batch`](/python/api/azure-eventhub/azure.eventhub.eventhubproducerclient#create-batch---kwargs-) metody należy określić `partition_id` lub `partition_key` . Następnie użyj metody, [`EventHubProducerClient.send_batch`](/python/api/azure-eventhub/azure.eventhub.aio.eventhubproducerclient#send-batch-event-data-batch--typing-union-azure-eventhub--common-eventdatabatch--typing-list-azure-eventhub-) Aby wysłać partię do partycji centrum zdarzeń. 

```python
event_data_batch = await producer.create_batch(partition_key='cities')
```

Można również użyć metody [EventHubProducerClient.send_batch](/python/api/azure-eventhub/azure.eventhub.eventhubproducerclient#send-batch-event-data-batch----kwargs-) , określając wartości `partition_id` `partition_key` parametrów lub.

```python
producer.send_batch(event_data_batch, partition_key="cities")
```


### <a name="javascript"></a>[JavaScript](#tab/javascript)
Pełny przykładowy kod, który pokazuje, jak wysłać partię zdarzeń do centrum zdarzeń (bez ustawiania identyfikatora partycji/klucza), zobacz [wysyłanie zdarzeń do i odbieranie zdarzeń z centrów zdarzeń przy użyciu języka JavaScript (Azure/Event-Hubs)](event-hubs-node-get-started-send.md).

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

* [Przegląd usługi Event Hubs](./event-hubs-about.md)
* [Tworzenie centrum zdarzeń](event-hubs-create.md)
