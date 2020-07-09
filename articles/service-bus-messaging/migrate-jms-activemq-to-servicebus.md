---
title: Migrowanie aplikacji usługi wiadomości języka Java (JMS) z programu ActiveMQ do Azure Service Bus | Microsoft Docs
description: W tym artykule wyjaśniono, jak migrować istniejące aplikacje JMS, które współdziałają z aktywnymi metodami MQ w celu współpracy z Azure Service Bus.
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
ms.openlocfilehash: 3da4f693f4cfec47c5456a0c5998f58f5fe02949
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86122415"
---
# <a name="migrate-existing-java-message-service-jms-20-applications-from-active-mq-to-azure-service-bus"></a>Migruj istniejące aplikacje JMS (Java Message Service) 2,0 z aktywnego MQ do Azure Service Bus

Azure Service Bus obsługuje obciążenia Java/J2EE i sprężyny korzystające z interfejsu API usługi wiadomości języka Java (JMS) 2,0 za pośrednictwem protokołu Advanced Message Queueing Protocol (AMQP).

W tym przewodniku opisano, co należy wiedzieć, gdy chcesz zmodyfikować istniejącą aplikację w języku Java (JMS) 2,0, która współdziała z usługą JMS Broker (w przypadku oprogramowania Apache ActiveMQ lub Amazon MQ) w celu interakcji z Azure Service Bus.

## <a name="before-you-start"></a>Przed rozpoczęciem

### <a name="differences-between-azure-service-bus-and-apache-activemq"></a>Różnice między Azure Service Bus i Apache ActiveMQ

Azure Service Bus i Apache ActiveMQ to brokerzy komunikatów, którzy działają jako dostawcy JMS dla aplikacji klienckich do wysyłania komunikatów do i odbierania komunikatów z programu. Umożliwiają one włączenie semantyki punkt-punkt z **kolejkami** i semantyką publikowania/subskrybowania z **tematami** i **subskrypcjami**. 

Nawet Dlatego istnieją pewne różnice między nimi.

| Kategoria | Aktywne MQ | Azure Service Bus |
| --- | --- | --- |
| Obsługa warstw aplikacji | Monolitu klastrowane | Dwie warstwy <br> (Brama + zaplecze) |
| Obsługa protokołów | <ul> <li>AMQP</li> <li> STOMP </li> <li> OpenWire </li> </ul> | AMQP |
| Tryb aprowizacji | <ul> <li> IaaS (lokalna) </li> <li> Amazon MQ (zarządzana PaaS) </li> | Zarządzane PaaS |
| Rozmiar komunikatu | Klient konfigurowalny | 1 MB (warstwa Premium) |
| Wysoka dostępność | Zarządzane przez klienta | Zarządzane przez platformę |
| Odzyskiwanie po awarii | Zarządzane przez klienta | Zarządzane przez platformę | 

### <a name="current-supported-and-unsupported-features"></a>Bieżące obsługiwane i nieobsługiwane funkcje

[!INCLUDE [service-bus-jms-features-list](../../includes/service-bus-jms-feature-list.md)]

### <a name="caveats"></a>Zastrzeżenia

Dwuwarstwowy charakter Azure Service Bus zapewnia różne możliwości ciągłości działania firmy (wysoka dostępność i odzyskiwanie po awarii). Należy jednak wziąć pod uwagę podczas korzystania z funkcji JMS.

#### <a name="service-upgrades"></a>Uaktualnienia usług

W przypadku uaktualnień i ponownych uruchomień usługi Service Bus, tymczasowe kolejki lub tematy zostaną usunięte.

Jeśli aplikacja jest wrażliwa na utratę danych w tymczasowych kolejkach lub tematach, zaleca się, aby **nie** używać kolejek tymczasowych ani tematów i używać w zamian trwałych kolejek, tematów i subskrypcji.

#### <a name="data-migration"></a>Migracja danych

W ramach migrowania/modyfikowania aplikacji klienckich w celu współpracy z Azure Service Bus dane przechowywane w ActiveMQ nie zostaną zmigrowane do Service Bus.

Niestandardowa aplikacja może być wymagana w celu opróżnienia kolejek, tematów i subskrypcji ActiveMQ, a następnie powtórzenia komunikatów do kolejek, tematów i subskrypcji Service Bus ".

#### <a name="authentication-and-authorization"></a>Uwierzytelnianie i autoryzacja

Access Control oparte na rolach (RBAC) obsługiwane przez usługę Azure ActiveDirectory jest preferowanym mechanizmem uwierzytelniania dla Azure Service Bus.

Jednak ponieważ RBAC nie jest obecnie obsługiwany z powodu braku obsługi uwierzytelniania opartego na rolach przez Apache QPID JMS.

Na razie uwierzytelnianie jest obsługiwane tylko przy użyciu kluczy SAS.

## <a name="pre-migration"></a>Czynności przed migracją

### <a name="version-check"></a>Sprawdzanie wersji

Poniżej przedstawiono składniki wykorzystywane podczas zapisywania aplikacji JMS i obsługiwanych wersji. 

