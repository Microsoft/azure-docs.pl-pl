---
title: Cyfrowe tekstu za pomocą Azure Media Analytics OCR | Microsoft Docs
description: Azure Media Analytics OCR (optyczne rozpoznawanie znaków) umożliwia konwertowanie zawartości tekstowej w plikach wideo na edytowalny, przeszukiwany tekst cyfrowy.  Pozwala to zautomatyzować wyodrębnianie znaczących metadanych z sygnału wideo nośnika.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 307c196e-3a50-4f4b-b982-51585448ffc6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 11889bd6df0bcc9564c17fdaacc333df1d418660
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77918340"
---
# <a name="use-azure-media-analytics-to-convert-text-content-in-video-files-into-digital-text"></a>Użyj Azure Media Analytics do przekonwertowania zawartości tekstowej w plikach wideo na tekst cyfrowy  

> [!NOTE]
> Procesor multimediów **usługi Azure Media OCR** zostanie wycofany. Aby uzyskać datę wycofania, zobacz temat [starsze składniki](legacy-components.md) .

## <a name="overview"></a>Omówienie
Jeśli zachodzi potrzeba wyodrębnienia zawartości tekstowej z plików wideo i wygenerowania edytowalnego, możliwego do przeszukiwania tekstu cyfrowego, należy użyć Azure Media Analytics OCR (optyczne rozpoznawanie znaków). Ten procesor multimediów platformy Azure wykrywa zawartość tekstową w plikach wideo i generuje pliki tekstowe do użycia. OCR umożliwia automatyzację wyodrębniania znaczących metadanych z sygnału wideo nośnika.

W połączeniu z aparatem wyszukiwania można łatwo indeksować multimedia według tekstu i zwiększyć wykrywalność zawartości. Jest to niezwykle przydatne w przypadku bardzo tekstowego wideo, takiego jak nagrywanie wideo lub przechwytywanie ekranu prezentacji pokazu slajdów. Procesor multimediów OCR na platformie Azure jest zoptymalizowany pod kątem tekstu cyfrowego.

Procesor multimediów **usługi Azure Media OCR** jest obecnie w wersji zapoznawczej.

