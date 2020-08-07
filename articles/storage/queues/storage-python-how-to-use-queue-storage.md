---
title: Jak korzystać z usługi Azure queue storage v 2.1 z poziomu języka Python — Azure Storage
description: Dowiedz się, jak używać platformy Azure usługa kolejki v 2.1 z poziomu języka Python do tworzenia i usuwania kolejek oraz wstawiania, pobierania i usuwania komunikatów.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/17/2019
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.custom: seo-javascript-october2019, devx-track-python
ms.openlocfilehash: 97cb41a47b18d403881432098ecdda9bb26146d2
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87848200"
---
# <a name="how-to-use-azure-queue-storage-v21-from-python"></a>Jak korzystać z usługi Azure queue storage w wersji 2.1 z poziomu języka Python

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

W tym artykule przedstawiono typowe scenariusze korzystające z usługi Azure queue storage. Omówione scenariusze obejmują Wstawianie, wgląd, pobieranie i usuwanie komunikatów w kolejce oraz tworzenie i usuwanie kolejek.

> [!IMPORTANT]
> Ten artykuł odnosi się do starszej wersji biblioteki klienta usługi Azure Storage dla języka Python. Aby rozpocząć pracę z najnowszą wersją, zobacz [Szybki Start: Biblioteka kliencka usługi Azure queue storage dla języka Python](storage-quickstart-queues-python.md)

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Omówienie

