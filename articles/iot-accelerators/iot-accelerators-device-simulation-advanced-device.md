---
title: Tworzenie zaawansowanego symulowanego modelu urządzenia — Azure| Dokumenty firmy Microsoft
description: W tym przewodniku instrukcja, dowiesz się, jak utworzyć zaawansowany model urządzenia do użytku z akceleratorem rozwiązania symulacji urządzenia.
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: troyhop
ms.custom:
- mvc
- amqp
- mqtt
ms.openlocfilehash: c568dddcbbf57ebd6ed5906bb83af01a84dafa41
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683828"
---
# <a name="create-an-advanced-device-model"></a>Utwórz zaawansowany model urządzenia

W tym przewodniku opisano pliki JSON i JavaScript definiujące niestandardowy model urządzenia. Ten artykuł zawiera kilka przykładowych plików definicji modelu urządzenia i pokazuje, jak przekazać je do wystąpienia symulacji urządzenia. Można utworzyć zaawansowane modele urządzeń, aby symulować bardziej realistyczne zachowania urządzeń podczas testowania.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku, potrzebujesz wdrożonego wystąpienia symulacji urządzeń w ramach subskrypcji platformy Azure.

Jeśli nie wdrożono jeszcze symulacji urządzenia, należy wykonać czynności opisane w przewodniku Szybki start [Wdrażanie i uruchamianie symulacji urządzenia usługi IoT na platformie Azure](quickstart-device-simulation-deploy.md).

### <a name="open-device-simulation"></a>Otwieranie symulacji urządzenia

