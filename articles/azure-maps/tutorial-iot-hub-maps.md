---
title: 'Samouczek: implementowanie analizy przestrzennej IoT | Microsoft Azure Maps'
description: Samouczek dotyczący sposobu integrowania aplikacji IoT Hub interfejsami API usługi Microsoft Azure Maps
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/01/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 9ebc6e266c93e55bc250e8450356f8b695dd9080
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714996"
---
# <a name="tutorial-implement-iot-spatial-analytics-by-using-azure-maps"></a>Samouczek: implementowanie analizy przestrzennej IoT przy użyciu Azure Maps

W scenariuszu IoT typowe jest przechwytywanie i śledzenie odpowiednich zdarzeń występujących w przestrzeni i czasie. Przykłady obejmują zarządzanie flotą, śledzenie zasobów, mobilność i aplikacje inteligentnego miasta. Ten samouczek przeprowadzi Cię przez rozwiązanie, które śledzi ruch używanych wypożyczeń samochodów przy użyciu interfejsów API Azure Maps API.

W tym samouczku:

> [!div class="checklist"]
> * Utwórz konto usługi Azure Storage, aby rejestrować dane śledzenia samochodu.
> * Przekaż geofence do usługi Azure Maps usługa danych (wersja zapoznawcza) przy użyciu interfejsu API przekazywania danych.
> * Utwórz centrum w Azure IoT Hub i zarejestruj urządzenie.
> * Utwórz funkcję w u Azure Functions, implementując logikę biznesową opartą na Azure Maps analizy przestrzennej.
> * Subskrybuj zdarzenia telemetrii urządzenia IoT z funkcji platformy Azure za pośrednictwem Azure Event Grid.
> * Filtrowanie zdarzeń telemetrii przy użyciu IoT Hub routingu komunikatów.

