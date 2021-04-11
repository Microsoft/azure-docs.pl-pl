---
title: Najlepsze rozwiązania dotyczące funkcji usługi Scheduler
titleSuffix: Azure Kubernetes Service
description: Zapoznaj się z najlepszymi rozwiązaniami operatora klastra dotyczącymi korzystania z zaawansowanych funkcji harmonogramu, takich jak przyrządy i tolerowanie, selektory węzłów i koligacja lub koligacja między innymi i ochrona w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: 27b32d7d10b691ed806e4d7aa31a095630d2bfc9
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103627"
---
# <a name="best-practices-for-advanced-scheduler-features-in-azure-kubernetes-service-aks"></a>Najlepsze rozwiązania dotyczące zaawansowanych funkcji harmonogramu w usłudze Azure Kubernetes Service (AKS)

Podczas zarządzania klastrami w usłudze Azure Kubernetes Service (AKS) często trzeba izolować zespoły i obciążenia. Zaawansowane funkcje udostępniane przez usługę Kubernetes Scheduler umożliwiają sterowanie:
* Które z nich można zaplanować na niektórych węzłach.
* Sposób, w jaki aplikacje wieloskładnikowe mogą być odpowiednio dystrybuowane w klastrze. 

Te najlepsze rozwiązania koncentrują się na zaawansowanych funkcjach planowania Kubernetes dla operatorów klastra. W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Użyj przydziałów i tolerowania, aby ograniczyć liczbę elementów, które można zaplanować w węzłach.
> * Nadaj preferencjom, które mają być uruchamiane na niektórych węzłach przy użyciu selektorów węzłów lub koligacji węzłów.
> * Podziel się na siebie lub pogrupowanie za pomocą koligacji internej lub antykoligacji.

## <a name="provide-dedicated-nodes-using-taints-and-tolerations"></a>Zapewnianie dedykowanych węzłów przy użyciu przydzielonych im i odtolerowaeń

> **Wskazówki dotyczące najlepszych rozwiązań:** 
>
> Ogranicz dostęp do aplikacji intensywnie korzystających z zasobów, takich jak kontrolery transferu danych przychodzących, do określonych węzłów. Przechowuj zasoby węzła dla obciążeń, które ich wymagają, i nie Zezwalaj na planowanie innych obciążeń w węzłach.

Podczas tworzenia klastra AKS można wdrożyć węzły z obsługą procesora GPU lub dużą liczbą zaawansowanych procesorów CPU. Za pomocą tych węzłów można korzystać z obciążeń przetwarzania dużych ilości danych, takich jak uczenie maszynowe (ML) lub Sztuczna inteligencja (AI). 

Ponieważ ten sprzęt zasobów węzła jest zwykle kosztowny do wdrożenia, Ogranicz obciążenia, które mogą być zaplanowane w tych węzłach. Zamiast tego można przeznaczyć kilka węzłów w klastrze, aby uruchamiać usługi transferu danych przychodzących i uniemożliwić inne obciążenia.

Ta obsługa różnych węzłów jest zapewniana przy użyciu wielu pul węzłów. Klaster AKS zawiera co najmniej jedną pulę węzłów.

Harmonogram Kubernetes korzysta z elementów i ograniczeń, aby ograniczyć liczbę obciążeń, które mogą być uruchamiane w węzłach.

* Zastosuj Przystąp do węzła, aby wskazać **, że na** nich można zaplanować tylko określone zasobniki.
* Następnie Zastosuj **tolerowanie** do pod, aby umożliwić im *tolerowanie* kształtu węzła.

Po wdrożeniu pod kątem klastra AKS, Kubernetes tylko planuje na węzłach, których przybarwienie jest wyrównane z tolerowaniem. Załóżmy na przykład, że masz pulę węzłów w klastrze AKS dla węzłów z obsługą procesora GPU. Należy zdefiniować nazwę, na przykład *GPU*, a następnie wartość planowania. Ustawienie tej wartości na *NoSchedule* ogranicza harmonogram Kubernetes z planowania z niezdefiniowanym wpływem na węzeł.

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

