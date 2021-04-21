---
title: Najlepsze rozwiązania dotyczące funkcji harmonogramu
titleSuffix: Azure Kubernetes Service
description: Poznaj najlepsze rozwiązania dotyczące operatora klastra dotyczące korzystania z zaawansowanych funkcji harmonogramu, takich jak nachylenia i tolerancje, selektory węzłów i koligacja lub koligacja i koligacja między zasobnikami w u Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: 971916c3fc903ff5d69db2e0f82fd884acf807b3
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831586"
---
# <a name="best-practices-for-advanced-scheduler-features-in-azure-kubernetes-service-aks"></a>Najlepsze rozwiązania dotyczące zaawansowanych funkcji harmonogramu w usłudze Azure Kubernetes Service (AKS)

Podczas zarządzania klastrami w Azure Kubernetes Service (AKS) często trzeba izolować zespoły i obciążenia. Zaawansowane funkcje udostępniane przez harmonogram usługi Kubernetes umożliwiają kontrolowanie:
* Zasobniki, które można zaplanować w niektórych węzłach.
* Sposób, w jaki aplikacje z wieloma zasobnikami mogą być odpowiednio dystrybuowane w klastrze. 

Ten artykuł z najlepszymi rozwiązaniami koncentruje się na zaawansowanych funkcjach planowania kubernetes dla operatorów klastra. W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Użyj funkcji taints i tolerations, aby ograniczyć zasobniki, które można zaplanować w węzłach.
> * Nadaj zasobnikom preferencję uruchamiania w określonych węzłach z selektorami węzłów lub koligacją węzłów.
> * Podziel lub pogrupuj zasobniki z koligacją między zasobnikami lub koligacją.

## <a name="provide-dedicated-nodes-using-taints-and-tolerations"></a>Zapewnianie dedykowanych węzłów przy użyciu funkcji taintów i tolerancji

> **Wskazówki dotyczące najlepszych rozwiązań:** 
>
> Ogranicz dostęp dla aplikacji intensywnie obciążanych zasobami, takich jak kontrolery ruchu wychodzącego, do określonych węzłów. Zachowaj zasoby węzłów dostępne dla obciążeń, które ich wymagają, i nie zezwalaj na planowanie innych obciążeń w węzłach.

Podczas tworzenia klastra usługi AKS można wdrażać węzły z obsługą procesora GPU lub dużą liczbą zaawansowanych procesorów CPU. Tych węzłów można używać w przypadku dużych obciążeń przetwarzania danych, takich jak uczenie maszynowe (ML) lub sztuczna inteligencja (AI). 

Ponieważ wdrożenie tego sprzętu zasobów węzła jest zwykle kosztowne, należy ograniczyć obciążenia, które można zaplanować w tych węzłach. Zamiast tego należy dedykować niektóre węzły w klastrze, aby uruchamiać usługi danych przychodzących i zapobiegać innym obciążeniom.

Ta obsługa różnych węzłów jest zapewniana przy użyciu wielu pul węzłów. Klaster usługi AKS udostępnia co najmniej jedną pulę węzłów.

Harmonogram usługi Kubernetes używa węzłów i tolerancji do ograniczania obciążeń, które można uruchamiać w węzłach.

* Zastosuj **awarii do węzła,** aby wskazać, że można na nich zaplanować tylko określone zasobniki.
* Następnie zastosuj **tolerancję do zasobnika,** aby *tolerować* nieumyślne awarii węzła.

Podczas wdrażania zasobnika w klastrze usługi AKS usługa Kubernetes planuje zasobniki tylko w węzłach, których ta wartość jest dopasowana do tolerancji. Załóżmy na przykład, że dodano pulę węzłów w klastrze usługi AKS dla węzłów z obsługą procesora GPU. Należy zdefiniować nazwę, taką jak *gpu,* a następnie wartość do planowania. Ustawienie tej wartości na *NoSchedule* ogranicza harmonogramowi kubernetes planowanie zasobników z niezdefiniowana tolerancją w węźle.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name taintnp \
    --node-taints sku=gpu:NoSchedule \
    --no-wait
```

Po zastosowaniu awarii do węzłów w puli węzłów zdefiniuje się tolerancję w specyfikacji zasobnika, która umożliwia planowanie na węzłach. W poniższym przykładzie zdefiniowano elementy i w celu tolerowania awarii zastosowanej do `sku: gpu` `effect: NoSchedule` puli węzłów w poprzednim kroku:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

Po wdrożeniu tego zasobnika przy użyciu rozwiązania kubernetes może pomyślnie zaplanować zasobnik na węzłach `kubectl apply -f gpu-toleration.yaml` z zastosowanym punktem. Ta izolacja logiczna umożliwia kontrolowanie dostępu do zasobów w klastrze.

W przypadku stosowania usterek należy współpracować z deweloperami i właścicielami aplikacji, aby umożliwić im definiowanie wymaganych tolerancji we własnych wdrożeniach.

Aby uzyskać więcej informacji na temat używania wielu pul węzłów w u usługi AKS, zobacz [Create and manage multiple node pools for a cluster in AKS][use-multiple-node-pools](Tworzenie wielu pul węzłów dla klastra w UKS i zarządzanie nimi).

### <a name="behavior-of-taints-and-tolerations-in-aks"></a>Zachowanie najechań i tolerancji w uścisku AKS

Gdy uaktualniasz pulę węzłów w użytą użytą do tego celu, talimy i tolerancje zgodnie z ustawionym wzorcem, ponieważ są one stosowane do nowych węzłów:

#### <a name="default-clusters-that-use-vm-scale-sets"></a>Klastry domyślne, które używają zestawów skalowania maszyn wirtualnych
Pulę [węzłów można za pomocą interfejsu][taint-node-pool] API usługi AKS skalować, aby nowo skalowane węzły odbierały określone wartości węzłów interfejsu API.

Załóżmy:
1. Rozpoczniesz od klastra z dwoma węzłami: *node1* i *node2.* 
1. Uaktualniasz pulę węzłów.
1. Tworzone są dwa dodatkowe węzły: *node3* i *node4.* 
1. Nachylenia są przekazywane odpowiednio.
1. Oryginalne *węzeł1* i *węzeł2* są usuwane.

#### <a name="clusters-without-vm-scale-set-support"></a>Klastry bez obsługi zestawu skalowania maszyn wirtualnych

Ponownie przyjmijmy, że:
1. Masz klaster z dwoma węzłami: *node1* i *node2.* 
1. Następnie uaktualniasz pulę węzłów.
1. Zostanie utworzony dodatkowy węzeł: *node3*.
1. Nachylenia z węzła *node1* są stosowane do *węzła 3*.
1. *węzeł node1* został usunięty.
1. Zostanie utworzony *nowy węzeł node1,* który zastąpi oryginalny *węzeł1*.
1. Na nowym węźle node1 są stosowane *łazy* *node2.* 
1. *węzeł node2* został usunięty.

W zasadzie *węzeł node1* staje *się węzłem 3,* a *węzeł2* staje się nowym *węzłem1*.

W przypadku skalowania puli węzłów w użytce AKS tańce i tolerancje nie są przenoszne zgodnie z projektem.

## <a name="control-pod-scheduling-using-node-selectors-and-affinity"></a>Planowanie zasobników sterowania przy użyciu selektorów węzłów i koligacji

> **Wskazówki dotyczące najlepszych rozwiązań** 
> 
> Kontroluj planowanie zasobników w węzłach przy użyciu selektorów węzłów, koligacji węzłów lub koligacji między zasobnikami. Te ustawienia umożliwiają harmonogramowi kubernetes logiczne izolowanie obciążeń, takich jak sprzęt w węźle.

Taints and tolerations logically isolate resources with a hard cut-off( Taints and tolerations logically isolate resources with a hard cut-off. Jeśli zasobnik nie toleruje niesprawnych danych węzła, nie jest on zaplanowany w węźle.

Alternatywnie można użyć selektorów węzłów. Można na przykład oznaczać węzły etykietami, aby wskazać lokalnie dołączony magazyn SSD lub dużą ilość pamięci, a następnie zdefiniować w specyfikacji zasobnika selektor węzła. Kubernetes planuje te zasobniki w zgodnym węźle.

W przeciwieństwie do tolerancji zasobniki bez pasującego selektora węzłów można nadal planować w węzłach oznaczonych etykietami. To zachowanie umożliwia wykorzystanie nieużywanych zasobów w węzłach, ale określa priorytety zasobników, które definiują zgodny selektor węzłów.

Przyjrzyjmy się przykładowi węzłów z dużą ilością pamięci. Te węzły mają priorytet dla zasobników, które żądają dużej ilości pamięci. Aby upewnić się, że zasoby nie są bezczynne, zezwalają również na uruchamianie innych zasobników. Następujące przykładowe polecenie dodaje pulę węzłów z etykietą *hardware=highmem* do klastra *myAKSCluster* *w grupie myResourceGroup.* Wszystkie węzły w tej puli węzłów będą mieć tę etykietę.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name labelnp \
    --node-count 1 \
    --labels hardware=highmem \
    --no-wait
```

Następnie specyfikacja zasobnika dodaje właściwość w celu zdefiniowania selektora `nodeSelector` węzłów, który odpowiada etykiecie ustawionej w węźle:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  nodeSelector:
      hardware: highmem
```

Korzystając z tych opcji harmonogramu, należy współpracować z deweloperami i właścicielami aplikacji, aby umożliwić im prawidłowe definiowanie specyfikacji zasobników.

Aby uzyskać więcej informacji na temat używania selektorów węzłów, zobacz [Przypisywanie zasobników do węzłów][k8s-node-selector].

### <a name="node-affinity"></a>Koligacja węzła

Selektor węzłów to podstawowe rozwiązanie do przypisywania zasobników do danego węzła. *Koligacja węzłów* zapewnia większą elastyczność, umożliwiając definiowanie, co się stanie, jeśli nie można dopasować zasobnika do węzła. Oto co możesz zrobić: 
* *Wymagaj,* aby harmonogram usługi Kubernetes był taki sam jak zasobnik z hostem z etykietą. Lub:
* *Preferuj* dopasowanie, ale zezwalaj na zaplanowanie zasobnika na innym hoście, jeśli dopasowanie nie jest dostępne.

Poniższy przykład ustawia koligację węzła na *requiredDuringSchedulingIgnoredDuringExecution*. Ta koligacja wymaga, aby harmonogram kubernetes używał węzła z pasującą etykietą. Jeśli żaden węzeł nie jest dostępny, zasobnik musi czekać na kontynuowanie planowania. Aby umożliwić zaplanowanie zasobnika w innym węźle, można zamiast tego ustawić wartość ***preferred** DuringSchedulingIgnoreDuringExecution*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: hardware
            operator: In
            values: highmem
```

Część *IgnoredDuringExecution* ustawienia wskazuje, że zasobnika nie należy eksmitować z węzła, jeśli zmienią się etykiety węzłów. Harmonogram kubernetes używa tylko zaktualizowanych etykiet węzłów dla nowych zaplanowanych zasobników, a nie zasobników już zaplanowanych w węzłach.

Aby uzyskać więcej informacji, zobacz [Koligacja i koligacja][k8s-affinity].

### <a name="inter-pod-affinity-and-anti-affinity"></a>Koligacja i koligacja między zasobnikami

Ostatnim podejściem do logicznego izolowania obciążeń przez harmonogram kubernetes jest użycie koligacji między zasobnikami lub koligacji. Te ustawienia definiują,  że zasobniki  nie powinny być lub powinny być zaplanowane w węźle, który ma istniejący pasujący zasobnik. Domyślnie harmonogram kubernetes próbuje zaplanować wiele zasobników w replice ustawionej między węzłami. Można zdefiniować bardziej szczegółowe reguły dotyczące tego zachowania.

Na przykład masz aplikację internetową, która również używa Azure Cache for Redis. 
1. Reguły ochrony koligacji zasobników są służące do żądania, aby harmonogram kubernetes dystrybuował repliki między węzłami. 
1. Reguły koligacji zapewniają, że każdy składnik aplikacji internetowej jest zaplanowany na tym samym hoście co odpowiednia pamięć podręczna. 

Rozkład zasobników między węzłami wygląda podobnie do następującego przykładu:

| **Węzeł 1** | **Węzeł 2** | **Węzeł 3** |
|------------|------------|------------|
| webapp-1   | webapp-2   | webapp-3   |
| pamięć podręczna 1    | pamięć podręczna 2    | pamięć podręczna 3    |

Koligacja i koligacja między zasobnikami zapewniają bardziej złożone wdrożenie niż selektory węzłów lub koligacja węzłów. Wdrożenie umożliwia logiczne odizolowanie zasobów i kontrolowanie sposobu planowania zasobników w węzłach przez usługę Kubernetes. 

Aby uzyskać kompletny przykład tej aplikacji internetowej z Azure Cache for Redis, zobacz Co-locate pods on the same node (Lokalizowanie [zasobników w tym samym węźle).][k8s-pod-affinity]

## <a name="next-steps"></a>Następne kroki

Ten artykuł koncentruje się na zaawansowanych funkcjach harmonogramu kubernetes. Aby uzyskać więcej informacji na temat operacji klastra w u usługi AKS, zobacz następujące najlepsze rozwiązania:

* [Wielodostęp i izolacja klastra][aks-best-practices-scheduler]
* [Podstawowe funkcje harmonogramu kubernetes][aks-best-practices-scheduler]
* [Uwierzytelnianie i autoryzacja][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->
[k8s-taints-tolerations]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[k8s-node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[k8s-affinity]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#affinity-and-anti-affinity
[k8s-pod-affinity]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#always-co-located-in-the-same-node

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[aks-best-practices-identity]: operator-best-practices-identity.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[taint-node-pool]: use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool
