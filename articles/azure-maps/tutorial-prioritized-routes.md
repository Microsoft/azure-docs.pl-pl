---
title: 'Samouczek: Znajdowanie wielu tras według trybu podróży | Mapy Microsoft Azure'
description: Samouczek dotyczący sposobu użycia Azure Maps, aby znaleźć trasy dla określonych trybów podróży do punktów zainteresowania. Zobacz, jak wyświetlić wiele tras na mapach.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 125ca501dbad74263f32632db44eebd097c3b0a1
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896705"
---
# <a name="tutorial-find-and-display-routes-for-different-modes-of-travel-using-azure-maps"></a>Samouczek: Znajdowanie i wyświetlanie tras dla różnych trybów podróży przy użyciu Azure Maps

W tym samouczku pokazano, jak używać [usługi route](/rest/api/maps/route) Azure Maps i [kontrolki mapy](./how-to-use-map-control.md) do wyświetlania kierunków tras zarówno dla pojazdów prywatnych, jak i pojazdów komercyjnych z `USHazmatClass2` typem ładunku. Ponadto przeprowadzimy Cię przez proces wizualizacji danych o ruchu w czasie rzeczywistym na mapie. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie i wyświetlanie kontrolki mapy na stronie sieci Web
> * Renderowanie danych o ruchu w czasie rzeczywistym na mapie
> * Żądaj i wyświetlaj trasy prywatnych i komercyjnych pojazdów na mapie

## <a name="prerequisites"></a>Wymagania wstępne

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. [Utwórz konto Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account).

