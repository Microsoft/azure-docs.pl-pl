---
title: Samouczek — przygotowanie obrazu kontenera do wdrożenia
description: Samouczek usługi Azure Container Instances, część 1 z 3 — przygotowywanie aplikacji w obrazie kontenera do wdrożenia w usłudze Azure Container Instances
ms.topic: tutorial
ms.date: 03/21/2018
ms.custom: seodec18, mvc, devx-track-js
ms.openlocfilehash: 1eca34fb5da50b15f15322a3b534556bcd3bda09
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91303118"
---
# <a name="tutorial-create-a-container-image-for-deployment-to-azure-container-instances"></a>Samouczek: Tworzenie obrazu kontenera na potrzeby wdrożenia Azure Container Instances

Usługa Azure Container Instances umożliwia wdrażanie kontenerów Docker w infrastrukturze platformy Azure bez aprowizowania maszyn wirtualnych ani adaptowania usług wyższego poziomu. Podczas pracy z tym samouczkiem spakujesz małą aplikację internetową środowiska Node.js do obrazu kontenera, który można uruchomić za pomocą usługi Azure Container Instances.

W tym artykule, który jest pierwszą częścią trzyczęściowej serii, wykonasz następujące działania:

> [!div class="checklist"]
> * Klonowanie kodu źródłowego aplikacji z usługi GitHub
> * Tworzenie obrazu kontenera ze źródła aplikacji
> * Testowanie obrazu w lokalnym środowisku Docker

Podczas pracy z częścią drugą i trzecią samouczka przekażesz obraz do usługi Azure Container Registry, a następnie wdrożysz go w usłudze Azure Container Instances.

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

## <a name="get-application-code"></a>Pobieranie kodu aplikacji

Przykład aplikacji podany w tym samouczku obejmuje prostą aplikację internetową utworzoną za pomocą oprogramowania [Node.js][nodejs]. Aplikacja służy za statyczną stronę HTML i wygląda podobnie do poniższego zrzutu ekranu:

![Samouczek aplikacji wyświetlony w przeglądarce][aci-tutorial-app]

Użyj narzędzia Git, aby sklonować repozytorium przykładowej aplikacji:

```bash
git clone https://github.com/Azure-Samples/aci-helloworld.git
```

Możesz też [pobrać archiwum ZIP][aci-helloworld-zip] bezpośrednio z usługi GitHub.

## <a name="build-the-container-image"></a>Kompilowanie obrazu kontenera

Plik Dockerfile w przykładowej aplikacji przedstawia sposób tworzenia kontenera. Praca rozpoczyna się od [oficjalnego obrazu oprogramowania Node.js][docker-hub-nodeimage] opartego na systemie [Alpine Linux][alpine-linux], małej dystrybucji, która dobrze nadaje się do korzystania z kontenerów. Kolejny krok to skopiowanie plików aplikacji do kontenera, zainstalowanie zależności za pomocą programu Node Package Manager i na koniec uruchomienie aplikacji.

```Dockerfile
FROM node:8.9.3-alpine
RUN mkdir -p /usr/src/app
COPY ./app/ /usr/src/app/
WORKDIR /usr/src/app
RUN npm install
CMD node /usr/src/app/index.js
```

Użyj polecenia [docker build][docker-build], aby utworzyć obraz kontenera, a następnie otaguj go jako *aci-tutorial-app*:

```bash
docker build ./aci-helloworld -t aci-tutorial-app
```

Dane wyjściowe polecenia [docker build][docker-build] są podobne do następujących (tutaj obcięto je dla zwiększenia czytelności):

```console
$ docker build ./aci-helloworld -t aci-tutorial-app
Sending build context to Docker daemon  119.3kB
Step 1/6 : FROM node:8.9.3-alpine
8.9.3-alpine: Pulling from library/node
88286f41530e: Pull complete
84f3a4bf8410: Pull complete
d0d9b2214720: Pull complete
Digest: sha256:c73277ccc763752b42bb2400d1aaecb4e3d32e3a9dbedd0e49885c71bea07354
Status: Downloaded newer image for node:8.9.3-alpine
 ---> 90f5ee24bee2
...
Step 6/6 : CMD node /usr/src/app/index.js
 ---> Running in f4a1ea099eec
 ---> 6edad76d09e9
Removing intermediate container f4a1ea099eec
Successfully built 6edad76d09e9
Successfully tagged aci-tutorial-app:latest
```

Użyj polecenia [docker images][docker-images], aby wyświetlić utworzony obraz:

```bash
docker images
```

Nowo utworzony obraz powinien zostać wyświetlony na liście:

```console
$ docker images
REPOSITORY          TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app    latest    5c745774dfa9    39 seconds ago    68.1 MB
```

## <a name="run-the-container-locally"></a>Uruchamianie kontenera w środowisku lokalnym

Przed wdrożeniem kontenera w usłudze Azure Container Instances użyj polecenia [docker run][docker-run], aby uruchomić go lokalnie i sprawdzić, czy działa. Przełącznik `-d` umożliwia uruchamianie kontenera w tle, a element `-p` — mapowanie dowolnego portu komputera do portu 80 w kontenerze.

```bash
docker run -d -p 8080:80 aci-tutorial-app
```

Dane wyjściowe z polecenia `docker run` wyświetlają identyfikator uruchomionego kontenera, jeśli polecenie zakończyło się pomyślnie:

```console
$ docker run -d -p 8080:80 aci-tutorial-app
a2e3e4435db58ab0c664ce521854c2e1a1bda88c9cf2fcff46aedf48df86cccf
```

Teraz przejdź do adresu `http://localhost:8080` w przeglądarce, aby potwierdzić, że kontener działa. Powinna zostać wyświetlona strona internetowa podobna do poniższej:

![Uruchamianie aplikacji lokalnie w przeglądarce][aci-tutorial-app-local]

## <a name="next-steps"></a>Następne kroki

Podczas pracy z samouczkiem utworzono obraz kontenera, który można wdrożyć w usłudze Azure Container Instances i zweryfikować jego działanie lokalnie. Jak dotąd wykonano następujące czynności:

> [!div class="checklist"]
> * Klonowanie źródła aplikacji z usługi GitHub
> * Tworzenie obrazu kontenera ze źródła aplikacji
> * Testowanie kontenera w środowisku lokalnym

Przejdź do kolejnego samouczka w serii, aby dowiedzieć się więcej o przechowywaniu obrazów kontenera w usłudze Azure Container Registry:

> [!div class="nextstepaction"]
> [Wypychanie obrazu do usługi Azure Container Registry](container-instances-tutorial-prepare-acr.md)

<!--- IMAGES --->
[aci-tutorial-app]:./media/container-instances-quickstart/aci-app-browser.png
[aci-tutorial-app-local]: ./media/container-instances-tutorial-prepare-app/aci-app-browser-local.png

<!-- LINKS - External -->
[aci-helloworld-zip]: https://github.com/Azure-Samples/aci-helloworld/archive/master.zip
[alpine-linux]: https://alpinelinux.org/
[docker-build]: https://docs.docker.com/engine/reference/commandline/build/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-hub-nodeimage]: https://store.docker.com/images/node
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[nodejs]: https://nodejs.org

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
