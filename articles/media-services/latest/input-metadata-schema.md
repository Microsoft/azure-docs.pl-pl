---
title: Schemat metadanych wejścia Azure Media Services v3
description: Ten artykuł zawiera omówienie schematu metadanych wejścia Azure Media Services v3.
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
ms.openlocfilehash: 1ca526b7ecbe20a54ec115521cdfbc93c713e0da
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360058"
---
# <a name="input-metadata"></a>Metadane wejściowe

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Zadanie kodowania jest skojarzone z wejściowym zasobem (lub zasobami), na którym mają zostać wykonane pewne zadania kodowania.  Po zakończeniu zadania tworzony jest wyjściowy element zawartości. Element zawartości wyjściowej zawiera wideo, audio, miniatury, manifest i inne pliki. 

Wyjściowy element zawartości zawiera również plik z metadanymi zawierającymi dane wejściowe. Nazwa pliku JSON metadanych ma identyfikator losowy, nie należy używać go do identyfikowania wejściowego zasobu, do którego należy element zawartości wyjściowej. Aby zidentyfikować wejściowy zasób, do którego należy, użyj `Uri` pola (Aby uzyskać więcej informacji, zobacz [inne elementy podrzędne](#other-child-elements)).  

Media Services nie zapobiegawczo do skanowania danych wejściowych w celu wygenerowania metadanych. Metadane wejściowe są generowane tylko jako artefakt, gdy wejściowy element zawartości jest przetwarzany w zadaniu. W związku z tym ten artefakt jest zapisywana w elemencie zawartości wyjściowej. Do generowania metadanych dla wejściowych zasobów i elementów zawartości wyjściowej są używane różne narzędzia. W związku z tym metadane wejściowe mają nieco inny schemat niż metadane wyjściowe.

W tym artykule omówiono elementy i typy schematu JSON, na których bazuje dane wejściowe metada ( &lt; asset_id &gt;_metadata.js). Aby uzyskać informacje o pliku zawierającym metadane dotyczące wyjściowego elementu zawartości, zobacz [metadane wyjściowe](output-metadata-schema.md).  

Przykład schematu JSON można znaleźć na końcu tego artykułu.  

## <a name="assetfile"></a>AssetFile  

Zawiera kolekcję elementów AssetFile dla zadania kodowania.  

> [!NOTE]
> Następujące cztery elementy podrzędne muszą występować w sekwencji.  
> 
> 

| Nazwa  | Opis |
| --- | --- | 
| **VideoTracks**|Każdy fizyczny plik zasobów może zawierać co najmniej zero ścieżek wideo, które są przeplatane w odpowiednim formacie kontenera. Aby uzyskać więcej informacji, zobacz [VideoTracks](#videotracks). |
| **AudioTracks**|Każdy fizyczny plik zasobów może zawierać co najmniej zero ścieżek audio przeplatanych w odpowiedni format kontenera. Aby uzyskać więcej informacji, zobacz [AudioTracks](#audiotracks) |
| **Metadane**  |Metadane pliku zasobów reprezentowane jako ciągi key\value. <br />Na przykład: `<Metadata key="language" value="eng" />` |

### <a name="other-child-elements"></a>Inne elementy podrzędne

| Nazwa | Opis |
| --- | --- |
| **Nazwa**<br />Wymagane |Nazwa pliku zasobu. <br /><br />Przykład: `"Name": "Ignite-short.mp4"` |
| **Adresu**<br />Wymagane |Adres URL, pod którym znajduje się wejściowy element zawartości. Aby zidentyfikować wejściowy zasób, do którego należy element zawartości wyjściowej, użyj `Uri` pola zamiast identyfikatora.|
| **Rozmiar**<br />Wymagane |Rozmiar pliku zasobów w bajtach.  <br /><br />Przykład: `"Size": 75739259`|
| **Czas trwania**<br />Wymagane |Czas trwania odtwarzania zawartości. <br /><br />Przykład: `"Duration": "PT1M10.304S"`. |
| **NumberOfStreams**<br />Wymagane |Liczba strumieni w pliku zasobów.  <br /><br />Przykład: `"NumberOfStreams": 2`|
| **FormatNames**<br />Wymagane |Nazwy formatów.  <br /><br />Przykład: `"FormatNames": "mov,mp4,m4a,3gp,3g2,mj2"`|
| **FormatVerboseName**<br /> Wymagane |Sformatuj pełne nazwy. <br /><br />Przykład: `"FormatVerboseName": "QuickTime / MOV"` |
| **Rozpoczęcia** |Godzina rozpoczęcia zawartości.  <br /><br />Przykład: `"StartTime": "PT0S"` |
| **OverallBitRate** |Średnia szybkość transmisji pliku zasobów w bitach na sekundę.  <br /><br />Przykład: `"OverallBitRate": 8618539`|

## <a name="videotracks"></a>VideoTracks

| Nazwa | Opis |
| --- | --- |
| **FourCC**<br />Wymagane |Kod kodera-dekoder wideo, który jest raportowany przez narzędzia FFmpeg.<br /><br />Przykład: `"FourCC": "avc1"` |
| **Profil** |Profil ścieżki wideo. <br /><br />Przykład: `"Profile": "Main"`|
| **Poziomie** |Poziom ścieżki wideo. <br /><br />Przykład: `"Level": "3.2"`|
| **PixelFormat** |Format piksela ścieżki wideo. <br /><br />Przykład: `"PixelFormat": "yuv420p"`|
| **Width**<br />Wymagane |Zakodowana Szerokość wideo (w pikselach). <br /><br />Przykład: `"Width": "1280"`|
| **Proporcj**<br />Wymagane |Zakodowana wysokość wideo w pikselach.<br /><br />Przykład: `"Height": "720"` |
| **DisplayAspectRatioNumerator**<br />Wymagane |Licznik współczynnik proporcji wyświetlania wideo.<br /><br />Przykład: `"DisplayAspectRatioNumerator": 16.0` |
| **DisplayAspectRatioDenominator**<br />Wymagane |Mianownik współczynnika proporcji wyświetlania wideo. <br /><br />Przykład: `"DisplayAspectRatioDenominator": 9.0`|
| **SampleAspectRatioNumerator** |Przykładowy współczynnik proporcji filmu wideo. <br /><br />Przykład: `"SampleAspectRatioNumerator": 1.0`|
| **SampleAspectRatioDenominator**|Przykład: `"SampleAspectRatioDenominator": 1.0`|
| **Szybkości**<br />Wymagane |Mierzona szybkość klatek wideo w formacie. 3F. <br /><br />Przykład: `"FrameRate": 29.970`|
| **Multimedia** |Średnia szybkość transmisji bitów wideo w bitach na sekundę, obliczona na podstawie pliku zasobów. Zliczane jest tylko podstawowy ładunek strumienia, a obciążenie opakowania nie jest uwzględniane. <br /><br />Przykład: `"Bitrate": 8421583`|
| **HasBFrames** |Numer ścieżki wideo klatek B. <br /><br />Przykład: `"HasBFrames": 2`|
| **Metadane** |Ogólne ciągi klucz/wartość, których można użyć do przechowywania różnych informacji. <br />Zobacz pełny przykład na końcu artykułu. |
| **#C1**<br />Wymagane |Indeks tej ścieżki audio lub wideo liczony od zera.<br /><br /> Ten **Identyfikator** nie musi być TrackID używany w pliku MP4. <br /><br />Przykład: `"Id": 2`|
| **Wymaga** |Ciąg kodera-dekoder śledzenia wideo. <br /><br />Przykład: `"Codec": "h264"`|
| **CodecLongName** |Długa nazwa kodera-dekoder dla ścieżki audio lub wideo. <br /><br />Przykład: `"CodecLongName": "H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10"`|
| **Wymaga** |Ciąg kodera-dekoder śledzenia wideo. <br /><br />Przykład: `"Codec": "h264"`|
| **Ujemną**<br />Wymagane |Podstawa czasu.<br /><br />Przykład: `"TimeBase": "1/30000"`|
| **NumberOfFrames** |Liczba ramek (obecnych dla ścieżek wideo). <br /><br />Przykład: `"NumberOfFrames": 2107`|
| **Rozpoczęcia** |Godzina rozpoczęcia śledzenia.<br /><br />Przykład: `"StartTime": "PT0.033S"` |
| **Czas trwania** |Czas trwania śledzenia. <br /><br />Przykład: `"Duration": "PT1M10.304S"`|

## <a name="audiotracks"></a>AudioTracks

| Nazwa  | Opis |
| --- | --- | 
| **SampleFormat** |Przykładowy format. <br /><br />Przykład: `"SampleFormat": "fltp"`|
| **ChannelLayout** |Układ kanału. <br /><br />Przykład: `"ChannelLayout": "stereo"`|
| **Kanały**<br />Wymagane |Liczba (0 lub więcej) kanałów audio. <br /><br />Przykład: `"Channels": 2`|
| **SamplingRate**<br />Wymagane |Częstotliwość próbkowania audio w próbkach/s lub Hz. <br /><br />Przykład: `"SamplingRate": 48000`|
| **Multimedia** |Średnia szybkość transmisji bitów audio w bitach na sekundę, obliczona na podstawie pliku zasobów. Zliczany jest tylko podstawowy ładunek strumienia, a obciążenie opakowania nie jest uwzględniane w tej liczbie. <br /><br />Przykład: `"Bitrate": 192080`|
| **Metadane** |Ogólne ciągi klucz/wartość, których można użyć do przechowywania różnych informacji.  <br />Zobacz pełny przykład na końcu artykułu. |
| **#C1**<br />Wymagane |Indeks tej ścieżki audio lub wideo liczony od zera.<br /><br /> Nie musi to być TrackID używany w pliku MP4. <br /><br />Przykład: `"Id": 1`|
| **Wymaga** |Ciąg kodera-dekoder śledzenia wideo. <br /><br />Przykład: `"Codec": "aac"`|
| **CodecLongName** |Długa nazwa kodera-dekoder dla ścieżki audio lub wideo. <br /><br />Przykład: `"CodecLongName": "AAC (Advanced Audio Coding)"`|
| **Ujemną**<br />Wymagane |Podstawa czasu.<br /><br />Przykład: `"TimeBase": "1/48000"` |
| **NumberOfFrames** |Liczba ramek (obecnych dla ścieżek wideo). <br /><br />Przykład: `"NumberOfFrames": 3294`|
| **Rozpoczęcia** |Godzina rozpoczęcia śledzenia. Aby uzyskać więcej informacji, zobacz [ISO8601](https://www.iso.org/iso-8601-date-and-time-format.html). <br /><br />Przykład: `"StartTime": "PT0S"` |
| **Czas trwania** |Czas trwania śledzenia. <br /><br />Przykład: `"Duration": "PT1M10.272S"` |

## <a name="metadata"></a>Metadane

| Nazwa | Opis |
| --- | --- |
| **głównych**<br />Wymagane |Klucz w parze klucz/wartość. |
| **wartość**<br /> Wymagane |Wartość w parze klucz/wartość. |

## <a name="schema-example"></a>Przykład schematu

```json
{
  "AssetFile": [
    {
      "VideoTracks": [
        {
          "FourCC": "avc1",
          "Profile": "Main",
          "Level": "3.2",
          "PixelFormat": "yuv420p",
          "Width": "1280",
          "Height": "720",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "SampleAspectRatioNumerator": 1.0,
          "SampleAspectRatioNumeratorSpecified": true,
          "SampleAspectRatioDenominator": 1.0,
          "SampleAspectRatioDenominatorSpecified": true,
          "FrameRate": 29.970,
          "Bitrate": 8421583,
          "BitrateSpecified": true,
          "HasBFrames": 2,
          "HasBFramesSpecified": true,
          "Disposition": {
            "Default": 1
          },
          "Metadata": [
            {
              "key": "creation_time",
              "value": "2018-02-21T21:42:08.000000Z"
            },
            {
              "key": "language",
              "value": "eng"
            },
            {
              "key": "handler_name",
              "value": "Video Media Handler"
            },
            {
              "key": "encoder",
              "value": "AVC Coding"
            }
          ],
          "Id": 2,
          "Codec": "h264",
          "CodecLongName": "H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10",
          "TimeBase": "1/30000",
          "NumberOfFrames": 2107,
          "NumberOfFramesSpecified": true,
          "StartTime": "PT0.033S",
          "Duration": "PT1M10.304S"
        }
      ],
      "AudioTracks": [
        {
          "SampleFormat": "fltp",
          "ChannelLayout": "stereo",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 192080,
          "BitrateSpecified": true,
          "BitsPerSampleSpecified": true,
          "Disposition": {
            "Default": 1
          },
          "Metadata": [
            {
              "key": "creation_time",
              "value": "2018-02-21T21:42:08.000000Z"
            },
            {
              "key": "language",
              "value": "eng"
            },
            {
              "key": "handler_name",
              "value": "Sound Media Handler"
            }
          ],
          "Id": 1,
          "Codec": "aac",
          "CodecLongName": "AAC (Advanced Audio Coding)",
          "TimeBase": "1/48000",
          "NumberOfFrames": 3294,
          "NumberOfFramesSpecified": true,
          "StartTime": "PT0S",
          "Duration": "PT1M10.272S"
        }
      ],
      "Metadata": [
        {
          "key": "major_brand",
          "value": "mp42"
        },
        {
          "key": "minor_version",
          "value": "19529854"
        },
        {
          "key": "compatible_brands",
          "value": "mp42isom"
        },
        {
          "key": "creation_time",
          "value": "2018-02-21T21:42:08.000000Z"
        }
      ],
      "Name": "Ignite-short.mp4",
      "Uri": "https://amsstorageacct.blob.core.windows.net/asset-00000000-0000-0000-000000000000/ignite.mp4",
      "Size": 75739259,
      "Duration": "PT1M10.304S",
      "NumberOfStreams": 2,
      "FormatNames": "mov,mp4,m4a,3gp,3g2,mj2",
      "FormatVerboseName": "QuickTime / MOV",
      "StartTime": "PT0S",
      "OverallBitRate": 8618539,
      "OverallBitRateSpecified": true
    }
  ]
}
```

## <a name="next-steps"></a>Następne kroki

[Metadane wyjściowe](output-metadata-schema.md)
