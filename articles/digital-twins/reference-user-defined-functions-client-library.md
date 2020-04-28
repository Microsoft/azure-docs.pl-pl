---
title: Funkcje zdefiniowane przez użytkownika Dokumentacja biblioteki klienta — Azure Digital bliźniaczych reprezentacji | Microsoft Docs
description: Dokumentacja biblioteki klienta funkcji zdefiniowanych przez użytkownika w usłudze Azure Digital bliźniaczych reprezentacji.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: article
ms.date: 01/17/2020
ms.custom: seodec18
ms.openlocfilehash: bd6095daca51ddca0cfb4b34ca86e763df9a3d02
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76276812"
---
# <a name="user-defined-functions-client-library-reference"></a>Dokumentacja biblioteki klienta funkcji zdefiniowanych przez użytkownika

Ten dokument zawiera informacje referencyjne dotyczące biblioteki klienta usługi Azure Digital bliźniaczych reprezentacji zdefiniowanej przez użytkownika.

## <a name="helper-methods"></a>Metody pomocnika

Biblioteka klienta definiuje metody pomocnika dla często używanych operacji.

### <a name="getspacemetadataid--space"></a>getSpaceMetadata (ID) ⇒`space`

Po otrzymaniu identyfikatora przestrzeni funkcja ta pobiera miejsce z grafu.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ---------- | ------------------- | ------------ |
| *#c1*  | `guid` | Identyfikator przestrzeni |

### <a name="getsensormetadataid--sensor"></a>getSensorMetadata (ID) ⇒`sensor`

Podanym identyfikatorem czujnika, ta funkcja pobiera czujnik z grafu.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ---------- | ------------------- | ------------ |
| *#c1*  | `guid` | Identyfikator czujnika |

### <a name="getdevicemetadataid--device"></a>getDeviceMetadata (ID) ⇒`device`

Po otrzymaniu identyfikatora urządzenia ta funkcja pobiera urządzenie z grafu.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *#c1* | `guid` | Identyfikator urządzenia |

### <a name="getsensorvaluesensorid-datatype--value"></a>getSensorValue (sensorId, dataType) ⇒`value`

Uwzględniając identyfikator czujnika i jego typ danych, ta funkcja pobiera bieżącą wartość dla tego czujnika.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *sensorId*  | `guid` | Identyfikator czujnika |
| *Typu*  | `string` | Typ danych czujnika |

### <a name="getspacevaluespaceid-valuename--value"></a>getSpaceValue (spaceId, valueName) ⇒`value`

Uwzględniając identyfikator przestrzeni i nazwę wartości, ta funkcja pobiera bieżącą wartość dla tej właściwości przestrzeni.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *spaceId*  | `guid` | Identyfikator przestrzeni |
| *valueName* | `string` | Nazwa właściwości obszaru |

### <a name="getsensorhistoryvaluessensorid-datatype--value"></a>getSensorHistoryValues (sensorId, dataType) ⇒`value[]`

Uwzględniając identyfikator czujnika i jego typ danych, ta funkcja pobiera wartości historyczne dla tego czujnika.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Identyfikator czujnika |
| *Typu* | `string` | Typ danych czujnika |

### <a name="getspacehistoryvaluesspaceid-datatype--value"></a>getSpaceHistoryValues (spaceId, dataType) ⇒`value[]`

Uwzględniając identyfikator przestrzeni i nazwę wartości, ta funkcja pobiera wartości historyczne dla tej właściwości w miejscu.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identyfikator przestrzeni |
| *valueName* | `string` | Nazwa właściwości obszaru |

### <a name="getspacechildspacesspaceid--space"></a>getSpaceChildSpaces(spaceId) ⇒`space[]`

Po otrzymaniu identyfikatora przestrzeni funkcja ta pobiera spacje podrzędne dla tego obszaru nadrzędnego.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identyfikator przestrzeni |

### <a name="getspacechildsensorsspaceid--sensor"></a>getSpaceChildSensors(spaceId) ⇒`sensor[]`

Po otrzymaniu identyfikatora przestrzeni funkcja ta pobiera czujniki podrzędne dla tego obszaru nadrzędnego.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identyfikator przestrzeni |

### <a name="getspacechilddevicesspaceid--device"></a>getSpaceChildDevices(spaceId) ⇒`device[]`

Po otrzymaniu identyfikatora przestrzeni funkcja ta pobiera urządzenia podrzędne dla tego obszaru nadrzędnego.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identyfikator przestrzeni |

### <a name="getdevicechildsensorsdeviceid--sensor"></a>getDeviceChildSensors (deviceId) ⇒`sensor[]`

Po otrzymaniu identyfikatora urządzenia ta funkcja pobiera czujniki podrzędne dla tego urządzenia nadrzędnego.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *Identyfikator* | `guid` | Identyfikator urządzenia |

