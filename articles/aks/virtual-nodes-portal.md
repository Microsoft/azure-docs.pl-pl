---
title: Tworzenie węzłów wirtualnych przy użyciu portalu w usłudze Azure Kubernetes Services (AKS)
description: Dowiedz się, jak za pomocą Azure Portal utworzyć klaster usługi Azure Kubernetes Services (AKS), który używa węzłów wirtualnych do uruchamiania tego programu.
services: container-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 4c67d3608d2128385c273425ea495a02fa5a8c45
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102180908"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-in-the-azure-portal"></a>Utwórz i skonfiguruj klaster usługi Azure Kubernetes Services (AKS) do używania węzłów wirtualnych w Azure Portal

W tym artykule pokazano, jak za pomocą Azure Portal utworzyć i skonfigurować zasoby sieci wirtualnej oraz klaster AKS z włączonymi węzłami wirtualnymi.

> [!NOTE]
> [Ten artykuł](virtual-nodes.md) zawiera omówienie dostępności i ograniczeń w regionie przy użyciu węzłów wirtualnych.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Węzły wirtualne umożliwiają komunikację sieciową między jednostkami, które działają w Azure Container Instances (ACI) i klastrze AKS. W celu zapewnienia tej komunikacji zostanie utworzona podsieć sieci wirtualnej i przypisane uprawnienia delegowane. Węzły wirtualne działają tylko w przypadku klastrów AKS utworzonych przy użyciu *zaawansowanej* sieci (Azure CNI). Domyślnie klastry AKS są tworzone przy użyciu sieci *podstawowej* (korzystającą wtyczki kubenet). W tym artykule opisano sposób tworzenia sieci wirtualnej i podsieci, a następnie wdrażania klastra AKS, który korzysta z zaawansowanej sieci.

Jeśli nie korzystasz wcześniej z ACI, zarejestruj dostawcę usług w ramach subskrypcji. Stan rejestracji dostawcy ACI można sprawdzić za pomocą polecenia [AZ Provider list][az-provider-list] , jak pokazano w następującym przykładzie:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

Dostawca *Microsoft. ContainerInstance* powinien raportować jako *zarejestrowane*, jak pokazano w następujących przykładowych danych wyjściowych:

```output
Namespace                    RegistrationState    RegistrationPolicy
---------------------------  -------------------  --------------------
Microsoft.ContainerInstance  Registered           RegistrationRequired
```

Jeśli dostawca jest wyświetlany jako *NotRegistered*, zarejestruj dostawcę przy użyciu polecenia [AZ Provider Register][az-provider-register] , jak pokazano w następującym przykładzie:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-an-aks-cluster"></a>Tworzenie klastra AKS

W lewym górnym rogu Azure Portal wybierz pozycję **Utwórz zasób**  >  **Kubernetes usługi**.

Na stronie **Podstawowe** skonfiguruj następujące opcje:

- *SZCZEGÓŁY PROJEKTU*: wybierz subskrypcję platformy Azure, a następnie wybierz lub utwórz grupę zasobów platformy Azure, taką jak *myResourceGroup*. Wprowadź **nazwę klastra Kubernetes**, taką jak *myAKSCluster*.
- *SZCZEGÓŁY KLASTRA*: wybierz region, wersję platformy Kubernetes i prefiks nazwy DNS dla klastra usługi AKS.
- *Pula węzłów podstawowych*: Wybierz rozmiar maszyny wirtualnej dla węzłów AKS. Rozmiar maszyny wirtualnej **nie może** zostać zmieniony po wdrożeniu klastra AKS.
     - Wybierz liczbę węzłów do wdrożenia w klastrze. W tym artykule Ustaw **liczbę węzłów** na *1*. Liczbę węzłów **można** dostosować po wdrożeniu klastra.

Kliknij przycisk **Dalej: Skaluj**.

Na stronie **Skala** wybierz pozycję *włączone* w obszarze **węzły wirtualne**.

![Utwórz klaster AKS i Włącz węzły wirtualne](media/virtual-nodes-portal/enable-virtual-nodes.png)

Domyślnie zostanie utworzona jednostka usługi Azure Active Directory. Ta nazwa główna usługi jest używana do komunikacji klastra i integracji z innymi usługami platformy Azure. Alternatywnie można użyć tożsamości zarządzanej w celu uzyskania uprawnień zamiast nazwy głównej usługi. Aby uzyskać więcej informacji, zobacz [Korzystanie z tożsamości zarządzanych](use-managed-identity.md).

Klaster jest również skonfigurowany pod kątem zaawansowanej sieci. Węzły wirtualne są skonfigurowane do używania własnej podsieci sieci wirtualnej platformy Azure. Ta podsieć ma delegowane uprawnienia do łączenia zasobów platformy Azure między klastrem AKS. Jeśli nie masz jeszcze delegowanej podsieci, Azure Portal tworzy i konfiguruje sieć wirtualną platformy Azure oraz podsieć do użycia z węzłami wirtualnymi.

Wybierz pozycję **Przejrzyj i utwórz**. Po zakończeniu walidacji wybierz pozycję **Utwórz**.

Utworzenie klastra usługi AKS i przygotowanie go do użycia trwa kilka minut.

## <a name="connect-to-the-cluster"></a>Łączenie z klastrem

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Aby zarządzać klastrem Kubernetes, należy użyć klienta wiersza polecenia usługi Kubernetes, narzędzia [kubectl][kubectl]. Klient `kubectl` jest instalowany wstępnie wraz z usługą Azure Cloud Shell.

