---
title: 'Szybki Start: Biblioteka usługi Azure queue storage V12 — Python'
description: Dowiedz się, jak utworzyć kolejkę i dodać do niej komunikaty przy użyciu biblioteki V12 w usłudze Azure Queue w języku Python. Następnie dowiesz się, jak odczytywać i usuwać wiadomości z kolejki. Dowiesz się również, jak usunąć kolejkę.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/10/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: 50f15d041f9bcea66400eda0877e9d7914335d74
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93345725"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-python"></a>Szybki Start: V12 biblioteki klienta usługi Azure queue storage dla języka Python

Rozpocznij pracę z biblioteką klienta usługi Azure queue storage w wersji 12 dla języka Python. Azure queue storage to usługa służąca do przechowywania dużej liczby komunikatów do późniejszego pobrania i przetworzenia. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.

Użyj biblioteki klienta usługi Azure queue storage V12 dla języka Python, aby:

- Tworzenie kolejki
- Dodawanie komunikatów do kolejki
- Wgląd w wiadomości w kolejce
- Aktualizowanie komunikatu w kolejce
- Odbieranie komunikatów z kolejki
- Usuwanie komunikatów z kolejki
- Usuwanie kolejki

Dodatkowe zasoby:

- [Dokumentacja referencyjna interfejsu API](/python/api/azure-storage-queue/index)
- [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue)
- [Pakiet (indeks pakietu języka Python)](https://pypi.org/project/azure-storage-queue/)
- [Samples](../common/storage-samples-python.md?toc=%252fazure%252fstorage%252fqueues%252ftoc.json#queue-samples)

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
- Konto magazynu platformy Azure — [Tworzenie konta magazynu](../common/storage-account-create.md)
- Język [Python](https://www.python.org/downloads/) dla systemu operacyjnego — 2,7, 3,5 lub nowszy

## <a name="setting-up"></a>Konfigurowanie

W tej sekcji omówiono przygotowanie projektu do pracy z biblioteką klienta usługi Azure queue storage V12 dla języka Python.

### <a name="create-the-project"></a>Tworzenie projektu

Tworzenie aplikacji w języku Python o nazwie *Queues — szybki start-V12*.

1. W oknie konsoli (na przykład cmd, PowerShell lub bash) Utwórz nowy katalog dla projektu.

    ```console
    mkdir queues-quickstart-v12
    ```

1. Przejdź do nowo utworzonych *kolejek — szybki start-V12* .

    ```console
    cd queues-quickstart-v12
    ```

### <a name="install-the-package"></a>Zainstaluj pakiet

Zainstaluj bibliotekę klienta usługi Azure Blob Storage dla pakietu języka Python za pomocą `pip install` polecenia.

```console
pip install azure-storage-queue
```

To polecenie powoduje zainstalowanie biblioteki klienta usługi Azure queue storage dla pakietu języka Python i wszystkich bibliotek, od których jest ona zależna. W tym przypadku jest to tylko podstawowa Biblioteka platformy Azure dla języka Python.

### <a name="set-up-the-app-framework"></a>Konfigurowanie struktury aplikacji

1. Otwórz nowy plik tekstowy w edytorze kodu
1. Dodaj `import` instrukcje
1. Utwórz strukturę dla programu, w tym bardzo podstawową obsługę wyjątków

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

1. Zapisz nowy plik jako *Queues-QuickStart-V12.py* w katalogu *Queues-Start-V12* .

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Model obiektów

Azure Queue Storage to usługa służąca do przechowywania dużej liczby komunikatów. Komunikat w kolejce może mieć rozmiar do 64 KB. Kolejka może zawierać miliony komunikatów, do łącznego limitu pojemności konta magazynu. Kolejki są często używane do tworzenia zaległości prac do przetwarzania asynchronicznego. Magazyn kolejek oferuje trzy typy zasobów:

- Konto magazynu
- Kolejka na koncie magazynu
- Komunikaty w kolejce

Na poniższym diagramie przedstawiono relacje między tymi zasobami.

![Diagram architektury magazynu kolejki](./media/storage-queues-introduction/queue1.png)

Użyj następujących klas języka Python do korzystania z tych zasobów:

- [QueueServiceClient](/python/api/azure-storage-queue/azure.storage.queue.queueserviceclient): `QueueServiceClient` umożliwia zarządzanie wszystkimi kolejkami na koncie magazynu.
- [QueueClient](/python/api/azure-storage-queue/azure.storage.queue.queueclient): `QueueClient` Klasa umożliwia zarządzanie pojedynczą kolejką i jej komunikatami oraz manipulowanie nimi.
- [QueueMessage](/python/api/azure-storage-queue/azure.storage.queue.queuemessage): `QueueMessage` Klasa reprezentuje poszczególne obiekty zwracane podczas wywoływania [receive_messages](/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) w kolejce.

## <a name="code-examples"></a>Przykłady kodu

Te przykładowe fragmenty kodu pokazują, jak wykonać następujące czynności w bibliotece klienta usługi Azure queue storage dla języka Python:

- [Pobieranie parametrów połączenia](#get-the-connection-string)
- [Tworzenie kolejki](#create-a-queue)
- [Dodawanie komunikatów do kolejki](#add-messages-to-a-queue)
- [Wgląd w wiadomości w kolejce](#peek-at-messages-in-a-queue)
- [Aktualizowanie komunikatu w kolejce](#update-a-message-in-a-queue)
- [Odbieranie komunikatów z kolejki](#receive-messages-from-a-queue)
- [Usuwanie komunikatów z kolejki](#delete-messages-from-a-queue)
- [Usuwanie kolejki](#delete-a-queue)

### <a name="get-the-connection-string"></a>Pobieranie parametrów połączenia

Poniższy kod pobiera parametry połączenia dla konta magazynu. Parametry połączenia są przechowywane w zmiennej środowiskowej utworzonej w sekcji [Konfigurowanie parametrów połączenia magazynu](#configure-your-storage-connection-string) .

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

Określ nazwę nowej kolejki. Poniższy kod dołącza wartość identyfikatora UUID do nazwy kolejki, aby upewnić się, że jest ona unikatowa.

> [!IMPORTANT]
> Nazwy kolejek mogą zawierać tylko małe litery, cyfry i łączniki, a także muszą zaczynać się literą lub cyfrą. Przed i za każdym łącznikiem musi znajdować się znak inny niż łącznik. Nazwa musi mieć również długość od 3 do 63 znaków. Aby uzyskać więcej informacji na temat nazewnictwa kolejek, zobacz [nazywanie kolejek i metadanych](/rest/api/storageservices/naming-queues-and-metadata).

Utwórz wystąpienie klasy [QueueClient](/python/api/azure-storage-queue/azure.storage.queue.queueclient) . Następnie Wywołaj metodę [create_queue](/python/api/azure-storage-queue/azure.storage.queue.queueclient#create-queue---kwargs-) , aby utworzyć kolejkę na koncie magazynu.

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

Poniższy fragment kodu dodaje komunikaty do kolejki, wywołując metodę [send_message](/python/api/azure-storage-queue/azure.storage.queue.queueclient#send-message-content----kwargs-) . Zapisuje także [QueueMessage](/python/api/azure-storage-queue/azure.storage.queue.queuemessage) zwracane z trzeciego `send_message` wywołania. `saved_message`Służy do aktualizacji zawartości wiadomości w dalszej części tego programu.

Dodaj ten kod na końcu `try` bloku:

```python
    print("\nAdding messages to the queue...")

    # Send several messages to the queue
    queue_client.send_message(u"First message")
    queue_client.send_message(u"Second message")
    saved_message = queue_client.send_message(u"Third message")
```

### <a name="peek-at-messages-in-a-queue"></a>Wgląd w wiadomości w kolejce

Zajrzyj do komunikatów w kolejce, wywołując metodę [peek_messages](/python/api/azure-storage-queue/azure.storage.queue.queueclient#peek-messages-max-messages-none----kwargs-) . `peek_messages`Metoda pobiera co najmniej jeden komunikat z przodu kolejki, ale nie zmienia widoczności komunikatu.

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

Zaktualizuj zawartość komunikatu, wywołując metodę [update_message](/python/api/azure-storage-queue/azure.storage.queue.queueclient#update-message-message--pop-receipt-none--content-none----kwargs-) . `update_message`Metoda może zmienić limit czasu i treść wiadomości. Zawartość komunikatu musi być ciągiem zakodowanym w formacie UTF-8, który ma rozmiar do 64 KB. Wraz z nową zawartością można przekazać wartości z wiadomości zapisanej wcześniej w kodzie. `saved_message`Wartości identyfikują, którą wiadomość należy zaktualizować.

```python
    print("\nUpdating the third message in the queue...")

    # Update a message using the message saved when calling send_message earlier
    queue_client.update_message(saved_message, pop_receipt=saved_message.pop_receipt, \
        content="Third message has been updated")
```

### <a name="receive-messages-from-a-queue"></a>Odbieranie komunikatów z kolejki

Pobierz wcześniej dodane wiadomości, wywołując metodę [receive_messages](/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) .

Dodaj ten kod na końcu `try` bloku:

```python
    print("\nReceiving messages from the queue...")

    # Get messages from the queue
    messages = queue_client.receive_messages(messages_per_page=5)
```

### <a name="delete-messages-from-a-queue"></a>Usuwanie komunikatów z kolejki

Usuwanie wiadomości z kolejki po ich odebraniu i przetworzeniu. W takim przypadku przetwarzanie właśnie wyświetla komunikat w konsoli programu.

Aplikacja wstrzymuje się do wprowadzania danych przez użytkownika, wywołując przed przetworzeniem `input` i usunięciem komunikatów. Przed usunięciem [Azure Portal](https://portal.azure.com) Sprawdź, czy zasoby zostały utworzone prawidłowo. Wszystkie komunikaty, które nie zostały jawnie usunięte, zostaną ostatecznie wyświetlone w kolejce w celu przetworzenia ich przez inną szansę.

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

Poniższy kod czyści zasoby utworzone przez aplikację przez usunięcie kolejki przy użyciu metody [delete_queue](/python/api/azure-storage-queue/azure.storage.queue.queueclient#delete-queue---kwargs-) .

Dodaj ten kod na końcu `try` bloku i Zapisz plik:

```python
    print("\nPress Enter key to delete the queue...")
    input()

    # Clean up
    print("Deleting queue...")
    queue_client.delete_queue()

    print("Done")
```

## <a name="run-the-code"></a>Uruchamianie kodu

Ta aplikacja tworzy i dodaje trzy komunikaty do kolejki platformy Azure. Kod wyświetla listę komunikatów w kolejce, a następnie pobiera i usuwa je przed usunięciem kolejki.

W oknie konsoli przejdź do katalogu zawierającego plik *Queues-QuickStart-V12.py* , a następnie wykonaj następujące `python` polecenie, aby uruchomić aplikację.

```console
python queues-quickstart-v12.py
```

Dane wyjściowe aplikacji są podobne do następujących:

```output
Azure Queue storage v12 - Python quickstart sample
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

Gdy aplikacja jest wstrzymywana przed odebraniem wiadomości, Sprawdź konto magazynu w [Azure Portal](https://portal.azure.com). Sprawdź, czy w kolejce znajdują się komunikaty.

Naciśnij klawisz **Enter** , aby odebrać i usunąć komunikaty. Po wyświetleniu monitu ponownie naciśnij klawisz **Enter** , aby usunąć kolejkę i zakończyć pokaz.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia kolejki i dodawania do niej komunikatów przy użyciu kodu języka Python. Następnie nauczysz się wglądu, pobierania i usuwania komunikatów. Na koniec wiesz już, jak usunąć kolejkę komunikatów.

Samouczki, przykłady, szybki start i inne dokumenty można znaleźć w temacie:

> [!div class="nextstepaction"]
> [Platforma Azure dla deweloperów języka Python](/azure/python/)

- Aby dowiedzieć się więcej, zobacz [biblioteki usługi Azure Storage dla języka Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage).
- Aby wyświetlić więcej przykładowych aplikacji usługi Azure queue storage, przejdź do [przykładów biblioteki klienta usługi Azure queue storage V12](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue/samples).
