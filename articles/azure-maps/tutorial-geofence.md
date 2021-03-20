---
title: 'Samouczek: Tworzenie geoogrodzenia i śledzenie urządzeń na mapie Microsoft Azure'
description: Samouczek dotyczący sposobu konfigurowania geoogrodzenia. Zobacz, jak śledzić urządzenia względem geoogrodzenia przy użyciu usługi przestrzennej Azure Maps
author: anastasia-ms
ms.author: v-stharr
ms.date: 8/20/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 759adea3cf34b79c76b6facec3bd4626ca54107e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98625036"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>Samouczek: Konfigurowanie geofencingu przy użyciu usługi Azure Maps

Ten samouczek przeprowadzi Cię przez podstawowe informacje na temat tworzenia i używania usług Azure Maps geoogrodzenia. Należy to zrobić w kontekście następującego scenariusza:

*Menedżer lokacji konstrukcja musi śledzić sprzęt w miarę wprowadzania i pozostawia obwody w ramach budowy. Za każdym razem, gdy sprzęt opuszcza lub wprowadza te obwody, do programu Operations Manager wysyłane jest powiadomienie e-mail.*

Azure Maps udostępnia wiele usług umożliwiających śledzenie sprzętu i opuszczanie obszaru budowy. W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Przekazuj [dane GEOnotacji GEOJSON](geofence-geojson.md) , które definiują obszary budowy, które chcesz monitorować. Użyjesz [interfejsu API przekazywania danych](/rest/api/maps/data/uploadpreview) , aby przekazać geoogrodzenie jako współrzędne wielokąta do konta Azure Maps.
> * Skonfiguruj dwie [Aplikacje logiki](../event-grid/handler-webhooks.md#logic-apps) , które po wyzwoleniu wysyłają powiadomienia e-mail do programu Operations Manager lokacji konstrukcja, gdy sprzęt wprowadza i opuszcza obszar geoogrodzenia.
> * Użyj [Azure Event Grid](../event-grid/overview.md) , aby subskrybować zdarzenia wprowadzania i kończenia dla Azure Maps geoogrodzenia. Należy skonfigurować dwie subskrypcje zdarzeń elementu webhook, które wywołują punkty końcowe HTTP zdefiniowane w dwóch aplikacjach logiki. Usługa Logic Apps wysyła odpowiednie powiadomienia e-mail dotyczące sprzętu przechodzącego poza lub wprowadzającego geoogrodzenie.
> * Użyj [interfejsu API wyszukiwania geoogrodzenia Szukaj](/rest/api/maps/spatial/getgeofence) , aby otrzymywać powiadomienia, gdy sprzęt zostanie zakończony i przejdzie do obszaru geoogrodzenia.

## <a name="prerequisites"></a>Wymagania wstępne

1. [Utwórz konto Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account).
2. [Uzyskaj podstawowy klucz subskrypcji](quick-demo-map-app.md#get-the-primary-key-for-your-account), nazywany także kluczem podstawowym lub kluczem subskrypcji.

W tym samouczku jest stosowana aplikacja programu [Poster](https://www.postman.com/) , ale można wybrać inne środowisko deweloperskie interfejsu API.

## <a name="upload-geofencing-geojson-data"></a>Przekazywanie geogeofencingu danych GEOJSON

W tym samouczku przekażemy geoplikowanie GEOJSON dane, które zawierają `FeatureCollection` . `FeatureCollection`Zawiera dwa geoogrodzenia, które definiują obszary wielokąta w obrębie lokacji konstrukcja. Pierwszy geoogrodzenie nie ma czasu wygaśnięcia ani ograniczeń. Dla drugiego z nich można wykonywać zapytania tylko w godzinach pracy (9:00 AM-5:00 PM w strefie czasowej pacyficznego) i nie będzie on już ważny po 1 stycznia 2022. Aby uzyskać więcej informacji na temat formatu GEOJSON, zobacz geovisioning [danych GEOJSON](geofence-geojson.md).

>[!TIP]
>W dowolnym momencie możesz zaktualizować dane dotyczące geofencingu. Aby uzyskać więcej informacji, zobacz [interfejs API przekazywania danych](/rest/api/maps/data/uploadpreview).

1. Otwórz aplikację Poster. W górnej części wybierz pozycję **Nowy**. W oknie **Tworzenie nowego** okna wybierz pozycję **Kolekcja**. Nadaj kolekcji nazwę i wybierz pozycję **Utwórz**.

2. Aby utworzyć żądanie, wybierz pozycję **nowe** ponownie. W oknie **Tworzenie nowego** okna wybierz pozycję **Żądaj**. Wprowadź **nazwę żądania** dla żądania. Wybierz kolekcję utworzoną w poprzednim kroku, a następnie wybierz pozycję **Zapisz**.

3. Wybierz metodę **post** http na karcie Konstruktor i wprowadź następujący adres URL, aby przekazać dane geofencingu do Azure Maps. W przypadku tego żądania i innych żądań wymienionych w tym artykule Zastąp `{Azure-Maps-Primary-Subscription-key}` klucz subskrypcji podstawowym.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    `geojson`Parametr w ścieżce URL reprezentuje format danych przekazywanych danych.

4. Wybierz kartę **treść** . Wybierz pozycję **RAW**, a następnie wartość **JSON** jako format wejściowy. Skopiuj i wklej następujące dane GEOJSON w obszarze tekstu **treści** :

   ```JSON
   {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13393688201903,
                  47.63829579223815
                ],
                [
                  -122.13389128446579,
                  47.63782047131512
                ],
                [
                  -122.13240802288054,
                  47.63783312249837
                ],
                [
                  -122.13238388299942,
                  47.63829037035086
                ],
                [
                  -122.13393688201903,
                  47.63829579223815
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "1"
          }
        },
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13374376296996,
                  47.63784758098976
                ],
                [
                  -122.13277012109755,
                  47.63784577367854
                ],
                [
                  -122.13314831256866,
                  47.6382813338708
                ],
                [
                  -122.1334782242775,
                  47.63827591198201
                ],
                [
                  -122.13374376296996,
                  47.63784758098976
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "2",
            "validityTime": {
            "expiredTime": "2022-01-01T00:00:00",
            "validityPeriod": [
                {
                  "startTime": "2020-07-15T16:00:00",
                  "endTime": "2020-07-15T24:00:00",
                  "recurrenceType": "Daily",
                  "recurrenceFrequency": 1,
                  "businessDayOnly": true
                }
              ]
            }
          }
        }
      ]
   }
   ```

5. Wybierz pozycję **Wyślij** i poczekaj na przetworzenie żądania. Po zakończeniu żądania przejdź do karty **nagłówki** odpowiedzi. Skopiuj wartość klucza **lokalizacji** , czyli `status URL` .

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. Aby sprawdzić stan wywołania interfejsu API, Utwórz żądanie **Get** http na stronie `status URL` . Musisz dołączyć podstawowy klucz subskrypcji do adresu URL na potrzeby uwierzytelniania. Żądanie **Get** powinno wyglądać następująco:

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Po pomyślnym zakończeniu żądania **Get** http zwraca wartość `resourceLocation` . `resourceLocation`Zawiera unikatowy `udid` dla przekazanej zawartości. Zapisz ten temat `udid` , aby wykonać zapytanie dotyczące interfejsu API Get geofencingu w ostatniej sekcji tego samouczka. Opcjonalnie możesz użyć `resourceLocation` adresu URL, aby pobrać metadane z tego zasobu w następnym kroku.

      ```json
      {
          "status": "Succeeded",
          "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
      }
      ```

8. Aby pobrać metadane zawartości, Utwórz żądanie **Get** http na `resourceLocation` adresie URL, który został pobrany w kroku 7. Pamiętaj o dołączeniu podstawowego klucza subskrypcji do adresu URL na potrzeby uwierzytelniania. Żądanie **Get** powinno wyglądać następująco:

    ```http
   https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

9. Po pomyślnym zakończeniu żądania **Get** http treść odpowiedzi będzie zawierać `udid` określony w `resourceLocation` kroku 7. Zawiera również lokalizację umożliwiającą dostęp i pobieranie zawartości w przyszłości oraz innych metadanych dotyczących zawartości. Przykładem ogólnej odpowiedzi jest:

    ```json
    {
        "udid": "{udid}",
        "location": "https://atlas.microsoft.com/mapData/{udid}?api-version=1.0",
        "created": "7/15/2020 6:11:43 PM +00:00",
        "updated": "7/15/2020 6:11:45 PM +00:00",
        "sizeInBytes": 1962,
        "uploadStatus": "Completed"
    }
    ```

## <a name="create-workflows-in-azure-logic-apps"></a>Tworzenie przepływów pracy w Azure Logic Apps

Następnie utworzysz dwa punkty końcowe [aplikacji logiki](../event-grid/handler-webhooks.md#logic-apps) , które wyzwalają powiadomienie e-mail. Poniżej przedstawiono sposób tworzenia pierwszego:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. W lewym górnym rogu Azure Portal wybierz pozycję **Utwórz zasób**.

3. W polu **Wyszukaj w witrynie Marketplace** wpisz **aplikacja logiki**.

4. Z wyników wybierz pozycję **aplikacja logiki**  >  **Utwórz**.

5. Na stronie **aplikacja logiki** wprowadź następujące wartości:
    * **Subskrypcja** , która ma być używana dla tej aplikacji logiki.
    * Nazwa **grupy zasobów** dla tej aplikacji logiki. Można **utworzyć nową** lub **użyć istniejącej** grupy zasobów.
    * **Nazwa aplikacji** logiki aplikacji logiki. W takim przypadku należy użyć `Equipment-Enter` jako nazwy.

    Na potrzeby tego samouczka Zachowaj wszystkie inne wartości w ustawieniach domyślnych.

    :::image type="content" source="./media/tutorial-geofence/logic-app-create.png" alt-text="Zrzut ekranu przedstawiający tworzenie aplikacji logiki.":::

6. Wybierz pozycję **Przejrzyj i utwórz**. Przejrzyj ustawienia i wybierz pozycję **Utwórz** , aby przesłać wdrożenie. Po pomyślnym zakończeniu wdrożenia wybierz pozycję **Przejdź do zasobu**. Nastąpi przekierowanie do **projektanta aplikacji logiki**.

7. Wybierz typ wyzwalacza. Przewiń w dół do sekcji **Rozpocznij od typowego wyzwalacza** . Wybierz **po odebraniu żądania HTTP**.

     :::image type="content" source="./media/tutorial-geofence/logic-app-trigger.png" alt-text="Zrzut ekranu przedstawiający tworzenie wyzwalacza HTTP aplikacji logiki.":::

8. W prawym górnym rogu projektanta aplikacji logiki wybierz pozycję **Zapisz**. **Adres URL post protokołu HTTP** jest generowany automatycznie. Zapisz adres URL. Jest ona potrzebna w następnej sekcji, aby utworzyć punkt końcowy zdarzenia.

    :::image type="content" source="./media/tutorial-geofence/logic-app-httprequest.png" alt-text="Zrzut ekranu przedstawiający adres URL żądania HTTP aplikacji logiki i kod JSON.":::

9. Wybierz pozycję **+ nowy krok**. Teraz wybierzesz akcję. Wpisz `outlook.com email` w polu wyszukiwania. Na liście **Akcje** przewiń w dół i wybierz pozycję **Wyślij wiadomość E-mail (wersja 2)**.
  
    :::image type="content" source="./media/tutorial-geofence/logic-app-designer.png" alt-text="Zrzut ekranu przedstawiający tworzenie projektanta aplikacji logiki.":::

10. Zaloguj się do konta programu Outlook. Upewnij się, że wybierz opcję **tak** , aby zezwolić aplikacji logiki na dostęp do konta. Wypełnij pola do wysyłania wiadomości e-mail.

    :::image type="content" source="./media/tutorial-geofence/logic-app-email.png" alt-text="Zrzut ekranu przedstawiający krok tworzenia wiadomości e-mail z aplikacji logiki.":::

    >[!TIP]
    > Możesz pobrać dane odpowiedzi GEOJSON, takie jak `geometryId` lub `deviceId` , w powiadomieniach e-mail. Logic Apps można skonfigurować w celu odczytania danych wysyłanych przez Event Grid. Aby uzyskać informacje na temat sposobu konfigurowania Logic Apps do korzystania z danych zdarzeń i przekazywania ich do powiadomień e-mail, zobacz [Samouczek: wysyłanie powiadomień e-mail dotyczących zdarzeń platformy Azure IoT Hub przy użyciu Event Grid i Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md).

11. W lewym górnym rogu projektanta aplikacji logiki wybierz pozycję **Zapisz**.

Aby utworzyć drugą aplikację logiki w celu powiadomienia Menedżera, gdy sprzęt opuszcza lokację konstrukcyjną, powtórz kroki 3-11. Nazwij aplikację logiki `Equipment-Exit` .

## <a name="create-azure-maps-events-subscriptions"></a>Tworzenie subskrypcji zdarzeń Azure Maps

Azure Maps obsługuje [trzy typy zdarzeń](../event-grid/event-schema-azure-maps.md). W tym miejscu należy utworzyć dwie różne subskrypcje zdarzeń: jeden dla zdarzeń w obszarze geoogrodzenia i jeden dla zdarzeń zakończenia geoogrodzenia.

Poniższe kroki pokazują, jak utworzyć subskrypcję zdarzeń dla zdarzeń wejścia geofencingu. Istnieje możliwość subskrybowania zdarzeń zakończenia geoogrodzenia przez powtarzanie kroków w podobny sposób.

1. Przejdź do swojego konta Azure Maps. Na pulpicie nawigacyjnym wybierz pozycję **subskrypcje**. Wybierz nazwę subskrypcji, a następnie wybierz pozycję **zdarzenia** z menu Ustawienia.

    :::image type="content" source="./media/tutorial-geofence/events-tab.png" alt-text="Zrzut ekranu przedstawiający przechodzenie do Azure Maps zdarzeń konta.":::

2. Aby utworzyć subskrypcję zdarzeń, wybierz pozycję **+ subskrypcja zdarzeń** na stronie zdarzenia.

    :::image type="content" source="./media/tutorial-geofence/create-event-subscription.png" alt-text="Zrzut ekranu przedstawiający tworzenie subskrypcji zdarzeń Azure Maps.":::

3. Na stronie **Tworzenie subskrypcji zdarzeń** wprowadź następujące wartości:
    * **Nazwa** subskrypcji zdarzeń.
    * **Schemat zdarzenia** powinien być *Event Grid schemacie*.
    * **Nazwa tematu systemu** dla tej subskrypcji zdarzeń, która w tym przypadku jest `Contoso-Construction` .
    * Dla opcji **Filtruj do typów zdarzeń** wybierz `Geofence Entered` jako typ zdarzenia.
    * W obszarze **Typ punktu końcowego** wybierz opcję `Web Hook` .
    * W przypadku **punktu końcowego** Skopiuj adres URL post protokołu HTTP dla aplikacji logiki wprowadź punkt końcowy, który został utworzony w poprzedniej sekcji. Jeśli nie pamiętasz go zapisać, możesz po prostu wrócić do projektanta aplikacji logiki i skopiować go z kroku wyzwalacza HTTP.

    :::image type="content" source="./media/tutorial-geofence/events-subscription.png" alt-text="Zrzut ekranu przedstawiający szczegóły subskrypcji zdarzeń Azure Maps.":::

4. Wybierz przycisk **Utwórz**.

Powtórz kroki 1-4 dla punktu końcowego aplikacji logiki, który został utworzony w poprzedniej sekcji. W kroku 3 Upewnij się, że wybrano opcję `Geofence Exited` jako typ zdarzenia.

## <a name="use-spatial-geofence-get-api"></a>Użyj interfejsu API pobierania przestrzennego przestrzenny

Użyj [interfejsu API pobierania przestrzennego przestrzenny](/rest/api/maps/spatial/getgeofence) do wysyłania powiadomień e-mail do programu Operations Manager, gdy w urządzeniu wchodzi lub opuszcza geoogrodzenie.

Każdy element sprzętu ma `deviceId` . W tym samouczku przedstawiono śledzenie pojedynczego urządzenia z unikatowym IDENTYFIKATORem `device_1` .

Na poniższym diagramie przedstawiono pięć lokalizacji sprzętu z upływem czasu, zaczynając od lokalizacji *początkowej* , która znajduje się poza granicami geograficznymi. Na potrzeby tego samouczka lokalizacja *początkowa* nie jest zdefiniowana, ponieważ nie można wysyłać zapytań do urządzenia w tej lokalizacji.

Podczas wykonywania zapytania dotyczącego [interfejsu API pobierania geoprzestrzennego przestrzenny](/rest/api/maps/spatial/getgeofence) z lokalizacją urządzenia wskazującą początkowy wpis geoogrodzenia lub wyjście, Event Grid wywołuje odpowiedni punkt końcowy aplikacji logiki w celu wysłania powiadomienia e-mail do programu Operations Manager.

Każda z poniższych sekcji wykonuje żądania interfejsu API przy użyciu pięciu różnych lokalizacji urządzeń.

![Diagram mapy geoogrodzenia w Azure Maps](./media/tutorial-geofence/geofence.png)

### <a name="equipment-location-1-47638237-122132483"></a>Lokalizacja sprzętu 1 (47.638237,-122,132483)

1. W górnej części aplikacji Poster wybierz pozycję **Nowy**. W oknie **Tworzenie nowego** okna wybierz pozycję **Żądaj**. Wprowadź **nazwę żądania** dla żądania. Ustaw *lokalizację 1*. Wybierz kolekcję utworzoną w [sekcji Przekaż dane GEOnotacji GEOJSON](#upload-geofencing-geojson-data), a następnie wybierz pozycję **Zapisz**.

2. Wybierz metodę **Get** http na karcie Konstruktor i wprowadź następujący adres URL. Pamiętaj, aby zamienić `{Azure-Maps-Primary-Subscription-key}` klucz subskrypcji na swój podstawowy i `{udid}` `udid` zapisany w [sekcji przekazywanie geonotacji GEOJSON](#upload-geofencing-geojson-data).

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

3. Wybierz pozycję **Wyślij**. Poniższy geoplik JSON pojawi się w oknie odpowiedzi.

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_1",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.638291,
          "nearestLon": -122.132483
        },
        {
          "deviceId": "device_1",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": 999.0,
          "nearestLat": 47.638053,
          "nearestLon": -122.13295
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ```

W poprzedniej odpowiedzi GEOJSON, ujemna odległość od geoogrodzenia lokacji głównej oznacza, że urządzenie znajduje się wewnątrz ogrodzenia. Dodatnia odległość między ogrodzeniem podwitryny oznacza, że urządzenie jest poza ogrodzeniem geograficznym. Ponieważ jest to pierwsze urządzenie znajdujące się w obrębie geoogrodzenia lokacji głównej, `isEventPublished` parametr jest ustawiany na `true` . W programie Operations Manager zostaje odebrana wiadomość e-mail z powiadomieniem, że w urządzeniu wprowadzono geoogrodzenie.

### <a name="location-2-4763800-122132531"></a>Lokalizacja 2 (47.63800,-122,132531)

1. W górnej części aplikacji Poster wybierz pozycję **Nowy**. W oknie **Tworzenie nowego** okna wybierz pozycję **Żądaj**. Wprowadź **nazwę żądania** dla żądania. Ustaw *lokalizację 2*. Wybierz kolekcję utworzoną w [sekcji Przekaż dane GEOnotacji GEOJSON](#upload-geofencing-geojson-data), a następnie wybierz pozycję **Zapisz**.

2. Wybierz metodę **Get** http na karcie Konstruktor i wprowadź następujący adres URL. Pamiętaj, aby zamienić `{Azure-Maps-Primary-Subscription-key}` klucz subskrypcji na swój podstawowy i `{udid}` `udid` zapisany w [sekcji przekazywanie geonotacji GEOJSON](#upload-geofencing-geojson-data).

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

3. Wybierz pozycję **Wyślij**. Poniższy GEOJSON zostanie wyświetlony w oknie odpowiedzi:

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.637997,
          "nearestLon": -122.132399
        },
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": 999.0,
          "nearestLat": 47.63789,
          "nearestLon": -122.132809
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": false
    }
    ````

W powyższej odpowiedzi GEOJSON urządzenie pozostawało w głównym obszarze geograficznym i nie zostało wprowadzone w ramach podwitryny podrzędnej. W związku z tym `isEventPublished` parametr jest ustawiany na `false` , a program Operations Manager nie odbiera żadnych powiadomień e-mail.

### <a name="location-3-4763810783315048-12213336020708084"></a>Lokalizacja 3 (47.63810783315048,-122.13336020708084)

1. W górnej części aplikacji Poster wybierz pozycję **Nowy**. W oknie **Tworzenie nowego** okna wybierz pozycję **Żądaj**. Wprowadź **nazwę żądania** dla żądania. Utwórz *lokalizację 3*. Wybierz kolekcję utworzoną w [sekcji Przekaż dane GEOnotacji GEOJSON](#upload-geofencing-geojson-data), a następnie wybierz pozycję **Zapisz**.

2. Wybierz metodę **Get** http na karcie Konstruktor i wprowadź następujący adres URL. Pamiętaj, aby zamienić `{Azure-Maps-Primary-Subscription-key}` klucz subskrypcji na swój podstawowy i `{udid}` `udid` zapisany w [sekcji przekazywanie geonotacji GEOJSON](#upload-geofencing-geojson-data).

    ```HTTP
      https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
      ```

3. Wybierz pozycję **Wyślij**. Poniższy GEOJSON zostanie wyświetlony w oknie odpowiedzi:

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.638294,
          "nearestLon": -122.133359
        },
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": -999.0,
          "nearestLat": 47.638161,
          "nearestLon": -122.133549
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ````

W powyższej odpowiedzi GEOJSON urządzenie pozostawało w głównym ogrodzeniu lokacji, ale podano w nim granicę geolokacja. W związku z tym `isEventPublished` parametr jest ustawiany na `true` . W programie Operations Manager zostaje odebrana wiadomość e-mail z informacją o tym, że urządzenie przeszedł geoogrodzenie.

>[!NOTE]
>Jeśli sprzęt został przeniesiony do lokacji podrzędnej po godzinach pracy, żadne zdarzenie nie zostanie opublikowane, a program Operations Manager nie otrzyma żadnych powiadomień.  

### <a name="location-4-47637988-1221338344"></a>Lokalizacja 4 (47.637988,-122,1338344)

1. W górnej części aplikacji Poster wybierz pozycję **Nowy**. W oknie **Tworzenie nowego** okna wybierz pozycję **Żądaj**. Wprowadź **nazwę żądania** dla żądania. Przekształć w *lokalizację 4*. Wybierz kolekcję utworzoną w [sekcji Przekaż dane GEOnotacji GEOJSON](#upload-geofencing-geojson-data), a następnie wybierz pozycję **Zapisz**.

2. Wybierz metodę **Get** http na karcie Konstruktor i wprowadź następujący adres URL. Pamiętaj, aby zamienić `{Azure-Maps-Primary-Subscription-key}` klucz subskrypcji na swój podstawowy i `{udid}` `udid` zapisany w [sekcji przekazywanie geonotacji GEOJSON](#upload-geofencing-geojson-data).

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.637988&userTime=2023-01-16&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

3. Wybierz pozycję **Wyślij**. Poniższy GEOJSON zostanie wyświetlony w oknie odpowiedzi:

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.637985,
          "nearestLon": -122.133907
        }
      ],
      "expiredGeofenceGeometryId": [
        "2"
      ],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": false
    }
    ````

W powyższej odpowiedzi GEOJSON sprzęt pozostawał w głównym ogrodzeniu lokacji, ale zakończył granicę geolokacji. Należy jednak zauważyć, że `userTime` wartość jest po wartości `expiredTime` zdefiniowanej w danych geoogrodzenia. W związku z tym `isEventPublished` parametr jest ustawiany na `false` , a program Operations Manager nie otrzymuje powiadomienia e-mail.

### <a name="location-5-4763799--122134505"></a>Lokalizacja 5 (47,63799,-122,134505)

1. W górnej części aplikacji Poster wybierz pozycję **Nowy**. W oknie **Tworzenie nowego** okna wybierz pozycję **Żądaj**. Wprowadź **nazwę żądania** dla żądania. Ustaw *lokalizację na 5*. Wybierz kolekcję utworzoną w [sekcji Przekaż dane GEOnotacji GEOJSON](#upload-geofencing-geojson-data), a następnie wybierz pozycję **Zapisz**.

2. Wybierz metodę **Get** http na karcie Konstruktor i wprowadź następujący adres URL. Pamiętaj, aby zamienić `{Azure-Maps-Primary-Subscription-key}` klucz subskrypcji na swój podstawowy i `{udid}` `udid` zapisany w [sekcji przekazywanie geonotacji GEOJSON](#upload-geofencing-geojson-data).

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.63799&lon=-122.134505&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

3. Wybierz pozycję **Wyślij**. Poniższy GEOJSON zostanie wyświetlony w oknie odpowiedzi:

    ```json
    {
      "geometries": [
      {
        "deviceId": "device_01",
        "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
        "geometryId": "1",
        "distance": -999.0,
        "nearestLat": 47.637985,
        "nearestLon": -122.133907
      },
      {
        "deviceId": "device_01",
        "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
        "geometryId": "2",
        "distance": 999.0,
        "nearestLat": 47.637945,
        "nearestLon": -122.133683
      }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ````

W powyższej odpowiedzi GEOJSON sprzęt zakończył geoogrodzenie lokacji głównej. W związku z tym `isEventPublished` parametr jest ustawiany na `true` , a program Operations Manager odbiera powiadomienie e-mail z informacją o tym, że urządzenie zakończyło geoogrodzenie.


[Powiadomienia e-mail można także wysyłać przy użyciu Event Grid i Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md) i sprawdzić [obsługiwane programy obsługi zdarzeń w Event Grid](../event-grid/event-handlers.md) przy użyciu Azure Maps.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Brak zasobów, które wymagają oczyszczenia.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Obsługa typów zawartości w Azure Logic Apps](../logic-apps/logic-apps-content-type.md)