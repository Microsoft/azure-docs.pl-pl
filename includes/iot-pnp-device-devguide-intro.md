---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 09/24/2020
ms.openlocfilehash: 706e936590f5f47ee2989bf77b3caad67a8f28fc
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91580068"
---
Usługa IoT Plug and Play umożliwia tworzenie inteligentnych urządzeń, które anonsują swoje możliwości w aplikacjach Azure IoT. Urządzenia IoT Plug and Play nie wymagają ręcznej konfiguracji, gdy klient łączy je z aplikacjami obsługującymi Plug and Play IoT.

Urządzenie inteligentne może być implementowane bezpośrednio, korzystać z [modułów](../articles/iot-hub/iot-hub-devguide-module-twins.md)lub używać [modułów IoT Edge](../articles/iot-edge/about-iot-edge.md).

W tym przewodniku opisano podstawowe kroki wymagane do utworzenia modułu urządzenia, modułu lub IoT Edge, które są zgodne z [konwencjami Plug and Play IoT](../articles/iot-pnp/concepts-convention.md).

Aby skompilować moduł Plug and Play IoT, module lub IoT Edge, wykonaj następujące kroki:

1. Upewnij się, że urządzenie korzysta z protokołu MQTT lub MQTT over WebSockets, aby nawiązać połączenie z usługą Azure IoT Hub.
1. Utwórz model [języka Digital bliźniaczych reprezentacji Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) , aby opisać urządzenie. Aby dowiedzieć się więcej, zobacz temat [Omówienie składników w modelach Plug and Play IoT](../articles/iot-pnp/concepts-components.md).
1. Zaktualizuj urządzenie lub moduł, aby ogłosić w `model-id` ramach połączenia urządzenia.
1. Implementowanie danych telemetrycznych, właściwości i poleceń przy użyciu [konwencji Plug and Play IoT](../articles/iot-pnp/concepts-convention.md)

Gdy wdrożenie urządzenia lub modułu jest gotowe, użyj programu [Azure IoT Explorer](../articles/iot-pnp/howto-use-iot-explorer.md) , aby sprawdzić, czy urządzenie jest zgodne z konwencjami Plug and Play IoT.
