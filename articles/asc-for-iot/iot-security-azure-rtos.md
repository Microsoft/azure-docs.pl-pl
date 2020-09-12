---
title: Moduł zabezpieczeń dla usługi Azure RTO — Omówienie
description: Dowiedz się więcej na temat modułu zabezpieczeń na potrzeby obsługi i implementacji usługi Azure RTO w ramach Azure Security Center usługi IoT.
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
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: bcf98f503b0f0c8bf76435d9884297d5f53d65e6
ms.sourcegitcommit: 7f62a228b1eeab399d5a300ddb5305f09b80ee14
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2020
ms.locfileid: "89514479"
---
# <a name="overview-security-module-for-azure-rtos-preview"></a>Przegląd: moduł zabezpieczeń dla usługi Azure RTO (wersja zapoznawcza)

Azure Security Center dla modułu zabezpieczeń IoT RTO to kompleksowe rozwiązanie zabezpieczeń dla urządzeń RTO platformy Azure. Usługa Azure RTO jest teraz dostarczana z modułem zabezpieczeń usługi Azure IoT i zawiera pokrycie typowych zagrożeń i potencjalnie złośliwych działań na urządzeniach z systemem operacyjnym w czasie rzeczywistym. 

![Azure Security Center usługi IoT Azure RTO](./media/architecture/azure-rtos-security-monitoring.png)


Moduł zabezpieczeń usługi Azure RTO oferuje następujące funkcje: 
- Wykrycie złośliwego działania sieci
- Niestandardowe alerty na podstawie, zachowanie urządzenia określania poziomu odniesienia
- Poprawa higieny zabezpieczeń urządzeń

### <a name="detection-of-malicious-network-activities"></a>Wykrywanie złośliwych działań w sieci

Monitorowane jest aktywność sieci przychodzących i wychodzących poszczególnych urządzeń (obsługiwane protokoły: TCP, UDP, ICMP w protokołach IPv4 i IPv6). Azure Security Center usługi IoT sprawdza wszystkie te działania w sieci w odniesieniu do źródła danych analizy zagrożeń firmy Microsoft. Kanał informacyjny jest aktualizowany w czasie rzeczywistym przy użyciu milionów unikatowych wskaźników zagrożeń zebranych na całym świecie. 

### <a name="device-behavior-baselining-based-on-custom-alerts"></a>Zachowanie urządzenia określania poziomu odniesienia na podstawie alertów niestandardowych

Określania poziomu odniesienia umożliwia klastrowanie urządzeń w grupach zabezpieczeń i Definiowanie oczekiwanych zachowań poszczególnych grup. Ponieważ urządzenia IoT są zwykle zaprojektowane do działania w dobrze zdefiniowanych i ograniczonych scenariuszach, można łatwo utworzyć linię bazową, która definiuje ich oczekiwane zachowanie przy użyciu zestawu parametrów. Wszelkie odchylenia od linii bazowej wyzwala alert. 

### <a name="improve-your-device-security-hygiene"></a>Ulepszanie higieny bezpieczeństwa urządzeń

Korzystając z zalecanej Azure Security Center infrastruktury dla IoT, uzyskasz wiedzę i wgląd w informacje o problemach w Twoim środowisku, które wpływają i uszkadzają stan zabezpieczeń urządzeń. Słabe stan zabezpieczeń urządzeń IoT mogą pozwolić na pomyślne ataki w przypadku pomyślnego powolnej ochrony, ponieważ zabezpieczenia są zawsze mierzone przez najsłabsze łącze w każdej organizacji. 

## <a name="get-started-protecting-azure-rtos-devices"></a>Wprowadzenie do ochrony urządzeń z usługą Azure RTO

Moduł zabezpieczeń dla usługi Azure RTO jest udostępniany bezpłatnie do pobrania dla urządzeń. Azure Security Center dla usługi IoT w chmurze jest dostępna z 30-dniową wersją próbną subskrypcji platformy Azure. Pobierz [moduł zabezpieczeń dla usługi Azure RTO](https://github.com/azure-rtos/iot-security-module-preview) , aby rozpocząć pracę. 


## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera informacje o module zabezpieczeń usługi Azure RTO. Aby dowiedzieć się więcej na temat modułu zabezpieczeń i rozpocząć pracę, zobacz następujące artykuły:

- [Pojęcia dotyczące modułu zabezpieczeń usługi Azure RTO IoT](concept-rtos-security-module.md)
- [Szybki Start: Azure RTO IoT Security module](quickstart-azure-rtos-security-module.md)


