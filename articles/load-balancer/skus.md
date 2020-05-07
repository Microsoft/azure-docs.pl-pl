---
title: Jednostki SKU Azure Load Balancer
description: Przegląd Azure Load Balancer jednostek SKU
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/01/2020
ms.author: allensu
ms.openlocfilehash: c7ca630b4a6a1bedeab21feacc22cd27a1a3ee7e
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82794154"
---
# <a name="azure-load-balancer-skus"></a>Jednostki SKU Azure Load Balancer

Azure Load Balancer ma dwie wersje lub jednostki SKU.

## <a name="sku-comparison"></a><a name="skus"></a>Porównanie jednostek SKU

Moduł równoważenia obciążenia obsługuje podstawowe i standardowe jednostki SKU. Te jednostki SKU różnią się w zależności od skali, funkcji i cen. Każdy scenariusz, który jest możliwy w przypadku podstawowego modułu równoważenia obciążenia, można utworzyć za pomocą usługi równoważenia obciążenia w warstwie Standardowa.

Aby porównać i poznać różnice, zapoznaj się z poniższą tabelą. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure usługa Load Balancer w warstwie Standardowa](load-balancer-standard-overview.md).

>[!NOTE]
> Firma Microsoft zaleca użycie usługi równoważenia obciążenia w warstwie Standardowa.
Autonomiczne maszyny wirtualne, zestawy dostępności i zestawy skalowania maszyn wirtualnych można połączyć tylko jednej jednostki SKU — nigdy do obu. Moduł równoważenia obciążenia i jednostka SKU publicznego adresu IP muszą być zgodne, gdy są używane z publicznymi adresami IP. Usługa równoważenia obciążenia i jednostki SKU publicznego adresu IP nie są modyfikowalne.

| | usługa Load Balancer w warstwie Standardowa | Load Balancer podstawowa |
| --- | --- | --- |
| [Rozmiar puli zaplecza](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer) | Obsługuje do 1000 wystąpień. | Obsługuje do 300 wystąpień. |
| Punkty końcowe puli zaplecza | Wszystkie maszyny wirtualne lub zestawy skalowania maszyn wirtualnych w jednej sieci wirtualnej. | Maszyny wirtualne w jednym zestawie dostępności lub zestaw skalowania maszyn wirtualnych. |
| [Sondy kondycji](./load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Zachowanie sondy kondycji podczas niepowodzenia](./load-balancer-custom-probe-overview.md#probedown) | Połączenia TCP pozostają aktywne na sondie wystąpienia __, a__ wszystkie sondy w dół. | Połączenia TCP pozostają aktywne na sondie wystąpienia. Wszystkie połączenia TCP kończą się, gdy wszystkie sondy są wyłączone. |
| Strefy dostępności | Strefowo nadmiarowe i strefowe frontony dla ruchu przychodzącego i wychodzącego. | Niedostępne |
| Diagnostyka | [Azure Monitor metryki wielowymiarowych](./load-balancer-standard-diagnostics.md) | [Dzienniki usługi Azure Monitor](./load-balancer-monitor-log.md) |
| Porty wysokiej dostępności | [Dostępne dla Load Balancer wewnętrznej](./load-balancer-ha-ports-overview.md) | Niedostępne |
| Zabezpieczenie domyślne | Zamknięty do przepływów przychodzących, chyba że jest to dozwolone przez grupę zabezpieczeń sieci. Należy pamiętać, że ruch wewnętrzny z sieci wirtualnej do wewnętrznego modułu równoważenia obciążenia jest dozwolony. | Otwórz domyślnie. Opcjonalna Grupa zabezpieczeń sieci. |
| Reguły ruchu wychodzącego | [Deklaratywna konfiguracja wychodzącego NAT](./load-balancer-outbound-rules-overview.md) | Niedostępne |
| Resetowanie protokołu TCP w wyniku bezczynności | [Dostępne dla każdej reguły](./load-balancer-tcp-reset.md) | Niedostępne |
| [Wiele frontonów](./load-balancer-multivip-overview.md) | Ruch przychodzący i [wychodzący](./load-balancer-outbound-connections.md) | Tylko ruch przychodzący |
| Operacje zarządzania | Większość operacji < 30 sekund | typowe 60 – 90 sekund |
| Umowa SLA | [99.99%](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/) | Niedostępne | 

Aby uzyskać więcej informacji, zobacz [limity usługi równoważenia obciążenia](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer). Szczegółowe informacje dotyczące modułu równoważenia obciążenia w warstwie Standardowa zawiera [przegląd](load-balancer-standard-overview.md), [cennik](https://aka.ms/lbpricing) i [umowa dotycząca poziomu usług](https://aka.ms/lbsla).

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć korzystanie z Load Balancer, zobacz [tworzenie usługa Load Balancer w warstwie Standardowa publicznego](quickstart-load-balancer-standard-public-portal.md) .
- Dowiedz się więcej o korzystaniu z [Usługa Load Balancer w warstwie Standardowa i strefy dostępności](load-balancer-standard-availability-zones.md).
- Dowiedz się więcej o [sondach kondycji](load-balancer-custom-probe-overview.md).
- Dowiedz się więcej na temat korzystania z [Load Balancer dla połączeń wychodzących](load-balancer-outbound-connections.md).
- Dowiedz się więcej o [Usługa Load Balancer w warstwie Standardowa z regułami równoważenia obciążenia dla portów ha](load-balancer-ha-ports-overview.md).
- Dowiedz się więcej na temat [sieciowych grup zabezpieczeń](../virtual-network/security-overview.md).
