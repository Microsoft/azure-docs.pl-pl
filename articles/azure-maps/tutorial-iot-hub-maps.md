---
title: 'Samouczek: implementowanie analizy przestrzennej usługi IoT | Mapy Microsoft Azure'
description: Samouczek dotyczący sposobu integrowania IoT Hub z interfejsami API usługi Maps Microsoft Azure
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/01/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: b5c65035f8b51b53f617d4562fe1982f53f0deec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98678275"
---
# <a name="tutorial-implement-iot-spatial-analytics-by-using-azure-maps"></a>Samouczek: implementowanie analizy przestrzennej IoT przy użyciu Azure Maps

W scenariuszu IoT często są przechwytywane i śledzone odpowiednie zdarzenia występujące w przestrzeni i czasie. Przykłady obejmują zarządzanie flotą, śledzenie zasobów, mobilność i aplikacje inteligentnego miasta. Ten samouczek przeprowadzi Cię przez rozwiązanie, które śledzi zużyte ruchy wynajmu samochodu przy użyciu interfejsów API Azure Maps.

W tym samouczku wykonasz następujące instrukcje:

> [!div class="checklist"]
> * Utwórz konto usługi Azure Storage, aby rejestrować dane śledzenia samochodu.
> * Przekaż geoogrodzenie do usługi danych Azure Maps (wersja zapoznawcza) za pomocą interfejsu API przekazywania danych.
> * Utwórz centrum w usłudze Azure IoT Hub i Zarejestruj urządzenie.
> * Utwórz funkcję w Azure Functions, implementując logikę biznesową opartą na Azure Maps analizie przestrzennej.
> * Subskrybuj zdarzenia telemetryczne urządzenia IoT z funkcji platformy Azure za pośrednictwem Azure Event Grid.
> * Filtrowanie zdarzeń telemetrii za pomocą IoT Hub Routing komunikatów.

## <a name="prerequisites"></a>Wymagania wstępne

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. [Utwórz konto Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account).

3. [Uzyskaj podstawowy klucz subskrypcji](quick-demo-map-app.md#get-the-primary-key-for-your-account), nazywany także kluczem podstawowym lub kluczem subskrypcji. Aby uzyskać więcej informacji, zobacz [Zarządzanie uwierzytelnianiem w Azure Maps](how-to-manage-authentication.md).

4. [Utwórz grupę zasobów](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups). W tym samouczku będziemy nazywać naszą grupę zasobów *ContosoRental*, ale można wybrać dowolną nazwę.

5. Pobierz [projekt języka C# rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation).

W tym samouczku jest stosowana aplikacja programu [Poster](https://www.postman.com/) , ale można wybrać inne środowisko deweloperskie interfejsu API.

## <a name="use-case-rental-car-tracking"></a>Przypadek użycia: śledzenie samochodu wynajmu

Załóżmy, że firma wynajmu samochodu chce rejestrować informacje o lokalizacji, podróży i stanie uruchomienia dla samochodów. Firma chce również przechowywać te informacje, gdy samochód opuszcza właściwy autoryzowany region geograficzny.

Samochody najmu są wyposażone w urządzenia IoT regularnie wysyłające dane telemetryczne do IoT Hub. Dane telemetryczne obejmują bieżącą lokalizację i wskazuje, czy aparat samochodu jest uruchomiony. Schemat lokalizacji urządzenia jest zgodny ze [schematem Plug and Play IoT dla danych geoprzestrzennych](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v1-preview/schemas/geospatial.md). Schemat telemetrii urządzenia dla samochodu wynajmu wygląda podobnie do następującego kodu JSON:

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

W tym samouczku jest śledzony tylko jeden pojazd. Po skonfigurowaniu usług platformy Azure należy pobrać [projekt RentalCarSimulation C#](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) w celu uruchomienia symulatora pojazdów. Cały proces, od zdarzenia do wykonania funkcji, został podsumowany w następujących krokach:

1. Urządzenie znajdujące się w pojeździe wysyła dane telemetryczne do IoT Hub.

2. Jeśli aparat samochodowy jest uruchomiony, centrum publikuje dane telemetryczne do Event Grid.

3. Funkcja platformy Azure jest wyzwalana z powodu subskrypcji zdarzeń dla zdarzeń telemetrii urządzenia.

4. Funkcja rejestruje współrzędne lokalizacji urządzenia pojazdu, czas zdarzenia i identyfikator urządzenia. Następnie używa [interfejsu API pobierania przestrzennego przestrzenny](/rest/api/maps/spatial/getgeofence) , aby określić, czy samochód jest oparty poza ogrodzeniem. Jeśli przepodróżuje poza granice geoogrodzenia, funkcja przechowuje dane lokalizacji odebrane ze zdarzenia do kontenera obiektów BLOB. Funkcja wysyła również zapytanie do [adresu wyszukiwania wstecz](/rest/api/maps/search/getsearchaddressreverse) w celu tłumaczenia lokalizacji współrzędnych na ulica i zapisuje ją wraz z pozostałymi danymi lokalizacji urządzenia.

Na poniższym diagramie przedstawiono ogólne omówienie systemu.

   :::image type="content" source="./media/tutorial-iot-hub-maps/system-diagram.png" border="false" alt-text="Diagram omówienia systemu.":::

Na poniższej ilustracji wyróżniono obszar geoogrodzenia na niebiesko. Trasa samochodu wynajmu jest wskazywana przez zieloną linię.

   :::image type="content" source="./media/tutorial-iot-hub-maps/geofence-route.png" border="false" alt-text="Rysunek przedstawiający trasę z geoogrodzeniem.":::

## <a name="create-an-azure-storage-account"></a>Tworzenie konta usługi Azure Storage

Aby przechowywać dane śledzenia naruszenia samochodu, Utwórz [konto magazynu ogólnego przeznaczenia w wersji 2](../storage/common/storage-account-overview.md#general-purpose-v2-accounts) w grupie zasobów. Jeśli grupa zasobów nie została utworzona, postępuj zgodnie z instrukcjami w temacie [Tworzenie grupy zasobów](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups). W tym samouczku określisz nazwę grupy zasobów *ContosoRental*.

Aby utworzyć konto magazynu, postępuj zgodnie z instrukcjami podanymi w temacie [Tworzenie konta magazynu](../storage/common/storage-account-create.md?tabs=azure-portal). W tym samouczku Nazwij konto magazynu *contosorentalstorage*, ale ogólnie możesz nadać mu nazwę.

Po pomyślnym utworzeniu konta magazynu należy utworzyć kontener do przechowywania danych rejestrowania.

1. Przejdź do nowo utworzonego konta magazynu. W sekcji **podstawowe** informacje wybierz łącze **kontenery** .

    :::image type="content" source="./media/tutorial-iot-hub-maps/containers.png" alt-text="Zrzut ekranu kontenerów dla usługi BLOB Storage.":::

2. W lewym górnym rogu wybierz pozycję **+ kontener**. Panel pojawia się po prawej stronie przeglądarki. Nazwij kontener *contoso-Wynajem dzienników* i wybierz pozycję **Utwórz**.

     :::image type="content" source="./media/tutorial-iot-hub-maps/container-new.png" alt-text="Zrzut ekranu przedstawiający tworzenie kontenera obiektów BLOB.":::

3. Przejdź do okienka **klucze dostępu** na koncie magazynu i skopiuj **nazwę konta magazynu** i wartość **klucza** w sekcji **Klucz1** . Obie te wartości są potrzebne w sekcji "Tworzenie funkcji platformy Azure i Dodawanie subskrypcji Event Grid".

    :::image type="content" source="./media/tutorial-iot-hub-maps/access-keys.png" alt-text="Zrzut ekranu przedstawiający nazwę i klucz konta magazynu kopiowania.":::

## <a name="upload-a-geofence"></a>Przekaż geoogrodzenie

Następnie użyj [aplikacji Poster](https://www.getpostman.com) , aby [przekazać wartość geoogrodzenia](./geofence-geojson.md) do Azure Maps. Geoogrodzenie definiuje uprawniony Obszar geograficzny dla pojazdu wynajmu. Będziesz używać geoogrodzenia w funkcji platformy Azure, aby określić, czy samochód przeniósł poza obszar geoogrodzenia.

Wykonaj następujące kroki, aby przekazać geoogrodzenie przy użyciu interfejsu API przekazywania danych Azure Maps: 

1. Otwórz aplikację Poster i wybierz pozycję **Nowy**. W oknie **Tworzenie nowego** okna wybierz pozycję **Kolekcja**. Nadaj kolekcji nazwę i wybierz pozycję **Utwórz**.

2. Aby utworzyć żądanie, wybierz pozycję **nowe** ponownie. W oknie **Tworzenie nowego** okna wybierz pozycję **żądanie**, a następnie wprowadź nazwę żądania dla żądania. Wybierz kolekcję utworzoną w poprzednim kroku, a następnie wybierz pozycję **Zapisz**.

3. Wybierz metodę **post** http na karcie Konstruktor i wprowadź następujący adres URL, aby przekazać geoogrodzenie do interfejsu API przekazywania danych. Pamiętaj, aby zastąpić `{subscription-key}` klucz subskrypcji podstawowym.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    W ścieżce URL `geojson` wartość `dataFormat` parametru reprezentuje Format przekazywanych danych.

4. Wybierz pozycję **treść**  >  **pierwotna** dla formatu danych wejściowych, a następnie z listy rozwijanej wybierz pozycję **JSON** . [Otwórz plik danych JSON](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4)i skopiuj kod JSON do sekcji treść. Wybierz pozycję **Wyślij**.

5. Wybierz pozycję **Wyślij** i poczekaj na przetworzenie żądania. Po zakończeniu żądania przejdź do karty **nagłówki** odpowiedzi. Skopiuj wartość klucza **lokalizacji** , czyli `status URL` .

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. Aby sprawdzić stan wywołania interfejsu API, Utwórz żądanie **Get** http na stronie `status URL` . Musisz dołączyć podstawowy klucz subskrypcji do adresu URL na potrzeby uwierzytelniania. Żądanie **Get** powinno wyglądać następująco:

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={subscription-key}
   ```
   
7. Po pomyślnym zakończeniu żądania **Get** http zwraca wartość `resourceLocation` . `resourceLocation`Zawiera unikatowy `udid` dla przekazanej zawartości. Skopiuj go `udid` do późniejszego użycia w tym samouczku.

      ```json
      {
          "status": "Succeeded",
          "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
      }
      ```

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

IoT Hub umożliwia bezpieczną i niezawodną komunikację dwukierunkową między aplikacją IoT i zarządzanymi urządzeniami. Na potrzeby tego samouczka chcesz uzyskać informacje z urządzenia w pojeździe w celu określenia lokalizacji samochodu wynajmu. W tej sekcji utworzysz Centrum IoT Hub w grupie zasobów *ContosoRental* . Centrum będzie odpowiedzialne za publikowanie zdarzeń telemetrii urządzenia.

> [!NOTE]
> Możliwość publikowania zdarzeń telemetrii urządzenia w Event Grid jest obecnie w wersji zapoznawczej. Ta funkcja jest dostępna we wszystkich regionach, z wyjątkiem następujących: Wschodnie stany USA, zachodnie stany USA, Europa Zachodnia, Azure Government, chiński (Chiny) i Azure (Niemcy).

Aby utworzyć Centrum IoT Hub w grupie zasobów *ContosoRental* , wykonaj kroki opisane w temacie [Tworzenie Centrum IoT Hub](../iot-hub/quickstart-send-telemetry-dotnet.md#create-an-iot-hub).

## <a name="register-a-device-in-your-iot-hub"></a>Rejestrowanie urządzenia w centrum IoT Hub

Urządzenia nie mogą nawiązać połączenia z Centrum IoT, chyba że są zarejestrowane w rejestrze tożsamości Centrum IoT Hub. Tutaj utworzysz jedno urządzenie o nazwie *InVehicleDevice*. Aby utworzyć i zarejestrować urządzenie w usłudze IoT Hub, wykonaj kroki opisane w temacie [Rejestrowanie nowego urządzenia w centrum IoT Hub](../iot-hub/iot-hub-create-through-portal.md#register-a-new-device-in-the-iot-hub). Należy pamiętać o skopiowaniu podstawowych parametrów połączenia urządzenia. Będzie on potrzebny później.

## <a name="create-a-function-and-add-an-event-grid-subscription"></a>Tworzenie funkcji i Dodawanie subskrypcji Event Grid

Azure Functions to bezserwerowa usługa obliczeniowa, która umożliwia uruchamianie małych fragmentów kodu ("Functions") bez konieczności jawnego udostępniania infrastruktury obliczeniowej ani zarządzania nią. Aby dowiedzieć się więcej, zobacz [Azure Functions](../azure-functions/functions-overview.md).

Funkcja jest wyzwalana przez określone zdarzenie. Tutaj utworzysz funkcję wyzwalaną przez wyzwalacz Event Grid. Utwórz relację między wyzwalaczem a funkcją, tworząc subskrypcję zdarzeń dla zdarzeń telemetrii urządzenia IoT Hub. Gdy wystąpi zdarzenie telemetrii urządzenia, funkcja jest wywoływana jako punkt końcowy i otrzymuje odpowiednie dane dla urządzenia, które zostało wcześniej zarejestrowane w IoT Hub.

Oto [kod skryptu w języku C#, który będzie zawierał funkcję](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx).

Teraz Skonfiguruj funkcję platformy Azure.

1. Na pulpicie nawigacyjnym Azure Portal wybierz pozycję **Utwórz zasób**. Wpisz **aplikacja funkcji** w polu tekstowym Wyszukaj. Wybierz pozycję **aplikacja funkcji**  >  **Utwórz**.

1. Na stronie Tworzenie **aplikacja funkcji** Nadaj nazwę aplikacji funkcji. W obszarze **Grupa zasobów** wybierz pozycję **ContosoRental** z listy rozwijanej. Wybierz pozycję **.NET Core** jako **stos środowiska uruchomieniowego**. W dolnej części strony wybierz pozycję **Dalej: Hosting >**.

    :::image type="content" source="./media/tutorial-iot-hub-maps/rental-app.png" alt-text="Zrzut ekranu przedstawiający tworzenie aplikacji funkcji.":::

1. W polu **konto magazynu** wybierz konto magazynu utworzone w [ramach tworzenia konta usługi Azure Storage](#create-an-azure-storage-account). Wybierz pozycję **Przejrzyj i utwórz**.

1. Przejrzyj szczegóły aplikacji funkcji i wybierz pozycję **Utwórz**.

1. Po utworzeniu aplikacji należy dodać do niej funkcję. Przejdź do aplikacji funkcji. Wybierz okienko **funkcje** . W górnej części strony wybierz pozycję **+ Dodaj**. Zostanie wyświetlony panel szablonu funkcji. Przewiń w dół panel i wybierz polecenie **Azure Event Grid wyzwalacza**.

     >[!IMPORTANT]
    > **Wyzwalacz usługi Azure Event Hub** i szablon **wyzwalacza Azure Event Grid** mają podobne nazwy. Upewnij się, że wybierasz szablon **wyzwalacza Azure Event Grid** .

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create.png" alt-text="Zrzut ekranu przedstawiający tworzenie funkcji.":::

1. Nadaj funkcji nazwę. W tym samouczku użyjesz nazwy, *GetGeoFunction*, ale ogólnie możesz użyć dowolnej nazwy. Wybierz pozycję **Utwórz funkcję**.

1. W menu po lewej stronie Wybierz okienko **kod + testowanie** . Skopiuj i wklej [skrypt C#](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) do okna kod.

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-code.png" alt-text="Kopiuj/zrzut ekranu przedstawiający wklejanie kodu do okna funkcji.":::

1. W kodzie C# Zastąp następujące parametry:
    * Zastąp **SUBSCRIPTION_KEY** podstawowym kluczem subskrypcji konta usługi Azure Maps.
    * Zastąp **UDID** z elementem `udid` geoogrodzenia przekazanym w obszarze [Przekaż geoogrodzenie](#upload-a-geofence).
    * `CreateBlobAsync`Funkcja w skrypcie tworzy obiekt BLOB dla zdarzenia na koncie magazynu danych. Zastąp **ACCESS_KEY**, **ACCOUNT_NAME** i **STORAGE_CONTAINER_NAME** kluczem dostępu do konta magazynu, nazwą konta i kontenerem magazynu danych. Te wartości zostały wygenerowane podczas tworzenia konta magazynu w [ramach tworzenia konta usługi Azure Storage](#create-an-azure-storage-account).

1. W menu po lewej stronie Wybierz okienko **integracja** . Wybierz **wyzwalacz Event Grid** na diagramie. Wpisz nazwę wyzwalacza, *eventGridEvent* i wybierz pozycję **utwórz subskrypcję Event Grid**.

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-integration.png" alt-text="Zrzut ekranu przedstawiający Dodawanie subskrypcji zdarzeń.":::

1. Wypełnij szczegóły subskrypcji. Nazwij subskrypcję zdarzeń. W obszarze **schemat zdarzenia** wybierz pozycję **Event Grid schemat**. W obszarze **typy tematów** wybierz pozycję **konta usługi Azure IoT Hub**. W obszarze **Grupa zasobów** wybierz grupę zasobów utworzoną na początku tego samouczka. W obszarze **zasób** wybierz Centrum IoT utworzone w obszarze "Tworzenie usługi Azure IoT Hub". W obszarze **Filtruj do typów zdarzeń** wybierz pozycję dane **telemetryczne urządzenia**.

   Po wybraniu tych opcji zobaczysz zmianę **typu tematu** na **IoT Hub**. W polu **nazwa tematu systemu** można użyć nazwy, która jest taka sama jak nazwa zasobu. Na koniec w sekcji **Szczegóły punktu końcowego** wybierz pozycję **Wybierz punkt końcowy**. Zaakceptuj wszystkie ustawienia i wybierz pozycję **Potwierdź wybór**.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription.png" alt-text="Zrzut ekranu przedstawiający subskrypcję zdarzeń tworzenia.":::

1. Przejrzyj ustawienia. Upewnij się, że punkt końcowy określa funkcję utworzoną na początku tej sekcji. Wybierz przycisk **Utwórz**.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription-confirm.png" alt-text="Zrzut ekranu przedstawiający potwierdzenie tworzenia subskrypcji zdarzeń.":::

1. Teraz wróć do panelu **Edytowanie wyzwalacza** . Wybierz pozycję **Zapisz**.

## <a name="filter-events-by-using-iot-hub-message-routing"></a>Filtrowanie zdarzeń przy użyciu IoT Hub Routing komunikatów

Po dodaniu subskrypcji Event Grid do funkcji platformy Azure zostanie automatycznie utworzona trasa obsługi komunikatów w określonym Centrum IoT Hub. Routing komunikatów umożliwia kierowanie różnych typów danych do różnych punktów końcowych. Można na przykład kierować komunikaty telemetryczne urządzenia, zdarzenia cyklu życia urządzenia i zdarzenia zmiany z przędzą urządzenia. Aby uzyskać więcej informacji, zobacz [używanie IoT Hub Routing komunikatów](../iot-hub/iot-hub-devguide-messages-d2c.md).

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-route.png" alt-text="Zrzut ekranu przedstawiający Routing komunikatów w usłudze IoT Hub.":::

W Twoim przykładowym scenariuszu chcesz otrzymywać tylko komunikaty po przeniesieniu samochodu wynajmu. Utwórz kwerendę routingu, aby odfiltrować zdarzenia, `Engine` w których właściwość jest równa **"on"**. Aby utworzyć zapytanie routingu, wybierz trasę **RouteToEventGrid** i Zastąp **zapytanie routingu** z **"aparat =" na ""**. Następnie wybierz pozycję **Zapisz**. Teraz Centrum IoT publikuje tylko dane telemetryczne urządzenia, na których jest włączony aparat.

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-filter.png" alt-text="Zrzut ekranu przedstawiający komunikaty dotyczące filtru routingu.":::

>[!TIP]
>Istnieją różne sposoby wykonywania zapytań dotyczących komunikatów przesyłanych z urządzeń do chmury. Aby dowiedzieć się więcej na temat składni routingu wiadomości, zobacz [IoT Hub Routing komunikatów](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

## <a name="send-telemetry-data-to-iot-hub"></a>Wyślij dane telemetryczne do IoT Hub

Gdy funkcja platformy Azure jest uruchomiona, można teraz wysyłać dane telemetryczne do centrum IoT Hub, które będą kierować do Event Grid. Użyj aplikacji języka C#, aby symulować dane lokalizacji na urządzeniu w ramach samochodu w kabinie. Do uruchomienia aplikacji jest potrzebny zestaw .NET Core SDK 2.1.0 lub nowszy na komputerze deweloperskim. Wykonaj następujące kroki, aby wysłać symulowane dane telemetryczne do centrum IoT:

1. Jeśli jeszcze tego nie zrobiono, Pobierz projekt C# [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) .

2. Otwórz `simulatedCar.cs` plik w wybranym edytorze tekstów i Zastąp wartość `connectionString` atrybutem zapisanym podczas rejestrowania urządzenia. Zapisz zmiany w pliku.

3. Upewnij się, że na maszynie jest zainstalowany program .NET Core. W oknie terminalu lokalnego przejdź do folderu głównego projektu C# i uruchom następujące polecenie, aby zainstalować wymagane pakiety dla aplikacji symulowanego urządzenia:

    ```cmd/sh
    dotnet restore
    ```

4. W tym samym terminalu uruchom następujące polecenie, aby skompilować i uruchomić aplikację symulacji samochodu wynajmu:

    ```cmd/sh
    dotnet run
    ```


  Terminal lokalny powinien wyglądać podobnie do przedstawionego poniżej.

:::image type="content" source="./media/tutorial-iot-hub-maps/terminal.png" alt-text="Zrzut ekranu przedstawiający dane wyjściowe terminalu.":::

Jeśli teraz otworzysz kontener usługi BLOB Storage, możesz zobaczyć cztery obiekty blob dla lokalizacji, w których pojazd znajduje się poza ogrodzeniem.

:::image type="content" source="./media/tutorial-iot-hub-maps/blob.png" alt-text="Zrzut ekranu przedstawiający Wyświetlanie obiektów BLOB w kontenerze.":::

Poniższa mapa przedstawia cztery punkty lokalizacji pojazdu poza ogrodzeniem. Każda lokalizacja była rejestrowana w regularnych odstępach czasu.

:::image type="content" source="./media/tutorial-iot-hub-maps/violation-map.png" alt-text="Zrzut ekranu przedstawiający mapę naruszeń.":::

## <a name="explore-azure-maps-and-iot"></a>Eksplorowanie Azure Maps i IoT

Aby poznać Azure Maps interfejsów API używanych w tym samouczku, zobacz:

* [Uzyskaj zwrotny adres wyszukiwania](/rest/api/maps/search/getsearchaddressreverse)
* [Pobierz geoogrodzenie](/rest/api/maps/spatial/getgeofence)

Aby uzyskać pełną listę Azure Maps interfejsów API REST, zobacz:

* [Interfejsy API REST Azure Maps](/rest/api/maps/spatial/getgeofence)

* [IoT Plug and Play](../iot-pnp/index.yml)

Aby uzyskać listę urządzeń z certyfikatem platformy Azure dla IoT, odwiedź stronę:

* [Certyfikowane urządzenia platformy Azure](https://catalog.azureiotsolutions.com/)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Brak zasobów, które wymagają oczyszczenia.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o sposobach przesyłania danych telemetrycznych z urządzenia do chmury oraz inne sposoby na ich temat, zobacz:


> [!div class="nextstepaction"]
> [Wysyłanie danych telemetrycznych z urządzenia](../iot-hub/quickstart-send-telemetry-dotnet.md)