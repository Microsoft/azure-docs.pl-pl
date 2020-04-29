---
title: 'Samouczek: Tworzenie geoogrodzenia i śledzenie urządzeń na mapie | Mapy Microsoft Azure'
description: Dowiedz się, jak skonfigurować geoogrodzenie i śledzić urządzenia względem geofencingu przy użyciu usługi Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 1/15/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 126829f12d71e40511c26e781cb191988c1d031e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80333868"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>Samouczek: Konfigurowanie geoogrodzenia przy użyciu Azure Maps

Ten samouczek przeprowadzi Cię przez podstawowe kroki konfigurowania geofencingu przy użyciu usługi Azure Maps. Rozważmy ten scenariusz, Site Manager konstrukcja musi monitorować potencjalne niebezpieczne urządzenia. Menedżer musi zapewnić, że urządzenie pozostanie w wybranych ogólnych obszarach konstrukcyjnych. Ten ogólny obszar konstrukcyjny jest twardym parametrem. Regulacje wymagają sprzętu do pozostania w tym parametrze, a naruszenia są zgłaszane do Operations Manager.  

Używamy interfejsu API przekazywania danych do przechowywania geoogrodzenia i używania interfejsu API geoogrodzenia do sprawdzenia lokalizacji sprzętu względem geoogrodzenia. Interfejs API przekazywania danych i interfejs API geoogrodzenia są z Azure Maps. Używamy również Azure Event Grid do przesyłania strumieniowego wyników geoogrodzenia i konfigurowania powiadomienia na podstawie wyników geoogrodzenia. Aby dowiedzieć się więcej na temat usługi Event Grid, zobacz [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview).

W tym samouczku omówiono sposób wykonywania tych zadań:

> [!div class="checklist"]
> * Przekazywanie obszaru geofencingu w usłudze Azure Maps — usłudze danych przy użyciu interfejsu API przekazywania danych.
> *   Konfigurowanie usługi Event Grid na potrzeby obsługi zdarzeń geofencingu.
> *   Konfigurowanie obsługi zdarzeń geofencingu.
> *   Konfigurowanie alertów w odpowiedzi na zdarzenia geofencingu przy użyciu aplikacji logiki.
> *   Śledzenie przy użyciu interfejsów API usługi geofencingu usługi Azure Maps, czy zasób budowlany znajduje się na terenie budowy.


## <a name="prerequisites"></a>Wymagania wstępne

### <a name="create-an-azure-maps-account"></a>Tworzenie konta usługi Azure Maps 

