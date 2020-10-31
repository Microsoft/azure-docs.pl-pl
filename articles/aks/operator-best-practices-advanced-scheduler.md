---
title: Najlepsze rozwiązania dotyczące funkcji usługi Scheduler
titleSuffix: Azure Kubernetes Service
description: Zapoznaj się z najlepszymi rozwiązaniami operatora klastra dotyczącymi korzystania z zaawansowanych funkcji harmonogramu, takich jak przyrządy i tolerowanie, selektory węzłów i koligacja lub koligacja między innymi i ochrona w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: c0c1f587b4e52607e9466300f976a52874c9e5ad
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93125635"
---
# <a name="best-practices-for-advanced-scheduler-features-in-azure-kubernetes-service-aks"></a>Najlepsze rozwiązania dotyczące zaawansowanych funkcji harmonogramu w usłudze Azure Kubernetes Service (AKS)

Podczas zarządzania klastrami w usłudze Azure Kubernetes Service (AKS) często trzeba izolować zespoły i obciążenia. Usługa Kubernetes Scheduler udostępnia zaawansowane funkcje, które umożliwiają kontrolowanie, które z nich można zaplanować na niektórych węzłach, lub jak aplikacje wieloskładnikowe mogą być odpowiednio dystrybuowane w klastrze. 

Te najlepsze rozwiązania koncentrują się na zaawansowanych funkcjach planowania Kubernetes dla operatorów klastra. W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Użyj przydziałów i tolerowania, aby ograniczyć liczbę elementów, które można zaplanować w węzłach
> * Nadaj preferencjom, które mają być uruchamiane na niektórych węzłach przy użyciu selektorów węzłów lub koligacji węzłów
> * Podział na siebie lub grupowanie z użyciem koligacji między

## <a name="provide-dedicated-nodes-using-taints-and-tolerations"></a>Zapewnianie dedykowanych węzłów przy użyciu przydzielonych im i odtolerowaeń

**Wskazówki dotyczące najlepszych** rozwiązań — ograniczanie dostępu do aplikacji intensywnie korzystających z zasobów, takich jak kontrolery ruchu przychodzącego, do określonych węzłów. Przechowuj zasoby węzła dla obciążeń, które ich wymagają, i nie Zezwalaj na planowanie innych obciążeń w węzłach.

Podczas tworzenia klastra AKS można wdrożyć węzły z obsługą procesora GPU lub dużą liczbą zaawansowanych procesorów CPU. Te węzły są często używane do obciążeń przetwarzania dużych ilości danych, takich jak uczenie maszynowe (ML) lub Sztuczna inteligencja (AI). Ponieważ ten typ sprzętu jest zazwyczaj kosztownym zasobem węzła do wdrożenia, Ogranicz obciążenia, które można zaplanować w tych węzłach. Możesz zamiast tego chcieć przeznaczyć niektóre węzły w klastrze do uruchamiania usług przychodzących i uniemożliwić inne obciążenia.

Ta obsługa różnych węzłów jest zapewniana przy użyciu wielu pul węzłów. Klaster AKS zawiera co najmniej jedną pulę węzłów.

Harmonogram Kubernetes może używać przyniesień i tolerowanych elementów w celu ograniczenia obciążeń, które mogą być uruchamiane w węzłach.

* Do **węzła jest stosowany** obiekt, który wskazuje na ich zaplanowanie tylko określonych zasobników.
* **Tolerowana** jest następnie stosowana do elementu, który umożliwia im *tolerowanie* kształtu węzła.

W przypadku wdrażania programu pod kątem klastra AKS, Kubernetes tylko planuje w węzłach, które są wyrównane z przydziałami. Załóżmy na przykład, że masz pulę węzłów w klastrze AKS dla węzłów z obsługą procesora GPU. Należy zdefiniować nazwę, na przykład *GPU* , a następnie wartość planowania. Jeśli ustawisz tę wartość na *NoSchedule* , usługa Kubernetes Scheduler nie będzie mogła zaplanować w węźle, czy nie zdefiniują odpowiednich wartości dopuszczalnych.

```console
kubectl taint node aks-nodepool1 sku=gpu:NoSchedule
```

Po zastosowaniu przeznaczenie do węzłów należy zdefiniować tolerowanie w specyfikacji pod, która umożliwia planowanie w węzłach. W poniższym przykładzie zdefiniowano `sku: gpu` i `effect: NoSchedule` można tolerować przebarwienie zastosowane do węzła w poprzednim kroku:

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

