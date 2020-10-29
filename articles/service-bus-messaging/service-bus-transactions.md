---
title: Przegląd przetwarzania transakcji w Azure Service Bus
description: Ten artykuł zawiera omówienie przetwarzania transakcji i funkcji wysyłania za pośrednictwem programu w Azure Service Bus.
ms.topic: article
ms.date: 10/28/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 9162b8578fe4f48cc3740b38d9d84ffaa2f260de
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2020
ms.locfileid: "92927791"
---
# <a name="overview-of-service-bus-transaction-processing"></a>Przegląd Service Bus przetwarzania transakcji

W tym artykule omówiono możliwości transakcji Microsoft Azure Service Bus. Wiele dyskusji jest zilustrowanych przez [transakcje AMQP z przykładem Service Bus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia). W tym artykule zawarto informacje dotyczące przetwarzania transakcji i funkcji *wysyłania za pośrednictwem* Service Bus, podczas gdy próbki niepodzielnych transakcji są szersze i bardziej złożone w zakresie.

## <a name="transactions-in-service-bus"></a>Transakcje w Service Bus

Grupy *transakcji* grupuje dwie lub więcej operacji jednocześnie do *zakresu wykonywania* . O charakterze taka transakcja musi zapewnić, że wszystkie operacje należące do danej grupy operacji kończą się powodzeniem lub niepowodzeniem. W tej kwestii transakcje działają jako jedna jednostka, która jest często określana jako *niepodzielność* .

Service Bus to transakcyjny Broker komunikatów i zapewnia integralność transakcyjną wszystkich operacji wewnętrznych względem magazynów komunikatów. Wszystkie transfery komunikatów wewnątrz Service Bus, takie jak przenoszenie komunikatów do [kolejki utraconych](service-bus-dead-letter-queues.md) wiadomości lub [Automatyczne przekazywanie](service-bus-auto-forwarding.md) komunikatów między jednostkami, są transakcyjne. W związku z tym, jeśli Service Bus akceptuje komunikat, został już zapisany i oznaczony numerem sekwencyjnym. Z tego miejsca wszystkie transfery komunikatów w ramach Service Bus są koordynowanymi operacjami między jednostkami i nie będą prowadzić do utraty (Źródło zakończy się pomyślnie, nie powiedzie się) lub do duplikacji (Źródło zakończy się niepowodzeniem i obiekt docelowy) komunikatu.

Usługa Service Bus obsługuje grupowanie operacji względem jednej jednostki obsługi komunikatów (kolejki, tematu, subskrypcji) w zakresie transakcji. Na przykład można wysłać kilka komunikatów do jednej kolejki z zakresu transakcji, a komunikaty zostaną przekazane do dziennika kolejki tylko po pomyślnym ukończeniu transakcji.

## <a name="operations-within-a-transaction-scope"></a>Operacje w zakresie transakcji

Operacje, które mogą być wykonywane w ramach zakresu transakcji są następujące:

* **[QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient), [MessageSender](/dotnet/api/microsoft.azure.servicebus.core.messagesender), [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient)** : `Send` , `SendAsync` , `SendBatch` ,`SendBatchAsync`
* **[BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)** :,,,,,,,, `Complete` `CompleteAsync` `Abandon` `AbandonAsync` `Deadletter` `DeadletterAsync` `Defer` `DeferAsync` `RenewLock` , `RenewLockAsync` 

Operacje odbioru nie są uwzględniane, ponieważ zakłada się, że aplikacja uzyskuje komunikaty przy użyciu trybu [ReceiveMode. PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) w ramach niektórych pętli Receive lub wywołania zwrotnego [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) , a następnie otwiera zakres transakcji w celu przetworzenia komunikatu.

Dyspozycja wiadomości (kompletna, Porzuć, utracony, odłóż) następuje w zakresie i zależy od ogólnego wyniku transakcji.

## <a name="transfers-and-send-via"></a>Transfery i "wysyłanie przez"

Aby włączyć transakcyjną przekazują danych z kolejki lub tematu do procesora, a następnie do innej kolejki lub tematu, Service Bus obsługuje *transfery* . W operacji transferu nadawca najpierw wysyła komunikat do *kolejki lub tematu transferu* , a kolejka transferu lub temat natychmiast przenosi komunikat do zamierzonej kolejki docelowej lub tematu za pomocą tej samej niezawodnej implementacji transferu, na której bazuje funkcja autoprzesyłania dalej. Komunikat nigdy nie jest przesyłany do kolejki transferu lub dziennika tematu w taki sposób, aby stał się widoczny dla danej kolejki transferu lub użytkowników tematu.

Moc tej możliwości transakcyjnej stanie się widoczna, gdy kolejka transferu lub sama sama sekcja jest źródłem komunikatów wejściowych nadawcy. Innymi słowy, Service Bus może przetransferować komunikat do kolejki docelowej lub tematu "za pośrednictwem kolejki transferu lub tematu, podczas gdy wykonywana jest pełna (lub odłożyć lub utracona) operacja w komunikacie wejściowym — wszystko w jednej operacji niepodzielnej. 

### <a name="see-it-in-code"></a>Zobacz go w kodzie

Aby skonfigurować takie transfery, należy utworzyć nadawcę wiadomości, który jest przeznaczony dla kolejki docelowej za pośrednictwem kolejki transferu. Istnieje również odbiornik, który pobiera wiadomości z tej samej kolejki. Przykład:

```csharp
var connection = new ServiceBusConnection(connectionString);

var sender = new MessageSender(connection, QueueName);
var receiver = new MessageReceiver(connection, QueueName);
```

Prosta transakcja następnie używa tych elementów, jak w poniższym przykładzie. Aby zapoznać się z pełnym przykładem, zapoznaj się z [kodem źródłowym w witrynie GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia):

```csharp
var receivedMessage = await receiver.ReceiveAsync();

using (var ts = new TransactionScope(TransactionScopeAsyncFlowOption.Enabled))
{
    try
    {
        // do some processing
        if (receivedMessage != null)
            await receiver.CompleteAsync(receivedMessage.SystemProperties.LockToken);

        var myMsgBody = new MyMessage
        {
            Name = "Some name",
            Address = "Some street address",
            ZipCode = "Some zip code"
        };

        // send message
        var message = myMsgBody.AsMessage();
        await sender.SendAsync(message).ConfigureAwait(false);
        Console.WriteLine("Message has been sent");

        // complete the transaction
        ts.Complete();
    }
    catch (Exception ex)
    {
        // This rolls back send and complete in case an exception happens
        ts.Dispose();
        Console.WriteLine(ex.ToString());
    }
}
```

## <a name="timeout"></a>Limit czasu
Transakcja zostanie przeprowadzona po upływie 2 minut. Czasomierz transakcji jest uruchamiany po rozpoczęciu pierwszej operacji w transakcji. 

## <a name="next-steps"></a>Następne kroki

Więcej informacji o kolejkach Service Bus można znaleźć w następujących artykułach:

* [Jak używać kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Łączenie Service Bus jednostek z funkcją autoprzesyłania dalej](service-bus-auto-forwarding.md)
* [Próbkowanie progresywne](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AutoForward)
* [Transakcje niepodzielne z przykładem Service Bus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions)
* [Porównanie kolejek platformy Azure i kolejek Service Bus](service-bus-azure-and-service-bus-queues-compared-contrasted.md)


