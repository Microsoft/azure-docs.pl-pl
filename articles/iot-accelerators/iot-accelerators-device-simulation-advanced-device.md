---
title: Tworzenie zaawansowanego modelu symulowanego urządzenia — Azure| Microsoft Docs
description: Z tego przewodnika dowiesz się, jak utworzyć zaawansowany model urządzenia do użycia z akceleratorem rozwiązania do symulacji urządzeń.
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
- devx-track-js
ms.openlocfilehash: dc2eac4e784d4224581078348f12b231befe1f35
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714114"
---
# <a name="create-an-advanced-device-model"></a>Utwórz zaawansowany model urządzenia

W tym przewodniku opisano pliki JSON i JavaScript definiujące niestandardowy model urządzenia. Ten artykuł zawiera przykładowe pliki definicji modelu urządzenia i pokazuje, jak przekazać je do wystąpienia symulacji urządzenia. Możesz tworzyć zaawansowane modele urządzeń, aby symulować bardziej realistyczne zachowania urządzeń podczas testowania.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku, potrzebujesz wdrożonego wystąpienia symulacji urządzenia w ramach subskrypcji platformy Azure.

Jeśli symulacja urządzenia nie została jeszcze wdrożona, zobacz [Wdrażanie symulacji urządzenia w](https://github.com/Azure/azure-iot-pcs-device-simulation/blob/master/README.md) witrynie GitHub.

### <a name="open-device-simulation"></a>Otwieranie symulacji urządzenia

Jeśli symulacja urządzenia nie została jeszcze wdrożona, zobacz [Wdrażanie symulacji urządzenia w](https://github.com/Azure/azure-iot-pcs-device-simulation/blob/master/README.md) witrynie GitHub.

## <a name="device-models"></a>Modele urządzeń

Każde symulowane urządzenie należy do określonego modelu urządzenia, który definiuje zachowanie symulacji. To zachowanie obejmuje częstotliwości wysyłania danych telemetrycznych, rodzaj komunikatów do wysłania i obsługiwane metody.

Model urządzenia definiuje się przy użyciu pliku definicji urządzenia JSON i zestawu plików JavaScript. Te pliki JavaScript definiują zachowanie symulacji, takie jak losowa telemetria i logika metody.

Typowy model urządzenia ma:

* Jeden plik JSON dla każdego modelu urządzenia (na przykład elevator.jssię).
* Jeden plik skryptu zachowania języka JavaScript dla każdego modelu urządzenia (na przykład elevator-state.js)
* Jeden plik skryptu metody JavaScript dla każdej metody urządzenia (na przykład elevator-go-down.js)

> [!NOTE]
> Nie wszystkie modele urządzeń definiują metody. W związku z tym model urządzenia może mieć skrypty metod lub nie. Jednak wszystkie modele urządzeń muszą mieć skrypt zachowania.

## <a name="device-definition-file"></a>Plik definicji urządzenia

Każdy plik definicji urządzenia zawiera szczegóły symulowanego modelu urządzenia, w tym następujące informacje:

* Nazwa modelu urządzenia: ciąg.
* Protokół: Protokół AMQP | MQTT | PROTOKÓŁ HTTP.
* Początkowy stan urządzenia.
* Jak często odświeżać stan urządzenia.
* Którego pliku JavaScript użyć do odświeżenia stanu urządzenia.
* Lista komunikatów telemetrii do wysłania, z których każdy ma określoną częstotliwość.
* Schemat komunikatów telemetrii używany przez aplikację serwera końcowego do analizowania odebranych danych telemetrycznych.
* Lista obsługiwanych metod i plik JavaScript do użycia w celu symulowania każdej metody.

### <a name="file-schema"></a>Schemat pliku

Wersja schematu to zawsze "1.0.0" i jest specyficzna dla formatu tego pliku:

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

Urządzenia IoT mogą łączyć się przy użyciu różnych protokołów. Symulacja umożliwia użycie protokołu **AMQP,** **MQTT** lub **HTTP:**

```json
"Protocol": "AMQP"
```

### <a name="simulated-device-state"></a>Stan symulowanego urządzenia

Każde symulowane urządzenie ma stan wewnętrzny, który należy zdefiniować. Stan definiuje również właściwości, które mogą być zgłaszane w telemetrii. Na przykład chłodziarz może mieć stan początkowy, taki jak:

```json
"InitialState": {
    "temperature": 50,
    "humidity": 40
},
```

Urządzenie przenośne z kilkoma czujnikami może mieć więcej właściwości, na przykład:

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

Stan urządzenia jest przechowywany w pamięci przez usługę symulacji i dostarczany jako dane wejściowe dla funkcji języka JavaScript. Funkcja języka JavaScript może zdecydować:

* Zignorowanie stanu i wygenerowanie danych losowych.
* Zaktualizowanie stanu urządzenia w realistyczny sposób dla danego scenariusza.

Funkcja, która generuje stan, również otrzymuje jako dane wejściowe:

* Identyfikator urządzenia.
* Model urządzenia.
* Bieżąca godzina. Ta wartość umożliwia generowanie różnych danych według urządzenia i czasu.

### <a name="generating-telemetry-messages"></a>Generowanie komunikatów telemetrii

Usługa symulacji może wysyłać kilka typów danych telemetrycznych dla każdego urządzenia. Zazwyczaj dane telemetryczne obejmują dane ze stanu urządzenia. Na przykład symulowane pomieszczenie może wysyłać informacje o temperaturze i wilgotności co 10 sekund. Zwróć uwagę na symbole zastępcze w poniższym fragmencie kodu, które są automatycznie zastępowane wartościami ze stanu urządzenia:

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

Symbole zastępcze używają specjalnej składni **${NAME},** gdzie **NAME** jest kluczem z obiektu stanu urządzenia zwróconego przez funkcję **główną języka** JavaScript. Ciągi powinny być w cudzysłowy, a liczby nie powinny.

#### <a name="message-schema"></a>Schemat komunikatów

Każdy typ komunikatu musi mieć dobrze zdefiniowany schemat. Schemat komunikatów jest również publikowany w IoT Hub, dzięki czemu aplikacje back-end mogą ponownie używać informacji do interpretowania przychodzących danych telemetrycznych.

Schemat obsługuje format JSON, który umożliwia łatwe analizowanie, przekształcanie i analizowanie w kilku systemach i usługach.

Pola wymienione w schemacie mogą być następującego typu:

* Obiekt — serializowany przy użyciu danych JSON
* Dane binarne — serializowane przy użyciu base64
* Tekst
* Wartość logiczna
* Liczba całkowita
* Double
* DateTime

### <a name="supported-methods"></a>Obsługiwane metody

Symulowane urządzenia mogą również reagować na wywołania metod, w takim przypadku wykonują logikę i zapewniają pewne odpowiedzi. Podobnie jak symulacja, logika metody jest przechowywana w pliku JavaScript i może wchodzić w interakcje ze stanem urządzenia. Na przykład:

```json
"CloudToDeviceMethods": {
    "Start": {
        "Type": "JavaScript",
        "Path": "release-pressure.js"
    }
}
```

## <a name="create-a-device-definition-file"></a>Tworzenie pliku definicji urządzenia

W tym przewodniku zobaczysz, jak utworzyć model urządzenia dla drona. Dron losowo lecą wokół początkowego zestawu współrzędnych, zmieniając lokalizację i wysokość nad poziomem morza.

Skopiuj następujący kod JSON do edytora tekstów i zapisz go jakodrone.js **pliku**.

### <a name="device-definition-json-example"></a>Przykładowe dane JSON definicji urządzenia

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

## <a name="behavior-script-files"></a>Pliki skryptów zachowania

Kod w pliku skryptu zachowania przenosi drona. Skrypt zmienia wysokość i lokalizację drona, manipulując stanem pamięci urządzenia.

Pliki JavaScript muszą mieć **funkcję main,** która akceptuje dwa parametry:

* Obiekt **kontekstu,** który zawiera trzy właściwości:
    * **currentTime** jako ciąg w formacie **yyyy-MM-dd'T'HH:mm:sszzz**.
    * **deviceId**. Na przykład **Simulated.Elevator.123.**
    * **deviceModel**. Na przykład **Winda**.
* Obiekt **stanu,** który jest wartością zwróconą przez funkcję w poprzednim wywołaniu. Ten stan urządzenia jest utrzymywany przez usługę symulacji i używany do generowania komunikatów telemetrycznych.

Funkcja **main** zwraca nowy stan urządzenia. Na przykład:

```JavaScript
function main(context, state) {

    // Use context if the simulation depends on
    // time or device details.
    // Execute some logic, updating 'state'

    return state;
}
```

## <a name="create-a-behavior-script-file"></a>Tworzenie pliku skryptu zachowania

Skopiuj następujący kod JavaScript do edytora tekstów i zapisz go jako **drone-state.js**.

### <a name="device-model-javascript-simulation-example"></a>Przykład symulacji języka JavaScript modelu urządzenia

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

Skrypty metod są podobne do skryptów zachowania. Definiują one zachowanie urządzenia w przypadku wywoływania określonej metody chmura-urządzenie.

Skrypt przywołania drona ustawia współrzędne drona na stały punkt, aby zasymulować powrót drona do domu.

Skopiuj następujący kod JavaScript do edytora tekstów i zapisz go jako **droneRecall-method.js**.

### <a name="device-model-javascript-simulation-example"></a>Przykład symulacji języka JavaScript modelu urządzenia

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

## <a name="debugging-script-files"></a>Debugowanie plików skryptu

Nie można dołączyć debugera do uruchomionego pliku zachowania, ale można zapisywać informacje w dzienniku usługi przy użyciu **funkcji log.** W przypadku błędów składni interpreter kończy się niepowodzeniem i zapisuje informacje o wyjątku w dzienniku.

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

Aby wdrożyć zaawansowany model urządzenia, przekaż pliki wystąpienia symulacji urządzenia:

Na pasku narzędzi wybierz pozycję **Modele urządzeń**. Na **stronie Modele** urządzeń jest dostępna lista modeli urządzeń dostępnych w tym wystąpieniu symulacji urządzenia:

![Modele urządzeń](media/iot-accelerators-device-simulation-advanced-device/devicemodelnav.png)

Kliknij pozycję **+ Dodaj modele urządzeń** w prawym górnym rogu strony:

![Dodawanie modelu urządzenia](media/iot-accelerators-device-simulation-advanced-device/devicemodels.png)

Kliknij **pozycję Zaawansowane,** aby otworzyć kartę zaawansowanego modelu urządzenia:

![Karta Zaawansowane](media/iot-accelerators-device-simulation-advanced-device/advancedtab.png)

Kliknij **przycisk Przeglądaj** i wybierz utworzone pliki JSON i JavaScript. Pamiętaj, aby wybrać wszystkie trzy pliki. Jeśli brakuje jakieśgo pliku, walidacja nie powiedzie się:

![Przeglądaj pliki](media/iot-accelerators-device-simulation-advanced-device/browse.png)

Jeśli twoje pliki przejdą walidację, kliknij przycisk **Zapisz,** aby model urządzenia był gotowy do użycia w symulacji. W przeciwnym razie napraw wszelkie błędy i ponownie załaduj pliki:

![Zapisz](media/iot-accelerators-device-simulation-advanced-device/validated.png)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku z how-to-to-guide opisano pliki modelu urządzenia używane w symulacji urządzenia i sposób tworzenia zaawansowanego modelu urządzenia. Następnie możesz dowiedzieć się, jak za pomocą usługi Time Series Insights wizualizować dane telemetryczne wysyłane z [akceleratora rozwiązania do symulacji urządzeń.](./iot-accelerators-device-simulation-time-series-insights.md)