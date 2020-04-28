---
title: Wprowadzenie do usługi queue storage przy użyciu programu Visual Studio (ASP.NET Core)
description: Jak rozpocząć korzystanie z usługi Azure queue storage w projekcie ASP.NET Core w programie Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: 04977069-5b2d-4cba-84ae-9fb2f5eb1006
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: article
ms.date: 11/14/2017
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5cdf6f2644788674df91b533c9444fc88ab30b09
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "72300021"
---
# <a name="get-started-with-queue-storage-and-visual-studio-connected-services-aspnet-core"></a>Wprowadzenie do usługi queue storage i usług połączonych programu Visual Studio (ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

W tym artykule opisano sposób rozpoczynania pracy z usługą Azure queue storage w programie Visual Studio po utworzeniu lub przywoływaniu konta usługi Azure Storage w projekcie ASP.NET Core przy użyciu funkcji **usługi połączone** programu Visual Studio. Operacja **połączone usługi** instaluje odpowiednie pakiety NuGet w celu uzyskania dostępu do usługi Azure Storage w projekcie i dodaje parametry połączenia dla konta magazynu do plików konfiguracji projektu. (Zobacz [dokumentację magazynu](https://azure.microsoft.com/documentation/services/storage/) , aby uzyskać ogólne informacje na temat usługi Azure Storage).

Azure queue storage to usługa służąca do przechowywania dużej liczby komunikatów, do których można uzyskać dostęp z dowolnego miejsca na świecie za pośrednictwem uwierzytelnionych połączeń przy użyciu protokołu HTTP lub HTTPS. Jeden komunikat w kolejce może mieć długość do 64 kilobajtów (KB), a Kolejka może zawierać miliony komunikatów, do łącznego limitu pojemności konta magazynu. Zobacz też wprowadzenie do [usługi Azure queue storage przy użyciu platformy .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) , aby uzyskać szczegółowe informacje na temat programistycznego manipulowania kolejkami.

Aby rozpocząć, najpierw utwórz kolejkę platformy Azure na koncie magazynu. W tym artykule pokazano, jak utworzyć kolejkę w języku C# i jak wykonywać podstawowe operacje na kolejkach, takie jak dodawanie, modyfikowanie, odczytywanie i usuwanie komunikatów w kolejce.  Kod używa biblioteki klienta usługi Azure Storage dla platformy .NET. Aby uzyskać więcej informacji na temat ASP.NET, zobacz [ASP.NET](https://www.asp.net).

Niektóre interfejsy API usługi Azure Storage są asynchroniczne, a w kodzie w tym artykule przyjęto, że metody asynchroniczne są używane. Aby uzyskać więcej informacji, zobacz [programowanie asynchroniczne](https://docs.microsoft.com/dotnet/csharp/async) .

## <a name="access-queues-in-code"></a>Dostęp do kolejek w kodzie

Aby uzyskać dostęp do kolejek w projektach ASP.NET Core, należy uwzględnić następujące elementy w dowolnym pliku źródłowym C#, który uzyskuje dostęp do usługi Azure queue storage. Użyj całego kodu przed kodem w poniższych sekcjach.

1. Dodaj wymagane `using` instrukcje:
    ```cs
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Queue;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```

1. Pobierz `CloudStorageAccount` obiekt reprezentujący informacje o koncie magazynu. Użyj poniższego kodu, aby uzyskać parametry połączenia magazynu i informacje o koncie magazynu z konfiguracji usługi platformy Azure:

    ```cs
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Pobierz `CloudQueueClient` obiekt, aby odwołać się do obiektów kolejki na koncie magazynu:

    ```cs
    // Create the CloudQueueClient object for the storage account.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Pobierz `CloudQueue` obiekt odwołujący się do określonej kolejki:

    ```cs
    // Get a reference to the CloudQueue named "messagequeue"
    CloudQueue messageQueue = queueClient.GetQueueReference("messagequeue");
    ```

### <a name="create-a-queue-in-code"></a>Tworzenie kolejki w kodzie

Aby utworzyć kolejkę platformy Azure w kodzie, `CreateIfNotExistsAsync`Wywołaj:

```cs
// Create the CloudQueue if it does not exist.
await messageQueue.CreateIfNotExistsAsync();
```

## <a name="add-a-message-to-a-queue"></a>Dodawanie komunikatu do kolejki

Aby wstawić komunikat do istniejącej kolejki, Utwórz nowy `CloudQueueMessage` obiekt, a następnie Wywołaj `AddMessageAsync` metodę. `CloudQueueMessage` Obiekt można utworzyć na podstawie ciągu (w formacie UTF-8) lub tablicy bajtowej.

```cs
// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await messageQueue.AddMessageAsync(message);
```

## <a name="read-a-message-in-a-queue"></a>Odczytaj wiadomość w kolejce

Możesz wgląd do komunikatu z przodu kolejki bez usuwania go z kolejki, wywołując `PeekMessageAsync` metodę:

```cs
// Peek the next message in the queue.
CloudQueueMessage peekedMessage = await messageQueue.PeekMessageAsync();
```

## <a name="read-and-remove-a-message-in-a-queue"></a>Odczytywanie i usuwanie komunikatu w kolejce

Twój kod może usunąć (z kolejki) komunikat w kolejce w dwóch krokach.

1. Wywołaj `GetMessageAsync` , aby pobrać następną wiadomość w kolejce. Komunikat zwrócony z programu `GetMessageAsync` stał się niewidoczny dla każdego innego kodu odczytującego komunikaty z tej kolejki. Domyślnie komunikat pozostanie niewidoczny przez 30 sekund.
1. Aby zakończyć usuwanie komunikatu z kolejki, wywołaj `DeleteMessageAsync`polecenie.

Ten dwuetapowy proces usuwania komunikatów gwarantuje, że jeśli kod nie będzie w stanie przetworzyć komunikatu z powodu awarii sprzętu lub oprogramowania, inne wystąpienie kodu będzie w stanie uzyskać ten sam komunikat i ponowić próbę. Następujący kod wywołuje `DeleteMessageAsync` się bezpośrednio po przetworzeniu komunikatu:

```cs
// Get the next message in the queue.
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();

// Process the message in less than 30 seconds.

// Then delete the message.
await messageQueue.DeleteMessageAsync(retrievedMessage);
```

## <a name="additional-options-for-dequeuing-messages"></a>Dodatkowe opcje związane z dekolejką komunikatów

Istnieją dwa sposoby dostosowywania pobierania komunikatów z kolejki. Po pierwsze można uzyskać komunikaty zbiorczo (do 32). Po drugie można ustawić dłuższy lub krótszy limit czasu niewidoczności, dzięki czemu kod będzie mieć więcej lub mniej czasu na pełne przetworzenie każdego komunikatu. Poniższy przykład kodu używa metody, `GetMessages` aby pobrać 20 komunikatów w jednym wywołaniu. Następnie przetwarza każdy komunikat przy użyciu `foreach` pętli. Ustawia również limitu czasu niewidoczności na pięć minut dla każdego komunikatu. Należy zauważyć, że czasomierz pięciu minut jest uruchamiany dla wszystkich komunikatów w tym samym czasie, więc po upływie pięciu minut wszystkie komunikaty, które nie zostały usunięte, staną się ponownie widoczne.

```cs
// Retrieve 20 messages at a time, keeping those messages invisible for 5 minutes, 
//   delete each message after processing.

foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.
    queue.DeleteMessage(message);
}
```

## <a name="get-the-queue-length"></a>Pobieranie długości kolejki

Możesz uzyskać szacunkową liczbę komunikatów w kolejce. `FetchAttributes` Metoda prosi usługę kolejki o pobranie atrybutów kolejki, łącznie z liczbą komunikatów. `ApproximateMethodCount` Właściwość zwraca ostatnią wartość pobraną przez `FetchAttributes` metodę bez wywoływania usługi kolejki.

```cs
// Fetch the queue attributes.
messageQueue.FetchAttributes();

// Retrieve the cached approximate message count.
int? cachedMessageCount = messageQueue.ApproximateMessageCount;

// Display the number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="use-the-async-await-pattern-with-common-queue-apis"></a>Używanie wzorca Async-await ze wspólnymi interfejsami API kolejki

Ten przykład pokazuje, jak używać wzorca Async-await ze wspólnymi interfejsami API kolejki `Async`kończącymi się na. Gdy używana jest Metoda asynchroniczna, wzorce asynchroniczne-await zawieszają wykonywanie lokalne do momentu ukończenia wywołania. To zachowanie umożliwia bieżącemu wątkowi wykonywanie innych zadań, które pomagają uniknąć wąskich gardeł w zakresie wydajności i poprawiać ogólną czas odpowiedzi aplikacji.

```cs
// Create a message to add to the queue.
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Async enqueue the message.
await messageQueue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message.
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Async delete the message.
await messageQueue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

## <a name="delete-a-queue"></a>Usuwanie kolejki

Aby usunąć kolejkę i wszystkie znajdujące się w niej komunikaty, wywołaj `Delete` metodę dla obiektu kolejki:

```cs
// Delete the queue.
messageQueue.Delete();
```

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]
