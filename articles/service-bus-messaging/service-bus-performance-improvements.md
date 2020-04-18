---
title: Najważniejsze wskazówki dotyczące zwiększania wydajności przy użyciu usługi Azure Service Bus
description: W tym artykule opisano, jak używać usługi Service Bus do optymalizacji wydajności podczas wymiany wiadomości obsługiwanych przez brokera.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 03/12/2020
ms.author: aschhab
ms.openlocfilehash: 267965ee41280a677050d1676285dda8734bc044
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606057"
---
# <a name="best-practices-for-performance-improvements-using-service-bus-messaging"></a>Najlepsze rozwiązania dotyczące zwiększania wydajności przy użyciu komunikatów usługi Service Bus

W tym artykule opisano sposób korzystania z usługi Azure Service Bus w celu optymalizacji wydajności podczas wymiany wiadomości obsługiwanych przez brokera. W pierwszej części tego artykułu opisano różne mechanizmy, które są oferowane w celu zwiększenia wydajności. Druga część zawiera wskazówki dotyczące korzystania z usługi Service Bus w sposób, który może zaoferować najlepszą wydajność w danym scenariuszu.

W tym artykule termin "klient" odnosi się do dowolnej jednostki, która uzyskuje dostęp do usługi Service Bus. Klient może przyjąć rolę nadawcy lub odbiorcy. Termin "nadawca" jest używany dla kolejki usługi Service Bus lub klienta tematu, który wysyła wiadomości do kolejki usługi Service Bus lub subskrypcji tematu. Termin "odbiorca" odnosi się do kolejki usługi Service Bus lub klienta subskrypcji, który odbiera wiadomości z kolejki lub subskrypcji usługi Service Bus.

W tych sekcjach przedstawiono kilka pojęć używanych przez usługę Service Bus w celu zwiększenia wydajności.

## <a name="protocols"></a>Protokoły

Usługa Service Bus umożliwia klientom wysyłanie i odbieranie wiadomości za pośrednictwem jednego z trzech protokołów:

1. Zaawansowane usługi kolejkowania Protocol (AMQP)
2. Protokół SBMP (Service Bus Messaging Protocol)
3. Protokół HTTP

Usługa AMQP jest najbardziej wydajna, ponieważ utrzymuje połączenie z usługą Service Bus. Implementuje również przetwarzanie wsadowe i wstępne. O ile nie wspomniano wyraźnie, cała zawartość w tym artykule zakłada użycie AMQP lub SBMP.

> [!IMPORTANT]
> SBMP jest dostępny tylko dla platformy .NET Framework. AMQP jest wartością domyślną dla platformy .NET Standard.

## <a name="choosing-the-appropriate-service-bus-net-sdk"></a>Wybór odpowiedniego SDK magistrali usług .NET

Istnieją dwa obsługiwane zestawy SDK usługi Azure Service Bus .NET. Ich interfejsy API są bardzo podobne i może być mylące, który z nich wybrać. Zapoznaj się z poniższą tabelą, aby pomóc w podejmowaniu decyzji. Sugerujemy microsoft.Azure.ServiceBus SDK, ponieważ jest bardziej nowoczesny, wydajny i jest kompatybilny z wieloma platformami. Ponadto obsługuje protokół AMQP za pośrednictwem sieci WebSockets i jest częścią kolekcji azure .NET SDK projektów typu open source.

| Pakiet NuGet | Główne przestrzenie nazw | Minimalna platforma(-y) | Protokoły |
|---------------|----------------------|---------------------|-------------|
| <a href="https://www.nuget.org/packages/Microsoft.Azure.ServiceBus" target="_blank">Microsoft.Azure.ServiceBus<span class="docon docon-navigate-external x-hidden-focus"></span></a> | `Microsoft.Azure.ServiceBus`<br>`Microsoft.Azure.ServiceBus.Management` | .NET Core 2.0<br>.NET Framework 4.6.1<br>Mono 5.4<br>Xamarin.iOS 10.14<br>Xamarin.Mac 3.8<br>Xamarin.Android 8.0<br>Uniwersalna platforma systemu Windows 10.0.16299 | AMQP<br>HTTP |
| <a href="https://www.nuget.org/packages/WindowsAzure.ServiceBus" target="_blank">WindowsAzure.ServiceBus<span class="docon docon-navigate-external x-hidden-focus"></span></a> | `Microsoft.ServiceBus`<br>`Microsoft.ServiceBus.Messaging` | .NET Framework 4.6.1 | AMQP<br>SBMP (sbmp)<br>HTTP |

Aby uzyskać więcej informacji na temat minimalnej obsługi platformy .NET Standard, zobacz [pomoc techniczna w zakresie implementacji platformy .NET](https://docs.microsoft.com/dotnet/standard/net-standard#net-implementation-support).

## <a name="reusing-factories-and-clients"></a>Ponowne zagospodowanie fabryk i klientów

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

Obiekty klienta usługi Service Bus, [`IQueueClient`][QueueClient] [`IMessageSender`][MessageSender]takie jak implementacje lub , powinny być zarejestrowane dla iniekcji zależności jako singletons (lub wystąpienia raz i udostępnione). Zaleca się, aby nie zamykać fabryk obsługi wiadomości lub kolejek, tematów i klientów subskrypcji po wysłaniu wiadomości, a następnie ponownie utworzyć je podczas wysyłania następnej wiadomości. Zamknięcie fabryki wiadomości powoduje usunięcie połączenia z usługą Service Bus, a podczas ponownego tworzenia fabryki ustanawiane jest nowe połączenie. Ustanawianie połączenia jest kosztowną operacją, której można uniknąć, ponownie korzystając z tych samych obiektów fabrycznych i klienckich dla wielu operacji. Można bezpiecznie używać tych obiektów klienta dla równoczesnych operacji asynchronicznych i z wielu wątków.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

Obiekty klienta usługi Service `QueueClient` `MessageSender`Bus, takie jak lub , są tworzone za pośrednictwem [MessagingFactory][MessagingFactory] obiektu, który zapewnia również wewnętrzne zarządzanie połączeniami. Zaleca się, aby nie zamykać fabryk obsługi wiadomości lub kolejek, tematów i klientów subskrypcji po wysłaniu wiadomości, a następnie ponownie utworzyć je podczas wysyłania następnej wiadomości. Zamknięcie fabryki wiadomości powoduje usunięcie połączenia z usługą Service Bus, a podczas ponownego tworzenia fabryki ustanawiane jest nowe połączenie. Ustanawianie połączenia jest kosztowną operacją, której można uniknąć, ponownie korzystając z tych samych obiektów fabrycznych i klienckich dla wielu operacji. Można bezpiecznie używać tych obiektów klienta dla równoczesnych operacji asynchronicznych i z wielu wątków.

---

## <a name="concurrent-operations"></a>Operacje współbieżne

Wykonanie operacji (wysyłanie, odbieranie, usuwanie itp.) zajmuje trochę czasu. Ten czas obejmuje przetwarzanie operacji przez usługę Service Bus oprócz opóźnienia żądania i odpowiedzi. Aby zwiększyć liczbę operacji na czas, operacje muszą być wykonywane jednocześnie.

Klient planuje równoczesnych operacji przez wykonywanie operacji asynchronicznych. Następne żądanie jest uruchamiane przed zakończeniem poprzedniego żądania. Poniższy fragment kodu jest przykładem operacji wysyłania asynchronii:

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

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

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

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

Poniższy kod jest przykładem operacji odbierania asynchronii.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

Zobacz repozytorium GitHub, aby zapoznać się z przykładami pełnego <a href="https://github.com/Azure/azure-service-bus/blob/master/samples/DotNet/Microsoft.Azure.ServiceBus/SendersReceiversWithQueues" target="_blank">kodu źródłowego: <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>

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

Obiekt `MessageReceiver` jest tworzone z ciągu połączenia, nazwa kolejki i tryb odbierania wyglądu wglądu. Następnie wystąpienie `receiver` jest używane do rejestrowania obsługi wiadomości.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

Zobacz repozytorium GitHub, aby zapoznać się z przykładami pełnego <a href="https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/SendersReceiversWithQueues" target="_blank">kodu źródłowego: <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>

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

Tworzy `MessagingFactory` `factory` obiekt z ciągu połączenia. W `factory` wystąpieniu `MessageReceiver` jest tworzone wystąpienie. Następnie wystąpienie `receiver` jest używane do rejestrowania programu obsługi wiadomości.

---

## <a name="receive-mode"></a>Tryb odbierania

Podczas tworzenia kolejki lub klienta subskrypcji można określić tryb odbierania: *Peek-lock* lub *Receive i Delete*. Domyślnym trybem `PeekLock`odbierania jest . Podczas pracy w trybie domyślnym klient wysyła żądanie odebrania wiadomości z usługi Service Bus. Po odebraniu wiadomości przez klienta wysyła żądanie ukończenia wiadomości.

Podczas ustawiania trybu `ReceiveAndDelete`odbierania na , oba kroki są łączone w jednym żądaniu. Te kroki zmniejszają ogólną liczbę operacji i mogą poprawić ogólną przepływność komunikatów. Ten przyrost wydajności wiąże się z ryzykiem utraty wiadomości.

Usługa Service Bus nie obsługuje transakcji operacji odbierania i usuwania. Ponadto semantyka zaglądania do blokady są wymagane dla wszystkich scenariuszy, w których klient chce odroczyć lub [dead-letter](service-bus-dead-letter-queues.md) wiadomości.

## <a name="client-side-batching"></a>Wsadowanie po stronie klienta

Przetwarzanie wsadowe po stronie klienta umożliwia klientowi kolejki lub tematu opóźnienie wysłania wiadomości przez określony czas. Jeśli klient wysyła dodatkowe komunikaty w tym okresie, przesyła komunikaty w jednej partii. Przetwarzanie wsadowe po stronie klienta powoduje również, że kolejka lub klient subskrypcji wsaduje wiele **kompletnych** żądań w jednym żądaniu. Przetwarzanie wsadowe jest dostępne tylko dla operacji asynchronicznych **wysyłania** i **wypełniania.** Operacje synchroniczne są natychmiast wysyłane do usługi Service Bus. Przetwarzanie wsadowe nie występuje dla operacji wglądu lub odbierania, ani nie występuje przetwarzanie wsadowe między klientami.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

Funkcja przetwarzania wsadowego dla .NET Standard SDK, nie udostępnia jeszcze właściwości do manipulowania.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

Domyślnie klient używa interwału partii 20 ms. Interwał wsadowy można zmienić, ustawiając właściwość [BatchFlushInterval][BatchFlushInterval] przed utworzeniem fabryki obsługi wiadomości. To ustawienie dotyczy wszystkich klientów utworzonych przez tę fabrykę.

Aby wyłączyć przetwarzanie wsadowe, należy ustawić właściwość [BatchFlushInterval][BatchFlushInterval] na **TimeSpan.Zero**. Przykład:

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

Przetwarzanie wsadowe nie wpływa na liczbę operacji obsługi wiadomości podlegających rozliczaniu i jest dostępne tylko dla protokołu klienta usługi Service Bus przy użyciu biblioteki [Microsoft.ServiceBus.Messaging.](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) Protokół HTTP nie obsługuje przetwarzania wsadowego.

> [!NOTE]
> Ustawienie `BatchFlushInterval` gwarantuje, że przetwarzanie wsadowe jest niejawne z punktu widzenia aplikacji. tj. aplikacja wykonuje `SendAsync` `CompleteAsync` i wywołuje i nie wykonuje określonych wywołań partii.
>
> Jawne przetwarzanie wsadowe po stronie klienta można zaimplementować przy użyciu poniższego wywołania metody:
> ```csharp
> Task SendBatchAsync(IEnumerable<BrokeredMessage> messages);
> ```
> W tym miejscu łączny rozmiar wiadomości musi być mniejszy niż maksymalny rozmiar obsługiwany przez warstwę cenową.

---

## <a name="batching-store-access"></a>Dostęp do magazynu wsadowego

Aby zwiększyć przepływność kolejki, tematu lub subskrypcji, usługa Service Bus partii wiele wiadomości, gdy zapisuje do magazynu wewnętrznego. Jeśli jest włączona w kolejce lub temacie, pisanie wiadomości do magazynu będzie wsadowe. Jeśli ta opcja jest włączona w kolejce lub w ramach subskrypcji, usunięcie wiadomości ze sklepu zostanie wsadowe. Jeśli dostęp do magazynu wsadowego jest włączony dla jednostki, usługa Service Bus opóźnia operację zapisu magazynu dotyczącą tej jednostki o maksymalnie 20 ms.

> [!NOTE]
> Nie ma ryzyka utraty wiadomości z przetwarzania wsadowego, nawet jeśli występuje błąd usługi Service Bus na końcu interwału wsadowego 20ms.

Dodatkowe operacje magazynu, które występują w tym przedziale są dodawane do partii. Dostęp do magazynu wsadowego dotyczy tylko operacji **wysyłania** i **wypełniania;** nie ma to wpływu na operacje odbioru. Dostęp do magazynu wsadowego jest właściwością jednostki. Przetwarzanie wsadowe występuje we wszystkich jednostkach, które umożliwiają dostęp do magazynu wsadowego.

Podczas tworzenia nowej kolejki, tematu lub subskrypcji dostęp do magazynu wsadowego jest domyślnie włączony.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

Aby wyłączyć dostęp do magazynu wsadowego, `ManagementClient`musisz mieć wystąpienie pliku . Utwórz kolejkę z opisu `EnableBatchedOperations` kolejki, który ustawia właściwość na `false`.

```csharp
var queueDescription = new QueueDescription(path)
{
    EnableBatchedOperations = false
};
var queue = await managementClient.CreateQueueAsync(queueDescription);
```

Aby uzyskać więcej informacji, zobacz następujące tematy:
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.queuedescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.Management.QueueDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.subscriptiondescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.Management.SubscriptionDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.topicdescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.Management.TopicDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

Aby wyłączyć dostęp do magazynu wsadowego, `NamespaceManager`musisz mieć wystąpienie pliku . Utwórz kolejkę z opisu `EnableBatchedOperations` kolejki, który ustawia właściwość na `false`.

```csharp
var queueDescription = new QueueDescription(path)
{
    EnableBatchedOperations = false
};
var queue = namespaceManager.CreateQueue(queueDescription);
```

Aby uzyskać więcej informacji, zobacz następujące tematy:
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.QueueDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.SubscriptionDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.topicdescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.TopicDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---

Dostęp do magazynu wsadowego nie wpływa na liczbę operacji obsługi wiadomości podlegania rozliczaniu i jest właściwością kolejki, tematu lub subskrypcji. Jest niezależna od trybu odbierania i protokołu, który jest używany między klientem a usługą Service Bus.

## <a name="prefetching"></a>Prefetching

[Pobierania z fragmentem wstępnym](service-bus-prefetch.md) umożliwia kolejki lub klienta subskrypcji, aby załadować dodatkowe komunikaty z usługi, gdy wykonuje operację odbierania. Klient przechowuje te komunikaty w lokalnej pamięci podręcznej. Rozmiar pamięci podręcznej jest `QueueClient.PrefetchCount` określany przez lub `SubscriptionClient.PrefetchCount` właściwości. Każdy klient, który umożliwia wstępne ustalanie utrzymuje własną pamięć podręczną. Pamięć podręczna nie jest współużytkowana przez klientów. Jeśli klient inicjuje operację odbierania, a jego pamięć podręczna jest pusta, usługa przesyła partię komunikatów. Rozmiar partii jest równy rozmiar pamięci podręcznej lub 256 KB, w zależności od tego, która z tych wartości jest mniejsza. Jeśli klient inicjuje operację odbierania, a pamięć podręczna zawiera komunikat, wiadomość jest pobierana z pamięci podręcznej.

Gdy wiadomość jest wstępnie nacięta, usługa blokuje wstępnie naciącony komunikat. W zamku wiadomość z nadszytym nie może zostać odebrana przez inny odbiornik. Jeśli odbiorca nie może ukończyć wiadomości przed wygaśnięciem blokady, wiadomość staje się dostępna dla innych odbiorców. Wstępnie naciącona kopia wiadomości pozostaje w pamięci podręcznej. Odbiornik, który zużywa wygasłą kopię buforowaną otrzyma wyjątek podczas próby wykonania tej wiadomości. Domyślnie blokada wiadomości wygasa po 60 sekundach. Tę wartość można przedłużyć do 5 minut. Aby zapobiec zużyciu wygasłych komunikatów, rozmiar pamięci podręcznej powinien być zawsze mniejszy niż liczba komunikatów, które mogą być używane przez klienta w przedziale limitów czasu blokady.

W przypadku korzystania z domyślnego wygaśnięcia blokady `PrefetchCount` 60 sekund, dobra wartość jest 20 razy maksymalna szybkość przetwarzania wszystkich odbiorników fabryki. Na przykład fabryka tworzy trzy odbiorniki, a każdy odbiornik może przetwarzać do 10 komunikatów na sekundę. Liczba zdobycza nie powinna przekraczać 20 X 3 x 10 = 600. Domyślnie `PrefetchCount` jest ustawiona na 0, co oznacza, że żadne dodatkowe komunikaty nie są pobierane z usługi.

Wstępne pobierania wiadomości zwiększa ogólną przepływność dla kolejki lub subskrypcji, ponieważ zmniejsza ogólną liczbę operacji wiadomości lub rund. Pobieranie pierwszej wiadomości potrwa jednak dłużej (ze względu na zwiększony rozmiar wiadomości). Odbieranie wiadomości z nadajeńczów z nadajedzą szybciej, ponieważ te wiadomości zostały już pobrane przez klienta.

Właściwość czasu wygaśnięcia wiadomości jest sprawdzana przez serwer w momencie, gdy serwer wysyła wiadomość do klienta. Klient nie sprawdza właściwości TTL wiadomości po odebraniu wiadomości. Zamiast tego wiadomość może zostać odebrana, nawet jeśli czas wygaśnięcia wiadomości przeszedł, gdy wiadomość została buforowana przez klienta.

Wstępne naliczanie nie wpływa na liczbę operacji obsługi wiadomości podlegających rozliczaniu i jest dostępne tylko dla protokołu klienta usługi Service Bus. Protokół HTTP nie obsługuje wstępnego naciągu. Wstępnefetching jest dostępny zarówno dla operacji synchronicznych i asynchronicznych odbierania.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

Aby uzyskać więcej informacji, zobacz następujące `PrefetchCount` właściwości:

* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.QueueClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.servicebus.subscriptionclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.SubscriptionClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

Aby uzyskać więcej informacji, zobacz następujące `PrefetchCount` właściwości:

* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicebus.messaging.queueclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.QueueClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.SubscriptionClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---

## <a name="prefetching-and-receivebatch"></a>Wstępnefetching i ReceiveBatch

> [!NOTE]
> Ta sekcja dotyczy tylko windowsazure.ServiceBus SDK, jak Microsoft.Azure.ServiceBus SDK nie udostępnia funkcji wsadowych.

Podczas gdy pojęcia wstępnego naciąwania wielu wiadomości razem mają podobną semantykę do przetwarzania wiadomości w partii (`ReceiveBatch`), istnieją pewne drobne różnice, które muszą być zachowane w pamięci podczas łączenia ich razem.

Prefetch jest konfiguracją (lub trybem) `SubscriptionClient`na `ReceiveBatch` kliencie (`QueueClient` i ) i jest operacją (która ma semantykę żądania i odpowiedzi).

Korzystając z nich łącznie, należy wziąć pod uwagę następujące

* Punkt z przedpremierowy powinien być większy lub równy liczbie `ReceiveBatch`wiadomości, które mają być odbierane od pliku .
* Przedfetchaj może być maksymalnie n/3 razy większa od liczby wiadomości przetwarzanych na sekundę, gdzie n jest domyślnym czasem blokady.

Istnieją pewne wyzwania związane z chciwym podejściem (tj. utrzymującą bardzo wysoką liczbę pobierań przedbrań), ponieważ oznacza to, że wiadomość jest zablokowana dla określonego odbiornika. Zalecenie jest wypróbowanie wartości z góry między progami wymienionych powyżej i empirycznie zidentyfikować, co pasuje.

## <a name="multiple-queues"></a>Wiele kolejek

Jeśli oczekiwane obciążenie nie może być obsługiwane przez pojedynczą kolejkę lub temat, należy użyć wielu jednostek obsługi wiadomości. Korzystając z wielu jednostek, należy utworzyć dedykowanego klienta dla każdej jednostki, zamiast używać tego samego klienta dla wszystkich jednostek.

## <a name="development-and-testing-features"></a>Funkcje rozwoju i testowania

> [!NOTE]
> Ta sekcja dotyczy tylko windowsazure.ServiceBus SDK, jak Microsoft.Azure.ServiceBus SDK nie udostępnia tej funkcji.

Usługa Service Bus ma jedną funkcję, używaną specjalnie do rozwoju, która **nigdy nie powinna być używana w konfiguracjach produkcyjnych:** [`TopicDescription.EnableFilteringMessagesBeforePublishing`][TopicDescription.EnableFiltering].

Po dodaniu nowych reguł lub filtrów do [`TopicDescription.EnableFilteringMessagesBeforePublishing`][TopicDescription.EnableFiltering] tematu można użyć, aby sprawdzić, czy nowe wyrażenie filtru działa zgodnie z oczekiwaniami.

## <a name="scenarios"></a>Scenariusze

W poniższych sekcjach opisano typowe scenariusze obsługi wiadomości i opisano preferowane ustawienia usługi Service Bus. Współczynniki przepływności są klasyfikowane jako małe (mniej niż 1 wiadomość/sekundę), umiarkowane (1 wiadomość/sekundę lub większa, ale mniej niż 100 wiadomości/sekundę) i wysokie (100 wiadomości/sekundę lub więcej). Liczba klientów jest klasyfikowana jako mała (5 lub mniejsza), umiarkowana (więcej niż 5, ale mniejsza lub równa 20) i duża (ponad 20).

### <a name="high-throughput-queue"></a>Kolejka o wysokiej przepływie

Cel: Maksymalizuj przepływność pojedynczej kolejki. Liczba nadawców i odbiorników jest niewielka.

* Aby zwiększyć ogólną szybkość wysyłania do kolejki, użyj wielu fabryk wiadomości, aby utworzyć nadawców. Dla każdego nadawcy należy użyć operacji asynchronicznych lub wielu wątków.
* Aby zwiększyć ogólną szybkość odbierania z kolejki, użyj wielu fabryk wiadomości do utworzenia odbiorników.
* Użyj operacji asynchronicznych, aby skorzystać z przetwarzania wsadowego po stronie klienta.
* Ustaw interwał przetwarzania wsadowego na 50 ms, aby zmniejszyć liczbę transmisji protokołu klienta usługi Service Bus. Jeśli używanych jest wielu nadawców, należy zwiększyć interwał wsadowego do 100 ms.
* Pozostaw wsadowy dostęp do magazynu włączony. Ten dostęp zwiększa ogólną szybkość, z jaką wiadomości mogą być zapisywane w kolejce.
* Ustaw liczbę zdobycz wstępną na 20-krotność maksymalnej szybkości przetwarzania wszystkich odbiorników fabryki. Ta liczba zmniejsza liczbę transmisji protokołu klienta usługi Service Bus.

### <a name="multiple-high-throughput-queues"></a>Wiele kolejek o wysokiej przepływności

Cel: Maksymalizuj ogólną przepustowość wielu kolejek. Przepływność pojedynczej kolejki jest umiarkowana lub wysoka.

Aby uzyskać maksymalną przepływność w wielu kolejkach, użyj opisanych ustawień, aby zmaksymalizować przepływność pojedynczej kolejki. Ponadto należy użyć różnych fabryk do tworzenia klientów, którzy wysyłają lub odbierają z różnych kolejek.

### <a name="low-latency-queue"></a>Kolejka małych opóźnień

Cel: Minimalizuj opóźnienia end-to-end kolejki lub tematu. Liczba nadawców i odbiorników jest niewielka. Przepływność kolejki jest mała lub umiarkowana.

* Wyłącz przetwarzanie wsadowe po stronie klienta. Klient natychmiast wysyła wiadomość.
* Wyłącz dostęp do magazynu wsadowego. Usługa natychmiast zapisuje komunikat do magazynu.
* Jeśli używasz jednego klienta, ustaw liczbę z przedsprzedaży na 20-krotność szybkości przetwarzania odbiornika. Jeśli wiele wiadomości dociera do kolejki w tym samym czasie, protokół klienta usługi Service Bus przesyła je wszystkie w tym samym czasie. Gdy klient odbiera następną wiadomość, ta wiadomość jest już w lokalnej pamięci podręcznej. Pamięć podręczna powinna być mała.
* Jeśli używasz wielu klientów, ustaw liczbę z przedsprzedaży na 0. Ustawiając liczbę, drugi klient może odbierać drugą wiadomość, podczas gdy pierwszy klient nadal przetwarza pierwszą wiadomość.

### <a name="queue-with-a-large-number-of-senders"></a>Kolejka z dużą liczbą nadawców

Cel: Maksymalizuj przepływność kolejki lub tematu z dużą liczbą nadawców. Każdy nadawca wysyła wiadomości o umiarkowanej szybkości. Liczba odbiorników jest niewielka.

Usługa Service Bus umożliwia maksymalnie 1000 równoczesnych połączeń z jednostką obsługi wiadomości (lub 5000 przy użyciu usługi AMQP). Ten limit jest wymuszany na poziomie obszaru nazw, a kolejki/tematy/subskrypcje są ograniczone przez limit równoczesnych połączeń na obszar nazw. W przypadku kolejek liczba ta jest współużytkowana przez nadawców i odbiorców. Jeśli wszystkie połączenia 1000 są wymagane dla nadawców, zastąp kolejkę tematem i pojedynczą subskrypcją. Temat akceptuje do 1000 równoczesnych połączeń od nadawców, podczas gdy subskrypcja akceptuje dodatkowe 1000 równoczesnych połączeń z odbiorników. Jeśli wymagane jest więcej niż 1000 równoczesnych nadawców, nadawcy powinni wysyłać wiadomości do protokołu service bus za pośrednictwem protokołu HTTP.

Aby zmaksymalizować przepływność, wykonaj następujące czynności:

* Jeśli każdy nadawca znajduje się w innym procesie, użyj tylko jednej fabryki na proces.
* Użyj operacji asynchronicznych, aby skorzystać z przetwarzania wsadowego po stronie klienta.
* Użyj domyślnego interwału przetwarzania wsadowego wynoszącego 20 ms, aby zmniejszyć liczbę transmisji protokołu klienta usługi Service Bus.
* Pozostaw wsadowy dostęp do magazynu włączony. Ten dostęp zwiększa ogólną szybkość, z jaką wiadomości mogą być zapisywane w kolejce lub temacie.
* Ustaw liczbę zdobycz wstępną na 20-krotność maksymalnej szybkości przetwarzania wszystkich odbiorników fabryki. Ta liczba zmniejsza liczbę transmisji protokołu klienta usługi Service Bus.

### <a name="queue-with-a-large-number-of-receivers"></a>Kolejka z dużą liczbą odbiorników

Cel: Maksymalizuj szybkość odbierania kolejki lub subskrypcji z dużą liczbą odbiorników. Każdy odbiornik odbiera wiadomości z umiarkowaną szybkością. Liczba nadawców jest niewielka.

Usługa Service Bus umożliwia maksymalnie 1000 równoczesnych połączeń z encją. Jeśli kolejka wymaga więcej niż 1000 odbiorników, zastąp kolejkę tematem i wieloma subskrypcjami. Każda subskrypcja może obsługiwać maksymalnie 1000 równoczesnych połączeń. Alternatywnie, odbiorcy mogą uzyskać dostęp do kolejki za pośrednictwem protokołu HTTP.

Aby zmaksymalizować przepływność, wykonaj następujące czynności:

* Jeśli każdy odbiornik znajduje się w innym procesie, należy użyć tylko jednej fabryki na proces.
* Odbiorniki mogą używać operacji synchronicznych lub asynchronicznych. Biorąc pod uwagę umiarkowaną szybkość odbioru pojedynczego odbiorcy, przetwarzanie wsadowe po stronie klienta żądania Complete nie wpływa na przepustowość odbiornika.
* Pozostaw wsadowy dostęp do magazynu włączony. Ten dostęp zmniejsza ogólne obciążenie jednostki. Zmniejsza również ogólną szybkość, z jaką wiadomości mogą być zapisywane w kolejce lub temacie.
* Ustaw liczbę złóż zaliczki na małą wartość (na przykład PrefetchCount = 10). Ta liczba zapobiega bezczynności odbiorników, podczas gdy inne odbiorniki mają dużą liczbę wiadomości w pamięci podręcznej.

### <a name="topic-with-a-small-number-of-subscriptions"></a>Temat z niewielką liczbą subskrypcji

Cel: Maksymalizuj przepływność tematu za pomocą niewielkiej liczby subskrypcji. Wiadomość jest odbierana przez wiele subskrypcji, co oznacza, że łączna stawka otrzymywania wszystkich subskrypcji jest większa niż stawka wysyłania. Liczba nadawców jest niewielka. Liczba odbiorników na subskrypcję jest niewielka.

Aby zmaksymalizować przepływność, wykonaj następujące czynności:

* Aby zwiększyć ogólną szybkość wysyłania do tematu, użyj wielu fabryk wiadomości do tworzenia nadawców. Dla każdego nadawcy należy użyć operacji asynchronicznych lub wielu wątków.
* Aby zwiększyć ogólną szybkość otrzymywania z subskrypcji, użyj wielu fabryk wiadomości do tworzenia odbiorników. Dla każdego odbiornika należy użyć operacji asynchronicznych lub wielu wątków.
* Użyj operacji asynchronicznych, aby skorzystać z przetwarzania wsadowego po stronie klienta.
* Użyj domyślnego interwału przetwarzania wsadowego wynoszącego 20 ms, aby zmniejszyć liczbę transmisji protokołu klienta usługi Service Bus.
* Pozostaw wsadowy dostęp do magazynu włączony. Ten dostęp zwiększa ogólną szybkość, z jaką wiadomości mogą być zapisywane w temacie.
* Ustaw liczbę zdobycz wstępną na 20-krotność maksymalnej szybkości przetwarzania wszystkich odbiorników fabryki. Ta liczba zmniejsza liczbę transmisji protokołu klienta usługi Service Bus.

### <a name="topic-with-a-large-number-of-subscriptions"></a>Temat z dużą liczbą subskrypcji

Cel: Maksymalizuj przepływność tematu dzięki dużej liczbie subskrypcji. Wiadomość jest odbierana przez wiele subskrypcji, co oznacza, że łączna stawka otrzymywania wszystkich subskrypcji jest znacznie większa niż stawka wysyłania. Liczba nadawców jest niewielka. Liczba odbiorników na subskrypcję jest niewielka.

Tematy z dużą liczbą subskrypcji zazwyczaj uwidaczniają niską ogólną przepływność, jeśli wszystkie wiadomości są kierowane do wszystkich subskrypcji. Ta niska przepływność jest spowodowana faktem, że każda wiadomość jest odbierana wiele razy, a wszystkie komunikaty, które są zawarte w temacie i wszystkie jego subskrypcje są przechowywane w tym samym magazynie. Zakłada się, że liczba nadawców i liczba odbiorników na subskrypcję jest niewielka. Usługa Service Bus obsługuje do 2000 subskrypcji na temat.

Aby zmaksymalizować przepływność, spróbuj wykonać następujące czynności:

* Użyj operacji asynchronicznych, aby skorzystać z przetwarzania wsadowego po stronie klienta.
* Użyj domyślnego interwału przetwarzania wsadowego wynoszącego 20 ms, aby zmniejszyć liczbę transmisji protokołu klienta usługi Service Bus.
* Pozostaw wsadowy dostęp do magazynu włączony. Ten dostęp zwiększa ogólną szybkość, z jaką wiadomości mogą być zapisywane w temacie.
* Ustaw liczbę z wyprzedzeniem na 20-krotność oczekiwanej szybkości odbierania w sekundach. Ta liczba zmniejsza liczbę transmisji protokołu klienta usługi Service Bus.

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