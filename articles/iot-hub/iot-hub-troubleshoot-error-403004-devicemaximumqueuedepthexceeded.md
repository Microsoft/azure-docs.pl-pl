---
title: Rozwiązywanie problemów z błędem usługi Azure IoT Hub 403004 DeviceMaximumQueueDepthExceeded
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
ms.openlocfilehash: 5cc8bae0f0245f5c4b45ca0cd446582b04788c21
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758751"
---
# <a name="403004-devicemaximumqueuedepthexceeded"></a>403004 DeviceMaximumQueueDepthExceeded

W tym artykule opisano przyczyny i rozwiązania dla **403004 DeviceMaximumQueueDepthExceeded** błędów.

## <a name="symptoms"></a>Objawy

Podczas próby wysłania wiadomości z chmury do urządzenia żądanie kończy się niepowodzeniem z błędem **403004** lub **DeviceMaximumQueueDepthExceeded**.

## <a name="cause"></a>Przyczyna

Podstawową przyczyną jest to, że liczba wiadomości w kolejce dla urządzenia przekracza [limit kolejki (50)](./iot-hub-devguide-quotas-throttling.md#other-limits).

Najbardziej prawdopodobną przyczyną, że używasz do tego limitu jest, ponieważ używasz HTTPS do `ReceiveAsync`odbierania wiadomości, co prowadzi do ciągłego sondowania przy użyciu , co powoduje, że Usługa IoT Hub ograniczania żądania.

## <a name="solution"></a>Rozwiązanie

Obsługiwany wzorzec dla wiadomości z chmury do urządzenia z HTTPS jest sporadycznie podłączonych urządzeń, które sprawdzają wiadomości rzadko (mniej niż co 25 minut). Aby zmniejszyć prawdopodobieństwo uruchomienia w limicie kolejki, przełącz się na usługę AMQP lub MQTT dla komunikatów z chmury do urządzenia.

Alternatywnie należy poprawić logikę po stronie urządzenia, aby szybko kończyć, odrzucać lub porzucać wiadomości w kolejce, skracać czas życia lub rozważać wysyłanie mniejszej liczby wiadomości. Zobacz [Czas wygaśnięcia komunikatu C2D](./iot-hub-devguide-messages-c2d.md#message-expiration-time-to-live).

Na koniec należy rozważyć użycie [interfejsu API kolejki przeczyszczać](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/purgecommandqueue) okresowo czyścić oczekujące wiadomości przed osiągnięciem limitu.