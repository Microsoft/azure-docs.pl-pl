---
title: Publikowanie komunikatów i przetwarzanie komunikatów z tematów usługi Atlas Kafka usługi Azure Purview za pośrednictwem Event Hubs przy użyciu platformy .NET
description: Ten artykuł zawiera przewodnik tworzenia aplikacji .NET Core, która wysyła zdarzenia do i z tematów platformy Apache Atlas Kafka firmy Purview i odbiera je przy użyciu najnowszego pakietu Azure.Messaging.EventHubs.
ms.topic: quickstart
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.devlang: dotnet
ms.date: 04/15/2021
ms.openlocfilehash: 60c177c913c78dbcfcbfe1d465044b69b0e6dd2e
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590437"
---
# <a name="publish-messages-to-and-process-messages-from-azure-purviews-atlas-kafka-topics-via-event-hubs-using-net"></a>Publikowanie komunikatów i przetwarzanie komunikatów z tematów usługi Atlas Kafka usługi Azure Purview za pośrednictwem Event Hubs przy użyciu platformy .NET 
W tym przewodniku Szybki start pokazano, jak wysyłać zdarzenia do tematów atlasu kafka usługi Azure Purview i odbierać je z tych tematów za pośrednictwem centrum zdarzeń przy użyciu biblioteki .NET **Azure.Messaging.EventHubs.** 

> [!IMPORTANT]
> Zarządzane centrum zdarzeń jest tworzone w ramach tworzenia konta programu Purview. Zobacz [Purview account creation (Tworzenie konta programu Purview).](create-catalog-portal.md) Komunikaty można publikować w temacie platformy Kafka centrum zdarzeń, ATLAS_HOOK aplikacja Purview będzie z niego korzystać i go przetwarzać. Aplikacja Purview powiadomi o zmianach jednostek w temacie platformy Kafka centrum zdarzeń ATLAS_ENTITIES a użytkownik może go używać i przetwarzać. W tym przewodniku Szybki start jest używana **nowa biblioteka Azure.Messaging.EventHubs.** 


## <a name="prerequisites"></a>Wymagania wstępne
Jeśli nie masz jeszcze nic do Azure Event Hubs, zobacz [omówienie](../event-hubs/event-hubs-about.md) Event Hubs przed rozpoczęciem tego przewodnika Szybki start. 

Do wykonania kroków tego przewodnika Szybki start niezbędne jest spełnienie następujących wymagań wstępnych:

- **Microsoft Azure subskrypcji**. Aby korzystać z usług platformy Azure, w tym Azure Event Hubs, potrzebna jest subskrypcja.  Jeśli nie masz istniejącego konta platformy Azure, możesz [](https://azure.microsoft.com/free/) zarejestrować się w celu korzystania z bezpłatnej wersji próbnej lub skorzystać z korzyści dla subskrybentów MSDN podczas [tworzenia konta](https://azure.microsoft.com).
- **Microsoft Visual Studio 2019 r.** Biblioteka Azure Event Hubs klienta korzysta z nowych funkcji wprowadzonych w języku C# 8.0.  Nadal możesz używać biblioteki z poprzednimi wersjami języka C#, ale nowa składnia nie będzie dostępna. Aby skorzystać z pełnej składni, zaleca się skompilowanie z wersją [zestaw .NET Core SDK](https://dotnet.microsoft.com/download) 3.0 lub nowszą i wersją [językową](/dotnet/csharp/language-reference/configure-language-version#override-a-default) ustawioną na `latest` . Jeśli używasz programu Visual Studio, wersje Visual Studio 2019 nie są zgodne z narzędziami wymaganymi do kompilowania projektów w języku C# 8.0. Visual Studio 2019 r., w tym bezpłatną wersję Community Edition, można pobrać [tutaj.](https://visualstudio.microsoft.com/vs/)

## <a name="publish-messages-to-purview"></a>Publikowanie komunikatów w aplikacji Purview 
W tej sekcji przedstawiono sposób tworzenia aplikacji konsolowej .NET Core w celu wysyłania zdarzeń do aplikacji Purview za pośrednictwem tematu platformy Kafka centrum zdarzeń **ATLAS_HOOK**. 

## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

Następnie utwórz aplikację konsolowa .NET w języku C# w Visual Studio:

1. Uruchom **Visual Studio**.
2. W oknie Start wybierz pozycję **Utwórz nowy projekt** Aplikacja  >  **konsolowa (.NET Framework).** Wymagana jest platforma .NET w wersji 4.5.2 lub nowszej.
3. W **nazwa projektu**, wprowadź **PurviewKafkaProducer**.
4. Wybierz polecenie **Create** (Utwórz), aby utworzyć projekt.

### <a name="create-a-console-application"></a>Tworzenie aplikacji konsolowej

1. Uruchom program Visual Studio 2019. 
1. Wybierz **pozycję Create a new project (Utwórz nowy projekt).** 
1. W **oknie dialogowym** Create a new project (Tworzenie nowego projektu) wykonaj następujące kroki: Jeśli to okno dialogowe nie jest wyświetlane, wybierz z menu pozycję **File** (Plik), wybierz pozycję **New**(Nowy), a następnie wybierz pozycję **Project (Projekt).** 
    1. Wybierz **C#** jako język programowania.
    1. Wybierz **pozycję** Konsola jako typ aplikacji. 
    1. Wybierz **pozycję Aplikacja konsoli (.NET Core)** z listy wyników. 
    1. Następnie wybierz pozycję **Dalej**. 


### <a name="add-the-event-hubs-nuget-package"></a>Dodawanie pakietu NuGet usługi Event Hubs

1. Wybierz **pozycję**  >  **Narzędzia NuGet Menedżer pakietów** Menedżer pakietów  >  **z** menu. 
1. Uruchom następujące polecenie, aby zainstalować pakiet NuGet **Azure.Messaging.EventHubs** i pakiet NuGet **Azure.Messaging.EventHubs.Producer:**

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```
    
    ```cmd
    Install-Package Azure.Messaging.EventHubs.Producer
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

2. Dodaj stałe do `Program` klasy dla parametrów Event Hubs i nazwy centrum zdarzeń. 

    ```csharp
    private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
    private const string eventHubName = "<EVENT HUB NAME>";
    ```

    Przestrzeń nazw centrum zdarzeń skojarzoną z kontem purview można uzyskać, patrząc na parametry połączenia podstawowego/pomocniczego punktu końcowego platformy Atlas kafka na karcie właściwości konta Purview.

    :::image type="content" source="media/manage-eventhub-kafka-dotnet/properties.png" alt-text="Przestrzeń nazw centrum zdarzeń":::

    Nazwa centrum zdarzeń powinna być **ATLAS_HOOK** do wysyłania komunikatów do programu Purview.

3. Zastąp metodę następującą metodą i dodaj element w celu `Main` `async Main` `async ProduceMessage` wypychania komunikatów do programu Purview. Zobacz komentarze kodu, aby uzyskać szczegółowe informacje. 

    ```csharp
        static async Task Main()
        {
            // Read from the default consumer group: $Default
            string consumerGroup = EventHubConsumerClient.DefaultConsumerGroupName;
            
            / Create an event producer client to add events in the event hub
            EventHubProducerClient producer = new EventHubProducerClient(ehubNamespaceConnectionString, eventHubName);
            
            await ProduceMessage(producer);
        }
        
        static async Task ProduceMessage(EventHubProducerClient producer)
     
        {
            // Create a batch of events 
            using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

            // Add events to the batch. An event is a represented by a collection of bytes and metadata. 
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("<First event>")));
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("<Second event>")));
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("<Third event>")));

            // Use the producer client to send the batch of events to the event hub
            await producerClient.SendAsync(eventBatch);
            Console.WriteLine("A batch of 3 events has been published.");
             
        }
    ```
5. Skompilowanie projektu i upewninie się, że nie ma żadnych błędów.
6. Uruchom program i poczekaj na komunikat potwierdzenia. 

    > [!NOTE]
    > Pełny kod źródłowy z bardziej informacyjnymi komentarzami można znaleźć w tym [pliku w witrynie GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample04_PublishingEvents.md)

### <a name="sample-create-entity-json-message-to-create-a-sql-table-with-two-columns"></a>Przykładowy komunikat Create Entity JSON (Tworzenie jednostki w języku JSON) w celu utworzenia tabeli SQL z dwiema kolumnami.

```json
    
    {
    "msgCreatedBy": "nayenama",
    "message": {
    "entities": {
    "referredEntities": {
        "-1102395743156037": {
            "typeName": "azure_sql_column",
            "attributes": {
                "owner": null,
                "userTypeId": 61,
                "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderID",
                "precision": 23,
                "length": 8,
                "description": "Sales Order ID",
                "scale": 3,
                "name": "OrderID",
                "data_type": "int",
                "table": {
                    "guid": "-1102395743156036",
                    "typeName": "azure_sql_table",
                    "entityStatus": "ACTIVE",
                    "displayText": "SalesOrderTable",
                    "uniqueAttributes": {
                                "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable"
                    }
            }
            },
            "guid": "-1102395743156037",
            "version": 2
        },
        "-1102395743156038": {
         "typeName": "azure_sql_column",
            "attributes": {
                "owner": null,
                "userTypeId": 61,
                "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderDate",
                "description": "Sales Order Date",
                "scale": 3,
                "name": "OrderDate",
                "data_type": "datetime",
                "table": {
                    "guid": "-1102395743156036",
                    "typeName": "azure_sql_table",
                    "entityStatus": "ACTIVE",
                    "displayText": "SalesOrderTable",
                    "uniqueAttributes": {
                                "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable"
                    }
            }
            },
            "guid": "-1102395743156038",
            "status": "ACTIVE",
            "createdBy": "ServiceAdmin",
            "version": 0
        }
        },
        "entity": 
                {
                    "typeName": "azure_sql_table",
                    "attributes": {
                        "owner": "admin",
                        "temporary": false,
                        "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable",
                        "name" : "SalesOrderTable",
                        "description": "Sales Order Table added via Kafka",
                        "columns": [
                            {
                                "guid": "-1102395743156037",
                                "typeName": "azure_sql_column",
                                "uniqueAttributes": {
                                    "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderID"
                                }
                            },
                            {
                                "guid": "-1102395743156038",
                                "typeName": "azure_sql_column",
                                "uniqueAttributes": {
                                    "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderDate"
                                }
                            }
                        ]
                        },
                        "guid": "-1102395743156036",
                    "version": 0                
                    }
                },
        "type": "ENTITY_CREATE_V2",
        "user": "admin"
    },
    "version": {
        "version": "1.0.0"
    },
    "msgCompressionKind": "NONE",
    "msgSplitIdx": 1,
    "msgSplitCount": 1
}

``` 

## <a name="consume-messages-from-purview"></a>Wykorzystanie komunikatów z programu Purview
W tej sekcji przedstawiono sposób pisania aplikacji konsolowej .NET Core, która odbiera komunikaty z centrum zdarzeń przy użyciu procesora zdarzeń. Należy użyć centrum zdarzeń usługi ATLAS_ENTITIES do odbierania komunikatów z usługi Purview.Procesor zdarzeń upraszcza odbieranie zdarzeń z centrów zdarzeń, zarządzając trwałymi punktami kontrolnymi i równoległymi odbiorem z tych centrów zdarzeń. 

> [!WARNING]
> Jeśli uruchamiasz ten kod na Azure Stack Hub, wystąpią błędy środowiska uruchomieniowego, chyba że jest to wersja docelowa dla określonej wersji interfejsu API usługi Storage. Wynika to z tego, że zestaw SDK Event Hubs korzysta z najnowszego dostępnego interfejsu API usługi Azure Storage dostępnego na platformie Azure, który może nie być dostępny na Azure Stack Hub platformie. Azure Stack Hub może obsługiwać inną wersję zestawu SDK usługi Storage Blob niż ta, która jest zwykle dostępna na platformie Azure. Jeśli używasz usługi Azure Blob Storage jako magazynu punktów kontrolnych, sprawdź obsługiwaną wersję interfejsu API usługi [Azure Storage](/azure-stack/user/azure-stack-acs-differences?#api-version) dla Azure Stack Hub kompilacji i docelowej tej wersji w kodzie. 
>
> Jeśli na przykład używasz programu Azure Stack Hub wersji 2005, najwyższą dostępną wersją usługi Storage jest wersja 2019-02-02. Domyślnie biblioteka klienta Event Hubs SDK używa najwyższej dostępnej wersji na platformie Azure (2019-07-07 w momencie wydania zestawu SDK). W takim przypadku oprócz kroków w tej sekcji należy również dodać kod docelowy interfejsu API usługi Storage w wersji 2019-02-02. Przykład sposobu kierowania określonej wersji interfejsu API usługi Storage można znaleźć w tym [przykładzie w witrynie GitHub.](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/) 
 

### <a name="create-an-azure-storage-and-a-blob-container"></a>Tworzenie usługi Azure Storage i kontenera obiektów blob
W tym przewodniku Szybki start użyjemy usługi Azure Storage jako magazynu punktów kontrolnych. Wykonaj następujące kroki, aby utworzyć konto usługi Azure Storage. 

1. [Tworzenie konta usługi Azure Storage](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Tworzenie kontenera obiektów blob](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Uzyskiwanie parametrów połączenia z kontem magazynu](../storage/common/storage-configure-connection-string.md)

    Zanotuj ciąg połączenia i nazwę kontenera. Użyjesz ich w kodzie odbioru. 


### <a name="create-a-project-for-the-receiver"></a>Tworzenie projektu dla odbiorcy

1. W oknie Eksplorator rozwiązań kliknij prawym przyciskiem myszy rozwiązanie **EventHubQuickStart,** wskaż polecenie **Dodaj** i wybierz **pozycję Nowy projekt.** 
1. Wybierz **pozycję Aplikacja konsoli (.NET Core)** i wybierz pozycję **Dalej.** 
1. Wprowadź **wartość PurviewKafkaConsumer** w nazwie **projektu** i wybierz pozycję **Utwórz**. 

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
    
    Przestrzeń nazw centrum zdarzeń skojarzoną z kontem purview można uzyskać, patrząc na parametry połączenia podstawowego/pomocniczego punktu końcowego platformy Atlas kafka na karcie właściwości konta Purview.

    :::image type="content" source="media/manage-eventhub-kafka-dotnet/properties.png" alt-text="Przestrzeń nazw centrum zdarzeń":::

    Nazwa centrum zdarzeń powinna być **ATLAS_ENTITIES** do wysyłania komunikatów do programu Purview.

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

            // Wait for 10 seconds for the events to be processed
            await Task.Delay(TimeSpan.FromSeconds(10));

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
    > Pełny kod źródłowy z bardziej informacyjnymi komentarzami można znaleźć w tym [pliku w witrynie GitHub.](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample01_HelloWorld.md)
6. Uruchom aplikację odbiorcy. 

### <a name="sample-message-received-from-purview"></a>Przykładowy komunikat odebrany z programu Purview

```json
{
    "version":
        {"version":"1.0.0",
         "versionParts":[1]
        },
         "msgCompressionKind":"NONE",
         "msgSplitIdx":1,
         "msgSplitCount":1,
         "msgSourceIP":"10.244.155.5",
         "msgCreatedBy":
         "",
         "msgCreationTime":1618588940869,
         "message":{
            "type":"ENTITY_NOTIFICATION_V2",
            "entity":{
                "typeName":"azure_sql_table",
                    "attributes":{
                        "owner":"admin",
                        "createTime":0,
                        "qualifiedName":"mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable",
                        "name":"SalesOrderTable",
                        "description":"Sales Order Table"
                        },
                        "guid":"ead5abc7-00a4-4d81-8432-d5f6f6f60000",
                        "status":"ACTIVE",
                        "displayText":"SalesOrderTable"
                    },
                    "operationType":"ENTITY_UPDATE",
                    "eventTime":1618588940567
                }
}
```

> [!IMPORTANT]
> Atlas obecnie obsługuje następujące typy operacji: **ENTITY_CREATE_V2**, **ENTITY_PARTIAL_UPDATE_V2**, **ENTITY_FULL_UPDATE_V2**, **ENTITY_DELETE_V2**. Wypychanie komunikatów do programu Purview jest obecnie domyślnie włączone. Jeśli scenariusz obejmuje przeczytanie z aplikacji Purview, skontaktuj się z nami, ponieważ musi być na liście. (podaj identyfikator subskrypcji i nazwę konta programu Purview).


## <a name="next-steps"></a>Następne kroki
Zapoznaj się z przykładami w witrynie GitHub. 

- [Event Hubs przykłady w witrynie GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)
- [Przykłady procesorów zdarzeń w usłudze GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples)
- [Atlas introduction to notifications (Wprowadzenie do powiadomień atlasu)](https://atlas.apache.org/2.0.0/Notifications.html)