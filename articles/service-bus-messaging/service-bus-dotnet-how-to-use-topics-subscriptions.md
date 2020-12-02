---
title: Wysyłanie komunikatów do tematów Azure Service Bus przy użyciu usługi Azure-Messaging-ServiceBus
description: W tym przewodniku szybki start pokazano, jak wysyłać komunikaty do Azure Service Bus tematów za pomocą pakietu Azure-Messaging-ServiceBus.
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 11/13/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 5dfdf846410e9b622f3ef1e9006aa9846de9979c
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498714"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-net"></a>Wysyłanie komunikatów do tematu Azure Service Bus i odbieranie komunikatów z subskrypcji do tematu (.NET)
W tym samouczku przedstawiono sposób tworzenia aplikacji konsolowej .NET Core, która wysyła komunikaty do Service Bus tematu i odbiera komunikaty z subskrypcji tematu. 

> [!Important]
> Ten przewodnik Szybki Start używa nowego pakietu **Azure. Messaging. ServiceBus** . Przewodnik Szybki Start, który używa starego pakietu Microsoft. Azure. ServiceBus, znajduje się w temacie [wysyłanie i odbieranie komunikatów za pomocą pakietu Microsoft. Azure. ServiceBus](service-bus-dotnet-how-to-use-topics-subscriptions-legacy.md).

## <a name="prerequisites"></a>Wymagania wstępne

