---
title: Korzystanie z Azure Service Bus tematów i subskrypcji w języku Python Azure-ServiceBus w wersji 7.0.0
description: W tym artykule pokazano, jak używać języka Python do wysyłania komunikatów do tematu i odbierania komunikatów z subskrypcji.
documentationcenter: python
author: spelluru
ms.devlang: python
ms.topic: quickstart
ms.date: 11/18/2020
ms.author: spelluru
ms.custom: devx-track-python
ms.openlocfilehash: 4eba3ea055e78888d482927fa6eed5c7d41fa0ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98630051"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-python"></a>Wysyłanie komunikatów do tematu Azure Service Bus i odbieranie komunikatów z subskrypcji do tematu (Python)
W tym artykule pokazano, jak za pomocą języka Python wysyłać komunikaty do Service Bus tematu i odbierać komunikaty z subskrypcji do tematu. 

## <a name="prerequisites"></a>Wymagania wstępne
- Subskrypcja platformy Azure. Możesz aktywować korzyści dla [subskrybentów programu Visual Studio lub MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Wykonaj kroki opisane w [przewodniku szybki start: użyj Azure Portal, aby utworzyć temat Service Bus i subskrypcje w temacie](service-bus-quickstart-topics-subscriptions-portal.md). Zanotuj parametry połączenia, nazwę tematu i nazwę subskrypcji. W tym przewodniku szybki start będziesz używać tylko jednej subskrypcji. 
- Język Python 2,7 lub nowszy z zainstalowanym pakietem [Azure Python SDK] [pakiet platformy Azure Python]. Aby uzyskać więcej informacji, zobacz [Przewodnik instalacji języka Python](/azure/developer/python/azure-sdk-install).

## <a name="send-messages-to-a-topic"></a>Wysyłanie komunikatów do tematu

1. Dodaj następującą instrukcję importu. 

    ```python
    from azure.servicebus import ServiceBusClient, ServiceBusMessage
    ```
2. Dodaj następujące stałe. 

    ```python
    CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
    TOPIC_NAME = "<TOPIC NAME>"
    SUBSCRIPTION_NAME = "<SUBSCRIPTION NAME>"
    ```
    
    > [!IMPORTANT]
    > - Zamień na `<NAMESPACE CONNECTION STRING>` Parametry połączenia dla przestrzeni nazw.
    > - Zamień na `<TOPIC NAME>` nazwę tematu.
    > - Zamień na `<SUBSCRIPTION NAME>` nazwę subskrypcji tematu. 
3. Dodaj metodę w celu wysłania pojedynczej wiadomości.

    ```python
    def send_single_message(sender):
        # create a Service Bus message
        message = ServiceBusMessage("Single Message")
        # send the message to the topic
        sender.send_messages(message)
        print("Sent a single message")
    ```

    Nadawca jest obiektem, który działa jako klient dla utworzonego tematu. Utworzysz go później i wyślesz jako argument do tej funkcji. 
4. Dodaj metodę, aby wysłać listę komunikatów.

    ```python
    def send_a_list_of_messages(sender):
        # create a list of messages
        messages = [ServiceBusMessage("Message in list") for _ in range(5)]
        # send the list of messages to the topic
        sender.send_messages(messages)
        print("Sent a list of 5 messages")
    ```
5. Dodaj metodę wysyłania partii komunikatów.

    ```python
    def send_batch_message(sender):
        # create a batch of messages
        batch_message = sender.create_message_batch()
        for _ in range(10):
            try:
                # add a message to the batch
                batch_message.add_message(ServiceBusMessage("Message inside a ServiceBusMessageBatch"))
            except ValueError:
                # ServiceBusMessageBatch object reaches max_size.
                # New ServiceBusMessageBatch object can be created here to send more data.
                break
        # send the batch of messages to the topic
        sender.send_messages(batch_message)
        print("Sent a batch of 10 messages")
    ```
6. Utwórz klienta Service Bus, a następnie obiekt nadawcy tematu do wysyłania wiadomości.

    ```python
    # create a Service Bus client using the connection string
    servicebus_client = ServiceBusClient.from_connection_string(conn_str=CONNECTION_STR, logging_enable=True)
    with servicebus_client:
        # get a Topic Sender object to send messages to the topic
        sender = servicebus_client.get_topic_sender(topic_name=TOPIC_NAME)
        with sender:
            # send one message        
            send_single_message(sender)
            # send a list of messages
            send_a_list_of_messages(sender)
            # send a batch of messages
            send_batch_message(sender)
    
    print("Done sending messages")
    print("-----------------------")
    ```
 
## <a name="receive-messages-from-a-subscription"></a>Odbieranie komunikatów z subskrypcji
Dodaj następujący kod po instrukcji Print. Ten kod ciągle otrzymuje nowe wiadomości, dopóki nie otrzyma żadnych nowych komunikatów przez 5 ( `max_wait_time` ) s. 

```python
with servicebus_client:
    # get the Subscription Receiver object for the subscription    
    receiver = servicebus_client.get_subscription_receiver(topic_name=TOPIC_NAME, subscription_name=SUBSCRIPTION_NAME, max_wait_time=5)
    with receiver:
        for msg in receiver:
            print("Received: " + str(msg))
            # complete the message so that the message is removed from the subscription
            receiver.complete_message(msg)
```

## <a name="full-code"></a>Pełny kod

```python
from azure.servicebus import ServiceBusClient, ServiceBusMessage

CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
TOPIC_NAME = "<TOPIC NAME>"
SUBSCRIPTION_NAME = "<SUBSCRIPTION NAME>"

def send_single_message(sender):
    message = ServiceBusMessage("Single Message")
    sender.send_messages(message)
    print("Sent a single message")

def send_a_list_of_messages(sender):
    messages = [ServiceBusMessage("Message in list") for _ in range(5)]
    sender.send_messages(messages)
    print("Sent a list of 5 messages")

def send_batch_message(sender):
    batch_message = sender.create_message_batch()
    for _ in range(10):
        try:
            batch_message.add_message(ServiceBusMessage("Message inside a ServiceBusMessageBatch"))
        except ValueError:
            # ServiceBusMessageBatch object reaches max_size.
            # New ServiceBusMessageBatch object can be created here to send more data.
            break
    sender.send_messages(batch_message)
    print("Sent a batch of 10 messages")

servicebus_client = ServiceBusClient.from_connection_string(conn_str=CONNECTION_STR, logging_enable=True)

with servicebus_client:
    sender = servicebus_client.get_topic_sender(topic_name=TOPIC_NAME)
    with sender:
        send_single_message(sender)
        send_a_list_of_messages(sender)
        send_batch_message(sender)

print("Done sending messages")
print("-----------------------")

with servicebus_client:
    receiver = servicebus_client.get_subscription_receiver(topic_name=TOPIC_NAME, subscription_name=SUBSCRIPTION_NAME, max_wait_time=5)
    with receiver:
        for msg in receiver:
            print("Received: " + str(msg))
            receiver.complete_message(msg)
```

## <a name="run-the-app"></a>Uruchamianie aplikacji
Po uruchomieniu aplikacji powinny zostać wyświetlone następujące dane wyjściowe: 

```console
Sent a single message
Sent a list of 5 messages
Sent a batch of 10 messages
Done sending messages
-----------------------
Received: Single Message
Received: Message in list
Received: Message in list
Received: Message in list
Received: Message in list
Received: Message in list
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
```

W Azure Portal przejdź do przestrzeni nazw Service Bus. Na stronie **Przegląd** Sprawdź, czy liczba komunikatów **przychodzących** i **wychodzących** wynosi 16. Jeśli liczby nie są widoczne, Odśwież stronę po odczekaniu przez kilka minut. 

:::image type="content" source="./media/service-bus-python-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Liczba wiadomości przychodzących i wychodzących":::

Wybierz temat w dolnym okienku, aby wyświetlić stronę **tematu Service Bus** tematu. Na tej stronie powinny być widoczne trzy przychodzące i trzy komunikaty wychodzące na wykresie **komunikatów** . 

:::image type="content" source="./media/service-bus-python-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="Komunikaty przychodzące i wychodzące":::

Na tej stronie, jeśli wybierzesz subskrypcję, uzyskasz dostęp do strony **subskrypcji Service Bus** . Na tej stronie można zobaczyć liczbę aktywnych komunikatów, liczbę wiadomości utraconych i więcej. W tym przykładzie wszystkie komunikaty zostały odebrane, więc liczba aktywnych komunikatów wynosi zero. 

:::image type="content" source="./media/service-bus-python-how-to-use-topics-subscriptions/active-message-count.png" alt-text="Liczba aktywnych komunikatów":::

Jeśli dodasz komentarz do kodu odbioru, liczba aktywnych komunikatów zostanie wyświetlona jako 16. 

:::image type="content" source="./media/service-bus-python-how-to-use-topics-subscriptions/active-message-count-2.png" alt-text="Liczba aktywnych komunikatów — brak odbierania":::

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z poniższą dokumentacją i przykładami: 

- [Azure Service Bus Biblioteka kliencka dla języka Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus)
- [Przykłady](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus/samples). 
    - Folder **sync_samples** zawiera przykłady pokazujące, jak w sposób synchroniczny korzystać z Service Bus. W tym przewodniku szybki start użyto tej metody. 
    - Folder **async_samples** zawiera przykłady pokazujące, jak interakcyjnie korzystać z Service Bus w sposób asynchroniczny. 
- [Dokumentacja referencyjna platformy Azure-ServiceBus](/python/api/azure-servicebus/azure.servicebus?preserve-view=true)