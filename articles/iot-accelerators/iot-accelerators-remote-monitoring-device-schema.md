---
title: Schemat urządzenia w rozwiązaniu do zdalnego monitorowania — Azure | Microsoft Docs
description: W tym artykule opisano schemat JSON, który definiuje symulowane urządzenie w rozwiązaniu do zdalnego monitorowania.
author: dominicbetts
manager: philmea
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 12/18/2018
ms.topic: conceptual
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: ac681bb13ccea49c7a2f566a6fcdb6adb8cec5bb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "81683742"
---
# <a name="understand-the-device-model-schema"></a>Opis schematu modelu urządzenia

Aby przetestować zachowanie, można użyć symulowanych urządzeń w rozwiązaniu do zdalnego monitorowania. Rozwiązanie do monitorowania zdalnego obejmuje usługę symulacji urządzenia do uruchamiania symulowanych urządzeń. Po wdrożeniu rozwiązania do monitorowania zdalnego automatycznie jest inicjowana kolekcja symulowanych urządzeń. Można dostosować istniejące symulowane urządzenia lub utworzyć własne.

W tym artykule opisano schemat modelu urządzenia, który określa możliwości i zachowanie symulowanego urządzenia. Model urządzenia jest przechowywany w pliku JSON.

> [!NOTE]
> Ten schemat modelu urządzenia jest przeznaczony tylko dla symulowanych urządzeń hostowanych w usłudze symulacji urządzenia. Jeśli chcesz utworzyć rzeczywiste urządzenie, zobacz [łączenie urządzenia z akceleratorem rozwiązania do monitorowania zdalnego](iot-accelerators-connecting-devices.md).

Poniższe artykuły dotyczą bieżącego artykułu:

* [Zaimplementuj zachowanie modelu urządzenia](iot-accelerators-remote-monitoring-device-behavior.md) opisuje pliki języka JavaScript, które są używane do implementowania zachowania symulowanego urządzenia.
* [Utwórz nowe symulowane urządzenie](iot-accelerators-remote-monitoring-create-simulated-device.md) , umieszcza je razem i pokazuje, jak wdrożyć nowy typ symulowanego urządzenia w rozwiązaniu.

W tym artykule omówiono sposób wykonywania następujących zadań:

>[!div class="checklist"]
> * Używanie pliku JSON do definiowania symulowanego modelu urządzenia
> * Określ właściwości symulowanego urządzenia
> * Określ dane telemetryczne wysyłane przez urządzenie symulowane
> * Określ metody z chmury do urządzenia

## <a name="the-parts-of-the-device-model-schema"></a>Części schematu modelu urządzenia

Każdy model urządzenia, taki jak chłodzenie lub ciężarówka, definiuje typ urządzenia, które może symulować usługa symulacji. Każdy model urządzenia jest przechowywany w pliku JSON z następującym schematem najwyższego poziomu:

```json
{
  "SchemaVersion": "1.0.0",
  "Id": "elevator-01",
  "Version": "0.0.1",
  "Name": "Elevator",
  "Description": "Elevator with floor, vibration and temperature sensors.",
  "Protocol": "AMQP",
  "Simulation": {
    // Specify the simulation behavior
  },
  "Properties": {
    // Define properties
  },
  "Telemetry": [
    // Specify telemetry
  ],
  "CloudToDeviceMethods": {
    // Specify methods
  }
}
```

Pliki schematu dla domyślnych symulowanych urządzeń można wyświetlić w [folderze DeviceModels](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels) w witrynie GitHub.

W poniższej tabeli opisano wpisy schematu najwyższego poziomu:

| Wpis schematu | Opis |
| -- | --- |
| `SchemaVersion` | Wersja schematu jest zawsze `1.0.0` i jest specyficzna dla formatu tego pliku. |
| `Id` | Unikatowy identyfikator tego modelu urządzenia. |
| `Version` | Identyfikuje wersję modelu urządzenia. |
| `Name` | Przyjazna nazwa dla modelu urządzenia. |
| `Description` | Opis modelu urządzenia. |
| `Protocol` | Protokół połączenia wykorzystywany przez urządzenie. Może być jedną z `AMQP` , `MQTT` , i `HTTP` . |

W poniższych sekcjach opisano inne sekcje schematu JSON:

## <a name="simulation"></a>Symulacja

W `Simulation` sekcji definiujesz wewnętrzny stan symulowanego urządzenia. Wszystkie wartości telemetryczne wysyłane przez urządzenie muszą być częścią tego stanu urządzenia.

Definicja stanu urządzenia ma dwa elementy:

* `InitialState` definiuje początkowe wartości dla wszystkich właściwości obiektu stanu urządzenia.
* `Script` identyfikuje plik JavaScript, który jest uruchamiany zgodnie z harmonogramem w celu zaktualizowania stanu urządzenia. Ten plik skryptu służy do losowego generowania wartości telemetrycznych wysyłanych przez urządzenie.

