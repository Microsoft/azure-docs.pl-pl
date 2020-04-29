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
ms.openlocfilehash: 792b95b120f67afcd360730acbd783a3071388b2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77431489"
---
# <a name="protect-your-network-resources"></a>Ochrona zasobów sieciowych
Azure Security Center stale analizuje stan zabezpieczeń zasobów platformy Azure pod kątem najlepszych rozwiązań z zakresu zabezpieczeń sieci. Gdy Security Center identyfikuje potencjalne luki w zabezpieczeniach, tworzy zalecenia, które przeprowadzą Cię przez proces konfigurowania wymaganych kontrolek do zabezpieczania i zabezpieczania zasobów.

W tym artykule opisano stronę **sieci** w sekcji zabezpieczenia zasobów Security Center.

Aby zapoznać się z pełną listą zaleceń dotyczących sieci, zobacz [zalecenia dotyczące sieci](recommendations-reference.md#recs-network).

Ten artykuł dotyczy zaleceń, które dotyczą zasobów platformy Azure z punktu widzenia zabezpieczeń sieci. Centrum zaleceń dotyczących sieci wokół zapór nowej generacji, sieciowych grup zabezpieczeń, dostępu JIT do maszyny wirtualnej, nadmiernie ograniczających reguły ruchu przychodzącego i nie tylko. Aby zapoznać się z listą zaleceń dotyczących sieci i akcji korygowania, zobacz [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w Azure Security Center](security-center-recommendations.md).

> [!NOTE]
> Strona **sieci** umożliwia głębokie szczegółowe usługi Azure Resource Health z perspektywy sieci. Mapy sieci i adaptacyjne kontrolki sieci są dostępne tylko dla Azure Security Center warstwy Standardowa. W [przypadku korzystania z warstwy Bezpłatna możesz kliknąć ten przycisk, aby **wyświetlić starszą sieć** i otrzymać zalecenia dotyczące zasobów sieciowych](#legacy-networking).
>

Strona **Sieć** zawiera przegląd sekcji, w których można szczegółowe, aby uzyskać więcej informacji na temat kondycji zasobów sieciowych:

- Mapa sieci (tylko Azure Security Center warstwy Standardowa)
- Adaptacyjne wzmocnienie zabezpieczeń sieci
- Zalecenia dotyczące zabezpieczeń sieci.
- Blok starszej **sieci** (poprzedni blok sieci) 
 
[![Okienko sieć](./media/security-center-network-recommendations/networking-pane.png)](./media/security-center-network-recommendations/networking-pane.png#lightbox)

## <a name="network-map"></a>Mapa sieci
Interaktywna mapa sieci zapewnia widok graficzny z nakładkami zabezpieczeń, które zapewniają zalecenia i szczegółowe informacje na potrzeby zabezpieczania zasobów sieciowych. Za pomocą mapy można zobaczyć topologię sieci obciążeń platformy Azure, połączenia między maszynami wirtualnymi i podsieciami oraz możliwość przechodzenia do szczegółów z mapy do określonych zasobów i zaleceń dotyczących tych zasobów.

Aby otworzyć mapę sieci:

1. W Security Center w obszarze higiena zabezpieczeń zasobów wybierz pozycję **Sieć**.
2. W obszarze **mapa sieci** kliknij pozycję **Zobacz topologia**.
 
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


## <a name="legacy-networking"></a>Starsze sieci<a name ="legacy-networking"></a>

Jeśli nie masz Security Center warstwy Standardowa, w tej sekcji wyjaśniono, jak wyświetlić bezpłatne zalecenia dotyczące sieci.

Aby uzyskać dostęp do tych informacji, w bloku sieć kliknij pozycję **Wyświetl starszą sieć**. 

[![Starsze sieci](./media/security-center-network-recommendations/legacy-networking.png)](./media/security-center-network-recommendations/legacy-networking.png#lightbox)

### <a name="internet-facing-endpoints-section"></a>Sekcja Internet facing endpoints (Punkty końcowe umożliwiające dostęp do Internetu)
W sekcji **punkty końcowe** połączone z Internetem można zobaczyć maszyny wirtualne, które są aktualnie skonfigurowane z punktem końcowym mającym dostęp do Internetu i jego stan.

Ta tabela zawiera nazwę punktu końcowego, adres IP połączony z Internetem oraz bieżący stan ważności sieciowej grupy zabezpieczeń i rekomendacji zapory następnej generacji. Tabela jest posortowana według ważności.

### <a name="networking-topology-section"></a>Sekcja Topologia sieci
Sekcja **topologia sieci** zawiera hierarchiczny widok zasobów.

Ta tabela jest posortowana (maszyny wirtualne i podsieci) według ważności.

W tym widoku topologii pierwszy poziom wyświetla sieci wirtualnych. Drugi wyświetla podsieci, a trzeci poziom przedstawia maszyny wirtualne, które należą do tych podsieci. W prawej kolumnie jest wyświetlany bieżący stan zaleceń sieciowych grup zabezpieczeń dla tych zasobów.

Trzeci poziom przedstawia maszyny wirtualne, które są podobne do opisanych wcześniej. Możesz kliknąć dowolny zasób, aby dowiedzieć się więcej lub zastosować wymaganą kontrolę zabezpieczeń lub konfigurację.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o zaleceniach dotyczących innych typów zasobów platformy Azure, zobacz następujące tematy:

* [Ochrona maszyn i aplikacji w usłudze Azure Security Center](security-center-virtual-machine-protection.md)
* [Ochrona usługi Azure SQL w Azure Security Center](security-center-sql-service-recommendations.md)