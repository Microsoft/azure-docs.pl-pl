---
title: Migrowanie aplikacji usługi wiadomości języka Java (JMS) z platformy Apache ActiveMQ do Azure Service Bus | Microsoft Docs
description: W tym artykule opisano sposób migrowania istniejących aplikacji JMS, które współdziałają z usługą Apache ActiveMQ w celu współdziałania z usługą Azure Service Bus.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2020
ms.author: aschhab
ms.custom: devx-track-java
ms.openlocfilehash: b8408dde86d1902cf5b4899c4783c9dd185449ee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92515750"
---
# <a name="migrate-existing-java-message-service-jms-20-applications-from-apache-activemq-to-azure-service-bus"></a>Migruj istniejące aplikacje JMS (Java Message Service) 2,0 z platformy Apache ActiveMQ do Azure Service Bus

W tym artykule omówiono sposób modyfikowania istniejącej aplikacji JMS (Java Message Service) 2,0, która współdziała z brokerem JMS w celu interakcji z Azure Service Bus. W szczególności artykuł dotyczy migracji z platformy Apache ActiveMQ lub Amazon MQ.

Azure Service Bus obsługuje środowisko Java 2 platform, wydanie korporacyjne i obciążenia sprężynowe korzystające z interfejsu API JMS 2,0 over Advanced Message Queueing Protocol (AMQP).

## <a name="before-you-start"></a>Przed rozpoczęciem

### <a name="differences-between-azure-service-bus-and-apache-activemq"></a>Różnice między Azure Service Bus i Apache ActiveMQ

Azure Service Bus i Apache ActiveMQ są brokerami komunikatów, działającymi jako dostawcy JMS dla aplikacji klienckich do wysyłania komunikatów do i odbierania komunikatów z programu. Umożliwiają one włączenie semantyki punkt-punkt z kolejkami oraz semantykę publikowania/subskrybowania z tematami i subskrypcjami. 

Nawet Dlatego istnieją pewne różnice między tymi dwoma, jak przedstawiono w poniższej tabeli:

| Kategoria | ActiveMQ | Usługa Azure Service Bus |
| --- | --- | --- |
| Obsługa warstw aplikacji | Monolitu klastrowane | Dwie warstwy <br> (Brama + zaplecze) |
| Obsługa protokołów | <ul> <li>AMQP</li> <li> STOMP </li> <li> OpenWire </li> </ul> | AMQP |
| Tryb aprowizacji | <ul> <li> Infrastruktura jako usługa (IaaS), lokalna </li> <li> Amazon MQ (platforma zarządzana jako usługa) </li> | Zarządzana platforma jako usługa (PaaS) |
| Rozmiar komunikatu | Klient konfigurowalny | 1 MB (warstwa Premium) |
| Wysoka dostępność | Zarządzane przez klienta | Zarządzane przez platformę |
| Odzyskiwanie po awarii | Zarządzane przez klienta | Zarządzane przez platformę | 

### <a name="current-supported-and-unsupported-features"></a>Bieżące obsługiwane i nieobsługiwane funkcje

[!INCLUDE [service-bus-jms-features-list](../../includes/service-bus-jms-feature-list.md)]

### <a name="considerations"></a>Zagadnienia do rozważenia

Dwuwarstwowy charakter Azure Service Bus zapewnia różne możliwości ciągłości działania firmy (wysoka dostępność i odzyskiwanie po awarii). Należy jednak wziąć pod uwagę podczas korzystania z funkcji JMS.

#### <a name="service-upgrades"></a>Uaktualnienia usług

W przypadku uaktualnień i ponownych uruchomień usługi Service Bus, tymczasowe kolejki lub tematy są usuwane. Jeśli aplikacja jest wrażliwa na utratę danych w tymczasowych kolejkach lub tematach, nie używaj tymczasowych kolejek ani tematów. Zamiast tego użyj trwałych kolejek, tematów i subskrypcji.

#### <a name="data-migration"></a>Migracja danych

W ramach migrowania i modyfikowania aplikacji klienckich w celu współpracy z Azure Service Bus dane przechowywane w ActiveMQ nie są migrowane do Service Bus. Może być potrzebna aplikacja niestandardowa, która będzie mogła opróżniać kolejki, tematy i subskrypcje ActiveMQ, a następnie odtwarzać komunikaty w kolejkach, tematach i subskrypcjach Service Bus.

#### <a name="authentication-and-authorization"></a>Uwierzytelnianie i autoryzacja

Kontrola dostępu oparta na rolach na platformie Azure (RBAC), obsługiwana przez Azure Active Directory, jest preferowanym mechanizmem uwierzytelniania dla Service Bus. Ponieważ RBAC lub uwierzytelnianie oparte na oświadczeniach nie jest obecnie obsługiwane przez usługę Apache QPID JMS, należy jednak użyć kluczy SAS do uwierzytelniania.

## <a name="pre-migration"></a>Przed migracją

### <a name="version-check"></a>Sprawdzanie wersji

Podczas pisania aplikacji JMS są używane następujące składniki i wersje: 

| Składnik | Wersja |
|---|---|
| Interfejs API usługi wiadomości Java (JMS) | 1,1 lub więcej |
| Protokół AMQP | 1.0 |

### <a name="ensure-that-amqp-ports-are-open"></a>Upewnij się, że porty AMQP są otwarte

Service Bus obsługuje komunikację za pośrednictwem protokołu AMQP. W tym celu Włącz komunikację przez porty 5671 (AMQP) i 443 (TCP). W zależności od tego, gdzie znajdują się aplikacje klienckie, może być potrzebny bilet pomocy technicznej, aby umożliwić komunikację za pośrednictwem tych portów.

> [!IMPORTANT]
> Service Bus obsługuje tylko protokół AMQP 1,0.

### <a name="set-up-enterprise-configurations"></a>Konfigurowanie konfiguracji przedsiębiorstwa

Service Bus włącza różne funkcje zabezpieczeń i wysokiej dostępności dla przedsiębiorstw. Aby uzyskać więcej informacji, zobacz: 

  * [Punkty końcowe usługi dla sieci wirtualnej](service-bus-service-endpoints.md)
  * [Zapora](service-bus-ip-filtering.md)
  * [Szyfrowanie po stronie usługi z kluczem zarządzanym przez klienta (BYOK)](configure-customer-managed-key.md)
  * [Prywatne punkty końcowe](private-link-service.md)
  * [Uwierzytelnianie i autoryzacja](service-bus-authentication-and-authorization.md)

### <a name="monitoring-alerts-and-tracing"></a>Monitorowanie, alerty i śledzenie

Dla każdej przestrzeni nazw Service Bus można publikować metryki na Azure Monitor. Za pomocą tych metryk można wysyłać alerty i dynamiczne skalowanie zasobów przyznanych do przestrzeni nazw.

Aby uzyskać więcej informacji na temat różnych metryk i sposobu konfigurowania na nich alertów, zobacz [Service Bus metryki w Azure monitor](service-bus-metrics-azure-monitor.md). Możesz również dowiedzieć się więcej o [śledzeniu po stronie klienta dla operacji na danych](service-bus-end-to-end-tracing.md) oraz [rejestrowania operacyjnego/diagnostyki dla operacji zarządzania](service-bus-diagnostic-logs.md).

### <a name="metrics---new-relic"></a>Metryki — nowy Relic

