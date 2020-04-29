---
title: (PRZESTARZAŁE) Samouczek usługi Azure Container Service — przygotowywanie aplikacji
description: Samouczek usługi Azure Container Service — przygotowywanie aplikacji
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: b67e3008bc2ad681e222af6b9edc4a08875732e4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76275430"
---
# <a name="deprecated-create-container-images-to-be-used-with-azure-container-service"></a>(PRZESTARZAŁE) Tworzenie obrazów kontenera do użycia z usługą Azure Container Service

> [!TIP]
> Aby uzyskać zaktualizowaną wersję tego samouczka korzystającego z usługi Azure Kubernetes, zobacz [Samouczek: przygotowywanie aplikacji dla usługi Azure Kubernetes Service (AKS)](../../aks/tutorial-kubernetes-prepare-app.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Ten samouczek, część 1 z 7, obejmuje przygotowanie aplikacji z wieloma kontenerami do użycia w usłudze Kubernetes. Wykonano następujące czynności:  

> [!div class="checklist"]
> * Klonowanie źródła aplikacji z usługi GitHub  
> * Tworzenie obrazu kontenera ze źródła aplikacji
> * Testowanie aplikacji w lokalnym środowisku usługi Docker

Po zakończeniu następująca aplikacja będzie dostępna w lokalnym środowisku programistycznym.

![Obraz przedstawiający klaster Kubernetes na platformie Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

W kolejnych samouczkach obraz kontenera zostanie przekazany do usługi Azure Container Registry, a następnie uruchomiony w klastrze Kubernetes hostowanym na platformie Azure.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Ten samouczek zakłada, że masz podstawową wiedzę na temat bazowych koncepcji usługi Docker, takich jak kontenery, obrazy kontenerów i podstawowe polecenia usługi Docker. W razie potrzeby zapoznaj się z tematem [Get started with Docker (Rozpoczynanie pracy z platformą Docker)]( https://docs.docker.com/get-started/), aby uzyskać podstawowe informacje na temat kontenerów. 

Do ukończenia tego samouczka konieczne będzie środowisko programowania Docker. Środowisko Docker zawiera pakiety, które umożliwiają łatwe konfigurowanie platformy Docker w systemie [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) lub [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

Usługa Azure Cloud Shell nie zawiera składników platformy Docker wymaganych do ukończenia każdego kroku w tym samouczku. Dlatego zalecamy używanie pełnego środowiska programistycznego usługi Docker.

## <a name="get-application-code"></a>Pobieranie kodu aplikacji

W tym samouczku jest używana przykładowa prosta aplikacja do głosowania. Ta aplikacja składa się ze składnika internetowego frontonu oraz działającego na zapleczu wystąpienia usługi Redis. Składnik internetowy znajduje się w pakiecie niestandardowego obrazu kontenera. Wystąpienie usługi Redis używa niezmodyfikowanego obrazu z usługi Docker Hub.  

Użyj narzędzia git, aby pobrać kopię tej aplikacji do swojego środowiska projektowego.

```bash
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Zmień katalogi, aby pracować w sklonowanym katalogu.

```
cd azure-voting-app-redis
```

W tym katalogu znajduje się kod źródłowy aplikacji, wstępnie utworzony plik redagowania usługi Docker i plik manifestu usługi Kubernetes. Te pliki są używane w całym zestawie samouczków. 

## <a name="create-container-images"></a>Tworzenie obrazów kontenerów

Program [Docker Compose](https://docs.docker.com/compose/) umożliwia automatyzowanie tworzenia obrazów poza kontenerem i wdrażanie aplikacji wielokontenerowych.

Uruchom plik `docker-compose.yml`, aby utworzyć obraz kontenera, pobrać obraz usługi Redis i uruchomić aplikację.

```bash
docker-compose up -d
```

Po zakończeniu użyj polecenia [docker images](https://docs.docker.com/engine/reference/commandline/images/) w celu wyświetlenia utworzonych obrazów.

```bash
docker images
```

Zwróć uwagę, że zostały pobrane lub utworzone trzy obrazy. Obraz `azure-vote-front` zawiera aplikację i używa obrazu `nginx-flask` jako podstawy. Obraz `redis` jest używany do uruchomienia wystąpienia usługi Redis.

```bash
REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Uruchom polecenie [Docker PS](https://docs.docker.com/engine/reference/commandline/ps/) , aby wyświetlić uruchomione kontenery.

```bash
docker ps
```

Dane wyjściowe:

```bash
CONTAINER ID        IMAGE             COMMAND                  CREATED             STATUS              PORTS                           NAMES
82411933e8f9        azure-vote-front  "/usr/bin/supervisord"   57 seconds ago      Up 30 seconds       443/tcp, 0.0.0.0:8080->80/tcp   azure-vote-front
b68fed4b66b6        redis             "docker-entrypoint..."   57 seconds ago      Up 30 seconds       0.0.0.0:6379->6379/tcp          azure-vote-back
```

## <a name="test-application-locally"></a>Testowanie aplikacji w środowisku lokalnym

Przejdź na adres `http://localhost:8080`, aby zobaczyć działającą aplikację.

![Obraz przedstawiający klaster Kubernetes na platformie Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Teraz, po zweryfikowaniu funkcjonalności aplikacji, uruchomione kontenery można zatrzymać i usunąć. Nie należy usuwać obrazów kontenera. Obraz `azure-vote-front` zostanie przekazany do wystąpienia usługi Azure Container Registry w następnym samouczku.

Uruchom następujące polecenie, aby zatrzymać uruchomione kontenery.

```bash
docker-compose stop
```

Usuń zatrzymane kontenery i zasoby przy użyciu następującego polecenia.

```bash
docker-compose down
```

Po zakończeniu będzie dostępny obraz kontenera zawierający aplikację Azure Vote.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przetestowano aplikację i obrazy kontenera utworzone dla aplikacji. Wykonano następujące czynności:

> [!div class="checklist"]
> * Klonowanie źródła aplikacji z usługi GitHub  
> * Tworzenie obrazu kontenera ze źródła aplikacji
> * Testowanie aplikacji w lokalnym środowisku usługi Docker

Przejdź do kolejnego samouczka, aby dowiedzieć się więcej o przechowywaniu obrazów kontenera w usłudze Azure Container Registry.

> [!div class="nextstepaction"]
> [Wypychanie obrazów do usługi Azure Container Registry](./container-service-tutorial-kubernetes-prepare-acr.md)