Gdy ten temat zostanie wdrożony, na przykład przy użyciu `kubectl apply -f gpu-toleration.yaml` , Kubernetes można pomyślnie zaplanować na węzłach z zastosowaniem zmiany. Ta izolacja logiczna pozwala kontrolować dostęp do zasobów w klastrze.

Po zastosowaniu przydziałów należy współpracować z programistami i właścicielami aplikacji, aby mogli definiować wymagane tolerowania w ich wdrożeniach.

Aby uzyskać więcej informacji na temat używania wielu pul węzłów w AKS, zobacz [Tworzenie wielu pul węzłów i zarządzanie nimi w klastrze w AKS][use-multiple-node-pools].

### <a name="behavior-of-taints-and-tolerations-in-aks"></a>Zachowanie przyciągania i tolerowania w AKS

Po uaktualnieniu puli węzłów w AKS, jego przypisaniach i tolerowaniu stosują się do wzorca zestawu, gdy są one stosowane do nowych węzłów:

- **Domyślne klastry korzystające z zestawów skalowania maszyn wirtualnych**
  - Można zmienić rozmiar [elementu nodepool][taint-node-pool] z interfejsu API AKS, aby nowo skalowane węzły mogły odbierać zmiany w węzłach interfejsu API.
  - Załóżmy, że masz klaster z dwoma węzłami — *Węzeł1* i *Węzeł2* . Należy uaktualnić pulę węzłów.
  - Tworzone są dwa dodatkowe węzły, *Węzeł3* i *Węzeł4* , a ich przekazanie odbywa się odpowiednio.
  - Oryginalne *Węzeł1* i *Węzeł2* są usuwane.

- **Klastry bez obsługi zestawu skalowania maszyn wirtualnych**
  - Załóżmy, że masz klaster z dwoma węzłami — *Węzeł1* i *Węzeł2* . Podczas uaktualniania jest tworzony dodatkowy węzeł ( *Węzeł3* ).
  - *Węzeł1* są stosowane do *Węzeł3* , a następnie *Węzeł1* jest usuwana.
  - Tworzony jest inny nowy węzeł (o nazwie *Węzeł1* , ponieważ poprzedni *Węzeł1* został usunięty), a do nowego *Węzeł1* są stosowane *węzeł2y* . Następnie *Węzeł2* jest usuwany.
  - W zasadzie *Węzeł1* staną się *Węzeł3* , a *Węzeł2* zostanie *Węzeł1* .

W przypadku skalowania puli węzłów w AKS, zmiany czasu i tolerowania nie są przenoszone przez projektowanie.

## <a name="control-pod-scheduling-using-node-selectors-and-affinity"></a>Kontrola pod planowaniem przy użyciu selektorów węzłów i koligacji

**Wskazówki dotyczące najlepszych** rozwiązań — Kontroluj planowanie zadań w węzłach przy użyciu selektorów węzłów, koligacji węzłów lub koligacji między innymi. Te ustawienia pozwalają usłudze Kubernetes Scheduler logicznie izolować obciążenia, na przykład przez sprzęt w węźle.

Przydziały i tolerowania są używane do logicznego izolowania zasobów przy użyciu trwałego obcinania — Jeśli nie jest to tolerowane w węźle, nie jest ono zaplanowane w węźle. Alternatywnym rozwiązaniem jest użycie selektorów węzłów. Etykiety węzłów, takie jak wskazuje lokalnie podłączony magazyn SSD lub dużą ilość pamięci, a następnie definiują w selektorze węzła specyfikację. Kubernetes następnie planuje te zasobniki w zgodnym węźle. W przeciwieństwie do tolerowania, w przypadku węzłów z etykietami nie można zaplanować wydziałów bez pasującego selektora węzłów. Takie zachowanie zezwala na używanie nieużywanych zasobów w węzłach, ale daje priorytet dla tego samego zasobnika, który definiuje pasujący selektor węzła.

Spójrzmy na przykład węzłów z dużą ilością pamięci. Te węzły mogą dać preferencję do zasobników, które żądają dużej ilości pamięci. Aby upewnić się, że zasoby nie są bezczynne, zezwalają również na uruchamianie innych zasobów.

```console
kubectl label node aks-nodepool1 hardware=highmem
```

