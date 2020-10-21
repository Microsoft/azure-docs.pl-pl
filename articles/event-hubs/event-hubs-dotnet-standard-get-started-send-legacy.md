---
title: Wysyłanie i odbieranie zdarzeń z usługi Azure Event Hubs przy użyciu platformy .NET (stary)
description: Ten artykuł zawiera Przewodnik dotyczący tworzenia aplikacji .NET Core, która wysyła/odbiera zdarzenia do/z usługi Azure Event Hubs przy użyciu starego pakietu Microsoft. Azure. EventHubs.
ms.topic: conceptual
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 8819a95364bf41c6f9837c3db31a9800968d096c
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92332165"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-net-core-microsoftazureeventhubs"></a>Wysyłanie zdarzeń do i odbieranie zdarzeń z usługi Azure Event Hubs przy użyciu platformy .NET Core (Microsoft. Azure. EventHubs)
Ten przewodnik Szybki Start przedstawia sposób wysyłania zdarzeń do i odbierania zdarzeń z centrum zdarzeń za pomocą biblioteki **Microsoft. Azure. EventHubs** .NET Core.

> [!WARNING]
> Ten przewodnik Szybki Start używa starego pakietu **Microsoft. Azure. EventHubs** . Przewodnik Szybki Start, który używa najnowszej biblioteki  **Azure. Messaging. EventHubs** , znajduje się w temacie [wysyłanie i odbieranie zdarzeń przy użyciu biblioteki Azure. Messaging. EventHubs](event-hubs-dotnet-standard-getstarted-send.md). Aby przenieść aplikację z używania starej biblioteki do nowej, zapoznaj się z [przewodnikiem migrowania z Microsoft. Azure. EventHubs do platformy Azure. Messaging. EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md).

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli dopiero zaczynasz w usłudze Azure Event Hubs, zapoznaj się z tematem [Event Hubs Overview](event-hubs-about.md) przed wykonaniem tego przewodnika Szybki Start. 

Do wykonania kroków tego przewodnika Szybki start niezbędne jest spełnienie następujących wymagań wstępnych:

- **Subskrypcja Microsoft Azure**. Do korzystania z usług platformy Azure, w tym usługi Azure Event Hubs, potrzebna jest subskrypcja.  Jeśli nie masz istniejącego konta platformy Azure, możesz zarejestrować się w celu korzystania z [bezpłatnej wersji próbnej](https://azure.microsoft.com/free/) lub skorzystać z korzyści dla subskrybentów MSDN podczas [tworzenia konta](https://azure.microsoft.com).
- [Microsoft Visual Studio 2019](https://www.visualstudio.com).
- [Narzędzia platformy .NET Core dla programu Visual Studio 2015 lub 2017](https://www.microsoft.com/net/core). 
- **Utwórz przestrzeń nazw Event Hubs i centrum zdarzeń**. Pierwszym krokiem jest użycie [Azure Portal](https://portal.azure.com) do utworzenia przestrzeni nazw typu Event Hubs i uzyskanie poświadczeń zarządzania wymaganych przez aplikację do komunikacji z centrum zdarzeń. Aby utworzyć przestrzeń nazw i centrum zdarzeń, wykonaj procedurę opisaną w [tym artykule](event-hubs-create.md). Następnie Pobierz **Parametry połączenia dla przestrzeni nazw centrum zdarzeń** , wykonując instrukcje podane w artykule: [pobieranie parametrów połączenia](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Parametry połączenia są używane w dalszej części tego przewodnika Szybki Start.

## <a name="send-events"></a>Wysyłanie zdarzeń 
W tej sekcji przedstawiono sposób tworzenia aplikacji konsolowej .NET Core w celu wysyłania zdarzeń do centrum zdarzeń. 

> [!NOTE]
> Ten przewodnik Szybki start możesz pobrać jako przykład z witryny [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender), zastąpić ciągi `EventHubConnectionString` i `EventHubName` wartościami swojego centrum zdarzeń, a następnie uruchomić go. Alternatywnie możesz wykonać kroki opisane w tym przewodniku Szybki Start, aby utworzyć własne.


### <a name="create-a-console-application"></a>Tworzenie aplikacji konsolowej

Uruchom program Visual Studio. W menu **plik** kliknij pozycję **Nowy**, a następnie kliknij pozycję **projekt**. Utwórz aplikację konsolową platformy .NET Core.

![Nowy projekt](./media/event-hubs-dotnet-standard-getstarted-send/netcoresnd.png)

### <a name="add-the-event-hubs-nuget-package"></a>Dodawanie pakietu NuGet usługi Event Hubs

Dodaj [`Microsoft.Azure.EventHubs`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) pakiet NuGet biblioteki .NET Core do projektu, wykonując następujące czynności: 

1. Kliknij prawym przyciskiem myszy nowo utworzony projekt i wybierz pozycję **Zarządzaj pakietami NuGet**.
2. Kliknij kartę **Przeglądaj**, a następnie wyszukaj ciąg „Microsoft.Azure.EventHubs” i wybierz pakiet **Microsoft.Azure.EventHubs**. Kliknij przycisk **Zainstaluj**, aby ukończyć instalację, a następnie zamknij to okno dialogowe.

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Pisanie kodu w celu wysyłania komunikatów do centrum zdarzeń

1. Dodaj następujące instrukcje `using` na początku pliku Program.cs:

    ```csharp
    using Microsoft.Azure.EventHubs;
    using System.Text;
    using System.Threading.Tasks;
    ```

2. Dodaj stałe do klasy `Program` na potrzeby parametrów połączenia usługi Event Hubs oraz ścieżki jednostki (indywidualna nazwa centrum zdarzeń). Zastąp symbole zastępcze w nawiasach odpowiednimi wartościami uzyskanymi podczas tworzenia centrum zdarzeń. Upewnij się, że `{Event Hubs connection string}` jest parametrem połączenia na poziomie przestrzeni nazw, a nie parametrem centrum zdarzeń. 

    ```csharp
    private static EventHubClient eventHubClient;
    private const string EventHubConnectionString = "{Event Hubs connection string}";
    private const string EventHubName = "{Event Hub path/name}";
    ```

3. Dodaj nową metodę o nazwie `MainAsync` do klasy `Program` w następujący sposób:

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
        // Typically, the connection string should have the entity path in it, but this simple scenario
        // uses the connection string from the namespace.
        var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
        {
            EntityPath = EventHubName
        };

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

        await SendMessagesToEventHub(100);

        await eventHubClient.CloseAsync();

        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
    }
    ```

4. Dodaj nową metodę o nazwie `SendMessagesToEventHub` do klasy `Program` w następujący sposób:

    ```csharp
    // Uses the event hub client to send 100 messages to the event hub.
    private static async Task SendMessagesToEventHub(int numMessagesToSend)
    {
        for (var i = 0; i < numMessagesToSend; i++)
        {
            try
            {
                var message = $"Message {i}";
                Console.WriteLine($"Sending message: {message}");
                await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
            }
            catch (Exception exception)
            {
                Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
            }

            await Task.Delay(10);
        }

        Console.WriteLine($"{numMessagesToSend} messages sent.");
    }
    ```

5. Dodaj następujący kod do metody `Main` w klasie `Program`:

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

   Oto jak powinien wyglądać plik Program.cs.

    ```csharp
    namespace SampleSender
    {
        using System;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.Azure.EventHubs;

        public class Program
        {
            private static EventHubClient eventHubClient;
            private const string EventHubConnectionString = "{Event Hubs connection string}";
            private const string EventHubName = "{Event Hub path/name}";

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
                // Typically, the connection string should have the entity path in it, but for the sake of this simple scenario
                // we are using the connection string from the namespace.
                var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
                {
                    EntityPath = EventHubName
                };

                eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

                await SendMessagesToEventHub(100);

                await eventHubClient.CloseAsync();

                Console.WriteLine("Press ENTER to exit.");
                Console.ReadLine();
            }

            // Uses the event hub client to send 100 messages to the event hub.
            private static async Task SendMessagesToEventHub(int numMessagesToSend)
            {
                for (var i = 0; i < numMessagesToSend; i++)
                {
                    try
                    {
                        var message = $"Message {i}";
                        Console.WriteLine($"Sending message: {message}");
                        await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
                    }
                    catch (Exception exception)
                    {
                        Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
                    }

                    await Task.Delay(10);
                }

                Console.WriteLine($"{numMessagesToSend} messages sent.");
            }
        }
    }
    ```

6. Uruchom program i upewnij się, że nie ma w nim żadnych błędów.

## <a name="receive-events"></a>Odbieranie zdarzeń
W tej sekcji pokazano, jak napisać aplikację konsolową .NET Core, która odbiera komunikaty z centrum zdarzeń za pomocą [hosta procesora zdarzeń](event-hubs-event-processor-host.md). [Host procesora zdarzeń](event-hubs-event-processor-host.md) jest klasą .NET, która upraszcza odbieranie zdarzeń z centrów zdarzeń przez zarządzanie trwałymi punktami kontrolnymi i równoległymi odbiorami z tej usługi. Za pomocą hosta procesora zdarzeń można podzielić zdarzenia między wieloma odbiornikami, nawet w przypadku hostowania w różnych węzłach. W tym przykładzie przedstawiono, jak używać hosta procesora zdarzeń dla jednego odbiornika.
> [!NOTE]
> Ten przewodnik Szybki Start można pobrać jako przykład z usługi [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver), zamienić `EventHubConnectionString` i `EventHubName` ,, `StorageAccountName` `StorageAccountKey` , i `StorageContainerName` ciągi z wartościami centrum zdarzeń, a następnie uruchomić go. Alternatywnie możesz utworzyć własne rozwiązanie, wykonując kroki opisane w tym samouczku.

[!INCLUDE [event-hubs-create-storage](../../includes/event-hubs-create-storage.md)]

### <a name="create-a-console-application"></a>Tworzenie aplikacji konsolowej

Uruchom program Visual Studio. W menu **plik** kliknij pozycję **Nowy**, a następnie kliknij pozycję **projekt**. Utwórz aplikację konsolową platformy .NET Core.

![Nowy projekt do odbioru](./media/event-hubs-dotnet-standard-getstarted-receive-eph/netcorercv.png)

### <a name="add-the-event-hubs-nuget-package"></a>Dodawanie pakietu NuGet usługi Event Hubs

Dodaj do swojego projektu pakiety NuGet biblioteki .NET Standard [**Microsoft.Azure.EventHubs**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) i [**Microsoft.Azure.EventHubs.Processor**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/), wykonując następujące czynności: 

1. Kliknij prawym przyciskiem myszy nowo utworzony projekt i wybierz pozycję **Zarządzaj pakietami NuGet**.
2. Kliknij kartę **Przeglądaj**, wyszukaj ciąg **Microsoft.Azure.EventHubs**, a następnie wybierz pakiet **Microsoft.Azure.EventHubs**. Kliknij przycisk **Zainstaluj**, aby ukończyć instalację, a następnie zamknij to okno dialogowe.
3. Powtórz kroki 1 i 2 w celu zainstalowania pakietu **Microsoft.Azure.EventHubs.Processor**.

### <a name="implement-the-ieventprocessor-interface"></a>Implementowanie interfejsu IEventProcessor

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt, kliknij polecenie **Dodaj**, a następnie kliknij pozycję **Klasa**. Nowej klasie nadaj nazwę **SimpleEventProcessor**.

2. Otwórz plik SimpleEventProcessor.cs i dodaj na jego początku następujące instrukcje `using`.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

3. Zaimplementuj interfejs `IEventProcessor`. Zastąp całą zawartość klasy `SimpleEventProcessor` następującym kodem:

    ```csharp
    public class SimpleEventProcessor : IEventProcessor
    {
        public Task CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
            return Task.CompletedTask;
        }

        public Task OpenAsync(PartitionContext context)
        {
            Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
            return Task.CompletedTask;
        }

        public Task ProcessErrorAsync(PartitionContext context, Exception error)
        {
            Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
            return Task.CompletedTask;
        }

        public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (var eventData in messages)
            {
                var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
                Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
            }

            return context.CheckpointAsync();
        }
    }
    ```

### <a name="update-the-main-method-to-use-simpleeventprocessor"></a>Aktualizowanie metody Main w celu użycia klasy SimpleEventProcessor

1. Dodaj następujące instrukcje `using` w górnej części pliku Program.cs.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

2. Dodaj stałe do klasy `Program` dla parametrów połączenia centrum zdarzeń, nazwy centrum zdarzeń, nazwy kontenera konta magazynu, nazwy konta magazynu i klucza konta magazynu. Dodaj następujący kod, zastępując tekst zastępczy odpowiednimi wartościami:

    ```csharp
    private const string EventHubConnectionString = "{Event Hubs connection string}";
    private const string EventHubName = "{Event Hub path/name}";
    private const string StorageContainerName = "{Storage account container name}";
    private const string StorageAccountName = "{Storage account name}";
    private const string StorageAccountKey = "{Storage account key}";

    private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);
    ```   

3. Dodaj nową metodę o nazwie `MainAsync` do klasy `Program` w następujący sposób:

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        Console.WriteLine("Registering EventProcessor...");

        var eventProcessorHost = new EventProcessorHost(
            EventHubName,
            PartitionReceiver.DefaultConsumerGroupName,
            EventHubConnectionString,
            StorageConnectionString,
            StorageContainerName);

        // Registers the Event Processor Host and starts receiving messages
        await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

        Console.WriteLine("Receiving. Press ENTER to stop worker.");
        Console.ReadLine();

        // Disposes of the Event Processor Host
        await eventProcessorHost.UnregisterEventProcessorAsync();
    }
    ```

