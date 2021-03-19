---
title: Konwencje Plug and Play IoT | Microsoft Docs
description: Opis Konwencji IoT Plug and Play oczekuje, że urządzenia będą używane podczas wysyłania danych telemetrycznych i właściwości oraz obsługi poleceń i aktualizacji właściwości.
author: rido-min
ms.author: rmpablos
ms.date: 07/10/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: a8dfb3cacf798559273361ec75cab4570a8a5228
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582684"
---
# <a name="iot-plug-and-play-conventions"></a>Konwencje technologii IoT Plug and Play

Urządzenia Plug and Play IoT powinny być zgodne z zestawem konwencji podczas wymiany komunikatów z Centrum IoT. Urządzenia Plug and Play IoT używają protokołu MQTT do komunikowania się z IoT Hub.

Urządzenia mogą zawierać [moduły](../iot-hub/iot-hub-devguide-module-twins.md)lub być zaimplementowane w [module IoT Edge](../iot-edge/about-iot-edge.md) hostowanym przez środowisko uruchomieniowe IoT Edge.

Opisz dane telemetryczne, właściwości i polecenia, które urządzenie Plug and Play IoT implementuje przy użyciu _modelu_ [Digital bliźniaczych reprezentacji Definition Language v2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) . W tym artykule istnieją dwa typy modeli, do których odwołuje się następujący:

- **Brak składnika** -modelu bez składników. Model deklaruje dane telemetryczne, właściwości i polecenia jako właściwości najwyższego poziomu w sekcji zawartość interfejsu głównego. W narzędziu Azure IoT Explorer ten model jest wyświetlany jako pojedynczy _składnik domyślny_.
- **Wiele składników** — model składający się z co najmniej dwóch interfejsów. Główny interfejs, który jest wyświetlany jako _składnik domyślny_, przy użyciu telemetrii, właściwości i poleceń. Jeden lub więcej interfejsów zadeklarowanych jako składniki z dodatkową telemetrią, właściwościami i poleceniami.

Aby uzyskać więcej informacji, zobacz artykuł [IoT Plug and Play Modeling Guide](concepts-modeling-guide.md).

## <a name="identify-the-model"></a>Identyfikowanie modelu

Aby zaanonsować model, który implementuje, urządzenie Plug and Play IoT lub moduł zawiera identyfikator modelu w pakiecie połączenia MQTT przez dodanie `model-id` do `USERNAME` pola.

Aby zidentyfikować model, który implementuje urządzenie lub moduł, usługa może pobrać identyfikator modelu z:

- Pole "sznurka urządzenia" `modelId` .
- Pole cyfrowej przędzy `$metadata.$model` .
- Powiadomienie o zmianie dwuosiowej zmiany.

## <a name="telemetry"></a>Telemetria

Dane telemetryczne wysyłane z żadnego urządzenia do składników nie wymagają żadnych dodatkowych metadanych. System dodaje `dt-dataschema` Właściwość.

Dane telemetryczne wysyłane z urządzenia z wieloma składnikami muszą dodać `$.sub` jako właściwość wiadomości. System dodaje `dt-subject` `dt-dataschema` właściwości i.

## <a name="read-only-properties"></a>Właściwości tylko do odczytu

### <a name="sample-no-component-read-only-property"></a>Przykład braku składnika — właściwość tylko do odczytu

Urządzenie lub moduł może wysłać dowolny prawidłowy kod JSON zgodny z regułami DTDL v2.

DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:example: Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "temperature",
      "schema": "double"
    }
  ]
}
```

Przykładowy raportowany ładunek właściwości:

```json
"reported" :
{
  "temperature" : 21.3
}
```

### <a name="sample-multiple-components-read-only-property"></a>Przykładowa właściwość z wieloma składnikami tylko do odczytu

Urządzenie lub moduł musi dodać znacznik, `{"__t": "c"}` Aby wskazać, że element odwołuje się do składnika.

DTDL, który odwołuje się do składnika:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "contents": [
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1"
    }
  ]
}
```

DTDL, który definiuje składnik:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "temperature",
      "schema": "double"
    }
  ]
}
```

Przykładowy raportowany ładunek właściwości:

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "temperature": 21.3
  }
}
```

## <a name="writable-properties"></a>Właściwości do zapisu

Urządzenie lub moduł powinno potwierdzić, że otrzymał Właściwość przez wysłanie raportowanej właściwości. Raportowana właściwość powinna obejmować:

- `value` — rzeczywista wartość właściwości (zazwyczaj odebrana wartość, ale urządzenie może zdecydować o zgłoszeniu innej wartości).
- `ac` -kod potwierdzający, który używa kodu stanu HTTP.
- `av` -wersja potwierdzenia odwołująca się do `$version` żądanej właściwości. Tę wartość można znaleźć w żądanym ładunku JSON właściwości.
- `ad` — opcjonalny opis potwierdzenia.

Po uruchomieniu urządzenia powinno ono zażądać sznurka urządzenia i sprawdzić, czy są możliwe do zapisu aktualizacje właściwości. Jeśli wersja właściwości zapisywalnej zwiększyła się, gdy urządzenie jest w trybie offline, urządzenie powinno wysłać zgłoszoną odpowiedź na właściwość w celu potwierdzenia, że została odebrana aktualizacja.

Gdy urządzenie jest uruchamiane po raz pierwszy, może wysłać wartość początkową zgłoszonej właściwości, jeśli nie otrzyma początkowej żądanej właściwości z centrum. W takim przypadku urządzenie powinno ustawić `av` na `1` . Na przykład:

```json
"reported": {
  "targetTemperature": {
    "value": 20.0,
    "ac": 200,
    "av": 1,
    "ad": "initialize"
  }
}
```

Urządzenie może korzystać z raportowanej właściwości, aby zapewnić innym informacjom centrum. Na przykład urządzenie może odpowiedzieć z serią komunikatów w toku, takich jak:

```json
"reported": {
  "targetTemperature": {
    "value": 35.0,
    "ac": 202,
    "av": 3,
    "ad": "In-progress - reporting current temperature"
  }
}
```

Gdy urządzenie osiągnie docelową temperaturę, wysyła następujący komunikat:

```json
"reported": {
  "targetTemperature": {
    "value": 20.0,
    "ac": 200,
    "av": 3,
    "ad": "Reached target temperature"
  }
}
```

Urządzenie może zgłosić błąd, na przykład:

```json
"reported": {
  "targetTemperature": {
    "value": 120.0,
    "ac": 500,
    "av": 3,
    "ad": "Target temperature out of range. Valid range is 10 to 99."
  }
}
```

### <a name="sample-no-component-writable-property"></a>Przykład brakujący właściwości składnika do zapisu

Gdy urządzenie odbiera wiele raportowanych właściwości w jednym ładunku, może wysyłać raportowane odpowiedzi na właściwości w wielu ładunku.

Urządzenie lub moduł może wysłać dowolny prawidłowy kod JSON zgodny z regułami DTDL v2:

DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:example: Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "targetTemperature",
      "schema": "double",
      "writable": true
    }
  ]
}
```

Przykładowy ładunek żądanej właściwości:

```json
"desired" :
{
  "targetTemperature" : 21.3,
  "targetHumidity" : 80
},
"$version" : 3
```

Przykładowy raportowany pierwszy ładunek właściwości:

```json
"reported": {
  "targetTemperature": {
    "value": 21.3,
    "ac": 200,
    "av": 3,
    "ad": "complete"
  }
}
```

Przykładowy raportowany drugi ładunek właściwości:

```json
"reported": {
  "targetHumidity": {
    "value": 80,
    "ac": 200,
    "av": 3,
    "ad": "complete"
  }
}
```

### <a name="sample-multiple-components-writable-property"></a>Przykładowa Właściwość wielu składników do zapisu

Urządzenie lub moduł musi dodać znacznik, `{"__t": "c"}` Aby wskazać, że element odwołuje się do składnika.

Znacznik jest wysyłany tylko dla aktualizacji właściwości zdefiniowanych w składniku. Aktualizacje właściwości zdefiniowanych w składniku domyślnym nie obejmują znacznika, zobacz [przykładowe właściwości bez składnika do zapisu](#sample-no-component-writable-property)

Gdy urządzenie odbiera wiele raportowanych właściwości w jednym ładunku, może wysyłać raportowane odpowiedzi na właściwości w wielu ładunku.

Urządzenie lub moduł powinno potwierdzić, że otrzymał właściwości, wysyłając zgłoszone właściwości:

DTDL, który odwołuje się do składnika:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "contents": [
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1"
    }
  ]
}
```

DTDL, który definiuje składnik:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "targetTemperature",
      "schema": "double",
      "writable": true
    }
  ]
}
```

Przykładowy ładunek żądanej właściwości:

```json
"desired": {
  "thermostat1": {
    "__t": "c",
    "targetTemperature": 21.3,
    "targetHumidity": 80
  }
},
"$version" : 3
```

Przykładowy raportowany pierwszy ładunek właściwości:

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "targetTemperature": {
      "value": 23,
      "ac": 200,
      "av": 3,
      "ad": "complete"
    }
  }
}
```

Przykładowy raportowany drugi ładunek właściwości:

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "targetHumidity": {
      "value": 80,
      "ac": 200,
      "av": 3,
      "ad": "complete"
    }
  }
}
```

## <a name="commands"></a>Polecenia

Żadne interfejsy składników nie używają nazwy polecenia bez prefiksu.

W urządzeniu lub module wiele interfejsów składników używa nazw poleceń w następującym formacie: `componentName*commandName` .

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz już konwencje Plug and Play IoT, Oto kilka dodatkowych zasobów:

- [Język definicji cyfrowych reprezentacji bliźniaczych (DTLD, Digital Twins Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C (zestaw SDK urządzenia)](/azure/iot-hub/iot-c-sdk-ref/)
- [Interfejs API REST usługi IoT](/rest/api/iothub/device)
- [Przewodnik modelowania Plug and Play IoT](concepts-modeling-guide.md)