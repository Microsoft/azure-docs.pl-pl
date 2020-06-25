---
title: Wysyłanie i odbieranie zdarzeń z usługi Azure Event Hubs przy użyciu języka Java (najnowsze)
description: Ten artykuł zawiera Przewodnik dotyczący tworzenia aplikacji Java, która wysyła/odbiera zdarzenia do/z usługi Azure Event Hubs przy użyciu najnowszego pakietu Azure-Messaging-eventhubs.
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 69c994eed0907c8bb46f47016769e2078432ee99
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85320073"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-messaging-eventhubs"></a>Wysyłanie zdarzeń do i odbieranie zdarzeń z usługi Azure Event Hubs (Azure-Messaging-eventhubs) przy użyciu języka Java
Ten przewodnik Szybki Start przedstawia sposób wysyłania zdarzeń do i odbierania zdarzeń z centrum zdarzeń przy użyciu pakietu **Azure-Messaging-eventhubs** Java.

> [!IMPORTANT]
> Ten przewodnik Szybki Start używa nowego pakietu **Azure-Messaging-eventhubs** . Aby uzyskać szybki Start, który używa starych pakietów **Azure-eventhubs** i **Azure-eventhubs-EPH** , zobacz [wysyłanie i odbieranie zdarzeń przy użyciu platformy Azure-eventhubs i platformy Azure-eventhubs-EPH](event-hubs-java-get-started-send.md). 


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
Kod w tym samouczku jest oparty na [przykładzie EventProcessorClient w witrynie GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs/EventProcessorClientSample.java), który można sprawdzić, aby zobaczyć pełną działającą aplikację.

> [!NOTE]
> Jeśli korzystasz z usługi Azure Stack Hub, Ta platforma może obsługiwać inną wersję zestawu SDK obiektów BLOB Storage, która jest zazwyczaj dostępna na platformie Azure. Na przykład jeśli korzystasz z programu [Azure Stack Hub w wersji 2002](https://docs.microsoft.com/azure-stack/user/event-hubs-overview), najwyższa dostępna wersja usługi Storage to wersja 2017-11-09. W takim przypadku, oprócz kroków opisanych w tej sekcji, należy również dodać kod docelowy interfejsu API usługi Storage w wersji 2017-11-09. Aby zapoznać się z przykładem dotyczącym konkretnej wersji interfejsu API usługi Storage, zobacz [ten przykład w witrynie GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java). Więcej informacji o wersjach usługi Azure Storage obsługiwanych w centrum Azure Stack można znaleźć w centrum [Azure Stack Storage: różnice i zagadnienia](https://docs.microsoft.com/azure-stack/user/azure-stack-acs-differences).

### <a name="create-a-java-project"></a>Tworzenie projektu Java

Biblioteka klienta Java dla Event Hubs jest dostępna do użycia w projektach Maven z [repozytorium Central Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22)i może być przywoływana przy użyciu następującej deklaracji zależności w pliku projektu Maven: 

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs</artifactId>
        <version>5.0.1</version>
    </dependency>
</dependencies>
```

1. Użyj następującego kodu, aby utworzyć nową klasę o nazwie `Receiver`. Zastąp symbole zastępcze wartościami użytymi podczas tworzenia centrum zdarzeń i konta magazynu:
   
   ```java
     import com.azure.messaging.eventhubs.*;
     import com.azure.messaging.eventhubs.models.ErrorContext;
     import com.azure.messaging.eventhubs.models.EventContext;
     import java.util.concurrent.TimeUnit;
     import java.util.function.Consumer;
    
     public class Receiver {
    
         final static String connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
         final static String eventHubName = "<EVENT HUB NAME>";
         
         public static void main(String[] args) throws Exception {
    
             // function to process events
             Consumer<EventContext> processEvent = eventContext  -> {
                 System.out.print("Received event: ");
                 // print the body of the event
                 System.out.println(eventContext.getEventData().getBodyAsString());
                 eventContext.updateCheckpoint();
             };
    
             // function to process errors
             Consumer<ErrorContext> processError = errorContext -> {
                 // print the error message
                 System.out.println(errorContext.getThrowable().getMessage());
             };
    
            
             EventProcessorClient eventProcessorClient = new EventProcessorClientBuilder()
                     .connectionString(connectionString, eventHubName)
                     .processEvent(processEvent)
                     .processError(processError)
                     .consumerGroup(EventHubClientBuilder.DEFAULT_CONSUMER_GROUP_NAME)
                     .checkpointStore(new SampleCheckpointStore())
                     .buildEventProcessorClient();
    
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
    
2. Pobierz plik **SampleCheckpointStore. Java** z usługi [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs/SampleCheckpointStore.java)i dodaj go do projektu. 
3. Skompiluj program i upewnij się, że nie ma żadnych błędów. 

## <a name="run-the-applications"></a>Uruchamianie aplikacji
1. Najpierw uruchom aplikację **odbiornika** .
1. Następnie uruchom aplikację **nadawcy** . 
1. W oknie Aplikacja **odbiornika** Potwierdź, że zobaczysz zdarzenia, które zostały opublikowane przez aplikację nadawcy.
1. Naciśnij klawisz **Enter** w oknie aplikacji odbiornika, aby zatrzymać aplikację. 

## <a name="next-steps"></a>Następne kroki
Zapoznaj się [z przykładami zestawu SDK dla języka Java w witrynie GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)

