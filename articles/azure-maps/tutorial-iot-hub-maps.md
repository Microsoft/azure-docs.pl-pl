---
title: 'Samouczek: implementowanie analizy przestrzennej IoT przy użyciu map Microsoft Azure'
description: Integruj IoT Hub z interfejsami API usługi Maps Microsoft Azure.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/01/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 86ae186d3d8b131955be7d9fa2c305316dea9f00
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2020
ms.locfileid: "89658446"
---
# <a name="tutorial-implement-iot-spatial-analytics-using-azure-maps"></a>Samouczek: implementowanie analizy przestrzennej IoT przy użyciu Azure Maps

W scenariuszu IoT często są przechwytywane i śledzone odpowiednie zdarzenia występujące w przestrzeni i czasie. Przykładowe scenariusze obejmują zarządzanie flotą, śledzenie zasobów, mobilność i aplikacje inteligentne miasto. Ten samouczek przeprowadzi Cię przez rozwiązanie, które śledzi używany ruch wynajmu samochodu przy użyciu interfejsów API Azure Maps.

W tym samouczku wykonasz następujące instrukcje:

> [!div class="checklist"]
> * Utwórz konto usługi Azure Storage, aby rejestrować dane śledzenia samochodu.
> * Przekaż geoogrodzenie do usługi danych Azure Maps przy użyciu interfejsu API przekazywania danych.
> * Utwórz IoT Hub i Zarejestruj urządzenie.
> * Utwórz funkcję w Azure Functions, implementując logikę biznesową opartą na Azure Maps analizie przestrzennej.
> * Subskrybuj zdarzenia telemetryczne urządzenia IoT z funkcji platformy Azure za pośrednictwem Event Grid.
> * Filtrowanie zdarzeń telemetrii za pomocą IoT Hub Routing komunikatów.

## <a name="prerequisites"></a>Wymagania wstępne

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. [Utwórz konto Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account).

