---
title: Odczytywanie i zapisywanie danych przestrzennych | Mapy Microsoft Azure
description: Dowiedz się, jak odczytywać i zapisywać dane przy użyciu przestrzennego modułu we/wy dostarczonego przez Azure Maps Web SDK.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/01/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 133674e6764e12742f5b238946e943d9b5011cd2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92891333"
---
# <a name="read-and-write-spatial-data"></a>Odczytywanie i zapisywanie danych przestrzennych

W poniższej tabeli przedstawiono formaty plików przestrzennych, które są obsługiwane w przypadku operacji odczytu i zapisu w module operacji we/wy przestrzennym.

| Format danych       | Odczyt | Zapisywanie |
|-------------------|------|-------|
| GeoJSON           | ✓  |  ✓  |
| GeoRSS            | ✓  |  ✓  |
| GML               | ✓  |  ✓  |
| GPX               | ✓  |  ✓  |
| KML               | ✓  |  ✓  |
| KMZ               | ✓  |  ✓  |
| Przestrzenny CSV       | ✓  |  ✓  |
| Well-Known tekst   | ✓  |  ✓  |

W następnych sekcjach opisano różne narzędzia do odczytywania i zapisywania danych przestrzennych przy użyciu przestrzennego modułu we/wy.

## <a name="read-spatial-data"></a>Odczytaj dane przestrzenne

`atlas.io.read`Funkcja jest główną funkcją służącą do odczytywania wspólnych formatów danych przestrzennych, takich jak KML, GPX, GeoRSS, GEOJSON i pliki CSV z danymi przestrzennymi. Ta funkcja umożliwia również odczytywanie skompresowanych wersji tych formatów jako pliku zip lub pliku KMZ. Format pliku KMZ to skompresowana wersja KML, która może zawierać również zasoby, takie jak obrazy. Alternatywnie funkcja Read może przyjmować adres URL wskazujący plik w dowolnym z tych formatów. Adresy URL powinny być hostowane w punkcie końcowym z obsługą mechanizmu CORS lub usługa serwera proxy powinna być dostępna w opcjach odczytu. Usługa serwera proxy służy do ładowania zasobów w domenach, które nie są włączone przy użyciu mechanizmu CORS. Funkcja Read zwraca obietnicę, aby dodać ikony obrazu do mapy, i asynchronicznie przetwarza dane, aby zminimalizować wpływ na wątek interfejsu użytkownika.

Podczas odczytywania skompresowanego pliku, jako zip lub KMZ, zostanie on rozpakowany i zeskanowany jako pierwszy prawidłowy plik. Na przykład doc. KML lub plik z innymi prawidłowymi rozszerzeniami, takimi jak:. KML,. XML, GEOJSON,. JSON,. csv,. tsv lub. txt. Następnie obrazy, do których odwołuje się plik KML i GeoRSS są wstępnie załadowane, aby upewnić się, że są dostępne. Dane niedostępnego obrazu mogą ładować alternatywny obraz rezerwowy lub zostaną usunięte ze stylów. Obrazy wyodrębnione z plików KMZ zostaną przekonwertowane na identyfikatory URI danych.

Wynikiem funkcji Read jest `SpatialDataSet` obiekt. Ten obiekt rozszerza klasę funkcji GEOJSON. Można je łatwo przesłać do `DataSource` funkcji AS, aby renderować jej funkcje na mapie. `SpatialDataSet`Nie tylko zawiera informacje o funkcji, ale może również obejmować nakładki KML, metryki przetwarzania i inne szczegóły, jak opisano w poniższej tabeli.

| Nazwa właściwości | Typ | Opis | 
|---------------|------|-------------|
| `bbox` | `BoundingBox` | Pole ograniczenia dotyczące wszystkich danych w zestawie danych. |
| `features` | `Feature[]` | Funkcje GEOJSON w zestawie danych. |
| `groundOverlays` | `(atlas.layer.ImageLayer | atlas.layers.OgcMapLayer)[]` | Tablica KML GroundOverlays. |
| `icons` | &lt;Ciąg rekordu, ciąg&gt; | Zestaw adresów URL ikon. Key = nazwa ikony, wartość = URL. |
| properties | dowolny | Informacje o właściwościach podane na poziomie dokumentu w zestawie danych przestrzennych. |
| `stats` | `SpatialDataSetStats` | Statystyka dotycząca zawartości i czasu przetwarzania przestrzennego zestawu danych. |
| `type` | `'FeatureCollection'` | Wartość typu GEOJSON tylko do odczytu. |

## <a name="examples-of-reading-spatial-data"></a>Przykłady odczytywania danych przestrzennych

Poniższy kod przedstawia sposób odczytywania przestrzennego zestawu danych i renderowania go na mapie przy użyciu `SimpleDataLayer` klasy. Kod używa pliku GPX wskazywanego przez adres URL.

<br/>

<iframe height='500' scrolling='no' title='Proste ładowanie danych przestrzennych' src='//codepen.io/azuremaps/embed/yLNXrZx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/yLNXrZx/'>dane przestrzenne ładowania</a> piórem proste przez Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

W następnym pokazie kodu pokazano, jak czytać i ładować KML lub KMZ do mapy. KML mogą zawierać nakładki gruntowe, które będą miały postać `ImageLyaer` lub `OgcMapLayer` . Te nakładki muszą być dodawane do mapy niezależnie od funkcji. Ponadto, jeśli zestaw danych zawiera niestandardowe ikony, te ikony muszą zostać załadowane do zasobów mapy przed załadowaniem funkcji.

<br/>

<iframe height='500' scrolling='no' title='Załaduj KML na mapie' src='//codepen.io/azuremaps/embed/XWbgwxX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/XWbgwxX/'>KML obciążenia</a> piórem na mapie według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Opcjonalnie możesz zapewnić usłudze proxy dostęp do zasobów między domenami, które mogą nie mieć włączonego mechanizmu CORS. Funkcja Read będzie próbować uzyskać dostęp do plików w innej domenie przy użyciu mechanizmu CORS. Po raz pierwszy nie można uzyskać dostępu do żadnego zasobu w innej domenie przy użyciu mechanizmu CORS, będzie ona żądać dodatkowych plików tylko wtedy, gdy została podana usługa serwera proxy. Funkcja Read dołącza adres URL pliku do końca podanego adresu URL serwera proxy. Ten fragment kodu przedstawia sposób przekazania usługi proxy do funkcji Read:

```javascript
//Read a file from a URL or pass in a raw data as a string.
atlas.io.read('https://nonCorsDomain.example.com/mySuperCoolData.xml', {
    //Provide a proxy service
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
}).then(async r => {
    if (r) {
        // Some code goes here . . .
    }
});

```

W poniższym przykładzie pokazano, jak odczytać rozdzielony plik i renderować go na mapie. W takim przypadku kod używa pliku CSV, który zawiera kolumny danych przestrzennych.

<br/>

<iframe height='500' scrolling='no' title='Dodawanie rozdzielanego pliku' src='//codepen.io/azuremaps/embed/ExjXBEb/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/ExjXBEb/'>Dodaj plik rozdzielony</a> przez Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="write-spatial-data"></a>Zapisz dane przestrzenne

Istnieją dwie główne funkcje zapisu w module operacji we/wy przestrzennym. `atlas.io.write`Funkcja generuje ciąg, podczas gdy `atlas.io.writeCompressed` funkcja generuje skompresowany plik zip. Skompresowany plik zip będzie zawierać plik tekstowy zawierający dane przestrzenne. Obie te funkcje zwracają obietnicę, aby dodać dane do pliku. I, oba te elementy mogą zapisywać dowolne z następujących danych: `SpatialDataSet` ,,, `DataSource` `ImageLayer` `OgcMapLayer` , kolekcję funkcji, funkcję, geometrię lub tablicę dowolnej kombinacji tych typów danych. Podczas pisania przy użyciu którejkolwiek z tych funkcji można określić żądany format pliku. Jeśli format pliku nie zostanie określony, dane będą zapisywane jako KML.

Poniższe narzędzie pokazuje większość opcji zapisu, które mogą być używane z `atlas.io.write` funkcją.

<br/>

<iframe height='700' scrolling='no' title='Opcje zapisu danych przestrzennych' src='//codepen.io/azuremaps/embed/YzXxXPG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/YzXxXPG/'>Opcje zapisu danych przestrzennych</a> pióra według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="example-of-writing-spatial-data"></a>Przykład zapisywania danych przestrzennych

Poniższy przykład umożliwia przeciąganie i upuszczanie, a następnie ładowanie plików przestrzennych na mapie. Dane GEOJSON można wyeksportować z mapy i zapisać je w jednym z obsługiwanych formatów danych przestrzennych jako ciąg lub skompresowany plik.

<br/>

<iframe height='700' scrolling='no' title='Przeciągnij i upuść pliki przestrzenne na mapie' src='//codepen.io/azuremaps/embed/zYGdGoO/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/zYGdGoO/'>przeciągnij i upuść pliki przestrzenne na mapie</a> według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Opcjonalnie możesz zapewnić usłudze proxy dostęp do zasobów między domenami, które mogą nie mieć włączonego mechanizmu CORS. Ten fragment kodu pokazuje, że można dołączyć usługę proxy:

```javascript
atlas.io.read(data, {
    //Provide a proxy service
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
}).then(
    //Success
    function(r) {
        //some code goes here ...
    }
);
```

## <a name="read-and-write-well-known-text-wkt"></a>Odczyt i zapis Well-Known tekst (WKT)

[Dobrze znany tekst](https://en.wikipedia.org/wiki/Well-known_text_representation_of_geometry) (WKT) to Open GEOSPATIAL Consortium (OGC) standard służący do reprezentowania geometrie przestrzennego jako tekst. Wiele systemów geoprzestrzennych obsługuje WKT, takie jak Azure SQL i Azure PostgreSQL, przy użyciu wtyczki PostGIS. Podobnie jak w przypadku większości standardów OGC, współrzędne są sformatowane jako "Szerokość geograficzna", aby dostosować ją do Konwencji "x y". Na przykład punkt o długości co najwyżej 110 i Latitude 45 można napisać jako `POINT(-110 45)` przy użyciu formatu WKT.

Dobrze znany tekst może zostać odczytany przy użyciu `atlas.io.ogc.WKT.read` funkcji i zapisany przy użyciu `atlas.io.ogc.WKT.write` funkcji.

## <a name="examples-of-reading-and-writing-well-known-text-wkt"></a>Przykłady odczytywania i pisania Well-Known tekst (WKT)

Poniższy kod przedstawia sposób odczytywania dobrze znanego ciągu tekstowego `POINT(-122.34009 47.60995)` i renderowania go na mapie za pomocą warstwy bąbelkowej.

<br/>

<iframe height='500' scrolling='no' title='Odczytaj Well-Known tekst' src='//codepen.io/azuremaps/embed/XWbabLd/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/XWbabLd/'>Odczytaj Well-Known tekst</a> przez Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Poniższy kod ilustruje odczytywanie i pisanie dobrze znanego tekstu.

<br/>

<iframe height='700' scrolling='no' title='Odczytuj i zapisuj Well-Known tekst' src='//codepen.io/azuremaps/embed/JjdyYav/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zapoznaj się z piórem <a href='https://codepen.io/azuremaps/pen/JjdyYav/'>Odczytaj i zapisz Well-Known tekst</a> przez Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="read-and-write-gml"></a>Odczyt i zapis GML

GML jest przestrzenną specyfikacją pliku XML, która jest często używana jako rozszerzenie do innych specyfikacji XML. Dane GEOJSON można zapisać jako XML za pomocą tagów GML przy użyciu `atlas.io.core.GmlWriter.write` funkcji. KOD XML zawierający GML można odczytać za pomocą `atlas.io.core.GmlReader.read` funkcji. Funkcja Read ma dwie opcje:

- `isAxisOrderLonLat`Opcja — kolejność osi współrzędnej "szerokości geograficznej, długości geograficznej" lub "Długość geograficzna" może się różnić między zestawami danych i nie zawsze jest dobrze zdefiniowana. Domyślnie czytnik GML odczytuje dane współrzędnych jako "Latitude, Długość geograficzna", ale ustawienie tej opcji na wartość true spowoduje jej odczytanie jako "Długość geograficzna".
- `propertyTypes`Opcja — ta opcja jest tabelą wyszukiwania wartości klucza, w której klucz jest nazwą właściwości w zestawie danych. Wartość jest typem obiektu, do którego ma zostać rzutowana wartość podczas analizowania. Obsługiwane wartości typu to: `string` , `number` , `boolean` i  `date` . Jeśli właściwość nie znajduje się w tabeli odnośników lub typ nie jest zdefiniowany, właściwość zostanie przeanalizowana jako ciąg.

`atlas.io.read`Funkcja domyślnie przejdzie do `atlas.io.core.GmlReader.read` funkcji, gdy wykryje, że dane wejściowe są XML, ale dane nie są jednym z innych obsługiwanych formatów XML.

`GmlReader`Zostaną przeanalizowane współrzędne, które mają jedną z następujących identyfikatory SRID:

- EPSG: 4326 (preferowany)
- EPSG: 4269, EPSG: 4283, EPSG: 4258, EPSG: 4308, EPSG: 4230, EPSG: 4272, EPSG: 4271, EPSG: 4267, EPSG: 4608, EPSG: 4674 prawdopodobnie z małym marginesem błędu.
- EPSG: 3857, EPSG: 102100, EPSG: 3785, EPSG: 900913, EPSG: 102113, EPSG: 41001, EPSG: 54004

## <a name="more-resources"></a>Więcej zasobów

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

[atlas.io funkcje statyczne](/javascript/api/azure-maps-spatial-io/atlas.io)

[SpatialDataSet](/javascript/api/azure-maps-spatial-io/atlas.spatialdataset)

[SpatialDataSetStats](/javascript/api/azure-maps-spatial-io/atlas.spatialdatasetstats)

[GmlReader](/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlreader)

[GmlWriter](/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlwriter)

[funkcje Atlas. IO. OGC. WKT](/javascript/api/azure-maps-spatial-io/atlas.io.ogc.wkt)

[Nawiązywanie połączenia z usługą WFS](spatial-io-connect-wfs-service.md)

[Korzystanie z podstawowych operacji](spatial-io-core-operations.md)

[Szczegóły obsługiwanego formatu danych](spatial-io-supported-data-format-details.md)


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami, aby uzyskać więcej przykładów kodu do dodania do Twoich map:

[Dodawanie warstwy mapy OGC](spatial-io-add-ogc-map-layer.md)