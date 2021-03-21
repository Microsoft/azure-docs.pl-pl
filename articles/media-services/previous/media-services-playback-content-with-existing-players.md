---
title: Korzystanie z istniejących odtwarzaczy do odtwarzania zawartości — Azure | Microsoft Docs
description: Ten artykuł zawiera listę istniejących odtwarzaczy, których można użyć do odtwarzania zawartości.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 7e9fcf89-0fb6-4fa4-96cb-666320684d69
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: ce773adace1baf6db8f1b747643ef0ffdc56a97c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103008295"
---
# <a name="playing-your-content-with-existing-players"></a>Odtwarzanie zawartości w istniejących odtwarzaczach

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Azure Media Services obsługuje wiele popularnych formatów przesyłania strumieniowego, takich jak Smooth Streaming, HTTP Live Streaming i MPEG-kreska. Ten temat prowadzi do istniejących graczy, których można użyć do testowania strumieni.

## <a name="the-azure-portal-media-services-content-player"></a>Azure Portal Media Services odtwarzacz zawartości

Witryna **Azure** Portal udostępnia odtwarzacz zawartości, którego można użyć do testowania wideo.

Kliknij żądany film wideo (Upewnij się, że został [opublikowany](media-services-portal-publish.md)), a następnie kliknij przycisk **Odtwórz** w dolnej części portalu.

Zagadnienia do rozważenia:

* **Odtwarzacz zawartości MEDIA SERVICES** odtwarza z domyślnego punktu końcowego przesyłania strumieniowego. Do odtwarzania z punktu końcowego przesyłania strumieniowego innego niż domyślny należy użyć innego odtwarzacza Na przykład [Azure Media Player](https://aka.ms/azuremediaplayer).

### <a name="azure-media-player"></a>Azure Media Player

Użyj [Azure Media Player](https://aka.ms/azuremediaplayer) do odtwarzania zawartości (wyczyść lub chroniona) w jednym z następujących formatów:

* Smooth Streaming
* MPEG DASH
* HLS
* Pliki MP4 progresywne

### <a name="flash-player"></a>Program Flash Player

#### <a name="playready-with-token"></a>PlayReady z tokenem

[http://reference.dashif.org/dash.js/nightly/samples/dash-if-reference-player/index.html](http://reference.dashif.org/dash.js/nightly/samples/dash-if-reference-player/index.html)

### <a name="dash-players"></a>Odtwarzacze PAUZ

[odtwarzacz kreskowany](http://players.akamai.com/players/dashjs)

[https://dashif.org](https://dashif.org)

### <a name="other"></a>Inne

Aby przetestować adresy URL HLS, można również użyć:

* **Przeglądarka Safari** na urządzeniu z systemem iOS lub
* **3ivx HLS Player** w systemie Windows.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
