---
title: Dodawanie warstwy obrazu do mapy systemu Android | Mapy Microsoft Azure
description: Dowiedz się, jak dodać obrazy do mapy. Zapoznaj się z tematem jak używać Android SDK Azure Maps, aby dostosowywać warstwy obrazu i nakładać obrazy w stałych zestawach współrzędnych.
author: rbrundritt
ms.author: richbrun
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: e1d99297c0357039606149bdf7e5a526258fc7c5
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054693"
---
# <a name="add-an-image-layer-to-a-map-android-sdk"></a>Dodawanie warstwy obrazu do mapy (Android SDK)

W tym artykule pokazano, jak nałożyć obraz na stały zestaw współrzędnych. Poniżej przedstawiono kilka przykładów różnych typów obrazów, które mogą być nałożone na mapy:

* Obrazy przechwycone z dronom
* Kompilowanie Floorplans
* Historyczne lub inne wyspecjalizowane obrazy map
* Plany dotyczące lokacji zadań

> [!TIP]
> Warstwa obrazu to prosty sposób nakładki obrazu na mapie. Należy pamiętać, że duże obrazy mogą zużywać dużą ilość pamięci i potencjalnie powodować problemy z wydajnością. W takim przypadku Rozważ przerwanie obrazu na kafelkach i załadowanie ich do mapy jako warstwy kafelków.

## <a name="add-an-image-layer"></a>Dodawanie warstwy obrazów

Poniższy kod nakłada obraz [mapy Newark, nowej Jersey, z 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) na mapie. Ten obraz jest dodawany do `drawable` folderu projektu. Warstwa obrazu jest tworzona przez ustawienie obrazu i współrzędne dla czterech rogów w formacie `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]` . Dodawanie warstw obrazów poniżej `label` warstwy jest często pożądane.

::: zone pivot="programming-language-java-android"

```java
//Create an image layer.
ImageLayer layer = new ImageLayer(
    imageCoordinates(
        new Position[] {
            new Position(-74.22655, 40.773941), //Top Left Corner
            new Position(-74.12544, 40.773941), //Top Right Corner
            new Position(-74.12544, 40.712216), //Bottom Right Corner
            new Position(-74.22655, 40.712216)  //Bottom Left Corner
        }
    ),
    setImage(R.drawable.newark_nj_1922)
);

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create an image layer.
val layer = ImageLayer(
    imageCoordinates(
        arrayOf<Position>(
            Position(-74.22655, 40.773941),  //Top Left Corner
            Position(-74.12544, 40.773941),  //Top Right Corner
            Position(-74.12544, 40.712216),  //Bottom Right Corner
            Position(-74.22655, 40.712216)   //Bottom Left Corner
        )
    ),
    setImage(R.drawable.newark_nj_1922)
)

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels")
```

::: zone-end

Alternatywnie można określić adres URL obrazu hostowanego w trybie online. Jeśli jednak w Twoim scenariuszu można dodać obraz do `drawable` folderu projekty, który będzie ładowany szybciej, ponieważ obraz będzie dostępny lokalnie i nie będzie trzeba go pobrać.

::: zone pivot="programming-language-java-android"

```java
//Create an image layer.
ImageLayer layer = new ImageLayer(
    imageCoordinates(
        new Position[] {
            new Position(-74.22655, 40.773941), //Top Left Corner
            new Position(-74.12544, 40.773941), //Top Right Corner
            new Position(-74.12544, 40.712216), //Bottom Right Corner
            new Position(-74.22655, 40.712216)  //Bottom Left Corner
        }
    ),
    setUrl("https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg")
);

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create an image layer.
val layer = ImageLayer(
    imageCoordinates(
        arrayOf<Position>(
            Position(-74.22655, 40.773941),  //Top Left Corner
            Position(-74.12544, 40.773941),  //Top Right Corner
            Position(-74.12544, 40.712216),  //Bottom Right Corner
            Position(-74.22655, 40.712216) //Bottom Left Corner
        )
    ),
    setUrl("https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg")
)

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels")
```

::: zone-end

