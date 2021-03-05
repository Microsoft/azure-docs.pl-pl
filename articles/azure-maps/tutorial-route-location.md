---
title: 'Samouczek: Znajdowanie trasy do lokalizacji | Mapy Microsoft Azure'
description: Samouczek dotyczący znajdowania trasy do punktu orientacyjnego. Zapoznaj się z tematem Ustawianie współrzędnych adresów i wykonywanie zapytań dotyczących usługi Azure Maps Route w celu uzyskania wskazówek dotyczących punktu.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/01/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 1f1647766eeae917f7a2266ebb8f00fdfab3452e
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102210584"
---
# <a name="tutorial-how-to-display-route-directions-using-azure-maps-route-service-and-map-control"></a>Samouczek: jak wyświetlić kierunki tras za pomocą usługi Azure Maps Route i kontrolki mapy

W tym samouczku pokazano, jak używać [interfejsu API usługi Route](/rest/api/maps/route) Azure Maps i [kontrolki mapy](./how-to-use-map-control.md) do wyświetlania kierunków tras od początku do punktu końcowego. Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Utwórz i Wyświetl formant mapy na stronie sieci Web. 
> * Definiowanie renderowania wyświetlania trasy przez definiowanie [warstw symboli](map-add-pin.md) i [warstw liniowych](map-add-line-layer.md).
> * Utwórz i Dodaj obiekty GEOJSON do mapy, aby reprezentować punkty początkowe i końcowe.
> * Pobierz wskazówki dotyczące trasy z punktów początkowych i końcowych za pomocą [interfejsu API uzyskiwania tras](/rest/api/maps/route/getroutedirections).

W [tym miejscu](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html)możesz uzyskać pełny kod źródłowy dla przykładu. Przykład na żywo można znaleźć [tutaj](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination).

## <a name="prerequisites"></a>Wymagania wstępne

