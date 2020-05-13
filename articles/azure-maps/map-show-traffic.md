---
title: Pokaż ruch na mapie | Mapy Microsoft Azure
description: W tym artykule dowiesz się, jak wyświetlać dane o ruchu na mapie przy użyciu zestawu Microsoft Azure Web SDK Maps.
author: Philmea
ms.author: philmea
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 9c17c3cc22d478d81ed3c2b2ae9f61c173aad6cd
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83123925"
---
# <a name="show-traffic-on-the-map"></a>Pokaż ruch na mapie

Istnieją dwa typy danych ruchu dostępne w Azure Maps:

- Dane zdarzenia — składa się z punktów i danych opartych na wierszu dla elementów, takich jak konstrukcja, zamknięcie dróg i awarie.
- Dane przepływu — dostarcza metryki przepływu ruchu na drogach. Często dane przepływu ruchu są używane do kolorowania dróg. Kolory są zależne od tego, jaki ruch zmniejsza przepływ w stosunku do limitu szybkości lub innej metryki. Dane przepływu ruchu w Azure Maps mają trzy różne metryki pomiaru:
    - `relative`-jest względem prędkości swobodnego przepływu drogi.
    - `absolute`-jest bezwzględną szybkością wszystkich pojazdów w podróży.
    - `relative-delay`-Wyświetla obszary, które są wolniejsze niż średnie oczekiwane opóźnienie.

Poniższy kod przedstawia sposób wyświetlania danych o ruchu na mapie.

```javascript
//Show traffic on the map using the traffic options.
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

Poniżej znajduje się kompletny przykładowy kod wykonywany z powyższymi funkcjami.

<br/>

<iframe height='500' scrolling='no' title='Pokaż ruch na mapie' src='//codepen.io/azuremaps/embed/WMLRPw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>Pokaż ruch na mapie</a> według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="traffic-overlay-options"></a>Opcje nakładki ruchu

Poniższe narzędzie umożliwia przełączenie między różnymi ustawieniami nakładki ruchu, aby zobaczyć, jak zmienia się renderowanie. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opcje nakładki ruchu" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>opcje nakładki ruchu</a> piórem według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) w witrynie <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [TrafficOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.trafficoptions)

Ulepsz środowisko użytkownika:

> [!div class="nextstepaction"]
> [Interakcja mapy ze zdarzeniami myszy](map-events.md)

> [!div class="nextstepaction"]
> [Kompilowanie dostępnej mapy](map-accessibility.md)

> [!div class="nextstepaction"]
> [Przykładowa strona kodowa](https://aka.ms/AzureMapsSamples)
