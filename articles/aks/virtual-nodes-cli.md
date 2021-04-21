---
title: Tworzenie węzłów wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak za pomocą interfejsu wiersza polecenia platformy Azure utworzyć klaster usług Azure Kubernetes Services (AKS), który używa węzłów wirtualnych do uruchamiania zasobników.
services: container-service
ms.topic: conceptual
ms.date: 03/16/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 86f1d8923eea961471883c44168c4fa848ac12d1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769287"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-using-the-azure-cli"></a>Tworzenie i konfigurowanie klastra usług Azure Kubernetes Services (AKS) do używania węzłów wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure

W tym artykule pokazano, jak za pomocą interfejsu wiersza polecenia platformy Azure tworzyć i konfigurować zasoby sieci wirtualnej i klaster usługi AKS, a następnie włączać węzły wirtualne.


## <a name="before-you-begin"></a>Zanim rozpoczniesz

Węzły wirtualne umożliwiają komunikację sieciową między zasobnikami uruchomionymi w Azure Container Instances (ACI) i klastrem usługi AKS. Aby zapewnić taką komunikację, tworzona jest podsieć sieci wirtualnej i przypisywane są uprawnienia delegowane. Węzły wirtualne działają tylko z klastrami usługi AKS utworzonymi przy użyciu *zaawansowanej* sieci (Azure CNI). Domyślnie klastry usługi AKS są tworzone przy użyciu *sieci podstawowej* (kubenet). W tym artykule pokazano, jak utworzyć sieć wirtualną i podsieci, a następnie wdrożyć klaster usługi AKS, który używa zaawansowanej sieci.

> [!IMPORTANT]
> Przed użyciem węzłów wirtualnych z usługą AKS zapoznaj się zarówno z ograniczeniami węzłów wirtualnych usługi [AKS,][virtual-nodes-aks] jak i ograniczeniami sieci [wirtualnych usługi ACI.][virtual-nodes-networking-aci] Te ograniczenia mają wpływ na lokalizację, konfigurację sieci i inne szczegóły konfiguracji klastra usługi AKS i węzłów wirtualnych.

Jeśli wcześniej nie używano usługi ACI, zarejestruj dostawcę usług w subskrypcji. Stan rejestracji dostawcy ACI można sprawdzić za pomocą polecenia [az provider list,][az-provider-list] jak pokazano w poniższym przykładzie:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

Dostawca *Microsoft.ContainerInstance* powinien zgłosić jako *zarejestrowany*, jak pokazano w następujących przykładowych danych wyjściowych:

```output
Namespace                    RegistrationState    RegistrationPolicy
---------------------------  -------------------  --------------------
Microsoft.ContainerInstance  Registered           RegistrationRequired
```

Jeśli dostawca jest wyświetlany jako *NotRegistered,* zarejestruj dostawcę przy użyciu [narzędzia az provider register,][az-provider-register] jak pokazano w poniższym przykładzie:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie.

