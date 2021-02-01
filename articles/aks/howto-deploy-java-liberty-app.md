---
title: Wdróż aplikację Java z otwartą wolnością lub WebSphere wolnością w klastrze usługi Azure Kubernetes Service (AKS)
description: Wdróż aplikację Java z otwartą wolnością lub WebSphere wolnością w klastrze usługi Azure Kubernetes Service (AKS).
author: jiangma
ms.author: jiangma
ms.service: container-service
ms.topic: conceptual
ms.date: 02/01/2021
keywords: Java, jakartaee, JavaEE, mikroprofil, Open-wolność, WebSphere-wolność, AKS, Kubernetes
ms.openlocfilehash: 93ffa3ded4d0771438c5d6a2dc23e6e184f04fe2
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2021
ms.locfileid: "99227629"
---
# <a name="deploy-a-java-application-with-open-liberty-or-websphere-liberty-on-an-azure-kubernetes-service-aks-cluster"></a>Wdróż aplikację Java z otwartą wolnością lub WebSphere wolnością w klastrze usługi Azure Kubernetes Service (AKS)

W tym przewodniku przedstawiono sposób uruchamiania aplikacji Java, Java EE, [Dżakarta EE](https://jakarta.ee/)lub [mikroprofilu](https://microprofile.io/) w środowisku uruchomieniowym Open wolności lub WebSphere, a następnie wdrażania aplikacji kontenera w klastrze AKS przy użyciu operatora Open wolności. Operator "Open wolności" upraszcza wdrażanie aplikacji i zarządzanie nimi w ramach otwartych klastrów Kubernetes. Można również wykonywać bardziej zaawansowane operacje, takie jak zbieranie śladów i zrzutów przy użyciu operatora. Ten artykuł przeprowadzi Cię przez proces przygotowywania aplikacji wolności, tworzenia obrazu platformy Docker i uruchamiania aplikacji kontenera w klastrze AKS.  Aby uzyskać więcej informacji na temat otwartej wolności, zobacz [stronę Otwórz projekt wolności](https://openliberty.io/). Aby uzyskać więcej informacji na temat usługi IBM WebSphere wolności, zobacz [stronę z wolnością WebSphere](https://www.ibm.com/cloud/websphere-liberty).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* W tym artykule jest wymagana Najnowsza wersja interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.
* Jeśli polecenia w tym przewodniku są uruchamiane lokalnie (zamiast Azure Cloud Shell):
  * Przygotuj maszynę lokalną z zainstalowanym systemem operacyjnym UNIX (na przykład Ubuntu, macOS).
  * Instalowanie implementacji języka Java SE (na przykład [AdoptOpenJDK OpenJDK 8 LTS/OpenJ9](https://adoptopenjdk.net/?variant=openjdk8&jvmVariant=openj9)).
  * Zainstaluj program [Maven](https://maven.apache.org/download.cgi) 3.5.0 lub nowszy.
  * Zainstaluj [platformę Docker](https://docs.docker.com/get-docker/) dla systemu operacyjnego.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczna grupa przeznaczona do wdrażania zasobów platformy Azure i zarządzania nimi. Utwórz grupę zasobów, *środowisko Java-wolność-Project* przy użyciu polecenia [AZ Group Create](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_create) w lokalizacji *Wschodnie* . Zostanie ona użyta do utworzenia wystąpienia Azure Container Registry (ACR) i klastra AKS w późniejszym czasie. 

```azurecli-interactive
az group create --name java-liberty-project --location eastus
```

## <a name="create-an-acr-instance"></a>Tworzenie wystąpienia ACR

Użyj polecenia [AZ ACR Create](/cli/azure/acr?view=azure-cli-latest&preserve-view=true#az_acr_create) , aby utworzyć wystąpienie ACR. Poniższy przykład tworzy wystąpienie ACR o nazwie *youruniqueacrname*. Upewnij się, że *youruniqueacrname* jest unikatowy na platformie Azure.

```azurecli-interactive
az acr create --resource-group java-liberty-project --name youruniqueacrname --sku Basic --admin-enabled
```

Po krótkim czasie powinny zostać wyświetlone dane wyjściowe JSON zawierające:

```output
  "provisioningState": "Succeeded",
  "publicNetworkAccess": "Enabled",
  "resourceGroup": "java-liberty-project",
```

### <a name="connect-to-the-acr-instance"></a>Nawiązywanie połączenia z wystąpieniem ACR

Aby wypchnąć obraz do wystąpienia ACR, należy najpierw zalogować się do niego. Uruchom następujące polecenia, aby zweryfikować połączenie:

```azurecli-interactive
REGISTRY_NAME=youruniqueacrname
LOGIN_SERVER=$(az acr show -n $REGISTRY_NAME --query 'loginServer' -o tsv)
USER_NAME=$(az acr credential show -n $REGISTRY_NAME --query 'username' -o tsv)
PASSWORD=$(az acr credential show -n $REGISTRY_NAME --query 'passwords[0].value' -o tsv)

docker login $LOGIN_SERVER -u $USER_NAME -p $PASSWORD
```

Po `Login Succeeded` pomyślnym zalogowaniu się do wystąpienia ACR powinna zostać wyświetlona na końcu danych wyjściowych polecenia.

## <a name="create-an-aks-cluster"></a>Tworzenie klastra AKS

Utwórz klaster AKS za pomocą polecenia [az aks create](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_create). W poniższym przykładzie pokazano tworzenie klastra o nazwie *myAKSCluster* z jednym węzłem. Wykonanie tej czynności potrwa kilka minut.

```azurecli-interactive
az aks create --resource-group java-liberty-project --name myAKSCluster --node-count 1 --generate-ssh-keys --enable-managed-identity
```

Po kilku minutach polecenie zostanie wykonane i zwróci informacje w formacie JSON o klastrze, w tym następujące:

```output
  "nodeResourceGroup": "MC_java-liberty-project_myAKSCluster_eastus",
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "java-liberty-project",
```

### <a name="connect-to-the-aks-cluster"></a>Nawiązywanie połączenia z klastrem AKS

Aby zarządzać klastrem Kubernetes, należy użyć klienta wiersza polecenia usługi Kubernetes — narzędzia [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/). Jeśli korzystasz z usługi Azure Cloud Shell, narzędzie `kubectl` jest już zainstalowane. Aby zainstalować narzędzie `kubectl` lokalnie, użyj polecenia [az aks install-cli](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_install_cli):

```azurecli-interactive
az aks install-cli
```

Aby skonfigurować narzędzie `kubectl` w celu nawiązania połączenia z klastrem Kubernetes, użyj polecenia [az aks get-credentials](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_get_credentials). To polecenie powoduje pobranie poświadczeń i zastosowanie ich w konfiguracji interfejsu wiersza polecenia Kubernetes.

```azurecli-interactive
az aks get-credentials --resource-group java-liberty-project --name myAKSCluster --overwrite-existing
```

> [!NOTE]
> Powyższe polecenie używa domyślnej lokalizacji [pliku konfiguracji Kubernetes](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/) `~/.kube/config` . Możesz określić inną lokalizację pliku konfiguracji Kubernetes za pomocą polecenia *--File*.

Aby sprawdzić połączenie z klastrem, użyj polecenia [kubectl get]( https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get), aby powrócić do listy węzłów klastra.

```azurecli-interactive
kubectl get nodes
```

Poniższe przykładowe dane wyjściowe zawierają jeden węzeł utworzony w poprzednich krokach. Upewnij się, że stan węzła to *Gotowy*:

```output
NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-xxxxxxxx-yyyyyyyyyy   Ready    agent   76s     v1.18.10
```

## <a name="install-open-liberty-operator"></a>Zainstaluj otwarty operator wolności

Po utworzeniu i nawiązaniu połączenia z klastrem Zainstaluj program " [Open wolności](https://github.com/OpenLiberty/open-liberty-operator/tree/master/deploy/releases/0.7.0) ", uruchamiając następujące polecenia.

```azurecli-interactive
OPERATOR_NAMESPACE=default
WATCH_NAMESPACE='""'

# Install Custom Resource Definitions (CRDs) for OpenLibertyApplication
kubectl apply -f https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.0/openliberty-app-crd.yaml

# Install cluster-level role-based access
curl -L https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.0/openliberty-app-cluster-rbac.yaml \
      | sed -e "s/OPEN_LIBERTY_OPERATOR_NAMESPACE/${OPERATOR_NAMESPACE}/" \
      | kubectl apply -f -

# Install the operator
curl -L https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.0/openliberty-app-operator.yaml \
      | sed -e "s/OPEN_LIBERTY_WATCH_NAMESPACE/${WATCH_NAMESPACE}/" \
      | kubectl apply -n ${OPERATOR_NAMESPACE} -f -
```

## <a name="build-application-image"></a>Obraz aplikacji kompilacji

Aby wdrożyć i uruchomić aplikację z wolnością w klastrze AKS, konteneryzowanie aplikację jako obraz platformy Docker za pomocą [otwartych obrazów kontenerów](https://github.com/OpenLiberty/ci.docker) lub [WebSphere wolności](https://github.com/WASdev/ci.docker).

1. Sklonuj przykładowy kod dla tego przewodnika. Przykład znajduje się w serwisie [GitHub](https://github.com/Azure-Samples/open-liberty-on-aks).
1. Zmień katalog na `javaee-app-simple-cluster` lokalny klon.
1. Uruchom `mvn clean package` , aby spakować aplikację.
1. Uruchom jedno z następujących poleceń, aby skompilować obraz aplikacji i wypchnąć go do wystąpienia ACR.
   * Kompiluj przy użyciu podstawowego obrazu podstawy, jeśli wolisz używać Open wolności jako uproszczonego środowiska uruchomieniowego Java™ Open Source:

     ```azurecli-interactive
     # Build and tag application image. This will cause the ACR instance to pull the necessary Open Liberty base images.
     az acr build -t javaee-cafe-simple:1.0.0 -r $REGISTRY_NAME .
     ```

   * Jeśli wolisz korzystać z komercyjnej wersji otwartej wolności, Kompiluj z WebSphereą wolności:

     ```azurecli-interactive
     # Build and tag application image. This will cause the ACR instance to pull the necessary WebSphere Liberty base images.
     az acr build -t javaee-cafe-simple:1.0.0 -r $REGISTRY_NAME --file=Dockerfile-wlp .
     ```

## <a name="deploy-application-on-the-aks-cluster"></a>Wdrażanie aplikacji w klastrze AKS

Wykonaj poniższe kroki, aby wdrożyć aplikację w klastrze AKS.

1. Utwórz klucz tajny ściągania, aby klaster AKS został uwierzytelniony do ściągania obrazu z wystąpienia ACR.

   ```azurecli-interactive
   kubectl create secret docker-registry acr-secret \
      --docker-server=${LOGIN_SERVER} \
      --docker-username=${USER_NAME} \
      --docker-password=${PASSWORD}
   ```

1. Sprawdź, czy bieżący katalog roboczy jest `javaee-app-simple-cluster` używany do lokalnego klonowania.
1. Uruchom następujące polecenia, aby wdrożyć aplikację dla wolności z 3 replikami w klastrze AKS. Dane wyjściowe polecenia są również wyświetlane w tekście.

   ```azurecli-interactive
   # Create OpenLibertyApplication "javaee-app-simple-cluster"
   cat openlibertyapplication.yaml | sed -e "s/\${Container_Registry_URL}/${LOGIN_SERVER}/g" | sed -e "s/\${REPLICAS}/3/g" | kubectl apply -f -

   openlibertyapplication.openliberty.io/javaee-app-simple-cluster created

   # Check if OpenLibertyApplication instance is created
   kubectl get openlibertyapplication javaee-app-simple-cluster

   NAME                        IMAGE                                                   EXPOSED   RECONCILED   AGE
   javaee-app-simple-cluster   youruniqueacrname.azurecr.io/javaee-cafe-simple:1.0.0             True         59s

   # Check if deployment created by Operator is ready
   kubectl get deployment javaee-app-simple-cluster --watch

   NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
   javaee-app-simple-cluster   0/3     3            0           20s
   ```

1. Zaczekaj, aż zobaczysz `3/3` poniżej `READY` kolumny i `3` pod `AVAILABLE` kolumną, użyj, `CTRL-C` Aby zatrzymać `kubectl` proces czujki.

### <a name="test-the-application"></a>Testowanie aplikacji

Po uruchomieniu aplikacji usługa równoważenia obciążenia Kubernetes udostępnia fronton aplikacji w Internecie. Ten proces może potrwać trochę czasu.

Aby monitorować postęp, użyj polecenia [kubectl get-service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) z argumentem `--watch`.

```azurecli-interactive
kubectl get service javaee-app-simple-cluster --watch

NAME                        TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)          AGE
javaee-app-simple-cluster   LoadBalancer   10.0.251.169   52.152.189.57   9080:31732/TCP   68s
```

Poczekaj, aż *zewnętrzny adres IP* zmieni się z *oczekującej* na rzeczywisty publiczny adres IP, użyj, `CTRL-C` Aby zatrzymać `kubectl` proces czujki.

Otwórz przeglądarkę internetową na zewnętrznym adresie IP i porcie usługi ( `52.152.189.57:9080` na potrzeby powyższego przykładu), aby wyświetlić stronę główną aplikacji. W lewym górnym rogu strony powinna zostać wyświetlona nazwa pod nazwą replik aplikacji. Zaczekaj kilka minut i Odśwież stronę, prawdopodobnie zobaczysz inną nazwę pod nazwą wyświetlaną z powodu równoważenia obciążenia zapewnionego przez klaster AKS.

:::image type="content" source="./media/howto-deploy-java-liberty-app/java-liberty-app-aks-deployed-success.png" alt-text="Aplikacja wolności Java została pomyślnie wdrożona na AKS":::

>[!NOTE]
> - Obecnie aplikacja nie korzysta z protokołu HTTPS. Zaleca się [włączenie protokołu TLS z własnymi certyfikatami](ingress-own-tls.md).

## <a name="clean-up-the-resources"></a>Oczyszczanie zasobów

Aby uniknąć naliczania opłat za platformę Azure, należy wyczyścić zasoby niepotrzebne.  Gdy klaster nie jest już wymagany, użyj polecenia [AZ Group Delete](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_delete) , aby usunąć grupę zasobów, usługę kontenera, rejestr kontenerów i wszystkie powiązane zasoby.

```azurecli-interactive
az group delete --name java-liberty-project --yes --no-wait
```

## <a name="next-steps"></a>Następne kroki

Więcej informacji można znaleźć w tematach w tym przewodniku:

* [Azure Kubernetes Service](https://azure.microsoft.com/free/services/kubernetes-service/)
* [Open wolności](https://openliberty.io/)
* [Otwarty operator wolności](https://github.com/OpenLiberty/open-liberty-operator)
* [Otwórz konfigurację serwera z wolnością](https://openliberty.io/docs/ref/config/)
* [Wtyczka z Maveną wolności](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin)
* [Otwieranie obrazów kontenera wolności](https://github.com/OpenLiberty/ci.docker)
* [WebSphereowe obrazy kontenerów](https://github.com/WASdev/ci.docker)
