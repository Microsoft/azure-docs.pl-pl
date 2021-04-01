---
title: Przesyłanie dużej liczby zadań do zadania wsadowego
description: Dowiedz się, jak efektywnie przesłać bardzo dużą liczbę zadań w ramach jednego Azure Batch zadania.
ms.topic: how-to
ms.date: 12/30/2020
ms.custom: devx-track-python, devx-track-csharp
ms.openlocfilehash: 08cf92507a4556afbf56c9cb7e2c9c1b3a6c9479
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97831520"
---
# <a name="submit-a-large-number-of-tasks-to-a-batch-job"></a>Przesyłanie dużej liczby zadań do zadania wsadowego

Po uruchomieniu obciążeń Azure Batch dużej skali można przesłać dziesiątki tysięcy, setki tysięcy lub jeszcze więcej zadań do jednego zadania.

W tym artykule przedstawiono sposób przesyłania dużej liczby zadań o znacznie większej przepływności do jednego zadania wsadowego. Po przesłaniu zadań wprowadzają kolejkę wsadową do przetwarzania w puli określonej dla danego zadania.

## <a name="use-task-collections"></a>Korzystanie z kolekcji zadań

W przypadku dodawania dużej liczby zadań należy użyć odpowiednich metod lub przeciążeń dostarczonych przez interfejsy API partii do dodawania zadań jako *kolekcji* , a nie pojedynczej. Ogólnie rzecz biorąc tworzysz kolekcję zadań przez Definiowanie zadań podczas iteracji nad zestawem plików wejściowych lub parametrów dla danego zadania.

Maksymalny rozmiar kolekcji zadań, którą można dodać w pojedynczym wywołaniu, zależy od używanego interfejsu API usługi Batch.

### <a name="apis-allowing-collections-of-up-to-100-tasks"></a>Interfejsy API zezwalające na kolekcje do 100 zadań

Te interfejsy API partii ograniczają kolekcję do 100 zadań. Limit może być mniejszy w zależności od rozmiaru zadań (na przykład jeśli zadania zawierają dużą liczbę plików zasobów lub zmiennych środowiskowych).

- [Interfejs API REST](/rest/api/batchservice/task/addcollection)
- [Interfejs API języka Python](/python/api/azure-batch/azure.batch.operations.TaskOperations)
- [Interfejs API Node.js](/javascript/api/@azure/batch/task)

Korzystając z tych interfejsów API, należy zapewnić logikę w celu podzielenia liczby zadań, które spełniają limit kolekcji, oraz do obsługi błędów i ponawiania prób w przypadku niepowodzenia dodawania zadań. Jeśli kolekcja zadań jest zbyt duża, aby można ją było dodać, żądanie generuje błąd i należy ponownie ponowić próbę z mniejszą liczbą zadań.

### <a name="apis-allowing-collections-of-larger-numbers-of-tasks"></a>Interfejsy API zezwalające na kolekcje o większej liczbie zadań

Inne interfejsy API w usłudze Batch obsługują znacznie większe kolekcje zadań, które są ograniczone tylko do dostępności pamięci RAM na kliencie przesyłania. Te interfejsy API w sposób przezroczysty obsługują dzielenie kolekcji zadań do "fragmentów" dla interfejsów API niższego poziomu i ponawianie prób w przypadku niepowodzenia dodawania zadań.

