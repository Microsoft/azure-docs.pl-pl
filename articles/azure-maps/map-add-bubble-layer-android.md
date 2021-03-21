---
title: Dodawanie warstwy bąbelkowej do map systemu Android | Mapy Microsoft Azure
description: Dowiedz się, jak renderować punkty na mapach jako okręgi o stałych rozmiarach. Zobacz, jak używać Android SDK Azure Maps, aby w tym celu dodawać i dostosowywać warstwy bąbelków.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: f3c175f30c5c0e6206f4fee274e0f3f000e55a74
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102100172"
---
# <a name="add-a-bubble-layer-to-a-map-android-sdk"></a>Dodawanie warstwy bąbelkowej do mapy (Android SDK)

W tym artykule przedstawiono sposób renderowania danych punktu ze źródła danych jako warstwy bąbelkowej na mapie. Warstwy bąbelków renderują punkty jako okręgi na mapie przy użyciu stałego promienia pikseli.

> [!TIP]
> Domyślnie warstwy bąbelków będą renderować współrzędne wszystkich geometrie w źródle danych. Aby ograniczyć warstwę tak, aby była renderowana tylko funkcja geometrii punktu, ustaw `filter` opcję warstwy na `eq(geometryType(), "Point")` . Jeśli chcesz również uwzględnić funkcje systemu MultiPoint, ustaw `filter` opcję warstwy na `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))` .

## <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że wykonano kroki opisane w dokumencie [Szybki Start: Tworzenie aplikacji dla systemu Android](quick-android-map.md) . Bloki kodu w tym artykule można wstawiać do `onReady` programu obsługi zdarzeń Maps.

## <a name="add-a-bubble-layer"></a>Dodawanie warstwy bąbelkowej

Poniższy kod ładuje tablicę punktów do źródła danych. Następnie łączy punkty danych z warstwą bąbelkową. Warstwa bąbelkowa renderuje promień każdego bąbelka z pięcioma pikselami i kolorem wypełnienia bieli. Kolor obrysu niebieskiego i szerokość obrysu wynoszącego sześć pikseli.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create point locations.
Point[] points = new Point[] {
    Point.fromLngLat(-73.985708, 40.75773),
    Point.fromLngLat(-73.985600, 40.76542),
    Point.fromLngLat(-73.985550, 40.77900),
    Point.fromLngLat(-73.975550, 40.74859),
    Point.fromLngLat(-73.968900, 40.78859)
};

//Add multiple points to the data source.
source.add(points);

//Create a bubble layer to render the filled in area of the circle, and add it to the map.
BubbleLayer layer = new BubbleLayer(source, 
    bubbleRadius(5f),
    bubbleColor("white"),
    bubbleStrokeColor("#4288f7"),
    bubbleStrokeWidth(6f)
);

map.layers.add(layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create point locations.
val points: Array<Point> = arrayOf<Point>(
    Point.fromLngLat(-73.985708, 40.75773),
    Point.fromLngLat(-73.985600, 40.76542),
    Point.fromLngLat(-73.985550, 40.77900),
    Point.fromLngLat(-73.975550, 40.74859),
    Point.fromLngLat(-73.968900, 40.78859)
)

//Add multiple points to the data source.
source.add(points)

//Create a bubble layer to render the filled in area of the circle, and add it to the map.
val layer = BubbleLayer(
    source,
    bubbleRadius(5f),
    bubbleColor("white"),
    bubbleStrokeColor("#4288f7"),
    bubbleStrokeWidth(6f)
)

map.layers.add(layer)
```

::: zone-end

Poniższy zrzut ekranu pokazuje powyższe punkty renderowania kodu w warstwie bąbelkowej.

![Mapuj z punktami renderowanymi przy użyciu warstwy bąbelków](media/map-add-bubble-layer-android/android-bubble-layer.png)

## <a name="show-labels-with-a-bubble-layer"></a>Wyświetlanie etykiet z warstwą bąbelkową

Ten kod pokazuje, jak używać warstwy bąbelkowej do renderowania punktu na mapie. I, jak używać warstwy symboli do renderowania etykiet. Aby ukryć ikonę warstwy symboli, należy ustawić `iconImage` opcję na `"none"` .

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Add a data point to the map.
source.add(Point.fromLngLat(-122.336641,47.627631));

//Add a bubble layer.
map.layers.add(new BubbleLayer(source,
    bubbleRadius(5f),
    bubbleColor("white"),
    bubbleStrokeColor("#4288f7"),
    bubbleStrokeWidth(6f)
));

//Add a symbol layer to display text, hide the icon image.
map.layers.add(new SymbolLayer(source,
    //Hide the icon image.
    iconImage("none"),
    textField("Museum of History & Industry (MOHAI)"),
    textColor("#005995"),
    textOffset(new Float[]{0f, -2.2f})
));
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Add a data point to the map.
source.add(Point.fromLngLat(-122.336641, 47.627631))

//Add a bubble layer.
map.layers.add(
    BubbleLayer(
        source,
        bubbleRadius(5f),
        bubbleColor("white"),
        bubbleStrokeColor("#4288f7"),
        bubbleStrokeWidth(6f)
    )
)

//Add a symbol layer to display text, hide the icon image.
map.layers.add(
    SymbolLayer(
        source,  //Hide the icon image.
        iconImage("none"),
        textField("Museum of History & Industry (MOHAI)"),
        textColor("#005995"),
        textOffset(arrayOf(0f, -2.2f))
    )
)
```

::: zone-end

Poniższy zrzut ekranu pokazuje powyższy kod rending punkt w warstwie bąbelkowej i etykietę tekstową dla punktu z warstwą symboli.

![Mapuj z punktem renderowanym przy użyciu warstwy bąbelków i etykiety tekstowej z warstwą symboli](media/map-add-bubble-layer-android/android-bubble-symbol-layer.png)

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami, aby uzyskać więcej przykładów kodu do dodania do Twoich map:

> [!div class="nextstepaction"]
> [Tworzenie źródła danych](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy symboli](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Korzystanie z wyrażeń stylu opartych na danych](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Wyświetlanie informacji o funkcjach](display-feature-information-android.md)
