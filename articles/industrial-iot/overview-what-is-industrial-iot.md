---
title: Omówienie usługi Azure Industrial IoT
description: Ten artykuł zawiera omówienie usługi IoT rzeczy. Wyjaśniono w nim składniki usługi Shop i zabezpieczenia związane z produkcją w IIoT.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: overview
ms.date: 3/22/2021
ms.openlocfilehash: 940391d26b5a8455fef01c8094cd08e05ab51290
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104787950"
---
# <a name="what-is-industrial-iot-iiot"></a>Co to jest przemysł IoT (IIoT)?

IIoT (przemysłowe Internet rzeczy) zwiększa efektywność przemysłową dzięki zastosowaniu IoT w branży produkcyjnej.

![Rzeczy przemysłowe](media/overview-what-is-Industrial-IoT/icon-255-px.png)

## <a name="improve-industrial-efficiencies"></a>Poprawa efektywności przemysłowej
Zwiększ produktywność operacyjną i zyskowność dzięki usłudze Azure Industrial IoT. Skrócenie czasochłonnego procesu uzyskiwania dostępu do zasobów w lokacji. Łączenie i monitorowanie sprzętu i urządzeń przemysłowych w chmurze — w tym na maszynach, które już działają w fabryce fabryki. Analizuj dane IoT w celu uzyskania szczegółowych informacji, które pomogą zwiększyć wydajność całej lokacji.

## <a name="industrial-iot-components"></a>Przemysłowe składniki IoT

**Urządzenia IoT Edge** Urządzenie IoT Edge składa się ze środowiska uruchomieniowego Edge i modułów brzegowych. 
- *Moduły brzegowe* są kontenerami platformy Docker, które są najmniejszą jednostką obliczeniową, taką jak OPC Publisher i OPC. 
- *Urządzenie brzegowe* służy do wdrażania takich modułów, które działają jako mediator między serwerem OPC UA i IoT Hub w chmurze. Więcej informacji na temat IoT Edge znajduje się [tutaj](https://azure.microsoft.com/services/iot-edge/).

**IoT Hub** IoT Hub pełni rolę centralnego centrum komunikatów na potrzeby komunikacji dwukierunkowej między aplikacją IoT a zarządzanymi urządzeniami. Jest to otwarta i elastyczna platforma w chmurze jako usługa, która obsługuje zestawy SDK typu open source i wiele protokołów. Przeczytaj więcej na temat IoT Hub [tym miejscu](https://azure.microsoft.com/services/iot-hub/).

**Moduły brzegowe przemysłowe**
- *Wydawca OPC*: Wydawca OPC działa w IoT Edge. Łączy się on z serwerami OPC UA i publikuje dane telemetryczne kodowane JSON z tych serwerów w formacie OPC UA "pub/Sub" na platformie Azure IoT Hub. Mogą być używane wszystkie protokoły transportowe obsługiwane przez zestaw SDK klienta IoT Hub platformy Azure, np. HTTPS, AMQP i MQTT.
- *OPC bliźniaczy*: OPCa z mikrousług, które używają Azure IoT Edge i IoT Hub do łączenia chmur i sieci fabrycznej. Przędza OPCa zapewnia odnajdywanie, rejestrację i zdalne sterowanie urządzeniami przemysłowymi za poorednictwem interfejsów API REST. OPC bliźniaczy nie wymaga zestawu SDK OPC Unified Architecture (OPC UA). Niezależny od język programowania i może być uwzględniony w przepływie pracy bezserwerowym.
- *Odnajdywanie*: moduł odnajdywania reprezentowany przez tożsamość odnajdowania zapewnia usługi odnajdywania na krawędzi, które obejmują odnajdowanie serwera OPC UA. Jeśli odnajdywanie jest skonfigurowane i włączone, moduł wyśle wyniki sondy skanowania za pośrednictwem IoT Edge i IoT Hub ścieżkę telemetrii do usługi dołączania. Usługa przetwarza wyniki i aktualizuje wszystkie powiązane tożsamości w rejestrze.


**Odkrywanie i rejestrowanie zasobów przemysłowych oraz zarządzanie nimi przy użyciu platformy Azure** Usługa Azure Industrial IoT umożliwia operatorom zakładu odnajdywanie serwerów z obsługą OPC UA w sieci fabrycznej i rejestrowanie ich w usłudze Azure IoT Hub. Pracownicy operacyjni mogą subskrybować lub reagować na zdarzenia w fabryce fabryki z dowolnego miejsca na świecie. Interfejsy API REST mikrousług są dublowane po stronie usługi OPC UA. Są one zabezpieczone przy użyciu uwierzytelniania OAUTH i autoryzacji obsługiwanej przez Azure Active Directory (AAD). Dzięki temu aplikacje w chmurze mogą przeglądać przestrzenie adresowe serwera lub zmienne odczytu/zapisu oraz wykonywać metody przy użyciu protokołu HTTPS i prostych ładunków JSON OPC UA.

## <a name="next-steps"></a>Następne kroki
Teraz, gdy wiesz już, co to jest przemysł IoT, możesz zapoznać się z informacjami na temat platformy IoT i wydawcy OPC:

> [!div class="nextstepaction"]
> [Co to jest platforma IoT w przemyśle?](overview-what-is-industrial-iot-platform.md)

> [!div class="nextstepaction"]
> [Co to jest Wydawca OPC?](overview-what-is-opc-publisher.md)