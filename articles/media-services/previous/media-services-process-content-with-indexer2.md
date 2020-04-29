---
title: Indeksowanie plików multimedialnych z Azure Media Indexer 2 wersja zapoznawcza | Microsoft Docs
description: Azure Media Indexer umożliwia przeszukiwanie zawartości plików multimedialnych oraz generowanie pełnotekstowego transkrypcji napisów i słów kluczowych. W tym temacie przedstawiono sposób korzystania z wersji zapoznawczej programu Media Indexer 2.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/22/2019
ms.author: juliako
ms.reviewer: adsolank
ms.openlocfilehash: c24218dc116803ca0e0a1f166b7b54b24fc4d5ef
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78163798"
---
# <a name="indexing-media-files-with-azure-media-indexer-2-preview"></a>Indeksowanie plików multimedialnych z Azure Media Indexer 2 wersja zapoznawcza

> [!NOTE]
> Procesor multimediów **Azure Media Indexer 2** zostanie wycofany. Aby uzyskać daty wycofania, zobacz temat ten [starszy składnik](legacy-components.md) . [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) zastępuje ten starszy procesor nośnika. Aby uzyskać więcej informacji, zobacz [Migrowanie z Azure Media Indexer i Azure Media Indexer 2 do Azure Media Services Video Indexer](migrate-indexer-v1-v2.md).

Procesor **Azure Media Indexer 2 (wersja zapoznawcza** ) umożliwia udostępnianie plików multimedialnych i zawartości, a także generowanie ścieżek napisów. W porównaniu do poprzedniej wersji [Azure Media Indexer](media-services-index-content.md), **Azure Media Indexer 2 wersja zapoznawcza** wykonuje szybsze indeksowanie i oferuje szersze wsparcie w zakresie języków. Obsługiwane języki to angielski, hiszpański, francuski, niemiecki, włoski, chiński (mandarynki, uproszczony), portugalski, arabski, rosyjski i japoński.

Pakiet MP **Azure Media Indexer 2 w wersji** zapoznawczej jest obecnie w wersji zapoznawczej.

W tym artykule przedstawiono sposób tworzenia zadań indeksowania przy użyciu **wersji Zapoznawczej Azure Media Indexer 2**.

## <a name="considerations"></a>Zagadnienia do rozważenia

Obowiązują następujące zastrzeżenia:
 
* Indeksator 2 nie jest obsługiwany w przypadku usług 21Vianet i Azure Government platformy Azure w Chinach.
* Podczas indeksowania zawartości upewnij się, że używasz plików multimedialnych, które mają bardzo jasne mowę (bez muzyki w tle, szumu, efektów lub HISS mikrofonu). Oto kilka przykładów odpowiedniej zawartości: zarejestrowane spotkania, wykłady lub prezentacje. Następująca zawartość może nie być odpowiednia do indeksowania: filmy, programy telewizyjne, wszystko z mieszaniem dźwięku i dYwiękiem, źle zarejestrowano zawartość z hałasem w tle (HISS).
 
## <a name="input-and-output-files"></a>Pliki wejściowe i wyjściowe
### <a name="input-files"></a>Pliki wejściowe
Pliki audio lub wideo

### <a name="output-files"></a>Pliki wyjściowe
Zadanie indeksowania może generować pliki napisów w następujących formatach:  

* **TTML**
* **Ścieżka**

Pliki napisów (DW) w tych formatach mogą służyć do udostępniania plików audio i wideo osobom niepełnosprawnym.

## <a name="task-configuration-preset"></a>Konfiguracja zadania (ustawienie wstępne)
Podczas tworzenia zadania indeksowania przy użyciu **wersji Zapoznawczej Azure Media Indexer 2**należy określić ustawienie wstępne konfiguracji.

Poniższe ustawienia JSON określają dostępne parametry.

```json
    {
      "version":"1.0",
      "Features":
        [
           {
           "Options": {
                "Formats":["WebVtt","ttml"],
                "Language":"enUs",
                "Type":"RecoOptions"
           },
           "Type":"SpReco"
        }]
    }
```

## <a name="supported-languages"></a>Obsługiwane języki
Wersja zapoznawcza Azure Media Indexer 2 obsługuje zamianę mowy na tekst dla następujących języków (w przypadku określenia nazwy języka w konfiguracji zadania, należy użyć 4-znakowego kodu w nawiasach, jak pokazano poniżej):

* Angielski [EnUs]
* Hiszpański [EsEs]
* Chiński (mandarynki, uproszczony) [ZhCn]
* Francuski [FrFr]
* Niemiecki [DeDe]
* Włoski [ItIt]
* Portugalski [PtBr]
* Arabski (Egipt) [ArEg]
* Japoński [JaJp]
* Rosyjski [RuRu]
* Angielski brytyjski [EnGb]
* Hiszpański (Meksyk) [EsMx] 

## <a name="supported-file-types"></a>Obsługiwane typy plików

Aby uzyskać informacje na temat typów plików obsługiwanych, zobacz sekcję [obsługiwane kodery-dekoder/formaty](media-services-media-encoder-standard-formats.md#input-containerfile-formats) .

## <a name="net-sample-code"></a>Przykładowy kod platformy .NET

Poniższy program pokazuje, jak:

1. Utwórz element zawartości i Przekaż plik multimedialny do elementu zawartości.
2. Utwórz zadanie przy użyciu zadania indeksowania opartego na pliku konfiguracji, który zawiera następujące ustawienia wstępne JSON:

    ```json
            {
            "version":"1.0",
            "Features":
                [
                {
                "Options": {
                        "Formats":["WebVtt","ttml"],
                        "Language":"enUs",
                        "Type":"RecoOptions"
                },
                "Type":"SpReco"
                }]
            }
    ```
    
3. Pobierz pliki wyjściowe. 
   
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

namespace IndexContent
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

            // Run indexing job.
            var asset = RunIndexingJob(@"C:\supportFiles\Indexer\BigBuckBunny.mp4",
                                        @"C:\supportFiles\Indexer\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\Indexer\Output");
        }

        static IAsset RunIndexingJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My Indexing Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Indexing Job");

            // Get a reference to Azure Media Indexer 2 Preview.
            string MediaProcessorName = "Azure Media Indexer 2 Preview";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Indexing Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset to be indexed.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);

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
[Omówienie Azure Media Services Analytics](media-services-analytics-overview.md)

[Demonstracje Azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

