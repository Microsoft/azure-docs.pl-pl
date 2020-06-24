---
title: Korzystanie z pul węzłów systemu w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak tworzyć pule węzłów systemu i zarządzać nimi w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 06/18/2020
ms.author: mlearned
ms.openlocfilehash: 9b6270f81e7af8bd508d29510698e6cf9a5a2010
ms.sourcegitcommit: ff19f4ecaff33a414c0fa2d4c92542d6e91332f8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2020
ms.locfileid: "85052656"
---
# <a name="manage-system-node-pools-in-azure-kubernetes-service-aks"></a>Zarządzanie pulami węzłów systemowych w usłudze Azure Kubernetes Service (AKS)

W usłudze Azure Kubernetes Service (AKS) węzły tej samej konfiguracji są pogrupowane w *Pule węzłów*. Pule węzłów zawierają bazowe maszyny wirtualne, na których działają aplikacje. Pule węzłów systemowych i pule węzłów użytkowników są dwoma różnymi trybami puli węzłów dla klastrów AKS. Pule węzłów systemu stanowią podstawowy cel hostingu krytycznych podstaw systemu, takich jak CoreDNS i tunnelfront. Pule węzłów użytkowników stanowią podstawowy cel hostingu podstaw aplikacji. Jednak w ramach klastra AKS można zaplanować pulę aplikacji w puli węzłów systemu. Każdy klaster AKS musi zawierać co najmniej jedną pulę węzłów systemu z co najmniej jednym węzłem.

> [!Important]
> W przypadku uruchamiania jednej puli węzłów systemu dla klastra AKS w środowisku produkcyjnym zaleca się użycie co najmniej trzech węzłów dla puli węzłów.

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Musisz mieć zainstalowany i skonfigurowany interfejs wiersza polecenia platformy Azure w wersji 2.3.1 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="limitations"></a>Ograniczenia

Podczas tworzenia klastrów AKS obsługujących pule węzłów systemu i zarządzania nimi obowiązują następujące ograniczenia.

