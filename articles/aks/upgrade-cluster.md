---
title: Uaktualnianie klastra usługi Azure Kubernetes Service (AKS)
description: Dowiedz się, jak uaktualnić klaster usługi Azure Kubernetes Service (AKS), aby uzyskać najnowsze funkcje i aktualizacje zabezpieczeń.
services: container-service
ms.topic: article
ms.date: 11/17/2020
ms.openlocfilehash: 262905c9f840850795ba9555912e81eca61369d1
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94683237"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Uaktualnianie klastra usługi Azure Kubernetes Service (AKS)

W ramach cyklu życia klastra AKS często konieczne jest uaktualnienie do najnowszej wersji programu Kubernetes. Ważne jest, aby zastosować najnowsze wersje zabezpieczeń Kubernetes lub uaktualnić je w celu uzyskania najnowszych funkcji. W tym artykule opisano sposób uaktualniania składników głównych lub pojedynczej, domyślnej puli węzłów w klastrze AKS.

W przypadku klastrów AKS, które korzystają z wielu pul węzłów lub węzłów systemu Windows Server, zobacz [uaktualnianie puli węzłów w AKS][nodepool-upgrade].

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Ten artykuł wymaga uruchomienia interfejsu wiersza polecenia platformy Azure w wersji 2.0.65 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

> [!WARNING]
> Uaktualnienie klastra AKS wyzwala Cordon i opróżnia węzły. W przypadku braku dostępnego limitu przydziału obliczeń uaktualnienie może zakończyć się niepowodzeniem. Aby uzyskać więcej informacji, zobacz [zwiększenie limitów przydziału](../azure-portal/supportability/resource-manager-core-quotas-request.md) .

## <a name="check-for-available-aks-cluster-upgrades"></a>Sprawdź dostępność dostępnych uaktualnień klastrów AKS

Aby sprawdzić, które wersje Kubernetes są dostępne dla klastra, użyj polecenia [AZ AKS Get-Upgrades][az-aks-get-upgrades] . Poniższy przykład sprawdza dostępność dostępnych uaktualnień do klastra o nazwie *myAKSCluster* w grupie zasobów o nazwie Moja *resourceName*:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> W przypadku uaktualniania obsługiwanego klastra AKS nie można pominąć wersji pomocniczych Kubernetes. Na przykład uaktualnienia między *1.12. x*  ->  *1.13. x* lub *1.13. x*  ->  *1.14. x* są dozwolone, jednak *1.12. x*  ->  *1.14. x* nie jest.
>
> Aby przeprowadzić uaktualnienie, z wersji *1.12. x*  ->  *1.14. x*, najpierw Uaktualnij z wersji *1.12. x*  ->  *1.13. x*, a następnie Uaktualnij z *1.13. x*  ->  *1.14. x*.
>
> Pomijanie wielu wersji można wykonać tylko w przypadku uaktualniania z nieobsługiwanej wersji z powrotem do obsługiwanej wersji. Na przykład uaktualnienie z nieobsługiwanej wersji *1.10. x* --> można ukończyć obsługiwane *1.15. x* .

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

## <a name="customize-node-surge-upgrade"></a>Dostosowywanie przepięcia węzła

> [!Important]
> Przepięcia węzłów wymagają przydziału subskrypcji dla wymaganej maksymalnej liczby przeskoków dla każdej operacji uaktualniania. Na przykład klaster, który ma 5 pul węzłów, każdy z liczbą 4 węzłów, ma łącznie 20 węzłów. Jeśli każda pula węzłów ma maksymalną wartość przepięcia wynoszącą 50%, do ukończenia uaktualnienia jest wymagane dodatkowe zasoby obliczeniowe i IP z 10 węzłów (2 węzły * 5 pul).
>
> Jeśli korzystasz z usługi Azure CNI, sprawdź, czy w podsieci są dostępne adresy IP, a także [wymagania dotyczące usługi Azure CNI](configure-azure-cni.md).

Domyślnie AKS konfiguruje uaktualnienia, aby przepięcia z jednym dodatkowym węzłem. Wartość domyślna dla ustawienia Maksymalna przeskoku umożliwia AKS zminimalizowanie przerwy w obciążeniu przez utworzenie dodatkowego węzła przed Cordon/opróżnieniem istniejących aplikacji w celu zastąpienia starszego węzła. Maksymalna wartość przepięcia może być dostosowana dla puli węzłów w celu zapewnienia wymiany między szybkością uaktualniania a przerwaniem uaktualniania. Zwiększając maksymalną wartość przepięcia, proces uaktualniania kończy się szybciej, ale ustawienie dużej wartości maksymalnego przepięcia może spowodować zakłócenia w trakcie procesu uaktualniania. 

