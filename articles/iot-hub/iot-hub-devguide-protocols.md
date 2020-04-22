---
title: Protokoły i porty komunikacyjne usługi Azure IoT Hub | Dokumenty firmy Microsoft
description: Przewodnik dla deweloperów — opisuje obsługiwane protokoły komunikacyjne dla komunikacji między urządzeniami a chmurą i chmurą do urządzenia oraz numery portów, które muszą być otwarte.
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
ms.openlocfilehash: 3e7f31371a0582a6f4941efbfa0087119278d2d1
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729116"
---
# <a name="reference---choose-a-communication-protocol"></a>Reference - wybierz protokół komunikacyjny

Usługa IoT Hub zezwala na używanie następujących protokołów na potrzeby komunikacji po stronie urządzenia:

* [MQTT](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf)
* MQTT za pośrednictwem obiektów WebSocket
* [AMQP](https://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
* AMQP za pośrednictwem obiektów WebSocket
* HTTPS

Aby uzyskać informacje o tym, jak te protokoły obsługują określone funkcje Usługi IoT Hub, zobacz [Wskazówki dotyczące komunikacji między urządzeniami](iot-hub-devguide-d2c-guidance.md) a chmurą oraz [wskazówki dotyczące komunikacji między chmurami a urządzeniem](iot-hub-devguide-c2d-guidance.md).

Poniższa tabela zawiera zalecenia wysokiego poziomu dotyczące wyboru protokołu:

| Protocol (Protokół) | Kiedy należy wybrać ten protokół |
| --- | --- |
| MQTT <br> MQTT przez WebSocket |Użyj na wszystkich urządzeniach, które nie wymagają podłączania wielu urządzeń (każdy z własnymi poświadczeniami na urządzenie) za pośrednictwem tego samego połączenia TLS. |
| AMQP <br> AMQP przez WebSocket |Użyj na bramkach polowych i chmurze, aby korzystać z multipleksowania połączeń na różnych urządzeniach. |
| HTTPS |Użyj dla urządzeń, które nie obsługują innych protokołów. |

Przy wyborze protokołu komunikacji po stronie urządzenia należy wziąć pod uwagę następujące kwestie:

* **Wzór chmury do urządzenia**. Protokół HTTPS nie ma skutecznego sposobu implementacji wypychania serwera. W związku z tym podczas korzystania z protokołu HTTPS urządzenia sondują Centrum IoT hub dla komunikatów z chmury do urządzenia. Takie podejście jest nieefektywne zarówno dla urządzenia i usługi IoT Hub. Zgodnie z aktualnymi wytycznymi HTTPS każde urządzenie powinno sondować wiadomości co 25 minut lub dłużej. MQTT i AMQP obsługują wypychanie serwera podczas odbierania komunikatów z chmury do urządzenia. Umożliwiają one natychmiastowe wypychanie wiadomości z usługi IoT Hub do urządzenia. Jeśli opóźnienie dostarczania jest problemem, MQTT lub AMQP są najlepsze protokoły do użycia. W przypadku rzadko podłączonych urządzeń działa również protokół HTTPS.

* **Bramy polowe**. MQTT i HTTPS obsługują tylko jedną tożsamość urządzenia (identyfikator urządzenia i poświadczenia) na połączenie TLS. Z tego powodu te protokoły nie są obsługiwane w [scenariuszach bramy pola,](iot-hub-devguide-endpoints.md#field-gateways) które wymagają multipleksowania wiadomości przy użyciu wielu tożsamości urządzeń w jednym lub puli nadrzędnych połączeń z Centrum IoT Hub. Takie bramy można użyć protokołu, który obsługuje wiele tożsamości urządzeń na połączenie, takich jak AMQP, dla ich ruchu nadrzędnego.

* **Urządzenia o niskiej alokacji zasobów**. Biblioteki MQTT i HTTPS mają mniejszy rozmiar niż biblioteki AMQP. W związku z tym jeśli urządzenie ma ograniczone zasoby (na przykład mniej niż 1 MB pamięci RAM), te protokoły mogą być jedyną dostępną implementacją protokołu.

* **Przechodzenie przez sieć**. Standardowy protokół AMQP używa portu 5671, a MQTT nasłuchuje na porcie 8883. USe tych portów może powodować problemy w sieciach, które są zamknięte dla protokołów innych niż HTTPS. Użyj MQTT przez WebSockets, AMQP przez WebSockets lub HTTPS w tym scenariuszu.

* **Rozmiar ładunku**. MQTT i AMQP są protokołami binarnymi, co skutkuje bardziej kompaktowymi ładunkami niż HTTPS.

> [!WARNING]
> Podczas korzystania z protokołu HTTPS każde urządzenie powinno sondować wiadomości z chmury do urządzenia nie częściej niż raz na 25 minut. W fazie rozwoju każde urządzenie może sondować częściej, w razie potrzeby.

## <a name="port-numbers"></a>Numery portów

Urządzenia mogą komunikować się z Usługą IoT Hub na platformie Azure przy użyciu różnych protokołów. Zazwyczaj wybór protokołu jest napędzany przez określone wymagania rozwiązania. W poniższej tabeli wymieniono porty wychodzące, które muszą być otwarte, aby urządzenie mogło używać określonego protokołu:

| Protocol (Protokół) | Port |
| --- | --- |
| MQTT |8883 |
| MQTT za pośrednictwem obiektów WebSocket |443 |
| AMQP |5671 |
| AMQP za pośrednictwem obiektów WebSocket |443 |
| HTTPS |443 |

Po utworzeniu centrum IoT hub w regionie platformy Azure centrum IoT zapewnia ten sam adres IP przez cały okres istnienia tego centrum IoT. Jeśli jednak firma Microsoft przeniesie centrum IoT hub do innej jednostki skalowania w celu utrzymania jakości usług, zostanie przypisany nowy adres IP.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o tym, jak Centrum IoT implementuje protokół MQTT, zobacz [Komunikowanie się z koncentratorem IoT za pomocą protokołu MQTT](iot-hub-mqtt-support.md).
