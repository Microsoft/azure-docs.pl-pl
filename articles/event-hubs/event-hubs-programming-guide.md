---
title: Przewodnik programowania .NET — Azure Event Hubs (starsza wersja) | Microsoft Docs
description: Ten artykuł zawiera informacje dotyczące sposobu pisania kodu dla platformy Azure Event Hubs przy użyciu zestawu Azure .NET SDK.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: a299813620ee90591d8c9491991237f75f2e9382
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98623052"
---
# <a name="net-programming-guide-for-azure-event-hubs-legacy-microsoftazureeventhubs-package"></a>Przewodnik programowania .NET dla platformy Azure Event Hubs (starszy pakiet Microsoft. Azure. EventHubs)
W tym artykule omówiono niektóre typowe scenariusze tworzenia kodu przy użyciu usługi Azure Event Hubs. Przyjęto założenie, że wstępnie znasz i rozumiesz usługę Event Hubs. Omówienie koncepcji usługi Event Hubs można znaleźć w temacie [Przegląd usługi Event Hubs](./event-hubs-about.md).

> [!WARNING]
> Ten przewodnik jest przeznaczony dla starego pakietu **Microsoft. Azure. EventHubs** . Zalecamy [Migrowanie](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md) kodu do korzystania z najnowszego pakietu [Azure. Messaging. EventHubs](event-hubs-dotnet-standard-getstarted-send.md) .  


## <a name="event-publishers"></a>Wydawcy zdarzeń

Zdarzenia są wysyłane do centrum zdarzeń przy użyciu protokołu HTTP POST lub za pośrednictwem połączenia z AMQP 1,0. Wybór, który ma być używany i kiedy zależy od konkretnego scenariusza. Połączenia protokołu AMQP 1.0 są mierzone jako połączenia obsługiwane przez brokera w Service Bus i są bardziej odpowiednie w scenariuszach z częstymi większymi ilościami wiadomości oraz wymaganiami dotyczącymi krótszych opóźnień, ponieważ zapewniają trwały kanał obsługi komunikatów.

W przypadku używania zarządzanych interfejsów API platformy .NET głównymi konstrukcjami na potrzeby publikowania danych w usłudze Event Hubs są klasy [EventHubClient][] i [EventData][]. [EventHubClient][] udostępnia kanał komunikacji AMQP, przez który zdarzenia są wysyłane do centrum zdarzeń. Klasa [EVENTDATA][] reprezentuje zdarzenie i jest używana do publikowania komunikatów w centrum zdarzeń. Ta klasa obejmuje treść, niektóre metadane (właściwości) i informacje nagłówka (SystemProperties) dotyczące zdarzenia. Inne właściwości są dodawane do obiektu [EVENTDATA][] , gdy przechodzi on przez centrum zdarzeń.

## <a name="get-started"></a>Rozpoczęcie pracy
Klasy .NET obsługujące Event Hubs są dostępne w pakiecie NuGet [Microsoft. Azure. EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) . Program można zainstalować za pomocą Eksploratora rozwiązań programu Visual Studio lub [konsoli Menedżera pakietów](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) w programie Visual Studio. Aby to zrobić, należy wydać następujące polecenie w oknie [konsoli menedżera pakietów](https://docs.nuget.org/docs/start-here/using-the-package-manager-console):

```shell
Install-Package Microsoft.Azure.EventHubs
```

## <a name="create-an-event-hub"></a>Tworzenie centrum zdarzeń

Możesz użyć Azure Portal, Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, aby utworzyć Event Hubs. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie przestrzeni nazw Event Hubs i centrum zdarzeń przy użyciu Azure Portal](event-hubs-create.md).

## <a name="create-an-event-hubs-client"></a>Tworzenie klienta usługi Event Hubs

Podstawową klasą służącą do współdziałania z Event Hubs jest [Microsoft. Azure. EventHubs. EventHubClient][EventHubClient]. Można utworzyć wystąpienie tej klasy przy użyciu metody [CreateFromConnectionString](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createfromconnectionstring) , jak pokazano w następującym przykładzie:

```csharp
private const string EventHubConnectionString = "Event Hubs namespace connection string";
private const string EventHubName = "event hub name";

var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
{
    EntityPath = EventHubName

};
eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
```

## <a name="send-events-to-an-event-hub"></a>Wysyłanie zdarzeń do centrum zdarzeń

Zdarzenia są wysyłane do centrum zdarzeń przez utworzenie wystąpienia [EventHubClient][] i wysłanie go asynchronicznie za pomocą metody [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) . Ta metoda przyjmuje jeden parametr wystąpienia [EVENTDATA][] i asynchronicznie wysyła go do centrum zdarzeń.

## <a name="event-serialization"></a>Serializacja zdarzeń

Klasa [EVENTDATA][] ma [dwa przeciążone konstruktory](/dotnet/api/microsoft.azure.eventhubs.eventdata.-ctor) , które przyjmują wiele parametrów, bajtów lub tablicę bajtową, która reprezentuje ładunek danych zdarzenia. W przypadku używania formatu JSON z klasą [EventData][] można użyć funkcji **Encoding.UTF8.GetBytes()** do pobrania tablicy bajtowej dla ciągu zakodowanego w formacie JSON. Przykład:

```csharp
for (var i = 0; i < numMessagesToSend; i++)
{
    var message = $"Message {i}";
    Console.WriteLine($"Sending message: {message}");
    await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
}
```

## <a name="partition-key"></a>Klucz partycji

