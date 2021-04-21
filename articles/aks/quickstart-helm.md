---
title: Opracowywanie na platformie Azure Kubernetes Service (AKS) za pomocą helm
description: Użyj programu Helm z usługą AKS Azure Container Registry, aby spakować i uruchomić kontenery aplikacji w klastrze.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 03/15/2021
ms.author: zarhoads
ms.openlocfilehash: e293d0c58f265b25f3df0a218f84888467468f59
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767497"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-helm"></a>Szybki start: opracowywanie w Azure Kubernetes Service (AKS) za pomocą helm

[Helm][helm] to narzędzie do tworzenia pakietów typu open source, które ułatwia instalowanie cyklu życia aplikacji Kubernetes i zarządzanie nimi. Podobnie jak menedżery pakietów systemu Linux, takie jak *APT* i *Yum,* program Helm zarządza wykresami Kubernetes, które są pakietami wstępnie skonfigurowanych zasobów kubernetes.

W tym przewodniku Szybki start użyjesz programu Helm, aby spakować i uruchomić aplikację w uakiecie AKS. Aby uzyskać więcej informacji na temat instalowania istniejącej aplikacji przy użyciu programu Helm, zobacz przewodnik z instalowania istniejących aplikacji za pomocą programu Helm w u usługi [AKS.][helm-existing]

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free).
* [Zainstalowany interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
* [Program Helm w wersji 3 został zainstalowany.][helm-install]

## <a name="create-an-azure-container-registry"></a>Tworzenie rejestru Azure Container Registry
Aby uruchomić aplikację w klastrze usługi AKS przy użyciu programu Helm, musisz przechowywać obrazy kontenerów w Azure Container Registry (ACR). Podaj własną nazwę rejestru unikatową w obrębie platformy Azure i zawierającą od 5 do 50 znaków alfanumerycznych. *Podstawowa* jednostka SKU to zoptymalizowany pod kątem kosztów punkt wejścia do celów programistycznych zapewniający równowagę między przestrzenią dyskową i przepływnością.

W poniższym przykładzie użyto [az acr create][az-acr-create] w celu utworzenia usługi ACR o nazwie *MyHelmACR* w *grupie MyResourceGroup* z *podstawową* sku.

```azurecli
az group create --name MyResourceGroup --location eastus
az acr create --resource-group MyResourceGroup --name MyHelmACR --sku Basic
```

Dane wyjściowe będą podobne do poniższego przykładu. Zanotuj wartość *loginServer* dla usługi ACR, ponieważ użyjesz jej w późniejszym kroku. W poniższym przykładzie *wartością myhelmacr.azurecr.io* *loginServer* dla *myHelmACR.*

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

## <a name="create-an-aks-cluster"></a>Tworzenie klastra AKS

Nowy klaster usługi AKS musi mieć dostęp do usługi ACR, aby ściągać obrazy kontenerów i uruchamiać je. Użyj następującego polecenia, aby:
* Utwórz klaster usługi AKS o nazwie *MyAKS* i dołącz *klaster MyHelmACR.*
* Przyznaj *klastrowi MyAKS* dostęp do usługi *MyHelmACR* ACR.


```azurecli
az aks create -g MyResourceGroup -n MyAKS --location eastus  --attach-acr MyHelmACR --generate-ssh-keys
```

## <a name="connect-to-your-aks-cluster"></a>Nawiązywanie połączenia z klastrem usługi AKS

Aby połączyć klaster Kubernetes lokalnie, użyj klienta wiersza polecenia Kubernetes, [kubectl][kubectl]. `kubectl` program jest już zainstalowany, jeśli używasz Azure Cloud Shell. 

1. Zainstaluj `kubectl` lokalnie przy użyciu `az aks install-cli` polecenia :

    ```azurecli
    az aks install-cli
    ```

2. Skonfiguruj polecenie , aby nawiązać połączenie `kubectl` z klastrem Kubernetes. `az aks get-credentials` Poniższy przykład polecenia pobiera poświadczenia dla klastra usługi AKS o nazwie *MyAKS* w *grupie MyResourceGroup:*  

    ```azurecli
    az aks get-credentials --resource-group MyResourceGroup --name MyAKS
    ```

## <a name="download-the-sample-application"></a>Pobieranie przykładowej aplikacji

W tym przewodniku Szybki start [jest Node.js przykładowa aplikacja z Azure Dev Spaces przykładowego repozytorium][example-nodejs]. Sklonuj aplikację z usługi GitHub i przejdź do `dev-spaces/samples/nodejs/getting-started/webfrontend` katalogu .

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="create-a-dockerfile"></a>Tworzenie pliku Dockerfile

Utwórz nowy plik *Dockerfile* przy użyciu następujących poleceń:

```dockerfile
FROM node:latest

WORKDIR /webfrontend

COPY package.json ./

RUN npm install

COPY . .

EXPOSE 80
CMD ["node","server.js"]
```

## <a name="build-and-push-the-sample-application-to-the-acr"></a>Kompilowanie i wypychanie przykładowej aplikacji do ACR

Używając poprzedniego pliku Dockerfile, uruchom [polecenie az acr build,][az-acr-build] aby skompilować i wypchnąć obraz do rejestru. Na końcu polecenia ustawia lokalizację pliku `.` Dockerfile (w tym przypadku bieżącego katalogu).

```azurecli
az acr build --image webfrontend:v1 \
  --registry MyHelmACR \
  --file Dockerfile .
```

## <a name="create-your-helm-chart"></a>Tworzenie wykresu helm

Wygeneruj wykres programu Helm za pomocą `helm create` polecenia .

```console
helm create webfrontend
```

Zaktualizuj *pliku webfrontend/values.yaml:*
* Zastąp wartość loginServer rejestru zanotną we wcześniejszym kroku, na przykład myhelmacr.azurecr.io *.*
* Zmień `image.repository` na `<loginServer>/webfrontend`
* Zmień `service.type` na `LoadBalancer`

Na przykład:

```yml
# Default values for webfrontend.
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

replicaCount: 1

image:
  repository: myhelmacr.azurecr.io/webfrontend
  pullPolicy: IfNotPresent
...
service:
  type: LoadBalancer
  port: 80
...
```

Zaktualizuj `appVersion` do w pliku `v1` *webfrontend/Chart.yaml*. Na przykład

```yml
apiVersion: v2
name: webfrontend
...
# This is the version number of the application being deployed. This version number should be
# incremented each time you make changes to the application.
appVersion: v1
```

## <a name="run-your-helm-chart"></a>Uruchamianie wykresu helm

Zainstaluj aplikację przy użyciu pakietu Helm za pomocą `helm install` polecenia .

```console
helm install webfrontend webfrontend/
```

Usługa zwraca publiczny adres IP po kilku minutach. Monitoruj postęp za `kubectl get service` pomocą polecenia z `--watch` argumentem .

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
webfrontend         LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
webfrontend         LoadBalancer  10.0.141.72   <EXTERNAL-IP> 80:32150/TCP   7m
```

Przejdź do usługi równoważenia obciążenia aplikacji w przeglądarce przy użyciu narzędzia , `<EXTERNAL-IP>` aby wyświetlić przykładową aplikację.

## <a name="delete-the-cluster"></a>Usuwanie klastra

Użyj polecenia [az group delete,][az-group-delete] aby usunąć grupę zasobów, klaster usługi AKS, rejestr kontenerów, obrazy kontenerów przechowywane w UCR i wszystkie powiązane zasoby.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> Po usunięciu klastra jednostka usługi Azure Active Directory używana przez klaster usługi AKS nie jest usuwana. Aby sprawdzić, jak usunąć jednostkę usługi, zobacz [AKS service principal considerations and deletion (Uwagi dotyczące jednostki usługi AKS i jej usuwanie)][sp-delete].
> 
> Jeśli używasz tożsamości zarządzanej, tożsamość jest zarządzana przez platformę i nie wymaga usunięcia.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat korzystania z programu Helm, zobacz dokumentację programu Helm.

> [!div class="nextstepaction"]
> [Dokumentacja programu Helm][helm-documentation]

[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-group-delete]: /cli/azure/group#az_group_delete
[az aks get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az aks install-cli]: /cli/azure/aks#az_aks_install_cli
[example-nodejs]: https://github.com/Azure/dev-spaces/tree/master/samples/nodejs/getting-started/webfrontend
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[helm]: https://helm.sh/
[helm-documentation]: https://helm.sh/docs/
[helm-existing]: kubernetes-helm.md
[helm-install]: https://helm.sh/docs/intro/install/
[sp-delete]: kubernetes-service-principal.md#additional-considerations
