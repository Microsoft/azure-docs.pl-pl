---
title: 'Szybki start: Azure Queue Storage klienta w wersji 12 — Python'
description: Dowiedz się, jak za pomocą Azure Queue Storage klienta w wersji 12 dla języka Python utworzyć kolejkę i dodać do niego komunikaty. Następnie dowiedz się, jak odczytywać i usuwać komunikaty z kolejki. Dowiesz się również, jak usunąć kolejkę.
author: twooley
ms.author: twooley
ms.date: 12/10/2019
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom:
- devx-track-python
- mode-api
ms.openlocfilehash: 68f68c32b160757e82d59b1dd5ee4847a06ec698
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534358"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-python"></a>Szybki start: Azure Queue Storage klienta w wersji 12 dla języka Python

Wprowadzenie do biblioteki Azure Queue Storage klienta w wersji 12 dla języka Python. Azure Queue Storage to usługa do przechowywania dużej liczby komunikatów do późniejszego pobierania i przetwarzania. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.

Użyj biblioteki Azure Queue Storage klienta w wersji 12 dla języka Python, aby:

- Tworzenie kolejki
- Dodawanie komunikatów do kolejki
- Wgląd w komunikaty w kolejce
- Aktualizowanie komunikatu w kolejce
- Odbieranie komunikatów z kolejki
- Usuwanie komunikatów z kolejki
- Usuwanie kolejki

Dodatkowe zasoby:

