---
title: Najlepsze praktyki dla operatorów — podstawowe funkcje harmonogramu w usłudze Azure Kubernetes Services (AKS)
description: Zapoznaj się z najlepszymi rozwiązaniami operatora klastra dotyczącymi korzystania z podstawowych funkcji usługi Scheduler, takich jak przydziały zasobów i budżety na przerwy w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: 087c1d2efc93b8460a3683a4e66916d73fd4e885
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87015684"
---
# <a name="best-practices-for-basic-scheduler-features-in-azure-kubernetes-service-aks"></a>Najlepsze rozwiązania dotyczące podstawowych funkcji harmonogramu w usłudze Azure Kubernetes Service (AKS)

Podczas zarządzania klastrami w usłudze Azure Kubernetes Service (AKS) często trzeba izolować zespoły i obciążenia. Usługa Kubernetes Scheduler udostępnia funkcje, które umożliwiają kontrolę dystrybucji zasobów obliczeniowych lub ograniczają wpływ zdarzeń konserwacyjnych.

Te najlepsze rozwiązania koncentrują się na podstawowych funkcjach planowania Kubernetes dla operatorów klastra. W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Użyj przydziałów zasobów, aby zapewnić stałą ilość zasobów dla zespołów lub obciążeń
> * Ogranicz wpływ zaplanowanej konserwacji przy użyciu budżetów przerwań
> * Wyszukaj brakujące żądania zasobów i limity przy użyciu `kube-advisor` Narzędzia

## <a name="enforce-resource-quotas"></a>Wymuszaj przydziały zasobów

**Wskazówki dotyczące najlepszych** rozwiązań — planowanie i stosowanie przydziałów zasobów na poziomie przestrzeni nazw. Jeśli nie definiują żądań zasobów i limitów, Odrzuć wdrożenie. Monitorowanie użycia zasobów i dostosowywanie przydziałów w razie konieczności.

Żądania zasobów i limity są umieszczane w specyfikacji pod. Te limity są używane przez harmonogram Kubernetes podczas wdrażania, aby znaleźć dostępny węzeł w klastrze. Te limity i żądania działają na poziomie poszczególnych poziomów. Aby uzyskać więcej informacji na temat sposobu definiowania tych wartości, zobacz [Definiowanie żądań zasobów i limitów][resource-limits] .

Aby zapewnić sposób rezerwowania i ograniczania zasobów w zespole lub projekcie deweloperskim, należy użyć *przydziałów zasobów*. Te przydziały są zdefiniowane w przestrzeni nazw i mogą być używane do ustawiania przydziałów w następujący sposób:

* **Zasoby obliczeniowe**, takie jak procesor CPU i pamięć, lub procesory GPU.
* **Zasoby magazynu** obejmują łączną liczbę woluminów lub ilość miejsca na dysku dla danej klasy magazynu.
* **Liczba obiektów**, takich jak Maksymalna liczba wpisów tajnych, usług lub zadań, może zostać utworzona.

Kubernetes nie zatwierdzi zasobów. Gdy skumulowana łączna liczba żądań lub limitów zasobów przekroczy przypisany przydział, dalsze wdrożenia nie powiedzie się.

Podczas definiowania przydziałów zasobów wszystkie zasobniki utworzone w przestrzeni nazw muszą podawać limity lub żądania w ich specyfikacjach. Jeśli te wartości nie zostaną podane, można odrzucić wdrożenie. Zamiast tego można [skonfigurować domyślne żądania i limity dla przestrzeni nazw][configure-default-quotas].

Poniższy przykład manifestu YAML o nazwie *dev-App-Team-Limited. YAML* ustawia stały limit *10* procesorów CPU, *20Gi* pamięci i *10* zasobników:

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: dev-app-team
spec:
  hard:
    cpu: "10"
    memory: 20Gi
    pods: "10"
```

Ten przydział zasobów można zastosować, określając przestrzeń nazw, na przykład *dev-Apps*:

```console
kubectl apply -f dev-app-team-quotas.yaml --namespace dev-apps
```

Pracuj z programistami i właścicielami aplikacji, aby zrozumieć ich potrzeby i zastosować odpowiednie przydziały zasobów.

Aby uzyskać więcej informacji na temat dostępnych obiektów zasobów, zakresów i priorytetów, zobacz [przydziały zasobów w Kubernetes][k8s-resource-quotas].

## <a name="plan-for-availability-using-pod-disruption-budgets"></a>Planowanie dostępności przy użyciu budżetów przerwań

**Wskazówki dotyczące najlepszych** rozwiązań — aby zachować dostępność aplikacji, należy zdefiniować budżety przerw w działaniu (plików PDB), aby upewnić się, że w klastrze są dostępne minimalne liczby zasobników.

Istnieją dwa zdarzenia zakłócające, które powodują usunięcie z nich:

* *Niedobrowolne zakłócenia* to zdarzenia wykraczające poza typową kontrolę nad operatorem klastra lub właścicielem aplikacji.
  * Niedobrowolne zakłócenia obejmują awarie sprzętowe na komputerze fizycznym, awaryjnego jądra lub usuwanie maszyny wirtualnej węzła
* *Dobrowolne zakłócenia* to zdarzenia żądane przez operatora klastra lub właściciela aplikacji.
  * Te dobrowolne zakłócenia obejmują uaktualnienia klastra, zaktualizowany szablon wdrożenia lub przypadkowe usunięcie pod.

Niedobrowolne zakłócenia można wyeliminować przy użyciu wielu replik Twojego zasobnika w ramach wdrożenia. Uruchomienie wielu węzłów w klastrze AKS również pomaga w niedobrowolnym zakłóceniom. W przypadku dobrowolnego zakłócenia Kubernetes zapewnia w *budżecie przerwy w zakłóceniach* , które umożliwiają operatorowi klastra Definiowanie minimalnej dostępnej lub maksymalnej liczby niedostępnych zasobów. Te budżety na przerwy w działaniu umożliwiają zaplanowanie sposobu, w jaki wdrożenia lub zestawy replik reagują, gdy wystąpi zdarzenie dobrowolnego zakłócenia.

Jeśli klaster ma zostać uaktualniony lub Zaktualizowano szablon wdrożenia, usługa Kubernetes Scheduler upewni się, że dodatkowe zasobniki są zaplanowane w innych węzłach, zanim będzie można kontynuować zdarzenia dobrowolnego zakłócenia. Harmonogram czeka przed ponownym uruchomieniem węzła do momentu pomyślnego zaplanowania zdefiniowanej liczby numerów w innych węzłach w klastrze.

Przyjrzyjmy się przykładowi do zestawu replik z pięcioma zasobnikami z systemem NGINX. W zestawie replik są przypisywane etykiety `app: nginx-frontend` . Podczas zdarzenia dobrowolnego zakłócenia, takiego jak uaktualnienie klastra, należy upewnić się, że co najmniej trzy zasobniki będą nadal działać. Następujący manifest YAML dla obiektu *PodDisruptionBudget* definiuje następujące wymagania:

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
   name: nginx-pdb
spec:
   minAvailable: 3
   selector:
    matchLabels:
      app: nginx-frontend
```

