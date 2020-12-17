---
title: Generowanie miniatur przy użyciu usługi Media Encoder Standard za pomocą platformy .NET
description: W tym temacie pokazano, jak używać platformy .NET do kodowania zasobów i generowania miniatur w tym samym czasie przy użyciu Media Encoder Standard.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: b8dab73a-1d91-4b6d-9741-a92ad39fc3f7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: c4b17fb5547c1522ec81369f2e362868a3f216a1
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97652997"
---
# <a name="how-to-generate-thumbnails-using-media-encoder-standard-with-net"></a>Generowanie miniatur przy użyciu usługi Media Encoder Standard za pomocą platformy .NET

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Za pomocą Media Encoder Standard można wygenerować jeden lub więcej miniatur z wejściowego filmu wideo w formatach plików obrazów [JPEG](https://en.wikipedia.org/wiki/JPEG), [PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics)lub [BMP](https://en.wikipedia.org/wiki/BMP_file_format) . Można przesłać zadania, które tworzą tylko obrazy, lub można połączyć Generowanie miniatur z kodowaniem. W tym artykule przedstawiono kilka przykładowych ustawień wstępnych miniatur XML i JSON dla takich scenariuszy. Na końcu tego artykułu znajduje się [przykładowy kod](#code_sample) , który pokazuje, jak używać zestawu SDK programu Media Services .NET do wykonywania zadań kodowania.

Aby uzyskać więcej informacji o elementach, które są używane w przykładowych ustawieniach wstępnych, należy zapoznać się ze [schematem Media Encoder Standard](media-services-mes-schema.md).

Zapoznaj się z sekcją [zagadnienia](media-services-dotnet-generate-thumbnail-with-mes.md#considerations) .
    
## <a name="example-of-a-single-png-file-preset"></a>Przykład ustawienia wstępnego "pojedynczego pliku PNG"

Następujące ustawienia wstępne JSON i XML mogą służyć do tworzenia pojedynczego wyjściowego pliku PNG z pierwszych kilku sekund wejściowego filmu wideo, gdzie koder zapewnia najlepszą próbę znalezienia "interesującej" ramki. Należy pamiętać, że wymiary obrazu wyjściowego zostały ustawione na 100%, co oznacza, że są one zgodne z wymiarami wejściowego wideo. Należy również pamiętać, że ustawienie "format" w elemencie "Outputs" jest wymagane do dopasowania do użycia "PngLayers" w sekcji "dekodery". 

### <a name="json-preset"></a>Ustawienia wstępne JSON

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "PngLayers": [
            {
              "Type": "PngLayer",
              "Width": "100%",
              "Height": "100%"
            }
          ],
          "Start": "{Best}",
          "Type": "PngImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "PngFormat"
          }
        }
      ]
    }
```
    
### <a name="xml-preset"></a>Ustawienia wstępne XML

```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <PngImage Start="{Best}">
          <PngLayers>
            <PngLayer>
              <Width>100%</Width>
              <Height>100%</Height>
            </PngLayer>
          </PngLayers>
        </PngImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <PngFormat />
        </Output>
      </Outputs>
    </Preset>
```

## <a name="example-of-a-series-of-jpeg-images-preset"></a>Przykład ustawienia wstępnego "Seria obrazów JPEG"

Następujące ustawienia wstępne JSON i XML mogą służyć do tworzenia zestawu 10 obrazów w sygnaturach czasowych wynoszących 5%, 15%,..., 95% wejściowej osi czasu, gdzie rozmiar obrazu jest określony jako jedna czwarta wejściowego wideo.

### <a name="json-preset"></a>Ustawienia wstępne JSON

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": "25%",
              "Height": "25%"
            }
          ],
          "Start": "5%",
          "Step": "10%",
          "Range": "96%",
          "Type": "JpgImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        }
      ]
    }
```

### <a name="xml-preset"></a>Ustawienia wstępne XML
    
```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <JpgImage Start="5%" Step="10%" Range="96%">
          <JpgLayers>
            <JpgLayer>
              <Width>25%</Width>
              <Height>25%</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>
```

## <a name="example-of-a-one-image-at-a-specific-timestamp-preset"></a>Przykład ustawienia "jeden obraz w określonym znaczniku czasu"

