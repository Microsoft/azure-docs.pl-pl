---
title: Uaktualnij obrazy węzła usługi Azure Kubernetes Service (AKS)
description: Dowiedz się, jak uaktualnić obrazy w węzłach klastra AKS i w pulach węzłów.
ms.service: container-service
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: jpalma
ms.openlocfilehash: e8214345bd1c328f0996f8aa8a2a8bb402a76e8d
ms.sourcegitcommit: ac7029597b54419ca13238f36f48c053a4492cb6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/29/2020
ms.locfileid: "96309600"
---
# <a name="azure-kubernetes-service-aks-node-image-upgrade"></a>Uaktualnianie obrazu węzła usługi Azure Kubernetes Service (AKS)

Program AKS obsługuje uaktualnianie obrazów w węźle, dzięki czemu jesteś aktualny z najnowszymi aktualizacjami systemu operacyjnego i środowiska uruchomieniowego. Program AKS udostępnia jeden nowy obraz tygodniowo z najnowszymi aktualizacjami, dlatego warto uaktualnić obrazy węzła regularnie w przypadku najnowszych funkcji, w tym dla systemu Linux lub Windows. W tym artykule pokazano, jak uaktualnić obrazy węzłów klastra AKS oraz jak aktualizować obrazy puli węzłów bez uaktualniania wersji programu Kubernetes.

Aby uzyskać więcej informacji na temat najnowszych obrazów dostarczonych przez AKS, zobacz [Informacje o wersji AKS](https://github.com/Azure/AKS/releases).

Aby uzyskać informacje na temat uaktualniania wersji Kubernetes dla klastra, zobacz [Uaktualnianie klastra AKS][upgrade-cluster].

> [!NOTE]
> Klaster AKS musi używać zestawów skalowania maszyn wirtualnych dla węzłów.

## <a name="check-if-your-node-pool-is-on-the-latest-node-image"></a>Sprawdź, czy pula węzłów znajduje się na najnowszym obrazie węzła

Możesz sprawdzić, jaka jest Najnowsza wersja obrazu węzła dostępna dla puli węzłów za pomocą następującego polecenia: 

```azurecli
az aks nodepool get-upgrades \
    --nodepool-name mynodepool \
    --cluster-name myAKSCluster \
    --resource-group myResourceGroup
```

W danych wyjściowych można zobaczyć, `latestNodeImageVersion` jak pokazano na poniższym przykładzie:

```output
{
  "id": "/subscriptions/XXXX-XXX-XXX-XXX-XXXXX/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/nodepool1/upgradeProfiles/default",
  "kubernetesVersion": "1.17.11",
  "latestNodeImageVersion": "AKSUbuntu-1604-2020.10.28",
  "name": "default",
  "osType": "Linux",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.ContainerService/managedClusters/agentPools/upgradeProfiles",
  "upgrades": null
}
```

W związku `nodepool1` z tym najnowszy obraz węzła jest dostępny `AKSUbuntu-1604-2020.10.28` . Teraz można ją porównać z bieżącą wersją obrazu węzła używaną przez pulę węzłów przez uruchomienie:

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --query nodeImageVersion
```

Przykładowe dane wyjściowe:

```output
"AKSUbuntu-1604-2020.10.08"
```

W tym przykładzie można przeprowadzić uaktualnienie z bieżącej `AKSUbuntu-1604-2020.10.08` wersji obrazu do najnowszej wersji `AKSUbuntu-1604-2020.10.28` . 

## <a name="upgrade-all-nodes-in-all-node-pools"></a>Uaktualnij wszystkie węzły we wszystkich pulach węzłów

Uaktualnianie obrazu węzła odbywa się przy użyciu `az aks upgrade` . Aby uaktualnić obraz węzła, użyj następującego polecenia:

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-image-only
```

Podczas uaktualniania sprawdź stan obrazów węzłów za pomocą następującego `kubectl` polecenia, aby uzyskać etykiety i odfiltrować informacje o bieżącym obrazie węzła:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Po zakończeniu uaktualniania Użyj `az aks show` programu, aby uzyskać informacje o zaktualizowanych puli węzłów. Bieżący obraz węzła jest pokazywany we `nodeImageVersion` właściwości.

```azurecli
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster
```

## <a name="upgrade-a-specific-node-pool"></a>Uaktualnianie określonej puli węzłów

Uaktualnianie obrazu w puli węzłów jest podobne do uaktualniania obrazu w klastrze.

Aby zaktualizować obraz systemu operacyjnego puli węzłów bez wykonywania uaktualnienia klastra Kubernetes, użyj `--node-image-only` opcji w następującym przykładzie:

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-image-only
```

Podczas uaktualniania sprawdź stan obrazów węzłów za pomocą następującego `kubectl` polecenia, aby uzyskać etykiety i odfiltrować informacje o bieżącym obrazie węzła:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Po zakończeniu uaktualniania Użyj `az aks nodepool show` programu, aby uzyskać informacje o zaktualizowanych puli węzłów. Bieżący obraz węzła jest pokazywany we `nodeImageVersion` właściwości.

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="upgrade-node-images-with-node-surge"></a>Uaktualnianie obrazów węzłów z przepięciem węzła

Aby przyspieszyć proces uaktualniania obrazu węzła, można uaktualnić obrazy węzłów przy użyciu dostosowywalnej wartości przepięcia węzła. Domyślnie AKS używa jednego dodatkowego węzła do konfigurowania uaktualnień.

Jeśli chcesz zwiększyć szybkość uaktualniania, użyj `--max-surge` wartości, aby skonfigurować liczbę węzłów, które mają być używane na potrzeby uaktualnień, aby zostały ukończone szybciej. Aby dowiedzieć się więcej o zaletach różnych `--max-surge` ustawień, zobacz [Dostosowywanie przepięcia węzła][max-surge].

Następujące polecenie ustawia maksymalną wartość przepięcia służącą do przeprowadzania uaktualnienia obrazu węzła:

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --max-surge 33% \
    --node-image-only \
    --no-wait
```

Podczas uaktualniania sprawdź stan obrazów węzłów za pomocą następującego `kubectl` polecenia, aby uzyskać etykiety i odfiltrować informacje o bieżącym obrazie węzła:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Użyj, `az aks nodepool show` Aby uzyskać zaktualizowane szczegóły puli węzłów. Bieżący obraz węzła jest pokazywany we `nodeImageVersion` właściwości.

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="next-steps"></a>Następne kroki

- Informacje o najnowszych obrazach węzłów można znaleźć w informacjach o [wersji AKS](https://github.com/Azure/AKS/releases) .
- Dowiedz się, jak uaktualnić wersję Kubernetes z [uaktualnieniem klastra AKS][upgrade-cluster].
- [Stosowanie aktualizacji zabezpieczeń i jądra do węzłów systemu Linux w usłudze Azure Kubernetes Service (AKS)][security-update]
- Dowiedz się więcej o wielu pulach węzłów oraz jak uaktualnić pule węzłów za pomocą [tworzenia wielu pul węzłów i zarządzania nimi][use-multiple-node-pools].

<!-- LINKS - internal -->
[upgrade-cluster]: upgrade-cluster.md
[security-update]: node-updates-kured.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
