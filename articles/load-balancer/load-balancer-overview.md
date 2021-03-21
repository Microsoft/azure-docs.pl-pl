---
title: Co to jest usługa Azure Load Balancer?
titleSuffix: Azure Load Balancer
description: Omówienie funkcji, architektury i implementacji usługi Azure Load Balancer. Dowiedz się, jak działa Load Balancer i jak korzystać z niego w chmurze.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/25/2021
ms.author: allensu
ms.openlocfilehash: 51ceb72d53f78264edcadd2255e20c8fbdac2cae
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102181214"
---
# <a name="what-is-azure-load-balancer"></a>Co to jest usługa Azure Load Balancer?

*Równoważenie obciążenia* polega na równomiernym rozłożeniu obciążenia (ruch sieciowy przychodzący) między grupą zasobów lub serwerów zaplecza. 

Azure Load Balancer działa w warstwie 4 modelu połączeń systemów otwartych (OSI). Jest to pojedynczy punkt kontaktu dla klientów. Moduł równoważenia obciążenia dystrybuuje przepływy przychodzące, które docierają do frontonu modułu równoważenia obciążenia z wystąpieniami puli zaplecza. Te przepływy są zgodne ze skonfigurowanymi regułami równoważenia obciążenia i sondami kondycji. Wystąpienia puli zaplecza mogą być Virtual Machinesami platformy Azure lub wystąpieniami w zestawie skalowania maszyn wirtualnych.

**[Publiczny moduł równoważenia obciążenia](./components.md#frontend-ip-configurations)** może zapewnić połączenia wychodzące maszyn wirtualnych w sieci wirtualnej. Te połączenia są realizowane przez przetłumaczenie prywatnych adresów IP na publiczne adresy IP. Publiczne usługi równoważenia obciążenia są używane do równoważenia obciążenia ruchu internetowego na maszynach wirtualnych.

**[Wewnętrzny (lub prywatny) moduł równoważenia obciążenia](./components.md#frontend-ip-configurations)** jest używany w przypadku, gdy prywatne adresy IP są używane tylko na frontonie. Wewnętrzne moduły równoważenia obciążenia są używane do równoważenia obciążenia ruchu w sieci wirtualnej. Dostęp do frontonu modułu równoważenia obciążenia można uzyskać z sieci lokalnej w scenariuszu hybrydowym.

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer.svg" alt="Figure depicts both public and internal load balancers directing traffic to port 80 on multiple servers on a Web tier and port 443 on multiple servers on a business tier." width="512" title="Azure Load Balancer">
</p>

*Ilustracja: Równoważenie aplikacji wielowarstwowych przy użyciu Load Balancer publicznego i wewnętrznego*

Aby uzyskać więcej informacji na temat poszczególnych składników usługi równoważenia obciążenia, zobacz [Azure Load Balancer Components](./components.md).

>[!NOTE]
> Platforma Azure udostępnia zestaw w pełni zarządzanych rozwiązań do równoważenia obciążenia dla Twoich scenariuszy. 
> * Jeśli chcesz korzystać z globalnego routingu opartego na systemie DNS i **nie** ma wymagań dotyczących zakończenia protokołu Transport Layer Security (TLS) ("odciążanie protokołu SSL"), żądania dla protokołu HTTP/HTTPS lub przetwarzania warstwy aplikacji, zapoznaj się z [Traffic Manager](../traffic-manager/traffic-manager-overview.md). 
> * Jeśli chcesz zrównoważyć obciążenie między serwerami w regionie w warstwie aplikacji, zapoznaj się z tematem [Application Gateway](../application-gateway/overview.md).
> * Jeśli chcesz zoptymalizować globalne Routing ruchu w sieci Web i zoptymalizować wydajność i niezawodność najwyższej warstwy dla użytkowników końcowych dzięki szybkiemu globalnemu przejściu w tryb failover, zobacz [drzwi z przodu](../frontdoor/front-door-overview.md).
> 
> Kompleksowe scenariusze mogą skorzystać z łączenia tych rozwiązań w razie potrzeby.
> Aby zapoznać się z porównaniem opcji równoważenia obciążenia platformy Azure, zobacz [Omówienie opcji równoważenia obciążenia na platformie Azure](/azure/architecture/guide/technology-choices/load-balancing-overview).


## <a name="why-use-azure-load-balancer"></a>Dlaczego warto używać Azure Load Balancer?
Za pomocą Azure Load Balancer można skalować aplikacje i tworzyć usługi o wysokiej dostępności. Moduł równoważenia obciążenia obsługuje scenariusze przychodzące i wychodzące. Moduł równoważenia obciążenia zapewnia małe opóźnienia i wysoką przepływność oraz skaluje do milionów przepływów dla wszystkich aplikacji TCP i UDP.

Kluczowe scenariusze, które można wykonać za pomocą usługi Azure usługa Load Balancer w warstwie Standardowa obejmują:

- Równoważyć obciążenie ruchem **[wewnętrznym](./quickstart-load-balancer-standard-internal-portal.md)** i **[zewnętrznym](./quickstart-load-balancer-standard-public-portal.md)** z maszynami wirtualnymi platformy Azure.

- Zwiększ dostępność przez dystrybucję zasobów **[w ramach](./tutorial-load-balancer-standard-public-zonal-portal.md)** i **[między](./tutorial-load-balancer-standard-public-zone-redundant-portal.md)** strefami.

- Skonfiguruj **[łączność wychodzącą](./load-balancer-outbound-connections.md)** dla maszyn wirtualnych platformy Azure.

- Do monitorowania zasobów o zrównoważonym obciążeniu służy **[sondy kondycji](./load-balancer-custom-probe-overview.md)** .

- Zastosowanie **[przekazywania portów](./tutorial-load-balancer-port-forwarding-portal.md)** w celu uzyskania dostępu do maszyn wirtualnych w sieci wirtualnej przez publiczny adres IP i port.

- Włącz obsługę **[równoważenia obciążenia](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)** **[protokołu IPv6](../virtual-network/ipv6-overview.md)**.

- Usługa równoważenia obciążenia w warstwie Standardowa oferuje wielowymiarowe metryki za poorednictwem [Azure monitor](../azure-monitor/overview.md).  Te metryki można filtrować, grupować i rozdzielić w danym wymiarze.  Zapewniają one bieżące i historyczne informacje o wydajności i kondycji usługi. [Szczegółowe informacje na temat Azure Load Balancer](./load-balancer-insights.md) oferują wstępnie skonfigurowany pulpit nawigacyjny z przydatnymi wizualizacjami dla tych metryk.  Obsługiwane są również Resource Health. Aby uzyskać więcej informacji, przejrzyj **[diagnostykę modułu równoważenia obciążenia w warstwie Standardowa](load-balancer-standard-diagnostics.md)** .

- Równoważyć obciążenie usług na **[wielu portach, wielu adresach IP lub obu](./load-balancer-multivip-overview.md)**.

- Przenoszenie **[wewnętrznych](./move-across-regions-internal-load-balancer-portal.md)** i **[zewnętrznych](./move-across-regions-external-load-balancer-portal.md)** zasobów modułu równoważenia obciążenia w regionach platformy Azure.

- Równoważ obciążenie protokołów TCP i UDP na wszystkich portach jednocześnie przy użyciu **[portów ha](./load-balancer-ha-ports-overview.md)**.

### <a name="secure-by-default"></a><a name="securebydefault"></a>Zabezpieczenie domyślne

* Usługa równoważenia obciążenia w warstwie Standardowa jest oparta na modelu zabezpieczeń sieci z zaufaniem równym zeru.

* Usługa Load Balancer w warstwie Standardowa jest zabezpieczony domyślnie i częścią sieci wirtualnej. Sieć wirtualna jest siecią prywatną i izolowaną.  

* Standardowe usługi równoważenia obciążenia i standardowe publiczne adresy IP są zamykane dla połączeń przychodzących, chyba że są otwierane przez sieciowe grupy zabezpieczeń. Sieciowych grup zabezpieczeń są używane do jawnego zezwalania na dozwolony ruch.  Jeśli nie masz sieciowej grupy zabezpieczeń w podsieci lub karcie sieciowej zasobu maszyny wirtualnej, ruch nie może dotrzeć do tego zasobu. Aby dowiedzieć się więcej o sieciowych grup zabezpieczeń i sposobach ich stosowania do scenariusza, zobacz [sieciowe grupy zabezpieczeń](../virtual-network/network-security-groups-overview.md).

* Podstawowa usługa równoważenia obciążenia jest domyślnie otwarta w Internecie. 

* Moduł równoważenia obciążenia nie przechowuje danych klienta.

## <a name="pricing-and-sla"></a>Cennik i Umowa SLA

Aby uzyskać informacje o cenach usługi równoważenia obciążenia w warstwie Standardowa, zobacz [Cennik usługi równoważenia obciążenia](https://azure.microsoft.com/pricing/details/load-balancer/).
Podstawowa usługa równoważenia obciążenia jest oferowana bezpłatnie.
Zobacz umowę [SLA dla usługi równoważenia obciążenia](https://aka.ms/lbsla). Podstawowa usługa równoważenia obciążenia nie ma umowy SLA.

## <a name="whats-new"></a>Co nowego

Zasubskrybuj źródło danych RSS i zapoznaj się z najnowszymi aktualizacjami funkcji Azure Load Balancer na stronie [aktualizacji platformy Azure](https://azure.microsoft.com/updates/?category=networking&query=load%20balancer) .

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć korzystanie z usługi równoważenia obciążenia, zobacz [Tworzenie publicznego standardowego modułu równoważenia obciążenia](quickstart-load-balancer-standard-public-portal.md) .

Aby uzyskać więcej informacji o Azure Load Balancer ograniczeniach i składnikach, zobacz [składniki Azure Load Balancer](./components.md) i [koncepcje Azure Load Balancer](./concepts.md)