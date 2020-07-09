---
title: Korzystanie z AMQP z interfejsem API usługi komunikatów Java & Azure Service Bus
description: Jak używać usługi wiadomości Java (JMS) z Azure Service Bus i Advanced Message Queuing Protocol (AMQP) 1,0.
ms.topic: article
ms.date: 06/23/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 04d2595951640b7fe878decfeb862863f06c17a2
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119161"
---
# <a name="use-the-java-message-service-jms-with-azure-service-bus-and-amqp-10"></a>Korzystanie z usługi wiadomości Java (JMS) z Azure Service Bus i AMQP 1,0

Obsługa protokołu **Advanced Message Queuing Protocol (AMQP) 1,0** w Azure Service Bus oznacza, że można używać funkcji kolejkowania i publikowania/subskrybowania komunikatów obsługiwanych przez brokera z różnych platform przy użyciu wydajnego protokołu binarnego. Ponadto można tworzyć aplikacje składające się ze składników utworzonych przy użyciu różnych języków, struktur i systemów operacyjnych.

W tym artykule wyjaśniono, jak korzystać z funkcji Azure Service Bus Messaging (kolejek i publikowania/subskrybowania tematów) z aplikacji Java przy użyciu popularnego interfejsu API **usługi komunikatów języka Java (JMS)** za pośrednictwem protokołu AMQP.

