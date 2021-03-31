---
title: Opcje Azure Media Player
description: Azure Media Player kodu osadzania to po prostu tag wideo HTML5, więc dla wielu opcji można użyć standardowych atrybutów tagu, aby ustawić opcje.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: 392ecff2ab120e713a07d130493a4a8339dae5ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92366874"
---
# <a name="options"></a>Opcje #

## <a name="setting-options"></a>Opcje ustawienia ##

Azure Media Player kodu osadzania to po prostu tag wideo HTML5, więc dla wielu opcji można użyć standardowych atrybutów tagu, aby ustawić opcje.

`<video controls autoplay ...>`

Alternatywnie można użyć atrybutu konfiguracja danych w celu zapewnienia opcji w formacie [JSON](http://json.org/example.html) . Jest to również sposób ustawienia opcji, które nie są standardowe dla znacznika wideo.

`<video data-setup='{ "controls": true, "autoplay": false }'...>`

Na koniec, jeśli nie używasz atrybutu konfiguracja danych do wyzwalania konfiguracji odtwarzacza, możesz przekazać obiekt z opcjami odtwarzacza jako drugi argument funkcji konfiguracji JavaScript.

`amp("vid1", { "controls": true, "autoplay": false });`

> [!NOTE]
> Opcje w Konstruktorze są ustawiane dopiero po pierwszym inicjacji przed ustawieniem źródła.  Jeśli chcesz zmodyfikować opcje w tym samym zainicjowanym elemencie Azure Media Player, musisz zaktualizować opcje przed zmianą źródła. Opcje w języku JavaScript można zaktualizować za pomocą polecenia `myPlayer.options({/*updated options*/});` . Zwróć uwagę na to, że wszystkie zmienione opcje będą mieć wartość wszystkie pozostałe opcje zestawu zostaną zachowane.

## <a name="individual-options"></a>Opcje indywidualne ##

> [!NOTE]
>Atrybuty tagu wideo mogą mieć tylko wartość true lub false (Boolean), wystarczy po prostu dołączyć atrybut (bez znaku równości), aby go włączyć, lub wykluczyć go, aby go wyłączyć. Na przykład, aby włączyć kontrolki: niewłaściwy dla `<video controls="true" ...>` `<video controls ...>` największego problemu osoby, które uruchamiają, podejmuje próbę ustawienia wartości false przy użyciu false jako wartość (np. Controls = "false"), która faktycznie wykonuje przeciwieństwo i ustawia wartość true, ponieważ ten atrybut jest nadal uwzględniony.

### <a name="controls"></a>funkcje sterowania ###

Opcja formanty określa, czy odtwarzacz ma kontrolki, które użytkownik może obsługiwać. Bez sterowania jedynym sposobem uruchamiania odtwarzania wideo jest z atrybutem autoodtwarzania lub przez interfejs API.

`<video controls ...>` lub `{ "controls": true }`

### <a name="autoplay"></a>autoplay ###

Jeśli Autoodtwarzanie ma wartość true, wideo rozpocznie się po załadowaniu strony (bez interakcji z użytkownikiem).

> [!NOTE]
> Ta opcja nie jest obsługiwana przez urządzenia przenośne, takie jak Windows Phone, Apple iOS i Android. Urządzenia przenośne blokują funkcję autoodtwarzania, aby zapobiec korzystaniu z miesięcznych planów taryfowych danych użytkownika (często kosztownych). Aby można było uruchomić wideo, w tym przypadku jest wymagane dotknięcie lub kliknięcie.

`<video autoplay ...>`oraz `{ "autoplay": true }`

### <a name="poster"></a>ogłaszając ###
Atrybut plakatu ustawia obraz wyświetlany przed rozpoczęciem odtwarzania wideo. Jest to często ramka wideo lub ekran tytułu niestandardowego. Zaraz po kliknięciu przycisku Odtwórz obraz zostanie odsunięty.

`<video poster="myPoster.jpg" ...>` lub `{ "poster": "myPoster.jpg" }`

### <a name="width"></a>szerokość ###

Atrybut width ustawia szerokość ekranu wideo.

`<video width="640" ...>` lub `{ "width": 640 }`

### <a name="height"></a>wysokość ###

Atrybut Height Ustawia wysokość wyświetlania wideo.

`<video height="480" ...>` lub `{ "height": 480 }`

### <a name="plugins"></a>Dodatki ###

Plik JSON wtyczek określa, które wtyczki są ładowane z tym wystąpieniem AMP umożliwia skonfigurowanie wszelkich opcji, które mogą być dostępne dla wtyczki.

   `<video... data-setup='{plugins: { "contentTitle": {"name": "Azure Medi Services Overview"}}}'...>`

Aby uzyskać więcej informacji na temat programowania i użycia wtyczek, zobacz [pisanie wtyczek](azure-media-player-writing-plugins.md)

### <a name="other-options"></a>inne opcje ###

Inne opcje można ustawić dla znacznika przy `<video>` użyciu `data-setup` parametru, który pobiera kod JSON.
`<video ... data-setup='{"nativeControlsForTouch": false}'>`

#### <a name="nativecontrolsfortouch"></a>nativeControlsForTouch ####

Jest to jawnie ustawione na wartość false. Ustawienie na wartość false spowoduje, że karnacja Azure Media Player będzie renderowana na różnych platformach.  Ponadto w przeciwieństwie do nazwy funkcja touch będzie nadal włączona.

### <a name="fluid"></a>płynów ###

Ustawienie tej opcji na true elementu wideo spowoduje pełną szerokość kontenera nadrzędnego, a wysokość zostanie dopasowana do wideo przy użyciu standardowego współczynnika proporcji 16:9.

`<video ... data-setup='{"fluid": true}'>`

`fluid` Opcja przesłania jawne `width` i `height` Ustawienia. Ta opcja jest dostępna tylko w wersji Azure Media Player `2.0.0` i nowszych.

### <a name="playbackspeed"></a>playbackSpeed ###

`playbackSpeed` Opcja steruje kontrolką playbackSpeed i zestawem ustawień szybkości odtwarzania dostępnych dla użytkownika. `playbackSpeed` przyjmuje obiekt. Aby włączyć kontrolę szybkości odtwarzania na pasku sterowania, właściwość `enabled` obiektu musi być ustawiona na wartość true. Przykład włączania szybkości odtwarzania w znaczniku:

`<video ... data-setup='{"playbackSpeed": {"enabled": true}}'>`


Inne właściwości `playbackSpeed` Ustawienia są nadawane przez obiekt [PlaybackSpeedOptions](/javascript/api/azuremediaplayer/amp.player.playbackspeedoptions) .

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

Ta opcja jest dostępna tylko w Azure Media Player w wersji 2.0.0 lub nowszej.

### <a name="staledatatimelimitinsec"></a>staleDataTimeLimitInSec ###

`staleDataTimeLimitInSec`Opcja jest optymalizacją, która umożliwia skonfigurowanie liczby sekund, które mają być używane w buforach mediaSource. Ta opcja jest domyślnie wyłączona.

### <a name="cea708captionssettings"></a>cea708CaptionsSettings ###

Ustawienie wartości true umożliwia wyświetlanie na żywo napisów CEA w strumieniach na żywo i archiwów na żywo. Atrybut Label nie jest wymagany, jeśli nie został dołączony, gracz powróci do etykiety domyślnej.

```javascript
     cea708CaptionsSettings: {
                enabled: true,
                srclang: 'en',
                label: 'Live CC'
            }
```

Ta opcja jest dostępna tylko w Azure Media Player w wersji 2.1.1 i nowszych.

## <a name="next-steps"></a>Następne kroki ##

- [Azure Media Player — Szybki Start](azure-media-player-quickstart.md)
