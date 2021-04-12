---
title: 'Szybki Start: Wdrażanie klastra AKS przy użyciu interfejsu wiersza polecenia platformy Azure'
description: Dowiedz się, jak szybko utworzyć klaster Kubernetes, wdrożyć aplikację i monitorować wydajność w usłudze Azure Kubernetes Service (AKS) przy użyciu interfejsu wiersza polecenia platformy Azure.
services: container-service
ms.topic: quickstart
ms.date: 02/26/2021
ms.custom:
- H1Hack27Feb2017
- mvc
- devcenter
- seo-javascript-september2019
- seo-javascript-october2019
- seo-python-october2019
- devx-track-azurecli
- contperf-fy21q1
ms.openlocfilehash: 8adfd1a6e26a3381653ca9a794b124e201b9d481
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106106742"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-cluster-using-the-azure-cli"></a>Szybki Start: Wdrażanie klastra usługi Azure Kubernetes Service przy użyciu interfejsu wiersza polecenia platformy Azure

Azure Kubernetes Service (AKS) to zarządzana usługa platformy Kubernetes, która umożliwia szybkie wdrażanie klastrów i zarządzanie nimi. W tym przewodniku szybki start:
* Wdróż klaster AKS przy użyciu interfejsu wiersza polecenia platformy Azure. 
* Uruchom aplikację wielokontenerową z frontonem sieci Web i wystąpieniem Redis w klastrze. 
* Monitoruj kondycję klastra i zasobników z uruchomioną aplikacją.

  ![Aplikacja do głosowania wdrożona w usłudze Azure Kubernetes Service](./media/container-service-kubernetes-walkthrough/voting-app-deployed-in-azure-kubernetes-service.png)

W tym przewodniku Szybki start założono, że masz podstawową wiedzę na temat pojęć związanych z rozwiązaniem Kubernetes. Aby uzyskać więcej informacji, zobacz [Podstawowe pojęcia dotyczące usługi Azure Kubernetes Service (AKS)][kubernetes-concepts].

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Aby dowiedzieć się więcej na temat tworzenia puli węzłów systemu Windows Server, zobacz [Tworzenie klastra AKS, który obsługuje kontenery systemu Windows Server][windows-container-cli].

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga wersji 2.0.64 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

> [!NOTE]
> Uruchom polecenia jako administrator, jeśli planujesz uruchamiać polecenia w tym przewodniku szybki start lokalnie, a nie w Azure Cloud Shell.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

[Grupa zasobów platformy Azure](../azure-resource-manager/management/overview.md) to grupa logiczna, w której zasoby platformy Azure są wdrażane i zarządzane. Podczas tworzenia grupy zasobów zostanie wyświetlony monit o określenie lokalizacji. Ta lokalizacja: 
* Lokalizacja przechowywania metadanych grupy zasobów.
* Miejsce, w którym zasoby będą działać na platformie Azure, jeśli nie określisz innego regionu podczas tworzenia zasobu. 

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

Utwórz grupę zasobów przy użyciu polecenia [az group create][az-group-create].


```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Dane wyjściowe dla pomyślnie utworzonej grupy zasobów:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="enable-cluster-monitoring"></a>Włączanie monitorowania klastra

1. Sprawdź, czy w Twojej subskrypcji zarejestrowano *Microsoft. OperationsManagement* i *Microsoft. OperationalInsights* . Aby sprawdzić stan rejestracji:

    ```azurecli
    az provider show -n Microsoft.OperationsManagement -o table
    az provider show -n Microsoft.OperationalInsights -o table
    ```
 
    Jeśli nie są zarejestrowane, zarejestruj *Microsoft. OperationsManagement* i *Microsoft. OperationalInsights* przy użyciu:
 
    ```azurecli
    az provider register --namespace Microsoft.OperationsManagement
    az provider register --namespace Microsoft.OperationalInsights
    ```

2. Włącz [Azure monitor dla kontenerów][azure-monitor-containers] za pomocą parametru *monitorowania--Enable-enableds* . 

## <a name="create-aks-cluster"></a>Tworzenie klastra AKS

Utwórz klaster AKS za pomocą polecenia [AZ AKS Create][az-aks-create] . W poniższym przykładzie tworzony jest klaster o nazwie *myAKSCluster* z jednym węzłem: 

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --node-count 1 --enable-addons monitoring --generate-ssh-keys
```

Po kilku minutach polecenie zostanie wykonane i zwróci informacje o klastrze w formacie JSON.

> [!NOTE]
> Podczas tworzenia klastra AKS zostaje automatycznie utworzona druga grupa zasobów do przechowywania zasobów AKS. Aby uzyskać więcej informacji, zobacz [dlaczego dwie grupy zasobów utworzono za pomocą AKS?](./faq.md#why-are-two-resource-groups-created-with-aks)

## <a name="connect-to-the-cluster"></a>Łączenie z klastrem

Aby zarządzać klastrem Kubernetes, użyj klienta wiersza polecenia Kubernetes [polecenia kubectl][kubectl]. `kubectl` jest już zainstalowany, jeśli używasz Azure Cloud Shell. 

1. Zainstaluj `kubectl` lokalnie za pomocą polecenia [AZ AKS Install-CLI][az-aks-install-cli] :

    ```azurecli
    az aks install-cli
    ```

2. Skonfiguruj `kubectl` , aby nawiązać połączenie z klastrem Kubernetes za pomocą polecenia [AZ AKS Get-Credentials][az-aks-get-credentials] . Następujące polecenie:  
      * Pobiera poświadczenia i konfiguruje interfejs wiersza polecenia Kubernetes, aby móc z nich korzystać.
      * `~/.kube/config`Program używa domyślnej lokalizacji [pliku konfiguracji Kubernetes][kubeconfig-file]. Określ inną lokalizację pliku konfiguracji Kubernetes za pomocą polecenia *--File*.


    ```azurecli-interactive
    az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
    ```

3. Sprawdź połączenie z klastrem za pomocą polecenia [polecenia kubectl Get][kubectl-get] . To polecenie zwraca listę węzłów klastra.

    ```azurecli-interactive
    kubectl get nodes
    ```

    Dane wyjściowe pokazują pojedynczy węzeł utworzony w poprzednich krokach. Upewnij się, że stan węzła jest *gotowy*:

    ```output
    NAME                       STATUS   ROLES   AGE     VERSION
    aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.12.8
    ```

## <a name="run-the-application"></a>Uruchamianie aplikacji

[Plik manifestu Kubernetes][kubernetes-deployment] definiuje żądany stan klastra, taki jak obrazy kontenerów do uruchomienia. 

W tym przewodniku szybki start użyjesz manifestu, aby utworzyć wszystkie obiekty potrzebne do uruchomienia [aplikacji do głosowania na platformie Azure][azure-vote-app]. Ten manifest obejmuje dwie [Kubernetes wdrożenia][kubernetes-deployment]:
* Przykładowe aplikacje języka Python na platformie Azure.
* Wystąpienie Redis. 

Tworzone są również dwie [usługi Kubernetes][kubernetes-service] :
* Usługa wewnętrzna dla wystąpienia usługi Redis.
* Zewnętrzna usługa do uzyskiwania dostępu do aplikacji do głosowania platformy Azure z Internetu.

1. Utwórz plik o nazwie `azure-vote.yaml`.
    * Jeśli używasz Azure Cloud Shell, ten plik można utworzyć przy użyciu `code` , `vi` lub `nano` w przypadku pracy w systemie wirtualnym lub fizycznym.
1. Kopiuj w następującej definicji YAML:

    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: azure-vote-back
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: azure-vote-back
      template:
        metadata:
          labels:
            app: azure-vote-back
        spec:
          nodeSelector:
            "beta.kubernetes.io/os": linux
          containers:
          - name: azure-vote-back
            image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
            env:
            - name: ALLOW_EMPTY_PASSWORD
              value: "yes"
            resources:
              requests:
                cpu: 100m
                memory: 128Mi
              limits:
                cpu: 250m
                memory: 256Mi
            ports:
            - containerPort: 6379
              name: redis
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: azure-vote-back
    spec:
      ports:
      - port: 6379
      selector:
        app: azure-vote-back
    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: azure-vote-front
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: azure-vote-front
      template:
        metadata:
          labels:
            app: azure-vote-front
        spec:
          nodeSelector:
            "beta.kubernetes.io/os": linux
          containers:
          - name: azure-vote-front
            image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
            resources:
              requests:
                cpu: 100m
                memory: 128Mi
              limits:
                cpu: 250m
                memory: 256Mi
            ports:
            - containerPort: 80
            env:
            - name: REDIS
              value: "azure-vote-back"
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: azure-vote-front
    spec:
      type: LoadBalancer
      ports:
      - port: 80
      selector:
        app: azure-vote-front
    ```

1. Wdróż aplikację przy użyciu polecenia [kubectl apply][kubectl-apply] i podaj nazwę manifestu YAML:

    ```console
    kubectl apply -f azure-vote.yaml
    ```

    W danych wyjściowych przedstawiono pomyślnie utworzone wdrożenia i usługi:

    ```output
    deployment "azure-vote-back" created
    service "azure-vote-back" created
    deployment "azure-vote-front" created
    service "azure-vote-front" created
    ```

## <a name="test-the-application"></a>Testowanie aplikacji

Podczas uruchamiania aplikacji usługa Kubernetes uwidacznia fronton aplikacji w Internecie. Ten proces może potrwać kilka minut.

Monitoruj postęp przy użyciu polecenia [polecenia kubectl Get Service][kubectl-get] z `--watch` argumentem.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

Zewnętrzne dane wyjściowe **IP** dla `azure-vote-front` usługi będą początkowo wyświetlane jako *oczekujące*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Gdy **zewnętrzny adres IP** zmieni się z *oczekujące* na rzeczywisty publiczny adres IP, użyj, `CTRL-C` Aby zatrzymać `kubectl` proces obserwacji. Następujące przykładowe dane wyjściowe przedstawiają prawidłowy publiczny adres IP przypisany do usługi:

```output
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Aby wyświetlić działającą aplikację Azure Vote, otwórz zewnętrzny adres IP usługi w przeglądarce internetowej.

![Aplikacja do głosowania wdrożona w usłudze Azure Kubernetes Service](./media/container-service-kubernetes-walkthrough/voting-app-deployed-in-azure-kubernetes-service.png)

Przejrzyj metryki kondycji węzłów klastra "i" w obszarze "przechwycone przez [Azure monitor dla kontenerów][azure-monitor-containers] w Azure Portal. 

## <a name="delete-the-cluster"></a>Usuwanie klastra

Aby uniknąć naliczania opłat za platformę Azure, Oczyść niepotrzebne zasoby. Usuń grupę zasobów, usługę kontenera i wszystkie powiązane zasoby za pomocą polecenia [az group delete][az-group-delete].

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Po usunięciu klastra jednostka usługi Azure Active Directory używana przez klaster usługi AKS nie jest usuwana. Aby sprawdzić, jak usunąć jednostkę usługi, zobacz [AKS service principal considerations and deletion (Uwagi dotyczące jednostki usługi AKS i jej usuwanie)][sp-delete].
> 
> Jeśli używana jest tożsamość zarządzana, tożsamość jest zarządzana przez platformę i nie wymaga usunięcia.

## <a name="get-the-code"></a>Uzyskiwanie kodu

W tym przewodniku szybki start użyto już istniejących obrazów kontenerów do utworzenia wdrożenia Kubernetes. Powiązany kod aplikacji, pliku dockerfile i plik manifestu Kubernetes są [dostępne w serwisie GitHub.][azure-vote-app]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start wdrożono klaster Kubernetes, a następnie wdrożono w nim aplikację obsługującą wiele kontenerów. [Uzyskaj dostęp do pulpitu nawigacyjnego sieci Web Kubernetes][kubernetes-dashboard] dla klastra AKS.

Aby dowiedzieć się więcej o usłudze AKS i poznać dokładnie proces od kompletnego kodu do wdrożenia, przejdź do samouczka dotyczącego klastra Kubernetes.

> [!div class="nextstepaction"]
> [Samouczek usługi AKS][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubeconfig-file]: https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: ../azure-monitor/containers/container-insights-onboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks#az_aks_browse
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: /cli/azure/install_azure_cli
[azure-monitor-containers]: ../azure-monitor/containers/container-insights-overview.md
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[windows-container-cli]: windows-container-cli.md
