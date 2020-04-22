---
title: Odtwarzanie zawartości za pomocą istniejących odtwarzaczy — Azure | Dokumenty firmy Microsoft
description: W tym artykule wymieniono istniejących graczy, których można użyć do odtwarzania zawartości.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 7e9fcf89-0fb6-4fa4-96cb-666320684d69
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 2d3c22e17c37bc46c16a9cc80eb3cf4b9ec93ecf
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686922"
---
# <a name="playing-your-content-with-existing-players"></a>Odtwarzanie zawartości w istniejących odtwarzaczach
Usługa Azure Media Services obsługuje wiele popularnych formatów przesyłania strumieniowego, takich jak płynne przesyłanie strumieniowe, przesyłanie strumieniowe na żywo HTTP i mpeg-dash. W tym temacie wskazano istniejące odtwarzacze, których można użyć do testowania strumieni.

### <a name="the-azure-portal-media-services-content-player"></a>Odtwarzacz zawartości usługi Azure portal Media Services
**Portal Azure** udostępnia odtwarzacz zawartości, którego można użyć do testowania wideo.

Kliknij żądany film (upewnij się, że został [opublikowany)](media-services-portal-publish.md)i kliknij przycisk **Odtwórz** u dołu portalu.

Zagadnienia do rozważenia:

* **Odtwarzacz zawartości MEDIA SERVICES** odtwarza z domyślnego punktu końcowego przesyłania strumieniowego. Do odtwarzania z punktu końcowego przesyłania strumieniowego innego niż domyślny należy użyć innego odtwarzacza Na przykład [program Azure Media Player](https://aka.ms/azuremediaplayer).

![AMSPlayer][AMSPlayer]

### <a name="azure-media-player"></a>Azure Media Player

Użyj [programu Azure Media Player](https://aka.ms/azuremediaplayer) do odtwarzania zawartości (wyczyszczonej lub chronionej) w dowolnym z następujących formatów:

* Smooth Streaming
* MPEG DASH
* HLS
* Progresywny MP4

### <a name="flash-player"></a>Flash Player

#### <a name="playready-with-token"></a>PlayReady z tokenem

[https://sltoken.azurewebsites.net](https://sltoken.azurewebsites.net)

### <a name="dash-players"></a>Gracze DASH

[https://dashplayer.azurewebsites.net](https://dashplayer.azurewebsites.net)

[https://dashif.org](https://dashif.org)

### <a name="other"></a>Inne
Aby przetestować adresy URL HLS, można również użyć:

* **Safari** na urządzeniu z systemem iOS lub
* **3ivx HLS Player** w systemie Windows.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png
