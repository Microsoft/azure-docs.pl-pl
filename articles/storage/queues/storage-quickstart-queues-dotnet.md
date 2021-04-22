---
title: 'Szybki start: Azure Queue Storage klienta w wersji 12 — .NET'
description: Dowiedz się, jak za pomocą Azure Queue Storage klienta w wersji 12 dla programu .NET utworzyć kolejkę i dodać komunikaty do kolejki. Następnie dowiesz się, jak odczytywać i usuwać komunikaty z kolejki. Dowiesz się również, jak usunąć kolejkę.
author: twooley
ms.author: twooley
ms.date: 07/24/2020
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-csharp
ms.openlocfilehash: f0b03f8a0a9e3aa277ec940c8e65064ef0411ccc
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867623"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-net"></a>Szybki start: Azure Queue Storage klienta w wersji 12 dla programu .NET

Wprowadzenie do biblioteki Azure Queue Storage klienta w wersji 12 dla programu .NET. Azure Queue Storage to usługa do przechowywania dużej liczby komunikatów do późniejszego pobrania i przetwarzania. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.

Użyj biblioteki Azure Queue Storage klienta v12 dla programu .NET, aby:

- Tworzenie kolejki
- Dodawanie komunikatów do kolejki
- Wgląd w komunikaty w kolejce
- Aktualizowanie komunikatu w kolejce
- Odbieranie komunikatów z kolejki
- Usuwanie komunikatów z kolejki
- Usuwanie kolejki

Dodatkowe zasoby:

- [Dokumentacja referencyjna interfejsu API](/dotnet/api/azure.storage.queues)
- [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues)
- [Pakiet (NuGet)](https://www.nuget.org/packages/Azure.Storage.Queues/12.0.0)
- [Samples](../common/storage-samples-dotnet.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure [— utwórz subskrypcję bezpłatnie](https://azure.microsoft.com/free/)
- Konto usługi Azure Storage [— tworzenie konta magazynu](../common/storage-account-create.md)
- Bieżące [zestaw .NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) dla systemu operacyjnego. Pamiętaj, aby pobrać zestaw SDK, a nie środowisko uruchomieniowe.

## <a name="setting-up"></a>Konfigurowanie

W tej sekcji odpowiedzialności za przygotowanie projektu do pracy z biblioteką klienta Azure Queue Storage w wersji 12 dla programu .NET.

### <a name="create-the-project"></a>Tworzenie projektu

Utwórz aplikację .NET Core o nazwie `QueuesQuickstartV12` .

1. W oknie konsoli (takim jak cmd, PowerShell lub Bash) użyj polecenia , aby utworzyć nową aplikację `dotnet new` konsolową o nazwie `QueuesQuickstartV12` . To polecenie tworzy prosty projekt języka C# "hello world" z pojedynczym plikiem źródłowym o nazwie `Program.cs` .

   ```console
   dotnet new console -n QueuesQuickstartV12
   ```

1. Przejdź do nowo utworzonego `QueuesQuickstartV12` katalogu.

   ```console
   cd QueuesQuickstartV12
   ```

### <a name="install-the-package"></a>Instalowanie pakietu

W katalogu aplikacji zainstaluj bibliotekę klienta Azure Queue Storage pakietu .NET przy użyciu `dotnet add package` polecenia .

```console
dotnet add package Azure.Storage.Queues
```

### <a name="set-up-the-app-framework"></a>Konfigurowanie struktury aplikacji

Z katalogu projektu:

1. Otwórz plik `Program.cs` w edytorze
1. Usuwanie `Console.WriteLine("Hello, World");` instrukcji
1. Dodawanie `using` dyrektyw
1. Aktualizowanie deklaracji `Main` metody w celu obsługi kodu [asynchronicznego](/dotnet/csharp/whats-new/csharp-7#async-main)

Oto kod:

```csharp
using Azure;
using Azure.Storage.Queues;
using Azure.Storage.Queues.Models;
using System;
using System.Threading.Tasks;

namespace QueuesQuickstartV12
{
    class Program
    {
        static async Task Main(string[] args)
        {
        }
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Model obiektu

Azure Queue Storage to usługa służąca do przechowywania dużej liczby komunikatów. Komunikat w kolejce może mieć rozmiar do 64 KB. Kolejka może zawierać miliony komunikatów do całkowitego limitu pojemności konta magazynu. Kolejki są często używane do tworzenia listy prac do asynchronicznego przetwarzania. Queue Storage oferuje trzy typy zasobów:

- Konto magazynu
- Kolejka na koncie magazynu
- Komunikaty w kolejce

Na poniższym diagramie przedstawiono relacje między tymi zasobami.

![Diagram architektury usługi Queue Storage](./media/storage-queues-introduction/queue1.png)

Użyj następujących klas .NET, aby wchodzić w interakcje z tymi zasobami:

- [`QueueServiceClient`](/dotnet/api/azure.storage.queues.queueserviceclient): `QueueServiceClient` umożliwia zarządzanie wszystkimi kolejkami na koncie magazynu.
- [`QueueClient`](/dotnet/api/azure.storage.queues.queueclient): Klasa umożliwia zarządzanie pojedynczą kolejką i jej komunikatami oraz `QueueClient` manipulowanie nimi.
- [`QueueMessage`](/dotnet/api/azure.storage.queues.models.queuemessage): `QueueMessage` klasa reprezentuje poszczególne obiekty zwracane podczas wywoływania [`ReceiveMessages`](/dotnet/api/azure.storage.queues.queueclient.receivemessages) w kolejce.

## <a name="code-examples"></a>Przykłady kodu

Te przykładowe fragmenty kodu pokazują, jak wykonać następujące akcje za pomocą Azure Queue Storage klienta dla programu .NET:

- [Pobieranie parametrów połączenia](#get-the-connection-string)
- [Tworzenie kolejki](#create-a-queue)
- [Dodawanie komunikatów do kolejki](#add-messages-to-a-queue)
- [Wgląd w komunikaty w kolejce](#peek-at-messages-in-a-queue)
- [Aktualizowanie komunikatu w kolejce](#update-a-message-in-a-queue)
- [Odbieranie komunikatów z kolejki](#receive-messages-from-a-queue)
- [Usuwanie komunikatów z kolejki](#delete-messages-from-a-queue)
- [Usuwanie kolejki](#delete-a-queue)

### <a name="get-the-connection-string"></a>Pobieranie parametrów połączenia

Poniższy kod pobiera ciąg połączenia dla konta magazynu. Ciąg połączenia jest przechowywany w zmiennej środowiskowej utworzonej w sekcji [Konfigurowanie parametrów połączenia magazynu.](#configure-your-storage-connection-string)

Dodaj ten kod wewnątrz `Main` metody :

```csharp
Console.WriteLine("Azure Queue Storage client library v12 - .NET quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable called
// AZURE_STORAGE_CONNECTION_STRING on the machine running the application.
// If the environment variable is created after the application is launched
// in a console or with Visual Studio, the shell or application needs to be
// closed and reloaded to take the environment variable into account.
string connectionString = Environment.GetEnvironmentVariable("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-queue"></a>Tworzenie kolejki

Wybierz nazwę nowej kolejki. Poniższy kod dołącza wartość identyfikatora GUID do nazwy kolejki, aby upewnić się, że jest unikatowa.

> [!IMPORTANT]
> Nazwy kolejek mogą zawierać tylko małe litery, cyfry i łączniki i muszą zaczynać się literą lub cyfrą. Przed i za każdym łącznikiem musi znajdować się znak inny niż łącznik. Nazwa musi mieć również od 3 do 63 znaków. Aby uzyskać więcej informacji, zobacz [Naming queues and metadata (Nazewnictwo kolejek i metadanych).](/rest/api/storageservices/naming-queues-and-metadata)

Utwórz wystąpienie [`QueueClient`](/dotnet/api/azure.storage.queues.queueclient) klasy . Następnie wywołaj [`CreateAsync`](/dotnet/api/azure.storage.queues.queueclient.createasync) metodę , aby utworzyć kolejkę na koncie magazynu.

Dodaj ten kod na końcu `Main` metody :

```csharp
// Create a unique name for the queue
string queueName = "quickstartqueues-" + Guid.NewGuid().ToString();

Console.WriteLine($"Creating queue: {queueName}");

// Instantiate a QueueClient which will be
// used to create and manipulate the queue
QueueClient queueClient = new QueueClient(connectionString, queueName);

// Create the queue
await queueClient.CreateAsync();
```

### <a name="add-messages-to-a-queue"></a>Dodawanie komunikatów do kolejki

Poniższy fragment kodu asynchronicznie dodaje komunikaty do kolejki przez wywołanie [`SendMessageAsync`](/dotnet/api/azure.storage.queues.queueclient.sendmessageasync) metody . Zapisuje również [`SendReceipt`](/dotnet/api/azure.storage.queues.models.sendreceipt) zwrócony z `SendMessageAsync` wywołania. Potwierdzenie służy do aktualizowania komunikatu w dalszej części programu.

Dodaj ten kod na końcu `Main` metody :

```csharp
Console.WriteLine("\nAdding messages to the queue...");

// Send several messages to the queue
await queueClient.SendMessageAsync("First message");
await queueClient.SendMessageAsync("Second message");

// Save the receipt so we can update this message later
SendReceipt receipt = await queueClient.SendMessageAsync("Third message");
```

### <a name="peek-at-messages-in-a-queue"></a>Wgląd w komunikaty w kolejce

Wgląd w komunikaty w kolejce przez wywołanie [`PeekMessagesAsync`](/dotnet/api/azure.storage.queues.queueclient.peekmessagesasync) metody . Ta metoda pobiera co najmniej jeden komunikat z przodu kolejki, ale nie zmienia widoczności komunikatu.

Dodaj ten kod na końcu `Main` metody :

```csharp
Console.WriteLine("\nPeek at the messages in the queue...");

// Peek at messages in the queue
PeekedMessage[] peekedMessages = await queueClient.PeekMessagesAsync(maxMessages: 10);

foreach (PeekedMessage peekedMessage in peekedMessages)
{
    // Display the message
    Console.WriteLine($"Message: {peekedMessage.MessageText}");
}
```

### <a name="update-a-message-in-a-queue"></a>Aktualizowanie komunikatu w kolejce

Zaktualizuj zawartość komunikatu, wywołując [`UpdateMessageAsync`](/dotnet/api/azure.storage.queues.queueclient.updatemessageasync) metodę . Ta metoda może zmienić limit czasu widoczności i zawartość komunikatu. Zawartość komunikatu musi być ciągiem zakodowanym w formacie UTF-8 o rozmiarze do 64 KB. Wraz z nową zawartością komunikatu przekaż wartości z pliku , `SendReceipt` który został zapisany wcześniej w kodzie. Wartości `SendReceipt` identyfikują komunikat do zaktualizowania.

```csharp
Console.WriteLine("\nUpdating the third message in the queue...");

// Update a message using the saved receipt from sending the message
await queueClient.UpdateMessageAsync(receipt.MessageId, receipt.PopReceipt, "Third message has been updated");
```

### <a name="receive-messages-from-a-queue"></a>Odbieranie komunikatów z kolejki

Pobierz wcześniej dodane komunikaty, wywołując [`ReceiveMessagesAsync`](/dotnet/api/azure.storage.queues.queueclient.receivemessagesasync) metodę .

Dodaj ten kod na końcu `Main` metody :

```csharp
Console.WriteLine("\nReceiving messages from the queue...");

// Get messages from the queue
QueueMessage[] messages = await queueClient.ReceiveMessagesAsync(maxMessages: 10);
```

### <a name="delete-messages-from-a-queue"></a>Usuwanie komunikatów z kolejki

Usuń komunikaty z kolejki po ich przetworzeniu. W takim przypadku przetwarzanie tylko wyświetla komunikat w konsoli.

Aplikacja wstrzymuje wprowadzanie danych przez użytkownika przez wywołanie metody przed rozpoczęciem przetwarzania `Console.ReadLine` i usunięciem komunikatów. Przed [usunięciem Azure Portal](https://portal.azure.com) w aplikacji sprawdź, czy zasoby zostały utworzone prawidłowo. Wszystkie komunikaty, które nie zostały jawnie usunięte, staną się w końcu ponownie widoczne w kolejce na kolejną szansę ich przetwarzania.

Dodaj ten kod na końcu `Main` metody :

```csharp
Console.WriteLine("\nPress Enter key to 'process' messages and delete them from the queue...");
Console.ReadLine();

// Process and delete messages from the queue
foreach (QueueMessage message in messages)
{
    // "Process" the message
    Console.WriteLine($"Message: {message.MessageText}");

    // Let the service know we're finished with
    // the message and it can be safely deleted.
    await queueClient.DeleteMessageAsync(message.MessageId, message.PopReceipt);
}
```

### <a name="delete-a-queue"></a>Usuwanie kolejki

Poniższy kod czyści zasoby utworzone przez aplikację przez usunięcie kolejki przy użyciu [`DeleteAsync`](/dotnet/api/azure.storage.queues.queueclient.deleteasync) metody .

Dodaj ten kod na końcu `Main` metody :

```csharp
Console.WriteLine("\nPress Enter key to delete the queue...");
Console.ReadLine();

// Clean up
Console.WriteLine($"Deleting queue: {queueClient.Name}");
await queueClient.DeleteAsync();

Console.WriteLine("Done");
```

## <a name="run-the-code"></a>Uruchamianie kodu

Ta aplikacja tworzy i dodaje trzy komunikaty do kolejki platformy Azure. Kod wyświetla listę komunikatów w kolejce, a następnie pobiera je i usuwa przed usunięciem kolejki.

W oknie konsoli przejdź do katalogu aplikacji, a następnie skompilować i uruchomić aplikację.

```console
dotnet build
```

```console
dotnet run
```

Dane wyjściowe aplikacji są podobne do następującego przykładu:

```output
Azure Queue Storage client library v12 - .NET quickstart sample

Creating queue: quickstartqueues-5c72da2c-30cc-4f09-b05c-a95d9da52af2

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Receiving messages from the queue...

Press Enter key to 'process' messages and delete them from the queue...

Message: First message
Message: Second message
Message: Third message has been updated

Press Enter key to delete the queue...

Deleting queue: quickstartqueues-5c72da2c-30cc-4f09-b05c-a95d9da52af2
Done
```

Gdy aplikacja zostanie wstrzymana przed odebraniem komunikatów, sprawdź konto magazynu w [Azure Portal](https://portal.azure.com). Sprawdź, czy komunikaty znajdują się w kolejce.

Naciśnij klawisz `Enter` , aby odbierać i usuwać komunikaty. Po wyświetleniu monitu naciśnij ponownie `Enter` klawisz , aby usunąć kolejkę i zakończyć pokaz.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start opisano sposób tworzenia kolejki i dodawania do niego komunikatów przy użyciu asynchronicznego kodu .NET. Następnie nauczyliśmy się podeglądać, pobierać i usuwać komunikaty. Na koniec dowiedzieliśmy się, jak usunąć kolejkę komunikatów.

Samouczki, przykłady, krótkie starty i inne dokumenty można znaleźć na stronie:

> [!div class="nextstepaction"]
> [Platforma Azure dla deweloperów .NET i .NET Core](/dotnet/azure/)

- Aby dowiedzieć się więcej, zobacz [Biblioteki usługi Azure Storage dla platformy .NET.](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage)
- Aby uzyskać więcej Azure Queue Storage przykładowych aplikacji, [zobacz Azure Queue Storage klienta dla przykładów dla programu .NET.](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples)
- Aby dowiedzieć się więcej na temat platformy .NET Core, zobacz [Get started with .NET in 10 minutes (Rozpoczynanie pracy z platformą .NET w 10 minut)](https://dotnet.microsoft.com/learn/dotnet/hello-world-tutorial/intro).
