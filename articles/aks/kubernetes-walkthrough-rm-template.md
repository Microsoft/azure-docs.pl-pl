---
title: Szybki start — tworzenie klastra usługi Azure Kubernetes Service (AKS)
description: Dowiedz się, jak szybko utworzyć klaster Kubernetes przy użyciu szablonu Azure Resource Manager i wdrożyć aplikację w Azure Kubernetes Service (AKS)
services: container-service
ms.topic: quickstart
ms.date: 03/15/2021
ms.custom: mvc,subject-armqs, devx-track-azurecli
ms.openlocfilehash: 4bcaafdb1f465fb8568078dfb5bfaf988d0cd587
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764445"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-an-arm-template"></a>Szybki start: wdrażanie klastra Azure Kubernetes Service (AKS) przy użyciu szablonu usługi ARM

Azure Kubernetes Service (AKS) to zarządzana usługa platformy Kubernetes, która umożliwia szybkie wdrażanie klastrów i zarządzanie nimi. W tym przewodniku Szybki start:
* Wdrażanie klastra usługi AKS przy użyciu Azure Resource Manager szablonu. 
* Uruchamianie aplikacji z wieloma kontenerami z frontoną internetową i wystąpieniem usługi Redis w klastrze. 

![Obraz przedstawiający przechodzenie do aplikacji Azure Vote](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

W tym przewodniku Szybki start założono, że masz podstawową wiedzę na temat pojęć związanych z rozwiązaniem Kubernetes. Aby uzyskać więcej informacji, zobacz [Podstawowe pojęcia dotyczące usługi Azure Kubernetes Service (AKS)][kubernetes-concepts].

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aks%2Fazuredeploy.json)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.61 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

- Aby utworzyć klaster usługi AKS przy użyciu Resource Manager szablonu, należy podać klucz publiczny SSH. Jeśli potrzebujesz tego zasobu, zobacz następującą sekcję: W przeciwnym razie przejdź do [sekcji Przeglądanie](#review-the-template) szablonu.

### <a name="create-an-ssh-key-pair"></a>Tworzenie pary kluczy SSH

Aby uzyskać dostęp do węzłów usługi AKS, połącz się przy użyciu pary kluczy SSH (publicznej i prywatnej), która jest generowana przy użyciu `ssh-keygen` polecenia . Domyślnie te pliki są tworzone w *katalogu ~/.ssh.* Uruchomienie polecenia spowoduje zastąpienie dowolnej pary kluczy SSH o takiej samej nazwie, która `ssh-keygen` już istnieje w danej lokalizacji.

1. Przejdź do [https://shell.azure.com](https://shell.azure.com) strony , Cloud Shell w przeglądarce.

1. Uruchom polecenie `ssh-keygen`. Poniższy przykład tworzy parę kluczy SSH przy użyciu szyfrowania RSA i długości bitów 2048:

    ```console
    ssh-keygen -t rsa -b 2048
    ```

Aby uzyskać więcej informacji na temat tworzenia kluczy SSH, zobacz [Tworzenie kluczy SSH][ssh-keys]do uwierzytelniania na platformie Azure i zarządzanie nimi.

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-aks/).

:::code language="json" source="~/quickstart-templates/101-aks/azuredeploy.json":::

Aby uzyskać więcej przykładów dla aplikacji AKS, zobacz witrynę [szablonów szybkiego startu aks.][aks-quickstart-templates]

## <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz poniższy przycisk, aby zalogować się do platformy Azure i otworzyć szablon.

    [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aks%2Fazuredeploy.json)

2. Wybierz lub wprowadź następujące wartości.

    W tym przewodniku Szybki start pozostaw wartości domyślne dla rozmiarów dysków systemu operacyjnego *GB,* liczby agentów, *rozmiaru* maszyny wirtualnej *agenta,* typu systemu operacyjnego i wersji *platformy Kubernetes.* Podaj własne wartości dla następujących parametrów szablonu:

    * **Subskrypcja:** wybierz subskrypcję platformy Azure.
    * **Grupa zasobów:** wybierz **pozycję Utwórz nową.** Wprowadź unikatową nazwę grupy zasobów, taką jak *myResourceGroup,* a następnie wybierz przycisk **OK.**
    * **Lokalizacja:** wybierz lokalizację, taką jak **Wschodnie usa.**
    * **Nazwa klastra:** wprowadź unikatową nazwę klastra usługi AKS, taką *jak myAKSCluster.*
    * **Prefiks DNS:** wprowadź unikatowy prefiks DNS klastra, taki *jak myakscluster.*
    * **Nazwa użytkownika administratora systemu Linux:** wprowadź nazwę użytkownika, aby nawiązać połączenie przy użyciu połączenia SSH, na przykład *azureuser.*
    * **Klucz publiczny SSH RSA:**  skopiuj i wklej publiczną część pary kluczy SSH (domyślnie zawartość *pliku ~/.ssh/id_rsa.pub).*

    ![Resource Manager, aby utworzyć Azure Kubernetes Service klastra w portalu](./media/kubernetes-walkthrough-rm-template/create-aks-cluster-using-template-portal.png)

3. Wybierz pozycję **Przejrzyj i utwórz**.

Utworzenie klastra AKS może potrwać kilka minut. Przed przejściem do następnego kroku poczekaj na pomyślne wdrożenie klastra.

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

### <a name="connect-to-the-cluster"></a>Łączenie z klastrem

Aby zarządzać klastrem Kubernetes, użyj klienta wiersza polecenia Kubernetes, [kubectl][kubectl]. `kubectl` program jest już zainstalowany, jeśli używasz Azure Cloud Shell. 

1. Zainstaluj `kubectl` lokalnie przy użyciu [polecenia az aks install-cli:][az-aks-install-cli]

    ```azurecli
    az aks install-cli
    ```

2. Skonfiguruj, `kubectl` aby nawiązać połączenie z klastrem Kubernetes za pomocą [polecenia az aks get-credentials.][az-aks-get-credentials] To polecenie powoduje pobranie poświadczeń i zastosowanie ich w konfiguracji interfejsu wiersza polecenia Kubernetes.

    ```azurecli-interactive
    az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
    ```

3. Sprawdź połączenie z klastrem przy użyciu [polecenia kubectl get.][kubectl-get] To polecenie zwraca listę węzłów klastra.

    ```console
    kubectl get nodes
    ```

    Dane wyjściowe pokazują węzły utworzone w poprzednich krokach. Upewnij się, że stan wszystkich węzłów to *Gotowe:*

    ```output
    NAME                       STATUS   ROLES   AGE     VERSION
    aks-agentpool-41324942-0   Ready    agent   6m44s   v1.12.6    
    aks-agentpool-41324942-1   Ready    agent   6m46s   v1.12.6
    aks-agentpool-41324942-2   Ready    agent   6m45s   v1.12.6
    ```

### <a name="run-the-application"></a>Uruchamianie aplikacji

Plik [manifestu kubernetes][kubernetes-deployment] definiuje żądany stan klastra, na przykład obrazy kontenerów do uruchomienia. 

W tym przewodniku Szybki start użyjemy manifestu do utworzenia wszystkich obiektów potrzebnych do uruchomienia [aplikacji Azure Vote.][azure-vote-app] Ten manifest zawiera dwa [wdrożenia rozwiązania Kubernetes:][kubernetes-deployment]
* Przykładowe aplikacje języka Python do głosowania na platformie Azure.
* Wystąpienie usługi Redis. 

Tworzone [są również dwie usługi Kubernetes:][kubernetes-service]
* Wewnętrzna usługa dla wystąpienia usługi Redis.
* Zewnętrzna usługa do uzyskiwania dostępu do aplikacji Azure Vote z Internetu.

1. Utwórz plik o nazwie `azure-vote.yaml`.
    * Jeśli używasz tej Azure Cloud Shell, ten plik można utworzyć przy użyciu lub tak, jakby pracować `vi` `nano` w systemie wirtualnym lub fizycznym
1. Skopiuj następującą definicję YAML:

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

    Dane wyjściowe pokazują pomyślnie utworzone wdrożenia i usługi:

    ```output
    deployment "azure-vote-back" created
    service "azure-vote-back" created
    deployment "azure-vote-front" created
    service "azure-vote-front" created
    ```

### <a name="test-the-application"></a>Testowanie aplikacji

Podczas uruchamiania aplikacji usługa Kubernetes uwidacznia fronton aplikacji w Internecie. Ten proces może potrwać kilka minut.

Monitoruj postęp za pomocą [polecenia kubectl get service][kubectl-get] z `--watch` argumentem .

```console
kubectl get service azure-vote-front --watch
```

Dane **wyjściowe EXTERNAL-IP** dla `azure-vote-front` usługi będą początkowo wyświetlane jako *oczekujące*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Gdy adres **EXTERNAL-IP** zmieni się z *oczekujące* na rzeczywisty publiczny adres IP, użyj funkcji , `CTRL-C` aby zatrzymać `kubectl` proces zegarka. Następujące przykładowe dane wyjściowe przedstawiają prawidłowy publiczny adres IP przypisany do usługi:

```output
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Aby wyświetlić działającą aplikację Azure Vote, otwórz zewnętrzny adres IP usługi w przeglądarce internetowej.

![Obraz przedstawiający przechodzenie do aplikacji Azure Vote](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby uniknąć nalicznych opłat za platformę Azure, wyczyść niepotrzebne zasoby. Usuń grupę zasobów, usługę kontenera i wszystkie powiązane zasoby za pomocą polecenia [az group delete][az-group-delete].

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Po usunięciu klastra jednostka usługi Azure Active Directory używana przez klaster usługi AKS nie jest usuwana. Aby sprawdzić, jak usunąć jednostkę usługi, zobacz [AKS service principal considerations and deletion (Uwagi dotyczące jednostki usługi AKS i jej usuwanie)][sp-delete].
> 
> Jeśli używasz tożsamości zarządzanej, tożsamość jest zarządzana przez platformę i nie wymaga usunięcia.

## <a name="get-the-code"></a>Uzyskiwanie kodu

Wcześniej istniejące obrazy kontenerów zostały użyte w tym przewodniku Szybki start do utworzenia wdrożenia kubernetes. Powiązany kod aplikacji, plik Dockerfile i plik manifestu Kubernetes są [dostępne w witrynie GitHub.][azure-vote-app]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wdrożono klaster Kubernetes, a następnie wdrożono w nim aplikację z wieloma kontenerami. [Uzyskaj dostęp do internetowego pulpitu nawigacyjnego rozwiązania Kubernetes][kubernetes-dashboard] dla klastra usługi AKS.

Aby dowiedzieć się więcej o usłudze AKS i poznać dokładnie proces od kompletnego kodu do wdrożenia, przejdź do samouczka dotyczącego klastra Kubernetes.

> [!div class="nextstepaction"]
> [Samouczek usługi AKS][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[azure-dev-spaces]: ../dev-spaces/index.yml
[aks-quickstart-templates]: https://azure.microsoft.com/resources/templates/?term=Azure+Kubernetes+Service

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
[azure-cli-install]: /cli/azure/install-azure-cli
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[ssh-keys]: ../virtual-machines/linux/create-ssh-keys-detailed.md
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
