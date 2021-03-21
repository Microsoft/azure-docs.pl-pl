---
title: Dodawanie warstwy mapy cieplnej do map systemu Android | Mapy Microsoft Azure
description: Dowiedz się, jak utworzyć mapę cieplną. Zobacz, jak używać zestawu Azure MapsAndroid SDK, aby dodać warstwę mapy cieplnej do mapy. Dowiedz się, jak dostosować warstwy mapy cieplnej.
author: rbrundritt
ms.author: richbrun
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: fce2c2d007f92c43e763826f9345f773324e885e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102100189"
---
# <a name="add-a-heat-map-layer-android-sdk"></a>Dodaj warstwę mapy cieplnej (Android SDK)

Mapy cieplne, znane także jako mapy gęstości punktów, są typem wizualizacji danych. Są one używane do reprezentowania gęstości danych przy użyciu różnych kolorów i pokazujące dane "gorąca" na mapie. Mapy cieplne to doskonały sposób renderowania zestawów danych z dużą liczbą punktów. 

Renderowanie dziesiątek tysięcy punktów, ponieważ symbole mogą obejmować większość obszaru mapy. W takim przypadku najkorzystniej powstaje wiele symboli. Utrudnia to lepsze zrozumienie danych. Wizualizacja tego samego zestawu danych jako mapy cieplnej pozwala jednak łatwo sprawdzić gęstość i względną gęstość każdego punktu danych.

W wielu różnych scenariuszach można używać map cieplnych, takich jak:

- **Dane temperatury**: zawiera przybliżenia między dwoma punktami danych.
- **Dane dla czujników szumu**: pokazuje nie tylko intensywność hałasu, gdzie czujnik jest, ale może także zapewnić wgląd w rozproszenie na odległość. Poziom szumu w jednej lokacji może być niewysoki. Jeśli obszar pokrycia szumów z wielu czujników nakłada się na siebie, istnieje możliwość, że ten obszar nakładający się może mieć wyższy poziom szumu. W związku z tym, nakładający się obszar będzie widoczny na mapie cieplnej.
- **Śledzenie GPS**: zawiera szybkość jako mapę ważonej wysokości, w której intensywność każdego punktu danych jest oparta na szybkości. Na przykład ta funkcja zapewnia sposób, aby zobaczyć, gdzie nastąpiło przyspieszenie pojazdu.

> [!TIP]
> Warstwy mapy cieplnej domyślnie renderują współrzędne wszystkich geometrie w źródle danych. Aby ograniczyć warstwę tak, aby była renderowana tylko funkcja geometrii punktu, ustaw `filter` opcję warstwy na `eq(geometryType(), "Point")` . Jeśli chcesz również uwzględnić funkcje systemu MultiPoint, ustaw `filter` opcję warstwy na `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))` .

</br>

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player?format=ny]

## <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że wykonano kroki opisane w dokumencie [Szybki Start: Tworzenie aplikacji dla systemu Android](quick-android-map.md) . Bloki kodu w tym artykule można wstawiać do `onReady` programu obsługi zdarzeń Maps.

## <a name="add-a-heat-map-layer"></a>Dodawanie warstwy mapy cieplnej

Aby renderować źródło danych punktów jako mapę cieplną, Przekaż źródło danych do wystąpienia `HeatMapLayer` klasy i Dodaj je do mapy.

Poniższy przykład kodu ładuje źródło danych GEOJSON z ziemi z zeszłego tygodnia i renderuje je jako mapę cieplną. Każdy punkt danych jest renderowany przy użyciu promienia 10 pikseli na wszystkich poziomach powiększenia. Aby zapewnić lepsze środowisko użytkownika, Mapa cieplna znajduje się poniżej warstwy etykiet, dzięki czemu etykiety pozostają widoczne. Dane w tym przykładzie pochodzą z [programu agencji usgse zagrożenia ziemią](https://earthquake.usgs.gov/). Ten przykład służy do ładowania danych GEOJSON z sieci Web przy użyciu bloku kodu narzędzia do importowania danych dostępnego w dokumencie [Tworzenie źródła danych](create-data-source-android-sdk.md) .

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a heat map layer.
HeatMapLayer layer = new HeatMapLayer(source,
  heatmapRadius(10f),
  heatmapOpacity(0.8f)
);

//Add the layer to the map, below the labels.
map.layers.add(layer, "labels");

//Import the geojson data and add it to the data source.
Utils.importData("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson",
    this,
    (String result) -> {
        //Parse the data as a GeoJSON Feature Collection.
        FeatureCollection fc = FeatureCollection.fromJson(result);

        //Add the feature collection to the data source.
        source.add(fc);

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        BoundingBox bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),
            padding(20));
    });
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a heat map layer.
val layer = HeatMapLayer(
    source,
    heatmapRadius(10f),
    heatmapOpacity(0.8f)
)

//Add the layer to the map, below the labels.
map.layers.add(layer, "labels")

//Import the geojson data and add it to the data source.
Utils.importData("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson",
    this
) { result: String? ->
    //Parse the data as a GeoJSON Feature Collection.
    val fc = FeatureCollection.fromJson(result!!)

    //Add the feature collection to the data source.
    source.add(fc)

    //Optionally, update the maps camera to focus in on the data.
    //Calculate the bounding box of all the data in the Feature Collection.
    val bbox = MapMath.fromData(fc)

    //Update the maps camera so it is focused on the data.
    map.setCamera(
        bounds(bbox),
        padding(20)
    )
}
```

::: zone-end

Poniższy zrzut ekranu przedstawia mapę z załadowaniem mapy cieplnej za pomocą powyższego kodu.

![Mapa z warstwą mapy cieplnej ostatnich ziemi](media/map-add-heat-map-layer-android/android-heat-map-layer.png)

## <a name="customize-the-heat-map-layer"></a>Dostosowywanie warstwy mapy cieplnej

Poprzedni przykład dostosowany do mapy cieplnej przez ustawienie opcji promień i nieprzezroczystość. Warstwa mapy cieplnej oferuje kilka opcji dostosowywania, takich jak:

- `heatmapRadius`: Definiuje promień pikseli, w którym ma być renderowany każdy punkt danych. Promień można ustawić jako liczbę stałą lub jako wyrażenie. Za pomocą wyrażenia można skalować promień na podstawie poziomu powiększenia i reprezentować spójny obszar przestrzenny mapy (na przykład promień 5-milowej).
- `heatmapColor`: Określa sposób kolorowania mapy cieplnej. Gradient koloru jest popularną funkcją map ciepła. Efekt można osiągnąć za pomocą `interpolate` wyrażenia. Możesz również użyć `step` wyrażenia do kolorowania mapy cieplnej, dzieląc gęstość na siebie na zakresy podobne do mapy stylu konturu lub wykresu radarowego. Te palety kolorów definiują kolory z minimum do wartości maksymalnej gęstości.

  Możesz określić wartości koloru dla map cieplnych jako wyrażenie w `heatmapDensity` wartości. Kolor obszaru, w którym nie ma żadnych danych, jest zdefiniowany przy indeksie 0 wyrażenia "Interpolacja" lub domyślnym kolorem wyrażenia "". Tej wartości można użyć do zdefiniowania koloru tła. Często ta wartość jest ustawiona na przezroczysty lub półprzezroczysty kolor przezroczysty. 

  Oto przykłady wyrażeń koloru:

  | Wyrażenie koloru interpolacji | Wyrażenie koloru z możliwością |
  |--------------------------------|--------------------------|
  | interpolować<br/>&nbsp;&nbsp;&nbsp;&nbsp;liniowy (), <br/>&nbsp;&nbsp;&nbsp;&nbsp;heatmapDensity(),<br/>&nbsp;&nbsp;&nbsp;&nbsp;Zatrzymaj (0, Color (Color. TRANSPARENT)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;Stop (0,01, Color (Color. MAGENTA)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;Stop (0,5, Color) (parseColor ("#fb00fb")),<br/>&nbsp;&nbsp;&nbsp;&nbsp;Stop (1, Color (#00c3ff parseColor))<br/>)` | czynności<br/>&nbsp;&nbsp;&nbsp;&nbsp;heatmapDensity(),<br/>&nbsp;&nbsp;&nbsp;&nbsp;kolor (Color. TRANSPARENT),<br/>&nbsp;&nbsp;&nbsp;&nbsp;Stop (0,01, Color (parseColor ("#000080"))),<br/>&nbsp;&nbsp;&nbsp;&nbsp;Stop (0,25, Color (parseColor ("#000080"))),<br/>&nbsp;&nbsp;&nbsp;&nbsp;Stop (0,5, Color),<br/>&nbsp;&nbsp;&nbsp;&nbsp;Zatrzymaj (0,5, Color (Color. YELLOW)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;Stop (1, Color))<br/>) |

