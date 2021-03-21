---
title: Monitorowanie partii przy użyciu usługi Azure Application Insights
description: Dowiedz się, jak instrumentować Azure Batch aplikację .NET przy użyciu biblioteki Application Insights platformy Azure.
ms.topic: how-to
ms.custom: devx-track-csharp
ms.date: 04/05/2018
ms.openlocfilehash: 9decb99c3de798df43dedc2441208066d18e3a13
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104605787"
---
# <a name="monitor-and-debug-an-azure-batch-net-application-with-application-insights"></a>Monitorowanie i debugowanie Azure Batch aplikacji .NET z Application Insights

[Application Insights](../azure-monitor/app/app-insights-overview.md) zapewnia elegancki i zaawansowany sposób, w jaki deweloperzy mogą monitorować i debugować aplikacje wdrożone w usługach platformy Azure. Użyj Application Insights do monitorowania liczników wydajności i wyjątków oraz Instrumentacji kodu z niestandardowymi metrykami i śledzeniem. Integracja Application Insights z aplikacją Azure Batch umożliwia uzyskanie szczegółowych informacji o zachowaniach i zbadanie problemów w czasie niemal rzeczywistym.

W tym artykule opisano sposób dodawania i konfigurowania biblioteki Application Insights w rozwiązaniu Azure Batch .NET oraz Instrumentacji kodu aplikacji. Przedstawiono w nim również sposoby monitorowania aplikacji za pomocą Azure Portal i tworzenia niestandardowych pulpitów nawigacyjnych. Aby uzyskać pomoc techniczną dotyczącą Application Insights w innych językach, zapoznaj się z [dokumentacją języków, platform i integracji](../azure-monitor/app/platforms.md).

Przykładowe rozwiązanie w języku C# z kodem, który jest dołączony do tego artykułu, jest dostępne w witrynie [GitHub](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights). Ten przykład dodaje kod Instrumentacji Application Insights do przykładu [przykładzie topnwords](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords) . Jeśli nie znasz tego przykładu, spróbuj najpierw skompilować i uruchomić przykładzie topnwords. Dzięki temu można zrozumieć podstawowy przepływ pracy wsadowej przetwarzania zestawu wejściowych obiektów BLOB równolegle w wielu węzłach obliczeniowych. 

