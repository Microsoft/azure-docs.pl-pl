---
title: Dodawanie warstwy symboli do map systemu Android | Mapy Microsoft Azure
description: Dowiedz się, jak dodać znacznik do mapy. Zobacz przykład, który używa Android SDK Azure Maps, aby dodać warstwę symboli, która zawiera dane oparte na punktach ze źródła danych.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: edb758469a06dcb7914025ea449b9d952e939533
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102097214"
---
# <a name="add-a-symbol-layer-android-sdk"></a>Dodaj warstwę symboli (Android SDK)

W tym artykule pokazano, jak renderować dane punktu ze źródła danych jako warstwę symboli na mapie przy użyciu Android SDK Azure Maps. Warstwy symboli renderują punkty jako obraz i tekst na mapie.

> [!TIP]
> Warstwy symboli domyślnie będą renderować współrzędne wszystkich geometrie w źródle danych. Aby ograniczyć warstwę tak, aby była renderowana tylko funkcja geometrii punktu, ustaw `filter` opcję warstwy na `eq(geometryType(), "Point")` . Jeśli chcesz również uwzględnić funkcje systemu MultiPoint, ustaw `filter` opcję warstwy na `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))` .

## <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że wykonano kroki opisane w dokumencie [Szybki Start: Tworzenie aplikacji dla systemu Android](quick-android-map.md) . Bloki kodu w tym artykule można wstawiać do `onReady` programu obsługi zdarzeń Maps.

## <a name="add-a-symbol-layer"></a>Dodawanie warstwy symboli

Aby można było dodać warstwę symboli do mapy, należy wykonać kilka kroków. Najpierw Utwórz źródło danych i Dodaj je do mapy. Utwórz warstwę symboli. Następnie Przekaż źródło danych do warstwy symboli, aby pobrać dane ze źródła danych. Na koniec Dodaj dane do źródła danych, aby było możliwe renderowanie.

Poniższy kod pokazuje, co należy dodać do mapy po jej załadowaniu. Ten przykład renderuje pojedynczy punkt na mapie za pomocą warstwy symboli.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point and add it to the data source.
source.add(Point.fromLngLat(0, 0));

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source);

