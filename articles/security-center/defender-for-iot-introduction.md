---
title: Usługa Azure Defender dla IoT
description: Informacje o usłudze Azure Defender dla IoT
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 2f41aac085fef62e0356a93a07823f21d7ba3667
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91448343"
---
# <a name="introduction-to-azure-defender-for-iot"></a>Wprowadzenie do usługi Azure Defender dla IoT

Ujednolicenie zarządzania zabezpieczeniami i umożliwienie kompleksowego wykrywania zagrożeń i analizy w ramach obciążeń chmury hybrydowej i rozwiązania Azure IoT.

Usługa Azure Defender dla IoT oferuje następujące informacje:

- **Odnajdowanie zasobów i mapowanie sieci**, w tym szczegóły dotyczące producenta urządzenia, typu urządzenia i sposobu komunikacji urządzeń w sieci
- **Zarządzanie lukami w zabezpieczeniach**, w tym informacje na temat CVEs, otwartych portów i nieautoryzowanych połączeń z Internetem
- **Ciągłe monitorowanie zagrożeń**przy użyciu alertów w czasie rzeczywistym wskazujących nietypowe lub nieautoryzowane działania, takie jak ataki skierowane lub złośliwe oprogramowanie

Pełne szczegóły są dostępne w [dedykowanej dokumentacji](https://docs.microsoft.com/azure/asc-for-iot/overview).

## <a name="availability"></a>Dostępność
|Aspekt|Szczegóły|
|----|:----|
|Stan wydania:|Ogólnie dostępna (GA)|
|Wpisaną|Wymaga [usługi Azure Defender](security-center-pricing.md)|
|Wymagane role i uprawnienia:|Uprawnienia do zapisu na sieciowych grup zabezpieczeń komputera|
|Połączeń|![Tak](./media/icons/yes-icon.png) Chmury komercyjne<br>![Tak](./media/icons/yes-icon.png) National/suwerenne (US Gov, Chiny gov, inne gov)|
|||

## <a name="what-devices-can-azure-defender-for-iot-secure"></a>Jakie urządzenia mogą być bezpieczne w usłudze Azure Defender?
Łącząc możliwości technologii Security Center, Azure Defender i CyberX bez agenta, można zabezpieczyć:

- **Urządzenia Greenfield** połączone za pośrednictwem IoT Hub. Dzięki temu organizacje mogą przyspieszyć swoje inicjatywy IoT, zabezpieczając nowoczesne urządzenia z możliwością obsługi Internetu oraz tradycyjne urządzenia usługi ICS/SCADA za pomocą jednego ujednoliconego rozwiązania.
    - Dołączaj nowe urządzenia i stosuj zasady zabezpieczeń w ramach obciążeń (urządzeń typu liść, urządzeń Microsoft Edge, IoT Hub), aby zapewnić zgodność ze standardami zabezpieczeń i zwiększyć bezpieczeństwo stan.

- **Niezarządzane urządzenia brownfield** używane w środowiskach technologii operacyjnych (OT), takich jak produkcja, systemy zarządzania budowaniem (MS), nauki przyrodnicze, narzędzia energetyczne i wodne, olej & i logistyka. 
    - W celu ochrony urządzeń niezarządzanych usługa Azure Defender for IoT korzysta z czujnika lokalnego do pasywnej, nieinwazyjnej analizy ruchu sieciowego (NTA), która ma negatywny wpływ na wydajność w środowiskach niezwiązanych z wydajnością. 
    - Zawiera on również szczegółowe informacje o wyspecjalizowanych protokołach IoT/OT — w połączeniu z opatentowaną analizą behawioralną i obsługą uczenia maszynowego, dzięki czemu można wyeliminować konieczność konfigurowania wszelkich reguł lub podpisów, co w ciągu kilku minut, a nie dni lub tygodni. 


## <a name="azure-defender-for-iot-integration-with-azure-sentinel"></a>Integracja usługi Azure Defender for IoT z platformą Azure — wskaźnik
Aby włączyć ujednolicone monitorowanie i zarządzanie zabezpieczeniami, usługa Azure Defender dla IoT natywnie integruje się z [platformą Azure — wskaźnikiem](../sentinel/overview.md).

Zespoły SecOP mogą:

- Błyskawiczne wykrywanie i reagowanie na zagrożenia IoT/OT przy użyciu odrębnej o elementy PlayBook dołączonej do
- Skorzystaj z stale aktualizowanej analizy zagrożeń IoT/niespecyficznej dla Internetu, dostarczonej w sekcji 52, zespół ds. platformy IoT firmy Microsoft w firmie
- Integruj usługę Azure Defender for IoT z istniejącymi przepływami pracy SOC i narzędziami zabezpieczeń innych firm, takimi jak Splunk, IBM QRadar i usługi ServiceNow


## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono informacje o usłudze Azure Defender for IoT w Azure Security Center. Aby uzyskać więcej informacji, zobacz:

- [Wprowadzenie do usługi Azure Security Center dla IoT](../asc-for-iot/overview.md)
