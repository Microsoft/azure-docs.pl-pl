---
title: Używanie grup umieszczania w sąsiedztwie w celu zmniejszenia opóźnień dla klastrów usługi Azure Kubernetes Service (AKS)
description: Dowiedz się, jak za pomocą grup umieszczania zbliżeniowe ograniczyć opóźnienia dla obciążeń klastra AKS.
services: container-service
manager: gwallace
ms.topic: article
ms.date: 06/22/2020
ms.openlocfilehash: f5b87d048127bf856a5a683397540769a7040fd6
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85323444"
---
# <a name="reduce-latency-with-proximity-placement-groups-preview"></a>Zmniejszanie opóźnień przy użyciu grup umieszczania w sąsiedztwie

> [!Note]
> W przypadku używania grup umieszczania bliskości z AKS, współlokalizacja dotyczy tylko węzłów agenta. Ulepszono węzeł z węzłem i odpowiadający mu oczekiwany czas oczekiwania. Współlokalizacja nie ma wpływu na rozmieszczenie płaszczyzny kontroli klastra.

Podczas wdrażania aplikacji na platformie Azure rozproszenie wystąpień maszyn wirtualnych (VM) między regionami lub strefami dostępności tworzy opóźnienie sieci, co może mieć wpływ na ogólną wydajność aplikacji. Grupa umieszczania bliskości jest grupą logiczną używaną do upewnienia się, że zasoby obliczeniowe platformy Azure znajdują się fizycznie blisko siebie. Niektóre aplikacje, takie jak gry, symulacje inżynieryjne i handel o wysokiej częstotliwości (HFT), wymagają krótkich opóźnień i zadań, które są szybko kompletne. W przypadku scenariuszy obliczeniowych o wysokiej wydajności (HPC), takich jak te, należy rozważyć użycie [grup umieszczania zbliżeniowe](https://docs.microsoft.com/azure/virtual-machines/linux/co-location#proximity-placement-groups) dla pul węzłów klastra.

## <a name="limitations"></a>Ograniczenia

* Grupa umieszczania bliskości obejmuje pojedynczą strefę dostępności.
* Nie ma bieżącej obsługi klastrów AKS korzystających z zestawów dostępności maszyn wirtualnych.
* Nie można modyfikować istniejących pul węzłów, aby używać grupy umieszczania w sąsiedztwie.

> [!IMPORTANT]
> Funkcje w wersji zapoznawczej AKS są dostępne w ramach samoobsługowego i samodzielnego wyboru. Wersje zapoznawcze są udostępniane w postaci "AS-IS" i "jako dostępne" i są wykluczone z umów dotyczących poziomu usług i ograniczonej rękojmi. Wersje zapoznawcze AKS są częściowo objęte obsługą klienta w oparciu o optymalny sposób. W związku z tym te funkcje nie są przeznaczone do użytku produkcyjnego. Aby uzyskać więcej informacji, zobacz następujące artykuły pomocy technicznej:
>
> - [Zasady pomocy technicznej AKS](support-policies.md)
> - [Pomoc techniczna platformy Azure — często zadawane pytania](faq.md)

## <a name="before-you-begin"></a>Przed rozpoczęciem

Wymagane są następujące zasoby:

- Rozszerzenie AKS-Preview 0.4.53

### <a name="set-up-the-preview-feature-for-proximity-placement-groups"></a>Skonfiguruj funkcję w wersji zapoznawczej dla grup umieszczania w sąsiedztwie

> [!IMPORTANT]
> W przypadku używania grup umieszczania bliskości z AKS, współlokalizacja dotyczy tylko węzłów agenta. Ulepszono węzeł z węzłem i odpowiadający mu oczekiwany czas oczekiwania. Współlokalizacja nie ma wpływu na rozmieszczenie płaszczyzny kontroli klastra.

```azurecli-interactive
# register the preview feature
az feature register --namespace "Microsoft.ContainerService" --name "ProximityPlacementGroupPreview"
```

Rejestracja może potrwać kilka minut. Użyj poniższego polecenia, aby sprawdzić, czy funkcja jest zarejestrowana:

```azurecli-interactive
# Verify the feature is registered:
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/ProximityPlacementGroupPreview')].{Name:name,State:properties.state}"
```

W trakcie korzystania z wersji zapoznawczej potrzebne jest rozszerzenie interfejsu wiersza polecenia *AKS-Preview* do używania grup umieszczania sąsiedztwa. Użyj polecenia [AZ Extension Add][az-extension-add] , a następnie sprawdź, czy są dostępne aktualizacje za pomocą polecenia [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```
## <a name="node-pools-and-proximity-placement-groups"></a>Pule węzłów i grupy umieszczania sąsiedztwa

Pierwszy zasób wdrażany wraz z grupą umieszczania w sąsiedztwie dołącza do określonego centrum danych. Dodatkowe zasoby wdrożone z tą samą grupą położenia sąsiedztwa znajdują się w tym samym centrum danych. Po zatrzymaniu lub usunięciu wszystkich zasobów przy użyciu grupy umieszczania w sąsiedztwie nie jest już ona dołączona.

* Wiele pul węzłów może być skojarzonych z pojedynczą grupą położenia sąsiedztwa.
* Pula węzłów może być skojarzona tylko z pojedynczą grupą położenia sąsiedztwa.

## <a name="create-a-new-aks-cluster-with-a-proximity-placement-group"></a>Tworzenie nowego klastra AKS z grupą położenia zbliżeniowe

W poniższym przykładzie za pomocą polecenia [AZ Group Create][az-group-create] można utworzyć grupę zasobów o nazwie Moja *zasobów* w regionie *centralnym* . Klaster AKS o nazwie *myAKSCluster* jest tworzony przy użyciu polecenia [AZ AKS Create][az-aks-create] . 

Przyspieszona sieć znacznie poprawia wydajność sieci maszyn wirtualnych. Najlepiej używać grup umieszczania sąsiedztwa w połączeniu z przyspieszoną siecią. Domyślnie AKS korzysta z przyspieszonej sieci na [obsługiwanych wystąpieniach maszyn wirtualnych](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli?toc=/azure/virtual-machines/linux/toc.json#limitations-and-constraints), które obejmują większość maszyn wirtualnych platformy Azure z co najmniej dwoma procesorów wirtualnych vCPU.

Utwórz nowy klaster AKS z grupą położenia zbliżeniowe:

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
# Create an AKS cluster that uses a proximity placement group for the initial node pool
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
[proximity-placement-groups]: /virtual-machines/windows/co-location.md#proximity-placement-groups/virtual-machines/linux/co-location
[az-aks-create]: /cli/azure/aks#az-aks-create
[system-pool]: ./use-system-pools.md
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete

