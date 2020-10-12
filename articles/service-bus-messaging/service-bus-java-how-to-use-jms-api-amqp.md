---
title: Użyj AMQP z interfejsem API usługi wiadomości Java i Azure Service Bus
description: Użyj usługi wiadomości Java (JMS) z Azure Service Bus i Advanced Message Queuing Protocol (AMQP) 1,0.
ms.topic: article
ms.date: 06/23/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 97031abaedaa3e5595e290fa0292646feb744d47
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90086695"
---
# <a name="use-the-java-message-service-with-azure-service-bus-and-amqp-10"></a>Korzystanie z usługi wiadomości Java z Azure Service Bus i AMQP 1,0

> [!WARNING]
> W tym artykule zawarto *ograniczoną obsługę* interfejsu API usługi wiadomości Java (JMS) 1,1 i istnieje tylko dla Azure Service Bus warstwy Standardowa.
>
> Pełna obsługa interfejsu API usługi wiadomości Java 2,0 jest dostępna tylko w [warstwie premium Azure Service Bus w wersji zapoznawczej](how-to-use-java-message-service-20.md). Zalecamy korzystanie z tej warstwy.
>

W tym artykule wyjaśniono, jak używać funkcji Service Bus Messaging z aplikacji Java przy użyciu popularnego standardu API JMS. Te funkcje obsługi wiadomości obejmują kolejki i publikowanie lub subskrybowanie tematów. W tym [artykule](service-bus-amqp-dotnet.md) opisano, jak wykonać te same czynności przy użyciu interfejsu API programu Azure Service Bus .NET. Te dwa artykuły można wykorzystać razem, aby dowiedzieć się więcej na temat obsługi komunikatów na wielu platformach przy użyciu Advanced Message Queuing Protocol (AMQP) 1,0.

AMQP 1,0 to wydajny, niezawodny protokół obsługi komunikatów na poziomie sieci, który umożliwia tworzenie niezawodnych aplikacji do obsługi komunikatów dla wielu platform.

Obsługa AMQP 1,0 w Service Bus oznacza, że można korzystać z funkcji kolejkowania i publikowania lub subskrybować funkcje komunikatów obsługiwanych przez brokera z różnych platform przy użyciu wydajnego protokołu binarnego. Można również tworzyć aplikacje składające się ze składników skompilowanych przy użyciu różnych języków, platform i systemów operacyjnych.

## <a name="get-started-with-service-bus"></a>Rozpoczynanie pracy z usługą Service Bus