Przykłady w tym artykule są zapisywane w języku Python i używają [zestawu SDK Microsoft Azure Storage dla języka Python]. Aby uzyskać więcej informacji o kolejkach, zobacz sekcję [następne kroki](#next-steps) .

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Pobierz i Zainstaluj zestaw SDK usługi Azure Storage dla języka Python

[Zestaw SDK usługi Azure Storage dla języka Python](https://github.com/azure/azure-storage-python) wymaga języka Python w wersji 2,7, 3,3 lub nowszej.
 
### <a name="install-via-pypi"></a>Zainstaluj za pomocą PyPi

Aby zainstalować program za pośrednictwem indeksu pakietów języka Python (PyPI), wpisz:

```bash
pip install azure-storage-queue==2.1.0
```

> [!NOTE]
> Jeśli uaktualniasz z zestawu SDK usługi Azure Storage dla języka Python w wersji 0,36 lub starszej, Odinstaluj starszy zestaw SDK przy użyciu programu `pip uninstall azure-storage` przed zainstalowaniem najnowszego pakietu.

Aby zapoznać się z alternatywnymi metodami instalacji, zobacz [zestaw SDK usługi Azure Storage dla języka Python](https://github.com/Azure/azure-storage-python/).

## <a name="view-the-sample-application"></a>Wyświetlanie przykładowej aplikacji

Aby wyświetlić i uruchomić przykładową aplikację, która pokazuje, jak używać języka Python z kolejkami platformy Azure, zobacz [Azure Storage: wprowadzenie z kolejkami platformy Azure w języku Python](https://github.com/Azure-Samples/storage-queue-python-getting-started). 

Aby uruchomić przykładową aplikację, upewnij się, że zainstalowano `azure-storage-queue` `azure-storage-common` pakiety i.

## <a name="create-a-queue"></a>Tworzenie kolejki

Obiekt [QueueService](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice) umożliwia korzystanie z kolejek. Poniższy kod tworzy `QueueService` obiekt. Dodaj następujące elementy w górnej części każdego pliku języka Python, w którym chcesz programowo uzyskać dostęp do usługi Azure Storage:

```python
from azure.storage.queue import QueueService
```

Poniższy kod tworzy `QueueService` Obiekt przy użyciu nazwy konta magazynu i klucza konta. Zastąp *konto* i *klucze* nazwą konta i kluczem.

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="insert-a-message-into-a-queue"></a>Wstawianie komunikatu do kolejki

Aby wstawić komunikat do kolejki, użyj metody [put_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#put-message-queue-name--content--visibility-timeout-none--time-to-live-none--timeout-none-) , aby utworzyć nowy komunikat i dodać go do kolejki.

```python
queue_service.put_message('taskqueue', u'Hello World')
```

Wiadomości w kolejce platformy Azure są przechowywane jako tekst. Jeśli chcesz przechowywać dane binarne, skonfiguruj kodowanie base64 i funkcje dekodowania w obiekcie usługi kolejki przed umieszczeniem komunikatu w kolejce.

```python
# setup queue Base64 encoding and decoding functions
queue_service.encode_function = QueueMessageFormat.binary_base64encode
queue_service.decode_function = QueueMessageFormat.binary_base64decode
```

## <a name="peek-at-the-next-message"></a>Podgląd kolejnego komunikatu

Możesz uzyskać wgląd w komunikat z przodu kolejki bez usuwania go z kolejki, wywołując metodę [peek_messages](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#peek-messages-queue-name--num-messages-none--timeout-none-) . Domyślnie `peek_messages` wgląd w jeden komunikat.

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="dequeue-messages"></a>Dequeueing messages

Kod usuwa komunikat z kolejki w dwóch krokach. Gdy wywołasz [get_messages](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-), domyślnie otrzymujesz następny komunikat w kolejce. Komunikat zwrócony z programu `get_messages` stał się niewidoczny dla każdego innego kodu odczytującego komunikaty z tej kolejki. Domyślnie komunikat pozostanie niewidoczny przez 30 sekund. Aby zakończyć usuwanie komunikatu z kolejki, należy również wywołać [delete_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-message-queue-name--message-id--pop-receipt--timeout-none-). Ten dwuetapowy proces usuwania komunikatu gwarantuje, że gdy kod nie może przetworzyć komunikatu z powodu awarii sprzętu lub oprogramowania, inne wystąpienie kodu może uzyskać ten sam komunikat i spróbować ponownie. Kod wywołuje `delete_message` się bezpośrednio po przetworzeniu komunikatu.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

Istnieją dwa sposoby dostosowania pobierania komunikatów z kolejki. Po pierwsze można uzyskać komunikaty zbiorczo (do 32). Po drugie można ustawić dłuższy lub krótszy limit czasu niewidoczności, dzięki czemu kod będzie mieć więcej lub mniej czasu na pełne przetworzenie każdego komunikatu. Poniższy przykład kodu używa `get_messages` metody do pobierania 16 komunikatów w jednym wywołaniu. Następnie przetwarza każdy komunikat przy użyciu pętli for. Ustawia również limitu czasu niewidoczności na pięć minut dla każdego komunikatu.

```python
messages = queue_service.get_messages(
    'taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

## <a name="change-the-contents-of-a-queued-message"></a>Zmiana zawartości komunikatu w kolejce

Możesz zmienić zawartość komunikatu w kolejce. Jeśli komunikat reprezentuje zadanie robocze, możesz użyć tej funkcji, aby zaktualizować stan zadania. W poniższym kodzie zastosowano metodę [update_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#update-message-queue-name--message-id--pop-receipt--visibility-timeout--content-none--timeout-none-) , aby zaktualizować komunikat. Limit czasu widoczności jest ustawiony na 0, co oznacza, że komunikat natychmiast pojawia się, a zawartość jest aktualizowana.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message(
        'taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="get-the-queue-length"></a>Pobieranie długości kolejki

Możesz uzyskać szacunkową liczbę komunikatów w kolejce. Metoda [get_queue_metadata](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-queue-metadata-queue-name--timeout-none-) żąda, aby usługa kolejki zwracała metadane dotyczące kolejki i `approximate_message_count` . Wynik jest przybliżony, ponieważ komunikaty mogą być dodawane lub usuwane po udzieleniu odpowiedzi przez usługę kolejki na żądanie.

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="delete-a-queue"></a>Usuwanie kolejki

Aby usunąć kolejkę i wszystkie znajdujące się w niej komunikaty, wywołaj metodę [delete_queue](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-queue-queue-name--fail-not-exist-false--timeout-none-) .

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz już podstawy magazynu kolejek, Skorzystaj z poniższych linków, aby dowiedzieć się więcej.

* [Dokumentacja interfejsu API języka Python usługi Azure Queues](/python/api/azure-storage-queue)
* [Centrum deweloperów języka Python](https://azure.microsoft.com/develop/python/)
* [Interfejs API REST usług Azure Storage](https://msdn.microsoft.com/library/azure/dd179355)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[Zestaw SDK Microsoft Azure Storage dla języka Python]: https://github.com/Azure/azure-storage-python