### <a name="getspaceparentspacechildspaceid--space"></a>getSpaceParentSpace(childSpaceId) ⇒`space`

Po otrzymaniu identyfikatora przestrzeni funkcja ta pobiera miejsce nadrzędne.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *childSpaceId* | `guid` | Identyfikator przestrzeni |

### <a name="getsensorparentspacechildsensorid--space"></a>getSensorParentSpace(childSensorId) ⇒`space`

Przy podanym identyfikatorze czujnika ta funkcja pobiera miejsce nadrzędne.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | Identyfikator czujnika |

### <a name="getdeviceparentspacechilddeviceid--space"></a>getDeviceParentSpace(childDeviceId) ⇒`space`

Po otrzymaniu identyfikatora urządzenia ta funkcja pobiera miejsce nadrzędne.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *childDeviceId* | `guid` | Identyfikator urządzenia |

### <a name="getsensorparentdevicechildsensorid--space"></a>getSensorParentDevice(childSensorId) ⇒`space`

Podanym identyfikatorem czujnika, ta funkcja pobiera urządzenie nadrzędne.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | Identyfikator czujnika |

### <a name="getspaceextendedpropertyspaceid-propertyname--extendedproperty"></a>getSpaceExtendedProperty (spaceId, propertyName) ⇒`extendedProperty`

Po otrzymaniu identyfikatora przestrzeni funkcja ta pobiera właściwość i jej wartość z obszaru.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identyfikator przestrzeni |
| *Funkcja* | `string` | Nazwa właściwości obszaru |

### <a name="getsensorextendedpropertysensorid-propertyname--extendedproperty"></a>getSensorExtendedProperty (sensorId, propertyName) ⇒`extendedProperty`

Podanym identyfikatorem czujnika, ta funkcja pobiera właściwość i jej wartość z czujnika.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Identyfikator czujnika |
| *Funkcja* | `string` | Nazwa właściwości czujnika |

### <a name="getdeviceextendedpropertydeviceid-propertyname--extendedproperty"></a>getDeviceExtendedProperty (deviceId, propertyName) ⇒`extendedProperty`

Po otrzymaniu identyfikatora urządzenia ta funkcja pobiera właściwość i jej wartość z urządzenia.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *Identyfikator* | `guid` | Identyfikator urządzenia |
| *Funkcja* | `string` | Nazwa właściwości urządzenia |

### <a name="setsensorvaluesensorid-datatype-value"></a>setSensorValue (sensorId, typ danych, wartość)

Ta funkcja ustawia wartość dla obiektu czujnika o danym typie danych.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Identyfikator czujnika |
| *Typu*  | `string` | Typ danych czujnika |
| *value*  | `string` | Wartość |

### <a name="setspacevaluespaceid-datatype-value"></a>setSpaceValue (spaceId, typ danych, wartość)

Ta funkcja ustawia wartość w obiekcie Space o danym typie danych.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identyfikator przestrzeni |
| *Typu* | `string` | Typ danych |
| *value* | `string` | Wartość |

### <a name="logmessage"></a>Dziennik (komunikat)

Ta funkcja rejestruje następujący komunikat w ramach funkcji zdefiniowanej przez użytkownika.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *Komunikat* | `string` | Komunikat do zarejestrowania |

### <a name="sendnotificationtopologyobjectid-topologyobjecttype-payload"></a>sendNotification (topologyObjectId, topologyObjectType, ładunek)

Ta funkcja wysyła powiadomienie niestandardowe do wysłania.

**Rodzaj**: funkcja globalna

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *topologyObjectId*  | `guid` | Identyfikator obiektu grafu. Przykłady to spacja, czujnik i identyfikator urządzenia.|
| *topologyObjectType*  | `string` | Przykłady to czujnik i urządzenie.|
| *payload*  | `string` | Ładunek JSON do wysłania z powiadomieniem. |

## <a name="return-types"></a>Typy zwracane

Modele odpowiedzi zwrócone z metod pomocnika odwołań klienta zostały opisane poniżej.

### <a name="space"></a>Miejsce

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "Space",
  "friendlyName": "Conference Room",
  "typeId": 0,
  "parentSpaceId": "00000000-0000-0000-0000-000000000001",
  "subtypeId": 0
}
```

### <a name="space-methods"></a>Metody spacji

#### <a name="parent--space"></a>Element nadrzędny () ⇒`space`

Ta funkcja zwraca przestrzeń nadrzędną bieżącego obszaru.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒`sensor[]`

Ta funkcja zwraca czujniki podrzędne bieżącego obszaru.

#### <a name="childdevices--device"></a>ChildDevices() ⇒`device[]`

Ta funkcja zwraca urządzenia podrzędne bieżącego obszaru.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>Właściwość rozszerzona (propertyName) ⇒`extendedProperty`

Ta funkcja zwraca właściwość rozszerzoną i jej wartość dla bieżącego obszaru.

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *Funkcja* | `string` | Nazwa właściwości rozszerzonej |

#### <a name="valuevaluename--value"></a>Wartość (valueName) ⇒`value`

Ta funkcja zwraca wartość bieżącego obszaru.

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | Nazwa wartości |

#### <a name="historyvaluename--value"></a>Historia (valueName) ⇒`value[]`

Ta funkcja zwraca wartości historyczne bieżącego obszaru.

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | Nazwa wartości |

#### <a name="notifypayload"></a>Powiadom (ładunek)

Ta funkcja wysyła powiadomienie z określonym ładunkiem.

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | Ładunek JSON do uwzględnienia w powiadomieniu |

### <a name="device"></a>Urządzenie

```JSON
{
  "id": "00000000-0000-0000-0000-000000000002",
  "name": "Device",
  "friendlyName": "Temperature Sensing Device",
  "description": "This device contains a sensor that captures temperature readings.",
  "type": "None",
  "subtype": "None",
  "typeId": 0,
  "subtypeId": 0,
  "hardwareId": "ABC123",
  "gatewayId": "ABC",
  "spaceId": "00000000-0000-0000-0000-000000000000"
}
```

### <a name="device-methods"></a>Metody urządzenia

#### <a name="parent--space"></a>Element nadrzędny () ⇒`space`

Ta funkcja zwraca przestrzeń nadrzędną bieżącego urządzenia.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒`sensor[]`

Ta funkcja zwraca czujniki podrzędne bieżącego urządzenia.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>Właściwość rozszerzona (propertyName) ⇒`extendedProperty`

Ta funkcja zwraca właściwość rozszerzoną i jej wartość dla bieżącego urządzenia.

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *Funkcja* | `string` | Nazwa właściwości rozszerzonej |

#### <a name="notifypayload"></a>Powiadom (ładunek)

Ta funkcja wysyła powiadomienie z określonym ładunkiem.

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | Ładunek JSON do uwzględnienia w powiadomieniu |

### <a name="sensor"></a>Czujnik

```JSON
{
  "id": "00000000-0000-0000-0000-000000000003",
  "port": "30",
  "pollRate": 3600,
  "dataType": "Temperature",
  "dataSubtype": "None",
  "type": "Classic",
  "portType": "None",
  "dataUnitType": "FahrenheitTemperature",
  "spaceId": "00000000-0000-0000-0000-000000000000",
  "deviceId": "00000000-0000-0000-0000-000000000001",
  "portTypeId": 0,
  "dataUnitTypeId": 0,
  "dataTypeId": 0,
  "dataSubtypeId": 0,
  "typeId": 0  
}
```

### <a name="sensor-methods"></a>Metody czujników

#### <a name="space--space"></a>Space () ⇒`space`

Ta funkcja zwraca przestrzeń nadrzędną bieżącego czujnika.

#### <a name="device--device"></a>Urządzenie () ⇒`device`

Ta funkcja zwraca urządzenie nadrzędne bieżącego czujnika.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>Właściwość rozszerzona (propertyName) ⇒`extendedProperty`

Ta funkcja zwraca właściwość rozszerzoną i jej wartość dla bieżącego czujnika.

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *Funkcja* | `string` | Nazwa właściwości rozszerzonej |

#### <a name="value--value"></a>Wartość () ⇒`value`

Ta funkcja zwraca wartość bieżącego czujnika.

#### <a name="history--value"></a>Historia () ⇒`value[]`

Ta funkcja zwraca wartości historyczne bieżącego czujnika.

#### <a name="notifypayload"></a>Powiadom (ładunek)

Ta funkcja wysyła powiadomienie z określonym ładunkiem.

| Parametr  | Typ                | Opis  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | Ładunek JSON do uwzględnienia w powiadomieniu |

### <a name="value"></a>Wartość

```JSON
{
  "dataType": "Temperature",
  "value": "70",
  "createdTime": ""
}
```

### <a name="extended-property"></a>Właściwość rozszerzona

```JSON
{
  "name": "OccupancyStatus",
  "value": "Occupied"
}
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [funkcjach zdefiniowanych przez użytkownika w usłudze Azure Digital bliźniaczych reprezentacji](./concepts-user-defined-functions.md).

- Dowiedz się [, jak tworzyć funkcje zdefiniowane przez użytkownika](./how-to-user-defined-functions.md).

- Dowiedz się [, jak debugować funkcje zdefiniowane przez użytkownika](./how-to-diagnose-user-defined-functions.md).
