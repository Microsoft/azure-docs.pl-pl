---
title: Dodawanie puli węzłów typu spot do klastra usługi Azure Kubernetes Service (AKS)
description: Dowiedz się, jak dodać pulę węzłów typu spot do Azure Kubernetes Service (AKS).
services: container-service
ms.service: container-service
ms.topic: article
ms.date: 10/19/2020
ms.openlocfilehash: f46a421ae2ad1a4d9c590c7e0b47784760ebcb9f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782805"
---
# <a name="add-a-spot-node-pool-to-an-azure-kubernetes-service-aks-cluster"></a>Dodawanie puli węzłów typu spot do klastra usługi Azure Kubernetes Service (AKS)

Pula węzłów typu spot to pula węzłów, która jest zapasowa przez zestaw [skalowania maszyn wirtualnych typu spot.][vmss-spot] Korzystanie z maszyn wirtualnych typu spot dla węzłów z klastrem usługi AKS umożliwia korzystanie z nie w pełni wykorzystać pojemności na platformie Azure przy znaczących oszczędnościach kosztów. Ilość dostępnej nie w pełni pustą pojemność będzie się różnić w zależności od wielu czynników, takich jak rozmiar węzła, region i godzina dnia.

Podczas wdrażania puli węzłów typu spot platforma Azure przydzieli węzły typu spot, jeśli jest dostępna pojemność. Nie ma jednak umowy SLA dla węzłów typu spot. Zestaw skalowania typu spot, który stanowi kopię zapasową puli węzłów typu spot, jest wdrażany w jednej domenie błędów i nie oferuje gwarancji wysokiej dostępności. W dowolnym momencie, gdy platforma Azure potrzebuje z powrotem pojemności, infrastruktura platformy Azure eksmisja węzłów typu spot.

Węzły typu spot są doskonałe dla obciążeń, które mogą obsługiwać przerwy, wczesne zakończenia lub eksmisje. Na przykład obciążenia, takie jak zadania przetwarzania wsadowego, środowiska projektowe i testowe oraz duże obciążenia obliczeniowe, mogą być dobrymi kandydatami do zaplanowania w puli węzłów typu spot.

W tym artykule dodasz pulę węzłów pomocniczych typu spot do istniejącego Azure Kubernetes Service klastra (AKS).

W tym artykule założono, że rozumiesz platformę Kubernetes i rozumiesz Azure Load Balancer pojęcia. Aby uzyskać więcej informacji, zobacz [Podstawowe pojęcia dotyczące usługi Azure Kubernetes Service (AKS)][kubernetes-concepts].

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Podczas tworzenia klastra w celu korzystania z puli węzłów typu spot ten klaster musi również używać Virtual Machine Scale Sets dla pul węzłów i usługi równoważenia obciążenia standardowej *wersji* SKU. Należy również dodać dodatkową pulę węzłów po utworzeniu klastra, aby użyć puli węzłów typu spot. Dodawanie dodatkowej puli węzłów zostanie uwzględnione w późniejszym kroku.

Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.14 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

### <a name="limitations"></a>Ograniczenia

Podczas tworzenia klastrów usługi AKS i zarządzania nimi za pomocą puli węzłów typu spot obowiązują następujące ograniczenia:

* Pula węzłów typu spot nie może być domyślną pulą węzłów klastra. Pula węzłów typu spot może być używana tylko dla puli pomocniczej.
* Nie można uaktualnić puli węzłów typu spot, ponieważ pule węzłów typu spot nie mogą zagwarantować odień i opróżniania. Należy zastąpić istniejącą pulę węzłów typu spot nową pulą, aby wykonać operacje, takie jak uaktualnianie wersji kubernetes. Aby zastąpić pulę węzłów typu spot, utwórz nową pulę węzłów typu spot z inną wersją kubernetes, poczekaj, aż jej stan będzie *gotowy,* a następnie usuń starą pulę węzłów.
* Płaszczyzny sterowania i pul węzłów nie można uaktualnić w tym samym czasie. Należy uaktualnić je oddzielnie lub usunąć pulę węzłów typu spot, aby jednocześnie uaktualnić płaszczyznę sterowania i pozostałe pule węzłów.
* Pula węzłów typu spot musi używać Virtual Machine Scale Sets.
* Po utworzeniu nie można zmienić wartości ScaleSetPriority ani SpotMaxPrice.
* Podczas ustawiania wartości SpotMaxPrice wartość musi być -1 lub wartość dodatnia z maksymalnie pięcioma miejscami dziesiętnym.
* Pula węzłów typu spot będzie mieć etykietę *kubernetes.azure.com/scalesetpriority:spot*, etykietę kubernetes.azure.com/scalesetpriority=spot:NoSchedule *,* a zasobniki systemowe będą mieć koligacji.
* Musisz dodać odpowiednią [tolerancję,][spot-toleration] aby zaplanować obciążenia w puli węzłów typu spot.

## <a name="add-a-spot-node-pool-to-an-aks-cluster"></a>Dodawanie puli węzłów typu spot do klastra usługi AKS

Należy dodać pulę węzłów typu spot do istniejącego klastra z włączonymi wieloma pulami węzłów. Więcej szczegółów na temat tworzenia klastra usługi AKS z wieloma pulami węzłów można [znaleźć tutaj.][use-multiple-node-pools]

Utwórz pulę węzłów przy użyciu [narzędzia az aks nodepool add][az-aks-nodepool-add].
```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name spotnodepool \
    --priority Spot \
    --eviction-policy Delete \
    --spot-max-price -1 \
    --enable-cluster-autoscaler \
    --min-count 1 \
    --max-count 3 \
    --no-wait
```

