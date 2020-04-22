---
title: Wysyłanie lub odbieranie zdarzeń z usługi Azure Event Hubs przy użyciu platformy .NET (najnowsze)
description: Ten artykuł zawiera przewodnik do utworzenia aplikacji .NET Core, która wysyła/odbiera zdarzenia do/z usługi Azure Event Hubs przy użyciu najnowszego pakietu Azure.Messaging.EventHubs.
services: event-hubs
documentationcenter: na
author: spelluru
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/20/2020
ms.author: spelluru
ms.openlocfilehash: bc8ec6cf4845eb100af5dcd80101f17102d2b7ac
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682139"
---
# <a name="send-events-to-and-receive-events-from-azure-event-hubs---net-core-azuremessagingeventhubs"></a>Wysyłanie zdarzeń do i odbieranie zdarzeń z usługi Azure Event Hubs — .NET Core (Azure.Messaging.EventHubs) 
Ten przewodnik Szybki start pokazuje sposób wysyłania zdarzeń do centrum zdarzeń i odbierania ich z usługi **Azure.Messaging.EventHubs** .NET Core. 

> [!IMPORTANT]
> Ten przewodnik Szybki start korzysta z nowej biblioteki **Azure.Messaging.EventHubs.** Aby uzyskać przewodnik Szybki start, który korzysta ze starej biblioteki **Microsoft.Azure.EventHubs,** zobacz [Wysyłanie i odbieranie zdarzeń przy użyciu biblioteki Microsoft.Azure.EventHubs](event-hubs-dotnet-standard-getstarted-send.md). 



## <a name="prerequisites"></a>Wymagania wstępne
Jeśli jesteś nowym użytkownikiem usługi Azure Event Hubs, zobacz [Omówienie centrów zdarzeń](event-hubs-about.md) przed rozpoczęciem tego przewodnika Szybki start. 

Do wykonania kroków tego przewodnika Szybki start niezbędne jest spełnienie następujących wymagań wstępnych:

- **Subskrypcja platformy Microsoft Azure**. Aby korzystać z usług platformy Azure, w tym usługi Azure Event Hubs, potrzebujesz subskrypcji.  Jeśli nie masz istniejącego konta platformy Azure, możesz zarejestrować się w celu [uzyskania bezpłatnej wersji próbnej](https://azure.microsoft.com/free/) lub skorzystać z korzyści dla subskrybenta MSDN podczas [tworzenia konta.](https://azure.microsoft.com)
- **Microsoft Visual Studio 2019**. Biblioteka klienta usługi Azure Event Hubs korzysta z nowych funkcji, które zostały wprowadzone w języku C# 8.0.  Nadal można używać biblioteki ze starszymi wersjami języka C#, ale niektóre z jego funkcji nie będą dostępne.  Aby włączyć te funkcje, należy [kierować .NET Core 3.0](/dotnet/standard/frameworks#how-to-specify-target-frameworks) lub [określić wersję językową,](/dotnet/csharp/language-reference/configure-language-version#override-a-default) której chcesz użyć (8.0 lub powyżej). Jeśli używasz programu Visual Studio, wersje przed Visual Studio 2019 nie są zgodne z narzędziami potrzebnymi do tworzenia projektów języka C# 8.0. Visual Studio 2019, w tym bezpłatna edycja społecznościowa, można pobrać [tutaj](https://visualstudio.microsoft.com/vs/)
- **Utwórz obszar nazw Centrów zdarzeń i centrum zdarzeń**. Pierwszym krokiem jest użycie [witryny Azure Portal](https://portal.azure.com) do utworzenia obszaru nazw typu Event Hubs i uzyskania poświadczeń zarządzania, których aplikacja potrzebuje do komunikowania się z centrum zdarzeń. Aby utworzyć przestrzeń nazw i centrum zdarzeń, wykonaj procedurę opisaną w [tym artykule](event-hubs-create.md). Następnie pobierz **ciąg połączenia dla obszaru nazw Centrum zdarzeń,** postępując zgodnie z instrukcjami z artykułu: [Pobierz parametry połączenia](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Ciąg połączenia należy użyć w dalszej części tego przewodnika Szybki start.

## <a name="send-events"></a>Wysyłanie zdarzeń 
W tej sekcji pokazano, jak utworzyć aplikację konsoli .NET Core do wysyłania zdarzeń do centrum zdarzeń. 

### <a name="create-a-console-application"></a>Tworzenie aplikacji konsolowej

1. Uruchom program Visual Studio 2019. 
1. Wybierz **pozycję Utwórz nowy projekt**. 
1. W oknie **dialogowym Tworzenie nowego projektu** wykonaj następujące czynności: Jeśli nie widzisz tego okna dialogowego, wybierz pozycję **Plik** w menu, wybierz pozycję **Nowy**, a następnie wybierz pozycję **Projekt**. 
    1. Wybierz **C#** dla języka programowania.
    1. Wybierz **konsolę** dla typu aplikacji. 
    1. Wybierz **aplikację konsoli (.NET Core)** z listy wyników. 
    1. Następnie wybierz przycisk **Dalej**. 

        ![Okno dialogowe Nowy projekt](./media/getstarted-dotnet-standard-send-v2/new-send-project.png)    
1. Wprowadź **EventHubsSender** dla nazwy projektu, **EventHubsQuickStart** dla nazwy rozwiązania, a następnie wybierz **OK,** aby utworzyć projekt. 

    ![Aplikacja > Konsola języka C#](./media/getstarted-dotnet-standard-send-v2/project-solution-names.png)

### <a name="add-the-event-hubs-nuget-package"></a>Dodawanie pakietu NuGet usługi Event Hubs

1. Z menu wybierz polecenie **Narzędzia** > **NuGet Package Manager** > **Package Manager Console.** 
1. Uruchom następujące polecenie, aby zainstalować pakiet **Azure.Messaging.EventHubs** NuGet:

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```


### <a name="write-code-to-send-messages-to-the-event-hub"></a>Pisanie kodu w celu wysyłania komunikatów do centrum zdarzeń

1. Dodaj następujące `using` instrukcje w górnej części pliku **Program.cs:**

    ```csharp
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Producer;
    ```

2. Dodaj stałe do `Program` klasy dla ciągu połączenia usługi Event Hubs i nazwy centrum zdarzeń. Zastąp symbole zastępcze w nawiasach odpowiednimi wartościami, które zostały dodane podczas tworzenia centrum zdarzeń. Upewnij się, że `{Event Hubs namespace connection string}` jest parametrem połączenia na poziomie przestrzeni nazw, a nie parametrem centrum zdarzeń. 

    ```csharp
    private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
    private const string eventHubName = "<EVENT HUB NAME>";
    ```

3. Zastąp `Main` metodę `async Main` następującą metodą. Zobacz komentarze do kodu, aby uzyskać szczegółowe informacje. 

    ```csharp
        static async Task Main()
        {
            // Create a producer client that you can use to send events to an event hub
            await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
            {
                // Create a batch of events 
                using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

                // Add events to the batch. An event is a represented by a collection of bytes and metadata. 
                eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("First event")));
                eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Second event")));
                eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Third event")));

                // Use the producer client to send the batch of events to the event hub
                await producerClient.SendAsync(eventBatch);
                Console.WriteLine("A batch of 3 events has been published.");
            }
        }
    ```
5. Skompiluj projekt i upewnij się, że nie ma żadnych błędów.
6. Uruchom program i poczekaj na komunikat potwierdzający. 
7. W witrynie Azure portal można sprawdzić, czy centrum zdarzeń odebrało wiadomości. Przełącz się do widoku **Wiadomości** w sekcji **Metryki.** Odśwież stronę, aby zaktualizować wykres. Może upłynąć kilka sekund, aby pokazać, że wiadomości zostały odebrane. 

    [![Sprawdź, czy centrum zdarzeń odebrało wiadomości](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > Pełny kod źródłowy z bardziej informacyjnymi komentarzami można znaleźć [w tym pliku w usłudze GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample03_PublishAnEventBatch.cs)

## <a name="receive-events"></a>Odbieranie zdarzeń
W tej sekcji pokazano, jak napisać aplikację konsoli .NET Core, która odbiera wiadomości z centrum zdarzeń przy użyciu procesora zdarzeń. Procesor zdarzeń upraszcza odbieranie zdarzeń z centrów zdarzeń, zarządzając trwałymi punktami kontrolnymi i równoległymi odbierami z tych centrów zdarzeń. Procesor zdarzeń jest skojarzony z centrum zdarzeń określonego zdarzenia i grupy konsumentów. Odbiera zdarzenia z wielu partycji w Centrum zdarzeń, przekazując je do pełnomocnika obsługi do przetwarzania przy użyciu kodu, który podałeś. 


> [!NOTE]
> Jeśli korzystasz z usługi Azure Stack Hub, ta platforma może obsługiwać inną wersję SDK obiektów blob magazynu niż te zwykle dostępne na platformie Azure. Na przykład jeśli korzystasz [z usługi Azure Stack Hub w wersji 2002](https://docs.microsoft.com/azure-stack/user/event-hubs-overview), najwyższą dostępną wersją usługi Storage jest wersja 2017-11-09. W takim przypadku oprócz następujących kroków w tej sekcji, należy również dodać kod do docelowej wersji interfejsu API usługi magazynu 2017-11-09. Na przykład, jak kierować określonej wersji interfejsu API magazynu, zobacz [ten przykład na GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs). Aby uzyskać więcej informacji na temat wersji usługi Azure Storage obsługiwanych w usłudze Azure Stack Hub, zobacz [usługi Azure Stack Hub storage: Różnice i zagadnienia.](https://docs.microsoft.com/azure-stack/user/azure-stack-acs-differences)

### <a name="create-an-azure-storage-and-a-blob-container"></a>Tworzenie usługi Azure Storage i kontenera obiektów blob
W tym przewodniku Szybki start używasz usługi Azure Storage jako magazynu punktów kontrolnych. Wykonaj następujące kroki, aby utworzyć konto usługi Azure Storage. 

1. [Tworzenie konta usługi Azure Storage](/azure/storage/common/storage-account-create?tabs=azure-portal)
2. [Tworzenie kontenera obiektów blob](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Pobierz ciąg połączenia z kontem magazynu](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

    Zanotuj w dół ciąg połączenia i nazwę kontenera. Użyjesz ich w kodzie odbierania. 


### <a name="create-a-project-for-the-receiver"></a>Tworzenie projektu dla odbiornika

1. W oknie Eksplorator rozwiązań kliknij prawym przyciskiem myszy rozwiązanie **EventHubQuickStart,** wskaż polecenie **Dodaj**i wybierz polecenie **Nowy projekt**. 
1. Wybierz **pozycję Aplikacja konsoli (.NET Core)** i wybierz pozycję **Dalej**. 
1. Wprowadź **EventHubsReceiver** dla **nazwy projektu**i wybierz pozycję **Utwórz**. 

### <a name="add-the-event-hubs-nuget-package"></a>Dodawanie pakietu NuGet usługi Event Hubs

1. Z menu wybierz polecenie **Narzędzia** > **NuGet Package Manager** > **Package Manager Console.** 
1. Uruchom następujące polecenie, aby zainstalować pakiet **Azure.Messaging.EventHubs** NuGet:

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```
1. Uruchom następujące polecenie, aby zainstalować pakiet **NuGet usługi Azure.Messaging.EventHubs.Processor:**

    ```cmd
    Install-Package Azure.Messaging.EventHubs.Processor
    ```    

### <a name="update-the-main-method"></a>Aktualizacja Metody głównej 

1. Dodaj następujące `using` instrukcje w górnej części pliku **Program.cs.**

    ```csharp
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Storage.Blobs;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Consumer;
    using Azure.Messaging.EventHubs.Processor;
    ```
1. Dodaj stałe do `Program` klasy dla ciągu połączenia usługi Event Hubs i nazwy centrum zdarzeń. Zastąp symbole zastępcze w nawiasach odpowiednimi wartościami, które zostały dodane podczas tworzenia centrum zdarzeń. Zastąp symbole zastępcze w nawiasach odpowiednimi wartościami, które zostały dodane podczas tworzenia centrum zdarzeń i konta magazynu (klucze dostępu — podstawowy ciąg połączenia). Upewnij się, że `{Event Hubs namespace connection string}` jest parametrem połączenia na poziomie przestrzeni nazw, a nie parametrem centrum zdarzeń.

    ```csharp
        private const string ehubNamespaceConnectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
        private const string eventHubName = "<EVENT HUB NAME>";
        private const string blobStorageConnectionString = "<AZURE STORAGE CONNECTION STRING>";
        private const string blobContainerName = "<BLOB CONTAINER NAME>";
    ```
3. Zastąp `Main` metodę `async Main` następującą metodą. Zobacz komentarze do kodu, aby uzyskać szczegółowe informacje. 

    ```csharp
        static async Task Main()
        {
            // Read from the default consumer group: $Default
            string consumerGroup = EventHubConsumerClient.DefaultConsumerGroupName;

            // Create a blob container client that the event processor will use 
            BlobContainerClient storageClient = new BlobContainerClient(blobStorageConnectionString, blobContainerName);

            // Create an event processor client to process events in the event hub
            EventProcessorClient processor = new EventProcessorClient(storageClient, consumerGroup, ehubNamespaceConnectionString, eventHubName);

            // Register handlers for processing events and handling errors
            processor.ProcessEventAsync += ProcessEventHandler;
            processor.ProcessErrorAsync += ProcessErrorHandler;

            // Start the processing
            await processor.StartProcessingAsync();

            // Wait for 10 seconds for the events to be processed
            await Task.Delay(TimeSpan.FromSeconds(10));

            // Stop the processing
            await processor.StopProcessingAsync();
        }    
    ```
1. Teraz dodaj następujące metody obsługi zdarzeń i błędów do klasy. 

    ```csharp
        static async Task ProcessEventHandler(ProcessEventArgs eventArgs)
        {
            // Write the body of the event to the console window
            Console.WriteLine("\tRecevied event: {0}", Encoding.UTF8.GetString(eventArgs.Data.Body.ToArray()));

            // Update checkpoint in the blob storage so that the app receives only new events the next time it's run
            await eventArgs.UpdateCheckpointAsync(eventArgs.CancellationToken);
        }

        static Task ProcessErrorHandler(ProcessErrorEventArgs eventArgs)
        {
            // Write details about the error to the console window
            Console.WriteLine($"\tPartition '{ eventArgs.PartitionId}': an unhandled exception was encountered. This was not expected to happen.");
            Console.WriteLine(eventArgs.Exception.Message);
            return Task.CompletedTask;
        }    
    ```
1. Skompiluj projekt i upewnij się, że nie ma żadnych błędów.

    > [!NOTE]
    > Pełny kod źródłowy z bardziej informacyjnymi komentarzami można znaleźć [w tym pliku w usłudze GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample01_HelloWorld.cs).
6. Uruchom aplikację odbiornika. 
1. Powinien zostać wyświetlony komunikat, że zdarzenie zostało odebrane. 

    ![Odebrano zdarzenie](./media/getstarted-dotnet-standard-send-v2/event-received.png)

    Te zdarzenia są trzy zdarzenia wysłane do centrum zdarzeń wcześniej przez uruchomienie programu nadawcy. 


## <a name="next-steps"></a>Następne kroki
Zapoznaj się z przykładami na GitHub. 

- [Przykłady centrów zdarzeń w usłudze GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)
- [Przykłady procesora zdarzeń w usłudze GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples)
- [Przykład kontroli dostępu opartej na rolach (RBAC)](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)
