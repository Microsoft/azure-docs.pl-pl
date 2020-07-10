---
title: 'Scenariusz: kierowanie ruchu przez urządzenie WUS'
titleSuffix: Azure Virtual WAN
description: Kierowanie ruchem za pośrednictwem urządzenia NVA
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: ed64b9d281cfbbf8202a99335ea2759b27a6fc42
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86142962"
---
# <a name="scenario-route-traffic-through-an-nva"></a>Scenariusz: kierowanie ruchu przez urządzenie WUS

Podczas pracy z routingiem wirtualnego koncentratora sieci WAN jest dość kilka dostępnych scenariuszy. W tym scenariuszu urządzenie WUS celem jest kierowanie ruchu przez urządzenie WUS (sieciowe urządzenie wirtualne) dla gałęzi do sieci wirtualnej i sieci wirtualnej do gałęzi. Aby uzyskać informacje na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).

> [!NOTE]
> Niektóre możliwości routingu mogą nadal zostać wycofane. Jeśli wdrożenie nie zakończyło się jeszcze w Twoim regionie, wykonaj kroki opisane w tych wersjach artykułów w międzyczasie:
>* [Azure Portal artykuł](virtual-wan-route-table-nva-portal.md)
>* [Artykuł programu PowerShell](virtual-wan-route-table-nva.md)
>

## <a name="scenario-architecture"></a><a name="architecture"></a>Architektura scenariusza

Na **rysunku 1**znajdują się dwa centra: **Hub 1** i **Hub 2**.

* **Centrum 1** i **koncentrator 2** są połączone bezpośrednio z usługą urządzenie WUS sieci wirtualnych **VNET 2** i siecią **VNET 4**.

* **Sieć wirtualna 5** i **Sieć wirtualna 6** są połączone z siecią **wirtualną 2**.

* **Sieć wirtualna 7** i **Sieć wirtualna 8** są połączone za pomocą sieci **wirtualnej 4**.

* **Sieci wirtualnych 5, 6, 7,** są pośrednimi szprychami, a nie bezpośrednio połączonymi z koncentratorem wirtualnym.

**Rysunek 1**

:::image type="content" source="./media/routing-scenarios/nva/nva.png" alt-text="Rysunek 1":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Przepływ pracy scenariusza

Aby skonfigurować Routing za pośrednictwem urządzenie WUS, poniżej przedstawiono kroki, które należy wziąć pod uwagę:

1. Zidentyfikuj połączenie sieci wirtualnej urządzenie WUS szprychy. Na **rysunku 1**są to **połączenia sieci VNET 2 (Eastusconn)** i **Sieć VNET 4 (weconn)**.

   Upewnij się, że skonfigurowano UDR:
   * Z sieci VNET 5 i 6 do sieci VNET 2 urządzenie WUS IP
   * Od sieci VNET 7 i 8 do sieci VNET 4 urządzenie WUS IP 
   
   Nie jest konieczne bezpośrednie łączenie sieci wirtualnej 5, 6, 7, 8 z centrami wirtualnymi. Upewnij się, że sieciowych grup zabezpieczeń w sieci wirtualnych 5, 6, 7, 8 zezwala na ruch dla gałęzi (VPN/ER/P2S) lub sieci wirtualnych połączony ze swoimi zdalnymi sieci wirtualnych. Na przykład Sieć wirtualna 5, 6 musi zapewnić, że sieciowych grup zabezpieczeń zezwala na ruch dla lokalnych prefiksów adresów i sieci wirtualnych 7, 8, które są podłączone do zdalnego koncentratora 2. 

2. Dodaj zagregowany wpis trasy statycznej dla sieci wirtualnych 2, 5, 6 do domyślnej tabeli tras centrum 1. 

   :::image type="content" source="./media/routing-scenarios/nva/nva-static-expand.png" alt-text="Przykład":::

3. Skonfiguruj trasę statyczną dla sieci wirtualnych 5, 6 w połączeniu z siecią wirtualną sieci wirtualnej 2. Aby skonfigurować konfigurację routingu dla połączenia sieci wirtualnej, zobacz [Routing koncentratora wirtualnego](how-to-virtual-hub-routing.md#routing-configuration).

4. Dodaj zagregowany wpis trasy statycznej dla sieci wirtualnych 4, 7, 8 do domyślnej tabeli tras Hub 1.

5. Powtórz kroki 2, 3 i 4 dla domyślnej tabeli tras centrum 2.

Spowoduje to zmianę konfiguracji routingu, tak jak pokazano na poniższej ilustracji.

   :::image type="content" source="./media/routing-scenarios/nva/nva-result.png" alt-text="Wynik":::

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat wirtualnej sieci WAN, zobacz [często zadawane pytania](virtual-wan-faq.md).
* Aby uzyskać więcej informacji na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).
