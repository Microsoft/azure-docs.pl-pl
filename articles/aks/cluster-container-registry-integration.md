---
title: Integracja Azure Container Registry z usługą Azure Kubernetes Service
description: Dowiedz się, jak zintegrować usługę Azure Kubernetes Service (AKS) z usługą Azure Container Registry (ACR)
services: container-service
manager: gwallace
ms.topic: article
ms.date: 01/08/2021
ms.openlocfilehash: 19ece696dabc81e643e8a904d506d22e40eaa099
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102499156"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Uwierzytelnianie za pomocą usługi Azure Container Registry z poziomu usługi Azure Kubernetes Service

W przypadku korzystania z Azure Container Registry (ACR) z usługą Azure Kubernetes Service (AKS) należy nawiązać mechanizm uwierzytelniania. Ta operacja jest zaimplementowana w ramach interfejsu wiersza polecenia i środowiska portalu, przyznając wymagane uprawnienia do ACR. W tym artykule przedstawiono przykłady konfigurowania uwierzytelniania między tymi dwiema usługami platformy Azure. 

Za pomocą interfejsu wiersza polecenia platformy Azure można skonfigurować AKS do integracji z ACR w kilku prostych poleceniach. Ta integracja przypisuje rolę AcrPull do zarządzanej tożsamości skojarzonej z klastrem AKS.

> [!NOTE]
> W tym artykule opisano automatyczne uwierzytelnianie między AKS i ACR. Jeśli musisz ściągnąć obraz z prywatnego rejestru zewnętrznego, użyj [hasła ściągania obrazu][Image Pull Secret].

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Te przykłady wymagają:

* Rola **właściciela** lub **administratora konta platformy Azure** w **subskrypcji platformy Azure**
* Interfejs wiersza polecenia platformy Azure w wersji 2.7.0 lub nowszej

Aby uniknąć potrzeby roli **właściciela** lub **administratora konta platformy Azure** , można skonfigurować tożsamość zarządzaną ręcznie lub użyć istniejącej tożsamości zarządzanej do uwierzytelniania ACR z AKS. Aby uzyskać więcej informacji, zobacz [Korzystanie z tożsamości zarządzanej przez platformę Azure do uwierzytelniania w usłudze Azure Container Registry](../container-registry/container-registry-authentication-managed-identity.md).

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>Utwórz nowy klaster AKS z integracją ACR

Integrację AKS i ACR można skonfigurować podczas początkowego tworzenia klastra AKS.  Aby umożliwić klastrowi AKS współdziałanie z usługą ACR, używana jest **tożsamość zarządzana** Azure Active Directory. Następujące polecenie interfejsu wiersza polecenia pozwala autoryzować istniejące ACR w ramach subskrypcji i skonfigurować odpowiednią rolę **ACRPull** dla tożsamości zarządzanej. Podaj prawidłowe wartości parametrów poniżej.

```azurecli
# set this to the name of your Azure Container Registry.  It must be globally unique
MYACR=myContainerRegistry

# Run the following line to create an Azure Container Registry if you do not already have one
az acr create -n $MYACR -g myContainerRegistryResourceGroup --sku basic

# Create an AKS cluster with ACR integration
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr $MYACR
```

Alternatywnie można określić nazwę ACR przy użyciu identyfikatora zasobu ACR, który ma następujący format:

`/subscriptions/\<subscription-id\>/resourceGroups/\<resource-group-name\>/providers/Microsoft.ContainerRegistry/registries/\<name\>`

> [!NOTE]
> Jeśli używasz ACR, który znajduje się w innej subskrypcji z klastra AKS, użyj identyfikatora zasobu ACR podczas dołączania lub odłączania z klastra AKS.

```azurecli
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr /subscriptions/<subscription-id>/resourceGroups/myContainerRegistryResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry
```

Wykonanie tego kroku może potrwać kilka minut.

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>Konfigurowanie integracji usługi ACR dla istniejących klastrów AKS

Zintegruj istniejący ACR z istniejącymi klastrami AKS, dostarczając prawidłowe wartości dla **ACR-Name** lub **ACR-Resource-ID** w następujący sposób.

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-name>
```

oraz

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

Można również usunąć integrację między usługą ACR a klastrem AKS z następującymi elementami:

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-name>
```

lub

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-resource-id>
```

## <a name="working-with-acr--aks"></a>Praca z ACR & AKS

### <a name="import-an-image-into-your-acr"></a>Importowanie obrazu do ACR

Zaimportuj obraz z usługi Docker Hub do ACR, wykonując następujące czynności:


```azurecli
az acr import  -n <acr-name> --source docker.io/library/nginx:latest --image nginx:v1
```

### <a name="deploy-the-sample-image-from-acr-to-aks"></a>Wdrażanie przykładowego obrazu z ACR do AKS

Upewnij się, że masz odpowiednie poświadczenia AKS

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

Utwórz plik o nazwie **ACR-Nginx. YAML** , który zawiera następujące elementy. Zastąp nazwę zasobu rejestru nazwą **ACR**. Przykład: *myContainerRegistry*.

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

Następnie Uruchom to wdrożenie w klastrze AKS:

```console
kubectl apply -f acr-nginx.yaml
```

Wdrożenie można monitorować, uruchamiając następujące:

```console
kubectl get pods
```

Należy mieć dwa uruchomione zasobniki.

```output
NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

### <a name="troubleshooting"></a>Rozwiązywanie problemów
* Uruchom polecenie [AZ AKS Check-ACR](/cli/azure/aks#az_aks_check_acr) , aby sprawdzić, czy rejestr jest dostępny z klastra AKS.
* Dowiedz się więcej o [diagnostyce ACR](../container-registry/container-registry-diagnostics-audit-logs.md)
* Dowiedz się więcej o [kondycji ACR](../container-registry/container-registry-check-health.md)

<!-- LINKS - external -->
[AKS AKS CLI]: /cli/azure/aks#az-aks-create
[Image Pull secret]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/