---
title: 'Szybki start: Azure Queue Storage klienta w wersji 12 — JavaScript'
description: Dowiedz się, jak za pomocą Azure Queue Storage klienta v12 dla języka JavaScript utworzyć kolejkę i dodać do niego komunikaty. Następnie dowiedz się, jak odczytywać i usuwać komunikaty z kolejki. Dowiesz się również, jak usunąć kolejkę.
author: twooley
ms.author: twooley
ms.date: 12/13/2019
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom:
- devx-track-js
- mode-api
ms.openlocfilehash: 943678debc32116ff7a2e54810c4edcf2d331bd6
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534430"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-javascript"></a>Szybki start: Azure Queue Storage klienta w wersji 12 dla języka JavaScript

Wprowadzenie do biblioteki Azure Queue Storage klienta w wersji 12 dla języka JavaScript. Azure Queue Storage to usługa do przechowywania dużej liczby komunikatów do późniejszego pobierania i przetwarzania. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.

Użyj biblioteki Azure Queue Storage klienta w wersji 12 dla języka JavaScript, aby:

- Tworzenie kolejki
- Dodawanie komunikatów do kolejki
- Wgląd w komunikaty w kolejce
- Aktualizowanie komunikatu w kolejce
- Odbieranie komunikatów z kolejki
- Usuwanie komunikatów z kolejki
- Usuwanie kolejki

Dodatkowe zasoby:

- [Dokumentacja referencyjna interfejsu API](/javascript/api/@azure/storage-queue/)
- [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue)
- [Pakiet (npm)](https://www.npmjs.com/package/@azure/storage-queue)
- [Samples](../common/storage-samples-javascript.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure [— utwórz subskrypcję bezpłatnie](https://azure.microsoft.com/free/)
- Konto usługi Azure Storage [— tworzenie konta magazynu](../common/storage-account-create.md)
- Bieżące [Node.js](https://nodejs.org/en/download/) dla twojego systemu operacyjnego.

## <a name="setting-up"></a>Konfigurowanie

Ta sekcja zawiera opis przygotowywania projektu do pracy z biblioteką klienta Azure Queue Storage w wersji 12 dla języka JavaScript.

### <a name="create-the-project"></a>Tworzenie projektu

Tworzenie aplikacji Node.js o nazwie `queues-quickstart-v12`

1. W oknie konsoli (takim jak cmd, PowerShell lub Bash) utwórz nowy katalog dla projektu.

    ```console
    mkdir queues-quickstart-v12
    ```

1. Przejdź do nowo utworzonego `queues-quickstart-v12` katalogu.

    ```console
    cd queues-quickstart-v12
    ```

1. Utwórz nowy plik tekstowy o nazwie `package.json` . Ten plik definiuje Node.js projektu. Zapisz ten plik w `queues-quickstart-v12` katalogu . Oto zawartość pliku:

    ```json
    {
        "name": "queues-quickstart-v12",
        "version": "1.0.0",
        "description": "Use the @azure/storage-queue SDK version 12 to interact with Azure Queue storage",
        "main": "queues-quickstart-v12.js",
        "scripts": {
            "start": "node queues-quickstart-v12.js"
        },
        "author": "Your Name",
        "license": "MIT",
        "dependencies": {
            "@azure/storage-queue": "^12.0.0",
            "@types/dotenv": "^4.0.3",
            "dotenv": "^6.0.0"
        }
    }
    ```

    Jeśli chcesz, możesz umieścić własną nazwę w `author` polu .

### <a name="install-the-package"></a>Instalowanie pakietu

W katalogu zainstaluj bibliotekę klienta Azure Queue Storage `queues-quickstart-v12` pakietu JavaScript przy użyciu `npm install` polecenia .

```console
npm install
```

To polecenie odczytuje plik i instaluje bibliotekę klienta Azure Queue Storage v12 dla pakietu JavaScript i wszystkie `package.json` biblioteki, od których zależy.

### <a name="set-up-the-app-framework"></a>Konfigurowanie struktury aplikacji

Z katalogu projektu:

1. Otwieranie innego nowego pliku tekstowego w edytorze kodu
1. Dodawanie `require` wywołań w celu załadowania platformy Azure Node.js modułów
1. Tworzenie struktury programu, w tym bardzo podstawowej obsługi wyjątków

    Oto kod:

    ```javascript
    const { QueueClient } = require("@azure/storage-queue");
    const uuidv1 = require("uuid/v1");

    async function main() {
        console.log("Azure Queue Storage client library v12 - JavaScript quickstart sample");
        // Quick start code goes here
    }

    main().then(() => console.log("\nDone")).catch((ex) => console.log(ex.message));

    ```

1. Zapisz nowy plik jako `queues-quickstart-v12.js` w `queues-quickstart-v12` katalogu .

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Model obiektów

Azure Queue Storage to usługa służąca do przechowywania dużej liczby komunikatów. Komunikat w kolejce może mieć rozmiar do 64 KB. Kolejka może zawierać miliony komunikatów do całkowitego limitu pojemności konta magazynu. Kolejki są często używane do tworzenia listy prac do asynchronicznego przetwarzania. Queue Storage oferuje trzy typy zasobów:

- Konto magazynu
- Kolejka na koncie magazynu
- Komunikaty w kolejce

Na poniższym diagramie przedstawiono relacje między tymi zasobami.

![Diagram architektury usługi Queue Storage](./media/storage-queues-introduction/queue1.png)

Użyj następujących klas języka JavaScript, aby wchodzić w interakcje z tymi zasobami:

- [`QueueServiceClient`](/javascript/api/@azure/storage-queue/queueserviceclient): `QueueServiceClient` umożliwia zarządzanie wszystkimi kolejkami na koncie magazynu.
- [`QueueClient`](/javascript/api/@azure/storage-queue/queueclient): Klasa umożliwia zarządzanie pojedynczą kolejką i jej komunikatami oraz `QueueClient` manipulowanie nimi.
- [`QueueMessage`](/javascript/api/@azure/storage-queue/queuemessage): `QueueMessage` klasa reprezentuje poszczególne obiekty zwracane podczas wywoływania [`ReceiveMessages`](/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) w kolejce.

## <a name="code-examples"></a>Przykłady kodu

Te przykładowe fragmenty kodu pokazują, jak wykonać następujące akcje przy użyciu Azure Queue Storage klienta dla języka JavaScript:

- [Pobieranie parametrów połączenia](#get-the-connection-string)
- [Tworzenie kolejki](#create-a-queue)
- [Dodawanie komunikatów do kolejki](#add-messages-to-a-queue)
- [Wgląd w komunikaty w kolejce](#peek-at-messages-in-a-queue)
- [Aktualizowanie komunikatu w kolejce](#update-a-message-in-a-queue)
- [Odbieranie komunikatów z kolejki](#receive-messages-from-a-queue)
- [Usuwanie komunikatów z kolejki](#delete-messages-from-a-queue)
- [Usuwanie kolejki](#delete-a-queue)

### <a name="get-the-connection-string"></a>Pobieranie parametrów połączenia

Poniższy kod pobiera ciąg połączenia dla konta magazynu ze zmiennej środowiskowej utworzonej w sekcji Konfigurowanie [parametrów połączenia](#configure-your-storage-connection-string) magazynu.

Dodaj ten kod wewnątrz `main` funkcji :

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be
// closed and reloaded to take the environment variable into account.
const AZURE_STORAGE_CONNECTION_STRING = process.env.AZURE_STORAGE_CONNECTION_STRING;
```

### <a name="create-a-queue"></a>Tworzenie kolejki

Wybierz nazwę nowej kolejki. Poniższy kod dołącza wartość UUID do nazwy kolejki, aby upewnić się, że jest unikatowa.

> [!IMPORTANT]
> Nazwy kolejek mogą zawierać tylko małe litery, cyfry i łączniki i muszą zaczynać się literą lub cyfrą. Przed i za każdym łącznikiem musi znajdować się znak inny niż łącznik. Nazwa musi mieć również od 3 do 63 znaków. Aby uzyskać więcej informacji, zobacz [Naming queues and metadata (Nazewnictwo kolejek i metadanych).](/rest/api/storageservices/naming-queues-and-metadata)

Utwórz wystąpienie [`QueueClient`](/javascript/api/@azure/storage-queue/queueclient) klasy . Następnie wywołaj [`create`](/javascript/api/@azure/storage-queue/queueclient#create-queuecreateoptions-) metodę , aby utworzyć kolejkę na koncie magazynu.

Dodaj ten kod na końcu `main` funkcji:

```javascript
// Create a unique name for the queue
const queueName = "quickstart" + uuidv1();

console.log("\nCreating queue...");
console.log("\t", queueName);

// Instantiate a QueueClient which will be used to create and manipulate a queue
const queueClient = new QueueClient(AZURE_STORAGE_CONNECTION_STRING, queueName);

// Create the queue
const createQueueResponse = await queueClient.create();
console.log("Queue created, requestId:", createQueueResponse.requestId);
```

### <a name="add-messages-to-a-queue"></a>Dodawanie komunikatów do kolejki

Poniższy fragment kodu dodaje komunikaty do kolejki przez wywołanie [`sendMessage`](/javascript/api/@azure/storage-queue/queueclient#sendmessage-string--queuesendmessageoptions-) metody . Zapisuje również [`QueueMessage`](/javascript/api/@azure/storage-queue/queuemessage) zwrócone z trzeciego `sendMessage` wywołania. Zwrócony tekst `sendMessageResponse` służy do aktualizowania zawartości komunikatu w dalszej części programu.

Dodaj ten kod na końcu `main` funkcji:

```javascript
console.log("\nAdding messages to the queue...");

// Send several messages to the queue
await queueClient.sendMessage("First message");
await queueClient.sendMessage("Second message");
const sendMessageResponse = await queueClient.sendMessage("Third message");

console.log("Messages added, requestId:", sendMessageResponse.requestId);
```

### <a name="peek-at-messages-in-a-queue"></a>Wgląd w komunikaty w kolejce

Wgląd w komunikaty w kolejce przez wywołanie [`peekMessages`](/javascript/api/@azure/storage-queue/queueclient#peekmessages-queuepeekmessagesoptions-) metody . Ta metoda pobiera co najmniej jeden komunikat z przodu kolejki, ale nie zmienia widoczności komunikatu.

Dodaj ten kod na końcu `main` funkcji:

```javascript
console.log("\nPeek at the messages in the queue...");

// Peek at messages in the queue
const peekedMessages = await queueClient.peekMessages({ numberOfMessages : 5 });

for (i = 0; i < peekedMessages.peekedMessageItems.length; i++) {
    // Display the peeked message
    console.log("\t", peekedMessages.peekedMessageItems[i].messageText);
}
```

### <a name="update-a-message-in-a-queue"></a>Aktualizowanie komunikatu w kolejce

Zaktualizuj zawartość komunikatu, wywołując [`updateMessage`](/javascript/api/@azure/storage-queue/queueclient#updatemessage-string--string--string--undefined---number--queueupdatemessageoptions-) metodę . Ta metoda może zmienić limit czasu widoczności i zawartość komunikatu. Zawartość komunikatu musi być ciągiem zakodowanym w formacie UTF-8 o rozmiarze do 64 KB. Wraz z nową zawartością przekaż i `messageId` `popReceipt` z odpowiedzi, która została zapisana wcześniej w kodzie. Właściwości `sendMessageResponse` identyfikują komunikat do zaktualizowania.

```javascript
console.log("\nUpdating the third message in the queue...");

// Update a message using the response saved when calling sendMessage earlier
updateMessageResponse = await queueClient.updateMessage(
    sendMessageResponse.messageId,
    sendMessageResponse.popReceipt,
    "Third message has been updated"
);

console.log("Message updated, requestId:", updateMessageResponse.requestId);
```

### <a name="receive-messages-from-a-queue"></a>Odbieranie komunikatów z kolejki

Pobierz wcześniej dodane komunikaty, wywołując [`receiveMessages`](/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) metodę . W polu przekaż maksymalną liczbę komunikatów do `numberOfMessages` odbierania dla tego wywołania.

Dodaj ten kod na końcu `main` funkcji:

```javascript
console.log("\nReceiving messages from the queue...");

// Get messages from the queue
const receivedMessagesResponse = await queueClient.receiveMessages({ numberOfMessages : 5 });

console.log("Messages received, requestId:", receivedMessagesResponse.requestId);
```

### <a name="delete-messages-from-a-queue"></a>Usuwanie komunikatów z kolejki

Usuń komunikaty z kolejki po ich otrzymaniu i przetworzeniu. W takim przypadku przetwarzanie tylko wyświetla komunikat w konsoli.

Usuń komunikaty, wywołując [`deleteMessage`](/javascript/api/@azure/storage-queue/queueclient#deletemessage-string--string--queuedeletemessageoptions-) metodę . Wszystkie komunikaty, które nie zostały jawnie usunięte, staną się w końcu ponownie widoczne w kolejce na kolejną szansę ich przetwarzania.

Dodaj ten kod na końcu `main` funkcji:

```javascript
// 'Process' and delete messages from the queue
for (i = 0; i < receivedMessagesResponse.receivedMessageItems.length; i++) {
    receivedMessage = receivedMessagesResponse.receivedMessageItems[i];

    // 'Process' the message
    console.log("\tProcessing:", receivedMessage.messageText);

    // Delete the message
    const deleteMessageResponse = await queueClient.deleteMessage(
        receivedMessage.messageId,
        receivedMessage.popReceipt
    );
    console.log("\tMessage deleted, requestId:", deleteMessageResponse.requestId);
}
```

### <a name="delete-a-queue"></a>Usuwanie kolejki

Poniższy kod czyści zasoby utworzone przez aplikację przez usunięcie kolejki przy użyciu [`delete`](/javascript/api/@azure/storage-queue/queueclient#delete-queuedeleteoptions-) metody .

Dodaj ten kod na końcu funkcji `main` i zapisz plik:

```javascript
// Delete the queue
console.log("\nDeleting queue...");
const deleteQueueResponse = await queueClient.delete();
console.log("Queue deleted, requestId:", deleteQueueResponse.requestId);
```

## <a name="run-the-code"></a>Uruchamianie kodu

Ta aplikacja tworzy i dodaje trzy komunikaty do kolejki platformy Azure. Kod wyświetla listę komunikatów w kolejce, a następnie pobiera je i usuwa przed usunięciem kolejki.

W oknie konsoli przejdź do katalogu zawierającego plik, a następnie użyj `queues-quickstart-v12.js` następującego `node` polecenia, aby uruchomić aplikację.

```console
node queues-quickstart-v12.js
```

Dane wyjściowe aplikacji są podobne do następującego przykładu:

```output
Azure Queue Storage client library v12 - JavaScript quickstart sample

Creating queue...
         quickstartc095d120-1d04-11ea-af30-090ee231305f
Queue created, requestId: 5c0bc94c-6003-011b-7c11-b13d06000000

Adding messages to the queue...
Messages added, requestId: a0390321-8003-001e-0311-b18f2c000000

Peek at the messages in the queue...
         First message
         Second message
         Third message

Updating the third message in the queue...
Message updated, requestId: cb172c9a-5003-001c-2911-b18dd6000000

Receiving messages from the queue...
Messages received, requestId: a039036f-8003-001e-4811-b18f2c000000
        Processing: First message
        Message deleted, requestId: 4a65b82b-d003-00a7-5411-b16c22000000
        Processing: Second message
        Message deleted, requestId: 4f0b2958-c003-0030-2a11-b10feb000000
        Processing: Third message has been updated
        Message deleted, requestId: 6c978fcb-5003-00b6-2711-b15b39000000

Deleting queue...
Queue deleted, requestId: 5c0bca05-6003-011b-1e11-b13d06000000

Done
```

Zapoznaj się z kodem w debugerze i sprawdź, Azure Portal [w](https://portal.azure.com) całym procesie. Sprawdź konto magazynu, aby sprawdzić, czy komunikaty w kolejce zostały utworzone i usunięte.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start opisano sposób tworzenia kolejki i dodawania do niego komunikatów przy użyciu kodu JavaScript. Następnie dowiedzieliśmy się, jak podeglądać, pobierać i usuwać komunikaty. Na koniec dowiedzieliśmy się, jak usunąć kolejkę komunikatów.

Samouczki, przykłady, poradniki Szybki start i inne dokumenty można znaleźć na stronie:

> [!div class="nextstepaction"]
> [Dokumentacja platformy Azure dla języka JavaScript](/azure/developer/javascript/)

- Aby dowiedzieć się więcej, zobacz [Azure Queue Storage klienta dla języka JavaScript.](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue)
- Aby uzyskać Azure Queue Storage przykładowych aplikacji, zobacz Azure Queue Storage klienta w wersji [12 dla języka JavaScript — przykłady](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue/samples).