1. [Utwórz konto Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Uzyskaj podstawowy klucz subskrypcji](quick-demo-map-app.md#get-the-primary-key-for-your-account), nazywany także kluczem podstawowym lub kluczem subskrypcji.

<a id="getcoordinates"></a>

## <a name="create-and-display-the-map-control"></a>Tworzenie i wyświetlanie kontrolki mapy

Poniższe kroki pokazują, jak utworzyć i wyświetlić formant mapy na stronie sieci Web.

1. Na komputerze lokalnym utwórz nowy plik i nadaj mu nazwę **MapRoute.html**.
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

     :::image type="content" source="./media/tutorial-route-location/basic-map.png" alt-text="Renderowanie mapy podstawowej kontrolki mapy":::

## <a name="define-route-display-rendering"></a>Definiowanie renderowania wyświetlania tras

W tym samouczku wyrenderuje trasę przy użyciu warstwy liniowej. Punkty początkowe i końcowe będą renderowane przy użyciu warstwy symboli. Aby uzyskać więcej informacji na temat dodawania warstw liniowych, zobacz [Dodawanie warstwy liniowej do mapy](map-add-line-layer.md). Aby dowiedzieć się więcej na temat warstw symboli, zobacz [Dodawanie warstwy symboli do mapy](map-add-pin.md).

1. Dołącz Poniższy kod JavaScript w `GetMap` funkcji. Ten kod implementuje `ready` procedurę obsługi zdarzeń kontrolki mapy. Pozostała część kodu w tym samouczku zostanie umieszczona wewnątrz `ready` procedury obsługi zdarzeń.

    ```JavaScript
    //Wait until the map resources are ready.
    map.events.add('ready', function() {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: '#2272B9',
            strokeWidth: 5,
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
    });
    ```

    W programie obsługi zdarzeń kontrolki mapy `ready` jest tworzone źródło danych służące do przechowywania trasy od początku do punktu końcowego. Aby zdefiniować sposób renderowania linii trasy, warstwa linii zostanie utworzona i dołączona do źródła danych.  Aby upewnić się, że linia trasy nie obejmuje etykiet drogi, przeszedł drugi parametr z wartością `'labels'` .

    Następnie zostanie utworzona i dołączona do źródła danych. Ta warstwa określa sposób renderowania punktów początkowych i końcowych. Dodano wyrażenia, aby pobrać obraz ikony i informacje o etykiecie tekstowej z właściwości dla każdego obiektu punktu. Aby dowiedzieć się więcej na temat wyrażeń, zobacz [oparte na danych wyrażenia stylów](data-driven-style-expressions-web-sdk.md).

2. Ustaw punkt początkowy jako Microsoft, a punkt końcowy jako stację gazową w Seattle.  W programie obsługi zdarzeń kontrolki mapy `ready` Dodaj następujący kod.

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end points of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.130137, 47.644702]), {
        title: "Redmond",
        icon: "pin-blue"
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.3352, 47.61397]), {
        title: "Seattle",
        icon: "pin-round-blue"
    });

    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 80
    });
    ```

    Ten kod tworzy dwa [obiekty punktu GEOJSON](https://en.wikipedia.org/wiki/GeoJSON) do reprezentowania punktów początkowych i końcowych, które następnie są dodawane do źródła danych. 

    Ostatni blok kodu ustawia widok kamery przy użyciu szerokości geograficznej i długości geograficznej punktu początkowego i końcowego. Punkt początkowy i końcowy zostały dodane do źródła danych. Pole ograniczenia dla punktu początkowego i końcowego jest obliczane przy użyciu funkcji `atlas.data.BoundingBox.fromData`. To ograniczenie służy do ustawiania widoku kamery mapy na całej trasie przy użyciu `map.setCamera` funkcji. Dodano uzupełnienie, aby skompensować wymiary pikseli ikon symboli. Aby uzyskać więcej informacji na temat właściwości setcamera kontrolki mapy, zobacz [setcamera (CameraOptions | CameraBoundsOptions & AnimationOptions)](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) .

3. Zapisz **MapRoute.html** i Odśwież przeglądarkę. Mapa jest teraz wyśrodkowana w Seattle. Niebieski numer PIN Teardrop oznacza punkt początkowy. Okrągły niebieski numer PIN oznacza punkt końcowy.

    :::image type="content" source="./media/tutorial-route-location/map-pins.png" alt-text="Wyświetlanie punktów początkowych i końcowych tras na mapie":::

<a id="getroute"></a>

## <a name="get-route-directions"></a>Pobierz wskazówki dotyczące trasy

W tej sekcji pokazano, jak za pomocą interfejsu API wskazówek dotyczących tras Azure Maps uzyskać wskazówki dotyczące trasy i szacowany czas przybycia z jednego punktu do drugiego.

>[!TIP]
>Usługi tras Azure Maps oferują interfejsy API, które umożliwiają planowanie tras opartych na różnych typach tras, takich jak najszybszy , *najkrótszy*, *thrilling* lub na trasie na podstawie odległości, warunków ruchu oraz używanego trybu transportu. Usługa umożliwia również użytkownikom planowanie przyszłych tras w oparciu o historyczne warunki ruchu. Użytkownicy mogą zobaczyć prognozowanie czasów trwania tras w danym momencie. Aby uzyskać więcej informacji, zobacz [Uzyskiwanie interfejsu API wskazówek dotyczących trasy](/rest/api/maps/route/getroutedirections).

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

2. Po skonfigurowaniu poświadczeń i adresu URL Dołącz następujący kod do `ready` programu obsługi zdarzeń kontrolki. Ten kod konstruuje trasę od punktu początkowego do punktu końcowego. `routeURL`Żąda interfejsu API usługi trasy Azure Maps, aby obliczyć kierunki tras. Zbieranie funkcji GEOJSON z odpowiedzi jest następnie wyodrębniane przy użyciu `geojson.getFeatures()` metody i dodawane do źródła danych.

    ```JavaScript
    //Start and end point input to the routeURL
    var coordinates= [[startPoint.geometry.coordinates[0], startPoint.geometry.coordinates[1]], [endPoint.geometry.coordinates[0], endPoint.geometry.coordinates[1]]];

    //Make a search route request
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {
        //Get data features from response
        var data = directions.geojson.getFeatures();
        datasource.add(data);
    });
    ```

3. Zapisz plik **MapRoute.html** i odśwież przeglądarkę. Mapa powinna teraz wyświetlać trasę od początku do punktu końcowego.

     :::image type="content" source="./media/tutorial-route-location/map-route.png" alt-text="Kontrolka mapy platformy Azure i usługa Route Service":::

W [tym miejscu](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html)możesz uzyskać pełny kod źródłowy dla przykładu. Przykład na żywo można znaleźć [tutaj](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Brak zasobów, które wymagają oczyszczenia.

## <a name="next-steps"></a>Następne kroki

W następnym samouczku pokazano, jak utworzyć zapytanie trasy z ograniczeniami, takimi jak tryb podróży lub typu ładunku. Następnie można wyświetlić wiele tras na tej samej mapie.

> [!div class="nextstepaction"]
> [Znajdowanie tras dla różnych sposobów podróży](./tutorial-prioritized-routes.md)