Postępuj zgodnie z instrukcjami w temacie [Tworzenie konta](quick-demo-map-app.md#create-an-account-with-azure-maps) , aby utworzyć subskrypcję konta Azure Maps przy użyciu warstwy cenowej S1. Kroki przedstawione w sekcji [Pobieranie klucza podstawowego](quick-demo-map-app.md#get-the-primary-key-for-your-account) pokazują, jak pobrać klucz podstawowy konta. Aby uzyskać więcej informacji na temat uwierzytelniania w Azure Maps, zobacz [Zarządzanie uwierzytelnianiem w programie Azure Maps](./how-to-manage-authentication.md).

## <a name="upload-geofences"></a>Przekazywanie geofencingu

Przyjęto założenie, że główny element geoogrodzenia jest subsite1, który ma ustawiony czas wygaśnięcia. W zależności od potrzeb możesz utworzyć więcej zagnieżdżonych wirtualnych ogrodzeń. Te zestawy ograniczników mogą służyć do śledzenia różnych obszarów konstrukcyjnych w ramach ogólnego obszaru budowy. Na przykład subsite1 może być miejscem pracy w tygodniu od 1 do 4 harmonogramu. subsite2 może być miejscem pracy w tygodniu od 5 do 7. Wszystkie takie horyzonty mogą być ładowane jako jeden zestaw danych na początku projektu. Te horyzonty są używane do śledzenia reguł na podstawie czasu i miejsca. 

Aby przekazać geoogrodzenie dla lokacji budownictwa przy użyciu interfejsu API przekazywania danych, używamy aplikacji do publikowania. Zainstaluj [aplikację Poster](https://www.getpostman.com/) i utwórz bezpłatne konto. 

Po zainstalowaniu aplikacji Poster wykonaj następujące kroki, aby przekazać wartość geoogrodzenia konstrukcji, korzystając z Azure Maps, interfejsu API przekazywania danych.

1. Otwórz aplikację Postman i kliknij pozycję New | Create new (Nowe | Utwórz nowe), a następnie wybierz pozycję Request (Żądanie). Wprowadź nazwę żądania na potrzeby przekazywania danych geofencing, wybierz docelową kolekcję lub folder zapisywania, a następnie kliknij pozycję Save (Zapisz).

    ![Przekazywanie geofencingu przy użyciu narzędzia Postman](./media/tutorial-geofence/postman-new.png)

2. Na karcie konstruktora wybierz metodę POST HTTP i wprowadź poniższy adres URL, aby utworzyć żądanie POST.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    Parametr GEOJSON w ścieżce adresu URL reprezentuje format przekazywanych danych.

3. Kliknij pozycję **Params** (Parametry), a następnie wprowadź poniższą parę klucz-wartość, która będzie używana w przypadku adresu URL żądania POST. Zastąp ciąg {Subscription-Key} kluczem subskrypcji Azure Maps, znanym również jako klucz podstawowy.
   
    ![Parametry przekazywania danych (geoogrodzenia) w programie Poster](./media/tutorial-geofence/postman-key-vals.png)

4. Kliknij pozycję **treść** , a następnie wybierz format danych wejściowych RAW i wybierz pozycję JSON jako format danych wejściowych z listy rozwijanej. Jako dane do przekazania podaj następujące dane JSON:

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
              "expiredTime": "2019-01-15T00:00:00",
              "validityPeriod": [
                {
                  "startTime": "2019-01-08T01:00:00",
                  "endTime": "2019-01-08T17:00:00",
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

5. Kliknij przycisk wysyłania i przejrzyj nagłówek odpowiedzi. Po pomyślnym żądaniu nagłówek **lokalizacji** będzie zawierać identyfikator URI stanu. Identyfikator URI stanu ma następujący format. Wartość uploadStatusId nie należy do zakresu od {}. Typowym sposobem użycia elementu {} jest wyświetlenie wartości, które użytkownik musi wprowadzić, lub wartości, które różnią się w zależności od użytkownika.

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. Skopiuj identyfikator URI stanu i Dołącz klucz subskrypcji. Format identyfikatora URI stanu powinien wyglądać podobnie do przedstawionego poniżej. Zwróć uwagę, że w formacie poniżej należy zmienić {Subscription-Key}, w tym {}, z kluczem subskrypcji.

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Aby uzyskać dostęp `udId`do programu, Otwórz nową kartę w aplikacji Poster i wybierz pozycję Pobierz metodę http na karcie Konstruktor. Wprowadź żądanie Get w identyfikatorze URI stanu z poprzedniego kroku. Jeśli przekazywanie danych zakończyło się pomyślnie, otrzymasz udId w treści odpowiedzi. Skopiuj udId do późniejszego użycia.

   ```JSON
   {
    "status": "Succeeded",
    "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udId}?api-version=1.0"
   }
   ```

## <a name="set-up-an-event-handler"></a>Konfigurowanie programu obsługi zdarzeń

W tej sekcji utworzymy procedurę obsługi zdarzeń, która odbiera powiadomienia. Ta procedura obsługi zdarzeń powinna powiadamiać Operations Manager o zdarzeniach wejścia i wyjścia dowolnego sprzętu.

Firma Microsoft udostępnia dwie [Logic Apps](https://docs.microsoft.com/azure/event-grid/event-handlers#logic-apps) usług do obsługi zdarzeń wejścia i wyjścia. Gdy zdarzenia w wyzwalaczu Logic Apps, więcej zdarzeń wyzwalacza w sekwencji. Pomysłem jest wysłanie alertów, w tym przypadku wiadomości e-mail do Operations Manager. Poniższa ilustracja przedstawia tworzenie aplikacji logiki na potrzeby zdarzenia wjazdu na obszar wirtualnego ogrodzenia. W podobny sposób można utworzyć zdarzenie wyjazdu. Aby uzyskać więcej informacji, możesz wyświetlić wszystkie [obsługiwane programy obsługi zdarzeń](https://docs.microsoft.com/azure/event-grid/event-handlers).

1. Utwórz aplikację logiki w Azure Portal. Wybierz aplikację logiki w portalu Azure Marketplace. Następnie wybierz przycisk **Utwórz** .

   ![Tworzenie Azure Logic Apps do obsługi zdarzeń geoogrodzenia](./media/tutorial-geofence/logic-app.png)

2. W menu Ustawienia aplikacji logiki przejdź do **projektanta aplikacji logiki**

3. Wybierz wyzwalacz żądania HTTP, a następnie wybierz pozycję "nowy krok". W łączniku programu Outlook wybierz pozycję "Wyślij wiadomość e-mail" jako akcję
  
   ![Schemat usługi Logic Apps](./media/tutorial-geofence/logic-app-schema.png)

4. Wypełnij pola do wysyłania wiadomości e-mail. Pozostaw adres URL HTTP, który zostanie wygenerowany automatycznie po kliknięciu przycisku "Zapisz"

   ![Generowanie punktu końcowego Logic Apps](./media/tutorial-geofence/logic-app-endpoint.png)

5. Zapisz aplikację logiki, aby wygenerować punkt końcowy adresu URL HTTP i skopiuj adres URL HTTP.

## <a name="create-an-azure-maps-events-subscription"></a>Tworzenie subskrypcji zdarzeń usługi Azure Maps

Usługa Azure Maps obsługuje trzy typy zdarzeń. Obsługiwanym typom zdarzeń usługi Azure Maps można przyjrzeć się [tutaj](https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps). Potrzebujemy dwóch różnych subskrypcji zdarzeń, jednej dla zdarzenia Enter i jednego dla zdarzeń zakończenia.

Wykonaj poniższe kroki, aby utworzyć subskrypcję dla zdarzeń wjazdu wirtualnego ogrodzenia. Zdarzenia wyjazdu wirtualnego ogrodzenia możesz subskrybować w podobny sposób.

1. Przejdź do konta Azure Maps. Na pulpicie nawigacyjnym wybierz pozycję subskrypcje. Kliknij nazwę subskrypcji i wybierz pozycję **zdarzenia** z menu Ustawienia.

   ![Przejdź do Azure Maps zdarzeń konta](./media/tutorial-geofence/events-tab.png)

2. Aby utworzyć subskrypcję zdarzeń, wybierz pozycję Subskrypcja zdarzeń ze strony zdarzeń.

   ![Tworzenie subskrypcji zdarzeń usługi Azure Maps](./media/tutorial-geofence/create-event-subscription.png)

3. Nadaj nazwę subskrypcji zdarzeń i rozpocznij subskrypcję typu zdarzenia Wjazd. Teraz wybierz element webhook jako "typ punktu końcowego". Kliknij pozycję "Wybierz punkt końcowy" i skopiuj punkt końcowy adresu HTTP aplikacji logiki do "{Endpoint}"

   ![Szczegóły subskrypcji zdarzeń Azure Maps](./media/tutorial-geofence/events-subscription.png)


## <a name="use-geofence-api"></a>Używanie interfejsu API wirtualnego ogrodzenia

Za pomocą interfejsu API geoogrodzenia można sprawdzić, czy **urządzenie**, w tym przypadku, znajduje się wewnątrz lub poza ogrodzeniem. Umożliwia wykonywanie zapytań dotyczących interfejsu API GET ogrodzenia w różnych lokalizacjach, w których konkretny sprzęt przemieszcza się w czasie. Na poniższej ilustracji przedstawiono pięć lokalizacji z pięcioma urządzeniami konstrukcyjnymi. 

> [!Note]
> Scenariusz i zachowanie są oparte na tym samym **identyfikatorze urządzenia** , tak aby odzwierciedlały pięć różnych lokalizacji, jak pokazano na poniższej ilustracji.

"DeviceId" jest unikatowym IDENTYFIKATORem dostarczanym dla urządzenia w żądaniu GET podczas wykonywania zapytania dotyczącego jego lokalizacji. Po wprowadzeniu żądania asynchronicznego do **interfejsu API wyszukiwania geoogrodzenia —**"deviceId" pomaga w publikowaniu zdarzeń geoogrodzenia dla tego urządzenia względem określonego geoogrodzenia. W tym samouczku wprowadziliśmy asynchroniczne żądania do interfejsu API z unikatowym identyfikatorem "deviceId". Żądania w samouczku są wykonywane w kolejności chronologicznej, jak na diagramie. Właściwość "isEventPublished" w odpowiedzi jest publikowana za każdym razem, gdy urządzenie wejdzie lub opuszcza geoogrodzenie. Nie musisz rejestrować urządzenia, aby wykonać czynności opisane w tym samouczku.

Umożliwia powracanie do diagramu. Każda z tych pięciu lokalizacji służy do oceny stanu wjazdów i wyjazdów na terenie geofencingu względem ogrodzenia. W przypadku zmiany stanu usługa geofencingu wyzwala zdarzenie, które jest wysyłane do aplikacji logiki przez usługę Event Grid. W związku z tym Menedżer operacji otrzyma odpowiednie powiadomienie o wprowadzeniu lub wyjściu za pośrednictwem wiadomości e-mail.

![Mapa geoogrodzenia w Azure Maps](./media/tutorial-geofence/geofence.png)

W aplikacji Postman otwórz nową kartę w tej samej kolekcji, która została utworzona powyżej. Na karcie konstruktora wybierz metodę GET HTTP:

Poniżej znajdują się pięć żądań interfejsu API do rozróżnienia HTTP GET, z różnymi współrzędnymi lokalizacji sprzętu. Współrzędne są obserwowane w kolejności chronologicznej. Po każdym żądaniu następuje treść odpowiedzi.
 
1. Lokalizacja 1:
    
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
   ![Zapytanie geofencingu 1](./media/tutorial-geofence/geofence-query1.png)

   W powyższej odpowiedzi odległość ujemna od głównego ogrodzenia oznacza, że sprzęt znajduje się wewnątrz ogrodzenia. Dodatnia odległość od ogrodzenia podrzędnego oznacza, że urządzenie znajduje się poza ogrodzeniem geograficznym. 

2. Lokalizacja 2: 
   
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
    
   ![Zapytanie geofencingu 2](./media/tutorial-geofence/geofence-query2.png)

   Jeśli zobaczysz uważnie poprzednią odpowiedź JSON, sprzęt znajduje się poza podwitryną, ale znajduje się w głównym ogrodzeniu. Żadne zdarzenie nie jest wyzwalane i nie jest wysyłany żaden adres e-mail.

3. Lokalizacja 3: 
  
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Zapytanie geofencingu 3](./media/tutorial-geofence/geofence-query3.png)

   Nastąpiła zmiana stanu i sprzęt znajduje się teraz zarówno w obrębie głównego wirtualnego ogrodzenia, jak i wirtualnego ogrodzenia terenu podrzędnego. Ta zmiana powoduje wystąpienie zdarzenia do opublikowania, a do Operations Manager zostanie wysłana wiadomość e-mail z powiadomieniem.

4. Lokalizacja 4: 

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
  
   ![Zapytanie geofencingu 4](./media/tutorial-geofence/geofence-query4.png)

   Obserwując dokładnie odpowiednią odpowiedź, możesz zauważyć, że w tym miejscu nie jest publikowane żadne zdarzenie, chociaż sprzęt wyjechał poza wirtualne ogrodzenie terenu podrzędnego. Jeśli zobaczysz określony czas użytkownika w żądaniu GET, zobaczysz, że w tym momencie nie wygasło geofencingu. Sprzęt nadal znajduje się w głównym ogrodzeniu. W obszarze `expiredGeofenceGeometryId` w treści odpowiedzi możesz również sprawdzić identyfikator geometrii wirtualnego ogrodzenia terenu podrzędnego.


5. Lokalizacja 5:
      
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63799&lon=-122.134505&userTime=2019-01-16&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Zapytanie geofencingu 5](./media/tutorial-geofence/geofence-query5.png)

   Możesz zobaczyć, że sprzęt wyjechał poza wirtualne ogrodzenie głównego terenu budowy. Zostanie opublikowane zdarzenie, a do Operations Manager jest wysyłana wiadomość e-mail z alertami.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób konfigurowania geoogrodzenia poprzez przekazanie go do Azure Maps i usługi danych przy użyciu interfejsu API przekazywania danych. Nauczyliśmy się również, jak za pomocą usługi Azure Maps Events Grid subskrybować i obsługiwać zdarzenia geofencingu. 

* Zobacz temat [Handle content types in Azure Logic Apps (Obsługa typów zawartości w usłudze Azure Logic Apps)](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type), aby dowiedzieć się, jak za pomocą aplikacji logiki analizować dane JSON w celu tworzenia bardziej złożonej logiki.
* Aby dowiedzieć się więcej na temat programów obsługi zdarzeń w usłudze Event Grid, zobacz [Supported Events Handlers in Event Grid (Obsługiwane programy obsługi zdarzeń w usłudze Event Grid)](https://docs.microsoft.com/azure/event-grid/event-handlers).
