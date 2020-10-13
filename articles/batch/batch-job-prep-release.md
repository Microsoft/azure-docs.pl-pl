---
title: Tworzenie zadań w celu przygotowania & ukończonych zadań w węzłach obliczeniowych
description: Zadania przygotowania na poziomie zadania umożliwiają minimalizowanie transferu danych do Azure Batch węzłów obliczeniowych oraz wykonywanie zadań do wyczyszczenia węzła podczas ukończenia zadania.
ms.topic: how-to
ms.date: 02/17/2020
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 5b1084cfdd5995b7983badcdce71460f7bdec3d5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88919458"
---
# <a name="run-job-preparation-and-job-release-tasks-on-batch-compute-nodes"></a>Uruchamianie zadań przygotowania i zwolnienia zadań w węzłach obliczeniowych wsadowych

 Zadanie Azure Batch często wymaga pewnego formularza Instalatora przed wykonaniem zadań i konserwacji po wykonaniu zadania po zakończeniu zadań. Może być konieczne pobranie typowych danych wejściowych zadania do węzłów obliczeniowych lub przekazanie danych wyjściowych zadania do usługi Azure Storage po zakończeniu zadania. Aby wykonać te operacje, można użyć zadań **przygotowania** i **zwolnienia** zadań.

## <a name="what-are-job-preparation-and-release-tasks"></a>Co to są zadania przygotowania i zwolnienia zadań?
Przed uruchomieniem zadań zadania, zadanie przygotowania zadania jest uruchamiane na wszystkich węzłach obliczeniowych zaplanowanych do uruchomienia co najmniej jednego zadania. Po zakończeniu zadania zadanie zwolnienia zadania jest uruchamiane w każdym węźle w puli, który wykonał co najmniej jedno zadanie. Podobnie jak w przypadku normalnych zadań wsadowych, można określić wiersz polecenia, który ma być wywoływany, gdy zadanie przygotowania lub zwolnienia zadania zostanie uruchomione.

Zadania przygotowania i zwolnienia zadań oferują znane funkcje zadań wsadowych, takie jak pobieranie plików ([pliki zasobów][net_job_prep_resourcefiles]), wykonywanie z podwyższonym poziomem uprawnień, niestandardowe zmienne środowiskowe, maksymalny czas trwania wykonywania, liczba ponownych prób i czas przechowywania pliku.

W poniższych sekcjach dowiesz się, jak używać klas [JobPreparationTask][net_job_prep] i [funkcji jobreleasetask][net_job_release] znajdujących się w bibliotece [Batch .NET][api_net] .

> [!TIP]
> Zadania przygotowania i zwolnienia zadań są szczególnie przydatne w środowiskach "Pula współdzielona", w których Pula węzłów obliczeniowych utrzymuje się między uruchomieniami zadań i jest używana przez wiele zadań.
> 
> 

## <a name="when-to-use-job-preparation-and-release-tasks"></a>Kiedy używać zadań przygotowania i zwolnienia zadań
Zadania przygotowania i zwolnienia zadań są dobre dla następujących sytuacji:

**Pobierz typowe dane zadania**

Zadania usługi Batch często wymagają wspólnego zestawu danych jako danych wejściowych dla zadań zadania. Na przykład Obliczanie analizy ryzyka dziennego oznacza, że dane rynkowe są specyficzne dla zadania, ale wspólne dla wszystkich zadań w zadaniu. Te dane na rynku, często kilka gigabajtów o rozmiarze, powinny być pobierane do poszczególnych węzłów obliczeniowych tylko raz, aby można było użyć dowolnego zadania uruchomionego w węźle. Użyj **zadania przygotowania zadania** , aby pobrać te dane do każdego węzła przed wykonaniem innych zadań.

**Usuń zadanie i dane wyjściowe zadania**

W środowisku "Pula współdzielona", w którym węzły obliczeniowe puli nie są likwidowane między zadaniami, może być konieczne usunięcie danych zadania między przebiegami. Może być konieczne zagwarantowanie miejsca na dysku w węzłach lub spełnienie zasad zabezpieczeń w organizacji. **Zadanie zwolnienia zadania** służy do usuwania danych pobranych przez zadanie przygotowania zadania lub wygenerowanych podczas wykonywania zadania.

**Przechowywanie dzienników**