3. [Uzyskaj podstawowy klucz subskrypcji](quick-demo-map-app.md#get-the-primary-key-for-your-account), nazywany także kluczem podstawowym lub kluczem subskrypcji. Aby uzyskać więcej informacji na temat uwierzytelniania w Azure Maps, zobacz [Zarządzanie uwierzytelnianiem w programie Azure Maps](how-to-manage-authentication.md).

4. [Utwórz grupę zasobów](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal#create-resource-groups). W tym samouczku będziemy nazywać naszą grupę zasobów *ContosoRental*, ale można wybrać dowolną nazwę.

5. Pobierz [projekt języka C# rentalCarSimulation ](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation).

W tym samouczku jest stosowana aplikacja programu [Poster](https://www.postman.com/) , ale można wybrać inne środowisko deweloperskie interfejsu API.

## <a name="use-case-rental-car-tracking"></a>Przypadek użycia: śledzenie samochodu wynajmu

W tym samouczku przedstawiono następujący scenariusz: Firma najmu samochodu chce rejestrować informacje o lokalizacji, podróży i stanie uruchomienia w przypadku samochodów leasingowych. Ponadto firma chce przechowywać te informacje, gdy samochód opuszcza właściwy autoryzowany region geograficzny.

W naszym przypadku użycia samochody najmu są wyposażone w urządzenia IoT regularnie wysyłające dane telemetryczne do IoT Hub platformy Azure. Dane telemetryczne obejmują bieżącą lokalizację i wskazuje, czy aparat samochodu jest uruchomiony. Schemat lokalizacji urządzenia jest zgodny ze [schematem Plug and Play IoT dla danych geoprzestrzennych](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v1-preview/schemas/geospatial.md). Schemat telemetrii urządzenia dla samochodu wynajmu wygląda podobnie do następującego kodu JSON:

```JSON
{
    "data": {
        "properties": {
            "Engine": "ON"
        },
        "systemProperties": {
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "ContosoRentalDevice",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "636959817064335548",
            "iothub-enqueuedtime": "2019-06-18T00:17:20.608Z",
            "iothub-message-source": "Telemetry"
        },
        "body": {
            "location": {
                "type": "Point",
                "coordinates": [ -77.025988698005662, 38.9015330523316 ]
            }
        }
    }
}
```

W tym samouczku będziemy śledzić tylko jeden pojazd. Po skonfigurowaniu usług platformy Azure konieczne będzie pobranie [projektu RentalCarSimulation C# ](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) w celu uruchomienia symulatora pojazdów. Cały proces, od zdarzenia do wykonania funkcji, został podsumowany w następujących krokach:

1. Urządzenie znajdujące się w pojeździe wysyła dane telemetryczne do centrum IoT Hub.

2. Jeśli aparat samochodowy jest uruchomiony, centrum IoT publikuje dane telemetryczne w Event Grid.

3. Funkcja platformy Azure jest wyzwalana z powodu subskrypcji zdarzeń dla zdarzeń telemetrii urządzenia.

4. Funkcja będzie rejestrować współrzędne lokalizacji urządzenia pojazdu, czas zdarzenia i identyfikator urządzenia. Następnie zostanie użyty [interfejs API pobierania geoogrodzenia przestrzennego](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence) , aby określić, czy samochód jest oparty poza ogrodzeniem. Jeśli przepodróżuje poza granice geoogrodzenia, funkcja przechowuje dane lokalizacji otrzymane ze zdarzenia w naszym kontenerze obiektów BLOB. Ponadto nasza funkcja wysyła zapytanie do [wyszukiwania wstecznego adresu](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) , aby przetłumaczyć lokalizację współrzędnej na ulicę, i zapisać ją z pozostałymi danymi lokalizacji urządzenia.

Poniższy diagram zawiera ogólne omówienie systemu.

   :::image type="content" source="./media/tutorial-iot-hub-maps/system-diagram.png" border="false" alt-text="Omówienie systemu":::

Na poniższej ilustracji wyróżniono obszar geoogrodzenia na niebiesko. Trasa samochodu wynajmu jest wskazywana przez zieloną linię.

   :::image type="content" source="./media/tutorial-iot-hub-maps/geofence-route.png" border="false" alt-text="Trasa geoogrodzenia":::

## <a name="create-an-azure-storage-account"></a>Tworzenie konta usługi Azure Storage

Aby przechowywać dane śledzenia naruszenia samochodu, utworzymy [konto magazynu ogólnego przeznaczenia w wersji 2](https://docs.microsoft.com/azure/storage/common/storage-account-overview#general-purpose-v2-accounts) w grupie zasobów. Jeśli grupa zasobów nie została utworzona, postępuj zgodnie z instrukcjami w temacie [Tworzenie grupy zasobów](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal#create-resource-groups). W tym samouczku zmienimy nazwę naszej grupy zasobów *ContosoRental*.

Aby utworzyć konto magazynu, postępuj zgodnie z instrukcjami podanymi w temacie [Tworzenie konta magazynu](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal). W tym samouczku nazywamy *contosorentalstorage*konta magazynu, ale możesz nadać mu dowolną nazwę.

Po pomyślnym utworzeniu konta magazynu należy utworzyć kontener do przechowywania danych rejestrowania.

1. Przejdź do nowo utworzonego konta magazynu. Kliknij łącze **kontenery** w sekcji podstawowe informacje.

    :::image type="content" source="./media/tutorial-iot-hub-maps/containers.png" alt-text="Kontenery dla usługi BLOB Storage":::

2. Kliknij przycisk **+ kontener** w lewym górnym rogu. Panel zostanie wyświetlony po prawej stronie przeglądarki. Nazwij kontener *contoso-Wynajem dzienników* i kliknij przycisk **Utwórz**.

     :::image type="content" source="./media/tutorial-iot-hub-maps/container-new.png" alt-text="Tworzenie kontenera obiektów blob":::

3. Przejdź do bloku **klucze dostępu** na koncie magazynu i skopiuj **nazwę konta magazynu** i wartość **klucza** w sekcji **Klucz1** . Będziemy potrzebować obu wartości w sekcji [Tworzenie funkcji platformy Azure i dodawanie Event Grid subskrypcji](#create-an-azure-function-and-add-an-event-grid-subscription) .

    :::image type="content" source="./media/tutorial-iot-hub-maps/access-keys.png" alt-text="Kopiuj nazwę i klucz konta magazynu":::

## <a name="upload-a-geofence"></a>Przekaż geoogrodzenie

Teraz będziemy używać [aplikacji Poster](https://www.getpostman.com) do [przekazywania geoogrodzenia](https://docs.microsoft.com/azure/azure-maps/geofence-geojson) do usługi Azure Maps. Geoogrodzenie definiuje uprawniony Obszar geograficzny dla pojazdu wynajmu.  Będziemy używać geofencingu w naszej funkcji platformy Azure, aby określić, czy samochód przeniósł poza obszar geoogrodzenia.

Otwórz aplikację Poster i postępuj zgodnie z poniższymi instrukcjami, aby przekazać geoogrodzenie przy użyciu interfejsu API przekazywania danych Azure Maps.  

1. Otwórz aplikację Poster. W górnej części aplikacji Poster wybierz pozycję **Nowy**. W oknie **Tworzenie nowego** okna wybierz pozycję **Kolekcja**.  Nadaj kolekcji nazwę i wybierz przycisk **Utwórz** .

2. Aby utworzyć żądanie, wybierz pozycję **nowe** ponownie. W oknie **Tworzenie nowego** okna wybierz pozycję **Żądaj**. Wprowadź **nazwę żądania** dla żądania. Wybierz kolekcję utworzoną w poprzednim kroku, a następnie wybierz pozycję **Zapisz**.

3. Wybierz metodę **post** http na karcie Konstruktor i wprowadź następujący adres URL, aby przekazać geoogrodzenie do interfejsu API przekazywania danych. Pamiętaj, aby zastąpić `{subscription-key}` klucz subskrypcji podstawowym.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    Wartość "GEOJSON" dla `dataFormat` parametru w ścieżce URL reprezentuje Format przekazywanych danych.

4. Kliknij pozycję **treść** , a następnie wybierz pozycję Format wejścia **RAW** i wybierz pozycję **JSON** jako format danych wejściowych z listy rozwijanej. Otwórz plik danych JSON w [tym miejscu](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4)i skopiuj kod JSON do sekcji treść. Kliknij pozycję **Wyślij**.

5. Kliknij niebieski przycisk **Wyślij** i poczekaj na przetworzenie żądania. Po zakończeniu żądania przejdź na kartę **nagłówki** odpowiedzi. Skopiuj wartość klucza **lokalizacji** , czyli `status URL` .

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. Aby sprawdzić stan wywołania interfejsu API, Utwórz żądanie **Get** http na stronie `status URL` . Musisz dołączyć podstawowy klucz subskrypcji do adresu URL na potrzeby uwierzytelniania. Żądanie **Get** powinno wyglądać następująco:

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={subscription-key}

7. When the **GET** HTTP request completes successfully, it will return a `resourceLocation`. The `resourceLocation` contains the unique `udid` for the uploaded content. You'll need to copy this `udid` for later use in this tutorial.

      ```json
      {
          "status": "Succeeded",
          "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
      }
      ```

## <a name="create-an-azure-iot-hub"></a>Tworzenie wystąpienia usługi Azure IoT Hub

Usługa Azure IoT Hub umożliwia bezpieczną i niezawodną komunikację dwukierunkową między aplikacją IoT i zarządzanymi urządzeniami.  W naszym scenariuszu chcemy uzyskać informacje z urządzenia znajdującego się w pojeździe w celu określenia lokalizacji samochodu wynajmu. W tej sekcji utworzysz Centrum IoT Hub w grupie zasobów *ContosoRental* . Centrum IoT będzie odpowiedzialne za publikowanie zdarzeń telemetrii urządzeń.

> [!NOTE]
> Funkcja publikowania zdarzeń telemetrii urządzenia w usłudze IoT Hub na Event Grid jest w publicznej wersji zapoznawczej. Funkcja publiczna w wersji zapoznawczej jest dostępna we wszystkich regionach z wyjątkiem **Wschodnie stany USA, zachodnie stany USA, Europa Zachodnia, Azure Government, Chiny z Chin** i **Azure (Niemcy**).

Aby utworzyć Centrum IoT Hub w grupie zasobów *ContosoRental* , wykonaj kroki opisane w temacie [Tworzenie Centrum IoT Hub](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet#create-an-iot-hub).

## <a name="register-a-device-in-iot-hub"></a>Rejestrowanie urządzenia w usłudze IoT Hub

Urządzenia nie mogą nawiązać połączenia z Centrum IoT, chyba że są zarejestrowane w rejestrze tożsamości Centrum IoT Hub. W naszym scenariuszu utworzymy jedno urządzenie o nazwie *InVehicleDevice*. Aby utworzyć i zarejestrować urządzenie w usłudze IoT Hub, wykonaj kroki opisane w temacie [Rejestrowanie nowego urządzenia w usłudze IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal#register-a-new-device-in-the-iot-hub). Upewnij się, że skopiujesz **podstawowe parametry połączenia** urządzenia, ponieważ będziemy z niego korzystać w późniejszym kroku.

## <a name="create-an-azure-function-and-add-an-event-grid-subscription"></a>Tworzenie funkcji platformy Azure i Dodawanie subskrypcji Event Grid

Azure Functions to bezserwerowa usługa obliczeniowa, która umożliwia uruchamianie małych fragmentów kodu ("Functions") bez konieczności jawnego udostępniania infrastruktury obliczeniowej ani zarządzania nią. Aby dowiedzieć się więcej na temat Azure Functions, zobacz dokumentację [usługi Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) .

Funkcja jest wyzwalana przez określone zdarzenie. W naszym scenariuszu utworzymy funkcję wyzwalaną przez wyzwalacz Event Grid. Utworzymy relację między wyzwalaczem a funkcją przez utworzenie subskrypcji zdarzeń dla zdarzeń telemetrii urządzenia usługi IoT Hub. Gdy wystąpi zdarzenie telemetrii urządzenia, nasza funkcja zostanie wywołana jako punkt końcowy i otrzyma odpowiednie dane dla urządzenia, które [wcześniej zarejestrowano w usłudze IoT Hub](#register-a-device-in-iot-hub).

Kod skryptu w języku C#, który będzie zawierał funkcję, może być widoczny w [tym miejscu](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx).

Teraz skonfigurujemy naszą funkcję platformy Azure.

1. Na pulpicie nawigacyjnym Azure Portal kliknij pozycję **Utwórz zasób**. Wpisz **aplikacja funkcji** w polu tekstowym Wyszukaj. Kliknij **aplikacja funkcji**. Kliknij przycisk **Utwórz**.

2. Na stronie Tworzenie **aplikacja funkcji** Nadaj nazwę aplikacji funkcji. W obszarze **Grupa zasobów**wybierz pozycję *ContosoRental* z listy rozwijanej.  Wybierz pozycję *.NET Core* jako **stos środowiska uruchomieniowego**. Kliknij przycisk **Dalej: Hosting >** w dolnej części strony.

    :::image type="content" source="./media/tutorial-iot-hub-maps/rental-app.png" alt-text="Tworzenie aplikacji funkcji":::

3. W polu **konto magazynu**wybierz konto magazynu utworzone w [ramach tworzenia konta usługi Azure Storage](#create-an-azure-storage-account). Kliknij pozycję **Przejrzyj i utwórz**.

4. Przejrzyj szczegóły aplikacji funkcji, a następnie kliknij przycisk **Utwórz**.

5. Po utworzeniu aplikacji dodamy do niej funkcję. Przejdź do aplikacji funkcji. Kliknij blok **funkcje** . Kliknij pozycję **+ Dodaj** w górnej części strony. Zostanie wyświetlony panel szablonu funkcji. Przewiń w dół panelu. Kliknij **Azure Event Grid wyzwalacza**.

     >[!WARNING]
    > **Wyzwalacz usługi Azure Event Hub** i szablon **wyzwalacza Azure Event Grid** mają podobne nazwy. Kliknij szablon **wyzwalacza Azure Event Grid** .

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create.png" alt-text="Tworzenie funkcji":::

6. Nadaj funkcji nazwę. W tym samouczku użyjemy nazwy, *GetGeoFunction*, ale możesz użyć dowolnej nazwy. Kliknij przycisk **Utwórz funkcję**.

7. Kliknij blok **kod i test** w menu po lewej stronie. Skopiuj i wklej [skrypt C#](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) do okna kod.

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-code.png" alt-text="Kopiowanie/wklejanie kodu do okna funkcji":::

8. W kodzie C# Zastąp poniższe parametry. Kliknij pozycję **Zapisz**. Nie klikaj jeszcze **testu/uruchomienia**
    * Zastąp **SUBSCRIPTION_KEY** podstawowym kluczem subskrypcji konta usługi Azure Maps.
    * Zastąp **UDID** z elementem `udid` geoogrodzenia przekazanym w obszarze [Przekaż geoogrodzenie](#upload-a-geofence).
    * Funkcja **CreateBlobAsync** w skrypcie tworzy obiekt BLOB dla zdarzenia na koncie magazynu danych. Zastąp **ACCESS_KEY**, **ACCOUNT_NAME**i **STORAGE_CONTAINER_NAME** kluczem dostępu do konta magazynu, nazwą konta i kontenerem magazynu danych. Te wartości zostały wygenerowane podczas tworzenia konta magazynu w [ramach tworzenia konta usługi Azure Storage](#create-an-azure-storage-account).

9. Kliknij blok **integracja** w menu po lewej stronie. Kliknij **Event Grid wyzwalacza** na diagramie. Wpisz nazwę wyzwalacza, *eventGridEvent*, a następnie kliknij pozycję **utwórz subskrypcję Event Grid**.

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-integration.png" alt-text="Dodawanie subskrypcji zdarzeń":::

10. Wypełnij szczegóły subskrypcji. Nazwij subskrypcję zdarzeń. W obszarze *schemat zdarzenia*wybierz pozycję *Event Grid schemat*. W obszarze **typy tematów**wybierz pozycję *konta usługi Azure IoT Hub*. W obszarze **Grupa zasobów**wybierz grupę zasobów utworzoną na początku tego samouczka. W obszarze **zasób**wybierz Centrum IoT utworzone w temacie [Tworzenie usługi Azure IoT Hub](#create-an-azure-iot-hub). W obszarze **Filtruj do typów zdarzeń**wybierz pozycję dane *telemetryczne urządzenia*. Po wybraniu tych opcji zobaczysz zmianę **typu tematu** na *IoT Hub*. W polu **nazwa tematu systemu**można użyć nazwy, która jest taka sama jak nazwa zasobu.  Na koniec kliknij pozycję **Wybierz punkt końcowy** w sekcji **Szczegóły punktu końcowego** . Zaakceptuj wszystkie ustawienia i kliknij przycisk **Potwierdź wybór**.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription.png" alt-text="Utwórz subskrypcję zdarzeń":::

11. Przejrzyj ustawienia. Upewnij się, że punkt końcowy określa funkcję utworzoną na początku tej sekcji. Kliknij przycisk **Utwórz**.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription-confirm.png" alt-text="Potwierdzenie utworzenia subskrypcji zdarzenia":::

12. Teraz wróć do panelu **Edytowanie wyzwalacza** . Kliknij pozycję **Zapisz**.

## <a name="filter-events-using-iot-hub-message-routing"></a>Filtrowanie zdarzeń przy użyciu IoT Hub Routing komunikatów

Po dodaniu subskrypcji Event Grid do funkcji platformy Azure zostanie automatycznie utworzona trasa obsługi komunikatów w określonym Centrum IoT Hub. Routing komunikatów umożliwia kierowanie różnych typów danych do różnych punktów końcowych. Można na przykład kierować komunikaty telemetryczne urządzenia, zdarzenia cyklu życia urządzenia i zdarzenia zmiany z przędzą urządzenia. Aby dowiedzieć się więcej na temat routingu komunikatów usługi IoT Hub, zobacz [używanie IoT Hub Routing komunikatów](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c).

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-route.png" alt-text="Routing komunikatów w usłudze IoT Hub":::

W naszym przykładowym scenariuszu chcemy otrzymywać komunikaty tylko w przypadku przeniesienia samochodu wynajmu. Utworzymy zapytanie routingu do filtrowania zdarzeń, `Engine` w których właściwość jest równa **"on"**. Aby utworzyć zapytanie routingu, kliknij trasę **RouteToEventGrid** i Zastąp **zapytanie routingu** z **"aparat =" na ""** , a następnie kliknij przycisk **Zapisz**. Teraz Centrum IoT będzie publikować dane telemetryczne urządzenia, na których jest włączony aparat.

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-filter.png" alt-text="Filtrowanie komunikatów routingu":::

>[!TIP]
>Istnieją różne sposoby wykonywania zapytań dotyczących komunikatów przesyłanych z urządzeń do chmury, aby dowiedzieć się więcej na temat składni routingu wiadomości, zobacz [IoT Hub Routing komunikatów](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax).

## <a name="send-telemetry-data-to-iot-hub"></a>Wyślij dane telemetryczne do IoT Hub

Gdy nasza funkcja platformy Azure zostanie uruchomiona, możemy teraz wysyłać dane telemetryczne do centrum IoT Hub, które będą kierować do Event Grid. Użyjmy aplikacji języka C# w celu symulowania danych lokalizacji na urządzeniu w ramach samochodu w kabinie wynajmu. Aby uruchomić aplikację, musisz mieć zestaw .NET Core SDK 2.1.0 na komputerze deweloperskim lub nowszym. Wykonaj poniższe kroki, aby wysłać symulowane dane telemetryczne do IoT Hub.

1. Jeśli jeszcze tego nie zrobiono, Pobierz projekt C# [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) .

2. Otwórz plik simulatedCar.cs w wybranym edytorze tekstów i Zastąp wartość `connectionString` atrybutem zapisanym podczas rejestrowania urządzenia i Zapisz zmiany w pliku.

3. Upewnij się, że na maszynie jest zainstalowany program .NET Core. W oknie terminalu lokalnego przejdź do folderu głównego projektu C# i uruchom następujące polecenie, aby zainstalować wymagane pakiety dla aplikacji symulowanego urządzenia:

    ```cmd/sh
    dotnet restore
    ```

4. W tym samym terminalu uruchom następujące polecenie, aby skompilować i uruchomić aplikację symulacji samochodu wynajmu:

    ```cmd/sh
    dotnet run
    ```

  Terminal lokalny powinien wyglądać podobnie do przedstawionego poniżej.

:::image type="content" source="./media/tutorial-iot-hub-maps/terminal.png" alt-text="Dane wyjściowe terminalu":::

Jeśli teraz otworzysz kontener usługi BLOB Storage, możesz zobaczyć cztery obiekty blob dla lokalizacji, w których pojazd znajduje się poza ogrodzeniem.

:::image type="content" source="./media/tutorial-iot-hub-maps/blob.png" alt-text="Wyświetlanie obiektów BLOB wewnątrz kontenera":::

Mapa poniżej przedstawia cztery punkty lokalizacji pojazdu poza ogrodzeniem. Każda lokalizacja była rejestrowana w regularnych odstępach czasu.

:::image type="content" source="./media/tutorial-iot-hub-maps/violation-map.png" alt-text="Mapa naruszenia":::

## <a name="explore-azure-maps-and-iot"></a>Eksplorowanie Azure Maps i IoT

Aby poznać Azure Maps interfejsów API używanych w tym samouczku, zobacz:

* [Uzyskaj zwrotny adres wyszukiwania](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [Pobierz geoogrodzenie](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Aby uzyskać pełną listę Azure Maps interfejsów API REST, zobacz:

* [Interfejsy API REST Azure Maps](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Aby dowiedzieć się więcej na temat Plug and Play IoT, zobacz:

* [IoT Plug and Play](https://docs.microsoft.com/azure/iot-pnp)

Aby uzyskać listę urządzeń z certyfikatem platformy Azure dla IoT, odwiedź stronę:

* [Certyfikowane urządzenia platformy Azure](https://catalog.azureiotsolutions.com/)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o sposobach wysyłania urządzenia do telemetrii w chmurze i innych sposobów, zobacz:

> [!div class="nextstepaction"]
> [Wysyłanie danych telemetrycznych z urządzenia](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)
