---
title: Korzystanie z Azure Service Bus tematów i subskrypcji w języku Java (Azure-Messaging-ServiceBus)
description: W tym przewodniku szybki start napiszesz kod Java za pomocą pakietu Azure-Messaging-ServiceBus, aby wysyłać komunikaty do tematu Azure Service Bus a następnie odbierać komunikaty z subskrypcji do tego tematu.
ms.devlang: Java
ms.topic: quickstart
ms.date: 11/09/2020
ms.openlocfilehash: 46dc6bed7e51a5157d7eb42dac75c0240d440780
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881622"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-java"></a>Wysyłanie komunikatów do tematu Azure Service Bus i odbieranie komunikatów z subskrypcji do tematu (Java)
W tym przewodniku szybki start napiszesz kod Java za pomocą pakietu Azure-Messaging-ServiceBus, aby wysyłać komunikaty do tematu Azure Service Bus a następnie odbierać komunikaty z subskrypcji do tego tematu.

> [!IMPORTANT]
> Ten przewodnik Szybki Start używa nowego pakietu Azure-Messaging-ServiceBus. Aby zapoznać się z przewodnikiem Szybki Start korzystającym z starego pakietu Azure-ServiceBus, zobacz [wysyłanie i odbieranie komunikatów przy użyciu platformy Azure-ServiceBus](service-bus-java-how-to-use-topics-subscriptions-legacy.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Możesz aktywować korzyści dla [subskrybentów programu Visual Studio lub MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Wykonaj kroki opisane w [przewodniku szybki start: użyj Azure Portal, aby utworzyć temat Service Bus i subskrypcje w temacie](service-bus-quickstart-topics-subscriptions-portal.md). Zanotuj parametry połączenia, nazwę tematu i nazwę subskrypcji. W tym przewodniku szybki start będziesz używać tylko jednej subskrypcji. 
- Zainstaluj [zestaw Azure SDK dla języka Java][Azure SDK for Java]. W przypadku korzystania z programu Zastąp, można zainstalować [Azure Toolkit for Eclipse][Azure Toolkit for Eclipse] zawierający zestaw Azure SDK dla języka Java. Następnie można dodać **biblioteki Microsoft Azure dla języka Java** do projektu. Jeśli używasz programu IntelliJ, zobacz [Install the Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/installation). 


## <a name="send-messages-to-a-topic"></a>Wysyłanie komunikatów do tematu
W tej sekcji utworzysz projekt konsoli Java, a następnie dodasz kod do wysyłania komunikatów do utworzonego tematu. 

### <a name="create-a-java-console-project"></a>Tworzenie projektu konsoli języka Java
Utwórz projekt Java przy użyciu narzędzia do zaszeregowania lub wybranego przez siebie narzędzi. 

### <a name="configure-your-application-to-use-service-bus"></a>Skonfiguruj aplikację do używania Service Bus
Dodaj odwołanie do biblioteki Azure Service Bus. Biblioteka klienta Java dla Service Bus jest dostępna w [repozytorium centralnym Maven](https://search.maven.org/search?q=a:azure-messaging-servicebus). Można odwołać się do tej biblioteki za pomocą następującej deklaracji zależności w pliku projektu Maven:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-servicebus</artifactId>
    <version>7.0.0</version>
</dependency>
```

### <a name="add-code-to-send-messages-to-the-topic"></a>Dodawanie kodu w celu wysyłania komunikatów do tematu
1. Dodaj następujące `import` instrukcje w temacie pliku Java. 

    ```java
    import com.azure.messaging.servicebus.*;
    import com.azure.messaging.servicebus.models.*;
    import java.util.concurrent.TimeUnit;
    import java.util.function.Consumer;
    import java.util.Arrays;
    import java.util.List;
    ```    
5. W klasie Zdefiniuj zmienne do przechowywania parametrów połączenia i nazwy tematu, jak pokazano poniżej: 

    ```java
    static String connectionString = "<NAMESPACE CONNECTION STRING>";
    static String topicName = "<TOPIC NAME>";    
    static String subName = "<SUBSCRIPTION NAME>";
    ```

    Zamień na `<NAMESPACE CONNECTION STRING>` Parametry połączenia z przestrzenią nazw Service Bus. I Zamień na `<TOPIC NAME>` nazwę tematu.
3. Dodaj metodę o nazwie `sendMessage` w klasie w celu wysłania jednej wiadomości do tematu. 

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
1. Dodaj metodę o nazwie `sendMessageBatch` Method, aby wysyłać komunikaty do utworzonego tematu. Ta metoda tworzy `ServiceBusSenderClient` dla tematu, wywołuje `createMessages` metodę w celu pobrania listy komunikatów, przygotowuje co najmniej jedną partię i wysyła partie do tematu. 

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
W tej sekcji dodasz kod umożliwiający pobranie komunikatów z subskrypcji do tematu. 

1. Dodaj metodę o nazwie `receiveMessages` do odbierania wiadomości z subskrypcji. Ta metoda tworzy `ServiceBusProcessorClient` dla subskrypcji przez określenie programu obsługi do przetwarzania komunikatów i innego do obsługi błędów. Następnie uruchamia procesor, czeka kilka sekund, drukuje odebrane komunikaty, a następnie kończy i zamyka procesor.

    ```java
    // handles received messages
    static void receiveMessages() throws InterruptedException
    {
        // Consumer that processes a single message received from Service Bus
        Consumer<ServiceBusReceivedMessageContext> messageProcessor = context -> {
            ServiceBusReceivedMessage message = context.getMessage();
            System.out.println("Received message: " + message.getBody().toString() + " from the subscription: " + subName);
        };

        // Consumer that handles any errors that occur when receiving messages
        Consumer<Throwable> errorHandler = throwable -> {
            System.out.println("Error when receiving messages: " + throwable.getMessage());
            if (throwable instanceof ServiceBusReceiverException) {
                ServiceBusReceiverException serviceBusReceiverException = (ServiceBusReceiverException) throwable;
                System.out.println("Error source: " + serviceBusReceiverException.getErrorSource());
            }
        };

        // Create an instance of the processor through the ServiceBusClientBuilder
        ServiceBusProcessorClient processorClient = new ServiceBusClientBuilder()
            .connectionString(connectionString)
            .processor()
            .topicName(topicName)
            .subscriptionName(subName)
            .processMessage(messageProcessor)
            .processError(errorHandler)
            .buildProcessorClient();

        System.out.println("Starting the processor");
        processorClient.start();

        TimeUnit.SECONDS.sleep(10);
        System.out.println("Stopping and closing the processor");
        processorClient.close();        
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
Uruchom program, aby zobaczyć dane wyjściowe podobne do następujących:

```console
Sent a batch of messages to the topic: mytopic
Starting the processor
Received message: First message from the subscription: mysub
Received message: Second message from the subscription: mysub
Received message: Third message from the subscription: mysub
Stopping and closing the processor
```

Na stronie **Omówienie** przestrzeni nazw Service Bus w Azure Portal można zobaczyć liczbę wiadomości **przychodzących** i **wychodzących** . Może być konieczne poczekanie na minutę lub, a następnie odświeżenie strony, aby zobaczyć najnowsze wartości. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Liczba wiadomości przychodzących i wychodzących" lightbox="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png":::

Przejdź do karty **Tematy** w środkowym dolnym okienku i wybierz temat, aby wyświetlić stronę **tematu Service Bus** tematu. Na tej stronie powinny być widoczne cztery przychodzące i cztery komunikaty wychodzące na wykresie **komunikatów** . 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="Komunikaty przychodzące i wychodzące" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png":::

Jeśli komentarz jest `receiveMessages` wywoływany w `main` metodzie i ponownie uruchamiasz aplikację, na stronie **tematu Service Bus** zobaczysz 8 komunikatów przychodzących (4 nowe), ale cztery komunikaty wychodzące. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png" alt-text="Zaktualizowana Strona tematu" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png":::

Na tej stronie, jeśli wybierzesz subskrypcję, uzyskasz dostęp do strony **subskrypcji Service Bus** . Na tej stronie można zobaczyć liczbę aktywnych komunikatów, liczbę wiadomości utraconych i więcej. W tym przykładzie istnieją cztery aktywne komunikaty, które nie zostały jeszcze odebrane przez odbiornik. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png" alt-text="Liczba aktywnych komunikatów" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png":::

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z poniższą dokumentacją i przykładami:

- [Azure Service Busa Biblioteka kliencka dla języka Java — plik Readme](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/README.md)
- [Przykłady w serwisie GitHub](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Dokumentacja interfejsów API języka Java](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-messaging-servicebus/7.0.0/index.html)


[Azure SDK for Java]: /java/api/overview/azure/
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /dotnet/api/microsoft.azure.servicebus.sqlfilter
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.azure.servicebus.sqlfilter.sqlexpression
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage