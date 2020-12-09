---
title: 'Samouczek: Tworzenie aplikacji lokalizatora sklepu przy użyciu Azure Maps | Mapy Microsoft Azure'
description: Samouczek dotyczący tworzenia aplikacji sieci Web lokalizatora magazynów. Użyj zestawu Web SDK Azure Maps, aby utworzyć stronę sieci Web, wysłać zapytanie do usługi wyszukiwania i wyświetlić wyniki na mapie.
author: anastasia-ms
ms.author: v-stharr
ms.date: 08/11/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 398e964ad773e4c015129c6dd3d4784f1300e16b
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905778"
---
# <a name="tutorial-create-a-store-locator-by-using-azure-maps"></a>Samouczek: Tworzenie lokalizatora sklepu za pomocą Azure Maps

Ten samouczek przeprowadzi Cię przez proces tworzenia prostego lokalizatora sklepów przy użyciu usługi Azure Maps. Lokalizatory sklepów są powszechne. Wiele koncepcji używanych w tego typu aplikacjach można stosować w wielu innych typach aplikacji. Oferowanie lokalizatora sklepów jest koniecznością dla większości firm prowadzących sprzedaż detaliczną bezpośrednio dla klientów. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie nowej strony internetowej przy użyciu interfejsu API kontrolki mapy platformy Azure.
> * Ładowanie danych niestandardowych z pliku i wyświetlanie ich na mapie.
> * Używanie usługi wyszukiwania usługi Azure Maps w celu znalezienia adresu lub wprowadzenia zapytania.
> * Uzyskiwanie lokalizacji użytkownika z przeglądarki i wyświetlanie jej na mapie.
> * Łączenie wielu warstw w celu utworzenia symboli niestandardowych na mapie.  
> * Punkty danych klastra.  
> * Dodawanie kontrolek powiększenia do mapy.

<a id="Intro"></a>

Przejdź dalej do [przykładu lokalizatora sklepów na żywo](https://azuremapscodesamples.azurewebsites.net/?sample=Simple%20Store%20Locator) lub [kodu źródłowego](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator).

## <a name="prerequisites"></a>Wymagania wstępne

1. [Utwórz konto Azure Maps za pomocą warstwy cenowej S1](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Uzyskaj podstawowy klucz subskrypcji](quick-demo-map-app.md#get-the-primary-key-for-your-account), nazywany także kluczem podstawowym lub kluczem subskrypcji.

Aby uzyskać więcej informacji na temat uwierzytelniania w Azure Maps, zobacz [Zarządzanie uwierzytelnianiem w programie Azure Maps](how-to-manage-authentication.md).

## <a name="design"></a>Projekt

Zanim przejdziesz do kodu, dobrze jest zacząć od projektu. Lokalizator sklepów może być prosty lub złożony — taki, jaki chcesz. W tym samouczku utworzymy prosty lokalizator sklepów. W trakcie tego procesu podamy kilka wskazówek pomocnych w rozszerzeniu niektórych funkcji, jeśli zechcesz to zrobić. Utworzymy lokalizator sklepów dla fikcyjnej firmy o nazwie Contoso Coffee. Na poniższej ilustracji przedstawiono szkielet ogólnego układu lokalizatora sklepów, który utworzymy w tym samouczku:

![Szkielet aplikacji lokalizatora sklepu dla lokalizacji warsztatów firmy Contoso](./media/tutorial-create-store-locator/SimpleStoreLocatorWireframe.png)

Aby zmaksymalizować przydatność tego lokalizatora sklepów, dołączymy układ dynamiczny, który dostosowuje się, jeśli szerokość ekranu użytkownika jest mniejsza niż 700 pikseli. Układ dynamiczny ułatwia używanie lokalizatora sklepów na małym ekranie, na przykład na urządzeniu przenośnym. Poniżej przedstawiono szkielet układu dla małego ekranu:  

![Szkielet aplikacji lokalizatora magazynu kawy firmy Contoso na urządzeniu przenośnym](./media/tutorial-create-store-locator/SimpleStoreLocatorMobileWireframe.png)</

Powyższe szkielety pokazują dość prostą aplikację. Aplikacja zawiera pole wyszukiwania, listę magazynów w pobliżu oraz mapę, która ma pewne znaczniki, takie jak symbole. I ma okno podręczne, które wyświetla dodatkowe informacje, gdy użytkownik wybierze znacznik. Poniżej znajduje się bardziej szczegółowa lista funkcji wbudowanych w lokalizatorze sklepów w tym samouczku:

* Wszystkie lokalizacje z zaimportowanego pliku danych rozdzielanych tabulatorami są ładowane na mapie.
* Użytkownik może przesuwać i powiększać mapę, wyszukiwać i wybierać przycisk GPS Moja lokalizacja.
* Układ strony jest dostosowywany w zależności od szerokości ekranu urządzenia.  
* Nagłówek pokazuje logo sklepu.  
* Użytkownik może użyć pola wyszukiwania i przycisku wyszukiwania, aby wyszukać lokalizację taką jak adres, kod pocztowy lub miasto. 
* Zdarzenie `keypress` dodane do pola wyszukiwania wyzwala wyszukiwanie, jeśli użytkownik naciśnie klawisz Enter. Ta funkcja często jest pomijana, ale dzięki niej wrażenia użytkownika są lepsze.
* Kiedy mapa jest przesuwana, obliczana jest odległość do każdej lokalizacji od środka mapy. Lista wyników jest aktualizowana, aby pokazywać najbliższe lokalizacje w górnej części mapy.  
* Po wybraniu wyniku na liście wyników mapa jest środkowana na wybranej lokalizacji, a informacje o tej lokalizacji są wyświetlane w oknie podręcznym.  
* Wybranie konkretnej lokalizacji na mapie także wywołuje okno podręczne.
* Gdy użytkownik pomniejsza mapę, lokalizacje są grupowane w klastrach. Klastry są reprezentowane przez kółka z liczbą w środku. Klastry tworzą się i rozdzielają, gdy użytkownik zmienia poziom powiększenia.
* Wybranie klastra powoduje powiększenie mapy o dwa poziomy i wyśrodkowanie jej na lokalizacji danego klastra.

<a id="create a data-set"></a>

## <a name="create-the-store-location-dataset"></a>Tworzenie zestawu danych lokalizacji sklepów

Zanim opracujemy aplikację lokalizatora sklepów, musimy utworzyć zestaw danych sklepów, które chcemy wyświetlać na mapie. W tym samouczku używamy zestawu danych dla fikcyjnej kawiarni o nazwie Contoso Coffee. Zestaw danych dla tego prostego lokalizatora sklepów jest zarządzany w skoroszycie programu Excel. Zestaw danych zawiera 10 213 lokalizacji warsztatów kawowych firmy Contoso rozmieszczonych w dziewięciu krajach/regionach: Stany Zjednoczone, Kanada, Zjednoczone Królestwo, Francja, Niemcy, Włochy, Niderlandy, dania i Hiszpania. Oto zrzut ekranu przedstawiający te dane:

![Zrzut ekranu przedstawiający dane lokalizatora sklepów w skoroszycie programu Excel](./media/tutorial-create-store-locator/StoreLocatorDataSpreadsheet.png)

Możesz [pobrać ten skoroszyt programu Excel](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data).

Patrząc na zrzut ekranu danych, możemy zauważyć następujące rzeczy:

* Informacje o lokalizacji są przechowywane przy użyciu kolumn **AddressLine** (Adres), **City** (Miasto), **Municipality** (Gmina), **AdminDivision** (Województwo), **PostCode** (Kod pocztowy) i **Country** (Kraj).  
* Kolumny **Latitude** (Szerokość geograficzna) i **Longitude** (Długość geograficzna) zawierają współrzędne dla każdej lokalizacji kawiarni Contoso Coffee. Jeśli nie masz informacji o współrzędnych lokalizacji, możesz je ustalić przy użyciu usług wyszukiwania w usłudze Azure Maps.
* Niektóre dodatkowe kolumny zawierają metadane związane z kawiarniami: numer telefonu, kolumny logiczne oraz czas otwierania i zamykania w formacie 24-godzinnym. Kolumny logiczne są przeznaczone dla Wi-Fi i dostępu do wózka. Możesz tworzyć własne kolumny zawierające metadane, które są bardziej odpowiednie dla danych lokalizacji.

> [!NOTE]
> Azure Maps renderuje dane w Merkatora projekcji sferycznej "EPSG: 3857", ale odczytuje dane w "EPSG: 4326", które używają elementu WGS84.

Istnieje wiele sposobów uwidocznienia zestawu danych dla aplikacji. Jednym z metod jest załadowanie danych do bazy danych i uwidocznienie usługi sieci Web, która wysyła zapytania do danych. Następnie możesz wysłać wyniki do przeglądarki użytkownika. Ta opcja jest idealnym rozwiązaniem w przypadku dużych lub często aktualizowanych zestawów danych. Jednak ta opcja wymaga większej liczby prac programistycznych i ma wyższy koszt.

Innym rozwiązaniem jest przekonwertowanie tego zestawu danych na prosty plik tekstowy, który przeglądarka może łatwo analizować. Plik może być hostowany razem z aplikacją. Ta opcja jest uproszczona, ale sprawdza się tylko w przypadku mniejszych zestawów danych, ponieważ użytkownik pobiera wszystkie dane. Dla tego zestawu danych używamy prostego pliku tekstowego, ponieważ rozmiar pliku jest mniejszy niż 1 MB.  

Aby przekonwertować skoroszyt na prosty plik tekstowy, zapisz skoroszyt jako plik rozdzielany tabulatorami. Każda kolumna jest oddzielona znakiem tabulacji, co sprawia, że kolumny są łatwe do analizowania w naszym kodzie. Można by było użyć formatu wartości rozdzielanych przecinkami (CSV), ale ta opcja wymaga więcej logiki analizowania. Każde pole, które miałoby wokół siebie przecinek, zostałoby ujęte w cudzysłów. Aby w programie Excel wyeksportować te dane w postaci pliku rozdzielanego tabulatorami, wybierz pozycję **Zapisz jako**. Na liście rozwijanej **Zapisz jako typ** wybierz pozycję **Tekst (rozdzielany znakami tabulacji)(*.txt)**. Nadaj plikowi nazwę *ContosoCoffee.txt*.

![Zrzut ekranu przedstawiający okno dialogowe Zapisz jako typ](./media/tutorial-create-store-locator/SaveStoreDataAsTab.png)

Jeśli otworzysz plik tekstowy w Notatniku, będzie on wyglądał podobnie, jak na poniższej ilustracji:

![Zrzut ekranu przedstawiający plik Notatnika, pokazujący zestaw danych rozdzielany tabulatorami](./media/tutorial-create-store-locator/StoreDataTabFile.png)

## <a name="set-up-the-project"></a>Konfigurowanie projektu

Aby utworzyć projekt, można użyć programu [Visual Studio](https://visualstudio.microsoft.com) lub innego wybranego edytora kodu. W folderze projektu utwórz trzy pliki: *index.html*, *index.css* i *index.js*. Te pliki definiują układ, styl i logikę aplikacji. Utwórz folder o nazwie *data* (dane) i dodaj do niego plik *ContosoCoffee.txt*. Utwórz inny folder o nazwie *images* (obrazy). W tej aplikacji używamy 10 obrazów dla ikon, przycisków i znaczników na mapie. Możesz [pobrać te obrazy](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data). Folder projektu powinien teraz wyglądać podobnie jak na poniższej ilustracji:

![Zrzut ekranu przedstawiający folder projektu prostego lokalizatora sklepów](./media/tutorial-create-store-locator/StoreLocatorVSProject.png)

## <a name="create-the-user-interface"></a>Tworzenie interfejsu użytkownika

Aby utworzyć interfejs użytkownika, dodaj kod do pliku *index.html*:

1. Dodaj następujące tagi `meta` do elementu `head` pliku *index.html*. `charset`Tag definiuje zestaw znaków (UTF-8). Wartość `http-equiv` instruuje przeglądarkę Internet Explorer i Microsoft Edge, aby używać najnowszych wersji przeglądarki. I ostatni `meta` tag określa okienko ekranu, które dobrze sprawdza się w przypadku odpowiadających układów.

    ```HTML
    <meta charset="utf-8">
    <meta http-equiv="x-ua-compatible" content="IE=Edge">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    ```

1. Dodaj odwołania do plików JavaScript i CSS kontrolki internetowej usługi Azure Maps:

    ```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
    ```

1. Dodaj odwołanie do modułu usług Azure Maps Services. Moduł ten to biblioteka języka JavaScript, która opakowuje usługi REST Azure Maps i umożliwia ich łatwe użycie w kodzie JavaScript. Moduł jest przydatny do obsługi funkcji wyszukiwania.

    ```HTML
    <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>
    ```

1. Dodaj odwołania do plików *index.js* i *index.css*:

    ```HTML
    <link rel="stylesheet" href="index.css" type="text/css">
    <script src="index.js"></script>
    ```

1. W treści dokumentu dodaj tag `header`. Wewnątrz tagu `header` dodaj nazwę i logo firmy.

    ```HTML
    <header>
        <img src="images/Logo.png" />
        <span>Contoso Coffee</span>
    </header>
    ```

1. Dodaj tag `main` i utwórz panel wyszukiwania, który zawiera pole tekstowe i przycisk wyszukiwania. Dodaj także odwołania `div` dla mapy, panelu listy i przycisku GPS Moja lokalizacja.

    ```HTML
    <main>
        <div class="searchPanel">
            <div>
                <input id="searchTbx" type="search" placeholder="Find a store" />
                <button id="searchBtn" title="Search"></button>
            </div>
        </div>
        <div id="listPanel"></div>
        <div id="myMap"></div>
        <button id="myLocationBtn" title="My Location"></button>
    </main>
    ```

Gdy skończysz, plik *index.html* powinien wyglądać, jak [ten przykładowy plik index.html](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/index.html).

Następnym krokiem jest zdefiniowanie stylów CSS. Style CSS definiują układ składników aplikacji i wygląd aplikacji. Otwórz plik *index.css* i dodaj do niego następujący kod. Styl `@media` definiuje alternatywne opcje stylu do użycia, gdy szerokość ekranu jest mniejsza niż 700 pikseli.  

   ```CSS
    html, body {
        padding: 0;
        margin: 0;
        font-family: Gotham, Helvetica, sans-serif;
        overflow-x: hidden;
    }

    header {
        width: calc(100vw - 10px);
        height: 30px;
        padding: 15px 0 20px 20px;
        font-size: 25px;
        font-style: italic;
        font-family: "Comic Sans MS", cursive, sans-serif;
        line-height: 30px;
        font-weight: bold;
        color: white;
        background-color: #007faa;
    }

    header span {
        vertical-align: middle;
    }

    header img {
        height: 30px;
        vertical-align: middle;
    }

    .searchPanel {
        position: relative;
        width: 350px;
    }

    .searchPanel div {
        padding: 20px;
    }

    .searchPanel input {
        width: calc(100% - 50px);
        font-size: 16px;
        border: 0;
        border-bottom: 1px solid #ccc;
    }

    #listPanel {
        position: absolute;
        top: 135px;
        left: 0px;
        width: 350px;
        height: calc(100vh - 135px);
        overflow-y: auto;
    }

    #myMap { 
        position: absolute;
        top: 65px;
        left: 350px;
        width: calc(100vw - 350px);
        height: calc(100vh - 65px);
    }

    .statusMessage {
        margin: 10px;
    }

    #myLocationBtn, #searchBtn {
        margin: 0;
        padding: 0;
        border: none;
        border-collapse: collapse;
        width: 32px;
        height: 32px; 
        text-align: center;
        cursor: pointer;
        line-height: 32px;
        background-repeat: no-repeat;
        background-size: 20px;
        background-position: center center;
        z-index: 200;
    }

    #myLocationBtn {
        position: absolute;
        top: 150px;
        right: 10px;
        box-shadow: 0px 0px 4px rgba(0,0,0,0.16);
        background-color: white;
        background-image: url("images/GpsIcon.png");
    }

    #myLocationBtn:hover {
        background-image: url("images/GpsIcon-hover.png");
    }

    #searchBtn {
        background-color: transparent;
        background-image: url("images/SearchIcon.png");
    }

    #searchBtn:hover {
        background-image: url("images/SearchIcon-hover.png");
    }

    .listItem {
        height: 50px;
        padding: 20px;
        font-size: 14px;
    }

    .listItem:hover {
        cursor: pointer;
        background-color: #f1f1f1;
    }

    .listItem-title {
        color: #007faa;
        font-weight: bold;
    }

    .storePopup {
        min-width: 150px;
    }

    .storePopup .popupTitle {
        border-top-left-radius: 4px;
        border-top-right-radius: 4px;
        padding: 8px;
        height: 30px;
        background-color: #007faa;
        color: white;
        font-weight: bold;
    }

    .storePopup .popupSubTitle {
        font-size: 10px;
        line-height: 12px;
    }

    .storePopup .popupContent {
        font-size: 11px;
        line-height: 18px;
        padding: 8px;
    }

    .storePopup img {
        vertical-align:middle;
        height: 12px;
        margin-right: 5px;
    }

    /* Adjust the layout of the page when the screen width is less than 700 pixels. */
    @media screen and (max-width: 700px) {
        .searchPanel {
            width: 100vw;
        }

        #listPanel {
            top: 385px;
            width: 100%;
            height: calc(100vh - 385px);
        }

        #myMap {
            width: 100vw;
            height: 250px;
            top: 135px;
            left: 0px;
        }

        #myLocationBtn {
            top: 220px;
        }
    }

    .mapCenterIcon {
        display: block;
        width: 10px;
        height: 10px;
        border-radius: 50%;
        background: orange;
        border: 2px solid white;
        cursor: pointer;
        box-shadow: 0 0 0 rgba(0, 204, 255, 0.4);
        animation: pulse 3s infinite;
    }

    @keyframes pulse {
        0% {
            box-shadow: 0 0 0 0 rgba(0, 204, 255, 0.4);
        }

        70% {
            box-shadow: 0 0 0 50px rgba(0, 204, 255, 0);
        }

        100% {
            box-shadow: 0 0 0 0 rgba(0, 204, 255, 0);
        }
    }
   ```

Uruchom aplikację teraz, zobaczysz nagłówek, pole wyszukiwania i przycisk wyszukiwania. Jednak mapa nie jest widoczna, ponieważ nie została jeszcze załadowana. Jeśli spróbujesz wykonać wyszukiwanie, nic się nie stanie. Musimy skonfigurować logikę JavaScript, która została opisana w następnej sekcji. Ta logika uzyskuje dostęp do wszystkich funkcji lokalizatora magazynu.

## <a name="wire-the-application-with-javascript"></a>Powiązanie aplikacji za pomocą języka JavaScript

Wszystko jest teraz skonfigurowane w interfejsie użytkownika. Nadal musimy dodać kod JavaScript w celu załadowania i przeanalizowania danych, a następnie renderowania danych na mapie. Aby rozpocząć, otwórz plik *index.js* i dodaj do niego kod zgodnie z opisem w poniższych krokach.

1. Dodaj opcje globalne, aby ułatwić aktualizowanie ustawień. Zdefiniuj zmienne dla mapy, okna wyskakującego, źródła danych, warstwy ikon i znacznika HTML. Ustaw znacznik HTML w taki sposób, aby wskazywał środek obszaru wyszukiwania. I zdefiniuj wystąpienie klienta usługi wyszukiwania Azure Maps.

    ```JavaScript
    //The maximum zoom level to cluster data point data on the map.
    var maxClusterZoomLevel = 11;

    //The URL to the store location data.
    var storeLocationDataUrl = 'data/ContosoCoffee.txt';

    //The URL to the icon image.
    var iconImageUrl = 'images/CoffeeIcon.png';
    var map, popup, datasource, iconLayer, centerMarker, searchURL;
    ```

1. Dodaj kod do pliku *index.js*. Poniższy kod inicjuje mapę. Dodaliśmy [odbiornik zdarzeń](/javascript/api/azure-maps-control/atlas.map#events) czekający na zakończenie ładowania strony. Następnie tworzymy zdarzenia w celu monitorowania ładowania mapy, a następnie przydzielenia funkcji dla przycisku wyszukiwania i przycisku Moje lokalizacje.

   Gdy użytkownik wybierze przycisk Wyszukaj lub wpisze lokalizację w polu wyszukiwania, a następnie naciśnie klawisz ENTER, inicjowane jest Wyszukiwanie rozmyte względem zapytania użytkownika. Przekaż tablicę wartości ISO 2 kraju/regionu do `countrySet` opcji, aby ograniczyć wyniki wyszukiwania do tych krajów/regionów. Ograniczenie krajów/regionów do wyszukiwania pomaga zwiększyć dokładność zwracanych wyników. 
  
   Po zakończeniu wyszukiwania wykonaj pierwszy wynik i ustaw dla niego kamerę mapy. Gdy użytkownik wybierze przycisk Moja lokalizacja, pobierze lokalizację użytkownika przy użyciu interfejsu API geolokalizacji HTML5. Ten interfejs API jest wbudowany w przeglądarkę. Następnie Wyśrodkuj mapę na swojej lokalizacji.  

   > [!Tip]
   > W przypadku wyskakujących okienek najlepiej utworzyć jedno wystąpienie `Popup` i używać go ponownie, aktualizując jego zawartość i położenie. Dla każdego wystąpienia `Popup` dodawanego do kodu do strony dodawanych jest wiele elementów DOM. Im więcej elementów DOM na stronie, tym więcej obiektów musi śledzić przeglądarka. W przypadku zbyt wielu elementów przeglądarka może zacząć wolno działać.

    ```JavaScript
    function initialize() {
        //Initialize a map instance.
        map = new atlas.Map('myMap', {
            center: [-90, 40],
            zoom: 2,

            //Add your Azure Maps primary subscription key to the map SDK.
            authOptions: {
                authType: 'subscriptionKey',
                subscriptionKey: '<Your Azure Maps Key>'
            }
        });

        //Create a pop-up window, but leave it closed so we can update it and display it later.
        popup = new atlas.Popup();

        //Use SubscriptionKeyCredential with a subscription key
        const subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

        //Use subscriptionKeyCredential to create a pipeline
        const pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
            retryOptions: { maxTries: 4 } // Retry options
        });

        //Create an instance of the SearchURL client.
        searchURL = new atlas.service.SearchURL(pipeline);

        //If the user selects the search button, geocode the value the user passed in.
        document.getElementById('searchBtn').onclick = performSearch;

        //If the user presses Enter in the search box, perform a search.
        document.getElementById('searchTbx').onkeyup = function(e) {
            if (e.keyCode === 13) {
                performSearch();
            }
        };

        //If the user selects the My Location button, use the Geolocation API (Preview) to get the user's location. Center and zoom the map on that location.
        document.getElementById('myLocationBtn').onclick = setMapToUserLocation;

        //Wait until the map resources are ready.
        map.events.add('ready', function() {

            //Add your post-map load functionality.

        });
    }

    //Create an array of country/region ISO 2 values to limit searches to. 
    var countrySet = ['US', 'CA', 'GB', 'FR','DE','IT','ES','NL','DK'];

    function performSearch() {
        var query = document.getElementById('searchTbx').value;

        //Perform a fuzzy search on the users query.
        searchURL.searchFuzzy(atlas.service.Aborter.timeout(3000), query, {
            //Pass in the array of country/region ISO2 for which we want to limit the search to.
            countrySet: countrySet
        }).then(results => {
            //Parse the response into GeoJSON so that the map can understand.
            var data = results.geojson.getFeatures();

            if (data.features.length > 0) {
                //Set the camera to the bounds of the results.
                map.setCamera({
                    bounds: data.features[0].bbox,
                    padding: 40
                });
            } else {
                document.getElementById('listPanel').innerHTML = '<div class="statusMessage">Unable to find the location you searched for.</div>';
            }
        });
    }

    function setMapToUserLocation() {
        //Request the user's location.
        navigator.geolocation.getCurrentPosition(function(position) {
            //Convert the Geolocation API (Preview) position to a longitude and latitude position value that the map can interpret and center the map over it.
            map.setCamera({
                center: [position.coords.longitude, position.coords.latitude],
                zoom: maxClusterZoomLevel + 1
            });
        }, function(error) {
            //If an error occurs when the API tries to access the user's position information, display an error message.
            switch (error.code) {
                case error.PERMISSION_DENIED:
                    alert('User denied the request for geolocation.');
                    break;
                case error.POSITION_UNAVAILABLE:
                    alert('Position information is unavailable.');
                    break;
                case error.TIMEOUT:
                    alert('The request to get user position timed out.');
                    break;
                case error.UNKNOWN_ERROR:
                    alert('An unknown error occurred.');
                    break;
            }
        });
    }

    //Initialize the application when the page is loaded.
    window.onload = initialize;
    ```

1. W odbiorniku zdarzeń `ready` mapy dodaj kontrolkę powiększenia i znacznik HTML, aby wyświetlać środek obszaru wyszukiwania.

    ```JavaScript
    //Add a zoom control to the map.
    map.controls.add(new atlas.control.ZoomControl(), {
        position: 'top-right'
    });

    //Add an HTML marker to the map to indicate the center to use for searching.
    centerMarker = new atlas.HtmlMarker({
        htmlContent: '<div class="mapCenterIcon"></div>',
        position: map.getCamera().center
    });

    map.markers.add(centerMarker);
    ```

1. W odbiorniku zdarzeń `ready` mapy dodaj źródło danych. Następnie wykonaj wywołanie, aby załadować i przeanalizować zestaw danych. Włącz klastrowanie na źródle danych. Klastrowanie na źródle danych grupuje nakładające się punkty w klastrze. Klastry są rozdzielane na poszczególne punkty, gdy użytkownik powiększa widok. Takie zachowanie zapewnia lepszy komfort pracy użytkowników i zwiększa wydajność.

    ```JavaScript
    //Create a data source, add it to the map, and then enable clustering.
    datasource = new atlas.source.DataSource(null, {
        cluster: true,
        clusterMaxZoom: maxClusterZoomLevel - 1
    });

    map.sources.add(datasource);

    //Load all the store data now that the data source is defined.  
    loadStoreData();
    ```

1. Po załadowaniu zestawu danych w odbiorniku zdarzeń `ready` mapy zdefiniuj zestaw warstw w celu renderowania danych. Warstwa bąbelkowa jest używana do renderowania punktów danych klastra. Warstwa symboli jest używana do renderowania liczby punktów w każdym klastrze powyżej warstwy bąbelkowej. Druga warstwa symboli renderuje ikonę niestandardową dla poszczególnych lokalizacji na mapie.

   Dodaj zdarzenia `mouseover` i `mouseout` do warstwy bąbelkowej i warstwy ikon, aby zmieniać kursor myszy, gdy użytkownik umieści go na klastrze lub ikonie na mapie. Dodaj zdarzenie `click` do bąbelkowej warstwy klastra. To `click` zdarzenie powiększa się na dwóch poziomach mapy i wyrównuje mapę w klastrze, gdy użytkownik wybierze dowolny klaster. Dodaj zdarzenie `click` do warstwy ikon. Zdarzenie `click` wyświetla okno podręczne, które pokazuje szczegółowe informacje o kawiarni, kiedy użytkownik wybierze ikonę określonej lokalizacji. Dodaj do mapy zdarzenie, które monitoruje, kiedy mapa przestanie być przesuwana. Gdy to zdarzenie zostanie wyzwolone, zaktualizuj elementy na panelu listy.  

    ```JavaScript
    //Create a bubble layer to render clustered data points.
    var clusterBubbleLayer = new atlas.layer.BubbleLayer(datasource, null, {
        radius: 12,
        color: '#007faa',
        strokeColor: 'white',
        strokeWidth: 2,
        filter: ['has', 'point_count'] //Only render data points that have a point_count property; clusters have this property.
    });

    //Create a symbol layer to render the count of locations in a cluster.
    var clusterLabelLayer = new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'none' //Hide the icon image.
        },

        textOptions: {
            textField: ['get', 'point_count_abbreviated'],
            size: 12,
            font: ['StandardFont-Bold'],
            offset: [0, 0.4],
            color: 'white'
        }
    });

    map.layers.add([clusterBubbleLayer, clusterLabelLayer]);

    //Load a custom image icon into the map resources.
    map.imageSprite.add('myCustomIcon', iconImageUrl).then(function() {

    //Create a layer to render a coffee cup symbol above each bubble for an individual location.
    iconLayer = new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            //Pass in the ID of the custom icon that was loaded into the map resources.
            image: 'myCustomIcon',

            //Optionally, scale the size of the icon.
            font: ['SegoeUi-Bold'],

            //Anchor the center of the icon image to the coordinate.
            anchor: 'center',

            //Allow the icons to overlap.
            allowOverlap: true
        },

        filter: ['!', ['has', 'point_count']] //Filter out clustered points from this layer.
    });

    map.layers.add(iconLayer);

    //When the mouse is over the cluster and icon layers, change the cursor to a pointer.
    map.events.add('mouseover', [clusterBubbleLayer, iconLayer], function() {
        map.getCanvasContainer().style.cursor = 'pointer';
    });

    //When the mouse leaves the item on the cluster and icon layers, change the cursor back to the default (grab).
    map.events.add('mouseout', [clusterBubbleLayer, iconLayer], function() {
        map.getCanvasContainer().style.cursor = 'grab';
    });

    //Add a click event to the cluster layer. When the user selects a cluster, zoom into it by two levels.  
    map.events.add('click', clusterBubbleLayer, function(e) {
        map.setCamera({
            center: e.position,
            zoom: map.getCamera().zoom + 2
        });
    });

    //Add a click event to the icon layer and show the shape that was selected.
    map.events.add('click', iconLayer, function(e) {
        showPopup(e.shapes[0]);
    });

    //Add an event to monitor when the map is finished rendering the map after it has moved.
    map.events.add('render', function() {
        //Update the data in the list.
        updateListItems();
    });
    ```

1. Kiedy zestaw danych kawiarni jest ładowany, najpierw musi zostać pobrany. Następnie plik tekstowy musi zostać podzielony na wiersze. Pierwszy wiersz zawiera informacje nagłówka. Aby ułatwić śledzenie kodu, analizujemy nagłówek jako obiekt, który następnie możemy użyć w celu wyszukania indeksu komórki każdej właściwości. Pozostałe wiersze, oprócz pierwszego, przetwórz w pętli i utwórz lokalizację w formie punktu. Dodaj lokalizację w formie punktu do źródła danych. Na koniec zaktualizuj panel listy.

    ```JavaScript
    function loadStoreData() {

    //Download the store location data.
    fetch(storeLocationDataUrl)
        .then(response => response.text())
        .then(function(text) {

            //Parse the tab-delimited file data into GeoJSON features.
            var features = [];

            //Split the lines of the file.
            var lines = text.split('\n');

            //Grab the header row.
            var row = lines[0].split('\t');

            //Parse the header row and index each column to make the code for parsing each row easier to follow.
            var header = {};
            var numColumns = row.length;
            for (var i = 0; i < row.length; i++) {
                header[row[i]] = i;
            }

            //Skip the header row and then parse each row into a GeoJSON feature.
            for (var i = 1; i < lines.length; i++) {
                row = lines[i].split('\t');

                //Ensure that the row has the correct number of columns.
                if (row.length >= numColumns) {

                    features.push(new atlas.data.Feature(new atlas.data.Point([parseFloat(row[header['Longitude']]), parseFloat(row[header['Latitude']])]), {
                        AddressLine: row[header['AddressLine']],
                        City: row[header['City']],
                        Municipality: row[header['Municipality']],
                        AdminDivision: row[header['AdminDivision']],
                        Country: row[header['Country']],
                        PostCode: row[header['PostCode']],
                        Phone: row[header['Phone']],
                        StoreType: row[header['StoreType']],
                        IsWiFiHotSpot: (row[header['IsWiFiHotSpot']].toLowerCase() === 'true') ? true : false,
                        IsWheelchairAccessible: (row[header['IsWheelchairAccessible']].toLowerCase() === 'true') ? true : false,
                        Opens: parseInt(row[header['Opens']]),
                        Closes: parseInt(row[header['Closes']])
                    }));
                }
            }

            //Add the features to the data source.
            datasource.add(new atlas.data.FeatureCollection(features));

            //Initially, update the list items.
            updateListItems();
        });
    }
    ```

1. Gdy panel listy zostanie zaktualizowany, odległość jest obliczana. Ta odległość pochodzi z centrum mapy do wszystkich funkcji punktów w bieżącym widoku mapy. Lokalizacje są następnie sortowane według odległości. Generowany jest kod HTML w celu wyświetlania każdej lokalizacji na panelu listy.

    ```JavaScript
    var listItemTemplate = '<div class="listItem" onclick="itemSelected(\'{id}\')"><div class="listItem-title">{title}</div>{city}<br />Open until {closes}<br />{distance} miles away</div>';

    function updateListItems() {
        //Hide the center marker.
        centerMarker.setOptions({
            visible: false
        });

        //Get the current camera and view information for the map.
        var camera = map.getCamera();
        var listPanel = document.getElementById('listPanel');

        //Check to see whether the user is zoomed out a substantial distance. If they are, tell the user to zoom in and to perform a search or select the My Location button.
        if (camera.zoom < maxClusterZoomLevel) {
            //Close the pop-up window; clusters might be displayed on the map.  
            popup.close(); 
            listPanel.innerHTML = '<div class="statusMessage">Search for a location, zoom the map, or select the My Location button to see individual locations.</div>';
        } else {
            //Update the location of the centerMarker property.
            centerMarker.setOptions({
                position: camera.center,
                visible: true
            });

            //List the ten closest locations in the side panel.
            var html = [], properties;

            /*
            Generating HTML for each item that looks like this:
            <div class="listItem" onclick="itemSelected('id')">
                <div class="listItem-title">1 Microsoft Way</div>
                Redmond, WA 98052<br />
                Open until 9:00 PM<br />
                0.7 miles away
            </div>
            */

            //Get all the shapes that have been rendered in the bubble layer. 
            var data = map.layers.getRenderedShapes(map.getCamera().bounds, [iconLayer]);

            //Create an index of the distances of each shape.
            var distances = {};

            data.forEach(function (shape) {
                if (shape instanceof atlas.Shape) {

                    //Calculate the distance from the center of the map to each shape and store in the index. Round to 2 decimals.
                    distances[shape.getId()] = Math.round(atlas.math.getDistanceTo(camera.center, shape.getCoordinates(), 'miles') * 100) / 100;
                }
            });

            //Sort the data by distance.
            data.sort(function (x, y) {
                return distances[x.getId()] - distances[y.getId()];
            });

            data.forEach(function(shape) {
                properties = shape.getProperties();
                html.push('<div class="listItem" onclick="itemSelected(\'', shape.getId(), '\')"><div class="listItem-title">',
                properties['AddressLine'],
                '</div>',
                //Get a formatted addressLine2 value that consists of City, Municipality, AdminDivision, and PostCode.
                getAddressLine2(properties),
                '<br />',

                //Convert the closing time to a format that is easier to read.
                getOpenTillTime(properties),
                '<br />',

                //Get the distance of the shape.
                distances[shape.getId()],
                ' miles away</div>');
            });

            listPanel.innerHTML = html.join('');

            //Scroll to the top of the list panel in case the user has scrolled down.
            listPanel.scrollTop = 0;
        }
    }

    //This converts a time that's in a 24-hour format to an AM/PM time or noon/midnight string.
    function getOpenTillTime(properties) {
        var time = properties['Closes'];
        var t = time / 100;
        var sTime;

        if (time === 1200) {
            sTime = 'noon';
        } else if (time === 0 || time === 2400) {
            sTime = 'midnight';
        } else {
            sTime = Math.round(t) + ':';

            //Get the minutes.
            t = (t - Math.round(t)) * 100;

            if (t === 0) {
                sTime += '00';
            } else if (t < 10) {
                sTime += '0' + t;
            } else {
                sTime += Math.round(t);
            }

            if (time < 1200) {
                sTime += ' AM';
            } else {
                sTime += ' PM';
            }
        }

        return 'Open until ' + sTime;
    }

    //Create an addressLine2 string that contains City, Municipality, AdminDivision, and PostCode.
    function getAddressLine2(properties) {
        var html = [properties['City']];

        if (properties['Municipality']) {
            html.push(', ', properties['Municipality']);
        }

        if (properties['AdminDivision']) {
            html.push(', ', properties['AdminDivision']);
        }

        if (properties['PostCode']) {
            html.push(' ', properties['PostCode']);
        }

        return html.join('');
    }
    ```

1. Kiedy użytkownik wybiera element na panelu listy, kształt, z którym jest powiązany ten element, jest pobierany ze źródła danych. Generowane jest okno podręczne, które opiera się na informacjach o właściwości przechowywanych w danym kształcie. Mapa jest środkowana na kształcie. Jeśli mapa ma szerokość mniejszą niż 700 pikseli, widok mapy jest przesuwany, aby okno podręczne było widoczne.

    ```JavaScript
    //When a user selects a result in the side panel, look up the shape by its ID value and display the pop-up window.
    function itemSelected(id) {
        //Get the shape from the data source by using its ID.  
        var shape = datasource.getShapeById(id);
        showPopup(shape);

        //Center the map over the shape on the map.
        var center = shape.getCoordinates();
        var offset;

        //If the map is less than 700 pixels wide, then the layout is set for small screens.
        if (map.getCanvas().width < 700) {
            //When the map is small, offset the center of the map relative to the shape so that there is room for the popup to appear.
            offset = [0, -80];
        }

        map.setCamera({
            center: center,
            centerOffset: offset
        });
    }

    function showPopup(shape) {
        var properties = shape.getProperties();

        /* Generating HTML for the pop-up window that looks like this:

            <div class="storePopup">
                <div class="popupTitle">
                    3159 Tongass Avenue
                    <div class="popupSubTitle">Ketchikan, AK 99901</div>
                </div>
                <div class="popupContent">
                    Open until 22:00 PM<br/>
                    <img title="Phone Icon" src="images/PhoneIcon.png">
                    <a href="tel:1-800-XXX-XXXX">1-800-XXX-XXXX</a>
                    <br>Amenities:
                    <img title="Wi-Fi Hotspot" src="images/WiFiIcon.png">
                    <img title="Wheelchair Accessible" src="images/WheelChair-small.png">
                </div>
            </div>
        */

         //Calculate the distance from the center of the map to the shape in miles, round to 2 decimals.
        var distance = Math.round(atlas.math.getDistanceTo(map.getCamera().center, shape.getCoordinates(), 'miles') * 100)/100;

        var html = ['<div class="storePopup">'];
        html.push('<div class="popupTitle">',
            properties['AddressLine'],
            '<div class="popupSubTitle">',
            getAddressLine2(properties),
            '</div></div><div class="popupContent">',

            //Convert the closing time to a format that's easier to read.
            getOpenTillTime(properties),

            //Add the distance information.  
            '<br/>', distance,
            ' miles away',
            '<br /><img src="images/PhoneIcon.png" title="Phone Icon"/><a href="tel:',
            properties['Phone'],
            '">',  
            properties['Phone'],
            '</a>'
        );

        if (properties['IsWiFiHotSpot'] || properties['IsWheelchairAccessible']) {
            html.push('<br/>Amenities: ');

            if (properties['IsWiFiHotSpot']) {
                html.push('<img src="images/WiFiIcon.png" title="Wi-Fi Hotspot"/>');
            }

            if (properties['IsWheelchairAccessible']) {
                html.push('<img src="images/WheelChair-small.png" title="Wheelchair Accessible"/>');
            }
        }

        html.push('</div></div>');

        //Update the content and position of the pop-up window for the specified shape information.
        popup.setOptions({

            //Create a table from the properties in the feature.
            content:  html.join(''),
            position: shape.getCoordinates()
        });

        //Open the pop-up window.
        popup.open(map);
    }
    ```

Teraz masz w pełni funkcjonalny lokalizator sklepów. W przeglądarce internetowej otwórz plik *index.html* dla lokalizatora sklepów. Gdy klastry zostaną wyświetlone na mapie, możesz wyszukać lokalizację, używając pola wyszukiwania, wybierając przycisk Moja lokalizacja, wybierając klaster lub powiększając mapę, aby wyświetlić poszczególne lokalizacje.

Gdy użytkownik wybierze przycisk Moja lokalizacja po raz pierwszy, w przeglądarce zostanie wyświetlone ostrzeżenie o zabezpieczeniach z prośbą o zezwolenie na dostęp do lokalizacji użytkownika. Jeśli użytkownik wyrazi zgodę na udostępnienie swojej lokalizacji, mapa zostanie powiększona na obszarze lokalizacji użytkownika i zostaną wyświetlone pobliskie kawiarnie.

![Zrzut ekranu przedstawiający pytanie przeglądarki o zezwolenie na dostęp do lokalizacji użytkownika](./media/tutorial-create-store-locator/GeolocationApiWarning.png)

Po zastosowaniu wystarczająco dużego powiększenia obszaru zawierającego lokalizacje kawiarni klastry zostaną rozdzielone na poszczególne lokalizacje. Wybierz jedną z ikon mapy lub wybierz element w panelu bocznym, aby wyświetlić okno podręczne. W oknie podręcznym są wyświetlane informacje dotyczące wybranej lokalizacji.

![Zrzut ekranu przedstawiający ukończony lokalizator sklepów](./media/tutorial-create-store-locator/FinishedSimpleStoreLocator.png)

Jeśli rozmiar okna przeglądarki zostanie zmieniony tak, że jego szerokość będzie mniejsza niż 700 pikseli, lub aplikacja zostanie otworzona na urządzeniu przenośnym, układ zostanie zmieniony w celu lepszego dopasowania do małego ekranu.

![Zrzut ekranu przedstawiający wersję lokalizatora sklepów na małe ekrany](./media/tutorial-create-store-locator/FinishedSimpleStoreLocatorSmallScreen.png)

W tym samouczku pokazano, jak utworzyć podstawowy lokalizator magazynu za pomocą Azure Maps. Lokalizator sklepu utworzony w tym samouczku może mieć wszystkie funkcje, których potrzebujesz. Możesz dodać funkcje do swojego lokalizatora sklepów lub użyć bardziej zaawansowanych funkcji w celu uzyskania bardziej niestandardowego środowiska użytkownika: 

 * Włącz [sugestie podczas pisania](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20Autosuggest%20and%20JQuery%20UI) w polu wyszukiwania.  
 * Dodaj [obsługę wielu języków](https://azuremapscodesamples.azurewebsites.net/?sample=Map%20Localization). 
 * Umożliw użytkownikom [filtrowanie lokalizacji wzdłuż trasy](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Data%20Along%20Route). 
 * Dodaj możliwość [ustawienia filtrów](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Symbols%20by%20Property). 
 * Dodaj obsługę określania początkowej wartości wyszukiwania przy użyciu ciągu zapytania. Jeśli włączysz tę opcję w lokalizatorze sklepów, użytkownicy będą mogli dodawać zakładki i udostępniać wyszukiwania. Zapewnia ona także prostą metodę przekazywania wyszukiwań do tej strony z innej strony.  
 * Wdróż swój lokalizator sklepów jako [aplikację internetową usługi Azure App Service](../app-service/quickstart-html.md). 
 * Przechowuj dane w bazie danych i wyszukuj pobliskie lokalizacje. Aby dowiedzieć się więcej, zobacz [omówienie typów danych przestrzennych programu SQL Server](/sql/relational-databases/spatial/spatial-data-types-overview?preserve-view=true&view=sql-server-2017) i artykuł na temat [wysyłania zapytań względem danych przestrzennych dla najbliższego otoczenia](/sql/relational-databases/spatial/query-spatial-data-for-nearest-neighbor?preserve-view=true&view=sql-server-2017).

Możesz [wyświetlić pełny kod źródłowy](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator), [wyświetlić próbkę na żywo](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Simple%20Store%20Locator) i dowiedzieć się więcej na temat pokrycia i możliwości Azure Maps przy użyciu [poziomów powiększenia i siatki kafelków](zoom-levels-and-tile-grid.md). Można również [użyć wyrażeń stylów opartych na danych](data-driven-style-expressions-web-sdk.md) do zastosowania do logiki biznesowej.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać dodatkowe przykłady kodu i zapoznać się z interaktywnym środowiskiem kodowania:

> [!div class="nextstepaction"]
> [Jak używać kontrolki mapy](how-to-use-map-control.md)
