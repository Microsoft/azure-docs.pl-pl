---
title: Dodawanie warstwy symboli do mapy | Mapy Microsoft Azure
description: Dowiedz się, jak dodawać niestandardowe symbole, takie jak tekst lub ikony, do map. W tym celu Zobacz jak używać źródeł danych i warstw symboli w Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 5b042bc12202de5fc0fe30aac62e065538abf3a0
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91310496"
---
# <a name="add-a-symbol-layer-to-a-map"></a>Dodawanie warstwy symboli do mapy

Połącz symbol ze źródłem danych i użyj go do renderowania ikony lub tekstu w danym punkcie. 

Warstwy symboli są renderowane przy użyciu WebGL. Użyj warstwy symboli, aby renderować Duże kolekcje punktów na mapie. W porównaniu do znacznika HTML, warstwa symboli renderuje dużą liczbę danych punktów na mapie, co zapewnia lepszą wydajność. Jednak warstwa symboli nie obsługuje tradycyjnych stylów CSS i HTML do ustawiania stylów.  

> [!TIP]
> Warstwy symboli domyślnie będą renderować współrzędne wszystkich geometrie w źródle danych. Aby ograniczyć warstwę w taki sposób, że renderuje tylko funkcje geometrii punktów `filter` , ustawia właściwość warstwy na `['==', ['geometry-type'], 'Point']` lub `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` Jeśli chcesz, możesz również uwzględnić funkcje MultiPoint.

Menedżer Sprite obrazu Maps ładuje obrazy niestandardowe używane przez warstwę symboli. Obsługuje następujące formaty obrazów:

- JPEG
- PNG
- FORMACIE
- BMP
- GIF (bez animacji)

## <a name="add-a-symbol-layer"></a>Dodawanie warstwy symboli

Aby można było dodać warstwę symboli do mapy, należy wykonać kilka kroków. Najpierw Utwórz źródło danych i Dodaj je do mapy. Utwórz warstwę symboli. Następnie Przekaż źródło danych do warstwy symboli, aby pobrać dane ze źródła danych. Na koniec Dodaj dane do źródła danych, aby było możliwe renderowanie. 

Poniższy kod pokazuje, co należy dodać do mapy po jej załadowaniu. Ten przykład renderuje pojedynczy punkt na mapie za pomocą warstwy symboli. 

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a symbol layer to render icons and/or text at points on the map.
var layer = new atlas.layer.SymbolLayer(dataSource);

//Add the layer to the map.
map.layers.add(layer);

//Create a point and add it to the data source.
dataSource.add(new atlas.data.Point([0, 0]));
```

Istnieją cztery różne typy danych punktowych, które można dodać do mapy:

- Geometria punktu GEOJSON — ten obiekt zawiera tylko współrzędną punktu i nic innego. `atlas.data.Point`Klasy pomocnika można użyć do łatwego tworzenia tych obiektów.
- Geometria GEOJSON MultiPoint — ten obiekt zawiera współrzędne wielu punktów i nic innego. `atlas.data.MultiPoint`Klasy pomocnika można użyć do łatwego tworzenia tych obiektów.
- Funkcja GEOJSON — ten obiekt składa się z geometrii GEOJSON oraz zestawu właściwości, które zawierają metadane skojarzone z geometrią. `atlas.data.Feature`Klasy pomocnika można użyć do łatwego tworzenia tych obiektów.
- `atlas.Shape` Klasa jest podobna do funkcji GEOJSON. Oba składają się z geometrii GEOJSON oraz zestawu właściwości, które zawierają metadane skojarzone z geometrią. Jeśli obiekt GEOJSON zostanie dodany do źródła danych, może być łatwo renderowany w warstwie. Jeśli jednak Właściwość koordynuje tego obiektu GEOJSON jest aktualizowana, źródło danych i mapa nie są zmieniane. Wynika to z faktu, że w obiekcie JSON nie ma mechanizmu wyzwalania aktualizacji. Klasa Shape zawiera funkcje służące do aktualizowania danych, które zawiera. Po dokonaniu zmiany źródło danych i mapa są automatycznie powiadamiane i aktualizowane. 

Poniższy przykład kodu tworzy geometrię punktu GEOJSON i przekazuje go do klasy, `atlas.Shape` Aby ułatwić jego aktualizowanie. Środek mapy jest początkowo używany do renderowania symbolu. Zdarzenie kliknięcia jest dodawane do mapy, w taki sposób, że gdy wyzwalane, Współrzędne myszy są używane z funkcją Shapes `setCoordinates` . Współrzędne myszy są rejestrowane w momencie zdarzenia kliknięcia. Następnie program `setCoordinates` aktualizuje lokalizację symbolu na mapie.

<br/>

<iframe height='500' scrolling='no' title='Przełącz lokalizację numeru PIN' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Sprawdź <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>lokalizację numeru PIN przełączania</a> piórem Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Domyślnie warstwy symboli optymalizują renderowanie symboli, ukrywając symbole, które nakładają się na siebie. W miarę powiększania, ukryte symbole stają się widoczne. Aby wyłączyć tę funkcję i renderować wszystkie symbole przez cały czas, ustaw `allowOverlap` Właściwość `iconOptions` opcji na `true` .

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Dodaj niestandardową ikonę do warstwy symboli

Warstwy symboli są renderowane przy użyciu WebGL. Ponieważ wszystkie zasoby, takie jak obrazy ikon, muszą zostać załadowane do kontekstu WebGL. Ten przykład pokazuje, jak dodać niestandardową ikonę do zasobów mapy. Ta ikona służy następnie do renderowania danych punktu przy użyciu symbolu niestandardowego na mapie. `textField`Właściwość warstwy symboli wymaga określenia wyrażenia. W tym przypadku chcemy renderować Właściwość temperatury. Ponieważ temperatura jest liczbą, należy ją przekonwertować na ciąg. Ponadto chcemy dołączyć do niej "°F". Wyrażenie może służyć do tego łączenia; `['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='Ikona obrazu niestandardowego symbolu' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>ikonę niestandardowego obrazu symbolu</a> pióra przez Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Zestaw SDK sieci Web Azure Maps udostępnia kilka dostosowywalnych szablonów obrazów, których można używać z warstwą symboli. Aby uzyskać więcej informacji, zobacz dokument [jak korzystać z szablonów obrazów](how-to-use-image-templates-web-sdk.md) .

## <a name="customize-a-symbol-layer"></a>Dostosowywanie warstwy symboli 

Warstwa symboli ma dostępne wiele opcji stylów. Oto narzędzie do testowania różnych opcji stylów.

<br/>

<iframe height='700' scrolling='no' title='Opcje warstwy symboli' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/PxVXje/'>Opcje warstwy symboli</a> pióra według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Aby renderować tylko tekst z warstwą symboli, można ukryć ikonę przez ustawienie `image` Właściwości opcji ikony na `'none'` .

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions)

> [!div class="nextstepaction"]
> [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions)

> [!div class="nextstepaction"]
> [TextOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions)

Zapoznaj się z następującymi artykułami, aby uzyskać więcej przykładów kodu do dodania do Twoich map:

> [!div class="nextstepaction"]
> [Tworzenie źródła danych](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Dodawanie menu podręcznego](map-add-popup.md)

> [!div class="nextstepaction"]
> [Korzystanie z wyrażeń stylu opartych na danych](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Jak używać szablonów obrazów](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy linii](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy wielokąta](map-add-shape.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy bąbelkowej](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Dodaj marki HTML](map-add-bubble-layer.md)
