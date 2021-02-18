---
title: Przewodnik dla deweloperów Azure Service Bus JMS 2,0
description: Jak komunikować się z Azure Service Bus przy użyciu interfejsu API usługi wiadomości języka Java (JMS) 2,0
ms.topic: article
ms.date: 01/17/2021
ms.openlocfilehash: 6c535b12906b6d9385029896dc5d0caf85d3399a
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654398"
---
# <a name="azure-service-bus-jms-20-developer-guide"></a>Przewodnik dla deweloperów Azure Service Bus JMS 2,0

Ten przewodnik zawiera szczegółowe informacje ułatwiające pomyślne pomyślną komunikację z Azure Service Bus przy użyciu interfejsu API 2,0 w usłudze wiadomości Java (JMS).

Jeśli jesteś deweloperem języka Java, jeśli jesteś nowym do Azure Service Bus, rozważ zapoznanie się z poniższymi artykułami.

| Wprowadzenie | Pojęcia |
|----------------|-------|
| <ul> <li> [Co to jest Azure Service Bus](service-bus-messaging-overview.md) </li> <li> [Kolejki, tematy i subskrypcje](service-bus-queues-topics-subscriptions.md) </li> </ul> | <ul> <li> [Azure Service Bus — warstwa Premium](service-bus-premium-messaging.md) </li> </ul> |

## <a name="java-message-service-jms-programming-model"></a>Model programowania usługi wiadomości Java (JMS)

Model programowania interfejsu API usługi wiadomości Java jest przedstawiony poniżej — 

> [!NOTE]
>
>**Azure Service Bus warstwa Premium** obsługuje JMS 1,1 i JMS 2,0.
> <br> <br>
> Warstwa **standardowa Azure Service Bus** obsługuje ograniczoną funkcjonalność JMS 1,1. Aby uzyskać więcej informacji, zapoznaj się z tą [dokumentacją](service-bus-java-how-to-use-jms-api-amqp.md).
>

# <a name="jms-20-programming-model"></a>[Model programowania w programie JMS 2,0](#tab/JMS-20)

:::image type="content" source="./media/jms-developer-guide/java-message-service-20-programming-model.png"alt-text="Diagram przedstawiający model programowania JMS 2,0."border="false":::

# <a name="jms-11-programming-model"></a>[Model programowania w programie JMS 1,1](#tab/JMS-11)

:::image type="content" source="./media/jms-developer-guide/java-message-service-11-programming-model.png"alt-text="Diagram przedstawiający model programowania JMS 1,1."border="false":::

---

## <a name="jms---building-blocks"></a>JMS bloki konstrukcyjne

Poniższe bloki konstrukcyjne są dostępne do komunikowania się z aplikacją JMS.

