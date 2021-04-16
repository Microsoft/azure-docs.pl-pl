---
title: Współbieżne uruchamianie zadań w celu zmaksymalizowania użycia węzłów obliczeniowych usługi Batch
description: Zwiększ wydajność i obniż koszty, używając mniejszej liczby węzłów obliczeniowych i uruchamiając zadania równolegle w każdym węźle w Azure Batch obliczeniowej
ms.topic: how-to
ms.date: 04/13/2021
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: 81648f30a7a02f702dcb189aa7df27e5a82e2b07
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389302"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Współbieżne uruchamianie zadań podrzędnych w celu zmaksymalizowania użycia węzłów obliczeniowych usługi Batch

Można zmaksymalizować użycie zasobów w mniejszej liczbie węzłów obliczeniowych w puli, uruchamiając jednocześnie więcej niż jedno zadanie w każdym węźle.

Chociaż niektóre scenariusze działają najlepiej ze wszystkimi zasobami węzła przeznaczonymi dla jednego zadania, niektóre obciążenia mogą widzieć krótsze czasy wykonywania zadań i obniżyć koszty, gdy wiele zadań współdzieli te zasoby. Rozważ następujące scenariusze:

- **Zminimalizuj transfer** danych dla zadań, które mogą udostępniać dane. Możesz znacznie zmniejszyć opłaty za transfer danych, kopiując udostępnione dane do mniejszej liczby węzłów, a następnie wykonując zadania równolegle w każdym węźle. Ma to zastosowanie zwłaszcza wtedy, gdy dane, które mają zostać skopiowane do każdego węzła, muszą być przesyłane między regionami geograficznymi.
- **Maksymalizuj użycie** pamięci dla zadań, które wymagają dużej ilości pamięci, ale tylko w krótkich okresach czasu i w zmiennych godzinach podczas wykonywania. Aby efektywnie obsłużyć takie skoki, można użyć mniejszej liczby większych węzłów obliczeniowych z większą ilością pamięci. Te węzły będą mieć wiele zadań podrzędnych uruchomionych równolegle w każdym węźle, ale każde zadanie może korzystać z pamięci wielowęzłowej węzłów w różnym czasie.
- **Ograniczenie limitów liczby węzłów,** gdy komunikacja między węzłami jest wymagana w puli. Obecnie pule skonfigurowane do komunikacji między węzłami są ograniczone do 50 węzłów obliczeniowych. Jeśli każdy węzeł w takiej puli jest w stanie wykonywać zadania równolegle, większą liczbę zadań można wykonać jednocześnie.
- **Replikowanie lokalnego klastra obliczeniowego,** na przykład przy pierwszym przenoszeniu środowiska obliczeniowego na platformę Azure. Jeśli bieżące rozwiązanie lokalne wykonuje wiele zadań na węzeł obliczeniowy, można zwiększyć maksymalną liczbę zadań węzła, aby dokładniej odzwierciedlać konfigurację.

## <a name="example-scenario"></a>Przykładowy scenariusz

Załóżmy na przykład, że aplikacja zadań z wymaganiami w zakresie procesora CPU i pamięci jest wystarczająca dla węzłów [ \_ Standardowa D1.](../cloud-services/cloud-services-sizes-specs.md#d-series) Jednak do ukończenia zadania w wymaganym czasie jest wymaganych 1000 z tych węzłów.

Zamiast używać węzłów Standard D1 z 1 rdzeniem procesora CPU, można użyć węzłów \_ [Standard \_ D14,](../cloud-services/cloud-services-sizes-specs.md#d-series) które mają 16 rdzeni każdy, i włączyć równoległe wykonywanie zadań. Oznacza to, że można użyć 16 razy mniejszej liczby węzłów — zamiast 1000 węzłów będzie wymaganych tylko 63. Jeśli dla każdego węzła są wymagane duże pliki aplikacji lub dane referencyjne, czas trwania zadania i wydajność są lepsze, ponieważ dane są kopiowane tylko do 63 węzłów.

## <a name="enable-parallel-task-execution"></a>Włączanie równoległego wykonywania zadań

Węzły obliczeniowe są konfigurowane do równoległego wykonywania zadań na poziomie puli. Za pomocą biblioteki .NET usługi Batch ustaw właściwość [CloudPool.TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode) podczas tworzenia puli. Jeśli używasz interfejsu API REST usługi Batch, ustaw element [taskSlotsPerNode](/rest/api/batchservice/pool/add) w treści żądania podczas tworzenia puli.

> [!NOTE]
> Element i `taskSlotsPerNode` właściwość [TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) można ustawić tylko w czasie tworzenia puli. Nie można ich modyfikować po utworzeniu puli.

Azure Batch umożliwia ustawienie miejsc zadań na węzeł do (4x) liczby rdzeni węzłów. Jeśli na przykład pula jest skonfigurowana z węzłami o rozmiarze "Duży" (cztery rdzenie), można ustawić wartość `taskSlotsPerNode` 16. Jednak bez względu na to, ile rdzeni ma węzeł, nie można mieć więcej niż 256 gniazd zadań na węzeł. Aby uzyskać szczegółowe informacje na temat liczby rdzeni dla każdego z rozmiarów węzłów, zobacz [Sizes for Cloud Services](../cloud-services/cloud-services-sizes-specs.md). Aby uzyskać więcej informacji na temat limitów usługi, zobacz Limity przydziału i [limity dla Azure Batch usługi](batch-quota-limit.md).

> [!TIP]
> Pamiętaj, aby wziąć pod uwagę `taskSlotsPerNode` wartość podczas konstruowania [formuły autoskalowania](/rest/api/batchservice/pool/enableautoscale) dla puli. Na przykład na formułę, która ocenia, może mieć wpływ wzrost `$RunningTasks` liczby zadań na węzeł. Aby uzyskać więcej informacji, zobacz [Automatyczne skalowanie węzłów obliczeniowych w puli Azure Batch obliczeniowej.](batch-automatic-scaling.md)

## <a name="specify-task-distribution"></a>Określanie dystrybucji zadań

Podczas włączania zadań współbieżnych należy określić sposób dystrybucji zadań podrzędnych między węzły w puli.

Za pomocą [właściwości CloudPool.TaskSchedulingPolicy](/dotnet/api/microsoft.azure.batch.cloudpool.taskschedulingpolicy) można określić, że zadania powinny być przypisywane równomiernie we wszystkich węzłach w puli ("rozłożenie"). Można też określić, że do każdego węzła należy przypisać jak najwięcej zadań, zanim zadania zostaną przypisane do innego węzła w puli ("pakowanie").

Rozważmy na przykład pulę węzłów [ \_ Standard D14](../cloud-services/cloud-services-sizes-specs.md#d-series) (w powyższym przykładzie), która jest skonfigurowana z wartością [CloudPool.TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode) 16. Jeśli [element CloudPool.TaskSchedulingPolicy](/dotnet/api/microsoft.azure.batch.cloudpool.taskschedulingpolicy) został skonfigurowany przy użyciu elementu [ComputeNodeFillType](/dotnet/api/microsoft.azure.batch.common.computenodefilltype) *pakietu*, zmaksymalizuje [](batch-automatic-scaling.md) użycie wszystkich 16 rdzeni każdego węzła i umożliwi puli skalowania automatycznego usunięcie nieużywanych węzłów (węzłów bez przypisanych zadań) z puli. Pozwala to zminimalizować użycie zasobów i zaoszczędzić pieniądze.

## <a name="define-variable-slots-per-task"></a>Definiowanie miejsc zmiennych na zadanie

Zadanie można zdefiniować za pomocą właściwości [CloudTask.RequiredSlots,](/dotnet/api/microsoft.azure.batch.cloudtask.requiredslots) określając liczbę miejsc wymaganych do uruchomienia w węźle obliczeniowym. Wartość domyślna to 1. Jeśli zadania mają różne wagi dotyczące użycia zasobów w węźle obliczeniowym, można ustawić zmienne miejsca zadań. Dzięki temu każdy węzeł obliczeniowy ma rozsądną liczbę współbieżnych uruchomionych zadań bez przeciążania zasobów systemowych, takich jak procesor CPU lub pamięć.

Na przykład w przypadku puli z właściwością można przesłać wielordzeniowe wymagane zadania intensywnie obciążane procesorem CPU za pomocą obiektu , a inne zadania można ustawić `taskSlotsPerNode = 8` `requiredSlots = 8` na `requiredSlots = 1` . Po zaplanowaniu tego obciążenia mieszanego zadania intensywnie korzystające z procesora CPU będą uruchamiane wyłącznie w ich węzłach obliczeniowych, podczas gdy inne zadania podrzędne mogą być uruchamiane współbieżnie (maksymalnie osiem zadań jednocześnie) w innych węzłach. Pomaga to zrównoważyć obciążenie w węzłach obliczeniowych i zwiększyć wydajność użycia zasobów.

Pamiętaj, aby nie określać, że zadania mają być większe niż . `requiredSlots` `taskSlotsPerNode` Spowoduje to, że nigdy nie będzie można uruchomić zadania. Usługa Batch obecnie nie weryfikuje tego konfliktu podczas przesyłania zadań, ponieważ zadanie może nie mieć puli powiązanej w czasie przesyłania lub może zostać zmienione na inną pulę przez wyłączenie/ponowne włączenie.

> [!TIP]
> W przypadku używania zmiennych gniazd zadań może się dzieje, że zaplanowane duże zadania z większą liczbę wymaganych miejsc tymczasowo nie będą możliwe, ponieważ w żadnym węźle obliczeniowym nie ma wystarczającej liczby miejsc, nawet jeśli w niektórych węzłach nadal znajdują się bezczynne miejsca. Można podnieść priorytet zadania dla tych zadań, aby zwiększyć ich szansę na konkurowanie o dostępne miejsca w węzłach.
>
> Usługa Batch emituje [taskScheduleFailEvent,](batch-task-schedule-fail-event.md) gdy nie może zaplanować zadania do uruchomienia, i ponawia próbę planowania do momentu, gdy wymagane miejsca staną się dostępne. Możesz nasłuchiwać tego zdarzenia, aby wykryć potencjalne problemy z planowaniem zadań i odpowiednio je rozwiązać.

## <a name="batch-net-example"></a>Przykład usługi Batch dla .NET

Poniższe fragmenty kodu interfejsu API [platformy .NET](/dotnet/api/microsoft.azure.batch) usługi Batch pokazują, jak utworzyć pulę z wieloma miejscami zadań na węzeł i jak przesłać zadanie z wymaganymi miejscami.

### <a name="create-a-pool-with-multiple-task-slots-per-node"></a>Tworzenie puli z wieloma miejscami zadań na węzeł

Ten fragment kodu przedstawia żądanie utworzenia puli zawierającej cztery węzły z czterema dozwolonymi miejscami zadań na węzeł. Określa zasady planowania zadań, które wypełnią każdy węzeł zadaniami przed przypisaniem zadań do innego węzła w puli.

Aby uzyskać więcej informacji na temat dodawania pul przy użyciu interfejsu API usługi Batch dla platformy .NET, zobacz [BatchClient.PoolOperations.CreatePool.](/dotnet/api/microsoft.azure.batch.pooloperations.createpool)

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicatedComputeNodes: 4
        virtualMachineSize: "standard_d1_v2",
        VirtualMachineConfiguration: new VirtualMachineConfiguration(
            imageReference: new ImageReference(
                                publisher: "MicrosoftWindowsServer",
                                offer: "WindowsServer",
                                sku: "2019-datacenter-core",
                                version: "latest"),
            nodeAgentSkuId: "batch.node.windows amd64");

pool.TaskSlotsPerNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

### <a name="create-a-task-with-required-slots"></a>Tworzenie zadania z wymaganymi miejscami

Ten fragment kodu tworzy zadanie o wartości spoza domyślnej `requiredSlots` . To zadanie zostanie uruchomione tylko wtedy, gdy w węźle obliczeniowym będzie dostępna wystarczająca ilość wolnych miejsc.

```csharp
CloudTask task = new CloudTask(taskId, taskCommandLine)
{
    RequiredSlots = 2
};
```

### <a name="list-compute-nodes-with-counts-for-running-tasks-and-slots"></a>Lista węzłów obliczeniowych z liczbami uruchomionych zadań i gniazd

Ten fragment kodu wyświetla listę wszystkich węzłów obliczeniowych w puli i wyświetla liczbę uruchomionych zadań podrzędnych i gniazd zadań na węzeł.

```csharp
ODATADetailLevel nodeDetail = new ODATADetailLevel(selectClause: "id,runningTasksCount,runningTaskSlotsCount");
IPagedEnumerable<ComputeNode> nodes = batchClient.PoolOperations.ListComputeNodes(poolId, nodeDetail);

await nodes.ForEachAsync(node =>
{
    Console.WriteLine(node.Id + " :");
    Console.WriteLine($"RunningTasks = {node.RunningTasksCount}, RunningTaskSlots = {node.RunningTaskSlotsCount}");

}).ConfigureAwait(continueOnCapturedContext: false);
```

### <a name="list-task-counts-for-the-job"></a>Lista liczników zadań dla zadania

Ten fragment kodu pobiera liczby zadań dla zadania, co obejmuje zarówno liczbę zadań, jak i gniazd zadań na stan zadania.

```csharp
TaskCountsResult result = await batchClient.JobOperations.GetJobTaskCountsAsync(jobId);

Console.WriteLine("\t\tActive\tRunning\tCompleted");
Console.WriteLine($"TaskCounts:\t{result.TaskCounts.Active}\t{result.TaskCounts.Running}\t{result.TaskCounts.Completed}");
Console.WriteLine($"TaskSlotCounts:\t{result.TaskSlotCounts.Active}\t{result.TaskSlotCounts.Running}\t{result.TaskSlotCounts.Completed}");
```

## <a name="batch-rest-example"></a>Przykład rest usługi Batch

Poniższe fragmenty [kodu interfejsu](/rest/api/batchservice/) API REST usługi Batch pokazują, jak utworzyć pulę z wieloma miejscami zadań na węzeł i jak przesłać zadanie z wymaganymi miejscami.

### <a name="create-a-pool-with-multiple-task-slots-per-node"></a>Tworzenie puli z wieloma miejscami zadań na węzeł

Ten fragment kodu przedstawia żądanie utworzenia puli zawierającej dwa duże węzły z maksymalnie czterema zadaniami na węzeł.

Aby uzyskać więcej informacji na temat dodawania pul przy użyciu interfejsu API REST, zobacz [Dodawanie puli do konta](/rest/api/batchservice/pool/add).

```json
{
  "odata.metadata":"https://myaccount.myregion.batch.azure.com/$metadata#pools/@Element",
  "id":"mypool",
  "vmSize":"large",
  "virtualMachineConfiguration": {
    "imageReference": {
      "publisher": "canonical",
      "offer": "ubuntuserver",
      "sku": "18.04-lts"
    },
    "nodeAgentSKUId": "batch.node.ubuntu 16.04"
  },
  "targetDedicatedComputeNodes":2,
  "taskSlotsPerNode":4,
  "enableInterNodeCommunication":true,
}
```

### <a name="create-a-task-with-required-slots"></a>Tworzenie zadania z wymaganymi miejscami

Ten fragment kodu przedstawia żądanie dodania zadania z wartością niedomyślną `requiredSlots` . To zadanie zostanie uruchomione tylko wtedy, gdy w węźle obliczeniowym jest dostępna wystarczająca ilość wolnych miejsc.

```json
{
  "id": "taskId",
  "commandLine": "bash -c 'echo hello'",
  "userIdentity": {
    "autoUser": {
      "scope": "task",
      "elevationLevel": "nonadmin"
    }
  },
  "requiredSLots": 2
}
```

## <a name="code-sample-on-github"></a>Przykładowy kod w witrynie GitHub

Projekt [ParallelTasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks) w usłudze GitHub ilustruje użycie właściwości [CloudPool.TaskSlotsPerNode.](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode)

Ta aplikacja konsolowa w języku C# używa biblioteki [.NET usługi Batch](/dotnet/api/microsoft.azure.batch) do tworzenia puli z co najmniej jednym węzłem obliczeniowym. Wykonuje on konfigurowalna liczba zadań podrzędnych w tych węzłach w celu symulowania obciążenia zmiennego. Dane wyjściowe aplikacji pokazują, które węzły wykonały każde zadanie. Aplikacja udostępnia również podsumowanie parametrów zadania i czasu trwania.

Poniżej przedstawiono przykładową część danych wyjściowych z dwóch różnych przebiegów przykładowej aplikacji ParallelTasks. Przedstawione tutaj czasy trwania zadań nie obejmują czasu tworzenia puli, ponieważ każde zadanie zostało przesłane do wcześniej utworzonej puli, której węzły obliczeniowe były w stanie *bezczynności* w czasie przesyłania.

Pierwsze wykonanie przykładowej aplikacji pokazuje, że w przypadku pojedynczego węzła w puli i domyślnego ustawienia jednego zadania na węzeł czas trwania zadania wynosi ponad 30 minut.

```
Nodes: 1
Node size: large
Task slots per node: 1
Max slots per task: 1
Tasks: 32
Duration: 00:30:01.4638023
```

Drugi przebieg próbki pokazuje znaczny spadek czasu trwania zadania. Wynika to z tego, że pula została skonfigurowana z czterema zadaniami w każdym węźle, co umożliwia wykonywanie zadań równoległych w ciągu niemal kwartału czasu.

```
Nodes: 1
Node size: large
Task slots per node: 4
Max slots per task: 1
Tasks: 32
Duration: 00:08:48.2423500
```

## <a name="next-steps"></a>Następne kroki

- Wypróbuj mapę [Batch Explorer](https://azure.github.io/BatchExplorer/) cieplną. Batch Explorer to bezpłatne, bogate w funkcje, autonomiczne narzędzie klienckie, które pomaga tworzyć, debugować i monitorować Azure Batch aplikacji. Podczas wykonywania przykładowej aplikacji [ParallelTasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks) funkcja mapy cieplnej Batch Explorer umożliwia łatwe wizualizowanie wykonywania równoległych zadań w każdym węźle.
- Zapoznaj [się Azure Batch przykładami w witrynie GitHub.](https://github.com/Azure/azure-batch-samples)
- Dowiedz się więcej o [zależnościach zadań usługi Batch.](batch-task-dependencies.md)
