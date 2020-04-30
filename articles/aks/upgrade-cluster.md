---
title: Uaktualnianie klastra usługi Azure Kubernetes Service (AKS)
description: Dowiedz się, jak uaktualnić klaster usługi Azure Kubernetes Service (AKS), aby uzyskać najnowsze funkcje i aktualizacje zabezpieczeń.
services: container-service
ms.topic: article
ms.date: 05/31/2019
ms.openlocfilehash: 7e9a47b7bda4cdb0ff6f1983bc884f7441a26d9b
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82207976"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Uaktualnianie klastra usługi Azure Kubernetes Service (AKS)

W ramach cyklu życia klastra AKS często konieczne jest uaktualnienie do najnowszej wersji programu Kubernetes. Ważne jest, aby zastosować najnowsze wersje zabezpieczeń Kubernetes lub uaktualnić je w celu uzyskania najnowszych funkcji. W tym artykule opisano sposób uaktualniania składników głównych lub pojedynczej, domyślnej puli węzłów w klastrze AKS.

W przypadku klastrów AKS, które korzystają z wielu pul węzłów, zobacz [uaktualnianie puli węzłów w AKS][nodepool-upgrade].

## <a name="before-you-begin"></a>Przed rozpoczęciem

Ten artykuł wymaga uruchomienia interfejsu wiersza polecenia platformy Azure w wersji 2.0.65 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

> [!WARNING]
> Uaktualnienie klastra AKS wyzwala Cordon i opróżnia węzły. W przypadku braku dostępnego limitu przydziału obliczeń uaktualnienie może zakończyć się niepowodzeniem. Aby uzyskać więcej informacji, zobacz [zwiększenie limitów przydziału](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) .
> Jeśli używasz własnego wdrożenia automatycznego skalowania klastra, wyłącz je (można je skalować do replik zerowych) podczas uaktualniania, ponieważ będzie to miało wpływ na problem z procesem uaktualniania. Zarządzane automatyczne skalowanie jest automatycznie obsługiwane. 

## <a name="check-for-available-aks-cluster-upgrades"></a>Sprawdź dostępność dostępnych uaktualnień klastrów AKS

Aby sprawdzić, które wersje Kubernetes są dostępne dla klastra, użyj polecenia [AZ AKS Get-Upgrades][az-aks-get-upgrades] . Poniższy przykład sprawdza dostępność dostępnych uaktualnień do klastra o nazwie *myAKSCluster* w grupie zasobów o nazwie Moja *resourceName*:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> W przypadku uaktualniania klastra AKS wersje pomocnicze Kubernetes nie mogą być pomijane. Na przykład uaktualnienia między *1.12. x* -> *1.13. x* lub *1.13. x* -> *1.14. x* są dozwolone, jednak *1.12. x* -> *1.14. x* nie jest.
>
> Aby przeprowadzić uaktualnienie, z wersji *1.12. x* -> *1.14. x*, najpierw Uaktualnij z wersji *1.12. x* -> *1.13. x*, a następnie Uaktualnij z *1.13. x* -> *1.14. x*.

Następujące przykładowe dane wyjściowe pokazują, że klaster można uaktualnić do wersji *1.13.9* i *1.13.10*:

```console
Name     ResourceGroup     MasterVersion    NodePoolVersion    Upgrades
-------  ----------------  ---------------  -----------------  ---------------
default  myResourceGroup   1.12.8           1.12.8             1.13.9, 1.13.10
```
Jeśli uaktualnienie nie jest dostępne, uzyskasz następujące korzyści:
```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="upgrade-an-aks-cluster"></a>Uaktualnianie klastra AKS

Mając listę dostępnych wersji klastra AKS, użyj polecenia [AZ AKS upgrade][az-aks-upgrade] , aby przeprowadzić uaktualnienie. W trakcie procesu uaktualniania program AKS dodaje nowy węzeł do klastra, na którym działa określona wersja Kubernetes, a następnie uważnie [Cordon i opróżnia][kubernetes-drain] jeden ze starych węzłów w celu zminimalizowania przerw w działaniu aplikacji. Gdy nowy węzeł zostanie potwierdzony jako uruchomiony program ApplicationManager, stary węzeł zostanie usunięty. Ten proces jest powtarzany do momentu uaktualnienia wszystkich węzłów w klastrze.

Poniższy przykład uaktualnia klaster do wersji *1.13.10*:

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.13.10
```

Uaktualnienie klastra trwa kilka minut, w zależności od liczby posiadanych węzłów. 

> [!NOTE]
> Istnieje łączny czas trwania uaktualniania klastra. Ten czas jest obliczany przez pobranie produktu z `10 minutes * total number of nodes in the cluster`. Na przykład w klastrze 20 węzłów operacje uaktualniania muszą się powieść w ciągu 200 minut, a operacja nie powiedzie się, aby uniknąć nieodwracalnego stanu klastra. Aby odzyskać sprawność po błędzie uaktualnienia, ponów próbę wykonania operacji uaktualniania po osiągnięciu limitu czasu.

Aby upewnić się, że uaktualnienie zakończyło się pomyślnie, użyj polecenia [AZ AKS show][az-aks-show] :

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

Następujące przykładowe dane wyjściowe pokazują, że klaster działa teraz *1.13.10*:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.13.10               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Następne kroki

W tym artykule pokazano, jak uaktualnić istniejący klaster AKS. Aby dowiedzieć się więcej o wdrażaniu klastrów AKS i zarządzaniu nimi, zobacz zestaw samouczków.

> [!div class="nextstepaction"]
> [Samouczki AKS][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