Warto zachować kopię plików dziennika generowanych przez zadania lub ewentualnie pliki zrzutu awaryjnego, które mogą być generowane przez aplikacje zakończone niepowodzeniem. W takich przypadkach można użyć **zadania zwolnienia zadania** w celu skompresowania i przekazania tych danych do konta [usługi Azure Storage][azure_storage] .

> [!TIP]
> Innym sposobem utrwalania dzienników i innych danych wyjściowych zadania jest użycie biblioteki [Konwencji plików Azure Batch](batch-task-output.md) .
>
>

## <a name="job-preparation-task"></a>Zadanie przygotowania zadania


Przed wykonaniem zadań zadania Batch wykonuje zadanie przygotowania zadania na każdym węźle obliczeniowym zaplanowanym do uruchomienia zadania. Domyślnie usługi Batch czeka na ukończenie zadania przygotowania zadania przed uruchomieniem zadań zaplanowanych do wykonania w węźle. Można jednak skonfigurować usługę tak, aby nie czekać. Jeśli węzeł zostanie ponownie uruchomiony, zadanie przygotowania zadania zostanie uruchomione ponownie. Możesz również wyłączyć to zachowanie. Jeśli masz zadanie z zadaniem przygotowania zadania i skonfigurowano zadanie Menedżera zadań, zadanie przygotowania zadania zostanie uruchomione przed zadaniem Menedżera zadań, tak jak w przypadku wszystkich innych zadań. Zadanie przygotowania zadania jest zawsze uruchamiane jako pierwsze.

Zadanie przygotowania zadania jest wykonywane tylko w węzłach, które zaplanowano do uruchomienia zadania. Zapobiega to niepotrzebnemu wykonaniu zadania przygotowania w przypadku, gdy węzeł nie ma przypisanego zadania. Taka sytuacja może wystąpić, gdy liczba zadań dla zadania jest mniejsza niż liczba węzłów w puli. Ma również zastosowanie, gdy włączone jest [współbieżne wykonywanie zadań](batch-parallel-node-tasks.md) , co pozostawia pewne węzły w stanie bezczynności, jeśli liczba zadań jest mniejsza niż łączna możliwa liczba współbieżnych zadań. Nie uruchamiając zadania przygotowania zadania w węzłach bezczynnych, można poświęcać mniej pieniędzy na opłaty za transfer danych.

> [!NOTE]
> [JobPreparationTask][net_job_prep_cloudjob] różni się od [CloudPool. StartTask][pool_starttask] w tym JobPreparationTask jest wykonywane na początku każdego zadania, natomiast StartTask jest wykonywane tylko wtedy, gdy węzeł obliczeniowy najpierw przyłączy pulę lub uruchamia ponownie.
>


>## <a name="job-release-task"></a>Zadanie zwolnienia zadania

Gdy zadanie zostanie oznaczone jako ukończone, zadanie Zwolnij zadanie jest wykonywane na każdym węźle w puli, który wykonał co najmniej jedno zadanie. Zadanie jest oznaczane jako ukończone przez wystawienie żądania przerwania. Usługa Batch ustawia następnie stan zadania na *zakończenie*, kończy wszystkie aktywne lub uruchomione zadania skojarzone z zadaniem i uruchamia zadanie zwolnienia zadania. Zadanie jest następnie przenoszone do stanu *ukończone* .

> [!NOTE]
> Podczas usuwania zadania jest również wykonywane zadanie zwolnienia zadania. Jeśli jednak zadanie zostało już zakończone, zadanie zlecenia nie jest uruchamiane po raz drugi, jeśli zadanie zostanie później usunięte.

Zadania wydania zadań mogą działać przez maksymalnie 15 minut, zanim zostaną zakończone przez usługę Batch. Aby uzyskać więcej informacji, zobacz [dokumentację interfejsu API REST](/rest/api/batchservice/job/add#jobreleasetask).
> 
> 

## <a name="job-prep-and-release-tasks-with-batch-net"></a>Zadania przygotowania i zwolnienia zadań za pomocą programu Batch .NET
Aby użyć zadania przygotowania zadania, przypisz obiekt [JobPreparationTask][net_job_prep] do właściwości [CloudJob. JobPreparationTask][net_job_prep_cloudjob] zadania. Podobnie zainicjuj [funkcji jobreleasetask][net_job_release] i przypisz go do właściwości [CloudJob. funkcji jobreleasetask][net_job_prep_cloudjob] zadania, aby ustawić zadanie zwolnienia zadania.

W tym fragmencie kodu `myBatchClient` jest wystąpienie elementu [BatchClient][net_batch_client]i `myPool` jest istniejącą pulą w ramach konta wsadowego.

```csharp
// Create the CloudJob for CloudPool "myPool"
CloudJob myJob =
    myBatchClient.JobOperations.CreateJob(
        "JobPrepReleaseSampleJob",
        new PoolInformation() { PoolId = "myPool" });

// Specify the command lines for the job preparation and release tasks
string jobPrepCmdLine =
    "cmd /c echo %AZ_BATCH_NODE_ID% > %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";
string jobReleaseCmdLine =
    "cmd /c del %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";

// Assign the job preparation task to the job
myJob.JobPreparationTask =
    new JobPreparationTask { CommandLine = jobPrepCmdLine };

// Assign the job release task to the job
myJob.JobReleaseTask =
    new JobReleaseTask { CommandLine = jobReleaseCmdLine };

await myJob.CommitAsync();
```

Jak wspomniano wcześniej, zadanie wydania jest wykonywane, gdy zadanie zostanie zakończone lub usunięte. Przerwij zadanie przy użyciu elementu [JobOperations. TerminateJobAsync][net_job_terminate]. Usuń zadanie z [JobOperations. DeleteJobAsync][net_job_delete]. Zadanie jest zazwyczaj przerywane lub usuwane po zakończeniu jego zadań lub po osiągnięciu limitu czasu zdefiniowanego przez użytkownika.

```csharp
// Terminate the job to mark it as Completed; this will initiate the
// Job Release Task on any node that executed job tasks. Note that the
// Job Release Task is also executed when a job is deleted, thus you
// need not call Terminate if you typically delete jobs after task completion.
await myBatchClient.JobOperations.TerminateJobAsync("JobPrepReleaseSampleJob");
```

## <a name="code-sample-on-github"></a>Przykład kodu w witrynie GitHub
Aby wyświetlić zadania przygotowania i zwolnienia zadania, zapoznaj się z przykładowym projektem [JobPrepRelease][job_prep_release_sample] w witrynie GitHub. Ta Aplikacja konsolowa wykonuje następujące czynności:

1. Tworzy pulę z dwoma węzłami.
2. Tworzy zadanie z przygotowaniem zadania, wydaniem i zadaniami standardowymi.
3. Uruchamia zadanie przygotowania zadania, które najpierw zapisuje identyfikator węzła do pliku tekstowego w katalogu "Shared" węzła.
4. Uruchamia zadanie dla każdego węzła, który zapisuje jego identyfikator zadania w tym samym pliku tekstowym.
5. Po zakończeniu wszystkich zadań (lub osiągnięciu limitu czasu) program drukuje zawartość pliku tekstowego każdego węzła w konsoli programu.
6. Po zakończeniu zadania uruchom zadanie Zwolnij zadanie, aby usunąć plik z węzła.
7. Drukuje kody zakończenia zadań przygotowania i zwolnienia zadania dla każdego węzła, na którym zostały wykonane.
8. Wstrzymuje wykonywanie, aby umożliwić potwierdzenie usunięcia zadania i/lub puli.

Dane wyjściowe z przykładowej aplikacji są podobne do następujących:

```
Attempting to create pool: JobPrepReleaseSamplePool
Created pool JobPrepReleaseSamplePool with 2 nodes
Checking for existing job JobPrepReleaseSampleJob...
Job JobPrepReleaseSampleJob not found, creating...
Submitting tasks and awaiting completion...
All tasks completed.

Contents of shared\job_prep_and_release.txt on tvm-2434664350_1-20160623t173951z:
-------------------------------------------
tvm-2434664350_1-20160623t173951z tasks:
  task001
  task004
  task005
  task006

Contents of shared\job_prep_and_release.txt on tvm-2434664350_2-20160623t173951z:
-------------------------------------------
tvm-2434664350_2-20160623t173951z tasks:
  task008
  task002
  task003
  task007

Waiting for job JobPrepReleaseSampleJob to reach state Completed
...

tvm-2434664350_1-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

tvm-2434664350_2-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

Delete job? [yes] no
yes
Delete pool? [yes] no
yes

Sample complete, hit ENTER to exit...
```

> [!NOTE]
> Ze względu na tworzenie zmiennej i czas rozpoczęcia węzłów w nowej puli (niektóre węzły są gotowe do wykonywania zadań przed innymi), mogą pojawić się różne dane wyjściowe. W związku z tym, ponieważ zadania są wykonywane szybko, jeden z węzłów puli może wykonywać wszystkie zadania zadań. W takim przypadku należy zauważyć, że zadania przygotowania i zwolnienia zadania nie istnieją dla węzła, który nie wykonał żadnych zadań.
> 
> 

### <a name="inspect-job-preparation-and-release-tasks-in-the-azure-portal"></a>Inspekcja zadań przygotowywania i zwalniania zadań w Azure Portal
Po uruchomieniu przykładowej aplikacji można użyć [Azure Portal][portal] , aby wyświetlić właściwości zadania i jego zadań, a nawet pobrać udostępniony plik tekstowy, który jest modyfikowany przez zadania zadania podrzędnego.

Zrzut ekranu poniżej przedstawia **blok zadania przygotowania** w Azure Portal po uruchomieniu przykładowej aplikacji. Przejdź do właściwości *JobPrepReleaseSampleJob* po ukończeniu zadań (ale przed usunięciem zadania i puli), a następnie kliknij pozycję **zadania przygotowania** lub **zlecenia wydania** , aby wyświetlić ich właściwości.

![Właściwości przygotowania zadania w Azure Portal][1]

## <a name="next-steps"></a>Następne kroki
### <a name="application-packages"></a>Pakiety aplikacji
Oprócz zadania przygotowania zadania można także użyć funkcji [pakietów aplikacji](batch-application-packages.md) usługi Batch do przygotowania węzłów obliczeniowych do wykonywania zadań. Ta funkcja jest szczególnie przydatna w przypadku wdrażania aplikacji, które nie wymagają uruchamiania Instalatora, aplikacji zawierających wiele plików (100 +) lub aplikacji wymagających ścisłej kontroli wersji.

### <a name="installing-applications-and-staging-data"></a>Instalowanie aplikacji i danych przemieszczania
Ten wpis na forum MSDN zawiera omówienie kilku metod przygotowywania węzłów do uruchamiania zadań:

[Instalowanie aplikacji i danych przemieszczania w węzłach obliczeniowych wsadowych][forum_post]

Zapisanie przez jednego z członków zespołu Azure Batch obejmuje kilka technik, których można użyć do wdrażania aplikacji i danych w węzłach obliczeniowych.

[api_net]: /dotnet/api/microsoft.azure.batch
[api_net_listjobs]: /dotnet/api/microsoft.azure.batch.joboperations
[api_rest]: /rest/api/batchservice/
[azure_storage]: https://azure.microsoft.com/services/storage/
[portal]: https://portal.azure.com
[job_prep_release_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/JobPrepRelease
[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[net_batch_client]: /dotnet/api/microsoft.azure.batch.batchclient
[net_cloudjob]:https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_prep]: /dotnet/api/microsoft.azure.batch.jobpreparationtask
[net_job_prep_cloudjob]: /dotnet/api/microsoft.azure.batch.cloudjob
[net_job_prep_resourcefiles]: /dotnet/api/microsoft.azure.batch.jobpreparationtask
[net_job_delete]: /previous-versions/azure/mt281411(v=azure.100)
[net_job_terminate]: /previous-versions/azure/mt188985(v=azure.100)
[net_job_release]: /dotnet/api/microsoft.azure.batch.jobreleasetask
[net_job_release_cloudjob]: /dotnet/api/microsoft.azure.batch.cloudjob
[pool_starttask]: /dotnet/api/microsoft.azure.batch.cloudpool

[net_list_certs]: /dotnet/api/microsoft.azure.batch.certificateoperations
[net_list_compute_nodes]: /dotnet/api/microsoft.azure.batch.pooloperations
[net_list_job_schedules]: /dotnet/api/microsoft.azure.batch.jobscheduleoperations
[net_list_jobprep_status]: /dotnet/api/microsoft.azure.batch.joboperations
[net_list_jobs]: /dotnet/api/microsoft.azure.batch.joboperations
[net_list_nodefiles]: /dotnet/api/microsoft.azure.batch.joboperations
[net_list_pools]: /dotnet/api/microsoft.azure.batch.pooloperations
[net_list_schedule_jobs]: /dotnet/api/microsoft.azure.batch.jobscheduleoperations
[net_list_task_files]: /dotnet/api/microsoft.azure.batch.cloudtask
[net_list_tasks]: /dotnet/api/microsoft.azure.batch.joboperations

[1]: ./media/batch-job-prep-release/portal-jobprep-01.png
