---
title: 'Szybki start: wdrażanie klastra usługi AKS przy użyciu Azure Portal'
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak szybko utworzyć klaster Kubernetes, wdrożyć aplikację i monitorować wydajność w usłudze Azure Kubernetes Service (AKS) przy użyciu witryny Azure Portal.
services: container-service
ms.topic: quickstart
ms.date: 03/15/2021
ms.custom: mvc, seo-javascript-october2019, contperf-fy21q3
ms.openlocfilehash: 28ba2ffd2007aeb45081cf66b05395a2b8456bf7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779709"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-the-azure-portal"></a>Szybki start: wdrażanie klastra Azure Kubernetes Service (AKS) przy użyciu Azure Portal

Azure Kubernetes Service (AKS) to zarządzana usługa platformy Kubernetes, która umożliwia szybkie wdrażanie klastrów i zarządzanie nimi. W tym przewodniku Szybki start:
* Wdrażanie klastra usługi AKS przy użyciu Azure Portal. 
* Uruchamianie aplikacji z wieloma kontenerami z frontonie internetowym i wystąpieniem usługi Redis w klastrze. 
* Monitoruj kondycję klastra i zasobników, w których jest uruchamiana aplikacja.

![Obraz przedstawiający przechodzenie do przykładowej aplikacji do głosowania na platformie Azure](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

W tym przewodniku Szybki start założono, że masz podstawową wiedzę na temat pojęć związanych z rozwiązaniem Kubernetes. Aby uzyskać więcej informacji, zobacz [Podstawowe pojęcia dotyczące usługi Azure Kubernetes Service (AKS)][kubernetes-concepts].

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-an-aks-cluster"></a>Tworzenie klastra AKS

1. W menu witryny Azure Portal lub na **stronie głównej** wybierz pozycję **Utwórz zasób**.

2. Wybierz pozycję **Kontenery** > **Usługa Kubernetes**.

3. Na stronie **Podstawowe** skonfiguruj następujące opcje:
    - **Szczegóły projektu:** 
        * Wybierz **subskrypcję** platformy Azure.
        * Wybierz lub utwórz grupę zasobów **platformy** Azure, taką *jak myResourceGroup.*
    - **Szczegóły klastra:** 
        * Wprowadź **nazwę klastra Kubernetes**, taką jak *myAKSCluster*. 
        * Wybierz region **i** **wersję kubernetes** dla klastra usługi AKS.
    - **Pula węzłów podstawowych:** 
        * Wybierz **rozmiar węzła** maszyny wirtualnej dla węzłów AKS. Rozmiar maszyny wirtualnej *nie może* zostać zmieniony po wdrożeniu klastra AKS.
        * Wybierz liczbę węzłów do wdrożenia w klastrze. Na potrzeby tego przewodnika Szybki start ustaw pozycję **Liczba węzłów** na *1*. Liczbę węzłów *można* dostosować po wdrożeniu klastra.
    
    ![Tworzenie klastra AKS — podaj podstawowe informacje](media/kubernetes-walkthrough-portal/create-cluster-basics.png)

4. Po **zakończeniu wybierz pozycję Dalej: Pule** węzłów.

5. Zachowaj domyślne opcje **pul węzłów.** W dolnej części ekranu kliknij przycisk **Dalej: Uwierzytelnianie**.
    > [!CAUTION]
    > Propagacja i pojawienie się nowo utworzonych podmiotów zabezpieczeń usługi Azure AD może potrwać kilka minut, co powoduje błędy "nie znaleziono jednostki usługi" i błędy walidacji w Azure Portal. W przypadku trafienia tej wyboju zapoznaj się z [naszym artykułem na temat rozwiązywania](troubleshooting.md#received-an-error-saying-my-service-principal-wasnt-found-or-is-invalid-when-i-try-to-create-a-new-cluster) problemów w celu ograniczenia ryzyka.

6. Na stronie **Uwierzytelnianie** skonfiguruj następujące opcje:
    - Utwórz nową tożsamość klastra przez:
        * Pozostawienie **pola Authentication** (Uwierzytelnianie) z **tożsamością zarządzaną assinged systemu** lub
        * Wybieranie **jednostki usługi** do używania jednostki usługi. 
            * Wybierz *(nową) domyślną jednostkę usługi,* aby utworzyć domyślną jednostkę usługi, lub
            * Wybierz *pozycję Skonfiguruj jednostkę usługi,* aby użyć istniejącej. Konieczne będzie podanie identyfikatora klienta i tajnego identyfikatora spN istniejącego podmiotu zabezpieczeń.
    - Włącz opcję Kontroli dostępu opartej na rolach (RBAC) na platformie Kubernetes, aby zapewnić bardziej precyzyjne sterowanie dostępem do zasobów usługi Kubernetes wdrożonych w klastrze usługi AKS.

    Domyślnie używana jest sieć *Podstawowa* i włączona jest usługa Azure Monitor dla kontenerów. 

7. Kliknij opcję **Przejrzyj i utwórz**, a następnie **Utwórz** po zakończeniu walidacji. 


8. Utworzenie klastra AKS może potrwać kilka minut. Po zakończeniu wdrażania przejdź do zasobu, wybierając jedną z nich:
    * Kliknij **pozycję Przejdź do zasobu** lub
    * Przeglądanie grupy zasobów klastra AKS i wybieranie zasobu usługi AKS. 
        * Przykładowy pulpit nawigacyjny klastra poniżej: przeglądanie grupy *myResourceGroup* i wybieranie *zasobu myAKSCluster.*

        ![Przykładowy pulpit nawigacyjny usługi AKS w witrynie Azure Portal](media/kubernetes-walkthrough-portal/aks-portal-dashboard.png)

## <a name="connect-to-the-cluster"></a>Łączenie z klastrem

Aby zarządzać klastrem Kubernetes, użyj klienta wiersza polecenia kubernetes, [kubectl][kubectl]. `kubectl` Program jest już zainstalowany, jeśli używasz Azure Cloud Shell. 

1. Otwórz Cloud Shell przy `>_` użyciu przycisku w górnej części Azure Portal.

    ![Otwieranie usługi Azure Cloud Shell w portalu](media/kubernetes-walkthrough-portal/aks-cloud-shell.png)

    > [!NOTE]
    > Aby wykonać te operacje w lokalnej instalacji powłoki:
    > 1. Sprawdź, czy interfejs wiersza polecenia platformy Azure jest zainstalowany.
    > 2. Połącz się z platformą Azure za pomocą `az login` polecenia .

2. Skonfiguruj polecenie , aby nawiązać połączenie `kubectl` z klastrem Kubernetes za pomocą polecenia [az aks get-credentials.][az-aks-get-credentials] Następujące polecenie pobiera poświadczenia i konfiguruje interfejs wiersza polecenia kubernetes do ich używania.

    ```azurecli
    az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
    ```

3. Sprawdź połączenie z klastrem przy użyciu funkcji , `kubectl get` aby zwrócić listę węzłów klastra.

    ```console
    kubectl get nodes
    ```

    Dane wyjściowe pokazują pojedynczy węzeł utworzony w poprzednich krokach. Upewnij się, że stan węzła to *Gotowe:*

    ```output
    NAME                       STATUS    ROLES     AGE       VERSION
    aks-agentpool-14693408-0   Ready     agent     15m       v1.11.5
    ```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Plik manifestu kubernetes definiuje żądany stan klastra, na przykład obrazy kontenerów do uruchomienia. 

W tym przewodniku Szybki start użyjemy manifestu do utworzenia wszystkich obiektów potrzebnych do uruchomienia aplikacji Azure Vote. Ten manifest obejmuje dwa wdrożenia rozwiązania Kubernetes:
* Przykładowe aplikacje języka Python do głosowania na platformie Azure.
* Wystąpienie usługi Redis. 

Tworzone są również dwie usługi Kubernetes:
* Wewnętrzna usługa dla wystąpienia usługi Redis.
* Zewnętrzna usługa do uzyskiwania dostępu do aplikacji Azure Vote z Internetu.

1. W Cloud Shell użyj edytora, aby utworzyć plik o nazwie `azure-vote.yaml` , taki jak:
    * `code azure-vote.yaml`
    * `nano azure-vote.yaml`Lub 
    * `vi azure-vote.yaml`. 

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

1. Wdróż aplikację za `kubectl apply` pomocą polecenia i określ nazwę manifestu YAML:

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

## <a name="test-the-application"></a>Testowanie aplikacji

Podczas uruchamiania aplikacji usługa Kubernetes uwidacznia fronton aplikacji w Internecie. Ten proces może potrwać kilka minut.

Aby monitorować postęp, użyj `kubectl get service` polecenia z `--watch` argumentem .

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

![Obraz przedstawiający przechodzenie do przykładowej aplikacji do głosowania na platformie Azure](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="monitor-health-and-logs"></a>Monitorowanie kondycji i dzienników

Podczas tworzenia klastra włączono usługę Azure Monitor dla kontenerów. Azure Monitor kontenerów udostępnia metryki kondycji dla klastra usługi AKS i zasobników uruchomionych w klastrze.

Wypełnianie danych metryki w Azure Portal. Aby wyświetlić bieżący stan kondycji, czas pracy i użycie zasobów dla zasobników aplikacji Azure Vote:

1. Przejdź z powrotem do zasobu AKS w Azure Portal.
1. W **obszarze** Monitorowanie po lewej stronie wybierz pozycję **Szczegółowe informacje.**
1. W górnej części wybierz pozycję **+ Dodaj filtr.**
1. Wybierz **pozycję Przestrzeń** nazw jako właściwość, a następnie wybierz pozycję *\<All but kube-system\>* .
1. Wybierz **pozycję Kontenery,** aby je wyświetlić.

Zostaną `azure-vote-back` wyświetlone `azure-vote-front` kontenery i , jak pokazano w poniższym przykładzie:

![Wyświetlanie kondycji uruchomionych kontenerów w usłudze AKS](media/kubernetes-walkthrough-portal/monitor-containers.png)

Aby wyświetlić dzienniki dla `azure-vote-front` zasobnika, wybierz pozycję **Wyświetl dzienniki kontenerów** z listy rozwijanej kontenerów. Te dzienniki obejmują *strumienie stdout* i *stderr* z kontenera.

![Wyświetlanie dzienników kontenerów w usłudze AKS](media/kubernetes-walkthrough-portal/monitor-container-logs.png)

## <a name="delete-cluster"></a>Usuwanie klastra

Aby uniknąć opłat za platformę Azure, wyczyść niepotrzebne zasoby. Wybierz przycisk **Usuń na pulpicie** nawigacyjnym klastra usługi AKS. Możesz również użyć polecenia [az aks delete][az-aks-delete] w Cloud Shell:

```azurecli
az aks delete --resource-group myResourceGroup --name myAKSCluster --no-wait
```
> [!NOTE]
> Po usunięciu klastra jednostka usługi Azure Active Directory używana przez klaster usługi AKS nie jest usuwana. Aby sprawdzić, jak usunąć jednostkę usługi, zobacz [AKS service principal considerations and deletion (Uwagi dotyczące jednostki usługi AKS i jej usuwanie)][sp-delete].
> 
> Jeśli używasz tożsamości zarządzanej, tożsamość jest zarządzana przez platformę i nie wymaga usunięcia.

## <a name="get-the-code"></a>Uzyskiwanie kodu

Wcześniej istniejące obrazy kontenerów zostały użyte w tym przewodniku Szybki start do utworzenia wdrożenia kubernetes. Powiązany kod aplikacji, plik dockerfile i plik manifestu kubernetes są [dostępne w witrynie GitHub.][azure-vote-app]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wdrożono klaster Kubernetes, a następnie wdrożono w nim aplikację z wieloma kontenerami. Uzyskaj dostęp do internetowego pulpitu nawigacyjnego usługi Kubernetes dla klastra usługi AKS.


Aby dowiedzieć się więcej na temat usługi AKS, przechodząc przez pełny przykład, w tym tworzenie aplikacji, wdrażanie z usługi Azure Container Registry, aktualizowanie uruchomionej aplikacji oraz skalowanie i uaktualnianie klastra, przejdź do samouczka klastra Kubernetes.

> [!div class="nextstepaction"]
> [Samouczek usługi AKS][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-documentation]: https://kubernetes.io/docs/home/

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-delete]: /cli/azure/aks#az_aks_delete
[aks-monitor]: ../azure-monitor/containers/container-insights-overview.md
[aks-network]: ./concepts-network.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[http-routing]: ./http-application-routing.md
[sp-delete]: kubernetes-service-principal.md#additional-considerations