---
title: Tworzenie usługi Azure Kubernetes Service (AKS) za pomocą usługi Helm
description: Użyj helm z AKS i Azure Container Registry do pakowania i uruchamiania kontenerów aplikacji w klastrze.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 04/20/2020
ms.author: zarhoads
ms.openlocfilehash: 77627ab846999ea5ba42fde7a9c49b9cc7559fba
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81873435"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-helm"></a>Szybki start: tworzenie usługi Azure Kubernetes (AKS) za pomocą helma

[Helm][helm] to narzędzie do pakowania typu open source, które pomaga zainstalować cykl życia aplikacji Kubernetes i zarządzać nimi. Podobnie jak w przypadku menedżerów pakietów Linuksa, takich jak *APT* i *Yum,* Helm służy do zarządzania wykresami Kubernetes, które są pakietami wstępnie skonfigurowanych zasobów Kubernetes.

W tym artykule pokazano, jak używać Helm do pakowania i uruchamiania aplikacji w u usługi AKS. Aby uzyskać więcej informacji na temat instalowania istniejącej aplikacji przy użyciu funkcji Helm, zobacz [Instalowanie istniejących aplikacji z usługą Helm w programie AKS][helm-existing].

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free).
* [Zainstalowany interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).
* Docker zainstalowany i skonfigurowany. Środowisko Docker zawiera pakiety, które umożliwiają konfigurowanie platformy Docker w systemie [Mac][docker-for-mac], [Windows][docker-for-windows] lub [Linux][docker-for-linux].
* [Zainstalowany Helm v3][helm-install].

## <a name="create-an-azure-container-registry"></a>Tworzenie rejestru Azure Container Registry
Aby użyć helm do uruchamiania aplikacji w klastrze AKS, trzeba azure container registry do przechowywania obrazów kontenerów. W poniższym przykładzie użyto [az acr create][az-acr-create] do utworzenia acr o nazwie *MyHelmACR* w grupie zasobów *MyResourceGroup* z *podstawową* jednostką SKU. Należy podać własną unikatową nazwę rejestru. Nazwa rejestru musi być unikatowa w obrębie platformy Azure i może zawierać od 5 do 50 znaków alfanumerycznych. *Podstawowa* jednostka SKU to zoptymalizowany pod kątem kosztów punkt wejścia do celów programistycznych zapewniający równowagę między przestrzenią dyskową i przepływnością.

```azurecli
az group create --name MyResourceGroup --location eastus
az acr create --resource-group MyResourceGroup --name MyHelmACR --sku Basic
```

Dane wyjściowe będą podobne do poniższego przykładu. Zanotuj wartość *loginServer* dla swojego ACR, ponieważ będzie on używany w późniejszym kroku. W poniższym przykładzie *myhelmacr.azurecr.io* jest *loginServer* dla *MyHelmACR*.

```console
{
  "adminUserEnabled": false,
  "creationDate": "2019-06-11T13:35:17.998425+00:00",
  "id": "/subscriptions/<ID>/resourceGroups/MyResourceGroup/providers/Microsoft.ContainerRegistry/registries/MyHelmACR",
  "location": "eastus",
  "loginServer": "myhelmacr.azurecr.io",
  "name": "MyHelmACR",
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

Aby użyć wystąpienia ACR, musisz się najpierw zalogować. Użyj polecenia [az acr login,][az-acr-login] aby się zalogować. Poniższy przykład zaloguje się do ACR o nazwie *MyHelmACR*.

```azurecli
az acr login --name MyHelmACR
```

Polecenie zwraca komunikat *Pomyślnie logowania* po zakończeniu.

## <a name="create-an-azure-kubernetes-service-cluster"></a>Tworzenie klastra usługi Kubernetes platformy Azure

Tworzenie klastra AKS. Poniższe polecenie tworzy klaster AKS o nazwie MyAKS i dołącza MyHelmACR.

```azurecli
az aks create -g MyResourceGroup -n MyAKS --location eastus  --attach-acr MyHelmACR --generate-ssh-keys
```

Klaster AKS potrzebuje dostępu do usługi ACR, aby wyciągnąć obrazy kontenerów i uruchomić je. Powyższe polecenie udziela również dostępu klastra *MyAKS* do usługi *MyHelmACR* ACR.

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

## <a name="download-the-sample-application"></a>Pobieranie przykładowej aplikacji

Ten przewodnik Szybki start korzysta [z przykładowej aplikacji Node.js z przykładowego repozytorium usługi Azure Dev Spaces.][example-nodejs] Sklonuj aplikację z gitHub `dev-spaces/samples/nodejs/getting-started/webfrontend` i przejdź do katalogu.

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="create-a-dockerfile"></a>Tworzenie pliku dockerfile

Utwórz nowy plik *Dockerfile,* korzystając z następujących elementów:

```dockerfile
FROM node:latest

