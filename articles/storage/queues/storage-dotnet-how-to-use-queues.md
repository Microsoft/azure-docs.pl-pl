---
title: Rozpoczynanie pracy z usługą Azure queue storage przy użyciu platformy .NET — Azure Storage
description: Usługa Azure Queues zapewnia niezawodne, asynchroniczne przesyłanie komunikatów między składnikami aplikacji. Przesyłanie komunikatów za pomocą chmury umożliwia składnikom aplikacji niezależne skalowanie.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/21/2019
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: 0806c1101c0bc93a1b917cb2d18709721ff0c6d6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75968293"
---
# <a name="get-started-with-azure-queue-storage-using-net"></a>Rozpoczynanie pracy z usługą Azure Queue Storage przy użyciu platformy .NET

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="overview"></a>Omówienie

Usługa Azure Queue Storage umożliwia przesyłanie komunikatów za pomocą chmury między składnikami aplikacji. W przypadku projektowania aplikacji pod kątem skalowania składniki aplikacji są często rozłączane, dzięki czemu mogą być skalowane niezależnie. Usługa Queue Storage zapewnia asynchroniczne przesyłanie komunikatów na potrzeby komunikacji między składnikami aplikacji niezależnie od tego, czy działają w chmurze, na komputerze, serwerze lokalnym czy urządzeniu przenośnym. Magazyn kolejek obsługuje również zarządzanie asynchronicznymi zadaniami oraz przepływy pracy procesu kompilacji.

### <a name="about-this-tutorial"></a>Informacje o tym samouczku

W tym samouczku pokazano, jak napisać kod .NET dla niektórych typowych scenariuszy przy użyciu usługi Azure Queue Storage. Omówione scenariusze obejmują tworzenie i usuwanie kolejek oraz dodawanie, odczytywanie i usuwanie komunikatów kolejek.

**Szacowany czas trwania:** 45 minut

### <a name="prerequisites"></a>Wymagania wstępne

