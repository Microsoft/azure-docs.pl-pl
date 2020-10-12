---
title: Wersja zapoznawcza — Dodawanie puli węzłów dodatkowych do klastra usługi Azure Kubernetes Service (AKS)
description: Dowiedz się, jak dodać pulę węzłów dodatkowych do klastra usługi Azure Kubernetes Service (AKS).
services: container-service
ms.service: container-service
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: dbb003c287a18810c2c14c4f2ea401fa55cca427
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87987294"
---
# <a name="preview---add-a-spot-node-pool-to-an-azure-kubernetes-service-aks-cluster"></a>Wersja zapoznawcza — Dodawanie puli węzłów dodatkowych do klastra usługi Azure Kubernetes Service (AKS)

Pula węzłów dodatkowych jest pulą węzłów utworzoną przez [zestaw skalowania maszyn wirtualnych][vmss-spot]. Używanie dodatkowych maszyn wirtualnych dla węzłów z klastrem AKS umożliwia korzystanie z niewykorzystywanych pojemności na platformie Azure przy znaczącym obciążeniu. Ilość dostępnej niewykorzystanej pojemności zależy od wielu czynników, w tym rozmiaru węzła, regionu i godziny dnia.

Podczas wdrażania puli węzłów dodatkowych platforma Azure przydzieli węzły dodatkowe, jeśli dostępna jest pojemność. Ale nie ma umowy SLA dla węzłów dodatkowych. Zestaw skalowania punktowego, który wykonuje kopię zapasową puli węzłów dodatkowych, jest wdrażany w jednej domenie błędów i nie oferuje gwarancji wysokiej dostępności. W dowolnym momencie, gdy platforma Azure potrzebuje pojemności z powrotem, infrastruktura platformy Azure wykryje węzły dodatkowe.

Węzły dodatkowe są doskonałe dla obciążeń, które mogą obsługiwać przerwy, wczesne zakończenia lub wykluczenia. Na przykład obciążenia takie jak zadania przetwarzania wsadowego, środowiska deweloperskie i testowe oraz duże obciążenia obliczeniowe mogą być dobrym kandydatami do zaplanowania w puli węzłów dodatkowych.

W tym artykule opisano Dodawanie dodatkowej puli węzłów dodatkowych do istniejącego klastra usługi Azure Kubernetes Service (AKS).

W tym artykule założono podstawową wiedzę na temat koncepcji Kubernetes i Azure Load Balancer. Aby uzyskać więcej informacji, zobacz [Podstawowe pojęcia dotyczące usługi Azure Kubernetes Service (AKS)][kubernetes-concepts].

Ta funkcja jest obecnie w wersji zapoznawczej.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Podczas tworzenia klastra w celu używania puli węzłów dodatkowych, ten klaster musi również używać Virtual Machine Scale Sets dla pul węzłów i usługi równoważenia obciążenia w *warstwie Standardowa* . Należy również dodać dodatkową pulę węzłów po utworzeniu klastra do używania puli węzłów dodatkowych. Dodawanie dodatkowej puli węzłów jest omówione w późniejszym kroku, ale najpierw musisz włączyć funkcję w wersji zapoznawczej.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="register-spotpoolpreview-preview-feature"></a>Rejestrowanie funkcji spotpoolpreview w wersji zapoznawczej

Aby utworzyć klaster AKS, który używa puli węzłów dodatkowych, należy włączyć flagę funkcji *spotpoolpreview* w subskrypcji. Ta funkcja udostępnia najnowszy zestaw rozszerzeń usługi podczas konfigurowania klastra.

Zarejestruj flagę funkcji *spotpoolpreview* za pomocą polecenia [AZ Feature Register][az-feature-register] , jak pokazano w następującym przykładzie:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "spotpoolpreview"
```

Wyświetlenie stanu *rejestracji*może potrwać kilka minut. Stan rejestracji można sprawdzić za pomocą polecenia [AZ Feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/spotpoolpreview')].{Name:name,State:properties.state}"
```

Gdy wszystko będzie gotowe, Odśwież rejestrację dostawcy zasobów *Microsoft. ContainerService* za pomocą polecenia [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="install-aks-preview-cli-extension"></a>Instalowanie rozszerzenia interfejsu wiersza polecenia aks-preview

Aby utworzyć klaster AKS, który używa puli węzłów dodatkowych, wymagany jest interfejs wiersza polecenia *AKS-Preview* w wersji 0.4.32 lub nowszej. Zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure w *wersji zapoznawczej* przy użyciu poleceń [AZ Extension Add][az-extension-add] , a następnie wyszukaj wszystkie dostępne aktualizacje za pomocą polecenia [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview
 
# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Ograniczenia

Podczas tworzenia klastrów AKS i zarządzania nimi za pomocą puli węzłów dodatkowych obowiązują następujące ograniczenia:

* Pula węzłów dodatkowych nie może być domyślną pulą węzłów klastra. Puli węzłów dodatkowych można używać tylko dla puli pomocniczej.
* Nie można uaktualnić puli węzłów dodatkowych, ponieważ pule węzłów dodatkowych nie mogą gwarantować Cordon i opróżniania. Należy zamienić istniejącą pulę węzłów dodatkowych na nową, aby wykonać operacje, takie jak uaktualnienie wersji Kubernetes. Aby zastąpić pulę węzłów dodatkowych, Utwórz nową pulę węzła dodatkowego z inną wersją Kubernetes, poczekaj, aż jej stan będzie *gotowa*, a następnie usuń starą pulę węzłów.
* Nie można jednocześnie uaktualnić płaszczyzny kontroli i pul węzłów. Należy je uaktualnić osobno lub usunąć pulę węzłów dodatkowych, aby uaktualnić płaszczyznę kontroli i pozostałe pule węzłów w tym samym czasie.
* Pula węzłów dodatkowych musi używać Virtual Machine Scale Sets.
* Po utworzeniu nie można zmienić ScaleSetPriority ani SpotMaxPrice.
* Podczas ustawiania SpotMaxPrice wartość musi być równa-1 lub wartość dodatnia z maksymalnie pięć miejsc dziesiętnych.
* Pula węzłów dodatkowych będzie miała etykietę *Kubernetes.Azure.com/scalesetpriority:Spot*, *Kubernetes.Azure.com/scalesetpriority=Spot:NoSchedule*i zasobniki systemowe będą mieć antykoligacje.
* Musisz dodać [odpowiednie tolerowanie][spot-toleration] , aby zaplanować obciążenia w puli węzłów dodatkowych.

## <a name="add-a-spot-node-pool-to-an-aks-cluster"></a>Dodawanie puli węzłów typu spot do klastra usługi AKS

Należy dodać pulę węzłów dodatkowych do istniejącego klastra z włączonymi wieloma pulami węzłów. Więcej szczegółowych informacji na temat tworzenia klastra AKS z wieloma pulami węzłów można znaleźć [tutaj][use-multiple-node-pools].

Utwórz pulę węzłów za pomocą polecenia [AZ AKS nodepool Add][az-aks-nodepool-add].
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

Domyślnie podczas tworzenia klastra z wieloma pulami węzłów w klastrze AKS należy utworzyć pulę węzłów o *priorytecie* *regularnym* . Powyższe polecenie dodaje pomocniczą pulę węzłów do istniejącego klastra AKS o *priorytecie* *.* *Priorytet* *obszaru Ustawia pulę węzłów dodatkowych.* Parametr *wykluczanie zasad* jest ustawiany na wartość *delete* w powyższym przykładzie, który jest wartością domyślną. Po ustawieniu [Zasady wykluczania][eviction-policy] do *usunięcia*węzły w podstawowym zestawie skalowania puli węzłów są usuwane, gdy zostaną wykluczone. Można również ustawić zasady wykluczania do *alokacji*. Po ustawieniu Zasady wykluczania na *Cofnij przydział*węzły w źródłowym zestawie skalowania są ustawiane na stan zatrzymany bez alokacji podczas wykluczania. Liczba węzłów w stanie zatrzymania bez przydziału względem limitu przydziału obliczeń i może powodować problemy ze skalowaniem klastra lub uaktualnieniem. Wartości *priorytetu* i *wykluczenia-zasady* można ustawić tylko podczas tworzenia puli węzłów. Tych wartości nie można później zaktualizować.

Polecenie włącza również [Automatyczne skalowanie klastra][cluster-autoscaler], zalecane do użycia z pulami węzłów dodatkowych. W zależności od obciążenia działającego w klastrze automatyczne skalowanie klastra skaluje się w górę i skaluje liczbę węzłów w puli węzłów. W przypadku pul węzła dodatkowego automatyczne skalowanie klastra będzie skalować liczbę węzłów po wykluczeniu, jeśli nadal będą potrzebne dodatkowe węzły. Jeśli zmienisz maksymalną liczbę węzłów, jaką może mieć Pula węzłów, należy również dostosować `maxCount` wartość skojarzoną z automatycznym skalowaniem klastra. Jeśli podczas wykluczania nie używasz automatycznego skalowania klastra, pula dodatkowa będzie ostatecznie zmniejszać do zera i wymagać ręcznej operacji w celu uzyskania dodatkowych węzłów dodatkowych.

> [!Important]
> Zaplanuj obciążenia tylko w pulach węzłów dodatkowych, które mogą obsłużyć przerwy, takie jak zadania przetwarzania wsadowego i środowiska testowania. Zaleca się skonfigurowanie przydziałów [i tolerowania][taints-tolerations] w puli węzłów punktowych, aby upewnić się, że tylko obciążenia, które mogą obsłużyć wykluczenia węzłów, zaplanowali w puli węzłów dodatkowych. Na przykład powyższym poleceniem domyślnie dodaje jest *Kubernetes.Azure.com/scalesetpriority=Spot:NoSchedule* , więc w tym węźle zaplanowano tylko te same wartości.

## <a name="verify-the-spot-node-pool"></a>Weryfikowanie puli węzłów dodatkowych

Aby sprawdzić, czy pula węzłów została dodana jako Pula węzłów dodatkowych:

```azurecli
az aks nodepool show --resource-group myResourceGroup --cluster-name myAKSCluster --name spotnodepool
```

Potwierdź, że *scaleSetPriority* jest na *miejscu*.

Aby zaplanować uruchomienie elementu na węźle dodatkowym, należy dodać tolerowanie odnoszące się do zmiany koloru w węźle. W poniższym przykładzie przedstawiono część pliku YAML, który definiuje tolerowanie, który odnosi się do *Kubernetes.Azure.com/scalesetpriority=Spot:NoSchedule* .

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

Gdy zostanie wdrożony element pod z tym tolerowaniem, Kubernetes może pomyślnie zaplanować element na węzłach z zastosowaniem zmiany.

## <a name="max-price-for-a-spot-pool"></a>Maksymalna cena puli dodatkowych
[Cennik wystąpień dodatkowych to zmienna][pricing-spot], na podstawie regionu i jednostki SKU. Aby uzyskać więcej informacji, zobacz cennik dla systemów [Linux][pricing-linux] i [Windows][pricing-windows].

W przypadku zmiennych cenowych istnieje możliwość ustawienia maksymalnej ceny w dolarach amerykańskich (USD) przy użyciu maksymalnie 5 miejsc dziesiętnych. Na przykład wartość *0,98765* to maksymalna cena $0,98765 USD za godzinę. Jeśli ustawisz maksymalną wartość *-1*, wystąpienie nie zostanie wykluczone w oparciu o cenę. Cena dla tego wystąpienia będzie aktualna cena za wystąpienie standardowe lub cena w przypadku wystąpienia standardowego, w zależności od tego, czy jest to mniejsze, pod warunkiem, że dostępne są pojemności i limity przydziału.

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano sposób dodawania puli węzłów dodatkowych do klastra AKS. Aby uzyskać więcej informacji na temat sterowania zestawami w puli węzłów, zobacz [najlepsze rozwiązania dotyczące zaawansowanych funkcji usługi Scheduler w AKS][operator-best-practices-advanced-scheduler].

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-deploy-create]: /cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-template-deploy]: ../azure-resource-manager/templates/deploy-cli.md#deployment-scope
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