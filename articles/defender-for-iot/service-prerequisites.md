---
title: Składniki & wymagania wstępne
description: Szczegóły dotyczące wszystkiego, czego potrzebujesz, aby rozpocząć pracę z usługą Azure Defender pod kątem wymagań wstępnych usługi IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2020
ms.author: rkarlin
ms.custom: references_regions
ms.openlocfilehash: b0913dc48f807c26396a38e31d293877b4561b7d
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92089183"
---
# <a name="azure-defender-for-iot-prerequisites"></a>Wymagania wstępne dotyczące usługi Azure Defender dla IoT

W tym artykule opisano różne składniki usługi Defender for IoT, co należy zacząć i wyjaśniono podstawowe pojęcia ułatwiające zrozumienie usługi.

## <a name="minimum-requirements"></a>Minimalne wymagania

- Monitorowanie bez agentów dla IoT i niezwiązanych z urządzeniami (w oparciu o technologię CyberXą)
    - Przełączniki sieciowe obsługujące monitorowanie ruchu przez port SPAN
    - Urządzenia sprzętowe czujnika NTA, aby uzyskać więcej informacji, zobacz [Certyfikowany sprzęt](https://aka.ms/AzureDefenderforIoTBareMetalAppliance)
    - Rola **współautora** subskrypcji platformy Azure (wymagana tylko podczas dołączania w celu zdefiniowania zatwierdzonych urządzeń)
    - Rola **współautor** IoT Hub (warstwa Bezpłatna lub standardowa) (w przypadku zarządzania połączonego z chmurą)
- Zabezpieczenia zarządzanych urządzeń IoT zarządzanych za pośrednictwem usługi Azure IoT Hub
    - Rola **współautor** IoT Hub (warstwa standardowa)
    - IoT Hub: przełącznik funkcji **usługi Azure Defender for IoT** powinien być włączony
    - Obsługa modułu zabezpieczeń na poziomie urządzenia  
        - Usługa Defender dla agentów IoT obsługuje rosnącą listę urządzeń i platform, zobacz [listę obsługiwanych platform](how-to-deploy-agent.md) .


## <a name="supported-service-regions"></a>Obsługiwane regiony usługi

Aby uzyskać więcej informacji, zobacz sekcję [Obsługiwane regiony IoT Hub](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub) . 

Usługa Defender for IoT kieruje cały ruch ze wszystkich regionów europejskich do regionalnego centrum danych Europa Zachodnia i wszystkie pozostałe regiony do centralnych, regionalnych centrów danych USA.

## <a name="next-steps"></a>Następne kroki

- Przeczytaj [Omówienie](overview.md) zabezpieczeń usługi Azure IoT
- Dowiedz się [, jak włączyć usługę](quickstart-onboard-iot-hub.md)
- Przeczytaj [często zadawane pytania dotyczące usługi Defender for IoT](resources-frequently-asked-questions.md)
- Zapoznaj się z tematem jak [zrozumieć alerty usługi Defender for IoT](concept-security-alerts.md)
