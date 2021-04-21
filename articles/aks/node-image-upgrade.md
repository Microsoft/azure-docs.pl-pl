---
title: Uaktualnianie Azure Kubernetes Service węzłów (AKS)
description: Dowiedz się, jak uaktualnić obrazy w węzłach klastra i pulach węzłów usługi AKS.
ms.service: container-service
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: jpalma
ms.openlocfilehash: 4f6ac01c1d4df288c823142abbc93e981048d8db
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767533"
---
# <a name="azure-kubernetes-service-aks-node-image-upgrade"></a>Azure Kubernetes Service obrazu węzła (AKS)

AKS obsługuje uaktualnianie obrazów w węźle, aby być na bieżąco z najnowszymi aktualizacjami systemu operacyjnego i środowiska uruchomieniowego. Usługa AKS udostępnia jeden nowy obraz na tydzień z najnowszymi aktualizacjami, dlatego warto regularnie uaktualniać obrazy węzła w celu uwzględnienia najnowszych funkcji, w tym poprawek systemu Linux lub Windows. W tym artykule pokazano, jak uaktualnić obrazy węzłów klastra usługi AKS i jak zaktualizować obrazy puli węzłów bez uaktualniania wersji usługi Kubernetes.

Aby uzyskać więcej informacji na temat najnowszych obrazów dostarczanych przez usługę AKS, zobacz [informacje o wersji usługi AKS.](https://github.com/Azure/AKS/releases)

Aby uzyskać informacje na temat uaktualniania wersji usługi Kubernetes dla klastra, zobacz [Uaktualnianie klastra usługi AKS.][upgrade-cluster]

> [!NOTE]
> Klaster usługi AKS musi używać zestawów skalowania maszyn wirtualnych dla węzłów.

## <a name="check-if-your-node-pool-is-on-the-latest-node-image"></a>Sprawdź, czy pula węzłów znajduje się w najnowszym obrazie węzła

Możesz sprawdzić, jaka jest najnowsza wersja obrazu węzła dostępna dla puli węzłów, za pomocą następującego polecenia: 

```azurecli
az aks nodepool get-upgrades \
    --nodepool-name mynodepool \
    --cluster-name myAKSCluster \
    --resource-group myResourceGroup
```

W danych wyjściowych możesz zobaczyć, jak `latestNodeImageVersion` w poniższym przykładzie:

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

Dlatego najnowszy `nodepool1` dostępny obraz węzła to `AKSUbuntu-1604-2020.10.28` . Teraz możesz porównać go z bieżącą wersją obrazu węzła używaną przez pulę węzłów, uruchamiając:

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --query nodeImageVersion
```

Przykładowe dane wyjściowe to:

```output
"AKSUbuntu-1604-2020.10.08"
```

Dlatego w tym przykładzie można uaktualnić bieżącą wersję `AKSUbuntu-1604-2020.10.08` obrazu do najnowszej wersji `AKSUbuntu-1604-2020.10.28` . 

## <a name="upgrade-all-nodes-in-all-node-pools"></a>Uaktualnianie wszystkich węzłów we wszystkich pulach węzłów

Uaktualnianie obrazu węzła odbywa się za pomocą programu `az aks upgrade` . Aby uaktualnić obraz węzła, użyj następującego polecenia:

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-image-only
```

Podczas uaktualniania sprawdź stan obrazów węzłów za pomocą następującego polecenia, aby uzyskać etykiety i `kubectl` odfiltrować informacje o bieżącym obrazie węzła:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Po zakończeniu uaktualniania użyj programu , `az aks show` aby uzyskać zaktualizowane szczegóły puli węzłów. Bieżący obraz węzła jest wyświetlany we właściwości `nodeImageVersion` .

```azurecli
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster
```

## <a name="upgrade-a-specific-node-pool"></a>Uaktualnianie określonej puli węzłów

Uaktualnianie obrazu w puli węzłów jest podobne do uaktualniania obrazu w klastrze.

Aby zaktualizować obraz systemu operacyjnego puli węzłów bez uaktualniania klastra Kubernetes, użyj `--node-image-only` opcji w poniższym przykładzie:

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-image-only
```

Podczas uaktualniania sprawdź stan obrazów węzłów za pomocą następującego polecenia, aby uzyskać etykiety i `kubectl` odfiltrować bieżące informacje o obrazie węzła:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Po zakończeniu uaktualniania użyj programu , `az aks nodepool show` aby uzyskać zaktualizowane szczegóły puli węzłów. Bieżący obraz węzła jest wyświetlany we właściwości `nodeImageVersion` .

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="upgrade-node-images-with-node-surge"></a>Uaktualnianie obrazów węzłów przy użyciu skoków liczby węzłów

Aby przyspieszyć proces uaktualniania obrazu węzła, możesz uaktualnić obrazy węzłów przy użyciu dostosowywanej wartości skoków węzła. Domyślnie do konfigurowania uaktualnień usługi AKS jest używany jeden dodatkowy węzeł.

Jeśli chcesz zwiększyć szybkość uaktualnień, użyj wartości , aby skonfigurować liczbę węzłów do użycia na użytek uaktualnień, aby były `--max-surge` one szybsze. Aby dowiedzieć się więcej o kosztach różnych ustawień, zobacz Dostosowywanie uaktualnienia `--max-surge` [skoków węzłów.][max-surge]

Następujące polecenie ustawia maksymalną wartość skoków dla wykonywania uaktualnienia obrazu węzła:

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --max-surge 33% \
    --node-image-only \
    --no-wait
```

Podczas uaktualniania sprawdź stan obrazów węzłów za pomocą następującego polecenia, aby uzyskać etykiety i `kubectl` odfiltrować bieżące informacje o obrazie węzła:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Użyj `az aks nodepool show` , aby uzyskać zaktualizowane szczegóły puli węzłów. Bieżący obraz węzła jest wyświetlany we właściwości `nodeImageVersion` .

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="next-steps"></a>Następne kroki

- Zobacz informacje [o wersji AKS,](https://github.com/Azure/AKS/releases) aby uzyskać informacje o najnowszych obrazach węzłów.
- Dowiedz się, jak uaktualnić wersję usługi Kubernetes za [pomocą uaktualnienia klastra usługi AKS.][upgrade-cluster]
- [Automatyczne stosowanie uaktualnień klastra i puli węzłów przy użyciu GitHub Actions][github-schedule]
- Dowiedz się więcej na temat wielu pul węzłów i sposobu uaktualniania pul węzłów za pomocą [tematu Tworzenie wielu pul węzłów][use-multiple-node-pools]i zarządzanie nimi.

<!-- LINKS - internal -->
[upgrade-cluster]: upgrade-cluster.md
[github-schedule]: node-upgrade-github-actions.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
