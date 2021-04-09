---
title: Rozpoczynanie pracy z usługą Azure Queue Storage przy użyciu platformy .NET — Azure Storage
description: Usługa Azure Queue Storage zapewnia niezawodne, asynchroniczne komunikaty między składnikami aplikacji. Przesyłanie komunikatów za pomocą chmury umożliwia składnikom aplikacji niezależne skalowanie.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 10/08/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-csharp
ms.openlocfilehash: d54b8f15c90aa8f6ffcc04453fee0349e501f47d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97585755"
---
# <a name="get-started-with-azure-queue-storage-using-net"></a>Rozpoczynanie pracy z usługą Azure Queue Storage przy użyciu platformy .NET

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Omówienie

Usługa Azure Queue Storage zapewnia obsługę komunikatów w chmurze między składnikami aplikacji. Podczas projektowania aplikacji do skalowania składniki aplikacji są często rozłączane, dzięki czemu mogą być skalowane niezależnie. Queue Storage zapewnia asynchroniczne przesyłanie komunikatów między składnikami aplikacji, niezależnie od tego, czy działają w chmurze, na komputerze, na serwerze lokalnym, czy na urządzeniu przenośnym. Queue Storage obsługuje również zarządzanie zadaniami asynchronicznymi i przepływami pracy procesu kompilacji.

### <a name="about-this-tutorial"></a>Informacje o tym samouczku

W tym samouczku pokazano, jak napisać kod .NET dla niektórych typowych scenariuszy przy użyciu usługi Azure Queue Storage. Omówione scenariusze obejmują tworzenie i usuwanie kolejek oraz dodawanie, odczytywanie i usuwanie komunikatów kolejek.

**Szacowany czas trwania:** 45 minut

### <a name="prerequisites"></a>Wymagania wstępne

