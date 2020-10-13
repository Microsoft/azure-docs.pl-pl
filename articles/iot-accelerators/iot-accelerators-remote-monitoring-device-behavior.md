---
title: Symulowane urządzenie w rozwiązaniu do zdalnego monitorowania — Azure | Microsoft Docs
description: W tym artykule opisano sposób użycia języka JavaScript w celu zdefiniowania zachowania symulowanego urządzenia w rozwiązaniu do zdalnego monitorowania.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.custom: devx-track-js
ms.openlocfilehash: 7f887aac91bdb1b8c752806c7c5076708a40bc10
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91276173"
---
# <a name="implement-the-device-model-behavior"></a>Zaimplementuj zachowanie modelu urządzenia

W tym artykule opisano schemat [modelu urządzenia](iot-accelerators-remote-monitoring-device-schema.md) opisany w schemacie, który definiuje model symulowanego urządzenia. Ten artykuł odnosił się do dwóch typów plików JavaScript, które implementują zachowanie symulowanego urządzenia:

- **Stan** Pliki JavaScript, które są uruchamiane w stałych interwałach, w celu zaktualizowania stanu wewnętrznego urządzenia.
- **Metoda** Pliki JavaScript, które są uruchamiane, gdy rozwiązanie wywołuje metodę na urządzeniu.

> [!NOTE]
> Zachowania modelu urządzenia są przeznaczone tylko dla symulowanych urządzeń hostowanych w usłudze symulacji urządzenia. Jeśli chcesz utworzyć rzeczywiste urządzenie, zobacz [łączenie urządzenia z akceleratorem rozwiązania do monitorowania zdalnego](iot-accelerators-connecting-devices.md).

W tym artykule omówiono sposób wykonywania następujących zadań:

>[!div class="checklist"]
> * Sterowanie stanem symulowanego urządzenia
> * Zdefiniuj, w jaki sposób symulowane urządzenie odpowiada na wywołanie metody z rozwiązania do zdalnego monitorowania
> * Debugowanie skryptów

## <a name="state-behavior"></a>Zachowanie stanu