> [!NOTE]
> Jeśli nie znasz już partycji, zapoznaj się z [tym artykułem](event-hubs-features.md#partitions). 

Podczas wysyłania danych zdarzenia można określić wartość, która jest podzielona na dane, aby utworzyć przypisanie partycji. Partycję należy określić przy użyciu właściwości [PartitionSender. PartitionID](/dotnet/api/microsoft.azure.eventhubs.partitionsender.partitionid) . Jednak decyzja o użyciu partycji oznacza wybór między dostępnością i spójnością. 

### <a name="availability-considerations"></a>Zagadnienia dotyczące dostępności

Użycie klucza partycji jest opcjonalne i należy uważnie rozważyć, czy należy go używać. Jeśli nie określisz klucza partycji podczas publikowania zdarzenia, Event Hubs zrównoważy obciążenie między partycjami. W wielu przypadkach użycie klucza partycji jest dobrym rozwiązaniem, jeśli kolejność zdarzeń jest ważna. W przypadku korzystania z klucza partycji te partycje wymagają dostępności w jednym węźle, a przerwy mogą wystąpić w czasie; na przykład po ponownym uruchomieniu i zaprawieniu węzłów obliczeniowych. W związku z tym, jeśli ustawisz identyfikator partycji i ta partycja będzie niedostępna z jakiegoś powodu, próba uzyskania dostępu do danych w tej partycji zakończy się niepowodzeniem. Jeśli wysoka dostępność jest najważniejsza, nie określaj klucza partycji. W takim przypadku zdarzenia są wysyłane do partycji przy użyciu wewnętrznego algorytmu równoważenia obciążenia. W tym scenariuszu wprowadzasz jawny wybór między dostępnością (bez identyfikatora partycji) i spójnością (Przypinanie zdarzeń do identyfikatora partycji).

Inne zagadnienie obsługują opóźnienia w przetwarzaniu zdarzeń. W niektórych przypadkach lepszym rozwiązaniem może być porzucenie danych i ponawianie próby przeprowadzenia przetwarzania, co może potencjalnie spowodować opóźnienia przetwarzania podrzędnego. Na przykład w przypadku grafu giełdowego lepszym rozwiązaniem jest zaczekanie na pełne dane, ale w przypadku rozmowy na żywo lub w scenariuszu korzystającym z technologii VOIP dane są szybko dostępne nawet wtedy, gdy nie są kompletne.

Biorąc pod uwagę te zagadnienia dotyczące dostępności, w tych scenariuszach możesz wybrać jedną z następujących strategii obsługi błędów:

- Zatrzymaj (Zatrzymywanie odczytywania z Event Hubs, dopóki nie zostaną naprawione)
- Drop (komunikaty nie są ważne, upuść je)
- Ponów próbę (ponów próbę wykonania komunikatów)

Aby uzyskać więcej informacji i zapoznać się z informacjami o różnicach w zakresie dostępności i spójności, zobacz [dostępność i spójność w Event Hubs](event-hubs-availability-and-consistency.md). 

## <a name="batch-event-send-operations"></a>Operacje wysyłania partii zdarzeń

Wysyłanie zdarzeń w partiach może pomóc zwiększyć przepływność. Za pomocą interfejsu API tworzenia [wsadowego](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) można utworzyć partię, do której obiekty danych można później dodać do wywołania [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) .

Pojedyncza partia nie może przekroczyć limitu 1 MB zdarzenia. Ponadto każdy komunikat w partii używa tej samej tożsamości wydawcy. Nadawca jest odpowiedzialny za upewnienie się, że partia nie przekracza maksymalnego rozmiaru zdarzenia. Jeśli go przekroczy, zostanie wygenerowany błąd metody **Send** klienta. Możesz użyć metody pomocnika [EventHubClient. Getbatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) , aby upewnić się, że partia nie przekracza 1 MB. Otrzymujesz pustą [EventDataBatch](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch) z interfejsu API tworzenia [wsadowego](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) , a następnie użyjemy [TryAdd](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch.tryadd) , aby dodać zdarzenia w celu skonstruowania partii. 

## <a name="send-asynchronously-and-send-at-scale"></a>Wysyłanie asynchroniczne i wysyłanie na dużą skalę

Zdarzenia są wysyłane do centrum zdarzeń asynchronicznie. Asynchroniczne wysyłanie zwiększa szybkość, z jaką klient może wysyłać zdarzenia. [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) zwraca obiekt [zadania](/dotnet/api/system.threading.tasks.task) . Aby sterować opcjami ponowień klienta, można użyć klasy [RetryPolicy](/dotnet/api/microsoft.servicebus.retrypolicy) na kliencie.

## <a name="event-consumers"></a>Odbiorcy zdarzeń
Klasa [EventProcessorHost][] przetwarza dane z usługi Event Hubs. Podczas tworzenia czytników zdarzeń na platformie .NET należy używać tej implementacji. Klasa [EventProcessorHost][] udostępnia bezpieczne wątkowo, wieloprocesowe, bezpieczne środowisko uruchomieniowe dla implementacji procesora zdarzeń, które umożliwia także tworzenie punktów kontrolnych i zarządzanie dzierżawą partycji.

Aby używać klasy [EventProcessorHost][], można zaimplementować interfejs [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor). Ten interfejs zawiera cztery metody:

* [OpenAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.openasync)
* [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.closeasync)
* [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync)
* [ProcessErrorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processerrorasync)

Aby rozpocząć przetwarzanie zdarzeń, Utwórz wystąpienie [klasy eventprocessorhost][], dostarczając odpowiednie parametry dla centrum zdarzeń. Przykład:

> [!NOTE]
> Klasy eventprocessorhost i powiązane klasy są dostępne w pakiecie **Microsoft. Azure. EventHubs. Processor** . Dodaj pakiet do projektu programu Visual Studio, wykonując instrukcje podane w [tym artykule](event-hubs-dotnet-framework-getstarted-send.md#add-the-event-hubs-nuget-package) lub wykonując następujące polecenie w oknie [konsola Menedżera pakietów](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) : `Install-Package Microsoft.Azure.EventHubs.Processor` .

```csharp
var eventProcessorHost = new EventProcessorHost(
        EventHubName,
        PartitionReceiver.DefaultConsumerGroupName,
        EventHubConnectionString,
        StorageConnectionString,
        StorageContainerName);
```

Następnie zadzwoń do [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync) w celu zarejestrowania implementacji [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) przy użyciu środowiska uruchomieniowego:

```csharp
await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();
```

W tym momencie host próbuje uzyskać dzierżawę na każdą partycję w centrum zdarzeń przy użyciu algorytmu "zachłanne". Te dzierżawy są ostatnie dla danego przedziału czasu, a następnie muszą zostać odnowione. W miarę jak nowe węzły, w tym przypadku wystąpienia procesów roboczych, przechodzą w tryb online, umieszczają rezerwacje dzierżawy i z czasem obciążenie przesuwa się między węzłami, ponieważ każdy próbuje uzyskać więcej dzierżaw.

![Host procesora zdarzeń](./media/event-hubs-programming-guide/IC759863.png)

W miarę upływu czasu zostaje ustalona równowaga. Ta dynamiczna funkcja umożliwia zastosowanie skalowania automatycznego na podstawie procesora CPU do odbiorców, zarówno w celu skalowania w górę, jak i w dół. Ponieważ Event Hubs nie ma bezpośredniej koncepcji liczby komunikatów, średnie wykorzystanie procesora CPU jest często najlepszym mechanizmem mierzenia skali zaplecza lub konsumenta. Jeśli wydawcy zaczną publikować więcej zdarzeń, niż odbiorcy mogą przetworzyć, zwiększenie użycia procesora CPU przez odbiorców może służyć do powodowania automatycznego skalowania liczby wystąpień procesu roboczego.

Klasa [EventProcessorHost][] implementuje również mechanizm tworzenia punktów kontrolnych oparty na usłudze Azure Storage. Ten mechanizm przechowuje przesunięcie na podstawie partycji, dzięki czemu każdy odbiorca może określić, jaki był ostatni punkt kontrolny od poprzedniego odbiorcy. Ponieważ partycje przechodzą między węzłami za pośrednictwem dzierżaw, jest to mechanizm synchronizacji, który ułatwia przesunięcie obciążenia.

## <a name="publisher-revocation"></a>Odwołanie wydawcy

Oprócz zaawansowanych funkcji w czasie wykonywania hosta procesora zdarzeń usługa Event Hubs umożliwia [odwoływanie wydawcy](/rest/api/eventhub/revoke-publisher) w celu zablokowania wysyłania zdarzeń przez określonych wydawców do centrum zdarzeń. Te funkcje są przydatne, jeśli naruszono token wydawcy lub aktualizacja oprogramowania powoduje niewłaściwe zachowanie. W takich sytuacjach dla tożsamości wydawcy, która jest częścią jego tokenu sygnatury dostępu współdzielonego, można zablokować dostęp do publikowania zdarzeń.

> [!NOTE]
> Obecnie tylko interfejs API REST obsługuje tę funkcję ([odwołanie wydawcy](/rest/api/eventhub/revoke-publisher)).


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o scenariuszach usługi Event Hubs, skorzystaj z następujących linków:

* [Omówienie interfejsu API usługi Event Hubs](./event-hubs-samples.md)
* [Co to jest Event Hubs](./event-hubs-about.md)
* [Availability and consistency in Event Hubs](event-hubs-availability-and-consistency.md) (Dostępność i spójność w usłudze Event Hubs)
* [Dokumentacja interfejsu API hosta procesora zdarzeń](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)

[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[EventHubClient]: /dotnet/api/microsoft.azure.eventhubs.eventhubclient
[EventData]: /dotnet/api/microsoft.azure.eventhubs.eventdata
[CreateEventHubIfNotExists]: /dotnet/api/microsoft.servicebus.namespacemanager.createeventhubifnotexists
[PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.eventdata#Microsoft_ServiceBus_Messaging_EventData_PartitionKey
[EventProcessorHost]: /dotnet/api/microsoft.azure.eventhubs.processor
