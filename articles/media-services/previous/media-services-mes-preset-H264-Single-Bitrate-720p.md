---
title: Wielokrotna H264 Media Encoder Standard pojedynczej szybkości transmisji bitów Microsoft Docs
description: Ten artykuł zawiera omówienie ustawienia Media Encoder Standard "wielokrotna H264 pojedynczej szybkości transmisji bitów".
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: f66da66c-9f21-441d-8038-51e3094e9307
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: ee6356981f6d7dace7869c99f69b0719677c5513
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89261567"
---
# <a name="h264-single-bitrate-720p"></a>Pojedyncza szybkość transmisji bitów H264 720p

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

`Media Encoder Standard` definiuje zestaw ustawień predefiniowanych kodowania, których można użyć podczas tworzenia zadań kodowania. Możesz użyć, `preset name` Aby określić format, w którym chcesz kodować plik multimedialny. Można też tworzyć własne ustawienia predefiniowane JSON lub XML (przy użyciu kodowania UTF-8 lub UTF-16. Następnie można przekazać niestandardowe ustawienie wstępne do kodera. Aby uzyskać listę wszystkich wstępnie zdefiniowanych nazw obsługiwanych przez ten `Media Encoder Standard` koder, zobacz [Ustawienia wstępne zadań dla Media Encoder Standard](media-services-mes-presets-overview.md).  
  
 W tym temacie przedstawiono `H264 Single Bitrate 720p` Ustawienia wstępne w formacie XML i JSON.  
  
 To ustawienie wstępne tworzy pojedynczy plik MP4 z szybkością transmisji bitów 4500 KB/s oraz stereo AAC audio. Aby uzyskać szczegółowe informacje na temat profilu, szybkości transmisji bitów, częstotliwości próbkowania itp., zapoznaj się z kodem XML lub JSON zdefiniowanym poniżej. Aby dowiedzieć się, co oznacza każdy element w tych ustawieniach wstępnych, i prawidłowe wartości dla każdego elementu, zobacz temat [schemat Media Encoder Standard](media-services-mes-schema.md) .  
  
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
          "Bitrate": 4500,  
          "MaxBitrate": 4500,  
          "BufferWindow": "00:00:05",  
          "Width": 1280,  
          "Height": 720,  
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
