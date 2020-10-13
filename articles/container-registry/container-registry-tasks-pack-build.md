---
title: Kompilowanie obrazu za pomocą natywnej chmury Buildpack
description: Użyj polecenia AZ ACR Pack Build, aby skompilować obraz kontenera z aplikacji i wypchnąć do Azure Container Registry bez użycia pliku dockerfile.
ms.topic: article
ms.date: 10/24/2019
ms.custom: devx-track-js
ms.openlocfilehash: cad7d5c066dd8c8a7468833cb67e6085f067417a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91262114"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>Kompilowanie i wypychanie obrazu z aplikacji przy użyciu natywnej Buildpack w chmurze

Polecenie interfejsu wiersza polecenia platformy Azure `az acr pack build` używa [`pack`](https://github.com/buildpack/pack) narzędzia CLI, z [Buildpacks](https://buildpacks.io/), do kompilowania aplikacji i wypychania jej obrazu do usługi Azure Container Registry. Ta funkcja udostępnia opcję szybkiego tworzenia obrazu kontenera na podstawie kodu źródłowego aplikacji w Node.js, Java i innych językach bez konieczności definiowania pliku dockerfile.

Możesz użyć Azure Cloud Shell lub lokalnej instalacji interfejsu wiersza polecenia platformy Azure, aby uruchomić przykłady w tym artykule. Jeśli chcesz używać go lokalnie, wymagana jest wersja 2.0.70 lub nowsza. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

> [!IMPORTANT]
> Ta funkcja jest obecnie w wersji zapoznawczej. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

## <a name="use-the-build-command"></a>Korzystanie z polecenia Build

Aby skompilować i wypchnąć obraz kontenera za pomocą natywnej Buildpacks w chmurze, uruchom polecenie [AZ ACR Pack Build][az-acr-pack-build] . Podczas gdy polecenie [AZ ACR Build][az-acr-build] kompiluje i wypycha obraz ze źródła pliku dockerfile i powiązanego kodu, `az acr pack build` należy bezpośrednio określić drzewo źródła aplikacji.

Podczas uruchamiania należy określić co najmniej następujące elementy `az acr pack build` :

* Rejestr kontenerów platformy Azure, w którym jest uruchamiane polecenie
* Nazwa obrazu i tag dla obrazu uzyskanego
* Jedna z [obsługiwanych lokalizacji kontekstu](container-registry-tasks-overview.md#context-locations) dla zadań ACR, takich jak katalog lokalny, repozytorium GitHub lub zdalne plik tar
* Nazwa obrazu programu Buildpack Builder odpowiednie dla Twojej aplikacji. Azure Container Registry pamięci podręcznej obrazów konstruktora, takich jak `cloudfoundry/cnb:0.0.34-cflinuxfs3` w przypadku szybszych kompilacji.  

`az acr pack build` obsługuje inne funkcje poleceń ACR Tasks, w tym [uruchamiania zmiennych](container-registry-tasks-reference-yaml.md#run-variables) i [dzienników uruchamiania zadań](container-registry-tasks-logs.md) , które są przesyłane strumieniowo, a także zapisane do późniejszego pobrania.

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>Przykład: kompilowanie obrazu Node.js za pomocą konstruktora Cloud Foundry

Poniższy przykład tworzy obraz kontenera z aplikacji Node.js w repozytorium [Azure-Samples/NodeJS-docs-Hello-World](https://github.com/Azure-Samples/nodejs-docs-hello-world) przy użyciu `cloudfoundry/cnb:0.0.34-cflinuxfs3` konstruktora. Ten konstruktor jest buforowany przez Azure Container Registry, więc `--pull` parametr nie jest wymagany:

```azurecli
az acr pack build \
    --registry myregistry \
    --image {{.Run.Registry}}/node-app:1.0 \
    --builder cloudfoundry/cnb:0.0.34-cflinuxfs3 \
    https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

Ten przykład kompiluje `node-app` obraz przy użyciu `1.0` znacznika i wypchnięcie go do rejestru kontenerów *rejestru* . W tym przykładzie docelowa Nazwa rejestru jest jawnie poprzedzona nazwą obrazu. Jeśli nie zostanie określony, nazwa serwera logowania rejestru zostanie automatycznie poprzedzona nazwą obrazu.

Dane wyjściowe polecenia pokazują postęp kompilowania i wypychania obrazu. 

Po pomyślnym skompilowaniu obrazu można uruchomić go przy użyciu platformy Docker, jeśli jest zainstalowany. Najpierw Zaloguj się do rejestru:

```azurecli
az acr login --name myregistry
```

Uruchom obraz:

```console
docker run --rm -p 1337:1337 myregistry.azurecr.io/node-app:1.0
```

Przejdź do `localhost:1337` ulubionej przeglądarki, aby wyświetlić przykładową aplikację internetową. Naciśnij klawisz, `[Ctrl]+[C]` Aby zatrzymać kontener.

## <a name="example-build-java-image-with-heroku-builder"></a>Przykład: kompilowanie obrazu Java za pomocą konstruktora Heroku

Poniższy przykład kompiluje obraz kontenera z aplikacji Java w repozytorium [buildpack/Sample-Java-App](https://github.com/buildpack/sample-java-app) , korzystając z `heroku/buildpacks:18` konstruktora. `--pull`Parametr określa, że polecenie powinno pobrać najnowszy obraz konstruktora. 

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

Ten przykład kompiluje `java-app` obraz oznaczony przy użyciu identyfikatora uruchomienia polecenia i wypchnięcie go do rejestru kontenerów *rejestru* .

Dane wyjściowe polecenia pokazują postęp kompilowania i wypychania obrazu. 

Po pomyślnym skompilowaniu obrazu można uruchomić go przy użyciu platformy Docker, jeśli jest zainstalowany. Najpierw Zaloguj się do rejestru:

```azurecli
az acr login --name myregistry
```

Uruchom obraz, zastępując tag obrazu *RunId*:

```console
docker run --rm -p 8080:8080 myregistry.azurecr.io/java-app:runid
```

Przejdź do `localhost:8080` ulubionej przeglądarki, aby wyświetlić przykładową aplikację internetową. Naciśnij klawisz, `[Ctrl]+[C]` Aby zatrzymać kontener.


## <a name="next-steps"></a>Następne kroki

Po skompilowaniu i wypchnięciu obrazu kontenera za pomocą programu `az acr pack build` można wdrożyć go jak dowolny obraz w wybranym miejscu docelowym. Opcje wdrażania platformy Azure obejmują uruchamianie go w [App Service](../app-service/tutorial-custom-container.md) lub [usługi Azure Kubernetes](../aks/tutorial-kubernetes-deploy-cluster.md).

Aby uzyskać więcej informacji na temat funkcji zadań ACR, zobacz [Automatyzacja kompilacji i konserwacji obrazów kontenerów za pomocą zadań ACR](container-registry-tasks-overview.md).


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
