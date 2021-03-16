---
title: 'Szybki Start: Wdrażanie klastra AKS przy użyciu programu PowerShell'
description: Dowiedz się, jak szybko utworzyć klaster Kubernetes, wdrożyć aplikację oraz monitorować wydajność w usłudze Azure Kubernetes Service (AKS) przy użyciu programu PowerShell.
services: container-service
ms.topic: quickstart
ms.date: 03/15/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2b61c791390200beac4a18422a4de58dd94fa711
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103492901"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-cluster-using-powershell"></a>Szybki Start: Wdrażanie klastra usługi Azure Kubernetes Service przy użyciu programu PowerShell

Azure Kubernetes Service (AKS) to zarządzana usługa platformy Kubernetes, która umożliwia szybkie wdrażanie klastrów i zarządzanie nimi. W tym przewodniku szybki start:
* Wdróż klaster AKS przy użyciu programu PowerShell. 
* Uruchom aplikację wielokontenerową z frontonem sieci Web i wystąpieniem Redis w klastrze. 
* Monitoruj kondycję klastra i zasobników z uruchomioną aplikacją.

Aby dowiedzieć się więcej na temat tworzenia puli węzłów systemu Windows Server, zobacz [Tworzenie klastra AKS, który obsługuje kontenery systemu Windows Server][windows-container-powershell].

![Aplikacja do głosowania wdrożona w usłudze Azure Kubernetes Service](./media/kubernetes-walkthrough-powershell/voting-app-deployed-in-azure-kubernetes-service.png)

W tym przewodniku Szybki start założono, że masz podstawową wiedzę na temat pojęć związanych z rozwiązaniem Kubernetes. Aby uzyskać więcej informacji, zobacz [Podstawowe pojęcia dotyczące usługi Azure Kubernetes Service (AKS)][kubernetes-concepts].

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

Jeśli używasz programu PowerShell lokalnie, zainstaluj moduł AZ PowerShell i Połącz się z kontem platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) . Aby uzyskać więcej informacji na temat instalowania modułu AZ PowerShell module, zobacz [Install Azure PowerShell][install-azure-powershell].

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Jeśli masz wiele subskrypcji platformy Azure, wybierz odpowiedni identyfikator subskrypcji, w którym zasoby powinny być rozliczane za pomocą polecenia cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext) .

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

[Grupa zasobów platformy Azure](../azure-resource-manager/management/overview.md) to grupa logiczna, w której zasoby platformy Azure są wdrażane i zarządzane. Podczas tworzenia grupy zasobów zostanie wyświetlony monit o określenie lokalizacji. Ta lokalizacja: 
* Lokalizacja przechowywania metadanych grupy zasobów.
* Miejsce, w którym zasoby będą działać na platformie Azure, jeśli nie określisz innego regionu podczas tworzenia zasobu. 

Poniższy przykład tworzy grupę zasobów o nazwie Moja **zasobów** w regionie **wschodnim** .

Utwórz grupę zasobów za pomocą polecenia cmdlet [New-AzResourceGroup][new-azresourcegroup] .

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location eastus
```

Dane wyjściowe dla pomyślnie utworzonej grupy zasobów:

```plaintext
ResourceGroupName : myResourceGroup
Location          : eastus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup
```

## <a name="create-aks-cluster"></a>Tworzenie klastra AKS

1. Generuj parę kluczy SSH przy użyciu `ssh-keygen` narzędzia wiersza polecenia. 
    * Aby uzyskać więcej informacji, zobacz [szybkie kroki: Tworzenie i używanie pary kluczy publiczny-prywatny SSH dla maszyn wirtualnych z systemem Linux na platformie Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

1. Utwórz klaster AKS przy użyciu polecenia cmdlet [New-AzAks][new-azaks] . Azure Monitor dla kontenerów jest domyślnie włączona.

    W poniższym przykładzie pokazano tworzenie klastra o nazwie **myAKSCluster** z jednym węzłem. 

    ```azurepowershell-interactive
    New-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster -NodeCount 1
    ```

Po kilku minutach polecenie zostanie zakończone i zwróci informacje o klastrze.

> [!NOTE]
> Podczas tworzenia klastra AKS zostaje automatycznie utworzona druga grupa zasobów do przechowywania zasobów AKS. Aby uzyskać więcej informacji, zobacz [dlaczego dwie grupy zasobów utworzono za pomocą AKS?](./faq.md#why-are-two-resource-groups-created-with-aks)

## <a name="connect-to-the-cluster"></a>Łączenie z klastrem

Aby zarządzać klastrem Kubernetes, użyj klienta wiersza polecenia Kubernetes [polecenia kubectl][kubectl]. `kubectl` jest już zainstalowany, jeśli używasz Azure Cloud Shell. 

1. Zainstaluj `kubectl` lokalnie przy użyciu `Install-AzAksKubectl` polecenia cmdlet:

    ```azurepowershell
    Install-AzAksKubectl
    ```

2. Skonfiguruj, `kubectl` Aby nawiązać połączenie z klastrem Kubernetes przy użyciu polecenia cmdlet [Import-AzAksCredential][import-azakscredential] . Poniższe polecenie cmdlet pobiera poświadczenia i konfiguruje interfejs wiersza polecenia Kubernetes, aby móc z nich korzystać.

    ```azurepowershell-interactive
    Import-AzAksCredential -ResourceGroupName myResourceGroup -Name myAKSCluster
    ```

3. Sprawdź połączenie z klastrem za pomocą polecenia [polecenia kubectl Get][kubectl-get] . To polecenie zwraca listę węzłów klastra.

    ```azurepowershell-interactive
    .\kubectl get nodes
    ```

    Dane wyjściowe pokazują pojedynczy węzeł utworzony w poprzednich krokach. Upewnij się, że stan węzła jest *gotowy*:

    ```plaintext
    NAME                       STATUS   ROLES   AGE     VERSION
    aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.15.10
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
    * W przypadku korzystania z Azure Cloud Shell ten plik można utworzyć przy użyciu programu `vi` lub `nano` w przypadku pracy w systemie wirtualnym lub fizycznym.
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

    ```azurepowershell-interactive
    .\kubectl apply -f azure-vote.yaml
    ```

    W danych wyjściowych przedstawiono pomyślnie utworzone wdrożenia i usługi:

    ```plaintext
    deployment.apps/azure-vote-back created
    service/azure-vote-back created
    deployment.apps/azure-vote-front created
    service/azure-vote-front created
    ```

## <a name="test-the-application"></a>Testowanie aplikacji

Podczas uruchamiania aplikacji usługa Kubernetes uwidacznia fronton aplikacji w Internecie. Ten proces może potrwać kilka minut.

Monitoruj postęp przy użyciu polecenia [polecenia kubectl Get Service][kubectl-get] z `--watch` argumentem.

```azurepowershell-interactive
.\kubectl get service azure-vote-front --watch
```

Zewnętrzne dane wyjściowe **IP** dla `azure-vote-front` usługi będą początkowo wyświetlane jako *oczekujące*.

```plaintext
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Gdy **zewnętrzny adres IP** zmieni się z *oczekujące* na rzeczywisty publiczny adres IP, użyj, `CTRL-C` Aby zatrzymać `kubectl` proces obserwacji. Następujące przykładowe dane wyjściowe przedstawiają prawidłowy publiczny adres IP przypisany do usługi:

```plaintext
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Aby wyświetlić działającą aplikację Azure Vote, otwórz zewnętrzny adres IP usługi w przeglądarce internetowej.

![Aplikacja do głosowania wdrożona w usłudze Azure Kubernetes Service](./media/kubernetes-walkthrough-powershell/voting-app-deployed-in-azure-kubernetes-service.png)

Przejrzyj metryki kondycji węzłów klastra "i" w obszarze "przechwycone przez Azure Monitor dla kontenerów w Azure Portal. 

## <a name="delete-the-cluster"></a>Usuwanie klastra

Aby uniknąć naliczania opłat za platformę Azure, Oczyść niepotrzebne zasoby. Użyj polecenia cmdlet [Remove-AzResourceGroup][remove-azresourcegroup] , aby usunąć grupę zasobów, usługę kontenera i wszystkie powiązane zasoby.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
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
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[azure-dev-spaces]: ../dev-spaces/index.yml
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git

<!-- LINKS - internal -->
[windows-container-powershell]: windows-container-powershell.md
[kubernetes-concepts]: concepts-clusters-workloads.md
[install-azure-powershell]: /powershell/azure/install-az-ps
[new-azresourcegroup]: /powershell/module/az.resources/new-azresourcegroup
[new-azaks]: /powershell/module/az.aks/new-azaks
[import-azakscredential]: /powershell/module/az.aks/import-azakscredential
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[remove-azresourcegroup]: /powershell/module/az.resources/remove-azresourcegroup
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[kubernetes-dashboard]: kubernetes-dashboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
