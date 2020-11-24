---
title: Korzystanie z kolejek Azure Service Bus w języku Python Azure-ServiceBus w wersji 7.0.0
description: W tym artykule pokazano, jak za pomocą języka Python wysyłać komunikaty do i odbierać komunikaty z kolejek Azure Service Bus.
author: spelluru
documentationcenter: python
ms.devlang: python
ms.topic: quickstart
ms.date: 11/18/2020
ms.author: spelluru
ms.custom: seo-python-october2019, devx-track-python
ms.openlocfilehash: 2b54b167413b0fcbe7022eab4bbbf34b37225be5
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95810572"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-python"></a>Wysyłanie komunikatów do i odbieranie komunikatów z kolejek Azure Service Bus (Python)
W tym artykule pokazano, jak za pomocą języka Python wysyłać komunikaty do i odbierać komunikaty z kolejek Azure Service Bus. 

## <a name="prerequisites"></a>Wymagania wstępne
- Subskrypcja platformy Azure. Możesz aktywować korzyści dla [subskrybentów programu Visual Studio lub MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Jeśli nie masz kolejki do współpracy z programem, postępuj zgodnie z instrukcjami w temacie [Use Azure Portal, aby utworzyć](service-bus-quickstart-portal.md) kolejkę Service Bus w celu utworzenia kolejki. Zanotuj **Parametry połączenia** dla przestrzeni nazw Service Bus i nazwę utworzonej **kolejki** .
- Język Python 2,7 lub nowszy z zainstalowanym pakietem [Azure Service Bus języka Python](https://pypi.python.org/pypi/azure-servicebus) . Aby uzyskać więcej informacji, zobacz [Przewodnik instalacji języka Python](/azure/developer/python/azure-sdk-install). 

## <a name="send-messages-to-a-queue"></a>Wysyłanie komunikatów do kolejki

1. Dodaj następującą instrukcję importu. 

    ```python
    from azure.servicebus import ServiceBusClient, ServiceBusMessage
    ```
2. Dodaj następujące stałe. 

    ```python
    CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
    QUEUE_NAME = "<QUEUE NAME>"
    ```

    > [!IMPORTANT]
    > - Zamień na `<NAMESPACE CONNECTION STRING>` Parametry połączenia dla przestrzeni nazw Service Bus.
    > - Zamień `<QUEUE NAME>` na nazwę kolejki. 
3. Dodaj metodę w celu wysłania pojedynczej wiadomości.

    ```python
    def send_single_message(sender):
        # create a Service Bus message
        message = ServiceBusMessage("Single Message")
        # send the message to the queue
        sender.send_messages(message)
        print("Sent a single message")
    ```

    Nadawca jest obiektem, który działa jako klient dla utworzonej kolejki. Utworzysz go później i wyślesz jako argument do tej funkcji. 
4. Dodaj metodę, aby wysłać listę komunikatów.

    ```python
    def send_a_list_of_messages(sender):
        # create a list of messages
        messages = [ServiceBusMessage("Message in list") for _ in range(5)]
        # send the list of messages to the queue
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
        # send the batch of messages to the queue
        sender.send_messages(batch_message)
        print("Sent a batch of 10 messages")
    ```
6. Utwórz klienta Service Bus, a następnie obiekt nadawcy kolejki, aby wysyłać komunikaty.

    ```python
    # create a Service Bus client using the connection string
    servicebus_client = ServiceBusClient.from_connection_string(conn_str=CONNECTION_STR, logging_enable=True)
    with servicebus_client:
        # get a Queue Sender object to send messages to the queue
        sender = servicebus_client.get_queue_sender(queue_name=QUEUE_NAME)
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
 
## <a name="receive-messages-from-a-queue"></a>Odbieranie komunikatów z kolejki
Dodaj następujący kod po instrukcji Print. Ten kod ciągle otrzymuje nowe wiadomości, dopóki nie otrzyma żadnych nowych komunikatów przez 5 ( `max_wait_time` ) s. 

```python
with servicebus_client:
    # get the Queue Receiver object for the queue
    receiver = servicebus_client.get_queue_receiver(queue_name=QUEUE_NAME, max_wait_time=5)
    with receiver:
        for msg in receiver:
            print("Received: " + str(msg))
            # complete the message so that the message is removed from the queue
            receiver.complete_message(msg)
```

## <a name="full-code"></a>Pełny kod

```python
# import os
from azure.servicebus import ServiceBusClient, ServiceBusMessage

CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
QUEUE_NAME = "<QUEUE NAME>"

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
    sender = servicebus_client.get_queue_sender(queue_name=QUEUE_NAME)
    with sender:
        send_single_message(sender)
        send_a_list_of_messages(sender)
        send_batch_message(sender)

print("Done sending messages")
print("-----------------------")

with servicebus_client:
    receiver = servicebus_client.get_queue_receiver(queue_name=QUEUE_NAME, max_wait_time=5)
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

Wybierz kolejkę na tej stronie **przeglądu** , aby przejść do strony **kolejki Service Bus** . Na tej stronie można także zobaczyć liczbę wiadomości **przychodzących** i **wychodzących** . Widoczne są również inne informacje, takie jak **bieżący rozmiar** kolejki i **Liczba aktywnych komunikatów**. 

:::image type="content" source="./media/service-bus-python-how-to-use-queues/queue-details.png" alt-text="Szczegóły kolejki":::


## <a name="next-steps"></a>Następne kroki
Zapoznaj się z poniższą dokumentacją i przykładami: 

- [Azure Service Bus Biblioteka kliencka dla języka Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus)
- [Przykłady](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus/samples). 
    - Folder **sync_samples** zawiera przykłady pokazujące, jak w sposób synchroniczny korzystać z Service Bus. W tym przewodniku szybki start użyto tej metody. 
    - Folder **async_samples** zawiera przykłady pokazujące, jak interakcyjnie korzystać z Service Bus w sposób asynchroniczny. 
- [Dokumentacja referencyjna platformy Azure-ServiceBus](https://docs.microsoft.com/python/api/azure-servicebus/azure.servicebus?view=azure-python-preview&preserve-view=true)

