---
title: Schemat metadanych danych wyjściowych Azure Media Services
description: Ten artykuł zawiera omówienie schematu metadanych danych wyjściowych Azure Media Services v3.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: f26905366949c2c198e52f78bc7adb734cbb7f90
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102612460"
---
# <a name="output-metadata"></a>Metadane wyjściowe

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Zadanie kodowania jest skojarzone z wejściowym zasobem (lub zasobami), na którym mają zostać wykonane pewne zadania kodowania. Na przykład Koduj plik MP4 do H. 264 zestawy adaptacyjnej szybkości transmisji bitów. Utwórz miniaturę; Utwórz nakładki. Po zakończeniu zadania tworzony jest wyjściowy element zawartości.  Element zawartości wyjściowej zawiera wideo, audio, miniatury i inne pliki. Wyjściowy element zawartości zawiera również plik z metadanymi dotyczącymi wyjściowego elementu zawartości. Nazwa pliku JSON metadanych ma następujący format: `<source_file_name>_manifest.json` (na przykład `BigBuckBunny_manifest.json` ). Aby znaleźć nazwę pliku źródłowego (bez obcięcia), należy przeprowadzić skanowanie w poszukiwaniu dowolnego znaku * _metadata.jsi zbadać ciąg FilePath w programie.

Media Services nie zapobiegawczo do skanowania danych wejściowych w celu wygenerowania metadanych. Metadane wejściowe są generowane tylko jako artefakt, gdy wejściowy element zawartości jest przetwarzany w zadaniu. W związku z tym ten artefakt jest zapisywana w elemencie zawartości wyjściowej. Do generowania metadanych dla wejściowych zasobów i elementów zawartości wyjściowej są używane różne narzędzia. W związku z tym metadane wejściowe mają nieco inny schemat niż metadane wyjściowe.

W tym artykule omówiono elementy i typy schematu JSON, na których bazują metadane wyjściowe ( &lt; source_file_name &gt;_manifest.js). <!--For information about the file that contains metadata about the input asset, see [Input metadata](input-metadata-schema.md).  -->

Pełny kod schematu i przykład JSON można znaleźć na końcu tego artykułu.  

## <a name="assetfile"></a>AssetFile

Kolekcja wpisów AssetFile dla zadania kodowania.  

