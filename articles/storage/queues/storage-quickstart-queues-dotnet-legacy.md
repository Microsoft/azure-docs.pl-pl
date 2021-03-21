---
title: 'Szybki Start: korzystanie z usługi Azure Storage v11 dla platformy .NET do zarządzania kolejką'
description: Z tego przewodnika Szybki start dowiesz się, jak używać biblioteki klienta usługi Azure Storage dla platformy .NET do tworzenia kolejki i dodawania do niej komunikatów. Następnie dowiesz się, jak odczytywać i przetwarzać komunikaty z kolejki.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 07/24/2020
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-csharp
ms.openlocfilehash: 23703dc507aa909aea4711289a4d7d5c5e6a170e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97588203"
---
# <a name="quickstart-use-the-azure-storage-sdk-v11-for-net-to-manage-a-queue"></a>Szybki Start: korzystanie z usługi Azure Storage SDK v11 for .NET do zarządzania kolejką

W tym przewodniku szybki start dowiesz się, jak utworzyć kolejkę i dodać do niej komunikaty przy użyciu biblioteki klienta usługi Azure Storage v11 dla platformy .NET. Następnie dowiesz się, jak odczytywać i przetwarzać komunikaty z kolejki.

> [!NOTE]
> Ten przewodnik Szybki Start używa starszej wersji biblioteki klienta Queue Storage platformy Azure. Aby rozpocząć pracę z najnowszą wersją, zobacz [Szybki Start: Azure queue storage Client Library V12 for .NET](storage-quickstart-queues-dotnet.md).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Następnie pobierz i zainstaluj program .NET Core 2.0 dla swojego systemu operacyjnego. Jeśli korzystasz z systemu Windows, możesz również zainstalować program Visual Studio i używać środowiska .NET Framework. Możesz również zainstalować edytor do korzystania w systemie operacyjnym.

### <a name="windows"></a>Windows

