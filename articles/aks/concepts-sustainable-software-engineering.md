---
title: Pojęcia — zrównoważony inżynieria oprogramowania w usłudze Azure Kubernetes Services (AKS)
description: Dowiedz się więcej o zrównoważonej inżynierii oprogramowania w usłudze Azure Kubernetes Service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: e179b49879b78b2bab738407984c0f50d161114b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100572674"
---
# <a name="sustainable-software-engineering-principles-in-azure-kubernetes-service-aks"></a>Zrównoważone Zasady inżynierii oprogramowania w usłudze Azure Kubernetes Service (AKS)

Zrównoważone Zasady inżynierii oprogramowania to zbiór kompetencji ułatwiających Definiowanie, kompilowanie i uruchamianie trwałych aplikacji. Ogólnym celem jest zmniejszenie poziomu węgla każdego aspektu aplikacji. [Zasady stałego inżynieria oprogramowania][principles-sse] mają przegląd zasad stałego inżynieria oprogramowania.

Ważnym pomysłem, aby zrozumieć, jak to jest trwały inżynieria oprogramowania, jest zmiana priorytetów i koncentracja. W wielu przypadkach oprogramowanie zostało zaprojektowane i uruchomione w sposób, który koncentruje się na szybkiej wydajności i małych opóźnieniach. Zrównoważona inżynieria oprogramowania koncentruje się na zredukowaniu możliwie dużej ilości emisji węgla. W niektórych przypadkach zastosowanie trwałych zasad inżynierii oprogramowania może zapewnić szybszą wydajność lub mniejsze opóźnienia, na przykład przez obniżenie łącznej liczby podróży w sieci. W innych przypadkach zmniejszenie emisji węgla może spowodować obniżenie wydajności lub zwiększone opóźnienia, takie jak opóźnianie obciążeń o niskim priorytecie. Przed uwzględnieniem zastosowania zasad dotyczących zrównoważonej inżynierii oprogramowania do aplikacji zapoznaj się z priorytetami, potrzebami i zaletami aplikacji.

## <a name="measure-and-optimize"></a>Mierzenie i optymalizacja

Aby obniżyć poziom węgla klastrów AKS, musisz zrozumieć, w jaki sposób zasoby klastra są używane. [Azure monitor][azure-monitor] zawiera szczegółowe informacje dotyczące użycia zasobów klastra, takie jak pamięć i użycie procesora CPU. Te dane mogą pomóc w poinformowaniu użytkownika o zmniejszeniu zużycia węgla w klastrze i zaobserwowaniu wpływu zmian. Możesz również zainstalować [Kalkulator Roztrwałości firmy Microsoft][sustainability-calculator] , aby zobaczyć zawartość węgla dla wszystkich zasobów platformy Azure.

## <a name="increase-resource-utilization"></a>Zwiększ wykorzystanie zasobów

Jednym z metod obniżenia poziomu węgla jest skrócenie czasu bezczynności zasobów obliczeniowych. Skrócenie czasu bezczynności obejmuje zwiększenie wykorzystania zasobów obliczeniowych. Jeśli na przykład w klastrze znajdują się cztery węzły, każde z nich działa o pojemności 50%, a wszystkie cztery węzły mają 50% niewykorzystanej wydajności pozostałe. Jeśli klaster został zredukowany do trzech węzłów, to to samo obciążenie spowoduje, że trzy węzły będą działać o pojemności 67%, zmniejszając nieużywaną pojemność do 33% na każdym węźle i zwiększając wykorzystanie.

> [!IMPORTANT]
> Rozważając wprowadzanie zmian w zasobach w klastrze, sprawdź, czy [Pule systemu][system-pools] mają wystarczającą ilość zasobów, aby zachować stabilność podstawowych składników systemu klastra. Nigdy nie należy zmniejszać zasobów klastra do punktu, w którym klaster może stać się niestabilny.

Po przejrzeniu wykorzystania klastra Rozważ użycie funkcji oferowanych przez [wiele pul węzłów][multiple-node-pools]. Można użyć [ustalania rozmiarów węzłów][node-sizing] , aby zdefiniować pule węzłów z określonymi profilami procesora i pamięci, co pozwala na dostosowanie węzłów do potrzeb związanych z obciążeniem. Zmiany rozmiarów węzłów na potrzeby obciążeń mogą pomóc w uruchamianiu kilku węzłów przy wyższym wykorzystaniu. Możesz również skonfigurować sposób [skalowania][scale] klastra i korzystania z skalowania w [poziomie w pionie][scale-horizontal] oraz automatycznego skalowania [klastra][scale-auto] , aby automatycznie skalować klaster na podstawie konfiguracji. Kontrolowanie sposobu, w jaki skalowanie klastra może pomóc w utrzymaniu wszystkich uruchomionych węzłów przy dużym obciążeniu, przy jednoczesnym zapewnieniu zmiany obciążenia klastra. W przypadkach, gdy obciążenie jest odporne na nagłe przerwy lub zakończenia, można użyć [pul dodatkowych][spot-pools] , aby wykorzystać możliwości bezczynności w ramach platformy Azure. Na przykład pule dodatkowe mogą dobrze współpracować w przypadku zadań wsadowych lub środowisk programistycznych.

Zwiększenie wykorzystania może również zmniejszyć liczbę nadmiernych węzłów, co zmniejsza zużycie energii przez [rezerwacje zasobów w każdym węźle][resource-reservations].

Przejrzyj również *żądania* procesora CPU i pamięci oraz *limity* w manifestach Kubernetes aplikacji. W miarę obniżenia ilości pamięci i procesora CPU klaster może korzystać z większej ilości pamięci i procesora CPU do uruchamiania innych obciążeń. Po uruchomieniu większej liczby obciążeń z niższym procesorem CPU i pamięci klaster zostaje bardziej gęsto przydzielony, co zwiększa wykorzystanie. Przy zmniejszaniu procesora i pamięci dla aplikacji zachowanie aplikacji może być obniżone lub niestabilne, jeśli te wartości są zbyt niskie. Przed zmianą liczby *żądań* procesora CPU i pamięci oraz *limitów* należy rozważyć uruchomienie niektórych testów porównawczych, aby zrozumieć, czy te wartości są odpowiednio ustawiane. Ponadto nigdy nie należy zmniejszać tych wartości do momentu, gdy aplikacja stanie się niestabilna.

## <a name="reduce-network-travel"></a>Zmniejsz liczbę podróży sieci

Zmniejszenie liczby żądań i odpowiedzi na odległość do i z klastra musi zwykle obniżyć zużycie energii elektrycznej przez urządzenia sieciowe i obniżyć emisję dwutlenku węgla. Po przejrzeniu ruchu sieciowego Rozważ utworzenie klastrów [w regionach][regions] bliżej źródła ruchu sieciowego. Możesz również użyć [usługi Azure Traffic Manager][azure-traffic-manager] , aby ułatwić kierowanie ruchu do najbliższego klastra i [grup umieszczania sąsiedztwa][proiximity-placement-groups] , aby zmniejszyć odległość między zasobami platformy Azure.

> [!IMPORTANT]
> Rozważając wprowadzanie zmian w sieci klastra, nigdy nie zmniejszaj ruchu sieciowego kosztem spełnienia wymagań dotyczących obciążenia. Na przykład użycie [stref dostępności][availability-zones] powoduje więcej ruchu sieciowego w klastrze, ale korzystanie z tej funkcji może być niezbędne do obsługi obciążenia.

## <a name="demand-shaping"></a>Kształtowanie popytu

Jeśli to możliwe, należy rozważyć zmianę zapotrzebowania na zasoby klastra na czasy lub regiony, w których można użyć nadmiarowej pojemności. Rozważmy na przykład zmianę czasu lub regionu zadania wsadowego w celu uruchomienia lub użycia [pul dodatkowych][spot-pools]. Rozważ również refaktoryzację aplikacji, aby użyć kolejki w celu odroczenia uruchomionych obciążeń, które nie wymagają natychmiastowego przetwarzania.

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