Można skorelować metryki z mapy ActiveMQ, do których metryki w Azure Service Bus. Zobacz następujący artykuł w nowej witrynie sieci Web Relic:

  * [ActiveMQ/Amazon MQ nowe metryki Relic](https://docs.newrelic.com/docs/integrations/amazon-integrations/aws-integrations-list/aws-mq-integration)
  * [Azure Service Bus nowe metryki Relic](https://docs.newrelic.com/docs/integrations/microsoft-azure-integrations/azure-integrations-list/azure-service-bus-monitoring-integration)

> [!NOTE]
> Obecnie nowe Relic nie mają bezpośredniej integracji z ActiveMQ, ale mają dostępne metryki Amazon MQ. Ponieważ Amazon MQ pochodzi od ActiveMQ, Poniższa tabela mapuje nowe metryki Relic z Amazon MQ na Azure Service Bus.
>

|Grupowanie metryk| Metryka Amazon/ActiveMQ | Metryka Azure Service Bus |
|------------|---------------------------|--------------------------|
|Brokera|`CpuUtilization`|`CPUXNS`|
|Brokera|`MemoryUsage`|`WSXNS`|
|Brokera|`CurrentConnectionsCount`|`activeConnections`|
|Brokera|`EstablishedConnectionsCount`|`activeConnections` + `connectionsClosed`|
|Brokera|`InactiveDurableTopicSubscribersCount`|Korzystanie z metryk subskrypcji|
|Brokera|`TotalMessageCount`|Użyj poziomu kolejki/tematu/subskrypcji `activeMessages`|
|Kolejka/temat|`EnqueueCount`|`incomingMessages`|
|Kolejka/temat|`DequeueCount`|`outgoingMessages`|
|Kolejka|`QueueSize`|`sizeBytes`|



## <a name="migration"></a>Migracja

Aby przeprowadzić migrację istniejącej aplikacji JMS 2,0 w celu współdziałania z Service Bus, wykonaj kroki opisane w kolejnych sekcjach.

### <a name="export-the-topology-from-activemq-and-create-the-entities-in-service-bus-optional"></a>Eksportowanie topologii z ActiveMQ i tworzenie jednostek w Service Bus (opcjonalnie)

Aby mieć pewność, że aplikacje klienckie mogą bezproblemowo łączyć się z Service Bus, dokonaj migracji topologii (w tym kolejek, tematów i subskrypcji) z platformy Apache ActiveMQ do Service Bus.

> [!NOTE]
> W przypadku aplikacji JMS tworzysz kolejki, tematy i subskrypcje w ramach operacji środowiska uruchomieniowego. Większość dostawców JMS (brokerów komunikatów) daje możliwość tworzenia tych plików w czasie wykonywania. Dlatego ten krok eksportu jest uznawany za opcjonalny. Aby upewnić się, że aplikacja ma uprawnienia do tworzenia topologii w czasie wykonywania, użyj parametrów połączenia z uprawnieniami sygnatury dostępu współdzielonego `Manage` .

W tym celu:

1. Użyj [narzędzi wiersza polecenia ActiveMQ](https://activemq.apache.org/activemq-command-line-tools-reference) do wyeksportowania topologii.
1. Utwórz ponownie tę samą topologię przy użyciu [szablonu Azure Resource Manager](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).
1. Uruchom szablon Azure Resource Manager.


### <a name="import-the-maven-dependency-for-service-bus-jms-implementation"></a>Zaimportuj zależność Maven dla implementacji JMS Service Bus

Aby zapewnić bezproblemową łączność z Service Bus, Dodaj `azure-servicebus-jms` pakiet jako zależność do pliku Maven `pom.xml` w następujący sposób:

```xml
<dependencies>
...
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus-jms</artifactId>
    </dependency>
...
</dependencies>
```

### <a name="application-server-configuration-changes"></a>Zmiany konfiguracji serwera aplikacji

Ta część jest dostosowana do serwera aplikacji, który obsługuje aplikacje klienckie łączące się z usługą ActiveMQ.

#### <a name="tomcat"></a>Tomcat

Tutaj Rozpocznij od konfiguracji specyficznej dla ActiveMQ, jak pokazano w `/META-INF/context.xml` pliku.

```XML
<Context antiJARLocking="true">
    <Resource
        name="jms/ConnectionFactory"
        auth="Container"
        type="org.apache.activemq.ActiveMQConnectionFactory"
        description="JMS Connection Factory"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        brokerURL="tcp://localhost:61616"
        brokerName="LocalActiveMQBroker"
        useEmbeddedBroker="false"/>

    <Resource name="jms/topic/MyTopic"
        auth="Container"
        type="org.apache.activemq.command.ActiveMQTopic"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO"/>
    <Resource name="jms/queue/MyQueue"
        auth="Container"
        type="org.apache.activemq.command.ActiveMQQueue"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO.QUEUE"/>
</Context>
```

Możesz to zrobić, aby wskazać Service Bus w następujący sposób:

```xml
<Context antiJARLocking="true">
    <Resource
        name="jms/ConnectionFactory"
        auth="Container"
        type="com.microsoft.azure.servicebus.jms.ServiceBusJmsConnectionFactory"
        description="JMS Connection Factory"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        connectionString="<INSERT YOUR SERVICE BUS CONNECTION STRING HERE>"/>

    <Resource name="jms/topic/MyTopic"
        auth="Container"
        type="org.apache.qpid.jms.JmsTopic"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO"/>
    <Resource name="jms/queue/MyQueue"
        auth="Container"
        type="org.apache.qpid.jms.JmsQueue"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO.QUEUE"/>
</Context>
```

#### <a name="spring-applications"></a>Aplikacje sprężynowe

##### <a name="update-the-applicationproperties-file"></a>Aktualizowanie `application.properties` pliku

Jeśli używasz aplikacji rozruchowej ze sprężyną do nawiązywania połączenia z usługą ActiveMQ, chcesz usunąć właściwości specyficzne dla ActiveMQ z `application.properties` pliku.

```properties
spring.activemq.broker-url=<ACTIVEMQ BROKER URL>
spring.activemq.user=<ACTIVEMQ USERNAME>
spring.activemq.password=<ACTIVEMQ PASSWORD>
```

Następnie Dodaj do pliku właściwości specyficzne dla Service Bus `application.properties` .

```properties
azure.servicebus.connection-string=Endpoint=myEndpoint;SharedAccessKeyName=mySharedAccessKeyName;SharedAccessKey=mySharedAccessKey
```

##### <a name="replace-activemqconnectionfactory-with-servicebusjmsconnectionfactory"></a>Zamień `ActiveMQConnectionFactory` na `ServiceBusJmsConnectionFactory`

Następnym krokiem jest zamienienie wystąpienia `ActiveMQConnectionFactory` z `ServiceBusJmsConnectionFactory` .

> [!NOTE] 
> Rzeczywiste zmiany kodu są specyficzne dla aplikacji i sposobu zarządzania zależnościami, ale w poniższym przykładzie przedstawiono wskazówki dotyczące tego, co powinno być zmienione.
>

Wcześniej można było utworzyć wystąpienie obiektu w `ActiveMQConnectionFactory` następujący sposób:

```java

String BROKER_URL = "<URL of the hosted ActiveMQ broker>";
ConnectionFactory factory = new ActiveMQConnectionFactory(BROKER_URL);

Connection connection = factory.createConnection();
connection.start();

```

Teraz zmieniasz ten element, aby utworzyć wystąpienie obiektu w `ServiceBusJmsConnectionFactory` następujący sposób:

```java

ServiceBusJmsConnectionFactorySettings settings = new ServiceBusJmsConnectionFactorySettings();
String SERVICE_BUS_CONNECTION_STRING = "<Service Bus Connection string>";

ConnectionFactory factory = new ServiceBusJmsConnectionFactory(SERVICE_BUS_CONNECTION_STRING, settings);

Connection connection = factory.createConnection();
connection.start();

```

## <a name="post-migration"></a>Po migracji

Po zmodyfikowaniu aplikacji w celu rozpoczęcia wysyłania i otrzymywania komunikatów z Service Bus należy sprawdzić, czy działa ona zgodnie z oczekiwaniami. Gdy to zrobisz, możesz kontynuować udoskonalanie i modernizację stosu aplikacji.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać bezproblemową integrację z usługą Service Bus, użyj programu [sprężynowego rozruchu Starter dla Azure Service Bus JMS](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-azure-service-bus) .

Aby dowiedzieć się więcej na temat Service Bus Messaging i JMS, zobacz:

* [Service Bus JMS](service-bus-java-how-to-use-jms-api-amqp.md)
* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)