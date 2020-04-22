---
title: Pełna konfiguracja programu Azure Media Player
description: Dowiedz się, jak skonfigurować program Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/20/2020
ms.openlocfilehash: d4c2dc58ca341db7ba17dbaf6a5ce7c009983379
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727244"
---
# <a name="azure-media-player-full-setup"></a>Pełna konfiguracja programu Azure Media Player #

Program Azure Media Player jest łatwy w konfiguracji. Wystarczy kilka chwil, aby uzyskać podstawowe odtwarzanie zawartości multimedialnej bezpośrednio z konta usługi Azure Media Services. [Przykłady](https://github.com/Azure-Samples/azure-media-player-samples) są również dostarczane w katalogu przykładów wydania.


## <a name="step-1-include-the-javascript-and-css-files-in-the-head-of-your-page"></a>Krok 1: Uwzględnij pliki JavaScript i CSS w głowicy strony ##

Za pomocą programu Azure Media Player można uzyskać dostęp do skryptów z wersji hosta cdn. Często zaleca się teraz umieścić JavaScript przed `<body>` tagiem treści `<head>`końcowej zamiast , ale Azure Media Player zawiera "HTML5 Shiv", który musi być w głowie dla starszych wersji IE, aby szanować tag wideo jako prawidłowy element.

> [!NOTE]
> Jeśli używasz już html5 shiv jak [Modernizr](http://modernizr.com/) można dołączyć Azure Media Player JavaScript w dowolnym miejscu. Upewnij się jednak, że twoja wersja Modernizr zawiera shiv do wideo.

### <a name="cdn-version"></a>Wersja CDN ###
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>

> [!IMPORTANT]
> **NIE** należy używać `latest` wersji w wersji produkcyjnej, ponieważ może to ulec zmianie na żądanie. Zamień `latest` na wersję programu Azure Media Player. Na przykład `latest` zastąp . `2.1.1` Wersje programu Azure Media Player można wyszukiwać [w tym miejscu](azure-media-player-changelog.md).

> [!NOTE]
> Od `1.2.0` czasu wydania nie jest już wymagane dołączanie lokalizacji do techników rezerwowych (automatycznie odbierze lokalizację ze ścieżki względnej pliku azuremediaplayer.min.js). Można zmodyfikować lokalizację techników rezerwowych, dodając następujący `<head>` skrypt w po po po powyższych skryptów.

> [!NOTE]
> Ze względu na charakter wtyczek Flash i Silverlight pliki swf i xap powinny być hostowane w domenie bez żadnych poufnych informacji lub danych — jest to automatycznie załatwiane za Ciebie w wersji hosta azure CDN.

```javascript
    <script>
      amp.options.flashSS.swf = "//amp.azure.net/libs/amp/latest/techs/StrobeMediaPlayback.2.0.swf"
      amp.options.silverlightSS.xap = "//amp.azure.net/libs/amp/latest/techs/SmoothStreamingPlayer.xap"
    </script>
```

## <a name="step-2-add-an-html5-video-tag-to-your-page"></a>Krok 2: Dodawanie tagu wideo HTML5 do strony ##

Za pomocą programu Azure Media Player można użyć tagu wideo HTML5, aby osadzić film wideo. Następnie program Azure Media Player odczyta tag i sprawi, że będzie działał we wszystkich przeglądarkach, a nie tylko w tych, które obsługują wideo HTML5. Poza podstawową marżą program Azure Media Player potrzebuje kilku dodatkowych elementów.

1. Atrybut `<data-setup>` w `<video>` programie Azure Media Player, aby automatycznie skonfigurować wideo, gdy strona jest gotowa i odczytać dowolny (w formacie JSON) z atrybutu.
1. Atrybut: `id` powinien być używany i unikatowy dla każdego filmu na tej samej stronie.
1. Atrybut `class` zawiera dwie klasy:
    - `azuremediaplayer`stosuje style wymagane dla funkcji interfejsu użytkownika programu Azure Media Player
    - `amp-default-skin`stosuje domyślną karnacji do formantów HTML5
1. Zawiera `<source>` dwa wymagane atrybuty
    - `src`atrybut może zawierać **.ism/manifest* plik z usługi Azure Media Services jest dodawany, Program Azure Media Player automatycznie dodaje adresy URL dash, SMOOTH i HLS do odtwarzacza
    - `type`jest wymaganym typem MIME strumienia. Typ MIME skojarzony z *".ism/manifest"* to *"application/vnd.ms-sstr+xml"*
1. *Atrybut opcjonalny* `<data-setup>` w `<source>` programie Azure Media Player informuje program Azure Media Player, jeśli istnieją unikatowe zasady dostarczania dla strumienia z usługi Azure Media Services, w tym między innymi typ szyfrowania (AES lub PlayReady, Widevine lub FairPlay) i token.

Uwzględnij/wyklucz atrybuty, ustawienia, źródła i ścieżki dokładnie tak, jak w przypadku wideo HTML5.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"], "nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

Domyślnie duży przycisk odtwarzania znajduje się w lewym górnym rogu, dzięki czemu nie zakrywa interesujących części plakatu. Jeśli wolisz wyśrodkować duży przycisk odtwarzania, `amp-big-play-centered` `class` możesz `<video>` dodać dodatkowy element.

### <a name="alternative-setup-for-dynamically-loaded-html"></a>Alternatywna konfiguracja dynamicznie ładowanego kodu HTML ###

Jeśli strona sieci Web lub aplikacja ładuje tag wideo dynamicznie (ajax, appendChild itp.), tak aby nie istniała podczas ładowania strony, należy ręcznie skonfigurować odtwarzacz zamiast polegać na atrybucie konfiguracji danych. Aby to zrobić, najpierw usuń atrybut konfiguracji danych z tagu, aby nie było żadnych nieporozumień podczas inicjowania odtwarzacza. Następnie uruchom następujący kod JavaScript jakiś czas po załadowaniu języka JavaScript programu Azure Media Player i po załadowaniu tagu wideo do modelu DOM.

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

Pierwszym argumentem `amp` w funkcji jest identyfikator tagu wideo. Wymień go na swój własny.

Drugi argument jest obiektem opcji. To pozwala ustawić dodatkowe opcje, takie jak można z atrybutem konfiguracji danych.

Trzeci argument to wywołanie zwrotne "ready". Po zainicjowaniu programu Azure Media Player wywoła tę funkcję. W gotowym wywołaniu zwrotnym obiekt "this" odnosi się do wystąpienia odtwarzacza.

Zamiast używać identyfikatora elementu, można również przekazać odwołanie do samego elementu.

```javascript

    amp(document.getElementById('example_video_1'), {/*Options*/}, function() {
        //This is functionally the same as the previous example.
    });
    myPlayer.src([{ src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml"]);
```

## <a name="next-steps"></a>Następne kroki ##

- [Szybki start programu Azure Media Player](azure-media-player-quickstart.md)