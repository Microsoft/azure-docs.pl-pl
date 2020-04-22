---
title: Szybki start programu Azure Media Player
description: Zapoznaj się z podstawowymi krokami konfigurowania programu Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: quickstart
ms.date: 04/20/2020
ms.openlocfilehash: ac81832765f674e58ad6b3213238e9c68e04d2dc
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726466"
---
# <a name="azure-media-player-quickstart"></a>Szybki start programu Azure Media Player
Program Azure Media Player jest łatwy w konfiguracji. Uzyskanie podstawowego odtwarzania zawartości multimedialnej z konta usługi Azure Media Services zajmuje tylko kilka minut. W tej sekcji omówiono podstawowe kroki bez przedstawiania szczegółów. Sekcje, które należy wykonać, podają szczegółowe informacje na temat konfigurowania i konfigurowania programu Azure Media Player.  Po prostu w dokumencie dodaj następujące dane do `<head>`:

```html
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
```

> [!IMPORTANT]
> **NIE** należy używać `latest` wersji w wersji produkcyjnej, ponieważ może to ulec zmianie na żądanie. Zamień `latest` na wersję programu Azure Media Player; na przykład `latest` `1.0.0`zastąp . Wersje programu Azure Media Player można wyszukiwać [w tym miejscu](azure-media-player-changelog.md).

## <a name="use-the-video-element"></a>Użyj elementu wideo

Następnie po prostu `<video>` użyj elementu, jak zwykle, `data-setup` ale z dodatkowym atrybutem zawierającym wszystkie opcje. Te opcje mogą obejmować dowolną opcję usługi Azure Media Services w prawidłowym obiekcie JSON.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

Jeśli nie chcesz używać automatycznej konfiguracji, możesz pominąć `data-setup` ten atrybut i zainicjować element wideo ręcznie.

```html
    var myPlayer = amp('vid1', { /* Options */
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
        src: "http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
        type: "application/vnd.ms-sstr+xml"
    }]);
```

## <a name="next-steps"></a>Następne kroki ##

- [Szybki start programu Azure Media Player](azure-media-player-quickstart.md)