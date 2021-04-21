---
title: Używanie stref dostępności w Azure Kubernetes Service (AKS)
description: Dowiedz się, jak utworzyć klaster, który dystrybuuje węzły między strefami dostępności w Azure Kubernetes Service (AKS)
services: container-service
ms.custom: fasttrack-edit, references_regions, devx-track-azurecli
ms.topic: article
ms.date: 03/16/2021
ms.openlocfilehash: 796cb0e2b76dc2a04834df61c053c5ad2ceb25fd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769629"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>Tworzenie klastra Azure Kubernetes Service (AKS) z zastosowaniem stref dostępności

Klaster Azure Kubernetes Service (AKS) dystrybuuje zasoby, takie jak węzły i magazyn, między logiczne sekcje podstawowej infrastruktury platformy Azure. Ten model wdrażania w przypadku korzystania ze stref dostępności gwarantuje, że węzły w danej strefie dostępności są fizycznie oddzielone od węzłów zdefiniowanych w innej strefie dostępności. Klastry usługi AKS wdrożone z wieloma strefami dostępności skonfigurowanymi w klastrze zapewniają wyższy poziom dostępności w celu ochrony przed awarią sprzętu lub zdarzeniem planowanej konserwacji.

Dzięki zdefiniowaniu pul węzłów w klastrze tak, aby obejmować wiele stref, węzły w danej puli węzłów mogą kontynuować pracę nawet w przypadku awarii jednej strefy. Aplikacje mogą nadal być dostępne nawet w przypadku awarii fizycznej w jednym centrum danych, jeśli są orkiestrowane w celu tolerowania awarii podzbioru węzłów.

W tym artykule pokazano, jak utworzyć klaster usługi AKS i dystrybuować składniki węzła między strefami dostępności.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Musisz mieć zainstalowany i skonfigurowany interfejs wiersza polecenia platformy Azure w wersji 2.0.76 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="limitations-and-region-availability"></a>Ograniczenia i dostępność regionów

Klastry usługi AKS można obecnie tworzyć przy użyciu stref dostępności w następujących regionach:

* Australia Wschodnia
* Brazylia Południowa
* Kanada Środkowa
* Central US
* East US 
* Wschodnie stany USA 2
* Francja Środkowa
* Niemcy Zachodnio-środkowe
* Japonia Wschodnia
* Europa Północna
* Azja Południowo-Wschodnia
* South Central US
* Południowe Zjednoczone Królestwo
* US Gov Wirginia
* West Europe
* Zachodnie stany USA 2

Podczas tworzenia klastra usługi AKS przy użyciu stref dostępności obowiązują następujące ograniczenia:

* Strefy dostępności można zdefiniować tylko podczas tworzenia klastra lub puli węzłów.
* Nie można zaktualizować ustawień strefy dostępności po utworzeniu klastra. Nie można również zaktualizować istniejącego klastra stref dostępności w celu używania stref dostępności.
* Wybrany rozmiar węzła (sku maszyny wirtualnej) musi być dostępny we wszystkich wybranych strefach dostępności.
* Klastry z włączonymi strefami dostępności wymagają użycia usług Azure Load Balancer w standardowych strefach do dystrybucji w różnych strefach. Ten typ usługi równoważenia obciążenia można zdefiniować tylko w czasie tworzenia klastra. Aby uzyskać więcej informacji i ograniczenia standardowego równoważenia obciążenia, zobacz Ograniczenia standardowej wersji SKU usługi [Azure Load Balancer.][standard-lb-limitations]

### <a name="azure-disks-limitations"></a>Ograniczenia dysków platformy Azure

Woluminy, które używają dysków zarządzanych platformy Azure, nie są obecnie zasobami strefowo nadmiarowych. Woluminy nie mogą być dołączane między strefami i muszą znajdować się w tej samej strefie co dany węzeł hostowania zasobnika docelowego.

Platforma Kubernetes wie o strefach dostępności platformy Azure od wersji 1.12. Obiekt PersistentVolumeClaim odwołujący się do dysku zarządzanego platformy Azure można wdrożyć w wielosełowym klastrze usługi AKS, a platforma [Kubernetes](https://kubernetes.io/docs/setup/best-practices/multiple-zones/#storage-access-for-zones) zajmie się planowaniem dowolnego zasobnika, który będzie przyjmować ten element w odpowiedniej strefie dostępności.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>Omówienie stref dostępności dla klastrów usługi AKS

Strefy dostępności to oferta wysokiej dostępności, która chroni aplikacje i dane przed awariami centrum danych. Strefy to unikatowe lokalizacje fizyczne w regionie świadczenia usługi Azure. Każda strefa składa się z co najmniej jednego centrum danych wyposażonego w niezależne zasilanie, chłodzenie i sieć. Aby zapewnić odporność, we wszystkich regionach z włączoną obsługą strefy zawsze istnieje więcej niż jedna strefa. Fizyczna separacja stref dostępności w ramach regionu chroni aplikacje i dane przed awariami centrum danych.

Aby uzyskać więcej informacji, zobacz [Co to są strefy dostępności na platformie Azure?][az-overview].

Klastry usługi AKS wdrażane przy użyciu stref dostępności mogą dystrybuować węzły między wieloma strefami w jednym regionie. Na przykład klaster w regionie Wschodnie usa 2 może  *tworzyć* węzły we wszystkich trzech strefach dostępności w   regionie Wschodnie usa *2.* Taka dystrybucja zasobów klastra usługi AKS zwiększa dostępność klastra, ponieważ są one odporne na awarie określonej strefy.

![Dystrybucja węzłów usługi AKS między strefami dostępności](media/availability-zones/aks-availability-zones.png)

Jeśli pojedyncza strefa stanie się niedostępna, aplikacje będą nadal działać, jeśli klaster jest rozłożony na wiele stref.

## <a name="create-an-aks-cluster-across-availability-zones"></a>Tworzenie klastra usługi AKS w różnych strefach dostępności

Podczas tworzenia klastra za pomocą [polecenia az aks create][az-aks-create] parametr definiuje strefy, w których są `--zones` wdrażane węzły agenta. Składniki płaszczyzny sterowania, takie jak etcd lub interfejs API, są rozłożone w dostępnych strefach w regionie, jeśli zdefiniujesz `--zones` parametr w czasie tworzenia klastra. Określone strefy, w których są rozłożone składniki płaszczyzny sterowania, są niezależne od tego, co jawnie wybrano dla początkowej puli węzłów.

Jeśli podczas tworzenia klastra usługi AKS nie zdefiniuje się żadnych stref dla domyślnej puli agentów, składniki płaszczyzny sterowania nie będą rozkładać się między strefy dostępności. Dodatkowe pule węzłów można dodać za pomocą polecenia [az aks nodepool add][az-aks-nodepool-add] i określić dla nowych węzłów, ale nie spowoduje to zmiany rozrzuty płaszczyzny sterowania między `--zones` strefami. Ustawienia strefy dostępności można zdefiniować tylko w czasie tworzenia puli klastrów lub węzłów.

Poniższy przykład tworzy klaster usługi AKS o *nazwie myAKSCluster* w grupie zasobów *o nazwie myResourceGroup*. Tworzone są *łącznie 3* węzły — jeden agent w strefie *1,* jeden w *2,* a następnie jeden w *3*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --generate-ssh-keys \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --node-count 3 \
    --zones 1 2 3
```

Utworzenie klastra AKS może potrwać kilka minut.

Podczas podejmowania decyzji o tym, do której strefy powinien należeć nowy węzeł, dla danej puli węzłów usługi AKS zostanie zastosowanie najlepszego równoważenia strefy nakładu pracy oferowanego przez bazową usługę [Azure Virtual Machine Scale Sets][vmss-zone-balancing]. Pula węzłów usługi AKS jest uznawana za "zrównoważoną", jeśli każda strefa ma taką samą liczbę maszyn wirtualnych lub + 1 maszynę wirtualną we wszystkich innych strefach \- dla zestawu skalowania.

## <a name="verify-node-distribution-across-zones"></a>Weryfikowanie dystrybucji węzłów między strefami

Gdy klaster jest gotowy, wyświetl listę węzłów agenta w zestawie skalowania, aby zobaczyć, w jakiej strefie dostępności są one wdrażane.

Najpierw pobierz poświadczenia klastra AKS za pomocą [polecenia az aks get-credentials:][az-aks-get-credentials]

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Następnie użyj polecenia [kubectl describe,][kubectl-describe] aby wyświetlić listę węzłów w klastrze i filtrować według *failure-domain.beta.kubernetes.io/zone* klastra. Poniższy przykład jest dla powłoki Bash.

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

Następujące przykładowe dane wyjściowe pokazują trzy węzły rozproszone w określonym regionie i strefach dostępności, takie jak *eastus2-1* dla pierwszej strefy dostępności i *eastus2-2* dla drugiej strefy dostępności:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

Podczas dodawania kolejnych węzłów do puli agentów platforma Azure automatycznie dystrybuuje bazowe maszyny wirtualne w określonych strefach dostępności.

Należy pamiętać, że w nowszych wersjach usługi Kubernetes (1.17.0 i nowszych) oprócz przestarzałej etykiety używa ona `topology.kubernetes.io/zone` nowszej etykiety `failure-domain.beta.kubernetes.io/zone` . Możesz uzyskać taki sam wynik jak powyżej za pomocą polecenia , uruchamiając następujący skrypt:

```console
kubectl get nodes -o custom-columns=NAME:'{.metadata.name}',REGION:'{.metadata.labels.topology\.kubernetes\.io/region}',ZONE:'{metadata.labels.topology\.kubernetes\.io/zone}'
```

Dzięki temu można uzyskać bardziej zwięzłe dane wyjściowe:

```console
NAME                                REGION   ZONE
aks-nodepool1-34917322-vmss000000   eastus   eastus-1
aks-nodepool1-34917322-vmss000001   eastus   eastus-2
aks-nodepool1-34917322-vmss000002   eastus   eastus-3
```

## <a name="verify-pod-distribution-across-zones"></a>Weryfikowanie dystrybucji zasobników między strefami

Zgodnie z dokumentacją w dokumencie [Well-Known Labels, Annotations and Taints][kubectl-well_known_labels](Dobrze znane etykiety, adnotacje i tainty) usługa Kubernetes używa etykiety do automatycznego dystrybuowania zasobników w kontrolerze replikacji lub usłudze w różnych dostępnych `failure-domain.beta.kubernetes.io/zone` strefach. Aby to przetestować, możesz przeskalować klaster w górę z 3 do 5 węzłów, aby sprawdzić poprawność rozrzucania zasobników:

```azurecli-interactive
az aks scale \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 5
```

Po zakończeniu operacji skalowania po kilku minutach polecenie w powłoce Bash powinno zwrócić dane wyjściowe `kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"` podobne do tych przykładowych:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
Name:       aks-nodepool1-28993262-vmss000003
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000004
            failure-domain.beta.kubernetes.io/zone=eastus2-2
```

Mamy teraz dwa dodatkowe węzły w strefach 1 i 2. Możesz wdrożyć aplikację składającą się z trzech replik. Jako przykładu użyjemy NGINX:

```console
kubectl create deployment nginx --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
kubectl scale deployment nginx --replicas=3
```

Wyświetlając węzły, w których są uruchomione zasobniki, zobaczysz, że zasobniki są uruchomione w węzłach odpowiadających trzem różnym strefom dostępności. Na przykład polecenie w `kubectl describe pod | grep -e "^Name:" -e "^Node:"` powłoce Bash umożliwia uzyskiwanie danych wyjściowych podobnych do tych:

```console
Name:         nginx-6db489d4b7-ktdwg
Node:         aks-nodepool1-28993262-vmss000000/10.240.0.4
Name:         nginx-6db489d4b7-v7zvj
Node:         aks-nodepool1-28993262-vmss000002/10.240.0.6
Name:         nginx-6db489d4b7-xz6wj
Node:         aks-nodepool1-28993262-vmss000004/10.240.0.8
```

Jak widać w poprzednich danych wyjściowych, pierwszy zasobnik jest uruchomiony w węźle 0, który znajduje się w strefie dostępności `eastus2-1` . Drugi zasobnik jest uruchomiony w węźle 2, który odpowiada właściwości , a trzeci w `eastus2-3` węźle 4 w . `eastus2-2` Bez dodatkowej konfiguracji kubernetes poprawnie rozrzuca zasobniki we wszystkich trzech strefach dostępności.

## <a name="next-steps"></a>Następne kroki

W tym artykule szczegółowo opisano sposób tworzenia klastra usługi AKS, który używa stref dostępności. Aby uzyskać więcej informacji na temat klastrów o wysokiej dostępie, zobacz Najlepsze rozwiązania dotyczące ciągłości działania i odzyskiwania po [awarii w u usługi AKS.][best-practices-bc-dr]

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-overview]: ../availability-zones/az-overview.md
[best-practices-bc-dr]: operator-best-practices-multi-region.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[standard-lb-limitations]: load-balancer-standard.md#limitations
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[vmss-zone-balancing]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing

<!-- LINKS - external -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-well_known_labels]: https://kubernetes.io/docs/reference/labels-annotations-taints/
