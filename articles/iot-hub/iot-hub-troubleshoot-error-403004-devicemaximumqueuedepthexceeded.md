---
title: Rozwiązywanie problemów z usługą Azure IoT Hub błąd 403004 DeviceMaximumQueueDepthExceeded
description: Dowiedz się, jak naprawić błąd 403004 DeviceMaximumQueueDepthExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 421066ef30e23a79b26f97939cdfffb5be83afb5
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148238"
---
# <a name="403004-devicemaximumqueuedepthexceeded"></a>403004 DeviceMaximumQueueDepthExceeded

W tym artykule opisano przyczyny i rozwiązania **403004 błędów DeviceMaximumQueueDepthExceeded** .

## <a name="symptoms"></a>Objawy

Podczas próby wysłania komunikatu z chmury do urządzenia żądanie kończy się niepowodzeniem z błędem **403004** lub **DeviceMaximumQueueDepthExceeded**.

## <a name="cause"></a>Przyczyna

Podstawową przyczyną jest to, że liczba komunikatów umieszczonych w kolejce dla urządzenia przekracza [limit kolejki (50)](./iot-hub-devguide-quotas-throttling.md#other-limits).

Najbardziej prawdopodobną przyczyną jest to, że używasz protokołu HTTPS, aby odebrać komunikat, który prowadzi do ciągłego sondowania przy użyciu `ReceiveAsync` , co spowodowało IoT Hub ograniczenie żądania.

## <a name="solution"></a>Rozwiązanie

Obsługiwany wzorzec dla komunikatów z chmury do urządzenia z protokołem HTTPS jest sporadycznie połączonymi urządzeniami, które często sprawdzają obecność komunikatów (mniej niż co 25 minut). Aby zmniejszyć prawdopodobieństwo uruchomienia do limitu kolejki, przełącz się do AMQP lub MQTT w celu uzyskania komunikatów z chmury do urządzenia.

Alternatywnie możesz ulepszyć logikę po stronie urządzenia, aby szybko zakończyć, odrzucić lub zrezygnować z kolejkowanej wiadomości, skrócić czas wygaśnięcia lub rozważyć wysłanie mniejszej liczby komunikatów. Zobacz [Czas wygaśnięcia komunikatu C2D](./iot-hub-devguide-messages-c2d.md#message-expiration-time-to-live).

Na koniec Rozważ użycie [interfejsu API przeczyszczania kolejki](/azure/iot-hub/iot-c-sdk-ref/iothub-registrymanager-h/iothubregistrymanager-deletedevice) , aby okresowo czyścić oczekujące komunikaty przed osiągnięciem limitu.