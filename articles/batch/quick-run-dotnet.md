---
title: Szybki start — uruchamianie pierwszego zadania Azure Batch za pomocą interfejsu API platformy .NET
description: W tym przewodniku Szybki start uruchamiasz przykładowe Azure Batch i zadania z aplikacji języka C# przy użyciu biblioteki klienta .NET usługi Batch.
ms.topic: quickstart
ms.date: 08/17/2020
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 1c2328db520042ceff1861b740313befbed0e20d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876728"
---
# <a name="quickstart-run-your-first-azure-batch-job-with-the-net-api"></a>Szybki start: uruchamianie pierwszego zadania usługi Azure Batch za pomocą interfejsu API platformy .NET

Rozpoczynanie pracy z Azure Batch przez uruchomienie zadania z aplikacji języka C# zbudowanej na podstawie interfejsu API platformy .NET Azure Batch. Aplikacja przekazuje kilka plików danych wejściowych do usługi Azure Storage oraz tworzy pulę węzłów obliczeniowych (maszyn wirtualnych) usługi Batch. Następnie aplikacja tworzy przykładowe zadanie, które uruchamia zadania podrzędne w celu przetworzenia każdego pliku wejściowego w puli przy użyciu podstawowego polecenia.

Po ukończeniu tego przewodnika Szybki start będziesz rozumieć kluczowe pojęcia związane z usługą Batch, co pozwoli na wypróbowanie tej usługi z bardziej realistycznymi obciążeniami na większą skalę.

![Diagram przedstawiający przegląd przepływu pracy Azure Batch aplikacji.](./media/quick-run-dotnet/sampleapp.png)

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

- Konto usługi Batch i połączone konto usługi Azure Storage. Aby utworzyć te konta, skorzystaj z przewodników Szybki start dla usługi Batch i [witryny Azure Portal](quick-create-portal.md) lub [interfejsu wiersza polecenia platformy Azure](quick-create-cli.md).

- [Visual Studio 2017 lub nowszy](https://www.visualstudio.com/vs)albo [zestaw SDK platformy .NET Core 2.1](https://dotnet.microsoft.com/download/dotnet/2.1) dla systemu Linux, macOS lub Windows. 

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)]

## <a name="download-the-sample"></a>Pobieranie przykładu

[Pobierz lub sklonuj przykładową aplikację](https://github.com/Azure-Samples/batch-dotnet-quickstart) z usługi GitHub. Aby sklonować repozytorium przykładowej aplikacji za pomocą klienta Git, użyj następującego polecenia:

```
git clone https://github.com/Azure-Samples/batch-dotnet-quickstart.git
```

Przejdź do katalogu, który zawiera plik rozwiązania programu Visual Studio `BatchDotNetQuickstart.sln`.

Otwórz plik rozwiązania w programie Visual Studio i zaktualizuj ciągi poświadczeń w pliku `Program.cs`, wprowadzając wartości uzyskane dla kont. Na przykład:

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

## <a name="build-and-run-the-app"></a>Skompiluj i uruchom aplikację

Aby zobaczyć działanie przepływu pracy usługi Batch, skompiluj i uruchom aplikację w programie Visual Studio lub w wierszu polecenia, używając poleceń `dotnet build` i `dotnet run`. Po uruchomieniu aplikacji przejrzyj kod, aby poznać działanie poszczególnych części aplikacji. Na przykład w programie Visual Studio:

- W Eksploratorze rozwiązań kliknij rozwiązanie prawym przyciskiem myszy, a następnie kliknij polecenie **Kompiluj rozwiązanie**. 

- Jeśli zostanie wyświetlony monit, potwierdź przywrócenie pakietów NuGet. Jeśli musisz pobrać brakujące pakiety, upewnij się, że zainstalowano [menedżera pakietów NuGet](https://docs.nuget.org/consume/installing-nuget).

Po uruchomieniu aplikacji przykładowej dane wyjściowe w konsoli będą wyglądać mniej więcej następująco. W czasie wykonywania nastąpi wstrzymanie operacji w momencie wyświetlenia komunikatu `Monitoring all tasks for 'Completed' state, timeout in 00:30:00...` podczas uruchamiania węzłów obliczeniowych puli. Zadania podrzędne zostaną umieszczone w kolejce do uruchomienia zaraz po uruchomieniu pierwszego węzła obliczeniowego. Przejdź do konta usługi Batch w [Azure Portal,](https://portal.azure.com) aby monitorować pulę, węzły obliczeniowe, zadanie i zadania podrzędne.

```
Sample start: 11/16/2018 4:02:54 PM

Container [input] created.
Uploading file taskdata0.txt to container [input]...
Uploading file taskdata1.txt to container [input]...
Uploading file taskdata2.txt to container [input]...
Creating pool [DotNetQuickstartPool]...
Creating job [DotNetQuickstartJob]...
Adding 3 tasks to job [DotNetQuickstartJob]...
Monitoring all tasks for 'Completed' state, timeout in 00:30:00...
```

Po zakończeniu zadań podrzędnych dla każdego z nich zostaną wyświetlone dane wyjściowe podobne do następujących:

```
Printing task output.
Task: Task0
Node: tvm-2850684224_3-20171205t000401z
Standard out:
Batch processing began with mainframe computers and punch cards. Today it still plays a central role in business, engineering, science, and other pursuits that require running lots of automated tasks....
stderr:
...
```

Typowy czas wykonywania wynosi mniej więcej 5 minut w przypadku uruchomienia aplikacji w konfiguracji domyślnej. Początkowa konfiguracja puli zajmuje najwięcej czasu. Aby ponownie uruchomić zadanie, usuń je z poprzedniego uruchomienia, ale nie usuwaj puli. We wstępnie skonfigurowanej puli zadanie zostanie ukończone w ciągu kilku sekund.

## <a name="review-the-code"></a>Przeglądanie kodu

Aplikacja platformy .NET omawiana w tym przewodniku Szybki start wykonuje następujące akcje:

- Przekazuje trzy małe pliki tekstowe do kontenera obiektów blob na koncie usługi Azure Storage. Te pliki to dane wejściowe do przetworzenia przez usługę Batch.
- Tworzy pulę węzłów obliczeniowych z systemem Windows Server.
- Tworzy zadanie i trzy zadania podrzędne do uruchomienia w tych węzłach. Każde zadanie podrzędne przetwarza jeden z plików wejściowych przy użyciu wiersza polecenia systemu Windows. 
- Wyświetla pliki zwrócone przez zadania podrzędne.

Aby uzyskać szczegółowe informacje, zapoznaj się z plikiem `Program.cs` i poniższymi sekcjami.

### <a name="preliminaries"></a>Akcje wstępne

Do interakcji z kontem magazynu aplikacja używa biblioteki klienta usługi Azure Storage dla platformy .NET. Tworzy ona odwołanie do konta za pomocą obiektu [CloudStorageAccount](/dotnet/api/microsoft.azure.storage.cloudstorageaccount) i przy jego użyciu tworzy obiekt [CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient).

```csharp
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```

Za pomocą odwołania `blobClient` aplikacja tworzy kontener w ramach konta magazynu i przekazuje pliki danych do kontenera. Pliki w magazynie są zdefiniowane jako obiekty [ResourceFile](/dotnet/api/microsoft.azure.batch.resourcefile) usługi Batch, które następnie mogą zostać pobrane przez tę usługę do węzłów obliczeniowych.

```csharp
List<string> inputFilePaths = new List<string>
{
    "taskdata0.txt",
    "taskdata1.txt",
    "taskdata2.txt"
};

List<ResourceFile> inputFiles = new List<ResourceFile>();

foreach (string filePath in inputFilePaths)
{
    inputFiles.Add(UploadFileToContainer(blobClient, inputContainerName, filePath));
}
```

Aplikacja tworzy obiekt [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient) w celu tworzenia pul, zadań i zadań podrzędnych w usłudze Batch i zarządzania nimi. Klient usługi Batch w przykładzie korzysta z uwierzytelniania za pomocą klucza wspólnego. (Usługa Batch obsługuje też uwierzytelnianie za pomocą usługi Azure Active Directory).

```csharp
BatchSharedKeyCredentials cred = new BatchSharedKeyCredentials(BatchAccountUrl, BatchAccountName, BatchAccountKey);

using (BatchClient batchClient = BatchClient.Open(cred))
...
```

### <a name="create-a-pool-of-compute-nodes"></a>Tworzenie puli węzłów obliczeniowych

Aby utworzyć pulę usługi Batch, aplikacja ustawia liczbę węzłów, rozmiar maszyny wirtualnej i konfigurację puli za pomocą metody [BatchClient.PoolOperations.CreatePool](/dotnet/api/microsoft.azure.batch.pooloperations.createpool). W tym przypadku obiekt [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) określa parametr [ImageReference](/dotnet/api/microsoft.azure.batch.imagereference) z odwołaniem do obrazu systemu Windows Server opublikowanego w witrynie Azure Marketplace. Usługa Batch obsługuje szeroki zakres obrazów systemów Linux i Windows Server pochodzących z witryny Azure Marketplace, a także niestandardowe obrazy maszyn wirtualnych.

Liczba węzłów (`PoolNodeCount`) i rozmiar maszyny wirtualnej (`PoolVMSize`) to zdefiniowane stałe. Domyślnie przykładowa aplikacja tworzy pulę składającą się z 2 węzłów o rozmiarze *Standard_A1_v2*. Sugerowany rozmiar oferuje dobry kompromis między wydajnością a kosztem na potrzeby tego krótkiego przykładu.

Metoda [Commit](/dotnet/api/microsoft.azure.batch.cloudpool.commit) przesyła pulę do usługi Batch.

```csharp

private static VirtualMachineConfiguration CreateVirtualMachineConfiguration(ImageReference imageReference)
{
    return new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: "batch.node.windows amd64");
}

private static ImageReference CreateImageReference()
{
    return new ImageReference(
        publisher: "MicrosoftWindowsServer",
        offer: "WindowsServer",
        sku: "2016-datacenter-smalldisk",
        version: "latest");
}

private static void CreateBatchPool(BatchClient batchClient, VirtualMachineConfiguration vmConfiguration)
{
    try
    {
        CloudPool pool = batchClient.PoolOperations.CreatePool(
            poolId: PoolId,
            targetDedicatedComputeNodes: PoolNodeCount,
            virtualMachineSize: PoolVMSize,
            virtualMachineConfiguration: vmConfiguration);

        pool.Commit();
    }
...

```

### <a name="create-a-batch-job"></a>Utworzenie zadania usługi Batch

Zadanie usługi Batch to logiczna grupa zawierająca co najmniej jedno zadanie podrzędne. Zadanie uwzględnia wspólne ustawienia zadań podrzędnych, takie jak priorytet i pula, w której zadania podrzędne mają być uruchamiane. Aplikacja tworzy zadanie w puli za pomocą metody [BatchClient.JobOperations.CreateJob](/dotnet/api/microsoft.azure.batch.joboperations.createjob).

Metoda [Commit](/dotnet/api/microsoft.azure.batch.cloudjob.commit) przesyła zadanie do usługi Batch. Początkowo zadanie nie zawiera zadań podrzędnych.

```csharp
try
{
    CloudJob job = batchClient.JobOperations.CreateJob();
    job.Id = JobId;
    job.PoolInformation = new PoolInformation { PoolId = PoolId };

    job.Commit();
}
...
```

### <a name="create-tasks"></a>Tworzenie zadań

Aplikacja tworzy listę obiektów [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask). Każde zadanie podrzędne przetwarza wejściowy obiekt `ResourceFile` przy użyciu właściwości [CommandLine](/dotnet/api/microsoft.azure.batch.cloudtask.commandline). W tym przykładzie wiersz polecenia uruchamia polecenie systemu Windows `type`, aby wyświetlić plik wejściowy. To polecenie to prosty przykład dla celów demonstracyjnych. Podczas korzystania z usługi Batch aplikację lub skrypt określa się w wierszu polecenia. Usługa Batch udostępnia kilka sposobów wdrażania aplikacji i skryptów w węzłach obliczeniowych.

Następnie aplikacja dodaje zadania podrzędne do zadania za pomocą metody [AddTask](/dotnet/api/microsoft.azure.batch.joboperations.addtask), która tworzy kolejkę zadań podrzędnych do uruchomienia w węzłach obliczeniowych.

```csharp
for (int i = 0; i < inputFiles.Count; i++)
{
    string taskId = String.Format("Task{0}", i);
    string inputFilename = inputFiles[i].FilePath;
    string taskCommandLine = String.Format("cmd /c type {0}", inputFilename);

    CloudTask task = new CloudTask(taskId, taskCommandLine);
    task.ResourceFiles = new List<ResourceFile> { inputFiles[i] };
    tasks.Add(task);
}

batchClient.JobOperations.AddTask(JobId, tasks);
```

### <a name="view-task-output"></a>Wyświetlanie danych wyjściowych zadania podrzędnego

Aplikacja tworzy obiekt [TaskStateMonitor](/dotnet/api/microsoft.azure.batch.taskstatemonitor) do monitorowania zadań podrzędnych, aby upewnić się, że zostaną one ukończone. Następnie za pomocą właściwości [CloudTask.ComputeNodeInformation](/dotnet/api/microsoft.azure.batch.cloudtask.computenodeinformation) aplikacja wyświetla plik `stdout.txt` generowany przez każde ukończone zadanie podrzędne. Po pomyślnym uruchomieniu zadania podrzędnego dane wyjściowe polecenia tego zadania zostaną zapisane w pliku `stdout.txt`:

```csharp
foreach (CloudTask task in completedtasks)
{
    string nodeId = String.Format(task.ComputeNodeInformation.ComputeNodeId);
    Console.WriteLine("Task: {0}", task.Id);
    Console.WriteLine("Node: {0}", nodeId);
    Console.WriteLine("Standard out:");
    Console.WriteLine(task.GetNodeFile(Constants.StandardOutFileName).ReadAsString());
}
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aplikacja automatycznie usuwa utworzony kontener magazynu, a także udostępnia opcję usunięcia puli i zadania usługi Batch. Opłaty za pulę są naliczane, dopóki węzły działają, nawet jeśli nie zostały zaplanowane żadne zadania. Gdy pula nie jest już potrzebna, usuń ją. W przypadku usunięcia puli usuwane są również wszystkie dane wyjściowe zadań podrzędnych w węzłach.

Gdy grupa zasobów, konto usługi Batch i konto magazynu nie będą już potrzebne, usuń je. W tym celu w witrynie Azure Portal zaznacz grupę zasobów konta usługi Batch i kliknij pozycję **Usuń grupę zasobów**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start uruchomiono niewielką aplikację utworzoną za pomocą interfejsu API platformy .NET usługi Batch w celu utworzenia puli i zadania usługi Batch. Zadanie uruchomiło przykładowe zadania podrzędne i pobrało dane wyjściowe utworzone w węzłach. Teraz, gdy już rozumiesz kluczowe pojęcia związane z usługą Batch, możesz wypróbować tę usługę z bardziej realistycznymi obciążeniami na większą skalę. Aby dowiedzieć się więcej o usłudze Azure Batch i zapoznać się z przewodnikiem dotyczącym obciążenia równoległego o rzeczywistym zastosowaniu, kontynuuj naukę w ramach samouczka platformy .NET usługi Batch.

> [!div class="nextstepaction"]
> [Przetwarzanie obciążenia równoległego za pomocą platformy .NET](tutorial-parallel-dotnet.md)
