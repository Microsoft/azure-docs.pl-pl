---
title: Uruchamianie równoległego obciążenia przy użyciu interfejsu API platformy .NET
description: Samouczek — Równoległe transkodowanie plików multimedialnych przy użyciu narzędzia ffmpeg w usłudze Azure Batch z zastosowaniem biblioteki klienta Batch .NET
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 09/29/2020
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 396d0f6a2ef9a8c24fc92b641c889ef9e1a7df49
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578301"
---
# <a name="tutorial-run-a-parallel-workload-with-azure-batch-using-the-net-api"></a>Samouczek: uruchamianie równoległego obciążenia w usłudze Azure Batch przy użyciu interfejsu API .NET

Usługa Azure Batch umożliwia wydajne uruchamianie równoległych zadań wsadowych oraz zadań wsadowych obliczeń o wysokiej wydajności na platformie Azure. W tym samouczku przedstawiono przykład uruchamiania równoległego obciążenia za pomocą usługi Azure Batch i języka C#. Poznasz prosty przepływ pracy aplikacji usługi Azure Batch i sposób pracy programowej z zasobami usług Azure Batch i Storage. Omawiane kwestie:

> [!div class="checklist"]
> * Dodawanie pakietu aplikacji do konta usługi Batch
> * Uwierzytelnianie przy użyciu kont usług Batch i Storage
> * Przekazywanie plików wejściowych do usługi Storage
> * Tworzenie puli węzłów obliczeniowych w celu uruchomienia aplikacji
> * Tworzenie zadania i zadań podrzędnych przetwarzania plików wejściowych
> * Monitorowanie wykonania zadań podrzędnych
> * Pobieranie plików wyjściowych

W tym samouczku przekonwertujesz równolegle pliki multimedialne w formacie MP4 do formatu MP3 za pomocą narzędzia typu „open source”, [ffmpeg](https://ffmpeg.org/). 

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* [Program Visual Studio 2017 lub nowszy](https://www.visualstudio.com/vs)lub [.NET Core 2,1](https://www.microsoft.com/net/download/dotnet-core/2.1) dla systemów Linux, macOS i Windows.

* Konto usługi Batch i połączone konto usługi Azure Storage. Aby utworzyć te konta, skorzystaj z przewodników Szybki start dla usługi Batch i [witryny Azure Portal](quick-create-portal.md) lub [interfejsu wiersza polecenia platformy Azure](quick-create-cli.md).

* [Windows 64-bitowa wersja narzędzia FFmpeg 4.3.1](https://github.com/GyanD/codexffmpeg/releases/tag/4.3.1-2020-11-08) (. zip). Pobierz plik zip na komputer lokalny. Na potrzeby tego samouczka potrzebujesz tylko pliku zip. Nie musisz go rozpakowywać ani instalować lokalnie.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="add-an-application-package"></a>Dodawanie pakietu aplikacji

W witrynie Azure Portal dodaj narzędzie ffmpeg jako [pakiet aplikacji](batch-application-packages.md) do konta usługi Batch. Pakiety aplikacji ułatwiają zarządzanie aplikacjami zadań i wdrażanie ich w węzłach obliczeniowych w puli. 

1. W Azure Portal kliknij pozycję **więcej usług usługa**  >  **Batch konta** , a następnie kliknij nazwę konta usługi Batch.
3. Kliknij pozycję **aplikacje**  >  **Dodaj**.
4. W przypadku **identyfikatora aplikacji** wprowadź *Narzędzia FFmpeg* i wersję programu *4.3.1*. Zaznacz pobrany wcześniej plik zip narzędzia ffmpeg i kliknij przycisk **OK**. Pakiet aplikacji z narzędziem ffmpeg zostanie dodany do konta usługi Batch.

![Dodawanie pakietu aplikacji](./media/tutorial-parallel-dotnet/add-application.png)

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)]

## <a name="download-and-run-the-sample"></a>Pobieranie i uruchamianie aplikacji przykładowej

### <a name="download-the-sample"></a>Pobieranie przykładu

[Pobierz lub sklonuj przykładową aplikację](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) z usługi GitHub. Aby sklonować repozytorium przykładowej aplikacji za pomocą klienta Git, użyj następującego polecenia:

```
git clone https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial.git
```

Przejdź do katalogu, który zawiera plik rozwiązania programu Visual Studio `BatchDotNetFfmpegTutorial.sln`.

Otwórz plik rozwiązania w programie Visual Studio i zaktualizuj ciągi poświadczeń w pliku `Program.cs`, wprowadzając wartości uzyskane dla kont. Przykład:

```csharp
// Batch account credentials
private const string BatchAccountName = "mybatchaccount";
private const string BatchAccountKey  = "xxxxxxxxxxxxxxxxE+yXrRvJAqT9BlXwwo1CwF+SwAYOxxxxxxxxxxxxxxxx43pXi/gdiATkvbpLRl3x14pcEQ==";
private const string BatchAccountUrl  = "https://mybatchaccount.mybatchregion.batch.azure.com";

// Storage account credentials
private const string StorageAccountName = "mystorageaccount";
private const string StorageAccountKey  = "xxxxxxxxxxxxxxxxy4/xxxxxxxxxxxxxxxxfwpbIC5aAWA8wDu+AFXZB827Mt9lybZB1nUcQbQiUrkPtilK5BQ==";
```

[!INCLUDE [batch-credentials-include](../../includes/batch-credentials-include.md)]

Upewnij się też, że odwołanie do pakietu aplikacji narzędzia ffmpeg w pliku rozwiązania jest zgodne z identyfikatorem i wersją pakietu aplikacji ffmpeg przekazanego na konto usługi Batch.

```csharp
const string appPackageId = "ffmpeg";
const string appPackageVersion = "4.3.1";
```

### <a name="build-and-run-the-sample-project"></a>Kompilowanie i uruchamianie przykładowego projektu

Skompiluj i uruchom aplikację w programie Visual Studio lub w wierszu polecenia, używając poleceń `dotnet build` i `dotnet run`. Po uruchomieniu aplikacji przejrzyj kod, aby poznać działanie poszczególnych części aplikacji. Na przykład w programie Visual Studio:

* Kliknij prawym przyciskiem myszy rozwiązanie w Eksplorator rozwiązań i kliknij polecenie **Kompiluj rozwiązanie**. 

* Jeśli zostanie wyświetlony monit, potwierdź przywrócenie pakietów NuGet. Jeśli musisz pobrać brakujące pakiety, upewnij się, że zainstalowano [menedżera pakietów NuGet](https://docs.nuget.org/consume/installing-nuget).

Następnie uruchom go. Po uruchomieniu aplikacji przykładowej dane wyjściowe w konsoli będą wyglądać mniej więcej następująco. W czasie wykonywania nastąpi wstrzymanie operacji w momencie wyświetlenia komunikatu `Monitoring all tasks for 'Completed' state, timeout in 00:30:00...` podczas uruchamiania węzłów obliczeniowych puli. 

```
Sample start: 11/19/2018 3:20:21 PM

Container [input] created.
Container [output] created.
Uploading file LowPriVMs-1.mp4 to container [input]...
Uploading file LowPriVMs-2.mp4 to container [input]...
Uploading file LowPriVMs-3.mp4 to container [input]...
Uploading file LowPriVMs-4.mp4 to container [input]...
Uploading file LowPriVMs-5.mp4 to container [input]...
Creating pool [WinFFmpegPool]...
Creating job [WinFFmpegJob]...
Adding 5 tasks to job [WinFFmpegJob]...
Monitoring all tasks for 'Completed' state, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Deleting container [input]...

Sample end: 11/19/2018 3:29:36 PM
Elapsed time: 00:09:14.3418742
```

Przejdź do konta usługi Batch w witrynie Azure Portal, aby monitorować pulę, węzły obliczeniowe, zadanie i zadania podrzędne. Na przykład aby wyświetlić mapę cieplną węzłów obliczeniowych w puli, kliknij pozycję **Pule**  >  *WinFFmpegPool*.

Podczas wykonywania zadań podrzędnych mapa cieplna może wyglądać następująco:

![Mapa cieplna puli](./media/tutorial-parallel-dotnet/pool.png)

Typowy czas wykonywania wynosi mniej więcej **10 minut** w przypadku uruchomienia aplikacji w konfiguracji domyślnej. Tworzenie puli zajmuje najwięcej czasu.

[!INCLUDE [batch-common-tutorial-download](../../includes/batch-common-tutorial-download.md)]

## <a name="review-the-code"></a>Przeglądanie kodu

W poniższych sekcjach przykładowa aplikacja została podzielona na kroki wykonywane w celu przetworzenia obciążenia w usłudze Batch. Podczas czytania dalszej części tego artykułu obserwuj zawartość pliku `Program.cs`, ponieważ nie omówiono tu wszystkich wierszy kodu z próbki.

### <a name="authenticate-blob-and-batch-clients"></a>Uwierzytelnianie klientów obiektów blob i usługi Batch

Podczas interakcji z połączonym kontem magazynu aplikacja używa biblioteki klienta usługi Azure Storage dla środowiska .NET. Tworzy odwołanie do konta przy użyciu obiektu [CloudStorageAccount](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount), korzystając z uwierzytelniania za pomocą klucza współużytkowanego. Następnie tworzy obiekt [CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient).

```csharp
// Construct the Storage account connection string
string storageConnectionString = String.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
                                StorageAccountName, StorageAccountKey);

// Retrieve the storage account
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);

CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```

Aplikacja tworzy obiekt [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient) w celu tworzenia pul, zadań i zadań podrzędnych w usłudze Batch i zarządzania nimi. Klient usługi Batch w przykładzie korzysta z uwierzytelniania za pomocą klucza wspólnego. Usługa Batch obsługuje również uwierzytelnianie za pośrednictwem usługi [Azure Active Directory](batch-aad-auth.md), umożliwiające uwierzytelnianie poszczególnych użytkowników lub nienadzorowanej aplikacji.

```csharp
BatchSharedKeyCredentials sharedKeyCredentials = new BatchSharedKeyCredentials(BatchAccountUrl, BatchAccountName, BatchAccountKey);

using (BatchClient batchClient = BatchClient.Open(sharedKeyCredentials))
...
```

### <a name="upload-input-files"></a>Przekazywanie plików wejściowych

Aplikacja przekazuje obiekt `blobClient` do metody `CreateContainerIfNotExistAsync` w celu utworzenia kontenera magazynu dla plików wejściowych (w formacie MP4) oraz kontenera dla danych wyjściowych zadania podrzędnego.

```csharp
CreateContainerIfNotExistAsync(blobClient, inputContainerName);
CreateContainerIfNotExistAsync(blobClient, outputContainerName);
```

Następnie pliki są przekazywane do kontenera wejściowego z lokalnego folderu `InputFiles`. Pliki w magazynie są zdefiniowane jako obiekty [ResourceFile](/dotnet/api/microsoft.azure.batch.resourcefile) usługi Batch, które następnie mogą zostać pobrane przez tę usługę do węzłów obliczeniowych. 

Podczas przekazywania plików używane są dwie metody w pliku `Program.cs`:

* `UploadFilesToContainerAsync`: zwraca kolekcję obiektów ResourceFile i wywołuje wewnętrznie element `UploadResourceFileToContainerAsync` w celu przekazania wszystkich plików przekazywanych w parametrze `inputFilePaths`.
* `UploadResourceFileToContainerAsync`: przekazuje poszczególne pliki jako obiekty blob do kontenera wejściowego. Po przekazaniu pliku uzyskuje sygnaturę dostępu współdzielonego (SAS) dla obiektu blob i zwraca obiekt ResourceFile, który go reprezentuje.

```csharp
string inputPath = Path.Combine(Environment.CurrentDirectory, "InputFiles");

List<string> inputFilePaths = new List<string>(Directory.GetFileSystemEntries(inputPath, "*.mp4",
    SearchOption.TopDirectoryOnly));

List<ResourceFile> inputFiles = await UploadFilesToContainerAsync(
  blobClient,
  inputContainerName,
  inputFilePaths);
```

Szczegółowe informacje na temat przekazywania plików jako obiektów blob na konto magazynu przy użyciu środowiska .NET zawiera artykuł [Szybki start: przekazywanie, pobieranie i wyświetlanie listy obiektów blob za pomocą platformy .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md).

### <a name="create-a-pool-of-compute-nodes"></a>Tworzenie puli węzłów obliczeniowych

Następnie w przykładzie tworzona jest pula węzłów obliczeniowych na koncie usługi Batch z wywołaniem funkcji `CreatePoolIfNotExistAsync`. Ta zdefiniowana metoda używa metody [BatchClient.PoolOperations.CreatePool](/dotnet/api/microsoft.azure.batch.pooloperations.createpool) w celu ustawienia liczby węzłów, rozmiaru maszyny wirtualnej i konfiguracji puli. W tym przypadku obiekt [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) określa parametr [ImageReference](/dotnet/api/microsoft.azure.batch.imagereference) z odwołaniem do obrazu systemu Windows Server opublikowanego w witrynie Azure Marketplace. Usługa Batch obsługuje szeroki zakres obrazów maszyn wirtualnych z witryny Azure Marketplace oraz niestandardowe obrazy maszyn wirtualnych.

Liczba węzłów i rozmiar maszyny wirtualnej są ustawiane przy użyciu zdefiniowanych stałych. Usługa Batch obsługuje węzły dedykowane oraz [węzły o niskim priorytecie](batch-low-pri-vms.md). W puli możesz użyć dowolnego z tych typów węzłów lub obu. Węzły dedykowane są zarezerwowane dla Twojej puli. Węzły o niskim priorytecie są oferowane w obniżonej cenie i korzystają z nadwyżek pojemności maszyn wirtualnych na platformie Azure. Węzły o niskim priorytecie staną się niedostępne, jeśli pojemność platformy Azure będzie niewystarczająca. Domyślnie przykładowa aplikacja tworzy pulę zawierającą tylko 5 węzłów o niskim priorytecie i rozmiarze *Standardowa_A1_v2*.

>[!Note]
>Upewnij się, że przydziały węzłów są sprawdzane. Zobacz [przydziały i limity usługi Batch,](batch-quota-limit.md#increase-a-quota) Aby uzyskać instrukcje dotyczące sposobu tworzenia żądania limitu przydziału.

Aplikacja ffmpeg jest wdrażana w węzłach obliczeniowych przez dodanie parametru [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference) do konfiguracji puli.

Metoda [CommitAsync](/dotnet/api/microsoft.azure.batch.cloudpool.commitasync) przesyła pulę do usługi Batch.

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "MicrosoftWindowsServer",
    offer: "WindowsServer",
    sku: "2016-Datacenter-smalldisk",
    version: "latest");

VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(
    imageReference: imageReference,
    nodeAgentSkuId: "batch.node.windows amd64");

pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: DedicatedNodeCount,
    targetLowPriorityComputeNodes: LowPriorityNodeCount,
    virtualMachineSize: PoolVMSize,
    virtualMachineConfiguration: virtualMachineConfiguration);

pool.ApplicationPackageReferences = new List<ApplicationPackageReference>
    {
    new ApplicationPackageReference {
    ApplicationId = appPackageId,
    Version = appPackageVersion}};

await pool.CommitAsync();  
```

### <a name="create-a-job"></a>Tworzenie zadania

Zadanie usługi Batch określa pulę, w której będą uruchamiane zadania podrzędne, wraz z ustawieniami opcjonalnymi, takimi jak priorytet i harmonogram pracy. Przykładowa aplikacja tworzy zadanie z wywołaniem `CreateJobAsync`. W tej zdefiniowanej metodzie zadanie jest tworzone w puli za pomocą metody [BatchClient.JobOperations.CreateJob](/dotnet/api/microsoft.azure.batch.joboperations.createjob).

Metoda [CommitAsync](/dotnet/api/microsoft.azure.batch.cloudjob.commitasync) przesyła zadanie do usługi Batch. Początkowo zadanie nie zawiera zadań podrzędnych.

```csharp
CloudJob job = batchClient.JobOperations.CreateJob();
job.Id = JobId;
job.PoolInformation = new PoolInformation { PoolId = PoolId };

await job.CommitAsync();
```

### <a name="create-tasks"></a>Tworzenie zadań

Przykładowa aplikacja tworzy zadania podrzędne w ramach zadania, wywołując metodę `AddTasksAsync`, co powoduje utworzenie listy obiektów [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask). Każdy obiekt `CloudTask` uruchamia narzędzie ffmpeg w celu przetworzenia wejściowego obiektu `ResourceFile` za pomocą właściwości [CommandLine](/dotnet/api/microsoft.azure.batch.cloudtask.commandline). Narzędzie ffmpeg zostało już zainstalowane na wszystkich węzłach podczas tworzenia puli. Tutaj wiersz polecenia jest używany do uruchomienia narzędzia ffmpeg w celu przekonwertowania każdego z plików wejściowych w formacie MP4 (wideo) na format MP3 (audio).

Przykładowa aplikacja tworzy obiekt [OutputFile](/dotnet/api/microsoft.azure.batch.outputfile) dla pliku MP3 po uruchomieniu wiersza polecenia. Pliki wyjściowe z każdego zadania podrzędnego (w tym przypadku jeden plik) są przekazywane do kontenera na połączonym koncie magazynu przy użyciu właściwości [OutputFiles](/dotnet/api/microsoft.azure.batch.cloudtask.outputfiles) w zadaniu podrzędnym. Wcześniej w przykładowym kodzie uzyskano adres URL sygnatury dostępu współdzielonego (`outputContainerSasUrl`) w celu zapewnienia dostępu do zapisu do kontenera wyjściowego. Zwróć uwagę na warunki ustawione dla obiektu `outputFile`. Plik wyjściowy z zadania jest przekazywany do kontenera tylko po pomyślnym zakończeniu zadania (`OutputFileUploadCondition.TaskSuccess`). Zobacz cały [przykładowy kod](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) w witrynie GitHub, aby uzyskać więcej informacji o szczegółach implementacji.

Następnie przykładowa aplikacja dodaje zadania podrzędne do zadania za pomocą metody [AddTaskAsync](/dotnet/api/microsoft.azure.batch.joboperations.addtaskasync), która tworzy kolejkę zadań podrzędnych do uruchomienia w węzłach obliczeniowych.

```csharp
 // Create a collection to hold the tasks added to the job.
List<CloudTask> tasks = new List<CloudTask>();

for (int i = 0; i < inputFiles.Count; i++)
{
    string taskId = String.Format("Task{0}", i);

    // Define task command line to convert each input file.
    string appPath = String.Format("%AZ_BATCH_APP_PACKAGE_{0}#{1}%", appPackageId, appPackageVersion);
    string inputMediaFile = inputFiles[i].FilePath;
    string outputMediaFile = String.Format("{0}{1}",
        System.IO.Path.GetFileNameWithoutExtension(inputMediaFile),
        ".mp3");
    string taskCommandLine = String.Format("cmd /c {0}\\ffmpeg-4.3.1-2020-09-21-full_build\\bin\\ffmpeg.exe -i {1} {2}", appPath, inputMediaFile, outputMediaFile);

    // Create a cloud task (with the task ID and command line)
    CloudTask task = new CloudTask(taskId, taskCommandLine);
    task.ResourceFiles = new List<ResourceFile> { inputFiles[i] };

    // Task output file
    List<OutputFile> outputFileList = new List<OutputFile>();
    OutputFileBlobContainerDestination outputContainer = new OutputFileBlobContainerDestination(outputContainerSasUrl);
    OutputFile outputFile = new OutputFile(outputMediaFile,
       new OutputFileDestination(outputContainer),
       new OutputFileUploadOptions(OutputFileUploadCondition.TaskSuccess));
    outputFileList.Add(outputFile);
    task.OutputFiles = outputFileList;
    tasks.Add(task);
}

// Add tasks as a collection
await batchClient.JobOperations.AddTaskAsync(jobId, tasks);
return tasks
```

### <a name="monitor-tasks"></a>Monitorowanie podzadań

Po dodaniu zadań podrzędnych do zadania usługa Batch automatycznie tworzy kolejkę zadań podrzędnych i planuje ich wykonanie w węzłach obliczeniowych powiązanej puli. Na podstawie określonych przez użytkownika ustawień usługa Batch obsługuje dodawanie zadań podrzędnych do kolejki, ich planowanie, ponawianie prób ich wykonania oraz inne czynności administracyjne.

Istnieje wiele sposobów, w jakie można monitorować wykonanie podzadań. Ta przykładowa aplikacja definiuje metodę `MonitorTasks`, umożliwiającą tylko raportowanie zakończenia zadań podrzędnych oraz ich powodzenia lub niepowodzenia. Kod metody `MonitorTasks` określa parametr [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel), aby efektywnie wybrać tylko minimum informacji o zadaniach podrzędnych. Następnie tworzy funkcję [TaskStateMonitor](/dotnet/api/microsoft.azure.batch.taskstatemonitor), która udostępnia narzędzia do monitorowania stanu zadań podrzędnych. W przypadku metody `MonitorTasks` przykładowa aplikacja czeka, aż wszystkie zadania podrzędne osiągną stan `TaskState.Completed` w określonym czasie. Następnie kończy zadanie i zgłasza wszystkie zadania podrzędne, które zostały ukończone, ale w przypadku których mógł wystąpić błąd, na przykład niezerowy kod zakończenia.

```csharp
TaskStateMonitor taskStateMonitor = batchClient.Utilities.CreateTaskStateMonitor();
try
{
    await taskStateMonitor.WhenAll(addedTasks, TaskState.Completed, timeout);
}
catch (TimeoutException)
{
    batchClient.JobOperations.TerminateJob(jobId);
    Console.WriteLine(incompleteMessage);
    return false;
}
batchClient.JobOperations.TerminateJob(jobId);
 Console.WriteLine(completeMessage);
...

```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po wykonaniu zadań podrzędnych aplikacja automatycznie usuwa utworzony wejściowy kontener magazynu, a opcjonalnie także pulę i zadanie usługi Batch. Dla obu klas [JobOperations](/dotnet/api/microsoft.azure.batch.batchclient.joboperations) i [PoolOperations](/dotnet/api/microsoft.azure.batch.batchclient.pooloperations) klienta BatchClient istnieją odpowiednie metody usuwania, które są wywoływane, jeśli potwierdzisz usunięcie. Mimo że nie są naliczane opłaty za same zadania i zadania podrzędne, są naliczane opłaty za węzły obliczeniowe. W związku z tym zaleca się przydzielanie pul stosownie do potrzeb. W przypadku usunięcia puli usuwane są również wszystkie dane wyjściowe zadań podrzędnych w węzłach. Pliki wyjściowe pozostają jednak na koncie magazynu.

Gdy grupa zasobów, konto usługi Batch i konto magazynu nie będą już potrzebne, usuń je. W tym celu w witrynie Azure Portal zaznacz grupę zasobów konta usługi Batch i kliknij pozycję **Usuń grupę zasobów**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie pakietu aplikacji do konta usługi Batch
> * Uwierzytelnianie przy użyciu kont usług Batch i Storage
> * Przekazywanie plików wejściowych do usługi Storage
> * Tworzenie puli węzłów obliczeniowych w celu uruchomienia aplikacji
> * Tworzenie zadania i zadań podrzędnych przetwarzania plików wejściowych
> * Monitorowanie wykonania zadań podrzędnych
> * Pobieranie plików wyjściowych

Aby zapoznać się z innymi przykładami planowania i przetwarzania obciążeń usługi Batch przy użyciu interfejsu API .NET, zobacz przykłady w witrynie GitHub.

> [!div class="nextstepaction"]
> [Przykłady kodu C# dla usługi Batch](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp)


Ustawienie zmiennej wystąpienia LowPriorityNodeCount = 0 i DedicatedNodeCount = 5 rozwiązało problem i zezwala na ukończenie zadania.
