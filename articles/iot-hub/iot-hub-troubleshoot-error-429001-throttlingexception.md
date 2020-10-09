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
ms.openlocfilehash: 3095e398d7e5cfe59085144d5bb4e8dc33618064
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "76960700"
---
# <a name="429001-throttlingexception"></a>429001 ThrottlingException

W tym artykule opisano przyczyny i rozwiązania **429001 błędów ThrottlingException** .

## <a name="symptoms"></a>Objawy

Twoje żądania do IoT Hub zakończą się niepowodzeniem z błędem **429001 ThrottlingException**.

## <a name="cause"></a>Przyczyna

Przekroczono [limity ograniczania](./iot-hub-devguide-quotas-throttling.md) IoT Hub dla wymaganej operacji.

## <a name="solution"></a>Rozwiązanie

Sprawdź, czy osiągnięto limit ograniczania, porównując wartość metryki *wysyłania komunikatów telemetrycznych* z ograniczeniami określonymi powyżej. Możesz również sprawdzić *liczbę błędów ograniczania przepustowości* . Aby uzyskać więcej informacji na temat tych i innych metryk dostępnych dla IoT Hub, zobacz [IoT Hub metryki i sposoby ich używania](./iot-hub-metrics.md#iot-hub-metrics-and-how-to-use-them).

IoT Hub zwraca 429 ThrottlingException tylko po naruszeniu limitu przez zbyt długi czas. Dzieje się tak, aby komunikaty nie zostały porzucone, jeśli Twoje dane będą pobierane przez Centrum IoT Hub. W międzyczasie usługa IoT Hub przetwarza komunikaty z ograniczoną przepływnością, co może być powolne, jeśli lista prac zawiera zbyt wiele danych. Aby dowiedzieć się więcej, zobacz [Kształtowanie ruchu w usłudze IoT Hub](./iot-hub-devguide-quotas-throttling.md#traffic-shaping).

## <a name="next-steps"></a>Następne kroki

Rozważ [skalowanie w górę IoT Hub](./iot-hub-scaling.md) , jeśli korzystasz z limitów przydziału lub ograniczania przepustowości.