> [!TIP]
> Alternatywnie Skonfiguruj rozwiązanie do przetwarzania wsadowego, aby wyświetlać Application Insights dane, takie jak liczniki wydajności maszyn wirtualnych, w Batch Explorer. [Batch Explorer](https://github.com/Azure/BatchExplorer) to bezpłatne, bogate w funkcje, autonomiczne narzędzie klienta pomagające tworzyć, debugować i monitorować aplikacje Azure Batch. Pobierz [pakiet instalacyjny](https://azure.github.io/BatchExplorer/) dla komputerów Mac lub systemu Linux albo Windows. Zapoznaj się z [repozytorium usługi Batch Insights](https://github.com/Azure/batch-insights) , aby uzyskać szybkie kroki umożliwiające włączenie Application Insights danych w Batch Explorer. 
>

## <a name="prerequisites"></a>Wymagania wstępne
* [Program Visual Studio 2017 lub nowszy](https://www.visualstudio.com/vs)

* [Konto wsadowe i połączone konto magazynu](batch-account-create-portal.md)

* [Zasób usługi Application Insights](../azure-monitor/app/create-new-resource.md )
  
   * Użyj Azure Portal, aby utworzyć *zasób* Application Insights. Wybierz **Typ aplikacji** *Ogólne* .

   * Skopiuj [klucz Instrumentacji](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key) z portalu. Jest to wymagane w dalszej części tego artykułu.
  
  > [!NOTE]
  > Za dane przechowywane w Application Insights mogą być [naliczone opłaty](https://azure.microsoft.com/pricing/details/application-insights/) . Obejmuje to dane diagnostyczne i monitorowania omówione w tym artykule.
  > 

## <a name="add-application-insights-to-your-project"></a>Dodawanie Application Insights do projektu

Pakiet NuGet **Microsoft. ApplicationInsights. WindowsServer** i jego zależności są wymagane dla projektu. Dodaj lub Przywróć je do projektu aplikacji. Aby zainstalować pakiet, użyj `Install-Package` polecenia lub Menedżera pakietów NuGet.

```powershell
Install-Package Microsoft.ApplicationInsights.WindowsServer
```
Odwołuje się Application Insights z aplikacji .NET przy użyciu przestrzeni nazw **Microsoft. ApplicationInsights** .

## <a name="instrument-your-code"></a>Instrumentacja kodu

Aby instrumentować kod, Twoje rozwiązanie musi utworzyć Application Insights [TelemetryClient](/dotnet/api/microsoft.applicationinsights.telemetryclient). W przykładzie TelemetryClient ładuje swoją konfigurację z pliku [ApplicationInsights.config](../azure-monitor/app/configuration-with-applicationinsights-config.md) . Pamiętaj, aby zaktualizować ApplicationInsights.config w następujących projektach z kluczem Instrumentacji Application Insights: Microsoft.Azure.Batch. Przykłady. TelemetryStartTask i TopNWordsSample.

```xml
<InstrumentationKey>YOUR-IKEY-GOES-HERE</InstrumentationKey>
```
Dodaj również klucz Instrumentacji w pliku przykładzie topnwords. cs.

W przykładzie w przykładzie topnwords. cs są stosowane następujące [wywołania Instrumentacji](../azure-monitor/app/api-custom-events-metrics.md) z interfejsu API Application Insights:
* `TrackMetric()` -Śledzi, jak długo i średnio, węzeł obliczeniowy pobiera wymagany plik tekstowy.
* `TrackTrace()` — Dodaje wywołania debugowania do kodu.
* `TrackEvent()` — Śledzi interesujące zdarzenia do przechwycenia.

W tym przykładowym celu pozostawi obsługę wyjątków. Zamiast tego Application Insights automatycznie zgłasza Nieobsłużone wyjątki, co znacznie zwiększa możliwości debugowania. 

Poniższy fragment kodu ilustruje sposób korzystania z tych metod.

```csharp
public void CountWords(string blobName, int numTopN, string storageAccountName, string storageAccountKey)
{
    // simulate exception for some set of tasks
    Random rand = new Random();
    if (rand.Next(0, 10) % 10 == 0)
    {
        blobName += ".badUrl";
    }

    // log the url we are downloading the file from
    insightsClient.TrackTrace(new TraceTelemetry(string.Format("Task {0}: Download file from: {1}", this.taskId, blobName), SeverityLevel.Verbose));

    // open the cloud blob that contains the book
    var storageCred = new StorageCredentials(storageAccountName, storageAccountKey);
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(blobName), storageCred);
    using (Stream memoryStream = new MemoryStream())
    {
        // calculate blob download time
        DateTime start = DateTime.Now;
        blob.DownloadToStream(memoryStream);
        TimeSpan downloadTime = DateTime.Now.Subtract(start);

        // track how long the blob takes to download on this node
        // this will help debug timing issues or identify poorly performing nodes
        insightsClient.TrackMetric("Blob download in seconds", downloadTime.TotalSeconds, this.CommonProperties);

        memoryStream.Position = 0; //Reset the stream
        var sr = new StreamReader(memoryStream);
        var myStr = sr.ReadToEnd();
        string[] words = myStr.Split(' ');

        // log how many words were found in the text file
        insightsClient.TrackTrace(new TraceTelemetry(string.Format("Task {0}: Found {1} words", this.taskId, words.Length), SeverityLevel.Verbose));
        var topNWords =
            words.
                Where(word => word.Length > 0).
                GroupBy(word => word, (key, group) => new KeyValuePair<String, long>(key, group.LongCount())).
                OrderByDescending(x => x.Value).
                Take(numTopN).
                ToList();
        foreach (var pair in topNWords)
        {
            Console.WriteLine("{0} {1}", pair.Key, pair.Value);
        }

        // emit an event to track the completion of the task
        insightsClient.TrackEvent("Done counting words");
    }
}
```

### <a name="azure-batch-telemetry-initializer-helper"></a>Pomocnik inicjatora telemetru Azure Batch
W przypadku raportowania danych telemetrycznych dla danego serwera i wystąpienia Application Insights używa roli maszyny wirtualnej platformy Azure i nazwy maszyny wirtualnej dla wartości domyślnych. W kontekście Azure Batch, w przykładzie pokazano, jak zamiast tego używać nazwy puli i węzła obliczeniowego. Użyj [inicjatora telemetrii](../azure-monitor/app/api-filtering-sampling.md#add-properties) , aby zastąpić wartości domyślne. 

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;
using System;
using System.Threading;

namespace Microsoft.Azure.Batch.Samples.TelemetryInitializer
{
    public class AzureBatchNodeTelemetryInitializer : ITelemetryInitializer
    {
        // Azure Batch environment variables
        private const string PoolIdEnvironmentVariable = "AZ_BATCH_POOL_ID";
        private const string NodeIdEnvironmentVariable = "AZ_BATCH_NODE_ID";

        private string roleInstanceName;
        private string roleName;

        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                // override the role name with the Azure Batch Pool name
                string name = LazyInitializer.EnsureInitialized(ref this.roleName, this.GetPoolName);
                telemetry.Context.Cloud.RoleName = name;
            }

            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleInstance))
            {
                // override the role instance with the Azure Batch Compute Node name
                string name = LazyInitializer.EnsureInitialized(ref this.roleInstanceName, this.GetNodeName);
                telemetry.Context.Cloud.RoleInstance = name;
            }
        }

        private string GetPoolName()
        {
            return Environment.GetEnvironmentVariable(PoolIdEnvironmentVariable) ?? string.Empty;
        }

        private string GetNodeName()
        {
            return Environment.GetEnvironmentVariable(NodeIdEnvironmentVariable) ?? string.Empty;
        }
    }
}
```

Aby włączyć inicjatora telemetrii, plik ApplicationInsights.config w projekcie TopNWordsSample obejmuje następujące elementy:

```xml
<TelemetryInitializers>
    <Add Type="Microsoft.Azure.Batch.Samples.TelemetryInitializer.AzureBatchNodeTelemetryInitializer, Microsoft.Azure.Batch.Samples.TelemetryInitializer"/>
