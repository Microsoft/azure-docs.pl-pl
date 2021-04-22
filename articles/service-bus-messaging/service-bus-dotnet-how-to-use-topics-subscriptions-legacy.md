---
title: Rozpoczynanie pracy z tematami i subskrypcjami usługi Azure Service Bus | Microsoft Docs
description: Napisz aplikację konsolową .NET Core w języku C#, która korzysta z tematów i subskrypcji komunikatów usługi Service Bus.
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 09/02/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: a64ccec49248f48a85782cfe9537513a97b2bdfc
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868163"
---
# <a name="get-started-with-service-bus-topics"></a>Wprowadzenie do tematów usługi Service Bus
Ten samouczek obejmuje następujące kroki:

1. Napisanie aplikacji konsolowej .NET Core w celu wysłania zestawu komunikatów do tematu.
2. Napisanie aplikacji konsolowej .NET Core w celu odebrania tych komunikatów z subskrypcji.

> [!WARNING]
> W tym przewodniku Szybki start jest używany stary pakiet Microsoft.Azure.ServiceBus. Aby uzyskać przewodnik Szybki start korzystający z najnowszego pakietu Azure.Messaging.ServiceBus, zobacz Wysyłanie i odbieranie komunikatów przy użyciu pakietu [Azure.Messaging.ServiceBus.](service-bus-dotnet-how-to-use-topics-subscriptions.md) Aby przenieść aplikację z używania starej biblioteki do nowej, zobacz Przewodnik migracji z witryny [Microsoft.Azure.ServiceBus do pakietu Azure.Messaging.ServiceBus.](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/servicebus/Azure.Messaging.ServiceBus/MigrationGuide.md) 

## <a name="prerequisites"></a>Wymagania wstępne

