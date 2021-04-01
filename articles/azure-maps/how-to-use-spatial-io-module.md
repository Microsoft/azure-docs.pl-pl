---
title: Jak używać Azure Maps przestrzennego modułu we/wy | Mapy Microsoft Azure
description: Dowiedz się, jak używać przestrzennego modułu we/wy dostarczonego przez zestaw SDK sieci Web Azure Maps. Ten moduł udostępnia niezawodne funkcje ułatwiające deweloperom integrację danych przestrzennych z zestawem SDK sieci Web Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/28/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: cd64c80acceb1542c080fc45efbce59f287d448a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97680691"
---
# <a name="how-to-use-the-azure-maps-spatial-io-module"></a>Jak używać przestrzennego modułu we/wy Azure Maps

Zestaw SDK sieci Web Azure Maps udostępnia **moduł operacji we/wy**, który integruje dane przestrzenne z zestawem SDK Azure Maps sieci Web przy użyciu języka JavaScript lub TypeScript. Niezawodne funkcje w tym module umożliwiają deweloperom:

- [Odczytuj i zapisuj pliki wspólnych danych przestrzennych](spatial-io-read-write-spatial-data.md). Obsługiwane formaty plików to: KML, KMZ, GPX, GeoRSS, GML, GEOJSON i pliki CSV zawierające kolumny z informacjami przestrzennymi. Obsługuje również Well-Known Text (WKT).
- [Łączenie się z usługami Open Geospatial Consortium (OGC) i integrację z programem Azure Maps Web SDK. Nałóż usługi mapy sieci Web (WMS) i usługi kafelków mapy sieci Web (WMTS) jako warstwy na mapie](spatial-io-add-ogc-map-layer.md).
- [Wykonywanie zapytań dotyczących danych w usłudze funkcji sieci Web (WFS)](spatial-io-connect-wfs-service.md).
- [Nałóż złożone zestawy danych, które zawierają informacje o stylu i są renderowane automatycznie przy użyciu minimalnej ilości kodu](spatial-io-add-simple-data-layer.md).
- Korzystanie z [wysokiej szybkości plików XML i rozdzielanych klas modułów odczytu i](spatial-io-core-operations.md)pamięci.

W tym przewodniku dowiesz się, jak zintegrować i używać przestrzennego modułu we/wy w aplikacji sieci Web.

Ten film wideo zawiera przegląd modułu operacji we/wy w Azure Maps Web SDK.

</br>

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Easily-integrate-spatial-data-into-the-Azure-Maps/player?format=ny]

> [!WARNING]
> Używaj tylko danych i usług, które pochodzą z zaufanego źródła, szczególnie w przypadku odwoływania się do niego z innej domeny. Moduł operacji we/wy w trybie przestrzennym podejmuje kroki w celu zminimalizowania ryzyka, ale najbezpieczniejsze podejście jest zbyt niedozwolone, aby danagerous dane do aplikacji. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było używać przestrzennego modułu we/wy, należy [utworzyć konto Azure Maps](./quick-demo-map-app.md#create-an-azure-maps-account) i [uzyskać podstawowy klucz subskrypcji dla Twojego konta](./quick-demo-map-app.md#get-the-primary-key-for-your-account).

## <a name="installing-the-spatial-io-module"></a>Instalowanie przestrzennego modułu we/wy

Moduł operacji we/wy można załadować Azure Maps przy użyciu jednej z dwóch opcji:

* Globalnie hostowana Azure CDN dla Azure Maps przestrzennego modułu we/wy. W przypadku tej opcji należy dodać odwołanie do kodu JavaScript w `<head>` elemencie pliku HTML.

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

* Kod źródłowy dla [usługi Azure-Maps-przestrzenny](https://www.npmjs.com/package/azure-maps-spatial-io) można załadować lokalnie, a następnie obsługiwać ją w aplikacji. Ten pakiet zawiera również definicje języka TypeScript. W przypadku tej opcji Użyj następującego polecenia, aby zainstalować pakiet:

    ```sh
    npm install azure-maps-spatial-io
    ```

    Następnie Dodaj odwołanie do kodu JavaScript w `<head>` elemencie dokumentu HTML:

    ```html
    <script src="node_modules/azure-maps-spatial-io/dist/atlas-spatial.min.js"></script>
    ```

## <a name="using-the-spatial-io-module"></a>Używanie przestrzennego modułu we/wy

1. Utwórz nowy plik HTML.

2. Załaduj zestaw SDK sieci Web Azure Maps i zainicjuj formant mapy. Szczegóły można znaleźć w przewodniku [kontroli mapy Azure Maps](./how-to-use-map-control.md) . Po wykonaniu tego kroku plik HTML powinien wyglądać następująco:

    ```html
    <!DOCTYPE html>
    <html>

    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.js"></script>

        <script type='text/javascript'>

            var map;

            function GetMap() {
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
                map.events.add('ready', function() {

                    // Write your code here to make sure it runs once the map resources are ready

                });
            }
        </script>
    </head>

    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>

    </html>
    ```

2. Załaduj moduł operacji wejścia/wyjścia w Azure Maps. W tym ćwiczeniu należy użyć usługi CDN dla Azure Maps przestrzennego modułu we/wy. Dodaj odwołanie poniżej do `<head>` elementu pliku HTML:

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

3. Zainicjuj `datasource` i Dodaj źródło danych do mapy. Zainicjuj `layer` i Dodaj źródło danych do warstwy mapy. Następnie Renderuj zarówno źródło danych, jak i warstwę. Przed przewinięciem w dół, aby zobaczyć pełny kod w następnym kroku, należy zastanowić się, jak najlepiej umieścić źródło danych i fragmenty kodu warstw. Należy przypomnieć, że przed programistycznym manipulowaniem mapą należy zaczekać, aż zasób mapy będzie gotowy.

    ```javascript
    var datasource, layer;
    ```

    oraz

    ```javascript
    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);
    
    //Add a simple data layer for rendering the data.
    layer = new atlas.layer.SimpleDataLayer(datasource);
    map.layers.add(layer);
    ```

4. W każdym miejscu kod HTML powinien wyglądać podobnie do poniższego kodu. Ten przykład pokazuje, jak odczytać plik XML z adresu URL. Następnie załaduj i Wyświetl dane funkcji pliku na mapie. 

    ```html
    <!DOCTYPE html>
    <html>

    <head>
        <title>Spatial IO Module Example</title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.js"></script>

        <!-- Add reference to the Azure Maps Spatial IO module. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

        <script type='text/javascript'>
            var map, datasource, layer;

            function GetMap() {
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
                map.events.add('ready', function() {

                    //Create a data source and add it to the map.
                    datasource = new atlas.source.DataSource();
                    map.sources.add(datasource);

                    //Add a simple data layer for rendering the data.
                    layer = new atlas.layer.SimpleDataLayer(datasource);
                    map.layers.add(layer);

                    //Read an XML file from a URL or pass in a raw XML string.
                    atlas.io.read('superCoolKmlFile.xml').then(r => {
                        if (r) {
                            //Add the feature data to the data source.
                            datasource.add(r);

                            //If bounding box information is known for data, set the map view to it.
                            if (r.bbox) {
                                map.setCamera({
                                    bounds: r.bbox,
                                    padding: 50
                                });
                            }
                        }
                    });
                });
            }
        </script>
    </head>

    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>

    </html>
    ```

5. Pamiętaj, aby zastąpić `<Your Azure Maps Key>` klucz podstawowy. Otwórz plik HTML i zobaczysz wyniki podobne do poniższej:

    <center>

    ![Przykład danych przestrzennych](./media/how-to-use-spatial-io-module/spatial-data-example.png)

    </center>

## <a name="next-steps"></a>Następne kroki

Ta funkcja jest dostępna w tym miejscu jest tylko jedną z wielu funkcji dostępnych w module operacji we/wy. Zapoznaj się z poniższymi wskazówkami, aby dowiedzieć się, jak używać innych funkcji w module operacji we/wy przestrzennym:

> [!div class="nextstepaction"]
> [Dodawanie prostej warstwy danych](spatial-io-add-simple-data-layer.md)

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

Zapoznaj się z dokumentacją dotyczącą operacji we/wy Azure Maps:

> [!div class="nextstepaction"]
> [Azure Maps pakiet operacji we/wy](/javascript/api/azure-maps-spatial-io/)