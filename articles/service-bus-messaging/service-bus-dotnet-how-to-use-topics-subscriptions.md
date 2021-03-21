---
title: Wysyłanie komunikatów do tematów Azure Service Bus przy użyciu usługi Azure-Messaging-ServiceBus
description: W tym przewodniku szybki start pokazano, jak wysyłać komunikaty do Azure Service Bus tematów za pomocą pakietu Azure-Messaging-ServiceBus.
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 03/16/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 7b313caf6709429de9e0dcac219a4180c7391cf7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104607588"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-net"></a>Wysyłanie komunikatów do tematu Azure Service Bus i odbieranie komunikatów z subskrypcji do tematu (.NET)
W tym samouczku utworzysz aplikację w języku C#, aby wykonać następujące zadania:

1. Wysyłanie komunikatów do tematu Service Bus. 

    Temat Service Bus zawiera punkt końcowy dla aplikacji nadawcy do wysyłania wiadomości. Temat może mieć co najmniej jedną subskrypcję. Każda subskrypcja tematu otrzymuje kopię wiadomości wysłanej do tematu. Aby uzyskać więcej informacji na temat Service Bus tematów, zobacz [co to jest Azure Service Bus?](service-bus-messaging-overview.md). 
1. Odbieranie komunikatów z subskrypcji tematu. 

    :::image type="content" source="./media/service-bus-messaging-overview/about-service-bus-topic.png" alt-text="Tematy i subskrypcje usługi Service Bus":::

    > [!Important]
    > Ten przewodnik Szybki Start używa nowego pakietu **Azure. Messaging. ServiceBus** . Jeśli używasz starego pakietu Microsoft. Azure. ServiceBus, zobacz [wysyłanie i odbieranie komunikatów za pomocą pakietu Microsoft. Azure. ServiceBus](service-bus-dotnet-how-to-use-topics-subscriptions-legacy.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Możesz aktywować korzyści dla [subskrybentów programu Visual Studio lub MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Wykonaj kroki opisane w tym [przewodniku szybki start](service-bus-quickstart-topics-subscriptions-portal.md) , aby utworzyć temat Service Bus i subskrypcje w temacie. 

    > [!NOTE]
    > Parametry połączenia będą używane do przestrzeni nazw, nazwy tematu i nazwy jednej z subskrypcji w temacie w tym samouczku.  
- [Program Visual Studio 2019](https://www.visualstudio.com/vs). 
 
## <a name="send-messages-to-a-topic"></a>Wysyłanie komunikatów do tematu
W tej sekcji utworzysz aplikację konsolową .NET Core w programie Visual Studio, dodając kod do wysyłania komunikatów do utworzonego tematu Service Bus. 

### <a name="create-a-console-application"></a>Tworzenie aplikacji konsolowej
Tworzenie aplikacji konsolowej platformy .NET Core przy użyciu programu Visual Studio. 

1. Uruchom program Visual Studio.  
1. Jeśli zostanie wyświetlona strona **wprowadzenie** , wybierz pozycję **Utwórz nowy projekt**. 
1. Na stronie **Tworzenie nowego projektu** wykonaj następujące kroki: 
    1. W polu język programowania wybierz pozycję **C#**. 
    1. W polu Typ projektu wybierz pozycję **konsola**. 
    1. Wybierz pozycję **aplikacja konsoli (.NET Core)** z listy szablonów. 
    1. Następnie wybierz pozycję **Dalej**. 
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-console-project.png" alt-text="Tworzenie projektu aplikacji konsolowej":::
1. Na stronie **Konfiguruj nowy projekt** wykonaj następujące kroki: 
    1. W polu **Nazwa projektu** wprowadź nazwę dla projektu. 
    1. W polu **Lokalizacja** wybierz lokalizację plików projektu i rozwiązania. 
    1. W polu **Nazwa rozwiązania** wprowadź nazwę rozwiązania. Rozwiązanie programu Visual Studio może mieć jeden lub więcej projektów. W tym przewodniku szybki start rozwiązanie będzie miało tylko jeden projekt. 
    1. Wybierz polecenie **Create** (Utwórz), aby utworzyć projekt. 
            
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-console-project-2.png" alt-text="Podaj nazwę i lokalizację projektu i rozwiązania":::    


### <a name="add-the-service-bus-nuget-package"></a>Dodawanie pakietu NuGet usługi Service Bus
1. Kliknij prawym przyciskiem myszy nowo utworzony projekt i wybierz pozycję **Zarządzaj pakietami NuGet**.

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/manage-nuget-packages-menu.png" alt-text="Menu Zarządzanie pakietami NuGet":::        
1. Przejdź do karty **przeglądanie** .
1. Wyszukaj i wybierz pozycję **[Azure. Messaging. ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/)**.
1. Wybierz pozycję **Zainstaluj**, aby ukończyć instalację.

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/select-service-bus-package.png" alt-text="Wybierz Service Bus pakiet NuGet.":::
5. Jeśli zobaczysz okno dialogowe **Podgląd zmian** , wybierz **przycisk OK** , aby kontynuować. 
1. Jeśli zobaczysz stronę **akceptacji licencji** , wybierz pozycję **Akceptuję** , aby kontynuować. 
    

### <a name="add-code-to-send-messages-to-the-topic"></a>Dodawanie kodu w celu wysyłania komunikatów do tematu 

1. W oknie **Eksplorator rozwiązań** kliknij dwukrotnie pozycję **program. cs** , aby otworzyć plik w edytorze. 
1. Dodaj następujące `using` instrukcje w górnej części definicji przestrzeni nazw przed deklaracją klasy:
   
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using Azure.Messaging.ServiceBus;
    ```
1. W `Program` klasie powyżej `Main` funkcji deklaruj następujące zmienne:

    ```csharp
        static string connectionString = "<NAMESPACE CONNECTION STRING>";
        static string topicName = "<SERVICE BUS TOPIC NAME>";
        static string subscriptionName = "<SERVICE BUS - TOPIC SUBSCRIPTION NAME>";
    ```

    Zastąp następujące wartości:
    - `<NAMESPACE CONNECTION STRING>` z parametrami połączenia do przestrzeni nazw Service Bus
    - `<TOPIC NAME>` o nazwie tematu
    - `<SUBSCRIPTION NAME>` z nazwą subskrypcji

### <a name="send-a-single-message-to-the-topic"></a>Wyślij pojedynczą wiadomość do tematu
Dodaj metodę o nazwie `SendMessageToTopicAsync` do `Program` klasy powyżej lub poniżej `Main` metody. Ta metoda wysyła pojedynczy komunikat do tematu.

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

Ta metoda wykonuje następujące czynności: 
1. Tworzy obiekt [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) przy użyciu parametrów połączenia do przestrzeni nazw. 
1. Używa `ServiceBusClient` obiektu do utworzenia obiektu [ServiceBusSender](/dotnet/api/azure.messaging.servicebus.servicebussender) dla określonego tematu Service Bus. W tym kroku jest stosowana Metoda [ServiceBusClient. Issender](/dotnet/api/azure.messaging.servicebus.servicebusclient.createsender) .
1. Następnie metoda wysyła pojedynczy komunikat testowy do tematu Service Bus przy użyciu metody [ServiceBusSender. SendMessageAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.sendmessageasync) . 

### <a name="send-a-batch-of-messages-to-the-topic"></a>Wysyłanie partii komunikatów do tematu
1. Dodaj metodę o nazwie `CreateMessages` , aby utworzyć kolejkę (kolejkę .NET, a nie Service Bus kolejki) komunikatów `Program` do klasy. Zwykle te komunikaty są uzyskiwane z różnych części aplikacji. Tutaj tworzymy kolejkę przykładowych wiadomości. Jeśli nie masz doświadczenia z kolejkami .NET, zobacz [Queue.](/dotnet/api/system.collections.queue.enqueue)kolejce.

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
1. Dodaj metodę o nazwie `SendMessageBatchAsync` `Program` Class i Dodaj następujący kod. Ta metoda pobiera kolejkę komunikatów i przygotowuje co najmniej jedną partię do wysłania do tematu Service Bus. 

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

    Oto ważne kroki z kodu:
    1. Tworzy obiekt [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) przy użyciu parametrów połączenia do przestrzeni nazw. 
    1. Wywołuje metodę elementu [wysyłającego](/dotnet/api/azure.messaging.servicebus.servicebusclient.createsender) dla obiektu, `ServiceBusClient` Aby utworzyć obiekt [ServiceBusSender](/dotnet/api/azure.messaging.servicebus.servicebussender) dla określonego tematu Service Bus. 
    1. Wywołuje metodę pomocnika, `GetMessages` Aby uzyskać kolejkę komunikatów do wysłania do tematu Service Bus. 
    1. Tworzy [ServiceBusMessageBatch](/dotnet/api/azure.messaging.servicebus.servicebusmessagebatch) za pomocą [ServiceBusSender. CreateMessageBatchAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.createmessagebatchasync).
    1. Dodaj komunikaty do usługi Batch za pomocą [ServiceBusMessageBatch. TryAddMessage](/dotnet/api/azure.messaging.servicebus.servicebusmessagebatch.tryaddmessage). Po dodaniu komunikatów do partii są one usuwane z kolejki programu .NET. 
    1. Wysyła partię komunikatów do tematu Service Bus przy użyciu metody [ServiceBusSender. SendMessagesAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.sendmessagesasync) .

### <a name="update-the-main-method"></a>Aktualizowanie metody Main
Zastąp `Main()` metodę następującą metodą **asynchroniczną** `Main` . Wywołuje obydwie metody wysyłania, aby wysyłać pojedyncze wiadomości i partie komunikatów do tematu.  

```csharp
    static async Task Main()
    {
        // send a single message to the topic
        await SendMessageToTopicAsync();

        // send a batch of messages to the topic
        await SendMessageBatchToTopicAsync();
    }
```

### <a name="test-the-app-to-send-messages-to-the-topic"></a>Testowanie aplikacji w celu wysyłania komunikatów do tematu
1. Uruchom aplikację. Powinny zostać wyświetlone następujące dane wyjściowe:

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

    Oto ważne kroki z kodu:
    1. Tworzy obiekt [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) przy użyciu parametrów połączenia do przestrzeni nazw. 
    1. Wywołuje metodę [CreateProcess](/dotnet/api/azure.messaging.servicebus.servicebusclient.createprocessor) dla `ServiceBusClient` obiektu, aby utworzyć obiekt [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor) dla określonego tematu Service Bus i kombinacji subskrypcji. 
    1. Określa programy obsługi dla zdarzeń [ProcessMessageAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processmessageasync) i [ProcessErrorAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processerrorasync) `ServiceBusProcessor` obiektu. 
    1. Uruchamia przetwarzanie komunikatów przez wywołanie [StartProcessingAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.startprocessingasync) w `ServiceBusProcessor` obiekcie. 
    1. Gdy użytkownik naciśnie klawisz, aby zakończyć przetwarzanie, wywołuje [StopProcessingAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.stopprocessingasync) na `ServiceBusProcessor` obiekcie. 
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
- [Przykłady dla platformy .NET dla Azure Service Bus w witrynie GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [Dokumentacja interfejsów API platformy .NET](/dotnet/api/azure.messaging.servicebus?preserve-view=true)