## <a name="get-started-with-service-bus"></a>Rozpoczynanie pracy z usługą Service Bus
W tym przewodniku przyjęto założenie, że masz już Service Bus przestrzeń nazw zawierającą kolejkę o nazwie `basicqueue` . Jeśli tego nie zrobisz, możesz [utworzyć przestrzeń nazw i kolejkę](service-bus-create-namespace-portal.md) za pomocą [Azure Portal](https://portal.azure.com). Aby uzyskać więcej informacji na temat sposobu tworzenia Service Bus obszarów nazw i kolejek, zobacz [Rozpoczynanie pracy z kolejkami Service Bus](service-bus-dotnet-get-started-with-queues.md).

> [!NOTE]
> Partycjonowane kolejki i tematy obsługują również AMQP. Aby uzyskać więcej informacji, zobacz [partycjonowane jednostki obsługi komunikatów](service-bus-partitioning.md) i [obsługę AMQP 1,0 Service Bus dla kolejek i tematów z podziałem na partycje](service-bus-partitioned-queues-and-topics-amqp-overview.md).
> 
>

## <a name="what-jms-features-are-supported"></a>Jakie funkcje JMS są obsługiwane?

Poniżej przedstawiono funkcje JMS, które są obsługiwane w Azure Service Bus.

| Funkcje | Azure Service Bus warstwa standardowa — JMS 1,1 | Azure Service Bus — warstwa Premium — JMS 2,0 (wersja zapoznawcza) |
|---|---|---|
| Tworzenie jednostek za pośrednictwem AMQP | Nieobsługiwane | **Obsługiwane** |
| Kolejki | **Obsługiwane** | **Obsługiwane** |
| Tematy | **Obsługiwane** | **Obsługiwane** |
| Kolejki tymczasowe | Nieobsługiwane <br/> (Utwórz kolejkę regularną z zestawem *AutoDeleteOnIdle* ) | **Obsługiwane** |
| Tematy tymczasowe | Nieobsługiwane | **Obsługiwane** |
| Selektory komunikatów | Nieobsługiwane | **Obsługiwane** |
| Przeglądarki kolejek | Nieobsługiwane <br/> (Użyj funkcji *wglądu* w interfejsie API Service Bus) | **Obsługiwane** |
| Udostępnione trwałe subskrypcje | **Obsługiwane** | **Obsługiwane**|
| Nieudostępniane trwałe subskrypcje | Nieobsługiwane | **Obsługiwane** |
| Udostępnione nietrwałe subskrypcje | Nieobsługiwane | **Obsługiwane** |
| Nieudostępniane subskrypcje nietrwałe | Nieobsługiwane | **Obsługiwane** |
| Anulowanie subskrypcji dla trwałych subskrypcji | Nieobsługiwane | **Obsługiwane** |
| ReceiveNoWait | Nieobsługiwane | **Obsługiwane** |
| Transakcje rozproszone | Nieobsługiwane | Nieobsługiwane |
| Terminus trwałe | Nieobsługiwane | Nieobsługiwane |

### <a name="additional-caveats-for-service-bus-standard-tier"></a>Dodatkowe zastrzeżenia dla Service Bus warstwy Standardowa
Dla **sesji**można używać tylko jednego **MessageProducer** lub **MessageConsumer** . Jeśli musisz utworzyć wiele **MessageProducers** lub **MessageConsumers** w aplikacji, Utwórz dedykowaną **sesję** dla każdego z nich.

## <a name="downloading-the-java-message-service-jms-client-library"></a>Pobieranie biblioteki klienta usługi wiadomości Java (JMS)

Aby nawiązać połączenie z usługą Azure Service Bus i wykorzystać interfejs API usługi wiadomości Java (JMS) dla AMQP, należy użyć poniższych bibliotek. Muszą one zostać dodane do ścieżki kompilacji przy użyciu preferowanego narzędzia do zarządzania zależnościami dla projektu.

Wymagana biblioteka klienta zależy od używanej warstwy cenowej.

### <a name="premium-tier---jms-20-over-amqp-preview"></a>Warstwa Premium — JMS 2,0 over AMQP (wersja zapoznawcza)

Aby korzystać ze wszystkich funkcji w wersji zapoznawczej dostępnych w Azure Service Bus warstwie Premium, użyj biblioteki [Azure-ServiceBus-JMS](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) .

### <a name="standard-tier---jms-11-over-amqp"></a>Warstwa standardowa — JMS 1,1 over AMQP

Aby korzystać z funkcji JMS obsługiwanych przez Service Bus warstwy Standardowa (Zobacz, [jakie funkcje JMS są obsługiwane?](service-bus-java-how-to-use-jms-api-amqp.md#what-jms-features-are-supported)), Użyj poniższych bibliotek:

* [Specyfikacja Geronimo JMS 1,1](https://search.maven.org/artifact/org.apache.geronimo.specs/geronimo-jms_1.1_spec)
* [Klient Qpid JMS](https://search.maven.org/artifact/org.apache.qpid/qpid-jms-client)

> [!NOTE]
> JMS nazwy i wersje JAR mogą ulec zmianie. Aby uzyskać szczegółowe informacje, zobacz [QPID JMS-AMQP 1,0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10).
>

## <a name="coding-java-applications"></a>Kodowanie aplikacji języka Java

Po zaimportowaniu zależności aplikacje Java mogą być zapisywane w JMS dostawcy niezależny od sposób.

Ponieważ Azure Service Bus Standard i premium różnią się w zależnościach i liczbie obsługiwanych przez nie funkcji JMS, model programowania jest nieco różny dla dwóch.

> [!IMPORTANT]
> Poniższy przewodnik przedstawia sposób nawiązywania połączeń z Azure Service Busą przez prostą aplikację.
>
> Mając na względzie, że większość architektur aplikacji dla przedsiębiorstw może mieć niestandardowy sposób zarządzania zależnościami i konfiguracjami, użyj poniższego przewodnika, aby zrozumieć, co jest potrzebne i odpowiednio dostosować do aplikacji.
>

### <a name="connecting-to-azure-service-bus-using-jms"></a>Nawiązywanie połączenia z Azure Service Bus przy użyciu JMS

Aby nawiązać połączenie z usługą Azure Service Bus przy użyciu klientów JMS, konieczne jest **Parametry** połączenia, które są dostępne w zasadach dostępu współdzielonego w [Azure Portal](https://portal.azure.com) w ramach **podstawowych parametrów połączeń**.


#### <a name="connecting-to-azure-service-bus-premium-over-jms-20-preview"></a>Nawiązywanie połączenia z usługą Azure Service Bus Premium over JMS 2,0 (wersja zapoznawcza)

1. Tworzenie wystąpienia`ServiceBusJmsConnectionFactorySettings`
    ```java
    ServiceBusJmsConnectionFactorySettings connFactorySettings = new ServiceBusJmsConnectionFactorySettings();

    connFactorySettings.setConnectionIdleTimeoutMS(20000);
    ```
2. Utwórz wystąpienie `ServiceBusJmsConnectionFactory` z odpowiednią `ServiceBusConnectionString` .
    ```java
    String ServiceBusConnectionString = "<SERVICE_BUS_CONNECTION_STRING_WITH_MANAGE_PERMISSIONS>";
    ConnectionFactory factory = new ServiceBusJmsConnectionFactory(ServiceBusConnectionString, connFactorySettings);
    ```

3. Użyj, `ConnectionFactory` Aby utworzyć `Connection` a i`Session` 
    ```java
    Connection connection = factory.createConnection();
    Session session = connection.createSession();
    ```
    lub a `JMSContext` (dla klientów z systemem JMS 2,0)

    ```java
    JMSContext jmsContext = factory.createContext();
    ```

#### <a name="connecting-to-azure-service-bus-standard-over-jms-11"></a>Nawiązywanie połączenia ze standardem Azure Service Bus ponad JMS 1,1

1. Wstaw Azure Service Bus konfigurację do pliku właściwości JNDI o nazwie **ServiceBus. Properties**.
    ```properties
    # servicebus.properties - sample JNDI configuration
    
    # Register a ConnectionFactory in JNDI using the form:
    # connectionfactory.[jndi_name] = [ConnectionURL]
    connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net
    ```

2. Konfiguracja kontekstu JNDI i Konfigurowanie ConnectionFactory
    ```java
    ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);

    // set up JNDI context
    Hashtable<String, String> hashtable = new Hashtable<>();
    hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
    hashtable.put("queue.QUEUE", "BasicQueue");
    hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
    Context context = new InitialContext(hashtable);
    
    ConnectionFactory factory = (ConnectionFactory) context.lookup("SBCF");
    ```
3. Użyj, `ConnectionFactory` Aby utworzyć `Connection` a i `Session` .
    ```java
    Connection connection - factory.createConnection(csb.getSasKeyName(), csb.getSasKey());
    Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
    ```

### <a name="write-the-jms-application"></a>Napisz aplikację JMS

Po utworzeniu `Session` `JMSContext` wystąpienia lub, aplikacja może wykorzystać znane interfejsy API JMS do wykonywania operacji zarządzania i danych.

Zapoznaj się z listą [obsługiwanych funkcji JMS](service-bus-java-how-to-use-jms-api-amqp.md#what-jms-features-are-supported) dla warstwy Standardowa i Premium, aby sprawdzić, które interfejsy API będą obsługiwane w poszczególnych warstwach.

## <a name="summary"></a>Podsumowanie
W tym przewodniku przedstawiono sposób, w jaki aplikacje klienckie Java korzystające z usługi wiadomości Java (JMS) over AMQP 1,0 mogą współdziałać z Azure Service Bus.

Możesz również użyć Service Bus AMQP 1,0 z innych języków, w tym .NET, C, Python i PHP. Składniki utworzone przy użyciu tych różnych języków mogą niezawodnie wymieniać komunikaty i z pełną dokładnością dzięki obsłudze AMQP 1,0 w Service Bus.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat Azure Service Bus i szczegółów dotyczących jednostek usługi wiadomości języka Java (JMS), zapoznaj się z poniższymi linkami — 
* [Service Bus — kolejki, tematy i subskrypcje](service-bus-queues-topics-subscriptions.md)
* [Service Bus — jednostki usługi komunikatów języka Java](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities-preview)
* [Obsługa AMQP 1,0 w Azure Service Bus](service-bus-amqp-overview.md)
* [Podręcznik dewelopera Service Bus AMQP 1,0](service-bus-amqp-dotnet.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)

