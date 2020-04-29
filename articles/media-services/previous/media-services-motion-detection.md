---
title: Wykrywaj ruchy przy użyciu Azure Media Analytics | Microsoft Docs
description: Procesor Media Azure Media Motion Detector (MP) pozwala wydajnie identyfikować interesujące Cię sekcje w innym pliku wideo długim i niezdarzeń.
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
ms.date: 03/19/2019
ms.author: juliako
ms.reviewer: milanga
ms.openlocfilehash: f4c021531a4d04bf16e5dbee4172952433f675d9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77913008"
---
# <a name="detect-motions-with-azure-media-analytics"></a>Wykrywaj ruchy przy użyciu Azure Media Analytics

> [!NOTE]
> Procesor multimediów **Azure Media Motion Detector** zostanie wycofany. Aby uzyskać datę wycofania, zobacz temat [starsze składniki](legacy-components.md) .
 
## <a name="overview"></a>Omówienie

Procesor Media **Azure Media Motion Detector** (MP) pozwala wydajnie identyfikować interesujące Cię sekcje w innym pliku wideo długim i niezdarzeń. Wykrywanie ruchu może być używane na potrzeby statycznego filmu kamery do identyfikowania sekcji wideo, w których odbywa się ruch. Generuje plik JSON zawierający metadane z sygnaturami czasowymi i obszarem ograniczenia, w którym wystąpiło zdarzenie.

Technologia ta umożliwia kategoryzowanie ruchu na odpowiednich zdarzeniach i fałszywych wartościach, takich jak cieniowanie i oświetlenie. Dzięki temu można generować alerty zabezpieczeń z kanałów informacyjnych z aparatu, bez konieczności spamu, przy jednoczesnym wykorzystaniu momentu, w którym można wyodrębnić czas zainteresowania z długiego wideo nadzoru.

Pakiet MP **Azure Media Motion Detector** jest obecnie w wersji zapoznawczej.

Ten artykuł zawiera szczegółowe informacje dotyczące **Azure Media Motion Detector** i pokazuje, jak używać go z zestawem SDK Media Services dla platformy .NET

## <a name="motion-detector-input-files"></a>Pliki wejściowe czujnika ruchu
Pliki wideo. Obecnie obsługiwane są następujące formaty: MP4, MOV i WMV.

## <a name="task-configuration-preset"></a>Konfiguracja zadania (ustawienie wstępne)
Podczas tworzenia zadania z **Azure Media Motion Detector**należy określić ustawienia wstępne konfiguracji. 

### <a name="parameters"></a>Parametry
Można użyć następujących parametrów:

| Nazwa | Opcje | Opis | Domyślny |
| --- | --- | --- | --- |
| sensitivityLevel |Ciąg: "Low", "medium", "High" |Ustawia poziom czułości, przy którym są raportowane ruchy. Dostosuj ten sposób, aby dopasować liczbę fałszywie dodatnich. |średniookresow |
| frameSamplingValue |Dodatnia liczba całkowita |Ustawia częstotliwość uruchamiania algorytmu. 1 równa się każdej klatce, 2 oznacza każdą klatkę i tak dalej. |1 |
| detectLightChange |Wartość logiczna: "true", "false" |Ustawia, czy jasne zmiany są raportowane w wynikach |False |
| mergeTimeThreshold |Godzina xs: hh: mm: SS<br/>Przykład: 00:00:03 |Określa przedział czasu między zdarzeniami ruchu, w przypadku których 2 zdarzenia są łączone i raportowane jako 1. |00:00:00 |
| detectionZones |Tablica stref wykrywania:<br/>— Strefa wykrywania jest tablicą z 3 lub więcej punktów<br/>-Point to współrzędna x i y od 0 do 1. |Opisuje listę wielokątaowych stref wykrywania, które mają być używane.<br/>Wyniki są raportowane ze strefami jako identyfikator, a pierwszy z nich to "ID": 0 |Pojedynczej strefy, która obejmuje całą ramkę. |

### <a name="json-example"></a>Przykład JSON

```json
    {
      "version": "1.0",
      "options": {
        "sensitivityLevel": "medium",
        "frameSamplingValue": 1,
        "detectLightChange": "False",
        "mergeTimeThreshold":
        "00:00:02",
        "detectionZones": [
          [
            {"x": 0, "y": 0},
            {"x": 0.5, "y": 0},
            {"x": 0, "y": 1}
           ],
          [
            {"x": 0.3, "y": 0.3},
            {"x": 0.55, "y": 0.3},
            {"x": 0.8, "y": 0.3},
            {"x": 0.8, "y": 0.55},
            {"x": 0.8, "y": 0.8},
            {"x": 0.55, "y": 0.8},
            {"x": 0.3, "y": 0.8},
            {"x": 0.3, "y": 0.55}
          ]
        ]
      }
    }
```

## <a name="motion-detector-output-files"></a>Pliki wyjściowe czujnika ruchu
Zadanie wykrywania ruchu zwraca plik JSON w wyjściowym elemencie zawartości, który opisuje alerty ruchu i ich kategorie w wideo. Plik zawiera informacje o czasie i czasie trwania ruchu wykrytym w filmie wideo.

Interfejs API wykrywania ruchu udostępnia wskaźniki, gdy istnieją obiekty w ruchu w stałym wideo w tle (na przykład wideo z nadzorem). Detektor ruchu jest szkolony, aby zmniejszyć liczbę fałszywych alarmów, takich jak oświetlenie i zmiany w tle. Bieżące ograniczenia dotyczące algorytmów obejmują wideo z nocną obsługą, obiekty częściowo przezroczyste i małe obiekty.

### <a name="elements-of-the-output-json-file"></a><a id="output_elements"></a>Elementy wyjściowego pliku JSON
> [!NOTE]
> W najnowszej wersji format wyjściowego JSON został zmieniony i może reprezentować nieprzerwaną zmianę dla niektórych klientów.
> 
> 

W poniższej tabeli opisano elementy wyjściowego pliku JSON.

| Element | Opis |
| --- | --- |
| Wersja |Odnosi się to do wersji interfejsu API wideo. Bieżąca wersja to 2. |
| Dział |"Ticks" na sekundę filmu wideo. |
| przesunięcie |Przesunięcie czasu dla sygnatur czasowych w "Takty". W wersji 1,0 interfejsów API wideo zawsze będzie równa 0. W przyszłych scenariuszach dział IT może zmienić tę wartość. |
| szybkości |Liczba klatek na sekundę w wideo. |
| Szerokość, Wysokość |Odnosi się do szerokości i wysokości wideo w pikselach. |
| rozpoczynanie |Początkowa sygnatura czasowa w "taktach". |
| czas trwania |Długość zdarzenia w "taktach". |
| interval |Interwał każdego wpisu w zdarzeniu w "Takty". |
| zdarzenia |Każdy fragment zdarzenia zawiera ruch wykryty w tym czasie. |
| type |W bieżącej wersji jest to zawsze "2" dla ruchu ogólnego. Ta etykieta umożliwia interfejsom API wideo elastyczność kategoryzowania ruchu w przyszłych wersjach. |
| regionId |Zgodnie z powyższym opisem, ta wersja będzie zawsze równa 0. Ta etykieta daje interfejsowi API wideo elastyczność umożliwiającą znalezienie ruchu w różnych regionach w przyszłych wersjach. |
| regionach |Odnosi się do obszaru w Twoim wideo, w którym zawarto informacje o ruchu. <br/><br/>-"ID" reprezentuje obszar regionu — w tej wersji istnieje tylko jeden identyfikator 0. <br/>-"Type" reprezentuje kształt regionu, który ma być związany z ruchem. Aktualnie "prostokąt" i "Wielokąt" są obsługiwane.<br/> Jeśli określono "prostokąt", region ma wymiary w X, Y, Szerokość i wysokość. Współrzędne X i Y reprezentują górne współrzędne XY w regionie w znormalizowanej skali od 0,0 do 1,0. Szerokość i wysokość przedstawiają rozmiar regionu w znormalizowanej skali od 0,0 do 1,0. W bieżącej wersji, X, Y, Szerokość i wysokość są zawsze stałe na 0, 0 i 1, 1. <br/>Jeśli określono "Wielokąt", region ma wymiary w punktach. <br/> |
| elementy |Metadane są podzielone na różne segmenty o nazwie fragmenty. Każdy fragment zawiera rozpoczęcie, czas trwania, wartość interwału i zdarzenia. Fragment bez zdarzeń oznacza, że nie wykryto żadnego ruchu w czasie i czasie trwania. |
| nawiasy kwadratowe [] |Każdy nawias reprezentuje jeden interwał w zdarzeniu. Puste nawiasy dla tego interwału oznacza, że nie wykryto żadnego ruchu. |
| locations |Ten nowy wpis w obszarze zdarzenia zawiera listę lokalizacji, w której wystąpiło ruch. Jest to bardziej szczegółowe niż strefy wykrywania. |

