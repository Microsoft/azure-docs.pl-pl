---
title: Dodawanie warstwy obrazu do mapy | Mapy Microsoft Azure
description: Dowiedz się, jak dodać obrazy do mapy. Zapoznaj się z tematem jak używać zestawu SDK sieci Web Azure Maps do dostosowywania warstw obrazu i obrazów nakładanych na stałe zestawy współrzędnych.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 43de832095e2c2dd674a156da914ed26f1e472d0
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92892948"
---
# <a name="add-an-image-layer-to-a-map"></a>Dodawanie warstwy obrazu do mapy

W tym artykule pokazano, jak nałożyć obraz na stały zestaw współrzędnych. Poniżej przedstawiono kilka przykładów różnych typów obrazów, które mogą być nałożone na mapy:

* Obrazy przechwycone z dronom
* Kompilowanie Floorplans
* Historyczne lub inne wyspecjalizowane obrazy map
* Plany dotyczące lokacji zadań
* Obrazy radaru pogody

> [!TIP]
> [ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer) to prosty sposób nakładki obrazu na mapie. Należy pamiętać, że przeglądarki mogą mieć problemy z ładowaniem dużego obrazu. W takim przypadku Rozważ przerwanie obrazu na kafelkach i załadowanie ich do mapy jako [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer).

Warstwa obrazu obsługuje następujące formaty obrazów:

- JPEG
- PNG
- BMP
- GIF (bez animacji)

## <a name="add-an-image-layer"></a>Dodawanie warstwy obrazów

Poniższy kod nakłada obraz [mapy Newark, nowej Jersey, z 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) na mapie. [ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer) jest tworzony przez przekazanie adresu URL do obrazu i współrzędne dla czterech rogów w formacie `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]` .

```javascript
//Create an image layer and add it to the map.
map.layers.add(new atlas.layer.ImageLayer({
    url: 'newark_nj_1922.jpg',
    coordinates: [
        [-74.22655, 40.773941], //Top Left Corner
        [-74.12544, 40.773941], //Top Right Corner
        [-74.12544, 40.712216], //Bottom Right Corner
        [-74.22655, 40.712216]  //Bottom Left Corner
    ]
}));
```

Oto kompletny przykładowy kod dla poprzedniego kodu.

<br/>

<iframe height='500' scrolling='no' title='Prosta warstwa obrazu' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zapoznaj się z <a href='https://codepen.io/azuremaps/pen/eQodRo/'>prostą warstwą obrazu</a> piórem Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="import-a-kml-file-as-ground-overlay"></a>Importowanie pliku KML jako nakładki gruntowej

Ten przykład pokazuje, jak dodać informacje o nakładce KML ziemi jako warstwę obrazu na mapie. Nakładki naziemne KML zapewniają współrzędne północne, Południowe, wschodnie i zachodnie oraz obrót w prawo. Jednak warstwa obrazu oczekuje współrzędnych dla każdego rogu obrazu. Nakładka uziemienia KML w tym przykładzie jest dla Cathedral Chartres i jest źródłem z [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml).

Kod używa funkcji statycznej `getCoordinatesFromEdges` z klasy [ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer) . Oblicza cztery rogi obrazu przy użyciu informacji o północy, południe, wschód, zachód i rotacji nakładki naziemnej KML.

<br/>

<iframe height='500' scrolling='no' title='KML nakładki jako warstwa obrazu' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zapoznaj się z KML piórem, <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>jak warstwa obrazu</a> według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-an-image-layer"></a>Dostosowywanie warstwy obrazu

Warstwa obrazu ma wiele opcji stylów. Oto narzędzie do wypróbowania.

<br/>

<iframe height='700' scrolling='no' title='Opcje warstwy obrazu' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/RqOGzx/'>Opcje warstwy obrazu</a> pióra według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer)

> [!div class="nextstepaction"]
> [ImageLayerOptions](/javascript/api/azure-maps-control/atlas.imagelayeroptions)

Zapoznaj się z następującymi artykułami, aby uzyskać więcej przykładów kodu do dodania do Twoich map:

> [!div class="nextstepaction"]
> [Dodawanie warstwy kafelków](./map-add-tile-layer.md)