* Zobacz [limity przydziałów, ograniczenia rozmiaru maszyny wirtualnej i dostępność regionów w usłudze Azure Kubernetes Service (AKS)][quotas-skus-regions].
* Klaster AKS musi być skompilowany przy użyciu zestawów skalowania maszyn wirtualnych jako typ maszyny wirtualnej.
* Nazwa puli węzłów może zawierać tylko małe znaki alfanumeryczne i musi zaczynać się małą literą. W przypadku pul węzłów systemu Linux długość musi należeć do zakresu od 1 do 12 znaków. W przypadku pul węzłów systemu Windows długość musi należeć do zakresu od 1 do 6 znaków.
* Aby ustawić tryb puli węzłów, należy użyć interfejsu API w wersji 2020-03-01 lub nowszej. Klastry utworzone w wersji interfejsu API starszej niż 2020-03-01 zawierają tylko pule węzłów użytkownika, ale można je migrować, aby zawierały pule węzłów systemowych, wykonując następujące [kroki w trybie puli aktualizacji](#update-existing-cluster-system-and-user-node-pools).
* Tryb puli węzłów jest wymaganą właściwością i musi być jawnie ustawiony podczas korzystania z szablonów ARM lub bezpośrednich wywołań interfejsu API.

## <a name="system-and-user-node-pools"></a>Pule węzłów systemu i użytkownika

Każdy węzeł puli węzłów systemu ma etykietę **Kubernetes.Azure.com/Mode: system**. Każdy klaster AKS zawiera co najmniej jedną pulę węzłów systemu. Pule węzłów systemowych mają następujące ograniczenia:

* OsType pul systemu musi być Linux.
* Pule węzłów użytkowników osType mogą mieć system Linux lub Windows.
* Pule systemu muszą zawierać co najmniej jeden węzeł, a pule węzłów użytkownika mogą zawierać zero lub więcej węzłów.
* Pule węzłów systemu wymagają jednostki SKU maszyny wirtualnej z co najmniej 2 procesorów wirtualnych vCPU i 4 GB pamięci.
* Pule węzłów systemowych muszą obsługiwać co najmniej 30 zasobników, zgodnie z opisem w [formule minimalnej i maksymalnej wartości dla zasobników][maximum-pods].
* Pule węzłów dodatkowych wymagają pul węzłów użytkownika.

W przypadku pul węzłów można wykonać następujące operacje:

* Zmień pulę węzłów systemu, aby była pulą węzłów użytkownika, pod warunkiem, że w klastrze AKS masz inną pulę węzłów systemu.
* Zmień pulę węzłów użytkownika na pulę węzłów systemu.
* Usuń pule węzłów użytkownika.
* Pule węzłów systemowych można usunąć, pod warunkiem, że w klastrze AKS istnieje inna Pula węzłów systemu.
* Klaster AKS może mieć wiele pul węzłów systemowych i wymaga co najmniej jednej puli węzłów systemu.
* Jeśli chcesz zmienić różne niezmienne ustawienia dla istniejących pul węzłów, możesz utworzyć nowe pule węzłów, aby je zastąpić. Jednym z przykładu jest dodanie nowej puli węzłów z nowym ustawieniem maxPods i usunięcie starej puli węzłów.

## <a name="create-a-new-aks-cluster-with-a-system-node-pool"></a>Tworzenie nowego klastra AKS z pulą węzłów systemowych

Podczas tworzenia nowego klastra AKS automatycznie tworzy się pulę węzłów systemowych z pojedynczym węzłem. Początkowa Pula węzłów domyślnie jest trybem typu System. Gdy tworzysz nowe pule węzłów za pomocą AZ AKS nodepool Add, te pule węzłów są pulami węzłów użytkownika, chyba że jawnie określisz parametr mode.

Poniższy przykład tworzy grupę zasobów o nazwie Moja *zasobów* w regionie *wschodnim* .

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Utwórz klaster AKS za pomocą polecenia [az aks create][az-aks-create]. W poniższym przykładzie tworzony jest klaster o nazwie *myAKSCluster* z jedną pulą systemową zawierającą jeden węzeł. W przypadku obciążeń produkcyjnych upewnij się, że używasz pul węzłów systemu z co najmniej trzema węzłami. Wykonanie tej operacji może potrwać kilka minut.

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

## <a name="add-a-system-node-pool-to-an-existing-aks-cluster"></a>Dodawanie puli węzłów systemowych do istniejącego klastra AKS

Do istniejących klastrów AKS można dodać co najmniej jedną pulę węzłów systemu. Następujące polecenie dodaje pulę węzłów systemu typu system z domyślną liczbą trzech węzłów.

```azurecli-interactive
az aks nodepool add -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```
## <a name="show-details-for-your-node-pool"></a>Pokaż szczegóły puli węzłów

Szczegóły puli węzłów można sprawdzić za pomocą następującego polecenia.  

```azurecli-interactive
az aks nodepool show -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

Tryb typu **system** jest zdefiniowany dla pul węzłów systemowych, a tryb typu **User** został zdefiniowany dla pul węzłów użytkownika.

```output
{
  "agentPoolType": "VirtualMachineScaleSets",
  "availabilityZones": null,
  "count": 3,
  "enableAutoScaling": null,
  "enableNodePublicIp": false,
  "id": "/subscriptions/666d66d8-1e43-4136-be25-f25bb5de5883/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/mynodepool",
  "maxCount": null,
  "maxPods": 110,
  "minCount": null,
  "mode": "System",
  "name": "mynodepool",
  "nodeLabels": {},
  "nodeTaints": null,
  "orchestratorVersion": "1.15.10",
  "osDiskSizeGb": 100,
  "osType": "Linux",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "scaleSetEvictionPolicy": null,
  "scaleSetPriority": null,
  "spotMaxPrice": null,
  "tags": null,
  "type": "Microsoft.ContainerService/managedClusters/agentPools",
  "vmSize": "Standard_DS2_v2",
  "vnetSubnetId": null
}
```

## <a name="update-existing-cluster-system-and-user-node-pools"></a>Aktualizowanie istniejącego systemu klastra i pul węzłów użytkownika

> [!NOTE]
> Aby ustawić tryb puli węzłów systemu, należy użyć interfejsu API w wersji 2020-03-01 lub nowszej. Klastry utworzone w wersji interfejsu API starszej niż 2020-03-01 zawierają tylko pule węzłów użytkownika. Aby otrzymywać funkcje puli węzłów systemu i korzyści ze starszych klastrów, zaktualizuj tryb istniejących pul węzłów za pomocą poniższych poleceń w najnowszej wersji interfejsu wiersza polecenia platformy Azure.

Można zmienić tryby dla pul węzłów systemu i użytkownika. Pulę węzłów systemowych można zmienić na pulę użytkowników tylko wtedy, gdy inna Pula węzłów systemu już istnieje w klastrze AKS.

To polecenie powoduje zmianę puli węzłów systemowych na pulę węzłów użytkownika.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode user
```

To polecenie powoduje zmianę puli węzłów użytkownika na pulę węzłów systemowych.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```

## <a name="delete-a-system-node-pool"></a>Usuwanie puli węzłów systemowych

> [!Note]
> Aby użyć pul węzłów systemowych w klastrach AKS przed interfejsem API w wersji 2020-03-02, Dodaj nową pulę węzłów systemu, a następnie usuń oryginalną domyślną pulę węzłów.

Wcześniej nie można było usunąć puli węzłów systemu, która była początkową domyślną pulą węzłów w klastrze AKS. Masz teraz elastyczność usuwania dowolnej puli węzłów z klastrów. Ponieważ klastry AKS wymagają co najmniej jednej puli węzłów systemu, przed usunięciem jednej z nich należy mieć co najmniej dwie pule węzłów systemu w klastrze AKS.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób tworzenia pul węzłów systemu i zarządzania nimi w klastrze AKS. Aby uzyskać więcej informacji na temat korzystania z wielu pul węzłów, zobacz [Korzystanie z wielu pul węzłów][use-multiple-node-pools].

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/resource-group-using-tags.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/linux/sizes.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[maximum-pods]: configure-azure-cni.md#maximum-pods-per-node