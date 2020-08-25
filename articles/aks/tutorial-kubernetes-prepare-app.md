---
title: Samouczek dotyczący usługi Kubernetes na platformie Azure — przygotowywanie aplikacji
description: Z tego samouczka dotyczącego usługi Azure Kubernetes Service (AKS) dowiesz się, jak przygotować i skompilować aplikację z wieloma kontenerami przy użyciu narzędzia Docker Compose, którą można następnie wdrożyć w usłudze AKS.
services: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.custom: mvc
ms.openlocfilehash: feab8495536b3306fd96793323d51644570b401b
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2020
ms.locfileid: "77593164"
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

Po zakończeniu użyj polecenia [docker images][docker-images] w celu wyświetlenia utworzonych obrazów. Zostały pobrane lub utworzone trzy obrazy. Obraz *Azure-vote-front* zawiera aplikację frontonu i używa obrazu `nginx-flask` jako podstawy. Obraz `redis` jest używany do uruchomienia wystąpienia usługi Redis.

```
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Uruchom polecenie [docker ps][docker-ps], aby wyświetlić uruchomione kontenery:

```
$ docker ps

CONTAINER ID        IMAGE             COMMAND                  CREATED             STATUS              PORTS                           NAMES
82411933e8f9        azure-vote-front  "/usr/bin/supervisord"   57 seconds ago      Up 30 seconds       443/tcp, 0.0.0.0:8080->80/tcp   azure-vote-front
b68fed4b66b6        redis             "docker-entrypoint..."   57 seconds ago      Up 30 seconds       0.0.0.0:6379->6379/tcp          azure-vote-back
```

## <a name="test-application-locally"></a>Testowanie aplikacji w środowisku lokalnym

Aby wyświetlić działającą aplikację, wprowadź adres `http://localhost:8080` w lokalnej przeglądarce internetowej. Zostanie załadowana przykładowa aplikacja, jak pokazano w poniższym przykładzie:

![Obraz przedstawiający klaster Kubernetes na platformie Azure](./media/container-service-tutorial-kubernetes-prepare-app/azure-vote.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

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
[git]: https://git-scm.com/downloads

<!-- LINKS - internal -->
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
