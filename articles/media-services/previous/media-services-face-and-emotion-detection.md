---
title: Wykrywanie kroju i rozpoznawania emocji przy użyciu Azure Media Analytics | Microsoft Docs
description: W tym temacie pokazano, jak wykrywać twarze i emocji przy użyciu Azure Media Analytics.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 5ca4692c-23f1-451d-9d82-cbc8bf0fd707
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.reviewer: milanga
ms.openlocfilehash: 2d746167f993438e5fce467365844df2078c08a6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77919315"
---
# <a name="detect-face-and-emotion-with-azure-media-analytics"></a>Wykrywanie kroju i rozpoznawania emocji przy użyciu Azure Media Analytics

> [!NOTE]
> Procesor multimediów **Azure Media Face Detector** zostanie wycofany. Aby uzyskać datę wycofania, zobacz temat [starsze składniki](legacy-components.md) .

## <a name="overview"></a>Omówienie

Procesor Media **Azure Media Face Detector** (MP) umożliwia zliczanie, śledzenie przesunięć, a nawet ocenia uczestnictwo i reagowanie odbiorców przy użyciu wyrażeń twarzy. Ta usługa zawiera dwie funkcje: 

* **Wykrywanie twarzy**
  
    Wykrywanie twarzy wykrywa i śledzi ludzkie twarze w filmie wideo. Można wykryć wiele twarzy, a następnie śledzić je w miarę poruszania się, z metadanymi czasu i lokalizacji zwracanymi w pliku JSON. Podczas śledzenia próbuje nadawać spójny identyfikator do tej samej pory, gdy osoba jest przenoszona na ekran, nawet jeśli są one zakłócone lub krótko opuszczają ramkę.
  
  > [!NOTE]
  > Ta usługa nie wykonuje rozpoznawania twarzy. Osoba, która opuści ramkę lub nie jest zbyt długa, otrzymuje nowy identyfikator, gdy zwróci.
  > 
  > 
* **Wykrywanie rozpoznawania emocji**
  
    Wykrywanie rozpoznawania emocji jest opcjonalnym składnikiem procesora multimediów wykrywanie twarzy, który zwraca analizę dla wielu atrybutów emocjonalnej z wykrytych twarzy, w tym szczęście, smutek, obaw, gniew i wiele innych. 

Pakiet MP **Azure Media Face Detector** jest obecnie w wersji zapoznawczej.

Ten artykuł zawiera szczegółowe informacje dotyczące **Azure Media Face Detector** i pokazuje, jak używać go z zestawem SDK Media Services dla platformy .NET.

## <a name="face-detector-input-files"></a>Pliki wejściowe czujnika kroju
Pliki wideo. Obecnie obsługiwane są następujące formaty: MP4, MOV i WMV.

## <a name="face-detector-output-files"></a>Pliki wyjściowe czujnika kroju
Interfejs API wykrywania i śledzenia twarzy zapewnia wykrywanie i śledzenie lokalizacji twarzy o wysokiej precyzji, które mogą wykrywać nawet 64 ludzkich buziek wideo. Twarze czołowe zapewniają najlepsze wyniki, a powierzchnie boczne i małe twarze (mniejsze niż lub równe 24x24 pikseli) mogą być niedokładne.

Wykryte i śledzone powierzchnie są zwracane ze współrzędnymi (z lewej, góry, szerokości i wysokości) wskazujące lokalizację powierzchni w obrazie w pikselach, a także numer IDENTYFIKACYJNy twarzy wskazujący na śledzenie tej osoby. Numery IDENTYFIKACYJNe kroju są podatne na zresetowanie w warunkach, gdy czołowa powierzchnia zostanie utracona lub nakłada się w ramkę, co oznacza, że niektóre osoby mogą uzyskać przypisane wiele identyfikatorów.

## <a name="elements-of-the-output-json-file"></a><a id="output_elements"></a>Elementy wyjściowego pliku JSON

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

Wykrywanie kroju korzysta z technik fragmentacji (gdzie metadane można rozbić w fragmenty czasu i można pobrać tylko te, czego potrzebujesz) i segmentacji (gdzie zdarzenia są podzielone na wypadek, gdyby były zbyt duże). W transformacji danych może pomóc kilka prostych obliczeń. Na przykład, jeśli zdarzenie zostało uruchomione o godzinie 6300 (Takty), z przedziałem czasu 2997 (Takty/s) i szybkością 29,97 (ramki/s), wówczas:

* Pocżątek/skala czasu= 2,1 s
* Sekundy x klatek = 63 klatek

