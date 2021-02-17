---
title: Używanie stref dostępności w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak utworzyć klaster, który dystrybuuje węzły w strefach dostępności w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.custom: fasttrack-edit, references_regions, devx-track-azurecli
ms.topic: article
ms.date: 09/04/2020
ms.openlocfilehash: 481dc112b36e1f9fba3739f5b88e34d15d538e47
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100558966"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>Tworzenie klastra usługi Azure Kubernetes Service (AKS) korzystającego ze stref dostępności

Klaster usługi Azure Kubernetes Service (AKS) dystrybuuje zasoby, takie jak węzły i magazyn, w logicznych sekcjach podstawowej infrastruktury platformy Azure. Ten model wdrażania w przypadku korzystania ze stref dostępności gwarantuje, że węzły w danej strefie dostępności są fizycznie oddzielone od tych, które są zdefiniowane w innej strefie dostępności. Klastry AKS wdrożone z wieloma strefami dostępności skonfigurowanymi w ramach klastra zapewniają wyższy poziom dostępności do ochrony przed awariami sprzętowymi lub zaplanowanym zdarzeniem konserwacji.

Definiując pule węzłów w klastrze w celu rozdzielenia wielu stref, węzły w danej puli węzłów są w stanie kontynuować działanie nawet wtedy, gdy jedna strefa przestała działać. Aplikacje mogą być nadal dostępne nawet wtedy, gdy w jednym centrum danych występuje awaria fizyczna, jeśli jest ona niedostępna do tolerowanej awarii podzbioru węzłów.

W tym artykule pokazano, jak utworzyć klaster AKS i rozesłać składniki węzła w strefach dostępności.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0.76 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="limitations-and-region-availability"></a>Ograniczenia i dostępność regionów

Klastry AKS można obecnie tworzyć przy użyciu stref dostępności w następujących regionach:

* Australia Wschodnia
* Kanada Środkowa
* Central US
* East US 
* Wschodnie stany USA 2
* Francja Środkowa
* Niemcy Środkowo-Zachodnie
* Japonia Wschodnia
* Europa Północna
* Azja Południowo-Wschodnia
* South Central US
* Południowe Zjednoczone Królestwo
* US Gov Wirginia
* West Europe
* Zachodnie stany USA 2

Podczas tworzenia klastra AKS przy użyciu stref dostępności są stosowane następujące ograniczenia:

* Strefy dostępności można definiować tylko po utworzeniu klastra lub puli węzłów.
* Nie można zaktualizować ustawień strefy dostępności po utworzeniu klastra. Nie można również zaktualizować istniejącego klastra strefowego niebędącego dostępnością do korzystania ze stref dostępności.
* Wybrana opcja rozmiar węzła (jednostka SKU maszyny wirtualnej) musi być dostępna dla wszystkich wybranych stref dostępności.
* Klastry z włączonymi strefami dostępności wymagają użycia usługi równoważenia obciążenia w warstwie Standardowa na potrzeby dystrybucji między strefami. Ten typ modułu równoważenia obciążenia można zdefiniować tylko w czasie tworzenia klastra. Aby uzyskać więcej informacji i ograniczeń dotyczących standardowego modułu równoważenia obciążenia, zobacz [ograniczenia dotyczące standardowej jednostki SKU modułu równoważenia obciążenia platformy Azure][standard-lb-limitations].

### <a name="azure-disks-limitations"></a>Ograniczenia dotyczące dysków platformy Azure

Woluminy korzystające z usługi Azure Managed disks nie są obecnie strefowo nadmiarowe. Nie można dołączać woluminów między strefami i muszą one znajdować się w tej samej strefie co węzeł obsługujący element docelowy pod.

Kubernetes ma świadomość stref dostępności platformy Azure od wersji 1,12. Można wdrożyć obiekt PersistentVolumeClaim odwołujący się do dysku zarządzanego platformy Azure w klastrze AKS z obsługą wielostrefową, a [Kubernetes zaplanował zaplanowanie](https://kubernetes.io/docs/setup/best-practices/multiple-zones/#storage-access-for-zones) każdego oświadczenia tego obwodu PVC w prawidłowej strefie dostępności.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>Przegląd stref dostępności dla klastrów AKS

Strefy dostępności to oferta wysokiej dostępności, która chroni Twoje aplikacje i dane przed awariami centrów danych. Strefy są unikatowymi lokalizacjami fizycznymi w regionie świadczenia usługi Azure. Każda strefa składa się z co najmniej jednego centrum danych wyposażonego w niezależne zasilanie, chłodzenie i sieć. W celu zapewnienia odporności zawsze istnieje więcej niż jedna strefa we wszystkich regionach z włączoną obsługą strefy. Fizyczna separacja stref dostępności w ramach regionu chroni aplikacje i dane przed awariami centrum danych.

Aby uzyskać więcej informacji, zobacz [co to są strefy dostępności na platformie Azure?][az-overview]

Klastry AKS wdrożone przy użyciu stref dostępności umożliwiają dystrybucję węzłów między wieloma strefami w jednym regionie. Na przykład klaster w regionie  *Wschodnie stany USA 2*   może tworzyć węzły we wszystkich trzech strefach dostępności w *regionach Wschodnie stany USA 2*. Ta dystrybucja zasobów klastra AKS zwiększa dostępność klastra w miarę odporności na awarię określonej strefy.

![Rozkład węzłów AKS w różnych strefach dostępności](media/availability-zones/aks-availability-zones.png)

Jeśli jedna strefa będzie niedostępna, aplikacje kontynuują działanie, Jeśli klaster jest rozłożony w wielu strefach.

## <a name="create-an-aks-cluster-across-availability-zones"></a>Tworzenie klastra AKS w różnych strefach dostępności

W przypadku tworzenia klastra przy użyciu polecenia [AZ AKS Create][az-aks-create] `--zones` parametr określa, które węzły agenta stref są wdrożone w programie. Składniki płaszczyzny kontroli, takie jak etcd lub interfejs API, są rozłożone w strefach dostępnych w regionie, jeśli zdefiniujesz `--zones` parametr podczas tworzenia klastra. Strefy, w których są rozmieszczone składniki płaszczyzny kontroli, są niezależne od wybranych dla puli węzłów początkowych.

Jeśli nie zdefiniowano żadnych stref dla domyślnej puli agentów podczas tworzenia klastra AKS, składniki płaszczyzny kontroli nie są gwarantowane do rozprzestrzeniania się między strefami dostępności. Można dodać dodatkowe pule węzłów za pomocą polecenia [AZ AKS nodepool Add][az-aks-nodepool-add] i określić `--zones` dla nowych węzłów, ale nie zmieni się, jak płaszczyzna kontroli została rozłożona między strefami. Ustawienia stref dostępności można definiować tylko w przypadku tworzenia klastra lub puli węzłów.

Poniższy przykład tworzy klaster AKS o nazwie *myAKSCluster* w grupie zasobów o nazwie Moja *resourceName*. Łącznie *3* węzły są tworzone — jeden Agent w strefie *1*, jeden w *2*, a następnie jeden w *3*.

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

Przy wyborze strefy, do której powinien się znajdować nowy węzeł, dana Pula węzłów AKS będzie korzystać z [optymalnego równoważenia strefy oferowanego przez bazowe Virtual Machine Scale Sets platformy Azure][vmss-zone-balancing]. Dana Pula węzłów AKS jest traktowana jako "Zrównoważona", jeśli każda strefa ma taką samą liczbę maszyn wirtualnych lub \- maszynę wirtualną "+ 1" we wszystkich innych strefach zestawu skalowania.

## <a name="verify-node-distribution-across-zones"></a>Weryfikowanie dystrybucji węzłów między strefami

Gdy klaster jest gotowy, Utwórz listę węzłów agenta w zestawie skalowania, aby zobaczyć, w jakiej strefie dostępności są one wdrażane.

Najpierw pobierz poświadczenia klastra AKS za pomocą polecenia [AZ AKS Get-Credentials][az-aks-get-credentials] :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Następnie użyj polecenia [polecenia kubectl opisywania][kubectl-describe] , aby wyświetlić listę węzłów w klastrze i odfiltrować wartość *Failure-Domain.beta.Kubernetes.IO/Zone* . Poniższy przykład dotyczy powłoki bash.

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

Poniższe przykładowe dane wyjściowe przedstawiają trzy węzły rozproszone w określonym regionie i strefach dostępności, takie jak *eastus2-1* dla pierwszej strefy dostępności i *eastus2-2* dla drugiej strefy dostępności:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

Podczas dodawania kolejnych węzłów do puli agentów platforma Azure automatycznie dystrybuuje bazowe maszyny wirtualne w określonych strefach dostępności.

Należy pamiętać, że w nowszych wersjach programu Kubernetes (1.17.0 i nowsze), AKS używa nowszej etykiety `topology.kubernetes.io/zone` oprócz przestarzałej `failure-domain.beta.kubernetes.io/zone` . Aby uzyskać ten sam wynik, jak powyżej, można uruchomić następujący skrypt:

```console
kubectl get nodes -o custom-columns=NAME:'{.metadata.name}',REGION:'{.metadata.labels.topology\.kubernetes\.io/region}',ZONE:'{metadata.labels.topology\.kubernetes\.io/zone}'
```

Zapewnia to bardziej zwięzłe dane wyjściowe:

```console
NAME                                REGION   ZONE
aks-nodepool1-34917322-vmss000000   eastus   eastus-1
aks-nodepool1-34917322-vmss000001   eastus   eastus-2
aks-nodepool1-34917322-vmss000002   eastus   eastus-3
```

## <a name="verify-pod-distribution-across-zones"></a>Weryfikacja pod kątem dystrybucji między strefami

Zgodnie z opisem w [dobrze znanych etykietach, adnotacjach i][kubectl-well_known_labels]zastosowaniach, Kubernetes używa `failure-domain.beta.kubernetes.io/zone` etykiety do automatycznego dystrybucji zasobników w kontrolerze replikacji lub w usłudze w różnych dostępnych strefach. W celu przetestowania można skalować klaster z 3 do 5 węzłów, aby sprawdzić poprawność rozpraszania:

```azurecli-interactive
az aks scale \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 5
```

Gdy operacja skalowania kończy się po kilku minutach, polecenie `kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"` w powłoce bash powinno dać dane wyjściowe podobne do tego przykładu:

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

Mamy teraz dwa dodatkowe węzły w strefach 1 i 2. Można wdrożyć aplikację składającą się z trzech replik. Będziemy używać NGINX jako przykładu:

```console
kubectl create deployment nginx --image=nginx
kubectl scale deployment nginx --replicas=3
```

Oglądając węzły, w których są uruchomione Twoje zasobniki, zobaczysz, że na węzłach odpowiadających trzema różnymi strefami dostępności są uruchomione na podst. Na przykład przy użyciu polecenia `kubectl describe pod | grep -e "^Name:" -e "^Node:"` w powłoce bash można uzyskać dane wyjściowe podobne do następujących:

```console
Name:         nginx-6db489d4b7-ktdwg
Node:         aks-nodepool1-28993262-vmss000000/10.240.0.4
Name:         nginx-6db489d4b7-v7zvj
Node:         aks-nodepool1-28993262-vmss000002/10.240.0.6
Name:         nginx-6db489d4b7-xz6wj
Node:         aks-nodepool1-28993262-vmss000004/10.240.0.8
```

Jak widać na podstawie poprzednich danych wyjściowych, pierwszy pod z nich jest uruchamiany w węźle 0, który znajduje się w strefie dostępności `eastus2-1` . Drugi pod jest uruchomiony w węźle 2, który odnosi się do `eastus2-3` , i trzeci drugi w węźle 4, w `eastus2-2` . Bez dodatkowej konfiguracji program Kubernetes poprawna rozłożeniem tego samego zasobnika we wszystkich trzech strefach dostępności.

## <a name="next-steps"></a>Następne kroki

W tym artykule szczegółowo opisano sposób tworzenia klastra AKS używającego stref dostępności. Aby uzyskać więcej informacji na temat klastrów o wysokiej dostępności, zobacz [najlepsze rozwiązania dotyczące ciągłości działania i odzyskiwania po awarii w programie AKS][best-practices-bc-dr].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-overview]: ../availability-zones/az-overview.md
[best-practices-bc-dr]: operator-best-practices-multi-region.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[standard-lb-limitations]: load-balancer-standard.md#limitations
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[vmss-zone-balancing]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing

<!-- LINKS - external -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-well_known_labels]: https://kubernetes.io/docs/reference/labels-annotations-taints/
