---
title: Dodawanie warstwy kafelków do map systemu Android | Mapy Microsoft Azure
description: Dowiedz się, jak dodać warstwę kafelków do mapy. Zobacz przykład, który używa Android SDK Azure Maps, aby dodać nakładkę radaru do mapy.
author: rbrundritt
ms.author: richbrun
ms.date: 3/25/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: ac37a4e6d68decdf6780560963a0c534689e8dbb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608989"
---
# <a name="add-a-tile-layer-to-a-map-android-sdk"></a>Dodawanie warstwy kafelków do mapy (Android SDK)

W tym artykule przedstawiono sposób renderowania warstwy kafelków na mapie przy użyciu Android SDK Azure Maps. Warstwy kafelków umożliwiają nakładanie obrazów na kafelkach mapy podstawowej Azure Maps. Więcej informacji o Azure Maps systemie rozmieszczania można znaleźć w dokumentacji [poziomów powiększenia i siatki kafelków](zoom-levels-and-tile-grid.md) .

Warstwa kafelków jest ładowana na kafelkach z serwera. Te obrazy mogą być wstępnie renderowane i przechowywane jak każdy inny obraz na serwerze, przy użyciu konwencji nazewnictwa, która jest rozpoznawana przez warstwę kafelków. Można też renderować te obrazy za pomocą usługi dynamicznej, która generuje obrazy niemal w czasie rzeczywistym. Istnieją trzy różne konwencje nazewnictwa usługi kafelków obsługiwane przez Azure Maps TileLayer klasy:

* X, Y, z notacją powiększenia w oparciu o poziom powiększenia, x to kolumna, a Y to pozycja w wierszu kafelka w siatce kafelków.
* Quadkey-kombinacja x, y, Powiększ informacje w postaci pojedynczej wartości ciągu, która jest unikatowym identyfikatorem dla kafelka.
* Współrzędne pola ograniczenia obwiedni mogą służyć do określania obrazu w formacie `{west},{south},{east},{north}` , który jest często używany przez [usługi mapowania sieci Web (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> TileLayer to doskonały sposób wizualizacji dużych zestawów danych na mapie. Nie tylko można wygenerować warstwy kafelków z obrazu, ale dane wektorowe mogą być również renderowane jako warstwa kafelków. Przez renderowanie danych wektorowych jako warstwy kafelków, formant mapy musi ładować kafelki, które mogą być znacznie mniejsze w rozmiarze pliku niż dane wektorowe, które reprezentują. Ta technika jest używana przez wiele osób, które muszą renderować miliony wierszy danych na mapie.

Adres URL kafelka przesłany do warstwy kafelków musi być adresem URL protokołu HTTP/HTTPS do zasobu TileJSON lub szablonem adresu URL kafelka, który używa następujących parametrów:

* `{x}` -X pozycja kafelka. Również wymagają `{y}` i `{z}` .
* `{y}` -Y pozycja kafelka. Również wymagają `{x}` i `{z}` .
* `{z}` — Poziom powiększenia kafelka. Również wymagają `{x}` i `{y}` .
* `{quadkey}` -Kafelek quadkey identyfikator oparty na konwencji nazewnictwa systemu kafelków mapy Bing.
* `{bbox-epsg-3857}` -Ciąg pola granicznego z formatem `{west},{south},{east},{north}` w systemie referencyjnym przestrzennym EPSG 3857.
* `{subdomain}` — Symbol zastępczy wartości poddomeny, jeśli określono wartość poddomeny.
* `azmapsdomain.invalid` — Symbol zastępczy, aby wyrównać domenę i uwierzytelnianie żądań kafelków z tymi samymi wartościami, które są używane przez mapę. Użyj tego podczas wywoływania usługi kafelka hostowanej przez Azure Maps.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć proces w tym artykule, należy zainstalować [Azure Maps Android SDK](how-to-use-android-map-control-library.md) w celu załadowania mapy.

## <a name="add-a-tile-layer-to-the-map"></a>Dodawanie warstwy kafelków do mapy

Ten przykład pokazuje, jak utworzyć warstwę kafelków, która wskazuje zestaw kafelków. Ten przykład używa systemu dzielenia "x, y, zoom". Źródłem tej warstwy kafelków jest [projekt OpenSeaMap](https://openseamap.org/index.php), który zawiera wykresy mil morskich ze źródłem. Często podczas wyświetlania warstw kafelków wskazane jest, aby wyraźnie widzieć etykiety miast na mapie. Takie zachowanie można osiągnąć, wstawiając warstwę kafelków poniżej warstw etykiet mapy.

::: zone pivot="programming-language-java-android"

```java
TileLayer layer = new TileLayer(
    tileUrl("https://tiles.openseamap.org/seamark/{z}/{x}/{y}.png"),
    opacity(0.8f),
    tileSize(256),
    minSourceZoom(7),
    maxSourceZoom(17)
);

map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = TileLayer(
    tileUrl("https://tiles.openseamap.org/seamark/{z}/{x}/{y}.png"),
    opacity(0.8f),
    tileSize(256),
    minSourceZoom(7),
    maxSourceZoom(17)
)

map.layers.add(layer, "labels")
```

::: zone-end

Poniższy zrzut ekranu przedstawia powyższy kod wyświetlający warstwę kafelka informacji morskich na mapie, która ma styl ciemny w skali szarości.

![Mapa systemu Android — wyświetlanie warstwy kafelków](media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)

## <a name="add-an-ogc-web-mapping-service-wms"></a>Dodaj usługę mapowania sieci Web OGC (WMS)

Usługa mapowania sieci Web (WMTS) jest standardem programu Open Geospatial Consortium (OGC) służącym do obsługi obrazów danych mapy. W tym formacie jest dostępnych wiele otwartych zestawów danych, których można używać z Azure Maps. Tego typu usługi można użyć w połączeniu z warstwą kafelka, jeśli usługa obsługuje `EPSG:3857` system odniesienia współrzędnych (KSR). W przypadku korzystania z usługi WMS ustaw parametry width i Height na taką samą wartość, która jest obsługiwana przez usługę, pamiętaj, aby ustawić tę samą wartość w `tileSize` opcji. W sformatowanym adresie URL ustaw `BBOX` parametr usługi z `{bbox-epsg-3857}` symbolem zastępczym.

::: zone pivot="programming-language-java-android"

``` java
TileLayer layer = new TileLayer(
    tileUrl("https://mrdata.usgs.gov/services/gscworld?FORMAT=image/png&HEIGHT=1024&LAYERS=geology&REQUEST=GetMap&STYLES=default&TILED=true&TRANSPARENT=true&WIDTH=1024&VERSION=1.3.0&SERVICE=WMS&CRS=EPSG:3857&BBOX={bbox-epsg-3857}"),
    tileSize(1024)
);

map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = TileLayer(
    tileUrl("https://mrdata.usgs.gov/services/gscworld?FORMAT=image/png&HEIGHT=1024&LAYERS=geology&REQUEST=GetMap&STYLES=default&TILED=true&TRANSPARENT=true&WIDTH=1024&VERSION=1.3.0&SERVICE=WMS&CRS=EPSG:3857&BBOX={bbox-epsg-3857}"),
    tileSize(1024)
)

map.layers.add(layer, "labels")
```

::: zone-end

Poniższy zrzut ekranu pokazuje, że powyższy kod nakładają usługę mapowania sieci Web o dane geologicznej z [(agencji USGS)](https://mrdata.usgs.gov/) na mapie, poniżej etykiet.

![Mapa systemu Android wyświetlająca warstwę kafelków WMS](media/how-to-add-tile-layer-android-map/android-tile-layer-wms.jpg)

## <a name="add-an-ogc-web-mapping-tile-service-wmts"></a>Dodaj usługę kafelków OGC Web Mapping (WMTS)

Usługa kafelków map sieci Web (WMTS) jest standardem Open Geospatial Consortium (OGC) w celu obsłużenia opartymi na kafelkach nakładki dla map. W tym formacie jest dostępnych wiele otwartych zestawów danych, których można używać z Azure Maps. Tego typu usługi można użyć w połączeniu z warstwą kafelka, jeśli usługa obsługuje `EPSG:3857` `GoogleMapsCompatible` system odniesienia lub układ współrzędnych (KSR). W przypadku korzystania z usługi WMTS ustaw parametry width i Height na taką samą wartość, która jest obsługiwana przez usługę, pamiętaj, aby ustawić tę samą wartość w `tileSize` opcji. W sformatowanym adresie URL Zastąp odpowiednio następujące symbole zastępcze:

* `{TileMatrix}` => `{z}`
* `{TileRow}` => `{y}`
* `{TileCol}` => `{x}`

::: zone pivot="programming-language-java-android"

``` java
TileLayer layer = new TileLayer(
    tileUrl("https://basemap.nationalmap.gov/arcgis/rest/services/USGSImageryOnly/MapServer/WMTS/tile/1.0.0/USGSImageryOnly/default/GoogleMapsCompatible/{z}/{y}/{x}"),
    tileSize(256),
    bounds(-173.25000107492872, 0.0005794121990209753, 146.12527718104752, 71.506811402077),
    maxSourceZoom(18)
);

map.layers.add(layer, "transit");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = TileLayer(
    tileUrl("https://basemap.nationalmap.gov/arcgis/rest/services/USGSImageryOnly/MapServer/WMTS/tile/1.0.0/USGSImageryOnly/default/GoogleMapsCompatible/{z}/{y}/{x}"),
    tileSize(256),
    bounds(-173.25000107492872, 0.0005794121990209753, 146.12527718104752, 71.506811402077),
    maxSourceZoom(18)
)

map.layers.add(layer, "transit")
```

::: zone-end

Poniższy zrzut ekranu przedstawia powyższy kod, który nakłada mapę sieci Web kafelków obrazów z [mapy krajowej w Stanach Zjednoczonych (agencji USGS)](https://viewer.nationalmap.gov/services/) na mapie, poniżej dróg i etykiet.

![Mapa systemu Android wyświetlająca warstwę kafelków WMTS](media/how-to-add-tile-layer-android-map/android-tile-layer-wmts.jpg)

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z poniższym artykułem, aby dowiedzieć się więcej o sposobach nakładania obrazów na mapę.

> [!div class="nextstepaction"]
> [Warstwa obrazu](map-add-image-layer-android.md)
