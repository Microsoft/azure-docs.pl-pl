---
title: Architektura rozwiązań opartych na agentach
description: Dowiedz się więcej o architekturze i przepływie informacji opartym na agentach usługi Azure Defender.
ms.topic: overview
ms.date: 1/25/2021
ms.openlocfilehash: b04e8fa6225aef9f3c228a44631f117fedffccff
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784887"
---
# <a name="agent-based-solution-for-device-builders"></a>Rozwiązanie oparte na agencie dla konstruktorów urządzeń

Ten artykuł zawiera opis architektury systemu funkcjonalnego rozwiązania Defender for IoT opartego na agencie. Usługa Azure Defender for IoT oferuje dwa zestawy funkcji, które odpowiadają wymaganiom środowiska, rozwiązanie bez agenta dla organizacji i rozwiązanie oparte na agentach dla konstruktorów urządzeń.

## <a name="iot-hub-built-in-security"></a>Wbudowane zabezpieczenia Centrum IoT Hub

Usługa Defender for IoT jest domyślnie włączona w każdej nowo utworzonej IoT Hub. Usługa Defender for IoT zapewnia monitorowanie w czasie rzeczywistym, zalecenia i alerty, bez konieczności instalowania agenta na żadnym urządzeniu i używa zaawansowanej analizy na zarejestrowanych IoT Hub metadanych do analizowania i ochrony urządzeń pola oraz centrów IoT. 

## <a name="defender-for-iot-micro-agent"></a>Usługa Defender dla programu IoT Micro Agent 

Usługa Defender for IoT Micro Agent zapewnia kompleksową ochronę zabezpieczeń i wgląd w zachowanie urządzeń. zbiera, agreguje i analizuje nieprzetworzone zdarzenia zabezpieczeń z urządzeń. Surowe zdarzenia zabezpieczeń mogą obejmować połączenia IP, tworzenie procesów, logowania użytkowników i inne informacje związane z zabezpieczeniami. Usługa Defender dla agentów urządzeń IoT obsługuje również agregację zdarzeń, co pozwala uniknąć dużej przepływności sieci. Agenci są w wysokim stopniu dostosowywany, co pozwala na korzystanie z nich do określonych zadań, takich jak wysyłanie tylko ważnych informacji z najszybszą umową SLA lub agregowanie szczegółowych informacji o zabezpieczeniach i kontekstu w dużych segmentach, co pozwala uniknąć wyższych kosztów usługi.

Agenci urządzeń i inne aplikacje używają **zestawu SDK komunikatów zabezpieczeń platformy Azure** do wysyłania informacji o zabezpieczeniach do usługi Azure IoT Hub. Usługa IoT Hub pobiera te informacje i przekazuje je do usługi Defender for IoT.

Po włączeniu usługi Defender for IoT oprócz przesyłanych dalej danych usługa IoT Hub wysyła także wszystkie dane wewnętrzne do analizy za pomocą programu Defender for IoT. Te dane obejmują dzienniki operacji w chmurze urządzenia, tożsamości urządzeń i konfigurację centrum. Wszystkie te informacje ułatwiają tworzenie usługi Defender dla potoku analizy IoT.

Usługa Defender dla potoku usługi IoT Analytics odbiera również inne strumienie analizy zagrożeń z różnych źródeł w ramach firmy Microsoft i partnerów firmy Microsoft. Usługa Defender for IoT cała potoku analizy współpracuje z każdą konfiguracją klienta dokonaną w usłudze (taką jak niestandardowe alerty i użycie zestawu SDK komunikatów o zabezpieczeniach wysyłania).

Za pomocą potoku analizy usługa Defender for IoT łączy wszystkie strumienie informacji w celu generowania zaleceń i alertów z możliwością wykonania akcji. Potok zawiera zarówno reguły niestandardowe utworzone przez badaczy zabezpieczeń, jak i ekspertów, jak również modele uczenia maszynowego poszukujące odchyleń od standardowych zachowań urządzeń i analizy ryzyka.

Zalecenia i alerty usługi Defender for IoT (dane wyjściowe potoku analiz) są zapisywane w obszarze roboczym Log Analytics każdego klienta. Uwzględnianie nieprzetworzonych zdarzeń w obszarze roboczym oraz alertów i zaleceń umożliwia głębokie badanie szczegółowe oraz zapytania wykorzystujące dokładne szczegóły wykrytych podejrzanych działań.

:::image type="content" source="media/architecture/micro-agent-architecture.png" alt-text="Architektura Micro Agent.":::

## <a name="see-also"></a>Zobacz też

[Defender — często zadawane pytania dotyczące usługi IoT](resources-frequently-asked-questions.md)

[Wymagania wstępne systemu](quickstart-system-prerequisites.md)
