---
title: Dodawanie warstwy bąbelkowej do mapy | Mapy Microsoft Azure
description: Dowiedz się, jak renderować punkty na mapach jako okręgi o stałych rozmiarach. Zapoznaj się z tematem jak używać zestawu SDK sieci Web Azure Maps do dodawania i dostosowywania warstw bąbelków do tego celu.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: cae29dcc0d334a2296199da0d8e3bc4562e275d3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92895332"
---
# <a name="add-a-bubble-layer-to-a-map"></a>Dodawanie warstwy bąbelkowej do mapy

W tym artykule przedstawiono sposób renderowania danych punktu ze źródła danych jako warstwy bąbelkowej na mapie. Warstwy bąbelków renderują punkty jako okręgi na mapie przy użyciu stałego promienia pikseli. 

> [!TIP]
> Domyślnie warstwy bąbelków będą renderować współrzędne wszystkich geometrie w źródle danych. Aby ograniczyć warstwę w taki sposób, że renderuje tylko funkcje geometrii punktów, ustawia `filter` Właściwość warstwy na `['==', ['geometry-type'], 'Point']` lub `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` Jeśli chcesz również uwzględnić funkcje MultiPoint.

## <a name="add-a-bubble-layer"></a>Dodawanie warstwy bąbelkowej

Poniższy kod ładuje tablicę punktów do źródła danych. Następnie łączy punkty danych z [warstwą bąbelkową](/javascript/api/azure-maps-control/atlas.layer.bubblelayer). Warstwa bąbelkowa renderuje promień każdego bąbelka z pięcioma pikselami i kolorem wypełnienia bieli. Kolor obrysu niebieskiego i szerokość obrysu wynoszącego sześć pikseli. 

```javascript
//Add point locations.
var points = [
    new atlas.data.Point([-73.985708, 40.75773]),
    new atlas.data.Point([-73.985600, 40.76542]),
    new atlas.data.Point([-73.985550, 40.77900]),
    new atlas.data.Point([-73.975550, 40.74859]),
    new atlas.data.Point([-73.968900, 40.78859])
];

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Add multiple points to the data source.
dataSource.add(points);

//Create a bubble layer to render the filled in area of the circle, and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(dataSource, null, {
    radius: 5,
    strokeColor: "#4288f7",
    strokeWidth: 6, 
    color: "white" 
}));
```

Poniżej znajduje się kompletny przykładowy kod wykonywany z powyższymi funkcjami.

<br/>

<iframe height='500' scrolling='no' title='BubbleLayer DataSource' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>Źródło danych BubbleLayer</a> dla pióra według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="show-labels-with-a-bubble-layer"></a>Wyświetlanie etykiet z warstwą bąbelkową

Ten kod pokazuje, jak używać warstwy bąbelkowej do renderowania punktu na mapie. I, jak używać warstwy symboli do renderowania etykiet. Aby ukryć ikonę warstwy symboli, ustaw `image` Właściwość opcji ikon na `'none'` .

<br/>

<iframe height='500' scrolling='no' title='MultiLayer DataSource' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>Źródło danych MultiLayer</a> dla pióra według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-bubble-layer"></a>Dostosowywanie warstwy bąbelków

Warstwa bąbelków zawiera tylko kilka opcji stylów. Oto narzędzie do wypróbowania.

<br/>

<iframe height='700' scrolling='no' title='Opcje warstwy bąbelkowej' src='//codepen.io/azuremaps/embed/eQxbGm/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/eQxbGm/'>Opcje warstwy bąbelków</a> pióra według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [BubbleLayer](/javascript/api/azure-maps-control/atlas.layer.bubblelayer)

> [!div class="nextstepaction"]
> [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions)

Zapoznaj się z następującymi artykułami, aby uzyskać więcej przykładów kodu do dodania do Twoich map:

> [!div class="nextstepaction"]
> [Tworzenie źródła danych](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy symboli](map-add-pin.md)

> [!div class="nextstepaction"]
> [Korzystanie z wyrażeń stylu opartych na danych](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Przykłady kodu](/samples/browse/?products=azure-maps)