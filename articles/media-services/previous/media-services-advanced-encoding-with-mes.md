---
title: Wykonaj zaawansowane kodowanie, dostosowując ustawienia wstępne ustawień RYNKOWych | Microsoft Docs
description: W tym temacie pokazano, jak wykonać zaawansowane kodowanie przez dostosowanie Media Encoder Standard ustawień wstępnych zadań.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 2a4ade25-e600-4bce-a66e-e29cf4a38369
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 345fd56e5ff069ea222661a4820e75d96a9e2395
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103014506"
---
# <a name="perform-advanced-encoding-by-customizing-mes-presets"></a>Wykonaj zaawansowane kodowanie, dostosowując ustawienia wstępne ustawień RYNKOWych

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Omówienie

W tym temacie przedstawiono sposób dostosowywania ustawień wstępnych Media Encoder Standard. W temacie [kodowanie z Media Encoder standard przy użyciu niestandardowych ustawień predefiniowanych](media-services-custom-mes-presets-with-dotnet.md) pokazano, jak używać programu .NET do tworzenia zadania kodowania i zadania, które wykonuje to zadanie. Po dostosowaniu ustawienia wstępnego Podaj niestandardowe ustawienia wstępne do zadania kodowania. 

W przypadku użycia ustawienia wstępnego XML Pamiętaj, aby zachować kolejność elementów, jak pokazano w poniższych przykładach XML (np. KeyFrameInterval powinna poprzedzać SceneChangeDetection).

> [!NOTE] 
> Wiele funkcji zaawansowanych Media Services V2 Media Encoder Standard nie są obecnie dostępne w wersji 3. Aby uzyskać więcej informacji, zobacz [Przewodnik migracji](../latest/migrate-v-2-v-3-migration-introduction.md).

## <a name="support-for-relative-sizes"></a>Obsługa rozmiarów względnych

Podczas generowania miniatur nie jest konieczne, aby zawsze określać szerokość i wysokość danych wyjściowych (w pikselach). Można określić wartości procentowe w zakresie [1%,..., 100%].

### <a name="json-preset"></a>Ustawienia wstępne JSON

```json
"Width": "100%",
"Height": "100%"
```

### <a name="xml-preset"></a>Ustawienia wstępne XML

```xml
<Width>100%</Width>
<Height>100%</Height>
```

## <a name="generate-thumbnails"></a>Generowanie miniatur

W tej sekcji pokazano, jak dostosować ustawienie wstępne, które generuje miniatury. Zdefiniowane poniżej ustawienie wstępne zawiera informacje na temat sposobu kodowania pliku oraz informacje potrzebne do generowania miniatur. W [tej](media-services-mes-presets-overview.md) sekcji można zastosować dowolne ustawienia wstępne, a następnie dodać kod generujący miniatury.  

> [!NOTE]
> Ustawienie **SceneChangeDetection** w następujących ustawieniach wstępnych można ustawić na wartość true tylko wtedy, gdy kodowanie odbywa się przy użyciu pojedynczej szybkości transmisji bitów. W przypadku kodowania do wideo o wysokiej szybkości transmisji bitów i ustawieniu **SceneChangeDetection** na true koder zwraca błąd.  
>
>

Informacje o schemacie znajdują się w [tym](media-services-mes-schema.md) temacie.

