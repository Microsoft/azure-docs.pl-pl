---
title: Azure Media Player — Szybki Start
description: Zapoznaj się z podstawowymi krokami w celu skonfigurowania Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: quickstart
ms.date: 04/20/2020
ms.openlocfilehash: 1634731381be6ebbb8750746f88b8ff9e761025d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98879194"
---
# <a name="azure-media-player-quickstart"></a>Szybki start: usługa Azure Media Player
Azure Media Player można łatwo skonfigurować. Uzyskanie podstawowego odtwarzania zawartości multimedialnej z konta Azure Media Services trwa zaledwie kilka minut. W tej sekcji omówiono podstawowe kroki bez przedstawiania szczegółów. Poniższe sekcje zawierają szczegółowe informacje na temat konfigurowania i konfigurowania Azure Media Player.  Po prostu w dokumencie dodaj następujące dane do `<head>`:

```html
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
```

> [!IMPORTANT]
> **Nie** należy używać `latest` wersji w środowisku produkcyjnym, ponieważ może to ulec zmianie na żądanie. Zamień na `latest` wersję Azure Media Player; na przykład Zastąp ciąg `latest` opcją `1.0.0` . W [tym miejscu](azure-media-player-changelog.md)można wykonywać zapytania dotyczące wersji Azure Media Player.

## <a name="use-the-video-element"></a>Korzystanie z elementu wideo

Następnie wystarczy użyć `<video>` elementu w zwykły sposób, ale z dodatkowym `data-setup` atrybutem zawierającym wszelkie opcje. Te opcje mogą zawierać dowolną opcję Azure Media Services w prawidłowym obiekcie JSON.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

Jeśli nie chcesz używać funkcji autoinstalacji, możesz pominąć `data-setup` atrybut i ręcznie zainicjować element wideo.

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

- [Azure Media Player pełna konfiguracja](./azure-media-player-full-setup.md)