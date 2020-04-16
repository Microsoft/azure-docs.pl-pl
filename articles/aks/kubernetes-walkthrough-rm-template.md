---
title: Szybki start — tworzenie klastra usługi Azure Kubernetes Service (AKS)
description: Dowiedz się, jak szybko utworzyć klaster kubernetes przy użyciu szablonu usługi Azure Resource Manager i wdrożyć aplikację w usłudze Azure Kubernetes (AKS)
services: container-service
ms.topic: quickstart
ms.date: 04/19/2019
ms.custom: mvc,subject-armqs
ms.openlocfilehash: c5ea54a33ee027de0b11c59c53085b9d20ca6a3a
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392833"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-an-azure-resource-manager-template"></a>Szybki start: wdrażanie klastra usługi Kubernetes platformy Azure (AKS) przy użyciu szablonu usługi Azure Resource Manager

Azure Kubernetes Service (AKS) to zarządzana usługa platformy Kubernetes, która umożliwia szybkie wdrażanie klastrów i zarządzanie nimi. W tym przewodniku Szybki start można wdrożyć klaster AKS przy użyciu szablonu usługi Azure Resource Manager. W klastrze jest uruchamiana aplikacja obsługująca wiele kontenerów, która składa się z frontonu internetowego i wystąpienia pamięci podręcznej Redis.

![Obraz przedstawiający przechodzenie do aplikacji Azure Vote](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

W tym przewodniku Szybki start założono, że masz podstawową wiedzę na temat pojęć związanych z rozwiązaniem Kubernetes. Aby uzyskać więcej informacji, zobacz [Podstawowe pojęcia dotyczące usługi Azure Kubernetes Service (AKS)][kubernetes-concepts].

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i używać interfejsu wiersza polecenia lokalnie, ten szybki start wymaga, aby uruchomić interfejsu wiersza polecenia platformy Azure w wersji 2.0.61 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć klaster AKS przy użyciu szablonu Menedżera zasobów, należy podać klucz publiczny SSH i jednostkę usługi Azure Active Directory.  Alternatywnie można użyć [tożsamości zarządzanej](use-managed-identity.md) zamiast jednostki usługi dla uprawnień. Jeśli potrzebujesz jednego z tych zasobów, zobacz następującą sekcję; w przeciwnym razie przejdź do sekcji [Tworzenie klastra AKS.](#create-an-aks-cluster)

### <a name="create-an-ssh-key-pair"></a>Tworzenie pary kluczy SSH

Aby uzyskać dostęp do węzłów AKS, należy połączyć się przy użyciu pary kluczy SSH. `ssh-keygen` Polecenie służy do generowania plików kluczy publicznych i prywatnych SSH. Domyślnie pliki te są tworzone w katalogu *~/.ssh.* Jeśli para kluczy SSH o tej samej nazwie istnieje w danej lokalizacji, pliki te są zastępowane.

Przejdź [https://shell.azure.com](https://shell.azure.com) do, aby otworzyć Cloud Shell w przeglądarce.

Następujące polecenie tworzy parę kluczy SSH przy użyciu szyfrowania RSA i długości bitowej 2048:

```console
ssh-keygen -t rsa -b 2048
```

Aby uzyskać więcej informacji na temat tworzenia kluczy SSH, zobacz [Tworzenie kluczy SSH i zarządzanie nimi do uwierzytelniania na platformie Azure][ssh-keys].

### <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

Jednostka usługi Azure Active Directory zezwala klastrowi usługi AKS na interakcje z innymi zasobami platformy Azure. Utwórz jednostkę usługi za pomocą polecenia [az ad sp create-for-rbac][az-ad-sp-create-for-rbac]. Parametr `--skip-assignment` umożliwia ograniczenie przypisywania dodatkowych uprawnień. Domyślnie ta jednostka usługi jest ważna przez rok. Należy zauważyć, że można użyć tożsamości zarządzanej zamiast jednostki usługi. Aby uzyskać więcej informacji, zobacz [Używanie tożsamości zarządzanych](use-managed-identity.md).

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Dane wyjściowe są podobne do poniższego przykładu:

```json
{
  "appId": "8b1ede42-d407-46c2-a1bc-6b213b04295f",
  "displayName": "azure-cli-2019-04-19-21-42-11",
  "name": "http://azure-cli-2019-04-19-21-42-11",
  "password": "27e5ac58-81b0-46c1-bd87-85b4ef622682",
  "tenant": "73f978cf-87f2-41bf-92ab-2e7ce012db57"
}
```

Zwróć uwagę na wartości *appId* i *password*. Te wartości są używane w kolejnych krokach.

## <a name="create-an-aks-cluster"></a>Tworzenie klastra AKS

### <a name="review-the-template"></a>Przejrzyj szablon

Szablon używany w tym przewodniku Szybki start pochodzi z [szablonów szybki start platformy Azure](https://azure.microsoft.com/resources/templates/101-aks/).

:::code language="json" source="~/quickstart-templates/101-aks/azuredeploy.json" range="1-126" highlight="86-118":::

Aby uzyskać więcej przykładów usługi AKS, zobacz witrynę [szablonów szybkiego startu usługi AKS.][aks-quickstart-templates]

### <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz poniższy obraz, aby zalogować się na platformie Azure i otworzyć szablon.

    [![Wdrażanie na platformie Azure](./media/kubernetes-walkthrough-rm-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aks%2Fazuredeploy.json)

2. Wybierz lub wprowadź następujące wartości.

    W przypadku tego przewodnika Szybki start pozostaw wartości domyślne dla *rozmiaru dysku systemu operacyjnego GB,* *liczby agentów,* *rozmiaru maszyny wirtualnej agenta,* *typu systemu operacyjnego*i *wersji kubernetes*. Podaj własne wartości dla następujących parametrów szablonu:

    * **Subskrypcja**: Wybierz subskrypcję platformy Azure.
    * **Grupa zasobów**: Wybierz **pozycję Utwórz nowy**. Wprowadź unikatową nazwę grupy zasobów, taką jak *myResourceGroup,* a następnie wybierz przycisk **OK**.
    * **Lokalizacja**: Wybierz lokalizację, na przykład **Wschodnie stany USA**.
    * **Nazwa klastra**: Wprowadź unikatową nazwę klastra AKS, taką jak *myAKSCluster*.
    * **Prefiks DNS:** Wprowadź unikatowy prefiks DNS klastra, taki jak *myakscluster*.
    * **Nazwa administratora Linuksa**: Wprowadź nazwę użytkownika, aby połączyć się za pomocą SSH, takich jak *azureuser*.
    * **Klucz publiczny SSH RSA**: Skopiuj i wklej *publiczną* część pary kluczy SSH (domyślnie zawartość *~/.ssh/id_rsa.pub).*
    * **Identyfikator klienta jednostki usługi:** Skopiuj i wklej *identyfikator appId* jednostki usługi z `az ad sp create-for-rbac` polecenia.
    * **Klucz tajny klienta głównego usługi:** skopiuj `az ad sp create-for-rbac` i wklej *hasło* jednostki usługi z polecenia.
    * **Wyrażam zgodę na powyższe warunki:** zaznacz to pole, w tym wyrażenie zgody.

    ![Szablon Menedżera zasobów do tworzenia klastra usługi Kubernetes platformy Azure w portalu](./media/kubernetes-walkthrough-rm-template/create-aks-cluster-using-template-portal.png)

3. Wybierz pozycję **Kup**.

Utworzenie klastra usługi AKS zajmuje kilka minut. Poczekaj na pomyślne wdrożenie klastra przed przejściem do następnego kroku.

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

### <a name="connect-to-the-cluster"></a>Łączenie z klastrem

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

Poniższy przykład danych wyjściowych pokazuje węzły utworzone w poprzednich krokach. Upewnij się, że stan wszystkich węzłów jest *gotowy:*

```output
NAME                       STATUS   ROLES   AGE     VERSION
aks-agentpool-41324942-0   Ready    agent   6m44s   v1.12.6
aks-agentpool-41324942-1   Ready    agent   6m46s   v1.12.6
aks-agentpool-41324942-2   Ready    agent   6m45s   v1.12.6
```

### <a name="run-the-application"></a>Uruchamianie aplikacji

Plik manifestu platformy Kubernetes definiuje żądany stan klastra, w tym informacje o obrazach kontenera do uruchomienia. W tym przewodniku Szybki start manifest służy do tworzenia wszystkich obiektów potrzebnych do uruchomienia aplikacji Azure Vote. Ten manifest obejmuje dwa [wdrożenia platformy Kubernetes][kubernetes-deployment] — jedno dla aplikacji Azure Vote w języku Python i jedno dla wystąpienia usługi Redis. Tworzone są również dwie [usługi platformy Kubernetes][kubernetes-service] — wewnętrzna usługa dla wystąpienia usługi Redis i zewnętrzna usługa służąca do uzyskiwania dostępu do aplikacji Azure Vote z Internetu.

> [!TIP]
> W tym przewodniku Szybki start ręcznie utworzysz i wdrożysz manifesty aplikacji w klastrze AKS. W bardziej rzeczywistych scenariuszach można użyć obszarów [Azure Dev Spaces][azure-dev-spaces], aby szybko iterować i debugować kod bezpośrednio w klastrze AKS. Obszarów Dev Spaces można używać na różnych platformach systemów operacyjnych i w środowiskach deweloperskich, aby pracować razem z innymi członkami zespołu.

Utwórz plik o nazwie `azure-vote.yaml` i skopiuj go do poniższej definicji YAML. Jeśli używasz usługi Azure Cloud Shell, ten plik można utworzyć przy użyciu programu `vi` lub `nano`, tak jak podczas pracy w systemie wirtualnym lub fizycznym:

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
        image: redis
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
        image: microsoft/azure-vote-front:v1
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

Wdróż aplikację przy użyciu polecenia [kubectl apply][kubectl-apply] i podaj nazwę manifestu YAML:

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

### <a name="test-the-application"></a>Testowanie aplikacji

Podczas uruchamiania aplikacji usługa Kubernetes uwidacznia fronton aplikacji w Internecie. Ten proces może potrwać kilka minut.

Aby monitorować postęp, użyj polecenia [kubectl get-service][kubectl-get] z argumentem `--watch`.

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

![Obraz przedstawiający przechodzenie do aplikacji Azure Vote](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy klaster nie będzie już potrzebny, usuń grupę zasobów, usługę kontenera i wszystkie pokrewne zasoby za pomocą polecenia [az group delete][az-group-delete].

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Po usunięciu klastra jednostka usługi Azure Active Directory używana przez klaster usługi AKS nie jest usuwana. Aby sprawdzić, jak usunąć jednostkę usługi, zobacz [AKS service principal considerations and deletion (Uwagi dotyczące jednostki usługi AKS i jej usuwanie)][sp-delete]. Jeśli użyto tożsamości zarządzanej, tożsamość jest zarządzana przez platformę i nie wymaga usuwania.

## <a name="get-the-code"></a>Uzyskiwanie kodu

W tym przewodniku Szybki start utworzono wdrożenie platformy Kubernetes za pomocą utworzonych wcześniej obrazów kontenerów. Powiązany kod aplikacji, plik Dockerfile i plik manifestu rozwiązania Kubernetes są dostępne w serwisie GitHub.

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wdrożono klaster Kubernetes oraz wdrożono w nim aplikację obsługującą wiele kontenerów. [Dostęp do pulpitu nawigacyjnego sieci Web kubernetes][kubernetes-dashboard] dla utworzonego klastra.

Aby dowiedzieć się więcej o usłudze AKS i poznać dokładnie proces od kompletnego kodu do wdrożenia, przejdź do samouczka dotyczącego klastra Kubernetes.

> [!div class="nextstepaction"]
> [Samouczek usługi AKS][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[aks-quickstart-templates]: https://azure.microsoft.com/resources/templates/?term=Azure+Kubernetes+Service

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: https://aka.ms/coingfonboarding
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks?view=azure-cli-latest#az-aks-browse
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[ssh-keys]: ../virtual-machines/linux/create-ssh-keys-detailed.md
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
