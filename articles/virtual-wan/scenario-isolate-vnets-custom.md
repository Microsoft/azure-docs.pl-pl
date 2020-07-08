---
title: 'Scenariusz: niestandardowa izolacja dla sieci wirtualnych'
titleSuffix: Azure Virtual WAN
description: Scenariusze dotyczące routingu — Zapobiegaj dostępowi wybranych sieci wirtualnych do siebie nawzajem
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 3719956df0dce62ee69d8e306ff2cad27197616d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85568708"
---
# <a name="scenario-custom-isolation-for-vnets"></a>Scenariusz: niestandardowa izolacja dla sieci wirtualnych

Podczas pracy z routingiem wirtualnego koncentratora sieci WAN jest dość kilka dostępnych scenariuszy. W niestandardowym scenariuszu izolacji dla sieci wirtualnych celem jest uniemożliwienie konkretnego zestawu sieci wirtualnych z możliwością uzyskania dostępu do innego określonego zestawu sieci wirtualnych. Jednak sieci wirtualnych są wymagane do uzyskania dostępu do wszystkich gałęzi (sieci VPN/ER/użytkownika).

W tym scenariuszu połączenia sieci VPN, ExpressRoute i VPN użytkownika (nazywane rozgałęziami) są skojarzone z tą samą tabelą tras (domyślna tabela tras). Wszystkie połączenia sieci VPN, ExpressRoute i VPN użytkowników propagują trasy do tego samego zestawu tabel tras. Aby uzyskać więcej informacji na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).

## <a name="scenario-workflow"></a><a name="architecture"></a>Przepływ pracy scenariusza

Na **rysunku 1**znajdują się niebieskie i czerwone połączenia sieci wirtualnej.

* Sieci wirtualnych połączone z niebieską siecią mogą się łączyć ze sobą, a także dotrzeć do wszystkich rozgałęzień (VPN/ER/P2S).
* Czerwona sieci wirtualnych może nawiązać połączenie ze sobą, a także dotrzeć do wszystkich rozgałęzień (VPN/ER/P2S).

Podczas konfigurowania routingu należy wziąć pod uwagę następujące czynności.

1. Utwórz dwie niestandardowe tabele tras w Azure Portal, **RT_BLUE** i **RT_RED**.
2. W przypadku tabeli tras **RT_BLUE**w obszarze:
   * **Skojarzenie**: zaznacz wszystkie niebieskie sieci wirtualnych
   * **Propagacja**: w przypadku gałęzi, wybierz opcję dla gałęzi, co oznacza, że połączenia z gałęzią (VPN/er/P2S) będą propagowane tras do tej tabeli tras.
3. Powtórz te same czynności dla **RT_REDj** tabeli tras dla Red sieci wirtualnych i rozgałęzień (VPN/er/P2S).

Spowoduje to zmianę konfiguracji routingu, tak jak pokazano na poniższej ilustracji.

**Rysunek 1**

:::image type="content" source="./media/routing-scenarios/custom-isolation/custom.png" alt-text="Rysunek 1":::

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat wirtualnej sieci WAN, zobacz [często zadawane pytania](virtual-wan-faq.md).
* Aby uzyskać więcej informacji na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).
