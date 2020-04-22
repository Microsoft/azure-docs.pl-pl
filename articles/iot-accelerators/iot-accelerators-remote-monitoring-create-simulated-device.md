---
title: Symulacja urządzenia za pomocą zdalnego monitorowania IoT — Platforma Azure | Dokumenty firmy Microsoft
description: W tym przewodniku pokazano, jak używać symulatora urządzenia z akceleratorem rozwiązania zdalnego monitorowania.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.custom: mqtt
ms.openlocfilehash: d31e520dac1c7e2a13fbd9e24a0cd3167f69e904
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682007"
---
# <a name="create-and-test-a-new-simulated-device"></a>Tworzenie i testowanie nowego symulowanego urządzenia

Akcelerator rozwiązań do zdalnego monitorowania umożliwia definiowanie własnych symulowanych urządzeń. W tym artykule pokazano, jak zdefiniować nowe symulowane urządzenie żarówki, a następnie przetestować go lokalnie. Akcelerator rozwiązań obejmuje symulowane urządzenia, takie jak agregaty chłodnicze i ciężarówki. Można jednak zdefiniować własne symulowane urządzenia, aby przetestować rozwiązania IoT przed wdrożeniem rzeczywistych urządzeń.

> [!NOTE]
> W tym artykule opisano sposób używania symulowanych urządzeń hostowanych w usłudze symulacji urządzeń. Jeśli chcesz utworzyć prawdziwe urządzenie, zobacz [Łączenie urządzenia z akceleratorem rozwiązań do zdalnego monitorowania](iot-accelerators-connecting-devices.md).

W tym przewodniku pokazano, jak dostosować mikrousługi symulacji urządzenia. Ta mikrousługa jest częścią akceleratora rozwiązania zdalnego monitorowania. Aby pokazać możliwości symulacji urządzenia, w tym przewodniku opisano dwa scenariusze w aplikacji Contoso IoT:

W pierwszym scenariuszu należy dodać nowy typ telemetrii do istniejącego typu urządzenia **agregatu chłodniczego** firmy Contoso.

W drugim scenariuszu Contoso chce przetestować nowe inteligentne urządzenie żarówki. Aby uruchomić testy, należy utworzyć nowe symulowane urządzenie o następujących cechach:

*Właściwości*

| Nazwa                     | Wartości                      |
| ------------------------ | --------------------------- |
| Kolor                    | Biały, Czerwony, Niebieski            |
| Jasność               | Od 0 do 100                    |
| Szacowany pozostały okres eksploatacji | Odliczanie od 10 000 godzin |

*Telemetria*

W poniższej tabeli przedstawiono dane raportów żarówki do chmury jako strumień danych:

| Nazwa   | Wartości      |
| ------ | ----------- |
| Stan | "on", "off" |
| Temperatura | Stopnie F |
| online | wartość true, false |

> [!NOTE]
> Wartość telemetrii **online** jest obowiązkowa dla wszystkich symulowanych typów.

*Metody*

W poniższej tabeli przedstawiono akcje, które obsługuje nowe urządzenie:

| Nazwa        |
| ----------- |
| Włączanie   |
| Wyłączyć  |

*Stan początkowy*

W poniższej tabeli przedstawiono stan początkowy urządzenia:

| Nazwa                     | Wartości |
| ------------------------ | -------|
| Kolor początkowy            | Biały  |
| Początkowa jasność       | 75     |
| Początkowy pozostały okres eksploatacji   | 10 000 |
| Stan danych telemetrycznych początkowych | "on"   |
| Początkowa temperatura telemetrii | 200   |

Aby wykonać kroki opisane w tym przewodniku, potrzebujesz aktywnej subskrypcji platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby postępować zgodnie z tym przewodnikiem, potrzebujesz:

* Program Visual Studio Code. Program Visual Studio Code można [pobrać dla komputerów Mac, Linux i Windows](https://code.visualstudio.com/download).
* .NET Core. Program .NET Core można pobrać [dla komputerów Mac, Linux i Windows](https://www.microsoft.com/net/download).
* [Rozszerzenie C# for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* Listonosz. Listonosz można pobrać [dla komputerów Mac, Windows lub Linux](https://www.getpostman.com/apps).
* [Centrum IoT wdrożone w ramach subskrypcji platformy Azure.](../../articles/iot-hub/iot-hub-create-through-portal.md) Aby wykonać kroki opisane w tym przewodniku, potrzebujesz ciągu połączenia centrum IoT. Ciąg połączenia można uzyskać z witryny Azure portal.
* Baza danych Usługi Cosmos, która używa interfejsu API SQL i która jest skonfigurowana pod kątem [silnej spójności.](../../articles/cosmos-db/how-to-manage-database-account.md) Aby wykonać kroki opisane w tym przewodniku, potrzebny jest parametry połączenia bazy danych usługi Cosmos DB. Ciąg połączenia można uzyskać z witryny Azure portal.

## <a name="prepare-your-development-environment"></a>Przygotowywanie środowiska projektowego

Wykonaj następujące zadania, aby przygotować środowisko programistyczne:

* Pobierz źródło mikrousług symulacji urządzenia.
* Pobierz źródło mikrousługi karty magazynu.
* Uruchom mikrousługę karty magazynu lokalnie.

W instrukcjach w tym artykule założono, że używasz systemu Windows. Jeśli używasz innego systemu operacyjnego, może być konieczne dostosowanie niektórych ścieżek plików i poleceń do środowiska.

### <a name="download-the-microservices"></a>Pobierz mikrousługi

Pobierz i rozpaj [mikrousług monitorowania zdalnego](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) z gitHub do odpowiedniej lokalizacji na komputerze lokalnym. W artykule przyjęto założenie, że nazwa tego folderu to **remote-monitoring-services-dotnet-master**.

Pobierz i rozpaj [mikrousługę symulacji urządzenia](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) z usługi GitHub do odpowiedniej lokalizacji na komputerze lokalnym. W artykule przyjęto założenie, że nazwa tego folderu to **device-simulation-dotnet-master**.

### <a name="run-the-storage-adapter-microservice"></a>Uruchamianie mikrousług karty pamięci masowej

Otwórz folder **zdalnego monitorowania usług-dotnet-master\storage-adapter** w programie Visual Studio Code. Kliknij dowolne przyciski **Przywracanie,** aby naprawić wszelkie nierozwiązane zależności.

Otwórz plik **karty magazynu/WebService/appsettings.ini** i przypisz parametry połączenia usługi Cosmos DB do zmiennej **documentDBConnectionString.**

Aby uruchomić mikrousługę lokalnie, kliknij przycisk **Debugowanie > Rozpocznij debugowanie**.

Okno **Terminal** w programie Visual Studio Code pokazuje dane wyjściowe z uruchomionej mikrousługi, w tym adres URL sprawdzania kondycji usługi sieci web: [http://127.0.0.1:9022/v1/status](http://127.0.0.1:9022/v1/status). Po przejściu do tego adresu, stan powinien być "OK: Żyje i dobrze".

Pozostaw mikrousługę karty magazynu uruchomione w tym wystąpieniu programu Visual Studio Code podczas wykonywania kolejnych kroków.

## <a name="modify-the-chiller"></a>Modyfikowanie agregatu chłodniczego

W tej sekcji należy dodać nowy typ danych telemetrycznych **temperatury wewnętrznej** do istniejącego typu urządzenia **agregatu chłodniczego:**

1. Utwórz nowy folder **C:\temp\devicemodels** na komputerze lokalnym.

1. Skopiuj następujące pliki do nowego folderu z pobranej kopii mikrousługi symulacji urządzenia:

    | Element źródłowy | Element docelowy |
    | ------ | ----------- |
    | Usługi\dane\devicemodels\chiller-01.json | C:\temp\devicemodels\chiller-01.json |
    | Usługi\dane\devicemodels\scripts\chiller-01-state.js | C:\temp\devicemodels\scripts\chiller-01-state.js |
    | Usługi\dane\devicemodels\scripts\Reboot-method.js | C:\temp\devicemodels\scripts\Reboot-method.js |
    | Usługi\dane\devicemodels\scripts\FirmwareUpdate-method.js | C:\temp\devicemodels\scripts\FirmwareUpdate-method.js |
    | Usługi\dane\devicemodels\scripts\EmergencyValveRelease-method.js | C:\temp\devicemodels\scripts\EmergencyValveRelease-method.js |
    | Usługi\dane\devicemodels\skrypty\IncreasePressure-method.js | C:\temp\devicemodels\scripts\IncreasePressure-method.js |

1. Otwórz plik **C:\temp\devicemodels\chiller-01.json.**

1. W sekcji **Stan początkowy** dodaj następujące dwie definicje:

    ```json
    "internal_temperature": 65.0,
    "internal_temperature_unit": "F",
    ```

1. W tablicy **Telemetria** dodaj następującą definicję:

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

1. Zapisz plik **C:\temp\devicemodels\chiller-01.json.**

1. Otwórz plik **C:\temp\devicemodels\scripts\chiller-01-state.js.**

1. Dodaj następujące pola do zmiennej **stanu:**

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. Zaktualizuj funkcję **główną** w następujący sposób:

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

1. Zapisz plik **C:\temp\devicemodels\scripts\chiller-01-state.js.**

## <a name="create-the-lightbulb"></a>Tworzenie żarówki

W tej sekcji należy zdefiniować nowy typ urządzenia **żarówki:**

1. Utwórz plik **C:\temp\devicemodels\lightbulb-01.json** i dodaj następującą zawartość:

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

    Zapisz zmiany w **języku C:\temp\devicemodels\lightbulb-01.json**.

1. Utwórz plik **C:\temp\devicemodels\scripts\lightbulb-01-state.js** i dodaj następującą zawartość:

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

1. Utwórz plik **C:\temp\devicemodels\scripts\SwitchOn-method.js** i dodaj następującą zawartość:

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

    Zapisz zmiany w **języku C:\temp\devicemodels\scripts\SwitchOn-method.js**.

1. Utwórz plik **C:\temp\devicemodels\scripts\SwitchOff-method.js** i dodaj następującą zawartość:

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

    Zapisz zmiany w **języku C:\temp\devicemodels\scripts\SwitchOff-method.js**.

Teraz utworzono dostosowaną wersję typu urządzenia **chiller** i utworzono nowy typ urządzenia **żarówki.**

## <a name="test-the-devices"></a>Testowanie urządzeń

W tej sekcji można przetestować typy urządzeń utworzonych w poprzednich sekcjach lokalnie.

### <a name="run-the-device-simulation-microservice"></a>Uruchamianie mikrousług symulacji urządzenia

Otwórz folder **device-simulation-dotnet-master** pobrany z usługi GitHub w nowym wystąpieniu programu Visual Studio Code. Kliknij dowolne przyciski **Przywracanie,** aby naprawić wszelkie nierozwiązane zależności.

Otwórz plik **WebService/appsettings.ini** i przypisz parametry połączenia usługi Cosmos DB do **zmiennej documentdb_connstring,** a także zmodyfikuj ustawienia w następujący sposób:

```ini
device_models_folder = C:\temp\devicemodels\

device_models_scripts_folder = C:\temp\devicemodels\scripts\
```

Aby uruchomić mikrousługę lokalnie, kliknij przycisk **Debugowanie > Rozpocznij debugowanie**.

Okno **Terminal** w programie Visual Studio Code pokazuje dane wyjściowe z uruchomionej mikrousługi.

Pozostaw mikrousługi symulacji urządzenia uruchomione w tym wystąpieniu programu Visual Studio Code podczas wykonywania kolejnych kroków.

### <a name="set-up-a-monitor-for-device-events"></a>Konfigurowanie monitora zdarzeń urządzenia

W tej sekcji można użyć interfejsu wiersza polecenia platformy Azure, aby skonfigurować monitor zdarzeń, aby wyświetlić dane telemetryczne wysyłane z urządzeń podłączonych do centrum IoT hub.

Poniższy skrypt zakłada, że nazwa centrum IoT hub jest **device-simulation-test**.

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-iot

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

Pozostaw monitor zdarzeń uruchomiony podczas testowania symulowanych urządzeń.

### <a name="create-a-simulation-with-the-updated-chiller-device-type"></a>Tworzenie symulacji ze zaktualizowanym typem urządzenia agregatu chłodniczego

W tej sekcji użyj narzędzia Postman, aby zażądać mikrousług symulacji urządzenia do uruchomienia symulacji przy użyciu zaktualizowanego typu urządzenia agregatu chłodniczego. Listonosz to narzędzie, które umożliwia wysyłanie żądań REST do usługi sieci web. Pliki konfiguracyjne postmana, których potrzebujesz, znajdują się w lokalnej kopii repozytorium **device-simulation-dotnet.**

Aby skonfigurować listonosza:

1. Otwórz listonosza na lokalnym komputerze.

1. Kliknij **pozycję Plik > Importuj**. Następnie kliknij pozycję **Wybierz pliki**.

1. Przejdź do folderu **device-simulation-dotnet-master/docs/listonosz.** Wybierz **akcelerator rozwiązań symulacji urządzeń Azure IoT.postman_collection** i **akcelerator rozwiązań symulacji urządzeń Usługi Azure IoT.postman_environment** i kliknij przycisk **Otwórz**.

1. Rozwiń **akcelerator rozwiązań symulacji urządzeń usługi Azure IoT** do żądań, które można wysłać.

1. Kliknij **pozycję Brak środowiska** i wybierz **akcelerator rozwiązań symulacji urządzeń Usługi Azure IoT**.

Masz teraz kolekcji i środowiska załadowany w obszarze roboczym Postman, który służy do interakcji z mikrousługi symulacji urządzenia.

Aby skonfigurować i uruchomić symulację:

1. W kolekcji Listonosz wybierz pozycję **Utwórz zmodyfikowaną symulację agregatu chłodniczego** i kliknij przycisk **Wyślij**. To żądanie tworzy cztery wystąpienia typu symulowanego urządzenia agregatu chłodniczego.

1. Dane wyjściowe monitora zdarzeń w oknie interfejsu wiersza polecenia platformy Azure zawiera dane telemetryczne z symulowanych urządzeń, w tym nowe wartości **internal_temperature.**

Aby zatrzymać symulację, wybierz żądanie **Zatrzymaj symulację** w postmanie i kliknij przycisk **Wyślij**.

### <a name="create-a-simulation-with-the-lightbulb-device-type"></a>Tworzenie symulacji z typem urządzenia żarówki

W tej sekcji można użyć narzędzia Postman, aby zażądać mikrousług symulacji urządzenia do uruchomienia symulacji przy użyciu typu urządzenia żarówki. Listonosz to narzędzie, które umożliwia wysyłanie żądań REST do usługi sieci web.

Aby skonfigurować i uruchomić symulację:

1. W kolekcji Listonosz wybierz pozycję **Utwórz symulację żarówki** i kliknij przycisk **Wyślij**. To żądanie tworzy dwa wystąpienia symulowanego typu urządzenia żarówki.

1. Dane wyjściowe monitora zdarzeń w oknie interfejsu wiersza polecenia platformy Azure pokazuje dane telemetryczne z symulowanych żarówek.

Aby zatrzymać symulację, wybierz żądanie **Zatrzymaj symulację** w postmanie i kliknij przycisk **Wyślij**.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Można zatrzymać dwa lokalnie uruchomione mikrousług w ich wystąpień kodu programu Visual Studio **(Debug > Zatrzymaj debugowanie**).

Jeśli nie potrzebujesz już wystąpień usługi IoT Hub i usługi Cosmos DB, usuń je z subskrypcji platformy Azure, aby uniknąć niepotrzebnych opłat.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku pokazano, jak utworzyć niestandardowe typy symulowanych urządzeń i przetestować je, uruchamiając lokalnie mikrousługę symulacji urządzenia.

Sugerowanym następnym krokiem jest zapoznanie się z instrukcjami wdrażania niestandardowych typów symulowanych urządzeń w [akceleratorze rozwiązań do zdalnego monitorowania.](iot-accelerators-remote-monitoring-deploy-simulated-device.md)
