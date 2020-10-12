---
title: Rozwiązywanie problemów z usługą Azure IoT Hub błąd 404104 DeviceConnectionClosedRemotely
description: Dowiedz się, jak naprawić błąd 404104 DeviceConnectionClosedRemotely
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: mqtt
ms.openlocfilehash: c8cb91aa0c7ce1610320d4107db282d3c34407ba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "81758725"
---
# <a name="404104-deviceconnectionclosedremotely"></a>404104 DeviceConnectionClosedRemotely

W tym artykule opisano przyczyny i rozwiązania **404104 błędów DeviceConnectionClosedRemotely** .

## <a name="symptoms"></a>Objawy

### <a name="symptom-1"></a>Objaw 1

Urządzenia są rozłączane w regularnych odstępach czasu (na przykład co 65 minut) i zobaczysz **404104 DeviceConnectionClosedRemotely** IoT Hub w dziennikach diagnostycznych. Czasami widoczne jest również **401003 IoTHubUnauthorized** i pomyślne zdarzenie połączenia urządzenia krótsze niż minutę.

### <a name="symptom-2"></a>Objaw 2

Urządzenia rozłączją losowo i zobaczysz **404104 DeviceConnectionClosedRemotely** w dziennikach diagnostycznych IoT Hub.

### <a name="symptom-3"></a>Objaw 3

Wiele urządzeń rozłączy się jednocześnie, zobaczysz DIP w [metrykach podłączonych urządzeń](iot-hub-metrics.md), a w dziennikach diagnostycznych występuje więcej **404104 DeviceConnectionClosedRemotely** i [500xxx błędów wewnętrznych](iot-hub-troubleshoot-error-500xxx-internal-errors.md) .

## <a name="causes"></a>Przyczyny

### <a name="cause-1"></a>Przyczyna 1

[Token sygnatury dostępu współdzielonego używany do nawiązywania połączenia z usługą IoT Hub](iot-hub-devguide-security.md#security-tokens) wygasł, co powoduje IoT Hub rozłączenia urządzenia. Połączenie zostanie ponownie nawiązane, gdy token zostanie odświeżony przez urządzenie. Na przykład [token sygnatury dostępu współdzielonego wygasa co godzinę domyślnie dla zestawu C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-authentication), co może prowadzić do regularnego rozłączenia.

Aby dowiedzieć się więcej, zobacz [401003 IoTHubUnauthorized przyczyna](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#cause-1).

### <a name="cause-2"></a>Przyczyna 2

Dostępne są następujące możliwości:

- Urządzenie utraciło łączność sieciową dłużej niż [MQTT Keep-Alive](iot-hub-mqtt-support.md#default-keep-alive-timeout), co spowodowało zdalny limit czasu bezczynności. Ustawienie Keep-Alive MQTT może być inne dla każdego urządzenia.

- Urządzenie wysłało Reset poziomu protokołu TCP/IP, ale nie wysłało poziomu aplikacji `MQTT DISCONNECT` . W zasadzie urządzenie nie zamknął podstawowego połączenia gniazda. Czasami przyczyną tego problemu są usterki we wcześniejszych wersjach zestawu SDK usługi Azure IoT.

- Awaria aplikacji po stronie urządzenia.

### <a name="cause-3"></a>Przyczyna 3

W IoT Hub może występować przejściowy problem. Zobacz, [IoT Hub Przyczyna błędu wewnętrznego serwera](iot-hub-troubleshoot-error-500xxx-internal-errors.md#cause).

## <a name="solutions"></a>Rozwiązania

### <a name="solution-1"></a>Rozwiązanie 1

Zobacz [401003 IoTHubUnauthorized rozwiązanie 1](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#solution-1)

### <a name="solution-2"></a>Rozwiązanie 2

- Upewnij się, że urządzenie ma dobrą łączność do IoT Hub przez [testowanie połączenia](tutorial-connectivity.md). Jeśli sieć jest niezawodna lub nieprzerwana, nie zalecamy zwiększenia wartości Keep-Alive, ponieważ może to skutkować wykryciem (na przykład za pośrednictwem Azure Monitor alertów) trwających dłużej. 

- Użyj najnowszych wersji [zestawów SDK IoT](iot-hub-devguide-sdks.md).

### <a name="solution-3"></a>Rozwiązanie 3

Zobacz [rozwiązania, na których IoT Hub wewnętrzne błędy serwera](iot-hub-troubleshoot-error-500xxx-internal-errors.md#solution).

## <a name="next-steps"></a>Następne kroki

Zalecamy używanie zestawów SDK urządzeń usługi Azure IoT do niezawodnego zarządzania połączeniami. Aby dowiedzieć się więcej, zobacz [Zarządzanie łącznością i niezawodną obsługą komunikatów przy użyciu zestawów SDK urządzeń IoT Hub platformy Azure](iot-hub-reliability-features-in-sdks.md)