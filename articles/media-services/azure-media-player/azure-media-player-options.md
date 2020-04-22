---
title: Opcje programu Azure Media Player
description: Kod osadzania programu Azure Media Player jest po prostu tagiem wideo HTML5, więc w przypadku wielu opcji można użyć standardowych atrybutów tagów, aby ustawić opcje.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: e26215115b4c4484e5e05a2fd94a4d2c6680a4d0
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727166"
---
# <a name="options"></a>Opcje #

## <a name="setting-options"></a>Opcje ustawienia ##

Kod osadzania programu Azure Media Player jest po prostu tagiem wideo HTML5, więc w przypadku wielu opcji można użyć standardowych atrybutów tagów, aby ustawić opcje.

`<video controls autoplay ...>`

Alternatywnie można użyć atrybutu konfiguracji danych, aby zapewnić opcje w formacie [JSON.](http://json.org/example.html) W ten sposób można ustawić opcje, które nie są standardowe dla tagu wideo.

`<video data-setup='{ "controls": true, "autoplay": false }'...>`

Jeśli nie używasz atrybutu konfiguracji danych do wyzwalania konfiguracji odtwarzacza, możesz przekazać w obiekcie opcje odtwarzacza jako drugi argument w funkcji konfiguracji JavaScript.

`amp("vid1", { "controls": true, "autoplay": false });`

> [!NOTE]
> Opcje w konstruktorze są ustawiane tylko przy pierwszym inicjowaniu przed ustawieniem źródła.  Jeśli chcesz zmodyfikować opcje na tym samym zainicjowany element programu Azure Media Player, należy zaktualizować opcje przed zmianą źródła. Opcje w języku JavaScript można `myPlayer.options({/*updated options*/});`zaktualizować za pomocą programu . Należy zauważyć, że będzie to miało wpływ tylko na zmienione opcje, wszystkie inne wcześniej ustawione opcje będą się powtarzać.

## <a name="individual-options"></a>Indywidualne opcje ##

> [!NOTE]
>Atrybuty tagu wideo mogą być tylko prawdziwe lub fałszywe (logiczne), wystarczy dołączyć atrybut (bez znaku równości), aby go włączyć, lub wykluczyć go, aby go wyłączyć. Na przykład, aby włączyć `<video controls="true" ...>` formanty: WRONG RIGHT `<video controls ...>` Największy problem, na który ludzie napotkali, próbuje ustawić te wartości na false przy użyciu false jako wartości (np. controls="false"), która faktycznie robi odwrotnie i ustawia wartość true, ponieważ atrybut jest nadal uwzględniany.

### <a name="controls"></a>funkcje sterowania ###

Opcja formanty określa, czy odtwarzacz ma formanty, z którymi użytkownik może wchodzić w interakcje. Bez kontroli jedynym sposobem rozpoczęcia odtwarzania wideo jest atrybut autoodtwarzania lub za pośrednictwem interfejsu API.

`<video controls ...>` lub `{ "controls": true }`

### <a name="autoplay"></a>autoplay ###

Jeśli autoodtwarzanie jest prawdziwe, film rozpocznie odtwarzanie zaraz po załadowaniu strony (bez żadnej interakcji z użytkownikiem).

> [!NOTE]
> Ta opcja nie jest obsługiwana przez urządzenia przenośne, takie jak Windows Phone, Apple iOS i Android. Urządzenia przenośne blokują funkcję autoodtwarzania, aby zapobiec wykorzystaniu miesięcznych planów transmisji danych (często drogich). Aby uruchomić film wideo, w takim przypadku wymagane jest dotknięcie/kliknięcie użytkownika.

`<video autoplay ...>`Lub`{ "autoplay": true }`

### <a name="poster"></a>Plakat ###
Atrybut plakatu ustawia obraz wyświetlany przed rozpoczęciem odtwarzania filmu. Często jest to klatka wideo lub niestandardowy ekran tytułowy. Jak tylko użytkownik kliknie odtworzyć obraz zniknie.

`<video poster="myPoster.jpg" ...>` lub `{ "poster": "myPoster.jpg" }`

### <a name="width"></a>szerokość ###

Atrybut szerokość ustawia szerokość wyświetlania wideo.

`<video width="640" ...>` lub `{ "width": 640 }`

### <a name="height"></a>height ###

Atrybut wysokość ustawia wysokość wyświetlania wideo.

`<video height="480" ...>` lub `{ "height": 480 }`

### <a name="plugins"></a>Wtyczki ###

Wtyczki JSON określa, które wtyczki się załadować z tego wystąpienia AMP pozwala skonfigurować wszelkie opcje, które wtyczka może mieć.

   `<video... data-setup='{plugins: { "contentTitle": {"name": "Azure Medi Services Overview"}}}'...>`

Aby uzyskać więcej informacji na temat tworzenia i używania wtyczek, zobacz [pisanie wtyczek](azure-media-player-writing-plugins.md)

### <a name="other-options"></a>inne opcje ###

Inne opcje można ustawić `<video>` na tagu przy użyciu parametru, `data-setup` który przyjmuje JSON.
`<video ... data-setup='{"nativeControlsForTouch": false}'>`

#### <a name="nativecontrolsfortouch"></a>nativeControlsForTouch ####

Jest to jawnie ustawione na false. Przez ustawienie false, pozwoli skórki programu Azure Media Player do renderowania takie same na różnych platformach.  Ponadto, w przeciwieństwie do nazwy, dotyk będzie nadal włączony.

### <a name="fluid"></a>Płynu ###

Ustawienie tej opcji na true video element zajmie pełną szerokość kontenera nadrzędnego i wysokość zostanie dostosowana do wideo o standardowym współczynniku proporcji 16:9.

`<video ... data-setup='{"fluid": true}'>`

`fluid`zastępuje jawne `width` i `height` ustawienia. Ta opcja jest dostępna tylko `2.0.0` w wersji programu Azure Media Player i nowszych wersjach.

### <a name="playbackspeed"></a>odtwarzaniePewno ###

`playbackSpeed`opcja steruje odtwarzaniemKontrola prędkości i zestaw ustawień prędkości odtwarzania dostępnych dla użytkownika. `playbackSpeed`przejmuje obiekt. Aby włączyć kontrolę prędkości odtwarzania na pasku `enabled` sterowania, właściwość obiektu musi być ustawiona na true. Przykład włączania szybkości odtwarzania w znacznikach:

`<video ... data-setup='{"playbackSpeed": {"enabled": true}}'>`


Inne właściwości `playbackSpeed` ustawienia są podane przez [PlaybackSpeedOptions](https://docs.microsoft.com/javascript/api/azuremediaplayer/amp.player.playbackspeedoptions) obiektu.

Przykład ustawiania opcji szybkości odtwarzania w języku JavaScript:

```javascript
    var myPlayer = amp('vid1', {
        fluid: true,
        playbackSpeed: {
            enabled: true,
            initialSpeed: 1.0,
            speedLevels: [
                { name: "x4.0", value: 4.0 },
                { name: "x3.0", value: 3.0 },
                { name: "x2.0", value: 2.0 },
                { name: "x1.75", value: 1.75 },
                { name: "x1.5", value: 1.5 },
                { name: "x1.25", value: 1.25 },
                { name: "normal", value: 1.0 },
                { name: "x0.75", value: 0.75 },
                { name: "x0.5", value: 0.5 },
            ]
        }
    });
```

Ta opcja jest dostępna tylko w usłudze Azure Media Player w wersji 2.0.0 i nowszej.

### <a name="staledatatimelimitinsec"></a>staleDataTimeLimitInSec ###

Opcja `staleDataTimeLimitInSec` jest optymalizacja, która pozwala skonfigurować, ile sekund warto starych danych, które chcesz zachować w buforach mediaSource. Ta opcja jest domyślnie wyłączona.

### <a name="cea708captionssettings"></a>cea708CaptionsSettings ###

Ustawienie true pozwala na wyświetlanie napisów CEA na żywo w transmisjach na żywo i archiwach na żywo. Atrybut etykiety nie jest wymagany, jeśli nie zostanie uwzględniony, odtwarzacz powróci do domyślnej etykiety.

```javascript
     cea708CaptionsSettings: {
                enabled: true,
                srclang: 'en',
                label: 'Live CC'
            }
```

Ta opcja jest dostępna tylko w usłudze Azure Media Player w wersji 2.1.1 i nowszej.

## <a name="next-steps"></a>Następne kroki ##

- [Szybki start programu Azure Media Player](azure-media-player-quickstart.md)