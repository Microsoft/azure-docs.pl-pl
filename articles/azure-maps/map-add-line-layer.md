---
title: Dodawanie warstwy liniowej do mapy | Mapy Microsoft Azure
description: Dowiedz się, jak dodać wiersze do map. Zapoznaj się z przykładami korzystającymi z Azure Maps Web SDK, aby dodać warstwy liniowe do map i dostosowywać linie za pomocą symboli i gradientów kolorów.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 9cf2f4876d62572ace118562fe5760f2b4010fa5
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92891163"
---
# <a name="add-a-line-layer-to-the-map"></a>Dodawanie warstwy liniowej do mapy

Warstwa linii może służyć do renderowania `LineString` i `MultiLineString` funkcji jako ścieżki lub trasy na mapie. Warstwa linii może również służyć do renderowania konspektu `Polygon` i `MultiPolygon` funkcji. Źródło danych jest połączone z warstwą linii w celu zapewnienia jej danych do renderowania. 

> [!TIP]
> Warstwy liniowe domyślnie będą renderować współrzędne wielokątów, jak również linie w źródle danych. Aby ograniczyć warstwę, która jest renderowana tylko w celu renderowania funkcji LineString, ustaw `filter` Właściwość warstwy na `['==', ['geometry-type'], 'LineString']` lub, `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]` Jeśli chcesz również uwzględnić funkcje MultiLineString.

Poniższy kod pokazuje, jak utworzyć wiersz. Dodaj wiersz do źródła danych, a następnie Renderuj go za pomocą warstwy liniowej za pomocą klasy [LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer) .

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a line and add it to the data source.
dataSource.add(new atlas.data.LineString([[-73.972340, 40.743270], [-74.004420, 40.756800]]));
  
//Create a line layer to render the line to the map.
map.layers.add(new atlas.layer.LineLayer(dataSource, null, {
    strokeColor: 'blue',
    strokeWidth: 5
}));
```

Poniżej znajduje się kompletny przykładowy kod wykonywany z powyższymi funkcjami.

<br/>

<iframe height='500' scrolling='no' title='Dodawanie linii do mapy' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/qomaKv/'>Dodaj linię do mapy</a> według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Warstwy linii można stylować za pomocą [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions) i [używać wyrażeń opartych na danych](data-driven-style-expressions-web-sdk.md).

## <a name="add-symbols-along-a-line"></a>Dodaj symbole wzdłuż linii

Ten przykład pokazuje, jak dodać ikony strzałek wzdłuż linii na mapie. W przypadku korzystania z warstwy symboli ustaw opcję "położenie" na "wiersz". Ta opcja spowoduje renderowanie symboli wzdłuż linii i obrócenie ikon (0 stopni = Right).

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Pokaż strzałkę wzdłuż linii" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/drBJwX/'>strzałka Pokaż strzałkę obok pozycji</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Zestaw SDK sieci Web Azure Maps udostępnia kilka dostosowywalnych szablonów obrazów, których można używać z warstwą symboli. Aby uzyskać więcej informacji, zobacz dokument [jak korzystać z szablonów obrazów](how-to-use-image-templates-web-sdk.md) .

<a name="line-stroke-gradient"></a>

## <a name="add-a-stroke-gradient-to-a-line"></a>Dodawanie gradientu obrysu do linii

Możesz zastosować jeden kolor pociągnięcia do linii. Możesz również wypełnić linię gradientem kolorów, aby pokazać przejście z jednego segmentu linii do następnego segmentu wiersza. Na przykład gradienty linii mogą służyć do reprezentowania zmian w czasie i odległości lub w różnych temperaturach w połączonej linii obiektów. Aby można było zastosować tę funkcję do wiersza, do źródła danych musi być `lineMetrics` ustawiona opcja true, a następnie wyrażenie gradientu koloru może być przesyłane do `strokeColor` opcji wiersza. Wyrażenie gradientu obrysu musi odwoływać się do `['line-progress']` wyrażenia danych, które uwidacznia obliczane metryki linii dla wyrażenia.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Linia z gradientem pociągnięcia" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
Zobacz linię pióra <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>z gradientem pociągnięcia</a> , Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-line-layer"></a>Dostosowywanie warstwy liniowej

Warstwa linii ma kilka opcji stylów. Oto narzędzie do wypróbowania.

<br/>

<iframe height='700' scrolling='no' title='Opcje warstwy linii' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>Opcje warstwy</a> pióra według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer) 

> [!div class="nextstepaction"]
> [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions)

Zapoznaj się z następującymi artykułami, aby uzyskać więcej przykładów kodu do dodania do Twoich map:

> [!div class="nextstepaction"]
> [Tworzenie źródła danych](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Dodawanie menu podręcznego](map-add-popup.md)

> [!div class="nextstepaction"]
> [Korzystanie z wyrażeń stylu opartych na danych](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Jak używać szablonów obrazów](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy wielokąta](map-add-shape.md)