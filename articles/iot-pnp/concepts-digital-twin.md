---
title: Interpretacja bliźniaczej reprezentacji urządzenia IoT Plug and Play
description: Zrozumienie, jak Plug and Play IoT używa cyfrowego bliźniaczych reprezentacji
author: prashmo
ms.author: prashmo
ms.date: 07/17/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: f13230c7bd88a9c3cf043fc1881a34f6b7ce6fe7
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95495325"
---
# <a name="understand-iot-plug-and-play-digital-twins"></a>Interpretacja bliźniaczej reprezentacji urządzenia IoT Plug and Play

Urządzenie Plug and Play IoT implementuje model opisany przez schemat [Digital bliźniaczych reprezentacji Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) . Model opisuje zestaw składników, właściwości, poleceń i komunikatów telemetrycznych, które może mieć określone urządzenie. Za pierwszym razem, gdy urządzenie IoT Plug and Play nawiązuje połączenie z Centrum IoT.

Plug and Play IoT używa programu DTDL w wersji 2. Aby uzyskać więcej informacji na temat tej wersji, zobacz Specyfikacja [Digital bliźniaczych reprezentacji Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) w witrynie GitHub.

DTDL nie ma na wyłączność Plug and Play IoT. Inne usługi IoT, takie jak [Azure Digital bliźniaczych reprezentacji](../digital-twins/overview.md), służą do reprezentowania całych środowisk, takich jak budynki i sieci energetyczne. Aby dowiedzieć się więcej, zobacz [Omówienie modeli bliźniaczych w usłudze Azure Digital bliźniaczych reprezentacji](../digital-twins/concepts-models.md).

W tym artykule opisano, jak składniki i właściwości są reprezentowane w *odpowiednich* i *raportowanych* sekcjach sznurka urządzenia. Opisano w nim również, w jaki sposób te koncepcje są mapowane do odpowiedniej bliźniaczej reprezentacji urządzenia.

Urządzenie typu Plug and Play w tym artykule implementujące [model kontrolera temperatury](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) za pomocą składnika [termostatu](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) .

## <a name="device-twins-and-digital-twins"></a>Bliźniaczych reprezentacji urządzeń i Digital bliźniaczych reprezentacji

Bliźniaczych reprezentacji urządzeń to dokumenty JSON, które przechowują informacje o stanie urządzenia, w tym metadane, konfiguracje i warunki. Aby dowiedzieć się więcej, zobacz temat [Omówienie i używanie urządzenia bliźniaczych reprezentacji w IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md). Zarówno konstruktory urządzeń, jak i rozwiązania mogą nadal korzystać z tego samego zestawu interfejsów API i zestawów SDK urządzeń, aby zaimplementować urządzenia i rozwiązania przy użyciu konwencji Plug and Play IoT.

Interfejsy API Digital bliźniaczy działają na konstrukcjach wysokiego poziomu w języku Digital bliźniaczych reprezentacji Definition Language (DTDL), takich jak składniki, właściwości i polecenia. Interfejsy API cyfrowego przędzy ułatwiają konstruktorom rozwiązań tworzenie rozwiązań Plug and Play IoT.

W przypadku sznurka urządzenia stan właściwości z możliwością zapisu jest podzielony na odpowiednie i raportowane sekcje. Wszystkie właściwości tylko do odczytu są dostępne w sekcji zgłoszone.

W formie dwucyfrowej dwuosiowej istnieje ujednolicony widok bieżącego i żądanego stanu właściwości. Stan synchronizacji danej właściwości jest przechowywany w odpowiedniej sekcji składnika domyślnego `$metadata` .

### <a name="digital-twin-json-format"></a>Format JSON cyfrowego przędzy

Gdy jest reprezentowany jako obiekt JSON, dwuosiowa cyfra obejmuje następujące pola:

| Nazwa pola | Opis |
| --- | --- |
| `$dtId` | Ciąg dostarczony przez użytkownika reprezentujący identyfikator wieloosiowego urządzenia |
| `{propertyName}` | Wartość właściwości w formacie JSON |
| `$metadata.$model` | Obowiązkowe Identyfikator interfejsu modelu, który charakteryzuje ten dwucyfrowego przędzy |
| `$metadata.{propertyName}.desiredValue` | [Tylko do zapisu właściwości] Wymagana wartość określonej właściwości |
| `$metadata.{propertyName}.desiredVersion` | [Tylko do zapisu właściwości] Wersja żądanej wartości utrzymywanej przez IoT Hub|
| `$metadata.{propertyName}.ackVersion` | [Wymagane tylko dla właściwości z możliwością zapisu] Wersja potwierdzona przez urządzenie implementujące dwuosiową cyfrę, musi być większa lub równa żądanej wersji |
| `$metadata.{propertyName}.ackCode` | [Wymagane tylko dla właściwości z możliwością zapisu] `ack` Kod zwrócony przez aplikację urządzenia implementującą dwuosiową cyfrę |
| `$metadata.{propertyName}.ackDescription` | [Opcjonalne, tylko dla właściwości z możliwością zapisu] `ack` Opis zwrócony przez aplikację urządzenia implementującą dwuosiową cyfrę |
| `$metadata.{propertyName}.lastUpdateTime` | IoT Hub utrzymuje sygnaturę czasową ostatniej aktualizacji właściwości przez urządzenie. Sygnatury czasowe są zakodowane w formacie UTC, a ISO8601 format RRRR-MM-DDTgg: MM: SS. mmmZ |
| `{componentName}` | Obiekt JSON zawierający wartości właściwości i metadanych składnika. |
| `{componentName}.{propertyName}` | Wartość właściwości składnika w formacie JSON |
| `{componentName}.$metadata` | Informacje o metadanych składnika. |

#### <a name="device-twin-sample"></a>Przykładowa dwuosiowa urządzenia

Poniższy fragment kodu przedstawia dwuosiowe urządzenie Plug and Play IoT sformatowane jako obiekt JSON:

```json
{
    "deviceId": "sample-device",
    "modelId": "dtmi:com:example:TemperatureController;1",
    "version": 15,
    "properties": {
        "desired": {
            "thermostat1": {
                "__t": "c",
                "targetTemperature": 21.8
            },
            "$metadata": {...},
            "$version": 4
        },
        "reported": {
            "serialNumber": "alwinexlepaho8329",
            "thermostat1": {
                "maxTempSinceLastReboot": 25.3,
                "__t": "c",
                "targetTemperature": {
                    "value": 21.8,
                    "ac": 200,
                    "ad": "Successfully executed patch",
                }
            },
            "$metadata": {...},
            "$version": 11
        }
    }
}
```

#### <a name="digital-twin-sample"></a>Digital bliźniaczy — przykład

Poniższy fragment kodu przedstawia cyfrowe sznurki sformatowane jako obiekt JSON:

```json
{
    "$dtId": "sample-device",
    "serialNumber": "alwinexlepaho8329",
    "thermostat1": {
        "maxTempSinceLastReboot": 25.3,
        "targetTemperature": 21.8,
        "$metadata": {
            "targetTemperature": {
                "desiredValue": 21.8,
                "desiredVersion": 4,
                "ackVersion": 4,
                "ackCode": 200,
                "ackDescription": "Successfully executed patch",
                "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
            },
            "maxTempSinceLastReboot": {
                "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
            }
        }
    },
    "$metadata": {
        "$model": "dtmi:com:example:TemperatureController;1",
        "serialNumber": {
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

### <a name="properties"></a>Właściwości

Właściwości to pola danych, które reprezentują stan jednostki (na przykład właściwości w wielu językach programowania zorientowanego obiektowo).

#### <a name="read-only-property"></a>Właściwość tylko do odczytu

Schematy

```json
{
    "@type": "Property",
    "name": "serialNumber",
    "displayName": "Serial Number",
    "description": "Serial number of the device.",
    "schema": "string"
}
```

W tym przykładzie `alwinexlepaho8329` jest bieżącą wartością `serialNumber` właściwości tylko do odczytu raportowaną przez urządzenie.
Poniższe fragmenty kodu pokazują reprezentację w formacie JSON obok siebie `serialNumber` Właściwości:

:::row:::
   :::column span="":::
      **Bliźniak urządzenia**

```json
"properties": {
    "reported": {
        "serialNumber": "alwinexlepaho8329"
    }
}
```

   :::column-end:::
   :::column span="":::
      **Cyfrowe sznurki**

```json
"serialNumber": "alwinexlepaho8329"
```

   :::column-end:::
:::row-end:::

#### <a name="writable-property"></a>Modyfikowalna Właściwość

Załóżmy, że urządzenie miało również następującą modyfikowalną właściwość w składniku domyślnym:

```json
{
    "@type": "Property",
    "name": "fanSpeed",
    "displayName": "Fan Speed",
    "writable": true,
    "schema": "double"
}
```

:::row:::
   :::column span="":::
      **Bliźniak urządzenia**

```json
{
    "properties": {
        "desired": {
            "fanSpeed": 2.0,
        },
        "reported": {
            "fanSpeed": {
                "value": 3.0,
                "ac": 200,
                "av": 1,
                "ad": "Successfully executed patch version 1"
            }
        }
    },
}
```

   :::column-end:::
   :::column span="":::
      **Cyfrowe sznurki**

```json
{
    "fanSpeed": 3.0,
    "$metadata": {
        "fanSpeed": {
            "desiredValue": 2.0,
            "desiredVersion": 2,
            "ackVersion": 1,
            "ackCode": 200,
            "ackDescription": "Successfully executed patch version 1",
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

   :::column-end:::
:::row-end:::

W tym przykładzie `3.0` jest bieżącą wartością `fanSpeed` Właściwości raportowanej przez urządzenie. `2.0` jest pożądaną wartością ustawioną przez rozwiązanie. Wymagana wartość i stan synchronizacji właściwości na poziomie głównym są ustawiane na poziomie głównym `$metadata` dla dwuosiowej sieci. Gdy urządzenie przejdzie w tryb online, może zastosować tę aktualizację i zgłosić zaktualizowaną wartość.

### <a name="components"></a>Składniki

Składniki umożliwiają kompilowanie interfejsu modelu jako zestawu innych interfejsów.
Rozważmy Interfejs [termostatu](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) , który jest zdefiniowany jako model.
Ten interfejs można teraz dołączyć jako składnik thermostat1 (i inny składnik thermostat2) podczas definiowania [modelu kontrolera temperatury](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json).

W przypadku urządzeń z oznaczeniem składnik jest identyfikowany przez `{ "__t": "c"}` znacznik. W formie dwuosiowej, obecność `$metadata` oznacza składnik.

W tym przykładzie `thermostat1` jest składnikiem z dwiema właściwościami:

- `maxTempSinceLastReboot` jest właściwością tylko do odczytu.
- `targetTemperature` jest modyfikowalną właściwością, która została pomyślnie zsynchronizowana przez urządzenie. Wymagana wartość i stan synchronizacji tych właściwości znajdują się w składniku `$metadata` .

Poniższe fragmenty kodu pokazują reprezentację typu Side-by-Side `thermostat1` składnika:

:::row:::
   :::column span="":::
      **Bliźniak urządzenia**

```json
"properties": {
    "desired": {
        "thermostat1": {
            "__t": "c",
            "targetTemperature": 21.8
        },
        "$metadata": {
        },
        "$version": 4
    },
    "reported": {
        "thermostat1": {
            "maxTempSinceLastReboot": 25.3,
            "__t": "c",
            "targetTemperature": {
                "value": 21.8,
                "ac": 200,
                "ad": "Successfully executed patch",
                "av": 4
            }
        },
        "$metadata": {
        },
        "$version": 11
    }
}
```

   :::column-end:::
   :::column span="":::
      **Cyfrowe sznurki**

```json
"thermostat1": {
    "maxTempSinceLastReboot": 25.3,
    "targetTemperature": 21.8,
    "$metadata": {
        "targetTemperature": {
            "desiredValue": 21.8,
            "desiredVersion": 4,
            "ackVersion": 4,
            "ackCode": 200,
            "ackDescription": "Successfully executed patch",
            "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
        },
        "maxTempSinceLastReboot": {
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

   :::column-end:::
:::row-end:::

## <a name="digital-twin-apis"></a>Interfejsy API Digital bliźniaczy

Usługa Azure Digital bliźniaczych reprezentacji jest wyposażona w program umożliwiający **Uzyskiwanie dwuosiowych**, **Aktualizowanie dwuosiowych**, **Wywoływanie polecenia składnika** i **Wywoływanie polecenia** służącego do zarządzania wieloosiowymi urządzeniami Możesz użyć [interfejsów API REST](/rest/api/iothub/service/digitaltwin) bezpośrednio lub za pomocą [zestawu SDK usługi](../iot-pnp/libraries-sdks.md).

## <a name="digital-twin-change-events"></a>Zdarzenia zmiany cyfrowej reprezentacji bliźniaczej

W przypadku włączenia zdarzeń zmiany reprezentacji bliźniaczej zdarzenie jest wyzwalane za każdym razem, gdy bieżąca lub żądana wartość składnika lub właściwości zostanie zmieniona. Zdarzenia zmiany cyfrowej przędzy są generowane w formacie [poprawek JSON](http://jsonpatch.com/) . Odpowiednie zdarzenia są generowane w formacie przędzy urządzenia, jeśli są włączone zdarzenia zmiany bliźniaczych.

Aby dowiedzieć się, jak włączyć routing dla zdarzeń cyfrowych i wieloosiowych, zobacz [używanie IoT Hub Routing komunikatów do wysyłania komunikatów z urządzenia do chmury do różnych punktów końcowych](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events). Aby zrozumieć format wiadomości, zobacz [Tworzenie i odczytywanie wiadomości IoT Hub](../iot-hub/iot-hub-devguide-messages-construct.md).

Na przykład następujące zdarzenie zmiany cyfrowej przędzy jest wyzwalane, gdy `targetTemperature` jest ustawiony przez rozwiązanie:

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/17/2020 6:11:04 AM
iothub-message-source:digitalTwinChangeEvents
correlation-id:275d463fa034
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature",
    "value": {
      "desiredValue": 21.8,
      "desiredVersion": 4
    }
  }
]
```

Następujące cyfrowe zdarzenie zmiany przędzy jest wyzwalane, gdy urządzenie zgłosi, że Poprzednia zmiana została zastosowana:

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/17/2020 6:11:05 AM
iothub-message-source:digitalTwinChangeEvents
correlation-id:275d464a2c80
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/ackCode",
    "value": 200
  },
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/ackDescription",
    "value": "Successfully executed patch"
  },
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/ackVersion",
    "value": 4
  },
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/lastUpdateTime",
    "value": "2020-07-17T06:11:04.9309159Z"
  },
  {
    "op": "add",
    "path": "/thermostat1/targetTemperature",
    "value": 21.8
  }
]
```

> [!NOTE]
> Komunikaty powiadomień o zmianach przędzy są podwójnie włączone w przypadku powiadomienia o zmianach urządzenia i cyfrowych sznurów.

## <a name="next-steps"></a>Następne kroki

Teraz, kiedy już wiesz o programie Digital bliźniaczych reprezentacji, Oto kilka dodatkowych zasobów:

- [Jak używać interfejsów API cyfrowych bliźniaczych Plug and Play IoT](howto-manage-digital-twin.md)
- [Interakcja z urządzeniem z rozwiązania](quickstart-service.md)
- [Interfejs API REST cyfrowego przędzy IoT](/rest/api/iothub/service/digitaltwin)
- [Eksplorator IoT Azure](howto-use-iot-explorer.md)