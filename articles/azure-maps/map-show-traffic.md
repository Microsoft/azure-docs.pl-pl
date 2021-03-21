---
title: Pokaż ruch na mapie | Mapy Microsoft Azure
description: Dowiedz się, jak dodać dane ruchu do map. Dowiedz się więcej o danych przepływu i zobacz, jak używać zestawu SDK sieci Web Azure Maps do dodawania danych zdarzenia i danych przepływu do map.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: b6fcf39e05c4649503f0b7a80aadaaa16df24674
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98599549"
---
# <a name="show-traffic-on-the-map"></a>Pokaż ruch na mapie

Istnieją dwa typy danych ruchu dostępne w Azure Maps:

- Dane zdarzenia — składa się z punktów i danych opartych na wierszu dla elementów, takich jak konstrukcja, zamknięcie dróg i awarie.
- Dane przepływu — dostarcza metryki przepływu ruchu na drogach. Często dane przepływu ruchu są używane do kolorowania dróg. Kolory są zależne od tego, jaki ruch zmniejsza przepływ w stosunku do limitu szybkości lub innej metryki. Istnieją cztery wartości, które można przekazywać do opcji ruchu na `flow` mapie.

    |Wartość przepływu | Opis|
    | :-- | :-- |
    | `none` | Nie wyświetla danych o ruchu na mapie |
    | `relative` | Pokazuje dane o ruchu odnoszące się do szybkości swobodnego przepływu drogi |
    | `relative-delay` | Wyświetla obszary, które są wolniejsze niż średnie oczekiwane opóźnienie |
    | `absolute` | Pokazuje absolutną prędkość wszystkich pojazdów w podróży |

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

<iframe height='500' scrolling='no' title='Pokaż ruch na mapie' src='//codepen.io/azuremaps/embed/WMLRPw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>Pokaż ruch na mapie</a> według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="traffic-overlay-options"></a>Opcje nakładki ruchu

Poniższe narzędzie umożliwia przełączenie między różnymi ustawieniami nakładki ruchu, aby zobaczyć, jak zmienia się renderowanie. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opcje nakładki ruchu" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=result" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>opcje nakładki ruchu</a> piórem według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) w witrynie <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="add-traffic-controls"></a>Dodawanie kontrolek ruchu

Istnieją dwie różne kontrolki ruchu, które można dodać do mapy. Pierwszy formant, `TrafficControl` , dodaje przycisk przełączania, który może służyć do włączania i wyłączania ruchu. Opcje tej kontrolki umożliwiają określenie, kiedy ustawienia ruchu mają być używane podczas wyświetlania ruchu. Domyślnie ta kontrolka będzie wyświetlać względny przepływ ruchu i dane zdarzenia, ale można to zmienić tak, aby pokazywać bezwzględny przepływ ruchu i żadne zdarzenia w razie potrzeby. Druga kontrolka, `TrafficLegendControl` dodaje do mapy legendę przepływu ruchu, która pomaga użytkownikom zrozumieć, co oznacza kolor kodu drogi. Ta kontrolka będzie wyświetlana tylko na mapie, gdy dane przepływu ruchu są wyświetlane na mapie i będą ukryte w innym czasie.

Poniższy kod pokazuje, jak dodać kontrolki ruchu do mapy.

```JavaScript
//Att the traffic control toogle button to the top right corner of the map.
map.controls.add(new atlas.control.TrafficControl(), { position: 'top-right' });

//Att the traffic legend control to the bottom left corner of the map.
map.controls.add(new atlas.control.TrafficLegendControl(), { position: 'bottom-left' });
```

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Kontrolki ruchu" src="https://codepen.io/azuremaps/embed/ZEWaeLJ?height500&theme-id=0&default-tab=js,result&embed-version=2&editable=true" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/ZEWaeLJ'>kontrolki ruchu</a> pióra przez Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [Mapa](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [TrafficOptions](/javascript/api/azure-maps-control/atlas.trafficoptions)

Ulepsz środowisko użytkownika:

> [!div class="nextstepaction"]
> [Interakcja mapy ze zdarzeniami myszy](map-events.md)

> [!div class="nextstepaction"]
> [Kompilowanie dostępnej mapy](map-accessibility.md)

> [!div class="nextstepaction"]
> [Przykładowa strona kodowa](https://aka.ms/AzureMapsSamples)