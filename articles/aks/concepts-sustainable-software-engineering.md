---
title: Pojęcia — zrównoważony inżynieria oprogramowania w usłudze Azure Kubernetes Services (AKS)
description: Dowiedz się więcej o zrównoważonej inżynierii oprogramowania w usłudze Azure Kubernetes Service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 03/29/2021
ms.openlocfilehash: c43c65dfa2f3930510bd59aaa24c798525bd691b
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011495"
---
# <a name="sustainable-software-engineering-principles-in-azure-kubernetes-service-aks"></a>Zrównoważone Zasady inżynierii oprogramowania w usłudze Azure Kubernetes Service (AKS)

Zrównoważone Zasady inżynierii oprogramowania to zbiór kompetencji ułatwiających Definiowanie, kompilowanie i uruchamianie trwałych aplikacji. Ogólnym celem jest zmniejszenie poziomu węgla każdego aspektu aplikacji. [Zasady stałego inżynieria oprogramowania][principles-sse] mają przegląd zasad stałego inżynieria oprogramowania.

Zrównoważona inżynieria oprogramowania to zmiana priorytetów i fokus. W wielu przypadkach większość oprogramowania jest zaprojektowana i uruchamiana z wyróżnioną dużą wydajnością i małymi opóźnieniami. Tymczasem, zrównoważony inżynieria oprogramowania koncentruje się na zmniejszeniu możliwie największej emisji węgla. Rozważ następujące kwestie:

* Zastosowanie stałych zasad inżynierii oprogramowania może zapewnić szybszą wydajność lub mniejsze opóźnienia, na przykład przez obniżenie łącznej liczby podróży w sieci. 
* Zmniejszenie emisji węgla może spowodować obniżenie wydajności lub zwiększone opóźnienia, takie jak opóźnianie obciążeń o niskim priorytecie. 

Przed zastosowaniem w aplikacji trwałych zasad inżynierii oprogramowania należy zapoznać się z priorytetami, potrzebami i zaletami aplikacji.

## <a name="measure-and-optimize"></a>Mierzenie i optymalizacja

Aby obniżyć poziom węgla klastrów AKS, musisz zrozumieć, w jaki sposób zasoby klastra są używane. [Azure monitor][azure-monitor] zawiera szczegółowe informacje dotyczące użycia zasobów klastra, takie jak pamięć i użycie procesora CPU. Te dane informuje o zmniejszeniu zużycia węgla w klastrze i obserwują efekt zmian. 

Możesz również zainstalować [Kalkulator Roztrwałości firmy Microsoft][sustainability-calculator] , aby zobaczyć zawartość węgla dla wszystkich zasobów platformy Azure.

## <a name="increase-resource-utilization"></a>Zwiększ wykorzystanie zasobów

Jednym z metod obniżenia poziomu węgla jest skrócenie czasu bezczynności. Skrócenie czasu bezczynności obejmuje zwiększenie wykorzystania zasobów obliczeniowych. Na przykład:
1. W klastrze wprowadzono cztery węzły, z których każda działa o pojemności 50%. W związku z tym wszystkie cztery węzły mają 50% niewykorzystane zdolności produkcyjne pozostałe. 
1. Klaster został zredukowany do trzech węzłów, z których każda działa o pojemności 67%, przy jednoczesnym obciążeniu. W każdym węźle można pomyślnie obniżyć nieużywaną pojemność do 33% i zwiększyć wykorzystanie.

> [!IMPORTANT]
> Podczas rozważania zmiany zasobów w klastrze Sprawdź, czy [Pule systemu][system-pools] mają wystarczającą ilość zasobów, aby zachować stabilność głównych składników systemu klastra. **Nigdy nie** należy zmniejszać zasobów klastra do punktu, w którym klaster może stać się niestabilny.

Po przejrzeniu wykorzystania klastra Rozważ użycie funkcji oferowanych przez [wiele pul węzłów][multiple-node-pools]: 

* Ustalanie rozmiarów węzłów

    Użyj [ustalania rozmiarów węzłów][node-sizing] , aby zdefiniować pule węzłów z określonymi profilami procesora i pamięci, co pozwala na dostosowanie węzłów do potrzeb związanych z obciążeniami. Zmieniając rozmiar węzłów na potrzeby związane z obciążeniem, można uruchomić kilka węzłów na wyższym poziomie. 

* Skalowanie klastra

    Skonfiguruj sposób [skalowania][scale]klastra. Aby automatycznie skalować klaster na podstawie konfiguracji, należy użyć skalowania w [poziomie][scale-horizontal] poniżej i automatycznego skalowania [klastra][scale-auto] . Kontroluj sposób skalowania klastra, aby zapewnić, że wszystkie węzły działają przy wysokim obciążeniu, jednocześnie zachowując synchronizację ze zmianami obciążenia klastra. 

* Pule punktów

    W przypadkach, gdy obciążenie jest odporne na nagłe przerwy lub zakończenia, można użyć [pul dodatkowych][spot-pools]. Pule kontroli wykorzystują możliwości bezczynności w ramach platformy Azure. Na przykład pule dodatkowe mogą dobrze współpracować w przypadku zadań wsadowych lub środowisk programistycznych.

> [!NOTE]
>Zwiększenie wykorzystania może również zmniejszyć liczbę nadmiernych węzłów, co zmniejsza zużycie energii przez [rezerwacje zasobów w każdym węźle][resource-reservations].

Na koniec Przejrzyj *żądania* procesora CPU i pamięci oraz *limity* w manifestach Kubernetes aplikacji. 
* W przypadku obniżenia ilości pamięci i procesora CPU klaster jest dostępny do uruchamiania innych obciążeń. 
* Po uruchomieniu większej liczby obciążeń z niższym procesorem CPU i pamięci, klaster będzie bardziej gęsto przydzielony, co zwiększa wykorzystanie. 

Przy zmniejszaniu procesora i pamięci dla aplikacji zachowanie aplikacji może być obniżone lub niestabilne, jeśli wartości procesora i pamięci są zbyt niskie. Przed zmianą liczby *żądań* procesora CPU i pamięci oraz *limitów* należy uruchomić testy porównawcze, aby sprawdzić, czy wartości są odpowiednio ustawione. Nigdy nie należy zmniejszać tych wartości do punktu niestabilności aplikacji.

## <a name="reduce-network-travel"></a>Zmniejsz liczbę podróży sieci

Zmniejszając liczbę żądań i odpowiedzi na odległość do i z klastra, można zmniejszyć emisję węgla i zużycie energii elektrycznej przez urządzenia sieciowe. Po przejrzeniu ruchu sieciowego Rozważ utworzenie klastrów [w regionach][regions] bliżej źródła ruchu sieciowego. Korzystając z [usługi azure Traffic Manager][azure-traffic-manager] , można kierować ruchem do najbliższego klastra i [grup umieszczania bliskości][proiximity-placement-groups] i zmniejszyć odległość między zasobami platformy Azure.

> [!IMPORTANT]
> Rozważając wprowadzanie zmian w sieci klastra, nigdy nie zmniejszaj ruchu sieciowego kosztem spełnienia wymagań dotyczących obciążenia. Na przykład podczas korzystania z [stref dostępności][availability-zones] powoduje więcej ruchu sieciowego w klastrze, strefy dostępności mogą być niezbędne do obsługi obciążenia.

## <a name="demand-shaping"></a>Kształtowanie popytu

Jeśli to możliwe, należy rozważyć zmianę zapotrzebowania na zasoby klastra na czasy lub regiony, w których można użyć nadmiarowej pojemności. Rozważmy na przykład:
* Zmiana czasu lub regionu zadania usługi Batch do uruchomienia.
* Korzystanie z [pul dodatkowych][spot-pools]. 
* Refaktoryzacja aplikacji w celu użycia kolejki w celu odroczenia uruchomionych obciążeń, które nie wymagają natychmiastowego przetwarzania.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o funkcjach AKS wymienionych w tym artykule:

* [Pule wielu węzłów][multiple-node-pools]
* [Ustalanie rozmiarów węzłów][node-sizing]
* [Skalowanie klastra][scale]
* [Narzędzie do automatycznego skalowania zasobników w poziomie][scale-horizontal]
* [Narzędzie do automatycznego skalowania klastra][scale-auto]
* [Pule punktów][spot-pools]
* [Pule systemu][system-pools]
* [Rezerwacje zasobów][resource-reservations]
* [Grupy umieszczania w pobliżu][proiximity-placement-groups]
* [Strefy dostępności][availability-zones]

[availability-zones]: availability-zones.md
[azure-monitor]: ../azure-monitor/containers/container-insights-overview.md
[azure-traffic-manager]: ../traffic-manager/traffic-manager-overview.md
[proiximity-placement-groups]: reduce-latency-ppg.md
[regions]: faq.md#which-azure-regions-currently-provide-aks
[resource-reservations]: concepts-clusters-workloads.md#resource-reservations
[scale]: concepts-scale.md
[scale-auto]: concepts-scale.md#cluster-autoscaler
[scale-horizontal]: concepts-scale.md#horizontal-pod-autoscaler
[spot-pools]: spot-node-pool.md
[multiple-node-pools]: use-multiple-node-pools.md
[node-sizing]: use-multiple-node-pools.md#specify-a-vm-size-for-a-node-pool
[sustainability-calculator]: https://azure.microsoft.com/blog/microsoft-sustainability-calculator-helps-enterprises-analyze-the-carbon-emissions-of-their-it-infrastructure/
[system-pools]: use-system-pools.md
[principles-sse]: https://docs.microsoft.com/learn/modules/sustainable-software-engineering-overview/