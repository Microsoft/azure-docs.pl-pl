---
title: Dodawanie warstwy liniowej do map systemu Android | Mapy Microsoft Azure
description: Dowiedz się, jak dodać wiersze do map. Zobacz przykłady, które używają Android SDK Azure Maps, aby dodać warstwy liniowe w celu zamapowania i dostosowania linii przy użyciu symboli i gradientów kolorów.
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 62002b776262e97dd34db1d9ecd3b7b0e09f46f3
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102044236"
---
# <a name="add-a-line-layer-to-the-map-android-sdk"></a>Dodaj warstwę linii do mapy (Android SDK)

Warstwa linii może służyć do renderowania `LineString` i `MultiLineString` funkcji jako ścieżki lub trasy na mapie. Warstwa linii może również służyć do renderowania konspektu `Polygon` i `MultiPolygon` funkcji. Źródło danych jest połączone z warstwą linii w celu zapewnienia jej danych do renderowania.

> [!TIP]
> Warstwy liniowe domyślnie będą renderować współrzędne wielokątów, jak również linie w źródle danych. Aby ograniczyć warstwę w taki sposób, że renderuje tylko funkcje geometrii LineString, ustaw `filter` opcję warstwy na `eq(geometryType(), "LineString")` . Jeśli chcesz również uwzględnić funkcje MultiLineString, ustaw `filter` opcję warstwy na `any(eq(geometryType(), "LineString"), eq(geometryType(), "MultiLineString"))` .

## <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że wykonano kroki opisane w dokumencie [Szybki Start: Tworzenie aplikacji dla systemu Android](quick-android-map.md) . Bloki kodu w tym artykule można wstawiać do `onReady` programu obsługi zdarzeń Maps.

## <a name="add-a-line-layer"></a>Dodawanie warstwy linii

Poniższy kod pokazuje, jak utworzyć wiersz. Dodaj wiersz do źródła danych, a następnie Renderuj go za pomocą warstwy liniowej przy użyciu `LineLayer` klasy.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a list of points.
List<Point> points = Arrays.asList(
    Point.fromLngLat(-73.972340, 40.743270),
    Point.fromLngLat(-74.004420, 40.756800));

//Create a LineString geometry and add it to the data source.
source.add(LineString.fromLngLats(points));

//Create a line layer and add it to the map.
LineLayer layer = new LineLayer(source,
    strokeColor("blue"),
    strokeWidth(5f)
);

map.layers.add(layer);
```

Poniższy zrzut ekranu przedstawia powyższy kod podczas renderowania linii w warstwie liniowej.

![Mapuj z linią renderowaną przy użyciu warstwy linii](media/android-map-add-line-layer/android-line-layer.png)

## <a name="data-drive-line-style"></a>Styl linii dysku danych

Poniższy kod tworzy dwie funkcje liniowe i dodaje wartość limitu szybkości jako właściwość do każdego wiersza. Warstwa linii używa wyrażenia stylu dysku danych. kolor wierszy na podstawie wartości limitu szybkości. Ze względu na to, że dane linii są nakładane na drogi, poniższy kod dodaje warstwę linii poniżej warstwy etykiet, dzięki czemu etykiety dróg nadal mogą być czytelne.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a line feature.
Feature feature = Feature.fromGeometry(
    LineString.fromLngLats(Arrays.asList(
        Point.fromLngLat(-122.131821, 47.704033),
        Point.fromLngLat(-122.099919, 47.703678))));

//Add a property to the feature.
feature.addNumberProperty("speedLimitMph", 35);

//Add the feature to the data source.
source.add(feature);

//Create a second line feature.
Feature feature2 = Feature.fromGeometry(
    LineString.fromLngLats(Arrays.asList(
        Point.fromLngLat(-122.126662, 47.708265),
        Point.fromLngLat(-122.126877, 47.703980))));

//Add a property to the second feature.
feature2.addNumberProperty("speedLimitMph", 15);

//Add the second feature to the data source.
source.add(feature2);

//Create a line layer and add it to the map.
LineLayer layer = new LineLayer(source,
    strokeColor(
        interpolate(
            linear(),
            get("speedLimitMph"),
            stop(0, color(Color.GREEN)),
            stop(30, color(Color.YELLOW)),
            stop(60, color(Color.RED))
        )
    ),
    strokeWidth(5f)
);

map.layers.add(layer, "labels");
```

Poniższy zrzut ekranu przedstawia powyższy kod, który renderuje dwa wiersze w warstwie liniowej i pobiera ich kolory z wyrażenia stylu opartego na danych na podstawie właściwości w funkcjach wiersza.

![Mapuj z liniami ze stylem dysku danych renderowanymi w warstwie liniowej](media/android-map-add-line-layer/android-line-layer-data-drive-style.png)

## <a name="add-a-stroke-gradient-to-a-line"></a>Dodawanie gradientu obrysu do linii

Możesz zastosować jeden kolor pociągnięcia do linii. Możesz również wypełnić linię gradientem kolorów, aby pokazać przejście z jednego segmentu linii do następnego segmentu wiersza. Na przykład gradienty linii mogą służyć do reprezentowania zmian w czasie i odległości lub w różnych temperaturach w połączonej linii obiektów. Aby zastosować tę funkcję do linii, źródło danych musi mieć `lineMetrics` ustawioną opcję `true` , a następnie wyrażenie gradientu koloru można przesłać do `strokeColor` opcji wiersza. Wyrażenie gradientu obrysu musi odwoływać się do `lineProgress` wyrażenia danych, które uwidacznia obliczane metryki linii dla wyrażenia.

