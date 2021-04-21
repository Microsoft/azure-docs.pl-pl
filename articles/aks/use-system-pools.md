---
title: Używanie pul węzłów systemowych w Azure Kubernetes Service (AKS)
description: Dowiedz się, jak tworzyć pule węzłów systemu i zarządzać nimi w Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 06/18/2020
ms.author: mlearned
ms.custom: fasttrack-edit, devx-track-azurecli
ms.openlocfilehash: 8b41b43c70f72ab327de2f1d59415cc1f49e5a5b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767407"
---
# <a name="manage-system-node-pools-in-azure-kubernetes-service-aks"></a>Zarządzanie pulami węzłów systemowych w Azure Kubernetes Service (AKS)

W Azure Kubernetes Service (AKS) węzły tej samej konfiguracji są grupowane w *pule węzłów.* Pule węzłów zawierają bazowe maszyny wirtualne, na których są uruchamiane aplikacje. Pule węzłów systemowych i pule węzłów użytkownika to dwa różne tryby puli węzłów dla klastrów usługi AKS. Pule węzłów systemowych służą do hostowania krytycznych zasobników systemowych, takich `CoreDNS` jak i `metrics-server` . Pule węzłów użytkownika służą do hostowania zasobników aplikacji. Zasobniki aplikacji można jednak zaplanować w pulach węzłów systemowych, jeśli w klastrze usługi AKS ma być tylko jedna pula. Każdy klaster usługi AKS musi zawierać co najmniej jedną pulę węzłów systemu z co najmniej jednym węzłem.

> [!Important]
> Jeśli uruchamiasz pojedynczą pulę węzłów systemowych dla klastra usługi AKS w środowisku produkcyjnym, zalecamy użycie co najmniej trzech węzłów dla puli węzłów.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

* Musisz mieć zainstalowany i skonfigurowany interfejs wiersza polecenia platformy Azure w wersji 2.3.1 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="limitations"></a>Ograniczenia

Poniższe ograniczenia mają zastosowanie podczas tworzenia klastrów usługi AKS, które obsługują pule węzłów systemowych, i zarządzania nimi.

* Zobacz [Limity przydziału, ograniczenia rozmiaru maszyny wirtualnej i dostępność regionów w Azure Kubernetes Service (AKS).][quotas-skus-regions]
* Klaster usługi AKS musi być zbudowany przy użyciu zestawów skalowania maszyn wirtualnych jako typu maszyny wirtualnej i usługi *load* balancer w standardowych sku.
* Nazwa puli węzłów może zawierać tylko małe litery alfanumeryczne i musi zaczynać się małą literą. W przypadku pul węzłów systemu Linux długość musi zawierać od 1 do 12 znaków. W przypadku pul węzłów systemu Windows długość musi zawierać od 1 do 6 znaków.
* Aby ustawić tryb puli węzłów, należy użyć interfejsu API w wersji 2020-03-01 lub większej. Klastry utworzone w wersjach interfejsu API starszych niż 2020-03-01 zawierają tylko pule węzłów użytkownika, ale można je migrować w celu zawierania pul węzłów systemowych, korzystając z kroków trybu puli [aktualizacji](#update-existing-cluster-system-and-user-node-pools).
* Tryb puli węzłów jest wymaganą właściwością i musi być jawnie ustawiony podczas korzystania z szablonów usługi ARM lub bezpośrednich wywołań interfejsu API.

## <a name="system-and-user-node-pools"></a>Pule węzłów systemu i użytkownika

W przypadku puli węzłów systemu usługi AKS automatycznie przypisują etykietę **kubernetes.azure.com/mode: system** do swoich węzłów. Powoduje to, że usługę AKS preferuje planowanie zasobników systemowych w pulach węzłów zawierających tę etykietę. Ta etykieta nie uniemożliwia planowania zasobników aplikacji w pulach węzłów systemowych. Zalecamy jednak odizolowanie krytycznych zasobników systemowych od zasobników aplikacji, aby zapobiec przypadkowemu zaśmieceniu zasobników systemowych przez nieprawidłowo skonfigurowane lub nieautoryzowane zasobniki aplikacji. To zachowanie można wymusić, tworząc dedykowaną pulę węzłów systemu. Użyj tej `CriticalAddonsOnly=true:NoSchedule` funkcji, aby uniemożliwić zaplanowanie zasobników aplikacji w pulach węzłów systemowych.

Pule węzłów systemowych mają następujące ograniczenia:

* Typ osType pul systemowych musi być w systemie Linux.
* Typ osType pul węzłów użytkownika może być w systemie Linux lub Windows.
* Pule systemowe muszą zawierać co najmniej jeden węzeł, a pule węzłów użytkownika mogą zawierać zero lub więcej węzłów.
* Pule węzłów systemu wymagają co najmniej 2 procesorów wirtualnych i 4 GB pamięci dla maszyny wirtualnej. Jednak maszyna wirtualna z serii B z serii burstable nie jest zalecana.
* Zalecane są co najmniej dwa węzły — 4 procesory wirtualne (np. Standard_DS4_v2), szczególnie w przypadku dużych klastrów (replik z wieloma zasobnikami CoreDNS, 3–4 lub więcej dodatków itp.).
* Pule węzłów systemowych muszą obsługiwać co najmniej 30 zasobników zgodnie z opisem formuły wartości minimalnej i maksymalnej [dla zasobników][maximum-pods].
* Pule węzłów typu spot wymagają pul węzłów użytkownika.
* Dodanie dodatkowej puli węzłów systemowych lub zmiana puli węzłów jako puli węzłów systemowych *NIE* spowoduje automatycznego przeniesienia zasobników systemowych. Zasobniki systemowe mogą nadal działać w tej samej puli węzłów, nawet jeśli zmienisz ją na pulę węzłów użytkownika. W przypadku usunięcia lub skalowania w dół puli węzłów z uruchomionymi zasobnikami systemu, które były wcześniej pulą węzłów systemu, te zasobniki systemowe zostaną ponownie uruchomione z preferowanym harmonogramem do nowej puli węzłów systemu.

Za pomocą pul węzłów można wykonać następujące operacje:

* Utwórz dedykowaną pulę węzłów systemu (preferowanie planowania zasobników systemowych na pule węzłów `mode:system` )
* Zmień pulę węzłów systemu na pulę węzłów użytkownika, o ile w klastrze usługi AKS znajduje się inna pula węzłów systemowych.
* Zmień pulę węzłów użytkownika na pulę węzłów systemu.
* Usuń pule węzłów użytkownika.
* Pule węzłów systemowych można usunąć, o ile w klastrze usługi AKS znajduje się inna pula węzłów systemowych.
* Klaster usługi AKS może mieć wiele pul węzłów systemowych i wymaga co najmniej jednej puli węzłów systemu.
* Jeśli chcesz zmienić różne niezmienne ustawienia w istniejących pulach węzłów, możesz utworzyć nowe pule węzłów, aby je zastąpić. Jednym z przykładów jest dodanie nowej puli węzłów z nowym ustawieniem maxPods i usunięcie starej puli węzłów.

## <a name="create-a-new-aks-cluster-with-a-system-node-pool"></a>Tworzenie nowego klastra usługi AKS z pulą węzłów systemu

Podczas tworzenia nowego klastra usługi AKS automatycznie tworzymy pulę węzłów systemu z jednym węzłem. Początkowa pula węzłów domyślnie jest w trybie systemu typu. Podczas tworzenia nowych pul węzłów za pomocą funkcji te pule węzłów są pulami węzłów użytkownika, chyba że `az aks nodepool add` jawnie określisz parametr trybu.

Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w *regionie eastus.*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Utwórz klaster AKS za pomocą polecenia [az aks create][az-aks-create]. Poniższy przykład tworzy klaster o nazwie *myAKSCluster* z jedną dedykowaną pulą systemu zawierającą jeden węzeł. W przypadku obciążeń produkcyjnych upewnij się, że używasz pul węzłów systemowych z co najmniej trzema węzłami. Ukończenie tej operacji może potrwać kilka minut.

```azurecli-interactive
# Create a new AKS cluster with a single system pool
az aks create -g myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

## <a name="add-a-dedicated-system-node-pool-to-an-existing-aks-cluster"></a>Dodawanie dedykowanej puli węzłów systemu do istniejącego klastra usługi AKS

> [!Important]
> Po utworzeniu puli węzłów nie można zmienić węzłów za pomocą interfejsu wiersza polecenia.

Do istniejących klastrów usługi AKS można dodać co najmniej jedną pulę węzłów systemowych. Zaleca się zaplanowanie zasobników aplikacji w pulach węzłów użytkownika i przeznaczynie pul węzłów systemowych tylko do krytycznych zasobników systemowych. Zapobiega to przypadkowemu zabicia zasobników systemowych przez nieautoryzowane zasobniki aplikacji. Wymusz to zachowanie za `CriticalAddonsOnly=true:NoSchedule` [pomocą awarii][aks-taints] pul węzłów systemu. 

Następujące polecenie dodaje dedykowaną pulę węzłów systemu typów trybu z domyślną licznikiem trzech węzłów.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name systempool \
    --node-count 3 \
    --node-taints CriticalAddonsOnly=true:NoSchedule \
    --mode System
```
## <a name="show-details-for-your-node-pool"></a>Wyświetlanie szczegółów puli węzłów

Szczegóły puli węzłów można sprawdzić za pomocą następującego polecenia.  

```azurecli-interactive
az aks nodepool show -g myResourceGroup --cluster-name myAKSCluster -n systempool
```

Tryb typu System jest **definiowany** dla pul węzłów systemowych, a tryb typu **Użytkownik** jest definiowany dla pul węzłów użytkownika. W przypadku puli systemowej sprawdź, czy ma ustawioną wartość , co uniemożliwi zaplanowanie zasobników aplikacji w `CriticalAddonsOnly=true:NoSchedule` tej puli węzłów.

```output
{
  "agentPoolType": "VirtualMachineScaleSets",
  "availabilityZones": null,
  "count": 1,
  "enableAutoScaling": null,
  "enableNodePublicIp": false,
  "id": "/subscriptions/yourSubscriptionId/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/systempool",
  "maxCount": null,
  "maxPods": 110,
  "minCount": null,
  "mode": "System",
  "name": "systempool",
  "nodeImageVersion": "AKSUbuntu-1604-2020.06.30",
  "nodeLabels": {},
  "nodeTaints": [
    "CriticalAddonsOnly=true:NoSchedule"
  ],
  "orchestratorVersion": "1.16.10",
  "osDiskSizeGb": 128,
  "osType": "Linux",
  "provisioningState": "Failed",
  "proximityPlacementGroupId": null,
  "resourceGroup": "myResourceGroup",
  "scaleSetEvictionPolicy": null,
  "scaleSetPriority": null,
  "spotMaxPrice": null,
  "tags": null,
  "type": "Microsoft.ContainerService/managedClusters/agentPools",
  "upgradeSettings": {
    "maxSurge": null
  },
  "vmSize": "Standard_DS2_v2",
  "vnetSubnetId": null
}
```

## <a name="update-existing-cluster-system-and-user-node-pools"></a>Aktualizowanie istniejącego systemu klastra i pul węzłów użytkownika

> [!NOTE]
> Do ustawienia trybu puli węzłów systemu należy użyć interfejsu API w wersji 2020-03-01 lub większej. Klastry utworzone w wersjach interfejsu API starszych niż 2020-03-01 zawierają w wyniku tylko pule węzłów użytkownika. Aby uzyskać funkcje i korzyści puli węzłów systemu w starszych klastrach, zaktualizuj tryb istniejących pul węzłów przy użyciu następujących poleceń w najnowszej wersji interfejsu wiersza polecenia platformy Azure.

Tryby można zmieniać dla pul węzłów systemu i użytkownika. Pulę węzłów systemu można zmienić na pulę użytkowników tylko wtedy, gdy inna pula węzłów systemowych już istnieje w klastrze usługi AKS.

To polecenie zmienia pulę węzłów systemu na pulę węzłów użytkownika.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode user
```

To polecenie zmienia pulę węzłów użytkownika na pulę węzłów systemu.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```

## <a name="delete-a-system-node-pool"></a>Usuwanie puli węzłów systemu

> [!Note]
> Aby używać pul węzłów systemowych w klastrach usługi AKS przed wersją interfejsu API 2020-03-02, dodaj nową pulę węzłów systemowych, a następnie usuń oryginalną domyślną pulę węzłów.

Aby można było usunąć jedną z nich, musisz mieć co najmniej dwie pule węzłów systemowych w klastrze usługi AKS.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby usunąć klaster, użyj [polecenia az group delete,][az-group-delete] aby usunąć grupę zasobów usługi AKS:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```



## <a name="next-steps"></a>Następne kroki

W tym artykule opisano sposób tworzenia pul węzłów systemowych i zarządzania nimi w klastrze usługi AKS. Aby uzyskać więcej informacji na temat używania wielu pul węzłów, zobacz [Use multiple node pools (Używanie wielu pul węzłów).][use-multiple-node-pools]

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-taints]: use-multiple-node-pools.md#setting-nodepool-taints
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az_aks_nodepool_add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool#az_aks_nodepool_list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool#az_aks_nodepool_update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool#az_aks_nodepool_upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool#az_aks_nodepool_scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool#az_aks_nodepool_delete
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[az-group-deployment-create]: /cli/azure/group/deployment#az_group_deployment_create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/management/tag-resources.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/sizes.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[maximum-pods]: configure-azure-cni.md#maximum-pods-per-node