| Nazwa | Opis |
| --- | --- |
| **Źródła** |Kolekcja plików nośnika danych wejściowych/źródłowych, które zostały przetworzone w celu utworzenia tego AssetFile.<br />Przykład: `"Sources": [{"Name": "Ignite-short_1280x720_AACAudio_3551.mp4"}]`|
| **VideoTracks**|Każda AssetFile fizyczna może zawierać w niej zero lub więcej ścieżek wideo z przeplotem do odpowiedniego formatu kontenera. <br />Zobacz [VideoTracks](#videotracks). |
| **AudioTracks**|Każda AssetFile fizyczna może zawierać w niej zero lub więcej ścieżek audio przeplatanych w odpowiedni format kontenera. To jest kolekcja wszystkich ścieżek audio.<br /> Aby uzyskać więcej informacji, zobacz [AudioTracks](#audiotracks). |
| **Nazwa**<br />Wymagane |Nazwa pliku zasobów nośnika. <br /><br />Przykład: `"Name": "Ignite-short_1280x720_AACAudio_3551.mp4"`|
| **Rozmiar**<br />Wymagane |Rozmiar pliku zasobów w bajtach. <br /><br />Przykład: `"Size": 32414631`|
| **Czas trwania**<br />Wymagane |Czas trwania odtwarzania zawartości. Aby uzyskać więcej informacji, zobacz Format [ISO8601](https://www.iso.org/iso-8601-date-and-time-format.html) . <br /><br />Przykład: `"Duration": "PT1M10.315S"`|

## <a name="videotracks"></a>VideoTracks 

Każda AssetFile fizyczna może zawierać w niej zero lub więcej ścieżek wideo z przeplotem do odpowiedniego formatu kontenera. Element **VideoTracks** reprezentuje kolekcję wszystkich ścieżek wideo.  

| Nazwa | Opis |
| --- | --- |
| **#C1**<br /> Wymagane |Indeks tej ścieżki wideo liczony od zera. **Uwaga:**  Ten **Identyfikator** nie musi być TrackID używany w pliku MP4. <br /><br />Przykład: `"Id": 1`|
| **FourCC**<br />Wymagane | Kod kodera-dekoder wideo, który jest raportowany przez narzędzia FFmpeg.  <br /><br />Przykład: `"FourCC": "avc1" | "hev1" | "hvc1"`|
| **Profilu** |Profil wielokrotna H264 (dotyczy tylko wielokrotna H264 Codec)  <br /><br />Przykład: `"Profile": "High"` |
| **Poziomie** |Poziom wielokrotna H264 (dotyczy tylko wielokrotna H264 Codec).  <br /><br />Przykład: `"Level": "3.2"`|
| **Width**<br />Wymagane |Zakodowana Szerokość wideo (w pikselach).  <br /><br />Przykład: `"Width": "1280"`|
| **Height**<br />Wymagane |Zakodowana wysokość wideo w pikselach.  <br /><br />Przykład: `"Height": "720"`|
| **DisplayAspectRatioNumerator**<br />Wymagane|Licznik współczynnik proporcji wyświetlania wideo.  <br /><br />Przykład: `"DisplayAspectRatioNumerator": 16.0`|
| **DisplayAspectRatioDenominator**<br />Wymagane |Mianownik współczynnika proporcji wyświetlania wideo.  <br /><br />Przykład: `"DisplayAspectRatioDenominator": 9.0`|
| **Framerate**<br />Wymagane |Mierzona szybkość klatek wideo w formacie. 3F.  <br /><br />Przykład: `"Framerate": 29.970`|
| **Multimedia**<br />Wymagane |Średnia szybkość transmisji bitów wideo w bitach na sekundę, obliczona na podstawie AssetFile. Zlicza tylko podstawowy ładunek strumienia i nie uwzględnia obciążenia opakowania.  <br /><br />Przykład: `"Bitrate": 3551567`|
| **TargetBitrate**<br />Wymagane |Przekieruj średnią szybkość transmisji bitów dla tej ścieżki wideo zgodnie z żądaniem w bitach na sekundę. <br /><br />Przykład: `"TargetBitrate": 3520000` |

## <a name="audiotracks"></a>AudioTracks 

Każda AssetFile fizyczna może zawierać w niej zero lub więcej ścieżek audio przeplatanych w odpowiedni format kontenera. Element **AudioTracks** reprezentuje kolekcję wszystkich ścieżek audio.  

| Nazwa  | Opis |
| --- | --- |
| **#C1**<br />Wymagane  |Indeks tej ścieżki audio liczony od zera. **Uwaga:**  Nie jest to konieczne TrackID w pliku MP4.  <br /><br />Przykład: `"Id": 2`|
| **Wymaga**  |Ciąg Codec ścieżki audio.  <br /><br />Przykład: `"Codec": "aac"`|
| **Język**|Przykład: `"Language": "eng"`|
| **Kanały**<br />Wymagane|Liczba kanałów audio.  <br /><br />Przykład: `"Channels": 2`|
| **SamplingRate**<br />Wymagane |Częstotliwość próbkowania audio w próbkach/s lub Hz.  <br /><br />Przykład: `"SamplingRate": 48000`|
| **Multimedia**<br />Wymagane |Średnia szybkość transmisji bitów audio w bitach na sekundę, obliczona na podstawie AssetFile. Zlicza tylko podstawowy ładunek strumienia i nie uwzględnia obciążenia opakowania.  <br /><br />Przykład: `"Bitrate": 128041`|

## <a name="json-schema-example"></a>Przykład schematu JSON

```json
{
  "AssetFile": [
    {
      "Sources": [
        {
          "Name": "Ignite-short_1280x720_AACAudio_3551.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "3.2",
          "Width": "1280",
          "Height": "720",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 3551567,
          "TargetBitrate": 3520000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_1280x720_AACAudio_3551.mp4",
      "Size": 32414631,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_960x540_AACAudio_2216.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "3.1",
          "Width": "960",
          "Height": "540",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 2216326,
          "TargetBitrate": 2210000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_960x540_AACAudio_2216.mp4",
      "Size": 20680897,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_640x360_AACAudio_1150.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "3.0",
          "Width": "640",
          "Height": "360",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 1150440,
          "TargetBitrate": 1150000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_640x360_AACAudio_1150.mp4",
      "Size": 11313920,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_480x270_AACAudio_722.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "2.1",
          "Width": "480",
          "Height": "270",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 722682,
          "TargetBitrate": 720000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_480x270_AACAudio_722.mp4",
      "Size": 7554708,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_320x180_AACAudio_380.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "1.3",
          "Width": "320",
          "Height": "180",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 380655,
          "TargetBitrate": 380000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }`
      ],
      "Name": "Ignite-short_320x180_AACAudio_380.mp4",
      "Size": 4548932,
      "Duration": "PT1M10.315S"
    }
  ]
}
```

## <a name="next-steps"></a>Następne kroki

[Utwórz dane wejściowe zadania na podstawie adresu URL HTTPS](job-input-from-http-how-to.md)