1. Subskrypcja platformy Azure. Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Możesz aktywować korzyści [dla subskrybentów Visual Studio MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) lub utworzyć bezpłatne [konto.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
2. Wykonaj kroki opisane w przewodniku [Szybki start:](service-bus-quickstart-topics-subscriptions-portal.md) tworzenie Azure Portal tematu Service Bus tematu i subskrypcji tematu w celu wykonania następujących zadań:
    1. Utwórz Service Bus **nazw**.
    2. Pobierz **ciąg połączenia**.
    3. Utwórz temat **w** przestrzeni nazw .
    4. Utwórz **jedną subskrypcję** tematu w przestrzeni nazw .
3. [Program Visual Studio 2017 Update 3 (wersja 15.3, 26730.01)](https://www.visualstudio.com/vs) lub nowszy.
4. [Zestaw NET Core SDK](https://dotnet.microsoft.com/download), wersja 2.0 lub nowsza.
 
## <a name="send-messages-to-the-topic"></a>Wysyłanie komunikatów do tematu

Aby wysyłać komunikaty do tematu, napisz aplikację konsolową w języku C# za pomocą programu Visual Studio.

### <a name="create-a-console-application"></a>Tworzenie aplikacji konsolowej

Uruchom program Visual Studio i utwórz nowy projekt **Aplikacja konsoli (.NET Core)**.

### <a name="add-the-service-bus-nuget-package"></a>Dodawanie pakietu NuGet usługi Service Bus

1. Kliknij prawym przyciskiem myszy nowo utworzony projekt i wybierz pozycję **Zarządzaj pakietami NuGet**.
2. Kliknij kartę **Przeglądaj**, wyszukaj ciąg **[Microsoft.Azure.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)**, a następnie wybierz element **Microsoft.Azure.ServiceBus**. Kliknij przycisk **Zainstaluj**, aby ukończyć instalację, a następnie zamknij to okno dialogowe.
   
    ![Wybieranie pakietu NuGet][nuget-pkg]

### <a name="write-code-to-send-messages-to-the-topic"></a>Pisanie kodu w celu wysyłania komunikatów do tematu

1. W pliku Program.cs dodaj następujące instrukcje `using` na początku definicji przestrzeni nazw przed deklaracją klasy:
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. W ramach klasy `Program` zadeklaruj następujące zmienne. Ustaw jako zmienną `ServiceBusConnectionString` parametry połączenia, które zostały uzyskane podczas tworzenia przestrzeni nazw, a następnie ustaw jako zmienną `TopicName` nazwę użytą podczas tworzenia tematu:
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string TopicName = "<your_topic_name>";
    static ITopicClient topicClient;
    ``` 

3. Zastąp metodę następującą metodą asynchroniczną, która wysyła komunikaty asynchronicznie przy użyciu metody `Main()`  `Main` SendMessagesAsync, która zostanie dodać w następnym kroku. 

    ```csharp
    public static async Task Main(string[] args)
    {
        const int numberOfMessages = 10;
        topicClient = new TopicClient(ServiceBusConnectionString, TopicName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after sending all the messages.");
        Console.WriteLine("======================================================");

        // Send messages.
        await SendMessagesAsync(numberOfMessages);

        Console.ReadKey();

        await topicClient.CloseAsync();
    }
    ```
5. Bezpośrednio po metodzie `Main` dodaj następującą metodę `SendMessagesAsync()`, która wykonuje zadanie wysyłania liczby komunikatów określonej przy użyciu elementu `numberOfMessagesToSend` (wartość aktualnie ustawiona na 10):

    ```csharp
    static async Task SendMessagesAsync(int numberOfMessagesToSend)
    {
        try
        {
            for (var i = 0; i < numberOfMessagesToSend; i++)
            {
                // Create a new message to send to the topic.
                string messageBody = $"Message {i}";
                var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                // Write the body of the message to the console.
                Console.WriteLine($"Sending message: {messageBody}");

                // Send the message to the topic.
                await topicClient.SendAsync(message);
            }
        }
        catch (Exception exception)
        {
            Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
        }
    }
    ```
  
6. Oto jak powinien wyglądać plik Program.cs nadawcy.
   
    ```csharp
    namespace CoreSenderApp
    {
        using System;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.ServiceBus;

        class Program
        {
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string TopicName = "<your_topic_name>";
            static ITopicClient topicClient;

            public static async Task Main(string[] args)
            {
                const int numberOfMessages = 10;
                topicClient = new TopicClient(ServiceBusConnectionString, TopicName);
    
                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after sending all the messages.");
                Console.WriteLine("======================================================");
    
                // Send messages.
                await SendMessagesAsync(numberOfMessages);
    
                Console.ReadKey();
    
                await topicClient.CloseAsync();
            }

            static async Task SendMessagesAsync(int numberOfMessagesToSend)
            {
                try
                {
                    for (var i = 0; i < numberOfMessagesToSend; i++)
                    {
                        // Create a new message to send to the topic
                        string messageBody = $"Message {i}";
                        var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                        // Write the body of the message to the console
                        Console.WriteLine($"Sending message: {messageBody}");

                        // Send the message to the topic
                        await topicClient.SendAsync(message);
                    }
                }
                catch (Exception exception)
                {
                    Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
                }
            }
        }
    }
    ```

3. Uruchom program i sprawdź witrynę Azure Portal: kliknij nazwę swojego tematu w oknie **Przegląd** przestrzeni nazw. Zostanie wyświetlony ekran **Podstawowe elementy** tematu. Zwróć uwagę, że w subskrypcji wyświetlonej w dolnej części okna wartość pola **Liczba komunikatów** dla subskrypcji wynosi teraz **10**. Przy każdym uruchomieniu aplikacji nadawcy bez pobierania komunikatów (zgodnie z opisem w następnej sekcji) ta wartość zwiększa się o 10. Należy również zauważyć, że bieżący rozmiar tematu zwiększa wartość **Bieżące** w oknie **Podstawowe elementy** za każdym razem, kiedy aplikacja dodaje komunikaty do tematu.
   
      ![Rozmiar komunikatu][topic-message]

## <a name="receive-messages-from-the-subscription"></a>Odbieranie komunikatów z subskrypcji

Aby odbierać wysłane komunikaty, utwórz inną aplikację konsolową .NET Core i zainstaluj pakiet NuGet **Microsoft.Azure.ServiceBus,** podobnie jak w przypadku poprzedniej aplikacji nadawcy.

### <a name="write-code-to-receive-messages-from-the-subscription"></a>Pisanie kodu w celu odbierania komunikatów z subskrypcji

1. W pliku Program.cs dodaj następujące instrukcje `using` na początku definicji przestrzeni nazw przed deklaracją klasy:
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. W ramach klasy `Program` zadeklaruj następujące zmienne. Ustaw jako zmienną `ServiceBusConnectionString` parametry połączenia, które zostały uzyskane podczas tworzenia przestrzeni nazw, ustaw jako zmienną `TopicName` nazwę użytą podczas tworzenia tematu, a następnie ustaw jako zmienną `SubscriptionName` nazwę użytą podczas tworzenia subskrypcji do tematu:
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string TopicName = "<your_topic_name>";
    const string SubscriptionName = "<your_subscription_name>";
    static ISubscriptionClient subscriptionClient;
    ```

3. Zastąp `Main()` metodę następującą metodą  `Main` asynchroniczną. Wywołuje on `RegisterOnMessageHandlerAndReceiveMessages()` metodę, która zostanie dodasz w następnym kroku. 

    ```csharp
    public static async Task Main(string[] args)
    {    
        subscriptionClient = new SubscriptionClient(ServiceBusConnectionString, TopicName, SubscriptionName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
        Console.WriteLine("======================================================");

        // Register subscription message handler and receive messages in a loop
        RegisterOnMessageHandlerAndReceiveMessages();

        Console.ReadKey();

        await subscriptionClient.CloseAsync();    
    }
   ```
5. Bezpośrednio po metodzie `Main()` dodaj następującą metodę, która rejestruje obsługę komunikatów i odbiera komunikaty wysyłane przez aplikację nadawcy:

    ```csharp
    static void RegisterOnMessageHandlerAndReceiveMessages()
    {
        // Configure the message handler options in terms of exception handling, number of concurrent messages to deliver, etc.
        var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
        {
            // Maximum number of concurrent calls to the callback ProcessMessagesAsync(), set to 1 for simplicity.
            // Set it according to how many messages the application wants to process in parallel.
            MaxConcurrentCalls = 1,

            // Indicates whether the message pump should automatically complete the messages after returning from user callback.
            // False below indicates the complete operation is handled by the user callback as in ProcessMessagesAsync().
            AutoComplete = false
        };

        // Register the function that processes messages.
        subscriptionClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
    }
    ```    

6. Bezpośrednio po poprzedniej metodzie dodaj następującą metodę `ProcessMessagesAsync()` w celu przetworzenia odebranych komunikatów:
 
    ```csharp
    static async Task ProcessMessagesAsync(Message message, CancellationToken token)
    {
        // Process the message.
        Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

        // Complete the message so that it is not received again.
        // This can be done only if the subscriptionClient is created in ReceiveMode.PeekLock mode (which is the default).
        await subscriptionClient.CompleteAsync(message.SystemProperties.LockToken);

        // Note: Use the cancellationToken passed as necessary to determine if the subscriptionClient has already been closed.
        // If subscriptionClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
        // to avoid unnecessary exceptions.
    }
    ```

7. Na koniec dodaj następującą metodę obsługującą ewentualne wyjątki, które mogą wystąpić:
 
    ```csharp
    // Use this handler to examine the exceptions received on the message pump.
    static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
    {
        Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
        var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
        Console.WriteLine("Exception context for troubleshooting:");
        Console.WriteLine($"- Endpoint: {context.Endpoint}");
        Console.WriteLine($"- Entity Path: {context.EntityPath}");
        Console.WriteLine($"- Executing Action: {context.Action}");
        return Task.CompletedTask;
    }    
    ```    

8. Oto jak powinien wyglądać plik Program.cs odbiorcy:
   
    ```csharp
    namespace CoreReceiverApp
    {
        using System;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.ServiceBus;

        class Program
        {
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string TopicName = "<your_topic_name>";
            const string SubscriptionName = "<your_subscription_name>";
            static ISubscriptionClient subscriptionClient;

            public static async Task Main(string[] args)
            {    
                subscriptionClient = new SubscriptionClient(ServiceBusConnectionString, TopicName, SubscriptionName);
        
                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
                Console.WriteLine("======================================================");
        
                // Register subscription message handler and receive messages in a loop
                RegisterOnMessageHandlerAndReceiveMessages();
        
                Console.ReadKey();
        
                await subscriptionClient.CloseAsync();    
            }

            static void RegisterOnMessageHandlerAndReceiveMessages()
            {
                // Configure the message handler options in terms of exception handling, number of concurrent messages to deliver, etc.
                var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
                {
                    // Maximum number of concurrent calls to the callback ProcessMessagesAsync(), set to 1 for simplicity.
                    // Set it according to how many messages the application wants to process in parallel.
                    MaxConcurrentCalls = 1,

                    // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
                    // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
                    AutoComplete = false
                };

                // Register the function that processes messages.
                subscriptionClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
            }

            static async Task ProcessMessagesAsync(Message message, CancellationToken token)
            {
                // Process the message.
                Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

                // Complete the message so that it is not received again.
                // This can be done only if the subscriptionClient is created in ReceiveMode.PeekLock mode (which is the default).
                await subscriptionClient.CompleteAsync(message.SystemProperties.LockToken);

                // Note: Use the cancellationToken passed as necessary to determine if the subscriptionClient has already been closed.
                // If subscriptionClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
                // to avoid unnecessary exceptions.
            }

            static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
            {
                Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
                var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
                Console.WriteLine("Exception context for troubleshooting:");
                Console.WriteLine($"- Endpoint: {context.Endpoint}");
                Console.WriteLine($"- Entity Path: {context.EntityPath}");
                Console.WriteLine($"- Executing Action: {context.Action}");
                return Task.CompletedTask;
            }
        }
    }
    ```
9. Uruchom program i ponownie sprawdź portal. Zauważ, że wartości **Liczba komunikatów** i **Bieżące** wynoszą teraz **0**.
   
    ![Długość tematu][topic-message-receive]

Gratulacje! Za pomocą biblioteki .NET Standard utworzono teraz temat i subskrypcję, wysłano 10 komunikatów i odebrano te komunikaty.

> [!NOTE]
> Za pomocą Service Bus można zarządzać [zasobami Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Usługa Service Bus Explorer umożliwia użytkownikom łączenie się z przestrzenią nazw Service Bus i administrowanie jednostkami obsługi komunikatów w prosty sposób. Narzędzie udostępnia zaawansowane funkcje, takie jak funkcja importowania/eksportowania lub możliwość testowania tematu, kolejek, subskrypcji, usług przekazywania, centrów powiadomień i centrów zdarzeń. 

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z naszym [repozytorium GitHub zawierającym przykłady](https://github.com/Azure/azure-service-bus/tree/master/samples) dotyczące usługi Service Bus, które pokazują niektóre bardziej zaawansowane funkcje obsługi komunikatów usługi Service Bus.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/nuget-package.png
[topic-message]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message.png
[topic-message-receive]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message-receive.png
[createtopic1]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic1.png
[createtopic2]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic2.png
[createtopic3]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic3.png
[createtopic4]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic4.png
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples
[azure-portal]: https://portal.azure.com
