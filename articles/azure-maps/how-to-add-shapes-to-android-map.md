---
title: Dodawanie warstwy wielokąta do mapy systemu Android | Mapy Microsoft Azure
description: Dowiedz się, jak dodać wielokąty lub okręgi do map. Zobacz, jak używać Android SDK Azure Maps, aby dostosować kształty geometryczne i ułatwić ich aktualizowanie i konserwowanie.
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 1712cedab9cef23108fcc48b8e09bdc3e33065c4
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97679484"
---
# <a name="add-a-polygon-layer-to-the-map-android-sdk"></a>Dodawanie warstwy wielokąta do mapy (Android SDK)

W tym artykule pokazano, jak renderować obszary `Polygon` i `MultiPolygon` funkcje geometrie na mapie przy użyciu warstwy wielokąta.

## <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że wykonano kroki opisane w dokumencie [Szybki Start: Tworzenie aplikacji dla systemu Android](quick-android-map.md) . Bloki kodu w tym artykule można wstawiać do `onReady` programu obsługi zdarzeń Maps.

## <a name="use-a-polygon-layer"></a>Użyj warstwy wielokąta

Gdy warstwa wielokątów jest połączona ze źródłem danych i załadowana na mapie, renderuje obszar z `Polygon` i `MultiPolygon` funkcjami. Aby utworzyć wielokąt, Dodaj go do źródła danych i Renderuj za pomocą warstwy wielokąta przy użyciu `PolygonLayer` klasy.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a rectangular polygon.
source.add(Polygon.fromLngLats(
    Arrays.asList(
        Arrays.asList(
            Point.fromLngLat(-73.98235, 40.76799),
            Point.fromLngLat(-73.95785, 40.80044),
            Point.fromLngLat(-73.94928, 40.79680),
            Point.fromLngLat(-73.97317, 40.76437),
            Point.fromLngLat(-73.98235, 40.76799)
        )
    )
));

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(new PolygonLayer(source, 
    fillColor("red"),
    fillOpacity(0.7f)
), "labels");
```

Poniższy zrzut ekranu przedstawia powyższy kod podczas renderowania obszaru wielokąta przy użyciu warstwy wielokąta.

![Wielokąt z renderowanym obszarem wypełnienia](media/how-to-add-shapes-to-android-map/android-polygon-layer.png)

## <a name="use-a-polygon-and-line-layer-together"></a>Użyj jednocześnie wielokąta i warstwy liniowej

Warstwa linii służy do renderowania konspektu wielokątów. Poniższy przykład kodu renderuje Wielokąt podobny do poprzedniego, ale teraz dodaje warstwę linii. Ta warstwa linii jest drugą warstwą połączoną ze źródłem danych.  

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a rectangular polygon.
source.add(Polygon.fromLngLats(
    Arrays.asList(
        Arrays.asList(
            Point.fromLngLat(-73.98235, 40.76799),
            Point.fromLngLat(-73.95785, 40.80044),
            Point.fromLngLat(-73.94928, 40.79680),
            Point.fromLngLat(-73.97317, 40.76437),
            Point.fromLngLat(-73.98235, 40.76799)
        )
    )
));

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(new PolygonLayer(source,
    fillColor("rgba(0, 200, 200, 0.5)")
), "labels");

//Create and add a line layer to render the outline of the polygon.
map.layers.add(new LineLayer(source,
    strokeColor("red"),
    strokeWidth(2f)
));
```

Poniższy zrzut ekranu przedstawia powyższy kod, który renderuje wielokąt z konturem renderowany przy użyciu warstwy liniowej.

![Wielokąt z wyrenderowanym obszarem wypełnienia i konturem](media/how-to-add-shapes-to-android-map/android-polygon-and-line-layer.png)

> [!TIP]
> W przypadku tworzenia konspektu wielokąta z warstwą linii należy zamknąć wszystkie pierścienie w wielokątach, tak aby każda tablica punktów była taka sama jak początkowa i końcowa. Jeśli to nie zrobisz, warstwa linii nie może połączyć ostatniego punktu wielokąta z pierwszym punktem.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami, aby uzyskać więcej przykładów kodu do dodania do Twoich map:

> [!div class="nextstepaction"]
> [Tworzenie źródła danych](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Korzystanie z wyrażeń stylu opartych na danych](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy linii](android-map-add-line-layer.md)