Następujące ustawienia wstępne JSON i XML mogą służyć do tworzenia pojedynczego obrazu JPEG przy 30-sekundowym znaku wideo wejściowego. To ustawienie wstępne oczekuje, że wejściowy film wideo będzie większy niż 30 sekund (w przeciwnym razie zadanie zakończy się niepowodzeniem).

### <a name="json-preset"></a>Ustawienia wstępne JSON

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": "25%",
              "Height": "25%"
            }
          ],
          "Start": "00:00:30",
          "Step": "1",
          "Range": "1",
          "Type": "JpgImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        }
      ]
    }
```

### <a name="xml-preset"></a>Ustawienia wstępne XML
```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <JpgImage Start="00:00:30" Step="00:00:01" Range="00:00:01">
          <JpgLayers>
            <JpgLayer>
              <Width>25%</Width>
              <Height>25%</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>
```

## <a name="example-of-a-thumbnails-at-different-resolutions-preset"></a>Przykład ustawienia "miniatury w różnych rozdzielczościach"

Poniższego ustawienia wstępnego można użyć do wygenerowania miniatur w różnych rozdzielczościach w jednym zadaniu. W przykładzie w pozycjach 5%, 15%,..., 95% wejściowej osi czasu koder generuje dwa obrazy — jeden o 100% wejściowej rozdzielczości wideo, a drugi przy 50%.

Zwróć uwagę na użycie makra {Solution} w nazwie pliku; wskazuje, że koder ma używać szerokości i wysokości określonej w sekcji kodowanie ustawienia wstępnego podczas generowania nazwy pliku obrazów wyjściowych. Pozwala to również łatwo rozróżnić różne obrazy

### <a name="json-preset"></a>Ustawienia wstępne JSON

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "JpgLayers": [
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "100%",
          "Height": "100%"
        },
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "50%",
          "Height": "50%"
        }

          ],
          "Start": "5%",
          "Step": "10%",
          "Range": "96%",
          "Type": "JpgImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Resolution}_{Index}{Extension}",
          "Format": {
        "Type": "JpgFormat"
          }
        }
      ]
    }
```

### <a name="xml-preset"></a>Ustawienia wstępne XML
```xml
    <?xml version="1.0" encoding="utf-8"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
    <Encoding>
    <JpgImage Start="5%" Step="10%" Range="96%"><JpgImage Start="00:00:01" Step="00:00:15">
      <JpgLayers>
       <JpgLayer>
        <Width>100%</Width>
        <Height>100%</Height>
        <Quality>90</Quality>
       </JpgLayer>
       <JpgLayer>
        <Width>50%</Width>
        <Height>50%</Height>
        <Quality>90</Quality>
       </JpgLayer>
      </JpgLayers>
    </JpgImage>
    </Encoding>
    <Outputs>
      <Output FileName="{Basename}_{Resolution}_{Index}{Extension}">
        <JpgFormat/>
      </Output>
    </Outputs>
    </Preset>
```

## <a name="example-of-generating-a-thumbnail-while-encoding"></a>Przykład generowania miniatury podczas kodowania

Chociaż wszystkie powyższe przykłady zostały omówione w tym, jak można przesłać zadanie kodowania, które tworzy tylko obrazy, można także połączyć kodowanie wideo/audio z generowaniem miniatur. Poniższe ustawienia wstępne JSON i XML poinformują **Media Encoder Standard** o wygenerowaniu miniatury podczas kodowania.

### <a name="json-preset"></a><a id="json"></a>Ustawienia wstępne JSON
Aby uzyskać informacje o schemacie, zobacz [ten](./media-services-mes-schema.md) artykuł.

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": "true",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 4500,
              "MaxBitrate": 4500,
              "BufferWindow": "00:00:05",
              "Width": 1280,
              "Height": 720,
              "ReferenceFrames": 3,
              "EntropyMode": "Cabac",
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
    
            }
          ],
          "Type": "H264Video"
        },
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": "100%",
              "Height": "100%"
            }
          ],
          "Start": "{Best}",
          "Type": "JpgImage"
        },
        {
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        },
        {
          "FileName": "{Basename}_{Resolution}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }
```

### <a name="xml-preset"></a><a id="xml"></a>Ustawienia wstępne XML
Aby uzyskać informacje o schemacie, zobacz [ten](./media-services-mes-schema.md) artykuł.

```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <SceneChangeDetection>true</SceneChangeDetection>
          <H264Layers>
            <H264Layer>
              <Bitrate>4500</Bitrate>
              <Width>1280</Width>
              <Height>720</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>4500</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <AACAudio>
          <Profile>AACLC</Profile>
          <Channels>2</Channels>
          <SamplingRate>48000</SamplingRate>
          <Bitrate>128</Bitrate>
        </AACAudio>
        <JpgImage Start="{Best}">
          <JpgLayers>
            <JpgLayer>
              <Width>100%</Width>
              <Height>100%/Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Resolution}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>   
```

## <a name="encode-video-and-generate-thumbnail-with-net"></a><a id="code_sample"></a>Koduj wideo i Generuj miniaturę przy użyciu platformy .NET

Poniższy przykład kodu używa Media Services .NET SDK do wykonywania następujących zadań:

* Utwórz zadanie kodowania.
* Pobierz odwołanie do kodera Media Encoder Standard.
* Załaduj wstępnie zdefiniowane dane [XML](media-services-dotnet-generate-thumbnail-with-mes.md#xml) lub [JSON](media-services-dotnet-generate-thumbnail-with-mes.md#json) , które zawierają ustawienia wstępne kodowania, a także informacje konieczne do generowania miniatur. Możesz zapisać ten plik  [XML](media-services-dotnet-generate-thumbnail-with-mes.md#xml) lub [kod JSON](media-services-dotnet-generate-thumbnail-with-mes.md#json) w pliku i użyć następującego kodu do załadowania pliku.

    ```csharp
    // Load the XML (or JSON) from the local file.
    string configuration = File.ReadAllText(fileName);  
    ```

* Dodaj jedno zadanie kodowania do zadania. 
* Określ zasób wejściowy do zakodowania.
* Utwórz zasób wyjściowy, który zawiera zakodowany element zawartości.
* Dodaj procedurę obsługi zdarzeń, aby sprawdzić postęp zadania.
* Przesyłanie zadania.

Zapoznaj się z artykułem [programowanie Media Services przy użyciu platformy .NET](media-services-dotnet-how-to-use.md) , aby uzyskać instrukcje dotyczące sposobu konfigurowania środowiska deweloperskiego.

```csharp
using System;
using System.Configuration;
using System.IO;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;

namespace EncodeAndGenerateThumbnails
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

            // Encode and generate the thumbnails.
            EncodeToAdaptiveBitrateMP4Set(asset);

            Console.ReadLine();
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Thumbnail Job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Load the XML (or JSON) from the local file.
            string configuration = File.ReadAllText("ThumbnailPreset_JSON.json");

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard Thumbnail task",
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

## <a name="considerations"></a>Zagadnienia do rozważenia
Obowiązują następujące zastrzeżenia:

* Użycie jawnych sygnatur czasowych dla elementu Start/Step/Range zakłada, że źródło danych wejściowych jest co najmniej 1 minutę.
* Elementy JPG/PNG/BmpImage mają atrybuty typu Start, krok i zakres — można je interpretować jako:
  
  * Numer ramki, jeśli są nieujemnymi liczbami całkowitymi, na przykład "Start": "120",
  * W odniesieniu do czasu trwania źródła, jeśli jest wyrażona jako%-sufiksy, na przykład "Start": "15%" lub
  * Sygnatura czasowa, jeśli jest wyrażona w formacie gg: MM: SS... Formatowanie. Na przykład "Start": "00:01:00"
    
    Można mieszać i dopasowywać notacje w miarę jak ty.
    
    Ponadto funkcja Start obsługuje również specjalne makro: {Najlepsza}, które próbuje określić pierwszy "interesujące" ramki notatki zawartości: (krok i zakres są ignorowane, gdy początek jest ustawiony na {Najlepsza})
  * Wartości domyślne: początek: {Najlepsza}
* Format danych wyjściowych musi być jawnie podany dla każdego formatu obrazu: JPG/PNG/BmpFormat. Gdy jest obecny, MES pasuje do JpgVideo JpgFormat i tak dalej. OutputFormat wprowadza nowy obraz — specyficzne dla kodera-dekoder makro: {index}, które musi być obecne (raz i tylko raz) dla formatów danych wyjściowych obrazu.

## <a name="next-steps"></a>Następne kroki

[Postęp zadania](media-services-check-job-progress.md) można sprawdzić, gdy zadanie kodowania jest w stanie oczekiwania.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zobacz też
[Omówienie kodowania Media Services](media-services-encode-asset.md)