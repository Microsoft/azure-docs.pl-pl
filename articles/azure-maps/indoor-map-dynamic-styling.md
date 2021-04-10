---
title: Zaimplementuj dynamiczne style dla programu Azure Maps Creator (wersja zapoznawcza) Maps
description: Dowiedz się, jak zaimplementować dynamiczne style dla programu Creator (wersja zapoznawcza) Maps
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: a23c492d4a81703c0dc6612928a56b5b31d52cae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101726323"
---
# <a name="implement-dynamic-styling-for-creator-preview-indoor-maps"></a>Zaimplementuj dynamiczne style dla programu Creator (wersja zapoznawcza) Maps

> [!IMPORTANT]
> Usługi Azure Maps Creator Services są obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Usługa stanu funkcji](/rest/api/maps/featurestate) Azure Maps Creator umożliwia stosowanie stylów na podstawie właściwości dynamicznych funkcji danych mapy.  Można na przykład renderować Pokoje spotkań funkcji z określonym kolorem w celu odzwierciedlenia stanu zajętości. W tym artykule przedstawiono sposób dynamicznego renderowania funkcji mapy pomieszczeń przy użyciu [usługi stanu funkcji](/rest/api/maps/featurestate) i pozostałego [modułu sieci Web](how-to-use-indoor-module.md).

## <a name="prerequisites"></a>Wymagania wstępne

