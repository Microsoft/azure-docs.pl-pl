---
title: Tworzenie węzłów wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak używać interfejsu wiersza polecenia platformy Azure do tworzenia klastra usług Kubernetes platformy Azure (AKS), który używa węzłów wirtualnych do uruchamiania zasobników.
services: container-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: b3dec8a7d46226b9d6f4416c98332f0023c0c294
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392602"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-using-the-azure-cli"></a>Tworzenie i konfigurowanie klastra usług Kubernetes platformy Azure (AKS) w celu używania węzłów wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure

Aby szybko skalować obciążenia aplikacji w klastrze usługi Azure Kubernetes (AKS), można użyć węzłów wirtualnych. W przypadku węzłów wirtualnych masz szybkie inicjowanie obsługi administracyjnej zasobników i płacisz tylko za sekundę za czas wykonywania. Nie trzeba czekać na skalowanie automatyczne klastra Kubernetes do wdrożenia węzłów obliczeniowych maszyny Wirtualnej do uruchomienia dodatkowych zasobników. Wirtualne węzły są obsługiwane tylko w zasobnikach i węzłach systemu Linux.

W tym artykule pokazano, jak utworzyć i skonfigurować zasoby sieci wirtualnej i klaster AKS, a następnie włączyć węzły wirtualne.

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

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie.

