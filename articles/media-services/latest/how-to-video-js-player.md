---
title: Używanie odtwarzacza Video.js z Azure Media Services
description: W tym artykule wyjaśniono, jak używać obiektu wideo HTML i języka JavaScript z Azure Media Services
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
ms.openlocfilehash: a0b357705de04ed4c2be3223f9dd07f61f75d970
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954584"
---
# <a name="how-to-use-the-videojs-player-with-azure-media-services"></a>Jak używać odtwarzacza Video.js z Azure Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview"></a>Omówienie

Video.js to odtwarzacz wideo dla sieci Web zaprojektowany dla świata HTML5. Odtwarza formaty multimedialne (takie jak ŁĄCZNIKi i HLS) w przeglądarce, bez używania wtyczek ani Flash. Zamiast tego Video.js używa rozszerzeń Open Web Standards MediaSource i szyfrowanych rozszerzeń multimediów. Ponadto obsługuje odtwarzanie wideo na komputerach stacjonarnych i urządzeniach przenośnych.

Oficjalną dokumentację można znaleźć pod adresem [https://docs.videojs.com/](https://docs.videojs.com/) .

## <a name="sample-code"></a>Przykładowy kod
Przykładowy kod dla tego artykułu jest dostępny na [platformie Azure — przykłady/Media-Services-3rdparty-Player-Samples](https://github.com/Azure-Samples/media-services-3rdparty-player-samples).

## <a name="implement-the-player"></a>Implementowanie odtwarzacza

1. Utwórz `index.html` plik, który będzie hostem odtwarzacza. Dodaj następujące wiersze kodu (można zastąpić wersje w razie potrzeby nowsze):

    ```html
    <html>
      <head>
        <link href="https://vjs.zencdn.net/7.8.2/video-js.css" rel="stylesheet" />
      </head>
      <body>
        <video id="video" class="video-js vjs-default-skin vjs-16-9" controls data-setup="{}">
        </video>

        <script src="https://vjs.zencdn.net/7.8.2/video.js"></script>
        <script src="https://cdn.jsdelivr.net/npm/videojs-contrib-eme@3.7.0/dist/videojs-contrib-eme.min.js"></script>
        <script type="module" src="index.js"></script>
      </body>
    <html>
    ```

2. Dodaj `index.js` plik o następującym kodzie:

    ```javascript
    var videoJS = videojs("video");
    videoJS.src({
      src: "manifestUrl",
      type: "protocolType",
    });
    ```

3. Zamień na `manifestUrl` adres URL HLS lub pauzy z lokalizatora przesyłania strumieniowego zasobu, który można znaleźć na stronie lokalizatora przesyłania strumieniowego w Azure Portal.
    ![Adresy URL lokalizatora przesyłania strumieniowego](media/how-to-shaka-player/streaming-urls.png)

4. Zamień `protocolType` na następujące opcje:

    - "application/x-mpegURL" dla protokołów HLS
    - "Application/kreskowany + XML" dla protokołów KRESek

### <a name="set-up-captions"></a>Ustaw napisy

Uruchom `addRemoteTextTrack` metodę i Zastąp:

- `subtitleKind` z albo `"captions"` , `"subtitles"` , `"descriptions"` lub `"metadata"`  
- `caption` ze ścieżką pliku. VTT (plik VTT musi znajdować się na tym samym hoście, aby uniknąć błędu CORS)
- `subtitleLang` za pomocą kodu BCP 47 dla języka, na przykład `"eng"` w przypadku języka angielskiego lub `"es"` hiszpańskiego
- `subtitleLabel` z żądaną nazwą wyświetlaną podpisu

```javascript
videojs.players.video.addRemoteTextTrack({
  kind: subtitleKind,
  src: caption,
  srclang: subtitleLang,
  label: subtitleLabel
});
```

### <a name="set-up-token-authentication"></a>Konfigurowanie uwierzytelniania tokenu

Token musi być ustawiony w polu autoryzacji nagłówka żądania. Aby uniknąć problemów z mechanizmem CORS, token ten musi być ustawiony tylko w tych żądaniach `'keydeliver'` w adresie URL. Następujące wiersze kodu powinny wykonać prace:

```javascript
setupTokenForDecrypt (options) {
  if (options.uri.includes('keydeliver')) {
    options.headers = options.headers || {}
    options.headers.Authorization = 'Bearer=' + this.getInputToken()
  }

  return options
}
```

Następnie powyższa funkcja musi być dołączona do `videojs.Hls.xhr.beforeRequest` zdarzenia.

```javascript
videojs.Hls.xhr.beforeRequest = setupTokenForDecrypt;
```

### <a name="set-up-aes-128-encryption"></a>Konfigurowanie szyfrowania AES-128

Video.js obsługuje szyfrowanie AES-128 bez żadnej dodatkowej konfiguracji. 

> [!NOTE] 
> Obecnie występuje [problem](https://github.com/videojs/video.js/issues/6717) z szyfrowaniem i HLS/CMAF zawartością, które nie są naliczane.

### <a name="set-up-drm-protection"></a>Konfigurowanie ochrony DRM

Aby można było obsługiwać ochronę przy użyciu technologii DRM, należy dodać oficjalne rozszerzenie [videojs-contrib-EME](https://github.com/videojs/videojs-contrib-eme) . Również działa wersja sieci CDN.

1. W pliku opisanym `index.js` powyżej należy zainicjować rozszerzenie EME przez dodanie `videoJS.eme();` *przed* dodaniem źródła filmu:

   ```javascript
    videoJS.eme();
   ```

2. Teraz można zdefiniować adresy URL usług DRM i adresy URL odpowiednich licencji w następujący sposób:

   ```javascript
   videoJS.src({
       keySystems: {
           "com.microsoft.playready": "YOUR PLAYREADY LICENSE URL",
           "com.widevine.alpha": "YOUR WIDEVINE LICENSE URL",
           "com.apple.fps.1_0": {
            certificateUri: "YOUR FAIRPLAY CERTIFICATE URL",
            licenseUri: "YOUR FAIRPLAY LICENSE URL"
           }
         }
       })

   ```

#### <a name="acquiring-the-license-url"></a>Uzyskiwanie adresu URL licencji

Aby uzyskać adres URL licencji, możesz:

- Zapoznaj się z konfiguracją dostawcy DRM
- lub, jeśli używasz przykładu, zapoznaj się z `output.json` dokumentem wygenerowanym po uruchomieniu skryptu programu *setup-vod.ps1* PowerShell dla VODs lub skryptu *start-live.ps1* dla strumieni na żywo. Znajdziesz również dzieci w tym pliku.

#### <a name="using-tokenized-drm"></a>Korzystanie z tokenu DRM

Aby można było obsługiwać zamówione zabezpieczenia DRM, należy dodać następujący wiersz do `src` Właściwości odtwarzacza:

```javascript
videoJS.src({
src: ...,
emeHeaders: {'Authorization': "Bearer=" + "YOUR TOKEN"},
keySystems: {...
```

## <a name="next-steps"></a>Następne kroki

- [Użyj Azure Media Player](../azure-media-player/azure-media-player-overview.md)  
- [Szybki Start: szyfrowanie zawartości](encrypt-content-quickstart.md)
