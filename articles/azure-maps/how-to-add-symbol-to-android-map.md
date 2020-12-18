---
title: Dodawanie warstwy symboli do map systemu Android | Mapy Microsoft Azure
description: Dowiedz się, jak dodać znacznik do mapy. Zobacz przykład, który używa Android SDK Azure Maps, aby dodać warstwę symboli, która zawiera dane oparte na punktach ze źródła danych.
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 040fcde35707074ffaf102ed6c224b2f47a084bb
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97679350"
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

Istnieją trzy różne typy danych punktowych, które można dodać do mapy:

- Geometria punktu GEOJSON — ten obiekt zawiera tylko współrzędną punktu i nic innego. `Point.fromLngLat`Metoda statyczna może służyć do łatwego tworzenia tych obiektów.
- Geometria GEOJSON MultiPoint — ten obiekt zawiera współrzędne wielu punktów i nic innego. Przekaż tablicę punktów do klasy, `MultiPoint` Aby utworzyć te obiekty.
- Funkcja GEOJSON — ten obiekt składa się z geometrii GEOJSON oraz zestawu właściwości, które zawierają metadane skojarzone z geometrią.

Aby uzyskać więcej informacji, zobacz temat [Tworzenie dokumentu źródła danych](create-data-source-android-sdk.md) na potrzeby tworzenia i dodawania danych do mapy.

Poniższy przykład kodu tworzy geometrię punktu GEOJSON i przekazuje go do funkcji GEOJSON i ma `title` wartość dodaną do jej właściwości. `title`Właściwość jest wyświetlana jako tekst nad ikoną symbolu na mapie.

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

Poniższy zrzut ekranu przedstawia powyższy kod rending funkcję punktu przy użyciu ikony i etykiety tekstowej z warstwą symboli.

![Mapuj z punktem renderowanym przy użyciu warstwy symboli wyświetlającej ikonę i etykietę tekstową dla funkcji punktu](media/how-to-add-symbol-to-android-map/android-map-pin.png)

> [!TIP]
> Domyślnie warstwy symboli optymalizują renderowanie symboli, ukrywając symbole, które nakładają się na siebie. W miarę powiększania, ukryte symbole stają się widoczne. Aby wyłączyć tę funkcję i renderować wszystkie symbole przez cały czas, ustaw `iconAllowOverlap` Opcje i `textAllowOverlap` `true` .

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Dodaj niestandardową ikonę do warstwy symboli

Warstwy symboli są renderowane przy użyciu WebGL. Ponieważ wszystkie zasoby, takie jak obrazy ikon, muszą zostać załadowane do kontekstu WebGL. Ten przykład pokazuje, jak dodać niestandardową ikonę do zasobów mapy. Ta ikona służy następnie do renderowania danych punktu przy użyciu symbolu niestandardowego na mapie. `textField`Właściwość warstwy symboli wymaga określenia wyrażenia. W tym przypadku chcemy renderować Właściwość temperatury. Ponieważ temperatura jest liczbą, należy ją przekonwertować na ciąg. Ponadto chcemy dołączyć do niej "°F". Wyrażenie może służyć do tego łączenia; `concat(Expression.toString(get("temperature")), literal("°F"))`.

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

Na potrzeby tego przykładu Poniższy obraz został załadowany do folderu do rysowania aplikacji.

| ![Obraz ikony pogody natrysków deszczu](media/how-to-add-symbol-to-android-map/showers.png)|
|:-----------------------------------------------------------------------:|
| showers.png                                                  |

Poniższy zrzut ekranu przedstawia powyższy kod rending funkcję punktu przy użyciu ikony niestandardowej i sformatowanej etykiety tekstowej z warstwą symboli.

![Mapuj z punktem renderowanym przy użyciu warstwy symboli wyświetlającej ikonę niestandardową i sformatowaną etykietę tekstu dla funkcji punktu](media/how-to-add-symbol-to-android-map/android-custom-symbol-layer.png)

> [!TIP]
> Aby renderować tylko tekst z warstwą symboli, można ukryć ikonę przez ustawienie `iconImage` Właściwości opcji ikony na `"none"` .

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
