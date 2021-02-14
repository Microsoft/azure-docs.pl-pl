---
title: Korzystanie z kolejek Azure Service Bus w języku Java (Azure-Messaging-ServiceBus)
description: W ramach tego samouczka nauczysz się używać języka Java do wysyłania komunikatów do i odbierania komunikatów z kolejki Azure Service Bus. Używasz nowego pakietu Azure-Messaging-ServiceBus.
ms.devlang: Java
ms.topic: quickstart
ms.date: 02/13/2021
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: bfe7835bea4415085279fb77eb85d67ed3f5f0f3
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518610"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-java"></a>Wysyłanie komunikatów do i odbieranie komunikatów z kolejek Azure Service Bus (Java)
W tym przewodniku szybki start utworzysz aplikację Java do wysyłania komunikatów do i odbierania komunikatów z kolejki Azure Service Bus. 

> [!IMPORTANT]
> Ten przewodnik Szybki Start używa nowego pakietu Azure-Messaging-ServiceBus. Aby zapoznać się z przewodnikiem Szybki Start korzystającym z starego pakietu Azure-ServiceBus, zobacz [wysyłanie i odbieranie komunikatów przy użyciu platformy Azure-ServiceBus](service-bus-java-how-to-use-queues-legacy.md).


## <a name="prerequisites"></a>Wymagania wstępne
- Subskrypcja platformy Azure. Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Możesz aktywować korzyści dla [subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Jeśli nie masz kolejki do współpracy z programem, postępuj zgodnie z instrukcjami w temacie [Use Azure Portal, aby utworzyć](service-bus-quickstart-portal.md) kolejkę Service Bus w celu utworzenia kolejki. Zanotuj **Parametry połączenia** dla przestrzeni nazw Service Bus i nazwę utworzonej **kolejki** .
- Zainstaluj [zestaw Azure SDK dla języka Java][Azure SDK for Java]. W przypadku korzystania z programu Zastąp, można zainstalować [Azure Toolkit for Eclipse][Azure Toolkit for Eclipse] zawierający zestaw Azure SDK dla języka Java. Następnie można dodać **biblioteki Microsoft Azure dla języka Java** do projektu. Jeśli używasz programu IntelliJ, zobacz [Install the Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/installation). 


## <a name="send-messages-to-a-queue"></a>Wysyłanie komunikatów do kolejki
W tej sekcji utworzysz projekt konsoli Java, a następnie dodasz kod do wysyłania wiadomości do kolejki utworzonej wcześniej. 

### <a name="create-a-java-console-project"></a>Tworzenie projektu konsoli języka Java
Utwórz projekt Java przy użyciu narzędzia do zaszeregowania lub wybranego przez siebie narzędzi. 

### <a name="configure-your-application-to-use-service-bus"></a>Skonfiguruj aplikację do używania Service Bus
Dodaj odwołania do bibliotek podstawowych i Azure Service Bus platformy Azure. 

Jeśli używasz funkcji Zastąp i utworzono aplikację konsolową Java, przekonwertuj swój projekt Java na Maven: kliknij prawym przyciskiem myszy projekt w oknie **Eksplorator pakietów** , a następnie wybierz polecenie **Konfiguruj**  ->  **konwersję do Maven Project**. Następnie Dodaj zależności do tych dwóch bibliotek, jak pokazano w poniższym przykładzie.

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
5. W klasie Zdefiniuj zmienne do przechowywania parametrów połączenia i nazwy kolejki, jak pokazano poniżej: 

    ```java
    static String connectionString = "<NAMESPACE CONNECTION STRING>";
    static String queueName = "<QUEUE NAME>";    
    ```

    Zamień na `<NAMESPACE CONNECTION STRING>` Parametry połączenia z przestrzenią nazw Service Bus. I Zamień na `<QUEUE NAME>` nazwę kolejki.
3. Dodaj metodę o nazwie `sendMessage` w klasie w celu wysłania jednej wiadomości do kolejki. 

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
1. Dodaj metodę o nazwie `createMessages` w klasie, aby utworzyć listę komunikatów. Zwykle te komunikaty są uzyskiwane z różnych części aplikacji. Tutaj tworzymy listę przykładowych wiadomości.

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
1. Dodaj metodę o nazwie `sendMessageBatch` Method, aby wysyłać komunikaty do utworzonej kolejki. Ta metoda tworzy `ServiceBusSenderClient` dla kolejki, wywołuje `createMessages` metodę w celu pobrania listy komunikatów, przygotowuje co najmniej jedną partię i wysyła partie do kolejki. 

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
W tej sekcji dodasz kod umożliwiający pobieranie komunikatów z kolejki. 

1. Dodaj metodę o nazwie `receiveMessages` , aby odbierać komunikaty z kolejki. Ta metoda tworzy `ServiceBusProcessorClient` dla kolejki przez określenie programu obsługi do przetwarzania komunikatów i drugiego dla obsługi błędów. Następnie uruchamia procesor, czeka kilka sekund, drukuje odebrane komunikaty, a następnie kończy i zamyka procesor.

    > [!IMPORTANT]
    > Zamień `QueueTest` w `QueueTest::processMessage` kodzie na nazwę klasy. 

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
2. Dodaj `processMessage` metodę, aby przetworzyć komunikat otrzymany z subskrypcji Service Bus. 

    ```java
    private static void processMessage(ServiceBusReceivedMessageContext context) {
        ServiceBusReceivedMessage message = context.getMessage();
        System.out.printf("Processing message. Session: %s, Sequence #: %s. Contents: %s%n", message.getMessageId(),
            message.getSequenceNumber(), message.getBody());
    }    
    ```
3. Dodaj `processError` metodę, aby obsłużyć komunikaty o błędach.

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
2. Zaktualizuj `main` metodę, aby wywołać `sendMessage` metody Invoke, `sendMessageBatch` , i, `receiveMessages` Aby zgłosić `InterruptedException` .     

    ```java
    public static void main(String[] args) throws InterruptedException {        
        sendMessage();
        sendMessageBatch();
        receiveMessages();
    }   
    ```

## <a name="run-the-app"></a>Uruchamianie aplikacji
Po uruchomieniu aplikacji w oknie konsoli są wyświetlane następujące komunikaty. 

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

Na stronie **Omówienie** przestrzeni nazw Service Bus w Azure Portal można zobaczyć liczbę wiadomości **przychodzących** i **wychodzących** . Może być konieczne poczekanie na minutę lub, a następnie odświeżenie strony, aby zobaczyć najnowsze wartości. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Liczba wiadomości przychodzących i wychodzących" lightbox="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png":::

Wybierz kolejkę na tej stronie **przeglądu** , aby przejść do strony **kolejki Service Bus** . Na tej stronie jest również wyświetlana liczba komunikatów **przychodzących** i **wychodzących** . Widoczne są również inne informacje, takie jak **bieżący rozmiar** kolejki, **Maksymalny rozmiar**, **Liczba aktywnych komunikatów** itd. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/queue-details.png" alt-text="Szczegóły kolejki" lightbox="./media/service-bus-java-how-to-use-queues/queue-details.png":::



## <a name="next-steps"></a>Następne kroki
Zapoznaj się z poniższą dokumentacją i przykładami:

- [Azure Service Busa Biblioteka kliencka dla języka Java — plik Readme](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/README.md)
- [Przykłady w witrynie GitHub](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Dokumentacja interfejsów API języka Java](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-messaging-servicebus/7.0.0/index.html)

[Azure SDK for Java]: /azure/developer/java/sdk/java-sdk-azure-get-started
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage