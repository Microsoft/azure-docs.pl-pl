---
title: (PRZESTARZAŁE) Samouczek usługi Azure Container Service — wdrażanie klastra
description: Samouczek usługi Azure Container Service — wdrażanie klastra
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 09/14/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: b8821f3bb3d48786697cbc4137baf530856774fd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78274010"
---
# <a name="deprecated-deploy-a-kubernetes-cluster-in-azure-container-service"></a>(PRZESTARZAŁE) Wdrażanie klastra Kubernetes w usłudze Azure Container Service

> [!TIP]
> Aby uzyskać zaktualizowaną wersję tego samouczka korzystającego z usługi Azure Kubernetes, zobacz [Samouczek: Wdrażanie klastra usługi Azure Kubernetes Service (AKS)](../../aks/tutorial-kubernetes-deploy-cluster.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Usługa Kubernetes zapewnia rozproszoną platformę dla konteneryzowanych aplikacji. Usługa Azure Container Service umożliwia łatwe i szybkie aprowizowanie produkcyjnego klastra Kubernetes. W tym samouczku (część trzecia z siedmiu) wdrażany jest klaster Kubernetes usługi Azure Container Service. Wykonano następujące czynności:

> [!div class="checklist"]
> * Wdrażanie klastra Kubernetes ACS
> * Instalacja interfejsu wiersza polecenia rozwiązania Kubernetes (kubectl)
> * Konfiguracja narzędzia kubectl

W kolejnych samouczkach aplikacja do głosowania platformy Azure będzie wdrażana w klastrze, skalowana i aktualizowana, a usługa Log Analytics zostanie skonfigurowana do monitorowania klastra usługi Kubernetes.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W poprzednich samouczkach utworzono obraz kontenera i przekazano go do wystąpienia usługi Azure Container Registry. Jeśli nie wykonano tych kroków, a chcesz kontynuować pracę, wróć do części [Samouczek 1 — tworzenie obrazów kontenera](./container-service-tutorial-kubernetes-prepare-app.md).

## <a name="create-kubernetes-cluster"></a>Tworzenie klastra Kubernetes

Utwórz klaster Kubernetes w usłudze Azure Container Service za pomocą polecenia [az acs create](/cli/azure/acs#az-acs-create). 

Poniższy przykład obejmuje tworzenie klastra o nazwie `myK8sCluster` w grupie zasobów o nazwie `myResourceGroup`. Ta grupa zasobów została utworzona w [poprzednim samouczku](./container-service-tutorial-kubernetes-prepare-acr.md).

```azurecli-interactive
az acs create --orchestrator-type kubernetes --resource-group myResourceGroup --name myK8SCluster --generate-ssh-keys 
```

W niektórych przypadkach, np. ograniczonej wersji próbnej, subskrypcja platformy Azure ma ograniczony dostęp do zasobów platformy Azure. Jeśli wdrożenie nie powiedzie się z powodu ograniczonej liczby dostępnych rdzeni, zmniejsz domyślną liczbę agentów, dodając `--agent-count 1` do polecenia [az acs create](/cli/azure/acs#az-acs-create). 

Po kilku minutach wdrażanie zostanie zakończone i zwróci informacje o wdrożeniu usługi ACS w formacie JSON.

## <a name="install-the-kubectl-cli"></a>Instalowanie interfejsu wiersza polecenia kubectl

Aby nawiązać połączenie z klastrem Kubernetes z komputera klienckiego, należy użyć narzędzia [kubectl](https://kubernetes.io/docs/user-guide/kubectl/), czyli klienta wiersza polecenia usługi Kubernetes. 

Jeśli korzystasz z usługi Azure Cloud Shell, narzędzie kubectl jest już zainstalowane. Jeśli chcesz zainstalować je lokalnie, użyj polecenia [az acs kubernetes install-cli](/cli/azure/acs/kubernetes).

W przypadku systemu Linux lub macOS konieczne może być uruchomienie narzędzia przy użyciu programu sudo. W systemie Windows upewnij się, że powłoka została uruchomiona przy użyciu uprawnień administratora.

```azurecli-interactive
az acs kubernetes install-cli 
```

Instalacja domyślna w systemie Windows to *c:\program files (x86)\kubectl.exe*. Być może trzeba będzie dodać ten plik do ścieżki systemu Windows. 

## <a name="connect-with-kubectl"></a>Nawiązywanie połączenia przy użyciu narzędzia kubectl

Aby skonfigurować narzędzie kubectl w celu nawiązania połączenia z klastrem Kubernetes, uruchom polecenie [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes).

```azurecli-interactive
az acs kubernetes get-credentials --resource-group myResourceGroup --name myK8SCluster
```

Aby sprawdzić połączenie z klastrem, uruchom polecenie [kubectl get nodes](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get).

```console
kubectl get nodes
```

Dane wyjściowe:

```output
NAME                    STATUS                     AGE       VERSION
k8s-agent-98dc3136-0    Ready                      5m        v1.6.2
k8s-agent-98dc3136-1    Ready                      5m        v1.6.2
k8s-agent-98dc3136-2    Ready                      5m        v1.6.2
k8s-master-98dc3136-0   Ready,SchedulingDisabled   5m        v1.6.2
```

Po ukończeniu samouczka klaster ACS Kubernetes jest gotowy do użycia z obciążeniami. W kolejnych samouczkach aplikacja wielu kontenerów jest wdrażana do tego klastra, skalowana w poziomie, aktualizowana i monitorowana.

## <a name="next-steps"></a>Następne kroki

W tym samouczku wdrożono klaster Kubernetes usługi Azure Container Service. Wykonano następujące czynności:

> [!div class="checklist"]
> * Wdrażanie klastra Kubernetes ACS
> * Instalacja interfejsu wiersza polecenia rozwiązania Kubernetes (kubectl)
> * Konfigurowanie narzędzia kubectl

Przejdź do następnego samouczka, aby dowiedzieć się, jak uruchomić aplikację w klastrze.

> [!div class="nextstepaction"]
> [Wdrażanie aplikacji w rozwiązaniu Kubernetes](./container-service-tutorial-kubernetes-deploy-application.md)
