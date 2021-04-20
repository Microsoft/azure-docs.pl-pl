---
title: Jak zarządzać usługą IoT Plug and Play digital twins
description: Jak zarządzać urządzeniem IoT Plug and Play przy użyciu interfejsów API bliźniaczych reprezentacji cyfrowych
author: prashmo
ms.author: prashmo
ms.date: 12/17/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: e68003878dc0e9275461100a59e0f45486c2978f
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739876"
---
# <a name="manage-iot-plug-and-play-digital-twins"></a>Zarządzanie IoT Plug and Play bliźniaczych reprezentacji

IoT Plug and Play obsługuje operacje **Get digital twin i** Update digital **twins** w celu zarządzania cyfrowymi bliźniaczych reprezentacji. Możesz użyć interfejsów [API REST](/rest/api/iothub/service/digitaltwin) lub jednego z [zestawów SDK usługi](libraries-sdks.md).

W momencie pisania tego tekstu wersja cyfrowego interfejsu API bliźniaczej reprezentacji to `2020-09-30` .

## <a name="update-a-digital-twin"></a>Aktualizowanie bliźniaczej reprezentacji

Urządzenie IoT Plug and Play implementuje model opisany przez język [Digital Twins Definition Language v2 (DTDL).](https://github.com/Azure/opendigitaltwins-dtdl) Deweloperzy rozwiązań mogą używać **interfejsu API aktualizacji bliźniaczej** reprezentacji usługi Digital Twin, aby zaktualizować stan składnika i właściwości bliźniaczej reprezentacji cyfrowej.

Urządzenie IoT Plug and Play używane jako przykład w tym artykule implementuje [model kontrolera](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) temperatury ze [składnikami termostatu.](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)

Poniższy fragment kodu przedstawia odpowiedź na żądanie Get **digital twins** sformatowane jako obiekt JSON. Aby dowiedzieć się więcej na temat formatu bliźniaczych reprezentacji cyfrowych, zobacz [Understand IoT Plug and Play digital twins](./concepts-digital-twin.md#digital-twin-example):

```json
{
    "$dtId": "sample-device",
    "serialNumber": "alwinexlepaho8329",
    "thermostat1": {
        "maxTempSinceLastReboot": 25.3,
        "targetTemperature": 20.4,
        "$metadata": {
            "targetTemperature": {
                "desiredValue": 20.4,
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

Usługi Digital Twins umożliwiają aktualizowanie całego składnika lub właściwości przy użyciu poprawki [JSON](http://jsonpatch.com/).

Na przykład możesz zaktualizować właściwość `targetTemperature` w następujący sposób:

```json
[
    {
        "op": "add",
        "path": "/thermostat1/targetTemperature",
        "value": 21.4
    }
]
```

Poprzednia aktualizacja ustawia żądaną wartość właściwości na odpowiednim poziomie składnika, `$metadata` jak pokazano w poniższym fragmencie kodu. IoT Hub aktualizuje żądaną wersję właściwości:

```json
"thermostat1": {
    "targetTemperature": 20.4,
    "$metadata": {
        "targetTemperature": {
            "desiredValue": 21.4,
            "desiredVersion": 5,
            "ackVersion": 4,
            "ackCode": 200,
            "ackDescription": "Successfully executed patch",
            "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
        }
    }
}
```

### <a name="add-replace-or-remove-a-component"></a>Dodawanie, zastępowanie lub usuwanie składnika

Operacje na poziomie składnika wymagają pustego `$metadata` znacznika obiektu w ramach wartości.

Operacja dodawania lub zamieniania składnika ustawia żądane wartości wszystkich podanych właściwości. Ponadto czyszczy żądane wartości dla wszystkich właściwości zapisywalnych, które nie zostały dostarczone wraz z aktualizacją.

Usunięcie składnika usuwa żądane wartości wszystkich obecnych właściwości zapisywalnych. Urządzenie ostatecznie synchronizuje to usunięcie i przestaje zgłaszać poszczególne właściwości. Składnik jest następnie usuwany z bliźniaczej reprezentacji cyfrowej.

Poniższy przykład poprawki JSON pokazuje, jak dodać, zastąpić lub usunąć składnik:

```json
[
    {
        "op": "add",
        "path": "/thermostat1",
        "value": {
            "targetTemperature": 21.4,
            "anotherWritableProperty": 42,
            "$metadata": {}
        }
    },
    {
        "op": "replace",
        "path": "/thermostat1",
        "value": {
            "targetTemperature": 21.4,
            "$metadata": {}
        }
    },
    {
        "op": "remove",
        "path": "/thermostat2"
    }
]
```

### <a name="add-replace-or-remove-a-property"></a>Dodawanie, zastępowanie lub usuwanie właściwości

Operacja dodawania lub zamieniania ustawia żądaną wartość właściwości. Urządzenie może synchronizować stan i zgłaszać aktualizację wartości wraz z `ack` kodem, wersją i opisem.

Usunięcie właściwości usuwa żądaną wartość właściwości, jeśli jest ustawiona. Następnie urządzenie może zatrzymać raportowanie tej właściwości i zostanie usunięte ze składnika. Jeśli ta właściwość jest ostatnią właściwością w składniku, składnik również jest usuwany.

Poniższy przykład poprawki JSON pokazuje, jak dodać, zastąpić lub usunąć właściwość w składniku:

```json
[
    {
        "op": "add",
        "path": "/thermostat1/targetTemperature",
        "value": 21.4
    },
    {
        "op": "replace",
        "path": "/thermostat1/anotherWritableProperty",
        "value": 42
    },
    {
        "op": "remove",
        "path": "/thermostat2/targetTemperature",
    }
]
```

### <a name="rules-for-setting-the-desired-value-of-a-digital-twin-property"></a>Reguły ustawiania żądanej wartości właściwości bliźniaczej reprezentacji cyfrowej

**Nazwa**

Nazwa składnika lub właściwości musi być prawidłową nazwą DTDL v2.

Dozwolone znaki to a–z, A–Z, 0–9 (nie jako pierwszy znak) i podkreślenie (nie jako pierwszy ani ostatni znak).

Nazwa może mieć od 1 do 64 znaków.

**Wartość właściwości**

Wartość musi być prawidłową [właściwością DTDL v2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#property).

Obsługiwane są wszystkie typy pierwotne. W ramach typów złożonych obsługiwane są wylinia, mapy i obiekty. Aby dowiedzieć się więcej, zobacz [DTDL v2 Schemas (Schematy języka DTDL w wersji 2).](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#schemas)

Właściwości nie obsługują tablicy ani żadnego złożonego schematu z tablicą.

Maksymalna głębokość pięciu poziomów jest obsługiwana dla obiektu złożonego.

Wszystkie nazwy pól w obiekcie złożonym powinny być prawidłowymi nazwami DTDL w wersji 2.

Wszystkie klucze mapy powinny być prawidłowymi nazwami DTDL v2.

## <a name="troubleshoot-update-digital-twin-api-errors"></a>Rozwiązywanie problemów z błędami aktualizowania interfejsu API bliźniaczej reprezentacji bliźniaczej

Interfejs API bliźniaczej reprezentacji cyfrowej zgłasza następujący ogólny komunikat o błędzie:

`ErrorCode:ArgumentInvalid;'{propertyName}' exists within the device twin and is not digital twin conformant property. Please refer to aka.ms/dtpatch to update this to be conformant.`

Jeśli widzisz ten błąd, upewnij się, że poprawka aktualizacji jest zgodna z regułami ustawiania żądanej wartości [właściwości bliźniaczej reprezentacji cyfrowej](#rules-for-setting-the-desired-value-of-a-digital-twin-property)

Podczas aktualizowania składnika upewnij się, że ustawiono [pusty $metadata znacznika.](#add-replace-or-remove-a-component)

Aktualizacje mogą się nie powieść, jeśli zgłoszone wartości urządzenia nie są zgodne z konwencjami [IoT Plug and Play.](./concepts-convention.md#writable-properties)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz o bliźniaczych reprezentacjiach cyfrowych, oto kilka dodatkowych zasobów:

- [Interakcja z urządzeniem z rozwiązania](quickstart-service.md)
- [IoT Digital Twin REST API](/rest/api/iothub/service/digitaltwin)
- [Eksplorator usługi Azure IoT](howto-use-iot-explorer.md)
