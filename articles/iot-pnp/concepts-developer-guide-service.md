---
title: Przewodnik dla deweloperów usług — IoT Plug and Play | Microsoft Docs
description: Opis Plug and Play IoT dla deweloperów usług
author: dominicbetts
ms.author: dobett
ms.date: 10/01/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: a5889be88dfd0870a2eee868c97787ff354cff68
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104582735"
---
# <a name="iot-plug-and-play-service-developer-guide"></a>Przewodnik dewelopera usługi IoT Plug and Play

Usługa IoT Plug and Play umożliwia tworzenie inteligentnych urządzeń, które anonsują swoje możliwości w aplikacjach Azure IoT. Urządzenia IoT Plug and Play nie wymagają ręcznej konfiguracji, gdy klient łączy je z aplikacjami obsługującymi Plug and Play IoT.

Plug and Play IoT umożliwia korzystanie z urządzeń, które ogłosiły swój identyfikator modelu w usłudze IoT Hub. Można na przykład uzyskać dostęp do właściwości i poleceń urządzenia bezpośrednio.

Aby korzystać z urządzenia Plug and Play IoT, które jest połączone z Centrum IoT, jeden z zestawów SDK usługi IoT:

## <a name="service-sdks"></a>Zestawy SDK usług

Użyj zestawów SDK usługi Azure IoT w rozwiązaniu, aby móc korzystać z urządzeń i modułów. Można na przykład użyć zestawów SDK usługi do odczytu i aktualizacji właściwości przędzy i poleceń Invoke. Obsługiwane języki to C#, Java, Node.js i Python.

Zestawy SDK usług umożliwiają dostęp do informacji o urządzeniach, takich jak aplikacje klasyczne lub sieci Web. Zestawy SDK usługi obejmują dwie przestrzenie nazw i modele obiektów, których można użyć do pobrania identyfikatora modelu:

- Klient usługi IoT Hub. Ta usługa ujawnia Identyfikator modelu jako właściwość przędzy urządzenia.

- Klient Digital bliźniaczych reprezentacji. Nowy interfejs API Digital bliźniaczych reprezentacji działa na konstrukcjach modelu [Digital bliźniaczych reprezentacji Definition Language (DTDL)](concepts-digital-twin.md) , takich jak składniki, właściwości i polecenia. Interfejsy API cyfrowego przędzy ułatwiają konstruktorom rozwiązań tworzenie rozwiązań Plug and Play IoT.

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-pnp-service-devguide-csharp](../../includes/iot-pnp-service-devguide-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-pnp-service-devguide-java](../../includes/iot-pnp-service-devguide-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-pnp-service-devguide-node](../../includes/iot-pnp-service-devguide-node.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-pnp-service-devguide-python](../../includes/iot-pnp-service-devguide-python.md)]

:::zone-end

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak modelowanie urządzeń, Oto kilka dodatkowych zasobów:

- [Język definicji cyfrowych reprezentacji bliźniaczych (DTLD, Digital Twins Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C (zestaw SDK urządzenia)](/azure/iot-hub/iot-c-sdk-ref/)
- [Interfejs API REST usługi IoT](/rest/api/iothub/device)
- [Przewodnik modelowania Plug and Play IoT](concepts-modeling-guide.md)
- [Instalowanie narzędzi autorskich DTDL i korzystanie z nich](howto-use-dtdl-authoring-tools.md)