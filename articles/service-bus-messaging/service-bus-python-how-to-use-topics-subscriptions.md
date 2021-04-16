---
title: Używanie Azure Service Bus tematach i subskrypcjach za pomocą pakietu azure-servicebus języka Python w wersji 7.0.0
description: W tym artykule pokazano, jak używać języka Python do wysyłania komunikatów do tematu i odbierania komunikatów z subskrypcji.
documentationcenter: python
author: spelluru
ms.author: spelluru
ms.date: 11/18/2020
ms.topic: quickstart
ms.devlang: python
ms.custom:
- devx-track-python
- mode-api
ms.openlocfilehash: 49e80e277c6df5372341293861d5bda0580f3e8c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537169"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-python"></a>Wysyłanie komunikatów do Azure Service Bus tematu i odbieranie komunikatów z subskrypcji do tematu (Python)
W tym artykule pokazano, jak używać języka Python do wysyłania komunikatów Service Bus tematu i odbierania komunikatów z subskrypcji do tematu. 

## <a name="prerequisites"></a>Wymagania wstępne
- Subskrypcja platformy Azure. Możesz aktywować korzyści [dla subskrybentów Visual Studio MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) lub zarejestrować się w celu korzystania z [bezpłatnego konta.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Wykonaj kroki opisane w [przewodniku Szybki start: tworzenie](service-bus-quickstart-topics-subscriptions-portal.md)Azure Portal tematu i subskrypcji Service Bus tematu . Zanotuj ciąg połączenia, nazwę tematu i nazwę subskrypcji. W tym przewodniku Szybki start użyjesz tylko jednej subskrypcji. 
- Język Python w wersji 2.7 lub wyższej z zainstalowanym pakietem [Azure Python SDK][Pakiet Języka Python] platformy Azure. Aby uzyskać więcej informacji, zobacz [Przewodnik instalacji języka Python](/azure/developer/python/azure-sdk-install).

## <a name="send-messages-to-a-topic"></a>Wysyłanie komunikatów do tematu

1. Dodaj następującą instrukcje importu. 

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
    > - Zastąp `<NAMESPACE CONNECTION STRING>` ciąg ciągami połączenia dla przestrzeni nazw.
    > - Zastąp `<TOPIC NAME>` nazwą tematu.
    > - Zastąp `<SUBSCRIPTION NAME>` nazwą subskrypcji tematu. 
3. Dodaj metodę w celu wysłania pojedynczego komunikatu.

    ```python
    def send_single_message(sender):
        # create a Service Bus message
        message = ServiceBusMessage("Single Message")
        # send the message to the topic
        sender.send_messages(message)
        print("Sent a single message")
    ```

    Nadawca jest obiektem, który działa jako klient dla utworzonego tematu. Utworzysz go później i wyślesz jako argument do tej funkcji. 
4. Dodaj metodę wysyłania listy komunikatów.

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
6. Utwórz klienta Service Bus, a następnie obiekt nadawcy tematu do wysyłania komunikatów.

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
Dodaj następujący kod po instrukcji print. Ten kod stale odbiera nowe komunikaty, dopóki nie odbierze żadnych nowych komunikatów przez 5 `max_wait_time` ( ) sekund. 

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
Po uruchomieniu aplikacji powinny zostać wyświetlony następujące dane wyjściowe: 

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

W Azure Portal przejdź do swojej Service Bus nazw. Na stronie **Przegląd** sprawdź, czy  **liczba** komunikatów przychodzących i wychodzących wynosi 16. Jeśli nie widzisz liczby, odśwież stronę po odczekieniu kilku minut. 

:::image type="content" source="./media/service-bus-python-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Liczba komunikatów przychodzących i wychodzących":::

Wybierz temat w dolnym okienku, aby wyświetlić Service Bus **tematu** dla tematu. Na tej stronie powinny być wyświetlane trzy komunikaty przychodzące i trzy wychodzące na **wykresie Komunikaty.** 

:::image type="content" source="./media/service-bus-python-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="Komunikaty przychodzące i wychodzące":::

Na tej stronie, jeśli wybierzesz subskrypcję, zostanie Service Bus **Subskrypcja.** Na tej stronie można zobaczyć liczbę aktywnych komunikatów, liczbę utraconych komunikatów i inne. W tym przykładzie odebrano wszystkie komunikaty, więc liczba aktywnych komunikatów wynosi zero. 

:::image type="content" source="./media/service-bus-python-how-to-use-topics-subscriptions/active-message-count.png" alt-text="Liczba aktywnych komunikatów":::

Jeśli kod odbioru zostanie w komentarzu, zobaczysz aktywną liczbę komunikatów jako 16. 

:::image type="content" source="./media/service-bus-python-how-to-use-topics-subscriptions/active-message-count-2.png" alt-text="Liczba aktywnych komunikatów — brak odbierania":::

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującą dokumentacją i przykładami: 

- [Azure Service Bus klienta dla języka Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus)
- [Przykłady](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus/samples). 
    - Folder **sync_samples** zawiera przykłady, które pokazują, jak korzystać z Service Bus w sposób synchroniczny. W tym przewodniku Szybki start została użyta ta metoda. 
    - Folder **async_samples** zawiera przykłady, które pokazują, jak korzystać z Service Bus w sposób asynchroniczny. 
- [Dokumentacja referencyjna usługi azure-servicebus](/python/api/azure-servicebus/azure.servicebus?preserve-view=true)
