---
title: Moduł narzędzi do rysowania | Mapy Microsoft Azure
description: W tym artykule dowiesz się, jak ustawiać dane dotyczące opcji rysowania przy użyciu zestawu SDK sieci Web Microsoft Azure Maps
author: anastasia-ms
ms.author: v-stharr
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 95a04d763fa5982181cc1c797bce969d9857ae4b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92890636"
---
# <a name="use-the-drawing-tools-module"></a>Korzystanie z modułu narzędzi do rysowania

Zestaw SDK sieci Web Azure Maps udostępnia *moduł narzędzi do rysowania*. Ten moduł ułatwia rysowanie i edytowanie kształtów na mapie przy użyciu urządzenia wejściowego, takiego jak mysz lub ekran dotykowy. Podstawową klasą tego modułu jest [Menedżer rysowania](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager#setoptions-drawingmanageroptions-). Menedżer rysowania udostępnia wszystkie funkcje, które są konieczne do rysowania i edytowania kształtów na mapie. Może być używana bezpośrednio i jest zintegrowana z niestandardowym interfejsem użytkownika paska narzędzi. Można również użyć wbudowanej klasy [paska narzędzi rysunku](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar) . 

## <a name="loading-the-drawing-tools-module-in-a-webpage"></a>Ładowanie modułu narzędzi do rysowania na stronie sieci Web

1. Utwórz nowy plik HTML i [Zaimplementuj mapę w zwykły](./how-to-use-map-control.md)sposób.
2. Załaduj moduł narzędzi do rysowania Azure Maps. Możesz załadować go na jeden z dwóch sposobów:
    - Użyj ogólnie hostowanej wersji Content Delivery Network platformy Azure modułu Azure Maps Services. Dodaj odwołanie do arkusza stylów JavaScript i CSS w `<head>` elemencie pliku:

        ```html
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.js"></script>
        ```

    - Można też załadować moduł narzędzi do rysowania dla kodu źródłowego Azure Maps Web SDK lokalnie przy użyciu pakietu [Azure-Maps-Drawing-Tools](https://www.npmjs.com/package/azure-maps-drawing-tools) npm, a następnie hostować go przy użyciu aplikacji. Ten pakiet zawiera również definicje języka TypeScript. Użyj następującego polecenia:
    
        > **npm Zainstaluj platformę Azure — Maps — narzędzia do rysowania**
    
        Następnie Dodaj odwołanie do arkusza stylów JavaScript i CSS w `<head>` elemencie pliku:

         ```html
        <link rel="stylesheet" href="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.css" type="text/css" />
        <script src="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.js"></script>
         ```

## <a name="use-the-drawing-manager-directly"></a>Korzystanie z Menedżera rysowania bezpośrednio

Po załadowaniu modułu narzędzi do rysowania w aplikacji można włączyć możliwości rysowania i edytowania za pomocą [Menedżera rysowania](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager#setoptions-drawingmanageroptions-). Możesz określić opcje dla Menedżera rysowania podczas tworzenia jego wystąpienia lub alternatywnie użyć `drawingManager.setOptions()` funkcji.

### <a name="set-the-drawing-mode"></a>Ustawianie trybu rysowania

Poniższy kod tworzy wystąpienie Menedżera rysowania i ustawia opcję **trybu** rysowania. 

```javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon"
});
```

Poniższy kod stanowi kompletny przykład sposobu ustawiania trybu rysowania Menedżera rysowania. Kliknij mapę, aby rozpocząć rysowanie wielokąta.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Rysowanie wielokątu" src="//codepen.io/azuremaps/embed/YzKVKRa/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz pióro <a href='https://codepen.io/azuremaps/pen/YzKVKRa/'>narysuj Wielokąt</a> o Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


### <a name="set-the-interaction-type"></a>Ustaw typ interakcji

Menedżer rysowania obsługuje trzy różne sposoby współpracy z mapą do rysowania kształtów.

* `click` -Współrzędne są dodawane po kliknięciu myszy lub dotknięcia.
* `freehand ` -Współrzędne są dodawane, gdy mysz lub dotknięcie zostanie przeciągnięte na mapie. 
* `hybrid` -Współrzędne są dodawane, gdy mysz lub dotknięcie zostanie kliknięte lub przeciągnięte.

Poniższy kod włącza tryb rysowania Wielokąt i ustawia typ interakcji do rysowania, z którymi powinien być zgodny Menedżer rysowania `freehand` . 

```javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

 Ten przykładowy kod implementuje funkcje rysowania wielokąta na mapie. Po prostu przytrzymaj lewy przycisk myszy i przeciągnij go wokół siebie, swobodnie.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Rysowanie swobodne" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'>rysunek bezpłatny</a> pióro według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) w witrynie <a href='https://codepen.io'>CodePen</a>.
</iframe>


### <a name="customizing-drawing-options"></a>Dostosowywanie opcji rysowania

Poprzednie przykłady pokazują, jak dostosować opcje rysowania podczas tworzenia wystąpienia Menedżera rysowania. Możesz również ustawić opcje Menedżera rysowania przy użyciu `drawingManager.setOptions()` funkcji. Poniżej znajduje się narzędzie do testowania dostosowywania wszystkich opcji Menedżera rysowania przy użyciu funkcji SetOptions.

<br/>

<iframe height="685" title="Dostosuj Menedżera rysowania" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>Pobierz dane kształtu</a> według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak używać dodatkowych funkcji modułu narzędzi do rysowania:

> [!div class="nextstepaction"]
> [Dodawanie paska narzędzi rysowania](map-add-drawing-toolbar.md)

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
> [Menedżer rysowania](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager)

> [!div class="nextstepaction"]
> [Rysowanie paska narzędzi](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)