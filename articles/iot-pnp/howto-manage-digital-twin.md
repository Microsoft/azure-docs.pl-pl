---
title: Jak zarządzać usługą IoT Plug and Play Digital bliźniaczych reprezentacji
description: Jak zarządzać urządzeniem Plug and Play IoT za pomocą interfejsów API cyfrowych przędzy
author: prashmo
ms.author: prashmo
ms.date: 07/20/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5f1c52b764634f8086763aca67dfc32b507d2edd
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92042851"
---
# <a name="manage-iot-plug-and-play-digital-twins"></a>Zarządzanie usługą IoT Plug and Play Digital bliźniaczych reprezentacji

Plug and Play IoT obsługuje **szybkie** i **Aktualizowanie** wieloosiowych operacji na potrzeby zarządzania cyfrowymi bliźniaczych reprezentacji. Można użyć [interfejsów API REST](/rest/api/iothub/service/digitaltwin) lub jednego z [zestawów SDK usługi](libraries-sdks.md).

W momencie pisania wersja interfejsu API Digital przędzy to `2020-09-30` .

## <a name="update-a-digital-twin"></a>Aktualizowanie wieloosiowej cyfrowej

Urządzenie Plug and Play IoT implementuje model opisany przez [Digital bliźniaczych reprezentacji Definition Language v2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl). Deweloperzy rozwiązań mogą korzystać z **interfejsu API Digital przędzy** , aby zaktualizować stan składnika i właściwości wielocyfrowej sznurka.

Urządzenie Plug and Play IoT używane jako przykład w tym artykule implementuje [model kontrolera temperatury](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) ze składnikami [termostatu](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) .

Poniższy fragment kodu przedstawia odpowiedź na żądanie **uzyskania cyfrowej sieci dwuosiowej** sformatowaną jako obiekt JSON. Aby dowiedzieć się więcej o formacie cyfrowej przędzy, zobacz [Opis IoT Plug and Play Digital bliźniaczych reprezentacji](./concepts-digital-twin.md#digital-twin-json-format):

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

Digital bliźniaczych reprezentacji umożliwia zaktualizowanie całego składnika lub właściwości przy użyciu [poprawki JSON](http://jsonpatch.com/).

Na przykład można zaktualizować `targetTemperature` Właściwość w następujący sposób:

```json
[
    {
        "op": "add",
        "path": "/thermostat1/targetTemperature",
        "value": 21.4
    }
]
```

Poprzednia aktualizacja ustawia żądaną wartość właściwości na poziomie odpowiedniego składnika `$metadata` , jak pokazano w poniższym fragmencie kodu. IoT Hub aktualizuje żądaną wersję właściwości:

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

Operacje na poziomie składnika wymagają pustego `$metadata` znacznika obiektu w wartości.

Operacja Dodaj lub Zamień składnik ustawia wymagane wartości wszystkich podanych właściwości. Czyści również żądane wartości dla wszystkich zapisywalnych właściwości, które nie są dostarczane z aktualizacją.

Usunięcie składnika czyści wymagane wartości wszystkich właściwości, które mają możliwość zapisu. Urządzenie ostatecznie zsynchronizuje to usunięcie i zakończy raportowanie poszczególnych właściwości. Składnik jest następnie usuwany z dwuosiowej.

Poniższy przykład poprawek JSON pokazuje, jak dodać, zastąpić lub usunąć składnik:

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

Usunięcie właściwości czyści żądaną wartość właściwości, jeśli jest ustawiona. Następnie urządzenie może zatrzymać raportowanie tej właściwości i zostanie usunięte ze składnika. Jeśli ta właściwość jest ostatnią z nich w składniku, składnik zostanie również usunięty.

Poniższy przykład poprawek JSON pokazuje, jak dodać, zastąpić lub usunąć właściwość w składniku:

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

### <a name="rules-for-setting-the-desired-value-of-a-digital-twin-property"></a>Reguły dotyczące ustawiania żądanej wartości właściwości dwuosiowej

**Nazwa**

Nazwa składnika lub właściwości musi być prawidłową nazwą DTDL v2.

Dozwolone znaki to a-z, A-Z, 0-9 (nie jako pierwszy znak) i podkreślenia (nie jako pierwszy lub ostatni znak).

Nazwa może składać się z od 1-64 znaków.

**Wartość właściwości**

Wartość musi być prawidłową [właściwością DTDL v2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#property).

Obsługiwane są wszystkie typy pierwotne. W przypadku typów złożonych, typy wyliczeniowe, mapy i obiekty są obsługiwane. Aby dowiedzieć się więcej, zobacz [schematy DTDL v2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#schemas).

Właściwości nie obsługują tablicy ani żadnego złożonego schematu z tablicą.

Dla obiektu złożonego jest obsługiwana Maksymalna głębokość pięciu poziomów.

Wszystkie nazwy pól w ramach obiektu złożonego powinny być Prawidłowymi nazwami DTDL v2.

Wszystkie klucze mapowania powinny być Prawidłowymi nazwami DTDL v2.

## <a name="troubleshoot-update-digital-twin-api-errors"></a>Rozwiązywanie problemów z aktualizacją interfejsów API cyfrowych przędzy

Interfejs API Digital bliźniaczy zgłasza następujący ogólny komunikat o błędzie:

`ErrorCode:ArgumentInvalid;'{propertyName}' exists within the device twin and is not digital twin conformant property. Please refer to aka.ms/dtpatch to update this to be conformant.`

Jeśli widzisz ten błąd, upewnij się, że poprawka aktualizacji jest zgodna z [regułami dotyczącymi ustawiania żądanej wartości właściwości dwuosiowej](#rules-for-setting-the-desired-value-of-a-digital-twin-property)

Podczas aktualizowania składnika upewnij się, że jest ustawiony [znacznik $Metadata pustego obiektu](#add-replace-or-remove-a-component) .

Aktualizacje mogą zakończyć się niepowodzeniem, jeśli raportowane wartości urządzenia nie są zgodne z [konwencjami Plug and Play IoT](./concepts-convention.md#writable-properties).

## <a name="next-steps"></a>Następne kroki

Teraz, kiedy już wiesz o programie Digital bliźniaczych reprezentacji, Oto kilka dodatkowych zasobów:

- [Interakcja z urządzeniem z rozwiązania](quickstart-service-node.md)
- [Interfejs API REST cyfrowego przędzy IoT](/rest/api/iothub/service/digitaltwin)
- [Eksplorator IoT Azure](howto-use-iot-explorer.md)