- `heatmapOpacity`: Określa sposób nieprzezroczystości lub przezroczystości warstwy mapy cieplnej.
- `heatmapIntensity`: Stosuje mnożnik do wagi każdego punktu danych w celu zwiększenia całkowitego intensywności mapę cieplną. Powoduje to różnicę wagi punktów danych, ułatwiając wizualizację.
- `heatmapWeight`: Domyślnie wszystkie punkty danych mają wagę 1 i są ważone w równym stopniu. Opcja wagi pełni rolę mnożnika i można ją ustawić jako liczbę lub wyrażenie. Jeśli liczba jest ustawiona jako waga, to równoważność umieszczania każdego punktu danych na mapie dwa razy. Na przykład jeśli waga to `2` , wówczas gęstość podwaja się. Ustawienie opcji wagi na liczbę renderuje mapę cieplną w podobny sposób, aby użyć opcji intensywność.

  Jeśli jednak używasz wyrażenia, waga każdego punktu danych może opierać się na właściwościach poszczególnych punktów danych. Załóżmy na przykład, że każdy punkt danych reprezentuje trzęsienie ziemi. Wartość wielkości jest ważną metryką dla każdego punktu danych trzęsienia. Zdarza się cały czas, ale większość z nich ma niską wartość i nie są zauważalne. Użyj wartości wielkości w wyrażeniu, aby przypisać wagę do każdego punktu danych. Przy użyciu wartości wielkości do przypisywania wagi, uzyskuje się lepszą reprezentację znaczenia ziemi w ramach mapy cieplnej.
- `minZoom` i `maxZoom` : zakres poziomu powiększenia, w którym powinna być wyświetlana warstwa.
- `filter`: Wyrażenie filtru używane do ograniczenia pobrane ze źródła i renderowane w warstwie.
- `sourceLayer`: Jeśli źródło danych połączone z warstwą jest źródłem kafelka wektorowego, należy określić warstwę źródłową wewnątrz kafelków wektorowych.
- `visible`: Ukrywa lub pokazuje warstwę.

Poniżej znajduje się przykład mapy cieplnej, w której wyrażenie interpolacji liniowej służy do tworzenia gradientu gładkiego koloru. `mag`Właściwość zdefiniowana w danych jest używana z interpolacją wykładniczą do ustawiania wagi lub znaczenia poszczególnych punktów danych.

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
    heatmapRadius(10f),

    //A linear interpolation is used to create a smooth color gradient based on the heat map density.
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.BLACK)),
            stop(0.25, color(Color.MAGENTA)),
            stop(0.5, color(Color.RED)),
            stop(0.75, color(Color.YELLOW)),
            stop(1, color(Color.WHITE))
        )
    ),

    //Using an exponential interpolation since earthquake magnitudes are on an exponential scale.
    heatmapWeight(
       interpolate(
            exponential(2),
            get("mag"),
            stop(0,0),

            //Any earthquake above a magnitude of 6 will have a weight of 1
            stop(6, 1)
       )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
    heatmapRadius(10f),

    //A linear interpolation is used to create a smooth color gradient based on the heat map density.
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.BLACK)),
            stop(0.25, color(Color.MAGENTA)),
            stop(0.5, color(Color.RED)),
            stop(0.75, color(Color.YELLOW)),
            stop(1, color(Color.WHITE))
        )
    ),

    //Using an exponential interpolation since earthquake magnitudes are on an exponential scale.
    heatmapWeight(
       interpolate(
            exponential(2),
            get("mag"),
            stop(0,0),

            //Any earthquake above a magnitude of 6 will have a weight of 1
            stop(6, 1)
       )
    )
)
```

::: zone-end

Poniższy zrzut ekranu przedstawia powyższą niestandardową warstwę mapy cieplnej, korzystając z tych samych danych z poprzedniego przykładu mapy cieplnej.

![Mapowanie przy użyciu warstwy niestandardowa Mapa cieplna ostatnich ziemi](media/map-add-heat-map-layer-android/android-custom-heat-map-layer.png)

## <a name="consistent-zoomable-heat-map"></a>Spójna Mapa cieplna z powiększaniem

Domyślnie promienie punktów danych renderowane w warstwie mapy cieplnej mają stały promień pikseli dla wszystkich poziomów powiększenia. W miarę powiększania mapy dane zagregowane i warstwy mapy cieplnej wyglądają inaczej. Poniższy film wideo pokazuje domyślne zachowanie mapy cieplnej, gdzie utrzymuje promień pikseli podczas powiększania mapy.

![Animacja pokazująca powiększanie mapy za pomocą warstwy mapy cieplnej pokazująca spójny rozmiar pikseli](media/map-add-heat-map-layer-android/android-heat-map-layer-zoom.gif)

Użyj `zoom` wyrażenia, aby skalować promień dla każdego poziomu powiększenia, w taki sposób, że każdy punkt danych obejmuje ten sam obszar fizyczny mapy. To wyrażenie sprawia, że warstwa mapy cieplnej jest bardziej statyczna i spójna. Każdy poziom powiększenia mapy ma dwa piksele w pionie i poziomie jako poprzedni poziom powiększenia.

Skalowanie promienia tak, aby podwajał się przy każdym poziomie powiększenia tworzy mapę cieplną, która wygląda spójnie na wszystkich poziomach powiększenia. Aby zastosować to skalowanie, należy użyć `zoom` z `exponential interpolation` wyrażeniem podstawowym 2, z zestawem pikseli dla minimalnego poziomu powiększenia i skalowanego promienia dla maksymalnego poziomu powiększenia obliczonego jak `2 * Math.pow(2, minZoom - maxZoom)` pokazano w poniższym przykładzie. Powiększ mapę, aby zobaczyć, jak mapa cieplna jest skalowana z poziomem powiększenia.

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
  heatmapRadius(
    interpolate(
      exponential(2),
      zoom(),

      //For zoom level 1 set the radius to 2 pixels.
      stop(1, 2f),

      //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
      stop(19, Math.pow(2, 19 - 1) * 2f)
    )
  ),
  heatmapOpacity(0.75f)
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
  heatmapRadius(
    interpolate(
      exponential(2),
      zoom(),

      //For zoom level 1 set the radius to 2 pixels.
      stop(1, 2f),

      //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
      stop(19, Math.pow(2.0, 19 - 1.0) * 2f)
    )
  ),
  heatmapOpacity(0.75f)
)
```

::: zone-end

Poniższy film wideo pokazuje mapę, w której znajduje się powyższy kod, co skaluje promień, podczas gdy mapa jest powiększona, aby utworzyć spójne renderowanie mapy cieplnej na poziomach powiększenia.

![Animacja pokazująca powiększanie mapy za pomocą warstwy mapy cieplnej pokazująca spójny rozmiar geoprzestrzenny](media/map-add-heat-map-layer-android/android-consistent-zoomable-heat-map-layer.gif)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej przykładów kodu do dodania do usługi Maps, zobacz następujące artykuły:

> [!div class="nextstepaction"]
> [Tworzenie źródła danych](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Korzystanie z wyrażeń stylu opartych na danych](data-driven-style-expressions-android-sdk.md)
