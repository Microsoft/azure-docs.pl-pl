---
title: Używanie Azure Service Bus kolejek w języku Java (azure-messaging-servicebus)
description: Z tego samouczka dowiesz się, jak używać języka Java do wysyłania komunikatów do i odbierania komunikatów z Azure Service Bus kolejki. Użyj nowego pakietu azure-messaging-servicebus.
ms.date: 02/13/2021
ms.topic: quickstart
ms.devlang: Java
ms.custom:
- seo-java-july2019
- seo-java-august2019
- seo-java-september2019
- devx-track-java
- mode-api
ms.openlocfilehash: e3998e812bb921ff8bea5e83199bc4e142ed2e83
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533421"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-java"></a>Wysyłanie i odbieranie komunikatów z Azure Service Bus kolejek (Java)
W tym przewodniku Szybki start utworzysz aplikację w języku Java do wysyłania komunikatów do i odbierania komunikatów z Azure Service Bus kolejki. 

> [!IMPORTANT]
> W tym przewodniku Szybki start jest używany nowy pakiet azure-messaging-servicebus. Aby uzyskać przewodnik Szybki start korzystający ze starego pakietu azure-servicebus, zobacz Wysyłanie i odbieranie komunikatów [przy użyciu usługi azure-servicebus.](service-bus-java-how-to-use-queues-legacy.md)


## <a name="prerequisites"></a>Wymagania wstępne
- Subskrypcja platformy Azure. Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Możesz aktywować korzyści [dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) lub utworzyć bezpłatne [konto.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Jeśli nie masz kolejki do pracy, wykonaj kroki opisane w artykule Use [Azure Portal to create a Service Bus queue to](service-bus-quickstart-portal.md) create a queue (Tworzenie kolejki przy użyciu usługi Azure Portal do tworzenia kolejki). Zanotuj **ciąg** połączenia dla Service Bus nazw i nazwę **utworzonej** kolejki.
- Zainstaluj [zestaw Azure SDK dla języka Java.][Azure SDK for Java] Jeśli używasz środowiska Eclipse, możesz zainstalować pakiet Azure Toolkit for Eclipse [który][Azure Toolkit for Eclipse] zawiera zestaw Azure SDK dla języka Java. Następnie możesz dodać **biblioteki Microsoft Azure dla języka Java** do projektu. Jeśli używasz programu IntelliJ, zobacz Instalowanie programu [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/installation). 


## <a name="send-messages-to-a-queue"></a>Wysyłanie komunikatów do kolejki
W tej sekcji utworzysz projekt konsoli Java i dodasz kod w celu wysyłania komunikatów do utworzonej wcześniej kolejki. 

### <a name="create-a-java-console-project"></a>Tworzenie projektu konsoli Java
Utwórz projekt w języku Java przy użyciu środowiska Eclipse lub wybranego narzędzia. 

### <a name="configure-your-application-to-use-service-bus"></a>Konfigurowanie aplikacji do używania Service Bus
Dodawanie odwołań do platformy Azure Core Azure Service Bus bibliotek. 

Jeśli używasz środowiska Eclipse i utworzono aplikację konsolową Java, przekonwertuj projekt Java na projekt Maven: kliknij prawym przyciskiem myszy projekt w oknie **Eksplorator pakietów,** wybierz pozycję Konfiguruj konwertowanie na projekt  ->  **Maven.** Następnie dodaj zależności do tych dwóch bibliotek, jak pokazano w poniższym przykładzie.

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>org.myorg.sbusquickstarts</groupId>
    <artifactId>sbustopicqs</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <build>
        <sourceDirectory>src</sourceDirectory>
        <plugins>
            <plugin>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
                <configuration>
                    <release>15</release>
                </configuration>
            </plugin>
        </plugins>
    </build>
    <dependencies>
        <dependency>
            <groupId>com.azure</groupId>
            <artifactId>azure-core</artifactId>
            <version>1.13.0</version>
        </dependency>
        <dependency>
            <groupId>com.azure</groupId>
            <artifactId>azure-messaging-servicebus</artifactId>
            <version>7.0.2</version>
        </dependency>
    </dependencies>
</project>
```

### <a name="add-code-to-send-messages-to-the-queue"></a>Dodawanie kodu w celu wysyłania komunikatów do kolejki
1. Dodaj następujące `import` instrukcje w temacie pliku Java. 

    ```java
    import com.azure.messaging.servicebus.*;
    
    import java.util.concurrent.CountDownLatch;
    import java.util.concurrent.TimeUnit;
    import java.util.Arrays;
    import java.util.List;
    ```    
5. W klasie zdefiniuj zmienne do przechowywania parametrów połączenia i nazwy kolejki, jak pokazano poniżej: 

    ```java
    static String connectionString = "<NAMESPACE CONNECTION STRING>";
    static String queueName = "<QUEUE NAME>";    
    ```

    Zastąp `<NAMESPACE CONNECTION STRING>` ciąg ciągami połączenia z Service Bus nazw. Zamień `<QUEUE NAME>` na nazwę kolejki.
3. Dodaj metodę o nazwie `sendMessage` w klasie , aby wysłać jeden komunikat do kolejki. 

    ```java
    static void sendMessage()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .queueName(queueName)
                .buildClient();
        
        // send one message to the queue
        senderClient.sendMessage(new ServiceBusMessage("Hello, World!"));
        System.out.println("Sent a single message to the queue: " + queueName);        
    }
    ```
1. Dodaj metodę o `createMessages` nazwie w klasie , aby utworzyć listę komunikatów. Zazwyczaj te komunikaty są odbierane z różnych części aplikacji. W tym miejscu utworzymy listę przykładowych komunikatów.

    ```java
    static List<ServiceBusMessage> createMessages()
    {
        // create a list of messages and return it to the caller
        ServiceBusMessage[] messages = {
                new ServiceBusMessage("First message"),
                new ServiceBusMessage("Second message"),
                new ServiceBusMessage("Third message")
        };
        return Arrays.asList(messages);
    }
    ```
1. Dodaj metodę o nazwie `sendMessageBatch` , aby wysyłać komunikaty do utworzonej kolejki. Ta metoda tworzy dla kolejki, wywołuje metodę w celu uzyskania listy komunikatów, przygotowuje co najmniej jedną partię i wysyła partie `ServiceBusSenderClient` `createMessages` do kolejki. 

    ```java
    static void sendMessageBatch()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .queueName(queueName)
                .buildClient();

        // Creates an ServiceBusMessageBatch where the ServiceBus.
        ServiceBusMessageBatch messageBatch = senderClient.createMessageBatch();        
        
        // create a list of messages
        List<ServiceBusMessage> listOfMessages = createMessages();
        
        // We try to add as many messages as a batch can fit based on the maximum size and send to Service Bus when
        // the batch can hold no more messages. Create a new batch for next set of messages and repeat until all
        // messages are sent.        
        for (ServiceBusMessage message : listOfMessages) {
            if (messageBatch.tryAddMessage(message)) {
                continue;
            }

            // The batch is full, so we create a new batch and send the batch.
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the queue: " + queueName);
            
            // create a new batch
            messageBatch = senderClient.createMessageBatch();

            // Add that message that we couldn't before.
            if (!messageBatch.tryAddMessage(message)) {
                System.err.printf("Message is too large for an empty batch. Skipping. Max size: %s.", messageBatch.getMaxSizeInBytes());
            }
        }
        
        if (messageBatch.getCount() > 0) {
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the queue: " + queueName);
        }

        //close the client
        senderClient.close();
    }
    ```

## <a name="receive-messages-from-a-queue"></a>Odbieranie komunikatów z kolejki
W tej sekcji dodasz kod do pobierania komunikatów z kolejki. 

1. Dodaj metodę o nazwie `receiveMessages` , aby odbierać komunikaty z kolejki. Ta metoda tworzy dla kolejki, określając program obsługi do przetwarzania komunikatów, a drugi do `ServiceBusProcessorClient` obsługi błędów. Następnie uruchamia procesor, czeka kilka sekund, drukuje odebrane komunikaty, a następnie zatrzymuje i zamyka procesor.

    > [!IMPORTANT]
    > Zastąp `QueueTest` w kodzie nazwą swojej `QueueTest::processMessage` klasy. 

    ```java
    // handles received messages
    static void receiveMessages() throws InterruptedException
    {
        CountDownLatch countdownLatch = new CountDownLatch(1);

        // Create an instance of the processor through the ServiceBusClientBuilder
        ServiceBusProcessorClient processorClient = new ServiceBusClientBuilder()
            .connectionString(connectionString)
            .processor()
            .queueName(queueName)
            .processMessage(QueueTest::processMessage)
            .processError(context -> processError(context, countdownLatch))
            .buildProcessorClient();

        System.out.println("Starting the processor");
        processorClient.start();

        TimeUnit.SECONDS.sleep(10);
        System.out.println("Stopping and closing the processor");
        processorClient.close();        
    }   
    ```
2. Dodaj metodę `processMessage` , aby przetworzyć komunikat otrzymany z subskrypcji Service Bus subskrypcji. 

    ```java
    private static void processMessage(ServiceBusReceivedMessageContext context) {
        ServiceBusReceivedMessage message = context.getMessage();
        System.out.printf("Processing message. Session: %s, Sequence #: %s. Contents: %s%n", message.getMessageId(),
            message.getSequenceNumber(), message.getBody());
    }    
    ```
3. Dodaj metodę `processError` do obsługi komunikatów o błędach.

    ```java
    private static void processError(ServiceBusErrorContext context, CountDownLatch countdownLatch) {
        System.out.printf("Error when receiving messages from namespace: '%s'. Entity: '%s'%n",
            context.getFullyQualifiedNamespace(), context.getEntityPath());

        if (!(context.getException() instanceof ServiceBusException)) {
            System.out.printf("Non-ServiceBusException occurred: %s%n", context.getException());
            return;
        }

        ServiceBusException exception = (ServiceBusException) context.getException();
        ServiceBusFailureReason reason = exception.getReason();

        if (reason == ServiceBusFailureReason.MESSAGING_ENTITY_DISABLED
            || reason == ServiceBusFailureReason.MESSAGING_ENTITY_NOT_FOUND
            || reason == ServiceBusFailureReason.UNAUTHORIZED) {
            System.out.printf("An unrecoverable error occurred. Stopping processing with reason %s: %s%n",
                reason, exception.getMessage());

            countdownLatch.countDown();
        } else if (reason == ServiceBusFailureReason.MESSAGE_LOCK_LOST) {
            System.out.printf("Message lock lost for message: %s%n", context.getException());
        } else if (reason == ServiceBusFailureReason.SERVICE_BUSY) {
            try {
                // Choosing an arbitrary amount of time to wait until trying again.
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                System.err.println("Unable to sleep for period of time");
            }
        } else {
            System.out.printf("Error source %s, reason %s, message: %s%n", context.getErrorSource(),
                reason, context.getException());
        }
    }  
    ```
2. Zaktualizuj metodę `main` , aby wywoływać metody , i i , aby `sendMessage` `sendMessageBatch` `receiveMessages` zgłaszały wyjątek `InterruptedException` .     

    ```java
    public static void main(String[] args) throws InterruptedException {        
        sendMessage();
        sendMessageBatch();
        receiveMessages();
    }   
    ```

## <a name="run-the-app"></a>Uruchamianie aplikacji
Po uruchomieniu aplikacji w oknie konsoli zobaczysz następujące komunikaty. 

```console
Sent a single message to the queue: myqueue
Sent a batch of messages to the queue: myqueue
Starting the processor
Processing message. Session: 88d961dd801f449e9c3e0f8a5393a527, Sequence #: 1. Contents: Hello, World!
Processing message. Session: e90c8d9039ce403bbe1d0ec7038033a0, Sequence #: 2. Contents: First message
Processing message. Session: 311a216a560c47d184f9831984e6ac1d, Sequence #: 3. Contents: Second message
Processing message. Session: f9a871be07414baf9505f2c3d466c4ab, Sequence #: 4. Contents: Third message
Stopping and closing the processor
```

Na stronie **Przegląd** dla Service Bus nazw w Azure Portal można zobaczyć **liczbę** komunikatów przychodzących **i wychodzących.** Może być konieczne odczekenie około minuty, a następnie odświeżenie strony w celu zobaczenia najnowszych wartości. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Liczba komunikatów przychodzących i wychodzących" lightbox="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png":::

Wybierz kolejkę na tej **stronie Przegląd,** aby przejść do Service Bus **kolejki.** Na tej stronie **zobaczysz** również **liczbę** komunikatów przychodzących i wychodzących. Zobaczysz również inne informacje, takie jak **bieżący rozmiar** **kolejki,** maksymalny **rozmiar,** liczba aktywnych komunikatów i tak dalej. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/queue-details.png" alt-text="Szczegóły kolejki" lightbox="./media/service-bus-java-how-to-use-queues/queue-details.png":::



## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującą dokumentacją i przykładami:

- [Azure Service Bus klienta dla języka Java — Readme](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/README.md)
- [Przykłady w witrynie GitHub](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Dokumentacja interfejsów API języka Java](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-messaging-servicebus/7.0.0/index.html)

[Azure SDK for Java]: /azure/developer/java/sdk/get-started
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
