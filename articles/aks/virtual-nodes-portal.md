---
title: Tworzenie węzłów wirtualnych przy użyciu portalu w usługach Azure Kubernetes (AKS)
description: Dowiedz się, jak używać witryny Azure Portal do tworzenia klastra usług Kubernetes platformy Azure (AKS), który używa węzłów wirtualnych do uruchamiania zasobników.
services: container-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 62d8fec4c5c3ff35fb46826cb7118946f66948b2
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392578"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-in-the-azure-portal"></a>Tworzenie i konfigurowanie klastra usług Kubernetes platformy Azure (AKS) do używania węzłów wirtualnych w witrynie Azure portal

Aby szybko wdrożyć obciążenia w klastrze usługi Azure Kubernetes (AKS), można użyć węzłów wirtualnych. W przypadku węzłów wirtualnych masz szybkie inicjowanie obsługi administracyjnej zasobników i płacisz tylko za sekundę za czas wykonywania. W scenariuszu skalowania nie trzeba czekać na skalowanie automatyczne klastra Kubernetes wdrożyć węzły obliczeniowe maszyny Wirtualnej do uruchomienia dodatkowych zasobników. Wirtualne węzły są obsługiwane tylko w zasobnikach i węzłach systemu Linux.

W tym artykule pokazano, jak utworzyć i skonfigurować zasoby sieci wirtualnej i klaster AKS z włączonymi węzłami wirtualnymi.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Węzły wirtualne umożliwiają komunikację sieciową między zasobnikami uruchamianym w instancjach kontenera azure (ACI) i klastrze AKS. Aby zapewnić tę komunikację, tworzona jest podsieć sieci wirtualnej i przypisywane są uprawnienia delegowane. Węzły wirtualne działają tylko z klastrami AKS utworzonymi przy użyciu *zaawansowanej* sieci. Domyślnie klastry AKS są tworzone z *podstawową* siecią. W tym artykule pokazano, jak utworzyć sieć wirtualną i podsieci, a następnie wdrożyć klaster AKS korzystający z sieci zaawansowanych.

Jeśli wcześniej nie korzystano z usługi ACI, zarejestruj dostawcę usług w swojej subskrypcji. Stan rejestracji dostawcy usługi ACI można sprawdzić za pomocą polecenia [listy dostawców az,][az-provider-list] jak pokazano w poniższym przykładzie:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

Dostawca *microsoft.containerinstance* powinien zgłosić jako *zarejestrowany,* jak pokazano w poniższym przykładzie danych wyjściowych:

```output
Namespace                    RegistrationState    RegistrationPolicy
---------------------------  -------------------  --------------------
Microsoft.ContainerInstance  Registered           RegistrationRequired
```

Jeśli dostawca jest wyświetlany jako *NotRegistered*, zarejestruj dostawcę przy użyciu [rejestru dostawcy az,][az-provider-register] jak pokazano w poniższym przykładzie:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="regional-availability"></a>Dostępność regionalna

Następujące regiony są obsługiwane dla wdrożeń węzłów wirtualnych:

* Australia Wschodnia (australiaeast)
* Środkowe stany USA (centralus)
* Wschodnie stany USA (eastus)
* Wschodnie stany USA 2 (eastus2)
* Japonia Wschodnia (japaneast)
* Europa Północna (europa północna)
* Azja Południowo-Wschodnia (południowo-wschodnia)
* Zachodnie środkowe stany USA (westcentralus)
* Europa Zachodnia (europa zachodnia)
* Zachodnie stany USA (zachód)
* Zachodnie stany USA 2 (westus2)

## <a name="known-limitations"></a>Znane ograniczenia
Funkcja wirtualnych węzłów jest w dużym stopniu zależna od zestawu funkcji ACI. Następujące scenariusze nie są jeszcze obsługiwane za pomocą węzłów wirtualnych

