---
title: Monitorowanie usługi Batch za pomocą usługi Azure Application Insights
description: Dowiedz się, jak instrumentować aplikację .NET Azure Batch pomocą biblioteki azure Application Insights.
ms.topic: how-to
ms.custom: devx-track-csharp
ms.date: 04/13/2021
ms.openlocfilehash: 8bc8ff0a04996d988a642062f118e9e6792abbf0
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389353"
---
# <a name="monitor-and-debug-an-azure-batch-net-application-with-application-insights"></a>Monitorowanie i debugowanie aplikacji .NET Azure Batch pomocą Application Insights

[Application Insights](../azure-monitor/app/app-insights-overview.md) to elegancki i zaawansowany sposób na monitorowanie i debugowanie aplikacji wdrożonych w usługach platformy Azure. Użyj Application Insights, aby monitorować liczniki wydajności i wyjątki, a także instrumentować kod za pomocą niestandardowych metryk i śledzenia. Integracja Application Insights z aplikacją Azure Batch umożliwia uzyskanie szczegółowego wglądu w zachowania i badanie problemów w czasie niemal rzeczywistym.

W tym artykule pokazano, jak dodać i skonfigurować bibliotekę Application Insights w Azure Batch .NET i instrumentować kod aplikacji. Przedstawiono w nim również sposoby monitorowania aplikacji za pośrednictwem Azure Portal i tworzenia niestandardowych pulpitów nawigacyjnych. Aby Application Insights w innych językach, zobacz dokumentację [dotyczącą języków, platform i integracji.](../azure-monitor/app/platforms.md)

Przykładowe rozwiązanie języka C# z kodem, który ma towarzyszyć temu artykułowi, jest dostępne w [witrynie GitHub.](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights) Ten przykład dodaje Application Insights instrumentacji do [przykładu TopNWords.](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords) Jeśli nie znasz tego przykładu, spróbuj najpierw sbudować i uruchomić program TopNWords. Pomoże to zrozumieć podstawowy przepływ pracy usługi Batch przetwarzania zestawu wejściowych obiektów blob równolegle w wielu węzłach obliczeniowych.

