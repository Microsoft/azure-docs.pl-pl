---
title: Wprowadzenie do kolejek usługi Azure Service Bus | Microsoft Docs
description: W tym samouczku utworzysz aplikacje konsolowe .NET Core do wysyłania komunikatów do kolejki Service Bus odbierania komunikatów.
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 09/01/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: d92a5e0b0d1a6953d0043f85ff98f6740aaacebb
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864018"
---
# <a name="get-started-with-service-bus-queues"></a>Wprowadzenie do kolejek usługi Service Bus
W tym samouczku utworzysz aplikacje konsolowe .NET Core do wysyłania komunikatów do kolejki Service Bus odbierania komunikatów.

> [!WARNING]
> W tym przewodniku Szybki start jest używany stary pakiet Microsoft.Azure.ServiceBus. Aby uzyskać przewodnik Szybki start korzystający z najnowszego pakietu Azure.Messaging.ServiceBus, zobacz Wysyłanie i odbieranie zdarzeń przy użyciu pakietu [Azure.Messaging.ServiceBus.](service-bus-dotnet-get-started-with-queues.md) 

## <a name="prerequisites"></a>Wymagania wstępne

- [Visual Studio 2019](https://www.visualstudio.com/vs)r.
- [Zestaw NET Core SDK](https://dotnet.microsoft.com/download), wersja 2.0 lub nowsza.
- Subskrypcja platformy Azure. Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Możesz aktywować korzyści [dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) lub utworzyć bezpłatne [konto.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Jeśli nie masz kolejki do pracy, wykonaj kroki opisane w artykule Use [Azure Portal to create a Service Bus queue to](service-bus-quickstart-portal.md) create a queue (Tworzenie kolejki przy użyciu usługi Azure Portal do tworzenia kolejki).

  - Zapoznaj się z szybkim omówieniem Service Bus kolejek.
  - Utwórz nową Service Bus nazw.
  - Pobierz ciąg połączenia.
  - Utwórz kolejkę Service Bus kolejki.

## <a name="send-messages"></a>Wysyłanie komunikatów

Aby wysyłać komunikaty do kolejki, napisz aplikację konsolową w języku C# za pomocą programu Visual Studio.

### <a name="create-a-console-application"></a>Tworzenie aplikacji konsolowej

Uruchom Visual Studio i utwórz nowy projekt **Aplikacja konsoli (.NET Core)** dla języka C#. W tym przykładzie nazwa aplikacji *to CoreSenderApp.*

### <a name="add-the-service-bus-nuget-package"></a>Dodawanie pakietu NuGet usługi Service Bus

1. Kliknij prawym przyciskiem myszy nowo utworzony projekt i wybierz pozycję **Zarządzaj pakietami NuGet**.
1. Wybierz pozycję **Przeglądaj**. Wyszukaj i wybierz **[pozycję Microsoft.Azure.ServiceBus.](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)**
1. Wybierz **pozycję Zainstaluj,** aby ukończyć instalację, a następnie zamknij Menedżer pakietów.

    ![Wybieranie pakietu NuGet][nuget-pkg]

### <a name="write-code-to-send-messages-to-the-queue"></a>Pisanie kodu w celu wysyłania komunikatów do kolejki

1. W *programie Program.cs* dodaj następujące instrukcje na początku definicji przestrzeni nazw `using` przed deklaracją klasy:

    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

1. W `Program` klasie zadeklaruj następujące zmienne:

    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string QueueName = "<your_queue_name>";
    static IQueueClient queueClient;
    ```

    Wprowadź ciąg połączenia dla przestrzeni nazw jako `ServiceBusConnectionString` zmienną. Wprowadź nazwę kolejki.

1. Zastąp `Main()` metodę następującą metodą  `Main` asynchroniczną. Wywołuje on metodę, która zostanie dodać w następnym kroku w celu `SendMessagesAsync()` wysyłania komunikatów do kolejki. 

    ```csharp
    public static async Task Main(string[] args)
    {    
        const int numberOfMessages = 10;
        queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after sending all the messages.");
        Console.WriteLine("======================================================");

        // Send messages.
        await SendMessagesAsync(numberOfMessages);

        Console.ReadKey();

        await queueClient.CloseAsync();
    }
    ```
1. Bezpośrednio po metodzie dodaj następującą metodę, która umożliwia wysyłanie liczby komunikatów określonej przez metodę `MainAsync()` `SendMessagesAsync()` `numberOfMessagesToSend` (obecnie ustawioną na 10):

    ```csharp
    static async Task SendMessagesAsync(int numberOfMessagesToSend)
    {
        try
        {
            for (var i = 0; i < numberOfMessagesToSend; i++)
            {
                // Create a new message to send to the queue.
                string messageBody = $"Message {i}";
                var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                // Write the body of the message to the console.
                Console.WriteLine($"Sending message: {messageBody}");

                // Send the message to the queue.
                await queueClient.SendAsync(message);
            }
        }
        catch (Exception exception)
        {
            Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
        }
    }
    ```

Oto jak powinien wyglądać *plik Program.cs.*

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
        // Connection String for the namespace can be obtained from the Azure portal under the 
        // 'Shared Access policies' section.
        const string ServiceBusConnectionString = "<your_connection_string>";
        const string QueueName = "<your_queue_name>";
        static IQueueClient queueClient;

        public static async Task Main(string[] args)
        {    
            const int numberOfMessages = 10;
            queueClient = new QueueClient(ServiceBusConnectionString, QueueName);
    
            Console.WriteLine("======================================================");
            Console.WriteLine("Press ENTER key to exit after sending all the messages.");
            Console.WriteLine("======================================================");
    
            // Send messages.
            await SendMessagesAsync(numberOfMessages);
    
            Console.ReadKey();
    
            await queueClient.CloseAsync();
        }

        static async Task SendMessagesAsync(int numberOfMessagesToSend)
        {
            try
            {
                for (var i = 0; i < numberOfMessagesToSend; i++)
                {
                    // Create a new message to send to the queue
                    string messageBody = $"Message {i}";
                    var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                    // Write the body of the message to the console
                    Console.WriteLine($"Sending message: {messageBody}");

                    // Send the message to the queue
                    await queueClient.SendAsync(message);
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

Uruchom program i sprawdź Azure Portal.

Wybierz nazwę kolejki w oknie Przegląd przestrzeni **nazw,** aby wyświetlić kolejkę **Podstawowe elementy**.

![Odebrane komunikaty z licznikiem i rozmiarem][queue-message]

Wartość **liczba aktywnych komunikatów** dla kolejki wynosi teraz **10**. Za każdym razem, gdy uruchamiasz tę aplikację nadawcy bez pobierania komunikatów, ta wartość zwiększa się o 10.

Bieżący rozmiar kolejki zwiększa wartość **CURRENT** w **essentials**  za każdym razem, gdy aplikacja dodaje komunikaty do kolejki.

W następnej sekcji opisano sposób pobierania tych komunikatów.

## <a name="receive-messages"></a>Odbieranie komunikatów

Aby odbierać wysłane komunikaty, utwórz inną aplikację **konsoli (.NET Core).** Zainstaluj pakiet **NuGet Microsoft.Azure.ServiceBus,** tak jak w przypadku aplikacji nadawcy.

### <a name="write-code-to-receive-messages-from-the-queue"></a>Pisanie kodu w celu odbierania komunikatów z kolejki

1. W *programie Program.cs* dodaj następujące instrukcje na początku definicji przestrzeni nazw `using` przed deklaracją klasy:

    ```csharp
    using System;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

1. W `Program` klasie zadeklaruj następujące zmienne:

    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string QueueName = "<your_queue_name>";
    static IQueueClient queueClient;
    ```

    Wprowadź ciąg połączenia dla przestrzeni nazw jako `ServiceBusConnectionString` zmienną. Wprowadź nazwę kolejki.

1. Zastąp metodę `Main()` poniższym kodem:

    ```csharp
    static void Main(string[] args)
    {
        MainAsync().GetAwaiter().GetResult();
    }

    static async Task MainAsync()
    {
        queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
        Console.WriteLine("======================================================");

        // Register QueueClient's MessageHandler and receive messages in a loop
        RegisterOnMessageHandlerAndReceiveMessages();

        Console.ReadKey();

        await queueClient.CloseAsync();
    }
    ```

1. Bezpośrednio po metodzie dodaj następującą metodę, która rejestruje program obsługi komunikatów i odbiera komunikaty `MainAsync()` wysyłane przez aplikację nadawcy:

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
        queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
    }
    ```

1. Bezpośrednio po poprzedniej metodzie dodaj następującą metodę `ProcessMessagesAsync()` w celu przetworzenia odebranych komunikatów:

    ```csharp
    static async Task ProcessMessagesAsync(Message message, CancellationToken token)
    {
        // Process the message.
        Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

        // Complete the message so that it is not received again.
        // This can be done only if the queue Client is created in ReceiveMode.PeekLock mode (which is the default).
        await queueClient.CompleteAsync(message.SystemProperties.LockToken);

        // Note: Use the cancellationToken passed as necessary to determine if the queueClient has already been closed.
        // If queueClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
        // to avoid unnecessary exceptions.
    }
    ```

1. Na koniec dodaj następującą metodę obsługującą ewentualne wyjątki, które mogą wystąpić:

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

Oto jak powinien wyglądać *plik Program.cs:*

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
        // Connection String for the namespace can be obtained from the Azure portal under the 
        // 'Shared Access policies' section.
        const string ServiceBusConnectionString = "<your_connection_string>";
        const string QueueName = "<your_queue_name>";
        static IQueueClient queueClient;

        static void Main(string[] args)
        {
            MainAsync().GetAwaiter().GetResult();
        }

        static async Task MainAsync()
        {
            queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

            Console.WriteLine("======================================================");
            Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
            Console.WriteLine("======================================================");

            // Register QueueClient's MessageHandler and receive messages in a loop
            RegisterOnMessageHandlerAndReceiveMessages();
 
            Console.ReadKey();

            await queueClient.CloseAsync();
        }

        static void RegisterOnMessageHandlerAndReceiveMessages()
        {
            // Configure the MessageHandler Options in terms of exception handling, number of concurrent messages to deliver etc.
            var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
            {
                // Maximum number of Concurrent calls to the callback `ProcessMessagesAsync`, set to 1 for simplicity.
                // Set it according to how many messages the application wants to process in parallel.
                MaxConcurrentCalls = 1,

                // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
                // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
                AutoComplete = false
            };

            // Register the function that will process messages
            queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
        }

        static async Task ProcessMessagesAsync(Message message, CancellationToken token)
        {
            // Process the message
            Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

            // Complete the message so that it is not received again.
            // This can be done only if the queueClient is created in ReceiveMode.PeekLock mode (which is default).
            await queueClient.CompleteAsync(message.SystemProperties.LockToken);

            // Note: Use the cancellationToken passed as necessary to determine if the queueClient has already been closed.
            // If queueClient has already been Closed, you may chose to not call CompleteAsync() or AbandonAsync() etc. calls 
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

Uruchom program i ponownie sprawdź portal. Wartości **Liczba aktywnych komunikatów** **i BIEŻĄCE** mają teraz **wartość 0**.

![Kolejkowanie po otrzymaniu komunikatów][queue-message-receive]

Gratulacje! Utworzono kolejkę, wysłano do tej kolejki zestaw komunikatów i odebrano te komunikaty z tej samej kolejki.

> [!NOTE]
> Za pomocą Service Bus można zarządzać [zasobami Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Usługa Service Bus Explorer umożliwia użytkownikom łatwe łączenie się z przestrzenią nazw Service Bus i administrowanie jednostkami obsługi komunikatów. Narzędzie udostępnia zaawansowane funkcje, takie jak funkcja importowania/eksportowania lub możliwość testowania tematów, kolejek, subskrypcji, usług przekazywania, centrów powiadomień i centrów zdarzeń.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z naszym [repozytorium GitHub zawierającym przykłady](https://github.com/Azure/azure-service-bus/tree/master/samples), które pokazują niektóre bardziej zaawansowane funkcje obsługi komunikatów usługi Service Bus.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues-legacy/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues-legacy/messages-sent-to-essentials.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues-legacy/queue-message-receive-in-essentials.png

