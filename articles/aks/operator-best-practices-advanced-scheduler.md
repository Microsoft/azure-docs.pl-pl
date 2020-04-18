---
title: Najważniejsze wskazówki dotyczące funkcji harmonogramu
titleSuffix: Azure Kubernetes Service
description: Poznaj najlepsze rozwiązania operatora klastra dotyczące korzystania z zaawansowanych funkcji harmonogramu, takich jak skazy i tolerancje, selektory węzłów i koligacja lub koligacja i koligacja między zasobami w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: d0d13a699d2559c6b4360c807721e0b748959382
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617533"
---
# <a name="best-practices-for-advanced-scheduler-features-in-azure-kubernetes-service-aks"></a>Najważniejsze wskazówki dotyczące zaawansowanych funkcji harmonogramu w usłudze Azure Kubernetes Service (AKS)

Podczas zarządzania klastrami w usłudze Azure Kubernetes Service (AKS) często należy izolować zespoły i obciążenia. Harmonogram Kubernetes udostępnia zaawansowane funkcje, które umożliwiają kontrolowanie, które zasobniki mogą być zaplanowane w niektórych węzłach lub jak aplikacje z wieloma zasobami mogą być odpowiednio dystrybuowane w klastrze. 

Ten artykuł dotyczący najlepszych rozwiązań koncentruje się na zaawansowanych funkcjach planowania kubernetes dla operatorów klastrów. W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Użyj skazy i tolerancji, aby ograniczyć, jakie zasobniki mogą być zaplanowane w węzłach
> * Nadaj preferencje zasobnikom uruchamiania w niektórych węzłach z selektorami węzłów lub koligalnością węzłów
> * Rozdziel lub zgrupuj razem strąki z koligalnością lub antykowinią

## <a name="provide-dedicated-nodes-using-taints-and-tolerations"></a>Zapewnienie dedykowanych węzłów przy użyciu skaz i tolerancji

**Wskazówki dotyczące najlepszych rozwiązań** — ograniczanie dostępu dla aplikacji intensywnie korzystających z zasobów, takich jak kontrolery transferu danych przychodzących, do określonych węzłów. Zachowaj zasoby węzłów dostępne dla obciążeń, które ich wymagają i nie zezwalaj na planowanie innych obciążeń w węzłach.

Podczas tworzenia klastra AKS można wdrażać węzły z obsługą procesora GPU lub dużą liczbą zaawansowanych procesorów. Te węzły są często używane do dużych obciążeń przetwarzania danych, takich jak uczenie maszynowe (ML) lub sztuczna inteligencja (AI). Ponieważ ten typ sprzętu jest zazwyczaj kosztownym zasobem węzła do wdrożenia, ogranicz obciążenia, które można zaplanować w tych węzłach. Zamiast tego można poświęcić niektóre węzły w klastrze do uruchamiania usług transferu przychodzącego i zapobiegania innym obciążeniom.

Ta obsługa różnych węzłów jest dostępna przy użyciu wielu pul węzłów. Klaster usługi AKS udostępnia co najmniej jedną pulę węzłów.

Harmonogram Kubernetes można używać taints i tolerancji, aby ograniczyć, jakie obciążenia można uruchomić w węzłach.

* **Skazy** jest stosowany do węzła, który wskazuje tylko określone zasobników mogą być zaplanowane na nich.
* **Tolerancja** jest następnie stosowana do zasobnika, który pozwala im *tolerować skazy* węzła.

Podczas wdrażania zasobnika do klastra AKS, Kubernetes planuje tylko zasobników w węzłach, gdzie tolerancja jest wyrównana z taint. Na przykład załóżmy, że masz pulę węzłów w klastrze AKS dla węzłów z obsługą gpu. Definiujesz nazwę, taką jak *gpu,* a następnie wartość planowania. Jeśli ta wartość zostanie ustawiona na *NoSchedule,* harmonogram Kubernetes nie może zaplanować zasobników w węźle, jeśli zasobnik nie definiuje odpowiedniej tolerancji.

```console
kubectl taint node aks-nodepool1 sku=gpu:NoSchedule
```

