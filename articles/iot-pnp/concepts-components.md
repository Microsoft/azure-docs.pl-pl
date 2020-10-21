---
title: Informacje o składnikach modeli Plug and Play IoT | Microsoft Docs
description: Informacje o różnicach między modelami DTDL IoT Plug and Play, które korzystają ze składników i modeli, które nie korzystają ze składników programu.
author: ericmitt
ms.author: ericmitt
ms.date: 07/07/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 7d33811dc056baa3d205d7d86c1bdd7bff74ae54
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92330006"
---
# <a name="iot-plug-and-play-components-in-models"></a>Składniki technologii IoT Plug and Play w modelach

W konwencjach technologii IoT Plug and Play urządzenie jest urządzeniem IoT Plug and Play, jeśli prezentuje swój identyfikator modelu języka DTDL (digital twins definition language) podczas nawiązywania połączenia z usługą IoT Hub.

Poniższy fragment kodu przedstawia przykładowe identyfikatory modeli:

```json
 "@id": "dtmi:com:example:TemperatureController;1"
 "@id": "dtmi:com:example:Thermostat;1",
```

## <a name="no-components"></a>Brak składników

Prosty model nie używa składników osadzonych ani kaskadowych. Zawiera informacje nagłówka i sekcji zawartości w celu zdefiniowania danych telemetrycznych, właściwości i poleceń.

Poniższy przykład przedstawia część prostego modelu, który nie używa składników:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
...
```

Mimo że model nie definiuje jawnie składnika, działa tak, jakby istnieje pojedynczy _składnik domyślny_, ze wszystkimi danymi telemetrycznymi, właściwościami i poleceniami.

Poniższy zrzut ekranu przedstawia sposób wyświetlania modelu w narzędziu Azure IoT Explorer:

:::image type="content" source="media/concepts-components/default-component.png" alt-text="Składnik domyślny w programie Azure IoT Explorer":::

Identyfikator modelu jest przechowywany we właściwości przędzy urządzenia, jak pokazano na poniższym zrzucie ekranu:

:::image type="content" source="media/concepts-components/twin-model-id.png" alt-text="Składnik domyślny w programie Azure IoT Explorer":::

Model DTDL bez składników to przydatne uproszczenie dla urządzeń lub IoT Edge modułu z pojedynczym zestawem danych telemetrycznych, właściwościami i poleceniami. Model, który nie używa składników, ułatwia Migrowanie istniejącego urządzenia lub modułu jako urządzenia Plug and Play IoT lub modułu — tworzysz model DTDL, który opisuje rzeczywiste urządzenie lub moduł bez konieczności definiowania składników.

> [!TIP]
> Moduł może [być modułem urządzenia lub](../iot-hub/iot-hub-devguide-module-twins.md) [modułem IoT Edge](../iot-edge/about-iot-edge.md).

## <a name="multiple-components"></a>Wiele składników

Składniki umożliwiają skompilowanie interfejsu modelu jako zestawu innych interfejsów.

Na przykład interfejs [termostatu](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) jest zdefiniowany jako model. Ten interfejs można dołączyć jako co najmniej jeden składnik podczas definiowania [modelu kontrolera temperatury](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json). W poniższym przykładzie te składniki są wywoływane `thermostat1` i `thermostat2` .

W przypadku modelu DTDL z wieloma składnikami istnieją co najmniej dwie sekcje składników. Każda sekcja ma `@type` ustawioną wartość `Component` i jawnie odwołuje się do schematu, jak pokazano w poniższym fragmencie kodu:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "description": "Device with two thermostats and remote reboot.",
  "contents": [
...
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1",
      "displayName": "Thermostat One",
      "description": "Thermostat One of Two."
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat2",
      "displayName": "Thermostat Two",
      "description": "Thermostat Two of Two."
    },
    {
      "@type": "Component",
      "schema": "dtmi:azure:DeviceManagement:DeviceInformation;1",
      "name": "deviceInformation",
      "displayName": "Device Information interface",
      "description": "Optional interface with basic device hardware information."
    }
...
```

Ten model ma trzy składniki zdefiniowane w sekcji zawartości — dwa `Thermostat` składniki i `DeviceInformation` składnik. Istnieje również składnik domyślny.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz o składnikach modelu, Oto kilka dodatkowych zasobów:

- [Instalowanie narzędzi autorskich DTDL i korzystanie z nich](howto-use-dtdl-authoring-tools.md)
- [Digital bliźniaczych reprezentacji Definition Language v2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [Repozytoria modelu](./concepts-model-repository.md)
