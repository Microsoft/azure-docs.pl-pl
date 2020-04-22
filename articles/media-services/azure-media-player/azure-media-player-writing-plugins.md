---
title: Pisanie wtyczek do programu Azure Media Player
description: Dowiedz się, jak napisać wtyczkę za pomocą programu Azure Media Player w języku JavaScript
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/20/2020
ms.openlocfilehash: b7aac80b19a7c30d994f3c14e19047583d5334ac
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727114"
---
# <a name="writing-plugins-for-azure-media-player"></a>Pisanie wtyczek do programu Azure Media Player #

Wtyczka jest JavaScript napisany, aby rozszerzyć lub poprawić odtwarzacz. Można pisać wtyczki, które zmieniają wygląd programu Azure Media Player, jego funkcjonalność, a nawet mieć go interfejs z innymi usługami. Można to zrobić w dwóch prostych krokach:

## <a name="step-1"></a>Krok 1 ##

Napisz swój JavaScript w funkcji takiej jak tak:

```javascript

    (function () {
        amp.plugin('yourPluginName', function (options) {
        var myPlayer = this;
           myPlayer.addEventListener(amp.eventName.ready, function () {
        console.log("player is ready!");
            });
        });
    }).call(this);
```

Kod można zapisać bezpośrednio na stronie `<script>` HTML w tagach lub w zewnętrznym pliku JavaScript. Jeśli to zrobisz, pamiętaj, aby dołączyć plik `<head>` JavaScript do strony HTML *po* skrypcie AMP.

Przykład:

```javascript
    <!--*****START OF Azure Media Player Scripts*****-->
    <script src="//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <!--*****END OF Azure Media Player Scripts*****-->
    <!--Add Plugins-->
    <script src="yourPluginName.js"></script>
```

## <a name="step-2"></a>Krok 2 ##
Zasuń wtyczkę za pomocą języka JavaScript na jeden z dwóch sposobów:

Metoda 1.

```javascript
    var myOptions = {
        autoplay: true,
        controls: true,
        width: "640",
        height: "400",
        poster: "",
        plugins: {
            yourPluginName: {
                [your plugin options]: [example options]
           }
        }
    };     
    var myPlayer = amp([videotag id], myOptions);
```

Metoda 2.

```javascript
    var video = amp([videotag id]);
    video.yourPluginName({[your plugins option]: [example option]});
```

Opcje wtyczki nie są wymagane, w tym po prostu pozwala programistom za pomocą wtyczki, aby skonfigurować swoje zachowanie bez konieczności zmiany kodu źródłowego.

Inspiracje i więcej przykładów na temat tworzenia wtyczki zapoznaj się z naszą [galerią](azure-media-player-plugin-gallery.md)

>[!NOTE]
> Kod wtyczki dynamicznie zmienia elementy w DOM w okresie istnienia gracza widza, nigdy nie wprowadza trwałych zmian w kodzie źródłowym gracza. W tym miejscu przydaje się zrozumienie narzędzi programistycznych przeglądarki. Na przykład, jeśli chcesz zmienić wygląd elementu w odtwarzaczu można znaleźć jego element HTML według jego nazwy klasy, a następnie dodać lub zmienić atrybuty stamtąd. Oto świetny zasób na [temat zmiany atrybutów HTML.](http://www.w3schools.com/js/js_htmldom_html.asp)

### <a name="integrated-plugins"></a>Zintegrowane wtyczki ###

 Obecnie istnieją dwie wtyczki pieczone w AMP: [wskazówka czasu](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timetip/example.html) i [skróty klawiszowe](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/hotkeys/example.html). Wtyczki te zostały pierwotnie opracowane jako modułowe wtyczki dla gracza, ale są teraz zawarte w kodzie źródłowym odtwarzacza.

### <a name="plugin-gallery"></a>Galeria wtyczek ###

[Galeria wtyczek](http//:aka.ms/ampplugins) zawiera kilka wtyczek, które społeczność już przyczyniła się do takich funkcji, jak znaczniki linii czasowej, zoom, analityka i wiele innych. Strona zapewnia dostęp do wtyczek i instrukcji, jak go skonfigurować, a także demo, które pokazuje wtyczkę w akcji. Jeśli stworzysz fajną wtyczkę, która naszym zdaniem powinna zostać uwzględniona w naszej galerii, prosimy o przesłanie jej, abyśmy mogli to sprawdzić.

## <a name="next-steps"></a>Następne kroki ##

- [Szybki start programu Azure Media Player](azure-media-player-quickstart.md)