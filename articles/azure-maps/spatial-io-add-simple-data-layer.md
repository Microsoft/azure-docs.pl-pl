---
title: Dodawanie prostej warstwy danych | Mapy Microsoft Azure
description: Dowiedz się, jak dodać prostą warstwę danych przy użyciu przestrzennego modułu we/wy dostarczonego przez Azure Maps Web SDK.
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 90f3cb0ae44be176d3ae248988d098039c140c3e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92896161"
---
# <a name="add-a-simple-data-layer"></a>Dodawanie prostej warstwy danych

Moduł operacji we/wy przestrzennego udostępnia `SimpleDataLayer` klasę. Ta klasa ułatwia renderowanie funkcji stylów na mapie. Może nawet renderować zestawy danych, które mają właściwości stylu i zestawy danych, które zawierają mieszane typy geometrii. Prosta warstwa danych uzyskuje tę funkcję przez zapakowanie wielu warstw renderingu i użycie wyrażeń stylów. Wyrażenia stylu wyszukują wspólne właściwości stylu funkcji w tych warstwach opakowanych. `atlas.io.read`Funkcja i `atlas.io.write` Funkcja używają tych właściwości do odczytu i zapisu stylów do obsługiwanego formatu pliku. Po dodaniu właściwości do obsługiwanego formatu pliku można użyć tego pliku do różnych celów. Na przykład plik może służyć do wyświetlania funkcji stylów na mapie.

Oprócz funkcji stylów, `SimpleDataLayer` zapewnia wbudowaną funkcję podręczną z szablonem podręcznym. Zostanie wyświetlone okno podręczne, gdy zostanie kliknięta funkcja. W razie potrzeby można wyłączyć domyślną funkcję podręczną. Ta warstwa obsługuje również dane klastrowane. Po kliknięciu klastra mapa zostanie powiększana do klastra i rozwinie do poszczególnych punktów i klastrów.

`SimpleDataLayer`Klasa jest przeznaczona do użycia w dużych zestawach danych z wieloma typami geometrii i wieloma stylami zastosowanymi w tych funkcjach. Gdy jest używana, ta klasa dodaje narzuty na sześć warstw zawierających wyrażenia stylów. Dlatego istnieją przypadki, w których bardziej wydajne jest korzystanie z podstawowych warstw renderowania. Na przykład użyj warstwy Podstawowa do renderowania kilku typów geometrii i kilku stylów dla funkcji

## <a name="use-a-simple-data-layer"></a>Korzystanie z prostej warstwy danych

`SimpleDataLayer`Klasa jest używana, podobnie jak inne warstwy renderowania. Poniższy kod pokazuje, jak używać prostej warstwy danych na mapie:

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Add a simple data layer for rendering data.
var layer = new atlas.layer.SimpleDataLayer(datasource);
map.layers.add(layer);
```

Dodaj funkcje do źródła danych. Następnie prosta warstwa danych będzie dowiedzieć się, jak najlepiej renderować funkcje. Style dla poszczególnych funkcji można ustawić jako właściwości funkcji. Poniższy kod pokazuje funkcję punktu GEOJSON z `color` właściwością ustawioną na `red` . 

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [0, 0]
    },
    "properties": {
        "color": "red"
    }
}
```

Poniższy kod renderuje funkcję powyższych punktów przy użyciu prostej warstwy danych. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Korzystanie z prostej warstwy danych" src="//codepen.io/azuremaps/embed/zYGzpQV/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true"> Zobacz, jak <a href='https://codepen.io/azuremaps/pen/zYGzpQV/'>korzystać z prostej warstwy danych</a> według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Rzeczywista moc prostej warstwy danych to:

- Istnieje kilka różnych typów funkcji w źródle danych. oraz
- Funkcje w zestawie danych mają ustawione na nich kilka właściwości stylu; oraz
- Nie masz pewności, co zestaw danych dokładnie zawiera.

Na przykład podczas analizowania strumieniowych źródeł danych XML nie można znać dokładnych stylów i typów geometrii funkcji. Poniższy przykład pokazuje moc prostej warstwy danych przez renderowanie funkcji pliku KML. Przedstawiono w nim również różne opcje zapewniane przez klasę prostej warstwy danych.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Proste Opcje warstwy danych" src="//codepen.io/azuremaps/embed/gOpRXgy/?height=700&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true"> Zapoznaj się z <a href='https://codepen.io/azuremaps/pen/gOpRXgy/'>opcjami proste warstwy danych</a> piórem Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!NOTE]
> Ta prosta warstwa danych używa klasy [szablonu popup](map-add-popup.md#add-popup-templates-to-the-map) do wyświetlania KML dymków lub właściwości funkcji w postaci tabeli. Domyślnie cała zawartość renderowana w oknie podręcznym zostanie przeizolowana wewnątrz elementu IFRAME jako funkcja zabezpieczeń. Istnieją jednak ograniczenia:
>
> - Wszystkie skrypty, formularze, blokada wskaźnika i najważniejsze funkcje nawigacji są wyłączone. Po kliknięciu łącza mogą zostać otwarte na nowej karcie. 
> - Starsze przeglądarki, które nie obsługują `srcdoc` parametru w iframeach, będą ograniczone do renderowania niewielkiej ilości zawartości.
> 
> Jeśli ufasz danych ładowanych do okien podręcznych i potencjalnie chcesz, aby te skrypty były ładowane do okien podręcznych, można je wyłączyć, ustawiając opcję szablony wyskakujące `sandboxContent` na false. 

## <a name="default-supported-style-properties"></a>Domyślne obsługiwane właściwości stylu

Jak wspomniano wcześniej, prosta warstwa danych otacza kilka podstawowych warstw renderowania: bąbelki, symbole, linie, Wielokąt i wytłaczany wielokąt. Następnie używa wyrażeń do wyszukiwania prawidłowych właściwości stylu dla poszczególnych funkcji.

Azure Maps i właściwości stylu GitHub to dwa główne zestawy obsługiwanych nazw właściwości. Większość nazw właściwości różnych opcji warstwy usługi Azure Maps jest obsługiwana jako właściwości stylu funkcji w prostej warstwie danych. Do niektórych opcji warstwy dodano wyrażenia obsługujące nazwy właściwości stylu, które są często używane przez witrynę GitHub. Te nazwy właściwości są definiowane przez [obsługę mapy GEOjson usługi GitHub](https://help.github.com/en/github/managing-files-in-a-repository/mapping-geojson-files-on-github)i są używane do stylu plików GeoJSON, które są przechowywane i renderowane na platformie. Wszystkie właściwości stylu usługi GitHub są obsługiwane w prostej warstwie danych, z wyjątkiem `marker-symbol` właściwości stylów.

Jeśli czytnik znajduje się w obrębie właściwości o mniej typowym stylu, zostanie przekonwertowany na najbliższy Azure Maps Właściwość stylu. Dodatkowo wyrażenia stylu domyślnego można przesłonić, używając `getLayers` funkcji prostej warstwy danych i aktualizując opcje na dowolnej z warstw.

Poniższe sekcje zawierają szczegółowe informacje o właściwościach domyślnych stylów, które są obsługiwane przez prostą warstwę danych. Kolejność nazwy obsługiwanej właściwości jest również priorytetem właściwości. Jeśli zdefiniowano dwie właściwości stylu dla tej samej opcji warstwy, pierwsza z nich na liście ma wyższy priorytet. Kolory mogą być dowolną wartością koloru CSS3; Wartość SZESNASTKOWa, RGB, RGBA, HSL, HSLA lub nazwanego koloru.

### <a name="bubble-layer-style-properties"></a>Właściwości bąbelków warstwowych

Jeśli funkcja jest lub a `Point` `MultiPoint` , a funkcja nie ma `image` właściwości, która mogłaby być używana jako ikona niestandardowa, aby renderować punkt jako symbol, funkcja będzie renderowana przy użyciu `BubbleLayer` .

| Opcja warstwy | Obsługiwane nazwy właściwości | Wartość domyślna |
|--------------|----------------------------|---------------|
| `color` | `color`, `marker-color` | `'#1A73AA'` |
| `radius` | `size`<sup>1</sup>, `marker-size` <sup>2</sup>, `scale` <sup>1</sup> | `8` |
| `strokeColor` | `strokeColor`, `stroke` | `'#FFFFFF'` |

\[1 \] `size` wartości i `scale` są uznawane za wartości skalarne i zostaną pomnożone przez `8`

\[2 \] Jeśli opcja GitHub `marker-size` jest określona, dla usługi RADIUS będą używane następujące wartości.

| Rozmiar znacznika | Radius |
|-------------|--------|
| `small`     | `6`    |
| `medium`    | `8`    |
| `large`     | `12`   |

Klastry są również renderowane przy użyciu warstwy bąbelki. Domyślnie promień klastra jest ustawiony na `16` . Kolor klastra różni się w zależności od liczby punktów w klastrze, zgodnie z definicją poniżej:

| Liczba punktów | Kolor    |
|-------------|----------|
| &gt;= 100   | `red`    |
| &gt;= 10    | `yellow` |
| &lt; dziesięć     | `green`  |

### <a name="symbol-style-properties"></a>Właściwości stylu symbolu

Jeśli funkcja jest lub a `Point` `MultiPoint` i ma `image` Właściwość, która byłaby używana jako ikona niestandardowa, aby renderować punkt jako symbol, funkcja będzie renderowana przy użyciu `SymbolLayer` .

| Opcja warstwy | Obsługiwane nazwy właściwości | Wartość domyślna |
|--------------|----------------------------|---------------|
| `image` | `image` | ``none`` |
| `size` | `size`, `marker-size` <sup>1</sup> | `1` |
| `rotation` | `rotation` | `0` |
| `offset` | `offset` | `[0, 0]` |
| `anchor` | `anchor` | `'bottom'` |

\[1 \] Jeśli `marker-size` została określona opcja GitHub, dla opcji rozmiar ikony zostaną użyte następujące wartości.

| Rozmiar znacznika | Rozmiar symboli |
|-------------|-------------|
| `small`     | `0.5`       |
| `medium`    | `1`         |
| `large`     | `2`         |

Jeśli punkt jest klastrem, `point_count_abbreviated` Właściwość będzie renderowana jako etykieta tekstowa. Obraz nie będzie renderowany.

### <a name="line-style-properties"></a>Właściwości stylu linii

Jeśli funkcja jest `LineString` , `MultiLineString` , `Polygon` , lub `MultiPolygon` , funkcja będzie renderowana przy użyciu `LineLayer` .

| Opcja warstwy | Obsługiwane nazwy właściwości | Wartość domyślna |
|--------------|----------------------------|---------------|
| `strokeColor` | `strokeColor`, `stroke` | `'#1E90FF'` |
| `strokeWidth` | `strokeWidth`, `stroke-width`, `stroke-thickness` | `3` |
| `strokeOpacity` | `strokeOpacity`, `stroke-opacity` | `1` |

### <a name="polygon-style-properties"></a>Właściwości stylu wielokąta

Jeśli funkcja jest lub a `Polygon` `MultiPolygon` , a funkcja nie ma `height` właściwości lub `height` Właściwość jest równa zero, funkcja będzie renderowana przy użyciu `PolygonLayer` .

| Opcja warstwy | Obsługiwane nazwy właściwości | Wartość domyślna |
|--------------|----------------------------|---------------|
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `fillOpacity` | `fillOpacity`, '`fill-opacity` | `0.5` |

### <a name="extruded-polygon-style-properties"></a>Właściwości wyciągania stylu wielokątu

Jeśli funkcja jest lub a `Polygon` `MultiPolygon` i ma `height` Właściwość o wartości większej niż 0, funkcja będzie renderowana przy użyciu `PolygonExtrusionLayer` .

| Opcja warstwy | Obsługiwane nazwy właściwości | Wartość domyślna |
|--------------|----------------------------|---------------|
| `base` | `base` | `0` |
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `height` | `height` | `0` |

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [SimpleDataLayer](/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)

> [!div class="nextstepaction"]
> [SimpleDataLayerOptions](/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

Zapoznaj się z następującymi artykułami, aby uzyskać więcej przykładów kodu do dodania do Twoich map:

> [!div class="nextstepaction"]
> [Odczytywanie i zapisywanie danych przestrzennych](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy mapy OGC](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z usługą WFS](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Korzystanie z podstawowych operacji](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Szczegóły obsługiwanego formatu danych](spatial-io-supported-data-format-details.md)