---
title: Tworzenie zależności zadań do uruchamiania zadań
description: Utwórz zadania, które zależą od ukończenia innych zadań przetwarzania stylu MapReduce i podobnych obciążeń danych Big Data w Azure Batch.
ms.topic: how-to
ms.date: 12/28/2020
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: ef05a98fffc3c0684ad0fa29f2f9f039b388f5ad
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803939"
---
# <a name="create-task-dependencies-to-run-tasks-that-depend-on-other-tasks"></a>Tworzenie zależności zadań w celu uruchamiania zadań zależnych od innych zadań

W przypadku zależności zadań wsadowych można tworzyć zadania zaplanowane do wykonania w węzłach obliczeniowych po zakończeniu jednego lub kilku zadań nadrzędnych. Można na przykład utworzyć zadanie, które renderuje każdą ramkę filmu 3W z oddzielnymi zadaniami równoległymi. Zadanie końcowe — "zadanie scalania" — Scala renderowane ramki do kompletnego filmu dopiero po pomyślnym wyrenderowaniu wszystkich ramek.

Niektóre scenariusze, w których są przydatne zależności zadań, obejmują:

- Obciążenia w stylu MapReduce w chmurze.
- Zadania, których zadania przetwarzania danych mogą być wyrażone jako ukierunkowane wykresy acykliczne (DAG).
- Procesy przed renderowaniem i po renderingu, gdzie każde zadanie musi zakończyć się przed rozpoczęciem następnego zadania.
- Wszystkie inne zadania, w których zadania podrzędne są zależne od danych wyjściowych zadań nadrzędnych.

Domyślnie zadania zależne są planowane do wykonania dopiero po pomyślnym ukończeniu zadania nadrzędnego. Opcjonalnie można określić [akcję zależności](#dependency-actions)  , aby zastąpić zachowanie domyślne i uruchamiać zadania, gdy zadanie nadrzędne zakończy się niepowodzeniem.

## <a name="task-dependencies-with-batch-net"></a>Współzależności zadań z usługą Batch .NET

W tym artykule omówiono sposób konfigurowania zależności zadań przy użyciu biblioteki usługi Batch dla [platformy .NET](/dotnet/api/microsoft.azure.batch) . Najpierw pokazano, jak [włączyć zależność zadań](#enable-task-dependencies) względem zadań, a następnie zademonstrować sposób [konfigurowania zadania z zależnościami](#create-dependent-tasks). Opisano również sposób określania akcji zależności do uruchamiania zadań zależnych w przypadku niepowodzenia elementu nadrzędnego. Na koniec omówiono [scenariusze zależności](#dependency-scenarios) obsługiwane przez partię.

## <a name="enable-task-dependencies"></a>Włącz zależności zadań

Aby korzystać z zależności zadań w aplikacji wsadowej, należy najpierw skonfigurować zadanie tak, aby korzystało z zależności zadań. W usłudze Batch .NET Włącz ją na [CloudJob](/dotnet/api/microsoft.azure.batch.cloudjob) , ustawiając jej właściwość [UsesTaskDependencies](/dotnet/api/microsoft.azure.batch.cloudjob.usestaskdependencies) na `true` :

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

W poprzednim fragmencie kodu "batchClient" jest wystąpieniem klasy [batchClient](/dotnet/api/microsoft.azure.batch.batchclient) .

## <a name="create-dependent-tasks"></a>Tworzenie zadań zależnych

Aby utworzyć zadanie, które zależy od ukończenia jednego lub kilku zadań nadrzędnych, można określić, że zadanie "zależy od" innych zadań. W usłudze Batch .NET Skonfiguruj Właściwość [CloudTask. DependsOn](/dotnet/api/microsoft.azure.batch.cloudtask.dependson) z wystąpieniem klasy [TaskDependencies](/dotnet/api/microsoft.azure.batch.taskdependencies) :

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

Ten fragment kodu tworzy zadanie zależne z IDENTYFIKATORem zadania "kwiaty". Zadanie "kwiaty" zależy od zadań "deszcz" i "Sun". Zadanie "kwiaty" zostanie zaplanowane do uruchomienia w węźle obliczeniowym dopiero po pomyślnym ukończeniu zadań "deszcz" i "Sun".

> [!NOTE]
> Domyślnie zadanie jest uznawane za zakończone pomyślnie, gdy jest w stanie ukończone i jego kod zakończenia to `0` . W usłudze Batch .NET oznacza to, że właściwość [CloudTask. State](/dotnet/api/microsoft.azure.batch.cloudtask.state) ma wartość `Completed` i Właściwość [TaskExecutionInformation. ExitCode](/dotnet/api/microsoft.azure.batch.taskexecutioninformation.exitcode) CloudTask ma wartość `0` . Aby dowiedzieć się, jak to zmienić, zobacz sekcję [Akcje zależności](#dependency-actions) .

## <a name="dependency-scenarios"></a>Scenariusze zależności

Istnieją trzy podstawowe scenariusze zależności zadań, których można użyć w Azure Batch: jeden do jednego, jeden-do-wielu i zakres identyfikatorów zadań. Te trzy scenariusze można łączyć, aby zapewnić czwarty scenariusz: wiele-do-wielu.

| Scenariusz&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Przykład | Ilustracji |
|:---:| --- | --- |
|  [Jeden do jednego](#one-to-one) |*zadaniab* zależy od *zadania* <p/> *zadaniab* nie zostanie zaplanowana do wykonania przed pomyślnym zakończeniem *zadania* |:::image type="content" source="media/batch-task-dependency/01_one_to_one.png" alt-text="Diagram przedstawiający scenariusz zależności zadań jeden-do-jednego."::: |
|  [Jeden do wielu](#one-to-many) |*zadaniec* zależy od *zadania* i *zadaniab* <p/> *zadaniec* nie zostanie zaplanowana do wykonania, dopóki *zadania* i *zadaniab* nie zostaną ukończone pomyślnie |:::image type="content" source="media/batch-task-dependency/02_one_to_many.png" alt-text="Diagram przedstawiający scenariusz współzależności między zadaniami &quot;jeden do wielu&quot;."::: |
|  [Zakres identyfikatorów zadań](#task-id-range) |*zadania* podrzędne są zależne od zakresu zadań <p/> *zadanie* nie zostanie zaplanowane do wykonania, dopóki zadania o identyfikatorach od *1* do *10* nie zakończą się pomyślnie |:::image type="content" source="media/batch-task-dependency/03_task_id_range.png" alt-text="Diagram przedstawiający scenariusz zależności zadania zakresu identyfikatorów zadań."::: |

> [!TIP]
> Można utworzyć relacje **wiele-do-wielu** , takie jak zadania C, D, E i F, które są zależne od zadań a i B. Jest to przydatne na przykład w przypadku równoległych scenariuszy przetwarzania wstępnego, w których zadania podrzędne zależą od danych wyjściowych wielu zadań nadrzędnych.
> 
> W przykładach w tej sekcji zadanie zależne jest uruchamiane dopiero po pomyślnym ukończeniu zadań nadrzędnych. To zachowanie jest zachowaniem domyślnym dla zadania zależnego. Zadanie zależne można uruchomić po niepowodzeniu zadania nadrzędnego, określając [akcję zależności](#dependency-actions)  , która zastąpi zachowanie domyślne.

### <a name="one-to-one"></a>Jeden do jednego

W relacji jeden do jednego zadanie jest zależne od pomyślnego ukończenia jednego zadania nadrzędnego. Aby utworzyć zależność, podaj jeden identyfikator zadania do statycznej metody [TaskDependencies. OnId](/dotnet/api/microsoft.azure.batch.taskdependencies.onid) , gdy zostanie wypełniona Właściwość [CloudTask. DependsOn](/dotnet/api/microsoft.azure.batch.cloudtask.dependson) .

```csharp
// Task 'taskA' doesn't depend on any other tasks
new CloudTask("taskA", "cmd.exe /c echo taskA"),

// Task 'taskB' depends on completion of task 'taskA'
new CloudTask("taskB", "cmd.exe /c echo taskB")
{
    DependsOn = TaskDependencies.OnId("taskA")
},
```

### <a name="one-to-many"></a>Jeden do wielu

W relacji jeden do wielu zadanie zależy od ukończenia wielu zadań nadrzędnych. Aby utworzyć zależność, podaj kolekcję identyfikatorów zadań do statycznej metody [TaskDependencies. OnIds](/dotnet/api/microsoft.azure.batch.taskdependencies.onids) , gdy zostanie wypełniona Właściwość [CloudTask. DependsOn](/dotnet/api/microsoft.azure.batch.cloudtask.dependson) .

```csharp
// 'Rain' and 'Sun' don't depend on any other tasks
new CloudTask("Rain", "cmd.exe /c echo Rain"),
new CloudTask("Sun", "cmd.exe /c echo Sun"),

// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

### <a name="task-id-range"></a>Zakres identyfikatorów zadań

W zależności od zakresu zadań nadrzędnych zadanie zależy od ukończenia zadań, których identyfikatory mieszczą się w zakresie.
Aby utworzyć zależność, podaj pierwsze i ostatnie identyfikatory zadań w zakresie do metody statycznej [TaskDependencies](/dotnet/api/microsoft.azure.batch.taskdependencies.onidrange) po wypełnieniu właściwości [CloudTask. DependsOn](/dotnet/api/microsoft.azure.batch.cloudtask.dependson) .

> [!IMPORTANT]
> W przypadku korzystania z zakresów identyfikatorów zadań w zależności od zakresu będą wybierane tylko zadania z identyfikatorami reprezentującymi wartości całkowite. Na przykład zakres `1..10` będzie wybierać zadania `3` i `7` , ale nie `5flamingoes` .
>
> Zera wiodące nie są istotne podczas oceniania zależności zakresu, dlatego zadania z identyfikatorami `4` ciągów `04` i `004` wszystkie będą znajdować się *w* zakresie, a wszystkie będą traktowane jako zadania `4` , więc pierwszy z nich będzie spełniał zależność.
>
> Każde zadanie w zakresie musi spełniać zależność, przez pomyślne zakończenie lub przez zakończenie z powodu błędu zamapowanego na [akcję zależności](#dependency-actions) ustawioną na **spełnienie**.

```csharp
// Tasks 1, 2, and 3 don't depend on any other tasks. Because
// we will be using them for a task range dependency, we must
// specify string representations of integers as their ids.
new CloudTask("1", "cmd.exe /c echo 1"),
new CloudTask("2", "cmd.exe /c echo 2"),
new CloudTask("3", "cmd.exe /c echo 3"),

// Task 4 depends on a range of tasks, 1 through 3
new CloudTask("4", "cmd.exe /c echo 4")
{
    // To use a range of tasks, their ids must be integer values.
    // Note that we pass integers as parameters to TaskIdRange,
    // but their ids (above) are string representations of the ids.
    DependsOn = TaskDependencies.OnIdRange(1, 3)
},
```

## <a name="dependency-actions"></a>Akcje zależności

Domyślnie zależne zadanie lub zestaw zadań jest uruchamiany dopiero po pomyślnym ukończeniu zadania nadrzędnego. W niektórych scenariuszach można uruchamiać zadania zależne nawet wtedy, gdy zadanie nadrzędne zakończy się niepowodzeniem. Zachowanie domyślne można przesłonić, określając akcję zależności.

Akcja zależności określa, czy zadanie zależne jest uprawnione do uruchomienia, na podstawie sukcesu lub niepowodzenia zadania nadrzędnego. Załóżmy na przykład, że zadanie zależne oczekuje na dane po zakończeniu zadania nadrzędnego. Jeśli zadanie nadrzędne zakończy się niepowodzeniem, zadanie zależne może nadal być uruchamiane przy użyciu starszych danych. W takim przypadku akcja zależności może określać, że zadanie zależne jest uprawnione do uruchomienia niezależnie od błędu zadania nadrzędnego.

Akcja zależności jest oparta na warunku wyjścia zadania nadrzędnego. Można określić akcję zależności dla dowolnego z następujących warunków zakończenia:

- Gdy wystąpi błąd podczas przetwarzania wstępnego.
- Gdy wystąpi błąd przekazywania pliku. Jeśli zadanie zostanie zakończone z kodem zakończenia, który został określony za pośrednictwem **exitCodes** lub **exitCodeRanges**, a następnie napotka błąd przekazywania pliku, Akcja określona przez kod zakończenia ma pierwszeństwo.
- Gdy zadanie zostanie zakończone z kodem zakończenia zdefiniowanym przez właściwość **ExitCodes** .
- Gdy zadanie zostanie zakończone z kodem zakończenia, który mieści się w zakresie określonym przez właściwość **ExitCodeRanges** .
- Przypadek domyślny, jeśli zadanie zostanie zakończone z kodem zakończenia niezdefiniowanym przez **ExitCodes** lub **ExitCodeRanges**, lub jeśli zadanie zakończy się z błędem przetwarzania wstępnego, a właściwość **PreProcessingError** nie została ustawiona lub jeśli zadanie zakończy się niepowodzeniem z błędem przekazywania pliku i Właściwość **FileUploadError** nie jest ustawiona. 

Aby uzyskać więcej informacji na temat tych warunków, zobacz Klasa [ExitConditions](/dotnet/api/microsoft.azure.batch.exitconditions) .

Aby określić akcję zależności w programie .NET, należy ustawić właściwość [ExitOptions. DependencyAction](/dotnet/api/microsoft.azure.batch.exitoptions.dependencyaction) dla warunku zakończenia na jedną z następujących wartości:

- **Spełnianie**: wskazuje, że zadania zależne są uprawnione do uruchomienia, jeśli zadanie nadrzędne zostanie zakończone z określonym błędem.
- **Blok**: wskazuje, że zadania zależne nie mogą być uruchamiane.

Ustawienie domyślne właściwości **DependencyAction** jest **zgodne** dla kodu zakończenia 0 i **bloku** dla wszystkich pozostałych warunków zakończenia.

Poniższy fragment kodu ustawia właściwość **DependencyAction** dla zadania nadrzędnego. Jeśli zadanie nadrzędne zostanie zakończone z błędem przetwarzania wstępnego lub z określonymi kodami błędów, zadanie zależne zostanie zablokowane. Jeśli zadanie nadrzędne zostanie zakończone z jakimkolwiek innym błędem o wartości innej niż zero, zadanie zależne może zostać uruchomione.

```csharp
// Task A is the parent task.
new CloudTask("A", "cmd.exe /c echo A")
{
    // Specify exit conditions for task A and their dependency actions.
    ExitConditions = new ExitConditions
    {
        // If task A exits with a pre-processing error, block any downstream tasks (in this example, task B).
        PreProcessingError = new ExitOptions
        {
            DependencyAction = DependencyAction.Block
        },
        // If task A exits with the specified error codes, block any downstream tasks (in this example, task B).
        ExitCodes = new List<ExitCodeMapping>
        {
            new ExitCodeMapping(10, new ExitOptions() { DependencyAction = DependencyAction.Block }),
            new ExitCodeMapping(20, new ExitOptions() { DependencyAction = DependencyAction.Block })
        },
        // If task A succeeds or fails with any other error, any downstream tasks become eligible to run 
        // (in this example, task B).
        Default = new ExitOptions
        {
            DependencyAction = DependencyAction.Satisfy
        }
    }
},
// Task B depends on task A. Whether it becomes eligible to run depends on how task A exits.
new CloudTask("B", "cmd.exe /c echo B")
{
    DependsOn = TaskDependencies.OnId("A")
},
```

## <a name="code-sample"></a>Przykład kodu

Przykładowy projekt [TaskDependencies](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies) w witrynie GitHub ilustruje:

- Jak włączyć zależność zadania w zadaniu.
- Sposób tworzenia zadań, które są zależne od innych zadań.
- Sposób wykonywania tych zadań w puli węzłów obliczeniowych.

## <a name="next-steps"></a>Następne kroki

- Poznaj funkcję [pakietów aplikacji](batch-application-packages.md) w usłudze Batch, która zapewnia łatwy sposób wdrażania i obsługi aplikacji wykonywanych przez zadania w węzłach obliczeniowych.
- Dowiedz się więcej [na temat sprawdzania błędów dla zadań i zadań](batch-job-task-error-checking.md).
