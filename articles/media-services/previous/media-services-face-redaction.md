---
title: Redagowanie twarzy przy użyciu Azure Media Analytics | Microsoft Docs
description: Azure Media Redactor to Azure Media Analyticsy procesor multimediów, który oferuje skalowalne możliwości redakcyjne w chmurze. W tym artykule przedstawiono sposób redagowania twarzy przy użyciu usługi Azure Media Analytics.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: 9a562e31a26cc6cf7188dd635273bd9fa8c57213
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89269475"
---
# <a name="redact-faces-with-azure-media-analytics"></a>Redagowanie twarzy przy użyciu Azure Media Analytics

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Omówienie
**Azure Media redactor** to procesor Media [Azure Media Analytics](media-services-analytics-overview.md) (MP), który oferuje skalowalne możliwości redakcyjne w chmurze. Redakcja twarzy umożliwia modyfikowanie wideo w celu rozmycia powierzchni wybranych osób. Możesz chcieć użyć usługi redakcyjnej ze stroną w scenariuszach bezpieczeństwa publicznego i mediów informacyjnych. Kilka minut filmu, które zawiera wiele twarzy, może zająć więcej czasu, ale w przypadku tej usługi proces redakcyjny twarzy będzie wymagał zaledwie kilku prostych kroków. Aby uzyskać więcej informacji, zobacz [ten](https://azure.microsoft.com/blog/azure-media-redactor/) blog.

Ten artykuł zawiera szczegółowe informacje dotyczące **Azure Media redactor** i pokazuje, jak używać go z zestawem SDK Media Services dla platformy .NET.

## <a name="face-redaction-modes"></a>Tryby redakcyjne
Redakcja twarzy działa przez wykrywanie powierzchni w każdej klatce wideo i śledzenie obiektu twarzy zarówno do przodu, jak i do tyłu, tak aby ta sama osoba mogła być zamazana z innych kątów. Zautomatyzowany proces redakcyjny jest skomplikowany i nie zawsze produkuje 100% żądanych danych wyjściowych. z tego powodu Media Analytics udostępnia kilka sposobów modyfikowania końcowych danych wyjściowych.

Oprócz pełnego trybu automatycznego istnieje dwuprzebiegowy przepływ pracy, który umożliwia zaznaczanie/usuwanie wybranych twarzy przez listę identyfikatorów. Ponadto w celu dostrojenia dowolnej ramki pakiet MP używa pliku metadanych w formacie JSON. Ten przepływ pracy jest podzielony na tryby **analizowania** i **redagowania** . Dwa tryby można połączyć w jednym przebiegu, który uruchamia oba zadania w jednym zadaniu. Ten tryb jest wywoływany **razem**.

### <a name="combined-mode"></a>Tryb połączony
Spowoduje to wygenerowanie redagowane MP4 automatycznie bez żadnych ręcznych danych wejściowych.

| Etap | Nazwa pliku | Uwagi |
| --- | --- | --- |
| Zasób wejściowy |foo. bar |Wideo w formacie WMV, MOV lub MP4 |
| Konfiguracja wejściowa |Ustawienie wstępne konfiguracji zadania |{"Version": "1.0", "Options": {"Mode": "połączony"}} |
| Wyjściowy element zawartości |foo_redacted.mp4 |Film wideo z zastosowanym rozmyciem |

#### <a name="input-example"></a>Przykład danych wejściowych:
[Obejrzyj to wideo](https://ampdemo.azureedge.net/?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fed99001d-72ee-4f91-9fc0-cd530d0adbbc%2FDancing.mp4)

#### <a name="output-example"></a>Przykładowe dane wyjściowe:
[Obejrzyj to wideo](https://ampdemo.azureedge.net/?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc6608001-e5da-429b-9ec8-d69d8f3bfc79%2Fdance_redacted.mp4)

### <a name="analyze-mode"></a>Tryb analizy
**Przeanalizuj** przebieg przepływu pracy dwuprzebiegowej pobiera wideo i tworzy plik JSON lokalizacji, a obrazy jpg każdej wykrytej klasy.

| Etap | Nazwa pliku | Uwagi |
| --- | --- | --- |
| Zasób wejściowy |foo. bar |Wideo w formacie WMV, MPV lub MP4 |
| Konfiguracja wejściowa |Ustawienie wstępne konfiguracji zadania |{"Version": "1.0", "Options": {"Mode": "Analizuj"}} |
| Wyjściowy element zawartości |foo_annotations.jsna |Dane adnotacji lokalizacji czołowych w formacie JSON. Może to być edytowane przez użytkownika w celu zmodyfikowania pól związanych z rozmyciem. Zobacz przykład poniżej. |
| Wyjściowy element zawartości |foo_thumb% 06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg] |Przycięta jpg każdej wykrytej klasy, gdzie liczba wskazuje labelId |

#### <a name="output-example"></a>Przykładowe dane wyjściowe:

```json
    {
      "version": 1,
      "timescale": 24000,
      "offset": 0,
      "framerate": 23.976,
      "width": 1280,
      "height": 720,
      "fragments": [
        {
          "start": 0,
          "duration": 48048,
          "interval": 1001,
          "events": [
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [
              {
                "index": 13,
                "id": 1138,
                "x": 0.29537,
                "y": -0.18987,
                "width": 0.36239,
                "height": 0.80335
              },
              {
                "index": 13,
                "id": 2028,
                "x": 0.60427,
                "y": 0.16098,
                "width": 0.26958,
                "height": 0.57943
              }
            ],

    … truncated
```

### <a name="redact-mode"></a>Tryb redagowania
Drugi przebieg przepływu pracy pobiera większą liczbę danych wejściowych, które muszą być połączone w pojedynczy element zawartości.

Obejmuje to listę identyfikatorów rozmycia, oryginalnego wideo oraz JSON adnotacji. W tym trybie są stosowane adnotacje w celu zastosowania rozmycia danych wejściowych wideo.

Dane wyjściowe z przebiegu analizy nie obejmują oryginalnego wideo. Film wideo musi zostać przekazany do wejściowego zasobu dla zadania tryb Zredaguj i wybrany jako plik podstawowy.

| Etap | Nazwa pliku | Uwagi |
| --- | --- | --- |
| Zasób wejściowy |foo. bar |Wideo w formacie WMV, MPV lub MP4. To samo wideo jak w kroku 1. |
| Zasób wejściowy |foo_annotations.jsna |plik metadanych adnotacji z fazy pierwszej, z opcjonalnymi modyfikacjami. |
| Zasób wejściowy |foo_IDList.txt (opcjonalnie) |Opcjonalna lista oddzielonych od nowa linia identyfikatorów kroju do redagowania. Jeśli pole pozostanie puste, rozmycie wszystkie twarze. |
| Konfiguracja wejściowa |Ustawienie wstępne konfiguracji zadania |{"Version": "1.0", "Options": {"Mode": "Zredaguj"}} |
| Wyjściowy element zawartości |foo_redacted.mp4 |Wideo z rozmyciem stosowane na podstawie adnotacji |

#### <a name="example-output"></a>Przykładowe dane wyjściowe
Jest to wyjście z IDList z wybranym IDENTYFIKATORem.

[Obejrzyj to wideo](https://ampdemo.azureedge.net/?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fad6e24a2-4f9c-46ee-9fa7-bf05e20d19ac%2Fdance_redacted1.mp4)

Przykład foo_IDList.txt
 
```output
1
2
3
```

## <a name="blur-types"></a>Typy rozmycia

W trybie **połączonym** lub **Zredaguj** istnieją 5 różnych trybów rozmycia, z których można skorzystać za pośrednictwem konfiguracji danych wejściowych JSON: **Low**, **Med**, **High**, **Box**i **Black**. Domyślnie jest używana wartość **Med** .

Przykłady typów rozmycia można znaleźć poniżej.

### <a name="example-json"></a>Przykładowy kod JSON:

```json
    {'version':'1.0', 'options': {'Mode': 'Combined', 'BlurType': 'High'}}
```

#### <a name="low"></a>Niski

![Niski](./media/media-services-face-redaction/blur1.png)
 
#### <a name="med"></a>Komitetem

![Komitetem](./media/media-services-face-redaction/blur2.png)

#### <a name="high"></a>Wysoki

![Wysoki](./media/media-services-face-redaction/blur3.png)

#### <a name="box"></a>Box

![Box](./media/media-services-face-redaction/blur4.png)

#### <a name="black"></a>Czarnoskórzy

![Czarny](./media/media-services-face-redaction/blur5.png)

## <a name="elements-of-the-output-json-file"></a>Elementy wyjściowego pliku JSON

Pakiet administracyjny redakcyjny zapewnia wykrywanie i śledzenie lokalizacji twarzy z wysoką dokładnością, które mogą wykrywać nawet 64 ludzkich powierzchni w ramce wideo. Twarze czołowe zapewniają najlepsze wyniki, a jednocześnie twarze i małe twarze (mniejsze niż lub równe 24x24 pikseli) są trudne.

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

## <a name="net-sample-code"></a>Przykładowy kod platformy .NET

Poniższy program pokazuje, jak:

1. Utwórz element zawartości i Przekaż plik multimedialny do elementu zawartości.
2. Utwórz zadanie z zadaniem redakcyjnym ze stroną na podstawie pliku konfiguracji, który zawiera następujące ustawienia wstępne JSON: 

    ```json
            {
                'version':'1.0',
                'options': {
                    'mode':'combined'
                }
            }
    ```

3. Pobierz wyjściowe pliki JSON. 

#### <a name="create-and-configure-a-visual-studio-project"></a>Tworzenie i konfigurowanie projektu programu Visual Studio

Skonfiguruj środowisko projektowe i wypełnij plik app.config przy użyciu informacji dotyczących połączenia, zgodnie z opisem w sekcji [Projektowanie usługi Media Services na platformie .NET](media-services-dotnet-how-to-use.md). 

#### <a name="example"></a>Przykład

```csharp
using System;
using System.Configuration;
using System.IO;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;
using System.Threading.Tasks;

namespace FaceRedaction
{
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        // Field for service context.
        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Run the FaceRedaction job.
            var asset = RunFaceRedactionJob(@"C:\supportFiles\FaceRedaction\SomeFootage.mp4",
                        @"C:\supportFiles\FaceRedaction\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\FaceRedaction\Output");
        }

        static IAsset RunFaceRedactionJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
            "My Face Redaction Input Asset",
            AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Face Redaction Job");

            // Get a reference to Azure Media Redactor.
            string MediaProcessorName = "Azure Media Redactor";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Face Redaction Task",
            processor,
            configuration,
            TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Face Redaction Output Asset", AssetCreationOptions.None);

            // Use the following event handler to check job progress.  
            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

            // Launch the job.
            job.Submit();

            // Check job execution and wait for job to finish.
            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);

            progressJobTask.Wait();

            // If job state is Error, the event handling
            // method for job progress should log errors.  Here we check
            // for error state and exit if needed.
            if (job.State == JobState.Error)
            {
                ErrorDetail error = job.Tasks.First().ErrorDetails.First();
                Console.WriteLine(string.Format("Error: {0}. {1}",
                                error.Code,
                                error.Message));
                return null;
            }

            return job.OutputMediaAssets[0];
        }

        static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
        {
            IAsset asset = _context.Assets.Create(assetName, options);

            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
            assetFile.Upload(filePath);

            return asset;
        }

        static void DownloadAsset(IAsset asset, string outputDirectory)
        {
            foreach (IAssetFile file in asset.AssetFiles)
            {
                file.Download(Path.Combine(outputDirectory, file.Name));
            }
        }

        static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors
            .Where(p => p.Name == mediaProcessorName)
            .ToList()
            .OrderBy(p => new Version(p.Version))
            .LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor",
                                       mediaProcessorName));

            return processor;
        }

        static private void StateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);

            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished.");
                    Console.WriteLine();
                    break;
                case JobState.Canceling:
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    Console.WriteLine("Please wait...\n");
                    break;
                case JobState.Canceled:
                case JobState.Error:
                    // Cast sender as a job.
                    IJob job = (IJob)sender;
                    // Display or log error details as needed.
                    // LogJobStop(job.Id);
                    break;
                default:
                    break;
            }
        }
    }
}
```

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Linki pokrewne
[Omówienie Azure Media Services Analytics](media-services-analytics-overview.md)

[Demonstracje Azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

