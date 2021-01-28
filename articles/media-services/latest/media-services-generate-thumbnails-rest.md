---
title: Pobieranie klucza podpisywania z zasad przy użyciu platformy .NET w wersji 3 Azure Media Services
description: W tym artykule przedstawiono sposób użycia funkcji REST do kodowania zasobów i generowania miniatur w tym samym czasie przy użyciu Media Encoder Standard.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 75b8fd53836cce02816413afaeb656c3ac3f8796
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98935398"
---
# <a name="how-to-generate-thumbnails-using-encoder-standard-with-rest"></a>Jak generować miniatury przy użyciu standardu Encoder w warstwie REST

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Za pomocą Media Encoder Standard można wygenerować jeden lub więcej miniatur z wejściowego filmu wideo w formatach plików obrazów [JPEG](https://en.wikipedia.org/wiki/JPEG), [PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics)lub [BMP](https://en.wikipedia.org/wiki/BMP_file_format) .

## <a name="recommended-reading-and-practice"></a>Zalecany odczyt i ćwiczenia

Zaleca się zapoznanie się z niestandardowymi transformacjemi, odczytując [sposób kodowania przy użyciu niestandardowej transformacji](custom-preset-rest-howto.md).

## <a name="thumbnail-parameters"></a>Parametry miniatur

Należy ustawić następujące parametry:

- **początek** — pozycja w wejściowym filmie wideo z lokalizacji, w której ma zostać rozpoczęte Generowanie miniatur. Wartość może być w formacie ISO 8601 (na przykład PT05S rozpoczyna się od 5 sekund) lub liczba klatek (na przykład 10, aby zacząć od dziesiątej ramki) lub wartość względna do czasu trwania strumienia (na przykład 10% do uruchomienia o 10% czasu trwania strumienia). Obsługuje również makro {Najlepsza}, które informuje koder, aby wybierał najlepszą miniaturę z pierwszych kilku sekund filmu wideo i utworzy tylko jedną miniaturę bez względu na to, jakie inne ustawienia są przeznaczone dla kroku i zakresu. Wartość domyślna to makro {Najlepsza}.
- **krok** — interwały, w których są generowane miniatury. Wartość może być w formacie ISO 8601 (na przykład PT05S dla jednego obrazu co 5 sekund) lub liczba klatek (na przykład 30 dla jednego obrazu co 30 klatek) lub wartość względna do czasu trwania strumienia (na przykład 10% dla jednego obrazu co 10% czasu trwania strumienia). Wartość kroku będzie miała wpływ na pierwszą wygenerowaną miniaturę, która może nie być dokładnie określona w czasie rozpoczęcia przekształceń wstępnej. Jest to spowodowane koderem, który próbuje wybrać najlepszą miniaturę między godziną rozpoczęcia a pozycją kroku od czasu rozpoczęcia jako pierwszy wynik. Ponieważ wartość domyślna to 10%, oznacza to, że jeśli strumień ma długi czas trwania, to pierwsza wygenerowana Miniatura może znajdować się daleko od określonej w czasie rozpoczęcia. Spróbuj wybrać rozsądną wartość dla kroku, jeśli pierwsza miniatura powinna być bliska czasowi rozpoczęcia, lub ustaw wartość zakres na 1, jeśli podczas uruchamiania jest wymagana tylko jedna miniatura.
- **zakres** — pozycja względem predefiniowanego czasu rozpoczęcia w wejściowym wideo, w którym ma zostać zatrzymane Generowanie miniatur. Wartość może być w formacie ISO 8601 (na przykład PT5M30S, aby zatrzymać o 5 minut i 30 sekund od czasu rozpoczęcia) lub liczbę klatek (na przykład 300, aby zatrzymać ją w klatce 300th z ramki w czasie rozpoczęcia. Jeśli ta wartość wynosi 1, oznacza to, że jest tworzona tylko jedna Miniatura w czasie rozpoczęcia lub wartość względna do czasu trwania strumienia (na przykład 50%, aby zatrzymać o połowie okresu trwania strumienia od czasu rozpoczęcia). Wartość domyślna to 100%, co oznacza zatrzymanie na końcu strumienia.
- **warstwy** — kolekcja warstw obrazów wyjściowych, które mają zostać utworzone przez koder.

## <a name="example-of-a-single-png-file-preset"></a>Przykład ustawienia wstępnego "pojedynczego pliku PNG"

Poniższe ustawienie wstępne JSON może służyć do tworzenia pojedynczego wyjściowego pliku PNG z pierwszych kilku sekund wejściowego filmu wideo, gdzie koder zapewnia najlepszą próbę znalezienia "interesującej" ramki. Należy pamiętać, że wymiary obrazu wyjściowego zostały ustawione na 100%, co oznacza, że są one zgodne z wymiarami wejściowego wideo. Należy również pamiętać, że ustawienie "format" w elemencie "Outputs" jest wymagane do dopasowania do użycia "PngLayers" w sekcji "dekodery".  

```json
{
    "properties": {
        "description": "Basic Transform using a custom encoding preset for thumbnails",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
                    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
                    "codecs": [
                        {
                            "@odata.type": "#Microsoft.Media.PngImage",
                            "stretchMode": "AutoSize",
                            "start": "{Best}",
                            "step": "25%",
                            "range": "80%",
                            "layers": [
                                {
                                    "width": "50%",
                                    "height": "50%"
                                }
                            ]
                        }
                    ],
                    "formats": [
                        {
                            "@odata.type": "#Microsoft.Media.Mp4Format",
                            "filenamePattern": "Video-{Basename}-{Label}-{Bitrate}{Extension}",
                            "outputFiles": []
                        },
                        {
                            "@odata.type": "#Microsoft.Media.PngFormat",
                            "filenamePattern": "Thumbnail-{Basename}-{Index}{Extension}"
                        }
                    ]
                }
            }
        ]
    }
}

```

## <a name="example-of-a-series-of-jpeg-images-preset"></a>Przykład ustawienia wstępnego "Seria obrazów JPEG"

Poniższe ustawienie wstępne JSON może służyć do tworzenia zestawu 10 obrazów w sygnaturach czasowych 5%, 15%,..., 95% wejściowej osi czasu, gdzie rozmiar obrazu jest określony jako jedna czwarta wejściowego wideo.

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

## <a name="example-of-a-one-image-at-a-specific-timestamp-preset"></a>Przykład ustawienia "jeden obraz w określonym znaczniku czasu"

Poniższe ustawienie wstępne JSON może służyć do tworzenia pojedynczego obrazu JPEG z 30-sekundowym znacznikiem wideo wejściowego. To ustawienie wstępne oczekuje, że wejściowy film wideo będzie większy niż 30 sekund (w przeciwnym razie zadanie zakończy się niepowodzeniem).

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

## <a name="example-of-a-thumbnails-at-different-resolutions-preset"></a>Przykład ustawienia "miniatury w różnych rozdzielczościach"

Poniższego ustawienia wstępnego można użyć do wygenerowania miniatur w różnych rozdzielczościach w jednym zadaniu. W przykładzie w pozycjach 5%, 15%,..., 95% wejściowej osi czasu koder generuje dwa obrazy — jeden o 100% wejściowej rozdzielczości wideo, a drugi przy 50%.

Zwróć uwagę na użycie makra {Solution} w nazwie pliku; wskazuje, że koder ma używać szerokości i wysokości określonej w sekcji kodowanie ustawienia wstępnego podczas generowania nazwy pliku obrazów wyjściowych. Pozwala to również łatwo rozróżnić różne obrazy.

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

## <a name="example-of-generating-a-thumbnail-while-encoding"></a>Przykład generowania miniatury podczas kodowania

Chociaż wszystkie powyższe przykłady zostały omówione w tym, jak można przesłać zadanie kodowania, które tworzy tylko obrazy, można także połączyć kodowanie wideo/audio z generowaniem miniatur. Poniższe ustawienie wstępne JSON nakazuje programowi Encoder wygenerowanie miniatury podczas kodowania.

### <a name="json-preset"></a>Ustawienia wstępne JSON

Aby uzyskać informacje o schemacie, zobacz [ten](../previous/media-services-mes-schema.md) artykuł.

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

## <a name="next-steps"></a>Następne kroki
[Generuj miniatury przy użyciu platformy .NET](media-services-generate-thumbnails-dotnet.md)