WORKDIR /webfrontend

COPY package.json ./

RUN npm install

COPY . .

EXPOSE 80
CMD ["node","server.js"]
```

## <a name="build-and-push-the-sample-application-to-the-acr"></a>Tworzenie i wypychanie przykładowej aplikacji do usługi ACR

Pobierz adres serwera logowania za pomocą polecenia [az acr list][az-acr-list] i zapytania dla *loginServer:*

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Użyj platformy Docker do tworzenia, oznaczania i wypychania przykładowego kontenera aplikacji do usługi ACR:

```console
docker build -t webfrontend:latest .
docker tag webfrontend <acrLoginServer>/webfrontend:v1
docker push <acrLoginServer>/webfrontend:v1
```

## <a name="create-your-helm-chart"></a>Tworzenie wykresu Helm

Wygeneruj `helm create` wykres Helm za pomocą polecenia.

```console
helm create webfrontend
```

Wprowadzanie następujących aktualizacji *webfrontend/values.yaml:*

* Zmień `image.repository` na`<acrLoginServer>/webfrontend`
* Zmień `service.type` na`LoadBalancer`

Przykład:

```yml
# Default values for webfrontend.
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

replicaCount: 1

image:
  repository: <acrLoginServer>/webfrontend
  pullPolicy: IfNotPresent
...
service:
  type: LoadBalancer
  port: 80
...
```

Aktualizacja `appVersion` `v1` do w *webfrontend/Chart.yaml*. Na przykład:

```yml
apiVersion: v2
name: webfrontend
...
# This is the version number of the application being deployed. This version number should be
# incremented each time you make changes to the application.
appVersion: v1
```

## <a name="run-your-helm-chart"></a>Uruchamianie wykresu Helm

Użyj `helm create` polecenia, aby zainstalować aplikację przy użyciu wykresu Helm.

```console
helm install webfrontend webfrontend/
```

Trwa kilka minut dla usługi do zwrócenia publicznego adresu IP. Aby monitorować postęp, `kubectl get service` użyj polecenia z parametrem *czujki:*

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
webfrontend         LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
webfrontend         LoadBalancer  10.0.141.72   <EXTERNAL-IP> 80:32150/TCP   7m
```

Przejdź do modułu równoważenia obciążenia `<EXTERNAL-IP>` aplikacji w przeglądarce, aby wyświetlić przykładową aplikację.

## <a name="delete-the-cluster"></a>Usuwanie klastra

Gdy klaster nie jest już potrzebny, użyj polecenia [delete grupy AZ,][az-group-delete] aby usunąć grupę zasobów, klaster AKS, rejestr kontenerów, przechowywane tam obrazy kontenerów i wszystkie powiązane zasoby.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> Po usunięciu klastra jednostka usługi Azure Active Directory używana przez klaster usługi AKS nie jest usuwana. Aby sprawdzić, jak usunąć jednostkę usługi, zobacz [AKS service principal considerations and deletion (Uwagi dotyczące jednostki usługi AKS i jej usuwanie)][sp-delete]. Jeśli użyto tożsamości zarządzanej, tożsamość jest zarządzana przez platformę i nie wymaga usuwania.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat korzystania z helm, zobacz dokumentację Helm.

> [!div class="nextstepaction"]
> [Dokumentacja steru][helm-documentation]

[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-list]: /cli/azure/acr#az-acr-list
[az-group-delete]: /cli/azure/group#az-group-delete
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli

[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[example-nodejs]: https://github.com/Azure/dev-spaces/tree/master/samples/nodejs/getting-started/webfrontend
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[helm]: https://helm.sh/
[helm-documentation]: https://helm.sh/docs/
[helm-existing]: kubernetes-helm.md
[helm-install]: https://helm.sh/docs/intro/install/
[sp-delete]: kubernetes-service-principal.md#additional-considerations
