---
title: Dodawanie paska narzędzi narzędzi do rysowania do mapowania | Mapy Microsoft Azure
description: Jak dodać pasek narzędzi rysowania do mapy za pomocą Azure Maps Web SDK
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: b00628ec5a9f41b027bf90b93421f3aa1404e97a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92896399"
---
# <a name="add-a-drawing-tools-toolbar-to-a-map"></a>Dodawanie paska narzędzi narzędzi do rysowania do mapy

W tym artykule pokazano, jak używać modułu narzędzi do rysowania i wyświetlać pasek narzędzi rysowania na mapie. Kontrolka [DrawingToolbar](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar) dodaje pasek narzędzi Rysowanie na mapie. Dowiesz się, jak tworzyć mapy za pomocą tylko jednego i wszystkich narzędzi do rysowania oraz jak dostosować renderowanie kształtów rysowania w Menedżerze rysowania.

## <a name="add-drawing-toolbar"></a>Dodawanie paska narzędzi rysowania

Poniższy kod tworzy wystąpienie Menedżera rysowania i wyświetla pasek narzędzi na mapie.

```javascript
//Create an instance of the drawing manager and display the drawing toolbar.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'dark'
        })
    });
```

Poniżej znajduje się kompletny przykładowy kod wykonywany w powyższych funkcjach:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Dodawanie paska narzędzi rysowania" src="//codepen.io/azuremaps/embed/ZEzLeRg/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Zapoznaj się z piórem <a href='https://codepen.io/azuremaps/pen/ZEzLeRg/'>Dodawanie paska narzędzi Rysowanie</a> przez Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="limit-displayed-toolbar-options"></a>Ogranicz wyświetlane opcje paska narzędzi

Poniższy kod tworzy wystąpienie Menedżera rysowania i wyświetla pasek narzędzi z narzędziem rysowania wielokątów na mapie. 

```javascript
//Create an instance of the drawing manager and display the drawing toolbar with polygon drawing tool.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'light',
            buttons: ["draw-polygon"]
        })
    });
```

Poniżej znajduje się kompletny przykładowy kod wykonywany w powyższych funkcjach:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Dodawanie narzędzia do rysowania wielokątów" src="//codepen.io/azuremaps/embed/OJLWWMy/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Zobacz pióro <a href='https://codepen.io/azuremaps/pen/OJLWWMy/'>Dodaj narzędzie do rysowania wielokątów</a> , Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) w <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="change-drawing-rendering-style"></a>Zmień styl renderowania rysowania

Styl rysowanych kształtów można dostosować przez pobranie podstawowych warstw Menedżera rysowania przy użyciu `drawingManager.getLayers()` funkcji, a następnie ustawienie opcji na poszczególnych warstwach. Uchwyty przeciągania, które pojawiają się dla współrzędnych podczas edytowania kształtu, są znacznikami HTML. Styl uchwytów przeciągania można dostosować, przekazując opcje znacznika HTML do `dragHandleStyle` opcji i w `secondaryDragHandleStyle` Menedżerze rysowania.  

Poniższy kod pobiera warstwy renderowania z Menedżera rysowania i modyfikuje ich opcje, aby zmienić styl renderowania dla rysowania. W takim przypadku punkty będą renderowane z niebieską ikoną znacznika. Linie będą czerwone i cztery piksele. Wielokąty mają zielony kolor wypełnienia i pomarańczowy konspekt. Następnie zmienia style uchwytów przeciąganych na ikony kwadratowe. 

```javascript
//Get rendering layers of drawing manager.
var layers = drawingManager.getLayers();

//Change the icon rendered for points.
layers.pointLayer.setOptions({
    iconOptions: {
        image: 'marker-blue'
    }
});

//Change the color and width of lines.
layers.lineLayer.setOptions({
    strokeColor: 'red',
    strokeWidth: 4
});

//Change fill color of polygons.
layers.polygonLayer.setOptions({
    fillColor: 'green'
});

//Change the color of polygon outlines.
layers.polygonOutlineLayer.setOptions({
    strokeColor: 'orange'
});

//Update the style of the drag handles that appear when editting.
drawingManager.setOptions({
    //Primary drag handle that represents coordinates in the shape.
    dragHandleStyle: {
        anchor: 'center',
        htmlContent: '<svg width="15" height="15" viewBox="0 0 15 15" xmlns="http://www.w3.org/2000/svg" style="cursor:pointer"><rect x="0" y="0" width="15" height="15" style="stroke:black;fill:white;stroke-width:4px;"/></svg>',
        draggable: true
    },

    //Secondary drag hanle that represents mid-point coordinates that users can grab to add new cooridnates in the middle of segments.
    secondaryDragHandleStyle: {
        anchor: 'center',
        htmlContent: '<svg width="10" height="10" viewBox="0 0 10 10" xmlns="http://www.w3.org/2000/svg" style="cursor:pointer"><rect x="0" y="0" width="10" height="10" style="stroke:white;fill:black;stroke-width:4px;"/></svg>',
        draggable: true
    }
});  
```

Poniżej znajduje się kompletny przykładowy kod wykonywany w powyższych funkcjach:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Zmień styl renderowania rysowania" src="//codepen.io/azuremaps/embed/OJLWpyj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Zobacz styl renderowania dla pióra <a href='https://codepen.io/azuremaps/pen/OJLWpyj/'>zmiana rysowania</a> według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak używać dodatkowych funkcji modułu narzędzi do rysowania:

> [!div class="nextstepaction"]
> [Pobieranie danych kształtów](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Reagowanie na zdarzenia rysowania](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Typy interakcji i skróty klawiaturowe](drawing-tools-interactions-keyboard-shortcuts.md)

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [Mapa](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Rysowanie paska narzędzi](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)

> [!div class="nextstepaction"]
> [Menedżer rysowania](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager)