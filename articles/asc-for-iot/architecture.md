---
title: Architektura rozwiązania
description: Dowiedz się więcej o przepływie informacji w Azure Security Center usługi IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 4cc7d1982555f058a4ea23f7d8a6fdc2d83e484d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81311747"
---
# <a name="azure-security-center-for-iot-architecture"></a>Architektura usługi Azure Security Center dla IoT

W tym artykule wyjaśniono architekturę systemu funkcjonalnego Azure Security Center rozwiązania IoT.

## <a name="azure-security-center-for-iot-components"></a>Azure Security Center dla składników IoT

Azure Security Center IoT składa się z następujących składników:

- Integracja IoT Hub
- Agenci urządzeń (opcjonalnie)
- Wyślij zestaw SDK komunikatów zabezpieczeń
- Potok analizy

### <a name="azure-security-center-for-iot-workflows"></a>Azure Security Center dla przepływów pracy IoT

Azure Security Center IoT działa w jednym z dwóch przepływów pracy funkcji: wbudowanych i ulepszonych

### <a name="built-in"></a>Wbudowane

W trybie **wbudowanym** Azure Security Center usługi IoT jest włączane po wybraniu opcji Włącz opcję **zabezpieczenia** w IoT Hub. Zapewnianie monitorowania w czasie rzeczywistym, zaleceń i alertów, wbudowany tryb oferuje jednoetapową widoczność urządzenia i niedopasowane zabezpieczenia. Tryb kompilacji nie wymaga instalacji agenta na żadnym urządzeniu i używa zaawansowanej analizy na zarejestrowanych działaniach, aby przeanalizować i chronić urządzenie pola.

### <a name="enhanced"></a>Rozszerzone

W trybie **rozszerzonym** po włączeniu opcji **zabezpieczeń** w IoT Hub i zainstalowaniu Azure Security Center dla agentów urządzeń IoT na urządzeniach Agenci zbierają, agregują i analizują surowe zdarzenia zabezpieczeń z urządzeń. Surowe zdarzenia zabezpieczeń mogą obejmować połączenia IP, tworzenie procesów, logowania użytkowników i inne informacje związane z zabezpieczeniami. Azure Security Center dla agentów urządzeń IoT obsługują również agregację zdarzeń, aby zapobiec dużej przepływności sieci. Agenci są w wysokim stopniu dostosowywany, co pozwala na korzystanie z nich do określonych zadań, takich jak wysyłanie tylko ważnych informacji z najszybszą umową SLA lub agregowanie szczegółowych informacji o zabezpieczeniach i kontekstu w dużych segmentach, co pozwala uniknąć wyższych kosztów usługi.

![Architektura usługi Azure Security Center dla IoT](./media/architecture/azure-iot-security-architecture.png)

Agenci urządzeń i inne aplikacje używają **zestawu SDK komunikatów zabezpieczeń platformy Azure** do wysyłania informacji o zabezpieczeniach do usługi Azure IoT Hub. IoT Hub pobiera te informacje i przekazuje je do Azure Security Center usługi IoT.

Gdy Azure Security Center usługi IoT zostanie włączona, oprócz danych przekazywanych przez program IoT Hub wysyła także wszystkie dane wewnętrzne do analizy przez Azure Security Center dla IoT. Te dane obejmują dzienniki operacji w chmurze urządzenia, tożsamości urządzeń i konfigurację centrum. Wszystkie te informacje ułatwiają tworzenie Azure Security Center dla potoku analizy IoT.

Azure Security Center dla potoku usługi IoT Analytics odbiera również dodatkowe strumienie analizy zagrożeń z różnych źródeł w ramach firmy Microsoft i partnerów firmy Microsoft. Azure Security Center dla całego potoku analizy usługi IoT działa z każdą konfiguracją klienta w usłudze (taką jak niestandardowe alerty i użycie zestawu SDK komunikatów o zabezpieczeniach wysyłania).

Korzystając z potoku analizy, Azure Security Center dla usługi IoT łączy wszystkie strumienie informacji w celu generowania zaleceń i alertów z możliwością wykonania akcji. Potok zawiera zarówno reguły niestandardowe utworzone przez badaczy zabezpieczeń, jak i ekspertów, jak również modele uczenia maszynowego poszukujące odchyleń od standardowych zachowań urządzeń i analizy ryzyka.

Azure Security Center do zaleceń i alertów usługi IoT (dane wyjściowe potoku analiz) są zapisywane w obszarze roboczym Log Analytics każdego klienta. Uwzględnianie nieprzetworzonych zdarzeń w obszarze roboczym, a także alertów i zaleceń pozwala na głębokie badanie szczegółowe oraz zapytania wykorzystujące dokładne szczegóły wykrytych podejrzanych działań.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono informacje o podstawowej architekturze i przepływie pracy Azure Security Center na potrzeby rozwiązania IoT. Aby dowiedzieć się więcej o wymaganiach wstępnych, sposobach rozpoczynania i włączania rozwiązania zabezpieczeń w IoT Hub, zobacz następujące artykuły:

- [Wymagania wstępne usługi](service-prerequisites.md)
- [Rozpoczęcie pracy](getting-started.md)
- [Konfigurowanie rozwiązania](quickstart-configure-your-solution.md)
- [Włącz zabezpieczenia w IoT Hub](quickstart-onboard-iot-hub.md)
- [Azure Security Center często zadawane pytania dotyczące usługi IoT](resources-frequently-asked-questions.md)
- [Azure Security Center for IoT security alerts (Alerty zabezpieczeń usługi Azure Security Center dla IoT)](concept-security-alerts.md)
