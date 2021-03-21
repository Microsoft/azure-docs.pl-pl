---
title: Azure Load Balancer przestawna konfiguracja adresów IP
description: Przegląd Azure Load Balancer zmiennoprzecinkowy adres IP
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2020
ms.author: allensu
ms.openlocfilehash: 01cca2f2233ed5cdfb3003bb44c40f481bcf9bda
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94699410"
---
# <a name="azure-load-balancer-floating-ip-configuration"></a>Azure Load Balancer przestawna konfiguracja adresów IP

Moduł równoważenia obciążenia oferuje kilka możliwości dla aplikacji UDP i TCP.

## <a name="floating-ip"></a>Pływający adres IP

Niektóre scenariusze aplikacji wolą lub wymagają, aby ten sam port był używany przez wiele wystąpień aplikacji na jednej maszynie wirtualnej w puli zaplecza. Typowe przykłady ponownego użycia portów obejmują klastrowanie dla wysokiej dostępności, sieciowych urządzeń wirtualnych i udostępnianie wielu punktów końcowych protokołu TLS bez ponownego szyfrowania. Jeśli chcesz ponownie użyć portu zaplecza dla wielu reguł, musisz włączyć przestawne adresy IP w definicji reguły.

**Zmienny adres IP** to terminologia platformy Azure dla części tego, co jest znane jako bezpośrednie zwrócenie serwera (DSR). DSR składa się z dwóch części:

- Topologia przepływu
- Schemat mapowania adresów IP

Na poziomie platformy Azure Load Balancer zawsze działa w topologii przepływu DSR, niezależnie od tego, czy jest włączony swobodny adres IP. Oznacza to, że wychodząca część przepływu jest zawsze poprawnie zapisywana do przepływu bezpośrednio z powrotem do źródła.
Bez zmiennoprzecinkowych adresów IP platforma Azure udostępnia tradycyjny schemat mapowania adresów IP na potrzeby łatwego użytkowania ("adres IP wystąpień maszyn wirtualnych"). Włączenie pływającego adresu IP powoduje zmianę mapowania adresów IP na adres IP frontonu usługi równoważenia obciążenia w celu zapewnienia dodatkowej elastyczności. Dowiedz się więcej [tutaj](load-balancer-multivip-overview.md).

## <a name="limitations"></a><a name = "limitations"></a>Ograniczenia

- Zmienny adres IP nie jest obecnie obsługiwany w konfiguracjach pomocniczych adresów IP na potrzeby scenariuszy równoważenia obciążenia

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć korzystanie z Load Balancer, zobacz [tworzenie usługa Load Balancer w warstwie Standardowa publicznego](quickstart-load-balancer-standard-public-portal.md) .
- Dowiedz się więcej o [Azure Load Balancer połączenia wychodzące](load-balancer-outbound-connections.md).
- Dowiedz się więcej o [Azure Load Balancer](load-balancer-overview.md).
- Dowiedz się więcej o [sondach kondycji](load-balancer-custom-probe-overview.md).
- Dowiedz się więcej na temat [diagnostyki usługa Load Balancer w warstwie Standardowa](load-balancer-standard-diagnostics.md).
- Dowiedz się więcej na temat [sieciowych grup zabezpieczeń](../virtual-network/network-security-groups-overview.md).