W tym artykule przyjęto założenie, że masz już Service Bus przestrzeń nazw, która zawiera kolejkę o nazwie `basicqueue` . Jeśli tego nie zrobisz, możesz [utworzyć przestrzeń nazw i kolejkę](service-bus-create-namespace-portal.md) za pomocą [Azure Portal](https://portal.azure.com). Aby uzyskać więcej informacji na temat sposobu tworzenia Service Bus obszarów nazw i kolejek, zobacz [Rozpoczynanie pracy z kolejkami Service Bus](service-bus-dotnet-get-started-with-queues.md).

> [!NOTE]
> Partycjonowane kolejki i tematy obsługują również AMQP. Aby uzyskać więcej informacji, zobacz [partycjonowane jednostki obsługi komunikatów](service-bus-partitioning.md) i [obsługę AMQP 1,0 Service Bus dla kolejek i tematów z podziałem na partycje](./service-bus-amqp-protocol-guide.md).
> 
> 

## <a name="download-the-amqp-10-jms-client-library"></a>Pobierz bibliotekę kliencką AMQP 1,0 JMS

Informacje o tym, gdzie pobrać najnowszą wersję biblioteki klienta Apache Qpid JMS AMQP 1,0, można znaleźć w [witrynie pobierania Qpid Apache](https://qpid.apache.org/download.html).

Podczas kompilowania i uruchamiania aplikacji JMS z Service Bus należy dodać następujące pliki JAR z archiwum dystrybucji dystrybucyjnej platformy Apache Qpid JMS AMQP 1,0 do zmiennej środowiskowej klasy Java.

* Geronimo-JMS \_ 1,1 \_ spec-1.0. jar
* qpid-JMS-Client-[wersja]. jar

> [!NOTE]
> JMS nazwy i wersje JAR mogą ulec zmianie. Aby uzyskać więcej informacji, zobacz [QPID JMS AMQP 1,0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10).

## <a name="code-java-applications"></a>Kod aplikacji Java

### <a name="java-naming-and-directory-interface"></a>Nazewnictwo Java i interfejs katalogu

JMS używa języka Java Name and Directory Interface (JNDI) w celu utworzenia rozdzielenia między nazwami logicznymi i nazwami fizycznymi. Dwa typy obiektów JMS są rozwiązywane za pomocą JNDI: **ConnectionFactory** i **Destination**. JNDI korzysta z modelu dostawcy, w którym można podłączyć różne usługi katalogowe do obsługi obowiązków rozpoznawania nazw. Biblioteka Apache Qpid JMS AMQP 1,0 zawiera prosty, oparty na plikach właściwości dostawca JNDI, który jest konfigurowany przy użyciu pliku właściwości w następującym formacie:

```TEXT
# servicebus.properties - sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### <a name="set-up-jndi-context-and-configure-the-connectionfactory-object"></a>Konfigurowanie kontekstu JNDI i Konfigurowanie obiektu ConnectionFactory

Odwołujące się do tego parametry połączenia są dostępne w zasadach dostępu współdzielonego w [Azure Portal](https://portal.azure.com) w obszarze **podstawowych parametrów połączenia**.

```java
// The connection string builder is the only part of the azure-servicebus SDK library
// we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
// connection string. 
ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
// Set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");

// Look up queue
Destination queue = (Destination) context.lookup("QUEUE");
```

#### <a name="configure-producer-and-consumer-destination-queues"></a>Konfigurowanie kolejek docelowych producenta i odbiorcy

Wpis używany do definiowania miejsca docelowego w pliku właściwości Qpid dostawcy JNDI ma następujący format.

Aby utworzyć kolejkę docelową dla producenta:
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create producer
MessageProducer producer = session.createProducer(queue);
```

Aby utworzyć kolejkę docelową dla konsumenta:
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create consumer
MessageConsumer consumer = session.createConsumer(queue);
```

### <a name="write-the-jms-application"></a>Napisz aplikację JMS

W przypadku używania JMS z Service Busami nie są wymagane żadne specjalne interfejsy API ani opcje. Istnieje kilka ograniczeń, które zostaną omówione później. Podobnie jak w przypadku dowolnej aplikacji JMS, najpierw wymagane jest konfiguracja środowiska JNDI, aby można było rozpoznać obiekt **ConnectionFactory** i miejsca docelowe.

#### <a name="configure-the-jndi-initialcontext-object"></a>Konfigurowanie obiektu JNDI InitialContext

Środowisko JNDI jest konfigurowane przez przekazanie tabeli skrótów informacji o konfiguracji do konstruktora klasy javax.naming.InitialContext. Dwa wymagane elementy w tabeli skrótów to nazwa klasy początkowej fabryki kontekstu i adres URL dostawcy. Poniższy kod przedstawia sposób konfigurowania środowiska JNDI do używania dostawcy JNDI opartego na plikach właściwości Qpid z plikiem właściwości o nazwie **ServiceBus. Properties**.

```java
// Set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + \
"?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);
``` 

### <a name="a-simple-jms-application-that-uses-a-service-bus-queue"></a>Prosta aplikacja JMS, która korzysta z kolejki Service Bus

Poniższy przykładowy program wysyła wiadomości SMS JMS do kolejki Service Bus z logiczną nazwą JNDI kolejki i odbiera je z powrotem.

Możesz uzyskać dostęp do całego kodu źródłowego i informacji o konfiguracji z [Azure Service Bus Samples JMS Queue szybki start](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client/JmsQueueQuickstart).

```java
// Copyright (c) Microsoft. All rights reserved.
// Licensed under the MIT license. See LICENSE file in the project root for full license information.

package com.microsoft.azure.servicebus.samples.jmsqueuequickstart;

import com.microsoft.azure.servicebus.primitives.ConnectionStringBuilder;
import org.apache.commons.cli.*;
import org.apache.log4j.*;

import javax.jms.*;
import javax.naming.Context;
import javax.naming.InitialContext;
import java.util.Hashtable;
import java.util.concurrent.atomic.AtomicInteger;
import java.util.function.Function;

/**
 * This sample demonstrates how to send messages from a JMS queue producer into
 * an Azure Service Bus queue and receive them with a JMS message consumer.
 * JMS queue. 
 */
public class JmsQueueQuickstart {

    // Number of messages to send
    private static int totalSend = 10;
    //Tracking counter for how many messages have been received; used as termination condition
    private static AtomicInteger totalReceived = new AtomicInteger(0);
    // log4j logger 
    private static Logger logger = Logger.getRootLogger();

    public void run(String connectionString) throws Exception {

        // The connection string builder is the only part of the azure-servicebus SDK library
        // we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
        // connection string. 
        ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
        // Set up JNDI context
        Hashtable<String, String> hashtable = new Hashtable<>();
        hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
        hashtable.put("queue.QUEUE", "BasicQueue");
        hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
        Context context = new InitialContext(hashtable);
        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
        
        // Look up queue
        Destination queue = (Destination) context.lookup("QUEUE");

        // We create a scope here so we can use the same set of local variables cleanly 
        // again to show the receive side separately with minimal clutter.
        {
            // Create connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            // Create session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

            // Create producer
            MessageProducer producer = session.createProducer(queue);

            // Send messages
            for (int i = 0; i < totalSend; i++) {
                BytesMessage message = session.createBytesMessage();
                message.writeBytes(String.valueOf(i).getBytes());
                producer.send(message);
                System.out.printf("Sent message %d.\n", i + 1);
            }

            producer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        {
            // Create connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            connection.start();
            // Create session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
            // Create consumer
            MessageConsumer consumer = session.createConsumer(queue);
            // Create a listener callback to receive the messages
            consumer.setMessageListener(message -> {
                try {
                    // Received message is passed to callback
                    System.out.printf("Received message %d with sq#: %s\n",
                            totalReceived.incrementAndGet(), // increments the tracking counter
                            message.getJMSMessageID());
                    message.acknowledge();
                } catch (Exception e) {
                    logger.error(e);
                }
            });

            // Wait on the main thread until all sent messages have been received
            while (totalReceived.get() < totalSend) {
                Thread.sleep(1000);
            }
            consumer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        System.out.printf("Received all messages, exiting the sample.\n");
        System.out.printf("Closing queue client.\n");
    }

    public static void main(String[] args) {

        System.exit(runApp(args, (connectionString) -> {
            JmsQueueQuickstart app = new JmsQueueQuickstart();
            try {
                app.run(connectionString);
                return 0;
            } catch (Exception e) {
                System.out.printf("%s", e.toString());
                return 1;
            }
        }));
    }

    static final String SB_SAMPLES_CONNECTIONSTRING = "SB_SAMPLES_CONNECTIONSTRING";

    public static int runApp(String[] args, Function<String, Integer> run) {
        try {

            String connectionString = null;

            // Parse connection string from command line
            Options options = new Options();
            options.addOption(new Option("c", true, "Connection string"));
            CommandLineParser clp = new DefaultParser();
            CommandLine cl = clp.parse(options, args);
            if (cl.getOptionValue("c") != null) {
                connectionString = cl.getOptionValue("c");
            }

            // Get overrides from the environment
            String env = System.getenv(SB_SAMPLES_CONNECTIONSTRING);
            if (env != null) {
                connectionString = env;
            }

            if (connectionString == null) {
                HelpFormatter formatter = new HelpFormatter();
                formatter.printHelp("run jar with", "", options, "", true);
                return 2;
            }
            return run.apply(connectionString);
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
            return 3;
        }
    }
}
```

### <a name="run-the-application"></a>Uruchamianie aplikacji

Przekaż **Parametry połączenia** z zasad dostępu współdzielonego, aby uruchomić aplikację.
Następujące dane wyjściowe są w formie działającej w aplikacji:

```Output
> mvn clean package
>java -jar ./target/jmsqueuequickstart-1.0.0-jar-with-dependencies.jar -c "<CONNECTION_STRING>"

Sent message 1.
Sent message 2.
Sent message 3.
Sent message 4.
Sent message 5.
Sent message 6.
Sent message 7.
Sent message 8.
Sent message 9.
Sent message 10.
Received message 1 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-1
Received message 2 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-2
Received message 3 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-3
Received message 4 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-4
Received message 5 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-5
Received message 6 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-6
Received message 7 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-7
Received message 8 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-8
Received message 9 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-9
Received message 10 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-10
Received all messages, exiting the sample.
Closing queue client.

```

## <a name="amqp-disposition-and-service-bus-operation-mapping"></a>AMQP i Service Bus mapowanie operacji

Oto, jak Dyspozycja AMQP jest tłumaczona na operację Service Bus:

```Output
ACCEPTED = 1; -> Complete()
REJECTED = 2; -> DeadLetter()
RELEASED = 3; (just unlock the message in service bus, will then get redelivered)
MODIFIED_FAILED = 4; -> Abandon() which increases delivery count
MODIFIED_FAILED_UNDELIVERABLE = 5; -> Defer()
```

## <a name="jms-topics-vs-service-bus-topics"></a>Tematy JMS a Service Bus — tematy

Korzystanie z Service Bus tematów i subskrypcji za pośrednictwem interfejsu API JMS zapewnia podstawowe możliwości wysyłania i odbierania. Jest to wygodny wybór podczas portów aplikacji z innych brokerów komunikatów za pomocą interfejsów API zgodnych z JMS, mimo że tematy Service Bus różnią się od tematów JMS i wymagają kilku korekt.

Tematy Service Bus kierować wiadomości do nazwanych, udostępnionych i trwałych subskrypcji, które są zarządzane za pomocą interfejsu usługi Azure Resource Management, narzędzi wiersza polecenia platformy Azure lub Azure Portal. Każda subskrypcja umożliwia korzystanie z maksymalnie 2 000 reguł wyboru, z których każdy może mieć warunek filtru i, w przypadku filtrów SQL, również akcję przekształcenia metadanych. Każde dopasowanie warunku filtru wybiera komunikat wejściowy, który ma zostać skopiowany do subskrypcji.  

Otrzymywanie komunikatów z subskrypcji jest takie samo, jak otrzymywanie komunikatów z kolejek. Każda subskrypcja ma skojarzoną kolejkę utraconych wiadomości i umożliwia automatyczne przekazywanie komunikatów do innej kolejki lub tematów.

Tematy JMS umożliwiają klientom dynamiczne tworzenie nietrwałych i trwałych subskrybentów, którzy opcjonalnie zezwalają na filtrowanie komunikatów za pomocą selektorów komunikatów. Te jednostki nieudostępnione nie są obsługiwane przez Service Bus. Składnia reguły filtru SQL dla Service Bus jest podobna do składni selektora komunikatów obsługiwanej przez JMS.

Strona wydawcy tematu JMS jest zgodna z Service Bus, jak pokazano w tym przykładzie, ale Subskrybenci dynamiczni nie są. Następujące interfejsy API JMS związane z topologią nie są obsługiwane w programie Service Bus.

## <a name="unsupported-features-and-restrictions"></a>Nieobsługiwane funkcje i ograniczenia

Następujące ograniczenia istnieją w przypadku używania JMS przez AMQP 1,0 z Service Bus, mianowicie:

* Dla sesji można używać tylko jednego obiektu **MessageProducer** lub **MessageConsumer** . Jeśli musisz utworzyć wiele obiektów **MessageProducer** lub **MessageConsumer** w aplikacji, Utwórz dedykowaną sesję dla każdego z nich.
* Nietrwałe subskrypcje tematu nie są obecnie obsługiwane.
* Obiekty **MessageSelector** nie są obecnie obsługiwane.
* Transakcje rozproszone nie są obsługiwane, ale sesje transakcyjne są obsługiwane.

Service Bus dzieli płaszczyznę kontroli od płaszczyzny danych, więc nie obsługuje kilku funkcji topologii dynamicznej JMS.

| Nieobsługiwana Metoda          | Zamień na                                                                             |
|-----------------------------|------------------------------------------------------------------------------------------|
| createDurableSubscriber     | Utwórz subskrypcję tematu, która określa porty selektora wiadomości.                                |
| createDurableConsumer       | Utwórz subskrypcję tematu, która określa porty selektora wiadomości.                                |
| createSharedConsumer        | Tematy Service Bus są zawsze możliwe do udostępnienia. Zapoznaj się z sekcją "JMS tematy a Service Bus tematy".                                    |
| createSharedDurableConsumer | Tematy Service Bus są zawsze możliwe do udostępnienia. Zapoznaj się z sekcją "JMS tematy a Service Bus tematy".                                      |
| createTemporaryTopic        | Utwórz temat za pomocą interfejsu API zarządzania, narzędzi lub portalu z *AutoDeleteOnIdle* ustawionym na okres ważności. |
| w temacie                 | Utwórz temat za pośrednictwem interfejsu API zarządzania, narzędzi lub portalu.                                         |
| Anuluj subskrypcję                 | Usuń interfejs API zarządzania tematami, narzędzia i Portal.                                            |
| Przeglądarka \ przeglądarki               | Ich. Użyj funkcji Peek () interfejsu API Service Bus.                         |
| createQueue                 | Utwórz kolejkę za pośrednictwem interfejsu API zarządzania, narzędzi lub portalu.                                           | 
| createTemporaryQueue        | Utwórz kolejkę za pomocą interfejsu API zarządzania, narzędzi lub portalu z *AutoDeleteOnIdle* ustawionym na okres ważności. |
| receiveNoWait               | Użyj metody Receive () dostarczonej przez zestaw SDK Service Bus i określ bardzo niski lub zero limitu czasu. |

## <a name="summary"></a>Podsumowanie

W tym artykule pokazano, jak używać funkcji komunikatów obsługiwanych przez brokera Service Bus, takich jak kolejki i publikowanie lub subskrybowanie tematów, z poziomu języka Java przy użyciu popularnych interfejsów API JMS i AMQP 1,0.

Możesz również użyć Service Bus AMQP 1,0 z innych języków, takich jak .NET, C, Python i PHP. Składniki utworzone przy użyciu tych różnych języków mogą niezawodnie wymieniać komunikaty i z pełną dokładnością dzięki obsłudze AMQP 1,0 w Service Bus.

## <a name="next-steps"></a>Następne kroki

* [Obsługa AMQP 1,0 w Azure Service Bus](service-bus-amqp-overview.md)
* [Używanie AMQP 1,0 z interfejsem API platformy .NET Service Bus](./service-bus-amqp-dotnet.md)
* [Podręcznik dewelopera Service Bus AMQP 1,0](service-bus-amqp-dotnet.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Centrum deweloperów języka Java](https://azure.microsoft.com/develop/java/)