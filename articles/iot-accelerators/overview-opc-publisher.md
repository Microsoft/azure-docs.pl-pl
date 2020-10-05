---
title: Co to jest Wydawca OPC — Azure | Microsoft Docs
description: Ten artykuł zawiera omówienie funkcji wydawcy programu OPC. Umożliwia publikowanie szyfrowanych danych telemetrycznych JSON przy użyciu ładunku JSON w usłudze Azure IoT Hub.
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
ms.openlocfilehash: 49ca12ed4f408e2a3fce1c8e59f541778f35311e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91281783"
---
# <a name="what-is-opc-publisher"></a>Co to jest Wydawca OPC?

> [!IMPORTANT]
> Gdy aktualizujemy ten artykuł, zobacz [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) , aby uzyskać najbardziej aktualną zawartość.

OPC Publisher jest implementacją referencyjną, która demonstruje, jak:

- Połącz się z istniejącymi serwerami OPC UA.
- Publikuj dane telemetryczne kodowane w formacie JSON z serwerów OPC UA w podsystemie OPC UA pub/sub przy użyciu ładunku JSON do usługi Azure IoT Hub.

Można użyć dowolnego protokołu transportowego obsługiwanego przez zestaw SDK klienta platformy Azure IoT Hub: HTTPS, AMQP i MQTT.

Implementacja odniesienia obejmuje:

- *Klient* OPC UA do łączenia się z istniejącymi serwerami OPC UA w sieci.
- *Serwer* OPC UA na porcie 62222, za pomocą którego można zarządzać opublikowanymi i oferowanymi IoT Hub metodami bezpośrednimi w taki sam sposób.

[Implementację odwołania wydawcy OPC](https://github.com/Azure/iot-edge-opc-publisher) można pobrać z witryny GitHub.

Aplikacja jest implementowana przy użyciu technologii .NET Core i może działać na dowolnej platformie obsługiwanej przez platformę .NET Core.

Wydawca OPC implementuje logikę ponawiania prób w celu ustanowienia połączeń z punktami końcowymi, które nie odpowiadają na określoną liczbę żądań Keep Alive. Na przykład, jeśli serwer OPC UA przestanie odpowiadać z powodu awarii w zasilaniu.

Dla każdego odrębnego interwału publikowania na serwerze OPC UA aplikacja tworzy oddzielną subskrypcję, w ramach której zostaną zaktualizowane wszystkie węzły z tym interwałem publikacji.

Wydawca OPC obsługuje przetwarzanie wsadowe danych wysyłanych do IoT Hub, aby zmniejszyć obciążenie sieci. Ta Partia zadań wysyła pakiet do IoT Hub tylko wtedy, gdy osiągnięto skonfigurowany rozmiar pakietu.

Ta aplikacja używa stosu referencyjnego OPC Foundation OPC UA jako pakietów NuGet. Zapoznaj [https://opcfoundation.org/license/redistributables/1.3/](https://opcfoundation.org/license/redistributables/1.3/) się z postanowieniami licencyjnymi.

## <a name="next-steps"></a>Następne kroki

Teraz wiesz, co to jest Wydawca OPC, sugerowanym następnym krokiem jest zapoznanie się z tematem:

[Konfigurowanie wydawcy OPC](howto-opc-publisher-configure.md)