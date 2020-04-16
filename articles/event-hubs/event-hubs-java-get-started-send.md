---
title: Wysyłanie lub odbieranie zdarzeń z usługi Azure Event Hubs przy użyciu języka Java (starsza wersja)
description: Ten artykuł zawiera przewodnik tworzenia aplikacji Java, która wysyła/odbiera zdarzenia do/z usługi Azure Event Hubs przy użyciu starego pakietu azure eventhubs.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 02/11/2020
ms.author: spelluru
ms.openlocfilehash: 379739533e15e60bc47bfc883a67037d4a58d0e0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417619"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-eventhubs"></a>Używanie języka Java do wysyłania zdarzeń do usługi Azure Event Hubs lub odbierania ich z usługi Azure Event Hubs (azure-eventhubs)

Ten przewodnik Szybki start pokazuje, jak wysyłać zdarzenia i odbierać zdarzenia z centrum zdarzeń przy użyciu pakietu **Java azure eventhubs.**

> [!WARNING]
> Ten przewodnik Szybki start używa starych pakietów **azure-eventhubs** i **azure-eventhubs-eph.** Aby uzyskać przewodnik Szybki start, który korzysta z najnowszego pakietu **azure-messaging-eventhubs,** zobacz [Wysyłanie i odbieranie zdarzeń przy użyciu witryn azure-messaging-eventhubs.](get-started-java-send-v2.md) Aby przenieść aplikację z używania starego pakietu do nowego, zobacz [Przewodnik do migracji z azure eventhubs do azure-messaging-eventhubs](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/migration-guide.md). 


## <a name="prerequisites"></a>Wymagania wstępne

Jeśli jesteś nowy w usłudze Azure Event Hubs, zobacz [Centrum zdarzeń omówienie](event-hubs-about.md) przed rozpoczęciem tego przewodnika Szybki start. 

Do wykonania kroków tego przewodnika Szybki start niezbędne jest spełnienie następujących wymagań wstępnych:

- **Subskrypcja platformy Microsoft Azure**. Aby korzystać z usług platformy Azure, w tym usługi Azure Event Hubs, potrzebujesz subskrypcji.  Jeśli nie masz istniejącego konta platformy Azure, możesz zarejestrować się w celu [uzyskania bezpłatnej wersji próbnej](https://azure.microsoft.com/free/) lub skorzystać z korzyści dla subskrybenta MSDN podczas [tworzenia konta.](https://azure.microsoft.com)
- Środowisko programistyczne Java. Ten szybki start używa [Eclipse](https://www.eclipse.org/).
- **Utwórz obszar nazw Centrów zdarzeń i centrum zdarzeń**. Pierwszym krokiem jest użycie [witryny Azure Portal](https://portal.azure.com) do utworzenia obszaru nazw typu Event Hubs i uzyskania poświadczeń zarządzania, których aplikacja potrzebuje do komunikowania się z centrum zdarzeń. Aby utworzyć przestrzeń nazw i centrum zdarzeń, wykonaj procedurę opisaną w [tym artykule](event-hubs-create.md). Następnie uzyskaj wartość klucza dostępu dla Centrum zdarzeń, wykonując instrukcje z artykułu: [Pobierz parametry połączenia](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Klucz dostępu w kodzie, który piszesz w dalszej części tego przewodnika Szybki start. Domyślna nazwa klucza to: **RootManageSharedAccessKey**.

## <a name="send-events"></a>Wysyłanie zdarzeń 
W tej sekcji pokazano, jak utworzyć aplikację Java do wysyłania zdarzeń centrum zdarzeń. 

> [!NOTE]
> Ten przewodnik Szybki start możesz pobrać jako przykład z witryny [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend), zastąpić ciągi `EventHubConnectionString` i `EventHubName` wartościami swojego centrum zdarzeń, a następnie uruchomić go. Alternatywnie można wykonać kroki opisane w tym przewodniku Szybki start, aby utworzyć własne.

### <a name="add-reference-to-azure-event-hubs-library"></a>Dodawanie odwołania do biblioteki usługi Azure Event Hubs

Biblioteka klienta Java dla centrów zdarzeń jest dostępna do użytku w projektach Maven z [repozytorium Maven Central.](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) Do tej biblioteki można odwołać się przy użyciu następującej deklaracji zależności wewnątrz pliku projektu Maven:

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
```

W przypadku różnych typów środowisk kompilacji można jawnie uzyskać najnowsze zwolnione pliki JAR z [repozytorium Maven Central](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22).  

W przypadku prostego wydawcy zdarzeń zaimportuj pakiet *com.microsoft.azure.eventhubs* dla klas klientów centrów zdarzeń i pakiet *com.microsoft.azure.servicebus* dla klas narzędzi, takich jak typowe wyjątki, które są współużytkowane klientowi obsługi wiadomości usługi Azure Service Bus. 

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Pisanie kodu w celu wysyłania komunikatów do centrum zdarzeń

Na potrzeby poniższego przykładu należy w ulubionym środowisku programowania Java utworzyć nowy projekt Maven dla aplikacji konsoli lub powłoki. Dodaj klasę `SimpleSend`o nazwie i dodaj do klasy następujący kod:

```java
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
import com.microsoft.azure.eventhubs.EventData;
import com.microsoft.azure.eventhubs.EventHubClient;
import com.microsoft.azure.eventhubs.EventHubException;

import java.io.IOException;
import java.nio.charset.Charset;
import java.time.Instant;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Executors;
import java.util.concurrent.ScheduledExecutorService;

public class SimpleSend {

    public static void main(String[] args)
            throws EventHubException, ExecutionException, InterruptedException, IOException {
            
            
    }
 }
```

### <a name="construct-connection-string"></a>Konstruuj parametry połączenia

Klasa ConnectionStringBuilder służy do konstruowania wartości ciągu połączenia w celu przekazania do wystąpienia klienta centrum zdarzeń. Zastąp symbole zastępcze wartościami uzyskanymi podczas tworzenia obszaru nazw i centrum zdarzeń:

```java
        final ConnectionStringBuilder connStr = new ConnectionStringBuilder()
                .setNamespaceName("<EVENTHUB NAMESPACE") 
                .setEventHubName("EVENT HUB")
                .setSasKeyName("RootManageSharedAccessKey")
                .setSasKey("SHARED ACCESS KEY");
```

### <a name="write-code-to-send-events"></a>Napisz kod do wysyłania zdarzeń

Utwórz zdarzenie pojedynczej, przekształcając ciąg w jego kodowanie bajtów UTF-8. Następnie utwórz nowe wystąpienie klienta usługi Event Hubs z ciągu połączenia i wyślij wiadomość:   

```java 
        final Gson gson = new GsonBuilder().create();

        // The Executor handles all asynchronous tasks and this is passed to the EventHubClient instance.
        // This enables the user to segregate their thread pool based on the work load.
        // This pool can then be shared across multiple EventHubClient instances.
        // The following sample uses a single thread executor, as there is only one EventHubClient instance,
        // handling different flavors of ingestion to Event Hubs here.
        final ScheduledExecutorService executorService = Executors.newScheduledThreadPool(4);

        // Each EventHubClient instance spins up a new TCP/TLS connection, which is expensive.
        // It is always a best practice to reuse these instances. The following sample shows this.
        final EventHubClient ehClient = EventHubClient.createSync(connStr.toString(), executorService);


        try {
            for (int i = 0; i < 10; i++) {

                String payload = "Message " + Integer.toString(i);
                byte[] payloadBytes = gson.toJson(payload).getBytes(Charset.defaultCharset());
                EventData sendEvent = EventData.create(payloadBytes);

                // Send - not tied to any partition
                // Event Hubs service will round-robin the events across all Event Hubs partitions.
                // This is the recommended & most reliable way to send to Event Hubs.
                ehClient.sendSync(sendEvent);
            }

            System.out.println(Instant.now() + ": Send Complete...");
            System.out.println("Press Enter to stop.");
            System.in.read();
        } finally {
            ehClient.closeSync();
            executorService.shutdown();
        }

``` 

Skompiluj i uruchom program i upewnij się, że nie ma żadnych błędów.

Gratulacje! Wysłano komunikaty do centrum zdarzeń.

### <a name="appendix-how-messages-are-routed-to-eventhub-partitions"></a>Dodatek: Jak wiadomości są kierowane do partycji EventHub

Zanim wiadomości są pobierane przez konsumentów, muszą być publikowane na partycjach najpierw przez wydawców. Gdy wiadomości są publikowane w Centrum zdarzeń synchronicznie przy użyciu metody sendSync() na com.microsoft.azure.eventhubs.EventHubClient obiektu, wiadomość może być wysłana do określonej partycji lub dystrybuowane do wszystkich dostępnych partycji w sposób okrężny w zależności od tego, czy klucz partycji jest określony, czy nie.

Po określeniu ciągu reprezentującego klucz partycji klucz zostanie on hashed, aby określić, do której partycji ma zostać wysłane zdarzenie.

Gdy klucz partycji nie jest ustawiony, wiadomości będą zaokrąglane do wszystkich dostępnych partycji

```java
// Serialize the event into bytes
byte[] payloadBytes = gson.toJson(messagePayload).getBytes(Charset.defaultCharset());

// Use the bytes to construct an {@link EventData} object
EventData sendEvent = EventData.create(payloadBytes);

// Transmits the event to event hub without a partition key
// If a partition key is not set, then we will round-robin to all topic partitions
eventHubClient.sendSync(sendEvent);

//  the partitionKey will be hash'ed to determine the partitionId to send the eventData to.
eventHubClient.sendSync(sendEvent, partitionKey);

// close the client at the end of your program
eventHubClient.closeSync();

```

## <a name="receive-events"></a>Odbieranie zdarzeń
Kod w tym samouczku jest oparty na [kodzie EventProcessorSample na GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/EventProcessorSample), który można sprawdzić, aby zobaczyć pełną działającą aplikację.

### <a name="receive-messages-with-eventprocessorhost-in-java"></a>Odbieranie komunikatów EventProcessorHost w języku Java

**EventProcessorHost** to klasa Java, która upraszcza odbieranie zdarzeń z centrów zdarzeń, zarządzając trwałymi punktami kontrolnymi i równoległymi odbierami z tych centrów zdarzeń. Za pomocą EventProcessorHost, można podzielić zdarzenia między wielu odbiorników, nawet wtedy, gdy hostowane w różnych węzłach. W tym przykładzie przedstawiono, jak używać klasy EventProcessorHost dla jednego odbiornika.

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Aby korzystać z usługi EventProcessorHost, musisz mieć [konto usługi Azure Storage][konto usługi Azure Storage]:

1. Zaloguj się w [witrynie Azure portal](https://portal.azure.com)i wybierz pozycję **Utwórz zasób** po lewej stronie ekranu.
2. Wybierz **pozycję Magazyn**, a następnie wybierz pozycję Konto **magazynu**. W oknie **Tworzenie konta magazynu** wpisz nazwę konta magazynu. Wypełnij pozostałe pola, wybierz żądany region, a następnie wybierz pozycję **Utwórz**.
   
    ![Tworzenie konta magazynu w witrynie Azure portal](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-azure-storage-account.png)

3. Wybierz nowo utworzone konto magazynu, a następnie wybierz pozycję **Klucze dostępu:**
   
    ![Uzyskiwanie kluczy dostępu w witrynie Azure portal](./media/event-hubs-dotnet-framework-getstarted-receive-eph/select-azure-storage-access-keys.png)

    Skopiuj wartość key1 do lokalizacji tymczasowej. Będziesz jej używać w dalszej części tego samouczka.

### <a name="create-a-java-project-using-the-eventprocessor-host"></a>Tworzenie projektu języka Java za pomocą hosta EventProcessor

Biblioteka klienta Java dla centrów zdarzeń jest dostępna do użycia w projektach Maven z [repozytorium Centralnego Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22)i można się do niej odwoływać przy użyciu następującej deklaracji zależności wewnątrz pliku projektu Maven: 

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs-eph</artifactId>
    <version>2.4.0</version>
</dependency>
```

W przypadku różnych typów środowisk kompilacji można jawnie uzyskać najnowsze zwolnione pliki JAR z [repozytorium Maven Central](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22).

1. Na potrzeby poniższego przykładu należy w ulubionym środowisku programowania Java utworzyć nowy projekt Maven dla aplikacji konsoli lub powłoki. Klasa jest `ErrorNotificationHandler`nazywana .     
   
    ```java
    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   
    public class ErrorNotificationHandler implements Consumer<ExceptionReceivedEventArgs>
    {
        @Override
        public void accept(ExceptionReceivedEventArgs t)
        {
            System.out.println("SAMPLE: Host " + t.getHostname() + " received general error notification during " + t.getAction() + ": " + t.getException().toString());
        }
    }
    ```
2. Użyj następującego kodu, aby utworzyć nową klasę o nazwie `EventProcessorSample`. Zastąp symbole zastępcze wartościami używanymi podczas tworzenia centrum zdarzeń i konta magazynu:
   
   ```java
   package com.microsoft.azure.eventhubs.samples.eventprocessorsample;

   import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
   import com.microsoft.azure.eventhubs.EventData;
   import com.microsoft.azure.eventprocessorhost.CloseReason;
   import com.microsoft.azure.eventprocessorhost.EventProcessorHost;
   import com.microsoft.azure.eventprocessorhost.EventProcessorOptions;
   import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   import com.microsoft.azure.eventprocessorhost.IEventProcessor;
   import com.microsoft.azure.eventprocessorhost.PartitionContext;

   import java.util.concurrent.ExecutionException;
   import java.util.function.Consumer;

   public class EventProcessorSample
   {
       public static void main(String args[]) throws InterruptedException, ExecutionException
       {
           String consumerGroupName = "$Default";
           String namespaceName = "----NamespaceName----";
           String eventHubName = "----EventHubName----";
           String sasKeyName = "----SharedAccessSignatureKeyName----";
           String sasKey = "----SharedAccessSignatureKey----";
           String storageConnectionString = "----AzureStorageConnectionString----";
           String storageContainerName = "----StorageContainerName----";
           String hostNamePrefix = "----HostNamePrefix----";
        
           ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey);
        
           EventProcessorHost host = new EventProcessorHost(
                EventProcessorHost.createHostName(hostNamePrefix),
                eventHubName,
                consumerGroupName,
                eventHubConnectionString.toString(),
                storageConnectionString,
                storageContainerName);
        
           System.out.println("Registering host named " + host.getHostName());
           EventProcessorOptions options = new EventProcessorOptions();
           options.setExceptionNotification(new ErrorNotificationHandler());

           host.registerEventProcessor(EventProcessor.class, options)
           .whenComplete((unused, e) ->
           {
               if (e != null)
               {
                   System.out.println("Failure while registering: " + e.toString());
                   if (e.getCause() != null)
                   {
                       System.out.println("Inner exception: " + e.getCause().toString());
                   }
               }
           })
           .thenAccept((unused) ->
           {
               System.out.println("Press enter to stop.");
               try 
               {
                   System.in.read();
               }
               catch (Exception e)
               {
                   System.out.println("Keyboard read failed: " + e.toString());
               }
           })
           .thenCompose((unused) ->
           {
               return host.unregisterEventProcessor();
           })
           .exceptionally((e) ->
           {
               System.out.println("Failure while unregistering: " + e.toString());
               if (e.getCause() != null)
               {
                   System.out.println("Inner exception: " + e.getCause().toString());
               }
               return null;
           })
           .get(); // Wait for everything to finish before exiting main!
        
           System.out.println("End of sample");
       }
   }
   ```
3. Utwórz jeszcze `EventProcessor`jedną klasę o nazwie , używając następującego kodu:
   
    ```java
    public static class EventProcessor implements IEventProcessor
    {
        private int checkpointBatchingCount = 0;

        // OnOpen is called when a new event processor instance is created by the host. 
        @Override
        public void onOpen(PartitionContext context) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is opening");
        }

        // OnClose is called when an event processor instance is being shut down. 
        @Override
        public void onClose(PartitionContext context, CloseReason reason) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is closing for reason " + reason.toString());
        }
        
        // onError is called when an error occurs in EventProcessorHost code that is tied to this partition, such as a receiver failure.
        @Override
        public void onError(PartitionContext context, Throwable error)
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " onError: " + error.toString());
        }

        // onEvents is called when events are received on this partition of the Event Hub. 
        @Override
        public void onEvents(PartitionContext context, Iterable<EventData> events) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " got event batch");
            int eventCount = 0;
            for (EventData data : events)
            {
                try
                {
                    System.out.println("SAMPLE (" + context.getPartitionId() + "," + data.getSystemProperties().getOffset() + "," +
                            data.getSystemProperties().getSequenceNumber() + "): " + new String(data.getBytes(), "UTF8"));
                    eventCount++;
                    
                    // Checkpointing persists the current position in the event stream for this partition and means that the next
                    // time any host opens an event processor on this event hub+consumer group+partition combination, it will start
                    // receiving at the event after this one. 
                    this.checkpointBatchingCount++;
                    if ((checkpointBatchingCount % 5) == 0)
                    {
                        System.out.println("SAMPLE: Partition " + context.getPartitionId() + " checkpointing at " +
                            data.getSystemProperties().getOffset() + "," + data.getSystemProperties().getSequenceNumber());
                        // Checkpoints are created asynchronously. It is important to wait for the result of checkpointing
                        // before exiting onEvents or before creating the next checkpoint, to detect errors and to ensure proper ordering.
                        context.checkpoint(data).get();
                    }
                }
                catch (Exception e)
                {
                    System.out.println("Processing failed for an event: " + e.toString());
                }
            }
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " batch size was " + eventCount + " for host " + context.getOwner());
        }
    }
    ```

Instrukcje w tym samouczku obejmują użycie pojedynczego wystąpienia hosta EventProcessorHost. Aby zwiększyć przepływność, zaleca się uruchomienie wielu wystąpień EventProcessorHost, najlepiej na oddzielnych komputerach.  Zapewnia również nadmiarowość. W tych przypadkach różne wystąpienia automatycznie koordynują się ze sobą w celu równoważenia obciążenia odebranych zdarzeń. Jeśli chcesz, aby wiele odbiorników przetwarzało *wszystkie* zdarzenia, musisz użyć koncepcji **ConsumerGroup**. W przypadku odbierania zdarzeń z różnych komputerów dobrym rozwiązaniem może być określenie nazw wystąpień hosta EventProcessorHost w oparciu o komputery (lub role), w których są one wdrażane.

### <a name="publishing-messages-to-eventhub"></a>Publikowanie wiadomości w ujrze eventhub

Zanim wiadomości są pobierane przez konsumentów, muszą być publikowane na partycjach najpierw przez wydawców. Warto zauważyć, że gdy wiadomości są publikowane w Centrum zdarzeń synchronicznie przy użyciu sendSync() metody na com.microsoft.azure.eventhubs.EventHubClient obiektu, wiadomość może być wysyłana do określonej partycji lub dystrybuowane do wszystkich dostępnych partycji w sposób okrężny w zależności od tego, czy klucz partycji jest określony, czy nie.

Po określeniu ciągu reprezentującego klucz partycji klucz jest skrótowy, aby określić, do której partycji należy wysłać zdarzenie.

Gdy klucz partycji nie jest ustawiony, wiadomości są okrężne do wszystkich dostępnych partycji

```java
// Serialize the event into bytes
byte[] payloadBytes = gson.toJson(messagePayload).getBytes(Charset.defaultCharset());

// Use the bytes to construct an {@link EventData} object
EventData sendEvent = EventData.create(payloadBytes);

// Transmits the event to event hub without a partition key
// If a partition key is not set, then we will round-robin to all topic partitions
eventHubClient.sendSync(sendEvent);

//  the partitionKey will be hash'ed to determine the partitionId to send the eventData to.
eventHubClient.sendSync(sendEvent, partitionKey);

```

### <a name="implementing-a-custom-checkpointmanager-for-eventprocessorhost-eph"></a>Implementowanie niestandardowego programu CheckpointManager dla usługi EventProcessorHost (EPH)

Interfejs API udostępnia mechanizm do zaimplementowania menedżera niestandardowych punktów kontrolnych dla scenariuszy, w których domyślna implementacja nie jest zgodna z przypadkiem użycia.

Domyślny menedżer punktów kontrolnych używa magazynu obiektów blob, ale jeśli zastąpisz menedżera punktów kontrolnych używanego przez EPH z własną implementacją, możesz użyć dowolnego magazynu, który ma zostać poparty implementacją menedżera punktów kontrolnych.

Tworzenie klasy implementacji interfejsu com.microsoft.azure.eventprocessorhost.ICheckpointManager

Użyj niestandardowej implementacji menedżera punktów kontrolnych (com.microsoft.azure.eventprocessorhost.ICheckpointManager)

W ramach implementacji można zastąpić domyślny mechanizm punktów kontrolnych i zaimplementować nasze własne punkty kontrolne na podstawie własnego magazynu danych (takich jak SQL Server, CosmosDB i Azure Cache for Redis). Zaleca się, że magazyn używany do kopii zapasowej implementacji menedżera punktów kontrolnych jest dostępny dla wszystkich wystąpień EPH, które przetwarzają zdarzenia dla grupy konsumentów.

Można użyć dowolnego magazynu danych, który jest dostępny w danym środowisku.

Com.microsoft.azure.eventprocessorhost.EventProcessorHost klasy zapewnia dwa konstruktory, które umożliwiają zastąpienie menedżera punktu kontrolnego dla EventProcessorHost.


## <a name="next-steps"></a>Następne kroki
Przeczytaj następujące artykuły: 

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funkcje i terminologia w usłudze Azure Event Hubs](event-hubs-features.md)
- [Event Hubs — często zadawane pytania](event-hubs-faq.md)

