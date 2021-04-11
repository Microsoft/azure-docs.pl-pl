---
title: Jak używać Queue Storage (C++) — Azure Storage
description: Dowiedz się, jak używać usługi Queue Storage na platformie Azure. Przykłady są zapisywane w języku C++.
author: twooley
ms.author: twooley
ms.reviewer: dineshm
ms.date: 07/16/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 0e8274378ed321dc7c3d6ddcfb85f5bb0a2f7993
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106276284"
---
# <a name="how-to-use-queue-storage-from-c"></a>Jak używać usługi Queue Storage z poziomu języka C++

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Omówienie

W tym przewodniku przedstawiono sposób wykonywania typowych scenariuszy przy użyciu usługi Azure Queue Storage. Przykłady są zapisywane w języku C++ i używają [biblioteki klienta usługi Azure Storage dla języka c++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). Omówione scenariusze obejmują **Wstawianie**, **wgląd**, **pobieranie** i **usuwanie** komunikatów w kolejce, a także **Tworzenie i usuwanie kolejek**.

> [!NOTE]
> Ten przewodnik dotyczy biblioteki klienta usługi Azure Storage dla języka C++ w 1.0.0 i nowszych. Zalecana wersja to biblioteka 2.2.0 klienta usługi Azure Storage, która jest dostępna za pośrednictwem programu [NuGet](https://www.nuget.org/packages/wastorage) lub [GitHub](https://github.com/Azure/azure-storage-cpp/).

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Tworzenie aplikacji języka C++

W tym przewodniku będziesz używać funkcji magazynu, które można uruchomić w aplikacji C++.

W tym celu należy zainstalować bibliotekę klienta usługi Azure Storage dla języka C++ i utworzyć konto usługi Azure Storage w ramach subskrypcji platformy Azure.

<!-- docutune:casing "Getting Started on Linux" -->

Aby zainstalować bibliotekę klienta usługi Azure Storage dla języka C++, można użyć następujących metod:

- System **Linux:** Postępuj zgodnie z instrukcjami podanymi w [bibliotece klienta usługi Azure Storage dla pliku Readme języka C++: wprowadzenie na](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux) stronie systemu Linux.
- **System Windows:** W systemie Windows Użyj [vcpkg](https://github.com/microsoft/vcpkg) jako Menedżera zależności. Aby zainicjować, postępuj zgodnie z [przewodnikiem Szybki Start](https://github.com/microsoft/vcpkg#quick-start) `vcpkg` . Następnie użyj następującego polecenia, aby zainstalować bibliotekę:

```powershell
.\vcpkg.exe install azure-storage-cpp
```

Przewodnik dotyczący sposobu tworzenia kodu źródłowego i eksportowania go do programu NuGet można znaleźć w pliku [README](https://github.com/Azure/azure-storage-cpp#download--install) .

## <a name="configure-your-application-to-access-queue-storage"></a>Skonfiguruj aplikację pod kątem dostępu do Queue Storage

Dodaj następujące instrukcje include na początku pliku języka C++, w którym chcesz używać interfejsów API usługi Azure Storage w celu uzyskiwania dostępu do kolejek:

```cpp
#include <was/storage_account.h>
#include <was/queue.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>Konfigurowanie parametrów połączenia usługi Azure Storage

Klient usługi Azure Storage korzysta z parametrów połączenia magazynu w celu przechowywania punktów końcowych i poświadczeń w celu uzyskania dostępu do usług zarządzania danymi. W przypadku uruchamiania w aplikacji klienckiej należy podać parametry połączenia magazynu w następującym formacie, używając nazwy konta magazynu i klucza dostępu do magazynu dla konta magazynu wymienionego w [Azure Portal](https://portal.azure.com) `AccountName` `AccountKey` wartości i. Aby uzyskać informacje na temat kont magazynu i kluczy dostępu, zobacz [Informacje o kontach usługi Azure Storage](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json). W tym przykładzie pokazano, jak można zadeklarować pole statyczne w celu przechowywania parametrów połączenia:

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

Aby przetestować aplikację na komputerze lokalnym z systemem Windows, można użyć [emulatora magazynu azurite](../common/storage-use-azurite.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json). Azurite to narzędzie, które symuluje Blob Storage platformy Azure i Queue Storage na lokalnym komputerze deweloperskim. W tym przykładzie pokazano, jak można zadeklarować pole statyczne w celu przechowywania parametrów połączenia z lokalnym emulatorem magazynu:

```cpp
// Define the connection-string with Azurite.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Aby rozpocząć azurite, zobacz [Używanie emulatora azurite na potrzeby tworzenia lokalnych magazynów platformy Azure](../common/storage-use-azurite.md).

W poniższych przykładach założono, że uzyskano parametry połączenia za pomocą jednej z tych dwóch metod.

## <a name="retrieve-your-connection-string"></a>Pobieranie parametrów połączenia

Możesz użyć `cloud_storage_account` klasy do reprezentowania informacji o koncie magazynu. Aby pobrać informacje o koncie magazynu z parametrów połączenia magazynu, można użyć `parse` metody.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="how-to-create-a-queue"></a>Instrukcje: Tworzenie kolejki

`cloud_queue_client`Obiekt umożliwia uzyskanie obiektów referencyjnych dla kolejek. Poniższy kod tworzy `cloud_queue_client` obiekt.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create a queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();
```

Użyj `cloud_queue_client` obiektu, aby uzyskać odwołanie do kolejki, której chcesz użyć. Możesz utworzyć kolejkę, jeśli nie istnieje.

```cpp
// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Create the queue if it doesn't already exist.
queue.create_if_not_exists();  
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Instrukcje: Wstawianie komunikatu do kolejki

Aby wstawić komunikat do istniejącej kolejki, najpierw utwórz nowy `cloud_queue_message` . Następnie Wywołaj `add_message` metodę. Element `cloud_queue_message` można utworzyć na podstawie ciągu (w formacie UTF-8) lub tablicy bajtowej. Oto kod, który tworzy kolejkę (jeśli nie istnieje) i wstawia komunikat `Hello, World` :

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Create the queue if it doesn't already exist.
queue.create_if_not_exists();

// Create a message and add it to the queue.
azure::storage::cloud_queue_message message1(U("Hello, World"));
queue.add_message(message1);  
```

## <a name="how-to-peek-at-the-next-message"></a>Instrukcje: wgląd do następnego komunikatu

Możesz wgląd do komunikatu z przodu kolejki bez usuwania go z kolejki, wywołując `peek_message` metodę.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Peek at the next message.
azure::storage::cloud_queue_message peeked_message = queue.peek_message();

// Output the message content.
std::wcout << U("Peeked message content: ") << peeked_message.content_as_string() << std::endl;
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Instrukcje: zmienianie zawartości komunikatu w kolejce

Możesz zmienić zawartość komunikatu w kolejce. Jeśli komunikat reprezentuje zadanie robocze, możesz użyć tej funkcji, aby zaktualizować stan zadania. Poniższy kod aktualizuje komunikat kolejki o nową zawartość i ustawia rozszerzenie limitu czasu widoczności o kolejne 60 sekund. Operacja ta zapisuje stan pracy powiązanej z komunikatem i daje klientowi kolejną minutę na kontynuowanie pracy nad komunikatem. Korzystając z tej metody, można śledzić wieloetapowe przepływy pracy dla komunikatów w kolejce, bez konieczności rozpoczynania od początku, jeśli etap przetwarzania zakończy się niepowodzeniem z powodu awarii sprzętu lub oprogramowania. Zazwyczaj stosuje się również liczbę ponownych prób. Jeśli komunikat zostanie ponowiony więcej niż n razy, zostanie usunięty. Jest to zabezpieczenie przed komunikatami, które wyzwalają błąd aplikacji zawsze, gdy są przetwarzane.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Get the message from the queue and update the message contents.
// The visibility timeout "0" means make it visible immediately.
// The visibility timeout "60" means the client can get another minute to continue
// working on the message.
azure::storage::cloud_queue_message changed_message = queue.get_message();

changed_message.set_content(U("Changed message"));
queue.update_message(changed_message, std::chrono::seconds(60), true);

// Output the message content.
std::wcout << U("Changed message content: ") << changed_message.content_as_string() << std::endl;  
```

## <a name="how-to-dequeue-the-next-message"></a>Instrukcje: dequeueing Next Message

Kod usuwa komunikat z kolejki w dwóch krokach. Po wywołaniu otrzymujesz `get_message` następną wiadomość w kolejce. Komunikat zwrócony z programu `get_message` stał się niewidoczny dla każdego innego kodu odczytującego komunikaty z tej kolejki. Aby zakończyć usuwanie komunikatu z kolejki, należy również wywołać metodę `delete_message` . Ten dwuetapowy proces usuwania komunikatów gwarantuje, że jeśli kod nie będzie w stanie przetworzyć komunikatu z powodu awarii sprzętu lub oprogramowania, inne wystąpienie kodu będzie w stanie uzyskać ten sam komunikat i ponowić próbę. Kod wywołuje `delete_message` się bezpośrednio po przetworzeniu komunikatu.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Get the next message.
azure::storage::cloud_queue_message dequeued_message = queue.get_message();
std::wcout << U("Dequeued message: ") << dequeued_message.content_as_string() << std::endl;

// Delete the message.
queue.delete_message(dequeued_message);
```

## <a name="how-to-use-additional-options-for-dequeuing-messages"></a>Instrukcje: korzystanie z dodatkowych opcji związanych z dekolejką komunikatów

Istnieją dwa sposoby dostosowania pobierania komunikatów z kolejki. Po pierwsze można uzyskać komunikaty zbiorczo (do 32). Po drugie można ustawić dłuższy lub krótszy limit czasu niewidoczności, dzięki czemu kod będzie mieć więcej lub mniej czasu na pełne przetworzenie każdego komunikatu. Poniższy przykład kodu używa metody, `get_messages` Aby pobrać 20 komunikatów w jednym wywołaniu. Następnie przetwarza każdy komunikat przy użyciu `for` pętli. Ustawia również limitu czasu niewidoczności na pięć minut dla każdego komunikatu. Należy zauważyć, że pięć minut rozpoczyna się dla wszystkich komunikatów w tym samym czasie, więc po upływie pięciu minut od wywołania do `get_messages` , wszystkie komunikaty, które nie zostały usunięte, staną się znów widoczne.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Dequeue some queue messages (maximum 32 at a time) and set their visibility timeout to
// 5 minutes (300 seconds).
azure::storage::queue_request_options options;
azure::storage::operation_context context;

// Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
std::vector<azure::storage::cloud_queue_message> messages = queue.get_messages(20, std::chrono::seconds(300), options, context);

for (auto it = messages.cbegin(); it != messages.cend(); ++it)
{
    // Display the contents of the message.
    std::wcout << U("Get: ") << it->content_as_string() << std::endl;
}
```

## <a name="how-to-get-the-queue-length"></a>Instrukcje: pobieranie długości kolejki

Możesz uzyskać szacunkową liczbę komunikatów w kolejce. `download_attributes`Metoda zwraca właściwości kolejki, w tym liczbę komunikatów. `approximate_message_count`Metoda pobiera przybliżoną liczbę komunikatów w kolejce.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Fetch the queue attributes.
queue.download_attributes();

// Retrieve the cached approximate message count.
int cachedMessageCount = queue.approximate_message_count();

// Display number of messages.
std::wcout << U("Number of messages in queue: ") << cachedMessageCount << std::endl;  
```

## <a name="how-to-delete-a-queue"></a>Instrukcje: usuwanie kolejki

Aby usunąć kolejkę i wszystkie znajdujące się w niej komunikaty, wywołaj `delete_queue_if_exists` metodę w obiekcie Queue.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// If the queue exists and delete it.
queue.delete_queue_if_exists();  
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz już podstawy Queue Storage, Skorzystaj z poniższych linków, aby dowiedzieć się więcej o usłudze Azure Storage.

- [Jak używać Blob Storage z C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
- [Jak używać Table Storage z C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
- [Generowanie listy zasobów usługi Azure Storage w języku C++](../common/storage-c-plus-plus-enumeration.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [Dokumentacja biblioteki klienckiej usługi Azure Storage dla języka C++](https://azure.github.io/azure-storage-cpp)
- [Dokumentacja usługi Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