- Zainstaluj program[.NET Core dla systemu Windows](https://www.microsoft.com/net/download/windows) lub program [.NET Framework](https://www.microsoft.com/net/download/windows) (dołączony do programu Visual Studio dla systemu Windows)
- Zainstaluj program [Visual Studio dla systemu Windows](https://www.visualstudio.com/). Jeśli używasz programu .NET Core, instalacja programu Visual Studio jest opcjonalna.

Aby uzyskać informacje dotyczące wyboru między programem.NET Core i programem.NET Framework, zobacz [Choose between .NET Core and .NET Framework for server apps (Wybieranie między programami .NET Core i .NET Framework w przypadku aplikacji serwera)](/dotnet/standard/choosing-core-framework-server).

### <a name="linux"></a>Linux

- Zainstaluj program [.NET Core dla systemu Linux](https://www.microsoft.com/net/download/linux)
- Opcjonalnie zainstaluj program [Visual Studio Code](https://www.visualstudio.com/) i [rozszerzenie języka C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)

### <a name="macos"></a>macOS

- Zainstaluj program [.NET Core dla systemu macOS](https://www.microsoft.com/net/download/macos)
- Opcjonalnie zainstaluj program [Visual Studio dla komputerów Mac](https://www.visualstudio.com/vs/visual-studio-mac/)

## <a name="download-the-sample-application"></a>Pobieranie przykładowej aplikacji

Przykładowa aplikacja używana w tym przewodniku Szybki start to podstawowa aplikacja konsoli. Przykładową aplikację można eksplorować w serwisie [GitHub](https://github.com/Azure-Samples/storage-queues-dotnet-quickstart).

Użyj [narzędzia Git](https://git-scm.com/) , aby pobrać kopię aplikacji do środowiska deweloperskiego.

```bash
git clone https://github.com/Azure-Samples/storage-queues-dotnet-quickstart.git
```

To polecenie klonuje repozytorium do lokalnego folderu git. Aby otworzyć rozwiązanie Visual Studio, poszukaj `storage-queues-dotnet-quickstart` folderu, otwórz go i kliknij dwukrotnie `storage-queues-dotnet-quickstart.sln` .

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Konfigurowanie parametrów połączenia magazynu

Aby uruchomić aplikację, należy wprowadzić parametry połączenia konta magazynu. Przykładowa aplikacja odczytuje parametry połączenia ze zmiennej środowiskowej i używa ich do autoryzacji żądań w usłudze Azure Storage.

Po skopiowaniu parametrów połączenia zapisz je w nowej zmiennej środowiskowej na komputerze, na którym uruchomiona jest aplikacja. Aby ustawić zmienną środowiskową, otwórz okno konsoli i postępuj zgodnie z instrukcjami dla systemu operacyjnego. Zastąp wartość `<yourconnectionstring>` rzeczywistymi parametrami połączenia:

### <a name="windows"></a>Windows

```cmd
setx storageconnectionstring "<yourconnectionstring>"
```

Po dodaniu zmiennej środowiskowej być może trzeba będzie ponownie uruchomić działające programy, które muszą odczytywać zmienną środowiskową, w tym okno konsoli. Jeśli na przykład używasz programu Visual Studio jako edytora, uruchom ponownie program Visual Studio przed uruchomieniem przykładu.

### <a name="linux"></a>Linux

```bash
export storageconnectionstring=<yourconnectionstring>
```

Po dodaniu zmiennej środowiskowej uruchom polecenie `source ~/.bashrc` z okna konsoli, aby zmiany zostały uwzględnione.

### <a name="macos"></a>macOS

Edytuj swój plik .bash_profile i dodaj zmienną środowiskową:

```bash
export STORAGE_CONNECTION_STRING=<yourconnectionstring>
```

Po dodaniu zmiennej środowiskowej uruchom polecenie `source .bash_profile` z okna konsoli, aby zmiany zostały uwzględnione.

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Przykładowa aplikacja tworzy kolejkę i dodaje do niej komunikat. Aplikacja najpierw sprawdza komunikat bez usuwania go z kolejki, a następnie pobiera komunikat i usuwa go z kolejki.

### <a name="windows"></a>Windows

Jeśli używasz programu Visual Studio jako edytora, możesz nacisnąć klawisz, `F5` Aby uruchomić.

W pozostałych przypadkach przejdź do katalogu aplikacji, a następnie uruchom aplikację za pomocą polecenia `dotnet run`.

```
dotnet run
```

### <a name="linux"></a>Linux

Przejdź do katalogu aplikacji, a następnie uruchom aplikację za pomocą polecenia `dotnet run`.

```
dotnet run
```

### <a name="macos"></a>macOS

Przejdź do katalogu aplikacji, a następnie uruchom aplikację za pomocą polecenia `dotnet run`.

```
dotnet run
```

Dane wyjściowe przykładowej aplikacji będą podobne do poniższego przykładu:

```
Azure Queues - .NET Quickstart sample

Created queue 'quickstartqueues-3136fe9a-fa52-4b19-a447-8999a847da52'

Added message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7' to queue 'quickstartqueues-3136fe9a-fa52-4b19-a447-8999a847da52'
Message insertion time: 2/7/2019 4:30:46 AM +00:00
Message expiration time: 2/14/2019 4:30:46 AM +00:00

Contents of peeked message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7': Hello, World

Message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7' becomes visible again at 2/7/2019 4:31:16 AM +00:00

Processed and deleted message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7'

Press any key to delete the sample queue.
```

## <a name="understand-the-sample-code"></a>Omówienie przykładowego kodu

Następnie omówimy przykładowy kod, aby wyjaśnić, w jaki sposób działa.

### <a name="try-parsing-the-connection-string"></a>Próba analizowania parametrów połączenia

Przykład najpierw sprawdza, czy zmienna środowiskowa zawiera parametry połączenia, które mogą być analizowane w celu utworzenia [`CloudStorageAccount`](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount) obiektu wskazującego na konto magazynu. Aby sprawdzić, czy parametry połączenia są prawidłowe, przykład używa [`TryParse`](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount.tryparse) metody. Jeśli `TryParse` się powiedzie, inicjuje `storageAccount` zmienną i zwraca `true` .

```csharp
// Retrieve the connection string for use with the application. The storage connection string is stored
// in an environment variable called storageconnectionstring, on the machine where the application is running.
// If the environment variable is created after the application is launched in a console or with Visual
// Studio, the shell needs to be closed and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("storageconnectionstring");

// Check whether the connection string can be parsed.
if (CloudStorageAccount.TryParse(storageConnectionString, out storageAccount))
{
    // If the connection string is valid, proceed with calls to Azure Queues here.
    ...
}
else
{
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add an environment variable named 'storageconnectionstring' with your storage " +
        "connection string as a value.");
}
```

### <a name="create-the-queue"></a>Tworzenie kolejki

Najpierw przykładowy kod tworzy kolejkę i dodaje do niej komunikat.

```csharp
// Create a queue called 'quickstartqueues' and append a GUID value so that the queue name
// is unique in your storage account.
queue = cloudQueueClient.GetQueueReference("quickstartqueues-" + Guid.NewGuid().ToString());
await queue.CreateAsync();

Console.WriteLine("Created queue '{0}'", queue.Name);
Console.WriteLine();
```

### <a name="add-a-message"></a>Dodawanie komunikatu

Następnie przykładowy kod dodaje komunikat na końcu kolejki.

Wiadomość musi być w formacie, który można uwzględnić w żądaniu XML z kodowaniem UTF-8, a jego maksymalny rozmiar może wynosić maksymalnie 64 KB. Jeśli komunikat zawiera dane binarne, zalecamy kodowanie Base64 wiadomości.

Domyślnie maksymalny czas wygaśnięcia komunikatu wynosi 7 dni. Można określić dowolną liczbę dodatnią dla komunikatu czas wygaśnięcia.

```csharp
// Create a message and add it to the queue. Set expiration time to 14 days.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await queue.AddMessageAsync(message, new TimeSpan(14,0,0,0), null, null, null);
Console.WriteLine("Added message '{0}' to queue '{1}'", message.Id, queue.Name);
Console.WriteLine("Message insertion time: {0}", message.InsertionTime.ToString());
Console.WriteLine("Message expiration time: {0}", message.ExpirationTime.ToString());
Console.WriteLine();
```

Aby dodać komunikat, który nie wygasa, użyj `Timespan.FromSeconds(-1)` w wywołaniu metody [`AddMessageAsync`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync) .

```csharp
await queue.AddMessageAsync(message, TimeSpan.FromSeconds(-1), null, null, null);
```

### <a name="peek-a-message-from-the-queue"></a>Wgląd do wiadomości z kolejki

Przykład pokazuje, jak uzyskiwać wgląd w komunikat z kolejki. Po uzyskaniu wglądu w komunikat można przeczytać jego zawartość. Komunikat pozostanie jednak widoczny dla innych klientów, tak aby inny klient mógł następnie pobrać i przetworzyć komunikat.

```csharp
// Peek at the message at the front of the queue. Peeking does not alter the message's
// visibility, so that another client can still retrieve and process it.
CloudQueueMessage peekedMessage = await queue.PeekMessageAsync();

// Display the ID and contents of the peeked message.
Console.WriteLine("Contents of peeked message '{0}': {1}", peekedMessage.Id, peekedMessage.AsString);
Console.WriteLine();
```

### <a name="dequeue-a-message"></a>Usuwanie komunikatu z kolejki

W przykładzie pokazano również, jak usunąć komunikat z kolejki. Usuwanie komunikatów z kolejki polega na pobraniu komunikat z początku kolejki i renderowanie go jako tymczasowo niewidocznego dla innych klientów. Domyślnie komunikat pozostanie niewidoczny przez 30 sekund. W tym czasie kod może przetworzyć komunikat. Aby zakończyć usuwanie komunikatu z kolejki, usuwasz komunikat natychmiast po zakończeniu przetwarzania, tak aby inny klient nie mógł usunąć tego samego komunikatu z kolejki.

Jeśli kod nie może przetworzyć komunikatu z powodu awarii sprzętu lub oprogramowania, komunikat stanie się ponownie widoczny po zakończeniu okresu niewidoczności. Inny klient może pobrać ten sam komunikat i spróbować ponownie.

```csharp
// Retrieve the message at the front of the queue. The message becomes invisible for
// a specified interval, during which the client attempts to process it.
CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();

// Display the time at which the message will become visible again if it is not deleted.
Console.WriteLine("Message '{0}' becomes visible again at {1}", retrievedMessage.Id, retrievedMessage.NextVisibleTime);
Console.WriteLine();

//Process and delete the message within the period of invisibility.
await queue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Processed and deleted message '{0}'", retrievedMessage.Id);
Console.WriteLine();
```

### <a name="clean-up-resources"></a>Czyszczenie zasobów

Przykład czyści zasoby utworzone przez usunięcie kolejki. Usunięcie kolejki spowoduje również usunięcie innych znajdujących się w niej komunikatów.

```csharp
Console.WriteLine("Press any key to delete the sample queue.");
Console.ReadLine();
Console.WriteLine("Deleting the queue and any messages it contains...");
Console.WriteLine();
if (queue != null)
{
    await queue.DeleteIfExistsAsync();
}
```

## <a name="resources-for-developing-net-applications-with-queues"></a>Zasoby używane do tworzenia aplikacji .NET z kolejkami

Zapoznaj się z tymi dodatkowymi zasobami na potrzeby programowania .NET za pomocą usługi Azure Queue Storage:

### <a name="binaries-and-source-code"></a>Pliki binarne i kod źródłowy

- Pobierz pakiety NuGet dla najnowszej wersji [biblioteki klienta usługi Azure Storage dla platformy .NET](/dotnet/api/overview/azure/storage)
  - [Wspólne](https://www.nuget.org/packages/microsoft.azure.storage.common/)
  - [Kolejki](https://www.nuget.org/packages/Azure.Storage.Queues/)
- Wyświetl [kod źródłowy biblioteki klienta .NET](https://github.com/Azure/azure-storage-net) w usłudze GitHub.

### <a name="azure-storage-client-library-reference-and-samples"></a>Dokumentacja biblioteki klienta usługi Azure Storage i przykłady

- Aby uzyskać więcej informacji na temat bibliotek klienckich platformy .NET, zobacz [Dokumentacja biblioteki klienta usługi Azure Storage dla platformy .NET](/dotnet/api/overview/azure/storage) .
- Eksploruj [queue storage przykładów](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=queues) pisanych przy użyciu biblioteki klienckiej .NET.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start przedstawiono sposób dodawania komunikatów do kolejki, uzyskiwania wglądu w komunikaty w kolejce oraz usuwania komunikatów z kolejki i przetwarzania ich przy użyciu platformy .NET.

> [!div class="nextstepaction"]
> [Komunikacja między aplikacjami przy użyciu usługi Azure Queue Storage](/learn/modules/communicate-between-apps-with-azure-queue-storage/index)

- Aby dowiedzieć się więcej na temat platformy .NET Core, zobacz [Get started with .NET in 10 minutes (Rozpoczynanie pracy z platformą .NET w 10 minut)](https://www.microsoft.com/net/learn/get-started/).
