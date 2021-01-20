---
title: Jak korzystać z usługi Azure Queue Storage w języku Python — Azure Storage
description: Dowiedz się, jak używać Queue Storage platformy Azure z poziomu języka Python do tworzenia i usuwania kolejek oraz wstawiania, pobierania i usuwania komunikatów.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 01/19/2021
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: seo-javascript-october2019, devx-track-python
ms.openlocfilehash: 82d18fd79b10a8500cfd9191f143438d69fda401
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98600793"
---
# <a name="how-to-use-azure-queue-storage-from-python"></a>Jak korzystać z usługi Azure Queue Storage w języku Python

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Omówienie

W tym artykule przedstawiono typowe scenariusze korzystające z usługi Azure Queue Storage. Omówione scenariusze obejmują Wstawianie, wgląd, pobieranie i usuwanie komunikatów w kolejce. Podano również kod służący do tworzenia i usuwania kolejek.

Przykłady w tym artykule są zapisywane w języku Python i używają [biblioteki klienta queue storage platformy Azure dla języka Python](https://github.com/Azure/Azure-SDK-for-Python/tree/master/sdk/storage/azure-storage-queue). Aby uzyskać więcej informacji o kolejkach, zobacz sekcję [następne kroki](#next-steps) .

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Pobierz i Zainstaluj zestaw SDK usługi Azure Storage dla języka Python

[Zestaw SDK usługi Azure Storage dla języka Python](https://github.com/azure/azure-storage-python) wymaga języka Python v 2.7, v 3.3 lub nowszego.

### <a name="install-via-pypi"></a>Zainstaluj za pomocą PyPI

Aby zainstalować program za pośrednictwem indeksu pakietów języka Python (PyPI), wpisz:

# <a name="python-v12"></a>[V12 Python](#tab/python)

```console
pip install azure-storage-queue
```

# <a name="python-v2"></a>[Python v2](#tab/python2)

```console
pip install azure-storage-queue==2.1.0
```

---

> [!NOTE]
> W przypadku uaktualniania z zestawu SDK usługi Azure Storage dla języka Python v 0.36 lub starszego, `pip uninstall azure-storage` przed zainstalowaniem najnowszego pakietu Odinstaluj starszy zestaw SDK.

Aby zapoznać się z alternatywnymi metodami instalacji, zobacz [zestaw Azure SDK dla języka Python](https://github.com/Azure/Azure-SDK-for-Python).

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="configure-your-application-to-access-queue-storage"></a>Skonfiguruj aplikację pod kątem dostępu do Queue Storage

# <a name="python-v12"></a>[V12 Python](#tab/python)

[`QueueClient`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient)Obiekt umożliwia korzystanie z kolejki. Dodaj następujący kod w górnej części każdego pliku języka Python, w którym chcesz programowo uzyskać dostęp do kolejki platformy Azure:

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_ImportStatements":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

[`QueueService`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true)Obiekt umożliwia korzystanie z kolejek. Poniższy kod tworzy `QueueService` obiekt. Dodaj następujący kod w górnej części każdego pliku języka Python, w którym chcesz programowo uzyskać dostęp do usługi Azure Storage:

```python
from azure.storage.queue import (
        QueueService,
        QueueMessageFormat
)

import os, uuid
```

---

`os`Pakiet zapewnia obsługę pobierania zmiennej środowiskowej. `uuid`Pakiet zapewnia obsługę generowania unikatowego identyfikatora dla nazwy kolejki.

## <a name="create-a-queue"></a>Tworzenie kolejki

Parametry połączenia są pobierane ze `AZURE_STORAGE_CONNECTION_STRING` zdefiniowanej wcześniej zmiennej środowiskowej.

# <a name="python-v12"></a>[V12 Python](#tab/python)

Poniższy kod tworzy `QueueClient` Obiekt przy użyciu parametrów połączenia magazynu.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_CreateQueue":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Poniższy kod tworzy `QueueService` Obiekt przy użyciu parametrów połączenia magazynu.

```python
# Retrieve the connection string from an environment
# variable named AZURE_STORAGE_CONNECTION_STRING
connect_str = os.getenv("AZURE_STORAGE_CONNECTION_STRING")

# Create a unique name for the queue
queue_name = "queue-" + str(uuid.uuid4())

# Create a QueueService object which will
# be used to create and manipulate the queue
print("Creating queue: " + queue_name)
queue_service = QueueService(connection_string=connect_str)

# Create the queue
queue_service.create_queue(queue_name)
```

---

## <a name="insert-a-message-into-a-queue"></a>Wstawianie komunikatu do kolejki

# <a name="python-v12"></a>[V12 Python](#tab/python)

Aby wstawić komunikat do kolejki, użyj [`send_message`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#send-message-content----kwargs-) metody.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_AddMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Aby wstawić komunikat do kolejki, użyj [`put_message`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#put-message-queue-name--content--visibility-timeout-none--time-to-live-none--timeout-none-) metody w celu utworzenia nowej wiadomości i dodania jej do kolejki.

```python
message = u"Hello, World"
print("Adding message: " + message)
queue_service.put_message(queue_name, message)
```

---

Wiadomości w kolejce platformy Azure są przechowywane jako tekst. Jeśli chcesz przechowywać dane binarne, skonfiguruj kodowanie base64 i funkcje dekodowania przed umieszczeniem komunikatu w kolejce.

# <a name="python-v12"></a>[V12 Python](#tab/python)

Konfigurowanie funkcji kodowania i dekodowania Base64 w obiekcie klient kolejki.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_EncodeMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Skonfiguruj funkcje kodowania i dekodowania Base64 w programie Queue Storage obiektu.

```python
# Setup Base64 encoding and decoding functions
queue_service.encode_function = QueueMessageFormat.binary_base64encode
queue_service.decode_function = QueueMessageFormat.binary_base64decode
```

---

## <a name="peek-at-messages"></a>Wgląd w wiadomości

# <a name="python-v12"></a>[V12 Python](#tab/python)

Możesz uzyskać wgląd w wiadomości bez usuwania ich z kolejki, wywołując [`peek_messages`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#peek-messages-max-messages-none----kwargs-) metodę. Domyślnie ta metoda służy do wglądu w jeden komunikat.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_PeekMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Możesz uzyskać wgląd w wiadomości bez usuwania ich z kolejki, wywołując [`peek_messages`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#peek-messages-queue-name--num-messages-none--timeout-none-) metodę. Domyślnie ta metoda służy do wglądu w jeden komunikat.

```python
messages = queue_service.peek_messages(queue_name)

for peeked_message in messages:
    print("Peeked message: " + peeked_message.content)
```

---

## <a name="change-the-contents-of-a-queued-message"></a>Zmiana zawartości komunikatu w kolejce

Możesz zmienić zawartość komunikatu w kolejce. Jeśli komunikat reprezentuje zadanie, można użyć tej funkcji do zaktualizowania stanu zadania.

# <a name="python-v12"></a>[V12 Python](#tab/python)

Poniższy kod używa [`update_message`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#update-message-message--pop-receipt-none--content-none----kwargs-) metody do zaktualizowania wiadomości. Limit czasu widoczności jest ustawiony na 0, co oznacza, że komunikat natychmiast pojawia się, a zawartość jest aktualizowana.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_ChangeMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Poniższy kod używa [`update_message`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#update-message-queue-name--message-id--pop-receipt--visibility-timeout--content-none--timeout-none-) metody do zaktualizowania wiadomości. Limit czasu widoczności jest ustawiony na 0, co oznacza, że komunikat natychmiast pojawia się, a zawartość jest aktualizowana.

```python
messages = queue_service.get_messages(queue_name)

for message in messages:
    queue_service.update_message(
        queue_name, message.id, message.pop_receipt, 0, u"Hello, World Again")
```

---

## <a name="get-the-queue-length"></a>Pobieranie długości kolejki

Możesz uzyskać szacunkową liczbę komunikatów w kolejce.

# <a name="python-v12"></a>[V12 Python](#tab/python)

Metoda [get_queue_properties](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#get-queue-properties---kwargs-) zwraca właściwości kolejki, w tym `approximate_message_count` .

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_GetQueueLength":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

[`get_queue_metadata`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#get-queue-metadata-queue-name--timeout-none-)Metoda zwraca właściwości kolejki, włącznie z `approximate_message_count` .

```python
metadata = queue_service.get_queue_metadata(queue_name)
count = metadata.approximate_message_count
print("Message count: " + str(count))
```

---

Wynik jest przybliżony tylko dlatego, że komunikaty mogą być dodawane lub usuwane po udzieleniu odpowiedzi na żądanie przez usługę.

## <a name="dequeue-messages"></a>Dequeueing messages

Usuń komunikat z kolejki w dwóch krokach. Jeśli Twój kod nie może przetworzyć komunikatu, ten dwuetapowy proces zapewnia, że można uzyskać ten sam komunikat i spróbować ponownie. Wywołaj `delete_message` po pomyślnym przetworzeniu komunikatu.

# <a name="python-v12"></a>[V12 Python](#tab/python)

Gdy wywołasz [receive_messages](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-), domyślnie otrzymujesz następny komunikat w kolejce. Komunikat zwrócony z programu `receive_messages` stał się niewidoczny dla każdego innego kodu odczytującego komunikaty z tej kolejki. Domyślnie komunikat pozostanie niewidoczny przez 30 sekund. Aby zakończyć usuwanie komunikatu z kolejki, należy również wywołać [delete_message](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#delete-message-message--pop-receipt-none----kwargs-).

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_DequeueMessages":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Gdy wywołasz [get_messages](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-), domyślnie otrzymujesz następny komunikat w kolejce. Komunikat zwrócony z programu `get_messages` stał się niewidoczny dla każdego innego kodu odczytującego komunikaty z tej kolejki. Domyślnie komunikat pozostanie niewidoczny przez 30 sekund. Aby zakończyć usuwanie komunikatu z kolejki, należy również wywołać [delete_message](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#delete-message-queue-name--message-id--pop-receipt--timeout-none-).

```python
messages = queue_service.get_messages(queue_name)

for message in messages:
    print("Deleting message: " + message.content)
    queue_service.delete_message(queue_name, message.id, message.pop_receipt)
```

---

Istnieją dwa sposoby dostosowania pobierania komunikatów z kolejki. Po pierwsze można uzyskać komunikaty zbiorczo (do 32). Po drugie można ustawić dłuższy lub krótszy limit czasu niewidoczności, dzięki czemu kod będzie mieć więcej lub mniej czasu na pełne przetworzenie każdego komunikatu.

# <a name="python-v12"></a>[V12 Python](#tab/python)

Poniższy przykład kodu używa [`receive_messages`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) metody do pobierania komunikatów w partiach. Następnie przetwarza każdy komunikat w każdej partii przy użyciu zagnieżdżonej `for` pętli. Ustawia również limitu czasu niewidoczności na pięć minut dla każdego komunikatu.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_DequeueByPage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Poniższy przykład kodu używa [`get_messages`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-) metody do pobierania 16 komunikatów w jednym wywołaniu. Następnie przetwarza każdy komunikat przy użyciu `for` pętli. Ustawia również limitu czasu niewidoczności na pięć minut dla każdego komunikatu.

```python
messages = queue_service.get_messages(queue_name, num_messages=16, visibility_timeout=5*60)

for message in messages:
    print("Deleting message: " + message.content)
    queue_service.delete_message(queue_name, message.id, message.pop_receipt)
```

---

## <a name="delete-a-queue"></a>Usuwanie kolejki

# <a name="python-v12"></a>[V12 Python](#tab/python)

Aby usunąć kolejkę i wszystkie znajdujące się w niej komunikaty, wywołaj [`delete_queue`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#delete-queue---kwargs-) metodę.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_DeleteQueue":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Aby usunąć kolejkę i wszystkie znajdujące się w niej komunikaty, wywołaj [`delete_queue`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#delete-queue-queue-name--fail-not-exist-false--timeout-none-) metodę.

```python
print("Deleting queue: " + queue_name)
queue_service.delete_queue(queue_name)
```

---

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz już podstawy Queue Storage, Skorzystaj z poniższych linków, aby dowiedzieć się więcej.

- [Dokumentacja interfejsu API języka Python platformy Azure Queue Storage](/python/api/azure-storage-queue)
- [Centrum deweloperów języka Python](https://azure.microsoft.com/develop/python/)
- [Dokumentacja interfejsu API REST usługi Azure Storage](/rest/api/storageservices/)
