---
title: Azure Media Player ponownej edycji adresu URL
description: Azure Media Player zapisze dany adres URL z Azure Media Services, aby dostarczyć strumienie dla GŁADKIch, ŁĄCZNIKów, HLS v3 i HLS v4.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: b037eec13fda0b5ec16a4f2f53ad2a64fb5f8da1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91275561"
---
# <a name="url-rewriter"></a>Ponowny zapis adresu URL #

Domyślnie program Azure Media Player ponownie zapisuje dany adres URL z Azure Media Services, aby dostarczyć strumienie dla GŁADKIch, PAUZ, HLS v3 i HLS v4. Na przykład, jeśli źródło jest określone w następujący sposób, Azure Media Player sprawdzi, czy podejmie próbę odtworzenia wszystkich powyższych protokołów:

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" />
    </video>
```

Jeśli jednak chcesz nie używać funkcji ponownego zapisywania adresów URL, możesz to zrobić, dodając `disableUrlRewriter` Właściwość do parametru. Oznacza to, że wszystkie informacje przesyłane do źródeł są przesyłane bezpośrednio do odtwarzacza bez modyfikacji.  Oto przykład dodawania dwóch źródeł do odtwarzacza przy użyciu ŁĄCZNIKa i jednego przesyłania strumieniowego.

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

Ponadto, jeśli chcesz, możesz określić określone formaty przesyłania strumieniowego, które mają być Azure Media Player do ponownego zapisu przy użyciu `streamingFormats` parametru. Opcje obejmują `DASH` , `SMOOTH` , `HLSv3` , `HLSv4` , `HLS` . Różnica między HLS i HLSv3 & v4 polega na tym, że format HLS obsługuje odtwarzanie zawartości FairPlay. V3 i v4 nie obsługują FairPlay. Jest to przydatne, jeśli nie masz zasad dostarczania dla określonego dostępnego protokołu.  Oto przykład sytuacji, w której nie włączono protokołu ŁĄCZNIKa z elementem zawartości.

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

Powyższe dwa mogą być używane w połączeniu ze sobą w wielu przypadkach w zależności od danego elementu zawartości.

> [!NOTE]
> Informacje o ochronie Widevine są utrwalane tylko w protokole PAUZy.

## <a name="next-steps"></a>Następne kroki ##

- [Azure Media Player — Szybki Start](azure-media-player-quickstart.md)