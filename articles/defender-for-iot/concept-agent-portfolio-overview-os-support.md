---
title: Omówienie portfolio agentów i obsługa systemu operacyjnego (wersja zapoznawcza)
description: Usługa Azure Defender for IoT oferuje duży portfolio agentów na podstawie typu urządzenia.
ms.date: 1/20/2021
ms.topic: quickstart
ms.openlocfilehash: 6c65ad33439002bd651c4c926b6452868f6137ba
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784768"
---
# <a name="agent-portfolio-overview-and-os-support-preview"></a>Omówienie portfolio agentów i obsługa systemu operacyjnego (wersja zapoznawcza)

Usługa Azure Defender for IoT oferuje duży portfolio agentów na podstawie typu urządzenia. 

## <a name="standalone-agent"></a>Autonomiczny Agent

Agent autonomiczny obejmuje większość systemów operacyjnych Linux, które można wdrożyć jako pakiet binarny lub jako kod źródłowy, który można włączyć jako część oprogramowania układowego i zezwolić na modyfikację i dostosowanie w zależności od potrzeb klientów. Przykład obsługi systemu operacyjnego: 

| System operacyjny | Procesor | ARM32v7 |
|--|--|--|
| Debian 9 | ✓ | ✓ |
| Ubuntu 18.04 | ✓ |  |
| Ubuntu 20.04 | ✓ |  |

Aby uzyskać szczegółowe informacje, obsługę systemu operacyjnego lub zażądać dostępu do kodu źródłowego, aby można było je dołączyć do oprogramowania układowego urządzenia, skontaktuj się z kierownikiem ds. klientów lub Wyślij wiadomość e-mail na adres <defender_micro_agent@microsoft.com> . 

## <a name="azure-rtos-micro-agent"></a>Usługa Azure RTO Micro Agent

Usługa Azure Defender dla programu IoT Micro Agent oferuje kompleksowe i lekkie rozwiązanie zabezpieczeń dla urządzeń korzystających z usługi Azure RTO. Usługa Azure Defender dla programu IoT Micro Agent zapewnia ochronę typowych zagrożeń oraz potencjalne złośliwe działania na urządzeniach z systemem operacyjnym (RTO) w czasie rzeczywistym. Micro Agent jest wbudowany jako część składnika Azure RTO NetX Duo i monitoruje aktywność sieci urządzenia. 

Usługa Azure Defender for IoT Micro jest wbudowana w ramach składnika Azure RTO NetX Duo i monitoruje aktywność sieciową urządzenia. Micro Agent składa się z kompleksowego i lekkiego rozwiązania zabezpieczeń, które zapewnia pokrycie typowych zagrożeń i potencjalnie złośliwych działań na urządzeniach z systemem operacyjnym (RTO) w czasie rzeczywistym.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [autonomicznym omówieniu Micro Agent (wersja zapoznawcza)](concept-standalone-micro-agent-overview.md).