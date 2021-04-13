---
title: Zabezpieczanie ruchu między Application Gateway i pulami zaplecza
titleSuffix: Azure Virtual WAN
description: Scenariusze dotyczące routingu — zabezpieczony ruch przesyłany przez bramę aplikacji wdrożoną w sieci wirtualnej szprych połączonej z bezpiecznym wirtualnym koncentratorem sieci WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: cherylmc
ms.openlocfilehash: d9cb1251b90cf1c928f8286072bcd91e5ddf767e
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315577"
---
# <a name="scenario-secure-traffic-between-application-gateway-and-backend-pools"></a>Scenariusz: bezpieczny ruch między Application Gateway i pulami zaplecza

Podczas pracy z routingiem wirtualnego koncentratora sieci WAN jest dość kilka dostępnych scenariuszy. W tym scenariuszu ruch użytkownika przechodzi do platformy Azure za pomocą bramy aplikacji wdrożonej w sieci wirtualnej szprych, która jest połączona z bezpiecznym wirtualnym koncentratorem sieci WAN (wirtualnym koncentratorem sieci WAN przy użyciu zapory platformy Azure). Celem jest użycie zapory platformy Azure w zabezpieczonym koncentratorze wirtualnym w celu sprawdzenia ruchu między bramą aplikacji a pulami zaplecza.

W tym scenariuszu istnieją dwa specyficzne wzorce projektowe, w zależności od tego, czy Brama aplikacji i pule zaplecza znajdują się w tej samej sieci wirtualnej, czy też w różnych sieci wirtualnych.

* **Scenariusz 1:** Bramy aplikacji i pule zaplecza znajdują się w tej samej sieci wirtualnej równorzędnej z koncentratorem wirtualnym sieci WAN (odrębnymi podsieciami).
* **Scenariusz 2:** Bramy aplikacji i pule zaplecza znajdują się w różnych sieciach wirtualnych równorzędnych z wirtualnym koncentratorem sieci WAN.

## <a name="scenario-1---same-vnet"></a><a name="scenario-1"></a>Scenariusz 1 — ta sama sieć wirtualna

W tym scenariuszu bramy aplikacji i pule zaplecza znajdują się w tej samej sieci wirtualnej za pomocą komunikacji równorzędnej z koncentratorem wirtualnym sieci WAN (oddzielne podsieci).

:::image type="content" source="./media/routing-scenarios/secured-application-gateway/same-vnet.png" alt-text="Diagram scenariusza 1." lightbox="./media/routing-scenarios/secured-application-gateway/same-vnet.png":::

### <a name="workflow"></a>Przepływ pracy

Obecnie trasy anonsowane z tabeli tras wirtualnej sieci WAN do sieci wirtualnych szprych są stosowane do całej sieci wirtualnej, a nie podsieci sieci wirtualnej szprych. W związku z tym, trasy zdefiniowane przez użytkownika są niezbędne do włączenia tego scenariusza. Informacje o trasach zdefiniowanych przez użytkownika (UDR) znajdują się w temacie [Virtual Network Routes Customs](../virtual-network/virtual-networks-udr-overview.md#user-defined).


1. W Menedżerze zapory platformy Azure, w sieci wirtualnej szprych zawierającej bramę aplikacji i pule zaplecza, zaznacz opcję **Włącz bezpieczny ruch internetowy** i **Włącz bezpieczny ruch prywatny**.
1. Skonfiguruj trasy zdefiniowane przez użytkownika (UDR) w podsieci bramy aplikacji.

   * Aby zapewnić, że Brama aplikacji może wysyłać ruch bezpośrednio do Internetu, określ następujące UDR:

     * **Prefiks adresu:** 0.0.0.0.0/0
     * **Następny przeskok:** Internet

   * Aby zapewnić, że Brama aplikacji może wysyłać ruch do puli zaplecza za pośrednictwem zapory platformy Azure w wirtualnym koncentratorze sieci WAN, określ następujące UDR:

      * **Prefiks adresu:** Podsieć puli zaplecza (10.2.0.0/24)
      * **Następny przeskok:** Prywatny adres IP zapory platformy Azure

1. Skonfiguruj trasę zdefiniowaną przez użytkownika (UDR) w podsieci puli zaplecza.

   * **Prefiks adresu:** Podsieć Application Gateway
   * **Następny przeskok:** Prywatny adres IP zapory platformy Azure

## <a name="scenario-2---different-vnets"></a><a name="scenario-2"></a>Scenariusz 2 — różne sieci wirtualnych

W tym scenariuszu bramy aplikacji i pule zaplecza znajdują się w różnych sieciach wirtualnych, które są połączone za pomocą komunikacji równorzędnej z wirtualnym koncentratorem sieci WAN.

:::image type="content" source="./media/routing-scenarios/secured-application-gateway/different-vnets.png" alt-text="Diagram scenariusza 2." lightbox="./media/routing-scenarios/secured-application-gateway/different-vnets.png":::

### <a name="workflow"></a>Przepływ pracy

Obecnie trasy anonsowane z tabeli tras wirtualnej sieci WAN do sieci wirtualnych szprych są stosowane do całej sieci wirtualnej, a nie podsieci sieci wirtualnej szprych. W związku z tym, trasy zdefiniowane przez użytkownika są niezbędne do włączenia tego scenariusza. Informacje o trasach zdefiniowanych przez użytkownika (UDR) znajdują się w temacie [Virtual Network Routes Customs](../virtual-network/virtual-networks-udr-overview.md#user-defined).

1. W **Menedżerze zapory platformy Azure** zaznacz opcję **Włącz bezpieczny ruch internetowy** i **Włącz bezpieczny ruch prywatny** w obu sieciach wirtualnych szprych.

1. Skonfiguruj trasy zdefiniowane przez użytkownika (UDR) w podsieci bramy aplikacji. Aby zapewnić, że Brama aplikacji może wysyłać ruch bezpośrednio do Internetu, określ następujące UDR:

   * **Prefiks adresu:** 0.0.0.0.0/0
   * **Następny przeskok:** Internet

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).
* Aby uzyskać więcej informacji o trasach zdefiniowanych przez użytkownika, zobacz [Virtual Network tras niestandardowych](../virtual-network/virtual-networks-udr-overview.md#user-defined).
* Aby uzyskać informacje o bezpiecznych centrach wirtualnych sieci WAN, zobacz [bezpieczne centra wirtualne](../firewall-manager/secured-virtual-hub.md).