Ten artykuł zawiera szczegółowe informacje na temat **usługi Azure Media OCR** i pokazuje, jak używać jej z zestawem SDK Media Services dla platformy .NET. Aby uzyskać więcej informacji i przykładów, zobacz [ten blog](https://azure.microsoft.com/blog/announcing-video-ocr-public-preview-new-config/).

## <a name="ocr-input-files"></a>Pliki wejściowe OCR
Pliki wideo. Obecnie obsługiwane są następujące formaty: MP4, MOV i WMV.

## <a name="task-configuration"></a>Konfiguracja zadania
Konfiguracja zadania (ustawienie wstępne). Podczas tworzenia zadania za pomocą **usługi Azure Media OCR**należy określić ustawienie wstępne konfiguracji przy użyciu formatu JSON lub XML. 

>[!NOTE]
>Aparat OCR pobiera tylko region obrazu z minimum 40 pikseli do maksymalnie 32000 pikseli jako prawidłowe dane wejściowe w wysokości/szerokości.
>

### <a name="attribute-descriptions"></a>Opisy atrybutów
| Nazwa atrybutu | Opis |
| --- | --- |
|AdvancedOutput| Jeśli ustawisz wartość AdvancedOutput na true, dane wyjściowe JSON będą zawierać dane pozycyjne dla każdego pojedynczego wyrazu (oprócz fraz i regionów). Jeśli nie chcesz zobaczyć tych szczegółów, Ustaw flagę na false. Wartość domyślna to false. Aby uzyskać więcej informacji, zobacz [ten blog](https://azure.microsoft.com/blog/azure-media-ocr-simplified-output/).|
| Język |(opcjonalnie) zawiera opis języka tekstu, który ma być wyszukiwany. Jedno z następujących: automatyczne wykrywanie (domyślnie), arabski, ChineseSimplified, ChineseTraditional, Czeski Duński, holenderski, angielski, francuski, niemiecki, grecki, węgierski, włoski, japoński, koreański, norweski, polski, portugalski, szwedzki, rosyjski, turecki, SerbianCyrillic, SerbianLatin, słowacki, hiszpański |
| TextOrientation |(opcjonalnie) określa orientację tekstu, który ma być wyszukiwany.  "Left" oznacza, że górna część wszystkich liter jest wskazywana po lewej stronie.  Domyślny tekst (na przykład, który można znaleźć w książce) może być nazywany "w górę".  Jeden z następujących: Autowykrywanie (domyślnie), w górę, w prawo, w dół, w lewo. |
| TimeInterval |(opcjonalnie) opisuje częstotliwość próbkowania.  Wartość domyślna to co 1/2 sekund.<br/>Format JSON — HH: mm: SS. SSS (domyślnie 00:00:00.500)<br/>Format XML — pierwotny czas trwania XSD (W3C) |
| DetectRegions |obowiązkowe Tablica obiektów DetectRegion określający regiony w ramce wideo, w której ma być wykrywany tekst.<br/>Obiekt DetectRegion składa się z następujących czterech wartości całkowitych:<br/>Od lewej do pikseli od lewego marginesu<br/>Góra — piksele od górnego marginesu<br/>Width — szerokość regionu w pikselach.<br/>Height — wysokość regionu w pikselach |

#### <a name="json-preset-example"></a>Przykład ustawień wstępnych JSON

```json
    {
        "Version":1.0, 
        "Options": 
        {
            "AdvancedOutput":"true",
            "Language":"English", 
            "TimeInterval":"00:00:01.5",
            "TextOrientation":"Up",
            "DetectRegions": [
                    {
                       "Left": 10,
                       "Top": 10,
                       "Width": 100,
                       "Height": 50
                    }
             ]
        }
    }
```

#### <a name="xml-preset-example"></a>Przykład ustawienia wstępnego XML

```xml
    <?xml version=""1.0"" encoding=""utf-16""?>
    <VideoOcrPreset xmlns:xsi=""https://www.w3.org/2001/XMLSchema-instance"" xmlns:xsd=""https://www.w3.org/2001/XMLSchema"" Version=""1.0"" xmlns=""https://www.windowsazure.com/media/encoding/Preset/2014/03"">
      <Options>
         <AdvancedOutput>true</AdvancedOutput>
         <Language>English</Language>
         <TimeInterval>PT1.5S</TimeInterval>
         <DetectRegions>
             <DetectRegion>
                   <Left>10</Left>
                   <Top>10</Top>
                   <Width>100</Width>
                   <Height>50</Height>
            </DetectRegion>
       </DetectRegions>
       <TextOrientation>Up</TextOrientation>
      </Options>
    </VideoOcrPreset>
```

## <a name="ocr-output-files"></a>Pliki wyjściowe OCR
Wyjście procesora multimediów OCR jest plikiem JSON.

### <a name="elements-of-the-output-json-file"></a>Elementy wyjściowego pliku JSON
Dane wyjściowe OCR do wideo dostarczają segmentów czasowych dla znaków znalezionych w filmie wideo.  Możesz użyć atrybutów, takich jak Language lub Orientacja, do stworzyć właściwy na dokładnie wyrazach, które interesują się analizą. 

Dane wyjściowe zawierają następujące atrybuty:

| Element | Opis |
| --- | --- |
| Skala czasu |"Takty" na sekundę filmu wideo |
| Przesunięcie |przesunięcie czasu dla sygnatur czasowych. W wersji 1,0 interfejsów API wideo zawsze będzie równa 0. |
| Framerate |Klatki na sekundę filmu wideo |
| szerokość |Szerokość filmu wideo w pikselach |
| height |wysokość filmu wideo w pikselach |
| Fragments |Tablica fragmentów wideo opartych na czasie, w których fragmenty metadanych są podzielone |
| rozpoczynanie |Godzina rozpoczęcia fragmentu w "taktach" |
| czas trwania |Długość fragmentu w "taktach" |
| interval |interwał każdego zdarzenia w danym fragmencie |
| zdarzenia |Tablica zawierająca regiony |
| region |Obiekt reprezentujący wykryte wyrazy lub frazy |
| language |język tekstu wykryty w regionie |
| zorientowany |Orientacja tekstu wykryta w regionie |
| linie |Tablica wierszy tekstu wykrytych w regionie |
| tekst |rzeczywisty tekst |

### <a name="json-output-example"></a>Przykład danych wyjściowych JSON
Poniższy przykład danych wyjściowych zawiera ogólne informacje wideo i kilka fragmentów wideo. W każdym fragmencie wideo zawiera każdy region, który jest wykrywany przez program OCR MP z językiem i orientacją tekstu. Region zawiera również każdy wiersz słowa w tym regionie z tekstem wiersza, pozycją wiersza i wszystkimi informacjami o wyrazie (zawartością w programie Word, pozycją i pewnością) w tym wierszu. Poniżej znajduje się przykład i umieszczamy w niej pewne Komentarze.

```json
    {
        "version": 1, 
        "timescale": 90000, 
        "offset": 0, 
        "framerate": 30, 
        "width": 640, 
        "height": 480,  // general video information
        "fragments": [
            {
                "start": 0, 
                "duration": 180000, 
                "interval": 90000,  // the time information about this fragment
                "events": [
                    [
                       { 
                            "region": { // the detected region array in this fragment 
                                "language": "English",  // region language
                                "orientation": "Up",  // text orientation
                                "lines": [  // line information array in this region, including the text and the position
                                    {
                                        "text": "One Two", 
                                        "left": 10, 
                                        "top": 10, 
                                        "right": 210, 
                                        "bottom": 110, 
                                        "word": [  // word information array in this line
                                            {
                                                "text": "One", 
                                                "left": 10, 
                                                "top": 10, 
                                                "right": 110, 
                                                "bottom": 110, 
                                                "confidence": 900
                                            }, 
                                            {
                                                "text": "Two", 
                                                "left": 110, 
                                                "top": 10, 
                                                "right": 210, 
                                                "bottom": 110, 
                                                "confidence": 910
                                            }
                                        ]
                                    }
                                ]
                            }
                        }
                    ]
                ]
            }
        ]
    }
```

## <a name="net-sample-code"></a>Przykładowy kod platformy .NET

Poniższy program pokazuje, jak:

1. Utwórz element zawartości i Przekaż plik multimedialny do elementu zawartości.
2. Utwórz zadanie z konfiguracją OCR/wstępnie ustawionym plikiem.
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

namespace OCR
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

            // Run the OCR job.
            var asset = RunOCRJob(@"C:\supportFiles\OCR\presentation.mp4",
                                        @"C:\supportFiles\OCR\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\OCR\Output");
        }

        static IAsset RunOCRJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My OCR Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My OCR Job");

            // Get a reference to Azure Media OCR.
            string MediaProcessorName = "Azure Media OCR";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My OCR Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My OCR Output Asset", AssetCreationOptions.None);

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

