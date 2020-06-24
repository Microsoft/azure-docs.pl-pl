---
title: Często zadawane pytania — Azure Load Balancer
description: Odpowiedzi na często zadawane pytania dotyczące Azure Load Balancer.
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: article
ms.date: 04/22/2020
ms.author: errobin
ms.openlocfilehash: 4ae15d0898cedb0ed17dc308584769395aa819c2
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2020
ms.locfileid: "85079477"
---
# <a name="frequently-asked-questions"></a>Często zadawane pytania

## <a name="what-types-of-load-balancer-exist"></a>Jakie typy Load Balancer istnieją?
Wewnętrzne moduły równoważenia obciążenia, które równoważą ruch w obrębie sieci wirtualnej i zewnętrznych usług równoważenia obciążenia, które równoważą ruch do i z połączonego z Internetem punktu końcowego. Aby uzyskać więcej informacji, zobacz [typy Load Balancer](components.md#frontend-ip-configurations). 

Dla obu tych typów platforma Azure oferuje podstawową jednostkę SKU i standardową jednostkę SKU, która ma różne możliwości funkcjonalne, wydajnościowe, dotyczące zabezpieczeń i śledzenia kondycji. Te różnice zostały wyjaśnione w naszym artykule [porównawczym jednostki SKU](skus.md) .

 ## <a name="how-can-i-upgrade-from-a-basic-to-a-standard-load-balancer"></a>Jak przeprowadzić uaktualnienie z warstwy Podstawowa do usługa Load Balancer w warstwie Standardowa?
Zapoznaj się z artykułem " [uaktualnienie z warstwy Podstawowa do Standard](upgrade-basic-standard.md) ", aby uzyskać zautomatyzowany skrypt i wskazówki dotyczące uaktualniania jednostki SKU Load Balancer.

 ## <a name="what-are-the-different-load-balancing-options-in-azure"></a>Jakie są różne opcje równoważenia obciążenia na platformie Azure?
Zobacz [Przewodnik po technologii modułu równoważenia obciążenia](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview) dla dostępnych usług równoważenia obciążenia i zalecane zastosowania dla każdej z nich.

## <a name="where-can-i-find-load-balancer-arm-templates"></a>Gdzie mogę znaleźć szablony Load Balancer ARM?
Zobacz [listę Azure Load Balancer szablonów szybkiego startu](https://docs.microsoft.com/azure/templates/microsoft.network/loadbalancers#quickstart-templates) dla szablonów usługi ARM typowych wdrożeń.

## <a name="how-are-inbound-nat-rules-different-from-load-balancing-rules"></a>Jak różnią się przychodzące reguły NAT od reguł równoważenia obciążenia?
Reguły NAT są używane do określania zasobu zaplecza, do którego ma być kierowany ruch. Na przykład skonfigurowanie określonego portu usługi równoważenia obciążenia do wysyłania ruchu RDP do określonej maszyny wirtualnej. Reguły równoważenia obciążenia służą do określania puli zasobów zaplecza, do której ma być kierowany ruch, co pozwala zrównoważyć obciążenie dla każdego wystąpienia. Na przykład reguła modułu równoważenia obciążenia może kierować pakiety TCP na porcie 80 modułu równoważenia obciążenia w puli serwerów sieci Web.

## <a name="what-is-ip-1686312916"></a>Co to jest 168.63.129.16 IP?
Wirtualny adres IP dla hosta oznaczonego jako infrastruktura platformy Azure Load Balancer, z którego pochodzą sondy kondycji platformy Azure. Podczas konfigurowania wystąpień zaplecza muszą zezwalać na ruch z tego adresu IP, aby pomyślnie reagować na sondy kondycji. Ta reguła nie współdziała z dostępem do Load Balancer frontonu. Jeśli nie używasz Azure Load Balancer, możesz zastąpić tę regułę. Więcej informacji na temat tagów usługi można znaleźć [tutaj](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags).

## <a name="can-i-use-global-vnet-peering-with-basic-load-blancer"></a>Czy mogę używać globalnej komunikacji równorzędnej sieci wirtualnej z usługą pobierania w warstwie Podstawowa?
Nie. Podstawowa Load Balancer nie obsługuje globalnej komunikacji równorzędnej sieci wirtualnej. Zamiast tego można użyć usługa Load Balancer w warstwie Standardowa. Zapoznaj się z artykułem [uaktualnienie z warstwy Podstawowa do standardowa](upgrade-basic-standard.md) w celu zapewnienia bezproblemowego uaktualnienia.

## <a name="next-steps"></a>Następne kroki
Jeśli Twoje pytanie nie jest wymienione powyżej, Prześlij opinię na temat tej strony wraz z pytaniem. Spowoduje to utworzenie problemu usługi GitHub dla zespołu produktu, aby upewnić się, że otrzymasz odpowiedzi na wszystkie nasze pytania klientów.
