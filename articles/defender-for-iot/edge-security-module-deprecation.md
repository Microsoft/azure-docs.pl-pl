---
title: Obsługa i wycofanie funkcji
titleSuffix: Azure Defender for IoT
description: Usługa Defender for IoT będzie w dalszym ciągu obsługiwać język C, C# i Edge do 1 marca 2022.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/21/2021
ms.service: azure
ms.topic: how-to
ms.openlocfilehash: a33e7ff230292d4ceb14610e3cf00935dc1a67a0
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103493173"
---
# <a name="feature-support-and-retirement"></a>Obsługa i wycofanie funkcji

W tym artykule opisano funkcje usługi Azure Defender for IoT oraz obsługę różnych funkcji w usłudze Defender for IoT.

## <a name="defender-for-iot-c-c-and-edge-defender-iot-micro-agent-deprecation"></a>Usługa Defender dla IoT C, C# i Edge Defender-IoT-Micro-Agent

Nowy Micro Agent zastępuje bieżące C, C# i Edge Defender-IoT-Micro-Agent.  

Nowy Micro Agent jest oparty na wiedzy oraz o doświadczeniu zebranym w ramach rozwoju oprogramowania Defender-IoT-Micro-Agent, klientów i partnerów z uwzględnieniem czterech ważnych ulepszeń: 

- **Głębokość — wartość zabezpieczeń**: nowy Agent zostanie uruchomiony na poziomie hosta, co zapewni większą widoczność podstawowych operacji urządzenia i pozwala na lepsze pokrycie zabezpieczeń.

- **Ulepszona wydajność i zmniejszanie wydajności urządzeń**: osiągane przez małą pamięć RAM i zużywaną pamięć ROM oraz niskie użycie procesora CPU.  

- **Plug and Play**: nowy Micro Agent nie ma już żadnych zależności na poziomie jądra i wszystkie jego zależności oprogramowania są dostarczane jako część pakietu. Micro Agent obsługuje typową architekturę procesora CPU.

- **Łatwa do wdrożenia**: Micro Agent obsługuje różne modele dystrybucji, za pomocą kodu źródłowego i jako pakiet binarny. 

### <a name="timeline"></a>Oś czasu 

Usługa Defender for IoT będzie w dalszym ciągu obsługiwać język C, C# i Edge do 1 marca 2022. 

## <a name="micro-agent-preview-support"></a>Obsługa wersji zapoznawczej Micro Agent

W trakcie korzystania z wersji zapoznawczej Micro Agent może napotkać istotne zmiany bez powiadomienia.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z tematem [interfejsy API usługi Defender dla czujnika IoT i konsoli zarządzania](references-work-with-defender-for-iot-apis.md).