Aby dowiedzieć się więcej na temat pliku JavaScript, który aktualizuje obiekt stanu urządzenia, zobacz [Omówienie zachowania modelu urządzenia](../../articles/iot-accelerators/iot-accelerators-device-simulation-advanced-device.md).

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
  "Interval": "00:00:10",
  "Scripts": {
    "Type": "javascript",
    "Path": "chiller-01-state.js"
  }
}
```

Usługa symulacji uruchamia plik **chiller-01-state.js** co pięć sekund, aby zaktualizować stan urządzenia. Pliki JavaScript dla domyślnych symulowanych urządzeń można zobaczyć w [folderze skryptów](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) w witrynie GitHub. Zgodnie z Konwencją te pliki JavaScript mają **stan** sufiksu, aby odróżnić je od plików, które implementują zachowania metody.

## <a name="properties"></a>Właściwości

`Properties`Sekcja schematu definiuje wartości właściwości, które urządzenie zgłasza do rozwiązania. Na przykład:

```json
"Properties": {
  "Type": "Elevator",
  "Location": "Building 2",
  "Latitude": 47.640792,
  "Longitude": -122.126258
}
```

Po rozpoczęciu tego rozwiązania wysyła zapytanie do wszystkich symulowanych urządzeń, aby utworzyć listę `Type` wartości do użycia w interfejsie użytkownika. Rozwiązanie używa `Latitude` `Longitude` właściwości i do dodawania lokalizacji urządzenia do mapy na pulpicie nawigacyjnym.

## <a name="telemetry"></a>Telemetria

`Telemetry`Tablica zawiera wszystkie typy telemetrii, które symulowane urządzenie wysyła do rozwiązania.

Poniższy przykład wysyła komunikat telemetrii JSON co 10 sekund przy użyciu `floor` , `vibration` i `temperature` danych z czujników Wind:

```json
"Telemetry": [
  {
    "Interval": "00:00:10",
    "MessageTemplate": "{\"floor\":${floor},\"vibration\":${vibration},\"vibration_unit\":\"${vibration_unit}\",\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\"}",
    "MessageSchema": {
      "Name": "elevator-sensors;v1",
      "Format": "JSON",
      "Fields": {
        "floor": "integer",
        "vibration": "double",
        "vibration_unit": "text",
        "temperature": "double",
        "temperature_unit": "text"
      }
    }
  }
]
```

`MessageTemplate` definiuje strukturę komunikatu JSON wysyłanego przez symulowane urządzenie. Symbole zastępcze w `MessageTemplate` użyciu składni `${NAME}` gdzie `NAME` jest kluczem z [obiektu stanu urządzenia](#simulation). Ciągi powinny być ujęte w cudzysłowy.

`MessageSchema` definiuje schemat wiadomości wysyłanej przez symulowane urządzenie. Schemat komunikatów jest również publikowany w IoT Hub, aby umożliwić aplikacjom zaplecza ponowne użycie informacji w celu interpretacji przychodzącej telemetrii.

Obecnie można używać tylko schematów wiadomości JSON. Pola wymienione w schemacie mogą być następujące:

* Serializacja obiektu za pomocą kodu JSON
* Serializacja binarna przy użyciu algorytmu Base64
* Tekst
* Boolean (wartość logiczna)
* Liczba całkowita
* Double
* DateTime

Aby wysyłać komunikaty telemetryczne w różnych interwałach, Dodaj do tablicy wiele typów telemetrii `Telemetry` . Poniższy przykład wysyła dane temperatury i wilgotności co 10 sekund i stan światła co minutę:

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
  },
  {
    "Interval": "00:01:00",
    "MessageTemplate": "{\"lights\":${lights_on}}",
    "MessageSchema": {
      "Name": "RoomLights;v1",
      "Format": "JSON",
      "Fields": {
        "lights": "boolean"
      }
    }
  }
],
```

## <a name="cloudtodevicemethods"></a>CloudToDeviceMethods

Symulowane urządzenie może odpowiadać na metody z chmury do urządzenia wywoływane z Centrum IoT Hub. `CloudToDeviceMethods`Sekcja w pliku schematu modelu urządzenia:

* Definiuje metody, na które symulowane urządzenie może reagować.
* Identyfikuje plik JavaScript zawierający logikę do wykonania.

Symulowane urządzenie wysyła listę obsługiwanych metod do centrum IoT Hub, z którym jest połączona.

Aby dowiedzieć się więcej na temat pliku JavaScript, który implementuje zachowanie urządzenia, zobacz [Omówienie zachowania modelu urządzenia](../../articles/iot-accelerators/iot-accelerators-device-simulation-advanced-device.md).

W poniższym przykładzie określono trzy obsługiwane metody i pliki JavaScript, które implementują te metody:

```json
"CloudToDeviceMethods": {
  "Reboot": {
    "Type": "javascript",
    "Path": "Reboot-method.js"
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

Pliki JavaScript dla domyślnych symulowanych urządzeń można zobaczyć w [folderze skryptów](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) w witrynie GitHub. Zgodnie z Konwencją te pliki JavaScript mają **metodę** sufiksu, aby odróżnić je od plików, które implementują zachowanie stanu.

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano sposób tworzenia własnego niestandardowego modelu urządzenia symulowanego. W tym artykule pokazano, jak:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Używanie pliku JSON do definiowania symulowanego modelu urządzenia
> * Określ właściwości symulowanego urządzenia
> * Określ dane telemetryczne wysyłane przez urządzenie symulowane
> * Określ metody z chmury do urządzenia

Teraz, gdy znasz już schemat JSON, sugerowanym następnym krokiem jest zapoznanie się z informacjami dotyczącymi sposobu [wdrażania zachowań symulowanego urządzenia](iot-accelerators-remote-monitoring-device-behavior.md).

Aby uzyskać więcej informacji programistycznych dotyczących rozwiązania do zdalnego monitorowania, zobacz:

* [Przewodnik informacyjny dla deweloperów](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Przewodnik po rozwiązywaniu problemów dla deweloperów](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)