| Składniki | Wersja |
|---|---|
| Interfejs API usługi wiadomości Java (JMS) | 1,1 lub więcej |
| Protokół AMQP | 1.0 |

### <a name="ensure-that-amqp-ports-are-open"></a>Upewnij się, że porty AMQP są otwarte

Azure Service Bus obsługuje komunikację za pośrednictwem protokołu AMQP. W tym celu należy włączyć komunikację między portami 5671 (AMQP) i 443 (TCP). W zależności od tego, gdzie znajdują się aplikacje klienckie, może być potrzebny bilet pomocy technicznej, aby umożliwić komunikację za pośrednictwem tych portów.

> [!IMPORTANT]
> Azure Service Bus obsługuje **tylko** protokół AMQP 1,0.

### <a name="set-up-enterprise-configurations-vnet-firewall-private-endpoint-etc"></a>Konfigurowanie konfiguracji przedsiębiorstwa (Sieć wirtualna, Zapora, prywatny punkt końcowy itp.)

Azure Service Bus włącza różne funkcje zabezpieczeń i wysokiej dostępności dla przedsiębiorstw. Aby dowiedzieć się więcej o nich, Skorzystaj z poniższych linków do dokumentacji.

  * [Punkty końcowe usługi Virtual Network](service-bus-service-endpoints.md)
  * [Zapora](service-bus-ip-filtering.md)
  * [Szyfrowanie po stronie usługi z kluczem zarządzanym przez klienta (BYOK)](configure-customer-managed-key.md)
  * [Prywatne punkty końcowe](private-link-service.md)
  * [Uwierzytelnianie i autoryzacja](service-bus-authentication-and-authorization.md)

### <a name="monitoring-alerts-and-tracing"></a>Monitorowanie, alerty i śledzenie

Metryki są publikowane dla każdej przestrzeni nazw Service Bus na Azure Monitor i mogą być używane do generowania alertów i dynamicznego skalowania zasobów przyznanych do przestrzeni nazw.

Dowiedz się więcej o różnych metrykach i sposobach ich konfiguracji przy użyciu [metryk Service Bus w Azure monitor](service-bus-metrics-azure-monitor.md).

Ponadto Przeczytaj więcej na temat [śledzenia po stronie klienta dla operacji na danych](service-bus-end-to-end-tracing.md) oraz [rejestrowania operacyjnego/diagnostyki dla operacji zarządzania](service-bus-diagnostic-logs.md)

### <a name="metrics---newrelic"></a>Metryki — NewRelic

