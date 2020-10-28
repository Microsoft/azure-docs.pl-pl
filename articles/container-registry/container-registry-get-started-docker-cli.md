---
title: Wypychanie & ściągania obrazu platformy Docker
description: Wypychanie i ściąganie obrazów platformy Docker do prywatnego rejestru kontenerów na platformie Azure za pomocą interfejsu wiersza polecenia platformy Docker
ms.topic: article
ms.date: 01/23/2019
ms.custom: seodec18, H1Hack27Feb2017, devx-track-azurecli
ms.openlocfilehash: 067b5749332c2c41b86d22e7de60083d5f61a442
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746628"
---
# <a name="push-your-first-image-to-a-private-docker-container-registry-using-the-docker-cli"></a>Wypchnij swój pierwszy obraz do prywatnego rejestru kontenerów platformy Docker za pomocą interfejsu wiersza polecenia platformy Docker

Rejestr kontenera platformy Azure przechowuje prywatne obrazy kontenerów platformy [Docker](https://hub.docker.com) i zarządza nimi podobnie, jak [koncentrator platformy Docker](https://hub.docker.com/) przechowuje publiczne obrazy platformy Docker. Do [logowania](https://docs.docker.com/engine/reference/commandline/login/), [wypychania](https://docs.docker.com/engine/reference/commandline/push/), [ściągania](https://docs.docker.com/engine/reference/commandline/pull/)i innych operacji w rejestrze kontenerów można użyć [interfejsu wiersza polecenia platformy Docker](https://docs.docker.com/engine/reference/commandline/cli/) (CLI).

W poniższych krokach pobierasz oficjalny [obraz Nginx](https://store.docker.com/images/nginx) z publicznego rejestru centrum Docker, oznacz go jako prywatny rejestr kontenerów platformy Azure, wypchnij do rejestru, a następnie pobierz go z rejestru.

## <a name="prerequisites"></a>Wymagania wstępne

* **Usługa Azure Container Registry** — Tworzy rejestr kontenera w subskrypcji platformy Azure. Na przykład użyj [Azure Portal](container-registry-get-started-portal.md) lub [interfejsu wiersza polecenia platformy Azure](container-registry-get-started-azure-cli.md).
* **Interfejs wiersza polecenia platformy Docker** — należy również zainstalować lokalnie platformę Docker. Środowisko Docker zawiera pakiety, które umożliwiają łatwe konfigurowanie platformy Docker w systemie [macOS][docker-mac], [Windows][docker-windows] lub [Linux][docker-linux].

## <a name="log-in-to-a-registry"></a>Zaloguj się do rejestru

Istnieje [kilka sposobów uwierzytelniania](container-registry-authentication.md) do prywatnego rejestru kontenerów. Zalecaną metodą podczas pracy z wierszem polecenia jest polecenie [AZ ACR login](/cli/azure/acr?view=azure-cli-latest#az-acr-login). Na przykład, aby zalogować się do rejestru o nazwie Moje *Rejestr* :

```azurecli
az acr login --name myregistry
```

Możesz też zalogować się przy użyciu [logowania Docker](https://docs.docker.com/engine/reference/commandline/login/). Można na przykład [przypisać jednostkę usługi](container-registry-authentication.md#service-principal) do rejestru w scenariuszu automatyzacji. Po uruchomieniu poniższego polecenia, interaktywnie Podaj nazwę (username) jednostki usługi i hasło po wyświetleniu monitu. Aby zapoznać się z najlepszymi rozwiązaniami dotyczącymi zarządzania poświadczeniami logowania, zobacz informacje dotyczące poleceń [logowania Docker](https://docs.docker.com/engine/reference/commandline/login/) :

```
docker login myregistry.azurecr.io
```

Oba polecenia są zwracane `Login Succeeded` po zakończeniu.

> [!TIP]
> Zawsze określaj w pełni kwalifikowaną nazwę rejestru (wszystkie małe litery) podczas korzystania z programu `docker login` i podczas oznaczania obrazów do wypchnięcia do rejestru. W przykładach w tym artykule, w pełni kwalifikowana nazwa to *myregistry.azurecr.IO* .

## <a name="pull-the-official-nginx-image"></a>Ściąganie oficjalnego obrazu Nginx

Najpierw Pobierz publiczny obraz Nginx do komputera lokalnego.

```
docker pull nginx
```

## <a name="run-the-container-locally"></a>Uruchamianie kontenera w środowisku lokalnym

Wykonaj następujące polecenie [Docker Run](https://docs.docker.com/engine/reference/run/) , aby uruchomić lokalnie wystąpienie kontenera Nginx ( `-it` ) w porcie 8080. `--rm`Argument określa, że kontener powinien zostać usunięty po jego zatrzymaniu.

```
docker run -it --rm -p 8080:80 nginx
```

Przejdź do `http://localhost:8080` strony, aby wyświetlić domyślną stronę sieci Web, która jest obsługiwana przez Nginx w działającym kontenerze. Powinna zostać wyświetlona strona podobna do następującej:

![Kontener Nginx na komputerze lokalnym](./media/container-registry-get-started-docker-cli/nginx.png)

Ze względu na to, że interaktywnie uruchomiono kontener za pomocą programu `-it` , w wierszu polecenia można zobaczyć dane wyjściowe serwera Nginx po przejściu do niego w przeglądarce.

Aby zatrzymać i usunąć kontener, naciśnij klawisz `Control` + `C` .

## <a name="create-an-alias-of-the-image"></a>Tworzenie aliasu obrazu

Użyj [tagu Docker](https://docs.docker.com/engine/reference/commandline/tag/) , aby utworzyć alias obrazu z w pełni kwalifikowaną ścieżką do rejestru. W tym przykładzie jest określana przestrzeń nazw `samples`, aby uniknąć zaśmiecania katalogu głównego rejestru.

```
docker tag nginx myregistry.azurecr.io/samples/nginx
```

Aby uzyskać więcej informacji o znakowaniu z przestrzeniami nazw, zobacz sekcję [przestrzenie nazw repozytoriów](container-registry-best-practices.md#repository-namespaces) z [najlepszymi rozwiązaniami dotyczącymi Azure Container Registry](container-registry-best-practices.md).

## <a name="push-the-image-to-your-registry"></a>Wypchnięcie obrazu do rejestru

Teraz, po oznakowaniu obrazu za pomocą w pełni kwalifikowanej ścieżki do rejestru prywatnego, można wypchnąć go do rejestru przy użyciu [wypychania Docker](https://docs.docker.com/engine/reference/commandline/push/):

```
docker push myregistry.azurecr.io/samples/nginx
```

## <a name="pull-the-image-from-your-registry"></a>Ściągnięcie obrazu z rejestru

Użyj [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) polecenia, aby ściągnąć obraz z rejestru:

```
docker pull myregistry.azurecr.io/samples/nginx
```

## <a name="start-the-nginx-container"></a>Uruchamianie kontenera Nginx

Użyj polecenia [Docker Run](https://docs.docker.com/engine/reference/run/) , aby uruchomić pobrany obraz z rejestru:

```
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

Przeglądaj w celu `http://localhost:8080` wyświetlenia uruchomionego kontenera.

Aby zatrzymać i usunąć kontener, naciśnij klawisz `Control` + `C` .

## <a name="remove-the-image-optional"></a>Usuń obraz (opcjonalnie)

Jeśli obraz Nginx nie jest już potrzebny, możesz go usunąć lokalnie przy użyciu polecenia [Docker RMI](https://docs.docker.com/engine/reference/commandline/rmi/) .

```
docker rmi myregistry.azurecr.io/samples/nginx
```

Aby usunąć obrazy z usługi Azure Container Registry, można użyć polecenia ACR [repozytorium Delete](/cli/azure/acr/repository#az-acr-repository-delete). Na przykład następujące polecenie usuwa manifest, do którego odwołuje się `samples/nginx:latest` tag, wszelkie unikatowe dane warstwy i wszystkie inne Tagi odwołujące się do manifestu.

```azurecli
az acr repository delete --name myregistry --image samples/nginx:latest
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz już podstawowe elementy, możesz zacząć korzystać z rejestru. Na przykład Wdróż obrazy kontenerów z rejestru w celu:

* [Azure Kubernetes Service (AKS)](../aks/tutorial-kubernetes-prepare-app.md)
* [Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md)
* [Service Fabric](../service-fabric/service-fabric-tutorial-create-container-images.md)

Opcjonalnie zainstaluj [rozszerzenie Docker dla Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) i rozszerzenie [konta platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) , aby współpracowało z rejestrami kontenerów platformy Azure. Ściągaj i wypychanie obrazów do usługi Azure Container Registry lub uruchamiaj zadania ACR, w Visual Studio Code.


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/
