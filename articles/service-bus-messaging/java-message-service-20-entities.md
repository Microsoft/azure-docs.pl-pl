---
title: Azure Service Bus Messaging — jednostki usługi komunikatów Java (wersja zapoznawcza)
description: Ten artykuł zawiera omówienie Azure Service Bus jednostek komunikatów dostępnych za pomocą interfejsu API usługi komunikatów języka Java.
ms.topic: article
ms.date: 07/20/2020
ms.openlocfilehash: 1a7fe3d6355146ccf0fce50266a6f3b8da5231b3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87801804"
---
# <a name="java-message-service-jms-20-entities-preview"></a>Jednostki usługi wiadomości Java (JMS) 2,0 (wersja zapoznawcza)

Aplikacje klienckie łączące się z Azure Service Bus Premium i wykorzystujące [bibliotekę Azure Service Bus JMS](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) mogą korzystać z poniższych jednostek.

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

Gdy aplikacja kliencka wymaga tymczasowej jednostki, która istnieje w okresie istnienia aplikacji, może używać kolejek tymczasowych. Są one wykorzystywane w wzorcu [żądanie-odpowiedź](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) .

Aby utworzyć tymczasową kolejkę, Użyj poniższych metod w `JMSContext` klasie-

```java
TemporaryQueue createTemporaryQueue()
```

## <a name="temporary-topics"></a>Tematy tymczasowe

Podobnie jak w przypadku kolejek tymczasowych, tematy tymczasowe są dostępne, aby umożliwić publikowanie/subskrybowanie przez tymczasową jednostkę, która istnieje w okresie istnienia aplikacji.

Aby utworzyć temat tymczasowy, Użyj poniższych metod w `JMSContext` klasie —

```java
TemporaryTopic createTemporaryTopic()
```

## <a name="java-message-service-jms-subscriptions"></a>Subskrypcje usługi wiadomości Java (JMS)

Chociaż są one semantycznie podobne do [subskrypcji](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) (tj. istnieją w temacie i umożliwiają włączenie semantyki publikowania/subskrybowania), Specyfikacja usługi wiadomości języka Java wprowadza koncepcje dotyczące **współużytkowanych**, **nieudostępnianych**, **trwałych** i **nietrwałych** atrybutów dla danej subskrypcji.

> [!NOTE]
> Poniższe subskrypcje są dostępne w Azure Service Bus warstwie Premium na potrzeby wersji zapoznawczej dla aplikacji klienckich łączących się z Azure Service Bus przy użyciu [biblioteki Azure Service Bus JMS](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms).
>
> W publicznej wersji zapoznawczej te subskrypcje nie mogą być tworzone przy użyciu Azure Portal.
>

### <a name="shared-durable-subscriptions"></a>Udostępnione trwałe subskrypcje

Udostępniona trwała subskrypcja jest używana, gdy wszystkie komunikaty opublikowane w temacie są odbierane i przetwarzane przez aplikację, niezależnie od tego, czy aplikacja aktywnie zużywa się z subskrypcji przez cały czas.

Ponieważ jest to udostępniona subskrypcja, każda aplikacja, która została uwierzytelniona do odbierania z Service Bus może otrzymywać z subskrypcji.

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

Podobnie jak w przypadku udostępnionej subskrypcji trwałej, nieudostępniona subskrypcja jest używana, gdy wszystkie komunikaty opublikowane w temacie są odbierane i przetwarzane przez aplikację, niezależnie od tego, czy aplikacja aktywnie zużywa się z subskrypcji przez cały czas.

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

Nieudostępniana subskrypcja nietrwała jest używana, gdy aplikacja kliencka musi odebrać i przetworzyć komunikat z subskrypcji, tylko dopóki nie będzie aktywnie z niego korzystać. W tej subskrypcji może istnieć tylko jeden odbiorca, czyli klient, który utworzył subskrypcję.

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

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji i przykłady używania Service Bus Messaging, zobacz następujące tematy zaawansowane:

* [Przegląd komunikatów Service Bus](service-bus-messaging-overview.md)
* [Korzystanie z interfejsu API usługi wiadomości języka Java 2,0 w usłudze Azure Service Bus Premium (wersja zapoznawcza)](how-to-use-java-message-service-20.md)



