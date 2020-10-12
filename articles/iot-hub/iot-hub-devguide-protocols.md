---
title: Protokoły i porty komunikacyjne usługi Azure IoT Hub | Microsoft Docs
description: Przewodnik dla deweloperów — zawiera opis obsługiwanych protokołów komunikacyjnych na potrzeby komunikacji między urządzeniami i chmurą oraz numerami portów, które muszą być otwarte.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 1792535fab79ed20bdf77f96b4fc39f13b0c7bbb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90016008"
---
# <a name="reference---choose-a-communication-protocol"></a>Odwołanie — Wybieranie protokołu komunikacyjnego

Usługa IoT Hub zezwala na używanie następujących protokołów na potrzeby komunikacji po stronie urządzenia:

* [MQTT](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf)
* MQTT za pośrednictwem obiektów WebSocket
* [AMQP](https://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
* AMQP za pośrednictwem obiektów WebSocket
* HTTPS

Aby uzyskać informacje o tym, jak te protokoły obsługują określone funkcje IoT Hub, zobacz [wskazówki dotyczące komunikacji między urządzeniami i chmurą](iot-hub-devguide-d2c-guidance.md) oraz [wskazówki dotyczące komunikacji między chmurą i urządzeniami](iot-hub-devguide-c2d-guidance.md).

W poniższej tabeli przedstawiono zalecenia wysokiego poziomu dla wybranych protokołów:

| Protokół | Kiedy należy wybrać ten protokół |
| --- | --- |
| MQTT <br> MQTT za pośrednictwem protokołu WebSocket |Użyj na wszystkich urządzeniach, które nie wymagają połączenia wielu urządzeń (z ich własnymi poświadczeniami dla poszczególnych urządzeń) za pośrednictwem tego samego połączenia TLS. |
| AMQP <br> AMQP za pośrednictwem protokołu WebSocket |Użyj funkcji on i Gateway w chmurze, aby korzystać z multipleksera połączeń między urządzeniami. |
| HTTPS |Używany w przypadku urządzeń, które nie obsługują innych protokołów. |

Podczas wybierania protokołu dla komunikacji po stronie urządzenia należy wziąć pod uwagę następujące kwestie:

* **Wzorzec z chmury do urządzenia**. Protokół HTTPS nie ma wydajnej metody implementacji wypychania serwera. W związku z tym podczas korzystania z protokołu HTTPS IoT Hub sondowania urządzeń w przypadku komunikatów z chmury do urządzenia. Takie podejście jest nieefektywne dla urządzeń i IoT Hub. W obszarze bieżące wytyczne protokołu HTTPS każde urządzenie powinno sondować komunikaty co 25 minut lub dłużej. MQTT i AMQP obsługują wypychanie serwera podczas otrzymywania komunikatów z chmury do urządzenia. Umożliwiają natychmiastowe wypychanie komunikatów z IoT Hub do urządzenia. Jeśli opóźnienie dostarczania jest problemem, MQTT lub AMQP są najlepszymi protokołami do użycia. W przypadku rzadko połączonych urządzeń działa również protokół HTTPS.

* **Bramy pól**. MQTT i HTTPS obsługują tylko pojedynczą tożsamość urządzenia (identyfikator urządzenia i poświadczenia) na połączenie TLS. Z tego powodu te protokoły nie są obsługiwane w [scenariuszach z bramą pola](iot-hub-devguide-endpoints.md#field-gateways) , które wymagają buforowania komunikatów przy użyciu wielu tożsamości urządzeń w ramach jednej lub puli połączeń nadrzędnych do IoT Hub. Takie bramy mogą korzystać z protokołu, który obsługuje wiele tożsamości urządzeń na połączenie, takich jak AMQP, dla ruchu nadrzędnego.

* **Niskie urządzenia zasobów**. Biblioteki MQTT i HTTPS mają mniejsze rozmiary niż biblioteki AMQP. W związku z tym, jeśli urządzenie ma ograniczone zasoby (na przykład mniej niż 1 MB pamięci RAM), te protokoły mogą być jedyną dostępną implementacją protokołu.

* **Przechodzenie do sieci**. Standardowy protokół AMQP używa portu 5671, a MQTT nasłuchuje na porcie 8883. Korzystanie z tych portów może spowodować problemy w sieciach zamkniętych dla protokołów innych niż HTTPS. Używaj MQTT przez WebSockets, AMQP przez WebSockets lub HTTPS w tym scenariuszu.

* **Rozmiar ładunku**. MQTT i AMQP są protokołami binarnymi, co powoduje bardziej kompaktowe ładunki niż HTTPS.

> [!WARNING]
> W przypadku korzystania z protokołu HTTPS każde urządzenie powinno sondować komunikaty z chmury do urządzenia nie częściej niż co 25 minut. W trakcie opracowywania poszczególne urządzenia mogą sondować się częściej, w razie potrzeby.

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="port-numbers"></a>Numery portów

Urządzenia mogą komunikować się z IoT Hub na platformie Azure przy użyciu różnych protokołów. Zwykle wybór protokołu jest uzależniony od określonych wymagań rozwiązania. W poniższej tabeli wymieniono porty wychodzące, które muszą być otwarte dla urządzenia, aby można było korzystać z określonego protokołu:

| Protokół | Port |
| --- | --- |
| MQTT |8883 |
| MQTT za pośrednictwem obiektów WebSocket |443 |
| AMQP |5671 |
| AMQP za pośrednictwem obiektów WebSocket |443 |
| HTTPS |443 |

Po utworzeniu Centrum IoT Hub w regionie świadczenia usługi IoT Hub utrzymuje ten sam adres IP dla okresu istnienia tego Centrum IoT. Jeśli jednak firma Microsoft przeniesie Centrum IoT Hub do innej jednostki skalowania w celu utrzymania jakości usług, przypisze nowy adres IP.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o tym, jak IoT Hub implementuje protokół MQTT, zobacz temat [komunikowanie się z usługą IoT Hub przy użyciu protokołu MQTT](iot-hub-mqtt-support.md).
