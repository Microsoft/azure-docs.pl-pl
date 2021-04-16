---
title: Używanie Azure Service Bus kolejek w języku Java
description: Z tego samouczka dowiesz się, jak tworzyć aplikacje Java w celu wysyłania komunikatów do i odbierania komunikatów z Azure Service Bus kolejki.
ms.date: 06/23/2020
ms.topic: quickstart
ms.devlang: Java
ms.custom:
- seo-java-july2019
- seo-java-august2019
- seo-java-september2019
- devx-track-java
- mode-api
ms.openlocfilehash: 38657f333fea4ca18fb76eb5832649067bfd01b4
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538255"
---
# <a name="quickstart-use-azure-service-bus-queues-with-java-to-send-and-receive-messages"></a>Szybki start: używanie kolejek Azure Service Bus java do wysyłania i odbierania komunikatów

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]
Z tego samouczka dowiesz się, jak tworzyć aplikacje Java w celu wysyłania komunikatów do i odbierania komunikatów z Azure Service Bus kolejki. 

> [!WARNING]
>  W tym przewodniku Szybki start są używane `azure-servicebus` stare pakiety. Aby uzyskać przewodnik Szybki start korzystający z najnowszego `azure-messaging-servicebus` pakietu, zobacz [Wysyłanie i odbieranie komunikatów przy użyciu `azure-messaging-servicebus` narzędzia ](service-bus-java-how-to-use-queues.md). 


