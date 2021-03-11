---
title: Najlepsze rozwiązania dotyczące poprawy wydajności przy użyciu Azure Service Bus
description: Opisuje, w jaki sposób używać Service Bus do optymalizowania wydajności podczas wymiany komunikatów obsługiwanych przez brokera.
ms.topic: article
ms.date: 03/09/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 10435f74cfb7c87ccb28b64e1b3f136add1dc927
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102561878"
---
# <a name="best-practices-for-performance-improvements-using-service-bus-messaging"></a>Najlepsze rozwiązania dotyczące zwiększania wydajności przy użyciu komunikatów usługi Service Bus

W tym artykule opisano, jak za pomocą Azure Service Bus zoptymalizować wydajność podczas wymiany komunikatów obsługiwanych przez brokera. W pierwszej części tego artykułu opisano różne mechanizmy zwiększające wydajność. Druga część zawiera wskazówki dotyczące używania Service Bus w sposób, który może oferować najlepszą wydajność w danym scenariuszu.

W tym artykule termin "klient" odnosi się do każdej jednostki, która uzyskuje dostęp do Service Bus. Klient może przejąć rolę nadawcy lub odbiornika. Termin "Sender" jest używany dla klienta kolejki Service Bus lub klienta tematu, który wysyła komunikaty do kolejki Service Bus lub tematu. Termin "odbiornik" odnosi się do klienta kolejki Service Bus lub klienta subskrypcji, który odbiera wiadomości z kolejki Service Bus lub subskrypcji.

## <a name="protocols"></a>Protokoły
Service Bus umożliwia klientom wysyłanie i odbieranie komunikatów za pośrednictwem jednego z trzech protokołów:

1. Zaawansowane usługi kolejkowania Protocol (AMQP)
2. Service Bus Messaging Protocol (SBMP)
3. Protokół HTTP

AMQP jest najbardziej wydajny, ponieważ utrzymuje połączenie z Service Bus. Implementuje również [Przetwarzanie wsadowe](#batching-store-access) i [pobieranie z wyprzedzeniem](#prefetching). O ile nie zostało to wyraźnie określone, cała zawartość tego artykułu zakłada użycie AMQP lub SBMP.

> [!IMPORTANT]
> SBMP jest dostępna tylko dla .NET Framework. AMQP jest wartością domyślną dla .NET Standard.

## <a name="choosing-the-appropriate-service-bus-net-sdk"></a>Wybieranie odpowiedniego zestawu SDK platformy .NET Service Bus
Istnieją trzy obsługiwane Azure Service Bus zestawy SDK platformy .NET. Ich interfejsy API są podobne i mogą być trudne do wyboru. Zapoznaj się z poniższą tabelą, aby ułatwić podjęcie decyzji. Usługa Azure. Messaging. ServiceBus SDK jest najnowsza i zalecamy użycie jej w porównaniu z innymi zestawami SDK. Platformy Azure. Messaging. ServiceBus i Microsoft. Azure. ServiceBus SDK są nowoczesne, wydajne i Międzyplatformowe. Ponadto obsługują one AMQP za pośrednictwem elementów WebSockets i są częścią kolekcji zestawu SDK platformy Azure dla projektów typu open source.

| Pakiet NuGet | Podstawowe przestrzenie nazw | Minimalna liczba platform | Protokoły |
|---------------|----------------------|---------------------|-------------|
| [Azure. Messaging. ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus) | `Azure.Messaging.ServiceBus`<br>`Azure.Messaging.ServiceBus.Administration` | .NET Core 2.0<br>.NET Framework 4.6.1<br>Mono 5,4<br>Platforma Xamarin. iOS 10,14<br>Xamarin. Mac 3,8<br>Xamarin. Android 8,0<br>Platforma uniwersalna systemu Windows 10.0.16299 | AMQP<br>HTTP |
| [Microsoft. Azure. ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/) | `Microsoft.Azure.ServiceBus`<br>`Microsoft.Azure.ServiceBus.Management` | .NET Core 2.0<br>.NET Framework 4.6.1<br>Mono 5,4<br>Platforma Xamarin. iOS 10,14<br>Xamarin. Mac 3,8<br>Xamarin. Android 8,0<br>Platforma uniwersalna systemu Windows 10.0.16299 | AMQP<br>HTTP |
| [WindowsAzure. ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus) | `Microsoft.ServiceBus`<br>`Microsoft.ServiceBus.Messaging` | .NET Framework 4.6.1 | AMQP<br>SBMP<br>HTTP |

Aby uzyskać więcej informacji o minimalnej obsłudze platformy .NET Standard, zobacz [Obsługa implementacji platformy .NET](/dotnet/standard/net-standard#net-implementation-support).

## <a name="reusing-factories-and-clients"></a>Używanie fabryk i klientów
# <a name="azuremessagingservicebus-sdk"></a>[Zestaw SDK platformy Azure. Messaging. ServiceBus](#tab/net-standard-sdk-2)
Obiekty Service Bus, które współpracują z usługą, takie jak [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient), [ServiceBusSender](/dotnet/api/azure.messaging.servicebus.servicebussender), [ServiceBusReceiver](/dotnet/api/azure.messaging.servicebus.servicebusreceiver)i [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor), powinny być zarejestrowane na potrzeby iniekcji zależności jako pojedyncze (lub są tworzone raz i udostępnione). ServiceBusClient można zarejestrować na potrzeby iniekcji zależności z [ServiceBusClientBuilderExtensions](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/servicebus/Azure.Messaging.ServiceBus/src/Compatibility/ServiceBusClientBuilderExtensions.cs). 

Nie zaleca się zamykania ani usuwania tych obiektów po wysłaniu lub odebraniu poszczególnych komunikatów. Zamykanie lub likwidowanie obiektów specyficznych dla obiektu (ServiceBusSender/Receiver/procesor) powoduje przerwanie połączenia z usługą Service Bus. Likwidacja ServiceBusClient skutkuje rozbiciem połączenia z usługą Service Bus. 

# <a name="microsoftazureservicebus-sdk"></a>[Zestaw SDK Microsoft. Azure. ServiceBus](#tab/net-standard-sdk)

Service Bus obiektów klienta, takich jak implementacje [`IQueueClient`][QueueClient] lub [`IMessageSender`][MessageSender] , powinny być zarejestrowane na potrzeby iniekcji zależności jako pojedyncze (lub są tworzone raz i udostępnione). Firma Microsoft zaleca, aby nie zamykać fabryk komunikatów, kolejek, tematów lub klientów subskrypcji po wysłaniu wiadomości, a następnie utworzyć je ponownie po wysłaniu kolejnej wiadomości. Zamknięcie fabryki komunikatów powoduje usunięcie połączenia z usługą Service Bus. Podczas ponownego tworzenia fabryki jest nawiązywane nowe połączenie. 

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure. ServiceBus SDK](#tab/net-framework-sdk)

Service Bus obiektów klienta, takich jak `QueueClient` lub `MessageSender` , są tworzone za pomocą obiektu [MessagingFactory][MessagingFactory] , który również zapewnia wewnętrzne zarządzanie połączeniami. Firma Microsoft zaleca, aby nie zamykać fabryk komunikatów, kolejek, tematów lub klientów subskrypcji po wysłaniu wiadomości, a następnie utworzyć je ponownie po wysłaniu kolejnej wiadomości. Zamknięcie fabryki komunikatów powoduje usunięcie połączenia z usługą Service Bus i nawiązanie nowego połączenia podczas odtwarzania fabryki. 

---

Następująca Uwaga dotyczy wszystkich zestawów SDK:

> [!NOTE]
> Nawiązywanie połączenia jest kosztowną operacją, którą można uniknąć przez ponowne użycie tych samych obiektów Factory i Client dla wielu operacji. Można bezpiecznie używać tych obiektów klienta do równoczesnych operacji asynchronicznych i z wielu wątków.

## <a name="concurrent-operations"></a>Operacje współbieżne
Operacje, takie jak wysyłanie, odbieranie, usuwanie i tak dalej, zajmuje trochę czasu. Ten czas obejmuje czas, przez jaki usługa Service Bus podejmuje proces przetwarzania operacji oraz opóźnienia żądania i odpowiedzi. Aby zwiększyć liczbę operacji na czas, operacje muszą być wykonywane współbieżnie.

Klient planuje współbieżne operacje przez wykonywanie operacji **asynchronicznych** . Następne żądanie zostało uruchomione przed ukończeniem poprzedniego żądania. Poniższy fragment kodu jest przykładem asynchronicznej operacji wysyłania:

# <a name="azuremessagingservicebus-sdk"></a>[Zestaw SDK platformy Azure. Messaging. ServiceBus](#tab/net-standard-sdk-2)
```csharp
var messageOne = new ServiceBusMessage(body);
var messageTwo = new ServiceBusMessage(body);

var sendFirstMessageTask =
    sender.SendMessageAsync(messageOne).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #1");
    });
var sendSecondMessageTask =
    sender.SendMessageAsync(messageTwo).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #2");
    });

await Task.WhenAll(sendFirstMessageTask, sendSecondMessageTask);
Console.WriteLine("All messages sent");

```

# <a name="microsoftazureservicebus-sdk"></a>[Zestaw SDK Microsoft. Azure. ServiceBus](#tab/net-standard-sdk)

```csharp
var messageOne = new Message(body);
var messageTwo = new Message(body);

var sendFirstMessageTask =
    queueClient.SendAsync(messageOne).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #1");
    });
var sendSecondMessageTask =
    queueClient.SendAsync(messageTwo).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #2");
    });

await Task.WhenAll(sendFirstMessageTask, sendSecondMessageTask);
Console.WriteLine("All messages sent");
```

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure. ServiceBus SDK](#tab/net-framework-sdk)

```csharp
var messageOne = new BrokeredMessage(body);
var messageTwo = new BrokeredMessage(body);

var sendFirstMessageTask =
    queueClient.SendAsync(messageOne).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #1");
    });
var sendSecondMessageTask =
    queueClient.SendAsync(messageTwo).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #2");
    });

await Task.WhenAll(sendFirstMessageTask, sendSecondMessageTask);
Console.WriteLine("All messages sent");
```

---

Poniższy kod jest przykładem asynchronicznej operacji odbierania.

# <a name="azuremessagingservicebus-sdk"></a>[Zestaw SDK platformy Azure. Messaging. ServiceBus](#tab/net-standard-sdk-2)

```csharp
var client = new ServiceBusClient(connectionString);
var options = new ServiceBusProcessorOptions 
{

      AutoCompleteMessages = false,
      MaxConcurrentCalls = 20
};
await using ServiceBusProcessor processor = client.CreateProcessor(queueName,options);
processor.ProcessMessageAsync += MessageHandler;
processor.ProcessErrorAsync += ErrorHandler;

static Task ErrorHandler(ProcessErrorEventArgs args)
{
    Console.WriteLine(args.Exception);
    return Task.CompletedTask;
};

static async Task MessageHandler(ProcessMessageEventArgs args)
{
    Console.WriteLine("Handle message");
    await args.CompleteMessageAsync(args.Message);
}

await processor.StartProcessingAsync();
```

# <a name="microsoftazureservicebus-sdk"></a>[Zestaw SDK Microsoft. Azure. ServiceBus](#tab/net-standard-sdk)

Zobacz repozytorium GitHub, aby zapoznać się z pełnymi <a href="https://github.com/Azure/azure-service-bus/blob/master/samples/DotNet/Microsoft.Azure.ServiceBus/SendersReceiversWithQueues" target="_blank">przykładami <span class="docon docon-navigate-external x-hidden-focus"></span> kodu źródłowego </a>:

```csharp
var receiver = new MessageReceiver(connectionString, queueName, ReceiveMode.PeekLock);

static Task LogErrorAsync(Exception exception)
{
    Console.WriteLine(exception);
    return Task.CompletedTask;
};

receiver.RegisterMessageHandler(
    async (message, cancellationToken) =>
    {
        Console.WriteLine("Handle message");
        await receiver.CompleteAsync(message.SystemProperties.LockToken);
    },
    new MessageHandlerOptions(e => LogErrorAsync(e.Exception))
    {
        AutoComplete = false,
        MaxConcurrentCalls = 20
    });
```

`MessageReceiver`Wystąpienie obiektu jest tworzone z parametrami połączenia, nazwą kolejki i trybem odbioru wglądu w Podgląd. Następnie to `receiver` wystąpienie jest używane do rejestrowania programu obsługi komunikatów.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure. ServiceBus SDK](#tab/net-framework-sdk)

Zobacz repozytorium GitHub, aby zapoznać się z pełnymi <a href="https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/SendersReceiversWithQueues" target="_blank">przykładami <span class="docon docon-navigate-external x-hidden-focus"></span> kodu źródłowego </a>:

```csharp
var factory = MessagingFactory.CreateFromConnectionString(connectionString);
var receiver = await factory.CreateMessageReceiverAsync(queueName, ReceiveMode.PeekLock);

// Register the handler to receive messages asynchronously
receiver.OnMessageAsync(
    async message =>
    {
        Console.WriteLine("Handle message");
        await message.CompleteAsync();
    },
    new OnMessageOptions
    {
        AutoComplete = false,
        MaxConcurrentCalls = 20
    });
```

`MessagingFactory`Tworzy `factory` obiekt z parametrów połączenia. `factory`Wystąpienie `MessageReceiver` jest tworzone. Następnie to `receiver` wystąpienie jest używane do rejestrowania programu obsługi w wiadomości.

---

## <a name="receive-mode"></a>Tryb odbioru

Podczas tworzenia kolejki lub klienta subskrypcji można określić tryb odbioru: *Zablokuj* lub *Odbierz i Usuń*. Domyślny tryb odbioru to `PeekLock` . Podczas działania w trybie domyślnym klient wysyła żądanie odebrania komunikatu z Service Bus. Po odebraniu wiadomości klient wysyła żądanie, aby zakończyć ten komunikat.

Podczas ustawiania trybu odbioru do `ReceiveAndDelete` , oba kroki są łączone w pojedynczym żądaniu. Te kroki umożliwiają zmniejszenie ogólnej liczby operacji i zwiększenie ogólnej przepływności komunikatów. Ten zysk wydajności jest narażony na ryzyko utraty komunikatów.

Service Bus nie obsługuje transakcji dla operacji odbierania i usuwania. Ponadto semantyka blokady wglądu jest wymagana dla wszystkich scenariuszy, w których klient chce odroczyć lub [martwić](service-bus-dead-letter-queues.md) wiadomość.

## <a name="client-side-batching"></a>Przetwarzanie wsadowe po stronie klienta

Tworzenie wsadowe po stronie klienta umożliwia klientowi kolejki lub tematu opóźnienie wysyłania komunikatu przez określony czas. Jeśli klient wysyła dodatkowe komunikaty w tym okresie, przesyła komunikaty w jednej partii. Przetwarzanie wsadowe po stronie klienta powoduje także, że klient kolejki lub subskrypcji **wykonuje** wsadowe wiele żądań w ramach pojedynczego żądania. Przetwarzanie wsadowe jest dostępne tylko w przypadku asynchronicznych operacji **wysyłania** i **kończenia** . Operacje synchroniczne są natychmiast wysyłane do usługi Service Bus. Przetwarzanie wsadowe nie odbywa się w przypadku operacji wglądu lub odbierania, ani nie występuje przetwarzanie wsadowe na wielu klientach.

# <a name="azuremessagingservicebus-sdk"></a>[Zestaw SDK platformy Azure. Messaging. ServiceBus](#tab/net-standard-sdk-2)
Funkcja przetwarzania wsadowego dla zestawu SDK .NET Standard nie uwidacznia jeszcze właściwości do manipulowania.

# <a name="microsoftazureservicebus-sdk"></a>[Zestaw SDK Microsoft. Azure. ServiceBus](#tab/net-standard-sdk)

Funkcja przetwarzania wsadowego dla zestawu SDK .NET Standard nie uwidacznia jeszcze właściwości do manipulowania.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure. ServiceBus SDK](#tab/net-framework-sdk)

Domyślnie klient używa interwału partii 20 ms. Możesz zmienić interwał partii, ustawiając właściwość [BatchFlushInterval][BatchFlushInterval] przed utworzeniem fabryki komunikatów. To ustawienie ma wpływ na wszystkich klientów utworzonych przez tę fabrykę.

Aby wyłączyć tworzenie partii, ustaw właściwość [BatchFlushInterval][BatchFlushInterval] na **TimeSpan. zero**. Na przykład:

```csharp
var settings = new MessagingFactorySettings
{
    NetMessagingTransportSettings =
    {
        BatchFlushInterval = TimeSpan.Zero
    }
};
var factory = MessagingFactory.Create(namespaceUri, settings);
```

Przetwarzanie wsadowe nie ma wpływu na liczbę operacji do rozliczania komunikatów i jest dostępne tylko dla protokołu klienta Service Bus przy użyciu biblioteki [Microsoft. ServiceBus. Messaging](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) . Protokół HTTP nie obsługuje przetwarzania wsadowego.

> [!NOTE]
> Ustawienie `BatchFlushInterval` zapewnia, że przetwarzanie wsadowe jest niejawne w perspektywie aplikacji. tj.; aplikacja wykonuje `SendAsync` i `CompleteAsync` wywołuje i nie tworzy określonych wywołań wsadowych.
>
> Jawne wykonywanie wsadowe po stronie klienta można zaimplementować przy użyciu poniższego wywołania metody:
> ```csharp
> Task SendBatchAsync(IEnumerable<BrokeredMessage> messages);
> ```
> Łączny rozmiar komunikatów musi być mniejszy niż maksymalny rozmiar obsługiwany przez warstwę cenową.

---

## <a name="batching-store-access"></a>Dostęp do magazynu wsadowego

Aby zwiększyć przepływność kolejki, tematu lub subskrypcji, Service Bus przetworzyć wiele komunikatów podczas zapisywania ich w magazynie wewnętrznym. 

- Po włączeniu tworzenia pakietów wsadowych w kolejce zapisanie komunikatów do sklepu i usunięcie komunikatów ze sklepu spowoduje przetworzenie partii. 
- Po włączeniu tworzenia pakietów wsadowych w temacie zapisywanie komunikatów w sklepie odbywa się w partii. 
- Po włączeniu tworzenia pakietów wsadowych w ramach subskrypcji usuwanie wiadomości ze sklepu odbywa się wsadowo. 
- Gdy dla jednostki jest włączony dostęp do magazynu wsadowego, Service Bus opóźni operacji zapisu w sklepie dla tej jednostki o maksymalnie 20 ms.

> [!NOTE]
> Nie ma ryzyka utraty komunikatów z przetwarzaniem wsadowym, nawet jeśli wystąpi błąd Service Bus na końcu interwału wsadowego 20ms.

Dodatkowe operacje magazynu występujące w tym interwale są dodawane do zadania wsadowego. Dostęp do magazynu wsadowego ma wpływ tylko na operacje **wysyłania** i **kończenia** . nie ma to wpływu na operacje odbierania. Dostęp do magazynu wsadowego jest właściwością obiektu. Przetwarzanie wsadowe odbywa się we wszystkich jednostkach, które umożliwiają dostęp do magazynu wsadowego.

Podczas tworzenia nowej kolejki, tematu lub subskrypcji dostęp do magazynu wsadowego jest domyślnie włączony.


# <a name="azuremessagingservicebus-sdk"></a>[Zestaw SDK platformy Azure. Messaging. ServiceBus](#tab/net-standard-sdk-2)
Aby wyłączyć dostęp do magazynu wsadowego, konieczne będzie wystąpienie elementu `ServiceBusAdministrationClient` . Utwórz na `CreateQueueOptions` podstawie opisu kolejki, który ustawia `EnableBatchedOperations` Właściwość na `false` .

```csharp
var options = new CreateQueueOptions(path)
{
    EnableBatchedOperations = false
};
var queue = await administrationClient.CreateQueueAsync(options);
```


# <a name="microsoftazureservicebus-sdk"></a>[Zestaw SDK Microsoft. Azure. ServiceBus](#tab/net-standard-sdk)

Aby wyłączyć dostęp do magazynu wsadowego, konieczne będzie wystąpienie elementu `ManagementClient` . Utwórz kolejkę na podstawie opisu kolejki, która ustawia `EnableBatchedOperations` Właściwość na `false` .

```csharp
var queueDescription = new QueueDescription(path)
{
    EnableBatchedOperations = false
};
var queue = await managementClient.CreateQueueAsync(queueDescription);
```

Aby uzyskać więcej informacji, zobacz następujące artykuły:
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.queuedescription.enablebatchedoperations" target="_blank">`Microsoft.Azure.ServiceBus.Management.QueueDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.subscriptiondescription.enablebatchedoperations" target="_blank">`Microsoft.Azure.ServiceBus.Management.SubscriptionDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.topicdescription.enablebatchedoperations" target="_blank">`Microsoft.Azure.ServiceBus.Management.TopicDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure. ServiceBus SDK](#tab/net-framework-sdk)

Aby wyłączyć dostęp do magazynu wsadowego, konieczne będzie wystąpienie elementu `NamespaceManager` . Utwórz kolejkę na podstawie opisu kolejki, która ustawia `EnableBatchedOperations` Właściwość na `false` .

```csharp
var queueDescription = new QueueDescription(path)
{
    EnableBatchedOperations = false
};
var queue = namespaceManager.CreateQueue(queueDescription);
```

Aby uzyskać więcej informacji, zobacz następujące artykuły:
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablebatchedoperations" target="_blank">`Microsoft.ServiceBus.Messaging.QueueDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.enablebatchedoperations" target="_blank">`Microsoft.ServiceBus.Messaging.SubscriptionDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.topicdescription.enablebatchedoperations" target="_blank">`Microsoft.ServiceBus.Messaging.TopicDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---

Dostęp do magazynu wsadowego nie ma wpływu na liczbę operacji związanych z obsługą komunikatów. Jest to właściwość kolejki, tematu lub subskrypcji. Jest on niezależny od trybu odbioru i protokołu, który jest używany między klientem a usługą Service Bus.

## <a name="prefetching"></a>Wyprzedzeniem

Wstępne [pobieranie](service-bus-prefetch.md) umożliwia klientowi kolejki lub subskrypcji ładowanie dodatkowych komunikatów z usługi po odebraniu komunikatów. Klient przechowuje te komunikaty w lokalnej pamięci podręcznej. Rozmiar pamięci podręcznej jest określany przez `QueueClient.PrefetchCount` lub `SubscriptionClient.PrefetchCount` właściwości. Każdy klient, który umożliwia prepobieranie, utrzymuje własną pamięć podręczną. Pamięć podręczna nie jest współużytkowana przez klientów. Jeśli klient uruchamia operację odbierania, a jej pamięć podręczna jest pusta, usługa przesyła partię komunikatów. Rozmiar wsadu jest równy rozmiarowi pamięci podręcznej lub 256 KB, w zależności od tego, który jest mniejszy. Jeśli klient uruchamia operację odbierania, a pamięć podręczna zawiera komunikat, komunikat jest pobierany z pamięci podręcznej.

Gdy wiadomość jest pobierana, usługa blokuje pobierany komunikat. Po zablokowaniu nie można odebrać pobranego komunikatu przez innego odbiorcę. Jeśli odbiorca nie może zakończyć komunikatu przed wygaśnięciem blokady, komunikat zostanie udostępniony innym odbiornikom. Prebrana kopia komunikatu pozostaje w pamięci podręcznej. Odbiorca zużywający wygasłą kopię w pamięci podręcznej otrzyma wyjątek podczas próby wykonania tego komunikatu. Domyślnie Blokada wiadomości wygasa po 60 sekundach. Ta wartość może zostać przedłużona do 5 minut. Aby zapobiec zużyciu wygasłych komunikatów, należy ustawić rozmiar pamięci podręcznej mniejszy niż liczba komunikatów, których klient może użyć w interwale limitu czasu blokady.

W przypadku korzystania z domyślnego czasu blokady 60 sekund wartość jest równa `PrefetchCount` 20 razy większa niż maksymalna szybkość przetwarzania wszystkich odbiorników fabryki. Na przykład fabryka tworzy trzy odbiorniki, a każdy odbiornik może przetwarzać maksymalnie 10 komunikatów na sekundę. Liczba pobieranych odczytów nie powinna być większa niż 20 X 3 X 10 = 600. Domyślnie `PrefetchCount` jest ustawiona na 0, co oznacza, że żadne dodatkowe komunikaty nie są pobierane z usługi.

Pobieranie z wyprzedzeniem komunikatów zwiększa ogólną przepływność kolejki lub subskrypcji, ponieważ zmniejsza ogólną liczbę operacji komunikatów lub rundy. Pobieranie pierwszego komunikatu trwa jednak dłużej (ze względu na większy rozmiar wiadomości). Pobieranie pobranych z pamięci podręcznej komunikatów jest szybsze, ponieważ te komunikaty zostały już pobrane przez klienta.

Właściwość Time-to-Live (TTL) komunikatu jest sprawdzana przez serwer w momencie, gdy serwer wysyła komunikat do klienta programu. Po odebraniu komunikatu klient nie sprawdza właściwości TTL komunikatu. Zamiast tego komunikat można odbierać nawet wtedy, gdy czas wygaśnięcia komunikatu został przekazany, gdy komunikat został zbuforowany przez klienta.

Pobieranie z wyprzedzeniem nie wpływa na liczbę operacji wysyłania komunikatów do rozliczeń i jest dostępne tylko dla protokołu klienta Service Bus. Protokół HTTP nie obsługuje pobierania z wyprzedzeniem. Pobieranie z wyprzedzeniem jest dostępne zarówno dla operacji synchronicznych, jak i asynchronicznych.

# <a name="azuremessagingservicebus-sdk"></a>[Zestaw SDK platformy Azure. Messaging. ServiceBus](#tab/net-standard-sdk-2)
Aby uzyskać więcej informacji, zobacz następujące `PrefetchCount` Właściwości:

- [ServiceBusReceiver.PrefetchCount](/dotnet/api/azure.messaging.servicebus.servicebusreceiver.prefetchcount)
- [ServiceBusProcessor.PrefetchCount](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.prefetchcount)

Wartości tych właściwości można ustawić w [ServiceBusReceiverOptions](/dotnet/api/azure.messaging.servicebus.servicebusreceiveroptions) lub [ServiceBusProcessorOptions](/dotnet/api/azure.messaging.servicebus.servicebusprocessoroptions).

# <a name="microsoftazureservicebus-sdk"></a>[Zestaw SDK Microsoft. Azure. ServiceBus](#tab/net-standard-sdk)

Aby uzyskać więcej informacji, zobacz następujące `PrefetchCount` Właściwości:

* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount" target="_blank">`Microsoft.Azure.ServiceBus.QueueClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.subscriptionclient.prefetchcount" target="_blank">`Microsoft.Azure.ServiceBus.SubscriptionClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure. ServiceBus SDK](#tab/net-framework-sdk)

Aby uzyskać więcej informacji, zobacz następujące `PrefetchCount` Właściwości:

* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queueclient.prefetchcount" target="_blank">`Microsoft.ServiceBus.Messaging.QueueClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.prefetchcount" target="_blank">`Microsoft.ServiceBus.Messaging.SubscriptionClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---

## <a name="prefetching-and-receivebatch"></a>Pobieranie i ReceiveBatch
Chociaż koncepcje pobierania wielu wiadomości razem mają podobną semantykę do przetwarzania komunikatów w partii ( `ReceiveBatch` ), istnieją pewne drobne różnice, które należy wziąć pod uwagę podczas wspólnego korzystania z tych metod.

Pobieranie z wyprzedzeniem jest konfiguracją (lub trybem) na kliencie ( `QueueClient` i `SubscriptionClient` ) i `ReceiveBatch` jest operacją (która ma semantykę odpowiedzi żądania).

Jednocześnie wykorzystując te podejścia, weź pod uwagę następujące przypadki —

* Pobieranie z wyprzedzeniem powinno być większe niż lub równe liczbie komunikatów, z których oczekiwano `ReceiveBatch` .
* Pobieranie z wyprzedzeniem może trwać do n/3 razy więcej komunikatów przetworzonych na sekundę, gdzie n to domyślny czas blokady.

Istnieją pewne wyzwania z zachłanne podejściem, czyli utrzymywaniem dużej liczby odczytów, ponieważ oznacza to, że komunikat jest zablokowany do określonego odbiorcy. Zaleca się wypróbowanie wartości pobieranych z wyprzedzeniem między progami wymienionymi powyżej i empirycznie zidentyfikować, co pasuje.

## <a name="multiple-queues"></a>Wiele kolejek

Jeśli jedna kolejka lub temat nie może obsłużyć oczekiwanego, Użyj wielu jednostek obsługi komunikatów. W przypadku korzystania z wielu jednostek należy utworzyć dedykowanego klienta dla każdej jednostki, zamiast korzystać z tego samego klienta dla wszystkich jednostek.

## <a name="development-and-testing-features"></a>Funkcje deweloperskie i testowe

> [!NOTE]
> Ta sekcja dotyczy tylko zestawu SDK WindowsAzure. ServiceBus, takich jak Microsoft. Azure. ServiceBus i Azure. Messaging. ServiceBus nie uwidacznia tej funkcji.

Service Bus ma jedną funkcję, która została użyta do celów deweloperskich, która **nigdy nie powinna być używana w konfiguracjach produkcyjnych**: [`TopicDescription.EnableFilteringMessagesBeforePublishing`][TopicDescription.EnableFiltering] .

Po dodaniu nowych reguł lub filtrów do tematu można [`TopicDescription.EnableFilteringMessagesBeforePublishing`][TopicDescription.EnableFiltering] sprawdzić, czy nowe wyrażenie filtru działa zgodnie z oczekiwaniami.

## <a name="scenarios"></a>Scenariusze

W poniższych sekcjach opisano typowe scenariusze obsługi komunikatów oraz zawarto szczegółowe informacje o preferowanych ustawieniach Service Bus. Stawki za przepływność są klasyfikowane jako małe (mniej niż 1 komunikat/sekundę), umiarkowane (1 komunikat/sekundę lub większe, ale mniej niż 100 komunikatów/sekundę) i wysoka (100 komunikaty/s lub nowsze). Liczba klientów została sklasyfikowana jako mała (5 lub mniej), umiarkowana (więcej niż 5, ale mniejsza lub równa 20) i duża (więcej niż 20).

### <a name="high-throughput-queue"></a>Kolejka o wysokiej przepływności

Cel: Maksymalizuj przepływność pojedynczej kolejki. Liczba nadawców i odbiorników jest mała.

* Aby zwiększyć ogólną szybkość przesyłania do kolejki, należy użyć wielu fabryk komunikatów do tworzenia nadawców. Dla każdego nadawcy Użyj operacji asynchronicznych lub wielu wątków.
* Aby zwiększyć ogólną szybkość odbierania z kolejki, należy użyć wielu fabryk komunikatów do tworzenia odbiorników.
* Użyj operacji asynchronicznych, aby skorzystać z tworzenia wsadowego po stronie klienta.
* Ustaw interwał przetwarzania wsadowego na 50 ms, aby zmniejszyć liczbę operacji przesyłania Service Bus protokołu klienta. Jeśli używanych jest wiele nadawców, zwiększ interwał przetwarzania wsadowego do 100 ms.
* Pozostaw włączony dostęp do magazynu wsadowego. Ten dostęp zwiększa ogólną szybkość, z jaką komunikaty mogą być zapisywane do kolejki.
* Ustaw liczbę pobrania z wyprzedzeniem na 20 razy większą szybkość przetwarzania wszystkich odbiorników fabryki. Ta liczba zmniejsza liczbę Service Bus transmisjach protokołu klienta.

### <a name="multiple-high-throughput-queues"></a>Wiele kolejek o dużej przepływności

Cel: maksymalizowanie ogólnej przepływności wielu kolejek. Przepływność pojedynczej kolejki ma wartość umiarkowana lub wysoka.

Aby uzyskać maksymalną przepływność między wieloma kolejkami, Użyj ustawień opisanych w celu zmaksymalizowania przepływności pojedynczej kolejki. Należy również użyć różnych fabryk do tworzenia klientów wysyłających lub odbierających z różnych kolejek.

### <a name="low-latency-queue"></a>Kolejka małych opóźnień

Cel: Minimalizowanie opóźnień kolejki lub tematu. Liczba nadawców i odbiorników jest mała. Przepływność kolejki jest mała lub średnia.

* Wyłącz przetwarzanie wsadowe po stronie klienta. Klient natychmiast wysyła komunikat.
* Wyłącz dostęp do magazynu wsadowego. Usługa natychmiast zapisuje komunikat w sklepie.
* W przypadku korzystania z jednego klienta należy ustawić liczbę pobieranych z wyprzedzeniem na 20 razy szybkość przetwarzania odbiornika. Jeśli wiele komunikatów dociera do kolejki w tym samym czasie, protokół klienta Service Bus przesyła je wszystkie w tym samym czasie. Gdy klient odbierze następny komunikat, ten komunikat jest już w lokalnej pamięci podręcznej. Pamięć podręczna powinna być mała.
* W przypadku korzystania z wielu klientów należy ustawić liczbę pobrania z wyprzedzeniem na 0. Ustawiając licznik, drugi klient może odebrać drugi komunikat, podczas gdy pierwszy klient nadal przetwarza pierwszy komunikat.

### <a name="queue-with-a-large-number-of-senders"></a>Kolejka z dużą liczbą nadawców

Cel: maksymalizowanie przepływności kolejki lub tematu z dużą liczbą nadawców. Każdy nadawca wysyła komunikaty o umiarkowanej stawce. Liczba odbiorników jest mała.

Service Bus włącza do 1000 współbieżnych połączeń z jednostką obsługi komunikatów. Ten limit jest wymuszany na poziomie przestrzeni nazw, a kolejki, tematy lub subskrypcje są ograniczone przez limit równoczesnych połączeń na przestrzeń nazw. W przypadku kolejek ta liczba jest udostępniana między nadawcami i odbiornikami. Jeśli dla nadawców są wymagane wszystkie połączenia 1000, Zastąp kolejkę z tematem i jedną subskrypcją. Temat akceptuje do 1000 połączeń współbieżnych od nadawców. Subskrypcja akceptuje dodatkowe połączenia współbieżne 1000 od odbiorników. Jeśli wymagane są ponad 1000 współbieżnych nadawców, nadawcy powinni wysyłać komunikaty do protokołu Service Bus za pośrednictwem protokołu HTTP.

Aby zmaksymalizować przepływność, wykonaj następujące kroki:

* Jeśli każdy nadawca jest w innym procesie, użyj tylko jednej fabryki na proces.
* Użyj operacji asynchronicznych, aby skorzystać z tworzenia wsadowego po stronie klienta.
* Użyj domyślnego interwału wsadowego o wartości 20 ms, aby zmniejszyć liczbę Service Bus transmisjach protokołu klienta.
* Pozostaw włączony dostęp do magazynu wsadowego. Ten dostęp zwiększa ogólną szybkość, z jaką komunikaty mogą być zapisywane do kolejki lub tematu.
* Ustaw liczbę pobrania z wyprzedzeniem na 20 razy większą szybkość przetwarzania wszystkich odbiorników fabryki. Ta liczba zmniejsza liczbę Service Bus transmisjach protokołu klienta.

### <a name="queue-with-a-large-number-of-receivers"></a>Kolejka z dużą liczbą odbiorników

Cel: maksymalizuje szybkość odbierania kolejki lub subskrypcji o dużej liczbie odbiorników. Każdy odbiorca otrzymuje komunikaty o umiarkowanej stawce. Liczba nadawców jest niewielka.

Service Bus włącza do 1000 współbieżnych połączeń z jednostką. Jeśli kolejka wymaga więcej niż 1000 odbiorników, Zastąp kolejkę temat i wieloma subskrypcjami. Każda subskrypcja może obsługiwać maksymalnie 1000 połączeń współbieżnych. Alternatywnie odbiorcy mogą uzyskać dostęp do kolejki za pośrednictwem protokołu HTTP.

Aby zmaksymalizować przepływność, postępuj zgodnie z następującymi wskazówkami:

* Jeśli każdy odbiornik jest w innym procesie, użyj tylko jednej fabryki na proces.
* Odbiorcy mogą korzystać z operacji synchronicznych lub asynchronicznych. Mając na względzie umiarkowany wskaźnik odbioru poszczególnych odbiorników, przetwarzanie wsadowe po stronie klienta dla całego żądania nie ma wpływu na przepływność odbiorcy.
* Pozostaw włączony dostęp do magazynu wsadowego. Ten dostęp zmniejsza całkowite obciążenie jednostki. Zmniejsza również ogólną szybkość, z jaką komunikaty mogą być zapisywane do kolejki lub tematu.
* Ustaw liczbę pobierania z wyprzedzeniem na niewielką wartość (na przykład PrefetchCount = 10). Ta liczba uniemożliwia bezczynne odbiorców, a inne odbiorniki mają w pamięci podręcznej dużą liczbę komunikatów.

### <a name="topic-with-a-few-subscriptions"></a>Temat z kilkoma subskrypcjami

Cel: Maksymalizuj przepływność tematu za pomocą kilku subskrypcji. Wiadomość jest odbierana przez wiele subskrypcji, co oznacza, że łączna szybkość odbierania dla wszystkich subskrypcji jest większa niż szybkość wysyłania. Liczba nadawców jest niewielka. Liczba odbiorników na subskrypcję jest mała.

Aby zmaksymalizować przepływność, postępuj zgodnie z następującymi wskazówkami:

* Aby zwiększyć ogólną szybkość przesyłania do tematu, należy użyć wielu fabryk komunikatów do tworzenia nadawców. Dla każdego nadawcy Użyj operacji asynchronicznych lub wielu wątków.
* Aby zwiększyć ogólną szybkość odbierania z subskrypcji, Użyj wielu fabryk komunikatów do tworzenia odbiorników. Dla każdego odbiornika Użyj operacji asynchronicznych lub wielu wątków.
* Użyj operacji asynchronicznych, aby skorzystać z tworzenia wsadowego po stronie klienta.
* Użyj domyślnego interwału wsadowego o wartości 20 ms, aby zmniejszyć liczbę Service Bus transmisjach protokołu klienta.
* Pozostaw włączony dostęp do magazynu wsadowego. Ten dostęp zwiększa ogólną szybkość, z jaką komunikaty mogą być zapisywane w temacie.
* Ustaw liczbę pobrania z wyprzedzeniem na 20 razy większą szybkość przetwarzania wszystkich odbiorników fabryki. Ta liczba zmniejsza liczbę Service Bus transmisjach protokołu klienta.

### <a name="topic-with-a-large-number-of-subscriptions"></a>Temat z dużą liczbą subskrypcji

Cel: Maksymalizuj przepływność tematu o dużej liczbie subskrypcji. Wiadomość jest odbierana przez wiele subskrypcji, co oznacza, że łączna szybkość odbierania dla wszystkich subskrypcji jest znacznie większa niż szybkość wysyłania. Liczba nadawców jest niewielka. Liczba odbiorników na subskrypcję jest mała.

Tematy zawierające dużą liczbę subskrypcji zazwyczaj uwidaczniają niską ogólną przepływność, jeśli wszystkie komunikaty są kierowane do wszystkich subskrypcji. Jest to spowodowane tym, że każdy komunikat jest odbierany wiele razy, a wszystkie komunikaty w temacie i wszystkie jego subskrypcje są przechowywane w tym samym magazynie. W tym miejscu założono, że liczba nadawców i liczba odbiorników na subskrypcję jest mała. Service Bus obsługuje do 2 000 subskrypcji na temat.

Aby zmaksymalizować przepływność, spróbuj wykonać następujące czynności:

* Użyj operacji asynchronicznych, aby skorzystać z tworzenia wsadowego po stronie klienta.
* Użyj domyślnego interwału wsadowego o wartości 20 ms, aby zmniejszyć liczbę Service Bus transmisjach protokołu klienta.
* Pozostaw włączony dostęp do magazynu wsadowego. Ten dostęp zwiększa ogólną szybkość, z jaką komunikaty mogą być zapisywane w temacie.
* Ustaw liczbę pobierania z wyprzedzeniem na 20 razy określoną częstotliwość odbierania w sekundach. Ta liczba zmniejsza liczbę Service Bus transmisjach protokołu klienta.

<!-- .NET Standard SDK, Microsoft.Azure.ServiceBus -->
[QueueClient]: /dotnet/api/microsoft.azure.servicebus.queueclient
[MessageSender]: /dotnet/api/microsoft.azure.servicebus.core.messagesender

<!-- .NET Framework SDK, Microsoft.Azure.ServiceBus -->
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[BatchFlushInterval]: /dotnet/api/microsoft.servicebus.messaging.messagesender.batchflushinterval
[ForcePersistence]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage.forcepersistence
[EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnableFiltering]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablefilteringmessagesbeforepublishing

<!-- Local links -->
[Partitioned messaging entities]: service-bus-partitioning.md
