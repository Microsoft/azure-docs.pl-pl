---
title: 'Szybki Start: Wdrażanie klastra AKS przy użyciu Azure Portal'
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak szybko utworzyć klaster Kubernetes, wdrożyć aplikację i monitorować wydajność w usłudze Azure Kubernetes Service (AKS) przy użyciu witryny Azure Portal.
services: container-service
ms.topic: quickstart
ms.date: 03/15/2021
ms.custom: mvc, seo-javascript-october2019, contperf-fy21q3
ms.openlocfilehash: 1371fb22b3474e37e50fe0eb67541d9ced69555f
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104771876"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-the-azure-portal"></a>Szybki Start: Wdrażanie klastra usługi Azure Kubernetes Service (AKS) przy użyciu Azure Portal

Azure Kubernetes Service (AKS) to zarządzana usługa platformy Kubernetes, która umożliwia szybkie wdrażanie klastrów i zarządzanie nimi. W tym przewodniku szybki start:
* Wdróż klaster AKS przy użyciu Azure Portal. 
* Uruchom aplikację wielokontenerową z frontonem sieci Web i wystąpieniem Redis w klastrze. 
* Monitoruj kondycję klastra i zasobników z uruchomioną aplikacją.

![Obraz przedstawiający przechodzenie do przykładowej aplikacji do głosowania na platformie Azure](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

W tym przewodniku Szybki start założono, że masz podstawową wiedzę na temat pojęć związanych z rozwiązaniem Kubernetes. Aby uzyskać więcej informacji, zobacz [Podstawowe pojęcia dotyczące usługi Azure Kubernetes Service (AKS)][kubernetes-concepts].

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-an-aks-cluster"></a>Tworzenie klastra AKS

1. W menu witryny Azure Portal lub na **stronie głównej** wybierz pozycję **Utwórz zasób**.

2. Wybierz pozycję **Kontenery** > **Usługa Kubernetes**.

3. Na stronie **Podstawowe** skonfiguruj następujące opcje:
    - **Szczegóły projektu**: 
        * Wybierz **subskrypcję** platformy Azure.
        * Wybierz lub Utwórz **grupę zasobów** platformy Azure, taką jak grupa *zasobów.*
    - **Szczegóły klastra**: 
        * Wprowadź **nazwę klastra Kubernetes**, taką jak *myAKSCluster*. 
        * Wybierz **region** i **wersję KUBERNETES** dla klastra AKS.
    - **Pula węzłów podstawowych**: 
        * Wybierz **rozmiar węzła** maszyny wirtualnej dla węzłów AKS. Rozmiar maszyny wirtualnej *nie może* zostać zmieniony po wdrożeniu klastra AKS.
        * Wybierz liczbę węzłów do wdrożenia w klastrze. Na potrzeby tego przewodnika Szybki start ustaw pozycję **Liczba węzłów** na *1*. Liczbę węzłów *można* dostosować po wdrożeniu klastra.
    
    ![Tworzenie klastra AKS — podaj podstawowe informacje](media/kubernetes-walkthrough-portal/create-cluster-basics.png)

4. Wybierz pozycję **Dalej: pule węzłów** po zakończeniu.

5. Zachowaj domyślne opcje **pul węzłów** . W dolnej części ekranu kliknij przycisk **Dalej: uwierzytelnianie**.
    > [!CAUTION]
    > Nowo utworzone jednostki usługi Azure AD mogą potrwać kilka minut, aby propagowanie i dostęp do nich było możliwe, co spowodowało błędy i niepowodzenia walidacji w Azure Portal. W przypadku osiągnięcia tego nierówności należy zapoznać się [z artykułem rozwiązywania problemów](troubleshooting.md#received-an-error-saying-my-service-principal-wasnt-found-or-is-invalid-when-i-try-to-create-a-new-cluster) w celu rozwiązania problemu.

6. Na stronie **Uwierzytelnianie** skonfiguruj następujące opcje:
    - Utwórz nową tożsamość klastra:
        * Opuszczanie pola **uwierzytelniania** przy użyciu **tożsamości zarządzanej system-assinged** lub
        * Wybieranie **nazwy głównej usługi** do użycia jednostki usługi. 
            * Wybierz *(Nowa) domyślną nazwę główną usługi* , aby utworzyć domyślną nazwę główną usługi lub
            * Wybierz pozycję *Konfiguruj nazwę główną usługi* , aby użyć istniejącej. Należy podać identyfikator klienta i klucz tajny nazwy SPN istniejącego podmiotu zabezpieczeń.
    - Włącz opcję Kubernetes kontroli dostępu opartej na rolach (Kubernetes RBAC), aby zapewnić bardziej precyzyjną kontrolę dostępu do zasobów Kubernetes wdrożonych w klastrze AKS.

    Domyślnie używana jest sieć *Podstawowa* i włączona jest usługa Azure Monitor dla kontenerów. 

7. Kliknij opcję **Przejrzyj i utwórz**, a następnie **Utwórz** po zakończeniu walidacji. 


8. Utworzenie klastra AKS może potrwać kilka minut. Po zakończeniu wdrażania przejdź do zasobu, wykonując następujące czynności:
    * Kliknij pozycję **Przejdź do zasobu** lub
    * Przeglądanie grupy zasobów klastra AKS i wybieranie zasobu AKS. 
        * Na przykład pulpit nawigacyjny klastra poniżej: przeglądanie w poszukiwaniu *zasobów* i wybieranie zasobów *myAKSCluster* .

        ![Przykładowy pulpit nawigacyjny usługi AKS w witrynie Azure Portal](media/kubernetes-walkthrough-portal/aks-portal-dashboard.png)

## <a name="connect-to-the-cluster"></a>Łączenie z klastrem

Aby zarządzać klastrem Kubernetes, użyj klienta wiersza polecenia Kubernetes [polecenia kubectl][kubectl]. `kubectl` jest już zainstalowany, jeśli używasz Azure Cloud Shell. 

1. Otwórz Cloud Shell przy użyciu `>_` przycisku w górnej części Azure Portal.

    ![Otwieranie usługi Azure Cloud Shell w portalu](media/kubernetes-walkthrough-portal/aks-cloud-shell.png)

    > [!NOTE]
    > Aby wykonać te operacje w lokalnej instalacji powłoki:
    > 1. Upewnij się, że interfejs wiersza polecenia platformy Azure został zainstalowany.
    > 2. Połącz się z platformą Azure za pomocą `az login` polecenia.

2. Skonfiguruj `kubectl` , aby nawiązać połączenie z klastrem Kubernetes za pomocą polecenia [AZ AKS Get-Credentials][az-aks-get-credentials] . Następujące polecenie pobiera poświadczenia i konfiguruje interfejs wiersza polecenia Kubernetes, aby można było z nich korzystać.

    ```azurecli
    az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
    ```

3. Sprawdź połączenie z klastrem za pomocą polecenia `kubectl get` , aby zwrócić listę węzłów klastra.

    ```console
    kubectl get nodes
    ```

    Dane wyjściowe pokazują pojedynczy węzeł utworzony w poprzednich krokach. Upewnij się, że stan węzła jest *gotowy*:

    ```output
    NAME                       STATUS    ROLES     AGE       VERSION
    aks-agentpool-14693408-0   Ready     agent     15m       v1.11.5
    ```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Plik manifestu Kubernetes definiuje żądany stan klastra, na przykład które obrazy kontenerów mają być uruchamiane. 

W tym przewodniku szybki start użyjesz manifestu, aby utworzyć wszystkie obiekty potrzebne do uruchomienia aplikacji do głosowania na platformie Azure. Ten manifest obejmuje dwie Kubernetes wdrożenia:
* Przykładowe aplikacje języka Python na platformie Azure.
* Wystąpienie Redis. 

Tworzone są również dwie usługi Kubernetes:
* Usługa wewnętrzna dla wystąpienia usługi Redis.
* Zewnętrzna usługa do uzyskiwania dostępu do aplikacji do głosowania platformy Azure z Internetu.

1. W Cloud Shell Użyj edytora, aby utworzyć plik o nazwie `azure-vote.yaml` , taki jak:
    * `code azure-vote.yaml`
    * `nano azure-vote.yaml`lub 
    * `vi azure-vote.yaml`. 

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

1. Wdróż aplikację przy użyciu `kubectl apply` polecenia i określ nazwę manifestu YAML:

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

Aby monitorować postęp, użyj `kubectl get service` polecenia z `--watch` argumentem.

```console
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

![Obraz przedstawiający przechodzenie do przykładowej aplikacji do głosowania na platformie Azure](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="monitor-health-and-logs"></a>Monitorowanie kondycji i dzienników

Podczas tworzenia klastra włączono usługę Azure Monitor dla kontenerów. Azure Monitor kontenerów zawiera metryki kondycji zarówno dla klastra AKS, jak i dla każdego z nich uruchomionych w klastrze.

Dane metryk zajmują kilka minut, aby wypełnić je w Azure Portal. Aby wyświetlić bieżący stan kondycji, czas przestoju i użycie zasobów dla zasobników głosowania platformy Azure:

1. Przejdź z powrotem do zasobu AKS w Azure Portal.
1. W obszarze **monitorowanie** po lewej stronie wybierz pozycję **szczegółowe informacje**.
1. W górnej części wybierz opcję **+ Dodaj filtr**.
1. Wybierz **przestrzeń nazw** jako właściwość, a następnie wybierz *\<All but kube-system\>* .
1. Wybierz **kontenery** , aby je wyświetlić.

`azure-vote-back` `azure-vote-front` Zostaną wyświetlone kontenery i, jak pokazano w następującym przykładzie:

![Wyświetlanie kondycji uruchomionych kontenerów w usłudze AKS](media/kubernetes-walkthrough-portal/monitor-containers.png)

Aby wyświetlić dzienniki dla usługi `azure-vote-front` pod, wybierz pozycję **Wyświetl dzienniki kontenerów** z listy rozwijanej kontenery. Te dzienniki obejmują strumienie *stdout* i *stderr* z kontenera.

![Wyświetlanie dzienników kontenerów w usłudze AKS](media/kubernetes-walkthrough-portal/monitor-container-logs.png)

## <a name="delete-cluster"></a>Usuwanie klastra

Aby uniknąć naliczania opłat za platformę Azure, Oczyść niepotrzebne zasoby. Wybierz przycisk **Usuń** na pulpicie nawigacyjnym klastra AKS. Można również użyć polecenia [AZ AKS Delete][az-aks-delete] w Cloud Shell:

```azurecli
az aks delete --resource-group myResourceGroup --name myAKSCluster --no-wait
```
> [!NOTE]
> Po usunięciu klastra jednostka usługi Azure Active Directory używana przez klaster usługi AKS nie jest usuwana. Aby sprawdzić, jak usunąć jednostkę usługi, zobacz [AKS service principal considerations and deletion (Uwagi dotyczące jednostki usługi AKS i jej usuwanie)][sp-delete].
> 
> Jeśli używana jest tożsamość zarządzana, tożsamość jest zarządzana przez platformę i nie wymaga usunięcia.

## <a name="get-the-code"></a>Uzyskiwanie kodu

W tym przewodniku szybki start użyto już istniejących obrazów kontenerów do utworzenia wdrożenia Kubernetes. Powiązany kod aplikacji, pliku dockerfile i plik manifestu Kubernetes są [dostępne w serwisie GitHub.][azure-vote-app]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start wdrożono klaster Kubernetes, a następnie wdrożono w nim aplikację obsługującą wiele kontenerów. Uzyskaj dostęp do pulpitu nawigacyjnego sieci Web Kubernetes dla klastra AKS.


Aby dowiedzieć się więcej na temat AKS poprzez przechodzenie przez pełny przykład, w tym Kompilowanie aplikacji, wdrażanie z Azure Container Registry, aktualizowanie działającej aplikacji i skalowanie i Uaktualnianie klastra, przejdź do samouczka klastra Kubernetes.

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
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-delete]: /cli/azure/aks#az-aks-delete
[aks-monitor]: ../azure-monitor/containers/container-insights-overview.md
[aks-network]: ./concepts-network.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[http-routing]: ./http-application-routing.md
[sp-delete]: kubernetes-service-principal.md#additional-considerations