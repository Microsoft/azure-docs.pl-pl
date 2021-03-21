---
title: Wielokrotna H264 pojedynczej szybkości transmisji bitów 4K Media Encoder Standard wstępnej platformy Azure | Microsoft Docs
description: Ten artykuł zawiera Media Encoder Standard Omówienie wielokrotna H264 z pojedynczym ustawieniem zadania szybkości transmisji bitów KB/4.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 8e437aea-8193-49a0-9ff2-4fd391c80972
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 62984c938a3db67550b36c731e7a5a27de5df7ad
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103012874"
---
# <a name="h264-single-bitrate-4k"></a>Pojedyncza szybkość transmisji bitów H264 4K

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

`Media Encoder Standard` definiuje zestaw ustawień predefiniowanych kodowania, których można użyć podczas tworzenia zadań kodowania. Możesz użyć, `preset name` Aby określić format, w którym chcesz kodować plik multimedialny. Można też tworzyć własne ustawienia predefiniowane JSON lub XML (przy użyciu kodowania UTF-8 lub UTF-16. Następnie można przekazać niestandardowe ustawienie wstępne do kodera. Aby uzyskać listę wszystkich wstępnie zdefiniowanych nazw obsługiwanych przez ten `Media Encoder Standard` koder, zobacz [Ustawienia wstępne zadań dla Media Encoder Standard](media-services-mes-presets-overview.md).  
  
 W tym temacie przedstawiono `H264 Single Bitrate 4K` Ustawienia wstępne w formacie XML i JSON.  
  
 To ustawienie wstępne tworzy pojedynczy plik MP4 z szybkością transmisji bitów 18000 KB/s oraz stereo AAC audio. Aby uzyskać szczegółowe informacje na temat profilu, szybkości transmisji bitów, częstotliwości próbkowania itp., zapoznaj się z kodem XML lub JSON zdefiniowanym poniżej. Aby dowiedzieć się, co oznacza każdy element w tych ustawieniach wstępnych, i prawidłowe wartości dla każdego elementu, zobacz temat [schemat Media Encoder Standard](media-services-mes-schema.md) .  
  
> [!NOTE]
>  Należy uzyskać typ jednostki zarezerwowanej Premium z kodowaniem 4K. Aby uzyskać więcej informacji, zobacz [Jak skalować kodowanie](./media-services-scale-media-processing-overview.md).  
  
 XML  
  
```  
<?xml version="1.0" encoding="utf-16"?>  
<Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">  
  <Encoding>  
    <H264Video>  
      <KeyFrameInterval>00:00:02</KeyFrameInterval>  
      <SceneChangeDetection>true</SceneChangeDetection>  
      <H264Layers>  
        <H264Layer>  
          <Bitrate>18000</Bitrate>  
          <Width>3840</Width>  
          <Height>2160</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>Auto</Profile>  
          <Level>auto</Level>  
          <BFrames>3</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>true</AdaptiveBFrame>  
          <EntropyMode>Cabac</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>18000</MaxBitrate>  
        </H264Layer>  
      </H264Layers>  
      <Chapters />  
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
  
 JSON  
  
```  
{  
  "Version": 1.0,  
  "Codecs": [  
    {  
      "KeyFrameInterval": "00:00:02",  
      "SceneChangeDetection": true,  
      "H264Layers": [  
        {  
          "Profile": "Auto",  
          "Level": "auto",  
          "Bitrate": 18000,  
          "MaxBitrate": 18000,  
          "BufferWindow": "00:00:05",  
          "Width": 3840,  
          "Height": 2160,  
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
