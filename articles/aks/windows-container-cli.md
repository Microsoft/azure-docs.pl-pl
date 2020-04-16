---
title: Uruchamianie kontenera systemu Windows Server w klastrze usługi Kubernetes platformy Azure
description: Dowiedz się, jak szybko utworzyć klaster Kubernetes, wdrożyć aplikację w kontenerze systemu Windows Server w usłudze Azure Kubernetes (AKS) przy użyciu interfejsu wiersza polecenia platformy Azure.
services: container-service
ms.topic: article
ms.date: 01/27/2020
ms.openlocfilehash: 2aecebcc45cb24c9ab3a594aa4d74b1584c7ffa7
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392660"
---
# <a name="preview---create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>Podgląd — tworzenie kontenera systemu Windows Server w klastrze usługi Kubernetes platformy Azure (AKS) przy użyciu interfejsu wiersza polecenia platformy Azure

Azure Kubernetes Service (AKS) to zarządzana usługa platformy Kubernetes, która umożliwia szybkie wdrażanie klastrów i zarządzanie nimi. W tym artykule można wdrożyć klaster AKS przy użyciu interfejsu wiersza polecenia platformy Azure. Można również wdrożyć ASP.NET przykładową aplikację w kontenerze systemu Windows Server do klastra.

Ta funkcja jest obecnie dostępna w wersji zapoznawczej.

![Obraz przeglądania w celu ASP.NET przykładowej aplikacji](media/windows-container/asp-net-sample-app.png)

W tym artykule przyjęto podstawowe zrozumienie pojęć kubernetes. Aby uzyskać więcej informacji, zobacz [Podstawowe pojęcia dotyczące usługi Azure Kubernetes Service (AKS)][kubernetes-concepts].

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i używać interfejsu wiersza polecenia lokalnie, ten artykuł wymaga, aby uruchomić interfejsu wiersza polecenia platformy Azure w wersji 2.0.61 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="before-you-begin"></a>Przed rozpoczęciem

Po utworzeniu klastra, który może uruchamiać kontenery systemu Windows Server, należy dodać dodatkową pulę węzłów. Dodanie dodatkowej puli węzłów jest omówione w późniejszym kroku, ale najpierw należy włączyć kilka funkcji w wersji zapoznawczej.

> [!IMPORTANT]
> Funkcje podglądu usługi AKS są samoobsługowe. Wersje zapoznawcza są dostarczane w stanie "tak jak jest" i "w miarę dostępności" i są wyłączone z umów o gwarantowanym poziomie usług i ograniczonej gwarancji. Podglądy AKS są częściowo objęte obsługą klienta na podstawie najlepszych starań. W związku z tym te funkcje nie są przeznaczone do użytku produkcyjnego. Aby uzyskać dodatkowe infromation, zobacz następujące artykuły pomocy technicznej:
>
> * [Zasady wsparcia AKS][aks-support-policies]
> * [Często zadawane pytania dotyczące pomocy technicznej platformy Azure][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>Instalowanie rozszerzenia interfejsu wiersza polecenia aks-preview

Aby korzystać z kontenerów systemu Windows Server, potrzebujesz rozszerzenia interfejsu wiersza polecenia *aks-preview* w wersji 0.4.12 lub nowszej. Zainstaluj rozszerzenie interfejsu wiersza polecenia interfejsu wiersza polecenia platformy Azure *aks-preview* przy użyciu polecenia [dodawania rozszerzenia az,][az-extension-add] a następnie sprawdź dostępność dostępnych aktualizacji za pomocą polecenia [aktualizacji rozszerzenia az::][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-windows-preview-feature"></a>Rejestrowanie funkcji podglądu systemu Windows

Aby utworzyć klaster AKS, który może używać wielu pul węzłów i uruchamiać kontenery systemu Windows Server, należy najpierw włączyć flagi funkcji *WindowsPreview* w ramach subskrypcji. Funkcja *WindowsPreview* używa również klastrów puli wielu węzłów i skalowania maszyny wirtualnej ustawionej do zarządzania wdrażaniem i konfiguracją węzłów Kubernetes. Zarejestruj flagę funkcji *WindowsPreview* przy użyciu polecenia [az feature register,][az-feature-register] jak pokazano w poniższym przykładzie:

```azurecli-interactive
az feature register --name WindowsPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> Każdy klaster AKS utworzony po pomyślnym zarejestrowaniu flagi funkcji *WindowsPreview* używa tego środowiska klastra w wersji zapoznawczej. Aby nadal tworzyć regularne, w pełni obsługiwane klastry, nie włączaj funkcji w wersji zapoznawczej w subskrypcjach produkcyjnych. Użyj oddzielnego testu lub dewelopera subskrypcji platformy Azure do testowania funkcji w wersji zapoznawczej.

Rejestracja zajmuje kilka minut. Sprawdź stan rejestracji za pomocą polecenia [az feature list:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/WindowsPreview')].{Name:name,State:properties.state}"
```

Gdy jest `Registered`stan rejestracji , naciśnij klawisz Ctrl-C, aby zatrzymać monitorowanie stanu.  Następnie odśwież rejestrację dostawcy zasobów *Microsoft.ContainerService* za pomocą polecenia [rejestru az provider:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>Ograniczenia

Podczas tworzenia klastrów AKS i zarządzania nimi obowiązują następujące ograniczenia:

* Nie można usunąć pierwszej puli węzłów.

Gdy ta funkcja jest w wersji zapoznawczej, obowiązują następujące dodatkowe ograniczenia:

* Klaster AKS może mieć maksymalnie osiem pul węzłów.
* Klaster AKS może mieć maksymalnie 400 węzłów w tych ośmiu pulach węzłów.
* Nazwa puli węzłów systemu Windows Server ma limit 6 znaków.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczna grupa przeznaczona do wdrażania zasobów platformy Azure i zarządzania nimi. Podczas tworzenia grupy zasobów użytkownik jest proszony o określenie lokalizacji. Ta lokalizacja jest, gdzie są przechowywane metadane grupy zasobów, jest również tam, gdzie zasoby są uruchamiane na platformie Azure, jeśli nie określisz innego regionu podczas tworzenia zasobów. Utwórz grupę zasobów przy użyciu polecenia [az group create][az-group-create].

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

> [!NOTE]
> W tym artykule użyto składni Bash dla poleceń w tym samouczku.
> Jeśli używasz usługi Azure Cloud Shell, upewnij się, że rozwijana w lewym górnym rogu okna powłoki chmury jest ustawiona na **Bash**.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Następujące przykładowe dane wyjściowe przedstawiają pomyślnie utworzoną grupę zasobów:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

## <a name="create-an-aks-cluster"></a>Tworzenie klastra AKS

Aby uruchomić klaster AKS obsługujący pule węzłów kontenerów systemu Windows Server, klaster musi używać zasad sieciowych korzystających z wtyczki sieciowej [Azure CNI][azure-cni-about] (zaawansowane). Aby uzyskać bardziej szczegółowe informacje ułatwiające planowanie wymaganych zakresów podsieci i zagadnień dotyczących sieci, zobacz [konfigurowanie sieci CNI platformy Azure][use-advanced-networking]. Polecenie [az aks create][az-aks-create] służy do tworzenia klastra AKS o nazwie *myAKSCluster*. To polecenie utworzy niezbędne zasoby sieciowe, jeśli nie istnieją.
  * Klaster jest skonfigurowany z dwoma węzłami
  * Parametry *windows-admin-password* i *windows-admin-username* ustawiają poświadczenia administratora dla wszystkich kontenerów systemu Windows Server utworzonych w klastrze.

> [!NOTE]
> Aby upewnić się, że klaster działa niezawodnie, należy uruchomić co najmniej 2 (dwa) węzły w domyślnej puli węzłów.

Podaj własne bezpieczne *PASSWORD_WIN* (pamiętaj, że polecenia w tym artykule są wprowadzane do powłoki BASH):

```azurecli-interactive
PASSWORD_WIN="P@ssw0rd1234"

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --enable-addons monitoring \
    --kubernetes-version 1.15.7 \
    --generate-ssh-keys \
    --windows-admin-password $PASSWORD_WIN \
    --windows-admin-username azureuser \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --network-plugin azure
```

> [!Note]
> Jeśli zostanie wyświetlony błąd sprawdzania poprawności hasła, spróbuj utworzyć grupę zasobów w innym regionie.
> Następnie spróbuj utworzyć klaster z nową grupą zasobów.

> [!Note]
> Jeśli nie można utworzyć klastra AKS, ponieważ wersja nie jest obsługiwana w tym regionie, możesz użyć polecenia [az aks get-versions --location eastus], aby znaleźć obsługiwaną listę wersji dla tego regionu.


Po kilku minutach polecenie zostanie wykonane i zwróci informacje o klastrze w formacie JSON. Od czasu do czasu klaster może potrwać dłużej niż kilka minut do aprowizowania. W takich przypadkach odczekać do 10 minut. 

## <a name="add-a-windows-server-node-pool"></a>Dodawanie puli węzłów systemu Windows Server

Domyślnie klaster AKS jest tworzony z puli węzłów, które można uruchomić kontenery systemu Linux. Użyj `az aks nodepool add` polecenia, aby dodać dodatkową pulę węzłów, która może uruchamiać kontenery systemu Windows Server.

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1 \
    --kubernetes-version 1.15.7
```

Powyższe polecenie tworzy nową pulę węzłów o nazwie *npwin* i dodaje ją do *myAKSCluster*. Podczas tworzenia puli węzłów do uruchamiania kontenerów systemu Windows Server domyślną wartością *dla rozmiaru węzła-maszyny wirtualnej* jest *Standard_D2s_v3*. Jeśli zdecydujesz się ustawić parametr *rozmiaru węzła-vm,* sprawdź listę [ograniczonych rozmiarów maszyn wirtualnych][restricted-vm-sizes]. Minimalny zalecany rozmiar to *Standard_D2s_v3*. Powyższe polecenie używa również domyślnej podsieci w `az aks create`domyślnej sieci wirtualnej utworzonej podczas uruchamiania .

## <a name="connect-to-the-cluster"></a>Łączenie z klastrem

Aby zarządzać klastrem Kubernetes, należy użyć klienta wiersza polecenia usługi Kubernetes — narzędzia [kubectl][kubectl]. Jeśli korzystasz z usługi Azure Cloud Shell, narzędzie `kubectl` jest już zainstalowane. Aby zainstalować narzędzie `kubectl` lokalnie, użyj polecenia [az aks install-cli][az-aks-install-cli]:

```azurecli
az aks install-cli
```

Aby skonfigurować narzędzie `kubectl` w celu nawiązania połączenia z klastrem Kubernetes, użyj polecenia [az aks get-credentials][az-aks-get-credentials]. To polecenie powoduje pobranie poświadczeń i zastosowanie ich w konfiguracji interfejsu wiersza polecenia Kubernetes.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Aby sprawdzić połączenie z klastrem, użyj polecenia [kubectl get][kubectl-get], aby powrócić do listy węzłów klastra.

```console
kubectl get nodes
```

Poniższe przykładowe dane wyjściowe pokazuje wszystkie węzły w klastrze. Upewnij się, że stan wszystkich węzłów jest *gotowy:*

```output
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.15.7
aksnpwin987654                      Ready    agent   108s   v1.15.7
```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Plik manifestu platformy Kubernetes definiuje żądany stan klastra, w tym informacje o obrazach kontenera do uruchomienia. W tym artykule manifest jest używany do tworzenia wszystkich obiektów potrzebnych do uruchomienia ASP.NET przykładowej aplikacji w kontenerze systemu Windows Server. Ten manifest obejmuje [wdrożenie Kubernetes][kubernetes-deployment] dla ASP.NET przykładowej aplikacji i zewnętrzną [usługę Kubernetes,][kubernetes-service] aby uzyskać dostęp do aplikacji z Internetu.

Przykładowa aplikacja ASP.NET jest dostarczana jako część [przykładów programu .NET Framework][dotnet-samples] i uruchamiana w kontenerze systemu Windows Server. Usługa AKS wymaga, aby kontenery systemu Windows Server były oparte na obrazach *systemu Windows Server 2019* lub nowszych. Plik manifestu Kubernetes musi również zdefiniować [selektor węzłów,][node-selector] aby poinformować klaster AKS o uruchomieniu zasobnika ASP.NET przykładowej aplikacji w węźle, który może uruchamiać kontenery systemu Windows Server.

Utwórz plik o nazwie `sample.yaml` i skopiuj go do poniższej definicji YAML. Jeśli używasz usługi Azure Cloud Shell, ten plik można utworzyć przy użyciu programu `vi` lub `nano`, tak jak podczas pracy w systemie wirtualnym lub fizycznym:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
  labels:
    app: sample
spec:
  replicas: 1
  template:
    metadata:
      name: sample
      labels:
        app: sample
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": windows
      containers:
      - name: sample
        image: mcr.microsoft.com/dotnet/framework/samples:aspnetapp
        resources:
          limits:
            cpu: 1
            memory: 800M
          requests:
            cpu: .1
            memory: 300M
        ports:
          - containerPort: 80
  selector:
    matchLabels:
      app: sample
---
apiVersion: v1
kind: Service
metadata:
  name: sample
spec:
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 80
  selector:
    app: sample
```

Wdróż aplikację przy użyciu polecenia [kubectl apply][kubectl-apply] i podaj nazwę manifestu YAML:

```console
kubectl apply -f sample.yaml
```

Poniższy przykładowy wynik przedstawia wdrożenie i usługę utworzone pomyślnie:

```output
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>Testowanie aplikacji

Podczas uruchamiania aplikacji usługa Kubernetes uwidacznia fronton aplikacji w Internecie. Ten proces może potrwać kilka minut. Od czasu do czasu usługa może potrwać dłużej niż kilka minut do udostępnienia. W takich przypadkach odczekać do 10 minut.

Aby monitorować postęp, użyj polecenia [kubectl get-service][kubectl-get] z argumentem `--watch`.

```console
kubectl get service sample --watch
```

Początkowo *EXTERNAL-IP* dla *przykładowej* usługi jest wyświetlany jako *oczekujący*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Gdy dla adresu *EXTERNAL-IP* wartość *oczekujący* zmieni się na rzeczywisty publiczny adres IP, naciśnij klawisze `CTRL-C`, aby zatrzymać proces śledzenia narzędzia `kubectl`. Następujące przykładowe dane wyjściowe przedstawiają prawidłowy publiczny adres IP przypisany do usługi:

```output
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Aby wyświetlić przykładową aplikację w akcji, otwórz przeglądarkę internetową na zewnętrzny adres IP usługi.

![Obraz przeglądania w celu ASP.NET przykładowej aplikacji](media/windows-container/asp-net-sample-app.png)

> [!Note]
> Jeśli podczas próby załadowania strony zostanie wyświetlony limit czasu połączenia, należy sprawdzić, czy przykładowa aplikacja jest gotowa za pomocą następującego polecenia [kubectl get zasobników --watch]. Czasami kontener systemu Windows nie zostanie uruchomiony do czasu, gdy zewnętrzny adres IP jest dostępny.

## <a name="delete-cluster"></a>Usuwanie klastra

Gdy klaster nie będzie już potrzebny, usuń grupę zasobów, usługę kontenera i wszystkie pokrewne zasoby za pomocą polecenia [az group delete][az-group-delete].

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Po usunięciu klastra jednostka usługi Azure Active Directory używana przez klaster usługi AKS nie jest usuwana. Aby sprawdzić, jak usunąć jednostkę usługi, zobacz [AKS service principal considerations and deletion (Uwagi dotyczące jednostki usługi AKS i jej usuwanie)][sp-delete]. Jeśli użyto tożsamości zarządzanej, tożsamość jest zarządzana przez platformę i nie wymaga usuwania.

## <a name="next-steps"></a>Następne kroki

W tym artykule wdrożono klaster Kubernetes i wdrożono ASP.NET przykładową aplikację w kontenerze systemu Windows Server. [Uzyskaj dostęp do internetowego pulpitu nawigacyjnego rozwiązania Kubernetes][kubernetes-dashboard] dla właśnie utworzonego klastra.

Aby dowiedzieć się więcej o usłudze AKS i poznać dokładnie proces od kompletnego kodu do wdrożenia, przejdź do samouczka dotyczącego klastra Kubernetes.

> [!div class="nextstepaction"]
> [Samouczek usługi AKS][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: https://aka.ms/coingfonboarding
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks?view=azure-cli-latest#az-aks-browse
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-provider-register]: /cli/azure/provider#az-provider-register
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cni-about]: concepts-network.md#azure-cni-advanced-networking
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[restricted-vm-sizes]: quotas-skus-regions.md#restricted-vm-sizes
[use-advanced-networking]: configure-advanced-networking.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
