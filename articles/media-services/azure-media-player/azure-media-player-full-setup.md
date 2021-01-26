---
title: Azure Media Player pełna konfiguracja
description: Dowiedz się, jak skonfigurować Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: b6b239f44311424db2e80c59e2aba639ae3c0000
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98797448"
---
# <a name="azure-media-player-full-setup"></a>Pełna konfiguracja usługi Azure Media Player #

Azure Media Player można łatwo skonfigurować. Uzyskanie podstawowego odtwarzania zawartości multimedialnej bezpośrednio z konta Azure Media Services trwa zaledwie kilka minut. [Przykłady](https://github.com/Azure-Samples/azure-media-player-samples) są również dostępne w katalogu przykładów wydania.

## <a name="step-1-include-the-javascript-and-css-files-in-the-head-of-your-page"></a>Krok 1. Uwzględnij pliki JavaScript i CSS w nagłówku strony ##

Za pomocą Azure Media Player można uzyskać dostęp do skryptów z hostowanej wersji usługi CDN. Często zalecamy teraz umieszczenie kodu JavaScript przed znacznikiem treści końcowej `<body>` zamiast `<head>` , ale usługa Azure Meia Player zawiera element "HTML5 Shiv", który musi znajdować się w sekcji głównej dla starszych wersji programu IE, aby poszanowania tagu wideo jako prawidłowego elementu.

> [!NOTE]
> Jeśli korzystasz już z Shiv HTML5, takiego jak [modernizacja](https://modernizr.com/) , możesz w dowolnym miejscu umieścić Azure Media Player JavaScript. Upewnij się jednak, że Twoja wersja programu unowocześnienie obejmuje Shiv do wideo.

### <a name="cdn-version"></a>Wersja usługi CDN ###

```html
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
```

> [!IMPORTANT]
> **Nie** należy używać `latest` wersji w środowisku produkcyjnym, ponieważ może to ulec zmianie na żądanie. Zamień `latest` na wersję Azure Media Player. Na przykład Zastąp ciąg `latest` opcją `2.1.1` . W [tym miejscu](azure-media-player-changelog.md)można wykonywać zapytania dotyczące wersji Azure Media Player.

> [!NOTE]
> Ponieważ `1.2.0` wersja nie jest już wymagana do uwzględnienia lokalizacji powrotu do rezerwy, (automatycznie wybiera lokalizację ze ścieżki względnej pliku azuremediaplayer.min.js). Lokalizację powrotu można zmienić, dodając następujący skrypt w `<head>` powyższym skrypcie.

> [!NOTE]
> Ze względu na charakter wtyczek Flash i Silverlight, pliki SWF i XAP powinny być hostowane w domenie bez poufnych informacji lub danych — jest to automatycznie brane pod uwagę w przypadku Azure CDN hostowanej wersji.

```javascript
    <script>
      amp.options.flashSS.swf = "//amp.azure.net/libs/amp/latest/techs/StrobeMediaPlayback.2.0.swf"
      amp.options.silverlightSS.xap = "//amp.azure.net/libs/amp/latest/techs/SmoothStreamingPlayer.xap"
    </script>
```

## <a name="step-2-add-an-html5-video-tag-to-your-page"></a>Krok 2. Dodawanie tagu wideo HTML5 do strony ##

Za pomocą Azure Media Player można osadzić wideo za pomocą tagu wideo HTML5. Azure Media Player odczytuje tag i sprawia, że będzie działać we wszystkich przeglądarkach, a nie tylko w przypadku obsługi wideo HTML5. Poza znacznikiem Basic Azure Media Player potrzebuje kilku dodatkowych fragmentów.

1. `<data-setup>`Atrybut na `<video>` stronie instruuje Azure Media Player, aby automatycznie skonfigurować wideo, gdy strona jest gotowa, i odczytać dowolne (w formacie JSON) z atrybutu.
1. `id`Atrybut: powinien być używany i unikatowy dla każdego filmu wideo na tej samej stronie.
1. Ten `class` atrybut zawiera dwie klasy:
    - `azuremediaplayer` stosuje style, które są wymagane do Azure Media Player funkcji interfejsu użytkownika
    - `amp-default-skin` stosuje domyślną skórkę do formantów HTML5
1. `<source>`Zawiera dwa wymagane atrybuty
    - `src` atrybut może zawierać plik **. ISM/manifest* z Azure Media Services zostanie dodany, Azure Media Player automatycznie dodaje adresy URL dla łącznika, gładkie i HLS do odtwarzacza
    - `type` atrybut jest wymaganym typem MIME strumienia. Typ MIME skojarzony z elementem *". ISM/manifest"* to *"application/vnd. MS-SStR + XML"*
1. *Opcjonalny* `<data-setup>` atrybut na stronie `<source>` informuje Azure Media Player, jeśli istnieją jakieś unikatowe zasady dostarczania dla strumienia z Azure Media Services, w tym, ale nie jest ograniczony do, typ szyfrowania (AES, PlayReady, Widevine lub FairPlay) i token.

Uwzględnij/Wyklucz atrybuty, ustawienia, źródła i ścieżki dokładnie tak samo, jak w przypadku wideo HTML5.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"], "nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

Domyślnie przycisk dużego odtwarzania znajduje się w lewym górnym rogu, aby nie odnosił się do interesujących części plakatu. Jeśli wolisz wyśrodkować duży przycisk odtwarzania, możesz dodać dodatkowy `amp-big-play-centered` `class` `<video>` element do elementu.

### <a name="alternative-setup-for-dynamically-loaded-html"></a>Alternatywna konfiguracja dynamicznego ładowania kodu HTML ###

Jeśli strona sieci Web lub aplikacja ładuje tag wideo dynamicznie (AJAX, appendChild itp.), dzięki czemu może nie istnieć podczas ładowania strony, warto ręcznie skonfigurować odtwarzacz zamiast korzystać z atrybutu konfiguracja danych. W tym celu należy najpierw usunąć atrybut konfiguracji danych ze znacznika, aby nie było żadnych pomyłek wokół momentu zainicjowania odtwarzacza. Następnie uruchom następujący kod JavaScript po załadowaniu Azure Media Player JavaScript i po załadowaniu znacznika wideo do modelu DOM.

```javascript
    var myPlayer = amp('vid1', { /* Options */
            techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"],
            "nativeControlsForTouch": false,
            autoplay: false,
            controls: true,
            width: "640",
            height: "400",
            poster: ""
        }, function() {
              console.log('Good to go!');
               // add an event listener
              this.addEventListener('ended', function() {
                console.log('Finished!');
            }
          }
    );
    myPlayer.src([{
        src: "http://samplescdn.origin.mediaservices.windows.net/e0e820ec-f6a2-4ea2-afe3-1eed4e06ab2c/AzureMediaServices_Overview.ism/manifest",
        type: "application/vnd.ms-sstr+xml"
    }]);
```

Pierwszy argument `amp` funkcji jest identyfikatorem tagu wideo. Zastąp ją własnym.

Drugi argument jest obiektem opcji. Pozwala to na ustawienie dodatkowych opcji, takich jak można z atrybutem konfiguracja danych.

Trzeci argument to wywołanie zwrotne "gotowe". Po zainicjowaniu Azure Media Player wywoła tę funkcję. W gotowym wywołaniu zwrotnym obiekt "This" odwołuje się do wystąpienia odtwarzacza.

Zamiast używać identyfikatora elementu, można również przekazać odwołanie do samego elementu.

```javascript

    amp(document.getElementById('example_video_1'), {/*Options*/}, function() {
        //This is functionally the same as the previous example.
    });
    myPlayer.src([{ src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml"]);
```

## <a name="next-steps"></a>Następne kroki ##

- [Azure Media Player — Szybki Start](azure-media-player-quickstart.md)