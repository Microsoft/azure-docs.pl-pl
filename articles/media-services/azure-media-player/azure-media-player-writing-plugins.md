---
title: Pisanie wtyczek dla Azure Media Player
description: Dowiedz się, jak napisać wtyczkę z Azure Media Player przy użyciu języka JavaScript
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: c1a64bff8b81735d9c4c9a14d2c1e12bd0bfe57e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91296165"
---
# <a name="writing-plugins-for-azure-media-player"></a>Pisanie wtyczek dla Azure Media Player #

Wtyczka jest zapisywana w języku JavaScript w celu rozszerzenia lub udoskonalenia odtwarzacza. Można napisać wtyczki, które zmieniają wygląd Azure Media Player, jego funkcje lub nawet mają interfejs IT z innymi usługami. Można to zrobić w dwóch prostych krokach:

## <a name="step-1"></a>Krok 1 ##

Napisz JavaScript w taki sposób, aby:

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

Kod można napisać bezpośrednio na stronie HTML w `<script>` tagach lub w zewnętrznym pliku JavaScript. Jeśli to zrobisz, pamiętaj o uwzględnieniu pliku JavaScript na `<head>` stronie HTML *po* skrypcie amp.

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
Zainicjuj wtyczkę przy użyciu języka JavaScript na jeden z dwóch sposobów:

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

Opcje wtyczki nie są wymagane, w tym ich po prostu umożliwiają deweloperom korzystającym z wtyczki skonfigurowanie jego zachowania bez konieczności zmiany kodu źródłowego.

Aby uzyskać inspiracje i więcej przykładów na temat tworzenia wtyczki, zapoznaj się z naszą [galerią](azure-media-player-plugin-gallery.md)

>[!NOTE]
> Kod wtyczki dynamicznie zmienia elementy w modelu DOM w czasie trwania środowiska odtwarzacza przeglądarki, nigdy nie wprowadza trwałych zmian w kodzie źródłowym odtwarzacza. Jest to miejsce, w którym zrozumienie narzędzi programistycznych przeglądarki jest przydatne. Na przykład jeśli chcesz zmienić wygląd elementu w odtwarzaczu, możesz znaleźć jego element HTML według nazwy klasy, a następnie dodać lub zmienić atrybuty. Oto doskonałe zasoby dotyczące [zmieniania atrybutów HTML.](http://www.w3schools.com/js/js_htmldom_html.asp)

### <a name="integrated-plugins"></a>Zintegrowane wtyczki ###

 Obecnie istnieją dwa wtyczki rozszerzania do AMP: [etykietka czasowa](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timetip/example.html) i [klawisze](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/hotkeys/example.html)dostępu. Te wtyczki zostały pierwotnie opracowane jako wtyczki modularne odtwarzacza, ale są teraz dołączone do kodu źródłowego odtwarzacza.

### <a name="plugin-gallery"></a>Galeria wtyczek ###

[Galeria wtyczek](https://aka.ms/ampplugins) zawiera kilka wtyczek, które zostały już utworzone przez społeczność dla funkcji, takich jak znaczniki wiersza czasu, powiększanie, analiza i nie tylko. Strona zapewnia dostęp do wtyczek i instrukcje dotyczące sposobu ich konfigurowania oraz pokazu, który pokazuje wtyczkę w działaniu. Jeśli utworzysz chłodną wtyczkę, którą uważasz, że powinna zostać uwzględniona w naszej galerii, możesz ją przesłać, aby ją wyewidencjonować.

## <a name="next-steps"></a>Następne kroki ##

- [Azure Media Player — Szybki Start](azure-media-player-quickstart.md)