</TelemetryInitializers>
``` 

## <a name="update-the-job-and-tasks-to-include-application-insights-binaries"></a>Aktualizowanie zadania i zadań w celu uwzględnienia Application Insights plików binarnych

Aby Application Insights działały prawidłowo w węzłach obliczeniowych, upewnij się, że pliki binarne są poprawnie umieszczone. Dodaj wymagane pliki binarne do kolekcji plików zasobów zadania, aby były pobierane podczas wykonywania zadania. Poniższe wstawki są podobne do kodu w Job. cs.

Najpierw utwórz statyczną listę plików Application Insights do przekazania.

```csharp
private static readonly List<string> AIFilesToUpload = new List<string>()
{
    // Application Insights config and assemblies
    "ApplicationInsights.config",
    "Microsoft.ApplicationInsights.dll",
    "Microsoft.AI.Agent.Intercept.dll",
    "Microsoft.AI.DependencyCollector.dll",
    "Microsoft.AI.PerfCounterCollector.dll",
    "Microsoft.AI.ServerTelemetryChannel.dll",
    "Microsoft.AI.WindowsServer.dll",
    
    // custom telemetry initializer assemblies
    "Microsoft.Azure.Batch.Samples.TelemetryInitializer.dll",
 };
...
```

Następnie utwórz pliki przemieszczania, które są używane przez zadanie.
```csharp
...
// create file staging objects that represent the executable and its dependent assembly to run as the task.
// These files are copied to every node before the corresponding task is scheduled to run on that node.
FileToStage topNWordExe = new FileToStage(TopNWordsExeName, stagingStorageAccount);
FileToStage storageDll = new FileToStage(StorageClientDllName, stagingStorageAccount);

// Upload Application Insights assemblies
List<FileToStage> aiStagedFiles = new List<FileToStage>();
foreach (string aiFile in AIFilesToUpload)
{
    aiStagedFiles.Add(new FileToStage(aiFile, stagingStorageAccount));
}
...
```

`FileToStage`Metoda jest funkcją pomocnika w przykładowym kodzie, która umożliwia łatwe przekazywanie pliku z dysku lokalnego do obiektu BLOB usługi Azure Storage. Każdy plik jest później pobierany do węzła obliczeniowego i przywoływany przez zadanie.

Na koniec Dodaj zadania do zadania i Uwzględnij niezbędne Application Insights pliki binarne.
```csharp
...
// initialize a collection to hold the tasks that will be submitted in their entirety
List<CloudTask> tasksToRun = new List<CloudTask>(topNWordsConfiguration.NumberOfTasks);
for (int i = 1; i <= topNWordsConfiguration.NumberOfTasks; i++)
{
    CloudTask task = new CloudTask("task_no_" + i, String.Format("{0} --Task {1} {2} {3} {4}",
        TopNWordsExeName,
        string.Format("https://{0}.blob.core.windows.net/{1}",
            accountSettings.StorageAccountName,
            documents[i]),
        topNWordsConfiguration.TopWordCount,
        accountSettings.StorageAccountName,
        accountSettings.StorageAccountKey));

    //This is the list of files to stage to a container -- for each job, one container is created and 
    //files all resolve to Azure Blobs by their name (so two tasks with the same named file will create just 1 blob in
    //the container).
    task.FilesToStage = new List<IFileStagingProvider>
                        {
                            // required application binaries
                            topNWordExe,
                            storageDll,
                        };
    foreach (FileToStage stagedFile in aiStagedFiles)
   {
        task.FilesToStage.Add(stagedFile);
   }    
    task.RunElevated = false;
    tasksToRun.Add(task);
}
```

## <a name="view-data-in-the-azure-portal"></a>Wyświetlanie danych w Azure Portal

Teraz, po skonfigurowaniu zadania i zadań do użycia Application Insights, uruchom przykładowe zadanie w puli. Przejdź do Azure Portal i Otwórz zasób Application Insights, który został zainicjowany. Po zainicjowaniu obsługi puli należy zacząć wyświetlać przepływy danych i rejestrować. W pozostałej części tego artykułu przedstawiono tylko kilka Application Insights funkcji, ale możesz zapoznać się z pełnym zestawem funkcji.

### <a name="view-live-stream-data"></a>Wyświetl dane strumienia na żywo

Aby wyświetlić dzienniki śledzenia w zasobie usługi Application Insights, kliknij **Live Stream**. Poniższy zrzut ekranu pokazuje, jak wyświetlać dane na żywo pochodzące z węzłów obliczeniowych w puli, na przykład użycie procesora CPU na węzeł obliczeniowy.

![Dane węzła obliczeniowego usługi Live Stream](./media/monitor-application-insights/applicationinsightslivestream.png)

### <a name="view-trace-logs"></a>Wyświetlanie dzienników śledzenia

Aby wyświetlić dzienniki śledzenia w zasobie usługi Application Insights, kliknij przycisk **Wyszukaj**. Ten widok przedstawia listę danych diagnostycznych przechwytywanych przez Application Insights takich jak ślady, zdarzenia i wyjątki. 

Poniższy zrzut ekranu przedstawia sposób rejestrowania pojedynczego śledzenia zadania i późniejszego zapytania na potrzeby debugowania.

![Obraz dzienników śledzenia](./media/monitor-application-insights/tracelogsfortask.png)

### <a name="view-unhandled-exceptions"></a>Wyświetl Nieobsłużone wyjątki

Na poniższych zrzutach ekranu przedstawiono sposób, w jaki Application Insights rejestruje wyjątki zgłoszone przez aplikację. W takim przypadku w ciągu kilku sekund aplikacji zwracającej wyjątek można przejść do określonego wyjątku i zdiagnozować problem.

![Nieobsługiwane wyjątki](./media/monitor-application-insights/exception.png)

### <a name="measure-blob-download-time"></a>Czas pobierania obiektu BLOB miary

Metryki niestandardowe są również cennym narzędziem w portalu. Można na przykład wyświetlić średni czas, jaki zajęł każdy węzeł obliczeniowy do pobrania wymaganego pliku tekstowego, który został przetworzony.

Aby utworzyć przykładowy wykres:
1. W zasobie Application Insights kliknij pozycję **Eksplorator metryk**  >  **Dodaj wykres**.
2. Kliknij pozycję **Edytuj** na wykresie, który został dodany.
2. Zaktualizuj szczegóły wykresu w następujący sposób:
   * Ustaw **Typ wykresu** na **siatkę**.
   * Ustaw **agregację** na wartość **średnia**.
   * Ustaw wartość **Group by** na **NodeId**.
   * W obszarze **metryki** wybierz pozycję **niestandardowa**  >  **pobieranie obiektów BLOB w sekundach**.
   * Dostosuj wyświetlaną **paletę kolorów** do wybranej opcji. 

![Czas pobierania obiektów BLOB na węzeł](./media/monitor-application-insights/blobdownloadtime.png)


## <a name="monitor-compute-nodes-continuously"></a>Ciągłe monitorowanie węzłów obliczeniowych

Być może zauważono, że wszystkie metryki, w tym liczniki wydajności, są rejestrowane tylko wtedy, gdy zadania są uruchomione. To zachowanie jest przydatne, ponieważ ogranicza ilość danych, które Application Insights dzienników. Są jednak sytuacje, w których zawsze chcesz monitorować węzły obliczeniowe. Na przykład może być uruchomiona w tle, która nie jest zaplanowana za pośrednictwem usługi Batch. W takim przypadku należy skonfigurować proces monitorowania do uruchomienia w ramach cyklu życia węzła obliczeniowego. 

Jednym ze sposobów osiągnięcia tego zachowania jest zduplikowanie procesu ładującego bibliotekę Application Insights i uruchomienie jej w tle. W przykładzie zadanie uruchamiania ładuje pliki binarne na komputerze i utrzymuje proces działający w nieskończoność. Skonfiguruj Application Insights plik konfiguracyjny dla tego procesu, aby emitować interesujące Cię dane, takie jak liczniki wydajności.

```csharp
...
 // Batch start task telemetry runner
