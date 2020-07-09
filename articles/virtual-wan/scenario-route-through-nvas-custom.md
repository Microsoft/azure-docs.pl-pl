---
title: 'Scenariusz: kierowanie ruchu przez urządzeń WUS przy użyciu ustawień niestandardowych'
titleSuffix: Azure Virtual WAN
description: Ten scenariusz ułatwia kierowanie ruchu przez urządzeń WUS przy użyciu innego urządzenie WUS dla ruchu związanego z Internetem.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 3fcad59bcfb75b8de6af14eae7e0bcb4962b17eb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85569265"
---
# <a name="scenario-route-traffic-through-nvas---custom-preview"></a>Scenariusz: kierowanie ruchu przez urządzeń WUS — niestandardowy (wersja zapoznawcza)

Podczas pracy z routingiem wirtualnego koncentratora sieci WAN jest dość kilka dostępnych scenariuszy. W tym scenariuszu urządzenie WUS (sieciowe urządzenie wirtualne) celem jest kierowanie ruchu przez urządzenie WUS dla gałęzi wirtualnej <-> i używanie innego urządzenie WUS dla ruchu związanego z Internetem. Aby uzyskać informacje na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).

## <a name="scenario-architecture"></a><a name="architecture"></a>Architektura scenariusza

Na **rysunku 1**znajduje się jedno centrum, **Hub 1**.

* **Koncentrator 1** jest podłączony bezpośrednio do urządzenie WUS sieci wirtualnych sieci **wirtualnej 4** i sieci **wirtualnej 5**.

* Ruch między sieci wirtualnych 1, 2 i 3 i gałęziami (VPN/ER/P2S) powinien zostać przeszedł przez **Sieć VNET 4 urządzenie WUS** 10.4.0.5.

* Cały ruch związany z Internetem z sieci wirtualnych 1, 2 i 3 oczekuje się za pośrednictwem sieci **wirtualnej 5 urządzenie WUS** 10.5.0.5.

**Rysunek 1**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-1.png" alt-text="Rysunek 1":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Przepływ pracy scenariusza

Aby skonfigurować Routing za pośrednictwem urządzenie WUS, poniżej przedstawiono kroki, które należy wziąć pod uwagę:

1. Aby ruch związany z Internetem przeszedł przez sieci VNET5, potrzebne są sieci wirtualnych 1, 2 i 3 do bezpośredniego połączenia za pośrednictwem komunikacji równorzędnej sieci wirtualnej z siecią wirtualną 5. Wymagany jest również UDR skonfigurowany w sieci wirtualnych dla 0.0.0.0/0 i następnego skoku 10.5.0.5. Obecnie wirtualna sieć WAN nie zezwala na urządzenie WUS następnego skoku w koncentratorze wirtualnym dla 0.0.0.0/0.

1. W Azure Portal przejdź do centrum wirtualnego i Utwórz niestandardową tabelę tras **RT_Shared** , która będzie uczyć się tras za pośrednictwem propagacji ze wszystkich połączeń sieci wirtualnych i gałęzi. Na **rysunku 2**przedstawiono to pustą tabelę tras niestandardowych **RT_Shared**.

   * **Trasy:** Nie trzeba dodawać żadnych tras statycznych.

   * **Skojarzenie:** Wybierz sieci wirtualnych 4 i 5, co oznacza, że połączenia sieci wirtualnych 4 i 5 są kojarzone z tabelą tras **RT_Shared**.

   * **Propagacja:** Ponieważ chcesz, aby wszystkie gałęzie i połączenia sieci wirtualnej propagowanie tras dynamicznie do tej tabeli tras, zaznacz opcję gałęzie i wszystkie sieci wirtualnych.

1. Utwórz niestandardową tabelę tras **RT_V2B** do kierowania ruchu z sieci wirtualnych 1, 2 i 3 do gałęzi.

   * **Trasy:** Dodaj zagregowany wpis trasy statycznej dla gałęzi (VPN/ER/P2S) (10.2.0.0/16 na **rysunku 2**) z następnym przeskokiem jako połączenie sieci wirtualnej 4. Należy również skonfigurować trasę statyczną w połączeniu sieci wirtualnej 4 dla prefiksów gałęzi i wskazać następny przeskok jako określony adres IP urządzenie WUS w sieci wirtualnej 4.

   * **Skojarzenie:** Zaznacz wszystkie sieci wirtualnych 1, 2 i 3. Oznacza to, że połączenia sieci wirtualnej 1, 2 i 3 będą skojarzone z tą tabelą tras i będą mogły uczyć się tras (statyczne i dynamiczne za pośrednictwem propagacji) w tej tabeli tras.

   * **Propagacja:** Połączenia propagują trasy do tabel tras. Wybranie sieci wirtualnych 1, 2 i 3 umożliwi propagację tras z sieci wirtualnych 1, 2 i 3 do tej tabeli tras. Nie ma potrzeby propagowania tras z połączeń gałęzi do RT_V2B, ponieważ ruch sieci wirtualnej gałęzi przechodzi przez urządzenie WUS w sieci VNET4.
  
1. Edytuj domyślną tabelę tras **DefaultRouteTable**.

   Wszystkie połączenia sieci VPN, ExpressRoute i VPN użytkowników są skojarzone z domyślną tabelą tras. Wszystkie połączenia sieci VPN, ExpressRoute i VPN użytkowników propagują trasy do tego samego zestawu tabel tras.

   * **Trasy:** Dodaj zagregowany wpis trasy statycznej dla sieci wirtualnych 1, 2 i 3 (10.1.0.0/16 na **rysunku 2**) przy następnym przeskoku jako połączenie sieci wirtualnej 4. Należy również skonfigurować trasę statyczną w połączeniu sieci wirtualnej 4, 2 i 3 zagregowane prefiksy, a następnie wskazać następny przeskok jako określony adres IP urządzenie WUS w sieci wirtualnej 4.

   * **Skojarzenie:** Upewnij się, że wybrano opcję dla gałęzi (VPN/ER/P2S), dzięki czemu lokalne połączenia gałęzi są skojarzone z *defaultroutetable*.

   * **Propagacja z:** Upewnij się, że jest wybrana opcja dla gałęzi (VPN/ER/P2S), dzięki czemu połączenia lokalne są propagowane do *defaultroutetable*.

**Rysunek 2**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-2.png" alt-text="Rysunek 2":::

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat wirtualnej sieci WAN, zobacz [często zadawane pytania](virtual-wan-faq.md).
* Aby uzyskać więcej informacji na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).
