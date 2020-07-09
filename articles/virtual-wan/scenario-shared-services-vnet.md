---
title: 'Scenariusz: kierowanie do usług udostępnionych sieci wirtualnych'
titleSuffix: Azure Virtual WAN
description: Scenariusze dotyczące routingu — skonfiguruj trasy, aby uzyskać dostęp do udostępnionej sieci wirtualnej usługi z obciążeniem, które ma być dostępne dla każdej sieci wirtualnej i gałęzi.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 64bb4c85399f811c0ab7ff84b297b64734efc491
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85569257"
---
# <a name="scenario-route-to-shared-services-vnets"></a>Scenariusz: kierowanie do usług udostępnionych sieci wirtualnych

Podczas pracy z routingiem wirtualnego koncentratora sieci WAN jest dość kilka dostępnych scenariuszy. W tym scenariuszu celem jest skonfigurowanie tras w celu uzyskania dostępu do **udostępnionej** sieci wirtualnej usługi z obciążeniem, które ma mieć dostęp do każdej sieci wirtualnej i gałęzi (VPN/er/P2S). Aby uzyskać informacje na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).

## <a name="scenario-workflow"></a><a name="workflow"></a>Przepływ pracy scenariusza

Aby skonfigurować ten scenariusz, należy wziąć pod uwagę następujące czynności:

1. Zidentyfikuj sieć wirtualną **usług udostępnionych** .
2. Utwórz niestandardową tabelę tras. W tym przykładzie odwołujemy się do tej tabeli tras jako **RT_SHARED**. Aby uzyskać instrukcje dotyczące tworzenia tabeli tras, zobacz [jak skonfigurować Routing koncentratora wirtualnego](how-to-virtual-hub-routing.md). Użyj następujących wartości jako wytycznych:

   * **Skojarzenie**
     * W przypadku **sieci wirtualnych *z wyjątkiem* sieci wirtualnej usług udostępnionych**wybierz sieci wirtualnych do wyodrębnienia. Oznacza to, że wszystkie te sieci wirtualnych (poza siecią wirtualną usług udostępnionych) będą mogły uzyskać dostęp do miejsca docelowego na podstawie tras RT_SHARED tabeli tras.

   * **Propagacja**
      * W przypadku **gałęzi**, Propaguj trasy do tej tabeli tras, oprócz innych tabel tras, które mogły już zostać zaznaczone. Ze względu na ten krok RT_SHARED tabeli tras będzie uczyć się tras ze wszystkich połączeń rozgałęzień (VPN/ER/użytkownika).
      * Dla **sieci wirtualnych**wybierz **sieć wirtualną usługi udostępnione**. Ze względu na ten krok RT_SHARED tabeli tras będzie uczyć się tras z połączenia sieci wirtualnej usług udostępnionych.

     Spowoduje to zmianę konfiguracji routingu, tak jak pokazano na poniższej ilustracji.

   :::image type="content" source="./media/routing-scenarios/shared-service-vnet/shared-services.png" alt-text="Sieć wirtualna usług udostępnionych":::

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat wirtualnej sieci WAN, zobacz [często zadawane pytania](virtual-wan-faq.md).
* Aby uzyskać więcej informacji na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).