Możesz również określić wartość procentową, taką jak *60%*, która umożliwia automatyczne skompensowanie dla zestawu replik skalowanie w górę liczby numerów.

Można zdefiniować maksymalną liczbę niedostępnych wystąpień w zestawie replik. Ponownie można także zdefiniować wartość procentową dla maksymalnej niedostępnego zasobnika. W poniższym manifeście budżetu YAML zakłóceń nie można uzyskać dostępu do więcej niż dwóch zasobników w zestawie replik:

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
   name: nginx-pdb
spec:
   maxUnavailable: 2
   selector:
    matchLabels:
      app: nginx-frontend
```

Po zdefiniowaniu budżetu dla przerwy w działaniu można utworzyć go w klastrze AKS, tak jak w przypadku dowolnego innego obiektu Kubernetes:

```console
kubectl apply -f nginx-pdb.yaml
```

Skontaktuj się z programistami i właścicielami aplikacji, aby zrozumieć ich potrzeby i zastosować odpowiednie budżety na przerwy.

Aby uzyskać więcej informacji o korzystaniu z budżetów, zobacz temat [Określanie budżetu zakłóceń dla aplikacji][k8s-pdbs].

## <a name="regularly-check-for-cluster-issues-with-kube-advisor"></a>Regularnie sprawdzaj problemy z klastrem przy użyciu usługi polecenia-Advisor

**Wskazówki dotyczące najlepszych** rozwiązań — regularnie uruchamiaj najnowszą wersję `kube-advisor` Narzędzia Open Source, aby wykrywać problemy w klastrze. W przypadku zastosowania przydziałów zasobów w istniejącym klastrze AKS najpierw uruchom polecenie, `kube-advisor` Aby znaleźć informacje o jednostkach, dla których zdefiniowano żądania zasobów i limity.

Narzędzie [polecenia-Advisor][kube-advisor] to SKOJARZONY projekt AKS typu open source, który skanuje klaster Kubernetes i raportuje o znalezionych problemach. Jednym z przydatnych kontroli jest zidentyfikowanie, które nie mają żądań zasobów i limitów.

Narzędzie polecenia-Advisor może raportować żądania zasobów i limitów braku w PodSpecs dla aplikacji systemu Windows, a także aplikacji z systemem Linux, ale narzędzie Advisor polecenia musi zostać zaplanowane w systemie Linux pod. Można zaplanować uruchomienie w puli węzłów z określonym systemem operacyjnym przy użyciu [selektora węzłów][k8s-node-selector] w konfiguracji pod.

W klastrze AKS, który obsługuje wiele zespołów programistycznych i aplikacji, może być trudno śledzić zestaw na platformie i nie tylko te żądania zasobów i limity. Najlepszym rozwiązaniem jest regularne uruchamianie `kube-advisor` w klastrach AKS, zwłaszcza jeśli przydziały zasobów nie są przypisywane do przestrzeni nazw.

## <a name="next-steps"></a>Następne kroki

Ten artykuł koncentruje się na podstawowych funkcjach usługi Kubernetes Scheduler. Aby uzyskać więcej informacji na temat operacji klastra w programie AKS, zobacz następujące najlepsze rozwiązania:

* [Wielodostęp i izolacja klastra][aks-best-practices-cluster-isolation]
* [Zaawansowane funkcje usługi Scheduler Kubernetes][aks-best-practices-advanced-scheduler]
* [Uwierzytelnianie i autoryzacja][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->
[k8s-resource-quotas]: https://kubernetes.io/docs/concepts/policy/resource-quotas/
[configure-default-quotas]: https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/
[kube-advisor]: https://github.com/Azure/kube-advisor
[k8s-pdbs]: https://kubernetes.io/docs/tasks/run-application/configure-pdb/

<!-- INTERNAL LINKS -->
[resource-limits]: developer-best-practices-resource-management.md#define-pod-resource-requests-and-limits
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
