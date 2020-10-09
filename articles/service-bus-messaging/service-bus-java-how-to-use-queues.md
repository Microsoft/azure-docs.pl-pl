---
title: Korzystanie z kolejek Azure Service Bus przy użyciu języka Java
description: W ramach tego samouczka nauczysz się tworzyć aplikacje Java do wysyłania komunikatów do i odbierania komunikatów z kolejki Azure Service Bus.
ms.devlang: Java
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 8883b5959cc4c67d34efc3c9da7788f03fc6c9af
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91825195"
---
# <a name="quickstart-use-azure-service-bus-queues-with-java-to-send-and-receive-messages"></a>Szybki Start: korzystanie z kolejek Azure Service Bus w języku Java do wysyłania i odbierania wiadomości

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]
W ramach tego samouczka nauczysz się tworzyć aplikacje Java do wysyłania komunikatów do i odbierania komunikatów z kolejki Azure Service Bus. 

> [!NOTE]
> Przykłady dla języka Java w witrynie GitHub można znaleźć w [repozytorium Azure-Service-Bus](https://github.com/Azure/azure-service-bus/tree/master/samples/Java).

## <a name="prerequisites"></a>Wymagania wstępne
1. Subskrypcja platformy Azure. Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Możesz aktywować korzyści dla [subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Jeśli nie masz kolejki do współpracy z programem, postępuj zgodnie z instrukcjami w temacie [Use Azure Portal, aby utworzyć](service-bus-quickstart-portal.md) kolejkę Service Bus w celu utworzenia kolejki.
    1. Zapoznaj się **overview** z krótkim omówieniem **kolejek**Service Bus. 
    2. Utwórz **przestrzeń nazw**Service Bus. 
    3. Pobierz **Parametry połączenia**.
    4. Utwórz **kolejkę**Service Bus.
3. Zainstaluj [zestaw Azure SDK dla języka Java][Azure SDK for Java]. 


## <a name="configure-your-application-to-use-service-bus"></a>Skonfiguruj aplikację do używania Service Bus
Przed skompilowaniem tego przykładu upewnij się, że zainstalowano [zestaw Azure SDK dla języka Java][Azure SDK for Java] . 

W przypadku korzystania z programu zaćmienie można zainstalować [Azure Toolkit for Eclipse][Azure Toolkit for Eclipse] zawierający zestaw Azure SDK dla języka Java. Następnie można dodać **biblioteki Microsoft Azure dla języka Java** do projektu. Jeśli używasz programu IntelliJ, zobacz [Install the Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/installation). 

![Dodaj biblioteki Microsoft Azure dla języka Java do projektu przezaćmienia](./media/service-bus-java-how-to-use-queues/eclipse-azure-libraries-java.png)


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
Aby wysyłać komunikaty do kolejki Service Bus, aplikacja tworzy wystąpienie obiektu **QueueClient** i asynchronicznie wysyła komunikaty. Poniższy kod przedstawia sposób wysyłania komunikatu dla kolejki, która została utworzona za pomocą portalu.

```java
public void run() throws Exception {
    // Create a QueueClient instance and then asynchronously send messages.
    // Close the sender once the send operation is complete.
    QueueClient sendClient = new QueueClient(new ConnectionStringBuilder(ConnectionString, QueueName), ReceiveMode.PEEKLOCK);
    this.sendMessagesAsync(sendClient).thenRunAsync(() -> sendClient.closeAsync());

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

Komunikaty wysyłane do i odbierane z kolejek Service Bus są wystąpieniami klasy [Message](/java/api/com.microsoft.azure.servicebus.message?view=azure-java-stable) . Obiekty komunikatów mają zestaw właściwości standardowych (takich jak etykieta i TimeToLive), słownik używany do przechowywania niestandardowych właściwości specyficznych dla aplikacji oraz treść dowolnych danych aplikacji. Aplikacja może ustawić treść komunikatu przez przekazanie dowolnego obiektu możliwego do serializacji do konstruktora wiadomości, a następnie odpowiedni serializator zostanie użyty do serializacji obiektu. Alternatywnie możesz udostępnić **środowisko Java. /. Obiekt InputStream** .


Kolejki usługi Service Bus obsługują maksymalny rozmiar komunikatu 256 KB w [warstwie Standardowa](service-bus-premium-messaging.md) i 1 MB w [warstwie Premium](service-bus-premium-messaging.md). Nagłówek, który zawiera standardowe i niestandardowe właściwości aplikacji, może mieć maksymalny rozmiar 64 KB. Nie ma żadnego limitu liczby komunikatów w kolejce, ale jest ograniczenie całkowitego rozmiaru komunikatów przechowywanych przez kolejkę. Ten rozmiar kolejki jest definiowany w czasie tworzenia, z górnym limitem 5 GB.

## <a name="receive-messages-from-a-queue"></a>Odbieranie komunikatów z kolejki
Podstawowym sposobem odbierania komunikatów z kolejki jest użycie obiektu **ServiceBusContract** . Odebrane komunikaty mogą być wykonywane w dwóch różnych trybach: **ReceiveAndDelete** i **PeekLock**.

W przypadku korzystania z trybu **ReceiveAndDelete** odbieranie jest operacją pojedynczego zrzutu, czyli gdy Service Bus odbiera żądanie odczytu komunikatu w kolejce, oznacza komunikat jako używany i zwraca go do aplikacji. Tryb **ReceiveAndDelete** (który jest domyślnym trybem) jest najprostszym modelem i najlepiej sprawdza się w scenariuszach, w których aplikacja może tolerować nieprzetwarzanie komunikatu w przypadku awarii. Aby to zrozumieć, rozważmy scenariusz, w którym konsument wystawia żądanie odbioru, a następnie ulega awarii przed jego przetworzeniem.
Ponieważ Service Bus oznaczył komunikat jako używany, wtedy, gdy aplikacja zostanie ponownie uruchomiona i rozpocznie korzystanie z komunikatów, zostanie pominięty komunikat, który był używany przed awarią.

W trybie **PeekLock** , odbieranie staje się operacją dwuetapową, co umożliwia obsługę aplikacji, które nie mogą tolerować brakujących komunikatów. Gdy usługa Service Bus odbiera żądanie, znajduje następny komunikat do wykorzystania, blokuje go w celu uniemożliwienia innym klientom odebrania go i zwraca go do aplikacji. Gdy aplikacja zakończy przetwarzanie komunikatu (lub zapisuje ją w sposób niezawodny w przyszłości), kończy drugi etap procesu odbierania, wywołując metodę **Complete ()** dla odebranego komunikatu. Gdy Service Bus widzi **kompletne ()** wywołanie, oznacza komunikat jako używany i usuń go z kolejki. 

W poniższym przykładzie pokazano, jak można odbierać i przetwarzać komunikaty przy użyciu trybu **PeekLock** (a nie trybu domyślnego). W poniższym przykładzie zastosowano model wywołania zwrotnego z zarejestrowaną obsługą komunikatów i przetwarza komunikaty w miarę ich przybycia do naszej firmy `TestQueue` . Ten tryb wywołuje **pełny ()** automatycznie, gdy wywołanie zwrotne zwraca się normalnie i wywołuje metodę **Abandon ()** , jeśli wywołanie zwrotne zgłasza wyjątek. 

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
Usługa Service Bus zapewnia funkcję ułatwiającą bezpieczne odzyskiwanie w razie błędów w aplikacji lub trudności z przetwarzaniem komunikatu. Jeśli aplikacja odbiornika nie może przetworzyć komunikatu z jakiegoś powodu, może wywołać metodę **Abandon ()** w obiekcie Client z tokenem blokady otrzymanej wiadomości uzyskanym za pośrednictwem **getLockToken ()**. Powoduje to odblokowanie komunikatu w kolejce przez usługę Service Bus i ponowne udostępnienie go do odebrania przez tę samą lub inną odbierającą aplikację.

Istnieje również limit czasu skojarzony z komunikatem zablokowanym w kolejce i jeśli aplikacja nie może przetworzyć komunikatu przed upływem limitu czasu blokady (na przykład jeśli awaria aplikacji), Service Bus odblokowywanie komunikatu automatycznie i udostępnienie go do ponownego odebrania.

W przypadku awarii aplikacji po przetworzeniu komunikatu, ale przed wystawieniem **kompletnego żądania ()** , komunikat zostanie ponownie dostarczony do aplikacji po jej ponownym uruchomieniu. Jest to często nazywane *przetwarzaniem co najmniej raz*, co oznacza, że każdy komunikat jest przetwarzany co najmniej raz, ale w pewnych sytuacjach ten sam komunikat może być dostarczony ponownie. Jeśli scenariusz nie toleruje dwukrotnego przetwarzania, deweloperzy aplikacji powinni dodać dodatkową logikę do swojej aplikacji w celu obsługi dwukrotnego dostarczania komunikatów. Jest to często osiągane przy użyciu metody **getmessageid** komunikatu, która pozostaje stała między kolejnymi próbami dostarczenia.

> [!NOTE]
> Za pomocą [eksploratora Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/)można zarządzać zasobami Service Bus. Eksplorator Service Bus umożliwia użytkownikom łączenie się z przestrzenią nazw Service Bus i administrowanie jednostkami obsługi komunikatów w prosty sposób. Narzędzie zapewnia zaawansowane funkcje, takie jak funkcja importowania/eksportowania lub możliwość testowania tematów, kolejek, subskrypcji, usług przekazywania, centrów powiadomień i centrów zdarzeń. 

## <a name="next-steps"></a>Następne kroki
Teraz, gdy znasz już podstawy Service Busych kolejek, zobacz sekcję [kolejki, tematy i subskrypcje][Queues, topics, and subscriptions] , aby uzyskać więcej informacji.

Więcej informacji możesz znaleźć w [Centrum deweloperów języka Java](https://azure.microsoft.com/develop/java/).

[Azure SDK for Java]: /azure/developer/java/sdk/java-sdk-azure-get-started
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
