---
title: Wypychanie & ściągania obrazu kontenera
description: Wypychanie i ściąganie obrazów platformy Docker do prywatnego rejestru kontenerów na platformie Azure przy użyciu interfejsu wiersza polecenia platformy Docker
ms.topic: article
ms.date: 01/23/2019
ms.custom: seodec18, H1Hack27Feb2017
ms.openlocfilehash: 48f5f1707881ac8461e12212be631d3b80c16ca7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783831"
---
# <a name="push-your-first-image-to-your-azure-container-registry-using-the-docker-cli"></a>Wypychanie pierwszego obrazu do rejestru kontenerów platformy Azure przy użyciu interfejsu wiersza polecenia platformy Docker

Rejestr kontenerów platformy Azure przechowuje prywatne obrazy kontenerów i inne artefakty oraz [zarządza](https://hub.docker.com/) nimi, podobnie jak Docker Hub obrazy publicznych kontenerów platformy Docker. Interfejs wiersza polecenia platformy [Docker](https://docs.docker.com/engine/reference/commandline/cli/) umożliwia [logowanie,](https://docs.docker.com/engine/reference/commandline/login/)wypychanie, [](https://docs.docker.com/engine/reference/commandline/pull/)ściąganie i inne operacje obrazu kontenera w rejestrze kontenerów. [](https://docs.docker.com/engine/reference/commandline/push/)

W poniższych krokach pobierz publiczny obraz [Nginx,](https://store.docker.com/images/nginx)otaguj go dla prywatnego rejestru kontenerów platformy Azure, wypchniesz go do rejestru, a następnie ściągniesz z rejestru.

## <a name="prerequisites"></a>Wymagania wstępne

* **Usługa Azure Container Registry** — Tworzy rejestr kontenera w subskrypcji platformy Azure. Na przykład użyj interfejsu [wiersza Azure Portal](container-registry-get-started-portal.md) platformy [Azure.](container-registry-get-started-azure-cli.md)
* **Interfejs wiersza polecenia platformy Docker** — musisz mieć również lokalnie zainstalowaną aplikację Docker. Środowisko Docker zawiera pakiety, które umożliwiają łatwe konfigurowanie platformy Docker w systemie [macOS][docker-mac], [Windows][docker-windows] lub [Linux][docker-linux].

## <a name="log-in-to-a-registry"></a>Zaloguj się do rejestru

Istnieje kilka [sposobów uwierzytelniania w](container-registry-authentication.md) prywatnym rejestrze kontenerów. Zalecaną metodą podczas pracy w wierszu polecenia jest użycie polecenia interfejsu wiersza polecenia platformy Azure [az acr login](/cli/azure/acr#az_acr_login). Aby na przykład zalogować się do rejestru o nazwie *myregistry,* zaloguj się do interfejsu wiersza polecenia platformy Azure, a następnie uwierzytelnij się w rejestrze:

```azurecli
az login
az acr login --name myregistry
```

Możesz również zalogować się przy użyciu identyfikatora [logowania platformy Docker](https://docs.docker.com/engine/reference/commandline/login/). Na przykład można było przypisać [jednostkę](container-registry-authentication.md#service-principal) usługi do rejestru w scenariuszu automatyzacji. Po uruchomieniu następującego polecenia interaktywnie podaj jednostkę usługi appID (nazwa użytkownika) i hasło po wyświetleniu monitu. Aby uzyskać najlepsze rozwiązania dotyczące zarządzania poświadczeniami logowania, zobacz informacje na temat poleceń logowania platformy [Docker:](https://docs.docker.com/engine/reference/commandline/login/)

```
docker login myregistry.azurecr.io
```

Oba polecenia są `Login Succeeded` zwracane po zakończeniu.
> [!NOTE]
>* W celu szybszego i wygodniejszego logowania Visual Studio Code użyciu rozszerzenia platformy Docker.

> [!TIP]
> Zawsze określaj w pełni kwalifikowaną nazwę rejestru (wszystkie małe litery) podczas używania tagów i wypychania obrazów `docker login` do rejestru. W przykładach w tym artykule w pełni kwalifikowana nazwa to *myregistry.azurecr.io*.

## <a name="pull-a-public-nginx-image"></a>Ściąganie publicznego obrazu Nginx

Najpierw ściągnij publiczny obraz Nginx na komputer lokalny. Ten przykład ściąga obraz z Microsoft Container Registry.

```
docker pull mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
```

## <a name="run-the-container-locally"></a>Uruchamianie kontenera w środowisku lokalnym

Wykonaj następujące polecenie [docker run,](https://docs.docker.com/engine/reference/run/) aby uruchomić lokalne wystąpienie kontenera Nginx interaktywnie ( `-it` ) na porcie 8080. Argument `--rm` określa, że kontener powinien zostać usunięty po jego zatrzymaniu.

```
docker run -it --rm -p 8080:80 mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
```

Przejdź do `http://localhost:8080` witryny , aby wyświetlić domyślną stronę internetową, która jest obsługiwany przez serwer Nginx w uruchomionym kontenerze. Powinna zostać wyświetlony strona podobna do następującej:

![Kontener Nginx na komputerze lokalnym](./media/container-registry-get-started-docker-cli/nginx.png)

Ponieważ kontener został uruchomiony interaktywnie za pomocą polecenia , dane wyjściowe serwera Nginx można zobaczyć w wierszu polecenia po przejściu do niego `-it` w przeglądarce.

Aby zatrzymać i usunąć kontener, naciśnij klawisz `Control` + `C` .

## <a name="create-an-alias-of-the-image"></a>Tworzenie aliasu obrazu

Użyj [tagu docker,](https://docs.docker.com/engine/reference/commandline/tag/) aby utworzyć alias obrazu z w pełni kwalifikowaną ścieżką do rejestru. W tym przykładzie jest określana przestrzeń nazw `samples`, aby uniknąć zaśmiecania katalogu głównego rejestru.

```
docker tag mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine myregistry.azurecr.io/samples/nginx
```

Aby uzyskać więcej informacji na temat tagowania za pomocą przestrzeni nazw, zobacz sekcję [Repository namespaces](container-registry-best-practices.md#repository-namespaces) (Przestrzenie nazw repozytorium) artykułu [Best practices for Azure Container Registry](container-registry-best-practices.md)(Najlepsze rozwiązania dotyczące Azure Container Registry ).

## <a name="push-the-image-to-your-registry"></a>Wypchnięcie obrazu do rejestru

Teraz, po otagowaniu obrazu za pomocą w pełni kwalifikowanej ścieżki do rejestru prywatnego, możesz wypchnąć go do rejestru za pomocą [wypychania platformy Docker:](https://docs.docker.com/engine/reference/commandline/push/)

```
docker push myregistry.azurecr.io/samples/nginx
```

## <a name="pull-the-image-from-your-registry"></a>Ściągnięcie obrazu z rejestru

Użyj [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) polecenia , aby ściągnąć obraz z rejestru:

```
docker pull myregistry.azurecr.io/samples/nginx
```

## <a name="start-the-nginx-container"></a>Uruchamianie kontenera Nginx

Użyj polecenia [docker run,](https://docs.docker.com/engine/reference/run/) aby uruchomić obraz ściągnięty z rejestru:

```
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

Przejdź do `http://localhost:8080` strony , aby wyświetlić uruchomiony kontener.

Aby zatrzymać i usunąć kontener, naciśnij klawisz `Control` + `C` .

## <a name="remove-the-image-optional"></a>Usuwanie obrazu (opcjonalnie)

Jeśli obraz Nginx nie jest już potrzebny, możesz usunąć go lokalnie za pomocą polecenia [docker rmi.](https://docs.docker.com/engine/reference/commandline/rmi/)

```
docker rmi myregistry.azurecr.io/samples/nginx
```

Aby usunąć obrazy z rejestru kontenerów platformy Azure, możesz użyć polecenia interfejsu wiersza polecenia platformy Azure [az acr repository delete](/cli/azure/acr/repository#az_acr_repository_delete). Na przykład następujące polecenie usuwa manifest, do których odwołuje się tag, wszelkie unikatowe dane warstwy i wszystkie inne tagi odwołujące się `samples/nginx:latest` do manifestu.

```azurecli
az acr repository delete --name myregistry --image samples/nginx:latest
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz już podstawy, możesz rozpocząć korzystanie z rejestru. Możesz na przykład wdrożyć obrazy kontenerów z rejestru w celu:

* [Azure Kubernetes Service (AKS)](../aks/tutorial-kubernetes-prepare-app.md)
* [Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md)
* [Service Fabric](../service-fabric/service-fabric-tutorial-create-container-images.md)

Opcjonalnie zainstaluj rozszerzenie [platformy Docker dla Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) i rozszerzenie konta platformy [Azure,](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) aby pracować z rejestrami kontenerów platformy Azure. Ściąganie i wypychanie obrazów do rejestru kontenerów platformy Azure lub uruchamianie zadania usługi ACR w ramach Visual Studio Code.


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/