* [Program Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Wspólna Biblioteka klienta usługi Azure Storage dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)
* [Biblioteka kliencka kolejki usługi Azure Storage dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/)
* [Menedżer konfiguracji Azure dla programu .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)
* [Konto usługi Azure Storage](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="set-up-your-development-environment"></a>Konfigurowanie środowiska projektowego

Następnie skonfiguruj środowisko projektowe w programie Visual Studio, aby przygotować się do wypróbowania przykładów kodu zawartych w tym przewodniku.

### <a name="create-a-windows-console-application-project"></a>Utwórz projekt aplikacji konsolowej dla systemu Windows

W programie Visual Studio utwórz nową aplikację konsoli dla systemu Windows. Poniższe kroki pokazują, jak utworzyć aplikację konsolową w programie Visual Studio 2019. Procedura jest podobna w innych wersjach programu Visual Studio.

1. Wybierz pozycję **plik** > **Nowy** > **projekt**
2. Wybierz **Platform** > **okna** platformy
3. Wybierz pozycję **Aplikacja konsoli (.NET Framework)**
4. Wybierz pozycję **dalej**
5. W polu **Nazwa projektu** wprowadź nazwę aplikacji
6. Wybierz pozycję **Utwórz**

Wszystkie przykłady kodu w tym samouczku można dodać do metody **Main ()** pliku **program.cs** aplikacji konsolowej.

Możesz użyć bibliotek klienckich usługi Azure Storage w dowolnym typie aplikacji platformy .NET, w tym usługi w chmurze platformy Azure lub aplikacji sieci Web oraz aplikacji klasycznych i mobilnych. W tym przewodniku dla uproszczenia przedstawiono aplikację konsolową.

### <a name="use-nuget-to-install-the-required-packages"></a>Użycie pakietu NuGet w celu zainstalowania wymaganych pakietów

Aby ukończyć ten samouczek, należy odwołać się do następujących trzech pakietów w projekcie:

* [Microsoft Azure Storage wspólną bibliotekę kliencką dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/): Ten pakiet zapewnia programistyczny dostęp do zasobów danych na koncie magazynu.
* [Microsoft Azure Storage biblioteki kolejki dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/): Ta biblioteka klienta umożliwia pracę z Microsoft Azure Storage usługa kolejki do przechowywania komunikatów, do których dostęp jest możliwy dla klienta.
* [Biblioteka programu Microsoft Azure Configuration Manager dla środowiska .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/): ten pakiet zawiera klasę do analizowania parametrów połączenia w pliku konfiguracji, niezależnie od tego, gdzie została uruchomiona aplikacja.

Aby uzyskać te pakiety, można użyć narzędzia NuGet. Wykonaj następujące kroki:

1. Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań**i wybierz polecenie **Zarządzaj pakietami NuGet**.
2. Wybierz pozycję **Przeglądaj**
3. Wyszukaj w trybie online pozycję "Microsoft. Azure. Storage. queue" i wybierz pozycję **Zainstaluj** , aby zainstalować bibliotekę klienta magazynu i jej zależności. Spowoduje to również zainstalowanie biblioteki Microsoft. Azure. Storage. Common, która jest zależna od biblioteki kolejki.
4. Wyszukaj w trybie online pozycję "Microsoft. Azure. ConfigurationManager" i wybierz pozycję **Zainstaluj** , aby zainstalować Configuration Manager platformy Azure.

> [!NOTE]
> Pakiety bibliotek klienta magazynu są również dołączone do [zestawu Azure SDK dla platformy .NET](https://azure.microsoft.com/downloads/). Zaleca się jednak również zainstalowanie bibliotek klienta usługi Storage z programu NuGet, aby upewnić się, że zawsze masz najnowsze wersje.
>
> Zależności ODataLib w bibliotekach klienta usługi Storage dla platformy .NET są rozwiązywane przez pakiety ODataLib dostępne w pakiecie NuGet, a nie z Usługi danych programu WCF. Biblioteki ODataLib można pobrać bezpośrednio lub użyć odwołań w projekcie kodu za pośrednictwem pakietu NuGet. Określone pakiety ODataLib używane przez biblioteki klienta magazynu to [OData](https://nuget.org/packages/Microsoft.Data.OData/), [EDM](https://nuget.org/packages/Microsoft.Data.Edm/)i [przestrzenne](https://nuget.org/packages/System.Spatial/). Chociaż te biblioteki są używane przez klasy magazynu tabel platformy Azure, są one wymagane do programowania w bibliotekach klienta magazynu.

### <a name="determine-your-target-environment"></a>Określanie środowiska docelowego

W przypadku uruchamiania przykładów w tym przewodniku istnieją dwie opcje środowiska:

* Można uruchomić kod dla konta usługi Azure Storage w chmurze.
* Można uruchomić kod dla emulatora usługi Azure Storage. Emulator magazynu jest lokalnym środowiskiem, które emuluje konto usługi Azure Storage w chmurze. Emulator jest bezpłatną opcją do testowania i debugowania kodu, gdy aplikacja jest w fazie projektowania. Emulator używa dobrze znanego konta i klucza. Aby uzyskać więcej informacji, zobacz [Używanie emulatora usługi Azure Storage do programowania i testowania](../common/storage-use-emulator.md).

Jeśli obiektem docelowym jest konto magazynu w chmurze, skopiuj podstawowy klucz dostępu dla konta magazynu z witryny Azure Portal. Aby uzyskać więcej informacji, zobacz [Zarządzanie kluczami dostępu do konta magazynu](../common/storage-account-keys-manage.md).

> [!NOTE]
> Na obiekt docelowy można wybrać emulator magazynu, aby uniknąć ponoszenia kosztów związanych z usługą Azure Storage. Jednak jeśli wybranym obiektem docelowym będzie konto usługi Azure Storage w chmurze, koszty związane z wykonaniem instrukcji w tym samouczku będą niewielkie.

### <a name="configure-your-storage-connection-string"></a>Konfigurowanie parametrów połączenia magazynu

Biblioteki klienta usługi Azure Storage dla platformy .NET obsługują używanie parametrów połączenia magazynu w celu skonfigurowania punktów końcowych i poświadczeń w celu uzyskania dostępu do usług magazynu. Najlepiej przechowywać parametry połączenia magazynu w pliku konfiguracji.

Aby uzyskać więcej informacji dotyczących parametrów połączenia, zobacz [Konfigurowanie parametrów połączenia z usługą Azure Storage](../common/storage-configure-connection-string.md).

> [!NOTE]
> Klucz konta magazynu jest podobny do hasła głównego konta magazynu. Zawsze chroń klucz konta magazynu. Nie udostępniaj go innym użytkownikom, nie koduj go trwale ani nie zapisuj w zwykłym pliku tekstowym, do którego mają dostęp inne osoby. Wygeneruj ponownie klucz za pośrednictwem witryny Azure Portal, jeśli uważasz, że jego zabezpieczenia mogły zostać naruszone.

Aby skonfigurować parametry połączenia, Otwórz plik **App. config** z Eksplorator rozwiązań w programie Visual Studio. Dodaj zawartość elementu ** \<AppSettings\> ** pokazanego poniżej. Zastąp wartość *nazwa konta* nazwą konta magazynu i *klucz konta* kluczem dostępu do konta:

```xml
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
    </appSettings>
</configuration>
```

Na przykład ustawienie konfiguracji może wyglądać mniej więcej tak:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==" />
```

Jeśli obiektem docelowym ma być emulator magazynu, możesz użyć skrótu klawiaturowego, który mapuje dobrze znaną nazwę konta i klucz. W takim przypadku ustawienie parametrów połączenia wygląda następująco:

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true;" />
```

### <a name="add-using-directives"></a>Dodawanie dyrektyw using

Dodaj następujące dyrektywy `using` na początku pliku `Program.cs`:

```csharp
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage; // Namespace for CloudStorageAccount
using Microsoft.Azure.Storage.Queue; // Namespace for Queue storage types
```

### <a name="copy-your-credentials-from-the-azure-portal"></a>Kopiowanie poświadczeń z witryny Azure Portal

Przykładowy kod musi autoryzować dostęp do konta magazynu. Na potrzeby autoryzacji podajesz aplikacji swoje poświadczenia konta magazynu w postaci parametrów połączenia. Aby wyświetlić swoje poświadczenia konta magazynu:

1. Przejdź do [Azure Portal](https://portal.azure.com).
2. Odszukaj konto magazynu.
3. W sekcji **Ustawienia** omówienia kont magazynu wybierz pozycję **Klucze dostępu**. Zostaną wyświetlone klucze dostępu do Twojego konta, a także pełne parametry połączenia dla każdego klucza.
4. Znajdź wartość **Parametry połączenia** w obszarze **key1**i kliknij przycisk **Kopiuj**, aby skopiować parametry połączenia. W następnym kroku dodasz wartość parametrów połączenia do zmiennej środowiskowej.

    ![Zrzut ekranu pokazujący sposób kopiowania parametrów połączenia z witryny Azure Portal](media/storage-dotnet-how-to-use-queues/portal-connection-string.png)

### <a name="parse-the-connection-string"></a>Analizowanie parametrów połączenia

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-queue-service-client"></a>Tworzenie klienta usługi kolejki

Klasa [CloudQueueClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueueclient?view=azure-dotnet) umożliwia pobieranie kolejek przechowywanych w usłudze Queue Storage. Oto jeden ze sposobów tworzenia klienta usługi:

```csharp
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
```

Teraz możesz przystąpić do pisania kodu, który będzie odczytywać dane z usługi Queue Storage i zapisywać je w nim.

## <a name="create-a-queue"></a>Tworzenie kolejki

W tym przykładzie pokazano, jak utworzyć kolejkę, jeśli jeszcze nie istnieje:

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a container.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();
```

## <a name="insert-a-message-into-a-queue"></a>Wstawianie komunikatu do kolejki

Aby wstawić komunikat do istniejącej kolejki, najpierw utwórz nową klasę [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage?view=azure-dotnet). Następnie wywołaj metodę [AddMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessage?view=azure-dotnet). Klasę **CloudQueueMessage** można utworzyć przy użyciu ciągu (w formacie UTF-8) lub tablicy **bajtów**. Oto kod, który tworzy kolejkę (jeśli kolejka nie istnieje) i wstawia komunikat „Hello, World”:

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist.
queue.CreateIfNotExists();

// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
queue.AddMessage(message);
```

## <a name="peek-at-the-next-message"></a>Podgląd kolejnego komunikatu

Możesz uzyskać wgląd w komunikat z przodu kolejki bez jego usuwania z kolejki, wywołując metodę [PeekMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.peekmessage?view=azure-dotnet).

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

## <a name="change-the-contents-of-a-queued-message"></a>Zmiana zawartości komunikatu w kolejce

Możesz zmienić zawartość komunikatu w kolejce. Jeśli komunikat reprezentuje zadanie robocze, możesz użyć tej funkcji, aby zaktualizować stan zadania. Poniższy kod aktualizuje komunikat kolejki o nową zawartość i ustawia rozszerzenie limitu czasu widoczności o kolejne 60 sekund. Operacja ta zapisuje stan pracy powiązanej z komunikatem i daje klientowi kolejną minutę na kontynuowanie pracy nad komunikatem. Możesz użyć tej metody do śledzenia wieloetapowych przepływów pracy związanych z komunikatami kolejek, bez konieczności rozpoczynania od nowa, gdy dany etap nie powiedzie się ze względu na awarię sprzętu lub oprogramowania. Zazwyczaj stosuje się również liczbę ponownych prób. Jeśli komunikat zostanie ponowiony więcej niż *n* razy, zostanie usunięty. Jest to zabezpieczenie przed komunikatami, które wyzwalają błąd aplikacji zawsze, gdy są przetwarzane.

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

## <a name="de-queue-the-next-message"></a>Usunięcie następnego komunikatu z kolejki

Twój kod usuwa komunikat z kolejki w dwóch etapach. Jeśli wywołasz funkcję [GetMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage?view=azure-dotnet), uzyskasz następny komunikat w kolejce. Komunikat zwrócony z funkcji **GetMessage** staje się niewidoczny dla innego kodu odczytującego komunikaty z tej kolejki. Domyślnie komunikat pozostanie niewidoczny przez 30 sekund. Aby zakończyć usuwanie komunikatu z kolejki, musisz również wywołać funkcję [DeleteMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage?view=azure-dotnet). Ten dwuetapowy proces usuwania komunikatów gwarantuje, że jeśli kod nie będzie w stanie przetworzyć komunikatu z powodu awarii sprzętu lub oprogramowania, inne wystąpienie kodu będzie w stanie uzyskać ten sam komunikat i ponowić próbę. Twój kod wywołuje funkcję **DeleteMessage** natychmiast po przetworzeniu komunikatu.

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

## <a name="use-async-await-pattern-with-common-queue-storage-apis"></a>Używanie wzorca Async-Await z wspólnymi interfejsami API usługi Queue Storage

Ten przykład przedstawia sposób użycia wzorca Async-Await z wykorzystaniem wspólnych interfejsów API usługi Queue Storage. Przykład wywołuje asynchroniczną wersję każdej z danych metod, co jest wskazane przez sufiks *Async* każdej metody. Jeśli zostanie użyta metoda asynchroniczna, wzorzec Async-Await zawiesi lokalne wykonanie do momentu ukończenia wywołania. Takie zachowanie umożliwia wykonywanie innych zadań przez bieżący wątek, co pomaga unikać wąskich gardeł zmniejszających wydajność i poprawia ogólną szybkość reakcji aplikacji. Aby uzyskać szczegółowe informacje o wykorzystaniu wzorca Async-Await w programie .NET, zobacz [Async and Await (C# and Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx) (Async i Await [C# i Visual Basic]).

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

## <a name="leverage-additional-options-for-de-queuing-messages"></a>Wykorzystanie dodatkowych opcji do usuwania komunikatów z kolejek

Istnieją dwa sposoby dostosowania pobierania komunikatów z kolejki. Po pierwsze można uzyskać komunikaty zbiorczo (do 32). Po drugie można ustawić dłuższy lub krótszy limit czasu niewidoczności, dzięki czemu kod będzie mieć więcej lub mniej czasu na pełne przetworzenie każdego komunikatu. Poniższy przykład kodu wykorzystuje metodę [GetMessages](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessages?view=azure-dotnet), aby pobrać 20 komunikatów w jednym wywołaniu. Następnie przetwarza każdy komunikat przy użyciu pętli **foreach**. Ustawia również limitu czasu niewidoczności na pięć minut dla każdego komunikatu. Należy zauważyć, że okres 5 minut rozpoczyna się dla wszystkich komunikatów w tym samym czasie, więc po upływie 5 minut od wywołania metody **GetMessages** wszystkie komunikaty, które nie zostały usunięte, będą widoczne ponownie.

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

## <a name="get-the-queue-length"></a>Pobieranie długości kolejki

Możesz uzyskać szacunkową liczbę komunikatów w kolejce. Metoda [FetchAttributes](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.fetchattributes?view=azure-dotnet) prosi usługę kolejki o pobranie atrybutów kolejki, w tym liczby komunikatów. Właściwość [ApproximateMessageCount](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.approximatemessagecount?view=azure-dotnet) zwraca ostatnią wartość pobraną przez metodę **FetchAttributes** bez wywoływania usługi kolejki.

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
int? cachedMessageCount = queue.ApproximateMessageCount;

// Display number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="delete-a-queue"></a>Usuwanie kolejki

Aby usunąć kolejkę i wszystkie zawarte w niej komunikaty, wywołaj metodę [Delete](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.delete?view=azure-dotnet) na obiekcie kolejki.

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

## <a name="next-steps"></a>Następne kroki

Teraz, kiedy znasz już podstawy usługi Queue Storage, skorzystaj z poniższych linków, aby dowiedzieć się więcej o bardziej skomplikowanych zadaniach magazynu.

* Przejrzyj dokumentację referencyjną usługi kolejki, aby uzyskać szczegółowe informacje o dostępnych interfejsach API:
  * [Dokumentacja biblioteki klienta usługi Storage dla platformy .NET](https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
  * [Dokumentacja interfejsu API REST](https://msdn.microsoft.com/library/azure/dd179355)
* Dowiedz się, jak uprościć zapisywany kod, aby pracować z usługą Azure Storage za pomocą zestawu [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).
* Wyświetl więcej poradników dotyczących funkcji, aby dowiedzieć się więcej o dodatkowych opcjach przechowywania danych na platformie Azure.
  * Zapoznaj się z tematem [Rozpoczynanie pracy z usługą Azure Table Storage przy użyciu platformy .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md), aby przechowywać dane strukturalne.
  * Zapoznaj się z tematem [Rozpoczynanie pracy z usługą Azure Blob Storage przy użyciu platformy .NET](../blobs/storage-dotnet-how-to-use-blobs.md), aby przechowywać dane bez struktury.
  * [Połącz się z usługą SQL Database przy użyciu platformy .NET (C#)](../../sql-database/sql-database-connect-query-dotnet-core.md), aby zapisać dane relacyjne.

[Download and install the Azure SDK for .NET]: /develop/net/
[.NET client library reference]: https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
[Creating an Azure Project in Visual Studio]: https://msdn.microsoft.com/library/azure/ee405487.aspx
[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[OData]: https://nuget.org/packages/Microsoft.Data.OData/5.0.2
[Edm]: https://nuget.org/packages/Microsoft.Data.Edm/5.0.2
[Spatial]: https://nuget.org/packages/System.Spatial/5.0.2
