---
title: Dodawanie warstwy wytłoczenia wielokąta do mapy systemu Android | Mapy Microsoft Azure
description: Jak dodać warstwę wytłoczenia wielokąta do Android SDK map Microsoft Azure.
author: rbrundritt
ms.author: richbrun
ms.date: 02/19/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: b62c2540dc8cf2c7d4f67d465b2d464cbc0c3091
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054827"
---
# <a name="add-a-polygon-extrusion-layer-to-the-map-android-sdk"></a>Dodaj warstwę wytłoczenia wielokąta do mapy (Android SDK)

W tym artykule pokazano, jak za pomocą warstwy wytłoczenia wielokąta renderować obszary `Polygon` i `MultiPolygon` funkcje geometrie jako kształty wyciągnięcia.

## <a name="use-a-polygon-extrusion-layer"></a>Użyj warstwy wytłoczenia wielokątu

Połącz warstwę wytłoczenia wielokąta ze źródłem danych. Następnie załadowano ją na mapie. Warstwa wytłoczenia wielokąta renderuje obszary `Polygon` i `MultiPolygon` funkcje jako kształty wyciągnięcia. `height`Właściwości i `base` warstwy wytłoczenia wielokąta definiują odległość bazową od powierzchni i wysokości naciągnięcia kształtu w **licznikach**. Poniższy kod ilustruje sposób tworzenia wielokątów, dodawania go do źródła danych i renderowania przy użyciu klasy warstwy wytłoczenia wielokąt.

> [!Note]
> `base`Wartość zdefiniowana w warstwie wytłoczenia wielokąta powinna być mniejsza lub równa liczbie `height` .

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a polygon.
source.add(Polygon.fromLngLats(
    Arrays.asList(
        Arrays.asList(
            Point.fromLngLat(-73.95838379859924, 40.80027995478159),
            Point.fromLngLat(-73.98154735565186, 40.76845986171129),
            Point.fromLngLat(-73.98124694824219, 40.767761062136955),
            Point.fromLngLat(-73.97361874580382, 40.76461637311633),
            Point.fromLngLat(-73.97306084632874, 40.76512830937617),
            Point.fromLngLat(-73.97259950637817, 40.76490890860481),
            Point.fromLngLat(-73.9494466781616, 40.79658450499243),
            Point.fromLngLat(-73.94966125488281, 40.79708807289436),
            Point.fromLngLat(-73.95781517028809, 40.80052360358227),
            Point.fromLngLat(-73.95838379859924, 40.80027995478159)
        )
    )
));

//Create and add a polygon extrusion layer to the map below the labels so that they are still readable.
PolygonExtrusionLayer layer = new PolygonExtrusionLayer(source,
    fillColor("#fc0303"),
    fillOpacity(0.7f),
    height(500f)
);

//Create and add a polygon extrusion layer to the map below the labels so that they are still readable.
map.layers.add(layer, "labels");
```

Poniższy zrzut ekranu przedstawia powyższy kod, rending Wielokąt rozciągany w pionie przy użyciu warstwy wytłoczenia wielokąta.

![Mapuj z wielokątem rozciąganym w pionie przy użyciu warstwy wytłoczenia wielokąta](media/map-extruded-polygon-android/polygon-extrusion-layer.jpg)

## <a name="add-data-driven-polygons"></a>Dodawanie wielokątów opartych na danych

Mapę na mapie choropleth można renderować przy użyciu warstwy wytłoczenia wielokąta. Ustaw `height` właściwości i `fillColor` warstwy wytłoczenia na pomiar zmiennej statystycznej w `Polygon` `MultiPolygon` funkcji i geometrie. Poniższy przykładowy kod przedstawia wytłaczaną mapę na mapie choropleth Stany Zjednoczone na podstawie pomiaru gęstości populacji według stanu.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Import the geojson data and add it to the data source.
Utils.importData("https://azuremapscodesamples.azurewebsites.net/Common/data/geojson/US_States_Population_Density.json", this,  (String result) -> {
    //Parse the data as a GeoJSON Feature Collection.
    FeatureCollection fc = FeatureCollection.fromJson(result);

    //Add the feature collection to the data source.
    source.add(fc);
});

//Create and add a polygon extrusion layer to the map below the labels so that they are still readable.
PolygonExtrusionLayer layer = new PolygonExtrusionLayer(source,
    fillOpacity(0.7f),
    fillColor(
        step(
            get("density"),
            literal("rgba(0, 255, 128, 1)"),
            stop(10, "rgba(9, 224, 118, 1)"),
            stop(20, "rgba(11, 191, 103, 1)"),
            stop(50, "rgba(247, 227, 5, 1)"),
            stop(100, "rgba(247, 199, 7, 1)"),
            stop(200, "rgba(247, 130, 5, 1)"),
            stop(500, "rgba(247, 94, 5, 1)"),
            stop(1000, "rgba(247, 37, 5, 1)")
        )
    ),
    height(
        interpolate(
            linear(),
            get("density"),
            stop(0, 100),
            stop(1200, 960000)
        )
    )
);

//Create and add a polygon extrusion layer to the map below the labels so that they are still readable.
map.layers.add(layer, "labels");
```

Poniższy zrzut ekranu przedstawia mapę na mapie choropleth Stanów Zjednoczonych, które są kolorowe i rozciągane w pionie jako wytłaczane wielokąty w oparciu o gęstość populacji.

![Mapa na mapie choropleth Stanów Stanów Zjednoczonych i rozciągana pionowo w miarę wytłaczanych wielokątów w oparciu o gęstość populacji](media/map-extruded-polygon-android/android-extruded-choropleth.jpg)

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami, aby uzyskać więcej przykładów kodu do dodania do Twoich map:

> [!div class="nextstepaction"]
> [Tworzenie źródła danych](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Korzystanie z wyrażeń stylu opartych na danych](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy wielokąta](how-to-add-shapes-to-android-map.md)
