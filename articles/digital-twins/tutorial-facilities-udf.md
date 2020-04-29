---
title: 'Samouczek: monitorowanie przestrzeni urządzenia IoT — usługa Azure Digital bliźniaczych reprezentacji | Microsoft Docs'
description: Dowiedz się, jak aprowizować zasoby przestrzenne i monitorować warunki pracy za pomocą usługi Azure Digital Twins, wykonując czynności opisane w tym samouczku.
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 01/10/2020
ms.openlocfilehash: 6cf6a8f7de181a81d60028e33ba2631815c8ca04
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75895357"
---
# <a name="tutorial-provision-your-building-and-monitor-working-conditions-with-azure-digital-twins-preview"></a>Samouczek: udostępnianie warunków roboczych tworzenia i monitorowania za pomocą usługi Azure Digital bliźniaczych reprezentacji Preview

W tym samouczku pokazano, jak używać usługi Azure Digital bliźniaczych reprezentacji Preview do monitorowania obszarów pod kątem wymagań dotyczących temperatury i poziomu komfortu. Po [skonfigurowaniu przykładowego budynku](tutorial-facilities-setup.md) możesz zaaprowizować swój budynek i uruchamiać funkcje niestandardowe na danych z czujników, wykonując czynności opisane w tym samouczki.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Definiowanie warunków, które mają być monitorowane.
> * Tworzenie funkcji zdefiniowanej przez użytkownika (UDF, User Defined Function).
> * Symulowanie danych z czujników.
> * Pobieranie wyników funkcji zdefiniowanej przez użytkownika.

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku przyjęto założenie, że [ukończono konfigurację usługi Azure Digital Twins](tutorial-facilities-setup.md). Przed kontynuowaniem upewnij się, że masz:

- [Konto platformy Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uruchomione wystąpienie usługi Digital Twins. 
- Pobrane i wyodrębnione [przykłady usługi Digital Twins w języku C#](https://github.com/Azure-Samples/digital-twins-samples-csharp) na maszynie roboczej. 
- [Zestaw SDK .NET Core w wersji 2.1.403 lub nowszej](https://www.microsoft.com/net/download) na komputerze deweloperskim w celu skompilowania i uruchomienia przykładu. Uruchom polecenie `dotnet --version`, aby sprawdzić, czy zainstalowano prawidłową wersję. 
- Program [Visual Studio Code](https://code.visualstudio.com/) umożliwiający eksplorowanie przykładowego kodu. 

>[!TIP]
> Jeśli zainicjowano nowe wystąpienie, użyj unikatowej nazwy wystąpienia bliźniaczych reprezentacji Digital.

## <a name="define-conditions-to-monitor"></a>Definiowanie warunków, które mają być monitorowane

Możesz zdefiniować zestaw określonych warunków nazywanych elementami *matcher*, które mają być monitorowane w urządzeniu lub w danych z czujników. Następnie można zdefiniować *funkcje zdefiniowane przez użytkownika*. Funkcje zdefiniowane przez użytkownika umożliwiają wykonywanie niestandardowej logiki na danych pochodzących z przestrzeni i urządzeń, gdy wystąpią warunki określone w elementach matcher. Aby uzyskać więcej informacji, przeczytaj [Przetwarzanie danych i funkcje zdefiniowane przez użytkownika](concepts-user-defined-functions.md). 

W przykładowym projekcie **occupancy-quickstart** otwórz plik **src\actions\provisionSample.yaml** w programie Visual Studio Code. Zwróć uwagę na sekcję, która zaczyna się od typu **matchers**. Każdy wpis w ramach tego typu tworzy element matcher z określonym parametrem **Name** (nazwa). Element matcher będzie monitorował czujnik typu **dataTypeValue**. Zwróć uwagę, że jest on związany z przestrzenią o nazwie *Focus Room A1*, w której znajduje się węzeł **devices** zawierający kilka czujników. Aby aprowizować element matcher, który będzie śledzić jeden z tych czujników, należy upewnić się, że jego typ **dataTypeValue** jest zgodny z typem **dataType** czujnika. 

Dodaj następujący element matcher poniżej istniejących elementów matcher. Upewnij się, że klucze są dopasowane, a spacje nie zostały zastąpione znakami tabulacji. Te wiersze znajdują się również w pliku *provisionSample.yaml* jako komentarze. Możesz przenieść je poza komentarze, usuwając znak `#` na początku wiersza.

```yaml
      - name: Matcher Temperature
        dataTypeValue: Temperature
```

Ten odpowiednik śledzi `SAMPLE_SENSOR_TEMPERATURE` czujnik dodany w [pierwszym samouczku](tutorial-facilities-setup.md). 

## <a name="create-a-user-defined-function"></a>Tworzenie funkcji zdefiniowanej przez użytkownika

Funkcje zdefiniowane przez użytkownika umożliwiają dostosowanie sposobu przetwarzania danych z czujników. Są one niestandardowym kodem JavaScript, który można uruchamiać w ramach wystąpienia usługi Azure Digital Twins, gdy wystąpią warunki określone w elementach matcher. Możesz utworzyć elementy matcher i funkcje zdefiniowane przez użytkownika dla każdego czujnika, którego chcesz monitorować. Aby uzyskać więcej informacji, przeczytaj [Przetwarzanie danych i funkcje zdefiniowane przez użytkownika](concepts-user-defined-functions.md). 

W przykładowym pliku *provisionSample. YAML* odszukaj sekcję rozpoczynającą się od typu **UserDefinedFunctions**. Ta sekcja służy do aprowizowania funkcji zdefiniowanej przez użytkownika o określonej wartości parametru **Name**. Funkcja ta działa na listę elementów matcher wymienionych w sekcji **matcherNames**. Zauważ, że możesz podać własny plik JavaScript dla funkcji UDF jako **skrypt**.

Zwróć również uwagę na sekcję o nazwie **roleassignments**. Służy ona do przypisania roli administratora przestrzeni do funkcji zdefiniowanej przez użytkownika. Ta rola umożliwia funkcji dostęp do zdarzeń przychodzących z dowolnej z aprowizowanych przestrzeni. 

1. Skonfiguruj funkcję UDF w taki sposób, aby obejmowała dopasowanie do temperatury poprzez dodanie lub usunięcie komentarza z `matcherNames` następującego wiersza w węźle pliku *provisionSample. YAML* :

    ```yaml
            - Matcher Temperature
    ```

1. Otwórz plik **src\actions\userDefinedFunctions\availability.js** w edytorze. Jest to plik, do którego odwołuje się element **skryptu** *provisionSample. YAML*. Funkcja zdefiniowana przez użytkownika w tym pliku wyszukuje warunki, gdy w pomieszczeniu nie jest wykrywany ruch, a poziom dwutlenku węgla jest niższy niż 1,000 ppm. 

   Zmodyfikuj plik JavaScript, aby monitorować temperaturę oraz inne warunki. Dodaj następujące wiersze kodu, aby wyszukiwać warunki, gdy w pomieszczeniu nie jest wykrywany ruch, poziom dwutlenku węgla jest niższy niż 1,000 ppm, a temperatura jest niższa niż 78 stopni Fehrenheita (25,5 stopnia Celsjusza).

   >[!NOTE]
   > W tej sekcji zostanie zmodyfikowany plik *src\actions\userDefinedFunctions\availability.js*, aby umożliwić Ci szczegółowe poznanie jednego ze sposobów pisania funkcji zdefiniowanej przez użytkownika. Możesz jednak bezpośrednio użyć pliku [src\actions\userDefinedFunctions\availabilityForTutorial.js](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js) w swojej konfiguracji. Ten plik zawiera wszystkie zmiany, które są wymagane do celów tego samouczka. Jeśli w zamian użyjesz tego pliku, pamiętaj, aby użyć prawidłowej nazwy pliku dla klucza **script** w pliku [src\actions\provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml).

    a. W górnej części pliku dodaj następujące wiersze dla temperatury pod komentarzem `// Add your sensor type here`:

    ```JavaScript
        var temperatureType = "Temperature";
        var temperatureThreshold = 78;
    ```

    b. Dodaj następujące wiersze po instrukcji definiującej zmienną `var motionSensor` pod komentarzem `// Add your sensor variable here`:

     ```JavaScript
        var temperatureSensor = otherSensors.find(function(element) {
            return element.DataType === temperatureType;
        });
    ```

    c. Dodaj następujący wiersz po instrukcji definiującej zmienną `var carbonDioxideValue` pod komentarzem `// Add your sensor latest value here`:

    ```JavaScript
        var temperatureValue = getFloatValue(temperatureSensor.Value().Value);
    ```

    d. Usuń następujące wiersze kodu spod komentarza `// Modify this line to monitor your sensor value`:

     ```JavaScript
        if(carbonDioxideValue === null || motionValue === null) {
            sendNotification(telemetry.SensorId, "Sensor", "Error: Carbon dioxide or motion are null, returning");
            return;
        }
    ```

    Zastąp je z następującymi wierszami:

    ```JavaScript
        if(carbonDioxideValue === null || motionValue === null || temperatureValue === null){
            sendNotification(telemetry.SensorId, "Sensor", "Error: Carbon dioxide, motion, or temperature are null, returning");
            return;
        }
    ```

    e. Usuń następujące wiersze kodu spod komentarza `// Modify these lines as per your sensor`:

    ```JavaScript
        var availableFresh = "Room is available and air is fresh";
        var noAvailableOrFresh = "Room is not available or air quality is poor";
    ```

    Zastąp je z następującymi wierszami:

    ```JavaScript
        var alert = "Room with fresh air and comfortable temperature is available.";
        var noAlert = "Either room is occupied, or working conditions are not right.";
    ```

    f. Usuń następujące blok kodu *if-else* po komentarzu `// Modify this code block for your sensor`:

    ```JavaScript
        // If carbonDioxide less than threshold and no presence in the room => log, notify and set parent space computed value
        if(carbonDioxideValue < carbonDioxideThreshold && !presence) {
            log(`${availableFresh}. Carbon Dioxide: ${carbonDioxideValue}. Presence: ${presence}.`);
            setSpaceValue(parentSpace.Id, spaceAvailFresh, availableFresh);
        }
        else {
            log(`${noAvailableOrFresh}. Carbon Dioxide: ${carbonDioxideValue}. Presence: ${presence}.`);
            setSpaceValue(parentSpace.Id, spaceAvailFresh, noAvailableOrFresh);

            // Set up custom notification for poor air quality
            parentSpace.Notify(JSON.stringify(noAvailableOrFresh));
        }
    ```

    Zastąp go następującym blokiem *if-else*:

    ```JavaScript
        // If sensor values are within range and room is available
        if(carbonDioxideValue < carbonDioxideThreshold && temperatureValue < temperatureThreshold && !presence) {
            log(`${alert}. Carbon Dioxide: ${carbonDioxideValue}. Temperature: ${temperatureValue}. Presence: ${presence}.`);

            // log, notify and set parent space computed value
            setSpaceValue(parentSpace.Id, spaceAvailFresh, alert);

            // Set up notification for this alert
            parentSpace.Notify(JSON.stringify(alert));
        }
        else {
            log(`${noAlert}. Carbon Dioxide: ${carbonDioxideValue}. Temperature: ${temperatureValue}. Presence: ${presence}.`);

            // log, notify and set parent space computed value
            setSpaceValue(parentSpace.Id, spaceAvailFresh, noAlert);
        }
    ```

    Zmodyfikowana funkcja UDF będzie wyszukiwała warunek, w którym pomieszczenie jest dostępne, a poziom dwutlenku węgla i temperatura mieszczą się w dopuszczalnych limitach. Wygeneruje ona powiadomienie przy użyciu instrukcji `parentSpace.Notify(JSON.stringify(alert));`, gdy ten warunek zostanie spełniony. Ustawi wartość monitorowanej przestrzeni niezależnie od tego, czy warunek zostanie spełniony, i wyświetli odpowiedni komunikat.

    g. Zapisz plik.

1. Otwórz okno polecenia i przejdź do folderu **occupancy-quickstart\src**. Uruchom następujące polecenie, aby zaaprowizować wykres analizy przestrzennej i funkcję zdefiniowaną przez użytkownika:

    ```cmd/sh
    dotnet run ProvisionSample
    ```

   >[!IMPORTANT]
   > Aby uniemożliwić nieautoryzowany dostęp do interfejsu API zarządzania usługą Digital Twins, aplikacja **occupancy-quickstart** wymaga zalogowania się przy użyciu poświadczeń konta platformy Azure. Zapisuje ona Twoje poświadczenia na pewien okres czasu, aby logowanie nie było konieczne przy każdym uruchomieniu. Przy pierwszym uruchomieniu tego programu, a także po wygaśnięciu zapisanych poświadczeń, aplikacja przekieruje Cię do strony logowania i poda kod specyficzny dla sesji, który należy wprowadzić na tej stronie. Postępuj zgodnie z monitami, aby zalogować się na koncie platformy Azure.

1. Po uwierzytelnieniu konta aplikacja rozpocznie Tworzenie przykładowego wykresu przestrzennego zgodnie z konfiguracją w *provisionSample. YAML*. Poczekaj na ukończenie aprowizowania. Może to potrwać kilka minut. Następnie przyjrzyj się komunikatom w oknie polecenia i spójrz na utworzony wykres przestrzenny. Zauważ, że aplikacja tworzy centrum IoT w węźle głównym lub w lokalizacji `Venue`.

1. Z danych wyjściowych w oknie polecenia skopiuj wartość elementu `ConnectionString` w sekcji `Devices` do schowka. Ta wartość będzie potrzebna do symulowania połączenia urządzenia w następnej sekcji.

    [![Przykład udostępniania](./media/tutorial-facilities-udf/run-provision-sample.png)](./media/tutorial-facilities-udf/run-provision-sample.png#lightbox)

>[!TIP]
> Jeśli podczas aprowizowania zostanie wyświetlony komunikat podobny do następującego „Operacja We/Wy została przerwana z powodu zakończenia wątku lub żądania aplikacji”, spróbuj ponownie uruchomić polecenie. Może się to zdarzyć, jeśli klient HTTP przekroczy limit czasu ze względu na problem z siecią.

## <a name="simulate-sensor-data"></a>Symulowanie danych z czujników

W tej sekcji będziesz korzystać z projektu o nazwie *device-connectivity* w ramach przykładu. Użyjesz go do symulowania danych dotyczących wykrywania ruchu, temperatury i dwutlenku węgla. Ten projekt generuje losowe wartości dla czujników i wysyła je do centrum IoT przy użyciu parametrów połączenia urządzenia.

1. W osobnym oknie polecenia przejdź do przykładu usługi Azure Digital Twins, a następnie do folderu **device-connectivity**.

1. Uruchom następujące polecenie, aby upewnić się, że zależności dla projektu są prawidłowe:

    ```cmd/sh
    dotnet restore
    ```

1. Otwórz plik [appSettings. JSON](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/device-connectivity/appsettings.json) w edytorze i przeprowadź edycję następujących wartości:

   a. **DeviceConnectionString**: przypisz wartość `ConnectionString` w oknie danych wyjściowych z poprzedniej sekcji. Skopiuj cały ciąg między cudzysłowami, aby symulator mógł poprawnie nawiązać połączenie z centrum IoT Hub.

   b. **HardwareId** w tablicy **czujników** : ponieważ symulujesz zdarzenia z czujników, które są obsługiwane w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji, identyfikator sprzętu i nazwy czujników w tym pliku powinny być zgodne z `sensors` węzłem pliku *provisionSample. YAML* .

      Dodaj nowy wpis dla czujnika temperatury. Węzeł **czujników** w pliku *appSettings. JSON* powinien wyglądać następująco:

      ```JSON
      "Sensors": [{
        "DataType": "Motion",
        "HardwareId": "SAMPLE_SENSOR_MOTION"
      },{
        "DataType": "CarbonDioxide",
        "HardwareId": "SAMPLE_SENSOR_CARBONDIOXIDE"
      },{
        "DataType": "Temperature",
        "HardwareId": "SAMPLE_SENSOR_TEMPERATURE"
      }]
      ```

1. Uruchom następujące polecenie, aby rozpocząć symulację zdarzeń urządzeń dotyczących temperatury, ruchu i dwutlenku węgla:

    ```cmd/sh
    dotnet run
    ```

   >[!NOTE]
   > Ponieważ przykładowa symulacja nie komunikuje się bezpośrednio z wystąpieniem usługi Digital Twins, uwierzytelnianie nie jest konieczne.

## <a name="get-results-of-the-user-defined-function"></a>Pobieranie wyników funkcji zdefiniowanej przez użytkownika

Funkcja zdefiniowana przez użytkownika jest uruchamiana za każdym razem, gdy wystąpienie odbiera dane z urządzeń i czujników. W tej sekcji jest wykonywane zapytanie względem wystąpienia usługi Azure Digital Twins w celu uzyskania wyników funkcji zdefiniowanej przez użytkownika. Użytkownik zostanie powiadomiony o niemal czasie rzeczywistym, gdy będzie dostępny pokój, że powietrze jest świeże i temperatura jest odpowiednia. 

1. Otwórz okno polecenia, którego użyto do aprowizacji przykładu, lub nowe okno polecenia i ponownie przejdź do folderu **occupancy-quickstart\src** w przykładzie.

1. Uruchom następujące polecenie i po pojawieniu się monitu zaloguj się:

    ```cmd/sh
    dotnet run GetAvailableAndFreshSpaces
    ```

W oknie danych wyjściowych będzie widoczne wykonywanie funkcji zdefiniowanej przez użytkownika oraz przechwytywanie zdarzeń z symulacji urządzenia. 

   [![Dane wyjściowe funkcji zdefiniowanej przez użytkownika](./media/tutorial-facilities-udf/adt-tutorial-udf-running.png)](./media/tutorial-facilities-udf/adt-tutorial-udf-running.png#lightbox)

W przypadku spełnienia monitorowanego warunku funkcja zdefiniowana przez użytkownika ustawi wartość dla przestrzeni, zwracając odpowiedni komunikat, jak pokazano [wyżej](#create-a-user-defined-function). Funkcja `GetAvailableAndFreshSpaces` wyświetla komunikat w konsoli.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie chcesz kontynuować pracy z usługą Azure Digital Twins, możesz usunąć zasoby utworzone w tym samouczku:

1. W menu po lewej stronie w witrynie [Azure Portal](https://portal.azure.com) wybierz przycisk **Wszystkie zasoby**, wybierz grupę zasobów usługi Digital Twins i wybierz polecenie **Usuń**.

    >[!TIP]
    > Jeśli podczas usuwania wystąpienia usługi Digital Twins wystąpił problem, została wdrożona aktualizacja usługi zawierająca poprawkę. Ponów próbę usunięcia wystąpienia.

2. Jeśli będzie to konieczne, możesz usunąć przykładowe aplikacje na komputerze służbowym.

## <a name="next-steps"></a>Następne kroki

Teraz po zaaprowizowaniu przestrzeni i utworzeniu platformy do wyzwalania niestandardowych powiadomień możesz przejść do dowolnego z następujących samouczków:

> [!div class="nextstepaction"]
> [Samouczek: otrzymywanie powiadomień z przestrzeni usługi Azure Digital Twins przy użyciu usługi Logic Apps](tutorial-facilities-events.md)

> [!div class="nextstepaction"]
> [Samouczek: wizualizowanie i analizowanie zdarzeń z przestrzeni usługi Azure Digital Twins przy użyciu usługi Time Series Insights](tutorial-facilities-analyze.md)
