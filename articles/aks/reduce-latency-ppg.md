---
title: Używanie grup umieszczania w sąsiedztwie w celu zmniejszenia opóźnień dla klastrów usługi Azure Kubernetes Service (AKS)
description: Dowiedz się, jak za pomocą grup umieszczania zbliżeniowe ograniczyć opóźnienia dla obciążeń klastra AKS.
services: container-service
manager: gwallace
ms.topic: article
ms.date: 10/19/2020
ms.openlocfilehash: c0c1bf83bf5a816debe61cab0ceab856bfbd062f
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181180"
---
# <a name="reduce-latency-with-proximity-placement-groups"></a>Zmniejszanie opóźnień przy użyciu grup umieszczania w sąsiedztwie

> [!Note]
> W przypadku korzystania z grup umieszczania zbliżeniowe w AKS, współlokalizacja dotyczy tylko węzłów agenta. Ulepszono węzeł z węzłem i odpowiadający mu oczekiwany czas oczekiwania. Współlokalizacja nie ma wpływu na rozmieszczenie płaszczyzny kontroli klastra.

Podczas wdrażania aplikacji na platformie Azure rozproszenie wystąpień maszyn wirtualnych (VM) między regionami lub strefami dostępności tworzy opóźnienie sieci, co może mieć wpływ na ogólną wydajność aplikacji. Grupa umieszczania bliskości jest grupą logiczną używaną do upewnienia się, że zasoby obliczeniowe platformy Azure znajdują się fizycznie blisko siebie. Niektóre aplikacje, takie jak gry, symulacje inżynieryjne i handel o wysokiej częstotliwości (HFT), wymagają krótkich opóźnień i zadań, które są szybko kompletne. W przypadku scenariuszy obliczeniowych o wysokiej wydajności (HPC), takich jak te, należy rozważyć użycie [grup umieszczania sąsiedztwa](../virtual-machines/co-location.md#proximity-placement-groups) (PPG) dla pul węzłów klastra.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Ten artykuł wymaga uruchomienia interfejsu wiersza polecenia platformy Azure w wersji 2,14 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

### <a name="limitations"></a>Ograniczenia

* Grupa położenia sąsiedztwa może mapować do najwyżej jednej strefy dostępności.
* Pula węzłów musi używać Virtual Machine Scale Sets, aby skojarzyć grupę umieszczania sąsiedztwa.
* Pula węzłów może skojarzyć grupę umieszczania bliskości w puli węzłów tylko do tworzenia czasu.

## <a name="node-pools-and-proximity-placement-groups"></a>Pule węzłów i grupy umieszczania sąsiedztwa

Pierwszy zasób wdrażany wraz z grupą umieszczania w sąsiedztwie dołącza do określonego centrum danych. Dodatkowe zasoby wdrożone z tą samą grupą położenia sąsiedztwa znajdują się w tym samym centrum danych. Po zatrzymaniu lub usunięciu wszystkich zasobów przy użyciu grupy umieszczania w sąsiedztwie nie jest już ona dołączona.

* Wiele pul węzłów może być skojarzonych z pojedynczą grupą położenia sąsiedztwa.
* Pula węzłów może być skojarzona tylko z pojedynczą grupą położenia sąsiedztwa.

### <a name="configure-proximity-placement-groups-with-availability-zones"></a>Konfigurowanie grup umieszczania zbliżeniowego ze strefami dostępności

> [!NOTE]
> Mimo że grupy umieszczania sąsiedztwa wymagają puli węzłów do korzystania z co najwyżej jednej strefy dostępności, [Umowa SLA maszyny wirtualnej platformy Azure w wysokości 99,9%](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) nadal obowiązuje dla maszyn wirtualnych w pojedynczej strefie.

Grupy umieszczania zbliżeniowe to koncepcja puli węzłów i skojarzona z każdą pulą poszczególnych węzłów. Korzystanie z zasobu PPG nie ma wpływu na dostępność płaszczyzny sterowania AKS. Może to mieć wpływ na sposób, w jaki klaster powinien być zaprojektowany ze strefami. Aby zapewnić rozproszenie klastra między wieloma strefami, zaleca się wykonanie następujących czynności.

* Ustanów klaster z pierwszą pulą systemu przy użyciu 3 stref i bez skojarzonej grupy umieszczania sąsiedztwa. Pozwala to zagwarantować, że system ma być używany w dedykowanej puli węzłów, która będzie rozłożona na wiele stref.
* Dodaj dodatkowe pule węzłów użytkowników z unikatową strefą i grupą umieszczania sąsiedztwa skojarzoną z każdą pulą. Przykładem jest nodepool1 w strefie 1 i PPG1, nodepool2 w strefie 2 i PPG2, nodepool3 w strefie 3 z PPG3. Dzięki temu na poziomie klastra węzły są rozproszone w wielu strefach, a każda z nich znajduje się w wyznaczonym obszarze przy użyciu dedykowanego zasobu PPG.

## <a name="create-a-new-aks-cluster-with-a-proximity-placement-group"></a>Tworzenie nowego klastra AKS z grupą położenia zbliżeniowe

W poniższym przykładzie za pomocą polecenia [AZ Group Create][az-group-create] można utworzyć grupę zasobów o nazwie Moja *zasobów* w regionie *centralnym* . Klaster AKS o nazwie *myAKSCluster* jest tworzony przy użyciu polecenia [AZ AKS Create][az-aks-create] .

Przyspieszona sieć znacznie poprawia wydajność sieci maszyn wirtualnych. Najlepiej używać grup umieszczania sąsiedztwa w połączeniu z przyspieszoną siecią. Domyślnie AKS korzysta z przyspieszonej sieci na [obsługiwanych wystąpieniach maszyn wirtualnych](../virtual-network/create-vm-accelerated-networking-cli.md?toc=/azure/virtual-machines/linux/toc.json#limitations-and-constraints), które obejmują większość maszyn wirtualnych platformy Azure z co najmniej dwoma procesorów wirtualnych vCPU.

Utwórz nowy klaster AKS z grupą położenia zbliżeniowe skojarzoną z pierwszą pulą węzłów systemu:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```
Uruchom następujące polecenie i Zapisz identyfikator, który jest zwracany:

```azurecli-interactive
# Create proximity placement group
az ppg create -n myPPG -g myResourceGroup -l centralus -t standard
```

Polecenie generuje dane wyjściowe, które obejmują wartość *identyfikatora* potrzebną dla nadchodzących poleceń interfejsu wiersza polecenia:

```output
{
  "availabilitySets": null,
  "colocationStatus": null,
  "id": "/subscriptions/yourSubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Compute/proximityPlacementGroups/myPPG",
  "location": "centralus",
  "name": "myPPG",
  "proximityPlacementGroupType": "Standard",
  "resourceGroup": "myResourceGroup",
  "tags": {},
  "type": "Microsoft.Compute/proximityPlacementGroups",
  "virtualMachineScaleSets": null,
  "virtualMachines": null
}
```

Użyj identyfikatora zasobu grupy położenia zbliżeniowe dla wartości *myPPGResourceID* w poniższym poleceniu:

```azurecli-interactive
# Create an AKS cluster that uses a proximity placement group for the initial system node pool only. The PPG has no effect on the cluster control plane.
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --ppg myPPGResourceID
```

## <a name="add-a-proximity-placement-group-to-an-existing-cluster"></a>Dodawanie grupy umieszczania zbliżeniowe do istniejącego klastra

Grupę umieszczania można dodać do istniejącego klastra, tworząc nową pulę węzłów. Następnie można opcjonalnie migrować istniejące obciążenia do nowej puli węzłów, a następnie usunąć pierwotną pulę węzłów.

Użyj tej samej grupy położenia zbliżeniowe, która została utworzona wcześniej. spowoduje to, że węzły agentów w klastrze AKS są fizycznie zlokalizowane w tym samym centrum danych.

Użyj identyfikatora zasobu z utworzonej wcześniej grupy położenia zbliżeniowe i Dodaj nową pulę węzłów za pomocą [`az aks nodepool add`][az-aks-nodepool-add] polecenia:

```azurecli-interactive
# Add a new node pool that uses a proximity placement group, use a --node-count = 1 for testing
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 1 \
    --ppg myPPGResourceID
```

## <a name="clean-up"></a>Czyszczenie

Aby usunąć klaster, użyj polecenia, [`az group delete`][az-group-delete] Aby usunąć grupę zasobów AKS:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat [grup umieszczania w sąsiedztwie][proximity-placement-groups].

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[proximity-placement-groups]: ../virtual-machines/co-location.md#proximity-placement-groups
[az-aks-create]: /cli/azure/aks#az-aks-create
[system-pool]: ./use-system-pools.md
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az-aks-nodepool-add
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete