---
title: Utrwalanie danych wyjściowych w usłudze Azure Storage za pomocą biblioteki Konwencji plików platformy .NET
description: Dowiedz się, jak używać biblioteki Konwencji plików Azure Batch dla platformy .NET, aby utrwalać zadanie wsadowe & dane wyjściowe zadania do usługi Azure Storage, a następnie wyświetlać te dane wyjściowe w Azure Portal.
ms.topic: how-to
ms.date: 11/14/2018
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: 1a45eed421dd8d734fcef0dd452df1d4a65fd053
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88936966"
---
# <a name="persist-job-and-task-data-to-azure-storage-with-the-batch-file-conventions-library-for-net"></a>Utrwalanie danych zadań i zadań w usłudze Azure Storage za pomocą biblioteki Konwencji plików wsadowych dla platformy .NET

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Jednym ze sposobów utrwalania danych zadania jest użycie [biblioteki Konwencji plików Azure Batch dla platformy .NET][nuget_package]. Biblioteka Konwencji plików upraszcza proces przechowywania danych wyjściowych zadań w usłudze Azure Storage i pobierania go. Można użyć biblioteki Konwencji plików w kodzie zadania i klienta &mdash; w kodzie zadania do utrwalania plików, a także w kodzie klienta, aby wyświetlić listę i pobrać je. Kod zadania może również użyć biblioteki do pobrania danych wyjściowych zadań nadrzędnych, takich jak scenariusz [zależności zadań](batch-task-dependencies.md) .

Aby pobrać pliki wyjściowe z biblioteką Konwencji plików, można zlokalizować pliki dla danego zadania lub zadania, wymieniając je według identyfikatorów i przeznaczenie. Nie musisz znać nazw lub lokalizacji plików. Na przykład można użyć biblioteki Konwencji plików, aby wyświetlić listę wszystkich plików pośrednich dla danego zadania, lub pobrać plik podglądu dla danego zadania.

> [!TIP]
> Począwszy od wersji 2017-05-01, interfejs API usługi Batch obsługuje utrwalanie danych wyjściowych w usłudze Azure Storage na potrzeby zadań i zadań Menedżera zadań, które są uruchamiane w pulach utworzonych przy użyciu konfiguracji maszyny wirtualnej. Interfejs API usługi Batch zapewnia prosty sposób utrwalania danych wyjściowych w kodzie, który tworzy zadanie i służy jako alternatywa dla biblioteki Konwencji plików. Możesz zmodyfikować aplikacje klienckie wsadowe, aby utrwalać dane wyjściowe bez konieczności aktualizowania aplikacji, która jest uruchomiona. Aby uzyskać więcej informacji, zobacz [utrwalanie danych zadań w usłudze Azure Storage za pomocą interfejsu API usługi Batch](batch-task-output-files.md).

## <a name="when-do-i-use-the-file-conventions-library-to-persist-task-output"></a>Kiedy używać biblioteki Konwencji plików do utrwalania danych wyjściowych zadań?

Azure Batch zapewnia więcej niż jeden sposób utrwalania danych wyjściowych zadania. Konwencje dotyczące plików najlepiej pasują do tych scenariuszy:

- Możesz łatwo zmodyfikować kod aplikacji, w której uruchomiono zadanie w celu utrwalenia plików przy użyciu biblioteki Konwencji plików.
- Chcesz przesyłać strumieniowo dane do usługi Azure Storage, gdy zadanie jest nadal uruchomione.
- Chcesz utrwalać dane z pul utworzonych za pomocą konfiguracji usługi w chmurze lub konfiguracji maszyny wirtualnej.
- Aplikacja kliencka lub inne zadania w zadaniu muszą odszukać i pobrać pliki wyjściowe zadania według identyfikatora lub według celu.
- Chcesz wyświetlić dane wyjściowe zadania w Azure Portal.

Jeśli Twój Scenariusz różni się od wymienionych powyżej, może być konieczne rozważenie innego podejścia. Aby uzyskać więcej informacji na temat innych opcji utrwalania danych wyjściowych zadania, zobacz [utrwalanie zadań i zadań wyjściowych w usłudze Azure Storage](batch-task-output.md).

## <a name="what-is-the-batch-file-conventions-standard"></a>Co to jest standardowa Konwencja pliku wsadowego?

Standard nazewnictwa [plików wsadowych](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files) zawiera schemat nazw kontenerów docelowych i ścieżek obiektów blob, do których są zapisywane pliki wyjściowe. Pliki utrwalone w usłudze Azure Storage, które są zgodne ze standardem Konwencji plików, są automatycznie dostępne do wyświetlania w Azure Portal. Portal ma świadomość konwencji nazewnictwa i dlatego może wyświetlać pliki, które są do niego zgodne.

Biblioteka Konwencji plików dla platformy .NET automatycznie nazywa kontenery magazynów i pliki danych wyjściowych zadań zgodnie ze standardem Konwencji plików. Biblioteka Konwencji plików udostępnia również metody wykonywania zapytań dotyczących plików wyjściowych w usłudze Azure Storage zgodnie z IDENTYFIKATORem zadania, IDENTYFIKATORem zadania lub przeznaczeniem.

Jeśli tworzysz program przy użyciu języka innego niż .NET, możesz wdrożyć w aplikacji standardową Konwencję plików. Aby uzyskać więcej informacji, zobacz [implementacja standardu plików wsadowych](batch-task-output.md#implement-the-batch-file-conventions-standard).

## <a name="link-an-azure-storage-account-to-your-batch-account"></a>Połącz konto magazynu platformy Azure z kontem usługi Batch

Aby zachować dane wyjściowe do usługi Azure Storage przy użyciu biblioteki Konwencji plików, musisz najpierw połączyć konto usługi Azure Storage z kontem w usłudze Batch. Jeśli jeszcze tego nie zrobiono, Połącz konto magazynu z kontem usługi Batch przy użyciu [Azure Portal](https://portal.azure.com):

1. W witrynie Azure Portal przejdź do swojego konta usługi Batch.
1. W obszarze **Ustawienia** wybierz pozycję **konto magazynu**.
1. Jeśli nie masz jeszcze konta magazynu skojarzonego z kontem w usłudze Batch, kliknij pozycję **konto magazynu (brak)**.
1. Wybierz konto magazynu z listy dla subskrypcji. Aby uzyskać najlepszą wydajność, użyj konta usługi Azure Storage, które znajduje się w tym samym regionie, w którym są uruchomione zadania wsadowe.

## <a name="persist-output-data"></a>Utrwalanie danych wyjściowych

Aby zachować dane wyjściowe zadania i zadania przy użyciu biblioteki Konwencji plików, Utwórz kontener w usłudze Azure Storage, a następnie Zapisz dane wyjściowe do kontenera. Użyj [biblioteki klienta usługi Azure Storage dla platformy .NET](https://www.nuget.org/packages/WindowsAzure.Storage) w kodzie zadania, aby przekazać dane wyjściowe zadania do kontenera.

Aby uzyskać więcej informacji na temat pracy z kontenerami i obiektami BLOB w usłudze Azure Storage, zobacz Wprowadzenie [do usługi Azure Blob Storage przy użyciu platformy .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md).

> [!WARNING]
> Wszystkie dane wyjściowe zadania i zadania utrwalane za pomocą biblioteki Konwencji plików są przechowywane w tym samym kontenerze. W przypadku dużej liczby zadań, które próbują jednocześnie utrwalać pliki, można wymusić limity ograniczenia usługi Azure Storage. Aby uzyskać więcej informacji na temat limitów ograniczania przepustowości, zobacz [Lista kontrolna wydajności i skalowalności dla usługi BLOB Storage](../storage/blobs/storage-performance-checklist.md).

### <a name="create-storage-container"></a>Tworzenie kontenera magazynu

Aby zachować dane wyjściowe zadania do usługi Azure Storage, należy najpierw utworzyć kontener przez wywołanie [CloudJob][net_cloudjob]. [PrepareOutputStorageAsync][net_prepareoutputasync]. Ta metoda rozszerzenia przyjmuje obiekt [CloudStorageAccount][net_cloudstorageaccount] jako parametr. Tworzy kontener o nazwie zgodnej ze standardem Konwencji plików, dzięki czemu jego zawartość jest wykrywalna przez Azure Portal i metody pobierania omówione w dalszej części artykułu.

Zwykle umieszczasz kod w celu utworzenia kontenera w aplikacji klienckiej &mdash; aplikacji, która tworzy pule, zadania i zadania.

```csharp
CloudJob job = batchClient.JobOperations.CreateJob(
    "myJob",
    new PoolInformation { PoolId = "myPool" });

// Create reference to the linked Azure Storage account
CloudStorageAccount linkedStorageAccount =
    new CloudStorageAccount(myCredentials, true);

// Create the blob storage container for the outputs
await job.PrepareOutputStorageAsync(linkedStorageAccount);
```

### <a name="store-task-outputs"></a>Przechowuj dane wyjściowe zadania

Teraz, gdy przygotowano kontener w usłudze Azure Storage, zadania mogą zapisywać dane wyjściowe do kontenera za pomocą klasy [TaskOutputStorage][net_taskoutputstorage] znajdującej się w bibliotece Konwencji plików.

W kodzie zadania najpierw Utwórz obiekt [TaskOutputStorage][net_taskoutputstorage] , a następnie, gdy zadanie zakończy pracę, wywołaj [TaskOutputStorage][net_taskoutputstorage]. [SaveAsync][net_saveasync] metoda zapisywania danych wyjściowych w usłudze Azure Storage.

```csharp
CloudStorageAccount linkedStorageAccount = new CloudStorageAccount(myCredentials);
string jobId = Environment.GetEnvironmentVariable("AZ_BATCH_JOB_ID");
string taskId = Environment.GetEnvironmentVariable("AZ_BATCH_TASK_ID");

TaskOutputStorage taskOutputStorage = new TaskOutputStorage(
    linkedStorageAccount, jobId, taskId);

/* Code to process data and produce output file(s) */

await taskOutputStorage.SaveAsync(TaskOutputKind.TaskOutput, "frame_full_res.jpg");
await taskOutputStorage.SaveAsync(TaskOutputKind.TaskPreview, "frame_low_res.jpg");
```

`kind`Parametr [TaskOutputStorage](/dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage).[ Metoda SaveAsync](/dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync#overloads) klasyfikuje utrwalone pliki. Istnieją cztery wstępnie zdefiniowane typy [TaskOutputKind][net_taskoutputkind] : `TaskOutput` , `TaskPreview` ,, `TaskLog` i `TaskIntermediate.` można także definiować niestandardowe kategorie danych wyjściowych.

Te typy wyjściowe umożliwiają określenie typu danych wyjściowych do wyświetlenia podczas późniejszej kwerendy partii dla utrwalonych danych wyjściowych danego zadania. Innymi słowy, gdy wyświetlasz listę danych wyjściowych dla zadania, możesz filtrować listę na jednym z typów wyjściowych. Na przykład "Nadaj mi *Podgląd* danych wyjściowych dla zadania *109*". Więcej informacji na temat wyświetlania i pobierania danych wyjściowych znajduje się w dalszej części artykułu.

> [!TIP]
> Typ wyjściowy określa również, gdzie w Azure Portal pojawia się określony plik: pliki z kategoryzacją *TaskOutput* są wyświetlane w obszarze **pliki wyjściowe zadania**, a pliki *TaskLog* są wyświetlane w obszarze **dzienniki zadań**.

### <a name="store-job-outputs"></a>Przechowuj dane wyjściowe zadania

Oprócz przechowywania danych wyjściowych zadań można przechowywać dane wyjściowe skojarzone z całym zadaniem. Na przykład w zadaniu scalania zadania renderowania filmu można utrwalać w pełni renderowany film jako dane wyjściowe zadania. Gdy zadanie zostanie ukończone, aplikacja kliencka będzie mogła wyświetlać i pobierać dane wyjściowe zadania i nie musi wykonywać zapytania do poszczególnych zadań.

Przechowuj dane wyjściowe zadania, wywołując [JobOutputStorage][net_joboutputstorage]. [SaveAsync][net_joboutputstorage_saveasync] i określ [JobOutputKind][net_joboutputkind] i nazwę pliku:

```csharp
CloudJob job = new JobOutputStorage(acct, jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

Podobnie jak w przypadku typu **TaskOutputKind** dla danych wyjściowych zadania, należy użyć typu [JobOutputKind][net_joboutputkind] do kategoryzacji utrwalonych plików zadania. Ten parametr umożliwia późniejsze zapytanie o (listę) określonego typu danych wyjściowych. Typ **JobOutputKind** zawiera kategorie Output i Preview oraz obsługuje tworzenie kategorii niestandardowych.

### <a name="store-task-logs"></a>Przechowuj dzienniki zadań

Oprócz utrwalania plików w trwałej pamięci masowej po zakończeniu zadania lub zadania, może być konieczne utrwalenie plików, które są aktualizowane podczas wykonywania &mdash; plików dziennika zadań lub `stdout.txt` `stderr.txt` na przykład. W tym celu Biblioteka Konwencji plików Azure Batch udostępnia [TaskOutputStorage][net_taskoutputstorage]. [SaveTrackedAsync][net_savetrackedasync] . Za pomocą [SaveTrackedAsync][net_savetrackedasync]można śledzić aktualizacje pliku w węźle (w określonym interwale) i zachować te aktualizacje w usłudze Azure Storage.

W poniższym fragmencie kodu używamy [SaveTrackedAsync][net_savetrackedasync] do aktualizacji `stdout.txt` usługi Azure Storage co 15 sekund podczas wykonywania zadania:

```csharp
TimeSpan stdoutFlushDelay = TimeSpan.FromSeconds(3);
string logFilePath = Path.Combine(
    Environment.GetEnvironmentVariable("AZ_BATCH_TASK_DIR"), "stdout.txt");

// The primary task logic is wrapped in a using statement that sends updates to
// the stdout.txt blob in Storage every 15 seconds while the task code runs.
using (ITrackedSaveOperation stdout =
        await taskStorage.SaveTrackedAsync(
        TaskOutputKind.TaskLog,
        logFilePath,
        "stdout.txt",
        TimeSpan.FromSeconds(15)))
{
    /* Code to process data and produce output file(s) */

    // We are tracking the disk file to save our standard output, but the
    // node agent may take up to 3 seconds to flush the stdout stream to
    // disk. So give the file a moment to catch up.
     await Task.Delay(stdoutFlushDelay);
}
```

Sekcja z komentarzem `Code to process data and produce output file(s)` jest symbolem zastępczym dla kodu, który zwykle wykonuje zadanie. Przykładowo może być kod, który pobiera dane z usługi Azure Storage i wykonuje transformację lub obliczenie. Ważna część tego fragmentu kodu przedstawia sposób, w jaki można otoczyć ten kod w `using` bloku, aby okresowo aktualizować plik za pomocą [SaveTrackedAsync][net_savetrackedasync].

Agent węzła jest programem uruchamianym w każdym węźle w puli i udostępnia interfejs poleceń i kontroli między węzłem a usługą Batch. `Task.Delay`Wywołanie jest wymagane na końcu tego `using` bloku, aby upewnić się, że Agent węzła ma czas, aby opróżnić zawartość Standard do pliku stdout.txt w węźle. Bez tego opóźnienia można pominąć ostatnie kilka sekund danych wyjściowych. To opóźnienie może nie być wymagane dla wszystkich plików.

> [!NOTE]
> Po włączeniu śledzenia plików przy użyciu usługi **SaveTrackedAsync** tylko *dołączanie* do śledzonego pliku są utrwalane w usłudze Azure Storage. Tej metody należy używać tylko do śledzenia plików dziennika nieobracania lub innych plików, które są zapisywane przy użyciu operacji dołączania na końcu pliku.

## <a name="retrieve-output-data"></a>Pobieranie danych wyjściowych

Po pobraniu utrwalonych danych wyjściowych za pomocą biblioteki Konwencji plików Azure Batch, należy to zrobić w sposób zorientowany na zadania i zadania. Możesz zażądać danych wyjściowych dla danego zadania lub zadania bez znajomości jego ścieżki w usłudze Azure Storage, a nawet jego nazwy pliku. Zamiast tego można zażądać plików wyjściowych według zadania lub identyfikatora zadania.

Poniższy fragment kodu wykonuje iterację zadań zadania, drukuje pewne informacje o plikach wyjściowych zadania, a następnie pobiera pliki z magazynu.

```csharp
foreach (CloudTask task in myJob.ListTasks())
{
    foreach (OutputFileReference output in
        task.OutputStorage(storageAccount).ListOutputs(
            TaskOutputKind.TaskOutput))
    {
        Console.WriteLine($"output file: {output.FilePath}");

        output.DownloadToFileAsync(
            $"{jobId}-{output.FilePath}",
            System.IO.FileMode.Create).Wait();
    }
}
```

## <a name="view-output-files-in-the-azure-portal"></a>Wyświetlanie plików wyjściowych w Azure Portal

Azure Portal Wyświetla pliki wyjściowe zadania i dzienniki, które są utrwalane na połączonym koncie usługi Azure Storage przy użyciu [standardu plików wsadowych](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files). Można zaimplementować te konwencje samodzielnie w wybranym języku lub można użyć biblioteki Konwencji plików w aplikacjach .NET.

Aby umożliwić wyświetlanie plików wyjściowych w portalu, musisz spełnić następujące wymagania:

1. Połącz konto magazynu platformy Azure z kontem usługi Batch.
1. Przestrzegaj wstępnie zdefiniowanych konwencji nazewnictwa dla kontenerów i plików magazynu podczas utrwalania danych wyjściowych. Definicje tych konwencji można znaleźć w pliku [README][github_file_conventions_readme]biblioteki Konwencji plików. Jeśli używasz biblioteki [Konwencji plików Azure Batch][nuget_package] do utrwalania danych wyjściowych, pliki są utrwalane zgodnie ze standardem Konwencji plików.

Aby wyświetlić pliki i dzienniki danych wyjściowych zadania w Azure Portal, przejdź do zadania, którego dane wyjściowe interesują Cię, a następnie kliknij opcję **zapisane pliki wyjściowe** lub **zapisane dzienniki**. Ten obraz przedstawia **zapisane pliki wyjściowe** dla zadania o identyfikatorze "007":

![Blok danych wyjściowych zadania w Azure Portal][2]

## <a name="code-sample"></a>Przykład kodu

Przykładowy projekt [PersistOutputs][github_persistoutputs] jest jednym z [przykładów kodu Azure Batch][github_samples] w witrynie GitHub. W tym rozwiązaniu programu Visual Studio pokazano, jak używać biblioteki Konwencji plików Azure Batch do utrwalania danych wyjściowych zadań w magazynie trwałym. Aby uruchomić przykład, wykonaj następujące kroki:

1. Otwórz projekt w programie **Visual Studio 2019**.
2. Dodaj **poświadczenia konta** magazynu i partii do **AccountSettings. settings** w projekcie Microsoft.Azure.Batch. Samples. Common.
3. **Kompiluj** (ale nie uruchamiaj) rozwiązanie. Jeśli zostanie wyświetlony monit, Przywróć wszystkie pakiety NuGet.
4. Użyj Azure Portal, aby przekazać [pakiet aplikacji](batch-application-packages.md) dla **PersistOutputsTask**. Uwzględnij `PersistOutputsTask.exe` i jej zestawy zależne w pakiecie. zip, ustaw identyfikator aplikacji na "PersistOutputsTask" i wersję pakietu aplikacji na "1,0".
5. **Uruchom** (Uruchom) projekt **PersistOutputs** .
6. Po wyświetleniu monitu o wybranie technologii trwałości do uruchomienia przykładu wprowadź **1** , aby uruchomić przykład za pomocą biblioteki Konwencji plików, aby utrzymać dane wyjściowe zadania. 

## <a name="next-steps"></a>Następne kroki

### <a name="get-the-batch-file-conventions-library-for-net"></a>Pobierz bibliotekę Konwencji plików wsadowych dla platformy .NET

Biblioteka Konwencji plików wsadowych dla platformy .NET jest dostępna w programie [NuGet][nuget_package]. Biblioteka rozszerza klasy [CloudJob][net_cloudjob] i [CloudTask][net_cloudtask] o nowe metody. Zobacz również [dokumentację referencyjną](/dotnet/api/microsoft.azure.batch.conventions.files) biblioteki Konwencji plików.

[Kod źródłowy][github_file_conventions] biblioteki Konwencji plików jest dostępny w witrynie GitHub w repozytorium zestaw Microsoft Azure SDK dla platformy .NET. 

### <a name="explore-other-approaches-for-persisting-output-data"></a>Eksplorowanie innych metod utrwalania danych wyjściowych

- Zapoznaj się z omówieniem utrwalania danych zadań i zadań [w usłudze Azure Storage](batch-task-output.md) .
- Zobacz [utrwalanie danych zadań w usłudze Azure Storage za pomocą interfejsu API usługi Batch,](batch-task-output-files.md) aby dowiedzieć się, jak używać interfejsu API usługi Batch do utrwalania danych wyjściowych.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_file_conventions]: https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files
[github_file_conventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files/README.md
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: /dotnet/api/microsoft.azure.batch.batchclient
[net_cloudjob]: /dotnet/api/microsoft.azure.batch.cloudjob
[net_cloudstorageaccount]: /java/api/com.microsoft.azure.storage.cloudstorageaccount
[net_cloudtask]: /dotnet/api/microsoft.azure.batch.cloudtask
[net_fileconventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files/README.md
[net_joboutputkind]: /dotnet/api/microsoft.azure.batch.conventions.files.joboutputkind
[net_joboutputstorage]: /dotnet/api/microsoft.azure.batch.conventions.files.joboutputstorage
[net_joboutputstorage_saveasync]: /dotnet/api/microsoft.azure.batch.conventions.files.joboutputstorage.saveasync
[net_msdn]: /dotnet/api/microsoft.azure.batch
[net_prepareoutputasync]: /dotnet/api/microsoft.azure.batch.conventions.files.cloudjobextensions.prepareoutputstorageasync
[net_saveasync]: /dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync
[net_savetrackedasync]: /dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage.savetrackedasync
[net_taskoutputkind]: /dotnet/api/microsoft.azure.batch.conventions.files.taskoutputkind
[net_taskoutputstorage]: /dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage
[nuget_manager]: https://docs.nuget.org/consume/installing-nuget
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: https://storageexplorer.com/

[1]: ./media/batch-task-output/task-output-01.png "Zapisane pliki wyjściowe i selektory zapisanych dzienników w portalu"
[2]: ./media/batch-task-output/task-output-02.png "Blok danych wyjściowych zadania w Azure Portal"
