---
title: Korzystanie z kolejek usługi Azure Service Bus w języku Java
description: W tym samouczku dowiesz się, jak tworzyć aplikacje Java do wysyłania wiadomości do i odbierania wiadomości z kolejki usługi Azure Service Bus.
services: service-bus-messaging
documentationcenter: java
author: axisc
manager: timlt
editor: spelluru
ms.assetid: f701439c-553e-402c-94a7-64400f997d59
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aschhab
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 184ffd39281ea27d8596bc37a9f89fd22acfb1ba
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732186"
---
# <a name="quickstart-use-azure-service-bus-queues-with-java-to-send-and-receive-messages"></a>Szybki start: wysyłanie i odbieranie wiadomości za pomocą kolejek usługi Azure Service Bus w języku Java

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]
W tym samouczku dowiesz się, jak tworzyć aplikacje Java do wysyłania wiadomości do i odbierania wiadomości z kolejki usługi Azure Service Bus. 

> [!NOTE]
> Przykłady oprogramowania Java można znaleźć w usłudze GitHub w [repozytorium magistrali azure.](https://github.com/Azure/azure-service-bus/tree/master/samples/Java)

## <a name="prerequisites"></a>Wymagania wstępne
1. Subskrypcja platformy Azure. Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Możesz aktywować [swoje korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) lub założyć bezpłatne [konto.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
2. Jeśli nie masz kolejki do pracy, wykonaj kroki w [witrynie Azure portal, aby utworzyć kolejkę usługi Service Bus](service-bus-quickstart-portal.md) artykuł, aby utworzyć kolejkę.
    1. Przeczytaj krótki **przegląd** **kolejek**usługi Service Bus . 
    2. Tworzenie obszaru **nazw**usługi Service Bus . 
    3. Pobierz **ciąg połączenia**.
    4. Utwórz **kolejkę**usługi Service Bus .
3. Zainstaluj [zestaw SDK platformy Azure dla języka Java][Azure SDK for Java]. 


## <a name="configure-your-application-to-use-service-bus"></a>Konfigurowanie aplikacji do korzystania z usługi Service Bus
Przed rozpoczęciem tworzenia tego przykładu upewnij się, że [zainstalowano zestaw SDK platformy Azure dla języka Java.][Azure SDK for Java] 

Jeśli używasz programu Eclipse, możesz zainstalować [zestaw narzędzi Azure Toolkit for Eclipse,][Azure Toolkit for Eclipse] który zawiera zestaw SDK platformy Azure dla języka Java. Następnie można dodać **biblioteki platformy Microsoft Azure dla oprogramowania Java** do projektu. Jeśli używasz IntelliJ, zobacz [Instalowanie zestawu narzędzi azure dla intellij](/azure/developer/java/toolkit-for-intellij/installation). 

![Dodawanie bibliotek platformy Microsoft Azure dla oprogramowania Java do projektu programu Eclipse](./media/service-bus-java-how-to-use-queues/eclipse-azure-libraries-java.png)


Dodaj następujące `import` instrukcje do górnej części pliku Java:

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
Aby wysyłać wiadomości do kolejki usługi Service Bus, aplikacja wywołuje wystąpienie obiektu **QueueClient** i wysyła komunikaty asynchronicznie. Poniższy kod pokazuje, jak wysłać wiadomość dla kolejki, który został utworzony za pośrednictwem portalu.

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

Wiadomości wysyłane i odbierane z kolejek usługi Service Bus są wystąpieniami [message](/java/api/com.microsoft.azure.servicebus.message?view=azure-java-stable) klasy. Obiekty wiadomości mają zestaw właściwości standardowych (takich jak Label i TimeToLive), słownik, który jest używany do przechowywania właściwości specyficzne dla aplikacji niestandardowych i treść dowolnych danych aplikacji. Aplikacja może ustawić treść wiadomości, przekazując dowolny serializable obiektu do konstruktora Message, a odpowiedni serializator będzie następnie używany do serializacji obiektu. Alternatywnie, można podać **java. Io. InputStream,** obiekt.


Kolejki usługi Service Bus obsługują maksymalny rozmiar komunikatu 256 KB w [warstwie Standardowa](service-bus-premium-messaging.md) i 1 MB w [warstwie Premium](service-bus-premium-messaging.md). Nagłówek, który zawiera standardowe i niestandardowe właściwości aplikacji, może mieć maksymalny rozmiar 64 KB. Nie ma żadnego limitu liczby komunikatów w kolejce, ale jest ograniczenie całkowitego rozmiaru komunikatów przechowywanych przez kolejkę. Ten rozmiar kolejki jest definiowany w czasie tworzenia, z górnym limitem 5 GB.

## <a name="receive-messages-from-a-queue"></a>Odbieranie wiadomości z kolejki
Podstawowym sposobem odbierania wiadomości z kolejki jest użycie obiektu **ServiceBusContract.** Odebrane wiadomości mogą działać w dwóch różnych trybach: **ReceiveAndDelete** i **PeekLock**.

Korzystając z trybu **ReceiveAndDelete,** receive jest operacją pojedynczego strzału — oznacza to, że gdy usługa Service Bus odbiera żądanie odczytu wiadomości w kolejce, oznacza komunikat jako używany i zwraca ją do aplikacji. **ReceiveAndDelete** tryb (który jest trybem domyślnym) jest najprostszym modelem i działa najlepiej dla scenariuszy, w których aplikacja może tolerować nie przetwarzania wiadomości w przypadku awarii. Aby to zrozumieć, rozważmy scenariusz, w którym konsument wystawia żądanie odbioru, a następnie ulega awarii przed jego przetworzeniem.
Ponieważ usługa Service Bus oznaczyła komunikat jako zużyty, a następnie po ponownym uruchomieniu aplikacji i ponownym rozpoczęciu korzystania z wiadomości, nie odebrano komunikatu, który został zużyty przed awarią.

W trybie **PeekLock** receive staje się operacją dwuetapową, która umożliwia obsługę aplikacji, które nie tolerują brakujących komunikatów. Gdy usługa Service Bus odbiera żądanie, znajduje następny komunikat do wykorzystania, blokuje go w celu uniemożliwienia innym klientom odebrania go i zwraca go do aplikacji. Po zakończeniu przetwarzania wiadomości (lub magazynuje ją niezawodnie do przetwarzania w przyszłości), kończy drugi etap procesu odbierania, wywołując **complete()** w odebranej wiadomości. Gdy usługa Service Bus zobaczy wywołanie **complete(),** oznacza komunikat jako zużyty i usuwa ją z kolejki. 

Poniższy przykład pokazuje, jak wiadomości mogą być odbierane i przetwarzane w trybie **PeekLock** (nie w trybie domyślnym). W poniższym przykładzie użyto modelu wywołania zwrotnego z zarejestrowanym programem obsługi wiadomości i przetwarza wiadomości, gdy dotrą do naszego `TestQueue`. Ten tryb wywołuje **complete()** automatycznie, jak wywołania zwrotnego zwraca normalnie i wywołuje **abandon(),** jeśli wywołanie zwrotne zgłasza wyjątek. 

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
Usługa Service Bus zapewnia funkcję ułatwiającą bezpieczne odzyskiwanie w razie błędów w aplikacji lub trudności z przetwarzaniem komunikatu. Jeśli aplikacja odbiorcy nie jest w stanie przetworzyć wiadomości z jakiegoś powodu, może wywołać metodę **abandon()** na obiekcie klienta za pomocą tokenu blokady odebranej wiadomości uzyskanego za pośrednictwem **getLockToken()**. Powoduje to odblokowanie komunikatu w kolejce przez usługę Service Bus i ponowne udostępnienie go do odebrania przez tę samą lub inną odbierającą aplikację.

Istnieje również limit czasu skojarzony z komunikatem zablokowanym w kolejce, a jeśli aplikacja nie może przetworzyć wiadomości przed upływem limitu czasu blokady (na przykład, jeśli aplikacja ulegnie awarii), usługa Service Bus automatycznie odblokowuje wiadomość i udostępnia ją do odebrania.

W przypadku awarii aplikacji po przetworzeniu wiadomości, ale przed wydaniem żądania **complete(),** komunikat jest ponownie dostarczony do aplikacji po ponownym uruchomieniu. Jest to często nazywane *przetwarzaniem co najmniej raz*, co oznacza, że każdy komunikat jest przetwarzany co najmniej raz, ale w pewnych sytuacjach ten sam komunikat może być dostarczony ponownie. Jeśli scenariusz nie toleruje dwukrotnego przetwarzania, deweloperzy aplikacji powinni dodać dodatkową logikę do swojej aplikacji w celu obsługi dwukrotnego dostarczania komunikatów. Jest to często osiągane przy użyciu **getMessageId** metody wiadomości, która pozostaje stała w próbach dostarczenia.

> [!NOTE]
> Zasoby usługi Service Bus można zarządzać za pomocą [Eksploratora magistrali usług](https://github.com/paolosalvatori/ServiceBusExplorer/). Eksplorator usługi Service Bus umożliwia użytkownikom łączenie się z obszarem nazw usługi Service Bus i administrowanie jednostkami obsługi wiadomości w łatwy sposób. Narzędzie zapewnia zaawansowane funkcje, takie jak funkcje importu/eksportu lub możliwość testowania tematu, kolejek, subskrypcji, usług przekazywania, centrów powiadomień i centrów zdarzeń. 

## <a name="next-steps"></a>Następne kroki
Teraz, gdy poznasz podstawy kolejek usługi Service Bus, zobacz [Kolejki, tematy i subskrypcje,][Queues, topics, and subscriptions] aby uzyskać więcej informacji.

Więcej informacji możesz znaleźć w [Centrum deweloperów języka Java](https://azure.microsoft.com/develop/java/).

[Azure SDK for Java]: /azure/developer/java/sdk/java-sdk-azure-get-started
[Azure Toolkit for Eclipse]: https://docs.microsoft.com/java/azure/eclipse/azure-toolkit-for-eclipse
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
