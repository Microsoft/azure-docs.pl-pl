---
title: Przewodnik dla deweloperów urządzeń (C) — IoT Plug and Play | Microsoft Docs
description: Opis Plug and Play IoT dla deweloperów urządzeń C
author: rido-min
ms.author: rmpablos
ms.date: 11/19/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
zone_pivot_groups: programming-languages-set-twenty-six
ms.openlocfilehash: 0cca47269e632e1fcba1f8f9eb1c835f27e63059
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582841"
---
# <a name="iot-plug-and-play-device-developer-guide"></a>Przewodnik dla deweloperów urządzeń Plug and Play IoT

Usługa IoT Plug and Play umożliwia tworzenie inteligentnych urządzeń, które anonsują swoje możliwości w aplikacjach Azure IoT. Urządzenia IoT Plug and Play nie wymagają ręcznej konfiguracji, gdy klient łączy je z aplikacjami obsługującymi Plug and Play IoT.

Urządzenie inteligentne może być implementowane bezpośrednio, korzystać z [modułów](../iot-hub/iot-hub-devguide-module-twins.md)lub używać [modułów IoT Edge](../iot-edge/about-iot-edge.md).

W tym przewodniku opisano podstawowe kroki wymagane do utworzenia modułu urządzenia, modułu lub IoT Edge, które są zgodne z [konwencjami Plug and Play IoT](../iot-pnp/concepts-convention.md).

Aby skompilować moduł Plug and Play IoT, module lub IoT Edge, wykonaj następujące kroki:

1. Upewnij się, że urządzenie korzysta z protokołu MQTT lub MQTT over WebSockets, aby nawiązać połączenie z usługą Azure IoT Hub.
1. Utwórz model [języka Digital bliźniaczych reprezentacji Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) , aby opisać urządzenie. Aby dowiedzieć się więcej, zobacz temat [Omówienie składników w modelach Plug and Play IoT](concepts-modeling-guide.md).
1. Zaktualizuj urządzenie lub moduł, aby ogłosić w `model-id` ramach połączenia urządzenia.
1. Implementowanie danych telemetrycznych, właściwości i poleceń przy użyciu [konwencji Plug and Play IoT](concepts-convention.md)

Gdy wdrożenie urządzenia lub modułu jest gotowe, użyj programu [Azure IoT Explorer](howto-use-iot-explorer.md) , aby sprawdzić, czy urządzenie jest zgodne z konwencjami Plug and Play IoT.

:::zone pivot="programming-language-ansi-c"

[!INCLUDE [iot-pnp-device-devguide-c](../../includes/iot-pnp-device-devguide-c.md)]

:::zone-end

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-pnp-device-devguide-csharp](../../includes/iot-pnp-device-devguide-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-pnp-device-devguide-java](../../includes/iot-pnp-device-devguide-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-pnp-device-devguide-node](../../includes/iot-pnp-device-devguide-node.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-pnp-device-devguide-python](../../includes/iot-pnp-device-devguide-python.md)]

:::zone-end

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak często opracowano urządzenia Plug and Play IoT, Oto kilka dodatkowych zasobów:

- [Język definicji cyfrowych reprezentacji bliźniaczych (DTLD, Digital Twins Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C (zestaw SDK urządzenia)](/azure/iot-hub/iot-c-sdk-ref/)
- [Interfejs API REST usługi IoT](/rest/api/iothub/device)
- [Informacje o składnikach modeli Plug and Play IoT](concepts-modeling-guide.md)
- [Instalowanie narzędzi autorskich DTDL i korzystanie z nich](howto-use-dtdl-authoring-tools.md)
- [Przewodnik dewelopera usługi IoT Plug and Play](concepts-developer-guide-service.md)