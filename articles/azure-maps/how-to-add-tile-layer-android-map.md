---
title: Dodawanie warstwy kafelków do map systemu Android | Mapy Microsoft Azure
description: Dowiedz się, jak dodać warstwę kafelków do mapy. Zobacz przykład, który używa Android SDK Azure Maps, aby dodać nakładkę radaru do mapy.
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 8ea6f44c47c5cd4d223b053640f65827f46db482
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97679314"
---
# <a name="add-a-tile-layer-to-a-map-android-sdk"></a>Dodawanie warstwy kafelków do mapy (Android SDK)

W tym artykule przedstawiono sposób renderowania warstwy kafelków na mapie przy użyciu Android SDK Azure Maps. Warstwy kafelków umożliwiają nakładanie obrazów na kafelkach mapy podstawowej Azure Maps. Więcej informacji o Azure Maps systemie rozmieszczania można znaleźć w dokumentacji [poziomów powiększenia i siatki kafelków](zoom-levels-and-tile-grid.md) .

Warstwa kafelków jest ładowana na kafelkach z serwera. Te obrazy mogą być wstępnie renderowane i przechowywane jak każdy inny obraz na serwerze, przy użyciu konwencji nazewnictwa, która jest rozpoznawana przez warstwę kafelków. Można też renderować te obrazy za pomocą usługi dynamicznej, która generuje obrazy niemal w czasie rzeczywistym. Istnieją trzy różne konwencje nazewnictwa usługi kafelków obsługiwane przez Azure Maps TileLayer klasy:

* X, Y, z notacją powiększenia w oparciu o poziom powiększenia, x to kolumna, a Y to pozycja w wierszu kafelka w siatce kafelków.
* Quadkey-kombinacja x, y, Powiększ informacje w postaci pojedynczej wartości ciągu, która jest unikatowym identyfikatorem dla kafelka.
* Współrzędne pola powiązanego obwiedni mogą służyć do określania obrazu w formacie `{west},{south},{east},{north}` , który jest często używany przez [usługi mapowania sieci Web (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> TileLayer to doskonały sposób wizualizacji dużych zestawów danych na mapie. Nie tylko można wygenerować warstwy kafelków z obrazu, ale dane wektorowe mogą być również renderowane jako warstwa kafelków. Przez renderowanie danych wektorowych jako warstwy kafelków, formant mapy musi ładować kafelki, które mogą być znacznie mniejsze w rozmiarze pliku niż dane wektorowe, które reprezentują. Ta technika jest używana przez wiele osób, które muszą renderować miliony wierszy danych na mapie.

Adres URL kafelka przesłany do warstwy kafelków musi być adresem URL protokołu HTTP/HTTPS do zasobu TileJSON lub szablonem adresu URL kafelka, który używa następujących parametrów: 

* `{x}` -X pozycja kafelka. Również wymagają `{y}` i `{z}` .
* `{y}` -Y pozycja kafelka. Również wymagają `{x}` i `{z}` .
* `{z}` — Poziom powiększenia kafelka. Również wymagają `{x}` i `{y}` .
* `{quadkey}` -Kafelek quadkey identyfikator oparty na konwencji nazewnictwa systemu kafelków mapy Bing.
* `{bbox-epsg-3857}` -Ciąg pola granicznego z formatem `{west},{south},{east},{north}` w systemie referencyjnym przestrzennym EPSG 3857.
* `{subdomain}` — Symbol zastępczy wartości poddomeny, jeśli określono wartość poddomeny.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć proces w tym artykule, należy zainstalować [Azure Maps Android SDK](how-to-use-android-map-control-library.md) w celu załadowania mapy.

## <a name="add-a-tile-layer-to-the-map"></a>Dodawanie warstwy kafelków do mapy

Ten przykład pokazuje, jak utworzyć warstwę kafelków, która wskazuje zestaw kafelków. Ten przykład używa systemu dzielenia "x, y, zoom". Źródłem tej warstwy kafelków jest [projekt OpenSeaMap](https://openseamap.org/index.php), który zawiera wykresy mil morskich ze źródłem. Często podczas wyświetlania warstw kafelków wskazane jest, aby wyraźnie widzieć etykiety miast na mapie. Takie zachowanie można osiągnąć, wstawiając warstwę kafelków poniżej warstw etykiet mapy.

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

Poniższy zrzut ekranu przedstawia powyższy kod wyświetlający warstwę kafelka informacji morskich na mapie, która ma styl ciemny w skali szarości.

![Mapa systemu Android — wyświetlanie warstwy kafelków](media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z poniższym artykułem, aby dowiedzieć się więcej o sposobach ustawiania stylów mapy

> [!div class="nextstepaction"]
> [Zmiana stylu mapy](set-android-map-styles.md)

> [!div class="nextstepaction"]
> [Dodawanie mapy cieplnej](map-add-heat-map-layer-android.md)