> [!TIP]
> Alternatywnie skonfiguruj rozwiązanie usługi Batch tak, aby wyświetlało dane Application Insights, takie jak liczniki wydajności maszyn wirtualnych w Batch Explorer. [Batch Explorer](https://github.com/Azure/BatchExplorer) to bezpłatne, bogate w funkcje, autonomiczne narzędzie klienta, które pomaga tworzyć, debugować i monitorować Azure Batch aplikacji. Pobierz [pakiet instalacyjny](https://azure.github.io/BatchExplorer/) dla komputerów Mac lub systemu Linux albo Windows. Zapoznaj się z [repozjum batch-insights,](https://github.com/Azure/batch-insights) aby uzyskać szybkie kroki umożliwiające Application Insights danych w Batch Explorer.

## <a name="prerequisites"></a>Wymagania wstępne

- [Visual Studio 2017 lub nowszy](https://www.visualstudio.com/vs)
- [Konto usługi Batch i połączone konto magazynu](batch-account-create-portal.md)
- [Application Insights zasobu](../azure-monitor/app/create-new-resource.md). Użyj Azure Portal, aby utworzyć Application Insights *zasobu*. Wybierz typ *aplikacji* **Ogólne.**
- Skopiuj [klucz instrumentacji z](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key) Azure Portal. Ta wartość będzie potrzebna później.
  
  > [!NOTE]
  > Może zostać naliczona [opłata za](https://azure.microsoft.com/pricing/details/application-insights/) dane przechowywane w Application Insights. Obejmuje to dane diagnostyczne i dane monitorowania omówione w tym artykule.

## <a name="add-application-insights-to-your-project"></a>Dodawanie Application Insights do projektu

Pakiet **NuGet Microsoft.ApplicationInsights.WindowsServer** i jego zależności są wymagane dla projektu. Dodaj je lub przywróć do projektu aplikacji. Aby zainstalować pakiet, użyj polecenia lub polecenia `Install-Package` NuGet Menedżer pakietów.

```powershell
Install-Package Microsoft.ApplicationInsights.WindowsServer
```

Odwołanie Application Insights z aplikacji .NET przy użyciu przestrzeni **nazw Microsoft.ApplicationInsights.**

## <a name="instrument-your-code"></a>Instrumentuj kod

Aby instrumentować kod, rozwiązanie musi utworzyć klienta Application Insights [TelemetryClient.](/dotnet/api/microsoft.applicationinsights.telemetryclient) W tym przykładzie TelemetryClient ładuje swoją konfigurację z [ApplicationInsights.config](../azure-monitor/app/configuration-with-applicationinsights-config.md) pliku. Pamiętaj, aby zaktualizować ApplicationInsights.config następujących projektach przy użyciu klucza Application Insights instrumentacji: Microsoft.Azure.Batch. Samples.TelemetryStartTask i TopNWordsSample.

```xml
<InstrumentationKey>YOUR-IKEY-GOES-HERE</InstrumentationKey>
```

Dodaj również klucz instrumentacji w pliku TopNWords.cs.

W przykładzie w topnWords.cs użyto następujących [wywołań instrumentacji](../azure-monitor/app/api-custom-events-metrics.md) z Application Insights API:

- `TrackMetric()` — Śledzi średni czas pobierania wymaganego pliku tekstowego przez węzeł obliczeniowy.
- `TrackTrace()` — Dodaje wywołania debugowania do kodu.
- `TrackEvent()` — Śledzi interesujące zdarzenia do przechwycenia.

Ten przykład celowo nie pozwala na obsługę wyjątków. Zamiast tego Application Insights automatycznie zgłasza nieobsługiwane wyjątki, co znacznie poprawia środowisko debugowania.

Poniższy fragment kodu ilustruje sposób używania tych metod.

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

### <a name="azure-batch-telemetry-initializer-helper"></a>Azure Batch pomocnik inicjatora telemetrii

Podczas raportowania danych telemetrycznych dla danego serwera i wystąpienia program Application Insights jako wartości domyślnych używa roli maszyny wirtualnej platformy Azure i nazwy maszyny wirtualnej. W kontekście Azure Batch przykład pokazuje, jak zamiast tego użyć nazwy puli i nazwy węzła obliczeniowego. Użyj [inicjatora](../azure-monitor/app/api-filtering-sampling.md#add-properties) telemetrii, aby przesłonić wartości domyślne.

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

Aby włączyć inicjator telemetrii, ApplicationInsights.config w projekcie TopNWordsSample zawiera następujące elementy:

```xml
<TelemetryInitializers>
    <Add Type="Microsoft.Azure.Batch.Samples.TelemetryInitializer.AzureBatchNodeTelemetryInitializer, Microsoft.Azure.Batch.Samples.TelemetryInitializer"/>
</TelemetryInitializers>
```

## <a name="update-the-job-and-tasks-to-include-application-insights-binaries"></a>Aktualizowanie zadania i zadań w celu Application Insights plików binarnych

Aby pliki Application Insights działały poprawnie w węzłach obliczeniowych, upewnij się, że pliki binarne zostały poprawnie umieszczone. Dodaj wymagane pliki binarne do kolekcji plików zasobów zadania, aby były pobierane w czasie wykonywania zadania. Poniższe fragmenty kodu są podobne do kodu w job.cs.

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

Następnie utwórz pliki przejściowe, które są używane przez zadanie.

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

Metoda jest funkcją pomocnika w przykładowym kodzie, która umożliwia łatwe przekazywanie pliku z dysku lokalnego `FileToStage` do obiektu blob usługi Azure Storage. Każdy plik jest później pobierany do węzła obliczeniowego i przywołyny przez zadanie.

Na koniec dodaj zadania do zadania i uwzględnij niezbędne Application Insights plików binarnych.

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

Teraz, gdy skonfigurowano zadanie i zadania do używania Application Insights, uruchom przykładowe zadanie w puli. Przejdź do Azure Portal i otwórz Application Insights zasobu, który został zaaprowizowany. Po aprowieniu puli powinno zacząć być widać, że dane przepływają i są rejestrowane. Pozostała część tego artykułu dotyczy tylko kilku Application Insights, ale możesz eksplorować pełny zestaw funkcji.

### <a name="view-live-stream-data"></a>Wyświetlanie danych transmisji strumieniowej na żywo

Aby wyświetlić dzienniki śledzenia w zasobie usługi Applications Insights, **kliknij Live Stream**. Poniższy zrzut ekranu przedstawia sposób wyświetlania danych na żywo pochodzących z węzłów obliczeniowych w puli, na przykład użycia procesora CPU na węzeł obliczeniowy.

![Zrzut ekranu przedstawiający dane węzła obliczeniowego strumienia na żywo.](./media/monitor-application-insights/applicationinsightslivestream.png)

### <a name="view-trace-logs"></a>Wyświetlanie dzienników śledzenia

Aby wyświetlić dzienniki śledzenia w zasobie usługi Applications Insights, kliknij pozycję **Wyszukaj.** Ten widok przedstawia listę danych diagnostycznych przechwyconych przez Application Insights, w tym ślady, zdarzenia i wyjątki. 

Na poniższym zrzucie ekranu pokazano, jak jest rejestrowany pojedynczy ślad dla zadania, a później jest on wyszukiwany na potrzeby debugowania.

![Zrzut ekranu przedstawiający dzienniki dla pojedynczego śledzenia.](./media/monitor-application-insights/tracelogsfortask.png)

### <a name="view-unhandled-exceptions"></a>Wyświetlanie nieobsługiwanych wyjątków

Application Insights rejestruje wyjątki zgłaszane przez aplikację. W takim przypadku w ciągu kilku sekund od zgłoszenia wyjątku przez aplikację można przejść do szczegółów określonego wyjątku i zdiagnozować problem.

![Zrzut ekranu przedstawiający nieobsługiwane wyjątki.](./media/monitor-application-insights/exception.png)

### <a name="measure-blob-download-time"></a>Mierzenie czasu pobierania obiektu blob

Metryki niestandardowe są również cennym narzędziem w portalu. Można na przykład wyświetlić średni czas pobierania wymaganego pliku tekstowego, który był przetwarzany przez każdy węzeł obliczeniowy.

Aby utworzyć przykładowy wykres:

1. W zasobie Application Insights kliknij pozycję **Eksplorator metryk**  >  **Dodaj wykres.**
1. Kliknij **pozycję** Edytuj na dodanym wykresie.
1. Zaktualizuj szczegóły wykresu w następujący sposób:
   - Ustaw **typ wykresu** na **Siatka**.
   - Ustaw **agregację** na **wartość Średnia.**
   - Ustaw **ustawienie Grupuj według** na wartość **NodeId**.
   - W **metryce** wybierz pozycję **Pobieranie**  >  **niestandardowego obiektu blob w ciągu kilku sekund.**
   - Dostosuj **paletę kolorów wyświetlania** do wybranej opcji.

![Zrzut ekranu przedstawiający wykres przedstawiający czas pobierania obiektów blob na węzeł.](./media/monitor-application-insights/blobdownloadtime.png)

## <a name="monitor-compute-nodes-continuously&quot;></a>Ciągłe monitorowanie węzłów obliczeniowych

Można zauważyć, że wszystkie metryki, w tym liczniki wydajności, są rejestrowane tylko wtedy, gdy zadania są uruchomione. To zachowanie jest przydatne, ponieważ ogranicza ilość danych, które Application Insights dzienniki. Istnieją jednak przypadki, w których zawsze chcesz monitorować węzły obliczeniowe. Na przykład mogą one uruchamiać pracę w tle, która nie jest zaplanowana za pośrednictwem usługi Batch. W takim przypadku skonfiguruj proces monitorowania do działania przez okres życia węzła obliczeniowego. 

Jednym ze sposobów osiągnięcia tego zachowania jest zduplikowanie procesu, który ładuje bibliotekę Application Insights i działa w tle. W tym przykładzie zadanie uruchamiania ładuje pliki binarne na maszynie i utrzymuje proces uruchomiony przez czas nieokreślony. Skonfiguruj plik Application Insights dla tego procesu, aby emitować dodatkowe dane, które Cię interesują, takie jak liczniki wydajności.

```csharp
...
 // Batch start task telemetry runner
private const string BatchStartTaskFolderName = &quot;StartTask&quot;;
private const string BatchStartTaskTelemetryRunnerName = &quot;Microsoft.Azure.Batch.Samples.TelemetryStartTask.exe&quot;;
private const string BatchStartTaskTelemetryRunnerAIConfig = &quot;ApplicationInsights.config&quot;;
...
CloudPool pool = client.PoolOperations.CreatePool(
    topNWordsConfiguration.PoolId,
    targetDedicated: topNWordsConfiguration.PoolNodeCount,
    virtualMachineSize: &quot;standard_d1_v2&quot;,
    VirtualMachineConfiguration: new VirtualMachineConfiguration(
    imageReference: new ImageReference(
                        publisher: &quot;MicrosoftWindowsServer&quot;,
                        offer: &quot;WindowsServer&quot;,
                        sku: &quot;2019-datacenter-core&quot;,
                        version: &quot;latest"),
    nodeAgentSkuId: "batch.node.windows amd64");
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
> Aby zwiększyć możliwości zarządzania rozwiązaniem, możesz utworzyć pakiet zestawu w [pakiecie aplikacji](./batch-application-packages.md). Następnie, aby automatycznie wdrożyć pakiet aplikacji w pulach, dodaj odwołanie do pakietu aplikacji do konfiguracji puli.

## <a name="throttle-and-sample-data"></a>Ograniczanie przepustowości i przykładowe dane

Ze względu na dużą skalę aplikacji Azure Batch działających w środowisku produkcyjnym można ograniczyć ilość danych zbieranych przez Application Insights w celu zarządzania kosztami. Zobacz [Próbkowanie w Application Insights,](../azure-monitor/app/sampling.md) aby uzyskać pewne mechanizmy, które to osiągnąć.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [Application Insights](../azure-monitor/app/app-insights-overview.md).
- Aby Application Insights w innych językach, zobacz dokumentację [dotyczącą języków, platform i integracji.](../azure-monitor/app/platforms.md)
