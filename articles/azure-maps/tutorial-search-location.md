---
title: 'Samouczek: wyszukiwanie lokalizacji w pobliżu mapy | Mapy Microsoft Azure'
description: Samouczek dotyczący wyszukiwania punktów orientacyjnych na mapie. Zobacz, jak używać zestawu SDK sieci Web Azure Maps, aby dodawać możliwości wyszukiwania i interaktywne okna podręczne do mapy.
author: anastasia-ms
ms.author: v-stharr
ms.date: 1/15/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 4d6728d4fbde4b7d6cc8ed06e961642264ad31bc
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91321700"
---
# <a name="tutorial-search-nearby-points-of-interest-using-azure-maps"></a>Samouczek: Wyszukiwanie punktów orientacyjnych w pobliżu za pomocą Azure Maps

W tym samouczku przedstawiono konfigurowanie konta przy użyciu usługi Azure Maps, a następnie wyszukiwanie punktu orientacyjnego za pomocą interfejsów API usługi Maps. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie konta usługi Azure Maps
> * Pobieranie klucza podstawowego dla konta usługi Maps
> * Tworzenie nowej strony internetowej przy użyciu interfejsu API kontrolki mapy
> * Wyszukiwanie pobliskiego punktu orientacyjnego przy użyciu usługi wyszukiwania Maps

## <a name="prerequisites"></a>Wymagania wstępne

<a id="createaccount"></a>
<a id="getkey"></a>

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
2. [Utwórz konto Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Uzyskaj podstawowy klucz subskrypcji](quick-demo-map-app.md#get-the-primary-key-for-your-account), nazywany także kluczem podstawowym lub kluczem subskrypcji. Aby uzyskać więcej informacji na temat uwierzytelniania w Azure Maps, zobacz [Zarządzanie uwierzytelnianiem w programie Azure Maps](how-to-manage-authentication.md).

<a id="createmap"></a>

## <a name="create-a-new-map"></a>Tworzenie nowej mapy

Interfejs API kontrolka mapy jest wygodną biblioteką klienta. Ten interfejs API pozwala łatwo zintegrować mapy z aplikacją sieci Web. Powoduje to ukrycie złożoności wywołań usługi REST i zwiększa produktywność dzięki dostosowywanym składnikom. Poniższe kroki pokazują, jak utworzyć statyczną stronę HTML osadzoną przy użyciu interfejsu API kontrolki mapy.

1. Na maszynie lokalnej utwórz nowy plik i nadaj mu nazwę **MapSearch.html**.
2. Dodaj następujące składniki HTML do pliku:

   ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title>Map Search</title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>

        <script>
        function GetMap(){
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

   Zwróć uwagę, że nagłówek HTML zawiera pliki zasobów CSS i JavaScript obsługiwane przez bibliotekę kontrolek mapy platformy Azure. Zwróć uwagę na zdarzenie `onload` w treści strony, które spowoduje wywołanie funkcji `GetMap` po załadowaniu treści strony. `GetMap`Funkcja będzie zawierać wbudowany kod JavaScript, aby uzyskać dostęp do interfejsów api Azure Maps.

3. Dodaj następujący kod JavaScript do funkcji `GetMap` w pliku HTML. Zastąp ciąg `<Your Azure Maps Key>` kluczem podstawowym, który został skopiowany z konta Maps.

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

   Ten segment inicjuje interfejs API kontrolki mapy dla klucza konta usługi Azure Maps. `atlas` jest przestrzenią nazw, która zawiera interfejs API i powiązane składniki wizualne. `atlas.Map` zapewnia formant dla wizualizacji i interaktywnej mapy sieci Web.

4. Zapisz zmiany w pliku i otwórz stronę HTML w przeglądarce. Pokazana mapa to najbardziej podstawowa mapa, którą można wykonać, wywołując `atlas.Map` przy użyciu klucza konta.

   ![Wyświetlanie mapy](./media/tutorial-search-location/basic-map.png)

5. W funkcji `GetMap` po zainicjowaniu mapy dodaj następujący kod JavaScript.

    ```JavaScript
    //Wait until the map resources are ready.
    map.events.add('ready', function() {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering point data.
        var resultLayer = new atlas.layer.SymbolLayer(datasource, null, {
            iconOptions: {
                image: 'pin-round-darkblue',
                anchor: 'center',
                allowOverlap: true
            },
            textOptions: {
                anchor: "top"
            }
        });

        map.layers.add(resultLayer);
    });
    ```

   W tym segmencie kodu `ready` do mapy zostanie dodane zdarzenie, które zostanie wywołane po załadowaniu zasobów mapy, a mapa jest gotowa do uzyskania dostępu. W programie `ready` obsługi zdarzeń mapy tworzone jest źródło danych do przechowywania danych wynikowych. Tworzona jest warstwa symboli, która jest następnie dołączana do źródła danych. Ta warstwa określa, w jaki sposób mają być renderowane dane wynikowe w źródle danych. W takim przypadku wynik jest renderowany przy użyciu ciemnej niebieskiej ikony pinezki, wyśrodkowany przez współrzędne wyniki i zezwala na nakładanie się innych ikon. Warstwa wynikowa zostanie dodana do warstw mapy.

<a id="usesearch"></a>

## <a name="add-search-capabilities"></a>Dodawanie funkcji wyszukiwania

W tej sekcji pokazano, jak używać [interfejsu API wyszukiwania](https://docs.microsoft.com/rest/api/maps/search) Maps w celu znalezienia punktu orientacyjnego na mapie. Jest to interfejs API RESTful umożliwiający deweloperom wyszukiwanie adresów, punktów orientacyjnych i innych informacji geograficznych. Usługa Search Service przypisuje informacje o długości i szerokości geograficznej do określonego adresu. Opisany poniżej **moduł usługi** może być używany do wyszukiwania lokalizacji za pomocą interfejsu API usługi Search w usłudze Maps.

### <a name="service-module"></a>Moduł usługi

1. W programie `ready` obsługi zdarzeń mapy Utwórz adres URL usługi wyszukiwania, dodając następujący kod JavaScript.

    ```JavaScript
   // Use SubscriptionKeyCredential with a subscription key
   var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

   // Use subscriptionKeyCredential to create a pipeline
   var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

   // Construct the SearchURL object
   var searchURL = new atlas.service.SearchURL(pipeline); 
   ```

   `SubscriptionKeyCredential`Tworzy `SubscriptionKeyCredentialPolicy` do uwierzytelniania żądania HTTP do Azure Maps przy użyciu klucza subskrypcji. `atlas.service.MapsURL.newPipeline()`Przyjmuje `SubscriptionKeyCredential` zasady i tworzy wystąpienie [potoku](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline) . `searchURL`Reprezentuje adres URL służący do Azure Maps operacji [wyszukiwania](https://docs.microsoft.com/rest/api/maps/search) .

2. Następnie dodaj poniższy blok skryptu w celu utworzenia zapytania wyszukiwania. Ten fragment kodu używa usługi Fuzzy Search Service — podstawowego interfejsu API wyszukiwania usługi Search Service. Usługa Fuzzy Search Service obsługuje większość rozmytych danych wejściowych, takich jak adresy, miejsca i punkty orientacyjne (POI). Ten kod szuka w pobliżu stacji benzyny w określonym promieniu o podanej wartości szerokości i długości geograficznej. Kolekcje funkcji GEOJSON z odpowiedzi są następnie wyodrębniane przy użyciu `geojson.getFeatures()` metody i dodawane do źródła danych, co powoduje automatyczne wyniki renderowania danych na mapie za pośrednictwem warstwy symboli. Ostatnia część skrypt ustawia widok kamery mapy za pomocą pola ograniczenia wyników z wykorzystaniem właściwości mapy [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-).

    ```JavaScript
    var query =  'gasoline-station';
    var radius = 9000;
    var lat = 47.64452336193245;
    var lon = -122.13687658309935;

    searchURL.searchPOI(atlas.service.Aborter.timeout(10000), query, {
        limit: 10,
        lat: lat,
        lon: lon,
        radius: radius
    }).then((results) => {

        // Extract GeoJSON feature collection from the response and add it to the datasource
        var data = results.geojson.getFeatures();
        datasource.add(data);

        // set camera to bounds to show the results
        map.setCamera({
            bounds: data.bbox,
            zoom: 10
        });
    });
    ```

3. Zapisz plik **MapSearch.html** i odśwież przeglądarkę. Powinna zostać wyświetlona mapa wyśrodkowana w Seattle z okrągłymi numerami PIN dla lokalizacji stacji benzyny w danym regionie.

   ![Wyświetlanie mapy z wynikami wyszukiwania](./media/tutorial-search-location/pins-map.png)

4. Wprowadzenie następującego żądania HTTP w przeglądarce spowoduje wyświetlenie danych pierwotnych renderowanych przez mapę. Zamień \<Your Azure Maps Key\> na klucz podstawowy.

   ```http
   https://atlas.microsoft.com/search/poi/json?api-version=1.0&query=gasoline%20station&subscription-key=<subscription-key>&lat=47.6292&lon=-122.2337&radius=100000
   ```

Na stronie MapSearch można teraz wyświetlić lokalizacje punktów orientacyjnych, zwracanych przez zapytanie wyszukiwania rozmytego. Do lokalizacji dodamy informacje oraz interaktywne funkcje.

## <a name="add-interactive-data"></a>Dodawanie interaktywnych danych

W wynikach wyszukiwania na utworzonej mapie są używane tylko dane o długości i szerokości geograficznej. Jednak nieprzetworzony kod JSON, który zwraca usługa Maps Search, zawiera dodatkowe informacje o każdej stacji gazowej. Dołączenie nazwy i ulica adresu. Dane te można zintegrować z mapą za pomocą interaktywnych wyskakujących okienek.

1. Dodaj następujące wiersze kodu w programie `ready` obsługi zdarzeń mapy po kodzie, aby wykonać zapytanie do usługi wyszukiwania rozmytego. Ten kod spowoduje utworzenie wystąpienia okna podręcznego i dodanie zdarzenia mouseOver do warstwy symboli.

    ```JavaScript
   //Create a popup but leave it closed so we can update it and display it later.
    popup = new atlas.Popup();

    //Add a mouse over event to the result layer and display a popup when this event fires.
    map.events.add('mouseover', resultLayer, showPopup);
    ```

    Interfejs API `*atlas.Popup` udostępnia okno informacji zakotwiczone w wymaganym miejscu na mapie. 

2. Dodaj następujący kod wewnątrz `GetMap` funkcji, aby wyświetlić wskaźnik myszy nad informacjami o wynikach w menu podręcznym.

    ```JavaScript
    function showPopup(e) {
        //Get the properties and coordinates of the first shape that the event occurred on.

        var p = e.shapes[0].getProperties();
        var position = e.shapes[0].getCoordinates();

        //Create HTML from properties of the selected result.
        var html = `
          <div style="padding:5px">
            <div><b>${p.poi.name}</b></div>
            <div>${p.address.freeformAddress}</div>
            <div>${position[1]}, ${position[0]}</div>
          </div>`;

        //Update the content and position of the popup.
        popup.setPopupOptions({
            content: html,
            position: position
        });

        //Open the popup.
        popup.open(map);
    }
    ```

3. Zapisz plik i odśwież przeglądarkę. Teraz mapa w przeglądarce wyświetla wyskakujące okienka informacyjne po najechaniu kursorem na dowolny znacznik wyszukiwania.

    ![Kontrolka mapy platformy Azure i usługa Search Service](./media/tutorial-search-location/popup-map.png)

Aby wyświetlić pełny kod dla tego samouczka, kliknij [tutaj](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/search.html). Aby wyświetlić przykład na żywo, kliknij [tutaj](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20for%20points%20of%20interest) .

## <a name="next-steps"></a>Następne kroki

Następny samouczek przedstawia sposób wyświetlenia trasy między dwiema lokalizacjami.

> [!div class="nextstepaction"]
> [Trasa do miejsca docelowego](./tutorial-route-location.md)
