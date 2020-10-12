---
title: Architektura rozwiązania
description: Dowiedz się więcej o przepływie informacji w usłudze Azure Defender dla usługi IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: elazark
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2020
ms.author: v-ekrieg
ms.openlocfilehash: 3fc695770350e5a60ae3da9ab1796da5cac99370
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91843417"
---
# <a name="azure-defender-for-iot-architecture"></a>Architektura usługi Azure Defender for IoT

W tym artykule wyjaśniono architekturę systemu funkcjonalnego rozwiązania Defender for IoT.

## <a name="defender-for-iot-components"></a>Usługa Defender dla składników IoT

Usługa Defender for IoT składa się z następujących składników:

- Integracja IoT Hub
- Agenci urządzeń (opcjonalnie)
- Wyślij zestaw SDK komunikatów zabezpieczeń
- Potok analizy

### <a name="defender-for-iot-workflows"></a>Usługa Defender dla przepływów pracy usługi IoT

Usługa Defender for IoT działa w jednym z dwóch przepływów pracy funkcji: wbudowanych i ulepszonych

### <a name="built-in"></a>Wbudowane

W trybie **wbudowanym** usługa Defender for IoT jest włączona po wybraniu opcji Włącz opcję **zabezpieczenia** w IoT Hub. Zapewnianie monitorowania w czasie rzeczywistym, zaleceń i alertów, wbudowany tryb oferuje jednoetapową widoczność urządzenia i niedopasowane zabezpieczenia. Tryb kompilacji nie wymaga instalacji agenta na żadnym urządzeniu i używa zaawansowanej analizy na zarejestrowanych działaniach, aby przeanalizować i chronić urządzenie pola.

### <a name="enhanced"></a>Rozszerzone

W trybie **rozszerzonym** po włączeniu opcji **zabezpieczeń** w IoT Hub i zainstalowaniu agentów usługi Defender dla IoT na urządzeniach Agenci zbierają, agregują i analizują surowe zdarzenia zabezpieczeń z urządzeń. Surowe zdarzenia zabezpieczeń mogą obejmować połączenia IP, tworzenie procesów, logowania użytkowników i inne informacje związane z zabezpieczeniami. Obrony przed agentami urządzeń IoT obsługują również agregację zdarzeń, co pozwala uniknąć dużej przepływności sieci. Agenci są w wysokim stopniu dostosowywany, co pozwala na korzystanie z nich do określonych zadań, takich jak wysyłanie tylko ważnych informacji z najszybszą umową SLA lub agregowanie szczegółowych informacji o zabezpieczeniach i kontekstu w dużych segmentach, co pozwala uniknąć wyższych kosztów usługi.

![Architektura usługi Defender for IoT](./media/architecture/azure-iot-security-architecture.png)

Agenci urządzeń i inne aplikacje używają **zestawu SDK komunikatów zabezpieczeń platformy Azure** do wysyłania informacji o zabezpieczeniach do usługi Azure IoT Hub. IoT Hub pobiera te informacje i przekazuje je do usługi Defender for IoT.

Po włączeniu usługi Defender for IoT (oprócz danych przekazywanych dalej) IoT Hub wysyła także wszystkie dane wewnętrzne do analizy przez usługę Defender for IoT. Te dane obejmują dzienniki operacji w chmurze urządzenia, tożsamości urządzeń i konfigurację centrum. Wszystkie te informacje ułatwiają tworzenie usługi Defender dla potoku analizy IoT.

Usługa Defender dla potoku usługi IoT Analytics odbiera również dodatkowe strumienie analizy zagrożeń z różnych źródeł w ramach firmy Microsoft i partnerów firmy Microsoft. Usługa Defender for IoT cała potoku analizy współpracuje z każdą konfiguracją klienta dokonaną w usłudze (taką jak niestandardowe alerty i użycie zestawu SDK komunikatów o zabezpieczeniach wysyłania).

Za pomocą potoku analizy usługa Defender for IoT łączy wszystkie strumienie informacji w celu generowania zaleceń i alertów z możliwością wykonania akcji. Potok zawiera zarówno reguły niestandardowe utworzone przez badaczy zabezpieczeń, jak i ekspertów, jak również modele uczenia maszynowego poszukujące odchyleń od standardowych zachowań urządzeń i analizy ryzyka.

Zalecenia i alerty usługi Defender for IoT (dane wyjściowe potoku analiz) są zapisywane w obszarze roboczym Log Analytics każdego klienta. Uwzględnianie nieprzetworzonych zdarzeń w obszarze roboczym, a także alertów i zaleceń pozwala na głębokie badanie szczegółowe oraz zapytania wykorzystujące dokładne szczegóły wykrytych podejrzanych działań.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono informacje o podstawowej architekturze i przepływie pracy usługi Defender for IoT. Aby dowiedzieć się więcej o wymaganiach wstępnych, sposobach rozpoczynania i włączania rozwiązania zabezpieczeń w IoT Hub, zobacz następujące artykuły:

- [Wymagania wstępne usługi](service-prerequisites.md)
- [Rozpoczęcie pracy](getting-started.md)
- [Konfigurowanie rozwiązania](quickstart-configure-your-solution.md)
- [Włącz zabezpieczenia w IoT Hub](quickstart-onboard-iot-hub.md)
- [Defender — często zadawane pytania dotyczące usługi IoT](resources-frequently-asked-questions.md)
- [Alerty zabezpieczeń usługi Defender dla IoT](concept-security-alerts.md)
