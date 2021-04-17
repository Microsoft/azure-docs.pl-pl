---
title: Używanie Azure Service Bus i subskrypcji w języku Java (azure-messaging-servicebus)
description: W tym przewodniku Szybki start napiszesz kod Java przy użyciu pakietu azure-messaging-servicebus, aby wysyłać komunikaty do tematu Azure Service Bus, a następnie odbierać komunikaty z subskrypcji do tego tematu.
ms.date: 02/13/2021
ms.topic: quickstart
ms.devlang: Java
ms.custom:
- mode-api
ms.openlocfilehash: 6fe0a3a91ebbd5b6daced95494b8eaa5b7db0c46
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536375"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-java"></a>Wysyłanie komunikatów do Azure Service Bus tematu i odbieranie komunikatów z subskrypcji do tematu (Java)
W tym przewodniku Szybki start napiszesz kod Java przy użyciu pakietu azure-messaging-servicebus, aby wysyłać komunikaty do tematu Azure Service Bus, a następnie odbierać komunikaty z subskrypcji do tego tematu.

> [!IMPORTANT]
> W tym przewodniku Szybki start jest używany nowy pakiet azure-messaging-servicebus. Aby uzyskać przewodnik Szybki start korzystający ze starego pakietu azure-servicebus, zobacz Wysyłanie i odbieranie komunikatów [przy użyciu usługi azure-servicebus.](service-bus-java-how-to-use-topics-subscriptions-legacy.md)

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Możesz aktywować korzyści [dla subskrybentów Visual Studio MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) lub zarejestrować się w celu korzystania z [bezpłatnego konta.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Wykonaj kroki opisane w [przewodniku Szybki start: tworzenie](service-bus-quickstart-topics-subscriptions-portal.md)Azure Portal tematu i subskrypcji Service Bus tematu . Zanotuj ciąg połączenia, nazwę tematu i nazwę subskrypcji. W tym przewodniku Szybki start użyjesz tylko jednej subskrypcji. 
- Zainstaluj [zestaw Azure SDK dla języka Java.][Azure SDK for Java] Jeśli używasz środowiska Eclipse, możesz zainstalować pakiet Azure Toolkit for Eclipse [który][Azure Toolkit for Eclipse] zawiera zestaw Azure SDK dla języka Java. Następnie możesz dodać **biblioteki Microsoft Azure dla języka Java** do projektu. Jeśli używasz programu IntelliJ, zobacz Instalowanie programu [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/installation). 


## <a name="send-messages-to-a-topic"></a>Wysyłanie komunikatów do tematu
W tej sekcji utworzysz projekt konsoli Java i dodasz kod w celu wysyłania komunikatów do utworzonego tematu. 

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

### <a name="add-code-to-send-messages-to-the-topic"></a>Dodawanie kodu w celu wysyłania komunikatów do tematu
1. Dodaj następujące `import` instrukcje w temacie pliku Java. 

    ```java
    import com.azure.messaging.servicebus.*;
    
    import java.util.concurrent.CountDownLatch;
    import java.util.concurrent.TimeUnit;
    import java.util.Arrays;
    import java.util.List;
    ```    
5. W klasie zdefiniuj zmienne do przechowywania parametrów połączenia i nazwy tematu, jak pokazano poniżej: 

    ```java
    static String connectionString = "<NAMESPACE CONNECTION STRING>";
    static String topicName = "<TOPIC NAME>";    
    static String subName = "<SUBSCRIPTION NAME>";
    ```

    Zastąp `<NAMESPACE CONNECTION STRING>` ciągi połączenia z twoją Service Bus nazw. `<TOPIC NAME>`Zamień na nazwę tematu.
3. Dodaj metodę o nazwie `sendMessage` w klasie , aby wysłać jeden komunikat do tematu. 

    ```java
    static void sendMessage()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .topicName(topicName)
                .buildClient();
        
        // send one message to the topic
        senderClient.sendMessage(new ServiceBusMessage("Hello, World!"));
        System.out.println("Sent a single message to the topic: " + topicName);        
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
1. Dodaj metodę o nazwie `sendMessageBatch` , aby wysyłać komunikaty do utworzonego tematu. Ta metoda tworzy dla tematu, wywołuje metodę w celu uzyskania listy komunikatów, przygotowuje co najmniej jedną partię i wysyła partie `ServiceBusSenderClient` `createMessages` do tematu. 

    ```java
    static void sendMessageBatch()
    {
        // create a Service Bus Sender client for the topic 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .topicName(topicName)
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
            System.out.println("Sent a batch of messages to the topic: " + topicName);
            
            // create a new batch
            messageBatch = senderClient.createMessageBatch();

            // Add that message that we couldn't before.
            if (!messageBatch.tryAddMessage(message)) {
                System.err.printf("Message is too large for an empty batch. Skipping. Max size: %s.", messageBatch.getMaxSizeInBytes());
            }
        }

        if (messageBatch.getCount() > 0) {
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the topic: " + topicName);
        }

        //close the client
        senderClient.close();
    }
    ```

## <a name="receive-messages-from-a-subscription"></a>Odbieranie komunikatów z subskrypcji
W tej sekcji dodasz kod do pobierania komunikatów z subskrypcji do tematu. 

1. Dodaj metodę o nazwie `receiveMessages` w celu odbierania komunikatów z subskrypcji. Ta metoda tworzy dla subskrypcji, określając program obsługi do przetwarzania komunikatów, a drugi do `ServiceBusProcessorClient` obsługi błędów. Następnie uruchamia procesor, czeka kilka sekund, drukuje odebrane komunikaty, a następnie zatrzymuje i zamyka procesor.

    > [!IMPORTANT]
    > Zastąp `ServiceBusTopicTest` w kodzie nazwą swojej `ServiceBusTopicTest::processMessage` klasy. 

    ```java
    // handles received messages
    static void receiveMessages() throws InterruptedException
    {
        CountDownLatch countdownLatch = new CountDownLatch(1);

        // Create an instance of the processor through the ServiceBusClientBuilder
        ServiceBusProcessorClient processorClient = new ServiceBusClientBuilder()
            .connectionString(connectionString)
            .processor()
            .topicName(topicName)
            .subscriptionName(subName)
            .processMessage(ServiceBusTopicTest::processMessage)
            .processError(context -> processError(context, countdownLatch))
            .buildProcessorClient();

        System.out.println("Starting the processor");
        processorClient.start();

        TimeUnit.SECONDS.sleep(10);
        System.out.println("Stopping and closing the processor");
        processorClient.close();        
    }  
    ```
2. Dodaj metodę `processMessage` w celu przetwarzania komunikatu otrzymanego z Service Bus subskrypcji. 

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
1. Zaktualizuj metodę `main` , aby wywoływać metody , i i , aby `sendMessage` `sendMessageBatch` `receiveMessages` zgłaszały wyjątek `InterruptedException` .     

    ```java
    public static void main(String[] args) throws InterruptedException {        
        sendMessage();
        sendMessageBatch();
        receiveMessages();
    }   
    ```

## <a name="run-the-app"></a>Uruchamianie aplikacji
Uruchom program, aby wyświetlić dane wyjściowe podobne do następujących:

```console
Sent a single message to the topic: mytopic
Sent a batch of messages to the topic: mytopic
Starting the processor
Processing message. Session: e0102f5fbaf646988a2f4b65f7d32385, Sequence #: 1. Contents: Hello, World!
Processing message. Session: 3e991e232ca248f2bc332caa8034bed9, Sequence #: 2. Contents: First message
Processing message. Session: 56d3a9ea7df446f8a2944ee72cca4ea0, Sequence #: 3. Contents: Second message
Processing message. Session: 7bd3bd3e966a40ebbc9b29b082da14bb, Sequence #: 4. Contents: Third message
```

Na stronie **Przegląd** dla Service Bus nazw w Azure Portal można zobaczyć **liczbę** komunikatów przychodzących **i wychodzących.** Może być konieczne odczekenie około minuty, a następnie odświeżenie strony w celu zobaczenia najnowszych wartości. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Liczba komunikatów przychodzących i wychodzących" lightbox="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png":::

Przejdź do karty **Tematy** w środkowym dolnym okienku, a następnie wybierz temat, aby wyświetlić Service Bus **tematu.** Na tej stronie powinny być wyświetlane cztery komunikaty przychodzące i cztery wychodzące na **wykresie Komunikaty.** 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="Komunikaty przychodzące i wychodzące" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png":::

Jeśli przekłosz wywołanie metody w komentarz i ponownie uruchom aplikację, na stronie tematu usługi Service Bus zostanie wyświetlonych 8 komunikatów przychodzących (4 nowe), ale cztery komunikaty `receiveMessages` `main` wychodzące.  

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png" alt-text="Zaktualizowana strona tematu" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png":::

Na tej stronie, jeśli wybierzesz subskrypcję, zostanie Service Bus **Subskrypcja.** Na tej stronie można zobaczyć liczbę aktywnych komunikatów, liczbę utraconych komunikatów i inne. W tym przykładzie istnieją cztery aktywne komunikaty, które nie zostały jeszcze odebrane przez odbiornik. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png" alt-text="Liczba aktywnych komunikatów" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png":::

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującą dokumentacją i przykładami:

- [Azure Service Bus klienta dla języka Java — Readme](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/README.md)
- [Przykłady w witrynie GitHub](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Dokumentacja interfejsów API języka Java](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-messaging-servicebus/7.0.0/index.html)


[Azure SDK for Java]: /java/api/overview/azure/
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /dotnet/api/microsoft.azure.servicebus.sqlfilter
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.azure.servicebus.sqlfilter.sqlexpression
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
