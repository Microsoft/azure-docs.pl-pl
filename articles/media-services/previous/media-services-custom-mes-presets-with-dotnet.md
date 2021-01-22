---
title: Dostosowywanie ustawień wstępnych Media Encoder Standard | Microsoft Docs
description: W tym temacie pokazano, jak wykonać zaawansowane kodowanie przez dostosowanie Media Encoder Standard ustawień wstępnych zadań. W tym temacie pokazano, jak za pomocą zestawu SDK programu Media Services .NET utworzyć zadanie kodowania i zadanie. Przedstawiono w nim również sposób dostarczania niestandardowych ustawień wstępnych do zadania kodowania.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: ec95392f-d34a-4c22-a6df-5274eaac445b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/26/2019
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: 6c1c74f86a9cf0e4bcd73844222f256a715cbfe5
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98695894"
---
# <a name="customizing-media-encoder-standard-presets"></a>Dostosowywanie ustawień wstępnych Media Encoder Standard

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]  

## <a name="overview"></a>Omówienie

W tym artykule pokazano, jak wykonać zaawansowane kodowanie za pomocą Media Encoder Standard (MES) przy użyciu niestandardowego ustawienia wstępnego. W tym artykule jest używane środowisko .NET do tworzenia zadania kodowania i zadania, które wykonuje to zadanie.  

W tym artykule pokazano, jak dostosować ustawienie wstępne, pobierając [wielokrotna H264 wiele szybkości transmisji bitów 720](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) i zmniejszając liczbę warstw. W artykule [dostosowanie Media Encoder Standard predefiniowane](media-services-advanced-encoding-with-mes.md) przedstawiono niestandardowe ustawienia wstępne, których można użyć do wykonywania zaawansowanych zadań kodowania.

> [!NOTE]
> Niestandardowych ustawień predefiniowanych opisanych w tym artykule nie można używać w transformacjech [Media Services v3](../latest/index.yml) ani poleceń interfejsu wiersza polecenia. Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące migracji od wersji 2 do V3](../latest/migrate-v-2-v-3-migration-introduction.md) .

## <a name="customizing-a-mes-preset"></a><a id="customizing_presets"></a> Dostosowywanie ustawień wstępnych MES

### <a name="original-preset"></a>Oryginalne ustawienie wstępne

Zapisz kod JSON zdefiniowany w artykule [wielokrotna H264 o wielu szybkościach transmisji bitów](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) w niektórych plikach z rozszerzeniem. JSON. Na przykład **CustomPreset_JSON.js**.

### <a name="customized-preset"></a>Dostosowane ustawienia wstępne

Otwórz **CustomPreset_JSON.js** pliku i Usuń pierwsze trzy warstwy z **H264Layers** , aby plik wyglądał następująco.

```json 
  {  
    "Version": 1.0,  
    "Codecs": [  
      {  
        "KeyFrameInterval": "00:00:02",  
        "H264Layers": [  
          {  
            "Profile": "Auto",  
            "Level": "auto",  
            "Bitrate": 1000,  
            "MaxBitrate": 1000,  
            "BufferWindow": "00:00:05",  
            "Width": 640,  
            "Height": 360,  
            "BFrames": 3,  
            "ReferenceFrames": 3,  
            "AdaptiveBFrame": true,  
            "Type": "H264Layer",  
            "FrameRate": "0/1"  
          },  
          {  
            "Profile": "Auto",  
            "Level": "auto",  
            "Bitrate": 650,  
            "MaxBitrate": 650,  
            "BufferWindow": "00:00:05",  
            "Width": 640,  
            "Height": 360,  
            "BFrames": 3,  
            "ReferenceFrames": 3,  
            "AdaptiveBFrame": true,  
            "Type": "H264Layer",  
            "FrameRate": "0/1"  
          },  
          {  
            "Profile": "Auto",  
            "Level": "auto",  
            "Bitrate": 400,  
            "MaxBitrate": 400,  
            "BufferWindow": "00:00:05",  
            "Width": 320,  
            "Height": 180,  
            "BFrames": 3,  
            "ReferenceFrames": 3,  
            "AdaptiveBFrame": true,  
            "Type": "H264Layer",  
            "FrameRate": "0/1"  
          }  
        ],  
        "Type": "H264Video"  
      },  
      {  
        "Profile": "AACLC",  
        "Channels": 2,  
        "SamplingRate": 48000,  
        "Bitrate": 128,  
        "Type": "AACAudio"  
      }  
    ],  
    "Outputs": [  
      {  
        "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",  
        "Format": {  
          "Type": "MP4Format"  
        }  
      }  
    ]  
  }  
```

## <a name="encoding-with-media-services-net-sdk"></a><a id="encoding_with_dotnet"></a>Kodowanie przy użyciu zestawu SDK programu Media Services .NET

Poniższy przykład kodu używa Media Services .NET SDK do wykonywania następujących zadań:

- Utwórz zadanie kodowania.
- Pobierz odwołanie do kodera Media Encoder Standard.
- Załaduj niestandardowe ustawienie wstępne JSON, które zostało utworzone w poprzedniej sekcji. 

    ```csharp
    // Load the JSON from the local file.
    string configuration = File.ReadAllText(fileName);  
    ```

- Dodaj zadanie kodowania do zadania. 
- Określ zasób wejściowy do zakodowania.
- Utwórz zasób wyjściowy, który zawiera zakodowany element zawartości.
- Dodaj procedurę obsługi zdarzeń, aby sprawdzić postęp zadania.
- Przesyłanie zadania.
   
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

namespace CustomizeMESPresests
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

        private static readonly string _mediaFiles =
            Path.GetFullPath(@"../..\Media");

        private static readonly string _singleMP4File =
            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Get an uploaded asset.
            var asset = _context.Assets.FirstOrDefault();

            // Encode and generate the output using custom presets.
            EncodeToAdaptiveBitrateMP4Set(asset);

            Console.ReadLine();
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Load the XML (or JSON) from the local file.
            string configuration = File.ReadAllText("CustomPreset_JSON.json");

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
            processor,
            configuration,
            TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);
            // Add an output asset to contain the results of the job. 
            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is not encrypted. 
            task.OutputAssets.AddNew("Output asset",
            AssetCreationOptions.None);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
        }

        private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);
            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
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
                    break;
                default:
                    break;
            }
        }

        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

            return processor;
        }

    }
}
```

## <a name="see-also"></a>Zobacz także

- [Jak kodować za pomocą przekształcenia niestandardowego przy użyciu interfejsu wiersza polecenia](../latest/custom-preset-cli-howto.md)
- [Kodowanie przy użyciu usługi Media Services w wersji 3](../latest/encoding-concept.md)

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