Poniżej znajduje się przydatny Przewodnik dotyczący metryk z mapy ActiveMQ, do której metryki w Azure Service Bus. Poniżej znajdują się odwołania do NewRelic.

  * [ActiveMQ/Amazon MQ NewRelic Metrics](https://docs.newrelic.com/docs/integrations/amazon-integrations/aws-integrations-list/aws-mq-integration)
  * [Azure Service Bus metryki NewRelic](https://docs.newrelic.com/docs/integrations/microsoft-azure-integrations/azure-integrations-list/azure-service-bus-monitoring-integration)

> [!NOTE]
> Obecnie NewRelic nie ma bezproblemowej integracji z usługą ActiveMQ bezpośrednio, ale mają dostępne metryki Amazon MQ.
> Ponieważ Amazon MQ pochodzi od ActiveMQ, Poniższy przewodnik mapuje metryki NewRelic z AmazonMQ na Azure Service Bus.
>

|Grupowanie metryk| Metryka AmazonMQ/Active MQ | Metryka Azure Service Bus |
|------------|---------------------------|--------------------------|
|Brokera|`CpuUtilization`|`CPUXNS`|
|Brokera|`MemoryUsage`|`WSXNS`|
|Brokera|`CurrentConnectionsCount`|`activeConnections`|
|Brokera|`EstablishedConnectionsCount`|`activeConnections` + `connectionsClosed`|
|Brokera|`InactiveDurableTopicSubscribersCount`|Korzystanie z metryk subskrypcji|
|Brokera|`TotalMessageCount`|Skorzystaj z poziomu kolejki/tematu/subskrypcji`activeMessages`|
|Kolejka/temat|`EnqueueCount`|`incomingMessages`|
|Kolejka/temat|`DequeueCount`|`outgoingMessages`|
|Kolejka|`QueueSize`|`sizeBytes`|



## <a name="migration"></a>Migracja

Aby przeprowadzić migrację istniejącej aplikacji JMS 2,0 do współpracy z Azure Service Bus, należy wykonać poniższe czynności.

### <a name="export-topology-from-activemq-and-create-the-entities-in-azure-service-bus-optional"></a>Eksportowanie topologii z ActiveMQ i tworzenie jednostek w Azure Service Bus (opcjonalnie)

Aby mieć pewność, że aplikacje klienckie mogą bezproblemowo łączyć się z Azure Service Bus, topologia, która obejmuje kolejki, tematy i subskrypcje — należy zmigrować z **Apache ActiveMQ** do **Azure Service Bus**.

> [!NOTE]
> W przypadku aplikacji Java Message Service (JMS) Tworzenie kolejek, tematów i subskrypcji jest operacją w czasie wykonywania. Większość dostawców usługi wiadomości języka Java (JMS) (brokerów komunikatów) oferuje funkcje tworzenia *kolejek*, *tematów* i *subskrypcji* w czasie wykonywania.
>
> W związku z tym powyższy krok jest opcjonalny.
>
> Aby upewnić się, że aplikacja ma uprawnienia do tworzenia topologii w środowisku uruchomieniowym, upewnij się, że używane są parametry połączenia z uprawnieniami do zarządzania przy użyciu ***sygnatury dostępu współdzielonego*** .

Wymagana czynność 
  * Korzystanie z [narzędzi wiersza polecenia ActiveMQ](https://activemq.apache.org/activemq-command-line-tools-reference) do wyeksportowania topologii
  * Utwórz ponownie tę samą topologię przy użyciu [szablonu Azure Resource Manager](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)
  * Wykonaj szablon Azure Resource Manager.


### <a name="import-the-maven-dependency-for-service-bus-jms-implementation"></a>Zaimportuj zależność Maven dla implementacji JMS Service Bus

Aby zapewnić bezproblemowe połączenie z Azure Service Bus, należy dodać pakiet ***Azure-ServiceBus-JMS*** jako zależność do pliku Maven, `pom.xml` jak pokazano poniżej.

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

Ta część jest niestandardowa dla serwera aplikacji, który obsługuje aplikacje klienckie łączące się z aktywnym elementem MQ.

#### <a name="tomcat"></a>Tomcat

W tym miejscu zaczynamy od konfiguracji specyficznej dla aktywnego MQ, jak pokazano w `/META-INF/context.xml` pliku.

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

które można dostosować w następujący sposób, aby wskazywały Azure Service Bus

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

##### <a name="update-applicationproperties-file"></a>`application.properties`Plik aktualizacji

Jeśli używasz aplikacji rozruchu sprężynowego do łączenia się z usługą ActiveMQ.

W tym miejscu cel polega na ***usunięciu*** określonych właściwości ActiveMQ z `application.properties` pliku.

```properties
spring.activemq.broker-url=<ACTIVEMQ BROKER URL>
spring.activemq.user=<ACTIVEMQ USERNAME>
spring.activemq.password=<ACTIVEMQ PASSWORD>
```

Następnie ***Dodaj*** do pliku Service Bus specyficzne właściwości `application.properties` .

```properties
azure.servicebus.connection-string=Endpoint=myEndpoint;SharedAccessKeyName=mySharedAccessKeyName;SharedAccessKey=mySharedAccessKey
```

##### <a name="replace-the-activemqconnectionfactory-with-servicebusjmsconnectionfactory"></a>Zastąp ActiveMQConnectionFactory z ServiceBusJmsConnectionFactory

Następnym krokiem jest zamienienie wystąpienia elementu ActiveMQConnectionFactory z ServiceBusJmsConnectionFactory.

> [!NOTE] 
> Rzeczywiste zmiany kodu są specyficzne dla aplikacji i sposobu zarządzania zależnościami, ale w poniższym przykładzie przedstawiono wskazówki dotyczące ***tego, co*** powinno być zmienione.
>

Wcześniej można było utworzyć wystąpienie obiektu ActiveMQConnectionFactory, jak pokazano poniżej.

```java

String BROKER_URL = "<URL of the hosted ActiveMQ broker>";
ConnectionFactory factory = new ActiveMQConnectionFactory(BROKER_URL);

Connection connection = factory.createConnection();
connection.start();

```

Ta wartość zostanie zmieniona na utworzenie wystąpienia obiektu ServiceBusJmsConnectionFactory

```java

ServiceBusJmsConnectionFactorySettings settings = new ServiceBusJmsConnectionFactorySettings();
String SERVICE_BUS_CONNECTION_STRING = "<Service Bus Connection string>";

ConnectionFactory factory = new ServiceBusJmsConnectionFactory(SERVICE_BUS_CONNECTION_STRING, settings);

Connection connection = factory.createConnection();
connection.start();

```

## <a name="post-migration"></a>Po migracji

Po zmodyfikowaniu aplikacji w celu rozpoczęcia wysyłania i otrzymywania komunikatów z Azure Service Bus należy sprawdzić, czy działa ona zgodnie z oczekiwaniami. Po wykonaniu tej czynności możesz kontynuować udoskonalanie i modernizację stosu aplikacji.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać bezproblemową integrację z Azure Service Bus, Skorzystaj z usługi [sprężynowego rozruchu Starter dla Azure Service Bus JMS](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-azure-service-bus) .

Aby dowiedzieć się więcej na temat Service Bus komunikatów i usługi wiadomości języka Java (JMS), zobacz następujące tematy:

* [Service Bus JMS](service-bus-java-how-to-use-jms-api-amqp.md)
* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