Sekcja [symulacja](../../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#simulation) schematu modelu urządzenia definiuje stan wewnętrzny symulowanego urządzenia:

- `InitialState` definiuje początkowe wartości dla wszystkich właściwości obiektu stanu urządzenia.
- `Script` identyfikuje plik JavaScript, który jest uruchamiany zgodnie z harmonogramem w celu zaktualizowania stanu urządzenia.

Poniższy przykład przedstawia definicję obiektu stanu urządzenia symulowanego urządzenia chłodzenia:

```json
"Simulation": {
  "InitialState": {
    "online": true,
    "temperature": 75.0,
    "temperature_unit": "F",
    "humidity": 70.0,
    "humidity_unit": "%",
    "pressure": 150.0,
    "pressure_unit": "psig",
    "simulation_state": "normal_pressure"
  },
  "Interval": "00:00:05",
  "Scripts": {
    "Type": "javascript",
    "Path": "chiller-01-state.js"
  }
}
```

Stan symulowanego urządzenia, zgodnie z definicją w `InitialState` sekcji, jest przechowywany w pamięci przez usługę symulacji. Informacje o stanie są przesyłane jako dane wejściowe do `main` funkcji zdefiniowanej w **chiller-01-state.js**. W tym przykładzie usługa symulacji uruchamia plik **chiller-01-state.js** co pięć sekund. Skrypt może zmodyfikować stan symulowanego urządzenia.

Poniżej przedstawiono konspekt typowej `main` funkcji:

```javascript
function main(context, previousState, previousProperties) {

  // Use the previous device state to
  // generate the new device state
  // returned by the function.

  return state;
}
```

`context`Parametr ma następujące właściwości:

- `currentTime` jako ciąg z formatem `yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`, na przykład `Simulated.Chiller.123`
- `deviceModel`, na przykład `Chiller`

`state`Parametr zawiera stan urządzenia obsługiwanego przez usługę symulacji urządzenia. Ta wartość jest `state` obiektem zwracanym przez poprzednie wywołanie metody `main` .

Poniższy przykład przedstawia typową implementację `main` metody do obsługi stanu urządzenia obsługiwanego przez usługę symulacji:

```javascript
// Default state
var state = {
  online: true,
  temperature: 75.0,
  temperature_unit: "F",
  humidity: 70.0,
  humidity_unit: "%",
  pressure: 150.0,
  pressure_unit: "psig",
  simulation_state: "normal_pressure"
};

function restoreState(previousState) {
  // If the previous state is null, force a default state
  if (previousState !== undefined && previousState !== null) {
      state = previousState;
  } else {
      log("Using default state");
  }
}

function main(context, previousState, previousProperties) {

  restoreState(previousState);

  // Update state

  return state;
}
```

Poniższy przykład pokazuje, jak `main` Metoda może symulować wartości telemetryczne, które różnią się w zależności od czasu:

```javascript
/**
 * Simple formula generating a random value
 * around the average and between min and max
 */
function vary(avg, percentage, min, max) {
  var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
  value = Math.max(value, min);
  value = Math.min(value, max);
  return value;
}


function main(context, previousState, previousProperties) {

    restoreState(previousState);

    // 75F +/- 5%,  Min 25F, Max 100F
    state.temperature = vary(75, 5, 25, 100);

    // 70% +/- 5%,  Min 2%, Max 99%
    state.humidity = vary(70, 5, 2, 99);

    log("Simulation state: " + state.simulation_state);
    if (state.simulation_state === "high_pressure") {
        // 250 psig +/- 25%,  Min 50 psig, Max 300 psig
        state.pressure = vary(250, 25, 50, 300);
    } else {
        // 150 psig +/- 10%,  Min 50 psig, Max 300 psig
        state.pressure = vary(150, 10, 50, 300);
    }

    return state;
}
```

Pełną [chiller-01-state.js](https://github.com/Azure/device-simulation-dotnet/blob/master/Services/data/devicemodels/scripts/chiller-01-state.js) można wyświetlić w witrynie GitHub.

## <a name="method-behavior"></a>Zachowanie metody

Sekcja [CloudToDeviceMethods](../../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#cloudtodevicemethods) schematu modelu urządzenia definiuje metody, na które symulowane urządzenie reaguje.

Poniższy przykład pokazuje listę metod obsługiwanych przez symulowane urządzenie chłodzenia.

```json
"CloudToDeviceMethods": {
  "Reboot": {
    "Type": "javascript",
    "Path": "Reboot-method.js"
  },
  "FirmwareUpdate": {
    "Type": "javascript",
    "Path": "FirmwareUpdate-method.js"
  },
  "EmergencyValveRelease": {
    "Type": "javascript",
    "Path": "EmergencyValveRelease-method.js"
  },
  "IncreasePressure": {
    "Type": "javascript",
    "Path": "IncreasePressure-method.js"
  }
}
```

Każda metoda ma skojarzony plik JavaScript, który implementuje zachowanie metody.

Stan symulowanego urządzenia, zgodnie z definicją w `InitialState` sekcji schematu, jest przechowywany w pamięci przez usługę symulacji. Informacje o stanie są przesyłane jako dane wejściowe do `main` funkcji zdefiniowanej w pliku JavaScript, gdy wywoływana jest metoda. Skrypt może zmodyfikować stan symulowanego urządzenia.

Poniżej przedstawiono konspekt typowej `main` funkcji:

```javascript
function main(context, previousState, previousProperties) {

}
```

`context`Parametr ma następujące właściwości:

- `currentTime` jako ciąg z formatem `yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`, na przykład `Simulated.Chiller.123`
- `deviceModel`, na przykład `Chiller`

`state`Parametr zawiera stan urządzenia obsługiwanego przez usługę symulacji urządzenia.

`properties`Parametr zawiera właściwości urządzenia, które są zapisywane jako raportowane właściwości dla sznurka urządzenia IoT Hub.

Istnieją trzy funkcje globalne, których można użyć, aby pomóc zaimplementować zachowanie metody:

- `updateState` Aby zaktualizować stan przechowywany przez usługę symulacji.
- `updateProperty` Aktualizowanie pojedynczej właściwości urządzenia.
- `sleep` w celu wstrzymania wykonywania w celu symulowania długotrwałego zadania.

Poniższy przykład przedstawia skróconą wersję skryptu **IncreasePressure-method.js** używanego przez symulowane urządzenia chłodzenia:

```javascript
function main(context, previousState, previousProperties) {

    log("Starting 'Increase Pressure' method simulation (5 seconds)");

    // Pause the simulation and change the simulation mode so that the
    // temperature will fluctuate at ~250 when it resumes
    var state = {
        simulation_state: "high_pressure",
        CalculateRandomizedTelemetry: false
    };
    updateState(state);

    // Increase
    state.pressure = 210;
    updateState(state);
    log("Pressure increased to " + state.pressure);
    sleep(1000);

    // Increase
    state.pressure = 250;
    updateState(state);
    log("Pressure increased to " + state.pressure);
    sleep(1000);

    // Resume the simulation
    state.CalculateRandomizedTelemetry = true;
    updateState(state);

    log("'Increase Pressure' method simulation completed");
}
```

## <a name="debugging-script-files"></a>Debugowanie plików skryptów

Nie można dołączyć debugera do interpretera języka JavaScript używanego przez usługę symulacji urządzenia do uruchamiania skryptów stanu i metod. Można jednak rejestrować informacje w dzienniku usługi. Wbudowana `log()` Funkcja umożliwia zapisywanie informacji do śledzenia i debugowania wykonywania funkcji.

Jeśli wystąpi błąd składni interpretera kończy się niepowodzeniem i zapisuje wpis w `Jint.Runtime.JavaScriptException` dzienniku usługi.

W artykule dotyczącym [uruchamiania usługi w serwisie](https://github.com/Azure/device-simulation-dotnet#running-the-service-locally-eg-for-development-tasks) GitHub pokazano, jak uruchomić usługę symulacji urządzenia lokalnie. Uruchamianie usługi lokalnie ułatwia debugowanie symulowanych urządzeń przed ich wdrożeniem w chmurze.

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano sposób definiowania zachowania własnego niestandardowego symulowanego modelu urządzenia. W tym artykule pokazano, jak:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Sterowanie stanem symulowanego urządzenia
> * Zdefiniuj, w jaki sposób symulowane urządzenie odpowiada na wywołanie metody z rozwiązania do zdalnego monitorowania
> * Debugowanie skryptów

Teraz, gdy wiesz już, jak określić zachowanie symulowanego urządzenia, sugerowanym następnym krokiem jest zapoznanie się z tematem [tworzenia symulowanego urządzenia](iot-accelerators-remote-monitoring-create-simulated-device.md).

Aby uzyskać więcej informacji programistycznych dotyczących rozwiązania do zdalnego monitorowania, zobacz:

* [Przewodnik informacyjny dla deweloperów](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Przewodnik po rozwiązywaniu problemów dla deweloperów](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->
