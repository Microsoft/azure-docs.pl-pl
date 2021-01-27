---
title: Ustawienia małych opóźnień LiveEvent w Azure Media Services
description: Ten temat zawiera omówienie ustawień małych opóźnień LiveEvent i pokazuje, jak ustawić małe opóźnienia.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 023b0f4d7f0367882e0a5bb2be89c485c18bc03c
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897836"
---
# <a name="live-event-low-latency-settings"></a>Ustawienia niskiego opóźnienia zdarzenia na żywo

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

W tym artykule przedstawiono sposób ustawiania małych opóźnień dla [zdarzenia na żywo](/rest/api/media/liveevents). Omówiono w nim również typowe wyniki, które są wyświetlane podczas korzystania z ustawień małych opóźnień w różnych graczy. Wyniki różnią się w zależności od czasu oczekiwania na sieć CDN i sieci.

Aby użyć nowej funkcji **LowLatency** , należy ustawić **StreamOptionsFlag** na **LowLatency** na **LiveEvent**. Podczas tworzenia [LiveOutput](/rest/api/media/liveoutputs) na potrzeby odtwarzania HLS Ustaw [LiveOutput. HLS. fragmentsPerTsSegment](/rest/api/media/liveoutputs/create#hls) na 1. Gdy strumień jest uruchomiony, możesz użyć [Azure Media Player](https://ampdemo.azureedge.net/) (strona Demonstracja amp) i ustawić opcje odtwarzania, aby użyć "profilu heurystycznego o małym opóźnieniu".

> [!NOTE]
> Obecnie LowLatency HeuristicProfile w Azure Media Player jest przeznaczony do odtwarzania strumieni w protokole MPEG-KRESKowego przy użyciu formatu CSF lub CMAF (na przykład `format=mdp-time-csf` lub `format=mdp-time-cmaf` ). 

Poniższy przykład .NET pokazuje, jak ustawić **LowLatency** na **LiveEvent**:

```csharp
LiveEvent liveEvent = new LiveEvent(
            location: mediaService.Location, 
            description: "Sample LiveEvent for testing",
            vanityUrl: false,
            encoding: new LiveEventEncoding(
                        // Set this to Standard to enable a transcoding LiveEvent, and None to enable a pass-through LiveEvent
                        encodingType:LiveEventEncodingType.None, 
                        presetName:null
                    ),
            input: new LiveEventInput(LiveEventInputProtocol.RTMP,liveEventInputAccess), 
            preview: liveEventPreview,
            streamOptions: new List<StreamOptionsFlag?>()
            {
                // Set this to Default or Low Latency
                // To use low latency optimally, you should tune your encoder settings down to 1 second "Group Of Pictures" (GOP) length instead of 2 seconds.
                StreamOptionsFlag.LowLatency
            }
        );
```                

Zobacz pełny przykład: [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

## <a name="live-events-latency"></a>Opóźnienie zdarzeń na żywo

W poniższych tabelach przedstawiono typowe wyniki opóźnienia (gdy flaga LowLatency jest włączona) w Media Services mierzony od momentu, gdy w przeglądarce jest widoczne odtwarzanie w odtwarzaczu. Aby optymalnie korzystać z małych opóźnień, należy dostosować ustawienia kodera do 1 sekundy długości "grupy obrazów" (grupę GOP). W przypadku używania większej grupę GOP długości można zminimalizować użycie przepustowości i zmniejszyć szybkość transmisji bitów w ramach tej samej szybkości klatek. Jest to szczególnie przydatne w przypadku wideo z mniejszym ruchem.

### <a name="pass-through"></a>Przekazywanie 

||2 grupę GOP małe opóźnienia włączone|1 grupę GOP małe opóźnienia włączone|
|---|---|---|
|**KRESKa w AMP**|dziesiątkach|8S|
|**HLS na natywnym odtwarzaczu systemu iOS**|14s|dziesiątkach|

### <a name="live-encoding"></a>Kodowanie na żywo

||2 grupę GOP małe opóźnienia włączone|1 grupę GOP małe opóźnienia włączone|
|---|---|---|
|**KRESKa w AMP**|14s|dziesiątkach|
|**HLS na natywnym odtwarzaczu systemu iOS**|18s|13s|

> [!NOTE]
> Opóźnienie end-to-end może się różnić w zależności od warunków sieci lokalnej lub przez wprowadzenie warstwy buforowania sieci CDN. Należy przetestować dokładne konfiguracje.

## <a name="next-steps"></a>Następne kroki

- [Omówienie przesyłania strumieniowego na żywo](live-streaming-overview.md)
- [Samouczek przesyłania strumieniowego na żywo](stream-live-tutorial-with-api.md)
