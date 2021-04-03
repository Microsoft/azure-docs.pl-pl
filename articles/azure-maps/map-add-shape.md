---
title: Dodawanie warstwy wielokąta do mapy | Mapy Microsoft Azure
description: Dowiedz się, jak dodać wielokąty lub okręgi do map. Zapoznaj się z tematem jak używać zestawu SDK sieci Web Azure Maps, aby dostosować kształty geometryczne i ułatwić ich aktualizowanie i konserwowanie.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 96553e75d4b982cfe67d03961d4356a3844f253c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92891001"
---
# <a name="add-a-polygon-layer-to-the-map"></a>Dodawanie warstwy wielokąta do mapy

W tym artykule pokazano, jak renderować obszary `Polygon` i `MultiPolygon` funkcje geometrie na mapie przy użyciu warstwy wielokąta. Zestaw SDK sieci Web Azure Maps obsługuje również tworzenie okręgów geometrie zgodnie z definicją w [schemacie rozszerzonego kodu JSON](extend-geojson.md#circle). Te okręgi są przekształcane na wielokąty, gdy są renderowane na mapie. Wszystkie funkcje geometrie można łatwo aktualizować po zapakowaniu z [Atlasem. Shape](/javascript/api/azure-maps-control/atlas.shape) — Klasa.

## <a name="use-a-polygon-layer"></a>Użyj warstwy wielokąta 

Gdy warstwa wielokątów jest połączona ze źródłem danych i załadowana na mapie, renderuje obszar z `Polygon` i `MultiPolygon` funkcjami. Aby utworzyć wielokąt, Dodaj go do źródła danych i Renderuj go za pomocą warstwy wielokąta przy użyciu klasy [PolygonLayer](/javascript/api/azure-maps-control/atlas.layer.polygonlayer) .

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a rectangular polygon.
dataSource.add(new atlas.data.Feature(
    new atlas.data.Polygon([[
        [-73.98235, 40.76799],
        [-73.95785, 40.80044],
        [-73.94928, 40.7968],
        [-73.97317, 40.76437],
        [-73.98235, 40.76799]
    ]])
));

//Create and add a polygon layer to render the polygon to the map, below the label layer.
map.layers.add(new atlas.layer.PolygonLayer(dataSource, null,{
    fillColor: 'red',
    fillOpacity: 0.7
}), 'labels');
```

Poniżej znajduje się kompletny i uruchomiony przykład powyższego kodu.

<br/>

<iframe height='500' scrolling='no' title='Dodawanie wielokąta do mapy ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Dodaj wielokąt do mapy </a> według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-a-polygon-and-line-layer-together"></a>Użyj jednocześnie wielokąta i warstwy liniowej

Warstwa linii służy do renderowania konspektu wielokątów. Poniższy przykład kodu renderuje Wielokąt podobny do poprzedniego, ale teraz dodaje warstwę linii. Ta warstwa linii jest drugą warstwą połączoną ze źródłem danych.  

<br/>

<iframe height='500' scrolling='no' title='Wielokąt i warstwa linii w celu dodania wielokąta' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz Wielokąt pióra <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>i warstwę linii, aby dodać Wielokąt</a> o Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) w <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="fill-a-polygon-with-a-pattern"></a>Wypełnienie wielokąta z wzorcem

Oprócz wypełniania wielokątów kolorem możesz użyć wzorca obrazu, aby wypełnić wielokąt. Załaduj wzorzec obrazu do zasobów ikon obrazu mapy, a następnie odwołuje się do tego obrazu za pomocą `fillPattern` Właściwości warstwy Wielokąt.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Deseń wypełnienia wielokąta" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>wzorzec wypełnienia wielokąta</a> piórem Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!TIP]
> Zestaw SDK sieci Web Azure Maps udostępnia kilka dostosowywalnych szablonów obrazów, których można użyć jako wzorców wypełnienia. Aby uzyskać więcej informacji, zobacz dokument [jak korzystać z szablonów obrazów](how-to-use-image-templates-web-sdk.md) .

## <a name="customize-a-polygon-layer"></a>Dostosowywanie warstwy wielokątów

Warstwa wielokątów zawiera tylko kilka opcji stylów. Oto narzędzie do wypróbowania.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a> według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>Dodaj okrąg do mapy

Azure Maps używa rozszerzonej wersji schematu GEOJSON, który zawiera definicję okręgów, jak pokazano [tutaj](extend-geojson.md#circle). Okrąg jest renderowany na mapie przez utworzenie `Point` funkcji. `Point`Ma `subType` Właściwość o wartości `"Circle"` i `radius` właściwości o liczbie reprezentującej promień w licznikach. 

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}  
```

Zestaw SDK sieci Web Azure Maps konwertuje te `Point` funkcje na `Polygon` funkcje. Następnie te funkcje są renderowane na mapie przy użyciu warstw wielokątów i linii, jak pokazano w poniższym przykładzie kodu.

<br/>

<iframe height='500' scrolling='no' title='Dodaj okrąg do mapy' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Dodaj okrąg do mapy</a> według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="make-a-geometry-easy-to-update"></a>Łatwa aktualizacja geometrii

`Shape`Klasa otacza [geometrię](/javascript/api/azure-maps-control/atlas.data.geometry) lub [funkcję](/javascript/api/azure-maps-control/atlas.data.feature) , ułatwiając aktualizowanie i obsługę tych funkcji. Aby utworzyć wystąpienie zmiennej Shape, Przekaż geometrię lub zestaw właściwości do konstruktora kształtu.

```javascript
//Creating a shape by passing in a geometry and a object containing properties.
var shape1 = new atlas.Shape(new atlas.data.Point[0,0], { myProperty: 1 });

//Creating a shape using a feature.
var shape2 = new atlas.Shape(new atlas.data.Feature(new atlas.data.Point[0,0], { myProperty: 1 });
```

Poniższy przykład kodu pokazuje, jak otoczyć obiekt GEOJSON koła z klasą Shape. Gdy wartość promienia jest zmieniana w kształcie, okrąg jest automatycznie renderowany na mapie.

<br/>

<iframe height='500' scrolling='no' title='Aktualizowanie właściwości kształtu' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zapoznaj się z <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>właściwościami kształtu</a> Dodaj pióro według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [Wielokąt](/javascript/api/azure-maps-control/atlas.data.polygon)

> [!div class="nextstepaction"]
> [PolygonLayer](/javascript/api/azure-maps-control/atlas.layer.polygonlayer)

> [!div class="nextstepaction"]
> [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)

Aby uzyskać więcej przykładów kodu do dodania do usługi Maps, zobacz następujące artykuły:

> [!div class="nextstepaction"]
> [Tworzenie źródła danych](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Dodawanie menu podręcznego](map-add-popup.md)

> [!div class="nextstepaction"]
> [Korzystanie z wyrażeń stylu opartych na danych](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Jak używać szablonów obrazów](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy linii](map-add-line-layer.md)

Dodatkowe zasoby:

> [!div class="nextstepaction"]
> [Rozszerzenie specyfikacji GEOJSON Azure Maps](extend-geojson.md#circle)