## <a name="face-detection-input-and-output-example"></a>Przykład danych wejściowych i wyjściowych wykrywania kroju
### <a name="input-video"></a>Wejściowy film wideo
[Wejściowy film wideo](https://ampdemo.azureedge.net/azuremediaplayer.html?url=httpss%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Konfiguracja zadania (ustawienie wstępne)
Podczas tworzenia zadania z **Azure Media Face Detector**należy określić ustawienia wstępne konfiguracji. Poniższe ustawienia wstępne konfiguracji są przeznaczone tylko do wykrywania kroju.

```json
    {
      "version":"1.0",
      "options":{
          "TrackingMode": "Fast"
      }
    }
```

#### <a name="attribute-descriptions"></a>Opisy atrybutów
| Nazwa atrybutu | Opis |
| --- | --- |
| Tryb |Szybka szybkość przetwarzania, ale mniej dokładne (wartość domyślna).|

### <a name="json-output"></a>Dane wyjściowe JSON
Poniższy przykład danych wyjściowych JSON został obcięty.

```json
    {
    "version": 1,
    "timescale": 30000,
    "offset": 0,
    "framerate": 29.97,
    "width": 1280,
    "height": 720,
    "fragments": [
        {
        "start": 0,
        "duration": 60060
        },
        {
        "start": 60060,
        "duration": 60060,
        "interval": 1001,
        "events": [
            [
            {
                "id": 0,
                "x": 0.519531,
                "y": 0.180556,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517969,
                "y": 0.181944,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517187,
                "y": 0.183333,
                "width": 0.0851562,
                "height": 0.151389
            }
            ],
```


## <a name="emotion-detection-input-and-output-example"></a>Przykład danych wejściowych i wyjściowych wykrywania rozpoznawania emocji
### <a name="input-video"></a>Wejściowy film wideo
[Wejściowy film wideo](https://ampdemo.azureedge.net/azuremediaplayer.html?url=httpss%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Konfiguracja zadania (ustawienie wstępne)
Podczas tworzenia zadania z **Azure Media Face Detector**należy określić ustawienia wstępne konfiguracji. Poniższe ustawienie wstępne konfiguracji określa, aby utworzyć kod JSON na podstawie wykrywania rozpoznawania emocji.

```json
    {
      "version": "1.0",
      "options": {
        "aggregateEmotionWindowMs": "987",
        "mode": "aggregateEmotion",
        "aggregateEmotionIntervalMs": "342"
      }
    }
```


#### <a name="attribute-descriptions"></a>Opisy atrybutów
| Nazwa atrybutu | Opis |
| --- | --- |
| Tryb |Twarze: tylko wykrywanie twarzy.<br/>PerFaceEmotion: Zwróć rozpoznawania emocji niezależnie dla każdej detekcji czołowej.<br/>AggregateEmotion: Zwraca średnią wartość rozpoznawania emocji dla wszystkich twarzy w ramce. |
| AggregateEmotionWindowMs |Użyj, jeśli wybrano tryb AggregateEmotion. Określa długość wideo użytą do wygenerowania każdego zagregowanego wyniku w milisekundach. |
| AggregateEmotionIntervalMs |Użyj, jeśli wybrano tryb AggregateEmotion. Określa częstotliwość generowania zagregowanych wyników. |

#### <a name="aggregate-defaults"></a>Wartości domyślne agregacji
Poniżej znajdują się zalecane wartości okna agregacji i ustawień interwału. AggregateEmotionWindowMs powinna być dłuższa niż AggregateEmotionIntervalMs.

|| Wartości domyślne | Maksymalna liczba (s) | Min. |
|--- | --- | --- | --- |
| AggregateEmotionWindowMs |0,5 |2 |0,25|
| AggregateEmotionIntervalMs |0,5 |1 |0,25|

### <a name="json-output"></a>Dane wyjściowe JSON
Dane wyjściowe JSON dla zagregowanych rozpoznawania emocji (obcięty):

```json
    {
     "version": 1,
     "timescale": 30000,
     "offset": 0,
     "framerate": 29.97,
     "width": 1280,
     "height": 720,
     "fragments": [
       {
         "start": 0,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ]
         ]
       },
       {
         "start": 60060,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0.688541,
                 "happiness": 0.0586323,
                 "surprise": 0.227184,
                 "sadness": 0.00945675,
                 "anger": 0.00592107,
                 "disgust": 0.00154993,
                 "fear": 0.00450447,
                 "contempt": 0.0042109
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
```

## <a name="limitations"></a>Ograniczenia
* Obsługiwane formaty wideo to MP4, MOV i WMV.
* Wykrywalny zakres rozmiaru jest 24x24 do 2048x2048 pikseli. Nie zostaną wykryte powierzchnie z tego zakresu.
* Dla każdego filmu wideo Maksymalna liczba zwracanych twarzy to 64.
* Niektóre powierzchnie mogą nie zostać wykryte ze względu na wyzwania techniczne; na przykład bardzo duże kąty kroju (ułożenie głowy) i duże zamknięcia. Najbardziej czołowe i bliskie buźki mają najlepsze wyniki.

## <a name="net-sample-code"></a>Przykładowy kod platformy .NET

Poniższy program pokazuje, jak:

1. Utwórz element zawartości i Przekaż plik multimedialny do elementu zawartości.
2. Utwórz zadanie z zadaniem wykrywania kroju na podstawie pliku konfiguracji, który zawiera następujące ustawienia wstępne JSON: 

    ```json
            {
                "version": "1.0"
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

namespace FaceDetection
{
    class Program
    {
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

            // Run the FaceDetection job.
            var asset = RunFaceDetectionJob(@"C:\supportFiles\FaceDetection\BigBuckBunny.mp4",
                                        @"C:\supportFiles\FaceDetection\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\FaceDetection\Output");
        }

        static IAsset RunFaceDetectionJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My Face Detection Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Face Detection Job");

            // Get a reference to Azure Media Face Detector.
            string MediaProcessorName = "Azure Media Face Detector";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Face Detection Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Face Detection Output Asset", AssetCreationOptions.None);

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

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Linki pokrewne
[Omówienie Azure Media Services Analytics](media-services-analytics-overview.md)

[Demonstracje Azure Media Analytics](https://amslabs.azurewebsites.net/demos/Analytics.html)

