---
title: Ustawianie stylu mapy w usłudze mapy systemu Android | Mapy Microsoft Azure
description: Poznaj dwa sposoby ustawiania stylu mapy. Aby dostosować styl, zobacz jak używać Android SDK Azure Maps w pliku układu lub w klasie Activity.
author: rbrundritt
ms.author: richbrun
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 9dcb5b84faa47b7307ce8441003e8591d7c2757b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604791"
---
# <a name="set-map-style-android-sdk"></a>Ustaw styl mapy (Android SDK)

W tym artykule przedstawiono dwa sposoby ustawiania stylów mapy przy użyciu Android SDK Azure Maps. Azure Maps ma sześć różnych stylów map do wyboru. Aby uzyskać więcej informacji na temat obsługiwanych stylów mapy, zobacz [obsługiwane style mapy w Azure Maps](supported-map-styles.md).

## <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że wykonano kroki opisane w dokumencie [Szybki Start: Tworzenie aplikacji dla systemu Android](quick-android-map.md) .

## <a name="set-map-style-in-the-layout"></a>Ustawianie stylu mapy w układzie

Po dodaniu kontrolki mapy można ustawić styl mapy w pliku układu dla swojej klasy działania. Poniższy kod ustawia lokalizację centrum, poziom powiększenia i styl mapy.

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/mapcontrol"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_centerLat="47.602806"
    app:mapcontrol_centerLng="-122.329330"
    app:mapcontrol_zoom="12"
    app:mapcontrol_style="grayscale_dark"
    />
```

Poniższy zrzut ekranu przedstawia powyższy kod, który wyświetla mapę drogi z ciemnego stylu skali szarości.

![Mapa z stylem mapy drogowej z ciemną skalą szarości](media/set-android-map-styles/android-grayscale-dark.png)

## <a name="set-map-style-in-code"></a>Ustawianie stylu mapy w kodzie

Styl mapy można skonfigurować programowo w kodzie przy użyciu `setStyle` metody mapy. Poniższy kod ustawia lokalizację centrum i poziom powiększenia przy użyciu `setCamera` metody Maps oraz stylu mapy `SATELLITE_ROAD_LABELS` .

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {

    //Set the camera of the map.
    map.setCamera(center(Point.fromLngLat(-122.33, 47.64)), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE_ROAD_LABELS));
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl!!.onReady { map: AzureMap ->
    //Set the camera of the map.
    map.setCamera(center(Point.fromLngLat(-122.33, 47.64)), zoom(14))

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE_ROAD_LABELS))
}
```

::: zone-end

Poniższy zrzut ekranu przedstawia powyższy kod zawierający mapę z stylem satelitarnych etykiet.

![Mapowanie przy użyciu stylu etykiet dróg satelitarnych](media/set-android-map-styles/android-satellite-road-labels.png)

## <a name="setting-the-map-camera"></a>Ustawianie aparatu mapy

Formant mapy, która jest częścią świata wyświetlana w okienku ekranu mapy. Aparat może być w tym samym układzie programowo w kodzie. Gdy ustawiasz ją w kodzie, istnieją dwie główne metody ustawiania pozycji mapy; Używanie centrum i powiększanie lub przekazywanie w polu ograniczenia. Poniższy kod pokazuje, jak ustawić wszystkie opcjonalne opcje kamery podczas korzystania z `center` i `zoom` .

::: zone pivot="programming-language-java-android"

```java
//Set the camera of the map using center and zoom.
map.setCamera(
    center(Point.fromLngLat(-122.33, 47.64)), 

    //The zoom level. Typically a value between 0 and 22.
    zoom(14),

    //The amount of tilt in degrees the map where 0 is looking straight down.
    pitch(45),

    //Direction the top of the map is pointing in degrees. 0 = North, 90 = East, 180 = South, 270 = West
    bearing(90),

    //The minimum zoom level the map will zoom-out to when animating from one location to another on the map.
    minZoom(10),
    
    //The maximum zoom level the map will zoom-in to when animating from one location to another on the map.
    maxZoom(14)
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Set the camera of the map using center and zoom.
map.setCamera(
    center(Point.fromLngLat(-122.33, 47.64)), 

    //The zoom level. Typically a value between 0 and 22.
    zoom(14),

    //The amount of tilt in degrees the map where 0 is looking straight down.
    pitch(45),

    //Direction the top of the map is pointing in degrees. 0 = North, 90 = East, 180 = South, 270 = West
    bearing(90),

    //The minimum zoom level the map will zoom-out to when animating from one location to another on the map.
    minZoom(10),
    
    //The maximum zoom level the map will zoom-in to when animating from one location to another on the map.
    maxZoom(14)
)
```

::: zone-end

Często pożądane jest skoncentrowanie mapy na zestawie danych. Pole ograniczenia można obliczyć na podstawie funkcji przy użyciu `MapMath.fromData` metody i można je przekazywać do `bounds` opcji aparatu mapy. Podczas ustawiania widoku mapy na podstawie pola ograniczenia często warto określić `padding` wartość do konta dla rozmiaru pikseli dla punktów renderowanych jako bąbelki lub symbole. Poniższy kod pokazuje, jak ustawić wszystkie opcjonalne opcje kamery przy użyciu pola ograniczenia, aby ustawić pozycję aparatu.

::: zone pivot="programming-language-java-android"

```java
//Set the camera of the map using a bounding box.
map.setCamera(
    //The area to focus the map on.
    bounds(BoundingBox.fromLngLats(
        //West
        -122.4594,

        //South
        47.4333,
        
        //East
        -122.21866,
        
        //North
        47.75758
    )),

    //Amount of pixel buffer around the bounding box to provide extra space around the bounding box.
    padding(20),

    //The maximum zoom level the map will zoom-in to when animating from one location to another on the map.
    maxZoom(14)
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Set the camera of the map using a bounding box.
map.setCamera(
    //The area to focus the map on.
    bounds(BoundingBox.fromLngLats(
        //West
        -122.4594,

        //South
        47.4333,
        
        //East
        -122.21866,
        
        //North
        47.75758
    )),

    //Amount of pixel buffer around the bounding box to provide extra space around the bounding box.
    padding(20),

    //The maximum zoom level the map will zoom-in to when animating from one location to another on the map.
    maxZoom(14)
)
```

::: zone-end

Współczynnik proporcji pola ograniczenia nie może być taki sam jak współczynnik proporcji mapy, ponieważ w takiej formie Mapa często będzie wyświetlać pełen obszar pola ograniczenia, ale często jest to możliwe tylko w pionie lub w poziomie.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami, aby uzyskać więcej przykładów kodu do dodania do Twoich map:

> [!div class="nextstepaction"]
> [Dodawanie warstwy symboli](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy bąbelkowej](map-add-bubble-layer-android.md)