Za pomocą skazy zastosowanej do węzłów następnie należy zdefiniować tolerancję w specyfikacji zasobnika, która umożliwia planowanie w węzłach. Poniższy przykład definiuje `sku: gpu` `effect: NoSchedule` i tolerować skazy stosowane do węzła w poprzednim kroku:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: microsoft/samples-tf-mnist-demo:gpu
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

Po wdrożeniu tego zasobnika, na przykład przy użyciu `kubectl apply -f gpu-toleration.yaml`, Kubernetes można pomyślnie zaplanować zasobnika w węzłach z taint zastosowane. Ta izolacja logiczna umożliwia kontrolowanie dostępu do zasobów w klastrze.

Po zastosowaniu skazy, pracować z deweloperami aplikacji i właścicieli, aby umożliwić im definiowanie wymaganych tolerancji w ich wdrożeniach.

Aby uzyskać więcej informacji na temat skażenia i tolerancji, zobacz [stosowanie skażeń i tolerancji][k8s-taints-tolerations].

Aby uzyskać więcej informacji na temat używania wielu pul węzłów w ukasza, zobacz [Tworzenie wielu pul węzłów dla klastra w ucho.][use-multiple-node-pools]

### <a name="behavior-of-taints-and-tolerations-in-aks"></a>Zachowanie skazy i tolerancji w AKS

Podczas uaktualniania puli węzłów w uzywu AKS, skazy i tolerancje postępuj zgodnie z wzorcem zestawu, ponieważ są one stosowane do nowych węzłów:

- **Klastry domyślne korzystające z zestawów skalowania maszyn wirtualnych**
  - Załóżmy, że masz klaster dwuwęzły - *node1* i *node2*. Uaktualnienie puli węzłów.
  - Dwa dodatkowe węzły są tworzone, *node3* i *node4*, a skazy są przekazywane odpowiednio.
  - Oryginalne *node1* i *node2* są usuwane.

- **Klastry bez obsługi zestawu skalowania maszyny wirtualnej**
  - Ponownie załóżmy, że masz klaster dwuwęzły - *node1* i *node2*. Podczas uaktualniania tworzony jest dodatkowy węzeł *(węzeł3).*
  - Skazy z *węzła1* są stosowane do *węzła3,* a następnie *węzeł1* jest usuwany.
  - Tworzony jest kolejny nowy węzeł (o nazwie *node1*, ponieważ poprzedni *węzeł1* został usunięty), a *węzły2* są stosowane do nowego *węzła1*. Następnie *węzeł2* zostanie usunięty.
  - W istocie *węzeł1* staje się *node3*, a *węzeł2* staje się *węzłem1*.

Podczas skalowania puli węzłów w uzywu AKS, skazy i tolerancje nie są przewożona zgodnie z projektem.

## <a name="control-pod-scheduling-using-node-selectors-and-affinity"></a>Sterowanie planowaniem zasobników przy użyciu selektorów węzłów i koligacji

**Wskazówki dotyczące najlepszych praktyk** — sterowanie planowaniem zasobników w węzłach przy użyciu selektorów węzłów, koligacji węzła lub koligacji między zasobami. Te ustawienia umożliwiają harmonogramowi kubernetes logicznie izolować obciążenia, takie jak sprzęt w węźle.

Taints i tolerancji są używane do logicznego izolowania zasobów z twardego odcięcia - jeśli zasobnik nie toleruje skazy węzła, nie jest zaplanowane w węźle. Alternatywne podejście jest użycie selektorów węzłów. Należy oznaczyć węzły, takie jak wskazanie lokalnie dołączonego magazynu SSD lub dużej ilości pamięci, a następnie zdefiniować w specyfikacji zasobnika selektor węzła. Następnie kubernetes planuje te zasobników w pasującym węźle. W przeciwieństwie do tolerancji zasobników bez pasującego selektora węzłów można zaplanować w węzłach oznaczonych etykietą. To zachowanie umożliwia nieużywane zasoby w węzłach do wykorzystania, ale nadaje priorytet zasobnikom, które definiują pasujący selektor węzła.

Spójrzmy na przykład węzłów z dużą ilością pamięci. Te węzły mogą dawać pierwszeństwo zasobnikom, które żądają dużej ilości pamięci. Aby upewnić się, że zasoby nie siedzieć bezczynnie, umożliwiają one również inne zasobników do uruchomienia.

```console
kubectl label node aks-nodepool1 hardware:highmem
```