Na przykład maksymalna wartość przepięcia 100% zapewnia najszybszy możliwy proces uaktualniania (podwaja liczbę węzłów), ale również powoduje, że wszystkie węzły w puli węzłów są opróżniane jednocześnie. Możesz użyć wyższej wartości, na przykład w środowiskach testowych. W przypadku pul węzłów produkcyjnych zalecamy ustawienie max_surge 33%.

AKS akceptuje zarówno wartości całkowite, jak i wartość procentową maksymalnego przepięcia. Liczba całkowita, taka jak "5", wskazuje pięć dodatkowych węzłów do przepięcia. Wartość "50%" wskazuje wartość przepięcia połowy bieżącej liczby węzłów w puli. Maksymalne wartości procentowe przepięcia mogą być minimalne z 1% i maksymalnie 100%. Wartość procentowa jest zaokrąglana do najbliższej liczby węzłów. Jeśli maksymalna wartość przepięcia jest mniejsza niż bieżąca liczba węzłów w czasie uaktualniania, bieżąca liczba węzłów jest używana dla maksymalnej wartości przepięcia.

Podczas uaktualniania maksymalna wartość przepięcia może wynosić co najmniej 1, a maksymalna wartość równa liczbie węzłów w puli węzłów. Można ustawić większe wartości, ale Maksymalna liczba węzłów używanych do maksymalnego przepięcia nie będzie większa niż liczba węzłów w puli w czasie uaktualniania.

Do momentu uruchomienia interfejsu wiersza polecenia w wersji 2.16.0 +, aby użyć maksymalnego wzwyższego poziomu, należy mieć rozszerzenie CLI *AKS-Preview* . Użyj polecenia [AZ Extension Add][az-extension-add] , a następnie sprawdź, czy są dostępne aktualizacje za pomocą polecenia [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

> [!Important]
> Ustawienia maksymalnego przepięcia w puli węzłów są trwałe.  Kolejne uaktualnienia Kubernetes lub uaktualnienia wersji węzła będą używać tego ustawienia. W każdej chwili można zmienić maksymalną wartość skokową pul węzłów. W przypadku pul węzłów produkcyjnych zalecamy ustawienie maksymalnego przepięcia o 33%.

Użyj następujących poleceń, aby ustawić maksymalne wartości przepięcia dla nowych lub istniejących pul węzłów.

```azurecli-interactive
# Set max surge for a new node pool
az aks nodepool add -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 33%
```

```azurecli-interactive
# Update max surge for an existing node pool 
az aks nodepool update -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 5
```

## <a name="upgrade-an-aks-cluster"></a>Uaktualnianie klastra AKS

Mając listę dostępnych wersji klastra AKS, użyj polecenia [AZ AKS upgrade][az-aks-upgrade] , aby przeprowadzić uaktualnienie. W trakcie procesu uaktualniania AKS dodaje nowy węzeł buforu (lub tyle węzłów skonfigurowanych w [maksymalnym przeskoku](#customize-node-surge-upgrade)) do klastra, na którym działa określona wersja Kubernetes. Następnie [Cordon i opróżnienie][kubernetes-drain] jednego ze starych węzłów w celu zminimalizowania przerw w działaniu aplikacji (Jeśli używasz maksymalnego przepięcia, będzie [Cordon i opróżniać][kubernetes-drain] tyle węzłów w tym samym czasie co liczba określonych węzłów buforu). Gdy stary węzeł jest całkowicie opróżniany, zostanie odłączony do nowej wersji i będzie węzłem buforu dla następującego węzła do uaktualnienia. Ten proces jest powtarzany do momentu uaktualnienia wszystkich węzłów w klastrze. Po zakończeniu procesu ostatni opróżniany węzeł zostanie usunięty i będzie utrzymywać istniejącą liczbę węzłów agenta.

```azurecli-interactive
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version KUBERNETES_VERSION
```

Uaktualnienie klastra trwa kilka minut, w zależności od liczby posiadanych węzłów.

> [!NOTE]
> Istnieje łączny czas trwania uaktualniania klastra. Ten czas jest obliczany przez pobranie produktu z `10 minutes * total number of nodes in the cluster` . Na przykład w klastrze 20 węzłów operacje uaktualniania muszą się powieść w ciągu 200 minut, a operacja nie powiedzie się, aby uniknąć nieodwracalnego stanu klastra. Aby odzyskać sprawność po błędzie uaktualnienia, ponów próbę wykonania operacji uaktualniania po osiągnięciu limitu czasu.

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
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
