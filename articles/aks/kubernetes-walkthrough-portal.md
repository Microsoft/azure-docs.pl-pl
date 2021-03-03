---
title: 'Szybki Start: Wdrażanie klastra AKS przy użyciu Azure Portal'
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak szybko utworzyć klaster Kubernetes, wdrożyć aplikację i monitorować wydajność w usłudze Azure Kubernetes Service (AKS) przy użyciu witryny Azure Portal.
services: container-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.custom: mvc, seo-javascript-october2019, contperfq3
ms.openlocfilehash: 443c9e0cebe2a45386b63b3a0bc4a813d243e49e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101714565"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-the-azure-portal"></a>Szybki Start: Wdrażanie klastra usługi Azure Kubernetes Service (AKS) przy użyciu Azure Portal

Azure Kubernetes Service (AKS) to zarządzana usługa platformy Kubernetes, która umożliwia szybkie wdrażanie klastrów i zarządzanie nimi. W tym przewodniku Szybki start wdrażany jest klaster AKS przy użyciu witryny Azure Portal. W klastrze jest uruchamiana aplikacja obsługująca wiele kontenerów, która składa się z frontonu internetowego i wystąpienia pamięci podręcznej Redis. Następnie zobaczysz, jak monitorować kondycję klastra i zasobników, w których działa Twoja aplikacja.