3. Dodaj następujący wiersz kodu do metody `Main`:

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

    Oto jak powinien wyglądać plik Program.cs:

    ```csharp
    namespace SampleEphReceiver
    {

        public class Program
        {
            private const string EventHubConnectionString = "{Event Hubs connection string}";
            private const string EventHubName = "{Event Hub path/name}";
            private const string StorageContainerName = "{Storage account container name}";
            private const string StorageAccountName = "{Storage account name}";
            private const string StorageAccountKey = "{Storage account key}";

            private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                Console.WriteLine("Registering EventProcessor...");

                var eventProcessorHost = new EventProcessorHost(
                    EventHubName,
                    PartitionReceiver.DefaultConsumerGroupName,
                    EventHubConnectionString,
                    StorageConnectionString,
                    StorageContainerName);

                // Registers the Event Processor Host and starts receiving messages
                await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

                Console.WriteLine("Receiving. Press ENTER to stop worker.");
                Console.ReadLine();

                // Disposes of the Event Processor Host
                await eventProcessorHost.UnregisterEventProcessorAsync();
            }
        }
    }
    ```

4. Uruchom program i upewnij się, że nie ma w nim żadnych błędów.


## <a name="next-steps"></a>Następne kroki
Przeczytaj następujące artykuły:

- [Przykłady kontroli dostępu opartej na rolach (RBAC) na platformie Azure](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    Te przykłady używają starej biblioteki **Microsoft. Azure. EventHubs** , ale można ją łatwo zaktualizować do korzystania z najnowszej biblioteki **Azure. Messaging. EventHubs** . Aby przenieść przykład z używania starej biblioteki do nowej, zapoznaj się z [przewodnikiem migrowania z Microsoft. Azure. EventHubs do platformy Azure. Messaging. EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md).
- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funkcje i terminologia w usłudze Azure Event Hubs](event-hubs-features.md)
- [Event Hubs — często zadawane pytania](event-hubs-faq.md)


