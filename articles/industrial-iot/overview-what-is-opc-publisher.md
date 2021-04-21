---
title: Wydawca OPC firmy Microsoft
description: Ten artykuł zawiera omówienie modułu OPC Publisher Edge.
author: v-condav
ms.author: jemorina
ms.service: industrial-iot
ms.topic: conceptual
ms.date: 3/22/2021
ms.openlocfilehash: 6df39c93e9bcfca522ac61a863c87269216cc592
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816174"
---
# <a name="what-is-the-opc-publisher"></a>Co to jest wydawca OPC?

Wydawca OPC to w pełni obsługiwany produkt firmy Microsoft, który stanowi mostk między zasobami przemysłowymi a Microsoft Azure chmurą. Można to zrobić, łącząc zasoby z obsługą OPC UA lub oprogramowanie łączności przemysłowej z Microsoft Azure chmurą. Publikuje ona dane telemetryczne, które zbiera do usługi Azure IoT Hub w różnych formatach, w tym w standardowym formacie IEC62541 OPC UA PubSub (od wersji 2.6). Wydawca OPC działa na Azure IoT Edge jako moduł lub na zwykłej platformie Docker jako kontener. Ponieważ korzysta z międzyplatformowego środowiska uruchomieniowego platformy .NET, działa natywnie zarówno w systemie Linux, jak i Windows 10.

Wydawca OPC to implementacja referencyjna, która pokazuje, jak:

- Połącz się z istniejącymi serwerami OPC UA.
- Publikowanie danych telemetrycznych zakodowanych w formacie JSON z serwerów OPC UA w formacie Pub/Sub OPC UA przy użyciu ładunku JSON w Azure IoT Hub.

Można użyć dowolnego z protokołów transportu, które obsługują zestaw SDK Azure IoT Hub klienta, takich jak HTTPS, AMQP i MQTT.

Implementacja referencyjna obejmuje następujące elementy.

- Klient OPC UA *do* łączenia się z istniejącymi serwerami OPC UA w sieci.
- Serwer OPC *UA* na porcie 62222, który umożliwia zarządzanie opublikowanymi treściami i oferuje IoT Hub metody do tego samego.

Implementację referencyjną [wydawcy OPC można](https://github.com/Azure/iot-edge-opc-publisher) pobrać z usługi GitHub.

Aplikacja jest implementowany przy użyciu technologii .NET Core i może działać na dowolnej platformie obsługiwanej przez platformę .NET Core.

## <a name="what-does-the-opc-publisher-do"></a>Co robi wydawca OPC?

Wydawca OPC implementuje logikę ponawiania, aby nawiązywać połączenia z punktami końcowymi, które nie odpowiadają na określoną liczbę żądań utrzymania aktywności. Na przykład, jeśli serwer OPC UA przestanie odpowiadać z powodu 3000 000 000 000 000 000 000 000 000 000 000 00

Dla każdego oddzielnego interwału publikowania na serwerze OPC UA aplikacja tworzy oddzielną subskrypcję, w ramach której są aktualizowane wszystkie węzły z tym interwałem publikowania.

Wydawca OPC obsługuje przetwarzanie wsadowe danych wysyłanych do IoT Hub w celu zmniejszenia obciążenia sieci. To przetwarzanie wsadowe wysyła pakiet do IoT Hub tylko wtedy, gdy skonfigurowany rozmiar pakietu zostanie osiągnięty.

Ta aplikacja używa stosu referencyjnego OPC Foundation OPC UA jako pakietów NuGet. Zobacz [https://opcfoundation.org/license/redistributables/1.3/](https://opcfoundation.org/license/redistributables/1.3/) , aby uzyskać postanowienia licencyjne.

## <a name="next-steps"></a>Następne kroki
Teraz, gdy już wiesz, czym jest wydawca OPC, możesz rozpocząć od jego wdrożenia:

> [!div class="nextstepaction"]
> [Wdrażanie wydawcy OPC w trybie autonomicznym](tutorial-publisher-deploy-opc-publisher-standalone.md)
