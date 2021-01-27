---
title: Dostępność i spójność — Event Hubs platformy Azure | Microsoft Docs
description: Jak zapewnić maksymalną ilość dostępności i spójność za pomocą usługi Azure Event Hubs przy użyciu partycji.
ms.topic: article
ms.date: 01/25/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 2fdb62e953230a38a26d22e136789fea52c8ee8c
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98882199"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Availability and consistency in Event Hubs (Dostępność i spójność w usłudze Event Hubs)
Ten artykuł zawiera informacje o dostępności i spójności obsługiwanej przez usługę Azure Event Hubs. 

## <a name="availability"></a>Dostępność
Platforma Azure Event Hubs rozprzestrzeniać się ryzyka katastrofalnych awarii poszczególnych maszyn, a nawet kompletnych stojaków w klastrach obejmujących wiele domen awarii w centrum danych. Implementuje ona przejrzyste mechanizmy wykrywania awarii i trybu failover, dzięki czemu usługa będzie kontynuowała pracę w ramach gwarantowanych poziomów usług i zwykle bez zauważalnych przerw w przypadku wystąpienia błędów. Jeśli Event Hubs przestrzeń nazw została utworzona z włączoną opcją dla [stref dostępności](../availability-zones/az-overview.md), ryzyko przestoju jest dodatkowo rozłożone na trzy fizycznie oddzielone urządzenia, a usługa ma wystarczającą ilość rezerw, aby szybko sprostać całkowitej, krytycznej utracie całego obiektu. Aby uzyskać więcej informacji, zobacz [Azure Event Hubs — odzyskiwanie geograficznego odzyskiwania po awarii](event-hubs-geo-dr.md).

Gdy aplikacja kliencka wysyła zdarzenia do centrum zdarzeń, zdarzenia są automatycznie dystrybuowane między partycjami w centrum zdarzeń. Jeśli z jakiegoś powodu partycja nie jest dostępna, zdarzenia są dystrybuowane między pozostałe partycje. To zachowanie umożliwia największą ilość czasu. W przypadku przypadków użycia, które wymagają maksymalnego czasu, ten model jest preferowany zamiast wysyłania zdarzeń do określonej partycji. Aby uzyskać więcej informacji, zobacz [partycje](event-hubs-scalability.md#partitions).

## <a name="consistency"></a>Spójność
W niektórych scenariuszach kolejność zdarzeń może być ważna. Na przykład możesz chcieć, aby system zaplecza przetworzył polecenie aktualizacji przed poleceniem usuwania. W tym scenariuszu aplikacja kliencka wysyła zdarzenia do określonej partycji, dzięki czemu kolejność jest zachowywana. Gdy aplikacja konsumenta zużywa te zdarzenia z partycji, są one odczytywane w kolejności. 

W przypadku tej konfiguracji należy pamiętać, że jeśli określona partycja, do której jest wysyłana, jest niedostępna, zostanie wyświetlona odpowiedź na błąd. W ramach porównania, jeśli nie masz koligacji z jedną partycją, usługa Event Hubs wysyła zdarzenie do następnej dostępnej partycji.

Jednym z możliwych rozwiązań do zapewnienia uporządkowania, a jednocześnie zmaksymalizowania czasu, byłoby zaagregowanie zdarzeń w ramach aplikacji do przetwarzania zdarzeń. Najprostszym sposobem na jej wykonanie jest sygnatura zdarzenia z właściwością niestandardowego numeru sekwencji.

W tym scenariuszu klient produkcyjny wysyła zdarzenia do jednej z dostępnych partycji w centrum zdarzeń i ustawia odpowiedni numer sekwencyjny z aplikacji. To rozwiązanie wymaga zachowania stanu przez aplikację do przetwarzania, ale daje nadawcom punkt końcowy, który jest bardziej prawdopodobnie dostępny.

## <a name="appendix"></a>Dodatek

### <a name="net-examples"></a>Przykłady dla platformy .NET

#### <a name="send-events-to-a-specific-partition"></a>Wysyłanie zdarzeń do określonej partycji
Ustaw klucz partycji dla zdarzenia lub Użyj `PartitionSender` obiektu (Jeśli używasz starej biblioteki Microsoft. Azure. Messaging) do wysyłania tylko zdarzeń do określonej partycji. Dzięki temu w przypadku odczytywania tych zdarzeń z partycji są one odczytywane w pożądanej kolejności. 

Jeśli używasz nowszej biblioteki **Azure. Messaging. EventHubs** , zobacz [Migrowanie kodu z PartitionSender do EventHubProducerClient w celu publikowania zdarzeń na partycji](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md#migrating-code-from-partitionsender-to-eventhubproducerclient-for-publishing-events-to-a-partition).

#### <a name="azuremessagingeventhubs-500-or-later"></a>[Azure. Messaging. EventHubs (5.0.0 lub nowszy)](#tab/latest)

```csharp
var connectionString = "<< CONNECTION STRING FOR THE EVENT HUBS NAMESPACE >>";
var eventHubName = "<< NAME OF THE EVENT HUB >>";

await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
{
    var batchOptions = new CreateBatchOptions() { PartitionId = "my-partition-id" };
    using EventDataBatch eventBatch = await producerClient.CreateBatchAsync(batchOptions);
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("First")));
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Second")));
    
    await producerClient.SendAsync(eventBatch);
}
```

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[Microsoft. Azure. EventHubs (4.1.0 lub starszy)](#tab/old)

```csharp
var connectionString = "<< CONNECTION STRING FOR THE EVENT HUBS NAMESPACE >>";
var eventHubName = "<< NAME OF THE EVENT HUB >>";

var connectionStringBuilder = new EventHubsConnectionStringBuilder(connectionString){ EntityPath = eventHubName }; 
var eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
PartitionSender partitionSender = eventHubClient.CreatePartitionSender("my-partition-id");
try
{
    EventDataBatch eventBatch = partitionSender.CreateBatch();
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("First")));
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Second")));

    await partitionSender.SendAsync(eventBatch);
}
finally
{
    await partitionSender.CloseAsync();
    await eventHubClient.CloseAsync();
}
```

---

### <a name="set-a-sequence-number"></a>Ustaw numer sekwencyjny
Poniższy przykład oznacza sygnaturę zdarzenia za pomocą niestandardowej właściwości numeru sekwencji. 

#### <a name="azuremessagingeventhubs-500-or-later"></a>[Azure. Messaging. EventHubs (5.0.0 lub nowszy)](#tab/latest)

```csharp
// create a producer client that you can use to send events to an event hub
await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
{
    // get the latest sequence number from your application
    var sequenceNumber = GetNextSequenceNumber();

    // create a batch of events 
    using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

    // create a new EventData object by encoding a string as a byte array
    var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));

    // set a custom sequence number property
    data.Properties.Add("SequenceNumber", sequenceNumber);

    // add events to the batch. An event is a represented by a collection of bytes and metadata. 
    eventBatch.TryAdd(data);

    // use the producer client to send the batch of events to the event hub
    await producerClient.SendAsync(eventBatch);
}
```

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[Microsoft. Azure. EventHubs (4.1.0 lub starszy)](#tab/old)
```csharp
// Create an Event Hubs client
var client = new EventHubClient(connectionString, eventHubName);

//Create a producer to produce events
EventHubProducer producer = client.CreateProducer();

// Get the latest sequence number from your application 
var sequenceNumber = GetNextSequenceNumber();

// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));

// Set a custom sequence number property
data.Properties.Add("SequenceNumber", sequenceNumber);

// Send single message async
await producer.SendAsync(data);
```
---

Ten przykład wysyła zdarzenie do jednej z dostępnych partycji w centrum zdarzeń i ustawia odpowiedni numer sekwencyjny z aplikacji. To rozwiązanie wymaga zachowania stanu przez aplikację do przetwarzania, ale daje nadawcom punkt końcowy, który jest bardziej prawdopodobnie dostępny.

## <a name="next-steps"></a>Następne kroki
Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

* [Przegląd usługi Event Hubs](./event-hubs-about.md)
* [Tworzenie centrum zdarzeń](event-hubs-create.md)
