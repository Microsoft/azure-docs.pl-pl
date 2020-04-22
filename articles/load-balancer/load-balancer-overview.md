---
title: Co to jest usługa Azure Load Balancer?
titleSuffix: Azure Load Balancer
description: Omówienie funkcji, architektury i implementacji usługi Azure Load Balancer. Dowiedz się, jak działa moduł równoważenia obciążenia i jak go używać w chmurze.
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
ms.date: 1/14/2020
ms.author: allensu
ms.openlocfilehash: 8596b435ffa02da7daf4ef98bfe0fe7995b9270a
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768198"
---
# <a name="what-is-azure-load-balancer"></a>Co to jest usługa Azure Load Balancer?

*Równoważenie obciążenia* odnosi się do równomiernego podziału obciążenia (przychodzącego ruchu sieciowego) między grupę zasobów zaplecza lub serwerów. 

Moduł równoważenia obciążenia platformy Azure działa w warstwie czwartej modelu OSI (Open Systems Interconnection). Jest to pojedynczy punkt kontaktowy dla klientów. Moduł równoważenia obciążenia dystrybuuje przepływy przychodzące, które docierają do front-end modułu równoważenia obciążenia do wystąpień puli wewnętrznej bazy danych. Przepływy te są zgodne ze skonfigurowanymi regułami równoważenia obciążenia i sondami kondycji. Wystąpienia puli wewnętrznej bazy danych mogą być maszynami wirtualnymi platformy Azure lub wystąpieniami w zestawie skalowania maszyny wirtualnej.

**[Publiczny moduł równoważenia obciążenia](./concepts-limitations.md#publicloadbalancer)** może zapewnić połączenia wychodzące dla maszyn wirtualnych (VM) wewnątrz sieci wirtualnej. Połączenia te są realizowane przez tłumaczenie ich prywatnych adresów IP na publiczne adresy IP. Publiczne moduły równoważenia obciążenia są używane do równoważenia obciążenia ruchu internetowego do maszyn wirtualnych.

**[Wewnętrzny (lub prywatny) moduł równoważenia obciążenia](./concepts-limitations.md#internalloadbalancer)** jest używany tam, gdzie prywatne adresy IP są potrzebne tylko w przedmówce. Wewnętrzne moduły równoważenia obciążenia są używane do równoważenia obciążenia ruchu wewnątrz sieci wirtualnej. Wewnętrznej ceony modułu równoważenia obciążenia można uzyskać z sieci lokalnej w scenariuszu hybrydowym.

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer.svg" width="512" title="Azure Load Balancer">
</p>

*Rysunek: Równoważenie aplikacji wielowarstwowych przy użyciu publicznego i wewnętrznego modułu równoważenia obciążenia*

Aby uzyskać więcej informacji na temat poszczególnych składników modułu równoważenia obciążenia, zobacz [Składniki i ograniczenia modułu równoważenia obciążenia platformy Azure](./concepts-limitations.md)

>[!NOTE]
> Platforma Azure udostępnia zestaw w pełni zarządzanych rozwiązań do równoważenia obciążenia dla Twoich scenariuszy. Jeśli potrzebujesz wysokiej wydajności, niskich opóźnień, równoważenia obciążenia warstwy 7, zobacz [Co to jest brama aplikacji platformy Azure?](../application-gateway/overview.md) Jeśli szukasz globalnego równoważenia obciążenia DNS, zobacz [Co to jest Traffic Manager?](../traffic-manager/traffic-manager-overview.md) Scenariusze end-to-end mogą korzystać z łączenia tych rozwiązań.
>
> Aby zapoznać się z porównaniem opcji równoważenia obciążenia platformy Azure, zobacz [Omówienie opcji równoważenia obciążenia na platformie Azure.](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview)

## <a name="why-use-azure-load-balancer"></a>Dlaczego warto korzystać z modułu Równoważenia obciążenia platformy Azure?
Dzięki standardowej równoważce obciążenia można skalować aplikacje i tworzyć usługi o wysokiej dostępności. Moduł równoważenia obciążenia obsługuje scenariusze przychodzące i wychodzące. Moduł równoważenia obciążenia zapewnia małe opóźnienia i wysoką przepływność oraz skaluje do milionów przepływów dla wszystkich aplikacji TCP i UDP.

Kluczowe scenariusze, które można wykonać przy użyciu standardowego modułu równoważenia obciążenia obejmują:

- Równoważenie obciążenia ruchu **[wewnętrznego](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-manage-portal)** i **[zewnętrznego](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-internal-portal)** na maszynach wirtualnych platformy Azure.

- Zwiększ dostępność, rozdzielając zasoby **[w](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zonal-portal)** obrębie i **[między](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zone-redundant-portal)** strefami.

- Konfigurowanie **[łączności wychodzącej](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)** dla maszyn wirtualnych platformy Azure.

- Sondy **[kondycji](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)** służy do monitorowania zasobów z równoważenia obciążenia.

- Za pomocą **[przekierowania portów](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal)** można uzyskać dostęp do maszyn wirtualnych w sieci wirtualnej za pomocą publicznego adresu IP i portu.

- Włącz obsługę **[równoważenia obciążenia](https://docs.microsoft.com/azure/virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell)** **[IPv6](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)**.

- Standardowy moduł równoważenia obciążenia zapewnia wielowymiarowe metryki za pośrednictwem [usługi Azure Monitor.](https://docs.microsoft.com/azure/azure-monitor/overview)  Te metryki mogą być filtrowane, grupowane i podzielone dla danego wymiaru.  Zapewniają one aktualny i historyczny wgląd w wydajność i kondycję Twojej usługi.  Kondycja zasobów jest również obsługiwana. Przejrzyj **[standardową diagnostykę równoważenia obciążenia,](load-balancer-standard-diagnostics.md)** aby uzyskać więcej informacji.

- Usługi równoważenia obciążenia na **[wielu portach, wielu adresach IP lub obu](https://docs.microsoft.com/azure/load-balancer/load-balancer-multivip-overview)**.

- Przenoszenie **[zasobów modułu](https://docs.microsoft.com/azure/load-balancer/move-across-regions-internal-load-balancer-portal)** równoważenia obciążenia wewnętrznego i **[zewnętrznego](https://docs.microsoft.com/azure/load-balancer/move-across-regions-external-load-balancer-portal)** w regionach platformy Azure.

- Równoważenie obciążenia TCP i UDP przepływa na wszystkich portach jednocześnie przy użyciu **[portów wysokiej haw](https://docs.microsoft.com/azure/load-balancer/load-balancer-ha-ports-overview)**.

### <a name="secure-by-default"></a><a name="securebydefault"></a>Zabezpieczenie domyślne

Standardowy moduł równoważenia obciążenia jest zbudowany na modelu zabezpieczeń sieci zaufania zerowego w jego rdzeniu. Standardowy moduł równoważenia obciążenia jest domyślnie bezpieczny i jest częścią sieci wirtualnej. Sieć wirtualna jest siecią prywatną i odizolowaną.  Oznacza to, że standardowe moduły równoważenia obciążenia i standardowe publiczne adresy IP są zamykane dla przepływów przychodzących, chyba że są otwierane przez sieciowe grupy zabezpieczeń. NSG są używane do jawnego zezwalania na dozwolony ruch.  Jeśli nie masz sieciowej grupy sieciowej w podsieci lub karty sieciowej zasobu maszyny wirtualnej, ruch nie może dotrzeć do tego zasobu. Aby dowiedzieć się więcej o sieciach bezpieczeństwa sieciowych i sposobie ich stosowania w scenariuszu, zobacz [Sieciowe grupy zabezpieczeń](../virtual-network/security-overview.md).
Podstawowy moduł równoważenia obciążenia jest domyślnie otwarty dla Internetu.


## <a name="pricing-and-sla"></a>Ceny i umowy SLA

Aby uzyskać informacje o cenach standardowego modułu równoważenia obciążenia, zobacz [Cennik równoważenia obciążenia](https://azure.microsoft.com/pricing/details/load-balancer/).
Za użycie modułu równoważenia obciążenia w warstwie Podstawowa nie są naliczane opłaty.
Zobacz [SLA dla modułu równoważącego obciążenie](https://aka.ms/lbsla). Podstawowy moduł równoważenia obciążenia nie ma umowy SLA.

## <a name="next-steps"></a>Następne kroki
Zobacz [Uaktualnianie podstawowego modułu równoważenia obciążenia,](upgrade-basic-standard.md) aby uaktualnić podstawowy moduł równoważenia obciążenia do standardowego modułu równoważenia obciążenia.

Zobacz [Tworzenie publicznego standardowego modułu równoważenia obciążenia,](quickstart-load-balancer-standard-public-portal.md) aby rozpocząć korzystanie z modułu równoważenia obciążenia.

Aby uzyskać więcej informacji na temat ograniczeń i składników równoważenia obciążenia platformy Azure, zobacz [pojęcia i ograniczenia dotyczące równoważenia obciążenia platformy Azure](./concepts-limitations.md)
