---
title: Odtwarzacze multimedialne dla Media Services — Omówienie
description: Których odtwarzaczy multimedialne można używać z Azure Media Services? Azure Media Player, Shaka i Video.js do tej pory.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 3/08/2021
ms.openlocfilehash: 43d0a8ee82a84a57be7c8ded9e7f5afc172bd9d6
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106282407"
---
# <a name="media-players-for-media-services"></a>Odtwarzacze multimedialne dla Media Services

Do Media Services można używać kilku graczy multimedialnych.

## <a name="azure-media-player"></a>Azure Media Player

Azure Media Player to odtwarzacz wideo obejmujący wiele różnych przeglądarek i urządzeń. Azure Media Player używa standardów branżowych, takich jak HTML5, rozszerzenia źródła nośników (MSE) i rozszerzeń zaszyfrowanej multimediów (EME), aby zapewnić wzbogacone, adaptacyjne środowisko przesyłania strumieniowego. Jeśli te standardy nie są dostępne na urządzeniu lub w przeglądarce, Azure Media Player używa programu Flash i Silverlight jako technologii rezerwowej. Niezależnie od używanej technologii odtwarzania deweloperzy mają ujednolicony interfejs JavaScript do uzyskiwania dostępu do interfejsów API. Zawartość obsługiwana przez Azure Media Services można odtwarzać na wielu urządzeniach i w różnych przeglądarkach bez dodatkowych nakładów pracy.

Zapoznaj się z [dokumentacją Azure Media Player](../azure-media-player/azure-media-player-overview.md).

## <a name="shaka"></a>Shaka

Shaka Player to biblioteka języka JavaScript Open Source na potrzeby adaptacyjnego nośnika. Odtwarza formaty multimedialne (takie jak ŁĄCZNIKi i HLS) w przeglądarce, bez używania wtyczek ani Flash. Zamiast tego program Shaka Player używa rozszerzeń Open Web Standards Source i szyfrowanych rozszerzeń multimediów.

Zobacz [, jak używać odtwarzacza Shaka z Azure Media Services](player-shaka-player-how-to.md).

## <a name="videojs"></a>Video.js

Video.js to odtwarzacz wideo, który odtwarza dostosowane formaty multimediów (takie jak ŁĄCZNIKi i HLS) w przeglądarce. Video.js używa rozszerzeń Open Web Standards MediaSource i szyfrowanych rozszerzeń multimediów. Obsługuje ona odtwarzanie wideo na komputerach stacjonarnych i urządzeniach przenośnych. Oficjalną dokumentację można znaleźć pod adresem https://docs.videojs.com/ .

Zobacz [, jak używać odtwarzacza Video.js z Azure Media Services](player-how-to-video-js-player.md).


## <a name="next-steps"></a>Następne kroki ##

- [Azure Media Player — Szybki Start](../azure-media-player/azure-media-player-quickstart.md)