- [Visual Studio 2019](https://www.visualstudio.com/vs)
- Subskrypcja platformy Azure. Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Możesz aktywować korzyści dla [subskrybentów programu Visual Studio lub MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Wykonaj kroki opisane w [przewodniku szybki start: użyj Azure Portal, aby utworzyć temat Service Bus i subskrypcje w temacie](service-bus-quickstart-topics-subscriptions-portal.md). Zanotuj parametry połączenia, nazwę tematu i nazwę subskrypcji. W tym przewodniku szybki start będziesz używać tylko jednej subskrypcji. 

## <a name="send-messages-to-a-topic"></a>Wysyłanie komunikatów do tematu
W tej sekcji utworzysz aplikację konsolową .NET Core w programie Visual Studio, dodając kod do wysyłania wiadomości do utworzonego tematu. 

### <a name="create-a-console-application"></a>Tworzenie aplikacji konsolowej
Uruchom program Visual Studio i Utwórz nowy projekt **Aplikacja konsolowa (.NET Core)** dla języka C#. 

### <a name="add-the-service-bus-nuget-package"></a>Dodawanie pakietu NuGet usługi Service Bus

1. Kliknij prawym przyciskiem myszy nowo utworzony projekt i wybierz pozycję **Zarządzaj pakietami NuGet**.
1. Wybierz pozycję **Przeglądaj**. Wyszukaj i wybierz pozycję **[Azure. Messaging. ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/)**.
1. Wybierz pozycję **Zainstaluj** , aby ukończyć instalację, a następnie zamknij Menedżera pakietów NuGet.

### <a name="add-code-to-send-messages-to-the-topic"></a>Dodawanie kodu w celu wysyłania komunikatów do tematu 

1. W pliku Program.cs dodaj następujące instrukcje `using` na początku definicji przestrzeni nazw przed deklaracją klasy:
   
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using Azure.Messaging.ServiceBus;
    ```
1. W `Program` klasie należy zadeklarować następujące zmienne:

    ```csharp
        static string connectionString = "<NAMESPACE CONNECTION STRING>";
        static string topicName = "<TOPIC NAME>";
        static string subscriptionName = "<SUBSCRIPTION NAME>";
    ```

    Zastąp następujące wartości:
    - `<NAMESPACE CONNECTION STRING>` z parametrami połączenia do przestrzeni nazw Service Bus
    - `<TOPIC NAME>` o nazwie tematu
    - `<SUBSCRIPTION NAME>` z nazwą subskrypcji
2. Dodaj metodę o nazwie `SendMessageToTopicAsync` , która wysyła jeden komunikat do tematu. 

    ```csharp
        static async Task SendMessageToTopicAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a sender for the topic
                ServiceBusSender sender = client.CreateSender(topicName);
                await sender.SendMessageAsync(new ServiceBusMessage("Hello, World!"));
                Console.WriteLine($"Sent a single message to the topic: {topicName}");
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
1. Dodaj metodę o nazwie `SendMessageBatchAsync` do `Program` klasy i Dodaj następujący kod. Ta metoda pobiera kolejkę komunikatów i przygotowuje co najmniej jedną partię do wysłania do tematu Service Bus. 

    ```csharp
        static async Task SendMessageBatchToTopicAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {

                // create a sender for the topic 
                ServiceBusSender sender = client.CreateSender(topicName);

                // get the messages to be sent to the Service Bus topic
                Queue<ServiceBusMessage> messages = CreateMessages();

                // total number of messages to be sent to the Service Bus topic
                int messageCount = messages.Count;

                // while all messages are not sent to the Service Bus topic
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

                Console.WriteLine($"Sent a batch of {messageCount} messages to the topic: {topicName}");
            }
        }
    ```
1. Zastąp `Main()` metodę następującą metodą **asynchroniczną** `Main` . Wywołuje obydwie metody wysyłania, aby wysyłać pojedyncze wiadomości i partie komunikatów do tematu.  

    ```csharp
        static async Task Main()
        {
            // send a message to the topic
            await SendMessageToTopicAsync();

            // send a batch of messages to the topic
            await SendMessageBatchToTopicAsync();
        }
    ```
5. Uruchom aplikację. Powinny zostać wyświetlone następujące dane wyjściowe:

    ```console
    Sent a single message to the topic: mytopic
    Sent a batch of 3 messages to the topic: mytopic
    ```
1. W Azure Portal wykonaj następujące kroki:
    1. Przejdź do przestrzeni nazw Service Bus. 
    1. Na stronie **Przegląd** w środkowym okienku przejdź do karty **Tematy** i wybierz temat Service Bus. W poniższym przykładzie jest to `mytopic` .
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/select-topic.png" alt-text="Wybieranie tematu":::
    1. Na stronie **tematu Service Bus** na wykresie **komunikaty** w dolnej sekcji **metryk** można zobaczyć, że w temacie znajdują się cztery komunikaty przychodzące. Jeśli nie widzisz wartości, poczekaj kilka minut i Odśwież stronę, aby zobaczyć zaktualizowany wykres. 

        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/sent-messages-essentials.png" alt-text="Komunikaty wysyłane do tematu" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/sent-messages-essentials.png":::
    4. Wybierz subskrypcję w dolnym okienku. W poniższym przykładzie jest to **S1**. Na stronie **subskrypcja Service Bus** zostanie wyświetlona **Liczba aktywnych komunikatów** jako **4**. Subskrypcja otrzymała cztery wiadomości, które zostały wysłane do tematu, ale nie pobrały jeszcze odbiorcy. 
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page.png" alt-text="Komunikaty odebrane w ramach subskrypcji" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page.png":::
    

## <a name="receive-messages-from-a-subscription"></a>Odbieranie komunikatów z subskrypcji

1. Dodaj następujące metody do `Program` klasy, która obsługuje komunikaty i wszelkie błędy. 

    ```csharp
        static async Task MessageHandler(ProcessMessageEventArgs args)
        {
            string body = args.Message.Body.ToString();
            Console.WriteLine($"Received: {body} from subscription: {subscriptionName}");

            // complete the message. messages is deleted from the queue. 
            await args.CompleteMessageAsync(args.Message);
        }

        static Task ErrorHandler(ProcessErrorEventArgs args)
        {
            Console.WriteLine(args.Exception.ToString());
            return Task.CompletedTask;
        }
    ```
1. Dodaj następującą metodę `ReceiveMessagesFromSubscriptionAsync` do `Program` klasy.

    ```csharp
        static async Task ReceiveMessagesFromSubscriptionAsync()
        {
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a processor that we can use to process the messages
                ServiceBusProcessor processor = client.CreateProcessor(topicName, subscriptionName, new ServiceBusProcessorOptions());

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
1. Dodaj wywołanie `ReceiveMessagesFromSubscriptionAsync` metody do `Main` metody. Dodaj komentarz do `SendMessagesToTopicAsync` metody, jeśli chcesz przetestować tylko otrzymywanie komunikatów. Jeśli tego nie zrobisz, zobaczysz kolejne cztery komunikaty wysyłane do tematu. 

    ```csharp
        static async Task Main()
        {
            // send a message to the topic
            await SendMessageToTopicAsync();

            // send a batch of messages to the topic
            await SendMessageBatchToTopicAsync();

            // receive messages from the subscription
            await ReceiveMessagesFromSubscriptionAsync();
        }
    ```
## <a name="run-the-app"></a>Uruchamianie aplikacji
Uruchom aplikację. Poczekaj chwilę, a następnie naciśnij dowolny klawisz, aby przerwać otrzymywanie komunikatów. Powinny pojawić się następujące dane wyjściowe (spacja dla klucza). 

```console
Sent a single message to the topic: mytopic
Sent a batch of 3 messages to the topic: mytopic
Wait for a minute and then press any key to end the processing
Received: Hello, World! from subscription: mysub
Received: First message from subscription: mysub
Received: Second message from subscription: mysub
Received: Third message from subscription: mysub
Received: Hello, World! from subscription: mysub
Received: First message from subscription: mysub
Received: Second message from subscription: mysub
Received: Third message from subscription: mysub

Stopping the receiver...
Stopped receiving messages
```

Ponownie sprawdź Portal. 

- Na stronie **tematu Service Bus** na wykresie **komunikaty** zobaczysz osiem komunikatów przychodzących i ośmiu komunikatów wychodzących. Jeśli te liczby nie są widoczne, poczekaj kilka minut i Odśwież stronę, aby zobaczyć zaktualizowany wykres. 

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/messages-size-final.png" alt-text="Wysłane i odebrane komunikaty" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/messages-size-final.png":::
- Na stronie **subskrypcja Service Bus** zostanie wyświetlona **Liczba aktywnych komunikatów** jako zero. Wynika to z faktu, że odbiornik otrzymał komunikaty z tej subskrypcji i ukończył komunikaty. 

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page-final.png" alt-text="Liczba aktywnych komunikatów w subskrypcji na końcu" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page-final.png":::
    


## <a name="next-steps"></a>Następne kroki
Zapoznaj się z poniższą dokumentacją i przykładami:

- [Azure Service Busa Biblioteka kliencka dla platformy .NET — plik Readme](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus)
- [Przykłady w serwisie GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [Dokumentacja interfejsów API platformy .NET](/dotnet/api/azure.messaging.servicebus?preserve-view=true&view=azure-dotnet-preview)