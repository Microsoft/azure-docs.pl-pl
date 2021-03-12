---
title: Kodowanie elementu zawartości za pomocą Media Encoder Standard przy użyciu platformy .NET | Microsoft Docs
description: W tym artykule pokazano, jak używać platformy .NET do kodowania zasobów przy użyciu Media Encoder Standard.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 03431b64-5518-478a-a1c2-1de345999274
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.reviewer: anilmur
ms.custom: devx-track-csharp
ms.openlocfilehash: b94ec0b50182381d2a4e1bca09f4c8bc482d13ee
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103014098"
---
# <a name="encode-an-asset-with-media-encoder-standard-using-net"></a>Kodowanie elementu zawartości za pomocą Media Encoder Standard przy użyciu platformy .NET

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]  

Zadania kodowania są jedną z najczęstszych operacji przetwarzania w Media Services. Tworzysz zadania kodowania, aby przekonwertować pliki multimedialne z jednego kodowania na inne. Podczas kodowania można użyć Media Services wbudowanego kodera multimediów. Można również użyć kodera dostarczonego przez partnera Media Services; kodery innych firm są dostępne w portalu Azure Marketplace. 

W tym artykule pokazano, jak używać platformy .NET do kodowania zasobów przy użyciu Media Encoder Standard (MES). Media Encoder Standard jest skonfigurowany przy użyciu jednego z ustawień predefiniowanych koderów opisanych [poniżej](./media-services-mes-presets-overview.md).

Zalecane jest, aby zawsze kodować pliki źródłowe do zestawu MP4 z adaptacyjną szybkością transmisji bitów, a następnie przekonwertować zestaw na żądany format przy użyciu [dynamicznego tworzenia pakietów](media-services-dynamic-packaging-overview.md). 

Jeśli zasoby wyjściowe są szyfrowane magazynem, należy skonfigurować zasady dostarczania zasobów. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zasad dostarczania elementów zawartości](media-services-dotnet-configure-asset-delivery-policy.md).

> [!NOTE]
> MES tworzy plik wyjściowy o nazwie zawierającej pierwsze 32 znaków nazwy pliku wejściowego. Nazwa jest oparta na tym, co jest określone w pliku predefiniowanym. Na przykład "FileName": "{basename} _ {index} {Extension}". {Basename} jest zastępowana przez pierwsze 32 znaków nazwy pliku wejściowego.
> 
> 

### <a name="mes-formats"></a>Formaty MES
[Formaty i kodeki](media-services-media-encoder-standard-formats.md)

### <a name="mes-presets"></a>Ustawienia wstępne usługi MES
Media Encoder Standard jest skonfigurowany przy użyciu jednego z ustawień predefiniowanych koderów opisanych [poniżej](./media-services-mes-presets-overview.md).

### <a name="input-and-output-metadata"></a>Metadane wejściowe i wyjściowe
W przypadku kodowania danych wejściowych (lub zasobów) przy użyciu programu MES można uzyskać wyjściowy element zawartości po pomyślnym zakończeniu tego zadania kodowania. Element zawartości wyjściowej zawiera wideo, audio, miniatury, manifest itp. w zależności od używanego ustawienia wstępnego kodowania.

Wyjściowy element zawartości zawiera również plik z metadanymi zawierającymi dane wejściowe. Nazwa pliku XML metadanych ma następujący format: <asset_id # C1_metadata.xml (na przykład 41114ad3-eb5e-4c57-8d92-5354e2b7d4a4_metadata.xml), gdzie <asset_id,> jest wartością AssetId zawartości wejściowej. Schemat tego pliku XML metadanych wejściowych jest opisany [tutaj](media-services-input-metadata-schema.md).

Wyjściowy element zawartości zawiera również plik z metadanymi dotyczącymi wyjściowego elementu zawartości. Nazwa pliku XML metadanych ma następujący format: <source_file_name # C1_manifest.xml (na przykład BigBuckBunny_manifest.xml). Schemat tego XML metadanych wyjściowych jest opisany [tutaj](media-services-output-metadata-schema.md).

Jeśli chcesz przejrzeć jeden z tych dwóch plików metadanych, możesz utworzyć lokalizatora SAS i pobrać plik na komputer lokalny. Przykład tworzenia lokalizatora SAS i pobierania pliku przy użyciu rozszerzeń zestawu SDK platformy .NET Media Services.

## <a name="download-sample"></a>Pobieranie przykładu
Możesz uzyskać i uruchomić przykład, który pokazuje, jak kodować ze statusem MES w [tym miejscu](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## <a name="net-sample-code"></a>Przykładowy kod platformy .NET

Poniższy przykład kodu używa Media Services .NET SDK do wykonywania następujących zadań:

* Utwórz zadanie kodowania.
* Pobierz odwołanie do kodera Media Encoder Standard.
* Określ, aby użyć [adaptacyjnego ustawienia wstępnego przesyłania strumieniowego](media-services-autogen-bitrate-ladder-with-mes.md) . 
* Dodaj jedno zadanie kodowania do zadania. 
* Określ zasób wejściowy do zakodowania.
* Utwórz zasób wyjściowy, który zawiera zakodowany element zawartości.
* Dodaj procedurę obsługi zdarzeń, aby sprawdzić postęp zadania.
* Przesyłanie zadania.

#### <a name="create-and-configure-a-visual-studio-project"></a>Tworzenie i konfigurowanie projektu programu Visual Studio

Skonfiguruj środowisko projektowe i wypełnij plik app.config przy użyciu informacji dotyczących połączenia, zgodnie z opisem w sekcji [Projektowanie usługi Media Services na platformie .NET](media-services-dotnet-how-to-use.md). 

#### <a name="example"></a>Przykład 

```csharp
using System;
using System.Linq;
using System.Configuration;
using System.IO;
using System.Threading;
using Microsoft.WindowsAzure.MediaServices.Client;

namespace MediaEncoderStandardSample
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

        private static readonly string _supportFiles =
            Path.GetFullPath(@"../..\Media");

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

            // Encode and generate the output using the "Adaptive Streaming" preset.
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

            // Create a task with the encoding details, using a string preset.
            // In this case "Adaptive Streaming" preset is used.
            ITask task = job.Tasks.AddNew("My encoding task",
                processor,
                "Adaptive Streaming",
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

## <a name="advanced-encoding-features-to-explore"></a>Zaawansowane funkcje kodowania do eksplorowania
* [Jak generować miniatury](media-services-dotnet-generate-thumbnail-with-mes.md)
* [Generowanie miniatur podczas kodowania](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [Przytnij wideo podczas kodowania](media-services-crop-video.md)
* [Dostosowywanie ustawień wstępnych kodowania](media-services-custom-mes-presets-with-dotnet.md)
* [Nałożenie wideo z obrazem lub znak wodny](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Następne kroki
[Jak wygenerować miniaturę przy użyciu Media Encoder Standard z platformą .NET](media-services-dotnet-generate-thumbnail-with-mes.md) 
 [Omówienie kodowania Media Services](media-services-encode-asset.md)