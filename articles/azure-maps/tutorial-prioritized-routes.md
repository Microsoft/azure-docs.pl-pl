---
title: 'Samouczek: Znajdowanie wielu tras według trybu podróży | Mapy Microsoft Azure'
description: W tym samouczku dowiesz się, jak znaleźć trasy dla różnych trybów podróży przy użyciu Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 01/14/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: c7ed2421f468dfbb64c635683a7909b517105bc7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80333804"
---
# <a name="tutorial-find-routes-for-different-modes-of-travel-using-azure-maps"></a>Samouczek: Znajdowanie tras dla różnych trybów podróży przy użyciu Azure Maps

W tym samouczku pokazano, jak używać konta Azure Maps i usługi Route Service. Usługa Route Service może znaleźć trasę do swojego punktu orientacyjnego, na podstawie którego jest określony tryb podróży. Na mapie można wyświetlić dwie różne trasy, jedną dla samochodów i jeden dla wózków. Usługa routingu bierze pod uwagę ograniczenia ze względu na wysokość i wagę pojazdu lub w przypadku przewozu niebezpiecznego. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie nowej strony internetowej przy użyciu interfejsu API kontrolki mapy
> * Wizualizowanie przepływu ruchu na mapie
> * Tworzenie zapytań o trasę zawierających deklarację sposobu podróży
> * Wyświetlanie wielu tras na mapie

## <a name="prerequisites"></a>Wymagania wstępne
Przed kontynuowaniem postępuj zgodnie z instrukcjami w temacie [Tworzenie konta](quick-demo-map-app.md#create-an-account-with-azure-maps) i wybierz warstwę cenową S1. Postępuj zgodnie z instrukcjami w temacie [Pobieranie klucza podstawowego](quick-demo-map-app.md#get-the-primary-key-for-your-account) , aby uzyskać klucz podstawowy dla konta. Aby uzyskać więcej informacji na temat uwierzytelniania w Azure Maps, zobacz [Zarządzanie uwierzytelnianiem w programie Azure Maps](how-to-manage-authentication.md).

## <a name="create-a-new-map"></a>Tworzenie nowej mapy

Poniższe kroki pokazują, jak utworzyć statyczną stronę HTML osadzoną przy użyciu interfejsu API kontrolki mapy.

1. Na komputerze lokalnym utwórz nowy plik i nadaj mu nazwę **MapTruckRoute.html**.
2. Dodaj następujące składniki HTML do pliku:

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

    Zwróć uwagę, że nagłówek HTML zawiera pliki zasobów CSS i JavaScript obsługiwane przez bibliotekę kontrolek mapy platformy Azure. Zwróć uwagę na zdarzenie `onload` w treści strony, które spowoduje wywołanie funkcji `GetMap` po załadowaniu treści strony. Ta funkcja będzie zawierać śródwierszowy kod JavaScript umożliwiający dostęp do interfejsów API usługi Azure Maps.

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

    `atlas.Map` Klasa zapewnia formant dla wizualizacji i interaktywnej mapy sieci Web oraz jest SKŁADNIKIEM interfejsu API kontrolka mapy platformy Azure.

4. Zapisz plik i otwórz go w przeglądarce. Masz teraz podstawową mapę, którą możesz rozbudowywać.

   ![Wyświetlanie podstawowej mapy](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>Wizualizowanie przepływu ruchu

1. Dodaj widok przepływu ruchu do mapy. Zdarzenie Maps `ready` czeka do momentu załadowania zasobów mapy i gotowości do jej bezpiecznego współdziałania.

    ```javascript
    map.events.add("ready", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```

    W obsłudze `ready` zdarzeń mapy ustawienie przepływu ruchu na mapie jest ustawione na `relative`, czyli szybkość drogi względem swobodnego przepływu. Tę wartość możesz również ustawić na `absolute` (względna szybkość ruchu na drodze) lub `relative-delay` (umożliwia wyświetlanie względnej szybkości, gdy różni się ona od swobodnego przepływu ruchu).

2. Zapisz plik **MapTruckRoute.html** i odśwież stronę w przeglądarce. W przypadku korzystania z mapy i powiększania do Los Angeles należy zobaczyć ulice z bieżącymi danymi o ruchu.

   ![Wyświetlanie ruchu na mapie](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="define-how-the-route-will-be-rendered"></a>Definiowanie sposobu renderowania trasy

W tym samouczku na mapie zostaną obliczone i wyrenderowane dwie trasy. Jedna trasa będzie prowadziła po drogach dostępnych dla samochodów osobowych, a druga dla samochodów ciężarowych. Po wyrenderowaniu zostanie wyświetlona ikona symbolu dla początku i końca trasy oraz różne kolorowe linie dla każdej ścieżki trasy.

1. Po zainicjowaniu mapy Dodaj następujący kod JavaScript w obsłudze zdarzeń `ready` Maps.

    ```JavaScript
    //Wait until the map resources have fully loaded.
    map.events.add('ready', function () {

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
    });
    ```
    
    W programie obsługi `ready` zdarzeń mapy jest tworzone źródło danych do przechowywania linii tras oraz punktów początkowych i końcowych. Tworzona jest warstwa linii, która jest następnie dołączana do źródła danych w celu zdefiniowania sposobu renderowana linii trasy. Za pomocą wyrażeń są pobierane szerokość i kolor linii z właściwości funkcji linii trasy. Podczas dodawania warstwy do mapy przekazywany jest drugi parametr o wartości `'labels'`. Określa on, że ta warstwa ma być renderowana poniżej etykiet mapy. Dzięki temu linia trasy nie obejmuje etykiet dróg. Tworzona jest warstwa symboli, która jest następnie dołączana do źródła danych. Ta warstwa określa, jak będą renderowane punkty początkowe i końcowe. W takim przypadku dodano wyrażenia, aby pobrać obraz ikony i informacje o etykiecie tekstowej z właściwości dla każdego obiektu punktu. 
    
2. W tym samouczku ustawimy punkt początkowy w lokalizacji fikcyjnej firmy Fabrikam (w Seattle), a punkt docelowy w lokalizacji biura firmy Microsoft. W programie obsługi `ready` zdarzeń programu Maps Dodaj następujący kod.

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
    ```

    Ten kod tworzy dwa [obiekty GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) reprezentujące punkt początkowy i punkt końcowy trasy. Do każdego punktu są dodawane właściwości `title` i `icon`.

3. Następnie dodaj poniższy kod JavaScript, aby dodać pinezki punktu początkowego i punktu końcowego do mapy:

    ```JavaScript
    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    //Fit the map window to the bounding box defined by the start and end positions.
    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 100
    });
    ```

    Punkt początkowy i końcowy zostały dodane do źródła danych. Pole ograniczenia dla punktu początkowego i końcowego jest obliczane przy użyciu funkcji `atlas.data.BoundingBox.fromData`. To ograniczenie służy do ustawiania widoku kamery mapy na całej trasie przy użyciu `map.setCamera` funkcji. W celu skompensowania wymiarów pikseli ikon symboli dodawane jest wypełnienie.

4. Zapisz plik i odśwież przeglądarkę, aby wyświetlić znaczniki na mapie. Teraz mapa jest wyśrodkowana w Seattle. Możesz zobaczyć zaokrąglony niebieski numer PIN oznaczający punkt początkowy oraz niebieski numer PIN oznaczający punkt końcowy.

   ![Wyświetlanie mapy z punktem początkowym i punktem końcowym](./media/tutorial-prioritized-routes/pins-map.png)

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Renderowanie tras z uwzględnieniem priorytetów na podstawie sposobu podróży

W tej sekcji pokazano, jak używać interfejsu API usługi Routes Maps. Interfejs API tras służy do znajdowania wielu tras z danego punktu początkowego do punktu końcowego w zależności od używanego trybu transportu. Usługa Route Service oferuje interfejsy API do planowania *najszybszych*, *najkrótszych*, *ekonomicznych*i *thrillingych* tras. Nie tylko należy zaplanować trasy interfejsów API między dwiema lokalizacjami, ale również uwzględnić bieżące warunki ruchu. 

Interfejs API tras umożliwia użytkownikom planowanie tras w przyszłości przy użyciu bogatej bazy danych o ruchu historycznym platformy Azure. Interfejs API może przewidywać czasy trwania tras dla danego dnia i godziny. Aby uzyskać więcej informacji, zobacz [GetRoute Directions (Uzyskiwanie wskazówek dojazdu)](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). 

**W odbiornika obciążenia mapy** należy dodać wszystkie poniższe bloki kodu, aby upewnić się, że są one ładowane po całkowitym załadowaniu mapy.

1. W funkcji GetMap Dodaj następujący kod do kodu JavaScript.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   `SubscriptionKeyCredential` Tworzy `SubscriptionKeyCredentialPolicy` do uwierzytelniania żądania HTTP do Azure Maps przy użyciu klucza subskrypcji. `atlas.service.MapsURL.newPipeline()` Przyjmuje `SubscriptionKeyCredential` zasady i tworzy wystąpienie [potoku](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) . `routeURL` Reprezentuje adres URL służący do Azure Maps operacji [routingu](https://docs.microsoft.com/rest/api/maps/route) .

2. Po skonfigurowaniu poświadczeń i adresu URL Dodaj następujący kod JavaScript, aby utworzyć trasę od początku do punktu końcowego dla wózka przewożącego USHazmatClass2d i wyświetlić wyniki.

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

    Ten fragment kodu przeprowadzi zapytania do usługi routingu Azure Maps za pomocą metody [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-maps-typescript-latest) . Linia trasy jest następnie wyodrębniana z kolekcji funkcji GEOJSON z odpowiedzi wyodrębnionej przy użyciu `geojson.getFeatures()` metody. Linia trasy jest następnie dodawana do źródła danych. Indeks 0 daje pewność, że jest renderowany przed jakimkolwiek innym wierszem w źródle danych. Jest to realizowane, ponieważ Obliczanie trasy ciężarówki będzie często wolniejsze niż Obliczanie trasy samochodu. Jeśli linia trasa ciężarówki zostanie dodana do źródła danych po tej trasie samochodu, zostanie ona wyrenderowana powyżej. Do linii trasy ciężarówki dodawane są dwie właściwości, kolor pociągnięcia, który jest całkiem odcień Niebieskia, a szerokość obrysu dziewięciu pikseli.

3. Dodaj następujący kod JavaScript, aby utworzyć trasę dla samochodu i wyświetlić wyniki.

    ```JavaScript
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {

        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#B76DAB';
        routeLine.properties.strokeWidth = 5;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.  
        datasource.add(routeLine);
    });
    ```

    Ten fragment kodu przeprowadzi zapytania do usługi routingu Azure Maps za pomocą metody [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-maps-typescript-latest) . Linia trasy jest następnie wyodrębniana z kolekcji funkcji GEOJSON z odpowiedzi wyodrębnionej przy użyciu `geojson.getFeatures()` metody. Linia trasy jest następnie dodawana do źródła danych. Do linii trasy samochodu dodawane są dwie właściwości, kolor pociągnięcia, który jest odcieniem purpurowy, a szerokość obrysu pięć pikseli.  

4. Zapisz plik **MapTruckRoute.html** i odśwież stronę w przeglądarce, aby wyświetlić wynik. W przypadku pomyślnego połączenia z interfejsami API usługi Maps powinna pojawić się mapa podobna do poniższej.

    ![Określanie priorytetów tras za pomocą usługi Azure Route Service](./media/tutorial-prioritized-routes/prioritized-routes.png)

    Trasa ciężarówki jest gruba niebieska, a trasa samochodu jest cienka purpurowa. Trasa samochodowa odbywa się w jeziorach Waszyngton za pośrednictwem I-90, który przechodzi przez tunele w obszarach mieszkalnych. Ze względu na to, że tunele znajdują się blisko obszarów mieszkalnych, odpadi z odpadów niebezpiecznych są ograniczone. Trasa ciężarówki, która określa typ ładunku USHazmatClass2, jest skierowana do korzystania z innego autostrady.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie nowej strony internetowej przy użyciu interfejsu API kontrolki mapy
> * Wizualizowanie przepływu ruchu na mapie
> * Tworzenie zapytań o trasę zawierających deklarację sposobu podróży
> * Wyświetlanie wielu tras na mapie

> [!div class="nextstepaction"]
> [Wyświetl pełny kod źródłowy](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html)

> [!div class="nextstepaction"]
> [Wyświetl przykład na żywo](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)

W następnym samouczku przedstawiony jest proces tworzenia prostego lokalizatora sklepów przy użyciu usługi Azure Maps.

> [!div class="nextstepaction"]
> [Create a store locator using Azure Maps (Tworzenie lokalizatora sklepów przy użyciu usługi Azure Maps)](./tutorial-create-store-locator.md)

> [!div class="nextstepaction"]
> [Korzystanie z wyrażeń stylu opartych na danych](data-driven-style-expressions-web-sdk.md)