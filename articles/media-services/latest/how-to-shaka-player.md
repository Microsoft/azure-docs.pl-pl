---
title: Jak używać odtwarzacza Shaka z Azure Media Services
description: W tym artykule wyjaśniono, jak używać odtwarzacza Shaka z Azure Media Services
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: e4be3d4508d342c77507cf824fb036e32084b617
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91329741"
---
# <a name="how-to-use-the-shaka-player-with-azure-media-services"></a>Jak używać odtwarzacza Shaka z Azure Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview"></a>Omówienie

Shaka Player to biblioteka języka JavaScript Open Source na potrzeby adaptacyjnego nośnika. Odtwarza formaty multimedialne (takie jak ŁĄCZNIKi i HLS) w przeglądarce, bez używania wtyczek ani Flash. Zamiast tego program Shaka Player używa rozszerzeń Open Web Standards Source i szyfrowanych rozszerzeń multimediów.

Firma Microsoft zaleca używanie [Mux.js](https://github.com/videojs/mux.js/) jako bez tego, że odtwarzacz Shaka obsługuje format HLS CMAF, ale nie HLS TS.

Oficjalną dokumentację można znaleźć w [dokumentacji programu Shaka Player](https://shaka-player-demo.appspot.com/docs/api/tutorial-welcome.html).

## <a name="sample-code"></a>Przykładowy kod
Przykładowy kod dla tego artykułu jest dostępny na [platformie Azure — przykłady/Media-Services-3rdparty-Player-Samples](https://github.com/Azure-Samples/media-services-3rdparty-player-samples).

## <a name="implementing-the-player"></a>Implementowanie odtwarzacza

Wykonaj te instrukcje, jeśli musisz zaimplementować własne wystąpienie odtwarzacza:

1. Utwórz `index.html` plik, który będzie hostem odtwarzacza. Dodaj następujące wiersze kodu (można zastąpić wersje w razie potrzeby nowsze):

    ```html
    <html>
      <head>
        <script src="//cdn.jsdelivr.net/npm/shaka-player@3.0.1/dist/shaka-player.compiled.js"></script>
        <script src="//cdn.jsdelivr.net/npm/mux.js@5.6.3/dist/mux.js"></script>
        <script type="module" src="index.js"></script>
      </head>
      <body>
        <video id="video" controls></video>
      </body>
    </html>
    ```

1. Dodaj plik języka JavaScript o następującym kodzie:

    ```javascript
    // myScript.js
    shaka.polyfill.installAll();

    var video = document.getElementById('video');
    var player = new shaka.Player(video);
    window.player = player;

    var manifestUrl = 'https://amsplayeraccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/sample-vod.ism/manifest(format=m3u8-aapl)';
    player.load(manifestUrl);
    ```

1. Zamień na `manifestUrl` adres URL HLS lub pauzy z lokalizatora przesyłania strumieniowego zasobu, który można znaleźć na stronie lokalizatora przesyłania strumieniowego w Azure Portal.
    ![Adresy URL lokalizatora przesyłania strumieniowego](media/how-to-shaka-player/streaming-urls.png)

1. Uruchom serwer (na przykład z `npm http-server` ), a Twój gracz powinien działać...

## <a name="set-up-captions"></a>Ustaw napisy

### <a name="set-up-vod-captions"></a>Konfigurowanie podpisów VOD

Uruchom następujące wiersze kodu i zamień je na `captionUrl` katalog. VTT (plik VTT musi znajdować się na tym samym hoście, aby uniknąć błędu CORS), `lang` z dwuliterowym kodem dla języka i `type` z `caption` lub `subtitle` :

```javascript
player.configure('streaming.alwaysStreamText', true)
player.load(manifestUrl).then(function(){
        player.addTextTrack(captionUrl, lang, type, 'text/vtt');
        var tracks = player.getTextTracks();
        player.selectTextTrack(tracks[0]);
});
```

### <a name="set-up-live-stream-captions"></a>Skonfiguruj podpisy usługi Live Stream

Włączenie napisów w strumieniu na żywo jest konfigurowane przez dodanie następującego wiersza kodu:

```javascript
player.setTextTrackVisibility(true)
```

## <a name="set-up-token-authentication"></a>Konfigurowanie uwierzytelniania tokenu

Uruchom następujące wiersze kodu i Zastąp `token` ciąg tokenem:

```javascript
player.getNetworkingEngine().registerRequestFilter(function (type, request) {
  if (type === shaka.net.NetworkingEngine.RequestType.LICENSE) {
    request.headers['Authorization'] = 'Bearer ' + token;
  }
});
```

## <a name="set-up-aes-128-encryption"></a>Konfigurowanie szyfrowania AES-128

Program Shaka Player nie obsługuje obecnie szyfrowania AES-128.

Link do [problemu](https://github.com/google/shaka-player/issues/850) z usługą GitHub, który będzie zgodny z stanem tej funkcji.

## <a name="set-up-drm-protection"></a>Konfigurowanie ochrony DRM

Program Shaka Player korzysta z rozszerzeń EME (Encrypted Media Extensions), które wymagają bezpiecznego adresu URL. W celu przetestowania zawartości chronionej przez technologię DRM konieczne jest użycie protokołu HTTPS. Jeśli witryna korzysta z protokołu HTTPS, manifest i każdy segment również muszą używać protokołu HTTPS. Wynika to z wymagań dotyczących zawartości mieszanej.

Kolejność preferencji dla Shaka zarządzania adresami URL swoich serwerów licencji:

1. ClearKey config, używany do debugowania, powinien zastąpić wszystko inne. (Aplikacja może nadal określać serwer licencji ClearKey).
2. Serwery skonfigurowane dla aplikacji, jeśli są obecne, powinny zastąpić wszystkie elementy manifestu.
3. Serwery licencji udostępniane przez manifest są używane tylko wtedy, gdy nie określono żadnej innej.

Aby określić adres URL serwera licencji dla Widevine lub PlayReady, można użyć następującego kodu:

```javascript
player.configure({
  drm: {
    servers: {
      "com.widevine.alpha": "YOUR WIDEVINE LICENSE URL",
      "com.microsoft.playready": "YOUR PLAYREADY LICENSE URL"
    }
  }
});

```

Cała zawartość FairPlay wymaga ustawienia certyfikatu serwera. Jest ona ustawiana w konfiguracji odtwarzacza:

```javascript
const req = await fetch("YOUR FAIRPLAY CERTIFICATE URL");
const cert = await req.arrayBuffer();
player.configure('drm.advanced.com\\.apple\\.fps\\.1_0.serverCertificate', new Uint8Array(cert));
```

Aby uzyskać więcej informacji, zobacz [dokumentację ochrony DRM w programie Shaka Player](https://shaka-player-demo.appspot.com/docs/api/tutorial-drm-config.html).

## <a name="next-steps"></a>Następne kroki

* [Użyj Azure Media Player](../azure-media-player/azure-media-player-overview.md)
* [Szybki Start: szyfrowanie zawartości](encrypt-content-quickstart.md)