1. [Tworzenie konta usługi Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Uzyskaj podstawowy klucz subskrypcji](quick-demo-map-app.md#get-the-primary-key-for-your-account), nazywany także kluczem podstawowym lub kluczem subskrypcji.
3. [Tworzenie zasobu twórcy (wersja zapoznawcza)](how-to-manage-creator.md)
4. Pobierz [przykładowy pakiet rysowania](https://github.com/Azure-Samples/am-creator-indoor-data-examples).
5. [Utwórz mapę pomieszczeń](tutorial-creator-indoor-maps.md) , aby uzyskać `tilesetId` i `statesetId` .
6. Utwórz aplikację sieci Web, wykonując czynności opisane w temacie [jak używać modułu mapy w języku pomieszczeń](how-to-use-indoor-module.md).

W tym samouczku jest stosowana aplikacja programu [Poster](https://www.postman.com/) , ale można wybrać inne środowisko deweloperskie interfejsu API.

## <a name="implement-dynamic-styling"></a>Implementowanie dynamicznego ustawiania stylów

Po spełnieniu wymagań wstępnych należy mieć prostą aplikację sieci Web, która została skonfigurowana przy użyciu klucza subskrypcji, `tilesetId` i `statesetId` .

### <a name="select-features"></a>Wybieranie funkcji

Aby zaimplementować dynamiczne style, funkcja, taka jak spotkanie lub Pokój konferencyjny, musi odwoływać się do niej funkcja `id` . Ta funkcja służy `id` do aktualizowania właściwości dynamicznej lub *stanu* tej funkcji. Aby wyświetlić funkcje zdefiniowane w zestawie danych, można użyć jednej z następujących metod:

* Interfejs API WFS (usługa funkcji sieci Web). Do zestawów danych można wykonywać zapytania przy użyciu interfejsu API WFS. WFS jest zgodna z funkcjami interfejsu API Open Geospatial Consortium. Interfejs API WFS jest przydatny do wykonywania zapytań dotyczących funkcji w ramach zestawu danych. Na przykład można użyć WFS, aby znaleźć wszystkie pokoje spotkań na średnim poziomie danego obiektu i piętra.

* Zaimplementuj dostosowany kod umożliwiający użytkownikowi wybranie funkcji na mapie przy użyciu aplikacji sieci Web. W tym artykule użyjemy tej opcji.  

Poniższy skrypt implementuje zdarzenie kliknięcia myszą. Kod pobiera funkcję `id` w oparciu o kliknięty punkt. W aplikacji możesz wstawić kod poniżej bloku kodu kierownika pomieszczeń. Uruchom aplikację i sprawdź konsolę, aby uzyskać funkcję `id` klikniętego punktu.

```javascript
/* Upon a mouse click, log the feature properties to the browser's console. */
map.events.add("click", function(e){

    var features = map.layers.getRenderedShapes(e.position, "indoor");

    features.forEach(function (feature) {
        if (feature.layer.id == 'indoor_unit_office') {
            console.log(feature);
        }
    });
});
```

Samouczek [Tworzenie mapy pomieszczeńowej](tutorial-creator-indoor-maps.md) skonfigurował funkcję stateset, aby akceptować aktualizacje stanu dla programu `occupancy` .

W następnej sekcji ustawimy *stan* zajętości pakietu Office `UNIT26` na `true` . Pakiet Office `UNIT27` zostanie ustawiony na `false` .

### <a name="set-occupancy-status"></a>Ustawianie stanu zajętości

 Teraz będziemy aktualizować stan dwóch biur `UNIT26` i `UNIT27` :

1. W aplikacji pocztowej wybierz pozycję **Nowy**. W oknie **Tworzenie nowego** okna wybierz pozycję **Żądaj**. Wprowadź **nazwę żądania** i wybierz kolekcję. Kliknij pozycję **Zapisz**.

2. Użyj [interfejsu API Stany aktualizacji funkcji](/rest/api/maps/featurestate/updatestatespreview) , aby zaktualizować stan. Przekaż identyfikator stateset i `UNIT26` jedną z dwóch jednostek. Dołącz swój klucz subskrypcji Azure Maps. Oto adres URL żądania **post** , aby zaktualizować stan:

    ```http
    https://atlas.microsoft.com/featureState/state?api-version=1.0&statesetID={statesetId}&featureID=UNIT26&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. W **nagłówkach** żądania **post** ustaw wartość `Content-Type` `application/json` . W **treści** żądania **post** Zapisz następujący nieprzetworzony kod JSON z aktualizacjami funkcji. Aktualizacja zostanie zapisana tylko wtedy, gdy sygnatura czasowa użyta w poprzednich żądaniach aktualizacji stanu funkcji dla tej samej funkcji `ID` . Przekaż "zajęty", `keyName` Aby zaktualizować jego wartość.

    ```json
    {
        "states": [
            {
                "keyName": "occupied",
                "value": true,
                "eventTimestamp": "2019-11-14T17:10:20"
            }
        ]
    }
    ```

4. Wykonaj ponownie kroki 2 i 3 `UNIT27` , używając następującego kodu JSON.

    ``` json
    {
        "states": [
            {
                "keyName": "occupied",
                "value": false,
                "eventTimestamp": "2019-11-14T17:10:20"
            }
        ]
    }
    ```

### <a name="visualize-dynamic-styles-on-a-map"></a>Wizualizowanie stylów dynamicznych na mapie

Aplikacja sieci Web wcześniej otwarta w przeglądarce powinna teraz odzwierciedlać zaktualizowany stan funkcji mapy. `UNIT27`(142) powinien wyglądać zielony i `UNIT26` (143) powinien wyglądać czerwony.

![Wolne miejsce na zielono i zajętym pokoju w kolorze czerwonym](./media/indoor-map-dynamic-styling/room-state.png)

[Zobacz pokaz na żywo](https://azuremapscodesamples.azurewebsites.net/?sample=Creator%20indoor%20maps)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej, odczytując:

> [!div class="nextstepaction"]
> [Twórca (wersja zapoznawcza) dla mapowania pomieszczeń](creator-indoor-maps.md)

Zobacz odwołania do interfejsów API wymienionych w tym artykule:

> [!div class="nextstepaction"]
> [Przekazywanie danych](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [Konwersja danych](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [Zestawu](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [Tileset](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [Zestaw stanu funkcji](creator-indoor-maps.md#feature-statesets)

> [!div class="nextstepaction"]
> [Usługa WFS](creator-indoor-maps.md#web-feature-service-api)