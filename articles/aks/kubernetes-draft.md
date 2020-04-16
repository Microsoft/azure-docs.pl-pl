---
title: Tworzenie usługi Azure Kubernetes (AKS) przy 1
description: Użyj wersji roboczej z usługą AKS i azure container registry do pakowania i uruchamiania kontenerów aplikacji w klastrze.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 06/20/2019
ms.author: zarhoads
ms.openlocfilehash: 2d69676121fcb26f7d2f796e6de42435447084cd
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392750"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-draft"></a>Szybki start: tworzenie usługi Azure Kubernetes (AKS) przy 1

Wersja robocza to narzędzie typu open source, które pomaga pakować i uruchamiać kontenery aplikacji w klastrze Kubernetes. W wersji roboczej można szybko ponownie wdrożyć aplikację do kubernetes jak zmiany kodu występują bez konieczności zatwierdzania zmian do kontroli wersji. Aby uzyskać więcej informacji na temat wersji roboczej, zobacz [dokumentację roboczą w usłudze GitHub][draft-documentation].

W tym artykule pokazano, jak używać wersji roboczej do pakowania i uruchamiania aplikacji w u usługi AKS.


## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free).
* [Zainstalowany interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).
* Docker zainstalowany i skonfigurowany. Środowisko Docker zawiera pakiety, które umożliwiają konfigurowanie platformy Docker w systemie [Mac][docker-for-mac], [Windows][docker-for-windows] lub [Linux][docker-for-linux].
* [Helm v2 zainstalowany][helm-install].
* [Wersja robocza zainstalowana][draft-documentation].

## <a name="create-an-azure-kubernetes-service-cluster"></a>Tworzenie klastra usługi Kubernetes platformy Azure