Gdy ten temat zostanie wdrożony za pomocą programu `kubectl apply -f gpu-toleration.yaml` , Kubernetes może pomyślnie zaplanować na węzłach, w których zastosowano przystąpić. Ta izolacja logiczna pozwala kontrolować dostęp do zasobów w klastrze.

Po zastosowaniu przydziałów należy współpracować z programistami i właścicielami aplikacji, aby mogli definiować wymagane tolerowania w ich wdrożeniach.

Aby uzyskać więcej informacji na temat używania wielu pul węzłów w AKS, zobacz [Tworzenie wielu pul węzłów i zarządzanie nimi w klastrze w AKS][use-multiple-node-pools].

### <a name="behavior-of-taints-and-tolerations-in-aks"></a>Zachowanie przyciągania i tolerowania w AKS

Po uaktualnieniu puli węzłów w AKS, jego przypisaniach i tolerowaniu stosują się do wzorca zestawu, gdy są one stosowane do nowych węzłów:

#### <a name="default-clusters-that-use-vm-scale-sets"></a>Domyślne klastry korzystające z zestawów skalowania maszyn wirtualnych
Można zmienić rozmiar [puli węzłów][taint-node-pool] z interfejsu API AKS, aby nowo skalowane węzły mogły odbierać zmiany w węzłach interfejsu API.

Załóżmy, że:
1. Rozpoczyna się od klastra z dwoma węzłami: *Węzeł1* i *Węzeł2*. 
1. Należy uaktualnić pulę węzłów.
1. Tworzone są dwa dodatkowe węzły: *Węzeł3* i *Węzeł4*. 
1. Te okna są odpowiednio przesyłane.
1. Oryginalne *Węzeł1* i *Węzeł2* są usuwane.

#### <a name="clusters-without-vm-scale-set-support"></a>Klastry bez obsługi zestawu skalowania maszyn wirtualnych

Załóżmy, że:
1. Istnieje klaster z dwoma węzłami: *Węzeł1* i *Węzeł2*. 
1. Następnie można uaktualnić pulę węzłów.
1. Tworzony jest dodatkowy węzeł: *Węzeł3*.
1. W *Węzeł3* są stosowane ich *przewęzeł1y* .
1. *Węzeł1* został usunięty.
1. Zostanie utworzona nowa *Węzeł1* , która zastąpi pierwotną *Węzeł1*.
1. Do nowych *Węzeł1* są stosowane *węzeł2e* . 
1. *Węzeł2* został usunięty.

W zasadzie *Węzeł1* staną się *Węzeł3*, a *Węzeł2* staną się nowym *Węzeł1*.

W przypadku skalowania puli węzłów w AKS, zmiany czasu i tolerowania nie są przenoszone przez projektowanie.

## <a name="control-pod-scheduling-using-node-selectors-and-affinity"></a>Kontrola pod planowaniem przy użyciu selektorów węzłów i koligacji

> **Wskazówki dotyczące najlepszych rozwiązań** 
> 
> Kontroluj planowanie zadań w węzłach, korzystając z selektorów węzłów, koligacji węzłów lub koligacji Inter-pod. Te ustawienia pozwalają usłudze Kubernetes Scheduler logicznie izolować obciążenia, na przykład przez sprzęt w węźle.

Przydziały i tolerowania logicznie izolują zasoby z twardą granicą. Jeśli wartość nie jest tolerowana w tym węźle, nie jest ona zaplanowana w węźle. 

Alternatywnie można użyć selektorów węzłów. Na przykład etykiety węzłów wskazujące lokalnie dołączony magazyn SSD lub dużą ilość pamięci, a następnie definiują w selektorze węzła specyfikację. Kubernetes planuje te zasobniki w zgodnym węźle. 