Zapoznaj się z sekcją [zagadnienia](#considerations) .

### <a name="json-preset"></a>Ustawienia wstępne JSON

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
          "Width": 640,
          "Height": 360
        }
      ],
      "Start": "{Best}",
      "Type": "JpgImage"
    },
    {
      "PngLayers": [
        {
          "Type": "PngLayer",
          "Width": 640,
          "Height": 360,
        }
      ],
      "Start": "00:00:01",
      "Step": "00:00:10",
      "Range": "00:00:58",
      "Type": "PngImage"
    },
    {
      "BmpLayers": [
        {
          "Type": "BmpLayer",
          "Width": 640,
          "Height": 360
        }
      ],
      "Start": "10%",
      "Step": "10%",
      "Range": "90%",
      "Type": "BmpImage"
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
      "FileName": "{Basename}_{Index}{Extension}",
      "Format": {
        "Type": "PngFormat"
      }
    },
    {
      "FileName": "{Basename}_{Index}{Extension}",
      "Format": {
        "Type": "BmpFormat"
      }
    },
    {
      "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
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
          <Width>640</Width>
          <Height>360</Height>
          <Quality>90</Quality>
        </JpgLayer>
      </JpgLayers>
    </JpgImage>
    <BmpImage Start="10%" Step="10%" Range="90%">
      <BmpLayers>
        <BmpLayer>
          <Width>640</Width>
          <Height>360</Height>
        </BmpLayer>
      </BmpLayers>
    </BmpImage>
    <PngImage Start="00:00:01" Step="00:00:10" Range="00:00:58">
      <PngLayers>
        <PngLayer>
          <Width>640</Width>
          <Height>360</Height>
        </PngLayer>
      </PngLayers>
    </PngImage>
  </Encoding>
  <Outputs>
    <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
      <MP4Format />
    </Output>
    <Output FileName="{Basename}_{Index}{Extension}">
      <JpgFormat />
    </Output>
    <Output FileName="{Basename}_{Index}{Extension}">
      <BmpFormat />
    </Output>
    <Output FileName="{Basename}_{Index}{Extension}">
      <PngFormat />
    </Output>
  </Outputs>
</Preset>
```

### <a name="considerations"></a>Zagadnienia do rozważenia

Obowiązują następujące zastrzeżenia:

* Użycie jawnych sygnatur czasowych dla elementu Start/Step/Range zakłada, że źródło danych wejściowych jest co najmniej 1 minutę.
* Elementy JPG/PNG/BmpImage mają atrybuty typu Start, krok i zakres — można je interpretować jako:

  * Numer ramki, jeśli są nieujemnymi liczbami całkowitymi, na przykład "Start": "120",
  * W odniesieniu do czasu trwania źródła, jeśli jest wyrażona jako%-sufiksy, na przykład "Start": "15%" lub
  * Sygnatura czasowa, jeśli jest wyrażona w formacie gg: MM: SS... Format, na przykład "Start": "00:01:00"

    Można mieszać i dopasowywać notacje w miarę jak ty.

    Ponadto funkcja Start obsługuje również specjalne makro: {Najlepsza}, które próbuje określić pierwszy "interesujące" ramki notatki zawartości: (krok i zakres są ignorowane, gdy początek jest ustawiony na {Najlepsza})
  * Wartości domyślne: początek: {Najlepsza}
* Format danych wyjściowych musi być jawnie podany dla każdego formatu obrazu: JPG/PNG/BmpFormat. Gdy jest obecny, MES pasuje do JpgVideo JpgFormat i tak dalej. OutputFormat wprowadza nowy obraz — specyficzne dla kodera-dekoder makro: {index}, które musi być obecne (raz i tylko raz) dla formatów danych wyjściowych obrazu.

## <a name="trim-a-video-clipping"></a><a id="trim_video"></a>Przytnij wideo (przycinanie)
Ta sekcja zawiera informacje o modyfikowaniu ustawień wstępnych kodera w celu wycinania lub przycięcia wejściowego filmu wideo, w którym dane wejściowe to plik w postaci Mezzanine lub plik na żądanie. Koder może również służyć do wycinania lub przycinania elementu zawartości, który jest przechwytywany lub archiwizowany ze strumienia na żywo — szczegółowe informacje dla tej usługi są dostępne w [tym blogu](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).

Aby przyciąć wideo, możesz skorzystać z poniższych ustawień wstępnych, które zostały opisane w [tej](media-services-mes-presets-overview.md) sekcji, i zmodyfikować element **sources** (jak pokazano poniżej). Wartość StartTime musi odpowiadać bezwzględnym znacznikom czasu wejściowego wideo. Na przykład, jeśli pierwsza klatka wejściowego filmu wideo ma sygnaturę czasową 12:00:10.000, wartość StartTime powinna wynosić co najmniej 12:00:10.000 i więcej. W poniższym przykładzie przyjęto założenie, że wejściowy film wideo ma początkową sygnaturę czasową równą zero. **Źródła** powinny być umieszczane na początku ustawień wstępnych.

### <a name="json-preset"></a><a id="json"></a>Ustawienia wstępne JSON

```json
{
  "Version": 1.0,
  "Sources": [
    {
      "StartTime": "00:00:04",
      "Duration": "00:00:16"
    }
  ],
  "Codecs": [
    {
      "KeyFrameInterval": "00:00:02",
      "StretchMode": "AutoSize",
      "H264Layers": [
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 3400,
          "MaxBitrate": 3400,
          "BufferWindow": "00:00:05",
          "Width": 1280,
          "Height": 720,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        },
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 2250,
          "MaxBitrate": 2250,
          "BufferWindow": "00:00:05",
          "Width": 960,
          "Height": 540,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        },
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 1500,
          "MaxBitrate": 1500,
          "BufferWindow": "00:00:05",
          "Width": 960,
          "Height": 540,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        },
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

### <a name="xml-preset"></a>Ustawienia wstępne XML
Aby przyciąć wideo, możesz wykonać dowolne opisane [tutaj](media-services-mes-presets-overview.md) ustawienia wstępne i zmodyfikować element **sources** (jak pokazano poniżej).

```xml
<?xml version="1.0" encoding="utf-16"?>
<Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
  <Sources>
    <Source StartTime="PT4S" Duration="PT14S"/>
  </Sources>
  <Encoding>
    <H264Video>
      <KeyFrameInterval>00:00:02</KeyFrameInterval>
      <H264Layers>
        <H264Layer>
          <Bitrate>3400</Bitrate>
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
          <MaxBitrate>3400</MaxBitrate>
        </H264Layer>
        <H264Layer>
          <Bitrate>2250</Bitrate>
          <Width>960</Width>
          <Height>540</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>2250</MaxBitrate>
        </H264Layer>
        <H264Layer>
          <Bitrate>1500</Bitrate>
          <Width>960</Width>
          <Height>540</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>1500</MaxBitrate>
        </H264Layer>
        <H264Layer>
          <Bitrate>1000</Bitrate>
          <Width>640</Width>
          <Height>360</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>1000</MaxBitrate>
        </H264Layer>
        <H264Layer>
          <Bitrate>650</Bitrate>
          <Width>640</Width>
          <Height>360</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>650</MaxBitrate>
        </H264Layer>
        <H264Layer>
          <Bitrate>400</Bitrate>
          <Width>320</Width>
          <Height>180</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>400</MaxBitrate>
        </H264Layer>
      </H264Layers>
    </H264Video>
    <AACAudio>
      <Profile>AACLC</Profile>
      <Channels>2</Channels>
      <SamplingRate>48000</SamplingRate>
      <Bitrate>128</Bitrate>
    </AACAudio>
  </Encoding>
  <Outputs>
    <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
      <MP4Format />
    </Output>
  </Outputs>
</Preset>
```

## <a name="create-an-overlay"></a><a id="overlay"></a>Tworzenie nakładki

Media Encoder Standard umożliwia nałożenie obrazu na istniejący film wideo. Obecnie obsługiwane są następujące formaty: PNG, jpg, GIF i BMP. Zdefiniowane poniżej ustawienie wstępne jest podstawowym przykładem nakładki wideo.

Oprócz definiowania wstępnie zdefiniowanego pliku trzeba również pozwolić Media Services wiedzieć, który plik w elemencie zawartości jest obrazem nakładki, a który plik jest źródłowym wideo, na którym chcesz nałożyć obraz. Plik wideo musi być plikiem **podstawowym** .

Jeśli używasz platformy .NET, Dodaj następujące dwie funkcje do przykładu .NET zdefiniowanego w [tym](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) temacie. Funkcja **UploadMediaFilesFromFolder** przekazuje pliki z folderu (na przykład BigBuckBunny.mp4 i Image001.png) i ustawia plik MP4 jako plik podstawowy w elemencie zawartości. Funkcja **EncodeWithOverlay** używa niestandardowego pliku ustawień wstępnych, który został do niego przekazano (na przykład poniższego ustawienia wstępnego), aby utworzyć zadanie kodowania.

```csharp
static public IAsset UploadMediaFilesFromFolder(string folderPath)
{
    IAsset asset = _context.Assets.CreateFromFolder(folderPath, AssetCreationOptions.None);

    foreach (var af in asset.AssetFiles)
    {
        // The following code assumes 
        // you have an input folder with one MP4 and one overlay image file.
        if (af.Name.Contains(".mp4"))
            af.IsPrimary = true;
        else
            af.IsPrimary = false;

        af.Update();
    }

    return asset;
}

static public IAsset EncodeWithOverlay(IAsset assetSource, string customPresetFileName)
{
    // Declare a new job.
    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
    // Get a media processor reference, and pass to it the name of the 
    // processor to use for the specific task.
    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

    // Load the XML (or JSON) from the local file.
    string configuration = File.ReadAllText(customPresetFileName);

    // Create a task
    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
        processor,
        configuration,
        TaskOptions.None);

    // Specify the input assets to be encoded.
    // This asset contains a source file and an overlay file.
    task.InputAssets.Add(assetSource);

    // Add an output asset to contain the results of the job. 
    task.OutputAssets.AddNew("Output asset",
        AssetCreationOptions.None);

    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
    job.Submit();
    job.GetExecutionProgressTask(CancellationToken.None).Wait();

    return job.OutputMediaAssets[0];
}
```

> [!NOTE]
> Bieżące ograniczenia:
>
> Ustawienie nieprzezroczystości nakładki nie jest obsługiwane.
>
> Źródłowy plik wideo i plik obrazu nakładki muszą znajdować się w tym samym elemencie zawartości, a plik wideo musi być ustawiony jako plik podstawowy tego elementu zawartości.
>
>

### <a name="json-preset"></a>Ustawienia wstępne JSON

```json
{
  "Version": 1.0,
  "Sources": [
    {
      "Streams": [],
      "Filters": {
        "VideoOverlay": {
          "Position": {
            "X": 100,
            "Y": 100,
            "Width": 100,
            "Height": 50
          },
          "AudioGainLevel": 0.0,
          "MediaParams": [
            {
              "OverlayLoopCount": 1
            },
            {
              "IsOverlay": true,
              "OverlayLoopCount": 1
            }
          ],
          "Source": "Image001.png",
          "Clip": {
            "Duration": "00:00:05"
          },
          "FadeInDuration": {
            "Duration": "00:00:01"
          },
          "FadeOutDuration": {
            "StartTime": "00:00:03",
            "Duration": "00:00:04"
          }
        }
      },
      "Pad": true
    }
  ],
  "Codecs": [
    {
      "KeyFrameInterval": "00:00:02",
      "H264Layers": [
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 1045,
          "MaxBitrate": 1045,
          "BufferWindow": "00:00:05",
          "ReferenceFrames": 3,
          "EntropyMode": "Cavlc",
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "Width": "640",
          "Height": "360",
          "FrameRate": "0/1"
        }
      ],
      "Type": "H264Video"
    },
    {
      "Type": "CopyAudio"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}{Extension}",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

### <a name="xml-preset"></a>Ustawienia wstępne XML

```xml
<?xml version="1.0" encoding="utf-16"?>
<Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
  <Sources>
    <Source>
      <Streams />
      <Filters>
        <VideoOverlay>
          <Source>Image001.png</Source>
          <Clip Duration="PT5S" />
          <FadeInDuration Duration="PT1S" />
          <FadeOutDuration StartTime="PT3S" Duration="PT4S" />
          <Position X="100" Y="100" Width="100" Height="50" />
          <Opacity>0</Opacity>
          <AudioGainLevel>0</AudioGainLevel>
          <MediaParams>
            <MediaParam>
              <IsOverlay>false</IsOverlay>
              <OverlayLoopCount>1</OverlayLoopCount>
            </MediaParam>
            <MediaParam>
              <IsOverlay>true</IsOverlay>
              <OverlayLoopCount>1</OverlayLoopCount>
            </MediaParam>
          </MediaParams>
        </VideoOverlay>
      </Filters>
      <Pad>true</Pad>
    </Source>
  </Sources>
  <Encoding>
    <H264Video>
      <KeyFrameInterval>00:00:02</KeyFrameInterval>
      <H264Layers>
        <H264Layer>
          <Bitrate>1045</Bitrate>
          <Width>640</Width>
          <Height>360</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>0</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cavlc</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>1045</MaxBitrate>
        </H264Layer>
      </H264Layers>
    </H264Video>
    <CopyAudio />
  </Encoding>
  <Outputs>
    <Output FileName="{Basename}{Extension}">
      <MP4Format />
    </Output>
  </Outputs>
</Preset>
```

## <a name="insert-a-silent-audio-track-when-input-has-no-audio"></a><a id="silent_audio"></a>Wstaw cichą ścieżkę audio, gdy dane wejściowe nie zawierają dźwięku
Domyślnie, Jeśli wysyłasz dane wejściowe do kodera, który zawiera tylko wideo, a nie dźwięk, wówczas wyjściowy element zawartości zawiera pliki, które zawierają tylko dane wideo. Niektóre odtwarzacze mogą nie być w stanie obsłużyć takich strumieni wyjściowych. Możesz użyć tego ustawienia, aby wymusić, aby koder mógł dodać cichą ścieżkę audio do danych wyjściowych w tym scenariuszu.

Aby wymusić generowanie przez koder elementu zawartości zawierającej cichą ścieżkę audio, gdy dane wejściowe nie zawierają dźwięku, określ wartość "InsertSilenceIfNoAudio".

W tej sekcji można zastosować dowolne ustawienia wstępne, które opisano w [tym](media-services-mes-presets-overview.md) temacie, i wprowadzić następujące zmiany:

### <a name="json-preset"></a>Ustawienia wstępne JSON

```json
{
  "Channels": 2,
  "SamplingRate": 44100,
  "Bitrate": 96,
  "Type": "AACAudio",
  "Condition": "InsertSilenceIfNoAudio"
}
```

### <a name="xml-preset"></a>Ustawienia wstępne XML

```xml
<AACAudio Condition="InsertSilenceIfNoAudio">
  <Channels>2</Channels>
  <SamplingRate>44100</SamplingRate>
  <Bitrate>96</Bitrate>
</AACAudio>
```

## <a name="disable-auto-de-interlacing"></a><a id="deinterlacing"></a>Wyłącz funkcję autoprzeplotu
Klienci nie muszą wykonywać żadnych czynności, jeśli takie jak zawartość przeplotu zostaną automatycznie cofnięte. Gdy funkcja automatycznego usuwania przeplotu jest włączona (domyślnie), oznacza to, że jest to funkcja automatycznego wykrywania ramek z przeplotem i tylko odłożenia ramek oznaczonych jako przeplatane.

Można wyłączyć funkcję autoprzeplotu. Ta opcja nie jest zalecana.

### <a name="json-preset"></a>Ustawienia wstępne JSON

```json
"Sources": [
  {
    "Filters": {
      "Deinterlace": {
        "Mode": "Off"
      }
    },
  }
]
```

### <a name="xml-preset"></a>Ustawienia wstępne XML

```xml
<Sources>
  <Source>
    <Filters>
      <Deinterlace>
        <Mode>Off</Mode>
      </Deinterlace>
    </Filters>
  </Source>
</Sources>
```

## <a name="audio-only-presets"></a><a id="audio_only"></a>Ustawienia wstępne tylko audio
W tej sekcji przedstawiono dwa ustawienia wstępne dotyczące tylko dźwięku: AAC audio i AAC dobrej jakości audio.

### <a name="aac-audio"></a>AAC audio

```json
{
  "Version": 1.0,
  "Codecs": [
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
      "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

### <a name="aac-good-quality-audio"></a>AAC dobrej jakości audio

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "Profile": "AACLC",
      "Channels": 2,
      "SamplingRate": 48000,
      "Bitrate": 192,
      "Type": "AACAudio"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

## <a name="concatenate-two-or-more-video-files"></a><a id="concatenate"></a>Łączenie dwóch lub więcej plików wideo

Poniższy przykład ilustruje, jak można wygenerować ustawienia wstępne, aby połączyć dwa lub więcej plików wideo. Typowy scenariusz polega na tym, że chcesz dodać nagłówek lub przyczepę do głównego wideo. Zamierzone użycie polega na tym, że pliki wideo są edytowane wspólnie, udostępniają właściwości (rozdzielczość wideo, szybkość klatek, liczba ścieżek audio itp.). Należy zadbać o to, aby nie mieszać filmów wideo o różnych szybkościach klatek lub o różnej liczbie ścieżek audio.

>[!NOTE]
>Bieżący projekt funkcji łączenia oczekuje, że wejściowe klipy wideo są spójne pod kątem rozdzielczości, szybkości klatek itd. 

### <a name="requirements-and-considerations"></a>Wymagania i zagadnienia

* Wejściowe wideo powinny mieć tylko jedną ścieżkę audio.
* Wszystkie wejściowe wideo powinny mieć tę samą szybkość klatek.
* Musisz przekazać wideo do oddzielnych zasobów i ustawić wideo jako plik podstawowy w każdym z zasobów.
* Musisz znać czas trwania filmów wideo.
* W poniższych przykładach założono, że wszystkie wejściowe wideo zaczynają się od sygnatury czasowej równej zero. Należy zmodyfikować wartości StartTime, jeśli filmy wideo mają różne początkowe sygnatury czasowe, jak zwykle jest to przypadek z archiwami na żywo.
* Ustawienie wstępne JSON powoduje tworzenie jawnych odwołań do wartości AssetID zasobów wejściowych.
* Przykładowy kod założono, że ustawienie wstępne JSON zostało zapisane w pliku lokalnym, na przykład "C:\supportFiles\preset.json". Przyjęto również założenie, że dwa zasoby zostały utworzone przez przekazanie dwóch plików wideo i poznanie wynikowych wartości AssetID.
* Fragment kodu i ustawienia wstępne JSON przedstawiają przykład łączenia dwóch plików wideo. Można ją rozłożyć na więcej niż dwa filmy wideo:

  1. Wywoływanie zadania. InputAssets. Add () wielokrotnie, aby dodać więcej filmów wideo w pożądanej kolejności.
  2. Wprowadzanie odpowiednich zmian do elementu "sources" w kodzie JSON przez dodanie większej liczby wpisów w tej samej kolejności.

### <a name="net-code"></a>Kod platformy .NET

```csharp
IAsset asset1 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:606db602-efd7-4436-97b4-c0b867ba195b").FirstOrDefault();
IAsset asset2 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e").FirstOrDefault();

// Declare a new job.
IJob job = _context.Jobs.Create("Media Encoder Standard Job for Concatenating Videos");
// Get a media processor reference, and pass to it the name of the
// processor to use for the specific task.
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

// Load the XML (or JSON) from the local file.
string configuration = File.ReadAllText(@"c:\supportFiles\preset.json");

// Create a task
ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
    processor,
    configuration,
    TaskOptions.None);

// Specify the input videos to be concatenated (in order).
task.InputAssets.Add(asset1);
task.InputAssets.Add(asset2);
// Add an output asset to contain the results of the job.
// This output is specified as AssetCreationOptions.None, which
// means the output asset is not encrypted.
task.OutputAssets.AddNew("Output asset",
    AssetCreationOptions.None);

job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
job.Submit();
job.GetExecutionProgressTask(CancellationToken.None).Wait();
```

### <a name="json-preset"></a>Ustawienia wstępne JSON

Zaktualizuj niestandardowe ustawienia wstępne z identyfikatorami zasobów, które chcesz połączyć, oraz z odpowiednim segmentem czasu dla każdego wideo.

```json
{
  "Version": 1.0,
  "Sources": [
    {
      "AssetID": "606db602-efd7-4436-97b4-c0b867ba195b",
      "StartTime": "00:00:01",
      "Duration": "00:00:15"
    },
    {
      "AssetID": "a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e",
      "StartTime": "00:00:02",
      "Duration": "00:00:05"
    }
  ],
  "Codecs": [
    {
      "KeyFrameInterval": "00:00:02",
      "SceneChangeDetection": true,
      "H264Layers": [
        {
          "Level": "auto",
          "Bitrate": 1800,
          "MaxBitrate": 1800,
          "BufferWindow": "00:00:05",
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "Width": "640",
          "Height": "360",
          "FrameRate": "0/1"
        }
      ],
      "Type": "H264Video"
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
      "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

## <a name="crop-videos-with-media-encoder-standard"></a><a id="crop"></a>Przycinanie wideo za pomocą usługi Media Encoder Standard
Zapoznaj się z tematem [przycinanie wideo za pomocą Media Encoder Standard](media-services-crop-video.md) .

## <a name="insert-a-video-track-when-input-has-no-video"></a><a id="no_video"></a>Wstaw ścieżkę wideo, gdy dane wejściowe nie zawierają wideo

Domyślnie, Jeśli wysyłasz dane wejściowe do kodera, który zawiera tylko dźwięk, a nie wideo, wówczas wyjściowy element zawartości zawiera pliki, które zawierają tylko dane audio. Niektóre odtwarzacze, [w tym Azure Media Player (zobacz)](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/8082468-audio-only-scenarios), mogą nie być w stanie obsłużyć takich strumieni. Możesz użyć tego ustawienia, aby wymusić, że koder dodaje czarną ścieżkę wideo do danych wyjściowych w tym scenariuszu.

> [!NOTE]
> Wymuszanie, aby koder wstawiał wynikowe śledzenie wideo, zwiększa rozmiar wyjściowego elementu zawartości, a tym samym kosztem ponoszonym przez zadanie kodowania. Należy uruchomić testy, aby sprawdzić, czy ten wynikowy wzrost ma tylko umiarkowany wpływ na opłaty miesięczne.
>

### <a name="inserting-video-at-only-the-lowest-bitrate"></a>Wstawianie wideo tylko przy najmniejszej szybkości transmisji bitów

Załóżmy, że używasz wielu ustawień predefiniowanych kodowania transmisji bitów, takich jak ["wielokrotna H264 wiele szybkości transmisji bitów 720"](media-services-mes-preset-h264-multiple-bitrate-720p.md) , aby zakodować cały wykaz danych wejściowych do przesyłania strumieniowego, który zawiera wiele plików wideo i plików dźwiękowych. W tym scenariuszu, gdy dane wejściowe nie zawierają wideo, możesz wymusić, aby koder wstawiał czarną ścieżkę wideo z szybkością najmniejszej szybkości transmisji bitów, zamiast wstawiać wideo przy każdej wyjściowej szybkości transmisji bitów. Aby to osiągnąć, należy użyć flagi **InsertBlackIfNoVideoBottomLayerOnly** .

W tej sekcji można zastosować dowolne ustawienia wstępne, które opisano w [tym](media-services-mes-presets-overview.md) temacie, i wprowadzić następujące zmiany:

#### <a name="json-preset"></a>Ustawienia wstępne JSON

```json
{
  "KeyFrameInterval": "00:00:02",
  "StretchMode": "AutoSize",
  "Condition": "InsertBlackIfNoVideoBottomLayerOnly",
  "H264Layers": [
    …
  ]
}
```

#### <a name="xml-preset"></a>Ustawienia wstępne XML

W przypadku korzystania z XML, należy użyć warunku = "InsertBlackIfNoVideoBottomLayerOnly" jako atrybutu do elementu **H264Video** i Condition = "InsertSilenceIfNoAudio" jako atrybutu do **AACAudio**.

```xml
. . .
<Encoding>
  <H264Video Condition="InsertBlackIfNoVideoBottomLayerOnly">
    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <SceneChangeDetection>true</SceneChangeDetection>
    <StretchMode>AutoSize</StretchMode>
    <H264Layers>
      <H264Layer>
        . . .
      </H264Layer>
    </H264Layers>
    <Chapters />
  </H264Video>
  <AACAudio Condition="InsertSilenceIfNoAudio">
    <Profile>AACLC</Profile>
    <Channels>2</Channels>
    <SamplingRate>48000</SamplingRate>
    <Bitrate>128</Bitrate>
  </AACAudio>
</Encoding>
. . .
```

### <a name="inserting-video-at-all-output-bitrates"></a>Wstawianie wideo na wszystkich wyjściowych szybkości transmisji bitów
Załóżmy, że używasz wielu ustawień predefiniowanych kodowania szybkości transmisji bitów, takich jak ["wielokrotna H264 wiele szybkości transmisji bitów](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) , aby zakodować cały wykaz danych wejściowych do przesyłania strumieniowego, który zawiera kombinację plików wideo i plików audio. W tym scenariuszu, gdy dane wejściowe nie zawierają wideo, możesz wymusić, aby koder mógł wstawić czarną ścieżkę wideo na wszystkie szybkości transmisji bitów. Pozwala to zagwarantować, że zasoby wyjściowe są bardzo jednorodne w odniesieniu do liczby ścieżek wideo i ścieżek audio. Aby to osiągnąć, należy określić flagę "InsertBlackIfNoVideo".

W tej sekcji można zastosować dowolne ustawienia wstępne, które opisano w [tym](media-services-mes-presets-overview.md) temacie, i wprowadzić następujące zmiany:

#### <a name="json-preset"></a>Ustawienia wstępne JSON

```json
{
  "KeyFrameInterval": "00:00:02",
  "StretchMode": "AutoSize",
  "Condition": "InsertBlackIfNoVideo",
  "H264Layers": [
    …
  ]
}
```

#### <a name="xml-preset"></a>Ustawienia wstępne XML

W przypadku korzystania z XML, należy użyć warunku = "InsertBlackIfNoVideo" jako atrybutu do elementu **H264Video** i Condition = "InsertSilenceIfNoAudio" jako atrybutu do **AACAudio**.

```xml
. . .
<Encoding>
  <H264Video Condition="InsertBlackIfNoVideo">
    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <SceneChangeDetection>true</SceneChangeDetection>
    <StretchMode>AutoSize</StretchMode>
    <H264Layers>
      <H264Layer>
        . . .
      </H264Layer>
    </H264Layers>
    <Chapters />
  </H264Video>
  <AACAudio Condition="InsertSilenceIfNoAudio">
    <Profile>AACLC</Profile>
    <Channels>2</Channels>
    <SamplingRate>48000</SamplingRate>
    <Bitrate>128</Bitrate>
  </AACAudio>
</Encoding>
. . .  
```

## <a name="rotate-a-video"></a><a id="rotate_video"></a>Obróć wideo
[Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) obsługuje rotację według kątów 0/90/180/270. Domyślnym zachowaniem jest "automatycznie", gdzie próbuje wykryć metadane rotacji w pliku wideo przychodzącego i wyrównać je. Dołącz następujący element **sources** do jednego z ustawień predefiniowanych zdefiniowanych w [tej](media-services-mes-presets-overview.md) sekcji:

### <a name="json-preset"></a>Ustawienia wstępne JSON

```json
  "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
  ],
  "Codecs": [

    ...
```

### <a name="xml-preset"></a>Ustawienia wstępne XML

```xml
<Sources>
  <Source>
    <Streams />
    <Filters>
      <Rotation>90</Rotation>
    </Filters>
  </Source>
</Sources>
```

Ponadto zapoznaj się z [tym](media-services-mes-schema.md#PreserveResolutionAfterRotation) tematem, aby uzyskać więcej informacji na temat sposobu, w jaki koder interpretuje ustawienia szerokości i wysokości w ustawieniu wstępnym, gdy jest wyzwalana kompensacja obrotu.

Można użyć wartości "0", aby wskazać koderowi do ignorowania metadanych rotacji, jeśli jest obecny, w wejściowym wideo.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zobacz też
[Omówienie kodowania Media Services](media-services-encode-asset.md)
