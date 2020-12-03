---
title: Zażądaj danych podniesienia uprawnień za pomocą usługi podniesienia uprawnień Azure Maps
description: Dowiedz się, jak zażądać danych podniesienia uprawnień za pomocą usługi podniesienia uprawnień Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/02/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 9937d72b44eb33df8027eddb9a9f500a372c9037
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96554264"
---
# <a name="request-elevation-data-using-the-azure-maps-elevation-service"></a>Zażądaj danych podniesienia uprawnień za pomocą usługi podniesienia uprawnień Azure Maps

[Usługa podniesienia uprawnień](https://docs.microsoft.com/rest/api/maps/elevation) Azure Maps udostępnia interfejsy API do wykonywania zapytań o dane podniesienia uprawnień dla lokalizacji na ziemi. Można zażądać próbkowania pobranych próbek wzdłuż ścieżek, w obrębie zdefiniowanego pola ograniczenia lub określonych współrzędnych. Ponadto można użyć [interfejsu API renderowania w wersji 2-get mapy](https://docs.microsoft.com/rest/api/maps/renderv2) , aby pobrać dane podniesienia uprawnień w formacie kafelków. Kafelki są dostarczane w formacie TIFF rastrowej. W tym artykule pokazano, jak za pomocą usługi podniesienia uprawnień Azure Maps i uzyskać dostęp do interfejsu API kafelków mapy. Dane podniesienia uprawnień można żądać zarówno w formatach GEOJSON, jak i GeoTIFF.

## <a name="prerequisites"></a>Wymagania wstępne

1. [Utwórz konto Azure Maps w warstwie cenowej S1](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Uzyskaj podstawowy klucz subskrypcji](quick-demo-map-app.md#get-the-primary-key-for-your-account), nazywany także kluczem podstawowym lub kluczem subskrypcji.

Aby uzyskać więcej informacji na temat uwierzytelniania w Azure Maps, [Zarządzaj uwierzytelnianiem w programie Azure Maps](how-to-manage-authentication.md).

W tym artykule jest stosowana aplikacja programu [Poster](https://www.postman.com/) , ale można wybrać inne środowisko deweloperskie interfejsu API.

## <a name="request-elevation-data-in-raster-tiled-format"></a>Zażądaj danych podniesienia uprawnień w formacie rastrowym

Aby zażądać danych podniesienia uprawnień w formacie kafelków rastrowych, użyj [interfejsu API renderowania w wersji 2-get](https://docs.microsoft.com/rest/api/maps/renderv2). Jeśli kafelek można znaleźć, interfejs API zwraca kafelek jako GeoTIFF. W przeciwnym razie interfejs API zwróci wartość 0. Wszystkie kafelki rastrowych DEM są używane w trybie Geo (poziom morski). W tym przykładzie będziemy żądać danych podniesienia uprawnień dla Mt. Everest.

>[!TIP]
>Aby można było pobrać kafelek w określonym obszarze mapy świata, należy znaleźć odpowiedni kafelek na odpowiednim poziomie powiększenia. Należy również pamiętać, że WorldDEM obejmuje całe globalne landmass, ale nie obejmuje oceanów.  Aby uzyskać więcej informacji, zobacz [poziomy powiększenia i siatka kafelków](zoom-levels-and-tile-grid.md).

1. Otwórz aplikację Poster. W górnej części aplikacji Poster wybierz pozycję **Nowy**. W oknie **Tworzenie nowego** okna wybierz pozycję **Kolekcja**.  Nadaj kolekcji nazwę i wybierz przycisk **Utwórz** .

2. Aby utworzyć żądanie, wybierz pozycję **nowe** ponownie. W oknie **Tworzenie nowego** okna wybierz pozycję **Żądaj**. Wprowadź **nazwę żądania** dla żądania. Wybierz kolekcję utworzoną w poprzednim kroku, a następnie wybierz pozycję **Zapisz**.

3. Wybierz metodę **Get** http na karcie Konstruktor i wprowadź następujący adres URL, aby zażądać kafelka rastrowego. W przypadku tego żądania i innych żądań wymienionych w tym artykule Zastąp `{Azure-Maps-Primary-Subscription-key}` klucz subskrypcji podstawowym.

    ```http
    https://atlas.microsoft.com/map/tile?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=2.0&tilesetId=microsoft.dem&zoom=13&x=6074&y=3432
    ```

4. Kliknij przycisk **Wyślij** . Kafelek rastrowy powinien zawierać dane podniesienia uprawnień w formacie GeoTIFF. Każdy piksel w danych pierwotnych kafelków rastrowych jest typu `float` . Wartość każdego piksela reprezentuje wysokość podniesienia uprawnień w licznikach.

## <a name="request-elevation-data-in-geojson-format"></a>Zażądaj danych podniesienia uprawnień w formacie GEOJSON

Użyj interfejsów API usługi podniesienia uprawnień, aby zażądać danych podniesienia uprawnień w formacie GEOJSON. W tej sekcji przedstawiono każdy z trzech interfejsów API:

* [Pobierz dane dla punktów](https://docs.microsoft.com/rest/api/maps/elevation/getdataforlatlongcoordinates)
* [Publikuj dane dla punktów](https://docs.microsoft.com/rest/api/maps/elevation/postdataforlatlongcoordinates)
* [Pobieranie danych dla linii łamanej](https://docs.microsoft.com/rest/api/maps/elevation/getdataforpolyline)
* [Publikowanie danych dla linii łamanej](https://docs.microsoft.com/rest/api/maps/elevation/postdataforpolyline)
* [Pobierz dane dla pola ograniczenia](https://docs.microsoft.com/rest/api/maps/elevation/getdataforboundingbox)

>[!IMPORTANT]
> Gdy nie można zwrócić danych, wszystkie interfejsy API zwracają `0` .

### <a name="request-elevation-data-for-points"></a>Zażądaj danych podniesienia uprawnień dla punktów

W tym przykładzie użyjemy [interfejsu API Pobierz dane dla punktów](https://docs.microsoft.com/rest/api/maps/elevation/getdataforlatlongcoordinates) , aby zażądać podniesienia uprawnień na początku. Everest i Chamlang górach. Następnie użyjemy [interfejsu API publikowania danych dla punktów](https://docs.microsoft.com/rest/api/maps/elevation/postdataforlatlongcoordinates) , aby zażądać danych podniesienia uprawnień przy użyciu tych samych dwóch punktów. Latitudes i Długość geograficzna w adresie URL powinny być w zakresie od WGS84 (World Geodetic System).

 >[!IMPORTANT]
 >Ze względu na limit długości znaku adresu URL wynoszący 2048, nie jest możliwe przekazanie więcej niż 100 współrzędnych w ciągu do żądania pobrania adresu URL. Jeśli zamierzasz przekazać więcej niż 100 współrzędnych jako ciąg rozdzielany potoku, użyj danych POST dla punktów.

1. Aby utworzyć żądanie, wybierz pozycję **nowe** ponownie. W oknie **Tworzenie nowego** okna wybierz pozycję **Żądaj**. Wprowadź **nazwę żądania** dla żądania. Wybierz kolekcję utworzoną w poprzednim kroku, a następnie wybierz pozycję **Zapisz**.

2. Wybierz metodę **Get** http na karcie Konstruktor i wprowadź następujący adres URL. W przypadku tego żądania i innych żądań wymienionych w tym artykule Zastąp `{Azure-Maps-Primary-Subscription-key}` klucz subskrypcji podstawowym.

    ```http
    https://atlas.microsoft.com/elevation/point/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&points=-73.998672,40.714728|150.644,-34.397
    ```

3. Kliknij przycisk **Wyślij** .  Otrzymasz następującą odpowiedź JSON:

    ```json
    {
    "data": [
        {
            "coordinate": {
                "latitude": 40.714728,
                "longitude": -73.998672
            },
            "elevationInMeter": 12.142355447638208
        },
        {
            "coordinate": {
                "latitude": -34.397,
                "longitude": 150.644
            },
            "elevationInMeter": 384.47041445517846
        }
    ]
    }
    ```

4. Teraz wywołamy [interfejs API danych post dla punktów](https://docs.microsoft.com/rest/api/maps/elevation/postdataforlatlongcoordinates) , aby uzyskać dane podniesienia uprawnień dla tych samych dwóch punktów. Wybierz metodę **post** http na karcie Konstruktor i wprowadź następujący adres URL. W przypadku tego żądania i innych żądań wymienionych w tym artykule Zastąp `{Azure-Maps-Primary-Subscription-key}` klucz subskrypcji podstawowym.

    ```http
    https://atlas.microsoft.com/elevation/point/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

5. W **nagłówkach** żądania **post** ustaw wartość `Content-Type` `application/json` . W **treści** podaj poniższe informacje o punkcie współrzędnych. Gdy skończysz, kliknij przycisk **Wyślij**.

     ```json
    [
        {
            "lon": -73.998672,
            "lat": 40.714728
        },
        {
            "lon": 150.644,
            "lat": -34.397
        }
    ]
    ```

### <a name="request-elevation-data-samples-along-a-polyline"></a>Próbki danych podniesienia uprawnień w linii łamanej

W tym przykładzie użyjemy poleceń [Pobierz dane dla linii łamanej](https://docs.microsoft.com/rest/api/maps/elevation/getdataforpolyline) , aby zażądać pięciu przykładowo rozmieszczonych próbek danych podniesienia uprawnień wzdłuż prostej linii między współrzędne na Mt. Everest i Chamlang górach. Oba współrzędne muszą być zdefiniowane w formacie długim/warstwowym. Jeśli nie określisz wartości `samples` parametru, liczba próbek domyślnie wynosi 10. Maksymalna liczba próbek to 2 000.

Następnie użyjemy funkcji Pobierz dane dla linii łamanej, aby zażądać trzech równomiernie rozmieszczonych próbek danych podniesienia uprawnień wzdłuż ścieżki. Zdefiniujemy dokładną lokalizację dla przykładów, przekazując trzy pary współrzędnych "Long/lat".

Na koniec użyjemy [interfejsu API danych post dla linii łamanej](https://docs.microsoft.com/rest/api/maps/elevation/postdataforpolyline) , aby zażądać danych podniesienia uprawnień przy użyciu tych samych trzech równomiernie rozwidzianych próbek.

Latitudes i Długość geograficzna w adresie URL powinny być w zakresie od WGS84 (World Geodetic System).

 >[!IMPORTANT]
 >Ze względu na limit długości znaku adresu URL wynoszący 2048, nie jest możliwe przekazanie więcej niż 100 współrzędnych w ciągu do żądania pobrania adresu URL. Jeśli zamierzasz przekazać więcej niż 100 współrzędnych jako ciąg rozdzielany potoku, użyj danych POST dla punktów.

1. Wybierz pozycję **Nowy**. W oknie **Tworzenie nowego** okna wybierz pozycję **Żądaj**. Wprowadź **nazwę żądania** i wybierz kolekcję. Kliknij przycisk **Zapisz**.

2. Wybierz metodę **Get** http na karcie Konstruktor i wprowadź następujący adres URL. W przypadku tego żądania i innych żądań wymienionych w tym artykule Zastąp `{Azure-Maps-Primary-Subscription-key}` klucz subskrypcji podstawowym.

   ```http
    https://atlas.microsoft.com/elevation/line/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&lines=-73.998672,40.714728|150.644,-34.397&samples=5
    ```

3. Kliknij przycisk **Wyślij** .  Otrzymasz następującą odpowiedź JSON:

    ```JSON
    {
        "data": [
            {
                "coordinate": {
                    "latitude": 40.714728,
                    "longitude": -73.998672
                },
                "elevationInMeter": 12.14236
            },
            {
                "coordinate": {
                    "latitude": 21.936796000000001,
                    "longitude": -17.838003999999998
                },
                "elevationInMeter": 0.0
            },
            {
                "coordinate": {
                    "latitude": 3.1588640000000012,
                    "longitude": 38.322664000000003
                },
                "elevationInMeter": 598.66943
            },
            {
                "coordinate": {
                    "latitude": -15.619067999999999,
                    "longitude": 94.483332000000019
                },
                "elevationInMeter": 0.0
            },
            {
                "coordinate": {
                    "latitude": -34.397,
                    "longitude": 150.644
                },
                "elevationInMeter": 384.47041
            }
        ]
    }
    ```

4. Teraz będziemy żądać trzech próbek podniesienia uprawnień wzdłuż ścieżki między współrzędnymi w instalacji Everest, Chamlang i Jannu górach. W sekcji **params** Skopiuj następującą tablicę współrzędnych dla wartości `lines` klucza zapytania.

    ```html
        86.9797222, 27.775|86.9252778, 27.9880556 | 88.0444444, 27.6822222
    ```

5. Zmień `samples` wartość klucza zapytania na `3` .  Na poniższej ilustracji przedstawiono nowe wartości.

     :::image type="content" source="./media/how-to-request-elevation-data/get-elevation-samples.png" alt-text="Pobierz trzy próbki danych podniesienia uprawnień.":::

6. Kliknij przycisk **Wyślij** niebieską. Otrzymasz następującą odpowiedź JSON:

    ```json
    {
        "data": [
            {
                "coordinate": {
                    "latitude": 27.775,
                    "longitude": 86.9797222
                },
                "elevationInMeter": 7116.0348851572589
            },
            {
                "coordinate": {
                    "latitude": 27.737403546316028,
                    "longitude": 87.411180791156454
                },
                "elevationInMeter": 1798.6945512521534
            },
            {
                "coordinate": {
                    "latitude": 27.682222199999998,
                    "longitude": 88.0444444
                },
                "elevationInMeter": 7016.9372013588072
            }
        ]
    }
    ```

7. Teraz wywołamy [interfejs API danych post dla linii łamanej](https://docs.microsoft.com/rest/api/maps/elevation/postdataforpolyline) , aby uzyskać dane podniesienia uprawnień dla tych samych trzech punktów. Wybierz metodę **post** http na karcie Konstruktor i wprowadź następujący adres URL. W przypadku tego żądania i innych żądań wymienionych w tym artykule Zastąp `{Azure-Maps-Primary-Subscription-key}` klucz subskrypcji podstawowym.

    ```http
    https://atlas.microsoft.com/elevation/line/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&samples=5
    ```

8. W **nagłówkach** żądania **post** ustaw wartość `Content-Type` `application/json` . W **treści** podaj poniższe informacje o punkcie współrzędnych. Gdy skończysz, kliknij przycisk **Wyślij**.

     ```json
    [
        {
            "lon": 86.9797222,
            "lat": 27.775
        },
        {
            "lon": 86.9252778,
            "lat": 27.9880556
        },
        {
            "lon": 88.0444444,
            "lat": 27.6822222
        }
    ]
    ```

### <a name="request-elevation-data-by-bounding-box"></a>Zażądaj danych podniesienia uprawnień przez pole ograniczenia

Teraz użyjemy [pola Pobierz dane dla ograniczenia](https://docs.microsoft.com/rest/api/maps/elevation/getdataforboundingbox) , aby zażądać danych podniesienia uprawnień blisko Mt. Rainier, WA. Dane podniesienia uprawnień zostaną zwrócone w równomiernie rozmieszczeniu w obrębie pola ograniczenia. Obszar ograniczający zdefiniowany przez (2) zestawy lat/długich współrzędnych (Szerokość geograficzna, Długość geograficzna, Szerokość geograficzna, Długość geograficzna) jest podzielony na wiersze i kolumny. Krawędzie konta pola ograniczenia dla dwóch (2) wierszy i dwóch (2) kolumn. Podniesienia uprawnień są zwracane dla wierzchołków siatki utworzonych w ramach przecięć wierszy i kolumn. Do 2000 podniesienia uprawnień można zwrócić w jednym żądaniu.

W tym przykładzie określimy wiersze = 3 i kolumny = 6. w odpowiedzi są zwracane 18 wartości podniesienia uprawnień. Na poniższym diagramie wartości podniesienia uprawnień są uporządkowane począwszy od górnego rogu, a następnie Kontynuuj od północy do wschodu i od południa do północnego.  Punkty podniesienia uprawnień są numerowane w kolejności, w jakiej są zwracane.

:::image type="content" source="./media/how-to-request-elevation-data/bounding-box.png" border="false" alt-text="Współrzędne pola ograniczenia na początku i w rogach.":::

1. Wybierz pozycję **Nowy**. W oknie **Tworzenie nowego** okna wybierz pozycję **Żądaj**. Wprowadź **nazwę żądania** i wybierz kolekcję. Kliknij przycisk **Zapisz**.

2. Wybierz metodę **Get** http na karcie Konstruktor i wprowadź następujący adres URL. W przypadku tego żądania i innych żądań wymienionych w tym artykule Zastąp `{Azure-Maps-Primary-Subscription-key}` klucz subskrypcji podstawowym.

    ```http
    https://atlas.microsoft.com/elevation/lattice/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&bounds=-121.66853362143818, 46.84646479863713,-121.65853362143818, 46.85646479863713&rows=2&columns=3
    ```

3. Kliknij przycisk **Wyślij** niebieską. 18 próbek danych podniesienia uprawnień, jeden dla każdego wierzchołka siatki, są zwracane w odpowiedzi.

    ```json
    {
    "data": [
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2298.6581875651746
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2306.3980756609963
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2279.3385479564113
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2233.1549264690366
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2196.4485923541492
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2133.1756767157253
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2345.3227848228803
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2292.2449195443587
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2270.5867788258074
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2296.8311427390604
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2266.0729430891065
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2242.216346631234
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2378.460838833359
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2327.6761137260387
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2208.3782743402949
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2106.9526472760981
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2054.3270174034078
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2030.6438331110671
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2318.753153399402
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2253.88875188271
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2136.6145845357587
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2073.6734467948486
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2042.994055784251
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 1988.3631481900356
        }
    ]
    }
    ```

## <a name="samples-use-elevation-service-apis-in-azure-maps-control"></a>Przykłady: Używanie interfejsów API usługi podniesienia uprawnień w kontrolce Azure Maps

### <a name="get-elevation-data-by-coordinate-position"></a>Pobierz dane podniesienia uprawnień według położenia współrzędnej

Na poniższej przykładowej stronie sieci Web pokazano, jak używać kontrolki mapy do wyświetlania danych podniesienia uprawnień w punkcie współrzędnych. Gdy użytkownik przeciągnie znacznik, mapa będzie wyświetlać dane podniesienia uprawnień w oknie podręcznym.

<br/>

<iframe height="500" style="width:100%;" scrolling="no" title="Pobieranie podniesienia uprawnień na pozycji" src="https://codepen.io/azuremaps/embed/c840b510e113ba7cb32809591d5f96a2?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/c840b510e113ba7cb32809591d5f96a2'>podniesienie poziomu pióra na pozycji</a> o Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) w witrynie <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="get-elevation-data-by-bounding-box"></a>Pobierz dane podniesienia uprawnień przez pole ograniczenia

Na poniższej przykładowej stronie sieci Web pokazano, jak używać kontrolki mapy do wyświetlania danych podniesienia uprawnień zawartych w polu ograniczenia. Użytkownik definiuje pole ograniczenia, klikając `square` ikonę w lewym górnym rogu i rysując kwadrat w dowolnym miejscu na mapie. Kontrolka mapy będzie przetworzyć dane podniesienia uprawnień zgodnie z kolorami określonymi w kluczu znajdującym się w prawym górnym rogu.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Podniesienia uprawnień przez pole ograniczenia" src="https://codepen.io/azuremaps/embed/619c888c70089c3350a3e95d499f3e48?height=500&theme-id=default&default-tab=js,result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/619c888c70089c3350a3e95d499f3e48'>podniesienia uprawnień piórem według obwiedni</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="get-elevation-data-by-polyline-path"></a>Pobieranie danych o podniesionych uprawnieniach według ścieżki linii łamanej

Na poniższej przykładowej stronie sieci Web pokazano, jak używać kontrolki mapy do wyświetlania danych podniesienia uprawnień wzdłuż ścieżki. Użytkownik definiuje ścieżkę, klikając `PolyLine` ikonę w lewym górnym rogu i rysując linię łamaną na mapie. Kontrolka mapy następnie renderuje dane podniesienia uprawnień w kolorach, które są określone w kluczu znajdującym się w prawym górnym rogu.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Gradient ścieżki podniesienia uprawnień" src="https://codepen.io/azuremaps/embed/7bee08e5cb13d05cb0a11636b60f14ca?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/7bee08e5cb13d05cb0a11636b60f14ca'>gradient ścieżki podniesienia uprawnień</a> piórem według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat interfejsów API podniesienia uprawnień Azure Maps, zobacz:

> [!div class="nextstepaction"]
> [Podniesienie uprawnień — pobieranie danych dla długich współrzędnych lat](https://docs.microsoft.com/rest/api/maps/elevation/getdataforlatlongcoordinates)

> [!div class="nextstepaction"]
> [Podniesienie uprawnień — pobieranie danych dla pola ograniczenia](https://docs.microsoft.com/rest/api/maps/elevation/getdataforboundingbox)

> [!div class="nextstepaction"]
> [Podniesienie uprawnień — uzyskiwanie danych dla linii łamanej](https://docs.microsoft.com/rest/api/maps/elevation/getdataforpolyline)

> [!div class="nextstepaction"]
> [Renderowanie w wersji 2 — pobieranie kafelka mapy](https://docs.microsoft.com/rest/api/maps/renderv2)

Aby uzyskać pełną listę Azure Maps interfejsów API REST, zobacz:

> [!div class="nextstepaction"]
> [Interfejsy API REST Azure Maps](https://docs.microsoft.com/rest/api/maps/)
