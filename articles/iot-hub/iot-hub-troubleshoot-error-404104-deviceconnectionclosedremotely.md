---
title: Rozwiązywanie problemów z błędem usługi Azure IoT Hub 404104 DeviceConnectionClosedOneRemotely
description: Dowiedz się, jak naprawić błąd 404104 DeviceConnectionClosedNajmnie
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: mqtt
ms.openlocfilehash: c8cb91aa0c7ce1610320d4107db282d3c34407ba
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758725"
---
# <a name="404104-deviceconnectionclosedremotely"></a>404104 DeviceConnectionClosedRemotely

W tym artykule opisano przyczyny i rozwiązania dla **404104 DeviceConnectionClosedRemotely** błędów.

## <a name="symptoms"></a>Objawy

### <a name="symptom-1"></a>Objaw 1

Urządzenia rozłączają się w regularnych odstępach czasu (co 65 minut, na przykład) i widzisz **404104 DeviceConnectionClosedReotely** w dziennikach diagnostycznych Usługi IoT Hub. Czasami zobaczysz również **401003 IoTHubNieautoryzowane** i pomyślne zdarzenie połączenia urządzenia mniej niż minutę później.

### <a name="symptom-2"></a>Objaw 2

Urządzenia rozłączają się losowo, a w dziennikach diagnostycznych usługi IoT Hub **404104 jest widoczny** sposób.

### <a name="symptom-3"></a>Objaw 3

Wiele urządzeń odłącza się naraz, widać spadek w [metryki podłączonych urządzeń](iot-hub-metrics.md), i jest więcej **404104 DeviceConnectionClosedReotely** i [500xxx Błędy wewnętrzne](iot-hub-troubleshoot-error-500xxx-internal-errors.md) w dziennikach diagnostycznych niż zwykle.

## <a name="causes"></a>Przyczyny

### <a name="cause-1"></a>Przyczyna 1

[Token sygnatury dostępu Współdzielonego używany do łączenia się z centrum IoT Hub](iot-hub-devguide-security.md#security-tokens) wygasł, co powoduje, że usługa IoT Hub rozłącza urządzenie. Połączenie zostanie ponownie nawiązane, gdy token jest odświeżany przez urządzenie. Na przykład [token sygnatury dostępu Współdzielonego domyślnie wygasa co godzinę dla SDK C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-authentication), co może prowadzić do regularnych rozłączenia.

Aby dowiedzieć się więcej, zobacz [401003 IoTHubNieużużużona przyczyna](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#cause-1).

### <a name="cause-2"></a>Przyczyna 2

Niektóre możliwości obejmują:

- Urządzenie utraciło podstawową łączność sieciową dłużej niż [funkcja utrzymywana przy życiu mqtt,](iot-hub-mqtt-support.md#default-keep-alive-timeout)co spowodowało zdalny limit czasu bezczynności. Ustawienie MQTT keep-alive może być różne na urządzenie.

- Urządzenie wysłało reset na poziomie TCP/IP, ale nie `MQTT DISCONNECT`wysłało poziomu aplikacji. Zasadniczo urządzenie nagle zamknęło połączenie z gniazdem bazowym. Czasami ten problem jest spowodowany przez błędy w starszych wersjach zestawu SDK usługi Azure IoT.

- Aplikacja po stronie urządzenia uległa awarii.

### <a name="cause-3"></a>Przyczyna 3

Centrum IoT może wystąpić przejściowy problem. Zobacz [Przyczyna błędu serwera wewnętrznego usługi IoT Hub](iot-hub-troubleshoot-error-500xxx-internal-errors.md#cause).

## <a name="solutions"></a>Rozwiązania

### <a name="solution-1"></a>Rozwiązanie 1

Zobacz [401003 Rozwiązanie IoTHub Bezutworzenie 1](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#solution-1)

### <a name="solution-2"></a>Rozwiązanie 2

- Upewnij się, że urządzenie ma dobrą łączność z Centrum IoT, [testując połączenie.](tutorial-connectivity.md) Jeśli sieć jest zawodne lub sporadyczne, nie zaleca się zwiększenie wartości keep-alive, ponieważ może to spowodować wykrycie (za pośrednictwem alertów usługi Azure Monitor, na przykład) trwa dłużej. 

- Użyj najnowszych wersji [SDK IoT](iot-hub-devguide-sdks.md).

### <a name="solution-3"></a>Rozwiązanie 3

Zobacz [rozwiązania wewnętrznych błędów serwera usługi IoT Hub](iot-hub-troubleshoot-error-500xxx-internal-errors.md#solution).

## <a name="next-steps"></a>Następne kroki

Zalecamy używanie zestawów SDK urządzeń IoT platformy Azure do niezawodnego zarządzania połączeniami. Aby dowiedzieć się więcej, zobacz [Zarządzanie łącznością i niezawodną wiadomością przy użyciu zestawów SDK urządzeń usługi Azure IoT Hub](iot-hub-reliability-features-in-sdks.md)