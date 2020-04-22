---
title: Co to jest wydawca OPC - Azure | Dokumenty firmy Microsoft
description: Ten artykuł zawiera omówienie funkcji programu OPC Publisher. Umożliwia publikowanie zakodowanych danych telemetrycznych JSON przy użyciu ładunku JSON w usłudze Azure IoT Hub.
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: c77dff4a4f89a78dc0a0d723fbb45fe691246112
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681916"
---
# <a name="what-is-opc-publisher"></a>Co to jest OPC Publisher?

OPC Publisher to implementacja referencyjna, która pokazuje, jak:

- Połącz się z istniejącymi serwerami OPC UA.
- Publikowanie danych telemetrycznych zakodowanych w formacie JSON z serwerów OPC UA w formacie OPC UA Pub/Sub przy użyciu ładunku JSON w usłudze Azure IoT Hub.

Można użyć dowolnego protokołu transportu, który obsługuje zestaw SDK klienta usługi Azure IoT Hub: HTTPS, AMQP i MQTT.

Implementacja referencyjna obejmuje:

- *Klient* OPC UA do łączenia się z istniejącymi serwerami OPC UA, które masz w sieci.
- *Serwer* OPC UA na porcie 62222, którego można używać do zarządzania tym, co zostało opublikowane i oferuje bezpośrednie metody Usługi IoT Hub, aby zrobić to samo.

[Implementację referencyjną programu OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) można pobrać z usługi GitHub.

Aplikacja jest implementowana przy użyciu technologii .NET Core i może działać na dowolnej platformie obsługiwanej przez .NET Core.

OPC Publisher implementuje logikę ponawiania próby do ustanowienia połączeń z punktami końcowymi, które nie odpowiadają na pewną liczbę żądań keep alive. Na przykład jeśli serwer OPC UA przestaje odpowiadać z powodu przerwy w zasilaniu.

Dla każdego odrębnego interwału publikowania na serwerze OPC UA aplikacja tworzy oddzielną subskrypcję, za pomocą której aktualizowane są wszystkie węzły z tym interwałem publikowania.

OPC Publisher obsługuje przetwarzanie danych wysyłanych do usługi IoT Hub w celu zmniejszenia obciążenia sieci. To przetwarzanie wsadowe wysyła pakiet do Centrum IoT tylko wtedy, gdy osiągnięto skonfigurowany rozmiar pakietu.

Ta aplikacja używa stosu odwołań OPC Foundation OPC UA jako pakietów NuGet. Zapoznaj [https://opcfoundation.org/license/redistributables/1.3/](https://opcfoundation.org/license/redistributables/1.3/) się z postanowieniami licencyjnymi.

### <a name="next-steps"></a>Następne kroki

Teraz dowiedziałeś się, czym jest OPC Publisher, następnym krokiem jest dowiedzenie się, jak [skonfigurować OPC Publisher](howto-opc-publisher-configure.md).