Specyfikacja pod, następnie dodaje `nodeSelector` Właściwość w celu zdefiniowania selektora węzła, który pasuje do zestawu etykiet w węźle:

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

Korzystając z tych opcji harmonogramu, Pracuj z programistami i właścicielami aplikacji, aby umożliwić im poprawne Definiowanie ich specyfikacji.

Aby uzyskać więcej informacji na temat używania selektorów węzłów, zobacz [przypisywanie zasobników do węzłów][k8s-node-selector].

### <a name="node-affinity"></a>Koligacja węzłów

Selektor węzła to podstawowy sposób przypisywania do danego węzła. Większa elastyczność jest dostępna przy użyciu *koligacji węzłów* . W przypadku koligacji węzłów należy określić, co się stanie, jeśli nie można dopasować elementu pod węzłem. Można *wymagać* , aby harmonogram Kubernetes był zgodny z hostem z etykietą. Lub można *preferować* dopasowanie, ale zezwolić na zaplanowanie pod innym hostem, jeśli nie jest zgodny.

W poniższym przykładzie koligacja węzła jest ustawiana na *requiredDuringSchedulingIgnoredDuringExecution* . Ta koligacja wymaga, aby harmonogram Kubernetes używał węzła ze zgodną etykietą. Jeśli żaden węzeł nie jest dostępny, musi on oczekiwać na kontynuowanie planowania. Aby umożliwić planowanie pod innym węzłem, można ustawić wartość *preferredDuringSchedulingIgnoreDuringExecution* :

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

*IgnoredDuringExecution* część tego ustawienia wskazuje, że jeśli etykiety węzła zmienią się, nie powinno być wykluczone z węzła. Harmonogram Kubernetes używa tylko zaktualizowanych etykiet węzłów dla nowych, zaplanowanych w harmonogramie, a nie dla każdego z nich, które są już zaplanowane w węzłach.

Aby uzyskać więcej informacji, zobacz [koligacja i ochrona przed koligacją][k8s-affinity].

### <a name="inter-pod-affinity-and-anti-affinity"></a>Koligacja między pod i ochrona przed koligacjami

Jedno z ostatecznych podejścia do usługi Kubernetes Scheduler w celu logicznego izolowania obciążeń odbywa się przy użyciu koligacji Inter-lub-koligacji. Ustawienia definiują, że *nie należy* planować w węźle, który ma istniejące dopasowanie pod lub *należy* zaplanować. Domyślnie harmonogram Kubernetes próbuje zaplanować wiele zasobników w zestawie replik w różnych węzłach. Istnieje możliwość zdefiniowania bardziej szczegółowych reguł dotyczących tego zachowania.

Dobrym przykładem jest aplikacja sieci Web, która również korzysta z pamięci podręcznej platformy Azure dla Redis. Za pomocą zasad ochrony przed koligacjami można zażądać, aby harmonogram Kubernetes dystrybuuje repliki między węzłami. Można następnie użyć reguł koligacji, aby upewnić się, że każdy składnik aplikacji sieci Web jest zaplanowany na tym samym hoście co odpowiednia pamięć podręczna. Rozkład liczby elementów w węzłach jest podobny do następującego:

| **Węzeł 1** | **Węzeł 2** | **Węzeł 3** |
|------------|------------|------------|
| webapp-1   | webapp-2   | webapp-3   |
| pamięć podręczna 1    | pamięć podręczna 2    | pamięć podręczna — 3    |

Ten przykład to bardziej złożone wdrożenie niż użycie selektorów węzłów lub koligacji węzłów. Wdrożenie zapewnia kontrolę nad sposobem, w jaki Kubernetes planuje się w węzłach i może logicznie izolować zasoby. Aby zapoznać się z kompletnym przykładem tej aplikacji sieci Web za pomocą usługi Azure cache for Redis, zobacz temat [lokalizowanie zasobników w tym samym węźle][k8s-pod-affinity].

## <a name="next-steps"></a>Następne kroki

Ten artykuł koncentruje się na zaawansowanych funkcjach usługi Kubernetes Scheduler. Aby uzyskać więcej informacji na temat operacji klastra w programie AKS, zobacz następujące najlepsze rozwiązania:

* [Wielodostęp i izolacja klastra][aks-best-practices-scheduler]
* [Podstawowe funkcje usługi Kubernetes Scheduler][aks-best-practices-scheduler]
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
