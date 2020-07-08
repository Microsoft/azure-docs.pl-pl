---
title: Uaktualnij obrazy węzła usługi Azure Kubernetes Service (AKS)
description: Dowiedz się, jak uaktualnić obrazy w węzłach klastra AKS i w pulach węzłów.
author: laurenhughes
ms.author: lahugh
ms.service: container-service
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 20379f6a1e87c7553d6567be5b50f22bbadb8db7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84514687"
---
# <a name="preview---azure-kubernetes-service-aks-node-image-upgrades"></a>Wersja zapoznawcza — uaktualnienia obrazów węzła usługi Azure Kubernetes Service (AKS)

Program AKS obsługuje uaktualnianie obrazów w węźle, dzięki czemu jesteś aktualny z najnowszymi aktualizacjami systemu operacyjnego i środowiska uruchomieniowego. Program AKS udostępnia jeden nowy obraz tygodniowo z najnowszymi aktualizacjami, dlatego warto uaktualnić obrazy węzła regularnie w przypadku najnowszych funkcji, w tym dla systemu Linux lub Windows. W tym artykule pokazano, jak uaktualnić obrazy węzłów klastra AKS oraz jak aktualizować obrazy puli węzłów bez uaktualniania wersji programu Kubernetes.

Jeśli interesują Cię informacje o najnowszych obrazach dostarczonych przez AKS, zapoznaj się z informacjami o [wersji AKS](https://github.com/Azure/AKS/releases) , aby uzyskać więcej szczegółów.

Aby uzyskać informacje na temat uaktualniania wersji Kubernetes dla klastra, zobacz [Uaktualnianie klastra AKS][upgrade-cluster].

## <a name="register-the-node-image-upgrade-preview-feature"></a>Rejestrowanie funkcji wersji zapoznawczej obrazu węzła

Aby skorzystać z funkcji uaktualniania obrazu węzła w trakcie okresu zapoznawczego, należy zarejestrować tę funkcję.

```azurecli
# Register the preview feature
az feature register --namespace "Microsoft.ContainerService" --name "NodeImageUpgradePreview"
```

Rejestracja może potrwać kilka minut. Aby sprawdzić, czy funkcja jest zarejestrowana, użyj następującego polecenia:

```azurecli
# Verify the feature is registered:
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/NodeImageUpgradePreview')].{Name:name,State:properties.state}"
```

W trakcie korzystania z wersji zapoznawczej potrzebne jest rozszerzenie wiersza polecenia *AKS-Preview* , aby można było użyć uaktualnienia obrazu węzła. Użyj polecenia [AZ Extension Add] [AZ-Extension-Add], a następnie sprawdź, czy są dostępne aktualizacje za pomocą polecenia [AZ Extension Update] [AZ-Extension-Update]:

```azurecli
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

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
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade-preview
