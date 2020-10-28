---
title: Tworzenie źródła danych dla mapy | Mapy Microsoft Azure
description: 'Dowiedz się, jak utworzyć źródło danych dla mapy. Dowiedz się więcej na temat źródeł danych używanych przez zestaw Azure Maps Web SDK: źródła GEOJSON i kafelki wektorowe.'
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 9c82b74ffdc8672dc3d84a98a036c6083bc6c309
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895923"
---
# <a name="create-a-data-source"></a>Tworzenie źródła danych

Azure Maps Web SDK przechowuje dane w źródłach danych. Użycie źródeł danych optymalizuje operacje na danych na potrzeby wykonywania zapytań i renderowania. Obecnie istnieją dwa typy źródeł danych:

- **Źródło GEOJSON** : dane lokalizacji pierwotnej w formacie GEOJSON są zarządzane lokalnie. Dobre dla małych i średnich zestawów danych (w górę setek tysięcy kształtów).
- **Źródło kafelka wektorowego** : wczytuje dane sformatowane jako kafelki wektorowe dla bieżącego widoku mapy, na podstawie systemu sąsiadującego z mapowaniem. Idealne rozwiązanie w przypadku dużych i wielkich zestawów danych (miliony lub miliardów kształtów).

## <a name="geojson-data-source"></a>Źródło danych GEOJSON

Źródło danych GEOJSON jest ładowane i przechowywane lokalnie przy użyciu `DataSource` klasy. Dane GEOJSON można utworzyć ręcznie lub utworzyć przy użyciu klas pomocnika w przestrzeni nazw [Atlas. Data](/javascript/api/azure-maps-control/atlas.data) . `DataSource`Klasa zawiera funkcje do importowania lokalnych lub zdalnych plików GeoJSON. Pliki zdalnego GEOJSON muszą być hostowane w punkcie końcowym z obsługą mechanizmu CORs. `DataSource`Klasa zawiera funkcje dla danych punktu klastrowania. I można łatwo dodawać, usuwać i aktualizować dane przy użyciu `DataSource` klasy. Poniższy kod pokazuje, jak dane GEOJSON można tworzyć w Azure Maps.

```javascript
//Create raw GeoJSON object.
var rawGeoJson = {
     "type": "Feature",
     "geometry": {
         "type": "Point",
         "coordinates": [-100, 45]
     },
     "properties": {
         "custom-property": "value"
     }
};

//Create GeoJSON using helper classes (less error prone and less typing).
var geoJsonClass = new atlas.data.Feature(new atlas.data.Point([-100, 45]), {
    "custom-property": "value"
}); 
```

Po utworzeniu źródła danych można dodać do mapy za pomocą `map.sources` właściwości, która jest elementem [sourcemanager](/javascript/api/azure-maps-control/atlas.sourcemanager). Poniższy kod pokazuje, jak utworzyć `DataSource` a i dodać go do mapy.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);
```

Poniższy kod przedstawia różne sposoby dodawania danych GEOJSON do obiektu `DataSource` .

```javascript
//GeoJsonData in the following code can be a single or array of GeoJSON features or geometries, a GeoJSON feature colleciton, or a single or array of atlas.Shape objects.

//Add geoJSON object to data source. 
dataSource.add(geoJsonData);

//Load geoJSON data from URL. URL should be on a CORs enabled endpoint.
dataSource.importDataFromUrl(geoJsonUrl);

//Overwrite all data in data source.
dataSource.setShapes(geoJsonData);
```

> [!TIP]
> Pozwala powiedzieć, że chcesz zastąpić wszystkie dane w `DataSource` . W przypadku wykonywania wywołań do `clear` funkcji then `add` mapa może być ponownie renderowana dwa razy, co może spowodować nieco opóźnienia. Zamiast tego należy użyć `setShapes` funkcji, która spowoduje usunięcie i zamianę wszystkich danych w źródle danych i wyzwala tylko pojedyncze ponowne renderowanie mapy.

## <a name="vector-tile-source"></a>Źródło kafelków wektora

Źródło kafelka wektorowego opisuje, jak uzyskać dostęp do warstwy kafelków wektorowych. Użyj klasy [VectorTileSource](/javascript/api/azure-maps-control/atlas.source.vectortilesource) , aby utworzyć wystąpienie źródła kafelka Vector. Warstwy kafelków wektorowych są podobne do warstw kafelków, ale nie są one takie same. Warstwa kafelków jest obrazem rastrowym. Warstwy kafelków wektorowych są skompresowanym plikiem w formacie **PBF** . Ten skompresowany plik zawiera dane mapy wektorowej i co najmniej jedną warstwę. Plik może być renderowany i ustalany na podstawie stylu każdej warstwy. Dane w kafelku wektorowym zawierają funkcje geograficzne w postaci punktów, linii i wielokątów. Istnieje kilka zalet używania warstw kafelków wektorowych zamiast warstw kafelków rastrowych:

 - Rozmiar pliku kafelka wektora jest zwykle znacznie mniejszy niż odpowiednik kafelka rastrowego. W związku z tym jest używana mniejsza przepustowość. Oznacza to małe opóźnienia, szybsze mapowanie i lepszy komfort pracy użytkowników.
 - Ponieważ kafelki wektorowe są renderowane na kliencie, dostosowują się do rozdzielczości urządzenia, na którym są one wyświetlane. W związku z tym renderowane mapy pojawiają się dokładniej i są bardziej zdefiniowane przy użyciu funkcji Crystal Clear labels.
 - Zmiana stylu danych w mapach wektorów nie wymaga ponownego pobierania danych, ponieważ nowy styl można zastosować na kliencie. Natomiast zmiana stylu warstwy kafelków rastrowych zazwyczaj wymaga załadowania kafelków z serwera, a następnie zastosowania nowego stylu.
 - Ponieważ dane są dostarczane w formie wektorowej, do przygotowania danych nie jest wymagane przetwarzanie po stronie serwera. W związku z tym nowsze dane można szybciej udostępnić.

Azure Maps jest zgodna ze [specyfikacją kafelka wektora MapBox](https://github.com/mapbox/vector-tile-spec), otwartym standardem. Azure Maps udostępnia następujące usługi kafelków wektorowych jako część platformy:

- [documentation](/rest/api/maps/renderv2/getmaptilepreview)  |  [Szczegóły formatu danych dokumentacji dotyczącej](https://developer.tomtom.com/maps-api/maps-api-documentation-vector/tile) kafelków dróg
- [documentation](/rest/api/maps/traffic/gettrafficincidenttile)  |  [Szczegóły dotyczące formatu danych dokumentacji dotyczącej](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles) zdarzeń ruchu
- [documentation](/rest/api/maps/traffic/gettrafficflowtile)  |  [Szczegóły formatu danych](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-flow/vector-flow-tiles) dokumentacji przepływu ruchu
- Kreator Azure Maps umożliwia również tworzenie niestandardowych kafelków wektorowych i uzyskiwanie do nich dostępu za pomocą funkcji [renderowania kafelków w wersji 2](/rest/api/maps/renderv2/getmaptilepreview)

> [!TIP]
> W przypadku używania kafelków obrazów wektorowych lub rastrowych z poziomu usługi Azure Maps Render z zestawem SDK sieci Web, można zastąpić `atlas.microsoft.com` symbolem zastępczym `{azMapsDomain}` . Ten symbol zastępczy zostanie zastąpiony tą samą domeną używaną przez mapę i automatycznie dołączać te same dane uwierzytelniania. Znacznie upraszcza to uwierzytelnianie za pomocą usługi renderowania przy użyciu uwierzytelniania Azure Active Directory.

Aby wyświetlić dane ze źródła kafelka wektorowego na mapie, Podłącz źródło do jednej z warstw renderowania danych. Wszystkie warstwy używające źródła wektora muszą określać `sourceLayer` wartość w opcjach. Poniższy kod ładuje usługę kafelków wektora przepływu ruchu Azure Maps jako źródło kafelka wektora, a następnie wyświetla ją na mapie przy użyciu warstwy liniowej. To źródło kafelka wektorowego zawiera pojedynczy zestaw danych w warstwie źródłowej o nazwie "przepływ ruchu". Dane wiersza w tym zestawie danych mają właściwość o nazwie `traffic_level` , która jest używana w tym kodzie do wybierania koloru i skalowania rozmiaru wierszy.

```javascript
//Create a vector tile source and add it to the map.
var datasource = new atlas.source.VectorTileSource(null, {
    tiles: ['https://{azMapsDomain}/traffic/flow/tile/pbf?api-version=1.0&style=relative&zoom={z}&x={x}&y={y}'],
    maxZoom: 22
});
map.sources.add(datasource);

//Create a layer for traffic flow lines.
var flowLayer = new atlas.layer.LineLayer(datasource, null, {
    //The name of the data layer within the data source to pass into this rendering layer.
    sourceLayer: 'Traffic flow',

    //Color the roads based on the traffic_level property. 
    strokeColor: [
        'interpolate',
        ['linear'],
        ['get', 'traffic_level'],
        0, 'red',
        0.33, 'orange',
        0.66, 'green'
    ],

    //Scale the width of roads based on the traffic_level property. 
    strokeWidth: [
        'interpolate',
        ['linear'],
        ['get', 'traffic_level'],
        0, 6,
        1, 1
    ]
});

//Add the traffic flow layer below the labels to make the map clearer.
map.layers.add(flowLayer, 'labels');
```

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Warstwa linii kafelka wektorowego" src="https://codepen.io/azuremaps/embed/wvMXJYJ?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zapoznaj się z <a href='https://codepen.io/azuremaps/pen/wvMXJYJ'>warstwą linii kafelka Vector</a> dla pióra Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="connecting-a-data-source-to-a-layer"></a>Łączenie źródła danych z warstwą

Dane są renderowane na mapie przy użyciu warstw renderowania. Pojedynczemu źródle danych może być przywoływana jedna lub więcej warstw renderowania. Następujące warstwy renderowania wymagają źródła danych:

- [Bąbelkowa warstwa](map-add-bubble-layer.md) renderuje dane punktowe jako skalowane kółka na mapie.
- [Warstwa symboli](map-add-pin.md) — renderuje dane punktu jako ikony lub tekst.
- [Warstwa mapy cieplnej](map-add-heat-map-layer.md) — renderuje dane punktu jako mapę cieplną gęstości.
- [Warstwa linii](map-add-shape.md) — Renderuj linię i Renderuj konspekt wielokątów. 
- [Warstwa wielokątów](map-add-shape.md) — wypełnia obszar wielokąta przy użyciu pełnego koloru lub wzorca obrazu.

Poniższy kod przedstawia sposób tworzenia źródła danych, dodawania go do mapy i łączenia go z warstwą bąbelkową. A następnie zaimportuj dane punktu GEOJSON z lokalizacji zdalnej do źródła danych. 

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(datasource));

//Load the earthquake data.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

Istnieją dodatkowe warstwy renderowania, które nie łączą się z tymi źródłami danych, ale bezpośrednio ładują dane do renderowania. 

- [Warstwa obrazu](map-add-image-layer.md) — nakłada pojedynczy obraz na mapę i wiąże jego rogi z zestawem określonych współrzędnych.
- [Warstwa kafelków](map-add-tile-layer.md) nakłada warstwę kafelków rastrowych na podstawie mapy.

## <a name="one-data-source-with-multiple-layers"></a>Jedno źródło danych z wieloma warstwami

Wiele warstw można podłączyć do pojedynczego źródła danych. Istnieje wiele różnych scenariuszy, w których ta opcja jest przydatna. Rozważmy na przykład scenariusz, w którym użytkownik rysuje wielokąt. Powinienmy renderować i wypełnić obszar Wielokąt, gdy użytkownik doda punkty do mapy. Dodanie linii z stylem do konturu Wielokąt sprawia, że krawędzie wielokąta są łatwiej widoczne podczas rysowania przez użytkownika. Aby wygodnie edytować poszczególne położenia w wielokąta, możemy dodać uchwyt, taki jak numer PIN lub znacznik, nad każdą pozycją.

![Mapa pokazująca wiele warstw renderowania danych z jednego źródła danych](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

W większości platform mapowania potrzebny jest obiekt wielokątny, obiekt linii i numer PIN dla każdej pozycji w wielokąta. Gdy Wielokąt jest modyfikowany, trzeba ręcznie zaktualizować linię i numery PIN, co może szybko stać się skomplikowany.

W przypadku Azure Maps, wystarczy, że jest to pojedynczy Wielokąt w źródle danych, jak pokazano w poniższym kodzie.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a polygon and add it to the data source.
dataSource.add(new atlas.data.Polygon([[[/* Coordinates for polygon */]]]));

//Create a polygon layer to render the filled in area of the polygon.
var polygonLayer = new atlas.layer.PolygonLayer(dataSource, 'myPolygonLayer', {
     fillColor: 'rgba(255,165,0,0.2)'
});

//Create a line layer for greater control of rendering the outline of the polygon.
var lineLayer = new atlas.layer.LineLayer(dataSource, 'myLineLayer', {
     color: 'orange',
     width: 2
});

//Create a bubble layer to render the vertices of the polygon as scaled circles.
var bubbleLayer = new atlas.layer.BubbleLayer(dataSource, 'myBubbleLayer', {
     color: 'orange',
     radius: 5,
     outlineColor: 'white',
     outlineWidth: 2
});

//Add all layers to the map.
map.layers.add([polygonLayer, lineLayer, bubbleLayer]);
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource)

> [!div class="nextstepaction"]
> [DataSourceOptions](/javascript/api/azure-maps-control/atlas.datasourceoptions)

> [!div class="nextstepaction"]
> [VectorTileSource](/javascript/api/azure-maps-control/atlas.source.vectortilesource)

> [!div class="nextstepaction"]
> [VectorTileSourceOptions](/javascript/api/azure-maps-control/atlas.vectortilesourceoptions)

Zapoznaj się z następującymi artykułami, aby uzyskać więcej przykładów kodu do dodania do Twoich map:

> [!div class="nextstepaction"]
> [Dodawanie menu podręcznego](map-add-popup.md)

> [!div class="nextstepaction"]
> [Korzystanie z wyrażeń stylu opartych na danych](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy symboli](map-add-pin.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy bąbelkowej](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy linii](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy wielokąta](map-add-shape.md)

> [!div class="nextstepaction"]
> [Dodawanie mapy cieplnej](map-add-heat-map-layer.md)

> [!div class="nextstepaction"]
> [Przykłady kodu](/samples/browse/?products=azure-maps)