---
title: Samouczek — Migrowanie aplikacji internetowej z usługi Google Maps | Mapy Microsoft Azure
description: Samouczek dotyczący sposobu migrowania aplikacji internetowej z usługi Mapy Google do usługi Microsoft Azure Maps
author: rbrundritt
ms.author: richbrun
ms.date: 08/18/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: devx-track-js
ms.openlocfilehash: b95800bea4bceffabad56aa29b68a57b310c5518
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896450"
---
# <a name="tutorial---migrate-a-web-app-from-google-maps"></a>Samouczek — Migrowanie aplikacji internetowej z usługi Google Maps

Większość aplikacji sieci Web korzystających z usługi Google Maps korzysta z zestawu SDK języka JavaScript usługi Google Maps v3. Zestaw SDK sieci Web Azure Maps to odpowiedni zestaw SDK oparty na platformie Azure do migracji. Zestaw SDK sieci Web Azure Maps umożliwia dostosowywanie interaktywnych map przy użyciu własnej zawartości i obrazów. Aplikację można uruchomić zarówno w sieci Web, jak i aplikacji mobilnych. Kontrolka korzysta z technologii WebGL, co umożliwia renderowanie dużych zestawów danych z wysoką wydajnością. Utwórz ten zestaw SDK przy użyciu języka JavaScript lub TypeScript. Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Załaduj mapę
> * Lokalizowanie mapy
> * Dodaj znaczniki, linie łamane i wielokąty.
> * Wyświetl informacje w oknie podręcznym lub informacji
> * Ładowanie i wyświetlanie danych KML i GEOJSON
> * Znaczniki klastra
> * Nałóż warstwę kafelków
> * Wyświetlanie danych o ruchu
> * Dodawanie nakładki uziemienia

Dowiesz się również: 

> [!div class="checklist"]
> * Jak wykonać typowe zadania związane z mapowaniem przy użyciu zestawu SDK sieci Web Azure Maps
> * Najlepsze rozwiązania zwiększające wydajność i środowisko użytkownika
> * Wskazówki dotyczące sposobu tworzenia aplikacji przy użyciu bardziej zaawansowanych funkcji dostępnych w programie Azure Maps

W przypadku migrowania istniejącej aplikacji sieci Web sprawdź, czy jest ona używana przez bibliotekę kontroli mapy Open Source. Przykłady biblioteki formantów typu "open source" to: cesium, ulotce i OpenLayers. Nadal można przeprowadzić migrację aplikacji, nawet jeśli korzysta ona z biblioteki kontroli mapy Open Source i nie chcesz używać Azure Maps Web SDK. W takim przypadku Połącz aplikację z usługami kafelków Azure Maps (kafelki[road tiles](/rest/api/maps/render/getmaptile) \| [satelitarne](/rest/api/maps/render/getmapimagerytile)kafelków). Poniższe punkty szczegółowo opisują sposób używania Azure Maps w niektórych najczęściej używanych bibliotekach kontroli mapy Open Source.

- Cesium — kontrolka mapy 3D dla sieci Web. [Przykładowy kod](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20Cesium%20JS) \| [Dokumentacja](https://cesiumjs.org/)
- Ulotek — uproszczona kontrolka mapy 2D dla sieci Web. [Przykładowy kod](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Azure%20Maps%20Raster%20Tiles%20in%20Leaflet%20JS) \| [Dokumentacja](https://leafletjs.com/)
- OpenLayers — kontrolka mapy 2D dla sieci Web, która obsługuje projekcje. [Przykładowy kod](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20OpenLayers) \| [Dokumentacja](https://openlayers.org/)

W przypadku opracowywania przy użyciu struktury JavaScript jeden z następujących projektów typu "open source" może być przydatny:

- [ng-Azure-Maps](https://github.com/arnaudleclerc/ng-azure-maps) — otoka o pokątowym 10 wokół usługi Azure Maps.
- [AzureMapsControl. Components](https://github.com/arnaudleclerc/AzureMapsControl.Components) — składnik Blazor Azure Maps.
- [Azure Maps reaguje składnik](https://github.com/WiredSolutions/react-azure-maps) — otoka reaguje na Azure Maps kontrolkę.
- [Vue Azure Maps](https://github.com/rickyruiz/vue-azure-maps) — składnik Azure Maps dla aplikacji Vue.

## <a name="prerequisites"></a>Wymagania wstępne 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
2. [Utwórz konto Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Uzyskaj podstawowy klucz subskrypcji](quick-demo-map-app.md#get-the-primary-key-for-your-account), nazywany także kluczem podstawowym lub kluczem subskrypcji. Aby uzyskać więcej informacji na temat uwierzytelniania w Azure Maps, zobacz [Zarządzanie uwierzytelnianiem w programie Azure Maps](how-to-manage-authentication.md).

## <a name="key-features-support"></a>Obsługa kluczowych funkcji

W tabeli wymieniono kluczowe funkcje interfejsu API w usłudze Google Maps v3 JavaScript SDK oraz obsługiwane funkcje interfejsu API w zestawie SDK sieci Web Azure Maps.

| Funkcja usługi Google Maps     | Azure Maps obsługa zestawu SDK sieci Web |
|-------------------------|:--------------------------:|
| Znaczniki                 | ✓                          |
| Znaczniki klastra       | ✓                          |
| Linie łamane & wielokąty    | ✓                          |
| Warstwy danych             | ✓                          |
| Nakładki uziemienia         | ✓                          |
| Mapy cieplne               | ✓                          |
| Kafelki warstw             | ✓                          |
| Warstwa KML               | ✓                          |
| Narzędzia do rysowania           | ✓                          |
| Usługa geocode        | ✓                          |
| Usługa wskazówek      | ✓                          |
| Usługa macierzy odległości | ✓                          |
| Usługa podniesienia uprawnień       | Planowany                     |

## <a name="notable-differences-in-the-web-sdks"></a>Istotne różnice w zestawach SDK sieci Web

Poniżej przedstawiono kilka najważniejszych różnic między usługą Mapy Google i Azure Maps zestawy SDK sieci Web, które mają być świadome:

- Oprócz udostępniania hostowanego punktu końcowego do uzyskiwania dostępu do zestawu Web SDK Azure Maps jest dostępny pakiet NPM. Osadź pakiet zestawu SDK sieci Web w aplikacjach. Aby uzyskać więcej informacji, zobacz tę [dokumentację](how-to-use-map-control.md). Ten pakiet zawiera również definicje języka TypeScript.
- Najpierw musisz utworzyć wystąpienie klasy map w Azure Maps. Poczekaj na `ready` uruchomienie map lub `load` zdarzenia przed programistycznym współdziałaniem z mapą. Ta kolejność zapewni, że wszystkie zasoby mapy zostały załadowane i są gotowe do uzyskania dostępu.
- Obie platformy używają podobnego systemu do rozmieszczania dla map podstawowych. Kafelki w usłudze Google Maps są 256 pikseli w wymiarze. jednak kafelki w Azure Maps są 512 pikseli w wymiarze. Aby uzyskać ten sam widok mapy w Azure Maps jako Google Maps, Odejmij poziom powiększenia usługi Google Maps o numer jeden w Azure Maps.
- Współrzędne w usłudze mapy Google są określane jako `latitude,longitude` , podczas gdy Azure Maps używa `longitude,latitude` . Format Azure Maps jest wyrównany do standardu `[x, y]` , po którym następuje większość platform GIS.
- Kształty w Azure Maps Web SDK są oparte na schemacie GEOJSON. Klasy pomocników są udostępniane za pomocą [przestrzeni nazw *Atlas. Data*](/javascript/api/azure-maps-control/atlas.data). Jest również w [*Atlasie. Shape*](/javascript/api/azure-maps-control/atlas.shape) — Klasa. Użyj tej klasy do zawijania obiektów GEOJSON, aby ułatwić aktualizowanie i konserwowanie danych w sposób możliwy do powiązania.
- Współrzędne w Azure Maps są zdefiniowane jako obiekty położenia. Współrzędna jest określona jako tablica liczbowa w formacie `[longitude,latitude]` . Lub jest on określony przy użyciu nowego Atlas. Data. Position (Długość geograficzna, Szerokość geograficzna).
    > [!TIP]
    > Klasa Position ma statyczną metodę pomocniczą do importowania współrzędnych w formacie "szerokości geograficznej". Metodę [Atlas. Data. Position. fromLatLng](/javascript/api/azure-maps-control/atlas.data.position) można często zastąpić `new google.maps.LatLng` metodą w kodzie usługi Google Maps.
- Zamiast określać informacje o stylu dla każdego kształtu, który jest dodawany do mapy, Azure Maps oddziela style od danych. Dane są przechowywane w źródle danych i są połączone z warstwami renderowania. Kod Azure Maps używa źródeł danych do renderowania danych. Takie podejście zapewnia zwiększoną korzyść w wydajności. Ponadto wiele warstw obsługuje Style oparte na danych, w których można dodać logikę biznesową do opcji stylu warstwy. Ta obsługa zmienia sposób renderowania poszczególnych kształtów wewnątrz warstwy na podstawie właściwości zdefiniowanych w kształcie.

## <a name="web-sdk-side-by-side-examples"></a>Przykłady obok siebie zestawu SDK sieci Web

Ta kolekcja zawiera przykłady kodu dla każdej platformy, a każdy przykład obejmuje typowy przypadek użycia. Ma ona na celu ułatwienie migracji aplikacji sieci Web z usługi Google Maps v3 JavaScript SDK do Azure Maps Web SDK. Przykłady kodu związane z aplikacjami sieci Web są udostępniane w języku JavaScript. Jednakże Azure Maps udostępnia również definicje języka TypeScript jako dodatkową opcję za poorednictwem [modułu npm](how-to-use-map-control.md).

**Tematy**

- [Załaduj mapę](#load-a-map)
- [Lokalizowanie mapy](#localizing-the-map)
- [Ustawianie widoku mapy](#setting-the-map-view)
- [Dodawanie znacznika](#adding-a-marker)
- [Dodawanie znacznika niestandardowego](#adding-a-custom-marker)
- [Dodawanie linii łamanej](#adding-a-polyline)
- [Dodawanie wielokątu](#adding-a-polygon)
- [Wyświetl okno informacji](#display-an-info-window)
- [Importowanie pliku GEOJSON](#import-a-geojson-file)- 
- [Znaczniki klastra](#marker-clustering)
- [Dodawanie mapy cieplnej](#add-a-heat-map)
- [Nałóż warstwę kafelków](#overlay-a-tile-layer)
- [Wyświetlanie danych o ruchu](#show-traffic-data)
- [Dodawanie nakładki uziemienia](#add-a-ground-overlay)
- [Dodawanie danych KML do mapy](#add-kml-data-to-the-map)

### <a name="load-a-map"></a>Załaduj mapę

Oba zestawy SDK mają te same kroki w celu załadowania mapy:

- Dodaj odwołanie do zestawu SDK mapy.
- Dodaj `div` tag do treści strony, który będzie pełnić rolę symbolu zastępczego mapy.
- Utwórz funkcję języka JavaScript, która jest wywoływana po załadowaniu strony.
- Utwórz wystąpienie odpowiedniej klasy map.

**Różnice między kluczami**

- Usługi Mapy Google wymagają określenia klucza konta w odniesieniu do skryptu interfejsu API. Poświadczenia uwierzytelniania dla Azure Maps są określone jako opcje klasy map. To poświadczenie może być kluczem subskrypcji lub Azure Active Directory informacji.
- Usługi Google Maps akceptują funkcję wywołania zwrotnego w odniesieniu do skryptu interfejsu API, który jest używany do wywołania funkcji inicjowania w celu załadowania mapy. Przy Azure Maps należy użyć zdarzenia OnLoad dla strony.
- Podczas odwoływania się do `div` elementu, w którym mapa będzie renderowana, `Map` klasa w Azure Maps wymaga tylko `id` wartości, a usługa mapy Google wymaga `HTMLElement` obiektu.
- Współrzędne w Azure Maps są zdefiniowane jako obiekty położenia, które można określić jako prostą tablicę liczbową w formacie `[longitude, latitude]` .
- Poziom powiększenia w Azure Maps to poziom niższy od poziomu powiększenia w usłudze mapy Google. Rozbieżność wynika z tego, że różnica w rozmiarach systemu rozmieszczania dwóch platform.
- Azure Maps nie dodaje żadnych kontrolek nawigacji do kanwy mapy. Tak więc domyślnie mapa nie ma przycisków powiększenia i przycisków stylu mapy. Jednak dostępne są opcje kontrolne umożliwiające dodanie selektora stylu mapy, przycisków powiększenia, kontrolki kompasu lub obrotu oraz kontrolki gęstość.
- Procedura obsługi zdarzeń została dodana w Azure Maps do monitorowania `ready` zdarzenia wystąpienia mapy. To zdarzenie zostanie wyzwolone po zakończeniu ładowania przez mapę kontekstu WebGL i wszystkich wymaganych zasobów. Dodaj dowolny kod, który ma zostać uruchomiony po zakończeniu ładowania mapy, do tej procedury obsługi zdarzeń.

Poniższe podstawowe przykłady używają usługi Google Maps do załadowania mapy wyśrodkowanej przez Nowy Jork przy współrzędnych. Długość geograficzna:-73,985, Szerokość geograficzna: 40,747, a mapa jest na poziomie powiększenia 12.

#### <a name="before-google-maps"></a>Wcześniej: Google Maps

Wyświetlanie mapy Google w środku i powiększonej do lokalizacji.

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
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.747, -73.985),
                zoom: 12
            });
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Uruchomienie tego kodu w przeglądarce spowoduje wyświetlenie mapy, która wygląda jak na poniższej ilustracji:

![Proste usługi Google Maps](media/migrate-google-maps-web-app/simple-google-map.png)

#### <a name="after-azure-maps"></a>Po: Azure Maps

Załaduj mapę z tym samym widokiem w Azure Maps wraz z kontrolką stylu mapy i przyciskami powiększenia.

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

                //Specify authentication information when loading the map.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
                //Add zoom controls to bottom right of map.
                map.controls.add(new atlas.control.ZoomControl(), {
                    position: 'bottom-right'
                });

                //Add map style control in top left corner of map.
                map.controls.add(new atlas.control.StyleControl(), {
                    position: 'top-left'
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

![Prosta Azure Maps](media/migrate-google-maps-web-app/simple-azure-maps.png)

Szczegółowe informacje na temat konfigurowania i używania kontrolki mapy Azure Maps w aplikacji sieci Web można znaleźć, klikając [tutaj](how-to-use-map-control.md).

> [!NOTE]
> W przeciwieństwie do usługi Google Maps Azure Maps nie wymaga początkowego centrum oraz poziomu powiększenia do załadowania mapy. Jeśli te informacje nie zostaną podane podczas ładowania mapy, usługa Azure Maps spróbuje określić miasto użytkownika. Spowoduje to wyśrodkowanie i powiększanie mapy w tym miejscu.

**Dodatkowe zasoby:**

- Azure Maps udostępnia również kontrolki nawigacji umożliwiające obracanie i pochylenia widoku mapy, jak opisano [tutaj](map-add-controls.md).

### <a name="localizing-the-map"></a>Lokalizowanie mapy

Jeśli odbiorcy są rozproszeni w wielu krajach/regionach lub mówisz w różnych językach, lokalizacja jest ważna.

#### <a name="before-google-maps"></a>Wcześniej: Google Maps

Aby zlokalizować usługi Google Maps, Dodaj parametry języka i regionu.

```html
<script type="text/javascript" src=" https://maps.googleapis.com/maps/api/js?callback=initMap&key=[api_key]& language=[language_code]&region=[region_code]" async defer></script>
```

Oto przykład mapy Google z ustawionym językiem "fr-FR".

![Lokalizacja usługi Google Maps](media/migrate-google-maps-web-app/google-maps-localization.png)

#### <a name="after-azure-maps"></a>Po: Azure Maps

Azure Maps oferuje dwa różne sposoby ustawiania języka i widoku regionalnego mapy. Pierwsza opcja polega na dodaniu tych informacji do globalnej przestrzeni nazw *szczytu* . Spowoduje to, że wszystkie wystąpienia kontrolki mapy w aplikacji domyślnie przeprowadzą do tych ustawień. Poniżej ustawia język francuski ("fr-FR") i widok regionalny na "Auto":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

Druga opcja polega na przejściu tych informacji do opcji mapy podczas ładowania mapy. Jak to:

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
> Za pomocą Azure Maps można załadować wiele wystąpień map na tej samej stronie z różnymi ustawieniami języka i regionu. Można również zaktualizować te ustawienia na mapie po jej załadowaniu. 

Szczegółowa lista [obsługiwanych języków](supported-languages.md) znajduje się w Azure Maps.

Oto przykład Azure Maps z językiem ustawionym na "fr", a regionem użytkownika ustawionym na "fr-FR".

![Lokalizacja Azure Maps](media/migrate-google-maps-web-app/azure-maps-localization.png)

### <a name="setting-the-map-view"></a>Ustawianie widoku mapy

Mapy dynamiczne na platformie Azure i w usłudze Google Maps można programistycznie przenieść do nowych lokalizacji geograficznych. Aby to zrobić, wywołaj odpowiednie funkcje w języku JavaScript. W przykładach pokazano, jak utworzyć mapę wyświetlania zdjęć satelitarnych, wyśrodkować mapę na miejscu i zmienić poziom powiększenia na 15 w usłudze mapy Google. Używane są następujące współrzędne lokalizacji: Długość geograficzna:-111,0225 i Szerokość geograficzna: 35,0272.

> [!NOTE]
> Usługa mapy Google używa kafelków, które są 256 pikseli w wymiarach, podczas gdy Azure Maps zużywa większy kafelek 512 pikseli. W takim przypadku Azure Maps wymaga mniejszej liczby żądań sieciowych do załadowania tego samego obszaru mapy co Google Maps. Ze względu na sposób, w jaki diagramy kafelków działają w kontrolkach mapy, należy odjęciu poziomu powiększenia używanego w usłudze mapy Google według numeru, który jest używany Azure Maps. Ta operacja arytmetyczna gwarantuje, że większe kafelki w Azure Maps renderują ten sam obszar mapy jak w usłudze mapy Google.

#### <a name="before-google-maps"></a>Wcześniej: Google Maps

Przenieś formant mapy usługi Google Maps przy użyciu `setOptions` metody. Ta metoda pozwala określić środek mapy i poziom powiększenia.

```javascript
map.setOptions({
    mapTypeId: google.maps.MapTypeId.SATELLITE,
    center: new google.maps.LatLng(35.0272, -111.0225),
    zoom: 15
});
```

![Widok zestawu usługi Google Maps](media/migrate-google-maps-web-app/google-maps-set-view.png)

#### <a name="after-azure-maps"></a>Po: Azure Maps

W Azure Maps Zmień położenie mapy przy użyciu `setCamera` metody i Zmień styl mapy za pomocą `setStyle` metody. Współrzędne w Azure Maps są w formacie "Długość geograficzna", a wartość poziomu powiększenia jest odejmowana według jednego.

```javascript
map.setCamera({
    center: [-111.0225, 35.0272],
    zoom: 14
});

map.setStyle({
    style: 'satellite'
});
```

![Widok zestawu Azure Maps](media/migrate-google-maps-web-app/azure-maps-set-view.jpeg)

**Dodatkowe zasoby:**

- [Wybieranie stylu mapy](choose-map-style.md)
- [Obsługiwane style mapy](supported-map-styles.md)

### <a name="adding-a-marker"></a>Dodawanie znacznika

W Azure Maps istnieje wiele sposobów renderowania danych punktu na mapie:

- **Znaczniki HTML** — renderuje punkty przy użyciu tradycyjnych elementów dom. Znaczniki HTML obsługują przeciąganie.
- **Warstwa symboli** — renderuje punkty z ikoną lub tekstem w kontekście WebGL.
- **Warstwa bąbelków** — renderuje punkty jako okręgi na mapie. Promienie okręgów mogą być skalowane na podstawie właściwości danych.

Renderowanie warstw symboli i warstw bąbelków w kontekście WebGL. Obie warstwy mogą renderować duże zestawy punktów na mapie. Te warstwy wymagają, aby dane były przechowywane w źródle danych. Po uruchomieniu zdarzenia należy dodać do mapy źródła danych i warstwy renderowania `ready` . Znaczniki HTML są renderowane jako elementy DOM na stronie i nie korzystają ze źródła danych. Im więcej elementów DOM na stronie, tym mniejsza strona. W przypadku renderowania więcej niż stu punktów na mapie zaleca się użycie jednej z warstw renderowania zamiast.

Dodajmy znacznik do mapy o liczbie 10 nałożonej jako etykieta. Użyj długości geograficznej:-0,2 i szerokości geograficznej: 51,5.

#### <a name="before-google-maps"></a>Wcześniej: Google Maps

Za pomocą usługi Google Maps Dodaj znaczniki do mapy przy użyciu `google.maps.Marker` klasy i określ mapę jako jedną z opcji.

```javascript
//Create a marker and add it to the map.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    label: '10',
    map: map
});
```

![Znacznik Google Maps](media/migrate-google-maps-web-app/google-maps-marker.png)

**Po: Azure Maps przy użyciu znaczników HTML**

W Azure Maps Użyj znaczników HTML, aby wyświetlić punkt na mapie. Znaczniki HTML są zalecane w przypadku aplikacji, które wymagają wyświetlania niewielkiej liczby punktów na mapie. Aby użyć znacznika HTML, Utwórz wystąpienie `atlas.HtmlMarker` klasy. Ustaw opcje tekstu i pozycji, a następnie Dodaj znacznik do mapy za pomocą `map.markers.add` metody.

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

![Azure Maps znacznika HTML](media/migrate-google-maps-web-app/azure-maps-html-marker.png)

**Po: Azure Maps przy użyciu warstwy symboli**

Dla warstwy symboli Dodaj dane do źródła danych. Dołącz źródło danych do warstwy. Ponadto źródło danych i warstwa powinny zostać dodane do mapy po uruchomieniu `ready` zdarzenia. Aby renderować unikatową wartość tekstową nad symbolem, informacje tekstowe muszą być przechowywane jako właściwość punktu danych. Właściwość musi być przywoływana w `textField` opcji warstwy. Takie podejście jest nieco bardziej wydajne niż używanie znaczników HTML, ale lepsza wydajność.

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

![Azure Maps warstwa symboli](media/migrate-google-maps-web-app/azure-maps-symbol-layer.png)

**Dodatkowe zasoby:**

- [Tworzenie źródła danych](create-data-source-web-sdk.md)
- [Dodaj warstwę symboli](map-add-pin.md)
- [Dodaj warstwę bąbelkową](map-add-bubble-layer.md)
- [Dane punktu klastra](clustering-point-data-web-sdk.md)
- [Dodawanie znaczników HTML](map-add-custom-html.md)
- [Korzystanie z wyrażeń stylu opartych na danych](data-driven-style-expressions-web-sdk.md)
- [Opcje ikon warstwy symboli](/javascript/api/azure-maps-control/atlas.iconoptions)
- [Opcja tekstu warstwy symboli](/javascript/api/azure-maps-control/atlas.textoptions)
- [Klasa znacznika HTML](/javascript/api/azure-maps-control/atlas.htmlmarker)
- [Opcje znacznika HTML](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

### <a name="adding-a-custom-marker"></a>Dodawanie znacznika niestandardowego

Możesz użyć niestandardowych obrazów do reprezentowania punktów na mapie. Mapa poniżej używa obrazu niestandardowego do wyświetlania punktu na mapie. Punkt jest wyświetlany na szerokości geograficznej: 51,5 i długości geograficznej: – 0,2. Kotwica przesunięcia położenia znacznika, tak aby punkt ikony pinezki wyrównany do poprawnego położenia na mapie.

<center>

![żółty obraz pinezki](media/migrate-google-maps-web-app/yellow-pushpin.png)<br/>
yellow-pushpin.png</center>


#### <a name="before-google-maps"></a>Wcześniej: Google Maps

Utwórz znacznik niestandardowy przez określenie `Icon` obiektu, który zawiera `url` obraz. Określ `anchor` punkt, aby wyrównać punkt obrazu pinezki ze współrzędną na mapie. Wartość zakotwiczenia w usłudze Google Maps jest określana względem lewego górnego rogu obrazu.

```javascript
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    icon: {
        url: 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png',
        anchor: new google.maps.Point(5, 30)
    },
    map: map
});
```


![Znacznik niestandardowy usługi Google Maps](media/migrate-google-maps-web-app/google-maps-custom-marker.png)

**Po: Azure Maps przy użyciu znaczników HTML**

Aby dostosować znacznik HTML, należy przekazać kod HTML `string` lub `HTMLElement` do `htmlContent` opcji znacznika. Użyj `anchor` opcji, aby określić względne położenie znacznika względem współrzędnej położenia. Przypisz do opcji jeden z dziewięciu zdefiniowanych punktów odwołania `anchor` . Te zdefiniowane punkty to: "Center", "Top", "Bottom", "Left", "Right", "Top-Left", "Top-Right", "Bottom-Left", "dolny prawy". Zawartość jest domyślnie zakotwiczona do dolnego centrum zawartości HTML. Aby ułatwić migrację kodu z usługi Google Maps, należy ustawić wartość `anchor` "na" z lewej strony, a następnie użyć `pixelOffset` opcji z tym samym przesunięciem, które jest używane w usłudze mapy Google. Przesunięcia w Azure Maps poruszają się w odwrotnym kierunku przesunięć w usłudze Google Maps. W związku z tym pomnóż przesunięcia przez minus jeden.

> [!TIP]
> Dodaj `pointer-events:none` jako styl do zawartości HTML, aby wyłączyć domyślne zachowanie przeciągania w przeglądarce Microsoft Edge, co spowoduje wyświetlenie niepożądanej ikony.

```javascript
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

![Azure Maps niestandardowego znacznika HTML](media/migrate-google-maps-web-app/azure-maps-custom-html-marker.png)

**Po: Azure Maps przy użyciu warstwy symboli**

Warstwy symboli w Azure Maps obsługują również obrazy niestandardowe. Najpierw Załaduj obraz do zasobów mapy i przypisz go z unikatowym IDENTYFIKATORem. Odwołuje się do obrazu w warstwie symboli. Użyj `offset` opcji, aby wyrównać obraz do poprawnego punktu na mapie. Użyj `anchor` opcji, aby określić względne położenie symbolu względem Współrzędne położenia. Użyj jednego z dziewięciu zdefiniowanych punktów odniesienia. Te punkty to: "Center", "Top", "Bottom", "Left", "Right", "Top-Left", "Top-Right", "Bottom-Left", "dolny prawy". Zawartość jest domyślnie zakotwiczona do dolnego centrum zawartości HTML. Aby ułatwić migrację kodu z usługi Google Maps, należy ustawić wartość `anchor` "na" z lewej strony, a następnie użyć `offset` opcji z tym samym przesunięciem, które jest używane w usłudze mapy Google. Przesunięcia w Azure Maps poruszają się w odwrotnym kierunku przesunięć w usłudze Google Maps. W związku z tym pomnóż przesunięcia przez minus jeden.

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

                //Load the custom image icon into the map resources.
                map.imageSprite.add('my-yellow-pin', 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png').then(function () {

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

![Warstwa symboli niestandardowych Azure Maps](media/migrate-google-maps-web-app/azure-maps-custom-icon-symbol-layer.png)</

> [!TIP]
> Aby renderować zaawansowane punkty niestandardowe, należy użyć wielu warstw renderowania jednocześnie. Załóżmy na przykład, że chcesz mieć wiele pinezki, które mają tę samą ikonę w różnych kolorach koła. Zamiast tworzyć wiele obrazów dla każdej nakładki kolorów, Dodaj warstwę symboli na górze warstwy bąbelków. Czy pinezki odwołują się do tego samego źródła danych. Takie podejście będzie bardziej wydajne niż tworzenie i konserwowanie wielu różnych obrazów.

**Dodatkowe zasoby:**

- [Tworzenie źródła danych](create-data-source-web-sdk.md)
- [Dodaj warstwę symboli](map-add-pin.md)
- [Dodawanie znaczników HTML](map-add-custom-html.md)
- [Korzystanie z wyrażeń stylu opartych na danych](data-driven-style-expressions-web-sdk.md)
- [Opcje ikon warstwy symboli](/javascript/api/azure-maps-control/atlas.iconoptions)
- [Opcja tekstu warstwy symboli](/javascript/api/azure-maps-control/atlas.textoptions)
- [Klasa znacznika HTML](/javascript/api/azure-maps-control/atlas.htmlmarker)
- [Opcje znacznika HTML](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

### <a name="adding-a-polyline"></a>Dodawanie linii łamanej

Użyj linii łamanej do reprezentowania linii lub ścieżki na mapie. Utwórzmy kreskowaną linię łamaną na mapie.

#### <a name="before-google-maps"></a>Wcześniej: Google Maps

Klasa linii łamanej akceptuje zestaw opcji. Przekaż tablicę współrzędnych w `path` opcji linii łamanej.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Define a symbol using SVG path notation, with an opacity of 1.
var lineSymbol = {
    path: 'M 0,-1 0,1',
    strokeOpacity: 1,
    scale: 4
};

//Create the polyline.
var line = new google.maps.Polyline({
    path: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1)
    ],
    strokeColor: 'red',
    strokeOpacity: 0,
    strokeWeight: 4,
    icons: [{
        icon: lineSymbol,
        offset: '0',
        repeat: '20px'
    }]
});

//Add the polyline to the map.
line.setMap(map);
```

![Google Maps — łamana](media/migrate-google-maps-web-app/google-maps-polyline.png)

#### <a name="after-azure-maps"></a>Po: Azure Maps

Linie łamane są nazywane `LineString` lub `MultiLineString` obiektami. Te obiekty mogą być dodawane do źródła danych i renderowane przy użyciu warstwy liniowej. Dodaj `LineString` do źródła danych, a następnie Dodaj źródło danych do programu, `LineLayer` aby je renderować.

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
![Azure Maps łamana](media/migrate-google-maps-web-app/azure-maps-polyline.png)

**Dodatkowe zasoby:**

- [Dodawanie wierszy do mapy](map-add-line-layer.md)
- [Opcje warstwy linii](/javascript/api/azure-maps-control/atlas.linelayeroptions)
- [Korzystanie z wyrażeń stylu opartych na danych](data-driven-style-expressions-web-sdk.md)

### <a name="adding-a-polygon"></a>Dodawanie wielokątu

Usługi Azure Maps i Google Maps zapewniają podobną obsługę wielokątów. Wielokąty są używane do reprezentowania obszaru na mapie. W poniższych przykładach pokazano, jak utworzyć wielokąt, który tworzy Trójkąt w oparciu o współrzędne środkowe mapy.

#### <a name="before-google-maps"></a>Wcześniej: Google Maps

Klasa Wielokąt akceptuje zestaw opcji. Przekaż tablicę współrzędnych do `paths` opcji wielokąta.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create a polygon.
var polygon = new google.maps.Polygon({
    paths: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1),
        center
    ],
    strokeColor: 'red',
    strokeWeight: 2,
    fillColor: 'rgba(0, 255, 0, 0.5)'
});

//Add the polygon to the map
polygon.setMap(map);
```

![Wielokąt usługi Google Maps](media/migrate-google-maps-web-app/google-maps-polygon.png)

#### <a name="after-azure-maps"></a>Po: Azure Maps

Dodaj `Polygon` `MultiPolygon` obiekty lub do źródła danych. Renderowanie obiektu na mapie przy użyciu warstw. Renderowanie obszaru wielokąta przy użyciu warstwy wielokąta. I, renderowanie konspektu wielokąta przy użyciu warstwy liniowej.

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
![Azure Maps Wielokąt](media/migrate-google-maps-web-app/azure-maps-polygon.png)

**Dodatkowe zasoby:**

- [Dodawanie wielokąta do mapy](map-add-shape.md)
- [Dodaj okrąg do mapy](map-add-shape.md#add-a-circle-to-the-map)
- [Opcje warstw wielokątów](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)
- [Opcje warstwy linii](/javascript/api/azure-maps-control/atlas.linelayeroptions)
- [Korzystanie z wyrażeń stylu opartych na danych](data-driven-style-expressions-web-sdk.md)

### <a name="display-an-info-window"></a>Wyświetl okno informacji

Dodatkowe informacje dla jednostki mogą być wyświetlane na mapie jako `google.maps.InfoWindow` Klasa w usłudze mapy Google. W Azure Maps funkcje te można osiągnąć przy użyciu `atlas.Popup` klasy. Następne przykłady umożliwiają dodanie znacznika do mapy. Po kliknięciu znacznika zostanie wyświetlone okno informacji lub menu podręczne.

#### <a name="before-google-maps"></a>Wcześniej: Google Maps

Tworzenie wystąpienia okna informacji przy użyciu `google.maps.InfoWindow` konstruktora.

```javascript
//Add a marker in which to display an infowindow for.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(47.6, -122.33),
    map: map
});

//Create an infowindow.
var infowindow = new google.maps.InfoWindow({
    content: '<div style="padding:5px"><b>Hello World!</b></div>'
});

//Add a click event to the marker to open the infowindow.
marker.addListener('click', function () {
    infowindow.open(map, marker);
});
```
![Okienko podręczne usługi Google Maps](media/migrate-google-maps-web-app/google-maps-popup.png)

#### <a name="after-azure-maps"></a>Po: Azure Maps

Użyjmy okna podręcznego, aby wyświetlić dodatkowe informacje o lokalizacji. Przekaż kod HTML `string` lub `HTMLElement` obiekt do `content` opcji okna podręcznego. Jeśli chcesz, okna podręczne mogą być wyświetlane niezależnie od dowolnego kształtu. W ten sposób wyskakujące okienka wymagają `position` określenia wartości. Określ `position` wartość. Aby wyświetlić okno podręczne, wywołaj `open` metodę i Przekaż, `map` w której ma być wyświetlane okno podręczne.

```javascript
//Add a marker to the map in which to display a popup for.
var marker = new atlas.HtmlMarker({
    position: [-122.33, 47.6]
});

//Add the marker to the map.
map.markers.add(marker);

//Create a popup.
var popup = new atlas.Popup({
    content: '<div style="padding:5px"><b>Hello World!</b></div>',
    position: [-122.33, 47.6],
    pixelOffset: [0, -35]
});

//Add a click event to the marker to open the popup.
map.events.add('click', marker, function () {
    //Open the popup
    popup.open(map);
});
```
![Azure Maps menu podręcznego](media/migrate-google-maps-web-app/azure-maps-popup.png)

> [!NOTE]
> Można to zrobić w taki sam sposób, jak w przypadku symboli, bąbelków, linii lub wielokątów, przekazując wybraną warstwę do kodu zdarzenia Maps zamiast znacznika.

**Dodatkowe zasoby:**

- [Dodawanie menu podręcznego](map-add-popup.md)
- [Podręczne z zawartością multimedialną](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
- [Okna podręczne dla kształtów](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
- [Używanie podręcznego z wieloma numerami PIN](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
- [Popup — Klasa](/javascript/api/azure-maps-control/atlas.popup)
- [Opcje wyskakujące](/javascript/api/azure-maps-control/atlas.popupoptions)

### <a name="import-a-geojson-file"></a>Importowanie pliku GEOJSON

Usługi Mapy Google obsługują ładowanie i dynamiczne Określanie stylu GEOJSON danych za pośrednictwem `google.maps.Data` klasy. Funkcje tej klasy są bardziej wyrównane przy użyciu stylów opartych na danych Azure Maps. Jednak istnieje kluczowa różnica. Za pomocą usługi Google Maps należy określić funkcję wywołania zwrotnego. Logika biznesowa do napełniania stylów każdej funkcji przetwarzanej pojedynczo w wątku interfejsu użytkownika. Ale w Azure Maps warstwy obsługują Określanie wyrażeń opartych na danych jako opcje stylów. Te wyrażenia są przetwarzane w czasie renderowania w osobnym wątku. Podejście Azure Maps poprawia wydajność renderowania. Ta korzyść jest zauważalna, gdy duże zestawy danych muszą być renderowane szybko.

Poniższe przykłady ładują strumieniowe źródło danych ze wszystkich trzęsienia, w ciągu ostatnich siedmiu dni od agencji USGS. Dane dotyczące trzęsienia ziemi są renderowane jako okręgi skalowane na mapie. Kolor i skala każdego okręgu bazują na wielkości każdej ziemi, która jest przechowywana we `"mag"` właściwości każdej funkcji w zestawie danych. Jeśli wartość jest większa lub równa pięciu, okrąg będzie czerwony. Jeśli jest większa lub równa trzy, ale mniej niż pięć, okrąg zostanie pomarańczowy. Jeśli wartość jest mniejsza niż trzy, okrąg będzie zielony. Promień każdego okręgu będzie wykładniczy wielkości pomnożonej przez 0,1.

#### <a name="before-google-maps"></a>Wcześniej: Google Maps

Określ pojedynczą funkcję wywołania zwrotnego w `map.data.setStyle` metodzie. Wewnątrz funkcji wywołania zwrotnego należy zastosować logikę biznesową do każdej funkcji. Załaduj kanał informacyjny GEOJSON przy użyciu `map.data.loadGeoJson` metody.

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
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Define a callback to style each feature.
            map.data.setStyle(function (feature) {

                //Extract the 'mag' property from the feature.
                var mag = parseFloat(feature.getProperty('mag'));

                //Set the color value to 'green' by default.
                var color = 'green';

                //If the mag value is greater than 5, set the color to 'red'.
                if (mag >= 5) {
                    color = 'red';
                }
                //If the mag value is greater than 3, set the color to 'orange'.
                else if (mag >= 3) {
                    color = 'orange';
                }

                return /** @type {google.maps.Data.StyleOptions} */({
                    icon: {
                        path: google.maps.SymbolPath.CIRCLE,

                        //Scale the radius based on an exponential of the 'mag' value.
                        scale: Math.exp(mag) * 0.1,
                        fillColor: color,
                        fillOpacity: 0.75,
                        strokeWeight: 2,
                        strokeColor: 'white'
                    }
                });
            });

            //Load the data feed.
            map.data.loadGeoJson(earthquakeFeed);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Mapa GEOJSON usługi Google Maps](media/migrate-google-maps-web-app/google-maps-geojson.png)

#### <a name="after-azure-maps"></a>Po: Azure Maps

GEOJSON jest podstawowym typem danych w Azure Maps. Zaimportuj go do źródła danych przy użyciu `datasource.importFromUrl` metody. Użyj warstwy bąbelkowej. Warstwa bąbelków zapewnia funkcję renderowania okręgów skalowanych na podstawie właściwości funkcji w źródle danych. Zamiast korzystać z funkcji wywołania zwrotnego, logika biznesowa jest konwertowana na wyrażenie i przenoszona do opcji stylu. Wyrażenia definiują sposób działania logiki biznesowej. Wyrażenia mogą być przesyłane do innego wątku i oceniane względem danych funkcji. Wiele źródeł danych i warstw można dodać do Azure Maps, z których każda ma inną logikę biznesową. Ta funkcja umożliwia renderowanie wielu zestawów danych na mapie na różne sposoby.

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
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
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

                //Create a layer to render the data points as scaled circles.
                map.layers.add(new atlas.layer.BubbleLayer(datasource, null, {
                    //Make the circles semi-transparent.
                    opacity: 0.75,

                    color: [
                        'case',

                        //If the mag value is greater than 5, return 'red'.
                        ['>=', ['get', 'mag'], 5],
                        'red',

                        //If the mag value is greater than 3, return 'orange'.
                        ['>=', ['get', 'mag'], 3],
                        'orange',

                        //Return 'green' as a fallback.
                        'green'
                    ],

                    //Scale the radius based on an exponential of the 'mag' value.
                    radius: ['*', ['^', ['e'], ['get', 'mag']], 0.1]
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



![Azure Maps GEOJSON](media/migrate-google-maps-web-app/azure-maps-geojson.png)

**Dodatkowe zasoby:**

- [Dodaj warstwę symboli](map-add-pin.md)
- [Dodaj warstwę bąbelkową](map-add-bubble-layer.md)
- [Dane punktu klastra](clustering-point-data-web-sdk.md)
- [Korzystanie z wyrażeń stylu opartych na danych](data-driven-style-expressions-web-sdk.md)

### <a name="marker-clustering"></a>Znaczniki klastra

Podczas wizualizacji wielu punktów danych na mapie punkty mogą nakładać się na siebie nawzajem. Nakładanie się sprawia, że mapa jest nieczytelna, a mapa staje się trudna do odczytania i użycia. Dane punktu klastrowania to proces łączenia punktów danych, które znajdują się blisko siebie i reprezentują je na mapie jako pojedyncze klastrowane punkty danych. Gdy użytkownik powiększa mapę, klastry rozdzielą się na poszczególne punkty danych. Punkty danych klastra w celu poprawy środowiska użytkownika i mapy wydajności.

W poniższych przykładach kod ładuje Źródło GEOJSON danych z ziemi z zeszłego tygodnia i dodaje je do mapy. Klastry są renderowane jako przeskalowane i kolorowe koła. Skala i kolor kół są zależne od liczby punktów, które zawierają.

> [!NOTE]
> Usługi Google Maps i Azure Maps używają nieco różnych algorytmów klastrowania. W związku z tym czasami dystrybucja punktów w klastrach różni się.

#### <a name="before-google-maps"></a>Wcześniej: Google Maps

Użyj biblioteki MarkerCluster do znaczników klastra. Ikony klastra są ograniczone do obrazów, które mają numery od jednej do pięciu jako nazwy. Są one hostowane w tym samym katalogu.

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
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Download the GeoJSON data.
            fetch(earthquakeFeed)
                .then(function (response) {
                    return response.json();
                }).then(function (data) {
                    //Loop through the GeoJSON data and create a marker for each data point.
                    var markers = [];

                    for (var i = 0; i < data.features.length; i++) {

                        markers.push(new google.maps.Marker({
                            position: new google.maps.LatLng(data.features[i].geometry.coordinates[1], data.features[i].geometry.coordinates[0])
                        }));
                    }

                    //Create a marker clusterer instance and tell it where to find the cluster icons.
                    var markerCluster = new MarkerClusterer(map, markers,
                        { imagePath: 'https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/m' });
                });
        }
    </script>

    <!-- Load the marker cluster library. -->
    <script src="https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/markerclusterer.js"></script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```



![Klastrowanie usługi Google Maps](media/migrate-google-maps-web-app/google-maps-clustering.png)

#### <a name="after-azure-maps"></a>Po: Azure Maps

Dodawanie danych i zarządzanie nimi w źródle danych. Połącz źródła danych i warstwy, a następnie Renderuj dane. `DataSource`Klasa w Azure Maps zawiera kilka opcji klastrowania.

- `cluster` — Informuje źródło danych o danych punktu klastra.
- `clusterRadius` -Promień w pikselach do wspólnej tabeli punktów.
- `clusterMaxZoom` — Maksymalny poziom powiększenia, w którym występuje klastrowanie. W przypadku powiększania więcej niż na tym poziomie wszystkie punkty są renderowane jako symbole.
- `clusterProperties` -Definiuje niestandardowe właściwości, które są obliczane przy użyciu wyrażeń dla wszystkich punktów w każdym klastrze i dodawane do właściwości każdego punktu klastra.

Po włączeniu klastrowania źródło danych wyśle klastrowane i nieklastrowane punkty danych do warstw na potrzeby renderowania. Źródło danych umożliwia klastrowanie setek tysięcy punktów danych. Klastrowany punkt danych ma następujące właściwości:

| Nazwa właściwości             | Typ    | Opis   |
|---------------------------|---------|---------------|
| `cluster`                 | boolean | Wskazuje, czy funkcja reprezentuje klaster. |
| `cluster_id`              | ciąg  | Unikatowy identyfikator klastra, który może być używany ze źródłem danych `getClusterExpansionZoom` , `getClusterChildren` i `getClusterLeaves` metodami. |
| `point_count`             | liczba  | Liczba punktów, które zawiera klaster.  |
| `point_count_abbreviated` | ciąg  | Ciąg, który skróci wartość, `point_count` Jeśli jest długi. (na przykład 4 000 to 4K)  |

`DataSource`Klasa ma następującą funkcję pomocniczą do uzyskiwania dostępu do dodatkowych informacji o klastrze przy użyciu `cluster_id` .

| Metoda | Typ zwracany | Opis |
|--------|-------------|-------------|
| `getClusterChildren(clusterId: number)` | &lt; &lt; Geometria funkcji macierzy Promise &lt; , dowolny &gt; \| kształt&gt;&gt; | Pobiera elementy podrzędne danego klastra na następnym poziomie powiększenia. Te elementy podrzędne mogą być kombinacją kształtów i podklastrów. Podklastry będą funkcjami o właściwościach pasujących do ClusteredProperties. |
| `getClusterExpansionZoom(clusterId: number)` | Numer obietnicy &lt;&gt; | Oblicza poziom powiększenia, przy którym klaster rozpocznie rozszerzanie lub przerywanie. |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | &lt; &lt; Geometria funkcji macierzy Promise &lt; , dowolny &gt; \| kształt&gt;&gt; | Pobiera wszystkie punkty w klastrze. Ustaw wartość `limit` na, aby zwracała podzestaw punktów, i Użyj `offset` strony do za pomocą punktów. |

Podczas renderowania danych klastrowanych na mapie często najlepiej jest używać co najmniej dwóch warstw. W poniższym przykładzie zastosowano trzy warstwy. Warstwa bąbelkowa służąca do rysowania skalowanego kolorowego koła na podstawie rozmiaru klastrów. Warstwa symboli służąca do renderowania rozmiaru klastra jako tekstu. I używa drugiej warstwy symbol do renderowania punktów nieklastrowanych. Istnieje wiele sposobów renderowania danych klastrowanych. Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [danych punktu klastra](clustering-point-data-web-sdk.md) .

Bezpośrednio Importuj dane GEOJSON przy użyciu `importDataFromUrl` funkcji w `DataSource` klasie, wewnątrz mapy Azure Maps.

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

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
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
                        filter: ['has', 'point_count'] //Only rendered data points which have a point_count property, which clusters do.
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



![Azure Maps klastrowanie](media/migrate-google-maps-web-app/azure-maps-clustering.png)

**Dodatkowe zasoby:**

- [Dodaj warstwę symboli](map-add-pin.md)
- [Dodaj warstwę bąbelkową](map-add-bubble-layer.md)
- [Dane punktu klastra](clustering-point-data-web-sdk.md)
- [Korzystanie z wyrażeń stylu opartych na danych](data-driven-style-expressions-web-sdk.md)

### <a name="add-a-heat-map"></a>Dodawanie mapy cieplnej

Mapy cieplne, znane także jako mapy gęstości punktów, są typem wizualizacji danych. Są one używane do reprezentowania gęstości danych przy użyciu szeregu kolorów. Są one często używane do wyświetlania danych "gorąca" na mapie. Mapy cieplne to świetny sposób renderowania zestawów danych z dużymi punktami.

Poniższe przykłady ładują strumieniowe źródło danych ze wszystkich trzęsienia, w ciągu ostatniego miesiąca, z agencji USGS i renderuje je jako ważoną mapy cieplnej. `"mag"`Właściwość jest używana jako waga.

#### <a name="before-google-maps"></a>Wcześniej: Google Maps

Aby utworzyć mapę cieplną, Załaduj bibliotekę "Wizualizacja", dodając `&libraries=visualization` do adresu URL skryptu interfejsu API. Warstwa mapy cieplnej w usłudze mapy Google nie obsługuje bezpośrednio danych GEOJSON. Najpierw Pobierz dane i Przekonwertuj je na tablicę ważonych punktów danych:

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

            var map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2,
                mapTypeId: 'satellite'
            });

            //Download the GeoJSON data.
            fetch(url).then(function (response) {
                return response.json();
            }).then(function (res) {
                var points = getDataPoints(res);

                var heatmap = new google.maps.visualization.HeatmapLayer({
                    data: points
                });
                heatmap.setMap(map);
            });
        }

        function getDataPoints(geojson, weightProp) {
            var points = [];

            for (var i = 0; i < geojson.features.length; i++) {
                var f = geojson.features[i];

                if (f.geometry.type === 'Point') {
                    points.push({
                        location: new google.maps.LatLng(f.geometry.coordinates[1], f.geometry.coordinates[0]),
                        weight: f.properties[weightProp]
                    });
                }
            }

            return points;
        } 
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]&libraries=visualization" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```



![Mapa cieplna usługi Google Maps](media/migrate-google-maps-web-app/google-maps-heatmap.png)

#### <a name="after-azure-maps"></a>Po: Azure Maps

Załaduj dane GEOJSON do źródła danych i połącz je ze źródłem danych w warstwie mapy cieplnej. Właściwość, która będzie używana dla wagi, może być przenoszona do `weight` opcji przy użyciu wyrażenia. Bezpośrednio Importuj dane GEOJSON do Azure Maps przy użyciu `importDataFromUrl` funkcji w `DataSource` klasie.

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
                style: 'satellite',

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
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
                    weight: ['get', 'mag'],
                    intensity: 0.005,
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



![Mapa cieplna Azure Maps](media/migrate-google-maps-web-app/azure-maps-heatmap.png)

**Dodatkowe zasoby:**

- [Dodawanie warstwy mapy cieplnej](map-add-heat-map-layer.md)
- [Klasa warstwy mapy cieplnej](/javascript/api/azure-maps-control/atlas.layer.heatmaplayer)
- [Opcje warstwy mapy cieplnej](/javascript/api/azure-maps-control/atlas.heatmaplayeroptions)
- [Korzystanie z wyrażeń stylu opartych na danych](data-driven-style-expressions-web-sdk.md)

### <a name="overlay-a-tile-layer"></a>Nałóż warstwę kafelków

Warstwy kafelków w Azure Maps są znane jako nakładki obrazów w usłudze mapy Google. Warstwy kafelków umożliwiają nakładanie dużych obrazów, które zostały podzielone na mniejsze obrazy z podziałem na fragmenty, które są wyrównane do systemu fragmentów mapy. To podejście jest często używane do nakładania dużych obrazów lub dużych zestawów danych.

Poniższe przykłady nakładają warstwę kafelków radarowych z Iowa środowiska Mesonet of Iowa State University.

#### <a name="before-google-maps"></a>Wcześniej: Google Maps

W usłudze mapy Google warstwy kafelków można tworzyć przy użyciu `google.maps.ImageMapType` klasy.

```javascript
map.overlayMapTypes.insertAt(0, new google.maps.ImageMapType({
    getTileUrl: function (coord, zoom) {
        return "https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/" + zoom + "/" + coord.x + "/" + coord.y;
    },
    tileSize: new google.maps.Size(256, 256),
    opacity: 0.8
}));
```



![Warstwa kafelków usługi Google Maps](media/migrate-google-maps-web-app/google-maps-tile-layer.png)

#### <a name="after-azure-maps"></a>Po: Azure Maps

Dodaj warstwę kafelków do mapy podobnie jak każda inna warstwa. Użyj sformatowanego adresu URL, który zawiera symbole zastępcze x, y powiększenia; `{x}`, `{y}` , `{z}`  Aby określić warstwę, w której mają być dostępne kafelki. Azure Maps warstw kafelków obsługują `{quadkey}` również `{bbox-epsg-3857}` `{subdomain}` symbole zastępcze, i.

> [!TIP]
> W Azure Maps warstwach można łatwo renderować poniżej innych warstw, w tym warstw mapy podstawowej. Często wskazane jest renderowanie warstw kafelków poniżej etykiet mapy, dzięki czemu można je łatwo odczytać. `map.layers.add`Metoda przyjmuje drugi parametr, który jest identyfikatorem warstwy, w której ma zostać wstawiona Nowa warstwa poniżej. Aby wstawić warstwę kafelków pod etykietami mapy, użyj następującego kodu: `map.layers.add(myTileLayer, "labels");`

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```



![Warstwa kafelków Azure Maps](media/migrate-google-maps-web-app/azure-maps-tile-layer.png)

> [!TIP]
> Żądania kafelków można przechwytywać przy użyciu `transformRequest` opcji mapy. Pozwoli to na modyfikację lub dodanie nagłówków do żądania w razie potrzeby.

**Dodatkowe zasoby:**

- [Dodawanie warstw kafelków](map-add-tile-layer.md)
- [Klasa kafelków](/javascript/api/azure-maps-control/atlas.layer.tilelayer)
- [Opcje warstwy kafelków](/javascript/api/azure-maps-control/atlas.tilelayeroptions)

### <a name="show-traffic-data"></a>Wyświetlanie danych o ruchu

Dane o ruchu można przemieścić zarówno na platformie Azure, jak i w usłudze Google Maps.

#### <a name="before-google-maps"></a>Wcześniej: Google Maps

Nałóż dane ruchu na mapie za pomocą warstwy ruchu.

```javascript
var trafficLayer = new google.maps.TrafficLayer();
trafficLayer.setMap(map);
```



![Ruch usługi Google Maps](media/migrate-google-maps-web-app/google-maps-traffic.png)

#### <a name="after-azure-maps"></a>Po: Azure Maps

Azure Maps oferuje kilka różnych opcji wyświetlania ruchu. Wyświetlaj zdarzenia dotyczące ruchu, takie jak zamknięcie dróg i wypadki jako ikony na mapie. Nałóż przepływ ruchu i kolorowe zakodowane drogi na mapie. Kolory można modyfikować w zależności od normalnego oczekiwanego opóźnienia lub bezwzględnego opóźnienia. Dane o zdarzeniach w Azure Maps są aktualizowane co minutę i przepływają aktualizacje danych co dwie minuty.

Przypisz odpowiednie wartości dla `setTraffic` opcji.

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```



![Ruch Azure Maps](media/migrate-google-maps-web-app/azure-maps-traffic.png)

Po kliknięciu jednej z ikon ruchu w Azure Maps w oknie podręcznym zostaną wyświetlone dodatkowe informacje.



![Zdarzenie Azure Maps ruchu](media/migrate-google-maps-web-app/azure-maps-traffic-incident.png)

**Dodatkowe zasoby:**

- [Pokaż ruch na mapie](map-show-traffic.md)
- [Opcje nakładki ruchu](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)

### <a name="add-a-ground-overlay"></a>Dodawanie nakładki uziemienia

Zarówno platforma Azure, jak i usługa Google Maps obsługują nałożenie obrazów ze spisem na mapie. Obrazy geograficzne są przesuwane i skalowane w miarę kadrowania i powiększania mapy. W usłudze Google Maps obrazy geograficzne są znane jako nakładki naziemne w Azure Maps są one nazywane warstwami obrazu. Doskonale nadaje się do kompilowania planów piętra, nakładania starych map lub obrazów z drona.

#### <a name="before-google-maps"></a>Wcześniej: Google Maps

Określ adres URL obrazu, który chcesz nałożyć, i obwiednię, aby powiązać obraz na mapie. Ten przykład nakłada obraz mapy [Newark nowej Jersey z 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) na mapie.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map, historicalOverlay;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.740, -74.18),
                zoom: 12
            });

            var imageBounds = {
                north: 40.773941,
                south: 40.712216,
                east: -74.12544,
                west: -74.22655
            };

            historicalOverlay = new google.maps.GroundOverlay(
                'https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg',
                imageBounds);
            historicalOverlay.setMap(map);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

Uruchomienie tego kodu w przeglądarce spowoduje wyświetlenie mapy, która wygląda jak na poniższej ilustracji:

![Nakładka obrazów usługi Google Maps](media/migrate-google-maps-web-app/google-maps-image-overlay.png)

#### <a name="after-azure-maps"></a>Po: Azure Maps

Użyj `atlas.layer.ImageLayer` klasy do nakładania obrazów ze spisem. Ta klasa wymaga adresu URL do obrazu oraz zestawu współrzędnych dla czterech rogów obrazu. Obraz musi być hostowany w tej samej domenie lub mieć włączony mechanizm CORs.

> [!TIP]
> Jeśli masz tylko informacje o północy, południe, wschód, zachód i rotacja, a nie masz współrzędnych dla każdego rogu obrazu, możesz użyć metody statycznej [`atlas.layer.ImageLayer.getCoordinatesFromEdges`](/javascript/api/azure-maps-control/atlas.layer.imagelayer#getcoordinatesfromedges-number--number--number--number--number-) .

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

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
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



![Nakładka obrazu Azure Maps](media/migrate-google-maps-web-app/azure-maps-image-overlay.png)

**Dodatkowe zasoby:**

- [Nakładka obrazu](map-add-image-layer.md)
- [Klasa warstwy obrazu](/javascript/api/azure-maps-control/atlas.layer.imagelayer)

### <a name="add-kml-data-to-the-map"></a>Dodawanie danych KML do mapy

Zarówno platforma Azure, jak i usługa Google Maps mogą importować i renderować dane KML, KMZ i GeoRSS na mapie. Azure Maps obsługuje również GPX, GML, przestrzenne pliki CSV, GEOJSON, dobrze znany tekst (WKT), Web-Mapping Services (WMS), usługi Web-Mapping kafelków () i usługi Web Feature Services (WFS). Azure Maps odczytuje pliki lokalnie do pamięci i w większości przypadków może obsłużyć znacznie większe pliki KML. 

#### <a name="before-google-maps"></a>Wcześniej: Google Maps


```javascript
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map, historicalOverlay;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(0, 0),
                zoom: 1
            });

             var layer = new google.maps.KmlLayer({
              url: 'https://googlearchive.github.io/js-v2-samples/ggeoxml/cta.kml',
              map: map
            });
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

Uruchomienie tego kodu w przeglądarce spowoduje wyświetlenie mapy, która wygląda jak na poniższej ilustracji:

![Google Maps KML](media/migrate-google-maps-web-app/google-maps-kml.png)

#### <a name="after-azure-maps"></a>Po: Azure Maps

W Azure Maps GEOJSON jest głównym formatem danych używanym w zestawie SDK sieci Web, dodatkowe formaty danych przestrzennych można łatwo zintegrować przy użyciu [przestrzennego modułu we/wy](/javascript/api/azure-maps-spatial-io/). Ten moduł ma funkcje do odczytu i zapisu danych przestrzennych, a także prostą warstwę danych, która może łatwo renderować dane z dowolnego z tych formatów danych przestrzennych. Aby odczytać dane w pliku danych przestrzennych, Przekaż adres URL lub dane pierwotne jako ciąg lub obiekt BLOB do `atlas.io.read` funkcji. Spowoduje to zwrócenie wszystkich przeanalizowanych danych z pliku, który można następnie dodać do mapy. KML jest nieco bardziej skomplikowany niż format danych przestrzennych, ponieważ zawiera on wiele informacji o stylu. `SpatialDataLayer`Klasa obsługuje renderowanie większości tych stylów, ale obrazy ikon muszą zostać załadowane do mapy przed załadowaniem danych funkcji, a nakładki gruntowe muszą być dodawane do mapy osobno. Podczas ładowania danych za pośrednictwem adresu URL powinno ono być hostowane w punkcie końcowym z włączoną funkcją CORs lub usługa serwera proxy powinna zostać przeniesiona jako opcja do funkcji Read. 

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

    <!-- Add reference to the Azure Maps Spatial IO module. -->
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

    <script type='text/javascript'>
        var map, datasource, layer;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                view: 'Auto',

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
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
                atlas.io.read('https://googlearchive.github.io/js-v2-samples/ggeoxml/cta.kml').then(async r => {
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


![Azure Maps KML](media/migrate-google-maps-web-app/azure-maps-kml.png)</center>


**Dodatkowe zasoby:**

- [Atlas. IO. Read — funkcja](/javascript/api/azure-maps-spatial-io/atlas.io#read-string---arraybuffer---blob--spatialdatareadoptions-)
- [SimpleDataLayer](/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)
- [SimpleDataLayerOptions](/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

## <a name="additional-code-samples"></a>Dodatkowe przykłady kodu

Poniżej przedstawiono kilka dodatkowych przykładów kodu związanych z migracją usługi Google Maps:

- [Narzędzia do rysowania](map-add-drawing-toolbar.md)
- [Ogranicz mapowanie do dwóch panoramowania palca](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Map%20to%20Two%20Finger%20Panning)
- [Ogranicz powiększenie kółka przewijania](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Scroll%20Wheel%20Zoom)
- [Tworzenie kontrolki na pełnym ekranie](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Create%20a%20Fullscreen%20Control)

**Services**

- [Korzystanie z modułu Azure Maps Services](how-to-use-services-module.md)
- [Wyszukiwanie punktów orientacyjnych](map-search-location.md)
- [Uzyskiwanie informacji z współrzędnych (odwrotne geokod)](map-get-information-from-coordinate.md)
- [Wyświetlanie instrukcji dotyczących trasy z punktu A do punktu B](map-route.md)
- [Wyszukaj automatyczne sugerowanie przy użyciu interfejsu użytkownika JQuery](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

## <a name="google-maps-v3-to-azure-maps-web-sdk-class-mapping"></a>Mapowanie wersji v3 usługi Google Maps do Azure Maps Web SDK

Poniższy dodatek zawiera krzyżowe odwołanie do najczęściej używanych klas w usłudze Google Maps v3 i równoważnej Azure Maps Web SDK.

### <a name="core-classes"></a>Klasy podstawowe

| Mapy Google   | Azure Maps  |
|---------------|-------------|
| `google.maps.Map` | [Atlas. Zmapować](/javascript/api/azure-maps-control/atlas.map)  |
| `google.maps.InfoWindow` | [Atlas. Elementy](/javascript/api/azure-maps-control/atlas.popup)  |
| `google.maps.InfoWindowOptions` | [Atlas. PopupOptions](https://docs.microsoft.com/) |
| `google.maps.LatLng`  | [Atlas. Data. Position](/javascript/api/azure-maps-control/atlas.data.position)  |
| `google.maps.LatLngBounds` | [Atlas. Data. BoundingBox](/javascript/api/azure-maps-control/atlas.data.boundingbox) |
| `google.maps.MapOptions`  | [Atlas. CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)<br/>[Atlas. CameraBoundsOptions](/javascript/api/azure-maps-control/atlas.cameraboundsoptions)<br/>[Atlas. Opcje](/javascript/api/azure-maps-control/atlas.serviceoptions)<br/>[Atlas. StyleOptions](/javascript/api/azure-maps-control/atlas.styleoptions)<br/>[Atlas. UserInteractionOptions](/javascript/api/azure-maps-control/atlas.userinteractionoptions) |
| `google.maps.Point`  | [Atlas. Pikseli](/javascript/api/azure-maps-control/atlas.pixel)   |

## <a name="overlay-classes"></a>Klasy nakładki

| Mapy Google  | Azure Maps  |
|--------------|-------------|
| `google.maps.Marker` | [atlas.HtmlMarker](/javascript/api/azure-maps-control/atlas.htmlmarker)<br/>[Atlas. Data. punkt](/javascript/api/azure-maps-control/atlas.data.point)  |
| `google.maps.MarkerOptions`  | [atlas.HtmlMarkerOptions](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)<br/>[Atlas. Layer. SymbolLayer](/javascript/api/azure-maps-control/atlas.layer.symbollayer)<br/>[Atlas. SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions)<br/>[Atlas. IconOptions](/javascript/api/azure-maps-control/atlas.iconoptions)<br/>[Atlas. TextOptions](/javascript/api/azure-maps-control/atlas.textoptions)<br/>[Atlas. Layer. BubbleLayer](/javascript/api/azure-maps-control/atlas.layer.bubblelayer)<br/>[Atlas. BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions) |
| `google.maps.Polygon`  | [Atlas. Data. Wielokąt](/javascript/api/azure-maps-control/atlas.data.polygon)               |
| `google.maps.PolygonOptions` |[Atlas. Layer. PolygonLayer](/javascript/api/azure-maps-control/atlas.layer.polygonlayer)<br/> [Atlas. PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)<br/> [Atlas. Layer. LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer)<br/> [Atlas. LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions)|
| `google.maps.Polyline` | [Atlas. Data. LineString](/javascript/api/azure-maps-control/atlas.data.linestring)         |
| `google.maps.PolylineOptions` | [Atlas. Layer. LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer)<br/>[Atlas. LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions) |
| `google.maps.Circle`  | Zobacz [Dodawanie okręgu do mapy](map-add-shape.md#add-a-circle-to-the-map)                                     |
| `google.maps.ImageMapType`  | [Atlas. TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer)         |
| `google.maps.ImageMapTypeOptions` | [Atlas. TileLayerOptions](/javascript/api/azure-maps-control/atlas.tilelayeroptions) |
| `google.maps.GroundOverlay`  | [Atlas. Layer. ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer)<br/>[Atlas. ImageLayerOptions](/javascript/api/azure-maps-control/atlas.imagelayeroptions) |

## <a name="service-classes"></a>Klasy usług

Zestaw SDK sieci Web Azure Maps zawiera moduł usług, który można załadować osobno. Ten moduł otacza Azure Maps usług REST za pomocą internetowego interfejsu API i może być używany w aplikacjach JavaScript, TypeScript i Node.js.

| Mapy Google | Azure Maps  |
|-------------|-------------|
| `google.maps.Geocoder` | [Atlas. Service. SearchUrl](/javascript/api/azure-maps-rest/atlas.service.searchurl)  |
| `google.maps.GeocoderRequest`  | [Atlas. SearchAddressOptions](/javascript/api/azure-maps-rest/atlas.service.searchaddressoptions)<br/>[Atlas. SearchAddressRevrseOptions](/javascript/api/azure-maps-rest/atlas.service.searchaddressreverseoptions)<br/>[Atlas. SearchAddressReverseCrossStreetOptions](/javascript/api/azure-maps-rest/atlas.service.searchaddressreversecrossstreetoptions)<br/>[Atlas. SearchAddressStructuredOptions](/javascript/api/azure-maps-rest/atlas.service.searchaddressstructuredoptions)<br/>[Atlas. SearchAlongRouteOptions](/javascript/api/azure-maps-rest/atlas.service.searchalongrouteoptions)<br/>[Atlas. SearchFuzzyOptions](/javascript/api/azure-maps-rest/atlas.service.searchfuzzyoptions)<br/>[Atlas. SearchInsideGeometryOptions](/javascript/api/azure-maps-rest/atlas.service.searchinsidegeometryoptions)<br/>[Atlas. SearchNearbyOptions](/javascript/api/azure-maps-rest/atlas.service.searchnearbyoptions)<br/>[Atlas. SearchPOIOptions](/javascript/api/azure-maps-rest/atlas.service.searchpoioptions)<br/>[Atlas. SearchPOICategoryOptions](/javascript/api/azure-maps-rest/atlas.service.searchpoicategoryoptions) |
| `google.maps.DirectionsService`  | [Atlas. Service. RouteUrl](/javascript/api/azure-maps-rest/atlas.service.routeurl)  |
| `google.maps.DirectionsRequest`  | [Atlas. CalculateRouteDirectionsOptions](/javascript/api/azure-maps-rest/atlas.service.calculateroutedirectionsoptions) |
| `google.maps.places.PlacesService` | [Atlas. Service. SearchUrl](/javascript/api/azure-maps-rest/atlas.service.searchurl)  |

## <a name="libraries"></a>Biblioteki

Biblioteki dodają dodatkowe funkcje do mapy. Wiele z tych bibliotek znajduje się w podstawowym zestawie SDK Azure Maps. Poniżej przedstawiono niektóre równoważne klasy używane zamiast tych bibliotek usługi Google Maps

| Mapy Google           | Azure Maps   |
|-----------------------|--------------|
| Biblioteka rysowania       | [Moduł narzędzi do rysowania](set-drawing-options.md) |
| Biblioteka geometrii      | [Atlas. Math](/javascript/api/azure-maps-control/atlas.math)   |
| Biblioteka wizualizacji | [Warstwa mapy cieplnej](map-add-heat-map-layer.md) |

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat Azure Maps Web SDK:

> [!div class="nextstepaction"]
> [Jak używać kontrolki mapy](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Jak używać modułu narzędzi do rysowania](set-drawing-options.md)

> [!div class="nextstepaction"]
> [Jak używać modułu usług](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Jak używać przestrzennego modułu we/wy](how-to-use-spatial-io-module.md)