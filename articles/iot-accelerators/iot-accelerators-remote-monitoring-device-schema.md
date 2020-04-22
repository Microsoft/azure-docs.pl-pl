---
title: Schemat urządzenia w rozwiązaniu do zdalnego monitorowania — Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano schemat JSON, który definiuje symulowane urządzenie w rozwiązaniu zdalnego monitorowania.
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
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683742"
---
# <a name="understand-the-device-model-schema"></a>Opis schematu modelu urządzenia

Można użyć symulowanych urządzeń w rozwiązaniu zdalnego monitorowania, aby przetestować jego zachowanie. Rozwiązanie do zdalnego monitorowania obejmuje usługę symulacji urządzeń do uruchamiania symulowanych urządzeń. Podczas wdrażania rozwiązania zdalnego monitorowania kolekcja symulowanych urządzeń jest aprowizowana automatycznie. Istniejące symulowane urządzenia można dostosować lub utworzyć własne.

W tym artykule opisano schemat modelu urządzenia, który określa możliwości i zachowanie symulowanego urządzenia. Model urządzenia jest przechowywany w pliku JSON.

> [!NOTE]
> Ten schemat modelu urządzenia jest przeznaczony tylko dla symulowanych urządzeń hostowanych w usłudze symulacji urządzeń. Jeśli chcesz utworzyć prawdziwe urządzenie, zobacz [Łączenie urządzenia z akceleratorem rozwiązań do zdalnego monitorowania](iot-accelerators-connecting-devices.md).

Następujące artykuły są związane z bieżącym artykułem:

* [Zaimplementuj zachowanie modelu urządzenia](iot-accelerators-remote-monitoring-device-behavior.md) opisuje pliki JavaScript używane do implementacji zachowania symulowanego urządzenia.
* [Tworzenie nowego symulowanego urządzenia](iot-accelerators-remote-monitoring-create-simulated-device.md) łączy je ze sobą i pokazuje, jak wdrożyć nowy typ symulowanego urządzenia w rozwiązaniu.

W tym artykule omówiono sposób wykonywania następujących zadań:

>[!div class="checklist"]
> * Definiowanie symulowanego modelu urządzenia za pomocą pliku JSON
> * Określanie właściwości symulowanego urządzenia
> * Określanie danych telemetrycznych wysyłanych przez symulowane urządzenie
> * Określ metody chmury do urządzenia, na które urządzenie reaguje

## <a name="the-parts-of-the-device-model-schema"></a>Części schematu modelu urządzenia

Każdy model urządzenia, taki jak agregat chłodniczy lub ciężarówka, definiuje typ urządzenia, które usługa symulacji może symulować. Każdy model urządzenia jest przechowywany w pliku JSON z następującym schematem najwyższego poziomu:

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

Można wyświetlić pliki schematu dla domyślnych symulowanych urządzeń w [folderze devicemodels](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels) w usłudze GitHub.

W poniższej tabeli opisano wpisy schematu najwyższego poziomu:

| Wpis schematu | Opis |
| -- | --- |
| `SchemaVersion` | Wersja schematu jest `1.0.0` zawsze i jest specyficzna dla formatu tego pliku. |
| `Id` | Unikatowy identyfikator dla tego modelu urządzenia. |
| `Version` | Identyfikuje wersję modelu urządzenia. |
| `Name` | Przyjazna nazwa modelu urządzenia. |
| `Description` | Opis modelu urządzenia. |
| `Protocol` | Protokół połączenia używany przez urządzenie. Może być `AMQP`jednym `MQTT`z `HTTP`, i . |

W poniższych sekcjach opisano inne sekcje w schemacie JSON:

## <a name="simulation"></a>Symulacja

W `Simulation` sekcji można zdefiniować stan wewnętrzny symulowanego urządzenia. Wszystkie wartości telemetryczne wysyłane przez urządzenie muszą być częścią tego stanu urządzenia.

Definicja stanu urządzenia ma dwa elementy:

* `InitialState`definiuje wartości początkowe dla wszystkich właściwości obiektu stanu urządzenia.
* `Script`identyfikuje plik JavaScript, który działa zgodnie z harmonogramem, aby zaktualizować stan urządzenia. Za pomocą tego pliku skryptu można losowo losować wartości telemetryczne wysyłane przez urządzenie.

Aby dowiedzieć się więcej o pliku JavaScript, który aktualizuje obiekt stanu urządzenia, zobacz [Opis zachowania modelu urządzenia](../../articles/iot-accelerators/iot-accelerators-device-simulation-advanced-device.md).

W poniższym przykładzie przedstawiono definicję obiektu stanu urządzenia dla symulowanego urządzenia agregatu chłodniczego:

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

Usługa symulacji uruchamia plik **chiller-01-state.js** co pięć sekund, aby zaktualizować stan urządzenia. Pliki JavaScript dla domyślnych symulowanych urządzeń można zobaczyć w [folderze skryptów](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) w usłudze GitHub. Zgodnie z konwencją te pliki JavaScript mają sufiks **-state,** aby odróżnić je od plików, które implementują zachowania metody.

## <a name="properties"></a>Właściwości

Sekcja `Properties` schematu definiuje wartości właściwości, które urządzenie zgłasza do rozwiązania. Przykład:

```json
"Properties": {
  "Type": "Elevator",
  "Location": "Building 2",
  "Latitude": 47.640792,
  "Longitude": -122.126258
}
```

Po uruchomieniu rozwiązania, wysyła zapytanie do wszystkich symulowanych `Type` urządzeń, aby utworzyć listę wartości do użycia w interfejsie użytkownika. Rozwiązanie używa `Latitude` właściwości `Longitude` i, aby dodać lokalizację urządzenia do mapy na pulpicie nawigacyjnym.

## <a name="telemetry"></a>Telemetria

Tablica `Telemetry` zawiera listę wszystkich typów danych telemetrycznych wysyłanych do rozwiązania przez symulowane urządzenie.

Poniższy przykład wysyła komunikat telemetrii JSON `floor` `vibration`co `temperature` 10 sekund z programem i dane z czujników windy:

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

`MessageTemplate`definiuje strukturę wiadomości JSON wysyłanej przez symulowane urządzenie. Symbole zastępcze `MessageTemplate` używają `${NAME}` składni, `NAME` w której jest kluczem z [obiektu stanu urządzenia](#simulation). Ciągi powinny być cytowane, liczby nie powinny.

`MessageSchema`definiuje schemat wiadomości wysyłanej przez symulowane urządzenie. Schemat wiadomości jest również publikowany w centrum IoT Hub, aby umożliwić aplikacjom wewnętrznej bazy danych ponowne użycie informacji do interpretacji danych wejściowych.

Obecnie można używać tylko schematów wiadomości JSON. Pola wymienione w schemacie mogą być następujące typy:

* Obiekt — serializowany przy użyciu języka JSON
* Binarny - serializowany przy użyciu base64
* Tekst
* Wartość logiczna
* Liczba całkowita
* Double
* DateTime

Aby wysyłać komunikaty telemetryczne w różnych odstępach czasu, dodaj wiele typów danych `Telemetry` telemetrycznych do tablicy. Poniższy przykład wysyła dane temperatury i wilgotności co 10 sekund i stan światła co minutę:

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

## <a name="cloudtodevicemethods"></a>Metody CloudToDeviceMethods

Symulowane urządzenie może reagować na metody chmury do urządzenia wywoływane z centrum IoT hub. Sekcja `CloudToDeviceMethods` w pliku schematu modelu urządzenia:

* Definiuje metody, na które symulowane urządzenie może reagować.
* Identyfikuje plik JavaScript, który zawiera logikę do wykonania.

Symulowane urządzenie wysyła listę metod, które obsługuje do centrum IoT, z który jest połączony.

Aby dowiedzieć się więcej o pliku JavaScript, który implementuje zachowanie urządzenia, zobacz [Opis zachowania modelu urządzenia](../../articles/iot-accelerators/iot-accelerators-device-simulation-advanced-device.md).

Poniższy przykład określa trzy obsługiwane metody i pliki JavaScript, które implementują te metody:

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

Pliki JavaScript dla domyślnych symulowanych urządzeń można zobaczyć w [folderze skryptów](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) w usłudze GitHub. Zgodnie z konwencją te pliki JavaScript mają sufiks **-metoda,** aby odróżnić je od plików, które implementują zachowanie stanu.

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano sposób tworzenia własnego niestandardowego symulowanego modelu urządzenia. W tym artykule pokazano, jak:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Definiowanie symulowanego modelu urządzenia za pomocą pliku JSON
> * Określanie właściwości symulowanego urządzenia
> * Określanie danych telemetrycznych wysyłanych przez symulowane urządzenie
> * Określ metody chmury do urządzenia, na które urządzenie reaguje

Teraz, gdy dowiedziałeś się o schemacie JSON, sugerowanym następnym krokiem jest nauczenie się [implementacji zachowania symulowanego urządzenia.](iot-accelerators-remote-monitoring-device-behavior.md)

Aby uzyskać więcej informacji dewelopera na temat rozwiązania do zdalnego monitorowania, zobacz:

* [Przewodnik informacyjny dla deweloperów](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Przewodnik po rozwiązywaniu problemów dla deweloperów](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)