> [!NOTE]
> Poniższy przewodnik został dostosowany z [samouczka Oracle Java EE 6 dla usługi wiadomości Java (JMS)](https://docs.oracle.com/javaee/6/tutorial/doc/bnceh.html)
>
> W celu lepszego zrozumienia usługi wiadomości języka Java (JMS) zaleca się zapoznawanie się z tym samouczkiem.
>

### <a name="connection-factory"></a>Fabryka połączeń
Obiekt fabryki połączeń jest używany przez klienta do nawiązywania połączenia z dostawcą JMS. Fabryka połączeń hermetyzuje zestaw parametrów konfiguracji połączenia, które są zdefiniowane przez administratora.

Każda fabryka połączeń jest wystąpieniem `ConnectionFactory` `QueueConnectionFactory` lub `TopicConnectionFactory` interfejsem.

Aby uprościć łączenie się z Azure Service Bus, te interfejsy są implementowane przez `ServiceBusJmsConnectionFactory` program, `ServiceBusJmsQueueConnectionFactory` a `ServiceBusJmsTopicConnectionFactory` odpowiednio. Fabrykę połączeń można utworzyć przy użyciu poniższych parametrów — 
   * Parametry połączenia — parametry połączenia dla przestrzeni nazw Azure Service Bus warstwy Premium.
   * Zbiór właściwości ServiceBusJmsConnectionFactorySettings zawierający
      * connectionIdleTimeoutMS — limit czasu połączenia (w milisekundach).
      * traceFrames — Flaga logiczna służąca do zbierania ramek śledzenia AMQP na potrzeby debugowania.
      * *inne parametry konfiguracji*

Fabrykę można utworzyć w następujący sposób. Parametry połączenia są wymaganym parametrem, ale dodatkowe właściwości są opcjonalne.

```java
ConnectionFactory factory = new ServiceBusJmsConnectionFactory(SERVICE_BUS_CONNECTION_STRING, null);
```

> [!IMPORTANT]
> Aplikacje Java korzystające z interfejsu API JMS 2,0 muszą łączyć się z Azure Service Bus wyłącznie przy użyciu parametrów połączenia. Obecnie uwierzytelnianie dla klientów JMS jest obsługiwane tylko przy użyciu parametrów połączenia.
>
> Uwierzytelnianie za pomocą usługi Azure Active Directory (AAD) nie jest obecnie obsługiwane.
>

### <a name="jms-destination"></a>Miejsce docelowe JMS

Miejsce docelowe jest obiektem, którego klient używa do określenia elementu docelowego wysyłanych komunikatów i źródła komunikatów, które wykorzysta.

Miejsca docelowe są mapowane na jednostki w kolejkach Azure Service Bus (w ramach scenariuszy punkt-punkt) i tematów (w scenariuszach pub-Sub).

### <a name="connections"></a>Połączenia

Połączenie hermetyzuje połączenie wirtualne z dostawcą JMS. Dzięki Azure Service Bus reprezentuje połączenie stanowe między aplikacją i Azure Service Bus przez AMQP.

Połączenie jest tworzone na podstawie fabryki połączeń, jak pokazano poniżej.

```java
Connection connection = factory.createConnection();
```

### <a name="sessions"></a>Sesje

Sesja jest kontekstem jednowątkowym na potrzeby tworzenia i zużywania komunikatów. Można go wykorzystać do tworzenia komunikatów, producentów komunikatów i odbiorców, ale również kontekstu transakcyjnego, aby umożliwić grupowanie wysyłanych i odbieranych przez niepodzielną jednostkę pracy.

Sesję można utworzyć z obiektu Connection, jak pokazano poniżej.

```java
Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
```

#### <a name="session-modes"></a>Tryby sesji

Sesję można utworzyć przy użyciu dowolnego z poniższych trybów.

| Tryby sesji | Zachowanie |
| ----------------- | -------- |
|**Session.AUTO_ACKNOWLEDGE** | Sesja automatycznie potwierdza odebranie komunikatu przez klienta w przypadku pomyślnego zwrócenia przez nią wywołania lub kiedy odbiornik komunikatów został wywołany w celu przetworzenia komunikatów pomyślnie zwraca.|
|**Session.CLIENT_ACKNOWLEDGE** |Klient potwierdza użyty komunikat, wywołując metodę potwierdzenia komunikatu.|
|**Session.DUPS_OK_ACKNOWLEDGE**|Ten tryb potwierdzania powoduje, że sesja opóźnieniem potwierdzenie dostarczania komunikatów.| 
|**Session.SESSION_TRANSACTED**|Ta wartość może zostać przeniesiona jako argument do metody SessionMode (int SessionMode) obiektu Connection, aby określić, że sesja powinna używać transakcji lokalnej.| 

Gdy tryb sesji nie zostanie określony, **Session.AUTO_ACKNOWLEDGE** jest domyślnie wybrany.

### <a name="jmscontext"></a>JMSContext

> [!NOTE]
> JMSContext jest definiowana jako część specyfikacji JMS 2,0.
>

JMSContext łączy funkcje udostępnione przez obiekt połączenia i sesji. Można ją utworzyć z poziomu obiektu fabryki połączeń.

```java
JMSContext context = connectionFactory.createContext();
```

#### <a name="jmscontext-modes"></a>Tryby JMSContext

Podobnie jak w przypadku obiektu **Session** JMSContext można utworzyć przy użyciu tych samych potwierdzonych trybów, jak wspomniano w [trybach sesji](#session-modes).

```java
JMSContext context = connectionFactory.createContext(JMSContext.AUTO_ACKNOWLEDGE);
```

Gdy tryb nie zostanie określony, **JMSContext.AUTO_ACKNOWLEDGE** jest domyślnie wybrany.

### <a name="jms-message-producers"></a>JMS producenci komunikatów

Producent komunikatu jest obiektem, który jest tworzony przy użyciu JMSContext lub sesji i służy do wysyłania komunikatów do miejsca docelowego.

Można ją utworzyć jako obiekt autonomiczny, jak pokazano poniżej — 

```java
JMSProducer producer = context.createProducer();
```

lub utworzone w czasie wykonywania, gdy wiadomość jest wymagana do wysłania.

```java
context.createProducer().send(destination, message);
```

### <a name="jms-message-consumers"></a>Odbiorcy komunikatów JMS

Odbiorca wiadomości jest obiektem, który jest tworzony przez JMSContext lub sesję i służy do otrzymywania komunikatów wysyłanych do miejsca docelowego. Można ją utworzyć, jak pokazano poniżej —

```java
JMSConsumer consumer = context.createConsumer(dest);
```

#### <a name="synchronous-receives-via-receive-method"></a>Odebrane synchroniczne za pośrednictwem metody Receive ()

Odbiorca wiadomości umożliwia synchroniczną metodę odbierania wiadomości z lokalizacji docelowej za pośrednictwem `receive()` metody.

Jeśli nie określono argumentów/limitu czasu lub określono limit czasu dla "0", wówczas odbiorca jest blokowany w nieskończoność, chyba że zostanie wyświetlony komunikat lub połączenie zostanie zerwane (w zależności od tego, co jest wcześniejsze).

```java
Message m = consumer.receive();
Message m = consumer.receive(0);
```

Po podaniu argumentu dodatniego o wartości innej niż zero bloki odbiorców do momentu wygaśnięcia tego czasomierza.

```java
Message m = consumer.receive(1000); // time out after one second.
```

#### <a name="asynchronous-receives-with-jms-message-listeners"></a>Asynchroniczne odbieranie przy użyciu detektorów komunikatów JMS

Odbiornik komunikatów jest obiektem, który jest używany do asynchronicznej obsługi komunikatów w miejscu docelowym. Implementuje interfejs, `MessageListener` który zawiera metodę, `onMessage` w której musi się znajdować określona logika biznesowa.

Obiekt odbiornika komunikatów musi być skonkretyzowany i zarejestrowany dla określonego odbiorcy komunikatu przy użyciu `setMessageListener` metody.

```java
Listener myListener = new Listener();
consumer.setMessageListener(myListener);
```

### <a name="consuming-from-topics"></a>Korzystanie z tematów

[Odbiorcy komunikatów JMS](#jms-message-consumers) są tworzone względem [lokalizacji docelowej](#jms-destination) , która może być kolejką lub tematem.

Użytkownicy w kolejkach są po prostu obiektami po stronie klienta, które znajdują się w kontekście sesji (i połączenia) między aplikacją kliencką a Azure Service Bus.

Klienci z tematami mają jednak 2 części — 
   * **Obiekt po stronie klienta** , który znajduje się w kontekście sesji (lub JMSContext), i
   * **Subskrypcja** , która jest jednostką na Azure Service Bus.

Subskrypcje są udokumentowane w [tym miejscu](java-message-service-20-entities.md#java-message-service-jms-subscriptions) i mogą być następujące — 
   * Udostępnione trwałe subskrypcje
   * Udostępnione nietrwałe subskrypcje
   * Nieudostępniane trwałe subskrypcje
   * Nieudostępniane subskrypcje nietrwałe

### <a name="jms-queue-browsers"></a>Przeglądarki kolejki JMS

Interfejs API JMS udostępnia `QueueBrowser` obiekt umożliwiający aplikacji przeglądanie komunikatów w kolejce i wyświetlanie wartości nagłówka dla każdego komunikatu.

Przeglądarkę kolejek można utworzyć przy użyciu JMSContext, jak pokazano poniżej.

```java
QueueBrowser browser = context.createBrowser(queue);
```

> [!NOTE]
> Interfejs API JMS nie udostępnia interfejsu API do przeglądania tematu.
>
> Dzieje się tak, ponieważ sam temat nie przechowuje komunikatów. Gdy tylko wiadomość zostanie wysłana do tematu, zostanie ona przekazana do odpowiednich subskrypcji.
>

### <a name="jms-message-selectors"></a>Selektory komunikatów JMS

Selektory komunikatów mogą być używane przez aplikacje odbierające do filtrowania odbieranych komunikatów. W przypadku selektorów komunikatów aplikacja otrzymująca odciąża zadania filtrowania komunikatów do dostawcy JMS (w tym przypadku Azure Service Bus), zamiast korzystać z tej odpowiedzialności.

Selektorów można używać podczas tworzenia dowolnego z poniższych odbiorców — 
   * Udostępniona trwała subskrypcja
   * Nieudostępniona subskrypcja trwała
   * Udostępniona nietrwała subskrypcja
   * Nieudostępniona subskrypcja nietrwała
   * Przeglądarka kolejek


## <a name="summary"></a>Podsumowanie

W tym przewodniku dla deweloperów przedstawiono sposób, w jaki aplikacje klienckie Java korzystające z usługi wiadomości Java (JMS) mogą łączyć się z Azure Service Bus.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat Azure Service Bus i szczegółów dotyczących jednostek usługi wiadomości języka Java (JMS), zapoznaj się z poniższymi linkami — 
* [Service Bus — kolejki, tematy i subskrypcje](service-bus-queues-topics-subscriptions.md)
* [Service Bus — jednostki usługi komunikatów języka Java](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities)
* [Obsługa AMQP 1,0 w Azure Service Bus](service-bus-amqp-overview.md)
* [Podręcznik dewelopera Service Bus AMQP 1,0](service-bus-amqp-dotnet.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Interfejs API usługi wiadomości Java (dokument zewnętrzny Oracle)](https://docs.oracle.com/javaee/7/api/javax/jms/package-summary.html)
* [Dowiedz się, jak przeprowadzić migrację z ActiveMQ do Service Bus](migrate-jms-activemq-to-servicebus.md)
