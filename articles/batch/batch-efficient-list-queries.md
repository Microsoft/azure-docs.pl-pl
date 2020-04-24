---
title: Projektuj wydajne zapytania dotyczące list
description: Zwiększenie wydajności przez filtrowanie zapytań podczas żądania informacji o zasobach wsadowych, takich jak pule, zadania, zadania i węzły obliczeniowe.
ms.topic: article
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: fea8efd4e4946b67754bad98589b728e8d696425
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82116115"
---
# <a name="create-queries-to-list-batch-resources-efficiently"></a>Twórz zapytania umożliwiające wydajne wyświetlanie zasobów wsadowych

Tutaj dowiesz się, jak zwiększyć wydajność aplikacji Azure Batch, zmniejszając ilość danych zwracanych przez usługę podczas wykonywania zapytań dotyczących zadań, zadań, węzłów obliczeniowych i innych zasobów przy użyciu biblioteki usługi [Batch platformy .NET][api_net] .

Niemal wszystkie aplikacje usługi Batch wymagają wykonania pewnego typu monitorowania lub innej operacji, która wysyła zapytanie do usługi Batch, często w regularnych odstępach czasu. Na przykład aby określić, czy w zadaniu istnieją wszystkie zadania w kolejce, należy pobrać dane dla każdego zadania w zadaniu. Aby określić stan węzłów w puli, należy pobrać dane w każdym węźle puli. W tym artykule wyjaśniono, jak wykonywać takie zapytania w najbardziej wydajny sposób.

> [!NOTE]
> Usługa Batch oferuje specjalną obsługę interfejsu API dla typowych scenariuszy zliczania zadań w ramach zadania i zliczania węzłów obliczeniowych w puli usługi Batch. Zamiast korzystać z zapytania dotyczącego listy, można wywołać operacje [pobierania liczby zadań][rest_get_task_counts] i [węzłów puli listy][rest_get_node_counts] . Te operacje są bardziej wydajne niż zapytanie dotyczące listy, ale zwracają więcej informacji. Zobacz [liczenie zadań i węzłów obliczeniowych według stanu](batch-get-resource-counts.md). 


## <a name="meet-the-detaillevel"></a>Zapoznaj się z DetailLevel
W produkcyjnej aplikacji wsadowej jednostki takie jak zadania, zadania i węzły obliczeniowe mogą być liczbami w tysiącach. Gdy żądasz informacji o tych zasobach, potencjalnie dużą ilością danych musi być "krzyżowy" z usługi Batch do aplikacji w każdym zapytaniu. Ograniczając liczbę elementów i typ informacji zwracanych przez zapytanie, można zwiększyć szybkość zapytań i w związku z tym wydajność aplikacji.

Ten fragment kodu interfejsu API usługi [Batch .NET][api_net] *zawiera listę wszystkich zadań* , które są skojarzone z zadaniem, wraz ze *wszystkimi* właściwościami każdego zadania:

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

Można jednak wykonać znacznie bardziej wydajne zapytanie dotyczące listy, stosując "poziom szczegółowości" do zapytania. W tym celu należy dostarczyć obiekt [ODATADetailLevel][odata] do metody [JobOperations. ListTasks][net_list_tasks] . Ten fragment kodu zwraca tylko właściwości identyfikatora, wiersza polecenia i informacji o węzłach obliczeniowych wykonanych zadań:

```csharp
// Configure an ODATADetailLevel specifying a subset of tasks and
// their properties to return
ODATADetailLevel detailLevel = new ODATADetailLevel();
detailLevel.FilterClause = "state eq 'completed'";
detailLevel.SelectClause = "id,commandLine,nodeInfo";

// Supply the ODATADetailLevel to the ListTasks method
IPagedEnumerable<CloudTask> completedTasks =
    batchClient.JobOperations.ListTasks("job-001", detailLevel);
```

