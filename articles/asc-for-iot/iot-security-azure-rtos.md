---
title: Pomoc techniczna platformy Azure RTO
description: Dowiedz się więcej o obsłudze usługi Azure RTO w Azure Security Center w usłudze IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/15/2020
ms.author: mlottner
ms.openlocfilehash: af4579d25b94fc12f67cfc91d0f6a5af4b35c810
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87097755"
---
# <a name="azure-security-center-for-iot-security-solution-for-azure-rtos"></a>Azure Security Center rozwiązania do ochrony IoT dla platformy Azure RTO 

Moduł Azure Security Center dla usługi IoT Security oferuje kompleksowe rozwiązanie zabezpieczeń dla urządzeń z systemem Azure RTO. Usługa Azure RTO jest dostarczana z wbudowanym modułem zabezpieczeń, który obejmuje typowe zagrożenia na urządzeniach z systemem operacyjnym w czasie rzeczywistym. 

![Azure Security Center usługi IoT Azure RTO](./media/architecture/azure-rtos-security-monitoring.png)


Azure Security Center dla modułu zabezpieczeń IoT z obsługą usługi Azure RTO oferuje następujące funkcje: 
- Wykrycie złośliwego działania sieci
- Niestandardowe alerty na podstawie, zachowanie urządzenia określania poziomu odniesienia
- Poprawa higieny zabezpieczeń urządzeń

### <a name="detection-of-malicious-network-activities"></a>Wykrywanie złośliwych działań w sieci

Monitorowane jest aktywność sieci przychodzących i wychodzących poszczególnych urządzeń (obsługiwane protokoły: TCP, UDP, ICMP w protokołach IPv4 i IPv6). Azure Security Center usługi IoT sprawdza wszystkie te działania w sieci w odniesieniu do źródła danych analizy zagrożeń firmy Microsoft. Kanał informacyjny jest aktualizowany w czasie rzeczywistym przy użyciu milionów unikatowych wskaźników zagrożeń zebranych na całym świecie. 

### <a name="device-behavior-baselining-based-on-custom-alerts"></a>Zachowanie urządzenia określania poziomu odniesienia na podstawie alertów niestandardowych

Określania poziomu odniesienia umożliwia klastrowanie urządzeń w grupach zabezpieczeń i Definiowanie oczekiwanych zachowań poszczególnych grup. Ponieważ urządzenia IoT są zwykle zaprojektowane do działania w dobrze zdefiniowanych i ograniczonych scenariuszach, można łatwo utworzyć linię bazową, która definiuje ich oczekiwane zachowanie przy użyciu zestawu parametrów. Wszelkie odchylenia od linii bazowej wyzwala alert. 

### <a name="improve-your-device-security-hygiene"></a>Ulepszanie higieny bezpieczeństwa urządzeń

Korzystając z zalecanej Azure Security Center infrastruktury dla IoT, uzyskasz wiedzę i wgląd w informacje o problemach w Twoim środowisku, które wpływają i uszkadzają stan zabezpieczeń urządzeń. Niska stan zabezpieczeń urządzeń IoT może pozwolić na pomyślne ataki w przypadku pomyślnego powolnej ochrony, ponieważ zabezpieczenia są zawsze mierzone przez najsłabsze łącze w każdej organizacji. 

## <a name="get-started-protecting-azure-rtos-devices"></a>Wprowadzenie do ochrony urządzeń z usługą Azure RTO

- Azure Security Center dla modułu zabezpieczeń IoT dla usługi Azure RTO jest dostępny bezpłatnie do pobrania dla urządzeń. Azure Security Center dla usługi IoT w chmurze jest dostępna z 30-dniową wersją próbną subskrypcji platformy Azure. Pobierz [Azure Security Center do modułu zabezpieczeń IoT dla usługi Azure RTO](https://github.com/azure-rtos/iot-security-module-preview) , aby rozpocząć pracę. 


## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono informacje o Azure Security Center pomocy technicznej usługi IoT Azure RTO. Aby dowiedzieć się, jak rozpocząć i włączyć rozwiązanie zabezpieczeń w IoT Hub, zobacz następujące artykuły:

- [Wymagania wstępne usługi](service-prerequisites.md)
- [Rozpoczęcie pracy](getting-started.md)
- [Konfigurowanie rozwiązania](quickstart-configure-your-solution.md)
- [Włącz zabezpieczenia w IoT Hub](quickstart-onboard-iot-hub.md)
- [Azure Security Center często zadawane pytania dotyczące usługi IoT](resources-frequently-asked-questions.md)
- [Azure Security Center for IoT security alerts (Alerty zabezpieczeń usługi Azure Security Center dla IoT)](concept-security-alerts.md)