Aby otworzyć Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Cloud Shell można również uruchomić na osobnej karcie przeglądarki, przechodząc do [https://shell.azure.com/bash](https://shell.azure.com/bash) . Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.

Użyj polecenia [az aks get-credentials][az-aks-get-credentials] w celu skonfigurowania narzędzia `kubectl` w celu nawiązania połączenia z klastrem Kubernetes. Poniższy przykład umożliwia pobranie poświadczeń dla nazwy klastra *myAKSCluster* w grupie zasobów *myResourceGroup*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Aby sprawdzić połączenie z klastrem, użyj polecenia [kubectl get][kubectl-get], aby powrócić do listy węzłów klastra.

```console
kubectl get nodes
```

Następujące przykładowe dane wyjściowe pokazują utworzony węzeł maszyny wirtualnej, a następnie węzeł wirtualny dla systemu Linux, *Virtual-Node-ACI-Linux*:

```output
NAME                           STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux         Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0       Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Wdrażanie przykładowej aplikacji

W Azure Cloud Shell Utwórz plik o nazwie `virtual-node.yaml` i skopiuj w następującym YAML. Do zaplanowania kontenera w węźle są zdefiniowane [nodeSelector][node-selector] i [tolerowanie][toleration] . Te ustawienia umożliwiają planowanie na węźle wirtualnym i sprawdzanie, czy funkcja została pomyślnie włączona.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aci-helloworld
  template:
    metadata:
      labels:
        app: aci-helloworld
    spec:
      containers:
      - name: aci-helloworld
        image: mcr.microsoft.com/azuredocs/aci-helloworld
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/role: agent
        beta.kubernetes.io/os: linux
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Exists
```

Uruchom aplikację za pomocą polecenia [polecenia kubectl Apply][kubectl-apply] .

```azurecli-interactive
kubectl apply -f virtual-node.yaml
```

Użyj [polecenia kubectl Get][kubectl-get] -Binding polecenia z `-o wide` argumentem, aby wyprowadzić listę z listy i zaplanowanego węzła. Zwróć uwagę na to, że zgodnie z `virtual-node-helloworld` harmonogramem zaplanowano w `virtual-node-linux` węźle.

```console
kubectl get pods -o wide
```

```output
NAME                                     READY     STATUS    RESTARTS   AGE       IP           NODE
virtual-node-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Pod przypisywany jest wewnętrzny adres IP z podsieci sieci wirtualnej platformy Azure delegowanej do użycia z węzłami wirtualnymi.

> [!NOTE]
> Jeśli używasz obrazów przechowywanych w Azure Container Registry, [Skonfiguruj i użyj wpisu tajnego Kubernetes][acr-aks-secrets]. Bieżące ograniczenie węzłów wirtualnych polega na tym, że nie można używać zintegrowanego uwierzytelniania podstawowego usługi Azure AD. Jeśli nie korzystasz z wpisu tajnego, nie można uruchomić i zgłosić błędu `HTTP response status code 400 error code "InaccessibleImage"` .

## <a name="test-the-virtual-node-pod"></a>Przetestuj węzeł wirtualny pod

Aby przetestować pod kątem działania w węźle wirtualnym, przejdź do aplikacji demonstracyjnej za pomocą klienta sieci Web. Jako że pod przypisywanym wewnętrznym adresem IP można szybko przetestować to połączenie z innego elementu pod względem klastra AKS. Utwórz test pod i Dołącz do niego sesję terminalu:

```console
kubectl run -it --rm virtual-node-test --image=debian
```

Zainstaluj `curl` w temacie using `apt-get` :

```console
apt-get update && apt-get install -y curl
```

Uzyskaj teraz dostęp do adresu Twojego elementu przy użyciu `curl` , takich jak *http://10.241.0.4* . Podaj własny wewnętrzny adres IP przedstawiony w poprzednim `kubectl get pods` poleceniu:

```console
curl -L http://10.241.0.4
```

Zostanie wyświetlona aplikacja demonstracyjna, jak pokazano w następujących wąskich przykładowych danych wyjściowych:

```output
<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

Zamknij sesję terminalu z testem pod `exit` . Po zakończeniu sesji, pod, jest usunięty.

## <a name="next-steps"></a>Następne kroki

W tym artykule zaplanowano w węźle wirtualnym i przypisano prywatny, wewnętrzny adres IP. Zamiast tego można utworzyć wdrożenie usługi i skierować ruch do swojego użytkownika przy użyciu modułu równoważenia obciążenia lub kontrolera transferu danych przychodzących. Aby uzyskać więcej informacji, zobacz [Tworzenie podstawowego kontrolera danych wejściowych w AKS][aks-basic-ingress].

Węzły wirtualne są jednym składnikiem rozwiązania do skalowania w AKS. Aby uzyskać więcej informacji na temat skalowania rozwiązań, zobacz następujące artykuły:

- [Korzystanie z funkcji automatycznego skalowania w Kubernetes w poziomie][aks-hpa]
- [Korzystanie z automatycznego skalowania klastra Kubernetes][aks-cluster-autoscaler]
- [Zapoznaj się z przykładem skalowania automatycznego dla węzłów wirtualnych][virtual-node-autoscale]
- [Przeczytaj więcej na temat biblioteki Open Source Kubelet wirtualnej][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[aks-github]: https://github.com/azure/aks/issues]
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet
[acr-aks-secrets]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

<!-- LINKS - internal -->
[aks-network]: ./configure-azure-cni.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: cluster-autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider#az-provider-register
