---
title: Moduł zabezpieczeń dla usługi Azure RTO — Omówienie
description: Dowiedz się więcej na temat modułu zabezpieczeń na potrzeby obsługi i implementacji usługi Azure RTO w ramach usługi Defender for IoT
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: 22bd12bbdcccef2fd0e9010f926cd18e95d42967
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91761859"
---
# <a name="overview-defender-for-iot-security-module-for-azure-rtos-preview"></a>Przegląd: usługa Defender for IoT Security module dla usługi Azure RTO (wersja zapoznawcza)

Usługa Defender for IoT Security module dla usługi RTO oferuje kompleksowe rozwiązanie zabezpieczeń dla urządzeń z systemem Azure RTO. Usługa Azure RTO jest teraz dostarczana z modułem zabezpieczeń usługi Azure IoT i zawiera pokrycie typowych zagrożeń i potencjalnie złośliwych działań na urządzeniach z systemem operacyjnym w czasie rzeczywistym. 

![Usługa Defender dla usługi IoT Azure RTO](./media/architecture/azure-rtos-security-monitoring.png)


Moduł zabezpieczeń usługi Azure RTO oferuje następujące funkcje: 
- Wykrycie złośliwego działania sieci
- Niestandardowe alerty na podstawie, zachowanie urządzenia określania poziomu odniesienia
- Poprawa higieny zabezpieczeń urządzeń

## <a name="detection-of-malicious-network-activities"></a>Wykrywanie złośliwych działań w sieci

Monitorowane jest aktywność sieci przychodzących i wychodzących poszczególnych urządzeń (obsługiwane protokoły: TCP, UDP, ICMP w protokołach IPv4 i IPv6). Usługa Defender for IoT sprawdza wszystkie te działania w sieci w odniesieniu do źródła danych analizy zagrożeń firmy Microsoft. Kanał informacyjny jest aktualizowany w czasie rzeczywistym przy użyciu milionów unikatowych wskaźników zagrożeń zebranych na całym świecie. 

## <a name="device-behavior-baselining-based-on-custom-alerts"></a>Zachowanie urządzenia określania poziomu odniesienia na podstawie alertów niestandardowych

Określania poziomu odniesienia umożliwia klastrowanie urządzeń w grupach zabezpieczeń i Definiowanie oczekiwanych zachowań poszczególnych grup. Ponieważ urządzenia IoT są zwykle zaprojektowane do działania w dobrze zdefiniowanych i ograniczonych scenariuszach, można łatwo utworzyć linię bazową, która definiuje ich oczekiwane zachowanie przy użyciu zestawu parametrów. Wszelkie odchylenia od linii bazowej wyzwala alert. 

## <a name="improve-your-device-security-hygiene"></a>Ulepszanie higieny bezpieczeństwa urządzeń

Dzięki zastosowaniu zalecanej usługi Defender for IoT można uzyskać wiedzę i wgląd w informacje o problemach w Twoim środowisku, które wpływają i uszkadzają stan zabezpieczeń urządzeń. Słabe stan zabezpieczeń urządzeń IoT mogą pozwolić na pomyślne ataki w przypadku pomyślnego powolnej ochrony, ponieważ zabezpieczenia są zawsze mierzone przez najsłabsze łącze w każdej organizacji. 

## <a name="get-started-protecting-azure-rtos-devices"></a>Wprowadzenie do ochrony urządzeń z usługą Azure RTO

Moduł zabezpieczeń dla usługi Azure RTO jest udostępniany bezpłatnie do pobrania dla urządzeń. Usługa Defender for IoT w chmurze jest dostępna z 30-dniową wersją próbną dla każdej subskrypcji platformy Azure. Pobierz [moduł zabezpieczeń dla usługi Azure RTO](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/defender-for-iot/iot-security-azure-rtos.md) , aby rozpocząć pracę. 


## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera informacje o module zabezpieczeń usługi Azure RTO. Aby dowiedzieć się więcej na temat modułu zabezpieczeń i rozpocząć pracę, zobacz następujące artykuły:

- [Pojęcia dotyczące modułu zabezpieczeń usługi Azure RTO IoT](concept-rtos-security-module.md)
- [Szybki Start: Azure RTO IoT Security module](quickstart-azure-rtos-security-module.md)
