---
title: Automatyczne generowanie drabiny szybkości transmisji bitów na platformie Azure za pomocą Media Encoder Standard Microsoft Docs
description: W tym temacie pokazano, jak za pomocą Media Encoder Standard (MES) automatycznie generować drabinę szybkości transmisji bitów na podstawie rozdzielczości wejścia i szybkości transmisji bitów.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: 0309579a674533ebd056c8493b149cc78beef607
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89254077"
---
#  <a name="use-media-encoder-standard-to-auto-generate-a-bitrate-ladder"></a>Użyj Media Encoder Standard, aby automatycznie wygenerować drabinę szybkości transmisji bitów

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]  

## <a name="overview"></a>Omówienie

W tym artykule pokazano, jak za pomocą Media Encoder Standard (MES) automatycznie generować drabinę szybkości transmisji bitów (pary rozdzielczości) na podstawie rozdzielczości wejścia i szybkości transmisji bitów. Automatycznie generowane ustawienie wstępne nigdy nie będzie przekroczyć rozdzielczości wejścia i szybkości transmisji bitów. Na przykład, jeśli dane wejściowe mają wartość 720 z 3 MB/s, dane wyjściowe pozostaną w najlepszy sposób i zaczynają się od stawek mniejszych niż 3 MB/s.

### <a name="encoding-for-streaming-only"></a>Kodowanie tylko do przesyłania strumieniowego

Jeśli zamierzasz zakodować źródłowy film wideo tylko do przesyłania strumieniowego, należy użyć ustawienia wstępnego "adaptacyjnego przesyłania strumieniowego" podczas tworzenia zadania kodowania. W przypadku korzystania z adaptacyjnego ustawienia wstępnego **przesyłania strumieniowego** koder MES rozpocznie inteligentną konfigurację drabiny szybkości transmisji bitów. Nie będzie jednak można kontrolować kosztów kodowania, ponieważ usługa określa liczbę warstw do użycia i rozdzielczość. Możesz zobaczyć przykłady warstw wyjściowych produkowanych przez MES w wyniku kodowania przy użyciu adaptacyjnego ustawienia wstępnego **przesyłania strumieniowego** na końcu tego artykułu. Element zawartości wyjściowej zawiera pliki MP4, w których audio i wideo nie są przeplatane.

### <a name="encoding-for-streaming-and-progressive-download"></a>Kodowanie na potrzeby przesyłania strumieniowego i pobierania progresywnego

Jeśli zamierzasz zakodować źródłowy plik wideo do przesyłania strumieniowego, a także tworzyć pliki MP4 do pobrania progresywnego, należy użyć ustawienia wstępnego "zawartość z wieloma szybkościami transmisji bitów" podczas tworzenia zadania kodowania. W przypadku korzystania z zawartości z możliwością **adaptacyjną z wieloma szybkościami transmisji bitów** , koder MES stosuje tę samą logikę kodowania jak powyżej, ale teraz zasób wyjściowy będzie zawierać pliki MP4, w przypadku których audio i wideo są przeplatane. Można użyć jednego z tych plików MP4 (na przykład o najwyższej szybkości transmisji bitów) jako pliku pobierania progresywnego.

## <a name="encoding-with-media-services-net-sdk"></a><a id="encoding_with_dotnet"></a>Kodowanie przy użyciu zestawu SDK programu Media Services .NET

Poniższy przykład kodu używa Media Services .NET SDK do wykonywania następujących zadań:

- Utwórz zadanie kodowania.
- Pobierz odwołanie do kodera Media Encoder Standard.
- Dodaj zadanie kodowania do zadania i określ, aby użyć adaptacyjnego ustawienia wstępnego **przesyłania strumieniowego** . 
- Utwórz zasób wyjściowy, który zawiera zakodowany element zawartości.
- Dodaj procedurę obsługi zdarzeń, aby sprawdzić postęp zadania.
- Przesyłanie zadania.

#### <a name="create-and-configure-a-visual-studio-project"></a>Tworzenie i konfigurowanie projektu programu Visual Studio

Skonfiguruj środowisko projektowe i wypełnij plik app.config przy użyciu informacji dotyczących połączenia, zgodnie z opisem w sekcji [Projektowanie usługi Media Services na platformie .NET](media-services-dotnet-how-to-use.md). 

#### <a name="example"></a>Przykład

```
using System;
using System.Configuration;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;

namespace AdaptiveStreamingMESPresest
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

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
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

## <a name="output"></a><a id="output"></a>Dane wyjściowe

W tej sekcji przedstawiono trzy przykłady warstw wyjściowych produkowanych przez MES w wyniku kodowania przy użyciu **adaptacyjnego ustawienia wstępnego przesyłania strumieniowego** . 

### <a name="example-1"></a>Przykład 1
Źródło o wysokości "1080" i szybkości klatek "29,970" tworzą 6 warstw wideo:

|Warstwa|Height|Width|Szybkość transmisji bitów (KB/s)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Przykład 2
Źródło o wysokości "720" i szybkości klatek "23,970" produkuje 5 warstw wideo:

|Warstwa|Height|Width|Szybkość transmisji bitów (KB/s)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Przykład 3
Źródło o wysokości "360" i szybkości klatek "29,970" tworzą 3 warstwy wideo:

|Warstwa|Height|Width|Szybkość transmisji bitów (KB/s)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|
## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zobacz też
[Omówienie kodowania Media Services](media-services-encode-asset.md)

