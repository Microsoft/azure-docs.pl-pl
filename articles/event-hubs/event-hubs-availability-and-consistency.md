---
title: Dostępność i spójność — Event Hubs platformy Azure | Microsoft Docs
description: Jak zapewnić maksymalną ilość dostępności i spójność za pomocą usługi Azure Event Hubs przy użyciu partycji.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 81bacd5507396352bb814310979498234ee35347
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96902905"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Availability and consistency in Event Hubs (Dostępność i spójność w usłudze Event Hubs)

## <a name="overview"></a>Omówienie
Usługa Azure Event Hubs używa [modelu partycjonowania](event-hubs-scalability.md#partitions) , aby zwiększyć dostępność i przetwarzanie równoległe w ramach jednego centrum zdarzeń. Na przykład jeśli centrum zdarzeń ma cztery partycje, a jedna z nich jest przenoszona z jednego serwera do drugiego w operacji równoważenia obciążenia, można nadal wysyłać i odbierać z trzech innych partycji. Ponadto posiadanie większej liczby partycji pozwala na przetworzenie danych przez większą liczbę czytelników i zwiększenie zagregowanej przepływności. Zrozumienie skutków partycjonowania i uporządkowania w systemie rozproszonym jest krytycznym aspektem projektu rozwiązania.

Aby ułatwić wyjaśnienie handlu między porządkowaniem i dostępnością, zobacz [theorem Cap](https://en.wikipedia.org/wiki/CAP_theorem), znany również jako theorem Brewer. Ten theorem omawia wybór między opcją spójności, dostępności i tolerancją partycji. W przypadku systemów partycjonowanych przez sieć zawsze występuje kompromis między spójnością i dostępnością.

Brewer theorem definiuje spójność i dostępność w następujący sposób:
* Tolerancja partycji: możliwość systemu przetwarzania danych kontynuuje przetwarzanie danych, nawet jeśli wystąpi awaria partycji.
* Dostępność: węzeł niepowodujący awarii zwraca rozsądną odpowiedź w rozsądnym czasie (bez błędów lub przekroczeń limitu czasu).
* Spójność: Odczyt jest gwarantowany do zwrócenia najnowszej zapis dla danego klienta.

## <a name="partition-tolerance"></a>Tolerancja partycji
Event Hubs jest tworzona na podstawie partycjonowanego modelu danych. Można skonfigurować liczbę partycji w centrum zdarzeń podczas instalacji, ale nie można zmienić tej wartości później. Ze względu na to, że należy używać partycji z Event Hubs, należy podjąć decyzję dotyczącą dostępności i spójności aplikacji.

## <a name="availability"></a>Dostępność
Najprostszym sposobem na rozpoczęcie pracy z Event Hubs jest użycie zachowania domyślnego. 

#### <a name="azuremessagingeventhubs-500-or-later"></a>[Azure. Messaging. EventHubs (5.0.0 lub nowszy)](#tab/latest)
Jeśli utworzysz nowy obiekt **[EventHubProducerClient](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient?view=azure-dotnet)** i użyjesz metody **[SendAsync](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient.sendasync?view=azure-dotnet)** , zdarzenia są automatycznie dystrybuowane między partycjami w centrum zdarzeń. To zachowanie umożliwia największą ilość czasu.

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[Microsoft. Azure. EventHubs (4.1.0 lub starszy)](#tab/old)
W przypadku utworzenia nowego obiektu **[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient)** i użycia metody **[send](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync?view=azure-dotnet#Microsoft_Azure_EventHubs_EventHubClient_SendAsync_Microsoft_Azure_EventHubs_EventData_)** zdarzenia są automatycznie dystrybuowane między partycjami w centrum zdarzeń. To zachowanie umożliwia największą ilość czasu.

---

Dla przypadków użycia, które wymagają maksymalnego czasu, preferowany jest ten model.

## <a name="consistency"></a>Spójność
W niektórych scenariuszach kolejność zdarzeń może być ważna. Na przykład możesz chcieć, aby system zaplecza przetworzył polecenie aktualizacji przed poleceniem usuwania. W tym przypadku można ustawić klucz partycji dla zdarzenia lub użyć `PartitionSender` obiektu (Jeśli używasz starej biblioteki Microsoft. Azure. Messaging Library) do wysyłania tylko zdarzeń do określonej partycji. Dzięki temu w przypadku odczytywania tych zdarzeń z partycji są one odczytywane w pożądanej kolejności. 

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

W przypadku tej konfiguracji należy pamiętać, że jeśli określona partycja, do której jest wysyłana, jest niedostępna, zostanie wyświetlona odpowiedź na błąd. Jako punkt porównania, jeśli nie masz koligacji z jedną partycją, usługa Event Hubs wysyła zdarzenie do następnej dostępnej partycji.

Jednym z możliwych rozwiązań do zapewnienia uporządkowania, a jednocześnie zmaksymalizowania czasu, byłoby zaagregowanie zdarzeń w ramach aplikacji do przetwarzania zdarzeń. Najprostszym sposobem na to jest sygnatura zdarzenia z właściwością niestandardowego numeru sekwencji. Poniżej znajduje się kod przykładowy:

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
