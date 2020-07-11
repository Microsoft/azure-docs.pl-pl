---
title: Użyj Azure Media Video Thumbnails, aby utworzyć podsumowanie wideo | Microsoft Docs
description: Podsumowanie wideo może pomóc w tworzeniu podsumowań długich filmów wideo przez automatyczne wybieranie interesujących fragmentów kodu z obrazu źródłowego. Jest to przydatne, gdy chcesz szybko zapoznać się z oczekiwaniami w długim filmie wideo.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: a245529f-3150-4afc-93ec-e40d8a6b761d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.reviewer: milanga
ms.openlocfilehash: 1b85cffe497ad0b374edb18a1089412da839cdf8
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86231849"
---
# <a name="use-azure-media-video-thumbnails-to-create-a-video-summarization"></a>Użyj Azure Media Video Thumbnails, aby utworzyć podsumowanie wideo  

> [!NOTE]
> Procesor multimediów **Azure Media Video thumbnails** zostanie wycofany. Aby uzyskać datę wycofania, zobacz temat [starsze składniki](legacy-components.md) .

## <a name="overview"></a>Omówienie

Procesor Media **Azure Media Video thumbnails** (MP) umożliwia tworzenie podsumowania filmu wideo, który jest przydatny dla klientów, którzy po prostu chcą obejrzeć podsumowanie długiego wideo. Na przykład klienci mogą chcieć zobaczyć krótkie "Podsumowanie wideo" po umieszczeniu wskaźnika myszy na miniaturie. Przez dostosowanie parametrów **Azure Media Video thumbnails** za pomocą ustawień wstępnych konfiguracji, można użyć zaawansowanego wykrywania zastrzelonych pakietów MP i technologii łączenia, aby algorithmically wygenerować opisowy podklip.  

Pakiet MP **usługi Azure Media Video** jest obecnie w wersji zapoznawczej.

Ten artykuł zawiera szczegółowe informacje na temat **miniatury usługi Azure Media Video** i pokazuje, jak używać jej z zestawem SDK Media Services dla platformy .NET.

## <a name="limitations"></a>Ograniczenia

W niektórych przypadkach, jeśli film wideo nie zawiera różnych scen, dane wyjściowe będą dotyczyły tylko pojedynczego zrzutu.

## <a name="video-summary-example"></a>Przykład podsumowania wideo
Poniżej przedstawiono kilka przykładów możliwości procesora multimediów Azure Media Video Thumbnails:

### <a name="original-video"></a>Oryginalne wideo
[Oryginalne wideo](https://ampdemo.azureedge.net/azuremediaplayer.html?url=httpss%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Faed33834-ec2d-4788-88b5-a4505b3d032c%2FMicrosoft%27s%20HoloLens%20Live%20Demonstration.ism%2Fmanifest)

### <a name="video-thumbnail-result"></a>Wynik miniatury wideo
[Wynik miniatury wideo](https://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Ff5c91052-4232-41d4-b531-062e07b6a9ae%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

## <a name="task-configuration-preset"></a>Konfiguracja zadania (ustawienie wstępne)
Podczas tworzenia zadania miniatury wideo przy użyciu **Azure Media Video thumbnails**należy określić ustawienie wstępne konfiguracji. Powyższy przykład miniatury został utworzony z następującą podstawową konfiguracją JSON:

```json
    {
        "version":"1.0"
    }
```

Obecnie można zmienić następujące parametry:

| Param | Opis |
| --- | --- |
| outputAudio |Określa, czy wynikowy film wideo zawiera dźwięk. <br/>Dozwolone wartości to: true lub false. Wartość domyślna to true. |
| fadeInFadeOut |Określa, czy przejścia zanikania są używane między oddzielnymi miniaturkami ruchu.  <br/>Dozwolone wartości to: true lub false.  Wartość domyślna to true. |
| maxMotionThumbnailDurationInSecs |Liczba całkowita określająca czas trwania całego wynikowego wideo.  Wartość domyślna zależy od oryginalnego czasu trwania wideo. |

W poniższej tabeli opisano domyślny czas trwania, gdy **maxMotionThumbnailInSecs** nie jest używany.

|  | Small | Krocz |
| --- | --- | --- |
| **Czas trwania wideo** |d < 3 min |3 minuty < d < 15 min |
| **Czas trwania miniatury** |15 sek. (2-3 scen) |30 sek. (3-5 scen) |

Poniższe ustawienia JSON określają dostępne parametry.

```json
    {
        "version": "1.0",
        "options": {
            "outputAudio": "true",
            "maxMotionThumbnailDurationInSecs": "10",
            "fadeInFadeOut": "true"
        }
    }
```

## <a name="net-sample-code"></a>Przykładowy kod platformy .NET

Poniższy program pokazuje, jak:

1. Utwórz element zawartości i Przekaż plik multimedialny do elementu zawartości.
2. Tworzy zadanie z miniaturą wideo zadania na podstawie pliku konfiguracji, który zawiera następujące ustawienia wstępne JSON: 
    
    ```json
            {                
                "version": "1.0",
                "options": {
                    "outputAudio": "true",
                    "maxMotionThumbnailDurationInSecs": "30",
                    "fadeInFadeOut": "false"
                }
            }
    ```

3. Pobiera pliki wyjściowe. 

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

    namespace VideoSummarization
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


                // Run the thumbnail job.
                var asset = RunVideoThumbnailJob(@"C:\supportFiles\VideoThumbnail\BigBuckBunny.mp4",
                                            @"C:\supportFiles\VideoThumbnail\config.json");

                // Download the job output asset.
                DownloadAsset(asset, @"C:\supportFiles\VideoThumbnail\Output");
            }

            static IAsset RunVideoThumbnailJob(string inputMediaFilePath, string configurationFile)
            {
                // Create an asset and upload the input media file to storage.
                IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                    "My Video Thumbnail Input Asset",
                    AssetCreationOptions.None);

                // Declare a new job.
                IJob job = _context.Jobs.Create("My Video Thumbnail Job");

                // Get a reference to Azure Media Video Thumbnails.
                string MediaProcessorName = "Azure Media Video Thumbnails";

                var processor = GetLatestMediaProcessorByName(MediaProcessorName);

                // Read configuration from the specified file.
                string configuration = File.ReadAllText(configurationFile);

                // Create a task with the encoding details, using a string preset.
                ITask task = job.Tasks.AddNew("My Video Thumbnail Task",
                    processor,
                    configuration,
                    TaskOptions.None);

                // Specify the input asset.
                task.InputAssets.Add(asset);

                // Add an output asset to contain the results of the job.
                task.OutputAssets.AddNew("My Video Thumbnail Output Asset", AssetCreationOptions.None);

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

### <a name="video-thumbnail-output"></a>Dane wyjściowe miniatur wideo
[Dane wyjściowe miniatur wideo](https://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Fd06f24dc-bc81-488e-a8d0-348b7dc41b56%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Linki pokrewne
[Omówienie Azure Media Services Analytics](media-services-analytics-overview.md)

[Demonstracje Azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

