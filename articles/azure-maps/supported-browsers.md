---
title: Obsługiwane przeglądarki sieci Web SDK | Mapy Microsoft Azure
description: Dowiedz się, jak sprawdzić, czy zestaw SDK sieci Web Azure Maps obsługuje przeglądarkę. Wyświetl listę obsługiwanych przeglądarek. Dowiedz się, jak używać usług map ze starszymi przeglądarkami.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 6321b96cb3db570102f138dcfd949d9c32daedbc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100384473"
---
# <a name="web-sdk-supported-browsers"></a>Przeglądarki obsługiwane przez zestaw Web SDK

Zestaw SDK sieci Web Azure Maps udostępnia funkcję pomocnika o nazwie [Atlas. Issupportd](/javascript/api/azure-maps-control/atlas#issupported-boolean-). Ta funkcja wykrywa, czy przeglądarka sieci Web ma minimalny zestaw funkcji WebGL wymaganych do obsługi ładowania i renderowania formantu mapy. Oto przykład sposobu korzystania z funkcji:

```JavaScript
if (!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if (!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    // Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>Komputer

Azure Maps Web SDK obsługuje następujące przeglądarki pulpitu:

- Microsoft Edge (bieżąca i Poprzednia wersja)
- Google Chrome (bieżąca i Poprzednia wersja)
- Mozilla Firefox (bieżąca i Poprzednia wersja)
- Apple Safari (macOS X) (bieżąca i Poprzednia wersja)

Zobacz też więcej niż [starsze przeglądarki](#Target-Legacy-Browsers) w dalszej części tego artykułu.

## <a name="mobile"></a>Aplikacje mobilne

Zestaw SDK sieci Web Azure Maps obsługuje następujące przeglądarki dla urządzeń przenośnych:

- Android
  - Bieżąca wersja programu Chrome w systemie Android 6,0 i nowszych
  - Przeglądarka Chrome WebView w systemie Android 6,0 i nowszych
- iOS
  - Mobilne przeglądarki Safari dotyczące bieżącej i wcześniejszej wersji głównej systemu iOS
  - UIWebView i WKWebView na bieżącą i poprzednią wersję główną systemu iOS
  - Bieżąca wersja programu Chrome dla systemu iOS

> [!TIP]
> Jeśli osadzasz mapę wewnątrz aplikacji mobilnej przy użyciu kontrolki WebView, możesz chcieć użyć [pakietu npm zestawu SDK sieci Web Azure Maps](https://www.npmjs.com/package/azure-maps-control) zamiast odwoływać się do wersji zestawu SDK hostowanego w usłudze Azure Content Delivery Network. Takie podejście skraca czas ładowania, ponieważ zestaw SDK znajduje się już na urządzeniu użytkownika i nie musi być pobierany w czasie wykonywania.

## <a name="nodejs"></a>Node.js

W Node.js są również obsługiwane następujące moduły zestawu SDK sieci Web:

- Module usług ([Dokumentacja](how-to-use-services-module.md)  |  [npm module](https://www.npmjs.com/package/azure-maps-rest))

## <a name="target-legacy-browsers"></a><a name="Target-Legacy-Browsers"></a>Docelowe starsze przeglądarki

Możesz chcieć użyć starszych przeglądarek, które nie obsługują WebGL lub które mają tylko ograniczoną obsługę. W takich przypadkach zalecamy używanie usług Azure Maps razem z kontrolką mapy typu "open source", taką jak [ulotka](https://leafletjs.com/). Oto przykład, który korzysta z [wtyczki ulotek Azure Maps](https://github.com/azure-samples/azure-maps-leaflet)typu open source.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Azure Maps + ulotka" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz pióro <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Azure Maps + ulotka</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dodatkowe przykłady kodu używające Azure Maps w ulotce można znaleźć [tutaj](https://azuremapscodesamples.azurewebsites.net/?search=leaflet).

[Poniżej](open-source-projects.md#third-part-map-control-plugins) przedstawiono popularne kontrolki mapy typu "open source", dla których zespół Azure Maps utworzył dla programu wtyczkę.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat Azure Maps Web SDK:

[Kontrolka mapy](how-to-use-map-control.md)

[Moduł usług](how-to-use-services-module.md)
