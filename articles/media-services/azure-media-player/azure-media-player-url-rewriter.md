---
title: Nagrywarka adresu URL programu Azure Media Player
description: Program Azure Media Player przepisze podany adres URL z usługi Azure Media Services, aby zapewnić strumienie dla smooth, DASH, HLS v3 i HLS v4.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: f238a2a3c499cf1e36f5e7c40e087375b7db0a70
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726459"
---
# <a name="url-rewriter"></a>Nagrywarka adresów URL #

Domyślnie program Azure Media Player przepisze podany adres URL z usługi Azure Media Services w celu zapewnienia strumieni dla smooth, DASH, HLS v3 i HLS v4. Na przykład jeśli źródło jest podane w następujący sposób, Program Azure Media Player zapewni, że próbuje odtworzyć wszystkie powyższe protokoły:

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" />
    </video>
```

Jednak jeśli nie chcesz używać nagrywarki adresu URL, możesz `disableUrlRewriter` to zrobić, dodając właściwość do parametru. Oznacza to, że wszystkie informacje przekazywane do źródeł są bezpośrednio przekazywane graczowi bez modyfikacji.  Oto przykład dodawania dwóch źródeł do odtwarzacza, na DASH i jeden SMOOTH Streaming.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest(format=mpd-time-csf)" type="application/dash+xml" data-setup='{"disableUrlRewriter": true}'/>
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" data-setup='{"disableUrlRewriter": true}'/>
    </video>
```

lub

```javascript
    myPlayer.src([
        { src: "//example/path/to/myVideo.ism/manifest(format=mpd-time-csf)", type: "application/dash+xml", disableUrlRewriter: true },
        { src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml", disableUrlRewriter: true }
    ]);
```

Ponadto jeśli chcesz, można określić określone formaty przesyłania strumieniowego, które chcesz `streamingFormats` azure media player do ponownego przeredagowania przy użyciu parametru. Dostępne `DASH`opcje `SMOOTH` `HLSv3`obejmują `HLSv4` `HLS`, , , . Różnica między HLS i HLSv3 & v4 polega na tym, że format HLS obsługuje odtwarzanie zawartości FairPlay. v3 i v4 nie obsługują FairPlay. Jest to przydatne, jeśli nie masz dostępnych zasad dostarczania dla określonego protokołu.  Oto przykład, gdy protokół DASH nie jest włączony z zasobem.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" data-setup='{"streamingFormats": ["SMOOTH", "HLS","HLS-V3", "HLS-V4"] }'/>
    </video>
```

lub

```javascript
    myPlayer.src([
        { src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml", streamingFormats: ["SMOOTH", "HLS","HLS-V3", "HLS-V4"]},
    ]);
```

Powyższe dwa mogą być używane w połączeniu ze sobą w wielu okolicznościach na podstawie danego zasobu.

> [!NOTE]
> Informacje o ochronie widevine są zachowywane tylko w protokole DASH.

## <a name="next-steps"></a>Następne kroki ##

- [Szybki start programu Azure Media Player](azure-media-player-quickstart.md)