//Add the layer to the map.
map.layers.add(layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a point and add it to the data source.
source.add(Point.fromLngLat(0, 0))

//Create a symbol layer to render icons and/or text at points on the map.
val layer = SymbolLayer(source)

//Add the layer to the map.
map.layers.add(layer)
```

::: zone-end

Istnieją trzy różne typy danych punktowych, które można dodać do mapy:

- Geometria punktu GEOJSON — ten obiekt zawiera tylko współrzędną punktu i nic innego. `Point.fromLngLat`Metoda statyczna może służyć do łatwego tworzenia tych obiektów.
- Geometria GEOJSON MultiPoint — ten obiekt zawiera współrzędne wielu punktów i nic innego. Przekaż tablicę punktów do klasy, `MultiPoint` Aby utworzyć te obiekty.
- Funkcja GEOJSON — ten obiekt składa się z geometrii GEOJSON oraz zestawu właściwości, które zawierają metadane skojarzone z geometrią.

Aby uzyskać więcej informacji, zobacz temat [Tworzenie dokumentu źródła danych](create-data-source-android-sdk.md) na potrzeby tworzenia i dodawania danych do mapy.

Poniższy przykład kodu tworzy geometrię punktu GEOJSON i przekazuje go do funkcji GEOJSON i ma `title` wartość dodaną do jej właściwości. `title`Właściwość jest wyświetlana jako tekst nad ikoną symbolu na mapie.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(0, 0));

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!");

//Add the feature to the data source.
source.add(feature);

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source, 
    //Get the title property of the feature and display it on the map.
    textField(get("title"))
);

//Add the layer to the map.
map.layers.add(layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a point feature.
val feature = Feature.fromGeometry(Point.fromLngLat(0, 0))

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!")

//Add the feature to the data source.
source.add(feature)

//Create a symbol layer to render icons and/or text at points on the map.
val layer = SymbolLayer(
    source,  //Get the title property of the feature and display it on the map.
    textField(get("title"))
)

//Add the layer to the map.
map.layers.add(layer)
```

::: zone-end

Poniższy zrzut ekranu przedstawia powyższy kod rending funkcję punktu przy użyciu ikony i etykiety tekstowej z warstwą symboli.

![Mapuj z punktem renderowanym przy użyciu warstwy symboli wyświetlającej ikonę i etykietę tekstową dla funkcji punktu](media/how-to-add-symbol-to-android-map/android-map-pin.png)

> [!TIP]
> Domyślnie warstwy symboli optymalizują renderowanie symboli, ukrywając symbole, które nakładają się na siebie. W miarę powiększania, ukryte symbole stają się widoczne. Aby wyłączyć tę funkcję i renderować wszystkie symbole przez cały czas, ustaw `iconAllowOverlap` Opcje i `textAllowOverlap` `true` .

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Dodaj niestandardową ikonę do warstwy symboli

Warstwy symboli są renderowane przy użyciu WebGL. Ponieważ wszystkie zasoby, takie jak obrazy ikon, muszą zostać załadowane do kontekstu WebGL. Ten przykład pokazuje, jak dodać niestandardową ikonę do zasobów mapy. Ta ikona służy następnie do renderowania danych punktu przy użyciu symbolu niestandardowego na mapie. `textField`Właściwość warstwy symboli wymaga określenia wyrażenia. W tym przypadku chcemy renderować Właściwość temperatury. Ponieważ temperatura jest liczbą, należy ją przekonwertować na ciąg. Ponadto chcemy dołączyć do niej "°F". Wyrażenie może służyć do tego łączenia; `concat(Expression.toString(get("temperature")), literal("°F"))`.

::: zone pivot="programming-language-java-android"

```java
//Load a custom icon image into the image sprite of the map.
map.images.add("my-custom-icon", R.drawable.showers);

//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-73.985708, 40.75773));

//Add a property to the feature.
feature.addNumberProperty("temperature", 64);

//Add the feature to the data source.
source.add(feature);

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source,
    iconImage("my-custom-icon"),
    iconSize(0.5f),

    //Get the title property of the feature and display it on the map.
    textField(concat(Expression.toString(get("temperature")), literal("°F"))),
    textOffset(new Float[]{0f, -1.5f})
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Load a custom icon image into the image sprite of the map.
map.images.add("my-custom-icon", R.drawable.showers)

//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a point feature.
val feature = Feature.fromGeometry(Point.fromLngLat(-73.985708, 40.75773))

//Add a property to the feature.
feature.addNumberProperty("temperature", 64)

//Add the feature to the data source.
source.add(feature)

//Create a symbol layer to render icons and/or text at points on the map.
val layer = SymbolLayer(
    source,
    iconImage("my-custom-icon"),
    iconSize(0.5f),  //Get the title property of the feature and display it on the map.
    textField(concat(Expression.toString(get("temperature")), literal("°F"))),
    textOffset(arrayOf(0f, -1.5f))
)
```

::: zone-end

Na potrzeby tego przykładu Poniższy obraz został załadowany do folderu do rysowania aplikacji.

| ![Obraz ikony pogody natrysków deszczu](media/how-to-add-symbol-to-android-map/showers.png)|
|:-----------------------------------------------------------------------:|
| showers.png                                                  |

Poniższy zrzut ekranu przedstawia powyższy kod rending funkcję punktu przy użyciu ikony niestandardowej i sformatowanej etykiety tekstowej z warstwą symboli.

![Mapuj z punktem renderowanym przy użyciu warstwy symboli wyświetlającej ikonę niestandardową i sformatowaną etykietę tekstu dla funkcji punktu](media/how-to-add-symbol-to-android-map/android-custom-symbol-layer.png)

> [!TIP]
> Aby renderować tylko tekst z warstwą symboli, można ukryć ikonę przez ustawienie `iconImage` Właściwości opcji ikony na `"none"` .

## <a name="modify-symbol-colors"></a>Modyfikuj kolory symboli

Android SDK Azure Maps jest dostarczany z zestawem wstępnie zdefiniowanych wariantów koloru ikony domyślnego znacznika. Na przykład `marker-red` można przesłać do `iconImage` opcji warstwy symboli, aby renderować czerwoną wersję ikony znacznika w tej warstwie.

::: zone pivot="programming-language-java-android"

```java
SymbolLayer layer = new SymbolLayer(source,
    iconImage("marker-red")
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = SymbolLayer(source,
    iconImage("marker-red")
)
```

::: zone-end

W poniższej tabeli wymieniono wszystkie wbudowane nazwy obrazów ikon. Wszystkie te znaczniki pobierają kolory z zasobów kolorów, które można przesłonić. Poza zastępowaniem głównego koloru wypełnienia tego znacznika. Należy jednak pamiętać, że zastępowanie koloru jednego z tych znaczników będzie miało zastosowanie do wszystkich warstw, które używają tego obrazu ikony.

| Nazwa obrazu ikony | Nazwa zasobu koloru |
|-----------------|---------------------|
| `marker-default` | `mapcontrol_marker_default` |
| `marker-black` | `mapcontrol_marker_black` |
| `marker-blue` | `mapcontrol_marker_blue` |
| `marker-darkblue` | `mapcontrol_marker_darkblue` |
| `marker-red` | `mapcontrol_marker_red` |
| `marker-yellow` | `mapcontrol_marker_yellow` |

Możesz również zastąpić kolor obramowania wszystkich znaczników przy użyciu `mapcontrol_marker_border` nazwy zasobu koloru. Kolory tych znaczników można przesłonić, dodając kolor o tej samej nazwie w `colors.xml` pliku aplikacji. Na przykład następujący `colors.xml` plik spowoduje, że domyślny kolor znacznika zostanie jaśniejszy na zielony.

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <color name="mapcontrol_marker_default">#00FF00</color>
</resources>
```

Poniżej znajduje się zmodyfikowana wersja domyślnego pliku XML wektora znaczników, którą można modyfikować, aby utworzyć dodatkowe niestandardowe wersje domyślnego znacznika. Zmodyfikowaną wersję można dodać do `drawable` folderu aplikacji i dodać do ikonki obrazu mapy przy użyciu `map.images.add` , a następnie użyć z warstwą symboli.

```xml
<vector xmlns:android="http://schemas.android.com/apk/res/android"
    android:width="24.5dp"
    android:height="36.5dp"
    android:viewportWidth="24.5"
    android:viewportHeight="36.5">
    <path
        android:pathData="M12.25,0.25a12.2543,12.2543 0,0 0,-12 12.4937c0,6.4436 6.4879,12.1093 11.059,22.5641 0.5493,1.2563 1.3327,1.2563 1.882,0C17.7621,24.8529 24.25,19.1857 24.25,12.7437A12.2543,12.2543 0,0 0,12.25 0.25Z"
        android:strokeWidth="0.5"
        android:fillColor="@color/mapcontrol_marker_default"
        android:strokeColor="@color/mapcontrol_marker_border"/>
</vector>
```

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami, aby uzyskać więcej przykładów kodu do dodania do Twoich map:

> [!div class="nextstepaction"]
> [Tworzenie źródła danych](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy bąbelkowej](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Korzystanie z wyrażeń stylu opartych na danych](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Wyświetlanie informacji o funkcjach](display-feature-information-android.md)
