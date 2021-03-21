---
title: Odzyskiwanie po awarii geograficznej w Azure Event Grid | Microsoft Docs
description: W tym artykule opisano, jak Azure Event Grid automatycznie obsługiwać odzyskiwanie geograficzne (GeoDR).
ms.topic: conceptual
ms.date: 11/19/2020
ms.openlocfilehash: 10beaf0ae25f3ed9b7bcda5961a89494b18b84d9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94980856"
---
# <a name="server-side-geo-disaster-recovery-in-azure-event-grid"></a>Odzyskiwanie po awarii geograficznej po stronie serwera w Azure Event Grid
Event Grid teraz ma automatyczne odzyskiwanie po awarii geograficznej (GeoDR) metadanych nie tylko dla nowych, ale wszystkie istniejące domeny, tematy i subskrypcje zdarzeń. W przypadku przekroczenia całego regionu platformy Azure Event Grid wszystkie metadane infrastruktury powiązane ze zdarzeniami są synchronizowane z sparowanym regionem. Twoje nowe zdarzenia zostaną ponownie przepływane bez interwencji użytkownika. 

Odzyskiwanie po awarii jest mierzone przy użyciu dwóch metryk:

- Cel punktu odzyskiwania (RPO): minuty lub godziny danych, które mogą zostać utracone.
- Cel czasu odzyskiwania (RTO): minuty lub godziny, w których usługa może być wyłączona.

Automatyczna tryb failover Event Grid ma różne RPO i RTO dla metadanych (subskrypcje zdarzeń itp.) oraz dane (zdarzenia). Jeśli wymagana jest inna Specyfikacja od następujących, można nadal zaimplementować swój wewnętrzny [tryb failover po stronie klienta za pomocą interfejsów API kondycji tematu](custom-disaster-recovery.md).

## <a name="recovery-point-objective-rpo"></a>Cel punktu odzyskiwania (recovery point objective, RPO)
- **Cel RPO**: zero minut. Za każdym razem, gdy zasób jest tworzony w Event Grid, jest on natychmiast replikowany między regionami. Gdy nastąpi przejście w tryb failover, żadne metadane nie są tracone.
- **Cel punktu odzyskiwania danych**: Jeśli system jest w dobrej kondycji i przechwycony w istniejącym ruchu w czasie regionalnego trybu failover, cel punktu odzyskiwania dla zdarzeń wynosi około 5 minut.

## <a name="recovery-time-objective-rto"></a>Cel czasu odzyskiwania (recovery time objective, RTO)
- **Metadata RTO**: Chociaż zwykle odbywa się to znacznie szybciej, w ciągu 60 minut Event Grid rozpocznie akceptowanie wywołań tworzenia/aktualizowania/usuwania dla tematów i subskrypcji.
- **RTO danych**: takie jak metadane, zwykle odbywa się znacznie szybciej, jednak w ciągu 60 minut Event Grid zacznie akceptować nowy ruch po regionalnym przejściu w tryb failover.

> [!NOTE]
> Koszt usługi Metadata GeoDR w Event Grid to: $0.


## <a name="next-steps"></a>Następne kroki
Jeśli chcesz zaimplementować własną logikę trybu failover po stronie klienta, zobacz [# Build własne odzyskiwanie po awarii dla tematów niestandardowych w Event Grid](custom-disaster-recovery.md)
