---
title: Korzystanie z AMQP z interfejsem API usługi komunikatów Java & Azure Service Bus
description: Jak używać usługi wiadomości Java (JMS) z Azure Service Bus i Advanced Message Queuing Protocol (AMQP) 1,0.
ms.topic: article
ms.date: 06/23/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: ccea6175d0baec56b609538d15c32892bb2edff0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85341735"
---
# <a name="use-the-java-message-service-jms-with-azure-service-bus-and-amqp-10"></a>Korzystanie z usługi wiadomości Java (JMS) z Azure Service Bus i AMQP 1,0
W tym artykule wyjaśniono, jak używać funkcji Azure Service Bus Messaging (kolejek i publikowania/subskrybowania tematów) z aplikacji Java przy użyciu popularnego interfejsu API usługi komunikatów języka Java (JMS). Istnieje [artykuł towarzyszący](service-bus-amqp-dotnet.md) , w którym wyjaśniono, jak to zrobić za pomocą interfejsu API programu Azure Service Bus .NET. Za pomocą tych dwóch przewodników można dowiedzieć się więcej na temat obsługi komunikatów na wielu platformach przy użyciu AMQP 1,0.

Advanced Message Queuing Protocol (AMQP) 1,0 to wydajny, niezawodny protokół obsługi komunikatów na poziomie sieci, który umożliwia tworzenie niezawodnych aplikacji do obsługi komunikatów dla wielu platform.

Obsługa AMQP 1,0 w Azure Service Bus oznacza, że można używać funkcji kolejkowania i publikowania/subskrybowania komunikatów obsługiwanych przez brokera z różnych platform przy użyciu wydajnego protokołu binarnego. Ponadto można tworzyć aplikacje składające się ze składników utworzonych przy użyciu różnych języków, struktur i systemów operacyjnych.

## <a name="get-started-with-service-bus"></a>Rozpoczynanie pracy z usługą Service Bus
W tym przewodniku przyjęto założenie, że masz już Service Bus przestrzeń nazw zawierającą kolejkę o nazwie `basicqueue` . Jeśli tego nie zrobisz, możesz [utworzyć przestrzeń nazw i kolejkę](service-bus-create-namespace-portal.md) za pomocą [Azure Portal](https://portal.azure.com). Aby uzyskać więcej informacji na temat sposobu tworzenia Service Bus obszarów nazw i kolejek, zobacz [Rozpoczynanie pracy z kolejkami Service Bus](service-bus-dotnet-get-started-with-queues.md).

> [!NOTE]
> Partycjonowane kolejki i tematy obsługują również AMQP. Aby uzyskać więcej informacji, zobacz [partycjonowane jednostki obsługi komunikatów](service-bus-partitioning.md) i [obsługę AMQP 1,0 Service Bus dla kolejek i tematów z podziałem na partycje](service-bus-partitioned-queues-and-topics-amqp-overview.md).
> 
> 

## <a name="downloading-the-amqp-10-jms-client-library"></a>Pobieranie biblioteki klienckiej AMQP 1,0 JMS
Aby uzyskać informacje o tym, gdzie pobrać najnowszą wersję biblioteki klienta Apache Qpid JMS AMQP 1,0, odwiedź stronę [https://qpid.apache.org/download.html](https://qpid.apache.org/download.html) .

Podczas kompilowania i uruchamiania aplikacji JMS z Service Bus należy dodać następujące cztery pliki JAR z archiwum dystrybucji dystrybucyjnej systemu Apache Qpid JMS AMQP 1,0 do ścieżki w języku Java.

* Geronimo-JMS \_ 1,1 \_ spec-1.0. jar
* qpid-JMS-Client-[wersja]. jar

> [!NOTE]
> JMS nazwy i wersje JAR mogą ulec zmianie. Aby uzyskać szczegółowe informacje, zobacz [QPID JMS-AMQP 1,0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10).

## <a name="coding-java-applications"></a>Kodowanie aplikacji języka Java
### <a name="java-naming-and-directory-interface-jndi"></a>Nazwa i interfejs katalogu Java (JNDI)
JMS używa języka Java Name and Directory Interface (JNDI) w celu utworzenia rozdzielenia między nazwami logicznymi i nazwami fizycznymi. Dwa typy obiektów JMS są rozwiązywane za pomocą JNDI: ConnectionFactory i Destination. JNDI korzysta z modelu dostawcy, w którym można podłączyć różne usługi katalogowe do obsługi obowiązków rozpoznawania nazw. Biblioteka Apache Qpid JMS AMQP 1,0 zawiera prostą, opartą na plikach Właściwość dostawcę JNDI, który jest konfigurowany przy użyciu pliku właściwości w następującym formacie:

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

#### <a name="setup-jndi-context-and-configure-the-connectionfactory"></a>Konfiguracja kontekstu JNDI i Konfigurowanie ConnectionFactory

**Parametry** , do których odwołuje się element "zasady dostępu współdzielonego" w [Azure Portal](https://portal.azure.com) w ramach **podstawowych parametrów połączenia**
```java
// The connection string builder is the only part of the azure-servicebus SDK library
// we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
// connection string. 
ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
// set up JNDI context
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
Wpis używany do definiowania miejsca docelowego w pliku właściwości Qpid dostawcy JNDI ma następujący format:

Aby utworzyć kolejkę docelową dla producenta — 
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Producer
MessageProducer producer = session.createProducer(queue);
```

Aby utworzyć kolejkę docelową dla konsumenta- 
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Consumer
MessageConsumer consumer = session.createConsumer(queue);
```

### <a name="write-the-jms-application"></a>Napisz aplikację JMS
W przypadku korzystania z programu JMS z Service Bus nie są wymagane żadne specjalne interfejsy API ani opcje. Istnieje jednak kilka ograniczeń, które zostaną omówione w przyszłości. Podobnie jak w przypadku dowolnej aplikacji JMS, najpierw wymagane jest konfiguracja środowiska JNDI, aby można było rozpoznać **ConnectionFactory** i miejsc docelowych.

#### <a name="configure-the-jndi-initialcontext"></a>Konfigurowanie InitialContext JNDI
Środowisko JNDI jest konfigurowane przez przekazanie tablicy skrótów informacji o konfiguracji do konstruktora klasy javax.naming.InitialContext. Dwa wymagane elementy w elemencie Hashtable są nazwą klasy początkowej fabryki kontekstu i adresem URL dostawcy. Poniższy kod przedstawia sposób konfigurowania środowiska JNDI do używania dostawcy JNDI na podstawie właściwości Qpid z plikiem właściwości o nazwie **ServiceBus. Properties**.

```java
// set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + \
"?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>Prosta aplikacja JMSa korzystająca z kolejki Service Bus
Poniższy przykładowy program wysyła JMS textmessages do kolejki Service Bus przy użyciu logicznej nazwy JNDI kolejki i odbiera komunikaty z powrotem.

Wszystkie informacje o kodzie źródłowym i konfiguracji można uzyskać, korzystając z [Azure Service Bus Samples JMS Queue szybki start](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client/JmsQueueQuickstart)

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
 * This sample demonstrates how to send messages from a JMS Queue producer into
 * an Azure Service Bus Queue, and receive them with a JMS message consumer.
 * JMS Queue. 
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
        
        // set up JNDI context
        Hashtable<String, String> hashtable = new Hashtable<>();
        hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
        hashtable.put("queue.QUEUE", "BasicQueue");
        hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
        Context context = new InitialContext(hashtable);
        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
        
        // Look up queue
        Destination queue = (Destination) context.lookup("QUEUE");

        // we create a scope here so we can use the same set of local variables cleanly 
        // again to show the receive side separately with minimal clutter
        {
            // Create Connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            // Create Session, no transaction, client ack
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
            // Create Connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            connection.start();
            // Create Session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
            // Create consumer
            MessageConsumer consumer = session.createConsumer(queue);
            // create a listener callback to receive the messages
            consumer.setMessageListener(message -> {
                try {
                    // receives message is passed to callback
                    System.out.printf("Received message %d with sq#: %s\n",
                            totalReceived.incrementAndGet(), // increments the tracking counter
                            message.getJMSMessageID());
                    message.acknowledge();
                } catch (Exception e) {
                    logger.error(e);
                }
            });

            // wait on the main thread until all sent messages have been received
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

            // parse connection string from command line
            Options options = new Options();
            options.addOption(new Option("c", true, "Connection string"));
            CommandLineParser clp = new DefaultParser();
            CommandLine cl = clp.parse(options, args);
            if (cl.getOptionValue("c") != null) {
                connectionString = cl.getOptionValue("c");
            }

            // get overrides from the environment
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
Poniżej znajduje się dane wyjściowe formularza przez uruchomienie aplikacji:

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
Poniżej przedstawiono sposób, w jaki Dyspozycja AMQP jest tłumaczona na operację Service Bus:

```Output
ACCEPTED = 1; -> Complete()
REJECTED = 2; -> DeadLetter()
RELEASED = 3; (just unlock the message in service bus, will then get redelivered)
MODIFIED_FAILED = 4; -> Abandon() which increases delivery count
MODIFIED_FAILED_UNDELIVERABLE = 5; -> Defer()
```

## <a name="jms-topics-vs-service-bus-topics"></a>Tematy JMS a Service Bus — tematy
Korzystanie z Azure Service Bus tematów i subskrypcji za pośrednictwem interfejsu API usługi wiadomości Java (JMS) zapewnia podstawowe możliwości wysyłania i odbierania. Jest to wygodny wybór podczas przenoszenia aplikacji z innych brokerów komunikatów za pomocą interfejsów API zgodnych z JMS, mimo że tematy Service Bus różnią się od tematów JMS i wymagają kilku korekt. 

Tematy Azure Service Bus kierować wiadomości do nazwanych, udostępnionych, trwałych subskrypcji, które są zarządzane za pomocą interfejsu usługi Azure Resource Management, narzędzi wiersza polecenia platformy Azure lub Azure Portal. Każda subskrypcja umożliwia korzystanie z maksymalnie 2000 reguł wyboru, z których każdy może mieć warunek filtru i, w przypadku filtrów SQL, również akcję przekształcenia metadanych. Każde dopasowanie warunku filtru wybiera komunikat wejściowy, który ma zostać skopiowany do subskrypcji.  

Otrzymywanie komunikatów z subskrypcji jest identyczne z komunikatami z kolejki. Każda subskrypcja ma skojarzoną kolejkę utraconych wiadomości i umożliwia automatyczne przekazywanie komunikatów do innej kolejki lub tematów. 

Tematy JMS umożliwiają klientom dynamiczne tworzenie nietrwałych i trwałych subskrybentów, którzy opcjonalnie zezwalają na filtrowanie komunikatów za pomocą selektorów komunikatów. Te jednostki nieudostępnione nie są obsługiwane przez Service Bus. Składnia reguły filtru SQL dla Service Bus jest jednak podobna do składni selektora komunikatów obsługiwanej przez JMS. 

Strona wydawcy tematu JMS jest zgodna z Service Bus, jak pokazano w tym przykładzie, ale Subskrybenci dynamiczni nie są. Następujące interfejsy API JMS związane z topologią nie są obsługiwane w programie Service Bus. 

## <a name="unsupported-features-and-restrictions"></a>Nieobsługiwane funkcje i ograniczenia
W przypadku korzystania z programu JMS AMQP 1,0 z Service Bus w programie istnieją następujące ograniczenia:

* Dla **sesji**można używać tylko jednego **MessageProducer** lub **MessageConsumer** . Jeśli musisz utworzyć wiele **MessageProducers** lub **MessageConsumers** w aplikacji, Utwórz dedykowaną **sesję** dla każdego z nich.
* Nietrwałe subskrypcje tematu nie są obecnie obsługiwane.
* **MessageSelectors** nie są obecnie obsługiwane.
* Transakcje rozproszone nie są obsługiwane (ale sesje transakcyjne są obsługiwane).

Ponadto Azure Service Bus dzieli płaszczyznę kontroli z płaszczyzny danych i w związku z tym nie obsługuje kilku funkcji dynamicznej topologii JMS:

| Nieobsługiwana Metoda          | Zamień na                                                                             |
|-----------------------------|------------------------------------------------------------------------------------------|
| createDurableSubscriber     | Tworzenie subskrypcji tematu przenoszenie selektora komunikatów                                 |
| createDurableConsumer       | Tworzenie subskrypcji tematu przenoszenie selektora komunikatów                                 |
| createSharedConsumer        | Tematy Service Bus są zawsze możliwe do udostępniania, zobacz powyżej                                       |
| createSharedDurableConsumer | Tematy Service Bus są zawsze możliwe do udostępniania, zobacz powyżej                                       |
| createTemporaryTopic        | Utwórz temat za pomocą interfejsu API zarządzania/narzędzia/Portal z *AutoDeleteOnIdle* ustawionym na okres ważności |
| w temacie                 | Tworzenie tematu za pomocą interfejsu API zarządzania/narzędzia/Portal                                           |
| Anuluj subskrypcję                 | usuwanie tematu API zarządzania tematami/narzędzia/Portal                                             |
| Przeglądarka \ przeglądarki               | ich. Korzystanie z funkcji wglądu () interfejsu API Service Bus                         |
| createQueue                 | Tworzenie kolejki za pomocą interfejsu API zarządzania/narzędzi/portalu                                           | 
| createTemporaryQueue        | Tworzenie kolejki za pomocą interfejsu API zarządzania/narzędzi/portalu z *AutoDeleteOnIdle* ustawionym na okres ważności |
| receiveNoWait               | Użyj metody Receive () dostarczonej przez zestaw SDK Service Bus i określ bardzo niski lub zero limitu czasu |

## <a name="summary"></a>Podsumowanie
W tym przewodniku opisano sposób Service Bus korzystania z funkcji komunikatów obsługiwanych przez brokera (w tematach JMS i publikowania/subskrybowania) z poziomu języka Java przy użyciu popularnych interfejsów API i AMQP 1,0.

Możesz również użyć Service Bus AMQP 1,0 z innych języków, w tym .NET, C, Python i PHP. Składniki utworzone przy użyciu tych różnych języków mogą niezawodnie wymieniać komunikaty i z pełną dokładnością dzięki obsłudze AMQP 1,0 w Service Bus.

## <a name="next-steps"></a>Następne kroki
* [Obsługa AMQP 1,0 w Azure Service Bus](service-bus-amqp-overview.md)
* [Jak używać AMQP 1,0 z interfejsem API platformy .NET Service Bus](service-bus-dotnet-advanced-message-queuing.md)
* [Podręcznik dewelopera Service Bus AMQP 1,0](service-bus-amqp-dotnet.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Centrum deweloperów języka Java](https://azure.microsoft.com/develop/java/)