private const string BatchStartTaskFolderName = "StartTask";
private const string BatchStartTaskTelemetryRunnerName = "Microsoft.Azure.Batch.Samples.TelemetryStartTask.exe";
private const string BatchStartTaskTelemetryRunnerAIConfig = "ApplicationInsights.config";
...
CloudPool pool = client.PoolOperations.CreatePool(
    topNWordsConfiguration.PoolId,
    targetDedicated: topNWordsConfiguration.PoolNodeCount,
    virtualMachineSize: "standard_d1_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));
...

// Create a start task which will run a dummy exe in background that simply emits performance
// counter data as defined in the relevant ApplicationInsights.config.
// Note that the waitForSuccess on the start task was not set so the Compute Node will be
// available immediately after this command is run.
pool.StartTask = new StartTask()
{
    CommandLine = string.Format("cmd /c {0}", BatchStartTaskTelemetryRunnerName),
    ResourceFiles = resourceFiles
};
...
```

> [!TIP]
> Aby zwiększyć możliwości zarządzania rozwiązaniami, można powiązać zestaw w [pakiecie aplikacji](./batch-application-packages.md). Następnie, aby automatycznie wdrożyć pakiet aplikacji w pulach, Dodaj odwołanie do pakietu aplikacji do konfiguracji puli.
>

## <a name="throttle-and-sample-data"></a>Ograniczanie i próbkowanie danych 

Ze względu na dużą skalę aplikacji Azure Batch działających w środowisku produkcyjnym możesz chcieć ograniczyć ilość danych zbieranych przez Application Insights, aby zarządzać kosztami. Aby to osiągnąć, zobacz [próbkowanie w Application Insights](../azure-monitor/app/sampling.md) .


## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [Application Insights](../azure-monitor/app/app-insights-overview.md).

* Aby uzyskać pomoc techniczną dotyczącą Application Insights w innych językach, zapoznaj się z [dokumentacją języków, platform i integracji](../azure-monitor/app/platforms.md).


