---
title: Samouczek dotyczący usługi Kubernetes na platformie Azure — przygotowywanie aplikacji
description: Z tego samouczka dotyczącego usługi Azure Kubernetes Service (AKS) dowiesz się, jak przygotować i skompilować aplikację z wieloma kontenerami przy użyciu narzędzia Docker Compose, którą można następnie wdrożyć w usłudze AKS.
services: container-service
ms.topic: tutorial
ms.date: 09/30/2020
ms.custom: mvc
ms.openlocfilehash: 15bf29c676c4ca41fc2d005f3500a89ed6b9c380
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91576340"
---
# <a name="tutorial-prepare-an-application-for-azure-kubernetes-service-aks"></a>Samouczek: przygotowywanie aplikacji na potrzeby usługi Azure Kubernetes Service (AKS)

Ten samouczek, część 1 z 7, obejmuje przygotowanie aplikacji z wieloma kontenerami do użycia w usłudze Kubernetes. Istniejące narzędzia programistyczne, takie jak narzędzia Docker Compose, są używane do lokalnego kompilowania i testowania aplikacji. Omawiane kwestie:

> [!div class="checklist"]
> * Klonowanie źródła przykładowej aplikacji z usługi GitHub
> * Tworzenie obrazu kontenera ze źródła przykładowej aplikacji
> * Testowanie aplikacji z wieloma kontenerami w lokalnym środowisku platformy Docker

Po zakończeniu następująca aplikacja będzie uruchomiona w lokalnym środowisku programistycznym:

![Obraz przedstawiający klaster Kubernetes na platformie Azure](./media/container-service-tutorial-kubernetes-prepare-app/azure-vote.png)

W dodatkowych samouczkach obraz kontenera zostanie przekazany do usługi Azure Container Registry, a następnie wdrożony w klastrze usługi AKS.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Ten samouczek zakłada, że masz podstawową wiedzę na temat najważniejszych pojęć dotyczących platformy Docker, takich jak kontenery, obrazy kontenerów i polecenia `docker`. Aby uzyskać podstawowe informacje na temat kontenerów, zapoznaj się z tematem [Get started with Docker (Rozpoczynanie pracy z platformą Docker)][docker-get-started].

Do ukończenia tego samouczka konieczne będzie lokalne środowisko programistyczne platformy Docker z działającymi kontenerami systemu Linux. Środowisko Docker zawiera pakiety, które umożliwiają konfigurowanie platformy Docker w systemie [Mac][docker-for-mac], [Windows][docker-for-windows] lub [Linux][docker-for-linux].

Usługa Azure Cloud Shell nie zawiera składników platformy Docker wymaganych do ukończenia każdego kroku w tych samouczkach. Dlatego zalecamy używanie pełnego środowiska programistycznego usługi Docker.

## <a name="get-application-code"></a>Pobieranie kodu aplikacji

W tym samouczku jest używana przykładowa prosta aplikacja do głosowania. Ta aplikacja składa się ze składnika internetowego frontonu oraz działającego na zapleczu wystąpienia usługi Redis. Składnik internetowy znajduje się w pakiecie niestandardowego obrazu kontenera. Wystąpienie usługi Redis używa niezmodyfikowanego obrazu z usługi Docker Hub.

Użyj narzędzia [git][], aby sklonować przykładową aplikację do swojego środowiska programistycznego:

```console
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Przejdź do sklonowanego katalogu.

```console
cd azure-voting-app-redis
```

W tym katalogu znajduje się kod źródłowy aplikacji, wstępnie utworzony plik redagowania usługi Docker i plik manifestu usługi Kubernetes. Te pliki są używane w całym zestawie samouczków.

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

![Obraz przedstawiający klaster Kubernetes na platformie Azure](./media/container-service-tutorial-kubernetes-prepare-app/azure-vote.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Teraz, po zweryfikowaniu funkcjonalności aplikacji, uruchomione kontenery można zatrzymać i usunąć. Nie należy usuwać obrazów kontenerów — w następnym samouczku obraz *azure-vote-front* zostanie przekazany do wystąpienia usługi Azure Container Registry.

Zatrzymaj i usuń wystąpienia kontenerów i zasoby za pomocą polecenia [docker-compose down][docker-compose-down]:

```console
docker-compose down
```

Po usunięciu aplikacji lokalnej masz obraz *platformy*Docker zawierający aplikację do głosowania platformy Azure, na której będzie można korzystać w następnym samouczku.

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
[narzędzia]: https://git-scm.com/downloads

<!-- LINKS - internal -->
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
