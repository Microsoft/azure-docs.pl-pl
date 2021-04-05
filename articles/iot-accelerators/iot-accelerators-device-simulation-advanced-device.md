---
title: Tworzenie zaawansowanego symulowanego modelu urządzenia — Azure | Microsoft Docs
description: W tym przewodniku krok po kroku dowiesz się, jak utworzyć zaawansowany model urządzenia do użycia z akceleratorem rozwiązania do symulacji urządzeń.
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
ms.openlocfilehash: 58891bdb4601744129c97454f43202d3ba07b709
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96852561"
---
# <a name="create-an-advanced-device-model"></a>Utwórz zaawansowany model urządzenia

Ten przewodnik zawiera opis plików JSON i JavaScript, które definiują niestandardowy model urządzeń. Artykuł zawiera kilka przykładowych plików definicji modelu urządzeń i pokazuje, jak przekazać je do wystąpienia symulacji urządzenia. Można tworzyć zaawansowane modele urządzeń w celu symulowania bardziej realistycznych zachowań urządzeń dla testowania.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku, musisz mieć wdrożone wystąpienie symulacji urządzenia w ramach subskrypcji platformy Azure.

Jeśli nie wdrożono jeszcze symulacji urządzenia, zobacz [wdrażanie symulacji urządzenia](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md) w serwisie GitHub.

### <a name="open-device-simulation"></a>Otwieranie symulacji urządzenia

Jeśli nie wdrożono jeszcze symulacji urządzenia, zobacz [wdrażanie symulacji urządzenia](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md) w serwisie GitHub.

## <a name="device-models"></a>Modele urządzeń

Każde symulowane urządzenie należy do określonego modelu urządzenia, który definiuje zachowanie symulacji. To zachowanie obejmuje częstotliwość wysyłania telemetrii, rodzaju komunikatów do wysłania oraz obsługiwanych metod.

Model urządzenia można zdefiniować przy użyciu pliku definicji urządzenia JSON oraz zestawu plików JavaScript. Te pliki języka JavaScript definiują zachowanie symulacji, takie jak losowe dane telemetryczne i logikę metody.

Typowy model urządzenia:

* Jeden plik JSON dla każdego modelu urządzenia (na przykład elevator.json).
* Jeden plik skryptu zachowań JavaScript dla każdego modelu urządzenia (na przykład elevator-state.js)
* Jeden plik skryptu metody JavaScript dla każdej metody urządzenia (na przykład elevator-go-down.js)

> [!NOTE]
> Nie wszystkie modele urządzeń definiują metody. W związku z tym model urządzenia może być lub nie mieć skryptów metod. Jednak wszystkie modele urządzeń muszą mieć skrypt zachowania.

## <a name="device-definition-file"></a>Plik definicji urządzenia

Każdy plik definicji urządzenia zawiera szczegółowe informacje o modelu symulowanego urządzenia, w tym następujące informacje:

* Nazwa modelu urządzenia: ciąg.
* Protokół: AMQP | MQTT | Protokoły.
* Początkowy stan urządzenia.
* Częstotliwość odświeżania stanu urządzenia.
* Plik języka JavaScript, który ma zostać użyty do odświeżenia stanu urządzenia.
* Lista komunikatów telemetrycznych do wysłania, z których każdy ma określoną częstotliwość.
* Schemat komunikatów telemetrycznych używany przez aplikację zaplecza do analizowania odebranych danych telemetrycznych.
* Lista obsługiwanych metod i plik języka JavaScript, który ma być używany do symulowania każdej metody.

### <a name="file-schema"></a>Schemat pliku

Wersja schematu jest zawsze "1.0.0" i jest specyficzna dla formatu tego pliku:

```json
"SchemaVersion": "1.0.0"
```

### <a name="device-model-description"></a>Opis modelu urządzenia

Poniższe właściwości opisują model urządzenia. Każdy typ ma unikatowy identyfikator, wersję semantyczną, nazwę i opis:

```json
"Id": "chiller-01",
"Version": "0.0.1",
"Name": "Chiller",
"Description": "Chiller with external temperature and humidity sensors."
```