Aby otworzyć powłokę chmury, wybierz **pozycję Wypróbuj ją** w prawym górnym rogu bloku kodu. Możesz również uruchomić usługę Cloud Shell w [https://shell.azure.com/bash](https://shell.azure.com/bash)osobnej karcie przeglądarki, przechodząc do . Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.

Jeśli wolisz zainstalować i używać interfejsu wiersza polecenia lokalnie, w tym artykule wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.49 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczna grupa przeznaczona do wdrażania zasobów platformy Azure i zarządzania nimi. Utwórz grupę zasobów za pomocą polecenia [az group create][az-group-create]. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *westus*.

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną za pomocą polecenia [tworzenie sieci wirtualnej az.][az-network-vnet-create] Poniższy przykład tworzy nazwę sieci wirtualnej *myVnet* z prefiksem adresu *10.0.0.0/8*i podsieci o nazwie *myAKSSubnet*. Prefiks adresu tej podsieci jest domyślnie *10.240.0.0/16:*

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16
```

Teraz utwórz dodatkową podsieć dla węzłów wirtualnych za pomocą polecenia tworzenia podsieci [sieci AZ.][az-network-vnet-subnet-create] Poniższy przykład tworzy podsieć o nazwie *myVirtualNodeSubnet* z prefiksem adresu *10.241.0.0/16*.

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name myVirtualNodeSubnet \
    --address-prefixes 10.241.0.0/16
```

## <a name="create-a-service-principal-or-use-a-managed-identity"></a>Tworzenie jednostki usługi lub używanie tożsamości zarządzanej

Jednostka usługi Azure Active Directory zezwala klastrowi usługi AKS na interakcje z innymi zasobami platformy Azure. Tę jednostkę usługi może automatycznie utworzyć interfejs wiersza polecenia platformy Azure lub portal. Inna możliwość to jej wstępne utworzenie i przypisanie dodatkowych uprawnień. Alternatywnie można użyć tożsamości zarządzanej dla uprawnień zamiast jednostki usługi. Aby uzyskać więcej informacji, zobacz [Używanie tożsamości zarządzanych](use-managed-identity.md).

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

Aby zezwolić klastrowi na używanie sieci wirtualnej i zarządzanie nią, należy przyznać podmiotowi usługi AKS poprawne prawa do korzystania z zasobów sieciowych.

Najpierw pobierz identyfikator zasobu sieci wirtualnej za pomocą [programu az network vnet show:][az-network-vnet-show]

```azurecli-interactive
az network vnet show --resource-group myResourceGroup --name myVnet --query id -o tsv
```

Aby udzielić klastrowi AKS prawidłowego dostępu do korzystania z sieci wirtualnej, utwórz przypisanie roli za pomocą [polecenia utwórz przypisanie roli az.][az-role-assignment-create] Zastąp ciągi `<appId`> i `<vnetId>` wartościami określonymi w dwóch poprzednich krokach.

```azurecli-interactive
az role assignment create --assignee <appId> --scope <vnetId> --role Contributor
```

## <a name="create-an-aks-cluster"></a>Tworzenie klastra AKS

Klaster AKS można wdrożyć w podsieci AKS utworzonej w poprzednim kroku. Pobierz identyfikator tej podsieci przy użyciu [podsieci sieci az:][az-network-vnet-subnet-show]

```azurecli-interactive
az network vnet subnet show --resource-group myResourceGroup --vnet-name myVnet --name myAKSSubnet --query id -o tsv
```

Utwórz klaster AKS za pomocą polecenia [az aks create][az-aks-create]. W poniższym przykładzie pokazano tworzenie klastra o nazwie *myAKSCluster* z jednym węzłem. Zamień `<subnetId>` identyfikator uzyskany w poprzednim `<appId>` kroku, a następnie i `<password>` z wartościami zebranymi w poprzedniej sekcji.

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

Aby włączyć wirtualne węzły, użyj teraz polecenia [az aks enable-addons.][az-aks-enable-addons] W poniższym przykładzie użyto podsieci o nazwie *myVirtualNodeSubnet* utworzonej w poprzednim kroku:

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

Poniższy przykładowy wynik przedstawia pojedynczy węzeł maszyny Wirtualnej utworzony, a następnie węzeł wirtualny dla systemu Linux, *virtual-node-aci-linux:*

```output
NAME                          STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux        Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0      Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Wdrażanie przykładowej aplikacji

Utwórz plik `virtual-node.yaml` o nazwie i skopiuj w następującym pliku YAML. Aby zaplanować kontener w węźle, [zdefiniowano nodeSelector][node-selector] i [tolerancja.][toleration]

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

```console
kubectl apply -f virtual-node.yaml
```

Użyj [polecenia kubectl get zasobników][kubectl-get] z argumentem, `-o wide` aby wyprowadzić listę zasobników i zaplanowanego węzła. Należy zauważyć, `aci-helloworld` że zasobnik `virtual-node-aci-linux` został zaplanowany w węźle.

```console
kubectl get pods -o wide
```

```output
NAME                            READY     STATUS    RESTARTS   AGE       IP           NODE
aci-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Zasobnik jest przypisany wewnętrzny adres IP z podsieci sieci wirtualnej platformy Azure delegowane do użytku z węzłami wirtualnymi.

> [!NOTE]
> Jeśli używasz obrazów przechowywanych w rejestrze kontenerów platformy Azure, [skonfiguruj i użyj klucza tajnego Kubernetes][acr-aks-secrets]. Bieżące ograniczenie węzłów wirtualnych jest, że nie można użyć zintegrowanego uwierzytelniania głównego usługi Azure AD. Jeśli nie używasz klucza tajnego, zasobników zaplanowanych w węzłach `HTTP response status code 400 error code "InaccessibleImage"`wirtualnych nie można uruchomić i zgłosić błąd .

## <a name="test-the-virtual-node-pod"></a>Testowanie zasobnika węzła wirtualnego

Aby przetestować zasobnika uruchomionego w węźle wirtualnym, przejdź do aplikacji demonstracyjnej za pomocą klienta sieci web. Ponieważ zasobnik jest przypisany wewnętrzny adres IP, można szybko przetestować tę łączność z innego zasobnika w klastrze AKS. Utwórz zasobnik testowy i dołącz do niej sesję terminala:

```console
kubectl run --generator=run-pod/v1 -it --rm testvk --image=debian
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

## <a name="remove-virtual-nodes"></a>Usuwanie węzłów wirtualnych

Jeśli nie chcesz już używać węzłów wirtualnych, możesz je wyłączyć za pomocą polecenia [az aks disable-addons.][az aks disable-addons] 

Jeśli to konieczne, przejdź do [https://shell.azure.com](https://shell.azure.com) otwierania usługi Azure Cloud Shell w przeglądarce.

Najpierw usuń `aci-helloworld` zasobnik uruchomiony w węźle wirtualnym:

```console
kubectl delete -f virtual-node.yaml
```

Poniższe przykładowe polecenie wyłącza węzły wirtualne systemu Linux:

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

W tym artykule zasobnik został zaplanowany w węźle wirtualnym i przypisano prywatny, wewnętrzny adres IP. Zamiast tego można utworzyć wdrożenie usługi i kierować ruch do zasobnika za pośrednictwem modułu równoważenia obciążenia lub kontrolera transferu danych przychodzących. Aby uzyskać więcej informacji, zobacz [Tworzenie podstawowego kontrolera transferu danych przychodzących w u.][aks-basic-ingress]

Węzły wirtualne są często jednym ze składników rozwiązania skalowania w uzywce AKS. Aby uzyskać więcej informacji na temat skalowania rozwiązań, zobacz następujące artykuły:

- [Użyj skalowania automatycznego skalowania w poziomie kubernetes][aks-hpa]
- [Korzystanie z skalowania automatycznego klastra Kubernetes][aks-cluster-autoscaler]
- [Zapoznaj się z przykładem skalowania automatycznego dla węzłów wirtualnych][virtual-node-autoscale]
- [Dowiedz się więcej o bibliotece open source Virtual Kubelet][virtual-kubelet-repo]

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
[az-group-create]: /cli/azure/group#az-group-create
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az-network-vnet-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-show
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-enable-addons]: /cli/azure/aks#az-aks-enable-addons
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks disable-addons]: /cli/azure/aks#az-aks-disable-addons
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider#az-provider-register