3. [Uzyskaj podstawowy klucz subskrypcji](quick-demo-map-app.md#get-the-primary-key-for-your-account), nazywany także kluczem podstawowym lub kluczem subskrypcji. Aby uzyskać więcej informacji na temat uwierzytelniania w Azure Maps, zobacz [Zarządzanie uwierzytelnianiem w programie Azure Maps](how-to-manage-authentication.md).

W [tym miejscu](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html)możesz uzyskać pełny kod źródłowy dla przykładu. Przykład na żywo można znaleźć [tutaj](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel).

## <a name="create-a-new-web-page-using-the-map-control-api"></a>Tworzenie nowej strony internetowej przy użyciu interfejsu API kontrolki mapy

Poniższe kroki pokazują, jak utworzyć i wyświetlić formant mapy na stronie sieci Web.

1. Na komputerze lokalnym utwórz nowy plik i nadaj mu nazwę **MapTruckRoute.html** .
2. Skopiuj/wklej następujący znacznik HTML do pliku.

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title>Map Route</title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>

        <script>
            var map, datasource, client;

            function GetMap() {
                //Add Map Control JavaScript code here.
            }
        </script>
        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #myMap {
                width: 100%;
                height: 100%;
            }
        </style>
    </head>
    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>
    </html>
    ```

     Nagłówek HTML zawiera pliki zasobów CSS i JavaScript hostowane przez bibliotekę kontrolka mapy platformy Azure. `onload`Zdarzenie treści wywołuje `GetMap` funkcję. W następnym kroku dodamy kod inicjalizacji formantu mapy.

3. Dodaj następujący kod JavaScript do funkcji `GetMap`. Zastąp ciąg `<Your Azure Maps Key>` kluczem podstawowym, który został skopiowany z konta Maps.

    ```JavaScript
    //Instantiate a map object
    var map = new atlas.Map("myMap", {
        //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
        authOptions: {
            authType: 'subscriptionKey',
            subscriptionKey: '<Your Azure Maps Key>'
        }
    });
    ```

4. Zapisz plik i otwórz go w przeglądarce. Wyświetlana jest prosta.

    :::image type="content" source="./media/tutorial-prioritized-routes/basic-map.png" alt-text="Renderowanie mapy podstawowej kontrolki mapy":::

## <a name="render-real-time-traffic-data-on-a-map"></a>Renderowanie danych o ruchu w czasie rzeczywistym na mapie

1. Dołącz Poniższy kod JavaScript w `GetMap` funkcji. Ten kod implementuje `ready` procedurę obsługi zdarzeń kontrolki mapy. Pozostała część kodu w tym samouczku zostanie umieszczona wewnątrz `ready` procedury obsługi zdarzeń.

    ```javascript
    map.events.add("ready", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```

    W `ready` obsłudze zdarzeń mapy ustawienie przepływu ruchu na mapie jest ustawione na `relative` , czyli szybkość drogi względem swobodnego przepływu. Aby uzyskać więcej opcji ruchu, zobacz [TrafficOptions Interface (interfejs](/javascript/api/azure-maps-control/atlas.trafficoptions?preserve-view=false&view=azure-maps-typescript-latest)).

2. Zapisz plik **MapTruckRoute.html** i odśwież stronę w przeglądarce. Jeśli powiększesz dowolne miasto, np. Los Angeles, zobaczysz, że ulice są wyświetlane z bieżącymi danymi przepływu ruchu.

    :::image type="content" source="./media/tutorial-prioritized-routes/traffic-map.png" alt-text="Renderowanie mapy podstawowej kontrolki mapy":::

<a id="queryroutes"></a>

## <a name="define-route-display-rendering"></a>Definiowanie renderowania wyświetlania tras

W tym samouczku na mapie zostaną obliczone i wyrenderowane dwie trasy. Pierwsza trasa zostanie obliczona dla prywatnego pojazdu (samochód). Druga trasa zostanie obliczona dla pojazdu komercyjnego (ciężarówki), aby pokazać różnicę między wynikami. Po wyrenderowaniu Mapa wyświetli ikonę symbolu dla punktów początkowych i końcowych trasy, a następnie geometrie wiersz o różne kolory dla każdej ścieżki tras. Aby uzyskać więcej informacji na temat dodawania warstw liniowych, zobacz [Dodawanie warstwy liniowej do mapy](map-add-line-layer.md). Aby dowiedzieć się więcej na temat warstw symboli, zobacz [Dodawanie warstwy symboli do mapy](map-add-pin.md).

1. W programie obsługi zdarzeń kontrolki mapy `ready` Dodaj następujący kod.

    ```JavaScript

    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);

    //Add a layer for rendering the route lines and have it render under the map labels.
    map.layers.add(new atlas.layer.LineLayer(datasource, null, {
        strokeColor: ['get', 'strokeColor'],
        strokeWidth: ['get', 'strokeWidth'],
        lineJoin: 'round',
        lineCap: 'round'
    }), 'labels');

    //Add a layer for rendering point data.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: ['get', 'icon'],
            allowOverlap: true
        },
        textOptions: {
            textField: ['get', 'title'],
            offset: [0, 1.2]
        },
        filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']] //Only render Point or MultiPoints in this layer.
    }));

    ```


    W programie obsługi zdarzeń kontrolki mapy `ready` jest tworzone źródło danych służące do przechowywania trasy od początku do końca. [Wyrażenia](data-driven-style-expressions-web-sdk.md) są używane do pobierania szerokości linii i koloru z właściwości w funkcji linia trasy. Aby upewnić się, że linia trasy nie obejmuje etykiet drogi, przeszedł drugi parametr z wartością `'labels'` .

    Następnie zostanie utworzona i dołączona do źródła danych. Ta warstwa określa sposób renderowania punktów początkowych i końcowych. Dodano wyrażenia, aby pobrać obraz ikony i informacje o etykiecie tekstowej z właściwości dla każdego obiektu punktu. Aby dowiedzieć się więcej na temat wyrażeń, zobacz [oparte na danych wyrażenia stylów](data-driven-style-expressions-web-sdk.md).

2. Ustaw punkt początkowy jako fikcyjną firmę w Seattle o nazwie Fabrikam i punkt końcowy jako pakiet Microsoft Office.  W programie obsługi zdarzeń kontrolki mapy `ready` Dodaj następujący kod.


    ```JavaScript
    //Create the GeoJSON objects which represent the start and end point of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.356099, 47.580045]), {
        title: 'Fabrikam, Inc.',
        icon: 'pin-blue'
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.201164, 47.616940]), {
        title: 'Microsoft - Lincoln Square',
        icon: 'pin-round-blue'
    });

    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    //Fit the map window to the bounding box defined by the start and end positions.
    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 100
    });

    ```

    Ten kod tworzy dwa [obiekty punktu GEOJSON](https://en.wikipedia.org/wiki/GeoJSON) do reprezentowania punktów początkowych i końcowych, które następnie są dodawane do źródła danych.

    Ostatni blok kodu ustawia widok kamery przy użyciu szerokości geograficznej i długości geograficznej punktu początkowego i końcowego. Punkt początkowy i końcowy zostały dodane do źródła danych. Pole ograniczenia dla punktu początkowego i końcowego jest obliczane przy użyciu funkcji `atlas.data.BoundingBox.fromData`. To ograniczenie służy do ustawiania widoku kamery mapy na całej trasie przy użyciu `map.setCamera` funkcji. Dodano uzupełnienie, aby skompensować wymiary pikseli ikon symboli. Aby uzyskać więcej informacji na temat właściwości setcamera kontrolki mapy, zobacz [setcamera (CameraOptions | CameraBoundsOptions & AnimationOptions)](/javascript/api/azure-maps-control/atlas.map?view=azure-maps-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-&preserve-view=false) .

3. Zapisz **TruckRoute.html** i Odśwież przeglądarkę. Mapa jest teraz wyśrodkowana w Seattle. Niebieski numer PIN Teardrop oznacza punkt początkowy. Okrągły niebieski numer PIN oznacza punkt końcowy.

   :::image type="content" source="./media/tutorial-prioritized-routes/pins-map.png" alt-text="Renderowanie mapy podstawowej kontrolki mapy":::

<a id="multipleroutes"></a>

## <a name="request-and-display-private-and-commercial-vehicle-routes-on-a-map"></a>Żądaj i wyświetlaj trasy prywatnych i komercyjnych pojazdów na mapie

W tej sekcji pokazano, jak za pomocą usługi Azure Maps Route uzyskać wskazówki od jednego punktu do drugiego, w zależności od używanego trybu transportu. Będziemy używać dwóch trybów transportu: ciężarówki i samochodu.

>[!TIP]
>Usługa Route Service oferuje interfejsy API do planowania *najszybszych* , *najkrótszych* , *ekonomicznych* lub *thrillingych* tras na podstawie odległości, warunków ruchu oraz używanego trybu transportu. Usługa umożliwia również użytkownikom planowanie przyszłych tras w oparciu o historyczne warunki ruchu. Użytkownicy mogą zobaczyć prognozowanie czasów trwania tras w danym momencie. Aby uzyskać więcej informacji, zobacz [Uzyskiwanie interfejsu API wskazówek dotyczących trasy](/rest/api/maps/route/getroutedirections).

1. W `GetMap` funkcji wewnątrz `ready` procedury obsługi zdarzeń kontrolki Dodaj następujący kod do kodu JavaScript.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   `SubscriptionKeyCredential`Tworzy `SubscriptionKeyCredentialPolicy` do uwierzytelniania żądania HTTP do Azure Maps przy użyciu klucza subskrypcji. `atlas.service.MapsURL.newPipeline()`Przyjmuje `SubscriptionKeyCredential` zasady i tworzy wystąpienie [potoku](/javascript/api/azure-maps-rest/atlas.service.pipeline) . `routeURL`Reprezentuje adres URL służący do Azure Maps operacji [routingu](/rest/api/maps/route) .

2. Po skonfigurowaniu poświadczeń i adresu URL Dodaj następujący kod JavaScript, aby utworzyć trasę trasy ciężarówki od początku do punktu końcowego. Ta trasa jest tworzona i wyświetlana dla wózka przewożącego ruch z `USHazmatClass2` klasą.

    ```JavaScript
    //Start and end point input to the routeURL
    var coordinates= [[startPoint.geometry.coordinates[0], startPoint.geometry.coordinates[1]], [endPoint.geometry.coordinates[0], endPoint.geometry.coordinates[1]]];

    //Make a search route request for a truck vehicle type
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates,{
        travelMode: 'truck',
        vehicleWidth: 2,
        vehicleHeight: 2,
        vehicleLength: 5,
        vehicleLoadType: 'USHazmatClass2'
    }).then((directions) => {
        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#2272B9';
        routeLine.properties.strokeWidth = 9;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.
        datasource.add(routeLine, 0);
    });
    ```

    Powyższy kod wysyła zapytania do usługi Route Azure Maps za pomocą [interfejsu API wskazówek dotyczących trasy Azure Maps](/javascript/api/azure-maps-rest/atlas.service.routeurl#calculateroutedirections-aborter--geojson-position----calculateroutedirectionsoptions-). Linia trasy jest następnie wyodrębniana z kolekcji funkcji GEOJSON z odpowiedzi wyodrębnionej przy użyciu `geojson.getFeatures()` metody. Na koniec linia trasy jest dodawana do źródła danych. Dodajemy ją pod indeksem 0, aby upewnić się, że trasa ciężarówki jest renderowana przed wszystkimi innymi wierszami w źródle danych, ponieważ obliczenie trasy ciężarówki będzie często wolniejsze niż Obliczanie trasy samochodu. Jeśli linia trasa ciężarówki zostanie dodana do źródła danych po tej trasie samochodu, zostanie ona wyrenderowana powyżej. Do linii trasy ciężarówki są dodawane dwie właściwości: kolor niebieskiego obrysu i szerokość obrysu dziewięciu pikseli.

    >[!TIP]
    > Aby wyświetlić wszystkie możliwe opcje i wartości dla interfejsu API wskazówek dotyczących trasy Azure Maps, zobacz [Parametry identyfikatora URI dla wskazówek dotyczących trasy](/rest/api/maps/route/postroutedirections#uri-parameters).

3. Teraz Dołącz następujący kod JavaScript, aby utworzyć trasę dla samochodu.

    ```JavaScript
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {

        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#B76DAB';
        routeLine.properties.strokeWidth = 5;

        //Add the route line to the data source. This will add the car route after the truck route.  
        datasource.add(routeLine);
    });
    ```

    Powyższy kod wysyła zapytania do usługi routingu Azure Maps za pomocą metody  [interfejsu API wskazówek dotyczących trasy Azure Maps](/javascript/api/azure-maps-rest/atlas.service.routeurl#calculateroutedirections-aborter--geojson-position----calculateroutedirectionsoptions-) . Linia trasy jest następnie wyodrębniana z kolekcji funkcji GEOJSON z odpowiedzi wyodrębnionej przy użyciu `geojson.getFeatures()` metody. Na koniec linia trasy jest dodawana do źródła danych. Do linii trasy ciężarówki są dodawane dwie właściwości: purpurowy kolor pociągnięcia oraz szerokość pociągnięcia równą pięć pikseli.

4. Zapisz plik **TruckRoute.html** i Odśwież przeglądarkę sieci Web. Mapa powinna teraz wyświetlać trasy ciężarówki i samochodu.

    :::image type="content" source="./media/tutorial-prioritized-routes/prioritized-routes.png" alt-text="Renderowanie mapy podstawowej kontrolki mapy":::

    Trasa ciężarówki jest wyświetlana przy użyciu grubego niebieskiego wiersza. Trasa samochodowa jest wyświetlana przy użyciu cienkiej linii purpurowej. Trasa samochodowa odbywa się w jeziorach Waszyngton za pośrednictwem I-90, przekazując tunele do obszarów mieszkalnych. Ze względu na to, że tunele znajdują się blisko obszarów mieszkalnych, odpadi z odpadów niebezpiecznych są ograniczone. Trasa ciężarówki, która określa `USHazmatClass2` Typ ładunku, jest skierowana do korzystania z innego autostrady.

W [tym miejscu](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html)możesz uzyskać pełny kod źródłowy dla przykładu. Przykład na żywo można znaleźć [tutaj](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel).

Można również [użyć wyrażeń stylów opartych na danych](data-driven-style-expressions-web-sdk.md)



## <a name="next-steps"></a>Następne kroki

W następnym samouczku przedstawiony jest proces tworzenia prostego lokalizatora sklepów przy użyciu usługi Azure Maps.

> [!div class="nextstepaction"]
> [Create a store locator using Azure Maps (Tworzenie lokalizatora sklepów przy użyciu usługi Azure Maps)](./tutorial-create-store-locator.md)