---
title: Rozpoczynanie pracy z usługą queue storage przy użyciu programu Visual Studio (Cloud Services)
description: Jak rozpocząć korzystanie z usługi Azure queue storage w projekcie usługi w chmurze w programie Visual Studio po nawiązaniu połączenia z kontem magazynu przy użyciu usług połączonych programu Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: da587aac-5e64-4e9a-8405-44cc1924881d
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure, devx-track-csharp
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 94f248edfebd6c6fedb78a54eee220c0ef38b4ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95545882"
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Wprowadzenie do usługi Azure queue storage i usług połączonych programu Visual Studio (projekty usług Cloud Services)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Omówienie
W tym artykule opisano sposób rozpoczynania pracy z usługą Azure queue storage w programie Visual Studio po utworzeniu lub przywoływaniu konta usługi Azure Storage w projekcie usług w chmurze przy użyciu okna dialogowego **Dodawanie połączonych usług** programu Visual Studio.

Pokażemy, jak utworzyć kolejkę w kodzie. Pokażemy również, jak wykonywać podstawowe operacje na kolejkach, takie jak dodawanie, modyfikowanie, odczytywanie i usuwanie komunikatów w kolejce. Przykłady są zapisywane w kodzie C# i używają [Microsoft Azure Storage biblioteki klienckiej dla platformy .NET](/previous-versions/azure/dn261237(v=azure.100)).

Operacja **Dodaj podłączone usługi** instaluje odpowiednie pakiety NuGet w celu uzyskania dostępu do usługi Azure Storage w projekcie i dodaje parametry połączenia dla konta magazynu do plików konfiguracji projektu.

* Zobacz Rozpoczynanie [pracy z usługą Azure queue storage przy użyciu platformy .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) , aby uzyskać więcej informacji na temat manipulowania kolejkami w kodzie.
* Aby uzyskać ogólne informacje o usłudze Azure Storage, zobacz [dokumentację usługi Storage](https://azure.microsoft.com/documentation/services/storage/) .
* Zobacz [dokumentację Cloud Services](https://azure.microsoft.com/documentation/services/cloud-services/) , aby uzyskać ogólne informacje na temat usług Azure Cloud Services.
* Aby uzyskać więcej informacji na temat programowania aplikacji ASP.NET, zobacz [ASP.NET](https://www.asp.net) .

Azure Queue Storage to usługa do przechowywania dużej liczby komunikatów, do której można uzyskać dostęp z dowolnego miejsca na świecie za pośrednictwem uwierzytelnionego połączenia za pomocą protokołu HTTP lub HTTPS. Pojedynczy komunikat z kolejki nie może przekraczać 64 KB, a kolejka może zawierać miliony komunikatów — maksymalnie liczbę nieprzekraczającą całkowitego limitu pojemności konta magazynu.

## <a name="access-queues-in-code"></a>Dostęp do kolejek w kodzie
Aby uzyskać dostęp do kolejek w projektach Cloud Services programu Visual Studio, należy uwzględnić następujące elementy w dowolnym pliku źródłowym C#, który uzyskuje dostęp do usługi Azure queue storage.

1. Upewnij się, że deklaracje przestrzeni nazw na początku pliku C# zawierają te instrukcje **using** .
   
    ```csharp
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Queue;
    ```
2. Pobierz obiekt **CloudStorageAccount** , który reprezentuje informacje o koncie magazynu. Użyj poniższego kodu, aby uzyskać informacje o parametrach połączenia magazynu i koncie magazynu z konfiguracji usługi platformy Azure.
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
3. Pobierz obiekt **CloudQueueClient** , aby odwoływać się do obiektów kolejki na koncie magazynu.  
   
    ```csharp
    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
4. Pobierz obiekt **CloudQueue** , aby odwołać się do określonej kolejki.
   
    ```csharp
    // Get a reference to a queue named "messageQueue"
    CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");
    ```

**Uwaga:** Skorzystaj ze wszystkich powyższych kodów przed kodem w poniższych przykładach.

## <a name="create-a-queue-in-code"></a>Tworzenie kolejki w kodzie
Aby utworzyć kolejkę w kodzie, należy po prostu dodać wywołanie do **metodę createifnotexists**.

```csharp
// Create the CloudQueue if it does not exist
messageQueue.CreateIfNotExists();
```

## <a name="add-a-message-to-a-queue"></a>Dodawanie komunikatu do kolejki
Aby wstawić komunikat do istniejącej kolejki, Utwórz nowy obiekt **CloudQueueMessage** , a następnie Wywołaj metodę **AddMessage** .

Obiekt **CloudQueueMessage** można utworzyć na podstawie ciągu (w formacie UTF-8) lub tablicy bajtowej.

Oto przykład, który wstawia komunikat "Hello, World".

```csharp
// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
messageQueue.AddMessage(message);
```

## <a name="read-a-message-in-a-queue"></a>Odczytaj wiadomość w kolejce
Możesz uzyskać wgląd w komunikat z przodu kolejki bez jego usuwania z kolejki, wywołując metodę **PeekMessage**.

```csharp
// Peek at the next message
CloudQueueMessage peekedMessage = messageQueue.PeekMessage();
```

## <a name="read-and-remove-a-message-in-a-queue"></a>Odczytywanie i usuwanie komunikatu w kolejce
Kod może usunąć (poza kolejką) komunikat z kolejki w dwóch krokach.

1. Wywołaj metodę **GetMessage** , aby uzyskać następną wiadomość w kolejce. Komunikat zwrócony z funkcji **GetMessage** staje się niewidoczny dla innego kodu odczytującego komunikaty z tej kolejki. Domyślnie komunikat pozostanie niewidoczny przez 30 sekund.
2. Aby zakończyć usuwanie komunikatu z kolejki, wywołaj **DeleteMessage**.

Ten dwuetapowy proces usuwania komunikatów gwarantuje, że jeśli kod nie będzie w stanie przetworzyć komunikatu z powodu awarii sprzętu lub oprogramowania, inne wystąpienie kodu będzie w stanie uzyskać ten sam komunikat i ponowić próbę. Poniższy kod wywołuje **DeleteMessage** bezpośrednio po przetworzeniu komunikatu.

```csharp
// Get the next message in the queue.
CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

// Process the message in less than 30 seconds

// Then delete the message.
await messageQueue.DeleteMessage(retrievedMessage);
```


## <a name="use-additional-options-to-process-and-remove-queue-messages"></a>Używanie dodatkowych opcji do przetwarzania i usuwania komunikatów w kolejce
Istnieją dwa sposoby dostosowania pobierania komunikatów z kolejki.

* Możesz uzyskać partia komunikatów (do 32).
* Można ustawić dłuższy lub krótszy limit czasu niewidoczności, co pozwala na zwiększenie lub skrócenie czasu w celu pełnego przetworzenia poszczególnych komunikatów. Poniższy przykład kodu wykorzystuje metodę **GetMessages**, aby pobrać 20 komunikatów w jednym wywołaniu. Następnie przetwarza każdy komunikat przy użyciu pętli **foreach**. Ustawia również limitu czasu niewidoczności na pięć minut dla każdego komunikatu. Należy zauważyć, że okres 5 minut rozpoczyna się dla wszystkich komunikatów w tym samym czasie, więc po upływie 5 minut od wywołania metody **GetMessages** wszystkie komunikaty, które nie zostały usunięte, będą widoczne ponownie.

Oto przykład:

```csharp
foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.

    // Then delete the message after processing
    messageQueue.DeleteMessage(message);

}
```

## <a name="get-the-queue-length"></a>Pobieranie długości kolejki
Możesz uzyskać szacunkową liczbę komunikatów w kolejce. Metoda **FetchAttributes** prosi usługę kolejki o pobranie atrybutów kolejki, w tym liczby komunikatów. Właściwość **ApproximateMethodCount** zwraca ostatnią wartość pobraną przez metodę **FetchAttributes** , bez wywoływania usługa kolejki.

```csharp
// Fetch the queue attributes.
messageQueue.FetchAttributes();

// Retrieve the cached approximate message count.
int? cachedMessageCount = messageQueue.ApproximateMessageCount;

// Display number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="use-the-async-await-pattern-with-common-azure-queue-apis"></a>Używanie wzorca Async-Await ze wspólnymi interfejsami API usługi Azure Queue
Ten przykład pokazuje, jak używać wzorca Async-Await ze wspólnymi interfejsami API usługi Azure Queue. Przykład wywołuje asynchroniczną wersję każdej z tych metod, może to być postrzegane przez **asynchroniczne** po rozwiązaniu każdej metody. Gdy używana jest metoda async, proces Async-await zawiesza wykonywanie lokalne do momentu zakończenia wywołania. To zachowanie umożliwia wykonywanie innych czynności przez bieżący wątek, co pomaga uniknąć wąskich gardeł w zakresie wydajności i zwiększa ogólną czas odpowiedzi aplikacji. Aby uzyskać szczegółowe informacje o wykorzystaniu wzorca Async-Await w programie .NET, zobacz [Async and Await (C# and Visual Basic)](/previous-versions/hh191443(v=vs.140)) (Async i Await [C# i Visual Basic]).

```csharp
// Create a message to put in the queue
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Add the message asynchronously
await messageQueue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Delete the message asynchronously
await messageQueue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

## <a name="delete-a-queue"></a>Usuwanie kolejki
Aby usunąć kolejkę i wszystkie zawarte w niej komunikaty, wywołaj metodę **Delete** na obiekcie kolejki.

```csharp
// Delete the queue.
messageQueue.Delete();
```

## <a name="next-steps"></a>Następne kroki
[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]