### <a name="iot-protocol"></a>Protokół IoT

Urządzenia IoT mogą łączyć się przy użyciu różnych protokołów. Symulacja pozwala korzystać z **AMQP**, **MQTT** lub **http**:

```json
"Protocol": "AMQP"
```

### <a name="simulated-device-state"></a>Stan symulowanego urządzenia

Każde symulowane urządzenie ma stan wewnętrzny, który musi być zdefiniowany. Stan definiuje również właściwości, które mogą być zgłaszane w telemetrii. Na przykład, chłodner może mieć stan początkowy, taki jak:

```json
"InitialState": {
    "temperature": 50,
    "humidity": 40
},
```

Przeniesienie urządzenia z kilkoma czujnikami może mieć więcej właściwości, na przykład:

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

Stan urządzenia jest przechowywany w pamięci przez usługę symulacji i zapewniany jako dane wejściowe funkcji JavaScript. Funkcja języka JavaScript może zdecydować:

* Aby zignorować stan i wygenerować pewne dane losowe.
* Aby zaktualizować stan urządzenia w sposób niedrogi w danym scenariuszu.

Funkcja, która generuje stan, również otrzymuje jako dane wejściowe:

* Identyfikator urządzenia.
* Model urządzenia.
* Bieżący czas. Ta wartość umożliwia generowanie różnych danych według urządzenia i czasu.

### <a name="generating-telemetry-messages"></a>Generowanie komunikatów telemetrycznych

Usługa symulacji może wysyłać kilka typów telemetrii dla każdego urządzenia. Zazwyczaj Telemetria zawiera dane ze stanu urządzenia. Na przykład symulowane pomieszczenie może wysyłać informacje o temperatury i wilgotności co 10 sekund. Zanotuj symbole zastępcze w poniższym fragmencie kodu, które są automatycznie zastępowane wartościami ze stanu urządzenia:

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

Symbole zastępcze używają specjalnej składni **$ {name}** , gdzie **name** jest kluczem z obiektu stanu urządzenia zwróconego przez funkcję **Main** języka JavaScript. Ciągi powinny być ujęte w cudzysłów, a liczby nie powinny.

#### <a name="message-schema"></a>Schemat wiadomości

Każdy typ komunikatu musi mieć dobrze zdefiniowany schemat. Schemat komunikatów jest również publikowany w IoT Hub, dzięki czemu aplikacje zaplecza mogą ponownie wykorzystać te informacje do interpretacji przychodzącej telemetrii.

Schemat obsługuje format JSON, który umożliwia łatwe analizowanie, przekształcanie i analizowanie w kilku systemach i usługach.

Pola wymienione w schemacie mogą być następujące:

* Serializacja obiektu za pomocą kodu JSON
* Serializacja binarna przy użyciu algorytmu Base64
* Tekst
* Wartość logiczna
* Liczba całkowita
* Double
* DateTime

### <a name="supported-methods"></a>Obsługiwane metody

Symulowane urządzenia mogą również reagować na wywołania metod, w tym przypadku wykonują pewne logike i dostarczają odpowiedzi. Podobnie jak w przypadku symulacji, logika metody jest przechowywana w pliku JavaScript i może współdziałać ze stanem urządzenia. Na przykład:

```json
"CloudToDeviceMethods": {
    "Start": {
        "Type": "JavaScript",
        "Path": "release-pressure.js"
    }
}
```

## <a name="create-a-device-definition-file"></a>Utwórz plik definicji urządzenia

W tym przewodniku opisano sposób tworzenia modelu urządzenia dla drona. Drona będzie losowo przepływać wokół początkowego zestawu współrzędnych zmiany lokalizacji i wysokości.

Skopiuj poniższy kod JSON do edytora tekstu i Zapisz go jako **drone.js**.

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

## <a name="behavior-script-files"></a>Pliki skryptów zachowań

Kod w pliku skryptu zachowania przenosi drona. Skrypt zmienia podnoszenie i lokalizację drona przez manipulowanie stanem pamięci urządzenia.

