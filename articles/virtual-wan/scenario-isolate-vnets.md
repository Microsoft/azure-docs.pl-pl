---
title: 'Scenariusz: izolowanie sieci wirtualnych'
titleSuffix: Azure Virtual WAN
description: Scenariusze związane z izolacją sieci wirtualnych
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: f43f17a0f3742831920836e448de3ef757f2dfa6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85568704"
---
# <a name="scenario-isolating-vnets"></a>Scenariusz: izolowanie sieci wirtualnych

Podczas pracy z routingiem wirtualnego koncentratora sieci WAN jest dość kilka dostępnych scenariuszy. W tym scenariuszu celem jest zapobieganie sieci wirtualnych się do innych. Jest to znane jako izolowanie sieci wirtualnych. Obciążenie w sieci wirtualnej pozostaje izolowane i nie może komunikować się z innymi sieci wirtualnych, tak jak w przypadku dowolnego scenariusza. Jednak sieci wirtualnych są wymagane do uzyskania dostępu do wszystkich gałęzi (VPN, ER i VPN użytkownika). W tym scenariuszu wszystkie połączenia sieci VPN, ExpressRoute i VPN użytkowników są skojarzone z tą samą i jedną tabelą tras. Wszystkie połączenia sieci VPN, ExpressRoute i VPN użytkowników propagują trasy do tego samego zestawu tabel tras. Aby uzyskać informacje na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).

## <a name="scenario-workflow"></a><a name="workflow"></a>Przepływ pracy scenariusza

Aby skonfigurować ten scenariusz, należy wziąć pod uwagę następujące czynności:

1. Utwórz niestandardową tabelę tras. W przykładzie tabela tras jest **RT_VNet**. Aby utworzyć tabelę tras, zobacz [jak skonfigurować Routing koncentratora wirtualnego](how-to-virtual-hub-routing.md). Aby uzyskać więcej informacji na temat tabel tras, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).
2. Podczas tworzenia tabeli tras **RT_VNet** skonfiguruj następujące ustawienia:

   * **Skojarzenie**: Wybierz sieci wirtualnych, które chcesz odizolować.
   * **Propagacja**: wybierz opcję dla gałęzi, co oznacza, że połączenia z gałęzią (VPN/er/P2S) będą propagowanie tras do tej tabeli tras.

:::image type="content" source="./media/routing-scenarios/isolated/isolated-vnets.png" alt-text="Izolowany sieci wirtualnych":::

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat wirtualnej sieci WAN, zobacz [często zadawane pytania](virtual-wan-faq.md).
* Aby uzyskać więcej informacji na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).
