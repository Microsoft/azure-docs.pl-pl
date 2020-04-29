---
title: Transkrypcja na żywo
titleSuffix: Azure Media Services
description: Dowiedz się więcej na temat Azure Media Services transkrypcji na żywo.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: b364b6e70e3b5723c483bc3435f0c3a152c03aa9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79499875"
---
# <a name="live-transcription-preview"></a>Transkrypcja dynamiczna (wersja zapoznawcza)

Usługa Azure Media Service oferuje wideo, audio i tekst w różnych protokołach. Gdy publikujesz strumień na żywo przy użyciu formatu MPEG-KRESKowego lub HLS/CMAF, a następnie wideo i audio, Nasza usługa dostarcza tekst uzyskanego w IMSC 1.1 zgodne TTML. Dostarczenie jest spakowane w fragmenty MPEG-4 część 30 (ISO/IEC 14496-30). W przypadku używania dostarczania za pośrednictwem HLS/TS, tekst jest dostarczany jako podzielony VTT.

W tym artykule opisano sposób włączania transkrypcji na żywo podczas przesyłania strumieniowego zdarzenia na żywo z Azure Media Services v3. Przed kontynuowaniem upewnij się, że znasz już korzystanie z interfejsów API REST usługi Media Services v3 (zobacz [ten samouczek](stream-files-tutorial-with-rest.md) , aby uzyskać szczegółowe informacje). Należy również zapoznać się z koncepcją [przesyłania strumieniowego na żywo](live-streaming-overview.md) . Zaleca się ukończenie [przesyłania strumieniowego na żywo za pomocą](stream-live-tutorial-with-api.md) samouczka Media Services.

> [!NOTE]
> Obecnie transkrypcja na żywo jest dostępna tylko jako funkcja w wersji zapoznawczej w regionie zachodnie stany USA 2. Obsługuje transkrypcję wyrazów mówionych w języku angielskim do tekstu. Dokumentacja interfejsu API dla tej funkcji znajduje się poniżej — ponieważ w wersji zapoznawczej. szczegóły nie są dostępne w przypadku dokumentów REST.

## <a name="creating-the-live-event"></a>Tworzenie wydarzenia na żywo

Aby utworzyć wydarzenie na żywo, Wyślij operację PUT do wersji 2019-05-01-Preview, na przykład:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview&autoStart=true 
```

Operacja ma następującą treść (w której jest tworzone zdarzenie typu pass-through na żywo z protokołem RTMP jako protokół pozyskiwania). Zwróć uwagę na dodanie właściwości transkrypcji. Jedyną dozwoloną wartością dla języka jest EN-US.

```
{ 
  "properties": { 
    "description": "Demonstrate how to enable live transcriptions", 
    "input": { 
      "streamingProtocol": "RTMP", 
      "accessControl": { 
        "ip": { 
          "allow": [ 
            { 
              "name": "Allow All", 
              "address": "0.0.0.0", 
              "subnetPrefixLength": 0 
            } 
          ] 
        } 
      } 
    }, 
    "preview": { 
      "accessControl": { 
        "ip": { 
          "allow": [ 
            { 
              "name": "Allow All", 
              "address": "0.0.0.0", 
              "subnetPrefixLength": 0 
            } 
          ] 
        } 
      } 
    }, 
    "encoding": { 
      "encodingType": "None" 
    }, 
    "transcriptions": [ 
      { 
        "language": "en-US" 
      } 
    ], 
    "vanityUrl": false, 
    "streamOptions": [ 
      "Default" 
    ] 
  }, 
  "location": "West US 2" 
} 
```

Sprawdź stan zdarzenia na żywo do momentu, gdy przejdzie ono w stan "uruchomiona", co oznacza, że możesz teraz wysyłać źródło danych RTMP. Teraz można wykonać te same czynności jak w tym samouczku, takie jak sprawdzanie kanału informacyjnego w wersji zapoznawczej i tworzenie danych wyjściowych na żywo.

## <a name="transcription-delivery-and-playback"></a>Dostarczanie i odtwarzanie transkrypcji

Zapoznaj się z artykułem [Omówienie pakietów dynamicznych](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery) , w jaki sposób usługa używa pakietu dynamicznego do dostarczania wideo, audio i tekstu w różnych protokołach. Gdy publikujesz strumień na żywo przy użyciu formatu MPEG-KRESKowego lub HLS/CMAF, a następnie wideo i audio, Nasza usługa dostarcza tekst uzyskanego w IMSC 1.1 zgodne TTML. To dostarczanie jest opakowane na fragmenty w formacie MPEG-4 część 30 (ISO/IEC 14496-30). W przypadku używania dostarczania za pośrednictwem HLS/TS tekst jest dostarczany jako podzielony VTT. Aby odtworzyć strumień, można użyć odtwarzacza sieci Web, takiego jak [Azure Media Player](use-azure-media-player.md) .  

> [!NOTE]
> Jeśli używasz Azure Media Player, użyj wersji 2.3.3 lub nowszej.

## <a name="known-issues"></a>Znane problemy

W przypadku wersji zapoznawczej poniżej przedstawiono znane problemy związane z transkrypcją na żywo:

* Ta funkcja jest dostępna tylko w regionie zachodnie stany USA 2.
* Aplikacje muszą używać interfejsów API w wersji zapoznawczej, opisanych [Media Services w specyfikacji openapi v3](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json).
* Jedynym obsługiwanym językiem jest angielski (EN-US).
* W przypadku ochrony zawartości obsługiwane jest tylko szyfrowanie kopert AES.

## <a name="next-steps"></a>Następne kroki

* [Przegląd Media Services](media-services-overview.md)