W tym przykładowym scenariuszu, jeśli w zadaniu istnieją tysiące zadań, wyniki drugiego zapytania są zwykle zwracane znacznie szybciej niż w pierwszej kolejności. Więcej informacji na temat korzystania z usługi ODATADetailLevel podczas wyświetlania listy elementów z interfejsem API usługi Batch .NET znajduje się [poniżej](#efficient-querying-in-batch-net).

> [!IMPORTANT]
> Zdecydowanie zalecamy, aby *zawsze* dostarczyć obiekt ODATADetailLevel do listy interfejsów API platformy .NET w celu zapewnienia maksymalnej wydajności i wydajności aplikacji. Określając poziom szczegółowości, można ułatwić skrócenie czasów odpowiedzi usługi Batch, zwiększyć wykorzystanie sieci i zminimalizować użycie pamięci przez aplikacje klienckie.
> 
> 

## <a name="filter-select-and-expand"></a>Filtrowanie, wybieranie i rozszerzanie
Interfejsy API usługi [Batch .NET][api_net] i usługi [Batch][api_rest] umożliwiają zredukowanie liczby elementów, które są zwracane na liście, a także ilości informacji zwracanych dla każdego z nich. W tym celu należy określić parametry **Filter**, **SELECT**i **expand** podczas wykonywania zapytań dotyczących list.

### <a name="filter"></a>Filtr
Ciąg filtru jest wyrażeniem, które zmniejsza liczbę zwracanych elementów. Na przykład Wyświetl listę tylko uruchomionych zadań dla zadania lub Wyświetl tylko węzły obliczeniowe, które są gotowe do uruchamiania zadań.

* Ciąg filtru składa się z co najmniej jednego wyrażenia z wyrażeniem, które składa się z nazwy właściwości, operatora i wartości. Właściwości, które można określić, są specyficzne dla każdego typu obiektu, który jest zaszukiwany, jako operatory obsługiwane dla każdej właściwości.
* Wiele wyrażeń można łączyć za pomocą operatorów `and` logicznych i. `or`
* Ten przykładowy ciąg filtru zawiera listę tylko uruchomionych zadań renderowania: `(state eq 'running') and startswith(id, 'renderTask')`.

### <a name="select"></a>Wybierz pozycję
Wybór ciągu ogranicza wartości właściwości, które są zwracane dla każdego elementu. Należy określić listę nazw właściwości i tylko te wartości właściwości są zwracane dla elementów w wynikach zapytania.

* Wybór ciągu składa się z listy nazw właściwości rozdzielanych przecinkami. Można określić dowolną właściwość dla typu jednostki, dla którego jest wysyłana kwerenda.
* W tym przykładzie wybrano ciąg określający, że dla każdego zadania należy zwrócić tylko trzy `id, state, stateTransitionTime`wartości właściwości:.

### <a name="expand"></a>Rozwiń
Ciąg rozszerzający zmniejsza liczbę wywołań interfejsu API, które są wymagane do uzyskania określonych informacji. W przypadku korzystania z ciągu rozszerzonego więcej informacji na temat każdego elementu można uzyskać przy użyciu jednego wywołania interfejsu API. Zamiast najpierw uzyskać listę jednostek, a następnie zażądać informacji dla każdego elementu na liście, należy użyć ciągu rozszerzonego, aby uzyskać te same informacje w jednym wywołaniu interfejsu API. Mniejsze wywołania interfejsu API to lepsza wydajność.

* Podobnie jak w przypadku wybrania ciągu, rozszerzający ciąg Określa, czy niektóre dane są zawarte w wynikach zapytania listy.
* Ciąg rozszerzający jest obsługiwany tylko w przypadku, gdy jest używany podczas wyświetlania listy zadań, harmonogramów zadań, zadań i pul. Obecnie obsługuje tylko informacje statystyczne.
* Gdy wszystkie właściwości są wymagane i nie określono żadnego ciągu, do pobrania informacji statystycznych *należy* użyć ciągu rozwijanego. Jeśli do uzyskania podzestawu właściwości zostanie użyty ciąg wyboru, `stats` można go określić w ciągu wybierania, a nie trzeba określać ciągu rozwijania.
* Ten przykład rozszerzający ciąg Określa, że informacje statystyczne powinny być zwracane dla każdego elementu na `stats`liście:.

> [!NOTE]
> Podczas konstruowania dowolnego z trzech typów ciągu zapytania (filtrowanie, wybieranie i rozszerzanie), należy się upewnić, że nazwy właściwości i wielkości liter są zgodne z odpowiednikami elementów interfejsu API REST. Na przykład podczas pracy z klasą .NET [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) należy określić **stan** zamiast **stanu**, mimo że właściwość .net ma wartość [CloudTask. State](/dotnet/api/microsoft.azure.batch.cloudtask.state#Microsoft_Azure_Batch_CloudTask_State). Zapoznaj się z tabelami poniżej, aby uzyskać mapowania właściwości między interfejsami API platformy .NET i REST.
> 
> 

### <a name="rules-for-filter-select-and-expand-strings"></a>Reguły filtrowania, wybierania i rozszerzania ciągów
* Nazwy właściwości w parametrach Filter, Select i expand powinny wyglądać tak jak w interfejsie API [REST usługi Batch][api_rest] — nawet w przypadku korzystania z usługi [Batch .NET][api_net] lub jednego z innych zestawów SDK usługi Batch.
* We wszystkich nazwach właściwości jest rozróżniana wielkość liter, ale w wartościach właściwości nie jest rozróżniana wielkość liter.
* Ciągi daty i godziny mogą być jednym z dwóch formatów i muszą być poprzedzone znakiem `DateTime`.
  
  * Przykład formatu W3C-DTF:`creationTime gt DateTime'2011-05-08T08:49:37Z'`
  * Przykład formatu RFC 1123:`creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
* Ciągi logiczne są `true` albo `false`.
* Jeśli określono nieprawidłową właściwość lub operator, zostanie `400 (Bad Request)` zwrócony błąd.

## <a name="efficient-querying-in-batch-net"></a>Wydajne wykonywanie zapytań w usłudze Batch .NET
W ramach interfejsu API usługi [Batch .NET][api_net] Klasa [ODATADetailLevel][odata] jest używana do dostarczania filtrów, wybierania i rozszerzania ciągów w celu wyświetlenia listy operacji. Klasa ODataDetailLevel ma trzy publiczne właściwości ciągu, które można określić w konstruktorze lub ustawić bezpośrednio dla obiektu. Następnie można przekazać obiekt ODataDetailLevel jako parametr do różnych operacji listy, takich jak [ListPools][net_list_pools], [ListJobs][net_list_jobs]i [ListTasks][net_list_tasks].

* [ODATADetailLevel][odata]. [FilterClause][odata_filter]: Ogranicz liczbę zwracanych elementów.
* [ODATADetailLevel][odata]. [SelectClause][odata_select]: Określ, które wartości właściwości są zwracane dla każdego elementu.
* [ODATADetailLevel][odata]. [ExpandClause][odata_expand]: Pobierz dane dla wszystkich elementów w jednym wywołaniu interfejsu API zamiast oddzielnych wywołań dla każdego elementu.

Poniższy fragment kodu używa interfejsu API usługi Batch .NET do wydajnego wykonywania zapytań w usłudze Batch w przypadku statystyk określonego zestawu pul. W tym scenariuszu użytkownik usługi Batch ma zarówno pulę testową, jak i produkcyjną. Identyfikatory puli testów są poprzedzone prefiksem "test", a identyfikatory puli produkcyjnej są poprzedzone "prod". W tym fragmencie kodu *myBatchClient* jest prawidłowo zainicjowany wystąpienie klasy [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient) .

```csharp
// First we need an ODATADetailLevel instance on which to set the filter, select,
// and expand clause strings
ODATADetailLevel detailLevel = new ODATADetailLevel();

// We want to pull only the "test" pools, so we limit the number of items returned
// by using a FilterClause and specifying that the pool IDs must start with "test"
detailLevel.FilterClause = "startswith(id, 'test')";

// To further limit the data that crosses the wire, configure the SelectClause to
// limit the properties that are returned on each CloudPool object to only
// CloudPool.Id and CloudPool.Statistics
detailLevel.SelectClause = "id, stats";

// Specify the ExpandClause so that the .NET API pulls the statistics for the
// CloudPools in a single underlying REST API call. Note that we use the pool's
// REST API element name "stats" here as opposed to "Statistics" as it appears in
// the .NET API (CloudPool.Statistics)
detailLevel.ExpandClause = "stats";

// Now get our collection of pools, minimizing the amount of data that is returned
// by specifying the detail level that we configured above
List<CloudPool> testPools =
    await myBatchClient.PoolOperations.ListPools(detailLevel).ToListAsync();
```

> [!TIP]
> Wystąpienie [ODATADetailLevel][odata] , które jest skonfigurowane z klauzulami SELECT i expand, można również przesłać do odpowiednich metod get, takich jak [PoolOperations. getpool](/dotnet/api/microsoft.azure.batch.pooloperations.getpool#Microsoft_Azure_Batch_PoolOperations_GetPool_System_String_Microsoft_Azure_Batch_DetailLevel_System_Collections_Generic_IEnumerable_Microsoft_Azure_Batch_BatchClientBehavior__), aby ograniczyć ilość zwracanych danych.
> 
> 

## <a name="batch-rest-to-net-api-mappings"></a>Mapowania usługi Batch do mapowań interfejsów API platformy .NET
Nazwy właściwości w parametrach Filter, Select i expand *muszą* odzwierciedlać odpowiedniki interfejsu API REST, zarówno w nazwach, jak i w przypadku. Poniższe tabele zapewniają mapowania między odpowiednikami interfejsów API .NET i REST.

### <a name="mappings-for-filter-strings"></a>Mapowania dla ciągów filtru
* **Metody list platformy .NET**: Każda z metod interfejsu API platformy .NET w tej kolumnie akceptuje obiekt [ODATADetailLevel][odata] jako parametr.
* **Żądania list REST**: Każda Strona interfejsu API REST połączona z tą kolumną zawiera tabelę, która określa właściwości i operacje, które są dozwolone w ciągach *filtru* . Te nazwy właściwości i operacje będą używane podczas konstruowania ciągu [ODATADetailLevel. FilterClause][odata_filter] .

| Metody list platformy .NET | Żądania listy REST |
| --- | --- |
| [Metody certificateoperations. ListCertificates][net_list_certs] |[Wyświetlanie listy certyfikatów na koncie][rest_list_certs] |
| [CloudTask. ListNodeFiles][net_list_task_files] |[Wyświetl listę plików skojarzonych z zadaniem][rest_list_task_files] |
| [JobOperations. ListJobPreparationAndReleaseTaskStatus][net_list_jobprep_status] |[Lista stanów zadań przygotowania i zwolnienia zadań dla zadania][rest_list_jobprep_status] |
| [JobOperations. ListJobs][net_list_jobs] |[Wyświetlanie listy zadań w ramach konta][rest_list_jobs] |
| [JobOperations. ListNodeFiles][net_list_nodefiles] |[Wyświetlanie listy plików w węźle][rest_list_nodefiles] |
| [JobOperations. ListTasks][net_list_tasks] |[Wyświetl listę zadań skojarzonych z zadaniem][rest_list_tasks] |
| [JobScheduleOperations.ListJobSchedules][net_list_job_schedules] |[Wyświetlanie listy harmonogramów zadań w ramach konta][rest_list_job_schedules] |
| [JobScheduleOperations.ListJobs][net_list_schedule_jobs] |[Wyświetl listę zadań skojarzonych z harmonogramem zadań][rest_list_schedule_jobs] |
| [PoolOperations. ListComputeNodes][net_list_compute_nodes] |[Wyświetlanie listy węzłów obliczeniowych w puli][rest_list_compute_nodes] |
| [PoolOperations. ListPools][net_list_pools] |[Wyświetlanie listy pul na koncie][rest_list_pools] |

### <a name="mappings-for-select-strings"></a>Mapowania dla wybranych ciągów
* **Typy usługi Batch .NET**: typy interfejsu API usługi Batch .NET.
* **Jednostki interfejsu API REST**: Każda Strona w tej kolumnie zawiera co najmniej jedną tabelę, w której znajduje się lista nazw właściwości interfejsu API REST dla tego typu. Te nazwy właściwości są używane podczas konstruowania *wybranych* ciągów. Te same nazwy właściwości będą używane podczas konstruowania ciągu [ODATADetailLevel. SelectClause][odata_select] .

| Typy wsadowe .NET | Jednostki interfejsu API REST |
| --- | --- |
| [Certyfikat][net_cert] |[Uzyskaj informacje na temat certyfikatu][rest_get_cert] |
| [CloudJob][net_job] |[Uzyskaj informacje o zadaniu][rest_get_job] |
| [CloudJobSchedule][net_schedule] |[Uzyskaj informacje o harmonogramie zadań][rest_get_schedule] |
| [ComputeNode][net_node] |[Pobierz informacje o węźle][rest_get_node] |
| [CloudPool][net_pool] |[Pobierz informacje o puli][rest_get_pool] |
| [CloudTask][net_task] |[Uzyskaj informacje o zadaniu][rest_get_task] |

## <a name="example-construct-a-filter-string"></a>Przykład: konstruowanie ciągu filtru
Podczas konstruowania ciągu filtru dla [ODATADetailLevel. FilterClause][odata_filter], zapoznaj się z powyższą tabelą w sekcji "mapowania dla ciągów filtrów", aby znaleźć stronę dokumentacji interfejsu API REST, która odpowiada operacji listy, którą chcesz wykonać. Właściwości z możliwością filtrowania i ich obsługiwane operatory są dostępne w pierwszej tabeli multirow na tej stronie. Jeśli chcesz pobrać wszystkie zadania, których kod zakończenia miał wartość różną od zera, na przykład ten wiersz na [liście zadań skojarzonych z zadaniem][rest_list_tasks] określa odpowiedni ciąg właściwości i dozwolone operatory:

| Właściwość | Dozwolone operacje | Typ |
|:--- |:--- |:--- |
| `executionInfo/exitCode` |`eq, ge, gt, le , lt` |`Int` |

W tym celu ciąg filtru służący do tworzenia listy wszystkich zadań o niezerowym kodzie zakończenia zostałby następujący:

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>Przykład: konstruowanie ciągu wyboru
Aby skonstruować [ODATADetailLevel. SelectClause][odata_select], zapoznaj się z powyższą tabelą w sekcji "mapowania dla wybranych ciągów" i przejdź do strony interfejsu API REST, która odnosi się do typu jednostki, która jest wyświetlona. Możliwe do wyboru właściwości i ich obsługiwane operatory zostaną znalezione w pierwszej tabeli multirow na tej stronie. Jeśli chcesz pobrać tylko identyfikator i wiersz polecenia dla każdego zadania na liście, możesz na przykład znaleźć te wiersze w odpowiedniej tabeli, aby [uzyskać informacje o zadaniu][rest_get_task]:

| Właściwość | Typ | Uwagi |
|:--- |:--- |:--- |
| `id` |`String` |`The ID of the task.` |
| `commandLine` |`String` |`The command line of the task.` |

Wybór ciągu, który obejmuje tylko identyfikator i wiersz polecenia z każdym z wymienionych zadań, będzie:

`id, commandLine`

## <a name="code-samples"></a>Przykłady kodu
### <a name="efficient-list-queries-code-sample"></a>Przykładowy kod zapytań dotyczących listy
Zapoznaj się z przykładowym projektem [EfficientListQueries][efficient_query_sample] w usłudze GitHub, aby zobaczyć, jak wydajne wykonywanie zapytań na liście może wpłynąć na wydajność aplikacji. Ta Aplikacja konsolowa w języku C# tworzy i dodaje do zadania dużą liczbę zadań. Następnie tworzy wiele wywołań metody [JobOperations. ListTasks][net_list_tasks] i przekazuje obiekty [ODATADetailLevel][odata] , które są skonfigurowane przy użyciu różnych wartości właściwości w celu zróżnicowania ilości danych do zwrócenia. Generuje dane wyjściowe podobne do następujących:

```
Adding 5000 tasks to job jobEffQuery...
5000 tasks added in 00:00:47.3467587, hit ENTER to query tasks...

4943 tasks retrieved in 00:00:04.3408081 (ExpandClause:  | FilterClause: state eq 'active' | SelectClause: id,state)
0 tasks retrieved in 00:00:00.2662920 (ExpandClause:  | FilterClause: state eq 'running' | SelectClause: id,state)
59 tasks retrieved in 00:00:00.3337760 (ExpandClause:  | FilterClause: state eq 'completed' | SelectClause: id,state)
5000 tasks retrieved in 00:00:04.1429881 (ExpandClause:  | FilterClause:  | SelectClause: id,state)
5000 tasks retrieved in 00:00:15.1016127 (ExpandClause:  | FilterClause:  | SelectClause: id,state,environmentSettings)
5000 tasks retrieved in 00:00:17.0548145 (ExpandClause: stats | FilterClause:  | SelectClause: )

Sample complete, hit ENTER to continue...
```

Jak pokazano w porach czasu, można znacznie obniżyć czas odpowiedzi na zapytania, ograniczając właściwości i liczbę zwracanych elementów. Te i inne przykładowe projekty można znaleźć w repozytorium [Azure-Batch-Samples][github_samples] w witrynie GitHub.

### <a name="batchmetrics-library-and-code-sample"></a>Przykładowy kod i Biblioteka BatchMetrics
Oprócz powyższego przykładu kodu EfficientListQueries można znaleźć projekt [BatchMetrics][batch_metrics] w repozytorium GitHub [Azure-Batch-Samples][github_samples] . Przykładowy projekt BatchMetrics pokazuje, jak skutecznie monitorować postęp zadania Azure Batch przy użyciu interfejsu API usługi Batch.

Przykład [BatchMetrics][batch_metrics] zawiera projekt biblioteki klas .NET, który można dołączyć do własnych projektów, oraz prosty program wiersza polecenia do wykonywania i zademonstrowania użycia biblioteki.

Przykładowa aplikacja w ramach projektu pokazuje następujące operacje:

1. Wybieranie określonych atrybutów w celu pobrania tylko potrzebnych właściwości
2. Filtrowanie czasów przejścia stanu, aby pobierać tylko zmiany od ostatniego zapytania

Na przykład następująca metoda pojawia się w bibliotece BatchMetrics. Zwraca ODATADetailLevel, który określa, że tylko właściwości `id` i `state` powinny być pobierane dla jednostek, które są zapytania. Określa również, że należy zwrócić tylko jednostki, których stan zmienił się `DateTime` od określonego parametru.

```csharp
internal static ODATADetailLevel OnlyChangedAfter(DateTime time)
{
    return new ODATADetailLevel(
        selectClause: "id, state",
        filterClause: string.Format("stateTransitionTime gt DateTime'{0:o}'", time)
    );
}
```

## <a name="next-steps"></a>Następne kroki
### <a name="parallel-node-tasks"></a>Równoległe zadania węzła
[Maksymalizuj Azure Batch użycie zasobów obliczeniowych za pomocą współbieżnych zadań węzła](batch-parallel-node-tasks.md) to inny artykuł dotyczący wydajności aplikacji wsadowych. Niektóre typy obciążeń mogą korzystać z wykonywania równoległych zadań w większych, ale mniejszych węzłach obliczeniowych. Zapoznaj się z [przykładowym scenariuszem](batch-parallel-node-tasks.md#example-scenario) w artykule, aby uzyskać szczegółowe informacje na ten temat.


[api_net]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: https://docs.microsoft.com/rest/api/batchservice/
[batch_metrics]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchMetrics
[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[github_samples]: https://github.com/Azure/azure-batch-samples
[odata]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[odata_ctor]: https://msdn.microsoft.com/library/azure/dn866178.aspx
[odata_expand]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx
[odata_filter]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx
[odata_select]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[rest_list_certs]: https://msdn.microsoft.com/library/azure/dn820154.aspx
[rest_list_compute_nodes]: https://msdn.microsoft.com/library/azure/dn820159.aspx
[rest_list_job_schedules]: https://msdn.microsoft.com/library/azure/mt282174.aspx
[rest_list_jobprep_status]: https://msdn.microsoft.com/library/azure/mt282170.aspx
[rest_list_jobs]: https://msdn.microsoft.com/library/azure/dn820117.aspx
[rest_list_nodefiles]: https://msdn.microsoft.com/library/azure/dn820151.aspx
[rest_list_pools]: https://msdn.microsoft.com/library/azure/dn820101.aspx
[rest_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/mt282169.aspx
[rest_list_task_files]: https://msdn.microsoft.com/library/azure/dn820142.aspx
[rest_list_tasks]: https://msdn.microsoft.com/library/azure/dn820187.aspx

[rest_get_cert]: https://msdn.microsoft.com/library/azure/dn820176.aspx
[rest_get_job]: https://msdn.microsoft.com/library/azure/dn820106.aspx
[rest_get_node]: https://msdn.microsoft.com/library/azure/dn820168.aspx
[rest_get_pool]: https://msdn.microsoft.com/library/azure/dn820165.aspx
[rest_get_schedule]: https://msdn.microsoft.com/library/azure/mt282171.aspx
[rest_get_task]: https://msdn.microsoft.com/library/azure/dn820133.aspx

[net_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificate.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_schedule]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjobschedule.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx

[rest_get_task_counts]: /rest/api/batchservice/get-the-task-counts-for-a-job
[rest_get_node_counts]: /rest/api/batchservice/account/listpoolnodecounts
