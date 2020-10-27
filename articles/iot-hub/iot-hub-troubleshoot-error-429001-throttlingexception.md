---
title: Rozwiązywanie problemów z usługą Azure IoT Hub błąd 429001 ThrottlingException
description: Dowiedz się, jak naprawić błąd 429001 ThrottlingException
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: d2f12a6982886eeaa375151c5b8a73acc573aab9
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545365"
---
# <a name="429001-throttlingexception"></a>429001 ThrottlingException

W tym artykule opisano przyczyny i rozwiązania **429001 błędów ThrottlingException** .

## <a name="symptoms"></a>Objawy

Twoje żądania do IoT Hub zakończą się niepowodzeniem z błędem **429001 ThrottlingException** .

## <a name="cause"></a>Przyczyna

Przekroczono [limity ograniczania](./iot-hub-devguide-quotas-throttling.md) IoT Hub dla wymaganej operacji.

## <a name="solution"></a>Rozwiązanie

Sprawdź, czy osiągnięto limit ograniczania, porównując wartość metryki *wysyłania komunikatów telemetrycznych* z ograniczeniami określonymi powyżej. Możesz również sprawdzić *liczbę błędów ograniczania przepustowości* . Aby uzyskać informacje o tych metrykach, zobacz [metryki telemetrii urządzeń](monitor-iot-hub-reference.md#device-telemetry-metrics). Aby dowiedzieć się, jak używać metryk do monitorowania Centrum IoT, zobacz [IoT Hub monitorowania](monitor-iot-hub.md).

IoT Hub zwraca 429 ThrottlingException tylko po naruszeniu limitu przez zbyt długi czas. Dzieje się tak, aby komunikaty nie zostały porzucone, jeśli Twoje dane będą pobierane przez Centrum IoT Hub. W międzyczasie usługa IoT Hub przetwarza komunikaty z ograniczoną przepływnością, co może być powolne, jeśli lista prac zawiera zbyt wiele danych. Aby dowiedzieć się więcej, zobacz [Kształtowanie ruchu w usłudze IoT Hub](./iot-hub-devguide-quotas-throttling.md#traffic-shaping).

## <a name="next-steps"></a>Następne kroki

Rozważ [skalowanie w górę IoT Hub](./iot-hub-scaling.md) , jeśli korzystasz z limitów przydziału lub ograniczania przepustowości.