- [Interfejs API .NET](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync)
- [Interfejs API języka Java](/java/api/com.microsoft.azure.batch.protocol.tasks.addcollectionasync)
- [Azure Batch rozszerzenia interfejsu wiersza](batch-cli-templates.md) polecenia z szablonami interfejsu wiersza polecenia w usłudze Batch
- [Rozszerzenie zestawu SDK języka Python](https://pypi.org/project/azure-batch-extensions/)

## <a name="increase-throughput-of-task-submission"></a>Zwiększenie przepływności przesyłania zadań

Dodanie dużej kolekcji zadań do zadania może zająć trochę czasu. Na przykład dodawanie 20 000 zadań za pośrednictwem interfejsu API platformy .NET może trwać do jednej minuty. W zależności od interfejsu API usługi Batch i obciążenia można zwiększyć przepływność zadania, modyfikując co najmniej jedną z poniższych czynności.

### <a name="task-size"></a>Rozmiar zadania

Dodawanie dużych zadań trwa dłużej niż Dodawanie mniejszych. Aby zmniejszyć rozmiar każdego zadania w kolekcji, można uprościć wiersz polecenia zadania, zmniejszyć liczbę zmiennych środowiskowych lub obsłużyć bardziej wydajne wymagania dotyczące wykonywania zadań.

Na przykład zamiast korzystać z dużej liczby plików zasobów, należy zainstalować zależności zadań przy użyciu [zadania uruchamiania](jobs-and-tasks.md#start-task) w puli lub użyć [pakietu aplikacji](batch-application-packages.md) lub [kontenera Docker](batch-docker-container-workloads.md).

### <a name="number-of-parallel-operations"></a>Liczba operacji równoległych

W zależności od interfejsu API usługi Batch można zwiększyć przepływność, zwiększając maksymalną liczbę operacji współbieżnych przez klienta usługi Batch. Skonfiguruj to ustawienie za pomocą właściwości [BatchClientParallelOptions. MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) w interfejsie API platformy .NET lub `threads` parametru metod, takich jak [TaskOperations.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations) , w rozszerzeniu zestawu SDK języka Python usługi Batch. (Ta właściwość nie jest dostępna w natywnym zestawie SDK Python w usłudze Batch).

Domyślnie ta właściwość ma wartość 1, ale można ją ustawić na wyższą, aby zwiększyć przepływność operacji. Zwiększa się przepływność poprzez zużywanie przepustowości sieci i pewnej wydajności procesora CPU. Przepływność zadań zwiększa się o maksymalnie 100 razy `MaxDegreeOfParallelism` lub `threads` . W tym celu należy ustawić liczbę operacji współbieżnych poniżej 100.

 Rozszerzenie interfejsu wiersza polecenia Azure Batch przy użyciu szablonów wsadowych zwiększa liczbę współbieżnych operacji automatycznie na podstawie liczby dostępnych rdzeni, ale tej właściwości nie można skonfigurować w interfejsie wiersza polecenia.

### <a name="http-connection-limits"></a>Limity połączeń HTTP

Wiele współbieżnych połączeń HTTP może ograniczać wydajność klienta programu Batch podczas dodawania dużej liczby zadań. Niektóre interfejsy API ograniczają liczbę połączeń HTTP. Podczas tworzenia przy użyciu interfejsu API .NET na przykład właściwość [ServicePointManager. DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) jest domyślnie ustawiona na 2. Zalecamy zwiększenie wartości do liczby zbliżonej do liczby operacji równoległych lub większej.

## <a name="example-batch-net"></a>Przykład: Batch .NET

Poniższe fragmenty kodu w języku C# pokazują ustawienia do skonfigurowania podczas dodawania dużej liczby zadań przy użyciu interfejsu API usługi Batch platformy .NET.

Aby zwiększyć przepływność zadań, należy zwiększyć wartość właściwości [MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient). Na przykład:

```csharp
BatchClientParallelOptions parallelOptions = new BatchClientParallelOptions()
  {
    MaxDegreeOfParallelism = 15
  };
...
```

Dodaj kolekcję zadań do zadania przy użyciu odpowiedniego przeciążenia metody [AddTaskAsync](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync) lub [AddTask](/dotnet/api/microsoft.azure.batch.cloudjob.addtask
) . Na przykład:

```csharp
// Add a list of tasks as a collection
List<CloudTask> tasksToAdd = new List<CloudTask>(); // Populate with your tasks
...
await batchClient.JobOperations.AddTaskAsync(jobId, tasksToAdd, parallelOptions);
```

## <a name="example-batch-cli-extension"></a>Przykład: rozszerzenie Batch CLI

Korzystając z rozszerzeń interfejsu wiersza polecenia Azure Batch z [szablonami interfejsu wiersza polecenia usługi Batch](batch-cli-templates.md), Utwórz plik JSON szablonu zadania zawierający [fabrykę zadań](https://github.com/Azure/azure-batch-cli-extensions/blob/master/doc/taskFactories.md). Fabryka zadań konfiguruje kolekcję powiązanych zadań dla zadania z jednej definicji zadania.  

Poniżej znajduje się przykładowy szablon zadania dla jednowymiarowego zadania odchylenia z dużą liczbą zadań (w tym przypadku 250 000). Wiersz polecenia zadania to proste `echo` polecenie.

```json
{
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "constraints": {
                "maxWallClockTime": "PT5H",
                "maxTaskRetryCount": 1
            },
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime":"PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
```

Aby uruchomić zadanie z szablonem, zobacz [Korzystanie z szablonów interfejsu wiersza polecenia i transferu plików Azure Batch](batch-cli-templates.md).

## <a name="example-batch-python-sdk-extension"></a>Przykład: rozszerzenie Batch Python SDK

Aby użyć rozszerzenia Azure Batch Python SDK, najpierw zainstaluj zestaw SDK języka Python i rozszerzenie:

```
pip install azure-batch
pip install azure-batch-extensions
```

Skonfiguruj zestaw `BatchExtensionsClient` , który używa rozszerzenia zestawu SDK:

```python

client = batch.BatchExtensionsClient(
    base_url=BATCH_ACCOUNT_URL, resource_group=RESOURCE_GROUP_NAME, batch_account=BATCH_ACCOUNT_NAME)
...
```

Utwórz kolekcję zadań do dodania do zadania. Na przykład:

```python
tasks = list()
# Populate the list with your tasks
...
```

Dodaj kolekcję zadań przy użyciu [Task.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations). Ustaw `threads` parametr, aby zwiększyć liczbę operacji współbieżnych:

```python
try:
    client.task.add_collection(job_id, threads=100)
except Exception as e:
    raise e
```

Rozszerzenie Batch Python SDK obsługuje także Dodawanie parametrów zadań do zadania przy użyciu specyfikacji JSON dla fabryki zadań. Na przykład skonfiguruj parametry zadania dla odchylenia parametrycznego podobnego do tego w poprzednim przykładzie szablonu interfejsu wiersza polecenia usługi Batch:

```python
parameter_sweep = {
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime": "PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
...
job_json = client.job.expand_template(parameter_sweep)
job_parameter = client.job.jobparameter_from_json(job_json)
```

Dodaj parametry zadania do zadania. Ustaw `threads` parametr, aby zwiększyć liczbę operacji współbieżnych:

```python
try:
    client.job.add(job_parameter, threads=50)
except Exception as e:
    raise e
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o korzystaniu z rozszerzenia interfejsu wiersza polecenia Azure Batch z [szablonami interfejsu wiersza polecenia usługi Batch](batch-cli-templates.md).
- Dowiedz się więcej na temat [rozszerzenia Batch Python SDK](https://pypi.org/project/azure-batch-extensions/).
- Zapoznaj się z [najlepszymi rozwiązaniami dotyczącymi Azure Batch](best-practices.md).
