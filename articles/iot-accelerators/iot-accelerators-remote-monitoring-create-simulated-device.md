---
title: Symulacja urządzenia za pomocą monitorowania zdalnego IoT — Azure | Microsoft Docs
description: Ten przewodnik przedstawia sposób korzystania z symulatora urządzeń przy użyciu akceleratora rozwiązania do zdalnego monitorowania.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.custom: mqtt, devx-track-js
ms.openlocfilehash: 21d0587b951d909f0091eccf9a7f86a484bc5051
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91324046"
---
# <a name="create-and-test-a-new-simulated-device"></a>Tworzenie i testowanie nowego symulowanego urządzenia

Akcelerator rozwiązania do monitorowania zdalnego pozwala definiować własne symulowane urządzenia. W tym artykule opisano sposób definiowania nowego symulowanego urządzenia żarówki, a następnie przetestowania go lokalnie. Akcelerator rozwiązania obejmuje symulowane urządzenia, takie jak chłodner i samochody. Można jednak zdefiniować własne symulowane urządzenia do testowania rozwiązań IoT przed wdrożeniem rzeczywistych urządzeń.

> [!NOTE]
> W tym artykule opisano sposób korzystania z symulowanych urządzeń hostowanych w usłudze symulacji urządzeń. Jeśli chcesz utworzyć rzeczywiste urządzenie, zobacz [łączenie urządzenia z akceleratorem rozwiązania do monitorowania zdalnego](iot-accelerators-connecting-devices.md).

W tym przewodniku opisano sposób dostosowywania mikrousługi symulacji urządzenia. Ta mikrousługa jest częścią akceleratora rozwiązania do monitorowania zdalnego. Aby wyświetlić możliwości symulacji urządzeń, ten przewodnik zawiera dwa scenariusze w aplikacji Contoso IoT:

W pierwszym scenariuszu należy dodać nowy typ telemetrii do istniejącego typu urządzenia dla **chłodzenia** firmy Contoso.

W drugim scenariuszu firma Contoso chce przetestować nowe urządzenie inteligentne żarówki. Aby uruchomić testy, należy utworzyć nowe symulowane urządzenie o następujących cechach:

*Właściwości*

| Nazwa                     | Wartości                      |
| ------------------------ | --------------------------- |
| Kolor                    | Biały, czerwony, niebieski            |
| Jasność               | od 0 do 100                    |
| Szacowany pozostały okres istnienia | Odliczanie od 10 000 godzin |

*Telemetria*

W poniższej tabeli przedstawiono dane, które żarówki raporty do chmury jako strumień danych:

| Nazwa   | Wartości      |
| ------ | ----------- |
| Stan | "on", "off" |
| Temperatura | Stopnie F |
| online | wartość true, false |

> [!NOTE]
> Wartość telemetrii **online** jest wymagana dla wszystkich typów symulowanych.

*Metody*

W poniższej tabeli przedstawiono akcje obsługiwane przez nowe urządzenie:

| Nazwa        |
| ----------- |
| Włącz   |
| Wyłącz  |

*Stan początkowy*

W poniższej tabeli przedstawiono początkowy stan urządzenia:

| Nazwa                     | Wartości |
| ------------------------ | -------|
| Kolor początkowy            | Biały  |
| Jasność początkowa       | 75     |
| Początkowy pozostały okres istnienia   | 10 000 |
| Początkowy stan telemetrii | z   |
| Początkowa temperatura telemetrii | 200   |

Aby wykonać kroki opisane w tym przewodniku, musisz mieć aktywną subskrypcję platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać czynności opisane w tym przewodniku:

* Program Visual Studio Code. Możesz [pobrać Visual Studio Code dla systemów Mac, Linux i Windows](https://code.visualstudio.com/download).
* .NET Core. Możesz pobrać [.NET Core dla systemów Mac, Linux i Windows](https://www.microsoft.com/net/download).
* [Rozszerzenie C# for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* Postman. Możesz pobrać program [Poster dla komputerów Mac, Windows lub Linux](https://www.getpostman.com/apps).
* [Centrum IoT zostało wdrożone w ramach subskrypcji platformy Azure](../../articles/iot-hub/iot-hub-create-through-portal.md). Do wykonania kroków opisanych w tym przewodniku potrzebne są parametry połączenia Centrum IoT Hub. Parametry połączenia można uzyskać z Azure Portal.
* Baza danych Cosmos DB, która korzysta z interfejsu API SQL i jest skonfigurowana pod kątem [silnej spójności](../../articles/cosmos-db/how-to-manage-database-account.md). Do wykonania kroków opisanych w tym przewodniku potrzebne są parametry połączenia bazy danych Cosmos DB. Parametry połączenia można uzyskać z Azure Portal.

## <a name="prepare-your-development-environment"></a>Przygotowywanie środowiska projektowego

Wykonaj następujące zadania, aby przygotować środowisko programistyczne:

* Pobierz Źródło dla mikrousługi symulacji urządzenia.
* Pobierz Źródło dla mikrousługi karty pamięci.
* Uruchom lokalnie kartę usługi Storage.

W instrukcjach przedstawionych w tym artykule przyjęto założenie, że używasz systemu Windows. Jeśli używasz innego systemu operacyjnego, może być konieczne dostosowanie niektórych ścieżek i poleceń plików do środowiska.

### <a name="download-the-microservices"></a>Pobierz mikrousługi

Pobierz i rozpakuj [mikrousługi zdalnego monitorowania](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) z witryny GitHub do odpowiedniej lokalizacji na komputerze lokalnym. W tym artykule założono, że nazwa tego folderu to **Remote-Monitoring-Services-dotnet-Master**.

Pobierz i rozpakuj [mikrousługę symulacji urządzenia z usługi](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) GitHub do odpowiedniej lokalizacji na komputerze lokalnym. W tym artykule przyjęto, że nazwa tego folderu to **symulacja urządzenia — dotnet-Master**.

### <a name="run-the-storage-adapter-microservice"></a>Uruchamianie mikrousługi karty pamięci

Otwórz folder **Remote-Monitoring-Services-dotnet-master\storage-adapter** w Visual Studio Code. Kliknij dowolne przyciski **przywracania** , aby naprawić wszystkie nierozwiązane zależności.

Otwórz plik **Storage-Adapter/WebService/appsettings.ini** i przypisz Cosmos DB parametry połączenia do zmiennej **documentDBConnectionString**  .

Aby uruchomić mikrousługę lokalnie, kliknij pozycję **debuguj > Rozpocznij debugowanie**.

W oknie **terminalu** w Visual Studio Code są wyświetlane dane wyjściowe z uruchomionej mikrousługi, w tym adres URL sprawdzania kondycji usługi sieci Web: [http://127.0.0.1:9022/v1/status](http://127.0.0.1:9022/v1/status) . Po przejściu na ten adres stan powinien mieć wartość "OK: Alive i".

W tym wystąpieniu Visual Studio Code należy pozostawić działającą mikrousługę kart pamięci podczas wykonywania następnych kroków.

## <a name="modify-the-chiller"></a>Modyfikowanie chłodzenia

W tej sekcji dodasz nowy typ telemetrii **temperatury wewnętrznej** do istniejącego typu urządzenia **chłodzenia** :

1. Utwórz nowy folder **C:\temp\devicemodels** na komputerze lokalnym.

1. Skopiuj następujące pliki do nowego folderu z pobranej kopii mikrousługi symulacji urządzenia:

    | Element źródłowy | Element docelowy |
    | ------ | ----------- |
    | Services\data\devicemodels\chiller-01.jsna | C:\temp\devicemodels\chiller-01.jsna |
    | Services\data\devicemodels\scripts\chiller-01-state.js | C:\temp\devicemodels\scripts\chiller-01-state.js |
    | Services\data\devicemodels\scripts\Reboot-method.js | C:\temp\devicemodels\scripts\Reboot-method.js |
    | Services\data\devicemodels\scripts\FirmwareUpdate-method.js | C:\temp\devicemodels\scripts\FirmwareUpdate-method.js |
    | Services\data\devicemodels\scripts\EmergencyValveRelease-method.js | C:\temp\devicemodels\scripts\EmergencyValveRelease-method.js |
    | Services\data\devicemodels\scripts\IncreasePressure-method.js | C:\temp\devicemodels\scripts\IncreasePressure-method.js |

1. Otwórz **C:\temp\devicemodels\chiller-01.js** pliku.

1. W sekcji **InitialState** Dodaj następujące dwie definicje:

    ```json
    "internal_temperature": 65.0,
    "internal_temperature_unit": "F",
    ```

1. W tablicy **telemetrii** Dodaj następującą definicję:

    ```json
    {
      "Interval": "00:00:05",
      "MessageTemplate": "{\"internal_temperature\":${internal_temperature},\"internal_temperature_unit\":\"${internal_temperature_unit}\"}",
      "MessageSchema": {
        "Name": "chiller-internal-temperature;v1",
        "Format": "JSON",
        "Fields": {
          "temperature": "double",
          "temperature_unit": "text"
        }
      }
    },
    ```

1. Zapisz **C:\temp\devicemodels\chiller-01.js** pliku.

1. Otwórz plik **C:\temp\devicemodels\scripts\chiller-01-state.js** .

1. Dodaj następujące pola do zmiennej **stanu** :

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. Zaktualizuj **główną** funkcję w następujący sposób:

    ```js
    function main(context, previousState, previousProperties) {

        // Restore the global state before generating the new telemetry, so that
        // the telemetry can apply changes using the previous function state.
        restoreSimulation(previousState, previousProperties);

        // 75F +/- 5%,  Min 25F, Max 100F
        state.temperature = vary(75, 5, 25, 100);

        // 70% +/- 5%,  Min 2%, Max 99%
        state.humidity = vary(70, 5, 2, 99);

        // 65F +/- 2%,  Min 15F, Max 125F
        state.internal_temperature = vary(65, 2, 15, 125);

        log("Simulation state: " + state.simulation_state);
        if (state.simulation_state === "high_pressure") {
            // 250 psig +/- 25%,  Min 50 psig, Max 300 psig
            state.pressure = vary(250, 25, 50, 300);
        } else {
            // 150 psig +/- 10%,  Min 50 psig, Max 300 psig
            state.pressure = vary(150, 10, 50, 300);
        }

        updateState(state);
        return state;
    }
    ```

1. Zapisz plik **C:\temp\devicemodels\scripts\chiller-01-state.js** .

## <a name="create-the-lightbulb"></a>Tworzenie żarówki

W tej sekcji definiujesz nowy typ urządzenia **żarówki** :

1. Utwórz plik **C:\temp\devicemodels\lightbulb-01.jsna** i Dodaj następującą zawartość:

    ```json
    {
      "SchemaVersion": "1.0.0",
      "Id": "lightbulb-01",
      "Version": "0.0.1",
      "Name": "Lightbulb",
      "Description": "Smart lightbulb device.",
      "Protocol": "MQTT",
      "Simulation": {
        "InitialState": {
          "online": true,
          "temperature": 200.0,
          "temperature_unit": "F",
          "status": "on"
        },
        "Interval": "00:00:20",
        "Scripts": [
          {
            "Type": "javascript",
            "Path": "lightbulb-01-state.js"
          }
        ]
      },
      "Properties": {
        "Type": "Lightbulb",
        "Color": "White",
        "Brightness": 75,
        "EstimatedRemainingLife": 10000
      },
      "Tags": {
        "Location": "Building 2",
        "Floor": "2",
        "Campus": "Redmond"
      },
      "Telemetry": [
        {
          "Interval": "00:00:20",
          "MessageTemplate": "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"status\":\"${status}\"}",
          "MessageSchema": {
            "Name": "lightbulb-status;v1",
            "Format": "JSON",
            "Fields": {
              "temperature": "double",
              "temperature_unit": "text",
              "status": "text"
            }
          }
        }
      ],
      "CloudToDeviceMethods": {
        "SwitchOn": {
          "Type": "javascript",
          "Path": "SwitchOn-method.js"
        },
        "SwitchOff": {
          "Type": "javascript",
          "Path": "SwitchOff-method.js"
        }
      }
    }
    ```

    Zapisz zmiany w **C:\temp\devicemodels\lightbulb-01.js**.

1. Utwórz plik **C:\temp\devicemodels\scripts\lightbulb-01-state.js** i Dodaj następującą zawartość:

    ```javascript
    "use strict";

    // Default state
    var state = {
      online: true,
      temperature: 200.0,
      temperature_unit: "F",
      status: "on"
    };

    // Default device properties
    var properties = {};

    /**
     * Restore the global state using data from the previous iteration.
     *
     * @param previousState device state from the previous iteration
     * @param previousProperties device properties from the previous iteration
     */
    function restoreSimulation(previousState, previousProperties) {
      // If the previous state is null, force a default state
      if (previousState) {
        state = previousState;
      } else {
        log("Using default state");
      }

      if (previousProperties) {
        properties = previousProperties;
      } else {
        log("Using default properties");
      }
    }

    /**
     * Simple formula generating a random value around the average
     * in between min and max
     *
     * @returns random value with given parameters
     */
    function vary(avg, percentage, min, max) {
      var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
      value = Math.max(value, min);
      value = Math.min(value, max);
      return value;
    }

    /**
     * Simple formula that sometimes flips the status of the lightbulb
     */
    function flip(value) {
      if (Math.random() < 0.2) {
        return (value == "on") ? "off" : "on"
      }
      return value;
    }

    /**
     * Entry point function called by the simulation engine.
     * Returns updated simulation state.
     * Device property updates must call updateProperties() to persist.
     *
     * @param context             The context contains current time, device model and id
     * @param previousState       The device state since the last iteration
     * @param previousProperties  The device properties since the last iteration
     */
    function main(context, previousState, previousProperties) {

      // Restore the global device properties and the global state before
      // generating the new telemetry, so that the telemetry can apply changes
      // using the previous function state.
      restoreSimulation(previousState, previousProperties);

      state.temperature = vary(200, 5, 150, 250);

      // Make this flip every so often
      state.status = flip(state.status);

      updateState(state);

      return state;
    }
    ```

    Zapisz zmiany w **C:\temp\devicemodels\scripts\lightbulb-01-state.js**.

1. Utwórz plik **C:\temp\devicemodels\scripts\SwitchOn-method.js** i Dodaj następującą zawartość:

    ```javascript
    "use strict";

    // Default state
    var state = {
      status: "on"
    };

    /**
     * Entry point function called by the method.
     *
     * @param context        The context contains current time, device model and id
     * @param previousState  The device state since the last iteration
     * @param previousProperties  The device properties since the last iteration
     */
    function main(context, previousState) {
      log("Executing lightbulb Switch On method.");
      state.status = "on";
      updateState(state);
    }
    ```

    Zapisz zmiany w **C:\temp\devicemodels\scripts\SwitchOn-method.js**.

1. Utwórz plik **C:\temp\devicemodels\scripts\SwitchOff-method.js** i Dodaj następującą zawartość:

    ```javascript
    "use strict";

    // Default state
    var state = {
      status: "on"
    };

    /**
     * Entry point function called by the method.
     *
     * @param context        The context contains current time, device model and id
     * @param previousState  The device state since the last iteration
     * @param previousProperties  The device properties since the last iteration
     */
    function main(context, previousState) {
      log("Executing lightbulb Switch Off method.");
      state.status = "off";
      updateState(state);
    }
    ```

    Zapisz zmiany w **C:\temp\devicemodels\scripts\SwitchOff-method.js**.

Utworzono dostosowaną wersję typu urządzenia **chłodzenia** i utworzono nowy typ urządzenia **żarówki** .

## <a name="test-the-devices"></a>Testowanie urządzeń

W tej sekcji przetestujesz typy urządzeń utworzone w poprzednich sekcjach lokalnie.

### <a name="run-the-device-simulation-microservice"></a>Uruchamianie mikrousługi symulacji urządzenia

Otwórz folder **"symulacja urządzenia-dotnet"** , który został pobrany z usługi GitHub w nowym wystąpieniu Visual Studio Code. Kliknij dowolne przyciski **przywracania** , aby naprawić wszystkie nierozwiązane zależności.

Otwórz plik usługi **WebService/appsettings.ini** i przypisz parametry połączenia Cosmos DB do zmiennej **documentdb_connstring** , a także zmodyfikuj ustawienia w następujący sposób:

```ini
device_models_folder = C:\temp\devicemodels\

device_models_scripts_folder = C:\temp\devicemodels\scripts\
```

Aby uruchomić mikrousługę lokalnie, kliknij pozycję **debuguj > Rozpocznij debugowanie**.

W oknie **terminalu** w Visual Studio Code są wyświetlane dane wyjściowe z uruchomionej mikrousługi.

W tym wystąpieniu Visual Studio Code należy pozostawić uruchomioną mikrousługę symulacji urządzenia podczas wykonywania następnych kroków.

### <a name="set-up-a-monitor-for-device-events"></a>Konfigurowanie monitora dla zdarzeń urządzeń

W tej sekcji użyto interfejsu wiersza polecenia platformy Azure, aby skonfigurować Monitor zdarzeń do wyświetlania danych telemetrycznych wysyłanych z urządzeń podłączonych do centrum IoT.

W poniższym skrypcie założono, że nazwa Twojego centrum IoT to **symulacja urządzenia**.

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-iot

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

Pozostaw uruchomiony Monitor zdarzeń podczas testowania symulowanych urządzeń.

### <a name="create-a-simulation-with-the-updated-chiller-device-type"></a>Tworzenie symulacji przy użyciu zaktualizowanego typu urządzenia dla platformy chłodzenia

W tej sekcji użyjemy narzędzia do ogłaszania, aby zażądać mikrousługi symulacji urządzenia do uruchomienia symulacji przy użyciu zaktualizowanego typu urządzenia chłodzenia. Program Poster to narzędzie, które umożliwia wysyłanie żądań REST do usługi sieci Web. Pliki konfiguracji programu Poster są potrzebne w lokalnej kopii repozytorium **dotnet dla symulacji urządzenia** .

Aby skonfigurować notkę:

1. Otwórz wpis na komputerze lokalnym.

1. Kliknij kolejno pozycje **plik > Importuj**. Następnie kliknij pozycję **Wybierz pliki**.

1. Przejdź do folderu **Device-symulacja dotnet-Master/docs/** doc. Wybierz **rozwiązanie do symulacji urządzeń Azure iot Accelerator.postman_collection** i **rozwiązanie do symulacji urządzeń Azure IoT Accelerator.postman_environment** a następnie kliknij przycisk **Otwórz**.

1. Rozwiń **Akcelerator rozwiązania do symulacji urządzenia Azure IoT** na żądania, które można wysłać.

1. Kliknij pozycję **Brak środowiska** i wybierz **Akcelerator rozwiązania do symulacji urządzeń Azure IoT**.

Masz teraz kolekcję i środowisko załadowane w obszarze roboczym programu Poster, którego można użyć do współdziałania z mikrousługą symulacji urządzenia.

Aby skonfigurować i uruchomić symulację:

1. W kolekcji Ogłośer wybierz pozycję **Utwórz zmodyfikowaną symulację chłodzenia** , a następnie kliknij pozycję **Wyślij**. To żądanie tworzy cztery wystąpienia symulowanego typu urządzenia chłodzenia.

1. Dane wyjściowe monitora zdarzeń w oknie interfejsu wiersza polecenia platformy Azure pokazują dane telemetryczne z symulowanych urządzeń, w tym nowe wartości **internal_temperature** .

Aby zatrzymać symulację, wybierz żądanie **zatrzymania symulacji** w programie Poster i kliknij pozycję **Wyślij**.

### <a name="create-a-simulation-with-the-lightbulb-device-type"></a>Tworzenie symulacji przy użyciu typu urządzenia żarówki

W tej sekcji użyjemy narzędzia do ogłaszania, aby zażądać mikrousługi symulacji urządzenia do uruchomienia symulacji przy użyciu typu urządzenia żarówki. Program Poster to narzędzie, które umożliwia wysyłanie żądań REST do usługi sieci Web.

Aby skonfigurować i uruchomić symulację:

1. W kolekcji Poster wybierz pozycję **Utwórz symulację żarówki** , a następnie kliknij pozycję **Wyślij**. To żądanie tworzy dwa wystąpienia symulowanego typu urządzenia żarówki.

1. Dane wyjściowe monitora zdarzeń w oknie interfejsu wiersza polecenia platformy Azure pokazują dane telemetryczne z symulowanego lightbulbs.

Aby zatrzymać symulację, wybierz żądanie **zatrzymania symulacji** w programie Poster i kliknij pozycję **Wyślij**.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Można zatrzymać dwa lokalnie działające mikrousługi w Visual Studio Code wystąpieniach (**debugowanie > zatrzymać debugowanie**).

Jeśli nie potrzebujesz już IoT Hub i Cosmos DB wystąpień, usuń je z subskrypcji platformy Azure, aby uniknąć niepotrzebnych opłat.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku pokazano, jak utworzyć niestandardowe typy symulowanych urządzeń i przetestować je przez uruchomienie mikrousługi symulacji urządzenia lokalnie.

Sugerowanym następnym krokiem jest zapoznanie się ze sposobem wdrażania niestandardowych typów urządzeń symulowanych w [akceleratorze rozwiązania do monitorowania zdalnego](iot-accelerators-remote-monitoring-deploy-simulated-device.md).