```java
//Create a data source and add it to the map.
source = new DataSource(
    //Enable line metrics on the data source. This is needed to enable support for strokeGradient.
    withLineMetrics(true)
);
map.sources.add(source);

//Create a line and add it to the data source.
source.add(LineString.fromLngLats(
    Arrays.asList(
        Point.fromLngLat(-122.18822, 47.63208),
        Point.fromLngLat(-122.18204, 47.63196),
        Point.fromLngLat(-122.17243, 47.62976),
        Point.fromLngLat(-122.16419, 47.63023),
        Point.fromLngLat(-122.15852, 47.62942),
        Point.fromLngLat(-122.15183, 47.62988),
        Point.fromLngLat(-122.14256, 47.63451),
        Point.fromLngLat(-122.13483, 47.64041),
        Point.fromLngLat(-122.13466, 47.64422),
        Point.fromLngLat(-122.13844, 47.65440),
        Point.fromLngLat(-122.13277, 47.66515),
        Point.fromLngLat(-122.12779, 47.66712),
        Point.fromLngLat(-122.11595, 47.66712),
        Point.fromLngLat(-122.11063, 47.66735),
        Point.fromLngLat(-122.10668, 47.67035),
        Point.fromLngLat(-122.10565, 47.67498)
    )
));

//Create a line layer and pass in a gradient expression for the strokeGradient property.
map.layers.add(new LineLayer(source,
    strokeWidth(6f),

    //Pass an interpolate or step expression that represents a gradient.
    strokeGradient(
        interpolate(
            linear(),
            lineProgress(),
            stop(0, color(Color.BLUE)),
            stop(0.1, color(Color.argb(255, 65, 105, 225))), //Royal Blue
            stop(0.3, color(Color.CYAN)),
            stop(0.5, color(Color.argb(255,0, 255, 0))), //Lime
            stop(0.7, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
));
```

Poniższy zrzut ekranu przedstawia powyższy kod wyświetlający wiersz renderowany przy użyciu koloru pociągnięcia gradientu.

![Mapuj z linią renderowaną jako ścieżka gradientu w warstwie liniowej](media/android-map-add-line-layer/android-line-layer-gradient.jpg)

## <a name="add-symbols-along-a-line"></a>Dodaj symbole wzdłuż linii

Ten przykład pokazuje, jak dodać ikony strzałek wzdłuż linii na mapie. W przypadku korzystania z warstwy symboli Ustaw `symbolPlacement` opcję na `SymbolPlacement.LINE` . Ta opcja spowoduje renderowanie symboli wzdłuż linii i obrócenie ikon (0 stopni = Right).

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Load a image of an arrow into the map image sprite and call it "arrow-icon".
map.images.add("arrow-icon", R.drawable.purple-arrow-right);

//Create and add a line to the data source.
source.add(LineString.fromLngLats(Arrays.asList(
    Point.fromLngLat(-122.18822, 47.63208),
    Point.fromLngLat(-122.18204, 47.63196),
    Point.fromLngLat(-122.17243, 47.62976),
    Point.fromLngLat(-122.16419, 47.63023),
    Point.fromLngLat(-122.15852, 47.62942),
    Point.fromLngLat(-122.15183, 47.62988),
    Point.fromLngLat(-122.14256, 47.63451),
    Point.fromLngLat(-122.13483, 47.64041),
    Point.fromLngLat(-122.13466, 47.64422),
    Point.fromLngLat(-122.13844, 47.65440),
    Point.fromLngLat(-122.13277, 47.66515),
    Point.fromLngLat(-122.12779, 47.66712),
    Point.fromLngLat(-122.11595, 47.66712),
    Point.fromLngLat(-122.11063, 47.66735),
    Point.fromLngLat(-122.10668, 47.67035),
    Point.fromLngLat(-122.10565, 47.67498)))
);

//Create a line layer and add it to the map.
map.layers.add(new LineLayer(source,
    strokeColor("DarkOrchid"),
    strokeWidth(5f)
));

//Create a symbol layer and add it to the map.
map.layers.add(new SymbolLayer(source,
    //Space symbols out along line.
    symbolPlacement(SymbolPlacement.LINE),

    //Spread the symbols out 100 pixels apart.
    symbolSpacing(100f),
    
    //Use the arrow icon as the symbol.
    iconImage("arrow-icon"),

    //Allow icons to overlap so that they aren't hidden if they collide with other map elements.
    iconAllowOverlap(true),

    //Center the symbol icon.
    iconAnchor(AnchorType.CENTER),

    //Scale the icon size.
    iconSize(0.8f)
));
```

Na potrzeby tego przykładu Poniższy obraz został załadowany do folderu do rysowania aplikacji.

| ![Obraz ikony purpurowej strzałki](media/android-map-add-line-layer/purple-arrow-right.png)|
|:-----------------------------------------------------------------------:|
|                                                  |

Poniższy zrzut ekranu pokazuje powyższy kod wyświetlający linię z wyświetlanymi na niej ikonami strzałek.

![Mapuj na linie z stylem dysku danych ze strzałkami renderowanymi w warstwie liniowej](media/android-map-add-line-layer/android-symbols-along-line-path.png)

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami, aby uzyskać więcej przykładów kodu do dodania do Twoich map:

> [!div class="nextstepaction"]
> [Tworzenie źródła danych](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Korzystanie z wyrażeń stylu opartych na danych](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy wielokąta](how-to-add-shapes-to-android-map.md)