Domyślnie pulę węzłów o  priorytecie *Regular* tworzy się w klastrze usługi AKS podczas tworzenia klastra z wieloma pulami węzłów. Powyższe polecenie dodaje pulę węzłów pomocniczych do istniejącego klastra usługi AKS z *priorytetem* *Spot.* Priorytet *spot* *sprawia,* że pula węzłów jest pulą węzłów typu spot. W *powyższym przykładzie parametr eviction-policy* jest ustawiony na wartość *Usuń,* co jest wartością domyślną. Po skonfigurowaniu zasad [eksmisji][eviction-policy] na Usuń węzły w bazowym zestawie skalowania puli węzłów są usuwane po eksmisji.  Można również ustawić zasady eksmisji na *cofanie alokacji*. W przypadku ustawienia zasad eksmisji na cofanie alokacji węzły w bazowym zestawie skalowania są ustawiane na stan zatrzymano-cofnieno alokację podczas eksmisji.  Węzły w stanie cofania przydziału są wliczane do limitu przydziału zasobów obliczeniowych i mogą powodować problemy ze skalowaniem lub uaktualnianiem klastra. Wartości *priorytetów* *i zasad eksmisji* można ustawić tylko podczas tworzenia puli węzłów. Tych wartości nie można zaktualizować później.

Polecenie włącza również funkcję [automatycznego skalowania klastra][cluster-autoscaler], która jest zalecana do użycia z pulami węzłów typu spot. W zależności od obciążeń uruchomionych w klastrze funkcja automatycznego skalowania klastra skaluje w górę i w dół liczbę węzłów w puli węzłów. W przypadku pul węzłów typu spot funkcja automatycznego skalowania klastra będzie skalować w górę liczbę węzłów po eksmisji, jeśli nadal będą potrzebne dodatkowe węzły. W przypadku zmiany maksymalnej liczby węzłów, które może mieć pula węzłów, należy również dostosować wartość skojarzoną `maxCount` z programem do automatycznego skalowania klastra. Jeśli nie używasz automatycznego skalowania klastra, po eksmisji pula typu spot ostatecznie zmniejszy się do zera i będzie wymagać ręcznej operacji odbierania dodatkowych węzłów typu spot.

> [!Important]
> Planowanie obciążeń tylko w pulach węzłów typu spot, które mogą obsługiwać przerwy, takie jak zadania przetwarzania wsadowego i środowiska testowe. Zaleca się skonfigurowanie [][taints-tolerations] w puli węzłów typu spot podzesieć i tolerancji, aby upewnić się, że w puli węzłów typu spot są zaplanowane tylko obciążenia, które mogą obsługiwać eksmisje węzłów. Na przykład powyższe polecenie domyślne dodaje do  węzła kubernetes.azure.com/scalesetpriority=spot:NoSchedule więc w tym węźle są zaplanowane tylko zasobniki z odpowiednią tolerancją.

## <a name="verify-the-spot-node-pool"></a>Weryfikowanie puli węzłów typu spot

Aby sprawdzić, czy pula węzłów została dodana jako pula węzłów typu spot:

```azurecli
az aks nodepool show --resource-group myResourceGroup --cluster-name myAKSCluster --name spotnodepool
```

Upewnij się, że dla pozycji *scaleSetPriority* *jest* spot .

Aby zaplanować uruchamianie zasobnika w węźle typu spot, dodaj tolerancję odpowiadającą wartości zastosowanej do węzła typu spot. W poniższym przykładzie pokazano część pliku yaml, która definiuje tolerancję, *która* odpowiada kubernetes.azure.com/scalesetpriority=spot:NoSchedule wartości użytej w poprzednim kroku.

```yaml
spec:
  containers:
  - name: spot-example
  tolerations:
  - key: "kubernetes.azure.com/scalesetpriority"
    operator: "Equal"
    value: "spot"
    effect: "NoSchedule"
   ...
```

Po wdrożeniu zasobnika z tą tolerancją kubernetes może pomyślnie zaplanować zasobnik w węzłach z zastosowanym tasiemcem.

## <a name="max-price-for-a-spot-pool"></a>Maksymalna cena puli spot
[Ceny wystąpień typu spot są zmienne][pricing-spot]na podstawie regionu i SKU. Aby uzyskać więcej informacji, zobacz cennik systemów [Linux i][pricing-linux] [Windows.][pricing-windows]

W przypadku zmiennych cen można ustawić maksymalną cenę w dolarach amerykańskich (USD) z dokładnością do 5 miejsc dziesiętnych. Na przykład wartość *0,98765* byłaby maksymalną ceną 0,98765 USD za godzinę. Jeśli ustawisz maksymalną cenę *na -1,* wystąpienie nie zostanie eksmisji na podstawie ceny. Cena wystąpienia będzie bieżącą ceną usługi Spot lub ceną wystąpienia standardowego, w zależności od tego, która wartość jest mniejsza, o ile dostępna jest pojemność i limit przydziału.

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano sposób dodawania puli węzłów typu spot do klastra usługi AKS. Aby uzyskać więcej informacji na temat kontrolowania zasobników w pulach węzłów, zobacz Najlepsze rozwiązania dotyczące zaawansowanych [funkcji harmonogramu w u usługach AKS.][operator-best-practices-advanced-scheduler]

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az_aks_nodepool_add
[cluster-autoscaler]: cluster-autoscaler.md
[eviction-policy]: ../virtual-machine-scale-sets/use-spot.md#eviction-policy
[kubernetes-concepts]: concepts-clusters-workloads.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[pricing-linux]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/
[pricing-spot]: ../virtual-machine-scale-sets/use-spot.md#pricing
[pricing-windows]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/
[spot-toleration]: #verify-the-spot-node-pool
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[use-multiple-node-pools]: use-multiple-node-pools.md
[vmss-spot]: ../virtual-machine-scale-sets/use-spot.md