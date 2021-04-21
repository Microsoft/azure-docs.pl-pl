---
title: Samouczek dotyczący usługi Kubernetes na platformie Azure — wdrażanie klastra
description: W tym samouczku dotyczącym usługi Azure Kubernetes Service (AKS) utworzysz klaster usługi AKS i nawiążesz połączenie z węzłem głównym usługi Kubernetes za pomocą narzędzia kubectl.
services: container-service
ms.topic: tutorial
ms.date: 01/12/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: d7e931a55ec0a9d46a8b92d4353bd2de8edd8818
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777837"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Samouczek: wdrażanie klastra usługi Azure Kubernetes Service (AKS)

Usługa Kubernetes zapewnia rozproszoną platformę dla konteneryzowanych aplikacji. Za pomocą usługi AKS można szybko utworzyć klaster Kubernetes gotowy do użycia w środowisku produkcyjnym. W tym samouczku (część trzecia z siedmiu) w usłudze AKS jest wdrażany klaster Kubernetes. Omawiane kwestie:

> [!div class="checklist"]
> * Wdrażanie klastra Kubernetes AKS, który może uwierzytelniać się w rejestrze kontenerów platformy Azure
> * Instalowanie interfejsu wiersza polecenia rozwiązania Kubernetes (kubectl)
> * Konfigurowanie narzędzia kubectl w celu nawiązania połączenia z klastrem AKS

W kolejnych samouczkach aplikacja Azure Vote zostanie wdrożona w klastrze, przeskalowana i zaktualizowana.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W poprzednich samouczkach utworzono obraz kontenera i przekazano go do wystąpienia usługi Azure Container Registry. Jeśli nie wykonano tych kroków, a chcesz kontynuować pracę, zacznij od części [Samouczek 1 — tworzenie obrazów kontenera][aks-tutorial-prepare-app].

Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.53 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="create-a-kubernetes-cluster"></a>Tworzenie klastra Kubernetes

Klastry usługi AKS mogą używać kontroli dostępu opartej na rolach (RBAC) kubernetes. Te kontrolki umożliwiają zdefiniowanie dostępu do zasobów na podstawie ról przypisanych użytkownikom. Uprawnienia są łączone, jeśli użytkownikowi przypisano wiele ról, a zakres uprawnień można ograniczyć do jednej przestrzeni nazw lub do całego klastra. Domyślnie interfejs wiersza polecenia platformy Azure automatycznie włącza RBAC platformy Kubernetes podczas tworzenia klastra usługi AKS.

Utwórz klaster usługi AKS za pomocą polecenia [az aks create][]. W poniższym przykładzie tworzony jest klaster o nazwie *myAKSCluster* w grupie zasobów o nazwie *myResourceGroup*. Ta grupa zasobów została utworzona w poprzednim [samouczku][aks-tutorial-prepare-acr] w *regionie eastus.* W poniższym przykładzie nie określono regionu, dlatego klaster usługi AKS jest również tworzony w *regionie eastus.* Aby uzyskać więcej informacji, zobacz Limity przydziału, ograniczenia rozmiaru maszyny wirtualnej i dostępność regionów w umacie [Azure Kubernetes Service (AKS),][quotas-skus-regions] aby uzyskać więcej informacji na temat limitów zasobów i dostępności regionów dla usługi AKS.

Aby zezwolić klastrowi usługi AKS na interakcję z innymi zasobami platformy Azure, tożsamość klastra jest tworzona automatycznie, ponieważ jej nie określono. W tym miejscu ta tożsamość [klastra][container-registry-integration] ma prawo do ściągania obrazów z Azure Container Registry (ACR) utworzonego w poprzednim samouczku. Aby pomyślnie wykonać polecenie, musisz mieć rolę **właściciela** lub administratora konta platformy **Azure** w subskrypcji platformy Azure.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --generate-ssh-keys \
    --attach-acr <acrName>
```

Aby uniknąć konieczności stosowania **roli właściciela** lub administratora konta platformy **Azure,** można również ręcznie skonfigurować jednostkę usługi do ściągania obrazów z usługi ACR. Aby uzyskać więcej informacji, zobacz [Uwierzytelnianie usługi ACR](../container-registry/container-registry-auth-service-principal.md) za pomocą jednostki usługi lub Uwierzytelnianie z [usługi Kubernetes przy użyciu tajnego hasła ściągania.](../container-registry/container-registry-auth-kubernetes.md) Alternatywnie można użyć tożsamości [zarządzanej](use-managed-identity.md) zamiast jednostki usługi w celu łatwiejszego zarządzania.

Po kilku minutach wdrażanie zostanie zakończone i zwróci informacje o wdrożeniu usługi AKS w formacie JSON.

> [!NOTE]
> Aby zapewnić niezawodne działanie klastra, należy uruchomić co najmniej 2 (dwa) węzły.

## <a name="install-the-kubernetes-cli"></a>Instalowanie interfejsu wiersza polecenia rozwiązania Kubernetes

Aby nawiązać połączenie z klastrem Kubernetes z komputera lokalnego, należy użyć narzędzia [kubectl][kubectl], czyli klienta wiersza polecenia usługi Kubernetes.

Jeśli korzystasz z usługi Azure Cloud Shell, narzędzie `kubectl` jest już zainstalowane. Możesz także zainstalować je lokalnie za pomocą polecenia [az aks install-cli][]:

```azurecli
az aks install-cli
```

## <a name="connect-to-cluster-using-kubectl"></a>Nawiązywanie połączenia z klastrem przy użyciu narzędzia kubectl

Aby skonfigurować narzędzie `kubectl` w celu nawiązania połączenia z klastrem Kubernetes, użyj polecenia [az aks get-credentials][]. Poniższy przykład umożliwia pobranie poświadczeń dla nazwy klastra usługi AKS *myAKSCluster* w grupie zasobów *myResourceGroup*:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Aby sprawdzić połączenie z klastrem, uruchom polecenie [kubectl get nodes,][kubectl-get] aby zwrócić listę węzłów klastra:

```
$ kubectl get nodes

NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-37463671-vmss000000   Ready    agent   2m37s   v1.18.10
aks-nodepool1-37463671-vmss000001   Ready    agent   2m28s   v1.18.10
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku wdrożono klaster Kubernetes w usłudze AKS i skonfigurowano narzędzie `kubectl` w celu nawiązania z nim połączenia. W tym samouczku omówiono:

> [!div class="checklist"]
> * Wdrażanie klastra Kubernetes AKS, który może uwierzytelniać się w rejestrze kontenerów platformy Azure
> * Instalowanie interfejsu wiersza polecenia rozwiązania Kubernetes (kubectl)
> * Konfigurowanie narzędzia kubectl w celu nawiązania połączenia z klastrem AKS

Przejdź do następnego samouczka, aby dowiedzieć się, jak wdrożyć aplikację w klastrze.

> [!div class="nextstepaction"]
> [Wdrażanie aplikacji w rozwiązaniu Kubernetes][aks-tutorial-deploy-app]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az ad sp create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az acr show]: /cli/azure/acr#az_acr_show
[az role assignment create]: /cli/azure/role/assignment#az_role_assignment_create
[az aks create]: /cli/azure/aks#az_aks_create
[az aks install-cli]: /cli/azure/aks#az_aks_install_cli
[az aks get-credentials]: /cli/azure/aks#az_aks_get_credentials
[azure-cli-install]: /cli/azure/install-azure-cli
[container-registry-integration]: ./cluster-container-registry-integration.md
[quotas-skus-regions]: quotas-skus-regions.md
