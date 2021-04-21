---
title: Kompilowanie obrazu za pomocą natywnego dla chmury buildpack
description: Użyj polecenia az acr pack build, aby skompilować obraz kontenera z aplikacji i wypchnąć go do Azure Container Registry bez użycia pliku Dockerfile.
ms.topic: article
ms.date: 10/24/2019
ms.custom: devx-track-js
ms.openlocfilehash: 1700c8fda8ac91e7d447d35c0989da2d5fc3aefe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780933"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>Kompilowanie i wypychanie obrazu z aplikacji przy użyciu natywnego dla chmury buildpack

Polecenie interfejsu wiersza polecenia platformy Azure używa narzędzia interfejsu wiersza polecenia z narzędzia Buildpacks do kompilowania aplikacji i wypychania jej obrazu `az acr pack build` [`pack`](https://github.com/buildpack/pack) do rejestru kontenerów platformy Azure. [](https://buildpacks.io/) Ta funkcja umożliwia szybkie skompilowanie obrazu kontenera na pomocą kodu źródłowego aplikacji w języku Node.js, Java i innych językach bez konieczności definiowania pliku Dockerfile.

Aby uruchomić przykłady Azure Cloud Shell tego artykułu, możesz użyć interfejsu wiersza polecenia platformy Azure lub lokalnej instalacji interfejsu wiersza polecenia platformy Azure. Jeśli chcesz używać go lokalnie, wymagana jest wersja 2.0.70 lub nowsza. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

> [!IMPORTANT]
> Ta funkcja jest obecnie w wersji zapoznawczej. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

## <a name="use-the-build-command"></a>Użyj polecenia build

Aby skompilować i wypchnąć obraz kontenera przy użyciu pakietów kompilacji natywnych dla chmury, uruchom [polecenie az acr pack build.][az-acr-pack-build] Podczas gdy [polecenie az acr build][az-acr-build] tworzy i wypycha obraz ze źródła pliku Dockerfile i powiązanego kodu, a ty bezpośrednio określasz drzewo źródłowe `az acr pack build` aplikacji.

Podczas uruchamiania należy określić co najmniej następujące `az acr pack build` elementy:

* Rejestr kontenerów platformy Azure, w którym uruchamiasz polecenie
* Nazwa i tag obrazu wynikowego
* Jedna z [obsługiwanych lokalizacji kontekstowych](container-registry-tasks-overview.md#context-locations) dla zadania usługi ACR, taka jak katalog lokalny, repozytorium GitHub lub zdalny tarball
* Nazwa obrazu konstruktora buildpack odpowiedniego dla Twojej aplikacji. Azure Container Registry buforuje obrazy konstruktorów, takie jak `cloudfoundry/cnb:0.0.34-cflinuxfs3` w celu szybszego kompilowania.  

`az acr pack build`Obsługuje inne funkcje poleceń zadania usługi ACR, w [](container-registry-tasks-logs.md) tym zmienne uruchamiania i dzienniki uruchamiania zadań, które są przesyłane strumieniowo, a także zapisywane do późniejszego pobrania. [](container-registry-tasks-reference-yaml.md#run-variables)

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>Przykład: kompilowanie Node.js obrazu za pomocą Cloud Foundry builder

Poniższy przykład tworzy obraz kontenera z aplikacji Node.js w repocie [Azure-Samples/nodejs-docs-hello-world](https://github.com/Azure-Samples/nodejs-docs-hello-world) przy użyciu `cloudfoundry/cnb:0.0.34-cflinuxfs3` konstruktora. Ten konstruktor jest buforowany przez Azure Container Registry, więc `--pull` parametr nie jest wymagany:

```azurecli
az acr pack build \
    --registry myregistry \
    --image {{.Run.Registry}}/node-app:1.0 \
    --builder cloudfoundry/cnb:0.0.34-cflinuxfs3 \
    https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

Ten przykład tworzy obraz z tagiem i wypycha go do `node-app` `1.0` rejestru *kontenerów myregistry.* W tym przykładzie nazwa rejestru docelowego jest jawnie dołączana do nazwy obrazu. Jeśli nie zostanie określony, nazwa serwera logowania rejestru jest automatycznie dołączana do nazwy obrazu.

Dane wyjściowe polecenia pokazują postęp budowania i wypychania obrazu. 

Po pomyślnym s zbudowaniu obrazu możesz uruchomić go za pomocą platformy Docker, jeśli został zainstalowany. Najpierw zaloguj się do rejestru:

```azurecli
az acr login --name myregistry
```

Uruchom obraz:

```console
docker run --rm -p 1337:1337 myregistry.azurecr.io/node-app:1.0
```

Przejdź do `localhost:1337` strony w ulubionej przeglądarce, aby wyświetlić przykładową aplikację internetową. Naciśnij `[Ctrl]+[C]` klawisz , aby zatrzymać kontener.

## <a name="example-build-java-image-with-heroku-builder"></a>Przykład: kompilowanie obrazu Java za pomocą aplikacji Heroku Builder

Poniższy przykład tworzy obraz kontenera z aplikacji Java w [repocie buildpack/sample-java-app](https://github.com/buildpack/sample-java-app) przy użyciu `heroku/buildpacks:18` konstruktora. Parametr `--pull` określa, że polecenie powinno ściągnąć najnowszy obraz konstruktora. 

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

W tym przykładzie jest kompilowany obraz oznaczony identyfikatorem uruchomienia polecenia i wypychany do rejestru kontenerów `java-app` *myregistry.*

Dane wyjściowe polecenia pokazują postęp budowania i wypychania obrazu. 

Po pomyślnym s zbudowaniu obrazu możesz uruchomić go za pomocą platformy Docker, jeśli został zainstalowany. Najpierw zaloguj się do rejestru:

```azurecli
az acr login --name myregistry
```

Uruchom obraz, zastępując tag obrazu dla *runid*:

```console
docker run --rm -p 8080:8080 myregistry.azurecr.io/java-app:runid
```

Przejdź do `localhost:8080` strony w ulubionej przeglądarce, aby wyświetlić przykładową aplikację internetową. Naciśnij `[Ctrl]+[C]` klawisz , aby zatrzymać kontener.


## <a name="next-steps"></a>Następne kroki

Po skompilowaniu i wypchnięciu obrazu kontenera za pomocą pliku możesz wdrożyć go jak dowolny obraz do wybranego `az acr pack build` miejsca docelowego. Opcje wdrażania platformy Azure obejmują uruchamianie go [App Service](../app-service/tutorial-custom-container.md) lub [Azure Kubernetes Service](../aks/tutorial-kubernetes-deploy-cluster.md), między innymi.

Aby uzyskać więcej informacji na temat zadania usługi ACR, zobacz [Automatyzowanie](container-registry-tasks-overview.md)kompilacji i konserwacji obrazu kontenera za pomocą zadania usługi ACR .


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az_acr_pack_build
