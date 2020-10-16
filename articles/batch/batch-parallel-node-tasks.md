---
title: Uruchom zadania współbieżnie, aby zmaksymalizować użycie węzłów obliczeniowych wsadowych
description: Zwiększenie wydajności i obniżenie kosztów dzięki użyciu mniejszej liczby węzłów obliczeniowych i uruchamiania zadań równolegle na każdym węźle w puli Azure Batch
ms.topic: how-to
ms.date: 10/08/2020
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: 8bc9f03f05d52df6e400be5c57033ab2a38fa8eb
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92102969"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Uruchom zadania współbieżnie, aby zmaksymalizować użycie węzłów obliczeniowych wsadowych

Można zmaksymalizować użycie zasobów na mniejszej liczbie węzłów obliczeniowych w puli, uruchamiając więcej niż jedno zadanie jednocześnie w każdym węźle.

Chociaż niektóre scenariusze działają najlepiej ze wszystkimi zasobami węzła przeznaczonymi dla jednego zadania, niektóre obciążenia mogą wyświetlać krótsze czasy zadań i obniżać koszty, gdy wiele zadań współużytkuje te zasoby:

- **Minimalizuj transfer danych** dla zadań, które mogą udostępniać dane. Można znacznie zmniejszyć opłaty za transfer danych przez skopiowanie danych udostępnionych do mniejszej liczby węzłów, a następnie wykonywanie zadań równolegle na każdym węźle. W szczególności ma to zastosowanie, jeśli dane, które mają być skopiowane do każdego węzła, muszą być transferowane między regionami geograficznymi.
- **Maksymalizuj użycie pamięci** dla zadań wymagających dużej ilości pamięci, ale tylko w krótkich okresach czasu i w zmiennym czasie wykonywania. Możesz użyć mniej, ale większych węzłów obliczeniowych z większą ilością pamięci, aby efektywnie obsłużyć takie skoki. Te węzły mają równolegle uruchomione wiele zadań w każdym węźle, ale każde zadanie będzie korzystać z plentiful pamięci węzłów w różnych godzinach.
- **Ograniczanie limitów liczby węzłów** , gdy komunikacja między węzłami jest wymagana w ramach puli. Obecnie pule skonfigurowane pod kątem komunikacji między węzłami są ograniczone do 50 węzłów obliczeniowych. Jeśli każdy węzeł w takiej puli ma możliwość równoległego wykonywania zadań, można jednocześnie wykonać większą liczbę zadań.
- **Replikacja lokalnego klastra obliczeniowego**, na przykład podczas pierwszego przenoszenia środowiska obliczeniowego na platformę Azure. Jeśli bieżące rozwiązanie lokalne wykonuje wiele zadań w każdym węźle obliczeniowym, można zwiększyć maksymalną liczbę zadań węzłów, aby dokładniej zdublować tę konfigurację.

## <a name="example-scenario"></a>Przykładowy scenariusz

Przykładowo Wyobraź sobie, że aplikacja zadania z wymaganiami dotyczącymi procesora CPU i pamięci, w taki sposób, że [standardowe węzły \_ D1](../cloud-services/cloud-services-sizes-specs.md) są wystarczające. Jednak w celu zakończenia zadania w wymaganym czasie 1 000 z tych węzłów są potrzebne.

Zamiast używać standardowych \_ węzłów D1, które mają 1 rdzeń procesora CPU, można użyć [standardowych węzłów \_ D14](../cloud-services/cloud-services-sizes-specs.md) o 16 rdzeniach i włączyć równoległe wykonywanie zadań. Oznacza to, że można użyć *16 razy mniej węzłów* — zamiast z 1 000 węzłów, wymagana będzie tylko 63. Jeśli dla każdego węzła wymagane są duże pliki aplikacji lub dane referencyjne, czas trwania zadania i wydajność są ponownie ulepszone, ponieważ dane są kopiowane tylko do 63 węzłów.

## <a name="enable-parallel-task-execution"></a>Włącz równoległe wykonywanie zadań

Węzły obliczeniowe można skonfigurować do równoległego wykonywania zadań na poziomie puli. Za pomocą biblioteki Batch .NET ustaw właściwość [CloudPool. TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) podczas tworzenia puli. Jeśli używasz interfejsu API REST usługi Batch, Ustaw element [taskSlotsPerNode](/rest/api/batchservice/pool/add) w treści żądania podczas tworzenia puli.

> [!NOTE]
> `taskSlotsPerNode`Właściwość element i [TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) można ustawić tylko w czasie tworzenia puli. Nie można ich modyfikować po utworzeniu puli.

Azure Batch pozwala ustawić gniazda zadań na węzeł do (4x) liczbę rdzeni węzła. Na przykład, jeśli Pula jest skonfigurowana z węzłami o rozmiarze "duże" (cztery rdzenie), wówczas `taskSlotsPerNode` może być ustawiona na 16. Jednak niezależnie od tego, ile rdzeni ma węzeł, nie można mieć więcej niż 256 gniazd zadań na węzeł. Aby uzyskać szczegółowe informacje o liczbie rdzeni dla każdego rozmiaru węzła, zobacz [rozmiary dla Cloud Services](../cloud-services/cloud-services-sizes-specs.md). Aby uzyskać więcej informacji na temat limitów usługi, zobacz limity [przydziału i limity dla usługi Azure Batch](batch-quota-limit.md).

> [!TIP]
> Pamiętaj, aby wziąć pod uwagę `taskSlotsPerNode` wartość podczas konstruowania [formuły skalowania automatycznego](/rest/api/batchservice/pool/enableautoscale) dla puli. Na przykład `$RunningTasks` można znacznie wpływać na liczbę zadań na węzeł. Aby uzyskać więcej informacji, zobacz [Automatyczne skalowanie węzłów obliczeniowych w puli Azure Batch](batch-automatic-scaling.md).

## <a name="specify-task-distribution"></a>Określ rozkład zadań

Podczas włączania współbieżnych zadań należy określić, w jaki sposób zadania mają być dystrybuowane między węzłami w puli.

Za pomocą właściwości [CloudPool. TaskSchedulingPolicy](/dotnet/api/microsoft.azure.batch.cloudpool) można określić, że zadania mają być przypisywane równomiernie we wszystkich węzłach w puli ("rozpraszanie"). Można też określić, że możliwie jak najwięcej zadań należy przypisać do każdego węzła przed przypisaniem zadań do innego węzła w puli ("pakowanie").

Przykładowo Rozważmy pulę [standardowych węzłów \_ D14](../cloud-services/cloud-services-sizes-specs.md) (w powyższym przykładzie), dla których skonfigurowano wartość [CloudPool. TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) równą 16. Jeśli [CloudPool. TaskSchedulingPolicy](/dotnet/api/microsoft.azure.batch.cloudpool) jest skonfigurowany przy użyciu [ComputeNodeFillType](/dotnet/api/microsoft.azure.batch.common.computenodefilltype) *pakietu*, maksymalizuje użycie wszystkich 16 rdzeni każdego węzła i umożliwi [puli skalowania](batch-automatic-scaling.md) automatycznego usunięcie nieużywanych węzłów (węzłów bez przypisanych zadań) z puli. Pozwala to zminimalizować użycie zasobów i zaoszczędzić pieniądze.

## <a name="define-variable-slots-per-task"></a>Definiuj zmienne gniazd na zadanie

Zadanie można zdefiniować za pomocą właściwości [CloudTask. RequiredSlots](/dotnet/api/microsoft.azure.batch.cloudtask.requiredslots) , określając, ile gniazd wymaga do uruchomienia w węźle obliczeniowym. Wartość domyślna to 1. Można ustawić zmienne gniazda zadań, jeśli zadania mają różne wagi dotyczące użycia zasobów w węźle obliczeniowym. Dzięki temu każdy węzeł obliczeniowy ma odpowiednią liczbę współbieżnych zadań wykonywanych bez przeciążania zasobów systemowych, takich jak procesor CPU lub pamięć.

Na przykład dla puli z właściwością `taskSlotsPerNode = 8` można przesłać wiele rdzeni wymaganych zadań intensywnie korzystających z procesora CPU `requiredSlots = 8` , a inne zadania można ustawić na `requiredSlots = 1` . Po zaplanowaniu tego obciążenia mieszanego zadania intensywnie obciążające procesor CPU będą uruchamiane wyłącznie na ich węzłach obliczeniowych, podczas gdy inne zadania mogą być uruchamiane współbieżnie (do ośmiu zadań jednocześnie) w innych węzłach. Dzięki temu można zrównoważyć obciążenie w węzłach obliczeniowych i zwiększyć efektywność użycia zasobów.

> [!TIP]
> W przypadku korzystania z gniazd zadań zmiennych istnieje możliwość tymczasowego zaplanowania dużych zadań z większą liczbą wymaganych miejsc, ponieważ nie ma wystarczającej liczby miejsc w żadnym węźle obliczeniowym, nawet jeśli w niektórych węzłach nadal znajdują się bezczynne gniazda. Możesz podnieść priorytet zadania dla tych zadań, aby zwiększyć ich szansę konkurowania z dostępnymi gniazdami w węzłach.
>
> Usługa Batch emituje [TaskScheduleFailEvent](batch-task-schedule-fail-event.md) , gdy nie zaplanował uruchomienia zadania i kontynuuje proces planowania, dopóki wymagane gniazda staną się niedostępne. Można nasłuchiwać tego zdarzenia, aby wykryć potencjalne problemy związane z planowaniem zadań i odpowiednio wyeliminować.

> [!NOTE]
> Nie określaj zadania, `requiredSlots` które ma być większe niż Pula `taskSlotsPerNode` . Spowoduje to, że zadanie nigdy nie będzie można uruchomić. Usługa Batch nie sprawdza obecnie tego konfliktu podczas przesyłania zadań, ponieważ zadanie może nie mieć powiązanej puli w czasie przesyłania lub można je zmienić na inną pulę przez wyłączenie/ponowne włączenie.

## <a name="batch-net-example"></a>Przykład platformy .NET w usłudze Batch

Poniższe fragmenty kodu interfejsu API [programu .NET Batch](/dotnet/api/microsoft.azure.batch) pokazują, jak utworzyć pulę z wieloma gniazdami zadań na węzeł oraz jak przesłać zadanie z wymaganymi gniazdami.

### <a name="create-a-pool-with-multiple-task-slots-per-node"></a>Tworzenie puli z wieloma gniazdami zadań na węzeł

Ten fragment kodu przedstawia żądanie utworzenia puli zawierającej cztery węzły z czterema gniazdami zadań dozwolonymi na węzeł. Określa zasady planowania zadań, które będą wypełnić każdy węzeł zadaniami przed przypisaniem zadań do innego węzła w puli.

Aby uzyskać więcej informacji na temat dodawania pul przy użyciu interfejsu API usługi Batch platformy .NET, zobacz [BatchClient. PoolOperations. ispool](/dotnet/api/microsoft.azure.batch.pooloperations).

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicatedComputeNodes: 4
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

pool.TaskSlotsPerNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

### <a name="create-a-task-with-required-slots"></a>Tworzenie zadania z wymaganymi gniazdami

Ten fragment kodu tworzy zadanie z wartością niedomyślną `requiredSlots` . To zadanie zostanie uruchomione tylko wtedy, gdy na węźle obliczeniowym będzie dostępna wystarczająca liczba wolnych gniazd.

```csharp
CloudTask task = new CloudTask(taskId, taskCommandLine)
{
    RequiredSlots = 2
};
```

### <a name="list-compute-nodes-with-counts-for-running-tasks-and-slots"></a>Wyświetl listę węzłów obliczeniowych z licznikami dla uruchomionych zadań i gniazd

Ten fragment kodu przedstawia wszystkie węzły obliczeniowe w puli, a następnie drukuje liczniki do uruchamiania zadań i gniazd zadań na węzeł.

```csharp
ODATADetailLevel nodeDetail = new ODATADetailLevel(selectClause: "id,runningTasksCount,runningTaskSlotsCount");
IPagedEnumerable<ComputeNode> nodes = batchClient.PoolOperations.ListComputeNodes(poolId, nodeDetail);

await nodes.ForEachAsync(node =>
{
    Console.WriteLine(node.Id + " :");
    Console.WriteLine($"RunningTasks = {node.RunningTasksCount}, RunningTaskSlots = {node.RunningTaskSlotsCount}");

}).ConfigureAwait(continueOnCapturedContext: false);
```

### <a name="list-task-counts-for-the-job"></a>Lista liczb zadań dla zadania

Ten fragment kodu pobiera liczbę zadań dla zadania, w tym liczbę zadań i miejsc zadań dla każdego stanu zadania.

```csharp
TaskCountsResult result = await batchClient.JobOperations.GetJobTaskCountsAsync(jobId);

Console.WriteLine("\t\tActive\tRunning\tCompleted");
Console.WriteLine($"TaskCounts:\t{result.TaskCounts.Active}\t{result.TaskCounts.Running}\t{result.TaskCounts.Completed}");
Console.WriteLine($"TaskSlotCounts:\t{result.TaskSlotCounts.Active}\t{result.TaskSlotCounts.Running}\t{result.TaskSlotCounts.Completed}");
```

## <a name="batch-rest-example"></a>Przykład REST w usłudze Batch

Poniższe fragmenty kodu interfejsu API [REST usługi Batch](/rest/api/batchservice/) pokazują, jak utworzyć pulę z wieloma gniazdami zadań na węzeł oraz jak przesłać zadanie z wymaganymi gniazdami.

### <a name="create-a-pool-with-multiple-task-slots-per-node"></a>Tworzenie puli z wieloma gniazdami zadań na węzeł

Ten fragment kodu przedstawia żądanie utworzenia puli zawierającej dwa duże węzły z maksymalnie czterema zadaniami na węzeł.

Aby uzyskać więcej informacji na temat dodawania pul przy użyciu interfejsu API REST, zobacz [Dodawanie puli do konta](/rest/api/batchservice/pool/add).

```json
{
  "odata.metadata":"https://myaccount.myregion.batch.azure.com/$metadata#pools/@Element",
  "id":"mypool",
  "vmSize":"large",
  "cloudServiceConfiguration": {
    "osFamily":"4",
    "targetOSVersion":"*",
  },
  "targetDedicatedComputeNodes":2,
  "taskSlotsPerNode":4,
  "enableInterNodeCommunication":true,
}
```

### <a name="create-a-task-with-required-slots"></a>Tworzenie zadania z wymaganymi gniazdami

Ten fragment kodu przedstawia żądanie dodania zadania z wartością niedomyślną `requiredSlots` . To zadanie zostanie uruchomione tylko wtedy, gdy na węźle obliczeniowym będzie dostępna wystarczająca liczba wolnych gniazd.

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

## <a name="code-sample-on-github"></a>Przykład kodu w witrynie GitHub

Projekt [ParallelTasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks) w usłudze GitHub ilustruje użycie właściwości [CloudPool. TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) .

Ta Aplikacja konsolowa w języku C# używa biblioteki [programu .NET Batch](/dotnet/api/microsoft.azure.batch) do tworzenia puli z co najmniej jednym węzłem obliczeniowym. Wykonuje konfigurowalną liczbę zadań w tych węzłach, aby symulować obciążenie zmienne. Dane wyjściowe aplikacji pokazują, które węzły wykonali każde zadanie. Aplikacja zawiera również podsumowanie parametrów zadania i czasu trwania.

Poniżej znajduje się na przykład część podsumowania danych wyjściowych z dwóch różnych przebiegów przykładowej aplikacji ParallelTasks. Podane tu czasy trwania zadania nie obejmują czasu utworzenia puli, ponieważ każde zadanie zostało przesłane do wcześniej utworzonej puli, której węzły obliczeniowe były w stanie *bezczynności* w czasie przesyłania.

Pierwsze wykonanie przykładowej aplikacji pokazuje, że z pojedynczym węzłem w puli i domyślnym ustawieniem jednego zadania na węzeł, czas trwania zadania wynosi ponad 30 minut.

```
Nodes: 1
Node size: large
Task slots per node: 1
Max slots per task: 1
Tasks: 32
Duration: 00:30:01.4638023
```

Drugie uruchomienie przykładu pokazuje znaczny spadek czasu trwania zadania. Wynika to z faktu, że pula została skonfigurowana z czterema zadaniami na węzeł, co umożliwia równoległe wykonywanie zadania w prawie czwartym czasie.

```
Nodes: 1
Node size: large
Task slots per node: 4
Max slots per task: 1
Tasks: 32
Duration: 00:08:48.2423500
```

## <a name="next-steps"></a>Następne kroki

- Wypróbuj [Batch Explorerową](https://azure.github.io/BatchExplorer/) mapę cieplną. Batch Explorer to bezpłatne, bogate w funkcje, autonomiczne narzędzie klienta pomagające tworzyć, debugować i monitorować aplikacje Azure Batch. Gdy uruchamiasz przykładową aplikację [ParallelTasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks) , funkcja mapy ciepła Batch Explorer pozwala łatwo wizualizować wykonywanie równoległych zadań w każdym węźle.
- Poznaj [przykłady Azure Batch w witrynie GitHub](https://github.com/Azure/azure-batch-samples).
- Dowiedz się więcej o [zależnościach zadań wsadowych](batch-task-dependencies.md).