## <a name="prerequisites"></a>Wymagania wstępne
1. Subskrypcja platformy Azure. Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Możesz aktywować korzyści [dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) lub utworzyć bezpłatne [konto.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
2. Jeśli nie masz kolejki do pracy, wykonaj kroki opisane w artykule Use [Azure Portal to create a Service Bus queue to](service-bus-quickstart-portal.md) create a queue (Tworzenie kolejki przy użyciu usługi Azure Portal do tworzenia kolejki).
    1. Zapoznaj się z **szybkim omówieniem** Service Bus **kolejek.** 
    2. Utwórz nową Service Bus **nazw**. 
    3. Pobierz **ciąg połączenia**.
    4. Utwórz kolejkę Service Bus **kolejki.**
3. Zainstaluj [zestaw Azure SDK dla języka Java.][Azure SDK for Java] 


## <a name="configure-your-application-to-use-service-bus"></a>Konfigurowanie aplikacji do używania Service Bus
Przed sbudowaniam tego przykładu upewnij się, że zainstalowano zestaw Azure SDK dla języka [Java.][Azure SDK for Java] 

Jeśli używasz środowiska Eclipse, możesz zainstalować pakiet Azure Toolkit for Eclipse [który][Azure Toolkit for Eclipse] zawiera zestaw Azure SDK dla języka Java. Następnie możesz dodać **biblioteki Microsoft Azure dla języka Java** do projektu. Jeśli używasz programu IntelliJ, zobacz [Instalowanie Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/installation). 

![Dodawanie Microsoft Azure bibliotek języka Java do projektu eclipse](./media/service-bus-java-how-to-use-queues/eclipse-azure-libraries-java.png)


Dodaj następujące `import` instrukcje na początku pliku Java:

```java
// Include the following imports to use Service Bus APIs
import com.google.gson.reflect.TypeToken;
import com.microsoft.azure.servicebus.*;
import com.microsoft.azure.servicebus.primitives.ConnectionStringBuilder;
import com.google.gson.Gson;

import static java.nio.charset.StandardCharsets.*;

import java.time.Duration;
import java.util.*;
import java.util.concurrent.*;

import org.apache.commons.cli.*;

```

## <a name="send-messages-to-a-queue"></a>Wysyłanie komunikatów do kolejki
Aby wysyłać komunikaty do Service Bus Queue, aplikacja iniekuje obiekt **QueueClient** i wysyła komunikaty asynchronicznie. Poniższy kod pokazuje, jak wysłać komunikat dla kolejki utworzonej za pośrednictwem portalu.

```java
public void run() throws Exception {
    // Create a QueueClient instance and then asynchronously send messages.
    // Close the sender once the send operation is complete.
    QueueClient sendClient = new QueueClient(new ConnectionStringBuilder(ConnectionString, QueueName), ReceiveMode.PEEKLOCK);
    this.sendMessageAsync(sendClient).thenRunAsync(() -> sendClient.closeAsync());

    sendClient.close();
}

    CompletableFuture<Void> sendMessagesAsync(QueueClient sendClient) {
        List<HashMap<String, String>> data =
                GSON.fromJson(
                        "[" +
                                "{'name' = 'Einstein', 'firstName' = 'Albert'}," +
                                "{'name' = 'Heisenberg', 'firstName' = 'Werner'}," +
                                "{'name' = 'Curie', 'firstName' = 'Marie'}," +
                                "{'name' = 'Hawking', 'firstName' = 'Steven'}," +
                                "{'name' = 'Newton', 'firstName' = 'Isaac'}," +
                                "{'name' = 'Bohr', 'firstName' = 'Niels'}," +
                                "{'name' = 'Faraday', 'firstName' = 'Michael'}," +
                                "{'name' = 'Galilei', 'firstName' = 'Galileo'}," +
                                "{'name' = 'Kepler', 'firstName' = 'Johannes'}," +
                                "{'name' = 'Kopernikus', 'firstName' = 'Nikolaus'}" +
                                "]",
                        new TypeToken<List<HashMap<String, String>>>() {}.getType());

        List<CompletableFuture> tasks = new ArrayList<>();
        for (int i = 0; i < data.size(); i++) {
            final String messageId = Integer.toString(i);
            Message message = new Message(GSON.toJson(data.get(i), Map.class).getBytes(UTF_8));
            message.setContentType("application/json");
            message.setLabel("Scientist");
            message.setMessageId(messageId);
            message.setTimeToLive(Duration.ofMinutes(2));
            System.out.printf("\nMessage sending: Id = %s", message.getMessageId());
            tasks.add(
                    sendClient.sendAsync(message).thenRunAsync(() -> {
                        System.out.printf("\n\tMessage acknowledged: Id = %s", message.getMessageId());
                    }));
        }
        return CompletableFuture.allOf(tasks.toArray(new CompletableFuture<?>[tasks.size()]));
    }

```

Komunikaty wysyłane do i odbierane z Service Bus są wystąpieniami klasy [Message.](/java/api/com.microsoft.azure.servicebus.message) Obiekty komunikatów mają zestaw standardowych właściwości (takich jak Label i TimeToLive), słownik, który służy do przechowywania właściwości niestandardowych specyficznych dla aplikacji, oraz treść dowolnych danych aplikacji. Aplikacja może ustawić treść komunikatu, przekazując dowolny obiekt, który można serializować do konstruktora message, a następnie odpowiednie serializator będzie używany do serializacji obiektu. Alternatywnie możesz podać kod **java. Io. InputStream,** obiekt.


Kolejki usługi Service Bus obsługują maksymalny rozmiar komunikatu 256 KB w [warstwie Standardowa](service-bus-premium-messaging.md) i 1 MB w [warstwie Premium](service-bus-premium-messaging.md). Nagłówek, który zawiera standardowe i niestandardowe właściwości aplikacji, może mieć maksymalny rozmiar 64 KB. Nie ma żadnego limitu liczby komunikatów w kolejce, ale jest ograniczenie całkowitego rozmiaru komunikatów przechowywanych przez kolejkę. Ten rozmiar kolejki jest definiowany w czasie tworzenia, z górnym limitem 5 GB.

## <a name="receive-messages-from-a-queue"></a>Odbieranie komunikatów z kolejki
Podstawowym sposobem odbierania komunikatów z kolejki jest użycie obiektu **ServiceBusContract.** Odebrane komunikaty mogą działać w dwóch różnych trybach: **ReceiveAndDelete** i **PeekLock.**

W przypadku korzystania z trybu **ReceiveAndDelete** odbieranie jest operacją pojedynczego zrzutu — oznacza to, że gdy usługa Service Bus odbiera żądanie odczytu komunikatu w kolejce, oznacza komunikat jako zużyty i zwraca go do aplikacji. **Tryb ReceiveAndDelete** (który jest trybem domyślnym) jest najprostszym modelem i działa najlepiej w scenariuszach, w których aplikacja może tolerować nie przetwarzania komunikatu w przypadku awarii. Aby to zrozumieć, rozważmy scenariusz, w którym konsument wystawia żądanie odbioru, a następnie ulega awarii przed jego przetworzeniem.
Ponieważ Service Bus komunikat został oznaczony jako zużyty, po ponownym uruchomieniu aplikacji i ponownym uruchomieniu komunikatów pominięto komunikat, który został zużyty przed awarią.

W **trybie PeekLock** odbieranie staje się operacją dwuetapową, która umożliwia obsługę aplikacji, które nie tolerują brakujących komunikatów. Gdy usługa Service Bus odbiera żądanie, znajduje następny komunikat do wykorzystania, blokuje go w celu uniemożliwienia innym klientom odebrania go i zwraca go do aplikacji. Po zakończeniu przetwarzania komunikatu (lub niezawodnym przechowyceniu go do przetwarzania w przyszłości) aplikacja kończy drugi etap procesu odbierania, wywołując wywołanie funkcji **complete()** dla odebranego komunikatu. Gdy Service Bus wywołanie **complete(),** oznacza komunikat jako zużywany i usuwa go z kolejki. 

W poniższym przykładzie pokazano, jak komunikaty mogą być odbierane i przetwarzane przy użyciu **trybu PeekLock** (nie trybu domyślnego). W poniższym przykładzie użyto modelu wywołania zwrotnego z zarejestrowanym programem obsługi komunikatów i przetwarzane są komunikaty przychodzące do naszego modułu `TestQueue` . Ten tryb automatycznie wywołuje **metody complete(),** ponieważ wywołanie zwrotne zwraca normalnie i **wywołuje metody abandon(),** jeśli wywołanie zwrotne zgłasza wyjątek. 

```java
    public void run() throws Exception {
        // Create a QueueClient instance for receiving using the connection string builder
        // We set the receive mode to "PeekLock", meaning the message is delivered
        // under a lock and must be acknowledged ("completed") to be removed from the queue
        QueueClient receiveClient = new QueueClient(new ConnectionStringBuilder(ConnectionString, QueueName), ReceiveMode.PEEKLOCK);
        this.registerReceiver(receiveClient);

        // shut down receiver to close the receive loop
        receiveClient.close();
    }
    void registerReceiver(QueueClient queueClient) throws Exception {
        // register the RegisterMessageHandler callback
        queueClient.registerMessageHandler(new IMessageHandler() {
            // callback invoked when the message handler loop has obtained a message
            public CompletableFuture<Void> onMessageAsync(IMessage message) {
            // receives message is passed to callback
                if (message.getLabel() != null &&
                    message.getContentType() != null &&
                    message.getLabel().contentEquals("Scientist") &&
                    message.getContentType().contentEquals("application/json")) {

                        byte[] body = message.getBody();
                        Map scientist = GSON.fromJson(new String(body, UTF_8), Map.class);

                        System.out.printf(
                            "\n\t\t\t\tMessage received: \n\t\t\t\t\t\tMessageId = %s, \n\t\t\t\t\t\tSequenceNumber = %s, \n\t\t\t\t\t\tEnqueuedTimeUtc = %s," +
                            "\n\t\t\t\t\t\tExpiresAtUtc = %s, \n\t\t\t\t\t\tContentType = \"%s\",  \n\t\t\t\t\t\tContent: [ firstName = %s, name = %s ]\n",
                            message.getMessageId(),
                            message.getSequenceNumber(),
                            message.getEnqueuedTimeUtc(),
                            message.getExpiresAtUtc(),
                            message.getContentType(),
                            scientist != null ? scientist.get("firstName") : "",
                            scientist != null ? scientist.get("name") : "");
                    }
                    return CompletableFuture.completedFuture(null);
                }

                // callback invoked when the message handler has an exception to report
                public void notifyException(Throwable throwable, ExceptionPhase exceptionPhase) {
                    System.out.printf(exceptionPhase + "-" + throwable.getMessage());
                }
        },
        // 1 concurrent call, messages are auto-completed, auto-renew duration
        new MessageHandlerOptions(1, true, Duration.ofMinutes(1)));
    }

```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Sposób obsługi awarii aplikacji i komunikatów niemożliwych do odczytania
Usługa Service Bus zapewnia funkcję ułatwiającą bezpieczne odzyskiwanie w razie błędów w aplikacji lub trudności z przetwarzaniem komunikatu. Jeśli aplikacja odbiorcy z jakiegoś powodu nie może przetworzyć komunikatu, może wywołać metodę **abandon()** na obiekcie klienta przy użyciu tokenu blokady odebranego komunikatu uzyskanego za pośrednictwem metody **getLockToken()**. Powoduje to odblokowanie komunikatu w kolejce przez usługę Service Bus i ponowne udostępnienie go do odebrania przez tę samą lub inną odbierającą aplikację.

Istnieje również limit czasu skojarzony z komunikatem zablokowanym w kolejce, a jeśli aplikacja nie może przetworzyć komunikatu przed wygaśnięciem limitu czasu blokady (na przykład w przypadku awarii aplikacji), program Service Bus automatycznie odblokowuje komunikat i udostępnia go do od otrzymania ponownie.

W przypadku, gdy aplikacja ulega awarii po przetworzeniu komunikatu, ale przed przesłaniem żądania **complete()** komunikat jest ponownie dostarczyć do aplikacji po ponownym uruchomieniu. Jest to często nazywane *przetwarzaniem co najmniej raz*, co oznacza, że każdy komunikat jest przetwarzany co najmniej raz, ale w pewnych sytuacjach ten sam komunikat może być dostarczony ponownie. Jeśli scenariusz nie toleruje dwukrotnego przetwarzania, deweloperzy aplikacji powinni dodać dodatkową logikę do swojej aplikacji w celu obsługi dwukrotnego dostarczania komunikatów. Jest to często osiągane przy użyciu **metody getMessageId** komunikatu, która pozostaje stała między próbami dostarczenia.

> [!NOTE]
> Za pomocą Service Bus można zarządzać [zasobami Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Usługa Service Bus Explorer umożliwia użytkownikom łączenie się z przestrzenią nazw Service Bus i administrowanie jednostkami obsługi komunikatów w prosty sposób. Narzędzie udostępnia zaawansowane funkcje, takie jak funkcja importowania/eksportowania lub możliwość testowania tematu, kolejek, subskrypcji, usług przekazywania, centrów powiadomień i centrów zdarzeń. 

## <a name="next-steps"></a>Następne kroki
Przykłady języka Java można znaleźć w witrynie GitHub w [ `azure-service-bus` repozytorium](https://github.com/Azure/azure-service-bus/tree/master/samples/Java).

[Azure SDK for Java]: /azure/developer/java/sdk/get-started
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