W przeciwieństwie do tolerowania, w przypadku węzłów z etykietą nie można nadal zaplanować nadania bez pasującego selektora węzła. Takie zachowanie zezwala na używanie nieużywanych zasobów w węzłach, ale określa priorytety, które definiują odpowiedni selektor węzła.

Spójrzmy na przykład węzłów z dużą ilością pamięci. Te węzły ustalają priorytety, które żądają dużej ilości pamięci. Aby upewnić się, że zasoby nie są bezczynne, zezwalają również na uruchamianie innych zasobów.

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

Selektor węzła to podstawowe rozwiązanie służące do przypisywania zasobników do danego węzła. *Koligacja węzłów* zapewnia większą elastyczność, umożliwiając Definiowanie, co się stanie, jeśli nie można dopasować elementu pod węzłem. Oto co możesz zrobić: 
* *Wymagaj* , aby usługa Kubernetes Scheduler była zgodna z hostem z etykietą. Lub:
* *Preferuj* dopasowanie, ale Zezwalaj na zaplanowanie pod innym hostem, jeśli nie jest dostępne żadne dopasowanie.

W poniższym przykładzie koligacja węzła jest ustawiana na *requiredDuringSchedulingIgnoredDuringExecution*. Ta koligacja wymaga, aby harmonogram Kubernetes używał węzła ze zgodną etykietą. Jeśli żaden węzeł nie jest dostępny, musi on oczekiwać na kontynuowanie planowania. Aby umożliwić planowanie pod innym węzłem, można zamiast tego ustawić wartość ***preferowany** DuringSchedulingIgnoreDuringExecution *:

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

*IgnoredDuringExecution* część tego ustawienia wskazuje, że nie powinien być wykluczony z węzła, jeśli etykiety węzła zmienią się. Harmonogram Kubernetes używa tylko zaktualizowanych etykiet węzłów dla nowych, zaplanowanych w harmonogramie, a nie dla każdego z nich, które są już zaplanowane w węzłach.

Aby uzyskać więcej informacji, zobacz [koligacja i ochrona przed koligacją][k8s-affinity].

### <a name="inter-pod-affinity-and-anti-affinity"></a>Koligacja między pod i ochrona przed koligacjami

Jedno z ostatecznych podejścia do usługi Kubernetes Scheduler w celu logicznego izolowania obciążeń odbywa się przy użyciu koligacji Inter-lub-koligacji. Te ustawienia definiują, że w węźle, który ma istniejący pasujący *element, nie* należy lub *powinny* być zaplanowane. Domyślnie harmonogram Kubernetes próbuje zaplanować wiele zasobników w zestawie replik w różnych węzłach. Istnieje możliwość zdefiniowania bardziej szczegółowych reguł dotyczących tego zachowania.

Załóżmy na przykład, że masz aplikację sieci Web, która używa również pamięci podręcznej platformy Azure dla Redis. 
1. Reguły ochrony przed koligacjami służą do żądania, aby harmonogram Kubernetes dystrybuuje repliki między węzłami. 
1. Reguły koligacji są używane do zapewnienia, że każdy składnik aplikacji sieci Web jest zaplanowany na tym samym hoście co odpowiednia pamięć podręczna. 

Rozkład liczby elementów w węzłach jest podobny do następującego:

| **Węzeł 1** | **Węzeł 2** | **Węzeł 3** |
|------------|------------|------------|
| webapp-1   | webapp-2   | webapp-3   |
| pamięć podręczna 1    | pamięć podręczna 2    | pamięć podręczna — 3    |

Koligacja między innymi i ochrona Antykoligacja zapewniają bardziej złożone wdrożenie niż selektory węzłów lub koligacja węzłów. Dzięki wdrożeniu można logicznie izolować zasoby i kontrolować sposób, w jaki Kubernetes planuje się w węzłach. 

Aby zapoznać się z kompletnym przykładem tej aplikacji sieci Web za pomocą usługi Azure cache for Redis, zobacz temat [lokalizowanie zasobników w tym samym węźle][k8s-pod-affinity].

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
