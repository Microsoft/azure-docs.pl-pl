---
title: Dodawanie formantów do mapy | Mapy Microsoft Azure
description: Jak dodać kontrolkę powiększenia, kontrolkę gęstość, obrócić formant i selektor stylu do mapy w Microsoft Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: 2a225d4ea19cccea114e47cc61a8055d28e8cd99
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895294"
---
# <a name="add-controls-to-a-map"></a>Dodawanie formantów do mapy

W tym artykule opisano sposób dodawania formantów do mapy. Dowiesz się również, jak utworzyć mapę ze wszystkimi kontrolkami i [selektorem stylów](./choose-map-style.md).

## <a name="add-zoom-control"></a>Dodaj kontrolkę powiększenia

Kontrolka powiększenia dodaje przyciski umożliwiające powiększanie mapy w i na zewnątrz. Poniższy przykład kodu tworzy wystąpienie klasy [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) i dodaje ją do prawego dolnego rogu mapy.

```javascript
//Construct a zoom control and add it to the map.
map.controls.add(new atlas.control.ZoomControl(), {
    position: 'bottom-right'
});
```

Poniżej znajduje się kompletny przykładowy kod wykonywany z powyższymi funkcjami.

<br/>

<iframe height='500' scrolling='no' title='Dodawanie kontrolki powiększenia' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro, <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>dodając kontrolkę powiększenia</a> przez Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-pitch-control"></a>Dodaj kontrolkę gęstość

Kontrolka gęstość dodaje przyciski umożliwiające przechylenie skoku na mapę względem zakresu. Poniższy przykład kodu tworzy wystąpienie klasy [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) . Dodaje PitchControl do prawego górnego rogu mapy.

```javascript
//Construct a pitch control and add it to the map.
map.controls.add(new atlas.control.PitchControl(), {
    position: 'top-right'
});
```

Poniżej znajduje się kompletny przykładowy kod wykonywany z powyższymi funkcjami.

<br/>

<iframe height='500' scrolling='no' title='Dodawanie kontrolki skoku' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro, <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>dodając kontrolkę gęstość</a> do Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-compass-control"></a>Dodaj kontrolkę kompas

Kontrolka kompas dodaje przycisk służący do obracania mapy. Poniższy przykład kodu tworzy wystąpienie klasy [kontrolki kompasu](/javascript/api/azure-maps-control/atlas.control.compasscontrol) i dodaje ją do lewego dolnego rogu mapy.

```javascript
//Construct a compass control and add it to the map.
map.controls.add(new atlas.control.Compass(), {
    position: 'bottom-left'
});
```

Poniżej znajduje się kompletny przykładowy kod wykonywany z powyższymi funkcjami.

<br/>

<iframe height='500' scrolling='no' title='Dodawanie kontrolki Obróć' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro, <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>dodając kontrolkę Obróć</a> do Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="a-map-with-all-controls"></a>Mapa ze wszystkimi kontrolkami

Wiele kontrolek można umieścić w tablicy i dodać do mapy wszystkie jednocześnie i umieścić w tym samym obszarze mapy, aby uprościć tworzenie. Poniższe podejście dodaje standardowe kontrolki nawigacji do mapy.

```javascript
map.controls.add([
    new atlas.control.ZoomControl(),
    new atlas.control.CompassControl(),
    new atlas.control.PitchControl(),
    new atlas.control.StyleControl()
], {
    position: "top-right"
});
```

Poniższy przykład kodu dodaje kontrolki powiększenia, kompasu, skoku i stylu do prawego górnego rogu mapy. Zwróć uwagę, jak automatycznie stosują się do nich stosy. Kolejność obiektów formantów w skrypcie określa kolejność, w jakiej są wyświetlane na mapie. Aby zmienić kolejność formantów na mapie, można zmienić ich kolejność w tablicy.

<br/>

<iframe height='500' scrolling='no' title='Mapa ze wszystkimi kontrolkami' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>Mapa ze wszystkimi kontrolkami</a> według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Kontrolka selektora stylów jest definiowana przez klasę [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) . Aby uzyskać więcej informacji na temat używania kontrolki selektora stylów, zobacz [Wybieranie stylu mapy](choose-map-style.md).

## <a name="customize-controls"></a>Dostosuj kontrolki

Oto narzędzie do testowania różnych opcji dostosowywania kontrolek.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opcje kontrolki nawigacji" src="//codepen.io/azuremaps/embed/LwBZMx/?height=700&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/LwBZMx/'>Opcje kontrolki Nawigacja</a> piórem, Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Jeśli chcesz utworzyć niestandardowe kontrolki nawigacji, Utwórz klasę, która rozciąga się od `atlas.Control` klasy lub Utwórz element HTML i umieść go powyżej bloku DIV mapy. Ta kontrolka interfejsu użytkownika wywołuje funkcję Maps, `setCamera` Aby przenieść mapę. 

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [Kontrolka kompasu](/javascript/api/azure-maps-control/atlas.control.compasscontrol)

> [!div class="nextstepaction"]
> [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) 

> [!div class="nextstepaction"]
> [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) 

> [!div class="nextstepaction"]
> [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) 

Pełny kod można znaleźć w następujących artykułach:

> [!div class="nextstepaction"]
> [Dodawanie numeru PIN](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Dodawanie menu podręcznego](./map-add-popup.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy linii](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy wielokąta](map-add-shape.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy bąbelkowej](map-add-bubble-layer.md)