* Za pomocą jednostki usługi do ściągania obrazów ACR. [Obejście polega](https://github.com/virtual-kubelet/virtual-kubelet/blob/master/providers/azure/README.md#Private-registry) na użyciu [wpisów tajnych kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line)
* [Ograniczenia sieci wirtualnej,](../container-instances/container-instances-vnet.md) w tym komunikacja równorzędna sieci wirtualnej, zasady sieci Kubernetes i ruch wychodzący do Internetu z sieciowymi grupami zabezpieczeń.
* Pojemniki init
* [Aliasy hostów](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)
* [Argumenty](../container-instances/container-instances-exec.md#restrictions) dla exec w ACI
* [DaemonSets](concepts-clusters-workloads.md#statefulsets-and-daemonsets) nie wdroży zasobników w węźle wirtualnym
* Węzły wirtualne obsługują planowanie zasobników systemu Linux. Można ręcznie zainstalować dostawcę [ACI Virtual Kubelet](https://github.com/virtual-kubelet/azure-aci) open source, aby zaplanować kontenery systemu Windows Server do usługi ACI. 

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-an-aks-cluster"></a>Tworzenie klastra AKS

W lewym górnym rogu witryny Azure portal wybierz pozycję **Utwórz usługę** > **Kubernetes**.

Na stronie **Podstawy** skonfiguruj następujące opcje:

- *SZCZEGÓŁY PROJEKTU*: wybierz subskrypcję platformy Azure, a następnie wybierz lub utwórz grupę zasobów platformy Azure, taką jak *myResourceGroup*. Wprowadź **nazwę klastra Kubernetes**, taką jak *myAKSCluster*.
- *SZCZEGÓŁY KLASTRA*: wybierz region, wersję platformy Kubernetes i prefiks nazwy DNS dla klastra usługi AKS.
- *GŁÓWNA PULA WĘZŁÓW:* Wybierz rozmiar maszyny Wirtualnej dla węzłów AKS. Rozmiar maszyny wirtualnej **nie może** zostać zmieniony po wdrożeniu klastra AKS.
     - Wybierz liczbę węzłów do wdrożenia w klastrze. W tym artykule ustaw **liczbę węzłów** na *1*. Liczbę węzłów **można** dostosować po wdrożeniu klastra.

Kliknij **przycisk Dalej: Skaluj**.

Na stronie **Skalowanie** wybierz pozycję *Włączone* w obszarze **Węzły wirtualne**.

![Tworzenie klastra AKS i włączanie węzłów wirtualnych](media/virtual-nodes-portal/enable-virtual-nodes.png)

Domyślnie tworzony jest podmiot usługi Azure Active Directory. Ten podmiot zabezpieczeń usługi jest używany do komunikacji klastra i integracji z innymi usługami platformy Azure. Alternatywnie można użyć tożsamości zarządzanej dla uprawnień zamiast jednostki usługi. Aby uzyskać więcej informacji, zobacz [Używanie tożsamości zarządzanych](use-managed-identity.md).

Klaster jest również skonfigurowany do zaawansowanej sieci. Węzły wirtualne są skonfigurowane do używania własnej podsieci sieci wirtualnej platformy Azure. Ta podsieć ma delegowane uprawnienia do łączenia zasobów platformy Azure między klastrem AKS. Jeśli nie masz jeszcze delegowanej podsieci, portal Azure tworzy i konfiguruje sieć wirtualną platformy Azure i podsieć do użytku z węzłami wirtualnymi.

Wybierz pozycję **Przegląd + utwórz**. Po zakończeniu sprawdzania poprawności wybierz pozycję **Utwórz**.

Utworzenie klastra usługi AKS i przygotowanie go do użycia trwa kilka minut.

## <a name="connect-to-the-cluster"></a>Łączenie z klastrem

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Aby zarządzać klastrem Kubernetes, należy użyć klienta wiersza polecenia usługi Kubernetes, narzędzia [kubectl][kubectl]. Klient `kubectl` jest instalowany wstępnie wraz z usługą Azure Cloud Shell.

Aby otworzyć powłokę chmury, wybierz **pozycję Wypróbuj ją** w prawym górnym rogu bloku kodu. Możesz również uruchomić usługę Cloud Shell w [https://shell.azure.com/bash](https://shell.azure.com/bash)osobnej karcie przeglądarki, przechodząc do . Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.

Użyj polecenia [az aks get-credentials][az-aks-get-credentials] w celu skonfigurowania narzędzia `kubectl` w celu nawiązania połączenia z klastrem Kubernetes. Poniższy przykład umożliwia pobranie poświadczeń dla nazwy klastra *myAKSCluster* w grupie zasobów *myResourceGroup*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Aby sprawdzić połączenie z klastrem, użyj polecenia [kubectl get][kubectl-get], aby powrócić do listy węzłów klastra.

```console
kubectl get nodes
```

Poniższy przykładowy wynik przedstawia pojedynczy węzeł maszyny Wirtualnej utworzony, a następnie węzeł wirtualny dla systemu Linux, *virtual-node-aci-linux:*

```output
NAME                           STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux         Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0       Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Wdrażanie przykładowej aplikacji

W usłudze Azure Cloud Shell `virtual-node.yaml` utwórz plik o nazwie i skopiuj w następującym pliku YAML. Aby zaplanować kontener w węźle, [zdefiniowano nodeSelector][node-selector] i [tolerancja.][toleration] Te ustawienia umożliwiają zaplanowanie zasobnika w węźle wirtualnym i potwierdzenie, że funkcja została pomyślnie włączona.

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
        image: microsoft/aci-helloworld
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/role: agent
        beta.kubernetes.io/os: linux
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Exists
      - key: azure.com/aci
        effect: NoSchedule
```

Uruchom aplikację za pomocą polecenia [zastosuj kubectl.][kubectl-apply]

```azurecli-interactive
kubectl apply -f virtual-node.yaml
```

Użyj [polecenia kubectl get zasobników][kubectl-get] z argumentem, `-o wide` aby wyprowadzić listę zasobników i zaplanowanego węzła. Należy zauważyć, `virtual-node-helloworld` że zasobnik `virtual-node-linux` został zaplanowany w węźle.

```console
kubectl get pods -o wide
```

```output
NAME                                     READY     STATUS    RESTARTS   AGE       IP           NODE
virtual-node-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Zasobnik jest przypisany wewnętrzny adres IP z podsieci sieci wirtualnej platformy Azure delegowane do użytku z węzłami wirtualnymi.

> [!NOTE]
> Jeśli używasz obrazów przechowywanych w rejestrze kontenerów platformy Azure, [skonfiguruj i użyj klucza tajnego Kubernetes][acr-aks-secrets]. Bieżące ograniczenie węzłów wirtualnych jest, że nie można użyć zintegrowanego uwierzytelniania głównego usługi Azure AD. Jeśli nie używasz klucza tajnego, zasobników zaplanowanych w węzłach `HTTP response status code 400 error code "InaccessibleImage"`wirtualnych nie można uruchomić i zgłosić błąd .

## <a name="test-the-virtual-node-pod"></a>Testowanie zasobnika węzła wirtualnego

Aby przetestować zasobnika uruchomionego w węźle wirtualnym, przejdź do aplikacji demonstracyjnej za pomocą klienta sieci web. Ponieważ zasobnik jest przypisany wewnętrzny adres IP, można szybko przetestować tę łączność z innego zasobnika w klastrze AKS. Utwórz zasobnik testowy i dołącz do niej sesję terminala:

```console
kubectl run -it --rm virtual-node-test --image=debian
```

Zainstaluj `curl` w zasobniku za pomocą: `apt-get`

```console
apt-get update && apt-get install -y curl
```

Teraz dostęp do adresu `curl`zasobnika *http://10.241.0.4*za pomocą , takich jak . Podaj swój wewnętrzny adres `kubectl get pods` IP pokazany w poprzednim poleceniu:

```console
curl -L http://10.241.0.4
```

Aplikacja demonstracyjna jest wyświetlana, jak pokazano na poniższym skondensowanym przykładzie:

```output
<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

Zamknij sesję terminalu do `exit`zasobnika testowego za pomocą pliku . Po zakończeniu sesji zasobnik zostanie usunięty.

## <a name="next-steps"></a>Następne kroki

W tym artykule zasobnik został zaplanowany w węźle wirtualnym i przypisano prywatny, wewnętrzny adres IP. Zamiast tego można utworzyć wdrożenie usługi i kierować ruch do zasobnika za pośrednictwem modułu równoważenia obciążenia lub kontrolera transferu danych przychodzących. Aby uzyskać więcej informacji, zobacz [Tworzenie podstawowego kontrolera transferu danych przychodzących w u.][aks-basic-ingress]

Węzły wirtualne są jednym ze składników rozwiązania skalowania w u. Aby uzyskać więcej informacji na temat skalowania rozwiązań, zobacz następujące artykuły:

- [Użyj skalowania automatycznego skalowania w poziomie kubernetes][aks-hpa]
- [Korzystanie z skalowania automatycznego klastra Kubernetes][aks-cluster-autoscaler]
- [Zapoznaj się z przykładem skalowania automatycznego dla węzłów wirtualnych][virtual-node-autoscale]
- [Dowiedz się więcej o bibliotece open source Virtual Kubelet][virtual-kubelet-repo]

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
[aks-network]: ./networking-overview.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: cluster-autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register