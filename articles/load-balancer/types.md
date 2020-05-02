---
title: Typy Azure Load Balancer
description: Przegląd typów Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2020
ms.author: allensu
ms.openlocfilehash: 31e2bf19967bb8870ee6ab75687bb3fcc37373f7
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629973"
---
# <a name="azure-load-balancer-types"></a>Typy Azure Load Balancer

Azure Load Balancer ma dwa typy i dwie jednostki SKU.

## <a name="public-load-balancer"></a><a name = "publicloadbalancer"></a>Publiczny moduł równoważenia obciążenia

Publiczny moduł równoważenia obciążenia mapuje publiczny adres IP i port ruchu przychodzącego na prywatny adres IP i port maszyny wirtualnej. Usługa równoważenia obciążenia mapuje ruch w inny sposób wokół ruchu odpowiedzi z maszyny wirtualnej. Można dystrybuować określone typy ruchu między wieloma maszynami wirtualnymi lub usługami, stosując reguły równoważenia obciążenia. Na przykład można rozłożyć obciążenie ruchu związanego z żądaniami internetowymi na wiele serwerów internetowych.

>[!NOTE]
>Możliwe jest zaimplementowanie tylko jednego publicznego modułu równoważenia obciążenia i jednego wewnętrznego modułu równoważenia obciążenia na zestaw dostępności.

Na poniższej ilustracji przedstawiono punkt końcowy ze zrównoważonym obciążeniem dla ruchu internetowego, który jest współużytkowany przez trzy maszyny wirtualne dla portu publicznego i TCP 80. Te trzy maszyny wirtualne znajdują się w zestawie z równoważeniem obciążenia.

![Przykład publicznego modułu równoważenia obciążenia](./media/load-balancer-overview/load-balancer.png)

*Ilustracja: Równoważenie ruchu internetowego za pomocą publicznego modułu równoważenia obciążenia*

Klienci internetowi wysyłają żądania strony sieci Web na publiczny adres IP aplikacji sieci Web na porcie TCP 80. Azure Load Balancer dystrybuuje żądania między trzema maszynami wirtualnymi w zestawie o zrównoważonym obciążeniu. Aby uzyskać więcej informacji na temat algorytmów modułu równoważenia obciążenia, zobacz [pojęcia dotyczące usługi równoważenia obciążenia](concepts.md).

Azure Load Balancer domyślnie dystrybuuje ruch sieciowy między wieloma wystąpieniami maszyn wirtualnych. Można również skonfigurować koligację sesji. Aby uzyskać więcej informacji, zobacz [Konfigurowanie trybu dystrybucji dla Azure Load Balancer](load-balancer-distribution-mode.md).

## <a name="internal-load-balancer"></a><a name = "internalloadbalancer"></a>Wewnętrzny moduł równoważenia obciążenia

Wewnętrzny moduł równoważenia obciążenia dystrybuuje ruch do zasobów znajdujących się w sieci wirtualnej. Platforma Azure ogranicza dostęp do adresów IP frontonu sieci wirtualnej ze zrównoważonym obciążeniem. 

Adresy IP frontonu i sieci wirtualne nigdy nie są bezpośrednio ujawniane w internetowym punkcie końcowym. Wewnętrzne aplikacje biznesowe są uruchomiane na platformie Azure i dostęp do nich jest uzyskiwany z poziomu platformy Azure lub zasobów lokalnych.

Wewnętrzny moduł równoważenia obciążenia włącza następujące typy równoważenia obciążenia:

* **W ramach sieci wirtualnej**: Równoważenie obciążenia z maszyn wirtualnych w sieci wirtualnej do zestawu maszyn wirtualnych znajdujących się w tej samej sieci wirtualnej.
* W **przypadku sieci wirtualnej obejmującej wiele**lokalizacji: Równoważenie obciążenia z komputerów lokalnych do zestawu maszyn wirtualnych znajdujących się w tej samej sieci wirtualnej.
* **W przypadku aplikacji wielowarstwowych**: Równoważenie obciążenia dla aplikacji wielowarstwowych dostępnych z Internetu, w których warstwy zaplecza nie są dostępne w Internecie. Warstwy zaplecza wymagają równoważenia obciążenia sieciowego z warstwy połączonej z Internetem. Zobacz następną ilustrację.
* **Dla aplikacji biznesowych**: równoważenie obciążenia na potrzeby aplikacji biznesowych, które są hostowane na platformie Azure bez użycia dodatkowego sprzętu lub oprogramowania służącego do równoważenia obciążenia. Ten scenariusz obejmuje serwery lokalne, które znajdują się w zestawie komputerów, których ruch jest zrównoważony.

![Przykład wewnętrznego modułu równoważenie obciążenia](./media/load-balancer-overview/load-balancer.png)

*Ilustracja: Równoważenie aplikacji wielowarstwowych przy użyciu usługi równoważenia obciążenia publicznego i wewnętrznego*

## <a name="load-balancer-sku-comparison"></a><a name="skus"></a> Porównanie jednostek SKU modułu równoważenia obciążenia

Moduł równoważenia obciążenia obsługuje podstawowe i standardowe jednostki SKU. Te jednostki SKU różnią się w zależności od skali, funkcji i cen. Każdy scenariusz, który jest możliwy w przypadku podstawowego modułu równoważenia obciążenia, można utworzyć za pomocą usługi równoważenia obciążenia w warstwie Standardowa.

Aby porównać i poznać różnice, zapoznaj się z poniższą tabelą. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure usługa Load Balancer w warstwie Standardowa](load-balancer-standard-overview.md).

>[!NOTE]
> Firma Microsoft zaleca użycie usługi równoważenia obciążenia w warstwie Standardowa.
Autonomiczne maszyny wirtualne, zestawy dostępności i zestawy skalowania maszyn wirtualnych można połączyć tylko jednej jednostki SKU — nigdy do obu. Moduł równoważenia obciążenia i jednostka SKU publicznego adresu IP muszą być zgodne, gdy są używane z publicznymi adresami IP. Usługa równoważenia obciążenia i jednostki SKU publicznego adresu IP nie są modyfikowalne.

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

Aby uzyskać więcej informacji, zobacz [limity usługi równoważenia obciążenia](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer). Szczegółowe informacje dotyczące modułu równoważenia obciążenia w warstwie Standardowa zawiera [przegląd](load-balancer-standard-overview.md), [cennik](https://aka.ms/lbpricing) i [umowa dotycząca poziomu usług](https://aka.ms/lbsla).

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć korzystanie z Load Balancer, zobacz [tworzenie usługa Load Balancer w warstwie Standardowa publicznego](quickstart-load-balancer-standard-public-portal.md) .
- Dowiedz się więcej o korzystaniu z [Usługa Load Balancer w warstwie Standardowa i strefy dostępności](load-balancer-standard-availability-zones.md).
- Dowiedz się więcej o [sondach kondycji](load-balancer-custom-probe-overview.md).
- Dowiedz się więcej na temat korzystania z [Load Balancer dla połączeń wychodzących](load-balancer-outbound-connections.md).
- Dowiedz się więcej o [Usługa Load Balancer w warstwie Standardowa z regułami równoważenia obciążenia dla portów ha](load-balancer-ha-ports-overview.md).
- Dowiedz się więcej na temat [sieciowych grup zabezpieczeń](../virtual-network/security-overview.md).