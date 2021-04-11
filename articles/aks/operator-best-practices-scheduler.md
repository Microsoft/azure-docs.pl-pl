---
title: Najlepsze praktyki dla operatorów — podstawowe funkcje harmonogramu w usłudze Azure Kubernetes Services (AKS)
description: Zapoznaj się z najlepszymi rozwiązaniami operatora klastra dotyczącymi korzystania z podstawowych funkcji usługi Scheduler, takich jak przydziały zasobów i budżety na przerwy w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: 8c0f1d0cda61638abe03b92c627a5ea0455c31cb
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107104902"
---
# <a name="best-practices-for-basic-scheduler-features-in-azure-kubernetes-service-aks"></a>Najlepsze rozwiązania dotyczące podstawowych funkcji harmonogramu w usłudze Azure Kubernetes Service (AKS)

Podczas zarządzania klastrami w usłudze Azure Kubernetes Service (AKS) często trzeba izolować zespoły i obciążenia. Usługa Kubernetes Scheduler pozwala kontrolować dystrybucję zasobów obliczeniowych lub ograniczać wpływ zdarzeń konserwacji.

Te najlepsze rozwiązania koncentrują się na podstawowych funkcjach planowania Kubernetes dla operatorów klastra. W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Użyj przydziałów zasobów, aby zapewnić stałą ilość zasobów dla zespołów lub obciążeń
> * Ogranicz wpływ zaplanowanej konserwacji przy użyciu budżetów przerwań
> * Wyszukaj brakujące żądania zasobów i limity przy użyciu `kube-advisor` Narzędzia

## <a name="enforce-resource-quotas"></a>Wymuszaj przydziały zasobów

> **Wskazówki dotyczące najlepszych rozwiązań** 
> 
> Planowanie i stosowanie przydziałów zasobów na poziomie przestrzeni nazw. Jeśli nie definiują żądań zasobów i limitów, Odrzuć wdrożenie. Monitorowanie użycia zasobów i dostosowywanie przydziałów w razie konieczności.

Żądania zasobów i limity są umieszczane w specyfikacji pod. Limity są używane przez harmonogram Kubernetes podczas wdrażania, aby znaleźć dostępny węzeł w klastrze. Limity i żądania działają na poziomie poszczególnych osób. Aby uzyskać więcej informacji na temat sposobu definiowania tych wartości, zobacz [Definiowanie żądań zasobów i limitów][resource-limits] .

Aby zapewnić sposób rezerwowania i ograniczania zasobów w zespole lub projekcie deweloperskim, należy użyć *przydziałów zasobów*. Te przydziały są zdefiniowane w przestrzeni nazw i mogą być używane do ustawiania przydziałów w następujący sposób:

* **Zasoby obliczeniowe**, takie jak procesor CPU i pamięć, lub procesory GPU.
* **Zasoby magazynu**, łącznie z łączną liczbą woluminów lub ilością miejsca na dysku dla danej klasy magazynu.
* **Liczba obiektów**, takich jak Maksymalna liczba wpisów tajnych, usług lub zadań, może zostać utworzona.

Kubernetes nie zatwierdzi zasobów. Po całkowitym żądaniu zasobów zbiorczych zostanie osiągnięty przypisany limit przydziału, wszystkie dalsze wdrożenia zakończą się niepowodzeniem.

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

> **Wskazówki dotyczące najlepszych rozwiązań** 
>
> Aby zapewnić dostępność aplikacji, należy zdefiniować budżety zakłóceń (plików PDB), aby upewnić się, że w klastrze są dostępne co najmniej minimalne numery.

Istnieją dwa zdarzenia zakłócające, które powodują usunięcie z nich:

### <a name="involuntary-disruptions"></a>Niedobrowolne zakłócenia

*Niedobrowolne zakłócenia* to zdarzenia wykraczające poza typową kontrolę nad operatorem klastra lub właścicielem aplikacji. Być
* Awaria sprzętowa na komputerze fizycznym
* Awaryjnego jądra
* Usuwanie maszyny wirtualnej węzła

Niedobrowolne zakłócenia mogą być skorygowane przez:
* Używanie wielu replik Twojego zasobnika we wdrożeniu. 
* Uruchamianie wielu węzłów w klastrze AKS. 

### <a name="voluntary-disruptions"></a>Dobrowolne zakłócenia

*Dobrowolne zakłócenia* to zdarzenia żądane przez operatora klastra lub właściciela aplikacji. Być
* Uaktualnienia klastra
* Zaktualizowany szablon wdrożenia
* Przypadkowe usunięcie elementu pod

Kubernetes zapewnia, że w przypadku dobrowolnego zakłócenia istnieją *budżety zakłóceń* , co pozwala na zaplanowanie sposobu, w jaki wdrożenia lub zestawy replik odpowiadają, gdy wystąpi zdarzenie dobrowolnego zakłócenia. W przypadku korzystania z budżetów, operatorzy klastrów mogą definiować minimalną lub maksymalną niedostępną liczbę zasobów. 

Jeśli uaktualniasz klaster lub zaktualizujesz szablon wdrożenia, harmonogram Kubernetes będzie zaplanował dodatkowe wystąpienia w innych węzłach przed zezwoleniem na kontynuowanie dobrowolnych zdarzeń zakłóceń. Harmonogram czeka na ponowne uruchomienie węzła do momentu pomyślnego zaplanowania zdefiniowanej liczby numerów w innych węzłach w klastrze.

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

> **Wskazówki dotyczące najlepszych rozwiązań** 
>
> Regularnie uruchamiaj najnowszą wersję `kube-advisor` Narzędzia Open Source, aby wykrywać problemy w klastrze. W przypadku zastosowania przydziałów zasobów w istniejącym klastrze AKS najpierw uruchom polecenie, `kube-advisor` Aby znaleźć informacje o jednostkach, dla których zdefiniowano żądania zasobów i limity.

Narzędzie [polecenia-Advisor][kube-advisor] to SKOJARZONY projekt AKS typu open source, który skanuje klaster Kubernetes i zgłasza zidentyfikowane problemy. `kube-advisor` okazuje się przydatne w identyfikacji zasobników bez żądań zasobów i limitów.

Gdy `kube-advisor` Narzędzie może raportować żądania zasobów i brak ograniczeń w PodSpecs dla aplikacji systemu Windows i Linux, samo narzędzie musi być zaplanowane w systemie Linux pod. Zaplanuj uruchomienie pod kątem uruchamiania w puli węzłów z określonym systemem operacyjnym przy użyciu [selektora węzłów][k8s-node-selector] w konfiguracji pod.

Śledzenie zestawów i nie ustawia żądań zasobów i limitów w klastrze AKS hostującym wiele zespołów programistycznych i aplikacji może być trudne. Najlepszym rozwiązaniem jest regularne uruchamianie `kube-advisor` w klastrach AKS, zwłaszcza jeśli przydziały zasobów nie są przypisywane do przestrzeni nazw.

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
