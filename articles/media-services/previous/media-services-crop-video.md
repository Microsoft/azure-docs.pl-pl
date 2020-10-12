---
title: Jak przyciąć wideo przy użyciu Media Encoder Standard — Azure | Microsoft Docs
description: Przycinanie jest procesem wyboru prostokątnego okna wewnątrz ramki wideo i kodowania tylko pikseli w tym oknie. W tym artykule przedstawiono sposób przycinania wideo przy użyciu Media Encoder Standard.
services: media-services
documentationcenter: ''
author: anilmur
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: anilmur
ms.reviewer: juliako
ms.openlocfilehash: 4264d1c0d83f14da02b26107d336521250fcf13b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89257861"
---
# <a name="crop-videos-with-media-encoder-standard"></a>Przycinanie wideo za pomocą usługi Media Encoder Standard

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

Za pomocą Media Encoder Standard (MES) można przyciąć dane wejściowe wideo. Przycinanie jest procesem wyboru prostokątnego okna wewnątrz ramki wideo i kodowania tylko pikseli w tym oknie. Poniższy diagram ułatwia zilustrowanie tego procesu.

![Przycinanie wideo](./media/media-services-crop-video/media-services-crop-video01.png)

Załóżmy, że masz jako dane wejściowe wideo, które ma rozdzielczość 1920 x 1080 pikseli (współczynnik proporcji 16:9), ale ma czarne słupki (pola filaru) po lewej i prawej stronie, tak aby tylko okno 4:3 lub piksele 1440x1080 zawierały aktywne wideo. Za pomocą MES można przyciąć lub edytować czarne paski oraz zakodować region 1440x1080.

Przycinanie w MES jest etapem przetwarzania wstępnego, dlatego parametry przycinania w ustawieniach wstępnych kodowania mają zastosowanie do oryginalnego wejściowego wideo. Kodowanie jest kolejnym etapem, a ustawienia szerokość/wysokość mają zastosowanie do *wstępnie przetworzonego* wideo, a nie do oryginalnego wideo. Podczas projektowania ustawień wstępnych należy wykonać następujące czynności: (a) wybrać parametry przycinania na podstawie oryginalnego wejściowego wideo i (b) wybrać ustawienia kodowania na podstawie przyciętego wideo. Jeśli ustawienia kodowania nie są zgodne z przyciętym wideo, dane wyjściowe nie będą oczekiwane.

W [poniższym](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) temacie pokazano, jak utworzyć zadanie kodowania przy użyciu MES i jak określić niestandardowe ustawienia wstępne dla zadania kodowania. 

## <a name="creating-a-custom-preset"></a>Tworzenie niestandardowego ustawienia wstępnego
W przykładzie pokazanym na diagramie:

1. Oryginalne dane wejściowe to 1920 x 1080
2. Musi być przycięty do danych wyjściowych 1440x1080, które są wyśrodkowane w ramce wejściowej
3. Oznacza to, że X przesunięcie (1920 – 1440)/2 = 240 i przesunięcie Y równe zero
4. Szerokość i wysokość prostokąta kadrowania są odpowiednio 1440 i 1080.
5. Na etapie kodowania poproszonym jest wygenerowanie trzech warstw — odpowiednio rozdzielczości 1440x1080, 960x720 i 480x360

### <a name="json-preset"></a>Ustawienia wstępne JSON

```json
{
  "Version": 1.0,
  "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Crop": {
          "X": 240,
          "Y": 0,
          "Width": 1440,
          "Height": 1080
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
          "Bitrate": 3400,
          "MaxBitrate": 3400,
          "BufferWindow": "00:00:05",
          "Width": 1440,
          "Height": 1080,
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
          "Bitrate": 1250,
          "MaxBitrate": 1250,
          "BufferWindow": "00:00:05",
          "Width": 480,
          "Height": 360,
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

## <a name="restrictions-on-cropping"></a>Ograniczenia dotyczące przycinania
Funkcja przycinania ma być ręczna. Konieczne będzie załadowanie wejściowego wideo do odpowiedniego narzędzia do edycji, które pozwala wybrać interesujące Cię ramki, ustawić kursor, aby określić przesunięcia dla prostokąta przycinania, aby określić ustawienie wstępne kodowania, które jest dostrojone dla danego wideo itd. Ta funkcja nie jest przeznaczona do włączania takich elementów, jak: automatyczne wykrywanie i usuwanie obramowań letterbox/pillarbox w wejściowym wideo.

Poniższe ograniczenia dotyczą funkcji przycinania. Jeśli nie są spełnione, zadanie kodowania może zakończyć się niepowodzeniem lub generować nieoczekiwane dane wyjściowe.

1. Współrzędne i rozmiar prostokąta kadrowania muszą pasować do wejściowego wideo
2. Jak wspomniano powyżej, Szerokość & wysokość w ustawieniach kodowania musi odpowiadać wideo z przyciętym
3. Przycinanie dotyczy wideo przechwytywane w trybie poziomym (tj. nie dotyczy filmów wideo zarejestrowanych przy użyciu telefonu smartphone w trybie pionowym lub w pionie).
4. Najlepiej sprawdza się w przypadku progresywnego wideo przechwytywanego przy użyciu pikseli kwadratowych

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Następny krok
Zobacz ścieżki uczenia Azure Media Services, aby uzyskać informacje na temat doskonałych funkcji oferowanych przez AMS.  

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