Poniższy zrzut ekranu przedstawia mapę Newark, nową Jersey, z 1922 nałożonej przy użyciu warstwy obrazu.

![Mapa Newark, Nowa Jersey, z 1922 nałożonej przy użyciu warstwy obrazu](media/map-add-image-layer-android/android-image-layer.gif)

## <a name="import-a-kml-file-as-ground-overlay"></a>Importowanie pliku KML jako nakładki gruntowej

Ten przykład pokazuje, jak dodać informacje o nakładce KML ziemi jako warstwę obrazu na mapie. Nakładki naziemne KML zapewniają współrzędne północne, Południowe, wschodnie i zachodnie oraz obrót w prawo. Jednak warstwa obrazu oczekuje współrzędnych dla każdego rogu obrazu. Nakładka uziemienia KML w tym przykładzie jest dla Cathedral Chartres i jest źródłem z [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml).

```xml
<?xml version="1.0" encoding="UTF-8"?>
<kml xmlns="http://www.opengis.net/kml/2.2" xmlns:gx="http://www.google.com/kml/ext/2.2" xmlns:kml="http://www.opengis.net/kml/2.2" xmlns:atom="http://www.w3.org/2005/Atom">
<GroundOverlay>
    <name>Map of Chartres cathedral</name>
    <Icon>
        <href>https://upload.wikimedia.org/wikipedia/commons/thumb/e/e3/Chartres.svg/1600px-Chartres.svg.png</href>
        <viewBoundScale>0.75</viewBoundScale>
    </Icon>
    <LatLonBox>
        <north>48.44820923628113</north>
        <south>48.44737203258976</south>
        <east>1.488833825534365</east>
        <west>1.486788581643038</west>
        <rotation>46.44067597839695</rotation>
    </LatLonBox>
</GroundOverlay>
</kml>
```

Kod używa metody statycznej `getCoordinatesFromEdges` z `ImageLayer` klasy. Ta metoda oblicza cztery rogi obrazu przy użyciu informacji o północy, południe, wschód, zachód i rotacji nakładki naziemnej KML.

::: zone pivot="programming-language-java-android"

```java
//Calculate the corner coordinates of the ground overlay.
Position[] corners = ImageLayer.getCoordinatesFromEdges(
    //North, south, east, west
    48.44820923628113, 48.44737203258976, 1.488833825534365, 1.486788581643038,

    //KML rotations are counter-clockwise, subtract from 360 to make them clockwise.
    360 - 46.44067597839695
);

//Create an image layer.
ImageLayer layer = new ImageLayer(
    imageCoordinates(corners),
    setUrl("https://upload.wikimedia.org/wikipedia/commons/thumb/e/e3/Chartres.svg/1600px-Chartres.svg.png")
);

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Calculate the corner coordinates of the ground overlay.
val corners: Array<Position> =
    ImageLayer.getCoordinatesFromEdges( //North, south, east, west
        48.44820923628113,
        48.44737203258976,
        1.488833825534365,
        1.486788581643038,  //KML rotations are counter-clockwise, subtract from 360 to make them clockwise.
        360 - 46.44067597839695
    )

//Create an image layer.
val layer = ImageLayer(
    imageCoordinates(corners),
    setUrl("https://upload.wikimedia.org/wikipedia/commons/thumb/e/e3/Chartres.svg/1600px-Chartres.svg.png")
)

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels")
```

::: zone-end

Poniższy zrzut ekranu przedstawia mapę z KML nakładką nałożoną przy użyciu warstwy obrazu.

![Mapuj z KML nakładką nałożoną przy użyciu warstwy obrazu](media/map-add-image-layer-android/android-ground-overlay.jpg)

> [!TIP]
> Użyj `getPixels` metod i `getPositions` klasy warstwy obrazu do konwersji między współrzędne geograficzne warstwy obrazu położenia i współrzędne pikseli obrazu lokalnego.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z poniższym artykułem, aby dowiedzieć się więcej o sposobach nakładania obrazów na mapę.

> [!div class="nextstepaction"]
> [Dodawanie warstwy kafelków](how-to-add-tile-layer-android-map.md)