---
title: Samouczek dotyczący usługi Kubernetes na platformie Azure — przygotowywanie aplikacji
description: Z tego samouczka dotyczącego usługi Azure Kubernetes Service (AKS) dowiesz się, jak przygotować i skompilować aplikację z wieloma kontenerami przy użyciu narzędzia Docker Compose, którą można następnie wdrożyć w usłudze AKS.
services: container-service
ms.topic: tutorial
ms.date: 01/12/2021
ms.custom: mvc
ms.openlocfilehash: 349bf90ea0b344d5232c885358814f39fba4c19f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98251962"
---
# <a name="tutorial-prepare-an-application-for-azure-kubernetes-service-aks"></a>Samouczek: przygotowywanie aplikacji na potrzeby usługi Azure Kubernetes Service (AKS)

Ten samouczek, część 1 z 7, obejmuje przygotowanie aplikacji z wieloma kontenerami do użycia w usłudze Kubernetes. Istniejące narzędzia programistyczne, takie jak narzędzia Docker Compose, są używane do lokalnego kompilowania i testowania aplikacji. Omawiane kwestie:

> [!div class="checklist"]
> * Klonowanie źródła przykładowej aplikacji z usługi GitHub
> * Tworzenie obrazu kontenera ze źródła przykładowej aplikacji
> * Testowanie aplikacji z wieloma kontenerami w lokalnym środowisku platformy Docker

Po zakończeniu następująca aplikacja będzie uruchomiona w lokalnym środowisku programistycznym:

:::image type="content" source="./media/container-service-kubernetes-tutorials/azure-vote-local.png" alt-text="Zrzut ekranu przedstawiający obraz kontenera aplikacja do głosowania platformy Azure uruchamiana lokalnie otwarta w lokalnej przeglądarce internetowej" lightbox="./media/container-service-kubernetes-tutorials/azure-vote-local.png":::

W kolejnych samouczkach obraz kontenera zostanie przekazany do Azure Container Registry, a następnie wdrożony w klastrze AKS.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Ten samouczek zakłada, że masz podstawową wiedzę na temat najważniejszych pojęć dotyczących platformy Docker, takich jak kontenery, obrazy kontenerów i polecenia `docker`. Aby uzyskać podstawowe informacje na temat kontenerów, zapoznaj się z tematem [Get started with Docker (Rozpoczynanie pracy z platformą Docker)][docker-get-started].

Do ukończenia tego samouczka konieczne będzie lokalne środowisko programistyczne platformy Docker z działającymi kontenerami systemu Linux. Środowisko Docker zawiera pakiety, które umożliwiają konfigurowanie platformy Docker w systemie [Mac][docker-for-mac], [Windows][docker-for-windows] lub [Linux][docker-for-linux].

> [!NOTE]
> Usługa Azure Cloud Shell nie zawiera składników platformy Docker wymaganych do ukończenia każdego kroku w tych samouczkach. Dlatego zalecamy używanie pełnego środowiska programistycznego usługi Docker.

## <a name="get-application-code"></a>Pobieranie kodu aplikacji

[Przykładowa aplikacja][sample-application] używana w tym samouczku to podstawowa aplikacja do głosowania składająca się ze składnika sieci Web frontonu i wystąpienia Redis zaplecza. Składnik internetowy znajduje się w pakiecie niestandardowego obrazu kontenera. Wystąpienie usługi Redis używa niezmodyfikowanego obrazu z usługi Docker Hub.

Użyj narzędzia [git][], aby sklonować przykładową aplikację do swojego środowiska programistycznego:

```console
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Przejdź do sklonowanego katalogu.

```console
cd azure-voting-app-redis
```

W tym katalogu znajduje się kod źródłowy aplikacji, wstępnie utworzony plik redagowania usługi Docker i plik manifestu usługi Kubernetes. Te pliki są używane w całym zestawie samouczków. Zawartość i struktura katalogu są następujące:

```output
azure-voting-app-redis
│   azure-vote-all-in-one-redis.yaml
│   docker-compose.yaml
│   LICENSE
│   README.md
│
├───azure-vote
│   │   app_init.supervisord.conf
│   │   Dockerfile
│   │   Dockerfile-for-app-service
│   │   sshd_config
│   │
│   └───azure-vote
│       │   config_file.cfg
│       │   main.py
│       │
│       ├───static
│       │       default.css
│       │
│       └───templates
│               index.html
│
└───jenkins-tutorial
        config-jenkins.sh
        deploy-jenkins-vm.sh
```

## <a name="create-container-images"></a>Tworzenie obrazów kontenerów

Narzędzie [Docker Compose][docker-compose] umożliwia automatyzowanie tworzenia obrazów kontenerów i wdrażanie aplikacji z wieloma kontenerami.

Użyj przykładowego pliku `docker-compose.yaml`, aby utworzyć obraz kontenera, pobrać obraz usługi Redis i uruchomić aplikację:

```console
docker-compose up -d
```

Po zakończeniu użyj polecenia [docker images][docker-images] w celu wyświetlenia utworzonych obrazów. Zostały pobrane lub utworzone trzy obrazy. Obraz z systemem *Azure — głosowanie* zawiera aplikację frontonu i używa obrazu *Nginx-kolby* jako bazy. Obraz *Redis* jest używany do uruchomienia wystąpienia Redis.

```
$ docker images

REPOSITORY                                     TAG                 IMAGE ID            CREATED             SIZE
mcr.microsoft.com/azuredocs/azure-vote-front   v1                  84b41c268ad9        9 seconds ago       944MB
mcr.microsoft.com/oss/bitnami/redis            6.0.8               3a54a920bb6c        2 days ago          103MB
tiangolo/uwsgi-nginx-flask                     python3.6           a16ce562e863        6 weeks ago         944MB
```

Uruchom polecenie [docker ps][docker-ps], aby wyświetlić uruchomione kontenery:

```
$ docker ps

CONTAINER ID        IMAGE                                             COMMAND                  CREATED             STATUS              PORTS                           NAMES
d10e5244f237        mcr.microsoft.com/azuredocs/azure-vote-front:v1   "/entrypoint.sh /sta…"   3 minutes ago       Up 3 minutes        443/tcp, 0.0.0.0:8080->80/tcp   azure-vote-front
21574cb38c1f        mcr.microsoft.com/oss/bitnami/redis:6.0.8         "/opt/bitnami/script…"   3 minutes ago       Up 3 minutes        0.0.0.0:6379->6379/tcp          azure-vote-back
```

## <a name="test-application-locally"></a>Testowanie aplikacji w środowisku lokalnym

Aby wyświetlić działającą aplikację, wprowadź adres `http://localhost:8080` w lokalnej przeglądarce internetowej. Zostanie załadowana przykładowa aplikacja, jak pokazano w poniższym przykładzie:

:::image type="content" source="./media/container-service-kubernetes-tutorials/azure-vote-local.png" alt-text="Zrzut ekranu przedstawiający obraz kontenera aplikacja do głosowania platformy Azure uruchamiana lokalnie otwarta w lokalnej przeglądarce internetowej" lightbox="./media/container-service-kubernetes-tutorials/azure-vote-local.png":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Teraz, po zweryfikowaniu funkcjonalności aplikacji, uruchomione kontenery można zatrzymać i usunąć. ***Nie usuwaj obrazów kontenerów** _ — w następnym samouczku obraz przedstawiający zagłosowanie z _azureem zostanie przekazany do wystąpienia Azure Container Registry.

Zatrzymaj i usuń wystąpienia kontenerów i zasoby za pomocą polecenia [docker-compose down][docker-compose-down]:

```console
docker-compose down
```

Po usunięciu aplikacji lokalnej masz obraz *platformy* Docker zawierający aplikację do głosowania platformy Azure, na której będzie można korzystać w następnym samouczku.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przetestowano aplikację i obrazy kontenera utworzone dla aplikacji. W tym samouczku omówiono:

> [!div class="checklist"]
> * Klonowanie źródła przykładowej aplikacji z usługi GitHub
> * Tworzenie obrazu kontenera ze źródła przykładowej aplikacji
> * Testowanie aplikacji z wieloma kontenerami w lokalnym środowisku platformy Docker

Przejdź do kolejnego samouczka, aby dowiedzieć się, jak przechowywać obrazy kontenerów w usłudze Azure Container Registry.

> [!div class="nextstepaction"]
> [Wypychanie obrazów do usługi Azure Container Registry][aks-tutorial-prepare-acr]

<!-- LINKS - external -->
[docker-compose]: https://docs.docker.com/compose/
[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-ps]: https://docs.docker.com/engine/reference/commandline/ps/
[docker-compose-down]: https://docs.docker.com/compose/reference/down
[git]: https://git-scm.com/downloads
[sample-application]: https://github.com/Azure-Samples/azure-voting-app-redis

<!-- LINKS - internal -->
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
