---
title: Informacje o usłudze Azure IoT Hub Messaging | Microsoft Docs
description: Przewodnik dla deweloperów — obsługa urządzeń do chmury i komunikatów z chmury do urządzeń przy użyciu IoT Hub. Zawiera informacje o formatach komunikatów i obsługiwanych protokołach komunikacyjnych.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: f56332fa7f53c729ffaa28ea375f043d1b4a3678
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "60626251"
---
# <a name="send-device-to-cloud-and-cloud-to-device-messages-with-iot-hub"></a>Wysyłanie komunikatów z urządzenia do chmury i z chmury do urządzeń za pomocą IoT Hub

IoT Hub umożliwia komunikację dwukierunkową z urządzeniami. Użyj usługi IoT Hub Messaging, aby komunikować się z urządzeniami przez wysyłanie komunikatów z urządzeń do zaplecza rozwiązań i wysyłanie poleceń z zaplecza rozwiązań IoT do urządzeń. Dowiedz się więcej o [formacie wiadomości IoT Hub](iot-hub-devguide-messages-construct.md).

## <a name="sending-device-to-cloud-messages-to-iot-hub"></a>Wysyłanie komunikatów z urządzenia do chmury do IoT Hub

IoT Hub ma wbudowany punkt końcowy usługi, który może być używany przez usługi zaplecza do odczytywania komunikatów telemetrycznych z urządzeń. Ten punkt końcowy jest zgodny z [Event Hubs](https://docs.microsoft.com/azure/event-hubs/) i można użyć standardowych zestawów SDK IoT Hub do [odczytania z tego wbudowanego punktu końcowego](iot-hub-devguide-messages-read-builtin.md).

IoT Hub obsługuje również [niestandardowe punkty końcowe](iot-hub-devguide-endpoints.md#custom-endpoints) , które mogą być definiowane przez użytkowników w celu wysyłania danych telemetrycznych urządzenia i zdarzeń do usług platformy Azure przy użyciu [routingu komunikatów](iot-hub-devguide-messages-d2c.md).

## <a name="sending-cloud-to-device-messages-from-iot-hub"></a>Wysyłanie komunikatów z chmury do urządzenia z IoT Hub

Komunikaty z [chmury do urządzenia](iot-hub-devguide-messages-c2d.md) można wysyłać z zaplecza rozwiązania do urządzeń.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Podstawowe właściwości funkcji obsługi komunikatów IoT Hub to niezawodność i trwałość komunikatów. Te właściwości umożliwiają odporność na sporadyczną łączność po stronie urządzenia i ładowanie skoków w przypadku przetwarzania zdarzeń po stronie chmury. IoT Hub implementuje *co najmniej raz* gwarancje dostarczenia dla przesyłania komunikatów z urządzenia do chmury i z chmury do urządzenia.

## <a name="choosing-the-right-type-of-iot-hub-messaging"></a>Wybieranie odpowiedniego typu IoT Hub komunikatów

Użyj komunikatów z urządzenia do chmury, aby wysyłać dane telemetryczne i alerty szeregów czasowych z aplikacji urządzenia oraz komunikaty z chmury do urządzeń w celu powiadomienia jednokierunkowego do aplikacji urządzenia.

* Zapoznaj się z tematem [wskazówki dotyczące komunikacji między urządzeniami i chmurą](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-d2c-guidance) , aby wybrać komunikaty z urządzenia do chmury, raportowane właściwości lub przekazywanie plików.

* Zapoznaj się ze [wskazówkami dotyczącymi komunikacji między chmurą i urządzeniami](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-c2d-guidance) , aby wybrać komunikaty z chmury do urządzenia, odpowiednie właściwości lub metody bezpośrednie.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o IoT Hub [kierowaniu komunikatów](iot-hub-devguide-messages-d2c.md).

* Dowiedz się więcej na temat IoT Hub [komunikatów z chmury do urządzenia](iot-hub-devguide-messages-c2d.md).