- [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
- [Konto usługi Azure Storage](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="set-up-your-development-environment"></a>Konfigurowanie środowiska projektowego

Następnie skonfiguruj środowisko projektowe w programie Visual Studio, aby przygotować się do wypróbowania przykładów kodu zawartych w tym przewodniku.

### <a name="create-a-windows-console-application-project"></a>Utwórz projekt aplikacji konsolowej dla systemu Windows

W programie Visual Studio utwórz nową aplikację konsoli dla systemu Windows. Poniższe kroki pokazują, jak utworzyć aplikację konsolową w programie Visual Studio 2019. Procedura jest podobna w innych wersjach programu Visual Studio.

1. Wybierz pozycję **plik**  >  **Nowy**  >  **projekt**
2. Wybierz   >  **okna** platformy
3. Wybieranie **aplikacji konsolowej (.NET Framework)**
4. Wybierz pozycję **Dalej**
5. W polu **Nazwa projektu** wprowadź nazwę aplikacji
6. Wybierz pozycję **Utwórz**

Wszystkie przykłady kodu w tym samouczku można dodać do metody `Main()` w pliku `Program.cs` aplikacji konsolowej.

Możesz użyć bibliotek klienckich usługi Azure Storage w dowolnym typie aplikacji platformy .NET, w tym usługi w chmurze platformy Azure lub aplikacji sieci Web oraz aplikacji klasycznych i mobilnych. W tym przewodniku dla uproszczenia przedstawiono aplikację konsolową.

### <a name="use-nuget-to-install-the-required-packages"></a>Użycie pakietu NuGet w celu zainstalowania wymaganych pakietów

# <a name="net-v12"></a>[V12 .NET](#tab/dotnet)

Aby ukończyć ten samouczek, należy odwołać się do następujących czterech pakietów w projekcie:

- [Biblioteka Azure. Core dla platformy .NET](https://www.nuget.org/packages/azure.core/): Ten pakiet zawiera udostępnione elementy podstawowe, abstrakcje i pomocnicy dla nowoczesnych bibliotek klienckich zestawu SDK platformy .NET.
- [Azure. Storage. Common Client Library for .NET](https://www.nuget.org/packages/azure.storage.common/): Ten pakiet udostępnia infrastrukturę udostępnioną przez inne biblioteki klienta usługi Azure Storage.
- [Biblioteka kliencka Azure. Storage. Queues dla platformy .NET](https://www.nuget.org/packages/azure.storage.queues/): Ten pakiet umożliwia pracę z usługą Azure queue storage do przechowywania komunikatów, do których dostęp jest możliwy dla klienta.
- [System.Configuration.ConfigurationManager Library for .NET](https://www.nuget.org/packages/system.configuration.configurationmanager/): Ten pakiet zapewnia dostęp do plików konfiguracji dla aplikacji klienckich.

Aby uzyskać te pakiety, można użyć narzędzia NuGet. Wykonaj następujące kroki:

1. Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań** i wybierz polecenie **Zarządzaj pakietami NuGet**.
1. Wybierz pozycję **Przeglądaj**
1. Wyszukaj w trybie online `Azure.Storage.Queues` , a następnie wybierz pozycję **Zainstaluj** , aby zainstalować bibliotekę klienta usługi Azure Storage i jej zależności. Spowoduje to również zainstalowanie bibliotek Azure. Storage. Common i Azure. Core, które są zależnościami biblioteki kolejki.
1. Wyszukaj w trybie online `System.Configuration.ConfigurationManager` , a następnie wybierz pozycję **Zainstaluj** , aby zainstalować Configuration Manager.

# <a name="net-v11"></a>[V11 .NET](#tab/dotnetv11)

Aby ukończyć ten samouczek, należy odwołać się do następujących trzech pakietów w projekcie:

- [Microsoft. Azure. Storage. Common Client Library for .NET](https://www.nuget.org/packages/microsoft.azure.storage.common/): Ten pakiet zapewnia programistyczny dostęp do zasobów danych na koncie magazynu.
- [Microsoft Azure queue storage biblioteki klienckiej dla platformy .NET](https://www.nuget.org/packages/microsoft.azure.storage.queue/): Ta biblioteka klienta umożliwia pracę z usługą Azure queue storage do przechowywania komunikatów, do których dostęp jest możliwy dla klienta.
- [Biblioteka programu Microsoft Azure Configuration Manager dla środowiska .NET](https://www.nuget.org/packages/microsoft.azure.configurationmanager/): ten pakiet zawiera klasę do analizowania parametrów połączenia w pliku konfiguracji, niezależnie od tego, gdzie została uruchomiona aplikacja.

Aby uzyskać te pakiety, można użyć narzędzia NuGet. Wykonaj następujące kroki:

1. Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań** i wybierz polecenie **Zarządzaj pakietami NuGet**.
1. Wybierz pozycję **Przeglądaj**
1. Wyszukaj w trybie online `Microsoft.Azure.Storage.Queue` , a następnie wybierz pozycję **Zainstaluj** , aby zainstalować bibliotekę klienta usługi Azure Storage i jej zależności. Spowoduje to również zainstalowanie biblioteki Microsoft. Azure. Storage. Common, która jest zależna od biblioteki kolejki.
1. Wyszukaj w trybie online `Microsoft.Azure.ConfigurationManager` , a następnie wybierz pozycję **Zainstaluj** , aby zainstalować Configuration Manager platformy Azure.

---

### <a name="determine-your-target-environment"></a>Określanie środowiska docelowego

W przypadku uruchamiania przykładów w tym przewodniku istnieją dwie opcje środowiska:

- Można uruchomić kod dla konta usługi Azure Storage w chmurze.
- Możesz uruchomić swój kod względem emulatora magazynu azurite. Azurite to środowisko lokalne, które emuluje konto usługi Azure Storage w chmurze. Azurite to bezpłatna opcja testowania i debugowania kodu podczas opracowywania aplikacji. Emulator używa dobrze znanego konta i klucza. Aby uzyskać więcej informacji, zobacz [Używanie emulatora azurite na potrzeby programowania i testowania lokalnego magazynu Azure](../common/storage-use-azurite.md).

> [!NOTE]
> Na obiekt docelowy można wybrać emulator magazynu, aby uniknąć ponoszenia kosztów związanych z usługą Azure Storage. Jeśli jednak wybierzesz opcję określania konta usługi Azure Storage w chmurze, koszty związane z wykonaniem tego samouczka będą nieistotne.

## <a name="get-your-storage-connection-string"></a>Pobieranie parametrów połączenia magazynu

Biblioteki klienta usługi Azure Storage dla platformy .NET obsługują używanie parametrów połączenia magazynu w celu skonfigurowania punktów końcowych i poświadczeń w celu uzyskania dostępu do usług magazynu. Aby uzyskać więcej informacji, zobacz [Zarządzanie kluczami dostępu do konta magazynu](../common/storage-account-keys-manage.md).

### <a name="copy-your-credentials-from-the-azure-portal"></a>Kopiowanie poświadczeń z witryny Azure Portal

Przykładowy kod musi autoryzować dostęp do konta magazynu. Na potrzeby autoryzacji podajesz aplikacji swoje poświadczenia konta magazynu w postaci parametrów połączenia. Aby wyświetlić swoje poświadczenia konta magazynu:

1. Przejdź do [Azure Portal](https://portal.azure.com).
2. Odszukaj konto magazynu.
3. W sekcji **Ustawienia** omówienia kont magazynu wybierz pozycję **Klucze dostępu**. Zostaną wyświetlone klucze dostępu do Twojego konta, a także pełne parametry połączenia dla każdego klucza.
4. Znajdź wartość **Parametry połączenia** w obszarze **key1** i kliknij przycisk **Kopiuj**, aby skopiować parametry połączenia. W następnym kroku dodasz wartość parametrów połączenia do zmiennej środowiskowej.

    ![Zrzut ekranu pokazujący sposób kopiowania parametrów połączenia z witryny Azure Portal](media/storage-dotnet-how-to-use-queues/portal-connection-string.png)

Aby uzyskać więcej informacji dotyczących parametrów połączenia, zobacz [Konfigurowanie parametrów połączenia z usługą Azure Storage](../common/storage-configure-connection-string.md).

> [!NOTE]
> Klucz konta magazynu jest podobny do hasła głównego konta magazynu. Zawsze chroń klucz konta magazynu. Nie udostępniaj go innym użytkownikom, nie koduj go trwale ani nie zapisuj w zwykłym pliku tekstowym, do którego mają dostęp inne osoby. Wygeneruj ponownie klucz za pośrednictwem witryny Azure Portal, jeśli uważasz, że jego zabezpieczenia mogły zostać naruszone.

Najlepiej przechowywać parametry połączenia magazynu w pliku konfiguracji. Aby skonfigurować parametry połączenia, otwórz plik `app.config` z Eksploratora rozwiązań w programie Visual Studio. Dodaj zawartość `<appSettings>` podanego elementu. Zamień na `connection-string` wartość skopiowaną z konta magazynu w portalu:

```xml
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.7.2" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="connection-string" />
    </appSettings>
</configuration>
```

Na przykład ustawienie konfiguracji może wyglądać mniej więcej tak:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==EndpointSuffix=core.windows.net" />
```

Aby kierować emulator magazynu azurite, można użyć skrótu, który jest mapowany na dobrze znaną nazwę i klucz konta. W takim przypadku ustawienie parametrów połączenia wygląda następująco:

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
```

### <a name="add-using-directives"></a>Dodawanie dyrektyw using

Dodaj następujące dyrektywy `using` na początku pliku `Program.cs`:

# <a name="net-v12"></a>[V12 .NET](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_UsingStatements":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnetv11)

```csharp
using System; // Namespace for Console output
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage; // Namespace for CloudStorageAccount
using Microsoft.Azure.Storage.Queue; // Namespace for Queue storage types
```

---

### <a name="create-the-queue-storage-client"></a>Tworzenie klienta Queue Storage

# <a name="net-v12"></a>[V12 .NET](#tab/dotnet)

[`QueueClient`](/dotnet/api/azure.storage.queues.queueclient)Klasa umożliwia pobieranie kolejek przechowywanych w queue storage. Oto jeden ze sposobów tworzenia klienta usługi:

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_CreateClient":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnetv11)

[`CloudQueueClient`](/dotnet/api/microsoft.azure.storage.queue.cloudqueueclient?view=azure-dotnet-legacy&preserve-view=true)Klasa umożliwia pobieranie kolejek przechowywanych w queue storage. Oto jeden ze sposobów tworzenia klienta usługi:

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
```

---

Teraz możesz przystąpić do pisania kodu, który odczytuje dane z i zapisuje dane w Queue Storage.

## <a name="create-a-queue"></a>Tworzenie kolejki

Ten przykład pokazuje, jak utworzyć kolejkę:

# <a name="net-v12"></a>[V12 .NET](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_CreateQueue":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnetv11)

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();
```

---

## <a name="insert-a-message-into-a-queue"></a>Wstawianie komunikatu do kolejki

# <a name="net-v12"></a>[V12 .NET](#tab/dotnet)

Aby wstawić komunikat do istniejącej kolejki, wywołaj [`SendMessage`](/dotnet/api/azure.storage.queues.queueclient.sendmessage) metodę. Komunikat może być ciągiem (w formacie UTF-8) lub tablicą bajtów. Poniższy kod tworzy kolejkę (jeśli nie istnieje) i wstawia komunikat:

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_InsertMessage":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnetv11)

Aby wstawić komunikat do istniejącej kolejki, najpierw utwórz nowy [`CloudQueueMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage?view=azure-dotnet-legacy&preserve-view=true) . Następnie Wywołaj [`AddMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessage?view=azure-dotnet-legacy&preserve-view=true) metodę. Element `CloudQueueMessage` można utworzyć na podstawie ciągu (w formacie UTF-8) lub tablicy bajtowej. Oto kod, który tworzy kolejkę (jeśli nie istnieje) i wstawia komunikat `Hello, World` : Aby wstawić komunikat do istniejącej kolejki, najpierw utwórz nowy [`CloudQueueMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage?view=azure-dotnet-legacy&preserve-view=true) . Następnie Wywołaj [`AddMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessage?view=azure-dotnet-legacy&preserve-view=true) metodę. Element `CloudQueueMessage` można utworzyć na podstawie ciągu (w formacie UTF-8) lub tablicy bajtowej. Oto kod, który tworzy kolejkę (jeśli nie istnieje) i wstawia komunikat `Hello, World` :

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();

// Create a message and add it to the queue
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
queue.AddMessage(message);
```

---

## <a name="peek-at-the-next-message"></a>Podgląd kolejnego komunikatu

# <a name="net-v12"></a>[V12 .NET](#tab/dotnet)

Możesz uzyskać wgląd w wiadomości w kolejce bez usuwania ich z kolejki, wywołując [`PeekMessages`](/dotnet/api/azure.storage.queues.queueclient.peekmessages) metodę. Jeśli wartość parametru nie zostanie przekazana `maxMessages` , domyślnie jest wgląd w jeden komunikat.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_PeekMessage":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnetv11)

Możesz wgląd do komunikatu z przodu kolejki bez usuwania go z kolejki, wywołując [`PeekMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.peekmessage?view=azure-dotnet-legacy&preserve-view=true) metodę.

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Peek at the next message
CloudQueueMessage peekedMessage = queue.PeekMessage();

// Display message.
Console.WriteLine(peekedMessage.AsString);
```

---

## <a name="change-the-contents-of-a-queued-message"></a>Zmiana zawartości komunikatu w kolejce

Możesz zmienić zawartość komunikatu w kolejce. Jeśli komunikat reprezentuje zadanie robocze, możesz użyć tej funkcji, aby zaktualizować stan zadania. Poniższy kod aktualizuje komunikat kolejki o nową zawartość i ustawia rozszerzenie limitu czasu widoczności o kolejne 60 sekund. Operacja ta zapisuje stan pracy powiązanej z komunikatem i daje klientowi kolejną minutę na kontynuowanie pracy nad komunikatem. Korzystając z tej metody, można śledzić wieloetapowe przepływy pracy dla komunikatów w kolejce, bez konieczności rozpoczynania od początku, jeśli etap przetwarzania zakończy się niepowodzeniem z powodu awarii sprzętu lub oprogramowania. Zazwyczaj stosuje się również liczbę ponownych prób. Jeśli komunikat zostanie ponowiony więcej niż *n* razy, zostanie usunięty. Jest to zabezpieczenie przed komunikatami, które wyzwalają błąd aplikacji zawsze, gdy są przetwarzane.

# <a name="net-v12"></a>[V12 .NET](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_UpdateMessage":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnetv11)

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the message from the queue and update the message contents.
CloudQueueMessage message = queue.GetMessage();
message.SetMessageContent2("Updated contents.", false);
queue.UpdateMessage(message,
    TimeSpan.FromSeconds(60.0),  // Make it invisible for another 60 seconds.
    MessageUpdateFields.Content | MessageUpdateFields.Visibility);
```

---

## <a name="dequeue-the-next-message"></a>Usuwa następny komunikat

# <a name="net-v12"></a>[V12 .NET](#tab/dotnet)

Usuwa komunikat z kolejki w dwóch krokach. Po wywołaniu otrzymujesz [`ReceiveMessages`](/dotnet/api/azure.storage.queues.queueclient.receivemessages) następną wiadomość w kolejce. Komunikat zwrócony z programu `ReceiveMessages` stał się niewidoczny dla każdego innego kodu odczytującego komunikaty z tej kolejki. Domyślnie komunikat pozostanie niewidoczny przez 30 sekund. Aby zakończyć usuwanie komunikatu z kolejki, należy również wywołać metodę [`DeleteMessage`](/dotnet/api/azure.storage.queues.queueclient.deletemessage) . Ten dwuetapowy proces usuwania komunikatów gwarantuje, że jeśli kod nie będzie w stanie przetworzyć komunikatu z powodu awarii sprzętu lub oprogramowania, inne wystąpienie kodu będzie w stanie uzyskać ten sam komunikat i ponowić próbę. Kod wywołuje `DeleteMessage` się bezpośrednio po przetworzeniu komunikatu.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DequeueMessage":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnetv11)

Kod usuwa komunikat z kolejki w dwóch krokach. Po wywołaniu otrzymujesz [`GetMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage?view=azure-dotnet-legacy&preserve-view=true) następną wiadomość w kolejce. Komunikat zwrócony z programu `GetMessage` stał się niewidoczny dla każdego innego kodu odczytującego komunikaty z tej kolejki. Domyślnie komunikat pozostanie niewidoczny przez 30 sekund. Aby zakończyć usuwanie komunikatu z kolejki, należy również wywołać metodę [`DeleteMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage?view=azure-dotnet-legacy&preserve-view=true) . Ten dwuetapowy proces usuwania komunikatów gwarantuje, że jeśli kod nie będzie w stanie przetworzyć komunikatu z powodu awarii sprzętu lub oprogramowania, inne wystąpienie kodu będzie w stanie uzyskać ten sam komunikat i ponowić próbę. Kod wywołuje `DeleteMessage` się bezpośrednio po przetworzeniu komunikatu.

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the next message
CloudQueueMessage retrievedMessage = queue.GetMessage();

//Process the message in less than 30 seconds, and then delete the message
queue.DeleteMessage(retrievedMessage);
```

---

<!-- docutune:casing "Async-Await pattern" "Async and Await" -->

## <a name="use-the-async-await-pattern-with-common-queue-storage-apis"></a>Używanie wzorca Async-Await ze wspólnymi interfejsami API Queue Storage

Ten przykład pokazuje, jak używać wzorca Async-Await ze wspólnymi interfejsami API Queue Storage. Przykład wywołuje asynchroniczną wersję każdej z podanych metod, zgodnie z `Async` sufiksem każdej metody. Gdy używana jest Metoda asynchroniczna, wzorzec Async-Await zawiesza wykonywanie lokalne do momentu zakończenia wywołania. Takie zachowanie umożliwia wykonywanie innych zadań przez bieżący wątek, co pomaga unikać wąskich gardeł zmniejszających wydajność i poprawia ogólną szybkość reakcji aplikacji. Aby uzyskać więcej informacji na temat używania wzorca Async-Await w programie .NET, zobacz [Async and Await (C# i Visual Basic)](/previous-versions/hh191443(v=vs.140))

# <a name="net-v12"></a>[V12 .NET](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_AsyncQueue":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnetv11)

```csharp
// Create the queue if it doesn't already exist
if(await queue.CreateIfNotExistsAsync())
{
    Console.WriteLine("Queue '{0}' Created", queue.Name);
}
else
{
    Console.WriteLine("Queue '{0}' Exists", queue.Name);
}

// Create a message to put in the queue
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Async enqueue the message
await queue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message
CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Async delete the message
await queue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

---

## <a name="use-additional-options-for-dequeuing-messages"></a>Użyj dodatkowych opcji, aby wykolejkować komunikaty

Istnieją dwa sposoby dostosowania pobierania komunikatów z kolejki. Po pierwsze można uzyskać komunikaty zbiorczo (do 32). Po drugie można ustawić dłuższy lub krótszy limit czasu niewidoczności, dzięki czemu kod będzie mieć więcej lub mniej czasu na pełne przetworzenie każdego komunikatu.

# <a name="net-v12"></a>[V12 .NET](#tab/dotnet)

Poniższy przykład kodu używa metody, [`ReceiveMessages`](/dotnet/api/azure.storage.queues.queueclient.receivemessages) Aby pobrać 20 komunikatów w jednym wywołaniu. Następnie przetwarza każdy komunikat przy użyciu `foreach` pętli. Ustawia również limitu czasu niewidoczności na pięć minut dla każdego komunikatu. Należy zauważyć, że pięć minut rozpoczyna się dla wszystkich komunikatów w tym samym czasie, więc po upływie pięciu minut od wywołania do `ReceiveMessages` , wszystkie komunikaty, które nie zostały usunięte, staną się znów widoczne.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DequeueMessages":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnetv11)

Poniższy przykład kodu używa metody, [`GetMessages`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessages?view=azure-dotnet-legacy&preserve-view=true) Aby pobrać 20 komunikatów w jednym wywołaniu. Następnie przetwarza każdy komunikat przy użyciu `foreach` pętli. Ustawia również limitu czasu niewidoczności na pięć minut dla każdego komunikatu. Należy zauważyć, że pięć minut rozpoczyna się dla wszystkich komunikatów w tym samym czasie, więc po upływie pięciu minut od wywołania do `GetMessages` , wszystkie komunikaty, które nie zostały usunięte, staną się znów widoczne.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.
    queue.DeleteMessage(message);
}
```

---

## <a name="get-the-queue-length"></a>Pobieranie długości kolejki

# <a name="net-v12"></a>[V12 .NET](#tab/dotnet)

Możesz uzyskać szacunkową liczbę komunikatów w kolejce. [`GetProperties`](/dotnet/api/azure.storage.queues.queueclient.getproperties)Metoda zwraca właściwości kolejki, w tym liczbę komunikatów. [`ApproximateMessagesCount`](/dotnet/api/azure.storage.queues.models.queueproperties.approximatemessagescount)Właściwość zawiera przybliżoną liczbę komunikatów w kolejce. Ta liczba nie jest niższa niż rzeczywista liczba komunikatów w kolejce, ale może być wyższa.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_GetQueueLength":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnetv11)

Możesz uzyskać szacunkową liczbę komunikatów w kolejce. [`FetchAttributes`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.fetchattributes?view=azure-dotnet-legacy&preserve-view=true)Metoda zwraca atrybuty kolejki, w tym liczbę komunikatów. [`ApproximateMessageCount`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.approximatemessagecount?view=azure-dotnet-legacy&preserve-view=true)Właściwość zwraca ostatnią wartość pobraną przez `FetchAttributes` metodę bez wywoływania queue storage.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Fetch the queue attributes.
queue.FetchAttributes();

// Retrieve the cached approximate message count.
int cachedMessageCount = queue.ApproximateMessageCount;

// Display number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

---

## <a name="delete-a-queue"></a>Usuwanie kolejki

# <a name="net-v12"></a>[V12 .NET](#tab/dotnet)

Aby usunąć kolejkę i wszystkie znajdujące się w niej komunikaty, wywołaj [`Delete`](/dotnet/api/azure.storage.queues.queueclient.delete) metodę w obiekcie Queue.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DeleteQueue":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnetv11)

Aby usunąć kolejkę i wszystkie znajdujące się w niej komunikaty, wywołaj [`Delete`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.delete?view=azure-dotnet-legacy&preserve-view=true) metodę w obiekcie Queue.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Delete the queue.
queue.Delete();
```

---

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz już podstawy Queue Storage, Skorzystaj z poniższych linków, aby dowiedzieć się więcej o bardziej skomplikowanych zadaniach magazynu.

- Zobacz dokumentację referencyjną Queue Storage, aby uzyskać szczegółowe informacje o dostępnych interfejsach API:
  - [Dokumentacja biblioteki klienta usługi Azure Storage dla platformy .NET](/dotnet/api/overview/azure/storage)
  - [Dokumentacja interfejsu API REST usługi Azure Storage](/rest/api/storageservices/)
- Wyświetl więcej poradników dotyczących funkcji, aby dowiedzieć się więcej o dodatkowych opcjach przechowywania danych na platformie Azure.
  - [Rozpocznij pracę z usługą Azure Table Storage przy użyciu platformy .NET](../../cosmos-db/tutorial-develop-table-dotnet.md) do przechowywania danych strukturalnych.
  - [Rozpocznij pracę z usługą Azure Blob Storage przy użyciu platformy .NET](../blobs/storage-quickstart-blobs-dotnet.md) do przechowywania danych bez struktury.
  - [Połącz się z usługą SQL Database przy użyciu platformy .NET (C#)](../../azure-sql/database/connect-query-dotnet-core.md), aby zapisać dane relacyjne.
- Dowiedz się, jak uprościć zapisywany kod, aby pracować z usługą Azure Storage za pomocą zestawu [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).