Pliki JavaScript muszą mieć **główną** funkcję, która akceptuje dwa parametry:

* Obiekt **kontekstu** , który zawiera trzy właściwości:
    * **currentTime** jako ciąg o formacie **rrrr-mm-dd'T'HH: mm: sszzz**.
    * Identyfikator **urządzenia.** Na przykład **symulowane. wind. 123**.
    * **deviceModel**. Na przykład **Wind**.
* Obiekt **stanu** , który jest wartością zwracaną przez funkcję w poprzednim wywołaniu. Ten stan urządzenia jest obsługiwany przez usługę symulacji i służy do generowania komunikatów telemetrycznych.

Funkcja **Main** zwraca nowy stan urządzenia. Na przykład:

```JavaScript
function main(context, state) {

    // Use context if the simulation depends on
    // time or device details.
    // Execute some logic, updating 'state'

    return state;
}
```

## <a name="create-a-behavior-script-file"></a>Utwórz plik skryptu zachowania

Skopiuj poniższy kod JavaScript do edytora tekstów i Zapisz go jako **drone-state.js**.

### <a name="device-model-javascript-simulation-example"></a>Przykład symulacji kodu JavaScript w modelu urządzenia

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

## <a name="create-a-method-script-file"></a>Utwórz plik skryptu metody

Skrypty metod są podobne do skryptów zachowań. Określają one zachowanie urządzenia w przypadku wywołania określonej metody chmury do urządzenia.

Skrypt odwoływania drona ustawia współrzędne drona na stały punkt, aby symulować drona zwracające stronę główną.

Skopiuj poniższy kod JavaScript do edytora tekstów i Zapisz go jako **droneRecall-method.js**.

### <a name="device-model-javascript-simulation-example"></a>Przykład symulacji kodu JavaScript w modelu urządzenia

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

Chociaż nie można dołączyć debugera do działającego pliku zachowań, istnieje możliwość zapisu informacji w dzienniku usługi za pomocą funkcji **log** . W przypadku błędów składni interpreter nie powiedzie się i zapisuje informacje o wyjątku w dzienniku.

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

Aby wdrożyć zaawansowany model urządzenia, Przekaż pliki do wystąpienia symulacji urządzenia:

Na pasku narzędzi wybierz pozycję **Modele urządzeń**. Na stronie **modele urządzeń** są wyświetlane modele urządzeń dostępne w tym wystąpieniu symulacji urządzenia:

![Modele urządzeń](media/iot-accelerators-device-simulation-advanced-device/devicemodelnav.png)

Kliknij pozycję **+ Dodaj modele urządzeń** w prawym górnym rogu strony:

![Dodawanie modelu urządzenia](media/iot-accelerators-device-simulation-advanced-device/devicemodels.png)

Kliknij przycisk **Zaawansowane** , aby otworzyć kartę Zaawansowane modele urządzeń:

![Karta Zaawansowane](media/iot-accelerators-device-simulation-advanced-device/advancedtab.png)

Kliknij przycisk **Przeglądaj** i wybierz utworzone pliki JSON i JavaScript. Upewnij się, że wybrano wszystkie trzy pliki. Jeśli brakuje jednego pliku, weryfikacja nie powiedzie się:

![Przeglądaj pliki](media/iot-accelerators-device-simulation-advanced-device/browse.png)

Jeśli pliki przechodzą walidację, kliknij przycisk **Zapisz** , a model urządzenia jest gotowy do użycia w symulacji. W przeciwnym razie Popraw błędy i przekazanie plików:

![Zapisz](media/iot-accelerators-device-simulation-advanced-device/validated.png)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku poznasz informacje o plikach modelu urządzenia używane w symulacji urządzenia i sposobach tworzenia zaawansowanego modelu urządzeń. Następnie warto dowiedzieć się, jak [za pomocą Time Series Insights wizualizować dane telemetryczne wysyłane z akceleratora rozwiązania do symulacji urządzeń](./iot-accelerators-device-simulation-time-series-insights.md).