---
title: Skalowanie klastra usługi Azure Kubernetes Service (AKS)
description: Dowiedz się, jak skalować liczbę węzłów w klastrze Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 09/16/2020
ms.openlocfilehash: 1468f9a0a23935022ed14488dfb65d789828d310
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782891"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>Skalowanie liczby węzłów w klastrze usługi Azure Kubernetes Service 

Jeśli potrzeby aplikacji dotyczące zasobów zmienią się, można ręcznie skalować klaster usługi AKS w celu uruchomienia innej liczby węzłów. Podczas skalowania w dół węzły są dokładnie [odiębniane][kubernetes-drain] i opróżnione, aby zminimalizować zakłócenia dla działających aplikacji. Podczas skalowania w górę usługa AKS czeka, aż węzły zostaną oznaczone przez `Ready` klaster Kubernetes, zanim zostaną zaplanowane na nich zasobniki.

## <a name="scale-the-cluster-nodes"></a>Skalowanie węzłów klastra

Najpierw pobierz nazwę *puli węzłów* za pomocą [polecenia az aks show.][az-aks-show] Poniższy przykład pobiera nazwę puli węzłów dla klastra o nazwie *myAKSCluster* w grupie zasobów *myResourceGroup:*

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

Następujące przykładowe dane wyjściowe pokazują, że *nazwa to* *nodepool1:*

```output
[
  {
    "count": 1,
    "maxPods": 110,
    "name": "nodepool1",
    "osDiskSizeGb": 30,
    "osType": "Linux",
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_DS2_v2"
  }
]
```

Użyj polecenia [az aks scale,][az-aks-scale] aby skalować węzły klastra. Poniższy przykład skaluje klaster o *nazwie myAKSCluster* do jednego węzła. Podaj własne polecenie `--nodepool-name` z poprzedniego polecenia, takie jak *nodepool1*:

```azurecli-interactive
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 1 --nodepool-name <your node pool name>
```

Następujące przykładowe dane wyjściowe pokazują, że klaster został pomyślnie przeskalowany do jednego węzła, jak pokazano w sekcji *agentPoolProfiles:*

```json
{
  "aadProfile": null,
  "addonProfiles": null,
  "agentPoolProfiles": [
    {
      "count": 1,
      "maxPods": 110,
      "name": "nodepool1",
      "osDiskSizeGb": 30,
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS2_v2",
      "vnetSubnetId": null
    }
  ],
  [...]
}
```


## <a name="scale-user-node-pools-to-0"></a>Skalowanie `User` pul węzłów do 0

W `System` przeciwieństwie do pul węzłów, które zawsze wymagają uruchomionych węzłów, `User` pule węzłów umożliwiają skalowanie do 0. Aby dowiedzieć się więcej na temat różnic między pulami węzłów systemu i użytkownika, zobacz [Pule węzłów systemu i użytkownika](use-system-pools.md).

Aby skalować pulę użytkowników do 0, możesz użyć skalowania [az aks nodepool][az-aks-nodepool-scale] w sposób alternatywny do powyższego polecenia i ustawić 0 jako `az aks scale` liczbę węzłów.


```azurecli-interactive
az aks nodepool scale --name <your node pool name> --cluster-name myAKSCluster --resource-group myResourceGroup  --node-count 0 
```

Możesz również automatycznie skalować `User` pule węzłów do 0 węzłów, ustawiając parametr autoskalowania `--min-count` [klastra](cluster-autoscaler.md) na 0.

## <a name="next-steps"></a>Następne kroki

W tym artykule ręcznie przeskalowanie klastra usługi AKS w celu zwiększenia lub zmniejszenia liczby węzłów. Możesz również użyć funkcji [automatycznego skalowania klastra,][cluster-autoscaler] aby automatycznie skalować klaster.

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-aks-scale]: /cli/azure/aks#az_aks_scale
[cluster-autoscaler]: cluster-autoscaler.md
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool#az_aks_nodepool_scale