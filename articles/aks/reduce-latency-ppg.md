---
title: Używanie grup umieszczania w pobliżu w celu zmniejszenia opóźnienia Azure Kubernetes Service (AKS)
description: Dowiedz się, jak za pomocą grup umieszczania w pobliżu zmniejszyć opóźnienie obciążeń klastra usługi AKS.
services: container-service
manager: gwallace
ms.topic: article
ms.date: 10/19/2020
ms.openlocfilehash: fbcff37185b2cba71c405e917653d52397479007
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779601"
---
# <a name="reduce-latency-with-proximity-placement-groups"></a>Zmniejszanie opóźnień za pomocą grup umieszczania w pobliżu

> [!Note]
> W przypadku korzystania z grup umieszczania w pobliżu w użytce AKS kolokacja ma zastosowanie tylko do węzłów agenta. Ulepszono węzeł-węzeł i poprawiono opóźnienie odpowiedniego zasobnika hostowanych zasobników. Kolokacja nie wpływa na rozmieszczenie płaszczyzny sterowania klastra.

Podczas wdrażania aplikacji na platformie Azure rozłożenie wystąpień maszyn wirtualnych między regionami lub strefami dostępności powoduje opóźnienie sieci, co może mieć wpływ na ogólną wydajność aplikacji. Grupa umieszczania w pobliżu to logiczne grupowanie, które służy do upewniania się, że zasoby obliczeniowe platformy Azure znajdują się fizycznie blisko siebie. Niektóre aplikacje, takie jak gry, symulacje inżynieryjne i transakcje o wysokiej częstotliwości (HFT), wymagają małych opóźnień i zadań, które są wykonywane szybko. W przypadku takich scenariuszy obliczeń o wysokiej wydajności [](../virtual-machines/co-location.md#proximity-placement-groups) (HPC) rozważ użycie grup umieszczania w pobliżu (PPG) dla pul węzłów klastra.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.14 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

### <a name="limitations"></a>Ograniczenia

* Grupa umieszczania w pobliżu może być mapowana na co najwyżej jedną strefę dostępności.
* Pula węzłów musi używać Virtual Machine Scale Sets do skojarzenia grupy umieszczania w pobliżu.
* Pula węzłów może skojarzyć grupę umieszczania w pobliżu tylko w czasie tworzenia puli węzłów.

## <a name="node-pools-and-proximity-placement-groups"></a>Pule węzłów i grupy umieszczania w pobliżu

Pierwszy zasób wdrażany z grupą umieszczania w pobliżu jest dołączany do określonego centrum danych. Dodatkowe zasoby wdrożone z taką samą grupą umieszczania w pobliżu znajdują się w tym samym centrum danych. Gdy wszystkie zasoby korzystające z grupy umieszczania w pobliżu zostaną zatrzymane (cofniesz ich alokację) lub usunięte, nie zostaną już dołączone.

* Wiele pul węzłów można skojarzyć z pojedynczą grupą umieszczania w pobliżu.
* Pula węzłów może być skojarzona tylko z jedną grupą umieszczania w pobliżu.

### <a name="configure-proximity-placement-groups-with-availability-zones"></a>Konfigurowanie grup umieszczania w pobliżu przy użyciu stref dostępności

> [!NOTE]
> Grupy umieszczania w pobliżu wymagają, aby pula węzłów używać co najwyżej jednej strefy dostępności, jednak podstawowa umowa SLA maszyny wirtualnej platformy Azure o wartości [99,9%](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) nadal obowiązywała dla maszyn wirtualnych w jednej strefie.

Grupy umieszczania w pobliżu to pojęcie puli węzłów skojarzone z poszczególnymi pulami węzłów. Korzystanie z zasobu PPG nie ma wpływu na dostępność płaszczyzny sterowania usługi AKS. Może to mieć wpływ na sposób, w jaki klaster powinien zostać zaprojektowany ze strefami. Aby zapewnić rozrzut klastra w wielu strefach, zaleca się następujący projekt.

* Aprowizowanie klastra z pierwszą pulą systemową przy użyciu 3 stref bez skojarzonej grupy umieszczania w pobliżu. Dzięki temu zasobniki systemowe trafiają do dedykowanej puli węzłów, która będzie rozrzucana w wielu strefach.
* Dodaj dodatkowe pule węzłów użytkownika z unikatową strefą i grupą umieszczania w pobliżu skojarzoną z każdą pulą. Przykładem jest nodepool1 w strefie 1 i PPG1, nodepool2 w strefie 2 i PPG2, nodepool3 w strefie 3 z PPG3. Dzięki temu na poziomie klastra węzły są rozłożone w wielu strefach, a każda pojedyncza pula węzłów jest kolokowana w wyznaczonej strefie z dedykowanym zasobem PPG.

## <a name="create-a-new-aks-cluster-with-a-proximity-placement-group"></a>Tworzenie nowego klastra usługi AKS z grupą umieszczania w pobliżu

W poniższym przykładzie użyto [polecenia az group create,][az-group-create] aby utworzyć grupę zasobów o nazwie *myResourceGroup* w *regionie centralus.* Klaster usługi AKS o *nazwie myAKSCluster* jest następnie tworzony przy użyciu [polecenia az aks create.][az-aks-create]

Przyspieszona sieć znacznie poprawia wydajność sieci maszyn wirtualnych. Najlepiej jest używać grup umieszczania w pobliżu w połączeniu z przyspieszoną siecią. Domyślnie usługa AKS używa przyspieszonej sieci na obsługiwanych wystąpieniach [maszyn](../virtual-network/create-vm-accelerated-networking-cli.md?toc=/azure/virtual-machines/linux/toc.json#limitations-and-constraints)wirtualnych, które obejmują większość maszyn wirtualnych platformy Azure z co najmniej dwoma procesorami wirtualnymi.

Utwórz nowy klaster usługi AKS z grupą umieszczania w pobliżu skojarzoną z pierwszą pulą węzłów systemowych:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```
Uruchom następujące polecenie i przechowaj zwrócony identyfikator:

```azurecli-interactive
# Create proximity placement group
az ppg create -n myPPG -g myResourceGroup -l centralus -t standard
```

Polecenie generuje dane wyjściowe, które zawierają wartość *identyfikatora* potrzebną do przyszłych poleceń interfejsu wiersza polecenia:

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

Użyj identyfikatora zasobu grupy umieszczania w pobliżu dla *wartości myPPGResourceID* w poniższym poleceniu:

```azurecli-interactive
# Create an AKS cluster that uses a proximity placement group for the initial system node pool only. The PPG has no effect on the cluster control plane.
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --ppg myPPGResourceID
```

## <a name="add-a-proximity-placement-group-to-an-existing-cluster"></a>Dodawanie grupy umieszczania w pobliżu do istniejącego klastra

Grupę umieszczania w pobliżu można dodać do istniejącego klastra, tworząc nową pulę węzłów. Następnie możesz opcjonalnie przeprowadzić migrację istniejących obciążeń do nowej puli węzłów, a następnie usunąć oryginalną pulę węzłów.

Użyj utworzonej wcześniej grupy umieszczania w pobliżu, co zapewni, że węzły agentów w obu pulach węzłów w klastrze usługi AKS znajdują się fizycznie w tym samym centrum danych.

Użyj identyfikatora zasobu z utworzonej wcześniej grupy umieszczania w pobliżu i dodaj nową pulę węzłów za pomocą [`az aks nodepool add`][az-aks-nodepool-add] polecenia :

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

Aby usunąć klaster, użyj [`az group delete`][az-group-delete] polecenia , aby usunąć grupę zasobów usługi AKS:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [grupach umieszczania w pobliżu.][proximity-placement-groups]

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az_aks_get_upgrades
[az-aks-upgrade]: /cli/azure/aks#az_aks_upgrade
[az-aks-show]: /cli/azure/aks#az_aks_show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[proximity-placement-groups]: ../virtual-machines/co-location.md#proximity-placement-groups
[az-aks-create]: /cli/azure/aks#az_aks_create
[system-pool]: ./use-system-pools.md
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az_aks_nodepool_add
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete