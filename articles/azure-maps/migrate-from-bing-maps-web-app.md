---
title: 'Samouczek: Migrowanie aplikacji internetowej z usługi mapy Bing | Mapy Microsoft Azure'
description: Samouczek dotyczący sposobu migrowania aplikacji internetowej z map Bing do Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 9/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: devx-track-js
ms.openlocfilehash: 168b3d51b66078b3d4c2e113711d3124820dd6bd
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92677799"
---
# <a name="tutorial---migrate-a-web-app-from-bing-maps"></a>Samouczek — Migrowanie aplikacji internetowej z usługi mapy Bing

Aplikacje sieci Web używające map Bing często korzystają z zestawu Bing SDK V8 JavaScript. Zestaw SDK sieci Web Azure Maps to odpowiedni zestaw SDK oparty na platformie Azure do migracji. Zestaw SDK sieci Web Azure Maps umożliwia dostosowanie interaktywnych map przy użyciu własnej zawartości i obrazów do wyświetlania w aplikacjach sieci Web lub aplikacji mobilnych. Kontrolka korzysta z technologii WebGL, co umożliwia renderowanie dużych zestawów danych z wysoką wydajnością. Utwórz ten zestaw SDK przy użyciu języka JavaScript lub TypeScript.

W przypadku migrowania istniejącej aplikacji sieci Web sprawdź, czy używa ona biblioteki kontroli typu open source, takiej jak cesium, ulotce i OpenLayers. Jeśli jest i wolisz nadal korzystać z tej biblioteki, możesz połączyć ją z usługami kafelków Azure Maps (kafelki satelitarne[kafelków](https://docs.microsoft.com/rest/api/maps/render/getmaptile) \| [satellite tiles](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile)). Poniższe linki zawierają szczegółowe informacje dotyczące korzystania z Azure Maps w niektórych najczęściej używanych bibliotekach kontroli mapy Open Source.

-   Cesium — kontrolka mapy 3D dla sieci Web. [Przykładowy kod](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20Cesium%20JS) \| [Dokumentacja](https://cesiumjs.org/)
-   Ulotek — uproszczona kontrolka mapy 2D dla sieci Web. [Przykładowy kod](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Azure%20Maps%20Raster%20Tiles%20in%20Leaflet%20JS) \| [Dokumentacja](https://leafletjs.com/)
-   OpenLayers — kontrolka mapy 2D dla sieci Web, która obsługuje projekcje. [Przykładowy kod](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20OpenLayers) \| [Dokumentacja](https://openlayers.org/)

W przypadku opracowywania przy użyciu struktury JavaScript jeden z następujących projektów typu "open source" może być przydatny:

- [ng-Azure-Maps](https://github.com/arnaudleclerc/ng-azure-maps) — otoka o pokątowym 10 wokół usługi Azure Maps.
- [AzureMapsControl. Components](https://github.com/arnaudleclerc/AzureMapsControl.Components) — składnik Blazor Azure Maps.
- [Azure Maps reaguje składnik](https://github.com/WiredSolutions/react-azure-maps) — otoka reaguje na Azure Maps kontrolkę.
- [Vue Azure Maps](https://github.com/rickyruiz/vue-azure-maps) — składnik Azure Maps dla aplikacji Vue.

## <a name="key-features-support"></a>Obsługa kluczowych funkcji

W poniższej tabeli przedstawiono najważniejsze funkcje interfejsu API w usłudze mapy Bing V8 JavaScript SDK oraz obsługę podobnego interfejsu API w zestawie SDK sieci Web Azure Maps.

| Funkcja mapy Bing        | Azure Maps obsługa zestawu SDK sieci Web                                                             |
|--------------------------|:--------------------------------------------------------------------------------------:|
| Pinezki                 | ✓                                                                                      |
| Klastrowanie pinezki       | ✓                                                                                      |
| Linie łamane & wielokąty     | ✓                                                                                      |
| Nakładki uziemienia          | ✓                                                                                      |
| Mapy cieplne                | ✓                                                                                      |
| Kafelki warstw              | ✓                                                                                      |
| Warstwa KML                | ✓                                                                                      |
| Warstwa konturów            | [Samples](https://azuremapscodesamples.azurewebsites.net/?search=contour)              |
| Warstwa pakowania danych       | [Samples](https://azuremapscodesamples.azurewebsites.net/?search=data%20binning)       |
| Animowana warstwa kafelków      | Uwzględnione w [module animacji](https://github.com/Azure-Samples/azure-maps-animations) Azure Maps "open source" |
| Narzędzia do rysowania            | ✓                                                                                      |
| Usługa geocode         | ✓                                                                                      |
| Usługa wskazówek       | ✓                                                                                      |
| Usługa macierzy odległości  | ✓                                                                                      |
| Usługa danych przestrzennych     | Nie dotyczy                                                                                    |
| Zdjęcia satelitarne i anteny | ✓                                                                                      |
| Obraz oka z ptakami         | Planowany                                                                                |
| Streetside       | Planowany                                                                                |
| Obsługa GEOJSON          | ✓                                                                                      |
| Obsługa GeoXML           | ✓                                                                                      |
| Obsługa tekstu Well-Known  | ✓                                                                                      |
| Niestandardowe style mapy        | Częściowe                                                                                |

Azure Maps również ma wiele dodatkowych [modułów Open Source dla zestawu SDK sieci Web](open-source-projects.md#open-web-sdk-modules) , które zwiększają możliwości.

## <a name="notable-differences-in-the-web-sdks"></a>Istotne różnice w zestawach SDK sieci Web

Poniżej przedstawiono niektóre kluczowe różnice między mapami Bing i Azure Maps zestawy SDK sieci Web, które mają być świadome:

-   Oprócz udostępniania hostowanego punktu końcowego do uzyskiwania dostępu do zestawu Web SDK Azure Maps pakiet NPM jest również dostępny do osadzania zestawu SDK sieci Web w aplikacjach, jeśli są preferowane. Aby uzyskać więcej informacji, zobacz tę [dokumentację](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control) , aby uzyskać więcej informacji. Ten pakiet zawiera również definicje języka TypeScript.
-   Mapy Bing udostępniają dwie hostowane gałęzie zestawu SDK; Wersja i eksperymentalna. W przypadku tworzenia nowych aplikacji eksperymentalna gałąź może otrzymywać wiele aktualizacji dziennie. Azure Maps obsługuje tylko gałąź wydania, ale funkcje eksperymentalne są tworzone jako moduły niestandardowe w projekcie Azure Maps kodu typu open source. Mapy Bing używane do posiadania zablokowanej gałęzi, która była również aktualizowana rzadziej, w związku z tym zmniejszają ryzyko przerwania zmian spowodowanych wydaniem. W Azure Maps można użyć modułu NPM i wskazać dowolną poprzednią wersję pomocniczą.

> [!TIP]
> Azure Maps publikuje wersje zestawu SDK zminimalizowanego i unminified. Proste usuwanie `.min` z nazw plików. Wersja unminified jest przydatna podczas debugowania problemów, ale należy użyć wersji zminimalizowanego w środowisku produkcyjnym, aby wykorzystać mniejszy rozmiar pliku.

-   Po utworzeniu wystąpienia klasy map w Azure Maps kod powinien czekać na `ready` uruchomienie map lub `load` zdarzenia przed współdziałaniem z mapą. Te zdarzenia zapewniają, że wszystkie zasoby mapy zostały załadowane i są gotowe do uzyskania dostępu.
-   Obie platformy używają podobnego systemu rozmieszczania dla map bazowych, jednak kafelki w usłudze mapy Bing są 256 pikseli w wymiarze, podczas gdy kafelki w Azure Maps mają 512 pikseli w wymiarze. W związku z tym aby uzyskać ten sam widok mapy w Azure Maps jak mapy Bing, poziom powiększenia używany w usłudze mapy Bing musi być odejmowany przez jeden w Azure Maps.
-   Współrzędne w usłudze mapy Bing są określane jako, `latitude, longitude` gdy Azure Maps używa `longitude, latitude` . Ten format jest zgodny ze standardem `[x, y]` , po którym następuje większość platform GIS.

-   Kształty w Azure Maps Web SDK są oparte na schemacie GEOJSON. Klasy pomocników są udostępniane za pomocą [przestrzeni nazw Atlas. Data](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data). Jest również w [Atlasie. Klasa Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape) , która może służyć do zawijania obiektów GEOJSON i ułatwiania ich aktualizowania i konserwowania w sposób umożliwiający powiązanie danych.
-   Współrzędne w Azure Maps są zdefiniowane jako obiekty położenia, które można określić jako prostą tablicę liczbową w formacie `[longitude, latitude]` lub `new atlas.data.Position(longitude, latitude)` .

> [!TIP]
> Klasa Position ma statyczną funkcję pomocniczą do importowania współrzędnych, które są w `latitude, longitude` formacie. Funkcja [Atlas. Data. Position. fromLatLng](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position)może często zastąpić `new Microsoft.Maps.Location` funkcję w usłudze mapy Bing.

-   Zamiast określać informacje o stylu dla każdego kształtu, który jest dodawany do mapy, Azure Maps oddziela style od danych. Dane są przechowywane w źródłach danych i są połączone z warstwami renderowania, które Azure Maps kod używa do renderowania danych. Takie podejście zapewnia zwiększoną korzyść w wydajności. Ponadto wiele warstw obsługuje Style oparte na danych, w których można dodać logikę biznesową do opcji stylu warstwy, które zmienią sposób renderowania poszczególnych kształtów wewnątrz warstwy w oparciu o właściwości zdefiniowane w kształcie.
-   Azure Maps udostępnia wiele przydatnych funkcji matematycznych w `atlas.math` przestrzeni nazw, ale różnią się one od tych w module matematycznym przestrzennym mapy Bing. Podstawowa różnica polega na tym, że Azure Maps nie udostępnia wbudowanych funkcji dla operacji binarnych, takich jak Unia i część wspólna, ponieważ jednak Azure Maps opiera się na formacie GEOJSON, który jest otwartym standardem, istnieje wiele dostępnych bibliotek typu open source. Jedną popularną opcją, która dobrze sprawdza się w przypadku Azure Maps i oferuje moc matematyczną w postaci [Turf js](http://turfjs.org/).

Zobacz również [słownik Azure Maps](https://docs.microsoft.com/azure/azure-maps/glossary) , aby uzyskać szczegółowe listę terminologii skojarzonej z Azure Maps.

## <a name="web-sdk-side-by-side-examples"></a>Przykłady obok siebie zestawu SDK sieci Web

Poniżej znajduje się kolekcja przykładów kodu dla każdej platformy, która obejmuje typowe przypadki użycia, aby ułatwić migrację aplikacji sieci Web z usługi mapy Bing V8 JavaScript SDK do Azure Maps Web SDK. Przykłady kodu związane z aplikacjami sieci Web są udostępniane w języku JavaScript; Jednakże Azure Maps udostępnia również definicje języka TypeScript jako dodatkową opcję za poorednictwem [modułu npm](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).

**Tematy**

- [Załaduj mapę](#load-a-map)
- [Lokalizowanie mapy](#localizing-the-map)
- [Ustawianie widoku mapy](#setting-the-map-view)
- [Dodawanie pinezki](#adding-a-pushpin)
- [Dodawanie niestandardowej pinezki](#adding-a-custom-pushpin)
- [Dodawanie linii łamanej](#adding-a-polyline)
- [Dodawanie wielokątu](#adding-a-polygon)
- [Wyświetlanie infobox](#display-an-infobox)
- [Klastrowanie pinezki](#pushpin-clustering)
- [Dodawanie mapy cieplnej](#add-a-heat-map)
- [Nałóż warstwę kafelków](#overlay-a-tile-layer)
- [Wyświetlanie danych o ruchu](#show-traffic-data)
- [Dodawanie nakładki uziemienia](#add-a-ground-overlay)
- [Dodawanie danych KML do mapy](#add-kml-data-to-the-map)
- [Dodawanie narzędzi do rysowania](#add-drawing-tools)


### <a name="load-a-map"></a>Załaduj mapę

Ładowanie mapy w obu zestawach SDK odbywa się zgodnie z tym samym zestawem kroków;

-   Dodaj odwołanie do zestawu SDK mapy.
-   Dodaj `div` tag do treści strony, która będzie pełnić rolę symbolu zastępczego mapy.
-   Utwórz funkcję języka JavaScript, która jest wywoływana po załadowaniu strony.
-   Utwórz wystąpienie odpowiedniej klasy map.

**Różnice między kluczami**

-   Mapy Bing wymagają, aby klucz konta został określony w odwołaniu do skryptu interfejsu API lub jako opcję mapowania. Poświadczenia uwierzytelniania dla Azure Maps są określone jako opcje klasy map. Może to być klucz subskrypcji lub Azure Active Directory informacji.
-   Mapy Bing pobierają funkcję wywołania zwrotnego w odniesieniu do skryptu interfejsu API, który jest używany do wywołania funkcji inicjowania w celu załadowania mapy. Przy Azure Maps należy użyć zdarzenia OnLoad dla strony.
-   W przypadku używania identyfikatora do odwoływania się do `div` elementu, w którym mapa będzie renderowana, w usłudze mapy Bing jest używany SELEKTOR HTML (tj. `#myMap` ), natomiast Azure Maps używa tylko wartości identyfikatora (tj. `myMap` ).
-   Współrzędne w Azure Maps są zdefiniowane jako obiekty położenia, które można określić jako prostą tablicę liczbową w formacie `[longitude, latitude]` .
-   Poziom powiększenia w Azure Maps to jeden poziom niższy niż przykład mapy Bing ze względu na różnice między różnymi rozmiarami systemu.
-   Domyślnie Azure Maps nie dodaje żadnych kontrolek nawigacji do kanwy mapy, takich jak przyciski powiększenia i przyciski stylu mapy. Istnieją jednak kontrolki do dodawania selektora stylów mapy, przycisków powiększenia, kontroli kompasu lub obrotu oraz kontroli skoku.
-   Procedura obsługi zdarzeń została dodana w Azure Maps do monitorowania `ready` zdarzenia wystąpienia mapy. Spowoduje to uruchomienie tej mapy po zakończeniu ładowania kontekstu WebGL i wszystkich wymaganych zasobów. Każdy kod ładowania można dodać do tego programu obsługi zdarzeń.

W poniższych przykładach pokazano, jak załadować mapę podstawową, która jest wyśrodkowana przez Nowy Jork przy współrzędnych (Długość geograficzna:-73,985, Szerokość geograficzna: 40,747) i ma poziom powiększenia 12 w usłudze mapy Bing.

**Przed: mapy Bing**

Poniższy kod przedstawia przykład sposobu wyświetlania mapy Bing wyśrodkowanej i powiększonej o lokalizację.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new Microsoft.Maps.Map('#myMap', {
                credentials: '<Your Bing Maps Key>',
          center: new Microsoft.Maps.Location(40.747, -73.985),
                zoom: 12
            });
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Uruchomienie tego kodu w przeglądarce spowoduje wyświetlenie mapy, która wygląda jak na poniższej ilustracji:

<center>

![Mapa mapy Bing](media/migrate-bing-maps-web-app/bing-maps-load-map.jpg)</center>

**Po: Azure Maps**

Poniższy kod pokazuje, jak załadować mapę z tym samym widokiem w Azure Maps wraz z kontrolką stylu mapy i przyciskami powiększenia.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-73.985, 40.747],  //Format coordinates as longitude, latitude.
                zoom: 11,   //Subtract the zoom level by one.

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
                //Add zoom and map style controls to top right of map.
                map.controls.add([
                    new atlas.control.StyleControl(),
                    new atlas.control.ZoomControl()
                ], {
                    position: 'top-right'
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Uruchomienie tego kodu w przeglądarce spowoduje wyświetlenie mapy, która wygląda jak na poniższej ilustracji:

<center>

![Mapa Azure Maps](media/migrate-bing-maps-web-app/azure-maps-load-map.jpg)</center>

Szczegółowa dokumentacja dotycząca sposobu konfigurowania i używania kontrolki mapy Azure Maps w aplikacji sieci Web można znaleźć [tutaj](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).

> [!TIP]
> Azure Maps publikuje wersje zestawu SDK zminimalizowanego i unminified. Usuń `.min` z nazw plików. Wersja unminified jest przydatna podczas debugowania problemów, ale należy użyć wersji zminimalizowanego w środowisku produkcyjnym, aby wykorzystać mniejszy rozmiar pliku.

**Dodatkowe zasoby**

-   Azure Maps udostępnia również kontrolki nawigacji umożliwiające obracanie i pochylenia widoku mapy zgodnie z opisem w [tym miejscu](https://docs.microsoft.com/azure/azure-maps/map-add-controls).

### <a name="localizing-the-map"></a>Lokalizowanie mapy

Jeśli odbiorcy są rozproszeni w wielu krajach lub mówisz w różnych językach, lokalizacja jest ważna.

**Przed: mapy Bing**

Aby zlokalizować mapy Bing, język i region są określane przy użyciu `setLang` parametrów i `UR` są dodawane do `<script>` odwołania do tagów interfejsu API. Niektóre funkcje w usłudze mapy Bing są dostępne tylko na niektórych rynkach, ponieważ takie rynek użytkownika jest określony za pomocą `setMkt` parametru.

```html
<script type="text/javascript" src="https://www.bing.com/api/maps/mapcontrol?callback=initMap&setLang=[language_code]&setMkt=[market]&UR=[region_code]" async defer></script>
```

Oto przykład mapy Bing z ustawionym językiem "fr-FR".

<center>

![Zlokalizowane mapowanie map Bing](media/migrate-bing-maps-web-app/bing-maps-localized-map.jpg)</center>

**Po: Azure Maps**

Azure Maps są dostępne tylko opcje ustawiania języka i widoku regionalnego mapy. Parametr rynku nie jest używany do ograniczania funkcji. Istnieją dwa różne sposoby ustawiania języka i widoku regionalnego mapy. Pierwsza opcja polega na dodaniu tych informacji do globalnej `atlas` przestrzeni nazw, co spowoduje, że wszystkie wystąpienia kontrolki mapy w aplikacji domyślnie przeprowadzą do tych ustawień. Poniżej ustawiono język francuski ("fr-FR") i widok regionalny `"auto"` :

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

Druga opcja polega na przejściu tych informacji do opcji mapy podczas ładowania mapy, jak:

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'auto',

    authOptions: {
        authType: 'subscriptionKey',
        subscriptionKey: '<Your Azure Maps Key>'
    }
});
```

> [!NOTE]
> Za pomocą Azure Maps można załadować wiele wystąpień map na tej samej stronie z różnymi ustawieniami języka i regionu. Ponadto można również zaktualizować te ustawienia na mapie po załadowaniu. Szczegółową listę obsługiwanych języków w Azure Maps można znaleźć [tutaj](https://docs.microsoft.com/azure/azure-maps/supported-languages).

Oto przykład Azure Maps z językiem ustawionym na "fr", a regionem użytkownika ustawionym na "fr-FR".

<center>

![Zlokalizowana Mapa Azure Maps](media/migrate-bing-maps-web-app/bing-maps-localized-map.jpg)</center>

### <a name="setting-the-map-view"></a>Ustawianie widoku mapy

Dynamiczne mapy w usłudze Bing i Azure Maps można programistycznie przenieść do nowych lokalizacji geograficznych, wywołując odpowiednie funkcje w języku JavaScript. W poniższych przykładach pokazano, jak utworzyć mapę wyświetlania obrazów satelitarnych satelitów, wyśrodkować mapę względem lokalizacji ze współrzędnymi (Długość geograficzna:-111,0225, Szerokość geograficzna: 35,0272) i zmienić poziom powiększenia na 15 w usłudze mapy Bing.

> [!NOTE]
> Usługa mapy Bing używa kafelków, które są 256 pikseli w wymiarach, podczas gdy Azure Maps zużywa większy kafelek 512 pikseli. Zmniejsza to liczbę żądań sieci wymaganych przez Azure Maps do załadowania tego samego obszaru mapy co mapy Bing. Jednak ze względu na sposób, w jaki diagramy kafelków działają w kontrolkach mapy, większe kafelki w Azure Maps oznacza, że aby osiągnąć ten sam obszar widoczny jako mapa w usłudze mapy Bing, należy odjąć poziom powiększenia używany w usłudze mapy Bing przez 1 podczas korzystania z Azure Maps.

**Przed: mapy Bing**

Formant mapy map Bing można programistycznie przenieść przy użyciu `setView` funkcji, która pozwala na określenie środka mapy i poziomu powiększenia.

```javascript
map.setView({
    mapTypeId: Microsoft.Maps.MapTypeId.aerial,
    center: new Microsoft.Maps.Location(35.0272, -111.0225),
    zoom: 15
});
```

<center>

![Widok mapy zestawu mapy Bing](media/migrate-bing-maps-web-app/bing-maps-set-map-view.jpg)</center>

**Po: Azure Maps**

W Azure Maps położenie mapy można zmienić programowo przy użyciu `setCamera` funkcji mapy, a styl mapy można zmienić przy użyciu `setStyle` funkcji. Należy zauważyć, że współrzędne w Azure Maps są w formacie "Długość geograficzna", a wartość poziomu powiększenia jest odejmowana od 1.

```javascript
map.setCamera({
    center: [-111.0225, 35.0272],
    zoom: 14
});

map.setStyle({
    style: 'satellite_with_roads'
});
```

<center>

![Widok mapy Azure Maps](media/migrate-bing-maps-web-app/azure-maps-set-map-view.jpg)</center>

**Dodatkowe zasoby**

-   [Wybieranie stylu mapy](https://docs.microsoft.com/azure/azure-maps/choose-map-style)
-   [Obsługiwane style mapy](https://docs.microsoft.com/azure/azure-maps/supported-map-styles)

### <a name="adding-a-pushpin"></a>Dodawanie pinezki

W Azure Maps istnieje wiele sposobów renderowania danych na mapie.

-   Znaczniki HTML — renderuje punkty przy użyciu tradycyjnych elementów DOM. Znaczniki HTML obsługują przeciąganie.
-   Warstwa symboli — renderuje punkty z ikoną i/lub tekstem w kontekście WebGL.
-   Warstwa bąbelków — renderuje punkty jako okręgi na mapie. Promienie okręgów mogą być skalowane na podstawie właściwości danych.

Zarówno warstwy symbol, jak i bąbelki są renderowane w kontekście WebGL i mogą renderować bardzo duże zestawy punktów na mapie. Te warstwy wymagają, aby dane były przechowywane w źródle danych. Po uruchomieniu zdarzenia należy dodać do mapy źródła danych i warstwy renderowania `ready` . Znaczniki HTML są renderowane jako elementy DOM na stronie i nie używają źródła danych. Im więcej elementów DOM na stronie, tym mniejsza strona. W przypadku renderowania więcej niż setek punktów na mapie zaleca się użycie jednej z warstw renderowania zamiast.

Poniższe przykłady umożliwiają dodanie znacznika do mapy pod adresem (Długość geograficzna:-0,2, Szerokość geograficzna: 51,5) o liczbie 10 nałożonej jako etykieta.

**Przed: mapy Bing**

W usłudze mapy Bing znaczniki są dodawane do mapy przy użyciu `Microsoft.Maps.Pushpin` klasy *. Pinezki są następnie dodawane do mapy przy użyciu jednej z dwóch funkcji.

Pierwsza funkcja to utworzenie warstwy, wstawienie pinezki do tej, a następnie dodanie warstwy do `layers` Właściwości mapy.

```javascript
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(51.5, -0.2), {
    text: '10'
});

var layer = new Microsoft.Maps.Layer();
layer.add(pushpin);
map.layers.insert(layer);
```

Drugim jest dodanie go przy użyciu `entities` Właściwości mapy. Ta funkcja jest oznaczona jako przestarzała w dokumentacji usługi mapy Bing V8, jednak pozostała częściowo funkcjonalna w przypadku scenariuszy podstawowych.

```javascript
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(51.5, -0.2), {
    text: '10'
});

map.entities.add(pushpin);
```

<center>

![Mapy Bing — Dodawanie puspin](media/migrate-bing-maps-web-app/bing-maps-add-pushpin.jpg)</center>

**Po: Azure Maps przy użyciu znaczników HTML**

W Azure Maps znaczniki HTML mogą służyć do łatwego wyświetlania punktu na mapie i są zalecane w przypadku prostych aplikacji, które wymagają tylko wyświetlania niewielkiej liczby punktów na mapie. Aby użyć znacznika HTML, Utwórz wystąpienie `atlas.HtmlMarker` klasy, ustaw opcje tekstu i pozycji, a następnie Dodaj znacznik do mapy przy użyciu `map.markers.add` funkcji.

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

<center>

![Azure Maps Dodaj znacznik](media/migrate-bing-maps-web-app/azure-maps-add-pushpin.jpg)</center>

**Po: Azure Maps przy użyciu warstwy symboli**

W przypadku korzystania z warstwy symboli dane muszą być dodawane do źródła danych i źródło danych dołączone do warstwy. Ponadto źródło danych i warstwa powinny zostać dodane do mapy po uruchomieniu `ready` zdarzenia. Aby renderować unikatową wartość tekstową nad symbolem, informacje tekstowe muszą być przechowywane jako właściwość punktu danych i właściwość, do której odwołuje się `textField` opcja warstwy. Jest to nieco więcej pracy niż używanie znaczników HTML, ale zapewnia wiele zalet wydajności.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Create a point feature, add a property to store a label for it, and add it to the data source.
                datasource.add(new atlas.data.Feature(new atlas.data.Point([-0.2, 51.5]), {
                    label: '10'
                }));

                //Add a layer for rendering point data as symbols.
                map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                    textOptions: {
                        //Use the label property to populate the text for the symbols.
                        textField: ['get', 'label'],
                        color: 'white',
                        offset: [0, -1]
                    }
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps Dodaj warstwę symboli](media/migrate-bing-maps-web-app/azure-maps-add-pushpin.jpg)</center>

**Dodatkowe zasoby**

-   [Tworzenie źródła danych](https://docs.microsoft.com/azure/azure-maps/create-data-source-web-sdk)
-   [Dodaj warstwę symboli](https://docs.microsoft.com/azure/azure-maps/map-add-pin)
-   [Dodaj warstwę bąbelkową](https://docs.microsoft.com/azure/azure-maps/map-add-bubble-layer)
-   [Dane punktu klastra](https://docs.microsoft.com/azure/azure-maps/clustering-point-data-web-sdk)
-   [Dodawanie znaczników HTML](https://docs.microsoft.com/azure/azure-maps/map-add-custom-html)
-   [Korzystanie z wyrażeń stylu opartych na danych](https://docs.microsoft.com/azure/azure-maps/data-driven-style-expressions-web-sdk)
-   [Opcje ikon warstwy symboli](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions)
-   [Opcja tekstu warstwy symboli](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions)
-   [Klasa znacznika HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker)
-   [Opcje znacznika HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

### <a name="adding-a-custom-pushpin"></a>Dodawanie niestandardowej pinezki

Obrazy niestandardowe mogą służyć do reprezentowania punktów na mapie. Poniższy obraz jest używany w poniższych przykładach i używa obrazu niestandardowego do wyświetlania punktu na mapie (Latitude: 51,5, Długość geograficzna:-0,2) i przesunięcia położenia znacznika, tak aby punkt ikony pinezki był wyrównany do poprawnej pozycji na mapie.

| ![Azure Maps dodać puspin](media/migrate-bing-maps-web-app/yellow-pushpin.png)|
|:-----------------------------------------------------------------------:|
| yellow-pushpin.png                                                        |


**Przed: mapy Bing**

W usłudze mapy Bing znacznik niestandardowy jest tworzony przez przekazanie adresu URL do obrazu do `icon` opcji pinezki. `anchor`Opcja służy do wyrównania punktu obrazu pinezki przy użyciu współrzędnej mapy. Wartość zakotwiczenia w mapach Bing względem lewego górnego rogu obrazu.

```javascript
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(51.5, -0.2), {
    icon: 'ylw-pushpin.png',
    anchor: new Microsoft.Maps.Point(5, 30)
});

var layer = new Microsoft.Maps.Layer();
layer.add(pushpin);
map.layers.insert(layer);
```

<center>

![Mapy Bing — Dodawanie niestandardowych puspin](media/migrate-bing-maps-web-app/bing-maps-add-custom-pushpin.jpg)</center>

**Po: Azure Maps przy użyciu znaczników HTML**

Aby dostosować znacznik HTML w Azure Maps pliku HTML `string` lub `HTMLElement` można go przesłać do `htmlContent` opcji znacznika. W Azure Maps opcja służy `anchor` do określania względnej pozycji znacznika względem współrzędnej położenia przy użyciu jednego z dziewięciu zdefiniowanych punktów odniesienia; "Center", "Top", "Bottom", "Left", "Right", "Top-Left", "Top-Right", "Bottom-Left", "dolny prawy". Zawartość jest zakotwiczona i jest domyślnie ustawiona na "dół", która jest dolną krawędzią zawartości HTML. Aby ułatwić migrację kodu z map Bing, ustaw zakotwiczenie na "z lewej strony", a następnie użyj `offset` opcji z tym samym przesunięciem używanym w usłudze mapy Bing. Przesunięcia w Azure Maps poruszają się w odwrotnym kierunku map Bing, więc pomnóż je przez minus jeden.

> [!TIP]
> Dodaj `pointer-events:none` jako styl do zawartości HTML, aby wyłączyć domyślne zachowanie przeciągania w przeglądarce MS Edge, które będzie wyświetlać niechcianą ikonę.

```html
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

<center>

![Azure Maps dodać znacznika niestandardowego](media/migrate-bing-maps-web-app/azure-maps-add-custom-marker.jpg)</center>

**Po: Azure Maps przy użyciu warstwy symboli**

Warstwy symboli w Azure Maps obsługują również obrazy niestandardowe, ale obraz musi zostać załadowany najpierw do zasobów mapy i przypisany unikatowy identyfikator. Warstwa symboli może odwoływać się do tego identyfikatora. Symbol może być przesunięty, aby wyrównać do poprawnego punktu na obrazie przy użyciu `offset` opcji ikona. W Azure Maps opcja służy `anchor` do określania względnej pozycji symbolu względem współrzędnej położenia przy użyciu jednego z dziewięciu zdefiniowanych punktów odniesienia; "Center", "Top", "Bottom", "Left", "Right", "Top-Left", "Top-Right", "Bottom-Left", "dolny prawy". Zawartość jest zakotwiczona i domyślnie ustawiona na "Dolna" jest to dolne centrum zawartości HTML. Aby ułatwić migrację kodu z map Bing, ustaw zakotwiczenie na "z lewej strony", a następnie użyj `offset` opcji z tym samym przesunięciem używanym w usłudze mapy Bing. Przesunięcia w Azure Maps poruszają się w odwrotnym kierunku map Bing, więc pomnóż je przez minus jeden.

```javascript
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Load the custom image icon into the map resources.
                map.imageSprite.add('my-yellow-pin', 'ylw-pushpin.png').then(function () {

                    //Create a data source and add it to the map.
                    datasource = new atlas.source.DataSource();
                    map.sources.add(datasource);

                    //Create a point and add it to the data source.
                    datasource.add(new atlas.data.Point([-0.2, 51.5]));

                    //Add a layer for rendering point data as symbols.
                    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            //Set the image option to the id of the custom icon that was loaded into the map resources.
                            image: 'my-yellow-pin',
                            anchor: 'top-left',
                            offset: [-5, -30]
                        }
                    }));
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Mapy Bing Dodaj warstwę symboli niestandardowych](media/migrate-bing-maps-web-app/azure-maps-add-custom-symbol-layer.jpg)</center>

> [!TIP]
> Aby utworzyć zaawansowane niestandardowe renderowanie punktów, należy użyć wielu warstw renderowania jednocześnie. Na przykład, jeśli chcesz mieć wiele pinezki o tej samej ikonie na różnych kolorach, zamiast tworzenia pęczków obrazów dla każdego koloru nakładki warstwy symbol na górze warstwy bąbelkowej i mają odwoływać się do tego samego źródła danych. Ta wartość będzie znacznie bardziej wydajna niż tworzenie i posiadanie mapy różnych obrazów przez mapę.

**Dodatkowe zasoby**

-   [Tworzenie źródła danych](https://docs.microsoft.com/azure/azure-maps/create-data-source-web-sdk)
-   [Dodaj warstwę symboli](https://docs.microsoft.com/azure/azure-maps/map-add-pin)
-   [Dodawanie znaczników HTML](https://docs.microsoft.com/azure/azure-maps/map-add-custom-html)
-   [Korzystanie z wyrażeń stylu opartych na danych](https://docs.microsoft.com/azure/azure-maps/data-driven-style-expressions-web-sdk)
-   [Opcje ikon warstwy symboli](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions)
-   [Opcja tekstu warstwy symboli](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions)
-   [Klasa znacznika HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker)
-   [Opcje znacznika HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

### <a name="adding-a-polyline"></a>Dodawanie linii łamanej

Linie łamane są używane do reprezentowania linii lub ścieżki na mapie. W poniższych przykładach pokazano, jak utworzyć kreskowaną linię łamaną na mapie.

**Przed: mapy Bing**

W usłudze mapy Bing Klasa linii łamanej przyjmuje tablicę lokalizacji i zestaw opcji.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create the polyline.
var polyline = new Microsoft.Maps.Polyline([
        center,
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude - 1),
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude + 1)
    ], {
        strokeColor: 'red',
        strokeThickness: 4,
        strokeDashArray: [3, 3]
    });

//Add the polyline to the map using a layer.
var layer = new Microsoft.Maps.Layer();
layer.add(polyline);
map.layers.insert(layer);
```

<center>

![Łamane mapy Bing](media/migrate-bing-maps-web-app/bing-maps-line.jpg)</center>

**Po: Azure Maps**

W Azure Maps linie łamane są określane bardziej często warunkami geograficznymi `LineString` lub `MultiLineString` obiektami. Te obiekty mogą być dodawane do źródła danych i renderowane przy użyciu warstwy liniowej. Opcje koloru obrysu, szerokości i kreski kreskowanej są niemal identyczne między platformami.

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a line and add it to the data source.
datasource.add(new atlas.data.LineString([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5]
]));

//Add a layer for rendering line data.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 4,
    strokeDashArray: [3, 3]
}));
```

<center>

![Linia Azure Maps](media/migrate-bing-maps-web-app/azure-maps-line.jpg)</center>

**Dodatkowe zasoby**

-   [Dodawanie wierszy do mapy](https://docs.microsoft.com/azure/azure-maps/map-add-line-layer)
-   [Opcje warstwy linii](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions)
-   [Korzystanie z wyrażeń stylu opartych na danych](https://docs.microsoft.com/azure/azure-maps/data-driven-style-expressions-web-sdk)

### <a name="adding-a-polygon"></a>Dodawanie wielokątu

Wielokąty są używane do reprezentowania obszaru na mapie. Mapy Azure Maps i Bing zapewniają bardzo podobne wsparcie dla wielokątów. W poniższych przykładach pokazano, jak utworzyć wielokąt, który tworzy Trójkąt w oparciu o współrzędne środkowe mapy.

**Przed: mapy Bing**

W usłudze mapy Bing Klasa wielokątów wykonuje tablicę współrzędnych lub współrzędne współrzędne i zestaw opcji.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create the polygon.
var polygon = new Microsoft.Maps.Polygon([
        center,
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude - 1),
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude + 1),
        center
    ], {
        fillColor: 'rgba(0, 255, 0, 0.5)',
        strokeColor: 'red',
        strokeThickness: 2
    });

//Add the polygon to the map using a layer.
var layer = new Microsoft.Maps.Layer();
layer.add(polygon);
map.layers.insert(layer);
```

<center>

![Mapy Bing polyogn](media/migrate-bing-maps-web-app/azure-maps-polygon.jpg)</center>

**Po: Azure Maps**

W Azure Maps obiektów Wielokąt i MultiPolygon można dodawać do źródła danych i renderowane na mapie przy użyciu warstw. Obszar wielokąta może być renderowany w warstwie wielokąta. Konspekt wielokąta może być renderowany przy użyciu warstwy linii.

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a polygon and add it to the data source.
datasource.add(new atlas.data.Polygon([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5],
    center
]));

//Add a polygon layer for rendering the polygon area.
map.layers.add(new atlas.layer.PolygonLayer(datasource, null, {
    fillColor: 'rgba(0, 255, 0, 0.5)'
}));

//Add a line layer for rendering the polygon outline.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 2
}));
```

<center>

![Azure Maps polyogn](media/migrate-bing-maps-web-app/azure-maps-polygon.jpg)</center>

**Dodatkowe zasoby**

-   [Dodawanie wielokąta do mapy](https://docs.microsoft.com/azure/azure-maps/map-add-shape#use-a-polygon-layer)
-   [Dodaj okrąg do mapy](https://docs.microsoft.com/azure/azure-maps/map-add-shape#add-a-circle-to-the-map)
-   [Opcje warstw wielokątów](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions)
-   [Opcje warstwy linii](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions)
-   [Korzystanie z wyrażeń stylu opartych na danych](https://docs.microsoft.com/azure/azure-maps/data-driven-style-expressions-web-sdk)

### <a name="display-an-infobox"></a>Wyświetlanie infobox

Dodatkowe informacje dla jednostki mogą być wyświetlane na mapie jako `Microsoft.Maps.Infobox` Klasa w usłudze mapy Bing w Azure Maps można to osiągnąć za pomocą `atlas.Popup` klasy. Poniższe przykłady umożliwiają dodanie do mapy pinezki/znacznika, a po kliknięciu wyświetla infobox/podręczny.

**Przed: mapy Bing**

Dzięki usłudze mapy Bing infobox jest tworzony przy użyciu `Microsoft.Maps.Infobox` konstruktora.

```javascript
//Add a pushpin where we want to display an infobox.
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(47.6, -122.33));

//Add the pushpin to the map using a layer.
var layer = new Microsoft.Maps.Layer();
layer.add(pushpin);
map.layers.insert(layer);

//Create an infobox and bind it to the map.
var infobox = new Microsoft.Maps.Infobox(new Microsoft.Maps.Location(47.6, -122.33), {
    description: '<div style="padding:5px"><b>Hello World!</b></div>',
    visible: false
});
infobox.setMap(map);

//Add a click event to the pushpin to open the infobox.
Microsoft.Maps.Events.addHandler(pushpin, 'click', function () {
    infobox.setOptions({ visible: true });
});
```

<center>

![Mapy Bing infobox](media/migrate-bing-maps-web-app/bing-maps-infobox.jpg)</center>

**Po: Azure Maps**

W Azure Maps, można użyć okna podręcznego, aby wyświetlić dodatkowe informacje dotyczące lokalizacji. KOD HTML `string` lub `HTMLElement` obiekt może być przekazywać do `content` opcji menu podręcznego. Okienka wyskakujące mogą być wyświetlane niezależnie od dowolnego kształtu, w razie potrzeby, a tym samym wymagają `position` określenia wartości. Aby wyświetlić okno podręczne, wywołaj `open` funkcję i przekaż mapę, w której ma zostać wyświetlone okno podręczne.

```javascript
//Add a marker to the map that to display a popup for.
var marker = new atlas.HtmlMarker({
    position: [-122.33, 47.6]
});

//Add the marker to the map.
map.markers.add(marker);

//Create a popup.
var popup = new atlas.Popup({
    content: '<div style="padding:10px"><b>Hello World!</b></div>',
    position: [-122.33, 47.6],
    pixelOffset: [0, -35]
});

//Add a click event to the marker to open the popup.
map.events.add('click', marker, function () {
    //Open the popup
    popup.open(map);
});
```

<center>

![Azure Maps menu podręcznego](media/migrate-bing-maps-web-app/azure-maps-popup.jpg)</center>

> [!NOTE]
> Aby zrobić to samo z użyciem symboli, bąbelków, linii lub wielokątów, Przekaż warstwę do kodu zdarzenia Maps zamiast znacznika.

**Dodatkowe zasoby**

-   [Dodawanie menu podręcznego](https://docs.microsoft.com/azure/azure-maps/map-add-popup)
-   [Podręczne z zawartością multimedialną](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
-   [Okna podręczne dla kształtów](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
-   [Używanie podręcznego z wieloma numerami PIN](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
-   [Popup — Klasa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup)
-   [Opcje wyskakujące](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions)

### <a name="pushpin-clustering"></a>Klastrowanie pinezki

Podczas wizualizacji wielu punktów danych na mapie, punkty nakładają się na siebie, mapa jest nieczytelna i trudno jest zobaczyć i korzystać z niej. Klastrowanie danych punktów może służyć do poprawy tego środowiska użytkownika i zwiększenia wydajności. Dane punktu klastrowania to proces łączenia danych punktów, które są blisko siebie i reprezentowania ich na mapie jako pojedynczy klastrowany punkt danych. Gdy użytkownik powiększa mapę, klastry rozdzielą się na poszczególne punkty danych.

Poniższe przykłady umożliwiają załadowanie źródła danych o ziemi GEOJSON z ostatnich tygodni i dodanie go do mapy. Klastry są renderowane jako przeskalowane i kolorowe koła w zależności od liczby zawartych w nich punktów.

> [!NOTE]
> Istnieje kilka różnych algorytmów używanych do klastrowania pinezki. Usługa mapy Bing korzysta z prostej funkcji opartej na siatce, natomiast w Azure Maps są stosowane bardziej zaawansowane i atrakcyjne wizualnie metody klastrowania oparte na punktach.

**Przed: mapy Bing**

W usłudze mapy Bing dane GEOJSON można ładować przy użyciu modułu GEOJSON. Pinezki mogą być klastrowane przez załadowanie w module klastrowania i użycie warstwy klastra, która zawiera.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            map = new Microsoft.Maps.Map(document.getElementById('myMap'), {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(20, -160),
                zoom: 2
            });

            //Load the GeoJSON and Clustering modules.
            Microsoft.Maps.loadModule(['Microsoft.Maps.GeoJson', 'Microsoft.Maps.Clustering'], function () {

                //Load the GeoJSON data from a URL.
                Microsoft.Maps.GeoJson.readFromUrl(earthquakeFeed, function (pins) {

                    //Create a ClusterLayer with options and add it to the map.
                    clusterLayer = new Microsoft.Maps.ClusterLayer(pins, {
                        clusteredPinCallback: createCustomClusteredPin,
                        gridSize: 100
                    });

                    map.layers.insert(clusterLayer);
                });
            });
        }

        //A function that defines how clustered pins are rendered.
        function createCustomClusteredPin(cluster) {
            //Get the number of pushpins in the cluster
            var clusterSize = cluster.containedPushpins.length;

            var radius = 20;    //Default radius to 20 pixels.
            var fillColor = 'lime';     //Default to lime green.

            if (clusterSize >= 750) {
                radius = 40;   //If point_count >= 750, radius is 40 pixels.
                fillColor = 'red';    //If the point_count >= 750, color is red.
            } else if (clusterSize >= 100) {
                radius = 30;    //If point_count >= 100, radius is 30 pixels.
                fillColor = 'yellow';    //If the point_count >= 100, color is yellow.
            }

            //Create an SVG string of a circle with the specified radius and color.
            var svg = ['<svg xmlns="http://www.w3.org/2000/svg" width="', (radius * 2), '" height="', (radius * 2), '">',
                '<circle cx="', radius, '" cy="', radius, '" r="', radius, '" fill="', fillColor, '"/>',
                '<text x="50%" y="50%" dominant-baseline="middle" text-anchor="middle" style="font-size:12px;font-family:arial;fill:black;" >{text}</text>',
                '</svg>'];

            //Customize the clustered pushpin using the generated SVG and anchor on its center.
            cluster.setOptions({
                icon: svg.join(''),
                anchor: new Microsoft.Maps.Point(radius, radius),
                textOffset: new Microsoft.Maps.Point(0, radius - 8) //Subtract 8 to compensate for height of text.
            });
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Usługi mapy Bing — klastrowanie](media/migrate-bing-maps-web-app/bing-maps-clustering.jpg)</center>

**Po: Azure Maps**

W Azure Maps dane są dodawane i zarządzane przez źródło danych. Warstwy łączą się ze źródłami danych i renderują w nich dane. `DataSource`Klasa w Azure Maps zawiera kilka opcji klastrowania.

-   `cluster` — Informuje źródło danych o danych punktu klastra. 
-   `clusterRadius` -Promień w pikselach do wspólnej tabeli punktów.
-   `clusterMaxZoom` — Maksymalny poziom powiększenia, który ma miejsce klaster. W przypadku powiększania w większym zakresie, wszystkie punkty są renderowane jako symbole.
-   `clusterProperties` -Definiuje niestandardowe właściwości, które są obliczane przy użyciu wyrażeń dla wszystkich punktów w każdym klastrze i dodawane do właściwości każdego punktu klastra.

Po włączeniu klastrowania źródło danych wyśle klastrowane i nieklastrowane punkty danych do warstw na potrzeby renderowania. Źródło danych umożliwia klastrowanie setek tysięcy punktów danych. Klastrowany punkt danych ma następujące właściwości:

| Nazwa właściwości               | Typ    | Opis                                    |
|-----------------------------|---------|------------------------------------------------|
| `cluster`                   | boolean | Wskazuje, czy funkcja reprezentuje klaster.     |
| `cluster_id`                | string  | Unikatowy identyfikator klastra, który może być używany z `DataSource` klasami `getClusterExpansionZoom` , `getClusterChildren` i i `getClusterLeaves` funkcjami. |
| `point_count`               | liczba  | Liczba punktów, które zawiera klaster.     |
| `point_count_abbreviated`   | string  | Ciąg, który skróci wartość, `point_count` Jeśli jest długi. (na przykład 4 000 to 4K) |

`DataSource`Klasa ma następującą funkcję pomocniczą do uzyskiwania dostępu do dodatkowych informacji o klastrze przy użyciu `cluster_id` .

| Funkcja       | Typ zwracany        | Opis     |
|----------------|--------------------|-----------------|
| `getClusterChildren(clusterId: number)`                              | `Promise<Feature<Geometry, any> | Shape>` | Pobiera elementy podrzędne danego klastra na następnym poziomie powiększenia. Te elementy podrzędne mogą być kombinacją kształtów i klastrów podrzędnych. Klastry podrzędne będą funkcjami z właściwościami pasującymi do właściwości klastra. |
| `getClusterExpansionZoom(clusterId: number)`                         | `Promise<number>`                            | Oblicza poziom powiększenia, który klaster rozpocznie rozszerzanie lub przerywanie.    |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | `Promise<Feature<Geometry, any> | Shape>` | Pobiera wszystkie punkty w klastrze. Ustaw wartość `limit` na, aby zwracała podzestaw punktów i używać `offset` na stronie do punktów.    |

Podczas renderowania danych klastrowanych na mapie często najłatwiejszym rozwiązaniem jest użycie co najmniej dwóch warstw. W poniższym przykładzie zastosowano trzy warstwy, warstwę bąbelków do rysowania skalowanego koła w oparciu o rozmiar klastrów, warstwę symboli służącą do renderowania rozmiaru klastra jako tekst i drugą warstwę symboli do renderowania punktów nieklastrowanych. Istnieje wiele innych sposobów renderowania danych klastrowanych w Azure Maps wyróżnione w dokumentacji dotyczącej [danych punktu klastra](https://docs.microsoft.com/azure/azure-maps/clustering-point-data-web-sdk) .

Dane GEOJSON można importować bezpośrednio w Azure Maps przy użyciu `importDataFromUrl` funkcji w `DataSource` klasie.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource(null, {
                    //Tell the data source to cluster point data.
                    cluster: true
                });
                map.sources.add(datasource);

                //Create layers for rendering clusters, their counts and unclustered points and add the layers to the map.
                map.layers.add([
                    //Create a bubble layer for rendering clustered data points.
                    new atlas.layer.BubbleLayer(datasource, null, {
                        //Scale the size of the clustered bubble based on the number of points inthe cluster.
                        radius: [
                            'step',
                            ['get', 'point_count'],
                            20,         //Default of 20 pixel radius.
                            100, 30,    //If point_count >= 100, radius is 30 pixels.
                            750, 40     //If point_count >= 750, radius is 40 pixels.
                        ],

                        //Change the color of the cluster based on the value on the point_cluster property of the cluster.
                        color: [
                            'step',
                            ['get', 'point_count'],
                            'lime',            //Default to lime green. 
                            100, 'yellow',     //If the point_count >= 100, color is yellow.
                            750, 'red'         //If the point_count >= 750, color is red.
                        ],
                        strokeWidth: 0,
                        filter: ['has', 'point_count'] //Only rendered data points that have a point_count property, which clusters do.
                    }),

                    //Create a symbol layer to render the count of locations in a cluster.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            image: 'none' //Hide the icon image.
                        },
                        textOptions: {
                            textField: ['get', 'point_count_abbreviated'],
                            offset: [0, 0.4]
                        }
                    }),

                    //Create a layer to render the individual locations.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        filter: ['!', ['has', 'point_count']] //Filter out clustered points from this layer.
                    })
                ]);

                //Retrieve a GeoJSON data set and add it to the data source. 
                datasource.importDataFromUrl(earthquakeFeed);
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps klastrowanie](media/migrate-bing-maps-web-app/azure-maps-clustering.jpg)</center>

**Dodatkowe zasoby**

-   [Dodaj warstwę symboli](https://docs.microsoft.com/azure/azure-maps/map-add-pin)
-   [Dodaj warstwę bąbelkową](https://docs.microsoft.com/azure/azure-maps/map-add-bubble-layer)
-   [Dane punktu klastra](https://docs.microsoft.com/azure/azure-maps/clustering-point-data-web-sdk)
-   [Korzystanie z wyrażeń stylu opartych na danych](https://docs.microsoft.com/azure/azure-maps/data-driven-style-expressions-web-sdk)

### <a name="add-a-heat-map"></a>Dodawanie mapy cieplnej

Mapy cieplne, znane także jako mapy gęstości punktów, są typem wizualizacji danych używanym do reprezentowania gęstości danych przy użyciu różnych kolorów. Są one często używane do wyświetlania danych "gorąca" na mapie i są doskonałym sposobem renderowania zestawów danych z dużymi punktami.

Poniższe przykłady załadują strumieniowe źródło danych ze wszystkich trzęsień w formacie JSON w ciągu ostatniego miesiąca z agencji USGS i renderuje je jako mapę cieplną.

**Przed: mapy Bing**

W obszarze mapy Bing w celu utworzenia mapy cieplnej Załaduj ją w module mapy cieplnej. Podobnie moduł GEOJSON jest ładowany w celu dodania obsługi danych GEOJSON.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {
            map = new Microsoft.Maps.Map(document.getElementById('myMap'), {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(20, -160),
                zoom: 2,
                mapTypeId: Microsoft.Maps.MapTypeId.aerial
            });

            //Load the GeoJSON and HeatMap modules.
            Microsoft.Maps.loadModule(['Microsoft.Maps.GeoJson', 'Microsoft.Maps.HeatMap'], function () {

                //Load the GeoJSON data from a URL.
                Microsoft.Maps.GeoJson.readFromUrl(earthquakeFeed, function (shapes) {

                    //Create a heat map and add it to the map.
                    var heatMap = new Microsoft.Maps.HeatMapLayer(shapes, {
                        opacity: 0.65,
                        radius: 10
                    });
                    map.layers.insert(heatMap);
                });
            });
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Mapy Bing mapę cieplną](media/migrate-bing-maps-web-app/bing-maps-heatmap.jpg)</center>

**Po: Azure Maps**

W Azure Maps Załaduj dane GEOJSON do źródła danych i Połącz źródło danych z warstwą mapy cieplnej. Dane GEOJSON można importować bezpośrednio w Azure Maps przy użyciu `importDataFromUrl` funkcji w `DataSource` klasie.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,
                style: 'satellite_with_roads',

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Load the earthquake data.
                datasource.importDataFromUrl(earthquakeFeed);

                //Create a layer to render the data points as a heat map.
                map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
                    opacity: 0.65,
                    radius: 10
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps mapę cieplną](media/migrate-bing-maps-web-app/azure-maps-heatmap.jpg)</center>

**Dodatkowe zasoby**

-   [Dodawanie warstwy mapy cieplnej](https://docs.microsoft.com/azure/azure-maps/map-add-heat-map-layer)
-   [Klasa warstwy mapy cieplnej](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.heatmaplayer)
-   [Opcje warstwy mapy cieplnej](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions)
-   [Korzystanie z wyrażeń stylu opartych na danych](https://docs.microsoft.com/azure/azure-maps/data-driven-style-expressions-web-sdk)

### <a name="overlay-a-tile-layer"></a>Nałóż warstwę kafelków

Warstwy kafelków umożliwiają nakładanie dużych obrazów, które zostały podzielone na mniejsze obrazy z sąsiadującymi obrazami, które są wyrównane do systemu fragmentów mapy. Jest to typowy sposób nakładania dużych obrazów lub bardzo dużych zestawów danych.

Poniższe przykłady nakładają warstwę kafelków z radaru na Pogoda z Iowaego środowiska Mesonet z University

**Przed: mapy Bing**

W usłudze mapy Bing warstwy kafelków można tworzyć przy użyciu `Microsoft.Maps.TileLayer` klasy.

```javascript
var weatherTileLayer = new Microsoft.Maps.TileLayer({
    mercator: new Microsoft.Maps.TileSource({
        uriConstructor: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{zoom}/{x}/{y}.png'
    })
});
map.layers.insert(weatherTileLayer);
```

<center>

![Mapy Bing mapę cieplną ważone](media/migrate-bing-maps-web-app/bing-maps-weighted-heatmap.jpg)</center>

**Po: Azure Maps**

W Azure Maps do mapy można dodać warstwę kafelków w taki sam sposób jak każda inna warstwa. Sformatowany adres URL, który zawiera symbole zastępcze x, y powiększenia; `{x}`, `{y}` `{z}` odpowiednio jest używany do określenia warstwy, w której mają być dostępne kafelki. Azure Maps także obsługują warstwy kafelków `{quadkey}` `{bbox-epsg-3857}` oraz `{subdomain}` symbole zastępcze.

> [!TIP]
> W Azure Maps warstwach można łatwo renderować poniżej innych warstw, w tym warstw mapy podstawowej. Często wskazane jest renderowanie warstw kafelków poniżej etykiet mapy, dzięki czemu można je łatwo odczytać. `map.layers.add`Funkcja przyjmuje drugi parametr, który jest identyfikatorem drugiej warstwy, aby wstawić nową warstwę poniżej. Aby wstawić warstwę kafelków poniżej etykiet mapy, można użyć następującego kodu:
> 
> `map.layers.add(myTileLayer, "labels");`

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

<center>

![Azure Maps ważone mapę cieplną](media/migrate-bing-maps-web-app/azure-maps-weighted-heatmap.jpg)</center>

> [!TIP]
> Żądania kafelków można przechwytywać przy użyciu `transformRequest` opcji mapy. Pozwoli to na modyfikację lub dodanie nagłówków do żądania w razie potrzeby.

**Dodatkowe zasoby**

-   [Dodawanie warstw kafelków](https://docs.microsoft.com/azure/azure-maps/map-add-tile-layer)
-   [Klasa kafelków](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer)
-   [Opcje warstwy kafelków](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions)

### <a name="show-traffic-data"></a>Wyświetlanie danych o ruchu

Dane o ruchu mogą być nałożone zarówno w usłudze Bing, jak i w usłudze Azure Maps.

**Przed: mapy Bing**

W usłudze mapy Bing dane dotyczące ruchu mogą być nałożone na mapę przy użyciu modułu ruchu.

```javascript
Microsoft.Maps.loadModule('Microsoft.Maps.Traffic', function () {
    var manager = new Microsoft.Maps.Traffic.TrafficManager(map);
    manager.show();
});
```

<center>

![Ruch w usłudze mapy Bing](media/migrate-bing-maps-web-app/bing-maps-traffic.jpg)</center>

**Po: Azure Maps**

Azure Maps oferuje kilka różnych opcji wyświetlania ruchu. Zdarzenia dotyczące ruchu, takie jak zamknięcie dróg i wypadki, mogą być wyświetlane jako ikony na mapie. Przepływ ruchu, kodowane kolorami dróg można przekroczyć na mapie, a kolory można modyfikować, aby były oparte na limicie limitów, względem normalnego oczekiwanego opóźnienia lub bezwzględnego opóźnienia. Dane zdarzenia w Azure Maps są aktualizowane co minutę i dane przepływu co 2 minuty.

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

<center>

![Ruch Azure Maps](media/migrate-bing-maps-web-app/azure-maps-traffic.jpg)</center>

Po kliknięciu jednej z ikon ruchu w Azure Maps w oknie podręcznym zostaną wyświetlone dodatkowe informacje.

<center>

![Okno podręczne Azure Maps ruchu](media/migrate-bing-maps-web-app/azure-maps-traffic-popup.jpg)</center>

**Dodatkowe zasoby**

-   [Pokaż ruch na mapie](https://docs.microsoft.com/azure/azure-maps/map-show-traffic)
-   [Opcje nakładki ruchu](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)
-   [Kontrola ruchu](https://azuremapscodesamples.azurewebsites.net/?sample=Traffic%20controls)

### <a name="add-a-ground-overlay"></a>Dodawanie nakładki uziemienia

Usługi Bing i Azure Maps obsługują nakładanie się obrazów z odwołaniami do mapy, dzięki czemu są one przesuwane i skalowane w miarę kadrowania i powiększania mapy. W usłudze mapy Bing są one znane jako nakładki naziemne w Azure Maps są one nazywane warstwami obrazu. Są one doskonałe do kompilowania planów piętra, nakładania starych map lub obrazów z drona.

**Przed: mapy Bing**

Podczas tworzenia nakładki naziemnej w usłudze mapy Bing należy określić adres URL obrazu do nakładki i ograniczenia powiązania obrazu z mapą. Ten przykład nakłada obraz mapy [Newark nowej Jersey z 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) na mapie.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new Microsoft.Maps.Map(document.getElementById('myMap'), {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(40.740, -74.18),
                zoom: 12
            });

            var overlay = new Microsoft.Maps.GroundOverlay({
                //Create a LocationRect from the edges of the bounding box; north, west, south, east.
                bounds: Microsoft.Maps.LocationRect.fromEdges(40.773941, -74.22655, 40.712216, -74.12544),
                imageUrl: 'newark_nj_1922.jpg'
            });
            map.layers.insert(overlay);
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Uruchomienie tego kodu w przeglądarce spowoduje wyświetlenie mapy, która wygląda jak na poniższej ilustracji:

<center>

![Nakładka uziemienia mapy Bing](media/migrate-bing-maps-web-app/bing-maps-ground-overlay.jpg)</center>

**Po: Azure Maps**

W Azure Maps obrazy geograficzne mogą zostać nadłożone przy użyciu `atlas.layer.ImageLayer` klasy. Ta klasa wymaga adresu URL do obrazu oraz zestawu współrzędnych dla czterech rogów obrazu. Obraz musi być hostowany w tej samej domenie lub mieć włączony mechanizm CORs.

> [!TIP]
> Jeśli masz tylko informacje o północy, południe, wschód, zachód i rotacja, a nie współrzędne dla każdego rogu obrazu, możesz użyć funkcji statycznej [Atlas. Layer. ImageLayer. getCoordinatesFromEdges](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer#getcoordinatesfromedges-number--number--number--number--number-) .

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-74.18, 40.740],
                zoom: 12,

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create an image layer and add it to the map.
                map.layers.add(new atlas.layer.ImageLayer({
                    url: 'newark_nj_1922.jpg',
                    coordinates: [
                        [-74.22655, 40.773941], //Top Left Corner
                        [-74.12544, 40.773941], //Top Right Corner
                        [-74.12544, 40.712216], //Bottom Right Corner
                        [-74.22655, 40.712216]  //Bottom Left Corner
                    ]
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps nakładki gruntowej](media/migrate-bing-maps-web-app/azure-maps-ground-overlay.jpg)</center>

**Dodatkowe zasoby**

-   [Nakładka obrazu](https://docs.microsoft.com/azure/azure-maps/map-add-image-layer)
-   [Klasa warstwy obrazu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer)

### <a name="add-kml-data-to-the-map"></a>Dodawanie danych KML do mapy

Mapy platformy Azure i Bing mogą importować i renderować dane KML, KMZ, GeoRSS, GEOJSON i Well-Known Text (WKT) na mapie. Azure Maps obsługuje również GPX, GML, przestrzenne pliki CSV, usługi Web-Mapping Services (WMS), Web-Mapping Kafelk Services (WMTS) i usługi Web Feature Services (WFS).

**Przed: mapy Bing**

Uruchomienie tego kodu w przeglądarce spowoduje wyświetlenie mapy, która wygląda jak na poniższej ilustracji:

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new Microsoft.Maps.Map('#myMap', {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(40.747, -73.985),
                zoom: 12
            });

            Microsoft.Maps.loadModule('Microsoft.Maps.GeoXml', function () {
                var callback = function (dataset) {
                    if (dataset.shapes) {
                        var l = new Microsoft.Maps.Layer();
                        l.add(dataset.shapes);
                        map.layers.insert(l);
                    }
                    if (dataset.layers) {
                        for (var i = 0, len = dataset.layers.length; i < len; i++) {
                            map.layers.insert(dataset.layers[i]);
                        }
                    }
                };
                Microsoft.Maps.GeoXml.readFromUrl('myKMLFile.kml', { error: function (msg) { alert(msg); } }, callback);
            });                
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Mapy Bing KML](media/migrate-bing-maps-web-app/bing-maps-kml.jpg)</center>

**Po: Azure Maps**

W Azure Maps GEOJSON jest głównym formatem danych używanym w zestawie SDK sieci Web, dodatkowe formaty danych przestrzennych można łatwo zintegrować przy użyciu [przestrzennego modułu we/wy](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/). Ten moduł ma funkcje do odczytu i zapisu danych przestrzennych, a także prostą warstwę danych, która może łatwo renderować dane z dowolnego z tych formatów danych przestrzennych. Aby odczytać dane w pliku danych przestrzennych, Przekaż adres URL lub dane pierwotne jako ciąg lub obiekt BLOB do `atlas.io.read` funkcji. Spowoduje to zwrócenie wszystkich przeanalizowanych danych z pliku, który można następnie dodać do mapy. KML jest nieco bardziej skomplikowany niż format danych przestrzennych, ponieważ zawiera on wiele informacji o stylu. `SpatialDataLayer`Klasa obsługuje renderowanie większości tych stylów, ale obrazy ikon muszą zostać załadowane do mapy przed załadowaniem danych funkcji, a nakładki gruntowe muszą być dodawane do mapy osobno. Podczas ładowania danych za pośrednictwem adresu URL powinno ono być hostowane w punkcie końcowym z włączoną funkcją CORs lub usługa serwera proxy powinna zostać przeniesiona jako opcja do funkcji Read.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <!-- Add reference to the Azure Maps Spatial IO module. -->
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

    <script type='text/javascript'>
        var map, datasource, layer;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                view: 'Auto',

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
            
                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Add a simple data layer for rendering the data.
                layer = new atlas.layer.SimpleDataLayer(datasource);
                map.layers.add(layer);

                //Read a KML file from a URL or pass in a raw KML string.
                atlas.io.read('myKMLFile.kml').then(async r => {
                    if (r) {

                        //Check to see if there are any icons in the data set that need to be loaded into the map resources.
                        if (r.icons) {
                            //For each icon image, create a promise to add it to the map, then run the promises in parrallel.
                            var imagePromises = [];

                            //The keys are the names of each icon image.
                            var keys = Object.keys(r.icons);

                            if (keys.length !== 0) {
                                keys.forEach(function (key) {
                                    imagePromises.push(map.imageSprite.add(key, r.icons[key]));
                                });

                                await Promise.all(imagePromises);
                            }
                        }

                        //Load all features.
                        if (r.features && r.features.length > 0) {
                            datasource.add(r.features);
                        }

                        //Load all ground overlays.
                        if (r.groundOverlays && r.groundOverlays.length > 0) {
                            map.layers.add(r.groundOverlays);
                        }

                        //If bounding box information is known for data, set the map view to it.
                        if (r.bbox) {
                            map.setCamera({ bounds: r.bbox, padding: 50 });
                        }
                    }
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps KML](media/migrate-bing-maps-web-app/azure-maps-kml.jpg)</center>

**Dodatkowe zasoby**

-   [Atlas. IO. Read — funkcja](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io#read-string---arraybuffer---blob--spatialdatareadoptions-)
-   [SimpleDataLayer](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)
-   [SimpleDataLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

### <a name="add-drawing-tools"></a>Dodawanie narzędzi do rysowania

Zarówno Bing, jak i Azure Maps udostępniają moduł, który umożliwia użytkownikowi dodawanie i edytowanie kształtów na mapie przy użyciu myszy lub innego urządzenia wejściowego. Obsługują one rysowanie pinezki, linii i wielokątów. Azure Maps udostępnia również opcje rysowania okręgów i prostokątów.

**Przed: mapy Bing**

W usłudze mapy Bing `DrawingTools` moduł jest ładowany przy użyciu `Microsoft.Maps.loadModule` funkcji. Po załadowaniu wystąpienie klasy DrawingTools można utworzyć, a `showDrawingManager` Funkcja jest wywoływana Dodaj pasek narzędzi do mapy.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
    var map, drawingManager;

    function initMap() {
        map = new Microsoft.Maps.Map('#myMap', {
            credentials: '<Your Bing Maps Key>'
        });

        //Load the DrawingTools module
        Microsoft.Maps.loadModule('Microsoft.Maps.DrawingTools', function () {
            //Create an instance of the DrawingTools class and bind it to the map.
            var tools = new Microsoft.Maps.DrawingTools(map);

            //Show the drawing toolbar and enable editting on the map.
            tools.showDrawingManager(function (manager) {
                //Store a reference to the drawing manager as it will be useful later.
                drawingManager = manager;
            });
        });
    }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>

```

<center>

![Narzędzia do rysowania map Bing](media/migrate-bing-maps-web-app/bing-maps-drawing-tools.jpg)</center>

**Po: Azure Maps**

W Azure Maps moduł narzędzi do rysowania musi być załadowany przez załadowanie plików JavaScript i CSS, które muszą być przywoływane w aplikacji. Po załadowaniu mapy `DrawingManager` można utworzyć wystąpienie klasy i `DrawingToolbar` dołączyć wystąpienie.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <!-- Add references to the Azure Maps Map Drawing Tools JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.js"></script>
    
    <script type='text/javascript'>
        var map, drawingManager;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                view: 'Auto',
                
                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create an instance of the drawing manager and display the drawing toolbar.
                drawingManager = new atlas.drawing.DrawingManager(map, {
                    toolbar: new atlas.control.DrawingToolbar({ position: 'top-left' })
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

<center>

![Narzędzia do rysowania Azure Maps](media/migrate-bing-maps-web-app/azure-maps-drawing-tools.jpg)</center>

> [!TIP]
> W warstwach Azure Maps narzędzia do rysowania zapewniają wiele sposobów rysowania kształtów przez użytkowników. Na przykład podczas rysowania wielokąta użytkownik może kliknąć, aby dodać każdy punkt, lub pozostawić lewy przycisk myszy w dół i przeciągnąć wskaźnik myszy, aby narysować ścieżkę. Można to zmodyfikować przy użyciu `interactionType` opcji `DrawingManager` .

**Dodatkowe zasoby**

-   [Dokumentacja](https://docs.microsoft.com/azure/azure-maps/set-drawing-options)
-   [Przykłady kodu](https://azuremapscodesamples.azurewebsites.net/#Drawing-Tools-Module)

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [modułami zestawu SDK sieci Web typu "open source" Azure Maps](open-source-projects.md#open-web-sdk-modules). Te moduły zapewniają dodatkową funkcjonalność i są w pełni dostosowywane.

Przejrzyj przykłady kodu powiązane z migrowaniem innych funkcji usługi mapy Bing:

**Wizualizacje danych**

> [!div class="nextstepaction"]
> [Warstwa konturów](https://azuremapscodesamples.azurewebsites.net/?search=contour)

> [!div class="nextstepaction"]
> [Pakowania danych](https://azuremapscodesamples.azurewebsites.net/?search=data%20binning)

**Usługi**

> [!div class="nextstepaction"]
> [Korzystanie z modułu Azure Maps Services](https://docs.microsoft.com/azure/azure-maps/how-to-use-services-module)

> [!div class="nextstepaction"]
> [Wyszukiwanie punktów orientacyjnych](https://docs.microsoft.com/azure/azure-maps/map-search-location)

> [!div class="nextstepaction"]
> [Uzyskiwanie informacji z współrzędnych (odwrotne geokod)](https://docs.microsoft.com/azure/azure-maps/map-get-information-from-coordinate)

> [!div class="nextstepaction"]
> [Wyświetlanie instrukcji dotyczących trasy z punktu A do punktu B](https://docs.microsoft.com/azure/azure-maps/map-route)

> [!div class="nextstepaction"]
> [Wyszukaj automatyczne sugerowanie przy użyciu interfejsu użytkownika JQuery](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

Dowiedz się więcej o zestawie SDK sieci Web Azure Maps.

> [!div class="nextstepaction"]
> [Jak używać kontrolki mapy](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Jak używać modułu usług](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Jak używać modułu narzędzi do rysowania](set-drawing-options.md)

> [!div class="nextstepaction"]
> [Przykłady kodu](https://docs.microsoft.com/samples/browse/?products=azure-maps)

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API usługi Azure Maps Web SDK](https://docs.microsoft.com/javascript/api/azure-maps-control/)
