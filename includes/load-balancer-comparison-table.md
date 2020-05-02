---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 02/21/2020
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: 33e6fecafa2b9748a27794800365974ebc975d0e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78202482"
---
| | usługa Load Balancer w warstwie Standardowa | Load Balancer podstawowa |
| --- | --- | --- |
| [Rozmiar puli zaplecza](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer) | Obsługuje do 1000 wystąpień. | Obsługuje do 300 wystąpień. |
| Punkty końcowe puli zaplecza | Wszystkie maszyny wirtualne lub zestawy skalowania maszyn wirtualnych w jednej sieci wirtualnej. | Maszyny wirtualne w jednym zestawie dostępności lub zestaw skalowania maszyn wirtualnych. |
| [Sondy kondycji](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Zachowanie sondy kondycji podczas niepowodzenia](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | Połączenia TCP pozostają aktywne na sondie wystąpienia __, a__ wszystkie sondy w dół. | Połączenia TCP pozostają aktywne na sondie wystąpienia. Wszystkie połączenia TCP kończą się, gdy wszystkie sondy są wyłączone. |
| Strefy dostępności | Strefowo nadmiarowe i strefowe frontony dla ruchu przychodzącego i wychodzącego. | Niedostępne |
| Diagnostyka | [Azure Monitor metryki wielowymiarowych](../articles/load-balancer/load-balancer-standard-diagnostics.md) | [Dzienniki usługi Azure Monitor](../articles/load-balancer/load-balancer-monitor-log.md) |
| Porty wysokiej dostępności | [Dostępne dla Load Balancer wewnętrznej](../articles/load-balancer/load-balancer-ha-ports-overview.md) | Niedostępne |
| Zabezpieczenie domyślne | Zamknięty do przepływów przychodzących, chyba że jest to dozwolone przez grupę zabezpieczeń sieci. Należy pamiętać, że ruch wewnętrzny z sieci wirtualnej do wewnętrznego modułu równoważenia obciążenia jest dozwolony. | Otwórz domyślnie. Opcjonalna Grupa zabezpieczeń sieci. |
| Reguły ruchu wychodzącego | [Deklaratywna konfiguracja wychodzącego NAT](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | Niedostępne |
| Resetowanie protokołu TCP w wyniku bezczynności | [Dostępne dla każdej reguły](../articles/load-balancer/load-balancer-tcp-reset.md) | Niedostępne |
| [Wiele frontonów](../articles/load-balancer/load-balancer-multivip-overview.md) | Ruch przychodzący i [wychodzący](../articles/load-balancer/load-balancer-outbound-connections.md) | Tylko ruch przychodzący |
| Operacje zarządzania | Większość operacji < 30 sekund | typowe 60 – 90 sekund |
| Umowa SLA | [99.99%](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/) | Niedostępne | 
