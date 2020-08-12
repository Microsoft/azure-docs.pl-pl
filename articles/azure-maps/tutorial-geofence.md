---
title: 'Samouczek: Tworzenie geoogrodzenia i śledzenie urządzeń na mapie Microsoft Azure'
description: Dowiedz się, jak skonfigurować geoogrodzenie. Zobacz, jak śledzić urządzenia względem geoogrodzenia przy użyciu usługi przestrzennej Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 8/11/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: b88d9132ec1548c9d94fc418af35b55ac2836e96
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121242"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>Samouczek: Konfigurowanie geofencingu przy użyciu usługi Azure Maps

Ten samouczek przeprowadzi Cię przez podstawowe informacje na temat tworzenia i używania usług Azure Maps geoogrodzenia w kontekście następującego scenariusza:

*Konstrukcja Site Manager musi śledzić sprzęt w miarę wejścia i opuszczania obwodów w ramach budowy. Gdy sprzęt opuszcza lub wprowadza te obwody, do Operations Manager zostanie wysłana wiadomość e-mail z powiadomieniem.*

Azure Maps udostępnia wiele usług, które obsługują śledzenie sprzętu i opuszczanie obszaru budowy w powyższym scenariuszu. W tym samouczku omówiono sposób wykonywania tych zadań:

> [!div class="checklist"]
> * Przekazuj [dane GEOnotacji GEOJSON](geofence-geojson.md) , które definiują obszary budowy, które chcemy monitorować. Użyjemy [interfejsu API przekazywania danych](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) , aby przekazać geoogrodzenie jako współrzędne wielokąta do konta Azure Maps.
> * Skonfiguruj dwie [Aplikacje logiki](https://docs.microsoft.com/azure/event-grid/handler-webhooks#logic-apps) , które po wyzwoleniu wyśle powiadomienia e-mail do lokacji budownictwa Operations Manager, gdy sprzęt wejdzie i opuszcza obszar geoogrodzenia.
> * Użyj [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) , aby subskrybować Azure Maps geoogrodzenia zdarzenia wejścia i wyjścia. Skonfigurujemy dwie subskrypcje zdarzeń elementu webhook, które będą wywoływać punkty końcowe HTTP zdefiniowane w dwóch Logic Apps. Logic Apps następnie wyśle odpowiednie powiadomienia e-mail dotyczące sprzętu przechodzącego poza lub wprowadzającego geoogrodzenie.
> * Użyj [interfejsu API wyszukiwania geoogrodzenia Szukaj](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence) , aby otrzymywać powiadomienia, gdy sprzęt zostanie zakończony i przejdzie do obszaru geoogrodzenia.

## <a name="prerequisites"></a>Wymagania wstępne

1. [Utwórz konto Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Uzyskaj podstawowy klucz subskrypcji](quick-demo-map-app.md#get-the-primary-key-for-your-account), nazywany także kluczem podstawowym lub kluczem subskrypcji.

W tym samouczku jest stosowana aplikacja programu [Poster](https://www.postman.com/) , ale można wybrać inne środowisko deweloperskie interfejsu API.

## <a name="upload-geofencing-geojson-data"></a>Przekazywanie geogeofencingu danych GEOJSON

W tym samouczku przekażemy geoplikowe dane GEOJSON, które zawierają `FeatureCollection` . `FeatureCollection`Zawiera dwa geoogrodzenia, które definiują obszary wielokąta w obrębie lokacji konstrukcja. Pierwszy geoogrodzenie nie ma czasu wygaśnięcia ani ograniczeń. Zapytanie drugi można wykonać tylko w godzinach pracy (9-5 P.M. PST) i nie będzie już obowiązywać po 1 stycznia 2022. Aby uzyskać więcej informacji na temat formatu GEOJSON, zobacz geovisioning [danych GEOJSON](geofence-geojson.md).

>[!TIP]
>W dowolnym momencie możesz zaktualizować dane dotyczące geofencingu. Aby uzyskać więcej informacji na temat aktualizowania danych, zobacz [interfejs API przekazywania danych](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)

1. Otwórz aplikację Poster. W górnej części aplikacji Poster wybierz pozycję **Nowy**. W oknie **Tworzenie nowego** okna wybierz pozycję **Kolekcja**.  Nadaj kolekcji nazwę i wybierz przycisk **Utwórz** .

2. Aby utworzyć żądanie, wybierz pozycję **nowe** ponownie. W oknie **Tworzenie nowego** okna wybierz pozycję **Żądaj**. Wprowadź **nazwę żądania** dla żądania. Wybierz kolekcję utworzoną w poprzednim kroku, a następnie wybierz pozycję **Zapisz**.

3. Wybierz metodę **post** protokołu HTTP na karcie Konstruktor i wprowadź następujący adres URL, aby przekazać dane dotyczące geofencingu do usługi Azure Maps. W przypadku tego żądania i innych żądań wymienionych w tym artykule Zastąp `{Azure-Maps-Primary-Subscription-key}` klucz subskrypcji podstawowym.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    Parametr _GEOJSON_ w ścieżce URL reprezentuje format danych przekazywanych danych.

4. Kliknij kartę **treść** **, a następnie pozycję** **JSON** jako format wejściowy. Skopiuj i wklej następujące dane GEOJSON w obszarze tekstu **treści** :

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

5. Kliknij niebieski przycisk **Wyślij** i poczekaj na przetworzenie żądania. Po zakończeniu żądania przejdź na kartę **nagłówki** odpowiedzi. Skopiuj wartość klucza **lokalizacji** , czyli `status URL` .

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. Aby sprawdzić stan wywołania interfejsu API, Utwórz żądanie **Get** http na stronie `status URL` . Musisz dołączyć podstawowy klucz subskrypcji do adresu URL na potrzeby uwierzytelniania. Żądanie **Get** powinno wyglądać następująco:

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Pomyślne zakończenie żądania **Get** http spowoduje zwrócenie elementu `resourceLocation` . `resourceLocation`Zawiera unikatowy `udid` dla przekazanej zawartości. Musisz zapisać ten temat `udid` , aby wykonać zapytanie dotyczące interfejsu API Get geofencingu w ostatniej sekcji tego samouczka. Opcjonalnie możesz użyć `resourceLocation` adresu URL, aby pobrać metadane z tego zasobu w następnym kroku.

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

9. Po pomyślnym zakończeniu żądania **Get** http treść odpowiedzi będzie zawierać `udid` określone w `resourceLocation` kroku 7, lokalizację dostępu/pobrać zawartość w przyszłości oraz inne metadane dotyczące zawartości, takie jak Data utworzenia/zaktualizowana, rozmiar itd. Przykładem ogólnej odpowiedzi jest:

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

## <a name="create-logic-app-workflows"></a>Tworzenie przepływów pracy aplikacji logiki

W tej sekcji utworzymy dwa punkty końcowe [aplikacji logiki](https://docs.microsoft.com/azure/event-grid/handler-webhooks#logic-apps) , które będą wyzwalać powiadomienie e-mail. Pokażemy, jak utworzyć pierwszy wyzwalacz, który wyśle powiadomienia e-mail przy każdym wywołaniu punktu końcowego.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

2. W lewym górnym rogu witryny [Azure Portal](https://portal.azure.com) kliknij przycisk **Utwórz zasób**.

3. W polu *Wyszukaj w witrynie Marketplace* wpisz **aplikacja logiki**.

4. Z *wyników*wybierz pozycję **aplikacja logiki**. Kliknij przycisk **Utwórz** .

5. Na stronie **aplikacja logiki** wprowadź następujące wartości:
    * *Subskrypcja* , która ma być używana dla tej aplikacji logiki.
    * Nazwa *grupy zasobów* dla tej aplikacji logiki. Można wybrać pozycję *Utwórz nowe* lub *Użyj istniejącego* dla grupy zasobów.
    * *Nazwa aplikacji* logiki aplikacji logiki. W takim przypadku użyjemy `Equipment-Enter` jako nazwy.

    Na potrzeby tego samouczka Zachowaj pozostałe wartości w ustawieniach domyślnych.

    :::image type="content" source="./media/tutorial-geofence/logic-app-create.png" alt-text="Tworzenie aplikacji logiki":::

6. Kliknij przycisk **Recenzja + Utwórz** . Przejrzyj ustawienia i kliknij przycisk **Utwórz** , aby przesłać wdrożenie. Po pomyślnym zakończeniu wdrożenia kliknij pozycję **Przejdź do zasobu**. Nastąpi przekierowanie do **projektanta aplikacji logiki**

7. Teraz wybierzemy typ wyzwalacza. Przewiń w dół do sekcji *Rozpocznij ze wspólnym wyzwalaczem**. Kliknij pozycję **po odebraniu żądania HTTP**.

     :::image type="content" source="./media/tutorial-geofence/logic-app-trigger.png" alt-text="Tworzenie wyzwalacza HTTP aplikacji logiki":::

8. Kliknij przycisk **Zapisz** w prawym górnym rogu projektanta. **Adres URL post protokołu HTTP** zostanie wygenerowany automatycznie. Zapisz adres URL, ponieważ będzie on potrzebny w następnej sekcji, aby utworzyć punkt końcowy zdarzenia.

    :::image type="content" source="./media/tutorial-geofence/logic-app-httprequest.png" alt-text="Adres URL żądania HTTP aplikacji logiki i kod JSON":::

9. Wybierz pozycję **+ nowy krok**. Teraz wybierzemy akcję. Wpisz `outlook.com email` w polu wyszukiwania. Na liście **Akcje** przewiń w dół i kliknij pozycję **Wyślij wiadomość E-mail (wersja 2)**.
  
    :::image type="content" source="./media/tutorial-geofence/logic-app-designer.png" alt-text="Tworzenie projektanta aplikacji logiki":::

10. Zaloguj się do konta Outlook.com. Upewnij się, że klikniesz przycisk **tak** , aby umożliwić aplikacji logiki dostęp do konta. Wypełnij pola do wysyłania wiadomości e-mail.

    :::image type="content" source="./media/tutorial-geofence/logic-app-email.png" alt-text="Tworzenie kroku wysyłania wiadomości e-mail aplikacji logiki":::

    >[!TIP]
    > Możesz pobrać dane odpowiedzi GEOJSON, takie jak `geometryId` lub `deviceId` w powiadomieniach e-mail przez skonfigurowanie aplikacji logiki w celu odczytania danych wysyłanych przez Event Grid. Aby uzyskać informacje dotyczące sposobu konfigurowania aplikacji logiki do korzystania z danych zdarzeń i przekazywania ich do powiadomień e-mail, zobacz [Samouczek: wysyłanie powiadomień e-mail dotyczących zdarzeń platformy Azure IoT Hub przy użyciu Event Grid i Logic Apps](https://docs.microsoft.com/azure/event-grid/publish-iot-hub-events-to-logic-apps).

11. Kliknij przycisk **Zapisz** w lewym górnym rogu projektanta Logic Apps.

12. Powtórz kroki 3-11, aby utworzyć drugą aplikację logiki w celu powiadomienia Menedżera, gdy sprzęt opuszcza lokację konstrukcyjną. Nazwij aplikację logiki `Equipment-Exit` .

## <a name="create-azure-maps-events-subscriptions"></a>Tworzenie subskrypcji zdarzeń Azure Maps

Usługa Azure Maps obsługuje trzy typy zdarzeń. Obsługiwanym typom zdarzeń usługi Azure Maps można przyjrzeć się [tutaj](https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps).  Musimy utworzyć dwie różne subskrypcje zdarzeń: jeden dla geofencingu wprowadź zdarzenia i jeden dla zdarzeń zakończenia geoogrodzenia.

Wykonaj poniższe kroki, aby utworzyć subskrypcję dla zdarzeń wjazdu wirtualnego ogrodzenia. Istnieje możliwość subskrybowania zdarzeń zakończenia geoogrodzenia przez powtarzanie kroków w podobny sposób.

1. Przejdź do konta Azure Maps. Na pulpicie nawigacyjnym wybierz pozycję **subskrypcje**. Kliknij nazwę subskrypcji i wybierz pozycję **zdarzenia** z menu Ustawienia.

    :::image type="content" source="./media/tutorial-geofence/events-tab.png" alt-text="Przejdź do Azure Maps zdarzeń konta":::

2. Aby utworzyć subskrypcję zdarzeń, wybierz pozycję **+ subskrypcja zdarzeń** na stronie zdarzenia.

    :::image type="content" source="./media/tutorial-geofence/create-event-subscription.png" alt-text="Tworzenie subskrypcji zdarzeń usługi Azure Maps":::

3. Na stronie **Tworzenie subskrypcji zdarzeń** wprowadź następujące wartości:
    * *Nazwa* subskrypcji zdarzeń.
    * *Schemat zdarzenia* powinien być *Event Grid schemacie*.
    * *Nazwa tematu systemu* dla tej subskrypcji zdarzeń. W takim przypadku będziemy używać `Contoso-Construction` .
    * Na stronie *Filtruj do typów zdarzeń*wybierz `Geofence Entered` jako typ zdarzenia.
    * Dla *typu punktu końcowego*wybierz opcję `Web Hook` .
    * W przypadku *punktu końcowego*Skopiuj adres URL post protokołu HTTP dla aplikacji logiki wprowadź punkt końcowy, który został utworzony w poprzedniej sekcji. Jeśli nie pamiętasz go zapisać, możesz po prostu wrócić do projektanta aplikacji logiki i skopiować go z kroku wyzwalacza HTTP.

    :::image type="content" source="./media/tutorial-geofence/events-subscription.png" alt-text="Szczegóły subskrypcji zdarzeń Azure Maps":::

4. Kliknij przycisk **Utwórz**.

5. Powtórz kroki 1-4 dla punktu końcowego aplikacji logiki, który został utworzony w poprzedniej sekcji. W kroku 3 Upewnij się, że wybrano opcję `Geofence Exited` jako typ zdarzenia.

## <a name="use-search-geofence-get-api"></a>Użyj interfejsu API pobierania geoogrodzenia wyszukiwania

Teraz użyjemy [interfejsu API "wyszukiwanie geoogrodzenia](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence) " do wysyłania powiadomień e-mail do Operations Manager, gdy sprzęt przejdzie lub wyjdzie z geoogrodzenia.

Każdy sprzęt ma `deviceId` . W tym samouczku będziemy monitorować pojedynczy fragment sprzętu, którego unikatowy identyfikator to `device_1` .

Na poniższym diagramie przedstawiono pięć lokalizacji wyposażenia w czasie, rozpoczynając od lokalizacji *początkowej* , która znajduje się poza granicami geograficznymi. Na potrzeby tego samouczka lokalizacja *początkowa* nie jest zdefiniowana, ponieważ nie będziemy wysyłać zapytań do urządzenia w tej lokalizacji.

Podczas wykonywania zapytania dotyczącego [interfejsu API pobierania geoogrodzenia wyszukiwania](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence) przy użyciu lokalizacji urządzenia, która wskazuje początkowy wpis geoogrodzenia lub wyjście, Event Grid wywoła odpowiedni punkt końcowy aplikacji logiki w celu wysłania powiadomienia e-mail do Operations Manager.

Każda z poniższych sekcji sprawia, że HTTP GET geofencingu żądania interfejsu API przy użyciu pięciu różnych lokalizacji urządzeń.

![Mapa geoogrodzenia w Azure Maps](./media/tutorial-geofence/geofence.png)

### <a name="equipment-location-1-47638237-122132483"></a>Lokalizacja sprzętu 1 (47.638237,-122,132483)

1. W górnej części aplikacji Poster wybierz pozycję **Nowy**. W oknie **Tworzenie nowego** okna wybierz pozycję **Żądaj**.  Wprowadź **nazwę żądania** dla żądania. Użyjemy nazwy, *lokalizacji 1*. Wybierz kolekcję utworzoną w [sekcji Przekaż dane GEOnotacji GEOJSON](#upload-geofencing-geojson-data), a następnie wybierz pozycję **Zapisz**.

2. Wybierz metodę **Get** http na karcie Konstruktor i wprowadź następujący adres URL, pamiętaj, aby zastąpić `{Azure-Maps-Primary-Subscription-key}` klucz subskrypcji podstawowy i `{udid}` `udid` zapisany w [sekcji przekazywanie geonotacji GEOJSON](#upload-geofencing-geojson-data).

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

3. Kliknij przycisk **Wyślij** . Poniższy geoplik JSON pojawi się w oknie odpowiedzi.

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

4. W powyższej odpowiedzi GEOJSON, ujemna odległość od geoogrodzenia lokacji głównej oznacza, że sprzęt znajduje się wewnątrz ogrodzenia. Dodatnia odległość od ogrodzenia podrzędnego oznacza, że urządzenie znajduje się poza ogrodzeniem geograficznym. Ponieważ jest to pierwsze urządzenie znajdujące się w obrębie geoogrodzenia lokacji głównej, `isEventPublished` parametr jest ustawiany na `true` , a Operations Manager będzie otrzymywał powiadomienie e-mail, że w urządzeniu wprowadzono geoogrodzenie.

### <a name="location-2-4763800-122132531"></a>Lokalizacja 2 (47.63800,-122,132531)

1. W górnej części aplikacji Poster wybierz pozycję **Nowy**. W oknie **Tworzenie nowego** okna wybierz pozycję **Żądaj**.  Wprowadź **nazwę żądania** dla żądania. Użyjemy nazwy, *lokalizacji 2*. Wybierz kolekcję utworzoną w [sekcji Przekaż dane GEOnotacji GEOJSON](#upload-geofencing-geojson-data), a następnie wybierz pozycję **Zapisz**.

2. Wybierz metodę **Get** http na karcie Konstruktor i wprowadź następujący adres URL, pamiętaj, aby zastąpić `{Azure-Maps-Primary-Subscription-key}` klucz subskrypcji podstawowy i `{udid}` `udid` zapisany w [sekcji przekazywanie geonotacji GEOJSON](#upload-geofencing-geojson-data).

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

3. Kliknij przycisk **Wyślij** . Następujący geoplik JSON pojawi się w oknie odpowiedzi:

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

4. W powyższej odpowiedzi GEOJSON urządzenie pozostawało w głównym obszarze geograficznym i nie zostało wprowadzone w ramach podwitryny podrzędnej. W związku z tym `isEventPublished` parametr jest ustawiany na, `false` a Operations Manager nie odbiera żadnych powiadomień e-mail.

### <a name="location-3-4763810783315048-12213336020708084"></a>Lokalizacja 3 (47.63810783315048,-122.13336020708084)

1. W górnej części aplikacji Poster wybierz pozycję **Nowy**. W oknie **Tworzenie nowego** okna wybierz pozycję **Żądaj**.  Wprowadź **nazwę żądania** dla żądania. Użyjemy nazwy, *lokalizacji 3*. Wybierz kolekcję utworzoną w [sekcji Przekaż dane GEOnotacji GEOJSON](#upload-geofencing-geojson-data), a następnie wybierz pozycję **Zapisz**.

2. Wybierz metodę **Get** http na karcie Konstruktor i wprowadź następujący adres URL, pamiętaj, aby zastąpić `{Azure-Maps-Primary-Subscription-key}` klucz subskrypcji podstawowy i `{udid}` `udid` zapisany w [sekcji przekazywanie geonotacji GEOJSON](#upload-geofencing-geojson-data).

    ```HTTP
      https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
      ```

3. Kliknij przycisk **Wyślij** . Następujący geoplik JSON pojawi się w oknie odpowiedzi:

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

4. W powyższej odpowiedzi GEOJSON urządzenie pozostawało w głównym ogrodzeniu lokacji, ale podano geoogrodzenie podrzędne. W związku z tym `isEventPublished` parametr jest ustawiany na, `true` a Operations Manager otrzyma powiadomienie e-mail z informacją o tym, że urządzenie przeszedł geoogrodzenie.

    >[!NOTE]
    >Jeśli sprzęt został przeniesiony do lokacji podrzędnej po godzinach pracy, żadne zdarzenie nie zostanie opublikowane, a Operations Manager nie otrzyma żadnych powiadomień.  

### <a name="location-4-47637988-1221338344"></a>Lokalizacja 4 (47.637988,-122,1338344)

1. W górnej części aplikacji Poster wybierz pozycję **Nowy**. W oknie **Tworzenie nowego** okna wybierz pozycję **Żądaj**.  Wprowadź **nazwę żądania** dla żądania. Użyjemy nazwy, *lokalizacji 4*. Wybierz kolekcję utworzoną w [sekcji Przekaż dane GEOnotacji GEOJSON](#upload-geofencing-geojson-data), a następnie wybierz pozycję **Zapisz**.

2. Wybierz metodę **Get** http na karcie Konstruktor i wprowadź następujący adres URL, pamiętaj, aby zastąpić `{Azure-Maps-Primary-Subscription-key}` klucz subskrypcji podstawowy i `{udid}` `udid` zapisany w [sekcji przekazywanie geonotacji GEOJSON](#upload-geofencing-geojson-data).

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.637988&userTime=2023-01-16&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

3. Kliknij przycisk **Wyślij** . Następujący geoplik JSON pojawi się w oknie odpowiedzi:

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

4. W powyższej odpowiedzi GEOJSON urządzenie pozostawało w głównym ogrodzeniu lokacji, ale zakończyło się geoogrodzeniem podrzędnego. Jeśli jednak zauważysz, `userTime` wartość jest po wartości `expiredTime` zdefiniowanej w danych geoogrodzenia. W związku z tym `isEventPublished` parametr jest ustawiany na, `false` a Operations Manager nie otrzyma powiadomienia e-mail.

### <a name="location-547637988-1221338344"></a>Lokalizacja 5 (47.637988,-122,1338344)

1. W górnej części aplikacji Poster wybierz pozycję **Nowy**. W oknie **Tworzenie nowego** okna wybierz pozycję **Żądaj**.  Wprowadź **nazwę żądania** dla żądania. Użyjemy nazwy, *lokalizacji 4*. Wybierz kolekcję utworzoną w [sekcji Przekaż dane GEOnotacji GEOJSON](#upload-geofencing-geojson-data), a następnie wybierz pozycję **Zapisz**.

2. Wybierz metodę **Get** http na karcie Konstruktor i wprowadź następujący adres URL, pamiętaj, aby zastąpić `{Azure-Maps-Primary-Subscription-key}` klucz subskrypcji podstawowy i `{udid}` `udid` zapisany w [sekcji przekazywanie geonotacji GEOJSON](#upload-geofencing-geojson-data).

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

3. Kliknij przycisk **Wyślij** . Następujący geoplik JSON pojawi się w oknie odpowiedzi:

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

4. W powyższej odpowiedzi GEOJSON sprzęt zakończył geoogrodzenie lokacji głównej. W związku z tym `isEventPublished` parametr jest ustawiany na, `true` a Operations Manager otrzyma powiadomienie e-mail z informacją o tym, że urządzenie zakończyło geoogrodzenie.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Obsługa typów zawartości w Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type)

> [!div class="nextstepaction"]
> [Wysyłaj powiadomienia e-mail przy użyciu Event Grid i Logic Apps](https://docs.microsoft.com/azure/event-grid/publish-iot-hub-events-to-logic-apps)

> [!div class="nextstepaction"]
> [Obsługiwane programy obsługi zdarzeń w Event Grid](https://docs.microsoft.com/azure/event-grid/event-handlers).