Aby uruchomić symulację urządzenia w przeglądarce, przejdź najpierw do [akceleratorów rozwiązań usługi Microsoft Azure IoT](https://www.azureiotsolutions.com).

Może być konieczne zalogowanie się przy użyciu poświadczeń subskrypcji platformy Azure.

Następnie kliknij przycisk **Uruchom** na kafelku symulacji urządzenia wdrożonego w [programie Deploy i uruchom symulację urządzenia IoT w](quickstart-device-simulation-deploy.md) przewodniku Szybki start platformy Azure.

## <a name="device-models"></a>Modele urządzeń

Każde symulowane urządzenie należy do określonego modelu urządzenia, który definiuje zachowanie symulacji. To zachowanie obejmuje, jak często wysyłać dane telemetryczne, jakiego rodzaju wiadomości do wysłania i obsługiwanych metod.

Model urządzenia można zdefiniować przy użyciu pliku definicji urządzenia JSON i zestawu plików JavaScript. Te pliki JavaScript definiują zachowanie symulacji, takie jak losowa telemetria i logika metody.

Typowy model urządzenia posiada:

* Jeden plik JSON dla każdego modelu urządzenia (na przykład elevator.json).
* Jeden plik skryptu zachowania JavaScript dla każdego modelu urządzenia (na przykład elevator-state.js)
* Jeden plik skryptu metody JavaScript dla każdej metody urządzenia (na przykład elevator-go-down.js)

> [!NOTE]
> Nie wszystkie modele urządzeń definiują metody. W związku z tym model urządzenia może lub nie może mieć skryptów metody. Jednak wszystkie modele urządzeń muszą mieć skrypt zachowania.

## <a name="device-definition-file"></a>Plik definicji urządzenia

Każdy plik definicji urządzenia zawiera szczegóły symulowanego modelu urządzenia, w tym następujące informacje:

* Nazwa modelu urządzenia: ciąg.
* Protokół: AMQP | MQTT | Http.
* Początkowy stan urządzenia.
* Jak często odświeżać stan urządzenia.
* Który plik JavaScript ma być używany do odświeżania stanu urządzenia.
* Lista komunikatów telemetrycznych do wysłania, każdy z określoną częstotliwością.
* Schemat komunikatów telemetrycznych, używany przez aplikację zaplecza do analizowaniu odebranych danych telemetrycznych.
* Lista obsługiwanych metod i plik JavaScript do symulacji każdej metody.

### <a name="file-schema"></a>Schemat pliku

Wersja schematu jest zawsze "1.0.0" i jest specyficzna dla formatu tego pliku:

```json
"SchemaVersion": "1.0.0"
```

### <a name="device-model-description"></a>Opis modelu urządzenia

Następujące właściwości opisują model urządzenia. Każdy typ ma unikatowy identyfikator, wersję semantyczną, nazwę i opis:

```json
"Id": "chiller-01",
"Version": "0.0.1",
"Name": "Chiller",
"Description": "Chiller with external temperature and humidity sensors."
```

### <a name="iot-protocol"></a>Protokół IoT

Urządzenia IoT można łączyć przy użyciu różnych protokołów. Symulacja umożliwia użycie **protokołu AMQP,** **MQTT**lub **HTTP:**

```json
"Protocol": "AMQP"
```

### <a name="simulated-device-state"></a>Symulowany stan urządzenia

Każde symulowane urządzenie ma stan wewnętrzny, który musi być zdefiniowany. Stan definiuje również właściwości, które mogą być zgłaszane w telemetrii. Na przykład agregat chłodnicz może mieć stan początkowy, taki jak:

```json
"InitialState": {
    "temperature": 50,
    "humidity": 40
},
```

Poruszające się urządzenie z kilkoma czujnikami może mieć więcej właściwości, na przykład:

```json
"InitialState": {
    "latitude": 47.445301,
    "longitude": -122.296307,
    "speed": 30.0,
    "speed_unit": "mph",
    "cargotemperature": 38.0,
    "cargotemperature_unit": "F"
}
```

Stan urządzenia jest przechowywany w pamięci przez usługę symulacji i dostarczany jako dane wejściowe do funkcji JavaScript. Funkcja JavaScript może zdecydować:

* Aby zignorować stan i wygenerować kilka losowych danych.
* Aby zaktualizować stan urządzenia w jakiś realistyczny sposób dla danego scenariusza.

Funkcja, która generuje stan odbiera również jako dane wejściowe:

* Identyfikator urządzenia.
* Model urządzenia.
* Bieżący czas. Ta wartość umożliwia generowanie różnych danych według urządzenia i czasu.

### <a name="generating-telemetry-messages"></a>Generowanie komunikatów telemetrycznych

Usługa symulacji może wysyłać kilka typów danych telemetrycznych dla każdego urządzenia. Zazwyczaj dane telemetryczne obejmują dane ze stanu urządzenia. Na przykład symulowany pokój może wysyłać informacje o temperaturze i wilgotności co 10 sekund. Zanotuj symbole zastępcze w poniższym urywek, które są automatycznie zastępowane wartościami ze stanu urządzenia:

```json
"Telemetry": [
    {
        "Interval": "00:00:10",
        "MessageTemplate":
            "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"humidity\":\"${humidity}\"}",
        "MessageSchema": {
            "Name": "RoomComfort;v1",
            "Format": "JSON",
            "Fields": {
                "temperature": "double",
                "temperature_unit": "text",
                "humidity": "integer"
            }
        }
    }
],
```

Symbole zastępcze używają specjalnej składni **${NAME},** gdzie **NAZWA** jest kluczem z obiektu stanu urządzenia zwróconego przez funkcję **główną** JavaScript. Ciągi powinny być cytowane, podczas gdy liczby nie powinny.

#### <a name="message-schema"></a>Schemat wiadomości

Każdy typ wiadomości musi mieć dobrze zdefiniowany schemat. Schemat wiadomości jest również publikowany w centrum IoT Hub, dzięki czemu aplikacje zaplecza mogą ponownie używać informacji do interpretacji danych wejściowych.

Schemat obsługuje format JSON, który umożliwia łatwe analizowanie, transformację i analizę w kilku systemach i usługach.

Pola wymienione w schemacie mogą być następujące typy:

* Obiekt — serializowany przy użyciu języka JSON
* Binarny - serializowany przy użyciu base64
* Tekst
* Wartość logiczna
* Liczba całkowita
* Double
* DateTime

### <a name="supported-methods"></a>Obsługiwane metody

Symulowane urządzenia mogą również reagować na wywołania metody, w którym to przypadku wykonują pewną logikę i zapewniają pewną odpowiedź. Podobnie jak symulacja, logika metody jest przechowywana w pliku JavaScript i może wchodzić w interakcje ze stanem urządzenia. Przykład:

```json
"CloudToDeviceMethods": {
    "Start": {
        "Type": "JavaScript",
        "Path": "release-pressure.js"
    }
}
```

## <a name="create-a-device-definition-file"></a>Tworzenie pliku definicji urządzenia

W tym poradniku można zobaczyć, jak utworzyć model urządzenia dla drona. Dron będzie losowo latać wokół początkowego zestawu współrzędnych zmieniających lokalizację i wysokość.

Skopiuj następujący JSON do edytora tekstu i zapisz go jako **drone.json**.

### <a name="device-definition-json-example"></a>Przykład JSON definicji urządzenia

```json
{
  "SchemaVersion": "1.0.0",
  "Id": "drone",
  "Version": "0.0.1",
  "Name": "Drone",
  "Description": "Simple drone.",
  "Protocol": "AMQP",
  "Simulation": {
    "InitialState": {
      "velocity": 0.0,
      "velocity_unit": "mm/sec",
      "acceleration": 0.0,
      "acceleration_unit": "mm/sec^2",
      "latitude": 47.476075,
      "longitude": -122.192026,
      "altitude": 0.0
    },
    "Interval": "00:00:05",
    "Scripts": [{
      "Type": "JavaScript",
      "Path": "drone-state.js"
    }]
  },
  "Properties": {
    "Type": "Drone",
    "Firmware": "1.0",
    "Model": "P-96"
  },
  "Tags": {
    "Owner": "Contoso"
  },
  "Telemetry": [{
      "Interval": "00:00:05",
      "MessageTemplate": "{\"velocity\":\"${velocity}\",\"acceleration\":\"${acceleration}\",\"position\":\"${latitude}|${longitude}|${altitude}\"}",
      "MessageSchema": {
        "Name": "drone-event-sensor;v1",
        "Format": "JSON",
        "Fields": {
          "velocity": "double",
          "velocity_unit": "text",
          "acceleration": "double",
          "acceleration_unit": "text",
          "latitude": "double",
          "longitude": "double",
          "altitude": "double"
        }
      }
    }
  ],
    "CloudToDeviceMethods": {
        "RecallDrone": {
            "Type": "JavaScript",
            "Path": "droneRecall-method.js"
        }
    }
}
```

## <a name="behavior-script-files"></a>Zachowanie plików skryptów

Kod w pliku skryptu zachowania przenosi drona. Skrypt zmienia podniesienie i lokalizację drona, manipulując urządzeniem w stanie pamięci.

Pliki JavaScript muszą mieć **główną** funkcję, która akceptuje dwa parametry:

* Obiekt **kontekstu,** który zawiera trzy właściwości:
    * **currentTime** jako ciąg z formatem **yyyy-MM-dd'T'HH:mm:sszzz**.
    * **deviceId**. Na przykład **Simulated.Elevator.123**.
    * **deviceModel**. Na przykład **Winda**.
* Obiekt **stanu,** który jest wartością zwracaną przez funkcję w poprzednim wywołaniu. Ten stan urządzenia jest obsługiwany przez usługę symulacji i służy do generowania komunikatów telemetrycznych.

Funkcja **główna** zwraca nowy stan urządzenia. Przykład:

```JavaScript
function main(context, state) {

    // Use context if the simulation depends on
    // time or device details.
    // Execute some logic, updating 'state'

    return state;
}
```

## <a name="create-a-behavior-script-file"></a>Tworzenie pliku skryptu zachowania

Skopiuj następujący JavaScript do edytora tekstu i zapisz go jako **drone-state.js**.

### <a name="device-model-javascript-simulation-example"></a>Przykład symulacji JavaScript modelu urządzenia

```JavaScript
"use strict";

// Position control
const DefaultLatitude = 47.476075;
const DefaultLongitude = -122.192026;
const DistanceVariation = 10;

// Altitude control
const DefaultAltitude = 0.0;
const AverageAltitude = 499.99;
const AltitudeVariation = 5;

// Velocity control
const AverageVelocity = 60.00;
const MinVelocity = 20.00;
const MaxVelocity = 120.00;
const VelocityVariation = 5;

// Acceleration control
const AverageAcceleration = 2.50;
const MinAcceleration = 0.01;
const MaxAcceleration = 9.99;
const AccelerationVariation = 1;

// Display control for position and other attributes
const GeoSpatialPrecision = 6;
const DecimalPrecision = 2;

// Default state
var state = {
    velocity: 0.0,
    velocity_unit: "mm/sec",
    acceleration: 0.0,
    acceleration_unit: "mm/sec^2",
    latitude: DefaultLatitude,
    longitude: DefaultLongitude,
    altitude: DefaultAltitude
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
 */
function vary(avg, percentage, min, max) {
    var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
    value = Math.max(value, min);
    value = Math.min(value, max);
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
/*jslint unparam: true*/
function main(context, previousState, previousProperties) {

    // Restore the global state before generating the new telemetry, so that
    // the telemetry can apply changes using the previous function state.
    restoreSimulation(previousState, previousProperties);

    state.acceleration = vary(AverageAcceleration, AccelerationVariation, MinAcceleration, MaxAcceleration).toFixed(DecimalPrecision);
    state.velocity = vary(AverageVelocity, VelocityVariation, MinVelocity, MaxVelocity).toFixed(DecimalPrecision);

    // Use the last coordinates to calculate the next set with a given variation
    var coords = varylocation(Number(state.latitude), Number(state.longitude), DistanceVariation);
    state.latitude = Number(coords.latitude).toFixed(GeoSpatialPrecision);
    state.longitude = Number(coords.longitude).toFixed(GeoSpatialPrecision);

    // Fluctuate altitude between given variation constant by more or less
    state.altitude = vary(AverageAltitude, AltitudeVariation, AverageAltitude - AltitudeVariation, AverageAltitude + AltitudeVariation).toFixed(DecimalPrecision);

    return state;
}

/**
 * Generate a random geolocation at some distance (in miles)
 * from a given location
 */
function varylocation(latitude, longitude, distance) {
    // Convert to meters, use Earth radius, convert to radians
    var radians = (distance * 1609.344 / 6378137) * (180 / Math.PI);
    return {
        latitude: latitude + radians,
        longitude: longitude + radians / Math.cos(latitude * Math.PI / 180)
    };
}
```

## <a name="create-a-method-script-file"></a>Tworzenie pliku skryptu metody

Skrypty metody są podobne do skryptów zachowania. Określają one zachowanie urządzenia, gdy wywoływana jest określona metoda chmury do urządzenia.

Skrypt przywołania drona ustawia współrzędne drona w stały punkt, aby symulować powrót drona do domu.

Skopiuj następujący JavaScript do edytora tekstu i zapisz go jako **droneRecall-method.js**.

### <a name="device-model-javascript-simulation-example"></a>Przykład symulacji JavaScript modelu urządzenia

```JavaScript
"use strict";

// Default state
var state = {
    velocity: 0.0,
    velocity_unit: "mm/sec",
    acceleration: 0.0,
    acceleration_unit: "mm/sec^2",
    latitude: 0.0,
    longitude: 0.0,
    altitude: 0.0
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
 * Entry point function called by the simulation engine.
 *
 * @param context        The context contains current time, device model and id, not used
 * @param previousState  The device state since the last iteration, not used
 * @param previousProperties  The device properties since the last iteration
 */
/*jslint unparam: true*/
function main(context, previousState, previousProperties) {

    // Restore the global device properties and the global state before
    // generating the new telemetry, so that the telemetry can apply changes
    // using the previous function state.
    restoreSimulation(previousState, previousProperties);

    //simulate the behavior of a drone when recalled
  state.latitude = 47.476075;
  state.longitude = -122.192026;
  return state;
}
```

## <a name="debugging-script-files"></a>Debugowanie plików skryptów

Chociaż nie można dołączyć debugera do uruchomionego pliku zachowania, można zapisać informacje w dzienniku usługi przy użyciu funkcji **dziennika.** W przypadku błędów składni interpreter kończy się niepowodzeniem i zapisuje informacje o wyjątku w dzienniku.

Przykład rejestrowania:

```JavaScript
function main(context, state) {

    log("This message will appear in the service logs.");

    log(context.deviceId);

    if (typeof(state) !== "undefined" && state !== null) {
        log("Previous value: " + state.temperature);
    }

    // ...

    return updateState;
}
```

## <a name="deploy-an-advanced-device-model"></a>Wdrażanie zaawansowanego modelu urządzenia

Aby wdrożyć zaawansowany model urządzenia, należy przekazać pliki wystąpienia symulacji urządzenia:

Na pasku narzędzi wybierz pozycję **Modele urządzeń**. Strona **Modele urządzeń** zawiera listę modeli urządzeń dostępnych w tym wystąpieniu symulacji urządzenia:

![Modele urządzeń](media/iot-accelerators-device-simulation-advanced-device/devicemodelnav.png)

Kliknij pozycję **+ Dodaj modele urządzeń** w prawym górnym rogu strony:

![Dodawanie modelu urządzenia](media/iot-accelerators-device-simulation-advanced-device/devicemodels.png)

Kliknij **pozycję Zaawansowane,** aby otworzyć kartę zaawansowanego modelu urządzenia:

![Karta Zaawansowane](media/iot-accelerators-device-simulation-advanced-device/advancedtab.png)

Kliknij **pozycję Przeglądaj** i wybierz utworzone pliki JSON i JavaScript. Pamiętaj, aby wybrać wszystkie trzy pliki. Jeśli brakuje jednego pliku, sprawdzanie poprawności kończy się niepowodzeniem:

![Przeglądaj pliki](media/iot-accelerators-device-simulation-advanced-device/browse.png)

Jeśli pliki przejdą weryfikację, kliknij przycisk **Zapisz,** a model urządzenia będzie gotowy do użycia w symulacji. W przeciwnym razie napraw wszelkie błędy i ponownie przeładuj pliki:

![Zapisz](media/iot-accelerators-device-simulation-advanced-device/validated.png)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku instrukcja, dowiedzieć się o plikach modelu urządzenia używane w symulacji urządzenia i jak utworzyć zaawansowany model urządzenia. Następnie można zbadać, jak [używać usługi Time Series Insights do wizualizacji danych telemetrycznych wysyłanych z akceleratora rozwiązań symulacji urządzeń.](https://docs.microsoft.com/azure/iot-accelerators/iot-accelerators-device-simulation-time-series-insights)