Tworzenie klastra AKS. Poniższe polecenia tworzą grupę zasobów o nazwie MyResourceGroup i klaster AKS o nazwie MyAKS.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --generate-ssh-keys
```

## <a name="create-an-azure-container-registry"></a>Tworzenie rejestru Azure Container Registry
Aby użyć wersji roboczej do uruchamiania aplikacji w klastrze AKS, potrzebujesz rejestru kontenerów platformy Azure do przechowywania obrazów kontenerów. W poniższym przykładzie użyto [az acr create][az-acr-create] do utworzenia acr o nazwie *MyDraftACR* w grupie zasobów *MyResourceGroup* z *podstawową* jednostką SKU. Należy podać własną unikatową nazwę rejestru. Nazwa rejestru musi być unikatowa w obrębie platformy Azure i może zawierać od 5 do 50 znaków alfanumerycznych. *Podstawowa* jednostka SKU to zoptymalizowany pod kątem kosztów punkt wejścia do celów programistycznych zapewniający równowagę między przestrzenią dyskową i przepływnością.

```azurecli
az acr create --resource-group MyResourceGroup --name MyDraftACR --sku Basic
```

Dane wyjściowe będą podobne do poniższego przykładu. Zanotuj wartość *loginServer* dla swojego ACR, ponieważ będzie on używany w późniejszym kroku. W poniższym przykładzie *mydraftacr.azurecr.io* jest *loginServer* dla *MyDraftACR*.

```console
{
  "adminUserEnabled": false,
  "creationDate": "2019-06-11T13:35:17.998425+00:00",
  "id": "/subscriptions/<ID>/resourceGroups/MyResourceGroup/providers/Microsoft.ContainerRegistry/registries/MyDraftACR",
  "location": "eastus",
  "loginServer": "mydraftacr.azurecr.io",
  "name": "MyDraftACR",
  "networkRuleSet": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "MyResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```


Aby wersja robocza używała wystąpienia usługi ACR, należy się najpierw zalogować. Użyj polecenia [az acr login,][az-acr-login] aby się zalogować. Poniższy przykład zaloguje się do ACR o nazwie *MyDraftACR*.

```azurecli
az acr login --name MyDraftACR
```

Polecenie zwraca komunikat *Pomyślnie logowania* po zakończeniu.

## <a name="create-trust-between-aks-cluster-and-acr"></a>Tworzenie zaufania między klastrem AKS a acr

Klaster AKS również potrzebuje dostępu do usługi ACR, aby wyciągnąć obrazy kontenerów i uruchomić je. Zezwalasz na dostęp do acr z AKS przez ustanowienie zaufania. Aby ustanowić zaufanie między klastrem AKS a rejestrem usługi ACR, należy udzielić uprawnień dla jednostki usługi Azure Active Directory używanej przez klaster AKS do uzyskiwania dostępu do rejestru ACR. Poniższe polecenia przyznają uprawnienia podmiotowi usługi klastra *MyAKS* w *grupie MyResourceGroup* do *usługi MyDraftACR* ACR w *grupie MyResourceGroup*.

```azurecli
# Get the service principal ID of your AKS cluster
AKS_SP_ID=$(az aks show --resource-group MyResourceGroup --name MyAKS --query "servicePrincipalProfile.clientId" -o tsv)

# Get the resource ID of your ACR instance
ACR_RESOURCE_ID=$(az acr show --resource-group MyResourceGroup --name MyDraftACR --query "id" -o tsv)

# Create a role assignment for your AKS cluster to access the ACR instance
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

## <a name="connect-to-your-aks-cluster"></a>Łączenie się z klastrem AKS

Aby nawiązać połączenie z klastrem Kubernetes z komputera lokalnego, należy użyć narzędzia [kubectl][kubectl], czyli klienta wiersza polecenia usługi Kubernetes.

Jeśli korzystasz z usługi Azure Cloud Shell, narzędzie `kubectl` jest już zainstalowane. Możesz także zainstalować je lokalnie za pomocą polecenia [az aks install-cli][]:

```azurecli
az aks install-cli
```

Aby skonfigurować narzędzie `kubectl` w celu nawiązania połączenia z klastrem Kubernetes, użyj polecenia [az aks get-credentials][]. Poniższy przykład pobiera poświadczenia dla klastra AKS o nazwie *MyAKS* w *MyResourceGroup:*

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

## <a name="create-a-service-account-for-helm"></a>Tworzenie konta usługi dla helmu

Przed wdrożeniem helm w klastrze AKS z włączoną funkcją RBAC, należy konto usługi i powiązanie roli dla usługi Tiller. Aby uzyskać więcej informacji na temat zabezpieczania helm / tiller w klastrze z włączoną funkcją RBAC, zobacz [Tiller, Przestrzenie nazw i RBAC][tiller-rbac]. Jeśli klaster AKS nie jest włączony RBAC, pomiń ten krok.

Utwórz plik `helm-rbac.yaml` o nazwie i skopiuj w następującym pliku YAML:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

Utwórz powiązanie konta usługi `kubectl apply` i roli za pomocą polecenia:

```console
kubectl apply -f helm-rbac.yaml
```

## <a name="configure-helm"></a>Konfigurowanie helma
Aby wdrożyć podstawowy kultywator w klastrze AKS, użyj polecenia [init helm.][helm-init] Jeśli klaster nie jest włączony RBAC, usuń `--service-account` argument i wartość.

```console
helm init --service-account tiller --node-selectors "beta.kubernetes.io/os"="linux"
```

## <a name="configure-draft"></a>Konfigurowanie wersji roboczej

Jeśli wersja robocza nie została skonfigurowana `draft init`na komputerze lokalnym, uruchom:

```console
$ draft init
Installing default plugins...
Installation of default plugins complete
Installing default pack repositories...
...
Happy Sailing!
```

Musisz również skonfigurować Draft, aby korzystać z *loginServer* swojego ACR. Następujące polecenie używa `draft config set` do `mydraftacr.azurecr.io` użycia jako rejestru.

```console
draft config set registry mydraftacr.azurecr.io
```

Wersja robocza została skonfigurowana do używania usługi ACR, a wersja robocza może wypychać obrazy kontenerów do usługi ACR. Gdy wersja robocza uruchamia aplikację w klastrze AKS, żadne hasła ani wpisy tajne nie są wymagane do wypychania lub ściągania z rejestru usługi ACR. Ponieważ między klastrem AKS a usługą ACR utworzono zaufanie, uwierzytelnianie odbywa się na poziomie usługi Azure Resource Manager przy użyciu usługi Azure Active Directory.

## <a name="download-the-sample-application"></a>Pobieranie przykładowej aplikacji

Ten szybki start używa [przykładowej aplikacji Java z repozytorium GitHub draft][example-java]. Sklonuj aplikację z gitHub `draft/examples/example-java/` i przejdź do katalogu.

```console
git clone https://github.com/Azure/draft
cd draft/examples/example-java/
```

## <a name="run-the-sample-application-with-draft"></a>Uruchamianie przykładowej aplikacji za pomocą wersji roboczej

Użyj `draft create` polecenia, aby przygotować aplikację.

```console
draft create
```

To polecenie tworzy artefakty, które są używane do uruchamiania aplikacji w klastrze Kubernetes. Elementy te obejmują plik Dockerfile, wykres Helm i plik *draft.toml,* który jest plikiem konfiguracji Wersja robocza.

```console
$ draft create

--> Draft detected Java (92.205567%)
--> Ready to sail
```

Aby uruchomić przykładową aplikację w klastrze `draft up` AKS, użyj polecenia.

```console
draft up
```

To polecenie tworzy plik Dockerfile, aby utworzyć obraz kontenera, wypycha obraz do usługi ACR i instaluje wykres Helm, aby uruchomić aplikację w aks. Przy pierwszym uruchomieniu tego polecenia naciśnięcie i pociągnięcie obrazu kontenera może zająć trochę czasu. Po buforowaniu warstw podstawowych znacznie skrócić czas wdrożenia aplikacji.

```
$ draft up

Draft Up Started: 'example-java': 01CMZAR1F4T1TJZ8SWJQ70HCNH
example-java: Building Docker Image: SUCCESS ⚓  (73.0720s)
example-java: Pushing Docker Image: SUCCESS ⚓  (19.5727s)
example-java: Releasing Application: SUCCESS ⚓  (4.6979s)
Inspect the logs with `draft logs 01CMZAR1F4T1TJZ8SWJQ70HCNH`
```

## <a name="connect-to-the-running-sample-application-from-your-local-machine"></a>Łączenie się z uruchomiającą przykładową aplikacją z komputera lokalnego

Aby przetestować aplikację, `draft connect` użyj polecenia.

```console
draft connect
```

To polecenie proxy bezpieczne połączenie z zasobnikiem Kubernetes. Po zakończeniu, aplikacja jest dostępna pod podanym adresem URL.

```console
$ draft connect

Connect to java:4567 on localhost:49804
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567
```

Przejdź do aplikacji w przeglądarce przy użyciu adresu URL, `localhost` aby wyświetlić przykładową aplikację. W powyższym przykładzie `http://localhost:49804`adres URL to . Zatrzymaj połączenie `Ctrl+c`za pomocą pliku .

## <a name="access-the-application-on-the-internet"></a>Dostęp do aplikacji w Internecie

W poprzednim kroku utworzono połączenie serwera proxy z zasobnikiem aplikacji w klastrze AKS. Podczas opracowywania i testowania aplikacji można udostępnić aplikację w Internecie. Aby udostępnić aplikację w Internecie, można utworzyć usługę Kubernetes z typem [LoadBalancer][kubernetes-service-loadbalancer].

Zaktualizuj, `charts/example-java/values.yaml` aby utworzyć usługę *LoadBalancer.* Zmień wartość *service.type* z *ClusterIP* na *LoadBalancer*.

```yaml
...
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
...
```

Zapisz zmiany, zamknij plik i `draft up` uruchom ponownie aplikację.

```console
draft up
```

Trwa kilka minut dla usługi do zwrócenia publicznego adresu IP. Aby monitorować postęp, `kubectl get service` użyj polecenia z parametrem *czujki:*

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
example-java-java   LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
example-java-java   LoadBalancer   10.0.141.72   52.175.224.118  80:32150/TCP   7m
```

Przejdź do modułu równoważenia obciążenia aplikacji w przeglądarce przy użyciu *adresu IP zewnętrznego,* aby wyświetlić przykładową aplikację. W powyższym przykładzie `52.175.224.118`adres IP jest .

## <a name="iterate-on-the-application"></a>Iteracji na wniosek

Możesz iterować aplikację, dokonując zmian lokalnie i ponownie wykonując . `draft up`

Aktualizacja wiadomości zwróconej na [linii 7 src/main/java/helloworld/Hello.java][example-java-hello-l7]

```java
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
```

Uruchom `draft up` polecenie, aby ponownie wdrożyć aplikację:

```console
$ draft up

Draft Up Started: 'example-java': 01CMZC9RF0TZT7XPWGFCJE15X4
example-java: Building Docker Image: SUCCESS ⚓  (25.0202s)
example-java: Pushing Docker Image: SUCCESS ⚓  (7.1457s)
example-java: Releasing Application: SUCCESS ⚓  (3.5773s)
Inspect the logs with `draft logs 01CMZC9RF0TZT7XPWGFCJE15X4`
```

Aby wyświetlić zaktualizowaną aplikację, przejdź ponownie do adresu IP modułu równoważenia obciążenia i sprawdź, czy zostaną wyświetlone zmiany.

## <a name="delete-the-cluster"></a>Usuwanie klastra

Gdy klaster nie jest już potrzebny, użyj polecenia [delete grupy AZ,][az-group-delete] aby usunąć grupę zasobów, klaster AKS, rejestr kontenerów, przechowywane tam obrazy kontenerów i wszystkie powiązane zasoby.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> Po usunięciu klastra jednostka usługi Azure Active Directory używana przez klaster usługi AKS nie jest usuwana. Aby sprawdzić, jak usunąć jednostkę usługi, zobacz [AKS service principal considerations and deletion (Uwagi dotyczące jednostki usługi AKS i jej usuwanie)][sp-delete]. Jeśli użyto tożsamości zarządzanej, tożsamość jest zarządzana przez platformę i nie wymaga usuwania.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat korzystania z wersji roboczej, zobacz wersji roboczej dokumentacji w usłudze GitHub.

> [!div class="nextstepaction"]
> [Wersja robocza dokumentacji][draft-documentation]


[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-create]: /cli/azure/acr#az-acr-login
[az-group-delete]: /cli/azure/group#az-group-delete
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[kubernetes-ingress]: ./ingress-basic.md

[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[draft-documentation]: https://github.com/Azure/draft/tree/master/docs
[example-java]: https://github.com/Azure/draft/tree/master/examples/example-java
[example-java-hello-l7]: https://github.com/Azure/draft/blob/master/examples/example-java/src/main/java/helloworld/Hello.java#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubernetes-service-loadbalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer
[helm-init]: https://v2.helm.sh/docs/helm/#helm-init
[helm-install]: https://v2.helm.sh/docs/using_helm/#installing-helm
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[tiller-rbac]: https://v2.helm.sh/docs/using_helm/#tiller-namespaces-and-rbac
