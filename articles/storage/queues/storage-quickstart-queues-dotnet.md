---
title: 'Szybki Start: Azure Queue Storage Client Library V12 — .NET'
description: Dowiedz się, jak utworzyć kolejkę i dodać do niej komunikaty przy użyciu V12 biblioteki klienta Queue Storage platformy Azure dla platformy .NET. Następnie dowiesz się, jak odczytywać i usuwać wiadomości z kolejki. Dowiesz się również, jak usunąć kolejkę.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/24/2020
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-csharp
ms.openlocfilehash: 22038e4145acabc067083177fcf297464972ad58
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97589529"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-net"></a>Szybki Start: V12 biblioteki klienta Queue Storage platformy Azure dla platformy .NET

Rozpocznij pracę z biblioteką klienta Queue Storage platformy Azure w wersji 12 dla platformy .NET. Azure Queue Storage to usługa służąca do przechowywania dużej liczby komunikatów do późniejszego pobrania i przetworzenia. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.

Użyj usługi Azure Queue Storage Client Library V12 for .NET, aby:

- Tworzenie kolejki
- Dodawanie komunikatów do kolejki
- Wgląd w wiadomości w kolejce
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

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
- Konto magazynu platformy Azure — [Tworzenie konta magazynu](../common/storage-account-create.md)
- Bieżąca [zestaw .NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) dla danego systemu operacyjnego. Pamiętaj, aby pobrać zestaw SDK, a nie środowisko uruchomieniowe.

## <a name="setting-up"></a>Konfigurowanie

W tej sekcji omówiono przygotowanie projektu do pracy z usługą Azure Queue Storage Client Library V12 for .NET.

### <a name="create-the-project"></a>Tworzenie projektu

Utwórz aplikację platformy .NET Core o nazwie `QueuesQuickstartV12` .

1. W oknie konsoli (na przykład cmd, PowerShell lub bash) Użyj `dotnet new` polecenia, aby utworzyć nową aplikację konsolową o nazwie `QueuesQuickstartV12` . To polecenie tworzy prosty projekt języka C# "Hello World" z pojedynczym plikiem źródłowym o nazwie `Program.cs` .

   ```console
   dotnet new console -n QueuesQuickstartV12
   ```

1. Przejdź do nowo utworzonego `QueuesQuickstartV12` katalogu.

   ```console
   cd QueuesQuickstartV12
   ```

### <a name="install-the-package"></a>Zainstaluj pakiet

Gdy nadal znajduje się w katalogu aplikacji, zainstaluj pakiet Azure Queue Storage Client Library for .NET przy użyciu `dotnet add package` polecenia.

```console
dotnet add package Azure.Storage.Queues
```

### <a name="set-up-the-app-framework"></a>Konfigurowanie struktury aplikacji

Z katalogu projektu:

1. Otwórz `Program.cs` plik w edytorze
1. Usuń `Console.WriteLine("Hello, World");` instrukcję
1. Dodaj `using` dyrektywy
1. Aktualizowanie `Main` deklaracji metody do [obsługi kodu asynchronicznego](/dotnet/csharp/whats-new/csharp-7#async-main)

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

## <a name="object-model"></a>Model obiektów

Azure Queue Storage to usługa służąca do przechowywania dużej liczby komunikatów. Komunikat w kolejce może mieć rozmiar do 64 KB. Kolejka może zawierać miliony komunikatów, do łącznego limitu pojemności konta magazynu. Kolejki są często używane do tworzenia zaległości prac do przetwarzania asynchronicznego. Queue Storage oferuje trzy typy zasobów:

- Konto magazynu
- Kolejka na koncie magazynu
- Komunikaty w kolejce

Na poniższym diagramie przedstawiono relacje między tymi zasobami.

![Diagram architektury magazynu kolejki](./media/storage-queues-introduction/queue1.png)

Użyj następujących klas platformy .NET do korzystania z tych zasobów:

- [`QueueServiceClient`](/dotnet/api/azure.storage.queues.queueserviceclient): `QueueServiceClient` Umożliwia zarządzanie wszystkimi kolejkami na koncie magazynu.
- [`QueueClient`](/dotnet/api/azure.storage.queues.queueclient): `QueueClient` Klasa umożliwia zarządzanie pojedynczą kolejką i jej komunikatami oraz manipulowanie nimi.
- [`QueueMessage`](/dotnet/api/azure.storage.queues.models.queuemessage): `QueueMessage` Klasa reprezentuje poszczególne obiekty zwrócone podczas wywoływania [`ReceiveMessages`](/dotnet/api/azure.storage.queues.queueclient.receivemessages) kolejki.

## <a name="code-examples"></a>Przykłady kodu

Te przykładowe fragmenty kodu pokazują, jak wykonać następujące czynności w bibliotece klienta Queue Storage platformy Azure dla platformy .NET:

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

Dodaj ten kod wewnątrz `Main` metody:

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

Określ nazwę nowej kolejki. Poniższy kod dołącza wartość identyfikatora GUID do nazwy kolejki, aby upewnić się, że jest ona unikatowa.

> [!IMPORTANT]
> Nazwy kolejek mogą zawierać tylko małe litery, cyfry i łączniki, a także muszą zaczynać się literą lub cyfrą. Przed i za każdym łącznikiem musi znajdować się znak inny niż łącznik. Nazwa musi mieć również długość od 3 do 63 znaków. Aby uzyskać więcej informacji, zobacz [nazywanie kolejek i metadanych](/rest/api/storageservices/naming-queues-and-metadata).

Utwórz wystąpienie [`QueueClient`](/dotnet/api/azure.storage.queues.queueclient) klasy. Następnie Wywołaj [`CreateAsync`](/dotnet/api/azure.storage.queues.queueclient.createasync) metodę, aby utworzyć kolejkę na koncie magazynu.

Dodaj ten kod na końcu `Main` metody:

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

Poniższy fragment kodu asynchronicznie dodaje komunikaty do kolejki przez wywołanie [`SendMessageAsync`](/dotnet/api/azure.storage.queues.queueclient.sendmessageasync) metody. Zapisuje również [`SendReceipt`](/dotnet/api/azure.storage.queues.models.sendreceipt) zwrot z `SendMessageAsync` wywołania. Potwierdzenie jest używane do aktualizacji wiadomości w dalszej części tego programu.

Dodaj ten kod na końcu `Main` metody:

```csharp
Console.WriteLine("\nAdding messages to the queue...");

// Send several messages to the queue
await queueClient.SendMessageAsync("First message");
await queueClient.SendMessageAsync("Second message");

// Save the receipt so we can update this message later
SendReceipt receipt = await queueClient.SendMessageAsync("Third message");
```

### <a name="peek-at-messages-in-a-queue"></a>Wgląd w wiadomości w kolejce

Wgląd w komunikaty w kolejce przez wywołanie [`PeekMessagesAsync`](/dotnet/api/azure.storage.queues.queueclient.peekmessagesasync) metody. Ta metoda pobiera co najmniej jeden komunikat z przodu kolejki, ale nie zmienia widoczności komunikatu.

Dodaj ten kod na końcu `Main` metody:

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

Zaktualizuj zawartość komunikatu, wywołując [`UpdateMessageAsync`](/dotnet/api/azure.storage.queues.queueclient.updatemessageasync) metodę. Ta metoda może zmienić limit czasu i treść wiadomości. Zawartość komunikatu musi być ciągiem zakodowanym w formacie UTF-8, który ma rozmiar do 64 KB. Wraz z nową zawartością wiadomości przekaż wartości z, `SendReceipt` które zostały zapisane wcześniej w kodzie. `SendReceipt`Wartości identyfikują, którą wiadomość należy zaktualizować.

```csharp
Console.WriteLine("\nUpdating the third message in the queue...");

// Update a message using the saved receipt from sending the message
await queueClient.UpdateMessageAsync(receipt.MessageId, receipt.PopReceipt, "Third message has been updated");
```

### <a name="receive-messages-from-a-queue"></a>Odbieranie komunikatów z kolejki

Pobierz wcześniej dodane wiadomości, wywołując [`ReceiveMessagesAsync`](/dotnet/api/azure.storage.queues.queueclient.receivemessagesasync) metodę.

Dodaj ten kod na końcu `Main` metody:

```csharp
Console.WriteLine("\nReceiving messages from the queue...");

// Get messages from the queue
QueueMessage[] messages = await queueClient.ReceiveMessagesAsync(maxMessages: 10);
```

### <a name="delete-messages-from-a-queue"></a>Usuwanie komunikatów z kolejki

Usuń komunikaty z kolejki po ich przetworzeniu. W takim przypadku przetwarzanie właśnie wyświetla komunikat w konsoli programu.

Aplikacja wstrzymuje się do wprowadzania danych przez użytkownika, wywołując przed przetworzeniem `Console.ReadLine` i usunięciem komunikatów. Przed usunięciem [Azure Portal](https://portal.azure.com) Sprawdź, czy zasoby zostały utworzone prawidłowo. Wszystkie komunikaty, które nie zostały jawnie usunięte, zostaną ostatecznie wyświetlone w kolejce w celu przetworzenia ich przez inną szansę.

Dodaj ten kod na końcu `Main` metody:

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

Poniższy kod czyści zasoby utworzone przez aplikację przez usunięcie kolejki przy użyciu [`DeleteAsync`](/dotnet/api/azure.storage.queues.queueclient.deleteasync) metody.

Dodaj ten kod na końcu `Main` metody:

```csharp
Console.WriteLine("\nPress Enter key to delete the queue...");
Console.ReadLine();

// Clean up
Console.WriteLine($"Deleting queue: {queueClient.Name}");
await queueClient.DeleteAsync();

Console.WriteLine("Done");
```

## <a name="run-the-code"></a>Uruchamianie kodu

Ta aplikacja tworzy i dodaje trzy komunikaty do kolejki platformy Azure. Kod wyświetla listę komunikatów w kolejce, a następnie pobiera i usuwa je przed usunięciem kolejki.

W oknie konsoli przejdź do katalogu aplikacji, a następnie Skompiluj i uruchom aplikację.

```console
dotnet build
```

```console
dotnet run
```

Dane wyjściowe aplikacji są podobne do następujących:

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

Gdy aplikacja jest wstrzymywana przed odebraniem wiadomości, Sprawdź konto magazynu w [Azure Portal](https://portal.azure.com). Sprawdź, czy w kolejce znajdują się komunikaty.

Naciśnij klawisz, `Enter` Aby odebrać i usunąć komunikaty. Po wyświetleniu monitu ponownie naciśnij klawisz, `Enter` Aby usunąć kolejkę i zakończyć demonstrację.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start pokazano, jak utworzyć kolejkę i dodać do niej komunikaty przy użyciu asynchronicznego kodu platformy .NET. Następnie nauczysz się wglądu, pobierania i usuwania komunikatów. Na koniec wiesz już, jak usunąć kolejkę komunikatów.

Samouczki, przykłady, szybki start i inne dokumenty można znaleźć w temacie:

> [!div class="nextstepaction"]
> [Platforma Azure dla deweloperów .NET i .NET Core](/dotnet/azure/)

- Aby dowiedzieć się więcej, zobacz [biblioteki usługi Azure Storage dla platformy .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage).
- Aby uzyskać więcej przykładowych aplikacji platformy Azure Queue Storage, zobacz [Biblioteka klienta platformy azure queue storage dla przykładów platformy .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples).
- Aby dowiedzieć się więcej na temat platformy .NET Core, zobacz [Get started with .NET in 10 minutes (Rozpoczynanie pracy z platformą .NET w 10 minut)](https://www.microsoft.com/net/learn/get-started/).
