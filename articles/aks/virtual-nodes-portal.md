---
title: Tworzenie węzłów wirtualnych przy użyciu portalu w usługach Azure Kubernetes Services (AKS)
description: Dowiedz się, jak za pomocą Azure Portal utworzyć klaster usług Azure Kubernetes Services (AKS), który używa węzłów wirtualnych do uruchamiania zasobników.
services: container-service
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: references_regions
ms.openlocfilehash: bfb9e4a9a7c788255ea7fcba0fb42404829f82f3
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379353"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-in-the-azure-portal"></a>Tworzenie i konfigurowanie klastra usług Azure Kubernetes Services (AKS) w celu używania węzłów wirtualnych w Azure Portal

W tym artykule pokazano, jak za pomocą Azure Portal tworzyć i konfigurować zasoby sieci wirtualnej oraz klaster usługi AKS z włączonymi węzłami wirtualnymi.

> [!NOTE]
> [Ten artykuł](virtual-nodes.md) zawiera omówienie dostępności regionów i ograniczeń dotyczących korzystania z węzłów wirtualnych.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Węzły wirtualne umożliwiają komunikację sieciową między zasobnikami uruchomionymi w Azure Container Instances (ACI) i klastrem usługi AKS. Aby zapewnić taką komunikację, tworzona jest podsieć sieci wirtualnej i przypisywane są uprawnienia delegowane. Węzły wirtualne działają tylko z klastrami usługi AKS *utworzonymi* przy użyciu sieci zaawansowanej (Azure CNI). Domyślnie klastry usługi AKS są tworzone z *podstawową siecią* (kubenet). W tym artykule pokazano, jak utworzyć sieć wirtualną i podsieci, a następnie wdrożyć klaster usługi AKS, który używa zaawansowanej sieci.

Jeśli wcześniej nie używano usługi ACI, zarejestruj dostawcę usług w ramach subskrypcji. Stan rejestracji dostawcy ACI można sprawdzić za pomocą polecenia [az provider list,][az-provider-list] jak pokazano w poniższym przykładzie:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

Dostawca *Microsoft.ContainerInstance* powinien zgłosić jako *Zarejestrowany*, jak pokazano w następujących przykładowych danych wyjściowych:

```output
Namespace                    RegistrationState    RegistrationPolicy
---------------------------  -------------------  --------------------
Microsoft.ContainerInstance  Registered           RegistrationRequired
```

Jeśli dostawca jest wyświetlany jako *NotRegistered,* zarejestruj go przy użyciu narzędzia [az provider register,][az-provider-register] jak pokazano w poniższym przykładzie:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-an-aks-cluster"></a>Tworzenie klastra AKS

W lewym górnym rogu okna usługi Azure Portal pozycję **Utwórz zasób**  >  **usługi Kubernetes Service.**

Na stronie **Podstawowe** skonfiguruj następujące opcje:

- *SZCZEGÓŁY PROJEKTU*: wybierz subskrypcję platformy Azure, a następnie wybierz lub utwórz grupę zasobów platformy Azure, taką jak *myResourceGroup*. Wprowadź **nazwę klastra Kubernetes**, taką jak *myAKSCluster*.
- *SZCZEGÓŁY KLASTRA:* wybierz region i wersję usługi Kubernetes dla klastra usługi AKS.
- *PULA WĘZŁÓW PODSTAWOWYCH:* wybierz rozmiar maszyny wirtualnej dla węzłów usługi AKS. Rozmiar maszyny wirtualnej **nie może** zostać zmieniony po wdrożeniu klastra AKS.
     - Wybierz liczbę węzłów do wdrożenia w klastrze. W tym artykule ustaw **liczbę węzłów na** *1*. Liczbę węzłów **można** dostosować po wdrożeniu klastra.

Kliknij **przycisk Dalej: Pule węzłów.**

Na stronie **Pule węzłów** wybierz pozycję *Włącz węzły wirtualne.*

:::image type="content" source="media/virtual-nodes-portal/enable-virtual-nodes.png" alt-text="W przeglądarce program pokazuje tworzenie klastra z węzłami wirtualnymi włączonymi na Azure Portal. Opcja &quot;Włącz węzły wirtualne&quot; jest wyróżniona.":::

Domyślnie tworzona jest tożsamość klastra. Ta tożsamość klastra jest używana do komunikacji klastra i integracji z innymi usługami platformy Azure. Domyślnie ta tożsamość klastra jest tożsamością zarządzaną. Aby uzyskać więcej informacji, zobacz [Use managed identities (Korzystanie z tożsamości zarządzanych).](use-managed-identity.md) Możesz również użyć jednostki usługi jako tożsamości klastra.

Klaster jest również skonfigurowany do zaawansowanej sieci. Węzły wirtualne są skonfigurowane do używania własnej podsieci sieci wirtualnej platformy Azure. Ta podsieć ma delegowane uprawnienia do łączenia zasobów platformy Azure między klastrem usługi AKS. Jeśli nie masz jeszcze podsieci delegowanych, Azure Portal tworzy i konfiguruje sieć wirtualną i podsieć platformy Azure do użycia z węzłami wirtualnymi.

Wybierz pozycję **Przejrzyj i utwórz**. Po zakończeniu walidacji wybierz pozycję **Utwórz**.

Utworzenie klastra usługi AKS i przygotowanie go do użycia trwa kilka minut.

## <a name="connect-to-the-cluster"></a>Łączenie z klastrem

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Aby zarządzać klastrem Kubernetes, należy użyć klienta wiersza polecenia usługi Kubernetes, narzędzia [kubectl][kubectl]. Klient `kubectl` jest instalowany wstępnie wraz z usługą Azure Cloud Shell.

Aby otworzyć Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Możesz również uruchomić Cloud Shell na osobnej karcie przeglądarki, przechodząc do strony [https://shell.azure.com/bash](https://shell.azure.com/bash) . Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.

Użyj polecenia [az aks get-credentials][az-aks-get-credentials] w celu skonfigurowania narzędzia `kubectl` w celu nawiązania połączenia z klastrem Kubernetes. Poniższy przykład umożliwia pobranie poświadczeń dla nazwy klastra *myAKSCluster* w grupie zasobów *myResourceGroup*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Aby sprawdzić połączenie z klastrem, użyj polecenia [kubectl get][kubectl-get], aby powrócić do listy węzłów klastra.

```console
kubectl get nodes
```

Następujące przykładowe dane wyjściowe pokazują utworzony pojedynczy węzeł maszyny wirtualnej, a następnie węzeł wirtualny dla systemu Linux *virtual-node-aci-linux:*

```output
NAME                           STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux         Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0       Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Wdrażanie przykładowej aplikacji

W Azure Cloud Shell utwórz plik o nazwie `virtual-node.yaml` i skopiuj go w poniższym pliku YAML. Aby zaplanować kontener w węźle, definiowane są [właściwości nodeSelector][node-selector] [i tolerancja.][toleration] Te ustawienia umożliwiają zaplanowanie zasobnika w węźle wirtualnym i potwierdzenie, że funkcja została pomyślnie włączona.

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

Uruchom aplikację za pomocą [polecenia kubectl apply.][kubectl-apply]

```azurecli-interactive
kubectl apply -f virtual-node.yaml
```

Użyj polecenia [kubectl get pods][kubectl-get] z argumentem , aby wyświetlić listę zasobników `-o wide` i zaplanowany węzeł. Zwróć uwagę, `virtual-node-helloworld` że zasobnik został zaplanowany w `virtual-node-linux` węźle.

```console
kubectl get pods -o wide
```

```output
NAME                                     READY     STATUS    RESTARTS   AGE       IP           NODE
virtual-node-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Zasobnik ma przypisany wewnętrzny adres IP z podsieci sieci wirtualnej platformy Azure delegowany do użytku z węzłami wirtualnymi.

> [!NOTE]
> Jeśli używasz obrazów przechowywanych w Azure Container Registry, [skonfiguruj klucz tajny Kubernetes][acr-aks-secrets]i użyj go. Bieżącym ograniczeniem węzłów wirtualnych jest to, że nie można używać zintegrowanego uwierzytelniania jednostki usługi Azure AD. Jeśli nie używasz tajnego, nie można uruchomić zasobników zaplanowanych w węzłach wirtualnych i zgłosić błąd `HTTP response status code 400 error code "InaccessibleImage"` .

## <a name="test-the-virtual-node-pod"></a>Testowanie zasobnika węzła wirtualnego

Aby przetestować zasobnik uruchomiony w węźle wirtualnym, przejdź do aplikacji demonstracyjnej przy użyciu klienta internetowego. Ponieważ zasobnik ma przypisany wewnętrzny adres IP, możesz szybko przetestować tę łączność z innego zasobnika w klastrze usługi AKS. Utwórz zasobnik testowy i dołącz do niego sesję terminalową:

```console
kubectl run -it --rm virtual-node-test --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
```

Zainstaluj `curl` w zasobniku przy użyciu narzędzia `apt-get` :

```console
apt-get update && apt-get install -y curl
```

Teraz uzyskaj dostęp do adresu zasobnika przy użyciu narzędzia `curl` , takiego jak *http://10.241.0.4* . Podaj własny wewnętrzny adres IP pokazany w poprzednim `kubectl get pods` poleceniu:

```console
curl -L http://10.241.0.4
```

Zostanie wyświetlona aplikacja demonstracyjna, jak pokazano w poniższych skróconych przykładowych danych wyjściowych:

```output
<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

Zamknij sesję terminalu z zasobnikiem testowym za pomocą . `exit` Po zakończeniu sesji zasobnik zostanie usunięty.

## <a name="next-steps"></a>Następne kroki

W tym artykule zasobnik został zaplanowany w węźle wirtualnym i przypisany prywatny wewnętrzny adres IP. Zamiast tego można utworzyć wdrożenie usługi i przekierowyć ruch do zasobnika za pośrednictwem usługi równoważenia obciążenia lub kontrolera ruchu wychodzącego. Aby uzyskać więcej informacji, zobacz Create a basic ingress controller in AKS (Tworzenie podstawowego kontrolera [ruchu wychodzącego w UKS).][aks-basic-ingress]

Węzły wirtualne są jednym ze składników rozwiązania skalowania w u usługi AKS. Aby uzyskać więcej informacji na temat rozwiązań skalowania, zobacz następujące artykuły:

- [Korzystanie z funkcji automatycznego skalowania zasobników w poziomie na kubernetes][aks-hpa]
- [Korzystanie z funkcji automatycznego skalowania klastra Kubernetes][aks-cluster-autoscaler]
- [Zapoznaj się z przykładowym skalowaniem automatycznym dla węzłów wirtualnych][virtual-node-autoscale]
- [Dowiedz się więcej o bibliotece usługi Virtual Kubelet open source Kubelet][virtual-kubelet-repo]

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