## <a name="prerequisites"></a>Wymagania wstępne

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. [Utwórz Azure Maps konto.](quick-demo-map-app.md#create-an-azure-maps-account)

3. [Uzyskaj podstawowy klucz subskrypcji](quick-demo-map-app.md#get-the-primary-key-for-your-account), znany również jako klucz podstawowy lub klucz subskrypcji. Aby uzyskać więcej informacji, zobacz [Zarządzanie uwierzytelnianiem w Azure Maps](how-to-manage-authentication.md).

4. [Utwórz grupę zasobów](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups). W tym samouczku nadamy grupie zasobów *nazwę ContosoRental,* ale możesz wybrać nazwę, która Ci się podoba.

5. Pobierz projekt [rentalCarSimulation w języku C#.](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation)

W tym samouczku jest [używana aplikacja Postman,](https://www.postman.com/) ale możesz wybrać inne środowisko projektowe interfejsu API.

## <a name="use-case-rental-car-tracking"></a>Przypadek użycia: śledzenie wypożyczanych samochodów

Załóżmy, że firma wypożyczaca samochody chce rejestrować informacje o lokalizacji, przebytą odległość i stan uruchomienia dla swoich samochodów wypożyczanych. Firma chce również przechowywać te informacje za każdym razem, gdy samochód opuści prawidłowy autoryzowany region geograficzny.

Wypożyczone samochody są wyposażone w urządzenia IoT, które regularnie wysyłają dane telemetryczne do IoT Hub. Dane telemetryczne zawierają bieżącą lokalizację i wskazują, czy silnik samochodu jest uruchomiony. Schemat lokalizacji urządzenia jest zgodny ze schematem Plug and Play IoT [dla danych geoprzestrzennych.](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v1-preview/schemas/geospatial.md) Schemat telemetrii urządzenia do wypożyczenia samochodu wygląda podobnie do następującego kodu JSON:

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

W tym samouczku będziesz śledzić tylko jeden pojazd. Po skonfigurowaniu usług platformy Azure musisz pobrać projekt [rentalCarSimulation w języku C#,](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) aby uruchomić symulator pojazdów. Cały proces, od zdarzenia do wykonania funkcji, został podsumowany w następujących krokach:

1. Urządzenie w pojazdów wysyła dane telemetryczne do IoT Hub.

2. Jeśli silnik samochodu jest uruchomiony, koncentrator publikuje dane telemetryczne w Event Grid.

3. Funkcja platformy Azure jest wyzwalana z powodu subskrypcji zdarzeń telemetrii urządzenia.

4. Funkcja rejestruje współrzędne lokalizacji urządzenia pojazdu, czas zdarzenia i identyfikator urządzenia. Następnie używa interfejsu [API get geofence Spatial Geofence,](/rest/api/maps/spatial/getgeofence) aby ustalić, czy samochód wybiegł poza geofence. Jeśli został on przenoszony poza granice geofence, funkcja przechowuje dane lokalizacji odebrane ze zdarzenia w kontenerze obiektów blob. Funkcja wysyła również zapytanie do funkcji [Wyszukiwania adresu zwrotnego,](/rest/api/maps/search/getsearchaddressreverse) aby przetłumaczyć lokalizację współrzędną na adres ulicy, i zapisuje ją z pozostałymi danymi lokalizacji urządzenia.

Na poniższym diagramie przedstawiono ogólne omówienie systemu.

   :::image type="content" source="./media/tutorial-iot-hub-maps/system-diagram.png" border="false" alt-text="Diagram przedstawiający przegląd systemu.":::

Na poniższej ilustracji wyróżniliśmy obszar geofence na niebiesko. Trasa wypożyczenia samochodu jest wskazywana przez zieloną linię.

   :::image type="content" source="./media/tutorial-iot-hub-maps/geofence-route.png" border="false" alt-text="Rysunek przedstawiający trasę geofence.":::

## <a name="create-an-azure-storage-account"></a>Tworzenie konta usługi Azure Storage

Aby przechowywać dane śledzenia naruszenia samochodu, utwórz konto magazynu ogólnego przeznaczenia w wersji [2](../storage/common/storage-account-overview.md) w grupie zasobów. Jeśli nie utworzono grupy zasobów, postępuj zgodnie z instrukcjami w tece [dotyczącej tworzenia grupy zasobów.](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) W tym samouczku nadasz grupie zasobów *nazwę ContosoRental.*

Aby utworzyć konto magazynu, postępuj zgodnie z instrukcjami w [tece Create a storage account (Tworzenie konta magazynu).](../storage/common/storage-account-create.md?tabs=azure-portal) W tym samouczku nadaj kontu magazynu nazwę *contosorentalstorage,* ale ogólnie możesz nazwać je w imieniu wszystkich osób.

Po pomyślnym utworzeniu konta magazynu należy utworzyć kontener do przechowywania danych rejestrowania.

1. Przejdź do nowo utworzonego konta magazynu. W sekcji **Podstawowe** elementy wybierz link **Kontenery.**

    :::image type="content" source="./media/tutorial-iot-hub-maps/containers.png" alt-text="Zrzut ekranu przedstawiający kontenery dla magazynu obiektów blob.":::

2. W lewym górnym rogu wybierz pozycję **+ Kontener**. Po prawej stronie przeglądarki zostanie wyświetlony panel. Nadaj *kontenerowi nazwę contoso-rental-logs* i wybierz pozycję **Utwórz**.

     :::image type="content" source="./media/tutorial-iot-hub-maps/container-new.png" alt-text="Zrzut ekranu przedstawiający tworzenie kontenera obiektów blob.":::

3. Przejdź do **okienka Klucze** dostępu na koncie magazynu i  skopiuj wartości **Nazwa** konta magazynu i Klucz w **sekcji klucz1.** Obie te wartości są potrzebne w sekcji "Tworzenie funkcji platformy Azure i dodawanie Event Grid subskrypcji".

    :::image type="content" source="./media/tutorial-iot-hub-maps/access-keys.png" alt-text="Zrzut ekranu przedstawiający kopiowanie nazwy i klucza konta magazynu.":::

## <a name="upload-a-geofence"></a>Przekazywanie geofence

Następnie użyj aplikacji [Postman,](https://www.getpostman.com) aby [przekazać geofence](./geofence-geojson.md) do Azure Maps. Geofence definiuje autoryzowany obszar geograficzny dla wypożyczanych pojazdów. Będziesz używać geofence w funkcji platformy Azure, aby określić, czy samochód został przeniesiony poza obszar geofence.

Wykonaj następujące kroki, aby przekazać geofence przy użyciu interfejsu API przekazywania Azure Maps danych: 

1. Otwórz aplikację Postman i wybierz pozycję **Nowy.** W **oknie Create New (Tworzenie** nowego) wybierz pozycję **Collection (Kolekcja).** Nadaj kolekcji nazwę i wybierz **pozycję Utwórz**.

2. Aby utworzyć żądanie, ponownie wybierz **pozycję** Nowy. W **oknie Create New (Tworzenie** nowego) wybierz **pozycję Request (Żądanie)** i wprowadź nazwę żądania. Wybierz kolekcję utworzoną w poprzednim kroku, a następnie wybierz pozycję **Zapisz**.

3. Wybierz metodę **HTTP POST** na karcie konstruktora i wprowadź następujący adres URL, aby przekazać geofence do interfejsu API przekazywania danych. Pamiętaj, aby zastąpić `{subscription-key}` element podstawowym kluczem subskrypcji.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    W ścieżce adresu URL wartość względem parametru reprezentuje `geojson` format `dataFormat` przekazywanych danych.

4. Wybierz **pozycję Treść**  >  **nieprzetworzona** jako format danych wejściowych, a następnie wybierz pozycję **JSON** z listy rozwijanej. [Otwórz plik danych JSON](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4)i skopiuj dane JSON do sekcji treść. Wybierz pozycję **Wyślij**.

5. Wybierz **pozycję Wyślij** i poczekaj na przetwarzanie żądania. Po zakończeniu żądania przejdź do karty **Nagłówki** odpowiedzi. Skopiuj wartość klucza **lokalizacji,** czyli `status URL` .

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. Aby sprawdzić stan wywołania interfejsu API, utwórz żądanie **GET** HTTP dla `status URL` . W celu uwierzytelnienia musisz dołączyć podstawowy klucz subskrypcji do adresu URL. Żądanie **GET** powinno wyglądać podobnie do następującego adresu URL:

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={subscription-key}
   ```
   
7. Po **pomyślnym** zakończeniu żądania GET HTTP zwracany jest . `resourceLocation` Plik `resourceLocation` zawiera unikatowy identyfikator `udid` przekazanej zawartości. Skopiuj tę `udid` kopię do późniejszego użycia w tym samouczku.

      ```json
      {
          "status": "Succeeded",
          "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
      }
      ```

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

IoT Hub bezpieczną i niezawodną dwukierunkową komunikację między aplikacją IoT i urządzeniami, które zarządza. W tym samouczku chcesz uzyskać informacje z urządzenia w pojazdu, aby określić lokalizację wypożyczenia samochodu. W tej sekcji utworzysz centrum IoT w grupie *zasobów ContosoRental.* To centrum będzie odpowiedzialne za publikowanie zdarzeń telemetrii urządzenia.

> [!NOTE]
> Możliwość publikowania zdarzeń telemetrii urządzenia na Event Grid jest obecnie dostępna w wersji zapoznawczej. Ta funkcja jest dostępna we wszystkich regionach z wyjątkiem następujących regionów: Wschodnie stany USA, Zachodnie stany USA, Europa Zachodnia, Azure Government, Azure (Chiny) — 21Vianet i Azure (Niemcy).

Aby utworzyć centrum IoT w grupie *zasobów ContosoRental,* wykonaj kroki opisane w teście tworzenia [centrum IoT.](../iot-hub/quickstart-send-telemetry-dotnet.md#create-an-iot-hub)

## <a name="register-a-device-in-your-iot-hub"></a>Rejestrowanie urządzenia w centrum IoT

Urządzenia nie mogą łączyć się z centrum IoT, chyba że są zarejestrowane w rejestrze tożsamości centrum IoT. W tym miejscu utworzysz jedno urządzenie o nazwie *InVehicleDevice.* Aby utworzyć i zarejestrować urządzenie w centrum IoT Hub, wykonaj kroki opisane w teście Rejestrowanie nowego urządzenia w centrum [IoT.](../iot-hub/iot-hub-create-through-portal.md#register-a-new-device-in-the-iot-hub) Pamiętaj o skopiowaniu podstawowych parametrów połączenia urządzenia. Będzie on potrzebny później.

## <a name="create-a-function-and-add-an-event-grid-subscription"></a>Tworzenie funkcji i dodawanie subskrypcji Event Grid subskrypcji

Azure Functions to bez serwera usługa obliczeniowa, która umożliwia uruchamianie małych fragmentów kodu ("funkcji") bez konieczności jawnego aprowizowania infrastruktury obliczeniowej ani zarządzania nimi. Aby dowiedzieć się więcej, [zobacz Azure Functions](../azure-functions/functions-overview.md).

Funkcja jest wyzwalana przez określone zdarzenie. W tym miejscu utworzysz funkcję wyzwalaną przez wyzwalacz Event Grid wyzwalacza. Utwórz relację między wyzwalaczem i funkcją, tworząc subskrypcję zdarzeń dla IoT Hub telemetrii urządzenia. Gdy wystąpi zdarzenie telemetrii urządzenia, funkcja jest wywoływana jako punkt końcowy i odbiera odpowiednie dane dla urządzenia, które zostało wcześniej zarejestrowane w IoT Hub.

Oto kod skryptu [języka C#, który funkcja będzie zawierać](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx).

Teraz skonfiguruj funkcję platformy Azure.

1. Na pulpicie Azure Portal nawigacyjnym wybierz **pozycję Utwórz zasób.** Wpisz **Aplikacja funkcji** w polu tekstowym wyszukiwania. Wybierz **pozycję Utwórz aplikację**  >  **funkcji.**

1. Na stronie **tworzenia aplikacji** funkcji nadaj aplikacji funkcji nazwę. W **obszarze Grupa zasobów** wybierz pozycję **ContosoRental** z listy rozwijanej. Wybierz **pozycję .NET Core** jako stos środowiska **uruchomieniowego.** W dolnej części strony wybierz pozycję **Dalej: Hosting >.**

    :::image type="content" source="./media/tutorial-iot-hub-maps/rental-app.png" alt-text="Zrzut ekranu przedstawiający tworzenie aplikacji funkcji.":::

1. W **przypadku konta magazynu** wybierz konto magazynu utworzone w ramach tworzenia konta usługi Azure [Storage.](#create-an-azure-storage-account) Wybierz pozycję **Przejrzyj i utwórz**.

1. Przejrzyj szczegóły aplikacji funkcji, a następnie wybierz pozycję **Utwórz.**

1. Po utworzeniu aplikacji należy dodać do niego funkcję. Przejdź do aplikacji funkcji. Wybierz **okienko** Funkcje. W górnej części strony wybierz pozycję **+ Dodaj**. Zostanie wyświetlony panel szablonu funkcji. Przewiń w dół panel i wybierz pozycję **Azure Event Grid wyzwalacza**.

     >[!IMPORTANT]
    > Szablony **wyzwalacza usługi Azure Event Hub** i Azure Event Grid **wyzwalacza** mają podobne nazwy. Upewnij się, że wybierasz **szablon Azure Event Grid Wyzwalacz.**

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create.png" alt-text="Zrzut ekranu przedstawiający tworzenie funkcji.":::

1. Nadaj funkcji nazwę. W tym samouczku użyjesz nazwy *GetGeoFunction,* ale ogólnie możesz użyć dowolnej nazwy. Wybierz **pozycję Utwórz funkcję**.

1. W menu po lewej stronie wybierz okienko **Kod i test.** Skopiuj i wklej skrypt [języka C#](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) w oknie kodu.

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-code.png" alt-text="Kopiowanie/zrzut ekranu przedstawiający wklejanie kodu w oknie funkcji.":::

1. W kodzie języka C# zastąp następujące parametry:
    * Zastąp **SUBSCRIPTION_KEY** kluczem subskrypcji Azure Maps podstawowego konta.
    * Zastąp **część UDID** za pomocą lokalizacji geofence przekazanej w części `udid` Przekazywanie [geofence](#upload-a-geofence).
    * Funkcja `CreateBlobAsync` w skrypcie tworzy obiekt blob na zdarzenie na koncie magazynu danych. Zastąp **ACCESS_KEY**, **ACCOUNT_NAME** i **STORAGE_CONTAINER_NAME** kluczem dostępu konta magazynu, nazwą konta i kontenerem magazynu danych. Te wartości zostały wygenerowane podczas tworzenia konta magazynu w ramach tworzenia [konta usługi Azure Storage.](#create-an-azure-storage-account)

1. W menu po lewej stronie wybierz **okienko Integracja.** Wybierz **Event Grid wyzwalacza** na diagramie. Wpisz nazwę wyzwalacza *eventGridEvent* i wybierz pozycję Utwórz Event Grid **subskrypcji.**

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-integration.png" alt-text="Zrzut ekranu przedstawiający dodawanie subskrypcji zdarzeń.":::

1. Wypełnij szczegóły subskrypcji. Nadaj nazwę subskrypcji zdarzeń. W **przypadku opcji Schemat** zdarzeń wybierz Event Grid **schemacie**. W **przypadku typów tematów** wybierz pozycję Azure IoT Hub **tematu**. W **przypadku grupy** zasobów wybierz grupę zasobów utworzoną na początku tego samouczka. W **przypadku** opcji Zasób wybierz centrum IoT utworzone w ramach tworzenia centrum Azure IoT Hub. W **przypadku opcji Filtruj do typów zdarzeń** wybierz pozycję **Telemetria urządzenia.**

   Po wybraniu tych opcji typ tematu zmieni się **na** **IoT Hub**. W **przypadku nazwy tematu** systemowego możesz użyć tej samej nazwy co zasób. Na koniec w sekcji **Szczegóły punktu końcowego** wybierz **pozycję Wybierz punkt końcowy**. Zaakceptuj wszystkie ustawienia i wybierz pozycję **Potwierdź wybór.**

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription.png" alt-text="Zrzut ekranu przedstawiający tworzenie subskrypcji zdarzeń.":::

1. Przejrzyj ustawienia. Upewnij się, że punkt końcowy określa funkcję utworzoną na początku tej sekcji. Wybierz przycisk **Utwórz**.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription-confirm.png" alt-text="Zrzut ekranu przedstawiający potwierdzenie utworzenia subskrypcji zdarzeń.":::

1. Teraz wrócisz do panelu **Edytowanie wyzwalacza.** Wybierz pozycję **Zapisz**.

## <a name="filter-events-by-using-iot-hub-message-routing"></a>Filtrowanie zdarzeń przy użyciu IoT Hub routingu komunikatów

Po dodaniu subskrypcji Event Grid do funkcji platformy Azure trasa obsługi komunikatów jest tworzona automatycznie w określonym centrum IoT. Routing komunikatów umożliwia kierowanie różnych typów danych do różnych punktów końcowych. Można na przykład rozsyłać komunikaty telemetrii urządzenia, zdarzenia cyklu życia urządzenia i zdarzenia zmiany bliźniaczej reprezentacji urządzenia. Aby uzyskać więcej informacji, zobacz [Używanie IoT Hub routingu komunikatów.](../iot-hub/iot-hub-devguide-messages-d2c.md)

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-route.png" alt-text="Zrzut ekranu przedstawiający routing komunikatów w centrum IoT.":::

W przykładowym scenariuszu chcesz odbierać komunikaty tylko wtedy, gdy samochód jest przejechyny. Utwórz zapytanie routingu, aby filtrować zdarzenia, w których `Engine` właściwość ma wartość **"ON".** Aby utworzyć zapytanie dotyczące routingu, wybierz **trasę RouteToEventGrid** i zastąp zapytanie **routingu** **"Engine='ON'".** Następnie wybierz pozycję **Zapisz**. Teraz centrum IoT publikuje telemetrię urządzenia tylko tam, gdzie aparat jest wł.

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-filter.png" alt-text="Zrzut ekranu przedstawiający filtrowanie komunikatów routingu.":::

>[!TIP]
>Istnieją różne sposoby wykonywania zapytań o komunikaty z urządzenia IoT do chmury. Aby dowiedzieć się więcej na temat składni routingu komunikatów, [zobacz IoT Hub routing komunikatów](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

## <a name="send-telemetry-data-to-iot-hub"></a>Wysyłanie danych telemetrycznych do IoT Hub

Gdy funkcja platformy Azure jest uruchomiona, możesz teraz wysyłać dane telemetryczne do centrum IoT, które będzie je kierowane do Event Grid. Symulowanie danych lokalizacji dla wypożyczanego samochodu przy użyciu aplikacji w języku C#. Do uruchomienia aplikacji potrzebny jest program zestaw .NET Core SDK 2.1.0 lub nowszy na komputerze dewelopera. Wykonaj następujące kroki, aby wysłać symulowane dane telemetryczne do centrum IoT:

1. Jeśli jeszcze tego nie zrobiono, pobierz projekt [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) w języku C#.

2. Otwórz plik w edytorze tekstów i zastąp wartość pliku wartością zapisaną `simulatedCar.cs` `connectionString` podczas rejestrowania urządzenia. Zapisz zmiany w pliku.

3. Upewnij się, że na maszynie zainstalowano program .NET Core. W lokalnym oknie terminalu przejdź do folderu głównego projektu C# i uruchom następujące polecenie, aby zainstalować wymagane pakiety dla aplikacji urządzenia symulowanego:

    ```cmd/sh
    dotnet restore
    ```

4. W tym samym terminalu uruchom następujące polecenie, aby skompilować i uruchomić aplikację symulacji wypożyczenia samochodu:

    ```cmd/sh
    dotnet run
    ```


  Lokalny terminal powinien wyglądać podobnie do poniższego.

:::image type="content" source="./media/tutorial-iot-hub-maps/terminal.png" alt-text="Zrzut ekranu przedstawiający dane wyjściowe terminalu.":::

Jeśli teraz otworzysz kontener magazynu obiektów blob, zobaczysz cztery obiekty blob dla lokalizacji, w których pojazd był poza geofencem.

:::image type="content" source="./media/tutorial-iot-hub-maps/blob.png" alt-text="Zrzut ekranu przedstawiający wyświetlanie obiektów blob wewnątrz kontenera.":::

Na poniższej mapie przedstawiono cztery punkty lokalizacji pojazdów poza geofencem. Każda lokalizacja była rejestrowana w regularnych odstępach czasu.

:::image type="content" source="./media/tutorial-iot-hub-maps/violation-map.png" alt-text="Zrzut ekranu przedstawiający mapę naruszenia.":::

## <a name="explore-azure-maps-and-iot"></a>Eksplorowanie Azure Maps i IoT

Aby poznać interfejsy API Azure Maps używane w tym samouczku, zobacz:

* [Uzyskiwanie odwrotnego adresu wyszukiwania](/rest/api/maps/search/getsearchaddressreverse)
* [Uzyskiwanie geofence](/rest/api/maps/spatial/getgeofence)

Aby uzyskać pełną listę Azure Maps API REST, zobacz:

* [Azure Maps API REST](/rest/api/maps/spatial/getgeofence)

* [IoT Plug and Play](../iot-pnp/index.yml)

Aby uzyskać listę urządzeń z certyfikatem platformy Azure dla IoT, odwiedź stronę:

* [Certyfikowane urządzenia platformy Azure](https://devicecatalog.azure.com/)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Nie ma żadnych zasobów, które wymagają czyszczenia.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o tym, jak wysyłać dane telemetryczne z urządzenia do chmury i na drugi sposób, zobacz:


> [!div class="nextstepaction"]
> [Wysyłanie danych telemetrycznych z urządzenia](../iot-hub/quickstart-send-telemetry-dotnet.md)