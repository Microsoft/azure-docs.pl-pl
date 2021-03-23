---
title: Wydawca programu Microsoft OPCe
description: Ten artykuł zawiera omówienie modułu OPC wydawcy Edge.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: conceptual
ms.date: 3/22/2021
ms.openlocfilehash: 3a44bdbadfe6ecd86a1b98fb7002f2d75c23bb6a
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104800537"
---
# <a name="what-is-the-opc-publisher"></a>Co to jest Wydawca OPC?

OPC Publisher to w pełni obsługiwany produkt firmy Microsoft opracowany w ramach otwartego, który mostkuje przerwy między zasobami przemysłowymi i chmurą Microsoft Azure. Jest to nawiązywane przez połączenie z zasobami z obsługą OPC UA lub oprogramowaniem do łączności przemysłowej oraz publikowanie danych telemetrycznych na platformie Azure IoT Hub w różnych formatach, w tym IEC62541 OPC UA PubSub Standard (od wersji 2,6 lub nowszej).

Jest on uruchamiany na Azure IoT Edge jako moduł lub w zwykłym Docker jako kontener. Ponieważ wykorzystuje środowisko uruchomieniowe dla wielu platform .NET, działa również natywnie w systemach Linux i Windows 10.

OPC Publisher jest implementacją referencyjną, która demonstruje, jak:

- Połącz się z istniejącymi serwerami OPC UA.
- Publikuj dane telemetryczne kodowane w formacie JSON z serwerów OPC UA w podsystemie OPC UA pub/sub przy użyciu ładunku JSON do usługi Azure IoT Hub.

Można użyć dowolnego protokołu transportowego obsługiwanego przez zestaw SDK klienta platformy Azure IoT Hub: HTTPS, AMQP i MQTT.

Implementacja odniesienia obejmuje:

- *Klient* OPC UA do łączenia się z istniejącymi serwerami OPC UA w sieci.
- *Serwer* OPC UA na porcie 62222, za pomocą którego można zarządzać opublikowanymi i oferowanymi IoT Hub metodami bezpośrednimi w taki sam sposób.

[Implementację odwołania wydawcy OPC](https://github.com/Azure/iot-edge-opc-publisher) można pobrać z witryny GitHub.

Aplikacja jest implementowana przy użyciu technologii .NET Core i może działać na dowolnej platformie obsługiwanej przez platformę .NET Core.

## <a name="what-does-the-opc-publisher-do"></a>Co robi OPC Wydawca?

Wydawca OPC implementuje logikę ponawiania prób w celu ustanowienia połączeń z punktami końcowymi, które nie odpowiadają na określoną liczbę żądań Keep Alive. Na przykład, jeśli serwer OPC UA przestanie odpowiadać z powodu awarii w zasilaniu.

Dla każdego odrębnego interwału publikowania na serwerze OPC UA aplikacja tworzy oddzielną subskrypcję, w ramach której zostaną zaktualizowane wszystkie węzły z tym interwałem publikacji.

Wydawca OPC obsługuje przetwarzanie wsadowe danych wysyłanych do IoT Hub, aby zmniejszyć obciążenie sieci. Ta Partia zadań wysyła pakiet do IoT Hub tylko wtedy, gdy osiągnięto skonfigurowany rozmiar pakietu.

Ta aplikacja używa stosu referencyjnego OPC Foundation OPC UA jako pakietów NuGet. Zapoznaj [https://opcfoundation.org/license/redistributables/1.3/](https://opcfoundation.org/license/redistributables/1.3/) się z postanowieniami licencyjnymi.

## <a name="next-steps"></a>Następne kroki
Teraz, gdy już wiesz, co to jest Wydawca OPC, możesz zacząć od jego wdrożenia:

> [!div class="nextstepaction"]
> [Wdróż wydawcę programu OPC w trybie autonomicznym](tutorial-publisher-deploy-opc-publisher-standalone.md)