Specyfikacja zasobnika `nodeSelector` następnie dodaje właściwość do definiowania selektora węzłów, który pasuje do etykiety ustawionej w węźle:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: microsoft/samples-tf-mnist-demo:gpu
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

Korzystając z tych opcji harmonogramu, współpracować z deweloperami aplikacji i właścicieli, aby umożliwić im poprawnie zdefiniować ich specyfikacje zasobników.

Aby uzyskać więcej informacji na temat używania selektorów węzłów, zobacz [Przypisywanie zasobników do węzłów][k8s-node-selector].

### <a name="node-affinity"></a>Koligacja węzła

Selektor węzłów to podstawowy sposób przypisywania zasobników do danego węzła. Większa elastyczność jest dostępna przy użyciu *koligacji węzłów*. Za pomocą koligacji węzła można zdefiniować, co się stanie, jeśli zasobnik nie może być dopasowany do węzła. Można *wymagać, aby* harmonogram Kubernetes dopasowywał zasobnik z hostem oznaczonym etykietą. Możesz też *preferować* mecz, ale zezwalaj na zaplanowanie zasobnika na innym hoście, jeśli nie jest dostępny.

W poniższym przykładzie ustawia koligację węzła na *wymaganąDwulingSchedUlingIgnoredDuringExecution*. Ta koligacja wymaga harmonogramu Kubernetes używać węzła z pasującą etykietą. Jeśli węzeł nie jest dostępny, zasobnik musi czekać na kontynuowanie planowania. Aby umożliwić zaplanowanie zasobnika w innym węźle, można zamiast tego ustawić wartość *preferowanąDuringSchedulingIgnoreDuringExecution*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: microsoft/samples-tf-mnist-demo:gpu
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

*Część ustawienia IgnoredDuringExecution* wskazuje, że jeśli zmienią się etykiety węzłów, zasobnik nie powinien zostać wykluczony z węzła. Harmonogram Kubernetes używa tylko zaktualizowanych etykiet węzłów dla nowych zasobników, które są zaplanowane, a nie zasobników już zaplanowanych w węzłach.

Aby uzyskać więcej informacji, zobacz [Powinowactwo i anty-koligacji][k8s-affinity].

### <a name="inter-pod-affinity-and-anti-affinity"></a>Koligacja inter-pod i anty-koligacja

Jednym z ostatecznych podejść do harmonogramu Kubernetes logicznie izolować obciążeń jest przy użyciu koligacji inter-pod lub anty-koligacji. Ustawienia definiują, że zasobników *nie powinny* być zaplanowane w węźle, który ma istniejący pasujące zasobników lub że *powinny* być zaplanowane. Domyślnie harmonogram Kubernetes próbuje zaplanować wiele zasobników w zestawie replik w węzłach. Można zdefiniować bardziej szczegółowe reguły wokół tego zachowania.

Dobrym przykładem jest aplikacja sieci web, która również używa pamięci podręcznej Azure dla redis. Reguły antyko koligacji zasobników można użyć do żądania, aby harmonogram Kubernetes dystrybuował repliki między węzłami. Następnie można użyć reguł koligacji, aby upewnić się, że każdy składnik aplikacji sieci web jest zaplanowany na tym samym hoście co odpowiednia pamięć podręczna. Rozkład zasobników między węzłami wygląda następująco:

| **Węzeł 1** | **Węzeł 2** | **Węzeł 3** |
|------------|------------|------------|
| webapp-1   | webapp-2   | webapp-3   |
| pamięć podręczna-1    | pamięć podręczna-2    | pamięć podręczna-3    |

W tym przykładzie jest bardziej złożone wdrożenie niż użycie selektorów węzłów lub koligacji węzła. Wdrożenie daje kontrolę nad tym, jak Kubernetes planuje zasobników w węzłach i logicznie izolować zasoby. Aby uzyskać pełny przykład tej aplikacji sieci web z pamięcią podręczną Azure dla przykładu Redis, zobacz [Co-locate zasobników w tym samym węźle.][k8s-pod-affinity]

## <a name="next-steps"></a>Następne kroki

W tym artykule skupiono się na zaawansowanych funkcjach harmonogramu kubernetes. Aby uzyskać więcej informacji na temat operacji klastra w uzywce AKS, zobacz następujące najlepsze rozwiązania:

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
