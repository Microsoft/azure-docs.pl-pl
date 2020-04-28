---
title: Wprowadzenie do usługi queue storage przy użyciu programu Visual Studio (projekty WebJob)
description: Jak rozpocząć pracę z usługą Azure queue storage w projekcie Zadania WebJob po nawiązaniu połączenia z kontem magazynu przy użyciu usług połączonych programu Visual Studio.
services: storage
author: ghogen
manager: jillfra
ms.assetid: 5c3ef267-2a67-44e9-ab4a-1edd7015034f
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: article
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ffba203bafaf3837cd2d7fc1a6fd962a6926b186
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "72298748"
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-webjob-projects"></a>Rozpoczynanie pracy z usługą Azure queue storage i usługami połączonymi programu Visual Studio (projekty zadań WebJob)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Omówienie
W tym artykule opisano sposób rozpoczynania pracy z usługą Azure queue storage w projekcie WebJob programu Visual Studio Azure po utworzeniu lub przywoływaniu konta usługi Azure Storage za pomocą okna dialogowego **Dodawanie usług połączonych** programu Visual Studio. Po dodaniu konta magazynu do projektu zadania WebJob przy użyciu okna dialogowego **Dodaj połączone usługi** programu Visual Studio zostaną zainstalowane odpowiednie pakiety NuGet magazynu platformy Azure, odpowiednie odwołania platformy .NET zostaną dodane do projektu, a parametry połączenia dla konta magazynu są aktualizowane w pliku App. config.  

W tym artykule przedstawiono przykłady kodu w języku C#, które pokazują, jak używać zestawu SDK Azure WebJobs w wersji 1. x z usługą Azure queue storage.

Azure Queue Storage to usługa do przechowywania dużej liczby komunikatów, do której można uzyskać dostęp z dowolnego miejsca na świecie za pośrednictwem uwierzytelnionego połączenia za pomocą protokołu HTTP lub HTTPS. Pojedynczy komunikat z kolejki nie może przekraczać 64 KB, a kolejka może zawierać miliony komunikatów — maksymalnie liczbę nieprzekraczającą całkowitego limitu pojemności konta magazynu. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z usługą Azure queue storage przy użyciu platformy .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) . Aby uzyskać więcej informacji na temat ASP.NET, zobacz [ASP.NET](https://www.asp.net).

## <a name="how-to-trigger-a-function-when-a-queue-message-is-received"></a>Jak wyzwolić funkcję po odebraniu komunikatu w kolejce
Aby napisać funkcję, którą zestaw SDK zadań WebJob wywołuje po odebraniu komunikatu kolejki, należy użyć atrybutu **QueueTrigger** . Konstruktor atrybutu przyjmuje parametr ciągu, który określa nazwę kolejki do sondowania. Aby dowiedzieć się, jak ustawić nazwę kolejki dynamicznie, zapoznaj [się z tematem Ustawianie opcji konfiguracji](#how-to-set-configuration-options).

### <a name="string-queue-messages"></a>Komunikaty w kolejce ciągów
W poniższym przykładzie Kolejka zawiera komunikat ciągu, więc **QueueTrigger** jest stosowana do parametru ciągu o nazwie **LogMessage** , który zawiera zawartość komunikatu w kolejce. Funkcja [zapisuje komunikat dziennika na pulpicie nawigacyjnym](#how-to-write-logs).

```csharp
public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
{
    logger.WriteLine(logMessage);
}
```

Oprócz **ciągu**, parametr może być tablicą bajtową, obiektem **CloudQueueMessage** lub zdefiniowanym poco.

### <a name="poco-plain-old-clr-object-queue-messages"></a>POCO [(zwykły stary obiekt CLR](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) komunikaty kolejki
W poniższym przykładzie komunikat kolejki zawiera kod JSON dla obiektu **BlobInformation** , który zawiera właściwość **blobname** . Zestaw SDK automatycznie deserializacji obiektu.

```csharp
public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
{
    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
}
```

Zestaw SDK używa [pakietu NuGet Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json) do serializacji i deserializacji komunikatów. W przypadku tworzenia komunikatów w kolejce w programie, który nie korzysta z zestawu SDK WebJobs, można napisać kod, taki jak Poniższy przykład, aby utworzyć komunikat w kolejce POCO, który może być analizowany przez zestaw SDK.

```csharp
BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
logQueue.AddMessage(queueMessage);
```

### <a name="async-functions"></a>Funkcje asynchroniczne
Następująca funkcja Async [zapisuje dziennik na pulpicie nawigacyjnym](#how-to-write-logs).

```csharp
public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
{
    await logger.WriteLineAsync(logMessage);
}
```

Funkcje asynchroniczne mogą przyjmować [token anulowania](https://www.asp.net/mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4#CancelToken), jak pokazano w poniższym przykładzie, który kopiuje obiekt BLOB. (Aby uzyskać wyjaśnienie symbolu zastępczego **queueTrigger** , zobacz sekcję [obiektów BLOB](#how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message) ).

```csharp
public async static Task ProcessQueueMessageAsyncCancellationToken(
    [QueueTrigger("blobcopyqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
    CancellationToken token)
{
    await blobInput.CopyToAsync(blobOutput, 4096, token);
}
```

## <a name="types-the-queuetrigger-attribute-works-with"></a>Typy, które współdziałają z atrybutem QueueTrigger
Możesz użyć **QueueTrigger** z następującymi typami:

* **parametry**
* Typ POCO, który jest serializowany jako JSON
* **Byte []**
* **CloudQueueMessage**

## <a name="polling-algorithm"></a>Algorytm sondowania
Zestaw SDK implementuje losowy wykładniczy algorytm wycofywania, aby zmniejszyć wpływ sondowania w kolejce na koszty transakcji magazynu.  Po znalezieniu komunikatu zestaw SDK czeka dwa sekundy, a następnie sprawdza inny komunikat; gdy nie zostanie znaleziony żaden komunikat, czeka około czterech sekund przed ponowieniem próby. Po kolejnych nieudanych próbach pobrania komunikatu w kolejce czas oczekiwania będzie się zwiększać do momentu osiągnięcia maksymalnego czasu oczekiwania, który jest wartością domyślną jednej minuty. [Maksymalny czas oczekiwania można skonfigurować](#how-to-set-configuration-options).

## <a name="multiple-instances"></a>Wiele wystąpień
Jeśli aplikacja sieci Web działa w wielu wystąpieniach, ciągłe zadania WebJob są uruchamiane na każdej maszynie, a każda maszyna będzie oczekiwać na wyzwalacze i próbuje uruchomić funkcje. W niektórych scenariuszach może to spowodować, że niektóre funkcje przetwarzają te same dane dwa razy, dlatego funkcje powinny być idempotentne (zapisane w taki sposób, aby wielokrotnie wywołujący te same dane wejściowe nie generowały zduplikowanych wyników).  

## <a name="parallel-execution"></a>Wykonywanie równoległe
Jeśli masz wiele funkcji nasłuchujących w różnych kolejkach, zestaw SDK wywoła je równolegle, gdy komunikaty są odbierane jednocześnie.

Taka sama ma wartość true, Jeśli odebrano wiele komunikatów dla jednej kolejki. Domyślnie zestaw SDK pobiera partię 16 komunikatów z kolejki jednocześnie i wykonuje funkcję, która przetwarza je równolegle. [Rozmiar wsadu można skonfigurować](#how-to-set-configuration-options). Gdy liczba jest przetwarzana do połowy rozmiaru partii, zestaw SDK pobiera kolejną partię i uruchamia przetwarzanie tych komunikatów. W związku z tym Maksymalna liczba współbieżnych komunikatów przetwarzanych na funkcję to jedna i połowa rozmiaru partii. Ten limit dotyczy osobno każdej funkcji, która ma atrybut **QueueTrigger** . Jeśli nie chcesz, aby wykonywanie równoległe komunikatów odebranych w jednej kolejce było możliwe, ustaw rozmiar wsadu na 1.

## <a name="get-queue-or-queue-message-metadata"></a>Pobieranie metadanych kolejki lub komunikatów w kolejce
Następujące właściwości komunikatu można uzyskać, dodając parametry do sygnatury metody:

* ExpirationTime **DateTimeOffset**
* InsertionTime **DateTimeOffset**
* NextVisibleTime **DateTimeOffset**
* **ciąg** queueTrigger (zawiera tekst komunikatu)
* Identyfikator **ciągu**
* popReceipt **ciągu**
* **int** dequeueCount

Jeśli chcesz współpracować bezpośrednio z interfejsem API usługi Azure Storage, możesz również dodać parametr **CloudStorageAccount** .

Poniższy przykład zapisuje wszystkie te metadane do dziennika aplikacji INFORMACYJNej. W przykładzie zarówno logMessage, jak i queueTrigger zawierają zawartość komunikatu w kolejce.

```csharp
public static void WriteLog([QueueTrigger("logqueue")] string logMessage,
    DateTimeOffset expirationTime,
    DateTimeOffset insertionTime,
    DateTimeOffset nextVisibleTime,
    string id,
    string popReceipt,
    int dequeueCount,
    string queueTrigger,
    CloudStorageAccount cloudStorageAccount,
    TextWriter logger)
{
    logger.WriteLine(
        "logMessage={0}\n" +
        "expirationTime={1}\ninsertionTime={2}\n" +
        "nextVisibleTime={3}\n" +
        "id={4}\npopReceipt={5}\ndequeueCount={6}\n" +
        "queue endpoint={7} queueTrigger={8}",
        logMessage, expirationTime,
        insertionTime,
        nextVisibleTime, id,
        popReceipt, dequeueCount,
        cloudStorageAccount.QueueEndpoint,
        queueTrigger);
}
```

Oto przykładowy dziennik zapisany przez przykładowy kod:

        logMessage=Hello world!
        expirationTime=10/14/2014 10:31:04 PM +00:00
        insertionTime=10/7/2014 10:31:04 PM +00:00
        nextVisibleTime=10/7/2014 10:41:23 PM +00:00
        id=262e49cd-26d3-4303-ae88-33baf8796d91
        popReceipt=AgAAAAMAAAAAAAAAfc9H0n/izwE=
        dequeueCount=1
        queue endpoint=https://contosoads.queue.core.windows.net/
        queueTrigger=Hello world!

## <a name="graceful-shutdown"></a>Bezpieczne zamykanie
Funkcja, która jest uruchamiana w ciągłym zadaniach WebJob, może przyjmować parametr **CancellationToken** , który umożliwia systemowi operacyjnemu powiadomienie funkcji, gdy zadanie WebJob zostanie zakończone. Możesz użyć tego powiadomienia, aby upewnić się, że funkcja nie kończy się nieoczekiwanie w sposób, który opuszcza dane w stanie niespójnym.

Poniższy przykład pokazuje, jak sprawdzić oczekujące zakończenie zadania WebJob w funkcji.

```csharp
public static void GracefulShutdownDemo(
            [QueueTrigger("inputqueue")] string inputText,
            TextWriter logger,
            CancellationToken token)
{
    for (int i = 0; i < 100; i++)
    {
        if (token.IsCancellationRequested)
        {
            logger.WriteLine("Function was cancelled at iteration {0}", i);
            break;
        }
        Thread.Sleep(1000);
        logger.WriteLine("Normal processing for queue message={0}", inputText);
    }
}
```

**Uwaga:** Pulpit nawigacyjny może nie wyświetlać poprawnie stanu i danych wyjściowych funkcji, które zostały zamknięte.

Aby uzyskać więcej informacji, zobacz temat [WebJobs bezpiecznie Shutdown](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR).   

## <a name="how-to-create-a-queue-message-while-processing-a-queue-message"></a>Jak utworzyć komunikat w kolejce podczas przetwarzania komunikatu w kolejce
Aby napisać funkcję, która tworzy nowy komunikat w kolejce, Użyj atrybutu **Queue** . Podobnie jak **QueueTrigger**, należy przekazać nazwę kolejki jako ciąg lub można [ustawić ją dynamicznie](#how-to-set-configuration-options).

### <a name="string-queue-messages"></a>Komunikaty w kolejce ciągów
Następujący przykładowy kod nieasynchroniczny tworzy nowy komunikat kolejki w kolejce o nazwie "operacja" z tą samą zawartością, co komunikat kolejki odebrany w kolejce o nazwie "InputQueue". (W przypadku funkcji asynchronicznych Użyj **\<IAsyncCollector T>** , jak pokazano w dalszej części tej sekcji).

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    [Queue("outputqueue")] out string outputQueueMessage )
{
    outputQueueMessage = queueMessage;
}
```

### <a name="poco-plain-old-clr-object-queue-messages"></a>POCO [(zwykły stary obiekt CLR](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) komunikaty kolejki
Aby utworzyć komunikat kolejki, który zawiera POCO, a nie ciąg, Przekaż typ POCO jako parametr wyjściowy do konstruktora atrybutu **kolejki** .

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
    [Queue("outputqueue")] out BlobInformation blobInfoOutput )
{
    blobInfoOutput = blobInfoInput;
}
```

Zestaw SDK automatycznie serializować obiekt do formatu JSON. Komunikat kolejki jest zawsze tworzony, nawet jeśli obiekt ma wartość null.

### <a name="create-multiple-messages-or-in-async-functions"></a>Tworzenie wielu komunikatów lub w funkcjach asynchronicznych
Aby utworzyć wiele komunikatów, należy utworzyć typ parametru dla kolejki wyjściowej **ICollector\<t>** lub **IAsyncCollector\<t>**, jak pokazano w poniższym przykładzie.

```csharp
public static void CreateQueueMessages(
    [QueueTrigger("inputqueue")] string queueMessage,
    [Queue("outputqueue")] ICollector<string> outputQueueMessage,
    TextWriter logger)
{
    logger.WriteLine("Creating 2 messages in outputqueue");
    outputQueueMessage.Add(queueMessage + "1");
    outputQueueMessage.Add(queueMessage + "2");
}
```

Każda wiadomość w kolejce jest tworzona natychmiast po wywołaniu metody **Add** .

### <a name="types-that-the-queue-attribute-works-with"></a>Typy, z którymi działa atrybut kolejki
Można użyć atrybutu **Queue** dla następujących typów parametrów:

* **ciąg out** (tworzy komunikat w kolejce, jeśli wartość parametru jest inna niż null, gdy funkcja zostanie zakończona)
* **out-Byte []** (działa jak **ciąg**)
* **CloudQueueMessage out** (działa jak **ciąg**)
* **poco out** (typ możliwy do serializacji, tworzy komunikat z obiektem o wartości null, jeśli parametr ma wartość null, gdy funkcja zostanie zakończona)
* **ICollector**
* **IAsyncCollector**
* **CloudQueue** (do ręcznego tworzenia komunikatów przy użyciu interfejsu API usługi Azure Storage)

### <a name="use-webjobs-sdk-attributes-in-the-body-of-a-function"></a>Używanie atrybutów zestawu SDK zadań WebJob w treści funkcji
Jeśli musisz wykonać pewne czynności w funkcji przed użyciem atrybutu zestawu SDK usługi WebJobs, takiego jak **Queue**, **BLOB**lub **Table**, możesz użyć interfejsu **IBinder** .

Poniższy przykład przyjmuje komunikat w kolejce wejściowej i tworzy nowy komunikat z tą samą zawartością w kolejce wyjściowej. Nazwa kolejki wyjściowej jest ustawiana przez kod w treści funkcji.

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    IBinder binder)
{
    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
    outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
}
```

Interfejs **IBinder** może być również używany z atrybutami **Table** i **BLOB** .

## <a name="how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message"></a>Odczytywanie i zapisywanie obiektów blob i tabel podczas przetwarzania komunikatu w kolejce
Atrybuty **obiektów BLOB** i **tabel** umożliwiają odczytywanie i zapisywanie obiektów blob i tabel. Przykłady w tej sekcji dotyczą obiektów BLOB. Przykłady kodu, które pokazują, jak wyzwolić procesy podczas tworzenia lub aktualizowania obiektów blob, można znaleźć w temacie [jak używać usługi Azure Blob Storage z zestawem SDK WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki).
<!-- , and for code samples that read and write tables, see [How to use Azure table storage with the WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk-storage-tables-how-to.md). -->

### <a name="string-queue-messages-triggering-blob-operations"></a>Komunikaty kolejki ciągów wyzwalające operacje obiektu BLOB
W przypadku komunikatu w kolejce, który zawiera ciąg, **queueTrigger** jest symbolem zastępczym, którego można użyć w parametrze **Blobpath ścieżką** atrybutu **obiektu BLOB** , który zawiera treść komunikatu.

W poniższym przykładzie za pomocą obiektów **strumienia** można odczytywać i zapisywać obiekty blob. Komunikat kolejki to nazwa obiektu BLOB znajdującego się w kontenerze textblobs. Kopia obiektu BLOB z "-New" dołączona do nazwy jest tworzona w tym samym kontenerze.

```csharp
public static void ProcessQueueMessage(
    [QueueTrigger("blobcopyqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}
```

Konstruktor atrybutu **obiektu BLOB** przyjmuje parametr **blobpath ścieżką** , który określa nazwę kontenera i obiektu BLOB. Aby uzyskać więcej informacji na temat tego symbolu zastępczego, zobacz [jak używać usługi Azure Blob Storage z zestawem SDK WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki).

Gdy atrybut zdobi obiekt **Stream** , inny parametr konstruktora określa tryb **FileAccess** jako Odczyt, zapis lub odczyt/zapis.

Poniższy przykład używa obiektu **CloudBlockBlob** , aby usunąć obiekt BLOB. Komunikat kolejki jest nazwą obiektu BLOB.

```csharp
public static void DeleteBlob(
    [QueueTrigger("deleteblobqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
{
    blobToDelete.Delete();
}
```

### <a name="poco-plain-old-clr-object-queue-messages"></a>POCO [(zwykły stary obiekt CLR](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) komunikaty kolejki
W przypadku elementu POCO przechowywanego jako dane JSON w komunikacie kolejki można użyć symboli zastępczych, które są właściwościami obiektu w parametrze **blobpath ścieżką** parametru **kolejki** . Nazwy właściwości metadanych kolejki można także użyć jako symboli zastępczych. Zobacz [Pobieranie metadanych kolejki lub komunikatów w kolejce](#get-queue-or-queue-message-metadata).

Poniższy przykład kopiuje obiekt BLOB do nowego obiektu BLOB przy użyciu innego rozszerzenia. Komunikat kolejki to obiekt **BlobInformation** , który zawiera właściwości **blobname** i **BlobNameWithoutExtension** . Nazwy właściwości są używane jako symbole zastępcze w ścieżce obiektu BLOB dla atrybutów **obiektu BLOB** .

```csharp
public static void CopyBlobPOCO(
    [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
    [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}
```

Zestaw SDK używa [pakietu NuGet Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json) do serializacji i deserializacji komunikatów. W przypadku tworzenia komunikatów w kolejce w programie, który nie korzysta z zestawu SDK WebJobs, można napisać kod, taki jak Poniższy przykład, aby utworzyć komunikat w kolejce POCO, który może być analizowany przez zestaw SDK.

```csharp
BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
logQueue.AddMessage(queueMessage);
```

Jeśli konieczne jest wykonanie pewnej pracy w funkcji przed powiązaniem obiektu BLOB z obiektem, można użyć atrybutu w treści funkcji, jak pokazano w temacie [Używanie atrybutów zestawu SDK zadań WebJob w treści funkcji](#use-webjobs-sdk-attributes-in-the-body-of-a-function).

### <a name="types-you-can-use-the-blob-attribute-with"></a>Typy, z których można korzystać przy użyciu atrybutu obiektu BLOB
Atrybut **obiektu BLOB** może być używany z następującymi typami:

* **Stream** (odczyt lub zapis, określony przy użyciu parametru konstruktora FileAccess)
* **TextReader**
* **TextWriter**
* **ciąg** (odczyt)
* **ciąg out** (Write; tworzy obiekt BLOB tylko wtedy, gdy parametr String ma wartość różną od null, gdy funkcja zwróci wartość)
* POCO (odczyt)
* POCO out (Write; zawsze tworzy obiekt BLOB, tworzy jako null Object, jeśli parametr POCO ma wartość null, gdy funkcja zwróci wartość)
* **CloudBlobStream** (zapis)
* **ICloudBlob** (odczyt lub zapis)
* **CloudBlockBlob** (odczyt lub zapis)
* **CloudPageBlob** (odczyt lub zapis)

## <a name="how-to-handle-poison-messages"></a>Jak obsługiwać skażone komunikaty
Komunikaty, których zawartość powoduje niepowodzenie funkcji, są nazywane *skażonymi komunikatami*. Gdy funkcja się nie powiedzie, komunikat kolejki nie zostanie usunięty i ostatecznie zostanie pobrany ponownie, co spowoduje powtarzanie cyklu. Zestaw SDK może automatycznie przerwać cykl po ograniczonej liczbie iteracji lub można to zrobić ręcznie.

### <a name="automatic-poison-message-handling"></a>Automatyczna obsługa skażonych komunikatów
Zestaw SDK będzie wywoływał funkcję do 5 razy w celu przetworzenia komunikatu w kolejce. Jeśli piąta próba nie powiedzie się, komunikat zostanie przeniesiony do kolejki trującej. Możesz zobaczyć, jak skonfigurować maksymalną liczbę ponownych prób w [sposobie ustawiania opcji konfiguracji](#how-to-set-configuration-options).

Kolejka Trująca nosi nazwę *{originalqueuename}*-trującą. Funkcję można napisać, aby przetwarzać komunikaty z kolejki trującej, rejestrując je lub wysyłając powiadomienie, które wymaga ręcznej uwagi.

W poniższym przykładzie funkcja **CopyBlob** zakończy się niepowodzeniem, gdy komunikat kolejki zawiera nazwę obiektu BLOB, który nie istnieje. Gdy tak się stanie, komunikat jest przenoszony z kolejki copyblobqueue do kolejki copyblobqueue-trującej. **ProcessPoisonMessage** następnie rejestruje trujący komunikat.

```csharp
public static void CopyBlob(
    [QueueTrigger("copyblobqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}

public static void ProcessPoisonMessage(
    [QueueTrigger("copyblobqueue-poison")] string blobName, TextWriter logger)
{
    logger.WriteLine("Failed to copy blob, name=" + blobName);
}
```

Na poniższej ilustracji przedstawiono dane wyjściowe konsoli z tych funkcji, gdy jest przetwarzany skażony komunikat.

![Dane wyjściowe konsoli dla obsługi skażonych komunikatów](./media/vs-storage-webjobs-getting-started-queues/poison.png)

### <a name="manual-poison-message-handling"></a>Obsługa ręcznej trującej wiadomości
Można uzyskać, ile razy wiadomość została pobrana do przetwarzania przez dodanie parametru **int** o nazwie **dequeueCount** do funkcji. Następnie można sprawdzić liczbę oddziałów w kodzie funkcji i wykonać własną trującą wiadomość, gdy liczba przekroczy wartość progową, jak pokazano w poniższym przykładzie.

```csharp
public static void CopyBlob(
    [QueueTrigger("copyblobqueue")] string blobName, int dequeueCount,
    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput,
    TextWriter logger)
{
    if (dequeueCount > 3)
    {
        logger.WriteLine("Failed to copy blob, name=" + blobName);
    }
    else
    {
        blobInput.CopyTo(blobOutput, 4096);
    }
}
```

## <a name="how-to-set-configuration-options"></a>Jak ustawić opcje konfiguracji
Możesz użyć typu **JobHostConfiguration** , aby ustawić następujące opcje konfiguracji:

* Ustaw parametry połączenia zestawu SDK w kodzie.
* Skonfiguruj ustawienia **QueueTrigger** , takie jak Maksymalna liczba Dequeue.
* Pobierz nazwy kolejek z konfiguracji.

### <a name="set-sdk-connection-strings-in-code"></a>Ustaw parametry połączenia zestawu SDK w kodzie
Ustawienie parametrów połączenia zestawu SDK w kodzie umożliwia korzystanie z własnych nazw parametrów połączenia w plikach konfiguracji lub zmiennych środowiskowych, jak pokazano w poniższym przykładzie.

```csharp
static void Main(string[] args)
{
    var _storageConn = ConfigurationManager
        .ConnectionStrings["MyStorageConnection"].ConnectionString;

    var _dashboardConn = ConfigurationManager
        .ConnectionStrings["MyDashboardConnection"].ConnectionString;

    var _serviceBusConn = ConfigurationManager
        .ConnectionStrings["MyServiceBusConnection"].ConnectionString;

    JobHostConfiguration config = new JobHostConfiguration();
    config.StorageConnectionString = _storageConn;
    config.DashboardConnectionString = _dashboardConn;
    config.ServiceBusConnectionString = _serviceBusConn;
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="configure-queuetrigger--settings"></a>Konfigurowanie ustawień QueueTrigger
Można skonfigurować następujące ustawienia, które są stosowane do przetwarzania komunikatów w kolejce:

* Maksymalna liczba komunikatów w kolejce, które są pobierane jednocześnie do wykonania równolegle (wartość domyślna to 16).
* Maksymalna liczba ponownych prób przed wysłaniem komunikatu kolejki do kolejki trującej (wartość domyślna to 5).
* Maksymalny czas oczekiwania przed ponownym sondowaniem, gdy kolejka jest pusta (wartość domyślna to 1 minuta).

Poniższy przykład pokazuje, jak skonfigurować następujące ustawienia:

```csharp
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.Queues.BatchSize = 8;
    config.Queues.MaxDequeueCount = 4;
    config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="set-values-for-webjobs-sdk-constructor-parameters-in-code"></a>Ustaw wartości parametrów konstruktora zestawu SDK usługi WebJobs w kodzie
Czasami chcesz określić nazwę kolejki, nazwę obiektu BLOB lub kontener albo nazwę tabeli w kodzie, a nie twardy kod. Na przykład możesz chcieć określić nazwę kolejki dla **QueueTrigger** w pliku konfiguracyjnym lub zmiennej środowiskowej.

Można to zrobić, przekazując obiekt **NameResolver** do typu **JobHostConfiguration** . Dołączasz specjalne symbole zastępcze otoczone procentowo (%) znaki konstruktora atrybutu zestawu SDK zadań WebJob i kod **NameResolver** określa rzeczywiste wartości używane zamiast tych symboli zastępczych.

Załóżmy na przykład, że chcesz użyć kolejki o nazwie logqueuetest w środowisku testowym i jednego o nazwie logqueueprod w produkcji. Zamiast zakodowanej nazwy kolejki, należy określić nazwę wpisu w kolekcji **AppSettings** , która ma rzeczywistą nazwę kolejki. Jeśli klucz **AppSettings** jest logqueue, funkcja może wyglądać podobnie jak w poniższym przykładzie.

```csharp
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Klasa **NameResolver** może następnie pobrać nazwę kolejki z **AppSettings** , jak pokazano w następującym przykładzie:

```csharp
public class QueueNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

Klasa **NameResolver** jest przekazywany do obiektu **JobHost** , jak pokazano w poniższym przykładzie.

```csharp
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new QueueNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

**Uwaga:** Nazwy kolejek, tabel i obiektów BLOB są rozpoznawane za każdym razem, gdy wywoływana jest funkcja, ale nazwy kontenerów obiektów BLOB są rozpoznawane tylko wtedy, gdy aplikacja jest uruchamiana. Nie można zmienić nazwy kontenera obiektów blob, gdy zadanie jest uruchomione.

## <a name="how-to-trigger-a-function-manually"></a>Jak ręcznie wyzwolić funkcję
Aby ręcznie wyzwolić funkcję, należy użyć metody **call** lub **CallAsync** w obiekcie **JobHost** i atrybucie **NoAutomaticTrigger** w funkcji, jak pokazano w poniższym przykładzie.

```csharp
public class Program
{
    static void Main(string[] args)
    {
        JobHost host = new JobHost();
        host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
    }

    [NoAutomaticTrigger]
    public static void CreateQueueMessage(
        TextWriter logger,
        string value,
        [Queue("outputqueue")] out string message)
    {
        message = value;
        logger.WriteLine("Creating queue message: ", message);
    }
}
```

## <a name="how-to-write-logs"></a>Jak pisać dzienniki
Pulpit nawigacyjny pokazuje dzienniki w dwóch miejscach: stronę Zadania WebJob i stronę dla określonego wywołania Zadania WebJob.

![Dzienniki na stronie Zadania WebJob](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

![Strona rejestrowania w wywołaniu funkcji](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

Dane wyjściowe z metod konsoli wywoływanych w funkcji lub w metodzie **Main ()** pojawiają się na stronie pulpitu nawigacyjnego Zadania WebJob, a nie na stronie dla określonego wywołania metody. Dane wyjściowe z obiektu TextWriter uzyskane z parametru w podpisie metody są wyświetlane na stronie pulpitu nawigacyjnego wywołania metody.

Dane wyjściowe konsoli nie mogą być połączone z konkretnym wywołaniem metody, ponieważ konsola jest jednowątkowa, podczas gdy wiele funkcji zadań może być uruchomionych w tym samym czasie. Dlatego zestaw SDK udostępnia każde wywołanie funkcji z własnym unikatowym obiektem modułu zapisywania dzienników.

Aby napisać [dzienniki śledzenia aplikacji](../app-service/troubleshoot-dotnet-visual-studio.md#logsoverview), użyj **konsoli. out** (tworzy dzienniki oznaczone jako info) i **Console. Error** (tworzy dzienniki oznaczone jako error). Alternatywą jest użycie [funkcji trace lub TraceSource](https://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx), która zapewnia pełne, ostrzegawcze i krytyczne poziomy oprócz informacji i błędów. Dzienniki śledzenia aplikacji są wyświetlane w plikach dziennika aplikacji sieci Web, w tabelach platformy Azure lub w obiektach Blob platformy Azure w zależności od sposobu konfigurowania aplikacji sieci Web platformy Azure. Podobnie jak w przypadku wszystkich danych wyjściowych konsoli, najnowsze Dzienniki aplikacji 100 są również wyświetlane na stronie pulpitu nawigacyjnego Zadania WebJob, a nie na stronie wywołania funkcji.

Dane wyjściowe konsoli są wyświetlane na pulpicie nawigacyjnym tylko wtedy, gdy program jest uruchomiony w zadaniach WebJob platformy Azure, a nie w przypadku uruchamiania lokalnego lub w innym środowisku.

Rejestrowanie można wyłączyć, ustawiając parametry połączenia pulpitu nawigacyjnego na wartość null. Aby uzyskać więcej informacji, zobacz [How to set Configuration Options](#how-to-set-configuration-options).

W poniższym przykładzie przedstawiono kilka sposobów pisania dzienników:

```csharp
public static void WriteLog(
    [QueueTrigger("logqueue")] string logMessage,
    TextWriter logger)
{
    Console.WriteLine("Console.Write - " + logMessage);
    Console.Out.WriteLine("Console.Out - " + logMessage);
    Console.Error.WriteLine("Console.Error - " + logMessage);
    logger.WriteLine("TextWriter - " + logMessage);
}
```

Na pulpicie nawigacyjnym zestawu SDK usługi WebJobs dane wyjściowe z obiektu **TextWriter** są wyświetlane po przejściu do strony dla określonego wywołania funkcji i wybraniu opcji **Przełącz dane wyjściowe**:

![Link wywołania](./media/vs-storage-webjobs-getting-started-queues/dashboardinvocations.png)

![Strona rejestrowania w wywołaniu funkcji](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

Na pulpicie nawigacyjnym zestawu SDK usługi WebJobs są wyświetlane ostatnie 100 wierszy danych wyjściowych konsoli, gdy przejdziesz do strony zadania WebJob (nie dla wywołania funkcji), a następnie wybierz pozycję **Przełącz dane wyjściowe**.

![Przełącz dane wyjściowe](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

W przypadku ciągłego Zadania WebJob Dzienniki aplikacji są wyświetlane w/Data/Jobs/Continuous/*{webjobname}*/job_log. txt w systemie plików aplikacji sieci Web.

        [09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
        [09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
        [09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

W obiekcie blob platformy Azure Dzienniki aplikacji wyglądają następująco: 2014-09-26T21:01:13, informacje, contosoadsnew, 491e54, 635473620738373502, 0, 17404, 17, Console. Write-Witaj świecie!, 2014-09-26T21:01:13, Error, contosoadsnew, 491e54, 635473620738373502, 0, 17404, 19, Console. Error-Hello World!, 2014-09-26T21:01:13, informacje, contosoadsnew, 491e54, 635473620738529920, 17404,, 17, Console. out-Hello World!,

I w tabeli platformy Azure **konsoli. out** i **Console. Error** Logs wyglądać następująco:

![Dziennik informacji w tabeli](./media/vs-storage-webjobs-getting-started-queues/tableinfo.png)

![Dziennik błędów w tabeli](./media/vs-storage-webjobs-getting-started-queues/tableerror.png)

## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono przykłady kodu, które pokazują, jak obsługiwać typowe scenariusze pracy z kolejkami platformy Azure. Więcej informacji o sposobach używania Azure WebJobs i zestawu SDK usługi WebJobs znajduje się w temacie [Azure WebJobs zasoby dokumentacji](https://go.microsoft.com/fwlink/?linkid=390226).

