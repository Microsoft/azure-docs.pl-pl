---
title: Integracja Azure Container Registry z Azure Kubernetes Service
description: Dowiedz się, jak zintegrować Azure Kubernetes Service (AKS) z Azure Container Registry (ACR)
services: container-service
manager: gwallace
ms.topic: article
ms.date: 01/08/2021
ms.openlocfilehash: ab8065a14aac9e798bfe7d632aa5b33c44706190
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775832"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Uwierzytelnianie za pomocą usługi Azure Container Registry z poziomu usługi Azure Kubernetes Service

Jeśli używasz usługi Azure Container Registry (ACR) z usługą Azure Kubernetes Service (AKS), należy ustalić mechanizm uwierzytelniania. Ta operacja jest implementowany jako część interfejsu wiersza polecenia i portalu przez przyznanie wymaganych uprawnień do usługi ACR. Ten artykuł zawiera przykłady konfigurowania uwierzytelniania między tymi dwiema usługami platformy Azure. 

Integrację usługi AKS z usługą ACR można skonfigurować za pomocą kilku prostych poleceń interfejsu wiersza polecenia platformy Azure. Ta integracja przypisuje rolę AcrPull do tożsamości zarządzanej skojarzonej z klastrem usługi AKS.

> [!NOTE]
> W tym artykule o mowa o automatycznym uwierzytelnianiu między AKS i ACR. Jeśli musisz ściągnąć obraz z prywatnego rejestru zewnętrznego, użyj klucza [tajnego ściągania obrazu][Image Pull Secret].

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Te przykłady wymagają:

* **Rola właściciela** **lub administratora konta platformy Azure** w subskrypcji platformy **Azure**
* Interfejs wiersza polecenia platformy Azure w wersji 2.7.0 lub nowszej

Aby uniknąć konieczności korzystania z **roli właściciela** lub administratora konta platformy **Azure,** możesz ręcznie skonfigurować tożsamość zarządzaną lub użyć istniejącej tożsamości zarządzanej do uwierzytelniania usługi ACR z usługi AKS. Aby uzyskać więcej informacji, zobacz [Używanie tożsamości zarządzanej platformy Azure do uwierzytelniania w rejestrze kontenerów platformy Azure.](../container-registry/container-registry-authentication-managed-identity.md)

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>Tworzenie nowego klastra usługi AKS z integracją usługi ACR

Integrację usług AKS i ACR można skonfigurować podczas początkowego tworzenia klastra usługi AKS.  Aby zezwolić klastrowi usługi AKS na interakcję z usługą ACR, Azure Active Directory **tożsamość zarządzana.** Następujące polecenie interfejsu wiersza polecenia umożliwia autoryzowanie istniejącej usługi ACR w subskrypcji i skonfigurowanie odpowiedniej roli **ACRPull** dla tożsamości zarządzanej. Poniżej należy podać prawidłowe wartości parametrów.

```azurecli
# set this to the name of your Azure Container Registry.  It must be globally unique
MYACR=myContainerRegistry

# Run the following line to create an Azure Container Registry if you do not already have one
az acr create -n $MYACR -g myContainerRegistryResourceGroup --sku basic

# Create an AKS cluster with ACR integration
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr $MYACR
```

Alternatywnie można określić nazwę usługi ACR przy użyciu identyfikatora zasobu usługi ACR, który ma następujący format:

`/subscriptions/\<subscription-id\>/resourceGroups/\<resource-group-name\>/providers/Microsoft.ContainerRegistry/registries/\<name\>`

> [!NOTE]
> Jeśli używasz usługi ACR, która znajduje się w innej subskrypcji niż klaster usługi AKS, użyj identyfikatora zasobu usługi ACR podczas dołączania lub odłączania klastra usługi AKS.

```azurecli
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr /subscriptions/<subscription-id>/resourceGroups/myContainerRegistryResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry
```

Ten krok może potrwać kilka minut.

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>Konfigurowanie integracji usługi ACR dla istniejących klastrów usługi AKS

Zintegruj istniejący identyfikator ACR z istniejącymi klastrami usługi AKS, podając prawidłowe wartości dla **nazwy acr-name** lub **acr-resource-id,** jak po przedstawiono poniżej.

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-name>
```

Lub

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

Możesz również usunąć integrację między usługą ACR i klastrem usługi AKS za pomocą następującego

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-name>
```

lub

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-resource-id>
```

## <a name="working-with-acr--aks"></a>Praca z ACR & AKS

### <a name="import-an-image-into-your-acr"></a>Importowanie obrazu docr

Zaimportuj obraz z usługi Docker Hub do usługi ACR, uruchamiając następujące elementy:


```azurecli
az acr import  -n <acr-name> --source docker.io/library/nginx:latest --image nginx:v1
```

### <a name="deploy-the-sample-image-from-acr-to-aks"></a>Wdrażanie przykładowego obrazu z usługi ACR do usługi AKS

Upewnij się, że masz odpowiednie poświadczenia usługi AKS

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

Utwórz plik o nazwie **acr-nginx.yaml,** który zawiera następujące elementy. Zastąp nazwę zasobu rejestru nazwą **acr-name**. Przykład: *myContainerRegistry*.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx0-deployment
  labels:
    app: nginx0-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx0
  template:
    metadata:
      labels:
        app: nginx0
    spec:
      containers:
      - name: nginx
        image: <acr-name>.azurecr.io/nginx:v1
        ports:
        - containerPort: 80
```

Następnie uruchom to wdrożenie w klastrze usługi AKS:

```console
kubectl apply -f acr-nginx.yaml
```

Wdrożenie można monitorować, uruchamiając:

```console
kubectl get pods
```

Powinny być uruchomione dwa zasobniki.

```output
NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

### <a name="troubleshooting"></a>Rozwiązywanie problemów
* Uruchom polecenie [az aks check-acr,](/cli/azure/aks#az_aks_check_acr) aby sprawdzić, czy rejestr jest dostępny z klastra usługi AKS.
* Dowiedz się więcej o [diagnostyce ACR](../container-registry/container-registry-diagnostics-audit-logs.md)
* Dowiedz się więcej na [temat kondycji ACR](../container-registry/container-registry-check-health.md)

<!-- LINKS - external -->
[AKS AKS CLI]: /cli/azure/aks#az_aks_create
[Image Pull secret]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/