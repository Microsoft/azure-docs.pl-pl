---
title: Używanie interfejsu API usługi wiadomości języka Java 2,0 z usługą Azure Service Bus Premium
description: Jak używać usługi wiadomości języka Java (JMS) z Azure Service Bus
ms.topic: article
ms.date: 07/17/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: b7e4bf0ad69b6cd183296a7245ad3f720ced76c5
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652614"
---
# <a name="use-java-message-service-20-api-with-azure-service-bus-premium"></a>Używanie interfejsu API usługi wiadomości języka Java 2,0 z usługą Azure Service Bus Premium

W tym artykule wyjaśniono, jak używać popularnego interfejsu API **2,0 usługi komunikatów języka Java (JMS)** w celu współdziałania z Azure Service Bus za pośrednictwem protokołu Advanced Message Queue Protocol (AMQP 1,0).

> [!NOTE]
> Obsługa interfejsu API usługi wiadomości języka Java (JMS) 2,0 jest dostępna tylko w **warstwie Premium Azure Service Bus**.
>

## <a name="pre-requisites"></a>Wymagania wstępne

### <a name="get-started-with-service-bus"></a>Rozpoczynanie pracy z usługą Service Bus

W tym przewodniku przyjęto założenie, że masz już Service Bus przestrzeń nazw. Jeśli tego nie zrobisz, możesz [utworzyć przestrzeń nazw i kolejkę](service-bus-create-namespace-portal.md) za pomocą [Azure Portal](https://portal.azure.com). 

Aby uzyskać więcej informacji na temat sposobu tworzenia Service Bus obszarów nazw i kolejek, zobacz [Rozpoczynanie pracy z kolejkami Service Bus za pomocą Azure Portal](service-bus-quickstart-portal.md).

### <a name="set-up-a-java-development-environment"></a>Konfigurowanie środowiska deweloperskiego Java

Aby tworzyć aplikacje Java, należy skonfigurować odpowiednie środowisko programistyczne — 
   * JDK (Java Development Kit) lub JRE (Java Runtime Environment) jest zainstalowany.
   * JDK lub JRE jest dodawany do ścieżki kompilacji i odpowiednich zmiennych systemowych.
   * Środowisko IDE języka Java jest zainstalowane do korzystania z JDK lub JRE. Na przykład przezaćmienie lub IntelliJ.

Aby dowiedzieć się więcej na temat przygotowywania środowiska deweloperskiego dla języka Java na platformie Azure, Skorzystaj z [tego przewodnika](https://docs.microsoft.com/azure/developer/java/fundamentals/).

## <a name="what-jms-features-are-supported"></a>Jakie funkcje JMS są obsługiwane?

[!INCLUDE [service-bus-jms-features-list](../../includes/service-bus-jms-feature-list.md)]

## <a name="downloading-the-java-message-service-jms-client-library"></a>Pobieranie biblioteki klienta usługi wiadomości Java (JMS)

Aby korzystać ze wszystkich funkcji dostępnych w Azure Service Bus warstwie Premium, należy dodać poniższą bibliotekę do ścieżki kompilacji projektu.

[Azure-ServiceBus-JMS](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)

> [!NOTE]
> Aby dodać [platformę Azure-ServiceBus-JMS](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) do ścieżki kompilacji, użyj preferowanego narzędzia do zarządzania zależnościami dla projektu, takiego jak [Maven](https://maven.apache.org/) lub [Gradle](https://gradle.org/).
>

## <a name="coding-java-applications"></a>Kodowanie aplikacji języka Java

Po zaimportowaniu zależności aplikacje Java mogą być zapisywane w JMS dostawcy niezależny od sposób.

### <a name="connecting-to-azure-service-bus-using-jms"></a>Nawiązywanie połączenia z Azure Service Bus przy użyciu JMS

Aby nawiązać połączenie z Azure Service Bus przy użyciu klientów JMS, potrzebne są **Parametry połączenia** , które są dostępne w zasadach dostępu współdzielonego w [Azure Portal](https://portal.azure.com) w ramach **podstawowych parametrów połączenia**.

1. Tworzenie wystąpienia `ServiceBusJmsConnectionFactorySettings`

    ```java
    ServiceBusJmsConnectionFactorySettings connFactorySettings = new ServiceBusJmsConnectionFactorySettings();
    connFactorySettings.setConnectionIdleTimeoutMS(20000);
    ```
2. Utwórz wystąpienie `ServiceBusJmsConnectionFactory` z odpowiednią `ServiceBusConnectionString` .

    ```java
    String ServiceBusConnectionString = "<SERVICE_BUS_CONNECTION_STRING_WITH_MANAGE_PERMISSIONS>";
    ConnectionFactory factory = new ServiceBusJmsConnectionFactory(ServiceBusConnectionString, connFactorySettings);
    ```

3. Użyj, `ConnectionFactory` Aby utworzyć `Connection` a i `Session` 

    ```java
    Connection connection = factory.createConnection();
    Session session = connection.createSession();
    ```
    lub a `JMSContext` (dla klientów z systemem JMS 2,0)

    ```java
    JMSContext jmsContext = factory.createContext();
    ```

    >[!IMPORTANT]
    > Chociaż podobnie nazywa się JMS "sesja" i Service Bus "sesja" jest całkowicie niezależna od siebie.
    >
    > W JMS 1,1, sesja jest istotnym blokiem konstrukcyjnym interfejsu API, który umożliwia tworzenie MessageProducer, MessageConsumer i samego komunikatu. Aby uzyskać więcej informacji, zapoznaj się z [modelem programowania interfejsu API JMS](https://docs.oracle.com/javaee/6/tutorial/doc/bnceh.html)
    >
    > W Service Bus [sesjami](message-sessions.md) są usługa i konstrukcja po stronie klienta, która umożliwia przetwarzanie FIFO w kolejkach i subskrypcjach.
    >

### <a name="write-the-jms-application"></a>Napisz aplikację JMS

Po utworzeniu `Session` `JMSContext` wystąpienia lub, aplikacja może używać znanych interfejsów API JMS do wykonywania operacji zarządzania i danych.

Zapoznaj się z listą [obsługiwanych funkcji JMS](how-to-use-java-message-service-20.md#what-jms-features-are-supported) , aby sprawdzić, które interfejsy API są obsługiwane.

Poniżej przedstawiono kilka przykładowych fragmentów kodu, aby rozpocząć pracę z JMS-

#### <a name="sending-messages-to-a-queue-and-topic"></a>Wysyłanie komunikatów do kolejki i tematu

```java
// Create the queue and topic
Queue queue = jmsContext.createQueue("basicQueue");
Topic topic = jmsContext.createTopic("basicTopic");
// Create the message
Message msg = jmsContext.createMessage();

// Create the JMS message producer
JMSProducer producer = jmsContext.createProducer();

// send the message to the queue
producer.send(queue, msg);
// send the message to the topic
producer.send(topic, msg);
```

#### <a name="receiving-messages-from-a-queue"></a>Otrzymywanie komunikatów z kolejki

```java
// Create the queue
Queue queue = jmsContext.createQueue("basicQueue");

// Create the message consumer
JMSConsumer consumer = jmsContext.createConsumer(queue);

// Receive the message
Message msg = (Message) consumer.receive();
```

#### <a name="receiving-messages-from-a-shared-durable-subscription-on-a-topic"></a>Otrzymywanie komunikatów z udostępnionej subskrypcji trwałej w temacie

```java
// Create the topic
Topic topic = jmsContext.createTopic("basicTopic");

// Create a shared durable subscriber on the topic
JMSConsumer sharedDurableConsumer = jmsContext.createSharedDurableConsumer(topic, "sharedDurableConsumer");

// Receive the message
Message msg = (Message) sharedDurableConsumer.receive();
```

## <a name="summary"></a>Podsumowanie

W tym przewodniku przedstawiono sposób, w jaki aplikacje klienckie Java korzystające z usługi wiadomości Java (JMS) over AMQP 1,0 mogą współdziałać z Azure Service Bus.

Możesz również użyć Service Bus AMQP 1,0 z innych języków, w tym .NET, C, Python i PHP. Składniki utworzone przy użyciu tych różnych języków mogą niezawodnie wymieniać komunikaty i z pełną dokładnością dzięki obsłudze AMQP 1,0 w Service Bus.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat Azure Service Bus i szczegółów dotyczących jednostek usługi wiadomości języka Java (JMS), zapoznaj się z poniższymi linkami — 
* [Service Bus — kolejki, tematy i subskrypcje](service-bus-queues-topics-subscriptions.md)
* [Service Bus — jednostki usługi komunikatów języka Java](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities)
* [Obsługa AMQP 1,0 w Azure Service Bus](service-bus-amqp-overview.md)
* [Podręcznik dewelopera Service Bus AMQP 1,0](service-bus-amqp-dotnet.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Interfejs API usługi wiadomości Java (dokument zewnętrzny Oracle)](https://docs.oracle.com/javaee/7/api/javax/jms/package-summary.html)
* [Dowiedz się, jak przeprowadzić migrację z ActiveMQ do Service Bus](migrate-jms-activemq-to-servicebus.md)
