---
title: Uruchamianie aplikacji MPI przy użyciu zadań o wielu wystąpieniach
description: Dowiedz się, jak wykonywać Message Passing Interface (MPI) przy użyciu typu zadania o wielu wystąpieniach w Azure Batch.
ms.topic: how-to
ms.date: 04/13/2021
ms.openlocfilehash: e96cfb89b186d69f6ad969949b8df609956114d2
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389404"
---
# <a name="use-multi-instance-tasks-to-run-message-passing-interface-mpi-applications-in-batch"></a>Używanie zadań o wielu wystąpieniach do uruchamiania Message Passing Interface (MPI) w umacie Batch

Zadania o wielu wystąpieniach umożliwiają uruchamianie zadania podrzędnego Azure Batch w wielu węzłach obliczeniowych jednocześnie. Te zadania umożliwiają scenariusze obliczeń o wysokiej wydajności, takie Message Passing Interface (MPI) w umacie Batch. Z tego artykułu dowiesz się, jak wykonywać zadania o wielu wystąpieniach przy użyciu [biblioteki .NET usługi Batch.](/dotnet/api/microsoft.azure.batch)

> [!NOTE]
> Chociaż przykłady w tym artykule koncentrują się na węzłach obliczeniowych usługi Batch .NET, MS-MPI i Windows, omówione tutaj pojęcia dotyczące zadań o wielu wystąpieniach mają zastosowanie do innych platform i technologii (na przykład Python i Intel MPI w węzłach systemu Linux).

## <a name="multi-instance-task-overview"></a>Omówienie zadań o wielu wystąpieniach

W usłudze Batch każde zadanie jest zwykle wykonywane w jednym węźle obliczeniowym — przesyłasz wiele zadań do zadania, a usługa Batch planuje wykonanie każdego zadania w węźle. Jednak konfigurując ustawienia wielu wystąpień zadania podrzędnego, należy poinformować usługę Batch, aby zamiast tego tworzyła jedno zadanie podstawowe i kilka podzadań, które są następnie wykonywane w wielu węzłach.

:::image type="content" source="media/batch-mpi/batch-mpi-01.png" alt-text="Diagram przedstawiający przegląd ustawień z wieloma wystąpieniami.":::

Podczas przesyłania zadania z ustawieniami wielu wystąpień do zadania usługa Batch wykonuje kilka kroków unikatowych dla zadań o wielu wystąpieniach:

1. Usługa Batch tworzy jeden **podstawowy i** kilka **podzadani** na podstawie ustawień wielu wystąpień. Łączna liczba zadań podrzędnych (podstawowych i wszystkich podzadań) odpowiada liczbie wystąpień **(węzłów** obliczeniowych) skojarzynych z ustawieniami wielu wystąpień.
2. Usługa Batch wyznacza jeden z węzłów obliczeniowych jako **główny** i planuje wykonanie podstawowego zadania nadrzędnego na wzorcu. Planuje wykonywanie podzadań w pozostałej części węzłów obliczeniowych przydzielonych do zadania o wielu wystąpieniach— po jednym podzadaniu na węzeł.
3. Podstawowe i wszystkie podzadnia pobierają wszystkie typowe pliki **zasobów określone** w ustawieniach wielu wystąpień.
4. Po pobraniu wspólnych plików zasobów zadania podstawowe i  podzadaży wykonują polecenie koordynacji określone w ustawieniach wielu wystąpień. Polecenie koordynacji jest zwykle używane do przygotowania węzłów do wykonania zadania. Może to obejmować uruchamianie usług w tle (takich jak [microsoft MPI)](/message-passing-interface/microsoft-mpi) i sprawdzanie, czy węzły są gotowe do `smpd.exe` przetwarzania komunikatów między węzłami.
5. Zadanie podstawowe wykonuje  polecenie aplikacji w  węźle głównym po pomyślnym ukończeniu polecenia koordynacji przez podstawowy i wszystkie podzadnia. Polecenie aplikacji jest wierszem polecenia zadania o wielu wystąpieniach i jest wykonywane tylko przez zadanie podstawowe. W [rozwiązaniu opartym na pliku MS-MPI](/message-passing-interface/microsoft-mpi) jest to miejsce, w którym aplikacja z obsługą mpi jest wykonywana przy użyciu polecenia `mpiexec.exe` .

> [!NOTE]
> Chociaż jest funkcjonalnie odrębny, "zadanie o wielu wystąpieniach" nie jest unikatowym typem zadania, takiego jak [StartTask](/dotnet/api/microsoft.azure.batch.starttask) lub [JobPreparationTask.](/dotnet/api/microsoft.azure.batch.jobpreparationtask) Zadanie o wielu wystąpieniach jest po prostu standardowym zadaniem usługi Batch[(CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) na platformie .NET usługi Batch), którego ustawienia wielu wystąpień zostały skonfigurowane. W tym artykule o tym mówimy jako o zadaniu **o wielu wystąpieniach.**

## <a name="requirements-for-multi-instance-tasks"></a>Wymagania dotyczące zadań o wielu wystąpieniach

Zadania z wieloma wystąpieniami wymagają puli z włączoną komunikacją między **węzłami** oraz z **wyłączonym współbieżną obsługą zadań.** Aby wyłączyć współbieżne wykonywanie zadań, ustaw [właściwość CloudPool.TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) na wartość 1.

> [!NOTE]
> Usługa [](batch-quota-limit.md#pool-size-limits) Batch ogranicza rozmiar puli z włączoną komunikacją między węzłami.

Ten fragment kodu przedstawia sposób tworzenia puli dla zadań o wielu wystąpieniach przy użyciu biblioteki usługi Batch dla programu .NET.

```csharp
CloudPool myCloudPool =
    myBatchClient.PoolOperations.CreatePool(
        poolId: "MultiInstanceSamplePool",
        targetDedicatedComputeNodes: 3
        virtualMachineSize: "standard_d1_v2",
        VirtualMachineConfiguration: new VirtualMachineConfiguration(
        imageReference: new ImageReference(
                        publisher: "MicrosoftWindowsServer",
                        offer: "WindowsServer",
                        sku: "2019-datacenter-core",
                        version: "latest"),
        nodeAgentSkuId: "batch.node.windows amd64");

// Multi-instance tasks require inter-node communication, and those nodes
// must run only one task at a time.
myCloudPool.InterComputeNodeCommunicationEnabled = true;
myCloudPool.TaskSlotsPerNode = 1;
```

> [!NOTE]
> Jeśli spróbujesz uruchomić zadanie o wielu wystąpieniach w puli z wyłączoną komunikacją międzywęzową lub z wartością *taskSlotsPerNode* większą niż 1, zadanie nigdy nie zostanie zaplanowane — pozostanie w stanie "aktywny" na czas nieokreślony.

### <a name="use-a-starttask-to-install-mpi"></a>Instalowanie pakietu MPI za pomocą funkcji StartTask

Aby uruchamiać aplikacje MPI z zadaniem o wielu wystąpieniach, należy najpierw zainstalować implementację MPI (na przykład MS-MPI lub Intel MPI) w węzłach obliczeniowych w puli. Jest to dobry czas na użycie funkcji [StartTask,](/dotnet/api/microsoft.azure.batch.starttask)która jest wykonywana za każdym razem, gdy węzeł dołącza do puli lub jest uruchamiany ponownie. Ten fragment kodu tworzy pakiet StartTask określający pakiet instalacyjny MS-MPI jako [plik zasobów](/dotnet/api/microsoft.azure.batch.resourcefile). Wiersz polecenia zadania uruchamiania jest wykonywany po pobraniu pliku zasobu do węzła. W takim przypadku wiersz polecenia wykonuje nienadzorowane instalowanie pliku MS-MPI.

```csharp
// Create a StartTask for the pool which we use for installing MS-MPI on
// the nodes as they join the pool (or when they are restarted).
StartTask startTask = new StartTask
{
    CommandLine = "cmd /c MSMpiSetup.exe -unattend -force",
    ResourceFiles = new List<ResourceFile> { new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MSMpiSetup.exe", "MSMpiSetup.exe") },
    UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin)),
    WaitForSuccess = true
};
myCloudPool.StartTask = startTask;

// Commit the fully configured pool to the Batch service to actually create
// the pool and its compute nodes.
await myCloudPool.CommitAsync();
```

### <a name="remote-direct-memory-access-rdma"></a>Zdalny bezpośredni dostęp do pamięci (RDMA)

Po wybraniu rozmiaru z [możliwością RDMA,](../virtual-machines/sizes-hpc.md?toc=/azure/virtual-machines/windows/toc.json) takiego jak A9 dla węzłów obliczeniowych w puli usługi Batch, aplikacja MPI może korzystać z wysokiej wydajności i małych opóźnień sieci zdalnego bezpośredniego dostępu do pamięci (RDMA) platformy Azure.

Poszukaj rozmiarów określonych jako "Z możliwością RDMA" w tece Sizes for virtual machines in Azure (for VirtualMachineConfiguration pools) (Rozmiary maszyn wirtualnych na platformie [Azure)](../virtual-machines/sizes.md) lub [Sizes for Cloud Services](../cloud-services/cloud-services-sizes-specs.md) (for CloudServicesConfiguration pools) (Rozmiary dla puli Cloud Services CloudServicesConfiguration).

> [!NOTE]
> Aby korzystać z funkcji RDMA w [węzłach obliczeniowych systemu Linux,](batch-linux-nodes.md)należy użyć węzły **Intel MPI.**

## <a name="create-a-multi-instance-task-with-batch-net"></a>Tworzenie zadania o wielu wystąpieniach za pomocą usługi Batch na platformie .NET

Teraz, gdy zostały już spełnione wymagania dotyczące puli i instalacja pakietu MPI, utwórzmy zadanie o wielu wystąpieniach. W tym fragmencie kodu utworzymy standardową właściwość [CloudTask,](/dotnet/api/microsoft.azure.batch.cloudtask)a następnie skonfigurujemy jej [właściwość MultiInstanceSettings.](/dotnet/api/microsoft.azure.batch.cloudtask) Jak wspomniano wcześniej, zadanie o wielu wystąpieniach nie jest odrębnym typem zadania, ale standardowym zadaniem usługi Batch skonfigurowanym z ustawieniami wielu wystąpień.

```csharp
// Create the multi-instance task. Its command line is the "application command"
// and will be executed *only* by the primary, and only after the primary and
// subtasks execute the CoordinationCommandLine.
CloudTask myMultiInstanceTask = new CloudTask(id: "mymultiinstancetask",
    commandline: "cmd /c mpiexec.exe -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe");

// Configure the task's MultiInstanceSettings. The CoordinationCommandLine will be executed by
// the primary and all subtasks.
myMultiInstanceTask.MultiInstanceSettings =
    new MultiInstanceSettings(numberOfNodes) {
    CoordinationCommandLine = @"cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d",
    CommonResourceFiles = new List<ResourceFile> {
    new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MyMPIApplication.exe",
                     "MyMPIApplication.exe")
    }
};

// Submit the task to the job. Batch will take care of splitting it into subtasks and
// scheduling them for execution on the nodes.
await myBatchClient.JobOperations.AddTaskAsync("mybatchjob", myMultiInstanceTask);
```

## <a name="primary-task-and-subtasks"></a>Zadanie podstawowe i podzadnia

Podczas tworzenia ustawień dla zadania o wielu wystąpieniach określana jest liczba węzłów obliczeniowych, które mają być wykonywane. Po przesłaniu zadania podrzędnego do zadania  usługa Batch  tworzy jedno zadanie podstawowe i wystarczającą liczbę podzadań, które razem pasują do określonej liczby węzłów.

Do tych zadań jest przypisywany identyfikator liczby całkowitej z zakresu od 0 do *numberOfInstances* - 1. Zadanie o identyfikatorze 0 jest zadaniem podstawowym, a wszystkie pozostałe identyfikatory są podzadaniami. Jeśli na przykład utworzysz następujące ustawienia wielu wystąpień dla zadania podrzędnego, zadanie podstawowe będzie miało identyfikator 0, a podzadnia będą miały identyfikatory od 1 do 9.

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

### <a name="master-node"></a>Węzeł główny

Podczas przesyłania zadania o wielu wystąpieniach usługa Batch wyznacza jeden z węzłów obliczeniowych jako węzeł "główny" i planuje wykonanie zadania podstawowego w węźle głównym. Podzadnia są zaplanowane do wykonania w pozostałych węzłach przydzielonych do zadania o wielu wystąpieniach.

## <a name="coordination-command"></a>Polecenie koordynacji

Polecenie **koordynacji jest** wykonywane zarówno przez podzadaniom podstawowym, jak i podrzędnym.

Wywołania polecenia koordynacji jest blokujące — usługa Batch nie wykonuje polecenia aplikacji, dopóki polecenie koordynacji nie zostanie pomyślnie zwrócone dla wszystkich podzadaniów. W związku z tym polecenie koordynacji powinno uruchomić wszystkie wymagane usługi w tle, sprawdzić, czy są gotowe do użycia, a następnie zakończyć działanie. Na przykład to polecenie koordynacji dla rozwiązania korzystającego z ms-MPI w wersji 7 uruchamia usługę SMPD w węźle, a następnie kończy działanie:

`cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d`

Zwróć uwagę na `start` użycie w tym poleceniu koordynacji . Jest to wymagane, ponieważ `smpd.exe` aplikacja nie jest zwracana natychmiast po wykonaniu. Bez użycia polecenia start to polecenie koordynacji nie zwróci i w związku z tym zablokowałoby uruchomienie polecenia aplikacji.

## <a name="application-command"></a>Application , polecenie

Po zakończeniu wykonywania polecenia koordynacji zadania podstawowego i wszystkich podzadań wiersz polecenia zadania podrzędnego z wieloma wystąpieniami jest wykonywany tylko przez zadanie *podstawowe*. Nazywamy to **poleceniem aplikacji,** aby odróżnić je od polecenia koordynacji.

W przypadku aplikacji MS-MPI użyj polecenia aplikacji, aby wykonać aplikację z obsługą mpi za pomocą polecenia `mpiexec.exe` . Na przykład poniżej znajduje się polecenie aplikacji dla rozwiązania korzystającego z pliku MS-MPI w wersji 7:

`cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe`

> [!NOTE]
> Ponieważ plik MS-MPI domyślnie używa zmiennej (zobacz Zmienne środowiskowe), przykładowy wiersz polecenia aplikacji `mpiexec.exe` `CCP_NODES` powyżej wyklucza ją. [](#environment-variables)

## <a name="environment-variables"></a>Zmienne środowiskowe

Usługa Batch tworzy kilka [zmiennych środowiskowych](batch-compute-node-environment-variables.md) specyficznych dla zadań o wielu wystąpieniach w węzłach obliczeniowych przydzielonych do zadania o wielu wystąpieniach. Polecenia koordynacji i aplikacji mogą odwoływać się do tych zmiennych środowiskowych, podobnie jak skrypty i programy, które wykonują.

Usługa Batch tworzy następujące zmienne środowiskowe do użycia przez zadania o wielu wystąpieniach:

- `CCP_NODES`
- `AZ_BATCH_NODE_LIST`
- `AZ_BATCH_HOST_LIST`
- `AZ_BATCH_MASTER_NODE`
- `AZ_BATCH_TASK_SHARED_DIR`
- `AZ_BATCH_IS_CURRENT_NODE_MASTER`

Aby uzyskać szczegółowe informacje na temat tych i innych zmiennych środowiska węzła obliczeniowego usługi Batch, w tym ich zawartość i widoczność, zobacz Zmienne środowiskowe węzła [obliczeniowego](batch-compute-node-environment-variables.md).

> [!TIP]
> Przykładowy [kod MPI dla](https://github.com/Azure-Samples/azure-batch-samples/tree/master/Python/Batch/article_samples/mpi) systemu Linux dla usługi Batch zawiera przykład sposobu, w jaki można użyć kilku z tych zmiennych środowiskowych.

## <a name="resource-files"></a>Pliki zasobów

Istnieją dwa zestawy plików zasobów, które należy wziąć pod  uwagę w przypadku zadań o wielu wystąpieniach: typowe pliki zasobów pobierane przez  wszystkie zadania podrzędne (zarówno podstawowe, jak i podrzędne) oraz pliki zasobów określone dla zadania o wielu wystąpieniach, które pobiera tylko zadanie podstawowe.  

W ustawieniach wielu  wystąpień zadania można określić co najmniej jeden wspólny plik zasobów. Te typowe pliki zasobów są pobierane z  [usługi Azure Storage](../storage/common/storage-introduction.md) do udostępnionego katalogu zadań poszczególnych węzłów przez podstawowy i wszystkie podzadnia. Dostęp do udostępnionego katalogu zadań można uzyskać z aplikacji i wiersza polecenia koordynacji przy użyciu zmiennej `AZ_BATCH_TASK_SHARED_DIR` środowiskowej . Ścieżka jest taka sama w każdym węźle przydzielonym do zadania o wielu wystąpieniach, w związku z tym można współdzielić jedno polecenie koordynacji między podstawowym i `AZ_BATCH_TASK_SHARED_DIR` wszystkimi podzadaniami. Usługa Batch nie "udostępnia" katalogu w sensie dostępu zdalnego, ale można go użyć jako punktu instalacji lub udostępniania, jak wspomniano wcześniej w wierzchołku zmiennych środowiskowych.

Pliki zasobów określone dla zadania o wielu wystąpieniach są domyślnie pobierane do katalogu roboczego `AZ_BATCH_TASK_WORKING_DIR` zadania. Jak wspomniano, w przeciwieństwie do typowych plików zasobów tylko zadanie podstawowe pobiera pliki zasobów określone dla samego zadania o wielu wystąpieniach.

> [!IMPORTANT]
> Zawsze używaj zmiennych środowiskowych i , aby odwoływać się do `AZ_BATCH_TASK_SHARED_DIR` tych `AZ_BATCH_TASK_WORKING_DIR` katalogów w wierszach polecenia. Nie należy próbować konstruować ścieżek ręcznie.

## <a name="task-lifetime"></a>Okres istnienia zadania

Okres istnienia zadania podstawowego kontroluje okres istnienia całego zadania o wielu wystąpieniach. Gdy podstawowy kończy działanie, wszystkie podzadań są przerywane. Kod zakończenia podstawowego jest kodem zakończenia zadania i dlatego jest używany do określenia powodzenia lub niepowodzenia zadania do celów ponawiania.

Jeśli którykolwiek z podzadań zakończy się niepowodzeniem, zakończenie z kodem zwracanym innym niż zero, na przykład całe zadanie podrzędne o wielu wystąpieniach kończy się niepowodzeniem. Zadanie o wielu wystąpieniach jest następnie przerywane i ponawiane do limitu ponawiania.

Po usunięciu zadania o wielu wystąpieniach podstawowe i wszystkie podzadnia są również usuwane przez usługę Batch. Wszystkie katalogi podzadaniowe i ich pliki są usuwane z węzłów obliczeniowych, tak jak w przypadku zadania standardowego.

[TaskConstraints](/dotnet/api/microsoft.azure.batch.taskconstraints) dla zadania o wielu wystąpieniach, takich jak [Właściwości MaxTaskRetryCount,](/dotnet/api/microsoft.azure.batch.taskconstraints.maxtaskretrycount) [MaxWallClockTime](/dotnet/api/microsoft.azure.batch.taskconstraints.maxwallclocktime)i [RetentionTime,](/dotnet/api/microsoft.azure.batch.taskconstraints.retentiontime) są honorowane jako zadania standardowe i mają zastosowanie do podzadaniów podstawowych i wszystkich. Jeśli jednak zmienisz właściwośćRetentionTime po dodaniu zadania podrzędnego z wieloma wystąpieniami do zadania, ta zmiana zostanie zastosowana tylko do zadania podstawowego, a wszystkie podzadanie nadal będą używać oryginalnej wartości RetentionTime.

Lista ostatnich zadań węzła obliczeniowego odzwierciedla identyfikator podzadaku, jeśli ostatnie zadanie było częścią zadania o wielu wystąpieniach.

## <a name="obtain-information-about-subtasks"></a>Uzyskiwanie informacji o podzadaniach

Aby uzyskać informacje o podzadaniach przy użyciu biblioteki .NET usługi Batch, wywołaj metodę [CloudTask.ListSubtasks.](/dotnet/api/microsoft.azure.batch.cloudtask.listsubtasks) Ta metoda zwraca informacje o wszystkich podzadaniach oraz informacje o węźle obliczeniowym, który wykonał zadania podrzędne. Na podstawie tych informacji można określić katalog główny każdego podzada, identyfikator puli, jej bieżący stan, kod zakończenia i inne. Tych informacji można użyć w połączeniu z metodą [PoolOperations.GetNodeFile](/dotnet/api/microsoft.azure.batch.pooloperations.getnodefile) w celu uzyskania plików podzadaniowych. Należy pamiętać, że ta metoda nie zwraca informacji o zadaniu podstawowym (identyfikator 0).

> [!NOTE]
> O ile nie określono inaczej, metody platformy .NET usługi Batch, które działają na wielu wystąpieniach [CloudTask,](/dotnet/api/microsoft.azure.batch.cloudtask) mają *zastosowanie tylko* do zadania podstawowego. Na przykład po wywołaniu metody [CloudTask.ListNodeFiles](/dotnet/api/microsoft.azure.batch.cloudtask.listnodefiles) w zadaniu o wielu wystąpieniach są zwracane tylko pliki zadania podstawowego.

Poniższy fragment kodu przedstawia sposób uzyskiwania informacji o podzadaniu, a także żądania zawartości pliku z węzłów, na których zostały wykonane.

```csharp
// Obtain the job and the multi-instance task from the Batch service
CloudJob boundJob = batchClient.JobOperations.GetJob("mybatchjob");
CloudTask myMultiInstanceTask = boundJob.GetTask("mymultiinstancetask");

// Now obtain the list of subtasks for the task
IPagedEnumerable<SubtaskInformation> subtasks = myMultiInstanceTask.ListSubtasks();

// Asynchronously iterate over the subtasks and print their stdout and stderr
// output if the subtask has completed
await subtasks.ForEachAsync(async (subtask) =>
{
    Console.WriteLine("subtask: {0}", subtask.Id);
    Console.WriteLine("exit code: {0}", subtask.ExitCode);

    if (subtask.State == SubtaskState.Completed)
    {
        ComputeNode node =
            await batchClient.PoolOperations.GetComputeNodeAsync(subtask.ComputeNodeInformation.PoolId,
                                                                 subtask.ComputeNodeInformation.ComputeNodeId);

        NodeFile stdOutFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardOutFileName);
        NodeFile stdErrFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardErrorFileName);
        stdOut = await stdOutFile.ReadAsStringAsync();
        stdErr = await stdErrFile.ReadAsStringAsync();

        Console.WriteLine("node: {0}:", node.Id);
        Console.WriteLine("stdout.txt: {0}", stdOut);
        Console.WriteLine("stderr.txt: {0}", stdErr);
    }
    else
    {
        Console.WriteLine("\tSubtask {0} is in state {1}", subtask.Id, subtask.State);
    }
});
```

## <a name="code-sample"></a>Przykład kodu

Przykładowy [kod MultiInstanceTasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks) w usłudze GitHub przedstawia sposób użycia zadania o wielu wystąpieniach do uruchomienia aplikacji [MS-MPI](/message-passing-interface/microsoft-mpi) w węzłach obliczeniowych usługi Batch. Wykonaj poniższe kroki, aby uruchomić przykład.

### <a name="preparation"></a>Przygotowanie

1. Pobierz zestaw [SDK MS-MPI i instalatory redystora](/message-passing-interface/microsoft-mpi) i zainstaluj je. Po instalacji możesz sprawdzić, czy zmienne środowiskowe MS-MPI zostały ustawione.
1. *Skompilowanie wersji* wydania przykładowego [programu MPIHelloWorld.](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks/MPIHelloWorld) Jest to program, który będzie uruchamiany w węzłach obliczeniowych przez zadanie o wielu wystąpieniach.
1. Utwórz plik zip zawierający `MPIHelloWorld.exe` pliki (które zostały wbudowane w kroku 2) i `MSMpiSetup.exe` (pobrane w kroku 1). W następnym kroku przekażesz ten plik zip jako pakiet aplikacji.
1. Użyj pliku [Azure Portal,](https://portal.azure.com) aby utworzyć aplikację usługi [Batch](batch-application-packages.md) o nazwie "MPIHelloWorld" i określić plik zip utworzony w poprzednim kroku jako wersję "1.0" pakietu aplikacji. Aby [uzyskać więcej informacji,](batch-application-packages.md#upload-and-manage-applications) zobacz Przekazywanie aplikacji i zarządzanie nimi.

> [!TIP]
> Tworzenie *wersji* wydania programu gwarantuje, że nie trzeba uwzględniać żadnych dodatkowych zależności (na przykład `MPIHelloWorld.exe` lub ) w `msvcp140d.dll` `vcruntime140d.dll` pakiecie aplikacji.

### <a name="execution"></a>Wykonanie

1. Pobierz plik [zip azure-batch-samples z](https://github.com/Azure/azure-batch-samples/archive/master.zip) usługi GitHub.
1. Otwórz rozwiązanie MultiInstanceTasks **w** Visual Studio 2019 r. Plik `MultiInstanceTasks.sln` rozwiązania znajduje się w:

    `azure-batch-samples\CSharp\ArticleProjects\MultiInstanceTasks\`
1. Wprowadź poświadczenia konta usługi Batch i Storage wMicrosoft.Azure.Bat`AccountSettings.settings` **ch.Samples.Common.**
1. **Skompilowanie i uruchomienie** rozwiązania MultiInstanceTasks w celu wykonania przykładowej aplikacji MPI w węzłach obliczeniowych w puli usługi Batch.
1. *Opcjonalnie:* [](https://portal.azure.com) użyj Azure Portal lub [Batch Explorer,](https://azure.github.io/BatchExplorer/) aby sprawdzić przykładową pulę, zadanie i zadanie ("MultiInstanceSamplePool", "MultiInstanceSampleJob", "MultiInstanceSampleTask") przed usunięciem zasobów.

> [!TIP]
> Możesz pobrać [Visual Studio Community](https://visualstudio.microsoft.com/vs/community/) bezpłatnie, jeśli nie masz jeszcze Visual Studio.

Dane wyjściowe `MultiInstanceTasks.exe` z są podobne do następujących:

```
Creating pool [MultiInstanceSamplePool]...
Creating job [MultiInstanceSampleJob]...
Adding task [MultiInstanceSampleTask] to job [MultiInstanceSampleJob]...
Awaiting task completion, timeout in 00:30:00...

Main task [MultiInstanceSampleTask] is in state [Completed] and ran on compute node [tvm-1219235766_1-20161017t162002z]:
---- stdout.txt ----
Rank 2 received string "Hello world" from Rank 0
Rank 1 received string "Hello world" from Rank 0

---- stderr.txt ----

Main task completed, waiting 00:00:10 for subtasks to complete...

---- Subtask information ----
subtask: 1
        exit code: 0
        node: tvm-1219235766_3-20161017t162002z
        stdout.txt:
        stderr.txt:
subtask: 2
        exit code: 0
        node: tvm-1219235766_2-20161017t162002z
        stdout.txt:
        stderr.txt:

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## <a name="next-steps"></a>Następne kroki

- Przeczytaj więcej na [temat obsługi mpi dla systemu Linux na Azure Batch](https://docs.microsoft.com/archive/blogs/windowshpc/introducing-mpi-support-for-linux-on-azure-batch).
- Dowiedz się, jak [tworzyć pule węzłów obliczeniowych systemu Linux](batch-linux-nodes.md) do użycia w Azure Batch MPI.
