---
title: Samouczek dotyczący rozwiązania Kubernetes na platformie Azure — uaktualnianie klastra
description: W tym samouczku dotyczącym usługi Azure Kubernetes Service (AKS) dowiesz się, jak uaktualnić istniejący klaster usługi AKS do najnowszej dostępnej wersji rozwiązania Kubernetes.
services: container-service
ms.topic: tutorial
ms.date: 02/25/2020
ms.custom: mvc
ms.openlocfilehash: 22aad0e601c600e582cbea0cea82dd67a20a2c06
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392672"
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>Samouczek: uaktualnianie rozwiązania Kubernetes w usłudze Azure Kubernetes Service (AKS)

W ramach cyklu życia aplikacji i klastra możesz chcieć wykonać uaktualnienie do najnowszej dostępnej wersji rozwiązania Kubernetes i skorzystać z nowych funkcji. Klaster usługi Azure Kubernetes Service (AKS) można uaktualnić za pomocą interfejsu wiersza polecenia platformy Azure.

W tym samouczku (część siódma z siedmiu) jest uaktualniany klaster Kubernetes. Omawiane kwestie:

> [!div class="checklist"]
> * Identyfikowanie bieżącej i dostępnych wersji rozwiązania Kubernetes
> * Uaktualnianie węzłów rozwiązania Kubernetes
> * Weryfikowanie pomyślnego uaktualnienia

## <a name="before-you-begin"></a>Przed rozpoczęciem

W poprzednich samouczkach aplikacja była spakowana do obrazu kontenera. Ten obraz został przekazany do usługi Azure Container Registry i utworzono klaster usługi AKS. Aplikacja została następnie wdrożona w klastrze usługi AKS. Jeśli nie wykonano tych kroków, a chcesz kontynuować pracę, wróć do części [Samouczek 1 — tworzenie obrazów kontenera][aks-tutorial-prepare-app].

Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.53 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="get-available-cluster-versions"></a>Pobieranie dostępnych wersji klastrów

Przed uaktualnieniem klastra użyj polecenia [az aks get-upgrades][], aby sprawdzić, która wersja rozwiązania Kubernetes jest dostępna na potrzeby uaktualnienia:

```azurecli
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

W poniższym przykładzie bieżąca wersja to *1.14.8,* a dostępne wersje są wyświetlane w kolumnie *Uaktualnienia.*

```
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  --------------
default  myResourceGroup  1.14.8           1.14.8             1.15.5, 1.15.7
```

## <a name="upgrade-a-cluster"></a>Uaktualnianie klastra

Aby zminimalizować zakłócenia dla działających aplikacji, węzły są dokładnie odizolowywane i opróżniane. W ramach tego procesu są wykonywane następujące kroki:

1. Harmonogram usługi Kubernetes zapobiega planowaniu dodatkowych zasobników w węźle, który ma zostać uaktualniony.
1. Zasobniki uruchomione w węźle są planowane w innych węzłach w klastrze.
1. Tworzony jest węzeł, który uruchamia najnowsze składniki platformy Kubernetes.
1. Gdy nowy węzeł jest gotowy i dołączony do klastra, harmonogram usługi Kubernetes rozpoczyna uruchamianie w nim zasobników.
1. Stary węzeł jest usuwany, a kolejny węzeł w klastrze rozpoczyna proces odizolowywania i opróżniania.

Użyj polecenia [az aks upgrade][], aby uaktualnić klaster usługi AKS. Poniższy przykład uaktualnia klaster do wersji *1.14.6.*

> [!NOTE]
> Jednocześnie można uaktualnić tylko jedną wersję pomocniczą. Można na przykład uaktualnić z *1.14.x* do *1.15.x*, ale nie można uaktualnić bezpośrednio z *1.14.x* do *1.16.x.* Aby uaktualnić z *1.14.x* do *1.16.x*, najpierw uaktualnić z *1.14.x* do *1.15.x*, a następnie wykonać kolejną aktualizację z *1.15.x* do *1.16.x*.

```azurecli
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.15.5
```

Następujące skondensowane przykładowe dane wyjściowe pokazują *kubernetesVersion* teraz raporty *1.15.5:*

```json
{
  "agentPoolProfiles": [
    {
      "count": 3,
      "maxPods": 110,
      "name": "nodepool1",
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS1_v2",
    }
  ],
  "dnsPrefix": "myAKSClust-myResourceGroup-19da35",
  "enableRbac": false,
  "fqdn": "myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io",
  "id": "/subscriptions/<Subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "kubernetesVersion": "1.15.5",
  "location": "eastus",
  "name": "myAKSCluster",
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="validate-an-upgrade"></a>Weryfikowanie uaktualnienia

Potwierdź, że uaktualnienie powiodło się, używając polecenia [az aks show][] w następujący sposób:

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

Poniższy przykładowy wynik przedstawia klaster AKS uruchamia *KubernetesVersion 1.15.5:*

```
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.15.5               Succeeded            myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io
```

## <a name="delete-the-cluster"></a>Usuwanie klastra

Ten samouczek jest ostatnią część serii, dlatego możesz usunąć klaster usługi AKS. Gdy węzły rozwiązania Kubernetes działają na maszynach wirtualnych platformy Azure, nadal są naliczane opłaty, nawet jeśli nie korzystasz z klastra. Usuń grupę zasobów, usługę kontenera i wszystkie powiązane zasoby za pomocą polecenia [az group delete][az-group-delete].

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Po usunięciu klastra jednostka usługi Azure Active Directory używana przez klaster usługi AKS nie jest usuwana. Aby sprawdzić, jak usunąć jednostkę usługi, zobacz [AKS service principal considerations and deletion (Uwagi dotyczące jednostki usługi AKS i jej usuwanie)][sp-delete]. Jeśli użyto tożsamości zarządzanej, tożsamość jest zarządzana przez platformę i nie wymaga aprowizowania ani obracania żadnych wpisów tajnych.

## <a name="next-steps"></a>Następne kroki

W tym samouczku uaktualniane jest rozwiązanie Kubernetes w klastrze AKS. W tym samouczku omówiono:

> [!div class="checklist"]
> * Identyfikowanie bieżącej i dostępnych wersji rozwiązania Kubernetes
> * Uaktualnianie węzłów rozwiązania Kubernetes
> * Weryfikowanie pomyślnego uaktualnienia

Skorzystaj z tego linku, aby dowiedzieć się więcej o usłudze AKS.

> [!div class="nextstepaction"]
> [Omówienie usługi AKS][aks-intro]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-intro]: ./intro-kubernetes.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az aks show]: /cli/azure/aks#az-aks-show
[az aks get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az aks upgrade]: /cli/azure/aks#az-aks-upgrade
[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-delete]: /cli/azure/group#az-group-delete
[sp-delete]: kubernetes-service-principal.md#additional-considerations
