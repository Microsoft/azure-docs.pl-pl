---
title: Dodawanie warstwy kafelków do mapy | Mapy Microsoft Azure
description: Dowiedz się, jak nakładać obrazy na mapach. Zobacz przykład, który używa zestawu SDK sieci Web Azure Maps, aby dodać warstwę kafelków zawierającą nakładkę radarową do mapy.
author: rbrundritt
ms.author: richbrun
ms.date: 3/25/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: e0fda77be23f6ea16d5e64b5d4796813c53f0e94
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608106"
---
# <a name="add-a-tile-layer-to-a-map"></a>Dodawanie warstwy kafelków do mapy

W tym artykule pokazano, jak nałożyć warstwę kafelków na mapie. Warstwy kafelków umożliwiają nakładanie obrazów na kafelkach mapy podstawowej Azure Maps. Aby uzyskać więcej informacji na Azure Maps rozdzielenie systemu, zobacz [poziomy powiększenia i siatka kafelków](zoom-levels-and-tile-grid.md).

Warstwa kafelków jest ładowana na kafelkach z serwera. Te obrazy mogą być wstępnie renderowane lub dynamicznie renderowane. Wstępnie renderowane obrazy są przechowywane jak każdy inny obraz na serwerze przy użyciu konwencji nazewnictwa, która jest rozpoznawana przez warstwę kafelków. Dynamicznie renderowane obrazy używają usługi do ładowania obrazów blisko czasu rzeczywistego. Istnieją trzy różne konwencje nazewnictwa usługi kafelków obsługiwane przez Azure Maps [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer) klasy:

* X, Y, notacja powiększenia-X to kolumna, Y jest pozycją w wierszu kafelka w siatce kafelków, a w notacji Powiększ wartość na podstawie poziomu powiększenia.
* Notacja Quadkey — łączy x, y i Powiększ informacje w jedną wartość ciągu. Ta wartość ciągu jest unikatowym identyfikatorem dla pojedynczego kafelka.
* Pole ograniczenia — Określ obraz w formacie współrzędnych pola ograniczenia: `{west},{south},{east},{north}` . Ten format jest często używany przez [usługi mapowania sieci Web (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer) to doskonały sposób wizualizacji dużych zestawów danych na mapie. Nie tylko można wygenerować warstwy kafelków z obrazu, dane wektorowe mogą być również renderowane jako warstwa kafelków. Przez renderowanie danych wektorowych jako warstwy kafelków, formant mapy musi ładować kafelki, które są mniejsze w rozmiarze pliku niż dane wektorowe, które reprezentują. Ta technika jest często używana do renderowania milionów wierszy danych na mapie.

Adres URL kafelka przesłany do warstwy kafelków musi być adresem URL http lub HTTPS do zasobu TileJSON lub szablonem adresu URL kafelka, który używa następujących parametrów:

* `{x}` -X pozycja kafelka. Również wymagają `{y}` i `{z}` .
* `{y}` -Y pozycja kafelka. Również wymagają `{x}` i `{z}` .
* `{z}` — Poziom powiększenia kafelka. Również wymagają `{x}` i `{y}` .
* `{quadkey}` -Kafelek quadkey identyfikator oparty na konwencji nazewnictwa systemu kafelków mapy Bing.
* `{bbox-epsg-3857}` -Ciąg pola granicznego z formatem `{west},{south},{east},{north}` w systemie referencyjnym przestrzennym EPSG 3857.
* `{subdomain}` -Symbol zastępczy dla wartości poddomeny, jeśli zostanie określony, `subdomain` zostanie dodany.
* `{azMapsDomain}` — Symbol zastępczy, aby wyrównać domenę i uwierzytelnianie żądań kafelków z tymi samymi wartościami, które są używane przez mapę.

## <a name="add-a-tile-layer"></a>Dodawanie warstwy kafelków

 Ten przykład pokazuje, jak utworzyć warstwę kafelków, która wskazuje zestaw kafelków. Ten przykład używa systemu dzielenia x, y, powiększenia. źródłem tej warstwy kafelków jest [projekt OpenSeaMap](https://openseamap.org/index.php), który zawiera wykresy mil morskich ze źródłem. Podczas przeglądania danych radarowych najlepiej widzimy etykiety miast podczas nawigowania po mapie. To zachowanie można zaimplementować, wstawiając warstwę kafelków poniżej `labels` warstwy.

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://tiles.openseamap.org/seamark/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256,
    minSourceZoom: 7,
    maxSourceZoom: 17
}), 'labels');
```

Poniżej znajduje się kompletny przykładowy kod wykonywany z powyższymi funkcjami.

<br/>

<iframe height='500' scrolling='no' title='Warstwa kafelków używająca X, Y i Z' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>warstwę kafelków pióra przy użyciu X, Y i Z z</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-an-ogc-web-mapping-service-wms"></a>Dodaj usługę mapowania sieci Web OGC (WMS)

Usługa mapowania sieci Web (WMTS) jest standardem programu Open Geospatial Consortium (OGC) służącym do obsługi obrazów danych mapy. W tym formacie jest dostępnych wiele otwartych zestawów danych, których można używać z Azure Maps. Tego typu usługi można użyć w połączeniu z warstwą kafelka, jeśli usługa obsługuje `EPSG:3857` system odniesienia współrzędnych (KSR). W przypadku korzystania z usługi WMS ustaw parametry width i Height na taką samą wartość, która jest obsługiwana przez usługę, pamiętaj, aby ustawić tę samą wartość w `tileSize` opcji. W sformatowanym adresie URL ustaw `BBOX` parametr usługi z `{bbox-epsg-3857}` symbolem zastępczym.

Poniższy zrzut ekranu pokazuje, że powyższy kod nakładają usługę mapowania sieci Web o dane geologicznej z [(agencji USGS)](https://mrdata.usgs.gov/) na mapie, poniżej etykiet.

<br/>

<iframe height="265" style="width: 100%;" scrolling="no" title="Warstwa kafelków WMS" src="https://codepen.io/azuremaps/embed/BapjZqr?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Zapoznaj się z <a href='https://codepen.io/azuremaps/pen/BapjZqr'>warstwą kafelka</a> programu pióro — Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-an-ogc-web-mapping-tile-service-wmts"></a>Dodaj usługę kafelków OGC Web Mapping (WMTS)

Usługa kafelków map sieci Web (WMTS) jest standardem Open Geospatial Consortium (OGC) w celu obsłużenia opartymi na kafelkach nakładki dla map. W tym formacie jest dostępnych wiele otwartych zestawów danych, których można używać z Azure Maps. Tego typu usługi można użyć w połączeniu z warstwą kafelka, jeśli usługa obsługuje `EPSG:3857` `GoogleMapsCompatible` system odniesienia lub układ współrzędnych (KSR). W przypadku korzystania z usługi WMTS ustaw parametry width i Height na taką samą wartość, która jest obsługiwana przez usługę, pamiętaj, aby ustawić tę samą wartość w `tileSize` opcji. W sformatowanym adresie URL Zastąp odpowiednio następujące symbole zastępcze:

* `{TileMatrix}` => `{z}`
* `{TileRow}` => `{y}`
* `{TileCol}` => `{x}`

Poniższy zrzut ekranu przedstawia powyższy kod, który nakłada mapę sieci Web kafelków obrazów z [mapy krajowej w Stanach Zjednoczonych (agencji USGS)](https://viewer.nationalmap.gov/services/) na mapie, poniżej dróg i etykiet.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Warstwa kafelków WMTS" src="https://codepen.io/azuremaps/embed/BapjZVY?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Zobacz Azure Maps <a href='https://codepen.io/azuremaps/pen/BapjZVY'>warstwę kafelków WMTS</a> ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-tile-layer"></a>Dostosowywanie warstwy kafelków

Klasa kafelków ma wiele opcji stylów. Oto narzędzie do wypróbowania.

<br/>

<iframe height='700' scrolling='no' title='Opcje warstwy kafelków' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>Opcje warstwy kafelków</a> pióra według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer)

> [!div class="nextstepaction"]
> [TileLayerOptions](/javascript/api/azure-maps-control/atlas.tilelayeroptions)

Zapoznaj się z następującymi artykułami, aby uzyskać więcej przykładów kodu do dodania do Twoich map:

> [!div class="nextstepaction"]
> [Dodawanie warstwy obrazów](./map-add-image-layer.md)