- [Dokumentacja referencyjna interfejsu API](/python/api/azure-storage-queue/index)
- [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue)
- [Package (Python Package Index)](https://pypi.org/project/azure-storage-queue/)
- [Samples](../common/storage-samples-python.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure [— utwórz subskrypcję bezpłatnie](https://azure.microsoft.com/free/)
- Konto usługi Azure Storage [— tworzenie konta magazynu](../common/storage-account-create.md)
- [Język Python](https://www.python.org/downloads/) dla systemu operacyjnego — 2.7 lub 3.6+

## <a name="setting-up"></a>Konfigurowanie

W tej sekcji ojęliśmy proces przygotowywania projektu do pracy z Azure Queue Storage klienta w wersji 12 dla języka Python.

### <a name="create-the-project"></a>Tworzenie projektu

Utwórz aplikację w języku Python o nazwie `queues-quickstart-v12` .

1. W oknie konsoli (takim jak cmd, PowerShell lub Bash) utwórz nowy katalog dla projektu.

    ```console
    mkdir queues-quickstart-v12
    ```

1. Przejdź do nowo utworzonego `queues-quickstart-v12` katalogu.

    ```console
    cd queues-quickstart-v12
    ```

### <a name="install-the-package"></a>Instalowanie pakietu

Zainstaluj bibliotekę Azure Blob Storage klienta dla pakietu języka Python przy użyciu `pip install` polecenia .

```console
pip install azure-storage-queue
```

To polecenie instaluje Azure Queue Storage klienta dla pakietu języka Python i wszystkich bibliotek, od których zależy. W tym przypadku jest to tylko podstawowa biblioteka platformy Azure dla języka Python.

### <a name="set-up-the-app-framework"></a>Konfigurowanie struktury aplikacji

1. Otwieranie nowego pliku tekstowego w edytorze kodu
1. Dodawanie `import` instrukcji
1. Utwórz strukturę programu, w tym bardzo podstawową obsługę wyjątków

    Oto kod:

    ```python
    import os, uuid
    from azure.storage.queue import QueueServiceClient, QueueClient, QueueMessage

    try:
        print("Azure Queue storage v12 - Python quickstart sample")
        # Quick start code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)

    ```

1. Zapisz nowy plik jako `queues-quickstart-v12.py` w `queues-quickstart-v12` katalogu .

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Model obiektów

Azure Queue Storage to usługa służąca do przechowywania dużej liczby komunikatów. Komunikat w kolejce może mieć rozmiar do 64 KB. Kolejka może zawierać miliony komunikatów do całkowitego limitu pojemności konta magazynu. Kolejki są często używane do tworzenia listy prac do asynchronicznego przetwarzania. Queue Storage oferuje trzy typy zasobów:

- Konto magazynu
- Kolejka na koncie magazynu
- Komunikaty w kolejce

Na poniższym diagramie przedstawiono relacje między tymi zasobami.

![Diagram architektury usługi Queue Storage](./media/storage-queues-introduction/queue1.png)

Użyj następujących klas języka Python, aby wchodzić w interakcje z tymi zasobami:

- [`QueueServiceClient`](/python/api/azure-storage-queue/azure.storage.queue.queueserviceclient): `QueueServiceClient` umożliwia zarządzanie wszystkimi kolejkami na koncie magazynu.
- [`QueueClient`](/python/api/azure-storage-queue/azure.storage.queue.queueclient): Klasa umożliwia zarządzanie pojedynczą kolejką i jej komunikatami oraz `QueueClient` manipulowanie nimi.
- [`QueueMessage`](/python/api/azure-storage-queue/azure.storage.queue.queuemessage): `QueueMessage` klasa reprezentuje poszczególne obiekty zwracane podczas wywoływania [`receive_messages`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) w kolejce.

## <a name="code-examples"></a>Przykłady kodu

Te przykładowe fragmenty kodu pokazują, jak wykonać następujące akcje przy użyciu Azure Queue Storage klienta dla języka Python:

- [Pobieranie parametrów połączenia](#get-the-connection-string)
- [Tworzenie kolejki](#create-a-queue)
- [Dodawanie komunikatów do kolejki](#add-messages-to-a-queue)
- [Wgląd w komunikaty w kolejce](#peek-at-messages-in-a-queue)
- [Aktualizowanie komunikatu w kolejce](#update-a-message-in-a-queue)
- [Odbieranie komunikatów z kolejki](#receive-messages-from-a-queue)
- [Usuwanie komunikatów z kolejki](#delete-messages-from-a-queue)
- [Usuwanie kolejki](#delete-a-queue)

### <a name="get-the-connection-string"></a>Pobieranie parametrów połączenia

Poniższy kod pobiera ciąg połączenia dla konta magazynu. W ciągu połączenia jest przechowywana zmienna środowiskowa utworzona w sekcji [Konfigurowanie parametrów połączenia](#configure-your-storage-connection-string) magazynu.

Dodaj ten kod wewnątrz `try` bloku:

```python
    # Retrieve the connection string for use with the application. The storage
    # connection string is stored in an environment variable on the machine
    # running the application called AZURE_STORAGE_CONNECTION_STRING. If the
    # environment variable is created after the application is launched in a
    # console or with Visual Studio, the shell or application needs to be
    # closed and reloaded to take the environment variable into account.
    connect_str = os.getenv('AZURE_STORAGE_CONNECTION_STRING')
```

### <a name="create-a-queue"></a>Tworzenie kolejki

Wybierz nazwę nowej kolejki. Poniższy kod dołącza wartość UUID do nazwy kolejki, aby upewnić się, że jest unikatowa.

> [!IMPORTANT]
> Nazwy kolejek mogą zawierać tylko małe litery, cyfry i łączniki i muszą zaczynać się literą lub cyfrą. Przed i za każdym łącznikiem musi znajdować się znak inny niż łącznik. Nazwa musi mieć również od 3 do 63 znaków. Aby uzyskać więcej informacji, zobacz [Naming queues and metadata (Nazewnictwo kolejek i metadanych).](/rest/api/storageservices/naming-queues-and-metadata)

Utwórz wystąpienie [`QueueClient`](/python/api/azure-storage-queue/azure.storage.queue.queueclient) klasy . Następnie wywołaj [`create_queue`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#create-queue---kwargs-) metodę , aby utworzyć kolejkę na koncie magazynu.

Dodaj ten kod na końcu `try` bloku:

```python
    # Create a unique name for the queue
    queue_name = "quickstartqueues-" + str(uuid.uuid4())

    print("Creating queue: " + queue_name)

    # Instantiate a QueueClient which will be
    # used to create and manipulate the queue
    queue_client = QueueClient.from_connection_string(connect_str, queue_name)

    # Create the queue
    queue_client.create_queue()
```

### <a name="add-messages-to-a-queue"></a>Dodawanie komunikatów do kolejki

Poniższy fragment kodu dodaje komunikaty do kolejki przez wywołanie [`send_message`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#send-message-content----kwargs-) metody . Zapisuje również [`QueueMessage`](/python/api/azure-storage-queue/azure.storage.queue.queuemessage) zwrócone z trzeciego `send_message` wywołania. Jest `saved_message` używany do aktualizowania zawartości komunikatu w dalszej części programu.

Dodaj ten kod na końcu `try` bloku:

```python
    print("\nAdding messages to the queue...")

    # Send several messages to the queue
    queue_client.send_message(u"First message")
    queue_client.send_message(u"Second message")
    saved_message = queue_client.send_message(u"Third message")
```

### <a name="peek-at-messages-in-a-queue"></a>Wgląd w komunikaty w kolejce

Zaglądaj do komunikatów w kolejce, wywołując [`peek_messages`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#peek-messages-max-messages-none----kwargs-) metodę . Ta metoda pobiera co najmniej jeden komunikat z przodu kolejki, ale nie zmienia widoczności komunikatu.

Dodaj ten kod na końcu `try` bloku:

```python
    print("\nPeek at the messages in the queue...")

    # Peek at messages in the queue
    peeked_messages = queue_client.peek_messages(max_messages=5)

    for peeked_message in peeked_messages:
        # Display the message
        print("Message: " + peeked_message.content)
```

### <a name="update-a-message-in-a-queue"></a>Aktualizowanie komunikatu w kolejce

Zaktualizuj zawartość komunikatu, wywołując [`update_message`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#update-message-message--pop-receipt-none--content-none----kwargs-) metodę . Ta metoda może zmienić limit czasu widoczności i zawartość komunikatu. Zawartość komunikatu musi być ciągiem zakodowanym w formacie UTF-8 o rozmiarze do 64 KB. Wraz z nową zawartością przekaż wartości z komunikatu zapisanego wcześniej w kodzie. Wartości `saved_message` identyfikują komunikat do zaktualizowania.

```python
    print("\nUpdating the third message in the queue...")

    # Update a message using the message saved when calling send_message earlier
    queue_client.update_message(saved_message, pop_receipt=saved_message.pop_receipt, \
        content="Third message has been updated")
```

### <a name="receive-messages-from-a-queue"></a>Odbieranie komunikatów z kolejki

Pobierz wcześniej dodane komunikaty, wywołując [`receive_messages`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) metodę .

Dodaj ten kod na końcu `try` bloku:

```python
    print("\nReceiving messages from the queue...")

    # Get messages from the queue
    messages = queue_client.receive_messages(messages_per_page=5)
```

### <a name="delete-messages-from-a-queue"></a>Usuwanie komunikatów z kolejki

Usuń komunikaty z kolejki po ich otrzymaniu i przetworzeniu. W takim przypadku przetwarzanie tylko wyświetla komunikat w konsoli.

Aplikacja wstrzymuje wprowadzanie danych przez użytkownika przez wywołanie metody przed rozpoczęciem przetwarzania `input` i usunięciem komunikatów. Przed [usunięciem Azure Portal](https://portal.azure.com) w aplikacji sprawdź, czy zasoby zostały utworzone prawidłowo. Wszystkie komunikaty, które nie zostały jawnie usunięte, staną się w końcu ponownie widoczne w kolejce na kolejną szansę ich przetwarzania.

Dodaj ten kod na końcu `try` bloku:

```python
    print("\nPress Enter key to 'process' messages and delete them from the queue...")
    input()

    for msg_batch in messages.by_page():
            for msg in msg_batch:
                # "Process" the message
                print(msg.content)
                # Let the service know we're finished with
                # the message and it can be safely deleted.
                queue_client.delete_message(msg)
```

### <a name="delete-a-queue"></a>Usuwanie kolejki

Poniższy kod czyści zasoby utworzone przez aplikację przez usunięcie kolejki przy użyciu [`delete_queue`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#delete-queue---kwargs-) metody .

Dodaj ten kod na końcu bloku `try` i zapisz plik:

```python
    print("\nPress Enter key to delete the queue...")
    input()

    # Clean up
    print("Deleting queue...")
    queue_client.delete_queue()

    print("Done")
```

## <a name="run-the-code"></a>Uruchamianie kodu

Ta aplikacja tworzy i dodaje trzy komunikaty do kolejki platformy Azure. Kod wyświetla listę komunikatów w kolejce, a następnie pobiera je i usuwa przed usunięciem kolejki.

W oknie konsoli przejdź do katalogu zawierającego plik, a następnie użyj `queues-quickstart-v12.py` następującego `python` polecenia, aby uruchomić aplikację.

```console
python queues-quickstart-v12.py
```

Dane wyjściowe aplikacji są podobne do następującego przykładu:

```output
Azure Queue Storage client library v12 - Python quickstart sample
Creating queue: quickstartqueues-cac365be-7ce6-4065-bd65-3756ea052cb8

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Receiving messages from the queue...

Press Enter key to 'process' messages and delete them from the queue...

First message
Second message
Third message has been updated

Press Enter key to delete the queue...

Deleting queue...
Done
```

Gdy aplikacja zostanie wstrzymana przed odebraniem komunikatów, sprawdź konto magazynu w Azure Portal [.](https://portal.azure.com) Sprawdź, czy komunikaty znajdują się w kolejce.

Naciśnij klawisz `Enter` , aby odbierać i usuwać komunikaty. Po wyświetleniu monitu naciśnij ponownie `Enter` klawisz , aby usunąć kolejkę i zakończyć pokaz.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start opisano sposób tworzenia kolejki i dodawania do niego komunikatów przy użyciu kodu w języku Python. Następnie dowiedzieliśmy się, jak podeglądać, pobierać i usuwać komunikaty. Na koniec dowiedzieliśmy się, jak usunąć kolejkę komunikatów.

Samouczki, przykłady, poradniki Szybki start i inne dokumenty można znaleźć na stronie:

> [!div class="nextstepaction"]
> [Azure dla deweloperów języka Python](/azure/python/)

- Aby dowiedzieć się więcej, zobacz [Biblioteki usługi Azure Storage dla języka Python.](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage)
- Aby uzyskać więcej Azure Queue Storage przykładowych aplikacji, zobacz Azure Queue Storage klienta w wersji [12 dla języka Python — przykłady.](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue/samples)
