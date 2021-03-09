---
title: Odtwarzacze multimedialne dla Media Services — Omówienie
description: Których odtwarzaczy multimedialne można używać z Azure Media Services? Azure Media Player, Shaka i Video.js do tej pory.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 3/08/2021
ms.openlocfilehash: 1a1d415b374818d9a51c87e78e7ac422fa374bc5
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102509596"
---
# <a name="media-players-for-media-services"></a>Odtwarzacze multimedialne dla Media Services

Do Media Services można używać kilku graczy multimedialnych.

## <a name="azure-media-player"></a>Azure Media Player

Azure Media Player to odtwarzacz wideo obejmujący wiele różnych przeglądarek i urządzeń. Azure Media Player używa standardów branżowych, takich jak HTML5, rozszerzenia źródła nośników (MSE) i rozszerzeń zaszyfrowanej multimediów (EME), aby zapewnić wzbogacone, adaptacyjne środowisko przesyłania strumieniowego. Jeśli te standardy nie są dostępne na urządzeniu lub w przeglądarce, Azure Media Player używa programu Flash i Silverlight jako technologii rezerwowej. Niezależnie od używanej technologii odtwarzania deweloperzy mają ujednolicony interfejs JavaScript do uzyskiwania dostępu do interfejsów API. Zawartość obsługiwana przez Azure Media Services można odtwarzać na wielu urządzeniach i w różnych przeglądarkach bez dodatkowych nakładów pracy.

Zapoznaj się z [dokumentacją Azure Media Player](https://docs.microsoft.com/azure/media-services/azure-media-player/azure-media-player-overview).

## <a name="shaka"></a>Shaka

Shaka Player to biblioteka języka JavaScript Open Source na potrzeby adaptacyjnego nośnika. Odtwarza formaty multimedialne (takie jak ŁĄCZNIKi i HLS) w przeglądarce, bez używania wtyczek ani Flash. Zamiast tego program Shaka Player używa rozszerzeń Open Web Standards Source i szyfrowanych rozszerzeń multimediów.

Zobacz [, jak używać odtwarzacza Shaka z Azure Media Services](how-to-shaka-player.md).

## <a name="videojs"></a>Video.js

Video.js to odtwarzacz wideo, który odtwarza dostosowane formaty multimediów (takie jak ŁĄCZNIKi i HLS) w przeglądarce. Video.js używa rozszerzeń Open Web Standards MediaSource i szyfrowanych rozszerzeń multimediów. Obsługuje ona odtwarzanie wideo na komputerach stacjonarnych i urządzeniach przenośnych. Oficjalną dokumentację można znaleźć pod adresem https://docs.videojs.com/ .

Zobacz [, jak używać odtwarzacza Video.js z Azure Media Services](how-to-video-js-player.md).


## <a name="next-steps"></a>Następne kroki ##

- [Azure Media Player — Szybki Start](../azure-media-player/azure-media-player-quickstart.md)
