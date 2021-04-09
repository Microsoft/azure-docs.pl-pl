---
title: Opracowywanie w usłudze Azure Kubernetes Service (AKS) przy użyciu usługi Helm
description: Używaj Helm z AKS i Azure Container Registry, aby spakować i uruchamiać kontenery aplikacji w klastrze.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 03/15/2021
ms.author: zarhoads
ms.openlocfilehash: 4f5232920853908aa5ad714313ead201494caa0d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103493085"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-helm"></a>Szybki Start: Programowanie w usłudze Azure Kubernetes Service (AKS) przy użyciu usługi Helm

[Helm][helm] to narzędzie do tworzenia pakietów typu "open source", które ułatwia Instalowanie i zarządzanie cyklem życia aplikacji Kubernetes. Podobnie jak w przypadku menedżerów pakietów systemu Linux, takich jak *apt* i *yum*, Helm zarządza wykresami Kubernetes, które są pakietami wstępnie skonfigurowanych zasobów Kubernetes.

W tym przewodniku szybki start będziesz używać Helm do pakowania i uruchamiania aplikacji na AKS. Aby uzyskać więcej informacji na temat instalowania istniejącej aplikacji przy użyciu programu Helm, zobacz temat [Instalowanie istniejących aplikacji z Helm w programie AKS][helm-existing] .

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free).
* [Zainstalowany interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
* [Helm v3][helm-install].

## <a name="create-an-azure-container-registry"></a>Tworzenie rejestru Azure Container Registry
Obrazy kontenerów należy przechowywać w Azure Container Registry (ACR), aby uruchamiać aplikację w klastrze AKS przy użyciu Helm. Podaj własną nazwę rejestru unikatową w ramach platformy Azure i zawierającą 5-50 znaków alfanumerycznych. *Podstawowa* jednostka SKU to zoptymalizowany pod kątem kosztów punkt wejścia do celów programistycznych zapewniający równowagę między przestrzenią dyskową i przepływnością.

Poniższy przykład używa [AZ ACR Create][az-acr-create] , aby utworzyć ACR o nazwie *MyHelmACR* w liście *zasobów* z *podstawową* jednostką SKU.

```azurecli
az group create --name MyResourceGroup --location eastus
az acr create --resource-group MyResourceGroup --name MyHelmACR --sku Basic
```

Dane wyjściowe będą podobne do poniższego przykładu. Zanotuj wartość *loginServer* dla ACR, ponieważ będziesz jej używać w późniejszym kroku. W poniższym przykładzie *myhelmacr.azurecr.IO* jest *loginServer* dla *myhelmacr*.

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

Nowy klaster AKS wymaga dostępu do ACR, aby ściągnąć obrazy kontenerów i je uruchomić. Użyj następującego polecenia, aby:
* Utwórz klaster AKS o nazwie *MyAKS* i Dołącz *MyHelmACR*.
* Udziel klastrowi *MyAKS* dostępu do *MyHelmACR* ACR.


```azurecli
az aks create -g MyResourceGroup -n MyAKS --location eastus  --attach-acr MyHelmACR --generate-ssh-keys
```

## <a name="connect-to-your-aks-cluster"></a>Nawiązywanie połączenia z klastrem AKS

Aby lokalnie połączyć klaster Kubernetes, należy użyć Kubernetes wiersza polecenia klienta [polecenia kubectl][kubectl]. `kubectl` jest już zainstalowany, jeśli używasz Azure Cloud Shell. 

1. Zainstaluj `kubectl` lokalnie przy użyciu `az aks install-cli` polecenia:

    ```azurecli
    az aks install-cli
    ```

2. Skonfiguruj, `kubectl` Aby nawiązać połączenie z klastrem Kubernetes przy użyciu `az aks get-credentials` polecenia. Następujące polecenie przykład pobiera poświadczenia dla klastra AKS o nazwie *MyAKS* w liście *zasobów*:  

    ```azurecli
    az aks get-credentials --resource-group MyResourceGroup --name MyAKS
    ```

## <a name="download-the-sample-application"></a>Pobieranie przykładowej aplikacji

Ten przewodnik Szybki Start używa [przykładowej aplikacji Node.js z repozytorium przykładowego Azure dev Spaces][example-nodejs]. Sklonuj aplikację z witryny GitHub i przejdź do `dev-spaces/samples/nodejs/getting-started/webfrontend` katalogu.

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="create-a-dockerfile"></a>Tworzenie pliku Dockerfile

Utwórz nowy plik *pliku dockerfile* za pomocą następujących poleceń:

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

Korzystając z powyższego pliku dockerfile, uruchom polecenie [AZ ACR Build][az-acr-build] , aby skompilować i wypchnąć obraz do rejestru. `.`Na końcu polecenia ustawia lokalizację pliku dockerfile (w tym przypadku bieżący katalog).

```azurecli
az acr build --image webfrontend:v1 \
  --registry MyHelmACR \
  --file Dockerfile .
```

## <a name="create-your-helm-chart"></a>Tworzenie wykresu Helm

Generowanie wykresu Helm przy użyciu `helm create` polecenia.

```console
helm create webfrontend
```

Zaktualizuj *webfronton/Values. YAML*:
* Zastąp loginServer rejestru, który został zanotowany we wcześniejszym kroku, na przykład *myhelmacr.azurecr.IO*.
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

Aktualizacja `appVersion` do `v1` programu w *webfrontonie/Chart. YAML*. Na przykład

```yml
apiVersion: v2
name: webfrontend
...
# This is the version number of the application being deployed. This version number should be
# incremented each time you make changes to the application.
appVersion: v1
```

## <a name="run-your-helm-chart"></a>Uruchamianie wykresu Helm

Zainstaluj aplikację przy użyciu wykresu Helm za pomocą `helm install` polecenia.

```console
helm install webfrontend webfrontend/
```

Zwrócenie publicznego adresu IP przez usługę może potrwać kilka minut. Monitoruj postęp przy użyciu `kubectl get service` polecenia z `--watch` argumentem.

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
webfrontend         LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
webfrontend         LoadBalancer  10.0.141.72   <EXTERNAL-IP> 80:32150/TCP   7m
```

Przejdź do modułu równoważenia obciążenia aplikacji w przeglądarce za pomocą polecenia, `<EXTERNAL-IP>` Aby zobaczyć przykładową aplikację.

## <a name="delete-the-cluster"></a>Usuwanie klastra

Użyj polecenia [AZ Group Delete][az-group-delete] , aby usunąć grupę zasobów, klaster AKS, rejestr kontenerów, obrazy kontenerów przechowywane w ACR i wszystkie powiązane zasoby.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> Po usunięciu klastra jednostka usługi Azure Active Directory używana przez klaster usługi AKS nie jest usuwana. Aby sprawdzić, jak usunąć jednostkę usługi, zobacz [AKS service principal considerations and deletion (Uwagi dotyczące jednostki usługi AKS i jej usuwanie)][sp-delete].
> 
> Jeśli używana jest tożsamość zarządzana, tożsamość jest zarządzana przez platformę i nie wymaga usunięcia.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat korzystania z programu Helm, zobacz dokumentację Helm.

> [!div class="nextstepaction"]
> [Dokumentacja programu Helm][helm-documentation]

[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-group-delete]: /cli/azure/group#az-group-delete
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[example-nodejs]: https://github.com/Azure/dev-spaces/tree/master/samples/nodejs/getting-started/webfrontend
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[helm]: https://helm.sh/
[helm-documentation]: https://helm.sh/docs/
[helm-existing]: kubernetes-helm.md
[helm-install]: https://helm.sh/docs/intro/install/
[sp-delete]: kubernetes-service-principal.md#additional-considerations
