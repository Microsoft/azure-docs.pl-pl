---
title: (PRZESTARZAŁE) Samouczek usługi Azure Container Service — przygotowywanie usługi ACR
description: Samouczek usługi Azure Container Service — przygotowywanie usługi ACR
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 087530fd3834c4ec4620c087134bee0ed26bb6c9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78399780"
---
# <a name="deprecated-deploy-and-use-azure-container-registry"></a>(PRZESTARZAŁE) Wdrażanie usługi Azure Container Registry i korzystanie z niej

> [!TIP]
> Aby uzyskać zaktualizowaną wersję tego samouczka korzystającego z usługi Azure Kubernetes, zobacz [Samouczek: wdrażanie i używanie Azure Container Registry](../../aks/tutorial-kubernetes-prepare-acr.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Usługa Azure Container Registry to oparty na platformie Azure rejestr prywatny na potrzeby obrazów kontenerów platformy Docker. Ten samouczek (część druga z siedmiu) zawiera opis sposobu wdrażania wystąpienia usługi Azure Container Registry i wypychania do niego obrazu kontenera. Wykonano następujące czynności:

> [!div class="checklist"]
> * Wdrażanie wystąpienia usługi Azure Container Registry
> * Tagowanie obrazu kontenera na potrzeby rejestru Azure Container Registry
> * Przekazywanie obrazu do rejestru Azure Container Registry

W kolejnych samouczkach to wystąpienie rejestru ACR zostanie zintegrowane z klastrem Kubernetes usługi Azure Container Service. 

## <a name="before-you-begin"></a>Przed rozpoczęciem

W [poprzednim samouczku ](./container-service-tutorial-kubernetes-prepare-app.md) utworzono obraz kontenera na potrzeby prostej aplikacji do głosowania platformy Azure. Jeśli obraz aplikacji do głosowania platformy Azure nie został utworzony, wróć do artykułu [Samouczek 1 — Tworzenie obrazów kontenerów](./container-service-tutorial-kubernetes-prepare-app.md).

Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 

## <a name="deploy-azure-container-registry"></a>Wdrażanie usługi Azure Container Registry

W przypadku wdrażania usługi Azure Container Registry należy najpierw posiadać grupę zasobów. Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). W tym przykładzie grupa zasobów o nazwie `myResourceGroup` zostanie utworzona w regionie `westeurope`.

```azurecli
az group create --name myResourceGroup --location westeurope
```

Utwórz usługę Azure Container Registry za pomocą polecenia [AZ ACR Create](/cli/azure/acr#az-acr-create) . Nazwa rejestru kontenera **musi być unikatowa**.

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

W dalszej części tego samouczka wartość `<acrname>` zostanie użyta jako symbol zastępczy nazwy rejestru kontenerów.

## <a name="container-registry-login"></a>Logowanie do rejestru Container Registry

Użyj polecenia [az acr login](https://docs.microsoft.com/cli/azure/acr#az-acr-login), aby zalogować się do wystąpienia rejestru Azure Container Registry. Należy podać unikatową nazwę nadaną kontenerowi podczas jego tworzenia.

```azurecli
az acr login --name <acrName>
```

Polecenie zwraca komunikat „Logowanie pomyślne” po ukończeniu.

## <a name="tag-container-images"></a>Tagowanie obrazów kontenerów

Aby wyświetlić listę bieżących obrazów, użyj polecenia [docker images](https://docs.docker.com/engine/reference/commandline/images/).

```bash
docker images
```

Dane wyjściowe:

```output
REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front             latest              4675398c9172        13 minutes ago      694MB
redis                        latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        9 months ago        694MB
```

Każdy obraz kontenera należy otagować za pomocą nazwy loginServer rejestru. Ten tag jest używany na potrzeby kierowania podczas wypychania obrazów kontenerów do rejestru obrazów.

Aby uzyskać nazwę loginServer, uruchom następujące polecenie.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Następnie otaguj obraz `azure-vote-front` wartością loginServer rejestru kontenerów. Ponadto dodaj wartość `:v1` na końcu nazwy obrazu. Ten tag wskazuje wersję obrazu.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Po otagowaniu uruchom polecenie [docker images](https://docs.docker.com/engine/reference/commandline/images/), aby zweryfikować operację.

```bash
docker images
```

Dane wyjściowe:

```output
REPOSITORY                                           TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front                                     latest              eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry082.azurecr.io/azure-vote-front   v1            eaf2b9c57e5e        8 minutes ago       716 MB
redis                                                latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask                           flask               788ca94b2313        8 months ago        694 MB
```

## <a name="push-images-to-registry"></a>Wypychanie obrazów do rejestru

Wypchnij obraz `azure-vote-front` do rejestru. 

Korzystając z następującego przykładu, zastąp nazwę loginServer usługi ACR nazwą loginServer z używanego środowiska.

```bash
docker push <acrLoginServer>/azure-vote-front:v1
```

Wykonanie tej operacji może zająć kilka minut.

## <a name="list-images-in-registry"></a>Wyświetlanie listy obrazów w rejestrze

Aby zwrócić listę obrazów, które zostały wypchnięte do usługi Azure Container Registry, użyj polecenia [az acr repository list](/cli/azure/acr/repository#az-acr-repository-list). Zaktualizuj polecenie nazwą wystąpienia usługi ACR.

```azurecli
az acr repository list --name <acrName> --output table
```

Dane wyjściowe:

```output
Result
----------------
azure-vote-front
```

Aby następnie wyświetlić tagi dla określonego obrazu, użyj polecenia [az acr repository show-tags](/cli/azure/acr/repository).

```azurecli
az acr repository show-tags --name <acrName> --repository azure-vote-front --output table
```

Dane wyjściowe:

```output
Result
--------
v1
```

Po ukończeniu tego samouczka obraz kontenera zostanie zapisany w prywatnym wystąpieniu usługi Azure Container Registry. W kolejnych samouczkach ten obraz zostanie wdrożony z rejestru Azure Container Registry do klastra Kubernetes.

## <a name="next-steps"></a>Następne kroki

W tym samouczku usługa Azure Container Registry została przygotowana do użycia w klastrze Kubernetes ACS. Wykonano następujące czynności:

> [!div class="checklist"]
> * Wdrażanie wystąpienia usługi Azure Container Registry
> * Tagowanie obrazu kontenera na potrzeby rejestru Azure Container Registry
> * Przekazywanie obrazu do rejestru Azure Container Registry

Przejdź do kolejnego samouczka, aby uzyskać więcej informacji o wdrażaniu klastra Kubernetes na platformie Azure.

> [!div class="nextstepaction"]
> [Wdrażanie klastra Kubernetes](./container-service-tutorial-kubernetes-deploy-cluster.md)
