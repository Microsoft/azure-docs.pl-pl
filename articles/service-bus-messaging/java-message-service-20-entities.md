---
title: Azure Service Bus Messaging — jednostki usługi komunikatów języka Java
description: Ten artykuł zawiera omówienie Azure Service Bus jednostek komunikatów dostępnych za pomocą interfejsu API usługi komunikatów języka Java.
ms.topic: article
ms.date: 07/20/2020
ms.openlocfilehash: ee4e0124dced16b86d5292c647e129aa87645f22
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100652585"
---
# <a name="java-message-service-jms-20-entities"></a>Jednostki usługi wiadomości Java (JMS) 2,0

Aplikacje klienckie łączące się z Azure Service Bus Premium i przy użyciu [biblioteki Azure Service Bus JMS](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) mogą używać poniższych jednostek.

## <a name="queues"></a>Kolejki

Kolejki w JMS są semantycznie porównywalne z tradycyjnymi [kolejkami Service Bus](service-bus-queues-topics-subscriptions.md#queues).

Aby utworzyć kolejkę, Użyj poniższych metod w `JMSContext` klasie-

```java
Queue createQueue(String queueName)
```

## <a name="topics"></a>Tematy

Tematy w programie JMS są semantycznie porównywalne z tradycyjnymi [tematami Service Bus](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions).

Aby utworzyć temat, Użyj poniższych metod w `JMSContext` klasie-

```java
Topic createTopic(String topicName)
```

## <a name="temporary-queues"></a>Kolejki tymczasowe

Jeśli aplikacja kliencka wymaga tymczasowej jednostki, która istnieje w okresie istnienia aplikacji, może używać kolejek tymczasowych. Te jednostki są używane w wzorcu [żądanie-odpowiedź](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) .

Aby utworzyć tymczasową kolejkę, Użyj poniższych metod w `JMSContext` klasie-

```java
TemporaryQueue createTemporaryQueue()
```

## <a name="temporary-topics"></a>Tematy tymczasowe

Podobnie jak w przypadku kolejek tymczasowych, tematy tymczasowe są dostępne, aby umożliwić publikowanie/subskrybowanie przez tymczasową jednostkę, która istnieje w okresie istnienia aplikacji.

Aby utworzyć temat tymczasowy, Użyj poniższych metod w `JMSContext` klasie-

```java
TemporaryTopic createTemporaryTopic()
```

## <a name="java-message-service-jms-subscriptions"></a>Subskrypcje usługi wiadomości Java (JMS)

Chociaż są one semantycznie podobne do [subskrypcji](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) (które istnieją w temacie i umożliwiają włączenie semantyki publikowania/subskrybowania), Specyfikacja usługi wiadomości Java wprowadza koncepcje dotyczące **współużytkowanych**, **nieudostępnianych**, * * trwałych i **nietrwałych** atrybutów dla danej subskrypcji.

> [!NOTE]
> Poniższe subskrypcje są dostępne w Azure Service Bus warstwie Premium dla aplikacji klienckich łączących się z Azure Service Bus przy użyciu [biblioteki Azure Service Bus JMS](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms).
>
> Za pomocą Azure Portal można tworzyć tylko trwałe subskrypcje.
>

### <a name="shared-durable-subscriptions"></a>Udostępnione trwałe subskrypcje

Udostępniona trwała subskrypcja jest używana, gdy wszystkie komunikaty opublikowane w temacie są odbierane i przetwarzane przez aplikację, niezależnie od tego, czy aplikacja aktywnie zużywa się z subskrypcji przez cały czas.

Wszystkie aplikacje, które zostały uwierzytelnione do odbierania z Service Bus mogą otrzymywać z udostępnionej subskrypcji trwałej.

Aby utworzyć udostępnioną subskrypcję trwałą, Użyj poniższych metod `JMSContext` klasy-

```java
JMSConsumer createSharedDurableConsumer(Topic topic, String name)

JMSConsumer createSharedDurableConsumer(Topic topic, String name, String messageSelector)
```

Udostępniona trwała subskrypcja nadal istnieje, chyba że zostanie usunięta przy użyciu `unsubscribe` metody `JMSContext` klasy.

```java
void unsubscribe(String name)
```

### <a name="unshared-durable-subscriptions"></a>Nieudostępniane trwałe subskrypcje

Podobnie jak w przypadku udostępnionej subskrypcji trwałej, nieudostępniona subskrypcja jest używana, gdy wszystkie komunikaty opublikowane w temacie są odbierane i przetwarzane przez aplikację, niezależnie od tego, czy aplikacja aktywnie zużywa się z subskrypcji.

Jednak ponieważ jest to nieudostępniona subskrypcja, z niej można odbierać tylko aplikację, która utworzyła subskrypcję.

Aby utworzyć nieudostępnioną subskrypcję trwałą, Użyj poniższych metod z `JMSContext` klasy- 

```java
JMSConsumer createDurableConsumer(Topic topic, String name)

JMSConsumer createDurableConsumer(Topic topic, String name, String messageSelector, boolean noLocal)
```

> [!NOTE]
> Ta `noLocal` Funkcja jest obecnie nieobsługiwana i ignorowana.
>

Nieudostępniona subskrypcja trwała nadal istnieje, chyba że zostanie usunięta przy użyciu `unsubscribe` metody `JMSContext` klasy.

```java
void unsubscribe(String name)
```

### <a name="shared-non-durable-subscriptions"></a>Udostępnione nietrwałe subskrypcje

Udostępniona nietrwała subskrypcja jest używana, gdy wiele aplikacji klienckich musi odbierać i przetwarzać komunikaty z pojedynczej subskrypcji, tylko do momentu ich aktywnego wykorzystywania/otrzymywania.

Ponieważ subskrypcja nie jest trwała, nie jest utrwalona. Wiadomości nie są odbierane przez tę subskrypcję, jeśli nie ma aktywnych odbiorców.

Aby utworzyć udostępnioną, nietrwałą subskrypcję, Utwórz `JmsConsumer` tak jak pokazano w poniższych metodach z `JMSContext` klasy-

```java
JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName)

JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName, String messageSelector)
```

Udostępniona nietrwała subskrypcja będzie nadal istnieć, dopóki nie otrzymasz od niej aktywnych odbiorców.

### <a name="unshared-non-durable-subscriptions"></a>Nieudostępniane subskrypcje nietrwałe

Nieudostępniana subskrypcja nietrwała jest używana, gdy aplikacja kliencka musi odebrać i przetworzyć komunikat z subskrypcji, tylko dopóki nie będzie aktywnie z niego korzystać. W tej subskrypcji może istnieć tylko jeden użytkownik, czyli klient, który utworzył subskrypcję.

Ponieważ subskrypcja nie jest trwała, nie jest utrwalona. Wiadomości nie są odbierane przez tę subskrypcję, jeśli nie ma żadnych aktywnych użytkowników.

Aby utworzyć nieudostępnioną subskrypcję nietrwałą, Utwórz `JMSConsumer` tak jak pokazano w poniższych metodach z `JMSContext` klasy-

```java
JMSConsumer createConsumer(Destination destination)

JMSConsumer createConsumer(Destination destination, String messageSelector)

JMSConsumer createConsumer(Destination destination, String messageSelector, boolean noLocal)
```

> [!NOTE]
> Ta `noLocal` Funkcja jest obecnie nieobsługiwana i ignorowana.
>

Nieudostępniona subskrypcja nietrwała nadal istnieje do momentu odebrania od niej aktywnego użytkownika.

### <a name="message-selectors"></a>Selektory komunikatów

Podobnie jak **filtry i akcje** istnieją dla zwykłych subskrypcji Service Bus, dla subskrypcji JMS istnieją **selektory komunikatów** .

Selektory komunikatów można skonfigurować dla każdej subskrypcji JMS i istnieć jako warunek filtru we właściwościach nagłówka komunikatu. Dostarczane są tylko komunikaty z właściwościami nagłówka, które pasują do wyrażenia selektora komunikatów. Wartość null lub pusty ciąg wskazuje, że nie istnieje selektor komunikatów dla subskrypcji/konsumenta JMS.

## <a name="additional-concepts-for-java-message-service-jms-20-subscriptions"></a>Dodatkowe koncepcje dotyczące subskrypcji 2,0 usługi wiadomości w języku Java (JMS)

### <a name="client-scoping"></a>Określanie zakresu klientów

Subskrypcje, jak określono w interfejsie API 2,0 usługi wiadomości Java (JMS), mogą lub nie mogą być *ograniczone do określonych aplikacji klienckich/s* (określonych z odpowiednią `clientId` ).

Gdy subskrypcja zostanie objęta zakresem, **można uzyskać do niej dostęp tylko** z aplikacji klienckich, które mają ten sam identyfikator klienta. 

Wszelkie próby uzyskania dostępu do subskrypcji w zakresie określonego identyfikatora klienta (Powiedz clientId1) z aplikacji o innym IDENTYFIKATORze klienta (Powiedz clientId2) spowodują utworzenie innej subskrypcji w zakresie innego identyfikatora klienta (clientId2).

> [!NOTE]
> Identyfikator klienta może mieć wartość null lub być pusty, ale musi być zgodny z IDENTYFIKATORem klienta ustawionym w aplikacji klienckiej JMS. W perspektywie Azure Service Bus identyfikator klienta o wartości null i pusty identyfikator klienta mają takie samo zachowanie.
>
> Jeśli identyfikator klienta ma wartość null lub jest pusty, jest dostępny tylko dla aplikacji klienckich, których identyfikator klienta ma również wartość null lub jest pusty.
>

### <a name="shareability"></a>Wymagających środowisku współdzielonym

**Udostępnione** subskrypcje zezwalają wielu klientom/konsumentom (czyli JMSConsumer obiektów) na odbieranie komunikatów z nich.

>[!NOTE]
> Udostępnione subskrypcje zakresu do określonego identyfikatora klienta nadal są dostępne dla wielu klientów/odbiorców (tj. obiektów JMSConsumer), ale każda aplikacja kliencka musi mieć ten sam identyfikator klienta.
>
 

**Nieudostępnione** subskrypcje zezwalają na odbieranie z nich komunikatów przy użyciu tylko jednego klienta/konsumenta (czyli obiektu JMSConsumer). Jeśli `JMSConsumer` jest tworzony w nieudostępnionej subskrypcji, gdy ma już aktywne `JMSConsumer` nasłuchiwanie na komunikatach, `JMSException` jest zgłaszany.


### <a name="durability"></a>Trwałość

**Trwałe** subskrypcje są utrwalane i kontynuują zbieranie komunikatów z tematu, niezależnie od tego, czy aplikacja ( `JMSConsumer` ) zużywa z niej komunikaty.

**Nietrwałe** subskrypcje nie są utrwalane i zbierają komunikaty z tematu tak długo, jak aplikacja ( `JMSConsumer` ) zużywa z niej komunikaty. 

## <a name="representation-of-client-scoped-subscriptions"></a>Reprezentacja subskrypcji z zakresem klienta

Mając na względzie, że subskrypcje z zakresu klienta (JMS) muszą współistnieć z istniejącymi [subskrypcjami](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions), sposób, w jaki subskrypcje w zakresie klienta (JMS) są reprezentowane zgodnie z poniższym formatem.

   * **\<SUBSCRIPTION-NAME\>**$**\<CLIENT-ID\>**$**D** (w przypadku subskrypcji trwałych)
   * **\<SUBSCRIPTION-NAME\>**$**\<CLIENT-ID\>**$**ND** (w przypadku subskrypcji nietrwałych)

Oto **$** ogranicznik.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji i przykłady używania Service Bus Messaging, zobacz następujące tematy zaawansowane:

* [Przegląd komunikatów Service Bus](service-bus-messaging-overview.md)
* [Używanie interfejsu API usługi wiadomości języka Java 2,0 z usługą Azure Service Bus Premium](how-to-use-java-message-service-20.md)



