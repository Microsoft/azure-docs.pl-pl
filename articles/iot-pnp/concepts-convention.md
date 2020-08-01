---
title: Konwencje Plug and Play IoT | Microsoft Docs
description: Opis Konwencji IoT Plug and Play oczekuje, że urządzenia będą używane podczas wysyłania danych telemetrycznych i właściwości oraz obsługi poleceń i aktualizacji właściwości.
author: rido-min
ms.author: rmpablos
ms.date: 07/10/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 126673391b49f884a51521d462060c425a314667
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475672"
---
# <a name="iot-plug-and-play-conventions"></a>Konwencje technologii IoT Plug and Play

Urządzenia usługi IoT Plug and Play w wersji zapoznawczej muszą przestrzegać zestawu konwencji podczas wymiany komunikatów z Centrum IoT. Urządzenia w wersji zapoznawczej IoT Plug and Play używają protokołu MQTT do komunikacji z IoT Hub.

Opisz dane telemetryczne, właściwości i polecenia, które urządzenie Plug and Play IoT implementuje przy użyciu _modelu_ [Digital bliźniaczych reprezentacji Definition Language v2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) . W tym artykule istnieją dwa typy modeli, do których odwołuje się następujący:

- **Brak składnika** -modelu bez składników. Model deklaruje dane telemetryczne, właściwości i polecenia jako właściwości najwyższego poziomu w sekcji zawartość interfejsu głównego.
- **Wiele składników** — model składający się z co najmniej dwóch interfejsów. Główny interfejs z danymi telemetrycznymi, właściwościami i poleceniami. Jeden lub więcej interfejsów zadeklarowanych jako składniki z dodatkową telemetrią, właściwościami i poleceniami.

Aby uzyskać więcej informacji, zobacz [składniki Plug and Play IoT w modelach](concepts-components.md).

## <a name="identify-the-model"></a>Identyfikowanie modelu

Aby zaanonsować model, który implementuje, urządzenie usługi IoT Plug and Play obejmuje Identyfikator modelu w pakiecie połączenia MQTT przez dodanie `model-id` do `USERNAME` pola.

Aby zidentyfikować model, który implementuje urządzenie, usługa może pobrać identyfikator modelu z:

- Pole "sznurka urządzenia" `modelId` .
- Pole cyfrowej przędzy `$metadata.$model` .
- Powiadomienie o zmianie dwuosiowej zmiany.

## <a name="telemetry"></a>Telemetry

Dane telemetryczne wysyłane z żadnego urządzenia do składników nie wymagają żadnych dodatkowych metadanych. System dodaje `dt-dataschema` Właściwość.

Dane telemetryczne wysyłane z urządzenia z wieloma składnikami muszą dodać `$.sub` jako właściwość wiadomości. System dodaje `dt-subject` `dt-dataschema` właściwości i.

## <a name="read-only-properties"></a>Właściwości tylko do odczytu

### <a name="sample-no-component-read-only-property"></a>Przykład braku składnika — właściwość tylko do odczytu

Urządzenie może wysłać dowolny prawidłowy kod JSON zgodny z regułami DTDL v2.

:::row:::
   :::column span="":::
      **Specyfikacja DTDL**

      ```json
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
      ```
   :::column-end:::
   :::column span="":::
      **Przykładowy ładunek**

      ```json
      "reported" :
      {
        "temperature" : 21.3
      }
      ```
   :::column-end:::
:::row-end:::

### <a name="sample-multiple-components-read-only-property"></a>Przykładowa właściwość z wieloma składnikami tylko do odczytu

Urządzenie musi dodać `{"__t": "c"}` znacznik, aby wskazać, że element odwołuje się do składnika.

:::row:::
   :::column span="":::
      **Specyfikacja DTDL**

      ```json
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
      ```
   :::column-end:::
   :::column span="":::
      **Raportowana Właściwość**

      ```json
      "reported": {
        "thermostat1": {
          "__t": "c",
          "temperature": 21.3
        }
      }
      ```
   :::column-end:::
:::row-end:::

## <a name="writable-properties"></a>Właściwości do zapisu

Urządzenie powinno potwierdzić, że otrzymał Właściwość przez wysłanie raportowanej właściwości. Raportowana właściwość powinna obejmować:

- `value`— wartość odebrana przez urządzenie.
- `ac`-kod potwierdzający, który używa kodu stanu HTTP.
- `av`-wersja potwierdzenia odwołująca się do `$version` żądanej właściwości.
- `ad`— opcjonalny opis potwierdzenia.

### <a name="sample-no-component-writable-property"></a>Przykład brakujący właściwości składnika do zapisu

Urządzenie może wysłać dowolny prawidłowy kod JSON zgodny z regułami DTDL v2:

:::row:::
   :::column span="":::
      **Specyfikacja DTDL**

      ```json
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
      ```
   :::column-end:::
   :::column span="":::
      **Żądana Właściwość**

      ```json
      "desired" :
      {
        "targetTemperature" : 21.3
      },
      "$version" : 3
      ```
   :::column-end:::
   :::column span="":::
      **Raportowana Właściwość**

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
   :::column-end:::
:::row-end:::

### <a name="sample-multiple-components-writable-property"></a>Przykładowa Właściwość wielu składników do zapisu

Urządzenie musi dodać `{"__t": "c"}` znacznik, aby wskazać, że element odwołuje się do składnika.

Znacznik jest wysyłany tylko dla aktualizacji na poziomie składnika, więc urządzenia nie możeą tę flagę.

Urządzenie powinno potwierdzić, że otrzymał Właściwość przez wysłanie raportowanej właściwości:

:::row:::
   :::column span="":::
      **Specyfikacja DTDL**

      ```json
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
      ```
   :::column-end:::
   :::column span="":::
      **Żądana Właściwość**

      ```json
      "desired": {
        "thermostat1": {
          "__t": "c",
          "targetTemperature": 21.3
        }
      },
      "$version" : 3
      ```
   :::column-end:::
   :::column span="":::
      **Raportowana Właściwość**

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
   :::column-end:::
:::row-end:::

## <a name="commands"></a>Polecenia

Żadne interfejsy składników nie używają nazwy polecenia bez prefiksu.

Na urządzeniu wiele interfejsów składników używa nazw poleceń w następującym formacie: `componentName*commandName` .

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz już konwencje Plug and Play IoT, Oto kilka dodatkowych zasobów:

- [Digital bliźniaczych reprezentacji Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C (zestaw SDK urządzenia)](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [Interfejs API REST usługi IoT](https://docs.microsoft.com/rest/api/iothub/device)
- [Składniki modelu](./concepts-components.md)
