---
title: Wysyłanie lub odbieranie zdarzeń z usługi Azure Event Hubs przy użyciu języka Java (najnowsze)
description: Ten artykuł zawiera przewodnik tworzenia aplikacji Java, która wysyła/odbiera zdarzenia do/z usługi Azure Event Hubs przy użyciu najnowszego pakietu azure-messaging-eventhubs.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 04/21/2020
ms.author: spelluru
ms.openlocfilehash: ca22f4481750abb3bd4432c8b42fbce93ede8ffd
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770879"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-messaging-eventhubs"></a>Używanie języka Java do wysyłania zdarzeń do centrów zdarzeń platformy Azure lub odbierania ich z usługi Azure Event Hubs (azure-messaging-eventhubs)
Ten przewodnik Szybki start pokazuje, jak wysyłać zdarzenia i odbierać zdarzenia z centrum zdarzeń przy użyciu pakietu Java **azure-messaging-eventhubs.**

> [!IMPORTANT]
> Ten przewodnik Szybki start używa nowego pakietu **azure-messaging-eventhubs.** Aby uzyskać przewodnik Szybki start, który używa starych pakietów **azure-eventhubs** i **azure-eventhubs-eph,** zobacz [Wysyłanie i odbieranie zdarzeń przy użyciu azure-eventhubs i azure-eventhubs-eph.](event-hubs-java-get-started-send.md) 


## <a name="prerequisites"></a>Wymagania wstępne
Jeśli jesteś nowym użytkownikiem usługi Azure Event Hubs, zobacz [Omówienie centrów zdarzeń](event-hubs-about.md) przed rozpoczęciem tego przewodnika Szybki start. 

Do wykonania kroków tego przewodnika Szybki start niezbędne jest spełnienie następujących wymagań wstępnych:

- **Subskrypcja platformy Microsoft Azure**. Aby korzystać z usług platformy Azure, w tym usługi Azure Event Hubs, potrzebujesz subskrypcji.  Jeśli nie masz istniejącego konta platformy Azure, możesz zarejestrować się w celu [uzyskania bezpłatnej wersji próbnej](https://azure.microsoft.com/free/) lub skorzystać z korzyści dla subskrybenta MSDN podczas [tworzenia konta.](https://azure.microsoft.com)
- Środowisko programistyczne Java. Ten szybki start używa [Eclipse](https://www.eclipse.org/). Wymagany jest zestaw Java Development Kit (JDK) w wersji 8 lub wyższej. 
- **Utwórz obszar nazw Centrów zdarzeń i centrum zdarzeń**. Pierwszym krokiem jest użycie [witryny Azure Portal](https://portal.azure.com) do utworzenia obszaru nazw typu Event Hubs i uzyskania poświadczeń zarządzania, których aplikacja potrzebuje do komunikowania się z centrum zdarzeń. Aby utworzyć przestrzeń nazw i centrum zdarzeń, wykonaj procedurę opisaną w [tym artykule](event-hubs-create.md). Następnie pobierz **ciąg połączenia dla obszaru nazw Centrum zdarzeń,** postępując zgodnie z instrukcjami z artykułu: [Pobierz parametry połączenia](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Ciąg połączenia należy użyć w dalszej części tego przewodnika Szybki start.

## <a name="send-events"></a>Wysyłanie zdarzeń 
W tej sekcji pokazano, jak utworzyć aplikację Java do wysyłania zdarzeń centrum zdarzeń. 

### <a name="add-reference-to-azure-event-hubs-library"></a>Dodawanie odwołania do biblioteki usługi Azure Event Hubs

Biblioteka klienta Java dla centrów zdarzeń jest dostępna w [repozytorium Centralnym Maven.](https://search.maven.org/search?q=a:azure-messaging-eventhubs) Do tej biblioteki można odwołać się przy użyciu następującej deklaracji zależności wewnątrz pliku projektu Maven:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-eventhubs</artifactId>
    <version>5.0.1</version>
</dependency>
```

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Pisanie kodu w celu wysyłania komunikatów do centrum zdarzeń

Na potrzeby poniższego przykładu należy w ulubionym środowisku programowania Java utworzyć nowy projekt Maven dla aplikacji konsoli lub powłoki. Dodaj klasę `Sender`o nazwie i dodaj do klasy następujący kod:

```java
import com.azure.messaging.eventhubs.*;
import static java.nio.charset.StandardCharsets.UTF_8;

public class Sender {
       public static void main(String[] args) {
    }
}
```

### <a name="connection-string-and-event-hub"></a>Ciąg połączenia i centrum zdarzeń
Ten kod używa ciągu połączenia do obszaru nazw Centrum zdarzeń i nazwy centrum zdarzeń do tworzenia klienta centrum zdarzeń. 

```java
String connectionString = "<CONNECTION STRING to EVENT HUBS NAMESPACE>";
String eventHubName = "<EVENT HUB NAME>";
```

### <a name="create-an-event-hubs-producer-client"></a>Tworzenie klienta producenta centrów zdarzeń 
Ten kod tworzy obiekt klienta producenta, który jest używany do tworzenia/wysyłania zdarzeń do centrum zdarzeń. 

```java
EventHubProducerClient producer = new EventHubClientBuilder()
    .connectionString(connectionString, eventHubName)
    .buildProducerClient();
```

### <a name="prepare-a-batch-of-events"></a>Przygotowanie partii zdarzeń
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
Ten kod wysyła partię zdarzeń przygotowanych w poprzednim kroku do Centrum zdarzeń. Następujące bloki kodu w operacji wysyłania. 

```java
producer.send(batch);
```

### <a name="close-and-cleanup"></a>Zamykanie i oczyszczanie
Ten kod zamyka producenta. 

```java
producer.close();
```
### <a name="complete-code-to-send-events"></a>Kompletny kod do wysyłania zdarzeń
Oto kompletny kod do wysyłania zdarzeń do centrum zdarzeń. 

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

Skompiluj program i upewnij się, że nie ma żadnych błędów. Program zostanie uruchomiony po uruchomieniu programu odbiornika. 

## <a name="receive-events"></a>Odbieranie zdarzeń
Kod w tym samouczku jest oparty na [próbce EventProcessorClient na GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs/EventProcessorClientSample.java), które można sprawdzić, aby zobaczyć pełną działającą aplikację.

> [!NOTE]
> Jeśli korzystasz z usługi Azure Stack Hub, ta platforma może obsługiwać inną wersję SDK obiektów blob magazynu niż te zwykle dostępne na platformie Azure. Na przykład jeśli korzystasz [z usługi Azure Stack Hub w wersji 2002](https://docs.microsoft.com/azure-stack/user/event-hubs-overview), najwyższą dostępną wersją usługi Storage jest wersja 2017-11-09. W takim przypadku oprócz następujących kroków w tej sekcji, należy również dodać kod do docelowej wersji interfejsu API usługi magazynu 2017-11-09. Na przykład, jak kierować określonej wersji interfejsu API magazynu, zobacz [ten przykład na GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java). Aby uzyskać więcej informacji na temat wersji usługi Azure Storage obsługiwanych w usłudze Azure Stack Hub, zobacz [usługi Azure Stack Hub storage: Różnice i zagadnienia.](https://docs.microsoft.com/azure-stack/user/azure-stack-acs-differences)

### <a name="create-a-java-project"></a>Tworzenie projektu Java

Biblioteka klienta Java dla centrów zdarzeń jest dostępna do użycia w projektach Maven z [repozytorium Centralnego Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22)i można się do niej odwoływać przy użyciu następującej deklaracji zależności wewnątrz pliku projektu Maven: 

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs</artifactId>
        <version>5.0.1</version>
    </dependency>
</dependencies>
```

1. Użyj następującego kodu, aby utworzyć nową klasę o nazwie `Receiver`. Zastąp symbole zastępcze wartościami używanymi podczas tworzenia centrum zdarzeń i konta magazynu:
   
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
    
2. Pobierz plik **SampleCheckpointStore.java** z [gitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs/SampleCheckpointStore.java)i dodaj go do swojego projektu. 
3. Skompiluj program i upewnij się, że nie ma żadnych błędów. 

## <a name="run-the-applications"></a>Uruchamianie aplikacji
1. Najpierw uruchom aplikację **odbiornika.**
1. Następnie uruchom aplikację **nadawcy.** 
1. W oknie aplikacji **odbiorcy** upewnij się, że są widoczne zdarzenia, które zostały opublikowane przez aplikację nadawcy.
1. Naciśnij **klawisz ENTER** w oknie aplikacji odbiornika, aby zatrzymać aplikację. 

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z [przykładami java SDK w usłudze GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)

