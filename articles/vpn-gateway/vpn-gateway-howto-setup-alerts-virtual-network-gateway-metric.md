---
title: Konfigurowanie alertów w usłudze Azure VPN Gateway metryki
description: Kroki konfigurowania alertów na VPN Gateway metryki
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: 01704030ee3142cac9a328e1a3edc024651919a0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82127905"
---
# <a name="set-up-alerts-on-vpn-gateway-metrics"></a>Konfigurowanie alertów dotyczących metryk VPN Gateway

Ten artykuł ułatwia Konfigurowanie alertów dotyczących usługi Azure VPN Gateway Metrics. Azure Monitor umożliwia Konfigurowanie alertów dla zasobów platformy Azure. Można skonfigurować alerty dla bram sieci wirtualnej typu "VPN".


|**Metryka**   | **Jednostka** | **Poziom szczegółowości** | **Opis** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| Bajty/s  | 5 minut| Średnie wykorzystanie łącznej przepustowości wszystkich połączeń między lokacjami w bramie.     |
|**P2SBandwidth**| Bajty/s  | 1 min  | Średnie wykorzystanie przepustowości połączonej wszystkich połączeń punkt-lokacja na bramie.    |
|**P2SConnectionCount**| Liczba  | 1 min  | Liczba połączeń punkt-lokacja na bramie.   |
|**TunnelAverageBandwidth** | Bajty/s    | 5 minut  | Średnie wykorzystanie przepustowości przez tunele utworzone na bramie. |
|**TunnelEgressBytes** | Bajty | 5 minut | Ruch wychodzący w tunelach utworzonych na bramie.   |
|**TunnelEgressPackets** | Liczba | 5 minut | Liczba wychodzących pakietów w tunelach utworzonych na bramie.   |
|**TunnelEgressPacketDropTSMismatch** | Liczba | 5 minut | Liczba pakietów wychodzących usuniętych w tunelach spowodowanych przez niezgodność selektora ruchu. |
|**TunnelIngressBytes** | Bajty | 5 minut | Ruch przychodzący w tunelach utworzonych na bramie.   |
|**TunnelIngressPackets** | Liczba | 5 minut | Liczba pakietów przychodzących w tunelach utworzonych na bramie.   |
|**TunnelIngressPacketDropTSMismatch** | Liczba | 5 minut | Liczba pakietów przychodzących porzuconych na tunelach spowodowanych przez niezgodność selektora ruchu. |


## <a name="set-up-azure-monitor-alerts-based-on-metrics-by-using-the-azure-portal"></a><a name="setup"></a>Konfigurowanie alertów Azure Monitor w oparciu o metryki przy użyciu Azure Portal

Poniższe przykładowe kroki spowodują utworzenie alertu na bramie dla:

- **Metryka:** TunnelAverageBandwidth
- **Warunek:** Przepustowość > 10 bajtów/s
- **Okno:** 5 minut
- **Akcja alertu:** Adres e-mail



1. Przejdź do zasobu bramy sieci wirtualnej i wybierz pozycję **alerty** na karcie **monitorowanie** . Następnie utwórz nową regułę alertu lub edytuj istniejącą regułę alertu.

   ![Wybory dotyczące tworzenia reguły alertu](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "Utwórz")

2. Wybierz bramę sieci VPN jako zasób.

   ![Przycisk Wybierz i Brama sieci VPN na liście zasobów](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "Wybierz")

3. Wybierz metrykę, która ma zostać skonfigurowana dla alertu.

   ![Wybrana Metryka na liście metryk](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "Wybierz")
4. Skonfiguruj logikę sygnału. Istnieją trzy składniki:

    a. **Wymiary**: Jeśli Metryka ma wymiary, można wybrać określone wartości wymiaru, aby alert obliczał tylko dane tego wymiaru. Są one opcjonalne.

    b. **Warunek**: jest to operacja szacowania wartości metryki.

    c. **Czas**: Określ stopień szczegółowości danych metryki oraz okres, w którym ma zostać obliczony alert.

   ![Szczegóły dotyczące konfigurowania logiki sygnałów](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "Wybierz")

5. Aby wyświetlić skonfigurowane reguły, wybierz pozycję **Zarządzaj regułami alertów**.

   ![Przycisk służący do zarządzania regułami alertów](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "Wybierz")

## <a name="next-steps"></a>Następne kroki

Aby skonfigurować alerty dotyczące dzienników zasobów tunelu, zobacz [Konfigurowanie alertów dotyczących dzienników zasobów VPN Gateway](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).
