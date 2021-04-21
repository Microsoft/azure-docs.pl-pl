---
title: Tworzenie kontenera systemu Windows Server w klastrze usługi AKS przy użyciu interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak szybko utworzyć klaster Kubernetes i wdrożyć aplikację w kontenerze systemu Windows Server w usłudze Azure Kubernetes Service (AKS) przy użyciu interfejsu wiersza polecenia platformy Azure.
services: container-service
ms.topic: article
ms.date: 07/16/2020
ms.openlocfilehash: 617590a3f482e246b8af5db6dd906591c16b20fa
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769431"
---
# <a name="create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>Tworzenie kontenera systemu Windows Server w klastrze usługi Azure Kubernetes Service (AKS) przy użyciu interfejsu wiersza polecenia platformy Azure

Azure Kubernetes Service (AKS) to zarządzana usługa platformy Kubernetes, która umożliwia szybkie wdrażanie klastrów i zarządzanie nimi. W tym artykule wdrożysz klaster usługi AKS przy użyciu interfejsu wiersza polecenia platformy Azure. Możesz również wdrożyć ASP.NET przykładowej aplikacji w kontenerze systemu Windows Server w klastrze.

![Obraz przeglądania do ASP.NET przykładowej aplikacji](media/windows-container/asp-net-sample-app.png)

W tym artykule założono, że rozumiesz pojęcia związane z platformą Kubernetes. Aby uzyskać więcej informacji, zobacz [Podstawowe pojęcia dotyczące usługi Azure Kubernetes Service (AKS)][kubernetes-concepts].

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

### <a name="limitations"></a>Ograniczenia

Podczas tworzenia klastrów usługi AKS, które obsługują wiele pul węzłów i zarządzają nimi, obowiązują następujące ograniczenia:

* Nie można usunąć pierwszej puli węzłów.

Następujące dodatkowe ograniczenia dotyczą pul węzłów systemu Windows Server:

* Klaster usługi AKS może mieć maksymalnie 10 pul węzłów.
* Klaster usługi AKS może mieć maksymalnie 100 węzłów w każdej puli węzłów.
* Nazwa puli węzłów systemu Windows Server ma limit 6 znaków.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczna grupa przeznaczona do wdrażania zasobów platformy Azure i zarządzania nimi. Podczas tworzenia grupy zasobów użytkownik jest proszony o określenie lokalizacji. W tej lokalizacji są przechowywane metadane grupy zasobów, a zasoby są uruchamiane na platformie Azure, jeśli nie określisz innego regionu podczas tworzenia zasobów. Utwórz grupę zasobów przy użyciu polecenia [az group create][az-group-create].

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

> [!NOTE]
> W tym artykule do poleceń w tym samouczku jest używana składnia powłoki Bash.
> Jeśli używasz usługi Azure Cloud Shell, upewnij się, że lista rozwijana w lewym górnym rogu okna Cloud Shell jest ustawiona na **bash.**

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

Aby uruchomić klaster AKS obsługujący pule węzłów dla kontenerów systemu Windows Server, klaster musi używać zasad sieciowych, które [używają Azure CNI][azure-cni-about] (zaawansowanej) wtyczki sieciowej. Aby uzyskać bardziej szczegółowe informacje pomocne w planowaniu wymaganych zakresów podsieci i kwestii dotyczących sieci, zobacz [konfigurowanie Azure CNI sieci.][use-advanced-networking] Użyj polecenia [az aks create,][az-aks-create] aby utworzyć klaster usługi AKS o *nazwie myAKSCluster.* To polecenie spowoduje utworzenie niezbędnych zasobów sieciowych, jeśli nie istnieją.

* Klaster jest skonfigurowany z dwoma węzłami.
* Parametry i ustawiają poświadczenia administratora dla dowolnych kontenerów systemu Windows Server utworzonych w klastrze i `--windows-admin-password` `--windows-admin-username` muszą spełniać wymagania dotyczące haseł systemu [Windows Server.][windows-server-password] Jeśli nie określisz parametru *windows-admin-password,* zostanie wyświetlony monit o podanie wartości.
* Pula węzłów `VirtualMachineScaleSets` używa .

> [!NOTE]
> Aby zapewnić niezawodne działanie klastra, należy uruchomić co najmniej 2 (dwa) węzły w domyślnej puli węzłów.

Utwórz nazwę użytkownika do użycia jako poświadczenia administratora dla kontenerów systemu Windows Server w klastrze. Następujące polecenia monitują o nazwę użytkownika i ustawiają ją WINDOWS_USERNAME do użycia w późniejszym poleceniu (pamiętaj, że polecenia w tym artykule są wprowadzane do powłoki BASH).

```azurecli-interactive
echo "Please enter the username to use as administrator credentials for Windows Server containers on your cluster: " && read WINDOWS_USERNAME
```

Utwórz klaster, określając parametr `--windows-admin-username` . Następujące przykładowe polecenie tworzy klaster przy użyciu wartości *WINDOWS_USERNAME* ustawionej w poprzednim poleceniu. Alternatywnie możesz podać inną nazwę użytkownika bezpośrednio w parametrze zamiast używać *WINDOWS_USERNAME*. Następujące polecenie wyświetli również monit o utworzenie hasła dla poświadczeń administratora dla kontenerów systemu Windows Server w klastrze. Alternatywnie możesz użyć parametru *windows-admin-password* i określić w tym miejscu własną wartość.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --enable-addons monitoring \
    --generate-ssh-keys \
    --windows-admin-username $WINDOWS_USERNAME \
    --vm-set-type VirtualMachineScaleSets \
    --network-plugin azure
```

> [!NOTE]
> Jeśli wystąpi błąd weryfikacji hasła, sprawdź, czy ustawione hasło spełnia wymagania [dotyczące hasła systemu Windows Server.][windows-server-password] Jeśli hasło spełnia wymagania, spróbuj utworzyć grupę zasobów w innym regionie. Następnie spróbuj utworzyć klaster z nową grupą zasobów.

Po kilku minutach polecenie zostanie wykonane i zwróci informacje o klastrze w formacie JSON. Czasami aprowizowanie klastra może potrwać dłużej niż kilka minut. W takich przypadkach odzwytuj do 10 minut.

## <a name="add-a-windows-server-node-pool"></a>Dodawanie puli węzłów systemu Windows Server

Domyślnie klaster usługi AKS jest tworzony z pulą węzłów, w których można uruchamiać kontenery systemu Linux. Użyj `az aks nodepool add` polecenia , aby dodać dodatkową pulę węzłów, która może uruchamiać kontenery systemu Windows Server obok puli węzłów systemu Linux.

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1
```

Powyższe polecenie tworzy nową pulę węzłów o nazwie *npwin* i dodaje ją do *klastra myAKSCluster.* Podczas tworzenia puli węzłów do uruchamiania kontenerów systemu Windows Server wartością domyślną rozmiaru *węzła-maszyny wirtualnej* *jest Standard_D2s_v3*. Jeśli zdecydujesz się ustawić parametr *node-vm-size,* sprawdź listę [ograniczonych rozmiarów maszyn wirtualnych.][restricted-vm-sizes] Minimalny zalecany rozmiar to *Standard_D2s_v3*. Powyższe polecenie używa również domyślnej podsieci w domyślnej sieci wirtualnej utworzonej podczas uruchamiania polecenia `az aks create` .

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

Poniższe przykładowe dane wyjściowe pokazują wszystkie węzły w klastrze. Upewnij się, że stan wszystkich węzłów to *Gotowe:*

```output
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.16.9
aksnpwin987654                      Ready    agent   108s   v1.16.9
```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Plik manifestu platformy Kubernetes definiuje żądany stan klastra, w tym informacje o obrazach kontenera do uruchomienia. W tym artykule manifest służy do tworzenia wszystkich obiektów potrzebnych do uruchomienia ASP.NET przykładowej aplikacji w kontenerze systemu Windows Server. Ten manifest obejmuje wdrożenie [rozwiązania Kubernetes][kubernetes-deployment] dla przykładowej ASP.NET oraz zewnętrzną usługę [Kubernetes][kubernetes-service] w celu uzyskania dostępu do aplikacji z Internetu.

Przykładowa ASP.NET jest dostarczana jako część przykładów .NET Framework [i][dotnet-samples] uruchamiana w kontenerze systemu Windows Server. Usługa AKS wymaga, aby kontenery systemu Windows Server bazowały na *obrazach systemu Windows Server 2019* lub jego wersji. Plik manifestu kubernetes musi [][node-selector] również zdefiniować selektor węzłów, aby poinformować klaster usługi AKS o uruchomieniu zasobnika przykładowej aplikacji usługi ASP.NET w węźle, w którym można uruchamiać kontenery systemu Windows Server.

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

Następujące przykładowe dane wyjściowe pokazują pomyślnie utworzone wdrożenie i usługę:

```output
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>Testowanie aplikacji

Podczas uruchamiania aplikacji usługa Kubernetes uwidacznia fronton aplikacji w Internecie. Ten proces może potrwać kilka minut. Czasami aprowizowania usługi może potrwać dłużej niż kilka minut. W takich przypadkach odzwyć do 10 minut.

Aby monitorować postęp, użyj polecenia [kubectl get-service][kubectl-get] z argumentem `--watch`.

```console
kubectl get service sample --watch
```

Początkowo adres *EXTERNAL-IP* dla *przykładowej usługi* jest wyświetlany *jako* oczekujący .

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Gdy dla adresu *EXTERNAL-IP* wartość *oczekujący* zmieni się na rzeczywisty publiczny adres IP, naciśnij klawisze `CTRL-C`, aby zatrzymać proces śledzenia narzędzia `kubectl`. Następujące przykładowe dane wyjściowe przedstawiają prawidłowy publiczny adres IP przypisany do usługi:

```output
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Aby zobaczyć, jak działa przykładowa aplikacja, otwórz w przeglądarce internetowej zewnętrzny adres IP usługi.

![Obraz przeglądania do ASP.NET przykładowej aplikacji](media/windows-container/asp-net-sample-app.png)

> [!Note]
> Jeśli podczas próby załadowania strony zostanie wyświetlony limit czasu połączenia, sprawdź, czy przykładowa aplikacja jest gotowa, korzystając z następującego polecenia [kubectl get pods --watch]. Czasami kontener systemu Windows nie zostanie uruchomiony do czasu, gdy zewnętrzny adres IP będzie dostępny.

## <a name="delete-cluster"></a>Usuwanie klastra

Gdy klaster nie będzie już potrzebny, usuń grupę zasobów, usługę kontenera i wszystkie pokrewne zasoby za pomocą polecenia [az group delete][az-group-delete].

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Po usunięciu klastra jednostka usługi Azure Active Directory używana przez klaster usługi AKS nie jest usuwana. Aby sprawdzić, jak usunąć jednostkę usługi, zobacz [AKS service principal considerations and deletion (Uwagi dotyczące jednostki usługi AKS i jej usuwanie)][sp-delete]. Jeśli używasz tożsamości zarządzanej, tożsamość jest zarządzana przez platformę i nie wymaga usunięcia.

## <a name="next-steps"></a>Następne kroki

W tym artykule wdrożono klaster Kubernetes i wdrożono w nim ASP.NET przykładową aplikację w kontenerze systemu Windows Server. [Uzyskaj dostęp do internetowego pulpitu nawigacyjnego rozwiązania Kubernetes][kubernetes-dashboard] dla właśnie utworzonego klastra.

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
[aks-monitor]: ../azure-monitor/containers/container-insights-onboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks#az_aks_browse
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[az-provider-register]: /cli/azure/provider#az_provider_register
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cni-about]: concepts-network.md#azure-cni-advanced-networking
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[restricted-vm-sizes]: quotas-skus-regions.md#restricted-vm-sizes
[use-advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[windows-server-password]: /windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements#reference