---
title: Skalowanie klastra usługi Azure Kubernetes Service (AKS)
description: Dowiedz się, jak skalować liczbę węzłów w klastrze usługi Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 09/16/2020
ms.openlocfilehash: fdb61bf090351894329c24eb1a3c73d627e622e8
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102173770"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>Skalowanie liczby węzłów w klastrze usługi Azure Kubernetes Service 

W przypadku zmiany zasobów aplikacji można ręcznie skalować klaster AKS, aby uruchamiał inną liczbę węzłów. Po skalowaniu w dół węzły są dokładnie [odizolowywane i opróżniane][kubernetes-drain] , aby zminimalizować zakłócenia w działaniu aplikacji. Podczas skalowania w górę AKS czeka, aż węzły zostaną oznaczone `Ready` przez klaster Kubernetes przed zaplanowaniem zasobników.

## <a name="scale-the-cluster-nodes"></a>Skalowanie węzłów klastra

Najpierw pobierz *nazwę* puli węzłów za pomocą polecenia [AZ AKS show][az-aks-show] . Poniższy przykład pobiera nazwę puli węzłów dla klastra o nazwie *myAKSCluster* w *grupie zasobów zasobu zasobu:*

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

Następujące przykładowe dane wyjściowe pokazują, że *Nazwa* jest *nodepool1*:

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

Aby skalować węzły klastra, użyj polecenia [AZ AKS Scale][az-aks-scale] . Poniższy przykład skaluje klaster o nazwie *myAKSCluster* do jednego węzła. Podaj własne `--nodepool-name` polecenie, takie jak *nodepool1*:

```azurecli-interactive
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 1 --nodepool-name <your node pool name>
```

Następujące przykładowe dane wyjściowe pokazują, że klaster został pomyślnie przeskalowany do jednego węzła, jak pokazano w sekcji *agentPoolProfiles* :

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


## <a name="scale-user-node-pools-to-0"></a>Skalowanie `User` pul węzłów do wartości 0

W przeciwieństwie do `System` pul węzłów, które zawsze wymagają uruchomionych węzłów, `User` Pule węzłów umożliwiają skalowanie do 0. Aby dowiedzieć się więcej na temat różnic między pulami węzłów systemu i użytkownika, zobacz [Pule węzłów systemu i użytkownika](use-system-pools.md).

Aby skalować pulę użytkowników do wartości 0, możesz użyć opcji [AZ AKS nodepool Scale][az-aks-nodepool-scale] zamiast powyższego `az aks scale` polecenia i ustawić 0 jako liczbę węzłów.


```azurecli-interactive
az aks nodepool scale --name <your node pool name> --cluster-name myAKSCluster --resource-group myResourceGroup  --node-count 0 
```

Można również automatycznie skalować `User` Pule węzłów do 0 węzłów, ustawiając `--min-count` parametr [automatycznego skalowania klastra](cluster-autoscaler.md) na 0.

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano ręczne skalowanie klastra AKS w celu zwiększenia lub zmniejszenia liczby węzłów. Możesz również użyć automatycznego [skalowania klastra][cluster-autoscaler] , aby automatycznie skalować klaster.

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool#az-aks-nodepool-scale