![Obraz przedstawiający przechodzenie do przykładowej aplikacji do głosowania na platformie Azure](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

W tym przewodniku Szybki start założono, że masz podstawową wiedzę na temat pojęć związanych z rozwiązaniem Kubernetes. Aby uzyskać więcej informacji, zobacz [Podstawowe pojęcia dotyczące usługi Azure Kubernetes Service (AKS)][kubernetes-concepts].

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-an-aks-cluster"></a>Tworzenie klastra AKS

Aby utworzyć klaster AKS, wykonaj następujące czynności:

1. W menu witryny Azure Portal lub na **stronie głównej** wybierz pozycję **Utwórz zasób**.

2. Wybierz pozycję **Kontenery** >  **Usługa Kubernetes**.

3. Na stronie **Podstawowe** skonfiguruj następujące opcje:
    - **Szczegóły projektu**: Wybierz **subskrypcję** platformy Azure, a następnie wybierz lub Utwórz **grupę zasobów** platformy Azure, taką jak grupa *zasobów*.
    - **Szczegóły klastra**: wprowadź **nazwę klastra Kubernetes**, na przykład *myAKSCluster*. Wybierz **region** i **wersję KUBERNETES** dla klastra AKS.
    - **Pula węzłów podstawowych**: Wybierz **rozmiar węzła** maszyny wirtualnej dla węzłów AKS. *Nie* można zmienić rozmiaru maszyny wirtualnej po wdrożeniu klastra AKS.
            -Wybierz liczbę węzłów, które mają zostać wdrożone w klastrze. Na potrzeby tego przewodnika Szybki start ustaw pozycję **Liczba węzłów** na *1*. Liczbę węzłów *można* dostosować po wdrożeniu klastra.
    
    ![Tworzenie klastra AKS — podaj podstawowe informacje](media/kubernetes-walkthrough-portal/create-cluster-basics.png)

    Wybierz pozycję **Dalej: pule węzłów** po zakończeniu.

4. Na stronie **Pule węzłów** Zachowaj ustawienia domyślne. W dolnej części ekranu kliknij przycisk **Dalej: uwierzytelnianie**.
    > [!CAUTION]
    > Utworzenie nowych jednostek usługi AAD może potrwać kilka minut, aby można było propagować i stać się dostępne, powodując nieznalezienie błędów jednostki usługi i niepowodzenia walidacji w Azure Portal. Jeśli trafisz, odwiedź stronę [Rozwiązywanie typowych problemów z usługą Azure Kubernetes](troubleshooting.md#received-an-error-saying-my-service-principal-wasnt-found-or-is-invalid-when-i-try-to-create-a-new-cluster) w celu rozwiązania problemu.

5. Na stronie **Uwierzytelnianie** skonfiguruj następujące opcje:
    - Utwórz nową nazwę główną usługi, pozostawiając w polu **Nazwa główna usługi** **domyślną nazwę główną usługi (nowa)**. Możesz też wybrać opcję *Skonfiguruj nazwę główną usługi*, aby użyć istniejącej usługi. Jeśli używasz istniejącej usługi, musisz podać identyfikator klienta i klucz tajny SPN.
    - Włącz opcję Kubernetes kontroli dostępu opartej na rolach (Kubernetes RBAC). Zapewni to dokładniejszą kontrolę dostępu do zasobów Kubernetes wdrożonych w klastrze AKS.

    Alternatywnie można użyć tożsamości zarządzanej zamiast nazwy głównej usługi. Aby uzyskać więcej informacji, zobacz temat [Używanie tożsamości zarządzanych](use-managed-identity.md) .

Domyślnie używana jest sieć *Podstawowa* i włączona jest usługa Azure Monitor dla kontenerów. Kliknij opcję **Przejrzyj i utwórz**, a następnie **Utwórz** po zakończeniu walidacji.

Utworzenie klastra AKS może potrwać kilka minut. Po zakończeniu wdrażania kliknij pozycję **Przejdź do zasobu** lub przejdź do grupy zasobów klastra AKS, *na przykład grupa zasobów, a* następnie wybierz zasób AKS, taki jak *myAKSCluster*. Zostanie wyświetlony pulpit nawigacyjny klastra AKS, jak w poniższym przykładzie:

![Przykładowy pulpit nawigacyjny usługi AKS w witrynie Azure Portal](media/kubernetes-walkthrough-portal/aks-portal-dashboard.png)

## <a name="connect-to-the-cluster"></a>Łączenie z klastrem

Aby zarządzać klastrem Kubernetes, należy użyć klienta wiersza polecenia usługi Kubernetes — narzędzia [kubectl][kubectl]. Klient `kubectl` jest instalowany wstępnie wraz z usługą Azure Cloud Shell.

Otwórz Cloud Shell przy użyciu `>_` przycisku w górnej części Azure Portal.

![Otwieranie usługi Azure Cloud Shell w portalu](media/kubernetes-walkthrough-portal/aks-cloud-shell.png)

> [!NOTE]
> Aby wykonać te operacje w lokalnej instalacji powłoki, należy najpierw sprawdzić, czy jest zainstalowany interfejs wiersza polecenia platformy Azure, a następnie nawiązać połączenie z platformą Azure za pośrednictwem `az login` poleceniu.

Aby skonfigurować narzędzie `kubectl` w celu nawiązania połączenia z klastrem Kubernetes, użyj polecenia [az aks get-credentials][az-aks-get-credentials]. To polecenie powoduje pobranie poświadczeń i zastosowanie ich w konfiguracji interfejsu wiersza polecenia Kubernetes. Poniższy przykład umożliwia pobranie poświadczeń dla nazwy klastra *myAKSCluster* w grupie zasobów *myResourceGroup*:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Aby sprawdzić połączenie z klastrem, użyj polecenia, `kubectl get` Aby zwrócić listę węzłów klastra.

```console
kubectl get nodes
```

Poniższe przykładowe dane wyjściowe zawierają jeden węzeł utworzony w poprzednich krokach. Upewnij się, że stan węzła to *Gotowy*:

```output
NAME                       STATUS    ROLES     AGE       VERSION
aks-agentpool-14693408-0   Ready     agent     15m       v1.11.5
```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Plik manifestu platformy Kubernetes definiuje żądany stan klastra, w tym informacje o obrazach kontenera do uruchomienia. W tym przewodniku Szybki start manifest służy do tworzenia wszystkich obiektów potrzebnych do uruchomienia aplikacji Azure Vote. Ten manifest obejmuje dwa wdrożenia platformy Kubernetes — jedno dla aplikacji Azure Vote w języku Python i jedno dla wystąpienia usługi Redis. Tworzone są również dwie usługi platformy Kubernetes — wewnętrzna usługa dla wystąpienia usługi Redis i zewnętrzna usługa służąca do uzyskiwania dostępu do aplikacji Azure Vote z Internetu.

W Cloud Shell Użyj edytora, aby utworzyć plik o nazwie `azure-vote.yaml` , taki jak `code azure-vote.yaml` , `nano azure-vote.yaml` lub `vi azure-vote.yaml` . Następnie skopiuj w następującej definicji YAML:

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

Wdróż aplikację przy użyciu `kubectl apply` polecenia i określ nazwę manifestu YAML:

```console
kubectl apply -f azure-vote.yaml
```

Następujące przykładowe dane wyjściowe przedstawiają pomyślnie utworzone wdrożenia i usługi:

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

Początkowo adres *EXTERNAL-IP* dla usługi *azure-vote-front* jest wyświetlany jako *oczekujący*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Gdy dla adresu *EXTERNAL-IP* wartość *oczekujący* zmieni się na rzeczywisty publiczny adres IP, naciśnij klawisze `CTRL-C`, aby zatrzymać proces śledzenia narzędzia `kubectl`. Następujące przykładowe dane wyjściowe przedstawiają prawidłowy publiczny adres IP przypisany do usługi:

```output
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Aby wyświetlić działającą aplikację Azure Vote, otwórz zewnętrzny adres IP usługi w przeglądarce internetowej.

![Obraz przedstawiający przechodzenie do przykładowej aplikacji do głosowania na platformie Azure](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="monitor-health-and-logs"></a>Monitorowanie kondycji i dzienników

Podczas tworzenia klastra włączono usługę Azure Monitor dla kontenerów. Ta funkcja monitorowania udostępnia metryki dotyczące kondycji zasobników i klastra usługi AKS działających w klastrze.

Zebranie tych danych w witrynie Azure Portal może zająć kilka minut. Aby wyświetlić bieżący stan, czas pracy i użycie zasobów na potrzeby zasobników aplikacji Azure Vote, wróć do zasobu usługi AKS, takiego jak *myAKSCluster*, w witrynie Azure Portal. Możesz uzyskać dostęp do stanu kondycji w następujący sposób:

1. W obszarze **Monitorowanie** po lewej stronie wybierz pozycję **Insights**
1. U góry wybierz pozycję **+ Dodaj filtr**
1. Wybierz *przestrzeń nazw* jako właściwość, a następnie wybierz pozycję *\<All but kube-system\>*
1. Wybierz pozycję **Kontenery**, aby ją wyświetlić.

Zostaną wyświetlone kontenery *azure-vote-back* i *azure-vote-front*, jak pokazano w poniższym przykładzie:

![Wyświetlanie kondycji uruchomionych kontenerów w usłudze AKS](media/kubernetes-walkthrough-portal/monitor-containers.png)

Aby wyświetlić dzienniki dla obszaru `azure-vote-front` pod, wybierz pozycję **Wyświetl dzienniki kontenerów** z listy rozwijanej na liście kontenerów. Te dzienniki obejmują strumienie *stdout* i *stderr* z kontenera.

![Wyświetlanie dzienników kontenerów w usłudze AKS](media/kubernetes-walkthrough-portal/monitor-container-logs.png)

## <a name="delete-cluster"></a>Usuwanie klastra

Gdy klaster nie będzie już potrzebny, usuń zasób klastra, co spowoduje usunięcie wszystkich skojarzonych zasobów. Tę operację można wykonać w Azure Portal, wybierając przycisk **Usuń** na pulpicie nawigacyjnym klastra AKS. Alternatywnie można użyć polecenia [AZ AKS Delete][az-aks-delete] w Cloud Shell:

```azurecli
az aks delete --resource-group myResourceGroup --name myAKSCluster --no-wait
```

> [!NOTE]
> Po usunięciu klastra jednostka usługi Azure Active Directory używana przez klaster usługi AKS nie jest usuwana. Aby sprawdzić, jak usunąć jednostkę usługi, zobacz [AKS service principal considerations and deletion (Uwagi dotyczące jednostki usługi AKS i jej usuwanie)][sp-delete]. Jeśli używana jest tożsamość zarządzana, tożsamość jest zarządzana przez platformę i nie wymaga usunięcia.

## <a name="get-the-code"></a>Uzyskiwanie kodu

W tym przewodniku Szybki start utworzono wdrożenie platformy Kubernetes za pomocą utworzonych wcześniej obrazów kontenerów. Powiązany kod aplikacji, plik Dockerfile i plik manifestu rozwiązania Kubernetes są dostępne w serwisie GitHub.

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wdrożono klaster Kubernetes oraz wdrożono w nim aplikację obsługującą wiele kontenerów.

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
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest&preserve-view=true#az-aks-get-credentials
[az-aks-delete]: /cli/azure/aks#az-aks-delete
[aks-monitor]: ../azure-monitor/containers/container-insights-overview.md
[aks-network]: ./concepts-network.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[http-routing]: ./http-application-routing.md
[sp-delete]: kubernetes-service-principal.md#additional-considerations