Poniższy przykład JSON przedstawia dane wyjściowe:

```json
    {
      "version": 2,
      "timescale": 23976,
      "offset": 0,
      "framerate": 24,
      "width": 1280,
      "height": 720,
      "regions": [
        {
          "id": 0,
          "type": "polygon",
          "points": [{'x': 0, 'y': 0},
            {'x': 0.5, 'y': 0},
            {'x': 0, 'y': 1}]
        }
      ],
      "fragments": [
        {
          "start": 0,
          "duration": 226765
        },
        {
          "start": 226765,
          "duration": 47952,
          "interval": 999,
          "events": [
            [
              {
                "type": 2,
                "typeName": "motion",
                "locations": [
                  {
                    "x": 0.004184,
                    "y": 0.007463,
                    "width": 0.991667,
                    "height": 0.985185
                  }
                ],
                "regionId": 0
              }
            ],
```

## <a name="limitations"></a>Ograniczenia
* Obsługiwane formaty wideo to MP4, MOV i WMV.
* Wykrywanie ruchu jest zoptymalizowane pod kątem stacjonarnych filmów w tle. Algorytm koncentruje się na zmniejszeniu fałszywych alarmów, takich jak zmiany oświetlenia i cienie.
* Niektóre ruchy mogą nie zostać wykryte ze względu na wyzwania techniczne; na przykład filmy wideo z nocną wzrokiem, obiekty częściowo przezroczyste i małe obiekty.

## <a name="net-sample-code"></a>Przykładowy kod platformy .NET

Poniższy program pokazuje, jak:

1. Utwórz element zawartości i Przekaż plik multimedialny do elementu zawartości.
2. Utwórz zadanie przy użyciu zadania wykrywania ruchu wideo na podstawie pliku konfiguracji, który zawiera następujące ustawienia wstępne JSON: 
   
    ```json
            {
            "Version": "1.0",
            "Options": {
                "SensitivityLevel": "medium",
                "FrameSamplingValue": 1,
                "DetectLightChange": "False",
                "MergeTimeThreshold":
                "00:00:02",
                "DetectionZones": [
                [
                    {"x": 0, "y": 0},
                    {"x": 0.5, "y": 0},
                    {"x": 0, "y": 1}
                ],
                [
                    {"x": 0.3, "y": 0.3},
                    {"x": 0.55, "y": 0.3},
                    {"x": 0.8, "y": 0.3},
                    {"x": 0.8, "y": 0.55},
                    {"x": 0.8, "y": 0.8},
                    {"x": 0.55, "y": 0.8},
                    {"x": 0.3, "y": 0.8},
                    {"x": 0.3, "y": 0.55}
                ]
                ]
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

namespace VideoMotionDetection
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

            // Run the VideoMotionDetection job.
            var asset = RunVideoMotionDetectionJob(@"C:\supportFiles\VideoMotionDetection\BigBuckBunny.mp4",
                                        @"C:\supportFiles\VideoMotionDetection\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\VideoMotionDetection\Output");
        }

        static IAsset RunVideoMotionDetectionJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My Video Motion Detection Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Video Motion Detection Job");

            // Get a reference to Azure Media Motion Detector.
            string MediaProcessorName = "Azure Media Motion Detector";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Video Motion Detection Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Video Motion Detection Output Asset", AssetCreationOptions.None);

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

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Powiązane linki
[Blog wykrywania ruchu Azure Media Services](https://azure.microsoft.com/blog/motion-detector-update/)

[Omówienie Azure Media Services Analytics](media-services-analytics-overview.md)

[Demonstracje Azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

