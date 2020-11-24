---
title: Rozpoczynanie pracy z kolejkami Azure Service Bus (Azure. Messaging. ServiceBus)
description: W tym samouczku utworzysz aplikację .NET Core w języku C# umożliwiającą wysyłanie komunikatów do i odbieranie komunikatów z kolejki Service Bus.
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 11/13/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 59dd1dadc7d037ff253812e4d3e1a1955d98e944
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95809138"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-net"></a>Wysyłanie komunikatów do i odbieranie komunikatów z kolejek Azure Service Bus (.NET)
W tym samouczku utworzysz aplikację konsolową .NET Core w celu wysyłania komunikatów do i odbierania komunikatów z kolejki Service Bus przy użyciu pakietu **Azure. Messaging. ServiceBus** . 

> [!Important]
> Ten przewodnik Szybki Start używa nowego pakietu Azure. Messaging. ServiceBus. Aby uzyskać szybki Start, który używa starego pakietu Microsoft. Azure. ServiceBus, zobacz [wysyłanie i odbieranie zdarzeń za pomocą pakietu Microsoft. Azure. ServiceBus](service-bus-dotnet-get-started-with-queues-legacy.md).

## <a name="prerequisites"></a>Wymagania wstępne

- [Visual Studio 2019](https://www.visualstudio.com/vs)
- Subskrypcja platformy Azure. Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Możesz aktywować korzyści dla [subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Jeśli nie masz kolejki do współpracy z programem, postępuj zgodnie z instrukcjami w temacie [Use Azure Portal, aby utworzyć](service-bus-quickstart-portal.md) kolejkę Service Bus w celu utworzenia kolejki. Zanotuj **Parametry połączenia** dla przestrzeni nazw Service Bus i nazwę utworzonej **kolejki** .

## <a name="send-messages-to-a-queue"></a>Wysyłanie komunikatów do kolejki
W tym przewodniku szybki start utworzysz aplikację konsolową w języku C# .NET Core, która będzie wysyłać komunikaty do kolejki.

### <a name="create-a-console-application"></a>Tworzenie aplikacji konsolowej
Uruchom program Visual Studio i Utwórz nowy projekt **Aplikacja konsolowa (.NET Core)** dla języka C#. 

### <a name="add-the-service-bus-nuget-package"></a>Dodawanie pakietu NuGet usługi Service Bus

1. Kliknij prawym przyciskiem myszy nowo utworzony projekt i wybierz pozycję **Zarządzaj pakietami NuGet**.
1. Wybierz pozycję **Przeglądaj**. Wyszukaj i wybierz pozycję **[Azure. Messaging. ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/)**.
1. Wybierz pozycję **Zainstaluj** , aby ukończyć instalację, a następnie zamknij Menedżera pakietów NuGet.

### <a name="add-code-to-send-messages-to-the-queue"></a>Dodawanie kodu w celu wysyłania komunikatów do kolejki

1. W *program.cs* Dodaj następujące `using` instrukcje w górnej części definicji przestrzeni nazw przed deklaracją klasy:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    
    using Azure.Messaging.ServiceBus;
    ```

1. W `Program` klasie należy zadeklarować następujące zmienne:

    ```csharp
        static string connectionString = "<NAMESPACE CONNECTION STRING>";
        static string queueName = "<QUEUE NAME>";
    ```

    Zamień na `<NAMESPACE CONNECTION STRING>` Parametry połączenia z przestrzenią nazw Service Bus. I Zamień na `<QUEUE NAME>` nazwę kolejki.     
2. Dodaj metodę o nazwie `SendMessageAsync` , która wysyła jeden komunikat do kolejki. 

    ```csharp
        static async Task SendMessageAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a sender for the queue 
                ServiceBusSender sender = client.CreateSender(queueName);

                // create a message that we can send
                ServiceBusMessage message = new ServiceBusMessage("Hello world!");

                // send the message
                await sender.SendMessageAsync(message);
                Console.WriteLine($"Sent a single message to the queue: {queueName}");
            }
        }
    ```
1. Dodaj metodę o nazwie `CreateMessages` , aby utworzyć kolejkę (kolejkę .NET queue) komunikatów do `Program` klasy. Zwykle te komunikaty są uzyskiwane z różnych części aplikacji. Tutaj tworzymy kolejkę przykładowych wiadomości.

    ```csharp
        static Queue<ServiceBusMessage> CreateMessages()
        {
            // create a queue containing the messages and return it to the caller
            Queue<ServiceBusMessage> messages = new Queue<ServiceBusMessage>();
            messages.Enqueue(new ServiceBusMessage("First message"));
            messages.Enqueue(new ServiceBusMessage("Second message"));
            messages.Enqueue(new ServiceBusMessage("Third message"));
            return messages;
        }
    ```
1. Dodaj metodę o nazwie `SendMessageBatchAsync` do `Program` klasy i Dodaj następujący kod. Ta metoda pobiera kolejkę komunikatów i przygotowuje co najmniej jedną partię do wysłania do kolejki Service Bus. 

    ```csharp
        static async Task SendMessageBatchAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a sender for the queue 
                ServiceBusSender sender = client.CreateSender(queueName);

                // get the messages to be sent to the Service Bus queue
                Queue<ServiceBusMessage> messages = CreateMessages();

                // total number of messages to be sent to the Service Bus queue
                int messageCount = messages.Count;

                // while all messages are not sent to the Service Bus queue
                while (messages.Count > 0)
                {
                    // start a new batch 
                    using ServiceBusMessageBatch messageBatch = await sender.CreateMessageBatchAsync();

                    // add the first message to the batch
                    if (messageBatch.TryAddMessage(messages.Peek()))
                    {
                        // dequeue the message from the .NET queue once the message is added to the batch
                        messages.Dequeue();
                    }
                    else
                    {
                        // if the first message can't fit, then it is too large for the batch
                        throw new Exception($"Message {messageCount - messages.Count} is too large and cannot be sent.");
                    }

                    // add as many messages as possible to the current batch
                    while (messages.Count > 0 && messageBatch.TryAddMessage(messages.Peek()))
                    {
                        // dequeue the message from the .NET queue as it has been added to the batch
                        messages.Dequeue();
                    }
        
                    // now, send the batch
                    await sender.SendMessagesAsync(messageBatch);
        
                    // if there are any remaining messages in the .NET queue, the while loop repeats 
                }

                Console.WriteLine($"Sent a batch of {messageCount} messages to the topic: {queueName}");
            }
        }
    ```
1. Zastąp `Main()` metodę następującą metodą **asynchroniczną** `Main` . Wywołuje obydwie metody wysyłania, aby wysyłać pojedyncze wiadomości i partie komunikatów do kolejki. 

    ```csharp
        static async Task Main()
        {
            // send a message to the queue
            await SendMessageAsync();

            // send a batch of messages to the queue
            await SendMessageBatchAsync();
        }
    ```
5. Uruchom aplikację. Powinny pojawić się następujące komunikaty. 

    ```console
    Sent a single message to the queue: myqueue
    Sent a batch of messages to the queue: myqueue
    ```       
1. W Azure Portal wykonaj następujące kroki:
    1. Przejdź do przestrzeni nazw Service Bus. 
    1. Na stronie **Przegląd** wybierz kolejkę w środkowym dolnym okienku. 
    1. Zwróć uwagę na wartości w sekcji **podstawy** .

    :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/sent-messages-essentials.png" alt-text="Odebrane komunikaty z liczbą i rozmiarem" lightbox="./media/service-bus-dotnet-get-started-with-queues/sent-messages-essentials.png":::

    Zwróć uwagę na następujące wartości:
    - Wartość **liczby aktywnych komunikatów** dla kolejki wynosi teraz **4**. Za każdym razem, gdy uruchamiasz tę aplikację nadawcy bez pobierania komunikatów, ta wartość rośnie o 4.
    - Bieżący rozmiar kolejki zwiększa **bieżącą** wartość w programie **Essentials**  za każdym razem, gdy aplikacja dodaje komunikaty do kolejki.
    - Na wykresie **komunikaty** w dolnej sekcji **metryk** można zobaczyć, że dla kolejki istnieją cztery przychodzące komunikaty. 

## <a name="receive-messages-from-a-queue"></a>Odbieranie komunikatów z kolejki
W tej sekcji dodasz kod umożliwiający pobieranie komunikatów z kolejki.

1. Dodaj następujące metody do `Program` klasy, która obsługuje komunikaty i wszelkie błędy. 

    ```csharp
        // handle received messages
        static async Task MessageHandler(ProcessMessageEventArgs args)
        {
            string body = args.Message.Body.ToString();
            Console.WriteLine($"Received: {body}");

            // complete the message. messages is deleted from the queue. 
            await args.CompleteMessageAsync(args.Message);
        }

        // handle any errors when receiving messages
        static Task ErrorHandler(ProcessErrorEventArgs args)
        {
            Console.WriteLine(args.Exception.ToString());
            return Task.CompletedTask;
        }
    ```
1. Dodaj metodę o nazwie `ReceiveMessagesAsync` do `Program` klasy i Dodaj następujący kod, aby odbierać komunikaty. 

    ```csharp
        static async Task ReceiveMessagesAsync()
        {
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a processor that we can use to process the messages
                ServiceBusProcessor processor = client.CreateProcessor(queueName, new ServiceBusProcessorOptions());

                // add handler to process messages
                processor.ProcessMessageAsync += MessageHandler;

                // add handler to process any errors
                processor.ProcessErrorAsync += ErrorHandler;

                // start processing 
                await processor.StartProcessingAsync();

                Console.WriteLine("Wait for a minute and then press any key to end the processing");
                Console.ReadKey();

                // stop processing 
                Console.WriteLine("\nStopping the receiver...");
                await processor.StopProcessingAsync();
                Console.WriteLine("Stopped receiving messages");
            }
        }
    ```
1. Dodaj wywołanie `ReceiveMessagesAsync` metody z `Main` metody. Dodaj komentarz do `SendMessagesAsync` metody, jeśli chcesz przetestować tylko otrzymywanie komunikatów. Jeśli tego nie zrobisz, zobaczysz kolejne cztery komunikaty wysyłane do kolejki. 

    ```csharp
        static async Task Main()
        {
            // send a message to the queue
            await SendMessageAsync();

            // send a batch of messages to the queue
            await SendMessageBatchAsync();

            // receive message from the queue
            await ReceiveMessagesAsync();
        }
    ```

## <a name="run-the-app"></a>Uruchamianie aplikacji
Uruchom aplikację. Poczekaj chwilę, a następnie naciśnij dowolny klawisz, aby przerwać otrzymywanie komunikatów. Powinny pojawić się następujące dane wyjściowe (spacja dla klucza). 

```console
Sent a single message to the queue: myqueue
Sent a batch of messages to the queue: myqueue
Wait for a minute and then press any key to end the processing
Received: Hello world!
Received: First message in the batch
Received: Second message in the batch
Received: Third message in the batch
Received: Hello world!
Received: First message in the batch
Received: Second message in the batch
Received: Third message in the batch

Stopping the receiver...
Stopped receiving messages
```

Ponownie sprawdź Portal. 

- **Liczba aktywnych komunikatów** i **bieżące** wartości to teraz **0**.
- Na wykresie **komunikaty** w dolnej sekcji **metryk** można zobaczyć, że istnieje osiem komunikatów przychodzących i ośmiu komunikatów wychodzących dla kolejki. 

    :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/queue-messages-size-final.png" alt-text="Aktywne komunikaty i rozmiar po odebraniu" lightbox="./media/service-bus-dotnet-get-started-with-queues/queue-messages-size-final.png":::

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z poniższą dokumentacją i przykładami:

- [Azure Service Busa Biblioteka kliencka dla platformy .NET — plik Readme](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus)
- [Przykłady w serwisie GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [Dokumentacja interfejsów API platformy .NET](https://docs.microsoft.com/dotnet/api/azure.messaging.servicebus?view=azure-dotnet-preview&preserve-view=true)

