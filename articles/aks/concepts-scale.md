---
title: Pojęcia — skalowanie aplikacji w usłudze Azure Kubernetes Services (AKS)
description: Dowiedz się więcej na temat skalowania w usłudze Azure Kubernetes Service (AKS), w tym automatycznego skalowania, automatycznego skalowania klastra i łącznika Azure Container Instances.
services: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.openlocfilehash: b72ed7cefc6a16eb484e1337dbd64e5f069a2201
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94686042"
---
# <a name="scaling-options-for-applications-in-azure-kubernetes-service-aks"></a>Opcje skalowania aplikacji w usłudze Azure Kubernetes Service 

Gdy uruchamiasz aplikacje w usłudze Azure Kubernetes Service (AKS), może być konieczne zwiększenie lub zmniejszenie ilości zasobów obliczeniowych. Liczba wystąpień aplikacji, które należy zmienić, może również wymagać zmiany liczby podstawowych węzłów Kubernetes. Może być również konieczne szybkie udostępnienie dużej liczby dodatkowych wystąpień aplikacji.

W tym artykule przedstawiono podstawowe pojęcia, które ułatwiają skalowanie aplikacji w programie AKS:

- [Skalowanie ręczne](#manually-scale-pods-or-nodes)
- [Skalowanie w poziomie na pionie (HPA)](#horizontal-pod-autoscaler)
- [Narzędzie do automatycznego skalowania klastra](#cluster-autoscaler)
- [Integracja z usługą Azure Container Instance (ACI) z usługą AKS](#burst-to-azure-container-instances)

## <a name="manually-scale-pods-or-nodes"></a>Ręczne skalowanie z podziałów lub węzłów

W celu przetestowania, w jaki sposób aplikacja reaguje na zmianę w dostępnych zasobach i stanie, można ręcznie skalować repliki (na podst.) i węzły. Ręczne skalowanie zasobów pozwala także zdefiniować określoną ilość zasobów do użycia w celu utrzymania stałego kosztu, takiego jak liczba węzłów. Aby ręcznie skalować, należy zdefiniować replikę lub liczbę węzłów. Interfejs API Kubernetes następnie tworzy harmonogram tworzenia dodatkowych węzłów lub opróżniania w oparciu o tę replikę lub liczbę węzłów.

Podczas skalowania węzłów w dół interfejs API Kubernetes wywołuje odpowiedni interfejs API obliczeń platformy Azure związany z typem obliczeń używanym przez klaster. Na przykład w przypadku klastrów opartych na VM Scale Sets logiki do wybierania węzłów do usunięcia jest określana przez VM Scale Sets API. Aby dowiedzieć się więcej o tym, jak węzły są wybrane do usunięcia podczas skalowania, zobacz [często zadawane pytania](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#if-i-reduce-my-scale-set-capacity-from-20-to-15-which-vms-are-removed)dotyczące usługi VMSS.

Aby rozpocząć pracę z ręcznym skalowaniem i węzłami, zobacz [skalowanie aplikacji w AKS][aks-scale].

## <a name="horizontal-pod-autoscaler"></a>Narzędzie do automatycznego skalowania zasobników w poziomie

Kubernetes korzysta ze skalowania poziomego na poziomie, aby monitorować zapotrzebowanie na zasoby i automatycznie skalować liczbę replik. Domyślnie funkcja automatycznego skalowania w poziomie nie sprawdza interfejsu API metryk co 30 sekund dla wszystkich wymaganych zmian w liczniku replik. Gdy zmiany są wymagane, liczba replik zostaje odpowiednio zwiększona lub zmniejszona. Skalowanie w poziomie na przykład działa z klastrami AKS, które wdrożyły serwer metryk dla Kubernetes 1.8 +.

![Skalowanie automatyczne w poziomie Kubernetes](media/concepts-scale/horizontal-pod-autoscaling.png)

Podczas konfigurowania automatycznego skalowania w poziomie pod kątem danego wdrożenia należy określić minimalną i maksymalną liczbę replik, które mogą być uruchamiane. Należy również zdefiniować metrykę do monitorowania i oprzeć wszelkie decyzje dotyczące skalowania, na przykład użycie procesora CPU.

Aby rozpocząć pracę z skalowaniem w poziomie, w AKS, zobacz [Skalowanie automatyczne w AKS][aks-hpa].

### <a name="cooldown-of-scaling-events"></a>Cooldown zdarzeń skalowania

Ponieważ funkcja automatycznego skalowania w poziomie nie sprawdza interfejsu API metryk co 30 sekund, poprzednie zdarzenia skalowania mogą nie zostać pomyślnie zakończone przed innym sprawdzaniem. Takie zachowanie może spowodować, że funkcja automatycznego skalowania w poziomie nie zmienia liczby replik przed upływem poprzedniego zdarzenia skalowania, które może odebrać obciążenie aplikacji, a wymagania dotyczące zasobów odpowiednio dostosować.

Aby zminimalizować zdarzenia wyścigu, ustawiana jest wartość opóźnienia. Ta wartość określa, jak długo funkcja automatycznego skalowania w poziomie musi czekać po zdarzeniu skalowania, zanim będzie możliwe wyzwolenie innego zdarzenia skalowania. Takie zachowanie umożliwia uwzględnienie nowej liczby replik i interfejsu API metryk w celu odzwierciedlenia rozproszonego obciążenia. Nie ma [opóźnienia dla zdarzeń skalowania w zakresie od Kubernetes 1,12](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/#support-for-cooldown-delay), jednak opóźnienie dla zdarzeń skalowania w dół jest domyślnie ustawione na 5 minut.

Obecnie nie można dostosowywać tych wartości cooldown z domyślnego.

## <a name="cluster-autoscaler"></a>Narzędzie do automatycznego skalowania klastra

Aby odpowiedzieć na zmieniające się zapotrzebowanie na żądanie, Kubernetes ma automatyczne skalowanie klastra, który dostosowuje liczbę węzłów na podstawie żądanych zasobów obliczeniowych w puli węzłów. Domyślnie Automatyczne skalowanie klastra sprawdza serwer interfejsu API metryk co 10 sekund dla wszystkich wymaganych zmian w liczniku węzłów. Jeśli automatyczne skalowanie klastra ustali, że wymagana jest zmiana, liczba węzłów w klastrze AKS zostaje odpowiednio zwiększona lub obniżona. Automatyczne skalowanie klastra współpracuje z Kubernetesami AKS z obsługą RBAC, w których działa system Kubernetes 1.10. x lub nowszy.

![Automatyczne skalowanie klastra Kubernetes](media/concepts-scale/cluster-autoscaler.png)

Automatyczne skalowanie klastra jest zwykle używane razem ze skalowaniem w poziomie. W związku z tym automatyczne skalowanie w poziomie, zwiększa lub zmniejsza liczbę zasobników na podstawie zapotrzebowania aplikacji, a automatyczne skalowanie klastra dostosowuje liczbę węzłów w miarę potrzeb, aby odpowiednio uruchamiać te dodatkowe zasobniki.

Aby rozpocząć pracę z automatycznym skalowaniem klastra w programie AKS, zobacz artykuł [Automatyczne skalowanie klastra na AKS][aks-cluster-autoscaler].

### <a name="scale-out-events"></a>Skalowanie zdarzeń w poziomie

Jeśli węzeł nie ma wystarczających zasobów obliczeniowych do uruchomienia żądanego elementu, to nie może postępować przez proces planowania. Nie można uruchomić programu pod warunkiem, że dodatkowe zasoby obliczeniowe nie są dostępne w ramach puli węzłów.

Gdy automatyczne skalowanie klastra zostanie powiadomione o tym, że nie można zaplanować wystąpienia ograniczeń zasobów puli węzłów, liczba węzłów w puli węzłów zostanie zwiększona, aby zapewnić dodatkowe zasoby obliczeniowe. Jeśli te dodatkowe węzły zostały pomyślnie wdrożone i dostępne do użycia w puli węzłów, zostaną zaplanowane do uruchomienia na nich.

Jeśli aplikacja wymaga szybkiego skalowania, niektóre zasobniki mogą pozostać w stanie oczekującym na zaplanowanie do momentu, aż dodatkowe węzły wdrożone przez automatyczne skalowanie klastra będą mogły akceptować zaplanowane zasobniki. W przypadku aplikacji, które mają duże wymagania dotyczące serii, można skalować z węzłami wirtualnymi i Azure Container Instances.

### <a name="scale-in-events"></a>Skalowanie w zdarzeniach

Automatyczne skalowanie klastra monitoruje również stan planowania pod kątem węzłów, które nie otrzymały ostatnio nowych żądań planowania. Ten scenariusz wskazuje, że Pula węzłów ma więcej zasobów obliczeniowych niż jest wymagane, i liczbę węzłów można zmniejszyć.

Węzeł, który przekazuje próg nie jest już wymagany przez 10 minut, jest domyślnie zaplanowany do usunięcia. Gdy wystąpi taka sytuacja, planowane jest uruchomienie w innych węzłach w puli węzłów, a automatyczne skalowanie klastra zmniejsza liczbę węzłów.

Aplikacje mogą napotkać pewne zakłócenia, ponieważ w przypadku zaplanowania automatycznego skalowania klastra w różnych węzłach jest zaplanowana liczba węzłów. Aby zminimalizować zakłócenia, należy unikać stosowania aplikacji korzystających z jednego wystąpienia.

## <a name="burst-to-azure-container-instances"></a>Seria do Azure Container Instances

Aby szybko skalować klaster AKS, możesz zintegrować z usługą Azure Container Instances (ACI). Kubernetes ma wbudowane składniki umożliwiające skalowanie repliki i liczby węzłów. Jeśli jednak aplikacja wymaga natychmiastowego skalowania, skalowanie w poziomie poniżej może zaplanować więcej zasobów obliczeniowych, niż można dostarczyć w ramach puli węzłów. W przypadku skonfigurowania ten scenariusz spowoduje wyzwolenie automatycznego skalowania klastra w celu wdrożenia dodatkowych węzłów w puli węzłów, ale może potrwać kilka minut, zanim te węzły przekroczą obsługę administracyjną, a następnie zezwoli na uruchamianie na nich Kubernetes.

![Kubernetes skalowanie serii ACI](media/concepts-scale/burst-scaling.png)

ACI umożliwia szybkie wdrażanie wystąpień kontenerów bez dodatkowych obciążeń związanych z infrastrukturą. Po nawiązaniu połączenia z usługą AKS ACI staną się zabezpieczonym, logicznym rozszerzeniem klastra AKS. Składnik [węzłów wirtualnych][virtual-nodes-cli] , który jest oparty na [wirtualnej Kubelet][virtual-kubelet], jest instalowany w klastrze AKS, który przedstawia ACI jako wirtualny węzeł Kubernetes. Kubernetes może następnie zaplanować wystąpienia, które są uruchamiane jako instancje ACI za poorednictwem węzłów wirtualnych, a nie jako zasobników w węzłach maszyn wirtualnych bezpośrednio w klastrze AKS.

Aplikacja nie wymaga modyfikacji, aby używać węzłów wirtualnych. Wdrożenia mogą skalować między AKS i ACI i bez opóźnień, ponieważ automatyczne skalowanie klastra wdraża nowe węzły w klastrze AKS.

Węzły wirtualne są wdrażane w dodatkowej podsieci w tej samej sieci wirtualnej co klaster AKS. Ta konfiguracja sieci wirtualnej umożliwia zabezpieczenie ruchu między ACI i AKS. Podobnie jak w przypadku klastra AKS wystąpienie usługi ACI jest bezpiecznym, logicznym zasobem obliczeniowym odizolowanym od innych użytkowników.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć skalowanie aplikacji, najpierw postępuj zgodnie z [przewodnikiem Szybki Start, aby utworzyć klaster AKS przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart]. Następnie można rozpocząć ręczne lub automatyczne skalowanie aplikacji w klastrze AKS:

- Ręczne [skalowanie][aks-manually-scale-pods] z podziałów lub [węzłów][aks-manually-scale-nodes]
- Korzystanie z [automatycznego skalowania w poziomie][aks-hpa]
- Korzystanie z [automatycznego skalowania klastra][aks-cluster-autoscaler]

Aby uzyskać więcej informacji na temat podstawowych pojęć związanych z Kubernetes i AKS, zobacz następujące artykuły:

- [Kubernetes/AKS klastrów i obciążeń][aks-concepts-clusters-workloads]
- [Kubernetes/AKS, dostęp i tożsamość][aks-concepts-identity]
- [Zabezpieczenia Kubernetes/AKS][aks-concepts-security]
- [Sieci wirtualne Kubernetes/AKS][aks-concepts-network]
- [Magazyn Kubernetes/AKS][aks-concepts-storage]

<!-- LINKS - external -->
[virtual-kubelet]: https://virtual-kubelet.io/

<!-- LINKS - internal -->
[aks-quickstart]: kubernetes-walkthrough.md
[aks-hpa]: tutorial-kubernetes-scale.md#autoscale-pods
[aks-scale]: tutorial-kubernetes-scale.md
[aks-manually-scale-pods]: tutorial-kubernetes-scale.md#manually-scale-pods
[aks-manually-scale-nodes]: tutorial-kubernetes-scale.md#manually-scale-aks-nodes
[aks-cluster-autoscaler]: ./cluster-autoscaler.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-network]: concepts-network.md
[virtual-nodes-cli]: virtual-nodes-cli.md
