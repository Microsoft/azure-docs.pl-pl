---
title: Ochrona zasobów sieciowych w Azure Security Center
description: Ten dokument zawiera zalecenia dotyczące Azure Security Center, które pomagają chronić zasoby sieci platformy Azure i zachować zgodność z zasadami zabezpieczeń.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 96c55a02-afd6-478b-9c1f-039528f3dea0
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2019
ms.author: memildin
ms.openlocfilehash: 28d0d7b14c18a1d98be1872b1555930b2e1650c3
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342454"
---
# <a name="protect-your-network-resources"></a>Ochrona zasobów sieciowych
Azure Security Center stale analizuje stan zabezpieczeń zasobów platformy Azure pod kątem najlepszych rozwiązań z zakresu zabezpieczeń sieci. Gdy Security Center identyfikuje potencjalne luki w zabezpieczeniach, tworzy zalecenia, które przeprowadzą Cię przez proces konfigurowania wymaganych kontrolek do zabezpieczania i zabezpieczania zasobów.

Aby zapoznać się z pełną listą zaleceń dotyczących sieci, zobacz [zalecenia dotyczące sieci](recommendations-reference.md#recs-network).

Ten artykuł dotyczy zaleceń, które dotyczą zasobów platformy Azure z punktu widzenia zabezpieczeń sieci. Centrum zaleceń dotyczących sieci wokół zapór nowej generacji, sieciowych grup zabezpieczeń, dostępu JIT do maszyny wirtualnej, nadmiernie ograniczających reguły ruchu przychodzącego i nie tylko. Aby zapoznać się z listą zaleceń dotyczących sieci i akcji korygowania, zobacz [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w Azure Security Center](security-center-recommendations.md).

Funkcje **sieciowe** Security Center obejmują: 

- Mapa sieci (wymaga usługi Azure Defender)
- [Adaptacyjne Zabezpieczanie sieci](security-center-adaptive-network-hardening.md) (wymaga usługi Azure Defender)
- Zalecenia dotyczące zabezpieczeń sieci
 
## <a name="view-your-networking-resources-and-their-recommendations"></a>Wyświetlanie zasobów sieciowych i ich zaleceń

Na [stronie spis](asset-inventory.md)zasobów Użyj filtru typu zasobów, aby wybrać zasoby sieciowe, które chcesz zbadać:

:::image type="content" source="./media/security-center-network-recommendations/network-filters-inventory.png" alt-text="Typy zasobów sieciowych dla spisu zasobów" lightbox="./media/security-center-network-recommendations/network-filters-inventory.png":::


## <a name="network-map"></a>Mapa sieci

Interaktywna mapa sieci zapewnia widok graficzny z nakładkami zabezpieczeń, które zapewniają zalecenia i szczegółowe informacje na potrzeby zabezpieczania zasobów sieciowych. Za pomocą mapy można zobaczyć topologię sieci obciążeń platformy Azure, połączenia między maszynami wirtualnymi i podsieciami oraz możliwość przechodzenia do szczegółów z mapy do określonych zasobów i zaleceń dotyczących tych zasobów.

Aby otworzyć mapę sieci:

1. W menu Security Center Otwórz pulpit nawigacyjny usługi Azure Defender i wybierz pozycję **mapa sieci**.

    :::image type="content" source="./media/security-center-network-recommendations/opening-network-map.png" alt-text="Typy zasobów sieciowych dla spisu zasobów" lightbox="./media/security-center-network-recommendations/opening-network-map.png":::

1. Wybierz menu **warstwy** wybierz **topologię**.
 
Zostanie wyświetlony domyślny widok mapy topologii:

- Subskrypcje wybrane na platformie Azure. Mapa obsługuje wiele subskrypcji.
- Maszyny wirtualne, podsieci i sieci wirtualnych typu zasobów Menedżer zasobów (klasyczne zasoby platformy Azure nie są obsługiwane)
- Sieci wirtualnych komunikacji równorzędnej
- Tylko zasoby mające [zalecenia dotyczące sieci](security-center-recommendations.md) o wysokiej lub średniej ważności  
- Zasoby połączone z Internetem
- Mapa jest zoptymalizowana pod kątem subskrypcji wybranych na platformie Azure. W przypadku zmodyfikowania zaznaczenia mapa zostanie ponownie obliczona i zoptymalizowana na podstawie nowych ustawień.  

[![Mapa topologii sieci](./media/security-center-network-recommendations/network-map-info.png)](./media/security-center-network-recommendations/network-map-info.png#lightbox)

## <a name="understanding-the-network-map"></a>Omówienie mapy sieci

Mapa sieci może przedstawiać zasoby platformy Azure w widoku **topologii** i widoku **ruchu** . 

### <a name="the-topology-view"></a>Widok topologii

W widoku **topologii** mapy sieci można wyświetlić następujące informacje o zasobach sieciowych:

- W okręgu wewnętrznym można zobaczyć wszystkie sieci wirtualnych w wybranych subskrypcjach, następnym okręgiem są wszystkie podsieci, a zewnętrzny okrąg to wszystkie maszyny wirtualne.
- Linie łączące zasoby w mapie informują o tym, które zasoby są ze sobą skojarzone, oraz o strukturze sieci platformy Azure. 
- Użyj wskaźników ważności, aby szybko zapoznać się z tym, które zasoby mają otwarte zalecenia z Security Center.
- Możesz kliknąć dowolny z zasobów, aby przejść do szczegółów i wyświetlić szczegóły tego zasobu oraz jego zalecenia bezpośrednio, a także w kontekście mapy sieci.  
- Jeśli na mapie są wyświetlane zbyt wiele zasobów, Azure Security Center używa swojego algorytmu własnościowego do inteligentnego klastrowania zasobów, wyróżnienia zasobów, które znajdują się w najbardziej krytycznym stanie, i mają najwięcej zaleceń dotyczących ważności. 

Ponieważ mapa jest interaktywna i dynamiczna, każdy węzeł jest klikany, a widok może ulec zmianie w zależności od filtrów:

1. Można modyfikować elementy widoczne na mapie sieci przy użyciu filtrów znajdujących się na górze. Możesz skupić się na mapie na podstawie:

   -  **Kondycja zabezpieczeń**: można filtrować mapę na podstawie ważności (wysoka, średnia, niska) zasobów platformy Azure.
   - **Zalecenia**: możesz wybrać, które zasoby są wyświetlane na podstawie zaleceń, które są aktywne dla tych zasobów. Można na przykład wyświetlić tylko zasoby, dla których Security Center zaleca się włączenie sieciowych grup zabezpieczeń.
   - **Strefy sieciowe**: Domyślnie mapa wyświetla tylko zasoby połączone z Internetem, można również wybrać wewnętrzne maszyny wirtualne.
 
2. Możesz kliknąć pozycję **Zresetuj** w lewym górnym rogu w dowolnym momencie, aby przywrócić mapę do stanu domyślnego.

Aby przejść do szczegółów zasobu:

1. Po wybraniu określonego zasobu na mapie zostanie otwarte okienko po prawej stronie, w którym znajdują się ogólne informacje o zasobie, połączone rozwiązania zabezpieczeń, jeśli istnieją, a także zalecenia dotyczące zasobu. Jest to ten sam typ zachowania dla każdego typu zasobu, który został wybrany. 
2. Po umieszczeniu wskaźnika myszy na węźle na mapie można wyświetlić ogólne informacje o zasobie, w tym o subskrypcji, typie zasobu i grupie zasobów.
3. Użyj linku, aby powiększyć do etykietki narzędzia i ponownie umieścić mapę na tym konkretnym węźle. 
4. Aby skoncentrować się na mapie od określonego węzła, Pomniejsz.

### <a name="the-traffic-view"></a>Widok ruchu

Widok **ruch** udostępnia mapę całego możliwego ruchu między zasobami. Zapewnia to wizualną mapę wszystkich skonfigurowanych reguł, które definiują, które zasoby mogą komunikować się z tym, z którymi się komunikują. Dzięki temu można zobaczyć istniejącą konfigurację sieciowych grup zabezpieczeń, a także szybko identyfikować możliwe konfiguracje ryzykowne w ramach obciążeń.

### <a name="uncover-unwanted-connections"></a>Odkrywanie niechcianych połączeń

Siła tego widoku pozwala na wyświetlanie tych dozwolonych połączeń wraz z lukami w zabezpieczeniach, dzięki czemu można użyć tej części danych w celu przeprowadzenia niezbędnej ochrony zasobów. 

Można na przykład wykryć dwie nieświadome komputery, które mogły się komunikować, co pozwala lepiej izolować obciążenia i podsieci.

### <a name="investigate-resources"></a>Zbadaj zasoby

Aby przejść do szczegółów zasobu:

1. Po wybraniu określonego zasobu na mapie zostanie otwarte okienko po prawej stronie, w którym znajdują się ogólne informacje o zasobie, połączone rozwiązania zabezpieczeń, jeśli istnieją, a także zalecenia dotyczące zasobu. Jest to ten sam typ zachowania dla każdego typu zasobu, który został wybrany. 
2. Kliknij pozycję **ruch** , aby wyświetlić listę możliwego ruchu wychodzącego i przychodzącego dla zasobu — jest to kompleksowa lista osób, które mogą komunikować się z zasobem i kto może się z nim komunikować oraz za pomocą których protokołów i portów. Na przykład po wybraniu maszyny wirtualnej są wyświetlane wszystkie maszyny wirtualne, z którymi mogą się komunikować, a po wybraniu podsieci są wyświetlane wszystkie podsieci, z którymi może się komunikować.

**Dane te są oparte na analizie sieciowych grup zabezpieczeń, a także zaawansowanych algorytmów uczenia maszynowego, które analizują wiele reguł, aby zrozumieć ich skrzyżowania i interakcje.** 

[![Mapa ruchu sieciowego](./media/security-center-network-recommendations/network-map-traffic.png)](./media/security-center-network-recommendations/network-map-traffic.png#lightbox)


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o zaleceniach dotyczących innych typów zasobów platformy Azure, zobacz następujące tematy:

- [Ochrona maszyn i aplikacji w usłudze Azure Security Center](./asset-inventory.md)