Aby otworzyć Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Możesz również uruchomić Cloud Shell na osobnej karcie przeglądarki, przechodząc do strony [https://shell.azure.com/bash](https://shell.azure.com/bash) . Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.

Jeśli wolisz zainstalować interfejs wiersza polecenia i używać go lokalnie, ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.49 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczna grupa przeznaczona do wdrażania zasobów platformy Azure i zarządzania nimi. Utwórz grupę zasobów za pomocą polecenia [az group create][az-group-create]. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *westus*.

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną za pomocą [polecenia az network vnet create.][az-network-vnet-create] Poniższy przykład tworzy nazwę sieci wirtualnej *myVnet* z prefiksem adresu *10.0.0.0/8* i podsiecią o nazwie *myAKSSubnet.* Prefiks adresu tej podsieci domyślnie to *10.240.0.0/16:*

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16
```

Teraz utwórz dodatkową podsieć dla węzłów wirtualnych za pomocą [polecenia az network vnet subnet create.][az-network-vnet-subnet-create] Poniższy przykład tworzy podsieć o nazwie *myVirtualNodeSubnet* z prefiksem *adresu 10.241.0.0/16.*

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name myVirtualNodeSubnet \
    --address-prefixes 10.241.0.0/16
```

## <a name="create-a-service-principal-or-use-a-managed-identity"></a>Tworzenie jednostki usługi lub używanie tożsamości zarządzanej

Aby umożliwić klastrowi usługi AKS interakcję z innymi zasobami platformy Azure, używana jest tożsamość klastra. Tę tożsamość klastra można utworzyć automatycznie za pomocą interfejsu wiersza polecenia platformy Azure lub portalu albo wstępnie utworzyć tę tożsamość i przypisać dodatkowe uprawnienia. Domyślnie ta tożsamość klastra jest tożsamością zarządzaną. Aby uzyskać więcej informacji, zobacz [Używanie tożsamości zarządzanych.](use-managed-identity.md) Możesz również użyć jednostki usługi jako tożsamości klastra. Poniższe kroki pokazują, jak ręcznie utworzyć i przypisać jednostkę usługi do klastra.

Utwórz jednostkę usługi za pomocą polecenia [az ad sp create-for-rbac][az-ad-sp-create-for-rbac]. Parametr `--skip-assignment` umożliwia ograniczenie przypisywania dodatkowych uprawnień.

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Dane wyjściowe są podobne do poniższego przykładu:

```output
{
  "appId": "bef76eb3-d743-4a97-9534-03e9388811fc",
  "displayName": "azure-cli-2018-11-21-18-42-00",
  "name": "http://azure-cli-2018-11-21-18-42-00",
  "password": "1d257915-8714-4ce7-a7fb-0e5a5411df7f",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

Zwróć uwagę na wartości *appId* i *password*. Te wartości są używane w kolejnych krokach.

## <a name="assign-permissions-to-the-virtual-network"></a>Przypisywanie uprawnień do sieci wirtualnej

Aby umożliwić klastrowi korzystanie z sieci wirtualnej i zarządzanie nimi, należy przyznać jednostki usługi AKS odpowiednie prawa do korzystania z zasobów sieciowych.

Najpierw pobierz identyfikator zasobu sieci wirtualnej przy użyciu [narzędzia az network vnet show][az-network-vnet-show]:

```azurecli-interactive
az network vnet show --resource-group myResourceGroup --name myVnet --query id -o tsv
```

Aby udzielić prawidłowego dostępu do klastra usługi AKS w celu korzystania z sieci wirtualnej, utwórz przypisanie roli za pomocą [polecenia az role assignment create.][az-role-assignment-create] Zastąp ciągi `<appId`> i `<vnetId>` wartościami określonymi w dwóch poprzednich krokach.

```azurecli-interactive
az role assignment create --assignee <appId> --scope <vnetId> --role Contributor
```

## <a name="create-an-aks-cluster"></a>Tworzenie klastra AKS

Klaster usługi AKS jest wdrażany w podsieci usługi AKS utworzonej w poprzednim kroku. Pobierz identyfikator tej podsieci za pomocą narzędzia [az network vnet subnet show][az-network-vnet-subnet-show]:

```azurecli-interactive
az network vnet subnet show --resource-group myResourceGroup --vnet-name myVnet --name myAKSSubnet --query id -o tsv
```

Utwórz klaster AKS za pomocą polecenia [az aks create][az-aks-create]. W poniższym przykładzie pokazano tworzenie klastra o nazwie *myAKSCluster* z jednym węzłem. Zastąp wartość identyfikatorem uzyskanymi w poprzednim kroku, a następnie wartościami `<subnetId>` `<appId>` `<password>` zebranymi w poprzedniej sekcji.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id <subnetId> \
    --service-principal <appId> \
    --client-secret <password>
```

Po kilku minutach polecenie zostanie zakończone i zwróci informacje o klastrze sformatowanym przy użyciu formatu JSON.

## <a name="enable-virtual-nodes-addon"></a>Włączanie dodatku węzłów wirtualnych

Aby włączyć węzły wirtualne, użyj teraz [polecenia az aks enable-addons.][az-aks-enable-addons] W poniższym przykładzie użyto podsieci *o nazwie myVirtualNodeSubnet* utworzonej w poprzednim kroku:

```azurecli-interactive
az aks enable-addons \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --addons virtual-node \
    --subnet-name myVirtualNodeSubnet
```

## <a name="connect-to-the-cluster"></a>Łączenie z klastrem

Aby skonfigurować narzędzie `kubectl` w celu nawiązania połączenia z klastrem Kubernetes, użyj polecenia [az aks get-credentials][az-aks-get-credentials]. Ten krok umożliwia pobranie poświadczeń i skonfigurowanie interfejsu wiersza polecenia Kubernetes do ich użycia.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Aby sprawdzić połączenie z klastrem, użyj polecenia [kubectl get][kubectl-get], aby powrócić do listy węzłów klastra.

```console
kubectl get nodes
```

Następujące przykładowe dane wyjściowe pokazują utworzony pojedynczy węzeł maszyny wirtualnej, a następnie węzeł wirtualny dla systemu Linux *virtual-node-aci-linux:*

```output
NAME                          STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux        Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0      Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Wdrażanie przykładowej aplikacji

Utwórz plik o nazwie `virtual-node.yaml` i skopiuj go w poniższym pliku YAML. Aby zaplanować kontener w węźle, definiowane są [właściwości nodeSelector][node-selector] [i tolerancja.][toleration]

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
      - key: azure.com/aci
        effect: NoSchedule
```

Uruchom aplikację za pomocą [polecenia kubectl apply.][kubectl-apply]

```console
kubectl apply -f virtual-node.yaml
```

Użyj polecenia [kubectl get pods][kubectl-get] z argumentem , aby wyświetlić listę zasobników `-o wide` i zaplanowany węzeł. Zwróć uwagę, `aci-helloworld` że zasobnik został zaplanowany w `virtual-node-aci-linux` węźle.

```console
kubectl get pods -o wide
```

```output
NAME                            READY     STATUS    RESTARTS   AGE       IP           NODE
aci-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Zasobnik ma przypisany wewnętrzny adres IP z podsieci sieci wirtualnej platformy Azure delegowany do użytku z węzłami wirtualnymi.

> [!NOTE]
> Jeśli używasz obrazów przechowywanych w Azure Container Registry, [skonfiguruj klucz tajny Kubernetes][acr-aks-secrets]i użyj go. Bieżącym ograniczeniem węzłów wirtualnych jest to, że nie można używać zintegrowanego uwierzytelniania jednostki usługi Azure AD. Jeśli nie używasz tajnego, nie można uruchomić zasobników zaplanowanych w węzłach wirtualnych i zgłosić błąd `HTTP response status code 400 error code "InaccessibleImage"` .

## <a name="test-the-virtual-node-pod"></a>Testowanie zasobnika węzła wirtualnego

Aby przetestować zasobnik uruchomiony w węźle wirtualnym, przejdź do aplikacji demonstracyjnej przy użyciu klienta internetowego. Ponieważ zasobnik ma przypisany wewnętrzny adres IP, możesz szybko przetestować tę łączność z innego zasobnika w klastrze usługi AKS. Utwórz zasobnik testowy i dołącz do niego sesję terminalową:

```console
kubectl run -it --rm testvk --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
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

## <a name="remove-virtual-nodes"></a>Usuwanie węzłów wirtualnych

Jeśli nie chcesz już używać węzłów wirtualnych, możesz je wyłączyć za pomocą [polecenia az aks disable-addons.][az aks disable-addons] 

W razie potrzeby przejdź do [https://shell.azure.com](https://shell.azure.com) strony , aby Azure Cloud Shell w przeglądarce.

Najpierw usuń zasobnik `aci-helloworld` uruchomiony w węźle wirtualnym:

```console
kubectl delete -f virtual-node.yaml
```

Następujące przykładowe polecenie wyłącza węzły wirtualne systemu Linux:

```azurecli-interactive
az aks disable-addons --resource-group myResourceGroup --name myAKSCluster --addons virtual-node
```

Teraz usuń zasoby sieci wirtualnej i grupę zasobów:

```azurecli-interactive
# Change the name of your resource group, cluster and network resources as needed
RES_GROUP=myResourceGroup
AKS_CLUSTER=myAKScluster
AKS_VNET=myVnet
AKS_SUBNET=myVirtualNodeSubnet

# Get AKS node resource group
NODE_RES_GROUP=$(az aks show --resource-group $RES_GROUP --name $AKS_CLUSTER --query nodeResourceGroup --output tsv)

# Get network profile ID
NETWORK_PROFILE_ID=$(az network profile list --resource-group $NODE_RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Delete the subnet delegation to Azure Container Instances
az network vnet subnet update --resource-group $RES_GROUP --vnet-name $AKS_VNET --name $AKS_SUBNET --remove delegations 0
```

## <a name="next-steps"></a>Następne kroki

W tym artykule zasobnik został zaplanowany w węźle wirtualnym i przypisany prywatny wewnętrzny adres IP. Zamiast tego można utworzyć wdrożenie usługi i przekierowyć ruch do zasobnika za pośrednictwem usługi równoważenia obciążenia lub kontrolera ruchu wychodzącego. Aby uzyskać więcej informacji, zobacz Create a basic ingress controller in AKS (Tworzenie podstawowego kontrolera [ruchu wychodzącego w u usługie AKS).][aks-basic-ingress]

Węzły wirtualne są często jednym ze składników rozwiązania skalowania w u usługi AKS. Aby uzyskać więcej informacji na temat rozwiązań skalowania, zobacz następujące artykuły:

- [Korzystanie z funkcji automatycznego skalowania zasobników w poziomie na kubernetes][aks-hpa]
- [Korzystanie z funkcji automatycznego skalowania klastra Kubernetes][aks-cluster-autoscaler]
- [Zapoznaj się z przykładowym skalowaniem automatycznym dla węzłów wirtualnych][virtual-node-autoscale]
- [Dowiedz się więcej o bibliotece usługi Virtual Kubelet open source Kubelet][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[aks-github]: https://github.com/azure/aks/issues
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet
[acr-aks-secrets]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-create]: /cli/azure/group#az_group_create
[az-network-vnet-create]: /cli/azure/network/vnet#az_network_vnet_create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az_network_vnet_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_show
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-enable-addons]: /cli/azure/aks#az_aks_enable_addons
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az aks disable-addons]: /cli/azure/aks#az_aks_disable_addons
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: ./cluster-autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az_provider_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[virtual-nodes-aks]: virtual-nodes.md
[virtual-nodes-networking-aci]: ../container-instances/container-instances-virtual-network-concepts.md