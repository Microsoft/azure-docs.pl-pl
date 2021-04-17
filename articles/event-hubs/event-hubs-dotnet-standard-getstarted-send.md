---
title: Wysyłanie lub odbieranie zdarzeń z usługi Azure Event Hubs przy użyciu programu .NET (najnowsza wersja)
description: Ten artykuł zawiera wskazówki dotyczące tworzenia aplikacji .NET Core, która wysyła i odbiera zdarzenia do/z usługi Azure Event Hubs przy użyciu najnowszego pakietu Azure.Messaging.EventHubs.
ms.topic: quickstart
ms.date: 09/25/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 58da331336481614cf0f85bdf6c1136c8bdc8db7
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536503"
---
# <a name="send-events-to-and-receive-events-from-azure-event-hubs---net-azuremessagingeventhubs"></a>Wysyłanie zdarzeń do usługi i odbieranie zdarzeń z usługi Azure Event Hubs — .NET (Azure.Messaging.EventHubs) 
W tym przewodniku Szybki start pokazano, jak wysyłać zdarzenia do centrum zdarzeń i odbierać je z tego centrum zdarzeń przy użyciu biblioteki .NET **Azure.Messaging.EventHubs.** 

> [!IMPORTANT]
> W tym przewodniku Szybki start jest używana **nowa biblioteka Azure.Messaging.EventHubs.** Aby uzyskać przewodnik Szybki start korzystający ze starej biblioteki **Microsoft.Azure.EventHubs,** zobacz Wysyłanie i odbieranie zdarzeń przy [użyciu biblioteki Microsoft.Azure.EventHubs.](event-hubs-dotnet-standard-get-started-send-legacy.md) 



## <a name="prerequisites"></a>Wymagania wstępne
Jeśli jesteś nowym użytkownikom tej Azure Event Hubs, zobacz [Event Hubs omówienie](event-hubs-about.md) przed rozpoczęciem tego przewodnika Szybki start. 

Do wykonania kroków tego przewodnika Szybki start niezbędne jest spełnienie następujących wymagań wstępnych:

- **Microsoft Azure subskrypcji usługi**. Aby korzystać z usług platformy Azure, w tym Azure Event Hubs, potrzebujesz subskrypcji.  Jeśli nie masz istniejącego konta platformy Azure, możesz [](https://azure.microsoft.com/free/) zarejestrować się w celu korzystania z bezpłatnej wersji próbnej lub skorzystać z korzyści dla subskrybentów MSDN podczas [tworzenia konta](https://azure.microsoft.com).
- **Microsoft Visual Studio 2019** r. Biblioteka Azure Event Hubs klienta korzysta z nowych funkcji, które zostały wprowadzone w języku C# 8.0.  Nadal można używać biblioteki z poprzednimi wersjami języka C#, ale nowa składnia nie będzie dostępna. Aby korzystać z pełnej składni, zaleca się skompilowanie z wersją [zestaw .NET Core SDK](https://dotnet.microsoft.com/download) 3.0 lub nowszą i wersją językową ustawioną na [](/dotnet/csharp/language-reference/configure-language-version#override-a-default) `latest` . Jeśli używasz programu Visual Studio, wersje starsze niż Visual Studio 2019 nie są zgodne z narzędziami wymaganymi do kompilowania projektów w języku C# 8.0. Visual Studio 2019 r., w tym bezpłatną wersję Community, można pobrać [tutaj.](https://visualstudio.microsoft.com/vs/)
- **Utwórz przestrzeń Event Hubs i centrum zdarzeń.** Pierwszym krokiem jest użycie usługi [Azure Portal](https://portal.azure.com) do utworzenia przestrzeni nazw typu Event Hubs i uzyskania poświadczeń zarządzania, których aplikacja potrzebuje do komunikowania się z centrum zdarzeń. Aby utworzyć przestrzeń nazw i centrum zdarzeń, wykonaj procedurę opisaną w [tym artykule](event-hubs-create.md). Następnie pobierz ciąg **połączenia dla przestrzeni nazw Event Hubs,** korzystając z instrukcji z artykułu: Uzyskiwanie parametrów [połączenia](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). W dalszej części tego przewodnika Szybki start użyjemy parametrów połączenia.

## <a name="send-events"></a>Wysyłanie zdarzeń 
W tej sekcji przedstawiono sposób tworzenia aplikacji konsolowej .NET Core w celu wysyłania zdarzeń do centrum zdarzeń. 

### <a name="create-a-console-application"></a>Tworzenie aplikacji konsolowej

1. Uruchom program Visual Studio 2019. 
1. Wybierz **pozycję Utwórz nowy projekt.** 
1. W **oknie dialogowym** Tworzenie nowego projektu wykonaj następujące kroki: Jeśli to okno  dialogowe nie jest wyświetlane, wybierz pozycję Plik z menu, wybierz pozycję **Nowy,** a następnie wybierz pozycję **Projekt**. 
    1. Wybierz **C#** jako język programowania.
    1. Wybierz **pozycję** Konsola jako typ aplikacji. 
    1. Wybierz **pozycję Aplikacja konsoli (.NET Core)** z listy wyników. 
    1. Następnie wybierz pozycję **Dalej**. 

        ![Okno dialogowe Nowy projekt](./media/getstarted-dotnet-standard-send-v2/new-send-project.png)    
1. Wprowadź **EventHubsSender** jako nazwę projektu, w polach **EventHubsQuickStart** wprowadź nazwę rozwiązania, a następnie wybierz przycisk **OK,** aby utworzyć projekt. 

    ![Aplikacja konsoli > C#](./media/getstarted-dotnet-standard-send-v2/project-solution-names.png)

### <a name="add-the-event-hubs-nuget-package"></a>Dodawanie pakietu NuGet usługi Event Hubs

1. Wybierz **pozycję**  >  **Narzędzia NuGet Menedżer pakietów** Menedżer pakietów  >  **z** menu. 
1. Uruchom następujące polecenie, aby zainstalować pakiet NuGet **Azure.Messaging.EventHubs:**

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```


### <a name="write-code-to-send-messages-to-the-event-hub"></a>Pisanie kodu w celu wysyłania komunikatów do centrum zdarzeń

1. Dodaj następujące `using` instrukcje na początku pliku **Program.cs:**

    ```csharp
    using System;
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Producer;
    ```

2. Dodaj stałe do `Program` klasy dla Event Hubs parametrów połączenia i nazwy centrum zdarzeń. Zastąp symbole zastępcze w nawiasach prawidłowymi wartościami, które otrzymujesz podczas tworzenia centrum zdarzeń. Upewnij się, że `{Event Hubs namespace connection string}` jest parametrem połączenia na poziomie przestrzeni nazw, a nie parametrem centrum zdarzeń. 

    ```csharp
    private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
    private const string eventHubName = "<EVENT HUB NAME>";
    ```

3. Zastąp `Main` metodę następującą `async Main` metodą. Zobacz komentarze kodu, aby uzyskać szczegółowe informacje. 

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
5. Skompilować projekt i upewnić się, że nie ma żadnych błędów.
6. Uruchom program i poczekaj na komunikat potwierdzający. 
7. W Azure Portal sprawdzić, czy centrum zdarzeń odebrało komunikaty. Przejdź do **widoku** Komunikaty w **sekcji** Metryki. Odśwież stronę, aby zaktualizować wykres. Może to potrwać kilka sekund, aby pokazać, że komunikaty zostały odebrane. 

    [![Sprawdź, czy centrum zdarzeń odebrało komunikaty](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > Pełny kod źródłowy z bardziej informacyjnymi komentarzami można znaleźć w [tym pliku w witrynie GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample04_PublishingEvents.md)

## <a name="receive-events"></a>Odbieranie zdarzeń
W tej sekcji przedstawiono sposób pisania aplikacji konsolowej .NET Core, która odbiera komunikaty z centrum zdarzeń przy użyciu procesora zdarzeń. Procesor zdarzeń upraszcza odbieranie zdarzeń z centrów zdarzeń, zarządzając trwałymi punktami kontrolnymi i równoległymi odbioru z tych centrów zdarzeń. Procesor zdarzeń jest skojarzony z określonym centrum zdarzeń i grupą odbiorców. Odbiera zdarzenia z wielu partycji w centrum zdarzeń, przekazując je do delegata programu obsługi w celu przetworzenia przy użyciu kodu, który podaniu. 


> [!WARNING]
> Jeśli ten kod zostanie uruchomiony na komputerze Azure Stack Hub, wystąpią błędy w czasie wykonywania, chyba że zostanie ukierunkowana na określoną wersję interfejsu API usługi Storage. Wynika to z tego, że zestaw SDK Event Hubs korzysta z najnowszego dostępnego interfejsu API usługi Azure Storage dostępnego na platformie Azure, który może nie być dostępny na Azure Stack Hub platformie. Azure Stack Hub może obsługiwać inną wersję zestawu SDK usługi Storage Blob niż ta, która jest zwykle dostępna na platformie Azure. Jeśli używasz usługi Azure Blob Storage jako magazynu punktów kontrolnych, sprawdź obsługiwaną wersję interfejsu API usługi [Azure Storage](/azure-stack/user/azure-stack-acs-differences?#api-version) dla swojego Azure Stack Hub kompilacji i docelowej tej wersji w kodzie. 
>
> Jeśli na przykład używasz programu Azure Stack Hub wersji 2005, najwyższą dostępną wersją usługi Storage jest wersja 2019-02-02. Domyślnie biblioteka klienta Event Hubs SDK używa najwyższej dostępnej wersji na platformie Azure (2019-07-07 w momencie wydania zestawu SDK). W takim przypadku oprócz kroków w tej sekcji należy również dodać kod docelowy interfejsu API usługi Storage w wersji 2019-02-02. Aby uzyskać przykład docelowy dla określonej wersji interfejsu API usługi Storage, zobacz [ten przykład w witrynie GitHub.](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/) 
 

### <a name="create-an-azure-storage-and-a-blob-container"></a>Tworzenie usługi Azure Storage i kontenera obiektów blob
W tym przewodniku Szybki start użyjemy usługi Azure Storage jako magazynu punktów kontrolnych. Wykonaj następujące kroki, aby utworzyć konto usługi Azure Storage. 

1. [Tworzenie konta usługi Azure Storage](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Tworzenie kontenera obiektów blob](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Uzyskiwanie parametrów połączenia z kontem magazynu](../storage/common/storage-configure-connection-string.md)

    Zanotuj ciąg połączenia i nazwę kontenera. Użyjesz ich w kodzie odbioru. 


### <a name="create-a-project-for-the-receiver"></a>Tworzenie projektu dla odbiornika

1. W oknie Eksplorator rozwiązań kliknij prawym przyciskiem myszy rozwiązanie **EventHubQuickStart,** wskaż polecenie **Dodaj** i wybierz **pozycję Nowy projekt.** 
1. Wybierz **pozycję Aplikacja konsoli (.NET Core)** i wybierz pozycję **Dalej.** 
1. Wprowadź **Nazwę projektu EventHubsReceiver** **i** wybierz pozycję **Utwórz.** 

### <a name="add-the-event-hubs-nuget-package"></a>Dodawanie pakietu NuGet usługi Event Hubs

1. Wybierz **pozycję**  >  **Narzędzia NuGet Menedżer pakietów** Menedżer pakietów  >  **z** menu. 
1. Uruchom następujące polecenie, aby zainstalować pakiet NuGet **Azure.Messaging.EventHubs:**

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```
1. Uruchom następujące polecenie, aby zainstalować pakiet NuGet **Azure.Messaging.EventHubs.Processor:**

    ```cmd
    Install-Package Azure.Messaging.EventHubs.Processor
    ```    

### <a name="update-the-main-method"></a>Aktualizowanie metody Main 

1. Dodaj następujące `using` instrukcje w górnej części pliku **Program.cs.**

    ```csharp
    using System;
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Storage.Blobs;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Consumer;
    using Azure.Messaging.EventHubs.Processor;
    ```
1. Dodaj stałe do `Program` klasy dla Event Hubs parametrów połączenia i nazwy centrum zdarzeń. Zastąp symbole zastępcze w nawiasach prawidłowymi wartościami, które otrzymujesz podczas tworzenia centrum zdarzeń. Zastąp symbole zastępcze w nawiasach prawidłowymi wartościami, które uzyskujesz podczas tworzenia centrum zdarzeń i konta magazynu (klucze dostępu — podstawowe ciągi połączenia). Upewnij się, że `{Event Hubs namespace connection string}` jest parametrem połączenia na poziomie przestrzeni nazw, a nie parametrem centrum zdarzeń.

    ```csharp
        private const string ehubNamespaceConnectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
        private const string eventHubName = "<EVENT HUB NAME>";
        private const string blobStorageConnectionString = "<AZURE STORAGE CONNECTION STRING>";
        private const string blobContainerName = "<BLOB CONTAINER NAME>";
    ```
3. Zastąp `Main` metodę następującą `async Main` metodą. Zobacz komentarze kodu, aby uzyskać szczegółowe informacje. 

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

            // Wait for 30 seconds for the events to be processed
            await Task.Delay(TimeSpan.FromSeconds(30));

            // Stop the processing
            await processor.StopProcessingAsync();
        }    
    ```
1. Teraz dodaj następujące metody obsługi zdarzeń i błędów do klasy . 

    ```csharp
        static async Task ProcessEventHandler(ProcessEventArgs eventArgs)
        {
            // Write the body of the event to the console window
            Console.WriteLine("\tReceived event: {0}", Encoding.UTF8.GetString(eventArgs.Data.Body.ToArray()));

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
1. Skompilowanie projektu i upewninie się, że nie ma żadnych błędów.

    > [!NOTE]
    > Pełny kod źródłowy z bardziej informacyjnymi komentarzami można znaleźć w [tym pliku w witrynie GitHub.](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample01_HelloWorld.md)
6. Uruchom aplikację odbiorcy. 
1. Powinien zostać wyświetlony komunikat informujący o tym, że zdarzenie zostało odebrane. 

    ![Odebrano zdarzenie](./media/getstarted-dotnet-standard-send-v2/event-received.png)

    Są to trzy zdarzenia wysłane wcześniej do centrum zdarzeń przez uruchomienie programu nadawcy. 


## <a name="next-steps"></a>Następne kroki
Zapoznaj się z przykładami w witrynie GitHub. 

- [Event Hubs przykłady w witrynie GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)
- [Przykłady procesorów zdarzeń w usłudze GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples)
- [Przykład kontroli dostępu opartej na rolach na platformie Azure (Azure RBAC)](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)
