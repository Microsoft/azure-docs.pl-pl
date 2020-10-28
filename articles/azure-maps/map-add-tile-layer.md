---
title: Dodawanie warstwy kafelków do mapy | Mapy Microsoft Azure
description: Dowiedz się, jak nakładać obrazy na mapach. Zobacz przykład, który używa zestawu SDK sieci Web Azure Maps, aby dodać warstwę kafelków zawierającą nakładkę radarową do mapy.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: b3619995739c51d68b00f37ebea3a38680a6b6e7
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92890981"
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

 Ten przykład pokazuje, jak utworzyć warstwę kafelków, która wskazuje zestaw kafelków. Ten przykład używa systemu dzielenia x, y, powiększenia. Źródłem tej warstwy kafelków jest nałożenie radaru pogody z [Iowa środowiska Mesonet Iowa University](https://mesonet.agron.iastate.edu/ogc/). Podczas przeglądania danych radarowych najlepiej widzimy etykiety miast podczas nawigowania po mapie. To zachowanie można zaimplementować, wstawiając warstwę kafelków poniżej `labels` warstwy.

```javascript
//Create a tile layer and add it to the map below the label layer.
//Weather radar tiles from Iowa Environmental Mesonet of Iowa State University.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

Poniżej znajduje się kompletny przykładowy kod wykonywany z powyższymi funkcjami.

<br/>

<iframe height='500' scrolling='no' title='Warstwa kafelków używająca X, Y i Z' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>warstwę kafelków pióra przy użyciu X, Y i Z z</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
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