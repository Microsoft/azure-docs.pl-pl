---
title: Używanie zadań o wiele wystąpień do uruchamiania aplikacji MPI
description: Dowiedz się, jak wykonywać aplikacje MPI (Message Passing Interface) za pomocą typu zadania o wiele wystąpień w Azure Batch.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 51fc580e0bb31e0e975c53b44887a5889a784eea
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605675"
---
# <a name="use-multi-instance-tasks-to-run-message-passing-interface-mpi-applications-in-batch"></a>Używanie zadań o wiele wystąpień do uruchamiania aplikacji interfejsu przekazywania komunikatów (MPI) w usłudze Batch

Zadania z wieloma wystąpieniami umożliwiają jednoczesne uruchamianie zadania Azure Batch w wielu węzłach obliczeniowych. Te zadania umożliwiają tworzenie scenariuszy obliczeniowych o wysokiej wydajności, takich jak aplikacje MPI (Message Passing Interface) w usłudze Batch. W tym artykule dowiesz się, jak wykonywać zadania o wiele wystąpień przy użyciu biblioteki usługi [Batch .NET](/dotnet/api/microsoft.azure.batch) .

> [!NOTE]
> Mimo że przykłady w tym artykule koncentrują się na węzłach obliczeniowych usługi Batch .NET, MS-MPI i Windows, koncepcje zadań o wiele wystąpień omówione tutaj mają zastosowanie do innych platform i technologii (na przykład w językach Python i Intel MPI w systemie Linux).

## <a name="multi-instance-task-overview"></a>Przegląd zadań o wiele wystąpień

W usłudze Batch każde zadanie jest zwykle wykonywane w jednym węźle obliczeniowym — do zadania są przesyłane wiele zadań, a usługa Batch planuje każde zadanie do wykonania w węźle. Jednak konfigurując **Ustawienia wielu wystąpień** zadania, należy powiedzieć usłudze Batch, aby zamiast tego utworzyć jedno zadanie podstawowe i kilka podzadań, które są następnie wykonywane na wielu węzłach.

:::image type="content" source="media/batch-mpi/batch_mpi_01.png" alt-text="Diagram przedstawiający przegląd ustawień o kilku wystąpieniach.":::

Po przesłaniu zadania z ustawieniami z wieloma wystąpieniami do zadania usługi Batch wykonuje kilka czynności unikatowych dla zadań z wieloma wystąpieniami:

1. Usługa Batch tworzy jedno **podstawowe** i kilka **podzadań** w oparciu o ustawienia wielu wystąpień. Całkowita liczba zadań (podstawowa i wszystkie podzadania) odpowiada liczbie **wystąpień** (węzłów obliczeniowych) określonych w ustawieniach wielu wystąpień.
2. Partia określa jeden z węzłów obliczeniowych jako **wzorzec** i planuje wykonywanie zadania podstawowego na wzorcu. Planuje wykonywanie podzadań w pozostałej części węzłów obliczeniowych przypisywanych do zadania o wielu wystąpieniach, jednego podzadania na węzeł.
3. Podstawowe i wszystkie podzadania pobierają wszystkie **pliki zasobów** , które są określone w ustawieniach z obsługą wiele wystąpień.
4. Po pobraniu wspólnych plików zasobów podstawowe i podrzędne zadania wykonują **polecenie koordynacyjne** określone w ustawieniach z obsługą wiele wystąpień. Polecenie koordynacyjne jest zwykle używane do przygotowywania węzłów do wykonania zadania. Może to obejmować uruchamianie usług w tle (takich jak [Microsoft MPI](/message-passing-interface/microsoft-mpi) `smpd.exe` ) i sprawdzanie, czy węzły są gotowe do przetwarzania komunikatów między węzłami.
5. Zadanie podstawowe wykonuje **polecenie aplikacji** w węźle głównym *po* pomyślnym wykonaniu polecenia koordynacji przez podstawowe i wszystkie podzadania. Polecenie aplikacji jest wierszem polecenia samego zadania o wiele wystąpień i jest wykonywane tylko przez zadanie główne. W rozwiązaniu opartym na [MS-MPI](/message-passing-interface/microsoft-mpi) to miejsce, w którym uruchamiasz aplikację z obsługą MPI przy użyciu programu `mpiexec.exe` .

> [!NOTE]
> Chociaż jest to funkcjonalne odrębnie, "zadanie o wiele wystąpień" nie jest unikatowym typem zadania, takim jak [StartTask](/dotnet/api/microsoft.azure.batch.starttask) lub [JobPreparationTask](/dotnet/api/microsoft.azure.batch.jobpreparationtask). Zadanie wielowystąpienie to po prostu standardowe zadanie wsadowe ([CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) w usłudze Batch .NET), dla którego skonfigurowano wiele wystąpień. W tym artykule nazywamy to **zadanie o wiele wystąpień**.

## <a name="requirements-for-multi-instance-tasks"></a>Wymagania dotyczące zadań o wiele wystąpień

Zadania z wielowystąpieniem wymagają puli z **włączoną funkcją komunikacji między węzłami** i **współbieżnego wykonywania zadań**. Aby wyłączyć współbieżne wykonywanie zadań, ustaw właściwość [CloudPool. TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) na 1.

> [!NOTE]
> Partia zadań [ogranicza](batch-quota-limit.md#pool-size-limits) rozmiar puli, w której włączono komunikację między węzłami.

Ten fragment kodu przedstawia sposób tworzenia puli dla zadań z wielowystąpieniami przy użyciu biblioteki usługi Batch platformy .NET.

```csharp
CloudPool myCloudPool =
    myBatchClient.PoolOperations.CreatePool(
        poolId: "MultiInstanceSamplePool",
        targetDedicatedComputeNodes: 3
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

// Multi-instance tasks require inter-node communication, and those nodes
// must run only one task at a time.
myCloudPool.InterComputeNodeCommunicationEnabled = true;
myCloudPool.TaskSlotsPerNode = 1;
```

> [!NOTE]
> Jeśli spróbujesz uruchomić zadanie o wielu wystąpieniach w puli z wyłączoną łącznością międzywęzłową lub z *taskSlotsPerNode* wartością większą niż 1, zadanie nigdy nie zostanie zaplanowane — pozostaje nieokreślony w stanie "aktywny".

### <a name="use-a-starttask-to-install-mpi"></a>Instalowanie MPI przy użyciu StartTask

Aby uruchamiać aplikacje MPI przy użyciu zadania o kilku wystąpieniach, należy najpierw zainstalować implementację MPI (na przykład MS-MPI lub Intel MPI) w węzłach obliczeniowych w puli. Jest to dobry moment, aby użyć [StartTask](/dotnet/api/microsoft.azure.batch.starttask), który jest wykonywany za każdym razem, gdy węzeł dołącza do puli lub jest uruchamiany ponownie. Ten fragment kodu tworzy StartTask, który określa pakiet instalacyjny MS-MPI jako [plik zasobów](/dotnet/api/microsoft.azure.batch.resourcefile). Wiersz polecenia zadania podrzędnego uruchamiania jest wykonywany po pobraniu pliku zasobów do węzła. W takim przypadku wiersz polecenia wykonuje instalację nienadzorowaną MS-MPI.

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

W przypadku wybrania [rozmiaru z obsługą funkcji RDMA](../virtual-machines/sizes-hpc.md?toc=/azure/virtual-machines/windows/toc.json) , takiego jak A9 dla węzłów obliczeniowych w puli usługi Batch, aplikacja MPI może korzystać z wysokiej wydajności sieci zdalnej bezpośredniego dostępu do pamięci (RDMA) na platformie Azure.

W obszarze [rozmiary maszyn wirtualnych na platformie Azure](../virtual-machines/sizes.md) (dla pul VirtualMachineConfiguration) lub [rozmiary dla Cloud Services](../cloud-services/cloud-services-sizes-specs.md) (dla pul CloudServicesConfiguration) należy szukać rozmiarów określonych jako "RDMA.

> [!NOTE]
> Aby skorzystać z funkcji RDMA w [węzłach obliczeniowych systemu Linux](batch-linux-nodes.md), należy użyć klasy **Intel MPI** na węzłach.

## <a name="create-a-multi-instance-task-with-batch-net"></a>Tworzenie zadania o kilku wystąpieniach za pomocą usługi Batch .NET

Teraz, gdy zostały omówione wymagania dotyczące puli i instalacji pakietu MPI, Utwórzmy zadanie o wiele wystąpień. W tym fragmencie kodu utworzysz standardową [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask), a następnie skonfigurujesz jej właściwość [MultiInstanceSettings](/dotnet/api/microsoft.azure.batch.cloudtask) . Jak wspomniano wcześniej, zadanie wielowystąpienie nie jest odrębnym typem zadania, ale standardowe zadanie wsadowe skonfigurowano z użyciem ustawień o kilku wystąpieniach.

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

## <a name="primary-task-and-subtasks"></a>Zadanie podstawowe i podzadania

Podczas tworzenia ustawień wielu wystąpień zadania należy określić liczbę węzłów obliczeniowych, które mają wykonać zadanie. Po przesłaniu zadania do zadania usługa Batch tworzy jedno zadanie **podstawowe** i wystarczającą ilość **podzadań** , które razem są zgodne z liczbą określonych węzłów.

Do tych zadań są przypisywane identyfikatory całkowite z zakresu od 0 do *numberOfInstances* -1. Zadanie o IDENTYFIKATORze 0 to zadanie podstawowe, a wszystkie inne identyfikatory są podzadaniami. Na przykład w przypadku tworzenia następujących ustawień dla zadania podrzędnego, zadanie podstawowe będzie miało identyfikator 0, a podzadania mają identyfikatory od 1 do 9.

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

### <a name="master-node"></a>Węzeł główny

Po przesłaniu zadania z wieloma wystąpieniami usługa Batch wyznacza jeden z węzłów obliczeniowych jako węzeł "główny" i planuje wykonywanie zadania podstawowego w węźle głównym. Zadania podrzędne są zaplanowane do wykonania w pozostałej części węzłów przydzielonych do zadania o wiele wystąpień.

## <a name="coordination-command"></a>Polecenie koordynacji

**Polecenie koordynacyjne** jest wykonywane przez podstawowe i podrzędne podzadania.

Wywołanie polecenia koordynacyjnego jest blokowane — usługa Batch nie wykonuje polecenia aplikacji, dopóki polecenie koordynacji nie zostanie pomyślnie zwrócone dla wszystkich podzadań. W związku z tym polecenie koordynacyjne powinno uruchomić wszystkie wymagane usługi w tle, sprawdzić, czy są gotowe do użycia, a następnie zamknąć. Na przykład to polecenie koordynacji dla rozwiązania korzystającego z MS-MPI w wersji 7 uruchamia usługę SMPD w węźle, a następnie kończy działanie:

`cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d`

Zwróć uwagę na użycie `start` w tym poleceniu koordynacyjnym. Jest to wymagane, ponieważ `smpd.exe` aplikacja nie zwraca natychmiast po wykonaniu. Bez użycia polecenia Uruchom to polecenie koordynacyjne nie zwróci i w związku z tym blokuje uruchamianie polecenia aplikacji.

## <a name="application-command"></a>Polecenie aplikacji

Gdy zadanie podstawowe i wszystkie podzadania zakończą wykonywanie polecenia koordynacji, wiersz polecenia zadania wielowystąpienia jest wykonywany *tylko* przez zadanie podstawowe. Nazywamy to **polecenie aplikacji** , aby odróżnić je od polecenia koordynacji.

W przypadku aplikacji MS-MPI Użyj polecenia aplikacji, aby uruchomić aplikację z obsługą MPIymi w usłudze `mpiexec.exe` . Na przykład poniżej przedstawiono polecenie aplikacji dla rozwiązania korzystającego z MS-MPI w wersji 7:

`cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe`

> [!NOTE]
> Ponieważ usługa MS-MPI `mpiexec.exe` używa `CCP_NODES` Domyślnie zmiennej (zobacz [zmienne środowiskowe](#environment-variables)), przykładowa powyższa część wiersza poleceń aplikacji wyklucza ją.

## <a name="environment-variables"></a>Zmienne środowiskowe

Wsadowe tworzy kilka [zmiennych środowiskowych](batch-compute-node-environment-variables.md) specyficznych dla zadań o wielu wystąpieniach w węzłach obliczeniowych przypisywanych do zadania o wielu wystąpieniach. Linie poleceń koordynacji i aplikacji mogą odwoływać się do tych zmiennych środowiskowych, co może uruchamiać skrypty i programy.

Następujące zmienne środowiskowe są tworzone przez usługę Batch do użycia przez zadania o wiele wystąpień:

- `CCP_NODES`
- `AZ_BATCH_NODE_LIST`
- `AZ_BATCH_HOST_LIST`
- `AZ_BATCH_MASTER_NODE`
- `AZ_BATCH_TASK_SHARED_DIR`
- `AZ_BATCH_IS_CURRENT_NODE_MASTER`

Aby uzyskać szczegółowe informacje na temat tych i innych zmiennych środowiskowych węzłów obliczeniowych usługi Batch, w tym ich zawartości i widoczności, zobacz [zmienne środowiskowe węzła obliczeniowego](batch-compute-node-environment-variables.md).

> [!TIP]
> [Przykładowy kod MPI systemu Linux](https://github.com/Azure-Samples/azure-batch-samples/tree/master/Python/Batch/article_samples/mpi) w usłudze Batch zawiera przykład sposobu użycia kilku z tych zmiennych środowiskowych.

## <a name="resource-files"></a>Pliki zasobów

Istnieją dwa zestawy plików zasobów, które należy wziąć pod uwagę w przypadku zadań obejmujących wiele wystąpień: **wspólne pliki zasobów** pobierane przez *wszystkie* zadania (podstawowe i podrzędne) oraz **pliki zasobów** określone dla zadania wielowystąpienia, które *są pobierane tylko przez podstawowe* zadanie.

W ustawieniach wielu wystąpień zadania można określić co najmniej jeden **typowy plik zasobów** . Te typowe pliki zasobów są pobierane z [usługi Azure Storage](../storage/common/storage-introduction.md) do **udostępnionego katalogu zadań** każdego węzła według podstawowego i wszystkich podzadań. Można uzyskać dostęp do udostępnionego katalogu zadania z poziomu wiersza polecenia aplikacji i koordynacji przy użyciu `AZ_BATCH_TASK_SHARED_DIR` zmiennej środowiskowej. `AZ_BATCH_TASK_SHARED_DIR`Ścieżka jest taka sama w każdym węźle przydzielonym do zadania z wielu wystąpień, dzięki czemu można udostępnić pojedyncze polecenie koordynacji między podstawowym i wszystkimi podzadaniami. Usługa Batch nie "udostępnia" katalogu w sensie dostępu zdalnego, ale można go użyć jako instalacji lub punktu udostępniania, jak wspomniano wcześniej w poradach zmiennych środowiskowych.

Pliki zasobów, które są określone dla samego zadania o wiele wystąpień, są domyślnie pobierane do katalogu roboczego zadania `AZ_BATCH_TASK_WORKING_DIR` . Jak wspomniano, w przeciwieństwie do wspólnych plików zasobów, tylko zadanie podstawowe pobiera pliki zasobów określone dla samego zadania wielowystąpienia.

> [!IMPORTANT]
> Zawsze używaj zmiennych środowiskowych `AZ_BATCH_TASK_SHARED_DIR` i `AZ_BATCH_TASK_WORKING_DIR` odwołują się do tych katalogów w wierszach poleceń. Nie należy próbować tworzyć ścieżek ręcznie.

## <a name="task-lifetime"></a>Okres istnienia zadania

Okres istnienia zadania podstawowego określa okres istnienia całego zadania o wiele wystąpień. Po zakończeniu podstawowego działania wszystkie podzadania zostaną zakończone. Kodem zakończenia zadania podstawowego jest kod zakończenia dla zadaniu i jest on używany do ustalenia sukcesu lub niepowodzenia zadania w celu ponowienia próby.

Jeśli którekolwiek z podzadań zakończy się niepowodzeniem, wyjście z niezerowym kodem powrotu nie powiedzie się. Zadanie z wielowystąpieniem jest następnie przerywane i ponawiane, aż do jego limitu ponownych prób.

Po usunięciu zadania o wiele wystąpień podstawowe i wszystkie podzadania są również usuwane przez usługę Batch. Wszystkie katalogi podzadań i ich pliki są usuwane z węzłów obliczeniowych, podobnie jak w przypadku zadania standardowego.

[TaskConstraints](/dotnet/api/microsoft.azure.batch.taskconstraints) dla zadania z wielowystąpieniem, takie jak właściwości [MaxTaskRetryCount](/dotnet/api/microsoft.azure.batch.taskconstraints.maxtaskretrycount), [MaxWallClockTime](/dotnet/api/microsoft.azure.batch.taskconstraints.maxwallclocktime)i [RetentionTime](/dotnet/api/microsoft.azure.batch.taskconstraints.retentiontime) , są uznawane za zadanie standardowe i mają zastosowanie do podstawowych i wszystkich podzadań. Jeśli jednak zmienisz Właściwość theRetentionTime po dodaniu zadania o wiele wystąpień do zadania, ta zmiana zostanie zastosowana tylko do zadania podstawowego, a wszystkie podzadania będą nadal używać oryginalnego RetentionTimeu.

Lista ostatnich zadań w węźle obliczeniowym odzwierciedla identyfikator podzadania, jeśli ostatnie zadanie było częścią zadania o wiele wystąpień.

## <a name="obtain-information-about-subtasks"></a>Uzyskaj informacje na temat podzadań

Aby uzyskać informacje dotyczące podzadań przy użyciu biblioteki usługi Batch .NET, wywołaj metodę [CloudTask. ListSubtasks](/dotnet/api/microsoft.azure.batch.cloudtask.listsubtasks) . Ta metoda zwraca informacje o wszystkich podzadaniach i informacje o węźle obliczeniowym, który wykonał zadania. Z tych informacji można określić katalog główny każdego podzadania, Identyfikator puli, bieżący stan, kod zakończenia i inne. Tych informacji można użyć w połączeniu z metodą [PoolOperations. GetNodeFile](/dotnet/api/microsoft.azure.batch.pooloperations.getnodefile) w celu uzyskania plików podzadań. Należy zauważyć, że ta metoda nie zwraca informacji dla zadania podstawowego (identyfikator 0).

> [!NOTE]
> O ile nie określono inaczej, metody usługi Batch .NET, które działają dla samego [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) o wiele wystąpień, mają zastosowanie *tylko* do zadania podstawowego. Na przykład po wywołaniu metody [CloudTask. ListNodeFiles](/dotnet/api/microsoft.azure.batch.cloudtask.listnodefiles) na zadaniu z wielowystąpieniem zwracane są tylko pliki podstawowego zadania.

Poniższy fragment kodu pokazuje, jak uzyskać informacje o podzadań, a także zażądać zawartości pliku z węzłów, na których zostały wykonane.

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

Przykład kodu [MultiInstanceTasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks) w witrynie GitHub pokazuje, jak używać zadania wieloetapowego do uruchamiania aplikacji [MS-MPI](/message-passing-interface/microsoft-mpi) w węzłach obliczeniowych usługi Batch. Wykonaj poniższe kroki, aby uruchomić przykład.

### <a name="preparation"></a>Przygotowanie

1. Pobierz [zestawy SDK MS-MPI i Redist](/message-passing-interface/microsoft-mpi) oraz zainstaluj je. Po zakończeniu instalacji można sprawdzić, czy zostały ustawione zmienne środowiskowe MS-MPI.
1. Utwórz wersję *wydania* przykładowego programu [MPIHelloWorld](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks/MPIHelloWorld) Sample MPI. Jest to program, który będzie uruchamiany w węzłach obliczeniowych za pomocą zadania obejmującego wiele wystąpień.
1. Utwórz plik zip zawierający `MPIHelloWorld.exe` (który został utworzony w kroku 2) i `MSMpiSetup.exe` (który został pobrany w kroku 1). Ten plik zip zostanie przekazany jako pakiet aplikacji w następnym kroku.
1. Użyj [Azure Portal](https://portal.azure.com) , aby utworzyć [aplikację](batch-application-packages.md) wsadową o nazwie "MPIHelloWorld" i określić plik zip utworzony w poprzednim kroku jako wersję "1,0" pakietu aplikacji. Aby uzyskać więcej informacji [, zobacz Przekazywanie aplikacji i zarządzanie nimi](batch-application-packages.md#upload-and-manage-applications) .

> [!TIP]
> Utworzenie wersji z *wydaną* wersją programu `MPIHelloWorld.exe` gwarantuje, że nie trzeba uwzględniać żadnych dodatkowych zależności (na przykład `msvcp140d.dll` lub `vcruntime140d.dll` ) w pakiecie aplikacji.

### <a name="execution"></a>Wykonanie

1. Pobierz [plik z usługi Azure-Batch-Samples. zip](https://github.com/Azure/azure-batch-samples/archive/master.zip) z witryny GitHub.
1. Otwórz **rozwiązanie** MultiInstanceTasks w programie Visual Studio 2019. `MultiInstanceTasks.sln`Plik rozwiązania znajduje się w:

    `azure-batch-samples\CSharp\ArticleProjects\MultiInstanceTasks\`
1. Wprowadź swoje poświadczenia konta partii i magazynu w programie `AccountSettings.settings` w projekcie **Microsoft.Azure.Batch. Samples. Common** Project.
1. **Kompiluj i uruchamiaj** rozwiązanie MultiInstanceTasks, aby wykonać przykładową aplikację MPI w węzłach obliczeniowych w puli wsadowej.
1. *Opcjonalne*: Użyj [Azure Portal](https://portal.azure.com) lub [Batch Explorer](https://azure.github.io/BatchExplorer/) , aby przejrzeć przykładową pulę, zadanie i zadanie ("MultiInstanceSamplePool", "MultiInstanceSampleJob", "MultiInstanceSampleTask") przed usunięciem zasobów.

> [!TIP]
> Możesz bezpłatnie pobrać [program Visual Studio Community](https://visualstudio.microsoft.com/vs/community/) , jeśli nie masz jeszcze programu Visual Studio.

Dane wyjściowe `MultiInstanceTasks.exe` są podobne do następujących:

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

- Przeczytaj więcej [na temat obsługi MPI dla systemu Linux na Azure Batch](https://docs.microsoft.com/archive/blogs/windowshpc/introducing-mpi-support-for-linux-on-azure-batch).
- Dowiedz się, jak [tworzyć pule węzłów obliczeniowych systemu Linux](batch-linux-nodes.md) do użycia w Azure Batch rozwiązaniach MPI.
