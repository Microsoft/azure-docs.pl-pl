---
title: Wysyłanie i odbieranie zdarzeń z usługi Azure Event Hubs przy użyciu języka Java (najnowsze)
description: Ten artykuł zawiera Przewodnik dotyczący tworzenia aplikacji Java, która wysyła/odbiera zdarzenia do/z usługi Azure Event Hubs przy użyciu najnowszego pakietu Azure-Messaging-eventhubs.
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: 640f6c4dcb223e55e10f7cb5d7daaa44dbd41578
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102172027"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-messaging-eventhubs"></a>Wysyłanie zdarzeń do i odbieranie zdarzeń z usługi Azure Event Hubs (Azure-Messaging-eventhubs) przy użyciu języka Java
Ten przewodnik Szybki Start przedstawia sposób wysyłania zdarzeń do i odbierania zdarzeń z centrum zdarzeń przy użyciu pakietu **Azure-Messaging-eventhubs** Java.

> [!IMPORTANT]
> Ten przewodnik Szybki Start używa nowego pakietu **Azure-Messaging-eventhubs** . Aby uzyskać szybki Start, który używa starych pakietów **Azure-eventhubs** i **Azure-eventhubs-EPH** , zobacz [wysyłanie i odbieranie zdarzeń przy użyciu platformy Azure-eventhubs i platformy Azure-eventhubs-EPH](event-hubs-java-get-started-send-legacy.md). 


## <a name="prerequisites"></a>Wymagania wstępne
Jeśli dopiero zaczynasz w usłudze Azure Event Hubs, zapoznaj się z tematem [Event Hubs Overview](event-hubs-about.md) przed wykonaniem tego przewodnika Szybki Start. 

Do wykonania kroków tego przewodnika Szybki start niezbędne jest spełnienie następujących wymagań wstępnych:

- **Subskrypcja Microsoft Azure**. Do korzystania z usług platformy Azure, w tym usługi Azure Event Hubs, potrzebna jest subskrypcja.  Jeśli nie masz istniejącego konta platformy Azure, możesz zarejestrować się w celu korzystania z [bezpłatnej wersji próbnej](https://azure.microsoft.com/free/) lub skorzystać z korzyści dla subskrybentów MSDN podczas [tworzenia konta](https://azure.microsoft.com).
- Środowisko projektowe Java. Ten przewodnik Szybki Start używa [Przezaćmienia](https://www.eclipse.org/). Wymagany jest zestaw Java Development Kit (JDK) z wersją 8 lub nowszą. 
- **Utwórz przestrzeń nazw Event Hubs i centrum zdarzeń**. Pierwszym krokiem jest użycie [Azure Portal](https://portal.azure.com) do utworzenia przestrzeni nazw typu Event Hubs i uzyskanie poświadczeń zarządzania wymaganych przez aplikację do komunikacji z centrum zdarzeń. Aby utworzyć przestrzeń nazw i centrum zdarzeń, wykonaj procedurę opisaną w [tym artykule](event-hubs-create.md). Następnie Pobierz **Parametry połączenia dla przestrzeni nazw Event Hubs** , wykonując instrukcje z artykułu: [pobieranie parametrów połączenia](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Parametry połączenia są używane w dalszej części tego przewodnika Szybki Start.

## <a name="send-events"></a>Wysyłanie zdarzeń 
W tej sekcji pokazano, jak utworzyć aplikację Java do wysyłania zdarzeń do centrum zdarzeń. 

### <a name="add-reference-to-azure-event-hubs-library"></a>Dodaj odwołanie do biblioteki Event Hubs platformy Azure

Biblioteka klienta Java dla Event Hubs jest dostępna w [repozytorium centralnym Maven](https://search.maven.org/search?q=a:azure-messaging-eventhubs). Można odwołać się do tej biblioteki za pomocą następującej deklaracji zależności w pliku projektu Maven:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-eventhubs</artifactId>
    <version>5.0.1</version>
</dependency>
```

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Pisanie kodu w celu wysyłania komunikatów do centrum zdarzeń

Na potrzeby poniższego przykładu należy w ulubionym środowisku programowania Java utworzyć nowy projekt Maven dla aplikacji konsoli lub powłoki. Dodaj klasę o nazwie `Sender` i Dodaj następujący kod do klasy:

```java
import com.azure.messaging.eventhubs.*;
import static java.nio.charset.StandardCharsets.UTF_8;

public class Sender {
       public static void main(String[] args) {
    }
}
```

### <a name="connection-string-and-event-hub"></a>Parametry połączenia i centrum zdarzeń
Ten kod używa parametrów połączenia do przestrzeni nazw Event Hubs i nazwy centrum zdarzeń do kompilowania klienta Event Hubs. 

```java
String connectionString = "<CONNECTION STRING to EVENT HUBS NAMESPACE>";
String eventHubName = "<EVENT HUB NAME>";
```

### <a name="create-an-event-hubs-producer-client"></a>Tworzenie klienta Event Hubs producent 
Ten kod tworzy obiekt klienta producenta służący do tworzenia/wysyłania zdarzeń do centrum zdarzeń. 

```java
EventHubProducerClient producer = new EventHubClientBuilder()
    .connectionString(connectionString, eventHubName)
    .buildProducerClient();
```

### <a name="prepare-a-batch-of-events"></a>Przygotowywanie partii zdarzeń
Ten kod przygotowuje partię zdarzeń. 

```java
EventDataBatch batch = producer.createBatch();
batch.tryAdd(new EventData("First event"));
batch.tryAdd(new EventData("Second event"));
batch.tryAdd(new EventData("Third event"));
batch.tryAdd(new EventData("Fourth event"));
batch.tryAdd(new EventData("Fifth event"));
```

### <a name="send-the-batch-of-events-to-the-event-hub"></a>Wysyłanie partii zdarzeń do centrum zdarzeń
Ten kod wysyła partię zdarzeń przygotowanych w poprzednim kroku do centrum zdarzeń. Poniższy blok kodu dla operacji wysyłania. 

```java
producer.send(batch);
```

### <a name="close-and-cleanup"></a>Zamknij i oczyść
Ten kod zamyka producenta. 

```java
producer.close();
```
### <a name="complete-code-to-send-events"></a>Ukończ kod do wysyłania zdarzeń
Oto kompletny kod umożliwiający wysyłanie zdarzeń do centrum zdarzeń. 

```java
import com.azure.messaging.eventhubs.*;

public class Sender {
    public static void main(String[] args) {
        final String connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
        final String eventHubName = "EVENT HUB NAME";

        // create a producer using the namespace connection string and event hub name
        EventHubProducerClient producer = new EventHubClientBuilder()
            .connectionString(connectionString, eventHubName)
            .buildProducerClient();

        // prepare a batch of events to send to the event hub    
        EventDataBatch batch = producer.createBatch();
        batch.tryAdd(new EventData("First event"));
        batch.tryAdd(new EventData("Second event"));
        batch.tryAdd(new EventData("Third event"));
        batch.tryAdd(new EventData("Fourth event"));
        batch.tryAdd(new EventData("Fifth event"));

        // send the batch of events to the event hub
        producer.send(batch);

        // close the producer
        producer.close();
    }
}
```

Skompiluj program i upewnij się, że nie ma żadnych błędów. Ten program zostanie uruchomiony po uruchomieniu programu odbiorcy. 

## <a name="receive-events"></a>Odbieranie zdarzeń
Kod w tym samouczku jest oparty na [przykładzie EventProcessorClient w witrynie GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorBlobCheckpointStoreSample.java), który można sprawdzić, aby zobaczyć pełną działającą aplikację.

> [!WARNING]
> Jeśli uruchomisz ten kod w Azure Stack centrum, wystąpią błędy środowiska uruchomieniowego, chyba że zostanie wybrana wersja interfejsu API określonej usługi Storage. Jest to spowodowane tym, że zestaw SDK Event Hubs korzysta z najnowszego dostępnego interfejsu API usługi Azure Storage dostępnego na platformie Azure, który może nie być dostępny na platformie Azure Stack Hub. Azure Stack Hub może obsługiwać inną wersję zestawu SDK obiektów blob magazynu niż te, które są zwykle dostępne na platformie Azure. Jeśli używasz magazynu blogów platformy Azure jako magazynu punktów kontrolnych, sprawdź [obsługiwaną wersję interfejsu API usługi Azure Storage dla kompilacji centrum Azure Stack](/azure-stack/user/azure-stack-acs-differences?#api-version) i wybierz tę wersję w kodzie. 
>
> Na przykład jeśli korzystasz z programu Azure Stack Hub w wersji 2005, najwyższa dostępna wersja usługi Storage to wersja 2019-02-02. Domyślnie Biblioteka klienta zestawu SDK Event Hubs używa najwyższej dostępnej wersji na platformie Azure (2019-07-07 w momencie wydania zestawu SDK). W takim przypadku, oprócz kroków opisanych w tej sekcji, należy również dodać kod docelowy interfejsu API usługi Storage w wersji 2019-02-02. Aby zapoznać się z przykładem dotyczącym konkretnej wersji interfejsu API usługi Storage, zobacz [ten przykład w witrynie GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java). 


### <a name="create-an-azure-storage-and-a-blob-container"></a>Tworzenie usługi Azure Storage i kontenera obiektów BLOB
W tym przewodniku szybki start użyjesz usługi Azure Storage (w Blob Storage) jako magazynu punktów kontrolnych. Punkt kontrolny jest procesem, przez który procesor zdarzeń oznacza lub zatwierdza pozycję ostatniego pomyślnie przetworzonego zdarzenia w ramach partycji. Oznaczanie punktu kontrolnego zwykle odbywa się w funkcji, która przetwarza zdarzenia. Aby dowiedzieć się więcej na temat punktów kontrolnych, zobacz [procesor zdarzeń](event-processor-balance-partition-load.md).

Wykonaj następujące kroki, aby utworzyć konto usługi Azure Storage. 

1. [Tworzenie konta usługi Azure Storage](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Tworzenie kontenera obiektów blob](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Pobierz parametry połączenia z kontem magazynu](../storage/common/storage-configure-connection-string.md)

    Zanotuj **Parametry połączenia** i **nazwę kontenera**. Będziesz ich używać w kodzie Receive. 

### <a name="add-event-hubs-libraries-to-your-java-project"></a>Dodawanie bibliotek Event Hubs do projektu Java
Dodaj następujące zależności w pliku pom.xml. 

- [Azure-Messaging — eventhubs](https://search.maven.org/search?q=a:azure-messaging-eventhubs)
- [Azure-Messaging-eventhubs-checkpointstore-BLOB](https://search.maven.org/search?q=a:azure-messaging-eventhubs-checkpointstore-blob)

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs</artifactId>
        <version>5.1.1</version>
    </dependency>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs-checkpointstore-blob</artifactId>
        <version>1.5.0</version>
    </dependency>
</dependencies>
```

1. Dodaj następujące instrukcje **importu** w górnej części pliku Java. 

    ```java
    import com.azure.messaging.eventhubs.EventHubClientBuilder;
    import com.azure.messaging.eventhubs.EventProcessorClient;
    import com.azure.messaging.eventhubs.EventProcessorClientBuilder;
    import com.azure.messaging.eventhubs.checkpointstore.blob.BlobCheckpointStore;
    import com.azure.messaging.eventhubs.models.ErrorContext;
    import com.azure.messaging.eventhubs.models.EventContext;
    import com.azure.storage.blob.BlobContainerAsyncClient;
    import com.azure.storage.blob.BlobContainerClientBuilder;
    import java.util.function.Consumer;
    import java.util.concurrent.TimeUnit;
    ```
2. Utwórz klasę o nazwie `Receiver` i Dodaj następujące zmienne ciągu do klasy. Zastąp symbole zastępcze prawidłowymi wartościami. 
    ```java
    private static final String EH_NAMESPACE_CONNECTION_STRING = "<EVENT HUBS NAMESPACE CONNECTION STRING>";
    private static final String eventHubName = "<EVENT HUB NAME>";
    private static final String STORAGE_CONNECTION_STRING = "<AZURE STORAGE CONNECTION STRING>";
    private static final String STORAGE_CONTAINER_NAME = "<AZURE STORAGE CONTAINER NAME>";
    ```
3. Dodaj następującą `main` metodę do klasy. 

    ```java
    public static void main(String[] args) throws Exception {
        // Create a blob container client that you use later to build an event processor client to receive and process events
        BlobContainerAsyncClient blobContainerAsyncClient = new BlobContainerClientBuilder()
            .connectionString(STORAGE_CONNECTION_STRING) 
            .containerName(STORAGE_CONTAINER_NAME) 
            .buildAsyncClient();
    
        // Create a builder object that you will use later to build an event processor client to receive and process events and errors.
        EventProcessorClientBuilder eventProcessorClientBuilder = new EventProcessorClientBuilder()
            .connectionString(EH_NAMESPACE_CONNECTION_STRING, eventHubName) 
            .consumerGroup(EventHubClientBuilder.DEFAULT_CONSUMER_GROUP_NAME)
            .processEvent(PARTITION_PROCESSOR) 
            .processError(ERROR_HANDLER) 
            .checkpointStore(new BlobCheckpointStore(blobContainerAsyncClient)); 

        // Use the builder object to create an event processor client 
        EventProcessorClient eventProcessorClient = eventProcessorClientBuilder.buildEventProcessorClient();

        System.out.println("Starting event processor");
        eventProcessorClient.start();

        System.out.println("Press enter to stop.");
        System.in.read();

        System.out.println("Stopping event processor");
        eventProcessorClient.stop();
        System.out.println("Event processor stopped.");

        System.out.println("Exiting process");
    }    
    ```
4. Dodaj dwie metody pomocnika ( `PARTITION_PROCESSOR` i `ERROR_HANDLER` ), które przetwarzają zdarzenia i błędy do `Receiver` klasy. 

    ```java
    public static final Consumer<EventContext> PARTITION_PROCESSOR = eventContext -> {
        System.out.printf("Processing event from partition %s with sequence number %d with body: %s %n", 
                eventContext.getPartitionContext().getPartitionId(), eventContext.getEventData().getSequenceNumber(), eventContext.getEventData().getBodyAsString());

        if (eventContext.getEventData().getSequenceNumber() % 10 == 0) {
            eventContext.updateCheckpoint();
        }
    };
    
    public static final Consumer<ErrorContext> ERROR_HANDLER = errorContext -> {
        System.out.printf("Error occurred in partition processor for partition %s, %s.%n",
            errorContext.getPartitionContext().getPartitionId(),
            errorContext.getThrowable());
    };    
    ```
3. Kompletny kod powinien wyglądać następująco: 

    ```java

    import com.azure.messaging.eventhubs.EventHubClientBuilder;
    import com.azure.messaging.eventhubs.EventProcessorClient;
    import com.azure.messaging.eventhubs.EventProcessorClientBuilder;
    import com.azure.messaging.eventhubs.checkpointstore.blob.BlobCheckpointStore;
    import com.azure.messaging.eventhubs.models.ErrorContext;
    import com.azure.messaging.eventhubs.models.EventContext;
    import com.azure.storage.blob.BlobContainerAsyncClient;
    import com.azure.storage.blob.BlobContainerClientBuilder;
    import java.util.function.Consumer;
    import java.util.concurrent.TimeUnit;
    
    public class Receiver {
        
        private static final String EH_NAMESPACE_CONNECTION_STRING = "<EVENT HUBS NAMESPACE CONNECTION STRING>";
        private static final String eventHubName = "<EVENT HUB NAME>";
        private static final String STORAGE_CONNECTION_STRING = "<AZURE STORAGE CONNECTION STRING>";
        private static final String STORAGE_CONTAINER_NAME = "<AZURE STORAGE CONTAINER NAME>";
    
        public static final Consumer<EventContext> PARTITION_PROCESSOR = eventContext -> {
        System.out.printf("Processing event from partition %s with sequence number %d with body: %s %n", 
                eventContext.getPartitionContext().getPartitionId(), eventContext.getEventData().getSequenceNumber(), eventContext.getEventData().getBodyAsString());

            if (eventContext.getEventData().getSequenceNumber() % 10 == 0) {
                eventContext.updateCheckpoint();
            }
        };
        
        public static final Consumer<ErrorContext> ERROR_HANDLER = errorContext -> {
            System.out.printf("Error occurred in partition processor for partition %s, %s.%n",
                errorContext.getPartitionContext().getPartitionId(),
                errorContext.getThrowable());
        };
        
        public static void main(String[] args) throws Exception {
            BlobContainerAsyncClient blobContainerAsyncClient = new BlobContainerClientBuilder()
                .connectionString(STORAGE_CONNECTION_STRING)
                .containerName(STORAGE_CONTAINER_NAME)
                .buildAsyncClient();
    
            EventProcessorClientBuilder eventProcessorClientBuilder = new EventProcessorClientBuilder()
                .connectionString(EH_NAMESPACE_CONNECTION_STRING, eventHubName)
                .consumerGroup(EventHubClientBuilder.DEFAULT_CONSUMER_GROUP_NAME)
                .processEvent(PARTITION_PROCESSOR)
                .processError(ERROR_HANDLER)
                .checkpointStore(new BlobCheckpointStore(blobContainerAsyncClient));
    
            EventProcessorClient eventProcessorClient = eventProcessorClientBuilder.buildEventProcessorClient();

            System.out.println("Starting event processor");
            eventProcessorClient.start();
        
            System.out.println("Press enter to stop.");
            System.in.read();
        
            System.out.println("Stopping event processor");
            eventProcessorClient.stop();
            System.out.println("Event processor stopped.");
        
            System.out.println("Exiting process");
        }
        
    }
    ```
3. Skompiluj program i upewnij się, że nie ma żadnych błędów. 

## <a name="run-the-applications"></a>Uruchamianie aplikacji
1. Najpierw uruchom aplikację **odbiornika** .
1. Następnie uruchom aplikację **nadawcy** . 
1. W oknie Aplikacja **odbiornika** Potwierdź, że zobaczysz zdarzenia, które zostały opublikowane przez aplikację nadawcy.
1. Naciśnij klawisz **Enter** w oknie aplikacji odbiornika, aby zatrzymać aplikację. 

## <a name="next-steps"></a>Następne kroki
Zobacz następujące przykłady w witrynie GitHub:

- [Azure-Messaging — przykłady eventhubs](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [Azure-Messaging-eventhubs-checkpointstore-przykłady obiektów BLOB](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob).  
